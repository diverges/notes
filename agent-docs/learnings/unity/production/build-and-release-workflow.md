# Unity build and release workflow

## 2026-09-27: Scope and evidence

These are proposed production practices for a solo developer or small team. Unity 6.0 is the engine documentation baseline; Test Framework references use package 1.4. Check the game's installed versions before adopting API or command details. No build pipeline, automated test, device run, or release was executed for these notes.

The goal is to produce an identifiable game artifact, know what was checked, and retain enough evidence to reproduce a failure. Start with a repeatable local build, then automate repetitive work and important checks. A complicated service setup is not a prerequisite for reliable habits.

## Keep build configuration explicit

Unity 6.0 build profiles store configurations as versionable assets. They support separate configurations for a platform and can specify scenes. Platform profiles and custom build profiles do not share settings in the same way, so inspect the profile actually used.

Recommendation: name profiles for purpose, such as `Windows-Development` and `Windows-Release`, and document the settings that matter to interpreting a result: target, backend, architecture, scenes, development options, scripting defines, and content delivery configuration. Avoid relying on whichever Editor settings happened to be active last.

A development build helps investigation; a release candidate checks the configuration intended for players. Keep both useful, and label captures accordingly. Do not infer release performance from an Editor measurement or assume development diagnostics remain available in the distributed artifact.

## Establish one repeatable build entry point

Unity supports command-line automation, including batch mode and execution of an Editor method through `-executeMethod`. Exact arguments and their interactions must be checked against the installed Editor.

Recommendation: use one build function or script that both local work and CI can call. Give it explicit inputs and a deterministic output directory. It should reject missing configuration, capture the build result, and report failure to its caller. Keeping local and automated paths aligned makes CI failures easier to reproduce.

Unity's `BuildReport` exposes build results and information useful for investigating the process. Preserve a concise summary plus the relevant full log as an artifact. A produced executable is not sufficient evidence of success if the script ignored errors or packaged the wrong content.

Build machine prerequisites belong beside the build instructions: Editor patch, platform modules, SDKs, external tools, and expected access to private dependencies. Keep credentials in the build environment's secret storage, not the repository or notes.

## Add automation according to failure cost

Recommendation: automate fast rule tests and compilation checks early when they catch mistakes during frequent changes. Run more expensive platform builds and integration scenarios at a cadence that matches iteration cost and risk. For a solo project, a reliable daily or milestone build can initially be more useful than a large CI matrix that is rarely maintained.

Unity Test Framework supports Edit Mode and Play Mode testing, including supported target-platform runs. Package behavior and available options depend on the installed version.

Use deterministic tests for rules such as inventory capacity or score calculation. Use Play Mode scenarios for lifecycle and scene integration. Use a Player build for platform dependencies and build-only behavior. Human playtests still decide whether controls, feedback, and pacing achieve the intended experience; passing automation cannot establish that.

For example, changing a save schema warrants old-save fixtures and a Player save/load check. Changing an icon position may only need visual inspection at supported layouts. The verification burden should follow the failure mode rather than the number of changed lines.

## Test release-sensitive behavior in the intended Player

Unity's managed-code stripping removes code judged unused through analysis. Reflection and other indirect access can require explicit preservation.

Recommendation: exercise reflection-dependent serialization, native plugins, scene transitions, and external content in the intended release configuration when the game uses them. Preserve only what the application needs and verify the behavior, rather than treating a broad preservation setting as proof of correctness.

For performance, select a repeatable scenario and target hardware. Record build configuration, scenario duration, relevant frame-time behavior, and memory observations. Compare changes under comparable conditions. Separate profiling builds used to diagnose a problem from final acceptance runs used to judge the player experience.

## Keep a small release record and rollback path

Recommendation: retain the exact distributed artifact, source revision, configuration, content version, and a short verification record. Include unresolved defects that affect the release decision. Build numbers identify artifacts; they do not replace evidence of what ran.

A compact smoke pass might launch from a fresh install, reach gameplay, exercise input and pause, transition scenes, restart, and save/resume when supported. Also check an upgrade from the previously supported version when player data must survive updates. Choose representative hardware and record what remains untested.

Keep the previous release available and understand whether its save format can read data written by the new version. Restoring an old executable may not reverse a data migration. Decide compatibility and recovery behavior before release, then verify it with supported save fixtures. This is a release planning recommendation, not a claim that any particular game already supports rollback.

## References

- [Unity 6.0: build profiles](https://docs.unity3d.com/6000.0/Documentation/Manual/build-profiles.html)
- [Unity 6.0: Editor command-line arguments](https://docs.unity3d.com/6000.0/Documentation/Manual/EditorCommandLineArguments.html)
- [Unity 6.0: BuildReport](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Build.Reporting.BuildReport.html)
- [Unity Test Framework 1.4](https://docs.unity3d.com/Packages/com.unity.test-framework@1.4/manual/index.html)
- [Unity 6.0: code preservation](https://docs.unity3d.com/6000.0/Documentation/Manual/managed-code-stripping-preserving.html)
