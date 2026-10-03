# Dark forest scatter props

Status: Living and dead scatter assets accepted by the user on 2026-10-02
Updated: 2026-10-01

## 2026-10-02: Models folder organization

Moved this asset kit into Assets/Models/DarkForest using Unity AssetDatabase.MoveAsset. Scatter contains15 prefabs and MagicalWillow contains1; each family keeps its FBX/native meshes, materials and palettes alongside its prefabs. All52 moved asset GUIDs were preserved; all16 prefab mesh/material/texture dependencies and all three review scripts passed after migration. Tools now use the new paths. Packed editable Blender sources remain under Art/Forest to avoid implicit Unity Blender imports. Existing older forest reference assets were not reorganized. Arena remains clean in Edit Mode. Paths in this note have been updated to the current locations.

## Problem statement

FirstLight needs roughly ten reusable dark forest scatter props, including two grass variants, consistent with its existing faceted tree and monster assets. The elevated orbiting camera, PS1 horror rendering and muted forest palette guide the shapes.

## Proposal

Ten opaque 3D props are saved in `D:/Repos/FirstLight/Assets/Models/DarkForest/Scatter`. Each GEN_ prefab has one mesh, one shared Retro material, identity transforms and a ground anchor. Editable Blender sources with packed palette are in `Art/Forest/Scatter`; explicit FBXs and baked native Unity meshes are alongside the prefabs. Small below-ground roots/stone bases deliberately seat into terrain. These are decorative assets with no colliders, gameplay components or wind animation.

| Prefab stem (all begin GEN_) | Triangles | Height, metres | Placement idea |
| --- | ---: | ---: | --- |
| Grass_Short | 240 | 0.351 | Broken patches around stones and roots |
| Grass_Wiry | 286 | 0.786 | Thin seed-headed clumps along clearing edges |
| Fern | 868 | 0.400 | Broad fronds beneath trees |
| Shrub_Low | 770 | 0.729 | Low non-berry foliage masses |
| Bramble | 578 | 0.765 | Sparse thorn branches in neglected ground |
| Twigs | 228 | 0.083 | Fallen debris beside trunks |
| Branch_Broken | 120 | 0.212 | Larger forked deadwood |
| Log_Mossy | 298 | 0.788 | Hollow log with moss and bracket fungi |
| Roots | 326 | 0.475 | Gnarled low root crown |
| Stones_Mossy | 100 | 0.557 | Three angular stones with green upper faces |

### Shrub and grass research

Researched 2026-10-01 against Unity 6000.3 terrain documentation and Unity's terrain demo article. Unity recommends instanced meshes for arbitrary terrain details; instancing retains the prefab shader/material. Legacy non-instanced modes have simplified material handling. Billboard grass turns toward the camera, which Unity notes can look conspicuous with sparse clumps.

For this project's orbit camera and faceted references, use solid low-poly shrubs and shaped grass geometry now. Closed leaf blades preserve silhouettes without transparent texture regions. This is an art/compatibility choice, not a measured claim that meshes always outperform cards. Two-sided alpha-clipped cards remain suitable for finer leaf silhouettes if the style changes; overlapping cards can increase pixel shading costs, and require appropriate shader/shadow handling. Camera-facing billboards are more plausible for dense distant cover or a measured LOD need. Do not add a billboard/LOD system before profiling representative density on the target platform.

The new material enables instancing, and the existing Retro shader declares instancing variants. All ten prefab DetailPrototype.Validate checks pass with useInstancing=true. Actual terrain painting, runtime batching, wind, browser performance and distant LOD behavior remain untested. Use Terrain > Paint Details > Edit Details > Add Detail Mesh and enable Use GPU Instancing for a trial; profile and tune density/distance before broad placement. The prefabs also support ordinary scene scattering. Short grass, fern and twigs do not cast shadows; all receive shadows.

## Use cases

### UC.1: Environment author places forest detail

Drag a GEN_ prefab from the Scatter folder onto ground. Start with approximately 0.8-1.2 uniform scale and varied Y rotation; use smaller groups near landmarks and keep selectable resources visually distinct. Existing World, museum and arena placements were not modified.

### UC.2: Artist edits an original source

Open the matching GEN_ Blender file. Saved sources are authoritative after delivery. `Tools/CreateDarkForestScatter.py` and `Tools/AuthorDarkForestScatter.cs` refuse existing output, protecting manual edits. Re-exporting FBX alone does not update the prefab's baked .asset mesh: deliberately rebuild that native mesh while preserving its GUID and references. The palette stores numeric linear swatches, so Unity imports it with sRGB disabled, point filtering and no mipmaps; Blender uses Non-Color. Leaf, bark and wood swatches match existing material values within 8-bit quantization.

## Requirements

