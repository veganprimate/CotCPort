## **LANGUAGE:**  [English](customqsts_en.md) | [日本語](customqsts_ja.md) | [中文](customqsts_zh.md) | [한국어](customqsts_ko.md)

# How to add or modify quests and events

This is meant as a practical map of the tables that matter when you want to add or modify quest flow, event flow, StoryBook entries, NPC gating, and related UI state.

Relevant folders for quest/event work are usually `Quest/`, `Event/`, `StoryBook/`, and often also `Npc/`, `ScenarioReplay/`, `Map/`, `Character/`, `Item/`, and `PartyChat/`.

If you are adding a completely new quest, the minimum working set is usually:
- text in `GameTextQuest` (and often `GameTextEvent` / `TalkText` and `GameTextNPC` / `TalkText` too)
- **a** `QuestList` row
- **one or more** `QuestTaskList` rows
- **one or more** `QuestTrigCondition` rows
- **one or more** `EventList` rows plus the actual event asset(s)
- **at least one** consumer table that exposes the quest to the player (`NpcSetList_*`, `NpcTalkList_*`, `StoryBook*`, `Map*`, etc.)

## High-level workflow

1. Reserve IDs and labels.
   - Pick new `m_id` values that do not collide with existing rows.
   - For events, pick a unique `EventList.m_Name` and matching JSON asset filename.
   - For NPC/map anchors, reuse or create unique labels such as `m_AppearLabel`, `m_DestNpcSetLabel`, and map path actor names.

2. Add text first.
   - Quest menu/title text lives mainly in `GameTextQuest`.
   - Event dialogue and narration use the event/talk text databases instead.

3. Add the quest shell.
   - Add the menu entry in `QuestList`.
   - Add start/progress rows in `QuestTaskList`.

4. Add reusable conditions.
   - Add `QuestTrigCondition` rows for visibility, availability, completion, unlocks, map reveals, NPC presence, and so on.

5. Add the actual event(s).
   - Add the `EventList` row.
   - Create the event JSON asset under the matching `Event/` subfolder.
   - Wire the event ID into `QuestTaskList`, `NpcTalkList_*`, `StoryBookList`, or wherever the scene is launched.

6. Attach the quest to world content.
   - NPC/object appearance usually goes through `NpcSetList_*` or `MapObjectConditionsList`.
   - Talk branches usually go through `NpcTalkList_*`.
   - World-map, StoryBook, party-chat, village-building, or replay UI exposure uses their own tables.

## Under `Quest/`

### `QuestList`

What appears in the quest menu: title/subtitle/synopsis, chapter number, recommended level, sorting, and when to show/hide it. An entry here is required if you want a quest to exist in the quest UI.

- `m_QuestCategory`: high-level bucket shown in the quest UI. The SDK defines:
```hpp
enum class EQUEST_CATEGORY : uint8
{
	QST_NONE                                 = 0,
	QST_BOSS                                 = 1,
	QST_CHARA                                = 2,
	QST_SIDE                                 = 3,
	QST_SUB                                  = 4,
	QST_TOWN                                 = 5,
	QST_TOWNSUB                              = 6,
	QST_JOIN                                 = 7,
	QST_JOINSUB                              = 8,
	QST_PROLOGUE                             = 9,
	QST_MAX                                  = 10,
};
```
- `m_QuestSubCategory`: sub-bucket inside the category, primarily used by main-quest style content. The SDK defines:
```hpp
enum class EQUEST_BOSS_SUB_CATEGORY : uint8
{
	NONE                                     = 0,
	EXTREME_WEALTH                           = 1,
	EXTREME_POWER                            = 2,
	EXTREME_FAME                             = 3,
	EXTREME_ALL                              = 4,
	AWARD_WEALTH                             = 5,
	AWARD_POWER                              = 6,
	AWARD_FAME                               = 7,
	AWARD_ALL                                = 8,
	EQUEST_BOSS_SUB_MAX                      = 9,
};
```
- Quest category `4` (`SUB`) has a special subcategory `20`, apparently used by Wishvale main-quest content.
- `m_JoinPlayerID`: controls which character portrait/head is shown on the quest icon. This does not seem to be the actual recruitment logic.
- `m_SortPriority`: explicit UI sort key.
- `m_QuestListViewCondition`: up to 5 `QuestTrigCondition.m_id` references that decide whether the quest is shown in the quest log.
- `m_QuestListHideCondition`: inverse hide-side conditions for the same quest entry.
- `m_AndTrigger`: likely switches the `m_QuestListViewCondition` logic between OR and AND style. `QuestTrigCondition#0` behaves like an always-true/no-op slot.
- `m_HideChapter`: if true, do not show `Chapter X` even if `m_Chapter` is set.
- `m_HideBossSilhouette` / `m_HideBossSilhouetteOnClear`: boss silhouette UI toggles for story-style quests.
- `m_Title`, `m_SubTitle`, `m_Synopsis`: point into `GameTextQuest.m_id`.
- `m_ScenarioProgress`: internal progress markers; still not fully decoded.
- `m_RecommNeedProg`: patterned progress-range values; probably story-progress guidance rather than ordinary levels.
- `m_RecommLv`: recommended level triplet.
- `m_InfluenceType`: which route/arc the quest belongs to.
- `m_Influence`: influence reward tuple.
- `m_HideInfoFlag`: likely toggles vague/hidden UI presentation.
- `m_DisablePriorityDialog`: still unclear.
- `m_ShowToBeContinued`: shows the `To Be Continued` end tag.
- `m_EnableReorder`: unused in OT0, but used in CotC for selected side-content style quests.
- `m_JobType`, `m_ScenarioPlayerID`, `m_BeforeAccept`: mostly unused in OT0; appear more active in CotC.
- `m_RewardType`, `m_RewardItemId`, `m_RewardItemNum`, `m_RewardOption0`: reward declaration block. OT0 only uses part of it, but the fields are still worth filling correctly if you want UI consistency.

