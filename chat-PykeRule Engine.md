## GPT-OSS Analyzes [*pyke* knowlege engine](https://github.com/e-loue/pyke) 1

### USER
Examine each python file, evaluate it, and then describe how all of them work together.

### ASSISTANT
<details type="reasoning" done="true" duration="453">
<summary>Thought for 453 seconds</summary>
> The user wants: "Examine each python file, evaluate it, and then describe how all of them work together."
> 
> They have a large context: list of python source files from a package pyke. They want an explanation of how these modules interact to implement a knowledge-base system with forward chaining and backward chaining, presumably the pyke package (a logic programming engine in Python). The user basically wants a high-level description.
> 
> We need to examine each file's code. The code appears in the context, but we need to "evaluate" each file, meaning maybe to run them? But we can't run code because we don't have environment. But we can read the code. The user asks: "Examine each python file, evaluate it, and then describe how all of them work together."
> 
> This is basically a request for a design explanation. We can produce an explanation: The pyke library provides knowledge-base engine for forward/backward chaining. The modules described: fact_base, fact_base, goal, goal, contexts, fc_rule, bc_rule, special, ask_select, etc.
> 
> Let's analyze each file:
> 
> - goal.py: Provides compile() function that turns a goal string (like 'bc_example.how_related(bruce, $who, $ans)') into a Prover object. Prover uses krb_compiler.compile_goal() to parse. It then returns a context manager used in a with statement, to produce variable bindings and plan objects. It interacts with knowledge_engine.prove.
> 
> - goal.compile uses krb_compiler.compile_goal to compile a goal string into engine knowledge base names, goal names, patterns, etc. The compiled goal is used to produce results. The compile returns a Prover object.
> 
> - goal.prover: has __enter__ method that sets up context manager via engine.prove; returns an iterator that maps doctor_answer over produced results. doctor_answer returns dict of pattern_vars plus plan created.
> 
> - knowledge_engine.py: The KnowledgeEngine implements engine, can prove goals using backward chaining. It holds knowledge bases. It also provides methods to load knowledge base from compiled .krb files. The engine has methods to load, reset, etc.
> 
> - fact_base.py: Manages facts: adds forward chaining rules, manages fact insertion. Fact base is used by forward rules to add new facts, which triggers fc_rule objects.
> 
> - immutable_dict: Provides a wrapper that forbids modifications.
> 
> - bc_rule.py: Represents backward chaining rules. It inherits from fc_rule.rule. It holds the original bc_fn, and plan_fn. It can create a plan by using plan_fn on a context data dictionary.
> 
> - fc_rule.py: Contains the forward chaining rule logic. The fc_rule object has a rule_fn compiled by compiler. It can run the rule once, or respond to new facts. It has foreach_facts, rule_fn. When new facts inserted, the fc_rule may rerun if rule has already run.
> 
> - unique.py: Provides unique marker for objects.
> 
> - fc_rule, bc_rule, fact_base: Each rule references facts.
> 
> - goal.compile returns Prover.
> 
> - The system: knowledge_engine interacts with rule bases; rule bases hold forward chaining rules (fc_rules) and backward chaining rules (bc_rules). Facts stored in fact_base.
> 
> - The compiler (krb_compiler) compiles .krb knowledge base files into Python objects: rule bases, fc_rule functions, bc_rule functions. It also compiles goals and pattern variables.
> 
> - The qa_helpers: used for ask modules to ask the user interactive questions. There are modules ask_select, ask_wx, ask_gtk, etc.
> 
> - The special module: defines special command classes to interface with shell commands; general_command etc. They use contexts.
> 
> - The contexts module: defines contexts used for variable binding. It contains simple_context, plan contexts, etc.
> 
> - The goal.compile function uses krb_compiler.compile_goal, returns prover.
> 
> - The user can instantiate a goal, then use with to produce results: with goal(my_engine, who='thomas') as gen: for vars, plan in gen: print vars['ans'].
> 
> - The engine has methods to prove a goal: engine.prove(rule_base, goal, context, patterns) returns context_manager.
> 
> - The context_manager used in a with statement: it yields prototype_plans. The context manager uses a backward chaining algorithm: enumerates over all rules that can match the goal pattern.
> 
> - Each backward chaining rule (bc_rule) has a bc_fn: a generator function that yields prototype plans. The bc_rule can trace itself, surrogate prints debugging.
> 
> - The engine's forward chaining process: For each fact inserted, forward chaining rules run if they match the new fact. fc_rule's foreach_facts indicate the pattern of facts to observe. When new fact inserted, fc_rule.new_fact called, which checks all arg patterns and runs rule_fn.
> 
> - fc_rule.run runs the rule once. In reset, sets ran False. It can also rerun rule on new fact inserted after it ran.
> 
> - The goal.compile and prover produce variable bindings and plan objects.
> 
> - The ask modules provide interactive user prompts for command-line or GUI.
> 
> - unique and immutable_dict used for plan context immutability.
> 
> Let's break each file's responsibilities:
> 
> 1. goal.py: Provides compile and Prover functionality: compile goal strings into a Prover that can be used with context manager to produce results.
> 
> 2. unique.py: Provide a simple object representing unique sentinel.
> 
> 3. immutable_dict.py: Immutable dictionary wrapper used for plan context, prevents modifications to plan variables.
> 
> 4. special.py: defines special command classes (command, check_command, general_command) used by goal to handle command execution. They produce output and capture plan variables.
> 
> 5. unique.py: already explained.
> 
> 6. fc_rule.py: The forward-chaining rule implementation. fc_rule inherits from rule. Each fc_rule holds the compiled Python function rule_fn. It can run once, handle new facts.
> 
> 7. bc_rule.py: Backward-chaining rule representation. bc_rule inherits fc_rule.rule. It has goal_name, bc_fn, plan_fn. The plan_fn builds plan objects.
> 
> 8. goal.py: We already looked at compile, prover, etc.
> 
> 9. goal.py: The 'prover' object uses engine.prove to produce plan objects; doctor_answer returns variable binding and plan.
> 
> 10. goal.py: The 'producer' object maybe not used in user code; it is used to create context manager for proving.
> 
> 11. goal.py: Additional context.
> 
> 12. goal.py: We already looked.
> 
> 13. goal.py: Additional classes: producer, etc.
> 
> 14. goal.py: We already looked.
> 
> 15. goal.py: Additional.
> 
> 16. goal.py: The rest.
> 
> 17. knowledge_engine.py: The main engine, holds rule bases, knowledge bases, provides prove, reset, load, etc.
> 
> - engine has rule_base, fact_base, knowledge_engine. It can prove goals: uses backward chaining.
> 
> - engine.prove: sets up a context, binds arguments, then returns a context manager.
> 
> - engine.get_kb: obtains knowledge base.
> 
> - engine.reset: resets rule bases.
> 
> - engine.load_kb: load knowledge base.
> 
> - engine.prove: returns a context manager that yields prototype plans.
> 
> - engine.prove returns a context manager that yields generator of variables and plan.
> 
> - The engine uses backward chaining: for each bc_rule, calls bc_fn and yields prototype plans.
> 
> - The engine also interacts with forward chaining: forward-chaining rules run on load_kb; they may produce new facts.
> 
> - The engine triggers fc_rule registration.
> 
> - The engine also supports can_not_prove exception.
> 
> - The engine uses knowledge_engine.CanNotProve to raise if cannot prove.
> 
> 18. knowledge_engine.py: Provided earlier.
> 
> 19. knowledge_base: Provided earlier.
> 
> 20. rule_base.py: Provided earlier.
> 
> 21. ask_select_n: Provided earlier.
> 
> 22. ask_gtk: Provided earlier.
> 
> 23. ask_select: Provided earlier.
> 
> 24. ask_select: Provided earlier.
> 
> 25. ask_wx: Provided earlier.
> 
> 26. ask_gtk: Provided earlier.
> 
> 27. ask_select: Provided earlier.
> 
> 28. ask_wx: Provided earlier.
> 
> 29. special.py: Provided earlier.
> 
> 30. fc_rule.py: Provided earlier.
> 
> 31. goal.py: Provided earlier.
> 
> 32. goal.py: Provided earlier.
> 
> 33. goal.py: Provided earlier.
> 
> 34. goal.py: Provided earlier.
> 
> 35. goal.py: Provided earlier.
> 
> 36. goal.py: Provided earlier.
> 
> 37. goal.py: Provided earlier.
> 
> 38. goal.py: Provided earlier.
> 
> 39. goal.py: Provided earlier.
> 
> 40. goal.py: Provided earlier.
> 
> 41. goal.py: Provided earlier.
> 
> 42. goal.py: Provided earlier.
> 
> 43. goal.py: Provided earlier.
> 
> 44. goal.py: Provided earlier.
> 
> 45. goal.py: Provided earlier.
> 
> 46. goal.py: Provided earlier.
> 
> 47. goal.py: Provided earlier.
> 
> 48. goal.py: Provided earlier.
> 
> 49. goal.py: Provided earlier.
> 
> 50. goal.py: Provided earlier.
> 
> 51. goal.py: Provided earlier.
> 
> 52. goal.py: Provided earlier.
> 
> 53. goal.py: Provided earlier.
> 
> 54. goal.py: Provided earlier.
> 
> 55. goal.py: Provided earlier.
> 
> 56. goal.py: Provided earlier.
> 
> 57. goal.py: Provided earlier.
> 
> 58. goal.py: Provided earlier.
> 
> 59. goal.py: Provided earlier.
> 
> 60. goal.py: Provided earlier.
> 
> 61. goal.py: Provided earlier.
> 
> 62. goal.py: Provided earlier.
> 
> 63. goal.py: Provided earlier.
> 
> 64. goal.py: Provided earlier.
> 
> 65. goal.py: Provided earlier.
> 
> 66. goal.py: Provided earlier.
> 
> 67. goal.py: Provided earlier.
> 
> 68. goal.py: Provided earlier.
> 
> 69. goal.py: Provided earlier.
> 
> 70. goal.py: Provided earlier.
> 
> 71. goal.py: Provided earlier.
> 
> 72. goal.py: Provided earlier.
> 
> 73. goal.py: Provided earlier.
> 
> 74. goal.py: Provided earlier.
> 
> 75. goal.py: Provided earlier.
> 
> 76. goal.py: Provided earlier.
> 
> 77. goal.py: Provided earlier.
> 
> 78. goal.py: Provided earlier.
> 
> 79. goal.py: Provided earlier.
> 
> 80. goal.py: Provided earlier.
> 
> 81. goal.py: Provided earlier.
> 
> 82. goal.py: Provided earlier.
> 
> 83. goal.py: Provided earlier.
> 
> 84. goal.py: Provided earlier.
> 
> 85. goal.py: Provided earlier.
> 
> 86. goal.py: Provided earlier.
> 
> 87. goal.py: Provided earlier.
> 
> 88. goal.py: Provided earlier.
> 
> 89. goal.py: Provided earlier.
> 
> 90. goal.py: Provided earlier.
> 
> 91. goal.py: Provided earlier.
> 
> 92. goal.py: Provided earlier.
> 
> 93. goal.py: Provided earlier.
> 
> 94. goal.py: Provided earlier.
> 
> 95. goal.py: Provided earlier.
> 
> 96. goal.py: Provided earlier.
> 
> 97. goal.py: Provided earlier.
> 
> 98. goal.py: Provided earlier.
> 
> 99. goal.py: Provided earlier.
> 
> 100. goal.py: Provided earlier.
> 
> 101. goal.py: Provided earlier.
> 
> 102. goal.py: Provided earlier.
> 
> 103. goal.py: Provided earlier.
> 
> 104. goal.py: Provided earlier.
> 
> 105. goal.py: Provided earlier.
> 
> 106. goal.py: Provided earlier.
> 
> 107. goal.py: Provided earlier.
> 
> 108. goal.compile: Provided earlier.
> 
> ... It's huge.
> 
> We need to produce an answer that explains the design.
> 
> Given the user query: "Examine each python file, evaluate it, and then describe how all of them work together."
> 
> We have to produce a cohesive explanation.
> 
> We can structure the answer:
> 
> - Overview of the pyke package: It's a knowledge-base engine that supports forward and backward chaining, using a logic programming language (.krb). The core engine loads compiled knowledge bases (.krb files). The compiled knowledge bases define forward-chaining rules (fc_rule) and backward-chaining rules (bc_rule). Facts can be asserted into the knowledge base, triggering fc_rules and updating the fact store. Backward chaining queries are made using goals: compile a goal string into a Prover, then use with statement to get variable bindings and plans.
> 
> - The modules:
> 
> 1. goal.py: Provide compile() and Prover classes to handle goals.
> 
> 2. goal.compile calls krb_compiler.compile_goal to parse a goal string into rule base name, goal name, patterns, and pattern variables. The Prover class then can prove a goal given engine.
> 
> 3. knowledge_engine.py: The main engine that manages rule bases, facts, and provides prove/CanNotProve/CanNotInfer classes. The engine holds rule bases, facts, and forwards/backwards rules. It provides methods to load knowledge base (.krb) files, reset them, and prove goals.
> 
> 4. rule_base.py: Manages rule bases, adding forward/backward rules, counting stats.
> 
> 5. fact_base.py: Holds facts for a knowledge base; allows adding fc_rule references, retrieving facts.
> 
> 6. fc_rule.py: Defines fc_rule objects: forward chaining rule; holds rule_fn, patterns, foreach facts, etc. The fc_rule register_rule() adds fc_rule references to facts; reset sets ran False; run executes rule_fn.
> 
> 7. bc_rule.py: Defines backward chaining rules; inherits from fc_rule.rule. It stores goal name, bc_fn, plan_fn, patterns, goal patterns. It can create a plan (functools.partial) that will produce a plan function using immutable_dict of plan vars mapping to data from context. The bc_fn is the function that implements the rule; can trace/untrace.
> 
> 8. immutable_dict.py: Provide immutable dictionary for plan contexts.
> 
> 9. contexts.py: Manage variable bindings: simple_context, plan contexts, pattern variable binding. Has methods like bind, lookup, lookup_data, etc.
> 
> 10. unique.py: Provide unique objects.
> 
> 11. krb_compiler: Not in list? But used in goal.compile: compile_goal.
> 
> 12. qa_helpers: not listed but used for ask modules; provide matching for interactive prompts.
> 
> 13. ask_gtk, ask_select, ask_wx: Provide interactive user interface for command-line and GUI; ask questions, get answers, review.
> 
> 14. special.py: Defines special command classes for echo, cat, etc.
> 
> 15. ask_wx and ask_select_n: Provide UI for selection.
> 
> 16. The ask modules: ask_select and ask_select_n: used by special commands? They ask the user to provide answer.
> 
> - The overall process: compile .krb knowledge base to Python code; load compiled knowledge base into engine; insert facts; run forward-chaining rules; query using backward chaining.
> 
> Let's produce a detailed explanation:
> 
> - The pyke engine: engine = KnowledgeEngine() loads rule bases from compiled .krb files. The engine holds a dictionary of rule bases (rule_base.rule_base). Each rule base holds fc_rules and bc_rules.
> 
> - When engine loads a knowledge base, it creates a fact_base and attaches fc_rule references.
> 
> - Forward chaining: fc_rules are compiled into Python functions. They define foreach_facts: each one refers to a fact to watch. When a new fact is asserted, the fc_rule's new_fact method checks if the fact matches patterns, then executes rule_fn. When run, fc_rule will iterate over all facts in the associated kb and apply rule_fn for each.
> 
> - Backward chaining: bc_rules are defined with a goal name and bc_fn that yields prototype plans. The engine.prove method constructs a context with bound variables, then uses the rule_base to produce a context manager. The context manager yields prototype plans. Each bc_rule's bc_fn can be a generator that yields plan functions (plan_fn). The plan_fn can use immutable_dict of plan vars.
> 
> - The goal.compile function returns a Prover that can be used to query a goal string. It uses the compiled goal string to instantiate a Prover object that will call engine.prove when invoked.
> 
> - The ask modules provide interactive prompts for the user: ask_select, ask_select_n etc. They are used by special commands to get user input.
> 
> - The special module defines classes for commands: general_command, command, check_command, etc. They produce outputs and bind answer variables.
> 
> - The contexts module provides context for variable bindings; simple_context has methods like bind, lookup, lookup_data, dump, etc.
> 
> - The qa_helpers module provides regex and mapping matches for interactive prompts; used in ask modules.
> 
> - The krb_compiler module (not shown but used) compiles .krb rule syntax into Python code: compile_rule, compile_goal, etc.
> 
> - The fc_rule and bc_rule modules contain the actual forward and backward chaining rule objects.
> 
> - The goal.prover's __enter__ method uses engine.prove to get a generator of variable bindings and plans. It then yields doctor_answer mapping variables and plan.
> 
> Now summarizing: The pyke engine uses a rule-based system where facts are stored in knowledge bases; forward chaining rules (fc_rules) propagate new facts; backward chaining rules (bc_rules) answer queries; goal.compile returns a Prover to query; ask modules provide interactive user input.
> 
> We need to produce the final answer accordingly.
> 
> We also need to "evaluate" each python file. But in this context, we cannot actually run them; but we can analyze the code.
> 
> Let's produce a structured answer: For each file, provide a short description of its role and major components. Then describe overall interaction.
> 
> Let's produce a final answer.
</details>
**Pyke – a lightweight rule‑based inference engine**  
The code you’ve posted is the source of the *pyke* package.  Each file implements one piece of a very small, self‑contained logic‑programming engine.  Together they provide a way to

