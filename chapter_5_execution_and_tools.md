# Chapter 5 – Execution and Tools: Acting Without Losing Control

Up to this point, the agent can **receive input**, **reason**, and **produce artifacts** like plans. It still cannot *do* anything.

Execution is where agents become dangerous.

Most agent failures do not come from bad reasoning. They come from **uncontrolled action**—tools firing implicitly, models deciding what runs next, side effects triggered without visibility.

This chapter introduces execution and tools without surrendering control.

The rule that governs everything here is simple:

> **The control loop executes actions. Tools never execute themselves.**

If that boundary holds, the system remains debuggable, reproducible, and safe.

---

## 1. Why Tools Break Agents

Tools are just functions.

But when paired with LLMs, they often become something else entirely: hidden controllers.

Common failure patterns:

- The model selects and executes tools implicitly
- Tool calls are embedded inside prompts
- Tool results mutate state invisibly
- Execution order emerges instead of being defined

The result is a system where:

- You cannot predict what runs next
- You cannot replay a turn deterministically
- You cannot inspect or block side effects

This chapter is about avoiding all of that.

---

## 2. What a Tool Actually Is

A tool is **not** an ability.

A tool is a **purely callable capability** with a clear contract.

At minimum, a tool must define:

- A name
- Explicit inputs
- Explicit outputs
- Explicit side effects (or lack thereof)

If a tool’s side effects are unclear, it does not belong in an agent.

---

## 3. Tool Interface: Boring on Purpose

We start with a minimal interface.

```python
from typing import Any, Dict

class Tool:
    name: str

    def run(self, input: Dict[str, Any]) -> Dict[str, Any]:
        raise NotImplementedError
```

There is no magic here:

- Tools do not know about the agent
- Tools do not know about plans
- Tools do not know about the loop

They accept input and return output. Nothing else.

---

## 4. A Concrete Tool Example

Let’s implement a trivial but illustrative tool: a calculator.

```python
class CalculatorTool(Tool):
    name = "calculator"

    def run(self, input: Dict[str, Any]) -> Dict[str, Any]:
        expression = input.get("expression")
        if not expression:
            return {"error": "missing expression"}

        try:
            result = eval(expression, {"__builtins__": {}})
            return {"result": result}
        except Exception as e:
            return {"error": str(e)}
```

Important observations:

- The tool is synchronous
- Errors are returned, not raised
- Side effects are zero

This makes execution predictable.

---

## 5. Execution Lives in the Control Loop

Execution does **not** live in the model.

Execution does **not** live in the planner.

Execution lives in Python.

We extend the agent with an explicit execution step.

```python
class Agent:
    def __init__(self, tools=None, planner=None):
        self.tools = {tool.name: tool for tool in tools or []}
        self.planner = planner

    def execute(self, tool_name: str, tool_input: Dict[str, Any]):
        tool = self.tools.get(tool_name)
        if not tool:
            return {"error": f"unknown tool: {tool_name}"}

        return tool.run(tool_input)
```

This method is intentionally dull.

That dullness is what keeps the system correct.

---

## 6. How the Model Interacts With Tools

The model does **not** execute tools.

It can:

- Propose a tool name
- Propose structured input

That proposal is just data.

Example model output (as text or structured JSON):

```json
{
  "tool": "calculator",
  "input": {"expression": "2 + 2 * 3"}
}
```

Until Python reads this and explicitly calls `execute()`, nothing happens.

---

## 7. Wiring Execution Into the Loop

Now we connect reasoning, planning, and execution—without changing the loop structure.

```python
    def think(self, user_input: str):
        plan = None
        if self.planner:
            plan = self.planner.create_plan(user_input)

        # Model reasoning would go here
        return plan

    def act(self, plan):
        if not plan:
            return

        for step in plan.steps:
            # placeholder: no execution yet
            print(f"PLAN STEP: {step}")
```

Notice what is missing:

- No automatic tool execution
- No step interpretation
- No branching logic

That comes *after* the structure is stable.

---

## 8. Why Execution Must Be Explicit

Explicit execution gives you:

- The ability to block or sandbox tools
- Deterministic replays
- Auditable side effects
- Clear failure handling

Implicit execution gives you none of these.

If a tool fires without you seeing the call site, the architecture is already compromised.

---

## 9. Failure Modes You Must Expect

Tools fail.

Common cases:

- Invalid input
- External service outages
- Partial results
- Timeouts

Tools must surface failures as **data**, not control flow.

The loop decides what to do next.

---

## 10. What We Still Refuse to Do

This chapter intentionally avoids:

- Model-driven tool routing
- Auto-execution from JSON
- Tool retries hidden behind decorators
- Chained tool execution

All of those belong *after* execution is observable and testable.

---

## 11. Exercises

1. Add a second tool that returns the current UTC time. Verify execution stays explicit.
2. Simulate a tool failure and confirm the loop continues.
3. Log every tool execution, then remove logging. Behavior must not change.

If execution occurs anywhere other than the control loop, the exercise is failed.

---

Execution is where agents earn trust—or lose it permanently.

In the next chapter, we will combine **planning + execution + memory** into a single, inspectable workflow without introducing hidden control.

