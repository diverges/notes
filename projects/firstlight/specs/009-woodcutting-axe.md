# Woodcutting axe

Current model correction (2026-09-30): Spec022 replaces the response-side axe flag with equipment-granted Chop and target Allowed actions. Chop and Punch share one tree response; both remain available with an axe. Accepted art and historical verification below are preserved.

Status: Complete; user accepted 2026-09-29
Updated: 2026-09-29

## Problem statement

Equipment currently moves between the bag and slots but has no character appearance. Trees offer Punch. The player needs a recognizable axe held in the right hand and a chopping motion when harvesting a tree.

## Proposal

Add a nonstackable Woodcutting axe with an original low-poly wooden handle and metal head, an inventory icon, ground pickup and right-hand visual. Equip it through the existing bag/equipment flow. Tree harvesting offers Chop tree instead of Punch while it is equipped. Empty hands or other right-hand equipment retain Punch.

Reuse each tree's existing action and all its numbers. Equipment changes presentation, not harvesting outcomes. Author an upper-body holding pose and looping chop clip on the existing Humanoid Animator; locomotion and navigation retain ownership of movement. Animation never awards loot or XP. The axe remains visible while idle, walking and paused.

- In scope: axe asset, right-hand attachment, holding/chopping animation, contextual labels, Bootstrap pickup and regression coverage.
- Out of scope: recipes, tool durability, bonuses, changed timings/XP/chances/yields/reach, other equipment visuals and new tree-falling effects.
- Tradeoff: the first animation is authored for the current Wanderer proportions; another character requires grip and pose review.
- Open questions: none for this milestone; final model and motion accepted 2026-09-29.

## Use cases

### UC.1: Player equips the axe

Pick up the axe and equip it from the bag. It occupies Right hand and follows the hand during movement, with a relaxed arm, the hand slightly forward, the shaft parallel to the ground and the cutting edge pointing down in the resting carry pose. Existing swaps, Unequip and Drop preserve item identity.

### UC.2: Player chops a tree

Select Chop tree, approach, face the tree and swing repeatedly while the existing harvesting timer runs. Cancel, depletion and pause stop or freeze the motion appropriately.

## Requirements

### 1: Equipment presentation

- **R.1.1:** The axe shall occupy Right hand and show exactly one held model when equipped.
- **R.1.2:** Unequip, replacement, Drop and Reset shall remove the held model; failed transfers shall preserve it.
- **R.1.3:** The axe shall have a dedicated ground visual and inventory icon, with no collision attached to the animated hand.

### 2: Tree interaction

- **R.2.1:** Equipped axe tree actions shall show Chop tree in hover/context/status; unarmed trees shall retain Punch.
- **R.2.2:** Existing tree durations, success chances, XP, loot, requirements, depletion and respawn values shall remain unchanged.
- **R.2.3:** Chopping shall animate only while performing a tree action, never during approach, other skills or combat.
- **R.2.4:** Pause shall freeze the motion. Cancellation, depletion and reset shall restore the appropriate holding or unarmed pose.
- **R.2.5:** Changing the right-hand item during harvesting shall cancel the current attempt without awarding a partial reward; the next click shall use the current equipment.

## Scenarios

### UC.1: Player equips the axe

#### S.1.1: Equip, walk and remove

- Given: The axe is in the bag and Right hand is empty or occupied.
- When: The player equips it, walks, then unequips, swaps or drops it.
- Then: The normal transfer rules apply; one model follows the right hand while equipped and disappears after removal.

#### S.1.2: Failed transfer and reset

- Given: The axe is equipped and the bag is full.
- When: Unequip is attempted, then Reset is used.
- Then: Failed Unequip preserves the axe; Reset removes it and restores the authored pickup.

### UC.2: Player chops a tree

#### S.2.1: Same harvesting result

- Given: A tree is available and the player meets its requirements.
- When: The axe is equipped and Chop tree is selected.
- Then: The player approaches, swings with the right hand and receives the unchanged harvesting result on the existing timer.

#### S.2.2: Lifecycle boundaries

- Given: A chop is in progress.
- When: The game pauses, resumes, cancels, depletes the tree or changes the right-hand item.
- Then: Pause freezes; resume continues; the other boundaries stop chopping without extra rewards or leftover attachments.

## Task list

- [x] Author axe mesh, icon, ground/held prefabs and Bootstrap pickup.
- [x] Integrate right-hand presentation and contextual tree labels.
- [x] Author holding/chop animation and inspect grip and motion in Unity.
- [x] Verify equipment, harvesting invariants and lifecycle regressions.
- [x] Update play/authoring documentation and record verification limits.

