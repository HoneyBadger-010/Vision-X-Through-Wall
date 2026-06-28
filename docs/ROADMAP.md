# Roadmap

Vision-X is staged so that a small, honest slice is provable in 24 hours, and every later stage is an *extension* of proven pieces — never a rewrite.

## Stage 0 — Phase 1 proposal *(current)*

- [x] System design across four tiers (node / phone / PC / cloud)
- [x] Feasibility grounded in published prior art
- [x] Demonstration plan scoped to one vertical slice
- [x] Repository scaffolded with full documentation

## Stage 1 — On-site MVP (hackathon, July 11–12 2026)

- [ ] UNO Q + IR-UWB: presence + distance + breathing **through a partition**, on-device
- [ ] Detect-then-confirm workflow (motion → pause → confirm-alive)
- [ ] CNN classifier pretrained on public data + on-site fine-tune
- [ ] Node → phone over BLE / Wi-Fi Direct; **spoken** cue out
- [ ] PC: single fused map + one LLM situation line
- [ ] Cloud: sync stub / precomputed heatmap on a slide

**Definition of done:** the [demo moment](DEMO.md#the-demo-moment-script) runs live.

## Stage 2 — Robustness & multi-responder

- [ ] IMU motion compensation hardened on a worn (moving) sensor
- [ ] Pedestrian dead-reckoning over a real floor plan, anchored by zero-velocity updates + map-matching
- [ ] Many-to-one fusion of several responders on the PC
- [ ] Downward team-map push to every phone
- [ ] Downed-firefighter alarm (motion-stop + vitals) and UWB homing

## Stage 3 — Intelligence at scale

- [ ] Cloud occupancy-likelihood model from blueprint + collapse / fire-spread simulation
- [ ] Live heatmap refinement from field detections (when uplinked)
- [ ] Larger PC LLM with retrieval grounded in the building floor plan
- [ ] OTA edge-model updates

## Stage 4 — Research extensions

- [ ] **Federated, privacy-preserving training** across fire services
- [ ] **3D pose / skeletons** (RF-Pose direction) — slumped vs. moving victim
- [ ] **Multi-victim simultaneous tracking** + antenna-array imaging
- [ ] **Heart-rate during stationary dwells**

## Stage 5 — Productization

- [ ] Heat-hardened enclosure, ruggedization, field certification
- [ ] Integration with fire-safety panels, pre-plans, dispatch / CAD
- [ ] **Adjacent applications** — earthquake SAR, elder-care fall detection, secure presence sensing

---

> Stages 1–2 are the credible near-term path; Stages 3–5 are the scale-up and research arc. The architecture does not change between them — only how much of each tier is filled in.
