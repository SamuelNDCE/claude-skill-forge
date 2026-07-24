---
name: "Repo Index Drift Check"
description: "Audit a hub/index repo's claims (skill counts, descriptions, links) against what the linked repos actually contain right now, and flag any drift. Use periodically on any README that summarizes other repos' contents, especially after any linked repo changes, don't trust the index's own stated numbers as ground truth."
---

# Repo Index Drift Check

An index repo's claims about the things it links to (a count, a one-line description) are a snapshot from whenever they were last written, and they go stale the moment the linked repo changes without a corresponding update here.

## Procedure

**1. Parse the index repo's claims** about each linked repo: stated counts, descriptions, feature callouts.

**2. Independently verify the real current state of each linked repo, never trust the index's number as the source of truth:**
```bash
gh api repos/{owner}/{repo}/git/trees/main?recursive=1 --jq '.tree[] | select(.path | test("SKILL.md$"))' | wc -l
gh repo view {owner}/{repo} --json description
```

**3. Diff claimed vs. actual.** Any mismatch is drift: report it plainly (what the index says vs. what's actually there).

**4. Fix mismatches at the source, not just the index**: a stale count is a symptom; check whether the underlying repo's own README/description also drifted from its own real content (it's common for both the repo's own claims AND the index pointing to it to be wrong simultaneously).

**5. Re-run after any change to a linked repo**, not just once. This is a check to repeat, not a one-time fix.

## Real precedent

In one afternoon, the actual number went 285 (what the index and both repos claimed) → 289 (the real, verified count) → 287 (after trimming two genuinely useless skills). Three different true numbers in a few hours. A repo's own stated count is a claim, not a fact, and it drifts faster than it looks like it would.
