## GPT-OSS Using Python owlready2 and OWL Ontologies with Ollama LLMs

### USER
How can I use Ollama LLMs with python owlready2 for using .owl ontologies with GTP-OSS?

### ASSISTANT
<details type="reasoning" done="true" duration="48">
<summary>Thought for 48 seconds</summary>
> We need to answer: How can use Ollama LLMs with python owlready2 for using .owl ontologies with GTP-OSS? Likely the user wants to integrate local LLM (Ollama) with ontology reasoning via owlready2, maybe feeding ontology into prompt for GPT-OSS? We need to explain steps: install Ollama, get LLM, use python API to call model. Then use owlready2 to load ontology, query with SPARQL, maybe convert ontology to natural language, embed in prompt. Also show how to create embeddings or use model to reason on ontology. Provide code examples. Use GTP-OSS? I think GTP-OSS refers to GPT-OSS (open-source GPT). So integrate. Provide pipeline: load ontology, extract relevant info, ask LLM. Use context or prompt injection. Also maybe use the LLM to answer questions about ontology, or to generate code. Show how to embed the ontology in vector store. Also mention huggingface huggingface_hub, and local model inference via llama.cpp or ollama. Use python's ollama package: pip install ollama. Use `ollama.run` to get completion. Provide sample prompts: "Given the following OWL ontology snippet, what is the relationship between X and Y?" Use the ontology loaded into owlready2 to extract triples or descriptions.
> 
> Also mention usage of GTP-OSS as an LLM library; but maybe the user means using GTP-OSS to process queries? Provide code skeleton. Also mention concurrency and safety. Also mention using knowledge graph embeddings. Provide advanced example: generate semantic query via LLM, parse to SPARQL.
> 
> So produce answer with sections: prerequisites, installing Ollama, installing owlready2, loading ontology, extracting info, constructing prompt, calling Ollama, processing response. Show example. Also mention best practices. Also mention using `langchain_community.document_loaders`? But keep it simple.
> 
> Let's produce.
</details>
Below is a practical “cook‑book” that shows you how to combine:

| Tool | Purpose |
|------|---------|
| **Ollama** | Run a local LLM (any `gpt‑oss`‑style model you’ve pulled into Ollama) |
| **Python** | Glue code that talks to Ollama, talks to an OWL file and does some light‑weight inference |
| **owlready2** | Load, inspect and run very light OWL reasoning (OWL‑lite/OWL‑DL) |
| **Optional – LangChain** | A convenient wrapper if you want to keep the prompt‑engineering part tidy |

The idea is to

1. **Load the ontology** in `owlready2`.
2. **Pull out the facts / axioms you care about** (e.g. all `rdfs:subClassOf` triples, all annotations, etc.).
3. **Turn that information into a prompt** you feed to the local LLM.
4. **Ask the LLM a question** (or let it generate a SPARQL query, a Python function, or a natural‑language answer).
5. **Return the answer to your app**.

Below is a step‑by‑step walkthrough with runnable snippets.

---

## 1. Install the prerequisites

```bash
# Python 3.11+ recommended
pip install owlready2
pip install ollama      # python‑client for Ollama

# (Optional) If you want LangChain for prompt handling
pip install langchain langchain-community
```

### 1.1 Pull a local LLM into Ollama

Open a terminal and run:

```bash
# Example: install a 7B gpt‑oss model
ollama pull gpt-oss-7b
```

If you already have a different model, just replace the name.

---

## 2. Load the ontology with `owlready2`

```python
import owlready2 as owl
from pathlib import Path

def load_ontology(owl_file: str) -> owl.onto:
    """
    Load an OWL file and return the ontology object.
    """
    onto_path = Path(owl_file).resolve()
    onto = owl.get_ontology(str(onto_path)).load()
    return onto

onto = load_ontology("example_ontology.owl")
```

### 2.1 Quick inspection helpers

```python
def list_classes(onto):
    return [c.name for c in onto.classes()]

def list_object_properties(onto):
    return [p.name for p in onto.object_properties()]

def list_individuals(onto):
    return [i.name for i in onto.individuals()]

print("Classes:", list_classes(onto))
print("Object Properties:", list_object_properties(onto))
print("Individuals:", list_individuals(onto))
```

### 2.2 Export a minimal description