- R.1.1: Deliver ten distinct scatter props, including two grass variants, with GEN_ filenames.
- R.1.2: Preserve the existing forest's faceted shape language and Retro rendering.
- R.1.3: Provide editable sources, Unity placement prefabs and suitable ground pivots.
- R.1.4: Research shrubs/grass approaches and distinguish recommendations from measurements.

## Scenarios

### S.1.1: Place an imported prop

- Given: A delivered prefab in the Scatter folder.
- When: Instantiated at identity scale and a ground-level position.
- Then: Its geometry has a measured metre-scale footprint and no gameplay/collision behavior.

### S.2.1: Protect authored sources

- Given: Saved .blend, FBX, prefab or native mesh outputs.
- When: The initial generator/authoring helper is rerun.
- Then: It refuses replacement. This guard was inspected; no destructive rerun was needed.

## Task list

- [x] Inspect existing tree/monster assets and rendering conventions.
- [x] Research and choose the initial foliage approach.
- [x] Generate ten original meshes, editable sources and prefabs.
- [x] Validate topology/imports/pivots/materials and inspect Unity preview renders.
- [x] Record usage, rationale and verification limits.

## Verification and review

Blender 4.0.1 generation verified each triangle has positive area and each edge belongs to two faces. Unity 6000.3.20f1 validation passed for ten single-renderer, single-submesh prefabs: finite vertices, UVs, identity roots, bounds, ground contact tolerance, common palette material, no colliders/scripts and valid instanced detail prototypes. Flat normals increase imported vertex counts to 232-2337; triangle counts alone do not describe GPU cost.

`Tools/ReviewDarkForestScatter.cs` renders in an isolated preview scene and produces elevated/reverse contact sheets plus a same-scale arrangement with the existing dead tree and Warg. These are brightened studio previews, not World day/night captures. Both sheets and the final arrangement were inspected. Initial palette darkness was corrected before final verification. Review cleanup errors were corrected by detaching camera render targets before release. Initial authoring exceeded Pipeline's transport timeout, but files were produced and independently validated; the failed transport was not treated as proof of success.

No gameplay code was changed; no gameplay regression suite, Player build, terrain scatter benchmark or user visual acceptance is claimed. The arena remains clean in Edit Mode. Unity Search was opened to `t:prefab dir:Assets/Models/DarkForest/Scatter` for review.

Preview order: top row short grass, wiry grass, fern, shrub, bramble; bottom row twigs, broken branch, hollow log, roots, stones. Files in `D:/Repos/FirstLight/Docs/Images`: `GEN_scatter-elevated.png`, `GEN_scatter-reverse.png`, `GEN_scatter-context.png`.

## 2026-10-01: Dead plant variants

Added GEN_Grass_Short_Dead, GEN_Grass_Wiry_Dead, GEN_Fern_Dead, GEN_Shrub_Low_Dead and GEN_Bramble_Dead in the same Scatter folders. Total catalog is now fifteen prefabs. Dry grass leans outward, fern fronds droop with smaller leaflets, the shrub loses its foliage masses and gains branching twigs, and the bramble retains only a few shriveled leaves. Brown/tan UV swatches reuse the existing palette/material. These are separate decorative variants; no automatic plant-death behavior was added.

Each has a packed editable .blend, FBX, native .asset mesh and identity-root prefab. Triangle counts are 240, 286, 868, 614 and 370 respectively. Ground pivots are compatible with the living versions, while their wilted silhouettes change bounds. Tools/CreateDeadForestScatter.py derives only new assets from saved living sources; Tools/AuthorDeadForestScatter.cs imports them without replacing existing outputs. Tools/ReviewDeadForestScatter.cs validates both sets and renders the comparison sheets.

Blender manifold/positive-area checks passed; Unity validation passed for all five living/dead pairs, including UVs, finite geometry, bounds, pivots, one renderer/material, no colliders/scripts and instanced DetailPrototype validity. Elevated and reverse comparisons were inspected. SHA256 comparison confirmed all pre-existing scatter assets, metadata and source files remained unchanged. Arena remains clean in Edit Mode. User visual acceptance and dense-scatter performance are unverified.

Preview files: Docs/Images/GEN_scatter-dead-elevated.png and GEN_scatter-dead-reverse.png. Top row shows living plants; bottom row shows corresponding dead variants. Columns: short grass, wiry grass, fern, low shrub, bramble.

## References

- [Unity 6000.3: Grass and other details](https://docs.unity.com/en-us/engine/6000.3/manual/creating-environments/script-terrain/terrain-grass): instanced mesh recommendation, material behavior and sparse billboard rotation.
- [Unity terrain demo article, 2022-02-18](https://unity.com/blog/games/experience-the-new-unity-terrain-demo-scenes-for-hdrp-and-urp): instanced detail meshes and foliage rendering tradeoffs.
