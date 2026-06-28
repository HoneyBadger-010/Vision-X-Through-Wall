# `cloud/` — Qualcomm Cloud AI 100 · Heavy Analytics, Simulation & Training

> **Job:** *"Given the blueprint and history, where are people most likely trapped — and keep the edge models sharp."*

The cloud carries the workloads too heavy for the edge and the work that benefits from **building-scale data**. Crucially, it is kept **off the critical path**: the field decision never waits on the cloud.

## Two modes

### Background / pre-incident
- **Trains and continually improves** the edge detection models from data aggregated across incidents → pushes back as **OTA updates**.
- For a registered building, builds a **risk model**: runs **structural-failure and fire-spread simulations** over the blueprint (building on established simulation methods, not reinventing them) to estimate how the structure may collapse and where fire travels.
- **Output:** a **predicted occupancy heatmap** over the blueprint — likely locations of trapped/buried occupants and the priority zones to search first, weighting occupancy patterns, collapse-prone areas, and fire spread.

### During an incident *(only when uplinked)*
- Serves the precomputed heatmap to the command-post PC.
- **Continuously refines** it with the live detections rising from the field.
- Resolves the **ambiguous faint signals** the edge cannot.

## The non-blocking guarantee

> With **no uplink**, the system uses the **last precomputed heatmap + live detections** and loses nothing critical.

This is what lets a heavy cloud tier **reinforce** graceful degradation instead of breaking the offline-first thesis. The cloud makes the system *smarter when connected*, never *dependent on connection*.

## Interfaces

```
  PC  ──opportunistic cellular / satellite──►  CLOUD AI 100
      ◄──occupancy heatmap + OTA models────
```

| Direction | Payload |
|-----------|---------|
| up | incident summary, aggregated detections (for refinement + training) |
| down | precomputed / refined occupancy heatmap; OTA edge-model updates |

## Privacy

No personal or location data crosses an external link the operator did not establish. Aggregation for training is a candidate for **federated, privacy-preserving** methods (see [`../docs/ROADMAP.md`](../docs/ROADMAP.md)).

## Planned layout

```
cloud/
├── training/       # edge-model training + OTA packaging
├── simulation/     # collapse / fire-spread over blueprints
├── heatmap/        # occupancy-likelihood model + refinement
└── README.md
```

> Code lands during / after the on-site build; the hackathon demo runs this tier as a thin sync stub or a precomputed heatmap.
