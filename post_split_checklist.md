# Post-split checklist

- Inspect each new repo under `/home/o_oka/project/leasy-dev`:
  - `presentation` (frontend)
  - `application` (backend)
  - `data` (db/migrations)

- For each repo:
  - Open and review `package.json` / dependency files.
  - Run `npm install` (or language-specific) and unit tests.
  - Ensure `.env` values are set in CI/secrets (do not commit secrets).

- Push to new remote repos:
  ```bash
  cd /home/o_oka/project/leasy-dev/presentation
  git remote add origin <presentation-remote-url>
  git push -u origin main
  ```

- Update CI/CD pipelines to trigger on the new repos and update any deployment manifests to reference new image sources and repo URLs.

- Integration smoke test (example):
  - Start `data` migrations/DB locally or in test infra.
  - Start `application` server and point it to the `data` DB.
  - Start `presentation` (npm start / build) and confirm it can call the `application` API.

- Verify logs, endpoints, and basic user flows.
