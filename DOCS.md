# ⬡ Virtual Science Lab Platform — Project Documentation

> A single-file interactive science simulation environment built with **Three.js WebGL** and **HTML5 Canvas 2D**, styled after a professional dark-theme laboratory UI.

---

## 📁 File Inventory

| File | Size | Lines | Description |
|------|------|-------|-------------|
| `virtual-lab-poc.html` | 33 KB | 1,066 | **POC v1** — Reference design: Acid-Base Titration 3D lab (Three.js) |
| `virtual-lab-poc-2.html` | 37 KB | 1,148 | **POC v2** — Electrolysis of Water 3D lab (Three.js) |
| `virtual-lab-platform.html` | 109 KB | 1,563 | **Full Platform** — 5 sections, 10 experiments, single HTML file |

---

## 🔬 Platform Overview (`virtual-lab-platform.html`)

### Architecture

```
virtual-lab-platform.html
├── <style>          CSS variables, layout, component styles
├── <body>
│   ├── <header>     Logo · Section tabs · Status indicator
│   ├── #page-home   Lab selection grid (5 section cards)
│   └── #page-lab    Experiment view
│       ├── #canvas-wrap
│       │   ├── #lab-canvas-3d   WebGL canvas (Three.js experiments)
│       │   ├── #lab-canvas-2d   Canvas 2D experiments
│       │   ├── .overlay-label   Floating labels (lbl-a … lbl-d)
│       │   └── #progress-bar-wrap  Bottom progress bar
│       └── .sidebar
│           ├── Experiment panel  (title + description)
│           ├── Procedure Steps   (.step → .step.active → .step.done)
│           ├── Controls          (dynamic per experiment)
│           ├── Live Readings     (reading-card grid)
│           ├── Lab Log           (timestamped log entries)
│           └── Hint              (contextual tip per step)
└── <script>
    ├── APP core     (showHome, openSection, loadExp, stopCurrent)
    ├── Utilities    (addLog, setStep, finishExp, rv, setup2D, setup3D)
    ├── 10× Experiment objects
    ├── SECTIONS registry
    └── EXPERIMENTS registry
```

### Rendering Strategy

| Canvas | Used by | Lifecycle |
|--------|---------|-----------|
| `#lab-canvas-3d` | Three.js WebGL experiments | `renderer.dispose()` on switch |
| `#lab-canvas-2d` | HTML5 Canvas 2D experiments | `cancelAnimationFrame` on switch |

Both canvases are `position:absolute` and fill the container. Only one is `.active` (visible) at a time.

---

## 🧬 Section 1 — Cell Biology

### EXP-B01: Osmosis Across a Semi-Permeable Membrane
- **Renderer:** Canvas 2D
- **Physics:** van't Hoff equation `Π = iMRT` for osmotic pressure
- **Visual:** Two-chamber simulation with blue membrane, red solute particles (NaCl), blue water particles
- **Mechanics:** Water particles cross membrane stochastically based on concentration gradient; solute particles blocked
- **Steps:**
  1. Fill left chamber with NaCl solution
  2. Fill right chamber with distilled water
  3. Open membrane pores — observe osmosis
  4. Watch water levels reach osmotic equilibrium
  5. Record osmotic pressure using van't Hoff equation
- **Controls:** Solute concentration slider, membrane pore size slider, Reset button
- **Readings:** Left level (mm), Right level (mm), Osmotic pressure (atm), Equilibrium status

### EXP-B02: Mitosis — Cell Division Phases
- **Renderer:** Canvas 2D
- **Phases:** Interphase → Prophase → Metaphase → Anaphase → Telophase/Cytokinesis
- **Visual:** Animated chromosomes, spindle fibres, cell membrane division, phase dot indicator
- **Mechanics:** Timer-based phase transitions; AUTO mode advances automatically
- **Steps:**
  1. Interphase — cell prepares, DNA replicates
  2. Prophase — chromosomes condense
  3. Metaphase — chromosomes align at plate
  4. Anaphase — chromatids separate to poles
  5. Telophase & Cytokinesis — two daughter cells
- **Controls:** NEXT PHASE button, AUTO ADVANCE toggle
- **Readings:** Current phase, Division progress (%), Chromosome count, Spindle state

---

## 🌡️ Section 2 — Thermodynamics

