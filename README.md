# Offline Course Builder

Cursor plugin that turns paid online course lessons (GetCourse and similar schools) into **offline HTML courses** a person can open from a USB/SD card **without internet**.

Built from a real production pipeline used to package art courses for offline viewing (local video, readable layout, TOC/nav, flash drive hub).

## What it does

| Stage | Skill | Outcome |
|-------|--------|---------|
| 1. Archive | `getcourse-offline-archive` | Content-complete lean HTML + local PDFs/images; videos as URLs |
| 2. Polish | `course-html-polish` | Players, CTAs, materials panels, markup + readability |
| 3. Navigate | `course-toc-nav` | Typed TOC, sidebar, sticky prev/next |
| 4. Video (optional) | `course-local-video` | yt-dlp MP4s, accordion n/N, «viewed» ticks |
| 5. Ship | `course-fin-sd` | USB/SD layout + local `http.server` start script |

Router skill: `offline-course-pipeline` — maps phrases like «собери курс» / «причеши курс» / «флешка» to the right stage.

## Install (local)

This repo/folder lives at:

```text
~/.cursor/plugins/local/offline-course-builder/
```

1. Reload Cursor (**Developer: Reload Window**), or restart the app.
2. Open **Customize → Plugins** and confirm **Offline Course Builder** components (skills, rules, commands).
3. Open a course workspace that has `уроки/` and ideally `уроки/_course-chrome-kit/` + `AGENTS.md`.

For marketplace later: push a public git repo and submit at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

## Commands

- `/archive-course` — scrape/archive to lean offline HTML
- `/polish-course` — full polish + TOC/nav (no re-scrape)
- `/pack-usb` — pack to USB/SD after confirmation

## Hard rules

- Never modify `*-эталон` canons.
- Never ask for school passwords in chat (use an authenticated browser session).
- Never rewrite the author’s voice.
- Chrome CSS/JS only from `_course-chrome-kit/`.

## Layout expectations

```text
course-workspace/
  AGENTS.md
  уроки/
    _course-chrome-kit/     # sole CSS/JS donor
    <slug>/                 # archive
    <slug>-polish/          # polish + nav
```

## Privacy

Scraping uses the user’s already-logged-in browser session. No credentials are stored in the plugin.

## License

MIT
