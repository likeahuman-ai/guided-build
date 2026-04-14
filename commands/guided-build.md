---
description: >
  Scaffold a guided build project with design baked in. Sets up Next.js + Convex + Tailwind
  with the participant's chosen shape, theme, and vibe. Run after picking your project in the
  playground. Use when participant says "start the guided build", "scaffold my project",
  "set up my project", or the playground tells them to run this command.
argument-hint: "[shape] [theme] — e.g. webshop coffee, booking yoga, quiz movies"
---

# /guided-build — Project Scaffold + Design

You are scaffolding a guided build project for a beginner. The playground has already walked them through picking a shape (webshop / booking / quiz) and a theme. Your job: set up the project with the right tech stack and design, so the playground's build prompts work.

Load `${CLAUDE_PLUGIN_ROOT}/references/tone.md` and follow it throughout.
Load `${CLAUDE_PLUGIN_ROOT}/references/shapes.md` for shape + theme data.

**Initial request:** $ARGUMENTS

---

## Phase 0: Pre-checks (~1 min, automatic)

### 0.1 Working directory check

```bash
pwd
```

If not in `~/Projects/masterclass/`:
> "I'd recommend working from `~/Projects/masterclass/` — that's where the workshop expects your projects. Want to continue here anyway?"

### 0.2 Orientation plugin cleanup

```bash
claude plugin uninstall lah-orientation --scope user 2>/dev/null
```

If removed: "I've removed the orientation plugin — you won't need it anymore."
If not found: proceed silently.

### 0.3 Telemetry config check

```bash
cat ~/.lah/telemetry-config.json 2>/dev/null
```

If missing: proceed silently. Telemetry events will no-op.

---

## Phase 1: Shape + Theme (~1 min, interactive or from arguments)

### 1.1 Parse arguments

If `$ARGUMENTS` contains a shape and theme (e.g. "webshop coffee", "booking yoga", "quiz movies"), use them directly.

If `$ARGUMENTS` is empty or unclear, ask:

> "Which shape did you pick in the playground?"
> - **Webshop** — product catalog, cart, checkout
> - **Booking** — availability grid, booking form, admin queue
> - **Quiz** — timed questions, score, share card

Then ask which theme within that shape (see `shapes.md` for the 4 options per shape).

### 1.2 Vibe question

> "What vibe should your [theme] [shape] have?"
> - Clean and minimal
> - Bold and colourful
> - Dark and moody
> - Warm and organic

If they skip: apply the theme's natural default from `shapes.md` (colour direction column).

### 1.3 Telemetry

```bash
bash ${CLAUDE_PLUGIN_ROOT}/telemetry/send-event.sh "guided-build:shape-selected" "{\"shape\":\"SHAPE\",\"theme\":\"THEME\",\"vibe\":\"VIBE\"}"
```

---

## Phase 2: Scaffold (~5 min, autonomous)

**Goal:** Create a running project with design baked in, so the playground's PRD prompt ("my starter already has Next.js 16 + Tailwind + Convex") is true.

### 2.1 Create Next.js project

```bash
cd ~/Projects/masterclass
npx create-next-app@latest guided-build --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*" --use-npm
```

**If it fails:** retry with `npm cache clean --force` first. If that fails: create the project manually (mkdir, npm init, install deps). If that fails: "Raise your hand — an instructor will help."

### 2.2 Set up Convex (webshop and booking only)

For webshop and booking shapes:

```bash
cd ~/Projects/masterclass/guided-build
npm install convex
npx convex dev
```

> "Convex is opening your browser to sign in. Create a free account if you don't have one — takes 30 seconds."

For quiz: skip Convex. The quiz is client-side only.

### 2.3 Apply design

Based on the vibe selection, configure the project's visual identity:

**Fonts:** Add a Google Fonts import to `src/app/layout.tsx`. Pick a distinctive pairing — NOT Inter, Roboto, Open Sans, DM Sans, Space Grotesk, Outfit, or Plus Jakarta Sans. Match the font to the theme + vibe (see `shapes.md` colour direction for guidance).

**Colours:** Add CSS custom properties to `src/app/globals.css`:
```css
:root {
  --color-primary: /* dominant from theme */;
  --color-accent: /* accent from theme */;
  --color-bg: /* background */;
  --color-text: /* tinted neutral, not pure black */;
  --color-muted: /* lighter text */;
  --color-border: /* tinted border */;
}
```

**Background:** Apply a subtle background treatment — gradient, tint, or texture. Not flat white.

**Layout:** Set up a basic page structure in `src/app/page.tsx` with the project name, a header, and styled empty content area. The participant should see a designed shell on localhost, not a blank Next.js starter.

### 2.4 Write Convex schema + seed data

For webshop: `products` table with 3 themed products from `shapes.md`.
For booking: `slots` table with 42 slots (7 days x 6), all open. Entity descriptions from `shapes.md`.
For quiz: hardcoded array of 10 questions in a TypeScript file (no Convex).

### 2.5 Write CLAUDE.md

Write `guided-build/CLAUDE.md` with two sections:

**Section 1: Project context**
```markdown
# Guided Build — Warm-Up Project

This is a Module 1 warm-up project, not the participant's main project.
Created by `/guided-build` from the `lah-guided-build` plugin.

Shape: [shape] | Theme: [theme] | Vibe: [vibe]
Stack: Next.js 16 + Tailwind CSS [+ Convex if applicable]

This folder is self-contained and safe to delete after the workshop.
```

**Section 2: Design rules** (copy from `${CLAUDE_PLUGIN_ROOT}/references/design-guide.md`)

This means every prompt the participant pastes from the playground will make Claude read these rules first.

### 2.6 Start dev server

```bash
cd ~/Projects/masterclass/guided-build && npm run dev
```

If port 3000 is taken:
```bash
npx next dev --port 3001
```

### 2.7 Telemetry

```bash
bash ${CLAUDE_PLUGIN_ROOT}/telemetry/send-event.sh "guided-build:scaffolded" "{\"shape\":\"SHAPE\",\"theme\":\"THEME\",\"vibe\":\"VIBE\"}"
```

---

## Phase 3: Handoff (~30 sec)

> "Your project is running at http://localhost:3000. Open it in your browser — you should see a designed page with your [theme] branding."
>
> "Head back to the playground. The next step is generating your mini-PRD — the playground will give you a prompt to paste."

Done. The plugin's active work is finished. The playground drives from here.

---

## Rules

Load `${CLAUDE_PLUGIN_ROOT}/references/tone.md` for communication guidance.

- **Be fast.** This is setup, not the build. Get the project running and hand off.
- **Be friendly.** Many participants are scaffolding for the first time.
- **If something breaks, fix it.** Don't explain debugging. Just fix it and move on.
- **Never block.** If Convex sign-up stalls, skip it and let the participant set it up later.
- **The wow moment is the designed shell.** When they open localhost, it should look like a real app — not a blank starter page.
- **Stay in `guided-build/`.** All files go in the subdirectory.
- **Don't build features.** The playground's prompts handle that. You only scaffold.
