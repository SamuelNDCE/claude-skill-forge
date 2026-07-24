# Claude Skill Forge

The [Claude Code Skills](https://docs.claude.com/en/docs/claude-code/skills) I've actually written from scratch, in one place — separate from [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library), which is the big curated collection of skills I use but mostly didn't author myself. This repo is just mine.

Each skill is self-contained: a `SKILL.md` with YAML frontmatter (`name`, `description`) plus any supporting files it needs. Claude Code auto-discovers skills and decides when to invoke one based on its description.

## The skills

- [`braindump`](skills/braindump/SKILL.md) - **the prompt fixer.** Dump whatever's on your mind, as messy and unstructured as it comes out, and it turns that into a clean, structured prompt, shows it to you for a quick sanity check, then runs it. Fires automatically on a raw ramble, no slash command required, or explicitly via `/braindump`.
- [`braindump-auto`](skills/braindump-auto/SKILL.md) - auto-accepts the corrected prompt and runs it immediately, skipping the confirmation step.
- [`superbraindump`](skills/superbraindump/SKILL.md) - the heavy-duty tier for big, tangled, multi-part dumps: deeper extraction, a richer prompt template, still requires review before running.
- [`windows-process-restart`](skills/windows-process-restart/SKILL.md) - safely restarts a supervised Windows background process (kill the child, not the supervisor) with real PID-level verification, written after `TaskStop`'s success message turned out to be untrustworthy on Windows and silently left dead processes behind more than once.
- [`repo-hygiene`](skills/repo-hygiene/SKILL.md) - cleans up proven-junk stray files (a known Git-Bash bug that mangles script fragments into literal files on disk) and defaults every new repo to private, without ever guessing at what counts as junk.

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

See [toolkit](https://github.com/SamuelNDCE/toolkit) for the full index of published tools, and [claude-super-skill-library](https://github.com/SamuelNDCE/claude-super-skill-library) for the full 289-skill collection this repo's five are also featured in.

## License

MIT - see [LICENSE](LICENSE).
