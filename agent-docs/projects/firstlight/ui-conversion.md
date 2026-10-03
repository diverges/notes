# First Light: Retro UI conversion



Status: Implemented; final visual acceptance remains with the user

Updated: 2026-10-01



## Problem statement



The game UI retained its preferred blocky layout but used smooth line icons, default typography and bright brass borders. Some item descriptions were placeholders or contained stale weapon damage. The player needs readable, world-consistent information without a layout redesign.



## Proposal



The user explicitly authorized code changes after reviewing the style direction. Preserve existing panel placement and interactions; use VT323, bone text, charcoal panels, cool inset slots and dark rust/burgundy bevels. Use 12 logical pixels at the sides and top health inset. Following user feedback, the log and navigation frame sit flush at the bottom. General panel padding is 12 and header gap 8; retain Bag/Equipment compact padding and control sizes.



In scope: HUD, navigation, Bag/Equipment/Skills/Settings, crafting styles, context menus, hover/progress text, Start button, missing active-skill icons and accurate item inspection. Out of scope: layout redesign, target HUD, new comparison panels, gameplay/balance and audio-bank changes. User follow-up: Cooking, Smithing and Agility are removed from the Skills panel. The panel now lists only the six catalog skills, retaining Strength and Block.



VT323 is an implementation choice within the approved pixel-font direction, licensed under SIL OFL 1.1. It is not Resident Evil's original font. Pixel icons use the approved game palette, not historical screenshots' world colors. Runtime display can interpolate or scale colors; exact-palette validation applies to source pixels.



## Use cases



### UC.1: Player reads and navigates game panels



Keep health upper-left and log lower-left. The fixed 218px-wide navigation frame sits lower-right, flush at the bottom. Bag, Equipment, Skills and Settings open as separate panels 8px above it; they do not expand or merge into the bottom frame. Crafting remains centered. This reflects the user's explicit clarification after an intermediate joined-panel trial. Maintain live Bag/Equipment/Skills and paused Settings behavior.



### UC.2: Player examines an item



Keep the current Examine action and message log. Equipment inspection appends its slot, current authored base damage and interval when applicable, and nonzero stat bonuses. Ordinary items retain their description. These are base weapon values, not a promise of final damage.



## Requirements



- R.1.1: Existing panel arrangement and controls shall remain recognizable and functional.

- R.1.2: The log and navigation frame shall touch the bottom edge; side margins and the health top inset shall be 12 logical pixels, subject to runtime pixel rounding.

- R.1.6: The navigation frame shall remain fixed while separate panels open above it. Cooking, Smithing and Agility shall not appear in the Skills UI.

- R.1.3: Panels shall use dark rust/burgundy borders and charcoal backgrounds, with readable pixel-style text.

- R.1.4: New icons shall use only game-palette colors with binary transparency and point-filtered imports.

- R.1.5: AI-generated asset filenames shall start with GEN_.

- R.2.1: Equipment inspection shall derive combat values from current item data, so edited profiles do not leave stale damage text.

- R.2.2: Examine shall use the same inspection content for ground, carried and equipped items.



## Scenarios



### UC.1: Player reads and navigates game panels



#### S.1.1: Open existing panels



- Given: A running game at a supported review resolution.

- When: The player selects a navigation button.

- Then: Its separate panel opens 8px above the fixed button frame, remains onscreen, and closes through the existing control.



#### S.1.2: Read colored icons



- Given: Bag, navigation or active combat skills are visible.

- When: Their icons render.

- Then: Their silhouettes and colored pixel treatment remain distinct from the dark background.



### UC.2: Player examines an item



#### S.2.1: Inspect an edited weapon



- Given: A weapon's attack profile or stat bonuses change.

- When: Examine runs.

- Then: The text reflects the current profile/bonuses and explains that final damage depends on combat stats.



## Implementation



Canonical source: D:/Repos/FirstLight. Styling lives in Assets/UI/Bootstrap.uss and StartScreen.uss; shared font and IMGUI colors in Assets/Runtime/RetroUi.cs. Resources/VT323-Regular.ttf supplies the font; Assets/UI/Fonts/OFL.txt retains its license.



