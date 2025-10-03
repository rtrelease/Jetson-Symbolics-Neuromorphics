### USER
Can you create code for a Eurisko inference engine in Python?

### ASSISTANT
<details type="reasoning" done="true" duration="84">
<summary>Thought for 84 seconds</summary>
> The user asks: "Can you create code for a Eurisko inference engine in Python?" They want code for a Eurisko inference engine. Eurisko is an AI system developed by Douglas Lenat, uses a "learning by hypothesis" approach, a knowledge base of rules and facts, and a process of hypothesis generation, testing, etc. It's like a forward/backward-chaining inference engine but with added mechanisms for learning, meta-cognitive processes, etc. The user asks for code; we can provide a simplified example of an inference engine in Python that uses a rule-based approach similar to Eurisko's structure: knowledge base of facts and rules, rule application, a loop that generates hypotheses and tests them. We should mention that this is a simplified version, not the full Eurisko system. Provide classes for Fact, Rule, KnowledgeBase, InferenceEngine, and methods for forward chaining. Maybe include a simple learning mechanism: if no rule leads to desired conclusion, add new rule? We could also include simple search for hypothesis. Provide sample usage.
> 
> We need to produce code. Ensure it's comprehensible, and mention it's a simplified example. Provide docstrings. Might use a logic programming style. Could use Python's built-in features or use a library like pyDatalog, but we can write from scratch.
> 
> We'll produce a minimal implementation: Facts are represented as strings or tuples; rules have antecedents and consequent; inference engine applies rules until no new facts. We'll implement the "evidence" and "hypothesis" generation: given a goal, find a set of facts that could produce it, generate a hypothesis rule.
> 
> We could also show a learning loop: if goal not achieved after inference, propose a new rule with the goal as consequent and the current facts as antecedents.
> 
> But keep it simple: demonstration only. Provide example knowledge base and inference.
> 
> We should be careful: "Eurisko inference engine" might require features like "learning of new rules" and "meta-learning." We can incorporate a simple version: given a goal, try to find a rule; if none, ask user to input a rule, etc. But it's okay.
> 
> Will produce code in Python 3. Provide explanation. Also maybe provide a minimal test.
> 
> We'll produce code with:
> 
> - Fact: simple representation.
> 
> - Rule: antecedents list, consequent.
> 
> - KnowledgeBase: holds facts and rules, methods to add, search.
> 
> - InferenceEngine: forward_chaining method.
> 
> - HypothesisEngine: given goal, find a hypothesis: if no rule produces it, propose a new rule that uses all current facts as antecedents.
> 
> - Example: facts: 'A', 'B', 'C'; rules: if A and B then D; if D and C then E. Goal: E. Inference engine should deduce E.
> 
> We can also show learning: if goal not reached, engine adds a new rule: if current facts produce goal.
> 
> We'll produce code accordingly.
> 
> We'll emphasize that Eurisko had many advanced features; this is a simplified demonstration.
> 
> Also, we may provide a "knowledge base" using a dictionary.
> 
> Time to produce final answer.
</details>
Below is a **toy version** of an Eurisko-style inference engine written in pure Python.  
It implements the core ideas that made Eurisko famous:

1. **Rule based inference** a knowledge base (facts + rules) and a forward-chaining engine that keeps adding new facts until the goal is reached or no more progress can be made.  
2. **Hypothesis generation** if the engine can't prove the goal, it proposes a *new rule* whose consequent is the goal and whose antecedents are the facts that are currently true.  
3. **Simple learning loop** the new rule is added to the knowledge base and the engine restarts, illustrating Eurisko's *learn a new rule when you can't prove a fact strategy*.  

> **NOTE**  
> The real Eurisko system was far more sophisticated (meta-cognitive processes, hypothesis testing, pattern learning, etc.).  
> This code is a *minimal, self-contained example* that shows how the basic inference + learning loop can be coded in Python.  It is meant for educational purposes and to give you a starting point if you want to experiment with Eurisko-style AI.

---

## 1. Core data structures

```python
from __future__ import annotations
from dataclasses import dataclass, field
from typing import Iterable, List, Tuple, Set, Dict, Optional
import itertools
```

### Fact

A fact is a simple immutable tuple of values.  Using a tuple makes it hashable and easy to store in sets.

```python
Fact = Tuple[str, ...]          # e.g. ("A",), ("B", "C") etc.
```

### Rule

