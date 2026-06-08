# constraint-viz

**Multi-scale constraint visualization oscilloscope** — a 5-panel real-time display for constraint theory applied to musical structure.

![Constraint Oscilloscope](docs/oscilloscope-preview.png)

## What Is This?

`constraint-viz` provides a **ConstraintOscilloscope** that renders any MIDI file through five complementary lenses, each revealing how constraint-theoretic structure manifests at a different scale:

| Panel | Scale | What It Shows |
|-------|-------|---------------|
| **1 — Waveform** | Sample | Rendered audio waveform with lattice snap geometry |
| **2 — Piano Roll** | Note | Pitch lattice & timing grid (note-level constraints) |
| **3 — Holonomy** | Phrase | Key drift trajectory, color-coded by harmonic region |
| **4 — Density** | Piece | Note density / structural arc over the entire composition |
| **5 — Lattice Snap** | Eisenstein | Eisenstein lattice snap visualization (tonal geometry) |

The key insight: **the same constraint structure appears at every scale**. The oscilloscope makes this self-similarity visible.

## Quick Start

### Installation

```bash
pip install constraint-viz
```

Or from source:

```bash
git clone https://github.com/SuperInstance/constraint-viz.git
cd constraint-viz
pip install -e .
```

### Dependencies

- Python ≥ 3.10
- NumPy ≥ 1.24
- Matplotlib ≥ 3.7
- Mido ≥ 1.3 (MIDI file parsing)

### Basic Usage

```python
from constraint_viz import ConstraintOscilloscope

scope = ConstraintOscilloscope()

# Visualize a single MIDI file
scope.visualize_midi(
    "my_song.mid",
    output_path="my_song_scope.png"
)

# High-resolution output (300 DPI)
scope.visualize_midi(
    "my_song.mid",
    output_path="my_song_hires.png",
    high_res=True
)
```

### Batch Processing

```python
import glob
from constraint_viz import ConstraintOscilloscope

scope = ConstraintOscilloscope()

for midi_file in glob.glob("*.mid"):
    output = midi_file.replace(".mid", "_scope.png")
    scope.visualize_midi(midi_file, output)
    print(f"✅ {midi_file} → {output}")
```

## The Five Panels in Detail

### Panel 1: Waveform (Sample Level)

Renders the MIDI to audio and displays the waveform. The constraint structure shows up as:

- **Lattice snap points**: amplitude values cluster at specific levels determined by the constraint lattice
- **Deadband regions**: near-zero segments where constraints are "satisfied" (system at rest)
- **Transient geometry**: attack/decay shapes reflect the underlying snap dynamics

