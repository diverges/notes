# Templates

Self-contained Markdown starting points. `spec.md`, `one-pager.md`, `bug.md` and
`agent-docs/` are **kept identical** with the work planning repo's `_templates/` —
change them in both places or neither. Don't refresh them from upstream sources.

| Template | Use for |
| --- | --- |
| [spec.md](spec.md) | One implementable feature or change: proposal, acceptance criteria, test plan, verification. |
| [one-pager.md](one-pager.md) | A proposal that needs use cases, requirements (R.n) and scenarios (S.n) before it's split into specs. |
| [bug.md](bug.md) | One technical or design issue: repro, root cause (or labelled hypothesis), fix, regression claim. |
| [game-design-document.md](game-design-document.md) | A game's intended experience, loop, rules, scope and design decisions (notes only). |
| [agent-docs/](agent-docs/README.md) | Starter set for a project's agent docs folder (`agent-docs/projects/<project>/`). |

## Usage

- Resolve the canonical project first (see the [project memory guide](../projects/README.md)).
- Specs and bugs go in root-level `projects/<project-name>/specs/` as `NNN-<name>.md`
  (three-digit, continue after the highest existing number, never renumber). The GDD
  stays at its existing authoritative location.
- Replace prompts with concrete content. Write `TBD — <what's needed to resolve>` for
  unknowns; omit sections that don't apply rather than padding them.
- Keep requirements and expected outcomes separate from the proposed implementation.
- Keep task checkboxes and the Verification section current; record only checks that ran.
