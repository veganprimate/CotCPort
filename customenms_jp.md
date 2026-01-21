### **LANGUAGE:**  [English](customenms_en.md) | [日本語](customenms_jp.md) | [中文](customenms_zh.md) | [한국어](customenms_kr.md)

『オクトパストラベラー0』に新しい敵や戦闘を追加する作業は、主に `AIBattle`、`Enemy`、（場合によっては）`Skill`、`Texture`、`Battle` 配下の DataBase ファイルを編集することに集約されます。以下に関連ファイルの説明をまとめます。見出しの星の数は「そのファイルを編集する必要性の高さ」を示します（星が少ないほど、より特殊な用途で使われます）。ただし、どの目的でどのファイルを触るべきかを把握するため、記事全体に目を通すことを推奨します。

## `Enemy/` 配下
### (***) `EnemyFormations`
フィールド上の新しい「敵編成（フォーメーション）」を指定できます。`m_PositionID` には `BattlePositions.m_id` を指定します（`BattlePositions` は `Battle/` 配下）。多数の敵が登場するカスタム戦闘を作る場合は、`m_PositionID` に 0 以外の値が十分入っている既存フォーメーションを探すか、自作するとよいでしょう。なお、戦闘マップによっては、同じ `PositionID` でも敵の立ち位置が大きく変わる場合があります。

### (***) `EnemyBattleAnimSet`
大陸の覇者（CotC）では敵に対して特定の Flipbook アセットを参照する ID を割り当てますが、OT0 ではそれを「ID のセット」として持ち、各 ID が Flipbook アセットを参照します（`m_EnemyTextureIDs` は `Texture` 配下の `EnemyTexID` の `m_id` を入力として取ります）。
通常、`m_EnemyTextureIDs[0]` はスプライトの待機（アイドル）アニメ（サフィックス `Idl00`）を指し、`m_EnemyTextureIDs[1]`〜`m_EnemyTextureIDs[3]` は攻撃アニメ（サフィックス `Atk00`〜`Atk02`）を指します。最低限、待機アニメ用の Flipbook アセットは追加する必要があります（つまり `EnemyTexID` に新しいパスを指すエントリを追加し、その新エントリの `m_id` を、ここで新規追加する `EnemyBattleAnimSet` の `m_EnemyTextureIDs[0]` に指定します）。それ以外は任意です。

注意：CotC の `EnemyType` にある `m_FlipbookID` は、この配列の追加要素を OT0 とは異なる使い方をしているように見えます。インデックス 0 が待機テクスチャを指す点は同じですが、CotC ではインデックス 1 に不可視テクスチャを割り当てている敵がいます（例：「双世界の影（Shadow of Twin Worlds）」のシャドウ）。

NPC テクスチャを使用する戦闘では、`EnemyBattleAnimSet` に追加エントリを作る必要は **ありません**。NPC は通常、戦闘用スプライトシート（多くは `_B` サフィックス付き）を既に持っているためです。`EnemyType`（下記）で NPC の `CharaID` を指定してください（必要に応じて [How to add custom characters](customchars_en.md) も参照）。

### (**) `EnemyParts`
カスタムボスが複数パーツで構成される場合（例：オルガルデラ（Or'Galdera）、天津神のオロチ（Amatsukami no Orochi）など）、各パーツをこのファイルで指定する必要があります。ただし「双世界の影（Shadow of Twin Worlds）」のように、このファイルを全く使わないケースもあります。

### (****) `EnemyTypeID`
このアセットは、OT0 における敵アーキタイプの「戦闘時スプライト表示（プレゼンテーション）記述子」のマスターに相当します。ゲームに対して以下を指定します：
- 使用するアニメーションセット、または NPC／キャラクタースプライトシートの関連付け（`m_AnimationSetIDs` は `EnemyBattleAnimSet` の `m_id` を入力に取り、`m_NpcCharaID` は `Character/` 配下の `CharaID` の `m_id` を入力に取る）
- スプライトおよび各種オーバーレイのスケール／位置：
  - ヒットスパーク（`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`）
  - シールド／弱点表示オーバーレイ
  - 敵エフェクト（`m_EffectOffsetX`,`m_EffectOffsetY` および `m_EffectFromBottomOffsetY`：スプライト下端からのオフセット。地面設置系エフェクトでよく使われる）
  - ダメージ数値（`m_DamageOffsetX`,`m_DamageOffsetY`）
  - 状態異常（`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`）
  - カーソル（`m_CursorOffsetX`,`m_CursorOffsetY`）
- その敵アーキタイプに紐付ける敵パーツ（`m_PartsID`）
- その敵タイプに使用するサウンド／ボイス：
  - `m_CallSE`（いわゆる「コールSE」）
  - `m_VoiceSymbol`（`Sound` 配下の `VoiceSymbolList` を参照していると思われる整数）
  - `m_CueSheet`（`Sound/` 配下の `SoundSheetList` を参照して、その敵のボイスセットで使用するキューシートを選ぶものと思われる）
