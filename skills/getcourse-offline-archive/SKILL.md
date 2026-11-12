---
name: getcourse-offline-archive
description: >-
  Builds offline HTML archives of GetCourse (and similar) online courses:
  modules/lessons, full ordered lesson text, local PDFs/schemes/images,
  Rutube/YouTube URLs with real titles, preserved external links, platform-junk
  cleanup, selective parity QA vs live, UTF-8 zip. Capture + dumb HTML only —
  not video players, CTA buttons, materials panels, or readability polish.
  Use when the user asks to archive, download, mirror, offline-copy, or
  «собрать курс», or mentions oxanaoleva.ru / pl/teach lesson URLs. If the user
  says «причеши курс» / «полный апгрейд» on an *already* archived folder —
  do NOT re-scrape; that is course-html-polish (Phase 0 packaging + markup +
  readability, then course-toc-nav). Do NOT use for players/CTAs/panels,
  walls of text, tip fatigue, or language editing — that is course-html-polish.
  Do NOT use for оглавление / сайдбар / TOC nav — that is course-toc-nav.
---

# GetCourse offline course archive

Project hub: `AGENTS.md`. After zip/polish/nav, offline mp4 downloads are **`course-local-video`** (this skill keeps videos as URLs by default). Fin pack to SD → `course-fin-sd`.

## Hard exclusion — эталон

**Never** scrape into or overwrite a `*-эталон` folder. Archives go to a new `уроки/<slug>/` tree; polish/toc-nav use copies. An existing эталон is read-only canon.

Assemble a **content-complete** local HTML copy of a GetCourse course — **parity with the live lesson** (text, order, files, links). Videos stay as **external URLs** (Rutube/YouTube). PDFs, schemes, and lesson images are saved locally.

This skill is **capture + dumb emit**. It does **not** build fancy players, burgundy CTAs, materials/stages panels, assignment `.note` chrome, lightbox standards, tip-groups, walls splits, or language polish — that is **`course-html-polish` Phase 0+** on a `*-polish` copy. It does **not** redesign оглавление / сайдбар — that is `course-toc-nav`.

Pipeline: **archive (this)** → polish (Phase 0 packaging → разметка → читаемость) → toc-nav.

**Lean archive reference (intentional):** any existing lean folder such as `уроки/янтра-курс/` shows the expected *chrome incompleteness* (link-cards, plain `t.me`, simple headings). That is not a packaging failure — packaging belongs to polish via `_course-chrome-kit/`. Do **not** copy another course’s `_assets` or brand into the new archive.

## Auth and access

- **Never ask for login/password in chat.** User opens the course logged-in in Cursor Browser (or an already authenticated tab).
- Extract content via browser MCP (`fetch` with `credentials: 'include'`, CDP `Runtime.evaluate`, snapshots). Prefer the live session over scraping cookies.
- If a lesson is empty or 403, re-fetch in the authenticated browser; do not invent content.

## Deliverable layout

```
уроки/<slug-курса>/
  index.html                 # TOC: modules → lessons
  модуль-1/
    index.html               # optional module TOC
    01-<slug>.html
    ...
  модуль-N/
  _вложения/
    модуль-N/<lessonId>/...  # PDFs, PNG/JPG schemes, images, audio
```

Sibling zip next to the folder (or Desktop): short human title + `.zip`.

Keep the zip **clean**: no `_data/`, `_tmp_*`, download logs, duplicate crops, checklist pages, or junk CDN saves.

## Workflow

```
- [ ] 1. Structure: modules + lesson IDs/titles from training tree
- [ ] 2. Extract each lesson (text, videos, files, images, external links) in live order
- [ ] 3. Download attachments locally
- [ ] 4. Generate dumb HTML (shared cream theme tokens OK; no packaging chrome / no toc-nav)
- [ ] 5. Cleanup — parity only (missing content, order, junk, broken files)
- [ ] 6. User QA / screenshots → fix mismatches vs live
- [ ] 7. Rebuild UTF-8 zip
```

After zip: if the user wants «причесать» / packaging / readability → **`course-html-polish`** on a sibling `*-polish` copy (legacy: `*-option3`). Do **not** mix Phase 0 packaging into this archive pass. After polish → **`course-toc-nav`**.

### 1. Structure

From the training/stream page collect ordered modules and lessons (`lesson/view?id=…`). Store IDs — attachments folders use `lessonId`.

**TOC (`index.html`):**

- Module label (`.module-label`) + lesson links only
- Do **not** add an extra «открыть модуль» card that repeats the module title
- Module `index.html` pages are fine for in-module nav; main course TOC lists lessons under each module label

### 2. Extract lessons (MUST capture)

