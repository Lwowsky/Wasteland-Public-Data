# Wasteland public JSON (separate GitHub Pages repo)

Contains **only** the export workflow, not the React website.

1. Create a new **public** repository (suggested `Lwowsky/Wasteland-Public-Data`).
2. Copy `.github/workflows/publish-data.yml` and this README into its default `main` branch.
3. Settings → Pages → Source: **GitHub Actions**.
4. Settings → Secrets and variables → Actions → Repository **secret**:
   - `PUBLIC_SNAPSHOT_EXPORT_TOKEN` — exact same token as the Cloudflare Worker.
5. Repository **variable**:
   - `PUBLIC_SNAPSHOT_EXPORT_URL` = `https://wasteland-king-api.vovapotaychuk.workers.dev`
6. Trigger **Run workflow** once to publish initial JSON.
7. Check `https://lwowsky.github.io/Wasteland-Public-Data/data/players.json` and `/data/summary.json`.

To launch automatically from changes:
- Create a **fine-grained** GitHub personal access token, repository access **only** to this new data repo; repository permission **Contents: Read and write** (needed for `repository_dispatch`). Set an expiration and plan to renew it.
- Save it to the Cloudflare Worker as secret `GITHUB_SNAPSHOT_DISPATCH_TOKEN`.
- Set a Worker plain environment variable `GITHUB_SNAPSHOT_REPOSITORY` = `Lwowsky/Wasteland-Public-Data`.
- Run remote D1 migration `0014_public_snapshot_sync.sql` and deploy the Worker with the new 1-minute Cron Trigger.

Security: JSON is fully public! Only the simplified display fields are exported. No admin table, passwords, emails, march/rally or extra squads.

If a dispatch fails, the dirty D1 revision remains pending and the Worker retries. If publication succeeds but acknowledgment fails, Worker retries a few minutes later.