- 付随エフェクト（VFX）をどれをどこに生成するか（`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`）
- 行動順（タイムライン）アイコンに使うテクスチャ位置（通常は Flipbook／キャラスプライトシートの先頭フレーム）と、その表示スケール：
  - 小アイコン：`m_OrderTexUS`, `m_OrderTexVS` と `m_OrderScaleS`
  - 大アイコン（例：そのキャラが行動中の強調表示時）：`m_OrderTexUL`, `m_OrderTexVL` と `m_OrderScaleL`
  例として、「双世界の影」戦のシャドウは、行動順アイコンに全身が収まるよう非常に小さい `OrderScale` を使用しています。
- その他（Miscallaneous）：
  - `m_OverrideOrderIconAnimSetID`：行動順アイコンに特殊なテクスチャアセットを使いたい場合
  - `m_AttachEffectInGodbeastProduction`：OT0 と CotC の双方で未使用。神獣エフェクト関連
  - `m_AddStartUV`：開始 UV オフセット（位置）を加算するかどうかのフラグ
  - `m_LinkOwnerAnim`：true の場合、パーツ／アタッチが本体のアニメーションタイミングに追従する可能性がある
  - `m_DefeatEffect`：撃破時に再生する Effect ID（例：「全てを授けし者（Bestower of All）」の特殊な撃破エフェクト）
  - `m_ChangeColorID`：色変更テーブルへの ID。`Character/` 配下の `CharaColorChangeParams` に関連している可能性があります。OT0 では、ゼロ／指輪に選ばれし者が自分の影と対峙する際（`ChangeColorID` 6）にのみ使用されます。CotC の `EnemyType` にはこのキー自体が存在しません。

### (**) `EnemyWeakID`
敵に割り当て可能な「弱点（耐性）プロファイル」を管理します。`m_ResistWeapon` と `m_ResistMagic` は `Skill/` 配下の `ResistType` の `m_id` を入力として取り、`ResistType` が 1 の場合は、指定した武器／属性タイプを弱点としても追加します。指定する武器・属性タイプの並びは、おそらく `Skill/` 配下の `WeaponType`／`MagicType` の `m_id` の順に対応しています。例：
```
{
            "m_id": 0,
            "m_ResistWeapon": [
              0, (Sword/剣)
              0, (Polearm/槍)
              0, (Dagger/短剣)
              0, (Axe/斧)
              0, (Bow/弓)
              0, (Staff/杖)
              0, (Polearm/槍)
              0, (Tome/本)
              0, (Fan/扇)
              0 (無属性の物理ダメージ。他の武器種はカウントされない。解析後もゲーム内では「？」表示になる)
            ],
            "m_ResistMagic": [
              0, ("None/なし"：弱点アイコンも表示されないように見える)
              0, (Fire/火)
              0, (Ice/氷)
              0, (Lightning/雷)
              0, (Wind/風)
              0, (Light/光)
              0, (Dark/闇)
              0, (無属性の魔法ダメージ。他の属性はカウントされない)
              0 (Poison/毒：毒状態の継続ダメージでシールドを1削る)
            ]
          }
```
**注意：** OT0 では 1 体の敵に対して 8 個を超える弱点を表示できません。

### (**) `EnemyWeakChangeID`
敵が弱点を変化させる仕組みを管理します。`m_WeakIndices` は `m_EnemyWeakID.m_id` を入力として取り、ここで定義した弱点変化の `m_id` は、例えば avail に割り当てることができます（[How to add custom skills and avails](customskls_en.md) も参照）。

### (**) `EnemyWeakLockID`
敵が弱点をロックする仕組みを管理します。無属性魔法ダメージと毒ダメージをロックするためのフィールドは存在しないように見えます。

