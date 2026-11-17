---
name: agents
description: Run the command center's agents (named team members like Scout or Analyst) through their queues. Runs inside the nightly sweep; also on demand ("run the agents", "run Scout", "run Scout on 12").
---

# Agents: work the team's queues

An agent is a team member with a name, a role, lanes, and a playbook (a BrainBox skill). Work reaches it two
ways: the user assigns it (Ask AI with that agent, `cc assign`), or the agent picks up open tasks in its lanes.
Code decides what is allowed (lanes, needs, today's picks, the wip cap); you do the judgment and the work.

**Never send anything** (email, Slack, invites): research, drafts and docs only. Why: the routine is read-only on
every source, and outward actions need the user's approval of that exact message.

## 1. Who runs
`cc_op {op:"agents", input:{action:"list"}}`. Run every active agent, or only the one the user named. "Run Scout
on 12" = assign first (`cc_update_task {id:12, action:"ask", agent:"scout", by:"me"}`), then run Scout on it alone.

## 2. Per agent (act as it: `by:"agent:<id>"` on every write, so history shows who did what)
1. `cc_op {op:"agents", input:{action:"queue", agent:"<id>"}}` → `assigned` (asked first), `claimable`, `room`.
2. **Playbook**: if the agent has a `skill`, read it once (`cc_op {op:"brain", input:{action:"skill", name}}`)
   and follow it. If the brain isn't connected, work from the agent's `role`.
3. **Assigned** first, at most 3 per agent per run (the rest wait, oldest first next time).
4. **Claim** from `claimable` only when you can move the task forward with what this run can read (research,
   prep, a draft reply). At most `room` tasks. `cc_update_task {id, action:"claim", by:"agent:<id>",
   note:"Picked up: <plan in one line>"}`. A refusal is the code's rule: skip that task, don't argue it.
   Why: a claim the agent can't finish just hides the task from the user.
5. **Do the work** for each task: `cc_op {op:"getTask", input:{id}}`, then research or draft. Save the output with
   `cc_save_doc {kind:"prep", key:"task-<id>", md}` (a short answer can be the note itself).
6. **Hand back**: `cc_update_task {id, action:"handback", by:"agent:<id>", note:"<what was done, where it is,
   the one thing the user should do next>"}`. Can't be done read-only → hand back with the reason.
   Why: an ask that sits unanswered teaches the user to stop asking.
7. **Learn**: if the work taught something reusable (a better source, a format the user liked), note it on
   the task; the brain sync carries outcomes into the brain.

## 3. Report
One line per agent: `Scout: did #12 (call prep), picked up #31, 1 waiting`. Nothing to do is a fine result.