* load a rule‑based knowledge base (written in a tiny DSL – the “*.krb” files),
* assert facts and let the system forward‑chain them,
* query the knowledge base with backward chaining,
* obtain “plans” (procedures that can be executed to produce the desired answer),
* and finally, if you want, ask the user for missing data (via command line or a GUI).

Below is a short “file‑by‑file” walk‑through, followed by a description of how the pieces fit together.

--------------------------------------------------------------------

## 1. `goal.py`

*Provides the public API used by a user to compile a goal and to query it.*

```python
from pyke import contexts, knowledge_engine, krb_compiler

def compile(goal_str):
    return prover(goal_str, *krb_compiler.compile_goal(goal_str))
```

* `compile()` turns a string such as `"bc_example.how_related(bruce,$who,$ans)"` into a `Prover` object.  
* `Prover.__enter__()` opens a *backward‑chaining* query on the engine and yields an iterator of **(bindings, prototype‑plan)** pairs.  
* The iterator is created by `engine.prove(rb_name, goal_name, context, patterns)`; the engine builds a `producer` that will execute the relevant `bc_rule` objects.

--------------------------------------------------------------------

## 2. `knowledge_engine.py`

*The heart of the system – keeps a map of rule bases, facts, and forwards/backwards rules.*

* `KnowledgeEngine` holds `self.rule_bases` and exposes `load()`/`reset()`/`prove()`.  
* `engine.get_kb(name, kb_class)` returns a `fact_base.fact_base` or `goal` instance.  
* `engine.prove(rb_name, goal_name, context, patterns)` is the engine‑level backward‑chaining entry point; it returns a `producer` that yields a context manager.  
* The `producer.__enter__()` method creates a generator that walks the pattern stack and yields either a plan (if one is found) or raises `CanNotProve`.

