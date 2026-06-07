# ⬡ Virtual Lab Platform — Feature Roadmap (P1 & P2)

> Detailed implementation plan for the next two milestones.
> Base: `virtual-lab-platform.html` (1,563 lines, 5 sections, 10 experiments).

---

## 🎯 Milestone P1 — Pedagogy & Learning Value

Goal: turn the simulator from a *toy* into a *teaching tool*. Students learn, get tested, and keep their progress.

### P1.1 — Quiz / Assessment Engine

**What:** Each experiment gets a 3–5 question quiz unlocked after completing all steps.

**Data shape:**
```javascript
const MyExp = {
  ...,
  quiz: [
    {
      q: 'If voltage doubles and resistance stays fixed, current...',
      options: ['Doubles', 'Halves', 'Stays same', 'Quadruples'],
      answer: 0,
      explain: 'I = V/R. V↑2 → I↑2.'
    },
  ],
};
```

**UI:**
- New sidebar panel `Assessment` (hidden until `finishExp()` fires).
- Radio options → SUBMIT → score + per-question explanation (green/red).
- Score saved to progress (P1.4).

**Files touched:** core (`loadExp`, new `renderQuiz()`), each experiment object (+`quiz` array).
**Effort:** M (1 engine + 10× quiz content).

---

### P1.2 — Data Export + Live Graphs

**What:** Record live readings over time → plot in real time → export CSV.

**Mechanics:**
- Ring buffer per reading: `APP.dataLog = { time:[], series:{} }`.
- Sample every N frames (throttle to ~10 Hz).
- Mini line chart in canvas corner OR dedicated `<canvas>` graph panel.
- `EXPORT CSV` button → Blob download `exp-<id>-<timestamp>.csv`.

**Reusable chart helper:**
```javascript
function drawGraph(ctx, x, y, w, h, series, opts) { /* axes + polyline */ }
```

**Best targets first:** Ohm (I vs V), Gas (P vs V), Heat (T vs position), Projectile (trajectory already plotted → just add export).

**Files touched:** core (data logger + chart util + CSV export), experiments opt-in via `exp.logKeys = ['r-voltage','r-current']`.
**Effort:** M.

---

### P1.3 — Guided vs Free-Play Mode

**What:** Toggle between locked step-by-step (guided) and open sandbox (free).

**Behavior:**
| Mode | Steps | Controls |
|------|-------|----------|
| Guided | Locked — must complete in order | Only current-step controls enabled |
| Free | All unlocked | All controls live |

**UI:** Header toggle `🎓 Guided / 🔧 Free`. Persists per session.

**Files touched:** core (`APP.mode`, gate `setStep` advancement + control `disabled` attr).
**Effort:** S.

---

### P1.4 — Progress Save (localStorage)

**What:** Remember completion + quiz scores across visits.

**Schema:**
```javascript
localStorage['vlab.progress'] = JSON.stringify({
  'bio/osmosis':  { completed:true,  bestScore:80, lastVisit:'2026-05-17' },
  'elec/ohm':     { completed:false, bestScore:0 },
});
```

**UI:**
- Home cards show ✓ badge + score ring when complete.
- Section progress bar ("3/2 done").
- `RESET PROGRESS` in a settings menu.

**Files touched:** core (load/save helpers, home card render), `finishExp` + quiz submit write progress.
**Effort:** S.

---

### P1 Acceptance Criteria
- [ ] Every experiment has a working quiz with explanations.
- [ ] At least 4 experiments log + graph + export CSV.
- [ ] Guided/Free toggle gates controls correctly.
- [ ] Refresh browser → progress + scores persist.
- [ ] Home grid reflects completion state.

**P1 total effort:** ~1 engine sprint + content pass.

---

## 🧪 Milestone P2 — Breadth (More Experiments)

Goal: grow from 10 → ~20 experiments. Reuse existing `setup2D`/`setup3D` + experiment object pattern. No core rewrite.

### P2 New Experiments

| # | Section | Experiment | Renderer | Core Physics | Effort |
|---|---------|-----------|----------|--------------|--------|
| 1 | 🧬 Biology | Photosynthesis Rate | Canvas 2D | Rate vs light/CO₂/temp; O₂ bubble count | M |
| 2 | 🧬 Biology | Enzyme Kinetics | Canvas 2D | Michaelis–Menten `v = Vmax·[S]/(Km+[S])` | M |
| 3 | 🧬 Biology | DNA Transcription | Canvas 2D | Base-pairing A-U/G-C, mRNA build animation | M |
| 4 | ⚗️ Chemistry | Reaction Rates | Canvas 2D | Collision theory; conc/temp/catalyst sliders | M |
| 5 | ⚗️ Chemistry | Flame Test | Canvas 2D | Emission colors per metal ion (Na/K/Cu/Ca) | S |
| 6 | 🔭 Physics | Simple Pendulum | Canvas 2D | `T = 2π√(L/g)`; damping; phase plot | S |
| 7 | 🔭 Physics | Lens & Optics | Canvas 2D | Ray-trace; `1/f = 1/v − 1/u`; real/virtual image | M |
| 8 | 🔭 Physics | 2D Collisions | Canvas 2D | Momentum + KE conservation; elastic/inelastic | M |
| 9 | ⚡ Electronics | RC Circuit | Canvas 2D | Charge/discharge `V=V₀(1−e^(−t/RC))` | S |
| 10 | ⚡ Electronics | Logic Gates | Canvas 2D | AND/OR/NOT/XOR; truth table; wire toggle | S |

### Per-experiment build checklist
For each new experiment:
1. Create `XxxExp` object — `shortName, badge, title, desc, steps[], hints[], labels[], controlsHTML, readings[], init(), cleanup()`.
2. Implement sim loop using `setup2D()`/`setup3D()`.
3. Register in `EXPERIMENTS{}` map.
4. Add id to relevant `SECTIONS[x].exps[]`.
5. (If P1 shipped) add `quiz[]` + `logKeys[]`.

### New section?
Electronics (#9–10) → add 6th section card:
```javascript
electronics: { icon:'🔌', label:'ELECTRONICS', color:'#4fd1c5',
               exps:['elec2/rc','elec2/logic'] },
```

### P2 Acceptance Criteria
- [ ] 10 new experiments live, each with steps + readings + controls.
- [ ] All reuse existing core (no `loadExp`/`stopCurrent` changes).
- [ ] Home grid shows 6 sections, ~20 experiments.
- [ ] Each new exp cleans up (no leaked animation loops on switch).

**P2 total effort:** content-heavy; ~10 experiment builds, batchable in pairs.

---

## 📦 Suggested Sequence

```
P1.4 (progress save)      ← smallest, unblocks UI badges
  → P1.3 (modes)          ← small
  → P1.1 (quiz engine)    ← medium, depends on progress save
  → P1.2 (graphs/export)  ← medium, independent
  → P2 experiments        ← batch in pairs, each independent
```

**Why this order:** P1 infra (save + quiz + graph) is reused by every P2 experiment. Build the rails once, then mass-produce experiments on top.

---

## 🚧 Cross-Cutting (do alongside)
- **Refactor:** split monolith HTML → `core.js` + `experiments/*.js` before P2 (file getting large).
- **Mobile/touch:** sliders + orbit controls need touch events (blocks real classroom use).
- **Deploy:** push to static host for a real shareable link.

---

*Generated: 2026-05-17 · Roadmap v1 · targets P1 + P2*
