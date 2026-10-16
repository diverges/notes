# Project documents

Keep human- and agent-authored project specifications and supporting documents here. Context snapshots, agent decisions, and notes used to improve future responses remain in `../agent-docs/projects/<project-name>/`.

Generated project Markdown, specs, and notepad-style planning default to this notes repository. Documentation in the code repository should describe the project itself and be primarily user-authored. Edit it when explicitly requested; do not migrate user-authored documentation simply because it is Markdown.

- [First Light specs](firstlight/specs/README.md): numbered specifications and verification report migrated from the game repository.

Use the same canonical repository or project name in both locations. Resolve names through the [project memory guide](../agent-docs/projects/README.md), and read applicable instructions and agent context before planning or editing. Load existing specs only when the user asks to read, review, update, or work from them. Ordinary project work and context links do not trigger spec loading.

```text
projects/
  <project-name>/
    specs/
      001-feature-name.md
      002-another-feature/
        spec.md
```

Create project folders when there is content to save. Specs default to `projects/<project-name>/specs/` in this notes repository, using the [local templates](../agent-docs/template/README.md). Name spec files or folders `000-<name>` with a three-digit sequence and a lowercase, hyphenated name. Continue after the highest existing project spec number, including specs still in the code repository; start at `001` when none exist. Preserve identifiers and supporting files during updates or migrations, and repair affected links. Maintain a project README when needed to navigate its documents. Spec status lives only in the project's `specs/README.md`; other indexes point to it rather than repeating it.

An explicit user destination takes precedence. Update an existing authoritative spec in place until migration is requested; avoid competing copies. Contributor and build documentation can remain with the code.
