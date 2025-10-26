## GPT-OSS Session

### USER
Propose programming methods for using ACT-R cognitive models with Llama type LLMs running on Ollama server.

### ASSISTANT
<details type="reasoning" done="true" duration="11">
<summary>Thought for 11 seconds</summary>
> The user asks: "Propose programming methods for using ACT-R cognitive models with Llama type LLMs running on Ollama server."
> 
> We need to propose methods to integrate ACT-R cognitive architecture with Llama models served via Ollama. Provide programming approaches, likely in Python, perhaps using PyACTR, ACT-R's Lisp interface, bridging via API calls to Ollama. Suggest ways to use ACT-R as cognitive controller, LLM as perception or language generation module, using prompts, embeddings, etc. Also discuss architecture, data flow, synchronization, state management, multi-threading, event-driven loops. Provide code snippets, examples, workflow. Possibly mention using the ACT-R Python port (PyACTR) and calling Ollama's REST API. Provide methods: wrapper functions, custom modules, using ACT-R's "production system" to trigger LLM calls, using LLM to generate text for ACT-R's visual or auditory modules. Also consider using LLM for knowledge retrieval, using ACT-R for procedural control. Outline steps: install Ollama, run Llama model, expose via HTTP; create Python interface; integrate with ACT-R model using Lisp or Python; use JSON messages.
> 
> Also discuss evaluation, logging, latency, concurrency, caching, token limits. Provide sample architecture diagrams, pseudocode.
> 
> We need to propose programming methods: direct embedding, asynchronous calls, microservices, using Python's asyncio, using external processes. Also mention possible use of "actr-py" library.
> 
> Thus answer will be detailed, structured: overview, architecture options, method 1: REST API bridging, method 2: embedding LLM via llama.cpp library with Python bindings, method 3: using LLM as external module within ACT-R Lisp environment via foreign function interface, method 4: hybrid approach with shared memory (Redis) for state exchange.
> 
> Provide example code for each. Also discuss handling ACT-R buffers, mapping to LLM prompts.
> 
> Conclude with recommendations, best practices, pitfalls.
> 
> Now produce final answer.
</details>
Below is a **practical, step‑by‑step guide** for wiring an **ACT‑R cognitive model** to a **Llama‑type LLM** that you run locally through the **Ollama server**.  
The focus is on **programming methods** (architectures, APIs, code patterns) rather than theory, so you can copy‑paste the snippets, adapt them to your own model, and start experimenting today.

---

## 1. High‑Level Architecture

| Component | Role | Typical Tech |
|-----------|------|--------------|
| **ACT‑R engine** | Symbolic procedural control, buffers, productions, learning | Original Lisp implementation, **PyACT‑R** (Python port), or **ACT‑R‑Python** bindings |
| **LLM service (Ollama)** | Natural‑language perception, generation, knowledge lookup | Ollama’s HTTP JSON API (`/api/generate`, `/api/chat`) |
| **Glue layer** | Translates ACT‑R buffer contents ↔ LLM prompts & responses; orchestrates async calls | Python (requests/urllib3, `httpx`), `asyncio`, or a lightweight RPC (ZeroMQ, Redis) |
| **State store (optional)** | Persist ACT‑R + LLM interaction history, cache results | Redis, SQLite, or plain JSON files |
| **Scheduler / Event Loop** | Guarantees that ACT‑R’s discrete‑time cycles and LLM latency coexist | `asyncio`, `trio`, or a custom “tick‑driven” loop in Lisp |

**Typical data flow**

1. **ACT‑R** updates its buffers (e.g., `visual`, `speech`, `goal`).
2. When a buffer reaches a **“needs‑LLM”** flag, the glue layer **forms a prompt** from the buffer contents.
3. The prompt is **sent to Ollama** (HTTP POST).  
   - For short, synchronous calls you can block.  
   - For realistic cognitive timing you can **schedule a future event** (e.g., 200 ms) that will fire when the response arrives.
4. The **LLM response** is parsed and **written back** to one or more ACT‑R buffers (e.g., `speech-output`, `retrieval`).
5. ACT‑R’s production system fires the next rule(s) based on the new buffer state.

---

## 2. Choosing a Programming Stack

