# FirstLight game code review

Date: 2026-10-02. Project: `D:\Repos\FirstLight`; Unity 6000.3.20f1.

## Scope and outcome

Reviewed project-owned game, Editor and Play Mode test code. Third-party packages were excluded. The user authorized implementation during the review and an independent tester review.

Production code now uses two assemblies: `FirstLight.Runtime` and Editor-only `FirstLight.Editor`. `FirstLight.PlayModeTests` remains separate so NUnit/test code stays outside ordinary player builds. Title, museum and isolated art prototype scripts moved into Runtime with their original script metadata; the Title and ArtPrototype assemblies were removed. All 96 existing game/Editor script metadata files were byte-identical after moves.

Runtime folders and namespaces use Actors, Actions, Combat, Crafting, Items, Progression, Rendering, UI and World, with isolated Title, Museum and ArtPrototype areas. All project-owned C# under Assets is inside Runtime, Editor or Tests. No additional service framework or assembly split was introduced. Existing HUD partial files retain their feature responsibilities.

Private game fields use camelCase. Renamed serialized fields retain their old names through `FormerlySerializedAs`; namespace moves retain script GUIDs and type migration annotations. Migration aliases were checked against original syntax for all 261 serialized fields, including grouped declarations and the historical `_runtimeRoot` alias. Game, Editor and test formatting now uses explicit conditional/loop braces, separate field declarations, explicit private helpers, fields before properties/operations and ordered Unity callbacks. Editor-only test branches were parsed with their active symbols. Reflection/property strings in tests and authoring tools were updated.

## Lifecycle and ownership fixes

- Disabled, paused and unready enemies no longer advance through manually invoked Tick methods.
- Living queens restore their navigation obstacle after their ability is re-enabled. Defeated queens retain a disabled obstacle. Acid-patch cleanup still invalidates queued hazard damage.
- Enemy attack animation duration uses running elapsed time; paused/unready sessions freeze state transitions as well as Animator speed.
- Enemy death always clears feedback, disables its collider and hides its visual, including when loot preparation throws. The existing exception contract is preserved. Prepared visuals are reused only while their item and variant still match; changing a variant before activation rebuilds the visual.
- Collected runtime loot is removed from the world service and destroyed; authored pickups remain available for reset. Prepared loot visuals are reused at activation rather than instantiated twice.
- Font ownership moved from a mutable global cache into each UI owner's RetroUi instance. Font assets loaded through Resources are borrowed, not destroyed. Animator/shader identifiers and raster fog color are instance values; palette colors are computed values. Authored runtime code has no static fields. Stateless helper methods and value-type operators remain static; this is a restriction on shared runtime state, not literal elimination of the static keyword.

## Test-suite review

The tester added regressions for disabled manual actor ticking, queen ability toggles, death cleanup when loot placement fails, and runtime pickup disposal. Cleanup now releases raster native textures and temporary scenes when assertions fail, restores borrowed camera state, destroys owned temporary cameras, resets sessions, and clears retained temporary-object lists. Test private fields and statement/brace layout were normalized with the game code.

Fresh final suite result: **284/284 passed**, zero failures, skipped or inconclusive, in 213.21 seconds. An intermediate full run passed 278/284; five stale nested test property paths and one prepared-variant cache regression were corrected. All 53 focused ground-item, combat, player-state and minimap cases then passed.

## Verification

Final corrected source compiled in the live Unity Editor with no C# errors. A separate probe resolved 67 literal/dotted Inspector paths against 20 game authoring types; dynamic field-name generation and GUI layout were not exercised. A prior fresh test run was cancelled after the independent migration audit identified conflicting grouped-field aliases; that interrupted run is not a verdict.

Forced reimport of 78 game data assets succeeded. A live Editor probe compared 477 saved scalar/reference values with their original YAML values, checked 23 prefabs and 12 scenes for missing scripts, and compared 36 saved lighting-stage scalars. All checks passed. The probe used preview scenes and did not save authored assets or scenes. All 96 pre-existing game/Editor script metadata pairs were preserved byte-for-byte.

After the passing run, the Editor returned to stopped Play Mode. Expected scene-unload error logs from passing lifecycle tests were acknowledged and cleared; current console errors and warnings are zero, compilation failed=false and compiling=false.

No standalone Player build, profiler capture, allocation benchmark or human gameplay/visual acceptance was performed. Passing lifecycle tests does not establish platform performance.

## Remaining review findings

- Loot placement failures still propagate through the combat batch and discard the rolled delivery. Corpse cleanup is now guaranteed, but a retry/fallback policy remains a separate gameplay decision; do not call reward delivery fully repaired.
- Title, museum and isolated art prototype components retain public serialized tuning fields. They need a focused authoring API/Inspector pass before claiming all project-owned code meets encapsulation conventions.
- Stateless helper methods, utility queries and operators remain static. Literal zero-static usage would need explicit API changes, including replacing the C# addition operator for CombatStats. No mutable static runtime state remains.
- Animation pause behavior received source review; the new regression cases do not directly exercise Animator clip transitions.

The current size does not justify a generic action framework, event bus or additional production assemblies. Further work should follow concrete ownership or reliability failures rather than splitting files to meet an arbitrary line limit.
