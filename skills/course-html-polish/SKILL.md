---
name: course-html-polish
description: >-
  Improves already-built offline course HTML: Phase 0 structure packaging
  (video players, Telegram/Pinterest CTAs, materials/stages panels, assignment
  .note, zoom/lightbox), then markup cleanup, UX hierarchy, tip/howto, video
  stage anchors, walls-of-text splits, light spelling/grammar without rewriting
  author voice, plus entry/exit parity QA vs the archive. Use when the user
  asks for readability polish, пасс разметки, пасс читаемости, walls of text,
  tip fatigue, video-stage anchors, language/orthography polish, UX audit,
  packaging normalize, or work on a *-polish / *-option3 course copy. Also use
  when the user says «причеши курс» or «полный апгрейд курса» — run Phase 0 +
  markup/readability, then hand off to course-toc-nav (no GetCourse scrape).
  Do NOT use for GetCourse scrape, download, or first-time archive build —
  that is getcourse-offline-archive. Do NOT use for оглавление / сайдбар alone
  — that is course-toc-nav. Legacy names «Wave 1» / «option 3» mean the markup
  and readability passes below.
---

# Offline course HTML polish

Project hub: `AGENTS.md` · sibling skills: `getcourse-offline-archive`, `course-toc-nav`, `course-local-video` (mp4 / n/N / «смотрела»), `course-fin-sd` (флешка).

## Hard exclusion — эталон

**Never** run this skill on folders named `*-эталон` (e.g. `квантовый-художник-зима-25-эталон`). Those are locked canons. «Причеши курс» / polish always targets `*-polish` / `*-option3` or a **new copy** — never the эталон tree. Frozen skill snapshots inside an эталон folder are documentation only.

Qualitative + **structural packaging** pass on an **already assembled** offline HTML course. Goal: complete chrome the lean archive intentionally skips, then clearer reading. Not a rewrite of the author’s voice. Not a GetCourse scrape.

Sibling skills: `getcourse-offline-archive` (capture + parity with live — **dumb HTML**); `course-toc-nav` (оглавление, сайдбар, sticky prev/next — after this skill). Pipeline: archive → **polish (this)** → toc-nav.

Assume the archive is **content-complete, chrome-incomplete** (like `янтра-курс/`): link-cards, bare `t.me`, plain «Задание» headings. That is normal — **Phase 0** owns packaging.

## Names (do not use Wave / option 3)

| Old (legacy) | New name | What it is |
|--------------|----------|------------|
| Archive debt gate | **Phase 0 — structure-normalize** | Players, CTAs, materials/stages panels, assignment `.note`, zoom |
| Wave 1 / «опция 2» | **Пасс разметки** | Fake headings → `h3`, real lists, CAPS↓, emoji-spam off — still no paraphrase |
| Option 3 | **Пасс читаемости** | Walls, tip-groups/howto, video stages, language polish, hierarchy |
| Folder `*-option3` | Prefer `*-polish` | Same role; existing `…-option3` folders are fine |

In chat, say **Phase 0** / **пасс разметки** / **пасс читаемости**.

## Chrome source (MUST)

Portable chrome lives at:

`уроки/_course-chrome-kit/`

| Do | Do not |
|----|--------|
| Copy `course-ui.css` (and later nav files via toc-nav) **only** from `_course-chrome-kit/` | Copy `_assets` from `квантовый-художник-*`, `янтра-*`, `коды-*`, `клуб-*`, `мастер-*`, or any other course polish |
| Treat `квантовый-художник-зима-25-эталон` as **visual/read-only** reference | Use эталон or another course as a file donor for CSS/JS |
| After sync: `rg 'Квантовый' <slug>-polish/_assets/` must be empty (except never leave hardcodes) | Hardcode any course title in shared JS/CSS |

**Step 0.5 (before Phase 0 content edits):** sync kit → `*-polish/_assets/`:
- `course-ui.css` (overwrite) — video-player overlay/poster/`Смотреть`, `.note`, `.btn-cta`, materials panels, lightbox, figure grids
- `course-lightbox.js` — required when lessons get `a.zoom` + `#lightbox`

