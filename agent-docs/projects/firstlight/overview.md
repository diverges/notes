# First Light: Architecture overview

_Drafted 2026-10-16 from notes and reviews only; not verified against source._

## The game

- Single-player, slow and grindy survival RPG. The player wakes with only their clothes in a gloomy, fog-bound forest with strange shadow creatures and little memory.
- RuneScape-style controls: elevated orbit camera, point-and-click movement, left-click default action, right-click action menu.
- Look: low-fidelity PS1/PSX horror (Retro Shaders Pro, full-screen dither, fog) with an accepted 40-colour palette.
- Web/desktop first, mobile possible. Player builds are deferred; verification is Editor Play Mode.
- Authoritative design: the GDD in the code repo (`FirstLight: Docs/game-design-document.md`). Specs: [specs index](../../../projects/firstlight/specs/README.md) (load only on request).

## Engine and packages

| Item | Version / note |
| --- | --- |
| Unity | 6000.3.20f1, URP 17.3.0 |
| Unity Pipeline (CLI) | 0.8.0-exp.1; drive the Editor with `unity command --project-path <FirstLight root> <command>` |
| AI Navigation | 2.0.13 (NavMeshSurface, saved navigation assets) |
| Cinemachine | 3.1.7 (separate GameplayCameraRig) |
| More Mountains Feel | 5.9.1 under `Assets/Plugins/Feel` (feedback sequences, MMProgressBar) |
| Retro Shaders Pro, FlatKit, SimpleNaturePack | Vendor; never edited. Project-owned copies/materials instead |

## Assemblies and code layout

- `FirstLight.Runtime`, Editor-only `FirstLight.Editor`, and `FirstLight.PlayModeTests` (kept separate so NUnit stays out of player builds). No other production assemblies.
- Namespaces `FirstLight.<Area>`; Runtime areas: Actors, Actions, Combat, Crafting, Items, Progression, Rendering, UI, World, plus isolated Title, Museum, ArtPrototype.
- No mutable static runtime state; stateless helpers/operators may stay static. No generic action framework, event bus or service locator.
- Asset structure is defined by `FirstLight: Docs/project-structure.md`: direct `Assets/` roots, vendor imports under `Assets/Plugins`, all gameplay definitions centralised under `Assets/Data` with semantic suffixes, scene-owned content in per-scene folders.
- Known locations: player content `Assets/Actors/Player`, enemies `Assets/Actors/Enemies/<Name>`, decorative kits `Assets/Models/DarkForest/{Scatter,MagicalWillow}`, editable Blender sources `Art/`, editor/authoring scripts `Tools/`, docs and captures `Docs/`, `Docs/Images/`.
- Generated assets/captures use a `GEN_` prefix.

## Scenes

| Scene | Role |
| --- | --- |
| `Start` | Title (PSX forest, campfire, Start button). Loads the game scene in Single mode. **Currently routed to `World_PlayModeTests`**; restore to `Assets/Scenes/World/World.unity` for production. |
| World set: `World`, `World_Player`, `World_Terrain`, `World_Gameplay`, `World_Lighting` | Production world. `World` hosts the manager, session, navigation and loading overlay; the others are loaded additively. Open via menu First Light > World > Open world scene set. |
| `World_PlayModeTests` | Single-scene test island (128 m terrain, 180 m ocean) with BootstrapSession; shared fixture for authored-scene Play Mode tests (spec 032). TBD — confirm it is the renamed former `Bootstrap.unity` (spec 032). |
| `World_Arena_PlayModeTests` | Editor-only 64×64 combat balance sandbox (spec 033); excluded from build settings. |
| `BiomeMuseum`, `PsxArtPrototype`, `WorldDesign` | Asset showcase with gameplay prefabs; isolated PSX study; terrain sandbox (512 m island). Not production. |

## Runtime model and ownership

