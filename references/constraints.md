# Guided Build Constraints

These rules define what a guided build project can and cannot be. Load this during the idea phase and apply to every project — curated or custom.

## Must

- **Frontend-only:** Next.js (App Router) + Tailwind CSS
- **3–5 features**, each producing a visible result in the browser
- **Buildable in ~20 minutes** by the main model
- **No database** — use React state (`useState`) or `localStorage` for persistence
- **No authentication** — no login, no user accounts, no sessions
- **No external API calls** — no fetch to third-party services, no API keys
- **No backend** — no API routes, no server actions, no database connections
- **Visual output** — every feature must change what the participant sees in the browser

## Red flags in custom ideas

| Participant says | What it means | How to redirect |
|-----------------|---------------|-----------------|
| "Users can log in" | Needs auth | "Let's skip login for now — we'll use the app as a single user. You can add accounts in your afternoon track." |
| "Save to a database" | Needs backend | "Let's use your browser's local storage instead — it works the same way for our purposes." |
| "Fetch data from [API]" | External dependency | "Let's use sample data built into the app — that way it works instantly without waiting for an API." |
| "I want 8 features" | Too ambitious | "Those all sound great. Which 3–5 are the most important? We'll start there." |
| "Real-time updates" | Needs WebSockets/backend | "Let's focus on the core features first. Real-time is a great stretch goal for the afternoon." |
| "Mobile app" | Wrong platform | "We're building for the browser today — but a responsive web app looks great on mobile too." |

## How to apply

- Check each proposed feature against the "Must" list
- If a feature violates a constraint, suggest the nearest feasible alternative
- Frame positively: "You can build the full version in your afternoon track"
- If the participant insists after being warned: **proceed**. Don't override. Note the risk.
- Never refuse an idea. Warn, suggest, proceed.