```python
def ontology_summary(onto, max_lines=20):
    """Return a short, human‑readable summary of the ontology."""
    desc = []
    for cls in onto.classes():
        # Class definition
        desc.append(f"Class: {cls.name}")
        # Super‑classes
        if cls.is_a:
            supers = ", ".join([s.name for s in cls.is_a if isinstance(s, owl.Thing)])
            desc.append(f"  Subclass of: {supers}")
        # Equivalent classes
        if cls.equivalent_to:
            eq = ", ".join([str(e) for e in cls.equivalent_to])
            desc.append(f"  Equivalent to: {eq}")
    return "\n".join(desc[:max_lines])

print(ontology_summary(onto))
```

---

## 3. Build the prompt

A clean prompt keeps the model focused. Two common patterns:

| Pattern | When to use it |
|---------|----------------|
| **Context‑First** | You feed the entire ontology snippet, then ask a question. |
| **Question‑First** | Ask a question that explicitly references the ontology’s namespace. |

### 3.1 Context‑First example

```python
# Take the first 1000 characters of the ontology file
with open("example_ontology.owl", "r", encoding="utf-8") as f:
    ontology_text = f.read()[:1000]

prompt = f"""
You are an ontology expert. The following snippet is an excerpt of an OWL ontology:

{ontology_text}

Answer the following question:
What is the relationship between {list_classes(onto)[0]} and {list_classes(onto)[1]}?
"""
```

### 3.2 Question‑First example

```python
prompt = f"""
You have an ontology loaded with the following classes: {", ".join(list_classes(onto))}.
What does {list_classes(onto)[0]} inherit from in this ontology?
"""
```

**Tip:** Keep the prompt under ~2048 tokens if you’re using a small local model; otherwise, use a longer prompt and rely on your model’s context window.

---

## 4. Call Ollama from Python

```python
import ollama

def ask_llm(prompt: str, model: str = "gpt-oss-7b", temperature: float = 0.2, max_tokens: int = 512):
    """
    Sends a prompt to Ollama and returns the model’s response.
    """
    response = ollama.generate(
        model=model,
        prompt=prompt,
        temperature=temperature,
        max_tokens=max_tokens,
        stream=False
    )
    return response["response"]

answer = ask_llm(prompt)
print("LLM says:", answer)
```

If you want streaming responses:

```python
for chunk in ollama.stream_generate(
        model="gpt-oss-7b",
        prompt=prompt,
        temperature=0.2,
        max_tokens=512):
    print(chunk["response"], end="", flush=True)
```

---

## 5. Use the answer in your app

Typical use‑cases:

1. **Natural‑Language QA** – Show the answer to a user.
2. **Generate SPARQL** – Let the LLM produce a query, then execute it with `owlready2`.
3. **Code Generation** – The LLM can output Python code that manipulates the ontology.
4. **Ontology Extension** – The LLM can suggest new classes / axioms that you can validate with `owlready2`.

### 5.1 Example: LLM‑generated SPARQL

```python
# Prompt the LLM to produce a SPARQL query that finds all subclasses of a given class
sparql_prompt = f"""
You are a SPARQL expert. Given the ontology {onto.base_iri}, write a SPARQL query that returns all subclasses of the class {list_classes(onto)[0]}.
Answer with only the query.
"""

sparql_query = ask_llm(sparql_prompt)

print("Generated SPARQL:\n", sparql_query)

# Execute it
result = onto.query(sparql_query)
for row in result:
    print(row)
```

---

## 6. Optional: Using LangChain for cleaner prompts

If you find yourself writing a lot of prompt‑building logic, LangChain can help:

```python
from langchain import PromptTemplate, LLMChain
from langchain.llms import Ollama

llm = Ollama(model="gpt-oss-7b")

template = PromptTemplate(
    input_variables=["class_a", "class_b"],
    template=(
        "You have an ontology with the following classes: {classes}.\n"
        "What is the relationship between {class_a} and {class_b}?"
    ),
)

chain = LLMChain(llm=llm, prompt=template)

response = chain.run(
    classes=", ".join(list_classes(onto)),
    class_a=list_classes(onto)[0],
    class_b=list_classes(onto)[1]
)
print(response)
```

---

## 7. Advanced: Embedding the ontology for retrieval

If you have a very large ontology and want to let the LLM query it by *retrieval‑augmented generation*:

1. **Embed the triples** with a sentence‑embedding model (e.g. `sentence-transformers/paraphrase-MiniLM-L6-v2`).
2. Store embeddings in a vector store (FAISS, Weaviate, or `pinecone`).
3. For a user query, embed the query, fetch the top‑k relevant triples, and paste them into the prompt.

