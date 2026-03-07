# SDLC Automation Coverage — Combined Overview

**Source roadmap:** [integrant-roadmap.md](./integrant-roadmap.md)
**Last updated:** 2026-03-06

This file maps every SDLC step defined in the roadmap's Level 0 Process Flow to the goal (or goals) that automate it — and shows what each phase adds on top of the previous one.

---

## The 9-Step SDLC Process Flow (from roadmap)

| # | SDLC Step | Description |
|---|---|---|
| 1 | Requirement Gathering | Automated collection and analysis of requirements |
| 2 | Work Item Creation | Convert requirements into structured work items |
| 3 | Implementation | Automated code generation and implementation |
| 4 | Pull Request Creation | Automated PR generation |
| 5 | Testing | Automated test creation and execution |
| 6 | Deployment | Automated deployment processes |
| 7 | Project Management | Project oversight and coordination |
| 8 | Business & Technical Documentation | Documentation as part of Definition of Done |
| 9 | Team Metrics & Reports | Automated metrics collection and reporting |

---

## Phase 0 (Level 0) — Goals 1–10: Tooling & Workflow Automation

**Objective:** Automate the entire SDLC using AI tools in a tool-agnostic way.
**Role:** Sets up the infrastructure, context, and skill layer that all future phases depend on.

| SDLC Step | Covered? | Goal(s) | What's delivered |
|---|---|---|---|
| 1 — Requirement Gathering | Partial | Goal 3 | `/create-work-items` skill converts a requirements doc into structured GitHub Issues JSON; Python import script posts them to GitHub |
| 2 — Work Item Creation | ✅ Full | Goal 3 | Work item templates (Story, Task, Bug); structured JSON output; automated GitHub REST API import |
| 3 — Implementation | ✅ Full | Goals 1, 2, 4, 8 | Multi-repo workspace (Goal 1); AGENTS.md as AI context layer (Goal 2); architecture + standards docs in all repos (Goal 4); reusable skill files encapsulating coding standards and patterns (Goal 8) |
| 4 — Pull Request Creation | ✅ Full | Goal 6 | `/branch-and-pr` skill + bash script; PR description template; branch naming conventions; GitHub API integration |
| 5 — Testing | ✅ Full | Goal 7 | Playwright MCP server with `generate_e2e_tests` and `run_tests` tools; GitHub Actions CI pipeline; E2E tests for Agency booking flows |
| 6 — Deployment | ✅ Full | Goal 9 | GitHub Actions CD for all 3 repos (GHCR for API, Vercel for web, GitHub Packages for UI); secrets runbook; branch protection gates merge on green deploy |
| 7 — Project Management | Partial | Goal 3 | Work items created and linked to GitHub Issues; no automated sprint tracking or board management at this phase |
| 8 — Business & Technical Documentation | ✅ Full | Goals 4, 5 | Architecture, coding standards, dependency, and business context docs in all repos (Goal 4); MCP doc server with `generate_release_notes` tool + GitHub Actions artifact gate (Goal 5) |
| 9 — Team Metrics & Reports | ✅ Full | Goal 10 | Weekly GitHub Actions report: PR cycle time, deployment frequency, CI pass rate, open issues; auto-committed to `docs/metrics/` and posted as GitHub issue |

### Phase 0 Coverage Summary

| Status | Steps |
|---|---|
| ✅ Fully automated | Work Item Creation, Implementation, PR Creation, Testing, Deployment, Documentation, Team Metrics |
| Partial | Requirement Gathering (manual input still needed), Project Management (no board/sprint automation) |
| Not covered | — |

### What Phase 0 does NOT attempt

- **Automated requirement gathering** from stakeholders (e.g. interviews, PRD generation from voice/meeting transcripts) — this requires language understanding capabilities addressed in Phase 1+
- **Sprint planning and board management** — depends on autonomous agent decision-making, addressed in Phase 2

---

## Phase 1 (Level 1) — Goals 11–12: AI Integration Engineer

**Objective:** Develop practical AI fundamentals and system integration capabilities.
**Role:** Engineers learn to build the tools Phase 0 used — MCPs, local LLMs, RAG, workflow automation.

