# 001: Game database

Current model correction (2026-09-30): Specs020–022 supersede the original player-state, equipment, recipe and action schema. The database authoring guide describes current ownership; this milestone retains its historical design and evidence.

Status: Implemented; Play Mode checks passed
Updated: 2026-09-28

**Current schema:** specs 003–004 supersede the original tree/processing definitions and inventory model. Use the database authoring guide for shared skill/action/loot assets, six-slot inventory, item icons and selected Use. The design and evidence below preserve this milestone’s history.

## Problem statement

Player tuning and world-interaction definitions are distributed across components and hardcoded behavior. Designers need shared, inspectable assets that describe the current game without changing code or confusing starting values with live state.

## Proposal

**2026-09-28 correction:** items are interactibles. This supersedes the original separate item/pickup records below. ItemDefinition derives from InteractibleDefinition and provides default Pick up behavior; inventory, processors and placed world targets reference the same asset. The catalog registers each interactible once, with Items as a filtered view. Dropping inventory items is future scope, not part of this correction or an automatic overflow behavior.

Use ScriptableObjects for player stats, item identities, and typed world interactibles, with an explicit searchable catalog that can support hundreds of records. Prefabs reference definitions directly. This is an authored asset database, not a save system or runtime service locator.

- In scope: current player movement/health/punch tuning; target identity and Examine text; pickup item; station input/output and duration; enemy starting health; migration and authoring validation.
- Out of scope: skill progression, survival, weapons, building, save/load, and new gameplay content. Item definitions and scalable authoring are explicitly in scope following user feedback.
- Constraints: preserve current scene/prefab behavior and overrides; retain prefab geometry/visual references; verify through Editor Play Mode, without builds.
- Open questions: future skill/stat formulas and save-file schemas remain deferred; stable content IDs are included now.

## Use cases

### UC.1: Tune or author a definition

- Actor: Designer.
- Goal: Change a supported stat or interaction rule through an asset Inspector.
- Starting conditions: The catalog and authored definitions exist.
- Main flow: Select a definition, edit its relevant fields, assign it to a prefab, enter Play Mode.
- Outcome: Referencing instances use the definition's settings.

### UC.2: Run several instances safely

- Actor: Player/runtime.
- Goal: Interact with one target without modifying another target or the source asset.
- Starting conditions: Multiple instances share one definition.
- Main flow: Damage an enemy or finish station work; reset or restart the run.
- Outcome: Each instance owns its live state; definitions retain authored values.

## Requirements

- **R.1.1:** Player definition shall supply supported maximum health, movement, interaction reach, and unarmed punch tuning.
- **R.1.2:** Interactible definitions shall supply identity, description, concrete behavior type, and behavior-specific gameplay configuration.
- **R.1.3:** A catalog shall expose the available player/item/interactible definitions through serialized references.
- **R.1.5:** Item identity shall use referenced ItemDefinition assets with stable IDs and stack limits; adding an item shall not require modifying an enum or controller switch.
- **R.1.6:** Interactible behavior shall use typed definitions for pickup, processing, enemy, and scenery; content growth shall not require a central kind enum. Small runtime-state enums may remain.
- **R.1.7:** The catalog shall support searching records and detect duplicate IDs, missing references, and invalid configuration.
- **R.1.4:** Inspectors shall identify units, distinguish applicable fields, and reject or explain invalid/missing configuration.
- **R.2.1:** Runtime health, hits, output readiness, bag contents/reservations, and action timers shall not be written into shared definition assets.
- **R.2.2:** Migration shall preserve existing authored values, scene overrides, prefab references, and navigation/visual geometry.
- **R.2.3:** Current pickup, work/collection, punch, pause, cancellation, reset, and UI input behavior shall remain operational.

## Scenarios

### S.1.1: Stats drive runtime behavior

- Given: A player definition with distinguishable supported values.
- When: A character uses it in Play Mode.
- Then: Movement, HUD maximum health, and punch timing/damage use that definition.

### S.1.2: Existing content migrates

- Given: Authored Bootstrap prefabs and scene instances.
- When: The migration assigns data assets.
- Then: Effective values and scene-specific variants remain equivalent, with valid references.

