---
name: today
description: Generates a structured morning brief by pulling live data from Google Calendar and Gmail, reading the user's portfolio file for investment context, and saving the output to projects/daily-digest/brief.md. Triggered when user says "generate my morning brief", "what's my day look like", "run today", "today's brief", or "morning brief".
---

# Today — Morning Brief

Generate a structured daily brief from live data. Follow every step in order.

## Step 1 — Pull today's calendar events

Call `list_events` on Google Calendar:
- `startTime`: today at 00:00:00 in the user's local time zone (read from memory/global.md)
- `endTime`: today at 23:59:59
- `pageSize`: 25

If Google Calendar is not connected, note "Calendar not connected" in the brief and continue.

## Step 2 — Pull inbox priorities

Call `search_threads` on Gmail:
- Query: `is:unread in:inbox`
- Page size: 10
- View: THREAD_VIEW_MINIMAL

Filter results: keep only threads that appear to require human action. Discard automated notifications (OAuth grants, security alerts, newsletter subscriptions, marketing emails) unless they contain a time-sensitive action item.

If a thread needs more context to assess, call `get_thread` on its ID.

If Gmail is not connected, note "Gmail not connected" in the brief and continue.

## Step 3 — Read portfolio context

Read `{BASE_PATH}/projects/investments/portfolio.md`.

If the file exists and has holdings, note any positions that are underwater (negative unrealised gain/loss) or flagged with price alerts.

If the file is empty or does not exist, skip the portfolio pulse section entirely.

Do NOT run web searches for live prices in this step — portfolio pulse is a quick flag, not a full research run.

## Step 4 — Carry forward open action items

Read `{BASE_PATH}/projects/daily-digest/brief.md`.

Extract any action items marked `- [ ]` (unchecked) from the previous brief. Carry them forward into today's brief.

## Step 5 — Compose and write the brief

Write `{BASE_PATH}/projects/daily-digest/brief.md` with this exact structure:

```markdown
# Morning Brief — [Weekday, DD Month YYYY]

---

## Portfolio pulse
[If portfolio.md has holdings: one line per holding — ticker, status (up/down/flat), flag if underwater or alerting. Note: "Prices not refreshed — say 'research [TICKER]' for a deep dive."]
[If no holdings file: omit this section]

## Today's calendar
[Each event on one line: HH:MM — Event name (duration). Mark past events with ~~strikethrough~~.]
[If no events: "No events today."]

## Inbox priorities
[3–5 threads requiring action, one line each: sender name + what action is needed]
[Ignore automated notifications, OAuth confirmations, newsletters]
[If inbox is clear: "Inbox clear."]

## Action items
- [ ] [Carry-forwards from yesterday]
- [ ] [New items from inbox or calendar]

---
*Generated: [datetime] | Sources: Google Calendar, Gmail (live)*
```

## Step 6 — Display and confirm

Display the brief in chat.

Tell the user: "Brief saved to projects/daily-digest/brief.md. Say 'today update [note]' to append a note without regenerating."

---

## Variant: "today update [note]"

When triggered with an update note (e.g. "today update Decided to hold GILT"):

1. Read `{BASE_PATH}/projects/daily-digest/brief.md`
2. Append under a `### Updates` section at the bottom: `[HH:MM] — [note]`
3. Save the file
4. Reply: "Added to today's brief."

---

## Notes

- Time zone is read from memory/global.md. Default to UTC if not found.
- Inbox summaries should be in the user's language. If an email is in a different language, note it in brackets (e.g. "[Hebrew]").
- Portfolio pulse is intentionally shallow — the research skill handles deep dives.
- If both Calendar and Gmail are disconnected, generate the brief with portfolio + action items only and note the missing connectors.
