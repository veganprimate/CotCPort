## **LANGUAGE:**  [English](customqsts_en.md) | [日本語](customqsts_ja.md) | [中文](customqsts_zh.md) | [한국어](customqsts_ko.md)

# 퀘스트와 이벤트를 추가하거나 수정하는 방법

이 문서는 퀘스트 진행, 이벤트 진행, StoryBook 항목, NPC 출현 조건, 그리고 관련 UI 상태를 추가·수정할 때 실제로 중요한 테이블을 정리한 실용적인 지도입니다.

저장소 경로와 문서 간 비교를 맞추기 위해 여기서는 `OT0` 약칭도 사용하지만, 한국어권에서는 공식 표기인 `옥토패스 트래블러 0` 또는 온라인에서 흔히 보이는 붙여쓴 형태 `옥토패스 트래블러0`가 더 자연스럽습니다.

퀘스트／이벤트 작업에 보통 관련되는 폴더는 `Quest/`, `Event/`, `StoryBook/`이며, 여기에 더해 `Npc/`, `ScenarioReplay/`, `Map/`, `Character/`, `Item/`, `PartyChat/`도 자주 관여합니다.

완전히 새로운 퀘스트를 추가할 때의 최소 동작 세트는 보통 다음과 같습니다.
- `GameTextQuest`의 텍스트(그리고 종종 `GameTextEvent` / `TalkText`, `GameTextNPC` / `TalkText`도)
- **한 개의** `QuestList` 행
- **하나 이상의** `QuestTaskList` 행
- **하나 이상의** `QuestTrigCondition` 행
- **하나 이상의** `EventList` 행과 실제 이벤트 자산
- 퀘스트를 플레이어에게 노출시키는 최소 **한 개의** 소비 테이블(`NpcSetList_*`, `NpcTalkList_*`, `StoryBook*`, `Map*` 등)

## 전체 작업 흐름

1. ID와 라벨을 확보합니다.
   - 기존 행과 충돌하지 않는 새 `m_id` 값을 고릅니다.
   - 이벤트의 경우 고유한 `EventList.m_Name`과 이에 대응하는 자산 파일명을 정합니다.
   - NPC／맵 앵커의 경우 `m_AppearLabel`, `m_DestNpcSetLabel`, 맵 경로 Actor 이름 같은 라벨을 재사용하거나 새로 고유하게 만듭니다.

2. 텍스트를 먼저 추가합니다.
   - 퀘스트 메뉴／제목 텍스트는 주로 `GameTextQuest`에 있습니다.
   - 이벤트 대사와 내레이션은 대신 이벤트／대화 텍스트 데이터베이스를 사용합니다.

3. 퀘스트 외곽을 만듭니다.
   - `QuestList`에 메뉴 엔트리를 추가합니다.
   - `QuestTaskList`에 시작／진행 행을 추가합니다.

4. 재사용 가능한 조건을 만듭니다.
   - 표시, 사용 가능, 완료, 해금, 맵 공개, NPC 출현 등에 필요한 `QuestTrigCondition` 행을 추가합니다.

5. 실제 이벤트를 추가합니다.
   - `EventList` 행을 추가합니다.
   - 대응하는 `Event/` 하위 폴더에 이벤트 자산을 만듭니다.
   - 그 이벤트 ID를 `QuestTaskList`, `NpcTalkList_*`, `StoryBookList`, 또는 해당 장면을 호출하는 다른 곳에 연결합니다.

6. 퀘스트를 월드 콘텐츠에 연결합니다.
   - NPC／오브젝트 출현은 보통 `NpcSetList_*` 또는 `MapObjectConditionsList`를 거칩니다.
   - 대화 분기는 보통 `NpcTalkList_*`를 거칩니다.
   - 월드맵, StoryBook, 파티 채팅, 마을 건설, replay UI 노출은 각각 별도 테이블을 사용합니다.

## `Quest/` 아래

### `QuestList`

퀘스트 메뉴에 무엇을 표시할지 결정합니다. 제목／부제／개요, 챕터 번호, 추천 레벨, 정렬, 표시／숨김 조건 등이 여기에 있습니다. 퀘스트를 퀘스트 UI에 존재시키려면 여기 행이 반드시 필요합니다.

