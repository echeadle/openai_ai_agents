# Chapter 8 – Putting It All Together: A Fully Inspectable Agent Loop

By now, every dangerous capability has been introduced—carefully, deliberately, and in isolation.

Earlier chapters were intentionally narrow. Each one added exactly one responsibility and then stopped. That discipline matters, because integration is where agent architectures usually fail. Not because individual components are wrong, but because *boundaries blur* once everything is turned on at the same time.

This chapter exists to prove a single claim:

> **These components compose without creating hidden control flow.**

No new capabilities are introduced here. If something feels new, it is a sign that a prior chapter was not fully understood.

---

## 1. The Non-Negotiable Invariants

Before assembling the full agent, we restate the invariants that must hold.

These are not design preferences. They are structural constraints. If any invariant breaks, the system becomes nondeterministic—even if it still appears to work.

- The control loop runs exactly once per turn
- The model never executes tools
- Planning never controls flow
- Memory never mutates implicitly
- Invalid model output halts execution

These rules are enforced by *where code is allowed to live*, not by hoping the model behaves.

Integration is simply the act of wiring components together without violating any of these constraints.

---

## 2. The Complete Control Loop

We begin with the outermost structure, because this is where control must remain anchored.

Here is the complete control loop, unchanged in shape from Chapter 2.

```python
class Agent:
    def __init__(self, model, planner, tools, memory):
        self.model = model
        self.planner = planner
        self.tools = {t.name: t for t in tools}
        self.memory = memory

    def run(self):
        while True:
            user_input = input(">>> ")
            if user_input.lower() in {"exit", "quit"}:
                break

            response = self.think(user_input)
            self.act(response)
```

Nothing inside this loop is intelligent.

That is intentional.

The loop does not reason, plan, execute, or remember. It only *orchestrates*. Every other capability must attach to this structure without reshaping it.

---

## 3. Reasoning: Reading Memory and Producing Proposals

Reasoning is the only phase where the model is invoked.

This phase performs three actions, in a fixed order:

1. Read memory
2. Generate a plan
3. Ask the model to propose a response

```python
    def think(self, user_input):
        memory_snapshot = self.memory.load()
        plan = self.planner.create_plan(user_input)

        raw = self.model.call(
            user_input=user_input,
            memory=memory_snapshot,
            plan=plan,
        )

        return parse_model_response(raw)
```

This ordering is critical.

- Memory is read *once*, as a snapshot. Execution cannot retroactively influence what the model saw.
- Planning produces a disposable artifact. It informs reasoning but does not determine action.
- The model returns structured data that must pass validation before anything happens.

If reasoning were allowed to write memory or execute tools, feedback loops would form inside a single turn. That is how determinism is lost.

---

## 4. Action: Execution and Persistence

Action is the only phase where side effects are allowed.

Everything that mutates the external world or internal state happens here—and nowhere else.

```python
    def act(self, response):
        if response.tool_call:
            result = self.execute(
                response.tool_call.tool,
                response.tool_call.input,
            )

            self.memory.write({
                "tool": response.tool_call.tool,
                "input": response.tool_call.input,
                "output": result,
            })

        if response.message:
            print(response.message)
```

The ordering inside `act()` is not arbitrary:

1. Execute the tool
2. Persist the result
3. Emit user-facing output

Memory is written *after* execution so that only validated, real outcomes are stored. The model never decides what is remembered, and memory never influences execution mid-turn.

---

## 5. Execution Is Explicit and Bounded

All tool execution passes through a single function.

```python
    def execute(self, tool_name, tool_input):
        tool = self.tools.get(tool_name)
        if not tool:
            return {"error": "unknown tool"}

        return tool.run(tool_input)
```

This function exists to make control visible.

There are no retries, no auto-routing, and no hidden branching. If a tool fails, it fails as data. The loop continues on the next turn.

If execution were distributed across prompts or callbacks, you would no longer be able to audit side effects.

---

## 6. Inspectability and Replay

Because boundaries are explicit, the system is fully inspectable.

At any point, you can:

- Print memory
- Log raw model outputs
- Disable planning
- Disable tools
- Replay prior inputs against saved state

Nothing is hidden behind framework machinery or implicit callbacks.

This is the difference between an agent you *understand* and one that merely appears to work.

---

## 7. Common Integration Mistakes

Most real-world agent failures occur at this stage.

Watch for these warning signs:

- Memory writes during reasoning
- Tool calls embedded in prompts
- Plans that directly trigger execution
- “Helpful” fallback behavior on invalid output

Each of these moves control out of Python and into places you cannot reliably debug.

---

## 8. What This Architecture Buys You

With this loop, you gain properties that most agent systems lack:

- Deterministic execution paths
- Auditable state transitions
- Testable components
- Clear failure boundaries

These properties do not come from smarter prompts. They come from disciplined control flow.

---

## 9. What We Still Have Not Done

Even now, we have intentionally avoided:

- Parallel agents
- Autonomous self-loops
- Self-modifying memory
- Framework abstractions

These are not omissions. They are deferred design decisions that only make sense once control is non-negotiable.

---

## 10. Exercises

1. Disable planning and confirm the agent still works.
2. Remove memory persistence and confirm behavior becomes stateless.
3. Log every state transition for one run, then replay it.

If behavior changes without code changes, determinism has been lost.

---

At this point, you have a complete agent architecture built from first principles.

In the next chapters, we will examine how systems like this fail under scale—and how to extend them without surrendering control.