### (****) `EnemyID`
ゲームにおける敵のマスターレコードです。ステータス／弱点／報酬／UI フラグ等を定義し、表示面は `EnemyTypeID`（アニメーションセット、テクスチャ、オフセット、ボイス、エフェクト）を参照し、挙動と使用スキルは `TacticalAssignList` と `TacticalSkillList` を参照します（後述の「`AIBattle` 配下」を参照）。
- `m_Label`：敵のラベル。内部的に使用されている可能性があり、ラベルが衝突する新規エントリは避けるべきです
- `m_DisplayName`：`GameTextEnemy.m_id` を参照する ID（全ローカライズ共通）
- `m_WeakID`：`EnemyWeakID.m_id` を参照する ID（上記参照）
- `m_DisplayLevel`：表示上のレベル。残念ながら `m_LevelUI` を true にしても表示されません
- `m_BreakRate`：ブレイク中に与えるダメージへ掛かる倍率（％）
- `m_DamageRate`：非ブレイク時に与えるダメージへ掛かる倍率（％）
- `m_MaxSLD`：敵のシールドポイント（シールド数）。キー名は最大値を示唆しますが、戦術やスキルによってはこれをさらに上回る値に設定できます（例：Lucian the Glorious）
- `m_MaxHP`：敵のHP。同様に、スキルや戦術で増加し得ます
- `m_MaxSP`：敵のSP。多くの敵スキルは SP 消費が 0 のため、このステータスは主に特定スキルの使用回数制限に用いられます
- `m_AtkP`：敵の物攻
- `m_AtkM`：敵の属攻
- `m_DefP`：敵の物防
- `m_DefM`：敵の属防
- `m_Agi`：敵の速度（敏捷）
- `m_Crt`：敵の会心
- `m_CrtDef`：敵の会心防御（このステータスは敵にのみ存在するように見える）
- `m_Hit`：敵の命中
- `m_Avd`：敵の回避
- `m_ResistAilmentID`：状態異常耐性を管理する ID を敵に割り当てる（`Skill/` 配下の `SkillResistAilmentID` を参照）
- `m_ResistAilment`：特定の状態異常への耐性率に対応する配列。割り当てた `ResistAilmentID` の `m_ResistAilments` 配列が埋まっている場合に使われる可能性があります。キャットリン（Caits）にのみ使用されているように見えます
- `m_TacticalAssignID`：戦闘中に使用する戦術リストに対応する `TacticalAssignList.m_id` を敵に割り当てる（後述の「`AIBattle` 配下」を参照）
- `m_SkillsID`：戦闘中に使用するスキルリストに対応する `TacticalSkllList.m_id` を敵に割り当てる（後述の「`AIBattle` 配下」を参照）
- `m_IsBoss`：多くのボスで使用されるフラグですが、街の NPC にも付与されています。機能的に何をするかは不明です
- `m_Exp`, `m_Money`, `m_JP`：撃破時に得られる EXP／リーフ（お金）／JP
- `m_PetExp`：CotC と OT0 の双方で常に -1。-1 以外の値にしても効果がないように見えます
- `m_DropReward`：敵が落とし得るアイテム（有効 ID は `EnemyDropID` を参照）
- `m_StealReward`：敵から盗めるアイテム（有効 ID は `Item/` 配下の `ItemList` を参照）
- `m_StealLeafNum`：敵から盗めるリーフの量
- `m_StatusOffset`：敵のステータスバー（弱点とシールド数）のオフセット
- `m_Bottle`：意味不明。OT0 では 1〜3 を使用。3 は OT0 では Wappa のみが使用。CotC は 4 と 5 も使用
- `m_NameUI`：UI に敵名を表示するか
- `m_LevelUI`：UI に `m_DisplayLevel` を表示するか（未使用／非機能）
- `m_HpUI`：OT0 では非機能
- `m_ShieldUI`：true の場合、敵のシールドアイコンが暗く表示されるように見えます
- `m_RaceIndices`：敵に割り当てる種族。ダメージ計算に影響し得ます
- `m_DisplaySpecialSkillGauge`：敵の BP カウンター（BPゲージ）を表示するフラグ
- `m_GenerateSpecialSkillValueOrverTurn`：敵がターンごとに生成する BP 量
- `m_ReduceSpecialSkillValueBreak`：ブレイク後に敵が失う BP 量
- `m_MaxSpecialSkillValue`：敵の最大 BP。20 を超えるとゲームがクラッシュします
- `m_OutsideTarget`：敵をターゲットできなくなり、かつ場にその敵しかいない場合、味方側も行動をスキップします（「全てを授けし者（Bestower of All）」は指輪が破壊されるまでこれを使用）
- `m_NotAction`：true の場合、敵が行動できません（「全てを授けし者」が全指輪撃破前に使用）
- `m_UnknownLv`：OT0 では未使用
- `m_Immortal`：true の場合、敵のHPは 1 未満になりません

### (**) `EnemyReinforcements`
戦闘中に追加で出現させる敵（増援）と、その出現位置を定義する補助テーブルです。
- `m_Reinforcements` は `EnemyID.m_id` を入力に取ります
- `m_Positions` は `BattlePositions.m_id` を入力に取ります（対象の敵グループのフォーメーション側でこの位置が指定されている必要はありません）
`EnemyReinforcements.m_id` を参照しているアセットは `Skill/` 配下の `SkillAvailID` のみです。