- `m_QuestCategory`: 퀘스트 UI에 표시되는 상위 분류입니다. SDK는 다음을 정의합니다.
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
- `m_QuestSubCategory`: 카테고리 내부의 하위 분류로, 주로 메인 퀘스트 성격의 콘텐츠에서 사용됩니다. SDK는 다음을 정의합니다.
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
- 퀘스트 카테고리 `4`(`SUB`)에는 특수한 하위 카테고리 `20`이 있으며, Wishvale 메인 퀘스트 계열 콘텐츠에서 쓰이는 것으로 보입니다.
- `m_JoinPlayerID`: 퀘스트 아이콘에 어느 캐릭터의 초상／머리 아이콘을 표시할지 제어합니다. 실제 영입 로직 자체는 아닌 듯합니다.
- `m_SortPriority`: 명시적인 UI 정렬 키입니다.
- `m_QuestListViewCondition`: 퀘스트가 퀘스트 로그에 표시될지를 결정하는 최대 5개의 `QuestTrigCondition.m_id` 참조입니다.
- `m_QuestListHideCondition`: 같은 퀘스트 항목에 대한 반대쪽 숨김 조건입니다.
- `m_AndTrigger`: `m_QuestListViewCondition`을 OR식으로 처리할지 AND식으로 처리할지 전환하는 것으로 보입니다. `QuestTrigCondition#0`은 항상 참／아무 일도 하지 않는 슬롯처럼 동작합니다.
- `m_HideChapter`: true이면 `m_Chapter`가 설정되어 있어도 `Chapter X`를 표시하지 않습니다.
- `m_HideBossSilhouette` / `m_HideBossSilhouetteOnClear`: 스토리 스타일 퀘스트의 보스 실루엣 UI 토글입니다.
- `m_Title`, `m_SubTitle`, `m_Synopsis`: `GameTextQuest.m_id`를 참조합니다.
- `m_ScenarioProgress`: 내부 진행 마커이며 아직 완전히 해독되지 않았습니다.
- `m_RecommNeedProg`: 일정한 패턴의 진행 범위 값으로, 일반 레벨이라기보다 스토리 진행 가이드일 가능성이 큽니다.
- `m_RecommLv`: 추천 레벨 3원 배열입니다.
- `m_InfluenceType`: 퀘스트가 어느 루트／아크에 속하는지.
- `m_Influence`: 영향력 보상 튜플.
- `m_HideInfoFlag`: 모호한／숨김 UI 표시를 전환하는 것으로 보입니다.
- `m_DisablePriorityDialog`: 아직 불명확합니다.
- `m_ShowToBeContinued`: `To Be Continued` 엔드 태그를 표시합니다.
- `m_EnableReorder`: OT0에서는 사용되지 않지만, 『옥토패스 트래블러: 대륙의 패자』에서는 일부 사이드 콘텐츠 스타일 퀘스트에 사용됩니다.
- `m_JobType`, `m_ScenarioPlayerID`, `m_BeforeAccept`: OT0에서는 거의 미사용이고, 『옥토패스 트래블러: 대륙의 패자』에서는 더 적극적으로 쓰이는 것으로 보입니다.
- `m_RewardType`, `m_RewardItemId`, `m_RewardItemNum`, `m_RewardOption0`: 보상 선언 블록입니다. OT0에서는 일부만 사용하지만, UI 일관성을 위해서도 올바르게 채워 둘 가치가 있습니다.

원본 export에서 놓치기 쉬운 점:
- `m_HideQuestList`는 조건부 표시와 별개인 강제 숨김 플래그입니다.
- `m_QuestListHideCondition`는 OT0 export DB에서는 단일 정수이며, `m_QuestListViewCondition`처럼 5슬롯 배열이 아닙니다.
- `m_Chapter`는 `m_HideChapter`가 false일 때 실제로 표시되는 챕터 번호입니다.
- `m_RecommLv`, `m_RecommNeedProg`, `m_Influence`는 고정 길이 배열이며, 보상 관련 필드는 원본 export에서 4슬롯 배열입니다. JSON을 수동으로 편집할 때 이 형태를 유지해야 합니다.

### `QuestTrigCondition`

이것은 핵심적인 데이터 기반 술어 시스템입니다.

`QuestTrigCondition.m_id`는 다른 테이블에서 참조하는 재사용 가능한 조건 행 ID입니다. `QuestTrigCondition.m_ConditionID`는 엔진에게 이 행을 어떻게 해석할지를 알려 주는 조건 타입／오퍼코드입니다.

