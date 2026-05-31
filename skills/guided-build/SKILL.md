---
name: guided-build
description: >
  Scaffold a guided build project with design baked in. Sets up Next.js + Convex + Tailwind
  with the participant's chosen shape, theme, and vibe. For webshop: also builds all 3 features.
  For booking/quiz: scaffolds and hands off to the playground's CopyableCode prompts.
  Use when participant says "start the guided build", "start building", "scaffold my project",
  "let's build something", "I'm ready to code", or "Module 1".
  Also triggers on "I'm done building", "launch my project", "run it", "start the server",
  or "check localhost" — these skip to Phase 4 (launch) if the project directory already exists.
argument-hint: "[shape] [theme] — e.g. \"webshop stackshop\", \"booking yoga\", \"quiz movies\"; or \"launch\" to skip ahead to running an existing build"
---

# /guided-build — Project Scaffold + Design + Build

You are running the guided build for a beginner. The playground has already walked them through picking a shape (webshop / booking / quiz) and a theme. Your job depends on the shape:

- **Webshop** → scaffold the project AND build all 3 features (the playground has no build prompts for webshop — it says "the plugin handles everything")
- **Booking / Quiz** → scaffold the project and hand off to the playground (it provides CopyableCode prompts the participant will paste)

> **HARD RULES — these never bend.** This is a single-session warm-up build, and you are the only one driving.
> - **Never leave `~/Projects/masterclass/guided-build/`.** Every file you create or edit lives in that one folder. Do not touch anything elsewhere on the machine.
> - **Never run git or gh.** No `git init`, no commits, no branches, no pushes, no pull requests. This is not a versioned project.
> - **Never uninstall plugins** other than the one-time orientation cleanup in Phase 0.3. Leave every other plugin in place.
> - **Never deploy.** No `convex deploy`, no `vercel`, no `--prod` anything. Local dev only — the app runs on localhost and nowhere else.

Load `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/tone.md` **once now** and follow it throughout — don't re-read it between phases.

**Initial request:** $ARGUMENTS

---

## Phase 0: Pre-checks + session warning (~1 min, automatic)

### 0.1 Session warning (always show first)

> "Heads up — this project is designed to be built in one sitting. Keep Claude Code open until all features are done. If you close the session, your build context is lost and features may not connect properly if you restart."

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

If `$ARGUMENTS` contains "launch", "localhost", "run", "done", "start the server", or "I'm done" — and `~/Projects/masterclass/guided-build/` exists — skip to Phase 4.

**Once you know a shape is involved (not a launch skip-ahead), load `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/shapes.md` now** for shape + theme data. Load it once here — don't re-read it later.

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

Pin `create-next-app` to the major version the workshop promises (Next.js 16) so every participant gets the same, reproducible environment. `@latest` could float to a newer major mid-workshop and break the prompts.

```bash
cd ~/Projects/masterclass
npx create-next-app@16 guided-build --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*" --use-npm
```

**If it fails:** retry with `npm cache clean --force` first. If that fails: create the project manually (mkdir, npm init, install deps). If that fails: "Raise your hand — an instructor will help."

After it succeeds, read the real Next.js version from the generated `package.json` — you'll write the exact version into the project CLAUDE.md (step 2.5) so the stack line matches what's actually installed:

```bash
node -p "require('./guided-build/package.json').dependencies.next"
```

### 2.2 Set up Convex (webshop and booking only)

For webshop and booking shapes, set Convex up **without blocking the scaffold on browser sign-in.** Run `npx convex dev` in the background and keep working while the participant signs in.

```bash
cd ~/Projects/masterclass/guided-build
npm install convex
```

Then start the Convex dev process in the background (it watches files and writes `CONVEX_URL` to `.env.local` once the participant has signed in):

```bash
npx convex dev
```
Run this with `run_in_background` so it does not block. Tell the participant:

> "Convex is opening your browser to sign in. Create a free account if you don't have one — takes 30 seconds. I'll keep building the rest of your project while you do that."

**Don't wait on sign-in to continue.** Carry on with steps 2.3–2.5 (design, seed data, CLAUDE.md). Convex only needs to be connected by the time we launch (Phase 4), not during scaffold.

**Gate on `CONVEX_URL`, not on the browser.** Before any step that actually needs the live database (writing/running Convex functions, or launch), check that the URL has appeared:

```bash
grep -q "CONVEX_URL" ~/Projects/masterclass/guided-build/.env.local && echo "CONVEX_READY" || echo "CONVEX_PENDING"
```

