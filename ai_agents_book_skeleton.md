# AI Agents Without Frameworks — 15-Chapter Expanded Skeleton

This is the **expanded skeleton** containing chapter titles, section headings, and short descriptions for each section. All chapters are placeholders to guide the full rewrite.

---
# **Chapter 1 — Why Agents Matter: The Evolution Beyond Chatbots**

## 1.1 The Limits of Simple LLM Wrappers
Briefly explain why basic "ask/answer" wrappers fail for real workflows.

## 1.2 What Makes an Agent an Agent?
Introduce the perception → reasoning → action loop and why structure matters.

## 1.3 Agents as Systems, Not Prompts
Explain that agents must integrate memory, tools, planning, and state.

## 1.4 Running Example: A Practical Agent
Describe the example agent that will evolve across the book.

## 1.5 Where Environment Setup Lives
Remind readers that full `uv` + `.env` setup is in Chapter 14.

---
# **Chapter 2 — The Core Agent Loop: Perception → Reason → Act**

## 2.1 The Minimal Agent
Show the smallest possible usable agent.

## 2.2 System Prompts and Behavioral Anchoring
Explain controlling the agent's role and behavior.

## 2.3 Deterministic vs Stochastic Reasoning
Discuss temperature and reproducibility.

## 2.4 The Full Control Loop
Formalize the receive → reason → act → repeat cycle.

## 2.5 Execution Boundaries
Define what the agent may or may not do.

---
# **Chapter 3 — Designing Durable Prompts & Output Schemas**

## 3.1 Why Prompt Engineering Still Matters
Clarify misconceptions and focus on structure, not tricks.

## 3.2 Task Prompts vs Agent Prompts
Differentiate between one-off tasks and agent roles.

## 3.3 Enforcing Structure with JSON
Explain why schemas prevent chaos.

## 3.4 Pydantic for Validation
Introduce typed schemas to validate agent outputs.

## 3.5 Error Handling for Invalid Model Outputs
Strategies for retrying and repairing.

---
# **Chapter 4 — Planning and Decomposition Strategies**

## 4.1 From Chatbot to Planner
Explain why agents need plans.

## 4.2 Plan-First vs Act-First
Compare two major agent paradigms.

## 4.3 Task Decomposition Patterns
Show examples of breaking a goal into subgoals.

## 4.4 Self-Reflection and Self-Correction
Introduce evaluation loops.

## 4.5 Integrating Plans into the Control Loop
Show how planning merges with Chapter 2 concepts.

---
# **Chapter 5 — Tools: Why Agents Need Them and How to Build Them**

## 5.1 What Is a Tool?
Define tools and why they exist.

## 5.2 Designing Clean Tool Interfaces
Focus on inputs, outputs, and constraints.

## 5.3 Building Tools in Python
Walk through writing a simple tool.

## 5.4 Safety Boundaries
Explain limiting what tools may access.

## 5.5 Exercising a Tool-Using Agent
Run a simple example end-to-end.

---
# **Chapter 6 — Tool Patterns: Single Tool, Multi-Tool, and Tool Sets**

## 6.1 Tool Registries
Explain how agents discover tools.

## 6.2 Choosing the Right Tool
Model-driven selection strategies.

## 6.3 Multi-Tool Execution
Chaining tools together.

## 6.4 Tool Selection Failures
Error handling and corrections.

## 6.5 Real-World Multi-Tool Example
Walk through a realistic demonstration.

---
# **Chapter 7 — Routers and Classifiers: Letting Agents Choose What to Do**

## 7.1 Why Routing Exists
Explain classification needs in multi-behavior agents.

## 7.2 Intent Classification
Show simple routing models.

## 7.3 Content-Based Routing
Route based on input characteristics.

## 7.4 Routing Errors and Recovery
Handle misclassification.

## 7.5 Routers Inside Multi-Agent Systems
Preview interactions with Chapter 10.

---
# **Chapter 8 — State and Memory Models: What to Remember and When**

## 8.1 Why Memory Must Be Designed
Avoid uncontrolled prompt growth.

## 8.2 Transient State
Short-lived task state.

## 8.3 Persistent State
Long-term knowledge without embeddings.

## 8.4 Interaction History Management
Summaries vs full logs.

## 8.5 Memory Policies
Define rules for what to store.

---
# **Chapter 9 — Embeddings and Vector Memory for Retrieval-Augmented Agents**

## 9.1 Why Embeddings Matter
Explain semantic similarity.

## 9.2 Building a Vector Store
Show FAISS or alternative.

## 9.3 Indexing and Searching
Explain how queries work.

## 9.4 Retrieval-Augmented Reasoning
Integrate with agent loop.

## 9.5 Long-Term Memory Strategies
Retention, cleanup, and scaling.

---
# **Chapter 10 — Multi-Agent Systems: Collaboration and Arbitration**

## 10.1 Why Multi-Agent Architectures Exist
Explain division of labor.

## 10.2 Role-Based Agents
Define specialist agents.

## 10.3 Supervisor / Arbiter Agents
Discuss coordination patterns.

## 10.4 Messaging Protocols
Define interfaces between agents.

## 10.5 Avoiding Chaos in Multi-Agent Designs
Address complexity and failure recovery.

---
# **Chapter 11 — Building Tool-Using Multi-Agent Workflows**

## 11.1 Workflow Composition
Explain how tools and agents form pipelines.

## 11.2 Blackboards and Task Boards
Introduce shared state structures.

## 11.3 Parallel Execution
Discuss concurrency patterns.

## 11.4 Coordination Failures
Handle deadlocks, loops, and stalls.

## 11.5 Full Workflow Example
Walk through a real scenario.

---
# **Chapter 12 — Designing Production-Grade Agent Architectures**

## 12.1 Reliability in Agent Systems
Retries, timeouts, and safe fallbacks.

## 12.2 Observability
Logging, metrics, and tracing.

## 12.3 Guardrails
Prevent harmful or invalid actions.

## 12.4 Testing Agent Systems
Unit, integration, and scenario tests.

## 12.5 Deployable Robust Design
Make production-ready systems.

---
# **Chapter 13 — Cost, Performance, and Efficiency Strategies**

## 13.1 Token Optimization
Control costs.

## 13.2 Model Selection
Choose the correct model for each task.

## 13.3 Memory and Routing Efficiency
Reduce unnecessary calls.

## 13.4 Local Caching
Avoid duplicate work.

## 13.5 Performance Profiling
Benchmarking approaches.

---
# **Chapter 14 — Deployment: Packaging Agents for Real Use**

## 14.1 Project Initialization with uv
Set up clean environments.

## 14.2 Dependency Management
Use uv add and uv pip.

## 14.3 Secrets with .env and python-dotenv
Store and load API keys securely.

## 14.4 Packaging for CLI Use
Build command-line agents.

## 14.5 Building Web Interfaces
Streamlit or FastAPI frontends.

## 14.6 Shipping Your Agent
Local vs cloud deployment options.

---
# **Chapter 15 — Designing Your Own Agent: A Complete Blueprint**

## 15.1 Requirements Gathering
Define agent goals.

## 15.2 Architecture Selection
Choose state, memory, tools, routing, and workflows.

## 15.3 Blueprint Template
Provide structured templates.

## 15.4 Implementation Walkthrough
Build a full example agent.

## 15.5 Validation and Testing
Ensure correctness and robustness.

