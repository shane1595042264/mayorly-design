# Decision log

Newest first. Each entry records what was decided, why, and what would reverse it.

## 2026-09-04

### Assets before game
Build the art pipeline first, then the game. **Why:** all art is hand-drawn by the founder and community artists, so the pipeline is the bottleneck on everything else. **Reverses if:** the catalogue stays small enough that a shared folder is genuinely sufficient.

### Start on GitHub, do not build the pipeline yet
See [03-asset-pipeline.md](03-asset-pipeline.md) for the three switch triggers. **Why:** it is the only route where validation is structurally pre-acceptance, and it is free. **Reverses if:** the art repo cannot be public, which is the primary trigger.

### Nine art classes off a 16x16 base unit
See [02-art-standard.md](02-art-standard.md). **Why:** integer multiples keep atlas packing, collision, and camera zoom exact.

### Colour counting rule made explicit
Distinct RGB among fully opaque pixels only. **Why:** the identical file passes or fails depending on this, so it cannot stay implicit.

### AI is mute
No dialogue, no clarifying questions, no approval step. It classifies and tags, you correct by dragging. **Why:** founder's call, LLM conversation quality is not worth the interaction cost. **Cost accepted:** clarifying questions were the one thing zero of 146 surveyed products ship, so this trades away the strongest differentiator. Replaced by the leisure-gating economy, which is also unique.

### Growth replaced mood and relationship
No hearts, no moods, no NPC feelings about your performance. The craftsman makes things out of what you finish. **Why:** founder's call, and it happens to sidestep the entire documented guilt-mechanic failure mode.

### Two completion paths
Tick it done yields a crop. Burn it in the furnace yields furniture. **Why:** gives the furnace a reason to exist beyond a coin multiplier.

### Nine chest cap, user chests locked
Classifier may rename and merge its own chests to stay under the cap, and may never touch a chest you made. **Why:** unbounded emergent taxonomies sprawl into forty near-duplicates.

### Chests leave the farm
The farm is purely the reward space. **Why:** founder's call.

### Stack: Phaser 4.2.1 + HTML overlay + Tauri v2
Not Phaser 3. See [research/phaser-technique.md](research/phaser-technique.md) and [research/tauri-macos-packaging.md](research/tauri-macos-packaging.md).

Two findings that change the code, both verified against source:
- **Zoom with `scale.zoom`, keep `camera.zoom` at 1.** Phaser 4's per-object vertex rounding only applies when the composed matrix has unit scale, so a zoomed camera silently disables pixel rounding on every sprite.
- **Use a plain absolutely-positioned overlay div, not Phaser `DOMElement`.** Phaser applies `transform: scale()` to its DOM container, which blows chat text up and blurs it.

### Write our own SQLite layer, not tauri-plugin-sql
**Why:** the plugin has no transactions, and the classifier batch-moves items between chests. Own commands over `rusqlite` or `sqlx` also keep DB access out of the webview's reach.

### Morning delivery needs a resident process
**Why:** Tauri scheduled notifications are mobile-only; `desktop.rs` has no schedule handling. This makes the menu-bar-resident design load-bearing rather than optional.

### Local-first SQLite as source of truth
**Why:** instant, offline, no data-loss risk.

### Classifier host is deliberately unresolved
Written as a swappable interface. Small local model versus hosted flash-class model. **Why:** not blocking, and the right answer depends on what is actually free at build time.

## Open questions

1. **Classifier host.** Local versus hosted. Local means no backend at all, works offline, and "nothing leaves your Mac" is a selling point given documented AI hostility in the pixel and indie audience.
2. **Wrong entertainment tags have no recovery path.** A bad tag silently charges coins for something useful. Cheapest fix is flipping the tag from the item row.
3. **Where chests live** now that they are off the farm. Building interior, or their own screen.
4. **Whether the art repo can be public.** This single answer decides the entire pipeline route.

## Security note

A Railway API token was pasted into a chat transcript on 2026-09-04 and must be rotated. Secrets belong in a gitignored `.env` or in Railway's own environment variables, never in chat and never in this repo.