### EXP-T01: Ideal Gas Law — PV = nRT
- **Renderer:** Canvas 2D
- **Physics:** Elastic particle collisions; particle speed ∝ √T; pressure counted from wall hits
- **Visual:** 80 bouncing particles in a container with movable piston; mini P-V diagram in corner
- **Mechanics:** Temperature slider adjusts speeds; volume piston compressed by `volPct` slider
- **Steps:**
  1. Initialise container with gas particles
  2. Observe particle motion at room temperature
  3. Increase temperature — watch pressure rise
  4. Compress the gas (reduce volume)
  5. Verify PV/nRT ≈ 1 (ideal gas constant)
- **Controls:** Temperature slider (100–1000 K), Volume % slider, Reset button
- **Readings:** Pressure (atm), Volume (L), Temperature (K), PV/nRT ratio

### EXP-T02: Fourier's Law of Heat Conduction
- **Renderer:** Canvas 2D
- **Physics:** Finite-difference heat equation: `T[i] += dt × α × (T[i-1] − 2T[i] + T[i+1])`
- **Visual:** 80-segment colour-mapped rod (blue→red gradient per temperature)
- **Materials:**
  | Material | α (mm²/s) |
  |----------|-----------|
  | Copper   | 117       |
  | Steel    | 12        |
  | Glass    | 0.5       |
- **Steps:**
  1. Select a material for the rod
  2. Set the heat source temperature
  3. Observe heat propagation along the rod
  4. Reach steady-state temperature gradient
  5. Compare α across different materials
- **Controls:** Material selector, Heat source temperature slider, Reset button
- **Readings:** Hot end temp (°C), Cold end temp (°C), Mid-point temp (°C), Diffusivity α

---

## ⚡ Section 3 — Electricity & Magnetism

### EXP-E01: Ohm's Law — V = IR
- **Renderer:** Canvas 2D
- **Physics:** `I = V/R`, `P = I²R`
- **Visual:** Circuit diagram — battery (left), ammeter (right wire), resistor (bottom zigzag), bulb (top, glows via radial gradient ∝ P); animated current dots moving clockwise at speed ∝ I
- **Steps:**
  1. Connect battery to the circuit
  2. Set the voltage (battery EMF)
  3. Adjust resistance and observe current change
  4. Note bulb brightness changes with power P = I²R
  5. Verify Ohm's Law: I = V/R at all settings
- **Controls:** Voltage slider (1–20 V), Resistance slider (10–1000 Ω)
- **Readings:** Voltage (V), Current (mA), Resistance (Ω), Power (mW)

### EXP-E02: Faraday's Law of Electromagnetic Induction
- **Renderer:** Canvas 2D
- **Physics:** `EMF ∝ −dΦ/dt`; EMF computed as `velocity × turns × strength × polarity`
- **Visual:** Draggable bar magnet, coil (overlapping ellipses), galvanometer with rotating needle, field lines as Bezier curves
- **Mechanics:** Magnet draggable via `onmousedown/onmousemove`; FLIP POLARITY button reverses sign
- **Steps:**
  1. Place magnet near coil — field lines visible
  2. Move magnet toward coil — observe EMF
  3. Move magnet away — EMF reverses
  4. Flip magnet polarity — reversed response
  5. Increase coil turns — larger induced EMF
- **Controls:** Coil turns slider (10–200), Magnet strength slider, FLIP POLARITY button
- **Readings:** EMF (mV), Galvanometer deflection (°), Coil turns, Magnet velocity (cm/s)

---

## ⚗️ Section 4 — Chemistry  *(Three.js 3D WebGL)*

### EXP-C01: Acid-Base Titration with pH Indicator
- **Renderer:** Three.js WebGL (`#lab-canvas-3d`)
- **Scene:** Erlenmeyer flask (LatheGeometry), beaker, retort stand, burette, Bunsen burner with flame particle system
- **Physics:** pH calculated from moles of acid and base; phenolphthalein indicator switches colour at equivalence point
- **Visual:** Solution colour shifts from clear → pale pink → magenta at pH ≈ 8.2; orbit controls (left-drag rotate, right-drag pan, scroll zoom)
- **Steps:**
  1. Add HCl solution to Erlenmeyer flask
  2. Add phenolphthalein indicator drops
  3. Fill burette with NaOH solution
  4. Begin slow titration — watch pH
  5. Stop at pink endpoint (pH ≈ 8.2)
