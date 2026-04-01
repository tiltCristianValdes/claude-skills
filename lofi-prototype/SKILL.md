---
name: lofi-prototype
description: >
  Build grayscale lo-fi interactive prototypes as single-file HTML apps. Use this skill whenever someone
  asks to prototype a flow, screen, interaction, or user journey — whether from a Figma file, sketch,
  Notion doc, Linear task, PRD, descriptive prompt, or any other input. Also trigger when someone says
  "prototype", "lo-fi", "low fidelity", "mockup", "interactive mock", "clickable prototype", "flow
  prototype", "screen prototype", or describes wanting to see how a multi-screen experience would feel
  on a phone. Even if they don't say "lo-fi" explicitly, if the request is about prototyping a mobile
  app flow, use this skill. Also trigger when someone says "sync this prototype to figma", "push to
  figma", "capture to figma", or "set up figma sync" on an existing prototype — the skill includes an
  opt-in Figma two-way sync workflow for longer-lived prototypes. The output is always a single
  self-contained HTML file with vanilla JS that feels like navigating a real app.
---

# Lo-Fi Prototype Skill

Build grayscale lo-fi interactive prototypes that feel like real apps.

## What "Grayscale Lo-Fi" Means

This is the most important concept in this skill — get it right and everything flows from it.

**Grayscale lo-fi = a desaturated version of a high-fidelity design.** Same layouts, same typography, same spacing, same content hierarchy, same screen structure. You strip out three things: images, icons, and color. Everything else stays faithful to the source design.

This is NOT wireframing. Wireframes use placeholder boxes, simplified layouts, and visual shorthand. That's not what we're doing. We're taking what would be a polished, production-looking screen and running it through a grayscale filter — then replacing images and icons with simple gray shapes.

Think of it like a beautifully typeset magazine page printed in grayscale on a black-and-white printer. The layout is identical. The typography is identical. The spacing is identical. It just has no color photos and no color.

### The grayscale treatment, specifically:

- **Typography**: Identical to the source — same sizes, weights, line heights, letter spacing. Text drives the visual hierarchy.
- **Spacing**: Identical to the source — same padding, margins, gaps.
- **Layout**: Identical to the source — same flex directions, alignment, card structures, scroll areas.
- **Content**: Use real copy from the designs, not lorem ipsum. If you're working from a description rather than a design file, write realistic copy.
- **Illustrations / images**: Replace with plain gray circles or rounded rectangles at the same dimensions as the original. No X-marks, no labels like "image placeholder" — just a quiet gray shape.
- **Icons**: Replace with small gray circles (typically 24-40px). No SVG icons, no emoji, no unicode symbols as icons.
- **Brand-colored buttons** (e.g. chartreuse/green primary buttons): On dark screens → `shade-300` background with `shade-900` text. On light screens → `shade-900` background with white text.
- **Secondary buttons**: On dark screens → `shade-800` background with `shade-100` text. On light screens → `shade-300` background with `shade-900` text.
- **Dark screens stay dark. Light screens stay light.** Preserve the original theme per screen.
- **No confetti, no particles, no gradients, no shadows beyond subtle card elevation, no decorative elements.**

### What's OK in grayscale lo-fi:

- Unicode characters for functional meaning: ✕ (close), ← (back), ✓ (checkmark in checkbox), • (bullet), ● ▮ ⊞ (tab bar placeholders)
- Borders and dividers for structure
- Border-radius matching the source design
- Progress bars as simple filled rectangles
- The shade palette for all color values

## Tilt Design System Tokens

The prototype uses Tilt's design system. These are the core tokens:

### Typography
- **Typeface**: Suisse Intl (with system fallbacks: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif)
- **Weights**: Black/900, Bold/700, SemiBold/600, Regular/400
- **Scale** (from the Figma design system):