Without kit CSS, dual-host players stack as an extra “frame”. Nav CSS/JS sync is owned by `course-toc-nav`.

## Pipeline (fixed order)

```
1. Archive build + zip          → getcourse-offline-archive (lean capture)
2. UX-аудит (optional)          → short verdict / canvas; no edits yet
3. Copy course → …-polish/      → never edit archive in place for polish
3.5 Sync chrome-kit → _assets   → course-ui.css from _course-chrome-kit ONLY
4. Phase 0 — structure-normalize → players, CTAs, panels, .note, zoom (MUST)
5. Пасс разметки                → fake headings, lists, CAPS, emoji
6. Zip after markup (optional)
7. Пасс читаемости              → walls, tips, stages, language, hierarchy
8. Zip polish                   → ask user, then build (name = current slug)
9. Exit parity spot-check       → 3–5 lessons vs archive + Phase 0 checklist
10. TOC / nav (optional)        → course-toc-nav (kit nav + JSON for THIS course)
```

Risk framing for **пасс читаемости**: medium risk to *rhythm*; **zero** risk to meaning/voice — no paraphrase.

## Magic phrase (remember this)

User says: **«причеши курс»** (or «полный апгрейд курса»).

Means — on the existing offline folder (prefer `*-polish` copy; create it from archive if missing), **in order, no live scrape**:

1. **Sync chrome-kit** into `_assets` (this skill + toc-nav)  
2. **Phase 0** (this skill): full packaging inventory below  
3. **Пасс разметки + пасс читаемости** (this skill)  
4. **`course-toc-nav`**: оглавление / сайдбар / sticky (brand = this course `title`)  

Do **not** re-download from GetCourse unless they also say «собери заново» / «с сайта». Ask before zip.

Same phrase is listed in `getcourse-offline-archive` and `course-toc-nav` so any of the three skills can catch it.

## When to use which skill

| Ask | Skill |
|-----|--------|
| **«Причеши курс»** / полный апгрейд | Phase 0 → разметка → читаемость → toc-nav (no scrape) |
| Собрать / скачать / архив / zip / GetCourse | `getcourse-offline-archive` |
| Плееры, CTA, panels, задания `.note`, packaging | **this skill (Phase 0)** |
| Стены текста, tip fatigue, орфография, пасс читаемости | **this skill** |
| Оглавление, сайдбар, TOC, «где я в курсе» | `course-toc-nav` |

## Working copy

```
уроки/<slug>/           # archive — content parity with live (chrome-incomplete OK)
уроки/<slug>-polish/    # packaging + readability playground (legacy: *-option3)
уроки/_course-chrome-kit/  # ONLY donor for shared course-ui / course-nav chrome
```

Visual look reference (read-only, never copy `_assets` from here):  
`уроки/квантовый-художник-зима-25-эталон/`  

Shared UI on the polish copy: `_assets/course-ui.css` synced from the kit (`.tip-group`, `.howto-steps`, `.video-stage`, `.materials-panel`, `.video-player`, …).

## Parity / QA checkpoints

Parity = **meaning and composition** vs the archive (or live if needed) — not “looks prettier.” Beauty work must never silently rewrite author voice. If content drifted, **restore from the archive sibling** and redo carefully.

Not full page-by-page every time: checklist + selective QA (typically 3–5 lessons).

### Before polish (entry)

- [ ] Working folder is the **`*-polish` / `*-option3` copy**, not the archive
- [ ] Copy matches archive at start (diff or spot-check) — same text, block order, video/file/external links
- [ ] Edits happen **only** on the copy; archive stays the restore source
- [ ] Note any user **«do not touch»** lessons / sections and leave them alone
- [ ] Expect archive to be **link-card / bare-URL** style — Phase 0 is required, not optional

### After Phase 0 (packaging gate)

