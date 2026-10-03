# 000-bootstrap: Implementation and verification

Status: Complete; accepted by the user on 2026-09-28
Updated: 2026-09-28 (local)
Environment: Windows Editor, Unity 6000.3.20f1, URP 17.3.0, Input System 1.19.0, AI Navigation 2.0.13, Unity Pipeline 0.8.0-exp.1.
Scope amendment: The user approved implementation and accepted Play Mode coverage. Player/web builds are deferred to the user.

## Implemented result

One authored clearing provides click-to-move, bounded orbit/zoom, sticks and rocks, a 24-slot bag, timed flax-to-string work with separate collection, repeating unarmed punches, context Examine, the requested HUD, focus/panel pause, and Reset test. Six reusable primitive prefabs retain root identity/collision independently of their visual children. No new packages, weapons, progression, survival, persistence, or building systems were added.

The scene owns the runtime session and references. The bag is plain C# with atomic additions and slot-preserving reservations. A single interaction controller owns the active intention and timers; cancellation releases reserved input and clears movement. Navigation revalidates range after reaching its destination. UI Toolkit hit testing prevents world commands through UI.

## Scenario evidence

Latest result after the ScriptableObject database migration (2026-09-28): **30 tests passed, 0 failed, 0 skipped** in 43.24 seconds. See [001-game-database](001-game-database.md) for the six added database checks. The separate normal-speed Input System pass succeeded for stick/rock pickup, Bag/Skills switching, Close, right-click IMGUI Examine, spin/collect, punching, Escape cancellation, scroll zoom, middle-drag orbit, Settings, and Reset. HUD and camera inputs did not issue world commands.

The Editor console reported **0 errors and 0 warnings**, with no compilation failure, after the final pass. Pipeline's retained log buffer still contained earlier development failures; these are distinct from the current Editor console. The editor was left stopped and ready with Start open. All FirstLight assets had metadata and local Markdown file links resolved.

Automated tests load the authored scene in Play Mode and exercise real components and NavMesh paths. Most use accelerated simulation; the approach regression and separate input smoke pass use normal speed. Focus tests invoke the same pause entry point used by `OnApplicationFocus`; this does not claim an operating-system focus-switch usability test.

| Scenario | Evidence |
| --- | --- |
| S.1.5: First ground pickups | `PickupsTransferExactlyOnce`; normal-speed pointer pickup of stick and rock. |
| S.1.6: Pickup capacity | `FullBagPreservesPickupThenResetAllowsIt`; full compatible stacks reject repeated pickup, then reset permits transfer. |
| S.1.7: Cancelled pickup approach | `NewGroundCommandCancelsPickupApproach`; item remains and bag is unchanged. |
| S.1.1: Station result | `StationReservesCompletesAndCollectsOnce`; normal-speed pointer spin and collection. |
| S.1.2: Interrupted work | `CancelledWorkReturnsReservedInputEvenWithFullBag`; reserved input returns even after filling remaining slots. |
| S.1.3: Full bag at collection | `FullBagKeepsReadyOutputAcrossRepeatedCollection`; output stays at the wheel with no transfer. |
| S.1.4: Unreachable target | `UnreachableTargetDoesNotSpendOrTransfer`; the enclosure prevents access. |
| S.2.1: Panel switching | `PanelSwitchingAndExamineDoNotCommandWorld`; pointer pass verifies button switching and no movement through UI. |
| S.2.4: Alternate action | Same panel test and pointer context-menu Examine; pickup remains in world. |
| S.3.1: Punch cadence | `RepeatedClicksPreserveFirstPunchInterval`; first hit waits, repeated clicks do not accelerate it. |
| S.3.2: Disengage | Same cadence test replaces punching with movement; normal-speed input checks Escape cancellation. |
| S.3.4: Target replacement/invalidation | `ReplacingTargetStopsOldHitsAndZeroHealthStopsNewHits`, `RangeLossAndDestroyedTargetCancelSafely`. |
| S.4.2: Pause | `PanelsAndFocusPauseTimersWithoutCatchup`, `PauseFreezesMovementAndPunchTimer`; position and elapsed work remain fixed. |
| S.4.4: Reset during action | `ResetCancelsWorkAndPunchAndRestoresWorld`; pointer Settings/Reset restores pickups and bag. |

Additional checks cover transaction atomicity, changed capacity/prerequisites during approach, visual replacement, and normal-speed arrival within usable range. Asset validation checks six prefab root colliders/visual children, saved navigation, player footprint, scene target/player references, and missing scripts.

## 2026-09-27: World-action UI revision

