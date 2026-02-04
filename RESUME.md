# Resume guide — leasy-dev

Purpose: quick reference so you (or another engineer) can resume the repo-splitting, CI setup, and deployment work in a fresh VS Code instance.

---

Status snapshot (what I did)

- Created the split workspace at `/home/o_oka/project/leasy-dev`.
- Split the mono-repo into three local repositories using `git subtree` and created/pushed GitHub repos under `https://github.com/leasy-platform`:
  - `leasy-frontend` (presentation) — pushed to https://github.com/leasy-platform/leasy-frontend
  - `leasy-backend` (application) — pushed to https://github.com/leasy-platform/leasy-backend
  - `leasy-db` (data) — pushed to https://github.com/leasy-platform/leasy-db
- Files & artifacts created in `/home/o_oka/project/leasy-dev`:
  - `split-subtree.sh`, `split-filter-repo.sh`
  - `README.md`, `migration_summary_template.md`, `post_split_checklist.md`, `rollback_plan.md`, `production_plan.md`, `prisma_notes.md`
  - CI templates: `.github_workflow_templates/frontend_ci.yml`, `backend_ci.yml`, `data_ci.yml`
  - `Dockerfile.backend`, `vercel.json`
  - README templates under `README_templates/`
- Created the three GitHub repos using the authenticated `gh` CLI and pushed the split branches as `main`.

What remains / pending (high priority)

- Run smoke tests (install dependencies, run unit tests) for each new repo locally and in CI.
- Populate `migration_summary_template.md` with exact moved file lists and commit ranges (I can generate this for you if you want).
- Add the CI workflow files and Dockerfile into the new repo roots and create PRs (the templates live in `leasy-dev/.github_workflow_templates/`).
- Configure GitHub Secrets and deployment settings for each repo (Vercel, GCP, Supabase credentials).
- Configure Cloud Run service settings (region, concurrency, min-instances) and Container Registry details.
- Final integration testing across repos (frontend → backend → DB) and deploy to staging.

Prerequisites for resuming in a new VS Code instance

- git installed
- GitHub CLI (`gh`) installed and authenticated (`gh auth login`)
- (Optional) `git-filter-repo` if you plan to use `split-filter-repo.sh` instead of `split-subtree.sh`
- Docker locally if you want to build backend images for smoke tests
- Node.js (v18 recommended) and npm/yarn for frontend/backend local runs

Quick commands: clone the `leasy-dev` workspace (or open local copy)

If you want to start from the files produced here (recommended):

```bash
# Option A: open the existing workspace on this machine
code /home/o_oka/project/leasy-dev

# Option B: clone leasy-backend to a fresh machine for local work
git clone https://github.com/leasy-platform/leasy-backend.git
git clone https://github.com/leasy-platform/leasy-frontend.git
git clone https://github.com/leasy-platform/leasy-db.git
```

Re-run split (if you need to regenerate splits)

```bash
# From a safe location, with git-filter-repo installed
cd /tmp
git clone --bare /path/to/original/leasy /tmp/leasy.git
# Use the provided script; edit the path arrays if needed
/home/o_oka/project/leasy-dev/split-filter-repo.sh /path/to/original/leasy /home/o_oka/project/leasy-dev
# OR run subtree split (no extra install):
/home/o_oka/project/leasy-dev/split-subtree.sh /path/to/original/leasy /home/o_oka/project/leasy-dev
```

Push changes to GitHub (if you re-generate locally)

```bash
cd /home/o_oka/project/leasy-dev/presentation
git remote remove origin 2>/dev/null || true
git remote add origin https://github.com/leasy-platform/leasy-frontend.git
# Use gh token or gh auth already configured to avoid interactive prompts
TOKEN=$(gh auth token)
git push https://$TOKEN@github.com/leasy-platform/leasy-frontend.git main
unset TOKEN

# Repeat for application and data:
cd /home/o_oka/project/leasy-dev/application
# ... add remote https://github.com/leasy-platform/leasy-backend.git and push

cd /home/o_oka/project/leasy-dev/data
# ... add remote https://github.com/leasy-platform/leasy-db.git and push
```

Add CI templates and Dockerfile to new repos (create PRs)

```bash
# Example: create a branch in backend, add CI and Dockerfile, push and open PR
cd /home/o_oka/project/leasy-dev/application
git checkout -b add-ci-templates
cp ../.github_workflow_templates/backend_ci.yml .github/workflows/ci.yml
cp ../Dockerfile.backend Dockerfile
git add .github/Dockerfile .github/workflows/ci.yml Dockerfile
git commit -m "Add CI/CD and Dockerfile templates"
# Push and open PR
git push origin add-ci-templates
gh pr create --fill --base main --head add-ci-templates
```

Set required GitHub Secrets for each repo (via `gh` or web UI)

- `leasy-frontend`: `VERCEL_TOKEN` (if deploying from CI), `REACT_APP_API_URL`
- `leasy-backend`: `GCP_PROJECT`, `GCP_REGION`, `GCP_SA_KEY` (service account JSON), `DATABASE_URL`
- `leasy-db`: `DATABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`

Use `gh` to set secrets non-interactively:

```bash
# Example: store DATABASE_URL for leasy-db
gh repo set-secret leasy-platform/leasy-db DATABASE_URL --body "$DATABASE_URL"
```

Run smoke tests locally (examples)

```bash
# Backend (leasy-backend)
cd /home/o_oka/project/leasy-dev/application || cd leasy-backend
npm ci
npm test    # or `npm run dev` to start dev server

# Frontend (leasy-frontend)
cd /home/o_oka/project/leasy-dev/presentation || cd leasy-frontend
npm ci
npm start
# Set REACT_APP_API_URL in env before starting

# Data (migrations)
cd /home/o_oka/project/leasy-dev/data || cd leasy-db
npm ci
npx prisma migrate deploy --preview-feature
```

Generate `migration_summary_template.md` details (sample commands)

```bash
# From original mono-repo path
cd /path/to/original/leasy
# list commits affecting a path
git log --oneline --pretty=format:"%H %ad %s" --date=short -- frontend/ | head -n 5
# capture earliest and latest commits for reporting
FIRST=$(git rev-list --reverse HEAD -- frontend | head -n1)
LAST=$(git rev-list HEAD -- frontend | head -n1)
echo "frontend: $FIRST..$LAST" >> /home/o_oka/project/leasy-dev/migration_summary_template.md
```

Notes, safety & rollback

- Sensitive data: never commit `.env` or service account JSONs. Use GitHub Secrets / secret manager.
- Keep the original mono-repo as the fallback until decommissioned.
- If you accidentally push an unwanted repo, delete it from the GitHub UI or `gh repo delete`.
- Back up the DB before any migration that changes schemas.

Contact points and useful URLs

- New GitHub repo URLs:
  - https://github.com/leasy-platform/leasy-frontend
  - https://github.com/leasy-platform/leasy-backend
  - https://github.com/leasy-platform/leasy-db

Useful local paths in this machine (if you resume here)

- `/home/o_oka/project/leasy` — original mono-repo (left untouched)
- `/home/o_oka/project/leasy-dev` — split workspace and templates
- `/home/o_oka/project/leasy-dev/presentation` — local split presentation repo
- `/home/o_oka/project/leasy-dev/application` — local split application repo
- `/home/o_oka/project/leasy-dev/data` — local split data repo

If you want, I can now:
- Populate `migration_summary_template.md` with exact moved file lists and commit SHAs.
- Run smoke tests automatically and report failures.
- Create PRs in the new repos adding the CI templates and `README` files.

End of resume guide.