- [ ] No stacked Rutube+YouTube cards for one part; each part has `.video-player` + poster + overlay title; switch when both hosts exist; no autoplay
- [ ] Course Telegram / Pinterest channel controls → burgundy `.btn-cta`; leftover «Канал курса:» labels removed
- [ ] Standalone actions («смотреть тут» / «открыть…») → `.btn-cta` in `.cta-wrap`, not `ext-link` chips
- [ ] Telegram **posts** → `link-actions` + `btn-ext` (not bare URL walls)
- [ ] Materials / stages → `.materials-panel` / `.stages-panel`; supplies **photo inside** `.materials-split` when it exists
- [ ] Assignments → `.note` + «Задание» + real `<ol>` / `<ul>`
- [ ] Content images: `a.zoom` + `#lightbox` + `course-lightbox.js`; schemas/covers per image-layout rules (`.figure-books` / contain — not `--fill` crop for covers)
- [ ] Shared `_assets/course-ui.css` (+ lightbox JS) came from `_course-chrome-kit/` and covers new classes
- [ ] `_assets` has no foreign course brand strings (e.g. leftover «Квантовый художник» in JS)

### End of polish (exit)

- [ ] Spot-check **3–5 lessons** (mix text-heavy + media-heavy) vs archive (and live if unsure)
- [ ] Author wording intact — no paraphrase, no invented/deleted meaning
- [ ] Block **order** still matches archive (intro → body → videos/files → assignment)
- [ ] **Video / file / zoom / external** links still work; nothing dropped by bulk scripts
- [ ] Phase 0 checklist still green on sampled lessons
- [ ] **No narrative slab** on text-heavy intros (structure visible in one viewport)
- [ ] Long sections use `.lesson-block` (+ alt); no ornament spam between every block
- [ ] Short lists → `.topics-list` / `.prep-steps`; one practice’s steps → one `.step-cluster`
- [ ] MK notes before videos; overlays = short titles only; shop/music → short `.ext-link` (not raw URL / not CTA spam)
- [ ] No emoji as list/section markers
- [ ] «Do not touch» list honored
- [ ] Chrome still kit-sourced; no `_assets` copied from another course mid-pass
- [ ] If drift found → restore those lessons from archive, then re-apply Phase 0 + polish narrowly

After polish (optional): оглавление / сайдбар → **`course-toc-nav`** (nav must not rewrite lesson bodies).

## How to view (чтобы не «пропали» правки)

1. **Открывать папку polish**, не архив без суффикса.
2. **Не полагаться на `file://` для видео.** YouTube в `file://` часто ломается (Error 153). Поднять локальный сервер из корня курса:
   ```bash
   cd "…/уроки/<slug>-polish" && python3 -m http.server 8766
   ```
   Смотреть: `http://127.0.0.1:8766/…/урок.html`
3. После правок дать пользователю **точный путь или URL**, не «открой курс».

## Visual reference

- **Look & chrome:** `_course-chrome-kit/` + read-only эталон (layout, fields, block types).
- **`_design-lab.html`** in a course folder (if present) is a **sample page**, not lesson content — rhythm/block types only. Do **not** copy design-lab text into lessons.
- Do **not** open another course’s `_assets` to “borrow” CSS/JS.

## UX audit (before heavy edits)

When the user asks for an audit only:

- Skim index + design-lab + a sample of text-heavy and MK lessons
- Deliver a short verdict: what’s strong, what’s broken, priority order
- **No edits** until they choose passes
- Typical findings feed the anti-pattern list below — include Phase 0 gaps (link-only video, bare Telegram, bare «Задание»)

---

## Phase 0 — structure-normalize (MUST)

**Do this before** accordion / tip regroup / walls polish. Do **not** wrap broken link-cards in `.video-stage-acc` and call packaging done.

Copy `course-ui.css` from `_course-chrome-kit/` into the polish `_assets/` if the course still has archive-only styles. **Never** copy CSS from another course polish/archive tree.

### 1. Video packaging

