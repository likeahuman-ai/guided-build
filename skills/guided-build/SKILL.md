---
name: guided-build
description: >
  Take a Module 1 workshop participant from idea to a designed, running first app — and to a
  live URL when they ask. Scoped to the workshop's guided-build warm-up project, never other
  repositories. Use when a workshop participant says "start the guided build", "start
  building", "scaffold my project", "I'm ready to code", or "Module 1"; when they name a
  feature of the warm-up build ("build the product catalog", "build the cart") — that
  continues an existing build; when they want to see it run ("I'm done building", "launch my
  project", "run it", "check localhost"); when they ask to put it live ("ship it", "deploy",
  "put it live", or a pasted Vercel deploy prompt); or when they ask it for real storage
  ("save my orders", "I want a real database", "can we use Convex").
argument-hint: "[shape] [theme] — e.g. \"webshop stackshop\", \"booking yoga\", \"quiz movies\""
---

# /guided-build — Project Scaffold + Design + Build

You are running the guided build for a beginner. The playground has already walked them through picking a shape (webshop / booking / quiz) and a theme. Your job depends on the shape:

- **Webshop** → scaffold the project AND build all 3 features (the playground has no build prompts for webshop — it says "the plugin handles everything")
- **Booking / Quiz** → scaffold the project and hand off to the playground (it provides CopyableCode prompts the participant will paste)

