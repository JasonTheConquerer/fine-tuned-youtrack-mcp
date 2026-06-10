---
description: Daily standup snapshot — my open RANGO/DAP/GAL tickets + recent mentions, grouped by Squad
---

Morning standup for YouTrack. Load `~/.claude/youtrack.md` if not in context. User is `YOUR_LOGIN`.

Run these queries in parallel.

**Query A — my open work:**
- `mcp__youtrack__search_issues`
- query: `for: YOUR_LOGIN State: -Done State: -Published State: -Closed State: -{Won't fix} State: -Duplicate State: -Obsolete`
- customFieldsToReturn: `["Stage","State","Priority","Squad","Assignee"]`
- limit: 20

**Query B — recent mentions of me (notification proxy, last 3 days):**
- `mcp__youtrack__search_issues`
- query: `mentions: YOUR_LOGIN updated: {Today}, {Yesterday}, {Two days ago}`
- customFieldsToReturn: `["Stage","State","Priority","Assignee","Squad"]`
- limit: 20

**Query C — tickets I commented on recently waiting for response:**
- `mcp__youtrack__search_issues`
- query: `commenter: YOUR_LOGIN commented: {This week} State: -Done State: -Published`
- customFieldsToReturn: `["Stage","State","Priority","Assignee"]`
- limit: 10

**Output:**

```
## Standup — <date>

### 🎯 My open work (N total)

#### RANGO — Squad A
| ID | Summary | Stage | Priority |
…

#### RANGO — Squad B/C/D (group whichever squads apply)
…

#### DAP
| ID | Summary | Stage | Priority |
…

#### GAL
| ID | Summary | State | Sprint | Priority |
…

### 🔔 Recent mentions (last 3 days)
| ID | Project | Summary | Who pinged | When |
…

### 💬 Tickets I'm waiting on
| ID | Project | Summary | Last activity |
…
```

Within each group, sort by Priority (Show-stopper → Critical → Major → Normal → Minor). Show ID as `#PROJ-NNNN`.

End with one short summary line:
- N total open across projects
- N show-stoppers/criticals
- N items in "needs response" (mentioned me, I haven't replied since)
- Link: https://rango.youtrack.cloud/notifications for the real notification inbox

If a result set is empty, say so explicitly — don't fake it.

$ARGUMENTS
