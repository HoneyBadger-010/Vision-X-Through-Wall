# Datasets

To make a 24-hour build credible, the edge detector is **pretrained on public IR-UWB data and fine-tuned on-site** against the actual partition and radar. Two complementary capabilities must be covered:

- **Detect-through-the-barrier** — is there a person behind a wall/door?
- **Confirm-alive** — is the chest moving with respiration?

No single public dataset covers both well, so they are **paired**.

## Primary datasets

| Dataset | Covers | Modality | Notes |
|---------|--------|----------|-------|
| **Through-wall victim detection** *(aftermath behind walls/obstacles, UWB radar)* | detect-through-barrier | IR-UWB | Built specifically for victim detection behind walls/obstacles — the through-wall half. |
| **`nesl/MobiVital`** | confirm-alive (vitals under motion) | IR-UWB + synchronized IMU + ground-truth respiration | The key set for **motion-robust** breathing: synchronized IMU enables training the motion-compensation CNN. Close-range, LoS, cooperative. |
| **`jocelynZXY/RadarDataforCSBHRD`** | confirm-alive | radar, breathing + heart rate, incl. activity | Adds heart-rate signals and activity conditions. Close-range, LoS, cooperative. |

> ⚠️ **Important caveat:** MobiVital and RadarDataforCSBHRD are **close-range, line-of-sight, cooperative** recordings — excellent for vitals extraction, **not** representative of through-wall geometry. Always pair them with the through-wall set, and **fine-tune on-site** through the real barrier.

## Supporting / optional

| Dataset | Use |
|---------|-----|
| **`UWB-gestures`** (9,600 labelled samples) | optional gesture-control input for hands-free interaction. |
| **`ZHOUYI1023/awesome-radar-perception`** | curated **index** of radar datasets + detection / domain-adaptation papers — the hub to find more. |

## How they map to the pipeline

```
pretrain ─┐
          ├─ through-wall set ──────────► presence / detection head (CNN)
          ├─ MobiVital (+IMU) ──────────► breathing + motion-compensation head
          └─ RadarDataforCSBHRD ────────► vitals robustness (rate, activity)
                                              │
on-site fine-tune (capture through the actual partition, IMU-synchronized) ─┘
```

## Domain-gap strategy

The biggest risk is the gap between cooperative LoS lab data and a real through-wall, across-rooms, moving-sensor scenario (often in smoke). Mitigations:

1. **On-site fine-tuning** — capture a small labelled set through the demo partition during the build.
2. **Motion-inclusive training** — use IMU-synchronized data (MobiVital) so the CNN learns cancellation rather than relying on hand-tuned filters.
3. **Domain-adaptation references** — techniques catalogued in `awesome-radar-perception`.
4. **Detect-then-confirm** — the workflow itself reduces the domain gap by confining the *alive* decision to a still dwell, closer to the cooperative-subject training regime.

## Licensing

Each dataset carries its own license — confirm terms before redistribution. This repository **does not vendor any dataset**; it references them. Place any locally downloaded data under a git-ignored `data/` directory (see [`.gitignore`](../.gitignore)).
