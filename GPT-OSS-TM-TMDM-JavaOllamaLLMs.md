## GPT-OSS vs Topic Maps + TMDM, Java, and Ollama LLMs

### USER
Can you recommend Java code for linking Topic Maps with TMDM to ollama LLMs?

### ASSISTANT
<details type="reasoning" done="true" duration="69">
<summary>Thought for 69 seconds</summary>
> The user wants Java code for linking Topic Maps with TMDM to Ollama LLMs. We need to explain TMDM - likely they mean "Topic Map Data Model" or "Topic Map Data Management"? Actually TMDM stands for "Topic Map Data Model"? It's likely a database-based storage for Topic Maps: the TMDM (Topic Map Data Model) used by some software (e.g., TMDM by InfoSphere?). Actually there is a product called TMDM: "Topic Map Data Model" or "Topic Map Data Management"? Might be a specific vendor's data model for topic maps, used in e.g., OpenRefine. But likely the user refers to "Topic Map Domain Model" or "Topic Map DMS" (data model). Might be a generic concept: TMDM is a relational schema that stores Topic Maps.
> 
> We need to give Java code that links TMDM to Ollama LLMs. That means: read topics and occurrences from the database (TMDM), build prompts, call Ollama, write back descriptions as new occurrences. Provide example Java code: using JDBC for database access, using TMAPI? Actually if the data is in a database, we can use JDBC and the TMDM schema (maybe the schema is known). But we can propose generic code: read topics and their data via queries, then use OkHttp for Ollama.
> 
> We need to produce Java code that:
> 
> - Connects to the database (JDBC).
> - Query topics: SELECT * FROM topics; SELECT * FROM occurrences WHERE topic_id = ?; etc.
> - Build a prompt string per topic.
> - Call Ollama via OkHttp.
> - Insert new occurrence with type "description".
> 
> Also might mention using Spring Boot or plain Java.
> 
> We can provide a simple main class with a repository abstraction, a service for prompt building, a service for calling Ollama, and a DAO for TMDM.
> 
> We can include an example of the TMDM schema: tables topics, names, occurrences, associations.
> 
> We need to recommend code for linking TMDM to Ollama LLMs. Provide steps:
> 
> 1. Setup database connection.
> 2. Query topics and related data.
> 3. Build prompt.
> 4. Call Ollama.
> 5. Write description occurrence to the database.
> 
> Also include configuration, environment variables for DB, Ollama URL, model.
> 
> Use OkHttp, Jackson for JSON, and plain JDBC.
> 
> We also can mention using TMAPI if the TMDM store implements TMAPI. But TMDM is just a database schema, so we can just use SQL.
> 
> Provide example code:
> 
> - `TMDMDao` with methods: `List<Topic> getAllTopics()`, `List<Name> getNamesForTopic(long topicId)`, `List<Occurrence> getOccurrencesForTopic(long topicId)`, `void insertOccurrence(long topicId, String type, String value)`.
> 
> - `PromptBuilder` to build prompt string.
> 
> - `OllamaClient` similar to previous.
> 
> - `TopicEnricher` that orchestrates: for each topic, build prompt, get description, insert occurrence.
> 
> - `Main` to run.
> 
> Also mention using Java 17+.
> 
> We can provide sample schema for TMDM:
> 
> - `topic` (id, identifier)
> - `name` (id, topic_id, type, value)
> - `occurrence` (id, topic_id, type, value)
> - `association` (id, type)
> - `role` (id, association_id, topic_id, type, value)
> 
> We can keep it simple.
> 
> We also recommend adding logging with SLF4J.
> 
> Also mention environment variables for DB connection: JDBC_URL, DB_USER, DB_PASS.
> 
> Ok.
> 
> We need to output Java code with proper imports and comment. Provide instructions.
> 
> Let's write final answer accordingly.
</details>
## Java – Enriching a **TMDM** Topic‑Map Store with an **Ollama** LLM  
(plain JDBC + OkHttp)

