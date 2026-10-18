# 3D Room Acoustic Modes

*By Mathis HOCHMUTH*

An interactive, single-file 3D visualisation of the **standing-wave (room) modes** of a rectangular room. Pick a mode, resize the room, and watch the pressure field oscillate as a cloud of spheres that move, grow and change colour with the local sound pressure.

Built with [three.js](https://threejs.org/) (r128). No build step, no dependencies to install: open the HTML file in a browser.

## Features

- **27 selectable modes** (nx, ny, nz from 0 to 2), with the resonance frequency displayed live
- **Adjustable room dimensions** (X, Y, Z from 1 m to 30 m) with sliders and numeric inputs
- **Pressure field animation**: sphere position, size, colour and opacity follow the standing wave
- **Rotation of the whole model** with adjustable speed and direction (−60 to +60 °/s)
- **Wave speed control** (0.5x to 3x) and pause/play
- **Mouse and touch navigation**: drag to orbit, wheel or pinch to zoom, reset-view button
- **Responsive layout** for phones, tablets and desktops (side panel in landscape, bottom panel in portrait)
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
| Wave speed | Speed of the pressure oscillation (0.5x – 3x) |
| Rotation speed | Model rotation speed in °/s (negative = reverse, 0 = stopped) |
| Room dimensions | Length (X), height (Y) and depth (Z) of the room, in metres |
| Drag / wheel / pinch | Orbit and zoom the camera |

## The physics

For a rectangular room with rigid walls, the resonance frequencies are:

```
f(nx, ny, nz) = (c / 2) · √( (nx / Lx)² + (ny / Lz)² + (nz / Ly)² )
```

where `c = 343 m/s` is the speed of sound in air at about 20 °C, and `Lx`, `Ly`, `Lz` are the room dimensions along the corresponding axes.

The normalised pressure at a point (u, v, w), with each coordinate running from 0 to 1 across the room, is:

```
p(u, v, w, t) = cos(nx·π·u) · cos(nz·π·v) · cos(ny·π·w) · cos(ωt)
```

Mode types:

- **Axial**: only one index is non-zero (waves bouncing between two parallel walls)
- **Tangential**: two indices are non-zero
- **Oblique**: all three indices are non-zero

### Axes and index convention

| Axis | Colour | Meaning | Default size | Mode index |
|---|---|---|---|---|
| X | red | length | 10 m | `nx` |
| Y | green | height (vertical) | 2.5 m | `nz` |
| Z | blue | depth | 5 m | `ny` |

Note that `ny` follows the **Z** axis and `nz` follows the **Y** (vertical) axis. This is inherited from the original version of the project.

### Example frequencies (10 × 2.5 × 5 m room)

| Mode (nx,ny,nz) | Type | Frequency |
|---|---|---|
| 1,0,0 | axial (X) | 17.15 Hz |
| 0,1,0 | axial (Z) | 34.30 Hz |
| 1,1,0 | tangential | 38.35 Hz |
| 0,0,1 | axial (Y) | 68.60 Hz |
| 1,1,1 | oblique | 78.59 Hz |
| 2,2,2 | oblique | 157.18 Hz |

All 27 values were checked against an independent calculation. Several modes share the same frequency when the room dimensions are simple multiples of each other (for example 0,0,1 and 0,2,0 are both 68.60 Hz).

## Project structure

```
.
├── 3D_Room_Acoustic_Modes.html   # the whole application (HTML + CSS + JavaScript)
└── README.md
```

## Implementation notes

- Spheres are stored in normalised coordinates, so changing the room size only rescales positions; no geometry is rebuilt.
- Sphere size and displacement amplitude scale with the room, so small or flat rooms stay readable.
- The camera distance is computed from the viewport aspect ratio and the room size, so the whole room stays in view in portrait and landscape.
- The canvas is absolutely positioned and resizes are deferred to the next animation frame, which avoids the `ResizeObserver loop completed with undelivered notifications` error.
- Animation time uses real elapsed time, so speed is the same on 60 Hz and 120 Hz displays.

## Browser support

Any browser with WebGL and `ResizeObserver` support (all current versions of Chrome, Edge, Firefox and Safari).

## Author

**Mathis HOCHMUTH**

## Credits

- [three.js](https://threejs.org/) (MIT License) for 3D rendering

## License

Copyright © 2026 Mathis HOCHMUTH.
