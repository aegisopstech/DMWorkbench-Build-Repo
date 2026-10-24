# DMWorkbench

CFO workbench for Diminishing Musharaka contracts: contract set-up, payment schedule, interim profit, income amortization, rental collection, contract reschedule, saved contracts and KIBOR update. It is a single HTML page with no build tooling beyond one PowerShell script.

## Two editions

| Edition | File | Storage |
| --- | --- | --- |
| Database | `workbench\Diminishing Musharaka_ CFO Workbench V2.1.html` (the source; edit this one) | Sign-in required; everything is saved in Supabase schema `dm` |
| Standalone | `workbench\standalone\... (standalone).html`, also written to `index.html` | Browser only, no network; saved in this browser, with Export and Import |

The standalone files are generated. Do not edit them by hand.

## Run the workbench locally

`.claude\launch.json` defines a `workbench` server on port 8765 that serves the `workbench` folder. Start it from the Claude Code preview, or run:

```bash
python -m http.server 8765 --bind 127.0.0.1 --directory workbench
```

Open `http://127.0.0.1:8765/Diminishing%20Musharaka_%20CFO%20Workbench%20V2.1.html`. The database edition shows a sign-in gate first.

## Build the standalone edition

```bash
powershell -File .\build_workbench.ps1
```

This writes the standalone file in `workbench\standalone` and an identical copy to `index.html` at the repo root. The script stops with an error if any expected marker is missing from the source.

## Database

Migrations `0001` to `0012` in `migrations` build the `dm` schema in the Supabase project (`0007` adds web API access with row level security, `0011` adds KIBOR rates, revision and the forward-only rule, `0012` adds `dm.app_user` (profile of a Supabase Auth user), `dm.party` and `dm.asset`). Apply them in order. Step-by-step set-up is in `GUIDE_Workbench_Supabase_Web_API.md`.

## Mock data

`seed/mock_data_dm.sql` loads invented demo data once: parties, assets and four contracts (MOCK 1 performing, MOCK 2 no payment for 90 days, MOCK 3 no payment for 120 days with non-performing status and income suspension, MOCK 4 quarterly KIBOR revisions). It is owned by made-up user ids, so use `seed/assign_mock_data.sql` with `-v new_owner=<user uid>` to show it to a real user. Run it with psql as the database owner, through the pooler URL.

## Tests

Tests are in `tests` and run with node. They need `psql` (default `D:/PostgreSQL/18/bin/psql.exe`, override with env var `PSQL`) and `DATABASE_URL` in `.env`.

The direct database host is IPv6 only. If your network has no IPv6, set `DATABASE_URL_OVERRIDE` to the same URL with the Supabase pooler host `aws-0-ap-northeast-1.pooler.supabase.com` and user `postgres.<project ref>`. Build it in the shell session from `.env`; do not print it or write it to a file.

```bash
node tests/kibor_dm.js
node tests/web_api_access_dm.js
node tests/golden_dm.js tests/fixtures/dm_workbench_v2_1_script.js
```

`golden_dm.js` and `golden_dm_collections.js` need the fixture `tests/fixtures/dm_workbench_v2_1_script.js` as the first argument; the other tests do not.

## Secrets

`.env` holds the database URL and is local only. It is listed in `.gitignore` and must never be committed. The workbench carries only the public project URL and the publishable key; never put the database password or the service-role key in any committed file.

## Publishing (GitHub Pages)

The repository `aegisopstech/DMWorkbench` publishes from branch `main`, root folder. Pages serves `index.html` (the standalone edition) at `dmworkbench.aegisopstech.me`, set by the `CNAME` file. To publish a change: edit the source, run the build, commit `index.html` with the source, and push to `main`.