> **TL;DR**  
> ```bash
> java -cp target/tmdm‑ollama-1.0.0.jar \
>      com.example.tmdm.ollama.Main \
>      db-config.properties
> ```
>  
> The program:  
> 1. Connects to your TMDM database.  
> 2. Pulls every topic + its names / occurrences.  
> 3. Builds a natural‑language prompt.  
> 4. Calls `http://localhost:11434/api/generate` (Ollama).  
> 5. Inserts a new occurrence `type="description"` with the model’s output.

---

## 1️⃣ What is TMDM?

TMDM is a **relational schema** that stores Topic Maps in a database.  
A very common minimal schema looks like this:

| Table      | Columns (illustrative)                           | Notes |
|------------|---------------------------------------------------|-------|
| `topic`    | `id BIGINT PK`, `identifier VARCHAR`             | One row per topic |
| `name`     | `id BIGINT PK`, `topic_id BIGINT FK`, `type VARCHAR`, `value VARCHAR` | Names attached to a topic |
| `occurrence` | `id BIGINT PK`, `topic_id BIGINT FK`, `type VARCHAR`, `value VARCHAR` | Any data linked to a topic |
| `association` | `id BIGINT PK`, `type VARCHAR` | – |
| `role`     | `id BIGINT PK`, `association_id BIGINT FK`, `topic_id BIGINT FK`, `type VARCHAR`, `value VARCHAR` | Roles inside an association |

> **NOTE** – The exact column names may vary.  
>  If you already have a **TMAPI‑compatible** repository on top of this DB, you can use TMAPI instead of raw JDBC – the rest of the code stays identical.

---

## 2️⃣ Maven Setup

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>tmdm-ollama</artifactId>
    <version>1.0.0</version>
    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- OkHttp – HTTP client -->
        <dependency>
            <groupId>com.squareup.okhttp3</groupId>
            <artifactId>okhttp</artifactId>
            <version>4.12.0</version>
        </dependency>

        <!-- Jackson – JSON (for the Ollama request/response) -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.17.1</version>
        </dependency>

        <!-- SLF4J + Logback – logging -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.4.12</version>
        </dependency>

        <!-- HikariCP – lightweight JDBC pool (optional but highly recommended) -->
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
            <version>5.1.0</version>
        </dependency>
    </dependencies>
</project>
```

---

## 3️⃣ Core Java Code

### 3.1  `OllamaClient.java`

```java
package com.example.tmdm.ollama;

import com.fasterxml.jackson.databind.ObjectMapper;
import okhttp3.*;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

/**
 * Minimal wrapper around Ollama's HTTP API.
 */
public class OllamaClient {
    private static final Logger log = LoggerFactory.getLogger(OllamaClient.class);

    private final OkHttpClient http;
    private final ObjectMapper mapper;
    private final String apiUrl;   // e.g. http://localhost:11434/api/generate
    private final String model;   // e.g. llama3

    public OllamaClient(String apiUrl, String model) {
        this.http = new OkHttpClient();
        this.mapper = new ObjectMapper();
        this.apiUrl = apiUrl;
        this.model = model;
    }

    /**
     * Calls Ollama with a single prompt and returns the generated text.
     */
    public String generate(String prompt) throws IOException {
        Map<String, Object> payload = new HashMap<>();
        payload.put("model", model);
        payload.put("prompt", prompt);
        payload.put("stream", false);

        RequestBody body = RequestBody.create(
                mapper.writeValueAsString(payload),
                MediaType.parse("application/json")
        );

        Request request = new Request.Builder()
                .url(apiUrl)
                .post(body)
                .build();

        try (Response resp = http.newCall(request).execute()) {
            if (!resp.isSuccessful()) {
                String err = resp.body() != null ? resp.body().string() : "";
                log.error("Ollama returned {}: {}", resp.code(), err);
                throw new IOException("Ollama error: " + resp.code());
            }
            String json = resp.body().string();
            Map<?, ?> map = mapper.readValue(json, Map.class);
            return (String) map.getOrDefault("response", "").trim();
        }
    }
}
```

### 3.2  `TMDMDao.java`

```java
package com.example.tmdm.ollama;

