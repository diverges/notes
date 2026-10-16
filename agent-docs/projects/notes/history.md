# Shared agent notes

Updated: 2026-10-03

## 2026-10-03: Shared specifications and project selection

Final clarification: generated Markdown, specs, and notepad-style planning default to this notes repository; code-repository documentation should describe the project and be primarily user-authored. User-requested repository documentation edits remain allowed. First Light's 38 spec/index files have now been migrated to root-level `projects/firstlight/specs/`, superseding earlier statements below that no migration occurred. Copy hashes were verified before link repair and source removal.

Further correction on 2026-10-03: load agent context by default, but load existing specs only when the user asks to read, review, update, or work from them. This supersedes earlier instructions below to read relevant specs automatically during project work. Context links alone do not trigger spec loading. The root guidelines and both project guides were updated consistently.

Correction on 2026-10-03: the user also writes specs, so the earlier `agent-docs/projects/<project-name>/specs/` destination below is superseded. New specs belong in this repository's root-level `projects/<project-name>/specs/`; context snapshots and agent notes remain in `agent-docs/projects/<project-name>/`. Both use the same canonical project identity, and agents must consult both locations. The root-level project documents guide, global guidelines, memory guide, template usage, and shared index now reflect this separation. Existing specs were not migrated. Checked the updated rules and local navigation targets; future-session compliance remains unverified.

The user selected this notes repository as the default home for agent-maintained specs. Root guidelines, the project memory guide, and template usage now route new feature and bug specs to `agent-docs/projects/<project-name>/specs/`, with supporting folders kept together. Explicit user destinations take precedence; existing specs are updated at their authoritative location until migration is requested. No existing project specs were moved in this session.

New spec files and folders use `000-<name>`: a three-digit, zero-padded sequence and lowercase, hyphenated name. Continue the existing project sequence across shared and repository-local specs, starting at `001` for a new sequence. Updates and migrations preserve existing identifiers.

Project work now explicitly resolves repository/project/repo names against existing folders and context identities, allowing ordinary case, spacing, and hyphen differences. Agents must load applicable instructions, project context, and relevant specs before planning or editing, reusing one canonical folder across branches, worktrees, and chats. The Codex profile's `AGENTS.md` was verified as a symbolic link to this repository's root guidelines. Edited guidance and local navigation targets were inspected; adoption in future sessions remains unverified.

## 2026-10-01: Reusable implementation guidance

Promoted FirstLight lessons into existing Unity asset workflow, engineering and production notes; strengthened placed-animation review in the Blender workflow. Detailed incidents and uncertain causes remain in Unity gotchas. Consolidated global planning rules into concise alignment, incremental delivery and user-feedback guidance. Updated Unity navigation; no new topic files or game changes.

## Identity and goal

Project: the shared guidelines and knowledge repository at `D:\Repos\notes`.

Maintain portable agent preferences, templates, and project memory so sessions across projects can reuse verified context and learnings. [AGENTS.md](../../../AGENTS.md) defines the working rules; [the shared index](../../INDEX.md) lists available knowledge.

## Constraints and decisions

Use the existing `agent-docs/projects/<project-name>/` structure. Keep project memory separate from general lessons, use stable project identities across chats and worktrees, and create files only when there is durable content.

The active shared source on this machine is `D:\Repos\notes`. The global guidelines, project memory guide, and setup skill name this location explicitly. Shared knowledge references must resolve there, even when a chat works in another repository.

## Current state and verification

### 2026-10-01: Basic product team setup

Team default refinement: grounding recommendations and asking about unresolved consequential choices were first added to all four roles, then promoted to shared AGENTS.md at the user's request. The rule distinguishes intended behavior from implementation and preserves routine autonomy and independent progress. Team instructions now reference the shared rule and retain only parent-mediated questions and decision sharing. The global guideline link and installed/source equality were verified, and all four role TOML files parsed; behavioral compliance remains untested.

