# Decision log

Newest first. Each entry records what was decided, why, and what would reverse it.

## 2026-09-04 (night)

### Every validator failure now names the fix
**Why:** the founder is a beginner artist. `PNG-32 (colour type 6, RGBA): RGB` is useless; "you have a visible Background layer, Layer menu > Background > Convert to Layer" is actionable. Verified in Aseprite's source: `Sprite::isOpaque()` is `bg && bg->isVisible()`, so a Background layer drops the alpha channel regardless of pixel opacity, and the New Sprite dialog offers one by default. This would have failed his first export.

### Aseprite, $19.99, with Pixelorama as the free fallback
**Why:** only editor whose PNG writer was verified line by line. Pixelorama passes our validator because it writes an `sRGB` marker and never `iCCP`, and we only reject `iCCP`. **Rejected:** Pyxel Edit (dead since Jan 2022), Procreate (colour profile is mandatory and unchangeable), Photoshop and Krita (anti-aliasing everywhere).

### Do not buy Mana Seed asset packs for this project
**Why:** its licence bars use "in a project alongside 'AI' generated imagery, writing, code, or anything else". This project is built with an AI coding assistant, so that clause plausibly bars the purchase outright, and it is not limited to art. The first research pass read this as harmless; the fact-checker caught that the quote had been truncated before the load-bearing half. **Kenney, Sprout Lands premium and LimeZu carry no such clause.**

### A duplicate Python validator was deleted on sight
A research agent wrote `tools/pngcheck_art.py`, a second implementation of the same rules in another language. **Why deleted:** two validators is exactly the drift the vendoring discipline exists to prevent. One file, three runtimes.

## 2026-09-04 (evening)

### The assets repo is public
**Why:** releases and Pages work anonymously so the game build needs no credential, CDNs become available, and every credential stays write-only. **Cost accepted:** every approved PNG is world-visible on merge, and a character reveal is spoiled by a merged PR.

### The workshop is parked until a second artist exists
**Why:** it removes the git barrier for people without write access. The founder has write access, so for now it adds an OAuth dance and a server to the act of committing to his own repo. It stays built and runnable. **Reverses when:** someone else wants to contribute.

### The game builds on placeholders from day one
`npm run placeholders` generates magenta checkerboards for every undrawn slot and the manifest includes them flagged. **Why:** otherwise art blocks code and code blocks art. Magenta rather than grey because a plausible placeholder looks deliberate and ships by accident, which is the same reason `#FF00FF` is a rejected colour in real art.

### Four commands are the whole artist interface
`next`, `scaffold`, `watch`, `sheet`. **Why:** the two failure modes for a solo artist are not knowing what to draw next and drawing at the wrong canvas size. `next` fixes the first, `scaffold` makes the second impossible.

## 2026-09-04 (later)

### todoFarm is a project folder of repos, not one repo
`design/`, `assets/`, `workshop/`, and later `game/`. **Why:** art has a different cadence, a different contributor set, and different licensing from game code. **Reverses if:** the split creates more cross-repo friction than it removes.

### The validator is one dependency-free file, vendored not duplicated
Lives in `assets/lib/validate.mjs`, vendored into the workshop with `sync:check` failing the build on drift. **Why:** an artist seeing one verdict in the browser and another on their PR is the worst failure this system can have. **Cost:** a vendoring step. Rejected alternatives: publishing an npm package (too much ceremony for one file), and a submodule (worse ergonomics than a checked sync).

### The workshop has a server, which the original sketch did not
**Why:** a GitHub write credential cannot live in a browser. The server does exactly two things, holds the credential and re-runs the validator. Everything else is static.

### Git Data API, not the Contents API
**Why:** a submission is two files (the PNG and the slot JSON) that must land in one commit. Contents writes one file per call, which would produce two commits and a window where the repo is inconsistent.

### CI is read-only so fork PRs work
Validation needs no secrets. Failure detail goes to the job summary rather than a PR comment. **Why:** this sidesteps the `pull_request_target` footgun entirely. A fork PR cannot comment back, but it does not need to, because the summary and the failing check carry the reason.

### The manifest is never committed. It is generated at publish and attached to a Release
**Superseded the same day.** The first version had CI check a committed manifest, which **deadlocked every submission**: an artist's PR adds art but no fresh manifest, so the check failed 100% of PRs. Generating at publish removes the check, the bot-push-to-protected-branch problem, and the distribution problem together.

### The game fetches two files from a GitHub Release, pinned by tag
**Why:** unauthenticated `raw.githubusercontent.com` is 60 requests/hour per IP, and GitHub-hosted runners share Azure egress addresses, so that bucket is shared with every other anonymous consumer on the same IP. It also returns no `x-ratelimit-*` headers, so there is no warning before throttling. Releases carry no bandwidth limit at all. **For a build-time fetch the packing threshold is two files**, since one archive is one request regardless of contents.

### Recommend a public assets repo
**Why:** releases and Pages work anonymously so the game build needs no credential, CDNs become available, and every credential in the system stays write-only. **Cost, stated plainly:** every approved PNG is world-visible on merge, every open submission PR is world-visible before merge, and a character reveal is spoiled by a merged PR. If it must be private, all CDNs are out, Pages needs Enterprise Cloud to be private, and browser preview needs a proxy route. **Still the founder's call.**

### One GitHub App for both writing and artist sign-in
**Why:** "a token cannot grant additional access capabilities to a user", so an artist's token structurally cannot write to the assets repo. An OAuth App's `public_repo` scope would instead grant write across every public repo the artist can reach. Attribution keys on the numeric GitHub id, never the login, because logins get renamed.

### Never `pull_request_target`
**Why:** `actions/checkout@v7` refuses fork PR checkout under it since 2026-07-20, and since 2025-12-08 it always takes the workflow file from the default branch. Most existing blog advice on commenting from CI is now wrong. Our CI is read-only and reports through the job summary, which needs no write token at all.

### Twelve slots block the first playable build
Listed in `assets/slots/` with `"blocks": "v0"`. **Why:** without them the game renders magenta placeholders.

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
4. ~~Whether the art repo can be public.~~ **Decided: public.**
5. **Where the workshop is hosted**, when it is switched on. Deferred with it.
6. **Whether to collapse the App and OAuth paths into one GitHub App** before the workshop goes live. The research recommends it: an artist token then structurally cannot write to the assets repo, whereas an OAuth App's `public_repo` scope grants write across every public repo that artist can reach.

## Security note

A Railway API token was pasted into a chat transcript on 2026-09-04 and must be rotated. Secrets belong in a gitignored `.env` or in Railway's own environment variables, never in chat and never in this repo.
