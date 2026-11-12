---
name: offline-course-pipeline
description: >-
  Routes offline course work to the correct pipeline stage. Use when the user
  mentions курсы, GetCourse, офлайн-курс, «собери курс», «причеши курс»,
  флешка, QUANTUM, or asks which course skill to run. Does not scrape or edit
  lessons itself — opens the matching skill.
---

# Offline course pipeline (router)

## Order (do not skip)

```
getcourse-offline-archive → course-html-polish → course-toc-nav
                                    ↓
                         course-local-video (optional mp4)
                                    ↓
                         course-fin-sd (USB / SD)
```

## Phrase → skill

| User says | Skill to read and follow |
|-----------|--------------------------|
| собери курс / архив / скачать с GetCourse / mirror | `getcourse-offline-archive` |
| причеши курс / полный апгрейд / пасс разметки / читаемости | `course-html-polish` then `course-toc-nav` (no re-scrape) |
| оглавление / сайдбар / TOC / где я в курсе | `course-toc-nav` |
| локальные видео / mp4 / Vimeo / смотрела / n/N | `course-local-video` |
| флешка / SD / QUANTUM / НАЧНИ ЗДЕСЬ | `course-fin-sd` |

## Workspace discovery

1. Prefer the open workspace: `AGENTS.md`, `уроки/`, `уроки/_course-chrome-kit/`.
2. If missing, ask for the course root once — do not invent paths.
3. Chrome CSS/JS donor is **only** `уроки/_course-chrome-kit/` (or the path the user names). Never copy `_assets` from another course.

## Hard stops

- Never edit or overwrite `*-эталон` folders.
- Never ask for school login/password in chat; use an already authenticated browser session.
- Never paraphrase the author; light spelling only.
- Do not commit unless the user explicitly asks.
