# Better Block Sounds — craftorio fork

A Minecraft **1.20.1** mod (Forge / Fabric / Quilt) that swaps the sound type on a
curated list of vanilla blocks for one that matches their material — ores sound like
ore, terracotta sounds like calcite, blackstone borrows the deepslate family, and so
on. This fork adds the same treatment to a handful of modded blocks that ship with
no fitting `SoundType` of their own.

- Upstream mod, by Ordana:
  <https://github.com/AstralOrdana/BetterBlockSounds>
- Inspired by Trainguy's _Block Sounds Refresher_ resource pack.

## What this fork adds

| Source | Coverage |
|---|---|
| Vanilla | unchanged from upstream — every block the original handles still gets its custom sound |
| IndustrialCraft 2: Refactored | `ic2:lead_ore`, `ic2:tin_ore`, `ic2:uranium_ore` (plain + deepslate variants via id match), wired through the same `STONE_ORE` sound used for vanilla ores |
| Any mod | blocks listed in the `#bbs:stone_ores` block tag get the ore sound — drop a datapack to extend without recompiling |

The IC2 integration is **soft compat**: no IC2 classes are referenced and no
dependency is declared in `mods.toml`. Without IC2 installed the new entries
simply never match.

## Branches

| Branch | Contents |
|---|---|
| `1.20.1` | **default** — live 1.20.1 build, Architectury (common/forge/fabric/quilt) |

Older multi-loader history (`1.18.2-*`, `1.19.2-multiloader`) is preserved from
upstream for reference but not maintained here.

## Requirements

| | |
|---|---|
| Minecraft | 1.20.1 |
| Loader | Forge `47.x`, Fabric `0.14+`, or Quilt `0.20+` |
| IC2 | optional — [IC2: Refactored](https://github.com/HalfCooler/ic2) `2.10.26-ex120` or newer |

## Building

Standard Architectury workflow — no vendored dependencies, no extra setup:

```sh
./gradlew build
```

Outputs:

```
forge/build/libs/bbs-1.20.1-0.1.4-forge.jar
fabric/build/libs/bbs-1.20.1-0.1.4-fabric.jar
quilt/build/libs/bbs-1.20.1-0.1.4-quilt.jar
```

For a tagged release, push a `release/<mod_version>` tag (e.g.
`release/1.20.1-0.1.4`) — the `Release` workflow builds all three jars and
publishes them to a GitHub Release. Plain semver tags (`1.20.1-0.1.4`) and
`workflow_dispatch` runs also work.

## How it works

A single client-side mixin into `Block.getSoundType(BlockState)` consults
`ModSoundTypes.assignSounds`. That method:

1. Checks block tags first — `#bbs:stone_ores`, `#bbs:obsidian`, `#bbs:heavy_metal`,
   etc. — for any modded block opted in via datapack.
2. Falls back to a hard-coded `switch` on the block's path id for ~230 vanilla
   blocks (and the IC2 ore paths added in this fork).
3. Returns the matching custom `SoundType`, which itself is built from existing
   vanilla `SoundEvents` — the mod ships no audio assets of its own.

## Extending via datapack

To add another mod's blocks to the ore sound without code changes, drop a
datapack with e.g.:

```json
// data/bbs/tags/blocks/stone_ores.json
{
  "replace": false,
  "values": [
    "examplemod:cobalt_ore",
    "examplemod:deepslate_cobalt_ore"
  ]
}
```

The same pattern works for `obsidian`, `blackstone`, `polished_blackstone`,
`blackstone_bricks`, `basalt`, `end_stone_bricks`, `clay_bricks`, `heavy_metal`,
`grass_blocks`, `small_objects` and `copper` / `glass` tags defined in
`com.ordana.bbs.BBSMain`.

## Credits & licensing

Better Block Sounds was created by **Ordana**; see the
[upstream repository](https://github.com/AstralOrdana/BetterBlockSounds) for the
original. This fork is published under the same LGPLv3 licence as upstream.
