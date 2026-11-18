---
name: brain-status
description: "Use this skill to get a quick health snapshot of BrainBox: CLI version, sign-in, current project, active layer version, tool count, dev server state, and any in-progress context draft. Invoke for: brain status, is BrainBox set up?, what version is active?, is the dev server running?, quick check before I start."
---

# brain-status: Health Snapshot

A quick read-only check. Run these in order and stop at the first failure; do not continue past a broken step. The CLI runs via `npx -y @brainbox-labs/cli@latest <cmd>`, or `brainbox <cmd>` if installed globally.

## Check order

### 1. CLI available

```bash
brainbox --version || npx -y @brainbox-labs/cli@latest --version
```

If neither works, direct the user to `brain-setup`. Then compare with the latest release (one line in the summary, never a blocker):

```bash
npm view @brainbox-labs/cli version
```

If the versions differ, tell the user `brain-upgrade` will update it.

### 2. Signed in

```bash
brainbox whoami --json
```

Prints the user, organization and API URL; exit code 1 means not logged in, so tell the user to run `brainbox login`. Do not read `~/.brainbox/credentials.json` directly. (A legacy `BRAINBOX_API_KEY` in the environment or `.env` still works for the CLI; do not recommend setting one up.)

### 3. Project initialized

```bash
cat .brainbox/config.json
```

Shows org and project. Missing: direct the user to `brain-setup` (or `brain-ingest` to create a project from data).

### 4. Connectivity and history

```bash
brainbox repo log
```

Prints local commits and remote version history. A `401` or `403` is a sign-in problem: run `brainbox login`. An empty log is fine for a new project.

### 5. Dev server

```bash
cat .brainbox/dev.pid 2>/dev/null && echo "running (PID $(cat .brainbox/dev.pid))" || echo "not running"
```

Not running is fine unless the user is about to run a workflow; note it, do not fail on it.

### 6. Active layer (only if connectivity succeeded)

```bash
LAYER_FILE=$(mktemp /tmp/brainbox-layer-XXXXXX.json)
brainbox repo pull --out "$LAYER_FILE" 2>&1 && \
  F="$LAYER_FILE" node -e "const l=require(process.env.F); console.log('version:', l.version, '| tools:', (l.tools||[]).length)" 2>/dev/null || \
  echo "no active layer"
rm -f "$LAYER_FILE"
```

"No active layer" means nothing has been ingested and approved yet.

### 7. Pending draft (only if connectivity succeeded)

```bash
brainbox repo meta status
```

Reports whether a context draft is `processing`, `ready` or `failed`. `processing`: generation is still running, check again in a minute. `failed`: rerun `brainbox repo meta create`.

## Output to show the user

```
CLI             ok v0.4.1 (latest: v0.4.1)
Signed in       ok (you@acme.com, org acme)
Project         acme / analytics
Connectivity    ok (3 remote versions)
Dev server      not running
Active layer    v3, 12 tools
```

If anything is broken, point to the fix:

| Problem | Fix |
|---|---|
| CLI missing | `brain-setup` |
| Not signed in, or `401` / `403` | `brainbox login` |
| No `.brainbox/` | `brain-setup` |
| No active layer | `brain-ingest` to ingest and approve |
| Dev server not running | `brain-workflow` starts it on demand |