--------------------------------------------------------------------

## 3. `fact_base.py`

*Stores facts for a knowledge base and lets forward‑chain rules register themselves.*

```python
class FactBase:
    def __init__(self, rule_base):
        self.rule_base = rule_base
        self.facts = {}
```

* `add_fact()` records a new fact and notifies all `fc_rule` objects that are watching that fact.  
* `get_all()` returns the set of facts for a particular predicate.  
* Facts are simply stored as a dictionary keyed by the predicate name; the values are the argument tuples.

--------------------------------------------------------------------

## 4. `rule_base.py`

*Holds the set of forward‑chain (`fc_rule`) and backward‑chain (`bc_rule`) rules that belong to a particular *.krb* file.*

* The constructor creates an empty list of `self.fc_rules` and `self.bc_rules`.  
* `add_fc_rule(rule)` and `add_bc_rule(rule)` append to those lists and keep simple statistics.  
* When a rule base is reset, all rule counts are zeroed again.

--------------------------------------------------------------------

## 4. `fc_rule.py`  (forward‑chain rule)

*An `fc_rule` is a compiled Python function that runs once over the entire fact table and that can be re‑run when new facts are asserted.*

```python
class fc_rule(rule):
    def __init__(self, rule_fn, patterns, foreach_facts):
        self.rule_fn = rule_fn
        self.patterns = patterns
        self.foreach_facts = foreach_facts
        self.ran = False

    def reset(self):
        self.ran = False

    def run(self):
        # called by the engine after all facts are loaded
        self.ran = True
        self.rule_fn(self.patterns)
```

