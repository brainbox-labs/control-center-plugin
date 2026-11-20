# Changelog

## Unreleased

Installable from Codex: the repo is now also a Codex marketplace (`.agents/plugins/marketplace.json`) and `brain` carries a `.codex-plugin/plugin.json`, sharing the same `skills/` and `.mcp.json` as the Claude plugin.

## 0.2.4

brain-setup offers the Olist sample brain (`brainbox demo olist`) when the directory scan finds nothing to ingest, so a first question never requires data of your own. Needs `@brainbox-labs/cli` 0.16.4 or later.

## 0.2.3

brain-setup no longer claims `init` creates `.brainbox/` and a project on its own: that needs `--project`, and the one-step path is `brainbox ingest --project <name> <source>`.

## 0.2.2

brain-setup now inspects the working directory before asking what to connect: it scans for docs, SQL files and database URLs (never printing passwords), recommends a concrete `brainbox ingest` command with counts, and suggests a first question drawn from what was ingested.

## 0.2.1

brain-setup no longer offers the Olist demo (parked in CLI 0.15.0); first run goes straight to connecting the user's data with `brainbox ingest`.

## 0.2.0

Six new skills, moved from ctx-layer-clients and rewritten for the public `@brainbox-labs/cli`: `brain-setup`, `brain-ingest`, `brain-workflow`, `brain-score`, `brain-status`, `brain-upgrade`. Sign-in is `brainbox login` (or `brainbox init`) (no API keys or `.env`); the CLI runs via `npx -y @brainbox-labs/cli@latest`. `brain-ingest` leads with one-shot `brainbox ingest <source>`. `brain-recall` merged with the ctx-layer-clients version (the manual `bb_proj_` key steps are gone: the bundled org MCP uses OAuth). The per-skill update-check preambles were removed; `brain-upgrade` covers updates.

## 0.1.0

First release. Recall only: the BrainBox org MCP plus the `brain-recall` skill. Ingest comes when the CLI is public.
