# Unity production checks

Updated: 2026-09-27
Status: Recommended checks; select according to change and release risk. The original research executed no game checks; dated implementation guidance below has separate evidence.

## Make the project reproducible

Recommendation: version source assets and their metadata, project settings, the package manifest, and package lock file. Exclude generated caches such as `Library` and `Temp` according to the repository's actual ignore policy. Use visible metadata and text serialization for reviewable Unity asset changes, and preserve binary source assets through appropriate storage practices. Decide on large-file storage or locking from actual content volume and collaboration needs.

Record the exact Editor and build prerequisites. Prove setup with a separate clean checkout when onboarding or changing the build environment; do not delete someone's working checkout or caches as a substitute. Successful package resolution alone does not prove the game can build. The lock file records resolved package dependencies.

## Match verification to failure modes

Unity Test Framework supports Editor and Player testing. Record its installed package version, execution mode, and platform in results. The source here is Test Framework 1.4, not an instruction to replace the version installed in a game.

| Change or claim | Useful evidence | What it does not establish |
| --- | --- | --- |
| Deterministic rule or calculation | Targeted automated cases and Unity compilation | Correct scene wiring or game feel |
| Component/lifecycle behavior | Play Mode scenario in the actual prefab or scene, including relevant reset paths | Correctness on every shipping platform |
| Asset move or serialized schema | Unity import, reference inspection, representative existing content, migration cases | That unexamined content is unaffected |
| Save compatibility | Supported old/new data fixtures, round trip, relevant missing/corrupt-data handling | Compatibility with untested historical saves |
| Build/platform change | Target Player build and smoke run with intended settings | Performance on different hardware |
| Performance improvement | Repeatable target-device capture before and after | Faster behavior in unrelated scenarios |
| Feel, balance, or readability | Human playtest against stated criteria | Universal player preference |

This table is our verification recommendation. Do not turn every small edit into a full release checklist. Test behavior and failure modes, rather than getters or implementation details. For important regressions, preserve the smallest useful reproduction and its result.

## Build and profile before release pressure

Recommendation: establish a repeatable local Player build early. Add automated builds and targeted tests when integration errors or repeated manual work justify them. Record target platform, backend, scenes/content, stripping settings, build instructions, and the tested revision.

Documented behavior: managed-code stripping uses static analysis; reflective usage can require explicit preservation. Therefore Editor success alone is weak evidence for a reflection-heavy save or dependency system in a shipping build.

Recommendation: set performance budgets from target hardware and player experience. Capture device, build settings, scenario, frame-time distribution, memory behavior, and before/after results relevant to the claim. Investigate the measured bottleneck before choosing pooling, jobs, or ECS. Unity documents profiling target builds to obtain more realistic performance evidence; the linked guide is 6000.5.

## A small release smoke pass

Adapt this recommendation to the game's features: launch a fresh Player build; reach representative gameplay; exercise input, pause, scene transition, restart, and save/resume when present; inspect critical errors; and check performance on representative hardware. Include the highest-risk changed content and the minimum supported environment when feasible.

Keep one concise record: revision/build, environment, scenarios, results, unresolved defects, and reviewer. A reproducible failure and honest verification limit are more valuable than an unsupported claim that the game is production-ready.

## 2026-10-01: Verify observable outcomes

Guidance derived from FirstLight on Unity 6000.3.20f1; incident details remain in the Unity gotchas log.

Use realistic input units, normal-speed interaction and representative world scale. Local paths, dispatched UI events and enabled renderers do not establish long-distance travel, pointer delivery or visible placement.

Check actual captures, current diagnostics and fresh test artifacts; tool completion alone is insufficient. Let relevant frame/layout updates and shader compilation finish before comparing results. Repeat suspect renders under controlled conditions before assigning a cause.

Separate structural validity, runtime behavior, visual or listening acceptance, and target-build compatibility. A passing check establishes only what it exercised.

## References

- [Unity: ignore configuration](https://docs.unity.com/ja-jp/unity-version-control/ignore-files)
- [Unity Learn: project tips](https://learn.unity.com/course/tips-ibiq/tutorial/unity-tips)
- [Unity 6.0: lock files](https://docs.unity3d.com/6000.0/Documentation/Manual/upm-conflicts-auto.html)
- [Unity Test Framework 1.4](https://docs.unity3d.com/Packages/com.unity.test-framework@1.4/manual/index.html)
- [Unity 6.0: managed-code stripping](https://docs.unity3d.com/6000.0/Documentation/Manual/managed-code-stripping.html)
- [Unity: target-device profiling](https://docs.unity.com/en-us/engine/6000.5/manual/analysis/profiler/profiling-applications/profiling-target-device)