| Owner | Responsibility |
| --- | --- |
| ScriptableObject definitions (`GameDatabase` catalog) | Authored config. `ItemDefinition : InteractibleDefinition` (every item is a world interactible); `EquipmentItemDefinition`, `SkillDefinition`, `SkillActionDefinition` → `RecipeDefinition`, `ActionDefinition` + interaction responses, `CraftingStationDefinition`, `EnemyDefinition` → `BossMonster`, `LootTableDefinition`. Stable IDs unique per category. |
| `PlayerDefinition` | Authored asset = defaults; a runtime clone owns live bag, equipment, health, skill XP and discovered recipes. Levels are derived from XP thresholds. Available actions are derived from default/skill/equipment/named grants. |
| `WorldGameState` (`GEN_WorldGameState`) | Serialized WorldId; nonserialized runtime ownership, providers and readiness for the world scene set. |
| `WorldGameManager` | Startup coordinator: async scene loading, validation, binding, black-overlay reveal, retry/back, teardown. |
| `WorldSceneManager` subclasses | `WorldGameplayManager`, `WorldTerrainManager`, `WorldPlayerManager`, `WorldLightingManager`; gameplay targets discovered at startup (including inactive, excluding runtime loot/fires), never serialized. |
| `BootstrapSession` | Per-run session: pause (Settings/menu and defeat only), manual ticking of actors → combat → interaction → visuals, reset. |
| `InteractionController` | Single active intention: approach, timing, input reservations, reward delivery. |
| `PlayerMotor` / `WorldGrid` | NavMesh movement to 1×1 grid cells; pickup endpoints limited to item cell + 8 neighbours. |
| `WorldInput`, `WorldActionGui`, `BootstrapHud` | Pointer input (raycast `worldMask` must include the pickup layer); IMGUI world hover/menus and action resolution; UI Toolkit HUD (partial classes). |
| `WorldTarget` | Per-instance health, footprint, availability, reset. Sole owner of current enemy health. |
| `EnemyActor`, `EnemyAnimation`, `QueenSpiderAttack` | Roaming/facing/death; Animator adapter; boss acid volleys. Prefab → definition is the only authored link. |
| `CombatService` / `CombatQueue` / `CombatMath` | Engagement, shared one-second clock, pre-damage rolls, simultaneous commit, rounding, XP. |
| `WorldLootService` | Loot/overflow placement on supported grid cells. |
| `WorldDayNightCycle` | Four lighting periods with per-period fog colour/Start/End and zoom compensation. |
| Presentation | `WorldDitherFeature` (full-screen perceptual dither), `WorldHoverOutline`, `CombatHealthBars`, `MinimapTerrainRaster`, `RetroUi` (per-owner font), Feel sequences on prefabs. |

## Rendering and world

- URP `WorldRenderer` + `WorldPresentation` volume profile; Retro Shaders Pro materials; dither at 96 RGB levels, 2 px; native geometry fog per lighting period (no screen-edge veil).
- Water: `Assets/Shaders/GEN_WorldWater.shader` ("First Light/Water"), a FlatKit Water copy with Ignore Lighting; `Ocean.mat` uses it.
- World terrain: nine `World_Terrain` tiles at transform Y −10 with 12 m vertical size (world Y −10..2); water at Y −1; Retro terrain material on all tiles.
- Minimap: baked terrain/water raster, session-only exploration in 5×5 cells.

## Build and test entry points

- Play Mode suite (`FirstLight.PlayModeTests`): last full run 284/284 on 2026-10-02.
- Smoke helpers in `Tools/` (e.g. CombatInputSmoke, InventoryInputSmoke, RecipeWindowSmoke). Use `--timeout 120` for minute-long CLI runs.
- Authoring helpers follow a guarded pattern: `Run` = one-time authoring that refuses to overwrite; `Validate` = safe, repeatable. Never rerun a completed `Run` over hand edits.
- Implementation gotchas live in the shared Unity gotchas collection, not here.
- TBD — Git state: reviews through 2026-10-02 report the project as untracked (no commits). Confirm before relying on Git recovery.
