# AI Agent Architecture: 4-Level Interactive Learning Curriculum

This curriculum is distilled from the **AI Agent Book**, compiled from technical lectures at Turing's AI Agent Bootcamp and real-world engineering of production systems (such as Pine AI and OpenClaw).

---

## Level 1: Core Framework & Architecture (Chapters 1 & 2)

### 1. The Core Formula & Foundations (Chapter 1)
* **The Core Triad**:
  $$\text{Agent} = \text{LLM} + \text{Context} + \text{Tools}$$
  * **LLM (Brain)** $\rightarrow$ Policy
  * **Context (Eyes)** $\rightarrow$ Observation Space
  * **Tools (Hands & Feet)** $\rightarrow$ Action Space
* **The ReAct Loop**:
  $$\text{Think} \longrightarrow \text{Act} \longrightarrow \text{Observe}$$
  * Understanding trajectory building, message lists, and step-by-step reasoning.
* **Harness Engineering**:
  * Why "Model + Harness" defines production reliability.
  * Runtime guardrails, constraint enforcement, verification loops, and error handling.
* **Orchestration Patterns**:
  * Deterministic Workflows vs. Autonomous Agents.

### 2. Context Engineering (Chapter 2)
* **API Message Architecture**: Structuring System, User, Assistant, and Tool result messages.
* **KV Cache Mechanics**: Stable prefixes, prefix sharing, and optimization strategies to reduce inference latency and cost.
* **Prompt Engineering & Defenses**: Process-oriented system prompts, business rule encoding, and Prompt Injection defense strategies.
* **Dynamic Context Management**:
  * **Agent Skills**: On-demand dynamic loading of task-specific prompts/instructions.
  * **Agent Status Bar**: Injecting environment state, temporal awareness, and task metadata.
  * **Context Compression**: Truncation, sliding windows, and key observation summaries to prevent context rot.

---

## Level 2: Building Agents — Memory, Tools, Coding & Interaction (Chapters 3–6)

### 3. User Memory & Knowledge Bases (Chapter 3)
* **4-Level User Memory**: From basic session notes to structured long-term memory cards.
* **Complete RAG Stack**: Dense & sparse embeddings, hybrid search, ranking optimization, and contextual retrieval.
* **Agentic RAG**: Autonomous retrieval decision-making, Proposer-Reviewer indexing, and structured knowledge cards.

### 4. Tools & Interface Protocols (Chapter 4)
* **Agent-Computer Interface (ACI)**: Design principles for transparent, robust tool APIs.
* **Model Context Protocol (MCP)**: Tool interoperability standards and Skill Hub integration.
* **Tool Taxonomy & Security**: Perception, Execution, Collaboration, Event Triggering, and User Communication tools with strict execution sandboxing.

### 5. Coding Agents as Meta-Capabilities (Chapter 5)
* **Code Generation as a Universal Medium**: Using code for logical reasoning, exact data processing, and dynamic tool creation.
* **OpenClaw Architecture**: Minimalist toolsets (bash, file editor, interpreter) coupled with rich Harness verification loops.
* **Self-Bootstrapping**: Agents creating tools, inspecting runtimes, and building lower-level Agents.

### 6. Advanced Interaction: Modality & Timing (Chapter 6)
* **Expanding Observation & Action Spaces**: Transitioning from turn-based chatbots to real-time, event-driven systems.
* **Asynchronous Runtimes**: Wake-up events, safe points, cancellation, preemption, and fast/slow path separation.
* **Frontier Modalities**:
  * **Voice Agents**: Cascading pipelines vs. end-to-end full-duplex audio.
  * **Computer Use**: Operative GUI navigation and visual action spaces.
  * **Robotics**: Vision-Language-Action (VLA) models and Sim2Real transfer.

---

## Level 3: Evaluation, Training & Continuous Evolution (Chapters 7–9)

### 7. Agent Evaluation & Debugging (Chapter 7)
* **Evaluation Frameworks**: Capabilities vs. Reliability metrics.
* **Environment & Benchmark Design**: Tool-calling benchmarks ($\tau^2$-bench), interactive environments, and simulation setups.
* **Automated Evaluation**: LLM-as-a-Judge rubrics, deterministic verification, and Trajectory-Prefix Regression.
* **Failure Attribution**: Systematic trajectory breakdown to guide engineering iterations.

### 8. Model Post-Training (Chapter 8)
* **The 4-Stage Capability Map**: Pre-training $\rightarrow$ Mid-training $\rightarrow$ Supervised Fine-Tuning (SFT) $\rightarrow$ Reinforcement Learning (RL).
* **Core Post-Training Principle**:
  > *"SFT memorizes, RL generalizes."*
* **Reward Engineering**: Outcome rewards vs. process rewards, verified path penalties, and multi-turn RL algorithms.

### 9. Continuous Agent Evolution (Chapter 9)
* **Experience Consolidation**: Transforming real-world execution traces into system improvements.
* **4 Update Carriers**:
  1. Knowledge Documents (Facts)
  2. Prompts & Skills (Principles)
  3. Programs & Harnesses (Procedures)
  4. Model Parameters (High-dimensional behaviors)
* **Safe Deployment Loops**: Candidate versioning, offline regression tests, and canary rollouts.

---

## Level 4: Multi-Agent Collaboration (Chapter 10)

### 10. Multi-Agent Systems & Topologies (Chapter 10)
* **Information Flow**: Shared Context vs. Isolated Context architectures.
* **Collaboration Topologies**:
  * **Peer Patterns**: Proposer-Reviewer debate loops.
  * **Manager / Orchestrator**: Hierarchical delegation and task decomposition.
  * **Decentralized Swarms**: Autonomous inter-agent protocols (A2A).
* **Value Criterion**: Ensuring multi-agent interaction generates true external value (via tools/code/verification) rather than redundant internal dialogue.
