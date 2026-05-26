# CoCo Max Online

A browser-based recreation of **CoCo Max** — the classic PMODE 4 artifact‑color paint
program for the Tandy/TRS‑80 Color Computer — built in the visual idiom of Apple
MacPaint. It runs entirely in the browser from a **single, self‑contained HTML file**
with no dependencies, no build step, and no server.

Draw in authentic 256×192 four‑color artifact graphics, load real CoCo disk images,
preview your work the way it would have looked on a composite TV, and export back out
to `.DSK`, `.MAX`, `.BIN`, and more.

---

## Table of contents

- [What it is](#what-it-is)
- [Quick start](#quick-start)
- [The CoCo artifact color model](#the-coco-artifact-color-model)
- [Features](#features)
  - [Drawing tools](#drawing-tools)
  - [Patterns](#patterns)
  - [Layers](#layers)
  - [Selections](#selections)
  - [Display modes](#display-modes)
  - [Fonts & text](#fonts--text)
  - [Windows & UI](#windows--ui)
- [File formats](#file-formats)
  - [Loading](#loading)
  - [Saving](#saving)
  - [Disk images (.DSK)](#disk-images-dsk)
- [Keyboard shortcuts](#keyboard-shortcuts)
- [Architecture](#architecture)
- [Building from source](#building-from-source)
- [Testing](#testing)
- [Known limitations](#known-limitations)
- [Credits](#credits)
- [License](#license)

---

## What it is

The original CoCo Max (Colorware, mid‑1980s) was a bitmap paint program for the Color
Computer that exploited the NTSC composite "artifact color" trick: a 1‑bit‑per‑pixel
PMODE 4 screen (256×192) produces **four perceived colors** — black, white, blue, and
orange — depending on each pixel's horizontal phase. CoCo Max wrapped that in a
mouse/joystick‑driven, MacPaint‑style interface with tools, patterns, and fonts.

**CoCo Max Online** rebuilds that experience for the modern browser:

- The same 256×192 PMODE 4 canvas and artifact‑color rendering.
- A faithful MacPaint‑style desktop: floating draggable windows over a striped
  orange/blue desktop, a bitmap tool palette, pull‑down menus, and a pattern tray.
- The ability to open genuine CoCo picture files and disk images, and to write disks
  back out for use in emulators or on real hardware.

It is a single file: `cocomax-online.html`. Open it and you're painting.

---

## Quick start

1. Download `cocomax-online.html`.
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari).
3. Start drawing. That's it — there is nothing to install.

To try the disk features, use **File ▸ Mount / Browse .DSK…** and pick any standard
161,280‑byte RS‑DOS `.DSK` image.

> An internet connection is used only to fetch the text‑tool fonts from Google Fonts.
> Everything else — engine, icons, patterns, decoders — is embedded in the file and
> works fully offline.

---

## The CoCo artifact color model

Understanding the color model makes the whole app make sense.

- The screen is **256×192 pixels, 1 bit each** (lit or unlit) — one 6,144‑byte PMODE 4
  page.
- Pixels are read in **horizontal pairs**. Within a pair, the pattern of lit bits
  determines the perceived color on a composite display:

  | Left pixel | Right pixel | Perceived color |
  |:----------:|:-----------:|:----------------|
  | off | off | **Black** |
  | on  | on  | **White** |
  | on  | off | **Blue** (even‑column lit) |
  | off | on  | **Orange** (odd‑column lit) |

- A lone lit pixel therefore shows up **blue or orange** depending on whether it lands
  on an even or odd column — this is the essence of artifact color, and it's exactly
  what the **Edit Pattern** dialog visualizes cell‑by‑cell.

The byte conventions used internally (matching the bundled 64‑pattern data) are
`0xAA → blue`, `0x55 → orange`, `0xFF → white`, `0x00 → black`.

---

## Features

### Drawing tools

A 24‑tool palette, drawn as crisp 1‑bit pixel‑art icons:

- **Lasso** and **Marquee** — freeform and rectangular selection
- **Grabber (hand)** — pan
- **Text** — type directly on the canvas with the chosen font
- **Paint Bucket** — flood fill with the current pattern
- **Spray Can**, **Paint Brush**, **Pencil** — freehand painting
- **Line**, **Eraser**
- **Rectangle / Filled Rectangle**
- **Rounded Rectangle / Filled**
- **Oval / Filled Oval**
- **Freehand shape / Filled**
- **Polygon / Filled**
- **Eyedropper** — sample the pattern under the cursor and make it current
- **Magic Wand** — select a contiguous region of like pixels
- **Crop** — trim the image to a dragged rectangle
- **Resize** — rescale the image (nearest / bilinear‑threshold / area‑average),
  clamped to a maximum of 256×192

### Patterns

- A **64‑pattern library** laid out as a 16‑column × 4‑row tray (column‑major: each
  column is a white‑, black‑, blue‑, and orange‑based variant).
- A **current‑pattern swatch** and an **Edit Pattern** editor where each 8×8 cell shows
  the *actual artifact color* it will produce (lone pixel → blue/orange by column, two
  adjacent → white, all off → black).

### Layers

- Multiple layers with **New / Merge down / Duplicate / Delete**.
- Per‑layer **show/hide** (eye toggle), **drag‑to‑reorder**, **double‑click rename**,
  and live **thumbnails**.
- Top‑pixel‑wins compositing over a default‑white background.

### Selections

- Rectangular/lasso marquee with animated **marching ants**.
- **Magic Wand** mask selection.
- **Invert Selection** and **Deselect**.
- Cut / Copy / Paste / Clear within selections.

### Display modes

- **Composite** — the standard four‑color artifact rendering.
- **RGB** — pure black/white (ignores artifact phase), for editing precision.
- **CSS phase toggle** — swap which parity reads as blue vs. orange.
- **NTSC Fringe** — an optional composite‑TV simulation: a tight YIQ chroma‑bandlimit
  that adds ~1px of authentic color fringing at edges (a blue pixel on white fringes
  toward `#a7e4ff`), soft non‑square pixels, and gentle dither blending — without
  smearing the image. The fringe is applied consistently to the canvas, the live
  Preview, and the pattern swatches.
- **Fat Bits** — zoomed pixel‑editing view with arrow‑key panning.
- **Grid** and **Rulers** (guides every 64px horizontally, 48px vertically).

### Fonts & text

The text tool offers six retro/pixel typefaces (via Google Fonts), each previewed in
its own face in the Font menu:

- Pixelify Sans · Doto · Tiny5 · Micro 5 · Jacquarda Bastarda 9 · Bytesized

Text supports bold/italic/underline/outline/shadow styles and left/center/right
alignment, and is thresholded to 1‑bit and stamped with the current pattern.

### Windows & UI

- Floating, draggable windows: **Tools, Drawing (document), Layers, Preview, Patterns,
  Disk Gallery.**
- A **256×192 1:1 Preview** window mirroring the rendered output (including NTSC fringe).
- A **Windows menu** to show/hide each window plus **Reset Window Positions**, which
  snaps the side palettes (Layers → Preview → Disk Gallery) into a tidy, non‑overlapping
  right‑edge stack.
- A **zoomable document window** with scroll bars — zoom scales the *content*, not the
  window.
- An **About** menu (the ✿ at the far left) with project info and copyright.
- A pixel‑exact striped orange/blue desktop background (1px alternating lines).

---

## File formats

### Loading

| Format | Notes |
|--------|-------|
| **PNG** | Quantized to ≤16 colors (median cut), then mapped to artifact patterns. Fits to 256×192 preserving aspect ratio. |
| **JSON** | Native layered project format (full round‑trip). |
| **.MAX / .BIN** | CoCo Max / raw PMODE 4 pages (LOADM‑wrapped or raw 6,144 bytes). |
| **.MGE** | Sub‑Etha / ColorMax CoCo 3 format (320×200, 16‑color, RLE or uncompressed). |
| **.HRS** | Hi‑res CoCo 3 (320×192, 16‑color). |
| **.CM3** | CoCo Max 3. |
| **.RAT** | CoCo 3 (escape‑RLE, 320×199). |
| **.VEF** | CoCo 3 (multiple sub‑types, optional RLE). |

CoCo 3 formats are decoded to 16 indexed colors and run through a **color‑to‑pattern
mapping dialog** (auto‑mapped to best‑fit artifact patterns, then adjustable per color).

### Saving

Via **File ▸ Save As…**, choose a format and a destination:

- **Formats:** `.MAX`, `.BIN`, `.PNG`, `.RLE`, `.ASM` (assembler source), plus the
  native layered `.JSON` project.
- **Destinations:** download to your computer, or — when a disk is mounted — write
  directly into the mounted `.DSK`.
- Every save is guarded to **256×192**; oversize images are refused with a warning.

### Disk images (.DSK)

CoCo Max Online reads and writes standard **RS‑DOS JV1** disk images (35 tracks,
161,280 bytes).

- **Mount / Browse .DSK…** opens the **Disk Gallery**: a floating window with a
  thumbnail for every picture on the disk, disk‑usage stats (granules + KB used/free),
  and **Load / Save→Disk / Duplicate / Delete / Download .DSK** actions.
- **Create .DSK…** makes a fresh, empty 35‑track disk and mounts it so you can save
  images into it.
- When saving a **Binary (.BIN)** image to a disk, you can tick **"Add a BASIC loader"**
  to also write a same‑named `.BAS` program that `PMODE`s, `LOADM`s, and displays the
  image — ready to `RUN` on a real CoCo or in an emulator. Existing loaders of that name
  are overwritten.

---

## Keyboard shortcuts

| Shortcut | Action |
|----------|--------|
| ⌘/Ctrl + Z | Undo |
| ⌘/Ctrl + Shift + Z, or Ctrl + Y | Redo |
| ⌘/Ctrl + X / C / V | Cut / Copy / Paste |
| ⌘/Ctrl + A | Select All |
| ⌘/Ctrl + Shift + I | Invert Selection |
| ⌘/Ctrl + D | Deselect |
| ⌘/Ctrl + O | Open |
| ⌘/Ctrl + S | Save As… |
| ⌘/Ctrl + E | Trace Edges |
| Arrow keys (in Fat Bits) | Pan the magnified view (Shift = larger step) |

---

## Architecture

- **Single file.** The shipped `cocomax-online.html` is produced by injecting the
  engine JavaScript into an HTML/CSS scaffold. There is no runtime dependency and no
  bundler.
- **Rendering.** A 256×192 offscreen canvas holds the artifact‑color render; it is
  scaled into the visible document canvas. NTSC fringe is an optional YIQ post‑process
  over the offscreen buffer. The Preview window blits the same buffer at 1:1.
- **Layer model.** Each layer is a `Uint8Array(256×192)` bitmap plus a mask; the
  compositor is top‑set‑pixel‑wins over white.
- **Patterns.** A 64‑entry library of 8‑byte tiles; the current pattern is stamped
  through a per‑pixel bit test so brushes/shapes/fills pick up the artifact phase.
- **Disk I/O.** Pure‑JS reader/writer for RS‑DOS JV1 layout (granule allocation table,
  directory entries, file types/ASCII flags) with capacity checks and safe rebuilds.
- **Decoders.** Self‑contained decoders for each supported CoCo/CoCo 3 picture format,
  plus a median‑cut quantizer and a 16‑color→pattern mapper for PNG and CoCo 3 imports.

### Repository layout (suggested)

```
cocomax-online.html      # the shippable, self-contained app
src/
  cmo.html               # HTML + CSS scaffold (contains an injection marker)
  engine.js              # the full application engine (~1,965 lines)
  patterns64.json        # 64-pattern library data
  icons_22_b64.json      # 24 hand-drawn 22×20 1-bit toolbar icons (base64 PNG)
  build.py / build.sh    # injects engine.js into cmo.html → cocomax-online.html
README.md
backlog.md
```

---

## Building from source

The build is a single text substitution — the engine is injected into the scaffold's
placeholder comment:

```bash
python3 -c "html=open('src/cmo.html').read(); \
engine=open('src/engine.js').read(); \
open('cocomax-online.html','w').write( \
  html.replace('/* engine + app code injected below */', engine))"
```

`engine.js` stays `node --check`‑clean throughout development, so a quick lint is:

```bash
node --check src/engine.js
```

---

## Testing

Development used **Playwright** (headless Chromium) for smoke and regression checks:
boot with zero page/console errors, draw operations, undo/redo, JSON round‑trip,
mounting real `.DSK` images, decoding every supported format, disk Save/Duplicate/Delete
round‑trips, window layout, and visual checks of NTSC fringe and thumbnails.

A representative check:

```python
from playwright.sync_api import sync_playwright
import pathlib
url = "file://" + str(pathlib.Path("cocomax-online.html").resolve())
with sync_playwright() as p:
    b = p.chromium.launch(); pg = b.new_page()
    errs = []; pg.on("pageerror", lambda e: errs.append(str(e)))
    pg.goto(url); pg.wait_for_timeout(500)
    assert pg.evaluate("layers.length > 0")
    assert not errs
    b.close()
```

---

## Known limitations

- **`.MAC` files (CoCo Max II native).** The format is detected (it carries a
  `PNTGMPNT` signature) but its proprietary RLE compression is undocumented and not yet
  decoded. Such files show a labeled placeholder and an explanatory message rather than
  garbage. Converting to `.MAX`/`.BIN` (e.g. with `MAC2CC3`) lets them load and thumbnail
  like everything else. *Help wanted — see the backlog.*
- **Fonts require the network.** The six text fonts load from Google Fonts; offline,
  the text tool falls back to a system face. (Everything else works offline.)
- **Resize at 1‑bit depth.** The bilinear/area resize algorithms re‑threshold to 1‑bit,
  so their differences are subtle at this depth.
- **Artifact fringe is a simulation.** The NTSC mode approximates composite output; it
  is tuned to look right, not to be a cycle‑accurate signal model.

---

## Credits

- Concept and direction: **Carlos Camacho**
- Inspired by **Colorware CoCo Max II / III** and **Apple MacPaint**.
- Fonts: Pixelify Sans, Doto, Tiny5, Micro 5, Jacquarda Bastarda 9, Bytesized
  (Google Fonts).

The TRS‑80 Color Computer, CoCo Max, and MacPaint are the works/trademarks of their
respective owners. This project is an independent, educational homage and is not
affiliated with or endorsed by any of them.

---

## License

© 2026 Carlos Camacho. All rights reserved.

> Choose and drop in your preferred license here (e.g. MIT) before publishing if you
> intend the source to be reused. Until then, "all rights reserved" applies.
