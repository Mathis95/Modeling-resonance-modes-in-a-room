# 3D Room Acoustic Modes

*By Mathis HOCHMUTH*

An interactive, single-file 3D visualization of the **standing-wave (room) modes** of a rectangular room. Pick a mode, resize the room, and watch fixed-size particles move in the slowed standing-wave pattern. Particle colors indicate pressure polarity, while their movement reveals motion nodes and antinodes.

Built with [three.js](https://threejs.org/) (r128). No build step, no dependencies to install: open the HTML file in a browser.

## Features

- **27 selectable index combinations** (nx, ny, nz from 0 to 2), including 26 non-zero-frequency modes
- **Adjustable room dimensions** (X, Y, Z from 1 m to 30 m) with sliders and numeric inputs
- **Moving particles**: fixed-size markers oscillate in the mode's particle-displacement pattern; their motion reveals motion nodes and antinodes, while vivid violet and cyan distinguish pressure polarity
- **Rotation of the whole model** with adjustable speed and direction (−60 to +60 °/s)
- **Animation playback speed control** (0.5x to 3x) and pause/play; the modes are deliberately slowed for visibility
- **Mouse and touch navigation**: drag to orbit, wheel or pinch to zoom, reset-view button
- **Responsive layout** for phones, tablets and desktops, with the parameter panel consistently on the right
- **Performance adaptation**: fewer spheres and simpler geometry on touch and small-screen devices

## Quick start

1. Clone or download this repository.
2. Open `3D_Room_Acoustic_Modes.html` in a modern browser (Chrome, Edge, Firefox, Safari).

```bash
git clone https://github.com/<your-user>/<your-repo>.git
cd <your-repo>
# then just open the file, or serve the folder locally:
python3 -m http.server 8000
# → http://localhost:8000/3D_Room_Acoustic_Modes.html
```

> The page loads three.js from the cdnjs CDN, so an internet connection is needed on first load. To work offline, download [`three.min.js` r128](https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js) next to the HTML file and change the `<script src="...">` tag to point to it.

### Publish with GitHub Pages

Rename the file to `index.html` (or keep the name and open `/3D_Room_Acoustic_Modes.html`), then enable **Settings → Pages → Deploy from a branch** on your default branch.

## Controls

| Control | Action |
|---|---|
| Mode buttons (nx,ny,nz) | Select the acoustic mode to display |
| ⏸ Pause / ▶ Play | Freeze or resume the pressure oscillation |
| 🔄 Auto-rotate | Turn the model rotation on or off |
| 🎯 Reset view | Restore the default camera angle and model rotation |
| Animation speed | Playback speed of the deliberately slowed visualization (0.5x – 3x); it does not change the physical resonance frequency or speed of sound |
| Rotation speed | Model rotation speed in °/s (negative = reverse, 0 = stopped) |
| Room dimensions | Length (X), width (Y) and height (Z) of the room, in metres |
| Drag / wheel / pinch | Orbit and zoom the camera |

## The physics

The project uses a right-handed Cartesian coordinate system: **X** is the room length and points toward the viewer, **Y** is the width and points left-to-right, and **Z** is the height and points upward. Thus `nx` is the length index, `ny` the width index, and `nz` the height index. In the 3D renderer the room is rigidly mapped onto Three.js coordinates so physical Z is vertical; mode indices and room dimensions remain in physical X/Y/Z coordinates.

For a rectangular room with perfectly rigid walls, the resonance frequencies are:

```
f(nx, ny, nz) = (c / 2) · √( (nx / Lx)² + (ny / Ly)² + (nz / Lz)² )
```

where `c = 343 m/s` is the speed of sound in air at about 20 °C, `Lx`, `Ly`, and `Lz` are the room length, width, and height, and `nx`, `ny`, and `nz` are their corresponding non-negative integer mode indices. This ideal rectangular-room formula assumes a homogeneous fluid and perfectly rigid, axis-aligned walls.

For the selected mode, the normalized pressure field is:

```
p(x, y, z, t) / P₀ =
  cos(nx·π·(x/Lx + 1/2)) · cos(ny·π·(y/Ly + 1/2)) · cos(nz·π·(z/Lz + 1/2)) · cos(2πft)
```

Here `x`, `y`, and `z` are centered coordinates in `[-Lx/2, Lx/2]`, `[-Ly/2, Ly/2]`, and `[-Lz/2, Lz/2]`; `P₀` is an arbitrary pressure amplitude and `ω = 2πf`. This cosine mode shape has zero normal pressure gradient at every rigid wall.

For each non-zero index `nᵢ`, pressure nodes occur on planes perpendicular to that axis at `xᵢ = -Lᵢ/2 + (j + 1/2)Lᵢ/nᵢ`, for `j = 0, …, nᵢ - 1`. These planes are not drawn, so they do not obscure the 3D view. Pressure antinodes are locations where the magnitude of the spatial pressure pattern is maximal. In an ideal standing wave, the pressure and particle-displacement patterns have opposite nodes: particles move least at pressure antinodes and most at pressure nodes. Equivalently, **particle-motion antinodes** are where particles travel farthest over a full cycle, and **particle-motion nodes** are where they move least. In multidimensional modes, the displacement at any location depends on the full three-dimensional pressure gradient.

For a harmonic pressure field, linearized momentum and continuity give the particle displacement field:

```
ξ(x, y, z, t) = ∇P(x, y, z) / (ρ ω²) · cos(ωt)
```

where `P(x, y, z)` is the spatial pressure amplitude and `ρ` is the air density. The marker motion follows this spatial gradient pattern, so its component directions and relative magnitudes follow the ideal mode. Marker size stays constant and is slightly enlarged for visibility. For visibility, each displayed displacement component is scaled to at most 40% of the particle-grid spacing; this is not calibrated to a real sound-pressure amplitude, and the markers are samples, not individual molecules.

Particle color uses a high-contrast two-sided gradient to show instantaneous pressure polarity: vivid violet for negative pressure, transitioning through blue near zero, to bright cyan for positive pressure. Color does not encode the particle-motion antinodes and nodes.

The frequency shown in the interface is the theoretical real-world resonance frequency for the selected mode. The animated oscillation is intentionally slowed down so its spatial pattern can be seen; it does **not** run at the displayed frequency. At `1x`, the visualization phase advances at `1.8 rad/s`. The animation-speed control changes only this playback rate. It does not change the calculated resonance frequency or the speed of sound (`c`). The displayed motion is therefore a mode-shape visualization, not a real-time recording or a quantitatively scaled molecular trajectory. The visible markers are not real particles moving by these large distances: actual air molecules oscillate over extremely small distances, so the animation greatly exaggerates their movement for clarity.

Mode types:

- **Axial**: only one index is non-zero (waves bouncing between two parallel walls)
- **Tangential**: two indices are non-zero
- **Oblique**: all three indices are non-zero

### Axes and index convention

| Axis | Colour | Meaning | Default size | Mode index |
|---|---|---|---|---|
| X | red | length, toward the viewer | 10 m | `nx` |
| Y | green | width, left-to-right | 5 m | `ny` |
| Z | blue | height, upward | 5 m | `nz` |

The default view opens with mode `(0,0,1)` (`nz = 1`), the first vertical (height) mode, in a `10 × 5 × 5 m` room.

### Example frequencies (10 × 5 × 2.5 m room: length × width × height)

| Mode (nx,ny,nz) | Type | Frequency |
|---|---|---|
| 1,0,0 | axial (X) | 17.15 Hz |
| 0,1,0 | axial (Y, width) | 34.30 Hz |
| 0,0,1 | axial (Z, height) | 68.60 Hz |
| 1,1,0 | tangential (X-Y) | 38.35 Hz |
| 1,1,1 | oblique | 78.59 Hz |
| 2,2,2 | oblique | 157.18 Hz |

The `(0,0,0)` case has zero frequency and represents a spatially uniform pressure perturbation, not an acoustic resonance. Different modes can share a frequency when dimensions are related (for example `(0,0,1)` and `(0,2,0)` are both 68.60 Hz).

## Project structure

```
.
├── 3D_Room_Acoustic_Modes.html   # responsive interactive version
├── Room_Modes.html               # original interactive version
└── README.md
```

## Implementation notes

- Particle markers are stored in normalized physical X/Y/Z coordinates, so changing room dimensions rescales their positions.
- Particle markers keep a constant size, independent of instantaneous pressure; they move according to the pressure gradient. Their displayed size is slightly enlarged for visibility, and the visible travel distance is greatly exaggerated: real air molecules oscillate over extremely small distances, not by the large distances shown. Over a full cycle, motion antinodes are where markers travel farthest and motion nodes are where they move least. Do not confuse these with pressure antinodes and nodes, which have the opposite relationship to particle motion. Marker colors use a high-contrast violet-to-cyan gradient to distinguish negative from positive pressure. Pressure-node planes are described by the mode shape but are not drawn in the 3D view.
- The right-handed orientation frame maps physical X toward the viewer, Y left-to-right, and Z upward onto the renderer's depth, horizontal, and vertical directions.
- The camera distance is computed from the viewport aspect ratio and the room size, so the whole room stays in view in portrait and landscape.
- The canvas is absolutely positioned and resizes are deferred to the next animation frame, which avoids the `ResizeObserver loop completed with undelivered notifications` error.
- The parameter panel remains in a right-hand sidebar at all viewport sizes; the panel contents use a compact layout on narrow screens.
- The slowed animation uses real elapsed time, so its playback rate is consistent on 60 Hz and 120 Hz displays.

## Browser support

Any browser with WebGL and `ResizeObserver` support (all current versions of Chrome, Edge, Firefox and Safari).

## Author

**Mathis HOCHMUTH**

## Credits

- [three.js](https://threejs.org/) (MIT License) for 3D rendering

## License

Copyright © 2026 Mathis HOCHMUTH.
