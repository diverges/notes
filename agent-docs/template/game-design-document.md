# [Game title]: Game design document

Status: [Concept / Prototype / Production / Released]
Updated: [Date]
Owner: [Person responsible for keeping the design current]
Design revision: [Identifier; tested build if any]

## How to use this document

Replace bracketed prompts with decisions or explicit unknowns. This describes the intended player experience and observable rules; it is not a code architecture or a complete task backlog. Label untested ideas **Proposed**, chosen rules **Decided**, and findings supported by a named playtest/build **Observed**. A decided rule is not necessarily implemented.

For a first concept pass, complete sections 1–3, 8, and 9; mark the rest unknown or not applicable with a reason. Expand before committing to production. Keep each section understandable on its own. Remove unused example rows, preserve meaningful section/requirement IDs, and update the contents when restructuring. Split substantial systems into focused documents only when needed, keeping their essential behavior summarized here.

## Contents

1. [Game overview](#1-game-overview)
2. [Experience and design pillars](#2-experience-and-design-pillars)
3. [Core loop and session flow](#3-core-loop-and-session-flow)
4. [Mechanics and rules](#4-mechanics-and-rules)
5. [Progression and content](#5-progression-and-content)
6. [Controls, interface, and accessibility](#6-controls-interface-and-accessibility)
7. [World, narrative, art, and audio](#7-world-narrative-art-and-audio)
8. [Scope and constraints](#8-scope-and-constraints)
9. [Decisions and open questions](#9-decisions-and-open-questions)

## 1: Game overview

- Pitch: [Who the player is, what they do, the central challenge, and what makes it appealing; one or two sentences.]
- Audience and play context: [Who this is for, expected familiarity, and when/where they play. Mark assumptions.]
- Genre and perspective: [How the game is experienced; avoid relying on genre labels to explain its rules.]
- Platforms and input: [Target devices and supported input methods; unknown if undecided.]
- Players and modes: [Single-player/co-op/competitive; local/online only if relevant.]
- Session and overall length: [Intended ranges and why; estimates, not promises.]
- Distinctive promise: [The particular experience this game offers.]
- References: [Optional named inspirations and the specific quality being studied; explain it here.]

## 2: Experience and design pillars

[Describe what players should feel, understand, and become better at. Choose a few pillars that help resolve competing ideas.]

| Pillar | Design implication | Tradeoff or exclusion | How to evaluate it |
| --- | --- | --- | --- |
| [Desired quality] | [Behavior that supports it] | [What we would sacrifice or reject] | [Player observation to seek] |

[Avoid goals such as “fun” without explaining the intended experience. Record deliberate tension between pillars.]

## 3: Core loop and session flow

Core loop: [Player goal] → [Action/choice] → [Challenge and feedback] → [Outcome/reward] → [Changed next choice].

[Walk through one representative moment in concrete terms: what the player sees, chooses, risks, and learns. Explain why repeating the loop stays interesting.]

- Starting: [First-time experience, initial state, and how the goal becomes clear.]
- Continuing: [How goals/challenges change within a session.]
- Ending: [Win, loss, stopping, or completion conditions; use “not applicable” where appropriate.]
- Recovery: [Retry/checkpoint behavior, cost of failure, and what resets.]
- Returning: [What persists between sessions and how play resumes.]

[Add a small state or flow diagram only if it clarifies transitions.]

## 4: Mechanics and rules

### M.1: [Mechanic name]

Status: [Proposed / Decided / Observed, with evidence if observed]

- Purpose and player choice: [Which experience/pillar this supports and the meaningful decision it creates.]
- Trigger and prerequisites: [Input, state, resources, or conditions required.]
- Rules: [Costs, effects, timing, limits, interactions, and feedback.]
- Boundaries: [Invalid input, interruption, simultaneous actions, failure, or exploit-sensitive cases that matter.]
- State: [What is local to the encounter/session and what persists.]
- Tuning: [Parameter meanings, units, provisional ranges, and authoritative location when implemented.]

**R.1.1:** [State one observable rule. Use “shall” for a chosen requirement and “should” for a preference.]

#### S.1.1: [Representative scenario]

- Given: [Relevant starting state.]
- When: [Player action or game event.]
- Then: [Observable outcome and feedback.]

[Repeat for important mechanics and boundary scenarios; keep IDs stable. For online play, state player-visible authority, joining/leaving, and disconnect rules if applicable. Keep implementation details in technical notes.]

## 5: Progression and content

- Progression: [What changes through learning, mastery, unlocks, resources, or story; or why there is no progression.]
- Difficulty and pacing: [How challenges are introduced, combined, varied, and relieved.]
- Content structure: [Levels, encounters, puzzles, items, characters, or other units; explain each unit's purpose.]
- Economy, if applicable: [Resources, sources, sinks, limits, and choices; distinguish in-game resources from monetization.]

| Content type | Smallest playable set | Planned release range | Authoring cost or uncertainty |
| --- | --- | --- | --- |
| [Type] | [Count and purpose] | [Estimate or unknown] | [Constraint] |

## 6: Controls, interface, and accessibility

| Player action | Input by supported device | Feedback | Relevant option or alternative |
| --- | --- | --- | --- |
| [Action] | [Mapping or unknown] | [Visual/audio/haptic response] | [Remapping, hold/toggle, or other need] |

- Camera and navigation: [View behavior, movement, aiming, and menu navigation.]
- Information: [What players need to notice; HUD/menu priorities and feedback for failure.]
- Onboarding: [How rules are taught and how understanding will be checked.]
- Accessibility: [Relevant barriers involving input, vision, hearing, cognition, timing, or motion; planned support and unresolved needs.]
- Settings and language: [Pause behavior, adjustable settings, text/localization needs, and persistence.]

[State intended support rather than claiming accessibility is complete. Plan feedback from players affected by the relevant barriers.]

## 7: World, narrative, art, and audio

- Setting and tone: [What the player needs to understand; avoid a lore dump.]
- Narrative delivery: [Story role, characters, delivery method, and player influence; optional.]
- Visual direction: [Style, silhouettes, readability, camera implications, and feasible asset approach.]
- Audio direction: [Music role, important cues, atmosphere, and alternatives for essential audio information.]
- Consistency: [How presentation supports the pillars and communicates actionable information.]

## 8: Scope and constraints

- Smallest playable prototype: [The central uncertainty it tests and the minimum loop/content needed.]
- Representative production slice: [What must demonstrate the intended quality and authoring process before scaling up; if needed.]
- Release essentials: [Prioritized features/content necessary for the intended experience.]
- Deferred and excluded: [Explicit cuts and what would justify reconsidering them.]
- Constraints: [Team capacity, budget/time, engine/platform, asset availability, performance goals, and dependencies that affect design. Mark estimates.]
- Commercial model, if relevant: [Premium/free/other and its effects on play; unknown rather than assumed.]
- Completion evidence: [Observable prototype/slice/release criteria; avoid arbitrary dates or invented budgets.]

## 9: Decisions and open questions

### [Date]: [Consequential design decision]

[State the issue, decision, reason, meaningful alternative, and evidence or assumption. If replacing an earlier decision, identify what changed and why.]

| Open question | Owner | Next evidence/action | Needed before |
| --- | --- | --- | --- |
| [Unknown] | [Person or unassigned] | [Concrete step] | [Decision or milestone] |

[Keep current rules accurate in their sections. Retain only meaningful dated history here, not routine progress logs. Before sharing, check that the pitch, loop, rules, scope, and design decisions agree.]
