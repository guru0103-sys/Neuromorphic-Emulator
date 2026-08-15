# Neuromorphic Emulator

A spiking neural network — 150 leaky integrate-and-fire (LIF) neurons wired into a random synaptic matrix — simulated with real NumPy and running entirely client-side in WebAssembly. No server, no backend, no build step. Open `index.html` in a browser and it runs.

**[Live demo →](#)** <!-- replace with your GitHub Pages URL once deployed -->

![status](https://img.shields.io/badge/status-active-brightgreen) ![runtime](https://img.shields.io/badge/runtime-WASM-00FF41) ![python](https://img.shields.io/badge/python-3.x-yellow)

---

## What it does

Inject a current burst into the first 15 neurons and watch it ripple outward through the network. Each neuron feeds its spike forward to every other neuron through a weighted synaptic matrix, so a single shock can trigger a cascade that propagates, peaks, and decays on its own — the same chain-reaction logic as a Kessler Syndrome debris cascade, just applied to a nervous system instead of an orbit.

The live canvas renders all 150 neurons in real time:
- **Color** = membrane voltage (dim green → bright green as it approaches threshold)
- **Red flash** = the neuron just fired
- Telemetry panel tracks clock time, average network voltage, and total spikes per tick

## How it's built

The project was built in three stages, each one runnable and complete before the next was layered on:

| Stage | What changed | File/scope |
|---|---|---|
| **01 · ECE Physics** | Single `LIFNeuron` class modeling the membrane as an RC circuit, integrated with Euler's method | Pure Python, terminal only |
| **02 · Matrix Math** | Rewritten with NumPy — a spike vector × an N×N weight matrix replaces per-neuron loops, scaled to 1,000 neurons | Pure Python, terminal only |
| **03 · Browser Bridge** | Same class ported unmodified into [PyScript](https://pyscript.net/), running on Pyodide (CPython + NumPy compiled to WASM). A JS loop drives `network.step()` at 30fps and paints straight to `<canvas>` | `index.html` (this repo) |

## Tech stack

- **[PyScript](https://pyscript.net/)** (`core.js` / `core.css`) — runs Python in the browser via Pyodide
- **NumPy** — loaded through PyScript's `<py-config>` package list, compiled to WASM
- **HTML5 Canvas** — 30fps render loop, driven by vanilla JS `setInterval`
- **Vanilla CSS** — Cyberpunk UI design system (matrix green on black, Fira Code / Fira Sans), generated with [ui-ux-pro-max](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)'s design system reasoning engine
- Zero build tooling, zero npm dependencies for the site itself — it's one static HTML file

## Model

Each neuron follows the standard leaky integrate-and-fire equation, updated per tick:

```
dv/dt = (1/τ) × ( -(v - v_rest) + R × I_total )
```

Where `I_total` is the sum of any external injected current plus the synaptic current arriving from every other neuron that spiked last step (`W · spikes`). When `v` crosses `v_threshold` the neuron fires, gets flagged as spiking for that tick, and resets to `v_reset`.

| Parameter | Value |
|---|---|
| Neurons (N) | 150 |
| τ (membrane time constant) | 15.0 |
| R (resistance) | 2.0 |
| v_rest | 0.0 |
| v_threshold | 1.0 |
| v_reset | 0.0 |
| Synaptic weights | `U(0, 0.08)`, diagonal zeroed (no self-connections) |
| Injected burst | neurons 0–14, current = 2.0, on "Inject" click |
| Tick rate | ~30 Hz (33ms interval) |

## Running it locally

No install required — it's a static file:

```bash
git clone <this-repo-url>
cd neuromorphic-emulator
# open index.html directly, or serve it:
python3 -m http.server 8000
```

Then visit `http://localhost:8000`, click **Boot Python Engine**, wait a few seconds for Pyodide + NumPy to load (~4MB, one-time, cached after), then **Inject Current Burst**.

## Deploying

Since it's a single static HTML file with no build step, it deploys as-is to GitHub Pages:

```bash
# Settings → Pages → Deploy from branch → main → / (root)
```

## Roadmap

- [ ] Sliders for `v_threshold` and leak rate (τ), live-editable mid-simulation
- [ ] Click-to-inject on individual neurons instead of a fixed burst
- [ ] Persist a specific cascade seed (`np.random.seed()`) for a consistent portfolio demo/GIF
- [ ] Scale render to the full 1,000-neuron network from Stage 02

## Credits

- Design system generated with [**ui-ux-pro-max**](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) — an AI skill for design intelligence (styles, palettes, typography, UX guidelines)
- Built with [PyScript](https://pyscript.net/) and [Pyodide](https://pyodide.org/)

## License

MIT — do whatever you want with it.# Neuromorphic-Emulator
