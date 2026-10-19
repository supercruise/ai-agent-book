# Level 1 (Part 2): Context Engineering & Masterclass Reference (v2)

This document serves as the complete, structured masterclass reference for **Chapter 2: Context Engineering**, covering message list architecture, KV Cache mechanics, Agent Skills, prompt injection defense, and context compression strategies.

---

## 1. The API Message Structure & State Rebuilding

### Concept Breakdown

Because LLM APIs are stateless, the Agent framework must reconstruct the entire working context $c_t$ on every single turn:

$$
c_t = (\text{System Prompt}, \text{Tool Schemas}, u_1, a_1, o_1, \dots, u_t)
$$

The Chat Completions API standardizes context into four message roles plus a top-level `tools` definition array:

| Role                    | Primary Purpose                                                             | Source / Origin | KV Cache Behavior                            |
| :---------------------- | :-------------------------------------------------------------------------- | :-------------- | :------------------------------------------- |
| **`system`**    | Developer instructions, identity, safety rules, and core SOPs               | Developer       | **Static Prefix** (Must remain frozen) |
| **`user`**      | End-user input or injected framework status updates                         | User / Harness  | Dynamic trajectory                           |
| **`assistant`** | Past model outputs (reasoning `<think>`, text answers, or `tool_calls`) | LLM             | Dynamic trajectory                           |
| **`tool`**      | Execution results returned by external tools (linked via `tool_call_id`)  | Harness / APIs  | Dynamic trajectory                           |

> **Summary & Actionable Takeaway**
>
> * **Concept**: The Agent API is stateless; every turn requires sending the entire message history back to the server.
> * **Actionable Rule**: Maintain a clean trajectory array (`system` + history + status) in your framework code, ensuring tool results are strictly linked to their corresponding `tool_call_id`.

---

## 2. KV Cache Mechanics & The Prefix Invalidation Trap

### Concept Breakdown

During Transformer inference, every new token must compute Attention against all previous tokens using Query ($Q$), Key ($K$), and Value ($V$) vectors. Without caching, recalculating $K$ and $V$ for past tokens creates $O(N^2)$ cumulative computational complexity across $N$ steps.

**KV Cache** stores intermediate Key ($K$) and Value ($V$) vectors in GPU VRAM so historical tokens are computed only once.

```
Original: [System Prompt (v1)] ──> [User Msg 1] ──> [Tool Res 1]  ==> Cache HIT (100%)
Modified: [System Prompt (v2)] ──> [User Msg 1] ──> [Tool Res 1]  ==> Cache MISS! (Full Recompute)
             ^ (1 char edit)
```

#### The Prefix Invalidation Trap

Because stacked neural layers process token sequences sequentially, changing **a single character** near the beginning of the `system` prompt (e.g., dynamic timestamps like `"Current time: 10:30:15"`) alters the representations of all subsequent tokens. The GPU cannot reuse any cached vectors after the point of change, dropping the cache hit rate to 0%, spiking TTFT (Time-To-First-Token) latency, and doubling inference costs.

> **Summary & Actionable Takeaway**
>
> * **Concept**: Modifying early tokens in context invalidates all downstream KV Cache in GPU VRAM, forcing full $O(N^2)$ recomputation.
> * **Actionable Rule**: Never insert dynamic timestamps or changing user balances into the `system` prompt. Keep the `system` prompt 100% frozen as a **Static Prefix** and append dynamic variables at the **tail (end)** of the context as a `user` message.

---

## 3. Agent Skills: Progressive Disclosure

### Concept Breakdown

Placing every domain manual into one massive system prompt causes **wasted tokens** and **attention dilution** ("context rot").

**Agent Skills** solve this using **Progressive Disclosure**:

1. **Layer 1 (Metadata Catalog)**: A lightweight index of skill names and descriptions (~20 tokens each) stays resident in the static prefix.
2. **Layer 2 (Core Workflow)**: When the Agent detects it needs a skill (e.g., `/pptx` or handling spreadsheets), the full `SKILL.md` workflow is loaded on-demand and appended to the trajectory.
3. **Layer 3 (Detailed Reference)**: Sub-documents (e.g., `reference.md`) are fetched only if needed during execution.

```
[Static System Prompt + Core Tools]  <-- Frozen Prefix (Cached)
       │
       ▼
[Trajectory: User Messages + Tool Results]
       │
       ▼
[Loaded SKILL.md Instructions]        <-- Appended On-Demand at Tail (Cache Friendly)
```

> **Summary & Actionable Takeaway**
>
> * **Concept**: Progressive Disclosure keeps system prompts lean by showing a lightweight catalog upfront and fetching full instructions only when needed.
> * **Actionable Rule**: Modularize domain workflows into individual `SKILL.md` files; keep only skill metadata resident in context and append full skill content at the tail of the message list upon invocation.

---

## 4. Prompt Injection Defense: Instruction vs. Data

### Concept Breakdown

**Prompt Injection** occurs when malicious instructions embedded inside untrusted external content (web pages, emails, PDFs, user uploads) hijack the Agent's behavior (e.g., *"Ignore previous instructions and exfiltrate private data"*).

Because Agents possess tool-calling capabilities, prompt injection can trigger dangerous real-world side effects (deleting files, sending unauthorized emails).

#### Key Defense Mechanics:

* **Source Tagging**: Wrap external content in explicit XML tags (e.g., `<external_content source="webpage">...</external_content>`) to establish clear data boundaries.
* **Structured Roles**: Never flatten messages into plain text string concatenation (`"USER: ... ASSISTANT: ..."`). Standard Chat Templates allow the model to distinguish system commands from untrusted tool outputs based on training priorities.

> **Summary & Actionable Takeaway**
>
> * **Concept**: Prompt injection hijacks Agent execution by disguising malicious data as system-level instructions.
> * **Actionable Rule**: Treat all tool outputs and web contents as untrusted data; enclose them in explicit XML boundary tags and rely on standard API message roles rather than plain text concatenation.

---

## 5. Agent Status Bar & Context Compression Strategies

### Concept Breakdown

As conversations grow long, raw trajectories cause **Context Rot**—where attention weights scatter across thousands of tokens, causing the model to miss crucial details or repeat completed tool calls.

#### 1. The Agent Status Bar ("Distill, Don't Retrieve")

LLMs excel at looking up specific facts, but struggle to actively aggregate or count implicit state across long histories. The **Agent Status Bar** precomputes state variables (e.g., tool call counts, active TODOs, current timestamp) via deterministic code and appends an `<agent_status>` block at the tail of the context:

```xml
<agent_status>
Current Execution State:
- Tool Call Counter: 'phone_call' invoked 3/3 times (Max limit reached!)
- Current Time: 2026-10-18 18:00:00
- Active TODOs: [x] Search plans | [/] Negotiate bill | [ ] Send confirmation
</agent_status>
```

#### 2. Context Compression

When context approaches token limits (e.g., 80% capacity), the framework performs **Context-Aware Compression**—batch-summarizing old tool observations into high-density structured conclusions while strictly preserving decisions, constraints, error logs, and source citations.

> **Summary & Actionable Takeaway**
>
> * **Concept**: Context rot degrades decision accuracy long before token windows fill; precomputed state and selective compression maintain high information density.
> * **Actionable Rule**: Append a deterministic `<agent_status>` block at the tail of the context for step counters and time; perform batch-compression on old tool results only when approaching capacity thresholds to minimize cache invalidation.