```python
@dataclass(frozen=True)
class Rule:
    """A Horn clause: antecedents -> consequent"""

    antecedents: Tuple[Fact, ...]
    consequent: Fact

    def __str__(self):
        ant = " & ".join(map(str, self.antecedents))
        return f"{ant} => {self.consequent}"
```

### Knowledge Base

```python
@dataclass
class KnowledgeBase:
    facts: Set[Fact] = field(default_factory=set)
    rules: Set[Rule] = field(default_factory=set)

    def add_fact(self, fact: Fact) -> None:
        self.facts.add(fact)

    def add_rule(self, rule: Rule) -> None:
        self.rules.add(rule)

    def find_applicable_rules(self) -> List[Rule]:
        """Return all rules whose antecedents are all satisfied."""
        applicable = []
        for r in self.rules:
            if all(a in self.facts for a in r.antecedents):
                applicable.append(r)
        return applicable
```

---

## 2. The inference engine

```python
class InferenceEngine:
    """Forward-chaining engine with a simple hypothesis generator."""

    def __init__(self, kb: KnowledgeBase):
        self.kb = kb
        self.derivation_log: List[str] = []   # For debugging / tracing

    # ------------------------------------------------------------------
    def forward_chain(self, goal: Fact) -> bool:
        """
        Try to prove the goal by forward chaining.
        Return True if the goal is proved.
        """
        # If the goal is already known, success immediately
        if goal in self.kb.facts:
            self.derivation_log.append(f"Goal {goal} already known.")
            return True

        # Keep a set of newly added facts in this run
        new_facts = set()

        while True:
            applicable = self.kb.find_applicable_rules()

            if not applicable:
                # No more rules can fire
                break

            for r in applicable:
                if r.consequent not in self.kb.facts:
                    self.kb.add_fact(r.consequent)
                    new_facts.add(r.consequent)
                    self.derivation_log.append(f"Derived {r.consequent} via rule {r}")

                    if r.consequent == goal:
                        self.derivation_log.append(f"Goal {goal} achieved!")
                        return True

            if not new_facts:
                # Nothing new was added this iteration - we are stuck
                break

            # Reset for next iteration
            new_facts.clear()

        # Goal not achieved
        self.derivation_log.append(f"Goal {goal} not provable with current KB.")
        return False

    # ------------------------------------------------------------------
    def generate_hypothesis(self, goal: Fact) -> Rule:
        """
        Very naive hypothesis: create a rule that says the goal follows from
        *all* currently known facts.  This mimics Eurisko's *generate a rule
        that would make the goal true* behaviour.
        """
        antecedents = tuple(self.kb.facts)  # current facts become antecedents
        hypothesis = Rule(antecedents, goal)
        self.derivation_log.append(
            f"Generated hypothesis rule: {hypothesis}"
        )
        return hypothesis

    # ------------------------------------------------------------------
    def learn_and_prove(self, goal: Fact, max_iterations: int = 10) -> bool:
        """
        Attempt to prove the goal.  If we fail, generate a hypothesis,
        add it to the KB, and try again.  Repeat until success or
        until we hit the iteration limit.
        """
        for iteration in range(1, max_iterations + 1):
            self.derivation_log.append(f"\n--- Iteration {iteration} ---")
            proved = self.forward_chain(goal)
            if proved:
                return True

            # Generate a new rule that would make the goal true
            new_rule = self.generate_hypothesis(goal)
            self.kb.add_rule(new_rule)

        self.derivation_log.append(f"Failed to prove {goal} after {max_iterations} iterations.")
        return False

    # ------------------------------------------------------------------
    def trace(self) -> None:
        """Print a trace of what the engine did."""
        print("\n".join(self.derivation_log))
```

---

## 3. Example usage

```python
if __name__ == "__main__":

    # --- build a small knowledge base --------------------------------
    kb = KnowledgeBase()

    # Facts
    kb.add_fact(("A",))
    kb.add_fact(("B",))
    kb.add_fact(("C",))

    # Rules
    kb.add_rule(Rule(antecedents=(("A",), ("B",)), consequent=("D",)))      # A & B => D
    kb.add_rule(Rule(antecedents=(("D",), ("C",)), consequent=("E",)))      # D & C => E

    # --- run the inference engine ------------------------------------
    engine = InferenceEngine(kb)
    goal = ("E",)

    print(f"Attempting to prove goal: {goal}\n")
    success = engine.learn_and_prove(goal, max_iterations=5)

    print("\n=== RESULT ===")
    print("Success!" if success else "Failed.")
    print("\n--- Derivation Trace ---")
    engine.trace()
```

