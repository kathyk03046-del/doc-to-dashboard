# doc-to-dashboard

You got a document. You need to know what's in it — fast.
The output is an HTML file you can open, scan, and pass on — no reformatting needed.

## How to Use

0. git clone https://github.com/kathyk03046-del/doc-to-dashboard
1. Open Claude Code in any project
2. Type @doc-to-dashboard/SKILL.md
3. Paste your document or drop a file path

## Supported formats

| Format | Notes |
|--------|-------|
| `.md`, `.txt` | Read directly |
| `.pdf` | Requires `pdftotext` or `pandoc` in your environment |
| `.docx` | Requires `pandoc` or `python-docx` in your environment |

## What you get

A single `.html` file — section navigation, tables, charts for numeric data, color scheme matched to the document's subject.
Written next to your source file, named `<source>-dashboard.html`. Open it, share it.

Works best with structured documents — reports, specs, research notes, reference wikis.

## Examples

Input: Wikipedia article — unstructured, text-heavy, no formatting
Output: ↓
<table>
  <tr>
    <td><img width="1710" height="988" alt="image" src="https://github.com/user-attachments/assets/6057835a-56f3-4b4e-bcf9-0f55f372f4ad" />
    <td><img width="1710" height="988" alt="image" src="https://github.com/user-attachments/assets/7c502ccb-820f-408c-814a-c7afe1922e17" />
  </tr>
</table>


