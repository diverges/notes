# Unity best practices

Updated: 2026-09-27
Scope: solo and small-team production games. These are researched defaults, not rules validated in our games. Check version-sensitive details against the project's Editor and packages.

## Project setup and reproducibility

Record the exact Editor version, target platforms, relevant packages, and build prerequisites. Version source assets with their `.meta` files, project settings, package manifest, and package lock file; exclude generated caches such as `Library` and `Temp`. Verify onboarding or build-environment changes using a separate clean checkout.

## Code and system ownership

Prefer readable components and explicit dependencies. Extract plain C# rules when independent testing or reuse makes the boundary useful. Give important systems a clear owner, lifetime, initialization path, and reset/disposal behavior. Keep Editor tooling separate from runtime code. Add interfaces, assemblies, event systems, or frameworks for a demonstrated need.

## Configuration, runtime state, and saves

Distinguish authored definitions, mutable per-run state, and saved player progress. Use component fields for local settings and consider ScriptableObject assets for shared definitions. State explicitly what resets after death, scene change, a new session, or a new game. Define durable identifiers and migration behavior when saved data must survive releases.

## Assets and serialized changes

Treat scenes, prefabs, Inspector assignments, and asset metadata as part of the program. Preserve asset/metadata pairs during moves. Inspect existing content before changing serialized fields and verify preserved values and references after Unity imports the change.

## Design and iteration

Write the intended player experience alongside observable acceptance scenarios. Document where important settings are authored, their units, and relevant interactions. Preserve reasons for deliberate exceptions such as input buffering. Build custom authoring tools when repeated errors or iteration cost justify their upkeep.

## Testing and shipping builds

Match verification to the risk: automated cases for important rules, Play Mode scenarios for component integration, import/reference checks for content changes, and target Player runs for platform behavior. Record environment, scenarios, results, and unverified areas. Test meaningful failure modes instead of pursuing coverage quotas.

## Performance and complexity

Set budgets from target hardware and the intended experience. Reproduce a representative workload, profile it, identify the bottleneck, and compare before/after results. Record device, build settings, scenario, frame times, and relevant memory behavior. Use pooling, jobs, ECS, or asset-loading infrastructure when a measured or established requirement warrants them.

## 2026-09-27: How this guidance evolves

These defaults consolidate the initial production research into independently readable sections. They are intentionally proportionate to a small team. Revise recommendations when implementation evidence supports a change. Record actual implementation gotchas separately in the dedicated gotchas.md; this guide is not an incident log.

## References

These recommendations synthesize Unity documentation on [asset metadata](https://docs.unity3d.com/6000.0/Documentation/Manual/AssetMetadata.html), [ScriptableObjects](https://docs.unity3d.com/6000.0/Documentation/Manual/class-ScriptableObject.html), [package lock files](https://docs.unity3d.com/6000.0/Documentation/Manual/upm-conflicts-auto.html), and [target-device profiling](https://docs.unity.com/en-us/engine/6000.5/manual/analysis/profiler/profiling-applications/profiling-target-device). Sources establish engine behavior; the proposed production workflow has not been tested in our games.
