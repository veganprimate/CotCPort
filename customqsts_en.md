## **LANGUAGE:**  [English](customqsts_en.md) | [日本語](customqsts_en.md) | [中文](customqsts_en.md) | [한국어](customqsts_en.md)
Relevant for adding quests are the folders `Quest/`, `Event/`, `StoryBook/` and possibly `Npc/`, `ScenarioReplay/` as well as `Map/`.
## Under `Quest/`
### `QuestList`
What appears in the quest menu (title/subtitle/synopsis, chapter number, recommended level, sorting, when to show/hide). An entry is necessary here to add quest tasks.

- `m_QuestCategory`: high-level bucket shown in the quest UI (e.g., main story vs. side content vs. special/tutorial), the SDK specifies the following categories:
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
- `m_QuestSubCategory`: sub-bucket inside the category, primarily used by the main quest, see the following enum in the SDK
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
Quest Category 4 (SUB) has a special subcategory (20), appears to be related to the Wishvale main quest (Rekindling the Flame)
- `m_JoinPlayerID`: Specifies which character's head is displayed on the quest icon. This is likely unrelated to actual recruitment, as this appears to be done in the respective event files.
- `m_SortPriority`: explicit ordering key for the quest list UI
- `m_QuestListViewCondition`: Up to 5 condition IDs that control whether the entry is shown in the quest log, `m_QuestListHideCondition` works analogously. See `QuestTrigCondition` for viable IDs
- `m_AndTrigger`: Likely how the conditions under `m_QuestListViewCondition` are treated (at least one of the conditions listed has to be met vs. all of them have to be met) Note: TrigCondition 0 appears to always be met.
- `m_HideChapter`:  if true, don’t show `Chapter X` even if m_Chapter is set (used for side quests, etc.)
- `m_HideBossSilhouette` and `m_HideBossSilhouetteOnClear`: controls the "boss silhouette" display used for story chapters
- `m_Title`, `m_SubTitle`, `m_Synopsis`: Takes `GameTextQuest.m_id` as input.
- `m_ScenarioProgress`: an internal "progress marker"/"milestone codes". Unsure what they are used for exactly.
- `m_RecommNeedProg`: two numbers that look like "recommended/required progress range" for UI guidance or unlock logic. The patterned values (e.g. `[1,2]`, `[11,22]`, `[111,222]`) suggest these are not levels, but encoded story-progress thresholds(?)
- `m_RecommLv`: three recommended-level numbers, possibly recommended level per sub-part
- `m_InfluenceType`: which influence route/arc this quest belongs (Fame, Wealth, Power)
- `m_Influence`: how much influence is awarded on clear, as a 3-tuple (likely one per influence)
- `m_HideInfoFlag`: controls how much of the quest's information is shown in the UI (untested but likely: "???"-style presentation if true)
- `m_DisablePriorityDialog`: 
- `m_ShowToBeContinued`: Shows `To Be Continued` at the end of the quest (only used in Bestower of Wealth, Fame, and Power)
- `m_EnableReorder`: Probably "allow manual reordering/pinning" of this quest in the UI (or allow changing its display priority). Set to `false` for all quests in OT0, CotC has 7 quests for which this is set to true: `Gathering Smithing Materials`, `Always Going Missing`, `Kushiyaki Grill and Coral Sand`, `Gathering Even More Smithing Materials`, `Gathering Snow-Country Materials`, `Time to Fish`, `Gather Ingredients for Beast-Luring Medicine` 
- `m_JobType`: Always set to 0 in OT0, used in CotC for traveler stories.
- `m_ScenarioPlayerID`: Always set to 0 in OT0. Used in CotC in traveler stories, likely legacy/left-over key from CotC that serves a similar purpose to `m_JoinPlayerID` which doesn't exist in CotC
- `m_BeforeAccept`: Always set to 0 in OT0. Takes as input `GameTextQuest.m_id`. Pre-acceptance text in CotC, may be unused in some cases (many of the referenced localization strings start with `[TEMP]`)
- `m_RewardType`: tells what the reward is (leaves/item/etc.). Does not appear to be used in OT0, CotC uses them though (besides the ruby reward, all the other listed Reward Types appear as item rewards in OT0 but do not add the specified item to your inventory):
    - `100`: Ruby reward (considered a money reward in OT0)
    - `110`: Unit reward with option to specify number of stars (?)
    - `200`: Money reward
    - `210`: Item rewards
    - `230`: Key item reward (e.g. the Wisdom Nut)
    - `240`: Class Level Items rewards
    - `250`: Materials reward
    - `255`: Guidestone reward
    - `260`: Weapon rewards (e.g. Feintz' Beloved Blade)
    - `270`: Valuables rewards
    - `290`: Soul Weapons?
    - `310`: Awakening Stone reward
    - `340`: Souls reward (e.g. Void Dust)
    - `350`: Game Ticket reward (e.g. Fortune's Game Ticket)
    - `370`: Divine Beast Reward
    - `380`: Emberfruit reward (only used for an experience emberfruit)
    - `400`: Emberfruit reward (only used for the Stamina Emberfruit)
    - `420`: Spirit Ember reward
    - `430`: "Words of Leaf" reward (888 Mails quest)
    - `1001`: Esoteric Orb reward (only used in "A Power Beyond" in the "The Cait Dancer and the Forbidden Burner" quest line) (used as ordinary item reward in OT0)
    Reward types found under CotC's `AchievementList`:
    - `130`: Body Armor reward (e.g. "Linen Robe")
    - `410`: Retainer achievement rewards
    - `440`: Retainer achievement rewards
    - `460`: Arcana stone reward
    Reward types found in other OT0 DataBase files:
    - `1000`: Money reward
- `m_RewardItemId`: associates an item ID to the reward type 
- `m_RewardItemNum`: number of items rewarded, also rubies, leaves, etc.
- `m_RewardOption0`: extra per-reward parameter, always set to [0,0,0,0] in both OT0 and CotC

### `QuestTrigCondition`
A data-driven predicate system: each row is one reusable boolean test ("is X true right now?") that other tables reference by ID, i.e. `QuestTrigCondition.m_id` is the identifier of a specific condition referenced in other files (e.g. `QuestList`), and `QuestTrigCondition.m_ConditionID` the condition "opcode/type" that tells the engine how to evaluate the row and which parameter fields matter. Field-for-field:
- `m_QuestRollbackId`: A "rollback/reset group" identifier. Used when the game supports rewinding some content state (limited-time events, temporary scenarios, etc.). Not related to `QuestList.m_id`.
- `m_ConditionID`: condition type IDs, appears to **not** be the same as `m_Conditions` in other files (e.g. `TacticalList`). Probably determines how the keys below are read by the game
- `m_OwnerQuest`: "target quest id" likely used by quest-state checks (cleared, etc.)
- `m_OwnerQuestTask`: "target quest task id" likely used by task-state checks
- `m_OwnerUniqueChara`: likely a character availability check, uses the character's `UniqueID`
- `m_EventID`:
- `m_EnemyRaceID`:
- `m_ItemID`: checks whether an item has been obtained by the player (?)
- `m_EquipID`: Always set to 0 in OT0 but may check whether a party member is wearing some piece of equipment
- `m_JobID`: 
- `m_GenderID`:
- `m_InfluenceID`:
- `m_EventFlagVariableID`: see `EventFlagVariableList` under `Event/`
- `m_UtcDateTime`: Unused in OT0, used in CotC for time-gating quests (Condition 160) most-likely. Inputs are Unix timestamps. Untested whether functional in OT0
- `m_IntParam`: Array of integer parameters. `m_ConditionID` likely determines how the game interprets them.
- `m_StrParam`: Array of string parameters. Above is likely true again 

CotC exclusive keys:
- `m_MultipleConditions`
- `m_WorldMode`

### `QuestTaskList`

### `LimitConditions`

### `MainStoryRingList`

### `QuestAggregatedPoint`

### `QuestAggregatedTaskList`

## Under `Event/`

## Under `StoryBook/`