필드별 설명:
- `m_QuestRollbackId`: 롤백／리셋 그룹입니다. 퀘스트 ID가 아닙니다.
- `m_ConditionID`: 퀘스트／글로벌 트리거 조건 타입입니다. battle／AI／skill 쪽 `m_Conditions`와는 다른 네임스페이스입니다.
- `m_OwnerQuest`: 퀘스트 상태 체크용 대상 `QuestList.m_id`입니다.
- `m_OwnerQuestTask`: 태스크 상태 체크용 대상 `QuestTaskList.m_id`입니다.
- `m_OwnerUniqueChara`: 보통 `CharaPlayer.m_UniqueID`를 가리킵니다.
- `m_EventID`: 흔히 `Event/EventList.m_id` 참조입니다.
- `m_EnemyID`: 적 대상.
- `m_EnemyRaceID`: 적 종족 대상.
- `m_ItemID`: 아이템 소지／사용 대상.
- `m_EquipID`: 장비 관련으로 보이지만 OT0 행에서는 사용되지 않습니다.
- `m_JobID`, `m_GenderID`, `m_InfluenceID`: 강하게 타입화된 enum 기반 체크입니다.
- `m_EventFlagVariableID`: `EventFlagVariableList`를 가리킵니다.
- `m_UtcDateTime`: 『옥토패스 트래블러: 대륙의 패자』의 시간 제한 콘텐츠에 쓰이는 것으로, Unix 타임스탬프 체크로 보입니다.
- `m_IntParam`: `m_ConditionID`에 따라 해석되는 추가 정수 페이로드입니다.
- `m_StrParam`: `m_ConditionID`에 따라 해석되는 추가 문자열 페이로드입니다.

OT0 데이터에서 강하게 시사되는 파라미터 힌트:
- `m_ConditionID = 53 (eCOND_MAP_REACH)`는 `m_IntParam` 값이 `Map/MapListTable.m_id`와 매우 깨끗하게 맞아떨어집니다.
- `m_ConditionID = 162`는 `m_IntParam` 값이 `CharaPlayer.m_UniqueID`와 매우 잘 맞습니다.
- `m_ConditionID = 181 (eCOND_ENEMY_GROUP)`는 `m_StrParam`을 `EnemyGroups.m_Label` 문자열로 사용합니다.
- `m_ConditionID = 202 (eCOND_HAS_VILLAGE_BUILDING)`는 `m_IntParam`을 `VillageBuildingData.m_id`로 사용합니다.
- `m_ConditionID = 13/14`는 `m_StrParam`에 `EventList.m_Name` 문자열을 넣는 경우가 많습니다.

`QuestTrigCondition.m_id`를 직접 소비하는 쪽은 `QuestList`보다 훨씬 넓습니다. OT0에서는 다음에도 사용됩니다.
- `QuestTaskList.m_StartConditionID` / `m_OpenConditionID`
- `NpcSetList_*` 및 `NpcTalkList_*`
- `PartyChatList`
- `MapObjectConditionsList`
- `WorldMapList`, `WorldMapIllustList`, `WorldMapMaskList`
- `StoryBookCondition`
- `CharaNpcList`, `CharaJoin`, `MainPlayerIllust`, `MainPlayerRingIllust`
- `NoteList`, `MonsterArenaList`, `VillageBuildingData`, `VillageBuildingLimit`, `VillageTownLevelCondition`

중요한 구분:
- `QuestList`, `NpcSetList_*`, `NpcTalkList_*`, `StoryBookCondition`, `WorldMap*` 같은 테이블은 `QuestTrigCondition.m_id`라는 “행 ID 층”을 소비합니다.
- 반면 `QuestTaskList.m_ClearConditionID`와 `StoryBookList.m_ReadCondition`은 직접 `EQUEST_CONDITION` 조건 타입 네임스페이스 자체를 소비합니다.

이 두 층을 혼동하는 것은, JSON상으로는 멀쩡해 보이는데 게임 안에서는 절대로 발동하지 않는 퀘스트를 만드는 가장 쉬운 방법 중 하나입니다.

OT0에서 자주 나오는 조건 타입에 대한 실용적인 마음가짐:
- `1`: 특정 퀘스트 태스크가 active
- `2`: 퀘스트가 ready
- `3`: 퀘스트가 active / in progress
- `4`: 퀘스트가 clear
- `12`: 이벤트가 clear
- `53`: 맵 도달／현재 위치 스타일 체크
- `123`: 이벤트 플래그／변수 체크

SDK／데이터에서 보이는 『옥토패스 트래블러: 대륙의 패자』 전용 키:
- `m_MultipleConditions`
- `m_WorldMode`

### `QuestTaskList`

이것이 실제 퀘스트 진행의 등뼈입니다. `QuestList`가 퀘스트가 존재함을 말한다면, `QuestTaskList`는 플레이어가 지금 무엇을 해야 하고 무엇이 퀘스트를 앞으로 밀어주는지를 말합니다.