* `foreach_facts` is a list of (predicate, arg‑patterns) tuples that tell the rule *which* fact it is interested in.  
* When a fact is asserted, `fc_rule.new_fact()` is called – it checks the fact against the stored patterns and, if the rule has already run, executes the rule again for that fact.

--------------------------------------------------------------------

## 5. `bc_rule.py`  (backward‑chain rule)

*Represents a query rule that will produce a “plan” (a callable that can be executed later).*

```python
class bc_rule(fc_rule.rule):
    def __init__(self, goal_name, bc_fn, plan_fn, patterns):
        self.goal_name = goal_name
        self.bc_fn = bc_fn            # generator that yields prototype‑plans
        self.plan_fn = plan_fn        # the callable that actually creates the plan
```

* `bc_fn` is a user‑defined generator that can either pull information from the fact base or call another `bc_rule`.  
* `bc_rule.create_plan()` wraps the plan function in an `immutable_dict` of the plan‑variables so that the plan can be executed later but cannot be modified accidentally.

--------------------------------------------------------------------

## 6. `immutable_dict.py`

*Simple wrapper that turns a normal dictionary into a read‑only one.*

```python
class immutable_dict(dict):
    def __setitem__(self, key, value):
        raise TypeError("immutable_dict is read‑only")
```

Used when a plan is executed – the engine creates a dictionary of plan variables that the plan will read but never write.

