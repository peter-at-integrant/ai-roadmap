# Goal 6: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What branch naming convention does the team use? Give an example for a feature, a fix, and a chore.**

> The convention is `<type>/<issue-number>-<short-description>`.
> - Feature: `feat/42-add-booking-confirmation-email`
> - Fix: `fix/67-venue-availability-timezone-bug`
> - Chore: `chore/91-update-ef-core-dependency`

---

**Q2. Walk through what `branch-and-pr.sh` does, step by step.**

> 1. Accepts a work item type, issue number, and description as arguments.
> 2. Constructs the branch name following the team convention.
> 3. Creates and checks out the new branch from the latest `main`.
> 4. Pushes the branch to the remote with tracking set.
> 5. Opens a PR using `gh pr create`, filling in the title and body from the PR description template.
> 6. Outputs the PR URL so the developer can navigate straight to it.

---

**Q3. Why automate PR creation rather than using the GitHub UI?**

> Consistency: every PR follows the same description template, has the right labels, and is linked to the correct issue — no one forgets to fill in the template or misnames the branch. Speed: one command replaces several manual steps. It also reduces the cognitive overhead of context-switching from code to browser and back. When the whole team uses the same script, PRs become predictable and easier to review.

---

**Q4. If the PR description template changes, what file do you update and where does it live?**

> The template lives in `.github/PULL_REQUEST_TEMPLATE.md` in the root repo (or within the individual service repo, depending on where the PR is raised). If the script also embeds the template as a heredoc, you'd update the corresponding section in `branch-and-pr.sh` as well. The two should stay in sync — the GitHub UI uses the `.github` template and the script uses its own copy.
