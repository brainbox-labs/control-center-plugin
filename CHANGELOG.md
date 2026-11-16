# Changelog

## 0.3.0

One sweep skill and one brief skill, carrying the rules learned from real use (previously a richer copy lived in the code repo).

- Sweep: daily sources, `me`/ignore doc, canary on Slack, reading summaries and think items, closing resolved tasks from sent mail, subjects and stages, offers, run log; new read-only Apollo step (`apollo:<id>`).
- Brief: one Opus judge, progressive disclosure (5 lines, then `## More`), prep docs, inbox triage, signal absorption, Friday pipeline numbers, push notification.
- Product-generic: founder context comes from the user's `me`, `culture` and goals docs.

## 0.2.0

Skills move to the v2 tools and vocabulary.

- Vocabulary: task (was loop), need (was kind), subject (was account/person), offer (was assist/suggestion).
- Tools: `cc_add_tasks`, `cc_update_task`, `cc_list_tasks` replace `cc_add_loops`, `cc_update_loop`,
  `cc_list_loops`; new `cc_subject`, `cc_send_feedback`, `cc_export`, `cc_delete_my_data`, `cc_save_doc`,
  `cc_get_doc`, `cc_list_docs`, `cc_op`. `cc_person` and `cc_page_action` are gone.
- `by: "me"` for what the user asks in chat (no source needed); `by: "agent"` plus a source and `key` for what a sweep finds.
- Sweep links people and companies as subjects; the brief reads and saves briefs as docs.

## 0.1.0

First release.

- Remote MCP connector (`https://control-center-v1.web.app/mcp`) with Google sign-in and per-user
  storage, so it works in chat on web, desktop and mobile, in Cowork and in Claude Code.
- Eight tools: `cc_dashboard`, `cc_add_loops`, `cc_update_loop`, `cc_list_loops`, `cc_set_today`,
  `cc_goals`, `cc_cursor`, `cc_feedback`.
- Dashboard MCP App: the one thing, goals, Today's 3, needs you, chase, wins, with Done, Snooze and Help.
- Skills: command-center, sweep, brief, setup. Commands: `/brainbox-ceo:sweep`, `brief`, `setup`.
