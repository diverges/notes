# 003: Skill interactibles and attempt outcomes

Current model correction (2026-09-30): Spec022 separates player ActionDefinition capabilities from target action/item-use/fire responses. Flax now owns fixed output rows. Existing attempt and loot mechanics below remain historical evidence; current authoring is in the database guide.

Status: Implemented and verified in Editor Play Mode; human balance review pending
Updated: 2026-09-28

**Implemented successor correction (spec 004 complete):** spec 004 supersedes this implemented milestone’s pre-consumption output-space gate: evaluate capacity after planned ingredient consumption, including room in compatible stacks. It also requires explicit inventory Use for spinning. The historical rules and verification below describe spec 003 as implemented, not the later verification of spec 004. Read spec 004 and the current Play Mode guide for the implemented inventory and controls.

## Problem statement

Current presentation update: spec 008 replaces runtime loot spheres with each item's assigned models or explicit bag placeholder. Skill overflow retains this spec's reachable-nearby placement and payout behavior; only manual bag/equipment Drop moves directly beneath the player's feet.

TreeDefinition currently describes punch counts, paid checkpoints and one fixed item output. ProcessingDefinition independently reserves one input, processes it, and leaves output for a later Collect action. Neither expresses the shared skill-based attempts, variable loot and failure products requested for gathering and processing.

The next model must support Woodcutting first while accommodating Mining, Fishing, Farming harvests, Herbalism and Cooking through configuration. It must preserve approach-before-validation, interruptible work, the click-open action menu and in-range Examine.

## Proposal

Use **SkillInteractibleDefinition : InteractibleDefinition** for objects offering skill-based actions, and **SkillActionDefinition** for those actions. This replaces the proposed names Resource Definition, GatheringResourceDefinition and ResourceInteractibleDefinition. Processing stations belong to this same family; they are not a separate conceptual category.

Use **LootTableDefinition** for rewards. Keep SkillDefinition for identity, icon and XP thresholds, and ItemDefinition for inventory/world item identity. Equipment modifiers will belong to equipment, not the interactible; tools are out of scope now.

- In scope: shared attempt execution, minimum skill gates, fixed success chances, optional consumed inputs, success/failure loot tables, success-only XP, finite harvest capacity or unlimited use, depletion/renewal, cancellation, inventory delivery with ground overflow, and Inspector balance previews.
- Out of scope: tool modifiers, new playable skills/content, farming growth and watering, fuel simulation, combat redesign, persistence, multiplayer, a player-initiated Drop command and a general-purpose condition/effect scripting framework.
- Constraint: preserve existing asset identity and scene/prefab references through an explicit migration. Do not rename serialized classes and assume Unity assets will migrate automatically.
- Tradeoff: random failures change the current deterministic timing and pacing. The existing full progression run does not validate the new economy.

### Definition ownership

| Asset | Fields and responsibility |
| --- | --- |
| InteractibleDefinition | Existing identity, display name and description; common world interaction identity. |
| SkillInteractibleDefinition | Supported skill-action references; finite successful-harvest capacity or unlimited use; depletion and renewal settings. Presentation references/state transitions remain separate from skill arithmetic. |
| SkillActionDefinition | Stable identity, menu verb/progress label, skill requirement, base attempt duration, fixed success chance (%), optional input item/quantity entries, success/failure loot references, XP per success and repetition behavior. No per-level chance gain or separate chance cap. |
| LootTableDefinition | Guaranteed entries and independent-chance entries referencing ItemDefinition assets. |
| SkillDefinition | Existing skill identity, icon and cumulative level thresholds. |

Requirements belong to the chosen action so that future actions on one object can have different gates. Migrate existing tree gates without removing requirement behavior used by unrelated interactible types. Examine bypasses use requirements but still requires approach.

### Loot table: accepted sections

| Section | Fields per entry | Evaluation |
| --- | --- | --- |
| Guaranteed | Item Definition slot, minimum quantity, maximum quantity | Award each entry; equal bounds give a fixed quantity. |
| Independent chances | Item Definition slot, minimum quantity, maximum quantity, chance (%) | Roll each entry separately; multiple entries may succeed. |

An outcome evaluates its table once. Loot probabilities are separate from the fixed action-success roll. Skill level only gates access in this version; it changes neither attempt success nor rare-loot probabilities. Empty failure tables are valid. No weighted-choice section is required.

Proposed validation: item references must be registered and valid, quantities positive with minimum <= maximum, chances finite within 0–100%, and duration positive and finite. Aggregate repeated item identities before capacity checks. Roll an integer quantity inclusively between the bounds. Gathering success tables should contain a guaranteed reward so that a successful harvest cannot silently consume capacity without an item.

