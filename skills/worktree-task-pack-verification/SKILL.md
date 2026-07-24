---
name: "Worktree Task-Pack Verification"
description: "Verification discipline for multi-task work packs dispatched across isolated git worktrees. One independent verification agent per worktree, a full dual-stack gate before any merge, and explicit stall detection. Use whenever you dispatch a numbered task pack (T1, T2, T3...) to run in parallel worktrees."
---

# Worktree Task-Pack Verification

Extends [`verify-dont-trust`](../verify-dont-trust/SKILL.md) to the specific shape of dispatching many tasks across isolated worktrees and merging the results back.

## Why this exists

Real, logged failure modes from exactly this pattern:
- A task can be genuinely finished, correct, well-tested code, and just sit uncommitted because the session stopped mid-task with no visible error.
- A task can report "merged cleanly" while the commit is provably single-parent and never touched the target branch.
- Two independent tasks can each compile fine in isolation and only break when merged together (e.g. two branches independently claiming the same enum slot).

## Procedure

**1. Dispatch one independent verification agent per worktree**, not the same agent that did the work grading its own output. Each verification agent reads the real commits/diffs directly and runs its own gate. It does not trust the executor's self-report.

**2. Check for silent stalls before assuming a task "isn't done":**
```bash
git status --porcelain              # uncommitted-but-real work sitting there
git log -1 --format="%cd"           # staleness of the last commit
```
Cross-reference against whether any process tied to that worktree path is still alive. A stalled-but-complete task should be finished and committed centrally, not re-run from scratch.

**3. Never trust a "merged cleanly" claim**: apply `verify-dont-trust` check #1 (`git show -1 --format=%P`, `git merge-base --is-ancestor`) to every task pack member before treating it as integrated.

**4. Run the full gate on the merged tree, not just each worktree individually.** Per-worktree tests passing does not guarantee the merged result is clean. Run the complete build+test suite for every affected stack (e.g. `cargo test --workspace` + `npm run check && npm run build && npm run test`) after merging, not before.

**5. Resolve real merge conflicts by regenerating, not hand-merging, where possible** (e.g. a lockfile conflict from two branches adding different dependencies: regenerate from the manifest rather than manually reconciling the lockfile diff).

**6. When multiple independently-dispatched verification agents surface the exact same gap or the exact same false claim**, treat that as a strong signal. Investigate immediately rather than assuming coincidence.

## Done-when

Every task pack member has: an independent verifier's confirmation (not the executor's own report), a real 2-parent merge commit (or a clean fast-forward), and a full green gate on the *post-merge* tree.
