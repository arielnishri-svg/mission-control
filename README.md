# Mission Control

A personal OS plugin for Claude Cowork. Unifies your daily calendar, inbox, and investments into a single morning brief — and keeps your context alive when you switch between Cowork and Claude Chat.

Built by [Ariel Nishri](https://cogniqos.substack.com).

---

## What it does

| Say this | What happens |
|----------|-------------|
| `set up mission control` | One-time setup: creates folder structure, seeds files, connects Drive spine |
| `generate my morning brief` | Pulls Google Calendar + Gmail → writes a structured daily brief |
| `research AAPL` | Web search + analyst data + thesis fit check → research note saved to dashboard.md |
| `wrap up session` | Updates Drive spine (NOW / DECISIONS / OPEN) + daily-log.md |
| `check the build queue` | Picks up PRDs from pending/ and builds them |

---

## The spine — what makes this different

Most AI context lives and dies in a single session. Mission Control uses three small Google Drive files as a shared memory layer across Claude Cowork, Claude Chat, and eventually Claude Code:

- **NOW.md** — what's active today, current state of every project
- **DECISIONS.md** — append-only log of locked decisions and their reasoning
- **OPEN.md** — live list of unresolved questions and next steps

Cowork writes to these files. Claude Chat reads them (via a Claude Project connected to Drive). You switch to Opus in Chat for deep reasoning — and it already knows your world without you re-explaining anything.

---

## Setup

### 1. Install the plugin
Install this `.plugin` file in Cowork (Settings → Plugins → Install from file).

### 2. Connect your Google services
In Cowork Settings → Connectors:
- Connect **Gmail**
- Connect **Google Calendar**
- Connect **Google Drive**

### 3. Run setup
In a new Cowork session, say: `set up mission control`

The setup skill will:
- Create your folder structure
- Ask for your name, email, time zone, and active projects
- Seed your memory and skill files
- Create the Drive spine (NOW, DECISIONS, OPEN)

### 4. Connect Drive to Claude Chat
In [claude.ai](https://claude.ai):
1. Go to Projects → New Project → name it "Mission Control"
2. Click Add content → Google Drive
3. Add the NOW, DECISIONS, and OPEN docs
4. Use this Project for any Chat session where you want context continuity

---

## Connectors required

| Connector | Used by | Required? |
|-----------|---------|-----------|
| Gmail | Morning brief (inbox priorities) | Yes for brief |
| Google Calendar | Morning brief (today's events) | Yes for brief |
| Google Drive | Drive spine (NOW / DECISIONS / OPEN) | Yes for cross-tool context |

---

## Folder structure created on setup

```
~/Documents/Claude Cowork/cowork/
├── memory/
│   ├── global.md           # Cross-project context (not committed to git)
│   └── daily-log.md        # Session log (not committed to git)
├── projects/
│   ├── daily-digest/
│   │   └── brief.md        # Today's brief (overwritten daily)
│   └── investments/        # Optional module
│       ├── portfolio.md    # Holdings + thesis
│       └── dashboard.md    # Research outputs
├── autonomous-builder/
│   ├── pending/            # Drop PRDs here
│   ├── done/               # Completed outputs
│   ├── failed/             # Failed builds with error notes
│   └── logs/               # Build history
└── mission-control.md      # System reference
```

---

## The investments module (optional)

Fill in `projects/investments/portfolio.md` with your holdings and investing philosophy. The `research` skill reads this file to check whether a stock fits your criteria before writing its verdict.

You don't need to use this module — the brief and builder work without it.

---

## The autonomous builder

Drop a markdown file into `autonomous-builder/pending/` named `YYYY-MM-DD-description.md`:

```markdown
# Weekly Portfolio Summary

## Goal
Write a one-page summary of my portfolio performance for the week.

## Output
- Format: md
- Filename: 2026-06-28-portfolio-week.md

## Data sources
- Read projects/investments/portfolio.md
- Web search current prices for each ticker

## Constraints
- One page, concise, no filler
```

Say `check the build queue` (or set up a 30-minute scheduled task) and it builds while you're away.

---

## Tips

- **"wrap up session"** is the most important habit. A stale NOW.md means Opus in Chat reasons from wrong premises. 30 seconds at the end of each session keeps the whole system honest.
- **Investments are optional.** The brief and builder work without portfolio.md. Add investments when you're ready.
- **The builder is off by default.** Enable scheduled checks in Cowork Settings → Scheduled Tasks when you're comfortable with how it works.

---

## What this is NOT

- Not a broker integration (prices come from web search, not your account)
- Not a meeting prep tool for Microsoft/Outlook calendars (Google Calendar only)
- Not a code assistant (use Claude Code for that)
- Not a replacement for your own judgment on investments

---

## License

MIT. Fork it, adapt it, publish your own version.