Raw-export details that are easy to miss:
- `m_HideQuestList` is a hard hide flag separate from conditional visibility.
- `m_QuestListHideCondition` is a single integer in OT0's exported DB, not a parallel 5-slot array like `m_QuestListViewCondition`.
- `m_Chapter` is the actual displayed chapter number when `m_HideChapter` is false.
- `m_RecommLv`, `m_RecommNeedProg`, and `m_Influence` are fixed-size arrays; the reward fields are 4-slot arrays in the raw export. Preserve their shape when editing JSON by hand.

### `QuestTrigCondition`

This is the core data-driven predicate system.

`QuestTrigCondition.m_id` is the reusable condition-row ID referenced by other tables. `QuestTrigCondition.m_ConditionID` is the condition type/opcode that tells the engine how to interpret the row.

Field-by-field:
- `m_QuestRollbackId`: rollback/reset group. This is not a quest ID.
- `m_ConditionID`: quest/global-trigger condition type. This is a different namespace from battle/AI/skill `m_Conditions`.
- `m_OwnerQuest`: target `QuestList.m_id` for quest-state checks.
- `m_OwnerQuestTask`: target `QuestTaskList.m_id` for task-state checks.
- `m_OwnerUniqueChara`: usually points at `CharaPlayer.m_UniqueID`.
- `m_EventID`: often an `Event/EventList.m_id` reference.
- `m_EnemyID`: enemy target.
- `m_EnemyRaceID`: enemy-race target.
- `m_ItemID`: item possession/use target.
- `m_EquipID`: apparently equipment-related; unused in OT0 rows.
- `m_JobID`, `m_GenderID`, `m_InfluenceID`: strongly typed enum-driven checks.
- `m_EventFlagVariableID`: points into `EventFlagVariableList`.
- `m_UtcDateTime`: used in CotC time-gated content; probably a Unix timestamp check.
- `m_IntParam`: extra integer payload interpreted by `m_ConditionID`.
- `m_StrParam`: extra string payload interpreted by `m_ConditionID`.

Strong parameter hints from OT0 data:
- `m_ConditionID = 53 (eCOND_MAP_REACH)` uses `m_IntParam` values that line up cleanly with `Map/MapListTable.m_id`.
- `m_ConditionID = 162` uses `m_IntParam` values that match `CharaPlayer.m_UniqueID` very well.
- `m_ConditionID = 181 (eCOND_ENEMY_GROUP)` uses `m_StrParam` as `EnemyGroups.m_Label` strings.
- `m_ConditionID = 202 (eCOND_HAS_VILLAGE_BUILDING)` uses `m_IntParam` as `VillageBuildingData.m_id`.
- `m_ConditionID = 13/14` frequently stores `EventList.m_Name` strings in `m_StrParam`.

