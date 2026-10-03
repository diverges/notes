# World bootstrap

Status: Implemented; verified in Editor, user visual review pending
Updated: 2026-09-30

## 2026-10-02: Additive scene ownership

[Spec 035](035-world-scene-management.md) supersedes the single-scene authoring and initialization assumptions below. World now owns manager/session/navigation/loading; World_Player, World_Terrain, World_Gameplay and World_Lighting hold their respective content. Open the full set using First Light > World > Open world scene set. Existing placements and terrain/nav assets were preserved. The earlier sections remain historical content and validation evidence, not instructions to rerun one-time monolithic authoring scripts.
## Problem statement

The gameplay systems need a first playable level with editable terrain and usable props. WorldDesign is a terrain experiment and Bootstrap is a mechanics test scene. The user selected the installed Retro Shaders Pro for URP package as the main gameplay surface treatment, initially approximating the existing PSX study.

## Proposal

World is a saved gameplay scene with a 64×64 m Unity Terrain, Wanderer, existing camera/UI/session, and a small resource clearing. Start enters World; Bootstrap remains available for regression tests. Terrain painting and further level dressing belong to the user.

- In scope: terrain, navigation, starter resources, existing gathering/crafting/fire systems, Retro materials, Start transition.
- Out of scope: new building mechanics, terrain painting, finished world layout, browser performance tuning.
- Tradeoff: gameplay prefabs now reference new Retro materials so runtime drops, held tools and constructed objects match. Other scenes using those prefabs inherit this change. Original materials and vendor assets remain intact; the existing art study keeps its saved comparison materials.
- Open question: final visual strength and atmosphere require user review.

## Use cases

### UC.1: Player enters and explores World

Start loads World. Point-and-click movement uses the saved terrain navigation and existing camera controls.

### UC.2: Player gathers and builds basic things

The clearing contains 17 harvestable trees, six loose sticks, five rocks, four birch logs, one crafting bench and one woodcutting axe. The bag begins empty. Birch is the starting tree; existing Oak/Pine skill gates remain.

### UC.3: Designer paints and extends the level

The terrain has Moss, Earth and Stone layers, with only a uniform Moss base applied. Its central area is flat at world Y=0; a low rim begins 23 m from the center. The 65×65 heightmap provides one-metre sample spacing. Props use whole-number X/Z placement.

## Requirements

- **R.1.1:** World shall contain the existing playable character, camera, HUD and working session bindings.
- **R.1.2:** The terrain shall remain a native editable Terrain with matching collider and saved navigation.
- **R.1.3:** Start shall enter World, which shall be enabled in build settings.
- **R.2.1:** Starter props shall support existing pickup, harvesting, fire construction and crafting behavior.
- **R.2.2:** Terrain, placed props and runtime gameplay objects shall use the installed Retro shader family.
- **R.3.1:** Further painting and saved scene edits shall not be overwritten by the authoring helper.

## Scenarios

### UC.1: Player enters and explores World

#### S.1.1: Enter from Start

- Given: the title scene is running.
- When: the player clicks Start.
- Then: World replaces the title, with an initialized player/session and no title presentation left alive.

#### S.1.2: Walk and approach resources

- Given: the player is at spawn.
- When: they click terrain or a starter resource.
- Then: movement reaches the aligned destination or valid interaction cell.

### UC.2: Player gathers and builds basic things

#### S.2.1: Gather, build and craft

- Given: the placed sticks, rocks and logs.
- When: the player gathers supplies and uses the existing recipes.
- Then: they can create a fire, make/drop a bench, craft/equip a Stone Axe and harvest Birch, with Retro materials on the new objects.

### UC.3: Designer paints and extends the level

#### S.3.1: Edit saved terrain

- Given: World is open outside Play Mode.
- When: the designer selects World Terrain - paint here.
- Then: native terrain sculpting/painting remains available with three prepared layers.

## Authoring and settings

Scene: `Assets/Scenes/World/World.unity`. Terrain, navigation, renderer and 40 new materials live under `Assets/Scenes/World`. Edit these saved assets directly. `Tools/AuthorWorld.cs` Run is completed one-time authoring and refuses replacement. Validate is read-only. RepairIntegerSettings is a targeted migration, not an ordinary workflow; rerunning it would overwrite later shader tuning.

