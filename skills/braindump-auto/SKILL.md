---
name: braindump-auto
description: "The prompt fixer, auto-accept variant: turns a messy brain dump into a clean, structured prompt and executes it immediately, no approval step. Same extraction and rewrite as braindump, but skips the confirmation gate entirely. Triggers on explicit invocation via /braindump-auto, or phrases like 'braindump auto', 'braindump autoexit', 'braindump and just run it', 'auto braindump', 'skip the confirmation and braindump this'. For a single-topic ramble where you want to review the cleaned-up prompt before it runs, use the braindump skill instead. For complex/high-stakes dumps, prefer superbraindump (which still requires approval) over skipping the gate."
---

# Braindump Auto

> **The prompt fixer, no-wait tier.** Same as `braindump`: messy input in, clean prompt out. The only difference is it doesn't stop to ask "go?" — it extracts, rewrites, and runs.

## What this does

Identical contract to [`braindump`](../braindump/SKILL.md) through Step 2: dump in, structured prompt out. Where `braindump` stops and waits for you to approve, tweak, or correct an assumption, `braindump-auto` states its assumptions in one line and executes immediately. Use it when you trust the extraction and just want the result, not a round trip.

## Choosing between braindump and braindump-auto

| | `braindump` | `braindump-auto` (this skill) |
|---|---|---|
| Use for | You want to see the cleaned-up prompt before it runs | You trust the fix, just want it run |
| Approval gate | Yes, hard stop until you say "go" or correct something | None, executes right after refining |
| Fires on | `/braindump`, a raw single-topic ramble, "fix this prompt" | `/braindump-auto`, "braindump auto", "braindump autoexit", "braindump and just run it" |
| Risk | Lower, you catch bad assumptions before anything happens | Higher, a wrong assumption runs before you see it. Don't use for hard-to-reverse or high-stakes asks |

For messages that bundle multiple asks, run long, or touch high-stakes/hard-to-reverse work, use [`superbraindump`](../superbraindump/SKILL.md) instead, and let it keep its approval gate. Auto-accept is for low-stakes, single-topic asks, not complex ones.

## Usage

```
/braindump-auto <everything on your mind, as messy as you like>
```

## Process

### Step 1: Extract the signal

Same as `braindump` Step 1: pull out goal, context, constraints, success criteria, ambiguities. Ignore filler and tangents unless they change the goal.

### Step 2: Rewrite as a structured prompt

Same template as `braindump` Step 2:

```
**Goal:** <one clear sentence>
**Context:** <relevant project/files/tools>
**Constraints:** <bullet list, only real ones>
**Done when:** <concrete success criteria>
```

Omit empty sections. Shorter than the dump, not longer.

### Step 3: State it and go, no gate

Show the refined prompt and any assumptions made to fill gaps, in the same breath as executing, not as a separate turn waiting on a reply:

> **Goal:** ...
> **Context:** ...
> Assumed: "the store" refers to the e-commerce store being discussed.
>
> Running this now.

Then immediately execute the refined prompt in the current session, as if it were the user's own message. Follow all normal workflows and skills the refined prompt would trigger, including this project's own safety rules (explicit-permission and prohibited-action categories still apply; auto-accept only removes the confirm-the-prompt step, it does not pre-authorize sends, purchases, deletions, or other gated actions the refined prompt might trigger).

## Rules

- **No approval gate.** This is the entire difference from `braindump`. Everything else about extraction and rewriting is identical.
- **One clarifying question max, and only if execution is genuinely impossible without the answer.** Otherwise assume and state it, then proceed.
- **Still surface assumptions.** Skipping the wait isn't skipping transparency. Always list what was assumed, inline, before running.
- **Preserve intent, not wording.** If the dump rambled toward X but clearly meant Y by the end, the prompt is Y.
- **Downstream gated actions still gate.** If the refined prompt would trigger a send/purchase/delete/publish or anything else requiring explicit permission under this project's rules, that permission check still happens. Auto-accept applies to the braindump confirmation step only.
- **If mid-extraction the dump turns out to be complex, bundled, or high-stakes, stop and say so** rather than auto-running a risky guess, suggest `superbraindump` instead.

## Example

**Dump:**
> braindump auto: rename the getUserData function to fetchUserProfile across the repo, it's a bad name and keeps confusing me

**Output (stated, then executed immediately):**
> **Goal:** Rename `getUserData` to `fetchUserProfile` across the repo.
> **Context:** Current name is confusing/misleading.
> **Done when:** All references renamed, no broken imports.
>
> Assumed: a call-graph-aware rename (not blind find-and-replace) since this touches multiple call sites.
>
> Running this now.

## See also

[`braindump`](../braindump/SKILL.md) — the same tool with a confirmation step before it runs.
[`superbraindump`](../superbraindump/SKILL.md) — the complex/high-stakes tier; keep its approval gate even when using auto-accept habits elsewhere.
