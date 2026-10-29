# OpenAI AI Agents — A Book & Codebase for Building Agents Without Frameworks

This repository accompanies the book **“AI Agents Without Frameworks”**, a hands-on, architecture-first guide to building production-grade agents using pure Python and the OpenAI API.

The goal of this project is to teach you how to build:
- Structured-output LLM agents  
- Planning agents  
- Tool-using agents  
- Memory-enabled agents  
- Multi-agent systems  
- Deployable autonomous applications  

All **without relying on LangChain, CrewAI, or other heavy frameworks**.

Instead, you will learn the underlying engineering patterns and implement everything step-by-step.

---
# 🚀 Features (Current Scope)
This repository currently includes the code for **Chapter 3**, where we build our first real agent:

- A validated, structured-output agent  
- JSON parsing + Pydantic schemas  
- Retry-on-invalid-output  
- uv-managed project environment  
- Example runnable script  

As the book progresses, this repo will grow naturally into a full, production-ready agent architecture.

---
# 🧱 Project Philosophy
- **Minimalism first.** Every file exists only when needed.
- **Pedagogy over magic.** No hidden abstractions.
- **Architecture forward.** Agents must be engineered, not demoed.
- **Production mindset.** Validation, retries, modularity, and safety come early.
- **Reproducible environments.** uv ensures consistent builds.

---
# 📁 Repository Structure

Below is the structure **as of Chapter 3**. Future chapters will extend this incrementally.

```text
openai_ai_agents/
│
├── pyproject.toml            # uv project config & dependencies
├── .env.example              # Template for secrets
├── .gitignore
│
├── ai_agents/                # Python package (kept separate from repo name)
│   ├── __init__.py
│   └── core/
│       ├── __init__.py
│       ├── base_agent.py     # Chapter 3: structured-output agent
│       └── schemas.py        # Pydantic models for validation
│
└── examples/
    └── chapter03_structured_agent.py
```

Future chapters add:

```text
ai_agents/core/planning.py         # Chapter 4
ai_agents/tools/                   # Chapters 5–6
aI_agents/memory/                  # Chapters 8–9
aI_agents/multi_agent/             # Chapters 10–11
```

---
# ⚙️ Installation & Environment Setup
This project uses **uv**, a fast Python package manager.

### 1. Create the environment
```bash
uv sync
```

### 2. Copy the `.env` template
```bash
cp .env.example .env
```

Edit `.env` and insert your API key:
```env
OPENAI_API_KEY="your-key-here"
MODEL_NAME="gpt-5-mini"
```

### 3. Run the Chapter 3 example
```bash
uv run python examples/chapter03_structured_agent.py
```

You should see structured JSON output validated by Pydantic.

---
# 📚 Chapter Progression

| Chapter | Status | Description |
|--------|--------|-------------|
| 1–2 | ✓ Complete | Conceptual foundations (no code) |
| **3** | ✓ Code included | First structured-output agent |
| 4 | TBD | Planning agents |
| 5–6 | TBD | Tools & multi-tool agents |
| 8–9 | TBD | Memory & retrieval |
| 10–11 | TBD | Multi-agent systems |
| 14 | TBD | Deployment with uv & FastAPI/CLI |

Each chapter will introduce new modules and examples.

---
# 🤝 Contributing
This repo is being built alongside the book. Feel free to submit improvements, typos, or enhancements once the book draft is public.

---
# 📄 License
You may choose your own license here (MIT recommended).

---
# ⭐ Acknowledgment
This repository is built collaboratively using OpenAI’s ChatGPT to refine architecture, code, and explanations—while keeping the engineering entirely transparent and framework-free.