Retro Lit uses point filtering, 128 texture resolution limit, 64 snapping points/metre in View mode, 32 color levels, screen dithering and full affine texturing. Meshes use vertex lighting, with unlit effects kept unlit. The terrain uses the package's Terrain/Lit shader, texel lighting for readable cast shadows, and native terrain layers. This approximates the study rather than copying its shader mathematics. World now applies the package full-screen presentation described in the 2026-09-30 update below. The study/title custom display shader remains separate.

Fog uses the study's cool color (0.25, 0.34, 0.36), linear range 8–26 m (updated from the initial 17–40 m), and ambient color (0.28, 0.33, 0.36). A World-specific URP renderer removes the earlier Flat Kit screen outlines. Existing shared renderers and vendor shaders are unchanged.

To add resources, instantiate the existing gameplay prefabs and register their WorldTarget in World session's Targets array. To add decorative props, assign a World Retro material. After sculpting terrain or changing solid obstacles, rebake the Navigation object's NavMeshSurface and save World. Never regenerate World to refresh navigation.

## Verification

- Authoring helper compiled; saved validation passed: terrain/collider/layers, player, 33 targets, 156 renderer material slots, saved NavMesh, supported shaders and zero missing scripts.
- Shader-mode authoring initially used the wrong numeric setter. Corrected to Material.SetInteger and retuned all 40 materials; current console then reported zero errors/warnings.
- WorldSmoke passed in 29.288 s: 33 valid prop approaches, real pointer terrain movement and seven pickups, normal-time fire/bench construction, bench placement, Stone Axe craft/equip and Birch harvest. Construction/crafting used service APIs; movement/pickups used pointer input. No runtime errors; all live renderer materials checked, including newly created objects.
- Final gameplay capture inspected after terrain texel-lighting correction. StartScreenSmoke passed a real pointer Start transition into World, including title cleanup.
- Full Play Mode run: 150/151 passed in 135.31 s. The only failure compared the burnt material name to the original exact name. Removed generated suffixes while preserving material GUIDs; all 17 Survival tests then passed in 10.83 s. No full-suite repeat after that naming-only correction. Final scene smoke reports zero console errors/warnings.
- No player build, web profiling or listening audition. Subjective appearance remains for user review.

## Task list

- [x] Inspect existing systems, shader package and authoring conventions.
- [x] Create terrain, scene, props, navigation and Retro material bindings.
- [x] Verify World interaction/building loop and inspect gameplay capture.
- [x] Verify existing gameplay regressions and title transition.
- [x] Finish authoring documentation and shared project context.


![World starter clearing](D:/Repos/FirstLight/Docs/Images/world-gameplay.png)


## 2026-09-30: Full-screen dithering and closer fog

User approved the proposed presentation change and explicitly excluded fisheye/CRT lens distortion. The World renderer now contains the vendor CRTEffect feature. A global Volume named World presentation - dithering and pixels references Assets/Settings/Rendering/WorldPresentation.asset. Camera post-processing is enabled. The pass runs before URP post-processing, after world lighting/fog; screen overlay HUD remains sharp.

The saved profile uses Custom RGB Sliders, 32 levels per channel, full-screen dithering, point filtering, pixel size 2 and dither pattern size 2 (the vendor calls this Scanline Size even when scanline strength is zero). Resolution scaling is off, giving half-resolution sampling relative to the current camera output. Existing material dithering remains enabled. Linear fog now starts at 8 m and reaches full strength at 26 m to suit the closer gameplay camera.

Barrel distortion is disabled and its strength/smoothing are zero. RGB/scanline overlays, random wear, chromatic aberration, tracking, interlacing and scrolling are also disabled. Brightness/contrast stay 1 and tint stays white. This uses the installed package without changing vendor code.

ConfigureWorldPresentation.Run is completed one-time authoring and refuses replacement. Tune the saved Volume profile and scene fog; Validate checks saved dither and unwanted-effect settings. Capture temporarily toggles live full-screen dithering for same-camera screenshots and restores its prior state. Saved and live Volume validation passed; both screenshots inspected, with visible dithering over fog and lighting and readable overlay UI. No full regression suite, build or web profiling was repeated for this presentation-only change. WorldSmoke passed again in 29.229 s: all 33 approaches, pointer movement/seven pickups, fire/bench construction, placement, axe crafting/equipment and Birch harvest, with zero runtime errors.

![World with full-screen dithering](D:/Repos/FirstLight/Docs/Images/world-dither-on.png)

