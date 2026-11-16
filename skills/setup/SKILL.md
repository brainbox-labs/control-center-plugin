---
name: setup
description: "First-run setup for the CEO command center: set up to 3 goals, confirm lanes, check which connectors are available. Use when the user says \"set up my command center\", \"get started\", \"configure goals\", or runs the setup command, or when cc_goals returns no goals."
---

# Setup

Goal: a working command center in under two minutes. Keep it conversational and short; the user
shouldn't fill out a form. Why goals first: every ranking decision is made against them, so
"what needs me" means "what moves my goals", not "what is loudest".

## Steps
1. `cc_goals` (no arguments reads). If goals exist, show them and ask what to change instead of starting over.
2. **Goals.** Ask: "What are your top goals right now (up to 3)? One line each, and a number if there
   is one." Examples: "Close 3 design partners", "Hire a founding engineer", "Raise the seed".
   Map each to a lane.
3. **Lanes.** Defaults: customers, team, fundraise, product, learn, ops (customers, team and fundraise
   come with pipeline stages). Offer them in one line and ask if any should be renamed or added (a
   later-stage CEO may want `board`). Don't over-explain.
4. Save with `cc_goals { goals: [{ title, lane, metric?, target?, current? }], lanes?, tz }` and confirm in
   one line. A number is optional: a goal can start as text. `tz` is the user's IANA timezone (e.g.
   `America/Los_Angeles`): take it from the conversation or the calendar's timezone, ask if unsure. Why:
   until set, dates are UTC, so "today", overdue and Today's 3 flip a day early in the evening.
5. **Connectors.** Check which read tools you can see (Gmail, Calendar, Slack, Granola, Outlook, Teams,
   Notion, Fireflies...) without reading any content. Report one line: "I can see Gmail, Calendar.
   Slack and Granola aren't connected; the sweep will skip them." Don't make connecting a prerequisite.
6. **Next.** Offer: "Want me to run a first sweep now (last 7 days, read-only), and then show your
   dashboard?" If yes, follow the `sweep` skill, then call `cc_dashboard`.

## Rules (and why)
- Up to 3 active goals: more than three means none is a priority.
- Nothing is sent, drafted or changed in any connected tool; the plugin only reads what you already
  connected, through Claude, and stores only short task records in your own account.
- Log any reaction ("too many questions") with `cc_feedback` and shorten.
