---
name: brain-setup
description: "Use this skill for first-time BrainBox setup: one command signs the user in, picks a project (or the Olist demo), and gets the first question answered in about 2 minutes. No install needed. Invoke for: set up BrainBox, get started with BrainBox, log in to BrainBox, I'm new to BrainBox."
---

# brain-setup: First-Time BrainBox Setup

Goal: the user's first query answered in about 2 minutes. Keep it short; do not explain the whole product.

The CLI runs from public npm, so nothing needs installing: `npx -y @brainbox-labs/cli@latest <cmd>`. If the user already has `brainbox` installed globally, `brainbox <cmd>` is the same.

## 1. Run init

```bash
npx -y @brainbox-labs/cli@latest init
```

`init` does the whole setup: it logs the user in if needed (a device-code flow in the browser; use `brainbox login --no-browser` on a headless machine), picks or creates a project, writes `.brainbox/config.json`, installs this plugin, and prints next steps. Credentials are stored by the CLI. Do not ask the user for an API key or write a `.env`.

Useful forms:

- **Try it with no data of their own:** `npx -y @brainbox-labs/cli@latest init --demo olist` (the Olist demo project).
- **Use a named project:** `init --project <name>`.
- **Plugin already installed:** add `--skip-plugin`.

To check whether the user is already signed in first, run `brainbox whoami` (it exits 1 and prints `{"signedIn":false}` with `--json` when not logged in).

## 2. Reload plugins

If `init` installed the plugin, the user runs `/reload-plugins` (or restarts Claude Code). This plugin already ships the hosted BrainBox MCP (server `brainbox`, the org door), so there is nothing to register. The first MCP call opens a one-click OAuth sign-in to BrainBox.

## 3. Ask the first question

Use `brain-recall` to answer something real, for example "what does BrainBox know about our customers?" (or an Olist question if they chose the demo). An answered question is the finish line for setup.

## Done

Tell the user:
- Setup is complete and which project they are on
- To add their own data: `brain-ingest`
- To check the environment at any time: `brain-status`

Note: an existing `BRAINBOX_API_KEY` in the environment or `.env` still works for the CLI. Do not tell new users to set one up; `brainbox login` is the path.
