---
name: figma-sync
description: Two-way sync between a localhost web app and Figma. Handles project setup, capturing screens to Figma, syncing Figma edits back to code, wiring prototype connections, and capturing multiple screens. Invoke when user says "capture to figma", "sync changes", "wire prototype", "setup figma sync", or any variation.
---

# Figma Two-Way Sync Skill

This skill manages a live two-way sync between a local React/Vite app and a Figma file.

## Commands

The user will give natural language commands. Map them to the workflows below:

| What the user says | Workflow to run |
|---|---|
| "setup figma sync" | **SETUP** |
| "capture to figma" | **CAPTURE** |
| "sync changes" | **SYNC** |
| "wire prototype" | **PROTOTYPE** |
| "build screens in figma" | **BUILD** |

---

## SETUP — New project with Figma sync

Run this when the user wants to create a new project from scratch with two-way sync enabled.

### Step 0: Ensure Node.js is installed

Before anything else, check if Node.js is available:

```bash
node --version
```

If that fails or Node is not found, install it via **nvm** (works on macOS/Linux, no sudo or Homebrew required):

```bash
# Install nvm
curl -o /tmp/nvm-install.sh https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh
bash /tmp/nvm-install.sh

# Load nvm into current shell
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && source "$NVM_DIR/nvm.sh"

# Install latest LTS version of Node
nvm install --lts

# Verify
node --version && npm --version
```

After installing, always prefix npm/node commands with `source ~/.nvm/nvm.sh &&` to ensure nvm is loaded in the shell session.

**On Windows:** Ask the user to download and install Node.js from https://nodejs.org (LTS version). Instruct them to run `! node --version` in the Claude Code prompt to confirm, then continue.

### Step 1: Create the Vite + React app
   ```bash
   source ~/.nvm/nvm.sh && npm create vite@latest <app-name> -- --template react
   cd <app-name> && npm install
   ```

### Step 2: Check Figma MCP is connected

The Figma MCP server (`figma-remote-mcp`) must be connected for any Figma tools to work. Verify by calling `whoami` or `get_metadata`. If tools are unavailable, tell the user:

> "You need the Figma MCP server connected. In Claude Code settings → MCP Servers, add the Figma MCP server. See: https://www.figma.com/developers/mcp"

### Step 3: Inject Figma capture script

Add to `index.html` inside `<head>`:
   ```html
   <script src="https://mcp.figma.com/mcp/html-to-design/capture.js" async></script>
   ```

### Step 4: Start the dev server

Check if port 5173 is free first, then start:
   ```bash
   lsof -i :5173 || (source ~/.nvm/nvm.sh && npm run dev -- --port 5173)
   ```
   If 5173 is taken, use 5174, 5175, etc.

### Step 5: Get the Figma target

Ask the user for:
   - The Figma file URL (extract `fileKey` and optionally a target `nodeId` for the page to place frames on)
   - What to build (description or existing Figma design URL)

### Step 6: Build the initial UI

Fetch the design with `get_design_context`, build the React components to match, then run **CAPTURE**.

---

## CAPTURE — Push code → Figma

Run this when the user wants to push their current localhost app to Figma.

**Steps:**
1. Confirm the dev server is running. If not, start it.
2. Confirm `index.html` has the capture script injected.
3. Call `generate_figma_design` with `outputMode: "existingFile"`, `fileKey`, and optionally `nodeId`.
4. Open the browser with the capture hash URL:
   ```
   open "http://localhost:<PORT>/<ROUTE>#figmacapture=<ID>&figmaendpoint=<ENDPOINT>&figmadelay=2000"
   ```
5. Poll `generate_figma_design` with the `captureId` every 5 seconds until status is `completed`.
6. Report the resulting Figma node ID to the user.

**For multiple screens:** Capture one screen at a time sequentially (parallel captures stall). Wait for each to complete before opening the next.

**Key rule:** Never open multiple browser tabs simultaneously for capture — do them one at a time.

### The capture toolbar

The capture toolbar appears in the browser **only when the capture URL is open** — the one with `#figmacapture=<ID>&figmaendpoint=<ENDPOINT>` in the hash. It does not appear automatically just from the script being loaded.

When a capture is initiated (via `generate_figma_design`), Claude opens the capture URL in the browser. During that window the toolbar is visible and the user can:

- **Capture page** — sends the full current screen to Figma
- **Select element** — click any element on the page to send just that component to Figma
- **Navigate + recapture** — click links to go to another route, then hit capture again

To enable element selection mode programmatically, add `&figmaselector=*` to the capture URL.

---

## SYNC — Pull Figma edits → Code

