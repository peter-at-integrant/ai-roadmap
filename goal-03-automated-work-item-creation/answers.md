# Goal 3: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What is a skill in Claude Code? How is it different from a prompt you type directly?**

> A skill is a reusable, named prompt stored as a markdown file in `.claude/skills/`. You invoke it with a `/` command (e.g. `/create-issues`). The difference from a direct prompt is repeatability and consistency — a skill encodes a workflow once and any team member can run it identically. Direct prompts are ephemeral and vary between people and sessions; skills are versioned, reviewable, and shared.

---

**Q2. Walk through how a requirements document gets converted to GitHub Issues — what are the steps and tools involved?**

> 1. A developer runs the `/create-issues` skill, pointing it at a requirements document.
> 2. Claude Code reads the document and extracts discrete, actionable work items.
> 3. For each work item it formats a GitHub Issue with title, description, acceptance criteria, and labels.
> 4. The `gh` CLI (GitHub CLI) is used to create each issue against the correct repo.
> 5. The developer reviews the created issues and adjusts any that need clarification before assigning them.

---

**Q3. What information must each work item contain to be actionable for a developer?**

> Title (clear and specific), description (what needs to be done and why), acceptance criteria (how to know it's done), relevant labels (area, priority, type), and any known dependencies or blockers. Without acceptance criteria in particular, a developer can't confidently close the issue — they don't know what "done" looks like.

---

**Q4. What does the automation get wrong or miss? What would you check manually before importing?**

> The automation can split tasks at the wrong boundary (too granular or too broad), miss implicit dependencies between issues, assign incorrect labels, or extract requirements that are actually design decisions not yet finalised. Before importing: verify each issue has clear acceptance criteria, check that dependency ordering makes sense, confirm nothing is scoped to work that hasn't been agreed on yet, and remove any duplicates the model may have created from similar-sounding requirements.
