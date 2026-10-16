# First Light: Context

Updated: 2026-10-16

**Identity:** FirstLight Unity game repository (local checkout; Unity 6000.3.20f1, URP). Specs: [specs index](../../../projects/firstlight/specs/README.md) (load only on request). Architecture: [overview.md](overview.md).

## Goals

- Slow, grindy single-player forest survival RPG with RuneScape-style point-and-click and PSX-horror presentation.
- Grow systems incrementally from user-reviewed specs; verify in Editor Play Mode (builds deferred).

## Current state (last activity 2026-10-03)

- Implemented: interaction model, inventory/equipment, Woodcutting, Survival fire, crafting bench + recipe window, combat v2 (rounded damage, health bars), Queen Spider boss, minimap, day/night with per-period fog, retro UI/audio, five-scene World set (specs 000–035).
- Last full suite: 284/284 Play Mode tests (2026-10-02, after assembly migration). Later changes verified only by focused tests.
- `Start` temporarily loads `World_PlayModeTests`; restore `Assets/Scenes/World/World.unity` for production routing.
- `World_PlayModeTests` is a single-scene test island; pickup raycast mask fixed to include layer 6 (mask 115).
- `World_Terrain` may hold unsaved Editor edits (Retro material on 8 tiles, Y −10 transforms); save it before reopening, since saved heightmaps assume the new transforms.

## Open issues

- Death-loot placement failure still discards the rolled delivery; corpse cleanup is fixed. Retry/fallback policy is a design decision.
- Overlapping station patterns (exact log vs Any log) validate but cannot craft (architecture review P2). TBD — no fix recorded; confirm in source.
- Strength/Block XP policy, weapon/armor tuning and difficulty-band validation open (spec 023).
- Title/Museum/ArtPrototype keep public tuning fields; Animator clip pause not directly tested.
- No disk save/load; no Player build or performance profiling.

## Awaiting user review

- Pickup by pointer on test island (2026-10-03); test-island scale/elevation/beaches and bright daylight.
- World loading/fade and designer workflow (spec 035); minimap water; water Ignore Lighting look.
- Health bars, square destination tile, recipe window, item inspector, XP/level feedback, polish pass (motion/listening).
- Magical willow; audio bank listening; arena power presets via playtest.

## Next steps

1. Collect user feedback on the review items above; tune rather than redesign.
2. Decide death-loot retry policy and Strength/Block XP; then encounter balance in the arena.
3. Restore production Start routing when the test island is no longer the default.
