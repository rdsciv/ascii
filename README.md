# Documentation

https://google-50c0c90a.mintlify.app/

# ASCII

A browser-based ASCII art converter. Drop an image, video, or point it at your webcam — get live ASCII back with granular cell sizing, rich color modes, dithering, retro palettes, and animated GIF export.

**Live:** https://rdsciv.github.io/ascii/
**Roadmap:** [docs/PRD.md](docs/PRD.md)

## Features

- **Inputs** — JPG, PNG, WebP, GIF, MP4, MOV. Drag-and-drop anywhere, paste from clipboard, live webcam, or file picker.
- **Granular cell size** — slider 1 to 500 in 0.5 steps, numeric up to 2000.
- **9 color modes** — Source (full color), Source boosted, White on black, Inverse, Green/Amber/Blue terminal, Gradient ramp, and Palette-quantized (CGA, EGA, C64, Game Boy, PICO-8).
- **Adjustments** — contrast, gamma, invert, edge-aware characters, Sobel edge threshold, char aspect ratio, saturation, hue shift.
- **Dithering** — None, Floyd-Steinberg, Atkinson, Ordered 4×4, Ordered 8×8.
- **Typography** — 8 monospace fonts (JetBrains Mono, IBM Plex Mono, Space Mono, Fira Code, Source Code Pro, Inconsolata, DM Mono, Courier Prime) plus system mono. Weight 100–900.
- **Post-processing** — Bloom, scanlines, vignette with intensity sliders, plus chromatic aberration and film grain.
- **Animated GIF export** — sweep any numeric parameter (cell size, contrast, hue, etc.) from A to B over N frames at configurable FPS, easing, direction, and quality. Also supports video-timeline sweeps.
- **Export formats** — PNG (1×/2×/4× with optional transparent background), SVG, ANSI truecolor `.ans`, plain text `.txt`, animated GIF.
- **Presets** — 8 built-in styles (Classic, Matrix, Newspaper, CRT Terminal, Neon, Game Boy, Blueprint, Xerox). Save custom presets. Auto-restore last session. Shareable URL encoding all settings.
- **UX** — keyboard shortcuts, render indicator, responsive layout, fullscreen preview, reset-to-defaults.

## Keyboard shortcuts

| Key | Action |
|---|---|
| `F` | Toggle fullscreen |
| `S` | Save PNG |
| `G` | Export GIF |
| `P` or `Space` | Play/stop animation preview |
| `[` / `]` | Decrement/increment cell size |

## Running locally

It's a single HTML file with no build step. Open `index.html` in a modern browser.

The GIF exporter uses [gif.js.optimized](https://www.npmjs.com/package/gif.js.optimized) loaded from jsDelivr; if you open via `file://` the worker is fetched and wrapped as a same-origin blob URL to avoid CORS issues.

## Tech

- HTML5 Canvas 2D
- Vanilla JavaScript, no framework, no build step
- Google Fonts for monospace variety
- `gif.js.optimized` via jsDelivr CDN

## License

MIT
