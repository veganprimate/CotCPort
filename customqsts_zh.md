## **LANGUAGE:**  [English](customqsts_en.md) | [日本語](customqsts_ja.md) | [中文](customqsts_zh.md) | [한국어](customqsts_ko.md)

# 如何添加或修改任务与事件

本文是一份实用导图，专门整理在你想新增或修改任务流程、事件流程、StoryBook 条目、NPC 出现条件以及相关 UI 状态时，真正重要的那些表。

为了与仓库路径和跨文档比较保持一致，本文仍会使用 `OT0` 这一缩写；但在中文语境中，更自然的写法通常是官方标题《歧路旅人 0》，另外也经常能看到系列旧译／俗称《八方旅人 0》。

进行任务／事件相关工作时，通常会涉及 `Quest/`、`Event/`、`StoryBook/`，并且也经常会涉及 `Npc/`、`ScenarioReplay/`、`Map/`、`Character/`、`Item/`、`PartyChat/`。

如果你要新增一整套全新的任务，通常最小可工作集合是：
- `GameTextQuest` 中的文本（而且经常还需要 `GameTextEvent` / `TalkText` 与 `GameTextNPC` / `TalkText`）
- **一条** `QuestList` 行
- **一条或多条** `QuestTaskList` 行
- **一条或多条** `QuestTrigCondition` 行
- **一条或多条** `EventList` 行，加上实际的事件资源
- 至少 **一张** 把任务暴露给玩家的消费端表（`NpcSetList_*`、`NpcTalkList_*`、`StoryBook*`、`Map*` 等）

## 高层工作流程

1. 预留 ID 与标签。
   - 选择不会与现有行冲突的新 `m_id`。
   - 对事件而言，要选择唯一的 `EventList.m_Name`，并让资源文件名与其一致。
   - 对 NPC／地图锚点而言，复用或创建唯一标签，如 `m_AppearLabel`、`m_DestNpcSetLabel` 与地图路径中的 Actor 名。

2. 先补文本。
   - 任务菜单／标题文本主要位于 `GameTextQuest`。
   - 事件对白与旁白则使用事件／会话文本数据库。

3. 建立任务外壳。
   - 在 `QuestList` 中加入菜单项。
   - 在 `QuestTaskList` 中加入开始／推进步骤。

4. 建立可复用条件。
   - 为显示、可用性、完成、解锁、地图揭示、NPC 出现等建立 `QuestTrigCondition` 行。

5. 加入实际事件。
   - 增加 `EventList` 行。
   - 在对应的 `Event/` 子目录下创建事件资源。
   - 将事件 ID 接到 `QuestTaskList`、`NpcTalkList_*`、`StoryBookList` 或其他负责启动该场景的地方。

6. 将任务接到世界内容上。
   - NPC／物件出现通常通过 `NpcSetList_*` 或 `MapObjectConditionsList`。
   - 对话分支通常通过 `NpcTalkList_*`。
   - 世界地图、StoryBook、队伍对话、村庄建设或 replay UI 的显示，则各自由专门的表负责。

## `Quest/` 下

### `QuestList`

这里决定任务菜单里显示什么：标题／副标题／概要、章节编号、推荐等级、排序，以及何时显示／隐藏。若你希望一个任务在任务 UI 中存在，这里就必须有一行。

