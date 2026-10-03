# Unity project organization

## 2026-09-27: Scope and starting point

These are researched recommendations for a solo developer or small team moving from prototype to a maintainable game. Unity 6.0 documentation supplies the engine facts; it is not an upgrade recommendation. No repository, import, compilation, or build was tested for this note. Folder layouts below are examples, not Unity requirements.

The useful test of organization is whether someone can locate a feature, understand its dependencies, and change it without surprising unrelated systems. Start with a small structure and introduce boundaries when ownership, testing, or iteration time gives them a purpose.

## Organize around features and shared ownership

Recommendation: put game-owned assets under a recognizable root such as `Assets/Game`. Keep a feature's code, prefabs, and configuration near one another when they change together. Keep truly shared art, audio, and infrastructure separate. For example:

```text
Assets/
  Game/
    Features/
      Combat/        # Combat code, prefabs, and tuning assets
      Inventory/
    Shared/          # Assets used by several features
    Scenes/
    Editor/          # Authoring and build tools
    Tests/
  ThirdParty/        # Imported assets, where their importer permits
Packages/
ProjectSettings/
```

This is convenient for a small action game because adding an enemy attack mostly involves one feature folder. An art-heavy project may instead keep source art together by discipline. Choose the arrangement that matches actual editing patterns; do not relocate every asset merely to imitate this example.

Treat `Shared` as an ownership decision. Move code there after identifying multiple consumers and a stable responsibility, not because its future use is imaginable. A broadly named `GameManager` or `Utilities` folder makes discovery harder if it accumulates unrelated behavior. Prefer names that describe the job, such as `SaveStorage` or `SceneTransition`.

## Keep dependencies visible with a few assemblies

Unity assembly definitions group scripts into compiled libraries and let custom assemblies declare references. Without custom definitions, most game scripts belong to `Assembly-CSharp`. Definitions apply through subfolders unless another assembly definition or reference changes the boundary.

Recommendation: begin with a small number of meaningful boundaries, such as game runtime, Editor tools, and tests. Add a feature assembly when it isolates a dependency or reduces costly recompilation. Configure the Editor assembly for Editor-only compilation; a directory label alone is not a substitute for reviewing assembly settings.

For example, combat rules can depend on a small damage contract, while the combat presentation depends on those rules. The rules should not need the HUD to calculate damage. If two assemblies need references to each other, first reconsider their responsibilities: combine an artificial split, or extract the genuinely shared contract. Do not add interfaces everywhere simply to make a folder diagram look tidy.

An assembly is a compile boundary, not proof of good design. A project can still have hidden runtime coupling through global state or event channels. Review the lifetime and owner of cross-feature state alongside assembly references.

## Version the inputs needed to reproduce the project

Recommendation: track game assets and their `.meta` files, project settings, package declarations, package resolution, and build scripts. Exclude generated caches such as `Library` and `Temp` according to the repository's ignore policy. Choose large-file storage for substantial binary assets before repeatedly committing replacements; use locking where simultaneous editing cannot be merged usefully.

Unity stores asset identifiers and importer settings in `.meta` files. Moving assets in the Project window moves their metadata too; moves outside Unity must preserve both files.

A clean checkout should open with the recorded Editor patch, resolve packages, import assets, and produce a representative Player build. This is a proposed acceptance check, not something verified here. Record platform modules, SDKs, native plugins, and any required external content so setup does not depend on one person's machine.

## Manage packages and upgrades as deliberate changes

Unity's `Packages/packages-lock.json` records resolved dependencies. The Package Manager maintains it; manual edits are not the supported way to choose package versions.

Recommendation: retain the manifest and lock file in version control, and review their diffs together. For dependencies outside the registry, prefer immutable revisions or checked-in sources over an unrecorded local directory. Note why each significant package exists and which feature owns it. This makes removing an unused dependency easier than rediscovering its purpose months later.

Upgrade the Editor, rendering pipeline, or important packages in a focused change with a recoverable baseline. Identify affected content, import time, platform builds, and save compatibility before accepting the upgrade. Keep unrelated gameplay changes separate so a regression has a smaller set of possible causes. Patch numbers and package compatibility should come from the actual project, not this note's documentation baseline.

## References

- [Unity 6.0: assemblies](https://docs.unity3d.com/6000.0/Documentation/Manual/assembly-definitions-intro.html)
- [Unity 6.0: asset metadata](https://docs.unity3d.com/6000.0/Documentation/Manual/AssetMetadata.html)
- [Unity 6.0: package lock files](https://docs.unity3d.com/6000.0/Documentation/Manual/upm-conflicts-auto.html)
