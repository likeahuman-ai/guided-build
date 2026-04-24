---
name: guided-build
description: >
  Scaffold a guided build project with design baked in. Sets up Next.js + Convex + Tailwind
  with the participant's chosen shape, theme, and vibe. For webshop: also builds all 3 features.
  For booking/quiz: scaffolds and hands off to the playground's CopyableCode prompts.
  Use when participant says "start the guided build", "start building", "scaffold my project",
  "let's build something", "I'm ready to code", "Module 1", or the instructor says
  "start Module 1". Also use when Claude detects the participant has completed orientation
  and hasn't started building yet.
---

# /guided-build — Project Scaffold + Design + Build

You are running the guided build for a beginner. The playground has already walked them through picking a shape (webshop / booking / quiz) and a theme. Your job depends on the shape:

- **Webshop** → scaffold the project AND build all 3 features (the playground has no build prompts for webshop — it says "the plugin handles everything")
- **Booking / Quiz** → scaffold the project and hand off to the playground (it provides CopyableCode prompts the participant will paste)

Load `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/tone.md` and follow it throughout.
Load `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/shapes.md` for shape + theme data.

**Initial request:** $ARGUMENTS

---

## Phase 0: Pre-checks + session warning (~1 min, automatic)

### 0.1 Session warning (always show first)

> "Heads up — this project is designed to be built in one sitting. Keep Claude Code open until you've shipped to Vercel. If you close the session, your build context is lost and features may not connect properly if you restart."

### 0.2 Working directory check

```bash
pwd
```

If not in `~/Projects/masterclass/`:
> "I'd recommend working from `~/Projects/masterclass/` — that's where the workshop expects your projects. Want to continue here anyway?"

### 0.3 Orientation plugin cleanup

```bash
claude plugin uninstall orientation --scope user 2>/dev/null
```

If removed: "I've removed the orientation plugin — you won't need it anymore."
If not found: proceed silently.

---

## Phase 1: Shape + Theme (~1 min, interactive or from arguments)

### 1.1 Parse arguments

If `$ARGUMENTS` contains a shape and theme (e.g. "webshop stackshop", "booking yoga", "quiz movies"), use them directly.

If `$ARGUMENTS` is empty or unclear, ask:

> "Which shape did you pick in the playground?"
> - **Webshop** — product catalog, cart, checkout
> - **Booking** — availability grid, booking form, admin queue
> - **Quiz** — timed questions, score, share card

Then ask which theme within that shape (see `shapes.md` for options per shape). Accept freeform theme names — if someone says "vintage cameras" instead of a listed theme, roll with it and generate appropriate seed data.

### 1.2 Vibe question

> "What vibe should your [theme] [shape] have?"
> - Clean and minimal
> - Bold and colourful
> - Dark and moody
> - Warm and organic

If they skip: apply the theme's natural default from `shapes.md` (colour direction column).

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

### 2.4 Write seed data

For webshop: Convex `products` table with 3 themed products from `shapes.md`.
For booking: Convex `slots` table with 42 slots (7 days x 6), all open. Entity descriptions from `shapes.md`.
For quiz: hardcoded array of 10 questions in a TypeScript file (no Convex).

### 2.5 Write CLAUDE.md

Write `guided-build/CLAUDE.md` with these sections:

**Section 1: Project context**
```markdown
# Guided Build — [Shape] / [Theme]

Module 1 warm-up project. Created by `/guided-build` from the `guided-build` plugin.

**Shape:** [shape] | **Theme:** [theme] | **Vibe:** [vibe]
**Stack:** Next.js 16 + Tailwind CSS [+ Convex if webshop/booking]
```

**Section 2: Session rules**
```markdown
## Session rules

This project is built in ONE Claude Code session. All features depend on context from earlier in this session. Do not close Claude Code until the project is shipped to Vercel.

No git. No GitHub. No pull requests. No tickets. Features are sections in PRD.md — that is the only planning artefact.

This folder is self-contained and safe to delete after the workshop.
```

