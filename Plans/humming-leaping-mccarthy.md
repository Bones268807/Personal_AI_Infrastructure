# Plan: Full Jarvis Experience in Obsidian

## Context

Nick wants to open the Obsidian Jarvis terminal and immediately be talking to Jarvis with full PAI modes — no typing `claude`, no broken permissions. When Jarvis edits vault files, Nick wants transparency: a backup of the original, a before/after diff shown in VS Code, and an audit that nothing important was lost.

## Changes (3 files)

### 1. `~/.zshrc` — Add `jarvis` alias

```bash
alias jarvis='claude'
```

Add near the existing PAI aliases (line ~18).

### 2. `~/Documents/GitHub/obsidian-jarvis-terminal/src/main.ts` — Auto-launch Claude Code

In `spawnShell()`, after the PTY listeners are connected, auto-send `claude` to the shell:

```typescript
// After child.onExit listener, add:
setTimeout(() => {
  child.write("claude\n");
}, 500);
```

Then rebuild and copy to plugin dir.

### 3. `~/VOCFS/.claude/settings.json` — Unlock PAI permissions

Replace the read-only permission set with full PAI tools:

```json
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep", "Write", "Edit", "Skill",
      "WebFetch", "WebSearch", "TodoWrite", "ExitPlanMode",
      "Bash", "mcp__*"
    ]
  },
  "env": {
    "VOCFS_DIR": "/Users/spar-voc/VOCFS"
  }
}
```

### 4. `~/VOCFS/.claude/CLAUDE.md` — Add vault edit safety protocol

Append to the existing VOCFS CLAUDE.md:

```markdown
## Vault Edit Safety Protocol

When editing ANY file inside this vault:

1. **Backup first** — Copy the original to `~/VOCFS/.claude/backups/YYYY-MM-DD/original-filename.md` before making changes
2. **Make the edit**
3. **Audit** — Compare backup vs edited file. Confirm no important information was removed (frontmatter fields, cross-references, citations, legal content)
4. **Open side-by-side diff in VS Code** — Run `code --diff <backup_path> <edited_path>` so Nick can see before/after in VS Code's diff viewer
5. **Report** — State what was added, changed, and preserved
```

This gives Nick full transparency: Jarvis writes freely, but every vault edit produces a backup and opens VS Code with a side-by-side diff so he can see exactly what changed.

## Build & Install

```bash
cd ~/Documents/GitHub/obsidian-jarvis-terminal
npm run build
cp main.js styles.css manifest.json ~/VOCFS/.obsidian/plugins/jarvis-terminal/
```

Then reload Obsidian or toggle the plugin off/on.

## Verification

1. Open any terminal → type `jarvis` → Claude Code launches
2. Open Obsidian → click Jarvis icon → Claude Code auto-starts
3. Say "hello" → MINIMAL mode response with `🗣️ Jarvis:` format
4. Ask Jarvis to edit a vault note → should see backup created, diff shown, audit reported