| Preference | Recommendation |
|------------|----------------|
| **Stay in Lisp (original ACT‑R)** | Use the **`!` external command** feature to call a tiny Python/Node wrapper that talks to Ollama. |
| **Python‑centric development** | Use **PyACT‑R** (a pure‑Python re‑implementation) and keep everything in Python. |
| **Hybrid: best of both worlds** | Run ACT‑R in its native Lisp, expose a **local JSON‑RPC server** (Python) that the Lisp model can call via `open-http`. |

Below are three concrete **programming methods** that cover each scenario.

---

## 3. Method 1 – **Python‑only** (PyACT‑R + Ollama)

### 3.1 Install the ingredients

```bash
# 1️⃣ Install Ollama (Linux/macOS/Windows)
curl -fsSL https://ollama.com/install.sh | sh   # or use the .exe on Windows

# 2️⃣ Pull a Llama model (e.g., llama3)
ollama pull llama3

# 3️⃣ Start the server (defaults to http://localhost:11434)
ollama serve &

# 4️⃣ Install Python packages
pip install pyactr httpx tqdm
```

### 3.2 Minimal glue code

```python
# llm_client.py
import httpx
import json
from typing import Dict, Any

OLLAMA_URL = "http://localhost:11434/api/chat"   # chat endpoint works well for multi‑turn

async def generate(prompt: str,
                   model: str = "llama3",
                   temperature: float = 0.7,
                   max_tokens: int = 256) -> str:
    async with httpx.AsyncClient() as client:
        payload = {
            "model": model,
            "messages": [{"role": "user", "content": prompt}],
            "temperature": temperature,
            "max_tokens": max_tokens,
        }
        r = await client.post(OLLAMA_URL, json=payload, timeout=30.0)
        r.raise_for_status()
        result = r.json()
        # Ollama returns a list of messages; we want the first assistant reply
        return result["message"]["content"]
```

### 3.3 A simple ACT‑R model that asks the LLM for a definition

```python
# my_model.py
import asyncio
from pyactr import Model, Chunk, Buffer, Production
from llm_client import generate

class DefinitionModel(Model):
    def __init__(self):
        super().__init__(name="Definer")
        # Buffers
        self.goal = Buffer("goal")
        self.request = Buffer("request")
        self.response = Buffer("response")
        # Initial state
        self.goal.add(Chunk(type="start"))
        self.request.add(Chunk(type="idle"))

        # Production that triggers a LLM call
        @Production(self, 
                    name="AskLLM",
                    goal="start",
                    request="idle")
        def ask_llm(self):
            # 1️⃣ Grab the term to define from the goal buffer (hard‑coded for demo)
            term = "cognitive architecture"
            prompt = f"Give a concise (≤2‑sentence) definition of: {term}"
            # 2️⃣ Schedule the async LLM call
            asyncio.create_task(self.call_llm(prompt))
            # 3️⃣ Mark request as pending so we don't fire again
            self.request.add(Chunk(type="pending"))

        # Production that fires when response arrives
        @Production(self,
                    name="StoreResult",
                    response="filled")
        def store_result(self):
            # Print the answer, then finish
            print("LLM says:", self.response.contents[0].content)
            self.goal.clear()      # end simulation

    async def call_llm(self, prompt: str):
        answer = await generate(prompt)
        # Put answer into the response buffer (type = "filled")
        self.response.add(Chunk(type="filled", content=answer))

# Run the simulation
if __name__ == "__main__":
    model = DefinitionModel()
    # Run for a max of 10 seconds (or until goal buffer is empty)
    model.run(stop_when=lambda: not model.goal.contents, max_time=10)
```

#### What this shows

* **ACT‑R → LLM**: The `AskLLM` production builds a prompt and schedules an **async** call.
* **LLM → ACT‑R**: When the coroutine finishes, it **adds a chunk** to the `response` buffer. The `StoreResult` production fires in the next ACT‑R cycle.
* **Timing**: By using `asyncio.create_task`, you can inject an **artificial latency** (e.g., `await asyncio.sleep(0.2)`) to emulate cognitive processing time.

---

## 4. Method 2 – **Lisp ACT‑R + External Python Wrapper**  

If you are comfortable with the classic **ACT‑R Lisp** implementation, you can keep the model in its native environment and call a tiny HTTP client written in Python (or any language) whenever you need the LLM.

### 4.1 Set up the HTTP wrapper (Python)

