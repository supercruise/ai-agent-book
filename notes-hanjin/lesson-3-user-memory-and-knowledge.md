# Level 2 (Part 1): User Memory and Knowledge Bases (Chapter 3 Masterclass)

---

## 1. The Hierarchical Structure of Memory (The 4 Layers)

To remember users across sessions and query massive domain knowledge, an Agent structures memory into **4 distinct layers**, moving from short-term context to long-term persistence:

```
┌──────────────────────────────────────────────────────────────────────────┐
│ 1. Working Memory (Scratchpad / Current Turn)                            │
│    - Intermediate reasoning steps (<think> tokens), tool outputs         │
├──────────────────────────────────────────────────────────────────────────┤
│ 2. Short-Term Memory (Session Trajectory)                                │
│    - Message history of the active conversation session                  │
├──────────────────────────────────────────────────────────────────────────┤
│ 3. Long-Term User Memory (Cross-Session Profile)                         │
│    - User preferences, personal entity cards, history across sessions    │
├──────────────────────────────────────────────────────────────────────────┤
│ 4. Domain Knowledge Base (External RAG System)                           │
│    - Enterprise manuals, policy docs, technical papers, product databases│
└──────────────────────────────────────────────────────────────────────────┘
```

* **Working Memory**: In-context reasoning and temporary calculations held in the active prompt trajectory.
* **Short-Term Memory**: The turn-by-turn chat history of the active session.
* **Long-Term User Profile**: Extracted preferences (e.g., *"User prefers concise Python code"*) stored permanently across sessions.
* **Domain Knowledge Base**: External corporate docs and databases retrieved via RAG.

> **Summary & Actionable Takeaway: Memory Hierarchy**
>
> * **Concept Definition**: Memory is divided into 4 tiers—Working Memory (scratchpad), Short-Term (session history), Long-Term (user profile), and Domain Knowledge (RAG)—to balance immediate reasoning with persistent cross-session knowledge.
> * **Practical Rule**: Never force all historical data into the short-term context window; extract persistent user facts into a long-term profile and query enterprise docs via RAG.

---

## 2. Four Storage Formats for User Memory

Long-term user memory isn't just unstructured text strings. The book identifies **4 storage formats**, each serving a different precision requirement:

| Format                             | Structure                              | Best Used For                              | Example                                                                  |
| :--------------------------------- | :------------------------------------- | :----------------------------------------- | :----------------------------------------------------------------------- |
| **1. Free-Form Text**        | Unstructured prose                     | General preferences, soft observations     | `"User mentioned they are learning PyTorch and prefer brief answers."` |
| **2. Structured JSON Cards** | Key-value schemas                      | Rigid entity facts, account settings       | `{"preferred_lang": "Python", "experience_level": "Intermediate"}`     |
| **3. Knowledge Graphs**      | Triples (`Subject-Predicate-Object`) | Complex relational networks                | `(User) --[owns]--> (Project Alpha) --[uses]--> (Docker)`              |
| **4. Executable Code**       | Python functions/scripts               | Complex procedural habits & business rules | `def calculate_discount(user): return user.tier * 0.10`                |

> **Summary & Actionable Takeaway: Memory Formats**
>
> * **Concept Definition**: Long-term memory uses four storage structures—Free-Form Text, Structured JSON Cards, Knowledge Graphs, and Executable Code—to store facts with varying degrees of precision.
> * **Practical Rule**: Use Structured JSON Cards for exact key-value facts and Executable Code for procedural rules, reserving Free-Form Text for qualitative user notes.

---

## 3. RAG Pipeline: Hybrid Search & Contextual Retrieval

Retrieval-Augmented Generation (RAG) connects the Agent's reasoning brain to enterprise knowledge bases.

```
Query ──┬──> Dense Search (Vector Similarity / Semantics) ──┐
        │                                                   ├──> RRF Hybrid Fusion ──> Reranker ──> Context
        └──> Sparse Search (BM25 / Keyword Exact-Match) ────┘
```

### 1. Hybrid Retrieval (Dense + Sparse)

* **Dense Retrieval (Vectors)**: Captures semantic meaning (e.g., matching `"automobile"` to `"car"`).
* **Sparse Retrieval (BM25)**: Captures exact keywords, proper nouns, and part numbers (e.g., searching for error code `ERR_404_NET`).
* **Reciprocal Rank Fusion (RRF)**: Combines dense and sparse search rankings to deliver high-precision results.

### 2. Contextual Retrieval

Standard document chunking splits text into 300-word blocks. However, a chunk like *"The revenue grew 15% in Q2"* loses its parent context (which company? which year?).

**Contextual Retrieval** fixes this: before embedding, an LLM generates a 1–2 sentence context header summarizing the document's origin, which is prepended to the chunk:
`"[Context: ACME Corp 2025 Annual Report > Financial Highlights] The revenue grew 15% in Q2."`

> **Summary & Actionable Takeaway: Modern RAG Pipelines**
>
> * **Concept Definition**: Hybrid Search combines dense vectors (semantics) with BM25 (exact keywords), while Contextual Retrieval prepends document metadata to chunks before embedding to prevent semantic loss.
> * **Practical Rule**: Always use Hybrid Retrieval + Reranking for technical domains, and prepend document context to chunks during indexing to eliminate orphaned facts.

---

## 4. Passive RAG vs. Agentic RAG

Traditional systems use **Passive RAG**, while autonomous agents use **Agentic RAG**:

```
PASSIVE RAG:  User Query ──> [Hardcoded Retriever] ──> Inject Context ──> LLM Response
                                                                       
AGENTIC RAG:  User Query ──> [Agent Reasoning] ──> [Tool: search_kb("...")] ──> Observe ──> Decide Next Step
```

* **Passive RAG**: A rigid pre-processing pipeline automatically searches the database before the LLM ever sees the query. If the query is multi-step or ambiguous, the search often retrieves wrong chunks.
* **Agentic RAG**: The Agent treats the knowledge base as a **Tool** (`search_knowledge_base`). The Agent can re-phrase queries, perform multi-hop searches, inspect results, and decide when it has retrieved enough evidence.

> **Summary & Actionable Takeaway: Agentic RAG**
>
> * **Concept Definition**: Agentic RAG transforms retrieval from a fixed pre-processing step into an autonomous tool call controlled by the LLM's ReAct loop.
> * **Practical Rule**: Expose knowledge base search as a dynamic tool rather than a rigid pre-step, allowing the Agent to re-query, synthesize, and verify search results iteratively.

---

## 5. Memory Maintenance: Proposer-Reviewer Verification

If an Agent updates its long-term user memory without validation, noisy conversation artifacts or prompt injection attacks can corrupt the user profile.

Production memory systems use the **Proposer-Reviewer Pattern**:

1. **Proposer**: Analyzes the session trajectory and proposes candidate memory updates (e.g., `"Update preferred language to Rust"`).
2. **Reviewer**: A separate LLM prompt or validation rule checks the proposal against existing memory cards to detect contradictions, hallucinated updates, or security violations.
3. **Consolidation**: Deduplicates and merges validated memory cards into persistent storage.

> **Summary & Actionable Takeaway: Memory Maintenance**
>
> * **Concept Definition**: The Proposer-Reviewer pattern uses a two-stage verification loop to prevent unverified facts, contradictions, or malicious inputs from corrupting long-term memory.
> * **Practical Rule**: Never write session observations directly to persistent memory; run candidate updates through a dedicated Reviewer guard prompt first to consolidate and deduplicate facts.
