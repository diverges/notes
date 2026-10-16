# Connect game design to implementation

Updated: 2026-09-27
Status: Research synthesis; proposed practice for solo developers and small teams.

## Preserve the reason behind the behavior

Recommendation: organize design notes around player-facing features, with links to their implementation. Explain the intended experience and observable rules together. Otherwise a future refactor may remove deliberate timing allowances or exceptions as apparent inconsistencies.

Maddy Thorson's account of Celeste describes timing and positioning allowances that support the intended feel. This is a first-person production example, not Unity-specific implementation guidance or a prescription for numeric defaults.

For example, “jumping should feel forgiving” is intent. “A jump pressed inside the configured buffer window before landing is consumed once on landing” is an observable scenario. A test can verify the latter; playtesting must assess whether it achieves the former.

## Extend the existing feature record

Use `agent-docs/template/spec.md` for a feature (or `one-pager.md` when it first needs use cases, requirements and scenarios), keeping its main sections. The finished record should explain the feature without requiring other notes. Include these details only where relevant:

- Player intent and scope: the experience sought, evidence for the problem, and what the change excludes.
- Scenarios: starting state, player action, outcome, relevant interruption/failure cases, and restart behavior.
- Authoring path: the scripts, prefabs, scenes, input actions, and assets that control the feature; how to preview it.
- Tuning contract: each important parameter's meaning, unit, valid range if known, and interactions. Link authoritative values in assets instead of copying every default.
- State contract: what resets on death, scene transition, new session, or new game; what survives a save.
- Evidence: tested build/revision, observations, decisions, and remaining uncertainty.

Keep cross-cutting rules such as save compatibility and input policy authoritative in the project documentation. Briefly state the rule relevant to this feature and identify its source, so the feature record is understandable without duplicating a complete policy.

## Make iteration cheap without building a tools department

Recommendation: start with clear Inspector fields and a representative test scene when these support the workflow. Use shared configuration assets when several prefabs need the same authored data. Add custom inspectors, validators, or preview tools after identifying a repeated authoring bottleneck or costly invalid setup.

Unity's data/logic guidance presents ScriptableObjects as useful for shared configuration and collaboration, while acknowledging that patterns have costs and must fit the project. Its account of Ryan Hipple's architecture shows how composable systems can support designer experimentation; adopting the entire architecture is not necessary to use that principle.

For a new authoring workflow, verify that a teammate or future you can find the setting, make a valid change, preview it, and recover from an invalid setup. Record recurring mistakes before deciding which tool to build.

## Keep consequential playtest evidence

Unity's prototyping material emphasizes testing the core experience. Our recommendation is a compact experiment record: hypothesis, build/revision and context, changed variable, observed player behavior, interpretation, and decision. Separate observation (“three testers missed the activation cue”) from inference (“the cue may be too subtle”).

Do not record every session as a transcript or treat a few testers as statistical proof. Preserve findings that change a design or justify retaining it. Name who evaluated subjective criteria and what they actually tried.

When applicable, include input remapping, readable feedback, localization expansion, accessibility needs, content dependencies, and save/restart behavior in acceptance criteria. These are prompts to establish the game's requirements, not instructions to build every system upfront.

## References

- [Celeste & Forgiveness](https://www.maddymakesgames.com/articles/celeste_and_forgiveness/index.html)
- [Unity: separate game data and logic](https://unity.com/how-to/separate-game-data-logic-scriptable-objects)
- [Unity: ScriptableObject architecture](https://unity.com/how-to/architect-game-code-scriptable-objects)
- [Unity Creative Core: prototyping](https://learn.unity.com/course/creative-core-prototyping?version=6.0)
