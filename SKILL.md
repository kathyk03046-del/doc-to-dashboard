# doc-to-dashboard

Convert any structured document into a self-contained, interactive HTML dashboard that opens directly in a browser — and is safe to share with others.

## Trigger

User @mentions this file and provides a document (file path, pasted text, or attachment).

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
- Dark header/nav, light content area

### Color

Infer the color palette from the document's domain:

| Domain signals | Palette |
|---------------|---------|
| Finance, revenue, budget | Green (#16a34a) for positive, red (#dc2626) for negative; neutral dark nav |
| Health, medical | Calm blue (#2563eb), white content |
| Research, academic | Neutral dark (#1e293b) nav, slate content |
| Product, roadmap | Purple (#7c3aed) accent |
| No clear domain | Neutral: dark nav (#1e293b), slate accent (#475569) |

Do not use a fixed accent color regardless of domain. If domain inference is ambiguous, use the neutral palette and note it in the decision log.

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
- Add a search/filter box only if the document contains a long enumeration that a user would realistically want to scan: a glossary, a list of ≥20 named items, or a data table with ≥15 rows. Do not add search for ordinary prose sections.
- Collapsible subsections if nesting depth ≥ 2

### Typography and spacing

```
Font: system-ui, -apple-system, sans-serif
Base size: 15px
Line height: 1.6
Card background: #ffffff, 1px border #e2e8f0
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
  <style>/* all styles inline */</style>
</head>
<body>
  <!--
  DASHBOARD LOG
  Source: [filename or "pasted text"]
  Domain detected: [domain or "none — using neutral palette"]
  Sections: [list]
  Charts: [list or "none"]
  Degraded sections: [list or "none"]
  Ambiguities noted: [list or "none"]
  -->
  <header><!-- title + metadata --></header>
  <nav><!-- tabs or sidebar --></nav>
  <main><!-- section content --></main>
  <script>/* all JS inline */</script>
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
@doc-to-dashboard/SKILL.md

/path/to/q2-report.md
```

```
@doc-to-dashboard/SKILL.md

[pastes raw text]
```
