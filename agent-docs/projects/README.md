# Project memory

Keep each project's durable agent memory in `agent-docs/projects/<project-name>/` within the shared knowledge folder. This is the existing plural `projects/` directory; do not create a parallel `project/` directory.

## Select the project

Project specs and other human-facing project documents live in the notes repository's separate root-level `projects/<project-name>/` folder. On this machine that is `D:\Repos\notes\projects\<project-name>\`; it is not relative to the current code repository. Keep context snapshots and agent notes here in `agent-docs/projects/<project-name>/`. Use the same canonical name for both folders. Check both locations for applicable instructions and load agent context before working. Load existing specs only when the user asks to read, review, update, or work from them; context links do not trigger loading. See the [project documents guide](../../projects/README.md).

Read the shared index and reuse a matching project folder. Use a stable, lowercase, hyphenated name based on the repository or project. Record the canonical repository URL or project identity in `CONTEXT.md`, with a local path when useful. Distinguish repositories with the same name using an owner or organization prefix. Branches, worktrees, and chats for the same project share its notes.

Resolve the user's repository/project/repo name or the current repository identity against existing folder names and `CONTEXT.md` identities. Ignore case and ordinary spacing or hyphen differences when matching; preserve the existing canonical folder name. For example, First Light and the FirstLight repository use the existing `firstlight/` folder. Read applicable folder instructions and `CONTEXT.md` before planning or editing; load existing specs only when the user asks to read, review, update, or work from them; consult relevant decisions and learnings as needed. Ask for clarification if multiple identities remain plausible rather than creating a duplicate folder.

On this machine, use the absolute shared root `D:\Repos\notes\agent-docs` or its verified profile link `C:\Users\sotol\.codex\agent-docs`. On another machine, resolve the profile's `agent-docs` link as described in the global guidelines. Verify the root and read its `INDEX.md` before selecting a project folder. Use absolute paths for shell reads and writes; relative Markdown links within notes remain appropriate. A missing repository-local `agent-docs` folder says nothing about the shared root. Existing project documentation remains authoritative; link to it and follow any project-specific storage restrictions.

## Read and write during sessions

1. Before substantive work, read `CONTEXT.md` and relevant entries in `decisions.md` and `learnings.md`. Check that old assumptions still apply.
2. If no project notes exist and the work needs durable context, create its folder and `CONTEXT.md`, then link it from the shared index. Create the other files only when there is content to record.
3. Record decisions and reusable findings as they emerge. Include enough evidence for a later session to verify or challenge them.
4. Before the final response or a handoff, update the current state, checks actually performed, unresolved issues, and next steps. Completed work should say so; do not manufacture follow-up tasks. A simple question or session with no durable change needs no new entry.
5. Re-read existing notes before editing, preserve unrelated contributions, and replace stale statements rather than appending contradictory summaries. Report any write-permission blocker and provide an unsaved proposed update.

## What each file contains

### Specifications

When asked to write a spec, use the shared `projects/<project-name>/specs/` directory by default, rather than the code repository. Use the local feature or bug template as appropriate. A standalone spec can be a Markdown file; keep a spec and its supporting documents together in a descriptive subfolder when needed. Preserve existing names and numbering and link active specs from `CONTEXT.md` so later sessions can find them.

An explicit user-requested destination takes precedence. Existing specs in a code repository do not establish the default for new specs. When updating an existing spec, edit its current authoritative copy unless migration is requested; do not create a competing copy. Migrations should preserve supporting files and repair affected navigation links. Contributor and build documentation can remain in the code repository, with a short pointer to shared agent specs when useful.

Name new spec files and folders `000-<name>`, using a three-digit, zero-padded sequential number and a descriptive lowercase, hyphenated name. Examples: `001-player-movement.md` for a standalone spec or `001-player-movement/` for a spec folder. Continue after the highest existing project spec number, checking shared specs and any specs still in the code repository; start at `001` when none exist. Preserve identifiers when updating or migrating existing specs.

### Capture implementation gotchas separately

Record gotchas only from actual coding or implementation work. Research, hypothetical pitfalls, note queries, and documentation organization do not qualify. Use a dedicated `gotchas.md`; Unity implementation findings belong in `agent-docs/learnings/unity/gotchas.md`, with their originating project and version stated. This explicit Unity location is an exception to the usual project-first storage rule. Do not duplicate the entries throughout topic notes or `learnings.md`.

Use a dated, descriptive heading in the dedicated file. Include:

- Trigger and symptom: the actual implementation task, reproduction, and observed behavior.
- Cause: established explanation or explicitly labeled hypothesis.
- Correction or workaround: what to do, including when it does not apply.
- Evidence and scope: observed result, relevant version/environment, and a concise reproduction or source link.
- Status: tentative, validated within the stated scope, or superseded; identify any remaining verification.

Search for an existing entry before adding another. Keep each entry self-contained and scoped to its evidence. An observed failure can be recorded while its cause or fix is unresolved; label that uncertainty. Update the entry as checks establish more. Mark disproven explanations superseded and preserve a concise dated correction. Do not populate the file with generic examples.

Group related concepts into descriptive sections that can be read independently. Split files when scope or navigation becomes unwieldy; avoid both giant logs and a separate file for every observation. Links support navigation and attribution, but should not replace the explanation. Brief restatements of essential context are preferable to dependent entries.

### File roles

- `CONTEXT.md`: project identity, last updated date, goals, constraints, current state, verification limits, and next steps. Explain essential facts directly and identify authoritative sources where useful. Keep this a concise entry point for the next session.
- `decisions.md`: dated consequential choices, rationale, alternatives or tradeoffs, and conditions that would justify revisiting them.
- `learnings.md`: dated agent-specific findings about effective or failed approaches, tool behavior, environment setup, debugging, testing, collaboration, and recovery. For each lesson, record context, evidence, recommended action, applicability, and status (`tentative`, `validated`, or `superseded`). Validation must describe what was actually observed; one success does not prove a universal rule.

Use the feature and bug templates for work that needs those documents, and link them from the context. Avoid transcripts, routine activity logs, credentials, and sensitive raw outputs. Promote a lesson to shared `learnings/` only when evidence supports broader use; retain a link to its project evidence. Turn repeatable, proven procedures into shared workflows.