User feedback specified RuneScape-style primary/secondary interactions and IMGUI for world action UI. `WorldActionGui` now projects the primary label and right-click menu above the target's world bounds. Left click performs the first action; right click shows only the remaining action (Examine in this bootstrap) and Cancel. The old UI Toolkit context menu and fixed HUD hover label were removed. Bag, Skills, Settings, and general feedback remain in the HUD.

Two new Play Mode tests verify label tracking, label-click execution, outside-click dismissal without movement, reset/removal cleanup, and hiding behind the camera or while a panel is open. Existing Examine coverage and the real Input System smoke script now target the IMGUI menu. The primary and secondary views below were captured at 1920 × 1080 from Play Mode and visually inspected. The final Editor console had zero errors/warnings.

![Primary action above the hovered rock](D:/Repos/FirstLight/Docs/Images/bootstrap-primary-action.png)

![Remaining actions in the world-anchored IMGUI menu](D:/Repos/FirstLight/Docs/Images/bootstrap-other-actions.png)

## 2026-09-27: Minimal UI designer review

A designer agent reviewed eleven captured states while a coder agent implemented two passes. Persistent heading/help and routine state narration were removed. The fixed HUD now contains health and Bag, Skills, and Settings buttons. Controls appear in Settings; Skills has one shared progression disclaimer. Result, error, and Examine notices last four unpaused seconds. Panel headers state Paused once.

Spinning and punch timing use IMGUI above the active target, independent of pointer hover. The active target does not repeat its action in a second hover label. The second review caught and corrected clipped hover text and menu/progress overlap at the top edge. The designer accepted the revised screenshots, including a long Examine notice. This is model visual review, not a human usability result.

![Minimal idle HUD](D:/Repos/FirstLight/Docs/Images/ui-review-idle.png)

![World-anchored punch cadence](D:/Repos/FirstLight/Docs/Images/ui-review-punching.png)

![Separate menu and progress at the viewport edge](D:/Repos/FirstLight/Docs/Images/ui-review-top-edge.png)

![Examine notification](D:/Repos/FirstLight/Docs/Images/ui-review-examine.png)

Additional regression coverage checks world progress tracking, pause/reset/cancel cleanup, top-edge menu separation, notification expiry, and absence of routine repeated notifications. The normal-speed Input System pass was repeated successfully. Runtime background execution was temporarily enabled for unattended captures and restored afterward; project settings were unchanged.

## Task disposition

| Tasks | Status and evidence |
| --- | --- |
| T.01 | Complete: user authorized scope and Play Mode coverage. |
| T.02 | Complete: user-created pinned project retained; open/run guide provided. |
| T.03 | Deferred by user: no build/export was attempted. |
| T.04–T.08 | Implemented: authored scene, prefabs, camera, lifecycle, bag, and HUD; covered by asset, component, and input checks. |
| T.08a, T.09 | Implemented: pickup transfer/cancellation/capacity and station reserve/convert/collect. |
| T.12–T.14 | Implemented: punch timing/range/replacement, pause, and reset. |
| T.16–T.17 | Automated and input verification recorded here; runtime regressions corrected before handoff. |
| T.18 | Deferred outside accepted milestone: no dedicated first-time-user observation was performed. |
| T.19 | Complete: technical fixes verified; user accepted the result and closed the milestone on 2026-09-28. |

## Issues found and corrected

- Native `NavMeshPath` creation moved out of a MonoBehaviour field initializer into `Awake`.
- Removed duplicate test-runner assembly references introduced by combining explicit and optional test references.
- Scene teardown now tolerates the navigation agent being destroyed before HUD/session cleanup.
- Pause explicitly zeros navigation velocity; `isStopped` alone allowed measurable drift in the pause test.
- Approach candidates leave a margin for stopping distance and arrival tolerance. The normal-speed input pass exposed an arrival just outside punch range that accelerated tests missed; a regression test now covers that route.

## Review boundaries

![Actual Bootstrap Play Mode capture with Bag open](D:/Repos/FirstLight/Docs/Images/bootstrap-playmode.png)

The screenshot was captured from the running Game view, including the UI Toolkit overlay, and visually inspected at 1280 × 720. The Game view/input surface was 1920 × 1080; this is a desktop Editor check, not a responsive/mobile or browser validation.

The user accepted the reviewed prototype and closed the milestone on 2026-09-28. Dedicated first-time usability observation remains unperformed; final art and broader usability research are future work. Browser/device tests and builds remain deferred. Start is the configured entry scene and loads Bootstrap; the original template scene is retained.

The prototype has a fixed authored target set. Reset restores surviving registered objects; externally destroying a whole object in Play Mode requires re-entering Play Mode to recreate it. Normal player actions retain pickup and enemy roots so Reset restores them.

