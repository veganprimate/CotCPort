## **LANGUAGE:**  [English](customskls_en.md) | [日本語](customskills_ja.md) | [中文](customskills_zh.md) | [한국어](customskills_ko.md)

# スキルおよび関連システムの追加・変更方法

このページは、`Skill/` フォルダとそれに直接データを供給するすべてのテーブルを対象にした、OT0優先のリバースエンジニアリングガイドです。可能な限り、**OT0 のデータベースと SDK で確認できる事項**、**『大陸の覇者』側のスキーマでのみ確認できる事項**、**観察はできるが SDK ダンプでは完全に命名されていない事項**、そして**ゲーム内で実証済みの事項**を区別して記述します。

- **実証済み** は、ゲーム内テストによって、その関係または機能がここで述べる意味を持つと実証されていることを意味します。
- **確認済み** は、エクスポートされた行レイアウト、OT0 SDK の構造体名または列挙型名、そして/または対象テーブルとの正確な ID 一致によって、その関係が裏付けられていることを意味します。
- **観察済み** は、行の使用パターンと名前がその意味を強く裏付けているものの、OT0 の SDK ダンプには完全に適切な名前の列挙型や呼び出し元が現れていないことを意味します。
- **未解決** は、そのフィールドが実在し、一部のコンテンツではおそらく重要である一方で、OT0 がその挙動を自信を持って命名できるだけの直接証拠を示しておらず、私自身もゲーム内テストをしていないか、変化を確認していないことを意味します。

## OT0 とモバイル版 `Skill/` フォルダの概要

| 項目 | OT0 | モバイル版（大陸の覇者） |
| --- | --- | --- |
| コア行数 | `SkillID 7330`、`SkillAvailID 7369`、`SkillEffectiveID 4381`、`SkillAilmentType 490` | `SkillID 23085`、`SkillAvailID 28023`、`SkillEffectiveID 11434`、`SkillAilmentType 968` |
| `Skill/` 配下の OT0 専用テーブル | `WeaponType`、`SkillCalcType`、`SkillAvailMagnificationList`、`SkillSupplyItemGroup`、`SkillSupplyItemList`、`SkillTargetShopList`、`SkillTargetFarmItemList` | モバイル版エクスポートには存在しない |
| `Skill/` 配下のモバイル版専用テーブル | 原版 OT0 には存在しない | `ClassSkillConditionList`、`SkillEffectiveLinkageList`、`SkillFilterCategory`、`SkillFilterType`、`SkillModifyType`、`SpecialSkillExtendCategory`、`SpecialSkillExtendType`、`SpecialSkillExtendList` |
| `SkillEffectiveID` の演出スキーマ | 行内の `m_Cameras` 配列と `m_CameraTimes` 配列を保持する | それらの行内カメラ配列を廃し、連携、UI 表示制御、カメラシェイク、魔石関連フィールドを追加する |
| `SkillVisitorList` の結び付き | `m_EnemyID` を直接保持する | 代わりに `m_EnemyType` を保持する |
| `SpecialSkillGrowthList` | `m_DisplayPriority` を持つ比較的コンパクトな成長行 | ゲージ、報酬、Avail 値、状態異常値、ダメージ上限列まで含む、より大きな成長行 |
| `SkillID` の重点 | フィールド使用フラグ、直接音声フック、OT0 独自の武器差し替えヘルパーを保持する | フィルタ/カテゴリ振り分け、ブースト表示バリアント、自動戦闘寄りの制御情報を追加する |
| `SkillAvailID` の重点 | 多数の休眠共有フィールドに加え、OT0 独自の村/増援/弱点ヘルパーを持つ | タグ数、ダメージ上限、追加弱点、回避無視、耐性操作チャンネルを積極的に使う |

- OT0 の `Skill/` は、シングルプレイ本編の戦闘フロー、前衛/後衛の仕組み、村管理システムにかなり近い構造です。
- モバイル版の `Skill/` エクスポートは、追加フィルタ UI、追加進行レイヤー、追加ダメージ上限ロジック、追加弱点システム、そして自動化された表示/制御経路向けのメタデータを抱えた、ライブサービス寄りの戦闘スタックに近い構造です。
- 片方の作品にテーブルが存在しないからといって、その列挙型やランタイム概念まで消えたと決めつけてはいけません。ランタイム列挙型自体は残っていても、データテーブルをエクスポートしているのが片方だけ、という箇所がいくつもあります。

## ランタイムモデル

OT0 のほぼすべてのアクティブスキルに対して、ランタイムチェーンは次のとおりです。

`SkillID` -> `SkillAvailID` + `SkillEffectiveID`

- `SkillID` はルート行です。名前/詳細テキスト、カテゴリ、コスト、アイコン、ブーストファミリー、およびゲームプレイと演出ステージの配列を持ちます。
- `SkillAvailID` はゲームプレイペイロードです。ターゲット選択、ダメージ/回復/バフ/デバフ/蘇生/弱点変更/増援/召喚ロジック、持続時間、回数、そして二次メカニクスを持ちます。
- `SkillEffectiveID` は演出ペイロードです。戦闘アクション、カメラ、エフェクト、サウンド、ボイス、フローティングテキスト、可視性変更を持ちます。各 avail には `SkillEffectiveID` から 1 つの ID が割り当てられます。

状態異常は、4 番目の必須段階ではなく並列サブシステムです。スキルがバフ、デバフ、状態異常、反撃、インターセプト、リレイズ、ダメージ上限オーラ、または類似の状態を付与する場合、通常の経路は `SkillAvailID` が 1 つ以上の `SkillAilmentType` 行を指定することです。

## よくある誤解

- **実証済み:** `SkillAvailID.m_ResistID` は **`Skill/SkillResistList.m_id` ではありません**。OT0 では非ゼロ値が `Enemy/EnemyWeakID.m_id` と正確に一致し、その対象行の形状こそが弱点テーブル差し替えとして実際に意味を持ちます。具体的な証拠: `SkillAvailID#40085` と `SkillAvailID#73697` はどちらも `m_ResistID = 6527` を使います。`EnemyWeakID#6527` は存在しますが、`SkillResistList#6527` は存在しません。
- **確認済み:** `SkillAvailID.m_SkillAvailMagnificationCondition` と `m_SkillAvailMagnificationConditionArray` は、`SkillAvailMagnificationConditionList` への行 ID のように振る舞います。
- **確認済み:** OT0 の `SkillAvailID.m_SkillAvailMagnification` と `m_SkillAvailMagnificationArray` は、`SkillAvailMagnificationList.m_id` のようには振る舞いません。OT0 は `110`、`130`、`150`、`180`、`200` のような直接値を使い、配列値は `300` まで現れます。具体例: `Surprise Attack` の `SkillAvailID#74940` は `110/130/150/170/200` を直接使い、`Ulti-meow` の `SkillAvailID#76297` は `300` まで上がります。
- **確認済み:** `SkillID.m_SkillIconID` は `Skill/SkillIconList` ではなく、`Texture/TextureID` を直接指します。`SkillIconList` 自体は実在しますが、これは**別個の**ペアアイコンバンクです。

## 主要な外部キー形式フィールドの証明行列

- `SkillID.m_AttachEffect` -> `Effect/AttachEffect.m_id`: `10/10` のゼロ以外の一意の OT0 値が一致しました。
- `SkillID.m_SkillIconID` -> `Texture/TextureID.m_id`: `24/24` のゼロ以外の一意の OT0 値が一致しました。
- `SkillID.m_VoiceID` -> `Sound/PartVoiceID.m_id`: `25/25` のゼロ以外の固有の OT0 値が一致しました。
- `SkillID.m_WeaponIconType` -> `Skill/WeaponType.m_id`: `9/9` のゼロ以外の一意の OT0 値が一致しました。
- `SkillID.m_MagicIconType` -> `Skill/MagicType.m_id`: `7/7` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_InvokeCondition` -> `Skill/SkillConditionList.m_id`: `37/37` のゼロ以外の固有の OT0 値が一致しました。
- `SkillAvailID.m_ModifyStatus` -> `Character/CharaStatusID.m_id`: `17/17` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_HitTypes[*]` -> `Skill/SkillHitRateList.m_id`: `26/26` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_ResistID` -> `Enemy/EnemyWeakID.m_id`: `32/32` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_ResistAilmentID` -> `Skill/SkillResistAilmentID.m_id`: `26/26` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_WeakChangeID` -> `Enemy/EnemyWeakChangeID.m_id`: `71/71` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_WeakLockID` -> `Enemy/EnemyWeakLockID.m_id`: `64/64` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_Reinforcement` -> `Enemy/EnemyReinforcements.m_id`: `59/60` のゼロ以外の一意の OT0 値が一致しました。 1 つの外れ値は値 `140` で、`SkillID#900504` から `SkillAvailID#74111` によって使用されます。
- `SkillAvailID.m_ModeChangeID` -> `Enemy/EnemyModeID.m_id`: `28/28` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_SummonGuestID` -> `Character/CharaGuest.m_id`: `1/1` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_OverrideWeapon` -> `Skill/WeaponType.m_id`: `8/8` のゼロ以外の固有の OT0 値が一致しました。
- `SkillAvailID.m_OverrideMagic` -> `Skill/MagicType.m_id`: `7/7` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_SkillAvailMagnificationCondition` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `9/9` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAvailID.m_SkillAvailMagnificationConditionArray[*]` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `28/28` のゼロ以外の一意の OT0 値が一致しました。
- `SkillEffectiveID.m_Cameras[*]` -> `Battle/BattleCameraParams.m_id`: `47/47` のゼロ以外の一意の OT0 値が一致しました。
- `SkillEffectiveID.m_Animations[*]` -> `Battle/BattleActionID.m_id`: `92/92` のゼロ以外の一意の OT0 値が一致しました。
- `SkillEffectiveID.m_Effects[*]` -> `Effect/EffectList.m_id`: `712/712` のゼロ以外の一意の OT0 値が一致しました。
- `SkillEffectiveID.m_Sounds[*]` -> `Sound/SoundList.m_id`: `259/259` のゼロ以外の固有の OT0 値が一致しました。
- `SkillEffectiveID.m_Voices1[*]` および `m_Voices2[*]` -> `Sound/PartVoiceID.m_id`: ゼロ以外のすべての OT0 値が一致しました。
- `SkillAilmentType.m_ActionID` -> `Battle/BattleActionID.m_id`: `2/2` のゼロ以外の一意の OT0 値が一致しました。
- `SkillAilmentType.m_TargetStatus` および `m_AbsorbHealStatus` -> `Character/CharaStatusID.m_id`: ゼロ以外のすべての OT0 値が一致しました。
- `SkillAilmentType.m_IconTexID` および `m_IconSptTexID` -> `Texture/TextureID.m_id`: ゼロ以外のすべての OT0 値が一致しました。
- `SkillAilmentType.m_AddEffect`、`m_CharacterEffect`、`m_FieldEffect`、`m_InvokeEffect` -> `Effect/EffectList.m_id`: ゼロ以外のすべての OT0 値が一致しました。
- `SkillAilmentType.m_AddEffectSound`、`m_InvokeSound` -> `Sound/SoundList.m_id`: ゼロ以外のすべての OT0 値が一致しました。
- `WeaponType.m_EquipType` -> `Item/ItemType.m_id`: `8/8` のゼロ以外の一意の OT0 値が一致しました。
- `WeaponType.m_AnimationSet` -> `Character/WeaponAnimationSet.m_id`: `8/8` のゼロ以外の一意の OT0 値が一致しました。
- `WeaponType.m_WeakIconTexID`、`WeaponType.m_SkillIconTexID`、`MagicType.m_WeakIconTexID`、`MagicType.m_SkillIconTexID`、`SkillUniqueIconID.m_SkillIconTexID`、および `SkillIconList` テクスチャ フィールドはすべて、`Texture/TextureID.m_id` と完全に一致します。

## 多言語クロスチェック

- OT0 は日本語をベーステキストバンクとして保持し、ローカライズ済みテキストを `GameText/Localize/*` に置きます。たとえば、`SkillID` の名前/詳細テキストは JP/EN/ZH-CN/KO-KR 全体で問題なく解決されます。`153236` は `復活手当` / `Vivify` / `复活处理` / `부활처치` であり、`153376` は対応する蘇生説明です。
- 状態異常 UI テキストについては、`SkillAilmentType` は主に `GameTextSkill` ではなく `GameTextUI` を指します。例: `62327` は `毒` / `Poison` / `中毒` / `독` であり、`96365` は状態異常の説明文字列です。

## `Skill/` フォルダの読み方

- `Skill/` フォルダには、**実行時行** (`SkillID`、`SkillAvailID`、`SkillEffectiveID`、`SkillAilmentType`)、**ルックアップ凡例** (`WeaponType`、`MagicType`、`ResistType`、`SkillCategory`、`SkillCalcType`)、そして **ヘルパー ペイロード** (`SkillHitRateList`、`SkillResistAilmentID`、`SkillTargetShopList` など) という、性質の大きく異なる 3 種類のテーブルが混在しています。
- `...ID` という名前のすべての整数フィールドを、別の `Skill/` テーブルの外部キーとして扱わないでください。 OT0 は、多くの直接列挙型、いくつかのインライン パーセンテージ配列、およびいくつかのクロスフォルダー行 ID を使用します。
- 共有の `SkillAvailID`、`SkillEffectiveID`、または `SkillAilmentType` 行をその場で複製または編集すると、OT0 はペイロード行を頻繁に再利用するため、多くの無関係なスキルをサイレントに変更する可能性があります。

## このガイドで使用する OT0 SDK 行構造体の正確な引用

このセクションでは、OT0 SDK を `SDK/Kingship_structs.hpp` から直接引用し、ガイドが言い換えだけで終わらないようにしています。正確な row-struct 定義は長く、複数のテーブルスキーマを並べて比較したい読者も多いため、ここに集約しています。

### 以下の `Skill/` セクションで使用される正確な OT0 行構造引用符

