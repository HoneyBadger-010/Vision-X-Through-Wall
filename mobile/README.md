# `mobile/` — Firefighter's Phone · Hands-free Mobile Brain

> **Job:** *"Where is that person relative to me, where am I in the building, and say it out loud."*

Worn on the firefighter's SCBA or chest and **paired directly to the node** over BLE / Wi-Fi Direct, so firefighter-plus-node is a self-contained unit. The display is the **output of real compute**, not a mirror of the PC.

## What it actually computes

| Function | How |
|----------|-----|
| **Victim localization** | fuses successive radar ranges with the phone IMU as the firefighter sweeps from slightly different spots → *"living person, ~2 m, behind this wall, low to the floor."* (relative cue accurate to **sub-metre**) |
| **Firefighter self-tracking** | **IMU pedestrian dead reckoning** from the entry door (= map origin): gyro heading + accelerometer footstep detection + barometer floor; anchored by **zero-velocity updates**, **map-matching**, optional **UWB anchors**. (absolute accuracy: **a few metres**) |
| **Spoken guidance** | a small **on-device LLM** turns a detection into a comms cue — because a gloved, masked firefighter in smoke cannot read a screen |
| **Team awareness** | receives the **fused team picture from the PC** and shows each teammate's live position relative to the wearer |

## Why a phone and not just a screen

A moving, gloved, masked responder needs *relative* localization and *spoken* output, personalized per-responder in real time. A stationary command PC structurally cannot do this for each firefighter at once — so the phone must compute, not just display.

## On-device AI

- Small LLM (e.g. **Llama 3.2 3B**) exported through **Qualcomm AI Hub → QNN / Genie**, run on the **NPU**.
- ONNX is the NPU path on Snapdragon (**not** GGUF).
- Speech in / speech out for fully hands-free operation.

## Interfaces

```
   node ──BLE/Wi-Fi Direct──►  PHONE  ──scene Wi-Fi/mesh──►  PC
 (detection)                     │      ◄──fused team map──   (down)
                                 ▼
                       comms radio / mask HUD
                       (spoken cue to firefighter)
```

## Degradation

- **No PC link?** → keeps its own victim cue and firefighter track; loses only the shared team view.
- **Lone firefighter, no PC at all?** → full single-responder guidance still works.

## Planned layout

```
mobile/
├── app/            # mobile app (UI + audio I/O)
├── localization/   # range + IMU fusion, dead reckoning
├── llm/            # on-device guidance model (QNN/Genie)
└── README.md
```

> Code lands during the on-site build.