### 2026-09-30: Softer presentation after user review

User liked the direction but found dithering and the fog line too aggressive. Increased full-screen channel levels from 32 to 64, reducing adjacent color-step contrast, and widened linear fog from 8–26 m to 6–40 m. Pixel/dither size 2 and all disabled lens/noise settings remain unchanged. Saved profile and World scene updated through ConfigureWorldPresentation.Soften. Saved/live validation and refreshed dither comparison capture passed; inspected final image shows a gentler transition. Console zero errors/warnings. No gameplay suite rerun for these numeric presentation changes. World left running for review. This supersedes the preceding 32-level/8–26 m settings.

### 2026-09-30: Persistent campfire light preview

Added Campfire - lighting preview at (1,0,-1), near spawn. Reuses the existing Retro campfire ring, looping flames and smoke as a permanent visual prop without fuel timer or interaction. Its child Campfire warm point light has warm orange color, intensity 5, range 8 m, hard shadows at 0.7 strength and 256px custom shadow resolution. Toggle that Light component to compare shader response. The solid 1.2 m footprint was included in a navigation rebake. Original gameplay fire rules/prefab are unchanged.

Light off/on captures show warm illumination on terrain around the fire; final lit image inspected. Saved validation passed 33 targets/177 renderer bindings, navigation and missing scripts. Console zero current errors/warnings. No gameplay regression rerun or build. World remains playing for review.

A newly encountered vendor terrain basemap TexelLit variant failed compilation on missing uvSplat01. Changed the World terrain material to the same shader's standard Lit mode, retaining filtering/dithering/snapping/fog. Current authoring source agrees. This supersedes the earlier terrain TexelLit recommendation. Vendor source remains unchanged; the unused variant itself is not repaired.

### 2026-09-30: Perceptual full-screen dither correction

User approved fixing color-band transitions while retaining the distant fog. The former project-owned full-screen pass replaced the presentation pass at that time; it has since been removed. The vendor CRT feature remains saved but disabled on WorldRenderer; vendor source is untouched. Existing CRTSettings profile supplies enabled/show-in-Scene-View, pass event, channel levels, useDithering, pixelSize and optional resolution scaling. CustomSliders enables quantization; None bypasses it. The project feature intentionally has no lens distortion, scanlines or tape effects; it always uses point sampling. Scanline Size no longer sizes the pattern: Bayer thresholds align with the low-resolution color pixels.

The pass downsamples the lit/fogged image as before, converts linear RGB to sRGB for perceptually spaced quantization, compares fractional channel values directly against centered 4x4 Bayer thresholds spanning the whole interval, then converts back to linear output. Uses levels minus one as the interval count and preserves alpha. The serialized shader reference keeps the asset reachable by the renderer; temporary render targets are RenderGraph-owned and the feature disposes its material.

64 channel levels, 2px sampling and fog 6–40 m remain unchanged. Compilation, shader support and single-active-feature validation passed. Inspected original/revised captures at maximum camera distance 15; the revised view greatly reduces broad near-field bands while preserving fog and crisp overlay HUD. Current Editor console zero errors/warnings; captured historical vendor terrain variant errors remain the separate known compatibility issue. No full gameplay suite, player build or web profiling. World left running zoomed out for user review. The former installer guarded against duplicate installation and provided comparison captures; that tool has since been removed.

Terrain variety is a separate issue: the Moss layer is still a solid-color 2×2 texture. The installed terrain shader's stochastic option offsets texture UVs to reduce tiling, so it cannot add variation to this source. Recommended next visual change is a restrained mottled ground texture with small flecks plus broader value variation, then evaluating stochastic sampling. No terrain texture or painting changed during this update.

![Perceptual dither at maximum zoom-out](D:/Repos/FirstLight/Docs/Images/world-dither-perceptual-zoomed-out.png)

### 2026-09-30: Moss tile color variation

User chose to preview adjacent tile colors. AuthorMossTiles.Run authored a 64x64 point-sampled color data texture, MossTileColors.asset, covering the full 64m terrain. Five muted greens span RGB (86,105,70) to (106,125,86), centered on the original moss. Deterministic broad noise plus restrained cell variation creates patches without an alternating checkerboard. One-metre cells use a half-metre layer offset to align centers with WorldGrid integer coordinates. No mipmaps or stochastic offsets. This is procedural tile-color data, not an AI-generated moss surface image; detailed moss generation remains deferred for this preview.

