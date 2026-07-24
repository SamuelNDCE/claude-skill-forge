---
name: "Project Design Doc"
description: "Maintain a persistent per-project design spec (colors, typography, motion/timing conventions, layout grid, voice) and follow it automatically on every future design-related request for that project instead of re-deriving or re-asking each time. Use whenever a design request references a specific existing project, and whenever a design decision is made that should become a standing convention."
---

# Project Design Doc

Design conventions for a project already exist. They're just scattered across past commits and your memory of them, re-derived or re-asked about every time instead of looked up. This writes them down once.

## Where it lives

One file per project, e.g. `docs/design-spec.md` at the project root. If it doesn't exist yet and a design request comes in for that project, create it from what's actually already in the codebase (colors in CSS variables/theme files, existing animation timing values, layout patterns already in use) rather than inventing new conventions. The spec should describe what the project already does, not impose something new unasked.

## What it captures

- **Color palette**: exact values (hex/CSS vars), with names matching how they're referenced in code, and light/dark variants if both exist.
- **Typography**: font families, scale, weight conventions.
- **Motion/timing conventions**: the specific pattern already established (e.g. a staggered entrance sequence with a base gap and per-element deltas, a shared CSS keyframe/class used across pages, standard easing curves). Record actual delay values from working examples, not approximations.
- **Layout conventions**: grid system, spacing scale, component patterns already in use (e.g. "cards default to a compact single row, expand-on-click reveals detail").
- **Voice/tone**: if the project has established copy conventions.

## Procedure

1. **On first design request for a project with no spec file**: read the existing implementation (CSS, theme config, recently-touched components) to extract real, already-in-use conventions. Don't guess. Cite the actual file/line the convention came from.
2. **Write the spec file**, organized by category above.
3. **On every subsequent design request for that project**: read the spec first, follow it by default. Don't silently re-derive from scratch or introduce an inconsistent new pattern.
4. **When the user changes a convention** ("make it feel less like a corkboard," "shorten the delay between these two"): apply the change, then update the spec file to reflect the new standing convention. Don't let the spec go stale while the codebase moves on.
5. **When a request is ambiguous against the spec** (asks for something the spec doesn't cover, or seems to conflict with it), surface the conflict rather than silently picking a side.

## What this is not

Not a rigid design system enforcement tool. It's a memory aid so established conventions get reused instead of re-litigated. If the user explicitly wants to break from the documented pattern for a specific request, that's a normal one-off override, not a spec violation to push back on.
