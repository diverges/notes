# Specification Templates for Agent Collaboration

Researched: 2026-09-26
Status: Historical research. The selection below was superseded by the self-contained local templates (2026-10-16: spec.md, one-pager.md and bug.md, shared with the work planning repo). External links are provenance only, not dependencies or instructions to refresh the templates.

## Findings

- GitHub Spec Kit supplies separate feature specifications and implementation plans. This provides a useful distinction between required behavior and the proposed implementation. Its feature template includes acceptance scenarios and measurable outcomes. Our versions omit framework commands and mandatory process gates.
- Cucumber defines scenarios around initial conditions, actions, and observable outcomes. That structure makes requirements easier to check even without adopting its tooling.
- Michael Nygard's architecture decision records preserve the context and consequences of a decision. They can reduce repeated debate and keep future agents from losing the rationale.
- Shape Up pitches bound an idea before committing to detailed execution. A time or effort appetite helps constrain scope; it is not a forecast.

## Original Selection (Superseded)

Start with a feature specification for behavior changes. Add a technical design only when the implementation merits comparison or coordination. Use a decision record for lasting choices. Use a project brief when the idea itself needs definition.

These patterns have published practitioner sources; that does not establish that this particular template set improves every model or task. Evaluate it through requirement coverage, rework, review effort, and completion time.

## References

- [GitHub feature specification template](https://github.com/github/spec-kit/blob/main/templates/spec-template.md)
- [GitHub implementation plan template](https://github.com/github/spec-kit/blob/main/templates/plan-template.md)
- [Cucumber Gherkin reference](https://cucumber.io/docs/gherkin/reference/)
- [Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
- [Shape Up: Write the Pitch](https://basecamp.com/shapeup/1.5-chapter-06)
