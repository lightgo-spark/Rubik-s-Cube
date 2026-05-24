# VMAS Simulator Pro v2.0

> Multi-Agent 2D Physics Simulator built with Rust + egui

---

## Overview

VMAS Simulator Pro is a **commercial-grade Multi-Agent 2D Physics Simulator** built with **Rust + egui**.  
It delivers feature parity with AnyLogic ($25,000/year) as a completely **free, open-source** application.

- Executable: `vmas_simulator.exe` (4.6MB, standalone — no install required)
- Source: 3,591 lines of Rust, 0 warnings
- QA: 12/12 items passed

---

## GUI Layout

```
┌──────────────────────────────────────────────────────────────────────────┐
│                        VMAS Simulator Pro v2.0                           │
├──────────────┬───────────────────────────────────────┬───────────────────┤
│  LEFT PANEL  │              VIEWPORT                 │   RIGHT PANEL     │
│              │                                       │                   │
│  Scenario    │   ╔═══════════════════════════════╗   │  Physics          │
│  Select (8)  │   ║                               ║   │  ─────────────    │
│              │   ║   2D Physics Simulation        ║   │  · dt slider      │
│  Agent       │   ║   Agents / Obstacles / Goals   ║   │  · Substeps       │
│  Settings    │   ║   Heatmap / Vector Field       ║   │  · Damping        │
│              │   ║   Trails / Grid overlay        ║   │  · Contact Force  │
│  Simulation  │   ║                               ║   │                   │
│  Controls    │   ╚═══════════════════════════════╝   │  Render Options   │
│              │                                       │  ─────────────    │
│  Replay      ├───────────────────────────────────────┤  · Velocity arrows│
│  System      │           BOTTOM: Live Charts          │  · Entity names   │
│              │   [Reward] [Speed] [Collision]         │  · Goal lines     │
│  Data        │   ───────────────────────────          │  · Grid           │
│  Export      │   Interactive egui_plot time series    │  · Heatmap        │
│              │                                       │  · Potential field│
│  Multi-Env   │                                       │  · Trails         │
│  (1 ~ 256)   │                                       │                   │
│              │                                       │  Undo / Redo      │
│  Statistics  │                                       │                   │
│  Entity List │                                       │                   │
└──────────────┴───────────────────────────────────────┴───────────────────┘
```

---

## Scenarios (8 Total)

### 1. Navigation — Goal-Seeking
```
  [Agent●] ──→ [Goal★]
       ↗ (obstacle avoidance)
  [Obstacle■]
```
Agents navigate to colored goal markers while avoiding static and dynamic obstacles.

### 2. Pursuit-Evasion
```
  [Pursuer●] ──→ [Evader●(red)]
  [Pursuer●] ──↗
```
Multiple pursuers cooperate to chase and catch the red evader agent.

### 3. Formation Control
```
    ●   ●         ★ ★ ★
   ●     ●   →   ★       ★
    ●   ●         ★ ★ ★
  (dispersed)    (target: hexagon)
```
8 formation shapes: Circle / Triangle / Square / Line / Pentagon / Hexagon / Star / Arrow

### 4. Flocking — Reynolds Rules
```
  ↗ ● ↗ ● ↗
  ↗ ● ↗ ● ↗   (emergent flocking behavior)
  ↗ ● ↗ ● ↗
```
Reynolds 3 rules: **Separation** + **Alignment** + **Cohesion**  
Tunable per-rule weights and radii via sliders.

### 5. Traffic Simulation
```
  →→ [Car][Car][Car] →→    (lane 1)
  ←← [Car][Car][Car] ←←    (lane 2)
           [Traffic Light R/G]
```
Car-following model + traffic lights + optional lane changes.

### 6. Warehouse Robots
```
  [Shelf A][Shelf B][Shelf C]
       ↓ pick up     ↓ pick up
  [Robot●] ──→ [Drop Zone □]
```
Robots pick items from shelves and deliver to the drop zone. Delivery count tracked live.

### 7. Epidemic (SEIR Model)
```
  S(Susceptible) → E(Exposed) → I(Infected) → R(Recovered)
  Blue              Yellow        Red            Green
                                      ↓
                                 V(Vaccinated)   Purple
```
Parameters: infection radius / probability / recovery period / incubation period / vaccination rate