### (****) `EnemyGroups`
全ての敵グループを記録します。戦闘をゲーム内で発生させるには、ここにエントリを追加する必要があります。
- `m_Label`：新規追加する敵グループでは、ラベルが既存エントリと重複しない（ユニークである）ことを必ず確認してください。イベントはこのラベルを使って敵グループとの戦闘を発火します。モンスターアリーナに追加する予定がある場合、そのエントリを他用途で再利用すると、撃破後にモンスターアリーナのメニューが表示され続けたり、クラッシュの原因になったりする可能性があります
- `m_Inescapable`：戦闘から逃走できるかどうか
- `m_DisableRetire`：OT0 では常に false。OT0 では機能していないように見えます。CotC で戦闘中にスマホを中断できる機能と関係がある可能性？
- `m_SkipResult`：リザルト画面（EXP／リーフ／JP の獲得等）をスキップするか
- `m_IgnoreDefeat`：敵グループに敗北してもゲームオーバー画面を出さないフラグ。OT0 では King Pardis III、Edoras Defenders of Fame/Power/Wealth、Colossal Blightant のみが使用。モンスターアリーナ内では上書きされるようで、敗北後はアリーナに戻されます
- `m_SkipBgmResult`：勝利BGMを流すかどうか
- `m_BanGodBeast`：戦闘で神獣の使用を禁止します。OT0 では未使用。本 MOD で一部キャラのスキルボードに神獣スキルを追加した場合、これがそれをブロックする可能性があります（未検証）
- `m_PlayerMemberSetID`：参照先 `PlayerMemberSetID` に指定されたキャラクター構成へパーティを強制します（[How to add custom characters](customchars_en.md) も参照）
- `m_Bgm`：戦闘BGMを設定（有効な選択肢は `Sound/` 配下の `SoundList` を参照）
- `m_NightBgm`：夜の戦闘BGMを設定。OT0 では未使用。機能するか未検証
- `m_DarkBgm`：`m_NightBgm` と同じ？ CotC では両キーが常に同値に見えます
- `m_BgmType`：
- `m_ResultBgmWin`：専用の勝利BGMを指定
- `m_ResultBgmLose`：専用のゲームオーバーBGMを指定
- `m_Camera`：使用するカメラセットを指定（入力：`BattleCameraSet.m_id`）
- `m_Formation`：敵グループのフォーメーションを指定（入力：`EnemyFormations.m_id`）
- `m_EnemyID`：場に出す敵を指定（入力：`EnemyID.m_id`）。この配列を 6 体超に拡張しても即クラッシュはしませんが、新しい敵はタイムライン（行動順）に表示されず、弱点／シールド／状態表示オーバーレイもなく、ターンが回ってくるとクラッシュします（エラーは allocator メタデータ破損／ポインタ破損に関連）。増援の呼び出しで回避できる可能性があります
<img width="829" height="459" alt="image" src="https://github.com/user-attachments/assets/433a1c36-e857-4b4c-aaae-07399ddac40b" />

- `m_EventIndices`：（戦闘）開始時または戦闘中に再生されるイベント（戦闘中会話等）。おそらく `EventList.m_id` ではなく `BattleEventList.m_id` が有効値です
- `m_AbortCondition`：戦闘を中断する条件。有効 ID は `BattleAbortConditions` 配下にあるはずです
- `m_DefeatPriority`：OT0/CotC とも常に false。何をするか不明
- `m_ForcedWinAbortCondition`：強制勝利にするための条件を設定。有効 ID は `BattleAbortConditions` 配下にあるはずです
- `m_IsAllEnemyDeadOnForceWin`：（未検証）`m_ForcedWinAbortCondition` の条件を満たした際に、全ての敵が死亡するかどうか？
- `m_ForcedLoseAbortCondition`：強制敗北にするための条件を設定。有効 ID は `BattleAbortConditions` 配下にあるはずです
- `m_ImportantFlags`：6 種類のフラグ。いずれも OT0 では未使用で、テストでも意味を特定できませんでした
  - Flag 1：CotC では主に Job Tower ボスが使用
  - Flag 2：`Dice_Bolaven_Rank04_BossGr03`（Idore、Roguish Riven Soldier I、Roguish Riven Soldier II との戦闘）のみが使用
  - Flag 3：Flag 2 と同じ敵グループのみが使用
  - Flag 4〜6：両作品とも未使用
- `m_PetDear`：-1、0、1 を値として取る
- `m_DisableBattleEndWipe`：多くのラスボス（全てを授けし者、オルガルデラ、サイドソリスティアのガルデラ）や、魔導アーマーでも使用
- `m_DisableBattleEndTraining`：訓練場にいるパーティメンバーが EXP／JP 等を得られないようにします。エメラルド・ダイアウルフ戦（ロラーナが場にいる場合のみ）、サザントスの回想中の「全てを授けし者」第7章戦などで使用
- `m_DisableSupporter`：戦闘で助っ人を呼べるかどうか
- `m_EncountEffectTypeID`：特殊なエンカウント演出を指定（例：FF6 コラボのエンカウント）。アリーナ戦では 0 にしないとゲームがソフトロックします。有効 ID は `EncountEffectTypeID` にあります
- `m_EnablePlayerShield`：OT0/CotC とも常に false
- `m_DisableGrade`：OT0 では常に false、CotC ではそうではありません。武器のグレードを無視するフラグの可能性
- `m_IsContainRareEnemy`：レア敵を含む敵グループをゲームに知らせるフラグの可能性（レア敵の遭遇率を上げるアクセサリーが影響するため）

### (**) `EnemyModeID`
どの mode ID が、フィールドまたは敵に適用されるどのエフェクトを指すかを定義します。これらのエフェクトは avail に割り当てられます。ボスがブーストモードに入る際に特殊演出を表示する用途で使われます。