Direct users of `QuestTrigCondition.m_id` are much broader than just `QuestList`. OT0 also uses them in:
- `QuestTaskList.m_StartConditionID` / `m_OpenConditionID`
- `NpcSetList_*` and `NpcTalkList_*`
- `PartyChatList`
- `MapObjectConditionsList`
- `WorldMapList`, `WorldMapIllustList`, `WorldMapMaskList`
- `StoryBookCondition`
- `CharaNpcList`, `CharaJoin`, `MainPlayerIllust`, `MainPlayerRingIllust`
- `NoteList`, `MonsterArenaList`, `VillageBuildingData`, `VillageBuildingLimit`, `VillageTownLevelCondition`

Important distinction: tables like `QuestList`, `NpcSetList_*`, `NpcTalkList_*`, `StoryBookCondition`, and `WorldMap*` consume `QuestTrigCondition.m_id` row IDs. By contrast, `QuestTaskList.m_ClearConditionID` and `StoryBookList.m_ReadCondition` consume the direct `EQUEST_CONDITION` type namespace itself. Mixing those two layers up is one of the easiest ways to build a quest that looks valid in JSON but never fires in-game.

Useful OT0 mental model for common condition types:
- `1`: specific quest task is active
- `2`: quest is ready
- `3`: quest is active/in progress
- `4`: quest is clear
- `12`: event is clear
- `53`: map reached/current location style check
- `123`: event flag / variable check

CotC-exclusive keys seen in the SDK/data:
- `m_MultipleConditions`
- `m_WorldMode`

### `QuestTaskList`

This is the practical backbone of quest progression. `QuestList` says the quest exists; `QuestTaskList` says what the player is currently supposed to do and what events/tasks move the quest forward.

The SDK exposes:
```hpp
enum class EQUEST_TASK_TYPE : uint8
{
	NONE                                     = 0,
	START                                    = 1,
	PROGRESS                                 = 2,
	EQUEST_TASK_MAX                          = 3,
};
```

Useful fields:
- `m_id`: task-row ID.
- `m_OwnerQuest`: owning `QuestList.m_id`.
- `m_OwnerTask`: parent task/group reference for chained task structures.
- `m_TaskType`: `START` vs `PROGRESS` in OT0.
- `m_TaskNo`: order/index shown inside the quest.
- `m_HideTask`: hide this task from the UI.
- `m_Purpose`, `m_Synopsis`, `m_NextDestination`: text/UI helpers.
- `m_DestNpcSetLabel`: label of the target NPC set/object used for destination guidance.
- `m_DestPathName`: path actor name used for navigation/UI guidance.
- `m_DestTownInteriorAppearPlacePointId`: interior destination anchor.
- `m_ClearedQuest`: array of quests considered already cleared/related for this row.
- `m_OpenConditionID`: `QuestTrigCondition.m_id` rows that make the task appear/open.
- `m_StartConditionID`: `QuestTrigCondition.m_id` rows that make the task start/activate.
- `m_InvalidConditionID`: disable/invalidate-side condition array.
- `m_ReorderMax`, `m_ReorderInterval`: task ordering controls; still not fully decoded.
- `m_SubClearConditionID`: extra row-ID based clear helpers.
- `m_ClearConditionID`: direct condition type/opcode, not a `QuestTrigCondition.m_id`. This is a big one.
- `m_CondQuest`, `m_CondQuestTask`, `m_OwnerUniqueChara`, `m_EventID`, `m_EnemyID`, `m_EnemyRaceID`, `m_ItemID`, `m_IntParam`, `m_StrParam`: inline payload for `m_ClearConditionID`.
- `m_StartEvent`: event launched when the task starts.
- `m_ShowStartUI`: whether to show task-start UI.
- `m_ClearEvent`: event launched when the task clears.
- `m_ShowClearUI`: whether to show task-clear UI.
- `m_TaskItemId`, `m_TaskItemNum`: collect/deliver style task payload.
- `m_AddMemberSetID`: points into `PlayerMemberSet` and appears to change party availability.
- `m_RemovePlayerIDs`: remove specific players from the available party set.

In practice, if a quest feels “present but never advances,” `QuestTaskList` and `QuestTrigCondition` are the first two places to audit.

Raw-export gotchas:
- `m_OpenConditionID`, `m_StartConditionID`, `m_InvalidConditionID`, `m_SubClearConditionID`, `m_TaskItemId`, `m_TaskItemNum`, and `m_RemovePlayerIDs` are arrays, not single values.
- In OT0, the vast majority of nonzero `m_ClearConditionID` values are `12 (eCOND_EVENT_CLEAR)`, so many tasks clear on “did this event finish?” rather than on a reusable `QuestTrigCondition` row.