### Shared attempt flow

1. Hover shows the border; left click opens the pointer-positioned action menu. Choosing an action replaces the previous intention and approaches.
2. In range, check the action's minimum skill level. Preserve the required/current-level log message. Check availability, input requirements and inventory capacity before each attempt; a full inventory prevents starting even if consumption would later free a slot.
3. Reserve inputs for one attempt, without consuming them. Run the base attempt timer in unpaused simulation time.
4. At completion, roll against the action's fixed success chance once, then roll the corresponding success or failure loot table once.
5. Consume the reserved inputs, deliver as much rolled output as fits and spawn the remainder on the ground. Account for slots freed by consumed inputs. On success only, award XP and decrement finite successful-harvest capacity, including when rewards overflow. Failure may award failure products but gives no XP and does not consume successful-harvest capacity.
6. Continue according to the action's repetition setting while eligible, or deplete/stop as appropriate.

A tree has no ingredient inputs; a failed attempt costs time and normally produces nothing. A campfire action consumes berries on either completed outcome: success produces the user's normal-berries example, failure produces burnt berries and no XP. Exact Cooking items, skill values and playable content are examples, not a request to add them during this refactor.

Replace checkpoint state with remaining successful harvests. One success consumes one harvest even if its table awards multiple items. Cancellation discards the unfinished attempt and releases inputs; previously delivered rewards and used harvests remain. Pause preserves elapsed attempt time and reservations. A finite resource depletes only after its final successful harvest; renewal restores its capacity. A processor may allow unlimited attempts, constrained by ingredients instead.

The above-player UI counts down to **attempt completion**, not a guaranteed reward. Failure feedback should be brief and should not flood the persistent game log.

### Inventory delivery and ground overflow: accepted correction

The user replaced the pending-outcome proposal: block starting with a full bag, but always resolve an attempt that reaches completion and drop any rolled overflow on the ground. Apply this to success and failure outputs. There is no deferred reward, retry collection transaction or reroll on resumption.

Operational interpretation of full: before reserving/consuming inputs, require at least one empty slot or space for at least one possible output item in an existing compatible stack. All occupied slots need not mean full. Spare space in unrelated stacks does not count. Consider items with a nonzero chance of appearing in a reachable success/failure outcome, without rolling early. Do not require space for the complete potential bundle. An action with no possible output items needs no output-space check. This preserves stack-aware capacity while honoring the new pre-attempt gate.

After completion, aggregate quantities by item identity and allocate inventory in stable order: guaranteed entries first, then successful independent entries in authored order. Split amounts as needed between bag and ground. Input consumption may free slots at this point. If the bag fills during an already-running attempt, its completed outputs still go to the ground; before another attempt, run the full-bag check again and stop with a clear message if needed. No capacity-triggered reroll or duplicate reward is allowed.

Ground rewards use the same ItemDefinition assets and default Pick up interaction as other world items. Runtime pickups must track quantities, allow collection of the amount that fits while retaining the rest, and grant no additional skill XP. Spawn near the player on reachable ground outside blocking footprints. World quantity, positioning and dynamic registration/reset cleanup are implementation scope; the current one-item static pickup behavior is insufficient. Use a generic item representation when no specialized visual exists. No automatic despawn, save/load or player Drop command is introduced. Depletion/renewal must not recreate or delete previously dropped rewards; session reset removes session-spawned drops.

Input consumption, inventory allocation, ground quantities, XP and capacity must describe one completed attempt before notifications can reenter. Prepare and validate world-drop creation before irreversible changes; a technical spawn failure must not lose items or reroll the attempt. This engineering safeguard is distinct from retaining rewards because the inventory is full, which the user rejected.

### Confirmed decisions and authoring defaults

| Decision | Current direction | Status |
| --- | --- | --- |
| Success chance | One fixed authored percentage per action, independent of level; keep the minimum skill gate. | Confirmed. Initial migrated content defaults to 100% to preserve existing reliability until authored tuning; 100% is a conservative implementation default, not a user-selected balance value. Failure paths must still be tested. |
| Existing tree migration | Birch/Oak/Pine minimum levels 1/5/10, capacities 1/1/2, base attempt durations 6/8/10 seconds, renewal 30 seconds; flat XP per success 10/20/18. | Pine's change from 35 to 36 XP per tree is confirmed. Other values preserve the current baseline. |
| Reward delivery | Deliver to inventory immediately; overflow becomes world item pickups. No separate processor Collect step or pending-capacity outcome. | Confirmed replacement for prior delivery proposal. |
| Repetition | Gathering repeats; processing defaults to one attempt, with repetition configurable. | Retained implementation default: the user changed delivery but did not request automatic processor repetition. |
| Balance tooling | Inspect expected rates and level progression when selecting a skill interactible in the Editor. | Confirmed scope addition; details below. |

