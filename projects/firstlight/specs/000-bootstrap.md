# 000-bootstrap: Version 0 interaction prototype

Status: Complete; accepted by the user on 2026-09-28
Updated: 2026-09-28
Target: First Light v0
Implementation status: Authorized by the user on 2026-09-27. Unity 6000.3.20f1 is pinned. Player builds are deferred to the user; these sessions use Play Mode for coverage.

[Verification report](000-bootstrap-verification.md): 30 passing Play Mode tests, normal-speed input checks, task disposition, and user acceptance.

## Problem statement

**The bootstrap exists to prove the game's interaction system.** It must show that a player can understand a target, choose an action, move into reach, perform it, interrupt it, and understand the result.

The earlier draft bundled Cooking progression, combat progression, recovery, and persistence into v0. Those systems are deferred. Bootstrap success is evidence that the same interaction flow works across several representative objects, not a miniature version of the complete game.

The [game design document](D:/Repos/FirstLight/Docs/game-design-document.md) remains authoritative for the full game. This spec defines the smaller interaction milestone.

## Proposal

Build one small Unity test clearing with a capsule player, green rectangular trees, loose sticks and rocks, one basic processing station, and a nearby enemy capsule. Start by picking up sticks and rocks. Then use a station and click the enemy to punch. Each example exercises a different action through the same selection, approach, execution, cancellation, and feedback flow.

The environment should take only a few minutes to explore and reset instantly for another pass. It is a test space, not a survival session or content showcase.

**Confirmed direction:** point-and-click play; elevated RuneScape-like camera; player occupies 1 × 1 tile; primitive prefab assets; sticks and rocks first; clicking nearby enemies causes unarmed punching; no minimap; health top left; Bag, Skills, and Settings buttons lower right. Unity and web remain the target. Building comes later.

The user approved implementation of this interaction-only scope. The requirements below define the contract with one delivery amendment: Editor Play Mode is sufficient for this milestone; browser builds and browser/device verification are deferred to the user.

### In scope and why

| Area | Minimum implementation | What it proves |
| --- | --- | --- |
| Camera and movement | Elevated camera, bounded zoom/rotation, click-to-move around simple obstacles. | Players can identify objects and reach them predictably. |
| Target selection | Visible target name and action; primary click and alternate-action menu with Examine. | Intent is clear before an action happens. |
| Ground pickups | Loose sticks and rocks near spawn; walk into reach and collect one item. | Immediate actions change world and inventory consistently. |
| Bag | Proposed 24 slots, basic stacks and counts; no item equipment system. | Pickup results and capacity failures are understandable. |
| Station | One wheel: use supplied flax, wait briefly, then Collect string. No harvesting subsystem or recipe catalogue required. | A timed action has prerequisites, cancellation, and a separate result-collection step. |
| Punching | Click a nearby enemy capsule; approach and repeat punches at a visible cadence until cancelled or the target becomes invalid. | Repeating actions respect range, target validity, and replacement commands. |
| HUD | Health placeholder top left; Bag, Skills, and Settings panels lower right. | UI and world input do not conflict. |
| Reset | A clearly labeled Reset test action restores the authored test state. | Boundary cases can be repeated without persistence work. |
| Delivery | The small test scene runs in Editor Play Mode. Builds are deferred to the user. | The interaction works in the approved review environment. |

The station is an interaction fixture, not a crafting-system milestone. Provide a small flax supply in the initial bag; sticks and rocks are still the first world interactions. The enemy needs only a target, range, simple hit feedback, and a hit counter or health value that can reach zero. No retaliation, pursuit, loot, player defeat, or recovery loop is needed.

### Frugal assets and prefab plan

- Player: capsule at the 1 × 1 tile scale.
- Trees: upright green rectangular blocks.
- Sticks and rocks: thin brown blocks and small gray primitives.
- Wheel: labeled block or minimal primitive assembly.
- Enemy: differently colored capsule; a small movement or flash indicates a punch.
- Ground: simple surface with a few obstacles and an intentionally unreachable test target.

Use reusable prefabs and a small set of flat-color materials. Keep gameplay identity and collision on a stable root with replaceable visual children. Demonstrate that changing appearance does not break interaction references or reachability. No custom art, asset shopping, detailed animation, or generalized asset framework is required.

### Out of scope