## Navigation

- [Approved specification](000-bootstrap.md)
- [Play Mode guide](D:/Repos/FirstLight/Docs/agent-docs/bootstrap-playmode.md)




## 2026-09-27: Interaction lock and console style

Active spinning and punching ignore new movement/target commands and stop navigation velocity. Completion, explicit cancellation, reset, or target invalidation release the lock; panel/focus pause preserves it. Escape closes open UI first, then cancels on the next press. Instant pickups/collection still have no animation duration; no clips were added.

Health moved to the top left. HUD panels and world IMGUI use brown/brass styling with warm text and a red health bar. Current idle, interaction, and panel captures were refreshed; Settings and spinning captures were visually inspected. Earlier Examine/wireframe imagery is historical for placement and styling.

All 23 Play Mode tests passed (47.20 seconds), including stationary interaction, ignored replacement clicks, pause/resume preservation, and movement after release. Existing cancellation tests now explicitly cancel before moving/replacing. Normal-speed pointer/keyboard smoke passed, including Escape. The final console contained one Pipeline request-timeout error from the background-execution setup call; captures and input checks subsequently completed. No gameplay errors or warnings were observed. An earlier test-runner startup failed before executing tests; rerunning after compilation settled produced the 24 passing results above. Editor left stopped; no builds performed.


## 2026-09-27: Scrollable log and world markers

The fixed lower-left game log retains 100 messages, scrolls without camera/world click-through, and follows new messages only from the bottom. Latest resumes following. Session-owned history preserves multiple messages in one frame and across HUD panel changes. Routine movement/punch status is excluded. Gold IMGUI destination rings mark accepted navigation, and hovered/context targets have a gold footprint outline; existing action labels remain.

A normal-speed input check caught an initial scroll-before-layout bug; following now waits for content geometry/layout. The updated check passed real queued mouse-wheel scrolling and verified unchanged camera/player positions. Current hover, destination, log, action, and panel screenshots were captured. The first three were visually inspected; scrollbar styling was then made consistent with the brown/brass HUD.


Final regression: 24/24 Play Mode tests passed in 51.19 seconds. Updated normal-speed console and gameplay input checks passed. Current Editor console had no errors/warnings; no builds performed.


## 2026-09-28: Compact console

Removed the Game log title and visible scrollbar; removed message margins/padding for closer spacing. Mouse-wheel scrolling and Latest remain. Compilation and the existing normal-speed input smoke passed, including console scroll/click isolation. Visually inspected Docs/Images/console-compact.png. Editor stopped; current console zero errors/warnings. The full 24-test suite was not rerun for this presentation-only refinement.


## 2026-09-28: Designer spacing and feedback pass

A designer agent reviewed fresh idle, hover/context, destination, spinning/punching, console, Bag, Skills, and Settings captures. The revision standardizes 24px outer margins, 8px navigation/skill spacing and 16px panel content insets/section spacing. Health and Settings slider now match the brass/brown palette; IMGUI uses parchment text and brass borders. The active panel button is highlighted, and Settings explains Escape and console scrolling without implementation jargon. The compact console retains no title/scrollbar and tightly spaced text.

The second screenshot review caught a label-margin specificity regression that removed Bag slot gaps. An explicit slot rule restores the regular grid. Compilation and normal-speed input smoke passed, including console scroll/click isolation, panel switching, world actions, cancellation, camera, and reset. The full gameplay suite was not repeated for these presentation changes.

The final Bag refinement centers the existing 72px slots for balanced side insets and preserves the four-column layout and regular gutters. A wider-slot trial wrapped to three columns and was reverted after visual review.



Designer final acceptance: the corrected Bag is four columns by six rows, with balanced side insets and regular gutters. Other UI states passed the spacing/style/feedback review. This is model visual review, not a replacement for user playtesting. Editor left stopped with no current errors/warnings.


## 2026-09-28: Start scene

Added Assets/Scenes/Start.unity with only the requested First Light title and Start button. Scene-local StartScreen builds UI Toolkit content and loads Bootstrap in Single mode with no artificial delay. Start and Bootstrap are enabled first in the scene list; the original SampleScene entry is preserved afterward. Shared panel settings are reused without modification.

Compilation succeeded. Tools/StartScreenSmoke.cs verified exactly one title/one button, used queued pointer press/release to click Start, and confirmed BootstrapSession exists and StartScreen is removed afterward. The captured screen was visually inspected. Current console zero errors/warnings; Editor left stopped on Start. No player build or full gameplay-suite rerun was needed for this isolated entry flow.



