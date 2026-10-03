# C# coding: table of contents

Updated: 2026-10-02
Scope: general C# and .NET coding, independent of an engine or application framework.

## Find a subject

Read [C# best practices](best-practices.md) before writing or changing any C# code. These are personal reference notes as well as agent context. Select a file and read the relevant section. Before editing, read this collection's `AGENTS.md`.

| Domain and question | Note | Sections and search terms |
| --- | --- | --- |
| Fundamentals: what does this data represent and how is it copied? | [Types and data](fundamentals/types-and-data.md) | Values/references; class/struct/record; nullability; equality; invariants |
| Fundamentals: how should I store, query, and transform data? | [Collections and queries](fundamentals/collections-and-queries.md) | List/Dictionary/HashSet; generics; LINQ; deferred execution; snapshots; allocations |
| Coding: what rules apply before writing C#? | [C# best practices](best-practices.md) | Naming; spacing; field and member order; methods; contracts; sparse comments; completion checks |
| Design: which pattern is worth introducing? | [Choosing patterns for maintainable code](design/maintainability-patterns.md) | Change difficulty; selection guide; tradeoffs; incremental adoption |
| Design: how should I organize features and dependencies? | [Boundaries and modules](design/boundaries-and-modules.md) | Layers; vertical slices; ports and adapters; composition root; modular monolith |
| Design: how do I keep state changes and workflows understandable? | [State, decisions, and effects](design/state-and-effects.md) | Functional core; imperative shell; state machines; commands/events; CQS; CQRS; event sourcing |
| Runtime: who owns work and cleanup? | [Async and resource lifetimes](runtime/async-and-resource-lifetimes.md) | Task; I/O vs CPU; cancellation; disposal; exception observation; concurrency |
| Quality: how do I prove behavior and investigate failures? | [Testing and debugging](quality/testing-and-debugging.md) | Boundary cases; xUnit example; deterministic tests; breakpoints; regression evidence |

For a learning pass, start with types, then collections, API design, async, and testing. For a task, read only the matching subject. These notes explain their own examples; following the sequence is optional.

## Compatibility and evidence

Research checked on 2026-09-27 against Microsoft primary documentation and original architectural writing by the cited authors. Pattern selection notes distinguish our recommendations from source descriptions. The examples use established syntax, generally C# 8 or earlier; newer concepts are labeled. Language features, library APIs, and host runtime behavior have separate compatibility requirements. A current documentation page may show newer syntax than the examples here.

All examples are illustrative and uncompiled. No application, benchmark, test suite, or implementation incident was produced. Recommendations have not been validated in one of our projects. There is no seeded gotcha log.

## 2026-09-27: Collection boundary

The user requested C# as its own learning collection. General language and coding knowledge lives here. The existing [Unity collection](../unity/README.md) retains engine-specific C# guidance: serialized fields, components, Unity object lifetimes, coroutine/Awaitable behavior, and Player validation. Do not assume general .NET examples establish Unity compatibility.
