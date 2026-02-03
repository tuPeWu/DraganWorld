# DraganWorld

**DraganWorld** is a browser-based, agent-based micro-world evolution simulator.
It visualizes emergent population dynamics driven solely by local interaction,
aging, death, and reproduction — without external population control.

> Inspired by A. Dragan’s *“QuoVaidis”*

---

## Concept

The system simulates autonomous individuals moving in a bounded 2D space.
Each individual:
- ages,
- may reproduce when encountering another individual,
- eventually dies.

Global population behavior emerges entirely from local rules.

---

## Features

- Pixel-art / retro-80s visual style
- Deterministic simulation with seed locking
- Live parameter control via sliders
- Real-time population graph
- No backend, no server — 100% client-side

---

## Tech Stack

- React + TypeScript
- Vite
- Canvas rendering
- shadcn/ui (generated via Figma Make)

---

## Project Structure

```text
src/
├─ components/
│  ├─ ui/                 # shadcn/ui – auto-generated
│  ├─ ControlPanel.tsx    # experiment parameters
│  ├─ SimulationCanvas.tsx
│  ├─ PopulationGraph.tsx
│  └─ Header.tsx
├─ sim/                   # simulation engine (hand-written)
│  ├─ engine.ts
│  ├─ model.ts
│  ├─ rng.ts
│  ├─ spatialHash.ts
│  ├─ render.ts
│  └─ metrics.ts
├─ styles/
│  └─ globals.css
└─ App.tsx
