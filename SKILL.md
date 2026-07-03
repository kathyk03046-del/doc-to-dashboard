---
name: doc-to-dashboard
description: |
  Convert a structured document (.md, .pdf, .docx, .txt, pasted text, or attachment) into a single self-contained, interactive HTML dashboard with a built-in light/dark theme toggle. Trigger via the /doc-to-dashboard command — no style or domain parameter needed.
---

# doc-to-dashboard

Convert any structured document into a self-contained, interactive HTML dashboard that opens directly in a browser — and is safe to share with others.

## Trigger

User invokes `/doc-to-dashboard` and provides a document (file path, pasted text, or attachment). No style parameter is needed — every dashboard ships with the same light/dark CSS tokens and a built-in toggle.

## What you produce

A single `.html` file with all CSS and JavaScript inline. The file must be fully self-contained: no local file references, no `file://` paths, no relative image src values. The only permitted external dependency is Chart.js loaded from CDN when numeric data is detected — and only when the user is likely online (default assumption unless they specify otherwise).

## Input handling

Accept any of:
- File path to `.md`, `.pdf`, `.docx`, or `.txt`
- Text pasted directly into the chat
- File attachment

**For `.md` and `.txt`:** read directly with the Read tool.

**For `.pdf` and `.docx`:** extract text using Bash (e.g., `pdftotext`, `pandoc`, `python-docx` via a scratch script — use whatever is available in the environment). Do not assume any specific skill exists. If no extraction tool is available, tell the user explicitly which formats can't be processed and ask them to paste the text.

## Output

Write the HTML file to the same directory as the source document, or to the current working directory if input was pasted text. Name it `<source-filename>-dashboard.html` or `dashboard.html` for pasted input.

Confirm the output path to the user after writing.

**Hard constraint:** the output HTML must contain zero references to local filesystem paths. All content must be embedded inline. Validate this before writing.

## Dashboard design rules

### Structure detection (do this first)

Analyze the document and identify:
1. **Top-level sections** — become tabs or accordion panels
2. **Lists and tables** — render as styled cards or data tables
3. **Numeric data** — flag for chart rendering
4. **Key-value pairs** — render as stat cards with large typography
5. **Ambiguous structure** — label explicitly as "Raw Content" rather than guessing

When structure is unclear, prefer explicit degradation: show the content in a clearly-labeled section rather than silently inventing hierarchy.

### Layout

- Use a top tab bar for ≤4 sections, a sidebar for ≥5 sections
- Responsive: readable at 1280px desktop minimum
- Header/nav and content area colors come from the theme tokens below, not fixed hex values

### Theme (light/dark CSS tokens + built-in toggle)

Every dashboard ships with the same two token sets — never infer a palette from document domain or content. Define both as CSS custom properties, default to light, and let the user flip between them at runtime.

```css
:root {
  --bg: #ffffff;
  --bg-elevated: #f8fafc;
  --card-bg: #ffffff;
  --text-primary: #1e293b;
  --text-secondary: #475569;
  --border: #e2e8f0;
  --nav-bg: #1e293b;
  --nav-text: #f8fafc;
  --accent: #2563eb;
  --accent-positive: #16a34a;
  --accent-negative: #dc2626;
}

[data-theme="dark"] {
  --bg: #0f172a;
  --bg-elevated: #1e293b;
  --card-bg: #1e293b;
  --text-primary: #f1f5f9;
  --text-secondary: #94a3b8;
  --border: #334155;
  --nav-bg: #020617;
  --nav-text: #f8fafc;
  --accent: #3b82f6;
  --accent-positive: #22c55e;
  --accent-negative: #f87171;
}
```

Rules:
- All colors in the generated CSS must reference `var(--token)`, never hardcoded hex values, so the toggle covers 100% of the UI (including chart colors — read them from the tokens at render time).
- Place a toggle control (icon button, e.g. ☀/☾) in the header. On click, flip `document.documentElement.dataset.theme` between `"light"` and `"dark"`.
- Persist the choice in `localStorage` and apply it before first paint (inline script in `<head>`, before the stylesheet renders) to avoid a flash of the wrong theme.
- Respect `prefers-color-scheme` only as the initial default when no `localStorage` value exists yet; the explicit toggle always wins after that.

### Numeric data → charts

If numeric data is detected (tables with numbers, time-series, comparisons):
- Load Chart.js from `https://cdn.jsdelivr.net/npm/chart.js`
- Choose chart type by data shape:
  - Time series → line chart
  - Categories with values → bar chart
  - Parts of a whole → doughnut chart
  - Two numeric variables → scatter
- If chart type is ambiguous, default to bar and add `<!-- chart type: bar (defaulted) -->` in the HTML

### Interactivity

- Tab/section switching with smooth transitions
- Tables: sortable columns (vanilla JS, no library)
- Light/dark theme toggle in the header (see Theme section above) — required on every dashboard
- Add a search/filter box only if the document contains a long enumeration that a user would realistically want to scan: a glossary, a list of ≥20 named items, or a data table with ≥15 rows. Do not add search for ordinary prose sections.
- Collapsible subsections if nesting depth ≥ 2

### Typography and spacing

```
Font: system-ui, -apple-system, sans-serif
Base size: 15px
Line height: 1.6
Card background: var(--card-bg), 1px border var(--border)
Border radius: 8px
```

## HTML output structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Document Title] — Dashboard</title>
  <!-- Chart.js CDN only if numeric data present -->
  <script>/* inline: apply saved/preferred theme to <html data-theme> before first paint */</script>
  <style>/* all styles inline, all colors via var(--token) */</style>
</head>
<body>
  <!--
  DASHBOARD LOG
  Source: [filename or "pasted text"]
  Sections: [list]
  Charts: [list or "none"]
  Degraded sections: [list or "none"]
  Ambiguities noted: [list or "none"]
  -->
  <header><!-- title + metadata + theme toggle button --></header>
  <nav><!-- tabs or sidebar --></nav>
  <main><!-- section content --></main>
  <script>/* all JS inline, incl. theme toggle handler */</script>
</body>
</html>
```

The decision log lives as an HTML comment so it's invisible to end users but readable by anyone inspecting the source.

## Error cases

| Situation | Action |
|-----------|--------|
| File not found | Stop, ask user to verify path |
| File too large to read fully | Process first ~8000 words, note truncation visibly in the dashboard header |
| No discernible structure | Single-panel dashboard with full text in a readable container; banner: "No structure detected — displaying raw content" |
| PDF/DOCX extraction tool unavailable | Tell the user which tool is missing and ask them to paste the text |

## Example invocations

```
/doc-to-dashboard /path/to/q2-report.md
```

```
/doc-to-dashboard
[pastes raw text]
```
