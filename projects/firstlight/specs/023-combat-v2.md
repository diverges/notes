# Combat v2: Rules and balance

Status: Implemented and verified; balance provisional
Updated: 2026-09-30

Quick reference: [Combat summary](D:/Repos/FirstLight/Docs/combat.md).

## Problem statement

Replace sword-gated, single-slot combat with equipment-independent attacks, shared one-second world ticks and combat progression.

## Proposal

- Attack is always available, including unarmed and with non-sword equipment. The player must still be alive and have an eligible target in reach.
- Multiple actions can resolve on each world tick, including simultaneous player and enemy attacks. Tick duration does not determine attack speed.
- Players and monsters share Vitality, Weapon Proficiency, Strength and Block.
- Weapons supply damage ranges. Explore five increasingly strong tiers; ranges remain undecided. Armor supplies defensive stats, with room for equipment and buffs to build player power.
- Start near 20 HP. Keep numbers readable; 100 HP is attainable, not a cap.
- Preserve approach, cancellation, pause/reset, enemy behavior, loot and feedback unless a later decision changes them.

### Stats

| Stat | Combat effect | Player XP trigger |
| --- | --- | --- |
| Vitality | +4 maximum HP per level | Takes damage |
| Weapon Proficiency | Opposes Block for hit chance | Lands an attack |
| Strength | +5% of base weapon damage per level | Undecided |
| Block | Opposes attack stats for avoidance and damage reduction | Progression model undecided |

Shared monster stats do not imply monster XP progression.

### Difficulty targets

Difference means **enemy combat level minus player combat level**.

| Difference | Intended difficulty |
| --- | --- |
| -4 or less | Easy |
| -3 through +3, including equal | Normal |
| +4 through +9 | Doable, medium to hard |
| +10 or more | Hard to impossible; generally avoid |

These guide combined stat/equipment tuning, not attack restrictions or guaranteed outcomes. Overall combat level and reference equipment/buffs remain undefined. The formulas have not yet been shown to achieve these bands.

## Worked calculations and sample tables

### Formula reference

P = attacker Proficiency; S = attacker Strength; B = defender Block; V = defender Vitality.
A = S: attacker Strength opposes defender Block for damage reduction.

```text
Hit chance:
p = 1 / (1 + 2^((B - P) / 5))

Strength multiplier:
m = 1 + 0.05 * S

Maximum health:
H = 20 + 4 * (V - 1)

Damage reduction:
gap = max(0, B - A)
q = 1 + 0.4 * gap
reduction = 1 - 1/q

Final damage range:
minimum = weaponMin * m / q
maximum = weaponMax * m / q
On a hit, roll between minimum and maximum, then round to nearest integer (.5 up).

For the uniform-roll examples:
meanHit = (minimum + maximum) / 2
meanAttempt = p * meanHit
landedHitBudget = H / meanHit
attemptBudget = H / meanAttempt
```

The hit curve and stat coefficients are agreed. Health uses a working level-1 baseline of 20 HP. The reciprocal reduction formula gives 80% reduction at a ten-level defensive advantage over Strength. No extra probability cap is applied. Here ^ means exponentiation.

**Example assumptions:** base weapon 2-4, uniform rolls, no equipment/buff bonuses or healing, and failed attacks deal zero damage. Mitigation uses A = S. These analytical tables show unrounded values to two decimals. Actual combat now rounds each rolled hit after modifiers to the nearest integer (.5 up); table damage and encounter estimates are pre-rounding, not exact current outcomes. The 2-4 range is the initial Worn sword profile. Weapon balance remains provisional.

A hit budget is health divided by mean damage, not an exact expected kill count. Randomness, overkill and rounding affect actual counts. For constant damage d, lethal landed hits = ceil(H/d).

### Hit chance by level difference