Multiple gathering actions sharing or independently consuming finite capacity still need a concrete design before authoring them on one depleted resource; initially author one harvesting action per tree. This is not a blocker for the first migration.

### Inspector balance preview

Inspecting a SkillInteractibleDefinition should show a read-only balance panel for each referenced action, using the actual skill thresholds, action values and loot entries. Controls select starting XP and target level. Display the cumulative XP curve and expected active time to each reachable level, marking the minimum-level gate. With fixed chance, gathering speed remains constant across eligible levels; only XP needed per level changes. Below the minimum, label the action unavailable rather than inventing progress from that action.

Show attempt duration, success/failure percentages, expected attempts/minute, successes/minute, XP/minute, expected item quantities/minute for each success/failure product, and ingredient consumption/minute. For finite resources also show expected attempts/time to depletion and XP/items per full resource. Show renewal time separately; optionally show a clearly labeled same-resource cycle including renewal. Default rate calculations exclude walking, menu interaction, inventory stops, pickup time and supply shortages. Processor preview assumes sufficient inputs and repeated manual or automatic attempts; disclose when the authored action does not auto-repeat.

For duration t seconds, success probability p, XP per success x and capacity H:
- Attempts/minute = 60 / t; successes/minute = 60p / t; XP/minute = 60px / t.
- Mean quantity per entry = (minimum + maximum) / 2, multiplied by its independent chance when applicable. Combine both outcome tables: item units/minute = (60 / t) × [p × E(success quantity) + (1 − p) × E(failure quantity)].
- Expected time to depletion = Ht / p; expected attempts = H / p; XP per depleted resource = Hx when p > 0.
- Expected active time to an XP target = ceil(remaining XP / x) × t / p for an eligible action with p > 0 and x > 0. Clamp credited XP to the skill cap. Finite-resource estimates assume access to replenished resources; show how many successful harvests/resource cycles are needed.

At p = 0, report no successful harvests/XP and unreachable depletion/XP targets without dividing by zero; failure-item and input rates still apply. Zero-XP actions cannot advance levels. Empty quantities and duplicate entries must use the same aggregation semantics as runtime loot. Label all stochastic times as expectations, not guarantees. The preview must not change assets, consume gameplay RNG, grant XP or require Play Mode. Test the calculations independently with known 0%, 50% and 100% examples; visually check the Inspector chart and labels.

## Use cases

### UC.1: Harvest through repeated skill attempts

- Actor: Player.
- Goal: Gather finite rewards using an eligible skill.
- Starting conditions: A standing tree with remaining successful harvests.
- Main flow: Choose action, approach, pass the minimum gate, attempt, resolve success/failure and repeat.
- Outcome: Successful rewards are delivered once; depletion tracks successes rather than elapsed time or punch checkpoints.

### UC.2: Process ingredients with success and failure products

- Actor: Player.
- Goal: Transform inputs using a skill-based action.
- Starting conditions: A processor and required ingredients.
- Main flow: Approach, validate, reserve, complete an attempt, consume inputs and deliver the selected outcome.
- Outcome: Either success or failure products, with XP only on success.

### UC.3: Author another skill interaction without copying behavior

- Actor: Content author.
- Goal: Configure an ore vein or fishing action using shared assets.
- Starting conditions: Existing skill/item definitions and a world representation.
- Main flow: Configure action, loot and interactible assets; register and place the object.
- Outcome: The same attempt engine handles the new content without tree-specific timing or reward code.

## Requirements

- **R.1.1:** Skill interactibles shall use shared action execution for gathering and processing; item and skill identity shall remain asset references.
- **R.1.2:** Minimum skill requirements shall be checked in range before attempts, with the existing required/current-level message. Examine shall still approach and shall bypass use prerequisites.
- **R.1.3:** Each completed attempt shall resolve success using the configured fixed chance, independent of level and evaluate exactly one corresponding loot table.
- **R.1.4:** Loot tables shall expose Guaranteed and Independent chances sections with ItemDefinition slots and quantity bounds.
- **R.1.5:** Failure shall award no XP. Configured inputs shall be consumed for either completed outcome; cancellation before completion shall consume none.
- **R.1.6:** Finite capacity shall count successful harvests, not item units, attempts or paid punch checkpoints.
- **R.1.7:** World movement/action replacement shall interrupt unfinished work; pause shall preserve it. Completed rewards shall never duplicate on resume.
- **R.1.8:** Definitions shall remain immutable during gameplay; instance/session state shall own remaining harvests, elapsed time, reservations, depletion and spawned item quantities.
- **R.1.9:** The system shall block starting attempts when the bag is full, deliver completed outcomes to inventory where possible and spawn every remaining item quantity on reachable ground without rerolling or granting duplicate XP.

