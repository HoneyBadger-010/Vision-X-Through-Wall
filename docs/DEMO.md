# Demonstration Runbook (24-hour build)

The on-site round gives ~24 hours. The goal is **one honest vertical slice** that proves the thesis end-to-end, not a broad-but-faked system. A genuinely smoke-immune radar makes the demo *real* rather than simulated.

## The one thing to prove

> A firefighter, blind in smoke, learns there is a **living person behind a closed door — and roughly where and that they're breathing — before opening it**, hands-free.

## Scope of the slice

| Tier | In the demo | Out of scope (slide / stub) |
|------|-------------|------------------------------|
| **Node** (UNO Q + 1 IR-UWB) | presence + distance + breathing through a partition, on-device | multi-victim, antenna array, gas/thermal fusion |
| **Phone** | live cue on screen **and spoken** | full pedestrian dead-reckoning over a real floor plan |
| **PC** | fused map with the one contact + one LLM situation line | many-responder fusion at scale |
| **Cloud** | thin sync stub, or a precomputed heatmap on a slide / second "site" | live training, live simulation |

## The demo moment (script)

1. **Setup** — a smoke machine fills a partition; a closed (non-metallic) door; a person hidden behind it, breathing normally.
2. **Advance** — the "firefighter" approaches with an obscured visor. The node reports *motion / direction* ("contact, that way") while moving.
3. **Pause at the threshold** — the firefighter stops for a few seconds. This still **dwell** is when breathing is confirmed (the detect-then-confirm workflow).
4. **The cue** — hands-free audio into the comms: *"Living person detected, ~2 m, behind this door, breathing."*
5. **Command view** — simultaneously, the command-post screen lights up the contact on its map.
6. **(Optional) contrast shot** — point at a hot object / empty corner → **no breathing waveform, no "alive."** Heat ≠ life.

## Pre-build checklist

- [ ] UNO Q flashed; Debian + Arduino App Lab toolchain confirmed booting.
- [ ] IR-UWB module (XeThru X4 / X4M200, or SLMX4 fallback) reading frames via the STM32 path.
- [ ] Clutter removal + range-FFT + breathing-rate estimator running on the Dragonwing MPU.
- [ ] CNN classifier loaded (pretrained on public data); **on-site fine-tune samples captured** through the actual partition.
- [ ] BLE / Wi-Fi Direct link node → phone verified.
- [ ] Phone app: detection → on-screen cue → **text-to-speech** out.
- [ ] PC app: receive detection over scene Wi-Fi → place on a simple building map → one LLM situation line.
- [ ] Partition + closed door + smoke machine staged; person-behind-door rehearsed.
- [ ] Contrast object (hot/empty) prepared for the "heat ≠ life" beat.
- [ ] Fallback recording of a successful run, in case live RF is noisy on stage.

## Risk & mitigation on the day

| Risk | Mitigation |
|------|------------|
| RF clutter / multipath in a crowded venue | record a clean run as backup; favour a thicker, quieter corner; on-site fine-tune. |
| Breathing buried by ambient motion | enforce the **pause-and-dwell** step; keep the subject still; lead with presence if needed. |
| Wireless congestion (Wi-Fi/BLE) | pre-pair; use Wi-Fi Direct; keep PC link optional in the narrative. |
| Time overrun | the node→phone→cue path is the must-have; PC map and cloud are layered on only if time allows. |

## What "done" looks like for the demo

A judge watches a blind-in-smoke approach end in a spoken, correct *"alive, behind this door, ~2 m, breathing"* — and sees the same contact appear on a command screen. Everything else in the design is the story of how this scales; the slice is the proof that it works.