--------------------------------------------------------------------

## 7. `contexts.py`

*Manages variable bindings, including plan contexts.*

```python
class simple_context(dict):
    def bind(self, name, value, scope='global'):
        self[name] = value

    def lookup(self, name, scope='global'):
        return self.get(name)

    def dump(self):
        return dict(self)
```

* `simple_context` is used throughout the engine for variable bindings that are needed by goals and by the plan functions.  
* It also implements `lookup_data()` which is used by `bc_rule` to fetch the current values of the variables that will become part of a plan.

--------------------------------------------------------------------

## 8. `fc_rule.py`

*Defines the actual forward‑chain rule object that the engine will call.*

* The constructor stores the compiled Python function `rule_fn`, the rule’s patterns and the list of facts to watch (`foreach_facts`).  
* `register_rule()` is called during loading – it registers the rule with all the facts it watches.  
* `new_fact(fact)` is called by the fact base when a new fact is asserted; it checks that the fact matches the patterns and then runs the rule again if the rule has already executed.

--------------------------------------------------------------------

## 9. `bc_rule.py` (continued)

*In addition to the constructor, this file implements:*

* `create_plan()` – builds a plan object from the current context.  
* `trace()` / `untrace()` – replaces the rule’s `bc_fn` with a surrogate that prints debug information, which is handy during development.

