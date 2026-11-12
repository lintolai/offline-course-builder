---
name: course-fin-sd
description: >-
  Packages finished offline courses onto a USB/SD volume (QUANTUM): two
  collections, НАЧНИ ЗДЕСЬ hub + .command local server for viewed ticks,
  FAT32/NFC copy caveats, parity vs laptop source. Use when the user mentions
  флешка, SD, QUANTUM, НАЧНИ ЗДЕСЬ, скопировать на карту, fin-staging, or mom
  open-without-internet. Do NOT re-scrape GetCourse or polish lesson text here.
---

# Fin pack → SD / флешка

## Goal

Mom opens **one** start file and reaches the right collection without guessing `index.html`. Internet not required. Local videos play; «смотрела» ticks persist when opened via the local server.

## Layout on the card

```
/Volumes/QUANTUM/   (or any mount name)
  НАЧНИ ЗДЕСЬ.command      ← double-click start
  НАЧНИ ЗДЕСЬ.html
  ДЛЯ МАМЫ.txt
  Сборник-1-Оксана-Олева/
    НАЧНИ ЗДЕСЬ.html
    ДЛЯ МАМЫ.txt
    01.… / 02.… / …
  Сборник-2-Юлия-Фолькман/
    НАЧНИ ЗДЕСЬ.html
    01. Вселенная Зентангла. Луна/
```

Laptop staging mirror: `уроки/_fin-staging/` is a **4 Sep 2026 snapshot**, not the latest collection. Do not sync it to the card. The card may be ahead (chapters 6–7 and unlisted visual fixes from mom’s computer) while the laptop working folder is ahead on other pages. Follow `.cursor/rules/flash-reconcile.mdc`: report the check / compare / add / remove list first. Copy only after Yana confirms each item.

## `НАЧНИ ЗДЕСЬ.command`

- `cd` to its own directory (flash root **or** `_fin-staging`).
- Start `python3 -m http.server 8766 --bind 127.0.0.1` if port free.
- `open` URL-encoded `НАЧНИ ЗДЕСЬ.html`.
- If port busy and start page ≠ HTTP 200 → Russian error; **do not** kill foreign servers.
- HTML pages: if opened as `file:`, try fetch localhost and `location.replace`; else show `#local-open` hint to use `.command`.

**Port conflict:** `Открыть курс.command` inside a single course also wants 8766 with a **different root**. Prefer hub opener for mom; stop the other server first.

**Windows launcher, not written yet.** Mom watches on Windows. Yana builds on a MacBook. `.command` does not run on her PC. The flash root needs a `.bat` that starts the same local server (`127.0.0.1:8766`) and opens `НАЧНИ ЗДЕСЬ.html`. Double-clicking the html is not a substitute: viewed ticks stick only on that origin.

## Copy rules

- FAT32 / msdos: normalize paths to **NFC** (Python `unicodedata.normalize("NFC", …)`). rsync alone can break Russian names.
- Prefer chunked copy + size check after each large mp4; card may `TimeoutError` on close of ~500MB+ files.
- Do not treat stale `download_failures*.json` as missing files — recheck disk.
- After copy: compare laptop ↔ card (sizes; small files full hash; mp4 head+tail + size). Run **twice** if user asks for careful QA.
- Update collection meta text («30 уроков · 7 глав») when the course grew.
- Ask before wiping/reformatting the card. Health: SD has no SMART; timeouts on read/write of large files ⇒ unreliable card, not “just FAT32”.

## Known state (22 Oct 2026)

- Do **not** run `уроки/_fin-staging/скопировать-на-QUANTUM.sh`. It `rsync --delete`s Luna from the 5-module staging folder and can wipe chapters 6–7 off the card.
- Working Luna on this Mac: `уроки/вселенная-зентангла-луна/` (7 modules, HTML 24 Sep 2026). Staging Luna: modules 1–5 only, HTML 4 Sep 2026.
- Large ch.6–7 mp4 were finished onto QUANTUM from mom’s computer (Yana, 22 Oct 2026). Do not repeat “the copy timed out and never arrived.”
- Small visual fixes were made on mom’s computer. The list was not written down and was not copied back. They show up only as a diff against the working folder once the card is readable.
- Report the reconcile list before any copy. Do not pick the laptop or the card as the winner beforehand.

## Do not

- Copy `_data/`, cookies, `.part`, venvs onto the card.
- Overwrite mom’s ticks (they live in browser localStorage, not in files).
- Claim “SD complete” without a size/hash pass.
