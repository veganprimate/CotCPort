### **LANGUAGE:**  [English](customenms_en.md) | [日本語](customenms_jp.md) | [中文](customenms_zh.md) | [한국어](customenms_kr.md)

The process of adding new enemies and battles to Octopath Traveler 0 primarily revolves around editing DataBase files under `AIBattle`, `Enemy` and possibly `Skill`, `Texture` as well as `Battle`. Below an explanation of all the relevant files, headlines are marked with a number of stars depending on how necessary these files are to edit (fewer stars = used in more specialized contexts) but it's recommended to read the entire article anyway to know which files to modify for what purposes:

## Under `Enemy/`
### (***) `EnemyFormations`
Allows you to specify new Enemy Formations on the field, `m_PositionID` takes `BattlePositions.m_id` as input (`BattlePositions` can be found under `Battle/`). For custom battles with many enemies, you may want to look at these files to find an appropriate formation with enough non-zero `m_PositionID` entries or make your own. Some `PositionID`s may produce wildly different enemy positions depending on the battle map.

### (***) `EnemyBattleAnimSet`
Where CotC assigns to enemies an ID referring to a specific Flipbook asset, OT0 uses these sets of IDs, each referring to such an asset (`m_EnemyTextureIDs` taking the `m_id`s of `EnemyTexID` under `Textures` as input).
`m_EnemyTextureIDs[0]` typically refers to the sprite's idle animation (suffix `Idl00`), whereas `m_EnemyTextureIDs[1]` to `m_EnemyTextureIDs[3]` would be the sprite's attack animations (suffixes `Atk00` to `Atk02`). You will have to add a flipbook asset for at least the idle animation (i.e. referencing a new path under `EnemyTexID` and using that new entry's `m_id` under `m_EnemyTextureIDs[0]` in your newly added entry there), everything else is optional.

Please note that CotC's `m_FlipbookID` under `EnemyType` appears to use these extra array components differently: while index 0 still refers to the idle texture, CotC assigns to some enemies an invisible texture in index 1 (e.g. the Shadow from Shadow of Twin Worlds).

Fights which use NPC textures do **not** need an extra entry to be made under `EnemyBattleAnimSet`, as NPCs typically already have their own in-battle spritesheets (often ending with the `_B` suffix). Simply specify the NPC's `CharaID` under `EnemyType` (see below and possibly [How to add custom characters](customchars_en.md))

### (**) `EnemyParts`
If your custom boss consists out of several parts (e.g. Or'Galdera, Amatsukami no Orochi), you will have to specify each part in this file. Some, like Shadow of Twin Worlds, do not use this file at all, however.

### (****) `EnemyTypeID`
This asset is basically OT0's master "battle sprite presentation descriptor" for an enemy archetype, it tells the game:
- which animation sets to use or alternatively NPC/character spritesheet to associate (`m_AnimationSetIDs` taking the `m_id`s of `EnemyBattleAnimSet` as input, `m_NpcCharaID` taking the `m_id`s of `CharaID` under `Character/` as input)
- how to scale and position the sprite and all overlays:
  - hit sparks (`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`)
  - shield and weakness overlay
  - enemy effects (`m_EffectOffsetX`,`m_EffectOffsetY`, and `m_EffectFromBottomOffsetY`: offset relative to the bottom of the sprite, common for ground-based effects)
  - damage numbers (`m_DamageOffsetX`,`m_DamageOffsetY`)
  - ailments (`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`)
  - cursor (`m_CursorOffsetX`,`m_CursorOffsetY`)
- which enemy parts to associate with that enemy archetype (`m_PartsID`)
- which sounds/voices to use for that enemy type:
  - `m_CallSE` (i.e. "call sound effect")
  - `m_VoiceSymbol` (an integer that likely indexes into `VoiceSymbolList` under `Sound`)
  - `m_CueSheet` (likely indexes into `SoundSheetList` under `Sound/` and selects which cuesheet to use for this enemy's voice set)
- which attach effects (VFX) should be spawned and where (`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`)
- which position of the referenced texture (usually first frame of the flipbook asset/character spritesheet) should be used for the turn order icons (`m_OrderTexUS`, `m_OrderTexVS`; for large icons, i.e. when the specified character is taking their action, `m_OrderTexUL` and `m_OrderTexVL` are used) and at what scale the referenced texture should render in the order icon (`m_OrderScaleS`; `m_OrderScaleL`). For example, the Shadow in the Shadow of Twin Worlds fight uses a very small order scale to ensure its entire body appears on the order icon.
- Miscallaneous:
  - `m_OverrideOrderIconAnimSetID`: in case you want to use special texture assets for the order icon
  - `m_AttachEffectInGodbeastProduction`: unused in OT0 and CotC, related to divine beast effects
  - `m_AddStartUV`: a flag whether to add a start UV offset (position)
  - `m_LinkOwnerAnim`: if true, parts/attachments may follow the owner's animation timing
  - `m_DefeatEffect`: Effect ID to be played on death (e.g. Bestower of All's special defeat effect)
  - `m_ChangeColorID`: an ID into a color-change table, might be related to `CharaColorChangeParams` under `Character/`. Is only used in OT0 when Zero/the Ringbearer Chosen faces off against his shadow (`ChangeColorID` 6). CotC's `EnemyType` lacks that key entirely
 
### (**) `EnemyWeakID`
Tracks different kinds of weakness profiles which can be assigned to enemies. `m_ResistWeapon` and `m_ResistMagic` take the `m_id`s of `ResistType` under `Skill/` as input; `ResistType` 1 also adds the specified weapon/magic type as a weakness. The order of the specified weapon and magic types likely follow the order of the `m_id`s in the `WeaponType`/`MagicType` assets under `Skill/`, i.e.:
```
{
            "m_id": 0,
            "m_ResistWeapon": [
              0, (Sword)
              0, (Polearm)
              0, (Dagger)
              0, (Axe)
              0, (Bow)
              0, (Staff)
              0, (Polearm)
              0, (Tome)
              0, (Fan)
              0 (Non-elemental physical damage, other weapon types do not count; shows up as ? in-game even after analysis)
            ],
            "m_ResistMagic": [
              0, ("None", does not appear to show a weakness icon either)
              0, (Fire)
              0, (Ice)
              0, (Lightning)
              0, (Wind)
              0, (Light)
              0, (Dark)
              0, (Non-Elemental Magic Damage, other magic types do not count)
              0 (Poison, as in the poison damage of a poisoned enemy will shave off one shield)
            ]
          }
```
**Note:** OT0 can't display more than 8 weaknesses on one enemy. 

### (**) `EnemyWeakChangeID`
Tracks how enemies change weaknesses. `m_WeakIndices` takes `m_EnemyWeakID.m_id` as inputs and the `m_id` of those weakness changes can be assigned to e.g. an avail (see also [How to add custom skills and avails](customskls_en.md)).

### (**) `EnemyWeakLockID`
Tracks how enemies lock weaknesses. There does not appear to be fields for locking non-elemental magic damage and poison damage.

### (****) `EnemyID`
This is the game's enemy master record: it defines stats/weaknesses/rewards/UI flags, points to `EnemyTypeID` for presentation (animation sets, textures, offsets, voice, and effects), and to `TacticalAssignList` and `TacticalSkillList` for enemy behavior and their skillset (see "under `AIBattle`" below).
- `m_Label`: label of the Enemy. May be used internally and it's likely a good idea to not add new entries with conflicting labels
- `m_DisplayName`: ID refers to `GameTextEnemy.m_id`, universal for all localizations.
- `m_WeakID`: ID refers to `EnemyWeakID.m_id`, see above
- `m_DisplayLevel`: The enemy's displayed level. Unfortunately does not show up even with `m_LevelUI` set to true
- `m_BreakRate`: Percentage value by which damage done to the enemy is multiplied when broken
- `m_DamageRate`: Percentage value by which damage done to the enemy is multiplied when not broken
- `m_MaxSLD`: Shield count of an enemy. Even if the name of the key may suggest it is the maximum value, some tactics and skills can set it to higher values (see Lucian the Glorious for example)
- `m_MaxHP`: HP of an enemy. As before, skills and tactics can increase it
- `m_MaxSP`: SP of an enemy. Most enemy skills use 0 SP, so this stat is primarily used to limit the number of usages on specific enemy skills
- `m_AtkP`: Phys. Atk. of the enemy
- `m_AtkM`: Elem. Atk. of the enemy
- `m_DefP`: Phys. Def. of the enemy
- `m_DefM`: Elem. Def. of the enemy
- `m_Agi`: Speed/Agility of the enemy
- `m_Crt`: Crit. stat of the enemy
- `m_CrtDef`: Crit. Def. of the enemy; only enemies appear to have this stat
- `m_Hit`: Accuracy stat of the enemy
- `m_Avd`: Evasion stat of the enemy
- `m_ResistAilmentID`: assigns ID which tracks resistance to status ailments to enemy (see `SkillResistAilmentID` under `Skill/`)
- `m_ResistAilment`: An array corresponding to resist rates to certain ailments, possibly when the corresponding `m_ResistAilments` array of the assigned `ResistAilmentID` is full. Appears to only be used by Caits.
- `m_TacticalAssignID`: Assigns `TacticalAssignList.m_id` to an enemy corresponding to a list of tactics used in battle, see "Under `AIBattle` below
- `m_SkillsID`: Assigns `TacticalSkllList.m_id` to an enemy corresponding to a list of skills used in battle, see "Under `AIBattle` below
- `m_IsBoss`: Flag used by many bosses but also NPCs in towns. Not sure what it does functionally
- `m_Exp`, `m_Money`, `m_JP`: XP, Money or JP gained from this enemy after defeating them.
- `m_PetExp`: Always set to -1 in both CotC and OT0. Values not equal to -1 appear to have no effect
- `m_DropReward`: Item the enemy may drop. (see `EnemyDropID` for viable IDs)
- `m_StealReward`: Item that can be stolen from the enemy (see `ItemList` under `Item/` for viable IDs)
- `m_StealLeafNum`: Number of leaves that can be stolen from the enemy
- `m_StatusOffset`: Offset of enemy's status bar (weaknesses and shield count)
- `m_Bottle`: Meaning unclear. OT0 uses values 1-3. Value 3 is only used by Wappa in OT0. CotC also makes use of values 4 and 5.
- `m_NameUI`: Whether an enemy's name shows up on the UI
- `m_LevelUI`: Whether an enemy's `m_DisplayLevel` shows up on the UI (unused/nonfunctional)
- `m_HpUI`: Not functional in OT0.
- `m_ShieldUI`: If set to true, appears to darken the enemy's shield icon
- `m_RaceIndices`: races assigned to enemy. Can influence the damage calculation
- `m_DisplaySpecialSkillGauge`: Flag for displaying the enemy's BP counter
- `m_GenerateSpecialSkillValueOrverTurn`: how many BP points the enemy generates per turn
- `m_ReduceSpecialSkillValueBreak`: how many BP points are lost by the enemy after break
- `m_MaxSpecialSkillValue`: maximum number of BP points the enemy has. Values higher than 20 crash the game.
- `m_OutsideTarget`: Enemy can't be targeted and if it's the only enemy on the field, party members will also skip their actions (Bestowr of All uses this until the rings are broken)
- `m_NotAction`: If true, does not allow the enemy to act (used by Bestower of All before all the rings are defeated)
- `m_UnknownLv`: Unused in OT0.
- `m_Immortal`: If set to true, enemy's HP will never go below 1.

### (**) `EnemyReinforcements`
This is a battle encounter helper table that defines what extra enemies can be spawned mid-fight (reinforcements) and where they should appear
- `m_Reinforcements` takes `EnemyID.m_id` as input
- `m_Positions` takes `BattlePositions.m_id` as input (the respective enemy group's formation need not have this position specified)
The only asset which uses `EnemyReinforcements.m_id` is `SkillAvailID` under `Skill/`.

### (****) `EnemyGroups`
Chronicles all enemy groups. An entry has to be added here for your battle to trigger in-game.
- `m_Label`: ensure this label is unique/does not conflict with existing entries for newly added enemy groups. Events trigger fights against enemy groups using this label. If you plan on adding an entry to the Monster Arena, you will not be able to use that entry again for other purposes without causing the Monster Arena menu to appear after defeating it and possibly causing crashes
- `m_Inescapable`: Flag which determines whether you can flee from a fight
- `m_DisableRetire`: Always set to false in OT0, does not appear to be functional in OT0. Might be related to the ability of suspending your phone during the fight in CotC?
- `m_SkipResult`: Flag which determines whether the battle results screen (XP, Money, JP gained, etc.) is skipped
- `m_IgnoreDefeat`: Flag which stops the Game Over screen from appearing when defeated by the enemy group. Only used by King Pardis III, Edoras Defenders of Fame, Power, Wealth, and the Colossal Blightant. Appears to be overridden if the enemy is in the Monster Arena and will put you back there after defeat.
- `m_SkipBgmResult`: Flag which determines whether the victory theme plays
- `m_BanGodBeast`: Disables the usage of Divine Beast in battles. Unused in OT0. May block Divine Beast skills in the mod when added to some character's skill board (untested).
- `m_PlayerMemberSetID`: Forces your party to consist out of the characters specified in the referenced `PlayerMemberSetID` (see also [How to add custom characters](customchars_en.md))
- `m_Bgm`: Sets the background music for the fight, see `SoundList` under `Sound/` for viable options
- `m_NightBgm`: Sets the background music for the fight at night. Unused in OT0, untested if functional.
- `m_DarkBgm`: The same as `m_NightBgm`? CotC's entries appear to always have the same values in both keys.
- `m_BgmType`:
- `m_ResultBgmWin`: Specifies a custom victory theme.
- `m_ResultBgmLose`: Specifies a custom Game Over theme.
- `m_Camera`: Specifies the camera set to use (input: `BattleCameraSet.m_id`)
- `m_Formation`: Specifies the formation of the enemy group (input: `EnemyFormations.m_id`)
- `m_EnemyID`: Specifies the enemies on the field (inputs: `EnemyID.m_id`). Game does not immediately crash if you extend this array to more than 6 enemies but the new enemies will not show up on the timeline, lack a weakness/shield/status overlay, and the game will crash after they get a turn (error is related to corrupted allocator metadata/a corrupted pointer). Can maybe be bypassed via reinforcement calls.
<img width="729" height="403" alt="image" src="https://github.com/user-attachments/assets/4e563205-ffca-479a-9321-33dceb3f650f" />

- `m_EventIndices`: (Battle) Events played at the beginning or during the fight (in-battle dialogue, etc.), possible values are likely `BattleEventList.m_id` and not `EventList.m_id`.
- `m_AbortCondition`: Condition for aborting the battle. Viable IDs can likely be found under `BattleAbortConditions`.
- `m_DefeatPriority`: Always set to false in OT0 and CotC. Not sure what it does if anything.
- `m_ForcedWinAbortCondition`: Sets a custom condition for forcing the battle to be won. Viable IDs can likely be found under `BattleAbortConditions`.
- `m_IsAllEnemyDeadOnForceWin`: (untested) Flag that determines whether all enemies perish upon fulfilling the condition set in `m_ForcedWinAbortCondition`?
- `m_ForcedLoseAbortCondition`: Sets a custom condition for forcibly losing the battle. Viable IDs can likely be found under `BattleAbortConditions`.
- `m_ImportantFlags`: Six different flags, all of which are unused in OT0 and whose meaning could not be determined through testing
  - Flag 1: used primarily by Job Tower bosses in CotC.
  - Flag 2: used only by `Dice_Bolaven_Rank04_BossGr03` (battle against Idore, Roguish Riven Soldier I, and Roguish Riven Soldier II)
  - Flag 3: only used by the same enemy group as Flag 2
  - Flag 4-6: never used in either game
-  `m_PetDear`: Takes -1, 0, and 1 as values
-  `m_DisableBattleEndWipe`: Used by many final bosses (Bestower of All, Or'Galdera, Side Solistia's Galdera) and also the Magitek Armor.
-  `m_DisableBattleEndTraining`: Prevents party members in the training grounds from gaining XP, JP, etc. Used in the Emerald Direwolf fight (only with Laurana on the field), the Bestower of All Ch. 7 fights during the Sazantos flashback, etc.
-  `m_DisableSupporter`: Flag which determines whether you can summon helpers in battle
-  `m_EncountEffectTypeID`: Specifies special encounter effects (e.g. FF6 collab encounters). Arena battles need this set to 0 or else they'll soft-lock the game. Viable IDs can be found under `EncountEffectTypeID`.
-  `m_EnablePlayerShield`: Always set to false in both OT0 and CotC. 
-  `m_DisableGrade`: Always set to false in OT0, not in CotC. Likely a flag to ignore weapons' grades.
-  `m_IsContainRareEnemy`: Likely a flag to tell the game which enemy groups are considered to contain rare enemies so accessories increasing the encounter rate of rare enemies increases.

### (**) `EnemyModeID`
Tells the game which mode ID refers to which effect to be applied to the field or enemy. These effects can be assigned to avails. Used when bosses enter boost mode and display special effects.

### (**) `EnemyDropID`
Keeps track of all the different drop IDs which can be assigned to enemies.

### (**) `EncountVolume`
Determines danger levels, which enemy groups can be encountered, and the respective encounter probabilities. encount volumes are associated with maps in `EncountList`. Maps may change their danger level and therefore possible encount volumes

### (**) `EncountList`
OT0's random encounter rule table: Specifies which encount volumes can be encountered on which map (`m_EncountVolume` being `m_EncountVolume.m_id`), minimum number of steps until encounter (`m_EncountStepMin`), and conditions which have to be met for the encounter to occur. 

Miscallenous keys:
- `m_DisableCondition`: condition to be met to disable this encounter
- `m_RandomStepA` and `m_RandomStepB`: parameters for the random step range/curve. Both keys appear to always have the same values in both OT0 and CotC.
- `m_FirstEncountStepRatio`: Likely a multiplier specifically for the first encounter after entering the map/zone, making it earlier or later than normal.

### (*) `EncountEffectTypeID`
Keeps track of different encount effects, `m_SeId` likely sets the map on which this effect occurs.

### (*) `EnemyGroupsByCondition`
Sets of enemy groups to spawn when certain conditions are met. Used to spawn the Accurst Flame alongside the Ringbearer in Bestower of All Chapter 7.

### (**) `SymbolEnemyList`
Specifies what overworld sprites or objects and enemy groups are assigned to symbol/elite enemies as well as their level.
- `m_GroupId`: Enemy Group associated with this symbol enemy entry
- `m_Index`: Index of this enemy within the enemy gorup. Commonly used when multiple symbol enemies share the same GroupId or spawn controller.
- `m_Dir`: The direction the symbol enemy faces by default
- `m_ObjectId`: Overrides the symbol enemy overworld sprite with a specific object (e.g. Tatloch's ships in Bestower of Power Ch. 3)
- `m_RepopCount`: Respawn/repopulation count, i.e., how often a symbol enemy respawns. 0 by default for OT0 enemies, not true for CotC enemies (enemies do not re-appear immediately after reloading the map)
- `m_Redrawing`: Always set to 0 in both OT0 and CotC
- `m_TargetLevel1` and `m_TargetLevel2`: possib2ly a level range for the symbol enemy
- `m_Scale`: Scale multiplier for the enemy symbol in the field (purely visual/collision size)

### (**) `SymbolEnemyGroupList`
Appears to assign symbol enemy IDs (`m_id` from the `SymbolEnemyList` table) to specific internal IDs that refer to particular symbol enemies on certain maps. The `m_SymbolEnemyId` array may determine which symbol enemies appear in a given spot after repopulation, once the symbol enemy under `m_StartSymbolEnemyId` has been defeated, with probabilities specified for each symbol enemy under `m_probability`. Neither CotC nor OT0 appear to use these arrays.

## Under `Battle/`
### (***) `BattleCameraParams`
IDs which correspond to battle camera presets/configurations, value `-1.0` appears to be interpreted as "use default" by the game:
- `m_PosotionX/Y/Z`: position of the battle camera in 3D space
- `m_RotationX/Y`: Rotation/Tilt of the camera in X/Y direction
- `m_FOV`: Field of view. A narrow field of view = zoomed-in/telephoto look. Keep in mind that the party's position is fixed when changing the FOV.
- `m_SequencerPath` Cinematic/Sequencer binding, likely points to a Level Sequence or some Sequencer-driven camera rig asset used for battle camera cuts/targeting
- `m_FocalDistance`, `m_FocusDistance` distance to the focus plane (one may be legacy vs new naming (CotC only has `m_FocalDistance`), or different systems). Both are always set to `-1.0` in OT0, CotC uses different focal distances.
- `m_FocalRegion`: size of the in-focus region
- `m_NearBlurSize` and `m_FarBlurSize`: blur intensities depending on distance
- `m_MaxBokehSize`: cap for bokeh blur
- `m_Scale`: likely a camera distance scale/shot scale override

### (***) `BattleCameraSet`
Sets of camera configurations which are assigned to enemy groups (`BattleCameraSet.m_id`=`EnemyGroups.m_Camera`), does not exist in CotC (CotC only uses one neutral camera configuration in battle).

### (***) `BattlePositions`
Defines position IDs used in e.g. `EnemyFormations` or `EnemyReinforcements`. `m_Priority` may determine which sprite appears in front of the other in case of conflicting coordinates or the order in which enemies are selected.

### (***) `BattleEventList`
Likely the events which can be assigned to enemy groups under `EnemyGroups.m_EventIndices`. 
- `m_IsMustPlay`: whether an event is skippable(?)
- `m_EventCommand`: the command script ID to execute when triggered (points into `BattleEventCommand`)
- `m_EventConditions`: Condition type IDs, likely the same as in `m_Conditions` arrays in other files (`TacticalList`, `SkillConditionList`, `TacticalActionList`, etc.)
- `m_EventParams`: Parameters for the conditions
- `m_EventEnemies`: Optional filter to specify which enemy slot/which enemy index/which part the condition applies to (seemingly unused in both OT0 and CotC).

### (***) `BattleEventCommand`
Contains scripted steps in a battle event sequence. Where `BattleEventList` describes what should run under what conditions, `BattleEventCommand` appears to execute the actual script steps (show dialogue, play animation, spawn effects, swap enemy textures, change BGM, etc.)

- `m_NextWait`: delay after executing this command before proceeding in seconds
- `m_isWaitSkillTime`: : if true, wait until a skill animation finishes
- `m_isSwitchActionSkip`: allow/force skipping action sequences
- `m_PlayBgmBlock`: likely blocks BGM changes until something completes
- `m_PerformerID`: likely an index into party/guest/enemy performer lists, or a special ID
- `m_PerformerIsLeader`: use party leader as performer
- `m_PerformerEnemy`: : if non-zero, indicates the performer is an enemy (appears to use `EnemyID.m_id` as input)
- `m_TargetID`: likely who to target for a forced action/skill
- `m_EnableTarget`,`m_EnableAction`: toggles to enable player targeting/action during/after this command.
- `m_NameID`: localization ID for the speaker name (found under `GameTextCharacter`)
- `m_TextID`: localization ID for the dialogue line (found under `GameTextEvent`)
- `m_BalloonType`: style of balloon/speech bubble (1 = probably normal speech bubble)
- `m_NoTail`: no speech-bubble tail
- `m_BaloonOffsetX/Y`: position offset for the balloon relative to performer
- `m_NoName`: hide speaker name if true
- `m_NarrationID`: takes IDs from `NarrationList.m_id`
- - `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`: narration-style related keys
- `m_AnimID`: which animation to play on the performer while speaking (or immediately before/after)
- `m_DirID`: direction/facing preset for the performer
- `m_isRewriteIdleMotion`: keep an idle override after the command
- `m_SoundID`: play a sound (likely `SoundList` ID)
- `m_SoundHandle`: possibly a runtime handle slot (e.g. start SFX and store handle so later command can stop/replace it) (?)
- `m_EffectID`: spawn an effect (likely `EffectList` ID)
- `m_EffectHandle`: possibly a runtime handle slot for later manipulation/stop (?)
- `m_EffectPosition`: where to spawn the effect (performer, target, screen, etc.)
- `m_BgmID`: change BGM (again likely `SoundList` ID)
- `m_FadeTime`: fade duration for BGM changes
- `m_IsRemoveAllAilments`: clear statuses (likely for phase transitions)
- `m_EnableRetire`, `m_DisableGameOver`: (untested) force-enable/disable UI options and fail states during scripted battles(?)
- `m_LastBattleMapColor`: likely battle background tint control
- `m_TextureChangeEnemyIdx`: which enemy slot to change texture on (`-1` means unused)
- `m_ChangeEnemyTextureIdx`: which texture index/slot to switch to (`-1` unused)
- `m_TextureChangeTime`: timing for texture swap (either delay or crossfade time, untested)
- `m_EnemyPartsIndex`: which enemy part index is being affected
- `m_EnemyPartsDisplay`: show/hide referenced enemy part during the event
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: Also exists in `TacticalList`. The battle engine likely advances scripts based on those flags (i.e. they are an implicit "next" to track what's occurred). Example: used in `TacticalList` by Shadow of Twin Worlds to track whether the tactic unlocking the Shadow's weaknesses upon break of Lucian and Lyblac has occured to re-lock them after Lucian and Lyblac recover from their break as there does not appear to exist a condition specifically for that situation.
- `m_JoinTrigger`: likely tied to "unit joins battle now" events (e.g. guest joins mid-fight)
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`: special command types that do not fit the generic struct


### (*) `BattlePlaybackData` and `BattlePlayback`
Thees look like OT0's battle-preview/battle-demo configuration: a small database that tells the game "for playback preset X, have enemy Y perform skill Z, and optionally show/hide attack effects"

#### `BattlePlaybacks`
The structure is basically: `PlaybackID` → `SkillID` (+ performer slot override) + VFX visibility toggle
- `m_EnemySkillID`: the (enemy) skill to play (although all skills are stored in the same file: `SkillID`)
- `m_SkillPlayEnemyIndex`: which enemy slot/index should perform the skill during the playback (`-1`="auto/default")
- `m_VisibleAttackEffect`: likely whether to show the "attack effect" layer during playback (e.g. show/hide slash/impact overlays, hit flashes, or extra VFX so the animation can be previewed "clean" vs "full")

#### `BattlePlaybackData`
This may not be real runtime data, appears to have a similar layout to `BattlePlaybacks` but with an odd line
```
"m_EnemySkillID_01": "m_EnemySkillID_01"
```
for each entry. It might be one of the following:
1. A schema/template/struct default that FModel dumped in a confusing way (e.g., a default object/struct where that field is an FName and got serialized strangely)
2. A "multi-slot" older table where there were meant to be fields like `m_EnemySkillID_01`, `m_EnemySkillID_02`, etc., and the dump/export mangled the values
3. A table where `m_EnemySkillID_01` is actually supposed to be a row handle/key (FName) into another table, and the dump is substituting the property name instead of the stored value
**Note:** this table has different `m_SkillPlayEnemyIndex` values in the Switch version. The Steam demo version also lacks some entries

### (*) BattleResultBonus
Tracks ratios and localization IDs of different battle result bonuses (the "No Damage", "Overkill", "Break", "1Turn Kill", etc. bonuses). `m_Param` appears to track which stats (XP, JP, or money) gets the bonus:
- 0: Money 
- 1: JP
- 2: XP
Untested whether unused params beyond 2 exist.

### (**) `BattleVoiceList` and `BattleVoiceArenaList`
Define a variety of voice lines to be played by party members or a narrator during battle (e.g. special voice lines during the Or'Galdera fight).

### (***) BattleAbortConditions
OT0's battle termination/forced-outcome condition table: each entry defines a small bundle of predicates that, when satisfied during a fight, cause the battle to abort (end early) or be treated as a forced win/forced lose depending on which field references it in `EnemyGroups`.
- `m_Conditions`: condition type IDs (likely the same as in `TacticalList`, `TacticalActionList`, `SkillConditionList`, and `BattleEventList`)
- `m_Params`: parameter for each condition (meaning depends on the condition type)
- and filters that only matter for certain condition types:
   - `m_AilmentTypes` (takes `SkillAilmentType.m_id` as input)
   - `m_StatusTypes` (possibly takes `CharaStatusID.m_id` as input)
   - `m_WeaponTypes` (takes `WeaponType.m_id` as input)
   - `m_MagicTypes` (takes `Magictype.m_id` as input)
   - `m_EnemyID` (takes `EnemyID.m_id` as input)

### (*) `BattleActionID`
A small lookup table that maps an "action ID" (likely `SkillAilmentType.m_ActionID`) to a concrete battle animation/motion, with a flag that says whether that animation should be played using the NPC/field character rig instead of the standard battle rig. `m_UseNPC` may determine whether this action should be played using an NPC character animation set rather than the usual battle-sprite.

### (*) BattleParams
A variety of battle-related parameters whose meaning is as follows according to `Kingship_structs.hpp` from the Dumper-7 SDK (see #file-dump in the BravelyPath Modular Discord):
```
enum class EBATTLE_PARAMS : uint8
{
	SWAP_WAIT_SEC                            = 0,
	QUICK_GAME_SPEED_RATIO                   = 1,
	DASH_ENCOUNT_RATIO                       = 2,
	SHORT_RESULT_MAX_TURN_COUNT              = 3,
	VICTORY_SP_MOTION_WAIT_SEC               = 4,
	STATUS_AVAIL_MAX_STEP                    = 5,
	STATUS_ANOTHER_AVAIL_MAX_STEP            = 6,
	POISON_DIVISION_VALUE_BOSS               = 7,
	POISON_DIVISION_VALUE                    = 8,
	DEATHBLOW_GAUGE_LEVEL_VALUE              = 9,
	DEATHBLOW_GAUGE_MAX_LEVEL                = 10,
	DEATHBLOW_GAUGE_BONUS_RATE               = 11,
	ORDER_OFFSET_BREAK_RESUME                = 12,
	ORDER_OFFSET_GUARD_BASE                  = 13,
	ORDER_OFFSET_GUARD_ADD                   = 14,
	ORDER_OFFSET_BREAK                       = 15,
	ORDER_OFFSET_STANDBY_PRESAGED            = 16,
	ORDER_PRIORITY_VALUE                     = 17,
	ORDERING_DONT_SHUFFLE_RATE               = 18,
	ORDER_OFFSET_FORCE_ORDER_HEAD            = 19,
	ORDER_OFFSET_FORCE_ORDER_END             = 20,
	DEATHBLOW_GAUGE_BONUS_RATE_2             = 21,
	FOUNDER_DRAGON_ORB_DEAD_EFFECT_Z_ORDER   = 22,
	AILMENT_ICON_DISP_CHANGE_SEC             = 23,
	AILMENT_EFFECT_CHANGE_SEC                = 24,
	RESULT_BASE_EXP_MAX                      = 25,
	RESULT_BASE_JP_MAX                       = 26,
	RESULT_BASE_MONEY_MAX                    = 27,
	AILMENT_LIST_DISPLAY_MAX                 = 28,
	AILMENT_EXPLANATION_TEXT_MAX             = 29,
	COMMAND_COST_MAX_DIGITS                  = 30,
	REMAIN_COUNT_MAX_DIGITS                  = 31,
	COLOR_CODE_TAG_BOOST_LV2                 = 32,
	COLOR_CODE_TAG_BOOST_LV3                 = 33,
	COLOR_CODE_TAG_BOOST_LV4                 = 34,
	EFFECT_SPECIAL_SKILL_WAIT                = 35,
	EFFECT_SPECIAL_SKILL_BOOT                = 36,
	EFFECT_SPECIAL_SKILL_BOOT_COMMON         = 37,
	EFFECT_RAID_ACTIVE_AREA_FRONT            = 38,
	EFFECT_RAID_ACTIVE_AREA_BACK             = 39,
	COLOR_CODE_OUT_TARGETABLE_CHARACTER      = 40,
	OL_SAZANTOS_BREAK_EFFECT_Z_OFFSET        = 41,
	MAX_SAME_VOICE_REPEAT_COUNT              = 42,
	ENCOUNT_STEP_LENGTH                      = 43,
	ENCOUNT_STEP_COUNT                       = 44,
	MAX_HP_LIMIT_OVER                        = 45,
	MAX_DAMAGE                               = 46,
	MIN_DAMAGE                               = 47,
	MAX_BP                                   = 48,
	FIRST_BP                                 = 49,
	MAX_SLD                                  = 50,
	CAUTION_RATE_HP                          = 51,
	CALC_HEAL_BASE_DEF_DIVISOR               = 52,
	CALC_HEAL_BASE_DEF_ADDITION              = 53,
	CALC_AILMENT_POISON_PLAYER_RATIO         = 54,
	CALC_AILMENT_BLEED_PLAYER_RATIO          = 55,
	CALC_MIN_GUARANTEE_CRITICAL_VALUE        = 56,
	CALC_CRITICAL_BREAK_RATE                 = 57,
	CRITICAL_DAMAGE_RATIO                    = 58,
	MAX_AILMENT_TURN                         = 59,
	MAX_AILMENT_COUNT                        = 60,
	MAX_SKILL_RATIO                          = 61,
	MIN_SKILL_RATIO                          = 62,
	MAX_SUPPORT_RATIO                        = 63,
	MIN_SUPPORT_RATIO                        = 64,
	MAX_EQUIPMENT_SKILL_RATIO                = 65,
	MIN_EQUIPMENT_SKILL_RATIO                = 66,
	MAX_SUPPORT_EXP_RATIO                    = 67,
	MAX_SUPPORT_LEAF_RATIO                   = 68,
	MAX_ENEMY_SKILL_RATIO                    = 69,
	MIN_ENEMY_SKILL_RATIO                    = 70,
	AILMENT_WASTE_SP_RATE                    = 71,
	TURN_COUNT_BREAK                         = 72,
	MAX_CALLABLE_NPC                         = 73,
	MAX_BOOST_LEVEL                          = 74,
	PLAYER_STEP_TIME                         = 75,
	DELAY_FIRST_SHOW_UI                      = 76,
	DELAY_ADD_BP                             = 77,
	DELAY_SE_MAX_BP                          = 78,
	DELAY_RESUME_MENU                        = 79,
	DELAY_RESULT_SKIP                        = 80,
	DELAY_VICTORY_A                          = 81,
	DELAY_VICTORY_B                          = 82,
	DELAY_VICTORY_C                          = 83,
	DELAY_VISITOR_LEAVE                      = 84,
	VISITOR_POS_ADJUST_RATE                  = 85,
	DELAY_ENEMY_FADE_IN                      = 86,
	DELAY_ENEMY_FADE_IN_AFTER                = 87,
	DELAY_PLAYER_DASH_IN_BASE                = 88,
	DELAY_PLAYER_DASH_IN_OFS                 = 89,
	DELAY_PLAYER_DASH_IN_DIST                = 90,
	BATTLE_FIRST_TUTORIAL_DELAY              = 91,
	BEGIN_ESCAPE_DELAY                       = 92,
	MOVE_ESCAPE_DELAY                        = 93,
	FAILED_ESCAPE_DELAY                      = 94,
	VIEW_ESCAPE_DELAY                        = 95,
	VIEW_CONTINUE_DELAY                      = 96,
	IGNORE_DEFEAT_DELAY                      = 97,
	FINISH_BATTLE_DELAY                      = 98,
	RETRY_WIPE_DELAY                         = 99,
	ABSORB_DAMAGE_DELAY                      = 100,
	SLIP_DAMAGE_AFTER_DELAY                  = 101,
	CHANGE_COUNT_DOWN_DELAY                  = 102,
	COUNT_DOWN_DELAY                         = 103,
	DAMAGE_ADD_AILMENT_MISS_DELAY            = 104,
	ANIM_RATE_ENEMY_BREAK                    = 105,
	SLOW_DELAY_BREAK_CAMERA                  = 106,
	SLOW_RATE_BREAK_CAMERA                   = 107,
	SLOW_WAIT_BREAK_CAMERA                   = 108,
	ACTION_INTENSITY_DL                      = 109,
	ENEMY_DEAD_KNOCKBACK_WAIT                = 110,
	ENEMY_DEAD_KNOCKBACK_LENGTH              = 111,
	DELAY_ACTION_INFO_SKIP                   = 112,
	DELAY_ACTION_INFO_LEAVE_NPC              = 113,
	DELAY_AUTO_CHANGE_RETIRE                 = 114,
	OFFSET_AUTO_CHANGE_RESCUER               = 115,
	UI_SIZE_DAMAGE_NORMAL_W                  = 116,
	UI_SIZE_DAMAGE_NORMAL_H                  = 117,
	UI_SIZE_DAMAGE_WEAK_W                    = 118,
	UI_SIZE_DAMAGE_WEAK_H                    = 119,
	UI_SIZE_DAMAGE_BREAK_W                   = 120,
	UI_SIZE_DAMAGE_BREAK_H                   = 121,
	DELAY_AILMENT_ICON_DISP_CHANGE           = 122,
	VOICE_DAMAGE_MIDDLE_HP_RATIO             = 123,
	VOICE_DAMAGE_LARGE_HP_RATIO              = 124,
	VOICE_VICTORY_NARROW_HP_RATIO            = 125,
	DELAY_PRESAGED_ACTION_ORDER              = 126,
	ORDERING_AGI_RATE_RANDOM_RANGE           = 127,
	MAX_SPECIAL_SKILL_RATIO                  = 128,
	MIN_SPECIAL_SKILL_RATIO                  = 129,
	NAX_SUPPORT_JP_RATIO                     = 130,
	MAX_RACE_DAMAGE_RATIO                    = 131,
	MAX_RACE_REDUCE_RATIO                    = 132,
	LIMIT_OVERED_MAX_DAMAGE                  = 133,
	MIN_CALC_DEF_PARAM                       = 134,
	PLAYER_BREAKED_DAMAGE_RATIO              = 135,
	BLEED_ENEMY_VALUE                        = 136,
	HIGH_BLEED_ENEMY_VALUE                   = 137,
	EBATTLE_MAX                              = 138,
};

```

### (*) `BattleDeathBlowPoint`
A lookup table for "Death Blow" (special attack) gauge gain. Each row essentially defines if a certain check condition is met (type + value), by how much the Death Blow gauge is to be increased.
- `m_CheckType`: what kind of thing is being checked.
- `m_CheckValue`: the threshold/amount for that check type. Appears to be a count
- `m_IncreasedPoint`: how many "Death Blow points" to add when that check matches.

### (*) `BattleLayoutSet`
Likely a small battle staging/layout preset table that ties together a camera preset and enemy-side positioning. It is unclear where in the game this is used (no formation or reinforcements appear to make use of `PositionID` 270, for instance).

## Under `AIBattle`
These files are responsible for enemy AI behavior and the skills used by them (and how).
### (****) `TacticalList`, `TacticalAssignList`, and `TacticalSkillList`
OT0's enemy AI "rule clause" table: each entry defines a set of conditions that, when satisfied, causes the AI to select or enable some behavior. Enemies are assigned these "rules" in the file `TacticalAssignList` whose `m_id`s correspond to `EnemyID.m_TacticalAssignID`; battle skills are assigned to them through `TacticalSkillList`, whose `m_id`s correspond to `EnemyID.m_SkillsID`.
- `m_Presage`: likely a boolean/enum indicating whether this tactic causes a presage (telegraphed action) to show
- `m_PresageSkillID`: skill ID associated with the presage  (what's being telegraphed). Used, for instance, when a boss is announcing boost mode (these skill IDs then notably **not** present in the enemy's `TacticalSkillList.m_UseSkills` array)
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: Event flag gating and side effects, see `BattleEventCommand`. Likely used for branching/sequencing AI behavior.
- `m_Conditions`: same conditions as `TacticalActionList`, `BattleEventList`, `SkillConditionList` most likely with same `m_Params` and filter arrays
   - `m_AilmentTypes` (takes `SkillAilmentType.m_id` as input)
   - `m_StatusTypes` (possibly takes `CharaStatusID.m_id` as input)
   - `m_WeaponTypes` (takes `WeaponType.m_id` as input)
   - `m_MagicTypes` (takes `Magictype.m_id` as input)
The meaning of each condition has yet to be rigorously worked out, this resource may help you out with that: https://github.com/veganprimate/CotCPort/tree/main/Conditions/Conditions_Datamine_EN
Misc.:
- 28 is the only OT0-exclusive condition
- Condition type IDs appear to be the same in OT0 and CotC, although CotC may have some OT0 lacks
- IDs in the 10k+ range appear to often be related to action execution order:
  - Condition 10100 is often used in skills announcing an enemy's boost mode,
  - Condition 10304 triggers right when the other conditions are met, e.g. the Shadow's veil lifting immediately after Lucian and Lyblac are both broken (Condition 718 with `m_params` the respective indices in their `EnemyGroup` entry)
  - Condition 10301, on the other hand would have the veil lift only in the subsequent turn
  - 10711 appears to have an action occur at the beginning of a turn (used by the Shadow after Lucian and Lyblac recover from their first break for instance)
### (****) `TacticalActionList`
Appears to tell the game what to actually do when a tactic applies: which skill to use (by index), who to target, how to select among possible targets, and additional conditions/priority rules.
- `m_SkillIndex`: This is not a SkillID itself. It's an index into a skill list/array (from testing: `TacticalSkillList.m_UseSkills[]` for the enemy's `m_SkillsID`)
- `m_SelectType`: Either a target selection mode enum or related to how indices like the skill index are selected. From my testing: `m_SelectType` had to be set to 3 for the associated skills to trigger (`SelectType` 1 had it default to its first(?) skill)
- `m_FriendlyIndex`: who to target on the "friendly" side (from the enemy's perspective?). `-1` here would likely mean "no fixed friendly; maybe: pick based on `SelectType`(?)"
- `m_PrioritySkill`: if set, it may override normal selection and force a specific skill index (or mark a skill index as preferred?)
- `m_TurnCount`: repeats an action every `N` turns where `N` is the value given to this key, provided the specified tactics ID's conditions are met
- `m_GroupIndex`: grouping key, likely used to separate actions by phase or restrict number of actions per group per turn or coordinate multi-enemy behavior in a group
- Additional action-level conditions (like `TacticalList`, but specific to the specified action): `m_Conditions`, `m_Params`, `m_AilmentTypes`, `m_StatusTypes`, `m_WeaponTypes`, `m_MagicTypes`