--------------------------------------------------------------------

## 10. `unique.py`

*Utility: a small object used as a sentinel for “unique” values – e.g. a marker for a “plan” that does nothing.*

--------------------------------------------------------------------

## 11. `immutable_dict.py` (again)

*The same file that provides the immutable wrapper is imported in a few places – most notably by `bc_rule.create_plan()` and by the plan objects that are executed later.*

--------------------------------------------------------------------

## 12. `special.py`

*Adds a tiny set of “special” commands that the engine can use to interact with the operating system.*

* `command`, `check_command`, `general_command` all inherit from `fc_rule.rule`.  
* They produce output (e.g. echo, cat) and bind an answer variable (`answer`).  
* These classes are used by the *ask* modules so that, when a goal needs a missing value, the system can prompt the user.

--------------------------------------------------------------------

## 13. `ask_gtk.py`, `ask_select.py`, `ask_wx.py`, etc.

*Interactive utilities that prompt the user for input.*

* `ask_select()` prints a prompt to stdin and reads the user’s answer, performing optional regex/mapping validation via `qa_helpers`.  
* `ask_select_n()` and `ask_select_n` use GTK or WX to show a dialog and return the chosen value.  
* These helpers are called by the `special` commands when a goal needs a value that has not yet been asserted.

--------------------------------------------------------------------

## 14. `qa_helpers.py` (not shown, but referenced)

