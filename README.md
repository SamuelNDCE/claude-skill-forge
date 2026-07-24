# Claude Skill Forge

The [Claude Code Skills](https://docs.claude.com/en/docs/claude-code/skills) I've actually written from scratch, in one place — separate from [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library), which is the big curated collection of skills I use but mostly didn't author myself. This repo is just mine.

Each skill is self-contained: a `SKILL.md` with YAML frontmatter (`name`, `description`) plus any supporting files it needs. Claude Code auto-discovers skills and decides when to invoke one based on its description.

## ⭐ Most Useful Skill

### `braindump`

If you only take one thing from this whole repo, take this one. It's the skill I use constantly, on nearly every task — dump whatever's on your mind, as messy and unstructured as it comes out, and it turns that into a clean, structured prompt, shows it to you for a quick sanity check, then runs it. Fires automatically on a raw ramble, no slash command required, or explicitly via `/braindump`.

```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
mkdir -p /path/to/your/project/.claude/skills
cp -r claude-skill-forge/skills/braindump /path/to/your/project/.claude/skills/
```

It has two sibling tiers for different situations — [`braindump-auto`](skills/braindump-auto/SKILL.md) (skip the confirmation step) and [`superbraindump`](skills/superbraindump/SKILL.md) (for big, tangled, multi-part dumps) — both listed with the rest of the Prompt Input bundle below.

## The skills

19 skills in 7 bundles. Every bundle groups skills that are meant to be installed and used **together** — each one below explains why, gives a one-shot command to install the whole bundle from a fresh clone, and gives every individual skill in it its own ready-to-paste install command too, so nothing needs manual editing either way.

### 📦 Bundle: Prompt Input

**What it's for:** turning a raw, messy prompt into something worth acting on, and catching a specific known failure mode (voice-dictation mishears) along the way. These four are designed as one system, not four separate tools — the three brain-dump tiers share one job (raw dump → clean prompt) at increasing levels of rigor, and the catcher rides shotgun on all three.

**Install the whole bundle:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "prompt input"
```

**Or install one at a time:**

- [`braindump`](skills/braindump/SKILL.md) — **⭐ my most useful skill, see above.** does: turns a messy, rambling prompt into a clean, structured one, shows it to you for a quick sanity check, then runs it.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/braindump /path/to/your/project/.claude/skills/
  ```
- [`braindump-auto`](skills/braindump-auto/SKILL.md) — does: the same fix, but skips the confirmation step and runs immediately.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/braindump-auto /path/to/your/project/.claude/skills/
  ```
- [`superbraindump`](skills/superbraindump/SKILL.md) — does: the heavy-duty tier for big, tangled, multi-part dumps — deeper extraction, a richer prompt template, still shows you the result before running.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/superbraindump /path/to/your/project/.claude/skills/
  ```
- [`dictation-garble-catcher`](skills/dictation-garble-catcher/SKILL.md) — does: catches a specific braindump blind spot — a word that doesn't fit the sentence and is phonetically close to a real project term (a voice-dictation mishear) — and confirms the likely correction instead of running with the literal transcription.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/dictation-garble-catcher /path/to/your/project/.claude/skills/
  ```

### 📦 Bundle: Verification & Trust Discipline

**What it's for:** never taking a self-report at face value. One general rule, then its specific application to the highest-stakes place it applies.

**Install the whole bundle:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "verification and trust discipline"
```

**Or install one at a time:**

- [`verify-dont-trust`](skills/verify-dont-trust/SKILL.md) — does: the general checklist — merge-parent counting instead of trusting "merged cleanly," direct-ID re-fetch instead of trusting a list/edge endpoint, stall detection instead of trusting a "done" claim, post-revert diffing instead of trusting memory.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/verify-dont-trust /path/to/your/project/.claude/skills/
  ```
- [`worktree-task-pack-verification`](skills/worktree-task-pack-verification/SKILL.md) — does: applies that checklist specifically to multi-task work dispatched across isolated git worktrees — one independent verifier per worktree, a full dual-stack build+test gate before any merge.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/worktree-task-pack-verification /path/to/your/project/.claude/skills/
  ```

### 📦 Bundle: Repo & Secret Hygiene

**What it's for:** three stages of the same concern, meant to be layered — prevent on every push, audit everything periodically, and clean up thoroughly if something still gets through.

**Install the whole bundle:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "repo and secret hygiene"
```

**Or install one at a time:**

- [`pre-push-secret-scan`](skills/pre-push-secret-scan/SKILL.md) — does: a fast key/token/webhook scan before every `git push`. The automatic, everyday layer.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/pre-push-secret-scan /path/to/your/project/.claude/skills/
  ```
- [`full-account-security-audit`](skills/full-account-security-audit/SKILL.md) — does: the comprehensive, periodic version — every local repo, every GitHub repo, full git history, local `.env` files, GitHub's own secret-scanning alerts.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/full-account-security-audit /path/to/your/project/.claude/skills/
  ```
- [`public-repo-leak-retraction`](skills/public-repo-leak-retraction/SKILL.md) — does: reactive cleanup when something already got through the first two — scrubs current files, rewrites git history with `git-filter-repo`, force-pushes, verifies zero-trace via GitHub code search.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/public-repo-leak-retraction /path/to/your/project/.claude/skills/
  ```
- [`repo-hygiene`](skills/repo-hygiene/SKILL.md) — does: cleans up proven-junk stray files (a known Git-Bash bug) and defaults every new repo to private, without ever guessing at what counts as junk.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/repo-hygiene /path/to/your/project/.claude/skills/
  ```

### 📦 Bundle: Windows Environment Ops

**What it's for:** the two most common ways a Windows Claude Code session goes sideways — a zombie process nobody stopped, and a shell-syntax trap between Bash and PowerShell.

**Install the whole bundle:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "windows environment ops"
```

**Or install one at a time:**

- [`zombie-process-sweep`](skills/zombie-process-sweep/SKILL.md) — does: finds and safely kills orphaned dev servers/watchers left running, instead of relying on memory to stop what you started.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/zombie-process-sweep /path/to/your/project/.claude/skills/
  ```
- [`windows-shell-tool-selection`](skills/windows-shell-tool-selection/SKILL.md) — does: a cheat-sheet for when to use a Bash tool vs. a PowerShell tool on Windows, and the specific syntax traps between them (chaining, heredocs, encoding).
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/windows-shell-tool-selection /path/to/your/project/.claude/skills/
  ```
- [`windows-process-restart`](skills/windows-process-restart/SKILL.md) — does: safely restarts a supervised Windows background process (kills the child, not the supervisor) with real PID-level verification.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/windows-process-restart /path/to/your/project/.claude/skills/
  ```

### 📦 Bundle: Project Content Safety

**What it's for:** two different angles on "don't ship a design/content change that quietly breaks something else."

**Install the whole bundle:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "project content safety"
```

**Or install one at a time:**

- [`project-design-doc`](skills/project-design-doc/SKILL.md) — does: maintains a persistent per-project design spec (colors, timing conventions, layout patterns) and follows it automatically on future design requests, instead of re-deriving or re-asking every time.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/project-design-doc /path/to/your/project/.claude/skills/
  ```
- [`safe-section-deletion`](skills/safe-section-deletion/SKILL.md) — does: greps the whole codebase for references before deleting any HTML section, anchor, or exported symbol. The markup/id-level sibling of call-graph impact analysis.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/safe-section-deletion /path/to/your/project/.claude/skills/
  ```

### 📦 Bundle: Skill Library Maintenance

**What it's for:** keeping a large skill collection (or any repo that indexes other repos) honest about what it actually contains.

**Install the whole bundle:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "skill library maintenance"
```

**Or install one at a time:**

- [`skill-overlap-audit`](skills/skill-overlap-audit/SKILL.md) — does: finds near-duplicate or overlapping skills in a library and recommends what to merge or retire.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/skill-overlap-audit /path/to/your/project/.claude/skills/
  ```
- [`repo-index-drift-check`](skills/repo-index-drift-check/SKILL.md) — does: audits a hub/index repo's claimed counts and descriptions against what the linked repos actually contain right now.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/repo-index-drift-check /path/to/your/project/.claude/skills/
  ```

### 📦 Bundle: Personal Workflow Ops

**What it's for:** two personal conventions worth automating rather than re-deriving each time.

**Install the whole bundle:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "personal workflow ops"
```

**Or install one at a time:**

- [`personal-dashboard-style`](skills/personal-dashboard-style/SKILL.md) — does: applies a fixed dark-theme HTML report convention (colors, save path, auto-open, always-paired summary) instead of inventing a new visual style per report.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/personal-dashboard-style /path/to/your/project/.claude/skills/
  ```
- [`discord-todo-ops`](skills/discord-todo-ops/SKILL.md) — does: wraps a reaction-based, Discord-backed shared todo list (script-driven add/edit, Discord-native accept/complete) into one skill instead of remembering three separate invocations.
  ```bash
  git clone https://github.com/SamuelNDCE/claude-skill-forge.git
  mkdir -p /path/to/your/project/.claude/skills
  cp -r claude-skill-forge/skills/discord-todo-ops /path/to/your/project/.claude/skills/
  ```

Plus 24 personal NeuralVault (`nv-*`) skills built for my own second-brain workflow — hidden for now, coming soon.

## How to install (general reference)

Every section above already has copy-paste-ready commands for that specific skill or bundle. This is just the general reference if you'd rather build your own command:

**A single skill** — swap `<skill-name>` for anything under `skills/`:
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
mkdir -p /path/to/your/project/.claude/skills
cp -r claude-skill-forge/skills/<skill-name> /path/to/your/project/.claude/skills/
```

**A whole bundle by name** — type the bundle name (spaces, hyphens, and `&`/`and` are all interchangeable, case doesn't matter):
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cd claude-skill-forge
./scripts/install-group.sh "<bundle name>" [destination]
```
Run `./scripts/install-group.sh` with no arguments to print the full list of bundle names.

**The whole repo:**
```bash
git clone https://github.com/SamuelNDCE/claude-skill-forge.git
cp -r claude-skill-forge/skills/* /path/to/your/project/.claude/skills/
```

Restart Claude Code (or start a new session) after adding skills - the skill list loads at session start.

## Part of a larger collection

See [toolkit](https://github.com/SamuelNDCE/toolkit) for the full index of published tools, and [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library) for the full 287-skill collection — five of the nineteen skills here (`braindump`, `braindump-auto`, `superbraindump`, `windows-process-restart`, `repo-hygiene`) are also featured there; the other fourteen are exclusive to this repo for now.

## License

MIT - see [LICENSE](LICENSE).
