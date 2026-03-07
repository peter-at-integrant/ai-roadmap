# Goal 1: Multi-Repository Root Setup

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** TPL
**Collaborators / Dependencies:** DevOps — required for GitHub repository access provisioning and permission setup
**Target:** Week 2

---

## Prerequisites

Before execution can begin:
- DevOps must provision read access to all 10 team repositories for the TPL and any contributing developers
- DevOps contact responsible for access provisioning must be confirmed before Week 1 ends
- If access is not fully provisioned by start of Week 2, the TPL must surface this as a risk immediately

---

## Description

Configure a root repository structure that allows AI tools to access and coordinate changes across all team repositories simultaneously, enabling cross-cutting feature implementation in a single session.

The workspace structure is **tool-agnostic** — it uses Git submodules and a VS Code multi-root workspace file (`.code-workspace`), which any AI tool supporting multi-root context can consume. Tool-specific configuration files are provided for **Cursor** and **Claude Code** as reference implementations; they are not the only supported tools.

> **Dependency — Goal 2:** Goal 1 configures the workspace to automatically consume `AGENTS.md` files once they exist (via Cursor context includes and Claude Code's `CLAUDE.md`). The creation and governance of `AGENTS.md` content is owned by Goal 2. These two goals must be sequenced accordingly.

> **Scope boundary — Goal 5:** MCP server configuration is **out of scope** for Goal 1. It is addressed in Goal 5 (Business Documentation Automation via MCPs).

---

## SMART Breakdown

- **Specific:** Set up a root repository using **Git submodules** as the primary linking mechanism for all 10 team repositories, combined with a VS Code multi-root workspace file (`.code-workspace`). Configure tool-specific AI context files for Cursor and Claude Code as reference implementations. The workspace pattern is designed to work with any AI tool that supports multi-root context.
- **Measurable:** An AI tool can successfully read files from and propose changes across **all 10 team repositories** in a single session without manual context switching.
- **Achievable:** Uses existing repo structure; requires only configuration files and workspace setup — no new infrastructure. Repository access provisioning by DevOps is a prerequisite (see above).
- **Relevant:** Eliminates manual context switching between repos for cross-cutting features, reducing developer handoff delays. Establishes the foundational workspace structure required by every subsequent Phase 0 goal.
- **Time-bound:** Complete by end of Week 2. Contingent on DevOps provisioning repo access by end of Week 1.

---

## Deliverables

- [ ] Root repository created and accessible to all team members
- [ ] All 10 team repositories linked as Git submodules under `services/`
- [ ] VS Code multi-root workspace file (`.code-workspace`) committed to root repo
- [ ] Claude Code `CLAUDE.md` context file committed to root repo (reference implementation)
- [ ] Cursor `.cursor/settings.json` context file committed to root repo (reference implementation)
- [ ] Validated: AI tool reads and proposes changes across all 10 repos in a single session
- [ ] Developer setup guide (`docs/workspace-guide.md`) committed and peer-reviewed
- [ ] **Out of scope — noted:** MCP server configuration → addressed in Goal 5
- [ ] **Out of scope — noted:** AGENTS.md content → addressed in Goal 2; workspace is pre-wired to consume it

---

## Acceptance Criteria

- A developer can open the root workspace in any AI tool that supports multi-root context and navigate to any of the 10 linked repositories without leaving the session.
- An AI tool can read files from and propose changes to **all 10 team repositories** in a single session.
- Tool-specific configuration files for Cursor and Claude Code are present, functional, and serve as reference implementations for onboarding other tools.
- Developer setup guide is present and reviewed by at least one other team member.
- All repository access permissions are confirmed working for all team members before sign-off.