| Broken (fail QA) | Required |
|------------------|----------|
| Two stacked `<article class="video">` for one part (Rutube + YouTube link-cards) | **One** `<article class="video">` with `.video-player` + poster + `.video-overlay` title + `.video-switch` |
| Title is bare «Rutube» / «YouTube» or duplicate `h4` | Real part title **once** on `.video-overlay` |
| Empty dark hole, no poster | `.video-poster` (YouTube `i.ytimg.com/vi/ID/hqdefault.jpg` or Rutube thumbnail API) |
| «Откройте запись по ссылке» as only body line | Remove filler; player + switch enough |
| Autoplay / live `iframe src` at parse time | `about:blank` until «Смотреть»; `autoplay=0`; strip `autoplay` from `allow` |
| YouTube Error 153 | `youtube-nocookie.com/embed/…` + `referrerpolicy="strict-origin-when-cross-origin"`; prefer local HTTP |

```html
<article class="video"
  data-rutube="https://rutube.ru/play/embed/…"
  data-youtube="https://www.youtube.com/watch?v=…">
  <div class="video-badge">Видео</div>
  <div class="video-player is-pending">
    <div class="video-overlay">Обзор материалов курса</div>
    <img class="video-poster" src="https://i.ytimg.com/vi/ID/hqdefault.jpg" alt=""/>
    <iframe src="about:blank" title="Обзор материалов курса" allowfullscreen loading="lazy"
      referrerpolicy="strict-origin-when-cross-origin"></iframe>
  </div>
  <div class="video-switch">
    <button type="button" class="btn btn-primary is-active" data-player="rutube">Смотреть на Rutube</button>
    <button type="button" class="btn btn-primary" data-player="youtube">Смотреть на YouTube</button>
  </div>
</article>
```

Include page/shared script that maps `data-player` → embed URL and toggles `.is-active`. Only **after** dual-host merge: add `h3.video-stage` and `.video-stage-acc` per accordion policy below.

### 2. Telegram / Pinterest / external CTAs

| Broken | Required |
|--------|----------|
| Bare `https://t.me/…` as visible URL for **channel** | Burgundy `.btn-cta` («Канал курса» / «Канал клуба») in `.cta-wrap` |
| Empty leftover «Канал курса:» / «Канал курса: Telegram» | Remove label text once button exists |
| Telegram treated as FILE card | Replace with `.btn-cta` or post `btn-ext` |
| Bare `pin.it` / Pinterest URL | `.btn-cta` («Доска в Pinterest» / short board label) |
| Telegram **posts** / «открой статью» as raw links | `<p class="link-actions">` + `a.btn.btn-ext` |
| Long marketplace URLs as visible text | Short anchors («ссылка на ВБ») via `a.ext-link` / `.shop-links` |

```html
<div class="cta-wrap">
  <a class="btn-cta" href="https://t.me/…" target="_blank" rel="noopener">Канал курса</a>
</div>
```

Style: background `#a61b3d`, color `#eaebd8`, `border-radius: 0`. Place where the live lesson had the channel control.

### 3. Materials packaging

| Broken | Required |
|--------|----------|
| «Необходимые материалы» / list as `-` / `1 …` paragraphs | `.materials-panel` + `.materials-list` |
| Photo + list stacked vertically | `.materials-split` / `.materials-row` — **one row** photo \| list |
| Materials photo as a lone `<figure>` **below** the panel | Move that figure **into** `.materials-split` (first column); never leave supplies photo under a long list |
| Hero photo + wall of prose + lone `.file` | Structure as materials / overview lesson (variants below) |
| Shop URL walls | `<ol class="shop-links">` + short `a.ext-link` labels |

**Variants:**

1. **List + photo (MK supplies)** — `.materials-panel` + `.materials-split` (photo | numbered list). Title centered.
2. **Overview / download lesson** — not a flat card dump:
   - Keep hero as `.figure` (zoom) **or** inside `.materials-panel` if it is the materials shot
   - Guidance → short intro + `.topics` / `.note` for caveats
   - File CTA stays a real `.file` **after** guidance (live order)
3. Never leave materials guidance as 4–6 equal paragraphs with no hierarchy when the lesson’s job is “what to buy / download”.

