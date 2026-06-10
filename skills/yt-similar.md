---
description: Find past YouTrack tickets similar to a description, for benchmarking when drafting a new task. Usage — /yt-similar <keywords or paste a description>
---

The user is about to draft a new YouTrack ticket and wants to benchmark against past completed work. The keywords / description are in `$ARGUMENTS`.

Load `~/.claude/youtrack.md` if needed.

**Step 1 — extract searchable keywords from $ARGUMENTS.** Pick 3-6 concrete terms (feature names, components, API names, bug symptoms). Avoid stopwords. If `$ARGUMENTS` mentions a project key (RANGO/DAP/GAL) explicitly, scope to it; otherwise scope to RANGO.

**Step 2 — search for completed similar tickets.** Run `mcp__youtrack__search_issues`:
- query: `project: <key> <keywords joined with space> State: Done, Published, {Test Result} created: {2025-01-01 .. <today>}`
- customFieldsToReturn: `["Stage","State","Priority","Squad","Story Point","Assignee","Reviewer","QA"]`
- limit: 10

If <3 results, retry with a broader query: drop the date filter and try `State: -Open` instead.

**Step 3 — for top 3 results by relevance, fetch full details.** Call `mcp__youtrack__get_issue` in parallel on the 3 most relevant IDs. Note the description structure (headings used), story points, squad, reviewers, linked issues.

**Step 4 — synthesize a draft template.** Output:

```
## Found N similar past tickets

| ID | Summary | SP | Squad | Reviewer | Resolution |
…

## Pattern across these tickets

- **Typical structure** (headings most past tickets used in their description): …
- **Typical story point range**: X-Y
- **Typical reviewer(s)**: <logins>
- **Typical squad**: <squad>

## Suggested draft for new ticket

### Summary
<one-line suggestion based on $ARGUMENTS>

### Description
<filled-in template using the structural pattern observed>

### Suggested fields
- Stage: Backlog
- Priority: <inferred from similar tickets>
- Squad: <inferred>
- Reviewer: <inferred>
- Story Point: <inferred range>
```

Do **not** call `create_issue` yet. Always show the draft and ask the user to confirm before any write operation.