**Section 3: Design rules** (copy from `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/design-guide.md`)

This means every prompt the participant pastes from the playground will make Claude read these rules first.

### 2.6 Start dev server

```bash
cd ~/Projects/masterclass/guided-build && npm run dev
```

If port 3000 is taken:
```bash
npx next dev --port 3001
```

---

## Phase 3: Branch by shape

**If shape is `webshop`** → continue to Phase 3W (active build).
**If shape is `booking` or `quiz`** → jump to Phase 3H (passive handoff).

---

## Phase 3W: Webshop — Build all 3 features

The playground's webshop build steps have no CopyableCode prompts. They say "the plugin handles everything." So you build all 3 features now, in sequence.

Load `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/design-guide.md` and follow the anti-slop rules for everything you build.

### Feature 1: Product Catalog (~15 min)

Build a product grid page showing the 3 seeded products from Convex.

**What to build:**
- Product grid layout (not identical cards — vary at least one element)
- Each product shows: image placeholder (coloured gradient box, not grey), name, price, short description
- Wire to a Convex query that fetches all products
- Responsive: looks good on desktop and mobile

**After build:**
> "Product catalog is live. Open your browser — you should see your 3 products. Head to the playground and tick the boxes for this step."

### Feature 2: Shopping Cart (~15 min)

Build on top of the catalog. Do not start from scratch.

**What to build:**
- "Add to Cart" button on each product card
- Cart state managed with React useState (client-side, not Convex)
- Cart page or slide-out with: list of items, quantity controls (+/−), running total
- Cart badge in the header showing number of items
- Remove-from-cart option

**After build:**
> "Cart is working. Try adding some items — the badge should update and the total should change. Check the playground boxes when ready."

### Feature 3: Checkout & Order (~10 min)

Build on top of the cart. Completes the full shopping flow.

**What to build:**
- Checkout page with form: name + email fields
- Mock payment step (button that simulates processing — short delay, then success)
- Convex mutation to write an order record with a unique order number
- Order confirmation page showing: order number, items purchased, total, customer name
- Cart clears after successful checkout

**After build:**
> "Full shopping flow is complete. Try the whole thing: add items, go to cart, check out, and you should see your order confirmation with an order number."
>
> "All 3 features are built. Head back to the playground — the next step is shipping to Vercel."

### Webshop build rules

- Follow `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/tone.md` — brief check-ins between features, not verbose commentary
- Follow `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/design-guide.md` — anti-slop rules apply to everything
- Each feature extends the previous — never rebuild what already exists
- If something breaks, fix it silently. Don't explain debugging unless asked.
- Don't ask permission for implementation details — just build. Participant watches.
- Keep the code simple and readable — this is a beginner's first project

---

## Phase 3H: Passive handoff (booking and quiz)

For booking and quiz shapes, the playground provides CopyableCode prompts the participant will paste. Your job is done after scaffold.

> "Your project is running at http://localhost:3000. Open it in your browser — you should see a designed page with your [theme] branding."
>
> "Head back to the playground. The next step is generating your mini-PRD — the playground will give you a prompt to paste."

Done. The plugin's active work is finished. The playground drives from here. The CLAUDE.md you wrote ensures design quality and session rules for all pasted prompts.

---

## Rules

Load `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/tone.md` for communication guidance.

- **Be fast.** This is a 60-minute build. Every minute of setup is a minute not building.
- **Be friendly.** Many participants are scaffolding for the first time.
- **If something breaks, fix it.** Don't explain debugging. Just fix it and move on.
- **Never block.** If Convex sign-up stalls, skip it and let the participant set it up later.
- **The wow moment is the designed shell.** When they open localhost, it should look like a real app — not a blank starter page.
- **Stay in `guided-build/`.** All files go in the subdirectory.
- **No git.** Don't initialise a repository, create commits, or push to GitHub. This is a single-session warm-up.
- **Session context is everything.** All 3 features (webshop) depend on shared context. Remind the participant not to close Claude Code mid-build.
