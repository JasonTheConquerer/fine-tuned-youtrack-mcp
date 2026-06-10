# Jason-MCP skills pack

These are **Claude Code commands** — small Markdown "skills" that turn everyday
YouTrack chores into one-word commands. They run on **your** laptop's Claude CLI and
drive the Jason-MCP server. Install the MCP first (see [`../docs/INSTALL.md`](../docs/INSTALL.md)),
then add these.

## What's in the pack

| Command | What it does |
|---|---|
| `/whatsup` | Quick digest of your YouTrack notifications — mentions, replies, tickets waiting on you. |
| `/stuck-tasks` | Current-sprint tasks stuck in the same status >48h (Squads B/C/D), with reviewer, time-in-status, priority, last comments. |
| `/sprint-summary` | Team → squad → developer summary of a sprint: status at start vs now, unplanned mid-sprint additions; optional HTML/PDF. |
| `/yt-board` | Board snapshot by Stage column, filtered by Squad. |
| `/yt-sprint` | Sprint view + the canonical board URLs. |
| `/yt-standup` | Daily standup snapshot — your open tickets + recent mentions, grouped by Squad. |
| `/yt-inbox` | Notification proxy — issues mentioning you + recent comment activity to respond to. |
| `/yt-similar` | Find past tickets similar to a description, for benchmarking a new task. |

## Install (one minute)

1. Make sure the **Jason-MCP server is installed and connected** as `youtrack`
   (the skills call `mcp__youtrack__*`). See [`../docs/INSTALL.md`](../docs/INSTALL.md).

2. Copy the skill files into your Claude commands folder:

   **Windows (PowerShell):**
   ```powershell
   Copy-Item .\skills\*.md "$env:USERPROFILE\.claude\commands\"
   ```
   **macOS / Linux:**
   ```bash
   cp skills/*.md ~/.claude/commands/
   ```
   (Create `~/.claude/commands/` first if it doesn't exist.)

   > `README.md` and `youtrack.md` are not commands — skip them or just leave the
   > stray copies, Claude ignores non-command Markdown. The real commands are the
   > ones with a `description:` front-matter block.

3. Set your login: copy `skills/youtrack.md` to `~/.claude/youtrack.md` and replace
   `YOUR_LOGIN` with your YouTrack username (or let Claude resolve it with
   `mcp__youtrack__get_current_user`). The personalized commands (`/whatsup`,
   `/yt-standup`, `/yt-inbox`) read it.

4. Restart Claude Code (or start a new session) and type `/` — the commands appear.

## Notes

- All commands are **read-only** — they report, they don't change issues.
- They assume the **Rango Dev** board and Squads A–D. If your board differs, edit the
  queries inside each `.md` (they're plain English + MCP calls).
