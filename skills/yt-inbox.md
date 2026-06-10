---
description: YouTrack notification proxy — show issues mentioning me + recent comment activity I should respond to
---

The official YouTrack MCP doesn't expose the notifications inbox. This command emulates it via mention search and comment timeline.

Load `~/.claude/youtrack.md` if needed. User's login is `YOUR_LOGIN`.

**Query 1 — issues that mention me, updated recently:**
- `mcp__youtrack__search_issues`
- query: `mentions: YOUR_LOGIN updated: {This week}`
- customFieldsToReturn: `["Stage","State","Priority","Assignee"]`
- limit: 20

**Query 2 — issues where I'm assignee with recent comment activity:**
- `mcp__youtrack__search_issues`
- query: `for: YOUR_LOGIN commented: {Today}, {Yesterday}, {Two days ago}, {Three days ago}`
- customFieldsToReturn: `["Stage","State","Priority"]`
- limit: 20

**Query 3 — issues I reported with recent comments by others:**
- `mcp__youtrack__search_issues`
- query: `reporter: YOUR_LOGIN commenter: -YOUR_LOGIN commented: {This week}`
- customFieldsToReturn: `["Stage","State","Assignee"]`
- limit: 20

Run all three in parallel.

**For the top 5 hits combined** (dedupe by ID, prioritize mentions > my-assigned-recent-comments > my-reported-recent-comments), call `mcp__youtrack__get_issue_comments` to pull the actual comment text. Show the last 1-2 comments per issue with author + timestamp, truncated to ~200 chars each.

**Output format:**

```
## YouTrack inbox proxy — <timestamp>

### 🔔 Mentions of me (last 7 days)
| ID | Summary | Who mentioned | When | State |
…

### 💬 My tickets with new comments
| ID | Summary | Last commenter | When | Last comment excerpt |
…

### 📌 Tickets I reported, others replied
| ID | Summary | Replier | When |
…
```

End with: "**Suggested triage:** N items need a response from you" — list the issue IDs that look like they're waiting on user action (mentions without a reply from `YOUR_LOGIN` after the mention; questions in comments addressed to user).

Do not auto-reply — only surface what needs attention.
