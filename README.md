# Claude Skill Forge

The [Claude Code Skills](https://docs.claude.com/en/docs/claude-code/skills) I've actually written from scratch, in one place — separate from [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library), which is the big curated collection of skills I use but mostly didn't author myself. This repo is just mine.

Each skill is self-contained: a `SKILL.md` with YAML frontmatter (`name`, `description`) plus any supporting files it needs. Claude Code auto-discovers skills and decides when to invoke one based on its description.

## The skills

15 skills, grouped by what they're for. Skills in the same group are meant to be used together or build directly on each other — that relationship is called out in each group.

### Prompt Input (the original three, plus one new companion)

- [`braindump`](skills/braindump/SKILL.md) - **the prompt fixer.** Dump whatever's on your mind, as messy and unstructured as it comes out, and it turns that into a clean, structured prompt, shows it to you for a quick sanity check, then runs it. Fires automatically on a raw ramble, no slash command required, or explicitly via `/braindump`.
- [`braindump-auto`](skills/braindump-auto/SKILL.md) - auto-accepts the corrected prompt and runs it immediately, skipping the confirmation step.
- [`superbraindump`](skills/superbraindump/SKILL.md) - the heavy-duty tier for big, tangled, multi-part dumps: deeper extraction, a richer prompt template, still requires review before running.
- [`dictation-garble-catcher`](skills/dictation-garble-catcher/SKILL.md) - **new.** Companion to the braindump family: flags a word that doesn't fit context and is phonetically close to a known project proper noun (a classic voice-dictation mishear), and confirms before acting on it instead of silently running with a mistranscription.

### Verification & Trust Discipline

The core rule, then its specific application to the biggest recurring workflow it protects.

- [`verify-dont-trust`](skills/verify-dont-trust/SKILL.md) - **new.** Never treat a self-report, a just-created API response, or an immediate list/edge-endpoint read as proof something worked. The checklist for verifying independently instead: merge-parent counting, direct-ID re-fetch, stall detection, post-revert diffing.
- [`worktree-task-pack-verification`](skills/worktree-task-pack-verification/SKILL.md) - **new.** Applies `verify-dont-trust` specifically to multi-task work dispatched across isolated git worktrees: one independent verifier per worktree, a full dual-stack gate before merge, explicit silent-stall detection.

### Repo & Secret Hygiene

Three stages of the same concern — prevent, periodically audit, and remediate if something still gets through.

- [`pre-push-secret-scan`](skills/pre-push-secret-scan/SKILL.md) - **new.** Fast key/token/webhook pattern scan before every `git push`. The everyday, automatic layer.
- [`full-account-security-audit`](skills/full-account-security-audit/SKILL.md) - **new.** The comprehensive, periodic version: every local repo, every GitHub repo, full history, local `.env` files, GitHub's own secret-scanning alerts.
- [`public-repo-leak-retraction`](skills/public-repo-leak-retraction/SKILL.md) - **new.** Reactive cleanup when something already got through the first two: scrub current files, rewrite git history with `git-filter-repo`, force-push, verify zero-trace via GitHub code search.
- [`repo-hygiene`](skills/repo-hygiene/SKILL.md) - cleans up proven-junk stray files (a known Git-Bash bug that mangles script fragments into literal files on disk) and defaults every new repo to private, without ever guessing at what counts as junk.

### Windows Environment Ops

- [`zombie-process-sweep`](skills/zombie-process-sweep/SKILL.md) - **new.** Finds and safely kills orphaned dev servers/watchers left running, instead of relying on memory to stop what you started.
- [`windows-shell-tool-selection`](skills/windows-shell-tool-selection/SKILL.md) - **new.** Cheat-sheet for when to use a POSIX/Bash tool vs. a PowerShell tool on Windows, and the specific syntax traps between them (chaining, heredocs, encoding).
- [`windows-process-restart`](skills/windows-process-restart/SKILL.md) - safely restarts a supervised Windows background process (kill the child, not the supervisor) with real PID-level verification, written after `TaskStop`'s success message turned out to be untrustworthy on Windows and silently left dead processes behind more than once.

### Project Content Safety

- [`project-design-doc`](skills/project-design-doc/SKILL.md) - **new.** Maintains a persistent per-project design spec (colors, timing conventions, layout patterns) and follows it automatically on every future design request for that project, instead of re-deriving or re-asking each time.
- [`safe-section-deletion`](skills/safe-section-deletion/SKILL.md) - **new.** Before deleting any HTML section, anchor, or exported symbol, grep the whole codebase for references first. The markup/id-level sibling of call-graph impact analysis.

Plus 24 personal NeuralVault (`nv-*`) skills built for my own second-brain workflow — hidden for now, coming soon.

## How to use a skill

Each folder is self-contained. Claude Code auto-discovers skills placed in `.claude/skills/` and decides when to invoke one based on its description.

**Clone a single skill** (example: `braindump`):
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
mkdir -p /path/to/your/project/.claude/skills
cp -r claude-skill-forge/skills/braindump /path/to/your/project/.claude/skills/
```

**Grab the whole set:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cp -r claude-skill-forge/skills/* /path/to/your/project/.claude/skills/
```

Restart Claude Code (or start a new session) after adding skills - the skill list loads at session start.

## Part of a larger collection

See [toolkit](https://github.com/SamuelNDCE/toolkit) for the full index of published tools, and [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library) for the full 287-skill collection — five of the fifteen skills here (`braindump`, `braindump-auto`, `superbraindump`, `windows-process-restart`, `repo-hygiene`) are also featured there; the other ten are exclusive to this repo for now.

## License

MIT - see [LICENSE](LICENSE).
