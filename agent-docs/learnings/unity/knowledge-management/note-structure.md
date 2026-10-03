# Structuring Unity software notes

Updated: 2026-09-27
Status: Agreed organization for solo and small-team Unity knowledge; technical recommendations require project verification.

## Put knowledge where it belongs

Reusable Unity concepts live in `agent-docs/learnings/unity/`. Each game's current state and findings live in `agent-docs/projects/<canonical-game>/`, using the same project identity across chats and worktrees. Do not create a second project named after a research session.

Use `CONTEXT.md` for essential constraints and current state, `decisions.md` for consequential choices, and `learnings.md` for general evidence-backed discoveries. Unity implementation gotchas have one dedicated home: `agent-docs/learnings/unity/gotchas.md`. Keep related entries in sections. Existing repository documentation remains authoritative; summarize the relevant fact in the note and identify its source without duplicating the whole document.

## Keep project context brief and understandable

Record the repository identity, game intent, team/scope, exact Editor version, relevant platform/package constraints, key entry scene, and important state ownership. Mark unknowns. Identify authoritative configuration such as `ProjectSettings/ProjectVersion.txt` and the package manifest rather than copying their contents.

Summarize current behavior, last verification and its environment, unresolved risks, and concrete next steps. State the essential meaning of a decision directly; a link alone does not explain the current state. Keep detailed history in dated topic entries rather than letting context grow indefinitely.

## Write small diary-style sections

Each consequential entry should explain the situation, what was learned or decided, why, evidence and scope, and what remains uncertain. Give it a descriptive heading and date. Keep observations distinct from interpretations and proposed practices distinct from verified behavior.

When a finding changes, add a concise dated correction or mark the earlier conclusion superseded instead of erasing meaningful history or leaving contradictory advice. Add gotchas only after actual coding or implementation exposes a problem; record the project/version, reproduction, observation, cause or hypothesis, correction, and verification in the dedicated file. Do not seed it with research, hypothetical pitfalls, or note-query issues.

Do not record routine activity or entire conversations. Use multiple sections within a focused topic; split into separate files only when distinct subjects or navigation difficulty justify it. Short repetition of essential context is acceptable. Repeating large explanations or mutable settings is not.

## Feature and bug records

Use the existing local templates in `agent-docs/template/` when creating these records. A feature record retains problem, proposal/scope, use cases, observable requirements, scenarios, and tasks. A bug record retains problem, reproduction/environment, observed result, proposed correction, verification, and tasks.

For Unity, include the applicable player intent, affected scene/prefab or system, tunable parameter meanings, state reset/persistence rules, and evidence. For example, a checkpoint feature needs to say what happens on activation, death, and reload; naming a script or linking a save note does not establish those rules. Exact numeric defaults should stay in the authoritative assets unless a dated measurement is necessary to explain the decision.

## Read sections as needed

Use the shared index and Unity topic directory to choose a file, then inspect headings and read the relevant section. Keep the file's scope and applicable constraints visible. Open sources or other notes only to resolve a concrete gap or verify current implementation details.

Keep local navigation links in directories. Put website links only in a final `References` section, labeled by subject. Explain each useful finding, its reason, and relevant limits in the body so it remains useful if the source disappears. Preserve research dates and version scope. Remove repeated explanations and source-review narration; keep detail that changes a decision.
