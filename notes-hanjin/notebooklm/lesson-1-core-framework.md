# Level 1: Core Framework & Architecture

## Lesson 1.1: The Core Formula of AI Agents

To build or understand any modern AI Agent, we start with one foundational formula:

$$\mathbf{Agent = LLM + Context + Tools}$$

All three components are completely indispensable. We break this formula down across three complementary levels of understanding:

* **Implementation Layer**: $\text{Agent} = \text{LLM} + \text{Context} + \text{Tools}$
* **Intuitive Layer**: $\text{Agent} = \text{Brain} + \text{Eyes} + \text{Hands \& Feet}$
* **Academic Layer**: $\text{Agent} = \text{Policy} + \text{Observation Space} + \text{Action Space}$

### 1. Intuitive Layer
* **The Brain ($\text{LLM}$)**: Large Language Model as the reasoning engine for intent understanding, planning, and decision-making.
* **The Eyes ($\text{Context}$)**: The working set of information available to the Agent at decision time (system rules, conversation history, current status, memory, domain knowledge). An Agent can only decide based on what it sees in context.
* **The Hands & Feet ($\text{Tools}$)**: Interfaces that allow the Agent to act on the external world (web search, Python interpreter, database access, APIs).

### 2. Academic Layer (Reinforcement Learning Mapping)
* **LLM $\rightarrow$ Policy ($\pi$)**: Decision-making rule calculating the next action given current state.
* **Context $\rightarrow$ Observation Space ($\mathcal{O}$) & History**: Structured representation of environment observations up to time step $t$ ($c_t = (o_1, a_1, \dots, o_t)$).
* **Tools $\rightarrow$ Action Space ($\mathcal{A}$)**: Set of allowable operations and interfaces through which the policy interacts with the environment.

> **Key Architectural Insight**: Expanding Context (Observation Space) and Tools (Action Space) is often the single most effective lever for improving Agent performance, rather than simply swapping in a larger model.

---

## Lesson 1.2: The ReAct Loop (Think $\rightarrow$ Act $\rightarrow$ Observe)

The dynamic interaction among Brain, Eyes, and Hands operates via the ReAct cycle:

$$\text{Think} \longrightarrow \text{Act} \longrightarrow \text{Observe}$$

1. **Think (Reasoning)**: LLM reads full context and generates step-by-step reasoning.
2. **Act (Action)**: LLM outputs a structured tool call request.
3. **Observe (Observation)**: Agent Framework (Harness) executes the tool and appends result back as a `tool` observation message.

---

## Lesson 1.3: Model vs. Harness ($\text{Agent} = \text{Model} + \text{Harness}$)

$$\mathbf{Agent = Model + Harness}$$
$$\mathbf{Harness = Context\ Management + Tool\ Interfaces + Constraints + Verification + Correction}$$

* **Constraints**: Defining permitted actions (permissions, rate limits).
* **Verification**: Testing execution success (syntax checks, unit tests, verifiers).
* **Correction**: Recovery handling (automated retries, circuit breakers, error feedback).

### Scenario Analysis: FileNotFoundError Recovery
* **Without Harness**: Raw LLM loop may repeatedly call failing tools in an infinite loop or hallucinate results from parametric memory.
* **With Harness**: Catches runtime errors, applies loop guards, and injects actionable recovery hints into context.
