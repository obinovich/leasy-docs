# Core Technology Reference — Leasy

This file records the chosen core stack and actionable migration checklist so the three split repos (`leasy-frontend`, `leasy-backend`, `leasy-db`) follow a single, maintainable architecture.

## Tier 1 — Presentation
- Hosting: Vercel (Free Tier)
- Technology: Vite + TypeScript + React (or Vue)

Notes:
- Frontend repo: `leasy-frontend` — migrate to Vite + TypeScript if not already. Ensure `build` outputs to `dist` and `vercel.json` points to the correct `dist` directory.
- Use `REACT_APP_API_URL` (or `VITE_API_URL`) as the runtime API environment variable in Vercel.

## Tier 2 — Application
- Hosting: Google Cloud Run (Free Tier)
- Technology: Next.js packaged in a Docker container
- Execution model: Scale-to-Zero (Cloud Run)

Notes:
- Backend repo: `leasy-backend` — migrate API to Next.js (or host the Express API inside a Next.js custom server if you prefer). Build and package using `Dockerfile.backend` tuned for Next.js.
- Expose the service on port `8080` (Cloud Run default) and set `GCP_PROJECT`, `GCP_REGION`, and `GCP_SA_KEY` as secrets.

## Tier 3 — Data
- Hosting: Managed MongoDB (Atlas)
- Core: Managed database
- Schema management: Prisma ORM

Important: The project previously referenced Supabase/Postgres. If you require MongoDB instead, choose one of the two supported options below and keep the choice consistent across infra and docs.

- Option A — Supabase (Postgres) + Prisma
  - Use Supabase managed Postgres and `prisma` with `provider = "postgresql"` in `schema.prisma`.
  - Ideal if you want built-in auth, storage, and serverless Postgres.

- Option B — Managed MongoDB (e.g. MongoDB Atlas) + Prisma
  - Use a managed MongoDB cluster and `prisma` with `provider = "mongodb"` in `schema.prisma`.
  - Good if your data model is document-oriented and you wish to keep MongoDB compatibility.

## Migration checklist (high level)
1. Pick Tier-3 provider (Supabase/Postgres OR MongoDB Atlas). Update `prisma/schema.prisma` accordingly.
2. Frontend: migrate `leasy-frontend` to Vite + TypeScript (create `vite.config.ts`, add `tsconfig.json`, convert top-level files). Update `vercel.json` if needed.
3. Backend: convert or repackage backend to Next.js and adapt API routes (or containerize existing Express app behind Cloud Run). Ensure `Dockerfile.backend` builds the Next.js app and exposes port `8080`.
4. CI: add `.github/workflows/ci.yml` from `.github_workflow_templates/` into each repo and create PRs.
5. Secrets & deploy: add GitHub Secrets (`VERCEL_TOKEN`, `GCP_SA_KEY`, `DATABASE_URL`, etc.) and configure Vercel/Cloud Run/Supabase dashboards.
6. Run smoke tests: `npm ci && npm run build` (frontend), `npm ci && npm run build && docker build` (backend), `npx prisma migrate` (data) in staging.

## Quick commands reference
Frontend (Vite):
```bash
cd leasy-frontend
npm ci
npm run build
```

Backend (Next.js Docker):
```bash
cd leasy-backend
npm ci
npm run build    # Next build
docker build -t leasy-backend:staging -f ../Dockerfile.backend .
```

Data (Prisma):
```bash
cd leasy-db
npm ci
# set DATABASE_URL in .env
npx prisma migrate deploy
```

If you'd like, I can start migrating one repo now (frontend → Vite+TS, backend → Next.js Docker, or initialize Prisma for the data repo). Tell me which to start with.