### S.2.1: Shared definitions, separate state

- Given: Two instances sharing one definition.
- When: One receives damage or completes work.
- Then: The other instance and source definition remain unchanged; reset restores starting state.

### S.1.3: Add content without enum changes

- Given: A new item asset with its own ID and stack limit.
- When: A pickup or processing definition references it.
- Then: The bag and interaction flow use it without adding a code enum member; capacity uses that item stack limit.

## Task list

- [x] Designer/programmer agree on bounded schema and runtime ownership.
- [x] Implement definitions, catalog, Inspectors, and runtime consumers.
- [x] Migrate prefabs/scene while preserving authored values.
- [x] Designer review final data assets and authoring workflow.
- [x] Verify migration, definition isolation, Play Mode regressions, and pointer input.
- [x] Publish database authoring guide and final evidence.


## Verification: 2026-09-28

The original evidence below predates the item/interactible consolidation; see the correction review at the end for updated checks.

Unity 6000.3.20f1: 30 Play Mode tests passed, zero failed/skipped, in 43.24 seconds. Six database tests cover authored player tuning, independent instance state, asset-defined processing, item identity/stack limits, invalid configuration, and duplicate/missing catalog records. The processing test creates a temporary Copper rivet asset and collects it without any enum or controller change; the asset is removed afterward.

The saved catalog contains one player, four items, and five typed interactibles. Validation checked all six prefabs, one scene player, 22 scene targets, and the session catalog/starting supplies after reopening the saved scene. A first test run exposed a missing session catalog reference; the migration was corrected and validation expanded before the passing rerun. Superseded definitions and content enums were removed.

Start-to-Bootstrap pointer input and the normal-speed bootstrap input pass succeeded for pickups, panels, Examine, processing/collection, punching, cancellation, camera controls, and reset. All 11 database asset SHA256 hashes remained unchanged through Play Mode. The final Editor console had zero errors/warnings and no compile failure; Editor stopped on Start. No build was performed.

Designer review accepted the schema, typed assets, and source-level catalog authoring workflow. Hundreds of live records and Inspector rendering have not been benchmarked or visually reviewed; the design provides searchable categories and asset references for that scale. New behavior families still require code.

## Item/interactible correction: 2026-09-28

User requested this database correction before implementing skills: inventory items must also be world interactibles with default Pick up, supporting future inventory dropping. Implemented ItemDefinition as an InteractibleDefinition subtype, inheriting identity/Examine data and adding stack limits. Its world action transfers one unit of itself. The world instance is claimed before inventory notifications to prevent duplicate collection through reentry; full capacity leaves it present for retry.

GameDatabase now registers each interactible exactly once. Items is a filtered view of that collection, with shared item/interactible IDs; player IDs remain separate. The catalog has one player plus seven interactibles, including Stick, Rock, Flax and String. Existing item GUIDs, names, IDs, stack limits, processor inputs/outputs and bag identity are preserved. The redundant Stick/Rock pickup definitions and PickupDefinition class were removed, and both pickup prefabs plus four scene placements now reference the item assets directly. Item visuals/colliders remain on world instances. Drop UI, dynamic spawning/stack splitting and skill implementation are not included.

Compilation passed. The first test run was blocked by invalid migrated references; explicit Editor reassignment/save and saved-scene validation repaired them. The final 34/34 Play Mode suite passed in 46 seconds, including four new tests for direct pickup of every item, independent instances, full-bag retry, one-record authoring/shared IDs and notification reentry. Saved-reference validation passed before and after tests, covering seven interactibles, all prefab references and 22 scene targets. Both Start-screen and normal-speed gameplay pointer passes succeeded. All nine database assets remained unchanged during play. Final console: zero errors/warnings and no compilation failure; Editor stopped on Start. No builds performed.

Tools/FinalizeItemInteractibles.cs records the bounded correction; Tools/ValidateItemInteractibles.cs validates saved results without editing them. The older separate-pickup migration script is explicitly retired. Remaining work is approval and implementation of spec 002; no inventory Drop capability is claimed.

