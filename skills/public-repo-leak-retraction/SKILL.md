---
name: "Public Repo Leak Retraction"
description: "Something business-identifying, client-identifying, or secret already got pushed to a public repo. Scrub current files, rewrite git history to remove it everywhere, force-push, and verify zero trace via GitHub code search. Use when asked to get something out of a public repo 'with no trace', this is reactive, after-the-fact cleanup, not pre-publish prep (see opensource-pipeline for that)."
---

# Public Repo Leak Retraction

Reactive cleanup for a repo that's already public and already contains something it shouldn't. Different from a project's initial "sanitize before first release" pass: this assumes the repo has real history that needs surgery, not just a working-tree fix.

## Procedure

**1. Find every occurrence, in content AND commit messages, not just the obvious file.**
```bash
git log --all -p | grep -i "<term>"           # every historical variant, in diffs
git log --all --format='%H %s%n%b' | grep -i "<term>"   # commit message mentions too
```
Don't assume the wording is identical everywhere. Check all historical variants before writing replacement rules.

**2. Fix the working tree first, with a normal commit.** Remove/genericize the current files, commit, push normally. This gets the live HEAD clean immediately, before touching history (which takes an extra step and a force-push).

**3. Rewrite history with `git-filter-repo`** (not `filter-branch`, deprecated and much slower):
```bash
git filter-repo --path <leaked-file-or-dir> --invert-paths \
  --replace-text <content-rules-file> \
  --replace-message <message-rules-file> \
  --force
```
- `--replace-text`: literal substitutions applied to file content across every historical blob.
- `--replace-message`: same syntax, applied to commit/tag messages, easy to forget, and old commit messages often repeat the exact term you just scrubbed from content.
- Use broad single-word substring rules (e.g. `BusinessName==>the-store`) rather than trying to match every phrasing variant exactly. It's safer and catches variants you didn't enumerate.
- Check for legitimate unrelated uses of the term being scrubbed before a blanket replace (e.g. a generic platform name used elsewhere for a real, unrelated technical reason). Don't collateral-damage content that has nothing to do with the leak.

**4. Filter-repo removes the `origin` remote as a safety measure.** Re-add it and force-push:
```bash
git remote add origin <url>
git push --force origin main
```

**5. Verify, don't assume, run it twice.** After the first pass, re-grep the full rewritten history (`git log --all -p | grep -i "<term>"`). It's common to catch content on pass one but miss commit-message-only mentions; add a second `--replace-message` rule and re-run rather than assuming clean.

**6. Confirm zero-trace on the live remote**, not just locally:
```bash
gh api "search/code?q=<term>+repo:<owner>/<repo>" --jq '.total_count'
```
GitHub's code search index can lag slightly, but combined with your own full-history grep this is strong confirmation.

## Constraints

- This force-pushes and rewrites commit SHAs. Get explicit confirmation before running it, especially on a repo other people may have cloned. It is not silently reversible for anyone who already has a copy.
- Apply to every mirror/fork of the repo you control, not just the primary one. Inconsistent copies defeat the point.
