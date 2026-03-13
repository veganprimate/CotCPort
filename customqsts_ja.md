## **LANGUAGE:**  [English](customqsts_en.md) | [日本語](customqsts_ja.md) | [中文](customqsts_zh.md) | [한국어](customqsts_ko.md)

# クエストとイベントの追加・変更方法

このページは、クエスト進行、イベント進行、StoryBook 項目、NPC の出現条件、関連 UI 状態を追加・変更したいときに重要になるテーブルを整理した、実用向けの地図です。

リポジトリ内のパスや比較の都合上、このページでは `OT0` という略記も使いますが、日本語圏では正式表記の `オクトパストラベラー0` や、オンライン上でよく使われる `オクトラ0` の方が自然です。

クエスト／イベント作業で関わるフォルダは、通常 `Quest/`、`Event/`、`StoryBook/` であり、さらに `Npc/`、`ScenarioReplay/`、`Map/`、`Character/`、`Item/`、`PartyChat/` も頻繁に関わります。

完全に新しいクエストを追加する場合、最低限動く構成は通常次のとおりです。
- `GameTextQuest` のテキスト（さらに `GameTextEvent` / `TalkText`、`GameTextNPC` / `TalkText` も必要になることが多い）
- **1 行の** `QuestList`
- **1 行以上の** `QuestTaskList`
- **1 行以上の** `QuestTrigCondition`
- **1 行以上の** `EventList` と、実際のイベントアセット
- プレイヤーにクエストを露出させるための、少なくとも 1 つの消費側テーブル（`NpcSetList_*`、`NpcTalkList_*`、`StoryBook*`、`Map*` など）

## 大まかな作業手順

1. ID とラベルを確保する。
   - 既存行と衝突しない新しい `m_id` を選びます。
   - イベントについては、一意な `EventList.m_Name` と、それに一致するアセットファイル名を選びます。
   - NPC／マップのアンカーでは、`m_AppearLabel`、`m_DestNpcSetLabel`、マップパス上の Actor 名などのラベルを再利用するか、新規に一意なものを作ります。

2. 先にテキストを追加する。
   - クエストメニュー／タイトル系テキストは主に `GameTextQuest` にあります。
   - イベントの台詞やナレーションは、代わりにイベント／会話テキスト DB を使います。

3. クエストの外枠を作る。
   - `QuestList` にメニュー行を追加します。
   - `QuestTaskList` に開始／進行行を追加します。

4. 再利用可能な条件を作る。
   - 表示、出現、達成、解放、マップ開示、NPC 出現などに使う `QuestTrigCondition` 行を追加します。

5. 実際のイベントを追加する。
   - `EventList` 行を追加します。
   - 対応する `Event/` サブフォルダ下にイベントアセットを作成します。
   - そのイベント ID を `QuestTaskList`、`NpcTalkList_*`、`StoryBookList` など、シーンを起動する側に配線します。

6. クエストをワールド内容に接続する。
   - NPC／オブジェクトの出現は通常 `NpcSetList_*` または `MapObjectConditionsList` を通ります。
   - 会話分岐は通常 `NpcTalkList_*` を通ります。
   - ワールドマップ、StoryBook、パーティチャット、村発展、リプレイ UI への露出は、それぞれ専用テーブルを使います。

## `Quest/` の下

### `QuestList`

クエストメニューに何を表示するかを決めます。タイトル／サブタイトル／概要、章番号、推奨レベル、並び順、表示／非表示条件などです。クエストをクエスト UI 上に存在させたいなら、ここへの行追加は必須です。

