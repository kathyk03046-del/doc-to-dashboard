# doc-to-dashboard

You got a document. You need to know what's in it — fast.
The output is an HTML file you can open, scan, and pass on — no reformatting needed.

## Usage

```
/doc-to-dashboard /path/to/your/document.md
```

Paste text directly if you don't have a file path. No style or domain parameter needed.

## Supported formats

| Format | Notes |
|--------|-------|
| `.md`, `.txt` | Read directly |
| `.pdf` | Requires `pdftotext` or `pandoc` in your environment |
| `.docx` | Requires `pandoc` or `python-docx` in your environment |

## What you get

A single `.html` file — section navigation, tables, charts for numeric data, and a built-in light/dark theme toggle (same CSS tokens every time, no domain-based color guessing).
Written next to your source file, named `<source>-dashboard.html`. Open it, share it.

Works best with structured documents — reports, specs, research notes, reference wikis.
