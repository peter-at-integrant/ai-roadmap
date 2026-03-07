# Goal 9 (LLM Fundamentals): Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What's the difference between an LLM and an AI agent?**

> An LLM is a model that takes text in and produces text out — it's stateless and completes one request at a time. An AI agent is a system built around an LLM that can take sequences of actions: it can call tools, read results, decide what to do next, and loop until a goal is achieved. The LLM is the reasoning engine inside the agent; the agent adds memory, tool use, and goal-directed behaviour on top of it.

---

**Q2. When would you use a local model (Ollama) instead of a cloud API? What are the trade-offs?**

> Use a local model when: data cannot leave your infrastructure (PII, proprietary source code), you need to avoid per-token cloud costs at scale, or you need to work offline. Trade-offs: local models are generally less capable than frontier cloud models (GPT-4, Claude), require GPU hardware to run at reasonable speed, and need to be updated manually. Cloud APIs are faster to integrate, always up to date, and offer much larger models — but cost money per token and send data externally.

---

**Q3. What is RAG? Give a concrete example of when you'd use it on this project.**

> RAG (Retrieval-Augmented Generation) is a pattern where you retrieve relevant documents from a knowledge base and include them in the prompt before the LLM generates its answer. This lets the model answer questions about content it wasn't trained on. Example for this project: storing all the architecture docs, ADRs, and coding standards as embeddings in a vector database, then when a developer asks "what pattern do we use for handling domain events?", the system retrieves the relevant ADR and architecture section and feeds it to the LLM — so the answer is grounded in the actual team docs, not generic knowledge.

---

**Q4. What is an embedding, and why does it enable semantic search?**

> An embedding is a vector (a list of numbers) that represents the semantic meaning of a piece of text. Texts with similar meaning produce vectors that are close together in vector space. Semantic search works by embedding the query, then finding stored embeddings that are closest to it (by cosine similarity). Unlike keyword search (which requires exact word matches), semantic search finds conceptually related content — so "how do we handle errors in the API?" can match a document titled "Exception handling standards" even with no shared keywords.
