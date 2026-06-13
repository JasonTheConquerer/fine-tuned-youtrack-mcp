---
description: "Related tasks — search YouTrack for issues about a subject and show the latest few with assignee, status, sprint, and recent comments. Usage — /related-tasks <subject>"
---

When someone asks about a subject — "what's been done about X", "any tickets on the
swapper whitelist", "show me tasks about the login crash" — find the most relevant
recent issues and lay them out in a table. Trigger phrases: "related tasks",
"related tickets", `/related-tasks <subject>`.

Use the connected YouTrack MCP (`mcp__youtrack__*`).

## Steps

**1 — Call the tool.** `mcp__youtrack__related_issues`:
- `subject`: the user's keywords (everything after the command / the thing they're asking about).
- `limit`: 5 (the 5 most recently-updated matching issues). Raise it only if they ask for more.
- `comments`: 5 (last 5 comments per issue).
- `project`: `RANGO` by default (drop or change if they name another project).

It returns each issue already enriched with `assignee`, `status`, `sprint`, and
`comments` (newest issues first), so you don't need extra calls.

**2 — Render a table (chat / Markdown):**

```
## 🔎 Related tasks — "<subject>" — <N> found

| Task | Assignee | Status | Sprint | Last comments |
|------|----------|--------|--------|---------------|
| Gaszip Aggregator — RANGO-5268 | Amin | Ready to Publish | Sprint #91 | *Hossein*: "…" · *Ali*: "…" |
```

- **Task** = the task **name** followed by its **number**, plain text — e.g.
  `Gaszip Aggregator — RANGO-5268`. **Do NOT make it a clickable link in chat.**
- **Sprint** = the sprint name, or "—" if it isn't in an active sprint.
- **Last comments** = up to the last 5, newest last, each `*author*: "excerpt"`
  (truncate each to ~150 chars). If a row's comments get long, list them under the
  table per issue instead.

End with one line: **"N related tasks for '<subject>'."** If nothing matches:
**"No tasks found about '<subject>'."**

**3 — HTML version (only if the user asks for HTML / a file).** Run the repo script
(read-only); in the HTML the task name+number **is** a hyperlink to the issue:
- `python scripts/related_tasks_html.py "<subject>" "<desktop>/related-tasks" RANGO`
Then report the output path.

Read-only. Just report — never change issues.
