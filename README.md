# Tilt Prototyping Skill Stack

A set of Claude Code skills that take a feature from problem statement to lo-fi prototype to stakeholder feedback to a polished HTML prototype plus native iOS and Android code ready for engineers. Built for Tilt, but the patterns apply to any mobile app team that wants to compress the design-to-handoff cycle.

---

## Prerequisites

- [Claude Code](https://claude.ai/code) — required
- [Figma MCP Server](https://www.figma.com/developers/mcp) connected in Claude Code settings — required for `lofi-prototype` Figma capture and `hifi-prototype` Figma references
- Linear MCP, Notion MCP, Slack MCP — optional but recommended; these unlock `prototype-agent`'s full context-gathering and tracking features (see [MCP Server Setup](#mcp-server-setup))

---

## Install

### Install everything (recommended)

Run this block once to install all four skills:

```bash
mkdir -p ~/.claude/skills/prototype-agent
curl -o ~/.claude/skills/prototype-agent/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/prototype-agent/SKILL.md

mkdir -p ~/.claude/skills/lofi-prototype
curl -o ~/.claude/skills/lofi-prototype/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/lofi-prototype/SKILL.md

mkdir -p ~/.claude/skills/hifi-prototype
curl -o ~/.claude/skills/hifi-prototype/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/hifi-prototype/SKILL.md

mkdir -p ~/.claude/skills/figma-sync
curl -o ~/.claude/skills/figma-sync/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/figma-sync/SKILL.md
```

### A la carte installs

**prototype-agent**
```bash
mkdir -p ~/.claude/skills/prototype-agent
curl -o ~/.claude/skills/prototype-agent/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/prototype-agent/SKILL.md
```

**lofi-prototype**
```bash
mkdir -p ~/.claude/skills/lofi-prototype
curl -o ~/.claude/skills/lofi-prototype/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/lofi-prototype/SKILL.md
```

**hifi-prototype**
```bash
mkdir -p ~/.claude/skills/hifi-prototype
curl -o ~/.claude/skills/hifi-prototype/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/hifi-prototype/SKILL.md
```

**figma-sync**
```bash
mkdir -p ~/.claude/skills/figma-sync
curl -o ~/.claude/skills/figma-sync/SKILL.md \
  https://raw.githubusercontent.com/tiltCristianValdes/claude-skills/main/figma-sync/SKILL.md
```

---

## The Stack at a Glance

```
prototype-agent
  └── Phase 1: context gathering    →  pulls from Linear, Notion, Slack (if connected)
  └── Phase 2: lofi-prototype       →  grayscale interactive HTML prototype
  └── Phase 3: figma-sync           →  push to Figma for design review (optional)
  └── Phase 4: hifi-prototype       →  polished HTML + SwiftUI + Compose + design tokens + QA + handoff doc
```

You can run the whole chain through `prototype-agent`, or drop into any individual skill directly depending on where you are in the process.

---

## Three Ways to Use It

### Option A: Full workflow (recommended for new features)

Use `prototype-agent` when starting from a problem statement. It orchestrates all four phases, gathers context from your tools, and produces the complete engineer handoff package.

In Claude Code, paste a prompt like this:

```
Run the prototyping workflow for a new "Spending Insights" feature.
The problem: users don't know where their money is going each month.
Target user: Tilt credit card holder, 25-35, checks the app weekly.
Learning goal: Does a spending breakdown by category feel useful, or overwhelming?
```

What happens across the four phases:

| Phase | What Claude does | Output |
|-------|-----------------|--------|
| 1. Context | Pulls relevant Linear tickets, Notion PRDs, and Slack threads | Summarized brief |
| 2. Lo-fi | Builds a grayscale interactive HTML prototype | Single `.html` file |
| 3. Figma (optional) | Pushes prototype screens to Figma for async design review | Figma frames |
| 4. Hi-fi | Graduates the validated prototype to a full engineer package | HTML + SwiftUI + Compose + tokens + QA + handoff doc |

At the end you have everything an engineer needs to build: working reference HTML, native code for both platforms, a design token file, a QA report, and a handoff document.

---

### Option B: Quick lofi (just need a prototype fast)

Use `lofi-prototype` directly when you need a clickable prototype quickly and don't need the full workflow overhead.

```
Build a lofi prototype for a credit card payment flow.
Screens: Payment amount selector, confirmation, success.
Dark screens throughout. Single primary CTA on each screen.
```

Output: a single `.html` file you can open in a browser and click through. No dependencies, no build step.

---

### Option C: Graduate an existing lofi to hi-fi

Use `hifi-prototype` directly when you already have a validated lofi and want to produce the engineer package.

```
Graduate the lo-fi prototype at ~/Desktop/my-feature-prototype.html to hi-fi.
Use tilt-app.html at ~/Desktop/tilt-app.html as the brand reference.
```

Output:

- `prototype.html` — polished, fully interactive HTML
- `[Feature]View.swift` — SwiftUI implementation for iOS
- `[Feature]Screen.kt` — Jetpack Compose implementation for Android
- `design-tokens.json` — shared color, typography, and spacing tokens
- `HANDOFF.md` — notes, decisions, and open questions for the engineering team
- `QA-report.md` — checklist of behaviors to verify before shipping

---

## Sample Asset: CC Cancellation

The `samples/cc-cancellation/` folder contains a ready-to-use example:

- `cc-cancellation-flow.png` — hi-fi design showing all 9 screens and the decision logic between them
- `BRIEF.md` — full screen inventory, navigation graph, and three copy-paste prompts to try the flow

This covers the full CC account cancellation flow including four edge cases (outstanding balance, subscription confirmation, pending transaction, credit balance). Use it to try any of the three options below.

---

## Try It: Chain Option B into Option C

The fastest way to see both skills in action is to run them back to back using the CC Cancellation sample.

**Step 1** — Generate a lofi with Option B:

```
Build a lofi prototype for the CC Cancellation flow.
Reference: samples/cc-cancellation/BRIEF.md
9 screens. Light theme throughout. Include all four edge case paths.
```

Claude will output a file like `cc-cancellation-prototype.html`. Open it in a browser to click through it.

**Step 2** — Graduate it to hi-fi with Option C:

```
Graduate the lo-fi prototype at ~/Desktop/cc-cancellation-prototype.html to hi-fi.
Visual reference: samples/cc-cancellation/cc-cancellation-flow.png
Use tilt-app.html at ~/Desktop/tilt-app.html as the brand reference.
```

You'll have a complete engineer package in a few minutes. This is the same chain `prototype-agent` runs automatically — you're just driving it manually.

---

## Skill Reference

| Skill | Trigger phrase | Primary output | Use directly when... |
|-------|---------------|----------------|----------------------|
| `prototype-agent` | "Run the prototyping workflow for..." | Full engineer package across 4 phases | Starting a new feature from scratch; want context gathering + tracking included |
| `lofi-prototype` | "Build a lofi prototype for..." | Single interactive `.html` file | You need a clickable prototype fast, no full workflow needed |
| `hifi-prototype` | "Graduate the lo-fi at [path] to hi-fi" | HTML + SwiftUI + Compose + tokens + QA + handoff | You have a validated lofi and need engineer-ready output |
| `figma-sync` | "Capture to Figma", "Sync changes", "Wire prototype" | Figma frames, synced code | You need two-way sync between your prototype and a Figma file |

---

## MCP Server Setup

The optional MCP integrations expand what `prototype-agent` can do in Phase 1 (context gathering) and after Phase 4 (tracking and communication). Without them, the skills still work — you just handle those steps manually.

**Linear**
Enables creating sub-issues for the prototype, updating ticket status, and posting progress comments. Configure at [linear.app/settings/api](https://linear.app/settings/api). Once connected, `prototype-agent` will find relevant tickets automatically when you describe the feature.

**Notion**
Enables pulling PRDs, specs, and research documents as context before prototyping begins. Connect via the Notion integration settings at [notion.so/my-integrations](https://www.notion.so/my-integrations). Point `prototype-agent` at your workspace and it will search for relevant docs.

**Slack**
Enables posting prototype links directly to channels for async feedback, and reading thread responses back into the workflow. Set up at [api.slack.com/apps](https://api.slack.com/apps). With this connected, `prototype-agent` can post the lofi to your design-review channel and incorporate comments before graduating to hi-fi.

---

## Troubleshooting

**"The prototype is blank when I open it in a browser."**
Almost always a JavaScript syntax error in the generated file. Run `node --check your-prototype.html` to surface it, then ask Claude to fix the specific error.

**"Figma capture hangs or times out."**
Avoid capturing multiple screens in parallel. Open screens one at a time in your browser before running the capture command. If it still hangs, try reducing the number of screens in a single capture pass.

**"The skill isn't triggering — Claude doesn't seem to know about it."**
Check the file path. The skill file must be at exactly `~/.claude/skills/[skill-name]/SKILL.md`. Run `ls ~/.claude/skills/` to confirm the directory exists and the file is named `SKILL.md` (not `skill.md` or `README.md`).

**"prototype-agent isn't pulling my Linear ticket."**
The Linear MCP must be connected in Claude Code settings, not just installed. Verify the connection is active by asking Claude to `list_issues` — if it returns results, the MCP is live. If not, reconnect it in settings and restart Claude Code.

**"The hi-fi output looks almost identical to the lofi — no brand styling."**
The `hifi-prototype` skill needs a brand reference file to apply color, typography, and component styles. Make sure your prompt includes the full path to `tilt-app.html` (or your brand reference file). Without it, the skill has nothing to pull brand values from.

**"SwiftUI or Compose output references components that don't exist in our codebase."**
The skill generates idiomatic platform code but doesn't know your internal component library. After generation, search for any imports or component names that don't resolve and replace them with your actual equivalents. The `HANDOFF.md` file will note assumptions made during generation — start there.
