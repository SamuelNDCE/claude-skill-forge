---
name: "Zombie Process Sweep"
description: "Find and safely kill orphaned dev servers/watchers left running at session end, enforcing a 'stop what you started' rule automatically instead of relying on memory. Use at the end of any session that started a background dev server, watcher, or long-running process — and whenever a dev server seems to be hanging mid-session."
---

# Zombie Process Sweep

Every background process started during a session should be stopped when its task is done, unless it was explicitly asked to persist. This automates checking for the ones that got left behind.

## Why this exists

The same "dev server hanging" failure has recurred multiple times in real sessions — a `next dev` or Vite process left running, or wedged, from an earlier turn, silently eating a port or CPU until someone notices something's wrong.

## Procedure

**1. Find the real processes, don't guess:**
```powershell
Get-CimInstance Win32_Process -Filter "Name='node.exe'" | Select-Object ProcessId, ParentProcessId, CommandLine
```
Match on `CommandLine`, not on process name alone — `node.exe` alone doesn't tell you what's actually running.

**2. Identify supervisor vs. child** for anything running under a watcher/supervisor pattern (e.g. `run.js` spawning `index.js`). Only kill the child if a live reload is the goal — killing the supervisor takes the whole thing down and it won't respawn.

**3. Kill only processes you can positively tie to this session's own launches**, or that are unambiguously orphaned (parent PID no longer exists, and the process matches a known dev-server/watcher command shape: `next dev`, `vite`, `npm run dev`, `tauri dev`, etc.). Confirm with the user before killing anything that might be serving live/production traffic.

**4. Never build this around a broad heuristic** ("kill anything that looks old or unused"). A dead-parent-PID heuristic alone has produced false positives against live processes before. Only act on the specific, verified signature: known dev-command pattern + confirmed-dead or confirmed-orphaned parent — flag anything less certain instead of killing it.

**5. Verify the kill actually worked and nothing needed it:**
```powershell
Get-Process -Id <pid> -ErrorAction SilentlyContinue   # should return nothing after Stop-Process
```
Then check the port is actually free if the goal was freeing a port, not just that the PID is gone.

## Relationship to `windows-shell-tool-selection`

Both are Windows-environment operational skills; this one is about *what's running*, that one is about *which tool to type the commands into*. They're commonly needed in the same session.