### (**) `EnemyDropID`
敵に割り当て可能なドロップ ID を管理します。

### (**) `EncountVolume`
危険度、遭遇可能な敵グループ、および遭遇確率を定義します。エンカウントボリュームは `EncountList` でマップに関連付けられます。マップは危険度が変化し得るため、使用されるエンカウントボリュームも変わる可能性があります。

### (**) `EncountList`
OT0 のランダムエンカウント規則テーブルです。どのマップでどのエンカウントボリュームが発生し得るか（`m_EncountVolume` は `m_EncountVolume.m_id`）、エンカウントまでの最小歩数（`m_EncountStepMin`）、および発生条件を指定します。

その他のキー：
- `m_DisableCondition`：このエンカウントを無効化するための条件
- `m_RandomStepA` と `m_RandomStepB`：ランダム歩数範囲／カーブ用パラメータ。OT0/CotC とも両キーが常に同値に見えます
- `m_FirstEncountStepRatio`：マップ／ゾーン侵入後の「最初の遭遇」に対する倍率と思われます（通常より早く／遅くする）

### (*) `EncountEffectTypeID`
各種エンカウント演出を管理します。`m_SeId` は、この演出が発生するマップを設定している可能性があります。

### (*) `EnemyGroupsByCondition`
特定条件を満たしたときに出現させる敵グループのセットです。「全てを授けし者」第7章で、指輪持ちと共に Accurst Flame を出現させる用途で使われています。

### (**) `SymbolEnemyList`
フィールド上のシンボル（強敵）に割り当てるオーバーワールドのスプライト／オブジェクト、および敵グループとレベルを指定します。
- `m_GroupId`：このシンボル敵エントリに対応する敵グループ
- `m_Index`：敵グループ内でのこの敵のインデックス。同じ GroupId／同じスポーンコントローラを複数のシンボル敵が共有する場合によく使われます
- `m_Dir`：シンボル敵がデフォルトで向いている方向
- `m_ObjectId`：シンボル敵のフィールド表示を特定オブジェクトで上書き（例：「力を授けし者」第3章のタトゥロックの船）
- `m_RepopCount`：リポップ（再出現）回数／頻度。OT0 の敵ではデフォルト 0。CotC ではそうではなく、（マップ再読み込み後も）敵が即座に再出現しません
- `m_Redrawing`：OT0/CotC とも常に 0
- `m_TargetLevel1` と `m_TargetLevel2`：シンボル敵のレベル範囲の可能性
- `m_Scale`：フィールド上の敵シンボルのスケール倍率（表示／当たり判定サイズとしての意味）

### (**) `SymbolEnemyGroupList`
`SymbolEnemyList` テーブルのシンボル敵 ID（`m_id`）を、特定マップ上の特定スポットに対応する内部 ID に割り当てているように見えます。`m_SymbolEnemyId` 配列は、`m_StartSymbolEnemyId` のシンボル敵を倒した後のリポップ時に、どのシンボル敵がその場所に出現するかを決めており、各シンボル敵の確率が `m_probability` に指定されている可能性があります。ただし CotC/OT0 とも、これらの配列を使用しているようには見えません。

## `Battle/` 配下
### (***) `BattleCameraParams`
戦闘カメラのプリセット／設定に対応する ID 群です。値 `-1.0` はゲーム側で「デフォルトを使用」と解釈されるようです。
- `m_PosotionX/Y/Z`：戦闘カメラの 3D 空間上の位置
- `m_RotationX/Y`：カメラの回転／ティルト（X/Y）
- `m_FOV`：視野角。狭いほどズーム（望遠）になります。FOV を変えてもパーティ位置は固定である点に注意してください
- `m_SequencerPath`：シネマティック／Sequencer バインディング。戦闘中のカメラカット／ターゲット用の Level Sequence もしくは Sequencer 駆動カメラリグを参照している可能性があります
- `m_FocalDistance`, `m_FocusDistance`：フォーカス面までの距離（片方が旧名称、片方が新名称の可能性（CotC には `m_FocalDistance` のみ）、または別系統のシステム）。OT0 では常に `-1.0`、CotC では異なる焦点距離が使われます
- `m_FocalRegion`：ピントが合う領域サイズ
- `m_NearBlurSize` と `m_FarBlurSize`：距離に応じたブラー強度
- `m_MaxBokehSize`：ボケ量の上限
- `m_Scale`：カメラ距離スケール／ショットスケール上書きの可能性

### (***) `BattleCameraSet`
敵グループに割り当てるカメラ設定のセットです（`BattleCameraSet.m_id`=`EnemyGroups.m_Camera`）。CotC には存在しません（CotC は戦闘で中立的なカメラ設定を1つだけ使用）。

### (***) `BattlePositions`
`EnemyFormations` や `EnemyReinforcements` 等で使用される PositionID を定義します。`m_Priority` は、座標が競合した場合にどちらのスプライトが前に来るか、あるいは敵の選択順を決める可能性があります。

