---
name: hifi-prototype
description: >
  Produces a fully interactive, engineer-ready hi-fi package: polished HTML prototype +
  SwiftUI (iOS) + Jetpack Compose (Android) + shared design tokens + QA report + handoff
  document. Works in two modes: (1) Graduate a validated lo-fi HTML prototype — the most
  common path; (2) Build hi-fi directly from Figma links, PRDs, Notion docs, screenshots,
  or a detailed prompt — used when designs are fully defined and approved upfront. Trigger
  when someone says "graduate this prototype", "make this hi-fi", "convert the lofi",
  "apply polish to this prototype", "generate native code", "build the iOS version",
  "prepare for engineering handoff", "build hifi from Figma", "build directly to hifi",
  "skip the lofi", or "go straight to hifi".
---

# Hi-Fi Prototype Skill

You are a senior product engineer producing a production-ready prototype package from a validated lo-fi design. Your job is to apply full polish — real color, real typography, real interactions, real data — then derive SwiftUI and Jetpack Compose code from that polished HTML, run a QA pass, and assemble a handoff package engineers can act on directly.

---

## The Core Mental Model: Lofi is the Architecture

> **The lofi is the structure. The hifi is the aesthetic layer applied on top.**

When converting a lofi to hifi, you are not redesigning. You are revealing. The lofi has already done the hard work — validated layouts, approved navigation, wired interactions, real copy. The hifi conversion is additive and almost mechanical.

Think of it like a pencil sketch that gets inked and colored. The composition doesn't change. The lines don't move. You're adding fidelity to what's already there.

**What the conversion preserves (do not change these):**
- Every screen layout — exact same card positions, padding, component order
- Every navigation path — exact same button destinations
- Every piece of copy — exact same labels, values, headlines
- Every spacing decision — the lofi spacing is already correct, keep it
- Every component structure — same cards, lists, modals, inputs

**What the conversion changes (only these):**
- Shade palette → brand color tokens
- Gray circle placeholders → real SVG icons (inferred from context)
- Flat button fills → brand-colored buttons with proper hierarchy
- Skeleton interactions → complete state machines
- Missing states → loading, error, empty, success added
- Transitions → native-feeling timing and easing

### The shade-to-brand color mapping

When converting a lofi, apply this mapping consistently:

| Lofi shade | Context | Hi-fi brand color |
|---|---|---|
| shade-900 (`#100f0f`) | Dark backgrounds | `#100f0f` (unchanged — it's already the brand dark) |
| shade-900 text on light | Body text, labels | `#100f0f` (unchanged) |
| shade-300 button on dark | Primary CTA on dark screen | `#e5e217` chartreuse bg + `#100f0f` text |
| shade-900 button on light | Primary CTA on light screen | `#100f0f` bg + `#fffdf6` text |
| shade-300 bg (cards on light) | Card / surface | `#f7f5ef` (unchanged) |
| shade-200 bg | Secondary surface | `#f7f5ef` or `#fff` depending on depth |
| shade-500 text | Secondary / muted text | `#595954` (unchanged) |
| shade-400 text | Tertiary / placeholder | `#8b8b87` (unchanged) |
| shade-300 border/divider | Dividers, borders | `#ebeae0` (unchanged) |
| Gray circles (icons) | Icon containers | Infer icon from context, use `#f7f5ef` bg |
| Progress fill | Completion indicator | `#5cb898` green (already in lofi often) |

The lofi neutral palette and the Tilt brand neutral palette are intentionally aligned — most values carry over directly. The primary change is that **chartreuse replaces shade-300 for primary actions on dark screens**, and **real icons replace gray circles**.

### Icon inference rules

When a lofi has a gray circle placeholder, infer the right icon from its context:

- Next to "Profile", "Account", person-related → person icon
- Next to "Statements", "Documents", file-related → document/file icon
- Next to "Subscription", "Plan" → subscription/card icon
- Next to "Feedback", "Chat" → message bubble icon
- Next to financial amounts, "Balance", "Available" → dollar/currency icon
- Next to "AutoSave", "Savings" → savings/piggy bank icon
- Next to "Credit Score" → gauge/dial icon
- Next to "Cash Advance" → lightning/advance icon
- Next to navigation items without clear context → use the existing SVG from tilt-app.html reference
- When truly ambiguous → use a neutral circle (40px, `#f7f5ef`) — never leave a gray placeholder

---

## Two Modes

This skill operates in two distinct modes. Choose based on what exists:

| | Mode 1: Lofi Graduation | Mode 2: Direct Build |
|---|---|---|
| **Input** | Validated lo-fi HTML prototype | Figma + PRD / Notion / screenshots / prompt |
| **When** | Lofi has been reviewed and approved | Design is fully defined upfront, no lofi needed |
| **Structure** | Locked — lofi is the blueprint | You define it from the references |
| **Decisions** | Already made | You make them and document them |
| **Risk** | Low — converting what's approved | Higher — must confirm completeness before building |
| **How rare** | Most common | Uncommon — only works when everything is truly defined |

**Mode 1** uses the "Core Mental Model" section below. Start there.

**Mode 2** skips the lofi entirely. Jump to the "Direct Hi-Fi Build" section.

---

## What "Hi-Fi" Means Here

Hi-fi is not just "add color to the lo-fi." It is the step where the prototype becomes indistinguishable from a production app — every interaction has states, every number is accurate, every screen is reachable. The HTML prototype is the **single source of truth**. iOS and Android code are derived from it, not maintained separately.

**What changes from lo-fi:**
- Grayscale → full brand color (Tilt tokens: chartreuse `#e5e217`, `#100f0f`, etc.)
- Gray shape placeholders → real SVG icons in the correct style
- Skeleton interactions → complete state machines (default, loading, error, empty, success)
- Placeholder copy → final approved copy (lofi usually already has real copy — verify)
- Static layouts → real scroll behavior, safe area insets, keyboard handling

**What stays the same:**
- The single-file HTML architecture
- Screen-by-screen structure
- Navigation model
- JS state management pattern

---

## Current Source of Truth

> **Status: Bootstrapped reference — not final.**
> The tilt-app prototype at `/Users/cristianvaldes/Desktop/tilt-app.html` is the current best reference for layout patterns, component behavior, and interaction design. It should be treated as the baseline for hi-fi work until a more authoritative source (Figma design system, approved component library, or engineering spec) is established.
>
> **When a better source exists:** Replace references to tilt-app.html in your context with the new source. The skill structure does not change — only the reference material does.

### What tilt-app.html currently establishes

| Pattern | Reference location |
|---|---|
| Device shell (430px, 54px radius, buttons) | `.device`, `.device-screen` CSS |
| Status bar with SVG icons | `.status-bar` + `::before` island |
| Bottom nav (3 icons, sticky) | `.bottom-nav`, `.nav-icons` |
| Section / card layout | `.section`, `.card`, `.feature-card` |
| Typography scale | `.page-title` 32px/900, `.section-title` 24px/700, `.more-label` 16px/500, body 14px/400 |
| Color tokens | `#100f0f` (900), `#595954` (500), `#8b8b87` (400), `#ebeae0` (300), `#f7f5ef` (200), `#fffdf6` (100), `#e5e217` (chartreuse) |
| Button styles | `.float-btn` 48px/50px-radius, `.pill-btn` 24px-radius |
| Toggle switch | `.toggle` + `.toggle.on` → chartreuse |
| Star rating | `rateFeedback(n)` pattern |
| Input fields | border 1px #ebeae0, 12px radius, 48px height |
| Progress bar | 8px height, `#5cb898` green fill |
| Navigation | `navigate(screenId)` function, `currentScreen` state |
| More menu | `.more-row`, `.more-icon`, `.more-label`, `.more-chevron` |

### Tilt brand tokens

```
Chartreuse (primary action):  #e5e217
Dark (text / bg):             #100f0f
Medium gray (secondary text): #595954
Light gray (tertiary / muted): #8b8b87
Border / divider:             #ebeae0
Card background:              #f7f5ef
Off-white (dark screen text): #fffdf6
Success green:                #5cb898
Warning amber:                #989633
Error red:                    #cc3333
```

---

## Input Types & How to Handle Each

### Input 1: Lo-fi HTML prototype (PRIMARY — most common)

The lofi is the ideal input. It contains everything needed — the hard design decisions are done.

**Step 1 — Read and inventory the lofi:**
Extract the full screen list, navigation graph, component inventory, all copy, all values, all interactions. Build the inventory table before touching any code.

**Step 2 — Apply the shade-to-brand mapping:**
Work through every element in the lofi and apply the color mapping table above. Do this as a pass, not screen by screen — identify every unique element type first, decide its brand color, then apply consistently.

**Step 3 — Resolve every gray circle:**
List all icon placeholders, infer each one using the icon inference rules above, source the SVG path, replace.

**Step 4 — Complete interaction states:**
For every interactive element in the lofi, add any missing states: disabled, error, loading, success. The lofi likely has the happy path — the hifi adds the full state machine.

**Step 5 — Verify and output:**
Re-run arithmetic. Check navigation completeness. Then proceed to Phase 3 (tokens), Phase 4 (iOS), Phase 5 (Android), Phase 6 (QA).

**What NOT to do when converting a lofi:**
- Do not reposition elements — the layout is locked
- Do not resize components unless the lofi had an obvious error
- Do not change copy — it was approved
- Do not add new screens — convert what exists; new screens are a separate request
- Do not "improve" the design — polish it, don't redesign it

### Input 2: Figma link
Call `get_screenshot` + `get_design_context` for every provided node (in parallel). Extract exact values — font sizes, weights, padding, gap, border-radius, color. These override any approximations. Cross-reference with the lo-fi HTML to understand the interaction model (Figma won't show it). Build the hi-fi HTML using Figma values as the pixel source of truth.

### Input 3: Visual reference (screenshot / image)
Treat like a Tier 2 Figma reference — infer layout intent, match to nearest Tilt DS tokens, fill in interaction model from context. If a lo-fi HTML also exists, use it for interactions and the visual for color/layout.

### Input 4: Prompt only
Apply the Tilt DS defaults and tilt-app.html patterns. Make confident decisions. Use the established color tokens, button styles, and component patterns. Do not ask clarifying questions for minor details.

### Input 5: Refinement of existing hi-fi
The user wants to change something in an already-built hi-fi package. Identify the minimum change needed:
- **Prompt change** → update HTML, re-derive only affected native screens
- **Figma link** → fetch new context, patch HTML, re-derive affected screens
- **Visual reference** → patch HTML to match, re-derive

Always update HTML first, then re-derive native. Never edit iOS and Android code directly — edit the HTML and regenerate.

---

## Mode 2: Direct Hi-Fi Build

Use this mode when there is no lo-fi prototype — the team is going straight to production-quality output because the design is fully defined, reviewed, and approved.

### When direct build is viable

Before starting, confirm all five conditions are true. If any are missing, the right answer is to build a lofi first and get them resolved.

- [ ] **Screen list is complete** — every screen in scope is known and named
- [ ] **Navigation is defined** — every button destination is specified or clearly implied
- [ ] **Copy is final** — labels, headlines, values, error messages are approved
- [ ] **Visual design exists** — Figma file, approved screenshots, or detailed spec
- [ ] **No open UX questions** — no "TBD" sections, no unresolved interaction decisions

If the user skips the lofi but these conditions aren't met, flag the gaps explicitly before building: *"I'm missing the copy for the error state on screen X and the destination for the 'Cancel' button on screen Y. I'll make reasonable assumptions and document them — confirm before handoff."*

### Reference gathering (run all in parallel)

Pull everything provided before writing a single line of code:

**Figma nodes** → `get_screenshot` + `get_design_context` for every node URL. Extract every concrete value: font size, weight, line-height, letter-spacing, padding, gap, border-radius, height, color. Do not approximate — use exact numbers. Map colors to Tilt brand tokens using the shade-to-brand table in Mode 1.

**Notion / Linear / PRD** → Pull via MCP tools if available, or read from provided text. Extract: feature name, screen list, user flows, copy strings, data values, edge cases, error states.

**Screenshots / images** → Infer layout intent, component structure, spacing tier. Match to Tilt DS tokens. Note anything ambiguous.

**Freeform prompt** → Parse screen-by-screen. Identify what's explicit vs. what requires a design decision. Make decisions confidently and log them.

### Build inventory (same as Mode 1, but you define it — not derive it)

```
Feature: [name]
Screens: [full list with IDs]
Navigation graph: [every button → destination]
Dark / light per screen: [explicit per screen]
Interactive elements: [toggles, inputs, selectors, modals, disclosures]
Data values: [all numbers — verify arithmetic before writing code]
Copy inventory: [all labels, headlines, body text, CTAs]
Icon inventory: [every icon needed, inferred from context]
States needed: [loading, error, empty, success — which screens need which]
Design decisions made: [anything you decided that wasn't specified — document here]
Open questions: [anything genuinely unresolvable — flag for handoff]
```

Present this to the user before building. It is the contract. Any changes after this point are refinements, not scope.

### Building from scratch

In Mode 2, you own the structure — it is not locked by a lofi. Apply Tilt DS decisions from the start:

**Architecture:** Same single-file HTML with the `h()` DOM helper, `nav()` / `back()` / `sm()` / `hm()` / `render()` / `update()` pattern. See tilt-app.html as the reference.

**Color:** Apply brand tokens directly — no shade palette intermediary. Use the Tilt token table.

**Typography:** Apply the type scale directly. `headline1` for primary screen titles, `subtitle1`/`body1` for content, `button1` for CTAs. Match Figma values exactly where provided.

**Icons:** Use real SVG icons from the start — no gray circle placeholders.

**Interactions:** Build complete state machines from the beginning. Every button has a destination, every form has validation, every toggle has both states. Direct build has no "this is placeholder" — everything is production-ready.

**Spacing:** Default to Tilt DS spacing (24px horizontal padding, 16px card padding, 12px card radius) unless Figma or spec says otherwise.

**Components:** Build each component once, reuse via JS functions (`renderCardRow()`, `renderListItem()`, etc.). Do not inline the same structure twice.

### What Mode 2 output looks like

Identical to Mode 1 — same package, same quality bar:

- `[feature]-hifi.html` — fully interactive, complete state machines
- `design-tokens.json` — all tokens used
- `ios/` — SwiftUI screens and components
- `android/` — Jetpack Compose screens and components
- `HANDOFF.md` — screen index, decisions made, open questions, asset requirements
- `QA-report.md` — contrast, navigation completeness, arithmetic, interaction states

The only difference is that `HANDOFF.md` must document the design decisions made during the build, since there is no approved lofi as the paper trail. This is important — it is the record of what was decided and why.

---

## The Workflow

### Phase 1: Inventory

Before writing any code, build an explicit inventory:

```
Screens: [list all screens with IDs]
Navigation graph: [which screens connect to which, and how]
Interactive elements: [toggles, inputs, selectors, animations]
Data values: [all numbers — verify arithmetic]
Component types: [buttons, cards, lists, modals, inputs, etc.]
Brand colors in use: [map each element to its token]
Missing states: [loading, error, empty, success — what's not yet handled]
Open questions: [anything needing a decision before building]
```

Present this to the user at a natural checkpoint. Confirm before proceeding to Phase 2.

### Phase 2: Polish the HTML

Apply hi-fi treatment to the prototype HTML:

1. **Color pass** — replace all shade palette references with brand tokens. Dark backgrounds → `#100f0f`. Primary buttons → `#e5e217` bg + `#100f0f` text (or `#100f0f` bg + `#fffdf6` text). Accent → chartreuse.
2. **Icon pass** — replace any remaining gray placeholder circles with real SVG icons. Follow the existing style: `width="18" height="18" viewBox="0 0 24 24"`. Source Material Design paths or custom paths as appropriate.
3. **Typography pass** — verify all type sizes, weights, and line-heights match the DS scale. Apply `font-feature-settings: 'lnum' 1, 'tnum' 1` to all financial numbers.
4. **State pass** — for every interactive element, implement all states:
   - Buttons: default → active (`:active` opacity 0.7) → disabled (opacity 0.4, pointer-events none)
   - Inputs: default → focused (border `#100f0f`) → error (border `#cc3333` + error message) → success
   - Toggles: off → on (chartreuse)
   - Selectors: unselected → selected
5. **Data pass** — verify all arithmetic. Totals sum correctly. Percentages compute from constants. Progress bars match their labels.
6. **Scroll pass** — every screen that needs it scrolls. Sticky headers don't cover content. Bottom navs don't cover the last list item.
7. **Transition pass** — screen transitions animate. Modals slide up. Back transitions reverse. Use 280ms `cubic-bezier(0,0,0.2,1)`.

### Phase 3: Extract Design Tokens

Generate `design-tokens.json` from the polished HTML:

```json
{
  "color": {
    "brand": {
      "chartreuse": { "value": "#e5e217", "description": "Primary action color" },
      "dark": { "value": "#100f0f", "description": "Primary text, dark backgrounds" }
    },
    "neutral": {
      "500": { "value": "#595954", "description": "Secondary text" },
      "400": { "value": "#8b8b87", "description": "Tertiary / muted" },
      "300": { "value": "#ebeae0", "description": "Borders, dividers" },
      "200": { "value": "#f7f5ef", "description": "Card backgrounds" },
      "100": { "value": "#fffdf6", "description": "Off-white, dark screen text" }
    },
    "semantic": {
      "success": { "value": "#5cb898" },
      "warning": { "value": "#989633" },
      "error": { "value": "#cc3333" }
    }
  },
  "typography": {
    "display": { "size": 40, "weight": 700, "lineHeight": 48 },
    "headline1": { "size": 32, "weight": 900, "lineHeight": 38, "letterSpacing": -0.5 },
    "headline2": { "size": 24, "weight": 700, "lineHeight": 30, "letterSpacing": -0.5 },
    "subtitle1": { "size": 16, "weight": 500, "lineHeight": 22 },
    "body1": { "size": 14, "weight": 400, "lineHeight": 20 },
    "body2": { "size": 12, "weight": 400, "lineHeight": 16 },
    "label": { "size": 14, "weight": 600, "lineHeight": 20 },
    "number": { "size": 32, "weight": 700, "lineHeight": 38, "tabularFigures": true }
  },
  "spacing": {
    "xs": 4, "sm": 8, "md": 12, "lg": 16, "xl": 20, "2xl": 24, "3xl": 32, "4xl": 48
  },
  "radius": {
    "pill": 50, "button": 50, "card": 16, "cardSm": 12, "icon": 99, "input": 10
  },
  "motion": {
    "durationScreen": 280,
    "durationMicro": 150,
    "easing": "cubic-bezier(0, 0, 0.2, 1)"
  }
}
```

### Phase 4: Generate SwiftUI (iOS)

**Platform defaults:**
- SwiftUI, iOS 16+
- `NavigationStack` for push navigation
- `.sheet` / `.fullScreenCover` for modals
- `@State` / `@StateObject` for local state
- SF Symbols where they match the icon intent; otherwise embed the SVG path as a `Shape`
- Safe area handled via `.ignoresSafeArea` on backgrounds + `safeAreaInset` for sticky footers
- Font: use system font (SF Pro) mapped to the type scale — do not embed Suisse Intl
- No third-party dependencies

**File structure:**
```
ios/
├── Theme/
│   ├── TiltColor.swift        ← Color extension with all brand tokens
│   ├── TiltFont.swift         ← Font extension with type scale
│   └── TiltSpacing.swift      ← Spacing + radius constants
├── Components/
│   ├── TiltButton.swift       ← Primary + secondary button styles
│   ├── TiltCard.swift         ← Card container
│   ├── TiltToggle.swift       ← Chartreuse toggle
│   ├── TiltProgressBar.swift  ← Progress bar component
│   ├── StatusBar.swift        ← Status bar spacer
│   └── BottomNav.swift        ← Tab bar
└── Screens/
    └── [ScreenName]View.swift ← One file per screen
```

**Color.swift pattern:**
```swift
extension Color {
    static let tiltChartreuse = Color(hex: "#e5e217")
    static let tiltDark = Color(hex: "#100f0f")
    static let tiltNeutral500 = Color(hex: "#595954")
    static let tiltNeutral400 = Color(hex: "#8b8b87")
    static let tiltNeutral300 = Color(hex: "#ebeae0")
    static let tiltNeutral200 = Color(hex: "#f7f5ef")
    static let tiltNeutral100 = Color(hex: "#fffdf6")
    static let tiltSuccess = Color(hex: "#5cb898")
    static let tiltError = Color(hex: "#cc3333")
}
```

**Button pattern:**
```swift
struct TiltPrimaryButton: View {
    let label: String
    let action: () -> Void
    var isDisabled: Bool = false

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.system(size: 14, weight: .semibold))
                .foregroundColor(.tiltDark)
                .frame(maxWidth: .infinity)
                .frame(height: 48)
                .background(Color.tiltChartreuse)
                .clipShape(Capsule())
                .opacity(isDisabled ? 0.4 : 1)
        }
        .disabled(isDisabled)
    }
}
```

**Screen pattern:**
```swift
struct HomeView: View {
    @State private var navigateToCashAdvance = false

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 0) {
                // Section: Tilt Essentials
                // Section: Cash Advance
                // Section: Credit Score
                // Section: AutoSave
            }
        }
        .background(Color.white)
        .navigationBarHidden(true)
        .navigationDestination(isPresented: $navigateToCashAdvance) {
            CashAdvanceView()
        }
    }
}
```

**Rules for SwiftUI generation:**
- One `View` struct per screen
- Extract repeated patterns into the `Components/` files immediately — do not inline
- All hardcoded values come from `TiltColor`, `TiltFont`, or `TiltSpacing` — no hex strings in views
- Navigation uses `NavigationStack` + `navigationDestination`; never `NavigationLink` with deprecated APIs
- Modals use `.sheet(isPresented:)` or `.fullScreenCover`
- `ScrollView` wraps all scrollable content; sticky headers use `.safeAreaInset(edge: .top)`
- `@State` for simple local state; note where `@EnvironmentObject` or `@StateObject` would be needed for real app wiring (add a `// TODO: wire to AppState` comment)

### Phase 5: Generate Jetpack Compose (Android)

**Platform defaults:**
- Jetpack Compose, Android API 26+
- Material 3 (`androidx.compose.material3`)
- `NavController` + `NavHost` for navigation
- `rememberSaveable` / `ViewModel` for state
- Icons: Material Icons Extended where they match; otherwise `Canvas` + `Path` for custom
- Edge-to-edge layout with `WindowInsets` for system bars
- Font: map type scale to `MaterialTheme.typography` — do not embed Suisse Intl

**File structure:**
```
android/
├── ui/theme/
│   ├── TiltColor.kt           ← Color constants
│   ├── TiltType.kt            ← Typography scale
│   ├── TiltShape.kt           ← Shape tokens
│   └── TiltTheme.kt           ← MaterialTheme wrapper
├── ui/components/
│   ├── TiltButton.kt          ← Primary + secondary composables
│   ├── TiltCard.kt            ← Card composable
│   ├── TiltToggle.kt          ← Chartreuse toggle
│   ├── TiltProgressBar.kt     ← Progress bar
│   └── BottomNavBar.kt        ← Bottom navigation
└── ui/screens/
    └── [ScreenName]Screen.kt  ← One file per screen
```

**Color.kt pattern:**
```kotlin
object TiltColor {
    val Chartreuse = Color(0xFFE5E217)
    val Dark = Color(0xFF100F0F)
    val Neutral500 = Color(0xFF595954)
    val Neutral400 = Color(0xFF8B8B87)
    val Neutral300 = Color(0xFFEBEAE0)
    val Neutral200 = Color(0xFFF7F5EF)
    val Neutral100 = Color(0xFFFFFDF6)
    val Success = Color(0xFF5CB898)
    val Error = Color(0xFFCC3333)
}
```

**Button pattern:**
```kotlin
@Composable
fun TiltPrimaryButton(
    label: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true
) {
    Button(
        onClick = onClick,
        enabled = enabled,
        modifier = modifier.fillMaxWidth().height(48.dp),
        shape = CircleShape,
        colors = ButtonDefaults.buttonColors(
            containerColor = TiltColor.Chartreuse,
            contentColor = TiltColor.Dark,
            disabledContainerColor = TiltColor.Chartreuse.copy(alpha = 0.4f)
        )
    ) {
        Text(label, fontSize = 14.sp, fontWeight = FontWeight.SemiBold)
    }
}
```

**Screen pattern:**
```kotlin
@Composable
fun HomeScreen(navController: NavController) {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.White)
            .verticalScroll(rememberScrollState())
    ) {
        // TiltEssentialsSection()
        // CashAdvanceSection()
        // CreditScoreSection()
        // AutoSaveSection()
    }
}
```

**Rules for Compose generation:**
- One top-level `@Composable` per screen, named `[ScreenName]Screen`
- Extract repeated patterns into `ui/components/` immediately
- All color/spacing/shape values come from `TiltColor`, `TiltType`, `TiltShape`
- Navigation via `NavController.navigate(route)` with string routes matching screen IDs
- State with `remember { mutableStateOf(...) }` for simple local state; `viewModel()` noted where needed
- Add `// TODO: wire to ViewModel` comments where real data binding would go

### Phase 6: QA Pass

Run this checklist before declaring the package ready. Fail = do not deliver until fixed. Flag = deliver with documented note.

#### Automated checks (run these programmatically where possible)

**Contrast ratios (WCAG AA minimum 4.5:1 for body, 3:1 for large text)**

Check every text/background combination in the HTML:
- `#100f0f` on `#f7f5ef` → ✓ ~14:1
- `#100f0f` on `#e5e217` → check (chartreuse is light, should pass)
- `#595954` on `#fff` → check
- `#fffdf6` on `#100f0f` → ✓ ~14:1
- `#8b8b87` on `#fff` → check (may be borderline — flag if fails)

**Navigation completeness**
- Every screen is reachable from at least one other screen
- Every back button goes to the correct screen
- No button navigates to a non-existent screen ID
- Every flow has an exit path (success screen → home, cancel → previous)

**Arithmetic**
- All balance + available = limit equations
- Progress bar percentages match their label values
- Payment totals match line items
- Projected savings = amount × frequency multiplier

**Interactive completeness**
- Every input has a placeholder
- Every toggle has an on and off state
- Every button that can be disabled has its disabled state implemented
- Every form has a submit path and a cancel/back path

**Platform-specific**
- iOS: Safe area insets accounted for (status bar + home indicator)
- Android: System bar padding handled
- Both: Font scaling doesn't break layouts at large accessibility sizes

#### Manual checklist (flag in QA-report.md)

- [ ] All copy is final (no "placeholder" or "lorem ipsum")
- [ ] All financial values are realistic and consistent
- [ ] All screens have been navigated to at least once in a review session
- [ ] No orphaned screens (screens with no entry point)
- [ ] Icons are contextually appropriate (not just whatever fit)
- [ ] Loading / empty states exist for data-driven screens
- [ ] Error states exist for all form submissions

### Phase 7: Assemble Handoff Package

Generate these files:

**HANDOFF.md**
```markdown
# [Feature Name] Hi-Fi Prototype — Engineering Handoff

## Overview
[2-3 sentence summary of what this prototype covers and what it's ready for]

## Screen Index
| Screen | ID | Platform notes |
|---|---|---|
| Home | screen-home | Sticky nav, scroll |
| ... | | |

## Design Decisions
[Key choices made during hi-fi — why chartreuse is used here, why that modal is fullscreen, etc.]

## Known Gaps
[Anything not yet resolved — missing states, unresolved copy, etc.]

## Token Reference
See design-tokens.json. All values are final unless noted.

## Asset Requirements
[List any images, lottie animations, or assets that need to be provided]

## Open Questions
[Questions for engineering, product, or design before shipping]
```

**QA-report.md**
```markdown
# QA Report — [Feature Name]
Generated: [date]

## ✓ Passed
- Navigation completeness: all [N] screens reachable
- Arithmetic: all values verified
- [...]

## ⚠ Flags (deliver with note)
- Contrast: #8b8b87 on #fff = 3.8:1 (below AA for body text — consider #595954)
- [...]

## ✗ Failures (must fix before handoff)
- [none] or [list]
```

---

## Refinement Loop

After the initial hi-fi package is delivered, the user will iterate. The loop is always:

1. **Receive refinement input** (prompt / visual / Figma link)
2. **Identify the minimum change** — which screens, which elements
3. **Update HTML first** — make the change in the prototype
4. **Re-derive native** — only regenerate the affected screen files
5. **Re-run QA** for the affected screens
6. **Update HANDOFF.md** if decisions changed

Never regenerate the entire package for a small change. Be surgical.

---

## Tone & Communication

- Present the Phase 1 inventory clearly before building — it's the checkpoint that prevents rework
- When something is ambiguous, make a decision and state it: *"I've used chartreuse for the CTA here because it's the primary action — let me know if you'd prefer the dark button style"*
- Flag QA issues immediately, don't bury them
- Keep the handoff document human — engineers will read it

---

## Relationship to Other Skills

| Skill | Role |
|---|---|
| `lofi-prototype` | Produces the grayscale HTML that feeds into this skill |
| `hifi-prototype` (this) | Polishes HTML + derives iOS + Android + QA + handoff |
| `figma-sync` | Optionally captures the hi-fi HTML into Figma for design review before handoff |
| `prototype-agent` | Orchestrates the full workflow — calls this skill in Phase 4 (graduation) |

**Invocation from prototype-agent:**
```
Skill(skill="hifi-prototype", args="""
Graduate the lo-fi prototype at [path].
Figma reference: [URL if available]
Target: iOS (SwiftUI) + Android (Compose) + HTML
Screens in scope: [list]
""")
```

---

## Growing the Source of Truth

The tilt-app.html reference will be replaced as better sources emerge. When that happens:

1. Update the "Current Source of Truth" section above with the new source
2. Update the token table to reflect the authoritative values
3. Update code examples to match any new patterns
4. Note the date and reason for the update

The skill structure stays the same. Only the reference material evolves.
