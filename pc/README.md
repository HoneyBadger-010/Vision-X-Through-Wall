# `pc/` — Copilot+ PC (X2 Elite) · Incident-Command Brain

> **Job:** *"Where is everyone and everything on one map, who's down, what do I tell the commander."*

The heavyweight tier, running on the **Hexagon NPU** at the scene — **private and internet-free**. Where the phone handles one firefighter, the PC handles the whole building.

## What only the PC can do

| Function | Why a single phone can't |
|----------|--------------------------|
| **Many-to-one fusion** | combines *every* responder + *every* detection into one live building map | a single moving phone has only its own view |
| **Team-map push (down)** | sends the fused team picture back to every phone, so each responder sees teammates | shared awareness must be computed once, centrally |
| **Downed-firefighter alarm** | motion-stop + the wearer's own vitals → an enhanced PASS alarm; UWB homing to reach them | needs cross-responder context |
| **Command reasoning** | a larger on-device LLM writes the situation report and answers *"how many alive on the second floor?"* | needs the whole-incident picture |
| **Occupancy prioritization** | runs occupancy-likelihood prediction to prioritise the search | needs the building-scale model |

## On-device AI

- Sensor-fusion + occupancy-prioritization models.
- A larger **on-device LLM with retrieval grounded in the building floor plan** (e.g. **AnythingLLM** on the Hexagon NPU, ~45 TOPS).
- All **private and offline-capable** — no incident data leaves the scene unless an uplink is deliberately established.

## Interfaces

```
  phones ──scene Wi-Fi / mesh (2-way)──►  PC  ──opportunistic cellular/sat──►  cloud
 (positions, detections)                  │   ◄──occupancy heatmap (down)──     (opportunistic)
                                          │
                                  ──fused team map (down)──► every phone
```

## Degradation

- **No cloud uplink?** → fully operational; uses the **last precomputed occupancy heatmap**.
- **No command-post link at all?** → the tier below (phones) keeps each firefighter's own cue and track; only the shared team view is lost.

## Planned layout

```
pc/
├── fusion/         # many-to-one responder + detection fusion → building map
├── command-llm/    # situation report + Q&A (NPU, floor-plan retrieval)
├── ui/             # command-post map + alerts
└── README.md
```

> Code lands during the on-site build.
