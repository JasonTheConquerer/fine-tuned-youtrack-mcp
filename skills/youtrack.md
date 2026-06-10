# YouTrack workspace reference (team template)

Context for Claude Code when you ask anything YouTrack-related. Copy this file to
`~/.claude/youtrack.md` and fill in **your** login below. Everything else is shared
by the team and can stay as-is.

## You (FILL THIS IN)

| Field | Value |
|---|---|
| Login | `YOUR_LOGIN` |

> Replace `YOUR_LOGIN` with your YouTrack username. Not sure what it is? Ask Claude
> to run `mcp__youtrack__get_current_user` — the API token authenticates as you, so
> that returns your login. `for: me` in queries also resolves to you.

## Workspace (shared)

| Field | Value |
|---|---|
| Workspace URL | `https://rango.youtrack.cloud` |
| Default board | `Rango Dev` (Scrum, partitioned by Squad A/B/C/D) |
| Issues search UI | https://rango.youtrack.cloud/issues |

## Stage (board column) order

Backlog → In Progress → OnHold → Review → Code Review → Ready to Staging → Test →
Ready to Publish → Published. (The board's own spelling is "Rady to Staging".)

## Sprints — now reachable via Jason-MCP

Unlike the stock YouTrack MCP, **Jason-MCP can read and act on sprints**:
`list_sprints`, `get_sprint_issues`, `move_issue_to_sprint`, `rollover_sprint`,
`start_sprint`, and `sprint_summary`. Prefer these over scraping the board UI.

## Notes

- Squad is a single-enum field; in queries write it braced: `Squad: {Squad B}`.
- People fields (Assignee, Reviewer) come back as lists — take the first entry.