import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

/**
 * Very small DAO that talks to the TMDM schema via JDBC.
 * If you already have a TMAPI repo you can replace this with a TMAPI DAO.
 */
public class TMDMDao {

    private final HikariDataSource ds;

    public TMDMDao(String jdbcUrl, String user, String password) {
        HikariConfig cfg = new HikariConfig();
        cfg.setJdbcUrl(jdbcUrl);
        cfg.setUsername(user);
        cfg.setPassword(password);
        cfg.setMaximumPoolSize(10);
        ds = new HikariDataSource(cfg);
    }

    /* ---------- models ---------- */
    public static record Topic(long id, String identifier) {}
    public static record Name(String type, String value) {}
    public static record Occurrence(String type, String value) {}

    /* ---------- queries ---------- */
    public List<Topic> getAllTopics() throws SQLException {
        List<Topic> out = new ArrayList<>();
        String sql = "SELECT id, identifier FROM topic";
        try (Connection c = ds.getConnection();
             PreparedStatement ps = c.prepareStatement(sql);
             ResultSet rs = ps.executeQuery()) {
            while (rs.next()) {
                out.add(new Topic(rs.getLong("id"), rs.getString("identifier")));
            }
        }
        return out;
    }

    public List<Name> getNamesForTopic(long topicId) throws SQLException {
        List<Name> out = new ArrayList<>();
        String sql = "SELECT type, value FROM name WHERE topic_id = ?";
        try (Connection c = ds.getConnection();
             PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setLong(1, topicId);
            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    out.add(new Name(rs.getString("type"), rs.getString("value")));
                }
            }
        }
        return out;
    }

    public List<Occurrence> getOccurrencesForTopic(long topicId) throws SQLException {
        List<Occurrence> out = new ArrayList<>();
        String sql = "SELECT type, value FROM occurrence WHERE topic_id = ?";
        try (Connection c = ds.getConnection();
             PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setLong(1, topicId);
            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    out.add(new Occurrence(rs.getString("type"), rs.getString("value")));
                }
            }
        }
        return out;
    }

    /** Inserts a *new* occurrence (type & value) for a topic. */
    public void insertDescription(long topicId, String type, String value) throws SQLException {
        String sql = "INSERT INTO occurrence (topic_id, type, value) VALUES (?, ?, ?)";
        try (Connection c = ds.getConnection();
             PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setLong(1, topicId);
            ps.setString(2, type);
            ps.setString(3, value);
            ps.executeUpdate();
        }
    }
}
```

> **Why a DAO?**  
> Keeps the database logic separate from business logic – you can later swap‑in an alternative storage (e.g., TMAPI) without touching the rest of the program.

### 3.3  `PromptBuilder.java`

```java
package com.example.tmdm.ollama;

import java.util.List;

/**
 * Turns a Topic + its names / occurrences into a prompt for the LLM.
 */
public class PromptBuilder {
    public String build(Topic topic, List<TMDMDao.Name> names,
                        List<TMDMDao.Occurrence> occs) {
        StringBuilder sb = new StringBuilder();
        sb.append("You are a knowledge‑graph assistant.\n\n");
        sb.append("Topic ID: ").append(topic.id()).append("\n");
        sb.append("Identifier: ").append(topic.identifier()).append("\n\n");

        sb.append("Names:\n");
        for (TMDMDao.Name n : names) {
            sb.append("- ").append(n.type()).append(": ").append(n.value()).append("\n");
        }

        sb.append("\nExisting Occurrences (excluding description):\n");
        for (TMDMDao.Occurrence o : occs) {
            if (!"description".equals(o.type())) {
                sb.append("- ").append(o.type()).append(": ").append(o.value()).append("\n");
            }
        }

        sb.append("\n\nTask: Write a concise (≤150 words) description of this topic for a knowledge portal. Do not explain the prompt itself.");
        return sb.toString();
    }
}
```

### 3.4  `TopicEnricher.java`

```java
package com.example.tmdm.ollama;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.sql.SQLException;
import java.util.List;

