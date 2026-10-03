# 002: Woodcutting and skill experience

Status: Implemented and verified in Editor Play Mode; human feedback pending
Updated: 2026-09-28

Implemented successor: [003: Skill interactibles](003-skill-interactibles.md) replaces tree checkpoints with shared skill attempts and loot tables. Spec 003 is now implemented and supersedes this milestone's checkpoint, processing and no-overflow rules. This document preserves the original design and verification history.

## Problem statement

The interaction bootstrap supports repeated punches, a bag, and a scrollable game log, but Skills is a display-only list. We need one playable skill that proves earning experience, gaining levels, unlocking interactions, and explaining a failed level requirement. Woodcutting supplies that first complete progression loop.

**Confirmed by the user:** begin with Woodcutting, approximately 15 levels, three tree types with increasing level requirements, punching trees for now, an XP system, and a game-log message when an interaction requires a higher level. Produce a spec and programmer research before seeking implementation approval.

**Confirmed follow-up:** the user accepted the quadratic XP curve, distinct log types, per-log XP with Pine's 17/18 split, and the compact Skills mock with its hover tooltip. Without space for the next output, the interaction stops; no ground overflow is included. Pine takes 20 active seconds and yields one log every 10 seconds. The user approved proceeding with this implementation package on 2026-09-28, including the content defaults, renewal, stacking and technical route. Runtime and pacing validation remain separate.

## Proposal

Click a tree to approach and repeatedly punch it. Each yield checkpoint grants a tree-specific log and its share of Woodcutting XP. The final checkpoint also fells the tree and leaves a temporary stump. Pine gives one log at 10 seconds and a second at 20 seconds, when it falls. Sufficient inventory capacity is required. Level up quickly at first, unlock tougher trees at levels 5 and 10, and finish this first curve at level 15. Tree tiers provide different materials and yields; the longer Pine currently gives less XP per second than Oak. Woodcutting alone advances; punching trees does not award Strength or Weapon Mastery XP.

- In scope: reusable skill identity, cumulative XP and level lookup, reusable interaction level requirements, one active skill, three tree definitions and log items, tree renewal, Skills feedback, existing-log messages, authoring validation, and Play Mode verification.
- Out of scope: ground overflow drops, axes or equipment, random success/drop rolls, combat changes, other skill progression, recipes or uses for the logs, survival costs, passive XP, milestone quests, offline progress, save/load, player builds, and new UI frameworks.
- Constraints: retain current point-and-click controls, primitive reusable prefabs, limited bag, pause and cancellation behavior, asset-based content identity, and independent runtime state. Keep unrelated bootstrap fixtures operational.
- Tradeoffs: logs stack to 99 in this test so the full recommended progression route fits before disposal/crafting exists. Renewable trees make the entire curve playable without resetting XP. There are only two material unlocks; intermediate levels show progress, not fifteen distinct benefits. This validates the XP foundation rather than a final deep skill.
- Approved implementation package: the asset/runtime architecture below, level gates and tree defaults, stack limit 99, 30-second renewal, session-only progression and Editor Play Mode validation. Retain accepted Pine timing/XP and UI; make no automatic balance adjustment. The 25–35 minute estimate is a playtest hypothesis, not a release guarantee.

### Tree content and interaction

| Tree | Required Woodcutting | Completed punches | Seconds per punch | Active seconds per tree | Total tree XP | Output | XP per active minute |
| --- | ---: | ---: | ---: | ---: | ---: | --- | ---: |
| Birch | 1 | 3 | 2 | 6 | 10 | 1 Birch log | 100 |
| Oak | 5 | 4 | 2 | 8 | 20 | 1 Oak log | 150 |
| Pine | 10 | 10 | 2 | 20 | 35 | 2 Pine logs, one at 10s and one at 20s | 105 |

Birch yields after punch 3, Oak after punch 4, and Pine after punches 5 and 10. Times are cumulative active work after arrival, excluding movement, pause and interruption. Each checkpoint grants one log and its XP share. Pine continues automatically after its first payout if another log fits. No extra log or XP batch is awarded for felling. Higher level alone does not change speed, success chance, yield or XP. Tree work remains separate from combat damage; the two-second interval is authored on the tree definition.

**Confirmed:** Oak and Pine award distinct log types, and multi-log trees split their XP across individual logs. Birch log, Oak log and Pine log are separate ItemDefinition assets with stable IDs `birch-log`, `oak-log` and `pine-log`, each with stack limit 99. Their names and stacks remain distinct; space in an Oak log stack cannot receive a Pine log.

**Database correction before skills:** ItemDefinition is also an InteractibleDefinition. Each log is one record usable both in inventory and by a world target with default Pick up; no paired pickup definition is required. Register it once in the Interactibles catalog; Items is a filtered view and shares the interactible ID namespace. A future inventory Drop command can create a world instance of that same item. Drop UI/spawning and full-bag ground overflow remain outside this milestone.

| Payout | Item | XP before cap |
| --- | --- | ---: |
| Birch at 6 seconds | 1 Birch log | 10 |
| Oak at 8 seconds | 1 Oak log | 20 |
| Pine at 10 seconds | 1 Pine log | 17 |
| Pine at 20 seconds | 1 Pine log | 18 |

**Approved integer split:** preserve the authored total XP and divide it as evenly as possible across logs. For total XP E and N logs, each gets `floor(E/N)` and the final `E mod N` logs get one extra XP. Pine gives the accepted 17 + 18 = 35, not 35 per log. Compute shares from the original total and checkpoint index, then clamp each credited award to remaining cap capacity. Do not redistribute XP lost to the cap. Validate that total XP is at least the log count so every uncapped payout grants positive XP.

Primary action: **Punch**. Secondary action: **Examine**. Examine includes the tree's required Woodcutting level and log output. Locked trees remain selectable and explain the gate; they do not silently disappear from action menus. World feedback uses the player-anchored progress panel above the character, labeled **Woodcutting**, with seconds remaining and a draining bar until the next log payout. Primitive variants need distinguishable silhouette/size and visible names; color alone is insufficient.

An accepted click performs one tree's repeated punches automatically. The first punch completes after a full interval, not immediately on click. Felling stops the action; the player selects the next tree. There is no automatic retargeting or automatic restart after respawn. During timed work or approach, clicking navigable ground or another interactible cancels the current action and starts the new intention. Escape cancels without a replacement. Clicking the same active target preserves progress. Interrupted tree work returns to its last paid checkpoint; the new target checks prerequisites upon arrival.

