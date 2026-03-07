# Goal 8: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What is `@agency/skills`? How is it different from having skill files in `.claude/skills/`?**

> `@agency/skills` is an npm package that contains the team's shared Claude Code skill files. When installed, it copies the skill files into the consuming repo's `.claude/skills/` directory. The difference from committing skills directly to each repo is centralisation — a single source of truth that all repos install as a dependency. Updates are published as a new version and repos opt in by bumping the version, rather than manually copying files across repos.

---

**Q2. How do you add a new skill to the package? Walk through the steps.**

> 1. Create the new skill file in the `skills/` directory of the `@agency/skills` package repo.
> 2. Test it locally by linking the package (`npm link`) and invoking the skill in a test repo.
> 3. Update the package version (following semver — patch for small additions, minor for new skills).
> 4. Commit, push, and publish to npm (or the private registry in use).
> 5. Update each consuming repo's `package.json` to reference the new version and re-run install.

---

**Q3. What's the advantage of publishing skills as an npm package vs committing them to each repo?**

> A single update to the package propagates to all repos — there's no risk of repos drifting out of sync with different versions of the same skill. It also enforces intentional adoption: repos upgrade explicitly, so a breaking change to a skill doesn't silently affect everyone. The skills are versioned, auditable, and can be reviewed in one place rather than scattered across 10 repos.

---

**Q4. Name 3 skills currently in the package and describe what each one does.**

> 1. **`/create-issues`** — reads a requirements document and creates structured GitHub Issues for each work item.
> 2. **`/branch-and-pr`** — creates a branch following the team naming convention and opens a PR with the standard description template.
> 3. **`/generate-release-notes`** — summarises merged PRs since the last tag and formats them as release notes grouped by type.