Created a compact `product-team` skill with four role definitions: PM (product manager), designer, architect, and engineer. The maintained source is `agent-docs/skills/product-team/`, with role TOML files under `references/`. Installed matching copies in `C:\Users\sotol\.codex\skills\product-team` and `C:\Users\sotol\.codex\agents`. Future edits must synchronize these installed copies with the source. Universal guidelines and model settings were not changed.

The parent supplies bounded assignments, coordinates dependencies and file ownership, reconciles recommendations, and checks acceptance criteria. Review requests remain reviews. If named custom agents are unavailable in a runtime, the skill directs the parent to pass the matching role instructions explicitly to ordinary subagents.

Follow-up on 2026-10-01: PM now owns documentation compliance and upkeep when assigned document writes. It reads the actual shared template, reuses authoritative project records, and selects a feature spec, bug record, or GDD as appropriate. It captures scope and decisions, preserves sections and IDs, and reconciles status, task completion, verification evidence, and unresolved acceptance before the parent reports completion. Read-only requests produce proposed updates instead. Updated PM and skill copies were synchronized to the personal profile; TOML parsing, template existence, and installed/source equality were checked. Actual PM behavior with a project remains untested.

All four installed TOML files parsed with their required fields, and all installed files matched the maintained source byte for byte. The bundled skill validator could not run because PyYAML is absent in both available Python runtimes; frontmatter and reference paths were checked separately. Automatic discovery and actual team behavior in a fresh chat remain unverified. First use: `Use product-team to review this feature; return recommendations without editing the project.` Restart Codex if the new skill does not appear.

### 2026-10-01: Git defaults

Shared guidelines now require semantic commit titles with an optional scope and prefer short sentences and bullets for commit descriptions. A description can be omitted when the title is sufficient.

The repository has a root `.gitignore` for operating system metadata, local editor state, temporary files, and `.env` values, with example environment files allowed. Root `.gitattributes` normalizes text to LF, keeps Windows command scripts at CRLF, and treats common media/archive formats as binary. Git rule checks confirmed representative matches and attributes; no existing files were excluded by the ignore rules. Git is initialized on `master`, with commit identity configured, but no commits or remote exist yet. Creating an initial commit and configuring a remote remain separate steps.

The Codex profile's `AGENTS.md` and `agent-docs` symbolic links point to this repository. Both targets and the linked index and templates were verified readable and matching their sources. No global `AGENTS.override.md` was present at that check. The user confirmed that another project loaded the shared guidance.

Project memory guidance now requires substantive sessions to read relevant context and save meaningful state, decisions, and agent learnings. The user reported that another chat incorrectly looked for `D:\Repos\GMTK-Jam-2023\agent-docs`. The guidance now specifies the absolute shared root and requires checking its index before using project notes. These instructions depend on session compliance and filesystem permissions; correct resolution and note writing from another project have not yet been reverified after this correction.

The [Unity production knowledge base](../../learnings/unity/README.md) now contains an initial research synthesis from engineering, game design, and AI perspectives, focused on solo and small-team production games. Reusable guidance lives under `learnings/unity/`; each game's decisions and validation evidence remain in its canonical project memory. The collection reuses the existing feature and bug templates and distinguishes documented engine behavior, recommendations, and project-validated findings. Research includes version-scoped primary sources; no Unity project, Editor behavior, build, or playtest was validated. Apply and refine it against a real project's next substantive task rather than treating it as a universal framework.

The user's AI focus is selective context retrieval through compact, self-contained Markdown. On 2026-09-27, the user corrected the earlier emphasis on cross-note links: related concepts now belong in independently readable sections, preserving findings and rationale as dated diary-style entries. The Unity README is a small topic directory; its agent and note-structure guides describe section-based reading. Essential explanations and warnings stay with the concept. Links support navigation, provenance, and optional detail. Effectiveness and context savings in fresh sessions remain unverified.