- Skill experience, levels, milestones, stat bonuses, and skill balancing, including Cooking and Strength progression.
- Cooking, food use, Smithing, recipe discovery systems, resource renewal, and broad production chains.
- Survival meters, health recovery, enemy AI, combat balance, loot, player death, weapons, or equipping.
- Save/load, accounts, persistent settings, Continue/New session flows, and offline simulation.
- Player building, ground preparation, terrain response, and construction content.
- Mobile/touch delivery, multiplayer, procedural worlds, final art/audio, public hosting, and production optimization.

The Skills panel lists Cooking, Smithing, Weapon Mastery, Strength, Vitality, and Agility with one shared progression-not-implemented notice. The health bar can remain at a fixed test value. These preserve the intended HUD without turning the bootstrap into those systems' implementation.

### Interaction contract

Every world action follows this proposed lifecycle:

**Select target/action → validate → approach if needed → validate again → execute → show result.**

A target action defines its label, prerequisites, usable range, and whether it is immediate, timed, or repeating. The player has one active world intention at a time. Object-specific results differ, but selection, approach, cancellation, and failure feedback should behave consistently. This is a behavioral contract, not a prescribed class hierarchy.

A new ground or object command replaces an approach; active spinning and punching require Escape cancellation before replacement. Opening a context menu does not itself perform an action. Moving before a pickup completes leaves the item in the world. Cancelling station work returns reserved input. Cancelling punches stops future hits. Completed results are never undone or granted twice.

Start with simple, editable test values: one flax becomes one string, a station cycle takes 2 seconds, punches repeat every 2 seconds, and material stacks hold 20. The first punch waits one interval. Values serve observable tests, not balance. Range is measured relative to the 1 × 1 player scale and must account for both objects' footprints.

### 2026-09-27: Interaction lock and console styling

Active spinning and punching lock player movement; ground and other-target clicks are ignored until completion or explicit Escape cancellation. Approaches remain replaceable. Panel/focus pause preserves the lock; reset and unavailable targets release it. Pickups and collection remain instantaneous until animations are authored. This supersedes earlier movement-click cancellation during active work/combat. No animation clips are included yet.

The HUD uses restrained brown panels, brass borders, warm text, and red health at the top left. This supersedes the lower-left health placement in older wireframes and captures. Action timing remains world-anchored IMGUI; brief feedback remains at the lower left.

## 2026-09-27: Scrollable game log and world feedback

The lower-left game log retains the last 100 result, error, and Examine messages for the current run, including messages emitted within the same frame. It supersedes the fading notification. Scroll back to read history; new messages follow automatically only when already at the bottom. Latest returns to the newest entry. Log pointer/wheel input is isolated from world movement and camera zoom; panels do not hide the log. Routine movement and punch cadence do not flood it.

A gold ring marks the accepted navigation destination while walking or approaching. It clears on arrival/cancel/reset and hides during pause. A gold footprint outline identifies the hovered target, alongside its primary action label; a context menu retains that target outline. These use world-projected IMGUI and do not modify shared materials. Health remains top left and action bars stay above their targets.

## References and review decisions

- [Camera reference](D:/Repos/FirstLight/Docs/Images/References/user-camera-reference.png): framing and object selection.
- [HUD mock](D:/Repos/FirstLight/Docs/Images/hud-layout.svg): layout and panel states.
- [Discovery storyboard](D:/Repos/FirstLight/Docs/Images/storyboard-discovery-v2.png): processing example after the first ground pickups.

| ID | Decision before implementation | Proposed starting point |
| --- | --- | --- |
| D.1 | Approve representative interactions | Ground pickup, one timed station, repeated punching, and HUD/world input separation. |
| D.2 | Select Unity version and minimum setup | Inspect installed tools and select the project version and needed packages; no version assumed here. |
| D.3 | Choose a desktop browser/device baseline | Record the actual test machine, browser, viewport, and build settings. Require a responsive, error-free review path; performance tuning is not a separate milestone. |
| D.4 | Confirm input and interruption behavior | Left click for primary action/move, right click for alternatives, Escape to cancel/dismiss; pause simulation while a HUD panel is open or focus is lost. |

D.1 and D.4 are accepted by the implementation request. D.2 uses the existing Unity 6000.3.20f1 project and installed packages. D.3 browser/device selection is deferred with builds. D.5 from the earlier draft concerned persistence and is retired.

## Use cases

### UC.1: Pick up materials and use an object

