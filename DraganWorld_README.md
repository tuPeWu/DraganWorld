# DraganWorld — interactive micro-world evolution simulator (web)

## Goal
Turn the existing Figma-Make-generated UI into a **working web app** that runs an **agent-based population simulation** with **time-stepped evolution**, rendered in **pixel/retro 80s style**. UI controls (sliders/buttons) must affect the simulation **live**.

**Definition of Done**
1. App runs locally with one command, and builds for production.
2. Central viewport shows a moving population in a bounded world.
3. Start / Pause / Reset works.
4. Sliders change parameters (immediately or on Reset, as specified below).
5. Simulation logic matches the rules in “Simulation Model”.
6. A small pixel-style line chart shows population over time.
7. “Seed locked” enables repeatable runs (same starting state -> same results).
8. No external backend. All client-side.

## Tech decisions (must follow)
- Stack: **Vite + React + TypeScript**.
- Rendering: **HTML `<canvas>`** (single canvas for world + optional overlay).
- State management: React state for UI; simulation runs in its own loop module.
- Chart: lightweight (canvas mini-chart) or a small inline SVG; keep retro look.
- Styling: keep existing Figma styles; do not “modernize” UI.

## Project structure (target)
- `src/`
  - `sim/`
    - `engine.ts` (core loop, stepping, seed, deterministic RNG)
    - `model.ts` (types, parameters, initialization, reproduction, death)
    - `rng.ts` (seeded RNG)
    - `render.ts` (canvas drawing, CRT overlay, debug overlay)
  - `ui/`
    - `controls.tsx` (sliders, buttons, seed lock)
    - `hud.tsx` (counters, legend, mini chart)
  - `App.tsx` (wires UI to sim)
- Add any files needed, but keep separation: UI vs sim vs render.

## Simulation model (must implement)

### Entities
Each agent (individual) has:
- `id: number`
- `x, y: number` (continuous 2D space)
- `age: number` (integer steps)
- `lifespan: number` (integer steps)
- `bornAt: number` (step index)
- `reproCooldown: number` (steps remaining until can reproduce; derived from min age + reproduction time)
- `lastReproAt: number | null`
- Optional debug flags.

### Time
Simulation proceeds in discrete steps `t = 0..T`.
At each step:
1. Move agents
2. Handle encounters + reproduction
3. Age increment + death removal
4. Emit metrics (population, births, deaths)

### World / environment
- World is a bounded rectangle (default: width=320, height=180 “pixels-as-world-units”).
- Agents move with speed limited by `movementIntensity`.
- Movement is stochastic: biased random walk with optional boundary bounce.
- Local interaction range `interactionRange` (radius). Two agents “meet” if distance <= range.

### Reproduction rules
- Reproduction requires **two agents** within interaction range.
- Agents must be **eligible**:
  - `age >= minReproAge`
  - `t - bornAt >= timeToReproduce` (or equivalently, a cooldown that ends after these steps)
  - Not in reproduction cooldown.
- When two eligible agents meet, they reproduce with probability `reproProbability` (default 1.0 unless slider says otherwise; if no slider, keep 1.0).
- Reproduction creates one new agent:
  - Spawn position = midpoint + small jitter.
  - New agent has `age=0`, `bornAt=t`, and its own `lifespan` inherited as below.
- After reproduction, both parents enter cooldown of `reproCooldownSteps` (default = `timeToReproduce`, unless you decide separate; if separate slider not present, reuse `timeToReproduce`).

### Inheritance + mutation (lifespan)
Child `lifespan` is derived from parents:
- Base: mean of parents’ lifespans OR pick one parent with `inheritRandomness` weight.
- `inheritRandomness` in [0..1]:
  - 0 = deterministic mean
  - 1 = choose random parent
  - intermediate = convex blend between mean and random parent
- Mutation:
  - `mutationAmount` in [0..1] maps to a max delta in steps, e.g. `maxDelta = round(mutationAmount * 40)`
  - childLifespan = clamp(base + randomInt(-maxDelta, +maxDelta), min=5, max=500)
