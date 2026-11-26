---
name: brain-dream
description: "Use this skill to consolidate a BrainBox project's memory offline, on a schedule or on request: verify learnings and wiki claims against traces, merge duplicates into one page per subject, add links, retire contradicted learnings, and log what changed. Invoke for: dream, consolidate the brain's memory, clean up learnings, merge duplicate wiki pages, check the brain's notes against what actually happened."
---

# brain-dream: Consolidate Memory

An offline pass over what agents have recorded. Sessions write quickly and carelessly; this pass slows down, checks, and tidies. It is the step that stops a wrong note from being read and trusted in every later session.

Run it on a schedule (the `loop` or `schedule` skills), or when the user asks. It works through the BrainBox MCP tools only, so it needs no server-side setup. Pick the project with `list_projects` (ask if ambiguous) and use `brain-memory` for what each layer is for.

This is separate from the server's own synthesis (candidates built from trace density), which is an opt-in understanding feature; this pass works with or without it.

## Principles

- **Verify against traces, not against other notes.** A note is checked against the trace it came from. Two notes agreeing proves nothing.
- **A trace stored with `interpret: false` is the original text; an interpreted trace is the Brain's reading of an event.** Judge accordingly: contradiction with original text is strong evidence, contradiction with an interpretation is a reason to look closer.
- **Leave what you cannot check.** "Unverifiable" is a valid result. Do not retire a learning only because you found no trace for it.
- **One writer per page.** Re-read a page and pass `baseVersion` before you rewrite it; on a conflict, re-read and merge.
- **Keep the injected layer small.** Active learnings go to every session. Move detail into wiki pages and keep the learning to a sentence.
- **Changes to what humans curated are suggestions.** Promoted learnings can only be retired by a person; list them in the log with the evidence.

## The pass

1. **Orient.** `get_index`, `list_learnings`, and `list_wiki_pages` for the `memory/` namespace. Read the latest entry of the `memory/dream-log` page; it starts with the time that pass began, and that time is your `since`. Traces already named in the log can be skipped. If there is no log, this is the first pass: start from the oldest traces.
2. **Gather.** `recall_traces` since the last pass, oldest first, for the project (narrow by entity or thread when the volume is large). Read the full body with `get_trace` for any trace that touches a learning or a wiki claim.
3. **Verify.** For each active learning and each wiki claim that recent traces touch, decide: supported, contradicted or unverifiable. Spend effort where facts change (statuses, owners, dates, locations), not on settled definitions. Note the trace ids.
4. **Merge.** Group what is known by subject. Make one wiki page per subject, in the `memory/` namespace or the namespace already used: update the existing page when there is one (`recall_wiki`, then `save_wiki_page` with `id` and `baseVersion`), create one when there is none. Write claims with the trace id beside them. When several learnings say the same thing, keep one and retire or supersede the rest. When a page is now a duplicate of another, use `archive_wiki_page` if your tool list has it (it appears only when the organization has agent archive tools turned on); otherwise overwrite the duplicate with a one-line pointer to the surviving page. Archived pages are hidden from every agent at once and are permanently deleted after a retention period unless a person restores them, so merge first and archive second. Say in the log which you did.
5. **Link.** Add `[[namespace/page-name]]` links and `relatedPages` between pages that mention each other, and repair links to pages that do not exist. If `get_wiki_page` returns no `links` or `backlinks`, the link graph is off for this organization: write the links anyway and say so in the log.
6. **Retire.** For each contradicted active learning, `create_learning` with `supersedes` when you have the corrected fact, otherwise `retire_learning`; the reason names the trace that contradicts it. Active learnings only.
7. **Promote upward, keep it lean.** A fact seen repeatedly and confirmed by traces may be worth one short learning; the rest stays in the wiki. If the active learnings still number in the dozens after this pass, say so in the log.
8. **Log.** Append one short entry to the `memory/dream-log` page (create it if missing) with `mode: "append"`: a heading with the full ISO time the pass began, how many traces were read, what was verified, merged, linked and retired, what stayed unverifiable, and any promoted learnings that look wrong. Keep it to a few lines; the log is for a reader who wants to know what changed.

## Cadence and cost

A pass reads every new trace, so run it often enough that each pass is small, and not so often that nothing changed. Daily is a reasonable default. The pass must be safe to repeat: with no new traces and nothing contradicted, change nothing except one log entry that says "no changes".

## What this pass does not do

It does not archive traces, edit promoted learnings, read other people's personal pages, or open the original artifact a trace points to.