- **Controls:** NaOH flow rate slider, ADD INDICATOR button, START TITRATION / STOP / RESET
- **Readings:** pH, NaOH added (mL), Moles HCl, Equivalence status
- **Cleanup:** `renderer.dispose()` + window event listener removal on section switch

### EXP-C02: Electrolysis of Water — Faraday's Law
- **Renderer:** Three.js WebGL (`#lab-canvas-3d`)
- **Scene:** Transparent electrolytic cell (5 glass panels), carbon electrodes with coloured bands, DC PSU with CatmullRomCurve3 + TubeGeometry wires
- **Physics:** H₂ : O₂ bubble ratio = 2 : 1 (stoichiometric); bubble rate ∝ current
- **Visual:** Bubbles (SphereGeometry) rise from each electrode; H₂ bubbles blue (cathode), O₂ bubbles red (anode)
- **Steps:**
  1. Fill electrolytic cell with distilled water
  2. Add Na₂SO₄ electrolyte — improve conductivity
  3. Insert carbon electrodes into solution
  4. Connect DC power — increase voltage, observe bubbles
  5. Record H₂:O₂ ratio — confirm water formula
- **Controls:** Voltage slider (0–24 V), POWER ON/OFF toggle
- **Readings:** Voltage (V), Current (A), H₂ volume (mL), O₂ volume (mL)
- **Cleanup:** `renderer.dispose()` + window event listener removal on section switch

---

## 🔭 Section 5 — Physics

### EXP-P01: Projectile Motion — Kinematic Equations
- **Renderer:** Canvas 2D
- **Physics:**
  ```
  x = v₀·cos(θ)·t
  y = v₀·sin(θ)·t − ½·g·t²
  Air resistance: F_drag = −k·v  (k = 0.08, toggle)
  ```
- **Visual:** Cannon at bottom-left; parabolic trajectory drawn in real time; up to 5 faded past trajectories; range/peak markers
- **Steps:**
  1. Set launch angle using slider
  2. Set initial velocity
  3. Press FIRE to launch projectile
  4. Observe parabolic trajectory and peak
  5. Measure range, height, and flight time
- **Controls:** Angle slider (0–90°), Velocity slider (10–100 m/s), Air resistance toggle, FIRE button
- **Readings:** Range (m), Max height (m), Flight time (s), Impact velocity (m/s)

### EXP-P02: Young's Double-Source Wave Interference
- **Renderer:** Canvas 2D (`ImageData` pixel-by-pixel)
- **Physics:**
  ```
  A(x,y) = Σ amp·sin(k·d − ω·t) / (1 + d·0.01)
  Path difference Δd = |d₁ − d₂|
  Constructive: Δd = nλ  |  Destructive: Δd = (n+½)λ
  ```
- **Visual:** Interference pattern rendered via `putImageData` every 3rd frame; two draggable source points (S1 blue, S2 green)
- **Mechanics:** Cursor shows live Δd and CONSTRUCTIVE/DESTRUCTIVE label; S1/S2 toggle buttons
- **Steps:**
  1. Place source S1 in the medium
  2. Position S2 at separation d
  3. Activate both sources
  4. Observe nodal lines (dark) and antinodes (bright)
  5. Measure path difference at various points
- **Controls:** Frequency slider (1–10 Hz), Amplitude slider, S1 ON/OFF, S2 ON/OFF
- **Readings:** Frequency (Hz), Wavelength (cm), Source separation (cm), Path diff at cursor (cm)

---

## 🎨 Design System (matching `virtual-lab-poc.html`)

### CSS Variables

```css
--bg:       #0a0e1a   /* Page background          */
--surface:  #111827   /* Panel background          */
--surface2: #1a2236   /* Input / card background   */
--border:   rgba(99,179,237,0.15)
--accent:   #63b3ed   /* Primary blue              */
--accent2:  #68d391   /* Green (success/biology)   */
--accent3:  #f6ad55   /* Orange (warning/thermo)   */
--danger:   #fc8181   /* Red (error/physics)       */
--text:     #e2e8f0
--muted:    #718096
```

### Typography

```css
--mono: 'Space Mono', monospace   /* Labels, readings, code    */
--sans: 'DM Sans', sans-serif     /* Body text, descriptions   */
```

### Step States

```
.step          → upcoming (muted dot)
.step.active   → current  (accent blue dot, highlighted text)
.step.done     → complete (green dot, strikethrough)
```