- This mutation is the only variability needed.

### Selection rule
- Agents that die without reproducing do not pass parameters (already implied by reproduction-only inheritance).
- No external population cap besides what emerges from rules.

### Experimental controls
- Iterations / speed:
  - `maxSteps` (or run indefinitely)
  - `simSpeed` slider controls steps per second.
- Initial conditions:
  - `initialPopulation` agents created at t=0.
  - If **Seed locked** is ON, the same seed must generate the same initial positions and initial lifespans.

## UI controls mapping (must be wired)
Implement sliders (with sensible ranges + defaults):
1. `initialPopulation` (default 200, range 10..2000)
2. `avgLifespan` (default 120, range 10..400)
3. `mutationAmount` (default 0.15, range 0..1)
4. `interactionRange` (default 6, range 1..30)
5. `minReproAge` (default 20, range 0..200)
6. `timeToReproduce` (default 20, range 0..200)
7. `movementIntensity` (default 1.0, range 0..5, float)
8. `inheritRandomness` (default 0.5, range 0..1)
9. `simSpeed` (default 30 steps/sec, range 1..240)
10. `maxSteps` (default 0=unlimited, range 0..20000) OR a “Stop at” input.

If some of these controls are missing in the exported UI, add them in the side panel while matching style.

### Apply behavior
- Changing `simSpeed` affects loop immediately.
- Changing other parameters:
  - Either apply immediately to future births/moves OR require Reset.
  - Preferred: apply immediately for movement/interaction; apply on Reset for `initialPopulation` and seed-dependent initialization.
  - Show small label near slider: “live” vs “on reset”.

## Visual requirements
- Pixel/retro look:
  - Use integer scaling for canvas (e.g., internal 320x180 scaled up 3–5x with `image-rendering: pixelated`).
  - Agents as 1–3 px sprites.
  - CRT overlay: subtle scanlines + noise (optional, low-cost).
- Color semantics:
  - Age gradient: young brighter, old dimmer (or vice versa).
  - Repro-eligible agents have a small highlight (single pixel outline or color shift).
- Legend: tiny key for colors/states.
- HUD:
  - Live counters: population, births, deaths, step t.
  - Mini-chart: population over last N steps (N=300 default).
- Debug toggle:
  - Overlay interaction range circles for a few sampled agents OR show grid + range ring.
  - Keep cheap; do not draw 2000 circles every frame.

## Performance targets
- Must remain interactive up to ~2000 agents at 30 steps/sec on a typical laptop.
- Use spatial partitioning for neighbor search:
  - Uniform grid hashing (cell size = interactionRange or slightly larger).
  - Avoid O(n^2).

## Determinism
- Use a seeded RNG for:
  - initialization positions
  - initial lifespans (around avgLifespan with small spread)
  - movement jitter
  - mutation
- When Seed locked is ON:
  - Reset must recreate identical run (given same parameter values).
- When OFF:
  - Reset uses new random seed.

## Build & run
- Provide:
  - `npm install`
  - `npm run dev`
  - `npm run build`
  - `npm run preview`

## Testing (lightweight, required)
Add minimal checks:
- A small deterministic test in `src/sim/`:
  - same seed + params -> same first 10 population counts.
- Optional: eslint/typecheck if already present.

## Tasks for Codex (execute in order)
1. Inspect existing UI code and locate controls/components.
2. Create `src/sim/*` modules and implement engine + model + RNG + spatial hashing.
3. Add canvas renderer and wire animation loop.
4. Wire UI controls to sim parameters (live vs reset behavior).
5. Implement Start/Pause/Reset and Seed locked.
6. Add HUD counters + mini population chart.
7. Add debug overlay toggle.
8. Ensure pixel scaling + `image-rendering: pixelated`.
9. Add deterministic test and ensure build passes.

## Non-goals
- No server, no database, no multiplayer.
- No heavy dependencies.
- Do not replace the retro aesthetic with modern UI kits.

## Branding text (must show in header)
- Title: `DraganWorld`
- Subtitle: `Inspired by A. Dragan's "QuoVaidis"`

