---
name: course-toc-nav
description: >-
  Redesigns offline course table of contents and in-lesson navigation: typed
  TOC cards with one-line blurbs, left sidebar (mobile drawer), sticky
  prev/next with lesson numbers, plus href/type parity QA after wiring. Use when
  the user asks for оглавление, навигация, сайдбар, TOC, «где я в курсе», or
  course index UX. Also the last step when the user says «причеши курс» /
  «полный апгрейд курса» (after course-html-polish Phase 0 packaging + markup
  + readability). Do NOT use alone for lesson content polish or Phase 0
  packaging (course-html-polish) or GetCourse archive builds
  (getcourse-offline-archive).
---

# Course TOC & navigation

Project hub: `AGENTS.md` · siblings: `course-html-polish` (before this), `course-local-video`, `course-fin-sd`. Prefer `num` like `"6.1"` when the course uses chapter.lesson numbering.

## Hard exclusion — эталон

**Never** wire or rewrite nav inside `*-эталон` folders. They are locked canons. Run toc-nav only on `*-polish` / `*-option3` (or a fresh copy). Snapshots in `_skills-frozen/` are not executable against the эталон.

## Chrome source (MUST)

Donor for nav CSS/JS:

`уроки/_course-chrome-kit/`

1. Copy `course-nav.css` + `course-nav.js` from the kit into the target `*-polish/_assets/` (overwrite).
2. **Build `course-nav.json` from scratch** for **this** course (modules, hrefs, titles, types, blurbs; module `label` + `name` + optional short `subtitle`). Use `course-nav.json.example` only as schema.
3. Sidebar brand must come from `data.title` in that JSON (`course-nav.js` in the kit already does this). **Never** hardcode a course name in JS.
4. Kit nav defaults (do not revert): crumb first link = `title` (not bare «Курс»); sticky prev/next **aligned to the readable field** (`.wrap` / `--read-max`); module `label` + `name` (+ `subtitle`) styled prominently; click on module title → first lesson of that module.

**Forbidden:** copying `_assets/course-nav.*` or `course-nav.json` from `квантовый-художник-*`, `янтра-*`, `коды-*`, or any other course and “tweaking” it. Layout width/gutters come from kit CSS (do **not** reintroduce `max-width:720` overrides).

Improve **orientation** in an offline HTML course: clearer index, persistent lesson picker, secondary prev/next.

Works on a **polish** (or `*-option3`) copy **after** Phase 0 packaging and readability passes. Shared files live in `_assets/`. Pipeline: archive (lean capture) → polish (Phase 0 → разметка → читаемость) → **toc-nav (this)**.

This skill does **not** own video players, CTAs, materials panels, assignment `.note`, or lesson-body readability — that is `course-html-polish`. Do not start toc-nav until Phase 0 is done on the copy (or the user explicitly wants nav-only on an already-packaged tree).

## Order of work

```
1. Sync kit → _assets/course-nav.css + course-nav.js
2. Inventory lessons from THIS tree (folders / index / h1)
3. Write course-nav.json (title = real course name; total = N)
4. Wire body.course-app, data-lesson-id, link/script tags
5. Rebuild index.html TOC from the same JSON
6. QA checklist below
```

## Deliverable mix (default)

1. **Left sidebar (desktop)** — module → lessons tree; current lesson highlighted. **Mobile** — «Содержание» opens a drawer. Brand line = `json.title`.
2. **Redesigned `index.html`** — each lesson: **type chip** + **one-line blurb**; each module: short stage subtitle. Hide archive counters (`видео: N · изобр.`) from the first screen.
3. **Sticky bottom bar** — prev / «N / total» / next, aligned to the **edges of the readable content column** (same width as `.wrap` / `--read-max` ≈ 54rem), not flush to the viewport and not jammed in a tight center cluster. Titles on larger screens. Quiet link to the index.

Do **not** build a full-screen “course map” unless asked. Do **not** rewrite lesson bodies for nav.

## Lesson types

| `type` | Label (RU) | When |
|--------|------------|------|
| `intro` | Старт | Rules, welcome |
| `materials` | Материалы | Supplies, tools lists |
| `practice` | Практика | MK / painting demos |
| `theory` | Теория | Concepts, frameworks |
| `live` | Эфир | Live/stream replays |
| `tools` | Инструменты | Tips, care, AI, constructors |
| `assignment` | Задание | Final / homework |
| `photo` | Фото | Photo module |
| `wrap` | Финал | Certificate, closing |

Store curated types + blurbs in `_assets/course-nav.json` (paths relative to course root).

## Files

```
_assets/course-nav.json   # modules, lessons, type, blurb
_assets/course-nav.css
_assets/course-nav.js
index.html                # rich TOC from the same JSON (or generated HTML matching it)
```