- `m_QuestCategory`: 任务 UI 中显示的大分类。SDK 定义如下：
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
- `m_QuestSubCategory`: 分类内部的子分类，主要用于主线风格内容。SDK 定义如下：
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
- 任务分类 `4`（`SUB`）有一个特殊子分类 `20`，看起来被 Wishvale 主线内容使用。
- `m_JoinPlayerID`: 控制任务图标上显示哪个角色头像／头部图标。看起来不是实际的入队逻辑。
- `m_SortPriority`: 明确的 UI 排序键。
- `m_QuestListViewCondition`: 最多 5 个 `QuestTrigCondition.m_id` 引用，用来决定任务是否显示在任务日志中。
- `m_QuestListHideCondition`: 同一任务项的反向隐藏条件。
- `m_AndTrigger`: 看起来用于在 OR 与 AND 逻辑之间切换 `m_QuestListViewCondition` 的处理方式。`QuestTrigCondition#0` 的行为类似“恒真／空操作槽”。
- `m_HideChapter`: 若为 true，则即便设置了 `m_Chapter` 也不显示 `Chapter X`。
- `m_HideBossSilhouette` / `m_HideBossSilhouetteOnClear`: 用于故事风格任务的 Boss 剪影 UI 开关。
- `m_Title`、`m_SubTitle`、`m_Synopsis`: 指向 `GameTextQuest.m_id`。
- `m_ScenarioProgress`: 内部进度标记；尚未完全解码。
- `m_RecommNeedProg`: 呈现固定模式的进度范围值；很可能是剧情进度指引，而不是普通等级。
- `m_RecommLv`: 推荐等级三元组。
- `m_InfluenceType`: 任务属于哪条路线／篇章。
- `m_Influence`: 影响力奖励元组。
- `m_HideInfoFlag`: 很可能用于切换模糊／隐藏式 UI 展示。
- `m_DisablePriorityDialog`: 仍不清楚。
- `m_ShowToBeContinued`: 显示 `To Be Continued` 结尾标签。
- `m_EnableReorder`: OT0 中未使用，但在《歧路旅人：大陆的霸者》中用于部分支线风格任务。
- `m_JobType`、`m_ScenarioPlayerID`、`m_BeforeAccept`: 在 OT0 中基本未使用；在《歧路旅人：大陆的霸者》中看起来更活跃。
- `m_RewardType`、`m_RewardItemId`、`m_RewardItemNum`、`m_RewardOption0`: 报酬声明块。OT0 只使用其中一部分，但若要保持 UI 一致性，仍值得正确填写。

生导出中容易忽略的细节：
- `m_HideQuestList` 是一个独立于条件显示之外的硬隐藏标记。
- 在 OT0 的导出 DB 中，`m_QuestListHideCondition` 是单个整数，而不是像 `m_QuestListViewCondition` 那样的 5 槽数组。
- `m_Chapter` 才是在 `m_HideChapter` 为 false 时实际显示的章节号。
- `m_RecommLv`、`m_RecommNeedProg`、`m_Influence` 都是固定长度数组；奖励相关字段在生导出中是 4 槽数组。手工编辑 JSON 时要保持这个形状。

### `QuestTrigCondition`

这是核心的数据驱动谓词系统。

`QuestTrigCondition.m_id` 是供其他表引用的可复用条件行 ID。`QuestTrigCondition.m_ConditionID` 则是条件类型／操作码，用来告诉引擎应该如何解释这条行数据。

逐字段说明：
- `m_QuestRollbackId`: 回滚／重置组。它不是任务 ID。
- `m_ConditionID`: 任务／全局触发条件类型。这与 battle／AI／skill 里的 `m_Conditions` 不是同一个命名空间。
- `m_OwnerQuest`: 用于任务状态检查的目标 `QuestList.m_id`。
- `m_OwnerQuestTask`: 用于步骤状态检查的目标 `QuestTaskList.m_id`。
- `m_OwnerUniqueChara`: 通常指向 `CharaPlayer.m_UniqueID`。
- `m_EventID`: 经常是 `Event/EventList.m_id` 引用。
- `m_EnemyID`: 敌人目标。
- `m_EnemyRaceID`: 敌人种族目标。
- `m_ItemID`: 物品拥有／使用目标。
- `m_EquipID`: 看起来与装备相关；在 OT0 行中未使用。
- `m_JobID`、`m_GenderID`、`m_InfluenceID`: 强类型 enum 驱动的检查项。
- `m_EventFlagVariableID`: 指向 `EventFlagVariableList`。
- `m_UtcDateTime`: 用于《歧路旅人：大陆的霸者》的限时内容，看起来像 Unix 时间戳检查。
- `m_IntParam`: 由 `m_ConditionID` 决定解释方式的额外整数载荷。
- `m_StrParam`: 由 `m_ConditionID` 决定解释方式的额外字符串载荷。

