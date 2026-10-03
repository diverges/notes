# Unity knowledge folder instructions

Scope: this folder and all its domain subfolders contain reusable Unity knowledge for solo developers and small production teams. Follow the shared repository guidelines as well.

These are personal reference notes for the user as well as agent context. Explain concepts in plain language, include small useful examples, and state when a practice is worth using. Do not reduce substantial subjects to agent checklists. Give large, distinct subjects their own Markdown files with independently readable sections.

Before writing or changing Unity C# code, read this root’s `best-practices.md` and the general C# `best-practices.md`.

## Choose the right domain

| Location | Purpose |
| --- | --- |
| `README.md` | Collection-wide table of contents: query terms, domains, files, and sections |
| `best-practices.md` | Concise Unity C# rules required before implementation |
| `programming/` | C# conventions, design patterns, lifetimes/async, and engineering foundations |
| `project/` | Repository organization, packages, assets, prefabs, scenes, and imports |
| `production/` | Verification, profiling, builds, automation, and release workflow |
| `design/` | Player intent, tuning, authoring, and playtest evidence |
| `knowledge-management/` | Note structure and selective agent context |
| `gotchas.md` | Problems observed during actual coding or implementation only |

Paths above are relative to the Unity knowledge root. Use `README.md` for individual file destinations; do not duplicate its full table of contents here. Keep the concise coding best practices and gotchas at the root; broader production defaults belong in `production/production-defaults.md`. The `project/` domain describes reusable Unity project practices; game-specific memory still belongs in the shared `agent-docs/projects/` directory.

Keep game-specific requirements, active work, and decisions under the existing `agent-docs/projects/<canonical-game>/` folder. Unity gotchas are the explicit exception: record them here with their originating project and version. Do not duplicate their entries elsewhere.

## Read and update efficiently

1. Read this file and the topic directory. Choose only the relevant file and sections; do not load the whole collection.
2. Search for an existing section before writing. Update it rather than adding a competing explanation or another process document.
3. Use concise, self-contained sections with descriptive headings. State essential context, the finding or recommendation, why it matters, and evidence limits directly. Put website links only in a final `References` section, labeled by subject. Preserve useful source findings in the body; references must be optional. Keep local navigation in the table of contents.
4. Date consequential findings and corrections. Preserve useful reasoning like a diary, without transcripts or routine activity logs. Distinguish researched recommendations, observed behavior, and untested hypotheses.
5. Keep related concepts together within the existing domain. Create a new file only for a distinct topic that no longer fits an existing note; do not create placeholder folders or split every observation into a file. Create a new domain only when existing domains do not fit populated subjects.
6. Update the root `README.md` when a file is added, moved, renamed, or changes scope. Keep its query terms useful; maintain one collection-wide table of contents. Check changed links and headings, and ensure the edited section makes sense without opening another note.

## Record implementation gotchas

Only actual coding or implementation can produce an entry in `gotchas.md`. Do not seed it from research, generic best practices, note queries, or documentation changes.

Use a short dated section containing project/version, task and reproduction, observed result, cause or labeled hypothesis, correction/workaround, verification actually performed, and unresolved status. Update the same entry as evidence changes. Leave the findings empty until qualifying work occurs. Consult matching entries when relevant to implementation; do not read the whole log by default.

## Maintain these instructions

Keep operational folder rules here. The other process notes explain rationale and examples; do not require reading them for every edit. Change instructions when the user changes the convention or observed work demonstrates a need, not after every query.