> **HARD RULES — these never bend.** This is a single-session warm-up build, and you are the only one driving. (Numbers match the plugin's rules register; 5 and 6 are the coding-standards and design rules carried in the phase text.)
> 1. **The project lives in `~/Projects/masterclass/guided-build/`.** Every project file you create or edit lives there. Throwaway scratch (server logs, capture files) goes to the system temp directory (`$TMPDIR`, e.g. `/tmp/guided-build/`) — or your session's scratchpad directory if the harness provides one. Never the project folder, never other user directories.
> 2. **Never run git or gh.** No `git init`, no commits, no branches, no pushes, no pull requests. `create-next-app` initialises a repo on its own — delete that `.git` directory immediately after the scaffold (step 2.1).
> 3. **Never deploy unprompted.** The build ends on localhost. When the participant asks to ship — the playground's ship step, "deploy", "put it live" — run Phase 5. Outside that request: no `vercel`, no `convex deploy`, no `--prod`.
> 4. **Never uninstall plugins** other than the one-time orientation cleanup in Phase 0.3.
> 7. **A build request runs the whole remaining flow.** "Build the product catalog" means the guided build, not one feature. Continue through every remaining feature, the cleanup pass, and launch without pausing to ask. The only stops are the named gates: vibe (1.2), PRD beat (2.5), playground handoff (3H), launch feedback (4.4), ship request (Phase 5) — plus, inside Phase 5 only, the sign-in waits in 5.1/5.2, the sole permitted service waits in the whole flow. A gate already passed (or skipped past via a 1.1 skip-ahead) never re-opens.

The default project needs **zero external services and zero sign-ins** — data lives in local files. A database is an upgrade the participant must ask for (see "Persistence upgrades" below). Never install one unprompted.

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

Either way, fix the result in your head for the whole run: **the project directory is `<chosen root>/guided-build/`** — by default `~/Projects/masterclass/guided-build/`. Every later step that says "the project root" or "the project directory" means exactly that directory.

### 0.3 Orientation plugin cleanup

```bash
claude plugin uninstall orientation --scope user 2>/dev/null
```

If removed: "I've removed the orientation plugin — you won't need it anymore."
If not found: proceed silently.

---

## Phase 1: Shape + Theme (~1 min, interactive or from context)

### 1.1 Parse arguments and context

Skip-aheads first — all three require the project directory (0.2 — default `~/Projects/masterclass/guided-build/`) to exist. Check them in this order; the first match wins, and a ship match always beats a launch match:

1. **Ship** — `$ARGUMENTS` or the request contains "ship it", "ship this", "deploy", "put it live", "go live", or is a pasted deploy prompt (a prompt whose goal is a live URL) → skip to **Phase 5**. The word "shipping" alone — shipping costs, shipping address — is webshop vocabulary, not a ship request.
2. **Launch** — `$ARGUMENTS` or the request contains "launch", "localhost", "run it", "start the server", "check localhost", or "I'm done" → skip to **Phase 4**.
3. **Build resume** — the request is a build request ("build the …", a feature name) and the project is already scaffolded → read **Shape / Theme / Vibe** from the project's `CLAUDE.md` (2.4 wrote them there; if that file is absent, infer from context as below), then skip to **Phase 3**, entering at the first feature that does not yet exist in `src/` — check the files, don't ask, and never rebuild one that exists. If every feature exists, run the cleanup pass and go to Phase 4. If `PRD.md` is missing, first write it yourself from `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/shapes.md` — don't re-present the PRD beat; the participant has moved on.

**Once you know a shape is involved, load `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/shapes.md` now** for shape + theme data. Load it once here — don't re-read it later.

Take the shape and theme from wherever the session already knows them, in this order:
1. `$ARGUMENTS` (e.g. "webshop stackshop", "booking yoga", "quiz movies")
2. Earlier conversation context — a pasted PRD, a mentioned theme, a feature name that implies a shape ("product catalog" → webshop)
3. Only if still unknown, ask:

> "Which shape did you pick in the playground?"
> - **Webshop** — product catalog, cart, checkout
> - **Booking** — availability grid, booking form, admin queue
> - **Quiz** — timed questions, score, share card

Then the theme within that shape (see `shapes.md` for options). Accept freeform theme names — if someone says "vintage cameras" instead of a listed theme, roll with it and generate appropriate seed data. Never re-ask something the session already answered.

### 1.2 Vibe question

Ask as a structured choice with a recommended default — the theme's natural colour direction from `shapes.md` marks the recommended option:

> "What vibe should your [theme] [shape] have?"
> - Clean and minimal
> - Bold and colourful
> - Dark and moody
> - Warm and organic

If they skip or shrug: apply the recommended default and move on.

---

## Phase 2: Scaffold (~3 min, autonomous)

**Goal:** a running project with design baked in, so the starter the playground's PRD prompt assumes is already on disk by the time the participant pastes it.

### 2.1 Create Next.js project

Pin `create-next-app` to the major version the workshop promises (Next.js 16) so every participant gets the same, reproducible environment. `@latest` could float to a newer major mid-workshop and break the prompts.

`create-next-app` initialises a git repository the workshop must not have (Rule 2) — remove it in the same invocation, so the same working directory anchors both commands:

```bash
cd ~/Projects/masterclass
npx create-next-app@16 guided-build --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*" --use-npm
rm -rf guided-build/.git
```

(If the participant chose a different root at 0.2, run the same pair from that root.)

**If the scaffold fails:** retry with `npm cache clean --force` first. If that fails: create the project manually (mkdir, npm init, install deps). If that fails: "Raise your hand — an instructor will help."

Then read the real Next.js version from the generated `package.json` — you'll write the exact version into the project CLAUDE.md (step 2.4):

```bash
node -p "require('./guided-build/package.json').dependencies.next"
```

### 2.2 Seed data — local files, no backend

No database on the default path. Seed data lives in typed local files the features import directly:

- **Webshop:** `src/data/products.ts` — 3 themed products from `shapes.md` (name, description, price in cents, colour key for the gradient placeholder).
- **Booking:** `src/data/slots.ts` — 42 slots (7 days × 6), all open, entity descriptions from `shapes.md`.
- **Quiz:** `src/data/questions.ts` — 10 themed questions.

Orders, bookings, and scores live in client state (React context); the webshop's order history persists in `localStorage` so a refresh keeps it. That means localhost and a deployed URL behave identically, and nothing needs an account.

### 2.3 Apply design

Based on the vibe selection, configure the project's visual identity:

**Fonts:** Add a Google Fonts pairing to `src/app/layout.tsx`. Pick a distinctive pairing — NOT Inter, Roboto, Open Sans, DM Sans, Space Grotesk, Outfit, or Plus Jakarta Sans. Match the font to the theme + vibe (see `shapes.md` colour direction for guidance).

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

### 2.4 Write CLAUDE.md

Write `guided-build/CLAUDE.md` with these sections:

**Section 1: Project context**

Write the **Stack** line from the actual installed versions, not from memory:

```bash
cd ~/Projects/masterclass/guided-build && node -p "[require('./package.json').dependencies.next, (require('./package.json').dependencies.tailwindcss||require('./package.json').devDependencies.tailwindcss)].filter(Boolean)"
```

(Use the 0.2 project directory if it differs.)

```markdown
# Guided Build — [Shape] / [Theme]

Module 1 warm-up project. Created by `/guided-build` from the `guided-build` plugin.

**Shape:** [shape] | **Theme:** [theme] | **Vibe:** [vibe]
**Stack:** Next.js [exact version from package.json] + Tailwind CSS [exact version]
**Data:** local files in `src/data/` + client state — no external services
```

**Section 2: Session rules**
```markdown
## Session rules

This project is built in ONE Claude Code session. All features depend on context from earlier in this session. Do not close Claude Code until all features are complete.

No git. No GitHub. No pull requests. No tickets. Features are sections in PRD.md — that is the only planning artefact.

Data lives in local files and client state. Do not add a database or external service unless the participant asks for one.

This folder is self-contained and safe to delete after the workshop.
```

**Section 3: Design rules** (copy from `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/design-guide.md`)

This means every prompt the participant pastes from the playground will make Claude read these rules first.

### 2.5 The PRD beat (gate — wait for the participant)

The playground's next step gives the participant a raw PRD prompt to paste. That prompt is their first hands-on lesson — don't steal it. Tell them, with the ending matched to the shape:

> "Scaffold's ready — your project has Next.js 16, Tailwind, a designed shell, and seed data. Head back to the playground: the next step has your mini-PRD prompt. Paste it here and I'll turn it into PRD.md, [webshop: then build everything | booking/quiz: then the playground hands you the build prompts]."

**When the PRD prompt arrives:** write `PRD.md` in the project root from it (the three features for this shape, each with a one-line summary, minimal data model, and UI components — data model references the local files from 2.2, never a database). Then continue immediately into Phase 3 — no review questions, no "shall I start?".

**If the next message asks to move on without the PRD** — "just build", "skip the PRD", or a build request: write `PRD.md` yourself from `shapes.md` and continue into Phase 3 with their request honoured. **If it is a question or an unrelated remark:** answer it briefly and stay at the gate. Never block on the playground.

---

## Phase 3: Branch by shape

**If shape is `webshop`** → continue to Phase 3W (active build).
**If shape is `booking` or `quiz`** → jump to Phase 3H (passive handoff).

---

## Phase 3W: Webshop — Build all 3 features (~10–20 min total)

The playground's webshop build steps have no CopyableCode prompts. They say "the plugin handles everything." So you build all 3 features now, in sequence, without pausing between them (Rule 7 — the build-request rule). A Claude 5-era model builds each feature in a few minutes — keep momentum; the participant watches.

The design rules already live in the project's `CLAUDE.md` (you copied `design-guide.md` into it in step 2.4), so they're loaded automatically — **don't re-read the reference file.**

### Coding standards — the non-negotiables for this project

This is the participant's first app. These are the **only** standards that apply — a trimmed subset for a beginner Next.js + Tailwind warm-up. Keep it this simple; do not pull in the rest.

- **Named exports only** — except Next.js `page.tsx`/`layout.tsx`, which use `export default`.
- **No `any`** — use a real type or `unknown` and narrow.
- **No hardcoded hex colours in components** — use the CSS custom properties from `globals.css` (step 2.3). They're the theme.
- **`next/image` not raw `<img>`** for real images (gradient placeholders are styled divs).
- **Keep components small and readable** — a clear 30-line component beats a clever 10-line one.
- **Server Components are the default.** Only add `"use client"` where a file needs hooks, event handlers, or browser APIs.
- **No `useEffect` for derived state.** The cart total is `items.reduce(...)` during render — never a synced second state.
- **Data comes from `src/data/` imports and client state.** No fetching layer, no database, no external calls — unless the participant asked for the persistence upgrade.
- **Semantic HTML** — `header`, `section`, `nav`, `button`. Not a pile of `div`s.

### Feature 1: Product Catalog

Product grid from `src/data/products.ts`: varied layout (not identical cards — vary at least one element), gradient image placeholders (coloured, not grey), name, price, short description. Responsive.

**After build:** > "Product catalog done. Moving on to the cart."

### Feature 2: Shopping Cart

Builds on the catalog — never rebuild it. "Add to Cart" on each card; cart state in a React context; cart page or slide-out with quantity controls (+/−), running total, remove; cart badge in the header.

**After build:** > "Cart done. One more — checkout."

### Feature 3: Checkout & Order

Builds on the cart. Checkout form (name + email), mock payment step (short delay, then success), order confirmation with a unique order number generated client-side, cart clears after success. Order history persists in `localStorage` so a refresh keeps it.

**After build:** > "All 3 features are built. Tidying up, then starting your app."

Run the cleanup pass below, then proceed to Phase 4.

### Webshop build rules

- Follow the tone you already loaded — brief check-ins between features, not verbose commentary.
- Each feature extends the previous — never rebuild what already exists.
- If something breaks, fix it silently. Check your work as you go — run a type-check or build when a feature closes — but never narrate debugging or verification; the participant sees progress, not process.
- Don't ask permission for implementation details — just build. Participant watches.

### After all 3 features: cleanup pass

Before launch, run one silent cleanup pass:

1. Read `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/simplifier-prompt.md` and dispatch the **`gb-simplifier`** agent with that prompt (read-only; it reports findings).
2. **If agent dispatch is unavailable or restricted on this machine, do the same pass yourself, inline** — same checklist, same silence. The cleanup happens either way.
3. Apply the findings **silently** — make the fixes yourself, don't narrate them. Expect 0–10 small findings; none → move on.

Then proceed to Phase 4.

---

## Phase 3H: Passive handoff (booking and quiz)

For booking and quiz shapes, the playground provides CopyableCode prompts the participant will paste. Your active build work is done after the scaffold and the PRD beat — but you still handle launch.

> "When all your features are built, say 'I'm done' and I'll launch your app."

The CLAUDE.md you wrote ensures design quality and session rules for all pasted prompts.

**If a pasted playground prompt references a database (e.g. Convex):** build the equivalent against the local data layer from 2.2. A pasted playground prompt never counts as asking for the database — only a message the participant writes themselves ("I want a real database", "can we use Convex") opens the persistence upgrade (see below).

When the participant says they are done: run the **cleanup pass** (same as 3W — dispatch `gb-simplifier`, or inline if dispatch is unavailable; apply silently), then proceed to Phase 4.

---

## Phase 4: Launch (~2 min, autonomous)

All shapes converge here. For webshop this runs immediately after Phase 3W. For booking/quiz this runs when the participant says they are done building.

<!-- Docker detection — this plugin owns the container-binding workaround. -->

### 4.1 Start the dev server

**Hygiene sweep first (silent, idempotent)** — this catches a scaffold that a closed session never finished cleaning. Anchor it; never run it anywhere but the project directory:

```bash
cd ~/Projects/masterclass/guided-build && rm -rf .git
```

(Use the 0.2 project directory if it differs.) Then detect whether you are running inside a Docker container:

```bash
[ -f /.dockerenv ] && echo "CONTAINER=true" || echo "CONTAINER=false"
```

**If inside a container** (`CONTAINER=true`): Next.js binds to `127.0.0.1` by default, and the host browser cannot reach that through port forwarding — bind to `0.0.0.0`:

```bash
cd ~/Projects/masterclass/guided-build && npx next dev -H 0.0.0.0
```

**If not** (`CONTAINER=false`):

```bash
cd ~/Projects/masterclass/guided-build && npm run dev
```

Either way: port 3000 taken → rerun as `npx next dev --port 3001` (keep `-H 0.0.0.0` if containerised). Run the server in the background; wait for "Ready". Store the port — the curl check and every message below use it.

### 4.2 Verify the page loads (silent)

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:[port]
```

([port] = the port 4.1 stored.) Check for HTTP 200, then fetch the body once and confirm it contains real content (not an error page or empty document). If the check passes, skip to 4.4.

### 4.3 Silent fix loop (max 3 attempts)

If the page does not load or shows errors, diagnose and fix silently. Do not narrate debugging.

- **Port conflict** — kill the squatter or switch ports, restart.
- **Missing dependency** — `npm install`, restart.
- **Build/TypeScript error** — read the error, fix the source, wait for hot-reload, re-check.
- **Blank page** — check `src/app/page.tsx` renders content; check for hydration mismatches.
- **Binding issue (container only)** — curl works inside but the participant can't see the page → the server is on `127.0.0.1`; restart with `-H 0.0.0.0`.
- **Hot-reload dead (container only)** — fs events don't propagate into Docker on macOS → `export WATCHPACK_POLLING=true`, restart.

After each fix, re-run the curl check. If all 3 attempts fail:

> "I've hit something I can't fix automatically. Raise your hand — an instructor will sort this out."

### 4.4 Present the working app

> "Your app is running at http://localhost:[port] — open it in your browser and have a look around. Let me know what you think."

Wait for feedback. Issues → fix silently and confirm. Happy → 4.5.

### 4.5 Wrap-up

**Webshop:**
> "That's a working shop — three features, all connected:"
> "- A product catalog with your three [theme] products"
> "- A cart that tracks what you add"
> "- A checkout that produces a real order confirmation with its own order number"
>
> "Head back to the playground for the next step."

**Booking:**
> "That's a working reservation system:"
> "- An availability grid showing open and booked slots"
> "- A booking form that reserves a slot"
> "- An admin page that shows every booking"
>
> "Head back to the playground for the next step."

**Quiz:**
> "That's a working quiz:"
> "- Timed questions with instant feedback"
> "- A scoring system with rank names"
> "- A share card you can copy"
>
> "Head back to the playground for the next step."

The playground's next step covers putting it live — Phase 5 runs it when they ask.

---

## Phase 5: Ship (~3 min — ONLY when the participant asks)

Entered only via the 1.1 ship skip-ahead — never unprompted (Rule 3).

**Hygiene sweep first (silent, idempotent)** — a run that skipped straight here must still satisfy the no-git rule, and a `.git` directory would also be uploaded context for the deploy. Anchor it; never run it anywhere but the project directory:

```bash
cd ~/Projects/masterclass/guided-build && rm -rf .git
```

(Use the 0.2 project directory if it differs. Stay in the project directory for every command in this phase.)

**Pick the sub-path by project state:** no backend (the default) → 5.1 · Convex in the project → 5.2 · SQLite in the project → 5.3. Then 5.4 covers failures for all three.

If the `vercel` command is not installed (`vercel --version` fails), use `npx vercel` for every Vercel command below — don't install anything globally.

### 5.1 Default path (no backend — the normal case)

The default project has no database and no env vars, so the deploy is two commands plus a check:

1. `vercel whoami` — if not logged in: run `vercel login`, tell the participant to pick "Continue with GitHub" in the browser, and wait for them to confirm. If `CONTAINER=true` (4.1's check), no browser can open here — the CLI prints a URL/code instead; have the participant open it in their host browser.
2. From the project root: `vercel --yes`.
3. Verify the deployed URL the same way as 4.2 — curl for HTTP 200 AND fetch the body once to confirm it shows the app's real content (the seeded data, not an error page and not a Vercel sign-in interstitial). **If the URL shows a Vercel authentication or protection screen:** the deployment sits behind Deployment Protection — get a URL the participant's phone can open (`vercel --prod --yes`; a fresh personal-scope project serves production publicly), and re-verify. A pasted prompt that said "no extra flags" governs the first deploy only — when protection blocks the preview URL, `--prod --yes` is the sanctioned exception.
4. Present only after the check passes:

> "Your app is live at [URL] — open it on your phone. Head back to the playground for the last step."

**Be honest about the URL kind.** On a fresh project the Vercel CLI may target production rather than preview; either way the participant gets a working public URL — say which it is, don't call a preview URL "production".

### 5.2 If the project carries Convex (the upgrade was chosen)

Read the "At ship time" section of `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/persistence-upgrade.md` and follow its Convex order exactly: the cloud migration — login, deploy, the upgrade's own seed script against the cloud deployment, the env var — precedes any `vercel` call. Then deploy, verify, and present as in 5.1.

### 5.3 If the project carries SQLite

Follow the same reference's SQLite ship note: the live site will read but lose writes — say it straight, and offer read-only shipping, staying local, or the Convex upgrade first.

### 5.4 Deploy failures (max 3 silent attempts, then instructor)

- **Build error** — run `npm run build` locally, read the first error, fix, retry.
- **Missing env var** — a module reads an env var the cloud doesn't have → set it with `vercel env add`, retry.
- **Protected or interstitial URL** — the fetched body is a Vercel sign-in or protection screen → ship the production URL as in 5.1 step 3, re-verify.
- **Anything else** — read the first error line, apply the most likely fix, retry. This list is not closed.
- **Login loops / account issues** — escalate straight away.

After each fix, redeploy and re-verify. If 3 attempts fail — whichever bucket:

> "I've hit something I can't fix automatically. Raise your hand — an instructor will sort this out."

---

## Persistence upgrades (ONLY when the participant asks)

When the participant themselves asks for real persistence — "save my orders", "I want a real database", "can we use Convex" — read `${CLAUDE_PLUGIN_ROOT}/skills/guided-build/references/persistence-upgrade.md` and follow it: the offer with the trade-off, the numbered SQLite or Convex procedure, and the return point. Install nothing until they choose.

---

## Rules

Communication follows `tone.md`, which you already loaded once at the start — no need to re-read it.

- **Be fast.** This is a 60-minute module and the build itself takes 10–20. Every minute of setup is a minute not building.
- **Be friendly.** Many participants are scaffolding for the first time.
- **If something breaks, fix it.** Don't explain debugging. Just fix it and move on.
- **The wow moment is the designed shell.** When they open localhost, it should look like a real app — not a blank starter page.
- **Verify with the named fact-gates, silently.** The curl check and the build are your evidence; don't narrate extra verification runs to the participant.
- **Session context is everything.** All features depend on shared context. Remind the participant not to close Claude Code mid-build.