Following user feedback about undersized icons, navigation art is displayed at 40px within its unchanged 44px buttons (previously 24px); Bag icons use 54px, Equipment 44px, skill icons 32px, and crafting icons have 4px insets. These changes enlarge source silhouettes without regenerating or adding detail. Narrow objects retain their natural proportions.



Ten original 24x24 icons in Assets/UI/Retro replace four runtime navigation drawings and the heart, provide Block/Strength/Proficiency (heart reused for Vitality), and correct SpiderBlood/SpiderSilk. The other 21 item icons already matched the palette and were retained; original spider PNGs remain available. Fourteen authored item descriptions were refreshed. ItemDefinition.InspectionText and EquipmentItemDefinition's override centralize Examine text.



Tools/AuthorRetroUi.cs is a completed guarded migration. Do not rerun it to validate: it authors descriptions and bindings. Tools/RetroUiResolutionReview.cs captures layout at three resolutions. Tools/RetroUiReview.cs exercises native pointer input without event fallbacks; repeated editor runs showed intermittent event-delivery failures, so an isolated passing run is not proof of repeatable input across all resolutions.



## Verification and limitations



Initial native-pointer run passed all four panel open/close actions, Bag context Examine, font loading and margins. A later fresh-session run again passed all four navigation toggles but failed to receive the Bag context click. Repeated fixed-resolution input runs were unreliable in the editor; no product input workaround was added. Final layout captures and geometry assertions passed 1280x720, 1920x1080 and 1024x768: both bottom sections flush, fixed dock width, separate panel parentage and 8px gap. All ten new PNGs contain only palette colors and binary alpha. World context and centered crafting were also captured at 1280x720 and visually inspected. The full Play Mode run passed 217/218; the remaining catalog assertion expected the old plain description. Updated it to InspectionText, then all 10 catalog tests passed. Both new dynamic-inspection tests passed in the full run. The updated catalog-only Skills/progress/tooltip test passed after removing the three placeholder rows. A subsequent HUD run had one stale nine-skill expectation and a processing timeout; corrected the expectation to six skills without changing gameplay timing. The final BootstrapTests rerun passed all 29/29 tests. Start-screen capture was also visually inspected. Human visual acceptance and a standalone Player build are not claimed.



## Task list



- [x] Preserve panel layout and implement approved style changes.

- [x] Create missing icons and correct the two off-palette item icons.

- [x] Add accurate data-driven equipment inspection.

- [x] Finish regression tests and supplementary captures.

- [x] Record final verification and project context.



## 2026-10-01: Compact message history

User approved replacing the always-open bottom-left console with up to three compact messages, newest at the bottom. Each has an independent 10-second unscaled timer and a dark rust underline that shrinks from the right with a fading tip. User feedback doubled underline height to 2 logical pixels. New messages push older ones up; a fourth evicts the oldest compact entry without queuing it to replay. Full history retains the existing 100-entry bound. A caret-only button expands/collapses history; no visible Log label. The compact area becomes only that caret when all messages expire and its empty footprint does not block world clicks.

Timers pause when game-paused, while hovering the message area, or while history is expanded. Expanded history stays open until collapsed, follows new messages only when already at the bottom, and retains the Latest control when reading older entries. Examine reports now include object/item names before the descriptions, for world, carried and equipped targets. Other HUD layout remains unchanged.

Implementation: BootstrapHud.Log.cs owns feedback presentation and countdowns; Bootstrap.uss owns layout and the 2px rule. No generated raster assets are required for caret or fading line. CompactLogTests verifies independent expiry, overflow, no replay, bounded history, hover/pause/expanded states, complete long messages and world-click passthrough. Three new tests passed. Bootstrap regression run passed 28/29; adapted history-scroll fixture to wait for opening layout/auto-scroll, then both GameLog tests passed. Tree Examine regression also passed. In-game 1280x720 captures of compact/expanded/expired states were inspected; caret open/close passed with explicitly dispatched UI Toolkit pointer events (not native device input). Native device input and Player build are not claimed.

## References



- [VT323 source font](https://github.com/google/fonts/tree/main/ofl/vt323): downloaded 2026-10-01; copyright The VT323 Project Authors; SIL OFL 1.1.