根据 OT0 数据可以强烈提示的参数规律：
- `m_ConditionID = 53 (eCOND_MAP_REACH)` 使用的 `m_IntParam` 与 `Map/MapListTable.m_id` 对应得非常干净。
- `m_ConditionID = 162` 的 `m_IntParam` 与 `CharaPlayer.m_UniqueID` 非常吻合。
- `m_ConditionID = 181 (eCOND_ENEMY_GROUP)` 将 `m_StrParam` 作为 `EnemyGroups.m_Label` 字符串使用。
- `m_ConditionID = 202 (eCOND_HAS_VILLAGE_BUILDING)` 将 `m_IntParam` 作为 `VillageBuildingData.m_id` 使用。
- `m_ConditionID = 13/14` 经常把 `EventList.m_Name` 字符串放进 `m_StrParam`。

直接消费 `QuestTrigCondition.m_id` 的地方远不止 `QuestList`。OT0 中还包括：
- `QuestTaskList.m_StartConditionID` / `m_OpenConditionID`
- `NpcSetList_*` 与 `NpcTalkList_*`
- `PartyChatList`
- `MapObjectConditionsList`
- `WorldMapList`、`WorldMapIllustList`、`WorldMapMaskList`
- `StoryBookCondition`
- `CharaNpcList`、`CharaJoin`、`MainPlayerIllust`、`MainPlayerRingIllust`
- `NoteList`、`MonsterArenaList`、`VillageBuildingData`、`VillageBuildingLimit`、`VillageTownLevelCondition`

一个非常重要的区分：
- `QuestList`、`NpcSetList_*`、`NpcTalkList_*`、`StoryBookCondition`、`WorldMap*` 这类表消费的是 `QuestTrigCondition.m_id` 这一“行 ID 层”。
- 相对地，`QuestTaskList.m_ClearConditionID` 与 `StoryBookList.m_ReadCondition` 消费的是直接的 `EQUEST_CONDITION` 条件类型命名空间本身。

把这两个层级混起来，是最容易做出“JSON 看起来合法、游戏里却永远不触发”的任务的原因之一。

在 OT0 中，对常见条件类型的一个实用心智模型：
- `1`: 指定任务步骤处于 active
- `2`: 任务处于 ready
- `3`: 任务处于 active / in progress
- `4`: 任务已 clear
- `12`: 事件已 clear
- `53`: 到达地图／当前位置类检查
- `123`: 事件标记／变量检查

SDK／数据中可见的《歧路旅人：大陆的霸者》专用键：
- `m_MultipleConditions`
- `m_WorldMode`

### `QuestTaskList`

这是任务推进真正的骨架。`QuestList` 只说明“任务存在”，而 `QuestTaskList` 说明“玩家现在应该做什么，以及什么会推动任务前进”。

SDK 暴露了：
```hpp
enum class EQUEST_TASK_TYPE : uint8
{
	NONE                                     = 0,
	START                                    = 1,
	PROGRESS                                 = 2,
	EQUEST_TASK_MAX                          = 3,
};
```

有用字段：
- `m_id`: 步骤行 ID。
- `m_OwnerQuest`: 所属 `QuestList.m_id`。
- `m_OwnerTask`: 用于链式步骤结构的父步骤／父组引用。
- `m_TaskType`: OT0 中的 `START` 与 `PROGRESS`。
- `m_TaskNo`: 在任务中显示的顺序／编号。
- `m_HideTask`: 将该步骤从 UI 中隐藏。
- `m_Purpose`、`m_Synopsis`、`m_NextDestination`: 文本／UI 辅助字段。
- `m_DestNpcSetLabel`: 用于目标引导的目标 NPC 集／物件标签。
- `m_DestPathName`: 用于导航／UI 引导的路径 Actor 名。
- `m_DestTownInteriorAppearPlacePointId`: 室内目的地锚点。
- `m_ClearedQuest`: 被视为对该行已经完成／相关完成的任务数组。
- `m_OpenConditionID`: 让步骤显示／开放的 `QuestTrigCondition.m_id` 行。
- `m_StartConditionID`: 让步骤开始／激活的 `QuestTrigCondition.m_id` 行。
- `m_InvalidConditionID`: 禁用／失效侧条件数组。
- `m_ReorderMax`、`m_ReorderInterval`: 步骤排序控制；仍未完全解码。
- `m_SubClearConditionID`: 额外的基于行 ID 的完成辅助项。
- `m_ClearConditionID`: 直接条件类型／操作码，不是 `QuestTrigCondition.m_id`。这一点非常关键。
- `m_CondQuest`、`m_CondQuestTask`、`m_OwnerUniqueChara`、`m_EventID`、`m_EnemyID`、`m_EnemyRaceID`、`m_ItemID`、`m_IntParam`、`m_StrParam`: 为 `m_ClearConditionID` 提供的内联载荷。
- `m_StartEvent`: 步骤开始时启动的事件。
- `m_ShowStartUI`: 是否显示步骤开始 UI。
- `m_ClearEvent`: 步骤完成时启动的事件。
- `m_ShowClearUI`: 是否显示步骤完成 UI。
- `m_TaskItemId`、`m_TaskItemNum`: 收集／交付类步骤载荷。
- `m_AddMemberSetID`: 指向 `PlayerMemberSet`，看起来会改变队伍可用性。
- `m_RemovePlayerIDs`: 将指定玩家从可用队伍集合中移除。