### (***) `BattleEventList`
`EnemyGroups.m_EventIndices` で敵グループに割り当てられるイベントと思われます。
- `m_IsMustPlay`：イベントがスキップ可能かどうか（？）
- `m_EventCommand`：発火時に実行するコマンドスクリプト ID（`BattleEventCommand` を参照）
- `m_EventConditions`：条件タイプ ID（他ファイルの `m_Conditions` 配列（`TacticalList`、`SkillConditionList`、`TacticalActionList` 等）と同一の可能性）
- `m_EventParams`：条件用パラメータ
- `m_EventEnemies`：条件の適用先（どの敵スロット／どの敵インデックス／どのパーツか）を絞るための任意フィルタ（OT0/CotC とも未使用に見える）

### (***) `BattleEventCommand`
戦闘イベントのシーケンス内で実行されるスクリプト手順を保持します。`BattleEventList` が「どの条件で何を走らせるか」を記述するのに対し、`BattleEventCommand` は「実際の手順」（会話表示、アニメ再生、エフェクト生成、敵テクスチャ差し替え、BGM 変更等）を実行するように見えます。

- `m_NextWait`：このコマンド実行後、次へ進むまでの待機（秒）
- `m_isWaitSkillTime`：true の場合、スキルアニメーション終了まで待つ
- `m_isSwitchActionSkip`：アクションシーケンスのスキップを許可／強制する
- `m_PlayBgmBlock`：何かが完了するまで BGM 変更をブロックする可能性
- `m_PerformerID`：パーティ／ゲスト／敵の performer リストへのインデックス、または特別 ID の可能性
- `m_PerformerIsLeader`：パーティリーダーを performer として使用
- `m_PerformerEnemy`：0 以外の場合 performer が敵であることを示す（`EnemyID.m_id` を入力に取っているように見える）
- `m_TargetID`：強制アクション／スキルのターゲット指定先と思われる
- `m_EnableTarget`,`m_EnableAction`：このコマンド中／後にプレイヤーのターゲット指定／行動を有効化するトグル
- `m_NameID`：話者名のローカライズ ID（`GameTextCharacter` 配下）
- `m_TextID`：台詞のローカライズ ID（`GameTextEvent` 配下）
- `m_BalloonType`：吹き出しタイプ（1 = 通常の吹き出しと思われる）
- `m_NoTail`：吹き出しのしっぽ無し
- `m_BaloonOffsetX/Y`：performer から見た吹き出し位置オフセット
- `m_NoName`：true の場合、話者名を非表示
- `m_NarrationID`：`NarrationList.m_id` の ID を取る
- - `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`：ナレーション表示系のキー
- `m_AnimID`：会話中（または直前／直後）に performer に再生させるアニメーション
- `m_DirID`：performer の向き／方向プリセット
- `m_isRewriteIdleMotion`：コマンド後もアイドル上書きを維持
- `m_SoundID`：サウンド再生（おそらく `SoundList` の ID）
- `m_SoundHandle`：実行時ハンドルスロットの可能性（例：SFX を開始してハンドルを保持し、後続コマンドで停止／置換する等）(?)
- `m_EffectID`：エフェクト生成（おそらく `EffectList` の ID）
- `m_EffectHandle`：後続操作／停止のための実行時ハンドルスロットの可能性（?）
- `m_EffectPosition`：エフェクト生成位置（performer、target、画面等）
- `m_BgmID`：BGM 変更（同じく `SoundList` の ID と思われる）
- `m_FadeTime`：BGM 変更のフェード時間
- `m_IsRemoveAllAilments`：状態異常をクリア（フェーズ移行等に使用？）
- `m_EnableRetire`, `m_DisableGameOver`：（未検証）演出戦闘中の UI オプション／敗北状態を強制的に有効／無効にする（？）
- `m_LastBattleMapColor`：戦闘背景の色味（ティント）制御の可能性
- `m_TextureChangeEnemyIdx`：どの敵スロットのテクスチャを変更するか（`-1` は未使用）
- `m_ChangeEnemyTextureIdx`：どのテクスチャインデックス／スロットへ切り替えるか（`-1` は未使用）
- `m_TextureChangeTime`：テクスチャ切替タイミング（ディレイまたはクロスフェード時間。未検証）
- `m_EnemyPartsIndex`：影響を受ける敵パーツのインデックス
- `m_EnemyPartsDisplay`：参照した敵パーツをイベント中に表示／非表示
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`：`TacticalList` にも存在。戦闘エンジンはこれらのフラグをもとにスクリプト進行を管理している可能性があります（暗黙の「次」を表す）。例：「双世界の影」では、ルシアンとリブラックが両方ブレイクした際にシャドウの弱点が解放される戦術が発生したかどうかを追跡し、その後ルシアンとリブラックがブレイク復帰した際に再ロックするために使用されます（その状況に特化した条件が存在しないように見えるため）
- `m_JoinTrigger`：戦闘中参加（例：ゲストが途中参加）に関するイベントに紐付く可能性
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`：一般的な構造に当てはまらない特殊コマンド種別

