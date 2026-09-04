# Asset pipeline: decision and design

**Verdict: nothing on the market does this. Start on GitHub, build when a trigger fires.**

Full memo with citations: [research/asset-pipeline-memo.md](research/asset-pipeline-memo.md).

## What we need

Seven steps, in order.

1. A maintainer creates an **asset slot** carrying a spec: exact dimensions, colour cap, transparency rule, frame count, anchor, naming, plus a written brief.
2. Artists browse **open slots**, claim one, draw it, upload against it.
3. The upload is **automatically validated against the spec before a human sees it.**
4. An admin approves or rejects what passed. Competing submissions stay as alternates.
5. Approved art becomes **developer-consumable**: stable id, JSON manifest, fetched by the build.
6. Every submission carries **licence and attribution**.
7. A dashboard shows slot status across the catalogue.

Think Crowdin, but the unit is a sprite instead of a string and the validator checks pixels instead of placeholders.

## Why nothing fits

Roughly 40 products across 7 categories. **Not one ships automatic validation of an upload against an admin-declared per-slot spec.** Every validation claim collapses into one of four things:

- a filetype allowlist and max byte size (Cloudinary, Canto, PocketBase, Supabase, Payload)
- a metadata readout shown to a human (PageProof Smart Check)
- an LLM advisory that **explicitly never blocks** (Ziflow, Filestage, Artstash)
- a naming regex on a text field labelled "Input Validation" (Autodesk Flow)

The second gap compounds it: **almost nothing has a first-class object for art that does not exist yet.** DAMs and proofing tools model assets that have been made. A slot is an empty placeholder carrying a spec, and without it there is no queue to claim from and no unfilled column. Only issue trackers and localisation platforms model unfilled work, and neither can look at a pixel.

The workflow half exists in Crowdin and GitHub. The validation half exists nowhere, and it is the cheap half.

## Decision: GitHub first

A public repo gives steps 1, 2, 4 and 7 for free. Issue forms now take real drag-and-drop uploads. It is also the **only route where validation is structurally pre-acceptance rather than advisory**, because a required status check genuinely blocks a merge.

**The disqualifier, and it is a real one.** GitHub ToS section D.5 grants every user a licence to "use, display, perform and reproduce (by forking) Your Content." A public repo makes the sprite catalogue, which is the actual differentiator, permanently forkable by anyone including competitors. A restrictive LICENSE constrains downstream use; it does not switch off forking.

**Three taxes to budget for on this route:**

- Fork PRs run with a read-only token and no secrets, so **the bot cannot post the rejection reason** without a two-stage `workflow_run` split.
- First-time contributors need a human click before their workflow runs, landing at peak drop-off.
- Assignment sits at Triage level, so **self-claiming needs a `/claim` bot** you write.

### Switch to building when any one of these breaks

1. The answer to "can the art repo be public?" becomes **no**. At that point GitHub costs $80/mo at 20 artists, anonymous asset fetch dies, and the fork-PR model stops working for outsiders.
2. The first non-technical artist abandons a submission at the git step.
3. The first time you need to answer "which sprite shipped in build 1.4.2" and cannot.

### Runners-up, for the record

| | covers | disqualifier |
|---|---|---|
| **Payload CMS** | cleanest verified pre-persist gate of any backend. MIT, no seats. | only one auth collection reaches the admin panel, so artists get **no UI at all**. Its own upload validation is mimetype and filesize, so **not one of the six spec fields is expressible in config**. |
| **Directus** | best API, approval workflows, real public read policy | `files.upload` is an **action** event only, never a filter. Docs state the files collection emits no create event on upload. A maintainer answered the blocking question with "I am afraid you can't." |

## What to steal from localisation platforms

The shape maps almost one to one.

- **Crowdin's string model is our submission model with the nouns swapped.** Many contributors submit competing candidates against one key, a proofreader with elevated permission approves exactly one, losers persist as retrievable alternates. Steal the data shape: slot is a key, submission is a candidate, approval is a separate role on a separate step.
- **Separate submit from review as first-class stages with different roles.** That is what makes "browse open slots" and "review what passed" two screens with two permission sets rather than one CRUD table with a status column.
- **Steal the rejection-error contract.** Crowdin's pre-import module returns a machine-readable error rendered in the contributor's own upload screen at upload time. Their engine does it for text; ours does it for pixels. Same API shape.
- **Steal Lokalise's async upload pattern.** Upload returns a job id you poll, so you never design a synchronous endpoint you have to tear out.

Also worth copying: **Roblox's validation-system docs** are the best available specification of good failure feedback. Name the failing check, say how to fix it, say why it matters, ship a visualiser.

## If we build it

**Five tables and one endpoint.** `slots` with the spec as JSONB, `claims` with artist id and expiry, `submissions` with file ref plus validation report plus licence grant plus status, `users`, `manifest_revisions`. One upload endpoint that validates server-side, writes to a quarantine prefix, and promotes to public only on pass. Two public GETs: a manifest filtered to approved, and a slot-status list.

**Cut from v1:** atlas generation (ship individual PNGs plus a manifest), animation frame tags, anchor rules beyond a stored integer pair, notifications, any alternates gallery richer than a list.

**What looks hard and is not: the validation.** All four checks are trivial and they are trivial in the browser, which means the artist gets the specific reason before a byte reaches the server.

- **Dimensions:** compare `naturalWidth`/`naturalHeight` to the spec.
- **Alpha:** scan every fourth byte for a value other than 255 and 0.
- **Palette:** pack each pixel into a uint32 and put it in a `Set`.
- **Frames:** integer-divide, then check no frame is blank.

An afternoon plus fixtures. Already implemented and working in [design/workshop.html](../design/workshop.html).

**What is actually hard:** artist identity and moderation at scale, storage and CDN, licence provenance, atlas generation, and versioning approved art without breaking shipped builds.

## The dev contract

The game never browses the workshop. It reads a manifest, and a missing key means the art is not done.

```json
{
  "standard": "todofarm-art/0.1",
  "tile": 16,
  "assets": {
    "prop1x1.chest.wooden.closed": {
      "url": "/a/prop1x1.chest.wooden.closed@1x.png",
      "w": 16, "h": 16, "frames": 1,
      "anchor": "bottom centre (8,15)",
      "artist": "mira_px", "licence": "CC0", "rev": 1
    }
  }
}
```

Art lands without a code change. Every asset carries its artist and licence so the credits screen builds itself. `rev` pins a build to exact art, so approving something new never breaks a shipped version.