```html
<div class="materials-panel">
  <h3>Материалы для этой работы</h3>
  <div class="materials-split">
    <figure class="figure">…</figure>
    <ol class="materials-list">…</ol>
  </div>
</div>
```

### 4. Work stages

«Этапы работы» (and similar) → `.stages-panel` + `<ol class="stages-list">`. Never leave as `1. 2. 3.` paragraphs.

```html
<div class="stages-panel">
  <h3>Этапы работы</h3>
  <ol class="stages-list">
    <li>Набросать эскиз в скетчбуке</li>
    <li>Акварельная заливка</li>
  </ol>
</div>
```

### 5. Assignments (`.note`)

| Broken | Required |
|--------|----------|
| Bare `<h3>Задание</h3>` + loose `<p>` / numbered paragraphs | Green `.note` callout + `<strong>Задание</strong>` + real `<ol>` / `<ul>` |
| One paragraph `1. … 2. … 3. …` | Split into list items |
| Homework wrapped only in `.howto-steps` without `.note` when it is the lesson assignment | Prefer `.note` for «Задание» blocks |

```html
<div class="note">
  <strong>Задание</strong>
  <ol>
    <li>…</li>
    <li>…</li>
  </ol>
</div>
```

Strip GetCourse submit chrome; keep author homework text. Assignments stay **fully visible** (do not accordion).

### 6. Files, zoom, image layout, shared CSS

- Keep each `.file` in **live / archive order** (do not dump all files at top while packaging)
- **Content images** → wrap in `<a class="zoom" href="…">` + page `#lightbox` + script `course-lightbox.js` from the kit (exception: `.video-poster`)
- Prefer shared `_assets/course-ui.css` over per-lesson CSS dumps
- Illustrated essays (dragons): `.dragon-block` + floated `.dragon-illust` as needed — still no voice rewrite

**Sizing / grids (Phase 0 QA):**

| Case | Required |
|------|----------|
| Square / diagram **схема** | `.figure.figure-schema` (centered, soft max-width ~480px) |
| Finished work / tall example | `.figure.figure-finished` (soft max-width ~640px) when alone, not in a grid |
| Two related diagrams side by side | `.figure-row-2` (+ zoom) |
| Three+ equal gallery cells | `.figure-row-3` with **`object-fit: contain`** |
| Book / cover grids | `.figure-books` — cells with **`object-fit: contain`**; **never** `.figure-row-3--fill` + `cover` (crops covers) |
| Asset is a **dual/composite screenshot** (two covers glued, GC chrome) | Crop to the single relevant subject before shipping; don’t “fix” a double image |

`--fill` + `cover` is only for intentional uniform photo tiles (mixed art examples), not documents or book covers.

### 7. «Полотно» ≠ only long `<p>` (early hierarchy)

Char-split alone is **not** done. Fail QA if a lesson opens with a long fairy-tale run that is still hard to scan: many similar-length paragraphs, **no** `h3` / `.note` / tip / figure break for a screenful or more.

| Do | Don’t |
|----|--------|
| Split `<p>` ≫ ~360–420 chars on sentence boundaries | Invent section titles the author never wrote |
| Promote **author’s own** title-like lines to `<h3>` | Paraphrase into “easy mode” |
| Insert existing ornaments / figures as breathing room | Accordion the intro narrative |

**Done for walls:** structure visible within one viewport — not a cream brick of same-weight paragraphs.

---

## Anti-patterns (scan for these)

