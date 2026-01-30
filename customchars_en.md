## **LANGUAGE:**  [English](customchars_en.md) | [日本語](customchars_jp.md) | [中文](customchars_zh.md) | [한국어](customchars_kr.md)
**Note:** This guide will not cover creating spritesheets for new characters. For details on that, please take a look at the BravelyPath Modular Discord and ask under #modding-help if needed. 

1. Look at `CharaID` under `/Game/Content/Local/DataBase/Character`. This file basically stores texture paths and more for NPCs. If the NPC you're trying to add lacks an entry there, you'll have to add one. You can also, of course, use this file to look up the texture paths of specific NPCs' spritesheets, expanding those files directly often works too.
2. Add a new entry to `CharaPlayer` referencing `CharaID.m_id` under `m_CharaID`. Names have to be added to `GameTextCharacter`, the listed params (I believe) are as follows
```
 "m_Param": [
        0, (none)
        250, (Max. HP)
        60, (Max. SP)
        0, (Max. BP)
        0, (None)
        0, (HP)
        0, (SP)
        0, (BP)
        0, (none)
        72, (Phys Atk)
        88, (Phys Def)
        88, (Elem Atk)
        96, (Elem Def)
        64, (Speed)
        72, (Crit)
        80, (Accuracy)
        80, (Evasion)
        1, ("none", likely shield damage multiplier)
        0, (none)
        0, (none)
        0 (none)
      ]
```
"none" is just the name these stats appear to have in `GameTextUI`, it is not meant to imply they have no use. 

3. Specify similar values for `m_MaxParam` and the game interpolates values at every level based on these two arrays. I am not sure if m_MaxParam contains the stats of the unit at level 99 as the HP max param for most CotC units goes well beyond 9999 and testing shows they never reach these values either.
4. Ensure the new unit has `m_InitialOpenType` set to 2 so you can equip learned skills (see `CharaRuneInitialOpenTypeID` for viable IDs) and `m_InfluenceValue` set to 10 so it gains XP
5. Create a new entry under `PlayerMemberSet` referencing that newly added `PlayerID` (`CharaPlayer.m_id` being `m_PlayerID`). The new entry also needs `m_ValidCondition` to be set to `991202` and make sure the first entry of the `m_PlayerIDs` array is 0. A non-zero first entry would theoretically replace Zero/the Ringbearer Chosen but it only causes a soft-lock with the event recruitment command 10029 (it does work, however, if you assign that `PlayerMemberSetID` to an enemy group).
6. Create a new entry under `SkillBoardData` (this file can be found under `/Game/Content/Local/DataBase/SkillBoard`) referencing that new `PlayerID`, `m_CanNotEssential` determines whether a skill is learnable but it only gives you an item if you also add the skill as an item under `ItemList` (can be found under `/Game/Content/Local/DataBase/Item`). Make sure to only ever use `m_SupportLockCond1` `16` and `0` for `m_SupportLockCond2`. CotC uses different lock conditions sometimes but they do not appear to work in OT0 and every lock condition besides `16` lacks localization anyway (`Kingship_structs.hpp` gives those extra lock conditions labels, however, see below). `m_SupportLockParam1` determines how many battle skills have to be learned to unlock the specified support skill
7. (Optional) Modify `SkillID` and `SkillAvailID` to add custom skills and avails, see [How to add custom skills and avails](customskls_en.md)
8. Adding custom special skills: Add a new entry under `SpecialSkillID` (under `/Game/Content/Local/DataBase/Skill/`), associate the unit to that new special skill ID by adding an entry to `CharaPlayerSpecialSkillList` (under `/Game/Content/Local/DataBase/Character/`). Associate that special skill ID to a skill by adding new entries to `SpecialSkillGrowthList` for every level of the ultimate. CotC units appear to often have special skills with maximum levels that are much higher than 3 but I'm not sure if you can even get the game to load them. Also, I'm not sure if specifying the same skill for different levels would work in the sense of whether the game would load the higher boost level versions of the avails/skills.
9. Unpak `RecruitEvent_p.pak` (see also [How to extract assets from this mod and/or OT0 and repak them](Begin_en.md))
10. Edit `/Game/Content/Local/DataBase/Event/VPartychat/EvPChatOth_01` to include a new command 10029 entry referencing that new `PlayerMemberSetID` under `m_OptAry[0]`. The 1 in `m_OptAry[5]` does not appear to be necessary. See also: [How to add custom quests and events](customqsts_en.md)

