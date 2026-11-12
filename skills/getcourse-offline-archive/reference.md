---
name: getcourse-offline-archive-reference
---

# Reference — GetCourse offline archive (lean capture)

Packaging chrome (players, `.btn-cta`, `.materials-panel`, `.note`, lightbox, shop chips) lives in **`course-html-polish` Phase 0** — not here. This file is for **parity scrape + dumb HTML**.

## House rules (archive only)

1. **VPN** — strip every VPN / «дублирует видео…» note; keep Rutube + YouTube URLs without those lines
2. **Video titles** — real part/title from live player or YouTube oEmbed (not bare «Rutube» / «YouTube»)
3. **Both hosts** — if live has Rutube and YouTube for one part, keep **both URLs** in the archive (two link buttons or two link-cards OK)
4. **Full text** — every live paragraph (intros, notes under video, assignment text) must appear offline
5. **Document order** — extract blocks in live page order; images/files stay between surrounding text
6. **Parse all lesson block types** — `lt-lesson-image`, `lt-lesson-files`, `lt-lesson-text`, `lt-lesson-header`, `lt-image-caption`, … (exact class tokens; don’t false-nest on `o-lt-*`)
7. **No platform chrome** — no comments, review forms, assignment submit UI, JSON widgets; do not scrape comment/answer images as lesson art
8. **External links** — preserve `t.me`, `pin.it`, marketplace hrefs with nearby labels (ugly bare URLs OK)
9. **Ornaments** — keep practice divider images; do not skip or collapse repeats
10. **Authors** — no duplicate attribution in `figcaption` if already on/above the image
11. **TOC** — module label + lessons only; no extra «открыть модуль» card
12. **No invented images** — only assets that exist on the live lesson
13. **Crooked whole lesson** — if many blocks are misplaced vs live, rebuild from ordered live blocks
14. **Audio** — download locally; simple «Скачать / слушать» card

## Live page QA checklist (parity)

When the user sends screenshots or says «проверь как на сайте»:

- [ ] Full lesson text present (not video-only when live has copy)
- [ ] Heading hierarchy matches live (Часть / Урок / МК)
- [ ] Images appear in the same places as on the live page (not only at the bottom)
- [ ] All video parts present with real titles; both hosts when live has both
- [ ] Each `.file` attachment sits where it is on the live lesson
- [ ] External links present (`t.me`, pin.it, shops) — formatting polish not required
- [ ] Audio/PDF from `.lt-lesson-files` / audio blocks present locally
- [ ] Schemes not cropped; captions accurate; no duplicate authors
- [ ] Ornaments / gallery **order** match live
- [ ] Main TOC has no duplicate module opener cards
- [ ] No VPN lines, comments, or GetCourse UI leftovers

Do **not** fail archive QA for missing `.video-player`, `.btn-cta`, `.materials-panel`, or assignment `.note`.

## Strip patterns (nav / chrome)

Remove from scraped text when safe:

- `Список тренингов…`
- `Предыдущий урок` / `Следующий урок` blocks
- `\d+ из \d+ уроков`
- `Задание выполнено` / `Доступен`
- `Отправить` / `Отзыв принят` / comment JSON
- Trailing `Бонусные уроки…` / `Для участников…`
- **All VPN lines** — any spelling. Never keep VPN hints in the offline archive

Keep real lesson greetings and body copy.

## Shared UI CSS (minimal)

Optional shared `_assets/course-ui.css` for theme helpers. **Do not** require packaging classes in the archive. Prefer linking a shared file over copying huge CSS into every lesson.

```html
<link rel="stylesheet" href="../_assets/course-ui.css"/>
```

## Audio (simple)

GetCourse audio lives in `.lt-lesson-audio` / jPlayer playlists (`mp3:` / `m4a:` URLs on `fileservice`).

1. Download under `_вложения/<lessonId>/`
2. Render a simple stacked card:

```html
<div class="audio">
  <div class="audio-top">
    <span class="audio-badge">Аудио</span>
    <span class="audio-time">19:42</span>
  </div>
  <strong>Аудио 1</strong>
  <a class="btn btn-primary" download href="…/file.ogg">Скачать / слушать</a>
</div>
```

