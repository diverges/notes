# First Light code and architecture review

Date: 2026-09-30
Scope: current D:/Repos/FirstLight snapshot, Unity 6000.3.20f1. Review only; no production source, prefab, scene or balance changes. Git reports the project as untracked, so this is a current-source review rather than a commit diff.

## Findings

### P1: Inactive authored enemies can interrupt the session update

EnemyActor.cs:49 dereferences _target before checking whether the component has initialized. The field is initialized by Restore, reached through WorldTarget.Awake. An enemy placed under an inactive parent has not received Awake, yet BootstrapSession.cs:176 ticks every registered enemy regardless of active state. The new collector explicitly includes inactive targets, and ValidateSetup accepts this state. The exception interrupts the rest of the session tick and repeats while the enemy remains inactive.

Verified with an inactive clone of the existing spider under an inactive parent: registration passed ValidateSetup, then EnemyActor.Tick(session, 0.1) threw NullReferenceException. This is different from disabling a previously awake enemy, which the current tests cover indirectly. Filter inactive/uninitialized actors before ticking, or establish an explicit initialization contract independent of activation. Add a fixture that starts inactive before Awake, not merely one deactivated after setup.

### P2: Overlapping recipe patterns validate but cannot be crafted

CraftingStationDefinition.cs:33 compares Family and Item for exact equality. Rock/Any log/Any log and Rock/Birch log/Any log are distinct by that test, but both match Rock/Birch log/Birch log. BootstrapHud.Crafting.cs:175 deliberately requires exactly one match and clears the result when there are two. Adding the specialized recipe therefore makes an existing valid combination unavailable without any station validation warning.

Verified using transient clones of the Stone Axe recipe and station with unique recipe IDs, assigning one pattern slot to an existing exact log. Both recipes and the station returned valid; the same three bag selections matched two recipes. Reject overlapping accepted input sets during authoring, or explicitly define a specificity rule and use it consistently in runtime matching and validation. Rejection is the smaller change for the current hidden-recipe design.

### P2: Failed death-loot placement leaves a partially defeated enemy

EnemyActor.cs:109 sets _dead and increments DeathCount before PrepareEnemyDrops at line114. That method can throw when supporting/reachable ground is unavailable. No cleanup or retry owns the failure. Health has already reached zero, but collider removal, visual hiding and death audio are skipped. Later Defeat calls return immediately because _dead is true, permanently losing the loot until reset.

Verified by lifting the live spider two units solely to force the existing placement failure: Punch threw InvalidOperationException, health was zero, collider and visual remained active, and a retry after restoring supported ground created zero drops. This is a fault-injection reproduction, not evidence that the default clearing currently triggers the failure. Preserve the rolled result and use a recoverable placement policy, while completing corpse cleanup regardless of delivery failure. Reuse the preparation/commit discipline already present in crafting; do not reroll on retry.

### P2: The player Inspector offers ineffective combat tuning

DefinitionEditors.cs:109–111 exposes Unarmed combat, Punch interval and Punch damage. PlayerDefinition retains and validates those fields, but no runtime code consumes their properties; enemies require a sword and CombatService owns fixed balance. A designer can change these controls and see no gameplay effect, or make unused data invalid and block setup.

Verified by reference search across runtime/editor code and the existing DatabaseTests.AuthoredPlayerValuesDriveSpawnMovementHudReachWhileCombatUsesFixedBalance test, which deliberately preserves fixed combat. Retire or clearly hide these legacy controls; do not connect them to sword combat without an explicit design change. This is an authoring defect, not a request to alter accepted combat values.

2026-09-30 implementation correction: spec020 replaces the player Inspector and hides these ineffective legacy punch controls. The other three review findings remain open.

## Architecture assessment

The current ScriptableObject definition versus runtime-state split is appropriate for the project's size. Bag reservation, selected-stack identity, outcome projection, and separate fire/loot ownership provide useful boundaries. The shared ordered combat queue expresses a concrete game rule. A generic ability framework, event bus or runtime catalog discovery is not justified by this review.

The priority is aligning validation with executable behavior: scene registration must respect Unity activation, recipe uniqueness must use matching semantics, and death must have an explicit failure/commit boundary. These are small contract repairs with direct gameplay and authoring value. Keep presentation independent of whether item delivery succeeds. Remove stale authoring controls before expanding balancing UI.

InteractionController carries approach, inventory work, station work, reservations and reward delivery in one class. It remains understandable at current scope, but another action family should first identify which fields form mutually exclusive states and introduce a focused operation object only if that removes concrete branching. Do not split it solely to reduce file length.

## Save-system readiness

Implementation at review time (superseded by spec020): BootstrapSession owns Bag, Equipment and PlayerSkills. Bag stores stacks with item references, quantities, stable runtime stack IDs and reservations. PlayerSkills stores XP keyed by SkillDefinition and derives levels from thresholds. PlayerDefinition currently contains identity and tuning only. No persistent snapshot schema or restore operation exists.

User direction, 2026-09-30: use the existing player ScriptableObject as the default and runtime source of truth. This supersedes the initial recommendation to leave player state owned by separate runtime containers and introduce an independent save-data model as the primary boundary.

Recommended implementation of that direction: extend PlayerDefinition into the single player-data model. Its authored asset supplies defaults; instantiate a runtime copy of that same ScriptableObject to own current inventory, equipment, health and skill XP. Bag and PlayerSkills should operate on this data rather than duplicate it. Derived levels continue to use XP and skill thresholds. Keep action reservations and pending interactions transient, with a defined cancellation/resume policy. Runtime cloning is the proposed isolation mechanism, not an implementation already present or a separately confirmed user preference.

Saving still requires writing the runtime object's persistent values to disk and resolving stable item/skill IDs on load; merely deriving from ScriptableObject does not provide disk persistence. The serialization format can be a transport adapter rather than a competing runtime source of truth. Preserve the authored defaults during Play. Define schema versioning, unknown IDs and world persistence in the implementation spec. No save-system or player-model code was changed in this review.

2026-09-30 implementation correction: spec020 implements PlayerDefinition ownership of bag, equipment, health and XP, authored defaults plus a runtime copy, and validated state application that cancels active work. Disk save/load and stable-ID transport remain future work. See the project context for completed validation.

## Verification and limits

Reviewed the model/catalog/validation, inventory and equipment transactions, action execution, session lifecycle, navigation/grid, enemy/combat queue, loot/fire services, UI command routing, presentation, editor tooling, rendering lifecycle and relevant regression cases. Imported plugin implementation was excluded. No Player build, profiler run, broad hardware/input matrix or human playtest was performed.

Temporary Unity probes compiled and ran from Temp/CodeReviewProbe.cs, using cloned assets and runtime fixtures with cleanup. Three findings reproduced; the unused combat controls were established by source references and test intent. Original Start scene was restored in Edit Mode before the regression run. No fixes were applied.

Fresh full Play Mode suite: 166/166 passed in148.97seconds, zero skipped/inconclusive. Current Editor console reports zero errors/warnings and no compile failure. These tests do not cover the reproduced edge cases above; passing the suite does not clear those findings.