| P minus B | Hit chance |
| --- | --- |
| -20 | 5.88% |
| -15 | 11.11% |
| -10 | 20.00% |
| -5 | 33.33% |
| -1 | 46.54% |
| 0 | 50.00% |
| +1 | 53.46% |
| +5 | 66.67% |
| +10 | 80.00% |
| +15 | 88.89% |
| +20 | 94.12% |

Example: P = 10, B = 20 gives `1 / (1 + 2^2) = 20%`. P = 50, B = 60 gives the same chance: only the difference matters.

### Strength and raw weapon damage

Base weapon 2-4; no damage reduction.

| Strength | Multiplier | Raw minimum | Raw maximum | Mean landed damage |
| --- | --- | --- | --- | --- |
| 1 | 1.05x | 2.10 | 4.20 | 3.15 |
| 5 | 1.25x | 2.50 | 5.00 | 3.75 |
| 10 | 1.50x | 3.00 | 6.00 | 4.50 |
| 15 | 1.75x | 3.50 | 7.00 | 5.25 |
| 20 | 2.00x | 4.00 | 8.00 | 6.00 |
| 30 | 2.50x | 5.00 | 10.00 | 7.50 |
| 50 | 3.50x | 7.00 | 14.00 | 10.50 |

Example: Strength 10 gives a 1.5x multiplier, turning 2-4 into 3-6 damage. Better weapon bounds remain proportionally stronger at every Strength level.

### Opposed Block reduction and final hit damage

Strength = A = 10, base weapon 2-4, raw damage 3-6.

| Defender Block | Defensive gap | Damage reduction | Final damage range | Mean landed damage | Landed capacity multiplier |
| --- | --- | --- | --- | --- | --- |
| 5 | 0 | 0.00% | 3.00-6.00 | 4.50 | 1.00x |
| 10 | 0 | 0.00% | 3.00-6.00 | 4.50 | 1.00x |
| 11 | 1 | 28.57% | 2.14-4.29 | 3.21 | 1.40x |
| 15 | 5 | 66.67% | 1.00-2.00 | 1.50 | 3.00x |
| 20 | 10 | 80.00% | 0.60-1.20 | 0.90 | 5.00x |
| 25 | 15 | 85.71% | 0.43-0.86 | 0.64 | 7.00x |
| 30 | 20 | 88.89% | 0.33-0.67 | 0.50 | 9.00x |

Example: Block 20 gives gap 10, divisor 5 and 80% reduction: final damage is 0.6-1.2. With Proficiency 10, hit chance is 20% and expected damage per attempt is `0.20 * 0.90 = 0.18`.

**Rounding, confirmed 2026-10-01:** round each final rolled hit to the nearest integer, with .5 upward. Do not interpolate rounded endpoints or round the sum of simultaneous hits. No minimum-one floor: values below .5 become zero. Queued hazards use the same rule. Hit chance remains unchanged; landed zero-damage hits remain hits and grant no damage-taken XP.

### Vitality versus Strength scaling

Strength = Vitality, base weapon 2-4, no mitigation. Proficiency = Block gives 50% hit chance.

| Strength = Vitality | Maximum HP | Landed damage range | Mean damage / HP | Landed-hit budget | Attempt budget at 50% |
| --- | --- | --- | --- | --- | --- |
| 1 | 20 | 2.10-4.20 | 15.75% | 6.35 | 12.70 |
| 5 | 36 | 2.50-5.00 | 10.42% | 9.60 | 19.20 |
| 10 | 56 | 3.00-6.00 | 8.04% | 12.44 | 24.89 |
| 15 | 76 | 3.50-7.00 | 6.91% | 14.48 | 28.95 |
| 20 | 96 | 4.00-8.00 | 6.25% | 16.00 | 32.00 |
| 21 | 100 | 4.10-8.20 | 6.15% | 16.26 | 32.52 |
| 30 | 136 | 5.00-10.00 | 5.51% | 18.13 | 36.27 |
| 50 | 216 | 7.00-14.00 | 4.86% | 20.57 | 41.14 |