### Adding custom jobs for Zero
The relevant file appears to be `CharacterCreateJobList`, but adding new entries to it causes the game to crash with an `EXCEPTION_ACCESS_VIOLATION` error. Only existing entries can be changed. `m_PlayerID` seems to largely be relevant for stat changes. It seems that the job's skillset simply incorporates all the skills of the units with that job, unless something specific (likely in the referenced `CharaPlayer` entry) is fulfilled. It is unclear to me how to get Zero to unlock these new jobs

## Extra information on individual assets
### Under `Character/`
#### `CharacterAnimationID`
- `m_AnimType`
`Kingship_structs.hpp` defines the following animation type IDs:

```
// Enum Kingship.ECHARA_ANIM_TYPE
// NumValues: 0x0005
enum class ECHARA_ANIM_TYPE : uint8
{
	eCHARA_ANIM_FIELD                        = 0,
	eCHARA_ANIM_BATTLE                       = 1,
	eCHARA_ANIM_EVENT                        = 2,
	eCHARA_ANIM_NUMS                         = 3,
	eCHARA_ANIM_MAX                          = 4,
};
```

### Under `CharacterCreate/`
```
// Enum Kingship.ECharacterCreatePersonalityID
// NumValues: 0x0005
enum class ECharacterCreatePersonalityID : uint8
{
	eNONE                                    = 0,
	eSERIOUS                                 = 1,
	eFINE                                    = 2,
	eCOOL                                    = 3,
	ECharacterCreatePersonalityID_MAX        = 4,
};

// Enum Kingship.ECHARACTER_CREATE_QUESTION
// NumValues: 0x0005
enum class ECHARACTER_CREATE_QUESTION : uint8
{
	QUESTION_FATHER                          = 0,
	QUESTION_MOTHER                          = 1,
	QUESTION_IMPORTANT                       = 2,
	QUESTION_LEARN                           = 3,
	QUESTION_MAX                             = 4,
};

// Enum Kingship.CHARACTER_PARTS_LIST
// NumValues: 0x000A
enum class ECHARACTER_PARTS_LIST : uint8
{
	HAIR_BACK                                = 0,
	HAIR_FRONT                               = 1,
	BODY_BACK                                = 2,
	BODY_FRONT                               = 3,
	FACE                                     = 4,
	HAIR_SHADOW                              = 5,
	EYE                                      = 6,
	HAIR_ADDITION                            = 7,
	NUMS                                     = 8,
	CHARACTER_PARTS_MAX                      = 9,
};
```

### Under `Npc/`
### `SkillBoardData`
`Kingship_struct.hpp` defines the following lock conditions

```
// Enum Kingship.EBOARD_LOCK
// NumValues: 0x0012
enum class EBOARD_LOCK : uint8
{
	eUNLOCK_NONE                             = 0,
	eUNLOCK_SCENARIO                         = 1,
	eUNLOCK_SKILL                            = 2,
	eUNLOCK_INFLUENCE                        = 3,
	eUNLOCK_NOW_LV                           = 4,
	eUNLOCK_MAX_HP                           = 5,
	eUNLOCK_MAX_SP                           = 6,
	eUNLOCK_NOW_HP                           = 7,
	eUNLOCK_NOW_SP                           = 8,
	eUNLOCK_PHYSICAL_ATTACK                  = 9,
	eUNLOCK_PHYSICAL_DEFENCE                 = 10,
	eUNLOCK_MAGIC_ATTACK                     = 11,
	eUNLOCK_MAGIC_DEFENCE                    = 12,
	eUNLOCK_SPEED                            = 13,
	eUNLOCK_CRITICAL                         = 14,
	eUNLOCK_MAX_LV                           = 15,
	eUNLOCK_SKILL_COUNT                      = 16,
	eUNLOCK_MAX                              = 17,
};
```
