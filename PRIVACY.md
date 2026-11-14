# Privacy

The short version: your loops are stored in your own space on BrainBox's Firebase backend, only you
can reach them, and nothing is sold or shared.

- **Who runs it.** BrainBox Labs runs the remote MCP server at `https://control-center-v1.web.app/mcp`
  on Google Firebase (Cloud Functions and Cloud Firestore, United States).
- **Signing in.** You connect with Google sign-in (OAuth). We receive your Google account id, name and
  email to keep your data separate. We get no access to your Gmail, Drive or anything else in that
  account.
- **What we store.** Open loops (title, next step, person, due date, a link or short quote from the
  source), your goals and lanes, Today's 3, sweep cursors, and the feedback you give the command
  center. All of it lives under your account id; every request is scoped to the signed-in user, and no
  other user can read it.
- **What we don't store.** Full emails, transcripts or Slack messages. Claude reads those through the
  connectors you already connected, under their own permissions, and saves only the short loop record.
- **Tokens.** Access tokens expire after one hour; refresh tokens after 60 days. Both are stored only as
  hashes.
- **What we don't do.** No selling or sharing of data, no advertising, no analytics or telemetry, no
  training on your data.
- **Outside requests.** The dashboard loads its fonts (Inter, Instrument Serif) from Google Fonts, so
  Google may see your IP address; no loop data is sent. No scripts or trackers load from other sites. The
  sign-in page loads Google's Firebase sign-in script.
- **Read-only on your tools.** The skills read connectors only. They draft a message only when you ask
  for that specific one and never send without your approval.
- **Feedback you send.** If you send feedback from the dashboard or ask Claude to send it, we store the
  text with your email so we can reply. It's kept until you ask us to delete it.
- **Deleting your data.** Say "delete my data" in Claude, or email the address below, and we delete
  everything under your account within 30 days. Disconnecting the connector in Claude stops all access immediately.
- **Changes.** If this ever changes, the CHANGELOG and this file will say so first.

Contact: hello@brainbox-ai.app
