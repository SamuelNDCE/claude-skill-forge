# Claude Skill Forge

The [Claude Code Skills](https://docs.claude.com/en/docs/claude-code/skills) I've actually written from scratch, in one place — separate from [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library), which is the big curated collection of skills I use but mostly didn't author myself. This repo is just mine.

Each skill is self-contained: a `SKILL.md` with YAML frontmatter (`name`, `description`) plus any supporting files it needs. Claude Code auto-discovers skills and decides when to invoke one based on its description.

## The skills

19 skills in 7 bundles. Every bundle groups skills that are meant to be installed and used **together** — each one below explains why, and gives the exact command to install that whole bundle in one shot. Skills not in a bundle of their own can still be installed individually.

### 📦 Bundle: Prompt Input

**What it's for:** turning a raw, messy prompt into something worth acting on, and catching a specific known failure mode (voice-dictation mishears) along the way. These four are designed as one system, not four separate tools — the three brain-dump tiers share one job (raw dump → clean prompt) at increasing levels of rigor, and the catcher rides shotgun on all three.
```bash
./scripts/install-group.sh "prompt input"
```
- [`braindump`](skills/braindump/SKILL.md) — **does:** turns a messy, rambling prompt into a clean, structured one, shows it to you for a quick sanity check, then runs it. Fires automatically on a raw ramble, no slash command needed, or explicitly via `/braindump`.
- [`braindump-auto`](skills/braindump-auto/SKILL.md) — **does:** the same fix, but skips the confirmation step and runs immediately. Use when you trust the correction and don't want to wait on it.
- [`superbraindump`](skills/superbraindump/SKILL.md) — **does:** the heavy-duty tier for big, tangled, multi-part dumps — deeper extraction, a richer prompt template, still shows you the result before running.
- [`dictation-garble-catcher`](skills/dictation-garble-catcher/SKILL.md) — **does:** catches a specific braindump blind spot — a word that doesn't fit the sentence and is phonetically close to a real project term (a voice-dictation mishear) — and confirms the likely correction instead of running with the literal transcription.

### 📦 Bundle: Verification & Trust Discipline

**What it's for:** never taking a self-report at face value. One general rule, then its specific application to the highest-stakes place it applies.
```bash
./scripts/install-group.sh "verification and trust discipline"
```
- [`verify-dont-trust`](skills/verify-dont-trust/SKILL.md) — **does:** the general checklist — merge-parent counting instead of trusting "merged cleanly," direct-ID re-fetch instead of trusting a list/edge endpoint, stall detection instead of trusting a "done" claim, post-revert diffing instead of trusting memory.
- [`worktree-task-pack-verification`](skills/worktree-task-pack-verification/SKILL.md) — **does:** applies that checklist specifically to multi-task work dispatched across isolated git worktrees — one independent verifier per worktree, a full dual-stack build+test gate before any merge.

### 📦 Bundle: Repo & Secret Hygiene

**What it's for:** three stages of the same concern, meant to be layered — prevent on every push, audit everything periodically, and clean up thoroughly if something still gets through.
```bash
./scripts/install-group.sh "repo and secret hygiene"
```
- [`pre-push-secret-scan`](skills/pre-push-secret-scan/SKILL.md) — **does:** a fast key/token/webhook scan before every `git push`. The automatic, everyday layer.
- [`full-account-security-audit`](skills/full-account-security-audit/SKILL.md) — **does:** the comprehensive, periodic version — every local repo, every GitHub repo, full git history, local `.env` files, GitHub's own secret-scanning alerts.
- [`public-repo-leak-retraction`](skills/public-repo-leak-retraction/SKILL.md) — **does:** reactive cleanup when something already got through the first two — scrubs current files, rewrites git history with `git-filter-repo`, force-pushes, verifies zero-trace via GitHub code search.
- [`repo-hygiene`](skills/repo-hygiene/SKILL.md) — **does:** cleans up proven-junk stray files (a known Git-Bash bug) and defaults every new repo to private, without ever guessing at what counts as junk.

### 📦 Bundle: Windows Environment Ops

**What it's for:** the two most common ways a Windows Claude Code session goes sideways — a zombie process nobody stopped, and a shell-syntax trap between Bash and PowerShell.
```bash
./scripts/install-group.sh "windows environment ops"
```
- [`zombie-process-sweep`](skills/zombie-process-sweep/SKILL.md) — **does:** finds and safely kills orphaned dev servers/watchers left running, instead of relying on memory to stop what you started.
- [`windows-shell-tool-selection`](skills/windows-shell-tool-selection/SKILL.md) — **does:** a cheat-sheet for when to use a Bash tool vs. a PowerShell tool on Windows, and the specific syntax traps between them (chaining, heredocs, encoding).
- [`windows-process-restart`](skills/windows-process-restart/SKILL.md) — **does:** safely restarts a supervised Windows background process (kills the child, not the supervisor) with real PID-level verification.

### 📦 Bundle: Project Content Safety

**What it's for:** two different angles on "don't ship a design/content change that quietly breaks something else."
```bash
./scripts/install-group.sh "project content safety"
```
- [`project-design-doc`](skills/project-design-doc/SKILL.md) — **does:** maintains a persistent per-project design spec (colors, timing conventions, layout patterns) and follows it automatically on future design requests, instead of re-deriving or re-asking every time.
- [`safe-section-deletion`](skills/safe-section-deletion/SKILL.md) — **does:** greps the whole codebase for references before deleting any HTML section, anchor, or exported symbol. The markup/id-level sibling of call-graph impact analysis.

### 📦 Bundle: Skill Library Maintenance

**What it's for:** keeping a large skill collection (or any repo that indexes other repos) honest about what it actually contains.
```bash
./scripts/install-group.sh "skill library maintenance"
```
- [`skill-overlap-audit`](skills/skill-overlap-audit/SKILL.md) — **does:** finds near-duplicate or overlapping skills in a library and recommends what to merge or retire.
- [`repo-index-drift-check`](skills/repo-index-drift-check/SKILL.md) — **does:** audits a hub/index repo's claimed counts and descriptions against what the linked repos actually contain right now.

### 📦 Bundle: Personal Workflow Ops

**What it's for:** two personal conventions worth automating rather than re-deriving each time.
```bash
./scripts/install-group.sh "personal workflow ops"
```
- [`personal-dashboard-style`](skills/personal-dashboard-style/SKILL.md) — **does:** applies a fixed dark-theme HTML report convention (colors, save path, auto-open, always-paired summary) instead of inventing a new visual style per report.
- [`discord-todo-ops`](skills/discord-todo-ops/SKILL.md) — **does:** wraps a reaction-based, Discord-backed shared todo list (script-driven add/edit, Discord-native accept/complete) into one skill instead of remembering three separate invocations.

Plus 24 personal NeuralVault (`nv-*`) skills built for my own second-brain workflow — hidden for now, coming soon.

## How to use a skill

Each folder is self-contained. Claude Code auto-discovers skills placed in `.claude/skills/` and decides when to invoke one based on its description. Three ways to install, from narrowest to broadest:

**1. A single skill** (example: `braindump`):
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
mkdir -p /path/to/your/project/.claude/skills
cp -r claude-skill-forge/skills/braindump /path/to/your/project/.claude/skills/
```

**2. A whole bundle by name** — type the bundle name (spaces, hyphens, and `&`/`and` are all interchangeable, case doesn't matter) and every skill in it installs at once:
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "personal workflow ops"
# or: ./scripts/install-group.sh skill-library-maintenance /path/to/your/project/.claude/skills
```
Run `./scripts/install-group.sh` with no arguments to print the full list of bundle names.

**3. The whole repo:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cp -r claude-skill-forge/skills/* /path/to/your/project/.claude/skills/
```

Restart Claude Code (or start a new session) after adding skills - the skill list loads at session start.

## Part of a larger collection

See [toolkit](https://github.com/SamuelNDCE/toolkit) for the full index of published tools, and [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library) for the full 287-skill collection — five of the nineteen skills here (`braindump`, `braindump-auto`, `superbraindump`, `windows-process-restart`, `repo-hygiene`) are also featured there; the other fourteen are exclusive to this repo for now.

## License

MIT - see [LICENSE](LICENSE).
