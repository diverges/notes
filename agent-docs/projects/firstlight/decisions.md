# First Light: Decisions

Newest first. Only consequential, still-relevant choices; full narrative in [history.md](history.md).

## 2026-10-03 — Specs live in the notes repository

- **Context:** Specs were in the game repo under `Docs/specs`.
- **Decision:** All specs (000–035, verification report, index) moved to the notes repo `projects/firstlight/specs/`. New specs continue at 036. Load specs only when the user asks.
- **Why:** User also writes specs; generated Markdown and planning default to the notes repo, while code-repo docs stay primarily user-authored.
- **Source:** history.md 2026-10-03 "Specs migrated to the notes repository".

## 2026-10-02 — Two production assemblies, FirstLight.<Area> namespaces

- **Context:** Senior engineer/architect review with fixes.
- **Decision:** `FirstLight.Runtime` + `FirstLight.Editor` + separate `FirstLight.PlayModeTests`; feature folders/namespaces; camelCase private fields (renames keep `FormerlySerializedAs`); no mutable static runtime state.
- **Why:** Keeps tests out of player builds; current size does not justify more assemblies, an event bus or a generic action framework. Split only on concrete ownership/reliability failures.
- **Source:** code-review-2026-10-02.md.

## 2026-10-02 — World split into a five-scene set with WorldGameState

- **Context:** Single World scene mixed designer-owned content and runtime systems.
- **Decision:** `World` hosts manager/session/navigation/loading; Player, Terrain, Gameplay and Lighting are separate scenes bound through `GEN_WorldGameState`. Gameplay starts after the black overlay reveals.
- **Why:** Separate scene ownership for authoring; explicit readiness and recovery. Shared prefab/TerrainData/bake files still need one writer.
- **Source:** history.md 2026-10-02 "World scene management implemented"; spec 035.

## 2026-10-02 — Scene managers discover gameplay targets automatically

- **Context:** Manual serialized target lists failed (35 null saved references, cause unestablished).
- **Decision:** `WorldSceneManager` subclasses (`World<Area>Manager`) collect targets at startup/retry, including inactive objects, excluding runtime loot/fires; nothing serialized. "Manager" names components coordinating services and multiple behaviours.
- **Why:** Removes a fragile authoring step. Supersedes the 2026-09-30 Undo-aware manual collection Inspector.
- **Source:** history.md 2026-10-02 "Automatic gameplay target discovery".

## 2026-10-02 — Project-owned water shader with Ignore Lighting

- **Context:** Water washed out in Play Mode; isolated to FlatKit Water's additional-light stage reacting to the Moon fill light.
- **Decision:** `GEN_WorldWater.shader` (copy of FlatKit Water) with an `_IgnoreLighting` toggle, enabled on `Ocean.mat`. Vendor shader unchanged.
- **Why:** Keeps moon lighting elsewhere; earlier foam/depth suspicions were wrong.
- **Source:** history.md 2026-10-02 water entries.

## 2026-10-02 — World terrain baseline at Y −10

- **Context:** User wanted room to sculpt below ground level.
- **Decision:** Terrain tiles at transform Y −10, heightmaps raised 10 m, vertical size 12 m (world Y −10..2); water at Y −1.
- **Why:** Preserves the world surface while enabling downward sculpting. Supersedes the zero-heightmap/Y0 baseline.
- **Source:** history.md 2026-10-02 "World terrain baseline raised to10m".

## 2026-10-02 — Per-period fog via geometry fog

- **Context:** Day fog tuning and removal of the screen-space veil.
- **Decision:** Each lighting period (Night/Dawn/Day/Dusk) owns fog colour, Start and End, blended across durations, with camera-zoom compensation (gain 1). Brightness default 0.7. No screen-edge fog veil.
- **Why:** User clarified fog must be per period. Supersedes the 2026-09-30 rolling edge mist and solar-elevation fog distance.
- **Source:** history.md 2026-10-01 "Actor feedback and scene polish"; 2026-10-02 fog entries; spec 017.

## 2026-10-01 — Focus loss does not pause gameplay