SDK는 다음을 공개합니다.
```hpp
enum class EQUEST_TASK_TYPE : uint8
{
	NONE                                     = 0,
	START                                    = 1,
	PROGRESS                                 = 2,
	EQUEST_TASK_MAX                          = 3,
};
```

유용한 필드:
- `m_id`: 태스크 행 ID.
- `m_OwnerQuest`: 소유하는 `QuestList.m_id`.
- `m_OwnerTask`: 체인형 태스크 구조에서 부모 태스크／그룹 참조.
- `m_TaskType`: OT0에서는 `START` 대 `PROGRESS`.
- `m_TaskNo`: 퀘스트 내부에 표시되는 순서／인덱스.
- `m_HideTask`: 이 태스크를 UI에서 숨깁니다.
- `m_Purpose`, `m_Synopsis`, `m_NextDestination`: 텍스트／UI 보조 필드.
- `m_DestNpcSetLabel`: 목적지 가이드에 사용되는 목표 NPC 세트／오브젝트의 라벨.
- `m_DestPathName`: 내비게이션／UI 가이드에 사용되는 path actor 이름.
- `m_DestTownInteriorAppearPlacePointId`: 실내 목적지 앵커.
- `m_ClearedQuest`: 이 행에 대해 이미 완료／관련 완료로 간주되는 퀘스트 배열.
- `m_OpenConditionID`: 태스크를 표시／오픈시키는 `QuestTrigCondition.m_id` 행.
- `m_StartConditionID`: 태스크를 시작／활성화하는 `QuestTrigCondition.m_id` 행.
- `m_InvalidConditionID`: 비활성화 쪽 조건 배열.
- `m_ReorderMax`, `m_ReorderInterval`: 태스크 정렬 제어이며, 아직 완전히 해독되지는 않았습니다.
- `m_SubClearConditionID`: 추가적인 행 ID 기반 완료 보조 항목.
- `m_ClearConditionID`: 직접 조건 타입／오퍼코드이며, `QuestTrigCondition.m_id`가 아닙니다. 매우 중요합니다.
- `m_CondQuest`, `m_CondQuestTask`, `m_OwnerUniqueChara`, `m_EventID`, `m_EnemyID`, `m_EnemyRaceID`, `m_ItemID`, `m_IntParam`, `m_StrParam`: `m_ClearConditionID`를 위한 인라인 페이로드입니다.
- `m_StartEvent`: 태스크가 시작될 때 실행되는 이벤트.
- `m_ShowStartUI`: 태스크 시작 UI를 표시할지 여부.
- `m_ClearEvent`: 태스크가 완료될 때 실행되는 이벤트.
- `m_ShowClearUI`: 태스크 완료 UI를 표시할지 여부.
- `m_TaskItemId`, `m_TaskItemNum`: 수집／전달형 태스크 페이로드.
- `m_AddMemberSetID`: `PlayerMemberSet`을 가리키며 파티 사용 가능 상태를 바꾸는 것으로 보입니다.
- `m_RemovePlayerIDs`: 사용 가능한 파티 집합에서 특정 플레이어를 제거합니다.

실전에서는 퀘스트가 “존재는 하지만 전혀 진행되지 않는” 것처럼 느껴진다면, 가장 먼저 감사해야 할 두 곳이 바로 `QuestTaskList`와 `QuestTrigCondition`입니다.

원본 export상의 주의점:
- `m_OpenConditionID`, `m_StartConditionID`, `m_InvalidConditionID`, `m_SubClearConditionID`, `m_TaskItemId`, `m_TaskItemNum`, `m_RemovePlayerIDs`는 모두 단일 값이 아니라 배열입니다.
- OT0에서는 0이 아닌 `m_ClearConditionID`의 대부분이 `12 (eCOND_EVENT_CLEAR)`입니다. 즉 많은 태스크가 재사용형 `QuestTrigCondition` 행이 아니라 “이 이벤트가 끝났는가?”로 완료됩니다.

### `LimitConditions`

이 테이블은 퀘스트 로그 테이블이라기보다 맵／지역／시스템 규칙 테이블처럼 보입니다.

관찰되는 필드:
- `m_LimitType`: 슬롯별 규칙 타입 배열.
- `m_worldMapID`, `m_mapID`: 월드／맵 대상.
- `m_BootQuestStaet`, `m_BootQuestID`, `m_BootQuestTaskId`: 활성화 쪽 퀘스트 상태 연동.
- `m_DisableQuestState`, `m_DisableQuestID`, `m_DisableQuestTaskId`: 비활성화 쪽 퀘스트 상태 연동.
- `m_TrigConditionID`, `m_NoneConditionID`: 추가적인 `QuestTrigCondition.m_id` 게이트.
- `m_BgmID`, 미니맵 텍스처 필드, `m_MinimapLCLabel`: 추가적인 연출／시스템 페이로드.