```python
# ollama_wrapper.py
# A minimal Flask/Quart server that forwards requests to Ollama
# (You can also use FastAPI, Bottle, etc.)

from quart import Quart, request, jsonify
import httpx

app = Quart(__name__)

OLLAMA_CHAT = "http://localhost:11434/api/chat"

@app.post("/llm")
async def llm():
    data = await request.get_json()
    prompt = data["prompt"]
    async with httpx.AsyncClient() as client:
        resp = await client.post(
            OLLAMA_CHAT,
            json={"model": "llama3", "messages": [{"role": "user", "content": prompt}]},
        )
        resp.raise_for_status()
        answer = resp.json()["message"]["content"]
    return jsonify({"answer": answer})

if __name__ == "__main__":
    app.run(port=5001)  # runs on http://localhost:5001/llm
```

Run it once:

```bash
python ollama_wrapper.py
```

### 4.2 Lisp side: use `!` to invoke curl (or a library)  

```lisp
;; actr_llm.lisp
(defun call-llm (prompt)
  "Synchronous wrapper that returns the LLM answer as a string."
  (let* ((url "http://localhost:5001/llm")
         (json (format nil "{\"prompt\": ~S}" prompt))
         (cmd (format nil "curl -s -X POST -H \"Content-Type: application/json\" -d '~A' ~A"
                      json url)))
    (let ((response (run-shell-command cmd)))   ; `run-shell-command` is a simple ACT-R helper
      (let ((alist (json:decode-json-from-string response))) ; requires a JSON lib
        (getf alist :answer)))))

;; Production that asks the LLM
(pdefchunk request (type pending))
(pdefchunk response (type filled content nil))

(production ask-llm
  =goal> isa start
  =request> isa idle
  ==>
  ;; Build prompt from goal buffer or external variable
  (let ((term "working memory"))
    (let ((answer (call-llm (format nil "Define ~A in one sentence." term))))
      (pinsert =response> isa filled content answer)
      (pdelete =request> isa pending))))
```

**Key points**

* **Blocking call** – `call-llm` blocks the ACT‑R cycle until the response arrives. If you want a realistic *cognitive* delay, wrap the `curl` call in a *timer* production that fires after `N` ms.
* **No Python in the model** – The ACT‑R code stays pure Lisp; only the **wrapper service** needs Python.
* **Error handling** – You can check HTTP status codes and fallback to a “no‑answer” chunk.

---

## 5. Method 3 – **Shared Memory / Message Queue** (Redis or ZeroMQ)

When you need **high‑throughput, bidirectional** interaction (e.g., thousands of token‑level exchanges) it’s cleaner to decouple the two processes entirely.

### 5.1 Why use a broker?

* **Non‑blocking** – Both ACT‑R and the LLM can run at their own pace.
* **Persistence** – You can log every request/response automatically.
* **Scalability** – Swap Ollama for a remote GPU server without touching ACT‑R code.

### 5.2 Minimal Redis‑based glue (Python)

```python
# redis_bridge.py
import redis
import json
import httpx
import asyncio

r = redis.Redis(host="localhost", port=6379, decode_responses=True)

OLLAMA_CHAT = "http://localhost:11434/api/chat"

async def poll_requests():
    while True:
        # Blocking pop with timeout (5 s)
        request = r.blpop("actr:to_llm", timeout=5)
        if request:
            _, payload = request
            data = json.loads(payload)
            prompt = data["prompt"]
            # Call Ollama
            async with httpx.AsyncClient() as client:
                resp = await client.post(
                    OLLAMA_CHAT,
                    json={"model": "llama3",
                          "messages": [{"role":"user","content":prompt}]},
                )
                answer = resp.json()["message"]["content"]
            # Push answer back
            r.rpush("llm:to_actr", json.dumps({"answer": answer, "id": data["id"]}))
        await asyncio.sleep(0.01)   # tiny back‑off

if __name__ == "__main__":
    asyncio.run(poll_requests())
```

Run `redis-server` first, then start `redis_bridge.py`.

### 5.3 ACT‑R side (Lisp) – poll Redis every cycle

```lisp
(defun redis-get-answer (msg-id)
  (let* ((raw (run-shell-command
               (format nil "redis-cli BLPOP llm:to_actr 0")))
         (json (second (split-string raw)))   ; "llm:to_actr" "value"
         (alist (json:decode-json-from-string json)))
    (when (string= (getf alist :id) msg-id)
      (getf alist :answer))))

(defun ask-llm-redis (prompt)
  (let ((msg-id (format nil "~A" (random 1000000)))) ; simple unique id
    (run-shell-command
     (format nil "redis-cli RPUSH actr:to_llm '~A'"
             (json:encode-json-to-string `((:prompt ,prompt) (:id ,msg-id)))))
    ;; Simulate a cognitive delay (e.g., 200 ms) before checking response
    (schedule-event-relative 0.2
      (lambda ()
        (let ((answer (redis-get-answer msg-id)))
          (when answer
            (pinsert =response> isa filled content answer)))))))
