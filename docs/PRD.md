# ASCII — Product Requirements Document

> *Every pixel. Every character. Every idea.*

**Live:** https://rdsciv.github.io/ascii/
**Repo:** https://github.com/rdsciv/ascii
**Status:** v0 shipped. v1 specified below.
**Owner:** Ryan Childress
**Last revised:** 2026-04-17

---

## 1. One-liner

The most powerful ASCII art converter on the open web — a GPU-accelerated, AI-aware, keyboard-driven creative instrument that turns any image, video, webcam feed, or written prompt into animated, character-rendered art you want to share.

## 2. North Star

**"If someone sees your output and doesn't stop scrolling, we failed."**

Every feature in this document is judged against a single question: *does it produce work that mesmerizes people on first glance?*

## 3. Target users

| Persona | Primary use |
|---|---|
| **Designer** | Retro/terminal/cyberpunk visual systems; client pitch decks; brand moodboards |
| **Streamer / creator** | Live ASCII visualizers in OBS, TikTok, Twitch overlays — low bandwidth, unique identity |
| **Indie game dev** | UI mockups, splash screens, ASCII-era aesthetic assets |
| **Developer** | Profile art, animated README banners, terminal screensavers, art commits |
| **Generative artist** | Glitch, databending, procedural character exploration |
| **Educator** | Teaching quantization, dithering, perceptual luminance, image processing |
| **Plotter / pen-art hobbyist** | SVG output for AxiDraw, HP pen plotters, laser engravers |
| **Demosceners** | WebGL ASCII playground with live-code feel |

## 4. Jobs-to-be-done

- "Turn this screenshot into ASCII I can paste in my terminal."
- "Make an 8-second looping GIF for my Twitter header."
- "Give me ASCII webcam in OBS with a transparent background."
- "Morph from my face to a cat over 3 seconds, export as MP4."
- "Plot this on my AxiDraw tonight."
- "Visualize my set on Twitch, reactive to mic input."
- "Type 'cyberpunk alley at night' and hand me ASCII of it."
- "Make something weird enough to go viral."

## 5. Competitive landscape

| Tool | Strength | Gap |
|---|---|---|
| ASCII.ly, manytools.org | Free, simple | Monochrome only, no animation, no export |
| `jp2a` / `libcaca` (CLI) | Fast, Unix-y | No live editing, no GUI, terminal-bound |
| AsciiNema | Terminal recording | Different niche — recording, not generation |
| Stable Diffusion + ASCII post | High-quality source | Two-step; not integrated; not interactive |

**Our moat**: GPU performance, live inputs (webcam/mic/MIDI), AI integration, motion design tooling, broad export surface, zero-install single-file shipment, shareable URL state.

## 6. Current state (v0, shipped)

Live at https://rdsciv.github.io/ascii/. ~1,500 lines of single-file HTML, no build step, GitHub Pages hosted.

- **Inputs**: image (JPG/PNG/WebP/GIF), video (MP4/MOV), live webcam, clipboard paste, drag-anywhere
- **Rendering**: Canvas 2D, 9 color modes (source/boost/palette-quantized for CGA/EGA/C64/Game Boy/PICO-8/terminal palettes/gradient), 5 dithering algorithms (FS, Atkinson, Bayer 4×4/8×8), 9 monospace fonts (incl. Google Sans Code variable), weight 100–900
- **Effects**: bloom, scanlines, vignette with per-effect intensity, chromatic aberration, film grain
- **Animation**: multi-track timeline, per-track From/To + frame windows, 5 easing curves, forward/reverse/ping-pong, in-canvas preview + animated GIF export
- **Exports**: PNG (1×/2×/4×, optional transparent BG), SVG, ANSI truecolor (.ans), plain text, animated GIF
- **State**: 8 built-in presets, user preset save/load, auto-persist last session, shareable URL encoding all settings
- **UX**: keyboard shortcuts (F/S/G/Space/[/]), fullscreen canvas, responsive layout, render indicator, reset

## 7. Feature themes for v1 (the mesmerize stack)

Ten themes, each a substantial body of work. Engineering estimates in Section 10.

### Theme A — GPU renderer

Port the hot path from Canvas 2D to WebGL2. Each cell becomes a textured quad sampled from a glyph atlas with brightness-indexed lookup.

- **10-100× faster** on large canvases
- **60fps realtime** even at 400×300 cells
- **4K / 8K targets** on-demand
- **Post-effects as a single fragment shader pass** (bloom, chromatic aberration, grain all GPU-side)
- **Fragment-shader dithering** — error diffusion in a compute-adjacent WebGL2 transform feedback pass
- Fallback to Canvas 2D on WebGL-less browsers

### Theme B — Media intelligence (in-browser ML)

Small, fast models running via ONNX Runtime Web or TF.js with WebGPU backend.

