---
name: brain-workflow
description: "Use this skill when the user wants to run a real business workflow end-to-end against their local data. Starts the local MCP dev server, which serves SQL data tools and domain model tools on the developer's machine. Invoke for: run a workflow, investigate this issue, analyze overdue orders, do a full reconciliation check."
---

# brain-workflow: Run Real Workflows Locally

Orchestrate multi-step business workflows with data staying on the developer's machine. Database credentials and query results never leave the local environment. The BrainBox API is still contacted at startup to resolve the project and sync tool definitions, so the user must be signed in.

The local dev server serves two things in one:

- **Domain model tools**: brain knowledge (definitions, workflows, rules) synced from BrainBox at startup, then served locally
- **SQL data tools**: live queries against the connected Postgres database; results stay on-machine

Use `brain-recall` instead if the user only wants to ask the brain questions without touching live data.

## Before you start

- Signed in: `brainbox whoami` succeeds. If it exits 1, tell the user to run `brainbox login`.
- A project is initialized (`.brainbox/config.json`). If not, use `brain-setup`.
- `DATABASE_URL` (Postgres connection string) is available, or the user gives one.

The CLI runs via `npx -y @brainbox-labs/cli@latest <cmd>`, or `brainbox <cmd>` if installed globally.

Check whether the dev server is already running:

```bash
cat .brainbox/dev.pid 2>/dev/null && curl -sf localhost:3100/mcp >/dev/null && echo "server running" || echo "not running"
```

## Start the local dev server

```bash
brainbox dev start [--db <postgres-url>] [--port 3100]
```

The CLI reads `DATABASE_URL` from the environment if `--db` is not passed. The server listens on `http://localhost:<port>/mcp` (default port 3100). Confirm it is up by checking that `.brainbox/dev.pid` exists and that `curl -sf localhost:<port>/mcp` succeeds; do not rely on matching the CLI's output text. Use `--no-brain` to skip domain model tools.

## Orchestration pattern

For each step in the workflow:

1. **Orient**: list the tools the local server exposes
2. **Get context**: call `recall_domain` (e.g. `kind: "workflow"`, `kind: "rule"`, or a `name` query) for the business rules of this step
3. **Get data**: call the relevant SQL data tool
4. **Reason**: combine context and data to decide or produce an output
5. **Continue**: repeat for the next step

## Tool routing

| Need | Use |
|---|---|
| Business rules, definitions, processes | Domain model tools from the local server |
| Live query covered by an approved tool | SQL data tools from the local server |
| Query not covered by any approved tool | Direct SQL via Bash (below) |

## Direct SQL fallback

If no approved tool covers a needed query:

```bash
psql "$DATABASE_URL" -c "SELECT ..."
```

This bypasses the semantic layer; use it for one-off investigation only. If you find yourself doing it repeatedly, suggest `brain-ingest` to add the missing query to the brain.

## Stop the server when done

```bash
brainbox dev stop
```

## Debugging

| Symptom | Fix |
|---|---|
| `dev start` fails with `ECONNREFUSED` | Check `DATABASE_URL` and that the DB is reachable |
| 401 or 403 at startup | Run `brainbox login` again |
| Data tool returns 0 rows | Data may be empty or filtered; check query params |
| Port already in use | Run `brainbox dev stop`, or use `--port` |
| Domain tool returns a vague answer | The brain may not cover this area; suggest `brain-ingest` |
| `dev start` says no active layer | Approve a context first using `brain-ingest` |
