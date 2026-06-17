# ◊ FallMage · sovereign image editor

> The Photoshop wedge. One HTML file. No server. No subscription. Open · crop · rotate · resize · filter · text · brush · layers · export.
>
> v1 · prime **1423** · MIT · ◊·κ=1

**Live:** [sjgant80-hub.github.io/fallmage](https://sjgant80-hub.github.io/fallmage/)
**Source:** [github.com/sjgant80-hub/fallmage](https://github.com/sjgant80-hub/fallmage)

First tool of the **FallStudio** plan — the sovereign creative suite that replaces Adobe Creative Cloud one HTML file at a time.

---

## What it does

Real image editing in one tab. Drop a photo (or paste from clipboard, or start a blank canvas), edit it, export.

### Tools

| | Tool | What it does |
|---|---|---|
| ⤓ | **Move** | drag the active layer · arrow nudges · num inputs in props panel |
| ⊞ | **Crop** | drag a rectangle · release to crop the whole document |
| **T** | **Text** | click canvas · type · choose font/size/colour/weight/align · per-layer editable |
| ⌇ | **Brush** | paint on a paint layer · adjustable size & colour |
| ✗ | **Erase** | erase on the active paint layer · adjustable size |
| ⊙ | **Eyedrop** | click anywhere to capture the colour into brush + fill |
| ▤ | **Fill** | flood-fill the active paint layer with the chosen colour |

### Adjust (non-destructive)

Brightness · Contrast · Saturation · Hue rotate · Blur · Sepia · Grayscale · Invert. Applied as a CSS filter for live preview. **Bake to pixels** when you want it permanent on the active image layer.

### Layers

Image layers (one per opened file), paint layers (one per brush stroke session), text layers (multiple). Show/hide, reorder, opacity, delete. Top of the list is the top of the stack.

### Export

PNG · JPG (quality 0.92) · WEBP. Composites all visible layers, bakes the adjust filter, downloads.

### History

20-state undo/redo stack. Pushed on every meaningful edit (crop, paint stroke, layer add/remove, bake, rotate/flip).

---

## Ω autopilot · Ctrl+K

Press **Ctrl+K**. Type plain English. Examples that just work:

- *"instagram square"* / *"linkedin post"* / *"a4 portrait"* → new canvas at the right size
- *"vintage"* / *"noir"* / *"pop"* / *"warm"* / *"cool"* / *"fade"* / *"dream"* → applies a filter preset to the open image
- *"make this pop"* → punchy contrast + saturation
- *"black and white"* → grayscale
- *"caption: Friday sale 20% off"* → adds a centred text layer with that text
- *"crop to 1080×1080"* → crops the document

**T0** (built-in) covers all of the above offline. **T3** (any LLM key in Settings → Anthropic / Gemini *free* / OpenAI / OpenRouter) lets Ω parse arbitrary intents and call any of the actions (`newDoc`, `adjust`, `text`, `crop`) with parameters it picks.

### Built-in canvas presets

`instagram square` `instagram portrait` `instagram story` `twitter post` `linkedin post` `youtube thumbnail` `a4 portrait` `a4 landscape` `facebook cover` `tiktok` `business card`

### Built-in filter presets

`vintage` `noir` `pop` `warm` `cool` `fade` `invert` `mono` `dream`

---

## Use it

1. Open `index.html` from `file://` or visit the live URL
2. Open a file (⇪), paste an image (⌘V), drop one, or start a blank canvas
3. Use the tools on the left, the layers + adjust + properties on the right
4. **Ctrl+K** for autopilot
5. Export PNG/JPG/WEBP from the top menu

## Keyboard

- **⌘O / Ctrl+O** — open file
- **⌘S / Ctrl+S** — export PNG
- **⌘Z / Ctrl+Z** — undo · **⇧⌘Z / Ctrl+Y** — redo
- **Ctrl+K** — autopilot
- **V** move · **C** crop · **T** text · **B** brush · **E** erase · **I** eyedrop · **G** fill
- **Esc** close any modal

## Sovereignty stack

| Layer | Status |
|---|---|
| **UI** | runs from `file://` |
| **Compute** | T0 in-browser Canvas · T2 your local Ollama · T3 your direct API call (Ω only) |
| **Storage** | IndexedDB for settings · documents kept in memory · export downloads |
| **Mesh** | `BroadcastChannel('fall-signal')` · prime 1423 · responds to ping · `postMessage` API |

## What this does NOT do (by design)

- **RAW photo decoding** — browsers can't · use RawTherapee then re-import as JPG
- **Magic wand / content-aware fill** — would need a heavy ML model bundled
- **Pantone / colour-managed pipelines** — licensing
- **16-bit colour** — Canvas is 8-bit
- **Camera-grade noise reduction** — same heavy-ML problem

If you need those, you need Photoshop. If you don't, you need this.

---

## For developers

```
index.html      one file · ~75KB · vanilla JS · no deps
README.md       this
LICENSE         MIT
.nojekyll       Pages legacy deploy
```

### Architecture

- **Document model:** `state.doc = { w, h, bg, layers[], adjust }`
- **Layer types:** `image` (loaded file, immutable img), `paint` (offscreen canvas), `text` (font + content)
- **Render:** composite all visible layers to `#c`, apply `adjust` as a CSS filter on the canvas element (non-destructive); a separate `#sel` canvas draws selection bounds / crop rectangles
- **History:** lightweight snapshots (paint layers get deep-cloned via offscreen canvas) capped at 20 entries
- **Tools:** stateless functions on the `#sel` pointer events; `state.tool` is the active mode
- **Export:** re-composites to a fresh offscreen canvas at full resolution, bakes the filter via a second canvas pass, `toBlob()` → download

### Adding a tool

1. Add a button to `#toolList` with `data-tool="x"` and `data-hint="x · k"`
2. Handle the keystroke in the `keydown` handler's `map`
3. Branch on `state.tool === 'x'` in `onPointerDown` / `onPointerMove` / `onPointerUp`
4. Add a properties branch in `renderProps()`

### Adding a filter preset

Add to the `FILTERS` object — the 8 adjust properties summing to a vibe. The palette automatically picks it up.

### Adding a canvas size preset

Add to the `PRESETS` object — `{w, h}`. Palette picks it up. Ω auto-routes on substring match.

## Roadmap

- Layer reordering by drag
- Free transform handles on selected layer (resize via corner drag)
- Mask layers
- Gradient fill tool
- Smart guides / snap to centre
- Auto-save document state to IDB (currently only settings persist)
- FallStudio hub when the next 2 Adobe tools (FallPDF, FallVector) land

## Credit

- Part of the **FallStudio** plan — the sovereign creative suite
- Same cascade pattern as [FallOffice](https://github.com/sjgant80-hub/falloffice), [FallMap](https://github.com/sjgant80-hub/fallmap), [ACG Mapper](https://github.com/sjgant80-hub/acg-mapper)
- Mage / image · the name was earned not chosen

⚒ Part of the [fall* estate](https://github.com/sjgant80-hub) · prime 1423 · ◊·κ=1