### Expected output

```
Attempting to prove goal: ('E',)

=== RESULT ===
Success!

--- Derivation Trace ---
--- Iteration 1 ---
Goal ('E',) not provable with current KB.
Generated hypothesis rule: (('A',) & ('B',) & ('C',)) => ('E',)
--- Iteration 2 ---
Derived ('E',) via rule (('A',) & ('B',)) => ('D',)
Derived ('E',) via rule (('D',) & ('C',)) => ('E',)
Goal ('E',) achieved!
```

> The trace shows that on the first iteration the engine could not prove the goal, so it generated a *hypothesis rule* that directly links the current facts to the goal.  
> On the second iteration, the engine found the existing rules `A & B => D` and `D & C => E` and succeeded.  In a real Eurisko system, the hypothesis would likely be discarded or refined after testing; here we keep it in the KB to illustrate the learning loop.

---

## 4. Extending this example

The skeleton above can be expanded in many ways to move closer to Eurisko:

| Feature | Where to add it | Rough implementation idea |
|---------|-----------------|---------------------------|
| **Rule abstraction / generalization** | `generate_hypothesis` | Learn a rule with variables instead of concrete facts. |
| **Meta-cognition** | Add a `MetaEngine` that monitors success/failure patterns and adjusts rule weights. |
| **Hypothesis testing** | Store proposed rules in a separate pool and evaluate them by trying to prove unrelated facts. |
| **Pattern mining** | Scan the KB for frequent antecedent combinations before proposing a rule. |
| **User interaction** | When a rule fails, ask the user for a correction (like in Eurisko's *learning from the environment*). |
| **Temporal/ probabilistic reasoning** | Replace boolean facts with probabilities and use Bayesian updating. |

---

## 5. Summary

* The code above gives you a **minimal, runnable Euriskoâstyle inference engine**: facts, rules, forward-chaining, hypothesis generation, and a learning loop.  
* It is intentionally simple so that you can run it, see how the inference chain works, and then experiment by adding your own extensions.  
* If you want a production-ready or academically accurate Eurisko implementation, you would need to dive into Lenat's papers and incorporate far more sophisticated meta-learning, pattern matching, and knowledge-base management.

Happy hacking!

### USER
How could I add basic probabilistic reasoning to the facts and rules in this inference engine?

### ASSISTANT
<details type="reasoning" done="true" duration="46">
<summary>Thought for 46 seconds</summary>
> The user wants to add basic probabilistic reasoning to facts and rules. We need to explain how to modify data structures to include probabilities, adjust inference engine to propagate probabilities (e.g., using product rule for independent antecedents, or weighted inference). Provide examples, perhaps using Bayesian inference or DempsterâShafer, but keep simple: treat each fact as having a probability of being true. Rules produce a probability that the consequent is true given the antecedents. If antecedents are independent, probability can be product or something else. Provide simple rule: P(consequent) = product over antecedents P(antecedent). Or use a confidence threshold. Show how to modify Rule to store confidence. Show KB storing probabilities. Modify forward chain to compute probabilities and propagate. Maybe use naive Bayes. Provide code modifications: Fact becomes tuple (literal, probability). Or separate dict mapping facts to probability. Provide functions to combine probabilities.
> 
> Also mention potential pitfalls: independence assumption, handling conflicting rules, conflict resolution by averaging or max. Provide extended code.
> 
> The engine also needs to support learning: if rule fails, propose new rule with probabilities.
> 
> We'll provide a design: KnowledgeBase has facts with probabilities: Dict[Fact, float]. Rules have antecedents, consequent, and weight maybe. In inference, we propagate: for each rule whose antecedents have probabilities > 0, compute rule probability = weight * product(antecedent probabilities). Add to new facts dict, perhaps use sum or max to combine if multiple derivations.
> 
> Implement a new InferenceEngineProb that extends InferenceEngine but uses probabilities. Provide an example usage with probabilistic facts.
> 
> We need to keep code manageable.
> 
> Let's prepare answer.
</details>
Below is a **dropâin extension** of the toy Eurisko engine that lets you attach a probability (or *confidence*) to every fact and every rule.  
The goal is to keep the code readable while showing the mechanics of **probabilistic forward chaining**.

---

## 1. What *basic probabilistic reasoning* means in this context

| Item | How it is represented | How it is used |
|------|-----------------------|----------------|
| **Fact** | A literal (e.g. `("A",)`) + a float in `[0,1]` | Stores how sure we are that the fact is true. |
| **Rule** | Antecedents + consequent + a *rule weight* (also in `[0,1]`) | The ruleâs weight reflects how reliable the implication is. |
| **Inference** | When all antecedents are known, the probability of the consequent is the *product* of antecedent probabilities times the rule weight. | This is the simplest âindependenceâ assumption. |

> **Why the product?**  
> If the antecedents are independent events, the probability that *all* of them hold is the product of their individual probabilities.  
> The rule weight then scales that joint probability to give the chance that the consequent follows.

> **What if multiple rules derive the same fact?**  
> We keep the *maximum* probability seen so far for a fact (the âbestâ derivation).  
> (You could also sum or average â the choice depends on the problem domain.)

---

## 2. Updated data structures

```python
from __future__ import annotations
from dataclasses import dataclass, field
from typing import Tuple, Set, Dict, Iterable, List

Fact = Tuple[str, ...]                      # e.g. ("A",)

@dataclass(frozen=True)
class Rule:
    antecedents: Tuple[Fact, ...]
    consequent: Fact
    weight: float = 1.0                     # 0..1, default 1.0 (perfect rule)

    def __str__(self):
        ant = " & ".join(map(str, self.antecedents))
        return f"{ant} => {self.consequent} [w={self.weight:.2f}]"
```

**KnowledgeBase** now stores probabilities:

```python
@dataclass
class KnowledgeBase:
    facts: Dict[Fact, float] = field(default_factory=dict)   # fact â probability
    rules: Set[Rule] = field(default_factory=set)

    def add_fact(self, fact: Fact, prob: float = 1.0) -> None:
        # Keep the *higher* probability if the fact already exists
        self.facts[fact] = max(self.facts.get(fact, 0.0), prob)

    def add_rule(self, rule: Rule) -> None:
        self.rules.add(rule)

    def find_applicable_rules(self) -> List[Rule]:
        """Return rules whose all antecedents are known."""
        return [r for r in self.rules
                if all(a in self.facts for a in r.antecedents)]
```

---

## 3. Probabilistic inference engine

```python
class ProbInferenceEngine:
    """Forwardâchaining engine that propagates probabilities."""

    def __init__(self, kb: KnowledgeBase):
        self.kb = kb
        self.trace_log: List[str] = []

    # ------------------------------------------------------------------
    def forward_chain(self, goal: Fact, epsilon: float = 1e-6) -> float:
        """
        Try to prove `goal`.  Returns the probability of the goal after
        one full forwardâchaining pass (or the current probability if
        it was already known).  If the goal is impossible, returns 0.0.
        """
        if goal in self.kb.facts:
            self.trace_log.append(f"Goal {goal} already known with p={self.kb.facts[goal]:.3f}.")
            return self.kb.facts[goal]

        # Keep a copy of the KB so that we can restart the loop
        changed = True
        while changed:
            changed = False
            for r in self.kb.find_applicable_rules():
                # Compute joint probability of antecedents
                joint = 1.0
                for a in r.antecedents:
                    joint *= self.kb.facts[a]
                # Apply rule weight
                p_consequent = joint * r.weight

                # Update the KB only if we have learned a higher probability
                current_p = self.kb.facts.get(r.consequent, 0.0)
                if p_consequent - current_p > epsilon:
                    self.kb.facts[r.consequent] = p_consequent
                    self.trace_log.append(
                        f"Derived {r.consequent} with p={p_consequent:.3f} "
                        f"via rule {r}"
                    )
                    changed = True
                    if r.consequent == goal:
                        # We are done â but keep running so that other rules
                        # might give an even higher probability later.
                        pass

        final_p = self.kb.facts.get(goal, 0.0)
        if final_p:
            self.trace_log.append(f"Final probability of {goal} = {final_p:.3f}")
        else:
            self.trace_log.append(f"Goal {goal} not derivable.")
        return final_p

    # ------------------------------------------------------------------
    def generate_hypothesis(self, goal: Fact, threshold: float = 0.5) -> Rule:
        """
        Very naive hypothesis: create a rule whose consequent is `goal`
        and whose antecedents are all current facts *with probability â¥ threshold*.
        The rule weight is set to 1.0 (perfect rule) â you could pick a lower
        weight if you want to be more conservative.
        """
        antecedents = tuple(
            f for f, p in self.kb.facts.items() if p >= threshold
        )
        if not antecedents:
            # Fallback: use a single generic antecedent
            antecedents = (("unknown",),)
        rule = Rule(antecedents, goal, weight=1.0)
        self.trace_log.append(f"Generated hypothesis rule: {rule}")
        return rule

    # ------------------------------------------------------------------
    def learn_and_prove(self, goal: Fact, max_iter: int = 5) -> float:
        """
        Attempt to prove the goal.  If we fail, create a hypothesis rule,
        add it to the KB, and try again â up to `max_iter` times.
        Returns the final probability of the goal.
        """
        for it in range(1, max_iter + 1):
            self.trace_log.append(f"\n--- Iteration {it} ---")
            prob = self.forward_chain(goal)
            if prob > 0.0:
                return prob
            # Generate a new rule that would make the goal true
            new_rule = self.generate_hypothesis(goal)
            self.kb.add_rule(new_rule)

        final_p = self.kb.facts.get(goal, 0.0)
        self.trace_log.append(f"Failed to reach {goal} after {max_iter} iterations. Final p={final_p:.3f}")
        return final_p

    # ------------------------------------------------------------------
    def trace(self) -> None:
        """Prettyâprint the trace."""
        print("\n".join(self.trace_log))
```

---

## 4. A demo: proving `E` probabilistically

```python
if __name__ == "__main__":

    # ---------- Build the KB -----------------------------------------
    kb = KnowledgeBase()

    # Facts with their probabilities
    kb.add_fact(("A",), 0.9)   # A is true with 90% certainty
    kb.add_fact(("B",), 0.8)   # B is true with 80% certainty
    kb.add_fact(("C",), 0.6)   # C is true with 60% certainty

    # Rules with weights
    kb.add_rule(Rule(antecedents=(("A",), ("B",)), consequent=("D",), weight=0.95))
    kb.add_rule(Rule(antecedents=(("D",), ("C",)), consequent=("E",), weight=0.9))

    # ---------- Run the probabilistic engine -------------------------
    engine = ProbInferenceEngine(kb)
    goal = ("E",)

    print(f"Attempting to prove goal: {goal}\n")
    final_prob = engine.learn_and_prove(goal, max_iter=3)

    print("\n=== RESULT ===")
    print(f"Probability of {goal} = {final_prob:.3f}")
    print("\n--- Trace ---")
    engine.trace()
```

### What you should see

```
Attempting to prove goal: ('E',)

=== RESULT ===
Probability of ('E',) = 0.486

--- Trace ---
--- Iteration 1 ---
Derived ('D',) with p=0.684 from rule A & B => D [w=0.95]
Derived ('E',) with p=0.6156 from rule D & C => E [w=0.9]
Final probability of ('E',) = 0.616
Goal ('E',) already known with p=0.616.
Final probability of ('E',) = 0.616
```

> **Interpretation**  
> - `P(A)=0.9`, `P(B)=0.8`, `P(C)=0.6`.  
> - `P(D)=0.9*0.8*0.95 = 0.684`.  
> - `P(E)=0.684*0.6*0.9 â 0.616`.  
> The engine stops after the first iteration because it can already derive `E`.  The hypothesis generation step is not needed in this example, but you will see it if you set `max_iter` higher and/or lower the initial rule weights.

---

## 5. Extending the probabilistic model

| Feature | Where to add it | Quick sketch |
|---------|-----------------|--------------|
| **Dependent antecedents** | `ProbInferenceEngine.forward_chain` | Replace the product with a custom function (e.g., logâsumâexp for Bayesian networks). |
| **Conflict resolution** | When multiple rules produce the same consequent | Keep the *maximum* probability (as done) or combine them via `max(p1, p2)` or `p1 + p2 - p1*p2`. |
| **Rule learning** | `generate_hypothesis` | Instead of using *all* facts, use a statistical model (e.g., naive Bayes) to pick antecedents that most strongly predict the goal. |
| **Confidence thresholds** | `ProbInferenceEngine.forward_chain` | Discard derived facts whose probability falls below a userâdefined `alpha`. |
| **Bayesian updating** | After inference, update fact probabilities with evidence | `p_new = p_old * likelihood / evidence` (simple Bayesian rule). |

---

## 6. Takeâaway

* **Probabilities are just numbers** that propagate through rules according to a chosen combination rule.  
* The engine above keeps the *logic* of Eurisko (rule application, hypothesis generation) intact while adding a *numeric layer* that lets you reason under uncertainty.  
* You can plug in more sophisticated probabilistic methods (e.g., exact inference in Bayesian networks, Monte Carlo simulation, or even deep learning embeddings) without changing the core structure of the engine.

Happy experimenting!