Timing uses unpaused simulation time from the start of in-range work. Carry surplus time across tree punch intervals rather than discarding it. If an update crosses several intervals, process them in order and revalidate each punch/payout; stop immediately if cancelled, blocked or depleted. A payout occurs on the first simulation update reaching its checkpoint, never early. Cancellation resets unpaid work and the partial interval to the last paid checkpoint; pause preserves both. This tree timing contract does not change enemy combat cadence. Renewal similarly occurs on the first update reaching 30 seconds since actual depletion, without counting pre-depletion time from that update.

Paid yield checkpoints persist on that individual tree until it is felled or the test resets. Completed unpaid punches are provisional and roll back to the last paid checkpoint on cancellation or a blocked action. Resuming after a payout never awards that same log again. Already delivered logs and their credited XP are retained if later work is cancelled or blocked. Cancelled unpaid work grants nothing; a later selection starts a full countdown from the last paid checkpoint, or from zero work if no log was paid. Pause freezes the current interval and tree renewal timers. Trees renew 30 seconds of unpaused session time after successful felling, with full work remaining and their original position. A stump is examinable but cannot be punched for rewards. Respawning never teleports or traps the player; retain a blocking footprint while depleted and keep its renewal geometry unchanged.

Place six reachable instances of each tree type in nearby distinguishable groups. Six Birch trees provide at least 30 seconds of other chopping between visits to a renewing tree, before travel. Preserve ground-pickup, wheel, and enemy fixtures; do not indiscriminately convert every decorative tree or obstruct existing routes.

### Leveling curve

Store total cumulative XP. Start at level 1 with 0 XP. The accepted starting curve is `T(L) = 10 × (L − 1) × (L + 2)` for levels 1–15. The cost from level L to L+1 is `20 × L + 20`. Runtime uses the authored threshold table, so future tuning need not change a formula in gameplay code.

| Level | Total XP required | XP to next level | New access |
| --- | ---: | ---: | --- |
| 1 | 0 | 40 | Birch |
| 2 | 40 | 60 | — |
| 3 | 100 | 80 | — |
| 4 | 180 | 100 | — |
| 5 | 280 | 120 | Oak |
| 6 | 400 | 140 | — |
| 7 | 540 | 160 | — |
| 8 | 700 | 180 | — |
| 9 | 880 | 200 | — |
| 10 | 1,080 | 220 | Pine |
| 11 | 1,300 | 240 | — |
| 12 | 1,540 | 260 | — |
| 13 | 1,800 | 280 | — |
| 14 | 2,080 | 300 | — |
| 15 | 2,380 | Capped | First-version cap |

Level is the highest threshold reached, including exact equality. One award can cross multiple levels; excess carries forward. Clamp stored XP to 2,380 for this version. The last award may be partially credited, and the log reports the actual credited amount. At cap, trees still give logs but no XP; use the compact Skills contract below: **Lv. 15**, full bar and **Max level reached** in the tooltip. Raising a cap later is an authoring change, but save migration remains outside this milestone.

**Calculated pacing, not playtest evidence:** four Birch trees reach level 2 in 24 seconds of punching. Switching at unlocks takes 28 Birch trees to level 5 (168 seconds), 40 Oak trees to level 10 (320 seconds), and 38 Pine trees to level 15 (760 seconds). Total: 106 trees, 144 logs (28 Birch, 40 Oak, 76 Pine), and 1,248 active seconds, or 20 minutes 48 seconds. The cap is reached on the first log of the 38th Pine: XP is 2,375 after 37 Pine trees, so its first 17-XP share credits only 5 XP. This reaches cap with 143 logs at 1,238 active seconds (20m38s). Completing that Pine yields the 144th log at 1,248 seconds with 0 further XP. The uncapped full-route total would be 2,410. Allow roughly 25–35 minutes including travel, reading and selecting; this is an untested estimate. Each stack still fits below 99. With Pine XP unchanged, remaining on Oak after level 5 is faster for XP: 28 Birch plus 105 Oak reaches the cap in 1,008 active seconds (16 minutes 48 seconds), with Oak logs occupying two stacks. The earlier claim that each unlock improves XP rate is superseded. Whether Pine should also become the fastest XP source requires a separate balance decision. Birch-only remains possible in 238 trees / 23 minutes 48 seconds of active work.

This gentle increasing-cost curve makes the first feedback quick without exponential grind. Pine offers a distinct material and two logs per selection, not a higher output rate: it gives 6 logs/minute versus Oak's 7.5, as well as less XP/minute. With log uses deferred, the level-10 unlock validates material access and multi-payout behavior rather than a superior economic reward. Retain this prototype limitation explicitly; a future use/value or balance pass must justify the longer harvest. Level 15 marks completion of the prototype and promises no fourth tree or unimplemented perk.

### Rewards, failure, and feedback

Selection starts the approach without checking skill or inventory prerequisites. Once in range, check target availability, skill requirement, then capacity for one log; repeat checks before each punch interval and at each yield checkpoint. Missing targets and unreachable paths still stop navigation safely. Capacity includes room in a matching stack, even when all slots are occupied; do not require space for all remaining tree logs up front. If a payout fills the bag and no space remains for the next log, stop immediately after the successful payout. If capacity becomes unavailable during an interval, stop at the next validation before committing further work.

A yield checkpoint atomically commits its work, one typed log, its bounded XP share and its paid-checkpoint state. Only the final checkpoint also commits depletion and renewal. No extra XP is awarded for felling, ordinary punches, clicks, failed attempts, cancellation, Examine or respawn. Previously delivered logs and credited XP remain earned after interruption; resuming cannot duplicate either reward.

If the bag cannot receive the next output, stop and log **Your bag is full. Make room for 1 Pine log.** Use the actual item name. The blocked checkpoint grants no new item or XP, the tree does not fall, and its payout punch is not consumed. Paid checkpoints, delivered logs and credited XP remain. After making room, a new click resumes at the last paid checkpoint with a full reward countdown; unpaid punches and partial time are discarded. No ground drop or automatic retry occurs. Disposal/crafting remains outside this spec; the default route fits the starting bag and stress tests may use fixtures to free space.
The existing lower-left scrollable log remains the single result/error history, with its 100-entry limit and current scrolling/input isolation. Emit messages for player attempts and results, not every update or punch. UI hover and availability probes never append messages.