Example at level 10: `H = 20 + 4 * 9 = 56`; mean damage is 4.5. Hit budget = `56 / 4.5 = 12.44`; attempt budget = `56 / 2.25 = 24.89`.

Health grows faster proportionally than fixed-weapon damage, so higher matched levels take more landed hits. Weapon tiers can offset this. Vitality 21 reaches 100 HP before buffs; 100 is not a cap.

### How avoidance and mitigation combine

Attacker P = A = S = 10, base weapon 2-4, defender HP fixed at 56. Only Block changes.

| Defender Block | Hit chance | Mean landed damage | Expected damage / attempt | Landed-hit budget | Attempt budget |
| --- | --- | --- | --- | --- | --- |
| 10 | 50.00% | 4.50 | 2.25 | 12.44 | 24.89 |
| 15 | 33.33% | 1.50 | 0.50 | 37.33 | 112.00 |
| 20 | 20.00% | 0.90 | 0.18 | 62.22 | 311.11 |

A ten-level defensive advantage gives 5x landed-damage capacity and lowers hit chance from 50% to 20%. Together, the attempt budget grows 12.5x. **This compounding is intended.** Armor bonuses to Block would affect both protections and need tuning against the difficulty bands.

## Implementation defaults and remaining decisions

User confirmed Strength as the mitigation opponent and authorized implementation. The first implementation uses the reversible defaults below; Strength/Block progression and encounter balance remain open.

| Area | Current behavior |
| --- | --- |
| Damage | Hit roll strictly below hit chance succeeds; failures are blocks. Independent continuous damage roll, then nearest-integer final damage (.5 up), no minimum floor. |
| Timing | All authored attacks take three seconds, including first attack; readiness rounds upward. Due attacks use pre-damage stats and may cause mutual defeat. |
| Cancellation | Cancel/equipment change removes the player's intent. Engaged enemies continue until out of reach or defeated. Repeated target clicks preserve progress. |
| XP | 1 Vitality XP per incoming damaging melee/acid result; 1 Proficiency XP per landed player attack, including unarmed and lethal results. No XP for cancelled intents. Strength/Block amounts default to zero, with landed-hit/blocked-incoming triggers ready for tuning. |
| Levels | Four shared skill references; initial 50-level curves use `XP(level) = 10*(level-1)*(level+2)`. Monsters author levels directly and do not earn XP. |
| Health | Maximum-HP gains do not heal or revive; decreases clamp current HP. Storage supports existing fractional health; combat applies whole-number damage and damage text displays whole numbers. |
| Equipment | Unarmed/non-weapon 1-2; Worn sword 2-4; Stone Sword 3-5. Equipment adds typed stat bonuses; named transient modifiers replace their source, sum across sources and clear on reset. Effective stats minimum 1. |
| Enemies | Initial ordinary enemies use all level-1 stats and 1-2 attacks. Queen uses Vitality 20, other stats 10, and 2-4 attacks. Existing aggression, movement and loot settings are retained. These are provisional tuning values. |
| World | Clock advances even when idle. Combat and acid damage resolve on its second boundaries; movement/animation remain continuous. Existing gathering, crafting, fire, regrowth and day/night timing is preserved pending a separate scheduling decision. |

Remaining: Strength/Block XP policy, five-tier weapons/armor, overall combat level, reference loadouts and measured difficulty bands. Existing gear bonuses start at zero; the typed bonus path is implemented, but no armor balance is claimed.

## Use cases

- **UC.1:** Player attacks without an equipment requirement.
- **UC.2:** Combatants resolve due actions on the same world tick.
- **UC.3:** Player gains combat skill experience.

## Requirements

IDs are retained for verification. Formula and table sections above own the numeric definitions.