If it shows `CONVEX_PENDING`, keep going with everything that doesn't need the database. Only if it is still pending at launch do you prompt the participant to finish signing in (Phase 4.3 handles this).

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

Write the **Stack** line from the actual installed versions, not from memory. Use the Next.js version you read in step 2.1 (`package.json` → `dependencies.next`), and read Tailwind (and Convex, if installed) the same way, so the stack line is always true to what's on disk:

```bash
node -p "[require('./package.json').dependencies.next, (require('./package.json').dependencies.tailwindcss||require('./package.json').devDependencies.tailwindcss), require('./package.json').dependencies.convex].filter(Boolean)"
```

```markdown
# Guided Build — [Shape] / [Theme]

Module 1 warm-up project. Created by `/guided-build` from the `guided-build` plugin.

**Shape:** [shape] | **Theme:** [theme] | **Vibe:** [vibe]
**Stack:** Next.js [exact version from package.json] + Tailwind CSS [exact version] [+ Convex [exact version] if webshop/booking]
```

**Section 2: Session rules**
```markdown
## Session rules

This project is built in ONE Claude Code session. All features depend on context from earlier in this session. Do not close Claude Code until all features are complete.

No git. No GitHub. No pull requests. No tickets. Features are sections in PRD.md — that is the only planning artefact.

This folder is self-contained and safe to delete after the workshop.
```

**Section 3: Design rules** (copy from `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/design-guide.md`)

This means every prompt the participant pastes from the playground will make Claude read these rules first.

---

## Phase 3: Branch by shape

**If shape is `webshop`** → continue to Phase 3W (active build).
**If shape is `booking` or `quiz`** → jump to Phase 3H (passive handoff).

---

## Phase 3W: Webshop — Build all 3 features

The playground's webshop build steps have no CopyableCode prompts. They say "the plugin handles everything." So you build all 3 features now, in sequence.

The design rules already live in the project's `CLAUDE.md` (you copied `design-guide.md` into it in step 2.5), so they're loaded automatically with the project — **don't re-read the reference file.** Just follow the anti-slop rules in the project CLAUDE.md for everything you build.

### Coding standards — read these before writing the first feature

This is the participant's first app, so don't build it blind. These are the **only** standards that apply to a beginner Next.js + Tailwind (+ Convex) warm-up — a trimmed subset of the team's full coding-standards. Keep it this simple; do not pull in the rest.

**Quick reference — the non-negotiables for this project:**

- **Named exports only** — except Next.js `page.tsx`/`layout.tsx`, which use `export default`.
- **No `any`** — if you don't know a type, use a real one or `unknown` and narrow. Don't reach for `any`.
- **No hardcoded hex colours in components** — use the CSS custom properties / Tailwind classes set up in `globals.css` (step 2.3). They're the theme.
- **`next/image` not raw `<img>`** — use the `<Image />` component for product images and placeholders.
- **Keep components small and readable** — this is a first project. A clear 30-line component beats a clever 10-line one.

**React / Next.js rules:**

- **Server Components are the default.** Only add `"use client"` when a file actually needs hooks, event handlers (`onClick`), or browser APIs. The cart (useState) and interactive bits are client; the catalog page can fetch on the server.
- **No `useEffect` for derived state.** If a value can be computed from existing state, compute it during render. The cart total is `items.reduce(...)` inline — never a separate `useState` + `useEffect` that syncs it.
- **No `useEffect` for data fetching.** Read Convex data with `useQuery(api.module.function)` — it's already real-time. Never `useEffect` + `fetch`.
- **Semantic HTML** — `header`, `section`, `nav`, `button`. Not a pile of `div`s.

**Convex rules (webshop only):**

- **Queries read, mutations write.** Use `query()` to fetch products/orders, `mutation()` to write an order. Never call an external API from a mutation.
- **`useQuery` for reads, `useMutation` for writes** on the frontend.
- This is a local warm-up with no auth — it's fine to leave functions public, but add a one-line comment saying so (`// Public: warm-up project, no auth`) so the habit sticks for the real project.
- **Cap your reads** — there are only 3 products, but still use `.take(50)` / `.first()` rather than unbounded `.collect()` as a habit.

### Feature 1: Product Catalog (~15 min)

Build a product grid page showing the 3 seeded products from Convex.

**What to build:**
- Product grid layout (not identical cards — vary at least one element)
- Each product shows: image placeholder (coloured gradient box, not grey), name, price, short description
- Wire to a Convex query that fetches all products
- Responsive: looks good on desktop and mobile

