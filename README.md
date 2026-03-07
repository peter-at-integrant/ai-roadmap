# AI Adoption Roadmap

**Created:** 2026-03-04
**Status:** Draft — Phase 0 complete; Phases 1–3 in progress
**Source:** [integrant-roadmap.md](./integrant-roadmap.md)
This repo tracks a structured, self-development programme for adopting AI across the full software development lifecycle. It documents 16 SMART goals across 4 progressive phases, with implementation plans, alignment discussions, and a live progress tracker.

The goals are demonstrated against the [Agency workspace](https://github.com/peter-at-integrant/agency-workspace) — a multi-repo band & venue booking platform ([project brief](https://github.com/peter-at-integrant/agency-workspace/blob/main/agency-project-brief.md)).

**Progress:** [Implementation Progress](./progress.md)
**Achievements:** [What was built + how to use it](./achievements.md)
**SDLC Coverage:** [Combined Coverage Overview](./sdlc-coverage-overview.md)
**Discussions:** [EM / TL Alignment Discussions](./discussions/README.md)

## Overview

Each goal has its own directory containing:
- The SMART goal definition (`README.md` inside the folder)
- An **implementation plan** (`implementation-plan.md`) — a concrete technical proposal demonstrating how to achieve the goal

## Goal Index

### Phase 1: Level 0 — Maximizing AI Tool Usage

| # | Goal | Implementation Plan | Owner | Target |
|---|---|---|---|---|
| 1 | [Multi-Repository Root Setup](./goal-01-multi-repo-root-setup/) | [Plan](./goal-01-multi-repo-root-setup/implementation-plan.md) | DevOps / TPL | Week 2 |
| 2 | [AGENTS.md as Tool-Agnostic Source of Truth](./goal-02-agents-md-source-of-truth/) | [Plan](./goal-02-agents-md-source-of-truth/implementation-plan.md) | All Teams | Week 4 |
| 3 | [Automated Work Item Creation](./goal-03-automated-work-item-creation/) | [Plan](./goal-03-automated-work-item-creation/implementation-plan.md) | PO / TPL | Week 5 |
| 4 | [Project Architecture and Standards Context](./goal-04-architecture-standards-context/) | [Plan](./goal-04-architecture-standards-context/implementation-plan.md) | TPL / Dev | Week 7 |
| 5 | [Business Documentation Automation via MCPs](./goal-05-business-doc-automation-mcp/) | [Plan](./goal-05-business-doc-automation-mcp/implementation-plan.md) | DevOps / Dev | Week 6 |
| 6 | [Automated Branching and PR Creation](./goal-06-automated-branching-pr-creation/) | [Plan](./goal-06-automated-branching-pr-creation/implementation-plan.md) | Dev | Week 6 |
| 7 | [Automated Testing with Playwright MCP](./goal-07-playwright-mcp-testing/) | [Plan](./goal-07-playwright-mcp-testing/implementation-plan.md) | SDET | Week 8 |
| 8 | [Skills-Based Guideline Encapsulation](./goal-08-skills-based-guideline-encapsulation/) | [Plan](./goal-08-skills-based-guideline-encapsulation/implementation-plan.md) | TPL | Week 6 |
| 9 | [Automated Deployment Pipelines](./goal-09-deployment-automation/) | [Plan](./goal-09-deployment-automation/implementation-plan.md) | DevOps | Week 9 |
| 10 | [Team Metrics & Engineering Health Reports](./goal-10-team-metrics-reports/) | [Plan](./goal-10-team-metrics-reports/implementation-plan.md) | EM / TPL | Week 10 |

### Phase 2: Level 1 — AI Integration Engineer

| # | Goal | Owner | Target |
|---|---|---|---|
| 11 | [LLM Fundamentals and Local Model Exploration](./goal-09-llm-fundamentals-local-models/) | All Engineers | Month 2 |
| 12 | [MCP Creation and Workflow Automation](./goal-10-mcp-creation-workflow-automation/) | Dev / DevOps | Month 3 |

### Phase 3: Level 2 — AI Agent Engineer

| # | Goal | Owner | Target |
|---|---|---|---|
| 13 | [Autonomous Multi-Step Agent Development](./goal-11-autonomous-agent-development/) | Dev | Month 5 |
| 14 | [Cost and Security Awareness for LLM Usage](./goal-12-cost-security-awareness/) | TPL / Security | Month 5 |

### Phase 4: Level 3 — AI Architect

| # | Goal | Owner | Target |
|---|---|---|---|
| 15 | [Scalable LLM Deployment Architecture](./goal-13-scalable-llm-deployment-architecture/) | Architect | Month 9 |
| 16 | [Agent Security and Learning Mechanisms](./goal-14-agent-security-learning-mechanisms/) | Architect / Security | Month 9 |

## Related Repos

| Repo | Purpose |
|---|---|
| [agency-workspace](https://github.com/peter-at-integrant/agency-workspace) | Dev environment — MCP servers, skills, scripts, workspace config + submodule links |
| [agency-gig-api](https://github.com/peter-at-integrant/agency-gig-api) | Backend REST API (.NET 9, EF Core, PostgreSQL) |
| [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web) | Next.js frontend |
| [agency-gig-ui](https://github.com/peter-at-integrant/agency-gig-ui) | Shared component library |
