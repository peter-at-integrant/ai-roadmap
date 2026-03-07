# Achievements — Goal 9: Automated Deployment Pipelines

**Status:** 🔜 Pending
**Goal file:** [goal-09-deployment-automation.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

_Not yet implemented. This file will be updated when the goal is complete._

## What will be built

| File | Repo | Purpose |
|---|---|---|
| `Dockerfile` | [agency-gig-api](https://github.com/peter-at-integrant/agency-gig-api) | Multi-stage .NET 9 image build |
| `.github/workflows/cd.yml` | [agency-gig-api](https://github.com/peter-at-integrant/agency-gig-api) | Build → push to GHCR → deploy to staging |
| `.github/workflows/cd.yml` (deploy step) | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web) | Vercel staging deploy on merge to `main` |
| `.github/workflows/publish.yml` | [agency-gig-ui](https://github.com/peter-at-integrant/agency-gig-ui) | Publish to GitHub Packages on version tag |
| [`docs/deployment-runbook.md`](../../docs/deployment-runbook.md) | `agency-workspace` | Secrets rotation, rollback procedure, environment variables |

## How to verify (once implemented)

Merge any PR to `main` in any Agency repo — the staging environment should reflect the change within 5 minutes with no manual steps. A failed deploy should block the next merge via branch protection.
