# Submission Proposal

This file holds the **condensed pitch** for the Phase 1 submission portal. The full, in-depth design lives in [`DESIGN.md`](DESIGN.md) — this is the short version you paste into the form.

---

## One-liner

> **Vision-X** — multi-device AI that finds living people through walls and smoke for firefighters, confirming they're alive from the millimetre motion of breathing, with on-device intelligence that keeps working even when the network doesn't.

---

## ~500-word description *(portal-ready)*

**Vision-X is a multi-device AI system that helps firefighters and rescue teams locate living people through smoke, interior walls, and darkness — and confirm they are alive — before breaching a door.**

Inside a burning building, the thermal camera is line-of-sight and reads heat, not life: it cannot see through a closed door, and a hot appliance looks like a person. The decisive question — *is there a living person behind this barrier, where, and is there still time?* — goes unanswered exactly when it matters. Radio frequency answers what heat and light cannot: smoke is transparent to radio, ultra-wideband (UWB) penetrates walls and doors, and radar can detect the millimetre chest motion of breathing. Vision-X adds the see-through-the-barrier, is-it-alive sense the camera lacks.

The system distributes **one capability across four devices**, governed by a single principle: *push every decision to the lowest device that can make it, and ensure each tier keeps working if the one above it disappears.*

- **Arduino UNO Q (edge node)** carries an IR-UWB radar. Its STM32 captures radar frames in hard real time while the Dragonwing MPU runs on-device AI (clutter removal + a small CNN) and emits a compact detection like *human · 2.8 m · breathing 14/min · conf 0.86*. Raw RF never leaves the board; it works with no network.
- **Mobile device (worn)** fuses radar sweeps with its IMU to localize the victim, tracks the firefighter's own path by dead reckoning, and runs a small on-device language model that speaks guidance into the comms — because a gloved, masked firefighter cannot read a screen.
- **Snapdragon Copilot+ PC (command post)** fuses every responder and detection into one live building map, pushes that team picture back to every phone, raises a downed-firefighter alarm, and writes the commander's situation report — all private and offline.
- **Qualcomm Cloud AI 100** trains the edge models and, from a building blueprint, simulates collapse and fire-spread to predict a heatmap of where people are likely trapped. It is non-blocking: with no uplink, the system uses the last heatmap and loses nothing critical.

The hardest problem — a moving firefighter's body motion swamping the millimetre breathing signal — is solved with a **detect-then-confirm** workflow (move for direction, pause at the threshold to confirm breathing), IMU motion compensation, and a motion-trained CNN.

The result is **graceful degradation**: the node finds a victim with no network, the phone guides one firefighter with no command post, the PC coordinates a building with no internet, and the cloud links sites only when a backhaul exists. Every device is non-substitutable because of *where the intelligence has to live* — making this a genuine distributed edge-to-cloud AI system, not a single-device idea with hardware bolted on. Every ingredient (through-wall UWB vitals, RF localization, on-device AI) is independently proven in published research; **the integration is the contribution, and that is what makes it low-risk.**

---

> _Word count of the description block above is ≈480 words — trim or expand against the portal's exact limit. Keep the one-liner as the opening hook if a separate title/summary field exists._