| Token | Size | Line Height | Weight | Letter Spacing |
|-------|------|-------------|--------|----------------|
| headline1 | 40px | 48px | 900 | -0.5px |
| headline2 | 32px | 38px | 900 | -0.25px |
| headline3 | 24px | 30px | 700 | -0.25px |
| subtitle1 | 16px | 22px | 600 | 0 |
| subtitle2 | 14px | 20px | 600 | 0 |
| body0 | 18px | 24px | 450 | 0 |
| body1 | 16px | 22px | 400 | 0 |
| body2 | 14px | 20px | 400 | 0 |
| body3 | 12px | 16px | 400 | 0 |
| button1 | 14px | 20px | 600 | 0 |
| button2 | 12px | 16px | 600 | 0 |
| number1 | 56px | 64px | 700 | 0 |
| number2 | 40px | 48px | 700 | 0 |
| number3 | 32px | 38px | 700 | 0 |

Numbers should use `font-feature-settings: 'lnum' 1, 'tnum' 1` for tabular figures.

### Shade Palette
All color in the prototype comes from this neutral palette:

| Token | Hex | Usage |
|-------|-----|-------|
| shade-900 | #100F0F | Darkest — dark backgrounds, primary text on light |
| shade-800 | #262525 | Cards on dark backgrounds, secondary buttons on dark |
| shade-700 | #3A3A3A | Icon circles on dark, tertiary surfaces |
| shade-500 | #595954 | Secondary text, muted elements |
| shade-400 | #8B8B87 | Tertiary text, subtle borders |
| shade-300 | #EBEAE0 | Primary buttons on dark, borders on light, icon circles on light |
| shade-200 | #F7F5EF | Card backgrounds on light screens |
| shade-100 | #FFFDF6 | Primary text on dark screens |

### Animation Tokens
- **Duration**: 280ms for screen transitions, 150ms for micro-interactions (active states, fades)
- **Easing**: `cubic-bezier(0, 0, 0.2, 1)` (deceleration curve, iOS-style)

## Architecture: Single-File HTML

Every prototype is a single `.html` file. No build step, no frameworks, no external dependencies. The file contains `<style>`, static HTML fallback, and a `<script>` block — all in one.

Read `references/architecture.md` for the complete technical specification including the DOM helper, navigation system, rendering pipeline, animation system, modal system, responsive behavior, and gesture handling.

## Input Sources & Fidelity Expectations

The accuracy expectation scales with the quality of the reference. A Figma file is a contract — build it faithfully. A sketch or text prompt is a starting point — use the design system and historical patterns to fill the gaps.

### Tier 1: Figma Nodes — Maximum fidelity

The user is handing you the actual design. Your job is to reproduce it accurately in grayscale, not to interpret or improve it. Every spacing, font, radius, and color decision has already been made — extract it and use it.

When given one or more Figma URLs:
1. **Always call both `get_screenshot` AND `get_design_context` for every node.** Screenshots show visual intent; design context gives exact measurements. You need both.
2. Extract every concrete value: font family, size, weight, line-height, letter-spacing, padding, gap, border-radius, width, height. Do not approximate — use the exact numbers.
3. Map Figma color tokens to the shade palette:
   - Chartreuse/brand colors → `shade-900` (on light) or `shade-300` (on dark)
   - Persimmon/red alert colors → `shade-300` (containers) or `shade-200`
   - Secondary colors (green, orange, purple category bubbles) → `shade-300`
   - Text colors map directly: `#100F0F` = shade-900, `#595954` = shade-500, etc.
4. Pay close attention to component sizes: checkbox 32×32, pill small 28px/medium 40px, CTA 48px/radius 50px, card radius 12px standard.
5. Fetch multiple Figma URLs in parallel for speed.

**The prototype should look nearly identical to the Figma reference — just stripped of color, images, and icons.**

### Tier 2: Screenshot or sketch

The reference has visual intent but no exact measurements. Use it to understand layout, flow order, and relative hierarchy — then fill in measurements using the Tilt Design System and, where possible, historical reference from previously built prototypes of similar screens.

1. Identify each screen and enumerate them before building anything
2. Infer flow order from arrows, numbering, or left-to-right layout
3. Match visible typography to the nearest Tilt DS token (large bold title → headline2: 32px/900/38px)
4. Match spacing to standard Tilt values (8, 12, 16, 20, 24, 32px)
5. **Look for historical patterns first.** If this screen type has been built before (e.g. a payment confirmation, a settings list, a home tile), use that as your baseline rather than reinventing. Consistency with established patterns is more valuable than novelty.
6. If a Figma URL is available for the same design, always pull node context too — it resolves ambiguity

