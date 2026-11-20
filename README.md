# BrainBox plugins

A Claude Code plugin marketplace from BrainBox Labs.

| Plugin | What it is |
|---|---|
| [`ceo-control-center`](plugins/ceo-control-center/README.md) | CEO Command Center: files every reply owed, promise made and decision pending, and shows one ranked screen of what needs you. |
| [`brain`](plugins/brain/README.md) | Query your company's BrainBox brain (domain model, traces, wiki, skills) from any Claude surface. |

## Install

Claude Code:

```
/plugin marketplace add brainbox-labs/control-center-plugin
/plugin install ceo-control-center@brainbox
/plugin install brain@brainbox
```

Codex (`brain` only; same skills and MCP server):

```
codex plugin marketplace add brainbox-labs/control-center-plugin
codex plugin add brain@brainbox
codex mcp login brainbox
```

## License

MIT