- Actor: Player.
- Goal: Understand and execute immediate and timed world actions.
- Starting conditions: Sticks and rocks visible near spawn; a small supplied flax stack and an accessible wheel.
- Main flow: Pick up a stick and rock, inspect Bag, choose Spin flax at the wheel, wait, then Collect string.
- Outcome: Correct world and bag changes with understandable feedback and no duplication.

### UC.2: Inspect targets and use the HUD

- Actor: Player.
- Goal: Choose an action and inspect UI without accidentally commanding the character.
- Starting conditions: World objects and the three HUD buttons are available.
- Main flow: Open an object menu, Examine, dismiss it, then toggle Bag, Skills, and Settings.
- Outcome: The chosen action is clear; UI clicks never pass through to the world.

### UC.3: Start, replace, and stop punching

- Actor: Unarmed player.
- Goal: Engage a nearby enemy and control when the action stops.
- Starting conditions: One or more enemy capsule instances are available.
- Main flow: Click an enemy, approach, punch repeatedly, then click elsewhere or another target.
- Outcome: One current intention governs movement and hits; invalid or out-of-range targets receive no further hits.

### UC.4: Interrupt and repeat the test

- Actor: Reviewer.
- Goal: Repeat normal and boundary interactions from known state.
- Starting conditions: Work or punching may be active and the bag may contain pickups.
- Main flow: Open a panel or change focus, resume, then Reset test.
- Outcome: No catch-up actions occur; reset restores the authored scene and clears all active intentions.

## Requirements

### 1: Project, world, and camera

- **R.1.1:** The approved project shall open with Unity 6000.3.20f1 and run the test scene in Editor Play Mode using documented steps. Player builds are deferred to the user.
- **R.1.2:** The player shall occupy a 1 × 1 tile footprint and navigate authored paths around obstacles.
- **R.1.3:** Camera rotation/zoom shall preserve the elevated view and shall not issue world actions.
- **R.1.4:** The scene shall contain only the objects needed to demonstrate the interaction cases; building and terrain modification are excluded.
- **R.1.5:** Player, trees, pickups, station, and enemy shall use primitive reusable prefabs.
- **R.1.6:** Replacing prefab visuals shall preserve gameplay identity, collision, references, and interaction behavior.

### 2: Shared world interaction

- **R.2.1:** A new movement or action command shall replace a prior approach. Active spinning/punching shall ignore replacement commands until completion or explicit cancellation. An out-of-range action shall approach a reachable interaction position before execution.
- **R.2.2:** The system shall validate target existence, prerequisites, and reachability before execution, including after approach. Failure shall explain the reason without spending items or applying a hit.
- **R.2.3:** Station work shall reserve input, release it on cancellation, and convert it to ready output once on completion.
- **R.2.4:** Collect shall transfer output only when it fits in the bag; failed or repeated clicks shall not lose or duplicate output.
- **R.2.6:** Loose sticks and rocks shall be the first world interaction opportunity. Pick up shall transfer one item without tools or skill gates and remove it from the world only after a successful transfer.
- **R.2.7:** Hover shall identify the target and first action through a world-anchored IMGUI label. Left click performs the first action; right click opens the remaining actions in an IMGUI menu anchored to that target, without duplicating the primary action. Examine shall show short target information without performing its primary action. Outside click/Escape dismisses the menu without issuing a world command. Bag, Skills, and Settings remain UI Toolkit HUD panels.

### 3: HUD and bag

- **R.3.1:** Show health top left and separate Bag, Skills, and Settings buttons lower right, with no minimap.
- **R.3.8:** Keep the persistent HUD minimal: do not repeat help text, action names, or idle/walking state in a fixed status card. Show spinning and punch-interval progress through IMGUI anchored above the active world target, even when the pointer leaves it. Result/error/Examine feedback may use a brief notification; controls belong in Settings.
- **R.3.2:** Buttons shall toggle the matching panel, switching closes the prior panel, and Close/Escape dismisses it. UI interaction shall never issue a world command.
- **R.3.3:** The bag shall enforce the proposed 24-slot capacity and stack limits with exactly-once pickup and collection.
- **R.3.4:** Skills shall show the six confirmed names in order and identify progression as unimplemented; no skill awards or rank-up behavior is required.
- **R.3.6:** Settings shall provide a controls summary, camera-sensitivity adjustment, and Reset test. Values need only last for the current run.

### 4: Punch interaction