Only Moss.terrainlayer's diffuse texture, tile size and offset changed. Original Moss.png is retained; restore it with tile size (2,2) and offset (0,0) to revert. Terrain geometry, painted weights, other layers, shader, fog and lights remain unchanged. Existing paint tools continue to operate on the Moss layer.

Authoring compiled and saved asset validation passed: five shades, 64x64, Point, one mip level, correct layer reference and 64m coverage. Inspected normal/max-distance captures: visible irregular tile patches with readable props and campfire light. Current Editor console has zero errors/warnings. No gameplay suite/build repeated for color-only changes. World left playing at maximum zoom for subjective review. Run refuses replacement; edit the helper and saved texture deliberately for future palette tuning.

![Moss tile color preview](D:/Repos/FirstLight/Docs/Images/world-moss-tiles.png)

### 2026-09-30: Starter terrain palette

After accepting Moss tiles, user requested Dirt, Sand, Rock and Clay in the same style. Four new TerrainLayer assets and four 64x64 Texture2D color-data assets live alongside Moss in Assets/Scenes/World/Terrain. Each has five shades, deterministic broad patches and restrained cell variation, Point filtering, no mipmaps, 64m tile size and 0.5m offset. Dirt endpoints RGB100/78/56–120/96/72; Sand157/143/105–179/164/124; Rock98/103/100–118/123/119; Clay133/89/68–155/108/85. Same code-authored tile-color method as Moss; no AI bitmap generation.

AuthorGroundTiles.Run refuses overwriting existing palette assets. These are reusable paint layers, not separate renderer materials: they use the terrain's existing Retro material. Add desired assets via Terrain > Paint Texture > Edit Terrain Layers > Add Layer. Existing terrain layer list and painted weights remain intact. No mass registration or fifth-layer/add-pass compatibility claim: preview exercised four layers together only.

Validated all four assets' references, dimensions, filtering, five-shade palettes and UV settings. Temporary runtime terrain clone displayed Dirt/Sand/Rock/Clay left to right under existing fog, dithering and campfire lighting; inspected screenshot and restored original terrain in finally. Current console zero errors/warnings. Editor returned to Edit Mode for painting. No gameplay suite/build rerun.

![Starter palette: Dirt, Sand, Rock, Clay](D:/Repos/FirstLight/Docs/Images/world-ground-tile-palette.png)

### 2026-09-30: Actionable hover outlines

User requested subtle white hover outlines only when a target offers an action beyond Examine. WorldActionGui.OutlineTarget resolves the same selected-item/action list as the menu and requires at least one non-Examine action, or an available non-skill target with a non-Examine verb. Context menus, pause, inactive/offscreen targets and invalid selected-item combinations suppress it. WorldInput clears stale hover on unavailable input, Escape, consumed pointer input and camera orbit.

WorldHoverOutline is installed on World's session object. It creates temporary MeshRenderer shells for the hovered target using the vendor Retro Outline shader and shared Hover outline.mat. Original meshes/materials are preserved; shells have no colliders or shadows and are destroyed on target change/disable. Only active, visible source renderers contribute, including LOD visibility. Current implementation covers MeshRenderer props, not skinned characters. Off-white RGB0.85/0.85/0.82, target width2.5 output pixels scaled by camera depth/FOV; actual raster thickness varies with normals and existing retro snapping/downsampling. Geometry edges hidden below terrain naturally remain hidden. No vendor modifications.

InstallHoverOutline.Run is guarded one-time installation; Smoke resets the test scene, temporarily controls focus/input, checks pickup/tree positives, depleted Examine-only suppression, pause/menu/clear suppression and actual pointer pickup/UI blocking, then restores input settings/devices. Final compilation and smoke passed; stick/tree normal and zoomed-out captures inspected. Current console zero errors/warnings after repairing a runtime MaterialPropertyBlock initialization error with lazy initialization. Earlier unsuccessful attempts remain in retained logs. No full regression suite/build or skinned-target validation. World remains playing for user review.

![Hover outline on a tree at maximum zoom](D:/Repos/FirstLight/Docs/Images/world-hover-tree-zoomed-out.png)

### 2026-09-30: Slightly gentler color dithering

