# Studio · Unified Producer Console

A single-page browser tool that locks a producer's **key** and **tempo** to three synchronized reference panels — a piano scale/chord visualizer, a tempo-synced compressor timing calculator, and a note-frequency EQ map.

Pick `G Major / 127 BPM` once. Every downstream decision — what chords fit the key, what attack and release times lock to the grid, what ISO bands your chord tones occupy — stays consistent across the entire project.

---

## Why

Producers constantly switch between disconnected reference tools: a chord chart in one tab, a delay-time calculator in another, a frequency chart somewhere on a bookmarked PDF. Values drift. Context gets lost.

Studio collapses those into one panel where the **master key** and **master tempo** drive every readout, and selecting a chord in one panel updates the others. The whole thing runs in a single HTML file with no build step.

---

## Features

### Master Controls
- **Key** — all 12 major keys, with enharmonic labels (C♯/D♭, etc.)
- **Tempo** — 40–240 BPM, direct numeric input

### Panel 01 · Harmonic Anchor
- Top-down 3D piano spanning two octaves
- Scale degrees rendered as tappable chips
- All seven diatonic triads labeled with Roman numerals (I, ii, iii, IV, V, vi, vii°)
- Click a chord → piano highlights all three chord tones

### Panel 02 · Tempo Sync
- Dual analog gauges for **ideal attack** and **ideal release**
- Six note divisions: ¼, ⅛, 1/16, 1/32, dotted ⅛, triplet ⅛
- Standard producer math: release = full note duration, attack = 2% of note duration
- Large numeric readouts in milliseconds

### Panel 03 · Frequency Map
- 31-band ISO third-octave EQ (20 Hz → 20 kHz)
- Selecting a single note raises every octave of that note across the spectrum
- Selecting a chord raises all three chord tones simultaneously
- Sorted frequency list with note names and exact Hz values

### Cross-Panel Routing
Selecting a chord in Panel 01 automatically updates Panels 02 and 03. One click, all three views stay in sync.

---

## Quick Start

```bash
git clone https://github.com/YOUR_USERNAME/studio.git
cd studio
```

Then either:

**Option A — open directly**
```bash
open studio.html          # macOS
xdg-open studio.html      # Linux
start studio.html         # Windows
```

**Option B — serve locally** (recommended, avoids any CDN caching quirks)
```bash
python3 -m http.server 8000
# then visit http://localhost:8000/studio.html
```

No build step. No `npm install`. One HTML file.

---

## Tech Stack

- **Vanilla HTML / CSS / JavaScript** — no framework, no bundler
- **Three.js r160** — loaded from unpkg CDN via import map, used for all 3D panels
- **Google Fonts** — Fraunces (display) and JetBrains Mono (technical readouts)

Total dependencies: two CDN links. That's it.

---

## Architecture

Single source of truth lives in the `state` object:

```js
const state = {
  key: 'G',
  bpm: 127,
  selectedNote: 'G',
  selectedChord: null,   // { root, type } or null
  division: 0.5
};
```

A minimal pub/sub (`subscribe` / `emit`) lets each panel register an update function. Any mutation to `state` is followed by `emit()`, which fans out to every subscribed panel.

Each panel lives in its own build function (`buildPiano`, `buildComp`, `buildFreq`) with its own Three.js scene, camera, and renderer, mounted into its own wrapper div. A `ResizeObserver` on each wrapper keeps canvases crisp on window resize.

---

## Project Structure

```
studio/
├── studio.html          # the entire app
├── README.md
└── LICENSE
```

---

## Browser Support

Tested on recent versions of Chrome, Firefox, and Safari. Requires WebGL and native ES module support (standard everywhere since ~2020).

---

## Roadmap

Ideas, not commitments — PRs welcome on any of these:

- Minor keys and modes (Dorian, Mixolydian, etc.)
- Seventh chords and extensions
- Export settings as a JSON preset
- MIDI output to a connected synth
- Alternate tuning systems (just intonation, etc.)
- Dark mode

---

## Contributing

Contributions are very welcome. For anything non-trivial, open an issue first so we can align on the approach.

1. Fork the repo
2. Create a feature branch (`git checkout -b feature/seventh-chords`)
3. Commit your changes
4. Open a pull request

Keep it vanilla — no framework additions, no build step. If you need a library, load it from a CDN via the import map.

---

## License

MIT — see [LICENSE](./LICENSE).

---

## Acknowledgments

Built for producers who care about mix consistency. Inspired by the hardware aesthetic of classic studio gear — Universal Audio, Neve, SSL — rendered in the browser.
