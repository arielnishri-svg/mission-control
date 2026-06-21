---
name: setup
description: Sets up Mission Control for a new user — creates the folder structure, seeds memory and skill files, creates the Drive spine (NOW, DECISIONS, OPEN), and guides through connecting Gmail and Google Calendar. Triggered when user says "set up mission control", "run setup", "install mission control", or "initialise mission control".
---

# Mission Control Setup

Walk the user through a complete Mission Control installation. Do every step in order. Do not skip steps. Confirm completion of each before moving to the next.

## Step 1 — Confirm workspace folder

Tell the user: "I'll create the Mission Control folder structure inside your connected Cowork folder. This will be at ~/Documents/Claude Cowork/cowork/ by default."

Ask them to confirm or provide a different path. Use their answer as BASE_PATH for all subsequent steps.

## Step 2 — Create folder structure

Create the following directories and empty seed files:

```
{BASE_PATH}/
├── memory/
│   ├── global.md          ← create with Step 3 content
│   └── daily-log.md       ← create with Step 4 content
├── projects/
│   ├── daily-digest/
│   │   ├── brief.md       ← empty
│   │   └── memory.md      ← empty
│   └── investments/       ← optional, create but note it
│       ├── portfolio.md   ← create with Step 5 content
│       ├── dashboard.md   ← empty
│       └── memory.md      ← empty
├── skills/                ← empty (skills live in the plugin)
├── autonomous-builder/
│   ├── pending/
│   ├── in-progress/
│   ├── done/
│   ├── failed/
│   └── logs/
│       └── builder-log.md ← create with header only
└── mission-control.md     ← create with Step 6 content
```

## Step 3 — Seed global.md

Ask the user for:
- Their name
- Their primary email (for Gmail connector)
- Their time zone
- Their active projects (one line each)

Write `{BASE_PATH}/memory/global.md` with this content, substituting their answers:

```markdown
# Global Memory

**Last updated:** {TODAY}

## Identity
- **Name:** {NAME}
- **Email:** {EMAIL}
- **Time zone:** {TIMEZONE}

## Tools & environment
- Mac (primary machine)
- Google Calendar + Gmail (personal connectors)
- Cowork + Claude for automation

## Active projects
{PROJECTS}

## Preferences
- Morning brief is manual (say "generate my morning brief"), not scheduled
- British English preferred (adjust to user's preference if they specify)
```

## Step 4 — Seed daily-log.md

Write `{BASE_PATH}/memory/daily-log.md`:

```markdown
# Daily Log

**Rule:** Append a 3-line summary at the end of every Cowork session by saying "wrap up session".

**Format:**
## YYYY-MM-DD
Built/decided: [what was done]
Key decisions: [anything that affects future sessions]
Next: [what to do next time]

---

## {TODAY}
Built/decided: Mission Control installed and configured.
Key decisions: Manual brief trigger (say "generate my morning brief"). Drive spine set up for Chat/Cowork context sharing.
Next: Connect Gmail and Google Calendar if not already done. Fill in portfolio.md if using investments module.
```

## Step 5 — Seed portfolio.md (investments module)

Tell the user: "The investments module is optional. I've created the file — fill it in when you're ready, or ignore it if you don't track investments here."

Write `{BASE_PATH}/projects/investments/portfolio.md`:

```markdown
# Investment Portfolio

**Last updated:** {TODAY}

---

## Philosophy & criteria

> Fill this in: What makes a stock worth holding long-term for you?

- **Horizon:** [e.g. Long-term, 10+ years]
- **Style:** [e.g. Compounders / Growth / Value]
- **Position sizing:** [e.g. Conviction-weighted]
- **Exit criteria:** [e.g. Thesis broken, valuation extreme, need capital]

---

## Current holdings

| Ticker | Qty | Cost basis | Last price | Unrealised G/L | Notes |
|--------|-----|-----------|-----------|----------------|-------|
| — | — | — | — | — | Add your holdings here |

---

## Per-holding thesis

> For each holding, add: entry thesis, current view, price alert, notes.

---

## Watchlist

> Tickers you're tracking but don't own yet.

---

## Price alerts

> Ticker, trigger price, reason.
```

## Step 6 — Seed mission-control.md

Write `{BASE_PATH}/mission-control.md`:

```markdown
# Mission Control
**Installed:** {TODAY}

## How to use

| Say this | What happens |
|----------|-------------|
| "Generate my morning brief" | Pulls Calendar + Gmail → writes brief.md |
| "Research [TICKER]" | Web search + thesis check → appends to dashboard.md |
| "Wrap up session" | Updates Drive spine + daily-log.md |
| "Check the build queue" | Runs the autonomous builder on pending/ |

## Connectors needed
- Gmail — connect in Cowork settings
- Google Calendar — connect in Cowork settings
- Google Drive — needed for spine sync (connect in Cowork settings)

## Key files
- memory/global.md — who you are, your tools, active projects
- memory/daily-log.md — session log, updated by "wrap up session"
- projects/daily-digest/brief.md — today's brief (overwritten each run)
- projects/investments/portfolio.md — holdings + thesis (optional)
```

## Step 7 — Create Drive spine

Tell the user: "Now I'll create the three spine files in your Google Drive. These are what connect this Cowork session to Claude Chat — so you can switch to Opus in Chat and it already knows your context."

Check if Google Drive connector is available. If yes, proceed automatically:

1. Create a folder called `_spine` in their Drive root
2. Create a Google Doc called `NOW` inside `_spine` with current state summary
3. Create a Google Doc called `DECISIONS` inside `_spine` with today's setup decision logged
4. Create a Google Doc called `OPEN` inside `_spine` with starter open items

If Drive is not connected, tell the user: "Connect Google Drive in Cowork Settings → Connectors, then say 'wrap up session' and I'll create the spine files automatically."

Save the Drive file IDs to `memory/global.md` under a `## Drive spine` section so the wrapup skill can find them.

## Step 8 — Guide: connect Gmail and Google Calendar

Tell the user exactly:

"Two connectors are needed for the morning brief:
1. Open Cowork Settings (gear icon)
2. Go to Connectors
3. Find Gmail → click Connect → sign in with your Google account
4. Find Google Calendar → click Connect → same Google account
5. Come back here and say 'generate my morning brief' to test it"

If the connectors are already connected (tools are available in this session), skip this step and tell the user they're already set up.

## Step 9 — Guide: connect Drive spine to Claude Chat

Tell the user:

"Last step — this connects your context to Claude Chat so you can switch to Opus for deep reasoning without losing your setup:

1. Go to claude.ai in your browser
2. Click Projects in the left sidebar
3. Create a new Project — call it 'Mission Control'
4. Inside the project, click Add content → Google Drive
5. Search for NOW, DECISIONS, OPEN and add all three
6. Done. Any Chat session inside this Project reads your current state."

## Step 10 — Confirm and summarise

Tell the user what was created, what still needs their input, and what to say to test each skill:

- "Say **'generate my morning brief'** to test the daily brief"
- "Say **'research AAPL'** to test investment research"
- "Say **'wrap up session'** at the end of any session to keep your context current"
- "Drop a .md file into `autonomous-builder/pending/` to test the builder"
