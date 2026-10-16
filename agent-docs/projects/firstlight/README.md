# First Light: Agent memory

FirstLight Unity game (forest survival RPG). Specs live separately in [projects/firstlight/specs](../../../projects/firstlight/specs/README.md); load them only on request.

## How to use this folder

- Read this README, then [CONTEXT.md](CONTEXT.md). Load other files only when the "When" column applies.
- Overwrite CONTEXT.md each session (≤ 40 lines); add consequential choices to decisions.md; never append to history.md.
- Code paths are plain text (`FirstLight: Assets/...`); nothing here is verified against source unless stated.

| File | Holds | When |
| --- | --- | --- |
| [CONTEXT.md](CONTEXT.md) | Current state, open issues, review queue, next steps | Always |
| [overview.md](overview.md) | Architecture: scenes, assemblies, runtime ownership, rendering, test entry points | Planning or changing code |
| [decisions.md](decisions.md) | Dated decision log, newest first | A choice may revisit an earlier one |
| [glossary.md](glossary.md) | Project terms | A term is unclear |
| [learnings.md](learnings.md) | Agent workflow lessons (UTF-8 source edits) | Editing source files on Windows |
| [history.md](history.md) | Archived dated diary (old CONTEXT.md, to 2026-10-03) | Never by default; search for a past event |
| [code-review-2026-10-02.md](code-review-2026-10-02.md) | Assembly/namespace migration, lifecycle fixes, remaining findings | Touching lifecycle, assemblies or conventions |
| [architecture-review-2026-09-30.md](architecture-review-2026-09-30.md) | Defects (enemy init, recipe overlap, death loot), save readiness | Enemies, crafting validation or save work |
| [enemy-architecture-review-2026-10-01.md](enemy-architecture-review-2026-10-01.md) | Enemy ownership table and recommended structure | Changing enemies or combat |
| [actions-review-2026-09-30.md](actions-review-2026-09-30.md) | Action/interaction model investigation (pre-spec 022) | Reworking actions or InteractionController |
| [ui-review-2026-10-01.md](ui-review-2026-10-01.md) | UI review; layout proposals rejected, style direction kept | UI direction questions |
| [ui-conversion.md](ui-conversion.md) | Implemented retro UI: sizes, fonts, console, verification | Changing HUD/panel styling |
| [firstlight-ui-direction.png](firstlight-ui-direction.png) / [.svg](firstlight-ui-direction.svg) | Rejected UI direction board | Only for the UI review history |
| [sound-review-2026-10-01.md](sound-review-2026-10-01.md) | Audio inventory, gaps (Boar/Spiderling/acid), direction | Audio work |
| [feel-integration.md](feel-integration.md) | Feel 5.9.1 capabilities and integration guidance | Adding feedback effects |
| [dark-forest-scatter.md](dark-forest-scatter.md) | Scatter prop kit, mesh-vs-billboard research | Placing or extending foliage |
| [magical-willow.md](magical-willow.md) | Willow model state and revisions | Editing the willow |
| [design-backlog.md](design-backlog.md) | Unselected design candidates (survival stats, recipes, skills) | Designing new mechanics |
| [character-camera-spec.md](character-camera-spec.md) | Relocation stub for spec 006 | Never; use the specs index |