```python
from sentence_transformers import SentenceTransformer
import faiss
import numpy as np

# 1. Prepare a list of text snippets
triples = [
    f"{s} rdfs:subClassOf {o}"
    for s, p, o in onto.world.get_triples()
    if p == owl.onto.onto['rdfs:subClassOf']
]

# 2. Encode
model = SentenceTransformer("paraphrase-MiniLM-L6-v2")
embeddings = model.encode(triples, convert_to_numpy=True)

# 3. Build FAISS index
d = embeddings.shape[1]
index = faiss.IndexFlatL2(d)
index.add(embeddings)

# 4. Query
query_text = "What is a subclass of Vehicle?"
query_vec = model.encode([query_text], convert_to_numpy=True)
D, I = index.search(query_vec, k=3)  # top‑3

retrieved = "\n".join([triples[i] for i in I[0]])

# 5. Build prompt with retrieved snippets
prompt = f"""
Here are some ontology facts relevant to your question:

{retrieved}

Answer: 
"""

print(prompt)
```

Now feed that prompt to Ollama and you get a more accurate answer that is grounded in the ontology.

---

## 8. Things to watch out for

| Issue | Mitigation |
|-------|------------|
| **Large ontologies** | Use FAISS retrieval or split the ontology into chunks. |
| **Model context limits** | Trim the prompt, or summarise the ontology. |
| **Model hallucinations** | Post‑process the LLM output, verify with owlready2. |
| **Ontology reasoning** | `owlready2` supports a small OWL‑DL reasoner; for heavy reasoning use `Pellet` or `HermiT` via Java interop. |
| **Thread‑safety** | The Ollama client is thread‑safe, but keep an eye on CPU/GPU load when batching. |

---

## 9. Wrap‑up

1. **Load** your `.owl` file with `owlready2`.
2. **Extract** the bits you care about (classes, properties, individuals, triples).
3. **Craft** a prompt that contains that context and a clear question or task.
4. **Send** the prompt to your local Ollama LLM using the Python API.
5. **Consume** the answer: show it to a user, run a generated SPARQL query, or generate new ontology axioms.

With this pattern you can build a local knowledge‑grounded LLM that speaks the language of OWL, all without sending data to a cloud provider. Happy coding!

### USER
What strategies do you recommend for handling large ontologies that exceed the LLM’s context window?

### ASSISTANT
<details type="reasoning" done="true" duration="28">
<summary>Thought for 28 seconds</summary>
> We need to give strategies for handling large ontologies that exceed the LLM's context window. Provide techniques: chunking, summarization, retrieval-augmented generation, embedding with vector DB, dynamic prompt, hierarchical context, progressive retrieval, use of external reasoning, knowledge graph embedding, selective extraction, query generation, caching, multi-pass prompting, etc. Also mention using OWL reasoning to precompute certain facts, using specialized tools like FAISS. Provide code snippets. Provide practical examples. Should consider trade-offs. Provide recommendations for combining with LLMs. Provide examples. Also mention using local vector store, chunking by class, using embeddings for subgraphs. Also mention prompt engineering for large contexts. Mention caching. Also mention knowledge graph query languages like SPARQL. Provide step-by-step guidelines. Mention incremental approach: first ask LLM to produce a concise description of relevant subset. Also mention hierarchical summarization. Also mention using embeddings to retrieve relevant sections. Also mention using external tools like graph databases. Also mention that you can ask the LLM to produce a query to retrieve needed parts from ontology. Provide details.
> 
> Ok produce answer.
</details>
Below is a practical play‑book for **“LLM + Big Ontology”** – a set of patterns, tools and code snippets that let you keep a huge OWL graph on‑disk while still feeding *only the bits that matter* into a local LLM whose context window (4 k, 8 k, 16 k tokens, etc.) is far smaller than the ontology’s raw size.