- Fake headings: title-like short `<p>` instead of `<h3>`
- Bullet-as-paragraph: `•` / `⁃` inside one `<p>` instead of `<ul>`/`<ol>`
- CAPS shout in headings or body — use sentence case + weight
- ✔ / emoji spam in body (including emoji as list/section markers)
- Tip fatigue: long stacks of identical cream `.tip-card` with no groups
- Orphan tips: `.tip-card` outside `.tip-list` / broken nesting
- Short one-liner lists forced into accordion / howto chrome / `.compact-cols` instead of `.topics-list` / `.prep-steps`
- Same ornament figure repeated between every subsection (prefer `.lesson-block` alternating cream/mint)
- One practice’s steps split into separate practice cards with duplicate numbers
- MK notes after videos; lesson prose dumped into `.video-overlay`
- Raw marketplace/music URLs as visible text (use short `.ext-link`)
- Every external link inflated to burgundy `.btn-cta`
- **Legacy dual video cards** / link-only video without `.video-player` + poster
- Bare Telegram channel URL / missing `.btn-cta`
- Action «смотреть тут» left as `ext-link` chip instead of `.btn-cta`
- Materials photo outside `.materials-split` / book covers cropped by `--fill` + `cover`
- Assignment left as bare `h3` + paragraphs (no `.note`)
- Video stacks with empty gaps and no stage titles
- Assignment / requirements as one paragraph `1 … 2 … 3 …`
- Walls: single `<p>` ≫ ~400 characters with several sentences
- **Narrative slab:** many mid-length `<p>` in a row, no heading/figure/note for a full viewport
- Materials overview left as photo + prose brick + file with no panel / topics / note hierarchy
- Adjacent duplicate / near-duplicate `<h3>`
- Encyclopedia blocks: color name + prose + perceptions as one wall — use `h4` + `.color-entry` + list

## Пасс разметки

Fix markup that lies or shouts — do not paraphrase:

- **First:** Phase 0 must be done (or do it now) — video merge, CTAs, materials/stages, `.note`, zoom
- Fake titles → `<h3>`; `•` lines → real lists
- CAPS → sentence case (keep real acronyms)
- Strip body emoji spam; **do not** re-add emoji as section/list markers later
- Split merged titles («Урок 1Урок 2»)
- Prefer shared `_assets/course-ui.css` over per-lesson tip CSS dumps

## Пасс читаемости

### Walls of text

- Split long `<p>` on sentence boundaries (~360–420 chars as a guide)
- Then apply **narrative slab** rule — char-split alone is insufficient
- **Never** invent, delete, or rephrase words
- After bulk splits: check `</p>` / div balance; fix stubborn joins **in place**

### Tip fatigue

- Numbered tip walls → `<ol class="howto-steps">`
- Conceptual stacks → `.tip-group` + label + `.tip-list` / `.tip-list--plain`
- Restore truncated `…` tip titles from full body line when the title was cut
- Dedupe title vs body **only** on exact repeat; never blind-trim into `. Даже если`

### Video stage anchors

- **Prerequisite:** each part is already one packaged player (Phase 0), not two host cards
- Empty gaps between videos → `<h3 class="video-stage">` from real oEmbed / part titles
- Skip if an `h3` already anchors the part

### Language polish

- Obvious typos/agreement/spacing only; no literary edit
- Leave jargon/brands; `Что бы вы…` ≠ `Чтобы`
- Edit only inside `<section class="card">`
- Speller OK + manual accept/reject

### Hierarchy

- Stronger `.tip-group-label` where stacks feel flat
- **Do not** use emoji as section/list markers (no ✔ / 🌿 / numbered emoji). Weight + headings carry hierarchy
- Pull lagging lessons toward design-lab / diag rhythm **without inventing content**
- Dragon-style essays: keep `.dragon-block`; don’t drop exercises when regrouping

### Sections / walls (framed blocks)

Long theory, сказ, or multi-part narrative → split into `.lesson-block` (+ alternate cream/mint via `.lesson-block--alt`). One purpose per block: one headline + short support.

| Do | Don’t |
|----|--------|
| Alternate `.lesson-block` / `.lesson-block--alt` for scan rhythm | Dump the same ornament figure between every subsection |
| Split walls into several framed blocks | One cream brick for a whole сказ/theory |
| Keep author headings; promote title-like lines to `h3` | Invent decorative section titles |

### Short lists

| Case | Use |
|------|-----|
| Short topic / «о чём» / prep bullets (3–8 one-liners) | `.topics-list` or `.prep-steps` |
| Numbered how-to with real explanation per step | `.howto-steps` / `.lesson-steps` |
| Parallel practice **steps inside one practice** | One practice card + inner `.step-cluster` (+ `--howto`) |

