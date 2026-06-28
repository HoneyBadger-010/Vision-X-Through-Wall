# `node/` — Arduino UNO Q · Edge Sensor Node

> **Job:** *"Is there a living person, at what range, breathing how fast, how confident?"* — answered with **no network**.

The non-substitutable heart of Vision-X: an autonomous detector that runs in a burning structure offline. It is **not** a radio that streams data elsewhere — raw RF never leaves the board.

## Hardware

| Component | Detail |
|-----------|--------|
| Board | **Arduino UNO Q** — Qualcomm **Dragonwing QRB2210** (Debian Linux MPU) + **STM32U585** (real-time MCU) |
| Radar | **Novelda XeThru X4** (X4M200 respiration / X4M300 presence; X4F103 dev kit) over `SPI / USB` — *core sensor* |
| IMU | 6/9-axis accel + gyro (optional magnetometer; foot-mounted variant) over `I²C / Qwiic` |
| Safety | CO + temperature sensor over `I²C / Qwiic` |
| Optional | USB thermal / IR camera (`USB-C`) |
| Alert | on-board LED matrix / buzzer (`GPIO`) — standalone "contact" even with no phone |
| Power | Li-ion pack (`USB-C 5V`); board peaks ≈4.5 W |

> **Radar fallbacks:** SensorLogic `SLMX4` (used by several public datasets) or Acconeer pulsed-coherent modules (cheaper; better for thin partitions than thick walls).

## The dual-brain split

```
            ┌──────────────────────── Arduino UNO Q ───────────────────────┐
  radar ───►│  STM32U585 (real-time)        Dragonwing QRB2210 (Linux+AI)   │───► BLE / Wi-Fi Direct
  IMU  ───► │  • capture frames on a    ──► • clutter removal → range-FFT   │     → firefighter phone
  gas  ───► │    hard real-time clock       • breathing analysis            │
            │  • timing Linux can't         • small CNN classifier          │
            │    guarantee                  • emit compact detection        │
            └───────────────────────────────────────────────────────────────┘
```

- **STM32** — deterministic, millisecond radar/sensor capture.
- **Dragonwing MPU** — the on-device AI and the wireless link.

## On-device AI pipeline

```
clutter removal → range-FFT → micro-Doppler / breathing analysis → small CNN classifier
```

- Deployed via **Edge Impulse** (UNO Q's native TinyML path) or **TensorFlow Lite** on the MPU.
- Pretrained on public IR-UWB data (see [`../docs/DATASETS.md`](../docs/DATASETS.md)), **fine-tuned on-site**.
- Target inference latency **< ~100 ms**.

## Output contract

A compact detection emitted to the phone — small, abstract, privacy-preserving:

```json
{ "class": "human", "range_m": 2.8, "breathing_bpm": 14, "confidence": 0.86, "ts": 0 }
```

## Offline guarantee

- No phone? → the on-board **LED/buzzer** still signals "contact detected."
- No network at all? → the node still detects and confirms alive. This is the whole point.

## Planned layout

```
node/
├── stm32/          # real-time capture firmware (radar + sensors)
├── mpu/            # Linux-side: signal processing + CNN inference + radio
├── models/         # trained / exported CNN artifacts
└── README.md
```

> Code lands during the on-site build. See the [demo runbook](../docs/DEMO.md) for the build checklist.
