---
name: add-cv
description: Add new CV HTML files to the WebCV repository and sync the entire repo. Processes raw .html files (folder + GoogleTag + index.html), then stages all repo changes (adds/modifies/deletes), commits, and pushes to main.
permalink: /add-cv
command: true
---

# Add CV to WebCV

Process raw CV `.html` files in the `WebCV/` parent directory, integrate them into the repository, then **sync the entire working tree** (not just the newly added CV).

## When to use

When the user asks to:
- Add a new CV, 简历, or HTML resume to WebCV
- Process HTML files in the WebCV directory
- Run the CV import/processing pipeline
- Sync / update the whole WebCV repo (including local deletions)

## Steps

### 1. Scan for .html files

Look in `D:\HWDownloads\AI_Agent\Codex\WebCV\` (the parent of the repo root) for `.html` files.

```bash
ls D:/HWDownloads/AI_Agent/Codex/WebCV/*.html
```

If there are no new `.html` files in the parent directory, skip steps 2–6 and go straight to **step 7 (full-repo sync)**.

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

Repeat steps 3–6 for every new `.html` file found in step 1.

### 7. Full-repo sync (required — do not only stage the new CV)

After processing (or if there was nothing new to process), sync **the entire repository working tree**, not just the newly added folder(s).

```bash
cd D:/HWDownloads/AI_Agent/Codex/WebCV/WebCV
git status
```

1. Review `git status` and list what will be included:
   - New CV folders (`untracked`)
   - Modified files
   - Deleted CV folders / files (local removals)
2. Stage **everything** in the repo:

```bash
git add -A
git status
```

3. If there is nothing staged, stop and report “working tree clean — nothing to push.”
4. Otherwise commit with a message that reflects the full change set:

```bash
# Prefer a summary covering all changes, e.g.:
# - only adds:     "Add <BaseName> CV" or "Add N CVs: A, B"
# - only deletes:  "Remove obsolete CVs: A, B"
# - mixed:         "Sync WebCV: add A; remove B, C"

git commit -m "$(cat <<'EOF'
<summary covering all staged changes>

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>
EOF
)"
```

5. Push the whole branch:

```bash
git push origin main
git status
git log -1 --oneline
```

### 8. Verify

- Parent dir should have no leftover CV `.html` files (only `Command.txt`, `GoogleTag.txt`, `WebCV/`, skills, etc.).
- For each newly added CV, list the target folder and read the first ~15 lines of `index.html`. The GoogleTag block should be the first child of `<head>` (or already present).
- Confirm `git status` is clean (or only shows intentional untracked non-CV noise) and branch is up to date with `origin/main`.

## Notes

- The git repo root is `WebCV/WebCV/`, not `WebCV/`.
- Remote: `https://github.com/Sundylee/WebCV.git`, branch: `main`.
- **Always use `git add -A` for the final stage** so deletions and other local edits ship with the new CVs. Never limit the commit to only `git add <BaseName>/` unless the user explicitly asks to stage a single path.
- If the file already has the `googletagmanager` snippet in `<head>`, skip the GoogleTag insertion step.
- Empty directories left after deleted `index.html` files are fine; git tracks files, not empty dirs. `git add -A` records the file deletions.
- Do not commit secrets; this repo only holds public CV HTML + Pages assets.