| Event | Example log text |
| --- | --- |
| Insufficient level | You need Woodcutting level 5 to punch Oak. Your level is 3. |
| First Pine payout | You receive 1 Pine log. +17 Woodcutting XP. |
| Final Pine payout | You receive 1 Pine log. +18 Woodcutting XP. |
| Fell a tree | You receive 1 Oak log. +20 Woodcutting XP. |
| Insufficient inventory space | Your bag is full. Make room for 1 Oak log. |
| Level up | Woodcutting reached level 5. You can now chop Oak. |
| Level without unlock | Woodcutting reached level 6. |
| Multiple levels in one award | Woodcutting advanced from level 1 to level 5. You can now chop Oak. |
| Reach cap | Woodcutting reached level 15. Max level for this version. |
| Fell at cap | You receive 1 Pine log. Woodcutting is at max level. |

At every successful payout, emit the item/credited-XP result first, then a single level-change message listing crossed unlocks. Intermediate payouts can level up immediately while the tree still stands. Each separately accepted failed click can produce one error; there is no repeated automatic retry or per-frame spam. If an interaction is locked by existing timed-work controls, ignored clicks do not generate misleading new attempt messages.

### Skills panel

![Skills panel concept with skill icons, names, level, unlabeled bars and XP hover tooltip](D:/Repos/FirstLight/Docs/Images/skills-panel-mock-v2.png)

The user accepted this compact v2 mock on 2026-09-28. It is the accepted layout target; actual runtime captures and checks appear in the verification record below. The enlarged panel and illustrative background are presentation context, not a requirement to replace the existing world or panel placement. Runtime readability, tooltip placement and input behavior have automated and visual checks; human usability feedback remains open.

**Confirmed compact row layout:** a small skill icon on the left, the skill name beside it and the current level aligned at the right on the same text baseline. A thin XP strip follows the bottom edge beneath the text, spanning from the start of the skill name to the right edge of the level, excluding the icon column. There is no separate progress-bar column. Keep row height and padding compact, with aligned icon/name/level positions. The strip has no XP numbers, fractions or percentage label; the numeric level remains above it. The v2 mock supersedes the wider v1 row layout; v1 is retained as history.

Woodcutting is first and is the only active progression row. Cooking, Smithing, Weapon Mastery, Strength, Vitality and Agility retain their relative order. Implemented deferred state: show their icon/name, **Not implemented**, and an inactive empty bar, with no fabricated level or XP. This does not activate those skills. Use simple replaceable skill icons, with a neutral fallback when an icon reference is missing; names keep the rows understandable without relying on artwork.

**Hover tooltip:** hovering the row, including its bar, reveals **Current XP: X** and **XP to next level: Y**. Current XP means total cumulative XP; remaining XP is the next cumulative threshold minus that total. Example at 300 XP: `Current XP: 300` and `XP to next level: 100`. The displayed level is 5 and the unlabeled bar fills to `(300 − 280) / (400 − 280)`, approximately 16.7%. This tooltip is the only numeric XP readout within the Skills panel; ordinary reward messages still show XP in the game log.

The bar represents progress within the current level: `(total XP − current threshold) / (next threshold − current threshold)`, clamped to 0–1. It is empty at a newly reached level threshold. At level 15 it is full, the level reads **Lv. 15**, and the tooltip says **Current XP: 2,380** and **Max level reached**; do not show a next-level denominator or claim more XP is needed. Deferred rows show **Not implemented** in the tooltip instead of invented XP values.

The tooltip stays within the panel/viewport, does not intercept clicks or trigger world input, and disappears on pointer exit, panel close/switch, reset or loss of focus. Refresh row and tooltip data from the same skill state when XP changes or the panel opens; opening Skills retains existing pause behavior. Displaying or hovering skill information must not change XP or emit log messages. No permanent XP totals, progress percentages, or tree-unlock list are added to these compact rows; tree requirements remain available through world Examine and unlock messages. Unknown recipes stay hidden. Numeric levels apply to this milestone; the GDD's five-rank/milestone proposal does not gate Woodcutting.

Progress lasts for the current Bootstrap session. Reset test restores level 1 / 0 XP, all trees, bag and timers together. Stopping Play Mode or starting a new Bootstrap session loses progression. This restriction must be visible in the testing guide and not described as persistent progression.

## Use cases

### UC.1: Learn and advance Woodcutting

- Actor: Player.
- Goal: Earn XP through a visible world action and use a higher-level tree.
- Starting conditions: Fresh run, Woodcutting level 1, reachable Birch and output capacity.
- Main flow: Select Birch, approach, punch repeatedly, receive a log and XP, inspect Skills, repeat to unlock Oak and Pine.
- Outcome: The player can explain how to advance, what the next tree requires, and what changed at an unlock.

### UC.2: Understand a blocked or interrupted interaction

- Actor: Player.
- Goal: Understand why an action failed and recover without losing an earned reward or gaining unearned XP.
- Starting conditions: A locked tree, full bag, partially punched tree, or unavailable target.
- Main flow: Attempt, read the specific message, satisfy the requirement or resume an interrupted action.
- Outcome: State stays consistent and the player knows the next step.

### UC.3: Author and reset progression safely

- Actor: Designer/tester.
- Goal: Tune skills and tree variants through assets, and reproduce a fresh progression run.
- Starting conditions: Valid catalog, tree definitions, and scene instances.
- Main flow: Edit thresholds/rewards, validate, start a run, operate independent trees, reset.
- Outcome: Authoring changes take effect without content enums or mutated source assets.

## Requirements

The following mandatory statements define the approved implementation contract.

### 1: Experience and level access

- **R.1.1:** The skill system shall store nonnegative integer cumulative XP per skill and derive levels from strictly increasing cumulative thresholds starting at 0.
- **R.1.2:** Woodcutting shall start at level 1, use the 15 thresholds above, carry XP across levels, handle multi-level awards, and clamp at the cap.
- **R.1.3:** Each successfully committed log payout shall award its bounded share of tree XP; felling shall grant no extra XP. All other skills shall remain unprogressed.
- **R.1.4:** An unmet skill requirement shall reject the action only after reaching interaction range and on subsequent revalidation, with required skill, required level, and current level in the existing log and no work/reward mutation.

### 2: Trees and completion