- **R.1.10:** The skill-interactible Inspector shall preview expected action rates, loot/input rates and progression against the referenced skill curve without mutating gameplay state.

## Scenarios

### UC.1: Gathering

#### S.1.1: Gate and success
- Given: An under-level player, then an eligible player at a two-harvest tree.
- When: Each selects its action and arrives; the eligible player's attempt is forced to succeed in a test.
- Then: The first sees the level message without an attempt; the second receives its rolled rewards/XP once and leaves one harvest.

#### S.1.2: Failure and interruption
- Given: A two-harvest tree, one prior success and an empty failure table.
- When: The next attempt fails, then a subsequent unfinished attempt is interrupted.
- Then: No extra items/XP or capacity loss occurs; resuming starts a full attempt with one harvest remaining.

### UC.2: Processing

#### S.2.1: Failure consumes inputs
- Given: A cooking action with berries reserved and a burnt-berries failure table.
- When: A completed attempt fails and the output fits.
- Then: Consume the ingredients, deliver burnt berries and award no XP. Cancelling before completion instead releases the ingredients.

#### S.2.2: Rolled overflow becomes world loot
- Given: An attempt started with available capacity, but its rolled output bundle cannot fully fit, including a bonus item.
- When: The attempt completes.
- Then: Consume inputs, deliver fitting quantities, create pickups for overflow, award success XP/capacity changes once, and gate any next attempt on current capacity. Later pickup adds items without extra XP.

#### S.2.3: Full before starting
- Given: No empty bag slot and no compatible space for any possible output, even if consuming an ingredient could free a slot.
- When: The character reaches the interactible or would start another attempt.
- Then: Log that the bag is full, do not start or roll, and reserve/consume no inputs.

### UC.3: Authoring

#### S.3.1: Shared behavior and migration
- Given: Migrated trees/processor and a temporary ore-vein fixture with different skill/items.
- When: All run through the same action engine with controlled RNG.
- Then: Gates, successes/failures, atomic rewards, cancellation and independent instance depletion hold without specialized harvesting code or mutated definition assets.

#### S.3.2: Expected-rate preview
- Given: A two-harvest action with a ten-second attempt, 50% success and 18 XP per success.
- When: Its interactible is inspected.
- Then: Show six attempts/minute, three successes/minute, 54 XP/minute, 40 expected active seconds to depletion and 36 XP per resource. Changing skill level above its gate does not change success chance. At 0%, successful depletion and XP targets are unreachable; at 100%, depletion takes 20 active seconds.

## Task list

- [x] Record accepted terminology and model; distinguish proposals from implemented behavior.
- [x] Record fixed chance, accepted Pine XP and immediate delivery with overflow; state conservative chance/repetition defaults.
- [x] Implement definitions, validation and a controllable RNG boundary for deterministic tests.
- [x] Extend multi-input reservations and outcome commit; add quantity-aware world pickups, safe placement, dynamic registration and reset cleanup. Verify pre-attempt full-bag rejection and overflow after input consumption.
- [x] Implement shared instance state and attempt execution; preserve approach/menu/Examine/pause/interruption behavior.
- [x] Migrate existing assets, inspector authoring, catalog references, prefabs and scenes with identity checks and recoverable backups; retire obsolete behavior only after migration validates.
- [x] Test gathering and processing success/failure, mixed loot, 0%/100% bonus rolls, cancellation, unavailable targets, cap XP, ground overflow/partial pickup/reset and temporary new-skill content.
- [x] Add Inspector balance preview, pure expected-value calculations, boundary tests and visual verification.
- [x] Run existing regressions and normal-speed interaction checks; assess new pacing separately, update authoring guides and record migration evidence.

## Readiness and verification

The implementation now uses the shared skill/action/loot assets and the conservative defaults above. TreeDefinition, TreeRuntimeState and ProcessingDefinition have been retired from runtime code. The original planning session changed documents only; the implementation evidence below supersedes that readiness status.


Documentation verification: reconciled the superseded chance-scaling and pending-outcome proposals, checked the 50% preview example (6 attempts, 3 successes and 54 XP per minute; 40 seconds to two successes), and updated the specification index. No gameplay implementation, Unity tests or builds were performed for these design updates.


## Implementation evidence: 2026-09-28

