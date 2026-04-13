# Post-Build Cleanup — Dispatch Template

Use this template when dispatching the `gb-simplifier` agent after all features are built.

## Prompt

> Do a quick cleanup pass on the project at `guided-build/`. This is a small beginner project (Next.js + Tailwind, 3–5 features, no database/auth).
>
> Find: unused imports, dead code, inconsistent patterns (mixed component/export styles), duplicate logic across components.
>
> Do NOT flag: style preferences, missing tests, TypeScript strictness, performance, scaffold boilerplate.
>
> For each finding: file path, line, what's wrong, fix.
