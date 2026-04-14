# Design Guide — Guided Build

This file gets injected into the project's CLAUDE.md during scaffolding. It ensures every feature built from the playground's prompts follows these rules.

---

## The AI Slop Test

> If you showed this to someone and said "AI made this," would they believe you immediately? If yes, rethink your choices.

---

## What NOT to Do

1. **Generic fonts** — Never default to Inter, Roboto, Open Sans, or system fonts. Use the fonts already configured in this project's globals.css.
2. **Purple-to-blue gradients** — The most overused AI colour scheme. Use the project's colour palette instead.
3. **Nested cards** — Never put cards inside cards. Use spacing and typography for hierarchy.
4. **Identical card grids** — Don't repeat the same card layout endlessly. Vary sizes or mix with non-card content.
5. **Side-stripe borders** — No thick coloured left/right borders on cards or alerts.
6. **Gradient text** — No `background-clip: text` with gradients. Use solid colours for text.
7. **Same spacing everywhere** — Vary spacing intentionally. Tight grouping for related elements, generous separation between sections.
8. **Centre everything** — Left-aligned text with asymmetric layouts feels more designed than centring every element.
9. **Pure black and white** — Don't use `#000` or `#fff`. Tint them toward the brand hue. Even a subtle tint creates cohesion.
10. **Dark-mode-with-neon by default** — Don't pick dark mode because it "looks cool." Use the theme already set up in this project.

## What TO Do

- **Use the project's existing fonts and colours.** They were chosen for this theme — don't override them.
- **Fewer font sizes, more contrast.** 5 sizes max. At least 1.25 ratio between steps.
- **Dominant + accent colour.** One main colour, sharp accents. Accents work because they're rare.
- **Tint your neutrals** toward the brand hue for cohesion.
- **Vary spacing for hierarchy.** 8-12px for related elements, 48-96px between sections.
- **Create depth.** Layered backgrounds, subtle shadows, thoughtful texture. Not flat solid colours.
- **One animation moment per feature.** A staggered reveal or smooth transition. Not scattered micro-interactions.
- **Cap body text at 65-75 characters.** Use `max-width` with `ch` units.
- **Respect `prefers-reduced-motion`.** Always.
