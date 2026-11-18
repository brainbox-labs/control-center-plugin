---
name: brain-score
description: "Use this skill to run a repeatable quality evaluation of a BrainBox brain. Calls MCP tools to assess coverage, description quality, domain model depth, and data freshness, and saves a versioned markdown report. Invoke for: score the brain, evaluate brain quality, audit BrainBox, how good is our brain?"
---

# brain-score: Evaluate Brain Quality

Run a fixed evaluation protocol against a BrainBox brain and save a versioned markdown report. The protocol is identical every run so scores are comparable over time.

## Prerequisites

This skill calls the BrainBox org MCP bundled with this plugin (server `brainbox`, the org door: OAuth sign-in, one connection across projects). If its tools are missing, the server needs authentication: call `mcp__plugin_brain_brainbox__authenticate`, give the user the URL, and see `brain-recall` (BrainBox MCP connection) for the fallbacks. Call `list_projects` first, choose the project to score (ask the user if ambiguous), and pass its `projectId` to every tool below. See `brain-recall` for how the tools route.

## Evaluation protocol

Run these steps in order. Collect evidence as you go and paste relevant excerpts into the report.

1. **Index the brain.** Call `get_index`. Record the total tool count, categories present, and obvious gaps (no data tools, no domain model tools).
2. **Test domain model depth.** Call `recall_domain` with `name:` set to each of 3 broad business terms inferred from the index. Rate each: specific, accurate facts, or vague generalities?
3. **Sample data tools.** Pick 3 representative data tools from the index and call each. Record whether each returned non-empty, plausible results.
4. **Trace a workflow.** Call `recall_domain` with `kind: "workflow"`, pick one, then call again with `kind: "workflow"` and its `name`. Rate: is it complete, are the steps specific?
5. **Check governing rules.** Call `recall_domain` with `kind: "rule"`. Did it return domain-specific rules, or nothing?
6. **Test connections.** Pick two entities that should be related and call `recall_domain` with `kind: "relationship"` and `name:` the first entity. Does it surface concrete relationships?
7. **Check learnings.** Call `list_learnings`. Are there saved learnings, and are they relevant and accurate?

## Scoring (each dimension 0-10)

| Dimension | What a 10 looks like |
|---|---|
| **Coverage** | Tools span all major business processes; no obvious gaps |
| **Description quality** | Every tool has a specific, actionable description in domain language |
| **Schema completeness** | Tool inputs have typed fields with descriptions, not bare param names |
| **Domain model depth** | `recall_domain` returns precise, specific facts, not generic definitions |
| **Data freshness** | Data tools return non-empty, plausible results |
| **Workflow traceability** | `recall_domain` (kind: "workflow") produces a coherent, complete process end-to-end |
| **Learnings** | `list_learnings` returns relevant, non-contradictory insights from past sessions |

## Risk flags

Detect and report any of these:

- Fewer than 5 tools in the index: **under-populated brain**
- Any "get recent" / "get all" data tool returns 0 rows: **stale or disconnected data**
- Tool descriptions under 20 characters: **low-quality annotations**
- `recall_domain` (kind: "rule") returns nothing: **business rules not captured**
- No domain model tools in the index: **knowledge layer missing**
- No data tools in the index: **semantic layer missing or inactive**
- `recall_domain` (kind: "relationship") returns nothing: **entity relationships not captured**
- `list_learnings` returns nothing and the brain is not new: **learnings never saved; consider `create_learning` after sessions**

## Save the report

Save as `brain-score-YYYY-MM-DD.md` in the current directory. If that file exists, use `brain-score-YYYY-MM-DD-2.md`, then `-3`, and so on.

```markdown
# Brain Score Report: YYYY-MM-DD

## What This Brain Knows
<2-3 sentences inferring what the company does, from tool names and domain model responses>

## Scores
| Dimension | Score | Evidence |
|---|---|---|
| Coverage | /10 | <one line> |
| Description quality | /10 | <one line> |
| Schema completeness | /10 | <one line> |
| Domain model depth | /10 | <one line> |
| Data freshness | /10 | <one line> |
| Workflow traceability | /10 | <one line> |
| Learnings | /10 | <one line> |

**Overall: X / 70**

## Risks
- <risk 1>
- <risk 2>

## Recommended Next Steps
- <action 1, e.g. "Run brain-ingest to ingest the orders SQL files">
- <action 2>
```

Tell the user the exact path where the report was saved.

## Repeatability

The protocol is fixed: same 7 steps every run. A score that drops between runs signals degradation: stale data, a removed tool, or deleted annotations. Run monthly or after any major ingestion change.
