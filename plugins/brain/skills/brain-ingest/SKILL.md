---
name: brain-ingest
description: "Use this skill when the user wants to ingest a Postgres or Trino database, dbt project, documents or an Agent Skill into BrainBox. One command does the whole pipeline. Invoke for: connect my database to BrainBox, add documents to the brain, push my schema, train BrainBox on my data."
---

# brain-ingest: Ingest Data into BrainBox

Teach BrainBox about the user's data: Postgres and Trino schemas, dbt projects and documents, turned into an annotated context layer that agents can query.

## Before you start

Check the user is signed in:

```bash
brainbox whoami
```

Exit code 1 means not logged in: tell the user to run `brainbox login` (or `brain-setup`) and come back. Do not read credentials files, and never ask for an API key or write a `.env`.

The CLI runs via `npx -y @brainbox-labs/cli@latest <cmd>`, or as `brainbox <cmd>` if installed globally. Examples below use `brainbox`.

## Choose the path

| What you have | Path |
|---|---|
| Postgres or Trino database, dbt project, runbooks, policy docs | **One-shot ingest** (below) |
| Meeting notes, emails, support tickets, Slack threads (raw events) | **Process events** (below) |
| Files to merge into a reusable Agent Skill | **Agent Skill** (below) |

If unsure: recent, time-stamped day-to-day content goes to process events; structural, schema or policy content goes to one-shot ingest.

## One-shot ingest

```bash
brainbox ingest <source> [--project <name>] [--review]
```

`<source>` is one of:

- `postgres://...` or `trino://user[:password]@host[:port]/catalog[/schema]`
- a docs path or glob (PDF, DOCX and HTML are converted to markdown automatically)
- `--dbt <dir>` for a dbt project

This single command runs the whole pipeline: init, stage, commit, push, context generation, polling until ready, and approve. Find the database URL in the user's README or ask them. With `--review` it stops before approving and prints a review URL.

If the CLI reports `0 tables`, the connection worked but the schema was not found: rerun with `--verbose`. Trino has no enforced keys or indexes, so only tables and columns are staged.

### Review before publishing

When the user wants to check the generated annotations before they are published, run with `--review`. **Do NOT approve on the user's behalf.** Show the user the review URL from the output and wait:

> The context is ready. Please review the generated annotations (entities, relationships, vocabulary, rules) at the link below before I publish:
>
> **[review URL]**
>
> Let me know when you're happy for me to approve, or if anything needs adjusting first.

Only after the user explicitly says to approve ("looks good", "approve it", "go ahead"), run `brainbox repo meta approve`. Share the layer version and review URL from the output, then suggest `brain-recall` to ask the first question.

## Process events (meeting notes, emails, tickets)

No commit or push is needed. Call the `process_event` MCP tool directly: read each event file, then pass the full text, `eventType`, `participants`, and a `source` pointer back to the file. Reuse the same `threadId` for events in one series.

- If the text is already processed (you wrote the summary), pass `interpret: false` to store it verbatim with no LLM call.
- Always set `source.externalId` so retries do not duplicate the trace.
- After each call, check `learningCandidates` and call `create_learning` for any with `confidence: high`.

## Agent Skill

```bash
brainbox meta ingest skill <name> <files...> --description "<what this skill does>"
```

Merges local files (text, markdown, html, pdf, docx) into one Agent Skill. It resolves immediately: no commit, push or approve step.

## Advanced / debugging: step by step

Use this only when `brainbox ingest` fails partway and you need to see or rerun a single stage. It is the same pipeline, one stage at a time.

```bash
brainbox repo init --org <name> --project <name>   # or: brainbox repo clone <project-name>
brainbox meta ingest db <url>                      # or: brainbox meta ingest doc <files...>
brainbox repo commit -m "add <source>"
brainbox repo push
brainbox repo meta create
brainbox repo meta status                          # poll until status: ready (usually 1-3 minutes)
brainbox repo meta approve                         # only after user review
```

Other options: `--profile-data[=full]` adds column statistics (derived stats, not raw rows; `full` also sends up to 50 distinct values per column, so check `brainbox profile preview` first if a column may hold PII). `--analysis-depth <none|shallow|deep>` controls document analysis (`deep` for dense runbooks, `none` to store verbatim). `brainbox meta ingest mcp <url>` stages an MCP server.

**Customer tenants:** commands that resolve org/project accept `--tenant <name>` instead of `--project <name>`. Use it when the user gives a tenant name: tenant overlay projects are named "<tenant name> - Tenant Overlay", so `--project` with the tenant name would silently create an empty orphan project. `--tenant` matches the exact tenant name and fails if none exists.

Rerun any command with `--verbose` for full HTTP logs (the Authorization header is always redacted).

| Symptom | Likely cause | Fix |
|---|---|---|
| `ECONNREFUSED` on Postgres | DB not running or wrong host/port | Check the connection string |
| `password authentication failed` | Wrong credentials in the URL | Check username/password |
| `API error 401` or `403` | Not signed in, or wrong organization | Run `brainbox login` again, check `brainbox whoami` |
| `0 tables` | Wrong DB or schema | Rerun with `--verbose` |
| `meta create` stays `processing` | Generation still running | Poll `repo meta status` for up to 5 minutes |
| Profiling is slow on large schemas | Full-table cardinality queries | Limit to specific tables |
