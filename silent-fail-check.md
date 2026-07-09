---
name: silent-fail-check
description: >
  Use this skill ONLY when the user explicitly types /silent-fail-check after a file
  has been generated. Do NOT trigger automatically after every file generation.
  This is a manual, on-demand verification skill — not an auto-firing protocol.

  When triggered, verify the most recently generated file: check file existence,
  size sanity, file type validation, and content extraction. This skill prevents
  the class of bugs where a script exits with returncode 0, prints a success message,
  but produces an empty, corrupt, or near-empty file because a library silently
  ignored invalid parameters or failed to write content.

  Trigger: /silent-fail-check — and only this. Never auto-apply.
---

# Silent Fail Check

A post-generation verification protocol born from a real incident:
a ReportLab PDF script exited cleanly, printed "PDF generated successfully",
but produced a 5.3KB file with no visible content — because `ROUNDEDCORNERS`
is not a valid TableStyle property and was silently ignored.

**Rule**: Only run this checklist when the user types `/silent-fail-check`. Do not auto-trigger after every file generation.

---

## The Core Problem

Libraries like ReportLab, python-docx, and openpyxl silently skip unknown
style properties, invalid parameters, and empty content blocks. They do not
raise exceptions. `returncode == 0` and a success print statement mean the
script ran — not that the output is correct.

---

## Verification Checklist

Run ALL applicable checks immediately after any file is generated.

### 1. File existence
```bash
ls -lh /path/to/output.file
```
- File must exist
- If missing: script failed to write — check path and permissions

### 2. File size sanity check (most important)

| File type     | Suspicious if under | Likely healthy above |
|---------------|---------------------|----------------------|
| PDF (1 page)  | 15 KB               | 25 KB                |
| PDF (5+ pages)| 40 KB               | 80 KB                |
| DOCX          | 8 KB                | 15 KB                |
| XLSX          | 6 KB                | 10 KB                |
| PPTX          | 20 KB               | 50 KB                |
| PNG/JPG       | 5 KB                | depends on content   |

```bash
# Quick check — flag anything suspiciously small
python3 -c "
import os
size = os.path.getsize('/path/to/output.file')
print(f'Size: {size/1024:.1f} KB')
if size < 15000:
    print('WARNING: File is suspiciously small — possible silent failure')
"
```

### 3. File type validation
```bash
file /path/to/output.file
```
Must report the expected type (e.g. "PDF document, version 1.7" not "empty" or "ASCII text").

### 4. Content verification (file-type specific)

**PDF:**
```python
from pypdf import PdfReader
reader = PdfReader("/path/to/output.pdf")
pages = len(reader.pages)
text = "".join(p.extract_text() or "" for p in reader.pages)
print(f"Pages: {pages}")
print(f"Text chars extracted: {len(text)}")
print(f"Sample: {text[:200]}")
# FAIL if: pages == 0, or len(text) < 50 for a content doc
```

**DOCX:**
```python
from docx import Document
doc = Document("/path/to/output.docx")
paras = [p.text for p in doc.paragraphs if p.text.strip()]
print(f"Non-empty paragraphs: {len(paras)}")
print(f"Sample: {paras[:3]}")
# FAIL if: len(paras) == 0
```

**XLSX:**
```python
import openpyxl
wb = openpyxl.load_workbook("/path/to/output.xlsx")
ws = wb.active
rows = list(ws.iter_rows(values_only=True))
non_empty = [r for r in rows if any(c is not None for c in r)]
print(f"Non-empty rows: {len(non_empty)}")
# FAIL if: len(non_empty) == 0
```

**HTML:**
```python
content = open("/path/to/output.html").read()
print(f"Size: {len(content)} chars")
# FAIL if: len(content) < 500 for a real page
```

---

## Decision Tree

```
File generated?
    └── Run size check
            ├── Size suspiciously small?
            │       └── Run content check → if empty: REGENERATE, do not present
            └── Size looks healthy?
                    └── Run content check → if content present: PRESENT to user
                                         → if empty despite size: REGENERATE
```

---

## Known Silent Failure Patterns by Library

### ReportLab
- `ROUNDEDCORNERS` in `TableStyle` — silently ignored, not a valid property
- Invalid `colWidths` summing to wrong total — table renders empty
- `Paragraph` with unsupported XML tags — paragraph skipped silently
- Missing `Spacer` between flowables — content may collapse

### python-docx
- Setting a style name that doesn't exist — falls back to default silently
- Adding a table with 0 rows — no error, no content

### openpyxl
- Writing to a merged cell range incorrectly — data lost silently
- Saving to a path that doesn't exist — raises error (good), but only if path is wrong

### weasyprint
- Network-loaded fonts failing (CSP/firewall) — falls back to system font silently
- CSS properties it doesn't support — silently ignored

---

## Response Protocol

If a silent failure is detected:

1. **Do not present the broken file** to the user
2. **State clearly** what failed and why (size, empty content, etc.)
3. **Fix the root cause** — don't just retry the same code
4. **Re-run verification** after the fix
5. **Only present** once all checks pass

If the file passes all checks:
- Present it confidently
- Optionally note: "Verified: X pages, Y KB" for transparency

---

## Origin

This skill was created after a ReportLab PDF generation incident (May 2026)
where `ROUNDEDCORNERS` was used as a `TableStyle` property — it is not valid.
ReportLab silently ignored it, produced a structurally valid but content-empty
5.3 KB PDF, and the script printed "PDF generated successfully."
The file was presented to the user and failed to display.

The fix: always verify file size AND extract content after generation,
never trust `returncode == 0` alone.