3. Do **not** embed native `<audio controls>` / Flash chrome

## Video block template (link-card — archive)

**Dumb emit.** Title once as text; hosts as buttons/links. Fancy player → polish Phase 0.

```html
<article class="video">
  <div class="video-badge">Видео</div>
  <h3 class="video-title">Обзор материалов курса</h3>
  <p class="video-links">
    <a class="btn btn-primary" href="https://rutube.ru/video/…" target="_blank" rel="noopener">Смотреть на Rutube</a>
    <a class="btn btn-primary" href="https://www.youtube.com/watch?v=…" target="_blank" rel="noopener">Смотреть на YouTube</a>
  </p>
</article>
```

If only one host exists, one button is enough. Two stacked articles for the same part (one per host) are **acceptable archive debt** — polish merges them.

## File block template

```html
<a class="file" href="../_вложения/модуль-N/LESSON_ID/name.pdf" download>
  <div class="file-icon">PDF</div>
  <div>
    <strong>name.pdf</strong>
    <span style="display:block;color:var(--muted);font-size:.82rem">скачано локально</span>
  </div>
</a>
```

Place each `.file` where the live `lt-lesson-files` / `lt-file` block sits. Never batch-move all files to the start of `.card`.

## Figure template

```html
<figure class="figure">
  <img src="../_вложения/модуль-N/LESSON_ID/схема.png" alt="…"/>
  <figcaption>Подпись как на сайте (без повторного автора, если он уже над картиной)</figcaption>
</figure>
```

`a.zoom` + lightbox is **optional** in archive; polish Phase 0 may add it course-wide.

Practice ornaments: `.figure-ornament` between practice blocks; keep every occurrence.

## External links (preserve raw)

```html
<p>Ссылка на канал: <a href="https://t.me/…" target="_blank" rel="noopener">https://t.me/…</a></p>
```

or labeled anchors. Do **not** convert to `.btn-cta` / `btn-ext` during archive.

## Common failure modes (content loss)

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Scheme cut at bottom | Cropped screenshot | Re-download CDN original |
| Tiny / garbage «image» | 127× or HTML error page | Delete; fetch real URL from live |
| Empty / video-only lesson | Parser skipped body | Re-fetch; restore full paragraphs |
| Generic «Rutube»/«YouTube» titles | Title not scraped | oEmbed / live player title |
| Missing second host URL | Scraper kept one host only | Restore both URLs from live |
| Dropped `t.me` / shop URL | Treated as junk | Preserve external hrefs |
| `SyntaxError: Non-UTF-8` | Python heredoc, no encoding | `# -*- coding: utf-8 -*-` or Write tool |
| Zip Cyrillic names garbled | macOS `zip` CLI | Python `zipfile` + UTF-8 `arcname` |
| VPN note under videos | Live copy mentions VPN | Always strip |
| Duplicate author under painting | Caption + on-image credit | Keep one attribution |
| Missing ornaments between practices | Treated as duplicate junk | Keep each divider |
| Images only at lesson bottom | Text first, images appended | Rebuild in live document order |
| TOC shows module title three times | Label + opener + lesson 1 | Drop module opener card |
| False «missing images» from comments | Auditor counted answer-files | Exclude comment widgets |
| Empty file-only lesson | Missed `.lt-lesson-files` | Parse files block; local PDF card |
| Nested-block filter ate all content | Regex matched `o-lt-*` wrappers | Match exact class tokens only |

## Filename conventions

- Lesson HTML: `NN-translit-slug.html` (e.g. `01-chto-takoe-yantra.html`)
- Attachments: human Russian names (`схема-янтра-луны.png`)
- Keep `lessonId` folder for stable remapping when re-scraping

## After user fixes

Always rebuild the zip and confirm key HTML + PNG sizes are inside the archive.

### Comparison markers — user decisions

- If the user says to **remove** a compared variant («убираем», «2 ст убираем», etc.), delete that content **and** its mark/wrapper (`.cmp-version`, tag). Never leave an empty colored box.
- If only the mark should go («пометку снимаем»), unwrap and keep the inner text as normal body.