- **R.2.1:** Three tree types shall implement the content table using one reusable tree behavior and separately authored definitions and outputs.
- **R.2.2:** Every completed interval shall consume one punch of tree work; partial intervals shall follow cancellation/pause rules above, independently of combat damage.
- **R.2.3:** Each yield checkpoint shall atomically commit one typed inventory log, its bounded XP share and its paid state exactly once. Only the final checkpoint shall also commit depletion. Uncapped shares shall sum to total tree XP. Insufficient space shall stop interaction without consuming blocked work or granting its reward; work through paid checkpoints, logs and XP shall remain; unpaid work shall roll back. No ground drop shall be created.
- **R.2.4:** Each tree shall own its remaining work and renewal timer; all trees shall renew after 30 unpaused seconds and award nothing on renewal.
- **R.2.5:** Repeated clicks, callbacks, stale targets, and interruption shall not duplicate completion. Felling shall stop the active action without selecting another tree.
- **R.2.6:** The scene shall provide six reachable trees of each type without blocking existing fixtures; the starting bag shall support the full recommended leveling route without reset or disposal.
- **R.2.8:** Birch, Oak and Pine shall deliver their own distinct item definitions; capacity and stacking shall use item identity, never a generic log name.
- **R.2.9:** Tree timing shall preserve interval surplus, process crossed punches/checkpoints in order with revalidation, and stop on interruption/depletion. Frame cadence shall not change work or reward totals for equal active time under equal conditions.

### 3: Presentation and lifecycle

- **R.3.1:** Each active Skills row shall show an icon at left, name beside it, level aligned right and an unlabeled within-level XP strip along the bottom edge from the name to the level, excluding the icon. Hovering shall show cumulative current XP and XP remaining to the next level. At cap the bar shall be full and the tooltip shall show total XP and max-level status. Deferred skills shall be clearly unimplemented rather than displaying fabricated progression.
- **R.3.2:** The game log shall retain existing behavior and append one result plus at most one level-change message per paid checkpoint, with no per-punch or passive validation spam.
- **R.3.3:** Locked trees shall remain examinable and identifiable; the action panel shall appear above the player and count down to the next reward, restarting for each intermediate payout, without adding a permanent HUD meter.
- **R.3.4:** Reset shall restore XP, tree state, timers and bag together; pause shall freeze actions and renewal. No persistence shall be claimed.
- **R.3.5:** Skill tooltips shall remain onscreen, dismiss when their row/panel is no longer hovered or active, preserve UI/world input isolation, and show current state after updates without gameplay mutation or log spam.

### 4: Authoring and regression safety

- **R.4.1:** Skill and tree identities shall be referenced assets with stable catalog IDs; supported new skills/tree variants shall not require new content enum members.
- **R.4.2:** Runtime XP and tree state shall never modify shared ScriptableObject definitions.
- **R.4.3:** Authoring validation shall reject missing/unregistered references, duplicate skill IDs, malformed thresholds, requirements outside a skill's range, nonpositive rewards/work/timing/renewal, invalid item capacity, and empty, duplicate, unordered or out-of-range yield checkpoints. The final checkpoint shall equal the total punch count.
- **R.4.4:** Existing pickups, processing, enemy punching, action cancellation, UI isolation, pause/reset and Start-to-Bootstrap entry shall remain operational. Verification shall use Editor Play Mode; builds remain deferred.
- **R.4.5:** Total tree XP shall be at least its payout count; integer shares shall be positive, differ by at most one, and sum exactly to the total before cap clamping. Runtime shall derive each share from the authored total and checkpoint index, without a second editable XP schedule.
- **R.4.6:** Before enabling gameplay input, session initialization shall validate the catalog, player/starting-item references, participating targets and every tree's requirement/reward/output references. Every participating tree shall be registered exactly once for ticking/reset. Invalid setup shall block initialization with an actionable error, not leave a partially playable session.

## Scenarios

### UC.1: Learn and advance Woodcutting

#### S.1.1: First tree

- Given: Level 1, 0 XP, a fresh Birch and space for its log.
- When: The player selects it and completes three two-second punches.
- Then: The tree becomes a stump, exactly one Birch log and 10 XP are received, and the action stops with one result message.

#### S.1.2: Exact threshold and unlock

- Given: Total XP 270 and a Birch ready for its final punch.
- When: The final punch successfully awards 10 XP.
- Then: Total XP becomes 280, level becomes 5, Oak is usable on the next selection, and the log announces the unlock once.

#### S.1.3: Threshold lookup and multiple levels

- Given: Test XP one below, exactly at, and one above each threshold, and a separate player at 0 XP.
- When: Levels are resolved and a single test award of 300 XP is applied to the separate player.
- Then: All boundary levels are correct; that player reaches level 5 at 300 total XP, with 20/120 within-level progress and one level-change notification.

#### S.1.4: Cap and excess

- Given: Level 14 with 2,375 XP and a fresh Pine with capacity for both logs.
- When: Its first log is awarded at 10 seconds and its second at 20 seconds.
- Then: The first credits 5 XP and reaches level 15 while Pine still stands; the second grants its log with 0 XP and fells Pine. The UI has no next-level denominator.

#### S.1.5: Complete normal-speed route

- Given: Fresh default starting supplies and six reachable trees per type.
- When: The tester fells 28 Birch, 40 Oak and 38 Pine, switching at unlocks without reset.
- Then: Level 15 and 2,380 XP are reached with 144 logs and no capacity dead end; observed travel/renewal time and clarity are recorded against the 25–35 minute hypothesis.

#### S.1.6: Pine yields before felling

- Given: A fresh Pine, sufficient level and capacity for two logs.
- When: Uninterrupted in-range punching reaches 9.9, 10 and 20 active seconds.
- Then: No log exists at 9.9 seconds; one Pine log and 17 XP are delivered at 10 seconds while Pine stands; at 20 seconds a second Pine log and 18 XP are delivered, Pine falls, no extra felling XP is awarded and the action stops.

#### S.1.7: Resume without duplicate intermediate yield

- Given: Pine has paid its first log after five punches, then the player cancels or is stopped because only one log fitted in the bag.
- When: The player frees space and selects Pine again; pause and repeated completion calls are also exercised.
- Then: Only five further two-second punches remain, the first log and its 17 XP are never reissued or removed, pause advances no work, and the final payout adds exactly one more Pine log and its bounded 18 XP share.

#### S.1.8: Distinct log types and stack capacity

- Given: Oak and Pine are usable, an Oak log stack has space, every bag slot is occupied and no Pine log stack has space.
- When: Oak pays a log and the player then attempts Pine.
- Then: Oak adds only an Oak log and its 20 XP. Pine is blocked by capacity; it neither adds to the Oak stack nor grants XP. With a free slot, Pine creates its own named stack and grants only its scheduled share.

