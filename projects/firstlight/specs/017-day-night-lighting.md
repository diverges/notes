# Day and night lighting preview

Status: Implemented; visual tuning pending user review
Updated: 2026-10-02

## 2026-10-02: Start/end ranges and zoom compensation

Each period foldout now contains Fog color, Fog start distance and Fog end distance. Both distances interpolate with the stage durations. Missing start values initialize from the scene's existing fog start (World6); existing end values remain intact. Validation keeps starts nonnegative and ends at least0.01 units beyond starts. Disabling restores both original scene distances.

Fog zoom compensation defaults to1: both interpolated endpoints receive `(current smoothed camera radius - default radius) * compensation`. Camera rig is optionally assigned or found in the same scene on enable; scenes without a gameplay rig get zero offset.0 disables compensation; values up to2 allow tuning. Application occurs in LateUpdate after the rig updates its radius. A shared lower clamp prevents negative starts while preserving fade width. This approximates a stable player-centered boundary during zoom; angle/height effects remain. No shaders changed.

Unity compilation passed. VerifyFogZoom exercised actual near/far zoom, distinct four-period ranges and every blend midpoint, identical endpoint shifts, stable fade width, compensation-off behavior, nonnegative clamping, midnight continuity and restoration of both endpoints. Actual420-point Inspector rendered and visually inspected at Docs/Images/GEN_daynight-inspector-fog.png with Start/End in period foldouts; capture did not mutate serialized data. Editor returned to Edit Mode. No full regression suite, Player build or human visual zoom acceptance performed. Arena authoring helper now writes the new range fields explicitly; it was not rerun to rebuild the scene.

### 2026-10-02: Fog fields grouped into period foldouts

Moved each fog end distance into its matching Night/Dawn/Day/Dusk foldout beside Fog color, removing the separate Fog section. Existing serialized distances and runtime blending are unchanged. User's active Play Mode session was preserved; Unity will load the new Inspector after Play Mode ends. Final rendered layout was not checked in this active session.

## 2026-10-02: Fog distance per period

User clarified that fog end distance should be configurable per lighting period. A dedicated Inspector Fog section now exposes Night, Dawn, Day and Dusk end distances in world units. World starts at40/40/24/40. Existing serialized daytime distances migrate into the Day value, including the arena's65-unit override. Distance now interpolates with smoothstep between stage values using their configured durations, including dusk-to-night wrap; this supersedes the earlier solar-elevation-only distance control. Fog start, mode, enabled state and stage colors retain their previous ownership. Disabling still restores the original scene fog distance.

Unity compiled. VerifyPerStageFog passed four distinct distances32/20/12/26, every blend midpoint, all boundaries/midnight continuity and disable restoration. VerifyDayNightRecovery passed again. Actual420-point Inspector captured and visually inspected at Docs/Images/GEN_daynight-inspector-fog.png without changing data. Editor returned to Edit Mode. No full suite, Player build or user visual acceptance performed.

## 2026-10-02: Brightness and duration authoring

User found all four stages too bright and requested an LOD-style Inspector chart for their durations. Brightness now defaults to0.7, a first-pass30% reduction in sun/moon intensity, ambient color and reflection strength. Fog colors and independent campfire lights retain their own authored values. Final perceived brightness requires user review; this multiplier is not a claim of30% lower displayed luminance.

The Inspector shows four colored duration segments totaling24 hours, draggable dividers, per-stage hours/real seconds, numeric start-hour fields and a yellow current-hour marker. Dragging snaps to15 minutes and keeps each segment at least15 minutes. Numeric edits allow finer tuning. Default boundaries remain0/6/12/18. Each segment smoothly blends from its named preset to the next; these are transition durations rather than flat lighting holds. Sunrise, noon and sunset follow Dawn, Day and Dusk boundaries. Preview buttons jump to the configured stage boundary and hold the clock.

Unity compilation passed. VerifyDayNightDurations passed unequal lengths8/2/4/10, boundary/midnight continuity, shifted sun/fog timing, brightness and invalid-duration clamping. VerifyDayNightRecovery again passed live clock advancement/wrap, pause, stage lighting/fog and disable restoration. The actual custom Inspector was rendered and visually inspected at420 points wide; injected GUI mouse events moved Dawn from6h to8h and Undo restored original serialized data. Capture: Docs/Images/GEN_daynight-inspector-durations.png. Editor left in Edit Mode with lighting selected. No full suite, Player build, narrow Inspector check or human darkness acceptance performed.

## 2026-10-02: Missing controller repaired

World's lighting object had a missing MonoBehaviour. Its saved script GUID `fe5b1977b81472749b35ee2e8b5fb138` resolved to no asset, and no WorldDayNightCycle source remained in Assets. The cause of its removal is unknown; this repository has no tracked source history available for recovery.

Reconstructed the controller under Assets/Runtime with its original GUID, retaining the saved sun, moon, session, four presets, four-minute duration and noon fog distance24. Restored the stage preview Inspector. Current presentation uses native RenderSettings fog; the historical edge-fog renderer integration described below is not present in current runtime sources and was not recreated.

