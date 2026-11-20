---
name: brain-setup
description: "Use this skill for first-time BrainBox setup: one command signs the user in, picks a project, and gets the first question answered in about 2 minutes. No install needed. Invoke for: set up BrainBox, get started with BrainBox, log in to BrainBox, I'm new to BrainBox."
---

# brain-setup: First-Time BrainBox Setup

Goal: the user's first query answered in about 2 minutes. Keep it short; do not explain the whole product.

The CLI runs from public npm, so nothing needs installing: `npx -y @brainbox-labs/cli@latest <cmd>`. If the user already has `brainbox` installed globally, `brainbox <cmd>` is the same.

## 1. Run init

```bash
npx -y @brainbox-labs/cli@latest init --json
```

`init` never prompts. It logs the user in if needed (a device-code flow in the browser; add `--no-browser` on a headless machine), installs this plugin itself, and ends with numbered next steps. Credentials are stored by the CLI. Do not ask the user for an API key or write a `.env`.

`--json` returns `{signedIn, project, plugin, nextSteps[]}` where `plugin` is `installed`, `no-claude`, `failed` or `skipped`. Follow `nextSteps` in order. If `plugin` is `no-claude` or `failed`, show the user the manual install lines:

```
/plugin marketplace add brainbox-labs/control-center-plugin
/plugin install brain@brainbox
```

Useful forms:

- **Use a named project:** `init --project <name>`. Without `--project`, `init` does not create `.brainbox/` or a project; `brainbox ingest --project <name> <source>` (step 3) creates both in one go.
- **Plugin already installed:** add `--skip-plugin`.

To check whether the user is already signed in first, run `brainbox whoami` (it exits 1 and prints `{"signedIn":false}` with `--json` when not logged in).

## 2. Reload plugins

If `init` installed the plugin, the user runs `/reload-plugins` (or restarts Claude Code). This plugin already ships the hosted BrainBox MCP (server `brainbox`, the org door), so there is nothing to register. After `/reload-plugins`, the plugin's server shows as `plugin:brain:brainbox` with status "Needs authentication" and its real tools do not exist yet. Call the `mcp__plugin_brain_brainbox__authenticate` tool (no arguments): it returns an authorization URL to give to the user. Once they authorize, the real tools appear with no restart. If the localhost callback page fails to load (remote sessions), have the user paste the callback URL and call `mcp__plugin_brain_brainbox__complete_authentication` with it. Manual fallback: `/mcp`, then `plugin:brain:brainbox`, then Authenticate. If the user already has a claude.ai BrainBox connector on `/api/org-mcp`, the tools will appear twice; either copy works.

## 3. Ask the first question

If the project has no data yet, do not stop at asking. Inspect the working directory first and come back with a concrete recommendation:

- **Scan (read-only, skip `node_modules`, `.git`, build output, `.env*` contents):** docs (`*.md`, `*.pdf`, `*.docx`, `*.html`, `docs/`, `runbooks/`, `policies/`), SQL (`*.sql`, `schema.sql`, `migrations/`), and database hints (`DATABASE_URL`, `postgres://`, `trino://`, `athena://` in `README*`, `docker-compose*.yml`, `.env.example`, config files). Never print a URL's password; if only `.env` holds the URL, say it exists and ask before using it.
- **Recommend** the best fit, with counts: e.g. "Found 42 markdown docs in `docs/` and 6 migrations in `db/migrations/`. I suggest ingesting both: `brainbox ingest docs/ db/migrations/`." Prefer structural content (schemas, policies, runbooks) for ingest; time-stamped notes, emails or tickets go to process events (see `brain-ingest`).
- **Confirm, then run.** Offer the exact command and ask one yes/no. If the folder has nothing useful (empty, or only app code), say so and offer two paths in one line: connect their own data (a database URL or a docs path), or load the Olist e-commerce sample brain to try BrainBox first. For the sample, run `npx -y @brainbox-labs/cli@latest demo olist` (takes seconds, copies a ready-made brain into their org; `init --demo olist` does it as part of init). When introducing it, show this credit verbatim: "Sample data: Brazilian E-Commerce Public Dataset by Olist (https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), licensed CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/). This sample brain is adapted from that dataset and shared under the same licence, free for non-commercial use. Not affiliated with or endorsed by Olist." When it is ready, offer 2-3 starter questions that show recall over the curated domain model and wiki (plain `brain-recall`, no synthesis tools). Prefer questions the CLI prints; otherwise use:
  - "What does the brain know about how an order moves from purchase to delivery?" (domain model: entities and relationships)
  - "How is a late delivery defined, and which tables hold the dates?" (wiki plus schema)
  - "Which product categories have the highest share of late deliveries?" (business question over the model)
  Offer it as an option, not a default: someone who has their own data should connect that.

Then use `brain-recall` to answer something real, ideally a question drawn from what was just ingested (e.g. a table or doc you saw), or "what does BrainBox know about our customers?". An answered question is the finish line for setup.

If the user is on the sample, treat it as an on-ramp, not the destination: right after the first answer, say once that this was sample data and offer to point BrainBox at their own (`brain-ingest`: a database URL, SQL files or a docs folder).

## Done

Tell the user:
- Setup is complete and which project they are on
- To add their own data: `brain-ingest`
- To check the environment at any time: `brain-status`

Note: an existing `BRAINBOX_API_KEY` in the environment or `.env` still works for the CLI. Do not tell new users to set one up; `brainbox login` is the path.
