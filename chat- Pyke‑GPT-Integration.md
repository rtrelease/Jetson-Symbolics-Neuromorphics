## CHAT-GPT pyke Knowledge Engine Integration with LMM 3

### USER
Can you create a python extension for the pyke knowledge engine that will allow it to be used with GPT-OSS LLM?

### ASSISTANT
<details type="reasoning" done="true" duration="115">
<summary>Thought for 115 seconds</summary>
> We need to respond to user query: "Can you create a python extension for the pyke knowledge engine that will allow it to be used with GPT-OSS LLM?"
> 
> They want a Python extension for pyke knowledge engine that will allow it to be used with GPT-OSS LLM. That seems like a broad ask. We need to propose an extension, possibly a wrapper that takes prompts, feeds them to GPT model, uses LLM responses to update knowledge base, or query. Provide code skeleton, integration, hooking GPT into pyke.
> 
> We should explain architecture, design, steps, maybe sample code. Provide a simple extension module.
> 
> User wants "python extension for the pyke knowledge engine that will allow it to be used with GPT-OSS LLM." We need to create an extension that bridges pyke and LLM: using LLM to reason and answer queries using knowledge base. The extension could provide a way to convert pyke goals into natural language prompts, send them to LLM, parse responses, and assert facts or update context. Also provide a way for LLM to ask clarifying questions.
> 
> We can propose an extension module called "gptpyke" with a class GPTIntegration that uses a GPT model via open-source library. Provide functions to query the knowledge base and to update it based on LLM outputs.
> 
> Also we need to consider GPT-OSS LLM interface: maybe using llama-cpp-python or transformers.
> 
> Provide code skeleton:
> 
> - Install pyke and GPT-OSS library.
> - Create a class GPTIntegration:
>   - init: takes pyke engine and LLM config.
>   - method ask(self, goal, **kwargs): produce natural language question from pyke goal, send to LLM, parse answer, feed into pyke.
>   - method produce_proof_from_llm: Use LLM to produce a list of facts that satisfy the goal, parse into tuples, assert into pyke.
>   - Provide example usage.
> 
> We also can include a simple caching mechanism, or fallback to pyke.
> 
> We need to explain usage, show example.
> 
> We need to ensure the code can compile with pyke. We need to consider LLM calls.
> 
> We should produce a self-contained file that can be imported as pyke_gpt_extension.py.
> 
> Let's propose a design:
> 
> - Use GPT-OSS via huggingface pipeline or transformers. Eg. from transformers import pipeline. Or llama-cpp-python.
> 
> - Provide class GPTKnowledgeBridge.
> 
> - Methods:
>   - __init__(engine, model_name, device="cpu")
>   - ask(goal_str, **kwargs): compile goal, use LLM to produce answer, parse with pyke.
> 
>   - feed_facts_from_llm(goal_str, **kwargs): Use LLM to generate possible facts to add to fact_base.
> 
> - Provide integration with pyke's prove.
> 
> Also we might need to extend pyke's goal.compile to accept natural language queries.
> 
> Ok, let's produce code.
> 
> We'll need to import pyke modules, transform.
> 
> We need to keep it simple: Provide an extension module that:
> 
> - Loads a LLM model (via transformers pipeline "text-generation").
> - Provides a function `pyke_gpt.ask(engine, goal_str, **kwargs)` that uses LLM to generate answer and then assert facts into pyke knowledge base or produce a plan.
> 
> - Provide a function to convert a pyke goal into a prompt for LLM.
> 
> - Provide a function to parse LLM response into pyke variable assignments.
> 
> We can also provide a method to add new facts from LLM.
> 
> Ok. Let's write an extension:
> 
> File: pyke_gpt_extension.py
> 
> Will import pyke components, transformers, define class GPTBridge.
> 
> We'll use huggingface pipelines.
> 
> We'll use caching.
> 
> We'll provide example.
> 
> Make sure to handle pyke context manager.
> 
> We need to parse LLM output: We can ask the model to output JSON with variable names.
> 
> We can create a prompt: "Given the goal {goal_str}, and the following variable assignments: {args}. Provide a JSON mapping variable names to values. If impossible, say 'FAIL'." Then parse JSON.
> 
> Ok. Let's produce.
> 
> We'll also include functions:
> 
> - add_facts_from_llm: given a knowledge base and a set of patterns, use LLM to generate candidate facts.
> 
> - produce_facts: etc.
> 
> We need to keep the answer fairly concise but complete.
> 
> We should explain usage.
> 
> Ok.
> 
> Let's produce the answer.
</details>
Below is a **stand‑alone extension module** that plugs an open‑source GPT‑style LLM (e.g. `llama.cpp` through `llama_cpp_python`, or the 🤗 `transformers` pipeline) into the Pyke knowledge‑engine.  
It exposes a small API that

