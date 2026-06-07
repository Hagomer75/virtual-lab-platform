# ⬡ Virtual Science Lab Platform

Interactive science simulation lab — **5 sections, 10 experiments** — built with **Three.js WebGL** + **HTML5 Canvas 2D** in a single static HTML file. No build step.

## Sections & Experiments

| Section | Experiments |
|---------|-------------|
| 🧬 Biology | Osmosis · Mitosis |
| 🌡️ Thermodynamics | Ideal Gas Law · Heat Conduction |
| ⚡ Electricity & Magnetism | Ohm's Law · EM Induction |
| ⚗️ Chemistry (3D) | Acid-Base Titration · Electrolysis of Water |
| 🔭 Physics | Projectile Motion · Wave Interference |

## Features

- Real-time interactive simulations with sliders, buttons, live readings
- **Procedure steps** + contextual **hints** per experiment
- **Assessment quizzes** — scored, with explanations (P1)
- **Guided / Free** mode toggle (P1)
- **Progress save** across visits via `localStorage` + completion badges (P1)
- **Live data plot** + **CSV export** of readings (P1)
- 3D chemistry labs with orbit controls (drag rotate · scroll zoom · right-drag pan)

## Run locally

Open `index.html` in any modern browser. Or serve:

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

## Deploy

Static site — deploys to Vercel / Netlify / GitHub Pages as-is (`index.html` at root).

## Docs

- [`DOCS.md`](DOCS.md) — full architecture + per-experiment physics reference
- [`ROADMAP.md`](ROADMAP.md) — P1 (pedagogy) + P2 (more experiments) plan

## Tech

Three.js r128 · HTML5 Canvas 2D · Space Mono + DM Sans · vanilla JS, zero dependencies to install.
