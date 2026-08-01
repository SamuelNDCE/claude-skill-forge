---
name: superbraindump
description: "The heavy-duty prompt fixer for complex dumps: turns a large, sprawling, multi-part, or high-stakes brain dump into a rigorous, expert-crafted prompt (goal, context, constraints, output format, edge cases, checkable done-when), confirms it, then executes it. Auto-triggers, even without the /superbraindump command, when a raw message bundles multiple unrelated asks, runs long, carries many constraints, spans multiple files/repos/systems, or involves high ambiguity or hard-to-reverse work. Also triggers on explicit invocation via /superbraindump, or requests like 'this is a big messy one', 'help me think through all of this', or 'turn this into a real spec'. For a single-topic, low-stakes ramble, use the lighter braindump skill instead."
---

# Super Braindump

> **The prompt fixer, high-end tier.** For dumps too big or too tangled for a quick clean-up: full signal extraction, ambiguity resolved instead of deferred, a prompt built like an expert prompt engineer would write it.

## What this does

Same contract as [`braindump`](../braindump/SKILL.md): messy input in, a structured prompt out, shown for approval, then executed. This tier is for the dumps plain `braindump` isn't rigorous enough for. See the [comparison table in `braindump/SKILL.md`](../braindump/SKILL.md#choosing-between-braindump-and-superbraindump) for exactly which dumps route here.

## Fires on

- Explicit `/superbraindump <dump>`.
- Automatically, whenever a raw message is clearly a braindump **and** shows complexity signals:
  - Bundles multiple unrelated asks
  - Runs long or sprawls across topics
  - Carries many constraints or stated edge cases
  - Spans multiple files, repos, or systems
  - Involves real ambiguity that needs a judgment call, not just a guess
  - Touches high-stakes or hard-to-reverse work
- Requests like "this is a big messy one", "help me think through all of this", "turn this into a real spec".

A single clean ask that's just poorly worded belongs to `braindump` instead, it's faster and just as good for that case.

## Usage

```
/superbraindump <everything on your mind, as messy and sprawling as it needs to be>
```

## Process

### Step 1: Deep extraction

Read the entire dump, more thoroughly than a quick pass. Pull out, per distinct ask if there's more than one:

- **Goal**: the real outcome, not just the first thing mentioned. If the dump wanders, follow it to where it lands.
- **Context**: project, files, tools, prior decisions, constraints from earlier conversations, anything load-bearing.
- **Constraints**: every "but", "don't", "except", "it has to", "I hate when", including ones implied by tone (frustration, urgency, prior bad experience).
- **Success criteria**: what "done" or "good" looks like. If not stated, infer something checkable, not a vibe.
- **Ambiguities**: anything readable two ways. Resolve what you reasonably can with a stated assumption; only leave genuinely unresolvable ones as an open question.
- **Distinct asks**: if the dump bundles unrelated requests, separate them explicitly rather than merging them into one blurry goal.

Ignore filler and tangents unless they change the goal or reveal a constraint.

### Step 2: Rewrite as an expert-crafted prompt

Produce a prompt block, one per distinct ask:

```
**Goal:** <one clear, specific sentence, the actual outcome, not the first thing they said>
**Context:** <relevant project/files/tools/prior decisions, only what's load-bearing>
**Constraints:** <bullet list, only real ones, technical, scope, style>
**Output format:** <how the result should be delivered, only when it changes what "correct" looks like, code vs. explanation vs. file, structure, length>
**Edge cases / watch-outs:** <ambiguities or failure modes the dump hints at, resolved or explicitly flagged, only if real ones exist>
**Done when:** <concrete, checkable success criteria, verifiable, not "it works">
```

Craft it like an expert prompt engineer would:

- **Specificity beats vagueness.** Replace "make it better" with the actual dimension: faster, clearer, fewer bugs, more prominent.
- **Resolve ambiguity in the prompt, don't defer it.** If the dump implies a decision, state it as a constraint or assumption instead of leaving it for the executor to guess.
- **Success criteria must be checkable.** "Done when tests pass" beats "done when it looks good."
- **Output format only when it matters.** Skip it if plain prose is obviously fine.
- **Cut everything that doesn't change the outcome.** More sections is not the goal; more signal per word is.

Omit any field with nothing real in it. The refined prompt should read like a well-written spec, not a longer version of the dump.

**Multiple distinct asks:** list each as its own prompt block, in a sensible order, and ask which to run first (or confirm running them in sequence).

### Step 3: Show it and flag assumptions

Present the refined prompt(s) to the user. Below them, list every assumption made to fill a gap, e.g.:

> Assumed: "the store" refers to the e-commerce store being discussed.
> Assumed: "high stakes" here means pushing to a public repo's main branch, not a staging environment.

Then ask for a go-ahead: the user replies "go", tweaks the prompt, reorders the asks, or corrects an assumption.

### Step 4: Execute on approval

When the user approves, execute the refined prompt(s) immediately in the current session, in the confirmed order, as if they were the user's own message. Follow all normal workflows and skills each refined prompt would trigger. If execution involves committing or pushing to a repo, verify the save actually landed (check `git status`, confirm the push reached the remote) before reporting it done, instead of assuming the command succeeded.

## Rules

- **One clarifying question max**, and only if execution is genuinely impossible without the answer. Otherwise make a reasonable assumption and flag it in Step 3, that's what this tier is for.
- **Never execute before approval.** Step 3 is a hard gate, same as `braindump`.
- **Preserve the user's intent, not their wording.** If they rambled toward X but clearly meant Y by the end, the prompt is Y.
- **No side effects during refinement.** No vault writes, no logging, no file changes until execution is approved.
- **Don't pad just because the dump was big.** A complex dump can still collapse into a short, sharp prompt; do that when it's true.

## Example

**Dump:**
> ok so the braindump skill on my github needs to look better and be more prominent, its really good, call it a prompt fixer so people dont have to prompt engineer. also add a featured section with a few other good skills. also improve braindump so it writes super high end prompts. also split that into a separate super braindump skill for complex dumps only, and make claude auto-use both without needing the slash command, with a guide for when each one fires.

**Refined prompt:**
> **Goal:** Rebrand and feature the `braindump` skill across its published repos as "the prompt fixer," split its advanced prompt-writing tier into a new `superbraindump` skill, and make both auto-invocable by Claude based on dump complexity.
> **Context:** Skill is published to the public skill library and mirrored locally in `~/.claude/skills/`. `/braindump` must stay the base command name.
> **Constraints:** Keep `braindump` fast and light; put the richer template only in `superbraindump`. Both need auto-trigger descriptions plus explicit slash commands.
> **Output format:** Updated `SKILL.md` for both skills (new + existing), a "Featured Skills" section in both READMEs, a one-line mention in the `toolkit` README.
> **Edge cases / watch-outs:** Don't let auto-triggering fire on ordinary well-formed requests, only on genuine rambles. Don't let the featured section balloon past a handful of skills.
> **Done when:** All files updated and pushed to `main` across all three repos plus the local copy.
>
> Assumed: featured skills alongside braindump/superbraindump are `verification-loop`, `karpathy-guidelines`, `tdd-workflow`, `prompt-optimizer`.

## See also

[`braindump`](../braindump/SKILL.md): the fast, everyday tier for simple messy asks.
