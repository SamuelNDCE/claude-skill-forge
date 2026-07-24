---
name: "Windows Shell Tool Selection"
description: "Cheat-sheet for when to use a POSIX/Bash tool vs. a PowerShell tool on Windows, and the specific syntax traps between them. Use whenever running shell commands on a Windows machine that has both available — before writing a multi-step or non-trivial command, not after it silently fails."
---

# Windows Shell Tool Selection

On Windows with both a Bash tool (Git-Bash/POSIX) and a PowerShell tool available, picking wrong — or mixing syntax from one into the other — produces failures that don't always look like failures.

## Known traps

**PowerShell chaining is not `&&`/`||`.** Windows PowerShell 5.1 has no pipeline chain operators. `A && B` is a parse error. Use:
```powershell
A; if ($?) { B }      # B only if A succeeded
A; B                  # unconditional
```

**PowerShell has no ternary, null-coalescing, or null-conditional operators** (`?:`, `??`, `?.`) in 5.1. Use explicit `if/else` and `$null -eq` checks.

**A multi-step PowerShell chain is all-or-nothing at parse time.** `step1; step2; step3` — if step 3 has a syntax error, steps 1 and 2 never ran at all, even though they were valid. A silent-looking failure can actually mean nothing executed. Split suspect chains into separate calls to isolate which part is failing.

**Don't redirect `2>&1` on native executables in PowerShell 5.1.** It wraps each stderr line in a NativeCommandError and flips `$?` to false even on a real exit-code-0 success. stderr is already captured — don't redirect it.

**Git-Bash heredocs for multi-line content (commit messages, file writes) — never inline multi-line `node -e` or similar on Windows.** A known Git-Bash bug mangles multi-line script fragments passed inline into literal stray files on disk. Use a proper heredoc (`<<'EOF' ... EOF`) or write to a temp file first.

**PowerShell here-strings (`@'...'@`) must have the closing `'@` at column 0** — indenting it is a parse error, not a formatting choice.

**Encoding:** `>`/`Out-File` in PowerShell 5.1 default to UTF-8 with BOM; `Set-Content`/`Add-Content` default to the system ANSI codepage. If another tool will read the file, pass `-Encoding utf8` explicitly rather than relying on the default.

## Which tool for which job

| Task | Use |
|---|---|
| git, npm, docker, standard CLI tools | PowerShell (or Bash — either works, PowerShell usually has better native integration) |
| Multi-line heredoc content (commit messages, file bodies) | Bash tool — PowerShell here-strings are stricter about formatting |
| Anything doing `find`/`grep`/`sed`-style text processing | Bash tool — the POSIX tools are more direct than PowerShell equivalents |
| Windows-native operations (registry, services, WMI/CIM queries) | PowerShell — no Bash equivalent exists |

## Rule of thumb

If a command is non-trivial (more than one clause, any conditional, any heredoc), decide which tool it's going into *before* writing it, based on which syntax it actually needs — don't write PowerShell-flavored syntax into a Bash call or vice versa and debug the failure after the fact.