在实践中，如果一个任务“看起来存在但永远不会推进”，首先要审查的两个地方就是 `QuestTaskList` 和 `QuestTrigCondition`。

生导出中的坑点：
- `m_OpenConditionID`、`m_StartConditionID`、`m_InvalidConditionID`、`m_SubClearConditionID`、`m_TaskItemId`、`m_TaskItemNum`、`m_RemovePlayerIDs` 都是数组，不是单个值。
- 在 OT0 中，绝大多数非零 `m_ClearConditionID` 都是 `12 (eCOND_EVENT_CLEAR)`。也就是说，许多步骤是靠“这个事件是否结束了”完成，而不是靠一个可复用的 `QuestTrigCondition` 行。

### `LimitConditions`

这张表看起来更像是地图／区域／系统规则表，而不是任务日志表。

观察到的字段：
- `m_LimitType`: 每槽位规则类型数组。
- `m_worldMapID`、`m_mapID`: 世界／地图目标。
- `m_BootQuestStaet`、`m_BootQuestID`、`m_BootQuestTaskId`: 启用侧任务状态联动。
- `m_DisableQuestState`、`m_DisableQuestID`、`m_DisableQuestTaskId`: 禁用侧任务状态联动。
- `m_TrigConditionID`、`m_NoneConditionID`: 额外的 `QuestTrigCondition.m_id` 门控。
- `m_BgmID`、小地图贴图字段以及 `m_MinimapLCLabel`: 附加的表现／系统载荷。

这里有用的 SDK enum：
- `EQUEST_STATE`: `0 NONE`、`1 READY`、`2 ACTIVE`、`3 CLEAR`
- `ELIMIT_CONDITIONS_TYPE`: OT0 最常用到的是 `8 eFIX_FIELD_BGM`、`2 eFASTTRAVEL_BAN`、`29 eMINIMAP_FAST_TRAVEL_BAN`、`13 eMINIMAP_HIDE_ICON`、`6 eMINIMAP_EXTRA_JOINMAP`、`97 eMINIMAP_FASTTRAVEL_EXCLUDE_FROM_SEARCH`

当你需要让任务或事件不仅改变任务日志状态，还改变环境／地图层行为时，就要用它。

完整 SDK 条目：
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

这是一个非常小的表。SDK 布局只暴露了：
- `m_PrevTermRingTex`
- `m_PrevTermQuestId`

它看起来更像是主线 ring／上一阶段的表现数据，而不是推进逻辑。如果你不碰主线 ring UI，通常可以不理它。

### `QuestAggregatedPoint`

它看起来像一张可复用的目的地／标记锚点表。

字段：
- `m_id`
- `m_LevelId`
- `m_AppearLabel`

当多个任务步骤应当汇聚到同一个世界标记／锚点，而不是各自直接拥有一个目的地时，就使用它。

### `QuestAggregatedTaskList`

它把单独的 `QuestTaskList` 行连接到聚合点。

字段：
- `m_QuestTaskId`
- `m_AggregatedPointId`
- `m_Priority`

换句话说：`QuestAggregatedPoint` 定义标记锚点，`QuestAggregatedTaskList` 把步骤分配给它。

## `Event/` 下

### `EventList`

`EventList` 是可播放／可调用事件资源的注册表。

它告诉引擎：有哪些事件资源存在、它属于哪个地图、事件结束后使用什么返回／位置元数据。

