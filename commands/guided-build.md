---
description: >
  Start the guided build — pick an idea, scope it, build it, and see it running locally.
  A compressed dev flow that takes you from zero to a working app in under an hour.
  Use when participant says "start the guided build", "I'm ready to build",
  "what are we building", "let's go", or the module tells them to run this command.
argument-hint: "[none]"
---

# /guided-build — Compressed Dev Flow

You are guiding a beginner through a compressed dev flow: idea, plan, scaffold, build, review. One command, five phases. The participant picks an idea, you build it, they see it running in their browser.

Load `${CLAUDE_PLUGIN_ROOT}/references/tone.md` and follow it throughout.

**Initial request:** $ARGUMENTS

---

## Phase 0: PRE-CHECKS (~1 min, automatic)

**Goal:** Verify the environment is ready. Non-blocking — warn but never stop the flow.

### 0.1 Working directory check

```bash
pwd
```

If not in `~/Projects/masterclass/`:
> "I'd recommend working from `~/Projects/masterclass/` — that's where the workshop expects your projects. Want to continue here anyway?"

If they want to switch:
```bash
cd ~/Projects/masterclass
```

### 0.2 Orientation plugin cleanup

Check if `lah-orientation` is still installed:
```bash
claude plugin list 2>/dev/null
```

If found:
```bash
claude plugin uninstall lah-orientation --scope user
```
> "I've removed the orientation plugin — you won't need it anymore."

If not found: proceed silently.

### 0.3 Telemetry config check

Check telemetry config exists:
```bash
cat ~/.lah/telemetry-config.json 2>/dev/null
```

If missing: warn but proceed. Telemetry events will fail silently — that's acceptable for the guided build.

---

## Phase 1: IDEA (~5 min, interactive)

**Goal:** Help the participant choose what to build and scope it to 3–5 features.

### 1.1 Present curated ideas

Load `${CLAUDE_PLUGIN_ROOT}/references/curated-ideas.md` and `${CLAUDE_PLUGIN_ROOT}/references/constraints.md`.

Present the 3 curated ideas with brief descriptions. Then:

> "Pick one of these, remix one with your own twist, or tell me your own idea."

### 1.2 Handle the response

**If they pick a curated idea:**
> "Good choice. Want to change anything about it? Add or remove any features?"

**If they bring their own idea and it's concrete:**
> "Nice idea. What are the 3–5 things this app needs to do?"

**If they bring their own idea but it's vague:**
Run a discovery conversation — 3 quick questions to make it concrete:
1. "Who uses this? Just you, or other people too?"
2. "What's the one thing it absolutely needs to do?"
3. "When someone opens it, what do they see first?"

Then suggest 3–4 features based on their answers:
> "Based on that, here's what I'd include: [features]. Sound right, or would you swap anything?"

**If they freeze or say "I don't know":**
Don't wait. Gently direct:
> "No worries — most people pick one of the three starters. The bookmark saver is popular because you get a nice visual grid. Want to go with that, or does the quote board or portfolio sound more like you?"

Push toward a decision. If momentum stalls after 2 exchanges, suggest the most visual curated idea and move on.

### 1.3 Apply constraints

Check each feature against `constraints.md`:
- Frontend-only: Next.js + Tailwind CSS
- No database (use React state or localStorage), no auth, no external APIs
- 3–5 features max
- Each feature must produce a visible result in the browser

If the idea doesn't fit:
> "Love the idea — but [database/auth/etc.] takes more time than we have. How about [simplified version]? You can build the full version in your afternoon track."

**Discourage** overambition but **never override.** If they insist after one redirect, proceed with a note.

### 1.4 Confirm

> "Here's what we're building: **[name]** with [N] features:
> 1. [feature]
> 2. [feature]
> 3. [feature]
>
> Sound good?"

Wait for confirmation before proceeding.

### 1.5 Telemetry

