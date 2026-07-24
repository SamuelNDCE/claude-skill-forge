---
name: "Discord Todo Ops"
description: "Wraps a reaction-based, Discord-backed shared team todo list (add/edit via scripts, accept/complete via Discord itself) into one skill instead of remembering separate script invocations and slash-command semantics each time. Use whenever adding, editing, or checking status on a shared team todo list that's backed by a Discord bot."
---

# Discord Todo Ops

A shared todo list where the list itself lives in a Discord channel, backed by small scripts for the parts that need to run outside Discord (adding and editing entries), and Discord's own reactions/slash-commands for the human-driven parts (accepting, completing).

## The three operations

**Add a task** (from any working directory — the scripts load their own config):
```bash
node path/to/discord-bot/add-todo.js "task text" --by "Claude (<project name>)"
```

**Edit a task** (matches by id or a text substring, rewrites both the task text and its Discord post):
```bash
node path/to/discord-bot/edit-todo.js "task query" "new task text"
```
If the task was already accepted by someone, editing it should also push an update notification to that person's progress channel — don't silently change accepted work without surfacing the change to whoever claimed it.

**Complete a task** — this is a human action, not a script: a person reacts ✅ on the Discord post itself. Don't try to mark something complete programmatically; that decision belongs to whoever's actually doing the work.

**Claim a task** — also a human/Discord-native action via a slash command in the channel (e.g. `/todo accept task:"..."`), not a script.

## Why this split matters

Adding and editing are mechanical (any agent can do them on someone's behalf), but accepting and completing represent a real commitment from a specific person — keeping those as native Discord actions (reaction, slash command) instead of scriptable ones means the audit trail of "who actually claimed and finished what" stays honest and can't be silently spoofed by an agent.

## One-off scripts against the same bot

For anything beyond these three operations (checking server structure, testing a new bot feature, verifying webhook health), write a disposable script following the bot project's own scripting conventions rather than improvising inline multi-line commands against a live Discord connection.