### Log Entry Classes

```
(none)       → default white  — system messages
.log-entry   → green          — success events
.log-warn    → orange         — warnings
.log-err     → red            — errors
```

### Reading Value States

```
.reading-value         → default white
.reading-value.hot     → red   (high temperature, danger)
.reading-value.good    → green (target reached, success)
```

---

## 🔧 App Core API

```javascript
// Navigation
showHome()              // Return to section selection grid
openSection(sid)        // sid: 'biology'|'thermo'|'electricity'|'chemistry'|'physics'
loadExp(expId)          // expId: 'bio/osmosis', 'chem/titration', etc.
stopCurrent()           // Cancel animation + call exp.cleanup()

// Sidebar
addLog(msg, cls)        // cls: ''|'log-entry'|'log-warn'|'log-err'
setStep(idx, hints)     // Advance step indicator + update hint + progress bar
finishExp()             // Set progress to 100%, mark done
rv(id, val, cls)        // Update a reading-value element

// Canvas Setup
setup2D()               // → { canvas, ctx, W, H }  (shows #lab-canvas-2d)
setup3D()               // → canvas element          (shows #lab-canvas-3d)
```

### Experiment Object Shape

```javascript
const MyExp = {
  shortName: 'My Exp',          // Used in tab buttons
  badge:     'EXP-X01: TITLE',  // Status bar text
  title:     'Full Title',       // Sidebar header
  desc:      'Description...',   // Sidebar subtitle
  steps:     ['Step 1', ...],    // Procedure list
  hints:     ['Hint 1', ...],    // One per step
  labels:    [{ id:'lbl-a', text:'Label', top:'30%', left:'20%' }],
  axesHint:  'LMB: rotate\nScroll: zoom',  // 3D only
  controlsHTML: `<label>...</label>`,
  readings:  [{ id:'r-voltage', label:'Voltage', unit:'V', init:'0', cls:'' }],
  init()    { /* start animation loop, set APP.animId */ },
  cleanup() { /* dispose renderer, remove listeners   */ },
};
```

---

## 📦 Dependencies

| Library | Version | CDN |
|---------|---------|-----|
| Three.js | r128 | `https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js` |
| Space Mono | — | Google Fonts |
| DM Sans | — | Google Fonts |

> No build step required. Open any `.html` file directly in a browser.

---

## 🚀 Usage

```bash
# Open the full platform
open /Users/hn/Downloads/virtual-lab-platform.html

# Open individual POC files
open /Users/hn/Downloads/virtual-lab-poc.html     # Titration only
open /Users/hn/Downloads/virtual-lab-poc-2.html   # Electrolysis only
```

1. The home page shows **5 section cards** — click any to enter
2. Within a section, switch experiments using the **tab buttons** in the header
3. Follow the **Procedure Steps** in the right sidebar
4. Adjust sliders/buttons in the **Controls** panel
5. Monitor values in **Live Readings** and log entries below
6. Click the **⬡ VIRTUALLAB** logo to return home

---

## 📐 Experiment Registry

```javascript
const SECTIONS = {
  biology:     { icon:'🧬', label:'BIOLOGY',        color:'#68d391', exps:['bio/osmosis','bio/mitosis'] },
  thermo:      { icon:'🌡️', label:'THERMODYNAMICS', color:'#f6ad55', exps:['thermo/gas','thermo/heat'] },
  electricity: { icon:'⚡', label:'ELECTRICITY',    color:'#63b3ed', exps:['elec/ohm','elec/induction'] },
  chemistry:   { icon:'⚗️', label:'CHEMISTRY',      color:'#b794f4', exps:['chem/titration','chem/electrolysis'] },
  physics:     { icon:'🔭', label:'PHYSICS',        color:'#fc8181', exps:['phys/projectile','phys/waves'] },
};

const EXPERIMENTS = {
  'bio/osmosis':       OsmosisExp,
  'bio/mitosis':       MitosisExp,
  'thermo/gas':        GasExp,
  'thermo/heat':       HeatExp,
  'elec/ohm':          OhmExp,
  'elec/induction':    InductionExp,
  'chem/titration':    TitrationExp,
  'chem/electrolysis': ElectrolysisExp,
  'phys/projectile':   ProjectileExp,
  'phys/waves':        WaveExp,
};
```

---

*Generated: 2026-05-17 · Virtual Lab Platform v2*
