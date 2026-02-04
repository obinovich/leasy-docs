# Rollback plan

If issues occur after the split or push:

1. Do not delete the original mono-repo at `/home/o_oka/project/leasy` — it remains the source of truth.
2. Revert any CI/CD changes that started deploying from the new repos.
3. If a new repo needs to be discarded, simply remove the repo or unpublish the remote, and continue using the mono-repo.
4. If history was accidentally altered, re-create the split by re-running the split scripts (they operate from the original repo/mirror).
5. If tags/releases need preservation, re-create tags in the new repos by mapping original commits (see `migration_summary_template.md` to aid this).

Note: Keep backups of the created repo directories under `/home/o_oka/project/leasy-dev` until you are confident the new repos are stable.
