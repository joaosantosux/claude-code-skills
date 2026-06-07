# Claude Code Skills

A collection of custom skills (slash commands) for [Claude Code](https://claude.ai/code) built from real-world problems.

## How to use a skill

Copy any `.md` file from the `commands/` folder into your global Claude Code commands directory:

**Windows:**
```
C:\Users\<your-username>\.claude\commands\
```

**Mac / Linux:**
```
~/.claude/commands/
```

Then invoke it in any Claude Code session with `/skill-name` (the filename without `.md`).

---

## Available skills

### `/powershell-utf8-fix`
**File:** `commands/powershell-utf8-fix.md`

Diagnoses and fixes UTF-8 encoding corruption in PowerShell 5.1 scripts that send JSON to REST APIs.

Use it when accented characters (á, é, ã, ú, ç) arrive as `?` or `°` on the receiving system (CRM, API, webhook, database). Covers diagnosis, the two-line fix, how to update existing scripts, and the permanent solution (upgrade to PowerShell 7).

---

## Contributing

Have a skill worth sharing? Open a PR with your `.md` file in the `commands/` folder.
