# Discussion: Roadmap Gap Review

**Date:** 2026-03-04
**Participants:** Hussein (Engineering Manager), Peter (Tech Lead)
**Subject:** Review of `integrant-roadmap.md` — identify gaps, conflicts, and misalignments before goal definition begins
**Status:** Resolved — 10 agreed changes applied to roadmap

---

## Context

The Engineering Manager authored the roadmap (`integrant-roadmap.md`). Before the Tech Lead breaks it into SMART goals, both need to be aligned that the roadmap is internally consistent, correctly sequenced, and complete. This discussion surfaces and resolves all gaps.

---

## Discussion

**Peter:** I've gone through the roadmap. There's real structure here, but I've got issues before this goes to the team. Most glaring: Phase 3 says "Learn Python." But Phase 2 already has you creating MCPs and automating workflows with n8n or Airflow. And Level 0 has MCP setup in the training outline. You can't build MCPs without Python. So either Python is a prerequisite for the whole plan, or the phases are sequenced wrong.

**Hussein:** Fair. I listed Python in Phase 3 because that's where it becomes critical — LangChain, CrewAI. But you're right that Phase 2 assumes it implicitly. I'll move Python fundamentals to Phase 2 as an explicit starting point.

**Peter:** Related — Phase 2 says "Create MCPs" as a new skill. But Level 0 Training Items 5 and 7 already use MCPs. You're expecting engineers to use MCPs before you've taught them to build them.

**Hussein:** The assumption in Level 0 is they're consuming pre-built MCPs, not creating them. Phase 2 is where they build their own. I'll make that distinction explicit in both places.

**Peter:** Deployment. It's in the Level 0 process flow, but there's no training item or SMART goal covering it. We have work item creation, branching, testing — but code never actually ships. The SDLC loop is open at the end.

**Hussein:** That's a real gap. Deployment automation should be Training Item 9. CD pipelines for all three repos — GitHub Actions, Docker, Vercel, GitHub Packages. I'll add it.

**Peter:** Same for Metrics. The roadmap mentions engineering health but there's nothing concrete. How does the EM know whether Phase 0 is working?

**Hussein:** Add Team Metrics & Reports as Training Item 10. Weekly automated report from GitHub API — PR cycle time, deployment frequency, CI pass rate. That closes the feedback loop.

**Peter:** Level 0 has a goal around "maximizing AI tool usage" but the methodology names specific tools: GitHub Copilot, Claude Code, Cursor. If a team member is on a different tool, they think the goal doesn't apply.

**Hussein:** Rewrite the Level 0 goal as tool-agnostic. The methodology is: structured prompts + AGENTS.md + skills. The tools are examples, not requirements.

**Peter:** The roadmap refers to "Antigravity" as an AI tool in one section. I assume that's a placeholder or a typo.

**Hussein:** Replace with GitHub Copilot — that's what it was meant to say.

**Peter:** Data engineers aren't mentioned anywhere. The roadmap reads as if the whole team is software engineers. Do we have a data track?

**Hussein:** Add a note in the Overview: data engineers follow a parallel track with focus on dbt, Spark, and data pipeline tools. Same AI principles, different domain context.

**Peter:** AI Security isn't covered at all. After adding MCP servers and embedding credentials in GitHub Secrets, security hygiene needs to be a Level 0 focus area — not something introduced in Phase 3.

**Hussein:** Agree. Add AI Security Basics to Level 0 Training Focus Areas: prompt injection awareness, secret management, no credentials in LLM context.

**Peter:** The phases have no completion criteria. How do we know when a phase is done?

**Hussein:** Add Completion Criteria to each phase. For Level 0: all SMART goals delivered, team demo completed, and all CI pipelines green.

**Peter:** Phase 4 is Architect level. Most engineers won't reach it. Is this expected for everyone or is it optional?

**Hussein:** Optional track. Add an audience statement to Phase 4: senior engineers and architects only. The rest of the team completes through Phase 3.

---

## Decisions Made

| # | Issue | Resolution |
|---|---|---|
| 1 | Python sequenced too late | Moved to Phase 2 as first item + prerequisite note |
| 2 | MCP authoring vs. usage distinction missing | Level 0 = consuming pre-built MCPs; Phase 2 = authoring |
| 3 | Deployment missing from Phase 0 | Added as Training Item 9: Automated Deployment Pipelines |
| 4 | Team Metrics missing from Phase 0 | Added as Training Item 10: AI-Generated Team Metrics and Reports |
| 5 | Level 0 goal tied to specific tools | Rewritten as tool-agnostic; tools listed as examples |
| 6 | "Antigravity" placeholder | Replaced with GitHub Copilot |
| 7 | Data engineers not mentioned | Parallel track note added to Overview |
| 8 | AI Security absent from Phase 0 | Added to Level 0 Training Focus Areas |
| 9 | No completion criteria per phase | Completion Criteria added to all 4 phases |
| 10 | Phase 4 scope unclear | Audience statement added: optional for senior/architect role |

---

## Outcome

All 10 issues resolved. Roadmap updated immediately after this discussion. TL cleared to begin SMART goal definition for Phase 0.
