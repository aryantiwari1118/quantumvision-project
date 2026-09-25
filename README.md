# QuantumVision — Setup Guide (VS Code)

QuantumVision is a single self-contained web app: `index.html` contains all the HTML, CSS, and JavaScript (Schrödinger-equation solver, neural network, and UI). There's no build step, no `npm install`, and no server required — but the steps below cover the easiest ways to run and edit it in VS Code.

## What's in this folder

```
quantumvision/
├── index.html      ← the entire app (physics engine + ML model + UI)
└── README.md       ← this file
```

## Option 1 — Just open it (fastest)

1. Unzip this folder anywhere on your computer.
2. Double-click `index.html`.

It opens directly in your default browser and works fully offline (it only reaches out to Google Fonts for typefaces — everything else, including the physics simulation and the neural network, runs entirely in your browser).

## Option 2 — Open in VS Code with Live Server (recommended for editing)

Running it through a local server avoids occasional browser restrictions on local files and auto-reloads the page whenever you save changes.

1. **Install VS Code** if you don't have it: https://code.visualstudio.com
2. **Open the folder**:
   - Launch VS Code
   - `File → Open Folder…` and select the unzipped `quantumvision` folder
3. **Install the "Live Server" extension**:
   - Click the Extensions icon in the sidebar (or press `Ctrl+Shift+X` / `Cmd+Shift+X`)
   - Search for **Live Server** (by Ritwick Dey)
   - Click **Install**
4. **Launch the app**:
   - Right-click `index.html` in the file explorer
   - Select **Open with Live Server**
   - Your browser opens automatically at something like `http://127.0.0.1:5500`

Any time you edit `index.html` and save, the page will auto-refresh.

## Option 3 — Any other local server

If you'd rather not install a VS Code extension, any static file server works, e.g. from a terminal inside the folder:

```bash
# Python 3
python3 -m http.server 8000
# then open http://localhost:8000
```

```bash
# Node.js (no install needed)
npx serve .
```

## Using the app

1. Click **Launch Simulation** in the hero section (or just scroll down).
2. Adjust the system controls if you'd like — potential type, mass, wave-packet position/width/momentum, simulation duration.
3. Click **Generate & Train**. This will, in order:
   - Solve the time-dependent Schrödinger equation numerically (split-step Fourier method)
   - Build a training set of consecutive wave-function states from that trajectory
   - Train a small neural network to predict the next state from the current one
   - Run that trained network autoregressively to produce its own predicted trajectory
4. Use **▶ / ⏸ / ↻** and the timeline slider to play back **Exact Physics** vs **ML Prediction** vs **Comparison**.
5. Check the **Live Metrics** panel and the **Physics Validation** section at the bottom for normalization, RMSE, and training/validation loss — all computed live from the actual simulation, nothing hardcoded.

## Notes for developers

- Everything is vanilla JavaScript — no frameworks, no build tools. The neural network (a small multilayer perceptron) and its training loop (Adam optimizer with backpropagation) are hand-written in the `<script>` block, as is the FFT used by the split-step Fourier solver.
- All rendering is done with the HTML5 Canvas 2D API — no charting library dependency.
- To change simulation parameters (grid size, number of frames, network size, epochs, etc.), look for the constants near the top of the `<script>` block: `N`, `N_FRAMES`, `SUBSTEPS`, and the `MLP([...])` architecture array inside `generateAndTrain()`.
- The five built-in potentials (free particle, harmonic oscillator, barrier, finite well, double well) are defined in the `potentialAt()` function — adding a new one just means adding another branch there and a matching entry in the `PRESETS` object.

## Requirements

- Any modern browser (Chrome, Firefox, Safari, Edge)
- VS Code is only needed if you want to edit the code — the app itself needs nothing installed