## Verification: 2026-09-29 initial implementation

- Unity 6000.3.20f1 compilation passed. All 84 Play Mode tests passed in 80.59 seconds, including four axe-specific tests. The earlier full run was 83/84 because the catalog test still expected 20 interactibles; the new axe correctly makes 21. Updated that assertion and reran the complete suite.
- Normal-speed synthetic pointer flow passed authored axe pickup, Bag Equip, Chop tree context, approach, active animation, Birch depletion/one log, Equipment Unequip and restored Punch. No event fallback or runtime errors were recorded.
- Saved ground/pickup validation passed for 15 items and 19 visual assignments. Palette validation passed 16 UI textures, both stylesheets and 28 material colors. All 54 checked local paths in the affected document indexes and authoring guides resolve.
- Inspected close-up holding and four chop phases plus the actual gameplay context/menu and harvesting captures. Corrected the hand's imported 100x unit scale and palm orientation during review. Animation and held prop remain driven by the same hand bone.
- Final Unity console check reported zero errors and warnings, with compilation complete.
- No harvesting numbers were changed. Tree actions retain their existing identity and data; only the explicit axe-presentation flag was added. Existing tree depletion/stump behavior remains the felling result.
- Bootstrap is prepared with the axe equipped for review. Model silhouette, grip and animation quality await user feedback; no player/browser build or performance claim.

## Review artifacts

- [Holding pose](D:/Repos/FirstLight/Docs/Images/axe-hold.png)
- [Raised swing](D:/Repos/FirstLight/Docs/Images/axe-high.png)
- [Forward swing](D:/Repos/FirstLight/Docs/Images/axe-strike.png)
- [Tree context](D:/Repos/FirstLight/Docs/Images/axe-context.png)
- [Live harvesting](D:/Repos/FirstLight/Docs/Images/axe-gameplay.png)
- [Authoring workflow](D:/Repos/FirstLight/Docs/agent-docs/axe-authoring.md)




## 2026-09-29: Right-handed and lowered carry revision

User feedback supersedes the original left-hand requirement. The axe now occupies Right hand, follows the RightHand bone and swaps with the worn sword. The animation curves and Avatar mask use the right arm, with mirrored torso twist. The held mesh turns 180 degrees around the handle so its cutting edge faces forward during chopping.

The resting pose lowers the arm and wrist, carrying the axe head down beside the leg while idle or walking. Chopping retains its raised windup. Sampled carry handle direction has world-up dot -0.90; the strike's cutting-edge direction has player-forward dot +0.50. Pose captures were visually inspected; these measurements supplement visual review rather than establish user acceptance.

Compilation passed and all four updated WoodcuttingAxeTests passed in 3.48s, including right-hand attachment/sword swap, downward carry in idle/walk and forward blade direction at strike. The normal-speed pickup/Equip/Chop tree/harvest/Unequip/Punch pointer flow passed again with no fallback or runtime errors. The earlier 84-test full-suite result precedes this revision; no full-suite rerun or build was performed. Bootstrap remains in Play Mode with the revised axe equipped for user review.


## 2026-09-29: Clarified horizontal carry pose

The user clarified that the arm should remain relaxed at the side with the hand slightly forward, the shaft parallel to the ground and the cutting edge downward. This supersedes the earlier interpretation that tilted the entire axe toward the ground. Adjusted only the holding pose's upper-arm position, forearm twist and wrist bend; chopping and equipment behavior remain unchanged.

Inspected front-quarter and side captures. The shaft's world-up dot is -0.003, and the cutting edge's world-down dot is 0.9996. Compilation and all four focused axe tests passed in 2.64s; idle/walk checks now require a level shaft, downward blade and hand beside/slightly ahead of the body. No full-suite or pointer-flow rerun for this pose-only revision. Bootstrap is in Play Mode with the revised axe equipped; visual acceptance remains with the user.

- [Side-view carry pose](D:/Repos/FirstLight/Docs/Images/axe-hold-side.png)

## 2026-09-29: Complete and accepted

The user accepted the final pose and confirmed this spec and session complete. Accepted behavior: right-hand axe, relaxed arm with hand slightly forward, horizontal shaft, downward cutting edge while carried, and forward-facing blade during Chop tree. Gameplay numbers remain unchanged. This acceptance supersedes all earlier pending-review statements in the dated verification history above.

No remaining spec009 work. This closure updates documentation only; no new runtime tests, build or Editor changes. Preserve the recorded verification chronology and build limitations.

