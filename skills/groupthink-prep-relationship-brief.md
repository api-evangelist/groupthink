---
name: Prepare a relationship brief before a meeting
description: Pull upcoming meetings from Groupthink, look up the people involved, summarize relationship health and history, and record prep notes.
api: mcp/groupthink-mcp.yml
operations: [get_upcoming_meetings, list_relationships, search_relationships, get_relationship, get_relationship_insights, get_past_meetings, upsert_relationship_note]
---

# Prepare a relationship brief before a meeting

Use Groupthink's remote MCP server (`https://api.groupthink.com/v1/mcp-server`) to
build a pre-meeting brief on the people you are about to meet.

## Authentication
All calls require a Bearer API token minted in the Groupthink app
(Settings -> API Tokens). See `authentication/groupthink-authentication.yml`.

## Steps
1. **Find the meeting** — call `get_upcoming_meetings` to list what is coming up
   with its relationship context.
2. **Resolve the people** — for each attendee, call `get_relationship` by email
   or ID. If you only have a name or a partial detail, use `search_relationships`
   (name, email, tags, details) or scan `list_relationships` for contact-frequency
   status.
3. **Assess health** — call `get_relationship_insights` for a health summary and
   whether the person is due for outreach.
4. **Review history** — call `get_past_meetings` (default last 7 days, max 30) to
   see recent interactions and what was decided.
5. **Record prep** — call `upsert_relationship_note` to write your talking points
   onto the relationship. This is an upsert keyed to the relationship, so
   re-running updates the same record rather than duplicating it.

## Rules
- Prefer `get_relationship` by email/ID for an exact match; fall back to
  `search_relationships` only when you lack an identifier.
- `upsert_relationship_note` is idempotent by relationship key — safe to retry.
- Respect the `get_past_meetings` 30-day ceiling; do not request a longer window.