- **Context:** User request.
- **Decision:** `BootstrapSession.Paused` depends only on Settings/menu pause and defeat. Focus loss clears transient selection/UI only.
- **Why:** Movement, combat and work continue in the background. Supersedes earlier focus-pause requirements.
- **Source:** history.md 2026-10-01 "Focus loss no longer pauses gameplay".

## 2026-10-01 — Whole-number combat damage

- **Context:** Inspector previews showed rounded damage.
- **Decision:** Round each final hit (and each hazard tick) to nearest integer, .5 up; no minimum-one floor. Zero-damage hits are hits but grant no Vitality XP.
- **Why:** User authorized applying preview rounding to real combat. Health remains float-compatible.
- **Source:** history.md 2026-10-01 "Whole-number combat damage implemented"; spec 023, 025.

## 2026-10-01 — Prefab → enemy definition is the only authored link

- **Context:** Enemy Inspector work created bidirectional links.
- **Decision:** Removed `EnemyDefinition.Prefab`; editor discovers prefabs by reverse lookup. Queen is `BossMonster : EnemyDefinition`; boss abilities stay prefab components. Gameplay action systems for enemies deferred.
- **Why:** User clarification; avoids two sources of truth.
- **Source:** history.md 2026-10-01 enemy entries; spec 025.

## 2026-10-01 — Keep the existing UI layout; retro style only

- **Context:** A proposed UI redesign board was rejected.
- **Decision:** Preserve panel arrangement. VT323 font, charcoal panels, bone text, rust/burgundy borders. Bag/Equipment/Recipes/Skills/Settings open as separate panels above a fixed bottom-right nav frame. Skills panel shows only implemented skills (Cooking, Smithing, Agility removed). Compact three-message console with caret history.
- **Why:** User prefers the existing layout and retro feel; an intermediate merged-panel trial was explicitly rejected.
- **Source:** ui-review-2026-10-01.md (correction); ui-conversion.md; history.md 2026-10-01 UI entries.

## 2026-10-01 — Original retro RPG audio

- **Context:** Sound review.
- **Decision:** Replace the whole 30-clip bank in place with original synthesis referencing Old School RuneScape plus some Diablo. No borrowed samples; GUIDs/wiring retained.
- **Why:** User-chosen references. Listening acceptance still pending.
- **Source:** sound-review-2026-10-01.md; history.md 2026-10-01 "Retro RPG audio implemented".

## 2026-10-01 — Minimap exploration is session-only

- **Context:** Minimap spec.
- **Decision:** Exploration persists only per session/reset (no cross-launch save); 5×5 cells, reveal occupied cell plus neighbours; markers default white items/red aggressive/yellow passive; distant markers opt-in per definition.
- **Why:** User explicitly chose existing session/reset persistence.
- **Source:** history.md 2026-10-01 minimap entries; spec 030.

## 2026-09-30 — Combat v2 rules

- **Context:** Replacing fixed 10-damage sword combat.
- **Decision:** Attack always available; one-second shared clock with simultaneous attacks (mutual defeat possible); four combat skills (Vitality, Proficiency, Strength, Block); hit chance `1/(1+2^((Block−Proficiency)/5))`; damage × `(1+0.05·Strength)/(1+0.4·max(0,Block−Strength))`; HP `20+4·(Vitality−1)`. Difficulty bands by combat-level gap (≤−4 easy … ≥+10 hard).
- **Why:** User-confirmed stat roles and targets (50% at parity, ~67% at +5, ~80% reduction at +10 Block advantage). Tuning values (weapons 1-2/2-4/3-5, enemy stats) are implementation defaults, not approved balance. Strength/Block XP policy open.
- **Source:** history.md 2026-09-30 combat entries; spec 023; code repo `combat.md`.

## 2026-09-30 — Player ScriptableObject is default and runtime source of truth

- **Context:** Architecture review; player state was split across session containers.
- **Decision:** `PlayerDefinition` owns bag, equipment, health, XP (later discovered recipes); runtime clone of the authored asset is live state. Reservations stay transient. Disk save is future work.
- **Why:** User direction; supersedes the independent save-model recommendation.
- **Source:** architecture-review-2026-09-30.md; spec 020.

## 2026-09-30 — Capability grants and one-way recipe ownership

