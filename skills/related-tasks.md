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

**2 — Render a table:**

```
## 🔎 Related tasks — "<subject>" — <N> found

| Task | Summary | Assignee | Status | Sprint | Last comments |
|------|---------|----------|--------|--------|---------------|
| `RANGO-1234` | short summary | Sina | In Progress | Sprint #91 | *Ehsan*: "…" · *Sina*: "…" |
```

- **Task** = the issue id as a link: `[RANGO-1234](<workspace-url>/issue/RANGO-1234)`
  (workspace URL is in `~/.claude/youtrack.md`).
- **Summary** truncated to ~50 chars.
- **Sprint** = the sprint name, or "—" if it isn't in an active sprint.
- **Last comments** = up to the last 5, newest last, each `*author*: "excerpt"`
  (truncate each to ~150 chars). If a row's comments get long, you may instead list
  them under the table per issue.

End with one line: **"N related tasks for '<subject>'."** If nothing matches:
**"No tasks found about '<subject>'."**

Read-only. Just report — never change issues.