Migrated Birch, Oak, Pine and Spinning wheel in place, preserving their asset GUIDs and all prefab/scene references. Added four SkillActionDefinition assets and four LootTableDefinition assets. The existing catalog remains one skill, one player and thirteen interactibles including seven items; there are twenty-four database assets total. Actions/loot are reachable through interactible references and validated transitively. Spinning remains ungated and gives no XP, preserving its bootstrap role without introducing an unimplemented crafting skill; it now delivers string directly and runs once per selection.

The first full test run passed 46/47, with one obsolete missing-input message assertion. After updating that assertion, retiring legacy code, adding a temporary Mining/multiple-action scenario, and strengthening multi-ingredient rollback and failure-XP coverage, all **49/49 Play Mode tests passed in 60.59 seconds**. Coverage includes fixed chance independent of level, success/failure products, input reservations, mixed inventory/ground rewards, reentrant callbacks, partial pickup, reset cleanup, per-instance renewal, cancellation, pause, full-bag preflight, quantity bounds, 0%/100% bonus rolls, expected-rate math, skill UI and real navigation to all eighteen trees.

Saved catalog/item validation passed, all forty saved scene targets and nine prefabs validate, and no missing scripts were found. The migration initially encountered stale loaded references after replacing asset script types; importing the catalog and reloading scripts resolved them. Source data backups are in Temp/skill-interactible-migration-20260928-193527; retired definition sources are in Temp/skill-interactible-legacy-source. These are local data snapshots, not a standalone full-project rollback. Do not rerun the historical migration/authoring tools against migrated assets.

The normal-speed overflow pointer smoke passed: Birch completed with a bag filled during work, spawned its log, then the player picked it up through the two-click menu without extra XP; reset removed dynamic drops. Bag/focus fixtures were used for that capacity case. Woodcutting and native Skills pointer/tooltip smoke also passed. Actual custom Inspector rendering and the ground-drop screenshot were visually inspected; label spacing was corrected and the Inspector recaptured. The final editor-only layout correction compiled successfully.

At authored 100% chance, the calculated unlock-switching route is now 28 Birch, 40 Oak and 37 Pine trees. Cap occurs on Pine success 73, after 1,218 active work seconds; finishing its final log takes 1,228 seconds and gives 142 total logs. This is a calculation, not a new full-route or human pacing test. No player builds were run. Equipment modifiers, new playable skills and farming growth remain deferred. Ground rewards currently use a generic sphere representation with item name/quantity in the menu.

![Skill interactible Inspector balance preview](D:/Repos/FirstLight/Docs/Images/skill-inspector-balance.png)

![Overflow reward on reachable ground](D:/Repos/FirstLight/Docs/Images/skill-overflow.png)


The broader normal-speed Bootstrap pointer check also passed: pickups, Examine approach, direct spinning output, enemy punches, Escape, camera controls, HUD isolation and reset. The old full-route and checkpoint smoke tools are retained as historical sources only; supported current tools are listed in Tools/README.md.


Final Editor state: stopped on Start. Console review found only the Pipeline warning from requesting stop while already stopped; after clearing that diagnostic, the console had zero errors/warnings and no compilation failure.

### 2026-09-28: Live Bag and Skills inspection

Bag and Skills no longer pause the simulation. Movement, attempts, rewards, XP and renewal continue; open panels refresh their values live. Settings and application focus loss still pause without catch-up. Panel input remains isolated from world commands, and opening a panel dismisses an existing action menu. Uncovered world areas remain interactive. Removed the Paused label from Bag/Skills and updated Settings help. The final Play Mode suite passed 51/51 in 63.35 seconds, including live rewards/UI, station approach/completion, renewal, Settings transitions and retained focus pause checks.

### 2026-09-28: Planned inventory Use correction

Spec 004 now requires selecting flax with inventory Use before the spinning wheel offers Process Flax. It also introduces the six-slot starting bag, inventory Inspect/Drop and reordering. This follow-up is implemented and user-accepted; its verification is recorded in spec 004 separately from the historical results above. Existing attempt timing, one-attempt spinning, input reservation and completed-reward overflow remain the baseline; direct wheel processing from carried flax is superseded by explicit Use in the current game.

### 2026-09-28: Inventory review decisions

The user confirmed that spec 004 should allow processing with all slots occupied when planned input consumption frees output room or a compatible output stack can accept the result. Preflight simulates consumption without mutating the bag or rolling loot; completed overflow still goes to ground. Inputs use the selected stack first, then other matching stacks. Spinning remains one flax per explicit selection to support slow progression, with no automatic repetition. Inventory Drop transfers the whole stack. These are accepted design decisions for the draft successor, not newly implemented or tested behavior.

