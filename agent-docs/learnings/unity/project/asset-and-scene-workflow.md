# Unity asset and scene workflow

## 2026-09-27: Scope and evidence

These notes propose an authoring workflow for solo developers and small teams. Engine behavior is sourced to Unity 6.0; examples and decision rules are recommendations. That research inspected no project content; the dated implementation guidance below has separate evidence. The aim is to make content changes understandable and repeatable without building an elaborate content framework first.

## Make reusable gameplay objects prefabs

Recommendation: put an object in a prefab when its structure or behavior needs a reusable source of truth. An enemy prefab can own its collider, visuals, behavior components, and configured references; a level scene then owns where that enemy appears. Keep the split understandable: prefab for the reusable object, scene for its placement and level-specific relationships.

A scene instance override should have a clear reason, such as a particular patrol route. A change intended for every instance belongs in the appropriate source prefab. Before applying overrides, inspect the changed properties and the destination asset. Applying a temporary level adjustment globally is a different action from tuning one encounter.

For collaboration, review both the content change and the ownership choice. A designer should be able to answer whether changing a speed value affects this instance, an enemy family, or every enemy. Naming and Inspector organization help, but cannot replace a clear source of truth.

## Use variants when inheritance matches the content

Unity prefab variants inherit from a base prefab, and their overrides take precedence over base values. A variant can itself be a base for another variant.

Recommendation: use a variant for a recognizable family relationship. `Guard` and `ArmoredGuard` may share movement, targeting, and hierarchy while changing armor and equipment. Keep that relationship shallow enough that an author can find the effective value quickly. There is no universal correct depth; repeated difficulty tracing values is a reason to simplify.

If the only differences are numbers, a separate configuration asset may be easier to tune than many prefab variants. If variants repeatedly replace most of the structure, separate prefabs or a few composable child prefabs may better describe the content. Pick the representation that makes the intended editing operation obvious, rather than minimizing the number of files.

When updating a base prefab, inspect a representative variant and a real scene instance. The purpose is to check whether the shared change and existing overrides still express the desired behavior. A text diff alone does not demonstrate correct visual layout or gameplay.

## Give scenes an explicit responsibility

Unity supports editing multiple scenes together, including workflows for large worlds and collaboration. This capability does not require every small game to use additive loading.

Recommendation: start with scene boundaries that match an actual lifetime or editing need. A small level-based game might have a menu and one scene per level. Introduce a persistent bootstrap scene when services truly need to survive level changes. Split level environment and gameplay authoring only when parallel editing or loading behavior makes the extra coordination worthwhile.

Write down who loads each scene, who unloads it, and who owns persistent objects. If a level can be launched directly in the Editor, provide a deliberate setup path rather than silently depending on a previously opened menu. Check both normal startup and direct level iteration when implementing that workflow.

For example, a puzzle room should reset its puzzle state when restarted while retaining account settings. That requirement establishes a boundary between level state and application state before any specific manager or scene architecture is chosen.

## Treat import settings as authored content

Unity metadata stores importer settings as well as asset identity, so preserving the source file without its metadata does not preserve the complete authored asset. Move assets with their metadata, preferably through Unity's Project window.

Recommendation: define import expectations by content role: UI sprite, world texture, music, short sound effect, animated character. Record only the settings that materially affect the game, such as units, texture quality, or audio loading decisions. Test a representative asset on the intended platform before imposing a default on a large collection.

Unity presets can reuse property settings and establish default import settings; they are Editor authoring tools and have no runtime effect. Use them once a repeated choice is stable. A folder name can guide an import rule, but changing that rule should be reviewed as a content change, including any reimported assets.

## Review content in small, recoverable changes

Recommendation: avoid combining mass asset moves, prefab restructuring, and gameplay behavior changes in one patch. Separate work makes it easier to identify whether a broken reference, layout change, or rule change caused a regression. Agree on temporary ownership when two people would otherwise edit the same scene or binary source file.

For a meaningful content change, keep a short record of its intent and actual checks: affected prefab or scene, expected behavior, representative scenario, and observed result. Useful checks include missing-reference inspection, import completion, a Play Mode run, and a target build when the content is platform-sensitive. Choose checks from the change; do not claim that opening a scene proves every variant is correct.

## 2026-10-01: Preserve asset contracts

Guidance derived from FirstLight implementation on Unity 6000.3.20f1 and Blender 4.0.1; incident details remain in the Unity gotchas log.

Keep placement and gameplay on stable roots, with visual animation beneath them. Bind scene-specific references on scene instances, not prefab assets.

Treat units, axes, pivots, companion filenames and import settings as contracts. Inspect imported transforms and bounds before overriding them. Preserved GUIDs alone do not prove dependencies survived.

After migrations, validate exact references, required component counts and removal of obsolete content; save and reopen representative assets. Reject missing required assignments rather than silently retaining old values. Register destination folders before batching dependent moves.

## References

- [Unity 6.0: prefab variants](https://docs.unity3d.com/6000.0/Documentation/Manual/PrefabVariants.html)
- [Unity 6.0: multiple scenes](https://docs.unity3d.com/6000.0/Documentation/Manual/MultiSceneEditing.html)
- [Unity 6.0: asset metadata](https://docs.unity3d.com/6000.0/Documentation/Manual/AssetMetadata.html)
- [Unity 6.0: presets](https://docs.unity3d.com/6000.0/Documentation/Manual/Presets.html)