#### `FMagicTypeBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14381`より引用。
```cpp
struct FMagicTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeakIconTexID;                                   // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconTexID;                                  // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FWeaponTypeBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:9814`より引用。
```cpp
struct FWeaponTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeakIconTexID;                                   // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconTexID;                                  // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EquipType;                                       // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AnimationSet;                                    // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FResistTypeBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:8760`より引用。
```cpp
struct FResistTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DamageRate;                                      // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillCategoryBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16239`より引用。
```cpp
struct FSkillCategoryBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_BuffDebuffLimit;                                 // 0x000C(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_D[0x3];                                        // 0x000D(0x0003)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillIDBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12824`より引用。
```cpp
struct FSkillIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Name;                                            // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Detail;                                          // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Job;                                             // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_BoostSkills;                                     // 0x0018(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_Category;                                        // 0x0028(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_RequireRatio;                                    // 0x002C(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_2D[0x3];                                       // 0x002D(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_RequireValue;                                    // 0x0030(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountLimit;                                      // 0x0034(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_UnableRecoverCount;                              // 0x0038(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_39[0x3];                                       // 0x0039(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_RequireBoost;                                    // 0x003C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeaponIconType;                                  // 0x0040(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MagicIconType;                                   // 0x0044(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsRandomReplace;                                 // 0x0048(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsWeaponSelect;                                  // 0x0049(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsReplaceNowSelectedWeapon;                      // 0x004A(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsReplaceMainWeapon;                             // 0x004B(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_4C[0x4];                                       // 0x004C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_WeaponReplaceSkill;                              // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Avails;                                          // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_BeginEffective;                                  // 0x0070(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_74[0x4];                                       // 0x0074(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Effectives;                                      // 0x0078(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_EndEffective;                                    // 0x0088(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AttachEffect;                                    // 0x008C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconID;                                     // 0x0090(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillUniqueIconID;                               // 0x0094(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CommandPhaseSkillIconID;                         // 0x0098(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_OverrideCommandName;                             // 0x009C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CommandCategory;                                 // 0x00A0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_BlockedSkill;                                    // 0x00A4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ReplaceCondition;                                // 0x00A8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_AC[0x4];                                       // 0x00AC(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ReplaceConditionArray;                           // 0x00B0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ReplaceSkill;                                    // 0x00C0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C4[0x4];                                       // 0x00C4(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ReplaceSkillArray;                               // 0x00C8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_DisableReplaceTag;                               // 0x00D8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_D9[0x3];                                       // 0x00D9(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_SpecialSkillInitialGaugeValue;                   // 0x00DC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SpecialSkillRegenerateValue;                     // 0x00E0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_RecommendCategory;                               // 0x00E4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_DuplicateAilment;                                // 0x00E8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_E9[0x3];                                       // 0x00E9(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AfterAction;                                     // 0x00EC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InitialChargeTrun;                               // 0x00F0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ReChargeTrun;                                    // 0x00F4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MaxBoostLv;                                      // 0x00F8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_FieldUseable;                                    // 0x00FC(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_FD[0x3];                                       // 0x00FD(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_VoiceID;                                         // 0x0100(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_104[0x4];                                      // 0x0104(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillAvailIDBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17289`より引用。
```cpp
struct FSkillAvailIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeTiming;                                    // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeCondition;                                 // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TargetType;                                      // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ModifyType;                                      // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ModifyStatus;                                    // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AvailTag;                                        // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_IsFoodAvail;                                     // 0x0030(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_31[0x7];                                       // 0x0031(0x0007)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_HitTypes;                                        // 0x0038(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_RandomChoiceCount;                               // 0x0048(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CalcType;                                        // 0x004C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Values;                                          // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_FluctuationValues;                               // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_SkillRatios;                                     // 0x0070(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Turns;                                           // 0x0080(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_OverwriteCount;                                  // 0x0090(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_91[0x7];                                       // 0x0091(0x0007)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Counts;                                          // 0x0098(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_OrderPriority;                                   // 0x00A8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_NextPriority;                                    // 0x00AC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TargetNextPriority;                              // 0x00B0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ShieldDamage;                                    // 0x00B4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IgnoreWeak;                                      // 0x00B8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_NoBreakable;                                     // 0x00B9(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_BA[0x2];                                       // 0x00BA(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_PhysicalHitRatio;                                // 0x00BC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CriticalRatio;                                   // 0x00C0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Suicide;                                         // 0x00C4(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_LotteryAilment;                                  // 0x00C5(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C6[0x2];                                       // 0x00C6(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AilmentInvokeCondition;                          // 0x00C8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AddAilmentPick;                                  // 0x00CC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AddAilment;                                      // 0x00D0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_CalcTypeAilment;                                 // 0x00E0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_ValueAilment;                                    // 0x00F0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_NotExtensionTarget;                              // 0x0100(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_101[0x3];                                      // 0x0101(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_WeakLockID;                                      // 0x0104(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DelayedSkill;                                    // 0x0108(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DelaySkillPriority;                              // 0x010C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SubAilmentPick;                                  // 0x0110(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_114[0x4];                                      // 0x0114(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SubAilment;                                      // 0x0118(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ResistID;                                        // 0x0128(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_12C[0x4];                                      // 0x012C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ResistAilment;                                   // 0x0130(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ResistAilmentID;                                 // 0x0140(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Reinforcement;                                   // 0x0144(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeakChangeID;                                    // 0x0148(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EnhancedSkillID;                                 // 0x014C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SummonGuestID;                                   // 0x0150(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_OverrideWeapon;                                  // 0x0154(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_OverrideMagic;                                   // 0x0158(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AppendType;                                      // 0x015C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillAvailMagnificationCondition;                // 0x0160(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_164[0x4];                                      // 0x0164(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SkillAvailMagnificationConditionArray;           // 0x0168(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_SkillAvailMagnification;                         // 0x0178(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_17C[0x4];                                      // 0x017C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SkillAvailMagnificationArray;                    // 0x0180(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_InvocationProbability;                           // 0x0190(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ModeChangeID;                                    // 0x0194(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountRefAilment;                                 // 0x0198(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountRefValue;                                   // 0x019C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountRefValueLimit;                              // 0x01A0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1A4[0x4];                                      // 0x01A4(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillConditionListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17406`より引用。
```cpp
struct FSkillConditionListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Conditions;                                      // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Params;                                          // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AilmentTypes;                                    // 0x0030(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_StatusTypes;                                     // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_WeaponTypes;                                     // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_MagicTypes;                                      // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Equipment;                                       // 0x0070(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_ORFlag;                                          // 0x0080(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_81[0x7];                                       // 0x0081(0x0007)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillAvailMagnificationConditionListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14869`より引用。
```cpp
struct FSkillAvailMagnificationConditionListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Conditions;                                      // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_IntParams;                                       // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AilmentParams;                                   // 0x0030(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_RaceParams;                                      // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Equipment;                                       // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillEffectiveIDBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17455`より引用。
```cpp
struct FSkillEffectiveIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Repeat;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_HoldTime;                                        // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_AvailTime;                                       // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Cameras;                                         // 0x0018(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_CameraTimes;                                     // 0x0028(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Animations;                                      // 0x0038(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_PlayTimes;                                       // 0x0048(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_PlayRates;                                       // 0x0058(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AnimationTarget;                                 // 0x0068(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AnimStartFrame;                                  // 0x0078(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AnimEndFrame;                                    // 0x0088(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_AnimNoIdle;                                      // 0x0098(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Effects;                                         // 0x00A8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_InvokeTimes;                                     // 0x00B8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_EffectLayout;                                    // 0x00C8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsH;                                        // 0x00D8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsV;                                        // 0x00E8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Sounds;                                          // 0x00F8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_SoundTimes;                                      // 0x0108(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_SoundToVoice;                                    // 0x0118(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Voices1;                                         // 0x0128(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Voices2;                                         // 0x0138(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Voices3;                                         // 0x0148(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_VoiceTimes;                                      // 0x0158(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Texts;                                           // 0x0168(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_TextTime;                                        // 0x0178(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_CharaVisibility;                                 // 0x0188(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_VisibilityTarget;                                // 0x0198(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_VisibilityStartTime;                             // 0x01A8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillAilmentTypeBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14674`より引用。
```cpp
struct FSkillAilmentTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	class FName                                   m_Label;                                           // 0x000C(0x0008)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Resist;                                          // 0x0014(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Minus;                                           // 0x0015(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Plus;                                            // 0x0016(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Removable;                                       // 0x0017(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Multiply;                                        // 0x0018(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverwriteTurn;                                   // 0x0019(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_ForceOverrideValue;                              // 0x001A(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_InvalidateDebuff;                                // 0x001B(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_CanInactiveFlag;                                 // 0x001C(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1D[0x3];                                       // 0x001D(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_InvokeTiming;                                    // 0x0020(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TargetStatus;                                    // 0x0024(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_TargetStatusArray;                               // 0x0028(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_AilmentCalc;                                     // 0x0038(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EfficacyStepRate;                                // 0x003C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_ResistWeapon;                                    // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_ResistMagic;                                     // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_RemoveAilment;                                   // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_TargetType;                                      // 0x0070(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_74[0x4];                                       // 0x0074(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_RemoveConditions;                                // 0x0078(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_RemoveParams;                                    // 0x0088(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ChildAilment;                                    // 0x0098(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x009C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ExplanationTextID;                               // 0x00A0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_IconTexID;                                       // 0x00A4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_IconSptTexID;                                    // 0x00A8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AddEffect;                                       // 0x00AC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CharacterEffect;                                 // 0x00B0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_FieldEffect;                                     // 0x00B4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeEffect;                                    // 0x00B8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OnGroundInvoke;                                  // 0x00BC(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_BD[0x3];                                       // 0x00BD(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AddEffectSound;                                  // 0x00C0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeSound;                                     // 0x00C4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ActionID;                                        // 0x00C8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ActionPriority;                                  // 0x00CC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ReplaceCharaID;                                  // 0x00D0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_CountDown;                                       // 0x00D4(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_NoCount;                                         // 0x00D5(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_D6[0x2];                                       // 0x00D6(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_StatusDetailSort;                                // 0x00D8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IgnoreLimit;                                     // 0x00DC(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_RemainDead;                                      // 0x00DD(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsNotMemorize;                                   // 0x00DE(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_DF[0x1];                                       // 0x00DF(0x0001)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AbsorbHealStatus;                                // 0x00E0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_NotRace;                                         // 0x00E4(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_E5[0x3];                                       // 0x00E5(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_EnemyRace;                                       // 0x00E8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_ForceSupportAbility;                             // 0x00F8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_ForceCommandAbility;                             // 0x00F9(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_FA[0x2];                                       // 0x00FA(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_WeaponAppendType;                                // 0x00FC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MagicAppendType;                                 // 0x0100(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_DuplicateAilment;                                // 0x0104(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_105[0x3];                                      // 0x0105(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_SkillReplace;                                    // 0x0108(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_10C[0x4];                                      // 0x010C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillAilmentOffsetsBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14518`より引用。
```cpp
struct FSkillAilmentOffsetsBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PoisonX;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PoisonY;                                         // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BleedX;                                          // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BleedY;                                          // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SilenceX;                                        // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SilenceY;                                        // 0x0020(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SleepX;                                          // 0x0024(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SleepY;                                          // 0x0028(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PalalysisX;                                      // 0x002C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PalalysisY;                                      // 0x0030(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_FearX;                                           // 0x0034(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_FearY;                                           // 0x0038(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_WasteX;                                          // 0x003C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_WasteY;                                          // 0x0040(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_DarknessX;                                       // 0x0044(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_DarknessY;                                       // 0x0048(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_HeadacheX;                                       // 0x004C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_HeadacheY;                                       // 0x0050(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_EnergyDrainX;                                    // 0x0054(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_EnergyDrainY;                                    // 0x0058(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_RegenerateX;                                     // 0x005C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_RegenerateY;                                     // 0x0060(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_ProvokeX;                                        // 0x0064(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_ProvokeY;                                        // 0x0068(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CounterX;                                        // 0x006C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CounterY;                                        // 0x0070(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_InterceptX;                                      // 0x0074(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_InterceptY;                                      // 0x0078(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PerfectInterceptScale;                           // 0x007C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BreakX;                                          // 0x0080(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BreakY;                                          // 0x0084(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CountDownX;                                      // 0x0088(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CountDownY;                                      // 0x008C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillAilmentSkillReplaceListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17264`より引用。
```cpp
struct FSkillAilmentSkillReplaceListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SkillList;                                       // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_AilmentType;                                     // 0x0020(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_24[0x4];                                       // 0x0024(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillResistListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12898`より引用。
```cpp
struct FSkillResistListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ResistWeapon;                                    // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_ResistMagic;                                     // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillResistAilmentIDBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17569`より引用。
```cpp
struct FSkillResistAilmentIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ResistAilments;                                  // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_ResistRate;                                      // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillHitRateListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17517`より引用。
```cpp
struct FSkillHitRateListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MinCount;                                        // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountAilment;                                    // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_14[0x4];                                       // 0x0014(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_HitRates;                                        // 0x0018(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_Shuffle;                                         // 0x0028(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_29[0x7];                                       // 0x0029(0x0007)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillIconListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16382`より引用。
```cpp
struct FSkillIconListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_BattleIconTexID;                                 // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MenuIconTexID;                                   // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_14[0x4];                                       // 0x0014(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillUniqueIconIDBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:13602`より引用。
```cpp
struct FSkillUniqueIconIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconTexID;                                  // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverrideCommandPanel;                            // 0x0014(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverrideInBattle;                                // 0x0015(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverrideBuff;                                    // 0x0016(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_17[0x1];                                       // 0x0017(0x0001)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillSupplyItemGroupBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17605`より引用。
```cpp
struct FSkillSupplyItemGroupBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillModifyType;                                 // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Lot;                                             // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillSupplyItemListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12910`より引用。
```cpp
struct FSkillSupplyItemListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_GroupId;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_RewardId;                                        // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ItemId;                                          // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Values;                                          // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Lot;                                             // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsUnique;                                        // 0x0020(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_21[0x7];                                       // 0x0021(0x0007)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillTargetShopListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12926`より引用。
```cpp
struct FSkillTargetShopListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ShopType;                                        // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MapId;                                           // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillTargetFarmItemListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17641`より引用。
```cpp
struct FSkillTargetFarmItemListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillModifyType;                                 // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_VillageFarmItemId;                               // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AdditionalVillageFarmItemId;                     // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1C[0x4];                                       // 0x001C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillVisitorListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17691`より引用。
```cpp
struct FSkillVisitorListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EnemyID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_EscortNum;                                       // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_EscortType;                                      // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsY;                                        // 0x0030(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsX;                                        // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_CallSE;                                          // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_IconID;                                          // 0x0060(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_64[0x4];                                       // 0x0064(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillEnhancedListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16345`より引用。
```cpp
struct FSkillEnhancedListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_EnhancedSkills;                                  // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSpecialSkillIDBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17824`より引用。
```cpp
struct FSpecialSkillIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSpecialSkillGrowthListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:13722`より引用。
```cpp
struct FSpecialSkillGrowthListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SpecialSkillID;                                  // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Level;                                           // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DisplayPriority;                                 // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1C[0x4];                                       // 0x001C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

## 完全な `Skill/` ファイル リファレンス

以下の各テーブル セクションには、依存する正確な OT0 SDK 列挙型の引用が埋め込まれています。すぐ上の集中セクションには、スキーマ レベルのクロスチェックのために正確な OT0 行構造の引用符が保持されます。

### `MagicType`
要素の凡例テーブル。 OT0 は、スキルや状態異常が図像や動作のオーバーライドのために要素ファミリーを必要とするあらゆる場所でそれを使用します。

- **OT0 行:** `9`。
- **『大陸の覇者』行数:** `8`。
- **OT0 SDK 行構造体:** `FMagicTypeBase`。
- **関連する OT0 SDK 列挙型:** `EMAGIC_TYPE`。
- `m_id` は、`SkillID.m_MagicIconType` および `SkillAvailID.m_OverrideMagic` に表示されるのと同じ要素キー ファミリです。

#### このテーブルで使用される正確な OT0 SDK 列挙型引用符
##### `EMAGIC_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:4295`より引用。
```cpp
enum class EMAGIC_TYPE : uint8
{
	MAGT_NONE                                = 0,
	MAGT_FIRE                                = 1,
	MAGT_ICE                                 = 2,
	MAGT_THUNDER                             = 3,
	MAGT_WIND                                = 4,
	MAGT_LIGHT                               = 5,
	MAGT_DARK                                = 6,
	MAGT_NAUGHT                              = 7,
	MAGT_POISON                              = 8,
	MAGT_MAX                                 = 9,
};
```
- `MagicType.m_id` は任意の行番号ではありません。 OT0 は、エクスポートされた JSON 行 ID `0..8` が上記のエンジン列挙型によって格納された生の値と同じになるように、このテーブルにキーを設定します。
- そのため、`SkillID.m_MagicIconType`、`SkillAvailID.m_OverrideMagic`、およびその他の要素ファミリー フィールドは、変換レイヤーなしで直接 SDK 列挙値として、および `MagicType` 行 ID として読み取ることができます。

#### フィールドごと
- `m_id`: **確認しました。** 直接要素ファミリー キー。 OT0 行 `0..8` は `EMAGIC_TYPE` に整列します (`NONE`、`FIRE`、`ICE`、`THUNDER`、`WIND`、`LIGHT`、`DARK`、`NAUGHT`、 `POISON`)。
- `m_TextID`: **確認済み。** `GameTextUI` のプレイヤー側要素名。例: OT0 `1 -> Fire`、`7 -> Non-Elemental`、`8 -> Poison`。
- `m_WeakIconTexID`: **確認済み。** この要素が弱点または耐性ファミリーとして表示されるときに使用される弱点パネル アイコンの `Texture/TextureID.m_id`。
- `m_SkillIconTexID`: **確認済み** この要素ファミリーのコマンド/スキル オーバーレイ アイコンの `Texture/TextureID.m_id`。

#### 『大陸の覇者』のみの追加フィールド
- `m_AdditionWeakIconTexID`: **『大陸の覇者』側のDBスキーマから確認** 大陸の覇者 の追加弱点システムで使用される追加の弱点アイコン テクスチャ。 OT0 には対応するフィールドがありません。

### `WeaponType`
武器ファミリーのレジェンドテーブル。これは、OT0 武器ラベル、武器アイコン、対応する装備タイプの行、および戦闘アニメーション ファミリの正規の場所です。

- **OT0 行:** `10`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** `FWeaponTypeBase`。
- **関連する OT0 SDK 列挙型:** `EWEAPON_TYPE`。
- `WeaponType` は単なる表面的なものではありません。 OT0 のスキル行、アイテム行、アニメーション行がすべてここに集まります。

#### このテーブルで使用される正確な OT0 SDK 列挙型引用符
##### `EWEAPON_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:7327`より引用。
```cpp
enum class EWEAPON_TYPE : uint8
{
	WPNT_NONE                                = 0,
	WPNT_SWORD                               = 1,
	WPNT_LANCE                               = 2,
	WPNT_DAGGER                              = 3,
	WPNT_AXE                                 = 4,
	WPNT_BOW                                 = 5,
	WPNT_ROD                                 = 6,
	WPNT_PAPERS                              = 7,
	WPNT_FAN                                 = 8,
	WPNT_NAUGHT                              = 9,
	WPNT_MAX                                 = 10,
};
```
- `WeaponType.m_id` も同様に、上記の生の SDK 列挙型にキー設定されます。エクスポートされた行は、エンジンの武器ファミリー定数のデータ テーブル ミラーです。
- これが、`SkillID.m_WeaponIconType`、`SkillAvailID.m_OverrideWeapon`、弱点グリッド、および武器アニメーション ルーティングがすべて、2 番目の再マッピング テーブルなしで同じ数値ファミリーを共有できる理由です。

#### フィールドごと
- `m_id`: **確認しました。** 武器ファミリーの直接キー。 OT0 行は `EWEAPON_TYPE` (`NONE`、`SWORD`、`LANCE`、`DAGGER`、`AXE`、`BOW`、`ROD`、`PAPERS`、`FAN`、 `NAUGHT`)。
- `m_TextID`: **確認済み。** `GameTextUI` のプレイヤー側の武器ファミリー名。例: `1 -> Sword`、`2 -> Polearm`、`7 -> Tome`、`8 -> Fan`。
- `m_WeakIconTexID`: **確認済み。** その武器ファミリーの弱点パネル アイコンの `Texture/TextureID.m_id`。
- `m_SkillIconTexID`: **確認済み** スキル UI で使用される武器オーバーレイ アイコンの `Texture/TextureID.m_id`。
- `m_EquipType`: **確認済み。** 一致する装備可能な武器ファミリー アイテム タイプの `Item/ItemType.m_id`。これが、OT0 行が武器列挙型の 2 番目のコピーではなく、`1`、`7`、`3`、`2`、`5`、`4`、`6`、`8` のようなアイテム タイプ ID を使用する理由です。
- `m_AnimationSet`: **確認済み** `Character/WeaponAnimationSet.m_id`。これは、スキルがその武器タイプとしてレンダリングされるときに戦闘アクションで使用されるアニメーション ファミリです。

### `ResistType`
OT0の弱点/耐性率の伝説。これは、`EnemyWeakID` などの弱点グリッド スタイル テーブルで使用されるパーセンテージ クラス テーブルです。

- **OT0 行:** `10`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** `FResistTypeBase`。
- この表は、`SkillAvailID.m_ResistID` を修正する必要がある理由です。 `EnemyWeakID` はスロットごとに `ResistType` ID を保存します。 `SkillResistList` は、代わりに直接パーセンテージ デルタを保存します。

#### フィールドごと
- `m_id`: **確認済み。** `Enemy/EnemyWeakID.m_ResistWeapon` および `m_ResistMagic` 内で使用される弱点クラスのキー。
- `m_DamageRate`: **確認済み。** その弱点クラスの実際のダメージ率パーセンテージ。 OT0 行は、`100` 通常、`130` 弱点、`50` レジスト、`0` null、`-100` 吸収、および中間状態 `90`、`85`、`80`、`75` にデコードされます。 `1`。

### `SkillCategory`
カテゴリの凡例テーブル。 The actual category number lives on `SkillID.m_Category`;このテーブルには、ランタイムがクエリできるカテゴリ レベルのブール値が 1 つ追加されます。

- **OT0 行:** `9`。
- **『大陸の覇者』行数:** `9`。
- **OT0 SDK 行構造体:** `FSkillCategoryBase`。
- **関連する OT0 SDK 列挙型:** `ESKILL_CATEGORY`。

#### このテーブルで使用される正確な OT0 SDK 列挙型引用符
##### `ESKILL_CATEGORY`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6344`より引用。
```cpp
enum class ESKILL_CATEGORY : uint8
{
	NONE                                     = 0,
	COMMAND                                  = 1,
	SUPPORT                                  = 2,
	WEAPON                                   = 3,
	ENEMYACTION                              = 4,
	ENEMYPASSIVE                             = 5,
	ENEMYEVENT                               = 6,
	MAXBOOST_COMMAND                         = 7,
	ITEM                                     = 8,
	ESKILL_MAX                               = 9,
};
```
- `SkillID.m_Category` は、上記の SDK 列挙型の生の `ESKILL_CATEGORY` 番号を保存します。 `SkillCategory.json` が存在するのは、OT0 がその列挙型の上に 1 つの追加のカテゴリごとのデータ フラグ (`m_BuffDebuffLimit`) を重ねたいためです。
- 言い換えれば、`SkillCategory` は無関係な外部テーブルではありません。これは、上で引用した正確なエンジン カテゴリ定数のデータに裏付けられた凡例です。

#### フィールドごと
- `m_id`: **確認しました。** 直接カテゴリ キー。 OT0 行は、`ESKILL_CATEGORY` (`COMMAND`、`SUPPORT`、`ENEMYACTION`、`ITEM` など) に揃えられます。
- `m_BuffDebuffLimit`: **観察されました。** バフ/デバフ キャップ動作のカテゴリレベルのブール値。 OT0 では、カテゴリ ID `1`、`3`、および `4` については true ですが、その他の場合は false であるため、すべてのカテゴリが同じ制限ルールに参加していると想定しないでください。

### `SkillCalcType`
純粋な列挙型凡例テーブル。 OT0 は、`m_id` のみを含む `Skill/SkillCalcType.json` を保持します。これは、`SkillAvailID.m_CalcType` で使用される計算モード列挙型を正確に反映します。

- **OT0 行:** `10`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** このリポジトリに同梱されている OT0 SDK ダンプには、一致する専用の行構造体が見つかりませんでした。
- **関連する OT0 SDK 列挙型:** `ESKILL_CALC_TYPE`。
- ここの OT0 SDK ダンプには専用の `FSkillCalcTypeBase` 行構造体は見つかりませんでしたが、OT0 JSON は依然としてクリーンな凡例テーブルです。

#### このテーブルで使用される正確な OT0 SDK 列挙型引用符
##### `ESKILL_CALC_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6327`より引用。
```cpp
enum class ESKILL_CALC_TYPE : uint8
{
	NONE                                     = 0,
	WEAPON                                   = 1,
	MAGIC                                    = 2,
	ADD_RATED                                = 3,
	ADD_FIXED                                = 4,
	ADD_NOW_RATED                            = 5,
	LAST_1                                   = 6,
	FEAR_LAST_1                              = 7,
	BP_SPECIAL_REGEN_RATE                    = 8,
	MAGIC_REFLECTION                         = 9,
	ESKILL_CALC_MAX                          = 10,
};
```
- `SkillAvailID.m_CalcType` は、これらの生の数値を直接保存します。したがって、`SkillCalcType.json` は、独立したゲームプレイ テーブルとしてではなく、SDK 列挙型のエクスポートされた凡例コピーとして読み取るのが最適です。
- これは、テーブルに `m_id` しかない理由も説明しています。セマンティック名は SDK 列挙型に存在しますが、JSON はデータ エクスポートで数値ドメインを表示したままにします。

