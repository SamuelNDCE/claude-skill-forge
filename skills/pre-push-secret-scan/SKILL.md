---
name: "Pre-Push Secret Scan"
description: "Quick local scan for API keys, tokens, webhook URLs, and credentials before any git push. Lightweight, automatic, every push, the fast everyday sibling of full-account-security-audit. Use right before pushing any commit."
---

# Pre-Push Secret Scan

Runs in seconds, before every push, not just when something feels risky. Cheap insurance against the exact mistake already logged once: a Discord webhook URL committed straight into a public repo.

## The pattern battery

Grep the diff about to be pushed (`git diff origin/<branch>..HEAD` or the staged changes) for:

```
sk-ant-           # Anthropic keys
sk-proj-          # OpenAI project keys
ghp_ | github_pat_ # GitHub tokens
AKIA              # AWS access keys
shpat_            # Shopify access tokens
AIza              # Google API keys
xox[baprs]-       # Slack tokens
discord.com/api/webhooks/   # Discord webhook URLs
hf_               # Hugging Face tokens
-----BEGIN.*PRIVATE KEY-----
eyJ[A-Za-z0-9_-]{10,}\.eyJ  # JWT shape
```

Plus a generic pass for hardcoded credentials: `(key|secret|password|token)\s*[:=]\s*["'][^"']{8,}["']`, filtering out obvious placeholders (`your-key-here`, `xxx`, `<...>`, `.example` files).

## Procedure

1. Run the battery against what's actually about to be pushed, not the whole repo (that's `full-account-security-audit`'s job).
2. Any hit: stop, do not push. Determine if it's a real secret or a placeholder/test fixture (e.g. an intentional fake key in a test file, clearly labeled).
3. If real: remove it, rotate the credential if it was ever pushed before (even to a private repo), and check whether it needs a history rewrite (`public-repo-leak-retraction`) rather than just a follow-up commit. A follow-up commit does not remove it from history.
4. If it's a `.env` file: confirm it's gitignored and was never tracked (`git log --diff-filter=A -- .env` should return nothing).

## Relationship to the other repo-hygiene skills

- This one: fast, runs on every push, catches it before it happens.
- [`full-account-security-audit`](../full-account-security-audit/SKILL.md): slow, comprehensive, periodic, sweeps everything that already exists.
- [`public-repo-leak-retraction`](../public-repo-leak-retraction/SKILL.md): reactive, something already got through both of the above and needs to come out of history entirely.
