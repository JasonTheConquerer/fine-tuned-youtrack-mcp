---
description: Show RANGO board snapshot by Stage column, optionally filtered by Squad. Usage — /yt-board [A|B|C|D|all] [full]
---

Render a board snapshot for RANGO board 121-3. Load `~/.claude/youtrack.md` if not already in context.

**Important honesty:** the MCP cannot fetch sprint membership. This command shows Stage columns (the actual board columns) filtered by Squad — it does **not** know whether each issue is in the current sprint, next sprint, or backlog. Disclose that in the output.

**Parse `$ARGUMENTS`:**
- Squad letter (A/B/C/D) → squad filter. Default: A. `all` → no squad filter.
- The word `full` anywhere → include Backlog, OnHold, Published columns. Default: only active columns.

**Active columns (default):** In Progress, Review, Test, Ready to Publish
**Full columns:** Backlog, In Progress, Review, Test, Ready to Publish, OnHold

**For each column**, call `mcp__youtrack__search_issues` in parallel:
- query: `project: RANGO Stage: {<column name>}` + (if squad filter set) ` Squad: {Squad <letter>}`
- customFieldsToReturn: `["Stage","State","Assignee","Priority","Squad","Story Point","Reviewer","QA"]`
- limit: 20

**Output format:**

```
## RANGO board — Squad <X> — <timestamp>

> ⚠️ Stage columns shown (board view). Sprint membership not visible via MCP — for current/next sprint contents open: https://rango.youtrack.cloud/agiles/121-3/current

### 🔄 In Progress (N)
| ID | Summary | Assignee | Priority | SP | Reviewer |
…

### 👀 Review (N)
…

### 🧪 Test (N)
…

### 🚀 Ready to Publish (N)
…
```

(Emojis allowed in this command since it's a board scan, not code.)

End with:
- Total open items
- Priority breakdown (Show-stoppers / Criticals / Majors counts)
- Anomalies: columns >10 issues, columns at 0, items with no Assignee, items with Priority Show-stopper not in In Progress
- The board URL for the squad shown: `https://rango.youtrack.cloud/agiles/121-3/current` (Squad A) or with `?query=%23%7BSquad%20<X>%7D%20` for B/C/D