- **R.4.1:** Clicking a nearby enemy shall select Punch without equipment, approach if necessary, and repeat while the target remains valid and in range.
- **R.4.2:** Repeated clicks shall not accelerate the punch interval. Each resolved punch shall produce one visible hit result.
- **R.4.3:** Explicit cancellation shall stop future punches against the old target before another command is accepted. No out-of-range hit shall resolve.
- **R.4.4:** When the target reaches zero test health or is removed, engagement shall end without further hits. No player defeat or reward system is required.

### 5: Pause, reset, and delivery

- **R.5.1:** Opening a HUD panel or losing focus shall pause movement and action timers; resuming shall not apply catch-up work or punches.
- **R.5.5:** Editor Play Mode shall support the reviewed interaction cases without unhandled errors. Browser parity is deferred to the user's later build pass.
- **R.5.6:** Reset test shall cancel current actions and clear menus/selections before restoring player, bag, pickups, station output, and enemy state. It shall not carry old callbacks or commands into the reset scene.

Retired requirement IDs: R.2.5 (resource renewal), R.3.5/R.3.7 (skill progression/food), R.4.5 (combat reward reset), R.5.2–R.5.4 (persistence). These are not bootstrap obligations.

## Scenarios

### UC.1: Pick up materials and use an object

#### S.1.5: First ground pickups

- Given: Loose sticks and rocks near spawn and available bag space.
- When: The player clicks a stick, then a rock.
- Then: The character reaches each and transfers it once; only successfully collected objects disappear.

#### S.1.6: Pickup capacity

- Given: A loose rock and a full bag with no compatible stack space.
- When: The player repeatedly attempts pickup.
- Then: Capacity feedback appears and the rock remains; a fresh reset allows the same interaction to succeed.

#### S.1.7: Cancelled pickup approach

- Given: The character is approaching a stick.
- When: Another ground destination is clicked before arrival.
- Then: The new intention replaces pickup and both the stick and bag remain unchanged.

#### S.1.1: Station result

- Given: One available flax and an empty reachable wheel.
- When: Spin flax completes and Collect is selected.
- Then: One flax becomes one string exactly once, without any recipe unlock or skill system.

#### S.1.2: Interrupted station work

- Given: Flax reserved for incomplete work.
- When: Escape cancels the action.
- Then: Work stops, input returns, and no output is created.

#### S.1.3: Full bag at collection

- Given: Ready string and no bag space.
- When: Collect is clicked repeatedly.
- Then: Output remains with a capacity message; no duplication or loss occurs.

#### S.1.4: Unreachable target

- Given: A target behind an impassable obstacle.
- When: Its primary action is selected.
- Then: The player receives a reason and no item or hit result is applied.

### UC.2: Inspect targets and use the HUD

#### S.2.1: Panel switching

- Given: Bag is open over a world object.
- When: Skills is selected, then dismissed with Escape.
- Then: Only the chosen panel appears, all six skills are listed as unimplemented, and no world action occurs beneath the clicks.

#### S.2.4: Alternate action

- Given: A pickup with Pick up and Examine actions.
- When: The context menu opens and Examine is selected.
- Then: Only the object's description appears; nothing is collected and dismissing the menu does not move the player.

### UC.3: Start, replace, and stop punching

#### S.3.1: Punch cadence

- Given: An unarmed player and an in-range enemy.
- When: The enemy is clicked repeatedly.
- Then: One punch sequence runs at the configured interval, with one result per hit.

#### S.3.2: Disengage

- Given: Punching is active.
- When: The player presses Escape, then clicks a distant ground point.
- Then: Escape releases the movement lock; subsequent movement is accepted and no later hit lands on the old target.

#### S.3.4: Replace or invalidate a target

- Given: Enemy A is selected and enemy B is reachable.
- When: A is explicitly cancelled before selecting B, or the active target is removed/reaches zero test health.
- Then: A receives no further hits after replacement; an invalid active target ends the action cleanly.

### UC.4: Interrupt and repeat the test

#### S.4.2: Pause

- Given: A station action or punch is partway through its interval.
- When: A panel opens or focus is lost, then play resumes.
- Then: The interval pauses and resumes without accumulated results.

#### S.4.4: Reset during an action

- Given: Work has reserved flax or the player is approaching/punching a target.
- When: Reset test is selected.
- Then: The baseline player, bag, pickups, station, and enemies return; no previous action subsequently changes them.

Retired scenarios: S.2.2–S.2.3 (Cooking/food), S.3.3 (victory/recovery), S.4.1/S.4.3 (persistence). Prior IDs are retained for the continuing cases.