| Strategy | What it does | When to use | Typical tooling | Quick code sketch |
|----------|--------------|-------------|-----------------|-------------------|
| **Chunk & Summarise** | Split the ontology into logical sub‑graphs (by namespace, class, or depth) and replace each chunk with a 1‑2 sentence summary. | The ontology is *self‑contained* – you can pre‑compute a small digest for every sub‑graph. | `owlready2`, `textwrap`, `tiktoken` for token counting | See §3 |
| **Retrieval‑Augmented Generation (RAG)** | Encode every triple (or sub‑graph) into vectors, store in a vector DB, fetch top‑k most relevant snippets for each prompt. | Ontology is too big for any form of summarisation, or you need *exact* facts. | `sentence-transformers`, `FAISS`, `pinecone`, `weaviate` | See §5 |
| **Incremental / Recursive Prompting** | Ask the LLM for a “mini‑outline” of the topic first; then iteratively drill down, each time adding a new chunk of context. | You want the LLM to decide which parts of the ontology are relevant. | `langchain`, `ollama` | See §6 |
| **External Reasoner + LLM** | Let an OWL reasoner (Pellet, HermiT, OWLAPI) compute all entailments, then query the reasoner via SPARQL or OWLAPI. Feed *only the answer* to the LLM. | You need rigorous logical inference, not just surface facts. | `owlready2` reasoning, `rdflib` SPARQL | See §7 |
| **Knowledge‑Graph Embedding (KGE)** | Train a KGE model on the full ontology; use embeddings to rank the “closest” entities to a query, then only load those sub‑graphs. | Ontology is huge, you want *latent* similarity, not explicit triples. | `openKE`, `pykeen`, `ampligraph` | See §8 |
| **Hybrid Graph‑Database + LLM** | Persist the ontology in a graph database (Neo4j, Blazegraph) and let the LLM send a natural‑language query → DB → result → LLM. | You have existing graph infrastructure; you want to keep the LLM stateless. | Neo4j + Cypher, `py2neo` | See §9 |
| **Cache & Re‑use** | Cache the “retrieved chunks” for a session or user; avoid recomputing the same subset of triples. | High‑frequency queries over the same sub‑graph. | `diskcache`, `sqlite` | See §10 |

---

## 1. Chunk & Summarise (Section §3)

```python
import owlready2 as owl
import textwrap
from tiktoken import get_encoding

def chunk_and_summarise(onto, max_tokens=200):
    """
    Return a list of {name, summary, tokens} dicts,
    one for each class (or namespace, etc.).
    """
    enc = get_encoding("cl100k_base")   # GPT‑4 tokeniser
    summaries = []
    for cls in onto.classes():
        # A crude “summary” – just the subclass relations
        supers = [s.name for s in cls.is_a if isinstance(s, owl.Thing)]
        subs   = [c.name for c in cls.subclasses()][:3]   # top 3 subclasses

        txt = f"Class {cls.name}. Subclasses: {', '.join(subs)}. Superclasses: {', '.join(supers)}."
        txt = textwrap.shorten(txt, width=120, placeholder="…")

        token_count = len(enc.encode(txt))
        if token_count <= max_tokens:
            summaries.append({"name": cls.name, "summary": txt, "tokens": token_count})
    return summaries

onto = owl.get_ontology("big_ontology.owl").load()
summaries = chunk_and_summarise(onto, max_tokens=80)
print(summaries[:5])
```

*Why it helps:*  
You can pre‑compute one‑sentence digests of **every** class. When the user asks about “`Vehicle`”, you simply grab that one digest (≈ 10–20 tokens) instead of the whole sub‑graph (~10 k tokens).  

---

## 2. Retrieval‑Augmented Generation (RAG) (Section §5)

1. **Encode every triple as a short sentence**  
   ```
   <subject> <predicate> <object>
   ```

2. **Store embeddings in FAISS**  
   ```python
   from sentence_transformers import SentenceTransformer
   import faiss, numpy as np

   model = SentenceTransformer("paraphrase-MiniLM-L6-v2")
   triples = [f"{s} {p} {o}" for s, p, o in onto.world.get_triples()]
   embeddings = model.encode(triples, convert_to_numpy=True)

   d = embeddings.shape[1]
   index = faiss.IndexFlatL2(d)
   index.add(embeddings)
   ```

3. **During query**  
   ```python
   def retrieve(query, k=5):
       q_vec = model.encode([query], convert_to_numpy=True)
       D, I = index.search(q_vec, k)
       return "\n".join([triples[i] for i in I[0]])

   prompt = f"""
   Relevant facts for the question:
   {retrieve("Which class inherits from Vehicle?")}

   Answer in one sentence:
   """
   answer = ollama.generate(model="gpt-oss-7b", prompt=prompt)["response"]
   print(answer)
   ```

