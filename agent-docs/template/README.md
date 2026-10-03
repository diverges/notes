# Templates

These self-contained Markdown templates are maintained locally. Using them does not require fetching external documents or following an external framework.

- [Feature specification](feature-spec.md): feature work covering the problem, proposal, Mermaid use-case diagram, use cases, requirements, scenarios, and tasks.
- [Bug](bug.md): one technical or design issue covering the problem, reproduction, proposed correction, and tasks.
- [Game design document](game-design-document.md): a game's intended experience, loop, rules, content, scope, and design decisions. Start with its concept pass and expand relevant sections before production; engine-independent and usable by solo developers or small teams.

## Usage

- Resolve and load the canonical project folder using the [project memory guide](../projects/README.md) before writing.
- Copy feature and bug specs into the shared `projects/<project-name>/specs/` by default, with a descriptive filename or a subfolder for supporting documents. Keep the GDD under the root-level projects/<project-name>/ folder unless its existing authoritative location differs. Follow an explicit user-requested destination; existing repository-local specs alone do not override the default. Update an existing spec in place unless migration is requested, and avoid duplicate authoritative copies.
- Replace prompts with concrete information. Mark unknowns explicitly and keep the document proportional to the work.
- Name new spec files and folders `000-<name>`, such as `001-player-movement.md` or `001-player-movement/`. Use a three-digit, zero-padded sequence and a lowercase, hyphenated name; continue after the highest existing project spec number, including repository-local specs, or start at `001` when none exist. Preserve existing identifiers when editing or migrating specs.
- Retain the main sections; remove irrelevant example rows and optional details.
- Keep requirements and expected outcomes separate from the proposed implementation.
- Keep task checkboxes current and record verification evidence in the working document.
- Use relative links to supporting local notes and Mermaid when a diagram helps.
- Update these local templates deliberately; do not refresh them from upstream sources.

Use a GDD to define the game and its design choices, a feature specification to detail one feature's implementation requirements, and a bug record for a particular problem. Keep the GDD's player-facing rules understandable without opening the other records.