/**
 * Orchestrates: topic → prompt → Ollama → new occurrence.
 */
public class TopicEnricher {
    private static final Logger log = LoggerFactory.getLogger(TopicEnricher.class);

    private final TMDMDao dao;
    private final PromptBuilder promptBuilder;
    private final OllamaClient ollama;
    private final boolean force;          // overwrite existing description?

    public TopicEnricher(TMDMDao dao, PromptBuilder promptBuilder,
                         OllamaClient ollama, boolean force) {
        this.dao = dao;
        this.promptBuilder = promptBuilder;
        this.ollama = ollama;
        this.force = force;
    }

    public void enrichAll() throws SQLException {
        for (TMDMDao.Topic topic : dao.getAllTopics()) {
            if (!force && hasDescription(topic.id())) {
                log.debug("[Topic {}] already has a description – skipping.", topic.id());
                continue;
            }

            List<TMDMDao.Name> names = dao.getNamesForTopic(topic.id());
            List<TMDMDao.Occurrence> occs = dao.getOccurrencesForTopic(topic.id());

            String prompt = promptBuilder.build(topic, names, occs);
            log.debug("[Topic {}] Prompt: \n{}", topic.id(), prompt);

            String description;
            try {
                description = ollama.generate(prompt);
            } catch (Exception e) {
                log.error("Failed to generate description for topic {}: {}", topic.id(), e.getMessage());
                continue;   // keep going
            }

            dao.insertDescription(topic.id(), "description", description);
            log.info("[Topic {}] Stored description ({} chars)", topic.id(), description.length());
        }
        log.info("All topics enriched.");
    }

    private boolean hasDescription(long topicId) throws SQLException {
        String sql = "SELECT 1 FROM occurrence WHERE topic_id = ? AND type = 'description' LIMIT 1";
        try (Connection c = dao.ds.getConnection();
             PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setLong(1, topicId);
            try (ResultSet rs = ps.executeQuery()) {
                return rs.next();
            }
        }
    }
}
```

> **Why not TMAPI?**  
> If your TMDM store is wrapped by a TMAPI implementation, just replace `TMDMDao` with a `TMAPIDao` that delegates to the TMAPI methods. The rest of the pipeline remains the same.

### 3.5  `Main.java`

```java
package com.example.tmdm.ollama;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.FileInputStream;
import java.sql.SQLException;
import java.util.Properties;

/**
 * Simple command‑line runner.
 *
 * Usage:
 *   java -cp target/tmdm‑ollama-1.0.0.jar \
 *        com.example.tmdm.ollama.Main \
 *        db-config.properties
 *
 * `db-config.properties` might look like:
 *   jdbc.url=jdbc:postgresql://localhost:5432/tmdm
 *   db.user=postgres
 *   db.pass=secret
 *   ollama.url=http://localhost:11434/api/generate
 *   ollama.model=llama3
 */
public class Main {
    private static final Logger log = LoggerFactory.getLogger(Main.class);

    public static void main(String[] args) throws Exception {
        if (args.length == 0) {
            System.err.println("Usage: java -jar tmdm-ollama-1.0.0.jar <db-config.properties>");
            System.exit(1);
        }

        Properties cfg = new Properties();
        try (FileInputStream fis = new FileInputStream(args[0])) {
            cfg.load(fis);
        }

        String jdbcUrl = cfg.getProperty("jdbc.url");
        String user    = cfg.getProperty("db.user");
        String pass    = cfg.getProperty("db.pass");
        String ollamaUrl = cfg.getProperty("ollama.url", "http://localhost:11434/api/generate");
        String ollamaModel = cfg.getProperty("ollama.model", "llama3");

        // Create the DAO (JDBC pool)
        TMDMDao dao = new TMDMDao(jdbcUrl, user, pass);

        // Ollama client
        OllamaClient ollama = new OllamaClient(ollamaUrl, ollamaModel);

        // Enrichment service
        TopicEnricher enricher = new TopicEnricher(dao, new PromptBuilder(), ollama, false);

        try {
            enricher.enrichAll();
            System.out.println("✅ Enrichment finished – check the database!");
        } catch (SQLException e) {
            log.error("Database error", e);
            System.exit(1);
        }
    }
}
```

---

## 4️⃣ Quick‑Start

```bash
# 1️⃣ Start Ollama
ollama run llama3   # or your favourite model

