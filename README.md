# DraganWorld

**DraganWorld** is a browser-based, agent-based micro-world evolution simulator.
It visualizes emergent population dynamics driven by local interactions, aging,
death, and reproduction — with a retro, pixel-art interface.

**Subtitle:** Inspired by A. Dragan’s *“QuoVaidis”*

---

## Concept

The simulation models autonomous individuals in a bounded 2D space.
Each individual:
- moves stochastically,
- ages over discrete time steps,
- may reproduce upon encountering another eligible individual,
- eventually dies.

Population behavior emerges from local rules (no external population cap).

---

## Features

- Pixel/retro 80s visualization (canvas, pixelated scaling)
- Parameter control via sliders (live + reset-only parameters)
- Start / Pause / Reset controls
- Seed lock for deterministic repeatable runs
- Real-time population history graph
- 100% client-side (no backend)

---

## Tech Stack

- React + TypeScript
- Vite
- Canvas rendering
- shadcn/ui (generated via Figma Make)

---

## Repository Structure

```text
src/
├─ components/
│  ├─ figma/
│  │  └─ ImageWithFallback.tsx
│  ├─ ui/                  # shadcn/ui (library) – do not edit for sim logic
│  ├─ Header.tsx
│  ├─ LeftPanel.tsx
│  ├─ ParametersPanel.tsx
│  ├─ ControlPanel.tsx
│  ├─ SimulationCanvas.tsx
│  ├─ PopulationGraph.tsx
│  ├─ RetroButton.tsx
│  └─ RetroSlider.tsx
├─ guidelines/
│  └─ Guidelines.md
├─ sim/                    # simulation engine (hand-written)
│  ├─ engine.ts
│  ├─ model.ts
│  ├─ rng.ts
│  ├─ spatialHash.ts
│  ├─ render.ts
│  ├─ metrics.ts
│  └─ types.ts
├─ styles/
│  └─ globals.css
├─ App.tsx
└─ Attributions.md