**Do not** turn short one-liners into accordion tip-cards, fat howto chrome, or wide `.compact-cols` when a quiet list is enough.

### One practice = one card

- Several «Шаг 1 / 2 / 3» of the **same** practice stay **inside one** practice block (`.step-cluster`), not as separate practice cards that renumber independently
- Notes / prep for an MK sit **before** the video stack; do not bury them after players
- Video `.video-overlay` = **short real part title only** — no dumped lesson paragraphs in the overlay

### External links (shop-style)

| Case | Required |
|------|----------|
| Marketplace / music / reference URL in body | Short `a.ext-link` label («Открыть на…», «Яндекс Музыка») — **not** raw URL text |
| Channel / board CTA | Burgundy `.btn-cta` |
| Standalone action after intro | `.btn-cta` — **not** an `ext-link` chip disguised as CTA |

Do **not** inflate every external href into a fat `.btn-cta`.

### Accordions (multi-open) — policy

Use native `<details class="course-acc">` (**never** exclusive JS that closes the previous row).

**Default open/closed:**

| Kind | Default | Notes |
|------|---------|--------|
| **Video stage** (`.video-stage-acc`), 3+ stack | **First open**, rest closed | Continuous stack |
| **Video stage**, adjacent pair (2, no gap) | **Both open** | Nothing between the two stages |
| **All other** encyclopedias / chapters / lesson-acc | **All closed** | Reader opens what they need |
| Multiple open at once | **Allowed** | Native multi-open; do not force single-open |

**Use when** the block is a **catalog / glossary / long parallel sections** the reader will sample:

| Yes | Examples |
|-----|----------|
| Color / term encyclopedias with long bodies | Lesson 11 color guide (+ `.color-swatch`) |
| Long parallel chapters | Dragons 1–4 (one accordion per dragon; exercises stay outside) |
| 5+ parallel composition schemes with detail | e.g. lesson 12 «Типы квантовых схем» |
| Big how-to chapters under one theme | e.g. lesson 26 lesson-blocks (optional) |

**Catalog grid (not accordion)** — short equal items to scan at a glance:

- Use `.catalog-grid` + `.catalog-card` (2 columns on desktop)
- Prefer this over numbered `.tip-card` strips when each item is 1–2 sentences
- **Do not** accordion these

**Lesson steps (not accordion)** — sequential «Шаг 1 / 2 / 3»:

- Use `.lesson-steps` + `.lesson-step` (same idea as lesson 14 `.diag-step`)
- Prefer this over bare `h3` + hairline only
- Only when there are clear parallel steps

**Do not accordion when:**

| No | Why |
|----|-----|
| Already balanced card / catalog grid | Lesson 14 diag; `.catalog-grid` props |
| Short tip groups / howto 3–4 steps | Click cost > benefit |
| Intro narrative, **assignments (`.note`)**, **materials panels** | Must stay fully visible |
| **Video players themselves** | Don’t restyle/shrink the player chrome |

**Video stacks (МК with many parts):**

1. Keep the **player UI as-is** (badge, poster, Смотреть, Rutube/YouTube).
2. Always keep **stage titles** (`h3.video-stage` / existing part titles).
3. Wrap stages in `<details class="course-acc video-stage-acc">`:

| Situation | What to do |
|-----------|------------|
| **3+ videos** in one continuous stack | Accordion each stage; **first open**, rest closed |
| **Exactly 2 videos** with **nothing between** | Accordion both; **both open** by default |
| **2 videos with a real gap** | Separate blocks — do not force one pair across the gap |
| Example image between two lesson videos | Move example **before** the first video; style as `.example-figure` |

4. Do **not** put all videos inside one accordion body; do **not** hide materials/intro inside video accordions.

**External / shop / action links** (also part of Phase 0 when scanning):

