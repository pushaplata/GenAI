# Prompt: Design Architecture.md & Phase-Wise Roadmap for Post-Retrieval Pipeline

> **Usage**: Copy and paste the entire prompt below into your coding agent (Claude Code / Antigravity / Cursor / Copilot) to generate the complete `Architecture.md` and phase-by-phase execution plan.

---

# PROMPT START

## 1. Role & Objective
You are a senior software architect working inside an existing AI resume search codebase. 

The system already has a working retrieval stage:
- **Input**: Recruiter Query / Job Description
- **Current Retrieval**: Hybrid Search with **BM25 Retrieval returning the top 100 candidate resumes**.

Your task is to **design (do not implement yet)** the architecture and generate a comprehensive `Architecture.md` file covering **ONLY** the next three post-retrieval stages plus the end-to-end frontend:
1. **Reranking** — Reorder and score the top-100 BM25 retrieval candidates by deep contextual relevance.
2. **Deduplication** — Identify and eliminate near-duplicate profiles / redundant candidates while preserving the highest-ranked instance.
3. **Summarization** — Generate per-candidate fit explanations and an executive cohort summary.
4. **End-to-End Frontend** — An interactive web UI that executes queries, displays real-time stage progress/telemetry, and renders the final summarized, deduplicated, reranked results with full profile inspection.

---

## 2. 🔒 [STRICT] Constraints — Read Before Taking Action

> **[STRICT CONSTRAINT] DO NOT CHANGE, REFACTOR, DELETE, OR CLEAN UP ANY EXISTING FUNCTIONALITY UNTIL I EXPLICITLY SAY SO.**

- **Retrieval-BM25 (Top 100) is STRICTLY READ-ONLY**: Treat all existing BM25 scoring, index definitions, vector searches, and ingestion pipelines as immutable. You may inspect their schemas and contracts, but you must NOT edit existing retrieval code.
- **Strictly Additive**: All new code must live in new files, new modules, new types, and new standalone components.
- **Touchpoints Require Approval**: If wiring the new pipeline into existing application entry points (e.g. `src/app.ts`) is necessary, do NOT make the change directly. Instead, document it in a dedicated section named **"Requires Approval — Existing Code Touchpoints"** and wait for explicit sign-off.
- **Design First**: Produce `Architecture.md` first. Do not begin writing implementation code until the user approves `Architecture.md`.
- **Phase-by-Phase Execution**: When approved, execute strictly one phase at a time and stop for review after each phase.

---

## 3. Step 1 — Discovery (Read-Only Codebase Inspection)

Inspect the existing repository without making any modifications and summarize:
1. **Current Retrieval Contract**: Exact location of BM25 retrieval, method signature, input query parameters, and output candidate schema (top 100 candidate fields, types, ordering, and scores).
2. **Existing Backend Structure**: Server framework (Express/Node), routing structure, error handlers, config/environment access, and test framework.
3. **Existing Frontend Structure**: UI stack (`frontEnd/recruitbot-web`), component hierarchy, state management, API clients, and styling system.

Present this analysis in `Architecture.md` under **"Current System Snapshot"**.

---

## 4. Step 2 — Deliverable: `Architecture.md`

Generate a comprehensive `Architecture.md` file in the root directory containing the following sections:

1. **Overview & Goals**
   - End-to-end post-retrieval pipeline objectives.
2. **Current System Snapshot**
   - Read-only discovery findings (BM25 top-100 contract, data structures, active frameworks).
3. **Scope Boundary**
   - Explicit list of what is **In-Scope** (Reranking, Deduplication, Summarization, Frontend) vs. **Out-of-Scope** (Existing BM25 retrieval, embeddings, database schemas).
4. **High-Level Architecture Diagram**
   - Mermaid diagram depicting:  
     `Recruiter Query → BM25 Retrieval (Top 100, Untouched) → Reranking → Deduplication → Summarization → Frontend UI`
5. **Component Design**
   - **5.1 Reranking Module**: Scoring algorithm (LLM cross-encoder vs. heuristic term alignment), input/output contracts, fallback mechanism on timeout/failure.
   - **5.2 Deduplication Module**: Similarity metric (Jaccard skill set overlap, normalized identity clustering), thresholding (default 0.85), rank preservation strategy, duplicate counters.
   - **5.3 Summarization Module**: Per-candidate qualification highlights, cohort executive aggregate summary, fallback extractions.
6. **End-to-End Data Flow & Shared Schemas**
   - Exact TypeScript types and data passed between each stage (Candidate shape, stage timings, telemetry metrics, degradation flags).
7. **API Contracts**
   - Request and response payloads for dedicated pipeline endpoints (e.g. `POST /v1/pipeline/search` or stage-specific endpoints).
8. **Frontend Architecture**
   - UI layout in `recruitbot-web`: Search bar, filter toggles (top-K, threshold, summary style), stage progress ticker, aggregate summary card, candidate cards with rank badges and score pills, and full profile drawer/modal.
   - Handling of states: Loading (staged progress), Success, Empty results, Degraded warnings, and Error recovery.
9. **Non-Functional Requirements**
   - Latency budgets per stage (BM25 $\le$ 150ms, Rerank $\le$ 600ms, Deduplication $\le$ 30ms, Summarization $\le$ 800ms, Total $\le$ 1800ms).
   - Graceful degradation and fallback strategies.
10. **Phase-Wise Implementation Roadmap** (See Step 3).
11. **Requires Approval — Existing Code Touchpoints**
   - Explicit table of any existing file modifications needed for final integration (e.g., router mounting in `src/app.ts`), marked as requiring approval before editing.
12. **Open Questions & Assumptions**

---

## 5. Step 3 — Phase-by-Phase Implementation Plan

Build the roadmap in `Architecture.md` according to this phase structure:

- **Phase 0 — Contracts & Scaffolding**
  - *Objective*: Define shared TypeScript interfaces, DTOs, and telemetry types in new standalone files.
  - *Deliverables*: Pure type definitions; no business logic.
  - *Stop Gate*: ⏸ Stop — awaiting approval before next phase.

- **Phase 1 — Reranking Module**
  - *Objective*: Build standalone `PipelineRerankService` with dual-mode AI/heuristic scoring and isolated unit tests feeding mock BM25 top-100 outputs.
  - *Deliverables*: Service file + Unit test suite.
  - *Stop Gate*: ⏸ Stop — awaiting approval before next phase.

- **Phase 2 — Deduplication Module**
  - *Objective*: Build standalone `PipelineDeduplicateService` with Jaccard skill similarity, identity clustering, rank re-indexing, and unit tests.
  - *Deliverables*: Service file + Unit test suite.
  - *Stop Gate*: ⏸ Stop — awaiting approval before next phase.

- **Phase 3 — Summarization Module**
  - *Objective*: Build standalone `PipelineSummarizeService` generating per-candidate fit summaries and cohort aggregate summaries with unit tests.
  - *Deliverables*: Service file + Unit test suite.
  - *Stop Gate*: ⏸ Stop — awaiting approval before next phase.

- **Phase 4 — End-to-End Orchestrator, API & Frontend Integration**
  - *Objective*: Build orchestrator service, API controller/routes, and rich React UI in `recruitbot-web`.
  - *Deliverables*: Orchestrator service, route definitions, frontend feature components, and end-to-end tests.
  - *Stop Gate*: ⏸ Stop — final review and verification.

---

## 6. Execution Rules
1. Generate `Architecture.md` first.
2. Stop and wait for explicit approval before creating implementation code.
3. Once approved, execute strictly one phase at a time and stop after each phase.
4. Never modify existing retrieval or BM25 code.

# PROMPT END
