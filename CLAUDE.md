# CLAUDE.md — LifeOS project working agreement

You are building **LifeOS**, a life-sim/economy game played inside a fake desktop OS.
**`docs/LIFEOS_GDD.md` is the single source of truth.** If a request conflicts with the GDD, say so and propose a GDD edit first. Do not invent systems, numbers, app names, or features not in the GDD.

## Before any task

1. Read the GDD sections named in the current phase (GDD §17). Phase work only — no opportunistic refactors or “while I’m here” features. New ideas → `docs/BACKLOG.md`.
1. Check the phase’s **Definition of Done**. That is the goal, not “code exists.”

## Hard rules

- **Engine purity:** `src/engine/` is pure TypeScript. No React, Zustand, DOM, or timers inside it. Functions are `(state, input) → newState` or pure calculators.
- **Money is integer cents** everywhere in state. Format only at the UI edge via `utils/format.ts`.
- **No `Math.random()`** outside `engine/rng.ts`. All randomness flows from seeded streams (`market`, `events`, `grey`, `misc`) so saves are deterministic.
- **TypeScript strict, no `any`**, no `@ts-ignore` without a comment explaining why.
- **Every schema change adds a migration** in `engine/save/migrate.ts` + a test. Never break old saves.
- **Tests with the work, not after:** every engine function gets a Vitest test in the same PR/commit. The **golden sim** (seed 42, 1 game-year, asserts in GDD §15.7) must be green before a phase is declared done.
- **All numerals render in JetBrains Mono with `tabular-nums`** (use the `<Money/>` / `<Stat/>` primitives).
- **Design tokens only** (GDD §14.2). No ad-hoc hex values, shadows, or radii in app code.
- **Zero raster image assets.** Icons = lucide-react; everything else = CSS/SVG/canvas.
- **Content rule (binding):** all grey-market content is fictional and abstracted — invented UI and flavor text only. Never include real hacking techniques, real tool names, or operational detail. Tone per GDD §10.
- **Attribution:** the About panel must include the TradingView Lightweight-Charts notice + link (Apache-2.0 requirement).
- **Performance:** tick pipeline ≤4ms; components subscribe via narrow selectors; charts consume bars (ring buffers), never raw ticks.

## Workflow

- Small commits, imperative messages (`engine: add regime markov step`). Tag `phase-N-done` only when DoD + tests pass.
- When a task is ambiguous, choose the option that best serves the **design pillars (GDD §1.2)** and note the decision in the commit body.
- Prefer boring, readable code over clever code. This project’s cleverness budget is spent in the simulation math, not the plumbing.
- When touching balance numbers, edit `src/content/*.ts` only, and re-run the golden sim to confirm the §6.3 income bands still hold.

## Definition-of-done checklist (every phase)

- [ ] Feature matches GDD spec section-by-section
- [ ] Engine logic unit-tested; golden sim green; determinism test green
- [ ] No console errors/warnings in a 10-minute manual session
- [ ] Save → reload → identical state; migration added if schema changed
- [ ] 60fps with the phase’s apps open; reduced-motion + dark mode verified
- [ ] Committed + tagged

## Project commands

`pnpm dev` · `pnpm test` · `pnpm test:golden` · `pnpm build` · `pnpm lint`