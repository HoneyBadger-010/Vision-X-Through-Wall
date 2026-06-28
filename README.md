<div align="center">

# 🔥 Vision-X

### Finding living people through walls, across rooms, and smoke — and giving rescue teams a hands-free sense in zero visibility.

[![Hackathon](https://img.shields.io/badge/Snapdragon_Multiverse-Bengaluru_2026-d6520e?style=for-the-badge)](https://github.com/HoneyBadger-010/Vision-X-Through-Wall)
[![Platform](https://img.shields.io/badge/Platform-Snapdragon-3253dc?style=for-the-badge&logo=qualcomm&logoColor=white)](https://www.qualcomm.com/)
[![Phase](https://img.shields.io/badge/Phase_1-Project_Proposal-0f7a5f?style=for-the-badge)](docs/PROPOSAL.md)
[![Design Document](https://img.shields.io/badge/Design_Document-read-2f6db0?style=for-the-badge&logo=readthedocs&logoColor=white)](https://honeybadger-010.github.io/Vision-X-Through-Wall/)
[![License](https://img.shields.io/badge/License-MIT-8a94a0?style=for-the-badge)](LICENSE)

**Multi-device AI · AI PC · Mobile · Arduino UNO Q · Qualcomm Cloud AI 100**

📄 **[Read the full design document →](https://honeybadger-010.github.io/Vision-X-Through-Wall/)**

</div>

<div align="center">
  <img src="docs/images/demo-scene.png" alt="Vision-X demo scene — top-down: a firefighter in one room detects a breathing victim in the next room through a solid wall; the command center shows the live alert" width="880">
  <br>
  <sub><b>Vision-X in action (top-down view).</b> A firefighter in Room A detects a person collapsed in Room B <i>through the dividing wall</i>: UWB pulses pass through, the system finds breathing (~0.25 Hz) and range (~2 m), the phone speaks the cue, and the Command Center flags <b>"LIVING PERSON DETECTED — ALIVE."</b> The device chain (Arduino UNO Q → phone → Snapdragon PC → Cloud AI 100) runs along the bottom.</sub>
</div>

---

> **The question that decides everything inside a fire:**
> *Is there a living person behind this wall — in the next room — where exactly, and is there still time —*
> *answered **before** the firefighter commits to entering.*

Vision-X is a four-device AI system that helps firefighters and rescue teams locate living people **through walls, across rooms, and smoke** — and **confirm they are alive** — using impulse ultra-wideband (IR-UWB) radar, distributed intelligence, and hands-free spoken guidance. It does not replace the thermal camera; it adds the **see-through-walls, is-it-alive** sense the camera lacks.

> 📄 **Full design document** — [**view it live (GitHub Pages)**](https://honeybadger-010.github.io/Vision-X-Through-Wall/) (print-friendly HTML, with all figures) · [markdown version](docs/DESIGN.md) · [raw HTML source](docs/Vision-X_design_document.html)

---

## 📑 Contents

- [The 30-second version](#-the-30-second-version)
- [The problem](#-the-problem)
- [The solution — one principle](#-the-solution--one-principle)
- [System architecture](#-system-architecture)
- [The four devices](#-the-four-devices)
- [How it works](#-how-it-works)
- [AI / ML pipeline & toolchain](#-ai--ml-pipeline--toolchain)
- [Tech stack](#-tech-stack)
- [Repository structure](#-repository-structure)
- [Demonstration plan](#-demonstration-plan)
- [Datasets](#-datasets)
- [Feasibility & challenges](#-feasibility--challenges)
- [Novelty & prior art](#-novelty--prior-art)
- [Roadmap](#-roadmap)
- [Getting started](#-getting-started)
- [Team](#-team)
- [References](#-references)
- [License](#-license)

---

## ⚡ The 30-second version

| | |
|---|---|
| **What** | A see-through-walls, across-rooms "is-it-alive" sense for firefighters, distributed across four Snapdragon-class devices. |
| **How** | IR-UWB radar passes through walls and doors into the next room — and through smoke; on-device AI confirms a living person from the millimetre motion of breathing. |
| **Why multi-device** | One capability is split so each device does **only what that device can** — and **every tier keeps working if the one above it loses connectivity**. |
| **The demo** | A hidden person in the next room → the firefighter, on the other side of a solid wall, pauses and detects *"living person, ~2 m, behind this wall, breathing"* before entering — and it still works with the room full of smoke. |
| **Why it's low-risk** | Every ingredient (through-wall UWB vitals, RF localization, on-device AI) is independently proven. **The integration is the contribution.** |

---

## 🚨 The problem

Inside a burning building, a firefighter's best perception tool is the **thermal imaging camera** — but it has two blind spots that kill:

- **It is line-of-sight.** It cannot see through a closed door, a wall, or a floor.
- **It reads _heat_, not _life_.** A hot appliance or a just-vacated spot looks like a person; a person behind a barrier looks like nothing.

So the decisive question — *is there a living person behind this wall, in the next room?* — goes unanswered at the exact moment it matters most. (Optical cameras and LiDAR don't even get that far: smoke defeats them entirely.)

**Radio frequency answers what heat and light cannot.** UWB **penetrates interior walls, doors, and floors** — so a firefighter can sense a living person in the *next room* before entering — it passes straight through smoke, and radar can detect the **millimetre chest motion of breathing**. The same RF that finds trapped civilians also finds a **downed colleague** — and loss of situational awareness inside structures is tied to a large share of the dozens of firefighter line-of-duty deaths each year.

---

## 💡 The solution — one principle

> **Push every decision to the lowest device that can make it, and ensure each tier keeps working if the one above it disappears.**

That single design rule produces the entire architecture *and* its strongest argument.

- **Data shrinks as it climbs** the stack — a raw radar echo becomes a detection, then a position, then a building-wide picture — while **intelligence grows**.
- The result is **graceful degradation**:
  - the **node** finds a victim with **no network**,
  - the **phone** guides one firefighter with **no command post**,
  - the **PC** coordinates a building with **no internet**,
  - the **cloud** links sites only when a backhaul exists.

Disaster zones force exactly this property — and it happens to be the precise **distributed edge-to-cloud AI** story this hardware is built for. **Every device is non-substitutable because of *where the intelligence has to live*, not because it was added for effect.**

<div align="center">
  <img src="docs/images/data-abstraction-pyramid.png" alt="Vision-X data-abstraction pyramid: raw RF echo at the bottom rising through detection, position, building map, to prediction at the top" width="640">
  <br>
  <sub><b>The core principle, visualized.</b> Bottom to top: raw RF echo → vital-sign detection → 3D position → live building map → trapped-occupant prediction. <b>Data shrinks</b> as it climbs (less, more abstract) while <b>intelligence grows</b> — each level handled by the lowest device that can do it: UNO Q → phone → AI PC → Cloud AI 100.</sub>
</div>

---

## 🏗 System architecture

Four tiers, from the physical world up to the cloud. Each arrow carries a **more abstract product** than the one below it; two refined products flow back **down**.

```mermaid
flowchart TB
    P["🧍 Person behind wall · in the next room<br/><i>the subject being sensed</i>"]
    N["📟 <b>Arduino UNO Q</b> — Edge Node<br/>Sense + confirm alive · <b>OFFLINE</b>"]
    M["📱 <b>Firefighter's Phone</b><br/>Localize · track · speak guidance"]
    PC["💻 <b>Copilot+ PC (X2 Elite)</b> — Command Post<br/>Fuse map · prioritize · alert"]
    CL["☁️ <b>Qualcomm Cloud AI 100</b><br/>Train · simulate · heatmap"]

    P -- "RF echo" --> N
    N -- "detection<br/>human · 2.8 m · 14 brpm · 0.86" --> M
    M -- "position + firefighter track" --> PC
    PC -. "summary · opportunistic uplink" .-> CL
    CL -. "occupancy heatmap" .-> PC
    PC -. "fused team map" .-> M

    classDef edge fill:#e3f3ee,stroke:#0f7a5f,color:#15181d;
    classDef cloud fill:#f1f3f6,stroke:#8a94a0,color:#15181d,stroke-dasharray:5 4;
    classDef subj fill:#ffffff,stroke:#c9d0d8,color:#15181d,stroke-dasharray:5 4;
    class N,M,PC edge;
    class CL cloud;
    class P subj;
```

> **Read the diagram:** solid arrows are always-available local links; dashed arrows are opportunistic. Each tier runs even if the tier above it is unreachable. A higher-fidelity rendering of every figure lives in [`docs/images/`](docs/images/) and the full [design document](docs/DESIGN.md).

<div align="center">
  <img src="docs/images/01-architecture.svg" alt="Vision-X four-tier architecture" width="560">
</div>

### Graceful degradation — the multi-device payoff

<div align="center">
  <img src="docs/images/graceful-degradation.png" alt="Vision-X graceful-degradation matrix showing which tiers stay alive when each link or device is lost" width="820">
  <br>
  <sub><b>What survives when links or devices are lost.</b> Cloud lost → PC, phone, node still run (uses last heatmap); command post lost → each phone still guides its own firefighter; lone firefighter → still gets victim detection + voice guidance; a phone fails → its node still raises a standalone alert and the PC keeps the other units. Green = fully operational, amber = degraded, red = offline.</sub>
</div>

---

## 🧩 The four devices

| Tier | Device | Job — and why nothing else can do it |
|------|--------|--------------------------------------|
| **Edge node** | **Arduino UNO Q** *(Dragonwing QRB2210 + STM32U585)* | Carries the IR-UWB radar. The **STM32 captures radar frames on a hard real-time clock**; the **Dragonwing MPU runs on-device AI** (clutter removal + small CNN) and emits a compact detection. **Raw RF never leaves the board.** An autonomous detector that works in a burning structure with **no network**. |
| **Mobile** | **Firefighter's phone** *(worn on SCBA/chest)* | Fuses radar sweeps with its own IMU to **localize the victim**, tracks the firefighter's **own path** by dead reckoning, and runs a small **on-device LLM** that speaks guidance into the comms (a gloved, masked firefighter can't read a screen). Also shows **every teammate's live position**. |
| **AI PC** | **Copilot+ PC (X2 Elite)** *(Hexagon NPU)* | The incident-command brain at the scene — private, internet-free. Fuses **every** responder and **every** detection into one live building map, **pushes the team picture back to every phone**, raises the **downed-firefighter alarm**, and writes the commander's situation report. Many-to-one fusion is what a single moving phone structurally cannot do. |
| **Cloud** | **Qualcomm Cloud AI 100** | The heavy, building-scale work: **trains/improves** the edge models (OTA), and from a building blueprint runs **structural-collapse + fire-spread simulation** to predict a **heatmap of where people are likely trapped**. **Non-blocking** — with no uplink the system uses the last precomputed heatmap and loses nothing critical. |

Full role write-ups: [`node/`](node/) · [`mobile/`](mobile/) · [`pc/`](pc/) · [`cloud/`](cloud/)

<div align="center">
  <img src="docs/images/device-responsibility-split.png" alt="Vision-X device responsibility split: four tiers each with a contract — SENSE, LOCALIZE, FUSE, PREDICT" width="900">
  <br>
  <sub><b>One job per device — remove one and the system degrades, not breaks.</b> UNO Q = <b>SENSE</b> (raw RF → compact detection), phone = <b>LOCALIZE</b> (position + voice guidance), AI PC = <b>FUSE</b> (one live team picture), Cloud AI 100 = <b>PREDICT</b> (learn + forecast where people are trapped). Each box lists that device's exact inputs and outputs.</sub>
</div>

---

## ⚙️ How it works

### 1. Seeing through walls, into the next room
The radar emits ultra-wideband pulses and reads the reflections. Radio at these frequencies passes through common non-metallic **walls, doors, and floors** — and through smoke — and the human body reflects it, so a firefighter can sense who is in the *next room* without entering. Echo delay gives distance (range bins); a person is found by the **motion** in those bins.

<div align="center">
  <img src="docs/images/radar-range-measurement.png" alt="Radar range measurement: a pulse is emitted through a wall, the echo returns, and the time delay gives distance; the range is split into bins" width="640">
  <br>
  <sub><b>How distance is measured.</b> A UWB pulse passes through the wall to the target and the echo returns; the round-trip time Δt gives range via <b>R = c·Δt/2</b>. The time window is sliced into <b>range bins</b>, each a fixed distance — the person is the bin whose echo moves over time.</sub>
</div>

### 2. Detecting breathing — the "is it alive" signal
The radar sweeps rapidly and repeatedly. The chest wall moves a few millimetres with each breath, so the chest's echo **oscillates between sweeps** at the breathing rate (≈0.2–0.5 Hz, i.e. 12–30 breaths/min). A frequency analysis shows a clear peak at the breathing rate. **Static objects produce no such oscillation** — that is how Vision-X tells a person from a hot radiator. Heartbeat is the same idea, smaller and faster (≈0.8–2 Hz), and is a stretch goal.

<div align="center">
  <img src="docs/images/04-working-principle.svg" alt="Working principle: UWB pulses pass through a barrier, reflect off a breathing chest, and become a breathing waveform" width="620">
</div>

<div align="center">
  <img src="docs/images/breathing-fft-spectrum.png" alt="Breathing to FFT spectrum: a living person's periodic chest motion produces a sharp peak at ~0.25 Hz; a static object produces a flat spectrum with no peak" width="720">
  <br>
  <sub><b>The "is it alive" signal, in the frequency domain.</b> A living person's chest motion is periodic, so its FFT shows a sharp peak at ~0.25 Hz (12–18 breaths/min). A static object — a hot appliance, furniture — has no periodic motion and a flat spectrum. <b>The presence of that peak is what separates a person from a warm object.</b></sub>
</div>

### 3. The motion problem → **detect-then-confirm**
The hardest issue: on a *moving* firefighter, the firefighter's own body motion (centimetres) is **10–100× larger** than the chest's breathing motion (millimetres) and swamps it. Vision-X is designed around this:

- **While advancing**, motion detection gives **direction** ("contact, that way").
- The firefighter **stops at the threshold** for a few seconds; **only during that still dwell is breathing confirmed.**
- Residual sway is removed by **IMU motion compensation** — the accelerometer/gyro measures the sensor's own motion and subtracts it from the radar track (the same approach airborne rescue radar uses for platform drift).
- A **CNN trained on motion-inclusive, IMU-synchronized data** learns the cancellation instead of hand-tuned filters.

<div align="center">
  <img src="docs/images/body-sway-vs-breathing.png" alt="Body sway (centimetres) vs breathing (millimetres) on the same scale, showing sway is 10-50x larger, with the detect, compensate, reveal, confirm workflow" width="820">
  <br>
  <sub><b>Why detect-then-confirm is necessary.</b> On the same scale, a moving firefighter's body sway (≈5–6 cm) is <b>10–50× larger</b> than breathing chest motion (≈2–5 mm) and buries it. The fix: detect any motion → use the IMU to cancel the large body sway → reveal the tiny breathing → confirm it with the FFT peak.</sub>
</div>

### 4. Localizing the victim
One reading gives range, not a position. As the firefighter sweeps from slightly different spots, the phone fuses successive ranges with its IMU to triangulate: *"living person, ~2 m, behind this wall, low to the floor."*

### 5. Tracking the firefighter across the blueprint
Indoors GPS fails, so position comes from **IMU pedestrian dead reckoning** (gyro heading + accelerometer footstep detection, integrated from the entry door = map origin; barometer for floor). Drift is anchored by **zero-velocity updates** at each footfall, **map-matching** to corridors/doors, and optional **UWB anchors** at the entry. This yields a live "where is everyone" view, greys out swept rooms, and gives — for free — a **downed firefighter's last-known location**.

> **Honest accuracy:** a few metres of absolute position over a mission (enough to place a contact in the right room); the **relative victim cue is sub-metre**.

### 6. Data-flow recap
```
raw RF echo (node, real-time)
  → confirmed detection (node MPU, offline)
    → relative position + firefighter track (phone, offline)
      → fused building map + situation report (PC NPU, local)
        → occupancy prediction + multi-site picture (cloud, opportunistic)
```
Two flows run **back down**: the cloud's occupancy heatmap → the PC, and the PC's fused team positions → every phone. Awareness that needs the whole picture is computed **once, centrally**, and distributed.

<div align="center">
  <img src="docs/images/full-process-flow.png" alt="Vision-X end-to-end process flow across four swimlanes: Arduino UNO Q, mobile device, Copilot+ PC, and Cloud AI 100" width="900">
  <br>
  <sub><b>End-to-end process flow.</b> Four swimlanes — UNO Q (sense + confirm alive, offline), mobile (localize + track + speak), Copilot+ PC (fuse + map + prioritize), Cloud AI 100 (train + simulate + heatmap) — traced from the radar pulse all the way to "Save Lives," with data abstraction growing upward (Edge → Cloud).</sub>
</div>

### 7. From collapse to search priority — the cloud's contribution
When a backhaul exists, the cloud turns the building blueprint and live detections into a **search plan**: it reconstructs the collapsed structure from radar + IMU data, builds a 3D **occupancy heatmap** of where people are most likely present, and ranks rooms by **search priority** so teams hit the highest-probability zones first. It stays non-blocking — if the uplink drops, the last computed heatmap is used.

<div align="center">
  <img src="docs/images/collapse-to-search-priority.png" alt="Vision-X cloud pipeline: building collapse to 3D reconstruction to occupancy heatmap to search-priority map" width="900">
  <br>
  <sub><b>The cloud's job — turn rubble into a search plan.</b> Collapse → 3D reconstruction (from radar + IMU) → <b>occupancy heatmap</b> (probability of people, layer by layer) → <b>search-priority map</b> (red = search first, green = last). The same data-shrinks / intelligence-grows principle, applied to disaster response.</sub>
</div>

---

## 🧠 AI / ML pipeline & toolchain

| Where | What runs | Stack |
|-------|-----------|-------|
| **Node** | clutter removal → range-FFT → micro-Doppler / breathing analysis → small **CNN** classifier | Edge Impulse (UNO Q's native TinyML path) or TensorFlow Lite on the Dragonwing MPU; STM32 does real-time capture |
| **Phone** | small **on-device LLM** (e.g. Llama 3.2 3B) for offline spoken guidance, speech in/out | Qualcomm AI Hub → QNN / Genie on the NPU (ONNX is the NPU path on Snapdragon, **not** GGUF) |
| **PC** | sensor-fusion + occupancy prediction, larger **on-device LLM** with retrieval grounded in the floor plan | AnythingLLM on the Hexagon NPU (~45 TOPS), all private / offline-capable |
| **Cloud** | heavier occupancy-likelihood model; *(future)* retraining the edge detector from aggregated data | Qualcomm Cloud AI 100 |

<div align="center">
  <img src="docs/images/ai-ml-pipeline.png" alt="Vision-X AI/ML pipeline: raw radar echo, clutter removal, range FFT, micro-Doppler extraction, CNN inference, detection JSON output" width="900">
  <br>
  <sub><b>On-device AI pipeline (runs on the UNO Q).</b> Raw IR-UWB echo → clutter removal (drop static walls/objects) → range-FFT (time delay → distance) → micro-Doppler extraction (breathing / subtle-motion signatures) → <b>CNN</b> (human vs non-human + vitals) → compact <b>detection JSON</b> (<code>human, distance, breathing_rate, confidence, status</code>).</sub>
</div>

**Toolchain:** Qualcomm AI Hub + `qai_hub_models` (supports Snapdragon X Elite **and X2 Elite** — the award hardware), QNN / QAIRT, the Genie LLM runtime, ONNX Runtime, Edge Impulse, TensorFlow Lite, and Arduino App Lab.

---

## 🛠 Tech stack

**Hardware** — Arduino UNO Q (Qualcomm Dragonwing QRB2210 + STM32U585) · Novelda XeThru X4 (X4M200 / X4M300 / X4F103) IR-UWB radar · 6/9-axis IMU · CO + temperature sensor · Snapdragon Copilot+ PC (X2 Elite) · mobile device · Qualcomm Cloud AI 100.

**Software / AI** — Qualcomm AI Hub · QNN / QAIRT · Genie · ONNX Runtime · Edge Impulse · TensorFlow Lite · Arduino App Lab · AnythingLLM.

**Connectivity** — BLE / Wi-Fi Direct (node ↔ phone) · scene Wi-Fi / mesh (units ↔ PC, two-way) · opportunistic cellular / satellite (PC ↔ cloud).

---

## 📂 Repository structure

```
Vision-X-Through-Wall/
├── README.md                 ← you are here
├── LICENSE
├── CONTRIBUTING.md
├── docs/
│   ├── DESIGN.md             ← full design document (markdown)
│   ├── ARCHITECTURE.md       ← deep architecture & data-flow dive
│   ├── DEMO.md               ← 24-hour demonstration runbook
│   ├── DATASETS.md           ← public datasets + how we use them
│   ├── ROADMAP.md            ← milestones & future scope
│   ├── PROPOSAL.md           ← condensed pitch (for the submission portal)
│   ├── images/               ← all diagrams & figures (SVG + PNG: architecture, pipeline, demo scene …)
│   └── Vision-X_design_document.html   ← original print-friendly design doc
├── node/                     ← Arduino UNO Q — edge sensor node (firmware + on-device AI)
├── mobile/                   ← firefighter phone app (localization, tracking, guidance LLM)
├── pc/                       ← Copilot+ PC — incident-command fusion & reasoning
├── cloud/                    ← Qualcomm Cloud AI 100 — training, simulation, heatmap
└── assets/                   ← logos, banners, media
```

> **Status:** this is the **Phase 1** repository. The four tier directories describe the planned implementation and interfaces; code lands during the on-site build (July 11–12, 2026).

---

## 🎬 Demonstration plan

The 24-hour build delivers **one vertical slice**: a single UNO Q with one IR-UWB module detecting presence, distance, and breathing **through a solid wall**; the phone showing the live cue; the PC showing a fused map with one LLM situation line. The cloud runs as a thin sync stub or a second site on a slide.

> **🔥 The demo moment:** a person hidden in the **next room**, behind a solid wall. The "firefighter" — in the adjoining room, visor obscured — **pauses at the wall**, and *before entering* gets the hands-free cue *"living person detected, ~2 m, behind this wall, breathing,"* while the command-post screen lights up the contact on its map. Fill the room with smoke and nothing changes — RF doesn't care.

The **detect-then-confirm** flow (walk up, pause, sweep) is the real intended workflow, and genuinely **sensing a living person through a solid wall** — no line of sight, no trick — makes the demo **authentic rather than simulated**. Full runbook → [`docs/DEMO.md`](docs/DEMO.md).

> 🖼 The illustration at the [top of this README](#-vision-x) shows this exact moment, end to end.

---

## 📊 Datasets

The detector is pretrained on public IR-UWB data and fine-tuned on-site. Two halves are covered: **detect-through-the-barrier** and **confirm-alive**.

- **Through-wall detection** — a dataset built specifically for victim detection behind walls/obstacles with a UWB radar sensor.
- **Vitals under motion** — `nesl/MobiVital` (IR-UWB chest signals + synchronized IMU + ground-truth respiration) and `RadarDataforCSBHRD` (breathing + heart rate during activity). *These are close-range, line-of-sight, cooperative — pair them with the through-wall set.*
- **Optional gesture control** — `UWB-gestures` (9,600 labelled samples).
- **Index** — `awesome-radar-perception`, a curated hub of radar datasets and detection / domain-adaptation papers.

Details and licensing → [`docs/DATASETS.md`](docs/DATASETS.md).

---

## ✅ Feasibility & challenges

| Challenge | How Vision-X addresses it |
|-----------|---------------------------|
| **Respiration from a moving worn sensor** | detect-then-confirm + IMU motion compensation + a motion-trained CNN; presence + breathing lead, heart rate is a stretch. |
| **Justifying the cloud** | scoped to opportunistic occupancy-likelihood prediction and made **non-blocking**, so it reinforces graceful degradation instead of undermining the offline thesis. |
| **The phone as more than a screen** | it runs localization, firefighter tracking, and the guidance SLM on its NPU; the display is the **output of real compute**. |
| **Firefighter self-localization** | IMU dead reckoning anchored by zero-velocity updates, map-matching, and optional UWB fixes; absolute accuracy claimed at a **few metres**, the relative cue at **sub-metre**. |
| **Downed firefighter** | rides on the same sensors: motion-stop + vitals for detection, last-known trail point for location, UWB homing to reach them through smoke. |

---

## 🌟 Novelty & prior art

The physics is proven (through-wall UWB vital-sign detection), the AI is proven (RF-based pose and localization), and individual pieces of the firefighter stack already exist — **SmokeNav** (mmWave + IMU navigation), **C-THRU** (a thermal see-through-smoke HUD), and **POINTER** (responder tracking). But none of them fuse, as one distributed multi-device system on Snapdragon silicon:

1. commodity **RF that confirms a _living_ victim** through walls and across rooms (and through smoke),
2. the **detection AI running on the sensor node itself**,
3. a **hands-free responder unit** that localizes the victim, and
4. a **command-level coordination + occupancy-prediction** layer.

**That integration is the contribution — and it is low-risk precisely because every ingredient is independently validated.**

<div align="center">
  <img src="docs/images/rf-pose-3d-mesh.png" alt="RF-Pose research: 3D human body meshes reconstructed purely from radio signals, shown beside the camera scene they were inferred from" width="720">
  <br>
  <sub><b>Prior art that de-risks us — and our future direction.</b> RF-Pose research (Zhao et al., MIT CSAIL) reconstructs full 3D human meshes from radio reflections alone, proving RF carries rich pose information. Vision-X ships with presence + breathing first; <b>3D posture (slumped vs. moving) is the roadmap extension</b>. <i>Illustration of the published research direction — not a Vision-X output.</i></sub>
</div>

---

## 🗺 Roadmap

- **Federated, privacy-preserving training** across fire services — shared models improve without centralizing sensitive incident data.
- **3D pose & skeletons** (the RF-Pose direction) — tell a slumped victim from a moving one.
- **Multi-victim simultaneous tracking** and antenna-array imaging for richer through-wall scenes.
- **Heart-rate during stationary dwells** as signal processing matures.
- **Integration with building systems** — fire-safety panels, pre-plans, dispatch / CAD.
- **Productization** — heat-hardened enclosure, ruggedization, field certification.
- **Adjacent applications** — earthquake search-and-rescue, privacy-preserving elder-care fall detection, secure presence sensing.

Full milestone plan → [`docs/ROADMAP.md`](docs/ROADMAP.md).

---

## 🚀 Getting started

> The hands-on build happens during the on-site round. Each tier has its own setup guide:

| Tier | Setup |
|------|-------|
| Edge node (Arduino UNO Q) | [`node/README.md`](node/README.md) |
| Mobile app | [`mobile/README.md`](mobile/README.md) |
| Command-post PC | [`pc/README.md`](pc/README.md) |
| Cloud | [`cloud/README.md`](cloud/README.md) |

---

## 👥 Team

**HoneyBadger** — Snapdragon Multiverse Hackathon, Bengaluru 2026.

| Role | Member |
|------|--------|
| Team Lead | _TBD_ |
| Edge / firmware | _TBD_ |
| Mobile / AI | _TBD_ |
| PC / fusion | _TBD_ |
| Cloud / ML | _TBD_ |

> _Update this table with team member names and GitHub handles._

---

## 📚 References

Selected prior art grounding the feasibility and novelty claims — full list in [`docs/DESIGN.md`](docs/DESIGN.md#references).

- Zhao et al., **"Through-Wall Human Pose Estimation Using Radio Signals" (RF-Pose)**, CVPR 2018, MIT CSAIL.
- Adib et al., **"WiTrack: 3D Tracking via Body Radio Reflections,"** USENIX NSDI 2014.
- Adib & Katabi, **"See Through Walls with WiFi,"** ACM SIGCOMM 2013.
- **"Ultra-Wideband Impulse Radar Through-Wall Detection of Vital Signs,"** Scientific Reports 2018.
- Chen et al., **"SmokeNav,"** Advanced Intelligent Systems 2024.
- Qwake / DHS S&T **C-THRU**; NASA JPL / DHS S&T **POINTER**.

---

## 📄 License

Released under the [MIT License](LICENSE).

<div align="center">

---

*Vision-X — accuracy figures are honest engineering estimates; production deployment requires ruggedization and certification.*

**Built for the Snapdragon Multiverse Hackathon · Bengaluru 2026**

</div>
