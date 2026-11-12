---
name: course-local-video
description: >-
  Downloads lesson videos to local MP4 (Vimeo/YouTube via yt-dlp), wires
  <video> players, video accordions (n/N chips), and «смотрела» viewed marks
  with a bottom count bar. Use when the user asks for локальные видео, offline
  mp4, Vimeo download, video accordion, n/N, «смотрела», course-seen, or a
  course that must play without internet. Do NOT use for GetCourse scrape
  (getcourse-offline-archive), readability polish alone (course-html-polish),
  or TOC (course-toc-nav). Lean archive keeps video as URLs; this skill is the
  optional offline-video stage after polish/nav.
---

# Local video for offline courses

Pipeline position: after archive + polish (+ toc-nav preferred) → **this** → fin-sd.

Chrome donor: `уроки/_course-chrome-kit/` (`course-ui.css`, `course-video.js`, `course-seen.js`).

## When

- Mom must watch **without internet**
- User asks to download Vimeo/YouTube into `_видео/`
- Apply accordion / n/N / «смотрела» to existing local players

## Layout

```
_видео/<модуль-N__lesson-slug>/01.mp4 …
уроки/<course>/модуль-N/0x-….html   # <video src="../_видео/…">
_assets/course-seen.js              # from kit
```

## Download rules

- Tool: `yt-dlp` + `ffmpeg`. Prefer `required_permissions: ["all"]` (sandbox proxy often 403s).
- **Vimeo unlisted:** use player URL with hash:  
  `https://player.vimeo.com/video/{id}?h={hash}`  
  Referer: school host (e.g. `https://school.prostorisovat.ru/`).  
  Do not strip `?h=` before download.
- **YouTube:** needs cookies file from the user; **never** commit/copy cookies into the repo or echo them.
- Verify each file starts with `ftyp` / plays; delete `.part` leftovers.
- Do **not** re-run a whole-course downloader that overwrites finished files unless asked.
- Optional compress only if policy says so (Zentangle used a large MB cap).

## Player markup

Local player (not dual-host Rutube/YouTube cards):

```html
<details class="course-acc video-stage-acc" open>
  <summary>
    <span class="video-acc-label">
      <span class="video-pos">1/3</span>
      Title without leading "1. "
    </span>
    <!-- course-seen.js injects «смотрела» pill here -->
  </summary>
  <div class="course-acc-body">
    <h3 class="video-stage">1/3 · Title</h3>
    <article class="video video-local">
      <div class="video-player video-player-local">
        <video controls playsinline preload="metadata"
          src="../_видео/…/01.mp4" title="…"></video>
      </div>
    </article>
  </div>
</details>
```

CSS hides `.video-part`, `.video-title`, `.video-badge`, and `h3.video-stage` inside the accordion; visible name = `<summary>`.

## Accordion open/close

| Situation | Open state |
|-----------|------------|
| 3+ continuous videos in a stack | first open, rest closed |
| Exactly 2 adjacent (nothing between) | both open |
| Videos separated by real text | each own accordion, **open** |
| Assignments / `.note` | never accordion |

Do not wrap across a text gap into one accordion.

## n/N chips

- Prefix `<span class="video-pos">k/N</span>` in the summary label.
- Strip leading `N. ` / `Видео N` from the title when N equals the position (avoid «4/8 · 4. Diva…»).
- Names with a later number stay («Тени. Часть 1», «Вебинар 2»).
- Highlight chip via kit CSS (`.video-pos` — larger, gold pill). Do not invent a second n/N that looks like the viewed count.

## «Смотрела» + bottom bar

- Script: `_assets/course-seen.js` from kit, **after** `course-nav.js` (defer), on every lesson that has `video-stage-acc`.
- Checkbox pill on summary: label «смотрела»; click must `stopPropagation` (must not toggle details).
- Storage key: `zentangle-seen:` + `body[data-lesson-id]` (or course-specific prefix — keep one prefix per course).
- Bottom bar inside `.course-sticky` first row: **«Видео»** + `0 из N` + «осталось K» / «все отмечены». Show even when N=1. Use «из», not `/`.
- Persist only on `http://127.0.0.1:PORT`. `file://` often forgets ticks — pair with `НАЧНИ ЗДЕСЬ.command` (see `course-fin-sd`).

## Lesson numbers

In `course-nav.json`, prefer `num` strings `"1.1"`, `"6.2"` (chapter.lesson). Sticky title: `n + ' ' + title` when `n` contains a dot.

## QA

- [ ] Every `<video src>` file exists and is real mp4  
- [ ] No leftover «Смотреть на Vimeo» host buttons for downloaded parts  
- [ ] Accordion policy matches the table above  
- [ ] n/N with no duplicated leading index  
- [ ] Seen bar on 1-video and multi-video lessons  
- [ ] Cache-bust `course-ui.css?v=` / `course-seen.js?v=` after changes
