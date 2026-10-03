# Enemy authoring inspector

Status: Implemented; designer review pending
Updated: 2026-10-01

## Problem statement

Enemy assets expose a flat default Inspector, making identity, stats and lifecycle settings difficult to tune. Designers need a model preview, readable stat controls and calculated health/damage. Queen Spider should be a BossMonster subtype while retaining existing references and balance.

## Proposal

Use one inherited custom Inspector for EnemyDefinition and BossMonster. Sections cover identity, model, combat stats, attack, calculated combat preview, movement, On Enter Combat and On Dead. BossMonster adds a boss identity and prefab-behavior note; it inherits existing enemy configuration without duplicating health or abilities.

- In scope: editor presentation, discovery of referencing prefabs, optional combat-entry audio and queen subtype migration.
- Deferred by user: gameplay action wiring and new reaction systems. Event sections currently expose audio, aggression and death loot.
- Constraints: existing enemy balance, prefab/scene references, acid behavior and asset GUIDs remain intact. Only prefabs reference monster definitions; definitions have no prefab field. The editor discovers referencing prefabs for preview/navigation and offers a temporary preview selector when multiple prefabs use the definition.
- Open questions: which designer-wired gameplay actions to support later; no action framework introduced.

## Use cases

### UC.1: Designer tunes an enemy

The designer selects an enemy asset, rotates its static model preview, edits labeled stat levels and sees health plus hit-chance and landed-damage graphs across opponent defence (Block) 0–99. The damage graph shades the nearest-integer minimum–maximum range with stepped edges (.5 rounds up); the chance graph uses a fixed 0–100% scale. Rounding now uses the same helper as runtime combat. There is no opponent-stat input. Level 0 uses level 1 under the existing runtime clamp, as stated beneath the graphs.

### UC.2: Designer configures lifecycle settings

The designer configures aggression and an optional entry sound under On Enter Combat, and existing loot and death sound under On Dead. Entry sound plays once when CombatService adds the enemy to engagement, including retaliation. No sound is assigned by this migration.

Attack, entry and death sound fields offer Play/Stop audition controls with duration and channel information in Edit Mode. Auditioning one stops the previous preview; changing a sound or leaving the Inspector stops its preview. No assigned clip disables audition controls. The loot table expands into a read-only item/quantity/chance summary with validation and an Open loot table shortcut. Independent rolls are labeled; previewing does not roll loot or change items.

### UC.3: Designer inspects a boss

The queen remains the same asset at Assets/Data/Enemies/QueenSpider_Enemy.asset, now typed BossMonster : EnemyDefinition. Open enemy prefab leads to existing QueenSpiderAttack settings. Boss assets can be created from First Light/Database/Interactibles/Boss Monster.

## Requirements

- R.1.1: The Inspector shall group settings and show a rotatable model preview without running enemy scripts.
- R.1.2: Stat fields shall explain their combat roles and display calculated health and damage using CombatMath.
- R.1.3: Graphs shall cover every integer opponent Block value from 0 through 99, separate hit chance from the landed minimum–maximum damage band, and label axes and endpoint values. Boss acid is excluded; runtime level clamping remains unchanged.
- R.1.4: Serialized edits shall support Unity Undo and remain read-only during Play Mode.
- R.2.1: Lifecycle sections shall expose existing death loot/audio and optional combat-entry audio; gameplay actions are deferred.
- R.3.1: BossMonster shall inherit EnemyDefinition; queen migration shall preserve its identity, references and balance.

## Scenarios

### S.1.1: Queen combat preview

- Given: queen Vitality 20, Strength/Proficiency/Block 10 and base damage 2–4.
- When: the designer views the graphs.
- Then: health is 96 and repeated attack spacing is three seconds. Defence 0 (clamped to 1) shows rounded landed damage 3–6 and hit chance about 77.7%; defence 99 shows rounded landed damage 0–0 and hit chance about 0.0004%. No minimum-one damage rule is implied.

### S.1.2: Invalid attack profile

- Given: a non-positive/reversed damage range or interval below one.
- When: the Inspector renders.
- Then: validation appears and the damage preview does not call Resolve on invalid input.

