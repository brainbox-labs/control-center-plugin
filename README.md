# CEO Command Center by BrainBox

A Claude plugin that keeps a founder's tasks from dropping: it reads the tools you already
connected, files every reply owed, promise made and decision pending, and shows one ranked screen of
what needs you.

## What you get

- A **dashboard** (an interactive MCP App) with the one thing to do now, your top goals, Today's 3,
  what needs you, who to chase, the agent's offers, your pipeline, and what you closed this week. Done, Snooze and Do it are one tap.
- A **sweep** that pulls new tasks from Gmail, Calendar, Slack, Granola and similar connectors, and
  closes tasks that resolved.
- A **brief** that picks Today's 3 against your goals and fits on one screen.
- Plain-language control: "what needs me", "done 12", "snooze 4", "remind me to send Sam the deck".

<!-- screenshot: dashboard (placeholder) -->

Zero items is a good result. The plugin never invents work to look busy.

## Install

- **Claude Code:** `/plugin install brainbox-ceo --marketplace brainbox-labs/control-center-plugin`.
  To try a local copy: `claude --plugin-dir ./control-center-plugin`.
- **Cowork:** Customize > Plugins, then add CEO Command Center by BrainBox.

- **Claude on the web, desktop and mobile:** Customize > Plugins, add CEO Command Center by BrainBox,
  then connect it from the plugin's **Connectors** tab and sign in with Google.

## First run

Run `/brainbox-ceo:setup`. It asks for up to three goals, confirms your lanes (customers, team,
fundraise, product, learn, ops by default), notes which connectors it can see, and offers a first sweep.

## Daily use

- `/brainbox-ceo:sweep` or "sweep now": pull new tasks.
- `/brainbox-ceo:brief` or "brief me": the one thing and Today's 3.
- "What needs me?": shows the dashboard.
- "done 12", "snooze 4", "drop 7": update a task by id.
- Dump a thought ("I promised Priya the pricing doc by Friday") and it becomes a task with a next step.

## Tools

The connector exposes these tools (Claude calls them; you never need to):

- `cc_dashboard`: the one-screen view (interactive where supported)
- `cc_add_tasks`, `cc_update_task`, `cc_list_tasks`, `cc_set_today`: tasks and Today's picks
- `cc_subject`: companies and people, and each lane's pipeline
- `cc_goals`, `cc_cursor`: goals, lanes, timezone; per-source sweep cursors
- `cc_save_doc`, `cc_get_doc`, `cc_list_docs`: briefs, prep, reading notes, decisions, lane notes
- `cc_feedback` (tunes your own command center), `cc_send_feedback` (to the BrainBox team)
- `cc_export`, `cc_delete_my_data`: your data, on request
- `cc_op`: used by the dashboard's buttons only

## Where it works

Everywhere Claude runs: chat on web, desktop and mobile, Cowork, and Claude Code. The plugin's
connector is a remote server, so your tasks follow you between devices. Hosts that can't render the
dashboard get a text version.

## What this plugin runs, reads and sends

- **Connects** to one remote MCP server, `https://control-center-v1.web.app/mcp`, run by BrainBox
  Labs on Google Firebase. You sign in with your Google account (OAuth); BrainBox sees your name and
  email from Google, nothing else from that account.
- **Stores** your tasks, companies and people (title, next step, source link or short quote, dates), goals, lanes, sweep
  cursors and feedback in Google Cloud Firestore, under your own account and readable only by you
  through the server. No other user can read them; we don't sell or share them.
- **Reads** only connectors you already connected to Claude, and only through Claude, when you ask
  for a sweep or brief. The plugin holds no Gmail, Slack or calendar credentials and makes no calls to
  those services. Skills are read-only on connectors: they never send, label or edit; drafts only
  when you ask.
- **Loads** only fonts from Google Fonts in the dashboard; no outside scripts or trackers.
- **Runs** nothing on your computer. No local server, no hooks, no telemetry or analytics.

See [PRIVACY.md](PRIVACY.md) for retention and deletion. MIT licensed.

## Feedback

Use **Feedback** on the dashboard, tell Claude "send feedback to BrainBox: …", or email
[hello@brainbox-ai.app](mailto:hello@brainbox-ai.app). We read every message.