| ID | Requirement |
| --- | --- |
| R.1.1 | Player always possesses Attack. |
| R.1.2 | Empty hands or non-sword equipment do not prevent otherwise eligible attacks. |
| R.1.3 | Unequipping a sword does not remove Attack; changing right-hand equipment cancels the current attack. |
| R.2.1 | Shared world ticks advance every unpaused simulation second. |
| R.2.2 | Multiple attacks can resolve in one tick. |
| R.2.3 | A due enemy attack is not delayed solely because the player attacks that tick. |
| R.2.4 | Attack intervals and same-tick lethal resolution are defined separately from tick duration. |
| R.3.1 | Player skills include Vitality, Weapon Proficiency and Strength. |
| R.3.2 | Taking damage grants Vitality XP. |
| R.3.3 | Landing an attack grants Weapon Proficiency XP. |
| R.3.4 | Keep unresolved progression configurable and distinguish initial tuning from confirmed formulas. |
| R.3.5 | Block influences avoidance and landed damage through opposed stats. |
| R.3.6 | Monsters have the same four combat stats as players. |
| R.3.7 | Attack success compares offensive/defensive stats with randomness. |
| R.3.8 | Use +4 HP per Vitality level and +5% base weapon damage per Strength level; working starting HP is 20 at Vitality 1. |
| R.3.9 | Weapons supply damage ranges; use a fallback profile for unarmed/non-weapon attacks. |
| R.3.10 | Equal Proficiency/Block gives 50% hit chance; +5 Proficiency advantage gives about 67%. |
| R.3.11 | Equal stat differences give equal hit chances regardless of absolute levels. |
| R.3.12 | Equipment supports defensive bonuses; additive stacking is implemented, armor values remain untuned. |
| R.4.1 | Tune encounters to the four difficulty bands above. |
| R.4.2 | Define combat level, reference loadouts and measurable criteria before verifying difficulty. |
| R.4.3 | Difficulty bands do not prohibit attacks or guarantee outcomes. |

## Scenarios

| ID | Given | When | Then |
| --- | --- | --- | --- |
| S.1.1: Unarmed | Living, unarmed player; eligible enemy in reach | Attack becomes due | Attempt resolves without a sword requirement. |
| S.1.2: Non-sword equipment | Player carries a non-sword item | Player selects an eligible enemy | Equipment does not deny Attack. |
| S.1.3: Unequip | Sword equipped | Sword removed | Attack capability remains. |
| S.2.1: Simultaneous attacks | Player/enemy attacks due on tick N; nonlethal outcomes | Tick N resolves | Both resolve on N. |
| S.2.2: Multiple enemies | Player/several enemies due on N; nonlethal outcomes | Tick N resolves | All resolve without successive global slots. |
| S.2.3: Pause | World between ticks | Pause, then resume | Paused time advances no ticks or attacks. |
| S.3.1: Vitality XP | Player below XP cap | Enemy deals nonlethal damage | Vitality XP increases by the agreed amount. |
| S.3.2: Proficiency XP | Player below XP cap | Unblocked attack deals positive damage | Proficiency XP increases by the agreed amount. |

## Engineering proposal

### Ownership

| Source | Responsibility |
| --- | --- |
| `CombatMath` | Pure hit chance, Strength multiplier, Block divisor, HP and sampled damage calculations. Receives random samples; changes no state. |
| `CombatRulesDefinition` | Four skill references, permanent Attack capability, unarmed profile and per-event XP tuning. |
| `PlayerDefinition.Combat` / `EnemyDefinition` | Effective player stats from skills, gear and temporary modifiers; authored monster stats. Both expose the same `CombatStats` and `AttackProfile`. |
| `CombatQueue` / `BootstrapSession` | One unpaused simulation clock. Collect all due callbacks at each integer second, then signal batch completion. Session subdivisions preserve existing non-combat deadline ordering. |
| `CombatService` | Engagement, independent readiness, reach checks, outcome collection, health commit, XP, death and result publication. |
| `CombatFeedback` / `PlayerPresentation` | Display actual damage/blocks and attack motion. Existing swing/guard is the temporary unarmed/non-sword fallback; animation events never apply combat damage. |

