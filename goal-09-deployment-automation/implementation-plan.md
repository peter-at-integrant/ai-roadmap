# Implementation Plan — Goal 9: Automated Deployment Pipelines

**Goal:** Automate deployment for all three Agency repos so every merge to `main` triggers a full build-test-deploy cycle to staging with zero manual steps.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** DevOps Engineer
**Target:** Week 9

---

## Approach

Extend the existing GitHub Actions CI pattern (established in Goal 7 for `agency-gig-web`) to cover all three repos. `agency-gig-api` gets a Dockerfile and a GHCR push + staging deploy workflow. `agency-gig-web` gets a staging deploy step appended to its existing CI workflow. `agency-gig-ui` gets a GitHub Packages publish workflow triggered on version bump. All secrets live in GitHub Secrets. A deployment runbook covers rotation and rollback.

---

## Step-by-Step Implementation

### Step 1 — Dockerfile for `agency-gig-api`

Create `agency-gig-api/Dockerfile`:

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
WORKDIR /src
COPY ["AgencyGigApi.csproj", "."]
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish .
EXPOSE 8080
ENV ASPNETCORE_URLS=http://+:8080
ENTRYPOINT ["dotnet", "AgencyGigApi.dll"]
```

Add `.dockerignore`:
```
**/bin/
**/obj/
**/.vs/
```

### Step 2 — CD workflow for `agency-gig-api`

Create `.github/workflows/cd.yml` in `agency-gig-api`:

```yaml
name: CD — Build & Deploy API

on:
  push:
    branches: [main]

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - name: Log in to GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ghcr.io/peter-at-integrant/agency-gig-api:${{ github.sha }}

  deploy-staging:
    needs: build-and-push
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to staging
        run: |
          curl -X POST "${{ secrets.STAGING_DEPLOY_URL }}" \
            -H "Authorization: Bearer ${{ secrets.DEPLOY_KEY }}" \
            -d '{"image":"ghcr.io/peter-at-integrant/agency-gig-api:${{ github.sha }}"}'
```

### Step 3 — CD workflow for `agency-gig-web`

Append a `deploy-staging` job to the existing `.github/workflows/ci.yml` in `agency-gig-web`:

```yaml
  deploy-staging:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Vercel staging
        run: npx vercel --token=${{ secrets.VERCEL_TOKEN }} --env=staging
        env:
          VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
          VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

### Step 4 — Publish workflow for `agency-gig-ui`

Create `.github/workflows/publish.yml` in `agency-gig-ui`:

```yaml
name: Publish — GitHub Packages

on:
  push:
    tags:
      - 'v*'

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          registry-url: 'https://npm.pkg.github.com'
          scope: '@peter-at-integrant'

      - run: npm ci
      - run: npm run build
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Step 5 — Store secrets in GitHub Secrets

Required secrets per repo:

| Secret | Repo | Description |
|---|---|---|
| `STAGING_DEPLOY_URL` | `agency-gig-api` | Staging webhook URL |
| `DEPLOY_KEY` | `agency-gig-api` | Bearer token for staging deploy endpoint |
| `VERCEL_TOKEN` | `agency-gig-web` | Vercel deploy token |
| `VERCEL_ORG_ID` | `agency-gig-web` | Vercel organisation ID |
| `VERCEL_PROJECT_ID` | `agency-gig-web` | Vercel project ID |

`GITHUB_TOKEN` is automatically provided by Actions — no manual setup required for GHCR or GitHub Packages publishing.

Add via GitHub CLI:
```bash
gh secret set STAGING_DEPLOY_URL --repo peter-at-integrant/agency-gig-api
gh secret set DEPLOY_KEY          --repo peter-at-integrant/agency-gig-api
gh secret set VERCEL_TOKEN        --repo peter-at-integrant/agency-gig-web
```

### Step 6 — Deployment runbook

Create `docs/deployment-runbook.md` in `agency-workspace`:

```markdown
# Deployment Runbook

## Environments
| Repo | Target | URL |
|---|---|---|
| agency-gig-api | Staging container | $STAGING_API_URL |
| agency-gig-web | Vercel staging | $VERCEL_STAGING_URL |
| agency-gig-ui | GitHub Packages | npm.pkg.github.com |

## Secrets Rotation
1. Generate new token/credential
2. Update via: `gh secret set <SECRET_NAME> --repo peter-at-integrant/<repo>`
3. Verify next workflow run succeeds

## Rollback Procedure (agency-gig-api)
```bash
# Re-deploy a previous image
curl -X POST "$STAGING_DEPLOY_URL" \
  -H "Authorization: Bearer $DEPLOY_KEY" \
  -d '{"image":"ghcr.io/peter-at-integrant/agency-gig-api:<previous-sha>"}'
```

## Failed Deployment
- Check Actions run logs in GitHub
- All failures block future merges (branch protection)
- Fix the root cause; do NOT delete the failing run
```

### Step 7 — Branch protection update

For each repo, add the CD workflow as a required status check:

```bash
gh api repos/peter-at-integrant/agency-gig-api/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["deploy-staging"]}'
```

### Step 8 — Team walkthrough

30-minute session:
1. Show a PR merge triggering the CD pipeline in real time
2. Walk through the workflow YAML — explain each job and secret reference
3. Demonstrate rollback by re-deploying a previous SHA
4. Show how branch protection blocks a merge when `deploy-staging` fails

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| GitHub Actions | CD pipeline orchestration |
| Docker + GHCR | `agency-gig-api` image build and registry |
| Vercel CLI | `agency-gig-web` staging deploy |
| GitHub Packages (npm) | `agency-gig-ui` package publishing |
| GitHub Secrets | Credential storage |
| `gh` CLI | Secret setup and branch protection scripting |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- Docker multi-stage build authoring for .NET 9
- GitHub Actions job chaining (`needs:`) and conditional execution
- GHCR authentication using `GITHUB_TOKEN` (no PAT required)
- GitHub Packages npm publishing with scoped registry config
- Secrets management: no credentials in workflow YAML literals
- Branch protection as a CD quality gate

---

## Demo Scenario

> DevOps merges a hotfix PR to `main` in `agency-gig-api`.
> GitHub Actions triggers automatically: builds Docker image → pushes to GHCR → deploys to staging.
> Staging reflects the change within 5 minutes.
> Branch protection shows ✅ `deploy-staging` passed on the PR.
> EM checks GitHub Actions — zero manual steps taken by the team.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Staging environment not yet provisioned | Use a stub deploy endpoint initially; replace with real target when infra is ready |
| VERCEL_TOKEN expiry | Document rotation cadence in runbook; set GitHub secret expiry reminder |
| Docker build time slows pipeline | Layer caching via `actions/cache` on `/root/.nuget/packages` and Docker layer cache |
| Secrets leak in logs | Never `echo` secrets; use `${{ secrets.* }}` interpolation only in `env:` or action inputs |
