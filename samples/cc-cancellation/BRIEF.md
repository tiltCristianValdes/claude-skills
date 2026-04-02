# Sample: CC Cancellation Flow

Use this sample to practice the full prototyping workflow or to test `hifi-prototype` directly with a visual reference.

## The flow

A credit card account cancellation flow triggered from the CC Management screen. Covers the happy path and four edge cases that block or modify the cancellation.

**Feature:** CC Cancellation  
**Platform:** iOS mobile (393×852)  
**Theme:** Light screens throughout  

## Screen inventory

| Screen | Description |
|---|---|
| CC Management | Active CC overview — $155.75 balance, status, highlights, Leap challenges |
| More modal | Bottom sheet — Autopay, Statements, Feedback, Help, Close Credit Card account |
| Close account — no balance | Confirmation screen with illustration and "Things to consider" |
| Close account — successful | Success state with illustration and "Done" CTA |
| Outstanding balance blocker | Blocks cancellation — user must pay off balance first |
| Subscription modal | "Are you sure?" checklist of what the user loses access to |
| Pending transaction blocker | Blocks cancellation — pending txn or payment in progress |
| Credit balance blocker | User has paid more than they owe — contact support to resolve |
| Home — account closed | Home screen post-cancellation with "Account closed" badge on CC card |

## Navigation

```
CC Management
  └── More modal (••• pill)
        └── "Close Credit Card account"
              ├── [No balance, no pending] → Close account confirmation
              │     ├── Back → CC Management
              │     └── Close account → Close account - successful → Home (closed)
              ├── [Outstanding balance] → Outstanding balance blocker
              │     └── I understand → CC Management
              ├── [Has subscription] → Subscription confirmation modal
              │     ├── Cancel → CC Management
              │     └── Confirm → Close account - successful → Home (closed)
              ├── [Pending txn or payment] → Pending transaction blocker
              │     └── Got it → CC Management
              └── [Credit balance] → Credit balance blocker
                    └── Contact support → (external)
```

## Reference material

`cc-cancellation-flow.png` — hi-fi design showing all screens and the decision logic between them.

Figma link: add yours here if available — exact values for spacing, typography, and color will make the output more precise.

## Try it

**Option 1 — Full workflow via prototype-agent:**
```
Run the prototyping workflow for the CC Cancellation feature.
Reference image: samples/cc-cancellation/cc-cancellation-flow.png
The flow covers account cancellation from the CC management screen, including four edge cases: outstanding balance, active subscription confirmation, pending transaction, and credit balance. All screens are light theme, 393×852.
```

**Option 2 — Direct hifi build (Mode 2, skip the lofi):**
```
Build a hi-fi prototype for the CC Cancellation flow.
Visual reference: samples/cc-cancellation/cc-cancellation-flow.png
All screens, navigation graph, and edge cases are defined in samples/cc-cancellation/BRIEF.md.
Use tilt-app.html as the brand reference.
Output: ~/Desktop/cc-cancellation-hifi.html
```

**Option 3 — Quick lofi first, then graduate:**
```
Build a lofi prototype for the CC Cancellation flow.
Reference: samples/cc-cancellation/BRIEF.md
9 screens. Light theme throughout. Include all four edge case paths.
```
Then once reviewed:
```
Graduate the lofi at ~/Desktop/cc-cancellation-prototype.html to hi-fi.
Visual reference: samples/cc-cancellation/cc-cancellation-flow.png
```