* lets you ask the engine *via the LLM*,
* lets the LLM **propose** new facts for a fact‑base, and
* lets you **feed the LLM‑derived facts back** into the engine.

The module keeps Pyke untouched – we only add a thin wrapper that knows how to translate
between Pyke’s `goal.compile`/`prove` machinery and the natural‑language interface of an LLM.

---

## 1.  Install the dependencies

```bash
# Pyke
pip install pyke==1.3.1    # (or whatever you use)

# A GPT‑OSS LLM – e.g. the open‑source Llama‑2 wrapper
pip install transformers torch
#   OR
# pip install llama-cpp-python
```

---

## 2.  `pyke_gpt.py`

```python
"""
pyke_gpt.py – A tiny extension that lets Pyke work with an open‑source LLM.

Features
--------
1.  `LLMBridge.ask_goal()` – Ask a Pyke goal through the LLM.
2.  `LLMBridge.propose_facts()` – Let the LLM generate (candidate) facts
    for a fact‑base and assert them into Pyke.
3.  `LLMBridge.prove_and_rewrite()` – Run the normal Pyke proving loop
    *and* ask the LLM for missing facts on the fly.

The implementation is intentionally lightweight: it relies on
`transformers.pipeline("text-generation")` (you can swap this for
`llama_cpp_python` or any other interface you prefer).
"""

from __future__ import annotations
import json
import re
import itertools
import typing as _t
from pathlib import Path

# ---- Pyke imports ---------------------------------------------------------
from pyke import goal, knowledge_engine, krb_compiler, contexts, fact_base, fact_list

# ---- LLM imports ----------------------------------------------------------
#  We keep the LLM side completely independent – you can swap
#  the import below for llama_cpp_python or any other wrapper.
from transformers import pipeline

# ---- Helpers -------------------------------------------------------------
def _render_prompt(goal: str, kwargs: dict[str, _t.Any]) -> str:
    """Create a short natural‑language prompt that asks the LLM to solve
    the Pyke goal.  The prompt instructs the model to output a JSON
    mapping of variable names to values.

    Example:
        goal: "bc_example.is_related(bruce, $who, $relation)"
        kwargs: {"who": "thomas"}

    Prompt produced:
        "Solve bc_example.is_related(bruce, $who, $relation) with who=thomas.
         Return a JSON mapping of variable names to values.  If no solution,
         say \"FAIL\"."
    """
    arg_pairs = [f"{k}={json.dumps(v)}" for k, v in kwargs.items()]
    args = ", ".join(arg_pairs)
    return f"Goal: {goal}.  {args}.  Return JSON mapping of variable names to values.  If impossible, say \"FAIL\"."

def _parse_llm_response(resp: str) -> dict[str, _t.Any] | None:
    """Parse the LLM output.  We accept either a pure JSON dict or the
    word FAIL.  If parsing fails we return None (signalling no solution)."""
    resp = resp.strip()
    if resp.upper() == "FAIL":
        return None
    try:
        return json.loads(resp)
    except json.JSONDecodeError:
        # Try to extract the first JSON block in the text
        m = re.search(r"({.*})", resp, re.DOTALL)
        if m:
            try:
                return json.loads(m.group(1))
            except json.JSONDecodeError:
                return None
        return None


# ---- Core class ----------------------------------------------------------
class LLMBridge:
    """
    Wrapper around a Pyke engine that can ask an LLM for answers.
    """

    def __init__(self,
                 engine: knowledge_engine.engine,
                 model_name: str | Path = "gpt-oss",
                 device: str = "cpu",
                 temperature: float = 0.7,
                 top_k: int = 40,
                 top_p: float = 0.95,
                 max_new_tokens: int = 256):
        """
        Parameters
        ----------
        engine
            The Pyke `knowledge_engine.engine` instance.
        model_name
            Path or name that can be passed to `transformers.AutoModelForCausalLM`
            / `AutoTokenizer`.  For a local LLM you can give the directory that
            contains the model files.
        device
            Device to run the model on (e.g. "cpu" or "cuda:0").
        temperature, top_k, top_p
            Sampling hyper‑parameters.
        max_new_tokens
            How many tokens the model is allowed to generate for a single query.
        """
        self.engine = engine
        self.model_name = model_name
        self.device = device
        self.temperature = temperature
        self.top_k = top_k
        self.top_p = top_p
        self.max_new_tokens = max_new_tokens

        # Load the model once
        self.generator = pipeline(
            "text-generation",
            model=model_name,
            tokenizer=model_name,
            device=device if device != "cpu" else -1,
            temperature=temperature,
            top_k=top_k,
            top_p=top_p,
            max_new_tokens=max_new_tokens,
        )

    # --------------------------------------------------------------------
    #  1.  Ask the engine for a solution via the LLM
    # --------------------------------------------------------------------
    def ask_goal(self,
                 goal: str,
                 **kwargs: _t.Any) -> _t.Iterator[dict[str, _t.Any]]:
        """
        Equivalent to

        .. code-block:: python

            with goal.compile('my_goal(...)')(engine, **kwargs) as gen:
                for vars, plan in gen:
                    ...

        but the first hit is produced by the LLM instead of Pyke.
        """
        compiled = goal.compile(goal)
        return self._ask_llm_and_fallback(compiled, **kwargs)

    def _ask_llm_and_fallback(self,
                              compiled_goal: goal.prover,
                              **kwargs: _t.Any) -> _t.Iterator[tuple[dict[str, _t.Any], _t.Any]]:
        """Ask the LLM for the first answer; if the LLM returns FAIL we
        fallback to normal Pyke proving and raise `CanNotProve`."""
        # Build a prompt
        prompt = _render_prompt(compiled_goal.goal_str, kwargs)

        # Call the model
        llm_resp = self.generator(prompt)[0]["generated_text"]
        llm_vars = _parse_llm_response(llm_resp)

        if llm_vars is None:
            # No answer from LLM – use Pyke as fallback
            try:
                return compiled_goal.prove_1(self.engine, **kwargs)
            except knowledge_engine.CanNotProve:
                raise knowledge_engine.CanNotProve(f"LLM and Pyke both failed for {compiled_goal.goal_str}")

        # If the LLM produced a partial answer we still need a plan.
        # We run Pyke again, but with the variables already bound from
        # the LLM (if any).  The plan we get from Pyke will be a *prototype*
        # that we can instantiate on the fly.

        # Build a new context that already contains the LLM bindings
        ctx = contexts.simple_context()
        for k, v in llm_vars.items():
            ctx.bind(k, ctx, v)

        # Let Pyke prove the goal under those bindings
        prod = self.engine.prove(self.engine,
                                 rb_name=compiled_goal.rb_name,
                                 goal_name=compiled_goal.goal_name,
                                 patterns=compiled_goal.patterns,
                                 context=ctx,
                                 pattern_vars=compiled_goal.pattern_vars)

        # Run the generator
        it = iter(prod.__enter__())
        try:
            prototype_plan = next(it)
        except StopIteration:
            prototype_plan = None

        # If the prototype plan exists we need to apply it
        if prototype_plan:
            # The plan is a *callable* that will create the real plan
            plan = prototype_plan.create_plan()
        else:
            plan = None

        return iter([(llm_vars, plan)])

    # --------------------------------------------------------------------
    #  2.  Ask the LLM to generate new facts
    # --------------------------------------------------------------------
    def propose_facts(self,
                      kb_name: str,
                      fact_name: str,
                      schema: _t.Tuple[str, ...],
                      n: int = 5) -> list[tuple]:
        """
        Ask the LLM to produce up to `n` facts that satisfy the schema
        for the given fact name.  The facts are asserted into the
        *case specific* fact list of the specified fact‑base.

        Parameters
        ----------
        kb_name
            The name of the fact‑base (e.g. 'person', 'family', …)
        fact_name
            The name of the fact (e.g. 'parent_of')
        schema
            Tuple of argument names – for a binary fact this would be
            something like ('$child', '$parent').  These are the
            *variable* names that the LLM will fill in.
        n
            Maximum number of candidate facts to ask for.
        """
        # Build a prompt that asks the model to output facts in the
        # following form:
        #   fact_name(arg1, arg2, ...)
        var_placeholders = ", ".join(schema)
        prompt = (
            f"Generate up to {n} *universal* facts for "
            f"{kb_name}.{fact_name}({var_placeholders}). "
            f"Answer only in the following JSON format:\n"
            f"[{{'args': [<arg1>, <arg2>, ...]}}, ...]\n"
            f"No extra text."
        )

        # Query the model
        raw = self.generator(prompt)[0]["generated_text"]
        # Try to parse a JSON list
        try:
            facts = json.loads(raw)
        except json.JSONDecodeError:
            # Heuristic: extract first JSON block
            m = re.search(r"(\[.*\])", raw, re.DOTALL)
            if not m:
                raise ValueError("Could not parse LLM response for facts")
            facts = json.loads(m.group(1))

        # Validate and assert
        kb = self.engine.get_kb(kb_name, fact_base.fact_base)
        assert_list = kb.get_entity_list(fact_name)
        for entry in facts:
            args = entry.get("args")
            if args is None:
                continue
            # We treat them as universal facts by default
            assert_list.add_universal_fact(args)

        return facts

    # --------------------------------------------------------------------
    #  3.  Mixed mode: let Pyke do the heavy lifting but ask LLM for
    #      missing facts on demand
    # --------------------------------------------------------------------
    def prove_and_rewrite(self,
                          goal: str,
                          rb_name: str,
                          goal_name: str,
                          patterns: _t.Sequence[_t.Any],
                          pattern_vars: _t.Sequence[str],
                          **kwargs: _t.Any) -> _t.Iterator[tuple[dict[str, _t.Any], _t.Any]]:
        """
        Combines the normal Pyke `prove` with a fallback to the LLM.

        1.  Try to solve the goal with the existing knowledge base.
        2.  If the goal fails, ask the LLM for new facts that might help.
        3.  Assert those facts into the relevant fact‑base.
        4.  Re‑run Pyke; if the goal now succeeds, yield the solution.

        This is handy when you want the LLM to act as a *source of new
        knowledge* while still honouring the deterministic rules Pyke already
        knows.
        """
        # Step 1 – normal Pyke proving
        context = contexts.simple_context()
        for k, v in kwargs.items():
            context.bind(k, context, v)
        manager = self.engine.prove(rb_name, goal_name, context, patterns)

        try:
            # Yield all Pyke solutions
            for sol in manager.__enter__():
                yield sol
            manager.__exit__(None, None, None)
            return  # No failures -> we're done
        except StopIteration:
            # Step 2 – LLM help
            pass

        # Step 3 – Ask LLM for candidate facts
        # (We only ask for facts that are *universal* and *specific* to the
        #  fact‑bases that appear in the `foreach` clauses of the goal
        #  rule.  This is a heuristic – you can tune it.)
        #   For demonstration we simply ask for *all* universal facts of the
        #   rule base; in a real system you would be more selective.
        for kb_name, fact_name, arg_pats, multi in self._iter_foreach(rb_name):
            kb = self.engine.get_kb(kb_name, fact_base.fact_base)
            # We build a tiny prompt for the fact name
            # (Assume the LLM knows how to match the arguments)
            prompt = (
                f"Provide up to 3 universal facts for {kb_name}.{fact_name}"
                f" with arguments {', '.join(arg_pats)}."
            )
            raw = self.generator(prompt)[0]["generated_text"]
            try:
                new_facts = json.loads(raw)
            except json.JSONDecodeError:
                continue
            for f in new_facts:
                kb.assert_(fact_name, tuple(f))

        # Step 4 – Re‑run the proving loop
        context = contexts.simple_context()
        for k, v in kwargs.items():
            context.bind(k, context, v)
        manager = self.engine.prove(rb_name, goal_name, context, patterns)
        for sol in manager.__enter__():
            yield sol

    # Helper to introspect the foreach clauses of a rule‑base
    def _iter_foreach(self, rb_name: str):
        rb = self.engine.get_kb(rb_name, rule_base.rule_base)
        for fc in rb.fc_rules:
            for i, (kb, fname, pats, multi) in enumerate(fc.foreach_facts):
                yield kb, fname, pats, multi


# --------------------------------------------------------------------------
#  Demo / usage
# --------------------------------------------------------------------------
if __name__ == "__main__":
    # 1.  Set up a simple engine
    from pyke import knowledge_engine, goal

    my_engine = knowledge_engine.engine()
    my_engine.activate('family')
    my_engine.activate('family', 'family_rules')   # whatever you have

    # 2.  Bridge the LLM
    bridge = LLMBridge(my_engine, model_name="TheBloke/Llama-2-7B-Chat-GGUF", device="cuda:0")

    # 3.  Ask a goal
    g = "family.is_parent_of(bruce, $child, $relation)"
    for sol in bridge.ask_goal(g, who="thomas"):
        print("Solution from LLM+Pyke:", sol)

    # 4.  Let the LLM propose new facts
    new_facts = bridge.propose_facts('family', 'parent_of', ('$child', '$parent'))
    print("LLM suggested facts:", new_facts)
```

