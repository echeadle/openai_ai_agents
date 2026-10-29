# Book Style Guide

*Building Effective LLM Workflows in Pure Python*

This style guide defines how all chapters in this book should be written. Its purpose is to ensure consistency, clarity, and technical rigor while preserving the book’s core philosophy: **explicit control beats abstraction**.

This is not a literary guide. It is an engineering guide.

---

## 1. Audience Assumptions

The reader is an **intermediate Python developer** who:

- understands functions, classes, and modules
- is comfortable with virtual environments and CLI tools
- can read non-trivial Python code

The reader is *not* assumed to:

- understand agent architectures
- trust AI abstractions
- be familiar with LLM failure modes

Writing must respect the reader’s intelligence without assuming prior agent experience.

---

## 2. Core Writing Philosophy

Every chapter must reinforce the following principles:

- **Nothing is magic**
- **State must be explicit**
- **Control flow belongs in Python, not the model**
- **Determinism is a design goal**
- **Transparency beats convenience**

If a section cannot clearly explain *where control lives*, it is incomplete.

---

## 3. Prose vs Structure Balance

This book uses a **hybrid, reference-leaning style**.

### Use prose when:

- introducing a new mental model
- explaining *why* a constraint exists
- describing failure modes
- framing architectural responsibility

Prose should be:
- concise
- concrete
- systems-oriented

Avoid motivational fluff or speculative language.

### Use bullets and structure when:

- listing API parameters
- describing interfaces
- enumerating constraints
- defining exercises
- presenting checklists or audits

Bullets are for **precision**, not for hiding complexity.

---

## 4. Code Presentation Rules

Code is central to the book and must follow strict rules.

### Code must always be framed

Never drop code without explaining:

- what problem it solves
- where it runs in the control flow
- what it deliberately does *not* do

### Code must be minimal but complete

- No pseudocode unless explicitly labeled
- No omitted imports
- No unexplained helpers
- No framework abstractions

Every snippet should be runnable with minimal setup.

### Code reflects architecture

If control flow is implicit or hidden, the code is wrong.

---

## 5. Control Loop Consistency

All agents in the book are built around an explicit control loop.

Rules:

- The loop structure does not change across chapters
- New capabilities attach to the loop; they do not replace it
- Memory, tools, and planning are *inputs* to the loop, not drivers of it

If a chapter modifies the loop structure, it must justify the change explicitly.

---

## 6. Memory and State Discipline

Memory is treated as an architectural component, not a feature.

Rules:

- All memory reads and writes must be visible
- No automatic persistence
- No hidden state mutation
- No unbounded growth

If memory cannot be inspected by a human in seconds, it violates the book’s standards.

---

## 7. Error Handling Philosophy

Failures are normal.

Writing should:

- treat API failures as expected control paths
- avoid retry magic
- explain tradeoffs explicitly

Any chapter that introduces external calls must discuss failure modes.

---

## 8. Exercises

Exercises are not optional or decorative.

Rules:

- Exercises are concrete and testable
- Each exercise reinforces architectural control
- No vague or open-ended prompts

Exercises should force the reader to:

- identify control boundaries
- validate assumptions
- reason about determinism

---

## 9. Tone and Language

Tone is:

- direct
- technical
- precise
- unsentimental

Avoid:

- hype
- anthropomorphism
- marketing language
- vague encouragement

The model is a tool. Treat it like one.

---

## 10. Final Test

Every section should pass this test:

> *Could a reader point to exactly where control lives after reading this?*

If the answer is no, the section must be rewritten.

---

This style guide is binding for all chapters. Deviations must be intentional and justified.

