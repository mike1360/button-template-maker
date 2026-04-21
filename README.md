# Button Template Maker

A single-file, browser-only tool for laying out print templates for pinback buttons. Drop in an image, pick a sizing preset or define your own widths + counts, and it packs copies onto a 19"×13" Super B / Tabloid Extra sheet. Print, cut, button.

No build step, no dependencies. Open `index.html` in a browser.

## Modes

- **Variety** — three hand-picked sizes (9" / 4.5" / 2.5") with counts 1/2/3. Good default.
- **Fill Page** — auto-packs the sheet with a guillotine packer. Tier ladder from hero → upper-mid → mid → mid-small → varied smalls, with per-tier max counts so large sizes don't eat the whole page. Tiers are capped by both `maxW` (image-aware) and page-width fractions (page-aware) so landscape images still leave room for mids.
- **Big Hero** — 12"/5"/3" with 1/1/4.
- **Scatter on Image** — previews what buttons will *look like* by placing varied circles on the source artwork. Sizes are snapshotted from whichever layout was active (Fill Page pulls the actual placed-copy diameters; Variety/Big Hero uses their width list). The biggest button maps to ~42% of the image's short dimension; all smaller sizes preserve their real-world inch ratios. Counts are boosted 4× to fill the space with variety. Bigger circles get inch labels. "Re-roll" reshuffles positions.

## Sidebar controls

- **Artwork** — drop or click to load an image, rotate 90° as needed.
- **Image Sizes & Counts** — width + count rows, add/remove freely. Hidden in Fill Page and Scatter modes.
- **Layout** — gap between copies (default 0"), page margin (default 0"), show image boundaries, show button cut guides.
- **Output** — Print Template button; stats panel below shows what got placed.

## Button cut guides

A checkbox under Layout draws a thick black+white circle inscribed in each copy on the print-template view — shows where the button would be cut from each rectangle. On-screen only; the `@media print` rule hides them so print output stays clean.

## Printing

Choose **19×13" (Super B / Tabloid Extra)**, **Actual Size / 100% scale**, and disable "Fit to page" in the print dialog.

## Code layout

All one file: `index.html`.

- `state` holds current mode (`fillMode` / `scatterMode`), sizes, zoom, etc.
- `layout()` — rectangular shelf packer for Variety / Big Hero modes.
- `fillPageLayout()` — guillotine packer with tier ladder for Fill Page.
- `computeScatter()` — random-rejection circle packer scaled to real button inches.
- `render()` branches: `renderScatter()` or the template-path. Both use the same zoom fit logic via `getContentSize()` + `getFitScale()`.
- Print hides the sidebar, zoom control, and all SVG overlays.

## Design notes

- Fill Page tier widths are capped by `min(maxW * fraction, pageW * fraction)`. The page-width cap is what keeps landscape images (`maxW == pageW`) from producing a monster hero that crowds out mid tiers. For portraits, `maxW` is already bound by page height × aspect, so the page-width cap rarely triggers.
- Scatter mode snapshots `state.scatterSizes` at the moment you click it, so re-rolling doesn't drift as you change other things. To change scatter sizes, click Variety / Fill Page / Big Hero to set up the source layout, then click Scatter again.
- The scatter preview re-anchors the biggest button at 42% of image short-dim (not 100%) on purpose — 100% would be accurate to print scale but would leave no room for variety.