- `m_QuestCategory`: クエスト UI 上で表示される大分類。SDK は次を定義しています。
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
- `m_QuestSubCategory`: 分類内のサブ分類。主にメインクエスト系コンテンツで使われます。SDK は次を定義しています。
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
- クエストカテゴリ `4`（`SUB`）には特殊なサブカテゴリ `20` があり、Wishvale のメインクエスト系コンテンツで使われているようです。
- `m_JoinPlayerID`: クエストアイコンに表示するキャラクターポートレート／頭部アイコンを制御します。実際の加入ロジックそのものではないようです。
- `m_SortPriority`: 明示的な UI ソートキー。
- `m_QuestListViewCondition`: クエストログにそのクエストを表示するかどうかを決める、最大 5 個の `QuestTrigCondition.m_id` 参照です。
- `m_QuestListHideCondition`: 同じクエスト行に対する、逆側の非表示条件です。
- `m_AndTrigger`: `m_QuestListViewCondition` を OR 的に扱うか AND 的に扱うかを切り替えているようです。`QuestTrigCondition#0` は常に真／何もしないスロットとして振る舞います。
- `m_HideChapter`: true の場合、`m_Chapter` が設定されていても `Chapter X` を表示しません。
- `m_HideBossSilhouette` / `m_HideBossSilhouetteOnClear`: ストーリー系クエスト用のボスシルエット UI 切替です。
- `m_Title`、`m_SubTitle`、`m_Synopsis`: `GameTextQuest.m_id` を参照します。
- `m_ScenarioProgress`: 内部進行マーカー。まだ完全には解読できていません。
- `m_RecommNeedProg`: パターン化された進行範囲値。通常のレベルというより、ストーリー進行ガイドである可能性が高いです。
- `m_RecommLv`: 推奨レベルの 3 要素配列です。
- `m_InfluenceType`: どのルート／アークに属するクエストか。
- `m_Influence`: 影響力報酬のタプル。
- `m_HideInfoFlag`: 曖昧表示／情報隠し UI を切り替えている可能性があります。
- `m_DisablePriorityDialog`: まだ不明です。
- `m_ShowToBeContinued`: `To Be Continued` の終了タグを表示します。
- `m_EnableReorder`: OT0 では未使用ですが、『大陸の覇者』では一部のサイド系クエストで使われています。
- `m_JobType`、`m_ScenarioPlayerID`、`m_BeforeAccept`: OT0 ではほぼ未使用で、『大陸の覇者』ではより積極的に使われているように見えます。
- `m_RewardType`、`m_RewardItemId`、`m_RewardItemNum`、`m_RewardOption0`: 報酬宣言ブロックです。OT0 では一部しか使っていませんが、UI 一貫性のためにも正しく埋める価値があります。

生エクスポートで見落としやすい点:
- `m_HideQuestList` は条件付き表示とは別の、強制非表示フラグです。
- `m_QuestListHideCondition` は OT0 のエクスポート DB では単一整数であり、`m_QuestListViewCondition` のような 5 スロット配列ではありません。
- `m_Chapter` は、`m_HideChapter` が false のときに実際に表示される章番号です。
- `m_RecommLv`、`m_RecommNeedProg`、`m_Influence` は固定長配列であり、報酬関連フィールドは生エクスポート上 4 スロット配列です。JSON を手で編集するなら、この形を崩さないでください。

### `QuestTrigCondition`

これは中核となるデータ駆動型の述語システムです。

`QuestTrigCondition.m_id` は、他テーブルから参照される再利用可能な条件行 ID です。`QuestTrigCondition.m_ConditionID` は、その行をどう解釈するかをエンジンに伝える条件タイプ／オペコードです。

