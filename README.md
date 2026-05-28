# Satellite Coverage Beam Visualizer
## 3D Real-Time Beam Footprint Visualization

*Track thousands of Starlink satellites in real time and visualize their beam-coverage footprints on Earth.*

![Coverage Demo](images/gifs/gif_example_low_res.gif)

**How many satellites can cover a given point on Earth at any moment?** This tool loads real satellite orbital data (TLE), propagates their positions in real time, and dynamically computes and renders each satellite's beam-coverage footprint — showing precisely which regions of Earth are illuminated by satellite downlink beams.

## Features

- **Live satellite tracking** — Real TLE data from Celestrak; satellite positions update every frame
- **Dynamic beam-coverage footprints** — Every satellite's beam footprint is computed in real time using the satellite altitude, Earth radius, and minimum ground-station elevation angle
- **Ground station visibility** — Pre-defined ground stations (London, Shanghai) turn bright blue when covered by a satellite beam
- **Cone beam visualization** — Thin lines from each satellite to its coverage-circle edge show the 3D beam geometry
- **High-performance kernels** — Numba-accelerated numerical computation for thousands of satellites
- **Interactive 3D viewer** — Rotate, zoom, and capture screenshots

## How Coverage Beams Are Computed

The beam footprint (coverage circle) is determined by three parameters:

| Parameter | Value | Description |
|-----------|-------|-------------|
| Satellite altitude | 550 km | Height above Earth surface |
| Earth radius | 6,371 km | For angular conversion |
| Min. elevation angle | 25 deg | Minimum look-angle above horizon for a ground user |

Using the law of sines on the Earth-centre--satellite--ground-point triangle:

- **Cone half-angle**: Maximum off-nadir angle the satellite can steer (≈53deg)
- **Earth central angle**: Angular radius of the footprint on Earth's surface (≈12deg)

Each satellite's beam footprint is rendered as a light-blue circle on the Earth's surface, with cone lines connecting the satellite to the footprint edge.

## Mouse & Keyboard Controls

- **Left-drag** — Rotate camera around Earth
- **Scroll wheel** — Zoom in/out
- **Double-click** — Save a high-res screenshot to `/images/`
- **Left-click (hold)** — Record a GIF while holding the button

## Configuration

Simulation parameters are in `simulation.py` under `DigitalTwinConfig`:

```python
@dataclass(frozen=True)
class DigitalTwinConfig:
    ground_stations: Tuple[...] = (
        ("London",   51.5074,  -0.1278),
        ("Shanghai", 31.2304, 121.4737),
    )
    gs_min_elevation_deg: float = 25.0       # minimum look-angle above horizon
    satellite_altitude_km: float = 550.0      # altitude used for footprint radius
    show_coverage: bool = True                # toggle beam-coverage visuals
    coverage_num_points: int = 48             # circle discretisation
    time_scale: float = 10.0                  # run 10x faster than real time
```

## Quick Start

```bash
python -m venv .venv
source .venv/bin/activate       # Linux/macOS
.\.venv\Scripts\activate        # Windows

pip install -r requirements.txt
python simulation.py
```

## Requirements

- Python 3.9+
- GPU/OpenGL-capable environment for Vispy rendering

## Performance Tips

- On first run, Numba JIT-compiles kernels (30--60 s delay is normal)
- Reduce `time_scale` for easier real-time computation
- Adjust `coverage_num_points` (lower = faster, coarser circles)
- Use a smaller TLE set for faster startup

## License

MIT License. See [LICENSE](LICENSE) for details.

---

Based on original work by Zhouyou Gu (SUTD), modified to focus on satellite beam-coverage visualization.
