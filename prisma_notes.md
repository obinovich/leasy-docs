```markdown
# Prisma notes: Postgres (Supabase) and MongoDB (Atlas) options

This project uses Prisma as the schema-management tool. You can target either a managed Postgres (Supabase) or a managed MongoDB (Atlas) database. Pick one provider and keep it consistent across infra, CI, and documentation.

1) Choose provider
- Supabase (Postgres): set `provider = "postgresql"` in `prisma/schema.prisma`.
- Managed MongoDB (Atlas): set `provider = "mongodb"` in `prisma/schema.prisma`.

2) Initialize provider
- Supabase: create a Supabase project and copy `DATABASE_URL` and `SERVICE_ROLE_KEY` into GitHub Secrets for `leasy-db`.
- MongoDB Atlas: create a cluster, whitelist required IPs (or use VPC peering), and copy the `MONGODB_URI` into GitHub Secrets.

3) Prisma setup
- Ensure `prisma/schema.prisma` exists in `leasy-db` and matches the chosen provider.
- Example Postgres URL: `postgresql://postgres:password@db.host:5432/postgres`.
- Example MongoDB URL: `mongodb+srv://<user>:<password>@cluster0.mongodb.net/mydb?retryWrites=true&w=majority`.

4) Local development
- Create a `.env` with `DATABASE_URL` (Postgres) or `MONGO_URI`/`DATABASE_URL` (MongoDB) for local dev (do not commit).
- For Postgres: use `npx prisma migrate dev` to create migrations locally.
- For MongoDB: Prisma's migration story is different; use `prisma db push` to sync schema or follow Prisma docs for MongoDB workflows.

5) CI & deployment
- Use `npx prisma migrate deploy` (Postgres) in CI to apply migrations to staging/production.
- For MongoDB, use `prisma db push` or CI scripts per Prisma's MongoDB recommendations.

6) Backups & safety
- Postgres: back up via Supabase UI or `pg_dump` before destructive changes.
- MongoDB: take scheduled snapshots in Atlas or use `mongodump` for backups.

7) Serverless considerations
- For serverless (Cloud Run) with Postgres, use connection pooling (PgBouncer or Supabase pooling) to avoid exceeding connection limits.
- For MongoDB Atlas, use the recommended connection string options and consider connection pool sizing per serverless instance.

``` 