*Why it helps:*  
The LLM sees only the *few* triples that are most semantically close to the user’s query. Even a 100 k‑triple ontology can be collapsed to a handful of sentences per prompt.

---

## 3. Incremental / Recursive Prompting (Section §6)

```python
from langchain import PromptTemplate, LLMChain
from langchain.llms import Ollama

llm = Ollama(model="gpt-oss-7b")

outline_template = PromptTemplate(
    input_variables=["topic"],
    template="Provide a concise 3‑point outline for explaining {topic} in the context of an ontology. No more than 30 tokens."
)

details_template = PromptTemplate(
    input_variables=["topic", "point"],
    template=(
        "Given the outline point '{point}' about {topic}, give a 2‑sentence detail "
        "including any relevant class names or relations. 30 tokens max."
    )
)

outline_chain = LLMChain(llm=llm, prompt=outline_template)
details_chain = LLMChain(llm=llm, prompt=details_template)

topic = "Vehicle"

outline = outline_chain.run(topic=topic)
print("Outline:\n", outline)

for point in outline.split(";"):
    detail = details_chain.run(topic=topic, point=point.strip())
    print(f"\nDetail for '{point}':\n", detail)
```

*Why it helps:*  
You let the LLM decide which *subset* of the ontology is worth exploring, and you never push more than the model’s window in any single call.

---

## 4. External Reasoner + LLM (Section §7)

```python
# Run a fast OWLDL reasoner (HermiT/Pellet via Java) or use owlready2’s built‑in reasoner
onto = owl.get_ontology("big_ontology.owl")
onto.save(format="rdf")          # for external reasoner

# Using rdflib + SPARQL
import rdflib
g = rdflib.Graph()
g.parse("big_ontology.owl", format="xml")

q = """
SELECT ?sub
WHERE { 
  ?sub rdfs:subClassOf <http://example.org#Vehicle> .
}
LIMIT 10
"""
res = g.query(q)
subclasses = [str(row[0]) for row in res]

prompt = f"Which classes are subclasses of Vehicle? {', '.join(subclasses)}"
answer = ollama.generate(model="gpt-oss-7b", prompt=prompt)["response"]
print(answer)
```

*Why it helps:*  
You outsource the heavy logical work to a reasoner. The LLM only sees the *deduced facts* (short list) and can provide natural‑language explanation.

---

## 5. Knowledge‑Graph Embedding (KGE) (Section §8)

```python
# Use pykeen for a quick KGE model
from pykeen.models import TransE
from pykeen.pipeline import pipeline

pipeline_result = pipeline(
    dataset="local",  # will auto‑detect triples file
    model="TransE",
    training_kwargs={"epochs": 100},
)

# Later, embed a query entity
from pykeen.triples import TripleFactory
factory = TripleFactory.from_path("big_ontology.owl")
embedding = pipeline_result.model.entity_embedding_factory.get_entity_embedding("Vehicle")

# Find nearest neighbours
import numpy as np
dist = np.linalg.norm(factory.entities - embedding, axis=1)
nearest = np.argsort(dist)[:5]
print("Most similar classes:", [factory.entities[i] for i in nearest])
```

*Why it helps:*  
You can retrieve *latent* relatedness without enumerating all triples. Feed the nearest few triples to the LLM.

---

## 6. Hybrid Graph‑Database + LLM (Section §9)

```python
from neo4j import GraphDatabase

driver = GraphDatabase.driver("bolt://localhost:7687", auth=("neo4j", "pass"))

def ask_db(query_text):
    with driver.session() as sess:
        res = sess.run(query_text)
        return [dict(row) for row in res]

# Example Cypher
cypher = """
MATCH (c:Class)-[:subClassOf]->(p:Class {name:'Vehicle'})
RETURN c.name AS child
"""

rows = ask_db(cypher)
children = [r["child"] for r in rows]
prompt = f"These classes inherit from Vehicle: {', '.join(children)}. Explain why."
answer = ollama.generate(model="gpt-oss-7b", prompt=prompt)["response"]
print(answer)
```

*Why it helps:*  
The graph database is a *dedicated* storage engine; the LLM only gets a concise, human‑readable slice of the answer.

---

## 7. Caching & Re‑use (Section §10)

