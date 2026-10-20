# Lesson 1: What Makes an AI Agent an Agent?

**Book reference:** [Chapter 1: Getting Started with AI Agents](../book-en/chapter1.md)  
**Status:** Introduced; discussion question is open.

## The main idea

An agent uses a language model together with information and tools to pursue a task. It can choose an action, observe the result, and decide what to do next. The book summarizes the core components as:

**Agent = LLM + context + tools**

- **LLM:** Helps decide what to do and interpret results.
- **Context:** The information available to the model at a particular decision point, such as the request, instructions, and prior tool results.
- **Tools:** Operations the agent can request, such as reading a file, listing files, or running code.

## Example: Find the largest Markdown file

Suppose you ask: “Find the largest Markdown file in this book folder and tell me what it covers.”

An agent might:

1. Read the request and identify the available tools.
2. List the Markdown files and inspect their sizes.
3. Read the tool results and identify the largest file.
4. Read the relevant contents of that file.
5. Explain what it covers.

The agent's key pattern is the **loop**: decide on an action, get feedback, and use that feedback to decide what to do next. If a tool fails, the agent can use the error to try a different approach.

## What is the harness?

The **harness** is the surrounding application that runs the agent: it supplies context and tools, executes requested tool calls, manages the loop, and enforces practical constraints. Think of the model as a component within a larger program.

For example, the harness can limit the number of iterations, handle tool errors, and enforce permissions. A model proposing a tool call does not itself execute the tool; the surrounding application must do that.

## Simplified pseudocode

This illustrates the control flow; the exact API and message format depend on the model provider.

```python
context = [user_request]

while True:
    response = model.respond(context, available_tools)

    if response.is_final:
        return response.text

    result = run_tool(response.tool_call)
    context.extend([response.tool_call, result])
```

A production implementation also needs error handling, permissions, and a limit on iterations.

## Think it through

Suppose the agent can reason well and read files, but has no way to list files or inspect their sizes. What prevents it from reliably answering the request?

**Your answer:** _To discuss._
