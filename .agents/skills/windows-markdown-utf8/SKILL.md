---
name: windows-markdown-utf8
description: Avoid UTF-8 BOM issues when creating or editing Markdown knowledge-base files from Windows PowerShell, especially when the ATS validator expects frontmatter at byte zero.
---

# Safe UTF-8 Markdown writes on Windows

When writing Markdown files in the ATS knowledge base from Windows PowerShell:

1. Prefer an explicit UTF-8 encoding without a byte-order mark:
   `$utf8 = New-Object System.Text.UTF8Encoding($false)`
   then write with `[System.IO.File]::WriteAllText($path, $content, $utf8)`.
2. Do not assume `Set-Content -Encoding utf8` is BOM-free. In Windows PowerShell it may write a UTF-8 BOM.
3. A BOM before the opening `---` can cause frontmatter parsers that anchor at the start of the file (for example, a Python regex using `^---`) to reject an otherwise valid Markdown document.
4. After writing, run the repository's validator and confirm it reports the new procedure's media count and no frontmatter errors.
5. Keep the change scoped to the intended Markdown files; do not bulk-rewrite unrelated content just to normalize encoding.