フィールドごと:
- `m_QuestRollbackId`: ロールバック／リセットグループです。クエスト ID ではありません。
- `m_ConditionID`: クエスト／グローバルトリガー条件タイプ。battle／AI／skill 側の `m_Conditions` とは別の名前空間です。
- `m_OwnerQuest`: クエスト状態チェック用の対象 `QuestList.m_id`。
- `m_OwnerQuestTask`: タスク状態チェック用の対象 `QuestTaskList.m_id`。
- `m_OwnerUniqueChara`: 通常は `CharaPlayer.m_UniqueID` を指します。
- `m_EventID`: しばしば `Event/EventList.m_id` 参照です。
- `m_EnemyID`: 敵対象。
- `m_EnemyRaceID`: 敵種族対象。
- `m_ItemID`: アイテム所持／使用対象。
- `m_EquipID`: 装備関連と思われますが、OT0 行では未使用です。
- `m_JobID`、`m_GenderID`、`m_InfluenceID`: 強く型付けされた enum ベースのチェックです。
- `m_EventFlagVariableID`: `EventFlagVariableList` を指します。
- `m_UtcDateTime`: 『大陸の覇者』の時間制限コンテンツで使われるもので、Unix タイムスタンプ系チェックと思われます。
- `m_IntParam`: `m_ConditionID` に応じて解釈される追加の整数ペイロードです。
- `m_StrParam`: `m_ConditionID` に応じて解釈される追加の文字列ペイロードです。

OT0 データから強く示唆されるパラメータ対応:
- `m_ConditionID = 53 (eCOND_MAP_REACH)` は `m_IntParam` が `Map/MapListTable.m_id` ときれいに一致します。
- `m_ConditionID = 162` は `m_IntParam` が `CharaPlayer.m_UniqueID` と非常によく一致します。
- `m_ConditionID = 181 (eCOND_ENEMY_GROUP)` は `m_StrParam` を `EnemyGroups.m_Label` 文字列として使います。
- `m_ConditionID = 202 (eCOND_HAS_VILLAGE_BUILDING)` は `m_IntParam` を `VillageBuildingData.m_id` として使います。
- `m_ConditionID = 13/14` は `m_StrParam` に `EventList.m_Name` 文字列を入れていることが多いです。

`QuestTrigCondition.m_id` を直接使う側は `QuestList` よりずっと広いです。OT0 では次でも使われています。
- `QuestTaskList.m_StartConditionID` / `m_OpenConditionID`
- `NpcSetList_*` と `NpcTalkList_*`
- `PartyChatList`
- `MapObjectConditionsList`
- `WorldMapList`、`WorldMapIllustList`、`WorldMapMaskList`
- `StoryBookCondition`
- `CharaNpcList`、`CharaJoin`、`MainPlayerIllust`、`MainPlayerRingIllust`
- `NoteList`、`MonsterArenaList`、`VillageBuildingData`、`VillageBuildingLimit`、`VillageTownLevelCondition`

重要な区別:
- `QuestList`、`NpcSetList_*`、`NpcTalkList_*`、`StoryBookCondition`、`WorldMap*` のようなテーブルは `QuestTrigCondition.m_id` という「行 ID 層」を消費します。
- それに対して `QuestTaskList.m_ClearConditionID` や `StoryBookList.m_ReadCondition` は、直接 `EQUEST_CONDITION` の条件タイプ名前空間そのものを消費します。

この 2 層を混同するのは、JSON 上は正しく見えるのにゲーム内では絶対に発火しないクエストを作ってしまう最も簡単な原因の 1 つです。

OT0 で役立つ、よくある条件タイプの心構え:
- `1`: 特定のクエストタスクが active
- `2`: クエストが ready
- `3`: クエストが active / in progress
- `4`: クエストが clear
- `12`: イベントが clear
- `53`: マップ到達／現在地系チェック
- `123`: イベントフラグ／変数チェック

SDK／データ上で見える『大陸の覇者』専用キー:
- `m_MultipleConditions`
- `m_WorldMode`

### `QuestTaskList`

これは実際のクエスト進行の背骨です。`QuestList` がクエストの存在を示し、`QuestTaskList` が「プレイヤーが今何をすべきか」「何で進行するか」を示します。

SDK は次を公開しています。
```hpp
enum class EQUEST_TASK_TYPE : uint8
{
	NONE                                     = 0,
	START                                    = 1,
	PROGRESS                                 = 2,
	EQUEST_TASK_MAX                          = 3,
};
```

