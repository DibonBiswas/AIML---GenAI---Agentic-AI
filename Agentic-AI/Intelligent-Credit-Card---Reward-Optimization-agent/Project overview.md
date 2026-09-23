# Intelligent Credit Card & Rewards Optimization Agent - Project Overview

## Problem
Choosing the best credit card for a specific transaction to optimize reward points is confusing and often involves complex math, remembering nuanced rules, and avoiding pitfalls like category exclusions. Users struggle to maximize their card benefits without manual calculations or reading dense terms and conditions, leading to lost value on their spending.

## Product Vision
To build an intelligent, agentic AI pipeline that deterministically calculates rewards, compares eligible cards, and safely advises users without hallucination. Unlike standard RAG chatbots, this system acts as a reliable, data-driven financial assistant that combines the reasoning capabilities of LLMs with strict programmatic safeguards, ensuring users always get the mathematically optimal card recommendation.

## Your Role
As the creator of this system, your role bridges multiple disciplines:
*   **Product Management:** Defining the agent's workflow, ensuring guardrails and human-in-the-loop (HITL) approvals are in place for critical actions (like reward points transfer).
*   **Business Analysis:** Mapping out complex credit card rules, reward multipliers, category exclusions, and conversion rates to structure the deterministic calculation logic.
*   **AI Engineering & Architecture:** Orchestrating a 10-node LangGraph pipeline, setting up a robust PostgreSQL/pgvector database for RAG, designing hybrid search for rule retrieval, and implementing rigorous evaluation mechanisms to prevent hallucinations.

## Key Features
*   **Deterministic Calculator Tool:** Relies on a pure Python calculator tool for exact INR values instead of depending on LLM math, ensuring 100% computational accuracy.
*   **Multi-Card Comparison & Ranking:** Analyzes multiple cards simultaneously for a given transaction and ranks them based on effective return percentage.
*   **Human-in-the-Loop (HITL) & Guardrails:** Enforces 9 distinct safety checks to prevent hallucinations and inappropriate advice, alongside mandatory human approval for irreversible financial decisions.
*   **Advanced RAG with pgvector:** Extracts and stores complex rule sets from card PDFs into PostgreSQL with pgvector for high-confidence, evidence-backed retrieval.
*   **Observability & Evaluation:** Full pipeline tracing via LangSmith, backed by a custom PostgreSQL audit log and comprehensive automated evaluation (Precision@K, Recall@K, MRR, RAGAS).

## Business Impact / Learning
*   **Outcome:** Delivers highly accurate, transparent, and actionable credit card recommendations that users can trust financially, turning a complex decision into a simple conversation.
*   **Insight & Learning:** Demonstrated that LLMs alone should not handle financial math. Combining an LLM's natural language and reasoning capabilities with a deterministic calculator, strictly typed state management (LangGraph), and rigid guardrails is essential for building reliable, production-ready FinTech AI agents.

---

## Technical Overview (End-to-End)
The application is structured as a robust, mathematically deterministic agent system:

### 1. Frontend Interface (Streamlit)
*   **Framework:** Streamlit (v1.41.0).
*   **Functionality:** Provides an interactive chat interface, multi-turn conversation support, and a monitoring tab for visually tracing agent logic, latency, token usage, and retrieved sources.
*   **Stability:** Bypasses standard Pandas to PyArrow serialization for complex JSON to prevent memory faults in cloud environments.

### 2. Backend Server (FastAPI)
*   **Framework:** FastAPI running on Uvicorn.
*   **Functionality:** Exposes REST API endpoints for seamless integration, enabling programmatic access to the agent's core decision engine outside of the Streamlit UI.

### 3. AI Orchestration (LangChain & LangGraph)
*   **Engine:** LangGraph manages a complex 10-node state machine (Intent ➡️ Clarification ➡️ Retrieval ➡️ Rule Validation ➡️ Calculation ➡️ Comparison ➡️ Guardrails ➡️ HITL ➡️ Final Answer).
*   **State Management:** Utilizes a strictly-typed `AgentState` to securely pass context, memory, and calculation results across the execution graph.
*   **LLMs:** Powered by OpenAI GPT-4o / GPT-4o-mini for reasoning, intent classification, and grounded natural language generation.

### 4. Databases & Storage
*   **Vector DB (PostgreSQL + pgvector):** Stores document embeddings (OpenAI `text-embedding-3-small`) and handles dense semantic search for the credit card knowledge base. Hosted on Supabase.
*   **Relational DB & Audit:** Utilizes pure Python `psycopg` and SQLAlchemy (with `NullPool` for transaction pooler compatibility) to manage schema and capture extensive audit logs securely.

### 5. Tools & Processing
*   **Document Ingestion:** Uses PyMuPDF (`fitz`) for robust text extraction from credit card PDFs.
*   **Tools:** Includes a custom `calculator` for exact rewards, a `rule_validator` for retrieval confidence checks, and a `transfer_calculator` for points conversion.
*   **Observability:** Integrated closely with LangSmith for automated tracing and performance debugging of all LLM calls.
