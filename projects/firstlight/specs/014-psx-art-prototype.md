# PSX art prototype

Status: Implemented; positive user review, preferred direction leans PSX; production shader undecided
Updated: 2026-09-29

## Problem statement

First Light needs an in-engine comparison of its existing stylized assets against a retro PlayStation-inspired treatment. This began as an art investigation. On 2026-09-29, the user liked the result and expressed a preference toward PSX rendering reminiscent of a PS1-era horror game. The GDD now records that direction; this does not authorize a production-wide material or shader replacement.

## Proposal

A standalone forest clearing reuses Birch, Oak and Pine trees, the Wanderer, rocks, logs, a crafting bench, an axe and campfire assets. An isolated shader provides projected vertex snapping, affine texture interpolation, point texture sampling, vertex lighting and distance fog. A point-filtered 360-pixel-high render target is enlarged to the Game view; the display shader quantizes to 32 levels per channel with optional ordered dithering.

Cool overcast directional light, muted ambient light and a warm campfire fill establish an eerie forest. Fog starts at 17 units and ends at 40. A new ground mesh uses two-metre triangles and existing moss/path materials so vertex lighting and fog can interpolate across the clearing.

- In scope: a new scene, isolated material variants, fog, lighting, shader controls, three viewpoints, orbit and comparison captures.
- Out of scope: gameplay changes, production renderer changes, source asset conversion, builds and approval of a new art direction.
- Tradeoffs: PSX-inspired rather than hardware emulation. Modern depth testing remains; no ordering-table artifacts, transparent-surface conversion or shadow casting. Character and flame are static art references. Palette-based textures make affine warping subtler than on detailed textures; judge vertex snapping during orbit.
- A/B holds geometry, camera and light/fog inputs constant. Original shaders retain their lighting response and outlines; PSX uses vertex lighting and an approximate warm point fill. This compares the study's lighting on both treatments, not Bootstrap's original lighting.
- Current direction: lean toward PSX with an eerie, gloomy PS1-era horror appearance. Final effect strength and production shader remain open. A terrain-compatible Asset Store shader is planned for future integration; the current study does not validate it.

## Use cases

### UC.1: Designer compares the two treatments

Enter Play Mode and alternate PSX and existing materials at a fixed viewpoint.

### UC.2: Designer isolates effects and inspects readability

Toggle artifacts, vary resolution and inspect clearing, gameplay-height and close prop views, including camera motion.

## Requirements

- **R.1.1:** The experiment shall use a separate scene outside the production build scene list.
- **R.1.2:** Existing meshes, materials, prefabs and game scenes shall retain their current configuration; PSX variants shall live in the prototype folder.
- **R.1.3:** The scene shall include recognizable existing environment, character and camp assets at their existing scale.
- **R.2.1:** PSX mode shall provide low-resolution point sampling, vertex snapping, affine mapping, color quantization, fog and lighting.
- **R.2.2:** A/B shall restore original materials and native resolution without moving the camera or changing light/fog inputs.
- **R.2.3:** Individual artifacts and camera motion shall be adjustable without adding gameplay systems.

## Scenarios

### UC.1: Designer compares the two treatments

#### S.1.1: Switch at a fixed camera pose

- Given: PSX mode with orbit off.
- When: The designer presses Tab or the comparison button.
- Then: Original materials render at native resolution with the same camera pose; another toggle restores PSX.

#### S.1.2: Return to the game

- Given: The prototype is running.
- When: Play Mode stops and a production scene is opened.
- Then: Production assets and build configuration retain their previous setup.

### UC.2: Designer isolates effects and inspects readability

#### S.2.1: Tune retro artifacts

- Given: PSX mode is active.
- When: The designer changes effect toggles or raster height.
- Then: The scene responds without altering source materials; height supports 120–480 pixels.

#### S.2.2: Inspect camera motion

- Given: The clearing is visible.
- When: The designer selects Gameplay or Props, presses Space, or uses arrow keys.
- Then: Small-object readability, fog depth and vertex motion can be inspected with comparison still available.

## Task list