여기서 유용한 SDK enum:
- `EQUEST_STATE`: `0 NONE`, `1 READY`, `2 ACTIVE`, `3 CLEAR`
- `ELIMIT_CONDITIONS_TYPE`: OT0에서는 특히 `8 eFIX_FIELD_BGM`, `2 eFASTTRAVEL_BAN`, `29 eMINIMAP_FAST_TRAVEL_BAN`, `13 eMINIMAP_HIDE_ICON`, `6 eMINIMAP_EXTRA_JOINMAP`, `97 eMINIMAP_FASTTRAVEL_EXCLUDE_FROM_SEARCH`가 자주 쓰입니다

퀘스트나 이벤트로 퀘스트 로그 상태뿐 아니라 환경／맵 레벨 동작까지 바꾸고 싶을 때 이것을 사용합니다.

전체 SDK 항목:
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

매우 작은 테이블입니다. SDK 레이아웃이 노출하는 것은 다음뿐입니다.
- `m_PrevTermRingTex`
- `m_PrevTermQuestId`

이것은 진행 로직이라기보다 스토리 링／이전 장 표시 데이터처럼 보입니다. 메인 스토리 링 UI를 건드리지 않는다면 보통 그대로 두어도 됩니다.

### `QuestAggregatedPoint`

재사용 가능한 목적지／마커 앵커 테이블처럼 보입니다.

필드:
- `m_id`
- `m_LevelId`
- `m_AppearLabel`

여러 퀘스트 태스크가 각자 개별 목적지를 갖는 대신 같은 월드 마커／앵커에 합쳐져야 할 때 사용합니다.

### `QuestAggregatedTaskList`

개별 `QuestTaskList` 행을 집계 포인트에 연결합니다.

필드:
- `m_QuestTaskId`
- `m_AggregatedPointId`
- `m_Priority`

즉, `QuestAggregatedPoint`가 마커 앵커를 정의하고, `QuestAggregatedTaskList`가 태스크를 그쪽에 할당합니다.

## `Event/` 아래

### `EventList`

`EventList`는 재생 가능／호출 가능한 이벤트 자산의 레지스트리입니다.

어떤 이벤트 자산이 존재하는지, 어느 맵에 속하는지, 이벤트 종료 시 어떤 복귀／위치 메타데이터를 적용할지를 엔진에 알려 줍니다.

중요 필드:
- `m_id`: 다른 곳에서 사용하는 이벤트 ID (`QuestTaskList`, `NpcTalkList_*`, `StoryBookList`, `ScenarioReplay*`, 이벤트 명령 등).
- `m_Version`: 폴더／카테고리식 그룹화입니다. 실제로는 실제 자산 경로 구성을 이루는 일부이기도 합니다.
- `m_Name`: 이벤트 자산 이름. `m_id` 다음으로 가장 중요한 필드입니다.
- `m_MapID`: 소속／주 맵.
- `m_ReturnMapID`, `m_ReturnPathActorName`, `m_ReturnPos`, `m_ReturnDir`: 이벤트 종료 후 플레이어가 돌아갈 위치.
- `m_Kind`: 이벤트 카테고리. SDK는 `EEVENT_KIND`를 노출하며, boss／chara／sub 등의 그룹을 가집니다.
- `m_PlayBGM`, `m_SoundCueID`: 오디오 설정.
- `m_Skip`: 스킵 동작.
- `m_BanAutoPlay`, `m_MapLoadWait`, `m_Seamless`, `m_StartEnvVolumeZero`: 로딩／재생 동작 플래그.
- `m_IsVillageEvent`, `m_IsWarpEvent`, `m_IsNotHideMapUI`, `m_BanChangePlayMode`, `m_BanEncountMgrBgmChange`, `m_BanLoadBattleLevel`: 특수 런타임 플래그.

관련 `EEVENT_KIND` 값에는 `1 BOSS_EVENT`, `2 CHARA_EVENT`, `3 SUB_EVENT`, `16 TOWN`, `18 VILLAGE`, `19 PARTYCHAT`, `27 JOIN_EVENT`, `28 ARRIVAL_EVENT` 등이 포함됩니다.

실전 규칙: 새 이벤트 자산을 추가했는데 대응하는 `EventList` 행을 추가하지 않으면, 거의 아무 것도 그 이벤트를 정상적으로 호출하지 못합니다.

모든 이벤트 종류:
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

실제 이벤트 명령은 `Event/V*/.../*.uasset` 아래 이벤트 자산에 들어 있습니다.