```python
import diskcache as dc
cache = dc.Cache('llm_cache')

def cached_prompt(prompt):
    if prompt in cache:
        return cache[prompt]
    response = ollama.generate(model="gpt-oss-7b", prompt=prompt)["response"]
    cache[prompt] = response
    return response

# Re‑use for the same sub‑graph or same question
q = "What is the subclass hierarchy under Vehicle?"
ans = cached_prompt(q)
print(ans)
```

*Why it helps:*  
You avoid re‑generating the same LLM pass for repeated queries or for the same sub‑graph (useful in interactive GUIs).

---

## 8. Practical Checklist for Production

| Step | What to do | Tooling | Quick note |
|------|------------|---------|------------|
| 1 | **Profile** the ontology: size, depth, namespace counts. | `owlready2` introspection | Get an idea of chunk granularity |
| 2 | **Choose a representation** (triples, sub‑graphs, embeddings). | `owlready2`, `rdflib`, `sentence-transformers` | Pick the one that gives the best token‑to‑info ratio |
| 3 | **Pre‑compute** whatever you can: summaries, embeddings, SPARQL views. | `faiss`, `diskcache` | Do it offline or on first startup |
| 4 | **Keep prompts < 80 % of the model’s context window**. | `tiktoken` | Avoid truncation |
| 5 | **Batch retrieval** if you need multiple sub‑graphs. | `faiss` k‑NN | 1 query → several chunks |
| 6 | **Post‑process** LLM output: strip markdown, extract SPARQL, verify with `owlready2`. | regex / `rdflib` | Guard against hallucination |
| 7 | **Instrument**: log token counts, latency, cache hit‑rate. | `prometheus`, `loguru` | Detect performance bottlenecks |

---

## 9. Quick Start: Putting It All Together

```python
# 1. Load ontology
onto = owl.get_ontology("big_ontology.owl").load()

# 2. Build vector index (once)
from sentence_transformers import SentenceTransformer
import faiss, numpy as np

model = SentenceTransformer("paraphrase-MiniLM-L6-v2")
triples = [f"{s} {p} {o}" for s, p, o in onto.world.get_triples()]
embeds = model.encode(triples, convert_to_numpy=True)

d = embeds.shape[1]
idx = faiss.IndexFlatL2(d)
idx.add(embeds)

# 3. Query routine
def ask(question, k=5):
    qvec = model.encode([question], convert_to_numpy=True)
    D, I = idx.search(qvec, k)
    context = "\n".join([triples[i] for i in I[0]])
    prompt = f"""
    Context from the ontology:
    {context}

    Q: {question}
    A:
    """
    return ollama.generate(model="gpt-oss-7b", prompt=prompt)["response"]

print(ask("Which class inherits from Vehicle?"))
```

> **Result:** a short, context‑grounded answer that never exceeded 200 tokens, while the full ontology remained on‑disk and never entered the LLM’s window.

---

### Bottom line

* **Never feed the whole ontology** into the LLM.  
* **Chunk, summarise, or embed** until the context window is respected.  
* **Let the model decide which pieces to load** (recursive prompting) or let you decide (RAG).  
* **Validate** every LLM output against the ontology or an external reasoner.  

With these patterns you can build a robust, local LLM‑powered ontology assistant that scales to millions of triples while keeping inference latency low and privacy intact. Happy modelling!

### USER
What are the best practices for summarizing an ontology sub‑graph into a token‑limited prompt that still preserves key information?

### ASSISTANT
<details type="reasoning" done="true" duration="17">
<summary>Thought for 17 seconds</summary>
> Need to answer best practices for summarizing ontology sub-graph into token-limited prompt preserving key info. Provide guidelines: use token counting, hierarchy, use OWL features, use concise phrasing, use context tokens, include relevant triples, etc. Provide steps: identify scope, extract essential axioms, map to natural language, use short descriptors, use bullet points, use pre‑defined templates, use token counting, iterative refinement, use embeddings for relevance, keep entity names, include class hierarchy, include property restrictions, include individuals. Provide code snippets for token counting with tiktoken, summarization heuristics, use of owlready2. Provide best practices.
</details>
## 📌  How to turn a *large* ontology sub‑graph into a **token‑limited prompt** that still feels “complete”

