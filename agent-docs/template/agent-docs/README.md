# Agent Docs — <codebase-name>

_Last reviewed: YYYY-MM-DD_

Durable knowledge about the **<codebase-name>** codebase, accumulated by agents
(and humans) across **every** work item that touches it.

Unlike a work item (spec, bug, PR) — which is scoped to one unit of work and then goes quiet —
this folder is **long-lived**. It is the shared memory of the codebase inside this
repo. Many projects and work items can map to the same codebase; they all read
from and contribute to this one folder.

> **Scope test — does it belong here?**
> Write it here only if it will **still be true after the current work item ships**
> (architecture, a decision + its rationale, a term, a recurring pitfall). Keep
> item-specific tasks, findings, and TODOs in the work item instead.

## How to use this folder

**Before** planning work whose _Code repository_ (or _Codebase_) is this codebase:

1. Read this README, then **only** the files and topic folders your task touches —
   use the "Read when" column. Don't bulk-load the folder.
2. Note anything that contradicts the current task — surface it, don't silently
   work around it.

**As** you work, when you learn something durable, update the matching file:

| File                         | What goes here                                                         | Read when |
| ---------------------------- | ---------------------------------------------------------------------- | --------- |
| [overview.md](overview.md)   | Domain, architecture, where things live, how the code repo builds/runs | Planning or changing code |
| [decisions.md](decisions.md) | Dated decision log — what was decided and **why** (ADR-lite)           | A choice may revisit an earlier one |
| [glossary.md](glossary.md)   | Project/domain terms and acronyms                                      | A term is unclear |
| [gotchas.md](gotchas.md)     | Pitfalls, sharp edges, lessons learned the hard way                    | Before touching the area it names |

_Optional, when the folder also tracks one project's active work:_ `CONTEXT.md`
(current state ≤ 40 lines, overwritten each session — read first), `learnings.md`
(agent-workflow lessons), `history.md` (archived dated log — never load by default;
search it for a specific past event).

Keep entries short and dated where relevant. Prefer editing an existing entry over
appending a near-duplicate. If something is now wrong, fix or remove it. Bump the
_Last reviewed_ date at the top of any file you touch, so stale knowledge is visible
at a glance. Create a file only when it has content — no empty scaffolding.

## Topic areas

_Delete this section if the codebase is small enough for the files above._

Once a system starts crowding the top-level files, give it a **topic subfolder** with
the same file set (`overview.md` / `decisions.md` / `glossary.md` / `gotchas.md`, plus
concept files where it warrants splitting) and list it here. The files above then
hold **cross-topic** knowledge only; topic-scoped lessons go in the topic's own files.
Read the relevant topic folder before working in that area.

| Topic | Scope |
| ----- | ----- |
| _none yet_ | |

## Code repository

- **Repo:** <repo name; its local path is per machine — plain text, never a link>
- **Codebase slug:** <lowercase-kebab-name, matches this folder name>

## Work items using this codebase

| Work item  | Location |
| ---------- | -------- |
| _none yet_ |          |
