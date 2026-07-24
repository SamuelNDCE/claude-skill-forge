---
name: "Large Task Session Split"
description: "For a large coding or multi-part task, draft an implementation plan split into independent pieces, then hand each piece off to its own separate session (not a sub-agent inside one session) to execute in parallel. Use when a task is big enough that one continuous session risks context rot, or would clearly benefit from parallel execution across independent features."
---

# Large Task Session Split

Separate sessions, not nested sub-agents, for large work. One long session accumulates history until quality degrades; sub-agents inside one session still share and compete for that session's context and token budget. Independent sessions each start clean and run genuinely in parallel.

## When to use this

A task is a candidate when it has multiple independent pieces (features, modules, migrations) that don't need to see each other's intermediate work, and the total scope is large enough that doing it serially in one session would run long enough to risk context rot. A single feature, or a task where every piece depends on the last, doesn't need this: just do it directly.

## Procedure

**1. Draft the plan in one stable session first.** Don't split before there's a plan. The planning session reads the codebase, decides what the independent pieces actually are, and writes an implementation plan for each: goal, relevant files, constraints, done-when.

**2. Verify the pieces are actually independent before splitting.** Two "independent" features that both touch the same file, config, or shared dependency will conflict when merged. Check for file/module overlap before committing to the split; if two pieces do overlap, either merge them into one piece or make the overlap explicit so it's handled centrally rather than by both sessions independently.

**3. Isolate each piece in its own git worktree.** See [`worktree-task-pack-verification`](../worktree-task-pack-verification/SKILL.md) for the full discipline once sessions are running: independent verification per worktree, a full gate before merge, and how to catch a falsely-claimed clean merge.

**4. Hand off a self-contained kickoff prompt per session.** Each session needs its own plan piece, not the whole master plan: what to build, which files it owns, what "done" looks like, and where its worktree lives. It shouldn't need to ask the planning session anything mid-execution.

**5. Merge and verify centrally when sessions report back.** Don't trust a session's own "done" report; see [`verify-dont-trust`](../verify-dont-trust/SKILL.md). Run the full gate on the merged result, not just on each piece individually.

## What this replaces

Running everything in one session until it's done (risks context rot on large scope), or dispatching many sub-agents from within a single session for pieces that are actually large enough to deserve their own full session and context budget. Sub-agents are still the right call for genuinely small, bounded pieces of work within one session; this skill is for when the pieces are big enough to be their own sessions.
