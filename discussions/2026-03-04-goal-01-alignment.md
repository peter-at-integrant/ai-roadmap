# Discussion: Goal 1 Alignment — Multi-Repository Root Setup

**Date:** 2026-03-04
**Participants:** Hussein (Engineering Manager), Peter (Tech Lead)
**Subject:** EM reviews Goal 1 implementation plan against the roadmap; aligns with TL on gaps
**Status:** Resolved — 10 agreed changes applied to Goal 1

---

## Context

The TL drafted Goal 1 (Multi-Repository Root Setup) and its implementation plan. The EM reviews it against the roadmap to ensure it is aligned, complete, and measurable. This is the first goal to go through the formal EM/TL alignment process.

---

## Discussion

**Hussein:** Let's start with the easy one. The roadmap's Tool Configuration focus area explicitly calls out MCP servers. I don't see MCP anywhere in this goal. What happened to it?

**Peter:** Intentional. MCP setup is a dependency for Goal 5 — business documentation automation. Goal 1 is workspace structure and multi-repo access. If I put MCP here, we're conflating two different kinds of infrastructure work and we blow the scope.

**Hussein:** My concern is the roadmap groups MCP alongside multi-repo access under Tool Configuration. That's this goal. If a developer finishes Goal 1 and gets to Goal 5 and has never touched MCP, that's a gap we didn't flag.

**Peter:** What I can do is add a forward reference — explicitly stating MCP is out of scope here and addressed in Goal 5. We're transparent about the handoff rather than silent about it.

**Hussein:** That works. Now — AGENTS.md. The roadmap names it in Context Management. Your implementation plan references it. But the goal file doesn't mention it. Goal 2 is AGENTS.md — is this a clean handoff?

**Peter:** Mostly. Goal 1 should set up the workspace so that when Goal 2 runs, the repos are already linked. But the AGENTS.md files themselves are Goal 2's territory.

**Hussein:** Then say that explicitly. The Deliverables for Goal 1 should state: AGENTS.md is out of scope, implemented in Goal 2.

**Peter:** Done.

**Hussein:** Roadmap Completion Criteria require a working demonstration for each training item. Goal 1 has no demo scenario in the goal file. There's technical steps but nothing that says "a developer sits down, does X, sees Y." Where's the demo?

**Peter:** I'll add a demo scenario: a new developer clones the workspace, runs `git submodule update --init --recursive`, opens the VS Code workspace, and Claude Code reads the CLAUDE.md and confirms it can see all three repos. That's a 60-second onboarding proof.

**Hussein:** Good. AI Security Basics is now a Level 0 focus area. Goal 1 clones all the repos into one root — that aggregates every leaked `.env`, hardcoded credential, or sensitive config into one place. Nothing in this goal addresses that.

**Peter:** Legitimate gap. I'll add a credential scan step — `git secret scan` or a pre-clone checklist — before any repo gets linked, and the setup guide will reference the AI Security Basics guidance.

**Hussein:** "All 10 team repositories" — named nowhere in the goal. Someone picking this up today has no idea which repos to link.

**Peter:** We've since agreed on the Agency project. The three repos are `agency-gig-api`, `agency-gig-web`, `agency-gig-ui`. I'll name them explicitly.

**Hussein:** The CI drift-prevention job is listed as a mitigation in the risk table but it's not in the Deliverables. Either it's a deliverable or it's not.

**Peter:** Make it a deliverable. A GitHub Actions workflow that runs daily, validates the workspace config hasn't drifted, and posts a Slack alert if it has.

**Hussein:** Two AI tools being validated simultaneously — Claude Code and Cursor — doubles the setup complexity and the failure surface. Is that in scope for Week 2?

**Peter:** Move Cursor config to a follow-up task. Goal 1 validates Claude Code only. Cursor setup is documented in the setup guide but not a delivery gate.

**Hussein:** Finally, the Measurable says "AI tool can access all repos." Access isn't measurable. What's the test?

**Peter:** Replace with: "The AI assistant can propose a cross-repo change — modifying the `BookingRequest` entity in `agency-gig-api` and the corresponding TypeScript type in `agency-gig-web` — in a single session without switching context." That's observable and binary.

**Hussein:** That's the kind of criterion that means something. Done.

---

## Decisions Made

| # | Issue | Resolution |
|---|---|---|
| 1 | MCP out of scope but not stated | Forward reference added: MCP is Goal 5 |
| 2 | AGENTS.md handoff unclear | Deliverables explicitly state AGENTS.md is Goal 2 |
| 3 | No demo scenario | Added: clone → submodule init → VS Code workspace → CLAUDE.md validation |
| 4 | No security consideration | Credential scan step added; setup guide references AI Security Basics |
| 5 | Repos unnamed | Explicitly named: `agency-gig-api`, `agency-gig-web`, `agency-gig-ui` |
| 6 | Drift-prevention job was a mitigation only | Promoted to deliverable: daily GitHub Actions workspace validation |
| 7 | Two AI tools = double validation risk | Cursor deferred; Claude Code is the Week 2 gate |
| 8 | "AI can access" not measurable | Replaced with: AI proposes cross-repo `BookingRequest` + TypeScript type change in single session |

---

## Outcome

Goal 1 file and implementation plan updated. EM sign-off given. TL cleared to proceed with implementation.
