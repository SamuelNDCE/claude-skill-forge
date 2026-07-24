---
name: "Windows Process Restart (Safe)"
description: "Safely stop and restart Windows background Node/supervised processes with real verification. Use when restarting a supervised bot process, any run.js-supervised watcher, or any long-running background process on this machine, especially after a code change that needs a live reload."
---

# Windows Process Restart (Safe)

TaskStop's success message is NOT trustworthy on this Windows setup (logged 2026-07-02, recurred 2026-07-04 and 2026-07-06). Never report a restart done without PID-level verification.

## The procedure

1. **Find the real processes first:**
```powershell
Get-CimInstance Win32_Process -Filter "Name='node.exe'" | Select-Object ProcessId, ParentProcessId, CommandLine
```
Identify which PID is the supervisor (e.g. `run.js`) and which is the child (e.g. `index.js`). Match on CommandLine, not guesswork.

2. **For supervised processes (child/supervisor pattern): kill ONLY the child.** The self-healing supervisor respawns it with the new code. Killing the supervisor takes everything down.
```powershell
Stop-Process -Id <childPid> -Force
```
Confirm with the user before killing anything if the process serves live traffic.

3. **Verify the respawn actually happened:**
```powershell
Get-CimInstance Win32_Process -Filter "Name='node.exe'" | Select-Object ProcessId, CommandLine
```
- A NEW PID must exist for the child script.
- Wait a few seconds and re-check: same PID still alive (no crash loop), sane memory footprint.
- For watchers, also confirm the state file timestamp refreshes (e.g. `launch-watch-state.json`).

4. **Before wiring new code into a supervised process:** `node --check <file>` every changed file first. A syntax error puts the supervisor into a crash loop.

## Rules

- Never trust "stopped successfully" from any tool: verify by process listing.
- Never `Stop-Process` by name (`node`) on this machine: multiple unrelated Node processes run (MCP servers, other bots).
- If a Tauri/WebView2 app window stops rendering with the process alive, check for a stale lock after a previous taskkill before restarting blindly (see NV lesson 2026-07-05).