| Goal | Why it matters | Practical trick |
|------|----------------|-----------------|
| **Keep the prompt < X tokens** | LLM context windows are hard‑coded (4 k, 8 k, 16 k, …).  Anything beyond that is silently truncated. | *Count* tokens **before** you build the prompt. |
| **Preserve the *meaning* of the sub‑graph** | A single sentence can hold the same information that a dozen triples carry. | **Select the most “informative” axioms** and phrase them in natural language. |
| **Make the LLM “understand” the ontology’s structure** | Models are better at reasoning when you give them a quick schema. | **Add a brief schema line** (e.g., “Classes are organised by `rdfs:subClassOf` hierarchy”). |
| **Leave room for the *question* or *task*** | You’ll usually append a question or instruction to the end. | Keep the summary ~ 30–40 % of the limit, leaving the rest for the prompt. |

Below is a step‑by‑step recipe that you can copy‑paste into a notebook or turn into a reusable function.

---

## 1. Decide the *scope* of the sub‑graph

| What to cut | How |
|-------------|-----|
| **Namespace** (e.g., all classes in `http://example.org#Vehicle`) | `onto.search(iri="http://example.org#Vehicle*")` |
| **Depth** (root → N levels) | BFS up to depth N |
| **Relation type** (only `rdfs:subClassOf`, `rdf:type`, or custom props) | Filter triples by predicate |
| **Entities only** (classes & individuals) | Omit data‑properties if not needed |

```python
def extract_subgraph(onto, root_class, depth=2, predicates=None):
    """
    Return a set of triples (subject, predicate, object) that belong to the
    subtree of `root_class` up to `depth` levels.
    """
    if predicates is None:
        predicates = {owl.onto.onto["rdfs:subClassOf"]}

    visited = set()
    frontier = [root_class]
    level = 0
    triples = []

    while frontier and level < depth:
        next_frontier = []
        for cls in frontier:
            visited.add(cls)
            for child in cls.subclasses():
                if child not in visited:
                    next_frontier.append(child)
                triples.append((cls, owl.onto.onto["rdfs:subClassOf"], child))
        frontier = next_frontier
        level += 1
    return triples
```

---

## 2. Pick the *most useful* axioms

| Axiom type | Why it’s useful | Typical token savings |
|------------|-----------------|-----------------------|
| `rdfs:subClassOf` | Reveals hierarchy. | 1–2 tokens per triple |
| `rdfs:label` | Human‑readable names. | 1–3 tokens |
| `rdfs:comment` | Quick definition. | 5–10 tokens |
| `owl:equivalentClass` | Important equivalence (e.g., `Car ≡ Vehicle ∧ hasWheel`). | 8–15 tokens |
| `owl:disjointWith` | Contradictions to avoid. | 3–5 tokens |
| `rdf:type` for individuals | Instance information. | 2–4 tokens |

**Rule of thumb:** keep *at most 5–7* distinct axiom types per class, and skip repetitive or low‑information ones.

```python
def keep_essential_axioms(triples, max_axioms_per_entity=5):
    from collections import defaultdict

    entity_axioms = defaultdict(list)
    for s, p, o in triples:
        entity_axioms[s].append((s, p, o))

    kept = []
    for ent, ax_list in entity_axioms.items():
        # sort by “importance” (predicate hierarchy)
        ax_list.sort(key=lambda t: {
            owl.onto.onto["rdfs:subClassOf"]: 1,
            owl.onto.onto["rdfs:label"]: 2,
            owl.onto.onto["rdfs:comment"]: 3,
            owl.onto.onto["owl:equivalentClass"]: 4,
            owl.onto.onto["owl:disjointWith"]: 5,
            owl.onto.onto["rdf:type"]: 6
        }[t[1]], reverse=True)
        kept.extend(ax_list[:max_axioms_per_entity])
    return kept
```

---

## 3. Turn axioms into **natural‑language fragments**

| Pattern | Example | Token count |
|---------|---------|-------------|
| `A rdfs:subClassOf B` | “*A* is a kind of *B*.” | 6–8 |
| `A rdfs:label "Foo"` | “Class *Foo*.” | 3–4 |
| `A rdfs:comment "Desc"` | “*A* represents *Desc*.” | 5–9 |
| `B owl:equivalentClass C` | “*B* is equivalent to *C*.” | 7–9 |
| `B owl:disjointWith C` | “*B* cannot be a *C*.” | 5–7 |

