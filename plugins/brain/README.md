# BrainBox Brain

Ask your company's BrainBox brain from Claude: domain model (concepts, terms, rules, workflows, metrics), recent traces from meetings and email, synthesized wiki pages, curated skills, and live data tools.

## Install

```
/plugin marketplace add brainbox-labs/control-center-plugin
/plugin install brain@brainbox
```

## What it bundles

- **The BrainBox org MCP** (`https://ctx.brainbox-ai.app/api/org-mcp`): one connection to every project Brain in your BrainBox organization. BrainBox is an OAuth 2.0 server, so there is no key to paste: on first use Claude asks you to sign in to BrainBox. If the tools are missing, connect the `brainbox` server (in Claude Code: `/mcp`).
- **Seven skills**:
  - `brain-setup`: first-time setup in about 2 minutes (`npx -y @brainbox-labs/cli@latest init`, optionally with the Olist demo)
  - `brain-recall`: how to route a question (list projects, read the index, then traces, wiki, domain model or data tools) and compose a grounded answer
  - `brain-ingest`: ingest a Postgres or Trino database, dbt project or documents with one `brainbox ingest <source>`
  - `brain-workflow`: run a business workflow end-to-end against local data through the local dev server
  - `brain-score`: a repeatable quality report on the brain
  - `brain-status`: a quick health snapshot (CLI, sign-in, project, active layer)
  - `brain-upgrade`: update the CLI (npm) and this plugin

## The CLI

The skills drive the public `@brainbox-labs/cli`. Nothing to install: they run it with `npx -y @brainbox-labs/cli@latest <cmd>` (or `brainbox <cmd>` if you installed it globally). Sign in once with `brainbox login`.

## License

MIT