- [x] Author the standalone scene from existing visual assets.
- [x] Implement isolated surface/display shaders and comparison controls.
- [x] Tune fog, cool key and warm fill through actual captures.
- [x] Verify compilation, saved bindings, comparison, effect settings, resize and orbit.
- [x] Save comparison captures and document limits.
- [x] User reviewed the look positively and prefers leaning toward PSX; the GDD records the direction. Detailed motion/readability tuning and production adoption remain future work.

## Run and tune

Open `Assets/Scenes/PsxArtPrototype/PsxArtPrototype.unity` directly and enter Play Mode. Edit Mode shows the PSX surface; final raster presentation and controls activate in Play Mode.

| Control | Action |
| --- | --- |
| Tab / comparison button | PSX versus original materials at native resolution |
| Space / orbit checkbox | Slow orbit |
| Left/right arrows | Manual orbit |
| H | Hide/show review panel |
| Clearing / Gameplay / Props | Three viewpoints |
| Effect checkboxes | Fog, snapping, affine textures, dithering |
| Raster slider | 120–480 pixels tall; width follows Game view aspect |

Select `Art study controls - enter Play Mode` for fog start/end/color, ambient, framing and references. Select `Cold overcast key` or `Warm campfire fill` to tune lights. Runtime changes are temporary; tune the saved scene in Edit Mode for persistence. Materials and shaders live under `Assets/Scenes/PsxArtPrototype`; runtime shader controls use temporary material copies.

`Tools/CreatePsxArtPrototype.cs` is completed one-time authoring. `Run` refuses to overwrite the scene; edit saved content instead. `Validate` checks shader support/errors, 143 renderer bindings, isolated variant paths, missing scripts and build exclusion. `Tools/PsxArtReviewSmoke.cs` exercises Play Mode and refreshes five captures. Run through Unity Pipeline `run_script` using the corresponding class entry.

## Verification and review

Unity 6000.3.20f1, URP 17.3.0, Windows Editor. C# compilation passed. Both shaders are supported without shader errors. Saved-content validation passed for 143 renderer bindings, including inactive variants. Play Mode smoke passed keyboard A/B, exact original material restoration, fixed framing, 240/360-pixel targets, fog/snap/affine controls and orbit. Final warmed-variant captures were inspected in both treatments and at gameplay/prop distances. A first cold stylized capture showed Unity's cyan loading shader; repeating after variant compilation produced the valid comparison below. Final console had zero current errors/warnings.

The PSX view visibly trades small-object clarity for coarser surfaces and atmosphere. This is an observation, not artistic acceptance. Gameplay regression tests, player builds and profiling were not run for this isolated scene. Review the axe/logs at gameplay distance, character separation, fog depth and moving-vertex comfort before adopting any treatment.

### Captures

PSX clearing:

![PSX clearing](D:/Repos/FirstLight/Docs/Images/psx-art-clearing.png)

Original materials with the same composition and light inputs:

![Original materials](D:/Repos/FirstLight/Docs/Images/psx-art-stylized.png)

Props:

![PSX props](D:/Repos/FirstLight/Docs/Images/psx-art-props.png)

Gameplay height:

![PSX gameplay](D:/Repos/FirstLight/Docs/Images/psx-art-gameplay.png)

Controls:

![Review controls](D:/Repos/FirstLight/Docs/Images/psx-art-controls.png)


### 2026-09-29: Softer pixelation

User requested less intense pixelation. Raised the saved scene and component default from 240 to 360 pixels tall, making displayed pixels one-third smaller at a fixed window size. Other effects retain their settings. Compilation and updated 360/480px smoke passed; five captures refreshed and the clearing inspected. Final live target is 360px with zero current console errors/warnings. Earlier 240px defaults and captures are superseded.

### 2026-09-29: Screenshot warning removed

PSX mode now creates an empty display camera behind its overlay canvas. This supplies a camera rendering directly to the display, addressing Unity's No cameras rendering banner while the world camera still targets the low-resolution texture. Original mode disables the display camera; cleanup destroys it. Compilation passed. Live checks confirmed exactly one enabled display-target camera in each mode, no world geometry rendered by the presentation camera, and successful A/B restoration. Inspected psx-art-clean-preview.png. User's live 420px Props view restored; saved default remains360px. A Pipeline request timed out during Play Mode startup; retry succeeded, with no new rendering exceptions observed.
