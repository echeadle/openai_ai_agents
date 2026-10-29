# Chapter 7 – Memory Meets Execution: Persisting State Without Losing Determinism

Up to this point, the agent can:

- Reason using a stable control loop
- Generate plans as disposable artifacts
- Execute tools explicitly in Python
- Reject invalid model output deterministically

What it still lacks is *continuity*.

Memory provides that continuity—but memory is also where most agent systems collapse into nondeterminism. State leaks. Prompts inflate. Old decisions silently influence new ones. Debugging becomes impossible.

This chapter introduces memory with a single governing rule:

> **Memory is written deliberately, read explicitly, and never mutated implicitly.**

If that rule holds, determinism survives.

---

## 1. Why Memory Is the Most Dangerous Capability

Memory changes the agent from a stateless function into a stateful system.

That transition is irreversible.

Common memory failures include:

- Storing raw conversation transcripts
- Allowing the model to decide what to remember
- Mutating memory during execution
- Accumulating state without bounds or inspection

Each of these breaks replay, predictability, or both.

Memory must therefore be treated as an **architectural component**, not a feature.

---

## 2. What Memory Is (And Is Not)

Memory is not context.

Memory is not a prompt.

Memory is **persisted state owned by Python**.

Concretely, memory is:

- A readable data structure
- Written at explicit points in the loop
- Loaded deliberately before reasoning
- Optional, discardable, and inspectable

If memory cannot be printed in seconds and understood by a human, it violates the system’s constraints.

---

## 3. Defining a Minimal Memory Interface

We start with the smallest possible abstraction.

```python
from typing import Any, List

class Memory:
    def load(self) -> List[Any]:
        raise NotImplementedError

    def write(self, item: Any) -> None:
        raise NotImplementedError
```

There is no automatic update.

There is no background sync.

Memory changes only when `write()` is called.

---

## 4. A Concrete, Inspectable Memory Store

We begin with an in-memory implementation.

```python
class ListMemory(Memory):
    def __init__(self):
        self._items = []

    def load(self):
        return list(self._items)

    def write(self, item):
        self._items.append(item)
```

Important properties:

- Order is preserved
- Mutation is explicit
- State is visible

This implementation is intentionally unsophisticated.

Sophistication comes later—after discipline is proven.

---

## 5. When Memory Is Read

Memory is read **before reasoning**.

Never during execution.

```python
    def think(self, user_input: str) -> ModelResponse:
        memory_snapshot = self.memory.load()

        raw = self.call_model(
            user_input=user_input,
            memory=memory_snapshot,
        )
        return parse_model_response(raw)
```

Key rule:

- The model sees a *snapshot*
- Execution cannot change what the model already saw

This prevents mid-turn feedback loops.

---

## 6. When Memory Is Written

Memory is written **after execution**, not during reasoning.

```python
    def act(self, response: ModelResponse):
        result = None

        if response.tool_call:
            result = self.execute(
                response.tool_call.tool,
                response.tool_call.input
            )

        if result is not None:
            self.memory.write({
                "tool": response.tool_call.tool,
                "input": response.tool_call.input,
                "output": result,
            })

        if response.message:
            print(response.message)
```

This ordering matters.

- The model does not decide what is stored
- Only validated, executed results enter memory

---

## 7. Memory Does Not Control Flow

Memory informs reasoning.

It does not:

- Trigger tools
- Skip steps
- End loops

If memory alters control flow, control has leaked.

The loop still runs exactly once per turn.

---

## 8. Determinism, Replay, and Debugging

With explicit memory discipline, you can:

- Serialize memory to disk
- Reload it later
- Replay the same inputs
- Observe identical execution paths

This is impossible with transcript-based memory.

Memory becomes testable.

---

## 9. What We Deliberately Still Avoid

This chapter intentionally avoids:

- Vector databases
- Similarity search
- Long-term summarization
- Model-driven memory writes

Those techniques are *optional optimizations*, not foundations.

Introducing them too early destroys clarity.

---

## 10. Exercises

1. Add a size limit to memory and enforce eviction explicitly.
2. Serialize memory to JSON and reload it. Confirm replay still works.
3. Attempt to write to memory during reasoning. Observe why this breaks determinism.

If memory changes without an explicit `write()` call, the exercise has failed.

---

Memory does not make an agent intelligent.

It makes an agent **accountable**.

In the next chapter, we will combine planning, structured outputs, execution, and memory into a single, fully inspectable agent loop—and examine where most real systems still go wrong.

