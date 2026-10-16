# Notes: Context

Updated: 2026-10-16

**Identity:** This shared guidelines and knowledge repository (agent preferences, templates, learnings, project memory). Rules: [AGENTS.md](../../../AGENTS.md); index: [INDEX.md](../../INDEX.md).

## Goals

- Portable agent guidelines, templates and per-project memory reusable across repositories and machines.
- Selective retrieval: compact, self-contained Markdown loaded only when relevant.

## Current state

- Layout: root `projects/<name>/specs/` for specs; `agent-docs/projects/<name>/` for agent memory; `agent-docs/learnings/` (Unity, C#) for shared knowledge; `agent-docs/template/` for spec/one-pager/bug/GDD templates and the agent-docs starter set (spec, one-pager, bug and agent-docs kept identical with the work planning repo); `agent-docs/skills/` for skills (e.g. product-team).
- Project memory folders now use the load-on-demand set (README, CONTEXT ≤ 40 lines, decisions, learnings, history archive); see [decisions.md](decisions.md).
- Codex profile links `AGENTS.md` and `agent-docs` to this repo (symbolic links, verified).
- Git initialised on `master` with identity configured. TBD — whether an initial commit and remote now exist (history last said none).

## Open items

- FirstLight spec 035 cites "2026-10-02 World ground/grid entries" in FirstLight CONTEXT.md; that content is now in its history.md.
- Unverified: compliance of fresh sessions with the guidelines, write access to this repo from other projects' sandboxes, product-team behaviour in real use, retrieval/context savings.

## Next steps

1. In the next substantive session in another project, verify its context is read and updated, and that it can write here.
