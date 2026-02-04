# Production deployment plan (mapped to your 3-tier design)

Summary:
- Presentation: `leasy-frontend` → Vercel (Vite + TypeScript + React/Vue)
- Application: `leasy-backend` → Google Cloud Run (Next.js in Docker)
- Data: `leasy-db` → Supabase (Managed Postgres) + Prisma for schema/migrations

High-level steps and required secrets:

1) Presentation (`leasy-frontend`)
- CI: Run tests and build on push to `main` via GitHub Actions.
- Deployment: Use Vercel Git integration (recommended) or deploy via Vercel CLI in CI.
- Secrets/Env: `VERCEL_TOKEN` (if CI deploy), `REACT_APP_API_URL` (pointing to Cloud Run endpoint).
- Notes: Vercel auto-optimizes Vite/Next builds; configure environment variables in the Vercel dashboard.

2) Application (`leasy-backend`)
- CI: Build Docker image and push to Google Artifact Registry or GitHub Container Registry.
- Deploy: Use GitHub Actions to deploy to Cloud Run using `google-github-actions/deploy-cloud-run`.
- Secrets/Env: `GCP_PROJECT`, `GCP_SA_KEY` (base64 JSON service account with Cloud Run deploy and Artifact Registry permissions), `DATABASE_URL` (Supabase connection string), `SUPABASE_SERVICE_KEY` for privileged DB operations if needed.
- Execution model: Cloud Run set to scale-to-zero by default; set min-instances>0 for paid 24/7 uptime.

3) Data (`leasy-db`)
- DB: Use Supabase (managed Postgres). Create a project and copy `DATABASE_URL` and `SUPABASE_SERVICE_ROLE_KEY` to GitHub Secrets.
- Migrations: Use Prisma. Store `prisma/schema.prisma` in the repo; run `prisma migrate deploy` in CI after deploy or during a controlled migration step.
- Secrets/Env: `DATABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY` (store in repository secrets or in deployment secret manager).

Validation & Rollback:
- Use the `post_split_checklist.md` and `rollback_plan.md` in `/home/o_oka/project/leasy-dev`.
- Keep the mono-repo as the canonical fallback until decommission.
- Back up production DB before any destructive migrations.

Next immediate actions I can take now:
- Add CI workflow templates for each repo (GitHub Actions) and a `Dockerfile` template for the backend.
- Add `prisma_notes.md` explaining local dev, migrations, and Supabase connection steps.

Reply `go ahead` and I'll add the templates and Dockerfile to `/home/o_oka/project/leasy-dev`. Alternatively tell me any adjustments to the plan above.