#### S.1.9: Intermediate level-up and exact XP shares

- Given: Level 10 with 1,290 XP and a fresh Pine; separately authored totals of 35/2, 10/3 and 12/3 XP/logs.
- When: Pine delivers its first log and the share calculation is evaluated.
- Then: Pine credits 17 XP, reaches level 11 at 1,307 XP while standing, and reports the level immediately. The schedules are [17,18], [3,3,4] and [4,4,4], each summing to its total. Replaying a paid checkpoint changes neither inventory nor XP.

### UC.2: Understand a blocked or interrupted interaction

#### S.2.1: Under-level attempt and arrival revalidation

- Given: Level 3 and a selected Oak; separately, a permitted approach whose test fixture changes the level before arrival.
- When: The player reaches interaction range and fails validation.
- Then: The character first approaches without a requirement message, then one level-requirement message appears for that attempt, no work begins, and no item, XP, or tree work changes.

#### S.2.2: Output capacity failure

- Given: Sufficient skill but no output capacity, before selection, after the first Pine payout, and separately just before either payout punch.
- When: Validation checks capacity.
- Then: The action stops with the specific bag message, the tree remains standing, no further item or XP is awarded, no ground drop appears, and the blocked payout punch remains available; earlier logs stay in inventory and their XP remains credited. After capacity is freed, a new selection can complete exactly once, preserving only work through the last paid checkpoint.

#### S.2.3: Cancel, resume and pause

- Given: Birch with one completed punch and one second elapsed toward its next.
- When: Escape cancels, then the player selects it again; separately the action is paused instead.
- Then: After cancellation three full intervals (six seconds) remain; after pause only the remaining one second is needed for the next punch, and paused time advances neither action nor renewal.

#### S.2.4: Duplicate or unavailable completion

- Given: A tree completing its final punch, or a target removed while approaching/working.
- When: Repeated selection or completion callbacks occur, or the target becomes unavailable.
- Then: A valid completion produces at most one item/XP/depletion transition; an unavailable target stops safely without awarding anything. Passive checks never spam the log.

### UC.3: Author and reset progression safely

#### S.3.1: Independent state and renewal

- Given: Two Birch instances referencing one definition.
- When: One is partially punched and felled, then 29.9 and 30 unpaused seconds pass.
- Then: The other remains unchanged; the depleted tree is still a stump at 29.9 seconds and renewed at 30 seconds, with no renewal reward and unchanged definition data.

#### S.3.2: Reset and fresh session

- Given: Nonzero XP, inventory logs, a damaged tree, a stump and an active action.
- When: Reset test is used, or a new Bootstrap session begins.
- Then: Woodcutting is level 1 / 0 XP, tree work/timers and default bag are restored, and no stale award or callback survives.

#### S.3.3: Content extension and invalid assets

- Given: A temporary second skill and tree variant with valid references; separately malformed thresholds, duplicate IDs and missing/out-of-range references.
- When: The valid assets are registered and used, and invalid assets are validated.
- Then: The variant reads the new skill's own XP without enum/controller content edits; invalid assets give actionable authoring errors and cannot begin a partially configured run.

#### S.3.4: Existing flow and UI regression

- Given: Existing bootstrap fixtures and the new Skills row/log events.
- When: Pickups, wheel, enemy, menus, panels, log scrolling, pause/reset and Start entry are exercised.
- Then: Existing behavior remains intact; UI clicks do not trigger world actions, level/result messages are retained in order, and no source asset changes during Play Mode.

#### S.3.5: Skill row and XP tooltip

- Given: Woodcutting has 300 total XP and Skills is open.
- When: The tester inspects the row and hovers its icon, name, level or bar.
- Then: The icon sits at left, Woodcutting and Lv. 5 share a baseline, and an approximately 16.7%-filled thin bar follows the bottom edge from the name to the level; the bar has no numeric text. The tooltip shows Current XP: 300 and XP to next level: 100. Hover causes no gameplay changes or log messages.

#### S.3.6: Threshold, cap and deferred states

- Given: Test states at 0 XP, 280 XP and 2,380 XP, plus a deferred skill and a missing icon.
- When: Skills is opened/refreshed and the corresponding rows are hovered.
- Then: Woodcutting shows Lv. 1 with an empty bar and 40 XP remaining, Lv. 5 with an empty bar and 120 remaining, or Lv. 15 with a full bar and Max level reached. Deferred rows show Not implemented with no invented XP/level, and a missing icon uses the fallback.

#### S.3.7: Tooltip lifecycle and input isolation

- Given: A visible XP tooltip near a panel/viewport edge.
- When: The pointer exits, the panel closes/switches, reset occurs, focus is lost, or skill state changes through a test fixture.
- Then: The tooltip stays onscreen while shown, dismisses on exit/close/reset/focus loss and refreshes from current state on update/reopen. It never triggers world input or blocks panel controls; normal panel pause behavior remains intact.

#### S.3.8: Uneven simulation intervals

- Given: Equivalent fresh Pine instances and sufficient level/capacity.
- When: One receives regular simulation deltas and another receives uneven deltas summing to the same active time, including an update crossing multiple punch intervals; separately only one log fits.
- Then: Neither pays before its threshold, both have identical work/log/XP totals at 10 and 20 active seconds, and the one-log-capacity case stops after the first payout with no later work/XP from that update. Existing enemy cadence is unchanged.

#### S.3.9: Missing scene registration and catalog wiring

- Given: A placed tree omitted from the session target list, duplicated registration, or a tree referencing an unregistered skill/item.
- When: The scene attempts to initialize.
- Then: Gameplay input stays disabled and the error identifies the missing/duplicate registration or reference; fixing the wiring permits a fresh run in which that tree renews and resets normally.

#### S.3.10: Tree world progress and Examine

- Given: A Pine between its first and second payout, and an unchanged practice enemy.
- When: Each is selected and its world progress/Examine presentation is inspected.
- Then: The panel follows the player above their head. Pine counts down ten seconds to its next log, restarts at ten seconds after the first payout, and disappears on completion/cancellation; Examine reflects the authored required level and Pine log output. The enemy retains its existing health and punch-interval display.

## Implementation route

