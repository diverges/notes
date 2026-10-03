# Magical willow model

Status: Delivered; user visual acceptance pending
Updated: 2026-10-02

## 2026-10-02: Models folder organization

Moved this asset kit into Assets/Models/DarkForest using Unity AssetDatabase.MoveAsset. Scatter contains15 prefabs and MagicalWillow contains1; each family keeps its FBX/native meshes, materials and palettes alongside its prefabs. All52 moved asset GUIDs were preserved; all16 prefab mesh/material/texture dependencies and all three review scripts passed after migration. Tools now use the new paths. Packed editable Blender sources remain under Art/Forest to avoid implicit Unity Blender imports. Existing older forest reference assets were not reorganized. Arena remains clean in Edit Mode. Paths in this note have been updated to the current locations.

## Problem statement

The user requested a magical tree resembling a willow with dark red fruits, matching the accepted faceted dark forest assets.

## 2026-10-02: Fruit hangs beneath upper branches (current)

User clarified that the six fruits should hang beneath the upper branches. Reflected each saved fruit/stalk/calyx group below its existing branch attachment, preserving the rest of the mesh. Exactly six fruits remain, each verified below its attachment; fruit geometry spans3.786-4.482m. Overall height is now4.762m above origin because fruit no longer projects over the foliage. Root footprint and canopy width are unchanged. Same prefab/source updated, with the prior source in Revisions/GEN_MagicalWillow_6fruit_above.blend. Topology and Unity asset/footprint checks passed; refreshed two-angle preview inspected. User appearance acceptance pending. This supersedes above-crown placement described below.

## 2026-10-02: Six-fruit refinement (placement superseded)

User requested at most six fruits. Removed twelve complete fruit/stalk/calyx groups from the saved source, retaining six spaced around the upper canopy. Trunk, branches, foliage and roots were preserved. Updated the same Blender/FBX/native mesh and refreshed both previews. Current mesh has 4,884 triangles, 3,198 source vertices and 12,314 imported vertices. Blender topology and Unity footprint, top-fruit, material, pivot and bounds checks passed; preview inspected. Original 18-fruit source is preserved as Revisions/GEN_MagicalWillow_18fruit.blend. Tools/ReduceWillowFruit.py records this guarded edit; the initial generator also now creates six fruits. Arena remains clean. This supersedes earlier fruit counts; visual acceptance remains pending.

## 2026-10-02: Creepy spiral revision (fruit count superseded)

User requested thinner branches, fruits on top, a creepier swirling trunk and a large tree occupying a2x2 square. Revised the same prefab/source: two gnarled trunk strands spiral around open negative space; main limb radii reduced from0.15 to0.083m; narrower crown masses expose18 fruits growing above the upper crown. Hanging stems are thinner and longer. Total tree height above origin is5.262m; mesh bounds4.052 x5.405 x4.052m, including0.143m buried root ends. Root vertices below0.5m remain inside x/z +/-1m, interpreting2x2 as the ground footprint with decorative canopy overhang. This interpretation was stated in chat; no gameplay2x2 occupancy/collider or harvesting behavior was added.

Current mesh:5,364 triangles,3,510 source vertices,13,610 imported vertices. All fruit geometry is above4.279m. Blender manifold/positive-area checks and saved-source root/fruit-height assertions passed. Unity checks independently verified updated geometry, identity transforms, root footprint, fruit UV region height, material and bounds. Fresh front/elevated-reverse views were inspected; scale view includes four1m ground squares plus the existing dead tree. Arena remains clean in Edit Mode. Visual acceptance pending; no gameplay or performance test.

Tools/UpdateMagicalWillow.cs explicitly rebuilds native mesh buffers in place, preserving the saved mesh and prefab references. Original v1 source, FBX, palette and manifest are recoverable under Art/Forest/MagicalWillow/Revisions. CreateMagicalWillow.py remains guarded; its --revise switch backs upv1 and refuses existing backups. Earlier dimensions and topology below describe the superseded initial draft.

## Initial draft (superseded by revision above)
## Proposal and delivered asset

GEN_MagicalWillow has a crooked bole, winding secondary trunk, spreading roots, nine arching limbs, a broad sage-green canopy, 36 hanging leafy strands and 18 faceted wine-red fruits. It is a static decorative prop with one mesh, one opaque instancing-enabled Retro material and one packed numeric palette. No glow, lights, wind, harvesting, collision or scene placement was added.

Prefab, FBX, native mesh and palette/material are in D:/Repos/FirstLight/Assets/Models/DarkForest/MagicalWillow. Editable source is Art/Forest/MagicalWillow/GEN_MagicalWillow.blend. All generated asset filenames begin GEN_. The mesh has 5,130 triangles, 3,393 source vertices and 12,737 imported vertices (flat shading splits vertices). Bounds are 4.052 x 3.698 x 4.052 metres. The canopy is about 3.56m above the placement origin; the root bases extend 0.139m below it for seating.

## Use cases

UC.1: An environment author drags GEN_MagicalWillow.prefab onto terrain and adjusts uniform scale/rotation.

UC.2: An artist edits the packed Blender source and deliberately updates the FBX and baked native mesh, preserving Unity GUIDs. The saved source takes ownership after creation; rerunning the initial generator/author helper refuses existing output.

## Requirements and scenarios

- R.1: Willow-like silhouette and dark red fruits; inspected from low and elevated reverse views.
- R.2: Existing forest style and metre-scale placement; compared against the existing dead-tree prefab.
- R.3: Editable source and Unity prefab; both saved, with identity root transforms and consistent palette import.
- S.1: At a ground placement origin, hanging growth remains visible and buried root ends seat into the terrain. Actual uneven World terrain placement is untested.

## Task list and verification

- [x] Generate original mesh, packed Blender source, FBX and Unity prefab.
- [x] Verify manifold edges and nondegenerate triangles in Blender4.0.1.
- [x] Validate Unity6000.3.20f1 finite vertices, UVs, single renderer/submesh, identity pivot, material and bounds.
- [x] Inspect two-angle preview and same-scale existing-tree comparison.
- [x] Preserve open scene: arena remains clean in Edit Mode.

Initial validation used an overly narrow root-depth tolerance and rejected the 0.139m buried winding root ends. Inspection confirmed their intended seating role; the check now permits up to0.18m. No geometry was hidden or removed to satisfy validation. No gameplay tests, Player build, wind animation or performance benchmark was run for this asset-only addition. Visual acceptance remains with the user.

Tools: Tools/CreateMagicalWillow.py (reuses geometry primitives from CreateDarkForestScatter.py without running its writes), Tools/AuthorMagicalWillow.cs and Tools/ReviewMagicalWillow.cs. FBX changes do not automatically refresh the prefab's native .asset mesh. Palette imports use sRGB=false, point filtering, no mipmaps and uncompressed data.

Preview files: Docs/Images/GEN_magical-willow.png and GEN_magical-willow-scale.png. Studio material copies use brighter ambient lighting for inspection; previews do not establish World day/night appearance.