有用なフィールド:
- `m_id`: タスク行 ID。
- `m_OwnerQuest`: 所属する `QuestList.m_id`。
- `m_OwnerTask`: 連鎖タスク構造で使う親タスク／グループ参照。
- `m_TaskType`: OT0 では `START` か `PROGRESS`。
- `m_TaskNo`: クエスト内で表示される順番／番号。
- `m_HideTask`: このタスクを UI から隠します。
- `m_Purpose`、`m_Synopsis`、`m_NextDestination`: テキスト／UI 補助。
- `m_DestNpcSetLabel`: 目的地ガイドに使う対象 NPC セット／オブジェクトのラベル。
- `m_DestPathName`: ナビゲーション／UI ガイドに使うパス Actor 名。
- `m_DestTownInteriorAppearPlacePointId`: 屋内目的地アンカー。
- `m_ClearedQuest`: この行に対して既にクリア済み／関連済みと見なすクエスト配列。
- `m_OpenConditionID`: タスクを表示／開放する `QuestTrigCondition.m_id` 行。
- `m_StartConditionID`: タスクを開始／有効化する `QuestTrigCondition.m_id` 行。
- `m_InvalidConditionID`: 無効化側条件配列。
- `m_ReorderMax`、`m_ReorderInterval`: タスク順序制御。まだ完全には解読できていません。
- `m_SubClearConditionID`: 追加の行 ID 型クリア補助。
- `m_ClearConditionID`: 直接の条件タイプ／オペコードであり、`QuestTrigCondition.m_id` ではありません。重要です。
- `m_CondQuest`、`m_CondQuestTask`、`m_OwnerUniqueChara`、`m_EventID`、`m_EnemyID`、`m_EnemyRaceID`、`m_ItemID`、`m_IntParam`、`m_StrParam`: `m_ClearConditionID` 用のインラインペイロード。
- `m_StartEvent`: タスク開始時に起動するイベント。
- `m_ShowStartUI`: タスク開始 UI を表示するかどうか。
- `m_ClearEvent`: タスククリア時に起動するイベント。
- `m_ShowClearUI`: タスククリア UI を表示するかどうか。
- `m_TaskItemId`、`m_TaskItemNum`: 収集／納品型タスクのペイロード。
- `m_AddMemberSetID`: `PlayerMemberSet` を指し、パーティ利用可能状態を変えるように見えます。
- `m_RemovePlayerIDs`: 利用可能パーティ集合から特定プレイヤーを除外します。

実際には、クエストが「存在はしているが決して進まない」ように見える場合、最初に監査すべき 2 箇所は `QuestTaskList` と `QuestTrigCondition` です。

生エクスポートでの注意点:
- `m_OpenConditionID`、`m_StartConditionID`、`m_InvalidConditionID`、`m_SubClearConditionID`、`m_TaskItemId`、`m_TaskItemNum`、`m_RemovePlayerIDs` は単一値ではなく配列です。
- OT0 では、非ゼロの `m_ClearConditionID` の大半は `12 (eCOND_EVENT_CLEAR)` です。つまり、多くのタスクは再利用型 `QuestTrigCondition` 行ではなく、「このイベントが終了したか」でクリアされます。

### `LimitConditions`

このテーブルはクエストログ用というより、マップ／エリア／システムルール用テーブルに見えます。

観察されるフィールド:
- `m_LimitType`: スロットごとのルールタイプ配列。
- `m_worldMapID`、`m_mapID`: ワールド／マップ対象。
- `m_BootQuestStaet`、`m_BootQuestID`、`m_BootQuestTaskId`: 有効化側のクエスト状態連携。
- `m_DisableQuestState`、`m_DisableQuestID`、`m_DisableQuestTaskId`: 無効化側のクエスト状態連携。
- `m_TrigConditionID`、`m_NoneConditionID`: 追加の `QuestTrigCondition.m_id` ゲート。
- `m_BgmID`、ミニマップテクスチャ関連フィールド、`m_MinimapLCLabel`: 追加の演出／システム系ペイロード。