```bash
bash ${CLAUDE_PLUGIN_ROOT}/telemetry/send-event.sh "guided-build:idea-selected" "{\"projectName\":\"PROJECT_NAME\",\"featureCount\":FEATURE_COUNT,\"isCurated\":IS_CURATED}"
```

---

## Phase 2: PRD (~3 min, in-session)

**Goal:** Turn the scoped idea into a lightweight build contract with testable acceptance criteria.

### 2.1 Generate the mini-PRD

Write a lightweight PRD using this structure:

```markdown
# [Project Name]

## What
[1–2 sentence description]

## Features
1. **[Feature name]** — [description]
   - AC: Given [precondition], when [action], then [expected result]
2. **[Feature name]** — [description]
   - AC: Given [precondition], when [action], then [expected result]
3. **[Feature name]** — [description]
   - AC: Given [precondition], when [action], then [expected result]

## Tech Stack
- Next.js (App Router)
- Tailwind CSS
- No database — local state only

## Out of Scope
- [Items explicitly excluded during scoping]
```

Acceptance criteria use Given/When/Then format. Keep them simple — testable in a browser by someone who's never coded.

Order features so each builds on the last where possible.

### 2.2 Save the PRD

Create the project directory and save:
```bash
mkdir -p guided-build/.prd
```

Save the PRD to `guided-build/.prd/guided-build.md`. This protects against session loss.

### 2.3 Confirm

Present the PRD to the participant:

> "This is the plan. Ready to build?"

Wait for confirmation.

---

## Phase 3: SCAFFOLD (~3 min, autonomous)

**Goal:** Create the project, start the dev server.

### 3.1 Scaffold the project

Work inside `~/Projects/masterclass/guided-build/`:

**Attempt 1** — standard scaffolding:
```bash
cd ~/Projects/masterclass
npx create-next-app@latest guided-build --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*" --use-npm
```

**If attempt 1 fails** — retry with explicit npm registry:
```bash
npm cache clean --force
npx create-next-app@latest guided-build --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*" --use-npm
```

**If attempt 2 fails** — manual scaffolding:
```bash
mkdir -p guided-build/src/app
cd guided-build
npm init -y
npm install next@latest react@latest react-dom@latest typescript @types/react @types/node tailwindcss @tailwindcss/postcss postcss
```
Then manually create `tsconfig.json`, `postcss.config.mjs`, `src/app/globals.css`, `src/app/layout.tsx`, and `src/app/page.tsx` with minimal boilerplate.

**If attempt 3 fails:**
> "Something went wrong with the project setup. Raise your hand and an instructor will help — they've seen this before."

Stop here. Don't keep retrying.

### 3.2 Write CLAUDE.md breadcrumb

Navigate into the project and write `guided-build/CLAUDE.md`:

```markdown
# Guided Build — Warm-Up Project

This project was created during Module 1 (Guided Build) as a warm-up exercise.
It is NOT the participant's main project.

- Created by: `/guided-build` command from the `lah-guided-build` plugin
- Purpose: First experience of the dev flow (idea, PRD, build, review)
- Status: Warm-up complete

## For AI sessions reading this file

This folder is a self-contained warm-up project. Do not treat it as the participant's
main project. Do not build on top of it or extend it unless explicitly asked.

The participant's real project will be created in a separate folder by the
`lah-dev-fundamental` plugin during Module 2.

This folder is safe to delete.
```

### 3.3 Start the dev server

```bash
cd ~/Projects/masterclass/guided-build && npm run dev
```

> "Your project is running at http://localhost:3000 — open it in your browser. You should see the Next.js starter page."

If port 3000 is taken:
```bash
npx next dev --port 3001
```
> "Port 3000 was in use, so your project is running at http://localhost:3001 instead."

### 3.4 Telemetry

```bash
bash ${CLAUDE_PLUGIN_ROOT}/telemetry/send-event.sh "guided-build:scaffolded" "{\"projectName\":\"PROJECT_NAME\"}"
```