**After build:**
> "Product catalog done. Moving on to the cart."

### Feature 2: Shopping Cart (~15 min)

Build on top of the catalog. Do not start from scratch.

**What to build:**
- "Add to Cart" button on each product card
- Cart state managed with React useState (client-side, not Convex)
- Cart page or slide-out with: list of items, quantity controls (+/−), running total
- Cart badge in the header showing number of items
- Remove-from-cart option

**After build:**
> "Cart done. One more — checkout."

### Feature 3: Checkout & Order (~10 min)

Build on top of the cart. Completes the full shopping flow.

**What to build:**
- Checkout page with form: name + email fields
- Mock payment step (button that simulates processing — short delay, then success)
- Convex mutation to write an order record with a unique order number
- Order confirmation page showing: order number, items purchased, total, customer name
- Cart clears after successful checkout

**After build:**
> "All 3 features are built. Tidying up, then starting your app."

Run the cleanup pass below ("After all 3 features: cleanup pass"), then proceed to Phase 4.

### Webshop build rules

- Follow the tone you already loaded (`tone.md`, loaded once at the top) — brief check-ins between features, not verbose commentary
- The anti-slop design rules live in the project `CLAUDE.md` — they apply to everything, and you don't need to re-read the reference file
- Each feature extends the previous — never rebuild what already exists
- If something breaks, fix it silently. Don't explain debugging unless asked.
- Don't ask permission for implementation details — just build. Participant watches.
- Keep the code simple and readable — this is a beginner's first project

### After all 3 features: cleanup pass

Once the three features are built and before you move to launch, dispatch the **`gb-simplifier`** agent for one silent cleanup pass — the same post-build cleanup the development plugins run.

1. Read `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/simplifier-prompt.md` and dispatch `gb-simplifier` with that prompt (it scans the project read-only and reports findings — it cannot edit anything itself).
2. Apply its findings **silently** — make the fixes yourself, don't narrate them or surface the cleanup to the participant.
3. Expect 0–10 small findings. If it reports nothing, move on.

Then proceed to Phase 4.

---

## Phase 3H: Passive handoff (booking and quiz)

For booking and quiz shapes, the playground provides CopyableCode prompts the participant will paste. Your active build work is done after scaffold — but you still handle launch.

> "Scaffold is ready. Head to the playground — it has your build prompts. Paste them into Claude Code one at a time."
>
> "When all your features are built, say 'I'm done' and I'll launch your app."

The CLAUDE.md you wrote ensures design quality and session rules for all pasted prompts.

When the participant says they are done, run the **cleanup pass** before launch — the same one the webshop path uses:

1. Read `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/simplifier-prompt.md` and dispatch the **`gb-simplifier`** agent with that prompt. It scans the project read-only and reports findings.
2. Apply its findings **silently** — make the fixes yourself, don't narrate them.
3. Expect 0–10 small findings; if none, move on.

Then proceed to Phase 4.

---

## Phase 4: Launch (~2 min, autonomous)

All shapes converge here. For webshop this runs immediately after Phase 3W. For booking/quiz this runs when the participant says they are done building.

### 4.1 Start the dev server

**First (webshop/booking only): confirm Convex finished connecting.** The `npx convex dev` you started in the background (step 2.2) writes `CONVEX_URL` to `.env.local` once the participant has signed in. Check for it now:

```bash
grep -q "CONVEX_URL" ~/Projects/masterclass/guided-build/.env.local && echo "CONVEX_READY" || echo "CONVEX_PENDING"
```

If `CONVEX_PENDING`, the participant likely hasn't finished the browser sign-in yet — this is the one moment it actually blocks, so prompt them:

> "Almost there — Convex just needs you to finish signing in. A browser tab should have opened earlier. Complete it and let me know."

Re-run the `grep` check until it shows `CONVEX_READY`, then continue. For quiz, there's no Convex — skip straight to starting the server.

<!-- Docker detection — guided-build now OWNS this 0.0.0.0 binding logic. The development /localhost skill that used to mirror it has been deleted, so there is no longer a sibling to keep in sync; this is the single source of truth for the container-binding workaround. -->

Detect whether you are running inside a Docker container:

```bash
[ -f /.dockerenv ] && echo "CONTAINER=true" || echo "CONTAINER=false"
```

**If inside a container** (`CONTAINER=true`):