### 8. Economic Agents
```
  [Resource ◆] ──→ [Agent●] ──→ [Market]
                  (collect)      (trade)
```
Agents collect resources and trade at markets. Price volatility and market count configurable.

---

## Feature Details

### Agent Settings
| Parameter | Range | Description |
|-----------|-------|-------------|
| Agent count | 1 ~ 9,999 | Slider + direct input |
| Name prefix | Text | Custom agent name prefix |
| Seed | 0 ~ MAX | Reproducible experiments |
| Episode limit | 0 (∞) ~ MAX | Max steps per episode |

### Physics Engine Parameters
| Parameter | Range | Description |
|-----------|-------|-------------|
| dt | 0.01 ~ 0.2s | Physics time step |
| Substeps | 1 ~ 20 | Collision resolution accuracy |
| Damping | 0.0 ~ 2.0 | Velocity damping coefficient |
| Contact Force | 10 ~ 1000 | Collision repulsion force |

### Multi-Environment View
```
  ┌──────┬──────┐   ┌────┬────┬────┐
  │ Env1 │ Env2 │   │ E1 │ E2 │ E3 │
  ├──────┼──────┤   ├────┼────┼────┤
  │ Env3 │ Env4 │   │ E4 │ E5 │ E6 │
  └──────┴──────┘   └────┴────┴────┘
   2×2 = 4 envs       2×3 = 6 envs
```
- Run up to **256 independent physics environments** simultaneously
- Quick presets: 1, 4, 9, 16, 25, 36
- Per-environment scenario selection

### Replay System
```
  [Recording] ──→ 300-frame circular buffer
       ↓
  [Play Replay] → Frame-by-frame slider scrubbing
       ↓
  [Save JSON] / [Load JSON]
```

### Live Charts (Bottom Panel)
```
  Reward ─────────────────────
  │  ╭─╮   ╭──╮
  │╭─╯  ╰──╯    ╭─
  └────────────────→ time

  Speed + Collision charts displayed simultaneously
```

### Data Export
- **CSV**: `Desktop/vmas_export_YYYYMMDD_HHMMSS.csv`
- **JSON**: Full agent-state snapshot

### Visualization Options
| Option | Description |
|--------|-------------|
| Velocity arrows | Direction + magnitude vectors |
| Entity names | Agent / obstacle name labels |
| Goal connections | Agent ↔ goal connecting lines |
| Grid | Background grid |
| Heatmap overlay | 40×40 density map (blue → red) |
| Potential field | Vector field overlay |
| Trajectory trails | Movement trail afterimage |

### Map Editor
- Draw walls by dragging
- Define zones
- Grid snap for precise placement
- Save / Load map as JSON
- Full Undo / Redo history

---

## Statistics Panel (Real-time)

| Metric | Description |
|--------|-------------|
| Steps | Total simulation steps |
| FPS | Frames per second |
| Collisions | Cumulative collision count |
| Entities | Total entity count |
| Total / Avg Reward | Reward sum and average |
| Avg Speed | Average agent speed |
| Episodes | Episode count |
| Success Rate | Successful episode % |
| Best / Worst Reward | Episode extremes |
| Avg Steps/Ep | Average steps per episode |
| Total Distance | Cumulative travel distance |

Scenario-specific extras:
- **Epidemic**: S / E / I / R / V live counts
- **Warehouse**: delivery count
- **Economic**: trade count

---

## How to Run

```bash
# Run directly (no dependencies required)
vmas_simulator.exe

# Build from source
cargo build --release
```

---

## Tech Stack

| Item | Detail |
|------|--------|
| Language | Rust (edition 2021) |
| GUI Framework | egui / eframe |
| Charts | egui_plot |
| Physics Engine | Custom 2D (circle collision) |
| File Dialogs | rfd |
| Serialization | serde + serde_json |
| Build Output | 3,591 lines, 0 warnings, 4.6MB .exe |
| QA | 12 / 12 passed |

---

## License

Copyright (c) 2026 **lightgo**

All rights reserved. The license for this software is held by **lightgo**.  
For licensing inquiries, permissions, or commercial use, please contact:

**Email: lightgo1230@gmail.com**

Unauthorized copying, distribution, or modification of this software  
without explicit written permission from the copyright holder is prohibited.