전형적인 작업 흐름:
- 비슷한 기존 이벤트 JSON을 복제한다
- 새 `EventList.m_Name`과 일치하는 새 파일명을 부여하고, 생성된 자산의 메타데이터도 그 파일명과 일치하도록 맞춘다(이를 위해 [UAssetGUI의 최신 experimental 릴리스](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest)를 사용)
- 명령 행 내부의 ID／텍스트／대상을 업데이트한다
- `QuestTaskList.m_StartEvent`, `QuestTaskList.m_ClearEvent`, `NpcTalkList_* .m_EventID`, 또는 다른 호출자를 새 `EventList.m_id`로 연결한다

명령별 opcode 주석은 다음을 참조:
- [Event Opcode Crossref Atlas](https://github.com/veganprimate/CotCPort/blob/main/Event/Event_Opcode_Crossref_Atlas.md)

### `EventFlagVariableList`

이것은 이벤트 로직과 `QuestTrigCondition.m_EventFlagVariableID`가 사용하는 수치 이벤트 플래그／이벤트 변수 테이블입니다.

관찰되는 필드:
- `m_id`: 플래그／변수 ID.
- `m_FlagType`: 플래그 타입／카테고리.
- `m_VolatilityValidMap`: 휘발성 플래그의 맵 스코프.
- `m_UpperLimit`, `m_LowerLimit`: 수치 범위／클램프 메타데이터.

퀘스트／이벤트가 단순한 퀘스트／태스크 완료 상태만으로는 표현되지 않는 커스텀 상태를 기억해야 할 때 이것을 사용합니다.

### `EventFlagObject`

`EventFlagObject/*.uasset`는 다른, 보다 기호적인 조건 시스템을 사용합니다. 행은 `QuestTrigCondition.m_id` 정수가 아니라 `conditions = ["..."]` 같은 문자열 리스트를 가집니다.

이를 `QuestTrigCondition`과 같은 네임스페이스로 보지 말고, 인접한 맵／오브젝트 조건 레이어로 취급하십시오.

## `StoryBook/` 아래

### `StoryBookCondition`

이 테이블은 StoryBook／Journal 해금 로직을 퀘스트 트리거 시스템에 연결하는 다리 역할을 합니다.

필드:
- `m_OpenCondition`: 보통 `QuestTrigCondition.m_id`
- `m_ConditionText`, `m_ConditionExplain`: UI 텍스트
- `m_ReccomendLevel`: 추천값
- `m_QuestID`: 퀘스트 연계
- `m_MapID`: 맵 연계

새 Journal 항목을 퀘스트／이벤트 조건이 충족될 때 해금하고 싶다면, 가장 깔끔한 다리가 이것입니다.

### `StoryBookList`

이것은 실제 StoryBook 항목 메타데이터 테이블입니다.

유용한 필드:
- title／tab／category／icon 계열 필드
- `m_ApperStoryBookCondition`, `m_ApperQuestCondition`, `m_ApperCondition`
- `m_OpenCondition`
- `m_ReadCondition`
- reward 계열 필드
- `m_EventID`
- battle 메타데이터 필드

중요한 뉘앙스:
- `StoryBookCondition.m_OpenCondition`은 `QuestTrigCondition.m_id` 간접층을 사용합니다.
- `StoryBookList.m_ReadCondition`은 대신 퀘스트／글로벌 트리거 조건 타입 네임스페이스를 직접 사용합니다.

## `Character/` 아래

### `PlayerMemberSet`

이것이 `QuestTaskList.m_AddMemberSetID` 뒤에 있는 테이블입니다.

필드:
- `m_ValidCondition`: 해당 member-set 행을 활성화하는 `QuestTrigCondition.m_id`
- `m_PlayerIDs`: 최대 8개의 플레이어 ID／파티 슬롯. 다만 첫 번째는 메인 캐릭터 전용이며, Zero가 아닌 Player ID를 넣으면 충돌할 수 있습니다

퀘스트 이벤트가 일시적으로 사용 가능한 파티 구성을 강제／추가／교체해야 할 때 사용합니다.

### `CharaJoin`

캐릭터 가입／해금 게이팅 테이블입니다.

중요 필드:
- `m_ConditionID`: 해당 join 행을 게이트하는 `QuestTrigCondition.m_id`

정확한 사용 방식은 아직 불분명합니다.

### `CharaNpcList`

맵 로컬 대화 트리 자체와는 별개의, 상위 수준 NPC 상호작용 게이트입니다.

조건 아틀라스에서 참조되는 중요 필드:
- `m_Hear_CheckCondition`
- `m_Invite_CheckCondition`
- `m_ReleaseConditionID`
- `m_VillageInviteReleaseConditionId`

퀘스트가 단순히 NPC가 무슨 대사를 하는가만이 아니라, 들을 수 있는가／초대할 수 있는가／해제할 수 있는가 같은 상호작용 자체를 바꿀 때 특히 중요합니다.

## `Npc/` 아래

### `NpcSetList_*`

이 파일들은 대체로 NPC／오브젝트가 월드에 실제로 나타날지 자체를 제어합니다.

중요 필드:
- owner／object／talk 식별자
- `m_MapID`
- `m_AppearLabel`
- `m_TalkID`
- quest boot／disable 계열 필드
- `m_TrigConditionID`
- `m_NoneConditionID`

전형적인 용도: 퀘스트 의뢰인을 어떤 조건 이후에만 등장시키고, 다른 조건 이후에는 사라지게 하며, 진행 후에는 다른 대화 트리로 바꾸기.

### `NpcTalkList_*`

이 파일들은 대체로 맵 전용 NPC 상호작용에서 실제 대화 노드／분기를 제어합니다.

중요 필드:
- `m_TalkText`
- `m_EventID`
- `m_QuestID`
- `m_TrigConditionID`
- `m_NoneConditionID`
- 선택지 텍스트／영향력 값／다음 대화 번호

전형적인 용도: 퀘스트 진행으로 다른 대화 분기를 게이트하고, 필요하다면 특정 대화 노드에서 퀘스트 이벤트를 시작하기.

### 루트 `NpcTalkList`

이것은 전체 `QuestTrigCondition.m_id` 행이 아니라 별도의 로컬 소형 ID 조건 네임스페이스를 쓰는 것으로 보입니다. 이 `m_TrigConditionID`／`m_NoneConditionID` 값을 `NpcTalkList_*`나 `QuestList`와 서로 바꿔 써도 된다고 가정하면 안 됩니다.

## `Map/` 아래

### `MapListTable`

이것은 메인 맵 레지스트리입니다.

퀘스트／이벤트 작업에서 가장 유용한 필드는 다음과 같습니다.
- `m_MapLabel`, `m_MapText`, `m_MapPath`, `m_PathDataPath`
- `m_PlayerAttachEffectTriggers`
- `m_PlayerAttachEffectInvalidConditions`

이 attach-effect 조건 배열 역시 `QuestTrigCondition.m_id` 행이 소비되는 또 하나의 위치입니다.

### `MapReplaceList`

조건부로 맵 상태／레이아웃을 교체합니다.

유용한 필드:
- `m_EnableTrigConditionID`
- `m_DisableTrigConditionID`

퀘스트 진행이 NPC만이 아니라 맵 자체도 바꿔야 할 때 사용합니다.

### `WorldMapList`, `WorldMapIllustList`, `WorldMapMaskList`

이들은 월드맵 마커, 일러스트, 공개 마스크를 제어합니다.

셋 다 조건 배열을 사용해 퀘스트 진행에 따라 콘텐츠를 표시／숨깁니다. 커스텀 퀘스트가 맵 아트를 공개하거나, 안개를 걷거나, 월드맵 마커를 추가／제거해야 한다면 여기를 수정해야 합니다.

### `WorldList`

보다 상위 수준의 월드 상태 가시성 게이트입니다.

중요 필드:
- `m_TrigConditionIds`
- `m_InvalidConditionIds`

이 역시 광역 월드맵이나 지역 표시가 퀘스트 로그 자체와 어긋날 수 있는 지점입니다.

## `Object/` 아래

### `MapObjectConditionsList`

맵 오브젝트별 표시／숨김 또는 활성／비활성 게이팅입니다.

중요 필드:
- `m_TrigConditionID`
- `m_NoneConditionID`

퀘스트가 오브젝트, 문(예: Gate of Orsa), 상호작용 오브젝트, 표지판, 기믹, 기타 배치물을 드러내거나 억제해야 한다면, 이 테이블은 `NpcSetList_*`만큼 중요해지는 경우가 많습니다.

## `ScenarioReplay/` 아래

Scenario replay 데이터는 대부분 퀘스트와 이벤트를 다시 가리키는 연출／UI 콘텐츠입니다.

유용한 테이블:
- `ScenarioReplayChapterParam`: 챕터 카드 비주얼과 `m_SilhouetteCond`
- `ScenarioReplayMainSectionParam`: 섹션 제목과 `m_EventId` 배열, 그리고 capture／check-entry 이벤트 ID
- `ScenarioReplaySubSectionParam`: `m_QuestId`와 `m_EventId`로 키되는 subsection 행
- `ScenarioReplayBondSectionParam`: `m_EventId`를 가진 bond／character recap 항목
- `ScenarioReplayMainSectionText`: 섹션 텍스트와 대체 이벤트 조건

기계적으로 동작하는 퀘스트만 만들고 싶다면 대개 이것들은 필요 없지만, 커스텀 콘텐츠를 replay／recap UI에 나타나게 하려면 필요합니다.

## 흔한 오류

- `QuestTrigCondition.m_ConditionID`는 battle 쪽 `m_Conditions`와 같은 네임스페이스가 아닙니다.
- `QuestTaskList.m_ClearConditionID`는 `QuestTrigCondition.m_id`가 아니라 직접 조건 타입입니다.
- `NpcTalkList.json`과 `NpcTalkList_*`는 같은 조건 네임스페이스를 쓰지 않는 것으로 보입니다.
- `EventList.m_Name`은 실제 이벤트 자산 이름과 일치해야 하며, 자산 메타데이터도 그에 맞게 패치해야 합니다.
- 많은 테이블은 숫자 ID만이 아니라 라벨(`m_AppearLabel`, `m_DestNpcSetLabel`, path actor 이름)도 참조합니다.
- UI 가시성, 월드 내 존재, 실제 진행은 종종 서로 다른 테이블에 나뉘어 있습니다. 보통 그중 하나만 고쳐서는 충분하지 않습니다.

## 추가 DB / SDK 발견

- `StoryBookList.m_OpenCondition`은 `StoryBookCondition.m_id` 값 배열이며, `QuestTrigCondition.m_id`와 같은 층이 아닙니다.
- `StoryBookList.m_ApperQuestCondition`은 주로 quest-ID 쪽 게이트처럼 동작하지만, `m_ApperCondition`은 여전히 별도의 appearance 쪽 조건 네임스페이스처럼 보이며 아직 완전히 해독되지 않았습니다.
- `MapReplaceList`는 퀘스트 상태 필드(`m_EnableQuestID`, `m_EnableQuestState`, `m_EnableQuestTaskID`)로도, 트리거 조건 배열로도 교체를 게이트할 수 있습니다. OT0에는 두 경로가 모두 존재합니다.
- `MainStoryRingList`는 OT0에서 매우 작고, 현재 export에도 정체불명의 항상 비어 있는 `m` 배열이 포함되어 있어, 메인 스토리 링 UI를 명시적으로 건드리지 않는다면 무시해도 안전해 보입니다.
- `MapObjectConditionsList`와 `NpcSetList_*`는 자주 함께 업데이트해야 합니다. 하나는 배치 오브젝트를 제어하고, 다른 하나는 배치 NPC／오브젝트 세트(예: 표지판 자체와, 같은 위치에 배치된 보이지 않는 NPC가 담당하는 표지판 텍스트)를 제어하기 때문입니다.

## 디버깅 체크리스트

- **퀘스트가 로그에 나타나지 않음**: `QuestList`, 그 텍스트 ID, view／hide 조건 ID를 확인합니다.
- **NPC나 오브젝트가 나타나지 않음**: `NpcSetList_*`, `MapObjectConditionsList`, `LimitConditions`, 맵 교체 규칙을 확인합니다.
- **잘못된 대화 분기가 나옴**: 그 맵이 `NpcTalkList_*`를 쓰는지, 루트 `NpcTalkList.json` 조건 네임스페이스를 쓰는지 확인합니다.
- **태스크가 시작되지 않음**: `QuestTaskList.m_OpenConditionID`, `m_StartConditionID`, `m_InvalidConditionID`를 감사합니다.
- **태스크가 완료되지 않음**: `QuestTrigCondition`만이 아니라 인라인 `m_ClearConditionID` 페이로드(`m_CondQuest`, `m_EventID`, `m_ItemID`, `m_IntParam`, `m_StrParam`)도 감사합니다.
- **이벤트가 재생되지 않음**: `EventList.m_id`, `m_Name`, `m_Version`, 이벤트 자산 메타데이터, 호출자 필드가 모두 맞는지 확인합니다.
- **파티 변경이 일어나지 않음**: `PlayerMemberSet`과 `QuestTaskList.m_AddMemberSetID` / `m_RemovePlayerIDs`를 확인하고, 새 플레이어블을 추가했다면 `CharaPlayer`도 확인합니다.
- **StoryBook 또는 replay UI가 없음**: `StoryBook/*`와 `ScenarioReplay/*`에 연결용 행을 추가합니다. 퀘스트 자체는 이것들 없이도 작동할 수 있지만 UI는 작동하지 않습니다.
