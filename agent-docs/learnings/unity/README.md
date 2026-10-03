# Unity knowledge: table of contents

Updated: 2026-10-02
Audience: solo developers and small teams shipping games; personal reference and agent context.

## Find the right domain

Choose a domain from the question, then a note and its relevant section. Do not read every file. Read `AGENTS.md` before editing this collection; its rules apply to all domain folders.

| Query concerns | Domain |
| --- | --- |
| C# style, patterns, dependencies, state ownership, async | Programming |
| Repository layout, packages, prefabs, scenes, imports | Project |
| Builds, tests, profiling, release checks, rollback | Production |
| Player intent, tuning, authoring iteration, playtests | Design |
| Note structure, agent context, selective retrieval | Knowledge management |

Additive scene migration, untitled scene saves and dirty-scene recovery: see [implementation gotchas](gotchas.md#2026-10-02-saving-additive-scene-migration-and-restoring-a-backup).

Read [Unity C# best practices](best-practices.md) before writing or changing Unity C# code. [Production defaults](production/production-defaults.md) retains broader project, state, asset, verification, and performance guidance. [Implementation gotchas](gotchas.md) records only problems encountered in actual coding or implementation; it includes FirstLight lifecycle, navigation, Pipeline input/capture, large-island path-query limits, deprecated native-query cleanup Flat Kit tree-instancing rendering, explicit Single sprite import, crafting FBX ground orientation stale Pipeline test-status findings, immediate collider-bound synchronization for grid approaches, and moving-enemy terrain offsets/collider replacement/imported pivot axes, batched folder-move registration implicit FBX texture filenames, and snapped props blocking preserved navigation endpoints (Snapping scene props section).

Serialized field renaming, grouped declarations, Inspector paths and inactive Editor test code: see [field migration findings](gotchas.md#2026-10-02-serialized-field-migration-across-grouped-declarations-and-editor-conditionals).

## Programming

For general language and .NET behavior, use the separate [C# learning collection](../csharp/README.md). The notes below retain Unity-specific conventions and integration guidance.

| Note | Sections and query terms |
| --- | --- |
| [Unity C# best practices](best-practices.md) | Required reading; compatibility; field and callback order; Inspector; Editor-only custom Inspectors; ownership; serialization; completion checks |
| [C# design patterns](programming/csharp-design-patterns.md) | Strategy; state machines; observer/events; factories and pools; commands; presenters |
| [C# lifetimes and async](programming/csharp-lifetimes-and-async.md) | Ownership/disposal; subscriptions; coroutine vs Task vs Awaitable; cancellation; threads and failures |
| [Engineering foundations](programming/engineering.md) | Component boundaries; configuration/runtime/save state; serialization; lifecycle and update boundaries |

For a pattern choice, start with design patterns; for who owns or resets state, start with engineering. For callback cleanup or cancelled operations, start with lifetimes and async.

## Project

| Note | Sections and query terms |
| --- | --- |
| [Project organization](project/project-organization.md) | Feature folders; assembly boundaries; version control and metadata; packages and upgrades |
| [Asset and scene workflow](project/asset-and-scene-workflow.md) | Prefab ownership; stable animation roots; import contracts; migration validation; variants; scene lifetimes; content review |

Related workflow: [Blender model generation for Unity](../../workflows/blender-model-generation.md) covers procedural mesh topology, skinning, FBX export, Humanoid import and pose review.

Use this domain for authoring and project structure. Runtime behavior within those assets belongs under programming; the player's intended experience belongs under design.

## Production

| Note | Sections and query terms |
| --- | --- |
| [Production checks](production/production-checks.md) | Clean checkout; verification by failure mode; realistic input and scale; capture/tool evidence; profiling; release checks |
| [Build and release workflow](production/build-and-release-workflow.md) | Build profiles; repeatable build entry points; CI and tests; stripping/Player checks; artifact records and rollback |

For what to verify after a change, use production checks. For how to organize the build and release process, use build and release workflow.

## Design

| Note | Sections and query terms |
| --- | --- |
| [Design and iteration](design/design-and-iteration.md) | Player intent and observable behavior; feature records; tuning and authoring; playtest evidence |
| [Writing game design documents](design/game-design-documents.md) | Research and rationale; concept-first drafting; design rules vs evidence; scope and accessibility |

Reusable artifact: [Game design document template](../../template/game-design-document.md), a self-contained Markdown starting point with a concept pass and expandable design sections.

## Knowledge management

| Note | Sections and query terms |
| --- | --- |
| [Note structure](knowledge-management/note-structure.md) | Note placement; brief project context; diary sections; feature and bug records |
| [Agent context](knowledge-management/ai-assisted-development.md) | File roles; selective section reads; writing findings; manageable file size |

These process notes are for improving the knowledge base, not required reading for ordinary Unity coding.

## Evidence and maintenance

The notes distinguish documented behavior, proposed recommendations, and project-validated findings. Research was checked on 2026-09-27, principally against Unity 6.0 documentation. That baseline is not an upgrade recommendation. Research examples remain uncompiled. The gotchas collection separately records implementation evidence from FirstLight on Unity 6000.3.20f1; no player build or first-time human playtest was performed.

On 2026-09-27, the user requested domain folders and a table of contents. Existing notes were grouped without changing their subject content. Keep this as the single collection-wide contents page, with paths and useful search terms. Topic notes remain self-contained; create another domain only when populated subjects justify it. Keep best practices and implementation gotchas at the Unity root because they span domains.







