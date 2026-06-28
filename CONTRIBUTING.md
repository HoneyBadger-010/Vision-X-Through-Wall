# Contributing to Vision-X

Thanks for working on Vision-X. This is a hackathon project built under time pressure, so the conventions below are deliberately lightweight — optimized for a small team moving fast during the on-site build.

## Repository map

| Area | Owner area | What goes here |
|------|------------|----------------|
| [`node/`](node/) | Edge / firmware | UNO Q firmware (STM32) + on-device AI (Dragonwing MPU) |
| [`mobile/`](mobile/) | Mobile / AI | phone app, localization, dead reckoning, guidance LLM |
| [`pc/`](pc/) | PC / fusion | command-post fusion, command LLM, UI |
| [`cloud/`](cloud/) | Cloud / ML | training, simulation, occupancy heatmap |
| [`docs/`](docs/) | Everyone | design, architecture, demo, datasets, roadmap |

## Workflow

1. **Branch** off `main`: `feat/<area>-<short-desc>`, `fix/...`, or `docs/...`
   _e.g._ `feat/node-breathing-cnn`, `fix/mobile-ble-pairing`.
2. **Commit** in the imperative mood, scoped by area:
   `node: add range-FFT clutter removal`
   `mobile: speak detection cue via on-device TTS`
3. **Open a PR** into `main`. Keep PRs small and focused on one tier where possible.
4. **Review** — at least one teammate, ideally the owner of an adjacent tier the change touches.

## Cross-tier contracts

The tiers talk over small, stable payloads. **If you change a contract, update both sides and the docs:**

- node → phone: the detection JSON (`class`, `range_m`, `breathing_bpm`, `confidence`, `ts`) — see [`node/README.md`](node/README.md).
- phone → PC and PC → phone: positions up, fused team map down — see [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md).
- PC ↔ cloud: incident summary up, occupancy heatmap down.

## Code style

- Match the language's standard formatter (e.g. `black` for Python, `clang-format` for C/C++, Prettier for JS). Keep diffs clean.
- Prefer clarity over cleverness — this code is read under stress on demo day.

## Data & models

- **Never commit datasets or large model binaries** — they are git-ignored (see [`.gitignore`](.gitignore)). Reference datasets in [`docs/DATASETS.md`](docs/DATASETS.md) instead.
- Keep exported model artifacts out of git; share them via the team's storage of choice.

## Docs

If you change behavior, update the relevant doc in `docs/` in the same PR. The documentation is part of the deliverable.
