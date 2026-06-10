---
description: "Sprint summary — team/squad/developer breakdown for a sprint (status at start vs now, unplanned additions). Usage — /sprint-summary [Sprint #NN] [pdf|html]"
---

Produce a **sprint summary**: for one sprint, each task's status **at the sprint
start** vs **now**, the progress made, the **unplanned** tasks added mid-sprint
(count + story points), broken down for the **team**, each **squad**, and each
**developer** (tasks assigned to them + where they are; tasks/points they review +
where). Epic is excluded. Trigger phrases: "sprint summary", `/sprint-summary`.

Board is **Rango Dev** (set `YOUTRACK_DEFAULT_BOARD`). Use the connected YouTrack
MCP (`mcp__youtrack__*`).

**Parse `$ARGUMENTS`:**
- A sprint name like `Sprint #91` → that sprint. Default: the board's current sprint.
- `pdf` or `html` anywhere → also render a file (see below). Default: just the tables.

## Steps

**1 — Get the data.** Call `mcp__youtrack__sprint_summary` (omit `sprint` for the
current one, or pass `sprint="Sprint #91"`). It returns the board summary, per-squad
summaries (each with a per-developer breakdown), and the unplanned list.

**2 — Show it as Markdown tables**, in this shape:

```
## Sprint Summary — <sprint> · <start> → <finish>

### Team (Epic excluded)
- Tasks: N (planned P, unplanned U) · Points: PTS (done D) · Completion X%

| Status | At start | Now | Δ |   ← order: Backlog, In Progress, OnHold, Review, Code Review, Ready to Staging, Test, Ready to Publish, Published

**Per-squad rollup:** | Squad | Tasks | Points | Done | Unplanned | Completion |

**Unplanned mid-sprint:** | Task | Squad | Status | Points |

### Squad B / C / D …
(KPIs, the same start-vs-now table, then per developer:)
**<Dev>** — assigned A (Apts), reviewing R (Rpts)
| Status | Assigned | Asg pts | Reviewing | Rev pts |
```

**3 — Optional file render.** If the user asked for `pdf` or `html`, run the
matching repo script (read-only) from the MCP repo root, writing to the user's
Desktop:
- HTML: `python scripts/sprint_summary_html.py "<desktop>/sprint-summary"`
- PDF:  `python scripts/sprint_summary_pdf.py  "<desktop>/sprint-summary"`
  (append the sprint name as a second arg for a past sprint, e.g. `"Sprint #90"`).
Then report the output path.

Read-only. Never modify issues — just report.