### (*) `BattlePlaybackData` と `BattlePlayback`
OT0 の戦闘プレビュー／戦闘デモ用設定のように見えます。「プレビュー設定 X では、敵 Y にスキル Z を実行させ、任意で攻撃エフェクト表示を ON/OFF する」といったことをゲームに伝える小規模 DB です。

#### `BattlePlaybacks`
構造は概ね：`PlaybackID` → `SkillID`（+ performer スロット上書き）+ VFX 表示トグル
- `m_EnemySkillID`：再生する（敵の）スキル（ただしスキルは全て同一ファイル `SkillID` に格納）
- `m_SkillPlayEnemyIndex`：プレビュー中にそのスキルを実行する敵スロット／インデックス（`-1`=「自動／デフォルト」）
- `m_VisibleAttackEffect`：プレビュー中に「攻撃エフェクト」レイヤーを表示するかどうか（例：斬撃／衝撃オーバーレイ、ヒットフラッシュ、追加 VFX を表示／非表示にして、アニメを「クリーン」または「フル」でプレビューできるようにする）

#### `BattlePlaybackData`
実行時データではない可能性があります。`BattlePlaybacks` と似たレイアウトですが、各エントリに対して次のような奇妙な行があります：
```
"m_EnemySkillID_01": "m_EnemySkillID_01"
```
これは以下のいずれかかもしれません：
1. FModel が紛らわしい形でダンプしたスキーマ／テンプレート／struct デフォルト（例：そのフィールドが FName で、直列化が奇妙に見えるデフォルトオブジェクト／struct）
2. 旧来の「複数スロット」テーブル（`m_EnemySkillID_01`、`m_EnemySkillID_02`…のようなフィールドを持つ想定）で、ダンプ／エクスポートが値を破損させた
3. `m_EnemySkillID_01` が実際には別テーブルへの行ハンドル／キー（FName）であるべきだが、ダンプが格納値ではなくプロパティ名を代入してしまっている
**注意：** Switch 版ではこのテーブルの `m_SkillPlayEnemyIndex` 値が異なります。Steam のデモ版では一部エントリ自体が欠けています。

### (*) `BattleResultBonus`
戦闘結果ボーナス（「ノーダメージ」「オーバーキル」「ブレイク」「1ターン撃破」等）の倍率とローカライズ ID を管理します。`m_Param` はどのステータス（EXP／JP／リーフ）にボーナスが適用されるかを示しているようです：
- 0：リーフ
- 1：JP
- 2：EXP
2 を超える未使用パラメータが存在するかは未検証です。

### (**) `BattleVoiceList` と `BattleVoiceArenaList`
戦闘中にパーティメンバーまたはナレーターが再生する各種ボイス台詞を定義します（例：オルガルデラ戦の特殊ボイス）。

### (***) `BattleAbortConditions`
OT0 の戦闘終了／強制結果条件テーブルです。各エントリは小さな述語（predicate）の束を定義し、戦闘中にそれらが満たされると、戦闘が中断（早期終了）されるか、`EnemyGroups` 側で参照しているフィールドに応じて「強制勝利／強制敗北」として扱われます。
- `m_Conditions`：条件タイプ ID（`TacticalList`、`TacticalActionList`、`SkillConditionList`、`BattleEventList` と同一の可能性）
- `m_Params`：各条件のパラメータ（意味は条件タイプに依存）
- 特定の条件タイプでのみ意味を持つフィルタ：
   - `m_AilmentTypes`（`SkillAilmentType.m_id` を入力に取る）
   - `m_StatusTypes`（`CharaStatusID.m_id` を入力に取る可能性）
   - `m_WeaponTypes`（`WeaponType.m_id` を入力に取る）
   - `m_MagicTypes`（`Magictype.m_id` を入力に取る）
   - `m_EnemyID`（`EnemyID.m_id` を入力に取る）

### (*) `BattleActionID`
「アクションID」（おそらく `SkillAilmentType.m_ActionID`）を、具体的な戦闘アニメーション／モーションにマッピングする小さな参照テーブルです。また、そのアニメーションを標準の戦闘リグではなく NPC／フィールドキャラ用リグで再生するかどうかを示すフラグも持ちます。`m_UseNPC` は、そのアクションを通常の戦闘スプライトではなく NPC キャラのアニメセットで再生するかどうかを決める可能性があります。

### (*) `BattleParams`
戦闘関連パラメータ群です。意味は Dumper-7 SDK の `Kingship_structs.hpp`（BravelyPath Modular Discord の #file-dump を参照）によれば次の通りです：
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
「Death Blow（必殺技）」ゲージの増加量を決める参照テーブルです。各行は、あるチェック条件（タイプ + 値）を満たした場合に、Death Blow ゲージをどれだけ増加させるかを定義します。
- `m_CheckType`：何をチェックするか
- `m_CheckValue`：そのチェックの閾値／量。カウントのように見えます
- `m_IncreasedPoint`：条件一致時に加算する「Death Blow ポイント」量

