# Feel integration research and designer review

Updated: 2026-09-28
Scope: First Light, Unity 6000.3.20f1, installed More Mountains Feel 5.9.1. Research and recommendations only; no integration or playback validation.

## Finding

Start with subtle footsteps driven by the Mixamo walk. This directly supports the GDD's quiet environmental audio and gives the Player prefab one reusable feedback sequence. A destination-ring pulse is the next useful candidate but requires changing the existing immediate-mode ring presentation. Sparse foot-contact particles are optional. These recommendations came from a separate designer agent reviewing the GDD, spec006 and installed source; user acceptance remains open.

Mixamo idle/walk, Cinemachine and Bootstrap remain the selected milestone scope. Feel supplies presentation effects, not the humanoid model, locomotion controller or foot-contact detection.

## What is installed

Assets/Feel/readme.txt identifies version 5.9.1. The current public documentation advertises 6.1, so local code is the authority for available APIs; no upgrade is recommended by this research.

| Area | Installed capability | Relevance |
| --- | --- | --- |
| MMFeedbacks | MMF_Player sequences with timing, cooldown, playback and stop/reset controls; sound, particle, transform, animation and other feedback types | Designer-authored presentation called by small gameplay/presentation hooks |
| Third-party integrations | Cinemachine, URP/HDRP, UI Toolkit, TextMesh Pro, VFX Graph, Post Processing and Nice Vibrations folders | Use only integrations needed by the game; folder presence does not prove a dependency is active |
| MMTools | Sound management, tweens, springs, UI/progress helpers and other utility systems | Available for later concrete needs; no reason to replace existing working systems |
| Nice Vibrations | Haptics, samples and supporting tools | Defer for this web-first mouse/keyboard milestone |
| Demos | General, URP and HDRP demos | Examples to inspect; not production systems to copy wholesale |

Local MMF_CinemachineImpulse includes both MM_CINEMACHINE and MM_CINEMACHINE3 branches and requires an impulse listener. This establishes source-level support for Cinemachine 3, not a tested project configuration. Cinemachine was absent from the inspected manifest.

## Designer recommendations

### 1: Soft footsteps on the player

Create Player/Feedbacks/Footstep with MMF_Player and MMF_Sound, outside the replaceable model. Add a thin foot-contact relay on the model's Animator object. Author contacts for the selected Mixamo walk and gate playback on measured motion, grounded validity and session pause state. Use one contact source; do not also run an independent timer that doubles the sound.

Trial two or three restrained clips with pitch variation around 0.97–1.03 and low volume. These are starting experiments, not approved values. MMF_Sound supports RandomSfx, pitch/volume ranges and Cached or Pool playback. Cached is sufficient for short cues if overlap is unnecessary; pool only if listening reveals truncated cues. Event mode needs a receiver, so it is not the minimal setup. A global sound manager is unnecessary for this first effect.

Acceptance: contact cues match the visible gait through acceleration, turns and blends; none occur while idle, blocked, paused or stationary working. Listen at both zoom bounds and during a minute of repeated walking. Preserve environmental audio prominence. Test pause/reset/disable and resume without delayed catch-up sounds.

### 2: Accepted destination-ring pulse

Use a single world marker, owned by Bootstrap/world-input presentation. After a ground command is accepted, position it at the accepted destination and run a short MMF_Scale sequence on the marker visual. Trial a 0.2–0.35-second contraction using the existing gold palette and similar readable footprint. New accepted commands reposition and restart that same instance.

The existing ring is drawn as lines in WorldActionGui; it has no transform for Feel to animate. Implementing this candidate requires replacing that visual and adding an explicit accepted-ground-command presentation hook. Do not parse report strings or show both old and new rings. Do not trigger a fresh command pulse for automatic object approach. Preserve the ring's destination-indication role after the initial pulse.

Acceptance: one marker, no pulse on rejected routes or consumed UI input, rapid commands do not accumulate effects, and marker lifetime follows active destination/cancellation. Keep it in world space rather than parenting it to the moving player. This is useful but a larger integration than footsteps.

### 3: Optional contact particles

Share the footstep trigger with MMF_Particles in Emit mode and an existing particle system. Trial 1–3 short-lived subdued particles per contact on suitable Bootstrap ground; the component default is 100, which is unsuitable here. Use world-space simulation/position consistently so emitted particles stay behind instead of following the player.

Omit if particles resemble smoke, obscure the feet, look wrong on the test surface or require a new surface-classification system. This is optional polish after gait/audio acceptance.

## Ownership and implementation boundaries

- Player owns its feedback sequences and a small presentation bridge. Model replacement changes the Animator relay/rig references, not gameplay rules.
- Animator owns skeletal motion; NavMeshAgent owns root motion; CameraTarget remains stable outside the skeleton.
- Feel must not animate Player root/collider/CameraTarget transforms or decide rewards, damage, movement completion or timings.
- Camera receivers belong on GameplayCameraRig when later effects justify them. No camera shake, bob, FOV kick or time-scale effect is recommended for routine movement.
- UI and resource effects belong with their own presentation owners.
- BootstrapSession gates pause explicitly rather than setting Time.timeScale. Explicitly stop short audio, suppress new contacts, and pause/clear particles as appropriate. Merely selecting scaled-time feedback is insufficient. Reset/disable must leave no active effects.
- Existing FirstLight.Runtime references only Input System and AI Navigation. In this import, feedback asmrefs resolve to MoreMountains.Tools.asmdef. Add the appropriate assembly reference for a typed adapter during implementation and verify compilation; do not infer assembly names from namespaces.
- Keep project-owned adapter/prefab/audio settings under Assets/FirstLight; do not edit vendor sources.

Defer humanoid squash/stretch, camera shake on work ticks, post-processing bursts, hit-stop, reward stingers, haptics and gathering impacts. Idle-only stationary work does not provide an animated contact to justify strong impact feedback.

## Assets and verification limits

NiceVibrations includes grass/gravel footstep WAVs, but they were not auditioned. Its HapticPackCClicense.txt lists mixed source licenses, including noncommercial entries. Imported samples are therefore not assumed to be production-ready audio; select clips with established project-use provenance before adopting them. No legal conclusion about individual samples was made.

Inspected local readme, feedback code, assembly references, Bootstrap pause logic and destination-ring drawing. Consulted official Feel documentation. No Editor access, import repair, compilation, runtime playtest, audio audition, performance check or browser test. This is not an implementation gotcha.

## References

- Official overview and versioned documentation landing page: https://feel-docs.moremountains.com/
- Official feedback catalogue: https://feel-docs.moremountains.com/list_mmfeedbacks.html
- Official sequence/core concepts: https://feel-docs.moremountains.com/core-concepts.html
- Official getting-started example, including child MMF_Player containers: https://feel-docs.moremountains.com/getting-started.html

