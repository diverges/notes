# Enemy architecture review

Date: 2026-10-01. Scope: current FirstLight source and enemy assets, Unity 6000.3.20f1. Review only; no gameplay changes. Findings below are source-traced unless explicitly attributed to an earlier reproduction. No fresh Unity test run, scene probe, build or profile.

## Assessment

The existing definition/runtime split and shared combat resolution are good foundations. The immediate architecture problem is mixed ownership: EnemyActor is both generic orchestration and spider presentation, CombatService decides aggression as well as resolving attacks, and the queen ability owns the boss navigation obstacle. Repair these boundaries incrementally. A behavior tree, ECS conversion, deep enemy inheritance hierarchy or global event bus is not justified by current behavior.

## Current ownership and behavior

| Owner | Responsibility |
| --- | --- |
| EnemyDefinition | Shared combat stats, attack profile, aggression, roam tuning, loot and audio; adapts enemy to player interaction |
| WorldTarget | Per-instance health, footprint, availability, initial transform and reset; invokes enemy death |
| EnemyActor | Home/destination/wait, roaming and facing, spider leg motion, strike animation/audio, death cleanup and loot; calls queen component |
| CombatService / CombatQueue / CombatMath | Proximity engagement and retaliation, shared second markers, pre-damage rolls and simultaneous commit, XP and presentation dispatch |
| QueenSpiderAttack | Health threshold, volley timing, tile selection, airborne globs, acid lifetime/damage, obstacle cleanup and loot position |
| EnemyAnimation | Animator idle/walk/attack, inferred from transform movement and AttackCount |
| BootstrapSession | Constructs combat and manually advances actors, combat, interaction and visuals |

The session advances actors before combat. An ordinary actor waits, chooses a supported snapped destination within home radius, then moves directly while NavMesh raycasts remain clear. This is constrained wandering, not path following around obstacles. Selection, player proximity or engagement holds the enemy in place and faces it toward the player. CombatService automatically engages aggressive enemies in player reach; manual attacks also engage passive enemies. No pursuit, perception cone, independent enemy reach or return-home combat state exists.

Queen behavior is independent of engagement: below the health threshold its timer launches acid even if the player has left combat. That is an existing gameplay policy, not automatically a bug. Boss thresholds and acid tuning live on the prefab component; shared melee stats live on EnemyDefinition. This is a workable authoring split if documented.

## Concrete findings

### P2: Loot failure interrupts death cleanup

EnemyActor.cs:119 marks the actor dead before PrepareEnemyDrops at line125. WorldLootService.Prepare can throw when supported reachable ground is unavailable. Collider removal and visual hiding at lines128-129 are skipped, and later Defeat calls return immediately. In the combat batch, the exception also interrupts later death handling and notifications after damage has already been applied.

This path was reproduced in the 2026-09-30 architecture review by moving the spider above supported ground. The current source retains it; it was not reproduced again today. Finish the death transition independently of delivery, retain a single rolled loot result and implement an explicit recoverable placement policy. A retry must not reroll loot or revive the actor.

### P2: Disabling the queen ability removes the living boss obstacle

QueenSpiderAttack.Clear disables NavMeshObstacle; OnDisable calls Clear. There is no OnEnable restoration. Disabling then re-enabling the attack component therefore leaves the obstacle disabled until Restore. The existing disable test checks cleared patches and stopped volleys but never checks navigation after re-enable.

Let actor/footprint lifecycle own navigation blocking, and let the ability own its patches. Test both component disable/re-enable and whole-object disable/re-enable without resetting health.

### P2: Manual ticking ignores EnemyActor.enabled

BootstrapSession.cs:181 calls every target's EnemyActor.Tick. Its guard at EnemyActor.cs:54 checks WorldTarget availability but not the actor's isActiveAndEnabled. Disabling only EnemyActor therefore leaves its movement and queen delegation running. Specify whether actor disable suspends the whole enemy or only behavior, then apply that contract consistently to ticking and combat eligibility.

The previous inactive-before-Awake null-reference finding is superseded: the current guard includes !_target. Do not report the old crash as current.

### P3: Animation pause uses a different clock

EnemyAnimation.cs:26 freezes Animator speed while the session is paused, but lines33-38 use Time.time to expire the attack window and continue selecting animation states. SetMenuPaused does not stop Unity's clock. A menu opened mid-attack can consume the attack window during pause and queue an idle transition. Use pause-aware elapsed time and avoid state transitions while paused.

## Recommended structure

Keep EnemyDefinition as shared configuration and WorldTarget health as the single current health owner. Do not add a second health field in a new enemy class.

Keep EnemyActor as a small coordinator with explicitly supplied session dependencies. Move procedural leg/body motion and sounds into presentation, alongside the existing Animator adapter. Presentation should consume movement and attack/death facts; it should not decide damage or loot success.

Make queen ability lifecycle composable so generic enemy code does not branch on QueenSpiderAttack for facing or loot. A small optional ability contract is sufficient if it removes the existing reverse dependency. Stationary footprint facing and supported drop placement should be general policies, not checks for a boss class.

Keep combat timing, math and simultaneous commits centralized. Move enemy intent decisions (when to engage, hold or roam) toward one behavior owner as those rules expand. Start with explicit named modes and transition methods when needed; separate state classes are warranted only when modes acquire substantial entry/exit behavior. Do not add chase or aggro-reset rules without design approval.

Keep direct calls for required work. Local notifications can feed independent presentation consumers; avoid turning death cleanup into an unordered event chain. Supply session references at initialization instead of FindFirstObjectByType fallbacks. Inject randomness for roam/volley selection as already done for combat and loot when reproducible behavior tests are added.

Physics.SyncTransforms occurs per actor and is a possible scaling cost. Profile representative enemy counts before optimization; batching must preserve the physics queries that depend on updated transforms. Likewise, pooling acid objects is optional until allocation/performance evidence warrants it.

## Verification priorities

1. Fault-inject loot placement failure; assert death finishes, the combat batch finishes, and any later delivery retains the original roll.
2. Exercise initially inactive enemies, actor-only disable, queen ability disable and re-enable, and whole-object re-enable.
3. Pause mid-attack and resume; verify presentation and simulation remain consistent.
4. Preserve existing shared-marker, mutual-defeat, aggression/retaliation, escape, death-once, reset and acid occupancy behavior.
5. After separating components, validate representative Spider, Boar/Warg and Queen prefabs and run focused integration tests plus pure combat tests.

Reviewed runtime implementations, SpiderTests, QueenSpiderTests, combat test declarations, representative definitions and earlier review evidence. Existing tests cover normal roaming, loot once, threshold/cadence, acid, pause/reset and combat rules, but their historical passing results are not fresh verification of these findings. Entire project is currently untracked in Git; this is not a diff review.

## References

- Unity, architecture as projects scale: separation of gameplay logic and presentation, proportionate composition. Consulted 2026-10-01. https://unity.com/how-to/how-architect-code-your-project-scales
- Unity, game programming patterns: select patterns according to concrete project needs. Consulted 2026-10-01. https://unity.com/blog/games/level-up-your-code-with-game-programming-patterns