- **Depth (MiDaS-small, ~25MB)** → per-pixel depth buffer. Enables z-parallax, depth-weighted characters (close = dense chars, far = sparse), faux-3D camera orbit on a static image
- **Segmentation (MODNet / BiSeNet, ~10MB)** → subject vs background. Apply different charsets per region. Clean transparent export without color-keying.
- **Pose (BlazePose)** → trails that follow keypoints; character physics anchored to joints
- **Face landmarks (MediaPipe)** → emphasize eyes/mouth with denser chars; expressions trigger parameter changes (blink → global fade)
- **Style transfer (Fast Neural Style)** → stylize input before ASCII conversion — impressionist + ASCII hybrid

### Theme C — Text-to-ASCII (prompt → image → ASCII)

Zero-setup generative input.

- Pollinations.ai free API (no key required) for image generation inside the sidebar
- "Prompt" textarea + Generate button
- Generated image loads as source, auto-renders in current style
- Curated prompt library (~30 mood starters: *neo tokyo alley*, *brutalist concrete*, *isometric lo-fi cafe*, *eldritch fractal*, *astro-biology lab*)
- Reproducibility: seed + prompt saved alongside settings in share URL

### Theme D — Live input orchestration

- **Microphone / audio-reactive**: FFT buckets bound to any parameter. Cell size pulses with bass, hue shifts with highs. Full mapping editor.
- **Web MIDI**: detect connected devices (Launchpad, controller faders); drag-drop bind CC# → parameter
- **Gamepad API**: Xbox/PS controllers — triggers + analog sticks drive animation scrub
- **OSC over WebSocket**: remote control from TouchDesigner, Ableton, Max/MSP via a thin relay
- **Face-tracking triggers** (using Theme B face model): smile → saturation up, nod → next preset

### Theme E — Motion design engine

Turn the current track system into a proper timeline editor.

- **Keyframe editor**: scrub timeline, drop keyframes on any parameter; interpolate between
- **Cubic-bezier easing editor**: draw custom easing per track with handle manipulation, à la After Effects graph editor
- **Per-track direction**: each track has its own forward/reverse/ping-pong
- **Expression fields**: small DSL (`sin(t * 2) * 50 + 100`) — procedural parameters without keyframes
- **Scenes**: multiple independent setups, crossfade transitions between them on a master timeline
- **Onion skinning**: ghost of previous/next frame overlay during editing

### Theme F — Character mastery

- **Unicode superpacks**:
  - **Braille patterns** (U+2800–U+28FF) — 2×4 subpixel density per cell. Insane detail.
  - **Block elements** (▁▂▃▄▅▆▇█▏▎▍▌▋▊▉) — 8-level vertical/horizontal resolution per cell
  - **Box drawing** — line-art that follows gradient direction
  - **Kana / kanji / Cyrillic / Arabic / Hebrew / emoji / math operators**
- **Character weighting**: assign pull weight per char in a custom charset (`@` 3× as likely as `.`)
- **Halftone mode**: variable-size filled circles — newspaper print aesthetic
- **Bitmap font upload**: user drops a 16×16 glyph grid PNG; we use it as a custom font
- **Per-channel chars**: different charsets for R/G/B channels composited — chromatic ASCII
- **Gradient-direction line art**: use Sobel direction to pick box-drawing glyphs that trace contours

### Theme G — Export omnivore

Every reasonable format a creator might need.

- **MP4 / WebM** via MediaRecorder — higher-quality than GIF, with optional audio track
- **Lottie JSON** — animated vector format for web/mobile apps
- **STL** — extrude brightness into a heightmap, 3D-printable
- **DXF / AI** — per-glyph paths for laser cutters and pen plotters
- **Print-ready PDF** — 300 DPI, CMYK option, multiple page sizes
- **Zine layout PDF** — large art spanning multi-page spreads
- **HTML with inline `<span>` styles** — pasteable into GitHub markdown (selectable text, searchable)
- **Twitter/X optimized GIF** — auto-palette-limit, 15MB cap
- **Apple Touch Icon / Favicon pack** in one click
- **Code block ASCII** — wrapped in triple-backtick markdown fences for dev use

### Theme H — Pro workflow

- **Command palette (Cmd+K)** — fuzzy search every action. "save 4x png transparent", "bloom toggle", "cell 100", "export gif 120 frames".
- **Undo / redo** with snapshot stack
- **Session replay** — record every parameter change; play back as a time-lapse of the making-of
- **Split view** — A/B compare two settings live, side-by-side
- **Diff wipe** — before/after with draggable wipe slider
- **Performance overlay** (dev mode): render ms, cell count, GPU VRAM
- **Vim-mode navigation**: hjkl adjusts cell size, `<leader>` bindings for presets

### Theme I — Ecosystem

- **PWA** — installable on macOS, Windows, iOS, Android; offline-capable
- **Chrome / Firefox extension** — right-click any image on the web → "Open in ASCII"
- **`.ascii-art` bundle format** — zipped project: source image + settings + output frames
- **Watch mode** (File System Access API) — auto-convert every image that drops into a watched folder
- **Community gallery** *(stretch, requires backend)* — submit work, vote, remix

### Theme J — Delight layer (pure mesmerize)

