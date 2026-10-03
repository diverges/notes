# C# learning collection instructions

Scope: this folder and its subfolders contain general C# and .NET coding notes for the user and future agents. Follow the core repository guidelines.

Before writing or changing any C# code, read `best-practices.md` at this collection root. For Unity code, also read the Unity C# conventions.

## Structure and ownership

- `best-practices.md`: concise coding defaults to read before any C# implementation.
- `README.md`: one collection-wide table of contents mapping query terms to files and sections.
- `fundamentals/`: language behavior, types, collections, and data handling.
- `design/`: conventions, API contracts, object design, and pattern selection.
- `runtime/`: asynchronous execution, resource ownership, and concurrency.
- `quality/`: tests, debugging, and verification practices.

Keep Unity lifecycle, serialization, Editor APIs, and platform integration in the Unity collection. A C# example can use game concepts, but should not require Unity. Project-specific decisions belong in canonical project memory.

## Read and write

Read the table of contents, choose relevant sections, and expand only for an actual information gap. Search existing notes before adding one. Keep each section concise and self-contained: explain the concept, useful example or decision rule, rationale, and limits. Put website links only in a final `References` section, labeled by subject. The body must preserve the useful source findings without requiring those websites. Keep local navigation in the table of contents.

Separate C# language support from .NET library and runtime support. State required features or packages for examples; do not assume a new API exists in every host. Label examples untested unless compilation or execution was actually performed. Recommendations are not language requirements.

Group substantial topics by domain, update the table of contents after additions/moves, and check links and code fences. Preserve meaningful dated corrections rather than transcripts. Do not duplicate the table of contents here or create empty domain folders.

## Implementation findings

Create a dedicated root `gotchas.md` only when actual coding or implementation reveals a qualifying finding, or when explicitly requested. Include project/version, reproduction, observed behavior, cause or hypothesis, correction, and verification/status. Research and documentation work do not qualify. Do not duplicate Unity incidents here; keep an incident in the collection matching its cause.
