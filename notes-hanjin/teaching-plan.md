# AI Agent Learning Plan

## Goal

Learn the concepts behind AI agents, build an agent, orchestrate agents with a framework, and apply the skills to a real-world problem. The learner knows Python and C#/.NET. Use Python for initial examples, with C#/.NET comparisons where useful.

The source material is the book in [`book-en`](../book-en/). Its chapters are detailed, so use them as a reference and teach in small, approachable lessons. Read the relevant book sections closely when preparing each lesson.

## How we will learn

- Introduce one concept at a time in plain language, using a concrete example.
- Check understanding with a short question or scenario and adjust the pace based on the answer.
- Add small coding exercises once the foundation is clear. Explain new terms and relate them to familiar programming concepts.
- Treat detailed research, advanced mathematics, and lengthy code as optional deeper layers.
- Save lesson notes in this folder as separate Markdown files, including explanations, examples, questions, and relevant book links. Record progress and revisit earlier topics as needed.

## Learning path

### 1. Understand the concepts

**Outcome:** Explain how an agent operates and identify the role of its model, context, tools, and surrounding application.

- What makes an agent an agent: model, context, tools, and the observe–act loop.
- The harness: how application code manages execution, errors, limits, and permissions.
- Instructions and context: what the model sees and how information reaches it.
- Tools: calling functions and using their results.
- Memory and knowledge: retaining and retrieving useful information.
- Reliability: verification, failure modes, and basic evaluation.

Primary reading: [Chapter 1](../book-en/chapter1.md), [Chapter 2](../book-en/chapter2.md), [Chapter 3](../book-en/chapter3.md), and [Chapter 4](../book-en/chapter4.md). Draw on [Chapter 7](../book-en/chapter7.md) for evaluation.

### 2. Build an agent

**Outcome:** Build and test a small agent that uses a tool to complete a defined task.

- Choose a small task and define success.
- Connect a model to Python code and define a tool.
- Implement the model–tool loop and manage context.
- Add error handling, iteration limits, and appropriate permissions.
- Test successful and failing cases; inspect what happened.

Use [Chapter 4](../book-en/chapter4.md), [Chapter 5](../book-en/chapter5.md), and [Chapter 7](../book-en/chapter7.md) as references. Pick a model provider and discuss any account or API costs before implementation.

### 3. Orchestrate agents with a framework

**Outcome:** Build a small coordinated workflow and understand when more than one agent is useful.

- Compare a simple workflow, a single agent, and multiple agents.
- Learn roles, delegation, handoffs, shared versus separate context, and coordination patterns.
- Select a suitable framework when we reach this stage, based on the learner's goals and current documentation.
- Implement and test a workflow with clear responsibilities and completion criteria.
- Examine coordination costs, failures, and safeguards.

Primary reading: [Chapter 1](../book-en/chapter1.md) and [Chapter 10](../book-en/chapter10.md); use [Chapter 7](../book-en/chapter7.md) for evaluation.

### 4. Solve a real-world problem

**Outcome:** Design, build, and evaluate an agent-based solution to a problem the learner chooses.

- Select a real problem and define its users, constraints, and success criteria.
- Decide whether a single agent or a coordinated workflow is appropriate.
- Identify required data, tools, permissions, and human checkpoints.
- Build a minimal working solution and test representative cases.
- Improve it based on observed failures, while documenting trade-offs and next steps.

## Progress

- **Started:** Lesson 1, what makes an AI agent an agent. The first understanding question is open.
- **Next:** Discuss the answer, then explore the agent loop and harness in more detail.

## Lesson notes

- [Lesson 1: What makes an AI agent an agent?](lesson-01-what-is-an-agent.md)