Correction on 2026-09-27: the user wants gotchas only from actual coding or implementation, in a dedicated file. Unity findings belong in `agent-docs/learnings/unity/gotchas.md`, with project/version, reproduction, observed behavior, cause/hypothesis, correction, verification, and status. Research and note-query issues do not qualify. The file currently has no findings because this work did not include Unity implementation. Earlier instructions to distribute gotchas among topic notes are superseded.

A self-contained Unity `best-practices.md` covers project setup, code ownership, state and saves, serialized assets, design iteration, verification, and performance. Generic gotcha callouts were removed; the guide contains researched recommendations rather than implementation incidents. No Unity implementation was tested.

The Unity folder now has a concise `AGENTS.md` defining file purposes, section-based reading/writing, maintenance, and implementation-only gotcha capture. The root guidelines and Unity README explicitly direct agents to read it before editing that folder. Operational rules live there; explanatory process notes need only be read when relevant.

Two user-requested research agents added six personal reference notes: C# conventions, gameplay patterns, lifetimes/async, Unity project organization, assets/scenes, and build/release workflow. Each contains focused sections, primary sources, examples or selection criteria, and scope limits. Unity 6.0 is the principal API baseline, not an upgrade recommendation. No code example was compiled or Unity workflow executed; `gotchas.md` remains without findings.

The collection is now grouped into `programming/`, `project/`, `production/`, `design/`, and `knowledge-management/` domains. The Unity root README is the single table of contents, mapping query terms to files and sections. Cross-domain `best-practices.md`, `gotchas.md`, and folder instructions remain at the root. Eleven notes were moved with unchanged content hashes; the instructions and shared index were updated for the new structure.

The core `AGENTS.md` now generalizes these conventions to future learning collections: domain folders for large subjects, a query-oriented root table of contents, concise collection instructions, self-contained human-readable sections, and separate implementation-only gotchas. Small subjects remain single files; no empty scaffolding is required. The project-first rule now explicitly permits designated subject locations such as Unity gotchas.

An engine-independent GDD template lives at `agent-docs/template/game-design-document.md`. It provides a concept-first pass, nine focused design sections, observable rule/scenario prompts, scope boundaries, and design decisions. At the user's request, the dedicated risks/playtest plan and dated playtest-finding entry were removed; decisions and open questions are now section 9. Research and rationale remain in the Unity design domain's `game-design-documents.md`. Indexes and core template selection include it. This is an original research-informed template, not a validated game design; no implementation findings were added.

General C# research now has its own collection at `agent-docs/learnings/csharp/`, as explicitly requested by the user. Its root instructions and table of contents organize eight notes under fundamentals, design, runtime, and quality: types/data, collections/queries, conventions/API design, maintainability pattern selection, boundaries/modules, state/effects, async/resource lifetimes, and testing/debugging. The 2026-09-27 architecture research covers layers, vertical slices, ports/adapters, modular monoliths, functional core/imperative shell, explicit transitions, and command/event distinctions. These are sourced descriptions and proposed selection rules, not a prescribed framework. Existing Unity-specific C# notes stay in Unity. The examples are uncompiled; no tests, benchmarks, or implementation incidents were produced, and no C# gotcha log was seeded.

All eight C# topic notes are listed in their table of contents; local collection links and code fences were checked. Actual compilation, runtime behavior, and future retrieval effectiveness remain unverified.

## 2026-09-27: Self-contained notes and references

Website links now belong only in a final `References` section. Essential findings, examples, reasons, and limits stay in the body; references provide optional provenance. This supersedes earlier guidance permitting inline citations. Local indexes and tables of contents remain navigational. Core and collection instructions now require concise prose and a body-only readability check. Existing research citations were moved to reference sections; this edit did not revalidate the underlying technical research.

## 2026-09-28: Feature use-case diagram

