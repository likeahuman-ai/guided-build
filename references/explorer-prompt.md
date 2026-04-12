# Scaffold Exploration — Dispatch Template

Use this template when dispatching the `gb-explorer` agent after scaffolding a new Next.js project.

## Prompt

> Map the project at `guided-build/`. This is a freshly scaffolded Next.js + Tailwind project. I need to know:
>
> 1. **File structure** — what directories exist, what's in each
> 2. **App Router** — what pages and layouts exist in `app/`
> 3. **Components** — what components exist, how they're structured (naming, exports)
> 4. **Styling** — Tailwind config, globals.css, any theme setup
> 5. **Feature placement** — for each of these planned features, suggest which files to create or modify:
>    {{feature_list}}
>
> Report file paths for everything you read. Note any patterns I should follow when adding features (naming conventions, export style, component structure).