### `LimitConditions`

This table appears to be a map/area/system-rule table rather than a quest-log table.

Observed fields:
- `m_LimitType`: array of per-slot rule types.
- `m_worldMapID`, `m_mapID`: world/map targets.
- `m_BootQuestStaet`, `m_BootQuestID`, `m_BootQuestTaskId`: activate-side quest state linkage.
- `m_DisableQuestState`, `m_DisableQuestID`, `m_DisableQuestTaskId`: disable-side quest state linkage.
- `m_TrigConditionID`, `m_NoneConditionID`: extra `QuestTrigCondition.m_id` gates.
- `m_BgmID`, minimap texture fields, and `m_MinimapLCLabel`: extra presentation/system payload.

Useful SDK enums here:
- `EQUEST_STATE`: `0 NONE`, `1 READY`, `2 ACTIVE`, `3 CLEAR`
- `ELIMIT_CONDITIONS_TYPE`: OT0 most often uses `8 eFIX_FIELD_BGM`, `2 eFASTTRAVEL_BAN`, `29 eMINIMAP_FAST_TRAVEL_BAN`, `13 eMINIMAP_HIDE_ICON`, `6 eMINIMAP_EXTRA_JOINMAP`, and `97 eMINIMAP_FASTTRAVEL_EXCLUDE_FROM_SEARCH`

Use this when you need a quest or event to also change environmental/map-level behavior instead of just quest-log state.

