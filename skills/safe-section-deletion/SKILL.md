---
name: "Safe Section Deletion"
description: "Before deleting any HTML section, anchor id, exported symbol, or named block, grep the whole codebase for references to it first, and only proceed once zero in-repo references are confirmed. Use whenever a request involves removing a section, id, component, or block that other content might link to or import — the markup/id-level sibling of call-graph impact analysis for code symbols."
---

# Safe Section Deletion

Deleting content that looks redundant or leftover can silently break something that referenced it — an anchor link, an import, a route. This is the discipline that catches that before it happens, not after.

## Procedure

**1. Before deleting, identify what could reference the thing being removed:**
- An HTML section or id → anchor links (`#section-id`) from anywhere in the site, including other pages.
- A named export/component → imports across the codebase.
- A route or page → nav links, redirects, sitemap entries.
- A CSS class → other stylesheets or inline styles depending on it.

**2. Grep for every plausible reference form before touching anything:**
```bash
grep -rn "#section-id" .        # anchor links
grep -rn "ComponentName" .      # imports/usages
grep -rn "/route-path" .        # route references
```
Check for the id/name used both as an href target and as a raw string (some references are constructed dynamically rather than hardcoded).

**3. Zero in-repo references confirmed → safe to delete.** Any hit → resolve it first (update the reference or confirm it's dead too) before deleting the target.

**4. For actual code symbols (functions, classes, methods) rather than markup**, prefer a proper call-graph-aware impact tool if one is available in the project (e.g. an impact-analysis MCP tool) over a plain grep — a grep catches text matches, not semantic call relationships, and can both over- and under-count real usages (a same-named local variable creates a false positive; a re-exported symbol under a different local name creates a false negative).

**5. If the content might be linked from *outside* the repo** (an external site linking to an anchor, a bookmarked URL, an API consumer), say so explicitly — a repo-wide grep can't see that, and the user may need to consider it separately (e.g. a redirect) before the deletion is truly safe.

## Done-when

Every plausible reference form has been checked and is either confirmed absent or resolved, and desktop+mobile (for web content) is verified after the deletion, not just before.
