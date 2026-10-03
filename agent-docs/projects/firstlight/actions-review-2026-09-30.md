# FirstLight action model investigation

Updated: 2026-09-30
Scope: source, authored assets and existing test assertions inspected. No implementation changes or fresh runtime tests.

## Subsequent implementation: 2026-09-30

Spec022 implements approved player capability grants and separate action, item-use and fire response types. The original investigation below is historical. Existing timers, reservations and temporary attempt state remain; this was not a complete executor rewrite. See the project context for verified results and the repository spec for current requirements.

## Model at investigation time

Action is not one universal abstraction. Seven SkillActionDefinition assets cover three tree harvests, flax processing and three fire operations. Six RecipeDefinition assets derive from SkillActionDefinition. Combat runs through CombatService/CombatQueue; pickup, Examine, equipment and dropping use direct interaction/session methods.

World skill interactibles own Actions lists. Stations own Recipes lists. Skills own global Recipes lists typed as SkillActionDefinition, which currently includes Make Crude Fire and Crafting Bench on Survival. GameDatabase.InventoryRecipes derives and deduplicates these skill lists. Global means no station visit; the existing interaction still selects a bag source and target rather than exposing an independent recipe-book button.

SkillActionDefinition combines identity/labels, skill requirements, duration/chance/repetition, consumed and retained items, source/target matching, success/failure loot, XP and fire-specific enum/prefab/log flags. SupportsWoodcuttingAxe changes verb/presentation for an equipped axe. RecipeDefinition adds ingredient resolution, ordered patterns and inline deterministic outputs, while forbidding incompatible inherited settings.

## Execution and ownership

WorldActionGui resolves visible world choices using the target list, selected item and action applicability. BootstrapSession resolves inventory choices through skill registrations. InteractionController owns the active selection and approaches world targets; inventory work starts in place.

Validation checks target and action skill gates, input availability and projected output room. Inputs are planned/reserved before work. Tick advances the duration, then resolves rewards, prepares world output placement, commits bag changes, applies world effects and XP, and publishes notifications. Repeat revalidates and reserves the next attempt. Failed chance attempts consume declared consumables; retained items remain. Cancellation releases uncommitted reservations. Session pause freezes progress rather than canceling work. Moving or selecting a replacement cancels the active interaction. Combat cancellation stops player attacks; nearby enemies may remain engaged.

Definitions are authored assets. The runtime PlayerDefinition owns bag/equipment/XP/health. InteractionController owns elapsed time, stack identities and active reservations. WorldTarget.SkillState owns harvest depletion/renewal and pending rolled rewards. Inventory and station work allocate temporary SkillInteractibleState objects even though they have no harvest lifecycle. World-target pending results can survive cancellation for delivery retry; temporary attempt state is discarded by Cancel. This distinction follows the code and was not fault-injection tested in this review.

## Complexity and recommended next discussion

The shared reservation/commit path has concrete value and should be preserved. The generic data shape is the main authoring problem: fire-only fields appear beside harvesting fields; recipes inherit unused loot/world settings and validate them away. SkillActionEditor currently draws all serialized fields without contextual grouping.

InteractionController also combines navigation, selection, input planning, timing, recipe contexts, fire execution, inventory material visuals and feedback dispatch. Several flags/null fields encode mutually exclusive states independently of IntentionState. Validation and discovery are distributed between GUI, session, definitions and executor. These are source observations, not newly reproduced defects.

A focused next design could retain a small common action definition for requirements/timing/XP and use concrete harvesting, crafting and fire behavior types with relevant Inspectors. Separate per-attempt execution data from persistent world harvest state. Keep combat's queue and simple immediate interactions separate unless a concrete feature requires convergence. Avoid a generic effect graph or additional registries without demonstrated need.

Decisions still to make: whether deterministic processing such as flax belongs in RecipeDefinition; whether global actions need a menu independent of source-target Use; and whether fire variants should be one focused subtype or separate behaviors. No design choice is approved by this investigation.

## Evidence locations

Runtime sources: Assets/Runtime/SkillActionDefinition.cs, RecipeDefinition.cs, InteractionController.cs, SkillInteractibleDefinition.cs, WorldActionGui.cs, BootstrapSession.cs, CombatService.cs, GameDatabase.cs and SkillDefinition.cs in D:/Repos/FirstLight. Authoring: Assets/Editor/SkillInteractibleEditor.cs and RecipeDefinitionEditor.cs. Inspected authored Actions and existing WoodcuttingTests, SurvivalTests and CraftingTests for cancellation, pause, input reservation and failure behavior. Existing prior regression results are recorded in project context; no new test pass is claimed here.