## Task list

Implementation is complete and accepted. Task evidence and deferred validation are recorded in the bootstrap verification report. The first pickup is the baseline for timed and repeating actions.

| ID | Task and completion evidence | Depends on | Covers |
| --- | --- | --- | --- |
| T.01 | Review the interaction-only boundary and D.1–D.4. Select tools and test baseline before implementation. | None | Scope |
| T.02 | Use the user-created, pinned Unity project; document open/Play Mode steps. | T.01 | R.1.1 |
| T.03 | Deferred by user: player/web builds. Record the Editor Play Mode environment instead. | T.02 | R.5.5 |
| T.04 | Assemble primitive prefabs and the tiny test clearing. Verify player scale, obstacle routes, and visual replacement. | T.02 | R.1.2, R.1.4–R.1.6 |
| T.05 | Establish camera framing and click-to-move; review selection visibility at the intended camera distance. | T.04 | R.1.3, R.2.1 |
| T.06 | Implement the shared target/action lifecycle, approach, revalidation, cancellation, context menu, and failure feedback. | T.05 | R.2.1–R.2.2, R.2.7 |
| T.07 | Add minimal bag state, capacity, stacks, and atomic item transfer. | T.02 | R.3.3 |
| T.08 | Add Bag, placeholder Skills, and Settings panels, health placeholder, and UI input isolation. | T.06–T.07 | R.3.1–R.3.4, R.3.6 |
| T.08a | Prove loose-stick and rock pickup, capacity failure, and cancelled approach before adding another action type. | T.06–T.08 | R.2.6; S.1.5–S.1.7 |
| T.09 | Add one timed wheel action and explicit collection through the shared lifecycle; verify reservation and cancellation. | T.08a | R.2.3–R.2.4; S.1.1–S.1.3 |
| T.12 | Add enemy-capsule Punch using the same lifecycle, with cadence and minimal visible hit feedback. | T.08a | R.4.1–R.4.2; S.3.1 |
| T.13 | Verify movement cancellation, target replacement, range loss, and target invalidation for punches. | T.12 | R.4.3–R.4.4; S.3.2, S.3.4 |
| T.14 | Complete pause/focus behavior and Reset test across pickup, station, and punch states. | T.09, T.13 | R.5.1, R.5.6; S.4.2, S.4.4 |
| T.16 | Add focused checks for exactly-once transfer, cancellation, range validation, action replacement, and stale actions after reset. | T.14 | State integrity |
| T.17 | Run every active scenario in Editor Play Mode; record results and input/runtime failures. Builds deferred by user. | T.16 | R.5.5; all active scenarios |
| T.18 | Observe first-time users trying pickups, the station, punching, and panel switching. Ask what they expected each click to do; record confusion and misclicks. | T.17 | Interaction clarity |
| T.19 | Resolve blocking interaction failures, rerun affected checks, and review the result with the user before adding game systems. | T.18 | Milestone decision |

Retired tasks: T.10 (progression), T.11 (Cooking), T.15 (save/load). They are deferred to future specifications, not completed.

### Review checklist

- [x] Approve the three action examples and interaction-only scope.
- [x] Confirm command replacement, cancellation, pause, and reset behavior.
- [x] Use Unity 6000.3.20f1; browser/device baseline deferred with builds.
- [x] Implementation authorized by the user; Play Mode coverage accepted for these sessions.

### Definition of done

A reviewer can select, approach, perform, cancel, and replace each action with clear feedback. Pickups, station work, punching, and UI panels follow consistent rules. Every active scenario passes in Editor Play Mode, without lost/duplicated items, unintended click-through, stale target actions, or effects after reset.

The user accepted the iteratively reviewed result on 2026-09-28 and explicitly closed this milestone. Dedicated first-time-user observation (T.18) was not performed and remains deferred outside milestone closure; acceptance does not claim that study occurred.

No skill advancement, survival balance, save data, or content volume is needed to pass bootstrap. Those systems build on the proven interaction foundation in later specs.





## Milestone acceptance: 2026-09-28

The user confirmed that the result looks good and the first spec can be completed following the ScriptableObject database work. 000-bootstrap is closed with its interaction-only scope delivered. Latest evidence is 30/30 Play Mode tests, successful Start and bootstrap pointer-input checks, and a clean current console. T.19 is complete through user acceptance. T.18 first-time-user observation and T.03 builds remain deferred; neither is reported as performed. Further game systems require a separate scoped milestone.
