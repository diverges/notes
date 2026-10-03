# First Light sound review

Date: 2026-10-01. Discussion only; no game changes or asset replacement authorized.

## Implementation follow-up

User subsequently authorized replacing the full existing bank and selected Old School RuneScape with some Diablo as references. All 30 original WAVs were replaced in place, retaining metadata, frame counts/rates and playback wiring. Python validation and Unity reimport/decoded-sample checks passed. Originals and a 30-pair standalone audition page are in D:/Repos/FirstLight/Art/Audio/Retro2026. See current project context and Docs/agent-docs/interaction-audio.md for implementation and verification limits. The earlier discussion-only restriction below is historical; listening acceptance is still pending. Missing creature/acid coverage remains separate from the completed replacement.

## Evidence and limits

Inspected all non-Plugins audio files under Assets, their WAV headers and metadata GUID references in saved prefabs, scenes and asset definitions; read audio documentation, generation recipes and runtime feedback code. No listening audition, live playback or new Unity tests. Descriptions of timbre come from documented intent and synthesis recipes, not hearing the clips. Saved references establish assignment, not current-scene reachability or successful playback.

30 WAVs: 3 footsteps; 4 player interaction cues (including unreferenced LevelUp); 8 wood interaction variants; 4 survival cues; 2 crafting/XP cues; 5 combat cues including Player_Hit; 2 spider and 2 Warg cues. All mono 16-bit PCM, at 22.05 or 44.1 kHz. 29 have saved serialized references. Sources span Assets/Audio, Assets/Actors/Player/Audio, Assets/Actors/Crafting/Audio, Assets/Actors/Enemies/Spider and Assets/Enemies/Warg.

LevelUp.wav has no saved references; the current positive-XP/completion cue is Experience.wav (0.65 s), whose recipe uses decaying 880/1320 Hz sine tones. Engage.wav (0.75 s) was authored as rising 392/523.25 Hz notes. Spider and queen definitions share attack/death clips. Warg has its own pair. Boar and Spiderling have null attack/death/enter-combat clips, though their prefabs retain generic combat feedback. No dedicated queen acid audio hook found in QueenSpiderAttack. Repeated XP feedback in combat deserves an in-game overlap/fatigue audition.

## Proposed direction, pending discussion

Use early-3D horror texture for physical actions and creatures, with restrained RPG tonal acknowledgment for interface/rewards. Favor recognizable material sources, dry close attacks, short decays, limited brightness and selective grain. Do not apply blanket bitcrushing: the footsteps, survival and crafting/XP files already use 22.05 kHz, and sample format alone cannot supply character. Preserve the quiet footsteps preference and useful existing trigger timing.

Footsteps: dry earth crunch/scuff, low in mix; later stone/wood sets only where surfaces justify them. Equip/unequip: leather, wood and small buckle clicks. Pickup: a short tactile click with a subdued tonal accent if needed. Punch/chop/depletion: hollow knock, weighty wood bite and localized fiber crack/settle, respectively. Craft/stone work: woody tap versus mineral click. Fire: irregular subdued crackle, brief ignition and ember decay, with loop repetition checked in context. Combat: short swish, solid body impact and material-appropriate guard; generic block must also serve unarmed/creature outcomes, so avoid universal sword clang. Engagement: replace upward chime with a restrained low warning cue. XP: a much smaller muted bell/pluck if the current sound proves intrusive. Creature identity: chitin scrape/click for spiders, breath/growl for Warg, distinct boar cues; queen needs separate scale and acid launch/impact/hiss cues.

Suggested first comparison batch after approval: footstep, chop, player hit, engagement, XP and spider attack. Compare against the current files at matched perceived loudness, then hear repeated harvesting and combat. User preference and in-game listening decide acceptance. Ambience and missing menu cues are later coverage work, separate from replacing existing effects. No specific RPG reference or balance between horror and musical feedback has been chosen yet.