Per lesson pull **in live document order** (never scrape all text first and dump all images at the end):

- **Full lesson body text** — greetings, intros before/after video, material notes, assignments. Never ship a lesson that is only video blocks when the live page has paragraphs
- Strip nav chrome only: «Предыдущий/Следующий урок», «N из M», «Задание выполнено», «Бонусные…»
- Catch GetCourse blocks by **exact class tokens** (not substring match on `o-lt-*` wrappers):  
  `lt-onecolumn`, `lt-twocolumn`, `lt-gallery`, `lt-video`, `lt-lesson-video`, `lt-lesson-audio`, `lt-lesson-text`, `lt-lesson-header`, `lt-lesson-image`, `lt-lesson-files`, `lt-image-caption`, `lt-file`, `lt-button`
- **Ignore comment / answer / feedback images** (`answer-files`, comment widgets, mission/feedback blocks)
- Video iframes / links → **Rutube and YouTube URLs only** (do not download video files). Keep **both hosts** when both exist. Store a **real part/title** (from live player or YouTube oEmbed), not generic «Rutube» / «YouTube»
- **Audio** (jPlayer / `.lt-lesson-audio`): download locally; emit a simple download card (badge + title + «Скачать / слушать»). No need for fancy shared chrome beyond usability
- File download links (PDF/DOCX/JPG) including `.lt-lesson-files` — keep **in the same place as on the live page**
- Content images (schemes, examples, **ornament dividers between practices**, `lt-lesson-image`) — full-resolution CDN when possible. **Image-only columns** must still be saved
- **Image placement** — keep each figure where it sits on the live page. Never append a pile of images at the bottom
- **External URLs** — preserve every `t.me`, `pin.it`, marketplace, and similar href **with surrounding label text**. Bare ugly links are OK in the archive; polish turns them into CTAs later
- Strip assignment **submit UI**; keep homework **text**

Batch via authenticated `fetch` in the browser; save raw HTML/JSON under `_data/` only while building, then delete before zip.

### 3. Attachments

- Save under `_вложения/модуль-N/<lessonId>/` (or `_вложения/<lessonId>/`) with clear Russian filenames
- Prefer CDN original over screenshot crops
- **Only live images** — do not invent decorative assets
- Failed CDN (127×, 403 HTML, tiny broken files): delete and re-download or ask user for a screenshot/export

### 4. HTML generation (dumb but complete)

Use a **neutral** inline theme (Cormorant + Manrope, cream card, green accent tokens) for readability of *raw* content. Match the shared look of the project’s offline courses, but **do not**:

- Copy CSS/JS from `янтра-курс`, `квантовый-художник-*`, or any other course folder
- Pull `_course-chrome-kit` polish/nav into the archive (kit is for `*-polish` only)
- Hardcode another course’s title in the HTML shell

Each lesson page:

- Crumb + prev/next nav
- `<section class="card">` for content
- **Videos** as `<article class="video">` with real title + **link buttons** to Rutube and/or YouTube (or two adjacent link-cards for the same part). See [reference.md](reference.md). Do **not** require `.video-player`, posters, `about:blank`, or switch UI here
- Local files as `<a class="file" download>` at live positions — never dump all attachments at the top
- Schemes as `<figure class="figure">` + `<figcaption>` — do not duplicate author names already on/above the image
- Lists: prefer real `<ul>` / `<ol>` when live is clearly a list; if scrape leaves `1. …` paragraphs, that is acceptable debt for polish (do not invent structure)
- **Assignment** — heading «Задание» + homework text/list as captured. Do **not** require green `.note` chrome
- **Materials / stages** — heading + list + nearby `.file` / figure as on live. Do **not** require `.materials-panel` / `.stages-panel`
- Source link to live GetCourse lesson at the bottom
- **Do not include** comments, «отправьте отзыв», assignment submit widgets, or platform JSON

Figure CSS must not crop schemes:

```css
.figure img{display:block;width:100%;max-width:100%;height:auto;object-fit:contain;…}
```

### Never in archive (polish owns these)

Do **not** spend archive time on:

| Packaging / UX | Owner |
|----------------|--------|
| `.video-player` / poster / overlay / `about:blank` / host switch / youtube-nocookie recipes | `course-html-polish` Phase 0 |
| Burgundy `.btn-cta`, shop chips, `link-actions` | Phase 0 |
| `.materials-panel` / `.stages-panel` / `.dragon-block` | Phase 0 |
| Assignment green `.note` | Phase 0 |
| Mandatory `a.zoom` + lightbox | Phase 0 |
| tip-group / howto / walls / language / video accordion | polish passes |
| Typed TOC / sidebar / sticky | `course-toc-nav` |

