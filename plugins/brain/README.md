# BrainBox Brain

Ask your company's BrainBox brain from Claude: domain model (concepts, terms, rules, workflows, metrics), recent traces from meetings and email, synthesized wiki pages, curated skills, and live data tools.

## Install

```
/plugin marketplace add brainbox-labs/control-center-plugin
/plugin install brain@brainbox
```

## What it bundles

- **The BrainBox org MCP** (`https://ctx.brainbox-ai.app/api/org-mcp`): one connection to every project Brain in your BrainBox organization. BrainBox is an OAuth 2.0 server, so there is no key to paste: on first use Claude asks you to sign in to BrainBox. If the tools are missing, connect the `brainbox` server (in Claude Code: `/mcp`).
- **One skill, `brain-recall`**: how to route a question (list projects, read the index, then traces, wiki, domain model or data tools) and compose a grounded answer.

## License

MIT