重要字段：
- `m_id`: 在其他地方使用的事件 ID（`QuestTaskList`、`NpcTalkList_*`、`StoryBookList`、`ScenarioReplay*`、事件命令等）。
- `m_Version`: 文件夹／类别式分组。实际上也是实际资源路径组织方式的一部分。
- `m_Name`: 事件资源名。这是除 `m_id` 外最重要的字段。
- `m_MapID`: 所属／主地图。
- `m_ReturnMapID`、`m_ReturnPathActorName`、`m_ReturnPos`、`m_ReturnDir`: 事件结束后玩家返回的位置。
- `m_Kind`: 事件类别。SDK 暴露了 `EEVENT_KIND`，其中包含 boss／chara／sub 等分组。
- `m_PlayBGM`、`m_SoundCueID`: 音频设置。
- `m_Skip`: 跳过行为。
- `m_BanAutoPlay`、`m_MapLoadWait`、`m_Seamless`、`m_StartEnvVolumeZero`: 加载／播放行为标志。
- `m_IsVillageEvent`、`m_IsWarpEvent`、`m_IsNotHideMapUI`、`m_BanChangePlayMode`、`m_BanEncountMgrBgmChange`、`m_BanLoadBattleLevel`: 特化运行时标志。

相关的 `EEVENT_KIND` 值包括 `1 BOSS_EVENT`、`2 CHARA_EVENT`、`3 SUB_EVENT`、`16 TOWN`、`18 VILLAGE`、`19 PARTYCHAT`、`27 JOIN_EVENT` 与 `28 ARRIVAL_EVENT`。

一个很实际的规则：如果你添加了新事件资源，却没有添加对应的 `EventList` 行，那么几乎没有其他地方能干净地调用它。

全部事件种类：
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

### Event assets

真正的事件命令位于 `Event/V*/.../*.uasset` 下的事件资源中。

典型流程：
- 复制一个相似的现有事件 JSON
- 给它一个与你的新 `EventList.m_Name` 一致的新文件名，同时也要确保所创建资源的元数据与这些文件名一致（这一步请使用 [UAssetGUI 的最新 experimental 版](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest)）
- 更新命令行中的 ID／文本／目标
- 把 `QuestTaskList.m_StartEvent`、`QuestTaskList.m_ClearEvent`、`NpcTalkList_* .m_EventID` 或其他调用方指向新的 `EventList.m_id`

