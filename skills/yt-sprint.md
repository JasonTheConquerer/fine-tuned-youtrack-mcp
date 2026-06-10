---
description: Sprint view for RANGO board 121-3. Outputs the canonical board URLs (MCP can't fetch sprint contents) + a best-effort Stage+Squad+recent-activity proxy. Usage — /yt-sprint [current|next|previous] [A|B|C|D|all]
---

The user wants a sprint view of RANGO board 121-3. **The MCP cannot read sprint membership** — verified by probe (board/sprint entities not exposed, `Sprint: current` returns junk, `customFields` never contains a Sprint key). So this command does two things:

1. Print the **canonical YouTrack board URL** for the requested sprint (this is the real source of truth — user opens it in browser).
2. Run a **best-effort MCP proxy query** for issues that *might* be in that sprint based on Stage + Squad + recent activity. Disclose loudly that this is a proxy, not ground truth.

**Parse `$ARGUMENTS`:**
- `current` (default), `next`, `previous`
- Squad letter A (default), B, C, D, or `all`

**Sprint ID resolution (best known):**
| Token | Sprint ID | URL fragment |
|---|---|---|
| current | (live) | `/agiles/121-3/current` |
| previous | 122-208 | `/agiles/121-3/122-208` |
| next | 122-210 | `/agiles/121-3/122-210` |

**Squad URL query fragment** (append to sprint URL after `?`):
| Squad | Query |
|---|---|
| A | (none — default) |
| B | `query=%23%7BSquad%20B%7D%20` |
| C | `query=%23%7BSquad%20C%7D%20` |
| D | `query=%23%7BSquad%20D%7D%20` |
| all | (omit squad filter) |

**Proxy query (best-effort substitute):**
Call `mcp__youtrack__search_issues`:
- query: `project: RANGO Stage: {In Progress}, Review, Test, {Ready to Publish}, Backlog` + ` Squad: {Squad <letter>}` (skip squad clause if `all`) + ` updated: {This week}, {Last week}` (for `current`); for `next` use `Stage: Backlog updated: {This week}`; for `previous` use `Stage: Published, Done updated: {Last week}, {Two weeks ago}`.
- customFieldsToReturn: `["Stage","State","Assignee","Priority","Squad","Story Point","Reviewer","QA"]`
- limit: 20

**Output format:**

```
## RANGO Sprint <current|next|previous> — Squad <X>

### 🔗 Real source of truth (open in browser)
<full board URL with squad query if applicable>

### ⚠️ MCP proxy (best-effort, NOT sprint-scoped)
The MCP cannot read sprint membership. The list below is a guess based on Stage + Squad + recent activity. For accuracy use the URL above.

| ID | Summary | Stage | Assignee | Priority | SP |
…

Counts: <Stage breakdown>
Priority: <show-stopper/critical/major counts>
```

Always print the URL FIRST and prominently. The proxy is secondary.