Programmer subagent research completed 2026-09-28 through read-only inspection of this repository and Unity 6.3 documentation. Recommendation: extend the existing authored-definition/runtime-state split. No new framework or package is needed. The approved route below is now implemented; verification is recorded separately.

| Responsibility | Implemented owner and extension |
| --- | --- |
| Skill identity and tuning | New SkillDefinition asset: stable ID, display name, skill icon reference, cumulative integer threshold list; maximum level is the list length. Add a Skills category to GameDatabase and DefinitionEditors. A missing icon uses the UI fallback without changing gameplay validity. |
| Player progression | New plain C# PlayerSkills owned by BootstrapSession, keyed by skill asset reference; store only XP and derive level. Award results expose old/new XP and levels to presentation. Reset with the session. |
| Shared level gates | Optional SkillRequirement entries on InteractibleDefinition: skill reference and minimum level. No entries means unrestricted; all entries must pass. InteractionController checks centrally before behavior-specific validation, so tree and future station behaviors use the same rule. |
| Tree behavior | New TreeDefinition subclass with work count, interval, ordered yield punch checkpoints, inherited requirements, reward skill, total tree XP split across checkpoints, output item and renewal duration. Initial checkpoints are [3], [4] and [5, 10]; each gives one log. Use three assets, without subclassing EnemyDefinition or reading combat punch damage. |
| Per-tree state | A small TreeRuntimeState owned by each WorldTarget, created only for tree definitions, containing remaining work, next unpaid checkpoint, depleted state and renewal time. Reset/renewal restores checkpoint state as well as work. Keep configuration in assets; reuse existing presentation hooks. |
| Session timing | BootstrapSession advances tree lifecycle from its existing unpaused update. Depleted trees stay registered and examinable. Use simulation delta time, not async delays or wall-clock time. |
| Startup wiring | BootstrapSession validates the catalog and serialized scene references before input is enabled. Check placed participating trees against the session target list; expose actionable setup errors. Registration enables both ticking and reset; catalog membership alone does not register a scene instance. |
| Presentation | BootstrapHud uses the existing UI Toolkit panel and stylesheet for aligned icon/name/level/bar rows and a runtime hover-tooltip overlay. Read names/icons from definitions and calculate level, bar fill and tooltip values from PlayerSkills plus thresholds; do not store a second UI copy of progression. Deferred roster presentation stays separate from active progression. Reuse BootstrapSession.Report and History for messages and WorldActionGui for world progress. |

Initial code evidence (superseded by the arrival-only prerequisite correction): InteractionController validated before approach and on arrival. BootstrapSession already owns the run lifecycle and a 100-message history. GameDatabase and DefinitionEditors already provide catalog validation and browsing. WorldTarget already isolates instance work/health and reset. These are concrete extension points, not reasons to replace the interaction architecture.

Three existing implementation assumptions require focused changes. InteractionController currently clears elapsed time after every interval: preserve surplus for tree work without silently changing enemy cadence. BootstrapSession currently checks only that a catalog exists and ticks/resets its serialized target list: enforce catalog/reference/target validation during startup. WorldActionGui currently assumes Punching means enemy health plus interval progress: provide player-anchored next-payout seconds and a draining countdown bar through a small read-only presentation helper or focused branch, retaining enemy presentation. Generate tree Examine requirement/output text from the same definition fields used by validation so tuning cannot leave stale authored gate text.

**Completion boundary:** Bag.TryAdd currently raises Changed synchronously after changing inventory. Use a narrow yield-checkpoint commit with preflight availability, level and one-log capacity, then defer notifications while updating work, paid-checkpoint state, typed inventory item and bounded XP share. At the final checkpoint also commit depletion and renewal, and cancel the action. At an intermediate checkpoint continue only if another log fits; otherwise stop with the bag message. Publish notifications after state is consistent. A failed checkpoint consumes no payout work. Track paid state before callbacks can reenter so cancellation/resume cannot duplicate an intermediate log or its XP. Test observers and duplicate calls at both checkpoints. Small internal deferred-notification support is sufficient; no general transaction framework is needed. The checkpoint extension is a design proposal following the user request, not runtime-verified code.

Runtime award arithmetic must avoid overflow before applying the cap, for example by clamping the award to remaining XP capacity. Reject negative awards and invalid asset inputs; at-cap zero credited XP is a valid outcome of a positive authored award. Read-only level/gate queries must not publish events or messages. Future skills reuse the XP model, but their qualifying actions still require explicit behavior integration; data-only authoring does not invent a new mechanic.

| Alternative | Decision and reason |
| --- | --- |
| Hardcoded Woodcutting XP/level fields | Reject: cheap initially, but duplicates level lookup, gates and UI wiring for subsequent skills. |
| Skill assets plus plain runtime state | Recommend: fits the existing catalog, supports independent curves, exact threshold tests and later persistence by stable ID. Persistence is still deferred. |
| Runtime formula or AnimationCurve | Prefer an explicit table: exact integer boundaries and individual-level tuning are visible without rounding ambiguity or code changes. Keep the formula as design provenance. |
| Universal abilities/requirements graph, event bus or transaction framework | Defer: the current need is a level gate and one completion operation; broader infrastructure would add unproven complexity. |

Implementation checks include numeric boundary/cap tests, gate and cancellation tests, full bag before selection and either checkpoint, a one-log-capacity first payout, resumed completion without duplicate logs, 10/20-second timing, an observer-consistent completion test, independent renewal, invalid assets, temporary second-skill content, existing Play Mode regressions, unchanged definition snapshots and a normal-speed end-to-end pass. The architecture was researched, not compiled or runtime-validated in this planning session.

## Task list

- [x] **T.01:** Draft the skill loop, XP table, pacing calculations, requirements and acceptance scenarios.
- [x] **T.02:** Complete programmer research against the current code and record the recommended route and alternatives.
- [x] **T.03:** User approved proceeding with the spec on 2026-09-28; implementation follows the reviewed route.
- [x] **T.04:** Add skill definitions, catalog/session wiring validation and session-owned XP state with threshold, cap, isolation and invalid-startup tests (R.1, R.4).
- [x] **T.05:** Add shared level/capacity validation, surplus-preserving tree timing, yield checkpoints, per-log XP and renewal with checkpoint, uneven-delta and interruption tests (R.2, R.3.4).
- [x] **T.06:** Author Woodcutting, three trees/logs and reachable scene fixtures; verify full-route capacity and independent instances.
- [x] **T.07:** Add skill icon references/fallbacks and icon/name/level/unlabeled-bar rows with runtime XP tooltips; connect world progress and existing-log feedback. Verify level boundaries, cap/deferred states, tooltip dismissal/placement and UI isolation (R.3).
- [x] **T.08:** Run targeted and existing Play Mode coverage, normal-speed interaction smoke, and one full progression route; record evidence without substituting accelerated tests for pacing review.
- [ ] **T.09:** Obtain human feedback on lock-message clarity, unlock motivation and pacing; tune approved values, update authoring/testing guides and record remaining limits.