Full SDK entry:
```hpp
// Enum Kingship.ELIMIT_CONDITIONS_TYPE
// NumValues: 0x0063
enum class ELIMIT_CONDITIONS_TYPE : uint8
{
	eNONE_BAN                                = 0,
	eBATTLE_BAN                              = 1,
	eFASTTRAVEL_BAN                          = 2,
	eFASTTRAVEL_REPOP                        = 3,
	eHOMEMENU_BAN                            = 4,
	eMINIMAP_BAN                             = 5,
	eMINIMAP_EXTRA_JOINMAP                   = 6,
	eMINIMAP_FINDMAP_SUB                     = 7,
	eFIX_FIELD_BGM                           = 8,
	eIGNORE_TOWN_RELATION                    = 9,
	eMINIMAP_EXTRA_LINKMAP                   = 10,
	eMAINMENU_SIMPLEPARTYEDHIT               = 11,
	eMINIMAP_CHANGE_TEXTURE                  = 12,
	eMINIMAP_HIDE_ICON                       = 13,
	eVILLAGE_FREE_BUILD_MENU                 = 14,
	eVILLAGE_FREE_RESIDENT_REGISTER          = 15,
	eMAINMENU_ABILITY_BAN                    = 16,
	eVILLAGE_FREE_BUILT_TRANSLATE            = 17,
	eMAINMENU_WORLDMAP_BAN                   = 18,
	eMAINMENU_RECORD_BAN                     = 19,
	eMAINMENU_PARTYEDIT_BAN                  = 20,
	eFIELD_COMMOND_BAN                       = 21,
	eFC_ILLUMINATE_PERMISSION                = 22,
	eVILLAGE_RESIDENT_AUTO_PLACE             = 23,
	eMAINMENU_ABILITY_LEAN_BAN               = 24,
	eMAINMENU_ABILITY_EQUIP_BAN              = 25,
	eMAINMENU_JOBCHANGE_BAN                  = 26,
	eFC_INVITE_PERMISSION                    = 27,
	eVILLAGE_SHOW_TOWN_TASK                  = 28,
	eMINIMAP_FAST_TRAVEL_BAN                 = 29,
	eFAST_TRAVEL_EX                          = 30,
	eWORLDMAP_EX                             = 31,
	eANIMAL_LANGUAGE                         = 32,
	eVILLAGE_FREE_GRADEUP                    = 33,
	eVILLAGE_FREE_SKIN_SELECT                = 34,
	eFC_NEXT_HEAR_PERMISSION                 = 35,
	eBLUE_TREASURE_BOX_OPEN                  = 36,
	eBATTLE_COOK_BAN                         = 37,
	eFC_B_END_PERMISSION                     = 38,
	eVILLAGE_FREE_EDIT_FIELD_MENU            = 39,
	eVILLAGE_FREE_EDIT_PALETTE               = 40,
	eVILLAGE_FREE_BUILT_DESTRUCT             = 41,
	eVILLAGE_PC_RANDOM_PLACE                 = 42,
	eFREE_MUSEUM_MEMO                        = 43,
	eFREE_MUSEUM_ANTIQUE                     = 44,
	eMAINMENU_RECOVERYABILITY_BAN            = 45,
	eKIRAKIRA_POINT_SHOW_BAN                 = 46,
	eMAINMENU_ITEM_BAN                       = 47,
	eMAINMENU_EQUIP_BAN                      = 48,
	eMAINMENU_STATUS_BAN                     = 49,
	eMAINMENU_INFLUENCE_BAN                  = 50,
	eTRADE_SHOP_MANUAL_UPDATE                = 51,
	eMINIMAP_FORCED_PATH_CLOSED              = 52,
	eMINIMAP_ENABLE_LIMITED_SECTION          = 53,
	eBATTLE_CALL_SUPPORT_DISABLE             = 54,
	eBATTLE_CALL_SUPPORT_HIDDEN              = 55,
	eVILLAGE_FREE_RESIDENT_OUTSIDE_PLACE     = 56,
	eWORLDMAP_VILLAGE_NOTIFICATION_BAN       = 57,
	eFIELD_MENU_BAN                          = 58,
	ePARTY_MEMBER_SHOW_BAN                   = 59,
	eFC_ILLUMINATE_SPECIAL_PERMISSION        = 60,
	ePARTYCHAT_BAN                           = 61,
	eACH_ELFRIC_RING_OBTAINED                = 62,
	eACH_DREFAND_RING_OBTAINED               = 63,
	eACH_BRAND_RING_OBTAINED                 = 64,
	eACH_EBER_RING_OBTAINED                  = 65,
	eACH_ALEFAN_RING_OBTAINED                = 66,
	eACH_BIFERGAN_RING_OBTAINED              = 67,
	eACH_SILTIGE_RING_OBTAINED               = 68,
	eACH_DOTHER_RING_OBTAINED                = 69,
	eACH_ALL_TREASURES_OBTAINED              = 70,
	eACH_MAX_TRAVELER_BOND                   = 71,
	eACH_BASE_CONSTRUCTION                   = 72,
	eACH_FARM_CONSTRUCTION                   = 73,
	eACH_SHOP_CONSTRUCTION                   = 74,
	eACH_CHURCH_CONSTRUCTION                 = 75,
	eACH_TORCH_CONSTRUCTION                  = 76,
	eACH_MUSEUM_CONSTRUCTION                 = 77,
	eACH_ARENA_CONSTRUCTION                  = 78,
	eACH_TOWN_LEVEL_MAX                      = 79,
	eACH_FIRST_ABILITY_LEARNED               = 80,
	eACH_ALL_MUSIC_OBTAINED                  = 81,
	eACH_OWN_SHIP                            = 82,
	eACH_DRAGON_DEFEATED                     = 83,
	eACH_REACHED_HELL                        = 84,
	eACH_ENDING_A                            = 85,
	eACH_ENDING_B                            = 86,
	eVILLAGE_BOX_BAN                         = 87,
	eAUTO_SAVE_BAN                           = 88,
	eRESIDENT_SKILL_ITEM_SUPPLY_BAN          = 89,
	eMAINMENU_ABILITY_OPENED_BAN             = 90,
	eSTATUSMENU_CHARAILLUST_BAN              = 91,
	eMAINMENU_QUEST_BAN                      = 92,
	eCHARACTER_TAB_RESERVE_BAN               = 93,
	eWORLDMAP_B_END_BAN                      = 94,
	eVILLAGE_DEFAULT_BGM__BAN                = 95,
	eSTATUSMENU_SAZANTOS_ILLUST_OVERWRITE    = 96,
	eMINIMAP_FASTTRAVEL_EXCLUDE_FROM_SEARCH  = 97,
	ELIMIT_CONDITIONS_MAX                    = 98,
};
```

### `MainStoryRingList`

Very small table. The SDK layout only exposes:
- `m_PrevTermRingTex`
- `m_PrevTermQuestId`

This looks like story-ring/previous-term presentation data rather than progression logic. If you are not touching main-story ring UI, you can usually leave it alone.

### `QuestAggregatedPoint`

This looks like a reusable destination/marker anchor table.

Fields:
- `m_id`
- `m_LevelId`
- `m_AppearLabel`

Use it when several quest tasks should collapse onto the same world marker/anchor instead of each owning its own destination directly.

### `QuestAggregatedTaskList`

This links individual `QuestTaskList` rows to aggregated points.

