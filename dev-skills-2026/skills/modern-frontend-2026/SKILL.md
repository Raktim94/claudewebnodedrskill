---
name: modern-frontend-2026
description: Build production-grade frontend apps with 2026-era standards — React 19+, Next.js App Router, Server Components, TypeScript strict mode, Tailwind v4, TanStack Query, testing, and proper project structure. Use this skill whenever the user builds or edits any web app, React/Next.js/Vue project, frontend feature, or asks which frontend stack/framework/library to use — even for "simple" apps, so the code comes out senior-quality.
---

# Modern Frontend (2026) — Senior Engineer Standards

Write frontend code the way a senior engineer at a good product company would: typed end-to-end, server-first, accessible, tested where it matters, and boring where boring wins.

## Stack defaults

- **Framework**: Next.js (App Router) for full-stack/SEO apps; Vite + React for SPAs/dashboards; Astro for content sites. Don't hand-roll webpack configs in 2026.
- **Language**: TypeScript, `strict: true`, no `any` (use `unknown` + narrowing). Types are documentation.
- **Styling**: Tailwind CSS v4 (CSS-first config) + design tokens as CSS variables. shadcn/ui or Radix primitives for accessible components — never build your own dropdown/dialog/combobox from divs.
- **Server state**: TanStack Query (or RSC + Server Actions in Next.js). **Client state**: `useState`/`useReducer` locally; Zustand for genuinely shared state. You almost never need Redux for new apps.
- **Forms**: react-hook-form + Zod resolver. One Zod schema = validation + TypeScript type + server-side re-validation.
- **Data layer**: end-to-end types — tRPC, or server actions, or OpenAPI-generated client. No untyped `fetch` scattered in components.

## React rules that matter

- Server Components by default in Next.js; add `"use client"` only at interactive leaves. Fetch data on the server; pass it down. Keep client bundles lean.
- Derive, don't sync: if a value can be computed from props/state, compute it in render — don't mirror it into `useState`/`useEffect`. Most `useEffect`s in junior code shouldn't exist ("You Might Not Need an Effect").
- `useEffect` only for real external synchronization (subscriptions, DOM APIs, analytics). Always with cleanup and a correct dep array.
- Keys: stable IDs, never array index for dynamic lists.
- Co-locate: component + its hooks + its types in one folder. Extract shared pieces only when actually shared (rule of three).
- Error boundaries around risky trees; Suspense boundaries with real skeletons around async trees.
- React 19: use `use()`, Actions/`useActionState`/`useOptimistic` for mutations with pending/error states instead of hand-rolled loading flags. React Compiler handles most memoization — write plain code first, add `memo/useMemo` only for measured problems.

## Project structure

```
src/
├── app/               # routes (Next) or pages
├── components/ui/     # design-system primitives (button, input, dialog)
├── components/        # shared composed components
├── features/<name>/   # feature folders: components, hooks, api, types together
├── lib/               # utilities, api client, config
└── styles/            # tokens, globals
```

Feature folders over type folders — you delete features, not "all hooks".

## Non-negotiable quality bar

- **Accessibility**: semantic elements (`button` not clickable div), labels on every input, focus management in dialogs, keyboard operability, alt text. Use Radix/shadcn so this is mostly free; verify with axe.
- **All UI states**: loading (skeleton), empty (with CTA), error (with retry), partial, and success. Junior code ships only the happy path.
- **Validation twice**: Zod on client for UX, same schema on server for security.
- **Env & config**: typed env parsing (e.g., Zod on `process.env`) at boot — fail fast on missing config.
- **Images/fonts**: framework image component, explicit dimensions, `next/font` or self-hosted with `font-display: swap`.
- **Linting**: ESLint (typescript-eslint, jsx-a11y, react-hooks) + Prettier/Biome. Fix warnings, don't disable rules without a comment saying why.

## Testing (pragmatic pyramid)

- Vitest + React Testing Library for logic and complex components — test behavior ("user clicks, sees X"), not implementation details.
- Playwright for the 3–10 critical user flows (signup, checkout, core action).
- Zod schemas + TypeScript catch a whole class of bugs — lean on them.
- Don't chase 100% coverage; cover money paths, auth paths, and gnarly logic.

## Performance & delivery

Dynamic-import heavy components; route-level code splitting is automatic — don't undo it with barrel-file mega-imports. Check bundle on CI. Meet Core Web Vitals (see performance-optimization skill). Deploy previews per PR; CI runs typecheck + lint + tests + build.

## Output format

When building features: brief plan → file tree of what's added/changed → complete typed code (no `// ...rest of implementation`) → note on states covered (loading/empty/error) → how to run/test it.