逐命令 opcode 注记请参见：
- [Event Opcode Crossref Atlas](https://github.com/veganprimate/CotCPort/blob/main/Event/Event_Opcode_Crossref_Atlas.md)

### `EventFlagVariableList`

这是供事件逻辑和 `QuestTrigCondition.m_EventFlagVariableID` 使用的数值事件标记／事件变量表。

观察到的字段：
- `m_id`: 标记／变量 ID。
- `m_FlagType`: 标记类型／类别。
- `m_VolatilityValidMap`: 挥发性标记的地图作用域。
- `m_UpperLimit`、`m_LowerLimit`: 数值范围／钳制元数据。

当你需要让任务／事件记住一个并非单纯由任务／步骤完成状态表示的自定义状态时，就用它。

### `EventFlagObject`

`EventFlagObject/*.uasset` 使用的是另一套、更符号化的条件系统：行里携带的是 `conditions = ["..."]` 这种字符串列表，而不是 `QuestTrigCondition.m_id` 整数。

应当把它视为与 `QuestTrigCondition` 相邻的地图／物件条件层，而不是同一个命名空间。

## `StoryBook/` 下

### `StoryBookCondition`

这张表负责把 StoryBook／Journal 解锁逻辑桥接到任务触发系统。

字段：
- `m_OpenCondition`: 通常是 `QuestTrigCondition.m_id`
- `m_ConditionText`、`m_ConditionExplain`: UI 文本
- `m_ReccomendLevel`: 推荐值
- `m_QuestID`: 任务关联
- `m_MapID`: 地图关联

如果你希望一个新的 Journal 条目在满足任务／事件条件时解锁，这是最干净的桥。

### `StoryBookList`

这是实际的 StoryBook 条目元数据表。

有用字段：
- title／tab／category／icon 相关字段
- `m_ApperStoryBookCondition`、`m_ApperQuestCondition`、`m_ApperCondition`
- `m_OpenCondition`
- `m_ReadCondition`
- 奖励字段
- `m_EventID`
- battle 元数据字段

重要细节：
- `StoryBookCondition.m_OpenCondition` 使用的是 `QuestTrigCondition.m_id` 间接层。
- `StoryBookList.m_ReadCondition` 则直接使用任务／全局触发条件类型命名空间。

## `Character/` 下

### `PlayerMemberSet`

这就是 `QuestTaskList.m_AddMemberSetID` 背后的表。

字段：
- `m_ValidCondition`: 让该 member-set 行生效的 `QuestTrigCondition.m_id`
- `m_PlayerIDs`: 最多 8 个玩家 ID／队伍槽位，但第一个只用于主角，若填入 Zero 以外的 Player ID 可能会导致崩溃

当某个任务事件应当临时强制、追加或替换可用队伍编成时，就用它。

### `CharaJoin`

角色加入／解锁的门控表。

重要字段：
- `m_ConditionID`: 用于门控该加入行的 `QuestTrigCondition.m_id`

确切用法仍不完全清楚。

### `CharaNpcList`

这是一层更高层级的 NPC 交互门控，与地图本地的对话树本身分开。

在条件图谱中被引用的重要字段：
- `m_Hear_CheckCondition`
- `m_Invite_CheckCondition`
- `m_ReleaseConditionID`
- `m_VillageInviteReleaseConditionId`

如果你的任务会改变“打听”“邀请”“放人”等 NPC 交互能力，而不仅仅是改变他们说哪句台词，那么这些字段尤其重要。

## `Npc/` 下

### `NpcSetList_*`

这些文件通常控制 NPC／物件是否出现在世界中。

重要字段：
- owner／object／talk 标识
- `m_MapID`
- `m_AppearLabel`
- `m_TalkID`
- quest boot／disable 相关字段
- `m_TrigConditionID`
- `m_NoneConditionID`

典型用途：让任务委托人只在某个条件后出现，在另一个条件后消失，或者在推进后切换到另一棵对话树。

### `NpcTalkList_*`

这些文件通常控制地图专属 NPC 交互中的实际对话节点／分支。

重要字段：
- `m_TalkText`
- `m_EventID`
- `m_QuestID`
- `m_TrigConditionID`
- `m_NoneConditionID`
- 选项文本／影响力数值／下一对话编号

典型用途：按任务进度门控不同对话分支，并可选择从某个对话节点启动任务事件。

### 根 `NpcTalkList`

它看起来使用的是另一套本地小 ID 条件命名空间，而不是完整的 `QuestTrigCondition.m_id` 行。不要假定它的 `m_TrigConditionID` / `m_NoneConditionID` 可以与 `NpcTalkList_*` 或 `QuestList` 互换。

## `Map/` 下

### `MapListTable`

这是主地图注册表。

在任务／事件工作里最有用的字段：
- `m_MapLabel`、`m_MapText`、`m_MapPath`、`m_PathDataPath`
- `m_PlayerAttachEffectTriggers`
- `m_PlayerAttachEffectInvalidConditions`

这些 attach-effect 条件数组也是 `QuestTrigCondition.m_id` 行被消费的另一个地方。

### `MapReplaceList`

按条件替换地图状态／布局。

有用字段：
- `m_EnableTrigConditionID`
- `m_DisableTrigConditionID`

如果任务进度必须改变地图本身，而不仅仅是改变 NPC，就用它。

### `WorldMapList`, `WorldMapIllustList`, `WorldMapMaskList`

它们控制世界地图标记、插画与揭示遮罩。

这三张表都会使用条件数组，根据任务进度来显示／隐藏内容。如果你的自定义任务应当揭示地图插画、清除迷雾、或者增删世界地图标记，这里就是要编辑的地方。

### `WorldList`

更高层级的世界状态可见性门控。

重要字段：
- `m_TrigConditionIds`
- `m_InvalidConditionIds`

这也是一个世界地图或区域展示会与任务日志本身产生分歧的地方。

## `Object/` 下

### `MapObjectConditionsList`

按物件粒度控制地图物件的显示／隐藏或启用／禁用。

重要字段：
- `m_TrigConditionID`
- `m_NoneConditionID`

如果一个任务需要显示或压制物件、门（例如 Gate of Orsa）、交互物、告示牌、机关或其他场景摆件，那么这张表经常和 `NpcSetList_*` 一样重要。

## `ScenarioReplay/` 下

Scenario replay 数据大多是指回任务与事件的表现／UI 内容。

有用表：
- `ScenarioReplayChapterParam`: 章节卡片视觉与 `m_SilhouetteCond`
- `ScenarioReplayMainSectionParam`: 章节标题与 `m_EventId` 数组，以及 capture／check-entry 事件 ID
- `ScenarioReplaySubSectionParam`: 以 `m_QuestId` 与 `m_EventId` 为键的子节行
- `ScenarioReplayBondSectionParam`: 带有 `m_EventId` 的 bond／角色回顾条目
- `ScenarioReplayMainSectionText`: 章节文本与替换事件条件

若你只是想让一个任务在机制上能运作，通常不需要这些；但如果你想让自定义内容出现在 replay／recap UI 里，就需要它们。

## 常见错误

- `QuestTrigCondition.m_ConditionID` 与 battle 里的 `m_Conditions` 不是同一个命名空间。
- `QuestTaskList.m_ClearConditionID` 是直接条件类型，不是 `QuestTrigCondition.m_id`。
- `NpcTalkList.json` 与 `NpcTalkList_*` 看起来并不使用同一条件命名空间。
- `EventList.m_Name` 必须与真实事件资源名一致；资源元数据也必须据此修补。
- 很多表引用的不只是数值 ID，还有标签（`m_AppearLabel`、`m_DestNpcSetLabel`、路径 Actor 名）。
- UI 可见性、世界中的存在，以及实际推进，往往分散在不同表中；通常只修其中一张表是不够的。

## 额外的 DB / SDK 发现

- `StoryBookList.m_OpenCondition` 是一组 `StoryBookCondition.m_id` 值；它与 `QuestTrigCondition.m_id` 不是同一层。
- `StoryBookList.m_ApperQuestCondition` 大多表现为任务 ID 侧门控，而 `m_ApperCondition` 仍像是另一个外观侧条件命名空间，目前尚未完全解码。
- `MapReplaceList` 可以通过任务状态字段（`m_EnableQuestID`、`m_EnableQuestState`、`m_EnableQuestTaskID`）或通过触发条件数组来控制替换；在 OT0 中这两条路径都存在。
- `MainStoryRingList` 在 OT0 中非常小，而且当前导出还包含一个神秘的、始终为空的 `m` 数组，因此除非你明确在处理主线 ring UI，否则看起来可以安全忽略。
- `MapObjectConditionsList` 与 `NpcSetList_*` 经常需要一起更新：前者控制摆放物件，后者控制摆放的 NPC／物件集合（例如告示牌本体，以及位于同一位置、负责显示牌面文字的隐形 NPC）。

## 调试检查清单

- **任务没有出现在日志里**：检查 `QuestList`、它的文本 ID，以及显示／隐藏条件 ID。
- **NPC 或物件不出现**：检查 `NpcSetList_*`、`MapObjectConditionsList`、`LimitConditions` 与地图替换规则。
- **出现了错误的对话分支**：确认该地图使用的是 `NpcTalkList_*` 还是根 `NpcTalkList.json` 的条件命名空间。
- **步骤永远不开始**：审查 `QuestTaskList.m_OpenConditionID`、`m_StartConditionID` 与 `m_InvalidConditionID`。
- **步骤永远不完成**：不要只看 `QuestTrigCondition`，还要审查内联的 `m_ClearConditionID` 载荷（`m_CondQuest`、`m_EventID`、`m_ItemID`、`m_IntParam`、`m_StrParam`）。
- **事件永远不播放**：确认 `EventList.m_id`、`m_Name`、`m_Version`、事件资源元数据与调用方字段完全对得上。
- **队伍变化没有发生**：检查 `PlayerMemberSet` 以及 `QuestTaskList.m_AddMemberSetID` / `m_RemovePlayerIDs`；如果你增加了新的可玩角色，也要检查 `CharaPlayer`。
- **StoryBook 或 replay UI 缺失**：在 `StoryBook/*` 与 `ScenarioReplay/*` 中补齐桥接行；任务本体可以在没有这些的情况下运行，但 UI 不行。