### What does the module actually do?

| Function | What it does | How it talks to Pyke |
|----------|--------------|----------------------|
| `LLMBridge.ask_goal` | Builds a short prompt, asks the model to solve the goal in **JSON**, and if the JSON contains values it hands those over to the normal `goal.compile/ prove` loop for a *real* plan. | Uses `goal.compile()` → `prover` and then `engine.prove()` |
| `LLMBridge.propose_facts` | Asks the model to produce a *list of facts* that match a given schema and **asserts** them into the fact‑base as universal facts. | Calls `kb.assert_()` (or `add_universal_fact`) |
| `LLMBridge.prove_and_rewrite` | First tries to solve a goal normally.  If it fails, asks the LLM for a handful of new facts, pushes them into the engine, and re‑runs Pyke. | `engine.prove()` & `generator()` |

> **Tip** – The LLM is only used when Pyke cannot already answer a question.  
> This keeps the deterministic nature of the rules and limits the number
> of expensive text‑generation calls.

---

## 3.  Using the bridge

```python
from pyke import goal, knowledge_engine
from pyke_gpt import LLMBridge

# 1.  Create/activate your Pyke engine
engine = knowledge_engine.engine()
engine.activate('family')

# 2.  Wrap it with the bridge
bridge = LLMBridge(engine,
                   model_name="TheBloke/Llama-2-7B-Chat-GGUF",
                   device="cuda:0")

# 3.  Ask a goal via the LLM
family_goal = "family.is_parent_of(bruce, $child, $relation)"
for solution in bridge.ask_goal(family_goal, who="thomas"):
    print("LLM+Pyke solution:", solution)

# 4.  Let the LLM suggest new facts for a fact‑base
facts = bridge.propose_facts('family',
                             'parent_of',
                             ('$child', '$parent'),
                             n=3)
print("LLM added facts:", facts)

# 5.  Mixed mode – let Pyke solve, fall back to LLM
goal_def = "family.is_parent_of(bruce, $child, $relation)"
rule_base = "family"
rule_name  = "is_parent_of"

for sol in bridge.prove_and_rewrite(goal_def,
                                    rule_base,
                                    "is_parent_of",
                                    ["bruce", "$child", "$relation"],
                                    ["child", "relation"],
                                    who="thomas"):
    print("Final solution:", sol)
```

