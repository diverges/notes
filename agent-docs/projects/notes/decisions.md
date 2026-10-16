# Notes: Decisions

Newest first. Guideline decisions for this notes repository; full narrative in [history.md](history.md).

## 2026-10-16 — Load-on-demand project memory files

- **Context:** Append-only CONTEXT.md diaries (FirstLight ~1,270 lines) were expensive to load every session.
- **Decision:** Each `agent-docs/projects/<name>/` folder uses README.md (index), CONTEXT.md (current state only, ≤ 40 lines, overwritten), decisions.md, optional overview.md/glossary.md, learnings.md, and history.md (archived old diary, not loaded by default).
- **Why:** Cuts default context to the index and current state; durable knowledge moves to purpose-specific files.
- **Source:** 2026-10-16 restructuring request (this session). The project memory guide outside these folders still describes the older model.

## 2026-10-03 — Generated planning lives in the notes repo

- **Context:** Where agent-written Markdown, specs and planning notes belong.
- **Decision:** Default to this notes repository. Code-repo documentation describes the project and is primarily user-authored; user-requested repo doc edits remain allowed.
- **Why:** Keeps agent output out of code repos.
- **Source:** history.md 2026-10-03.

## 2026-10-03 — Specs in root projects/<name>/specs

- **Context:** Specs were first routed to `agent-docs/projects/<name>/specs/`.
- **Decision:** New specs go in the root-level `projects/<name>/specs/`; context and agent notes stay in `agent-docs/projects/<name>/`. Same canonical project name for both. FirstLight's 38 spec files migrated (hash-verified).
- **Why:** The user also writes specs; separates human-facing documents from agent memory. Supersedes the agent-docs specs location.
- **Source:** history.md 2026-10-03.

## 2026-10-03 — Load specs only on request

- **Context:** Earlier rules loaded relevant specs automatically.
- **Decision:** Load agent context by default; load existing specs only when the user asks to read, review, update or work from them. Context links alone do not trigger loading.
- **Why:** Reduces unnecessary context.
- **Source:** history.md 2026-10-03.

## 2026-10-03 — Spec numbering 000-<name>

- **Context:** Naming new spec files/folders.
- **Decision:** Three-digit zero-padded sequence plus lowercase hyphenated name. Continue a project's existing sequence (start at 001 for a new one); updates and migrations keep identifiers.
- **Why:** Stable ordering and references.
- **Source:** history.md 2026-10-03.

## 2026-10-03 — Resolve project identity against existing folders

- **Context:** Chats looked in wrong locations or risked duplicate folders.
- **Decision:** Match repository/project names to existing folders and recorded identities ignoring case, spacing and hyphens; one canonical folder across branches, worktrees and chats. Ask only if several identities remain plausible.
- **Why:** Prevents duplicate memory.
- **Source:** history.md 2026-10-03.

## 2026-10-02 — C# readability defaults

- **Context:** User reported dense, inconsistent agent-written code.
- **Decision:** Condensed rule sets in `learnings/csharp/best-practices.md` (9 rules) and `learnings/unity/best-practices.md` (8 rules), required reading before C# work: Allman braces, logical blank lines, member and field ordering, camelCase private fields without underscore (`this.` to disambiguate), sparse comments. Unity: readable Editor-only custom Inspectors for authored components/assets.
- **Why:** User-selected style; camelCase supersedes an earlier `_camelCase` default; long guides superseded. Coherent project rules take precedence.
- **Source:** history.md 2026-10-02.

## 2026-10-01 — Product-team skill

- **Context:** Need for PM/designer/architect/engineer reviews.
- **Decision:** `product-team` skill with four role definitions; source in `agent-docs/skills/product-team/`, installed copies in the Codex profile must be kept in sync. PM owns documentation upkeep when assigned writes. The rule to ground recommendations and ask about unresolved consequential choices lives in root AGENTS.md.
- **Why:** Reusable team reviews; behaviour in real use still unverified.
- **Source:** history.md 2026-10-01 "Basic product team setup".

## 2026-10-01 — Semantic commits and repo hygiene

- **Context:** Git defaults for this repo.
- **Decision:** Semantic commit titles with optional scope; short sentences/bullets in descriptions, which may be omitted. Root `.gitignore` (OS/editor/temp/`.env`) and `.gitattributes` (LF; CRLF for Windows command scripts; media binary).
- **Why:** Consistent history across machines.
- **Source:** history.md 2026-10-01 "Git defaults".

## 2026-10-01 — Absolute shared root in global guidelines

- **Context:** A chat in another repo looked for a nonexistent local `agent-docs` folder.
- **Decision:** Global guidelines name the shared root's absolute path; check its index before using project notes; use absolute paths for file operations.
- **Why:** Relative paths resolve against the wrong working directory.
- **Source:** history.md "Constraints and decisions"; learnings.md 2026-09-26.

## 2026-10-01 — Promote project lessons to shared learnings

- **Context:** FirstLight produced reusable Unity lessons.
- **Decision:** Promote proven lessons into shared Unity/workflow notes; keep detailed incidents and uncertain causes in Unity gotchas; keep project evidence in the project folder.
- **Why:** Reuse across projects without duplicating incidents.
- **Source:** history.md 2026-10-01 "Reusable implementation guidance".

## 2026-09-28 — Use-case diagrams in the feature template

- **Context:** Feature spec template structure.
- **Decision:** Mermaid flowchart UML-style use-case diagram above use-case headings. Diagram labels are short actions ("Create" under "Documents"); use-case and scenario headings are full sentences naming actor, action and object ("Player creates a document"). Stable UC IDs link both.
- **Why:** User's final clarification; supersedes user-story labels and action-only headings.
- **Source:** history.md 2026-09-28.

## 2026-09-27 — Self-contained notes; references at the end

- **Context:** Early notes depended on links between notes.
- **Decision:** Independently readable sections carrying situation, finding, rationale, warnings and evidence limits. Web links only in a final `References` section; local indexes stay navigational.
- **Why:** Selective retrieval without mandatory cross-note reading. Supersedes inline citations and link-heavy routing.
- **Source:** history.md 2026-09-27 entries; learnings.md 2026-09-27.

## 2026-09-27 — Gotchas only from implementation

- **Context:** Where pitfalls are recorded.
- **Decision:** Gotchas come only from actual coding/implementation, in a dedicated `gotchas.md` with project/version, repro, observed behaviour, cause, correction, verification, status. Unity findings go in `agent-docs/learnings/unity/gotchas.md`.
- **Why:** Research and note-query issues are not evidence. Supersedes distributing gotchas across topic notes.
- **Source:** history.md 2026-09-27.

## 2026-09-27 — Learning collection structure

- **Context:** Unity and C# research collections grew.
- **Decision:** Large subjects use domain folders, one query-oriented root table of contents, a concise collection AGENTS.md, root `best-practices.md` and `gotchas.md`. Small subjects stay single files; no empty scaffolding. General C# lives in `learnings/csharp/`, Unity-specific C# stays in Unity.
- **Why:** Query routing to the right section with minimal loading.
- **Source:** history.md 2026-09-27.

## 2026-09-27 — Engine-independent GDD template

- **Context:** Need for a game design document template.
- **Decision:** `agent-docs/template/game-design-document.md`: concept-first pass, nine sections, decisions and open questions as section 9. No risks/playtest-plan section.
- **Why:** User removed the risks/playtest sections.
- **Source:** history.md 2026-09-27.
