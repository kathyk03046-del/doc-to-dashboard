# doc-to-dashboard

Most AI tools solve for "I don't want to read this." 
But reading still matters — especially for legal documents, medical records, 
academic papers, and high-stakes decisions where details can't be skipped and summaries aren't enough.

doc-to-dashboard solves for "I need to read this, but I need to finish it with ease."
It doesn't rewrite your document or compress its meaning. It gives you structure and visual navigation — so you can enter dense text faster and keep every detail within reach.

## How to Use
Sonnet is sufficient — no need for Opus

0. git clone https://github.com/kathyk03046-del/doc-to-dashboard
1. Copy SKILL.md to your project's .claude/commands/doc-to-dashboard.md
2. Open Claude Code in your project
3. /doc-to-dashboard your-file.md

## What you get

A single `.html` file — section navigation, tables, charts for numeric data, color scheme matched to the document's subject.
Written next to your source file, named `<source>-dashboard.html`. Open it, share it.

Works best with structured documents — reports, specs, research notes, reference wikis.

## Examples

Input: Wikipedia article — unstructured, text-heavy, no formatting
<table>
  <tr>
    <td><img width="1710" height="988" alt="image" src="https://github.com/user-attachments/assets/6057835a-56f3-4b4e-bcf9-0f55f372f4ad" />
    <td><img width="1710" height="988" alt="image" src="https://github.com/user-attachments/assets/7c502ccb-820f-408c-814a-c7afe1922e17" />
  </tr>
</table>

Input: World Data
<table>
  <tr>
    <td><img width="1710" height="841" alt="image" src="https://github.com/user-attachments/assets/d0b0b304-d0e0-4491-8bc0-aa5f2dcced9d" />
    <td><img width="1710" height="841" alt="image" src="https://github.com/user-attachments/assets/d5c3c48a-2731-4026-8d30-821b0afedde7" />
  </tr>
</table>

