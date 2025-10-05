# RevOps Command Center

Production-ready demo that shows how we rescue a tangled RevOps stack in a weekend. The project ships a FastAPI mock server, a polished Next.js dashboard, scripted automation telemetry, and a test suite that proves every artifact can be regenerated on demand.

## Highlights

- **Multi-CRM ingest simulation** – `revops_command_center.api` exposes a `/health` probe plus endpoints that mimic HubSpot, Salesforce, and Pipedrive payloads. Uvicorn hot-reloads while we iterate.
- **Scoring & automation dashboards** – `revops_command_center/ui` is a Next.js 14 app (React 18, Recharts) that renders hot/warm account lists, playbooks, automation queues, and async digests. Playwright snapshots in `testing/snapshot/` guarantee visuals stay on-brand.
- **Mission artifacts on tap** – `make revops-capture` zips the latest run into `docs/SWEcho/portfolio/...` with summaries, hot-account reports, digests, and the automation event log used in portfolio decks.
- **Hardening tests** – `make revops-test-suite` executes the 18-test harness in `testing/` (smoke, integration, chaos, accessibility, performance). GUI journeys are covered with Playwright, and the benchmark keeps orchestration under 2 s.

## Getting Started

```bash
poetry shell # or source .venv/bin/activate
make revops-dev
```

The make target will:
1. Kill lingering services on ports `8000` and `5173`.
2. Start the FastAPI mock server (`uvicorn revops_command_center.api.mock_server`).
3. Ensure `ui/node_modules/` exists (auto-installs if missing) and run `next dev -p 5173`.
4. Poll the `/health` endpoint and the Next.js dev server until they respond, then open <http://localhost:5173>.

Stop the stack with:

```bash
make revops-stop
```

## Useful Commands

| Command | Purpose |
| --- | --- |
| `make revops-dev` | Boot API + UI with health checks and auto-install of UI deps. |
| `make revops-stop` | Tear down both processes. |
| `make revops-test-suite` | Run the full pytest matrix in `testing/`. |
| `make revops-capture` | Generate the portfolio bundle in `docs/SWEcho/portfolio/revops/<timestamp>/`. |
| `python scripts/strip_and_zip.py revops_command_center --dry-run` | Preview what will be stripped before creating an uploadable ZIP. |
| `python scripts/strip_and_zip.py revops_command_center --output revops_command_center_bundle.zip --overwrite` | Produce a clean archive for Upwork/GitHub releases. |

## Structure

- `api/` – FastAPI entry point (`mock_server.py`) that mounts the real app from `api/main.py`.
- `ui/` – Next.js dashboard with reusable components and Playwright snapshots.
- `docs/` – Offer copy, client readiness checklists, and portfolio bundles (see `docs/SWEcho/portfolio`).
- `testing/` – pytest lanes (integration, chaos, accessibility, snapshots, performance).
- `scripts/`
  - `run_gauntlet.py` / `run_tests_matrix.py` – For LangGraph smoke suites.
  - `strip_and_zip.py` – Strip heavy dirs and produce an artifact bundle.
- `revops_api.log`, `revops_ui.log` – Runtime logs written by the make target.

## Workflow

1. `make revops-dev` to iterate locally.
2. Build assets or capture a run with `make revops-capture` (outputs to `docs/SWEcho/portfolio/...`).
3. Run `make revops-test-suite` before shipping or recording demos.
4. Use `python scripts/strip_and_zip.py revops_command_center --output docs/SWEcho/portfolio/revops/revops_bundle.zip --overwrite` to produce an upload-ready ZIP for Upwork or Git releases.

## Tech Stack

- **Backend:** Python 3.13, FastAPI, Uvicorn (reload enabled).
- **Frontend:** Next.js 14, React 18, TypeScript, Recharts.
- **Testing:** Pytest, Playwright snapshots (stored under `testing/snapshot/baseline`), pytest-benchmark.
- **Tooling:** Makefile helpers, npm, scripts for artifact capture and zipping.

---

Everything in this repo is designed to regenerate proof in minutes: telemetry logs, async digests, dashboards, and the zipped bundle we show prospects. Run the make targets, capture the artifacts, and drop the ZIP straight into our Upwork portfolio.
