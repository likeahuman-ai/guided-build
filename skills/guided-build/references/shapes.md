# Project Shapes

Three shapes. Each has themed options. The participant picks shape + theme in the playground before running `/guided-build`.

The plugin accepts freeform theme names too — if someone types a theme not listed here, generate appropriate seed data and colour direction for it.

---

## Webshop

**Hook:** A product catalog with a cart and a checkout flow.

**Build mode:** Plugin-driven. The plugin scaffolds AND builds all 3 features (the playground has no build prompts for webshop).

**Features:**
1. Product Catalog — grid of 3 seeded products with image placeholder, price, short description
2. Shopping Cart — add to cart, update quantity, running total, cart badge in header
3. Checkout & Order — simple form (name + email), mock payment step, order confirmation with unique order number

**Stack note:** Next.js 16 + Tailwind. Products in `src/data/products.ts`; cart and orders in client state; order history persists in `localStorage`. Convex upgrade (on request): products + orders move to Convex tables.

**Themes (playground pick-shop-theme step):**

| Theme | Seed products | Colour direction |
|-------|--------------|-----------------|
| StackShop | Product Photography Pro ($29), Full Stack Builder ($49), Blog Post Machine ($19) | Bright, techy — electric blue + warm white |
| BrainBoost Market | Cognitive Training Pack ($49), Sleep Optimization Guide ($29), Brain Health Assessment ($19) | Professional — deep teal + clean cream |

**Alternative themes (shown in playground track picker preview):**

| Theme | Seed products | Colour direction |
|-------|--------------|-----------------|
| Coffee | Ethiopian Yirgacheffe ($18), Colombian Supremo ($22), Sumatra Mandheling ($20) | Warm browns + cream |
| Plants | Monstera Deliciosa ($35), Fiddle Leaf Fig ($45), Snake Plant ($25) | Deep greens + terracotta |
| Records | Kind of Blue — Miles Davis ($28), Rumours — Fleetwood Mac ($24), OK Computer — Radiohead ($22) | Charcoal + amber |
| Prints | Mountain Dawn — A3 ($40), Urban Fog — A3 ($35), Coastal Light — A3 ($45) | Soft greys + warm white |

---

## Booking

**Hook:** A time-slot reservation system — customer + admin flows.

**Build mode:** Prompt-driven. The plugin scaffolds only. The playground provides CopyableCode prompts for each feature.

**Features:**
1. Availability Grid — 7-day x 6-slot grid showing open/booked status from the project's data layer
2. Booking Form — customer clicks open slot, fills name + email + party size, the slot flips to booked in the data layer, confirmation screen
3. Admin Queue — /admin page listing all bookings with Confirm/Cancel actions, live updates from the data layer

**Stack note:** Next.js 16 + Tailwind. Slots in `src/data/slots.ts`; bookings and statuses in client state. Convex upgrade (on request): slots + bookings move to Convex — the shape that gains most from it (real-time admin queue).

**Themes:**

| Theme | Entities | Colour direction |
|-------|---------|-----------------|
| Restaurant Tables | Table 1 (2 guests), Table 2 (4 guests), Table 3 (6 guests) | Warm gold + dark wood |
| Yoga Classes | Sunrise Flow (7am), Power Hour (noon), Yin Recovery (6pm) | Sage + sand |
| Barber Chairs | Chair 1 — Mark, Chair 2 — Sam, Chair 3 — Kim | Charcoal + copper |
| Co-working Desks | Desk A — window, Desk B — quiet corner, Desk C — standing | Clean blue + white |

---

## Quiz

**Hook:** A timed multi-choice quiz with points and a share card.

**Build mode:** Prompt-driven. The plugin scaffolds only. The playground provides CopyableCode prompts for each feature.

**Features:**
1. Question Screen — one question at a time, 4 answer tiles (A/B/C/D), progress dots, hardcoded 10-question deck
2. Timer & Feedback — 10-second countdown, green flash correct, red flash wrong, auto-advance
3. Score & Share — running score, end screen with rank name (Quiz Master / Solid / Warming Up / Better Luck), share button copies result to clipboard

**Stack note:** Next.js 16 + Tailwind. Client-side state only. Convex upgrade (on request, post-workshop): a leaderboard.

**Themes:**

| Theme | Sample questions | Colour direction |
|-------|----------------|-----------------|
| Movie Trivia | Who directed Inception? / What year did Jaws release? / Name the Pixar film with the chef rat | Dark navy + amber |
| Product Onboarding | What does our pricing tier X include? / Where is the status page? / Who owns support? | Professional blue + white |
| Pub Night | What's the capital of Portugal? / How many bones in the adult human body? / What year did the Berlin Wall fall? | Dark green + warm gold |
| Company History | When did we launch? / Who wrote the first commit? / What was our original tagline? | Brand colours (participant chooses) |