| Case | Required |
|------|----------|
| Inline shop/mention («ссылка на ВБ») | `a.ext-link` — small green chip, stays **inline** |
| Shop list | `<ol class="shop-links">` → each `<li>` = name + `a.ext-link`. **Never** bare `<a>` as direct children of `.shop-links` |
| Channel / board CTA (Telegram, Pinterest) | Burgundy `.btn-cta` in `.cta-wrap` |
| Standalone **action** («смотреть тут», «открыть разбор», «перейти к статье») after a short intro | `.btn-cta` in `.cta-wrap` (or under the sentence) — **not** an `ext-link` chip |
| Secondary post-style actions | `<p class="link-actions">` + `a.btn.btn-ext` |

```html
… — <a class="ext-link" href="…" target="_blank" rel="noopener">ссылка на ВБ</a>

<ol class="shop-links">
  <li><span class="shop-item-name">Холст 40×40</span>
      <a class="ext-link" href="…" target="_blank" rel="noopener">ссылка на ВБ</a></li>
</ol>

<p>Интересный разбор символа:</p>
<p class="cta-wrap"><a class="btn-cta" href="…" target="_blank" rel="noopener">Смотреть тут</a></p>

<p class="link-actions">
  <a class="btn btn-ext" href="https://t.me/…" target="_blank" rel="noopener">Открыть пост в Telegram</a>
</p>
```
**Color swatches:** `.color-swatch` + `--swatch:#hex`, not emoji.

**Compact / tile lists:**

- Short one-liners: quiet `.compact-cols` (2-col), **not** `.catalog-grid` for one-word effects
- Short keyword associations: `.assoc-chips`
- Long idea lists: `.idea-list` — **never** pill tags
- Parallel step groups: `.step-cluster` (+ `--howto`) with label; flatten heavy step-card chrome inside
- Long rule walls: `.lesson-acc` all closed + short `acc-hint`
- «Попробуй почувствовать» with tip + figure-row: **no** outer nested card around figures
- Dragon lessons: one `.dragon-acc` per dragon (all closed); tip-groups inside; exercises **after all** dragon accordions; `acc-hint` that dragons are expandable

## Pitfalls (stop list)

- Blind tip-title trim → corrupted openings (`. Даже если`)
- Regrouping tips → lost exercises / notes (restore from archive sibling)
- Regex wall-split that captures past `</p>` → duplicate blocks, missing video, wrong order
- Editing archive folder while user views `*-polish` (or the reverse)
- Treating design-lab as content to paste into lessons
- Shipping accordion / walls **on top of** Phase 0 debt (link-cards, bare Telegram, bare «Задание»)
- Copying `_assets` from another course (brand leak / stale 720px nav / wrong JSON)

After any bulk script: div balance = 0, no `</p<div`, videos/`Смотреть` still there, intro → sections → assignment order intact. If corrupted → restore from non-polish sibling and redo carefully.

## Zip polish

When the user asks for a zip of the readable copy:

- Build UTF-8 zip (same Python `zipfile` pattern as archive skill)
- Name from **this** course slug: e.g. `<slug>-polish.zip` / `Янтра-…-polish.zip` — not a hardcoded winter-KH example
- Ask before overwriting an existing zip
- Exclude `.DS_Store`, `_data/` raw dumps if huge and unused offline, and editor junk
- Exclude `_data/`, `_tmp_*`, `.DS_Store`
- Do **not** overwrite the archive zip unless asked

## Done criteria

- Correct folder opened via local HTTP when checking video
- **Phase 0 cleared** on agreed scope (players, CTAs, materials/stages, `.note`, zoom)
- Markup anti-patterns cleared on agreed scope
- Tips/walls/stages readable; no narrative slabs; author words preserved
- Language cleaned lightly; voice intact
- Exit parity checklist passed (spot-check vs archive)
- Polish zip delivered if requested

## Known failure mode (do not repeat)

Shipping accordion + char-split polish **on top of** lean archive chrome (two dark «Rutube»/«YouTube» link-cards, bare `t.me`, `h3` Задание without `.note`, materials as prose brick). Folder name says `*-polish` but Phase 0 never ran. **Fix Phase 0 first**; then accordion / walls / toc-nav.
