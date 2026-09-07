# Decision log

Newest first. Each entry records what was decided, why, and what would reverse it.

## 2026-09-07

### Characters are 16x32, everything else stays on the 16px base
The founder felt the sprites were too small to put detail into and asked whether to grow them. **Decided: keep the 16px tile, grow only characters to one tile wide by two tall.** **Why:** the tile, icon and prop sizes already match Stardew Valley exactly (16px tiles, 32x32 and 32x48 furniture), so the feeling of tightness there is the medium, not a bug; Stardew's people are 16x32 though, and ours were 16x16, which is Pokemon proportions and leaves no room for a face or a sash. A 32px base was rejected: four times the pixels per sprite, quadrupled walk cycles, and it loses the look the project is chasing. `char` sheets are now 64x128 with the anchor at (8,31) per cell. No character had been drawn, so nothing was invalidated. **Reverses if:** the tall cells fight the room scale once the hall is on screen.

## 2026-09-05 (name)

### Standalone app, not a Stardew Valley mod; mods get an API instead
The founder asked whether to build Mayorly as a SMAPI mod to reuse Stardew's world and assets. **Decided: standalone.** **Why:** a productivity tool has to live on a hotkey and a glance, not behind Steam, a loading screen and a save file; the mod terms forbid every commercial path we have named; SMAPI is desktop only so mobile dies; and the 1.6 update showed a mod inherits the game's breakage schedule. The asset saving is also smaller than it looks, since the hall, ledgers, desk, clerk and journal are new art either way. **What we keep from the idea:** Mayorly will expose a local sync API so anyone can write a Stardew mod, or any other client, against their own data for free. Mods are welcome; the product is not one. **Reverses if:** a fact-check of the legal and technical claims (in flight) overturns them.

### The product is Mayorly
"todoFarm" already existed and the farm was gone from the fiction anyway. Twelve candidates were checked across GitHub, npm, four domain endings, both app stores, Steam, itch and trademark databases, then four more. **Why Mayorly:** a dictionary word ("in the manner of a mayor") that names the core fantasy, with `mayorly.app` `.io` `.dev`, npm, the GitHub name and every store free and no mark on file. **Rejected with evidence:** Daybook (a 1M-install competitor), Docket (registered US mark for a mobile app), Reeve ("The Reeve" is a town-administration game on every one of our platforms), StarDo (two live productivity apps already use it, and STARDEW VALLEY is an incontestable US mark whose examiner-initiated citations have already killed a third-party filing). Full data in the research archive. Repos renamed `mayorly-*`; old URLs redirect. `tf` stays `tf`.

## 2026-09-05

### The Mayor's Hall replaces the farm; ledgers replace chests; tokens replace crops and furniture
The player is the mayor of a small town and starts in one room, the Mayor's Hall. Tasks are **journal entries**. Categories are **ledgers** on a shelf. The furnace is the mayor's **desk**. The mute craftsman is a mute **clerk**. Completing work earns **tokens only**; there is no crop for ticking and no forged furniture for desk time. Decorations and rooms are bought.

**Why:** the founder's words: a chest item as a todo item "just doesn't make sense, it's not immersive." A journal is what a person running a town would keep; a ledger is what their clerk would file into. The fiction now matches the nouns.

**What did not change:** every rule. Nine-ledger cap, clerk may rename and merge its own, hand-written ledgers are locked, four entries before a ledger is bound, one timer, tokens only from desk time, 12 per tomato with the 3-tomato bonus, leisure at 1 per minute and gated behind tokens, the mute classifier and its log, and every refusal in the anti-guilt list.

**What this unlocks:** rooms as purchasable content. A farm, a kitchen, a tavern, each built first in the assets repo and bought by the player with tokens. The asset pipeline is now the content pipeline, which is why it was built before the game.

**Cost:** six art slots retired (three chests, two furnace states, the craftsman) and their issues closed. Twelve new hall slots opened. The farm tiles already drawn drop to v1 as the first buyable room, nothing is wasted. The farm mockup in `prototypes/` is superseded and not rebuilt; the next design artefact is the game itself.

**Reverses if:** the hall turns out to be less legible than the farm was in play. Unlikely, since the mechanics are identical and only the skin changed.

### The long-term thesis is recorded: a life OS, not a todo list
Tokens are meant to eventually gate discretionary real-world spending, answering "have I earned it" where a bank balance only answers "can I afford it". **Out of v1 scope on purpose.** Written into the spec so the v1 token economy is designed with the destination in view.

### Open source first, built for one user first
No payment gates in v1. The founder is the only user until he likes it. Monetisation is a question for after traction.

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

### Mayorly is a project folder of repos, not one repo
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