Unity compiled and reconnected World's component without replacing the scene object. VerifyDayNightRecovery passed saved-reference/preset checks, smooth stage boundaries, night/noon/dusk light behavior, native fog distances40/24, live clock advancement and midnight wrap, menu pause and disable restoration. World returned to Edit Mode with its lighting object selected. No Player build, full regression suite or human visual acceptance performed. Other saved scenes use the same restored script GUID; their Play Mode behavior was not exercised.

## Problem statement

World needs a continuous day/night cycle to compare lighting choices while retaining the gloomy aesthetic, visible edge mist and warm campfire contrast.

## Proposal

World's Day and night lighting object owns WorldDayNightCycle. Four serialized lighting stages at midnight0, dawn6, day12 and dusk18 blend with smoothstep interpolation, including dusk-to-midnight wrap. A four-minute default cycle starts at night. Sun pitch rotates continuously and its light fades to zero at/below the horizon. A fixed, unshadowed moon fill preserves the earlier night light direction/intensity while the sun is absent. It is an artistic fill rather than an astronomical moon simulation.

Each stage exposes sun color/intensity, moon intensity, ambient color, world fog color, edge mist color/strength and reflection strength. Distance fog6–40, edge mask shape/motion, pixel/dither settings and campfire intensity5/range12 are unchanged. Renderer feature uses a transient cycle fog override, not mutations to its serialized palette. Disabling the controller restores prior ambient mode/colors/reflections and sun state, removes the override and turns off its moon.

- In scope: continuous clock, editable lighting presets, Inspector preview controls and four stage captures.
- Out of scope: gameplay time effects, saving time to disk, weather, sky astronomy, baked lighting and volumetric fog.
- Tradeoffs: additional directional fill; preview performance/build not profiled. Night palette/intensity reuse earlier tuning, but moon fill is unshadowed.
- Open questions: final palette, cycle duration and relative length of daylight remain user choices.

## Use cases

### UC.1: Designer compares lighting stages

Select Day and night lighting in the Hierarchy. In Play Mode use Night/Dawn/Day/Dusk buttons to jump and hold, or uncheck Running and scrub Hour. Minutes Per Day controls speed. Re-enable Running to resume. Expand each stage to tune colors and intensity. Play Mode Inspector edits revert on exit; apply accepted values in Edit Mode for persistence. In Edit Mode these controls configure the next Play session rather than rendering a live scene preview.

### UC.2: Player experiences a continuous cycle

Time advances while the session is ready and unpaused. Settings/focus/death pause progression through the existing session pause rules. Campfires remain constant and naturally become more prominent at night.

## Requirements

- R.1.1: Lighting and fog shall blend continuously through all stages and midnight wrap.
- R.1.2: Sun shall rotate continuously and contribute no light below the horizon.
- R.1.3: Campfire light settings shall remain independent of the cycle.
- R.1.4: Designer shall be able to hold, scrub and resume time and edit four presets.
- R.1.5: Disabling the controller shall restore the lighting it replaced.

## Scenarios

### UC.1: Designer compares lighting stages

#### S.1.1: Hold and inspect a stage

- Given: World is playing with cycle enabled.
- When: A stage button is selected.
- Then: Hour jumps to the stage, Running becomes false, lighting holds for inspection.

#### S.1.2: Restore prior lighting

- Given: Controller is driving lighting.
- When: Controller is disabled.
- Then: Original sun/ambient/fog/reflections return, moon becomes dark, edge override clears.

### UC.2: Player experiences a continuous cycle

#### S.2.1: Wrap and pause

- Given: Clock approaches midnight.
- When: Time advances past24, then gameplay pauses.
- Then: Hour wraps continuously to0 and remains fixed while paused.

## Task list

- [x] Implement clock, four-stage blending and sun horizon fade.
- [x] Add Inspector stage buttons, Hour slider, Running and duration controls.
- [x] Install World lighting object and transient edge-fog integration.
- [x] Verify stage boundaries/wrap, pause, sun horizon behavior, constant fire and restoration.
- [x] Capture and inspect all four stages; document visual acceptance limits.

## Verification

AuthorDayNight.Run performed guarded one-time scene setup; refuses duplicate installation. Verify temporarily holds clock/controls focus/input, samples intensity/ambient/edge-color continuity immediately around0/6/12/18/24, captures four stages, checks live midnight wrap and pause, checks zero sun at midnight/dusk, constant campfire values, and disable restoration of original ambient/sun intensity. All passed. Compilation completed successfully after qualifying the UnityEditor.Editor base class. Current console zero errors/warnings. Four images inspected. No full gameplay regression suite, player build, target-device profiling or full-cycle video review. World left playing with lighting controller selected and Running enabled. Gameplay state is not reset by verification.

## Stage previews

### Night

![Night](D:/Repos/FirstLight/Docs/Images/world-cycle-night.png)

### Dawn

![Dawn](D:/Repos/FirstLight/Docs/Images/world-cycle-dawn.png)

### Day

![Day](D:/Repos/FirstLight/Docs/Images/world-cycle-day.png)

### Dusk

![Dusk](D:/Repos/FirstLight/Docs/Images/world-cycle-dusk.png)

### 2026-09-30: Cycle light hierarchy

Sun and Moon fill are children of Day and night lighting. ParentCycleLights saved the grouping and verified unchanged world positions/rotations and controller references. Campfire light stays under its own campfire. Authoring helper now produces the same hierarchy.
