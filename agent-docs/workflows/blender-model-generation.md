# Blender model generation for Unity

Validated: 2026-09-28 in FirstLight with Blender 4.0.1, Unity 6000.3.20f1 and Mixamo Humanoid idle/walk clips. This is a repeatable workflow for simple original low-poly assets. It is procedural authoring through Blender Python, not a text-to-3D service. Complex anatomy, facial rigs, production retopology and other Blender versions are outside the demonstrated scope.

## Navigation and evidence

| Resource | Purpose |
| --- | --- |
| FirstLight character authoring (`FirstLight: Docs/character-authoring.md`) | Working source, commands, file ownership and review procedure. |
| [FirstLight spec 006](../../projects/firstlight/specs/006-character-camera.md) | Accepted character/camera and dated validation evidence. |
| [Unity implementation gotchas](../learnings/unity/gotchas.md) | Observed failures and scoped corrections, including disconnected generated limbs. |

## Establish the visual and technical contract

Read the project's art direction, palette, camera framing and gameplay constraints first. Agree proportions and clothing before detailed geometry. A silhouette that looks good in a studio render can be unreadable at the gameplay camera distance. Treat model appearance and runtime correctness as separate acceptance questions.

Identify who owns motion. For a NavMesh-driven character, preserve the gameplay root and replace only the visual/Animator. Keep collision, navigation, feedbacks and camera anchors outside the animated skeleton. Reuse compatible Humanoid clips after validating the new Avatar; animation events should not become gameplay reward or damage authorities.

## Find and run Blender

Check PATH, the Windows uninstall registry and known install roots, then run the discovered executable with `--version`. Do not infer the active version from a folder name or assume it is current. FirstLight had both a 3.1 subfolder and a working 4.0.1 executable at D:/Program Files/Blender/blender.exe.

```powershell
& 'D:/Program Files/Blender/blender.exe' --version
& 'D:/Program Files/Blender/blender.exe' --background --python Tools/CreateWanderer.py
```

Blender supplies its Python runtime; this flow did not require installing Python packages, an MCP bridge or paid add-ons. Background execution permits file generation and rendering without operating the UI. Review the generator before running it: scene-clearing operations should run in a disposable Blender process, never against an unsaved user scene.

Blender API calls are version-sensitive. The tested script uses BLENDER_EEVEE, mesh normals recalculation and the built-in FBX export operator as available in 4.0.1. Verify those APIs before reusing the script under a different release.

## Choose one source of truth

Keep the generator, editable .blend and exported FBX with clear ownership. For Unity, keep .blend source outside Assets and place the explicit FBX inside Assets. This makes the import independent of studio objects and an implicit Blender conversion step.

A deterministic generator overwrites its outputs. If the user edits the .blend manually, either preserve that as a separate authoritative source or port the changes into the generator before regenerating. Never silently overwrite hand-authored edits. Preserve a working visual/prefab fallback before replacement; retain .meta files and stable asset paths during iteration.

## Construct geometry that deforms

Build broad forms and inspect their silhouettes before adding small details. Low-poly shading works best when loops describe shoulders, elbows, hips and knees rather than merely tapering from one end to the other. Add a few loops near a bend to control its shape; extra subdivisions without a shape purpose are not useful.

For a continuous garment, share mesh vertices through the joint. Bridge sleeve loops into torso armholes. Join trouser tubes through a crotch seam and hip surface. Separate clothing layers such as belt, cuffs and boots can overlap deliberately, but inspect their boundaries in posed views. A single Blender object can still contain disconnected islands; joining objects is not the same as joining topology.

Useful generator invariants for each intended closed garment surface:

- All faces are connected through shared edges/vertices.
- Every undirected edge has exactly two incident faces.
- No unintended loose vertices remain.
- Skin weights exist for every deforming vertex and sum to one.
- Normals face outward after recalculation.

The first two invariants were implemented and passed for FirstLight's shirt/sleeves and hips/legs. They do not detect all self-intersections, inverted volumes or ugly deformation. Open surfaces, such as a deliberately open garment, need a different boundary expectation.

## Skeleton, skinning and materials

Create the armature in a consistent rest pose and coordinate system. Give bones stable names and an unambiguous parent hierarchy. FirstLight used a simple 22-bone humanoid with Mixamo-style names, which Unity mapped successfully; names alone do not guarantee mapping for another rig.

Assign vertex groups matching the deform bones and an Armature modifier. Blend influences across joints instead of assigning every torso vertex to the spine and every sleeve vertex to the upper arm. Keep root placement, scale and sole height intentional. Retargeting different proportions requires visual review even when Avatar validation succeeds.

Use the project's palette as material input. The tested Blender script converts sRGB swatches to linear values for shader inputs. Unity uses explicit project-owned URP materials with the intended palette colors; do not assume Blender shader graphs will transfer. Flat normals and material boundaries can increase the imported vertex count, so report source vertices and triangles separately from engine counts.

## Export and import deliberately

Select only the mesh and armature. The working FBX export uses `use_selection=True`, mesh/armature object types, forward `-Z`, up `Y`, `add_leaf_bones=False` and `bake_anim=False`. The last setting is appropriate because animation clips are separate assets; it would omit authored animation if that were the intended deliverable. Studio lights and cameras remain in the .blend, outside the export selection.

Stop Unity Play Mode, confirm the transition completed, refresh/import the FBX and configure Humanoid/CreateFromThisModel. Inspect the nested tool result and Avatar `isValid`/`isHuman`. A shell exit code or successful transport wrapper is not proof of a successful import operation.

For the visual prefab, assign the existing Animator controller, disable root motion when gameplay owns translation, reconnect presentation/event relays and apply project materials/layers. Preserve collision dimensions and the stable camera anchor. Ordinary mesh edits to an existing FBX should not require rebuilding the whole Player prefab.

## Review in verifiable increments

Render the rest pose in Blender, then inspect the actual imported model with the runtime material/lighting. Capture idle and several walk phases from front, side and back. A capture camera should use character-relative positioning so turning the player does not accidentally change the intended view. Pose-sampling tools must deliberately set Animator state/speed and restore or reset the session afterward.

Inspect shoulders/armpits, elbows, hips/crotch, knees, cuffs, belt, foot contact and silhouette at close and gameplay distances. Run continuous movement and turns for foot sliding, cadence and transient deformation. On a placed prefab, assert that visual clips preserve placement-root position, rotation and scale; keep visual motion on descendants (FirstLight correction, 2026-09-30). Static captures and a valid Humanoid cannot establish full-cycle quality or user acceptance.

Use focused animation/presentation tests after rig or binding changes. After geometry-only refinements, topology assertions, import validity and direct visual review are the relevant checks. Run broader regressions when gameplay changes or new evidence warrants them; preserve the dates and scope of prior results instead of claiming they were rerun.

Record accepted appearance, final asset paths, generator/manual ownership, source counts, tested versions, verification limits and fallback location. In FirstLight the final accepted model had 700 source vertices, 1,294 triangles and 22 bones; these are evidence for that asset, not universal targets.
