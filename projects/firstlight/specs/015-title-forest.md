# PSX forest title scene

Status: Implemented; visual review pending
Updated: 2026-09-30

## 2026-09-30: Match World rendering

### Terrain composition refinement

Follow-up: the campfire now has a level 4.5m-radius clearing, smoothly returning to existing terrain at 8.5m. Nearby scenery was re-grounded; textures and lighting values were preserved. Measured height variation within the inner 4m is below 0.005m. `FlattenTitleClearing.Run` is completed one-time tuning; do not repeatedly apply it over hand edits.

Correction to the initial terrain verification: legacy clearing/ridge objects had remained active because the migration searched their earlier descriptive names, while saved names were Clearing and Ridge 0–2. The overlap caused hard patches where the terrain crossed the old mesh. `RetireTitleMeshGround.Run` disabled all four using their mesh asset paths; authoring and validation now use those paths. Final wide capture shows a continuous pool of firelight without the overlapping bands. Aspect/profile checks passed, current console zero errors/warnings; Start remains playing. No transition test or build repeated for this targeted terrain correction.

The user liked the Retro treatment but found the migrated composition too plain. Start now has its own editable 128×128m TerrainData with 0.5m height samples and four painted layers sharing World's Moss, Dirt, Rock and Sand tile-color textures. The camera faces a sheltered clearing, winding dirt trail, asymmetric grassy banks and wooded hills. Near banks rise roughly 2–4m; larger slopes rise behind the forest within the fog/camera range. Camera-projected terrain samples span 0–21.6m, including 608 samples above 1m in the near region (z<20); captures, rather than projection alone, establish that the banks and rear slopes are visible.

All 88 tree, rock and campfire roots were repositioned onto the terrain. Original clearing/ridge objects remain disabled for recovery. The camera, title/button layout, shared Retro renderer/profile, fog and lighting were preserved. Terrain uses World's existing Retro Terrain Lit material; World terrain and layer assets were not edited. The generated layer weights include dirt at the trail/clearing, rock on steeper slopes and a little sand near the fire.

Select `Title Terrain - sculpt and paint here` under the title forest to edit terrain. Its data lives in `Assets/Scenes/Start/Terrain/TitleTerrain.asset`. `AuthorTitleTerrain.Run` is completed guarded authoring and refuses regeneration; use `Validate` for terrain/shader, grounding and camera-framed elevation checks. Preserve subsequent hand sculpting/painting.

Saved validation passed: four layers, terrain collider/data, supported shader, 88 grounded roots and camera-projected elevation. Wide/narrow captures inspected; native aspect/shared-profile checks and the real pointer Start-to-World transition passed, including flickering flames/light and emitting smoke. Current Editor console: zero errors/warnings. No build, performance profile or gameplay suite. Start left playing for subjective visual review.

Start now uses the shared WorldRenderer and WorldPresentation profile: Retro Shaders Pro surfaces, perceptual 96-level color dithering, 2px point sampling and rolling edge mist. This supersedes the prototype surface shader and 420px title raster described in the historical implementation below. The title artwork, button, camera, scenery geometry and animated fire/smoke remain in place. Start loads World.

All 218 solid-surface bindings use Retro materials. Trees, rocks, firewood and flames directly share World's materials; five title-specific ground/ridge materials inherit its surface settings. The soft transparent smoke shader remains, processed through the shared full-screen pass. Lighting uses World's saved night ambient, fog color and moon settings, with a flickering intensity-5 fire at 12m range. Title fog spans 6–80m to accommodate the deeper forward-facing composition; World fog remains 6–40m. The title stays at night rather than running the gameplay day/night cycle.

`TitleAtmosphere.useWorldRendering` bypasses its legacy material clones, render texture and overlay display; only flame and real-light animation runs. Legacy fields remain for the earlier authoring path. `WorldPresentation.asset` owns shared dither/pixel settings, so future profile adjustments affect both scenes. The title's palette is a saved night snapshot rather than a live dependency on World's scene lighting.

`Tools/MatchStartToWorld.cs` owns the completed guarded migration and saved/live Validate entry. Do not rerun Run on migrated content. `TitlePresentationSmoke` now checks native viewport aspects and the live shared profile; `StartScreenSmoke` checks fire/smoke/artwork and a real pointer transition to World.

Validation: Unity compilation passed; all 218 Retro bindings and shared renderer/profile validated. Widescreen and narrow captures inspected, live 96-level/2px dither settings checked, flame/light animation and smoke emission passed. Actual pointer Start loaded World and removed title rendering/UI. Current console had zero errors/warnings. No player build, browser profiling or full gameplay regression suite was run. Subjective visual acceptance remains with the user.

## Problem statement

The plain Start scene needs to introduce First Light's eerie PS1-inspired forest setting, using the existing shader experiment. The user requested the words on separate lines, distant trees and hills, and one flickering campfire with a smoke trail centered below the title and Start button.

## Proposal

Start now contains an authored 3D clearing with existing Pine/Oak and rock visuals, three generated faceted hill meshes, a triangulated ground mesh and one decorative campfire. Scene-local materials reuse the prototype PSX surface shader. A 420px point-filtered render target reuses its display shader with restrained dithering; the title graphic and button remain legible UI overlays.

- In scope: Start scene art, original stacked title artwork, PSX materials, smoke, flame and warm-light animation, responsive framing and the existing Start transition.
- Out of scope: gameplay changes, production terrain integration, audio, loading systems or additional menu options.
- Tradeoff: hills use unlit colors through the same PSX surface shader for distinct silhouettes; smoke uses a small transparent shader, then passes through the same low-resolution display. This is PSX-inspired rendering, not hardware emulation.
- Open question: user acceptance of composition, title treatment and effect strength.

