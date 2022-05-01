# pdf-text-quality

Tools for evaluating the quality of the text layer in PDFs.

## Background

Pages in PDF files which have been created from bitmap images rather than being
created from a digital source (eg. by rendering a Word or LaTeX document to a
PDF) typically combine the input bitmaps, as background graphics, with hidden
PDF text drawing commands overlaid on top. The hidden text enables text
selection to work.

If the words in the hidden text layer are not well aligned with the visible text
in the bitmap image, or if the characters were mis-recognized, text selection
will be difficult, or produce incorrect results when text is copied.

Poor text layers present a problem for text-centric annotation tools like
[Hypothesis](https://web.hypothes.is). Detecting low-quality text layers can
be used as part of manual or automated processes to re-OCR affected pages.

## Installation

1. Install [Pipenv](https://pipenv.pypa.io/en/latest/)

2. Install [Tesseract](https://github.com/tesseract-ocr/tesseract) and
   [Poppler](https://poppler.freedesktop.org) utilities.

   On macOS, these can be installed via Homebrew:

   ```
   brew install poppler tesseract
   ```

   On Linux, using apt:

   ```
   sudo apt-get install poppler-utils tesseract-ocr

   ```

   After this step, `pdftotext`, `pdftocairo` and `tesseract` binaries should
   be available on your system.

3. Clone this repository and install Python dependencies with:

   ```
   pipenv install --dev
   ```

## Usage

```
pipenv run check-pdf [args] <pdf_file>
```

This will extract the text layer from pages in `pdf_file` and compare it against
an OCR run over the same pages. For each page, a set of metrics is displayed.
The final `iou_weighted` metric is the recommended indicator. Values above 0.7
are generally "good", values from 0.5-0.7 are "fair" and values below 0.5 are
poor.

Example output for a PDF file where most pages are good, but one page (page 2)
is bad:

```
$ pipenv run check-pdf test-file.pdf

Checking text pages 1 to 5
Page 1 IoU: iou: 0.73, iou_x: 0.96, iou_y: 0.76, iou_weighted: 0.90
Page 2 IoU: iou: 0.19, iou_x: 0.33, iou_y: 0.41, iou_weighted: 0.35
Page 3 IoU: iou: 0.65, iou_x: 0.89, iou_y: 0.70, iou_weighted: 0.83
Page 4 IoU: iou: 0.65, iou_x: 0.90, iou_y: 0.70, iou_weighted: 0.84
Page 5 IoU: iou: 0.64, iou_x: 0.86, iou_y: 0.72, iou_weighted: 0.82
```

For contrast, here is the output from a PDF file which has a digitally created
cover page, with an accurate text layer, followed by a series of badly OCR-ed
pages with poor text layers:

```shellsession
$ pipenv run check-pdf bad-file.pdf

Checking text pages 1 to 5
Page 1 IoU: iou: 0.54, iou_x: 0.91, iou_y: 0.57, iou_weighted: 0.81
Page 2 IoU: iou: 0.20, iou_x: 0.41, iou_y: 0.36, iou_weighted: 0.39
Page 3 IoU: iou: 0.17, iou_x: 0.31, iou_y: 0.28, iou_weighted: 0.30
Page 4 IoU: iou: 0.19, iou_x: 0.36, iou_y: 0.31, iou_weighted: 0.35
Page 5 IoU: iou: 0.20, iou_x: 0.40, iou_y: 0.34, iou_weighted: 0.38
```

The range of pages to check can be controlled using the `--first-page` and
`--last-page` options. To see the full range of options, run:

```shellsession
pipenv run check-pdf --help
```
