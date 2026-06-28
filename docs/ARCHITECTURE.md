# Architecture & Data Flow

A deeper look at *how* the four tiers fit together, what crosses each boundary, the latency budget, and exactly how the system degrades when links drop. For the narrative design rationale see [`DESIGN.md`](DESIGN.md).

## The one principle, restated

> **Push every decision to the lowest device that can make it; ensure each tier survives the one above it disappearing.**

Two consequences follow mechanically:

1. **Data abstracts upward.** The payload on each link is smaller and more meaningful than the one below it.
2. **Intelligence concentrates upward.** Higher tiers reason over more context, but are never on the critical path for the decision below them.

```
            PAYLOAD SHRINKS  ▲                      ▲  INTELLIGENCE GROWS
                             │                      │
  Cloud      multi-site picture, occupancy heatmap  │  building-scale simulation, training
  PC         fused building map, situation report   │  many-to-one fusion, command reasoning
  Phone      relative position, firefighter track   │  per-responder localization, spoken guidance
  Node       detection: human · range · brpm · conf │  on-sensor presence + alive classification
  World      raw RF echo                            │  (physical reflection)
```

## What crosses each boundary

| Link | Direction | Payload | Transport | Always on? |
|------|-----------|---------|-----------|------------|
| World → Node | up | raw UWB reflections | radar front-end (SPI/USB) | yes (physics) |
| Node → Phone | up | `human · 2.8 m · 14 brpm · conf 0.86` | BLE / Wi-Fi Direct | yes (local pairing) |
| Phone → PC | up | victim position + firefighter track | scene Wi-Fi / mesh | scene-dependent |
| PC → Phone | **down** | fused team map (all teammates' positions) | scene Wi-Fi / mesh | scene-dependent |
| PC → Cloud | up | incident summary, aggregated detections | cellular / satellite | **opportunistic** |
| Cloud → PC | **down** | precomputed occupancy heatmap | cellular / satellite | **opportunistic** |

The two **downward** flows are the key to the architecture: awareness that requires the *whole* picture (where every teammate is; where occupants are statistically likely to be) is computed **once, centrally**, then distributed — rather than every phone trying to track every other phone.

## Latency budget

| Stage | Where | Target |
|-------|-------|--------|
| Radar frame capture | STM32 (hard real-time) | milliseconds |
| Clutter removal → FFT → CNN inference | Dragonwing MPU | **< ~100 ms** |
| Detection → spoken cue | Phone NPU (SLM) | near-real-time |
| Fusion → map update | PC NPU | near-real-time |
| Occupancy heatmap refresh | Cloud | seconds–minutes (non-blocking) |

The decision a firefighter acts on — *"alive, this way, ~2 m"* — is produced entirely below the scene-Wi-Fi line, so it has a **sub-second, network-independent** path.

## Degradation matrix

This table is the system's strongest argument — each row is a real disaster-response failure mode, and in every one the tiers below keep their core function.

| Failure | Node | Phone | PC | Cloud |
|---------|------|-------|----|-------|
| **No cloud uplink** | ✅ full | ✅ full | ✅ full (uses last heatmap) | ⚠️ stale heatmap |
| **No command-post link** | ✅ full | ✅ victim cue + own track (loses team view) | ❌ | — |
| **Lone firefighter, no PC at all** | ✅ full | ✅ full single-responder guidance | — | — |
| **Phone fails** | ✅ standalone LED/buzzer "contact" | ❌ | (degraded) | — |
| **Everything but the node** | ✅ on-board alert still fires | — | — | — |

> Legend: ✅ retains core function · ⚠️ degraded but useful · ❌ offline.

## Tier responsibilities (single-sentence contracts)

- **Node** — *"Is there a living person, at what range, breathing how fast, how confident?"* — and answer it with no network.
- **Phone** — *"Where is that person relative to me, where am I in the building, and say it out loud."*
- **PC** — *"Where is everyone and everything on one map, who's down, what do I tell the commander."*
- **Cloud** — *"Given the blueprint and history, where are people most likely trapped, and keep the models sharp."*

## Why each device is non-substitutable

- The **node** must run AI on-sensor because the structure has no network and raw RF is too heavy and too private to stream.
- The **phone** must compute because a moving, gloved, masked firefighter needs *relative* localization and *spoken* output that a stationary PC cannot personalize per-responder in real time.
- The **PC** must exist because many-to-one fusion across responders is structurally impossible for any single moving phone.
- The **cloud** must exist because blueprint-scale collapse/fire simulation and cross-incident training exceed the edge — but it is kept off the critical path.

Remove any tier and a capability is lost that no other tier can absorb. That is the multi-device thesis, made structural.