### S.2.1: Repeated engagement checks

- Given: an enemy already engaged in combat.
- When: CombatService ticks again.
- Then: entry feedback does not repeat; leaving and later re-entering creates a new engagement.

### S.3.1: Existing queen scene reference

- Given: a WorldTarget references the existing queen asset.
- When: it is loaded after migration.
- Then: it resolves a BossMonster that remains usable wherever EnemyDefinition is expected, with preserved acid, combat and loot behavior.

## Task list

- [x] Add grouped Inspector and mesh preview with editor resource cleanup.
- [x] Add readable stats and runtime-formula previews.
- [x] Add lifecycle sections and combat-entry audio.
- [x] Add BossMonster and preserve queen asset GUID and values.
- [x] Validate discovery of referencing prefabs for all six existing enemies.
- [x] Compile and run affected regression suites: QueenSpiderTests 7/7; CombatTests 17/17.
- [x] Capture and visually inspect the actual Inspector; fix the initially blank built-in prefab preview.
- [ ] Designer acceptance of layout and preview.

## Implementation and verification

EnemyDefinitionEditor uses SerializedObject for edits and PreviewRenderUtility for static mesh rendering. It copies materials for preview lighting and destroys copies and preview resources on replacement/disposal. It renders no gameplay GameObjects. Imported skinned meshes display their authored mesh pose; this is not an animation preview.

ConfigureEnemyInspectors.Run and Validate now only verify existing prefab-to-definition relationships, custom inspector types, and the queen's subtype, health, base attack and loot; they do not assign reverse references. The migration changed the queen script binding in place; it did not recreate the asset. CaptureEnemyInspector.Run captures the actual custom Inspector into Docs/Images/enemy-inspector.png.

Unity 6000.3.20f1 compilation passed. Seven queen tests and seventeen combat integration tests passed after runtime changes. Subsequent changes affect preview/layout only. Full suite, Player build, live audio audition, pointer-driven Undo/rotation and human visual acceptance were not performed. Previously reviewed enemy lifecycle issues are outside this feature.

2026-10-01 graph revision: replaced the adjustable opponent Block input and point estimates with two fixed-domain graphs generated by CombatMath.Resolve at each defence value. Compile passed and the actual Inspector capture at Docs/Images/enemy-combat-graphs.png was visually inspected for curves, shaded range, labels and full axis bounds. No runtime code or balance changed; gameplay tests were not rerun for this editor-only revision. Human review remains pending.

2026-10-01 ownership correction: removed the definition's prefab property and serialized links from all six enemy assets. The Inspector discovers prefab roots whose WorldTarget references the selected definition, caches results until project assets change, and handles missing or multiple matches without storing a reverse relationship. This supersedes the initial bidirectional preview linking.

2026-10-01 audio/loot and rounding revision: added Editor-only audition controls through the installed Unity AudioUtil preview API and a read-only loot summary. Missing API versions show a fallback message. Rounded graph endpoint/tooltips and stepped bands use nearest-integer rounding with positive halves rounded up, without changing CombatMath or runtime damage. Unity compilation passed; VerifyEnemyPreviews checked .49/.5/1.5/2.5 boundaries, queen high-defence zero and start/stop state for attack/death clips. Actual Inspector capture at Docs/Images/enemy-loot-preview.png visually checked: guaranteed Blood, 25% Silk, audio controls, rounded graphs and labels. No human listening assessment, pointer-button test or gameplay regression rerun for this Editor-only change.

2026-10-01 runtime rounding follow-up: user authorized the same rounding in combat. CombatMath.RoundDamage is now shared by the chart and final attack results; queued hazards round individually before batch aggregation. Final random damage is rounded after modifiers, not interpolated from rounded endpoints. Combat health storage remains float for compatibility, but damage events are whole numbers. Removed the preview-only caveat. Full 216/216 Play Mode suite passed in 174.73 seconds, including seven new formula cases and one hazard integration case. No minimum-one floor was added. The earlier preview-only scope above is superseded.
