# Goal 9: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. Walk through the deployment pipeline for `agency-gig-api` — what happens at each step?**

> 1. **CI trigger:** a push to `main` (or a merged PR) triggers the GitHub Actions workflow.
> 2. **Build & test:** the .NET project is built and unit/integration tests are run. If any fail, the pipeline stops.
> 3. **Docker build:** a Docker image is built from the `Dockerfile` in the repo.
> 4. **Push to GHCR:** the image is tagged (with the commit SHA and `latest`) and pushed to GitHub Container Registry.
> 5. **Deploy:** the target environment (e.g. a cloud VM or container service) pulls the new image and restarts the container.
> 6. **Health check:** the pipeline verifies the service is responding before marking the deployment successful.

---

**Q2. What's the difference between CI (continuous integration) and CD (continuous deployment)?**

> CI is the practice of automatically building and testing code every time a change is pushed — it validates that the new code integrates correctly with what already exists. CD extends this by automatically deploying passing builds to one or more environments without manual intervention. CI answers "does this change break anything?"; CD answers "is this change now running in production?". You can have CI without CD (manual deploy gate), but CD requires CI to be in place first.

---

**Q3. How are secrets (tokens, credentials) stored and used in the GitHub Actions workflows?**

> Secrets are stored in GitHub's encrypted secrets store, accessible under the repo or org settings. In the workflow YAML they're referenced as `${{ secrets.SECRET_NAME }}` — GitHub injects the value at runtime and masks it in logs. They're never committed to the repository. For GHCR authentication, the `GITHUB_TOKEN` is used (automatically provided by Actions); for external services, secrets are added manually and rotated as needed.

---

**Q4. A deployment to GHCR fails. Where do you look first, and what are the likely causes?**

> First check the GitHub Actions workflow run log — the failing step will be highlighted with the error output. Likely causes: authentication failure (token expired or missing `packages: write` permission), Docker build error (syntax in Dockerfile, missing dependency, build context too large), network timeout pushing the image, or an image tag conflict. If the build step passed and only the push failed, it's almost always an auth or permissions issue with the `GITHUB_TOKEN` scope.