Run this when the user says "sync changes" or wants to pull Figma edits back into code.

**Steps:**
1. Call `get_design_context` with the `fileKey` and `nodeId` of the captured frame.
2. Also call `get_screenshot` to visually confirm what changed.
3. Compare the returned design data against the current source files.
4. Apply only the changed values to the code — do not rewrite files wholesale.
5. Report exactly what changed (e.g. "Total Revenue changed from $284,392 to $310,000").

**What to look for in the diff:**
- Text content changes (labels, values, names)
- Color changes (background, text, accent)
- Layout changes (spacing, sizing)
- Added or removed elements

**Important:** If no differences are detected, tell the user and ask them to confirm they saved their Figma edits on the correct frame.

---

## PROTOTYPE — Wire Figma prototype connections

Run this when the user wants to connect screens with clickable prototype links in Figma.

**Steps:**
1. Confirm the node IDs of all screen frames in Figma (ask the user or retrieve from prior captures).
2. Use `use_figma` to add `reactions` to interactive elements.
3. Use the **`actions`** field (not `action`) in reactions — the Figma API requires this:
   ```js
   sourceNode.reactions = [...sourceNode.reactions, {
     actions: [{
       type: "NODE",
       destinationId: "TARGET_NODE_ID",
       navigation: "NAVIGATE",
       transition: {
         type: "SMART_ANIMATE",
         easing: { type: "EASE_IN_AND_OUT" },
         duration: 0.3,
       },
       preserveScrollPosition: false,
     }],
     trigger: { type: "ON_CLICK" },
   }];
   ```
4. Connect buttons by matching text content with `findOne(n => n.type === "TEXT" && n.characters === "Button Label")`.
5. Connect back buttons by finding frames containing a vector child.
6. Report all connections created.

**After wiring:** Tell the user to select the starting frame in Figma and click ▶ Play to preview the prototype.

---

## BUILD — Design screens in Figma using design system

Run this when the user wants Claude to design screens in Figma using their existing design system.

**Steps:**
1. Load the `figma-generate-design` skill for the screen-building workflow.
2. Inspect the Figma file for existing design system components, variables, and styles.
3. Build screens using `use_figma`, one section at a time.
4. Validate visually with `get_screenshot` after each section.
5. After screens are built, offer to run **PROTOTYPE** to wire them up.

---

## Project state tracking

When working on a project, keep track of:
- **fileKey** — the Figma file key (from URL)
- **Screen node IDs** — map of screen name → Figma node ID
- **localhost URL and port** — where the dev server is running
- **Routes** — URL path for each screen (e.g. `/`, `/cash-advance`)

Example state:
```
fileKey: GKeOFwGNE3biB41ZmDnUXl
port: 5174
screens:
  Home:         { nodeId: "186:2", route: "/" }
  CashAdvance:  { nodeId: "187:2", route: "/cash-advance" }
  CreditScore:  { nodeId: "188:2", route: "/credit-score" }
  AutoSave:     { nodeId: "189:2", route: "/autosave" }
```

---

## Mobile app prototypes — device shell

For mobile app projects, consider adding an iPhone or Android device shell to the project so the browser view simulates a real device. This gives a more realistic prototype experience without affecting the Figma capture workflow.

**How it works:**
- A CSS device frame wraps the app content in the browser
- The shell constrains the visible area to mobile dimensions (e.g. 852px height) and scrolls inside it
- Figma captures still get the full page height — the shell is invisible to the capture

**Key rules when adding a device shell:**
- Put `overflow-y: auto` and `height: 852px` on the screen container (`.device-screen`), not on `#root`
- Keep `#root` unconstrained — no `max-height`, no `overflow` — so captures get the full content height
- Use `figmaselector=%23root` in capture URLs to capture just the app content, not the shell

**To add a device shell:** tell Claude "add an iPhone device shell to the app" and it will set it up.

---

## Common issues & fixes

| Problem | Fix |
|---|---|
| Capture stays pending | Open tabs one at a time, not simultaneously |
| Frames land on wrong Figma page | Use `use_figma` to move them: `targetPage.appendChild(frame)` |
| `set_reactions: use actions not action` | Use `actions: [...]` array, not `action: {...}` |
| `layoutSizingHorizontal = 'FILL'` throws | Set AFTER `parent.appendChild(child)` |
| Font "SemiBold" not found | Inter uses "Semi Bold" (with space) |
| `figma.currentPage = page` throws | Use `await figma.setCurrentPageAsync(page)` |
| Sync detects no changes | User may have edited the wrong frame — confirm frame name/ID |