The feature specification template now places a Mermaid UML-style use-case diagram above the use-case headings. It uses flowchart syntax, an actor icon outside a named system boundary, and a rounded use-case node connected by an undirected association. Diagram labels carry the actor identity, replacing the repeated `Actor:` bullet. UC IDs connect diagram nodes to the corresponding sections. The template index entries were updated; source structure and affected local links were checked. The user reports Mermaid rendering works in their VS Code instance; this diagram has not been visually verified there.

Correction on 2026-09-28: the earlier user-story interpretation is superseded. Diagram labels use short actions, with the object implied by the enclosing system or feature: “Create” and “Delete” under “Documents”, or “Pay” under “Bills”. The user's final clarification distinguishes these labels from section headings: use-case and corresponding scenario group headings must use the same short sentence naming actor, action, and object, such as “Player creates a document”. Stable UC IDs connect both forms. This supersedes applying action-only names everywhere. User-story sentences and the original detailed use-case fields remain removed; detailed behavior belongs in requirements and scenarios. The updated source was inspected.

## 2026-10-02: C# readability defaults

The user reported dense agent-written code with too few line breaks, inconsistent names, and inconsistent structure. Existing general C# and Unity convention notes now contain explicit user-selected defaults for naming, Allman braces, logical blank lines, member ordering, simple expressions, and sparse comments. Unity guidance adds serialized-field grouping, callback ordering, Inspector restraint, and safe field renames. The root guidelines route future C# work to both relevant notes; collection tables of contents and the shared index describe the expanded coverage. These preferences guide new and changed code, with coherent project rules taking precedence. No game code was changed, no examples were compiled, and adoption in future sessions remains unverified.

Correction on 2026-10-02: the user explicitly selected camelCase private fields without underscore prefixes for general C# and Unity code. This supersedes the earlier `_camelCase` default. Both convention notes and their examples now use camelCase; `this.fieldName` disambiguates same-named parameters or locals. Existing serialized fields still require safe migration rather than unrelated style-only renames. The root guidelines include this preference. Checked both edited guides for remaining underscore-prefixed identifiers and balanced code fences; examples remain uncompiled.

Addition on 2026-10-02: explicit field ordering now groups general C# declarations as constants, static fields, instance dependencies/configuration, and runtime state. Unity refines this as constants, static fields, serialized references, serialized settings, dependencies/caches, and runtime state. Related fields stay together rather than being sorted alphabetically or by type. Initializer dependencies and Inspector ordering must be preserved. The corresponding tables of contents include field ordering. Checked section presence and balanced code fences; no application code changed.

Correction on 2026-10-02: at the user’s request, the general conventions guide moved from `csharp/design/conventions-and-api-design.md` to `csharp/best-practices.md` and was condensed to nine coding rules. The root guidelines, collection instructions, table of contents, and shared index now require or surface this reading before any C# implementation. The earlier long guide and examples are superseded. Verified the old file is absent, the new file exists, and active navigation/instructions have no references to the old path. No application code changed.

Correction on 2026-10-02: Unity C# conventions moved to the Unity root `best-practices.md` and were condensed to eight rules required before Unity C# implementation. The pre-existing broader production guide was preserved unchanged at `unity/production/production-defaults.md`. Root and collection instructions, navigation, and the shared index now point to these destinations. Checked destination existence and removed old-path references from active instructions/navigation. No application code changed.

Addition on 2026-10-02: the user requested readable Editor-only custom Inspectors for serialized objects. Unity best practices now requires creating or maintaining these for components/assets with authoring controls, using purposeful sections, readable labels/spacing/units, standard controls, secondary foldouts, Editor-only dependencies, and serialized-property editing. Persistence, Undo, and layout checks are required when implemented. Updated the Unity table of contents and checked the new rule appears once. No Inspector implementation or Unity verification occurred in this notes-only change.

## Next steps

In the next substantive session in another project, verify that its context is read or created and updated before the session ends. Check whether that workspace permits writes to the shared notes folder.

## Supporting notes

- [Agent learnings](learnings.md)
- [Project memory guide](../README.md)