#### フィールドごと
- `m_id`: **確認済み** 計算モード キーが `ESKILL_CALC_TYPE` と一致します (`NONE`、`WEAPON`、`MAGIC`、`ADD_RATED`、`ADD_FIXED`、`ADD_NOW_RATED`、`LAST_1`、`FEAR_LAST_1`、 `BP_SPECIAL_REGEN_RATE`、`MAGIC_REFLECTION`）。

### `SkillID`
ルートスキル列。スキルの複製時にテーブルを 1 つだけ変更する場合、最初に検査するのはこのテーブルですが、編集する必要がある唯一のテーブルであることはほとんどありません。

- **OT0 行:** `7330`。
- **『大陸の覇者』行数:** `23085`。
- **OT0 SDK 行構造体:** `FSkillIDBase`。
- **関連する OT0 SDK 列挙型:** `ESKILL_CATEGORY`、`ESKILL_RECOMEND_CATEGORY`。
- OT0 の使用量は敵側の行に大きく偏っています。カテゴリ `ENEMYACTION` のみがほとんどの行を占めています。プレーヤー コンテンツを作成するときは、任意の `SkillID` 行を選択するのではなく、プレーヤーに面している正しいファミリーから行を複製します。

#### この行で直接使用される正確な OT0 SDK 列挙型引用符
##### `ESKILL_RECOMEND_CATEGORY`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6655`より引用。
```cpp
enum class ESKILL_RECOMEND_CATEGORY : uint8
{
	NONE                                     = 0,
	ATTACK                                   = 1,
	SUPPORT                                  = 2,
	HEAL                                     = 3,
	NUM                                      = 4,
	ESKILL_RECOMEND_MAX                      = 5,
};
```
- `SkillID.m_Category` は、前の `SkillCategory` セクションに埋め込まれた正確な `ESKILL_CATEGORY` 引用符と一緒に読み取る必要があります。これは、スキル行には生の enum 値が格納され、`SkillCategory.json` はそのコンパニオン凡例テーブルであるためです。
- `SkillID.m_RecommendCategory` には、並列 `Skill/` 凡例ファイルが**ありません**。したがって、上記の正確な SDK 列挙型は、`ATTACK`、`SUPPORT`、`HEAL` などの値の信頼できる命名ソースとなります。

#### フィールドごと
- `m_id`: スキル行の主キー。
- `m_Name`: **確認しました。** スキル名のテキスト ID。ほとんどの OT0 行は `GameText/Localize/*/SystemText/GameTextSkill.json` を通じて解決されますが、より小さなサブセットは代わりに `GameTextUI` を通じて解決されます。表示される名前を確認する場合は、ローカライズされたテキスト バンクを使用します。
- `m_Detail`: **確認済み。** スキルの詳細/説明テキスト ID。 OT0 は、`GameTextUI` ではなく、`GameTextSkill` を通じてこれを圧倒的に解決します。
- `m_Job`: **確認済み。** 設定すると `Character/JobID.m_id`。 OT0 はここで 8 つの基本ジョブ ID のみを使用し、`44` 行でのみ使用するため、これはユニバーサル所有者ジョブ フィールドではありません**。
- `m_BoostSkills`: **確認済み。** ブースト ファミリ `[boost0, boost1, boost2, boost3]` の 4 つの `SkillID` 行の固定長配列。 OT0 では、通常、ブースト可能なファミリーの先頭行のみが設定されます。
- `m_Category`: **確認済み。** `ESKILL_CATEGORY` のスキル カテゴリ。これは、プレイヤー コマンド、サポート スキル、敵のアクション、敵のパッシブ、アイテム スキルを分離するために検査する必要がある最初のフィールドです。
- `m_RequireRatio`: **OT0 では未使用であることが確認されました。** SDK 構造体に存在しますが、OT0 スキル行では常に 0/false です。
- `m_RequireValue`: **観察されました。** コスト/バリュー ゲート。通常のプレイヤーのコマンドでは、これは表示される SP コストと一致します。非コマンドスキルの場合、その意味はカテゴリと呼び出し元によって異なります。
- `m_CountLimit`: **OT0 で未使用であることが確認されました。** 存在しますが、常にゼロです。
- `m_UnableRecoverCount`: **OT0 で未使用であることが確認されました。** 存在しますが、常に 0/false。
- `m_RequireBoost`: **OT0 で未使用であることが確認されました。** 存在しますが、常にゼロです。
- `m_WeaponIconType`: **確認済み** スキルとともに表示される武器アイコンのオーバーレイについては、`WeaponType.m_id` を直接指定してください。
- `m_MagicIconType`: **確認済み** スキルで表示される要素アイコンのオーバーレイについては、`MagicType.m_id` を直接指定してください。
- `m_IsRandomReplace`: **観察されました。** ランダムバリアント置換フラグ。 OT0 は、`Prism Mist` スタイルのスキルなど、少数のランダムな置換ファミリーでこれを使用します。
- `m_IsWeaponSelect`: **観察されました。** 武器形態選択フラグ。武器の選択または武器コンテキストのルーティングに応じて、表示されている 1 つのコマンドが異なる具体的なスキル行に解決される場合に使用されます。
- `m_IsReplaceNowSelectedWeapon`: **観察済み。** 交換には、固定ファミリー メンバーではなく、現在選択されている武器形式を使用する必要があります。
- `m_IsReplaceMainWeapon`: **観察されました。** 前のフィールドと似ていますが、メインの武器セットに偏っています。 OT0では珍しい。
- `m_WeaponReplaceSkill`: **観察されました。** 8 つの置換 `SkillID` 行の固定長プール。名前にもかかわらず、OT0 はこれを実際の武器形式の置き換えと一部のランダムなバリエーション ファミリの両方に使用します。
- `m_Avails`: **確認済み。** 最大 20 個の `SkillAvailID` ステージの固定長配列。これはスキルのゲームプレイ シーケンスです。ゼロスロットは単純にスキップされます。
- `m_BeginEffective`: **確認済み。** オプションのプレキャスト `SkillEffectiveID` ステージ。
- `m_Effectives`: **確認済み。** 最大 20 個の `SkillEffectiveID` ステージの固定長配列。これがスキルの提示シーケンスです。
- `m_EndEffective`: **OT0 で未使用であることが確認されました。** キャスト後の `SkillEffectiveID` フックが構造体に存在しますが、OT0 行では使用されていません。
- `m_AttachEffect`: **確認済み** `Effect/AttachEffect.m_id`。 OT0 は、少数の行でのみこれを使用します。
- `m_SkillIconID`: **確認しました。** `Texture/TextureID.m_id` を直接送信します。これは、OT0 のプレイヤーに面するメインのコマンド アイコン フィールドであり、それを `SkillIconList` 行に再ポイントすべきではない理由です。
- `m_SkillUniqueIconID`: **確認しました。** `Skill/SkillUniqueIconID.m_id`。スキーマ内に存在しますが、OT0 `SkillID` 行では使用されません。
- `m_CommandPhaseSkillIconID`: **OT0 では未使用であることが確認されました。** コマンドフェーズシステム用に予約された代替アイコン。
- `m_OverrideCommandName`: **OT0 では未使用であることが確認されました。** コマンド命名用に予約された代替テキスト ID。
- `m_CommandCategory`: **観察されました。** まれな UI/カテゴリ ヘルパー。これを使用するのは 5 つの OT0 行のみで、すべて同じ値 `62274` です。依存する前に、既に使用している行のクローンを作成します。
- `m_BlockedSkill`: **OT0では未使用を確認。** 予約された相互排他/ブロックスキルフック。
- `m_ReplaceCondition`: **OT0 で未使用を確認。** 単一の置換条件フックが予約されています。
- `m_ReplaceConditionArray`: **OT0で未使用を確認。** 予約された置換条件配列。
- `m_ReplaceSkill`: **OT0では未使用を確認。** 予約済みの置換スキルフック。
- `m_ReplaceSkillArray`: **OT0 で未使用を確認。** 予約済みの置換スキル配列。
- `m_DisableReplaceTag`: **観察されました。** 一部の置換タグ付けロジックまたは置換関連の UI ラベル付けを抑制します。 OT0 はどこでも false のままにします。
- `m_SpecialSkillInitialGaugeValue`: **OT0では未使用を確認。** 特殊スキルスタイルシステムのゲージシードフィールド。
- `m_SpecialSkillRegenerateValue`: **OT0では未使用を確認。** 特技スタイル系のゲージ回復フィールド。
- `m_RecommendCategory`: **確認済み。** `ESKILL_RECOMEND_CATEGORY` (`ATTACK`、`SUPPORT`、`HEAL`) からの推奨バケット。 OT0 は、行の小さなサブセットでのみこれを使用します。
- `m_DuplicateAilment`: **OT0 では未使用であることが確認されました。** スキル行レベルで予約済みの状態異常重複フラグ。
- `m_AfterAction`: **観察されました。** まれなフォローアップ アクション コード。 OT0 は 5 行でのみ使用します。これらの行には、強制的なポストアクション動作と一致するステージングされた可用性/有効データも含まれています。
- `m_InitialChargeTrun`: **OT0 では未使用であることが確認されました。** タイポはアセットによって保存されています。チャージターンパラメータは他のシステム用に予約されています。
- `m_ReChargeTrun`: **OT0 では未使用であることが確認されました。** 他のシステム用に予約されているリチャージターンパラメータ。
- `m_MaxBoostLv`: **確認済み。** 最大ブースト レベル。 OT0 は、すべての `SkillID` 行でこれを `3` に設定します。
- `m_FieldUseable`: **確認済み。** フィールド コンテキストでスキルを使用可能としてマークします。 OT0はフィールド蘇生スキルなどの20行にのみこれを設定します。
- `m_VoiceID`: **確認しました。** スキルの音声コールアウトについては `Sound/PartVoiceID.m_id` を指定します。

#### 具体的な OT0 の例
- `m_FieldUseable`: `SkillID#607665 First Aid`、`#900556 Healing Light`、`#902332 Vivify`、および `#902114 Revive` はすべてこのフラグを設定します。在庫のOT0では、これは一般的な「どこでも機能する」マーカーではなく、圧倒的に家族の治癒/復活のシグナルです。
- `m_AfterAction`: `SkillID#900012 Retreating Strike`、`#900042 Lunar Arc`、`#900100 Expert First Aid`、および `#900563/#901483 Song of Battle` はすべて `m_AfterAction = 1` を設定します。目に見える説明も、明示的に「その後、後列に移動します」で終わります。これは、フィールドがアクション後の強制的な列の移動に結びついていることの強力な証拠です。
- `m_RecommendCategory`: `SkillID#902332 Vivify`、`#901894 Arcane Healing`、`#902108 Heal Wounds`、`#902114 Revive` などの復元行は、`ESKILL_RECOMEND_CATEGORY::HEAL` と完全に一致する `m_RecommendCategory = 3` を使用します。

#### 『大陸の覇者』のみの追加フィールド
- `m_DispBoostLv`: **『大陸の覇者』側のDBスキーマから確認済み。** ブースト レベルを明示的に表示するための UI フラグ。
- `m_IsBasicAbility`: **『大陸の覇者』側のDBスキーマから確認。** 行を学習/特殊能力ではなく基本能力としてマークします。
- `m_NoChangeBoostType`: **名前と『大陸の覇者』側の使用例から確認。** 置換または変換ロジックによってスキルのブースト ファミリ/タイプが変更されるのを防ぎます。
- `m_Boost246`: **名前と『大陸の覇者』側の使用例から観察。** 通常の OT0 スタイルの `0/1/2/3` ファミリ プレゼンテーションを使用しないスキル用の 『大陸の覇者』固有のブースト パターン フラグ。
- `m_ForcedSkillFilter`: **『大陸の覇者』側のDBスキーマと ID の重複から確認。** 特定のフィルター バケットにスキルを強制的に含める `SkillFilterType.m_id` 行の配列。
- `m_ExclusionSkillFilter`: **『大陸の覇者』側のDBスキーマと ID の重複により確認されました。** 特定のフィルター バケットからスキルを強制的に除外する `SkillFilterType.m_id` 行の配列。
- `m_ForcedCategory`: **『大陸の覇者』側のDBスキーマと ID の重複から確認。** 『大陸の覇者』側のフィルター UI のスキルに `SkillFilterCategory.m_id` を強制。
- `m_NotUsedAutoBattle`: **名前と『大陸の覇者』側の使用例から観察。** 自動戦闘除外フラグ。
- `m_CmdPhaseTurnResetOnContinue`: **名前と『大陸の覇者』側の使用例から観察。** スキルが別のフェーズまたはターンに継続すると、コマンドフェーズの状態がリセットされます。

#### OT0 使用上の注意
- `Row-count bias`: OT0 には `7330` `SkillID` 行がありますが、`1614` のみがカテゴリ `COMMAND` です。敵のコンテンツがファイルを支配しています。
- `Array sizes`: OT0 は固定サイズを使用します: `m_BoostSkills` 長さ `4`、`m_Avails` 長さ `20`、`m_Effectives` 長さ `20`、`m_WeaponReplaceSkill` 長さ `8`。
- `True rarity`: OT0 は、`5` 行に `m_IsRandomReplace`、`23` に `m_IsWeaponSelect`、`4` に `m_IsReplaceMainWeapon`、`5` に `m_AfterAction` のみを設定します。 `20` の `m_FieldUseable`。

### `SkillAvailID`
ゲームプレイのペイロード行。 `SkillID` が *これがどのスキル ファミリであるかを示す場合、`SkillAvailID` は *戦闘またはフィールド ロジックでステージが実際に何を行うか*を示します。

- **OT0 行:** `7369`。
- **『大陸の覇者』行数:** `28023`。
- **OT0 SDK 行構造体:** `FSkillAvailIDBase`。
- **関連する OT0 SDK 列挙型:** `Kingship::ESKILL_INVOKE`、`ESKILL_TARGET_TYPE`、`ESKILL_MODIFY_TYPE`、`ESKILL_CALC_TYPE`、`ESKILL_AILMENT_CALC_TYPE`。
- OT0 リバース エンジニアリングの場合、`m_ModifyType` は行全体の中で最も重要なフィールドです。これは、ほとんどの数値ペイロード配列をどのように読み取るかを決定します。

#### この行を直接駆動する正確な OT0 SDK 列挙型引用符
##### `Kingship::ESKILL_INVOKE` (コンテキスト列挙型)
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6360`より引用。
```cpp
enum class Kingship::ESKILL_INVOKE : uint8
{
	eNONE                                    = 0,
	ePASSIVE                                 = 1,
	eBATTLE                                  = 2,
	eFIELD                                   = 3,
	SKILL_MAX                                = 4,
};
```
- SDK ダンプは、同じ印刷名を持つ 2 つの異なる `Kingship::ESKILL_INVOKE` 列挙型ブロックを公開します。上記のコンテキスト列挙型は、より広範なパッシブ/バトル/フィールド呼び出しドメインを記述しています。

##### `Kingship::ESKILL_INVOKE` (タイミング列挙型)
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6436`より引用。
```cpp
enum class Kingship::ESKILL_INVOKE : uint8
{
	NONE                                     = 0,
	COMMAND                                  = 1,
	BATTLING                                 = 2,
	BATTLE_1ST                               = 3,
	BATTLE_END                               = 4,
	TURN_1ST                                 = 5,
	TURN_END                                 = 6,
	ACTION_1ST                               = 7,
	ACTION_BEGIN                             = 8,
	ACTION_DAMAGE                            = 9,
	ACTION_FINISH                            = 10,
	ACTION_LAST                              = 11,
	CHANGE_FWD                               = 12,
	CHANGE_BAK                               = 13,
	FORWARD                                  = 14,
	BACKUP                                   = 15,
	JUST_BREAK                               = 16,
	SLIP_DAMAGE                              = 17,
	GUARDING                                 = 18,
	TURN_BEGIN                               = 19,
	TURN_FINISH                              = 20,
	BEFORE_COMMAND_SELECTION                 = 21,
	BATTLING_SLIP_DAMAGE                     = 22,
	BATTLETURN_MODECHANGE                    = 23,
	FORWARD_AND_BACKUP                       = 24,
	COMMAND_PHASE                            = 25,
	ON_BREAKED                               = 26,
	BUFF_LIMIT_CALCULATING                   = 27,
	JUST_DYING                               = 28,
	ANY_CHARA_ACTION_LAST                    = 29,
	FIELD_ACTION                             = 50,
	FIELDING                                 = 51,
	ALWAYS                                   = 100,
	ESKILL_MAX                               = 101,
};
```
- `SkillAvailID.m_InvokeTiming` は、以前のコンテキスト列挙型ではなく、上記のタイミング列挙型を使用します。これが、ライブ OT0 行に `COMMAND`、`ACTION_DAMAGE`、`FIELD_ACTION`、`ALWAYS` などの値が含まれる理由です。

##### `ESKILL_TARGET_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6683`より引用。
```cpp
enum class ESKILL_TARGET_TYPE : uint8
{
	NONE                                     = 0,
	ENEMY_1                                  = 1,
	ENEMY_ALL                                = 2,
	ENEMY_RND                                = 3,
	ENEMY_ALL_RND                            = 4,
	SELF                                     = 5,
	FRIENDLY_1                               = 6,
	FRIENDLY_ALL                             = 7,
	FRIENDLY_RND                             = 8,
	FRIENDLY_RND_WITHME                      = 9,
	FRIENDLY_ALL_RND                         = 10,
	FRIENDLY_ALL_RND_WITHME                  = 11,
	FRIENDLY_1_DEAD                          = 12,
	FRIENDLY_ALL_DEAD                        = 13,
	CONTINUE                                 = 14,
	FORWARDS                                 = 15,
	BACKUPS                                  = 16,
	BUDDY                                    = 17,
	PAIR                                     = 18,
	ALL                                      = 19,
	FRIENDLY_EVERYONE                        = 20,
	FRIENDLY_EVERYONE_DEAD                   = 21,
	FRIENDLY_1_WITHOUTME                     = 22,
	BACKUPS_DEAD                             = 23,
	FRIENDRY_ALL_RERAISEABLE                 = 24,
	FRIENDLY_1_ANY                           = 25,
	FRIENDLY_ALL_WITHOUTME                   = 26,
	FRIENDLY_1_HAS_SPSKILL                   = 27,
	FRIENDLY_EVERYONE_WITHOUTME              = 28,
	BACKUPS_ALIVE                            = 29,
	FRIENDLY_1_EVERYONE                      = 30,
	AI_CONTINUE                              = 50,
	AI_SELF                                  = 51,
	AI_ENEMY_RND                             = 52,
	AI_ENEMY_ALL                             = 53,
	AI_ENEMY_MIN_HP                          = 54,
	AI_ENEMY_MAX_HP                          = 55,
	AI_ENEMY_MIN_SP                          = 56,
	AI_ENEMY_MAX_SP                          = 57,
	AI_ENEMY_MIN_BP                          = 58,
	AI_ENEMY_MAX_BP                          = 59,
	AI_ENEMY_MIN_SLD                         = 60,
	AI_ENEMY_MAX_SLD                         = 61,
	AI_FRIENDLY_RND                          = 62,
	AI_FRIENDLY_RND_WITHME                   = 63,
	AI_FRIENDLY_ALL_RND                      = 64,
	AI_FRIENDLY_ALL_RND_WITHME               = 65,
	AI_FRIENDLY_MIN_HP                       = 66,
	AI_FRIENDLY_MAX_HP                       = 67,
	AI_FRIENDLY_MIN_SP                       = 68,
	AI_FRIENDLY_MAX_SP                       = 69,
	AI_FRIENDLY_MIN_BP                       = 70,
	AI_FRIENDLY_MAX_BP                       = 71,
	AI_FRIENDLY_MIN_SLD                      = 72,
	AI_FRIENDLY_MAX_SLD                      = 73,
	ENEMY_1_BUFFED                           = 150,
	ENEMY_1_DEBUFFED                         = 151,
	ENEMY_ALL_BUFFED                         = 152,
	ENEMY_ALL_DEBUFFED                       = 153,
	ENEMY_1_B_PATK                           = 154,
	ENEMY_1_B_MATK                           = 155,
	ENEMY_1_B_PDEF                           = 156,
	ENEMY_1_B_MDEF                           = 157,
	ENEMY_1_B_AGI                            = 158,
	ENEMY_1_B_CRT                            = 159,
	ENEMY_1_D_PATK                           = 160,
	ENEMY_1_D_MATK                           = 161,
	ENEMY_1_D_PDEF                           = 162,
	ENEMY_1_D_MDEF                           = 163,
	ENEMY_1_D_AGI                            = 164,
	ENEMY_1_D_CRT                            = 165,
	ENEMY_1_B_SWORD                          = 166,
	ENEMY_1_B_AXE                            = 167,
	ENEMY_1_B_DAGGER                         = 168,
	ENEMY_1_B_PAPER                          = 169,
	ENEMY_1_B_ROD                            = 170,
	ENEMY_1_B_BOW                            = 171,
	ENEMY_1_B_LANCE                          = 172,
	ENEMY_1_B_FAN                            = 173,
	ENEMY_1_B_FIRE                           = 174,
	ENEMY_1_B_ICE                            = 175,
	ENEMY_1_B_THUNDER                        = 176,
	ENEMY_1_B_WIND                           = 177,
	ENEMY_1_B_LIGHT                          = 178,
	ENEMY_1_B_DARK                           = 179,
	ENEMY_1_B_NAUGHT                         = 180,
	ENEMY_1_D_SWORD                          = 181,
	ENEMY_1_D_AXE                            = 182,
	ENEMY_1_D_DAGGER                         = 183,
	ENEMY_1_D_PAPER                          = 184,
	ENEMY_1_D_ROD                            = 185,
	ENEMY_1_D_BOW                            = 186,
	ENEMY_1_D_LANCE                          = 187,
	ENEMY_1_D_FAN                            = 188,
	ENEMY_1_D_FIRE                           = 189,
	ENEMY_1_D_ICE                            = 190,
	ENEMY_1_D_THUNDER                        = 191,
	ENEMY_1_D_WIND                           = 192,
	ENEMY_1_D_LIGHT                          = 193,
	ENEMY_1_D_DARK                           = 194,
	ENEMY_1_D_NAUGHT                         = 195,
	ENEMY_1_POISON                           = 196,
	ENEMY_1_BLEED                            = 197,
	ENEMY_1_SILENCE                          = 198,
	ENEMY_1_SLEEP                            = 199,
	ENEMY_1_PARALYSYS                        = 200,
	ENEMY_1_FEAR                             = 201,
	ENEMY_1_WASTE                            = 202,
	ENEMY_1_HEADACHE                         = 203,
	ENEMY_1_ENERGYDRAIN                      = 204,
	ENEMY_1_COUNTDOWN                        = 205,
	ENEMY_1_PROVOKE                          = 206,
	ENEMY_1_REGENERATE                       = 207,
	ENEMY_1_SP_REGENERATE                    = 208,
	ENEMY_1_BP_REPAIR                        = 209,
	ENEMY_1_SHIELD_REPAIR                    = 210,
	ENEMY_1_ROBUST                           = 211,
	ENEMY_1_COUNTER                          = 212,
	ENEMY_1_INTERCEPT                        = 213,
	FORWARDS_AND_BACKUPS                     = 214,
	FRIENDLY_ALL_NOALIVECHECK                = 215,
	FRIENDLY_MIN_HP                          = 216,
	ENEMY_BACKUPS_RND                        = 217,
	FORWARDS_RND_SIDE_BY_SIDE                = 218,
	BACKUPS_RND_SIDE_BY_SIDE                 = 219,
	PAIR_RND                                 = 220,
	PAIR_RND_SIDE_BY_SIDE                    = 221,
	ENEMY_HP_MAX_ORDER                       = 222,
	ENEMY_FORWARDS_HP_MAX_ORDER              = 223,
	FORWARDS_OR_BACKUPS                      = 224,
	FRIENDLY_1_WITHOUTME_ALL                 = 225,
	FORWARDS_OR_BACKUPS_ALIVE                = 226,
	CONTINUE_NOSELF_OR_ENEMY1                = 227,
	CONTINUE_NOSELF_FRIEND                   = 228,
	LAST_RECOVER                             = 229,
	ESKILL_TARGET_MAX                        = 230,
};
```
- `SkillAvailID.m_TargetType` は、この列挙型からの生の数値セレクターを保存します。そのため、フィールドは通常のプレーヤー ターゲット (`ENEMY_1`、`FRIENDLY_ALL_DEAD`) と AI/ヘルパー セレクター (`AI_ENEMY_MIN_HP`、`FORWARDS_OR_BACKUPS_ALIVE`) の両方を同じ整数列にエンコードできます。

##### `ESKILL_MODIFY_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6476`より引用。
```cpp
enum class ESKILL_MODIFY_TYPE : uint8
{
	NONE                                     = 0,
	CHARACTER                                = 1,
	STEAL_CURE                               = 2,
	FORCE_CHANGE                             = 3,
	WEAK_CHANGE                              = 4,
	ESCAPE                                   = 5,
	BOOST_LEVEL                              = 6,
	REVIVE                                   = 7,
	CALL_VISITOR                             = 8,
	SHORTEN_TURN_MINUS                       = 9,
	SHORTEN_TURN_DEBUFF                      = 10,
	SHORTEN_TURN_AILMENT                     = 11,
	GUARD                                    = 12,
	RERAISE                                  = 13,
	COUNT_RECOVER_ONE                        = 14,
	COUNT_RECOVER_ALL                        = 15,
	COUNT_RECOVER_SP_SKILL                   = 16,
	DECREASE_AILMENT_COUNT                   = 17,
	MAGIC_STONE_GAUGE                        = 18,
	INTENSIVE                                = 19,
	GUEST_CALLABLE_COUNT                     = 20,
	SHIELD_ONLY                              = 21,
	STEAL_ITEM                               = 22,
	STEAL_LEAF                               = 23,
	DETECT_HP                                = 24,
	STEAL_JP                                 = 25,
	PASS_SP                                  = 26,
	BUFF_EAT                                 = 27,
	SUCTION_LEAF                             = 28,
	DETECT_ITEM                              = 29,
	SP_BURST                                 = 30,
	BUFF_COPY                                = 31,
	ADD_TURN_PLUS_AILMENT                    = 32,
	ADD_TURN_MINUS_AILMENT                   = 33,
	MAKE_LEAVE_FROM_BATTLE                   = 34,
	PASS_SSP                                 = 35,
	REINFORCEMENT_PARTS                      = 36,
	RESULT_EXP                               = 100,
	RESULT_MONEY                             = 101,
	RESULT_ITEM                              = 102,
	AILMENT_RATE                             = 103,
	STEAL_CURE_RATE                          = 104,
	ADDTURN_BUFF_CAST                        = 105,
	ADDTURN_DEBUFF_CAST                      = 106,
	ADDTURN_BUFF_RECEIVE                     = 107,
	ADDTURN_DEBUFF_RECEIVE                   = 108,
	ADDTURN_M_AILMENT_CAST                   = 109,
	ADD_INFLUENCE                            = 110,
	ADD_STATUS_ALL                           = 111,
	ENHANCED_RATE                            = 112,
	ADDTURN_PLUS_CAST                        = 113,
	ADDTURN_MINUS_CAST                       = 114,
	ADDTURN_PLUS_RECEIVE                     = 115,
	ADDTURN_MINUS_RECEIVE                    = 116,
	GUARD_UPGRADE                            = 117,
	RESULT_RECOVER_STATUS                    = 118,
	RESULT_JP                                = 119,
	DETECT_WEAK                              = 120,
	ATTACKED_AILMENT                         = 121,
	DAMAGED_AILMENT                          = 122,
	DAMAGED_AILMENT_SWORD                    = 123,
	DAMAGED_AILMENT_LANCE                    = 124,
	DAMAGED_AILMENT_DAGGER                   = 125,
	DAMAGED_AILMENT_AXE                      = 126,
	DAMAGED_AILMENT_BOW                      = 127,
	DAMAGED_AILMENT_ROD                      = 128,
	DAMAGED_AILMENT_PAPERS                   = 129,
	DAMAGED_AILMENT_FAN                      = 130,
	ATTACK_CURE                              = 131,
	AUTOMATIC_CURE                           = 132,
	DAMAGED_CURE                             = 133,
	GUTS                                     = 134,
	ATTACK_AND_SKILL                         = 135,
	ATTACK_AND_SKILL_SWORD                   = 136,
	ATTACK_AND_SKILL_LANCE                   = 137,
	ATTACK_AND_SKILL_DAGGER                  = 138,
	ATTACK_AND_SKILL_AXE                     = 139,
	ATTACK_AND_SKILL_BOW                     = 140,
	ATTACK_AND_SKILL_ROD                     = 141,
	ATTACK_AND_SKILL_PAPERS                  = 142,
	ATTACK_AND_SKILL_FAN                     = 143,
	SKILL_AND_SKILL                          = 144,
	INCIDENTALLY_SKILL                       = 145,
	BREAKED_AFTER_SKILL                      = 146,
	SACRIFICE                                = 147,
	REPEAT_WEAPON_ATTACK                     = 148,
	REPEAT_MAGIC_ATTACK                      = 149,
	REPEAT_ATTACK                            = 150,
	REPEAT_SKILL_ATTACK                      = 151,
	REPEAT_SKILL_ANY                         = 152,
	DAMAGE_AND_SKILL                         = 153,
	ENCOUNT_RATE                             = 154,
	SKILLBOARD_MAX_COST                      = 155,
	AVAIL_VALUES_INCREASE                    = 156,
	BP_RENT_BUDDY                            = 157,
	AILMENT_MEMOLIZE                         = 158,
	TURN_LAST_AND_SKILL                      = 159,
	REQUIRE_INVALID                          = 160,
	RESULT_DROP                              = 161,
	EQUIP_REVERSAL                           = 162,
	BREAK_ACT                                = 163,
	LAST_ACT                                 = 164,
	WEAK_AFTER_SKILL                         = 165,
	AVAIL_TYPE_AND_SKILL                     = 166,
	REFLECT_AILMENT                          = 167,
	PURSUIT_SKILL                            = 168,
	ESCAPE_RATE                              = 169,
	PLUS_AILMENT_MEMOLIZE                    = 170,
	BATTLE_1ST_ACT                           = 171,
	P_ATK_M_ATK_COPY                         = 172,
	P_ATK_M_ATK_CHANGE                       = 173,
	DAMAGE_LIMIT_BREAK                       = 174,
	DEBUFF_COUNT_ACCEL                       = 175,
	PRE_EMPTIVE_RATE_UP                      = 176,
	SUCKER_PUNCH_DISABLE                     = 177,
	DEATHBLOWPOINT_UP                        = 178,
	RARE_ENCOUNT_UP                          = 179,
	TRIBUTE                                  = 200,
	ITEM_SUPPLY                              = 201,
	RENAME_UNLOCK                            = 202,
	SHOP_TRADE_PLUS                          = 203,
	SHOP_TRADE_SPEED                         = 204,
	SHOP_TRADE_RARITY                        = 205,
	FARM_GROWTH_SPEEDUP                      = 206,
	FARM_HARVEST_ALL_ITEM_UP                 = 207,
	FARM_HARVEST_ITEM_UP                     = 208,
	FARM_HARVEST_ITEM_AMOUNT_LOTUP           = 209,
	SHOP_DISCOUNT                            = 210,
	SHOP_SELL_UP                             = 211,
	NO_COUNT_GUESTCALL                       = 212,
	RANCH_ITEM_AMOUNT_LOTUP                  = 213,
	RANCH_GROWTH_SPEEDUP                     = 214,
	RANCH_APPEND_ITEM                        = 215,
	ITEM_SUPPLY_SPEED_UP                     = 216,
	SHOP_ALCHEMY_ITEM_BONUS                  = 217,
	GUESTCALL_COUNT_UP                       = 218,
	VILLAGE_BONUS_TYPE_SUPPORTER_ENTRY_COUNT = 219,
	VILLAGE_BONUS_TYPE_BATTLE_EXP_UP         = 220,
	VILLAGE_BONUS_TYPE_FARM_SLOT_COUNT       = 221,
	VILLAGE_BONUS_TYPE_SUPPORTER_CALL_COUNT  = 222,
	VILLAGE_BONUS_TYPE_SHOP_TRADE_SPEED      = 223,
	VILLAGE_BONUS_TYPE_SHOP_TRADE_RARITY     = 224,
	VILLAGE_BONUS_TYPE_SHOP_TRADE_COUNT      = 225,
	VILLAGE_BONUS_TYPE_SALON_REDO_BODY       = 226,
	VILLAGE_BONUS_TYPE_SALON_REDO_FACE_AND_EYE = 227,
	VILLAGE_BONUS_TYPE_SALON_REDO_HAIR_AND_HAIR_COLOR = 228,
	VILLAGE_BONUS_TYPE_SALON_REDO_SKIN_COLOR = 229,
	VILLAGE_BONUS_TYPE_SALON_REDO_GESTURE    = 230,
	VILLAGE_BONUS_TYPE_SALON_REDO_VOICE      = 231,
	VILLAGE_BONUS_TYPE_HARVEST_STORAGE_ITEM  = 232,
	VILLAGE_BONUS_TYPE_SUPPLY_ITEM_STORAGE_ITEM = 233,
	VILLAGE_BONUS_TYPE_HARVEST_STORAGE_CAPITAL = 234,
	VILLAGE_BONUS_TYPE_SUPPLY_ITEM_STORAGE_CAPITAL = 235,
	VILLAGE_BONUS_TYPE_TRAINING_COUNT        = 236,
	VILLAGE_BONUS_TYPE_TRAINING_OPEN_SPECIAL = 237,
	VILLAGE_BONUS_TYPE_TRAINING_OPEN_ESSENTIAL = 238,
	TRAINING_EXP_UP                          = 239,
	TRAINING_JP_UP                           = 240,
	FARM_FERTILIZER                          = 241,
	RANCH_FEED                               = 242,
	RESULT_EXP_ZERO                          = 243,
	VILLAGE_BONUS_TYPE_TRAINING_BATTLE_EXP_RATE = 244,
	VILLAGE_BONUS_TYPE_TRAINING_BATTLE_JP_RATE = 245,
	VILLAGE_BONUS_RESIDENT_ADD_COUNT         = 246,
	FARM_SPECIAL_GROWTH_SPEEDUP              = 247,
	RANCH_SPECIAL_GROWTH_SPEEDUP             = 248,
	FARM_HARVEST_ITEM_FINAL_AMOUNT_LOTUP     = 249,
	RANCH_ITEM_FINAL_AMOUNT_LOTUP            = 250,
	SHOP_PRICE_HIKE                          = 251,
	ARENA_EXP_UP                             = 252,
	RECOVER_DISCOUNT                         = 253,
	ITEM_SUPPLY_LOT_IN_GROUP                 = 254,
	ESKILL_MODIFY_MAX                        = 255,
};
```
- `SkillAvailID.m_ModifyType` は、この列挙型の生の数値オペコードを保存します。これが、この単一フィールドが `m_Values`、`m_Turns`、`m_Counts`、`m_ModifyStatus`、強化 ID、弱点変更 ID、ショップ/ファーム ヘルパー行、およびその他の多くのペイロードの解釈を支配する理由です。
- `SkillAvailID.m_CalcType` は、上記の `SkillCalcType` に埋め込まれた正確な `ESKILL_CALC_TYPE` 引用文と一緒に読む必要があります。
- `SkillAvailID.m_CalcTypeAilment` は、以下の `SkillAilmentType` に埋め込まれている正確な `ESKILL_AILMENT_CALC_TYPE` 引用文と一緒に読む必要があります。 OT0 値の使用法は、その状態異常側の enum ファミリーと一致します。

#### フィールドごと
- `m_id`: avail 行の主キー。
- `m_InvokeTiming`: **確認済み。** OT0 SDK ダンプ内のより豊富な `Kingship::ESKILL_INVOKE` ブロックからのタイミング列挙型。 OT0 は、`COMMAND`、`BATTLING`、`ACTION_DAMAGE`、`FIELD_ACTION`、`ALWAYS` などの値を使用します。
- `m_InvokeCondition`: **確認しました。** `Skill/SkillConditionList.m_id`。このステージは、この条件パッケージが合格した場合にのみ解決されます。
- `m_TargetType`: **確認済み。** `ESKILL_TARGET_TYPE` からのターゲット セレクター。ここで、ステージで 1 人の敵、すべての敵、自分自身、死亡した味方 1 人、全員、前列、バックアップ列、AI が選択した最も低い HP ターゲットなどを要求するかどうかを決定します。
- `m_ModifyType`: **確認済み。** `ESKILL_MODIFY_TYPE` のメイン操作コード。 `CHARACTER` は汎用のステータス/ダメージ/ヒール/バフ/デバフのパスですが、他の値は復活、弱点変更、強化、盗み、村の経済操作などを実行します。
- `m_ModifyStatus`: **確認済み。** 修正オペコードに具体的な統計またはリソース セレクターが必要な場合の `Character/CharaStatusID.m_id`。 OT0 はここで 17 個の一意のステータス ID のみを使用しており、それらはすべて `CharaStatusID` に完全に一致します。
- `m_AvailTag`: **観察されました。** 7 スロットの内部分類タグ配列。 OT0 は多くの繰り返しタプルを使用しますが、OT0 SDK ダンプはそのタプルのクリーンな列挙型を公開しません。明らかにそれに依存する既存のファミリーを複製する場合を除き、内部ルーティング/タグ付けフィールドとして扱います。
- `m_IsFoodAvail`: **観察されました。** 食料/村側のAvail動作に関するまれな OT0 フラグ。
- `m_HitTypes`: **確認済み。** 最大 4 つの `SkillHitRateList.m_id` 行の配列。これらは、マルチヒット数の分布と関連するランダムヒット ロジックを制御します。
- `m_RandomChoiceCount`: **観察されました。** 複数の結果から選択するオペコードのランダムな選択/分岐の数。 OT0では珍しい。
- `m_CalcType`: **確認済み。** `ESKILL_CALC_TYPE` からの計算モード。 OT0 では、一般的な値は `NONE`、`WEAPON`、`MAGIC`、`ADD_FIXED`、および `ADD_RATED` です。
- `m_Values`: **確認済み。** メインの数値ペイロード配列。解釈は、`m_ModifyType` と `m_CalcType` に依存します: ダメージ パワー、回復量、ステータス デルタ、報酬量、またはその他のオペコード固有の値。
- `m_FluctuationValues`: **観察されました。** 二次分散ペイロード。 OT0 は 3 行でゼロ以外の値のみを使用するため、ほとんどのスキルはこれを無視します。
- `m_SkillRatios`: **観察されていますが、非常に強力にサポートされています。** ブーストごとの比率の配列。一般的な OT0 のデフォルトは `(100,100,100,100)` ですが、ブースト可能な攻撃ファミリーの多くは `(100,180,260,340)` や `(100,200,300,400)` などのプログレッシブ セットを使用します。
- `m_Turns`: **観察されました。** ブーストごとの持続時間の配列。 OT0 は、これを時間指定のバフ、デバフ、カウンター、インターセプト、およびその他のステートフル効果に使用します。
- `m_OverwriteCount`: **観察されました。** ステージを再適用するときにカウントベースのエフェクトによって使用される明示的な上書きカウント。
- `m_Counts`: **観察されました。** ブーストごとのカウントまたはストック配列。制限された反応、繰り返しカウント、または同様のステージローカルカウンターなどのカウントベースのメカニズムによって使用されます。
- `m_OrderPriority`: **観察されました。** まれな戦闘順序のオーバーライド。 OT0 は 1 つの行でのみゼロ以外の値を使用します。
- `m_NextPriority`: **観察されました。** まれな次のアクションの優先度の調整。 OT0 は 7 行でのみ使用します。
- `m_TargetNextPriority`: **観察されました。** ターゲット側の次のアクションの優先順位設定。 OT0 は主にセンチネル値 `-100` を使用します。 `1`、`2`、`30`、または `31` のような値に設定すると、今後のターン順序の動作が変更されます。
- `m_ShieldDamage`: **OT0 で未使用を確認。** 常にゼロ。
- `m_IgnoreWeak`: **観察されました。** このステージの弱点相互作用または弱点要件を無視します。 OT0 は 73 行でのみ使用します。
- `m_NoBreakable`: **OT0 で未使用を確認。** 常にゼロ。
- `m_PhysicalHitRatio`: **観察されました。** まれな物理ヒット率のオーバーライド。 OT0 は 2 行でのみ使用します。
- `m_CriticalRatio`: **観察されました。** このステージのクリティカル率の調整。 OT0 は数百の攻撃列でそれを使用します。
- `m_Suicide`: **観察されました。** ステージを自己犠牲または自己殺害としてマークします。
- `m_LotteryAilment`: **観察されました。** ランダムな状態異常選択ヘルパー。 OT0 ではまれで、変更パスが確率的に異常パッケージを選択している場合にのみ役立ちます。
- `m_AilmentInvokeCondition`: **観察されました。** ステージの状態異常部分に特化した追加の条件ゲート。 OT0 は 1 行で使用します。
- `m_AddAilmentPick`: **観察されました。** `m_AddAilment` から選択するための珍しいセレクター フラグ。 OT0 は 4 行でのみ使用します。
- `m_AddAilment`: **確認済み。** 追加/適用する最大 16 行の `SkillAilmentType.m_id` 行のインライン リスト。
- `m_CalcTypeAilment`: **観察されました。** 状態異常計算モードの並列リスト。最も可能性の高い enum ファミリは `ESKILL_AILMENT_CALC_TYPE` で、OT0 SDK はこれを公開しています。
- `m_ValueAilment`: **観察済み。** `m_AddAilment` に対応する状態異常の効力/オーバーライド値の並列リスト。
- `m_NotExtensionTarget`: **観察されました。** 通常の期間延長動作からターゲット状態を除外します。 OT0 は `557` 行でこれを使用するため、これは単なる 1 回限りのデバッグ フラグではありません。
- `m_WeakLockID`: **確認済み** `Enemy/EnemyWeakLockID.m_id`。ステージで特定の弱点スロットがロックされている場合に使用します。
- `m_DelayedSkill`: **観察されました。** すぐにではなく後で解決されるフォローアップ `SkillID.m_id`。
- `m_DelaySkillPriority`: **観察されました。** 遅延スキルステージの優先バケット/タイミングヘルパー。
- `m_SubAilmentPick`: **観察されました。** `m_SubAilment` のまれなセレクター フラグ。
- `m_SubAilment`: **確認済み。** 最大 16 の `SkillAilmentType.m_id` 行の二次リスト。 OT0 は、メイン ステージに重ねられた条件付きまたはサポートの状態異常パッケージにこれを使用します。
- `m_ResistID`: **確認済み** `Enemy/EnemyWeakID.m_id`、**ではありません** `SkillResistList`。 `EnemyWeakID` は武器ごと/要素ごとの `ResistType` クラスを格納するため、このフィールドは完全な弱点テーブル行を交換または挿入します。そのため、値セットは `SkillResistList` ではなく、`EnemyWeakID` に一致します。
- `m_ResistAilment`: **観察されました。** 基本疾患ファミリーのインライン疾患耐性率配列。 OT0 では、これは `m_ResistAilmentID` と共存できるため、一方が他方を無効にすると想定しないでください。
- `m_ResistAilmentID`: **確認済み** `Skill/SkillResistAilmentID.m_id`。これは、明示的な状態異常 ID と一致率を保存する状態異常耐性のパッケージ化された形式です。
- `m_Reinforcement`: **確認済み。** 増援を召喚するステージの場合は `Enemy/EnemyReinforcements.m_id`。 OT0 には外れ値 `140` が 1 つあるため、レアなボスの動作を複製する前に必ずターゲット行を確認してください。
- `m_WeakChangeID`: **確認済み。** 段階的な弱点テーブル変更用の `Enemy/EnemyWeakChangeID.m_id`。
- `m_EnhancedSkillID`: **OT0 で未使用を確認。** 常にゼロ。
- `m_SummonGuestID`: **確認済み。** ゲスト召喚動作の `Character/CharaGuest.m_id`。
- `m_OverrideWeapon`: **確認済み。** `WeaponType.m_id` を直接オーバーライドします。これは、スキル ステージが「たとえソース コマンドが他のものであっても、ヒットまたはアイコンにこの武器ファミリーを使用する」ことを示す方法です。
- `m_OverrideMagic`: **確認済み。** ステージの `MagicType.m_id` を直接オーバーライドします。
- `m_AppendType`: **OT0 で未使用であることが確認されました。** 予約された追加動作セレクター。
- `m_SkillAvailMagnificationCondition`: **確認済み** `SkillAvailMagnificationConditionList.m_id`。これは、条件付き拡大動作の主な検索フィールドです。
- `m_SkillAvailMagnificationConditionArray`: **確認済み。** 追加の `SkillAvailMagnificationConditionList.m_id` 行。ここで OT0 は長さ 7 の配列を使用します。
- `m_SkillAvailMagnification`: **確認済み。** `SkillAvailMagnificationList` 行 ID ではなく、OT0 の直接倍率値。一般的な OT0 値は、`110`、`130`、`150`、`180`、`200` です。
- `m_SkillAvailMagnificationArray`: **確認済み。** 行 ID ではなく、OT0 の追加倍率値を直接指定します。 OT0 は、`[200,190,180,170,160,150,140]` などの配列を使用します。
- `m_InvocationProbability`: **観察されました。** 追加の確率ゲート。 OT0 は 1 行でのみ使用します。
- `m_ModeChangeID`: **確認しました。** `Enemy/EnemyModeID.m_id`。これはボス/AI のフェーズ移行フックであり、一般的なプレイヤーフォームのスイッチではありません。
- `m_CountRefAilment`: **OT0 で未使用を確認。** 常にゼロ。
- `m_CountRefValue`: **OT0 で未使用を確認。** 常にゼロ。
- `m_CountRefValueLimit`: **OT0 で未使用を確認。** 常にゼロ。

#### 具体的な OT0 の例
- `m_ResistID`: `SkillAvailID#40085` と `#73697` は両方とも `6527` を使用し、`SkillAvailID#73698` は `6528` を使用します。これらの値は `EnemyWeakID` 行として存在します。そのため、このフィールドは単純なパーセンテージ修飾子ではなく、完全な弱点テーブル置換パスのように動作します。
- `m_OverrideWeapon` / `m_OverrideMagic`: `SkillAvailID#76127` から `#76134` は、`m_OverrideWeapon = 1..8` を経由して、`Repeated Bash` によって使用される武器ファミリー ステージです。対照的に、`Call for Help` の `SkillAvailID#74047` および `#74051` は `m_OverrideMagic = 5` を使用しており、同じステージ構造体が代わりにマジック ファミリを強制的にレンダリングできることを示しています。
- `m_Reinforcement`: `SkillAvailID#20049` は、表示テキストが `<skl_user_name> calls for his lackeys!` である敵のスキルに対して `m_Reinforcement = 43` を使用します。そのペアの `SkillEffectiveID#20049` には、召喚成功/召喚失敗の戦闘ログ文字列も含まれており、これはゲームプレイ行とプレゼンテーション行が連携している良い例です。
- `m_WeakChangeID` / `m_WeakLockID`: `SkillAvailID#20079` および `#20089` は表示可能なファミリー `Weaknesses have been swapped!` に属しますが、`SkillAvailID#20048` は `<skl_user_name> is protected by his lackeys.` に属し、`m_WeakLockID = 11` を保持します。これはまさに、弱点テーブルの突然変異とスロット ロックで予想されるパターンです。
- `m_ModeChangeID`: `Indestructible Flame` の `SkillAvailID#77350`、`Dark Hex` の `#74765`、および `Vorpal Strike` の `#75499` はすべてゼロ以外のモード変更 ID を設定します。そのため、このフィールドは一般的なプレイヤーのスタンス スイッチではなく、ボス/AI フェーズ フックとして読み取るのが最適です。
- `m_SkillAvailMagnification*`: `Surprise Attack` の `SkillAvailID#74940` は、条件パッケージ `69` に加えて、配列パッケージ `[68,67,59,61]` および直接倍率 `110/130/150/170/200` を使用します。 `Ulti-meow` の `SkillAvailID#76297` は、パッケージ `102..109` と `300` までの直接倍率を使用します。これらは生の値であり、`SkillAvailMagnificationList` 行 ID ではありません。

#### 『大陸の覇者』のみの追加フィールド
- `m_HitRatio`: **『大陸の覇者』側のDBスキーマから確認。** OT0 のより単純なヒット率テーブルを超える追加のヒット率スカラー。
- `m_LotteryAilmentNum`: **『大陸の覇者』側のDBスキーマから確認。** 抽選パスから抽出する状態異常の数。
- `m_CompareValueAilment`: **『大陸の覇者』側のDBスキーマから確認。** 状態異常側ロジックで使用される比較値。
- `m_DelayedSkillActByOwner`: **名前と『大陸の覇者』側の使用例から観察。** 最終的な状態保持者ではなく、元の所有者として遅延スキルを実行します。
- `m_SelfAfterSkillCheck`: **名前と『大陸の覇者』側の使用例から観察。** スキル実行後の追加のセルフチェック。
- `m_NoSelfAfterSkillCheckEffective`: **名前と『大陸の覇者』側の使用例から観察。** セルフチェックが失敗した場合、チェック後のプレゼンテーション パスを抑制します。
- `m_SubAilmentTag`: **『大陸の覇者』側のDBスキーマから確認済み。** 二次的な状態異常のグループ化のタグ ID。
- `m_SubAilmentTagFlag`: **名前と『大陸の覇者』側の使用例から観察。** 前のフィールドの有効化/タグ動作の切り替え。
- `m_CalcTypeResist`: **名前と『大陸の覇者』側の使用例から確認。** 抵抗操作用の個別の計算モード セレクター。
- `m_AddResistWeapon`: **『大陸の覇者』側のDBスキーマから確認済み。** 追加の武器耐性ペイロード配列。
- `m_AddResistMagic`: **『大陸の覇者』側のDBスキーマから確認済み。** 加算要素耐性ペイロード配列。
- `m_AilmentMagnificationConditions`: **『大陸の覇者』側のDBスキーマで確認** 状態異常側拡大条件ID。
- `m_AilmentMagnifications`: **『大陸の覇者』側のDBスキーマから確認。** 状態異常側の直接倍率値。
- `m_CountRefAilmentTag`: **『大陸の覇者』側のDBスキーマから確認。** タグベースのカウンター参照チャネル。
- `m_CountRefType`: **名前と『大陸の覇者』側の使用例から観察されます。** どのカウント ファミリが参照されるかを選択します。
- `m_CountRefAttribute`: **名前と『大陸の覇者』側の使用例から観察。** 参照されるカウンターの属性セレクター。
- `m_IgnorePreviousSuccessful`: **名前と『大陸の覇者』側の使用例から観察。** 繰り返しのチェックを解決するときに、以前の成功状態を無視します。
- `m_IgnoreDodge`: **名前と『大陸の覇者』側の使用例から確認。** 回避解像度をバイパスします。
- `m_IgnoreZeroDamage`: **名前と『大陸の覇者』側の使用例から観察** ダメージがゼロになっても効果処理を継続。
- `m_AddDamageLimitValue`: **名前と『大陸の覇者』側の使用例から観察。** 追加のダメージ上限が増加。
- `m_AddDamageLimitCondition`: **名前と『大陸の覇者』側の使用例から観察。** ダメージ制限増加の条件ゲート。
- `m_UseAilmentMagnificationForAddDamageLimit`: **名前と『大陸の覇者』側の使用例から観察。** ダメージ上限の増加に状態異常の倍率値を再利用します。
- `m_NonCondition`: **名前と『大陸の覇者』側の使用例から観察。** 追加の否定/無条件スイッチ。
- `m_IgnoreResistAilment`: **名前と『大陸の覇者』側の使用例から観察。** バイパス状態異常耐性。
- `m_IgnoreDefenceBuff`: **名前と『大陸の覇者』側の使用例から観察。** 防御アップ バフをバイパスします。
- `m_IgnoreReduceDamage`: **名前と『大陸の覇者』側の使用例から確認。** 一般的なダメージ軽減効果をバイパスします。
- `m_HitWeaponEnable`: **名前と『大陸の覇者』側の使用例から確認。** 明示的なヒット武器のタイピングを有効にします。
- `m_HitWeaponType`: **『大陸の覇者』側のDBスキーマから確認。** ヒット武器タイピングが有効な場合に使用される武器ファミリー。
- `m_NoConsumeAdditionWeak`: **名前と『大陸の覇者』側の使用例から観察。** ヒット後の加算弱状態を保持します。
- `m_IsBuffDamageSupStrictlyCheck`: **名前と『大陸の覇者』側の使用例から観察。** バフとダメージのサポート相互作用の検証を厳格化。

#### OT0 使用上の注意
- `Array sizes`: OT0 は固定サイズを使用します: `m_HitTypes` 長さ `4`、`m_Values` 長さ `4`、`m_SkillRatios` 長さ `4`、`m_Turns` 長さ `4`、`m_Counts` 長さ`4`、`m_AddAilment` 長さ `16`、`m_SubAilment` 長さ `16`、`m_SkillAvailMagnificationConditionArray` 長さ `7`、`m_SkillAvailMagnificationArray` 長さ `7`。
- `Common opcodes`: OT0 は圧倒的に `m_ModifyType = 1 (CHARACTER)` を使用します。次に一般的なファミリーは、弱点変更、復活、盗み、結果報酬、および `ITEM_SUPPLY` などの村経済オペコードです。
- `Dormant counters`: `m_ShieldDamage`、`m_NoBreakable`、`m_EnhancedSkillID`、`m_AppendType`、および `m_CountRef*` フィールドは、OT0 で事実上休止状態になります。

### `SkillConditionList`
`SkillAvailID.m_InvokeCondition` およびその他のスキル システム用の再利用可能なブール条件パッケージ。このファイルを `SkillAvailMagnificationConditionList` と混同しないでください。それらは異なるオペコード ファミリです。

- **OT0 行:** `275`。
- **『大陸の覇者』行数:** `933`。
- **OT0 SDK 行構造体:** `FSkillConditionListBase`。
- ここで使用される OT0 SDK ダンプは、このテーブル内の `m_Conditions` のクリーンな名前付き列挙型を**公開しません**。観察されたオペコード セットは拡大条件列挙型よりもはるかに大きく、`10723`、`10790`、`10816` などの値が含まれています。

#### フィールドごと
- `m_id`: 条件パッケージの主キー。
- `m_Conditions`: **形状は確認済み、名前は未解決。** 4 つの条件オペコードがスロットごとに解釈されました。これは、`SkillAvailID.m_InvokeCondition` によって使用される主な条件ファミリーです。
- `m_Params`: **確認済み。** 4 つの汎用整数パラメーター スロット。各スロットの意味は、一致する `m_Conditions` エントリによって決まります。
- `m_AilmentTypes`: **確認済み。** 状態異常を認識する条件オペコード用の 4 つの `SkillAilmentType.m_id` パラメータ スロット。 OT0 はここで `36` の一意の状態異常 ID を使用します。
- `m_StatusTypes`: **確認された形状。** ステータス認識条件オペコード用の 4 つの `CharaStatusID` パラメーター スロット。 OT0 はそれらを未使用のままにしますが、大陸の覇者 は同じフィールド ファミリをより積極的に使用します。
- `m_WeaponTypes`: **形状を確認しました。** `WeaponType.m_id` パラメーター スロットが 4 つあります。 OT0では使用されません。
- `m_MagicTypes`: **形状確認済み** `MagicType.m_id` パラメーター スロットが 4 つあります。 OT0では使用されません。
- `m_Equipment`: **形状確認済み** 装備パラメータスロットは 4 つ。 OT0では使用されません。
- `m_ORFlag`: **確認済み。** 占有条件スロットを AND ではなく OR で結合します。 OT0 行 `335` および `336` は、異なる状態異常 ID に対して同じ状態異常チェック オペコードを繰り返すことで、これを直接証明しています。

#### 『大陸の覇者』のみの追加フィールド
- `m_AutoDungeonBuff`: **『大陸の覇者』側のDBスキーマから確認** 追加のオートダンジョン固有のバフ パラメーター チャネル。
- `m_AutoDungeonBuffType`: **『大陸の覇者』側のDBスキーマから確認済み。** オートダンジョン バフ チャネルのタイプ セレクター。

### `SkillAvailMagnificationConditionList`
条件付き倍率専用の再利用可能な条件パッケージ。 `SkillConditionList` とは異なり、このファイルは名前付き OT0 SDK enum と**一致します**。

- **OT0 行:** `89`。
- **『大陸の覇者』行数:** `217`。
- **OT0 SDK 行構造体:** `FSkillAvailMagnificationConditionListBase`。
- **関連する OT0 SDK 列挙型:** `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`。

#### このテーブルで使用される正確な OT0 SDK 列挙型引用符
##### `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6226`より引用。
```cpp
enum class ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID : uint8
{
	NONE                                     = 0,
	SLD_EQ                                   = 1,
	SLD_GE                                   = 2,
	SLD_LE                                   = 3,
	SLD_MUL                                  = 4,
	HAS_AILMENT                              = 5,
	RACE_KILLER                              = 6,
	BEFORE_ACTION                            = 7,
	HP_SELF_LE                               = 8,
	HP_SELF_GE                               = 9,
	HP_TARGET_LE                             = 10,
	HP_TARGET_GE                             = 11,
	HP_SELF_LT                               = 12,
	HP_TARGET_LT                             = 13,
	HAS_AILMENT_SELF                         = 14,
	AILMENT_ICON_COUNT_SELF_GE               = 15,
	AILMENT_ICON_COUNT_TARGET_GE             = 16,
	AILMENT_ICON_COUNT_TARGET_LE             = 26,
	EQUIP_SELF                               = 17,
	NoEQUIP_SELF                             = 18,
	HAS_AILMENT_COUNT_GE                     = 19,
	HAS_AILMENT_COUNT_SELF_GE                = 20,
	INTENSIVE_COUNT_GE                       = 21,
	BHP_SELF_FIXED_LE                        = 22,
	BHP_SELF_FIXED_GE                        = 23,
	BHP_TARGET_FIXED_LE                      = 24,
	BHP_TARGET_FIXED_GE                      = 25,
	ENABLED_SUPPORTABILITY_SELF              = 27,
	DAY_OR_NIGHT                             = 30,
	TURN_ORDER_FIXED_LE                      = 31,
	TURN_ORDER_FIXED_GE                      = 32,
	PET_TAG                                  = 33,
	NoPET_TAG                                = 34,
	AILMENT_PC_COUNT_GE                      = 35,
	AILMENT_PC_COUNT_LE                      = 36,
	AILMENT_ENEMY_COUNT_GE                   = 37,
	AILMENT_ENEMY_COUNT_LE                   = 38,
	JOB_COUNT_SET_ID                         = 39,
	JOB_COUNT_GE                             = 40,
	JOB_COUNT_LE                             = 41,
	PROGRESS                                 = 42,
	SP_BURST_GE                              = 43,
	COUNT_DAMAGE_SELF_GE                     = 44,
	COUNT_DAMAGE_SELF_LE                     = 45,
	TARGET_FRIEND                            = 46,
	TARGET_ENEMY                             = 47,
	P_DEF_SELF_GE                            = 48,
	P_DEF_SELF_LE                            = 49,
	ESKILL_AVAIL_MAGNIFICATION_CONDITION_MAX = 50,
};
```
- `m_Conditions` は、この列挙型からの生の ID を直接保存します。したがって、`SkillAvailMagnificationConditionList` は、曖昧な条件バケットではなく、これらの拡大オペコードの正確な条件パッケージ テーブルです。
- これは、`SkillAvailID.m_SkillAvailMagnificationCondition` と `m_SkillAvailMagnificationConditionArray` をこのファイルに行 ID として正確に記述できる理由でもあります。これらのフィールドは、占有スロット自体が上記の列挙型によって型付けされるパッケージを指します。

#### フィールドごと
- `m_id`: 倍率条件パッケージの主キー。
- `m_Conditions`: **確認済み。** `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID` の 4 つの条件オペコード。 OT0 は、`SLD_GE`、`HAS_AILMENT`、`RACE_KILLER`、`BEFORE_ACTION`、`HP_SELF_LE`、`ENABLED_SUPPORTABILITY_SELF`、`DAY_OR_NIGHT`、`TURN_ORDER_FIXED_LE`、`PROGRESS`、`SP_BURST_GE` などの値と防御しきい値を使用します。
- `m_IntParams`: **確認済み。** 占有されている `m_Conditions` スロットに対応する 4 つの一般的な数値しきい値。
- `m_AilmentParams`: **観察されました。** 状態異常関連のパラメータ。多くの OT0 値は `SkillAilmentType.m_id` と一致しますが、少数の OT0 行は現在の OT0 `SkillAilmentType` エクスポートに存在しない ID を参照するため、すべての値がライブ OT0 疾患行であると想定しないでください。
- `m_RaceParams`: **観察されました。** レース フィルター/パラメーター スロット。 OT0 はほとんど使用しません。
- `m_Equipment`: **形状確認済み** 機器フィルタースロット。 OT0 はそれらを未使用のままにします。

#### 具体的な OT0 の例
- `SkillAvailMagnificationConditionList#7` は、`m_Conditions = [2,0,0,0]` を `m_IntParams = [10,0,0,0]` とともに保存します。つまり、単純な `SLD_GE 10` パッケージです。
- `#8` は、`m_Conditions = [5,0,0,0]` を `m_AilmentParams = [1,0,0,0]` とともに格納します。つまり、`HAS_AILMENT` と状態異常行 `1` を格納します。
- `#10` は `m_Conditions = [7,0,0,0]`、つまり追加の整数ペイロードを持たない純粋な `BEFORE_ACTION` ゲートを格納します。
- `#13` は、`m_Conditions = [12,0,0,0]` を `m_IntParams = [100,0,0,0]` とともに保存します。つまり、`HP_SELF_LT 100` スタイルのパッケージです。
- `#81` は、`m_Conditions = [8,0,0,0]` を `m_IntParams = [90,0,0,0]` とともに保存します。つまり、`HP_SELF_LE 90` しきい値パッケージです。

#### 『大陸の覇者』のみの追加フィールド
- `m_WeaponTypes`: **『大陸の覇者』側のDBスキーマから確認。** 倍率条件の武器ファミリー フィルター スロット。
- `m_MagicTypes`: **『大陸の覇者』側のDBスキーマから確認。** 倍率条件の要素ファミリー フィルター スロット。

### `SkillAvailMagnificationList`
4 行の小さな OT0 専用ルックアップ テーブル。これは本物ですが、OT0 `SkillAvailID` 行で使用されるメインのリンク パスではありません**。

- **OT0 行:** `4`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** このリポジトリに同梱されている OT0 SDK ダンプには、一致する専用の行構造体が見つかりませんでした。
- 実用上重要な点は、OT0 `SkillAvailID.m_SkillAvailMagnification` には `110` や `200` などの直接値が格納されるのに対し、このテーブルの ID は `0..3` のみであるということです。

#### フィールドごと
- `m_id`: 小さなルックアップ行の主キー。
- `m_Conditions`: **観察されました。** この小さな凡例テーブルの単一スロット条件セレクター。
- `m_Params`: **観察されました。** ルックアップ行の単一スロット パラメーター ペイロード。

### `SkillEffectiveID`
プレゼンテーションペイロード行。ここでは、カメラ、戦闘アクション、エフェクト、サウンド、音声、テキスト ポップアップ、および可視性の変更が時間に応じてステージングされます。

- **OT0 行:** `4381`。
- **『大陸の覇者』行数:** `11434`。
- **OT0 SDK 行構造体:** `FSkillEffectiveIDBase`。
- **関連する OT0 SDK 列挙型:** `ESKILLEFFECT_LAYOUT`、`ESKILLEFFECTIVE_CHARA_VISIBILITY`、`ESKILLEFFECTIVE_VISIBILITY_TARGET`。

#### この行を直接駆動する正確な OT0 SDK 列挙型引用符
##### `ESKILLEFFECT_LAYOUT`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6406`より引用。
```cpp
enum class ESKILLEFFECT_LAYOUT : uint8
{
	NONE                                     = 0,
	SELF                                     = 1,
	TARGET                                   = 2,
	FRIENDLY_CENTER                          = 3,
	TARGET_CENTER                            = 4,
	BATTLE_CENTER                            = 5,
	SELF_NO_REVERSE                          = 6,
	ESKILLEFFECT_MAX                         = 7,
};
```
- `m_EffectLayout` は、生の `ESKILLEFFECT_LAYOUT` 値を保存します。そのため、各エフェクト スロットは、`SELF`、`TARGET`、`TARGET_CENTER`、または `BATTLE_CENTER` のいずれでスポーンするかを正確に示すことができます。

##### `ESKILLEFFECTIVE_CHARA_VISIBILITY`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6371`より引用。
```cpp
enum class ESKILLEFFECTIVE_CHARA_VISIBILITY : uint8
{
	NONE                                     = 0,
	VISIBLE                                  = 1,
	INVISIBLE                                = 2,
	FADE_VISIBLE                             = 10,
	FADE_INVISIBLE                           = 11,
	ESKILLEFFECTIVE_CHARA_MAX                = 12,
};
```
- `m_CharaVisibility` は、可視化アクション自体 (`VISIBLE`、`INVISIBLE`、`FADE_VISIBLE`、`FADE_INVISIBLE`) のこの列挙型の生の値を保存します。

##### `ESKILLEFFECTIVE_VISIBILITY_TARGET`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6383`より引用。
```cpp
enum class ESKILLEFFECTIVE_VISIBILITY_TARGET : uint8
{
	NONE                                     = 0,
	SELF                                     = 1,
	TARGET                                   = 2,
	GUEST                                    = 3,
	FRIENDLY_1                               = 4,
	FRIENDLY_ALL                             = 10,
	FRIENDLY_ALL_WITHOUT_SELF                = 11,
	FRIENDLY_ALL_GUEST                       = 12,
	FRIENDLY_ALL_GUEST_WO_SELF               = 13,
	ENEMY_ALL                                = 20,
	ENEMY_ALL_WITHOUT_TARGET                 = 21,
	FORWARDS                                 = 30,
	FORWARDS_WO_SELF                         = 31,
	BACKUPS                                  = 32,
	FORWARDS_AND_BACKUPS                     = 33,
	FORWARDS_AND_BACKUPS_WO_SELF             = 34,
	ESKILLEFFECTIVE_VISIBILITY_MAX           = 35,
};
```
- `m_VisibilityTarget` は、可視性アクション (`SELF`、`TARGET`、`FRIENDLY_ALL`、`ENEMY_ALL`、`FORWARDS_AND_BACKUPS` など) を受け取る *who* のこの列挙型からの生の値を保存します。
- OT0 は、可視性ステージングを意図的に 2 つの次元に分割します。1 つの列挙型は可視性操作を記述し、もう 1 つの列挙型は受信者セットを記述します。

#### フィールドごと
- `m_id`: プレゼンテーション ステージの主キー。
- `m_Repeat`: **確認済み。** ステージの繰り返し回数。 OT0 は、すべての行でこれを `1` に設定します。
- `m_HoldTime`: **観察されました。** 合計プレゼンテーション保持時間 (秒単位)。
- `m_AvailTime`: **観測済み。** プレゼンテーションに対してゲームプレイ ステージが利用可能/解決済みとみなされる時間 (秒単位)。
- `m_Cameras`: **確認済み** カメラコレオグラフィーには最大 3 つの `Battle/BattleCameraParams.m_id` 行。
- `m_CameraTimes`: **観察されました。** カメラ ステージのトリガー時間。
- `m_Animations`: **確認済み。** 実行された戦闘アクションの最大 3 つの `Battle/BattleActionID.m_id` 行。
- `m_PlayTimes`: **観察されました。** 戦闘アクションのトリガー時間。
- `m_PlayRates`: **観察。** 戦闘アクションの再生速度の乗数。
- `m_AnimationTarget`: **観察されました。** アニメーションを再生するユーザーのアクションごとのターゲット セレクター。
- `m_AnimStartFrame`: **OT0 では未使用であることが確認されました。** 開始フレーム オーバーライド フィールドは SDK 構造体に存在しますが、OT0 行では使用されません。
- `m_AnimEndFrame`: **観察されました。** アニメーション ステージの終了フレームまたは終了セクション マーカー。 OT0 はすべての行にこれを設定します。
- `m_AnimNoIdle`: **観察されました。** アニメーション後のアイドル リターンまたはアイドル ブレンドを抑制します。 OT0では珍しい。
- `m_Effects`: **確認済み。** プレゼンテーション ステージによって最大 3 つの `Effect/EffectList.m_id` 行が生成されます。
- `m_InvokeTimes`: **観察されました。** エフェクトの出現時間。
- `m_EffectLayout`: **確認済み。** `ESKILLEFFECT_LAYOUT` からのエフェクトごとのスポーン レイアウト (`SELF`、`TARGET`、`FRIENDLY_CENTER`、`TARGET_CENTER`、`BATTLE_CENTER` など)。
- `m_OffsetsH`: **観察されました。** 生成されたエフェクトの水平オフセット。
- `m_OffsetsV`: **観察されました。** 生成されたエフェクトの垂直オフセット。
- `m_Sounds`: **確認済み。** ステージによってトリガーされる最大 3 つの `Sound/SoundList.m_id` 行。
- `m_SoundTimes`: **観察されました。** サウンド行のトリガー時間。
- `m_SoundToVoice`: **観察されました。** サウンドが音声スタイルのタイミング/処理に関連付けられているかどうかを制御するまれなルーティング/リンク フラグ。
- `m_Voices1`: **確認済み。** プライマリ音声スロットは `Sound/PartVoiceID.m_id` を指します。
- `m_Voices2`: **確認済み。** セカンダリ音声スロットは `Sound/PartVoiceID.m_id` を指します。
- `m_Voices3`: **OT0 で未使用が確認されました。** 三次音声スロットは存在しますが、OT0 ではゼロです。
- `m_VoiceTimes`: **観察されました。** 音声トリガー時間。
- `m_Texts`: **強力な証拠で観察されました。** 最大 10 個のフローティング テキスト ID。ほとんどの OT0 値は `GameTextSkill` を通じて解決され、一部は `GameTextUI` を通じて解決され、現在、小さな残留物が明らかな OT0 バンクの外側に存在します。
- `m_TextTime`: **観察されました。** フローティング テキストのトリガー時間。
- `m_CharaVisibility`: **確認済み。** `ESKILLEFFECTIVE_CHARA_VISIBILITY` の可視性ステージごとの値。
- `m_VisibilityTarget`: **確認済み。** `ESKILLEFFECTIVE_VISIBILITY_TARGET` の可視性ステージごとの値。
- `m_VisibilityStartTime`: **観測されました。** 可視性変更の開始時間。

#### 具体的な OT0 の例
- フローティングテキストは仮説ではありません。 `SkillEffectiveID#20049` には文字列 `<skl_user_name> calls for his lackeys!` および `However, the lackeys did not appear...` が含まれており、これは強化スタイルの敵のスキルと正確に一致するプレゼンテーションです。
- 可視性のステージングも、残されたフィールドではなく、本物です。 `SkillEffectiveID#65047` と `#65052` は両方とも `Lotus Blade` で使用され、`TARGET` 上の `INVISIBLE -> VISIBLE` でターゲットの可視性を切り替えます。
- `Pilfer` で使用される `SkillEffectiveID#65954` および `#65956` は、`TARGET` で `FADE_INVISIBLE` / `FADE_VISIBLE` を使用し、ハード トグル バリアントではなくフェード バリアントを表示します。
- `Hunter's Drive` によって使用される `SkillEffectiveID#65572` および `#65631` は、SDK 列挙型の `FRIENDLY_ALL_WITHOUT_SELF` と一致する可視性セレクター `11` をターゲットとします。これは、可視性ターゲットの列挙型が実際にそのまま使用されていることを示す良い証拠です。
- `Soaring Javelin` および `Take Aim` によって使用される `SkillEffectiveID#66152` は、ターゲット セレクター `20`、つまり `ENEMY_ALL` に可視性ステージを適用します。これは、同じシステムが 1 対 1 のステルス効果に限定されないことを証明します。

#### 『大陸の覇者』のみの追加フィールド
- `m_MagicStonePerformance`: **『大陸の覇者』側のDBスキーマで確認** 『大陸の覇者』固有の魔石性能アセットまたはモード。
- `m_MagicStonePerformanceStartTime`: **『大陸の覇者』側のDBスキーマから確認済み。** 前のフィールドの開始時刻。
- `m_MagicStoneLevel`: **『大陸の覇者』側のDBスキーマから確認。** 魔石提示用のレベルまたは階層セレクター。
- `m_CameraShakeCurve`: **『大陸の覇者』側のDBスキーマから確認済み。** カメラ シェイク アセット/カーブ。
- `m_CameraShakeStartTime`: **『大陸の覇者』側のDBスキーマから確認。** 手ぶれの開始時間。
- `m_Text2nd`: **『大陸の覇者』側のDBスキーマから確認済み。** 2 番目のテキスト バンク/2 番目のフローティング テキスト。
- `m_SkillEffectiveLinkageID`: **『大陸の覇者』側のDBスキーマと ID の重複から確認。** 連鎖エフェクト グループの場合は `SkillEffectiveLinkageList.m_id`。
- `m_IsSetVisibleUI`: **名前と『大陸の覇者』側の使用例から観察。** ステージ中の UI 可視性制御を有効にします。
- `m_UIVisiblityFlag`: **名前と『大陸の覇者』側の使用例から観察。** UI 可視性モード/フラグ。
- `m_UIVisibilityTarget`: **名前と『大陸の覇者』側の使用例から観察。** UI 可視性ターゲット セレクター。
- `m_UIVisibilityStartTime`: **名前と『大陸の覇者』側の使用例から観察。** UI 可視性変更の開始時間。

#### OT0 と 大陸の覇者 の比較に関するメモ
- `Camera schema shift`: OT0 は、`m_Cameras` および `m_CameraTimes` を通じてカメラ行をインラインに保ちます。エクスポートされた `Skill/` フォルダー内の 大陸の覇者 `SkillEffectiveID` スキーマは、これらのインライン配列を削除し、代わりにセカンダリ UI/エフェクト リンケージ フィールドを拡張します。

### `SkillAilmentType`
再利用可能な状態異常/状態パッケージ。このファイルは、古典的なネガティブ ステータス、ポジティブ バフ、カウンター、インターセプト、リレイズ ステート、回避ステート、ダメージ制限オーラ、永続ステート、およびその他の多くの時限効果またはトリガー効果をカバーしています。

- **OT0 行:** `490`。
- **『大陸の覇者』行数:** `968`。
- **OT0 SDK 行構造体:** `FSkillAilmentTypeBase`。
- **関連する OT0 SDK 列挙型:** `Kingship::ESKILL_INVOKE`、`ESKILL_AILMENT_CALC_TYPE`。
- これは、スキル システム全体の中で最も密度が高く、最も重要なテーブルの 1 つです。 `SkillAvailID` は、ここで指定する以上に、それ自体ではほとんど何も実行しないことがよくあります。

#### この行を直接駆動する正確な OT0 SDK 列挙型引用符
##### `Kingship::ESKILL_INVOKE` (タイミング列挙型)
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6436`より引用。
```cpp
enum class Kingship::ESKILL_INVOKE : uint8
{
	NONE                                     = 0,
	COMMAND                                  = 1,
	BATTLING                                 = 2,
	BATTLE_1ST                               = 3,
	BATTLE_END                               = 4,
	TURN_1ST                                 = 5,
	TURN_END                                 = 6,
	ACTION_1ST                               = 7,
	ACTION_BEGIN                             = 8,
	ACTION_DAMAGE                            = 9,
	ACTION_FINISH                            = 10,
	ACTION_LAST                              = 11,
	CHANGE_FWD                               = 12,
	CHANGE_BAK                               = 13,
	FORWARD                                  = 14,
	BACKUP                                   = 15,
	JUST_BREAK                               = 16,
	SLIP_DAMAGE                              = 17,
	GUARDING                                 = 18,
	TURN_BEGIN                               = 19,
	TURN_FINISH                              = 20,
	BEFORE_COMMAND_SELECTION                 = 21,
	BATTLING_SLIP_DAMAGE                     = 22,
	BATTLETURN_MODECHANGE                    = 23,
	FORWARD_AND_BACKUP                       = 24,
	COMMAND_PHASE                            = 25,
	ON_BREAKED                               = 26,
	BUFF_LIMIT_CALCULATING                   = 27,
	JUST_DYING                               = 28,
	ANY_CHARA_ACTION_LAST                    = 29,
	FIELD_ACTION                             = 50,
	FIELDING                                 = 51,
	ALWAYS                                   = 100,
	ESKILL_MAX                               = 101,
};
```
- `SkillAilmentType.m_InvokeTiming` は、`SkillAvailID.m_InvokeTiming` が使用するものと同じタイミング列挙ファミリを使用します。したがって、エンジンは、同じグローバル タイミング ボキャブラリに基づいて異常ティックとステージの実行をスケジュールします。

##### `EAILMENT_CONDITION_ID`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:531`より引用。
```cpp
enum class EAILMENT_CONDITION_ID : uint8
{
	None                                     = 0,
	BREAK_ENFORCER                           = 1,
	BREAK_DAMAGE                             = 2,
	SKILL_DAMAGE                             = 10,
	PHYSICAL_DAMAGE                          = 11,
	MAGICAL_DAMAGE                           = 12,
	TURN_PROBABLITY                          = 13,
	ENFORCER_ALIVE_LE_AILMENT                = 20,
	ENFORCER_ALIVE_LE_AVAIL                  = 21,
	DEAD_ENFORCER                            = 30,
	BREAK_OR_DEAD_ENFORCER                   = 31,
	CONCURRENT_LINKAGE                       = 40,
	CONCURRENT_LINKAGE_WITHOUT_PERMANENT     = 41,
	CAN_NOT_ACTION                           = 42,
	EAILMENT_CONDITION_MAX                   = 43,
};
```
- `m_RemoveConditions` は、この状態異常条件列挙型の生の値を保存します。これが、スリープ、カウントダウン、リンク解除状態などの行で、`PHYSICAL_DAMAGE`、`BREAK_OR_DEAD_ENFORCER`、`CAN_NOT_ACTION` などの正確な削除トリガーを表現できる理由です。

##### `ESKILL_AILMENT_CALC_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6208`より引用。
```cpp
enum class ESKILL_AILMENT_CALC_TYPE : uint8
{
	NONE                                     = 0,
	CALC_DAMAGE                              = 1,
	CALC_CRITICAL_DAMAGE                     = 2,
	CALC_WEAK_DAMAGE                         = 3,
	CALC_BREAK_DAMAGE                        = 4,
	CALC_ATRIBUTE_BUFF_DAMAGE                = 5,
	CALC_ATRIBUTE_DEBUFF_DAMAGE              = 6,
	CALC_ALLY_DAMAGE                         = 7,
	CALC_RACE_BUFF_DAMAGE                    = 8,
	CALC_BUFF_LIMIT_STATUS                   = 9,
	CALC_BUFF_LIMIT_STATUS_SUP               = 10,
	ESKILL_AILMENT_CALC_MAX                  = 11,
};
```
- `m_AilmentCalc` はこの列挙型の生の値を保存し、`SkillAvailID.m_CalcTypeAilment` は追加する状態異常の行に同じファミリーを使用しているようです。これが、ダメージ由来のバフ、制限修飾子、および関連する状態異常の計算が 1 つの数値語彙を共有するまさにその理由です。
- `m_ResistWeapon` および `m_ResistMagic` は、上記の `WeaponType` および `MagicType` に埋め込まれた正確な `EWEAPON_TYPE` および `EMAGIC_TYPE` 引用符と照らし合わせて読み取られる必要があります。これは、これらの配列は、たとえ状態異常行にスカラー ID ではなくマスクとして格納されている場合でも、同じファミリー順序によってキー設定されているためです。

#### フィールドごと
- `m_id`: **確認されました。** 状態異常行の主キー。 `SkillAvailID.m_AddAilment`、`m_SubAilment`、`SkillConditionList.m_AilmentTypes`、`SkillResistAilmentID.m_ResistAilments`、`m_RemoveAilment`、および `m_ChildAilment` によって参照されます。
- `m_Label`: 内部/エディターのラベル。多くの場合、これは、行の目的を識別する最も速く人間が判読できる方法です。
- `m_Resist`: **強力な証拠によって観察されました。** 通常の耐病ロジックがこの行に適用されるかどうか。これは、毒、出血、睡眠、麻痺、恐怖、暗闇などの古典的なネガティブステータスにも当てはまります。
- `m_Minus`: **観察されました。** ネガティブ/デバフのような状態異常フラグ。
- `m_Plus`: **観察されました。** 陽性/バフのような状態異常のフラグ。
- `m_Removable`: **観察。** 通常の治療/クリア行動で状態異常を除去できるかどうか。
- `m_Multiply`: **観察されました。** スタッキング/共存動作フラグ。多くの通常のバフおよびデバフ状態は true のままですが、一部のシングルトン制御状態は true のままです。
- `m_OverwriteTurn`: **観察されました。** 単に既存の状態を拡張または無視するのではなく、保存されたターンを上書きして再適用します。
- `m_ForceOverrideValue`: **観察されました。** 保存されている異常値とターン数を上書きして再適用します。
- `m_InvalidateDebuff`: **使用パターンによって確認されます。** デバフを無効化または抑制するインタラクションのデバフ クラス状態として行をマークします。 OT0は防御ダウン、攻撃ダウン、武器弱化、属性弱化状態などのデバフ列に設定します。
- `m_CanInactiveFlag`: **観察されました。** 非アクティブ/オフ状態の表現を許可します。 OT0 は、通常のバフ/デバフ行とその永続バリアントに設定します。これはまさに、状態の切り替えや記憶されているが非アクティブな動作が予想される場所です。
- `m_InvokeTiming`: **確認済み。** より豊富な `Kingship::ESKILL_INVOKE` 列挙型からのトリガー タイミング。 OT0 は、`BATTLING`、`ACTION_DAMAGE`、`SLIP_DAMAGE`、`FORWARD`、`ALWAYS` などのタイミングを使用します。
- `m_TargetStatus`: **確認済み。** `Character/CharaStatusID.m_id` は、具体的なステータス/リソースを変更するときに状態異常の影響を受けます。
- `m_TargetStatusArray`: **形状を確認。** 複数状態異常の追加ステータス対象。 OT0はほとんど使用しません。
- `m_AilmentCalc`: **確認済み。** `ESKILL_AILMENT_CALC_TYPE` からの状態異常側の計算モード。 OT0 はほとんどの場合 `NONE` のままですが、ダメージ由来および制限由来の状態ではより高い値が使用されます。
- `m_EfficacyStepRate`: **観察されました。** 状態異常の有効性のステップ レート スケーリング パラメーター。 OT0では珍しい。
- `m_ResistWeapon`: **確認された形状と観察された意味。** ブール型の武器ファミリー マスク。名前にもかかわらず、OT0 はこれをカウンターとインターセプトの行で多用し、州がどの武器ヒットファミリーに反応するかを定義します。
- `m_ResistMagic`: **確認された形状と観察された意味。** ブール要素ファミリー マスクは、魔法/要素カウンターとインターセプトに同じ方法で使用されました。
- `m_RemoveAilment`: **確認済み。** この異常が適用されると、最大 4 つの `SkillAilmentType.m_id` 行が削除されます。例: より強力なバリアントまたは無効化状態は、より弱い先行バージョンを削除できます。
- `m_TargetType`: **観察されました。** 小さな状態異常の内部ターゲット セレクター。 OT0 は 3 つの `...TARGET_IS_ENEMY_ALL` カウンター/インターセプト行でゼロ以外の値のみを使用するため、完全な戦闘ターゲット列挙型として扱わないでください。
- `m_RemoveConditions`: **観察されました。** 状態異常を除去または解除する方法を制御する条件オペコード。スリープは良い例です。その OT0 行はゼロ以外の削除条件を使用しているため、ダメージ/アクションでクリアできます。
- `m_RemoveParams`: **観察されました。** 削除条件スロットのパラメーター。
- `m_ChildAilment`: **確認済み。** この状態異常に連鎖する子の状態異常の `SkillAilmentType.m_id`。
- `m_TextID`: **確認済み。** 主に `GameTextUI` に表示される状態異常名のテキスト ID。例: 毒、暗闇、およびバフ状態のラベルはここで解決されます。
- `m_ExplanationTextID`: **確認済み。** 表示される状態異常の説明テキスト ID (これも主に `GameTextUI`)。
- `m_IconTexID`: **確認済み** メインの状態異常アイコンは `Texture/TextureID.m_id`。
- `m_IconSptTexID`: **確認済み** サポート/二次異常アイコンの `Texture/TextureID.m_id`。 OT0 では、通常、`m_IconTexID` と一致します。
- `m_AddEffect`: **確認済み** 状態異常が追加されると `Effect/EffectList.m_id` が生成されます。
- `m_CharacterEffect`: **確認済み。** 状態異常がアクティブなままである間、状態異常にかかったキャラクターに `Effect/EffectList.m_id` が付加されます。
- `m_FieldEffect`: **確認済み。** `Effect/EffectList.m_id` は、この状態異常のフィールド マップのバリアントによって使用されます。
- `m_InvokeEffect`: **確認済み。** `Effect/EffectList.m_id` は、状態異常がトリガーまたはティックされると生成されます。
- `m_OnGroundInvoke`: **観察されました。** フィールドと地上の使用例など、通常の戦闘アクションのタイミング以外での状態異常の発動/ティック動作を許可します。
- `m_AddEffectSound`: **確認済み** 状態異常が適用されると`Sound/SoundList.m_id`が再生されます。
- `m_InvokeSound`: **確認済み。** 状態異常がトリガーまたはティックされると、`Sound/SoundList.m_id` が再生されます。
- `m_ActionID`: **確認済み。** 状態異常状態の `Battle/BattleActionID.m_id` アニメーション フック。
- `m_ActionPriority`: **OT0 で未使用であることが確認されました。** 構造体に存在しますが、OT0 はゼロのままにします。
- `m_ReplaceCharaID`: **OT0 では未使用であることが確認されました。** 構造体に存在しますが、OT0 はゼロのままにします。
- `m_CountDown`: **観察されました。** 状態異常をカウントダウン形式としてマークします。
- `m_NoCount`: **観察されました。** 通常のターン/カウントの消費を防ぎます。 OT0 は、吸収、保証/確率回避、ガッツ、およびマスタリーのような状態でこれを使用します。
- `m_StatusDetailSort`: **観察されました。** 状態異常/詳細表示用の UI 順序付けキー。
- `m_IgnoreLimit`: **観察されました。** 通常のバフ/デバフ上限ルールをバイパスします。 OT0 は、SP コスト修正、チャームのような状態、ダメージ制限の増加、および同様の特殊な場合のオーラにそれを使用します。
- `m_RemainDead`: **観察。** 死亡したユニットまたは死亡した場合でも、状態異常を意味のあるものに保ちます。 OT0 は、リレイズに似た状態といくつかの特別な永続状態でこれを使用します。
- `m_IsNotMemorize`: **観察されました。** まれなメモリ/スナップショット除外フラグ。 OT0 は 2 行でのみ使用します。
- `m_AbsorbHealStatus`: **確認済み。** `Character/CharaStatusID.m_id` は吸収/回復スタイルの状態異常ロジックによって回復されました。
- `m_NotRace`: **観察されました。** `m_EnemyRace` の競合フィルターを反転または無効にします。 OT0では珍しい。
- `m_EnemyRace`: **観察されました。** 状態異常ロジック用の小さな競合フィルター配列。 OT0では珍しい。
- `m_ForceSupportAbility`: **OT0 で未使用を確認。** 常に false。
- `m_ForceCommandAbility`: **観察されました。** 毒、出血、麻痺、暗闇などの標準的なネガティブステータスに設定されるレアフラグ。最も安全な解釈は、これらの行はコマンド機能対話パスに保持されますが、OT0 は SDK ダンプ内でより適切な名前の呼び出し元を提供しないということです。
- `m_WeaponAppendType`: **観察されました。** 武器追加動作タイプ ID。名前にもかかわらず、OT0 値はきれいな `EWEAPON_TYPE` マッピングではないため、これを生の武器列挙型ではなく、より高レベルの追加動作セレクターとして扱います。
- `m_MagicAppendType`: **観察されました。** 要素追加動作タイプ ID。繰り返しますが、OT0 値は単純な `EMAGIC_TYPE` マッピングではありません。
- `m_DuplicateAilment`: **観察されました。** 同じ状態異常の重複インスタンスを許可します。 OT0 は 1 行のみで使用します。
- `m_SkillReplace`: **OT0 では未使用を確認。** 予約済みのスキル置換フック。

#### 具体的な OT0 の例
- プレーン ステータス ファミリは簡単に直接確認できます。`SkillAilmentType#1 POISON` は `Poison`、`#8 HIGH_POISON` は `Toxin`、`#2 BLEED` は `Bleeding`、`#6 FEAR` は `Terror` です。
- `m_RemoveConditions` は装飾的ではありません。 `SkillAilmentType#4 SLEEP` は `m_RemoveConditions = [10,0]` を使用し、`#285 DEEP_SLEEP` は `[1,0]` を使用します。 OT0 は、同じ広範なスリープ テーマ内の異なる除去/ブレイク ロジック ファミリを明確に区別しています。
- カウンタ/インターセプト ファミリは、`m_ResistWeapon` および `m_ResistMagic` が通常の抵抗テーブルとしてよりもリアクション マスクとして読み取られる方が適切である理由を示しています。 `#52 COUNTER_SWORD` および `#70 INTERCEPT_SWORD` は、これらのファミリーの代表的な武器固有の行です。
- `m_CountDown` は実際のゲームプレイ フラグです。 `SkillAilmentType#20 COUNTDOWN` は `Fatal Sentence` ですが、`#43 DELAYED_SKILL` と `#44 DELAYED_RANGE_SKILL` は両方とも、カウント終了時にキューに登録されたスキルをトリガーする可視の `Countdown` バリアントです。
- `m_ForceCommandAbility` はランダムノイズではありません。 OT0 は、`#1 POISON`、`#2 BLEED`、`#5 PARALYSIS`、`#11 DARKNESS` などの古典的な負のステータスに設定します。
- `m_IgnoreLimit` も本物です。 `#203 BUFF_SP_COST_REDUCE (SP Cost Down)`、`#226 BUFF_REDUCE_DAMAGE_HP (Reduce Damage)`、および `#397 INCREASE_DAMAGE_LIMIT (Surpassing Power)` はすべてこれを保持しており、通常のバフ/デバフ キャップの処理をバイパスするという考えに適合します。
- `m_RemainDead` は 1 行に限定されません。 `#283 RERAISE_BUFF (Encore)` と `#503 RERAISE_BUFF_LAST_DIED (Last Man Standing)` は両方ともこれを使用します。これはまさに、無力化後も依然として重要な状態から期待されるものです。

#### 『大陸の覇者』のみの追加フィールド
- `m_IsBuffDebuff`: **『大陸の覇者』側のDBスキーマから確認済み。** 明示的なバフ/デバフ分類フラグ。
- `m_CompareTurn`: **『大陸の覇者』側のDBスキーマから確認済み。** 追加のターン比較パラメーター。
- `m_CompareOverrideValue`: **『大陸の覇者』側のDBスキーマから確認済み。** 追加の値比較パラメーター。
- `m_DisableBoostAnim`: **名前と『大陸の覇者』側の使用例から確認。** 状態異常が発生したときのブースト アニメーションを抑制します。
- `m_StatusDetailAllName`: **『大陸の覇者』側のDBスキーマから確認。** すべてのターゲット表示のステータス詳細名のテキスト ID が強化されました。
- `m_StatusDetailName`: **『大陸の覇者』側のDBスキーマから確認済み。** 単一ターゲット表示のステータス詳細名のテキスト ID が強化されました。
- `m_StatusDetailCommentTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** ステータス詳細 UI のコメント テキスト。
- `m_StatusDetailCommentCalcTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** ステータス詳細 UI の計算を認識するコメント テキスト。
- `m_StatusDetailCommentRatedTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** ステータス詳細 UI の評価されたコメント テキスト。
- `m_StatusDetailCommentNowRatedTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** ステータス詳細 UI の現在値評価コメント テキスト。
- `m_StatusDetailCommentFixedTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** ステータス詳細 UI の固定値のコメント テキスト。
- `m_StatusDetailAllCommentTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** オールターゲットのコメント テキスト。
- `m_StatusDetailAllCommentCalcTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** オールターゲット計算対応のコメント テキスト。
- `m_StatusDetailAllCommentRatedTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** オールターゲットの評価コメント テキスト。
- `m_StatusDetailAllCommentNowRatedTextID`: **『大陸の覇者』側のDBスキーマから確認済み。** オールターゲットの現在の評価のコメント テキスト。
- `m_StatusDetailAllCommentFixedTextID`: **『大陸の覇者』側のDBスキーマから確認。** オールターゲットの固定値コメント テキスト。
- `m_AllowAddAilmentOnDead`: **『大陸の覇者』側のDBスキーマから確認済み。** デッドユニットへの適用を許可します。
- `m_MaxUseCount`: **『大陸の覇者』側のDBスキーマから確認。** 状態異常が発生できる最大回数。
- `m_IsAttributeCheckAppendType`: **名前と『大陸の覇者』側の使用例から観察。** 属性を認識した追加タイプの検証。
- `m_BindWeakID`: **名前と『大陸の覇者』側の使用例から観察。** 弱さバインディングのヘルパー行またはキー。
- `m_IsAdditionWeak`: **名前と『大陸の覇者』側の使用例から観察。** 状態異常を 大陸の覇者 の追加弱点メカニズムの一部としてマークします。
- `m_AilmentTagID`: **『大陸の覇者』側のDBスキーマから確認済み。** 状態異常のタグ/グループ識別子。
- `m_Attribute`: **『大陸の覇者』側のDBスキーマから確認済み。** 大陸の覇者 異常ロジックの属性セレクター。
- `m_TargetAilment`: **『大陸の覇者』側のDBスキーマから確認。** 変換/相互作用ロジックのターゲット 状態異常 ID。
- `m_ToUnlimited`: **名前と『大陸の覇者』側の使用例から観察。** 無制限の期間に変換します。
- `m_Parallel`: **名前と『大陸の覇者』側の使用例から観察。** 関連する状態の並列共存を許可します。
- `m_PhaseOver`: **名前と『大陸の覇者』側の使用例から観察。** フェーズ遷移を通じて持続します。
- `m_SkillFilterType`: **『大陸の覇者』側のDBスキーマから確認済み。** `SkillFilterType.m_id` は 『大陸の覇者』側のフィルター UI および分類によって使用されます。

### `SkillAilmentOffsets`
モデルごとの状態異常アンカーのプリセット。このテーブルは `Skill/` の下に存在しますが、最も明らかなダウンストリーム OT0 コンシューマは `SkillID` 自体ではなく `Enemy/EnemyTypeID.m_AilmentOffset` です。

- **OT0 行:** `853`。
- **『大陸の覇者』行数:** `922`。
- **OT0 SDK 行構造体:** `FSkillAilmentOffsetsBase`。
- 状態異常のアイコン/効果には、敵の体の形状ごとに異なるアンカー位置が必要なため、この行が存在します。 OT0 には、すべての状態異常ファミリーのアンカー ペアを含む 1 つのプリセット行が保存されます。

#### フィールドごと
- `m_id`: **確認済み。** オフセット プリセット ID。 OT0 `Enemy/EnemyTypeID.m_AilmentOffset` はここを指します。
- `m_PoisonX`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行にポイズン アンカー X。
- `m_PoisonY`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行にあるポイズン アンカー Y。
- `m_BleedX`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行のアンカー X をブリードします。
- `m_BleedY`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のアンカー Y をブリードします。
- `m_SilenceX`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行のアンカー X を沈黙させます。
- `m_SilenceY`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行のアンカー Y を沈黙させます。
- `m_SleepX`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のアンカー X をスリープします。
- `m_SleepY`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のアンカー Y をスリープします。
- `m_PalalysisX`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行の麻痺アンカー X。
- `m_PalalysisY`: **フィールド名と下流の使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行の麻痺アンカー Y。
- `m_FearX`: **フィールド名と下流の使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行にある恐怖アンカー X。
- `m_FearY`: **フィールド名と下流の使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行にある恐怖アンカー Y。
- `m_WasteX`: **フィールド名と下流の使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行の廃棄物/SP廃棄物アンカー X。
- `m_WasteY`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行の廃棄物/SP 廃棄物アンカー Y。
- `m_DarknessX`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のダークネス アンカー X。
- `m_DarknessY`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のダークネス アンカー Y。
- `m_HeadacheX`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行の頭痛アンカー X。
- `m_HeadacheY`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行の頭痛アンカー Y。
- `m_EnergyDrainX`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のエネルギー ドレイン アンカー X。
- `m_EnergyDrainY`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のエネルギー ドレイン アンカー Y。
- `m_RegenerateX`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行にアンカー X を再生成します。
- `m_RegenerateY`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行にアンカー Y を再生成します。
- `m_ProvokeX`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行でアンカー X を誘発します。
- `m_ProvokeY`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行でアンカー Y を誘発します。
- `m_CounterX`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のカウンター アンカー X。
- `m_CounterY`: **フィールド名と下流の使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のカウンター アンカー Y。
- `m_InterceptX`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行のアンカー X をインターセプトします。
- `m_InterceptY`: **フィールド名とダウンストリームの使用法によって確認されます。** その戦闘機モデル/タイプのプリセット行のアンカー Y をインターセプトします。
- `m_BreakX`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行でアンカー X を分割します。
- `m_BreakY`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のアンカー Y をブレークします。
- `m_CountDownX`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のカウントダウン アンカー X。
- `m_CountDownY`: **フィールド名とダウンストリームの使用法によって確認されます。** そのバトラー モデル/タイプのプリセット行のカウントダウン アンカー Y。
- `m_PerfectInterceptScale`: **観察されました。** 完全切片スタイル表示のた​​めの追加のスケール値。 OT0 はすべての行にこれを設定します。

### `SkillAilmentSkillReplaceList`
小規模な「状態異常起点のスキル差し替え」補助テーブルです。OT0 では事実上休眠状態ですが、スキーマ自体は文書化する価値があります。

- **OT0 行:** `1`。
- **『大陸の覇者』行数:** `12`。
- **OT0 SDK 行構造体:** `FSkillAilmentSkillReplaceListBase`。

#### フィールドごと
- `m_id`: 置換パッケージの主キー。
- `m_SkillList`: **観察されました。** 置換先となる `SkillID` 行の一覧。
- `m_AilmentType`: **確認済み。** 置換パッケージをトリガーする `SkillAilmentType.m_id`。

### `SkillResistList`
インラインのパーセンテージベースの武器/要素耐性ペイロード。このテーブルは `EnemyWeakID` とよく混同されますが、保存されている値は、これらが異なるシステムであることを示しています。

- **OT0 行:** `215`。
- **『大陸の覇者』行数:** `241`。
- **OT0 SDK 行構造体:** `FSkillResistListBase`。

#### フィールドごと
- `m_id`: 抵抗ペイロード行の主キー。
- `m_ResistWeapon`: **確認済み。** 武器ファミリーごとの直接パーセンテージ/デルタ配列。 OT0 値には、`50`、`27`、`22`、`12`、`2`、`-40`、`-50`、`-100` などのエントリが含まれるため、これは明らかに `ResistType` ID 配列ではありません**。
- `m_ResistMagic`: **確認済み。** 要素ファミリーによる直接パーセンテージ/デルタ配列。`m_ResistWeapon` と同じセマンティクス。

### `SkillResistAilmentID`
1 つのインライン基本レート配列ではなく、明示的な状態異常/レートのペアを必要とするスキルと敵の行によって使用される、パッケージ化された状態異常耐性行。

- **OT0 行:** `74`。
- **『大陸の覇者』行数:** `60`。
- **OT0 SDK 行構造体:** `FSkillResistAilmentIDBase`。

#### フィールドごと
- `m_id`: 耐病パッケージの主キー。
- `m_ResistAilments`: **確認済み。** パッケージの対象となる最大 16 行の `SkillAilmentType.m_id` 行のリスト。
- `m_ResistRate`: **確認済み。** `m_ResistAilments` の一致率配列。配列は並列です。

### `SkillHitRateList`
再利用可能なマルチヒットおよび繰り返し解決確率テーブル。 `SkillAvailID.m_HitTypes` はここを指します。

- **OT0 行:** `68`。
- **『大陸の覇者』行数:** `72`。
- **OT0 SDK 行構造体:** `FSkillHitRateListBase`。

#### フィールドごと
- `m_id`: ヒット率パッケージの主キー。
- `m_MinCount`: **観察されました。** ランダム継続が考慮される前の最小保証数。
- `m_CountAilment`: **形状を確認しました。** 追加の状態異常/カウント参照チャネル。 OT0 はすべての行でゼロのままですが、大陸の覇者 はそれを使用します。
- `m_HitRates`: **観察されました。** 追加のヒット ロールまたは反復解決数を制御する 16 スロットの確率テーブル。
- `m_Shuffle`: **観察されました。** ヒット率パターンの動作をランダム化/シャッフルします。

### `SkillIconList`
小さなペアのアイコンバンク。本物ですが、OT0 の `SkillID.m_SkillIconID` の主なターゲットではありません。

- **OT0 行:** `11`。
- **『大陸の覇者』行数:** `12`。
- **OT0 SDK 行構造体:** `FSkillIconListBase`。

#### フィールドごと
- `m_id`: アイコン ペアの主キー。
- `m_BattleIconTexID`: **確認済み** 戦闘中アイコンは `Texture/TextureID.m_id`。
- `m_MenuIconTexID`: **確認済み** メニュー アイコンの `Texture/TextureID.m_id`。

### `SkillUniqueIconID`
特別なコマンド パネルまたはバフ表示用の独自のアイコン/テキスト オーバーライド テーブル。

- **OT0 行:** `39`。
- **『大陸の覇者』行数:** `137`。
- **OT0 SDK 行構造体:** `FSkillUniqueIconIDBase`。
- OT0 `SkillID` 行は実際にはこのテーブルを使用しませんが、テーブル自体には完全にデータが入力され、明確に構造化されています。

#### フィールドごと
- `m_id`: 固有のアイコン行の主キー。
- `m_TextID`: **観察されました。** 固有のアイコンのテキスト ラベル。いくつかの OT0 ID は `GameTextUI` (`65490 -> Hang Tough` など) で適切に解決されますが、多くの OT0 ID は現在エクスポートされている OT0 テキスト バンクには存在しません。未解決のサブセットを、フィールドが未使用であることの証拠としてではなく、レガシー/共有コンテンツとして扱います。
- `m_SkillIconTexID`: **確認済み** 独自のアイコン テクスチャの `Texture/TextureID.m_id`。
- `m_OverrideCommandPanel`: **観察されました。** コマンド パネルでこの固有のアイコン/テキストを使用します。
- `m_OverrideInBattle`: **観察されました。** このユニークなアイコンを戦闘 UI で使用してください。
- `m_OverrideBuff`: **観察されました。** このユニークなアイコンをバフ/状態表示に使用します。

### `SkillSupplyItemGroup`
OT0村/供給ヘルパーテーブル。スキルのオペコードを報酬宝くじグループにバインドします。

- **OT0 行:** `45`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** `FSkillSupplyItemGroupBase`。
- **関連する OT0 SDK 列挙型:** `ESKILL_MODIFY_TYPE`。

#### 具体的な OT0 の例
- 最初に表示されているマテリアル ファミリは、パターンを表示するのにすでに十分です。`SkillSupplyItemGroup#100/#101` は `Timber Supply` / `Timber Supply+` を指し、`#200/#201` は `Top Timber Supply` / `Top Timber Supply+` を指します。
- 石、布、鉱石、その他の村の素材でも同じパターンが繰り返されます。言い換えれば、この表は一般的な戦闘報酬リストではありません。これは、トリガースキルをキーとして厳選された村の供給宝くじです。
- OT0 は、これらの行のほとんどに `m_SkillModifyType = 201` を使用します。これは、`ESKILL_MODIFY_TYPE` の `ITEM_SUPPLY` ブランチと一致します。

#### フィールドごと
- `m_id`: 供給品目グループの主キー。
- `m_SkillId`: **確認済み** この抽選グループを発動するスキルの `SkillID.m_id`。
- `m_SkillModifyType`: **列挙型/値の重複によって確認されました。** `ESKILL_MODIFY_TYPE` のエコノミー オペコード。 OT0 は主に `201 ITEM_SUPPLY` を使用し、いくつかの `208 FARM_HARVEST_ITEM_AMOUNT_LOTUP` 行と `254 ITEM_SUPPLY_LOT_IN_GROUP` 行を使用します。
- `m_Lot`: **観察されました。** グループレベルの宝くじの重みまたは乗数。 OT0 は、すべての行で `100` のままにします。

### `SkillSupplyItemList`
OT0村/物資の抽選エントリー。これは、`SkillSupplyItemGroup` が選択するペイロード テーブルです。

- **OT0 行:** `166`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** `FSkillSupplyItemListBase`。

#### フィールドごと
- `m_id`: 宝くじエントリーの主キー。
- `m_GroupId`: **確認しました。** `SkillSupplyItemGroup.m_id` 親グループ。
- `m_RewardId`: **強力な証拠によって観察されました。** 報酬型の識別子。 OT0 は、お金/葉のエントリに `1000` を使用し、アイテムと報酬のエントリに `1001` を使用します。
- `m_ItemId`: **強力な証拠によって観察されました。** アイテム報酬の場合は `Item/ItemList.m_id`、金銭報酬の場合は `0`。
- `m_Values`: **観察されました。** 報酬額またはアイテム数。 `m_RewardId = 1000` の場合、これは葉の量です。 `1001` の場合、商品数量です。
- `m_Lot`: **観察。** 親グループ内のエントリーごとの宝くじの重み。
- `m_IsUnique`: **観察されました。** 固有の報酬フラグ。 OT0 は、グループ `4000` 内の 2 つのエントリのみでこれを使用します。

### `SkillTargetShopList`
村/貿易スキルのOT0ターゲットショップヘルパー行。

- **OT0 行:** `24`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** `FSkillTargetShopListBase`。
- **関連する OT0 SDK 列挙型:** `ESHOP_TYPE`。

#### このテーブルで使用される正確な OT0 SDK 列挙型引用符
##### `ESHOP_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6100`より引用。
```cpp
enum class ESHOP_TYPE : uint8
{
	eNONE                                    = 0,
	eINN                                     = 1,
	eBLACKSMITH                              = 2,
	eBAR                                     = 3,
	eARENA                                   = 4,
	eTOWER                                   = 5,
	eITEM                                    = 6,
	eGENERAL_MARCHANT                        = 7,
	eJOBSELECTER                             = 8,
	eRECOVERY_POINT                          = 9,
	eVILLAGE_EDIT                            = 10,
	eSAVE_POINT                              = 11,
	eCook                                    = 12,
	eFarm                                    = 13,
	eLINER                                   = 14,
	eRANCH                                   = 15,
	eACHIEVE                                 = 16,
	eVILLAGE_BASE                            = 17,
	eGramophone                              = 18,
	eMixing                                  = 19,
	eBEAUTY_SALON                            = 20,
	eEVENT_ROLLBACK                          = 21,
	eTRADE                                   = 22,
	eSACRED_TORCH                            = 23,
	eTRAINING_CENTER                         = 24,
	eMUSEUM                                  = 25,
	eNOTE                                    = 26,
	eMONSTER_ARENA                           = 27,
	eBUILDING_WORKSHOP                       = 28,
	eNOTE_BOOKSHELF                          = 29,
	ESHOP_MAX                                = 30,
};
```
- `SkillTargetShopList.m_ShopType` は、生の `ESHOP_TYPE` 数値を保存します。ヘルパー行が存在するため、スキルはショップ ファミリだけでなく、スキルがターゲットとする具体的なマップ コンテキストもバインドできます。
- これが、OT0 が宿屋や鍛冶屋などの汎用システムと、`eVILLAGE_BASE` や `eTRADE` などの村固有のヘルパーに同じ enum ファミリを使用できる理由です。

#### 具体的な OT0 の例
- `#1..#16` 行は、`Savvy Shopper`、`Master Haggler`、`Savvy Seller`、`Master Salesman`、`Appraiser`、`Grand Appraiser`、`Wholesale Expert`、`Master Tradesman` などの取引価格スキルをカバーします。これらはすべて `m_ShopType = 22`、つまり `eTRADE` を使用します。
- 行 `#17..#24` は、`Negotiator`、`Fixer`、`Cook`、`Grand Chef` などの村ベースのユーティリティ スキルをカバーします。これらは `m_ShopType = 17`、つまり `eVILLAGE_BASE` を使用します。
- すべての原版 OT0 行は `m_MapId = 900002` を使用します。そのため、このテーブルはユニバーサル ショップ カタログというよりは、1 つのマップ コンテキストを範囲とするビレッジ システム ヘルパー テーブルに似ています。

#### フィールドごと
- `m_id`: target-shop 行の主キー。
- `m_SkillId`: **確認しました。** ショップ対象スキルの `SkillID.m_id`。
- `m_ShopType`: **確認済み** 対象ショップの `ESHOP_TYPE` セレクター。 OT0 は `17 (eVILLAGE_BASE)` および `22 (eTRADE)` を使用します。
- `m_MapId`: **確認済み** `Map/MapListTable.m_id` は、ターゲット ショップのマップ コンテキストを提供します。 OT0 は、すべての行で村のベース マップである `900002` を使用します。

### `SkillTargetFarmItemList`
特定の村の農場アイテムを追加または対象とするスキルの OT0 牧場/農場ヘルパー行。

- **OT0 行:** `2`。
- **『大陸の覇者』行数:** が 大陸の覇者 `Skill/` フォルダーに存在しません。
- **OT0 SDK 行構造体:** `FSkillTargetFarmItemListBase`。
- **関連する OT0 SDK 列挙型:** `ESKILL_MODIFY_TYPE`。

#### 具体的な OT0 の例
- 両方のストック OT0 行は `Sheep King` ファミリに属します。`SkillTargetFarmItemList#1` は `SkillID#900938 Sheep King` を指し、`#2` は `SkillID#903901 Sheep King+` を指します。
- 両方の行が `m_VillageFarmItemId = 103` をターゲットとしています。セカンダリ ID `105` および `106` は、ストック OT0 内にほとんど空のプレースホルダーとして存在します。そのため、パターンを複製する際には、このフィールドを慎重に扱う必要があります。

#### フィールドごと
- `m_id`: ファームターゲット行の主キー。
- `m_SkillId`: **確認済み** 農場対象スキルの `SkillID.m_id`。
- `m_SkillModifyType`: **確認済み。** OT0 は `215` を使用します。これは、`ESKILL_MODIFY_TYPE` の `RANCH_APPEND_ITEM` です。
- `m_VillageFarmItemId`: **確認しました。** メイン対象ファーム アイテムの `Village/VillageFarmItem.m_id`。
- `m_AdditionalVillageFarmItemId`: **確認済み。** セカンダリ `Village/VillageFarmItem.m_id`。 OT0 では、参照される行は空のプレースホルダー (`105` および `106`) であるため、このフィールドにすでに意味のあるライブ コンテンツがストック OT0 にあるとは想定しないでください。

### `SkillVisitorList`
訪問者/召喚ヘルパーの行。 OT0 はテーブルをほとんど使用しませんが、意図した形状を示すには 1 つのライブ行で十分です。

- **OT0 行:** `2`。
- **『大陸の覇者』行数:** `7`。
- **OT0 SDK 行構造体:** `FSkillVisitorListBase`。

#### 具体的な OT0 の例
- OT0 には、ゼロ以外のライブ訪問者行が `SkillVisitorList#1` 1 つだけあります。
- その列は、敵ラベルが `Summon_PC072_Linde` である `m_EnemyID = 33000` を召喚します。
- その配置データも単純なフィラーではありません: `m_OffsetsX[0] = -500.0` および `m_IconID = 1000`。これは、訪問者テーブルが召喚スタイルのゲストのスポーン レイアウトと UI ID の両方を処理していることを示す強力なヒントです。

#### フィールドごと
- `m_id`: 訪問者行の主キー。
- `m_EnemyID`: **OT0で確認** ビジターバトラーの`Enemy/EnemyID.m_id`。 OT0 のライブ非ゼロ行は `33000`、つまり `Summon_PC072_Linde` を使用します。
- `m_EscortNum`: **観察されました。** スロットごとのエスコート数の配列。
- `m_EscortType`: **観察されました。** スロットごとのエスコート/レイアウト タイプの配列。 OT0 はゼロのままにします。
- `m_OffsetsY`: **観察されました。** 訪問者の配置のためのスロットごとの Y オフセット。
- `m_OffsetsX`: **観察されました。** 訪問者の配置のためのスロットごとの X オフセット。
- `m_CallSE`: **観察されました。** スロットごとのコール サウンド ID。 OT0 はゼロのままにします。
- `m_IconID`: **未解決。** 訪問者 UI アイコンの識別子。 OT0 はライブ行の値 `1000` のみを使用し、ターゲット テーブルは現在のエクスポートでは証明されていません。

#### 『大陸の覇者』側のDBスキーマの変更
- `m_EnemyType`: **『大陸の覇者』側のDBスキーマから確認。** 大陸の覇者 は、直接の OT0 スタイルの `EnemyID` の代わりに、ここに敵のタイプを保存します。

### `SkillEnhancedList`
拡張ヘルパーテーブル。 OT0 は事実上空のままですが、スキーマは単純です。

- **OT0 行:** `1`。
- **『大陸の覇者』行数:** `10`。
- **OT0 SDK 行構造体:** `FSkillEnhancedListBase`。

#### フィールドごと
- `m_id`: 拡張パッケージの主キー。
- `m_EnhancedSkills`: **観察されました。** 強化後スキル ID の一覧。OT0 には 0 行しかなく、参照できる標準コンテンツはありません。

### `SpecialSkillID`
特技マスターキーテーブル。 OT0 はここに ID のみを保存します。実際のレベルごとのマッピングは `SpecialSkillGrowthList` にあり、プレーヤー バインディングは `Character/CharaPlayerSpecialSkillList` にあります。

- **OT0 行:** `86`。
- **『大陸の覇者』行数:** `266`。
- **OT0 SDK 行構造体:** `FSpecialSkillIDBase`。

#### フィールドごと
- `m_id`: 特殊スキル ファミリの主キー。

### `SpecialSkillGrowthList`
特殊スキル ファミリからそのレベルで付与された具体的な `SkillID` 行へのレベルごとのマッピング。

- **OT0 行:** `497`。
- **『大陸の覇者』行数:** `5301`。
- **OT0 SDK 行構造体:** `FSpecialSkillGrowthListBase`。

#### フィールドごと
- `m_id`: 成長行の主キー。
- `m_SpecialSkillID`: **確認しました。** 成長中の特技ファミリーの `SpecialSkillID.m_id`。
- `m_Level`: **確認済み** 成長レベル。 OT0 は通常、プレイヤー向けの EX/スペシャル スキルに `1..3` を使用しますが、ファイル自体には `10` レベルまでの行が含まれています。
- `m_SkillID`: **確認済み。** `SkillID.m_id` は、その特殊スキル レベルで付与または代替されます。
- `m_DisplayPriority`: **観察されました。** 成長行間の UI ソート キー。 OT0 はほとんどの場合 `0` のままですが、値 `20..90` も使用します。

#### 『大陸の覇者』のみの追加フィールド
- `m_InitialGaugeValue`: **『大陸の覇者』側のDBスキーマで確認** 特技レベルの開始ゲージ。
- `m_GaugeRegenerateValue`: **『大陸の覇者』側のDBスキーマから確認。** サイクル/ターンごとのゲージの回復。
- `m_CountLimit`: **『大陸の覇者』側のDBスキーマから確認済み。** 使用量の上限。
- `m_RewardTypes`: **『大陸の覇者』側のDBスキーマから確認。** 成長行の報酬型配列。
- `m_ItemIDs`: **『大陸の覇者』側のDBスキーマから確認済み。** `m_RewardTypes` と同等の報酬アイテム ID。
- `m_ItemCounts`: **『大陸の覇者』側のDBスキーマから確認済み。** 報酬は報酬配列と並行してカウントされます。
- `m_AvailValues`: **『大陸の覇者』側のDBスキーマから確認済み。** 基礎となるAvailズ ロジックに渡されるレベルごとの調整値。
- `m_AvailHitTypes`: **『大陸の覇者』側のDBスキーマから確認。** レベルごとのヒット タイプのオーバーライド。
- `m_AilmentValues`: **『大陸の覇者』側のDBスキーマから確認。** レベルごとの状態異常の効力がオーバーライドされます。
- `m_AilmentTurns`: **『大陸の覇者』側のDBスキーマから確認済み。** レベルごとの状態異常期間のオーバーライド。
- `m_DamageLimitUp`: **『大陸の覇者』側のDBスキーマから確認。** レベルごとのダメージ上限の増加。

### `ClassSkillConditionList` (『大陸の覇者』のみ)
クラススキル使用条件用の『大陸の覇者』専用ヘルパーテーブル。ここに同梱された SDK ダンプには一致する専用 OT0 SDK 行構造体が見つからなかったため、以下のフィールドの意味は 『大陸の覇者』側のDBスキーマとフィールド名から派生しています。

#### フィールドごと
- `m_id`: クラス-スキル条件行の主キー。
- `m_UseCondition`: **フィールド名と 大陸の覇者 ロールから観察。** クラススキル システムによって評価された条件 ID。
- `m_ConditionText`: **フィールド名と 大陸の覇者 ロールから観察。** 条件に対してプレイヤーに表示されるテキスト ID。

### `SkillEffectiveLinkageList` (『大陸の覇者』のみ)
大陸の覇者 専用の連鎖プレゼンテーション ヘルパー。このテーブルは、`SkillEffectiveID.m_SkillEffectiveLinkageID` によって参照されます。

#### フィールドごと
- `m_id`: リンケージ行の主キー。
- `m_HoldTime`: リンクされたプレゼンテーション グループの合計保持時間。
- `m_AvailTime`: リンクされたプレゼンテーション グループの可用性/解決時間。
- `m_OffsetX`: リンクされたプレゼンテーション配置のグループレベルの X オフセット。
- `m_OffsetY`: リンクされたプレゼンテーション配置のグループレベルの Y オフセット。
- `m_LimitMax`: リンケージグループの観測上限フィールド。
- `m_LinkageType`: 監視されたリンケージ モード セレクター。
- `m_Effectives`: **IDの重複により確認** 本連携パッケージで発行する`SkillEffectiveID.m_id`行は最大16行となります。

### `SkillFilterCategory` (『大陸の覇者』のみ)
大陸の覇者 スキル フィルター UI の 大陸の覇者 専用フィルター カテゴリの凡例。

#### フィールドごと
- `m_id`: フィルター カテゴリの主キー。
- `m_Name`: カテゴリ名のテキスト ID。
- `m_Priority`: カテゴリの UI ソート優先度。

### `SkillFilterType` (『大陸の覇者』のみ)
『大陸の覇者』のみのフィルター タイプの凡例。 `SkillID.m_ForcedSkillFilter` と `m_ExclusionSkillFilter` はここを指しており、各フィルター タイプはフィルター カテゴリに属します。

#### フィールドごと
- `m_id`: フィルター タイプの主キー。
- `m_Name`: フィルター名のテキスト ID。
- `m_Priority`: フィルター タイプの UI ソート優先度。
- `m_Category`: **確認済み** `SkillFilterCategory.m_id` 親カテゴリ。

### `SkillModifyType` (『大陸の覇者』のみ)
大陸の覇者専用 bridge table from modify opcodes to filter types. The row IDs line up exactly with the nonzero `SkillAvailID.m_ModifyType` values used by 大陸の覇者.

#### フィールドごと
- `m_id`: **確認しました。** `SkillAvailID.m_ModifyType` に対応するオペコード キーを変更します。
- `m_SkillFilterType`: **ID の重複によって確認されました。** 変更オペコードに関連付けられた最大 5 つの `SkillFilterType.m_id` 行。

### `SpecialSkillExtendCategory` (『大陸の覇者』のみ)
特殊スキル拡張カテゴリの 大陸の覇者 専用凡例テーブル。

#### フィールドごと
- `m_id`: 拡張カテゴリの主キー。
- `m_Name`: カテゴリ名のテキスト ID。

### `SpecialSkillExtendType` (『大陸の覇者』のみ)
拡張エフェクト タイプの 『大陸の覇者』のみの凡例。 `SpecialSkillExtendList.m_ExtendType` はここを指します。

#### フィールドごと
- `m_id`: 拡張子タイプの主キー。
- `m_Category`: **確認しました。** `SpecialSkillExtendCategory.m_id`。
- `m_SubCategory`: 親カテゴリ内のサブタイプ セレクターが確認されました。
- `m_EffectName`: エフェクト名のテキスト ID。
- `m_DetailText`: エフェクトの詳細/説明のテキスト ID。
- `m_EffectValue`: この拡張機能タイプのプライマリ数値ペイロード。
- `m_SubEffectValue`: この拡張機能タイプのセカンダリ数値ペイロード。
- `m_AutoLearn`: 自動ロック解除フラグ。
- `m_SubText`: メインエフェクトの名前/詳細と一緒に使用される追加のテキスト ID。

### `SpecialSkillExtendList` (『大陸の覇者』のみ)
大陸の覇者 専用の特殊スキルごとの拡張マッピング。これは、特殊スキルがどの拡張タイプを取得し、どのようにロック解除され、どの報酬がバンドルされているかを示すペイロード テーブルです。

#### フィールドごと
- `m_id`: 拡張行の主キー。
- `m_TargetSSkill`: **確認しました。** 拡張機能の対象となる `SpecialSkillID.m_id`。
- `m_ExtendType`: **確認済み** 拡張効果を説明する `SpecialSkillExtendType.m_id`。
- `m_GetPlaceText`: 拡張機能がどこでどのように取得されたかをプレーヤーに伝えるテキスト ID。
- `m_PriorityItemRewardType`: 優先報酬タイプのコード。
- `m_PriorityItemID`: 優先報酬アイテムID。
- `m_PriorityItemNum`: 優先報酬アイテム数。
- `m_RewardTypes`: 一般的な報酬タイプの配列。
- `m_ItemIDs`: `m_RewardTypes` と同様の報酬アイテム ID。
- `m_ItemCounts`: 報酬は報酬配列と並行してカウントされます。
- `m_OpenTimestamp`: 拡張機能を開く/ロック解除するためのタイムスタンプ ゲート。
- `m_AddSSkillLv`: この拡張機能行によって付与または要求される追加の特殊スキル レベル。

## `Skill/` を直接フィードする関連フォルダー

### `Character/`
- ``JobID.m_Base AttackSkill` and `m_Sub AttackSkill``: **確認済み。** これらは `SkillID.m_id` の直接参照です。たとえば、OT0 `JobID#1` (戦士) は、ベース攻撃スキル `700` とサブ攻撃スキル `724` を使用します。
- ``WeaponAnimationSet``: **確認しました。** `WeaponType.m_AnimationSet` はここを指します。これは武器ファミリーから戦闘アニメーションファミリーへの架け橋です。
- ``CharaStatusID``: **確認済み。** `SkillAvailID.m_ModifyStatus`、`SkillAilmentType.m_TargetStatus`、および `SkillAilmentType.m_AbsorbHealStatus` はすべてここを指します。
- ``CharaGuest``: **確認済み。** `SkillAvailID.m_SummonGuestID` はゲスト召喚の動作についてここを指します。
- ``CharaPlayerSpecialSkillList``: **確認済み** これにより、プレーヤーが `SpecialSkillID` ファミリーにバインドされます。 OT0 には `48` 行があります。 `44` の個別の `m_SpecialSkillID` 値は、きれいに `Skill/SpecialSkillID` に解決されます。

### `Enemy/`
- ``EnemyWeakID``: **確認済みかつ重要です。** これは、`SkillAvailID.m_ResistID` が指しているものです。 `EnemyWeakID` 内の配列には `ResistType` ID が格納されます。そのため、`m_ResistID` は「`SkillResistList` からのペイロードの割合を追加する」ではなく、「弱点テーブルを交換する」ことを意味します。
- ``EnemyWeakChangeID``: **確認済み。** `SkillAvailID.m_WeakChangeID` は、段階的な弱点変更についてここを示しています。
- ``EnemyWeakLockID``: **確認済み。** `SkillAvailID.m_WeakLockID` はここで弱点スロット ロックを示しています。
- ``EnemyReinforcements``: **確認済み** `SkillAvailID.m_Reinforcement` はここを指し、増援召喚テーブルを参照します。
- ``EnemyModeID``: **確認済み。** `SkillAvailID.m_ModeChangeID` は、敵のモード/フェーズの変更についてここを指します。
- ``EnemyTypeID.m_AilmentOffset``: **確認済み。** `Skill/SkillAilmentOffsets.m_id` を指します。これが、`SkillID` が直接指していなくても、状態異常オフセット プリセット テーブルがスキル エコシステムに属する理由です。

### `Battle/`
- ``BattleActionID``: **確認しました。** `SkillEffectiveID.m_Animations[*]` と `SkillAilmentType.m_ActionID` はここを指します。
- ``BattleCameraParams``: **確認しました。** OT0 `SkillEffectiveID.m_Cameras[*]` はここを指します。

### `Effect/`、`Sound/`、および `Texture/`
- ``Effect/AttachEffect``: **確認しました。** `SkillID.m_AttachEffect` はここを指します。
- ``Effect/EffectList``: **確認済み。** `SkillEffectiveID.m_Effects[*]` および複数の `SkillAilmentType` エフェクト フィールドはここを指します。
- ``Sound/SoundList``: **確認済み** `SkillEffectiveID.m_Sounds[*]` および `SkillAilmentType` サウンド フィールドはここを指します。
- ``Sound/PartVoiceID``: **確認済み** `SkillID.m_VoiceID` と `SkillEffectiveID.m_Voices*` はここを指します。
- ``Texture/TextureID``: **確認済み。** `SkillID.m_SkillIconID`、`WeaponType`/`MagicType` アイコン フィールド、`SkillIconList`、`SkillUniqueIconID`、および状態異常アイコン フィールドはすべてここで解決されます。

### `GameText/`
- ``GameText/SystemText/GameTextSkill` and localized `GameText/Localize/*/SystemText/GameTextSkill``: **確認済み。** `SkillID.m_Name`、`SkillID.m_Detail`、およびほとんどの `SkillEffectiveID.m_Texts` のメイン テキスト バンク。
- ``GameText/SystemText/GameTextUI` and localized `GameText/Localize/*/SystemText/GameTextUI``: **確認済み。** `WeaponType.m_TextID`、`MagicType.m_TextID`、ほとんどの `SkillAilmentType.m_TextID`/`m_ExplanationTextID`、および一部の `SkillID.m_Name` のメイン テキスト バンク異常値。

### `AIBattle/` 敵のスキル パイプライン
- `Basic chain`: **確認済み。** 通常の敵の場合、チェーンは `EnemyID.m_SkillsID -> AIBattle/TacticalSkillList.m_id`、`EnemyID.m_TacticalAssignID -> AIBattle/TacticalAssignList.m_id`、`TacticalAssignList.m_Tactics[*] -> TacticalList.m_id`、および `TacticalActionList.m_SkillIndex` であり、所有する `TacticalSkillList.m_UseSkills` 配列にインデックス付けされます。
- `Concrete example 1`: **確認済み。** `EnemyID#32300` は `m_SkillsID = 55000` および `m_TacticalAssignID = 30500` を使用します。 `TacticalSkillList#55000` には、`610001`、`610008`、`610009` などのスキル ID が含まれており、`TacticalActionList` は `m_SkillIndex = 0/1/2` を通じてそれらを選択します。
- `Concrete example 2`: **確認済み。** `EnemyID#834` は `m_SkillsID = 752` および `m_TacticalAssignID = 3` を使用します。 `TacticalSkillList#752` には、`100405`、`100406`、`100407` が含まれており、インデックスによって選択される戦術行が含まれます。
- `Important exception`: **確認済み。** OT0 には、`m_SkillsID = 0` ではあるがゼロ以外の `m_TacticalAssignID` を持つ敵もいます (たとえば、いくつかの Cait と野生生物の行)。したがって、すべての敵の行動経路が単純な戦術スキル リストの検索であるわけではありません。
