# Hebrewbooks-TextLayers

Corrected OCR text layers for the [HebrewBooks](https://hebrewbooks-2026.github.io/) Hebrew PDF library.

## What is this?

The HebrewBooks corpus contains ~100,000 scanned Hebrew books whose embedded text
layer was produced by various OCR engines, some of them quite old. Many books have
text layers that are partially or completely broken — wrong characters, missing
words, or even Latin-1-encoded "fake Hebrew" fonts that look right visually but
can't be searched.

This repository holds **corrected text layers** for those books. Each file is a
small (~5MB) text-only PDF that carries the corrected Hebrew text plus per-word
coordinates. The HebrewBooks desktop app periodically downloads new entries from
this repo and applies them in-place onto the user's local USB-resident PDFs — so
the visual rendering stays as the original publisher intended, but search and
selection now work correctly.

## Layout

```
textlayers/<FileID>.pdf     ← the corrected text-only sidecar PDFs
index.json                  ← auto-generated manifest (don't edit manually)
.github/workflows/          ← GitHub Action that rebuilds index.json on every merge
```

`<FileID>` matches HebrewBooks's `Katalog.FileID` — the numeric stem of the
original PDF filename (e.g. `46205` for `46205.pdf`).

## How to contribute a fix

1. In the HebrewBooks desktop app, right-click a book whose text layer looks
   broken → **"תקן OCR לספר זה..."**.
2. Wait for the OCR run (~10–15 min for a 500-page book).
3. The app applies the fix locally AND saves a copy of the sidecar to
   `%AppData%\HebrewBooks\PendingContributions\<FileID>.pdf` — the "open folder"
   button reveals it.
4. **Open a Pull Request** here, adding that sidecar at `textlayers/<FileID>.pdf`.
5. On merge, the GitHub Action regenerates `index.json` — within minutes, every
   HebrewBooks user with the textlayer-update feature enabled receives your fix.

### Manifest format

Auto-generated; do not edit by hand. Schema:

```json
[
  {
    "FileId": 46205,
    "Sha256": "abc123…",
    "Version": 1,
    "DownloadUrl": "https://raw.githubusercontent.com/.../textlayers/46205.pdf",
    "SizeBytes": 5673129
  }
]
```

`Version` increments each time a sidecar's hash changes — so users on the old
version see the new one as an update; users already on the new hash get nothing.

## Policy

* **No copyrighted text is added** by this process. The sidecar contains the
  same words a human can read by looking at the original page; OCR just makes
  those words searchable.
* **No Personal-corpus books.** The HebrewBooks app enforces this on both the
  extract side (the right-click action is hidden for `SourceType=Personal`) and
  the apply side (the update service refuses to overwrite a Personal book even
  if the manifest somehow lists one).
* Sidecars carry **no images** and **no copyright-protected metadata** — only
  per-word text + coordinates. Reviewable as a regular PDF.

## License

Public domain — [CC0 1.0](LICENSE). Tag your contributions with whatever credit
attribution you'd like in the PR description, but the artifact itself is free
to redistribute, modify, and reuse without attribution.
