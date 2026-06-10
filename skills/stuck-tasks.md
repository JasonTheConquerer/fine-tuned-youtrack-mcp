---
description: "List stuck tasks — current-sprint tasks sitting in the same status >48h (Squads B/C/D), with reviewer, time-in-status, priority, and last comments"
---

Find **stuck tasks**: tasks in the current sprint that have been sitting in their
current status (Stage) for **more than 48 hours**. Trigger phrases: "list stuck tasks",
"stuck tasks", `/stuck-tasks`.

Board is **Rango Dev**. Default squads: **Squad B, Squad C, Squad D** (override if the
user names specific squads, e.g. "stuck tasks in squad A").

Use whichever YouTrack MCP server is connected (e.g. `mcp__youtrack__*` /
`mcp__youtrack-mine__*`).

## Rules

- **Stuck = strictly more than 48h** in the *current* Stage.
- **Excluded stages (never stuck):** `Published` and `Backlog`. Everything else is
  eligible — including **`OnHold`** (OnHold >48h IS stuck).
- "Time in current status" = now − timestamp of the **last Stage change**. If the task
  has never changed Stage, use its **creation** time.

## Steps

**1 — Pull the candidate tasks (one call).**
`search_issues` with:
- query: `Board Rango Dev: {current sprint} Squad: {Squad B}, {Squad C}, {Squad D}`
  - (adjust the `Squad: {…}` list if the user named specific squads; squad values MUST
    be written `{Squad B}`, not `B`)
- customFieldsToReturn: `["Stage","Priority","Assignee","Reviewer","Squad"]`
- limit: 200

Drop any task whose **Stage is `Published` or `Backlog`**. Note: Assignee and Reviewer
come back as **lists** (multi-user) — take the first name, or "—" if empty.

**2 — For each remaining task, get time-in-status + comments (one call each).**
Call `get_issue_history(issue_id)`. From the returned chronological `history` list:
- **Time in status:** find the LAST entry with `type == "Change"` and `field == "Stage"`;
  use its `date`. If there is no Stage change, use the `type == "Created"` entry's `date`.
  Compute hours since then (and days).
- **Last comments:** take the last 3 entries with `type == "Comment"`; keep `who` + `text`
  (truncate each to ~150 chars, collapse newlines).

Keep only tasks where **hours in status > 48**.

Run the history calls in parallel batches where possible (there can be ~80 candidates;
only the >48h ones make the final table). Tell the user roughly how many you scanned.

**3 — Output a table**, sorted by Squad, then by hours-in-status descending:

```
## 🚧 Stuck tasks — Sprint <name> · Squads B/C/D · >48h in status — <timestamp>

| Task | Developer | Reviewer | Status | In status | Priority | Last comments |
|------|-----------|----------|--------|-----------|----------|---------------|
| `RANGO-1234` Short summary | Sina Rezaei | — | OnHold | 8.5d (204h) | Major | *Ehsan*: "…" · *Sina*: "…" |
```

- **Task** = `ID` + short summary (truncate summary to ~50 chars).
- **In status** = days + hours, e.g. `3.2d (77h)`.
- **Last comments** = up to 3, newest last, each `*author*: "excerpt"`; show "—" if none.

End with a one-line summary: **"N stuck tasks across Squads B/C/D (scanned M)."**
If nothing is stuck: **"✅ No stuck tasks — everything's moved in the last 48h."**

Read-only. Do not change any task; just report.