## Verification and approval record

### Implementation and verification: 2026-09-28

The user approved proceeding with this spec. Implemented the reviewed asset/runtime route: SkillDefinition and PlayerSkills, shared interaction requirements, TreeDefinition and per-instance TreeRuntimeState, atomic log/XP checkpoints, independent renewal, and compact Skills rows with runtime tooltips. The catalog now contains one skill, one player and thirteen interactibles, including seven items. Eighteen harvestable trees were added without converting the original scenery trees; the clearing and saved navigation extend south of spawn.

- Compilation passed. The full Play Mode suite passed **46/46 in 56.35 seconds**. After adding current-level gate text and catalog-derived unlock messages, all **12 Woodcutting tests passed in 10.90 seconds**.
- Boundary tests cover every level threshold and adjacent XP, multi-level awards, cap overflow and reset. Tree tests cover typed outputs, first-interval timing, Pine's 17/18 split, unequal deltas, cancellation, pause, full-bag retry, notification reentry, renewal, and independent instances.
- Catalog/session validation rejects invalid definitions, missing skills, duplicate targets and unregistered scene trees. A temporary second skill/tree definition exercised the same runtime behavior without new content code. All eighteen authored trees passed real-navigation reach checks.
- Saved catalog, all nine prefabs and all forty scene targets validated, including retained item identity, stack limits, blocking roots and saved navigation.
- Start-to-Bootstrap pointer entry passed. The existing normal-speed pointer pass covered pickups, station processing/collection, enemy punching, cancellation, camera and HUD/world isolation.
- The Woodcutting normal-speed pointer pass verified six-second Birch delivery and native Skills hover/cap/deferred states, tooltip viewport bounds, focus loss and panel-switch dismissal. Runtime screenshots were inspected for work counts/progress, stump appearance, compact alignment and readable tooltips. The 300-XP/cap screenshots use test awards to display those states, not a claimed natural progression run.
- The full normal-speed route passed: **106 trees, 2,380 XP, level 15 and 144 logs** (28 Birch, 40 Oak, 76 Pine), retaining the three starting flax in a fourth occupied bag slot. No XP injection, warping, time scaling, resets or disposal were used. Cap occurred on the first log of Pine 38 with 143 logs: only 5 of that 17-XP share were credited, then the last log arrived 10 seconds later with 0 XP.
- Measured elapsed simulation time was **1,495.44 seconds (24m55s)**; cap occurred at 1,485.44 seconds. This includes one desktop-focus pause during the Birch phase, which preserved work/XP and was resumed without resetting. The run did not separately measure paused time versus navigation/selection overhead; the combined excess over the authored 1,248 chopping seconds was 247.44 seconds. This verifies a complete route and capacity, not uninterrupted human pacing or the 25–35-minute hypothesis.
- All **16 database assets retained their SHA-256 hashes** through tests and the full route. Final current console reported zero errors/warnings and no compilation failure.
- T.09 remains open for human feedback on clarity, motivation and pacing; its authoring/testing-guide updates are complete. No builds were attempted; Drop, persistence and other skills remain deferred.

Runtime captures:

![Woodcutting Skills at 300 XP](D:/Repos/FirstLight/Docs/Images/woodcutting-skills.png)

![World chopping progress](D:/Repos/FirstLight/Docs/Images/woodcutting-working.png)

![Pine progress during the natural route](D:/Repos/FirstLight/Docs/Images/woodcutting-pine-route.png)

![Completed route inventory](D:/Repos/FirstLight/Docs/Images/woodcutting-route-complete.png)

The earlier planning/review entries below remain historical. Their approval-pending statements are superseded by the user's implementation authorization.


### Final PM and engineering review: 2026-09-28

Two independent reviewers inspected this spec; the engineer also checked the current runtime code. After the corrections below, both re-reviewed and reported **ready for implementation-route approval**, with no remaining material specification blocker. This is document/architecture review, not implementation approval or a runtime pass.

| Finding | Resolution |
| --- | --- |
| Accepted curve, Pine XP split and compact UI still described as open | Reconciled acceptance labels; remaining defaults and architecture are a single approval package. |
| Competing max-level row labels | Standardized on Lv. 15, full strip and Max level reached in the tooltip. |
| Pine progression could imply an efficiency upgrade | Explicitly documented 105 vs 150 XP/min and 6 vs 7.5 logs/min, with Pine offering a different material and two logs per selection. No reward rebalance. |
| Existing interval timer discards surplus time | Required surplus-preserving tree timing, ordered revalidation and uneven-delta checks, without changing enemy cadence. |
| Catalog existence alone does not validate startup or register placed trees | Required catalog/session-reference checks and exactly-once participating-tree registration before gameplay input. |
| Existing world progress assumes all punching uses enemy health | Specified tree work/next-payout presentation and definition-derived Examine text, preserving enemy feedback. |

Verification performed: recalculated all 15 thresholds, simulated the checkpoint route (cap at 1,238 active seconds/143 logs; final tree complete at 1,248 seconds/144 logs), and checked 55 unique requirement/scenario/task IDs, requirement references and affected local file links. No gameplay code, assets or scenes changed; no compilation, Unity tests, player build or human pacing test was performed. The compact v2 mock is accepted as a layout target, not proof of runtime usability.

Current action: implementation and automated verification are complete; T.09 human feedback remains. Other skills, persistence, ground overflow and item disposal remain outside the milestone.

### Earlier planning decisions

2026-09-28: Planning only. Existing code and project documents were inspected; the XP formula/table and fastest-tier route are calculated rather than playtested. No gameplay implementation, asset/scene edit, Unity test, or build has been performed for this spec. User approval remains required before T.04 onward.

2026-09-28 clarification: the user accepted the quadratic curve. A briefly requested ground-overflow rule was withdrawn in favor of the original behavior: insufficient inventory capacity stops the interaction, with no felling, log or XP. The original decision kept earlier completed punches; the later checkpoint correction below supersedes that cancellation rule. Ground-drop requirements and scenarios are withdrawn. Implementation-route approval remains pending.

