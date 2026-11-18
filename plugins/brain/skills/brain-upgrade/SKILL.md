---
name: brain-upgrade
description: "Upgrade the BrainBox CLI and the brain plugin to the latest version. Invoke for: upgrade BrainBox, update BrainBox, get the latest CLI, update the skills, is BrainBox up to date?"
---

# brain-upgrade: Upgrade BrainBox

Two things update independently:

- **The CLI** (`@brainbox-labs/cli`, from public npm)
- **The plugin** (these brain-* skills and the bundled MCP, `brain@brainbox`)

## 1. Check the CLI

```bash
brainbox --version
npm view @brainbox-labs/cli version
```

The first is the local version, the second the latest on npm. If `brainbox` is not installed globally, the user is running via `npx -y @brainbox-labs/cli@latest`, which always fetches the latest: nothing to update.

If the local version is older, tell the user what changes (`vLocal -> vLatest`) and ask before upgrading:

```bash
npm install -g @brainbox-labs/cli@latest
brainbox --version
```

## 2. Update the plugin

Tell the user to run, in a terminal:

```bash
claude plugin update brain@brainbox
```

or `/plugin update` inside a Claude Code session. If the plugin is not installed yet: `/plugin marketplace add brainbox-labs/control-center-plugin`, then `/plugin install brain@brainbox`.

The update takes effect after a restart of Claude Code or `/reload-plugins`. Say so clearly; the current session keeps the old skills until then.

## 3. Report

Say what was upgraded (CLI version, plugin, or both). Upgrading does not sign the user out. If both were already current, say "BrainBox is up to date (CLI v{n})."