```python
def axiom_to_text(axiom):
    s, p, o = axiom
    if p == owl.onto.onto["rdfs:subClassOf"]:
        return f"{s.name} is a kind of {o.name}."
    if p == owl.onto.onto["rdfs:label"]:
        return f"{s.name} is called \"{o}\"."
    if p == owl.onto.onto["rdfs:comment"]:
        return f"{s.name} is {o}."
    if p == owl.onto.onto["owl:equivalentClass"]:
        return f"{s.name} is equivalent to {o.name}."
    if p == owl.onto.onto["owl:disjointWith"]:
        return f"{s.name} is disjoint with {o.name}."
    if p == owl.onto.onto["rdf:type"]:
        return f"{s.name} is an instance of {o.name}."
    return f"{s} {p} {o}"
```

---

## 4. Token‑count first, then format

### 4.1 Count tokens with the same tokenizer you’ll use for the LLM

```python
from tiktoken import get_encoding

enc = get_encoding("cl100k_base")   # GPT‑4 tokenizer (or the tokenizer that matches your model)

def count_tokens(s):
    return len(enc.encode(s))
```

### 4.2 Build the prompt incrementally

```python
def build_prompt(axioms, question, max_tokens=2000, schema_token_budget=200):
    """
    1. Add a tiny schema header.
    2. Append natural‑language fragments until the token limit is hit.
    3. Append the user question.
    """
    schema = ("This ontology uses `rdfs:subClassOf` for hierarchy, "
              "`rdf:type` for instances, and `owl:equivalentClass` for equivalences.")
    prompt_parts = [schema]
    used = count_tokens(schema)

    for ax in axioms:
        frag = axiom_to_text(ax)
        frag_token = count_tokens(frag)
        if used + frag_token + count_tokens(question) > max_tokens:
            break
        prompt_parts.append(frag)
        used += frag_token

    prompt_parts.append("Question: " + question)
    return "\n".join(prompt_parts)
```

---

## 5. Fine‑tune the **token budget**

| Component | Typical size | Where to cut |
|-----------|--------------|--------------|
| **Schema header** | 30–50 tokens | Shorten wording (“Classes use `subClassOf`”) |
| **Fragments** | 6–12 tokens each | Skip low‑information axioms, collapse long comments |
| **Question** | 5–30 tokens | Keep the user’s prompt short; the model can expand |

**Quick trick:** pre‑compute `count_tokens(frag)` for every fragment and sort by **importance** *minus* token cost, then pick the best subset that fits.

```python
def choose_fragments(fragments, budget):
    # fragments: list of (text, importance_score)
    fragments = sorted(fragments, key=lambda t: -t[1])  # higher score first
    chosen = []
    used = 0
    for txt, _ in fragments:
        tok = count_tokens(txt)
        if used + tok <= budget:
            chosen.append(txt)
            used += tok
    return chosen
```

---

## 6. Add a *schema cue* (optional but highly recommended)

```python
schema_cue = ("In this ontology: "
              "• `subClassOf` means “is a kind of”;\n"
              "• `equivalentClass` means “is identical to”;\n"
              "• `disjointWith` means “cannot share members”;\n"
              "• `type` is used for individuals.")
```

Putting it at the top gives the model a *mental map* before it sees the facts.

---

## 7. Example end‑to‑end

```python
# 1. Load ontology
onto = owl.get_ontology("big_ontology.owl").load()

# 2. Pick a sub‑graph
root = onto.search_one(iri="*Vehicle")          # whatever your root is
triples = extract_subgraph(onto, root, depth=2)
essential = keep_essential_axioms(triples)

# 3. Build prompt
question = "What classes are directly under Vehicle and what are their key properties?"
prompt = build_prompt(essential, question, max_tokens=1500)

print("Prompt tokens:", count_tokens(prompt))
print(prompt[:800])   # first 800 chars
```

---

## 🎯  Key Takeaways

| ✅ | What you should always do |
|----|----------------------------|
| 1 | **Token‑count before you build** the prompt. |
| 2 | **Keep the schema brief** (1–3 sentences). |
| 3 | **Prioritise axioms** that carry the most semantic weight. |
| 4 | **Translate triples to short natural‑language sentences** (≤ 12 tokens). |
| 5 | **Leave enough space for the question** or instruction. |
| 6 | **Iteratively refine** the summary until the model answers correctly. |
| 7 | **Cache the summary** for repeated queries on the same sub‑graph. |

When you follow these rules, you can compress **hundreds of triples** into a prompt that is still *grammatically coherent* and *ontologically complete* for the LLM’s eyes. The model then gets exactly the right amount of “context” to do its reasoning without being hit by the dreaded truncation bug. Happy summarising!
