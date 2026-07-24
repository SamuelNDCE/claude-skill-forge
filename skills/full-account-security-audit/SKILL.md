---
name: "Full Account Security Audit"
description: "Comprehensive periodic sweep for leaked secrets across every local repo, every GitHub repo (including full history), local .env files, and GitHub's own secret-scanning alerts. Use on demand or quarterly, not on every push. For that, use pre-push-secret-scan."
---

# Full Account Security Audit

The slow, thorough sibling of [`pre-push-secret-scan`](../pre-push-secret-scan/SKILL.md). This has been done by hand three separate times already. This codifies it into one invocation.

## Scope

1. **Every local git repo** under the workspace root (owned repos; explicitly skip third-party clones).
2. **Every GitHub repo** on the account, public and private.
3. **Full git history** of each, not just the working tree. A secret removed in a later commit is still in history unless it was rewritten out.
4. **Local `.env` and credential files** in every project: confirm each is gitignored and was never tracked (`git log --diff-filter=A -- .env` returns nothing).
5. **GitHub's own secret-scanning alerts API** for every repo where it's enabled.

## The pattern battery

Same core set as `pre-push-secret-scan`, run against tracked files AND full history:
```
sk-ant- | sk-proj-           # Anthropic / OpenAI
ghp_ | github_pat_           # GitHub
AKIA                         # AWS
shpat_                       # Shopify
AIza                         # Google
xox[baprs]-                  # Slack
discord.com/api/webhooks/    # Discord webhooks
hf_                          # Hugging Face
-----BEGIN.*PRIVATE KEY-----
eyJ[A-Za-z0-9_-]{10,}\.eyJ   # JWT shape
```
Plus a generic `key|secret|password|token = "..."` pass, and a `user:pass@` URL pass, both filtered for obvious placeholders.

## Procedure

1. **Local pass:** `git log --all -p` per repo piped through the battery (or shallow-then-full-clone if disk space matters), plus a working-tree grep.
2. **Remote pass:** for each GitHub repo, either clone locally and run the same check, or use the GitHub secret-scanning alerts API directly (`gh api repos/{owner}/{repo}/secret-scanning/alerts`) where available. Note it's typically disabled on free-tier private repos, so don't read a 404/disabled response as "clean."
3. **History-add check:** `git log --diff-filter=A --name-only` across all branches, filtered for credential-shaped filenames (`.env`, `*credentials*`, `*secret*`, `*.pem`, `*state.json` with tokens inside).
4. **Report, don't fix silently:** produce a clear list of findings (or confirm zero) before taking any remediation action, especially before recommending or running a history rewrite, since that's `public-repo-leak-retraction`'s job and is a separate, explicit decision.
5. **Recommend, don't silently enable:** flag whether GitHub's Secret Scanning + Push Protection is on for each public repo; enabling it is a settings change and needs the user's go-ahead.

## Done-when

A finding-by-finding report (zero findings is a valid, good outcome) covering all five scope items above, with nothing silently skipped. If a repo or branch was excluded from the sweep, say so explicitly rather than letting the report imply full coverage.
