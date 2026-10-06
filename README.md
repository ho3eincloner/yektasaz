# Yektasaz

Remove duplicate pages from scanned PDF files and compress them, safely.

Built for large folders of scanned case files, where the same page often appears several times: an exact copy, a phone photo of the same sheet, or the same sheet with an extra stamp. Removing a wrong page can't be undone, so the tool is designed to **keep a page when in doubt** and flag it for a human instead.

![screenshot](docs/screenshot.png)

> **Warning: files are rewritten in place and there is no undo.**
> Always try the tool on a copy of a few dozen files first.

## Features

- **Finds duplicates even when they are rotated, skewed or photographed.** Pages are aligned and compared at high resolution.
- **Never guesses on differences.** If a stamp, signature, number or typed text differs, both pages are kept and listed for review with the reason.
- **Two modes.** *Strict* (default) removes only practically identical pages. *Balanced* also removes re-scans and phone photos of the same sheet, at a higher risk of missing tiny differences in small text.
- **Compresses images** to 150 dpi / JPEG quality 70. Pages with no coloured ink are stored in grey.
- **Safe rewrite.** The new file is built next to the original, checked (page count and a visual comparison of every page), then swapped in. A power cut never leaves a half-written file.
- **Built for big batches.** Every file is processed in its own process with a time limit. A broken, encrypted or hanging file is left untouched and reported, and the rest continues. Progress is stored in SQLite, so an interrupted run resumes where it stopped.
- **Time estimate first.** The tool counts all files and measures real speed on a sample (changing nothing) before you start.
- **Persian UI** with a live log, alerts, a side-by-side review of near-duplicate pairs, and a final report (HTML + CSV).
- **No AI/ML and no internet.** Classic image processing only.

## Quick start

Requires Python 3.9+ (64-bit). Tested on 3.12.

```bash
pip install -r requirements.txt
python app.py              # opens the UI in your browser
python app.py --selftest   # checks the whole program on generated files
```

Without the browser:

```bash
python app.py --cli "D:\Parvandeh" --yes             # strict mode
python app.py --cli "D:\Parvandeh" --yes --balanced  # balanced mode
```

## Standalone Windows build (for offline servers)

Run `build_exe.bat` on a Windows PC **with** internet. It builds a self-contained folder and runs the self-test. Copy that folder to the server; neither Python nor internet is needed there.

> Status: the build script has not been verified on Windows yet.

## How it works

1. Each page is rendered to a small image; exact copies are found by hashing.
2. Visually similar pages (also rotated, cropped or zoomed) become candidates.
3. Each candidate pair is aligned (ORB features + RANSAC, refined with ECC) and compared at high resolution. The *difference score* is the amount of ink present in one page but not the other.
4. For typed forms, the visible text is compared too. Any text difference blocks removal.
5. A page is removed only when it is a duplicate of a page that is kept.

## Limitations

- No OCR: text inside images is not read, only compared as pixels. A one-character difference in very small, blurry text can fall below the detection limit. This is why strict mode is the default.
- Duplicates are detected within a file, not between different files.
- Files above 1500 pages are checked for exact copies only.
- Document title, author and bookmarks inside the PDF are dropped when a file is rewritten.

## Built with

Python, [PDFium](https://pdfium.googlesource.com/pdfium/) (via pypdfium2), OpenCV, NumPy, SQLite.

## License

MIT

---

<div dir="rtl">

**یکتاساز:** صفحه‌های تکراری PDFهای اسکن‌شده را حذف و فایل‌ها را فشرده می‌کند. اگر تفاوتی (مُهر، امضا، عدد، متن) باشد حذف نمی‌کند و برای بررسی علامت می‌زند. فایل‌ها روی خودشان بازنویسی می‌شوند و راه برگشت ندارد؛ اول روی یک کپی امتحان کنید.

</div>