This connects to [constraint-synth](https://github.com/SuperInstance/constraint-synth) for audio rendering.

### Panel 2: Piano Roll (Note Level)

Classic piano roll visualization enhanced with constraint coloring:

- **Vertical position** = pitch (mapped to lattice coordinates)
- **Horizontal position** = time
- **Color intensity** = constraint satisfaction (brighter = more constrained)
- **Grid overlay** = the timing lattice (quantized snap points)

Notes that land on lattice intersections are "snapped" — they satisfy constraints perfectly. Notes between intersections are in "deadband" — still valid but less constrained.

### Panel 3: Holonomy (Phrase Level)

Shows the **harmonic trajectory** of the piece as a path through tonal space:

- **X-axis** = circle-of-fifths position
- **Y-axis** = chromatic displacement
- **Color** = current harmonic region (tonic, dominant, subdominant, etc.)
- **Path curvature** = rate of harmonic change

Holonomy here refers to how the constraint manifold's geometry causes paths to "drift" when you traverse a closed loop — analogous to Berry phase in physics.

### Panel 4: Density (Piece Level)

A histogram of note density over time, revealing the **structural arc** of the composition:

- **Peaks** = climactic moments (many simultaneous constraints active)
- **Valleys** = transitions, rests, or cadences
- **Overall shape** = narrative arc (sonata form, blues, etc.)

The density profile is itself a constraint: the composer is working within an envelope that shapes the piece's emotional trajectory.

### Panel 5: Eisenstein Lattice Snap (Tonnetz Geometry)

A 2D projection of the **Eisenstein integer lattice** showing where each note snaps to:

- **Axes** = the two Eisenstein basis vectors (ω = e^(2πi/3))
- **Lattice points** = consonant intervals (perfect fifths, major thirds)
- **Snap lines** = paths from each note to its nearest lattice point
- **Cluster density** = harmonic richness at each moment

This is the deepest panel — it shows the mathematical structure underlying all the other panels. The Eisenstein lattice is the constraint substrate that generates the patterns visible at every other scale.

## Architecture

```
constraint_viz/
├── __init__.py              # Exports ConstraintOscilloscope
├── multi_scale.py           # Core: 5-panel visualization engine
docs/
├── DEVELOPER-GUIDE.md       # Contributing guide
├── USER-GUIDE.md            # Detailed usage documentation
examples/
├── demo_oscilloscope.py     # Batch visualization demo
tests/
├── test_viz.py              # Test suite
pyproject.toml               # Build config
```

### Key Design Decisions

1. **Matplotlib-based**: No heavy GPU dependencies; works on any system with a display or file output
2. **Lazy imports**: `constraint-synth` only loaded when waveform rendering is needed
3. **Agg backend**: Headless rendering by default — works on servers and CI
4. **Composable panels**: Each panel is a private method that can be called independently

## Connection to Constraint Theory

This tool visualizes the core principle of the [SuperInstance](https://github.com/SuperInstance) constraint research program:

> **Music is constraint propagation at multiple scales.**

At each level — from individual samples to entire compositions — the same mathematical structure (lattice geometry, deadband zones, snap dynamics) governs what sounds "right." The oscilloscope makes this self-similarity visible by rendering the same MIDI data through five different lenses.

Key concepts:

- **Constraints** = regions of valid behavior on a lattice
- **Deadband** = the tolerance zone where constraints are satisfied but not maximally tight
- **Snap** = the process of moving to the nearest lattice point (constraint satisfaction)
- **Holonomy** = the accumulated drift from traversing the constraint manifold

## Related Projects

| Repository | Description |
|-----------|-------------|
| [constraint-theory-core](https://github.com/SuperInstance/constraint-theory-core) | Core constraint theory mathematics |
| [constraint-synth](https://github.com/SuperInstance/constraint-synth) | Audio synthesis with constraint-based timbres |
| [constraint-instrument](https://github.com/SuperInstance/constraint-instrument) | Live constraint-based musical instrument |
| [constraint-theory-py](https://github.com/SuperInstance/constraint-theory-py) | Python library for constraint theory computations |
| [hex-graph-constraint](https://github.com/SuperInstance/hex-graph-constraint) | Hexagonal graph constraint engine (ZHC) |
| [fortran-constraint-checking](https://github.com/SuperInstance/fortran-constraint-checking) | High-performance Fortran constraint checker |
| [deadband-python](https://github.com/SuperInstance/deadband-python) | Deadband computation library |

## Development

### Setup

```bash
git clone https://github.com/SuperInstance/constraint-viz.git
cd constraint-viz
pip install -e ".[dev]"
```

### Running Tests

```bash
pytest tests/
```

### Adding a New Panel

1. Add a `_plot_<name>(self, ax, mid)` method to `ConstraintOscilloscope`
2. Call it from `visualize_midi()` with an appropriate subplot position
3. Add tests in `tests/test_viz.py`
4. Update this README with the panel description

See [DEVELOPER-GUIDE.md](docs/DEVELOPER-GUIDE.md) for detailed contribution guidelines.

## Examples

### Visualizing a Bach Fugue

```python
from constraint_viz import ConstraintOscilloscope

scope = ConstraintOscilloscope()
scope.visualize_midi(
    "bach_fugue_g_minor.mid",
    output_path="bach_fugue_scope.png",
    title="Bach Fugue in G Minor — Constraint Analysis"
)
```

Expected observations:
- **Panel 2**: Subject entries snap to lattice intersections
- **Panel 3**: Holonomy drift shows the fugue's modulation path
- **Panel 4**: Density peaks at each subject entry
- **Panel 5**: Counterpoint creates dense lattice clusters

### Visualizing a Jazz Blues

```python
scope.visualize_midi(
    "blues_in_c.mid",
    output_path="blues_scope.png",
    title="Blues in C — Constraint Oscilloscope"
)
```

Expected observations:
- **Panel 1**: Waveform shows blues-specific deadband (bent notes near constraints)
- **Panel 2**: Blue notes land *between* lattice points (intentional constraint violation)
- **Panel 3**: Holonomy loops around the tonic region
- **Panel 4**: 12-bar form visible in density pattern

### Comparing Multiple Pieces

```python
from constraint_viz import ConstraintOscilloscope
import os

scope = ConstraintOscilloscope()

pieces = ["classical_sonata.mid", "jazz_standard.mid", "electronic_loop.mid"]

for piece in pieces:
    name = os.path.splitext(piece)[0]
    scope.visualize_midi(
        piece,
        f"comparison/{name}_scope.png",
        title=f"Constraint Oscilloscope — {name}"
    )

# Compare the density profiles to see structural differences
```

## API Reference

### `ConstraintOscilloscope`

The main class. No configuration needed — instantiate and call `visualize_midi()`.

#### Methods

##### `visualize_midi(midi_path, output_path, title, high_res)`

Generate a full 5-panel visualization of a MIDI file.

**Parameters:**
- `midi_path` (str): Path to the input MIDI file
- `output_path` (str): Path for the output PNG image (default: `"constraint_scope.png"`)
- `title` (str | None): Custom title for the visualization (default: auto-generated from filename)
- `high_res` (bool): Output at 300 DPI instead of 150 DPI (default: `False`)

**Returns:** `str` — the path to the saved PNG file

## Performance

- A typical 3-minute MIDI file renders in **~2 seconds** at 150 DPI
- High-resolution (300 DPI) takes **~5 seconds**
- Memory usage scales with MIDI complexity; typical peak: **~50 MB**
- Batch processing: **~100 files/minute** on modern hardware

## Citation

If you use this tool in research, please cite the SuperInstance constraint theory work:

```bibtex
@software{constraint_viz_2026,
  title = {constraint-viz: Multi-Scale Constraint Visualization Oscilloscope},
  author = {SuperInstance Research},
  year = {2026},
  url = {https://github.com/SuperInstance/constraint-viz}
}
```

## License

MIT — see [LICENSE](LICENSE) for details.

---

Part of the [SuperInstance](https://github.com/SuperInstance) constraint theory ecosystem.
