# Goal 9: Automated Deployment Pipelines

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** DevOps Engineer (DRI); TPL (escalation)
**Target:** Week 9

## Description

Close the SDLC loop by automating deployment for all three Agency repos. Every merge to `main` triggers a GitHub Actions pipeline that builds, tests, and deploys to staging automatically — with zero manual steps. Deployment success or failure is visible directly in the GitHub Actions run.

## SMART Breakdown

- **Specific:** Configure GitHub Actions CD pipelines for `agency-gig-api` (Docker image → GitHub Container Registry), `agency-gig-web` (container or Vercel deploy), and `agency-gig-ui` (GitHub Packages publish on version bump). All secrets stored in GitHub Secrets with a runbook documenting rotation.
- **Measurable:** All 3 repos deploy to a staging environment automatically on merge to `main` with zero manual steps. First milestone: all 3 pipelines pass end-to-end in a single sprint. Ongoing: deployment success rate ≥ 95% per sprint.
- **Achievable:** GitHub Actions CI is already in place for `agency-gig-web` (Goal 7). Extend the pattern to `agency-gig-api` and `agency-gig-ui`. No new infrastructure tooling required — GitHub Actions + GHCR + GitHub Packages are all available.
- **Relevant:** Without CD, the SDLC automation chain is incomplete. Deployment is the final step from code to running software and is listed explicitly in the Phase 0 process flow.
- **Time-bound:** Complete by end of Week 9. Goal 7 (Playwright CI) completes Week 8 — CD builds directly on that foundation.

## Deliverables

- [ ] `agency-gig-api`: Dockerfile + GitHub Actions CD — builds image, pushes to GHCR, deploys to staging
- [ ] `agency-gig-web`: GitHub Actions CD — builds and deploys to staging environment
- [ ] `agency-gig-ui`: GitHub Actions publish workflow — publishes to GitHub Packages on version bump
- [ ] All deploy secrets (`GHCR_TOKEN`, `DEPLOY_KEY`, `STAGING_*`) stored in GitHub Secrets
- [ ] `docs/deployment-runbook.md` in `agency-workspace` — secrets rotation, rollback procedure, environment variables
- [ ] Branch protection updated: merge to `main` requires passing deployment pipeline on staging
- [ ] Team walkthrough: DevOps demonstrates full deploy flow from PR merge to live staging

## Acceptance Criteria

- Merging a PR to `main` in any Agency repo triggers the deployment pipeline without any manual action.
- Staging environment reflects the merged change within 5 minutes of merge.
- Failed deployments block future merges until resolved (branch protection rule active).
- All secrets are in GitHub Secrets — no credentials in code, workflow YAML, or `.env` files committed.
