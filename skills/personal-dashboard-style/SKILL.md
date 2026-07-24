---
name: "Personal Dashboard Style"
description: "Apply a consistent dark-theme HTML report/dashboard convention (background, accent colors, save path, open command) to any generated standalone report instead of re-deriving a visual style from scratch each time. Use whenever generating a self-contained HTML report, dashboard, or comparison view for personal review."
---

# Personal Dashboard Style

Visual/data-heavy output (reports, dashboards, comparisons, anything with 10+ rows of data) gets a self-contained HTML file rather than a wall of text, and it should look like the same system every time, not a new visual style invented per request.

## The convention

- **Dark theme, fixed palette**: navy background (`#0a0d18`), teal accent (`#22d3ee`), purple accent (`#7c3aed`). Use these consistently for the same semantic role every time (e.g. teal for primary metrics/links, purple for secondary/highlight elements) rather than swapping which color means what per report.
- **Save location**: a dedicated `ui/` folder, filename pattern `<date>-<topic>.html` so reports are chronologically sortable and self-describing without opening them.
- **Open command**: launch it directly (e.g. `Start-Process "<path>"` on Windows) rather than just stating the path and leaving it to the user to open manually.
- **Always pair with a short text summary** (2-3 lines) alongside the HTML. The file is the detailed view; the chat response is the at-a-glance takeaway. Never let the HTML be the *only* thing delivered.

## When to reach for this vs. plain text

Use the styled HTML report when: there's a real dataset (10+ rows), a comparison across multiple items, or a dashboard-shaped request. Don't reach for it for a simple one-off answer that reads fine as prose. The convention is for when structure and visual scanning genuinely help, not a default for every response.

## Consistency check before shipping

Before finalizing a report, confirm: same three colors, same semantic role for each, saved to the right path with a sortable filename, opened automatically, and a short accompanying summary written, not just the file dropped with no context.