```

**Advantages**

* **Fully asynchronous** – No need for `asyncio` inside ACT‑R; the Lisp side merely schedules a future check.
* **Multiple concurrent queries** – Each request carries its own `msg-id`.
* **Fault‑tolerant** – If the LLM crashes, the queue preserves the request for later retry.

---

## 6. Common Patterns & Helper Functions

Below are reusable building blocks you can copy into any of the three methods.

### 6.1 Prompt‑generation utilities (Python)

```python
def make_prompt(task: str, **kwargs) -> str:
    """Convenient central place to format prompts for different cognitive tasks."""
    if task == "definition":
        return f"Define the term '{kwargs['term']}' in one concise sentence."
    if task == "question_answer":
        return f"Q: {kwargs['question']}\nA:"
    if task == "dialogue":
        # simple 2‑turn chat format
        history = "\n".join(f"{s['role']}: {s['content']}" for s in kwargs["history"])
        return f"{history}\nassistant:"
    raise ValueError(f"unknown task {task}")
```

### 6.2 ACT‑R buffer helper (Lisp)

```lisp
(defun buffer-set (buf-name &rest slots)
  "Create or replace a chunk in BUF-NAME with the given slot=value pairs."
  (let ((chunk (apply #'make-chunk slots)))
    (pdelete =buf-name>)                ; clear existing
    (pinsert =buf-name> chunk)))        ; insert new
```

### 6.3 Simulating Cognitive Latency (Python)

```python
async def generate_with_latency(prompt, latency=0.25, **kwargs):
    await asyncio.sleep(latency)          # emulate 250 ms mental processing
    return await generate(prompt, **kwargs)
```

---

## 7. Putting It All Together – A Mini “Cognitive Chatbot”

Below is a **complete example** that mixes all the ideas: a **goal‑driven ACT‑R model** that asks the LLM for a fact, stores it in declarative memory, and later uses it to answer a user‑initiated query.

### 7.1 Python side (PyACT‑R + Ollama)

```python
# chatbot_model.py
import asyncio
from pyactr import Model, Chunk, Buffer, Production, DeclarativeMemory
from llm_client import generate

class CognitiveChatbot(Model):
    def __init__(self):
        super().__init__(name="ChatBot")
        # Buffers
        self.goal = Buffer("goal")
        self.request = Buffer("request")
        self.speech = Buffer("speech")
        self.retrieval = Buffer("retrieval")
        self.output = Buffer("output")

        # Declarative memory for facts
        self.dm = DeclarativeMemory(self)

        # -----------------------------------------------------------------
        # 1️⃣ Goal: acquire a fact
        self.goal.add(Chunk(type="acquire", fact="gravity"))
        self.request.add(Chunk(type="idle"))

        @Production(self, name="AskFact")
        def ask_fact(self):
            fact = self.goal.contents[0].fact
            prompt = f"What is {fact}? Give a one‑sentence answer."
            asyncio.create_task(self.llm_query(prompt, fact))
            self.request.add(Chunk(type="pending"))

        @Production(self, name="StoreFact")
        def store_fact(self):
            # Fired when the retrieval buffer receives a filled chunk
            retrieved = self.retrieval.contents[0]
            # Encode into declarative memory
            self.dm.add(Chunk(type="fact",
                              name=self.goal.contents[0].fact,
                              answer=retrieved.answer))
            # Reset buffers for next phase
            self.goal.clear()
            self.request.clear()
            self.retrieval.clear()
            # Move to answering phase
            self.goal.add(Chunk(type="answer", question="What is gravity?"))

        @Production(self, name="AnswerUser")
        def answer_user(self):
            # Look up the fact we just stored
            query = self.goal.contents[0].question.split()[-1].strip("?")
            self.dm.retrieve(chunk=Chunk(type="fact", name=query))
            # Retrieval will populate self.retrieval automatically

        @Production(self, name="SpeakAnswer")
        def speak_answer(self):
            ans = self.retrieval.contents[0].answer
            self.output.add(Chunk(type="speech", content=ans))
            print("BOT:", ans)  # side‑effect for us

        # -----------------------------------------------------------------
    async def llm_query(self, prompt: str, fact_name: str):
        answer = await generate(prompt)
        # Insert answer into the retrieval buffer for the next production
        self.retrieval.add(Chunk(type="filled", answer=answer))

if __name__ == "__main__":
    bot = CognitiveChatbot()
    bot.run(stop_when=lambda: not bot.goal.contents, max_time=15)
```

**What you see**

1. **Goal buffer** decides *what* to do (`acquire` vs `answer`).  
2. **Production `AskFact`** fires, sends a prompt to the LLM, and **marks the request pending**.  
3. The **async coroutine** `llm_query` writes the answer into the **retrieval buffer**.  
4. `StoreFact` stores the fact in **declarative memory** (a simple associative store).  
5. Later a **question** is placed in the goal, the model **retrieves** the fact, and `SpeakAnswer` outputs it.

You can now **extend** the model with more sophisticated goal structures, memory decay, or reinforcement learning—while the LLM remains the *knowledge source*.

---

## 8. Performance & Practical Tips

| Issue | Mitigation |
|-------|------------|
| **LLM latency (hundreds of ms)** | Add a `schedule-event-relative` delay that matches empirical reaction times (e.g., 300‑600 ms). |
| **Token‑limit overflow** | Truncate prompts, keep a rolling “chat history” of the last *N* turns, or store long context in declarative memory and only send short summaries. |
| **Rate‑limiting / GPU contention** | Cache answers (`dict` or Redis) keyed by prompt hash; reuse cached results for repeated queries. |
| **Thread safety** (when mixing Lisp & Python) | Keep all LLM calls **outside** the ACT‑R engine thread; use a queue or HTTP bridge that serializes requests. |
| **Debugging** | Log every buffer change and every HTTP request/response (timestamped). A simple CSV logger works well: `time,buffer,chunk`. |
| **Reproducibility** | Fix Ollama’s temperature (`0.0` for deterministic output) during model validation. |

---

## 9. Choosing the Right Method for Your Project

| Situation | Recommended method |
|-----------|--------------------|
| **Prototype in pure Python, want fast iteration** | **Method 1** – PyACT‑R + direct `httpx` calls |
| **Existing ACT‑R Lisp model, minimal code change** | **Method 2** – Lisp `!` → tiny Flask/Quart wrapper |
| **Production‑grade, many concurrent agents, need persistence** | **Method 3** – Redis/ZeroMQ broker (or any message queue) |
| **Need to swap LLM back‑ends (e.g., local llama.cpp, remote OpenAI)** | **Method 1** or **3** – keep LLM access in a single Python module, swap the endpoint only |
| **Want to simulate realistic cognitive timing** | Combine **any method** with `schedule-event-relative` and/or `asyncio.sleep` to insert controlled latencies |

---

## 10. Quick‑Start Checklist

1. **Install & start Ollama** (`ollama serve`).  
2. Pull a model (`ollama pull llama3`).  
3. Choose a glue method and set up the corresponding helper (Python script, Flask service, Redis bridge).  
4. Add a **prompt‑generation** function so you can reuse it across productions.  
5. In your ACT‑R model, **create a buffer** that will hold the LLM output (e.g., `llm-output`).  
6. Write a **production** that:
   * Detects a “need‑LLM” state,
   * Calls the glue (synchronously or via `asyncio.create_task`),
   * Marks the request as pending.
7. Write a **second production** that fires when the LLM output buffer is filled.  
8. (Optional) Store results in **declarative memory** for later retrieval.  
9. Run, watch the logs, tweak the **latency** and **temperature** until the behavior matches your experimental constraints.  

---

### TL;DR

* **Python‑centric**: `pyactr` + `httpx` → single‑process, easy to debug.  
* **Lisp‑centric**: Lisp `!` → tiny Flask wrapper → keep all classic ACT‑R code untouched.  
* **Scalable**: Redis/ZeroMQ broker → fully asynchronous, persistent, multi‑agent ready.

With any of the three patterns you can **seamlessly embed a state‑of‑the‑art Llama model** (via Ollama) into a **cognitively realistic ACT‑R simulation**—turning symbolic productions into language‑aware agents, enriching memory retrieval with up‑to‑date world knowledge, and opening the door to hybrid cognitive‑LLM research. Happy modeling!