- **Typewriter render mode** — characters type in one at a time, with configurable sound (mechanical typewriter, teletype, modem)
- **CRT shader** — realistic curvature, phosphor decay, aperture grille, screen glow
- **Boot sequence intro** — first load shows a fake terminal boot animation before the app appears
- **Cursor ripple** — hover the canvas; characters within radius ripple in brightness
- **Matrix rain overlay** — optional streaming-chars layer on top of the rendered art
- **Power-off transition** — CRT shut-down effect when switching presets
- **Haptic feedback** on supported devices
- **Retro sound design** — subtle click on param change, typewriter clack on export, modem chirp when share URL is copied

## 8. Top 10 mesmerize-features (the ones to build first)

Ranked by wow-per-line-of-code:

| # | Feature | Theme | Why it mesmerizes |
|---|---|---|---|
| 1 | **GPU renderer** | A | Unlocks everything downstream. Realtime 4K ASCII. |
| 2 | **Text-to-ASCII prompt bar** | C | Instant content creation with zero input. Viral-ready. |
| 3 | **Depth-aware 3D ASCII with parallax** | B | 2D input → 3D feel. Nobody expects this. |
| 4 | **Braille + block-element superpacks** | F | 2×4 subpixel density — photorealistic ASCII. |
| 5 | **MP4/WebM video export** | G | Unlocks social/streaming use cases GIF can't cover. |
| 6 | **Audio-reactive mic input** | D | Live music visualizer with a microphone. Twitch bait. |
| 7 | **Keyframe timeline + bezier easing** | E | Pro motion design, not a toy. |
| 8 | **Segment-aware regional ASCII** | B | Subject gets dense chars, background sparse. Magic. |
| 9 | **Command palette (Cmd+K)** | H | Keyboard-driven power user signal. |
| 10 | **Typewriter render + sound** | J | Feels cinematic. Memorable. |

## 9. Non-goals

- Running any backend. Everything ships as a single HTML file plus static assets.
- Requiring user accounts. (An optional cloud gallery is a far-future consideration.)
- Photoshop-level raster editing. The output medium is characters; we don't pretend otherwise.
- Supporting pre-ES2020 browsers. Modern Chrome/Firefox/Safari/Edge only.
- Monetization in v1. Ship free and open.

## 10. Roadmap — phased plans

Each phase will have its own implementation plan in `docs/superpowers/plans/`. These roll up to concrete milestones.

| Phase | Themes | Features | Estimate |
|---|---|---|---|
| **1 — GPU Foundation** | A, J, H partial | WebGL2 renderer, typewriter mode, Cmd+K palette | 4–6 weeks |
| **2 — Intelligence** | B, D | Depth, segmentation, audio-reactive, MIDI | 4–6 weeks |
| **3 — Motion** | E | Keyframe editor, bezier easing, expressions, scenes | 3–4 weeks |
| **4 — Character Depth** | F | Braille, block elements, bitmap fonts, halftone | 2–3 weeks |
| **5 — Generative** | C | Text-to-ASCII prompt bar, prompt library | 1 week |
| **6 — Export Omnivore** | G | Video, Lottie, STL, DXF, print-PDF, zine | 3–4 weeks |
| **7 — Ecosystem** | I | PWA, browser extension, bundles, watch mode | ongoing |

**Total v1 scope**: ~18–26 weeks at part-time pace, ship incrementally.

## 11. Success metrics

Quantitative:

- **Wow rate** — % of first-time visitors who export something within 60 seconds of load
- **Return rate** — 7-day and 30-day return visitors
- **Share rate** — exports per active session
- **GitHub stars** — 1,000+ within 6 months of v1 ship

Qualitative:

- Hacker News front page mention
- At least 5 visible Twitch streamers using it as a live visualizer
- Inclusion in 3+ "awesome-X" GitHub lists (awesome-ascii, awesome-creative-coding, awesome-webgl)
- Unprompted social posts / TikToks / Tweets showing output
- 1+ article from a design / creative-coding publication

## 12. Open questions

1. **Model hosting** — CDN OK for 25MB MiDaS, but aggressive caching via Service Worker is needed.
2. **Cloud gallery** — worth the backend cost? Could use Cloudflare R2 + Workers. Defer.
3. **File split** — v0 is single-file. WebGL alone adds ~500 lines. Plan: keep single-file as the public ship artifact; split into ES modules for dev, inline at build time via a trivial script.
4. **Mobile touch** — pinch to zoom canvas, tap-and-hold for color picker, swipe between presets?
5. **License** — MIT today. Dual-license for commercial use in v1? Probably no — stay MIT, let it spread.
6. **Telemetry** — anonymous usage counters (how often each feature is used) to inform Phase 7+? Requires a tiny endpoint. Defer until needed.

## 13. Appendix — the mesmerize test

Before merging any v1 feature, ask:

1. Can I screenshot or record the output and tweet it and have someone ask "what is that, how did you make it"?
2. Does it work end-to-end in under 10 clicks?
3. Does the output look different from anything produced by a generic ASCII converter?

If all three answers are yes, ship it. If not, iterate until they are.

---