Each lesson page:

- `body.course-app` (or wrapper)
- link `course-nav.css` + script `course-nav.js` (path relative to lesson depth)
- `data-lesson-id` on `<body>` = filename stem (e.g. `11-buystvo-tsveta`)
- Soften or remove competing `.nav-lessons` chrome (sidebar + sticky replace it); keep crumb lightly
- Crumb first link text = `course-nav.json` → `title` (kit `patchCrumb` + prefer the same string in static HTML so it shows before JS). Never leave bare «Курс» on lesson/module pages when the JSON title is known

## Index rules

- Module **`label`** (week / stage chip) + thematic **`name`** + optional short **`subtitle`** (kit: `.toc-module-label` / `.toc-module-name` / `.toc-module-sub`)
- Lesson card: number, title, type chip, blurb — **no** file/video counts on the card face
- Same visual language as lesson UI (Cormorant / Manrope, cream, green accent) — not a new brand

## Sidebar rules

- Brand `<strong>` text = `course-nav.json` → `title` only (fallback «Курс»)
- Each module: `label` + **`name`** (thematic title) + optional `subtitle` — **noticeable** (kit `.side-mod-lab` / `.side-mod-name` / `.course-side-mod-sub`). Do not ship pale tiny grey blurbs that disappear against the cream sidebar
- **Click module title/stack → first lesson** of that module (kit `.side-mod-link` / `firstHref`)
- Multi-open modules OK; **current module expanded** by default
- Current lesson: strong highlight + `aria-current="page"`
- Root-relative links resolved via script location (`…/_assets/course-nav.js` → course root)
- Drawer: focus trap light; close on Escape / backdrop / link navigate

## Sticky prev/next

- Always show lesson **numbers** (`11 / 42`)
- Show neighbor **titles** from `md` breakpoint up
- First/last: disable or hide the missing side
- Layout: full-bleed bar; inner grid **`1fr auto 1fr`** with horizontal padding so content matches `.wrap` / `--read-max` — prev at the **left edge of the readable field**, next at the **right edge**, counter centered. Do **not** pin to the viewport edges; do **not** jam into a narrow center cluster (avoid capped `14.5rem` columns that look “слипшимися”)

## Pitfalls

- Don’t duplicate a second full TOC inside every lesson body
- Don’t break `file://` — no absolute `/` paths; resolve from script/href
- After renaming lessons, regenerate `course-nav.json` and re-inject `data-lesson-id`
- Keep polish zip rebuild optional; ask before zipping
- Never rewrite lesson body copy while wiring nav — restore from polish/archive if a script touched content
- Never copy another course’s nav JSON/JS (brand leak)
- Never shrink the reading column back to 720px — kit CSS owns gutters/max-width

## Parity / QA checkpoints

Nav work is orientation only. Parity here = **every link reaches a real lesson**, metadata matches the course, and **lesson body content is unchanged**. Beauty of the TOC is not content parity.

Not a full page-by-page content audit — verify wiring + spot-check that bodies were not edited.

### After toc-nav

- [ ] All **N** lesson `href`s in `course-nav.json` / index / sidebar resolve (no 404s, no stale stems)
- [ ] **Types and titles** match the actual lessons (chip + blurb align with lesson purpose/title)
- [ ] Lesson **body** content unchanged by nav injection (diff vs pre-nav polish copy if unsure)
- [ ] `data-lesson-id` matches filename stems; current lesson highlights correctly
- [ ] Prev/next cover first→last without gaps or wrong neighbors
- [ ] Sidebar brand equals `course-nav.json` `title` (not another course’s name)
- [ ] Crumb first link shows the same `title` (not bare «Курс»)
- [ ] Module `label` + `name` (+ `subtitle`) visible in sidebar + index; click module → first lesson
- [ ] Sticky prev/next align to readable-field edges (`--read-max`), not viewport edges and not a tight center clump
- [ ] `rg 'Квантовый' _assets/course-nav.js` (and CSS) is empty unless *this* course title genuinely contains that word
- [ ] Reading column uses kit gutters (not a skinny centered strip)

## Done criteria

- Started only after polish Phase 0 + readability (unless user asked nav-only)
- Nav CSS/JS synced from `_course-chrome-kit/`; JSON built for this course only
- From any lesson, user can jump to any other via sidebar/drawer
- Index answers “what is this lesson?” without opening it
- Prev/next never the only way to move more than one step
- Mobile usable (drawer, sticky bar not covering CTAs awkwardly — pad `main` bottom)
- Post-nav parity checklist passed (hrefs, types/titles, bodies untouched, brand OK)
