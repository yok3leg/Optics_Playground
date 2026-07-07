# Optics Playground

An interactive, grid-based optics simulator for planning and teaching modular
tabletop optics experiments. Drag optical components onto a breadboard-style
grid (1 cell = one 50 mm optics cube), and the beam path is traced live —
including beam splitting, spectral filtering, interference, diffraction,
polarization, fluorescence, Raman scattering, and 4f Fourier filtering.

Built as a **single self-contained HTML file** — no build step, no
dependencies, no server. Open `index.html` in any modern browser and it runs.

<!-- Add a screenshot: use the app's PNG export button, save as docs/screenshot.png,
     then uncomment the line below. -->
<!-- ![screenshot](docs/screenshot.png) -->

## Quick start

```bash
git clone https://github.com/<you>/optics-playground.git
cd optics-playground
open index.html        # macOS — or just double-click the file
```

Or host it anywhere static (GitHub Pages works out of the box: Settings →
Pages → deploy from branch).

## How to use

| Action | How |
|---|---|
| Add a component | Drag it from the left palette onto a grid cell |
| Move a component | Drag it to another cell |
| Select / edit properties | Click it — properties appear in the Inspector (right panel) |
| Rotate | Press <kbd>R</kbd> or double-click the block |
| Delete | Select, then <kbd>Del</kbd> / <kbd>Backspace</kbd> |
| Zoom | `−` / `+` buttons (bottom-right), <kbd>Ctrl</kbd>+scroll, or keys <kbd>−</kbd>/<kbd>+</kbd> |
| Fit whole workspace | `⛶ Fit` button or key <kbd>0</kbd> |
| Resize the grid | "Grid size" card in the right panel (columns × rows) |
| Save / load a layout | `Save JSON` / `Load` in the header (also autosaves to the browser) |
| Export an image | `PNG` button |
| Parts list | The BOM card (right panel) counts every block with its key settings |

### Components

**Sources** — Laser (wavelength 400–780 nm, power, ON/OFF toggle), white LED.

**Beam steering** — Mirror 45° (folds the beam 90°), flat mirror
(retro-reflects, only when its face points into the beam), beam splitter
(adjustable ratio), dichroic mirror (adjustable cut-off: reflects short-λ,
transmits long-λ).

**Shaping & filtering** — Lens (f = −250…+250 mm; focal lengths map to grid
cells, 50 mm each), pinhole (10 µm–2 mm; small holes diffract), iris, ND
filter (OD 0–4), longpass / shortpass / bandpass / **notch** filters,
polarizer (Malus's law), diffraction grating, object test target (vertical /
horizontal / grid lines for Fourier optics), spatial filter (pinhole or slit
at the Fourier plane), fluorescent sample, **Raman sample** (Stokes lines,
optional fluorescence background).

**Detection** — Screen (spot, interference fringes, Airy rings, dispersed
spectra, reconstructed images), photodiode (V vs. brightness curve),
spectrometer (live spectrum), single-photon counter (animated pulse train
with saturation warning).

### Presets (header dropdown)

| Preset | What it demonstrates |
|---|---|
| Michelson interferometer | Two-beam interference; misalign a mirror and the fringes vanish |
| Mach–Zehnder interferometer | Interference at both output ports |
| Pinhole diffraction | Airy pattern; ring scale follows λ/d |
| 4f Fourier filtering | Fourier plane spectrum + spatial filtering of image frequencies |
| Epi-fluorescence path | Dichroic-based excitation/emission separation |
| Malus's law | cos² transmission through two polarizers |
| Fluorescence emission spectrum | Right-angle fluorimeter with longpass filter |
| Absorption spectrum | Dual-beam spectrophotometer with reference arm |
| Raman spectroscopy | Weak Stokes lines revealed by a notch filter; toggle the fluorescence background to swamp them |
| White LED spectrum | Broadband spectrum + grating dispersion |

## Code structure

Everything lives in `index.html` (~1,800 lines), organized top-to-bottom:

```
index.html
├── <style>                CSS (dark theme, layout, panels, detector windows)
├── <body>                 header / palette / board (SVG + overlay) / inspector / BOM
└── <script>
    ├── constants & state  grid size, direction tables, blocks[], helpers (wl2rgb, …)
    ├── DEFS               component registry: per-type properties, schema, notes
    ├── blockSVG()         SVG icon drawing for every component type
    ├── tracing engine     trace() marches rays cell-to-cell; interact() applies
    │                      each component's physics and returns outgoing rays
    ├── rendering          renderBlocks(), renderBeams() (glow, taper, dispersion)
    ├── detector windows   updateWindows(), drawWindow(), drawScreen(),
    │                      drawMaskPattern() (4f), SPCM animation loop
    ├── zoom & grid size   applyZoom(), fitZoom(), setGridSize()
    ├── interaction        pointer-based drag & drop, selection, keyboard
    ├── inspector & BOM    property editors generated from each type's schema
    ├── save/load/presets  JSON (de)serialization, localStorage autosave, PNG export
    └── init               restore autosave or load the default preset
```

The physics model of a ray: `{cell, direction, spectrum[{wl, I}], coherence,
width/slope (for lenses), polarization angle, diffraction strength, Fourier
mask, path length}`. Each component transforms rays in `interact()`; adding a
new component means one entry in `DEFS`, one `case` in `interact()`, and one
`case` in `blockSVG()`.

## Physics fidelity

The simulation is **qualitative with correct trends**, intended for teaching
and layout planning — not for quantitative design:

- Fringe spacing scales with λ; fringe phase shifts when path lengths change,
  but is not metrically calibrated.
- Diffraction ring scale follows λ/d; OD, Malus's law, and filter edges use
  the real formulas.
- Raman shifts are fixed illustrative offsets (not cm⁻¹ accurate);
  fluorescence and Raman intensities are stylized ratios.
- Coherence is tracked per source: lasers interfere, LEDs don't.

## License

MIT — see [LICENSE](LICENSE).