ここで役立つ SDK enum:
- `EQUEST_STATE`: `0 NONE`、`1 READY`、`2 ACTIVE`、`3 CLEAR`
- `ELIMIT_CONDITIONS_TYPE`: OT0 では特に `8 eFIX_FIELD_BGM`、`2 eFASTTRAVEL_BAN`、`29 eMINIMAP_FAST_TRAVEL_BAN`、`13 eMINIMAP_HIDE_ICON`、`6 eMINIMAP_EXTRA_JOINMAP`、`97 eMINIMAP_FASTTRAVEL_EXCLUDE_FROM_SEARCH` がよく使われます

クエストやイベントで、単にクエストログ状態だけでなく、環境／マップレベルの挙動も変えたい場合に使います。

完全な SDK エントリ:
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

とても小さいテーブルです。SDK レイアウトで見えるのは次だけです。
- `m_PrevTermRingTex`
- `m_PrevTermQuestId`

これは進行ロジックというより、ストーリーリング／前ターム表示データに見えます。メインストーリーリング UI を触らないなら、通常は放置で構いません。

### `QuestAggregatedPoint`

再利用可能な目的地／マーカーアンカーテーブルに見えます。

フィールド:
- `m_id`
- `m_LevelId`
- `m_AppearLabel`

複数のクエストタスクが、それぞれ別の目的地を持つのではなく、同じワールドマーカー／アンカーに集約されるべき場合に使います。

### `QuestAggregatedTaskList`

個々の `QuestTaskList` 行を集約ポイントに結び付けます。

フィールド:
- `m_QuestTaskId`
- `m_AggregatedPointId`
- `m_Priority`

言い換えると、`QuestAggregatedPoint` がマーカーアンカーを定義し、`QuestAggregatedTaskList` がタスクをそこへ割り当てます。

## `Event/` の下

### `EventList`

`EventList` は、再生可能／呼び出し可能なイベントアセットのレジストリです。

どのイベントアセットが存在するか、どのマップに属するか、イベント終了時にどこへ戻るかといった位置メタデータを、ここでエンジンに伝えます。

重要フィールド:
- `m_id`: 他所で使われるイベント ID（`QuestTaskList`、`NpcTalkList_*`、`StoryBookList`、`ScenarioReplay*`、イベントコマンドなど）。
- `m_Version`: フォルダ／カテゴリ的なグループ。実際には、実アセットパスの構成の一部になっています。
- `m_Name`: イベントアセット名。`m_id` の次に重要なフィールドです。
- `m_MapID`: 所属／主マップ。
- `m_ReturnMapID`、`m_ReturnPathActorName`、`m_ReturnPos`、`m_ReturnDir`: イベント終了後にプレイヤーが戻る位置。
- `m_Kind`: イベントカテゴリ。SDK は `EEVENT_KIND` を公開しており、boss／chara／sub などの分類があります。
- `m_PlayBGM`、`m_SoundCueID`: オーディオ設定。
- `m_Skip`: スキップ挙動。
- `m_BanAutoPlay`、`m_MapLoadWait`、`m_Seamless`、`m_StartEnvVolumeZero`: ロード／再生挙動フラグ。
- `m_IsVillageEvent`、`m_IsWarpEvent`、`m_IsNotHideMapUI`、`m_BanChangePlayMode`、`m_BanEncountMgrBgmChange`、`m_BanLoadBattleLevel`: 特殊なランタイムフラグ。

関連する `EEVENT_KIND` 値としては、`1 BOSS_EVENT`、`2 CHARA_EVENT`、`3 SUB_EVENT`、`16 TOWN`、`18 VILLAGE`、`19 PARTYCHAT`、`27 JOIN_EVENT`、`28 ARRIVAL_EVENT` などがあります。

実用上の原則: 新しいイベントアセットを追加しても、それに一致する `EventList` 行を追加しなければ、ほぼ何もそのイベントをまともに呼び出せません。

すべてのイベント種別:
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

実際のイベントコマンドは、`Event/V*/.../*.uasset` 配下のイベントアセットに入っています。

