# Chapter 6 – Structured Model Outputs: Constraining the Model Without Losing Control

Up to now, the model has been allowed to speak freely.

That freedom is already a liability.

Free‑form text is acceptable when the agent only *responds*. The moment the agent starts **planning**, **executing**, or **persisting state**, unconstrained text becomes dangerous. Ambiguity leaks into execution. Invalid assumptions get stored. Debugging turns into archaeology.

This chapter introduces structured model outputs—not as a convenience, but as a **control boundary**.

> **The model may propose decisions. Python must validate them.**

Nothing crosses that boundary unchecked.

---

## 1. Why Free‑Form Output Breaks Agents

Most agent examples rely on prompts like:

> “Respond in JSON.”

This is not a contract. It is a request.

When the model deviates—and it will—you are left with:

- Partial structures
- Hallucinated fields
- Silent coercions
- Fragile string parsing

Once those outputs are executed or stored, the system becomes nondeterministic.

If output cannot be validated, it cannot be trusted.

---

## 2. Structured Output Is a Control Primitive

Structured output is not about formatting.

It is about **who decides what is valid**.

With structured outputs:

- The model proposes data
- Python enforces shape
- Invalid outputs are rejected
- Execution is blocked until structure is correct

This keeps authority where it belongs: outside the model.

---

## 3. Defining an Explicit Response Contract

We begin by defining the *only* shape of output the system will accept.

```python
from typing import Optional, Dict, Any
from pydantic import BaseModel

class ToolCall(BaseModel):
    tool: str
    input: Dict[str, Any]

class ModelResponse(BaseModel):
    message: Optional[str] = None
    tool_call: Optional[ToolCall] = None
```

This schema is intentionally narrow:

- Either the model speaks to the user
- Or it proposes a tool call

Nothing else is allowed.

---

## 4. The Model Produces Data, Not Instructions

The model is now prompted to **emit data that matches the schema**.

```python
SYSTEM_PROMPT = """
Respond only using valid JSON that matches this schema:

{
  "message": string | null,
  "tool_call": {
    "tool": string,
    "input": object
  } | null
}

Do not include any other fields.
"""
```

This prompt is still advisory.

The contract is enforced in Python.

---

## 5. Validating Model Output

Every model response is parsed and validated before use.

```python
import json
from pydantic import ValidationError


def parse_model_response(text: str) -> ModelResponse:
    try:
        data = json.loads(text)
        return ModelResponse.model_validate(data)
    except (json.JSONDecodeError, ValidationError) as e:
        raise ValueError("Invalid model output") from e
```

Key discipline:

- Parsing errors stop execution
- Validation errors stop execution
- No partial recovery

If the structure is wrong, **nothing happens**.

---

## 6. Blocking Execution on Invalid Output

This is where control becomes explicit.

```python
    def think(self, user_input: str) -> ModelResponse:
        raw = self.call_model(user_input)
        return parse_model_response(raw)

    def act(self, response: ModelResponse):
        if response.tool_call:
            return self.execute(
                response.tool_call.tool,
                response.tool_call.input
            )

        if response.message:
            print(response.message)
```

There is no fallback behavior.

Invalid output means no action.

---

## 7. Why Rejection Is Better Than Repair

Many systems attempt to *repair* malformed output.

This is a mistake.

Repair logic:

- Encodes hidden assumptions
- Masks model failures
- Creates untestable paths

Rejection forces clarity.

If the model cannot produce valid structure, the system does not proceed.

That is not harsh—it is safe.

---

## 8. Determinism and Replay

Structured outputs make replay possible.

Given:

- The same input
- The same model version
- The same schema

You can:

- Store raw model output
- Re-validate it later
- Re-run execution deterministically

Free‑form text does not allow this.

---

## 9. What We Still Do Not Allow

This chapter intentionally avoids:

- Automatic retries on invalid output
- Schema evolution at runtime
- Multiple competing response formats
- Implicit coercion

Those choices belong to *explicit policy*, not hidden logic.

---

## 10. Exercises

1. Add a required `confidence` field to the schema and observe failure modes.
2. Intentionally break the model output and confirm execution halts.
3. Log rejected outputs for inspection, then delete the logs. Behavior must not change.

If execution proceeds on invalid structure, the exercise has failed.

---

Structured output is not about making the model behave.

It is about **making the system correct even when the model does not**.

In the next chapter, we will finally introduce memory—now that every decision entering the system is explicit, validated, and safe to persist.