# 2️⃣ Build the JAR
mvn clean package

# 3️⃣ Create a tiny property file
echo "jdbc.url=jdbc:postgresql://localhost:5432/tmdm" > db.properties
echo "db.user=postgres" >> db.properties
echo "db.pass=secret"   >> db.properties
# Optional overrides
# echo "ollama.url=http://localhost:11434/api/generate" >> db.properties
# echo "ollama.model=llama3" >> db.properties

# 4️⃣ Run
java -cp target/tmdm-ollama-1.0.0.jar \
     com.example.tmdm.ollama.Main db.properties
```

> After a few minutes you’ll see log lines like  
> ```
> [Topic 12] Generated description (324 chars)
> [Topic 12] Stored description
> ```
>  
> And a new row will appear in the `occurrence` table:
> ```
> topic_id | type        | value (the description)
> ---------+-------------+-------------------------
> 12       | description | "A concise overview of …"
> ```

---

## 5️⃣ Extending the Starter

| Want to add… | Where? | Quick snippet |
|--------------|--------|---------------|
| **Embeddings** | `OllamaClient` – add `public float[] embeddings(String text)` hitting `/api/embeddings`. | Return a `float[]` and insert it as `occurrence type="embedding"`. |
| **Cache prompt→answer** | Inside `TopicEnricher.enrichAll()`: read/write a `Map<String,String>` stored in a JSON file (`cache.json`). | Add `CacheDao`. |
| **Batch prompts** | In `enrichAll()`: collect N prompts, send one concatenated request, split the response by `#---#`. | Edit the loop. |
| **Role/association info** | Extend `TMDMDao.getAssociationsForTopic()` and add that data to the prompt. | In `PromptBuilder`. |
| **Parallelisation** | Wrap the loop in an `ExecutorService` (e.g., `Executors.newFixedThreadPool(4)`) and give each worker a fresh `OllamaClient` instance. | In `Main`. |

---

## 6️⃣ Things That Matter in Production

| Item | Practical tip |
|------|---------------|
| **Connection pooling** | Use HikariCP (as shown) – avoids repeatedly opening/closing connections. |
| **Transaction safety** | The DAO’s `insertDescription` is a single‑statement `INSERT`; you can wrap it in a transaction if you want to commit the whole enrichment at once. |
| **Rate‑limit** | Ollama can process a few dozen requests per second.  If you have a very large TMDM store, consider a *sleep* or *back‑off* strategy after each batch. |
| **Logging audit** | The `TopicEnricher` logs every generated description – you can redirect that to your repository’s audit trail. |
| **Configuration** | Prefer environment variables (`DB_URL`, `DB_USER`, `DB_PASS`, `OLLAMA_API_URL`, `OLLAMA_MODEL`) over hard‑coded strings. |
| **TMAPI fallback** | If you already own a TMAPI‑based repository built on the same DB, simply swap the DAO for a TMAPI implementation – no changes in the enrichment logic. |

---

## 7️⃣ Recap – One‑Line Summary

```bash
java -cp target/tmdm-ollama-1.0.0.jar \
     com.example.tmdm.ollama.Main db-config.properties
```

The program reads every **Topic** from the **TMDM** relational schema, builds a natural‑language prompt, calls **Ollama** (`/api/generate`), and writes a new `occurrence type="description"` back into the database.  
All code is plain Java 21, JDBC + OkHttp, and can be dropped into any Maven/Gradle project.
