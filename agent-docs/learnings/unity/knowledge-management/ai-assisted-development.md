# Agent context routing for Unity

Updated: 2026-09-27
Status: Agreed note convention; effectiveness in future sessions remains unverified.

## 2026-09-27: Self-contained sections

The user wants future queries to retrieve relevant knowledge without loading unrelated details. Notes should also preserve the context and reasoning of a diary entry. A dense network of links makes this harder when an entry cannot be understood on its own.

Decision: organize a small number of focused topic files into descriptive sections. Each section should contain enough context, the useful finding or decision, its reason, and verification limits to stand alone. Include dates for findings and changes of direction. Briefly restate an essential dependency rather than making the reader follow another note to understand the conclusion.

Links remain useful for indexes, exact implementation locations, and research provenance. They are optional supporting material, not a substitute for the finding itself. Do not copy entire source documents or duplicate changing configuration values.

## File roles

- `AGENTS.md`: concise behavior and reading rules; avoid detailed subject guidance.
- Shared `INDEX.md` and Unity `README.md`: small directories identifying the file and section relevant to a query.
- Game `CONTEXT.md`: identity, essential constraints, current state, verification limits, and next steps. Include enough explanation to understand the state without opening its references.
- Topic notes: related concepts grouped under independently readable headings.
- Project `decisions.md` and `learnings.md`: short dated entries preserving the problem, decision or discovery, rationale, evidence, and remaining uncertainty.
- Feature and bug records: self-contained requirements or reproduction, proposal, scenarios, and tasks using the existing templates.

Keep technical truth in the source code/assets or authoritative documentation. A note summarizes the relevant contract and identifies its source; it need not repeat the implementation.

## Reading workflow

1. Read applicable instructions, verify the shared knowledge root, and inspect its index. Read the game's context for substantive project work.
2. Use the topic directory or search to select a file. Inspect headings, for example with `rg -n '^#{1,3} ' <absolute-file>`, then read the matching section through the next heading of equal or higher level.
3. Include the file's brief date/scope information and any explicit constraint applying to the selected section. Do not load every linked document or the whole learning history.
4. Inspect source code, assets, or primary documentation when the task requires implementation evidence or version verification. Open another note only for a concrete unanswered question, not simply because a link exists.
5. Stop retrieving when the next action's outcome, constraints, affected behavior, and verification are understood. Expand again if new evidence exposes a gap.

If the selected entry repeatedly requires other notes just to make sense, repair its explanation. Self-contained notes do not eliminate the need to inspect a real game's current files.

## Writing findings

Within the relevant topic or project learning file, use a short dated heading naming the concept. Record the situation, finding, decision and why, evidence, scope, and unresolved checks. Actual Unity coding surprises belong only in the dedicated `gotchas.md`, with project/version, reproduction, observed behavior, correction, and verification. Research and retrieval problems are not implementation gotchas.

Update an existing entry when it describes the same issue. If a conclusion changes, retain a concise dated correction and mark the old conclusion superseded. Preserve meaningful history without accumulating transcripts or a daily activity log. Promote project findings only when their broader applicability is supported.

## Keep files manageable

Prefer sections before adding files. Split a file when it mixes independent topics or its headings become difficult to navigate. Keep each resulting note coherent on its own and update the directory. Avoid both one giant diary and one file per tiny observation; no fixed word count overrides clarity.

To evaluate this structure, try representative queries such as a serialized-field rename, a scene-reset failure, or a target-device frame spike. Check whether the selected section gives useful context without a chain of note reads. Correct unclear navigation when observed. Navigation checks alone do not prove reduced context use.
