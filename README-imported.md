# leasy-dev — split workspace for leasy

This folder contains scripts and documentation to split the mono-repo at `/home/o_oka/project/leasy` into three repositories following a 3-tier architecture:

- presentation — frontend/UI/public assets
- application — backend/API and business logic
- data — database migrations, seeds, and DB infra

Files:
- `split-filter-repo.sh` — primary recommended script using `git-filter-repo`.
- `split-subtree.sh` — alternative using `git subtree split`.
- `post_split_checklist.md` — verification steps after splitting.
- `rollback_plan.md` — rollback strategy.
- `migration_summary_template.md` — template to record moved files and commit ranges.

How to use (high-level):
1. Inspect `split-filter-repo.sh` and update included paths as needed.
2. Ensure `git-filter-repo` is installed.
3. Run the script from a safe place (it clones, doesn't modify original repo).
4. Inspect and push each generated repo to your chosen remotes.

Always test in CI/staging before updating production deployment.
