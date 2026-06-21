---
name: builder
description: Monitors the autonomous-builder/pending/ folder for markdown PRD files. When found, picks up the oldest one, executes the build, saves output to done/, and logs the result. Triggered when user says "check the build queue", "run the builder", "process pending builds", or "what's in the build queue". Also runs automatically on a schedule if a scheduled task has been configured.
---

# Autonomous Builder

Check the build queue and process one PRD file if found. Follow every step in order.

## Step 1 — Check the queue

List files in `{BASE_PATH}/autonomous-builder/pending/`.

If the folder is empty:
- Append to `{BASE_PATH}/autonomous-builder/logs/builder-log.md`: `[YYYY-MM-DD HH:MM] — Queue empty. No action.`
- Tell the user: "Build queue is empty. Drop a .md PRD file into autonomous-builder/pending/ to queue a build."
- Stop here.

If files exist: pick the oldest file (by name prefix date, or creation time). Process one file per run.

## Step 2 — Pick up the PRD

1. Read the PRD file content
2. Copy it to `{BASE_PATH}/autonomous-builder/in-progress/[filename]`
3. Delete the original from `pending/`
4. Append to log: `[timestamp] — Started: [filename]`

## Step 3 — Parse the PRD

Extract from the file:
- **Goal** — what to build
- **Output format** — file type (md, html, docx, etc.)
- **Output filename** — desired name for the output file
- **Data sources** — what to read or search
- **Constraints** — tone, length, format rules

If any field is missing, make a reasonable assumption and note it in the build log.

## Step 4 — Build

Execute the build using whatever tools are needed:
- Read files from the workspace
- Run web searches if data sources require them
- Use Gmail or Calendar connectors if specified
- Write the output file

## Step 5 — Move to done/ or failed/

**On success:**
1. Save output to `{BASE_PATH}/autonomous-builder/done/[output-filename]`
2. Move the PRD from `in-progress/` to `done/[original-prd-filename]`
3. Append to log: `[timestamp] — DONE: [filename] → [output-filename]`
4. Tell the user: "Build complete: [output-filename] is in autonomous-builder/done/"

**On failure:**
1. Move the PRD from `in-progress/` to `failed/[original-prd-filename]`
2. Write `{BASE_PATH}/autonomous-builder/failed/[filename].error.md` with what went wrong
3. Append to log: `[timestamp] — FAILED: [filename] — [reason]`
4. Tell the user: "Build failed: [filename]. Error note saved to autonomous-builder/failed/"

---

## PRD file format

Files dropped into `pending/` should follow this naming convention:
`YYYY-MM-DD-description.md`

Minimal PRD structure:

```markdown
# [Title]

## Goal
[What to build — one paragraph]

## Output
- Format: md / html / docx / xlsx
- Filename: [desired output filename]

## Data sources
- [What to read, search, or pull from connectors]

## Constraints
- [Tone, length, formatting rules]
```

---

## Notes

- Process one PRD per run — do not batch multiple files
- Never delete from `pending/` without first copying to `in-progress/`
- If a PRD is malformed or uninterpretable, move to `failed/` with an error note rather than guessing
- Log every run, including empty-queue checks
- To set up automatic 30-minute checks: go to Cowork Settings → Scheduled Tasks → create a task with prompt "Check the build queue" on a */30 * * * * cron
