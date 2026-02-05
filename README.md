# RAG with Knowledge Graph using Neo4j

A **production-safe Retrieval-Augmented Generation (RAG) system** that combines **Neo4j knowledge graphs**, **hybrid vector search**, and **LLM-based entity reasoning** to answer questions accurately and concisely — without token overflows or hallucinations.

This project demonstrates how to build an **end-to-end RAG pipeline** that scales beyond naive vector search by grounding answers in both **structured graph relationships** and **unstructured text**.

---

## ✨ Key Features

* **Hybrid Retrieval**

  * Combines **Neo4j graph traversal** (structured facts)
  * With **vector similarity search** (semantic context)

* **LLM-Driven Knowledge Graph Construction**

  * Automatically extracts entities and relationships from Wikipedia
  * Uses `LLMGraphTransformer` to populate Neo4j

* **Entity-Aware Question Answering**

  * Named entity extraction guides graph queries
  * Prevents irrelevant or excessive retrieval

* **Token-Safe by Design**

  * Hard caps on:

    * Graph traversal depth
    * Vector document size
    * LLM output tokens
  * Prevents `LengthFinishReasonError` and parser crashes

* **Conversation-Aware QA**

  * Follow-up questions are rewritten into standalone queries
  * Maintains context without bloating prompts

---

## 🏗️ Architecture Overview

```
User Question
     │
     ▼
Entity Extraction (LLM)
     │
     ├──► Neo4j Full-Text Search
     │        └─ Structured triples (facts & relations)
     │
     ├──► Neo4j Vector Search
     │        └─ Relevant document chunks
     │
     ▼
Context Fusion (Size-Limited)
     │
     ▼
LLM Answer Generation
     │
     ▼
Concise, Grounded Answer
```

---

## 🧠 Why Knowledge Graph + RAG?

Traditional RAG systems rely purely on vector similarity, which can:

* Miss explicit relationships
* Retrieve overly long or irrelevant text
* Hallucinate facts

By introducing a **knowledge graph**:

* Relationships are explicit and queryable
* Answers are grounded in facts, not just embeddings
* Retrieval is explainable and controllable

---

## 🚀 Getting Started

### 1. Prerequisites

* Python 3.9+
* Neo4j AuraDB or self-hosted Neo4j
* OpenRouter API key (or compatible OpenAI API)

---

### 2. Install Dependencies

```bash
pip install langchain langchain-community langchain-experimental \
            neo4j wikipedia tiktoken yfiles_jupyter_graphs
```

---

### 3. Configure Environment Variables

```bash
export OPENROUTER_API_KEY=your_key_here
export NEO4J_URI=neo4j+s://<your-db>.databases.neo4j.io
export NEO4J_USERNAME=neo4j
export NEO4J_PASSWORD=your_password
```

(Colab users can store these via `google.colab.userdata`.)

---

### 4. Run the Notebook / Cell

The entire pipeline — ingestion, graph construction, indexing, and QA — runs **end-to-end in a single cell**.

Example query:

```python
chain.invoke({"question": "Which house did Elizabeth I belong to?"})
```

Output:

```
House of Tudor
```

---

## 🧪 Example Follow-Up Question

```python
chain.invoke(
    {
        "question": "When was she born?",
        "chat_history": [
            ("Which house did Elizabeth I belong to?", "House of Tudor")
        ],
    }
)
```

Output:

```
She was born on 7 September 1533.
```

---

## 🔒 Token Safety & Stability

This project intentionally avoids common LLM failure modes:

| Risk             | Mitigation                  |
| ---------------- | --------------------------- |
| Context overflow | Hard caps on retrieved text |
| Parser crashes   | Reduced `max_tokens`        |
| Graph explosion  | Limited traversal depth     |
| Hallucinations   | Graph-grounded facts        |
| Latency          | Minimal retrieval per query |

---

## 🛠️ Technologies Used

* **LangChain**
* **Neo4j** (Graph DB + Vector Index)
* **OpenRouter / Mixtral-8x7B**
* **Wikipedia API**
* **Pydantic**
* **Cypher**

---

## 📌 Use Cases

* Knowledge-grounded chatbots
* Enterprise document QA
* Historical / factual assistants
* Explainable AI systems
* RAG research & prototyping

---

## 📈 Possible Extensions

* Automatic routing between “factual” vs “deep reasoning” queries
* Token usage monitoring & adaptive truncation
* Multi-hop graph reasoning
* Document ingestion beyond Wikipedia
* Answer citation tracing via graph paths

---

## 📄 License

MIT License

---

## 🙌 Acknowledgements

Inspired by modern RAG research and Neo4j’s graph-native approach to knowledge representation.