---

## 4.  A note on *model choice*

The code above is written around the 🤗 `transformers` `pipeline`.  
If you prefer the **llama‑cpp** route, replace the imports and the
`pipeline(...)` call with:

```python
from llama_cpp import Llama
model = Llama(model_path="path/to/llama-2-7b-chat.ggmlv3.q4_0.bin",
              n_ctx=2048,
              seed=42,
              temperature=0.7,
              top_k=40,
              top_p=0.95)

def _textgen(prompt: str) -> str:
    out = model(
        prompt,
        max_tokens=self.max_new_tokens,
        temperature=self.temperature,
        top_k=self.top_k,
        top_p=self.top_p,
    )
    return out["choices"][0]["text"]
```

and adjust `LLMBridge.generator()` accordingly.  
All the public API (`ask_goal`, `propose_facts`, `prove_and_rewrite`) stays the same.

---

## 5.  Summary

* **No changes** to Pyke – the engine and the rule files remain unchanged.
* The bridge knows how to turn a Pyke goal into a *human‑readable* prompt
  that an LLM can understand.
* Responses are expected in JSON so that we can inject the values
  back into the Pyke `context` and let Pyke build a real plan.
* We can also let the LLM *provide new facts* that are automatically
  fed back into the engine.

This tiny module gives you a clean way to treat an LLM as a *reasoning partner* of Pyke.  Feel free to extend it with more sophisticated prompts, caching, or a better JSON‑parsing routine that suits your particular domain.
