# Unity engineering foundations

Updated: 2026-09-27
Status: Research synthesis for solo and small-team production games; the dated implementation guidance below has separate project evidence.

## Start with ownership and useful boundaries

Recommendation: use components for scene integration, input, physics, animation, and presentation. Extract plain C# rules when independent testing or reuse earns the extra boundary: inventory transactions, damage rules, progression, or save transformations are candidates. A small, readable MonoBehaviour is not automatically a design problem.

Unity's 2019 architecture article illustrates separating growing simulation logic from Unity presentation. It is practitioner guidance, not an API contract or a reason to force every game into the same scene-loading model.

For each important system, record who creates it, who calls it, what state it owns, and when it resets or releases resources. Avoid implicit initialization chains spread across unrelated components. When ordering matters, make that dependency explicit and verify startup, disable/re-enable, scene reload, and return-to-menu behavior as applicable.

| Choice | Proportionate default | Reason to adopt more structure |
| --- | --- | --- |
| Dependencies | Inspector references or explicit construction for a small known graph | Repeated substitution, testing, or lifetime complexity may justify interfaces or a container |
| Communication | Direct calls for known collaborators; events for actual notifications | Independent listeners can justify events; document subscription ownership and cleanup |
| Global access | Clearly owned scene or application service when needed | Add global lifetime only for a real cross-scene requirement; test reset and duplicate creation |
| Rules | Keep local behavior together; extract independently meaningful rules | Branching rules or difficult test setup justify plain C# units |
| Modules | Few meaningful boundaries | Dependency control, compile time, or ownership may justify more assemblies |

These are recommendations. Avoid a mandatory interface per class, an event channel per interaction, or a service framework before the problem exists. A pattern is useful when it makes ownership, change, or verification simpler.

## Separate configuration, runtime state, and saves

Documented behavior: ScriptableObjects hold data independently of scene components and can share asset data. Editor asset persistence is different from saving progress in a deployed Player.

Recommendation: distinguish three responsibilities even if the initial implementation is small:

| Responsibility | Example | Question the notes must answer |
| --- | --- | --- |
| Authored configuration | Enemy definition or movement settings | Which asset or component is authoritative, and who edits it? |
| Runtime state | Current health, cooldown, current checkpoint | Who owns this instance, and when does it reset? |
| Persistent player data | Unlocked levels or saved inventory | What format/identifier survives releases, and how are older saves handled? |

Use serialized fields for local settings. Consider ScriptableObjects for shared definitions or independent authoring. Mutable ScriptableObject systems are possible, but need deliberate ownership and reset rules. Do not assume editing a shared definition is a safe way to change one instance's health. Asset persistence alone is not a save system.

For save-sensitive features, decide stable identifiers, supported schema versions, missing/corrupt-data behavior, and migration tests before shipping changes that depend on them. This is our production recommendation, not a built-in Unity save contract.

## Keep code boundaries aligned with dependencies

Recommendation: separate runtime code, Editor tooling, and tests where useful; introduce feature assemblies only when their benefit is concrete. Moving scripts between assemblies is a dependency change and needs compilation and asset validation.

Documented behavior: custom assembly definitions have reference rules and cannot form cyclic dependencies. This source is Unity 6000.6; check the game's own version before changing assembly settings.

Feature-oriented organization often keeps related scripts, prefabs, and tests easier to find. Keep shared infrastructure small and purposeful. Existing repository conventions take precedence over introducing a new folder taxonomy during an unrelated fix.

## Treat serialized content as part of the program

Documented behavior: asset metadata stores identity and import settings. Losing or replacing metadata can break references; moving assets inside Unity preserves the associated metadata, while external moves must preserve both files.

Documented behavior: Unity serializes supported fields and types; ordinary .NET serialization assumptions are insufficient. `FormerlySerializedAs` supports preserving values for field renames, not arbitrary type/schema migrations.

Recommendation: review the script, prefab, scene, and configuration changes together. A renamed field, changed component, or modified prefab can affect existing content even when C# compiles. Inspect representative assets before changing serialized data, choose an explicit migration when needed, import in Unity, and verify existing content afterward. Prefer supported Editor operations for complex asset edits. Successful text edits alone do not establish intact scene wiring. Verify preserved values and references in representative existing assets, then exercise the affected behavior in its actual scene or prefab. Record the Editor version and any untested content.

Before adopting pooling, ECS, Addressables, additive scene loading, or a dependency framework, record the actual problem, simpler alternative, expected benefit, and how that benefit will be measured. Those tools solve different problems; their presence is not evidence of production readiness.

## 2026-10-01: Respect lifecycle and update boundaries

Guidance derived from FirstLight on Unity 6000.3.20f1; incident details remain in the Unity gotchas log. Initialize engine resources in a supported lifecycle callback; cleanup must tolerate partial initialization and destroyed dependencies.

Transforms, physics bounds, UI layout, skinning and navigation may update at different points. Before consuming changed state, identify which subsystem must process it; synchronize or wait for that specific boundary. Avoid arbitrary delays or unconditional per-frame synchronization. Test immediate transitions as well as steady operation.

## References

- [Unity: architecture as a project scales](https://unity.com/how-to/how-architect-code-your-project-scales)
- [Unity 6.0: ScriptableObject](https://docs.unity3d.com/6000.0/Documentation/Manual/class-ScriptableObject.html)
- [Unity: assembly references](https://docs.unity.com/ko-kr/engine/6000.6/manual/scripting/compilation-and-code-reload/script-compilation/assembly-definition-files/assembly-definitions-referencing)
- [Unity 6.0: asset metadata](https://docs.unity3d.com/6000.0/Documentation/Manual/AssetMetadata.html)
- [Unity 6.0: serialization rules](https://docs.unity3d.com/6000.0/Documentation/Manual/script-serialization-rules.html)
- [FormerlySerializedAs API](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Serialization.FormerlySerializedAsAttribute.html)
