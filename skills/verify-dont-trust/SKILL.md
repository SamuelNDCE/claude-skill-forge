---
name: "Verify, Don't Trust"
description: "Never treat a self-report, a just-created API response, or an immediate list/edge-endpoint read as proof something worked. Verify independently via a different path before marking anything done. Use before closing out a dispatched subagent task, any API write (Shopify/Meta/GitHub/etc.), or any 'merged cleanly' claim."
---

# Verify, Don't Trust

The single most repeated lesson across this project's history: a report of success is not evidence of success. Every category below has produced a real, logged failure that this checklist would have caught before it shipped.

## The four checks

**1. "Merged cleanly" claims.** A commit message saying a branch was merged is not proof.
```bash
git show -1 --format=%P <commit>   # 2+ parent hashes = a real merge; 1 = it wasn't
git merge-base --is-ancestor <branch> <target> && echo "actually merged"
```
Caught: a subagent's commit message claimed "merged v3-stage-1 first, no conflicts". It was a single-parent commit that never touched the target branch, and merging it as reported would have silently regressed an unrelated feature via an enum-slot collision.

**2. API writes (Shopify, Meta, GitHub, any REST/GraphQL mutation).** Never trust the create/update response alone, and never trust the first list/edge read after it. List endpoints are commonly eventually-consistent and lag behind writes by seconds to minutes.
- Re-fetch the created/updated object by its own direct ID before reporting success.
- If a "just-created" object doesn't show up in a list view, don't conclude it failed. Check by ID first.

**3. Dispatched task "done" claims.** Before accepting that a background task or subagent actually finished (or actually stalled):
```bash
git status --porcelain          # real, uncommitted work sitting there = it finished, just didn't commit
git log -1 --format=%cd <path>  # how stale is the last change, really
```
Cross-check against whether the process is still alive. Real completed-but-uncommitted work should be verified and finished, not treated as a failure and re-run from scratch.

**4. Any temporary test-state seed.** If you hardcode a value to force an otherwise-unreachable UI/code path into view for verification (a locked state, an error state, a specific data shape), always `git diff` after reverting the seed to confirm only the real feature diff remains. Never trust memory that "I definitely changed it back."

## Rule of thumb

If the only evidence a thing worked is the tool call that just tried to make it work, that's not verification. It's an assertion. Read the state back through a second, independent path before calling it done.

## See also

[`worktree-task-pack-verification`](../worktree-task-pack-verification/SKILL.md): applies check #1 and #3 specifically to multi-task work dispatched across isolated git worktrees.
