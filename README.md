# Claude Skills

A collection of reusable Claude Code skills for design-to-code workflows.

## Skills

### `/figma-sync`

Two-way sync between a localhost web app and Figma. Handles project setup, capturing screens to Figma, syncing Figma edits back to code, wiring prototype connections, and capturing multiple screens.

**Install:**

```bash
mkdir -p ~/.claude/skills/figma-sync
curl -o ~/.claude/skills/figma-sync/SKILL.md https://raw.githubusercontent.com/cristianvaldes/claude-skills/main/figma-sync/SKILL.md
```

**Then in Claude Code, say:**
- `setup figma sync` — new project from scratch
- `capture to figma` — push current screen to Figma
- `sync changes` — pull Figma edits into code
- `wire prototype` — connect screens with click interactions

**Prerequisites:**
- [Claude Code](https://claude.ai/code)
- [Figma MCP Server](https://www.figma.com/developers/mcp) connected in Claude Code settings
