# First Light: Glossary

| Term | Meaning |
| --- | --- |
| Arena | `World_Arena_PlayModeTests`: Editor-only combat sandbox that spawns catalog enemies and switches player power presets (spec 033). |
| BootstrapSession | Per-run session component: pause, manual ticking of actors/combat/interaction/visuals, reset. Name survives from the original Bootstrap interaction prototype. |
| BossMonster | `EnemyDefinition` subclass for bosses (Queen Spider); boss abilities live on prefab components. |
| Capability grant | Source that makes a player action available: default, skill level, equipment, or a named additional grant (spec 022). |
| Catalog | `GameDatabase` asset listing all definitions; global recipe lookup is derived from skill lists. |
| Combat clock | Shared one-second tick on which due attacks from all sides resolve simultaneously. |
| Examine-only | A target whose only action is Examine; no hover label or left-click action, reachable via right-click. |
| GEN_ | Prefix for agent-generated assets and captures. |
| Guarded authoring helper | `Tools/` script whose `Run` performs one-time authoring and refuses to overwrite; `Validate` is the routine check. |
| Interactible | Any world object with actions; every item is one (`ItemDefinition : InteractibleDefinition`, default Pick up). |
| Overflow | Completed-attempt output that does not fit the bag; spawned as a quantity-aware ground pickup on a supported cell. |
| Pattern | Ordered ingredient arrangement in the bench's three squares (Empty / exact item / Any log). |
| Player preset | `PlayerDefinition` asset (e.g. Default_Player, `GEN_Player_01_Novice`..`04_Master`) providing defaults; runtime uses a clone. |
| Reservation | Input items held for an in-progress action; released on cancel, committed on success. |
| Retro | Retro Shaders Pro materials/shaders (vendor) used for PSX-style surfaces. |
| Station | Crafting station (`CraftingStationDefinition`), e.g. the portable Crafting Bench, listing its supported recipes. |
| WorldGameState | Shared `GEN_WorldGameState` asset: serialized WorldId plus nonserialized runtime ownership/providers/readiness for the world scene set. |
| World scene set | `World` + `World_Player`, `World_Terrain`, `World_Gameplay`, `World_Lighting`, loaded together (spec 035). |
| WorldTarget | Per-instance world component: health, footprint, availability, initial transform, reset. |