Fields:
- `m_QuestTaskId`
- `m_AggregatedPointId`
- `m_Priority`

In other words: `QuestAggregatedPoint` defines the marker anchor, `QuestAggregatedTaskList` assigns tasks to it.

## Under `Event/`

### `EventList`

`EventList` is the registry for playable/callable event assets.

It tells the engine which event JSON asset exists, what map it belongs to, and what return/location metadata is applied when the event exits.

Important fields:
- `m_id`: event ID used elsewhere (`QuestTaskList`, `NpcTalkList_*`, `StoryBookList`, `ScenarioReplay*`, event commands, etc.).
- `m_Version`: folder/category style grouping. In practice it is part of how the real asset path is organized.
- `m_Name`: event asset name. This is the most important field after `m_id`.
- `m_MapID`: owning/primary map.
- `m_ReturnMapID`, `m_ReturnPathActorName`, `m_ReturnPos`, `m_ReturnDir`: where the game returns the player after the event.
- `m_Kind`: event category. The SDK exposes `EEVENT_KIND` with boss/chara/sub/etc. groupings.
- `m_PlayBGM`, `m_SoundCueID`: audio setup.
- `m_Skip`: skip behavior.
- `m_BanAutoPlay`, `m_MapLoadWait`, `m_Seamless`, `m_StartEnvVolumeZero`: loading/playback behavior flags.
- `m_IsVillageEvent`, `m_IsWarpEvent`, `m_IsNotHideMapUI`, `m_BanChangePlayMode`, `m_BanEncountMgrBgmChange`, `m_BanLoadBattleLevel`: specialized runtime flags.

Relevant `EEVENT_KIND` values include `1 BOSS_EVENT`, `2 CHARA_EVENT`, `3 SUB_EVENT`, `16 TOWN`, `18 VILLAGE`, `19 PARTYCHAT`, `27 JOIN_EVENT`, and `28 ARRIVAL_EVENT`.

Practical rule: if you add a new event asset but do not add a matching `EventList` row, almost nothing else will be able to call it cleanly.

All event kinds:
```hpp
// Enum Kingship.EEVENT_KIND
// NumValues: 0x0021
enum class EEVENT_KIND : uint8
{
	NONE                                     = 0,
	BOSS_EVENT                               = 1,
	CHARA_EVENT                              = 2,
	SUB_EVENT                                = 3,
	TEST_EVENT                               = 4,
	B00_EVENT                                = 5,
	B01_EVENT                                = 6,
	B02_EVENT                                = 7,
	B03_EVENT                                = 8,
	B04_EVENT                                = 9,
	B05_EVENT                                = 10,
	B06_EVENT                                = 11,
	B07_EVENT                                = 12,
	B08_EVENT                                = 13,
	TOOL                                     = 14,
	BATTLE                                   = 15,
	TOWN                                     = 16,
	LEVEL                                    = 17,
	VILLAGE                                  = 18,
	PARTYCHAT                                = 19,
	T00_EVENT                                = 20,
	T01_EVENT                                = 21,
	T02_EVENT                                = 22,
	T03_EVENT                                = 23,
	T04_EVENT                                = 24,
	T05_EVENT                                = 25,
	T06_EVENT                                = 26,
	JOIN_EVENT                               = 27,
	ARRIVAL_EVENT                            = 28,
	OTHER_EVENT                              = 29,
	TOWNBUILD                                = 30,
	TUTORIAL                                 = 31,
	EEVENT_MAX                               = 32,
};
```

### Event JSON assets

The actual event commands live in the event assets under `Event/V*/.../*.uasset`.

Typical workflow:
- duplicate a similar existing event JSON
- give it a new filename matching your new `EventList.m_Name`, also ensure the metadata of the created assets match these filenames (use the [latest experimental release of UAssetGUI](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest) for that)
- update IDs/text/targets inside the command rows
- point `QuestTaskList.m_StartEvent`, `QuestTaskList.m_ClearEvent`, `NpcTalkList_* .m_EventID`, or other callers at the new `EventList.m_id`

