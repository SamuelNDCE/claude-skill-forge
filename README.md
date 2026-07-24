# Claude Workbench

What I actually use to build with Claude Code: skills first, then everything else. The skills below split into two kinds: [Claude Code Skills](https://docs.claude.com/en/docs/claude-code/skills) I've written entirely from scratch (separate from [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library), the big curated collection I use but mostly didn't author myself), and the wider set of skills, MCP servers, CLI tools, and conventions that make up my day-to-day setup.

Each skill is self-contained: a `SKILL.md` with YAML frontmatter (`name`, `description`) plus any supporting files it needs. Claude Code auto-discovers skills and decides when to invoke one based on its description.

## Most Useful Skill

### `braindump`

If you only take one thing from this whole repo, take this one. It's the skill I use constantly, on nearly every task. Dump whatever's on your mind, as messy and unstructured as it comes out, and it turns that into a clean, structured prompt, shows it to you for a quick sanity check, then runs it. Fires automatically on a raw ramble, no slash command required, or explicitly via `/braindump`.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
mkdir -p /path/to/your/project/.claude/skills
cp -r claude-workbench/skills/braindump /path/to/your/project/.claude/skills/
```

It has two sibling tiers for different situations: [`braindump-auto`](skills/braindump-auto/SKILL.md) (skip the confirmation step) and [`superbraindump`](skills/superbraindump/SKILL.md) (for big, tangled, multi-part dumps). Both listed with the rest of the Prompt Input bundle below.

## The skills

20 skills in 7 bundles. Every bundle groups skills that are meant to be installed and used together, and each one below explains why, with a single command to install the whole bundle from a fresh clone. Want just one skill out of a bundle? Clone the repo and copy that one skill's folder yourself, same as any other skill here.

### Bundle: Prompt Input

What it's for: turning a raw, messy prompt into something worth acting on, and catching a specific known failure mode (voice-dictation mishears) along the way. These four are designed as one system, not four separate tools. The three brain-dump tiers share one job (raw dump to clean prompt) at increasing levels of rigor, and the catcher rides shotgun on all three.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "prompt input"
```

- [`braindump`](skills/braindump/SKILL.md): my most useful skill, see above. Turns a messy, rambling prompt into a clean, structured one, shows it to you for a quick sanity check, then runs it.
- [`braindump-auto`](skills/braindump-auto/SKILL.md): the same fix, but skips the confirmation step and runs immediately.
- [`superbraindump`](skills/superbraindump/SKILL.md): the heavy-duty tier for big, tangled, multi-part dumps. Deeper extraction, a richer prompt template, still shows you the result before running.
- [`dictation-garble-catcher`](skills/dictation-garble-catcher/SKILL.md): catches a specific braindump blind spot. A word that doesn't fit the sentence and is phonetically close to a real project term (a voice-dictation mishear), confirming the likely correction instead of running with the literal transcription.

### Bundle: Multi-Session Task Discipline

What it's for: a pipeline for large work. Plan and split it, isolate each piece, then never take a self-report at face value when it comes back.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "multi-session task discipline"
```

- [`large-task-session-split`](skills/large-task-session-split/SKILL.md): for a large task, draft a plan split into independent pieces and hand each to its own separate session (not a sub-agent) to execute in parallel, instead of one long session accumulating context rot.
- [`worktree-task-pack-verification`](skills/worktree-task-pack-verification/SKILL.md): isolates each piece in its own git worktree with one independent verifier and a full dual-stack build and test gate before any merge.
- [`verify-dont-trust`](skills/verify-dont-trust/SKILL.md): the general checklist underneath both. Merge-parent counting instead of trusting "merged cleanly," direct-ID re-fetch instead of trusting a list/edge endpoint, stall detection instead of trusting a "done" claim, post-revert diffing instead of trusting memory.

### Bundle: Repo & Secret Hygiene

What it's for: three stages of the same concern, meant to be layered. Prevent on every push, audit everything periodically, and clean up thoroughly if something still gets through.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "repo and secret hygiene"
```

- [`pre-push-secret-scan`](skills/pre-push-secret-scan/SKILL.md): a fast key/token/webhook scan before every `git push`. The automatic, everyday layer.
- [`full-account-security-audit`](skills/full-account-security-audit/SKILL.md): the comprehensive, periodic version. Every local repo, every GitHub repo, full git history, local `.env` files, GitHub's own secret-scanning alerts.
- [`public-repo-leak-retraction`](skills/public-repo-leak-retraction/SKILL.md): reactive cleanup when something already got through the first two. Scrubs current files, rewrites git history with `git-filter-repo`, force-pushes, verifies zero-trace via GitHub code search.
- [`repo-hygiene`](skills/repo-hygiene/SKILL.md): cleans up proven-junk stray files (a known Git-Bash bug) and defaults every new repo to private, without ever guessing at what counts as junk.

### Bundle: Windows Environment Ops

What it's for: the two most common ways a Windows Claude Code session goes sideways. A zombie process nobody stopped, and a shell-syntax trap between Bash and PowerShell.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "windows environment ops"
```

- [`zombie-process-sweep`](skills/zombie-process-sweep/SKILL.md): finds and safely kills orphaned dev servers/watchers left running, instead of relying on memory to stop what you started.
- [`windows-shell-tool-selection`](skills/windows-shell-tool-selection/SKILL.md): a cheat sheet for when to use a Bash tool vs. a PowerShell tool on Windows, and the specific syntax traps between them (chaining, heredocs, encoding).
- [`windows-process-restart`](skills/windows-process-restart/SKILL.md): safely restarts a supervised Windows background process (kills the child, not the supervisor) with real PID-level verification.

### Bundle: Project Content Safety

What it's for: two different angles on not shipping a design or content change that quietly breaks something else.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "project content safety"
```

- [`project-design-doc`](skills/project-design-doc/SKILL.md): maintains a persistent per-project design spec (colors, timing conventions, layout patterns) and follows it automatically on future design requests, instead of re-deriving or re-asking every time.
- [`safe-section-deletion`](skills/safe-section-deletion/SKILL.md): greps the whole codebase for references before deleting any HTML section, anchor, or exported symbol. The markup/id-level sibling of call-graph impact analysis.

### Bundle: Skill Library Maintenance

What it's for: keeping a large skill collection, or any repo that indexes other repos, honest about what it actually contains.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "skill library maintenance"
```

- [`skill-overlap-audit`](skills/skill-overlap-audit/SKILL.md): finds near-duplicate or overlapping skills in a library and recommends what to merge or retire.
- [`repo-index-drift-check`](skills/repo-index-drift-check/SKILL.md): audits a hub/index repo's claimed counts and descriptions against what the linked repos actually contain right now.

### Bundle: Personal Workflow Ops

What it's for: two personal conventions worth automating rather than re-deriving each time.

```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "personal workflow ops"
```

- [`personal-dashboard-style`](skills/personal-dashboard-style/SKILL.md): applies a fixed dark-theme HTML report convention (colors, save path, auto-open, always-paired summary) instead of inventing a new visual style per report.
- [`discord-todo-ops`](skills/discord-todo-ops/SKILL.md): wraps a reaction-based, Discord-backed shared todo list (script-driven add/edit, Discord-native accept/complete) into one skill instead of remembering three separate invocations.

Plus 24 personal NeuralVault (`nv-*`) skills built for my own second-brain workflow. Hidden for now, coming soon.

## Skills I Use Most

The skills above are the ones I wrote from scratch. This is a tier list of the rest: not written by me, but installed and reached for constantly. Each one links back to where it actually lives (mostly [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library), a few to their original external source).

### Tier 1: Top 5

The five I'd keep if I had to drop everything else.

1. [`karpathy-guidelines`](https://x.com/karpathy/status/2015883857489522876): behavioral guardrails against over-engineering and unrequested scope, built on Andrej Karpathy's original LLM-coding pitfalls
2. [`gitnexus-impact-analysis`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/code-intelligence/gitnexus/gitnexus-impact-analysis/SKILL.md): blast-radius check before editing any symbol
3. [`para-second-brain`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/knowledge-vault-ops/para-second-brain/SKILL.md): PARA-method second-brain organization
4. [`git-workflow`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/misc-utilities/git-workflow/SKILL.md): branching strategies, commit conventions, merge vs. rebase
5. [`verification-loop`](https://github.com/affaan-m/everything-claude-code): verify a change actually works before calling it done

### Tier 2: The Next 25

Everything else in regular rotation, by category.

**Code intelligence:**
- [`gitnexus-exploring`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/code-intelligence/gitnexus/gitnexus-exploring/SKILL.md): trace execution flows and architecture in unfamiliar code
- [`gitnexus-debugging`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/code-intelligence/gitnexus/gitnexus-debugging/SKILL.md): trace a bug to its root cause via the call graph
- [`gitnexus-refactoring`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/code-intelligence/gitnexus/gitnexus-refactoring/SKILL.md): safe rename/extract/split with call-graph awareness
- [`codebase-onboarding`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/code-intelligence/codebase-onboarding/SKILL.md): structured onboarding guide with architecture map
- [`repo-scan`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/code-intelligence/repo-scan/SKILL.md): cross-stack source audit, classifies every file

**Workflow and meta:**
- [`tdd-workflow`](https://github.com/affaan-m/everything-claude-code): disciplined test-first workflow instead of ad hoc test-after
- [`prompt-optimizer`](https://github.com/affaan-m/everything-claude-code): tightens and evaluates prompts for reuse
- [`security-scan`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/security-review/security-scan/SKILL.md): scan a `.claude/` config for vulnerabilities and misconfigurations
- [`terminal-ops`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/devops-infra/terminal-ops/SKILL.md): evidence-first repo execution workflow
- [`deep-research`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/knowledge-vault-ops/deep-research/SKILL.md): multi-source research with cited reports
- [`qmd`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/knowledge-vault-ops/qmd/SKILL.md): search local markdown knowledge bases and wikis

**Dev patterns:**
- [`database-migrations`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/devops-infra/database-migrations/SKILL.md): schema changes, rollbacks, zero-downtime deployments
- [`deployment-patterns`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/language-frameworks/deployment-patterns/SKILL.md): CI/CD, containerization, health checks, rollback strategies
- [`error-handling`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/misc-utilities/error-handling/SKILL.md): typed errors, retries, and circuit breakers
- [`api-design`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/misc-utilities/api-design/SKILL.md): REST resource naming, status codes, pagination, versioning
- [`mcp-server-patterns`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/agent-ai-engineering/mcp-server-patterns/SKILL.md): build MCP servers with the Node/TypeScript SDK
- [`rust-patterns`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/language-frameworks/rust-patterns/SKILL.md): idiomatic Rust ownership, error handling, concurrency
- [`python-patterns`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/language-frameworks/python-patterns/SKILL.md): Pythonic idioms, PEP 8, type hints

**Frontend and design:**
- [`frontend-patterns`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/frontend-design-ui/frontend-patterns/SKILL.md): React/Next.js state management and performance
- [`accessibility`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/frontend-design-ui/accessibility/SKILL.md): WCAG 2.2 AA inclusive design for web and native
- [`motion-foundations`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/frontend-design-ui/motion-foundations/SKILL.md): motion tokens, spring presets, SSR-safe animation
- [`motion-patterns`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/frontend-design-ui/motion-patterns/SKILL.md): production-ready animation patterns for buttons and modals
- [`make-interfaces-feel-better`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/frontend-design-ui/make-interfaces-feel-better/SKILL.md): concrete polish details, spacing, motion, hit areas
- [`click-path-audit`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/frontend-design-ui/click-path-audit/SKILL.md): trace every button's state changes to find silent cancel-out bugs
- [`browser-qa`](https://github.com/SamuelNDCE/claude-super-skill-library/blob/main/skills/frontend-design-ui/browser-qa/SKILL.md): automate visual testing and UI verification after deploys

### Superpowers plugin

14 skills bundled with the [Superpowers](https://github.com/obra/superpowers) plugin, all in regular use, each labeled `(superpowers)` since they come from that plugin rather than the library above:

- [`using-superpowers`](https://github.com/obra/superpowers/blob/main/skills/using-superpowers/SKILL.md) (superpowers): how skills get discovered and invoked in the first place
- [`brainstorming`](https://github.com/obra/superpowers/blob/main/skills/brainstorming/SKILL.md) (superpowers): explores intent and requirements before any creative or building work
- [`writing-plans`](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md) (superpowers): turns a spec into a multi-step plan before touching code
- [`executing-plans`](https://github.com/obra/superpowers/blob/main/skills/executing-plans/SKILL.md) (superpowers): runs a written plan in a separate session with review checkpoints
- [`dispatching-parallel-agents`](https://github.com/obra/superpowers/blob/main/skills/dispatching-parallel-agents/SKILL.md) (superpowers): dispatches independent tasks with no shared state to run in parallel
- [`subagent-driven-development`](https://github.com/obra/superpowers/blob/main/skills/subagent-driven-development/SKILL.md) (superpowers): executes independent plan tasks within the current session
- [`using-git-worktrees`](https://github.com/obra/superpowers/blob/main/skills/using-git-worktrees/SKILL.md) (superpowers): isolated workspaces for feature work before executing a plan
- [`systematic-debugging`](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md) (superpowers): a structured approach before proposing any fix
- [`test-driven-development`](https://github.com/obra/superpowers/blob/main/skills/test-driven-development/SKILL.md) (superpowers): strict test-first discipline
- [`verification-before-completion`](https://github.com/obra/superpowers/blob/main/skills/verification-before-completion/SKILL.md) (superpowers): confirms work actually meets requirements before calling it done
- [`requesting-code-review`](https://github.com/obra/superpowers/blob/main/skills/requesting-code-review/SKILL.md) (superpowers): verifies work meets requirements before merging
- [`receiving-code-review`](https://github.com/obra/superpowers/blob/main/skills/receiving-code-review/SKILL.md) (superpowers): processes incoming review feedback
- [`finishing-a-development-branch`](https://github.com/obra/superpowers/blob/main/skills/finishing-a-development-branch/SKILL.md) (superpowers): structured options for merging, opening a PR, or cleaning up
- [`writing-skills`](https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md) (superpowers): the meta-skill for building new skills, the one behind most of this repo

### NeuralVault (private)

24 personal skills for my own second-brain workflow. Named here, not published:
- `nv-web-search`: web search that saves results straight into the vault, tagged and routed automatically
- `nv-video-ingest`: point it at a video link, it saves a structured note
- `nv-cross-linker`: links related notes together as new ones get added
- `nv-daily-brief` and `nv-weekly-review`: digests of what changed and what needs attention
- `nv-vault-audit`: finds orphaned or contradictory notes

## Other Stuff

Not skills, but the rest of the setup that makes the skills above actually work.

**MCP servers:**
- **GitNexus**: call-graph-aware code intelligence, the backbone of the code-intelligence skills above
- **GitHub**: repo, PR, and issue operations without shelling out to `gh` for everything
- **Supabase**: database inspection and migrations for Postgres-backed projects

**CLI tools:**
- `gh` (GitHub CLI): repo creation, PR management, API calls
- `git-filter-repo`: history rewrites, see [`public-repo-leak-retraction`](skills/public-repo-leak-retraction/SKILL.md) above
- `qmd`: local markdown knowledge-base search, also listed as a skill above
- agent-browser: browser automation for verifying UI changes actually work

**Standing conventions:**
- Dark navy/teal/purple HTML report style for anything data-heavy, see [`personal-dashboard-style`](skills/personal-dashboard-style/SKILL.md) above
- A Discord-based shared team todo list and activity log, see [`discord-todo-ops`](skills/discord-todo-ops/SKILL.md) above

## How I Run Claude

Workflow patterns, not skills or tools, kept high-level on purpose.

**Large tasks**

A big project starts in one session: that session plans the work and splits it into independent pieces. Each piece then goes to its own fresh session running in parallel, each in its own git worktree so nothing collides on the same files. This is cheaper in tokens than nesting a dozen sub-agents inside one conversation, and it sidesteps the quality drop that comes from a single session dragging on too long.

If several of those sessions are going to hit something expensive at the same time, a full build, for example, that one step runs through a shared lock so they queue instead of fighting over the same resources.

Before fanning out into parallel sessions or sub-agents at all, it's worth asking whether the task is actually big enough to justify the coordination cost. A one-line fix doesn't need a swarm behind it.

**Trust, but check**

A session doesn't get to grade its own work. Something separate, with no stake in the outcome, checks the result instead of taking a self-report at face value. The same applies to facts: a count or a claim about what exists gets checked directly rather than trusted from a cache or a tool's last answer.

**Prompts**

Messy asks get rewritten first, goal, context, constraints, done-when, before anything actually runs. This matters most for anything that touches multiple files or spans more than one session.

**Model choice**

Different models for different jobs instead of one model for everything. Reading documents and straightforward web scraping go to Haiku. Anything that needs real judgment, harder research or more complex scraping, goes to Sonnet. Coding work runs on Sonnet or Fable depending on what the task needs. This is a habit I follow, not something enforced by a config file.

## How to install

**A single skill.** Clone the repo, then copy the one folder you want:
```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
mkdir -p /path/to/your/project/.claude/skills
cp -r claude-workbench/skills/<skill-name> /path/to/your/project/.claude/skills/
```

**A whole bundle by name.** Type the bundle name (spaces, hyphens, and `&`/`and` are all interchangeable, case doesn't matter):
```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cd claude-workbench
./scripts/install-group.sh "<bundle name>" [destination]
```
Run `./scripts/install-group.sh` with no arguments to print the full list of bundle names.

**The whole repo:**
```bash
git clone https://github.com/SamuelNDCE/claude-workbench.git
cp -r claude-workbench/skills/* /path/to/your/project/.claude/skills/
```

Restart Claude Code (or start a new session) after adding skills. The skill list loads at session start.

## Part of a larger collection

See [toolkit](https://github.com/SamuelNDCE/toolkit) for the full index of published tools, and [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library) for the full 287-skill collection. Five of the twenty skills I wrote from scratch (`braindump`, `braindump-auto`, `superbraindump`, `windows-process-restart`, `repo-hygiene`) are also featured there. The other fifteen, plus everything in the "Skills I Use Most," "Other Stuff," and "How I Run Claude" sections, are specific to this repo.

## License

MIT. See [LICENSE](LICENSE).
