# todoFarm art standard v0.1

**All art is hand-drawn, by the founder or by community artists. No AI-generated art, ever.**

One base unit: the **16x16 tile**. Every canvas is a whole multiple of it, which is what keeps atlas packing, collision, and camera zoom integer. The constraint is what lets a hundred artists produce art that looks like one game.

Live tool with a working validator: [design/workshop.html](../design/workshop.html).

## Classes

| class | canvas | frames | anchor | colours | used for |
|---|---|---|---|---|---|
| `tile` | 16x16 | 1 | top left (0,0) | 16 | ground: grass, dirt, stone, path |
| `tile.anim` | 16x64 | 4, vertical strip | top left (0,0) | 16 | water, fire, anything that loops |
| `char` | 64x64 | 16, 4 dirs x 4 frames | feet (8,15) per cell | 24 | player and NPC walk sheets |
| `prop1x1` | 16x16 | 1 | bottom centre (8,15) | 20 | chests, lanterns, small decorations |
| `prop2x2` | 32x32 | 1 | bottom centre (16,31) | 24 | furniture the craftsman forges |
| `prop2x3` | 32x48 | 1 | bottom centre (16,47) | 24 | tall furniture, shelves, wardrobes |
| `icon` | 16x16 | 1 | centre (8,8) | 12 | inventory and list row glyphs |
| `ui9` | 24x24 | 1 | 8px corners | 8 | nine slice panels and frames |
| `crop` | 16x64 | 4, vertical strip | bottom centre (8,15) | 16 | 4 stage growth from a ticked task |

## Hard rules, enforced by the validator

A submission failing any of these is rejected before a human sees it.

1. **PNG-32 only.** Indexed PNG and JPG are rejected on sight.
2. **Binary alpha.** Every pixel is fully opaque or fully transparent. No soft edges, ever. Semi-transparent pixels break atlas bleeding and read as blur at 3x zoom.
3. **Exact canvas.** Not "about 16 wide". Exactly the size the slot names.
4. **Colour cap per class.** Counted as distinct RGB among **fully opaque pixels only**. A transparent pixel is not a colour, and the same RGB at two alpha values is not two colours. This rule is written down because the identical file passes or fails depending on it.
5. **No baked shadow.** The engine draws contact shadows so lighting stays consistent. A painted shadow cannot be turned off.
6. **Frames are a vertical strip.** Frame 1 on top, height divides exactly by the frame count, no frame blank.

**Aseprite exports are fine.** Validation runs in the browser on decoded RGBA, so an indexed PNG carrying transparency in a `tRNS` chunk passes. Naive server-side Pillow checks report mode `P` with no `A` and reject those legitimate exports. This is a real trap and the browser sidesteps it.

## Soft rules, judged by a human

Machines reject the boring failures for free so humans only spend attention on taste.

- 1px outlines maximum, darker than the fill rather than pure black.
- Light comes from the upper left. Every prop, no exceptions.
- No dithering below 2x2 blocks. It shimmers when the camera moves.
- Read the silhouette first. If it is unrecognisable as a solid black shape at 16x16, it will not read on the farm either.
- Stay in the game palette where you can. It is a guide, not a jail, and the validator only warns.

## Naming

```
<class>.<family>.<variant>[.<state>]        lowercase, snake_case, ascii only

prop1x1.chest.wooden.closed          a 16x16 prop, closed state
prop1x1.chest.wooden.open            the matching open state
prop2x2.furniture.oak_table          a 32x32 piece of furniture
char.craftsman.walk                  a 4-direction walk sheet
tile.terrain.grass_a                 variant a of the grass tile
crop.pumpkin.stages                  a 4-stage growth strip

file on disk:  <asset_id>@1x.png     one file, one slot, no exceptions
```

## Typography

From [research/pixel-fonts.md](research/pixel-fonts.md), fact-checked against the actual font binaries.

| role | font | licence |
|---|---|---|
| body, task rows, dates | **Pixel Operator** 16px | CC0 1.0 |
| headers, chest labels | Pixel Operator Bold 32px, or Silkscreen Bold 16px for short all-caps | CC0 / OFL 1.1 |

Pixel Operator is the only candidate that is simultaneously proportional, has true descenders, a slashed zero, smart punctuation, and a metrically identical bold.

**Set `line-height` explicitly.** Pixel Operator's default line box is 1.045em, not 1em, which knocks every baseline after the first off the pixel grid. Only the four 16px non-half-bold faces genuinely interchange; the Small Caps faces have no descenders and the 8px cut is a different design, not a scaled one.

## Third-party asset licensing

Two verified landmines from [research/art-asset-packs-and-licensing.md](research/art-asset-packs-and-licensing.md):

- The **free** tiers of Sprout Lands and Cozy Farm explicitly forbid commercial use. Verbatim: "can't be used in any commercial project, resold/redistributed, even if modified." Pay for premium.
- **Avoid Liberated Pixel Cup assets entirely.** CC-BY-SA 3.0 and GPL 3.0. Share-alike is exactly the trap for a closed-source paid app.

Safe for prototyping: Ninja Adventure (CC0) covers tileset, player, NPC, faces and UI on its own. Kenney Tiny Town and Tiny Farm are CC0.