### Tier 3: Text description (PRD, Notion, Linear, prompt, sketch notes)

The reference describes intent, not form. Your job is to make confident design decisions on behalf of the user using the Tilt DS and historical patterns — then build something that feels like a natural extension of the product, not a generic wireframe.

1. Parse the description into a screen list before building
2. Determine dark/light theme per screen — default to light unless the context or product area implies dark
3. Write realistic copy that sounds like the Tilt product — not lorem ipsum, not placeholder text
4. **Default to Tilt DS standards throughout:** 24px horizontal padding, 12px card radii, 48px CTAs, subtitle2 for section labels, body2 for descriptions, headline2/3 for page titles
5. **Mine historical reference for similar patterns.** A "confirm action" screen, a "success state", a "list with detail rows" — if you've seen how Tilt builds these, use that pattern
6. Ask clarifying questions only if truly ambiguous and it would change the structure significantly — don't hold up building over minor details

### Deviation and exploration

When the user wants to explore beyond the reference — try a different layout, test a new pattern, deviate from the established flow — they'll say so. Until then, stay close to what the reference and design system prescribe. The prototype is a tool for exploration, but a solid, accurate starting point is what enables productive exploration.

## Building the Prototype

### Step 1: Gather design context — MANDATORY EXTRACTION PASS

**Do not write a single line of code until this step is complete.**

#### 1a. Enumerate exactly what is shown

When a reference contains multiple screens (a Figma section, a screenshot with several frames, a flow overview), **list every screen you actually see before building anything.** State the screen name, its position in the flow, and what it contains. Do not invent screens that are not present. Do not add screens based on what "probably comes next." Only build what is visible in the reference, unless the user explicitly asks you to add something beyond it.

If you are uncertain whether something is a screen or a component state, call `get_screenshot` on the parent node to see the full layout, then confirm before building.

**Never hallucinate content.** If a label, number, or UI element is not legible or not present in the reference, use a reasonable Tilt-style placeholder and note that it was inferred — do not invent specific values or flows.

#### 1b. Extract exact values for every visible screen

