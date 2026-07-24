---
name: braindump
description: "The prompt fixer: turns a messy, unstructured brain dump into a clear, well-structured prompt, confirms it, then executes it, so the user never has to prompt engineer by hand. Auto-triggers on any raw, rambling, run-on, or disorganized message, even without the /braindump command: multiple clauses, self-corrections, filler, or unclear phrasing are enough on their own, single-topic or not. When genuinely unsure whether a message qualifies, fire rather than skip; Step 1 already routes to superbraindump if the complexity signals show up once you're reading it. Also triggers on explicit invocation via /braindump, or requests to 'fix this prompt', 'clean this up', or 'prompt engineer this for me'. For messages that clearly bundle multiple unrelated asks, run very long, carry many constraints, span multiple files/repos/systems, or involve high ambiguity or high-stakes/hard-to-reverse work, use the superbraindump skill instead."
---

# Braindump

> **The prompt fixer.** Skip prompt engineering: dump your thoughts as messy as they come, get back a clean, structured prompt built from them, review it in one glance, then it runs.

## What this does

Takes everything after `/braindump`, however messy, rambling, or disorganized, and turns it into a clean, structured prompt. Shows the refined prompt for approval, then executes it in the same session. The user gets the output quality of a well-written prompt without having to write one.

## Choosing between braindump and superbraindump

| | `braindump` (this skill) | [`superbraindump`](../superbraindump/SKILL.md) |
|---|---|---|
| Use for | A single, contained ask, messy but simple | Multiple bundled asks, large/cross-cutting scope, heavy ambiguity, high stakes |
| Fires on | `/braindump`, any raw ramble that doesn't clearly need the heavier tier, or "fix this prompt" / "clean this up" | `/superbraindump`, or a ramble showing the complexity signals in the left column |
| Template | Goal / Context / Constraints / Done when | Same, plus Output format and Edge cases |

Default to firing here when a message is messy but doesn't obviously need the heavier tier. If a dump turns out to match the right column partway through Step 1, stop and switch to `superbraindump` instead of continuing here.

## Usage

```
/braindump <everything on your mind, as messy as you like>
```

## Process

### Step 1: Extract the signal

Read the entire dump and pull out:

- **Goal**: what the user actually wants done (not just what they said first)
- **Context**: project, files, tools, prior work mentioned in passing
- **Constraints**: anything phrased as "but", "don't", "except", "it has to", "I hate when"
- **Success criteria**: what "done" or "good" looks like, stated or implied
- **Ambiguities**: anything that could be read two ways

Ignore filler, self-corrections, and tangents unless they change the goal.

### Step 2: Rewrite as a structured prompt

Produce a compact prompt block:

```
**Goal:** <one clear sentence>
**Context:** <relevant project/files/tools>
**Constraints:** <bullet list, only real ones>
**Done when:** <concrete success criteria>
```

Omit any section that has nothing real in it. Do not pad. The refined prompt should be shorter than the dump, not longer.

### Step 3: Show it and flag assumptions

Present the refined prompt to the user. Below it, list any assumptions made to fill gaps, e.g.:

> Assumed: "the store" refers to the e-commerce store being discussed.

Then ask for a go-ahead: the user replies "go", tweaks the prompt, or corrects an assumption.

### Step 4: Execute on approval

When the user approves, execute the refined prompt immediately in the current session as if it were their message. Follow all normal workflows and skills that the refined prompt would trigger. If execution involves committing or pushing to a repo, verify the save actually landed (check `git status`, confirm the push reached the remote) before reporting it done, instead of assuming the command succeeded.

## Rules

- **One clarifying question max**, and only if execution is genuinely impossible without the answer. Otherwise make a reasonable assumption and flag it in Step 3.
- **Never execute before approval.** Step 3 is a hard gate.
- **Preserve the user's intent, not their wording.** If they rambled toward X but clearly meant Y by the end, the prompt is Y.
- **No side effects during refinement.** No vault writes, no logging, no file changes until execution is approved.
- Keep the refined prompt in the user's language and terms where they are precise; replace vague terms with concrete ones only when the dump makes the meaning clear.

## Example

**Dump:**
> ok so like the product pages are kinda slow i think its the images maybe? also owen said something about the reviews widget but idk. anyway main thing is speed, google is gonna tank us, oh and dont touch the checkout we just fixed it

**Refined prompt:**
> **Goal:** Diagnose and fix product page load speed on the store.
> **Context:** Images are the suspected cause; a reviews widget was also flagged (secondhand, unverified).
> **Constraints:** Do not modify anything in the checkout flow.
> **Done when:** Product pages measurably load faster (before/after numbers shown).
>
> Assumed: "the store" refers to the store being discussed. The reviews widget is investigate-only unless it turns out to be the actual bottleneck.

## See also

[`superbraindump`](../superbraindump/SKILL.md): the complex/high-stakes tier. See the comparison table above for which one fires when.
