# Persistence upgrade — on request only

Read when the participant themselves asks for real persistence — "save my orders", "I want a real database", "can we use Convex". A pasted playground prompt never opens this; only the participant's own words do. Offer both options with the trade-off, let them pick, and install nothing until they choose.

## The offer

- **SQLite (quick)** — `better-sqlite3` + Next.js route handlers. ~5 minutes, real local persistence, zero accounts. Honest limit: file writes do not persist on Vercel's serverless filesystem — a shipped SQLite project serves reads and loses writes.
- **Convex (real backend)** — real-time cloud database, the same stack the main course teaches. Needs a free account, but not now: the sign-in belongs to the ship step.

After either upgrade, keep the beginner standards subset: queries read, mutations write, capped reads, a one-line `// Public: warm-up project, no auth` comment on each function.

## SQLite path

1. `npm install better-sqlite3` in the project.
2. One `src/lib/db.ts` that opens the database file inside the project directory and creates the table(s) mirroring the types in `src/data/`.
3. Route handlers for the writes (orders / bookings); reads may stay on the local data files.
4. Migrate the client code that recorded to client state so it POSTs to the route handlers.
5. Return to whatever feature you were building; if the build is done, re-run the launch checks (Phase 4.2).

## Convex path

1. `npm install convex` in the project.
2. `npx convex dev` in the background. Convex CLI 1.45+ runs an **anonymous local deployment** — no browser, no account, no blocking. If a browser opens instead, the installed CLI is older: treat that sign-in as the ship-time sign-in and let the participant complete it.
3. `convex/schema.ts` — tables mirroring the types in `src/data/` (products/orders, or slots/bookings).
4. Queries and mutations per the beginner subset; wire the frontend with `useQuery` / `useMutation`.
5. A seed mutation (e.g. `convex/seed.ts`) that writes the same records `src/data/` holds — run it once locally. Keep it: the ship step re-runs it against the cloud deployment.
6. Return to whatever feature you were building; if the build is done, re-run the launch checks (Phase 4.2).

## At ship time

- **Convex in the project:** a local deployment (`CONVEX_URL` at `127.0.0.1`) is unreachable from the cloud. Migrate first, in this order: `npx convex login` (browser) → `npx convex deploy` (prints the cloud URL) → run the step-5 seed against the cloud deployment → `vercel env add NEXT_PUBLIC_CONVEX_URL` with the cloud URL (production + preview) → then deploy and verify as in 5.1.
- **SQLite in the project:** say it straight — the live site will read but lose writes. Offer: ship read-only anyway, keep it local, or upgrade to Convex first (then the Convex ship path).