If the user only asked to «собрать курс», stop after parity zip. Hand packaging to polish when they say «причеши».

### 5. Cleanup (parity only)

Fix so the offline page **matches live meaning and composition**, not “looks nicer”:

| Problem | Action |
|---------|--------|
| **Any VPN mentions** | **Always remove** |
| Missing body paragraphs vs live | Restore from live |
| Generic video titles («Rutube» / «YouTube» only) | Replace with real part title (text / link-card label) |
| Extra images not on live | Delete from HTML and attachments |
| «Отзыв», comments, JSON, assignment submit chrome | Remove |
| Images or files dumped in wrong place | Rebuild in live document order |
| Comment/answer images treated as lesson art | Exclude |
| TOC module card + lesson with same title | Drop «открыть модуль» card |
| Duplicate author figcaptions | Keep one attribution |
| Merged headings («Урок 1Урок 2») | Split |
| Obvious scrape typos that break reading | Fix only if clear; full language → polish |
| Crooked layout vs live (many misplaced blocks) | Prefer full lesson rebuild from ordered live blocks |
| Dropped `t.me` / second video host / PDF | Restore — polish cannot invent missing URLs/files |

Do **not** “fix” dual link-cards into a player, bare Telegram into `.btn-cta`, or bare materials into `.materials-panel` during archive.

### 6. Image QA (critical for parity)

GetCourse and early scrapes often **crop scheme bottoms**. For every diagram:

1. Open local file and confirm full shape (bottom gates/lines present)
2. If cropped, re-download full CDN asset (not a page screenshot)
3. Prefer two complete sheets over one truncated crop
4. Spot-check figures after HTML changes

Lightbox clickability is **not** an archive exit requirement.

### 7. Zip

Build with Python `zipfile` and UTF-8 paths (macOS `zip` mangled Cyrillic names). Exclude `_data/`, `_tmp_*`, `.DS_Store`.

```python
# -*- coding: utf-8 -*-
from pathlib import Path
import zipfile
root = Path('…/курс')
out = Path('…/Название.zip')
with zipfile.ZipFile(out, 'w', zipfile.ZIP_DEFLATED) as z:
    for p in sorted(root.rglob('*')):
        if not p.is_file(): continue
        if p.name.startswith('_tmp') or '_data' in p.parts or p.name == '.DS_Store':
            continue
        z.write(p, (Path(root.name) / p.relative_to(root)).as_posix())
```

Python scripts that embed Cyrillic **must** declare `# -*- coding: utf-8 -*-` or write HTML via the Write tool.

## New course kickoff

Ask only what’s missing:

1. Course URL (or open tab)
2. Output folder name / zip title
3. Any lessons to skip (bonus, closed)

Then start a **fresh chat per course**, but load this skill. Packaging + readability → `course-html-polish`. Nav → `course-toc-nav`.

**How to view:** local HTTP from course root is fine; archive does not require working YouTube embeds.

## Parity / QA checkpoints

Parity = **meaning and composition vs the live lesson** (text, block order, video URLs + titles, files, schemes, external hrefs). Beauty / packaging is **not** parity.

Not a full page-by-page audit every time: checklist + selective spot-checks.

### After archive build (before zip / handoff)

- [ ] Module and lesson **counts** match the live training tree
- [ ] Sampled lessons have the same **headings / major sections** as live
- [ ] **Full body text** present (not video-only stubs)
- [ ] **Videos:** every part has real title + Rutube and/or YouTube URL(s); both hosts kept when live has both
- [ ] **PDFs / schemes / content images** saved locally and placed in live document order
- [ ] **External links** (`t.me`, pin.it, shops) preserved with nearby labels
- [ ] No platform junk left that hides or replaces author content
- [ ] Spot-check a few text-heavy + media-heavy lessons vs live — restore anything dropped

If something is missing vs live → fix in the archive before polish starts. Downstream skills assume this folder is the **content** source of truth (chrome-incomplete is expected).

## Review decisions (сверка зима ↔ другая версия)

When the archive has comparison markers (`.cmp-pair`, `.cmp-version--stage2`, `.cmp-version--winter`, banners «для сверки»):

- **«убираем …» / «2 ст убираем» / «это убираем»** — delete that variant **and** its colored mark/wrapper
- After removing one side of a `.cmp-pair`, unwrap the pair
- **«пометку снимаем»** alone — unwrap the mark but keep the text inside
- Prefer the winter course as the working copy unless the user explicitly keeps the other version

## More detail

- Lean templates + strip patterns: [reference.md](reference.md)
- Packaging + readability: skill `course-html-polish`
- Оглавление / сайдбар: skill `course-toc-nav`