- **Context:** Action model investigation.
- **Decision:** Available actions derive from default, skill-level, equipment and named grants (`ActionDefinition` identity; separate action, item-use and fire responses). Stations and skills list recipes; no back-references; recipes own inline outputs. Equipment is an `EquipmentItemDefinition` subclass.
- **Why:** Removes mixed-purpose fields and duplicate truth; keeps the shared reservation/commit path.
- **Source:** actions-review-2026-09-30.md; specs 020–022.

## 2026-09-30 — Direct Assets roots and centralised Data

- **Context:** Project structure migration.
- **Decision:** `Assets/FirstLight` removed; direct roots, vendor under `Assets/Plugins`, all gameplay definitions under `Assets/Data`; player-exclusive content grouped under Player. `Docs/project-structure.md` is authoritative.
- **Why:** User conventions; older `Assets/FirstLight/...` paths in history are superseded.
- **Source:** history.md 2026-09-30 structure entries.

## 2026-09-30 — Retro presentation via full-screen dither, no lens effects

- **Context:** Material-level dither did not reproduce the art study.
- **Decision:** Retro Shaders Pro surfaces plus project-owned `WorldDitherFeature` (perceptual sRGB quantisation, Bayer thresholds) at 96 levels, 2 px. No fisheye/scanlines/CRT distortion. Vendor CRT feature disabled.
- **Why:** User approved PSX-horror direction (2026-09-29) and explicitly excluded fisheye; perceptual quantisation removed near-field banding.
- **Source:** history.md 2026-09-29/30 presentation entries; spec 014, 016.

## 2026-09-29 — Shared 1×1 runtime grid

- **Context:** User wanted aligned movement, interaction and drops.
- **Decision:** `WorldGrid` snaps X/Z to whole numbers; movement ends on cells; drops/fires use supported cells; pickup ends on the item cell or a free neighbour (shortest path).
- **Why:** Supersedes free-destination movement; travel remains smooth.
- **Source:** history.md 2026-09-29 grid/pickup entries.

## 2026-09-29 — Hidden ordered crafting patterns

- **Context:** User rejected a visible recipe picker.
- **Decision:** Bench has three ingredient squares and a result square; only an exact ordered pattern match appears (R = Rock, L = any log). Duplicate patterns rejected. A discovered-recipe window lists only successfully crafted item recipes.
- **Why:** Discovery without exposing recipes.
- **Source:** history.md 2026-09-29 crafting entries; specs 012, 027.

## 2026-09-28 — GDD is authoritative, without decision labels

- **Context:** GDD mixed rules with approval history.
- **Decision:** GDD states direct game rules; no Decided/Proposed labels, approval narration or dated logs. Unselected ideas live in [design-backlog.md](design-backlog.md).
- **Why:** User preference; overrides the generic template's label convention.
- **Source:** history.md 2026-09-28 "Authoritative GDD language and structure".

## 2026-09-28 — Interaction model

- **Context:** RuneScape-like interaction iterations.
- **Decision:** Hover shows main action + name and an inline ellipsis badge if more actions; left-click = first action, right-click = full menu; Examine-only targets have no hover/left-click (right-click only). Bag/Equipment/Skills panels stay live; only Settings pauses. Selecting a new target or ground cancels active work.
- **Why:** User-confirmed; supersedes two-click menus and interaction locking.
- **Source:** history.md 2026-09-28 interaction entries; spec 004.

## 2026-09-28 — Gloomy forest setting and palette

- **Context:** World/art direction.
- **Decision:** Amnesiac wanderer in a foggy, eerie forest; slow grind is intentional. Old RuneScape/EverQuest low fidelity with modern lighting/fog. Accepted 40-colour palette (8 hue families × 5 values).
- **Why:** Supersedes quiet-valley framing and the six-colour mood palette.
- **Source:** history.md 2026-09-28 world/palette entries.

## 2026-09-27 — Play Mode verification; builds deferred

- **Context:** Bootstrap implementation.
- **Decision:** Verify in Editor Play Mode (tests + normal-speed pointer smokes) via the Unity Pipeline CLI with explicit project path. Player builds deferred to the user. Serialized scene references; no global singletons.
- **Why:** User instruction.
- **Source:** history.md 2026-09-27 bootstrap entries.