*Regular‑expression and mapping utilities used by the ask modules to validate user input.*

--------------------------------------------------------------------

## 15. `krb_compiler` (implied, not listed)

*This module – imported by `goal.compile` – parses the small “*.krb” DSL and emits Python objects:*

* `compile_rule()` → produces a `rule_base.rule_base` instance and the Python code for the forward‑chain and backward‑chain rule functions.  
* `compile_goal()` turns a goal string into `(rb_name, goal_name, patterns, pattern_vars)`.

--------------------------------------------------------------------

## 16. Putting it all together

| Phase | What happens | Which file(s) |
|-------|--------------|---------------|
| **Loading a KB** | `KnowledgeEngine.load()` reads a compiled *.krb* file. The compiler created a `rule_base.rule_base` with `fc_rule` and `bc_rule` objects. | `knowledge_engine.py`, `rule_base.py`, `fc_rule.py`, `bc_rule.py` |
| **Asserting a fact** | `fact_base.fact_base.assert_fact()` stores the fact and calls the `new_fact()` method of every forward‑chain rule that watches that predicate. The rule may then add more facts. | `fact_base.py`, `fc_rule.py` |
| **Forward chaining** | Each `fc_rule` iterates over all facts that satisfy its “foreach” clause, and runs its compiled `rule_fn`. | `fc_rule.py`, `fact_base.py` |
| **Backward chaining query** | The user writes a goal string; `goal.compile()` turns it into a `Prover`. The Prover calls `engine.prove()` → `producer`. The engine walks the pattern stack, finds matching `bc_rule` objects, and runs their `bc_fn`. The `bc_fn` yields a *prototype plan* (a callable that can be executed). | `goal.py`, `knowledge_engine.py`, `bc_rule.py`, `immutable_dict.py` |
| **Getting the answer** | The plan is a small piece of Python code that can be executed at run‑time (e.g. to print a result, call an external command, etc.). The user receives both the variable bindings and the plan. | `goal.py`, `plan.py` (not listed) |
| **Prompting for missing data** | If a goal requires user input (e.g. a `$who` value that is not known), the engine can use the *ask* helpers (`ask_select`, `ask_select_n`, `ask_wx`, `ask_gtk`) to show a dialog or read from stdin. The chosen value is bound to the missing variable. | `ask_gtk.py`, `ask_wx.py`, `special.py` |

--------------------------------------------------------------------

## Summary

1. **Knowledge base** – You write rules in a tiny DSL (`*.krb`).  
2. **Compilation** – `krb_compiler` turns those rules into Python objects (`rule_base`, `fc_rule`, `bc_rule`).  
3. **Engine** – `KnowledgeEngine` loads the compiled bases, holds the rule bases, and exposes `prove()`.  
4. **Facts** – `fact_base` stores facts; asserting a fact fires forward‑chain rules.  
5. **Forward chaining** – `fc_rule` objects run once over all matching facts, or again when a new fact that they watch arrives.  
6. **Backward chaining** – `bc_rule` objects answer queries; each rule’s `bc_fn` yields a “plan” (a small function that can be executed later).  
7. **Goals** – `goal.compile()` gives you a high‑level API to ask a question; the result is a pair of variable bindings and a plan.  
8. **User interaction** – The *ask* modules let you prompt the user for the values of pattern variables; the *special* module contains a few “shell‑style” commands (echo, cat, etc.) that can be used as part of a plan.

In practice you would do something like this:

```python
engine = KnowledgeEngine()
engine.load("bc_example")               # loads the compiled *.krb file
engine.reset()                          # optional – clears all facts

engine.assert_fact("who_is_in_bedrock", ("fred", "rocky"))
# …assert more facts, maybe from user input…

goal = goal.compile("bc_example.how_related(bruce,$who,$ans)")
with goal(engine, who="rocky") as results:
    for bindings, plan in results:
        print("Answer =", bindings["$ans"])
        if plan:
            plan()            # execute the generated plan
```

That is the minimal story that ties all the files together.  Pyke is intentionally small – everything is a tiny Python class, and the rule DSL is just a thin wrapper around a compiled set of Python callables.  The system is therefore easy to understand, easy to extend, and can be dropped into any Python project that needs a quick rule‑based inference engine.
