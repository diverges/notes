# Project memory

Each project's durable agent memory lives in `agent-docs/projects/<project-name>/` in the shared root. Specs and other human-facing documents live separately in the notes repository's root-level `projects/<project-name>/` (see the [project documents guide](../../projects/README.md)). Both use the same canonical name.

## Select the project

- Match the user's repository/project name, or the current repository's identity, against existing folder names and the identity line in each `CONTEXT.md`. Ignore case, spacing and hyphens; reuse the existing folder (First Light and the FirstLight repository both use `firstlight/`). Ask only if several identities remain plausible.
- Branches, worktrees and chats for the same project share one folder.
- New folder: stable, lowercase, hyphenated name. Distinguish same-named repositories with an owner prefix. Link it from the shared index.
- Paths: the shared root's location differs per machine; resolve it through the profile's `agent-docs` link and use absolute paths for shell reads and writes. Markdown links between notes stay relative. Never link into a code repository; name its files as plain text.

## Folder layout and when to read

Start each folder from [the agent-docs template](../template/agent-docs/README.md). Create a file only when it has content.

| File | Holds | Read |
| --- | --- | --- |
| `README.md` | Identity, then every file in the folder with one line on what it holds and when to read it | First, always |
| `CONTEXT.md` | Current state only: goals, state, open reviews, next steps. ≤ 40 lines | Second, always |
| `overview.md` | Architecture map: systems, ownership, where things live, build/test entry points | Planning or changing code |
| `decisions.md` | Dated decisions: context, decision, why, source | When a choice may revisit an earlier one |
| `glossary.md` | Project terms | When a term is unclear |
| `learnings.md` | Agent-workflow lessons: context, evidence, action, status (`tentative`, `validated`, `superseded`) | When the lesson's situation recurs |
| `history.md` | Archived dated log | Never by default; search it for a specific past event |
| `<topic>.md` | Reviews, research, concept notes | When the task touches the topic |

## During a session

1. Read `README.md` and `CONTEXT.md`, then only what the task touches. Check that old assumptions still apply.
2. Record decisions and reusable findings while the evidence is at hand, with enough of it for a later session to verify or challenge them.
3. Before the final response or a handoff, **rewrite** `CONTEXT.md`: current state, checks actually performed, unresolved issues, next steps. Never append; move dated detail worth keeping to `history.md`. A session with no durable change needs no entry.
4. Re-read notes before editing, preserve unrelated content, and replace stale statements instead of appending contradictions. If notes aren't writable, report it and give the proposed update.

## Specifications

- New specs default to root-level `projects/<project-name>/specs/`, not the code repository. An explicit user destination wins; existing repo-local specs don't set the default.
- Use `spec.md`, `one-pager.md` or `bug.md` from [the templates](../template/README.md).
- Name new specs `NNN-<name>.md` (or a `NNN-<name>/` folder for supporting files): three-digit, continue after the highest number in shared or repo-local specs, start at `001`. Never renumber.
- Edit the authoritative copy in place; no competing copies. Spec status lives only in the project's `specs/README.md`.

## Implementation gotchas

Only actual coding or implementation work produces a gotcha; research, queries and doc edits don't. Unity findings go in `agent-docs/learnings/unity/gotchas.md` (with originating project and version); others in the project's `gotchas.md`. Each dated entry has: trigger and symptom, cause (or labelled hypothesis), correction, evidence and scope, status. Search for an existing entry first; mark disproven explanations superseded with a short dated correction.

Avoid transcripts, routine activity logs, credentials and sensitive raw output. Promote a project lesson to shared `learnings/` only when evidence supports wider use.