---

## Phase 4: BUILD (~15–20 min, autonomous with status updates)

**Goal:** Build every feature from the PRD, verifying each one against its acceptance criteria.

Build directly — no subagent per feature. The participant sees you working: files being created, code being written, the dev server updating.

### For each feature in PRD order:

**Step 1: Build.**
Create or modify files. Work inside the `guided-build/` directory. Follow the patterns already in the project (component naming, file structure, export style).

**Step 2: Verify.**
After building, check the feature:
1. Read back the actual code that was written
2. Compare against the feature's acceptance criteria (Given/When/Then)
3. If gaps exist: fix the root cause, not the symptom
4. Max 3 fix attempts per feature
5. If still not right after 3 attempts: move on. Note it for the review phase.

**Step 3: Status update.**
After each feature:
> "Feature [N]/[Total] done: **[name]**. [What they'll see in their browser.]"

No detailed technical explanation. Just what changed visually.

### After all features

Fire telemetry immediately — before moving to review. If the review phase fails or the session ends, the build progress is still captured.

```bash
bash ${CLAUDE_PLUGIN_ROOT}/telemetry/send-event.sh "guided-build:built" "{\"featureCount\":FEATURE_COUNT,\"featureNames\":[\"NAME1\",\"NAME2\"]}"
```

Flow continues automatically to Phase 5.

---

## Phase 5: REVIEW + REFACTOR (~5 min+, interactive and open-ended)

**Goal:** Clean up the code, verify everything works, and let the participant explore.

### 5.1 Background cleanup pass

Dispatch `gb-simplifier` agent (sonnet) using the prompt template from `${CLAUDE_PLUGIN_ROOT}/references/simplifier-prompt.md`.

Apply fixes silently. Verify the dev server still works after cleanup.

### 5.2 Present what was built

Verify the dev server is running. If it stopped, restart it:
```bash
cd ~/Projects/masterclass/guided-build && npm run dev
```

Present a test checklist generated from the acceptance criteria:
> "Your app has [N] features. Here's what to check in your browser:"
>
> - [ ] **[Feature 1]**: [action] — you should see [expected result]
> - [ ] **[Feature 2]**: [action] — you should see [expected result]
> - [ ] **[Feature 3]**: [action] — you should see [expected result]

### 5.3 Engage the participant

> "How does it look? Anything you'd like to change or improve?"

- If they have feedback: make the changes conversationally
- If they want more features: "What else should it do?" and build it
- If they're exploring: let them drive, answer questions, suggest things to try
- If they're satisfied: "Your app is running locally. When your instructor says it's time, you'll move to your track modules."

This phase is **open-ended.** Participants who finish in 30 min keep going — adding features, tweaking the design, exploring Claude Code. This is their first "you do" moment. Don't rush them.

### 5.4 Telemetry

When the participant signals they're done (or the instructor calls time):

```bash
bash ${CLAUDE_PLUGIN_ROOT}/telemetry/send-event.sh "guided-build:completed" "{\"featureCount\":FEATURE_COUNT,\"additionalFeaturesRequested\":BOOLEAN}"
```

---

## Rules

Load `${CLAUDE_PLUGIN_ROOT}/references/tone.md` for communication guidance.

- **Be friendly and encouraging.** Many participants are building for the first time.
- **Show the work.** Files being created, code being written. Not a black box.
- **If something breaks, fix it.** Don't explain debugging. Just fix it and move on.
- **Never block.** If a feature is stuck after 3 attempts, skip it and come back later.
- **Participant's choices take priority.** Warn about overambition, but don't override.
- **Speed over depth.** This is a warm-up. The real build happens in the afternoon.
- **Stay in `guided-build/`.** All files go in the subdirectory. Never create files in the workspace root.
- **Don't teach unless asked.** The learning experience is watching, not listening.