| SDLC Step | What Phase 1 adds |
|---|---|
| 1 — Requirement Gathering | Engineers can build custom MCPs that pull requirements from external sources (e.g. Jira, Confluence, Notion) |
| 3 — Implementation | Local LLMs (Ollama, LM Studio) reduce cost and latency for code generation; RAG enables AI to reason over private codebase knowledge |
| 7 — Project Management | n8n / Airflow workflows can automate sprint rituals (standup summaries, backlog grooming triggers) |
| All steps | Engineers can now build new MCPs for any SDLC step — not just consume pre-built ones |

**Goals in this phase:**

| # | Goal | SDLC focus |
|---|---|---|
| 11 | LLM Fundamentals and Local Model Exploration | Implementation (cost reduction, private inference) |
| 12 | MCP Creation and Workflow Automation | All steps (custom integrations), Project Management |

---

## Phase 2 (Level 2) — Goals 13–14: AI Agent Engineer

**Objective:** Build autonomous AI agents and workflow automation capabilities.
**Role:** Replace human-in-the-loop steps with autonomous agents that reason, plan, and act across tools.

| SDLC Step | What Phase 2 adds |
|---|---|
| 1 — Requirement Gathering | Autonomous agents can interview stakeholders, parse documents, and produce structured PRDs with no human intervention |
| 2 — Work Item Creation | Agent reads PRD, creates and prioritises work items, assigns to the right queue — fully automated |
| 3 — Implementation | Multi-step agents implement a feature across multiple repos, verify it compiles, run tests, all in one session |
| 4 — PR Creation | Agent creates PR, self-reviews against AGENTS.md standards, requests reviewers |
| 7 — Project Management | Agents manage sprint boards: move items, send standup summaries, flag blockers |
| All steps | Cost and security awareness applied to every AI action — data exposure risk, token cost per operation |

**Goals in this phase:**

| # | Goal | SDLC focus |
|---|---|---|
| 13 | Autonomous Multi-Step Agent Development | All steps — end-to-end feature delivery without human handoffs |
| 14 | Cost and Security Awareness for LLM Usage | Cross-cutting — governs every AI operation across the SDLC |

---

## Phase 3 (Level 3) — Goals 15–16: AI Architect

**Objective:** Design and scale AI systems with advanced architecture capabilities.
**Role:** Infrastructure-level concerns — running LLMs at scale, securing agent systems, designing feedback loops.

| SDLC Step | What Phase 3 adds |
|---|---|
| 3 — Implementation | LLMs hosted on GPU infrastructure (vLLM, HuggingFace, Kubernetes) — no dependency on external providers |
| All steps | Agent security: prompt injection defence, sandboxing, access control for all SDLC agents |
| All steps | Agent learning: feedback loops, memory systems, and retraining strategies so SDLC agents improve over time |

**Goals in this phase:**

| # | Goal | SDLC focus |
|---|---|---|
| 15 | Scalable LLM Deployment Architecture | Implementation (self-hosted inference at scale) |
| 16 | Agent Security and Learning Mechanisms | Cross-cutting — governs security and improvement of all agents |

---

## Cross-Phase Coverage Map

| SDLC Step | Phase 0 | Phase 1 | Phase 2 | Phase 3 |
|---|---|---|---|---|
| 1 — Requirement Gathering | Partial (doc → work items) | Custom MCPs for external sources | Autonomous agent gathers & structures | — |
| 2 — Work Item Creation | ✅ Template + GitHub API | Richer extraction via RAG | Fully autonomous | — |
| 3 — Implementation | ✅ AI-assisted (AGENTS.md + skills) | Local LLMs, RAG over codebase | Autonomous multi-repo feature delivery | Self-hosted LLM inference at scale |
| 4 — Pull Request Creation | ✅ `/branch-and-pr` skill | — | Agent self-reviews and files PR | — |
| 5 — Testing | ✅ Playwright MCP + CI | — | Agent generates and runs test suite | — |
| 6 — Deployment | ✅ GitHub Actions CD | — | — | — |
| 7 — Project Management | Partial (work items only) | n8n / Airflow automation | Sprint management agents | — |
| 8 — Documentation | ✅ MCP doc server + arch docs | — | — | — |
| 9 — Team Metrics & Reports | ✅ Weekly GitHub API report | — | — | Metrics feed agent learning loops |

**Legend:** ✅ Fully addressed in this phase · Partial · — Not the primary focus of this phase