2026-09-28 yield revision: “Pike” is interpreted as Pine. Pine now takes 20 active seconds, delivering one log at 10 seconds and one at felling (20 seconds). Superseded assumption: XP was originally held until felling; the per-log correction below replaces this. The all-tier route now takes 1,248 active seconds and produces 144 logs. Oak is faster for XP with these unchanged awards; final balance is open. Requirements and proposed runtime ownership now track paid checkpoints to prevent duplicate payouts on resume. No gameplay implementation or Unity tests performed.

2026-09-28 per-log XP correction: the user requires distinct Oak/Pine logs and XP distributed with individual log payouts. This supersedes XP-on-felling-only assumptions. The proposed integer split preserves Pine at 35 XP as 17 + 18; payout item, XP and paid state commit together. Intermediate level-ups, cap clamping, interruption and typed-stack capacity are covered. Verified split sums and updated cap timing; no gameplay implementation or Unity tests performed.

2026-09-28 Skills UI decision: the user specified icon/name/current-level rows with an unlabeled XP bar and hover details for current and remaining XP. Current XP is interpreted as cumulative total; bar fill is within the current level. Added icon authoring, tooltip/cap/deferred states and verification scenarios; updated the GDD. Documentation only; runtime UI is not implemented or visually verified.

## References


- Unity 6.3 ScriptableObject documentation: shared authored data and behavior; reviewed by the programmer researcher on 2026-09-28. https://docs.unity3d.com/6000.3/Documentation/Manual/class-ScriptableObject.html
- Unity 6.3 serialization rules: supported authored arrays/lists and separation from ordinary runtime dictionaries; reviewed by the programmer researcher on 2026-09-28. https://docs.unity3d.com/6000.3/Documentation/Manual/script-serialization-rules.html


## 2026-09-28: Player progress and cancellation correction

The user requested progress above the player and a countdown to each reward. Woodcutting and processing now display remaining seconds with a draining bar; enemy health and punch cadence remain unchanged, with their panel also above the player. Escape cancels active work. All tree stops roll unfinished work back to the last paid checkpoint. Cancelling Pine at 17 seconds retains one Pine log, 17 XP and checkpoint five; resuming requires ten full seconds for the second log and its 18 XP. Cancelling before the first payout resets all work. Pause preserves unfinished work and time. Earlier screenshots and verification describe the initial implementation.


![Player-anchored reward countdown](D:/Repos/FirstLight/Docs/Images/woodcutting-countdown.png)


Verification: full Play Mode suite **46/46 passed in 55.72 seconds**. Updated assertions cover cancellation before any payout and after unpaid punches beyond the first payout, pause-preserved countdowns, capacity-stop rollback, no duplicate rewards, and player rather than target anchoring. A normal-speed Pine smoke passed with a queued Escape key, restart from checkpoint five, two total logs and 35 total XP; level, approach and focus used test fixtures. The first smoke timed out after cancellation; the rerun held session focus to prevent background Editor pauses. The updated screenshot was visually inspected. Final console had zero errors/warnings; Editor stopped on Start. Builds and another full leveling route were not run for this correction.

## 2026-09-28: Check interaction prerequisites in range

Clicking an actionable interactible first moves the character into range. Skill, inventory capacity and required-input checks run upon arrival, before any work, reservation or reward. This applies to trees, pickups and production stations through the shared interaction controller. An under-level player therefore walks to Oak and then receives its level requirement message. Requirements use current state at arrival and are still checked at work/reward boundaries. Unreachable or missing targets stop safely; Examine also approaches before showing its description (see the later Examine correction).


Verification: **47/47 Play Mode tests passed in 57.79 seconds**. The new Oak navigation test confirms no requirement message during approach, arrival within reach, one level message and zero work/XP/logs. Pickup-capacity and station-input tests now require arrival before rejection. The first run exposed two obsolete immediate-rejection test expectations, which were corrected before the passing full rerun. No builds were run.


## 2026-09-28: Interrupt actions with world commands

Ground movement commands and selection of another interactible replace active chopping, processing or combat. The shared cancellation path releases production reservations and resets unpaid tree work while retaining paid logs/XP. Same-target clicks do not restart work. UI panel clicks, camera controls and menu opening do not replace the action; choosing Examine now does. Reward commits remain protected against callback reentry. An unreachable replacement stops the old action and reports the navigation failure.


Verification: **48/48 Play Mode tests passed in 57.41 seconds**. Tests cover replacement movement and target selection during production/combat, returned station inputs, same-target continuity, Pine checkpoint rollback for both replacement commands, and resumed rewards without duplication. Final current console had zero errors/warnings. No build or additional pointer smoke was run.


## 2026-09-28: Click to open the action menu

Hover shows the interactible border only. Left click opens its action menu beside the pointer; a second click on the primary action starts the approach/interaction. The menu remains at the opening position, clamped to the viewport, and includes Examine and Cancel. Opening a menu does not interrupt ongoing work; confirming a different target does. Outside click, right click and Escape dismiss without click-through. Ground movement remains one click when no menu is open. This supersedes direct one-click target interactions and hover action labels described earlier.


Verification: **48/48 Play Mode tests passed in 58.16 seconds**. The updated normal-speed pointer smoke passed with native UI input: first click only opens the menu, second click on Punch starts Birch and grants one log/10 XP after six seconds. Menu positioning, stable pointer anchoring, edge bounds, primary action, Examine, dismissal and pause/reset behavior are covered. The menu screenshot was visually inspected. Settings controls text and current guides reflect the new flow.

![Click-open action menu](D:/Repos/FirstLight/Docs/Images/world-action-menu.png)


## 2026-09-28: Examine requires interaction range

Choosing Examine replaces the current intention and approaches the target, including scenery and depleted trees whose primary action is Examine. Report the description only upon arrival within interaction range. Examine does not require harvest levels, bag space or production inputs and grants no rewards. Moving, choosing another action or Escape can cancel the approach without reporting the description. Missing/deactivated targets and unreachable paths stop safely. This supersedes earlier remote Examine behavior.


Verification: **49/49 Play Mode tests passed in 58.98 seconds**. Menu Examine approaches before reporting, leaves the pickup intact, and finishes within reach. Under-level/full-bag tree Examine and depleted-tree primary Examine succeed without extra rewards. No builds or additional pointer smoke were run.
