---
description: "What's up? — quick digest of my YouTrack notifications: mentions, replies, and tickets waiting on me"
---

A fast, casual "what's up" check of my YouTrack notifications. The official MCP
doesn't expose the notifications inbox, so this emulates it via mention search +
comment timeline.

Your login: read it from `~/.claude/youtrack.md` (the `Login` field). If it's still
`YOUR_LOGIN` there, resolve it once with `mcp__youtrack__get_current_user` and use that.
Substitute your real login everywhere `YOUR_LOGIN` appears below.

Run these three searches **in parallel**:

**1 — Mentions of me this week:**
- `mcp__youtrack__search_issues`
- query: `mentions: YOUR_LOGIN updated: {This week}`
- customFieldsToReturn: `["Stage","State","Priority","Assignee"]`
- limit: 20

**2 — My tickets with fresh comments:**
- `mcp__youtrack__search_issues`
- query: `for: YOUR_LOGIN commented: {Today}, {Yesterday}, {Two days ago}, {Three days ago}`
- customFieldsToReturn: `["Stage","State","Priority"]`
- limit: 20

**3 — Tickets I reported, others replied:**
- `mcp__youtrack__search_issues`
- query: `reporter: YOUR_LOGIN commenter: -YOUR_LOGIN commented: {This week}`
- customFieldsToReturn: `["Stage","State","Assignee"]`
- limit: 20

For the **top 5 combined hits** (dedupe by ID; priority: mentions > my-tickets-new-comments
> reported-with-replies), call `mcp__youtrack__get_issue_comments` and show the last
1–2 comments with author + timestamp, truncated to ~200 chars.

**Output — keep it short and scannable:**

```
## 👋 What's up — <timestamp>

🔔 **<N> mentions** · 💬 **<N> new replies on my tickets** · 📌 **<N> replies on what I reported**

### 🔔 Mentioned me
- `ID` Summary — *who*, when · State

### 💬 New on my tickets
- `ID` Summary — last: *who* "excerpt…"

### 📌 I reported, they replied
- `ID` Summary — *who*, when
```

End with one line: **"⚡ Needs you: ID, ID, ID"** — the items waiting on a response from
`YOUR_LOGIN` (mentions with no reply from me after the mention; direct questions in comments).

If everything is quiet, just say: **"✅ All clear — nothing waiting on you."**

Read-only. Never auto-reply — only surface what needs attention.
