# First Light: Specifications

- [035: World scene management and loading](035-world-scene-management.md): implemented five-scene split, manager-owned WorldGameState, loading/recovery overlay and UML; 272 regression and seven final lifecycle tests passed; human presentation review pending.

- [034: Combat health bars](034-combat-health-bars.md): overhead Feel progress bars for the player and engaged enemies, accurate health and delayed damage feedback.

- [033: Combat balance arena](033-combat-arena.md): independent 64×64 arena, playable character, catalog enemy spawning and encounter reset controls.

- [032: World Play Mode test scene](032-world-playmode-tests.md): current-art, grid-aligned mechanics fixture renamed World_PlayModeTests; scene-dependent tests isolated from designer-owned World, 256/256 regressions and live checks passed; user visual acceptance pending.

- [031: Item authoring inspector](031-item-inspector.md): grouped item/equipment authoring, assigned icon previews and readable slots; final-source Editor verification, 23-item validation and automated Undo/persistence checks passed; user acceptance pending.

- [030: Minimap and explored world map](030-minimap.md): north-facing circular terrain map, session exploration in 5×5 cells, optional authored target markers and centered expanded map; implemented with 251 regression tests and two-resolution smoke passed; user review pending.

- [020: Player state](020-player-state.md): player ScriptableObject ownership, unified default/live Inspector, isolated runtime copies and preserved scene presets.

- [World bootstrap](016-world-bootstrap.md): playable first level, native terrain, Retro Shaders Pro materials and starter gathering/building props.

- [014: PSX art prototype](014-psx-art-prototype.md): isolated forest clearing, retro shaders, fog/lighting and live A/B; positive review, preferred direction leans PSX, production integration now implemented in World.

Specifications define reviewable implementation milestones. A draft is not authorization to start coding; confirmed game direction stays in the game design document. Later implemented specs supersede earlier rules; historical verification counts below describe each milestone, not the current suite. Current behavior is summarized in the GDD and Play Mode guide.

| Spec | Status | Purpose |
| --- | --- | --- |
| [000-bootstrap](000-bootstrap.md) | Complete; user accepted 2026-09-28 | Interaction-system proof: selecting, approaching, performing, cancelling, and receiving feedback; scenarios and ordered tasks. |
| [001-game-database](001-game-database.md) | Implemented; item/interactible correction passed 34 Play Mode tests | Items are interactibles with default Pick up; one catalog record serves bag/world identity. Runtime state stays separate. |
| [002-woodcutting](002-woodcutting.md) | Implemented; automated verification passed; human feedback pending | First XP skill: accepted 15-level curve, distinct log payouts with XP, three gated trees, compact Skills UI, lifecycle and validation contract. |
| [003-skill-interactibles](003-skill-interactibles.md) | Implemented; 51-test follow-up passed; human balance review pending | Shared skill actions for gathering/processing, fixed-chance attempts, success/failure loot, ground overflow and Inspector balance previews. |
| [004-bag-inventory](004-bag-inventory.md) | Complete; user accepted 2026-09-28 | Six-slot icon inventory; Inspect, Use, Reorder, Drop, and explicit flax-to-wheel processing. |
| [005-world-authoring](005-world-authoring.md) | Draft; initial terrain sandbox created | Handcrafted 3D open world, continuous movement, building grid, terrain painting and asset placement; WorldDesign scene available for experiments. |
| [006-character-camera](006-character-camera.md) | Complete; user accepted 2026-09-28 | Stylized humanoid, animated point-and-click movement and reference-based Cinemachine camera with designer/engineer iteration. |
| [007-equipment](007-equipment.md) | Implemented; 73 Play Mode tests and pointer smoke passed; human review pending | Six equipment slots beside Bag; Equip, swap, Unequip, Drop and ordinary ground pickup. |
| [008-item-models](008-item-models.md) | Complete; user accepted 2026-09-29 | Ground-model coverage for every item, shared bag placeholders, multiple rock/stick models and manual drops at the player's feet. |
| [009-woodcutting-axe](009-woodcutting-axe.md) | Complete; user accepted 2026-09-29 | Right-hand axe model, visible equipment, Chop tree and Humanoid animation with unchanged harvesting numbers. |
| [010-interaction-feedback](010-interaction-feedback.md) | Implemented; 91 tests and pointer flow passed; user audio/visual review pending | Feel movement dust, equipment/pickup audio, skill celebration, tree contact shake/chips and depletion burst. |
| [011-survival-fire](011-survival-fire.md) | Implemented; 105 tests and pointer loop passed; designer review pending | Second active skill; inventory Rock/Stick creation, timed fire upgrade, persistent campfire structures, log refueling and sound/VFX. |
| [012-crafting-bench](012-crafting-bench.md) | Completed; user accepted 2026-09-29 | Portable bench, three ingredient squares and a hidden matching result, ScriptableObject recipes, models and shared XP chime. Earlier picker verification remains historical. |

- [Bootstrap verification](000-bootstrap-verification.md): scenario evidence, task disposition, user acceptance, and deferred validation.











- [013-basic-combat](013-basic-combat.md): implemented sword combat and shared queue; 150 Play Mode tests passed, user presentation review pending.

- [PSX forest title scene](015-title-forest.md): stacked title, dark forest and distant hills, centered flickering campfire and smoke; implemented, visual review pending.
- [Day and night lighting](017-day-night-lighting.md): continuous four-stage preview, editable Inspector presets and clock controls; implemented, lighting tuning pending.
- [019: Model and authoring simplification](019-model-authoring.md): station patterns as the input source, recipe tuning and catalog navigation, Undo-aware scene target collection and complete setup validation.

- [021: Recipe ownership](021-recipe-ownership.md): station and skill recipe lists, inline crafting outputs and migration.

- [022: Player actions](022-player-actions.md): default, skill, equipment and other grants; target action and item-use responses.

- [023: Combat v2](023-combat-v2.md): implemented shared ticks, unarmed attacks, opposed formulas and skill progression; encounter balance and Strength/Block XP policy remain open.

- [024: Queen spider](024-queen-spider.md): 2x2 tarantula boss with airborne acid volleys below 80% health; implementation and verification.

- [025: Enemy inspector](025-enemy-inspector.md): grouped designer controls, model and combat previews, lifecycle settings and Queen Spider as BossMonster; gameplay actions deferred.

- [026: Item hover tooltips](026-item-tooltips.md): simple name, type and description cards for Bag and Equipment; implemented; 22 affected tests and two-resolution Editor checks passed, user visual acceptance pending.

- [027: Discovered recipe window](027-recipe-window.md): skill-grouped scrolling reference for successfully crafted item recipes, source/reagent/pattern rows, dedicated navigation icon and first-discovery logging; 230 tests and two-resolution smoke checks passed, user visual acceptance pending.

- [028: Skill progression feedback](028-skill-progression-feedback.md): near-player XP chips, queued top-center level fanfare and per-skill audio; 236 regression tests plus six focused tests after final audio refinement and two-resolution smoke passed; user motion/visual/listening acceptance pending.

- [029: Actor feedback and scene polish](029-production-polish.md): combat text and windup motion, planted work gestures, state-bound firelight and geometry fog; regression and scene checks recorded, user motion/visual/listening review pending.


