---
name: brain-memory
description: "Use this skill when you are working in a BrainBox project and need to decide what to remember and where to put it: record what happened, save a correction, build up knowledge about a subject, or keep a user's own note. Invoke for: remember this, save what we learned, should this be a learning or a wiki page, update what the brain knows about X, the brain has a wrong note."
---

# brain-memory: What to Remember, and Where

Memory is knowledge you gain from experience and keep for later sessions, saved as context a future agent can read. It is not a way to restate what the data already shows. Write down what a future agent could not work out for itself: why something is done, what you tried and found wrong, what a term really means here.

BrainBox holds memory in four layers, from raw to curated. Each layer is read by a different tool, and a mistake costs a different amount at each one.

| Layer | BrainBox | What it is for |
|---|---|---|
| Transcript | Traces (`process_event`, `recall_traces`, `get_trace`) | What happened. Immutable, cheap, the ground truth other notes are checked against |
| Notes | Learnings (`create_learning`), personal pages (`save_personal_page`) | A short fact worth carrying forward |
| Representation | Wiki pages (`save_wiki_page`, `recall_wiki`) | Everything known about one subject, in one place, linked to related subjects |
| Injected layer | `get_index`, plus any active learnings | What every session sees without asking |

## Read before you write

Call `list_projects`, then `get_index` for the project, then `list_learnings` if the index shows learnings. Search the wiki (`recall_wiki`) for the subject before you write about it. Most of the time the right move is to update something that already exists, not to add a new note.

## Choosing where it goes

Ask what the note will do for the next agent.

- **It happened and might matter later** (a meeting, a decision, a failed attempt): record a trace. Record a session's own transcript or summary with `interpret: false`: the text is stored as written, so a later pass has real ground truth to check notes against. A business event sent through `process_event` with the default is stored as the Brain's interpretation of it, not the original, and says so to anyone who verifies against it. Set `source.externalId` so a retry does not duplicate it, and reuse `threadId` for a series. Traces are always safe to add: nobody is told to act on them.
- **It is a fact that should change how every future agent answers** (a business rule, a definition, a correction of something the Brain said): `create_learning`. Be sure of it, because active learnings are injected into every session of the project, and a wrong one travels everywhere. If you are not sure, put it in a trace or a wiki page and let the evidence build.
- **It is knowledge about one subject that keeps growing** (a customer, a system, a recurring problem): a wiki page, one per subject. Look for the existing page and update it. Use `mode: "append"` for running logs so concurrent writers keep each other's entries; pass `baseVersion` when you rewrite a page so you do not overwrite someone else's change. Link related pages with `[[namespace/page-name]]`, where the name is the page title as a lowercase slug ("Acme Roasters" in `memory` is `[[memory/acme-roasters]]`), so the next reader can follow them.
- **It is how this person reads this business's data** (which column to trust, the report they like): a personal page. It is visible only to them.
- **It is only about how you like to work** (tone, length): not Brain memory; keep it in your own harness memory.

When two places fit, choose the one a wrong entry hurts least. Trace before wiki page, wiki page before learning.

## Keep it small and true

- One page per subject. If you find two, merge them into the one a reader would look for and leave a pointer in the other.
- Quote the source or cite the trace id next to a claim, so someone can check it.
- A learning is a sentence or two. Put the detail in a wiki page and link to it.
- Fix a wrong learning: `create_learning` with `supersedes` to replace it, or `retire_learning` with a reason. Only active learnings can be changed this way; a promoted rule is curated by a human, so say so to the user instead.
- Do not write down what is visible in the data, or what you did step by step. Do write down what surprised you.

## Link graph availability

Backlinks and the wiki section of `get_index` appear only when the organization has the wiki link graph turned on. If `get_index` shows no `wiki` section, links you write are still stored but you cannot see what links to a page, so say "link graph is off" if the user asks for backlinks.

## Cleaning up over time

Memory drifts. A scheduled pass can verify notes against traces, merge duplicate pages and retire contradicted learnings: see `brain-dream`.
