# Appendix A – Control‑Boundary Invariants (Chapters 15–18)

This appendix consolidates the **non‑negotiable system invariants** introduced in Chapters 15–18.

These are not guidelines.
They are **binary architectural tests**.

If any invariant fails, the system has violated a control boundary — regardless of intent, scale, or justification.

Use this checklist during:
- system design
- code review
- architecture review
- pre‑deployment sign‑off

---

## Invariants from Chapter 15 — Coordination Without Shared State

**Invariant 15.1 — No shared mutable state between agents**  
An agent must not be able to change another agent’s future behavior except through explicit message passing.

- ❌ Failure smell: agents read or write to the same in‑memory object, cache, or database row to coordinate
- ✅ Pass condition: all coordination occurs via explicit, inspectable messages or artifacts

**Invariant 15.2 — Coordination artifacts are not control signals**  
Messages may inform, but never command.

- ❌ Failure smell: receiving a message automatically triggers an action
- ✅ Pass condition: messages are inputs to reasoning, not execution drivers

---

## Invariants from Chapter 16 — Supervision Without Control Capture

**Invariant 16.1 — Supervisors have zero execution authority**  
Supervisors may observe and evaluate, but cannot influence execution in any form.

- ❌ Failure smell: supervisor output changes timing, routing, retries, or tool access
- ✅ Pass condition: removing the supervisor changes no agent behavior

**Invariant 16.2 — Supervision is strictly post‑hoc**  
Supervisors only consume artifacts from completed executions.

- ❌ Failure smell: supervisors run inside the agent’s control loop
- ✅ Pass condition: supervision begins only after execution has finished

---

## Invariants from Chapter 17 — Consuming Evaluations Without Feedback

**Invariant 17.1 — Evaluations are data, not rewards**  
Evaluation results must never function as implicit or explicit reward signals.

- ❌ Failure smell: scores influence future prompts, retries, or agent selection
- ✅ Pass condition: evaluations are written, stored, and consumed without side effects

**Invariant 17.2 — Evaluation does not alter execution ordering**  
Execution must not wait for, branch on, or react to evaluation outcomes.

- ❌ Failure smell: evaluation completion gates subsequent runs
- ✅ Pass condition: execution scheduling is evaluation‑agnostic

---

## Invariants from Chapter 18 — Governance Without Execution Authority

**Invariant 18.1 — Governance never runs in the control loop**  
Governance systems must not execute before or during agent operation.

- ❌ Failure smell: compliance checks gate execution or tool usage
- ✅ Pass condition: governance only reads immutable artifacts

**Invariant 18.2 — No causal path from governance to execution**  
Governance findings must not influence agent behavior — directly or indirectly.

- ❌ Failure smell: governance outcomes trigger retries, prompt edits, or policy injection
- ✅ Pass condition: governance escalates only to humans or external processes

---

## System‑Level Invariants (15–18)

**Invariant S.1 — Control flow lives in Python, not artifacts**  
Artifacts may inform humans. They must not steer machines.

**Invariant S.2 — All non‑execution layers are observational**  
Coordination, supervision, evaluation, and governance observe execution — they do not shape it.

**Invariant S.3 — Removing any non‑authoritative layer changes nothing**  
If removing a layer alters behavior, timing, or outcomes, that layer had authority.

---

## Final Gate

Before shipping a system, answer this question honestly:

> *Can any output produced after execution cause an agent to behave differently in the future?*

If the answer is **yes**, a control boundary has been breached.

Fix the architecture — not the policy.

