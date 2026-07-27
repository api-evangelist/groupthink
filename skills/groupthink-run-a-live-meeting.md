---
name: Run a live meeting with the Groupthink bot
description: Send the Groupthink notetaker bot into a live Zoom/Meet/Teams meeting, follow the transcript, optionally participate, and save notes.
api: mcp/groupthink-mcp.yml
operations: [join_meeting, bot_status, get_transcript, send_chat, speak, save_notes, get_notes, leave_meeting]
---

# Run a live meeting with the Groupthink bot

Use Groupthink's remote MCP server (`https://api.groupthink.com/v1/mcp-server`) to
put a notetaker bot into a live meeting and work the transcript in real time.

## Authentication
All calls require a Bearer API token minted in the Groupthink app
(Settings -> API Tokens). See `authentication/groupthink-authentication.yml`.

## Steps
1. **Join** — call `join_meeting` with the Zoom, Google Meet, or Microsoft Teams
   meeting URL.
2. **Confirm** — poll `bot_status` until the bot reports it is in the meeting.
3. **Follow the transcript** — call `get_transcript` on a loop; it uses a
   per-user cursor and returns only new lines each call, so you never
   re-process what you have already seen.
4. **Participate (optional)** — use `send_chat` to post a message (max 2000
   characters) or `speak` to have the bot say something aloud. `speak` is
   spend-capped, so use it sparingly.
5. **Save notes** — call `save_notes` to persist your notes. It is **idempotent**,
   so re-calling with the same notes will not create duplicates; retry safely on
   any transport error.
6. **Retrieve** — call `get_notes` to read back the saved notes.
7. **Leave** — call `leave_meeting` when the meeting ends.

## Rules
- Advance the `get_transcript` cursor rather than re-fetching from the start.
- Treat `save_notes` as safe to retry (idempotent); do not treat `speak` the
  same way — repeated `speak` calls both cost spend and talk over the room.
