# Backlog

Status tracker for **CoCo Max Online**. Grouped by state. Checked items (`[x]`) are
implemented and verified; unchecked items are open.

Legend: 🐞 bug · ✨ feature · 🎨 polish · 🔬 research · ⚙️ infra

---

## ✅ Done

### Core engine & canvas
- [x] 256×192 PMODE 4 canvas with four‑color artifact rendering
- [x] Artifact color convention aligned to bundled data (`AA`=blue, `55`=orange, `FF`=white, `00`=black)
- [x] Top‑pixel‑wins layer compositor over white
- [x] Pixel‑exact striped orange/blue desktop background (1px lines, no moiré)

### Tools (24)
- [x] Lasso, Marquee, Hand/Grabber, Text
- [x] Paint Bucket, Spray, Brush, Pencil, Line, Eraser
- [x] Rectangle, Rounded Rect, Oval, Freehand, Polygon (+ filled variants)
- [x] Eyedropper (sample pattern), Magic Wand (contiguous select)
- [x] Crop (drag rectangle), Resize (nearest / bilinear / area; clamped ≤256×192)
- [x] Hand‑drawn 1‑bit pixel‑art tool icons at 22×20; redrawn for clarity
- [x] Tools palette label removed (thin drag strip)

### Patterns
- [x] 64‑pattern library from `coco_mark_data_64_patterns.json`
- [x] 16‑col × 4‑row column‑major palette layout
- [x] Current‑pattern swatch
- [x] Edit Pattern dialog with correct artifact‑color cell preview (lone=blue/orange, pair=white, off=black)
- [x] Larger borders between pattern swatches for distinctness

### Layers
- [x] New / Merge down / Duplicate / Delete
- [x] Show/hide eye toggle, drag‑reorder, double‑click rename, live thumbnails

### Selections & edit
- [x] Marching‑ants rectangle/lasso selection
- [x] Magic‑wand mask selection
- [x] Invert Selection, Deselect
- [x] Cut / Copy / Paste / Clear
- [x] Undo / Redo (30‑step) with Edit‑menu entries and shortcuts

### Display
- [x] Composite / RGB display modes; CSS phase toggle
- [x] NTSC Fringe — YIQ composite simulation, tightened to ~1px (`#a7e4ff` blue‑on‑white)
- [x] NTSC fringe also applied to pattern swatches and Preview
- [x] Fat Bits zoom view with arrow‑key panning
- [x] Grid; Rulers (64px H / 48px V guides)
- [x] Zoom scales document *content* with scroll bars (window stays fixed size)

### File formats
- [x] Load PNG (median‑cut ≤16 colors → pattern mapping), fit to 256×192 preserving aspect
- [x] Load/save native layered JSON project (round‑trip verified)
- [x] Load .MAX / .BIN (LOADM or raw page)
- [x] Decode CoCo 3 formats: MGE, HRS, CM3, RAT, VEF (verified against real files)
- [x] Color→pattern mapping dialog (auto best‑fit + manual adjust)
- [x] Save As… unified dialog (MAX/BIN/PNG/RLE/ASM) with destination choice
- [x] 256×192 save‑size guard with warning

### Disk images
- [x] Read/write RS‑DOS JV1 `.DSK` (35‑track, 161,280 bytes)
- [x] Disk Gallery window: per‑file thumbnails, usage stats, Load/Save/Duplicate/Delete/Download
- [x] Bounds‑safe disk rebuild with capacity checks (DISK_FULL / DIR_FULL)
- [x] Create .DSK… (fresh blank disk, mounted)
- [x] Save image directly into a mounted disk
- [x] "Add BASIC loader" option when saving .BIN to disk (writes same‑named ASCII `.BAS`, overwrites existing)
- [x] Graceful `.MAC` detection (placeholder thumbnail + explanatory message)

### UI / windows
- [x] Floating draggable windows on striped desktop
- [x] Menus: About (✿) / File / Edit / Goodies / Font / Style / Windows
- [x] About dialog with project info and copyright
- [x] Windows menu (show/hide each window) + Reset Window Positions
- [x] 256×192 1:1 Preview window mirroring the render
- [x] Default startup layout: Layers & Preview right‑aligned
- [x] Reset snaps side palettes into a non‑overlapping right‑edge stack
- [x] Six Google text fonts (Pixelify Sans, Doto, Tiny5, Micro 5, Jacquarda Bastarda 9, Bytesized), previewed in‑menu

### Infra
- [x] Single self‑contained HTML build (engine injected into scaffold)
- [x] Playwright smoke/regression testing workflow

---

## 🚧 In progress / partially done

- [ ] 🔬 **`.MAC` (CoCo Max II) decode.** Format detected via `PNTGMPNT` signature; header
  (title + zero‑pad + signature) parsed. RLE schemes tried produce the correct ~6,144‑byte
  output length but not a clean image — compression remains unresolved. Currently shows a
  labeled placeholder + message instead of noise. **Needs the CoCo Max II disassembly or a
  format spec to finish.**

---

## 📋 Planned / open ideas

### Formats & interop
- [ ] 🔬 Finish `.MAC` decoder (blocked on format docs) and add `.MAC` thumbnails/loading
- [ ] ✨ Optional `.MAX`/`.BIN` → `.MAC` and `MAC2CC3`‑style conversion in‑app
- [ ] ✨ Export animated/− multi‑page CoCo 3 hi‑res formats (write, not just read)
- [ ] ✨ Import additional CoCo formats if requested (e.g. CM/ART variants)
- [ ] ✨ Tokenized (binary) BASIC loader option in addition to the ASCII loader

### Editing
- [ ] ✨ Upscaling resize with dithering (currently fit/contain + re‑threshold only)
- [ ] ✨ Per‑layer opacity / blend beyond top‑pixel‑wins
- [ ] ✨ Selection transform (move/scale/rotate floating selection)
- [ ] ✨ Configurable brush sizes/shapes; pressure‑agnostic smoothing
- [ ] 🎨 More authentic bitmap CoCo Max fonts (current text uses web fonts thresholded to 1‑bit)

### Display / fidelity
- [ ] 🔬 Tune NTSC fringe chroma matrix toward exact `#a7e4ff` (green channel currently a touch low)
- [ ] ✨ Optional scanline / phosphor CRT overlay to pair with NTSC fringe
- [ ] ✨ Toggle for artifact phase per row (some CoCos shift phase per scanline)

### UX
- [ ] ✨ Persist window layout & preferences (localStorage) between sessions
- [ ] ✨ Touch / tablet input support
- [ ] ✨ Mobile‑responsive window arrangement
- [ ] 🎨 Onboarding / first‑run hints
- [ ] ✨ Recent files / recent disks list

### Infra / project
- [ ] ⚙️ Split repo into `src/` + committed build artifact; add `build.sh`/`build.py`
- [ ] ⚙️ CI: run `node --check` + Playwright smoke tests on push
- [ ] ⚙️ Add a license file (MIT suggested) if opening the source
- [ ] ⚙️ Tag a versioned release and attach `cocomax-online.html`
- [ ] 🎨 Screenshots / animated demo GIF for the README and repo social card

---

## 🐞 Known issues to watch

- [ ] Fonts fail to load offline (Google Fonts) → text tool falls back to a system face.
  Consider self‑hosting/embedding the fonts for a fully offline build.
- [ ] Resize algorithm differences are subtle at 1‑bit depth (expected, document clearly).
- [ ] `.MAC` files cannot be loaded yet (see In‑progress).
