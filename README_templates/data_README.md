# Data (database & infra)

This repo contains migrations, seeds, and DB related scripts.

Bootstrap local DB:

```bash
# Example (postgres):
psql -h localhost -U postgres -f migrations/init.sql
# or run provided scripts in this repo
```

Notes:
- Keep credentials in CI/secret manager; never commit secrets.