典型的な作業手順:
- 近い既存イベント JSON を複製する
- 新しい `EventList.m_Name` に一致する新ファイル名を付ける。また、作成したアセットのメタデータもそのファイル名に一致するようにしてください（そのためには [UAssetGUI の最新 experimental リリース](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest) を使ってください）
- コマンド行内の ID／テキスト／対象を更新する
- `QuestTaskList.m_StartEvent`、`QuestTaskList.m_ClearEvent`、`NpcTalkList_* .m_EventID` など、呼び出し元を新しい `EventList.m_id` に向ける

コマンドごとのオペコード注記については次を参照してください。
- [Event Opcode Crossref Atlas](https://github.com/veganprimate/CotCPort/blob/main/Event/Event_Opcode_Crossref_Atlas.md)

### `EventFlagVariableList`

これはイベントロジックと `QuestTrigCondition.m_EventFlagVariableID` が使う数値イベントフラグ／イベント変数テーブルです。

観察されるフィールド:
- `m_id`: フラグ／変数 ID。
- `m_FlagType`: フラグの型／カテゴリ。
- `m_VolatilityValidMap`: 揮発フラグのマップスコープ。
- `m_UpperLimit`、`m_LowerLimit`: 数値範囲／クランプ用メタデータ。

クエスト／イベントに、クエスト／タスクのクリア状態だけでは表せない独自状態を記憶させたい場合に使います。

### `EventFlagObject`

`EventFlagObject/*.uasset` は別の、より記号的な条件システムを使います。行は `QuestTrigCondition.m_id` の整数ではなく、`conditions = ["..."]` のような文字列リストを持ちます。

これは `QuestTrigCondition` と同じ名前空間ではなく、隣接するマップ／オブジェクト条件レイヤーとして扱ってください。

## `StoryBook/` の下

### `StoryBookCondition`

このテーブルは StoryBook／Journal の解放ロジックをクエストトリガーシステムに橋渡しします。

フィールド:
- `m_OpenCondition`: 通常は `QuestTrigCondition.m_id`
- `m_ConditionText`、`m_ConditionExplain`: UI テキスト
- `m_ReccomendLevel`: 推奨値
- `m_QuestID`: クエスト関連付け
- `m_MapID`: マップ関連付け

新しい Journal 項目を、クエスト／イベント条件達成時に解放したいなら、最も素直な橋渡しはこれです。

### `StoryBookList`

これは実際の StoryBook エントリメタデータテーブルです。

有用なフィールド:
- title／tab／category／icon 系フィールド
- `m_ApperStoryBookCondition`、`m_ApperQuestCondition`、`m_ApperCondition`
- `m_OpenCondition`
- `m_ReadCondition`
- reward 系フィールド
- `m_EventID`
- battle メタデータ系フィールド

重要な注意点:
- `StoryBookCondition.m_OpenCondition` は `QuestTrigCondition.m_id` の間接参照を使います。
- それに対して `StoryBookList.m_ReadCondition` は、クエスト／グローバルトリガー条件タイプ名前空間を直接使います。

## `Character/` の下

### `PlayerMemberSet`

これは `QuestTaskList.m_AddMemberSetID` の背後にあるテーブルです。

フィールド:
- `m_ValidCondition`: その member-set 行を有効にする `QuestTrigCondition.m_id`
- `m_PlayerIDs`: 最大 8 個のプレイヤー ID／パーティスロット。ただし 1 つ目は主人公専用であり、Zero 以外の Player ID を入れるとクラッシュする可能性があります

クエストイベントで、利用可能パーティ編成を一時的に強制／追加／差し替えしたい場合に使います。

### `CharaJoin`

キャラクター加入／解放のゲーティングテーブルです。

重要フィールド:
- `m_ConditionID`: その加入行をゲートする `QuestTrigCondition.m_id`

正確な使い方はまだ不明です。

### `CharaNpcList`

マップローカルな会話ツリー自体とは別の、より高レベルな NPC インタラクションゲートです。

条件アトラスで参照される重要フィールド:
- `m_Hear_CheckCondition`
- `m_Invite_CheckCondition`
- `m_ReleaseConditionID`
- `m_VillageInviteReleaseConditionId`

これは、クエストによって単に台詞を変えるだけでなく、「聞き出す」「誘う」「解放する」といった NPC 相互作用自体を変えたい場合に特に重要です。

## `Npc/` の下

### `NpcSetList_*`

これらのファイルは通常、NPC／オブジェクトがワールドに出現するかどうかそのものを制御します。

重要フィールド:
- owner／object／talk 系識別子
- `m_MapID`
- `m_AppearLabel`
- `m_TalkID`
- quest boot／disable 系フィールド
- `m_TrigConditionID`
- `m_NoneConditionID`

典型的な用途: クエスト依頼人をある条件の後でのみ出現させ、別条件の後で消し、進行後には別の会話ツリーへ切り替える。

### `NpcTalkList_*`

これらのファイルは通常、マップ固有の NPC 会話インタラクションにおける実際の会話ノード／分岐を制御します。

重要フィールド:
- `m_TalkText`
- `m_EventID`
- `m_QuestID`
- `m_TrigConditionID`
- `m_NoneConditionID`
- 選択肢テキスト／影響力値／次の会話番号

典型的な用途: クエスト進行で別会話分岐をゲートし、必要なら会話ノードからクエストイベントを起動する。

### ルート `NpcTalkList`

これは、完全な `QuestTrigCondition.m_id` 行ではなく、別のローカル小 ID 条件名前空間を使っているようです。`m_TrigConditionID`／`m_NoneConditionID` の値が `NpcTalkList_*` や `QuestList` と互換だと仮定してはいけません。

## `Map/` の下

### `MapListTable`

これはメインのマップレジストリです。

クエスト／イベント作業で特に有用なフィールド:
- `m_MapLabel`、`m_MapText`、`m_MapPath`、`m_PathDataPath`
- `m_PlayerAttachEffectTriggers`
- `m_PlayerAttachEffectInvalidConditions`

この attach-effect 条件配列も、`QuestTrigCondition.m_id` 行が消費される場所の 1 つです。

### `MapReplaceList`

条件付きでマップ状態／レイアウトを差し替えます。

有用なフィールド:
- `m_EnableTrigConditionID`
- `m_DisableTrigConditionID`

クエスト進行で NPC だけでなくマップそのものを変える必要がある場合に使います。

### `WorldMapList`, `WorldMapIllustList`, `WorldMapMaskList`

これらはワールドマップのマーカー、イラスト、開示マスクを制御します。

3 つとも条件配列を使って、クエスト進行に応じた表示／非表示を行います。カスタムクエストでマップアートを開示したり、霧を消したり、ワールドマップマーカーを追加／削除したりしたいなら、ここを編集します。

### `WorldList`

より高レベルなワールド状態の可視性ゲートです。

重要フィールド:
- `m_TrigConditionIds`
- `m_InvalidConditionIds`

クエストログ上の状態とは別に、広域ワールドマップや地域表示が変わる場所の 1 つです。

## `Object/` の下

### `MapObjectConditionsList`

マップオブジェクトごとの表示／非表示または有効／無効ゲートです。

重要フィールド:
- `m_TrigConditionID`
- `m_NoneConditionID`

クエストでオブジェクト、扉（例: the Gate of Orsa）、インタラクト、看板、ギミック、その他の配置物を出したり消したりする必要があるなら、このテーブルは `NpcSetList_*` と同じくらい重要になることが多いです。

## `ScenarioReplay/` の下

Scenario replay データは、主にクエストやイベントに戻っていく演出／UI コンテンツです。

有用なテーブル:
- `ScenarioReplayChapterParam`: 章カード演出と `m_SilhouetteCond`
- `ScenarioReplayMainSectionParam`: セクションタイトルと `m_EventId` 配列、さらに capture／check-entry イベント ID
- `ScenarioReplaySubSectionParam`: `m_QuestId` と `m_EventId` でキー付けされた subsection 行
- `ScenarioReplayBondSectionParam`: `m_EventId` を持つ bond／character recap 項目
- `ScenarioReplayMainSectionText`: セクションテキストと置換イベント条件

機械的に動くクエストを作るだけなら通常これらは不要ですが、カスタムコンテンツを replay／recap UI に出したいなら必要です。

## よくあるエラー

- `QuestTrigCondition.m_ConditionID` は battle 側 `m_Conditions` と同じ名前空間ではありません。
- `QuestTaskList.m_ClearConditionID` は `QuestTrigCondition.m_id` ではなく、直接の条件タイプです。
- `NpcTalkList.json` と `NpcTalkList_*` は同じ条件名前空間を使っていないようです。
- `EventList.m_Name` は実際のイベントアセット名と一致しなければならず、アセットのメタデータもそれに合わせて修正する必要があります。
- 多くのテーブルは数値 ID だけでなく、ラベル（`m_AppearLabel`、`m_DestNpcSetLabel`、path actor 名）も参照します。
- UI 可視性、ワールド上の存在、実際の進行はしばしば別テーブルに分散しています。通常、そのうち 1 つだけ直しても足りません。

## 追加の DB / SDK 所見

- `StoryBookList.m_OpenCondition` は `StoryBookCondition.m_id` 値の配列であり、`QuestTrigCondition.m_id` と同じ層ではありません。
- `StoryBookList.m_ApperQuestCondition` は主に quest-ID 側ゲートとして振る舞いますが、`m_ApperCondition` は依然として別の appearance 側条件名前空間のようで、まだ完全には解読できていません。
- `MapReplaceList` は、クエスト状態フィールド（`m_EnableQuestID`、`m_EnableQuestState`、`m_EnableQuestTaskID`）か、トリガー条件配列のどちらでも差し替えをゲートできます。OT0 には両方の経路があります。
- `MainStoryRingList` は OT0 では非常に小さく、現在のエクスポートにも謎の常に空配列 `m` が含まれているため、メインストーリーリング UI を明示的に触るのでなければ無視しても安全そうです。
- `MapObjectConditionsList` と `NpcSetList_*` はしばしば一緒に更新する必要があります。片方が配置オブジェクトを制御し、もう片方が配置 NPC／オブジェクトセット（例: 看板そのものと、それに書かれたテキストを担う同位置の不可視 NPC）を制御するためです。

## デバッグ用チェックリスト

- **クエストがログに出ない**: `QuestList`、そのテキスト ID、view／hide 条件 ID を確認する。
- **NPC やオブジェクトが出ない**: `NpcSetList_*`、`MapObjectConditionsList`、`LimitConditions`、マップ差し替えルールを確認する。
- **違う会話分岐が出る**: そのマップが `NpcTalkList_*` を使うのか、ルート `NpcTalkList.json` の条件名前空間を使うのかを確認する。
- **タスクが開始しない**: `QuestTaskList.m_OpenConditionID`、`m_StartConditionID`、`m_InvalidConditionID` を監査する。
- **タスクがクリアしない**: `QuestTrigCondition` だけでなく、インライン `m_ClearConditionID` ペイロード（`m_CondQuest`、`m_EventID`、`m_ItemID`、`m_IntParam`、`m_StrParam`）を監査する。
- **イベントが再生されない**: `EventList.m_id`、`m_Name`、`m_Version`、イベントアセットメタデータ、呼び出し側フィールドがすべて一致しているか確認する。
- **パーティ変更が起きない**: `PlayerMemberSet` と `QuestTaskList.m_AddMemberSetID` / `m_RemovePlayerIDs`、さらに新規プレイアブルを追加しているなら `CharaPlayer` も確認する。
- **StoryBook や replay UI に出ない**: `StoryBook/*` と `ScenarioReplay/*` に橋渡し行を追加する。クエスト本体はそれら無しでも動き得ますが、UI は動きません。
