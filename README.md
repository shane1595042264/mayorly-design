# Mayorly design

Specs, decisions, research and prototypes. One of several repos under the `Mayorly/` project folder, alongside `assets/` and `workshop/`.

A zero-friction desk for people who want to max out their output. Tasks are entries; a mute clerk files them into ledgers; time at the desk earns coins; the couch costs them; and the desk moves on its own when the moment says so (an AI prompt, a streamer's death, an ad, your own League match), so nothing between you and the work costs a step.

**Since 2026-11-23 the walkable town is dropped** ([decision](docs/04-decisions.md)). The app is [mayorly-app](https://github.com/shane1595042264/mayorly-app): Electron and React on Windows and macOS, and an iPhone app, built since 2026-10-09. This repo keeps the decisions, the research and the art standard; the product spec below still describes the Mayor's Hall and is kept as history, with its nouns, economy and refusals still in force.

## Docs

| | |
|---|---|
| [01-product-spec.md](docs/01-product-spec.md) | what the game is and what it refuses to do |
| [02-art-standard.md](docs/02-art-standard.md) | the nine art classes, hard and soft rules, naming |
| [03-asset-pipeline.md](docs/03-asset-pipeline.md) | build-or-buy decision for the art pipeline |
| [04-decisions.md](docs/04-decisions.md) | decision log and open questions |

## Prototypes

Both are single-file, no build step. Open them in a browser.

- **[prototypes/mockup.html](prototypes/mockup.html)** — the game. Nine scenes. Real walking, tile collision, chest proximity, and canvas-to-HTML focus handoff. Every sprite is drawn procedurally so nothing is licensed yet.
- **[prototypes/workshop.html](prototypes/workshop.html)** — the asset pipeline, now superseded by the real `workshop/` repo. **The PNG validator is real**: it decodes the file and reads pixels, checking canvas size, binary alpha, colour cap, blankness, and animation frames. Nothing uploads; it runs entirely in the browser.

## Research

Roughly 380KB of cited findings in [docs/research/](docs/research/), produced by multi-agent workflows and adversarially fact-checked by a second agent per report. The critiques are appended to each and override the report where they disagree.

| | |
|---|---|
| [prior-art.md](docs/research/prior-art.md) | 146 products verified. Nothing like this ships. |
| [asset-pipeline-memo.md](docs/research/asset-pipeline-memo.md) | ~40 tools. None validate pixels on upload. |
| [npc-mood-and-motivation.md](docs/research/npc-mood-and-motivation.md) | why gamified todo apps get abandoned |
| [llm-classification-and-cost.md](docs/research/llm-classification-and-cost.md) | emergent taxonomy, anti-sprawl, real cost per user |
| [phaser-technique.md](docs/research/phaser-technique.md) | pixel-perfect rendering, canvas plus DOM |
| [tauri-macos-packaging.md](docs/research/tauri-macos-packaging.md) | signing, hotkeys, SQLite, notification limits |
| [art-asset-packs-and-licensing.md](docs/research/art-asset-packs-and-licensing.md) | which packs are commercially safe |
| [pixel-fonts.md](docs/research/pixel-fonts.md) | font choice, verified against the binaries |
| [github-as-backend.md](docs/research/github-as-backend.md) | the asset pipeline mechanics, and the CI deadlock it caught |
| [aseprite-pipeline.md](docs/research/aseprite-pipeline.md) | the Background-layer trap that would have failed every export |
| [pixel-art-tools.md](docs/research/pixel-art-tools.md) | what to draw in, how to get good, and one licence to avoid |

## Ground rules

- **All art is hand-drawn.** By the founder or community artists. No AI-generated art, ever.
- **No guilt mechanics.** Nothing decays, no streaks break, unfinished work just goes back in the chest.
- **Coins are only earned in the furnace.** Ticking a box without burning time pays nothing.
