---
name: add-cv
description: Add new CV HTML files to the WebCV repository. Processes raw .html files by creating a same-named folder, injecting the Google Analytics tag after <head>, renaming to index.html, and moving into WebCV/.
permalink: /add-cv
command: true
---

# Add CV to WebCV

Process raw CV `.html` files in the `WebCV/` parent directory and integrate them into the repository.

## When to use

When the user asks to:
- Add a new CV, 简历, or HTML resume to WebCV
- Process HTML files in the WebCV directory
- Run the CV import/processing pipeline

## Steps

### 1. Scan for .html files

Look in `D:\HWDownloads\AI_Agent\Codex\WebCV\` (the parent of the repo root) for `.html` files.

```bash
ls D:/HWDownloads/AI_Agent/Codex/WebCV/*.html
```

### 2. Identify CV files to process

Skip `WebCV/Index.html` — it's the dashboard/control panel, not a CV.

For each remaining `.html` file (e.g. `IsraelEshkolII_EISeniorEngineer.html`):

### 3. Create a folder named after the file (without .html extension)

```bash
mkdir -p "D:/HWDownloads/AI_Agent/Codex/WebCV/<BaseName>/"
```

### 4. Insert GoogleTag at the top of `<head>`

Read `D:\HWDownloads\AI_Agent\Codex\WebCV\GoogleTag.txt`. The content is:

```
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-E05GTYBL3K"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-E05GTYBL3K');
</script>
```

Use Edit to replace the first line `<head>` with `<head>\n` + the GoogleTag content + a blank line before the next tag. If the file already contains `googletagmanager`, skip the insertion.

### 5. Rename to index.html and move into the folder

```bash
mv "D:/HWDownloads/AI_Agent/Codex/WebCV/<BaseName>.html" "D:/HWDownloads/AI_Agent/Codex/WebCV/<BaseName>/index.html"
```

### 6. Move the folder into the repo

```bash
mv "D:/HWDownloads/AI_Agent/Codex/WebCV/<BaseName>" "D:/HWDownloads/AI_Agent/Codex/WebCV/WebCV/<BaseName>"
```

### 7. Commit and push

```bash
cd D:/HWDownloads/AI_Agent/Codex/WebCV/WebCV
git add <BaseName>/
git commit -m "Add <BaseName> CV

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
git push origin main
```

## Notes

- The git repo root is `WebCV/WebCV/`, not `WebCV/`.
- Remote: `https://github.com/Sundylee/WebCV.git`, branch: `main`.
- If the file already has the `googletagmanager` snippet in `<head>`, skip the GoogleTag insertion step.
- After processing, confirm success by listing the target folder and reading the first ~15 lines of `index.html`. The GoogleTag block should be the first child of `<head>`.
