---
name: brain-recall
description: "Use this skill when the user wants to query their BrainBox brain: ask business questions, look up domain knowledge, trace workflows, explore data, or understand entity relationships. The BrainBox MCP is bundled with this plugin. Invoke for: ask the brain, what does BrainBox know about X, query the domain model, look up business rules."
---

# brain-recall: Query the BrainBox Brain

Use this skill to ask questions of your company's BrainBox brain. The brain exposes several families of tools:

- **Domain model tools**: conceptual questions: definitions, workflows, business rules, entity relationships
- **Intelligence trace tools**: day-to-day events (meetings, emails, tickets) processed into the brain
- **Wiki pages**: synthesized narrative and entity briefs built from traces
- **Skills**: curated, versioned procedures for how work gets done in a project
- **Personal pages**: a specific person's own preferences and private context
- **Data tools**: live query results from the project's database via approved SQL

---

## BrainBox MCP connection

This plugin bundles the BrainBox org MCP (server `brainbox`). On first use Claude asks the user to sign in to BrainBox. If the tools are missing, the user needs to connect or authenticate the `brainbox` server (in Claude Code: `/mcp`).

---

## Start here: pick the project, then get the index

The org door serves every project Brain in the user's organization.

1. Call `list_projects` first.
2. Choose the project by routing on each project's `description` and `aliases`. If it is ambiguous, ask the user which project they mean.
3. Pass that `projectId` to every other tool.
4. Call `get_index` first on that project. It returns the complete tool manifest; use it to route the question to the right tool.

---

## Domain model tools

Use these for conceptual questions: what entities exist, how they relate, what rules and processes govern them.

| Tool | When to use |
|---|---|
| `recall_domain` | Unified query over the domain model. Pass `kind` to scope by type, `name` for a deep-dive on one item, or both. |
| `list_learnings` | Retrieve ad-hoc learnings saved during past sessions |
| `create_learning` | Persist a new insight or correction for future recall |

`recall_domain` `kind` values:

| `kind` | Use for |
|---|---|
| `concept` | Entities, definitions, facts the brain has learned |
| `term` | Precise business term definitions |
| `rule` | Constraints, validation rules, business logic |
| `workflow` | Business processes and their steps |
| `metric` | KPIs and measures |
| `relationship` | Relationships and dependencies between entities |

Omit `kind` to fuzzy-search across all kinds by `name`. Use `verbosity: "full"` for field types, descriptions and source table bindings. Prefer `get_index` for a project-wide overview.

---

## Intelligence trace tools

Use these for event-based questions: what has been happening with a seller, customer or entity. Traces are the brain's record of processed meeting notes, emails and tickets.

| Tool | When to use |
|---|---|
| `recall_traces` | "Get me up to speed on X" / "What's been happening with Y?" / "What did Logistics see this week?" |
| `get_trace` | Full body of a specific trace when the excerpt isn't enough |

**Routing rule:** any "get me up to speed", "what's going on with", or recency question about a named entity: call `recall_traces` **before** domain model tools. Traces carry day-to-day intelligence; the domain model carries structural vocabulary. Use both.

---

## Wiki pages

Wiki pages are synthesized narrative (entity briefs and observed patterns) written up from traces, as opposed to the structured domain model.

| Tool | When to use |
|---|---|
| `recall_wiki` | Instance-shaped questions ("what is happening with Acme"). Filter by `entity`, `kind` (e.g. `"entity-brief"`, `"synthesis"`), or `tag`. Pass `q` for free-text search. Returns a bare array of entries with a body excerpt; each entry's `id` field (not `pageId`) is what `get_wiki_page` expects. With `q`, entries may carry `matchedPassage` (`headingPath` + `text`): read it before fetching the whole page. |
| `get_wiki_page` | Full body of a specific page when the excerpt isn't enough. |
| `save_wiki_page` | File new synthesis. Search `recall_wiki` first and reuse an existing page's id to update it rather than creating a duplicate. To add to a page without re-sending it, pass `mode: "append"` with `id` and `body`. |

**Routing rule:** prefer `recall_wiki` over `recall_domain`/`recall_traces` for instance-shaped questions: it is synthesized narrative, not raw structure or a single trace.

---

## Skills

Skills are curated, versioned procedures: "how specific work gets done here."

| Tool | When to use |
|---|---|
| `list_skills` | Check before any multi-step or procedural task; a curated skill may already describe how the work is done in this project. |
| `get_skill` | Fetch a skill's full body by name once `list_skills` (or `get_index`'s `skillsCount`) suggests one applies. |

A non-zero `skillsCount` in `get_index` is the signal to call `list_skills` before improvising a multi-step task.

---

## Personal pages

Personal pages hold a specific person's own preferences and private context, distinct from project-wide learnings, which apply to everyone.

| Tool | When to use |
|---|---|
| `list_personal_pages` | Check when `get_index`'s `personalPagesCount` is non-zero. Apply results as priority context over default reasoning, like `list_learnings` corrections. |
| `save_personal_page` | Save a person's page. To flag an existing page stale or inaccurate, pass `id` + `flagForReview: true` with no `title`/`body`. A later content edit without the flag clears it. |

---

## Data tools

Data tools are project-specific; their names come from the index. They run approved SQL against the live database and return structured results. Call them by name after discovering them via `get_index`.

---

## Composing grounded answers

The best answers combine:

1. **Traces**: what has actually happened recently (`recall_traces`)
2. **Wiki pages**: synthesized narrative on a specific entity (`recall_wiki`)
3. **Domain model**: structural vocabulary and rules (`recall_domain`)
4. **Data tools**: live numbers from the database

---

## If a tool returns nothing useful

- No traces for an entity: events may not have been processed yet. Events and documents are added to the brain through BrainBox (ctx.brainbox-ai.app).
- No wiki pages: nothing has been synthesized yet for this entity. Fall back to `recall_traces`/`recall_domain`, and consider `save_wiki_page` once you've pieced together a useful answer.
- No domain model hits: try `recall_domain` with a broader `name`, or `kind: "workflow"` under a different name.
- No relevant data tools in the index: the source may not be connected yet; events and documents are added through BrainBox (ctx.brainbox-ai.app).