User found the pixel shader slightly strong. Made a conservative color-grain adjustment: full-screen RGB levels64 to96, reducing adjacent quantization-step contrast by about one third. Pixel size2, point sampling, dither enabled, fog and outline settings unchanged. The former tuning tool updated the saved profile and live volume instances; that tool has since been removed. Settings validation passed. Editor was stopped, so no new in-game capture or visual acceptance claimed; next Play uses the saved setting. This softens grain rather than changing block size. No gameplay tests/build for numeric tuning.

### 2026-09-30: Gloomy environment lighting

User reduced Sun intensity to0.25 but surroundings remained too bright. Saved World used Flat ambient RGB0.28/0.33/0.36 and fog RGB0.25/0.34/0.36, both independent of sun intensity. TuneWorldLighting now sets ambient RGB0.055/0.075/0.095, fog RGB0.075/0.105/0.125 and reflection intensity0.2. Preserved Sun0.25, campfire5, fog distances6–40 and presentation settings. Scene saved through Editor API using current scene content.

Saved/live values checked; final gameplay capture inspected: darker surroundings with readable props and stronger warm campfire contrast. Current console zero errors/warnings. No gameplay regression/build. World left playing for subjective review. Capture temporarily restores earlier ambient/fog/reflection for a reference image, then restores current values; user movement means captures should not be treated as a controlled pixel comparison.

![Gloomy World lighting](D:/Repos/FirstLight/Docs/Images/world-lighting-gloom.png)

### 2026-09-30: Campfire reach

User accepted gloomy lighting and requested greater campfire reach. Increased Campfire - lighting preview / Campfire warm point light / Light Range from8 to12m, intensity5 unchanged. Applied and verified live, then saved through Edit Mode and restarted Play. ExtendCampfireLight.Run performs this numeric adjustment. Range controls distance/falloff; Intensity controls brightness. No new visual capture or gameplay tests for this numeric change.

### 2026-09-30: Rolling peripheral mist

User considers fog at the screen edges essential and found the darkened distance fog too weak. Added screen-space peripheral mist to the former custom full-screen pass, composited before perceptual quantization so it shares the retro raster. Existing world distance fog6–40m and dark ambient remain unchanged. This is an atmospheric edge overlay, not volumetric/world-occluded fog.

Renderer feature exposes Edge Fog Strength0.65 and Edge Fog Color sRGB0.22/0.27/0.29. A rounded rectangular radius and broad smooth transition preserve the center; two scales of smooth procedural noise slowly drift in opposing directions, varying opacity and inner edge. Samples align to the low-resolution image. Strength0 bypasses the visible effect. HUD renders after the world pass. World remains dark, with a cool visible perimeter and warm fire contrast; campfire range12 retained.

Compilation/shader-support checks passed. Inspected normal/max-zoom captures showing smooth peripheral fog without a center cutoff and clear UI. Current console zero errors/warnings. No performance profiling, gameplay suite, build or recorded motion review. Animation is time-driven in shader; subjective motion and strength remain for user review. The former mist tool saved renderer settings and captured two camera views while restoring temporary focus/input state; that tool has since been removed. World left running zoomed out.

![Peripheral mist at maximum zoom](D:/Repos/FirstLight/Docs/Images/world-edge-fog-zoomed-out.png)

### 2026-09-30: Wider clear center

User liked rolling edge fog but wanted it confined closer to the camera perimeter. Moved smooth mask onset from normalized radius0.43 to0.70, keeping outer endpoint1.10, and reduced noise displacement0.16 to0.10 so wisps intrude less. Color, strength and drift speed retained. Synchronous shader import/support/error checks passed; refreshed normal/max-zoom captures and inspected final max-zoom view. Current console clear before final explicit reimport. World remains playing; no gameplay tests/build for mask tuning.

### 2026-09-30: Lighter side fog

User accepted top/bottom coverage but found left/right too strong. Scaled horizontal mask coordinate by0.88, leaving vertical coordinate, color, noise and transition settings unchanged. This reduces side coverage and softens corners. Shader import/support checks passed and refreshed max-zoom capture inspected.

### 2026-10-02: Custom presentation cleanup

The custom full-screen dithering and peripheral mist implementation has been removed. The enabled vendor CRT feature and existing WorldPresentation profile now own retro post-processing. Removed the orphaned renderer subasset and obsolete installation/mist/tuning tools; presentation validation now accepts only the vendor CRT feature. Earlier custom-pass and mist entries above describe historical results, not the current setup. Native scene distance fog remains available.

