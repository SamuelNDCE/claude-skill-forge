---
name: repo-hygiene
description: "Clean up proven-junk stray files that accumulate in a repo (Git-Bash heredoc-splitting artifacts, empty scratch dirs) once real work is safely committed, flag (never silently delete) regenerable build caches like target/ or node_modules/, and default every newly created repo to private. Use when the user mentions disk bloat, 'clean up old files', 'useless files', a messy `git status`, or when about to create a new GitHub/git repo."
---

# Repo Hygiene: Stray-File Cleanup & Private-by-Default

## Overview

Two standing rules:
1. **New repos are private by default.** Never create a public repo unless the user explicitly says so.
2. **Clean up proven junk, never guess at junk.** Only auto-delete files that are *provably* garbage (exact known bug signature). Everything else (regenerable caches, anything with real content) gets flagged with a size/count for the user to decide, never deleted automatically.

This skill exists because of a recurring problem in a large multi-project agent repo: 315 untracked garbage-named files had piled up from a known Git-Bash bug (see Rule 2), and separately a Rust `target/` build cache alone was eating 4.76GB. See `wiki/Mistakes & Fixes/2026-07-11-repo-hygiene-stray-file-sweep.md`.

## Rule 1: New repos default to private

When creating a repo via `gh repo create`, `mcp__github__create_repository`, or any equivalent:
- Always pass `--private` / `private: true` unless the user has explicitly said "public" or "open source" for that specific repo.
- Flipping an existing repo from private → public is a **visibility/access-control change**: per the global safety rules this needs explicit user confirmation in chat every time. It is never inferred from a prior approval.

## Rule 2: Stray-file cleanup, safe detection only

**The bug**: passing multi-line strings to `node -e` (or even running a normal `.js`/`.py`/`.rs` file) through Git Bash on Windows can silently mangle a fragment of the script into a literal file on disk, e.g. a script containing `!ids.has(e.source)` creates an actual empty file named `!ids.has(e.source)`. The real script still runs correctly, so this goes unnoticed unless `git status` is checked. See `wiki/Mistakes & Fixes/2026-07-04-node-e-multiline-creates-stray-files-gitbash.md`.

**Detection algorithm, both conditions must hold before deleting anything:**
1. File is exactly **0 bytes** (or, for a directory, recursively empty).
2. Name is unambiguously a code fragment: starts with punctuation like `!`, `(`, `{`, `,`, `$`, `'`, backtick, or contains unbalanced brackets/parens, or is a truncated expression (`.slice(0`, `.md\`)`, etc.), not a plausible intentional filename.

```powershell
$root = "<repo root>"
$strays = git -C $root status --porcelain=v1 -uall | Where-Object {$_ -match '^\?\? '} | ForEach-Object { $_.Substring(3) }
$zeroFiles=@(); $skip=@()
foreach ($f in $strays) {
  $p = Join-Path $root $f
  if ((Test-Path -LiteralPath $p -PathType Leaf) -and ((Get-Item -LiteralPath $p -Force).Length -eq 0)) {
    $zeroFiles += $f
  } else {
    $skip += $f   # anything with content, or that doesn't match the bug signature, never touch
  }
}
```

**Never auto-delete:**
- Anything with nonzero size, even if untracked/uncommitted (could be a real one-off script, a generated deliverable, vendored library, or work in progress).
- Anything with a plausible real filename, even if 0 bytes (could be an intentionally-created placeholder).

**Always report** what was skipped (count + list) so the user can decide: commit it, or delete it themselves.

**When to run this:** at the end of a task/session that used `node -e`, multiline heredocs, or any script execution through this Bash tool, and before committing. Also whenever the user mentions "clean up", "useless files", or disk bloat.

## Rule 3: Regenerable build caches, flag only, ask before deleting

Folders like `target/` (Rust), `node_modules/`, `.venv/`, `dist/`, `build/`, `__pycache__/` are the actual source of large disk usage (a single `target/` dir hit 4.76GB in this workspace), but they are **never** auto-deleted:
- Regenerating them costs real time/bandwidth (`cargo build`, `npm install`, `pip install`), so silent deletion is disruptive even when "safe."
- Only flag a candidate when the corresponding source directory is confirmed already committed (`git status` clean for the source files it was built from). Deleting a cache next to *uncommitted* source risks looking like data loss even though the cache itself holds no unique data.
- Report folder + size, and ask before running `Remove-Item -Recurse -Force` or equivalent. This is a destructive, hard-to-reverse-in-the-moment action (re-fetching everything takes time) even though no unique data is lost.

```powershell
Get-ChildItem -Path <root> -Directory -Recurse -Depth 2 -Force -ErrorAction SilentlyContinue |
  Where-Object { $_.Name -in @('target','node_modules','.venv','dist','build','__pycache__') } |
  ForEach-Object {
    $size = (Get-ChildItem $_.FullName -Recurse -Force -ErrorAction SilentlyContinue | Measure-Object Length -Sum).Sum
    [PSCustomObject]@{Path=$_.FullName; SizeGB=[math]::Round($size/1GB,2)}
  } | Sort-Object SizeGB -Descending
```

## Why not a broader heuristic sweep

Per `wiki/Mistakes & Fixes/2026-07-11-dead-parent-heuristic-kills-live-processes.md`: never build an automated cleanup/killer tool around a system-wide heuristic unless the signal has zero false positives. A "delete anything old" or "delete anything that looks unused" scan is exactly that trap. This skill only ever acts on the exact, proven-safe zero-byte + code-fragment-name signature, and only ever *flags* everything else.

Related: `git-workflow`
