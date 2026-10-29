# Chapter 2 – Building the First Control Loop (From Zero)

## 1. Introduction

In this chapter, we move from abstract definitions to an executable system. An agent is not an idea—it is a running control loop that accepts input, produces output, and persists across multiple turns. Until that loop exists, nothing else in this book matters.

We will intentionally build the smallest possible multi‑turn agent. No memory. No tools. No planning. Just a repeatable loop that talks to an LLM and returns its output. This establishes the mechanical foundation that every later capability will attach to.

The goal here is not usefulness—it is correctness. When this chapter is complete, you will have a working, inspectable control loop that you fully understand end to end.

## 2. Anatomy of a Minimal Control Loop

A control loop is nothing more than a repeatable cycle the agent executes every time it receives new input. The loop is simple by design; complexity comes later when memory, tools, and planning attach to it. The strength of this foundation determines how stable the entire agent framework will be.

The minimal loop performs three operations:

**Receive → Reason → Act → Repeat**

- **Receive**: Obtain input from a user or external source.
- **Reason**: Decide what the agent should output. Initially, this is just an LLM call.
- **Act**: Emit the result to the environment.
- **Repeat**: Stay alive for the next turn.

The important discipline here is separation of concerns:
- The loop handles orchestration.
- The reasoning system (the LLM) handles content.

This line must stay sharp. If the loop starts doing the agent’s thinking, everything collapses when you try to add memory, tools, evaluators, or planning later.

## 3. Implementing the Minimal Agent in Python

Theory is useless until it runs. The fastest way to understand a control loop is to build it exactly once, in its simplest possible form.

This section produces a fully functional multi‑turn terminal agent. It has no memory, no tools, and no planning logic. It does one thing: call an LLM and print the result. That is enough to demonstrate the mechanics of execution.

The loop lives inside a single class:

```python
import os
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()  # loads OPENAI_API_KEY

class Agent:
    def __init__(self, name="ZeroAgent"):
        self.name = name
        self.client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

    def think(self, user_input: str) -> str:
        """Minimal reasoning: one LLM call, no memory, no tools."""
        response = self.client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[{"role": "user", "content": user_input}]
        )
        return response.choices[0].message.content

    def act(self, text: str):
        print(text)

    def run(self):
        print(f"{self.name} is running. Type 'exit' to quit.")
        while True:
            user_input = input(">>> ")
            if user_input.lower() in {"exit", "quit"}:
                print("Goodbye.")
                break
            response = self.think(user_input)
            self.act(response)
```

Key points:

- The control loop lives in one place: `run()`.
- The LLM call is isolated in `think()` so the loop never performs reasoning.
- `act()` is trivial now but becomes important when tools enter the picture.
- The entire agent is inspectable—no hidden state, no magic abstraction.

With this, you have your first real agent. It isn’t smart, but it is **correct**, and correctness is the only thing that matters right now.

## 4. Direct API Integration

The minimal loop works, but until it calls a real model, it isn’t an agent. This section replaces the placeholder reasoning logic with a proper OpenAI API call using the modern client interface. No wrappers, no helpers—just raw Python.

The `think()` method becomes a thin, explicit LLM call:

```python
def think(self, user_input: str) -> str:
    response = self.client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": user_input}]
    )
    return response.choices[0].message.content
```

This is intentionally bare‑bones. The flow is predictable:
- Create the request.
- Send user input as a simple message list.
- Extract the model’s output directly.

Nothing is hidden. Nothing is abstracted. If this breaks, you know exactly where to look.

The decision to isolate the LLM call in `think()` is deliberate. When memory and tools arrive in later chapters, this method is the insertion point for both. The loop itself stays untouched.

Before moving on, run the agent with this implementation. Confirm it responds. Confirm multiple turns work. Confirm it exits cleanly. If something feels unclear here, it will become a disaster when tools and planning arrive.

## 5. Environment and Dependency Management

A control loop is worthless if the environment around it is brittle. Every example in this book follows the same setup process so readers can build agents that behave consistently on any machine.

This book standardizes on three requirements:
- **uv** for package and environment management
- **python-dotenv** for secrets
- **Python 3.11+** as the runtime

No exceptions.

### Creating the project environment

Use `uv` to create a clean, isolated workspace:

```bash
uv init minimal-agent
cd minimal-agent
uv add python-dotenv openai
```

This produces a reproducible Python environment with zero dependency bloat. `uv` handles everything: virtual env creation, dependency resolution, and locking.

### Managing secrets

Every agent requires an API key. Keys must never appear in source code or logs. `python-dotenv` is the enforcement mechanism.

Create a `.env` file in the project root:

```text
OPENAI_API_KEY=your_key_here
```

Make sure `.gitignore` excludes it:

```
.env
```

This keeps your environment clean and prevents accidental key leaks—one of the most common and costly mistakes new agent builders make.

### Verifying the setup

Run a quick diagnostic:

```bash
uv run python -c "from dotenv import load_dotenv; load_dotenv(); import os; print(bool(os.getenv('OPENAI_API_KEY')))"
```

You should see:

```
True
```

If you see `False`, the loop will fail before the first LLM call. Fix this now, not later.

At this point, your environment is stable, reproducible, and safe—everything an early control loop requires before you stack more complexity on top of it.

## 6. Running and Testing the Loop

A control loop is only real once it survives actual execution. This section is about validating the mechanics: multi‑turn flow, stable API calls, clean exits, and predictable behavior.

### Running the agent

From the project root:

```bash
uv run python agent.py
```

If everything is wired correctly, you should see:

```
ZeroAgent is running. Type 'exit' to quit.
>>> 
```

This is the loop coming to life—waiting for input, ready to reason, ready to act.

### Verifying multi‑turn behavior

Test basic interaction:

```
>>> Hello
<model output>
>>> What is 2+2?
<model output>
>>> exit
Goodbye.
```

You are looking for three things:
- Each turn triggers exactly one LLM call.
- The loop never blocks or hangs unexpectedly.
- Exit commands stop the loop immediately.

If any of these fail, the issue is structural—not logical. Fix the structure before continuing.

### Common early failures

These are the classic mistakes readers make:
- Missing `.env` file (loop fails on first LLM call)
- Typos in the environment variable name
- Installing `openai` under a system Python instead of the `uv` environment
- Attempting to run the file with `python` instead of `uv run`

Fixing the environment always comes before fixing the agent.

### Why testing matters at this stage

Later chapters will add memory, tools, evaluators, routing, and planning. If the core loop is even slightly unstable, those layers will amplify the instability. A clean baseline now saves you from cascading failures later.

