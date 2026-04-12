# Post-Build Cleanup — Dispatch Template

Use this template when dispatching the `gb-simplifier` agent after all features are built.

## Prompt

> Do a single cleanup pass on the project at `guided-build/`. This is a small beginner project (Next.js + Tailwind, 3–5 features, no database/auth). Look for:
>
> 1. **Unnecessary complexity** — nested conditionals, redundant logic, wrapper functions
> 2. **Dead code** — unused imports, unreachable code, commented-out blocks
> 3. **Inconsistencies** — mixed component styles, mixed export patterns, inconsistent naming
> 4. **Redundancy** — duplicate logic across components, repeated inline styles
>
> Do NOT flag: style preferences (semicolons, quotes), missing tests, TypeScript strictness, performance, or scaffold boilerplate.
>
> For each finding, report: file path, line, what's wrong, suggested fix, severity (cleanup or inconsistency).
>
> Keep it concise — expect 0–10 findings for a project this size.