Next.js binds to `127.0.0.1` by default. Inside Docker, the host browser cannot reach `127.0.0.1` even with port forwarding — the server must bind to `0.0.0.0`.

```bash
cd ~/Projects/masterclass/guided-build && npx next dev -H 0.0.0.0
```

If port 3000 is taken:
```bash
npx next dev -H 0.0.0.0 --port 3001
```

**If not inside a container** (`CONTAINER=false`):

```bash
cd ~/Projects/masterclass/guided-build && npm run dev
```

If port 3000 is taken:
```bash
npx next dev --port 3001
```

Wait for the server to be ready (watch for "Ready" or "compiled" in stdout). Store the port for messaging below.

### 4.2 Verify the page loads (silent)

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:3000
```

Check for HTTP 200. If 200, also fetch the body and confirm it contains real content (not a Next.js error page or empty document). For webshop/booking: check for absence of Convex connection errors.

If the check passes, skip to 4.4.

### 4.3 Silent fix loop (max 3 attempts)

If the page does not load or shows errors, diagnose and fix silently. Do not narrate debugging to the participant.

**Port conflict** — another process on the port. Kill it or switch ports, restart the server.

**Missing dependency** — `npm install`, restart.

**Build/TypeScript error** — read the error from terminal output, fix the source file, wait for hot-reload, re-check.

**Convex not connected** — check `.env.local` for `CONVEX_URL`. If it's missing, the background `npx convex dev` from step 2.2 is still waiting on sign-in (or stopped). Re-start it in the background if it's no longer running, then gate on the URL appearing rather than blocking:
```bash
grep -q "CONVEX_URL" ~/Projects/masterclass/guided-build/.env.local && echo "CONVEX_READY" || echo "CONVEX_PENDING"
```
If the participant never completed Convex sign-up, this is the one case where you speak:
> "Convex needs you to sign in — a browser tab should have opened. Complete the sign-up and let me know."

Poll the `grep` until it shows `CONVEX_READY`, then restart the dev server and re-check.

**Blank page** — check `src/app/page.tsx` renders content, check for hydration mismatches, fix the component.

**Binding issue (container only)** — if `CONTAINER=true` and curl works inside the container but the participant reports they cannot see the page, the server is bound to `127.0.0.1` instead of `0.0.0.0`. Kill the server and restart with `-H 0.0.0.0`.

**Hot-reload not working (container only)** — file system events may not propagate into Docker containers on macOS. If the participant changes a file but the browser does not update, set polling mode and restart the server:
```bash
export WATCHPACK_POLLING=true
```

After each fix, wait for hot-reload or restart, then re-run the curl check. If all 3 attempts fail, escalate:

> "I've hit something I can't fix automatically. Raise your hand — an instructor will sort this out."

### 4.4 Present the working app

Once the page loads successfully, tell the participant:

> "Your app is running at http://localhost:[port] — open it in your browser and have a look around. Let me know what you think."

Wait for the participant's feedback. If they report issues, fix them silently and confirm. If they are happy, proceed to 4.5.

### 4.5 Wrap-up

**Webshop:**
> "That's a working shop — three features, all connected:"
> "- A product page pulling live data from Convex"
> "- A cart that tracks what you add"
> "- A checkout that writes real orders to your database"
>
> "Head back to the playground for the next step."

**Booking:**
> "That's a working reservation system:"
> "- An availability grid showing open and booked slots"
> "- A booking form that writes to your Convex database"
> "- An admin page with real-time updates"
>
> "Head back to the playground for the next step."

**Quiz:**
> "That's a working quiz:"
> "- Timed questions with instant feedback"
> "- A scoring system with rank names"
> "- A share card you can copy"
>
> "Head back to the playground for the next step."

---

## Rules

Communication follows `tone.md`, which you already loaded once at the start — no need to re-read it.

- **Be fast.** This is a 60-minute build. Every minute of setup is a minute not building.
- **Be friendly.** Many participants are scaffolding for the first time.
- **If something breaks, fix it.** Don't explain debugging. Just fix it and move on.
- **Never block.** If Convex sign-up stalls, skip it and let the participant set it up later.
- **The wow moment is the designed shell.** When they open localhost, it should look like a real app — not a blank starter page.
- **Stay in `guided-build/`.** All files go in the subdirectory.
- **No git.** Don't initialise a repository, create commits, or push to GitHub. This is a single-session warm-up.
- **Session context is everything.** All 3 features (webshop) depend on shared context. Remind the participant not to close Claude Code mid-build.