For command-by-command opcode notes, see:
- [Event Opcode Crossref Atlas](https://github.com/veganprimate/CotCPort/blob/main/Event/Event_Opcode_Crossref_Atlas.md) 

### `EventFlagVariableList`

This is the numeric event flag / event variable table used by event logic and by `QuestTrigCondition.m_EventFlagVariableID`.

Observed fields:
- `m_id`: flag/variable ID.
- `m_FlagType`: type/category of the flag.
- `m_VolatilityValidMap`: map scoping for volatile flags.
- `m_UpperLimit`, `m_LowerLimit`: numeric range/clamp metadata.

Use this when you need a quest/event to remember a custom state that is not already represented by quest/task clear state alone.

### `EventFlagObject`

`EventFlagObject/*.uasset` uses a different, more symbolic condition system: rows carry string lists like `conditions = ["..."]` rather than `QuestTrigCondition.m_id` integers.

Treat it as a neighboring map/object condition layer, not as the same namespace as `QuestTrigCondition`.

## Under `StoryBook/`

### `StoryBookCondition`

This table bridges StoryBook/Journal unlock logic to the quest-trigger system.

Fields:
- `m_OpenCondition`: usually a `QuestTrigCondition.m_id`
- `m_ConditionText`, `m_ConditionExplain`: UI text
- `m_ReccomendLevel`: recommendation value
- `m_QuestID`: quest association
- `m_MapID`: map association

If you want a new Journal entry to unlock when a quest/event condition is met, this is the cleanest bridge.

### `StoryBookList`

This is the actual StoryBook entry metadata table.

Useful fields:
- title/tab/category/icon fields
- `m_ApperStoryBookCondition`, `m_ApperQuestCondition`, `m_ApperCondition`
- `m_OpenCondition`
- `m_ReadCondition`
- reward fields
- `m_EventID`
- battle metadata fields

Important nuance:
- `StoryBookCondition.m_OpenCondition` uses `QuestTrigCondition.m_id` indirection.
- `StoryBookList.m_ReadCondition` directly uses the quest/global-trigger condition type namespace instead.

## Under `Character/`

### `PlayerMemberSet`

This is the table behind `QuestTaskList.m_AddMemberSetID`.

Fields:
- `m_ValidCondition`: `QuestTrigCondition.m_id` that enables the member-set row
- `m_PlayerIDs`: up to 8 player IDs / party slots, though the first one is only for the main character and may cause a crash if set to a Player ID beside's Zero's

Use this when a quest event should temporarily force, add, or swap the available party composition.

### `CharaJoin`

Character join/unlock gating table.

Important field:
- `m_ConditionID`: `QuestTrigCondition.m_id` gate for the join row

Exact usage still unclear

### `CharaNpcList`

Higher-level NPC interaction gating separate from the map-local talk tree itself.

Important fields referenced by the condition atlas:
- `m_Hear_CheckCondition`
- `m_Invite_CheckCondition`
- `m_ReleaseConditionID`
- `m_VillageInviteReleaseConditionId`

These are especially relevant if your quest changes who can be heard/invited/released rather than only what line of dialogue they say.

## Under `Npc/`

### `NpcSetList_*`

These files usually control whether an NPC/object appears in the world at all.

Important fields:
- owner/object/talk identifiers
- `m_MapID`
- `m_AppearLabel`
- `m_TalkID`
- quest boot/disable fields
- `m_TrigConditionID`
- `m_NoneConditionID`

Typical use: make a quest giver appear only after one condition, disappear after another, or swap to a different talk tree after progression.

### `NpcTalkList_*`

These files usually control the actual talk nodes/branches for map-specific NPC interactions.

Important fields:
- `m_TalkText`
- `m_EventID`
- `m_QuestID`
- `m_TrigConditionID`
- `m_NoneConditionID`
- choice text / influence values / next talk numbers

Typical use: gate different talk branches by quest progress, and optionally launch a quest event from a talk node.

### Root `NpcTalkList`

This appears to use a separate/local small-ID condition namespace rather than full `QuestTrigCondition.m_id` rows. Do not assume its `m_TrigConditionID` / `m_NoneConditionID` values are interchangeable with `NpcTalkList_*` or `QuestList`.

## Under `Map/`

### `MapListTable`

This is the main map registry.

For quest/event work, the most useful fields are:
- `m_MapLabel`, `m_MapText`, `m_MapPath`, `m_PathDataPath`
- `m_PlayerAttachEffectTriggers`
- `m_PlayerAttachEffectInvalidConditions`

The attach-effect condition arrays are another place where `QuestTrigCondition.m_id` rows get consumed.

### `MapReplaceList`

Conditionally swaps map state/layout.

Useful fields:
- `m_EnableTrigConditionID`
- `m_DisableTrigConditionID`

Use it if quest progress must alter the map itself rather than only an NPC.

### `WorldMapList`, `WorldMapIllustList`, `WorldMapMaskList`

These control world-map markers, illustrations, and reveal masks.

All three use condition arrays to show/hide content depending on quest progress. This is the part to edit if your custom quest should reveal map art, clear fog, or add/remove world-map markers.

### `WorldList`

Higher-level world-state visibility gating.

Important fields:
- `m_TrigConditionIds`
- `m_InvalidConditionIds`

This is another place where broad world-map or region presentation can diverge from the quest log itself.

## Under `Object/`

### `MapObjectConditionsList`

Per-object show/hide or enable/disable gating for map objects.

Important fields:
- `m_TrigConditionID`
- `m_NoneConditionID`

If a quest needs to reveal or suppress objects, doors (e.g. the Gate of Orsa), interactables, signs, gimmicks, or other placed objects, this table is often just as important as `NpcSetList_*`.

## Under `ScenarioReplay/`

Scenario replay data is mostly presentation/UI content that points back at quests and events.

Useful tables:
- `ScenarioReplayChapterParam`: chapter card visuals and `m_SilhouetteCond`
- `ScenarioReplayMainSectionParam`: section titles and `m_EventId` arrays, plus capture/check-entry event IDs
- `ScenarioReplaySubSectionParam`: subsection rows keyed by `m_QuestId` and `m_EventId`
- `ScenarioReplayBondSectionParam`: bond/character recap entries with `m_EventId`
- `ScenarioReplayMainSectionText`: section text and replacement event conditions

You usually do not need these for a mechanically functional quest, but you do need them if you want your custom content to appear in replay/recap UI.

## Common errors

- `QuestTrigCondition.m_ConditionID` is not the same namespace as battle `m_Conditions`.
- `QuestTaskList.m_ClearConditionID` is a direct condition type, not a `QuestTrigCondition.m_id`.
- `NpcTalkList.json` and `NpcTalkList_*` do not appear to use the same condition namespace.
- `EventList.m_Name` must match the real event asset name; the asset's metadata has to be patched accordingly
- Many tables reference labels (`m_AppearLabel`, `m_DestNpcSetLabel`, path actor names) rather than only numeric IDs.
- UI visibility, world presence, and actual progression are often split across different tables; fixing only one of them is usually not enough.

## Extra DB / SDK findings

- `StoryBookList.m_OpenCondition` is an array of `StoryBookCondition.m_id` values; it is not the same layer as `QuestTrigCondition.m_id`.
- `StoryBookList.m_ApperQuestCondition` behaves mostly like a quest-ID side gate, while `m_ApperCondition` still looks like a separate appearance-side condition namespace that is not fully decoded yet.
- `MapReplaceList` can gate swaps either by quest-state fields (`m_EnableQuestID`, `m_EnableQuestState`, `m_EnableQuestTaskID`) or by trigger-condition arrays; both paths exist in OT0.
- `MainStoryRingList` is tiny in OT0 and the current export also contains a mysterious always-empty `m` array, so it appears to be safe to ignore unless you are explicitly touching main-story ring UI.
- `MapObjectConditionsList` and `NpcSetList_*` often need to be updated together: one controls placed objects, the other controls placed NPC/object sets (e.g. signs + the text signs bear, implemented with an invisible NPC at the same position).

## Debugging checklist

- **Quest does not show in the log**: check `QuestList`, its text IDs, and the view/hide condition IDs.
- **NPC or object does not appear**: check `NpcSetList_*`, `MapObjectConditionsList`, `LimitConditions`, and map-replacement rules.
- **Wrong talk branch appears**: confirm whether the map uses `NpcTalkList_*` or the root `NpcTalkList.json` condition namespace.
- **Task never starts**: audit `QuestTaskList.m_OpenConditionID`, `m_StartConditionID`, and `m_InvalidConditionID`.
- **Task never clears**: audit the inline `m_ClearConditionID` payload (`m_CondQuest`, `m_EventID`, `m_ItemID`, `m_IntParam`, `m_StrParam`), not only `QuestTrigCondition`.
- **Event never plays**: confirm `EventList.m_id`, `m_Name`, `m_Version`, event asset metadata, and caller field all match up.
- **Party changes do not happen**: check `PlayerMemberSet` plus `QuestTaskList.m_AddMemberSetID` / `m_RemovePlayerIDs`, as well as `CharaPlayer` in case you have added new playable characters
- **StoryBook or replay UI is missing**: add the glue rows in `StoryBook/*` and `ScenarioReplay/*`; the quest itself can work without them, but the UI cannot.
