# PDF Scan Merge

A Colab notebook that turns a multi-page PDF of scanned objects (ID cards,
passports, documents, etc.) — one object per page — into a single tidy A4
PDF page, with each object cropped, deskewed, and background-cleaned.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/xkzy/pdf_scan_merge/blob/main/PDF_Merge_All_Pages.ipynb)

## What it does

For each page of the input PDF:

1. **Detect & crop** — finds the scanned object's boundary (edge detection,
   with a background-color-difference fallback for low-contrast scans) and
   perspective-corrects/deskews it, even if it was scanned at an angle.
   A small outward padding step avoids clipping into the object when
   detection locks onto an inner printed border instead of the true edge.
2. **Clean the background** — flattens uneven scan shadow/tint toward white
   using brightness-only correction (hue/saturation are untouched, so ink
   and highlight colors don't shift).
3. **Pack onto one A4 page** — arranges all cropped objects into an
   auto-computed grid that best fills a single A4 canvas, each placed at
   its true (unscaled) pixel size. If an object is too large for its grid
   cell, it's placed as-is and a warning is printed rather than silently
   shrinking it.

Objects where no clean boundary can be detected fall back to the full,
uncropped page — a page is never dropped from the output.

## Usage

1. Open the notebook in Colab (badge above).
2. Run the cells in order. You'll be prompted to upload a PDF.
3. The merged PDF is saved as `<input filename>_merged.pdf` and offered as
   a download at the end.

Re-running the upload cell in the same session automatically cleans up the
previous input/output files first.

## Configuration

Key options are set as variables near the top of the processing cell:

| Variable | Default | Purpose |
|---|---|---|
| `REMOVE_BACKGROUND` | `True` | Flatten scan shadow/tint toward white |
| `ENABLE_DOCUMENT_CROP` | `True` | Detect and crop/deskew each page |
| `MIN_CONTOUR_AREA_RATIO` | `0.02` | Minimum object size (fraction of page area) to detect |
| `CROP_PADDING_RATIO` | `0.035` | Outward margin added to the detected crop boundary |
| `A4_ORIENTATION` | `"portrait"` | Output page orientation: `"portrait"`, `"landscape"`, or `"auto"` |
| `GRID_PADDING_RATIO` | `0.015` | Spacing between grid cells |

DPI is calculated automatically from the input PDF's first page.

## Requirements

Installed automatically by the notebook: `pymupdf`, `pillow`, `numpy`,
`opencv-python`.