For every Figma node provided:
1. Call `get_design_context` AND `get_screenshot` in parallel for every node. The screenshot reveals visual intent (dark vs light, density, composition). The design context gives exact measurements.
2. **Read the full `get_design_context` response top to bottom.** Do not skim. Every class like `px-[24px]`, `text-[14px]`, `rounded-[12px]`, `h-[48px]` is a precise measurement — extract it literally.
3. Build an extraction table before coding. For each screen, write out:
   - Background color (bg-white vs bg-[#f7f5ef] — these are different, do not default to white)
   - Every font size, weight, line-height, letter-spacing in use
   - Every padding, gap, border-radius value
   - Component heights (pills, buttons, cards, headers)
   - Icon/bubble sizes (24px? 40px? verify from the context — do not guess)
4. Note the "styles contained in the design" section at the bottom of each `get_design_context` response — it lists every token in use. Cross-reference these against your extraction table.
5. **Never approximate.** If the context says `p-[20px]`, use 20px — not 16px or 24px. If it says `text-[40px]`, use 40px — not 32px or 56px. The Figma file is the source of truth.

**Why this matters:** Skimming the design context is how small errors compound — a balance at 56px instead of 40px, modal icons at 40px instead of 24px, a header background at white instead of `#F7F5EF`. Each wrong value erodes fidelity. The whole point of Figma being available is that the prototype should look nearly identical, just in grayscale.

After extraction, also map out:
- How many screens, navigation order, dark vs light theme per screen (from the screenshot — not an assumption)
- Modals and their triggers
- Interactive elements
- **Declare all numeric values as constants at the top** (balances, limits, amounts). Verify arithmetic is consistent across screens.

### Step 2: Start from the reference architecture
Read `references/architecture.md` and use its patterns. The architecture covers: DOM helper, navigation, rendering, animation, modals, responsive behavior, gestures, and the critical `render()` vs `update()` vs direct DOM distinction.

### Step 3: Build screen renderers
Each screen gets a `renderScreenName()` function that returns a DOM element:
- Use the `h()` DOM helper for all element creation
- Each screen returns a `.sc` element with `.dk` or `.lt` class — set this from the screenshot, not assumption
- Structure: `.sh` (header) → `.ss` (scrollable body) → `.sf` (footer)
- Modals render inside the screen that triggers them
- **Use exact Figma values** for all typography (fontSize, fontWeight, lineHeight, letterSpacing) and spacing (padding, margin, gap, borderRadius, height)

### Step 4: Wire up navigation
- `nav(screenName)` pushes to history and navigates forward → calls `render()`
- `back()` pops history and navigates backward → calls `render()`
- `sm(modalName)` opens a modal → calls `update()` (NOT render)
- `hm()` closes the current modal → calls `update()` (NOT render)
- `R()` resets everything to the initial screen

### Step 5: Make it feel like a live app, not a slideshow
This is the most important step. The prototype must feel like a coded product, not a Figma click-through.

**Interaction tiers (most important):**
- **Direct DOM manipulation** for all in-screen state changes: radio/checkbox selection, disclosure expand/collapse, input typing, enabling/disabling buttons. Store element references in variables and update `.style` properties directly. NEVER call `render()` or `update()` for these — it causes visible flickering.
- **`update()`** for modal open/close. Re-renders the screen without animation, preserves scroll position. The modal's own CSS transition handles its entrance.
- **`render()`** only for actual screen navigation (nav/back). This is the only time the screen should visibly "change."

**Interaction details:**
- Inputs should work: text inputs accept focus and typing, selectors highlight on tap, checkboxes toggle with CSS transitions.
- Buttons should gate on state: if a button requires a selection, show visual difference (opacity, pointer-events).
- Transitions should mimic the platform: forward slides from right, back reveals from left, modals slide up from bottom.
- Touch feedback: every tappable element gets `.t` class (`:active` opacity 0.7).
- Swipe-back gesture: edge swipe (30px zone, 80px threshold) triggers `back()`.
- CSS transitions for micro-interactions: use `transition: all 150ms var(--ease)` on checkboxes, disclosure arrows, toggle states. This makes state changes feel smooth even without re-rendering.

### Step 6: Verify arithmetic
Before delivering, verify every number:
- `balance + available = limit`
- Payment options are ordered: past due < minimum < statement < total
- Amounts shown on confirmation screens match the selected option exactly
- Usage bar percentages are calculated from actual values, not hardcoded

## Figma Sync (Opt-In Graduation)

By default, every prototype is a standalone HTML file with zero dependencies. But when a designer knows they'll be iterating on a prototype — getting feedback, tweaking copy or spacing in Figma, going through design review — they can "graduate" the prototype to two-way Figma sync.

### When to trigger this

Listen for phrases like:
- "set up figma sync for this prototype"
- "sync this to figma"
- "push this to figma"
- "I want to iterate on this in figma"
- "capture this to figma"
- "connect this prototype to figma"

When detected, follow the workflow below. Do NOT set up Figma sync by default — only when the user explicitly asks.

### How it works

The `figma-sync` skill handles all the heavy lifting. This section just covers the bridge from a lo-fi prototype to that skill.

#### Step 1: Serve the prototype locally

The prototype is a static HTML file, so a simple HTTP server is enough:

```bash
cd /path/to/prototype/directory
python3 -m http.server 5173
```

If Python isn't available, use Node:
```bash
npx serve -l 5173 .
```

#### Step 2: Inject the Figma capture script

Add to the prototype's `<head>` tag:
```html
<script src="https://mcp.figma.com/mcp/html-to-design/capture.js" async></script>
```

This enables the Figma capture toolbar in the browser and the `generate_figma_design` workflow.

#### Step 3: Add screen routing for programmatic capture

The prototype uses in-memory JS navigation — each screen is the same URL rendered differently. To capture screens individually without manual clicking, add a `?screen=` URL param hook just before the initial `render()` call:

```js
// URL screen param for direct capture: ?screen=closeConfirm etc.
(function(){var p=new URLSearchParams(window.location.search).get('screen');if(p)S=p;}());
```

This lets each screen be opened directly as `?screen=screenName` for capture.

#### Step 4: Capture screen content only — not the device frame

**Critical rule**: Always use `figmaselector=%23mc` in the capture URL to target `#mc` (the inner screen container) rather than the full page. This captures only the 393×852 app content — no dark background, no device shell border.

Capture URL format:
```
http://localhost:5173/prototype.html?screen=SCREEN_NAME#figmacapture=ID&figmaendpoint=ENDPOINT&figmadelay=1000&figmaselector=%23mc
```

Never capture the full page — it brings in the dark dev toolbar, viewport background, and device frame chrome, which are prototype scaffolding, not design content.

#### Step 5: Hand off to figma-sync

From here, invoke the `figma-sync` skill. The relevant workflows:

| Designer says | figma-sync workflow |
|---|---|
| "push this to figma" / "capture" | **CAPTURE** — sends the current screen to Figma as a frame |
| "sync changes" / "pull from figma" | **SYNC** — diffs Figma edits against the prototype and patches the HTML |
| "wire up the prototype" | **PROTOTYPE** — connects Figma frames with click-through interactions |

#### Step 4: Embed source metadata

After the first capture, store the Figma file key and screen-to-node mapping as an HTML comment at the top of the prototype file:

```html
<!-- figma-sync: {"fileKey":"abc123","screens":{"home":"186:2","payment":"187:2","confirm":"188:2"}} -->
```

This lets future sessions know which Figma nodes to pull from when the designer says "sync changes" without re-specifying URLs.

### What the designer gets

- **View/share the prototype**: Still just an HTML file. Double-click to open. Share over Slack. No server needed for viewing.
- **Iterate in Figma**: Edit text, spacing, colors in Figma → say "sync changes" → prototype updates automatically.
- **Capture updates**: Change something in the prototype → say "capture to figma" → Figma frame updates.
- **Wire prototype**: Connect screens in Figma with click-through navigation for stakeholder walkthroughs.

### Important: the HTML file stays standalone

Even after Figma sync is set up, the prototype remains a single self-contained HTML file. The capture script tag is the only addition, and it loads async with no impact on offline viewing. If someone opens the file without a server, it works exactly the same — the capture toolbar just won't appear.

---

## Checklist Before Delivering

### Syntax Validation (run this first — a syntax error produces a silent blank screen)

After writing the HTML file, extract and validate the JavaScript before opening it:

```bash
python3 -c "
import re, sys
content = open('prototype.html').read()
match = re.search(r'<script>(.*?)</script>', content, re.DOTALL)
open('/tmp/_proto_check.js', 'w').write(match.group(1))
"
node --check /tmp/_proto_check.js && echo "✓ JS valid" || echo "✗ Syntax error — fix before delivering"
```

Replace `prototype.html` with the actual filename. If `node --check` fails, read the error line number, fix the issue, and re-run before proceeding.

- [ ] `node --check` passes with no errors

### Layout & Rendering
- [ ] Every screen renders identically in the phone frame (desktop) and fullscreen (mobile)
- [ ] Status bar with inline SVG icons (cellular, wifi, battery) renders at the top of every screen
- [ ] Home indicator renders at the bottom of every screen
- [ ] The file works when opened directly in a browser (no server needed)

### Fidelity
- [ ] Typography uses exact Figma values (not approximations) when design context is available
- [ ] Spacing (padding, margins, gaps) matches the source design
- [ ] Component sizing matches (button heights, checkbox sizes, pill radii, card radii)
- [ ] No images, no icons (only gray shape placeholders at correct dimensions)
- [ ] No brand colors — everything uses the shade palette
- [ ] Dark screens use shade-900 background, light screens use white

### Interaction Quality
- [ ] Navigation flow is complete — every button leads somewhere or does something
- [ ] Screen transitions animate (push/pop/fade) and feel native
- [ ] In-screen interactions (radio, checkbox, disclosure) update WITHOUT full re-render — no flickering
- [ ] Modals use `update()` + CSS transition, not `render()`
- [ ] Scroll position is preserved across in-screen state changes
- [ ] Interactive states use CSS transitions (150ms) for smooth visual feedback

### Data Integrity
- [ ] All financial values are declared as constants at the top of the script
- [ ] All derived values (percentages, totals) are computed from constants, not hardcoded
- [ ] Arithmetic is verified: sums match, ordering is correct, confirmation amounts are dynamic