### Tick contract

1. Sample eligibility/reach at the boundary after that update's movement. Queue order determines reproducible random-sample order, not exclusive priority.
2. Collect every due attack using pre-commit stats. Each attack consumes a hit and damage sample. Acid supplies fixed damage and rechecks tile occupancy at commit eligibility.
3. Apply enemy damage and aggregate player damage before death handling. Previously eligible attacks survive same-tick lethal damage.
4. Award XP from committed results, finalize death/loot, cancel future intents and publish typed `CombatResult` records. Newly earned stats affect the next batch; health growth never revives a defeated actor.

`WorldTarget.Punch` remains an immediate direct-damage helper; the resolver uses separate health/defeat operations. Queen acid keeps its telegraph/flight visuals and pulse cadence, but damage waits for the next shared boundary. Leaving a tile before that boundary avoids its pending pulse.

### Extending and tuning

Edit `Assets/Data/Combat_Rules.asset` for unarmed attacks and XP. Edit skill assets for progression curves, enemy definitions for monster stats/attacks, and equipment definitions for weapon ranges/cadence or typed bonuses. A new weapon tier needs data, not a formula branch. Temporary buffs use `SetCombatModifier(source, stats)` and `RemoveCombatModifier(source)`; the caller owns duration.

The one-time `Tools/ConfigureCombatV2.cs` migration preserves existing asset GUIDs, registers skills and assigns rules/profiles. `Run` refuses replacement; use `Validate` after designer edits. Player/enemy maximum health is derived from Vitality; old `_maxHealth` data is superseded. Current health now serializes as a float, and player state validation rejects health above the derived maximum. No persistent save format exists to migrate.

### Validation scope

Formula tests cover the hit boundary, absolute-gap invariance, pre-rounding 80% mitigation at +10 Block, rounded range endpoints, midpoint rounding, roll-before-round ordering, zero-damage hits and 100+ HP progression. Runtime tests cover simultaneous/multiple attackers, mutual defeat, cancellation, pause/reset, XP, derived HP, zero-damage non-accumulation and per-hit hazard rounding. Broader scene tests cover the existing interactions and queen hazards.

2026-10-01: nearest-integer damage implementation passed the full 216/216 Play Mode suite in 174.73 seconds on Unity 6000.3.20f1. Damage is rounded after the roll/modifiers, including each queued hazard; values below .5 become zero. Shared Inspector previews use the same rounding helper. No new Player build or difficulty playtest.

Difficulty still requires playtesting at -4/-3, +3/+4 and +9/+10 with defined combat levels/loadouts. A +3 defensive gap already yields 54.55% mitigation plus 39.75% hit chance; formulas alone do not validate the normal band.

## Task list

- [x] Record current rules, difficulty targets, formulas and sample tables.
- [x] Implement explicit initial timing, precision and XP defaults with edge-case scenarios.
- [ ] Tune weapons/armor and validate difficulty boundaries: -4/-3, +3/+4 and +9/+10.
- [x] Obtain implementation authorization and implement gameplay/data migration.
- [x] Complete verification and update current-behavior documentation.

## Verification

2026-09-30, Unity 6000.3.20f1: all 208 Play Mode tests passed. `ConfigureCombatV2.Validate` passed for four combat skills, two player presets and six enemy definitions. Pointer smoke checks passed unarmed and equipped: Attack menu, approach, hidden work bar, same-tick hits/blocks, actual red damage text, blue BLOCK text and escape without stale damage. Unarmed and equipped damage captures were visually inspected. No runtime errors were reported by either smoke check.

The 36 numerical example rows were independently checked during design. Local links, code fences and all 22 unique requirement IDs were checked after implementation. No Player build, difficulty playtest, performance profile or human presentation acceptance; weapon/monster values and Strength/Block XP remain provisional.