## Use cases

### UC.1: Player enters the game

The player sees the title above the forest clearing and selects Start to load Bootstrap.

## Requirements

- **R.1.1:** The title shall show First above Light with Start beneath it.
- **R.1.2:** The background shall show a dark forest and distant hills using the existing PSX treatment.
- **R.1.3:** One campfire shall appear centered below Start, with flickering flames, changing warm illumination and a rising smoke trail.
- **R.1.4:** Start shall load Bootstrap and release the title scene's temporary rendering resources.
- **R.1.5:** The image shall follow viewport aspect changes without stretching the title artwork.

## Scenarios

### UC.1: Player enters the game

#### S.1.1: Opening presentation

- Given: Start is running.
- When: time advances.
- Then: flames and illumination vary, smoke emits, and the title and button remain readable above the centered fire.

#### S.1.2: Start transition

- Given: the title screen is visible.
- When: the player clicks Start.
- Then: Bootstrap replaces Start, including its UI, raster display and scenery.

#### S.1.3: Viewport resize

- Given: the title screen is running.
- When: the viewport changes between widescreen and a narrower aspect.
- Then: the raster target follows the aspect and the artwork retains its proportions.

## Task list

- [x] Generate and import the stacked title artwork.
- [x] Author forest, hills, clearing, campfire and scene-local materials.
- [x] Add flame/light animation, smoke and PSX presentation.
- [x] Verify shader bindings, compilation, animation, resizing and pointer transition.
- [x] Inspect native widescreen and narrow captures and document authoring.
- [ ] User visual review.

## Authoring and source ownership

Open `Assets/Scenes/Start.unity`. The scene is already the first build scene. Edit its saved objects and materials; original gameplay prefabs and prototype assets remain their own sources.

`Assets/Scenes/Start` owns the new assets. `TitleAtmosphere` exposes the world camera, renderer bindings, flame transforms, light, raster height, warm-fill color and display material. It clones surface materials at runtime, updates the PSX shader's warm fill with the light flicker, and releases material/render-texture instances on disable. Hill meshes and clearing are saved `.asset` meshes. Trees, stones, campfire structure and flame mesh reuse existing visual assets without gameplay behaviours.

`StartScreen` uses a serialized artwork reference with a two-line text fallback. `StartScreen.uss` positions the transparent title overlay and existing focused Start button. No title-study controls appear in the menu.

`Tools/AuthorTitleForest.cs` is completed one-time authoring. Run refuses to regenerate an already-authored title. Validate checks saved bindings and shader support; use it in Start. `StartScreenSmoke.Run` checks the live artwork, 420px frame, changing fire and smoke, then performs a real input-system pointer click. `TitlePresentationSmoke.Run` checks viewport/raster resizing, saves two native captures, and restores 16:9 at 420px.

## Verification

Unity 6000.3.20f1, Windows Editor. C# compilation passed. Saved validation passed for 205 renderer bindings; PSX surface/display and smoke shaders supported without shader errors. Live smoke passed: stacked artwork, 420px point target, changing flame scale/light intensity, active particles, actual pointer Start transition and removal of title rendering. Separate 360/420px and widescreen/narrow resizing checks passed. Both final native captures were inspected; the shorter flames sit below the button and three hill layers are visible. No player build, web profiling, audio audition or full gameplay regression suite was performed for this presentation change.

![Widescreen title scene](D:/Repos/FirstLight/Docs/Images/title-forest-wide.png)

## 2026-09-29: Foreground elevation and lower campfire

After positive user review, the foreground now rises into gently rolling banks beside the central clearing, reaching about 1.8 metres near the camera and tapering into the flat forest by z=16. Nearby tree/rock roots follow the reshaped ground. The single campfire moved three metres toward the camera to sit lower in the frame; its flames, smoke, light and PSX warm-fill coordinates moved together. Camera and title/button layout are unchanged.

`AuthorTitleForest.RefineForeground` is a completed targeted migration; it reapplies these bank heights and fire position, so preserve subsequent manual tuning before rerunning. The initial authoring entry now includes the same settings. Migration compilation/execution and existing widescreen/narrow, 360/420px resize checks passed. Both refreshed native captures were inspected. Current console has zero errors/warnings; no gameplay tests or build were repeated for this scenery-only edit. Start remains running at 16:9 and 420px for review.

## Artwork generation

Built-in imagegen generated and edited the original transparent lettering. Selected asset: `Assets/Scenes/Start/Textures/FirstLightTitle.png`. Unity imports it with preserved alpha, point filtering, no mipmaps and no compression, capped at 1024px. Discarded horizontal variant remains outside the project.

Initial prompt:

> Use case: logo-brand. Asset: transparent title lettering for the Unity game First Light. Text exactly "First Light", one horizontal line, capital F and L. Original late-1990s PlayStation 1 eerie forest survival game title, elegant sharp tall serif letterforms, worn ivory stone-metal surface, subtle desaturated warm brass edges, rough pixel-grain distress, restrained shallow bevel and dark thin outline for legibility against a dark forest. Clearly readable at 600 pixels wide. Wide composition, lettering fills canvas with modest transparent padding. No background, no scenery, no symbols, no extra words, no PlayStation logo, no modern neon, no excessive flourishes. Genuine alpha transparency.

Final edit prompt, following the user's layout correction:

> Edit this game title lettering. Keep the exact original distressed ivory/brass late-1990s PS1 horror serif lettering style. Change only layout: exactly TWO centered lines, "First" on the first line and "Light" on the second. Equal font size, compact line spacing, balanced centered composition, no overlap. Text exactly First Light, no other text. Genuine transparent background, no scenery or symbols. Modest transparent margin. Asset for dark forest title screen.