### (*) `BattleLayoutSet`
戦闘のステージング／レイアウト用プリセット小テーブルと思われます。カメラプリセットと敵側の配置を結び付けます。ゲーム内でどこに使われているかは不明です（例えばフォーメーションや増援は `PositionID` 270 を使用しているように見えません）。

## `AIBattle` 配下
これらのファイルは、敵 AI の挙動と、敵が使用するスキル（およびその使い方）を司ります。

### (****) `TacticalList`、`TacticalAssignList`、`TacticalSkillList`
OT0 の敵 AI における「ルール句（rule clause）」テーブルです。各エントリは条件セットを定義し、それが満たされたときに AI が何らかの挙動を選択／有効化します。敵には `TacticalAssignList` によってこれらの「ルール」が割り当てられ（その `m_id` が `EnemyID.m_TacticalAssignID` に対応）、戦闘スキルは `TacticalSkillList` によって割り当てられます（その `m_id` が `EnemyID.m_SkillsID` に対応）。
- `m_Presage`：この戦術が「予兆（テレグラフ表示）」を出すかどうかを示す boolean/enum の可能性
- `m_PresageSkillID`：予兆に対応するスキル ID（何が予告されているか）。例として、ボスがブーストモードを宣言する際に使用されます（このスキル ID は敵の `TacticalSkillList.m_UseSkills` 配列には **存在しない** ことが特徴です）
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`：イベントフラグによるゲートと副作用。`BattleEventCommand` 参照。AI の分岐／シーケンス制御に使われている可能性があります
- `m_Conditions`：`TacticalActionList`、`BattleEventList`、`SkillConditionList` と同一の条件で、`m_Params` と各種フィルタ配列も同様である可能性
   - `m_AilmentTypes`（`SkillAilmentType.m_id` を入力に取る）
   - `m_StatusTypes`（`CharaStatusID.m_id` を入力に取る可能性）
   - `m_WeaponTypes`（`WeaponType.m_id` を入力に取る）
   - `m_MagicTypes`（`Magictype.m_id` を入力に取る）
各条件の意味はまだ厳密に解明できていません。以下のリソースが役立つかもしれません：
https://github.com/veganprimate/CotCPort/tree/main/Conditions/Conditions_Datamine_JP

その他：
- 28 は OT0 専用の条件です
- 条件タイプ ID は OT0 と CotC で同一に見えますが、CotC 側にのみ存在し OT0 にないものがある可能性があります
- 10000 以上の ID は、アクション実行順に関係していることが多いように見えます：
  - Condition 10100 は、敵のブーストモードを予告するスキルでよく使われます
  - Condition 10304 は、他条件が満たされた「その瞬間」に発火します（例：ルシアンとリブラックが同時にブレイクした直後にシャドウのヴェールが解除される。Condition 718 と `m_params` は `EnemyGroup` エントリ内のそれぞれのインデックス）
  - 一方 Condition 10301 は、ヴェール解除が「次のターン」まで遅延する挙動になります
  - 10711 は、ターン開始時にアクションを発生させるように見えます（例：ルシアンとリブラックが最初のブレイクから復帰した後にシャドウが使用）

### (****) `TacticalActionList`
戦術が適用されたときにゲームが「実際に何をするか」を指示するように見えます。どのスキルを使うか（インデックス指定）、誰を狙うか、複数ターゲット候補の選び方、追加条件／優先規則などを定義します。
- `m_SkillIndex`：これは SkillID そのものではありません。スキルリスト／配列へのインデックスです（テストより：敵の `m_SkillsID` に対応する `TacticalSkillList.m_UseSkills[]`）
- `m_SelectType`：ターゲット選択モード enum、またはスキルインデックス等の選び方に関係するもの。テストでは、関連スキルを発動させるには `m_SelectType` を 3 にする必要がありました（`SelectType` 1 だと最初（？）のスキルに固定された）
- `m_FriendlyIndex`：「味方側」（敵視点）で誰を狙うか。ここが `-1` の場合は「固定の味方なし。`SelectType` に基づいて選ぶ」等を意味する可能性があります
- `m_PrioritySkill`：設定されている場合、通常の選択を上書きして特定のスキルインデックスを強制する（またはそのスキルを優先扱いにする）可能性があります
- `m_TurnCount`：指定戦術 ID の条件が満たされている限り、このキーで与えた値 `N` ごとにアクションを繰り返します
- `m_GroupIndex`：グルーピング用キー。フェーズ分け、1ターン当たりのグループ内アクション数制限、同一グループ内の複数敵の協調動作などに使われている可能性があります
- 追加のアクション単位条件（`TacticalList` と同様だが、指定アクションに固有）：`m_Conditions`, `m_Params`, `m_AilmentTypes`, `m_StatusTypes`, `m_WeaponTypes`, `m_MagicTypes`
