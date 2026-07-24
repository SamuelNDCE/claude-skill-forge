---
name: "Skill Overlap Audit"
description: "Scan a skills library for near-duplicate or overlapping skills (same purpose, redundant description, an orphaned duplicate of a canonical one) and recommend which to merge or retire. Different from a quality audit — this is specifically about redundancy. Use periodically on any skill collection that's grown past a few dozen skills, or right after adding a batch of new skills."
---

# Skill Overlap Audit

A library that grows by accretion accumulates near-duplicates: two skills covering the same ground, written at different times, never reconciled. This finds them.

## Procedure

**1. Extract name + one-line description for every skill** in the library (from its README listing or its `SKILL.md` frontmatter directly).

**2. Group candidates by textual similarity**, not just exact match — two skills can have slightly different wording but describe the identical thing (e.g. both saying "development conventions for the same project").

**3. For each flagged pair, determine which is canonical, don't guess:**
```bash
grep -rl "<skill-name>" . --include="*.md"   # who else references it back
```
The one other skills actually point to as their source/foundation is almost always the one to keep. An orphaned duplicate that nothing else references is the safer one to retire.

**4. Diff the actual file content** of flagged pairs before recommending removal — a near-identical description doesn't always mean near-identical content. If the bodies genuinely diverge in substance (not just wording), they may be intentionally distinct variants, not true duplicates — flag those for a human judgment call rather than auto-recommending removal.

**5. Check for explicit self-marked deprecation** too — a skill whose own description says "deprecated, superseded by X" is an unambiguous removal candidate regardless of textual similarity to anything else.

**6. Report before removing.** This is an audit — surface the findings (candidate pairs, which one is canonical, confidence level) and let the removal happen as a separate, explicit action.

## Real precedent

This exact method found two genuine removals in this project's own library: a skill explicitly self-labeled "deprecated, superseded by" a newer version, and a 95%+-identical duplicate of another skill that 16 other skills referenced back to — the duplicate itself was referenced by nothing.
