# Chapter 4 – Planning Is a Derived Artifact, Not a Controller

Planning is where most agent systems quietly break their own rules.

They start with an explicit control loop, then introduce a planner that suddenly *decides what happens next*. Control migrates from Python into prompts, chains, or hidden state machines. The loop still exists—but only cosmetically. The system becomes harder to debug, harder to reproduce, and impossible to reason about under failure.

This chapter is about avoiding that failure.

Planning is useful. Planning is powerful. But planning must remain a **derived artifact**—an input to the control loop—not a driver of it. The loop decides *when* planning happens and *how* its output is consumed. The planner never decides *what runs next*.

If you remember nothing else from this chapter, remember this:

> **The control loop executes. Planning informs.**

---

## 1. Why Planning So Often Goes Wrong

Most planning implementations collapse into one of three failure modes:

- The model generates a plan *and* implicitly executes it
- The plan determines control flow instead of informing it
- The plan accumulates hidden state across turns

These failures all share the same root cause: **planning is treated as a controller**.

Once a plan starts deciding which tools run, which steps are skipped, or when the loop terminates, you no longer have an explicit system. You have delegated orchestration to a stochastic process.

That may look elegant in a diagram. It fails immediately in production.

The discipline this book enforces is simple:

- The loop always runs
- The loop always decides
- Planning output is data, nothing more

---

## 2. What Planning Actually Is

Planning is not foresight. It is not intent. It is not agency.

Planning is a **temporary, inspectable representation of possible actions** produced from the current state.

Concretely:

- Planning consumes *inputs* (user request, memory snapshot, constraints)
- Planning produces *data* (steps, options, ordering, rationale)
- Planning does **not** execute anything

A plan is closer to a parsed document than a program.

If you cannot serialize a plan, print it, delete it, and regenerate it without changing system behavior, then planning is doing too much.

---

## 3. Attaching Planning to the Control Loop

We do not change the loop structure introduced in Chapter 2.

We extend it.

The loop remains:

**Receive → Reason → Act → Repeat**

Planning attaches inside **Reason**, alongside the model call—not above the loop and not around it.

Critically:

- The loop decides *whether* to plan
- The loop decides *when* to plan
- The loop decides *how* to use the plan

The planner is just another callable.

---

## 4. A Minimal Planner Interface

A planner has exactly one responsibility: produce a plan from explicit inputs.

Nothing more.

```python
from typing import List
from dataclasses import dataclass

@dataclass
class Plan:
    steps: List[str]

class Planner:
    def create_plan(self, task: str) -> Plan:
        raise NotImplementedError
```

This interface is intentionally boring.

There is no execution logic. No branching. No retries. No memory writes.

A plan is just data.

---

## 5. Implementing a Model-Backed Planner

Now we implement a concrete planner that uses an LLM to generate a plan.

This planner does not know about tools. It does not know about loops. It does not know what will happen next.

```python
from openai import OpenAI
import os

class LLMPlanner(Planner):
    def __init__(self):
        self.client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

    def create_plan(self, task: str) -> Plan:
        response = self.client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[
                {"role": "system", "content": "Generate a short, ordered plan."},
                {"role": "user", "content": task},
            ],
        )

        text = response.choices[0].message.content
        steps = [line.strip("- ") for line in text.splitlines() if line.strip()]
        return Plan(steps=steps)
```

Notice what is missing:

- No tool calls
- No control decisions
- No persistence

The planner generates a plan and returns it. That is the end of its authority.

---

## 6. Using the Plan Without Surrendering Control

Now we attach the planner to the agent.

The control loop decides whether to invoke it.

```python
class Agent:
    def __init__(self, planner=None):
        self.planner = planner

    def think(self, user_input: str):
        plan = None
        if self.planner:
            plan = self.planner.create_plan(user_input)

        return plan
```

At this stage, the agent does **nothing** with the plan.

That is intentional.

Planning is introduced before execution so you can clearly see the separation:

- Planning produces structure
- Execution comes later

If you feel uncomfortable that the plan is unused, that discomfort is correct. It means control has not leaked.

---

## 7. Inspectability and Deletion

A critical test for any planner:

- Can you log the plan?
- Can you delete it?
- Can you regenerate it?

If the answer to any of these is "no", the planner is acting as a controller.

In practice, plans should often be:

- Printed for debugging
- Compared across turns
- Discarded aggressively

A plan that outlives its usefulness is a liability.

---

## 8. Failure Modes and Discipline

Planning introduces new failure paths:

- The model produces nonsense
- The plan is incomplete
- The plan contradicts constraints

None of these are fatal—as long as planning remains advisory.

If planning fails, the loop still runs.

That is the invariant.

---

## 9. What We Deliberately Did Not Do

This chapter intentionally avoids:

- Executing plans
- Tool routing based on plans
- Multi-step planners
- Recursive planning

Those capabilities come later, *after* execution is explicit and controlled.

Premature planners are the fastest way to destroy an otherwise sound agent architecture.

---

## 10. Exercises

1. Modify the planner to return numbered steps instead of free text. Confirm nothing else breaks.
2. Add logging that prints the plan every turn, then remove the logging. Verify behavior is unchanged.
3. Disable the planner entirely. The agent should still run.

If any exercise requires changing the control loop, you have violated the chapter’s core rule.

---

Planning is powerful precisely because it does **not** control anything.

In the next chapter, we will introduce execution and tools—where plans become useful without becoming dangerous.

