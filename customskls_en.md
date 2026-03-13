## **LANGUAGE:**  [English](customskills_en.md) | [日本語](customskills_ja.md) | [中文](customskills_zh.md) | [한국어](customskills_ko.md)

# How to add or modify skills and related systems

This page is an OT0-first reverse-engineering guide for the `Skill/` folder and every table that directly feeds it. Wherever possible, it distinguishes between things that are **confirmed from the OT0 database and SDK**, things that are **confirmed from CotC schema only**, things that are still **observed but not fully named by the SDK dump**, and things which have been **proven in-game**.

- **Proven** means the relationship or feature has been proven to have the stated meaning via in-game testing.
- **Confirmed** means the relationship is backed by the exported row layout, the OT0 SDK struct or enum names, and/or exact ID overlap against the target table.
- **Observed** means the field meaning is strongly supported by row usage patterns and names, but OT0 does not expose a perfectly named enum or caller in the SDK dump.
- **Unresolved** means the field is real and probably important in some content, but OT0 does not provide enough direct evidence to name the behavior with confidence and I have not tested it in-game or not seen any changes.

## OT0 versus the mobile-game `Skill/` folder at a glance

| Pattern | OT0 | Mobile game |
| --- | --- | --- |
| Core row counts | `SkillID 7330`, `SkillAvailID 7369`, `SkillEffectiveID 4381`, `SkillAilmentType 490` | `SkillID 23085`, `SkillAvailID 28023`, `SkillEffectiveID 11434`, `SkillAilmentType 968` |
| OT0-only tables under `Skill/` | `WeaponType`, `SkillCalcType`, `SkillAvailMagnificationList`, `SkillSupplyItemGroup`, `SkillSupplyItemList`, `SkillTargetShopList`, `SkillTargetFarmItemList` | not present in the mobile export |
| Mobile-only tables under `Skill/` | not present in stock OT0 | `ClassSkillConditionList`, `SkillEffectiveLinkageList`, `SkillFilterCategory`, `SkillFilterType`, `SkillModifyType`, `SpecialSkillExtendCategory`, `SpecialSkillExtendType`, `SpecialSkillExtendList` |
| `SkillEffectiveID` presentation schema | Keeps inline `m_Cameras` and `m_CameraTimes` arrays | Drops those inline camera arrays and adds linkage, UI-visibility, camera-shake, and magic-stone fields |
| `SkillVisitorList` binding | Stores direct `m_EnemyID` | Stores `m_EnemyType` instead |
| `SpecialSkillGrowthList` | Compact OT0 growth row with `m_DisplayPriority` | Much richer growth row with gauge, reward, avail, ailment, and damage-cap columns |
| `SkillID` focus | Keeps field-use flags, direct voice hooks, and OT0 weapon-replacement helpers | Adds filter/category routing, boost-display variants, and auto-battle-facing controls |
| `SkillAvailID` focus | Many dormant shared fields, plus OT0 village/reinforcement/weakness helpers | Activates tag-count, damage-limit, additional-weakness, dodge-ignore, and resist-manipulation channels |

- OT0 `Skill/` is structurally closer to the single-player game's battle flow, front/back-row mechanics, and village-management systems.
- The mobile-game `Skill/` export is structurally closer to a live-service combat stack: extra filter UI, extra progression layers, extra damage-cap logic, extra weakness systems, and more metadata for automated presentation/control paths.
- Do **not** assume that a table being absent from one title means the enum vanished from code. In several places the runtime enum still exists, but only one title exports a data table for it.


## Runtime model

For almost every active skill in OT0, the runtime chain is:

`SkillID` -> `SkillAvailID` + `SkillEffectiveID`

- `SkillID` is the root row: name/detail text, category, cost, icon, boost family, and the arrays of gameplay and presentation stages.
- `SkillAvailID` is the gameplay payload: target selection, damage/heal/buff/debuff/revive/weakness change/reinforcement/summon logic, durations, counts, and secondary mechanics.
- `SkillEffectiveID` is the presentation payload: battle actions, cameras, effects, sounds, voices, floating text, and visibility changes. Every avail gets assigned one ID from `SkillEffectiveID`

Ailments are a parallel subsystem rather than a fourth mandatory stage. When a skill applies a buff, debuff, status ailment, counter, intercept, reraise, damage cap aura, or similar state, the usual path is that `SkillAvailID` names one or more `SkillAilmentType` rows.

## Common misconceptions

- **Proven:** `SkillAvailID.m_ResistID` is **not** `Skill/SkillResistList.m_id`. In OT0 the nonzero values match `Enemy/EnemyWeakID.m_id` exactly, and that target row shape is the one that actually makes sense for weakness-table replacement. Concrete proof: `SkillAvailID#40085` and `SkillAvailID#73697` both use `m_ResistID = 6527`; `EnemyWeakID#6527` exists, while `SkillResistList#6527` does not.
- **Confirmed:** `SkillAvailID.m_SkillAvailMagnificationCondition` and `m_SkillAvailMagnificationConditionArray` do behave like row IDs into `SkillAvailMagnificationConditionList`.
- **Confirmed:** OT0 `SkillAvailID.m_SkillAvailMagnification` and `m_SkillAvailMagnificationArray` do **not** behave like `SkillAvailMagnificationList.m_id`. OT0 uses direct values such as `110`, `130`, `150`, `180`, `200`, and array values up to `300`. Concrete examples: `SkillAvailID#74940` from `Surprise Attack` uses direct values `110/130/150/170/200`, and `SkillAvailID#76297` from `Ulti-meow` climbs as high as `300`.
- **Confirmed:** `SkillID.m_SkillIconID` points directly into `Texture/TextureID`, not into `Skill/SkillIconList`. `SkillIconList` is still real, but it is a **separate** paired-icon bank.

## Proof matrix for the main foreign-key-style fields

- `SkillID.m_AttachEffect` -> `Effect/AttachEffect.m_id`: `10/10` unique nonzero OT0 values matched.
- `SkillID.m_SkillIconID` -> `Texture/TextureID.m_id`: `24/24` unique nonzero OT0 values matched.
- `SkillID.m_VoiceID` -> `Sound/PartVoiceID.m_id`: `25/25` unique nonzero OT0 values matched.
- `SkillID.m_WeaponIconType` -> `Skill/WeaponType.m_id`: `9/9` unique nonzero OT0 values matched.
- `SkillID.m_MagicIconType` -> `Skill/MagicType.m_id`: `7/7` unique nonzero OT0 values matched.
- `SkillAvailID.m_InvokeCondition` -> `Skill/SkillConditionList.m_id`: `37/37` unique nonzero OT0 values matched.
- `SkillAvailID.m_ModifyStatus` -> `Character/CharaStatusID.m_id`: `17/17` unique nonzero OT0 values matched.
- `SkillAvailID.m_HitTypes[*]` -> `Skill/SkillHitRateList.m_id`: `26/26` unique nonzero OT0 values matched.
- `SkillAvailID.m_ResistID` -> `Enemy/EnemyWeakID.m_id`: `32/32` unique nonzero OT0 values matched.
- `SkillAvailID.m_ResistAilmentID` -> `Skill/SkillResistAilmentID.m_id`: `26/26` unique nonzero OT0 values matched.
- `SkillAvailID.m_WeakChangeID` -> `Enemy/EnemyWeakChangeID.m_id`: `71/71` unique nonzero OT0 values matched.
- `SkillAvailID.m_WeakLockID` -> `Enemy/EnemyWeakLockID.m_id`: `64/64` unique nonzero OT0 values matched.
- `SkillAvailID.m_Reinforcement` -> `Enemy/EnemyReinforcements.m_id`: `59/60` unique nonzero OT0 values matched. The one outlier is value `140`, used by `SkillAvailID#74111` from `SkillID#900504`.
- `SkillAvailID.m_ModeChangeID` -> `Enemy/EnemyModeID.m_id`: `28/28` unique nonzero OT0 values matched.
- `SkillAvailID.m_SummonGuestID` -> `Character/CharaGuest.m_id`: `1/1` unique nonzero OT0 values matched.
- `SkillAvailID.m_OverrideWeapon` -> `Skill/WeaponType.m_id`: `8/8` unique nonzero OT0 values matched.
- `SkillAvailID.m_OverrideMagic` -> `Skill/MagicType.m_id`: `7/7` unique nonzero OT0 values matched.
- `SkillAvailID.m_SkillAvailMagnificationCondition` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `9/9` unique nonzero OT0 values matched.
- `SkillAvailID.m_SkillAvailMagnificationConditionArray[*]` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `28/28` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Cameras[*]` -> `Battle/BattleCameraParams.m_id`: `47/47` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Animations[*]` -> `Battle/BattleActionID.m_id`: `92/92` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Effects[*]` -> `Effect/EffectList.m_id`: `712/712` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Sounds[*]` -> `Sound/SoundList.m_id`: `259/259` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Voices1[*]` and `m_Voices2[*]` -> `Sound/PartVoiceID.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_ActionID` -> `Battle/BattleActionID.m_id`: `2/2` unique nonzero OT0 values matched.
- `SkillAilmentType.m_TargetStatus` and `m_AbsorbHealStatus` -> `Character/CharaStatusID.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_IconTexID` and `m_IconSptTexID` -> `Texture/TextureID.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_AddEffect`, `m_CharacterEffect`, `m_FieldEffect`, `m_InvokeEffect` -> `Effect/EffectList.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_AddEffectSound`, `m_InvokeSound` -> `Sound/SoundList.m_id`: all nonzero OT0 values matched.
- `WeaponType.m_EquipType` -> `Item/ItemType.m_id`: `8/8` unique nonzero OT0 values matched.
- `WeaponType.m_AnimationSet` -> `Character/WeaponAnimationSet.m_id`: `8/8` unique nonzero OT0 values matched.
- `WeaponType.m_WeakIconTexID`, `WeaponType.m_SkillIconTexID`, `MagicType.m_WeakIconTexID`, `MagicType.m_SkillIconTexID`, `SkillUniqueIconID.m_SkillIconTexID`, and `SkillIconList` texture fields all match `Texture/TextureID.m_id` cleanly.

## Multilingual cross-checks

- OT0 stores Japanese as the base text bank and localized text under `GameText/Localize/*`. For example, `SkillID` name/detail text resolves cleanly across JP/EN/ZH-CN/KO-KR: `153236` is `復活手当` / `Vivify` / `复活处理` / `부활처치`, and `153376` is the corresponding revive description.
- For ailment UI text, `SkillAilmentType` primarily points into `GameTextUI`, **not** `GameTextSkill`. Example: `62327` is `毒` / `Poison` / `中毒` / `독`, and `96365` is the ailment explanation string.
- 
## How to read the `Skill/` folder

- The `Skill/` folder mixes three very different kinds of tables: **runtime rows** (`SkillID`, `SkillAvailID`, `SkillEffectiveID`, `SkillAilmentType`), **lookup legends** (`WeaponType`, `MagicType`, `ResistType`, `SkillCategory`, `SkillCalcType`), and **helper payloads** (`SkillHitRateList`, `SkillResistAilmentID`, `SkillTargetShopList`, etc.).
- Do not treat every integer field named `...ID` as a foreign key to another `Skill/` table. OT0 uses many direct enums, some inline percentage arrays, and some cross-folder row IDs.
- If you clone or edit a shared `SkillAvailID`, `SkillEffectiveID`, or `SkillAilmentType` row in place, you may silently alter many unrelated skills, because OT0 reuses payload rows heavily.

## Exact OT0 SDK row-struct quotations used by this guide

This section quotes the OT0 SDK directly from `SDK/Kingship_structs.hpp` so the guide does not stop at paraphrases. The exact row-struct definitions stay centralized here because they are long and readers often want to compare several table schemas side by side.

### Exact OT0 row-struct quotations used by the following `Skill/` sections

#### `FMagicTypeBase`
Quoted from `SDK/Kingship_structs.hpp:14381`.
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
Quoted from `SDK/Kingship_structs.hpp:9814`.
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
Quoted from `SDK/Kingship_structs.hpp:8760`.
```cpp
struct FResistTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DamageRate;                                      // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillCategoryBase`
Quoted from `SDK/Kingship_structs.hpp:16239`.
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
Quoted from `SDK/Kingship_structs.hpp:12824`.
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
Quoted from `SDK/Kingship_structs.hpp:17289`.
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
Quoted from `SDK/Kingship_structs.hpp:17406`.
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
Quoted from `SDK/Kingship_structs.hpp:14869`.
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
Quoted from `SDK/Kingship_structs.hpp:17455`.
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
Quoted from `SDK/Kingship_structs.hpp:14674`.
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
Quoted from `SDK/Kingship_structs.hpp:14518`.
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
Quoted from `SDK/Kingship_structs.hpp:17264`.
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
Quoted from `SDK/Kingship_structs.hpp:12898`.
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
Quoted from `SDK/Kingship_structs.hpp:17569`.
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
Quoted from `SDK/Kingship_structs.hpp:17517`.
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
Quoted from `SDK/Kingship_structs.hpp:16382`.
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
Quoted from `SDK/Kingship_structs.hpp:13602`.
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
Quoted from `SDK/Kingship_structs.hpp:17605`.
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
Quoted from `SDK/Kingship_structs.hpp:12910`.
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
Quoted from `SDK/Kingship_structs.hpp:12926`.
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
Quoted from `SDK/Kingship_structs.hpp:17641`.
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
Quoted from `SDK/Kingship_structs.hpp:17691`.
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
Quoted from `SDK/Kingship_structs.hpp:16345`.
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
Quoted from `SDK/Kingship_structs.hpp:17824`.
```cpp
struct FSpecialSkillIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSpecialSkillGrowthListBase`
Quoted from `SDK/Kingship_structs.hpp:13722`.
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

## Complete `Skill/` file reference

### `MagicType`
Element legend table. OT0 uses it anywhere a skill or ailment needs an element family for iconography or override behavior.

- **OT0 rows:** `9`.
- **CotC rows:** `8`.
- **OT0 SDK row struct:** `FMagicTypeBase`.
- **Related OT0 SDK enums:** `EMAGIC_TYPE`.
- `m_id` is the same element key family that appears in `SkillID.m_MagicIconType` and `SkillAvailID.m_OverrideMagic`.

#### Exact OT0 SDK enum quote used by this table
##### `EMAGIC_TYPE`
Quoted from `SDK/Kingship_structs.hpp:4295`.
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
- `MagicType.m_id` is not an arbitrary row number. OT0 keys this table so that the exported JSON row IDs `0..8` are the same raw values stored by the engine enum above.
- That is why `SkillID.m_MagicIconType`, `SkillAvailID.m_OverrideMagic`, and other element-family fields can be read both as direct SDK enum values and as `MagicType` row IDs without any translation layer.
- It is untested whether `MagicType` can be expanded to add new magic types

#### Field-by-field
- `m_id`: **Proven.** Direct element family key. OT0 rows `0..8` align to `EMAGIC_TYPE` (`NONE`, `FIRE`, `ICE`, `THUNDER`, `WIND`, `LIGHT`, `DARK`, `NAUGHT`, `POISON`).
- `m_TextID`: **Proven.** Player-facing element name in `GameTextUI`. Example: OT0 `1 -> Fire`, `7 -> Non-Elemental`, `8 -> Poison`.
- `m_WeakIconTexID`: **Proven.** `Texture/TextureID.m_id` for the weakness-panel icon used when this element appears as a weakness or resistance family.
- `m_SkillIconTexID`: **Proven.** `Texture/TextureID.m_id` for the command/skill overlay icon for this element family.

#### CotC-only extra field
- `m_AdditionWeakIconTexID`: **Confirmed from CotC schema.** Additional weakness-icon texture used by CotC's added-weakness systems. OT0 has no corresponding field.

### `WeaponType`
Weapon-family legend table. This is the canonical place for OT0 weapon labels, weapon icons, the corresponding equipment-type row, and the battle-animation family.

- **OT0 rows:** `10`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FWeaponTypeBase`.
- **Related OT0 SDK enums:** `EWEAPON_TYPE`.
- It is untested whether `WeaponType` can be expanded to add new weapon types

#### Exact OT0 SDK enum quote used by this table
##### `EWEAPON_TYPE`
Quoted from `SDK/Kingship_structs.hpp:7327`.
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
- `WeaponType.m_id` is likewise keyed to the raw SDK enum above. The exported rows are the data-table mirror of the engine's weapon-family constants.
- This is why `SkillID.m_WeaponIconType`, `SkillAvailID.m_OverrideWeapon`, weakness grids, and weapon-animation routing can all share the same numeric family without a second remapping table.

#### Field-by-field
- `m_id`: **Proven.** Direct weapon-family key. OT0 rows align to `EWEAPON_TYPE` (`NONE`, `SWORD`, `LANCE`, `DAGGER`, `AXE`, `BOW`, `ROD`, `PAPERS`, `FAN`, `NAUGHT`).
- `m_TextID`: **Proven.** Player-facing weapon-family name in `GameTextUI`. Example: `1 -> Sword`, `2 -> Polearm`, `7 -> Tome`, `8 -> Fan`.
- `m_WeakIconTexID`: **Proven.** `Texture/TextureID.m_id` for the weakness-panel icon of that weapon family.
- `m_SkillIconTexID`: **Proven.** `Texture/TextureID.m_id` for the weapon overlay icon used in skill UI.
- `m_EquipType`: **Confirmed.** `Item/ItemType.m_id` for the matching equippable weapon-family item type. This is why the OT0 rows use item-type IDs like `1`, `7`, `3`, `2`, `5`, `4`, `6`, `8` rather than a second copy of the weapon enum.
- `m_AnimationSet`: **Confirmed.** `Character/WeaponAnimationSet.m_id`. This is the animation family that battle actions use when the skill is rendered as that weapon type.

### `ResistType`
OT0 weakness/resistance-rate legend. This is the percentage class table used by weakness-grid style tables such as `EnemyWeakID`.

- **OT0 rows:** `10`.
- **CotC rows:** not present in the CotC `Skill/` folder. CotC assigns resistance rates directly
- **OT0 SDK row struct:** `FResistTypeBase`.
- **Warning**: `EnemyWeakID` stores `ResistType` IDs per slot; `SkillResistList` stores direct percentage deltas instead. `SkillAvailID.m_ResistID` takes `EnemyWeakID.m_id` as input, **NOT** `SkillResistList.m_id`

#### Field-by-field
- `m_id`: **Proven.** Weakness-class key used inside `Enemy/EnemyWeakID.m_ResistWeapon` and `m_ResistMagic`.
- `m_DamageRate`: **Proven.** Actual damage-rate percentage for that weakness class. OT0 rows decode to `100` normal, `130` weakness, `50` resist, `0` null, `-100` absorb, plus intermediate states `90`, `85`, `80`, `75`, and `1`.

### `SkillCategory`
Category legend table. The actual category number lives on `SkillID.m_Category`; this table adds one category-level boolean that the runtime can query.

- **OT0 rows:** `9`.
- **CotC rows:** `9`.
- **OT0 SDK row struct:** `FSkillCategoryBase`.
- **Related OT0 SDK enums:** `ESKILL_CATEGORY`.

#### Exact OT0 SDK enum quote used by this table
##### `ESKILL_CATEGORY`
Quoted from `SDK/Kingship_structs.hpp:6344`.
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
- `SkillID.m_Category` stores the raw `ESKILL_CATEGORY` number from the SDK enum above. `SkillCategory.json` exists because OT0 wants one extra per-category data flag (`m_BuffDebuffLimit`) layered on top of that enum.
- In other words, `SkillCategory` is not an unrelated foreign table; it is the data-backed legend for the exact engine category constants quoted above.

#### Field-by-field
- `m_id`: **Confirmed.** Direct category key. OT0 rows align to `ESKILL_CATEGORY` (`COMMAND`, `SUPPORT`, `ENEMYACTION`, `ITEM`, and so on).
- `m_BuffDebuffLimit`: **Observed.** Category-level boolean for buff/debuff-cap behavior. In OT0 it is true for category IDs `1`, `3`, and `4` and false elsewhere, so do not assume all categories participate in the same limit rules.

### `SkillCalcType`
Pure enum legend table. OT0 keeps `Skill/SkillCalcType` with only `m_id`, which exactly mirrors the calculation-mode enum used by `SkillAvailID.m_CalcType`.

- **OT0 rows:** `10`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** no matching dedicated row struct was found in the OT0 SDK dump shipped in this repo.
- **Related OT0 SDK enums:** `ESKILL_CALC_TYPE`.
- No dedicated `FSkillCalcTypeBase` row struct was found in the OT0 SDK dump here, but the OT0 JSON is still a clean legend table.

#### Exact OT0 SDK enum quote used by this table
##### `ESKILL_CALC_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6327`.
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
- `SkillAvailID.m_CalcType` stores these raw numbers directly. `SkillCalcType` is therefore best read as the exported legend copy of the SDK enum rather than as an independent gameplay table.
- This also explains why the table has only `m_id`: the semantic names live in the SDK enum, while the JSON keeps the numeric domain visible in the data export.

#### Field-by-field
- `m_id`: **Confirmed.** Calculation-mode key matching `ESKILL_CALC_TYPE` (`NONE`, `WEAPON`, `MAGIC`, `ADD_RATED`, `ADD_FIXED`, `ADD_NOW_RATED`, `LAST_1`, `FEAR_LAST_1`, `BP_SPECIAL_REGEN_RATE`, `MAGIC_REFLECTION`).

### `SkillID`
Root skill row. If you change only one table when cloning a skill, this is the table you will inspect first, but it is almost never the only table you must edit.

- **OT0 rows:** `7330`.
- **CotC rows:** `23085`.
- **OT0 SDK row struct:** `FSkillIDBase`.
- **Related OT0 SDK enums:** `ESKILL_CATEGORY`, `ESKILL_RECOMEND_CATEGORY`.
- OT0 usage is heavily skewed toward enemy-side rows: category `ENEMYACTION` alone accounts for most rows. When making player content, clone a row from the correct player-facing family instead of picking an arbitrary `SkillID` row.

#### Exact OT0 SDK enum quote used directly by this row
##### `ESKILL_RECOMEND_CATEGORY`
Quoted from `SDK/Kingship_structs.hpp:6655`.
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
- `SkillID.m_Category` should be read together with the exact `ESKILL_CATEGORY` quote embedded in the preceding `SkillCategory` section, because the skill row stores that raw enum value and `SkillCategory` is its companion legend table.
- `SkillID.m_RecommendCategory` does **not** have a parallel `Skill/` legend file. The exact SDK enum above is therefore the authoritative naming source for values such as `ATTACK`, `SUPPORT`, and `HEAL`.

#### Field-by-field
- `m_id`: Primary key for the skill row.
- `m_Name`: **Proven.** Skill name text ID. Most OT0 rows resolve through `GameText/Localize/*/SystemText/GameTextSkill.json`, but a smaller subset resolves through `GameTextUI` instead. Use localized text banks when verifying visible names.
- `m_Detail`: **Proven.** Skill detail/description text ID. OT0 overwhelmingly resolves this through `GameTextSkill`, not `GameTextUI`.
- `m_Job`: **Proven.** `Character/JobID.m_id` when set. OT0 only uses the eight base job IDs here, and only on `44` rows, so this is **not** a universal owner-job field.
- `m_BoostSkills`: **Proven.** Fixed-length array of four `SkillID` rows for the boost family `[boost0, boost1, boost2, boost3]`. In OT0 only the lead rows of boostable families usually populate it.
- `m_Category`: **Proven.** Skill category from `ESKILL_CATEGORY`. This is the first field you should inspect to separate player commands, support skills, enemy actions, enemy passives, and item skills.
- `m_RequireRatio`: **Confirmed unused in OT0.** Present in the SDK struct but always zero/false in OT0 skill rows.
- `m_RequireValue`: **Proven.** SP Cost. For non-command skills the meaning depends on category and caller.
- `m_CountLimit`: **Proven;, unused in OT0.** Usage limit of the skill. Present but always zero.
- `m_UnableRecoverCount`: **Confirmed unused in OT0.** Present but always zero/false.
- `m_RequireBoost`: **Proven; unused in OT0.** Minimum boost level required to execute skill. Present but always zero.
- `m_WeaponIconType`: **Proven.** Direct `WeaponType.m_id` for the weapon icon overlay shown with the skill.
- `m_MagicIconType`: **Proven.** Direct `MagicType.m_id` for the element icon overlay shown with the skill.
- `m_IsRandomReplace`: **Observed.** Random-variant replacement flag. OT0 uses it on a handful of random replacement families such as `Prism Mist`-style skills.
- `m_IsWeaponSelect`: **Observed.** Weapon-form selection flag. Used when one visible command can resolve to different concrete skill rows depending on weapon choice or weapon-context routing (i.e. selecting a weapon to execute the skill with). 
- `m_IsReplaceNowSelectedWeapon`: **Observed.** Replacement should use the currently selected weapon form rather than a fixed family member.
- `m_IsReplaceMainWeapon`: **Observed.** Similar to the previous field, but biased to the main weapon set. Rare in OT0.
- `m_WeaponReplaceSkill`: **Proven.** Fixed-length pool of eight replacement `SkillID` rows. Despite the name, OT0 uses it both for true weapon-form replacement and for some random-variant families.
- `m_Avails`: **Proven.** Fixed-length array of up to twenty `SkillAvailID` stages. This is the gameplay sequence for the skill. Zero slots are simply skipped.
- `m_BeginEffective`: **Proven.** Optional pre-cast `SkillEffectiveID` stage.
- `m_Effectives`: **Proven.** Fixed-length array of up to twenty `SkillEffectiveID` stages. This is the presentation sequence for the skill, each avail is assigned one effect.
- `m_EndEffective`: **Confirmed; unused in OT0.** Post-cast `SkillEffectiveID` hook present in the struct but never used in OT0 rows.
- `m_AttachEffect`: **Confirmed.** `Effect/AttachEffect.m_id`. OT0 only uses this on a minority of rows.
- `m_SkillIconID`: **Proven.** Direct `Texture/TextureID.m_id`. This is the main player-facing command icon field in OT0, **not** `SkillIconList` rows.
- `m_SkillUniqueIconID`: **Confirmed.** `Skill/SkillUniqueIconID.m_id`. Present in the schema but unused by OT0 `SkillID` rows.
- `m_CommandPhaseSkillIconID`: **Confirmed unused in OT0.** Reserved alternate icon for command-phase systems.
- `m_OverrideCommandName`: **Confirmed; unused in OT0.** Reserved alternate text ID for command naming.
- `m_CommandCategory`: **Observed.** Rare UI/category helper. Only five OT0 rows use it, all with the same value `62274`. Clone a row that already uses it before you rely on it.
- `m_BlockedSkill`: **Proven; unused in OT0.** Reserved mutually exclusive/blocking skill hook.
- `m_ReplaceCondition`: **Confirmed; unused in OT0.** Reserved single replacement-condition hook.
- `m_ReplaceConditionArray`: **Confirmed; unused in OT0.** Reserved replacement-condition array.
- `m_ReplaceSkill`: **Confirmed unused in OT0.** Reserved replacement-skill hook.
- `m_ReplaceSkillArray`: **Confirmed unused in OT0.** Reserved replacement-skill array.
- `m_DisableReplaceTag`: **Observed.** Suppresses some replacement-tagging logic or replacement-related UI labeling. OT0 leaves it false everywhere.
- `m_SpecialSkillInitialGaugeValue`: **Confirmed unused in OT0.** Gauge-seeding field for special-skill style systems. Tested but no real difference was noted in-game.
- `m_SpecialSkillRegenerateValue`: **Confirmed unused in OT0.** Gauge-regeneration field for special-skill style systems. Tested but no real difference was noted in-game.
- `m_RecommendCategory`: **Confirmed.** Recommendation bucket from `ESKILL_RECOMEND_CATEGORY` (`ATTACK`, `SUPPORT`, `HEAL`). OT0 only uses this on a small subset of rows.
- `m_DuplicateAilment`: **Confirmed unused in OT0.** Reserved ailment-duplication flag at the skill-row level. Exact meaning unclear, also untested.
- `m_AfterAction`: **Observed.** Rare follow-up action code. OT0 only uses it on five rows; those rows also have staged avail/effective data consistent with forced post-action behavior.
- `m_InitialChargeTrun`: **Confirmed; unused in OT0.** Typo preserved by the asset. Charge-turn parameter reserved for other systems. Meaning could not be determined via in-game testing.
- `m_ReChargeTrun`: **Confirmed; unused in OT0.** Recharge-turn parameter reserved for other systems. Meaning could not be determined via in-game testing.
- `m_MaxBoostLv`: **Confirmed.** Maximum boost level. OT0 sets this to `3` on every `SkillID` row. Higher values may require edits to `BattleParams` too.
- `m_FieldUseable`: **Confirmed.** Marks the skill as usable in field context. OT0 only sets this on twenty rows, such as field revive, field healing skills, etc.
- `m_VoiceID`: **Confirmed.** Direct `Sound/PartVoiceID.m_id` for the skill's voice callout.

#### Concrete OT0 examples
- `m_FieldUseable`: `SkillID#607665 First Aid`, `#900556 Healing Light`, `#902332 Vivify`, and `#902114 Revive` all set this flag.
- `m_AfterAction`: `SkillID#900012 Retreating Strike`, `#900042 Lunar Arc`, `#900100 Expert First Aid`, and `#900563/#901483 Song of Battle` all set `m_AfterAction = 1`; their visible descriptions also explicitly end with "then move to back row." That is strong evidence that the field is tied to forced post-action row movement.
- `m_RecommendCategory`: restorative rows such as `SkillID#902332 Vivify`, `#901894 Arcane Healing`, `#902108 Heal Wounds`, and `#902114 Revive` use `m_RecommendCategory = 3`, which matches `ESKILL_RECOMEND_CATEGORY::HEAL` exactly.

#### CotC-only extra fields
- `m_DispBoostLv`: **Confirmed from CotC schema.** UI flag for explicitly displaying boost level.
- `m_IsBasicAbility`: **Confirmed from CotC schema.** Marks the row as a basic ability rather than a learned/special one.
- `m_NoChangeBoostType`: **Observed from name and CotC usage.** Prevents replacement or conversion logic from changing the skill's boost family/type.
- `m_Boost246`: **Observed from name and CotC usage.** CotC-specific boost-pattern flag for skills that do not use the ordinary OT0-style `0/1/2/3` family presentation.
- `m_ForcedSkillFilter`: **Confirmed from CotC schema and ID overlap.** Array of `SkillFilterType.m_id` rows that forcibly include the skill in specific filter buckets.
- `m_ExclusionSkillFilter`: **Confirmed from CotC schema and ID overlap.** Array of `SkillFilterType.m_id` rows that forcibly exclude the skill from specific filter buckets.
- `m_ForcedCategory`: **Confirmed from CotC schema and ID overlap.** `SkillFilterCategory.m_id` forced on the skill for CotC filter UI.
- `m_NotUsedAutoBattle`: **Observed from name and CotC usage.** Auto-battle exclusion flag.
- `m_CmdPhaseTurnResetOnContinue`: **Observed from name and CotC usage.** Command-phase state reset when the skill continues into another phase or turn.

#### OT0 usage notes
- `Row-count bias`: OT0 has `7330` `SkillID` rows, but only `1614` are category `COMMAND`. Enemy content dominates the file.
- `Array sizes`: OT0 uses fixed sizes: `m_BoostSkills` length `4`, `m_Avails` length `20`, `m_Effectives` length `20`, `m_WeaponReplaceSkill` length `8`.
- `True rarity`: OT0 only sets `m_IsRandomReplace` on `5` rows, `m_IsWeaponSelect` on `23`, `m_IsReplaceMainWeapon` on `4`, `m_AfterAction` on `5`, and `m_FieldUseable` on `20`.

### `SkillAvailID`
Gameplay payload row. If `SkillID` tells you *what skill family this is*, `SkillAvailID` tells you *what the stage actually does in battle or field logic*.

- **OT0 rows:** `7369`.
- **CotC rows:** `28023`.
- **OT0 SDK row struct:** `FSkillAvailIDBase`.
- **Related OT0 SDK enums:** `Kingship::ESKILL_INVOKE`, `ESKILL_TARGET_TYPE`, `ESKILL_MODIFY_TYPE`, `ESKILL_CALC_TYPE`, `ESKILL_AILMENT_CALC_TYPE`.
- For OT0 reverse engineering, `m_ModifyType` is the single most important field in the entire row. It determines how most of the numeric payload arrays should be read.

#### Exact OT0 SDK enum quotes that directly drive this row
##### `Kingship::ESKILL_INVOKE` (context enum)
Quoted from `SDK/Kingship_structs.hpp:6360`.
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
- The SDK dump exposes two different `Kingship::ESKILL_INVOKE` enum blocks with the same printed name. The context enum above describes the broader passive/battle/field invocation domain.

##### `Kingship::ESKILL_INVOKE` (timing enum)
Quoted from `SDK/Kingship_structs.hpp:6436`.
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
- `SkillAvailID.m_InvokeTiming` uses the timing enum above, not the earlier context enum. This is why live OT0 rows contain values such as `COMMAND`, `ACTION_DAMAGE`, `FIELD_ACTION`, and `ALWAYS`.

##### `ESKILL_TARGET_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6683`.
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
- `SkillAvailID.m_TargetType` stores the raw numeric selector from this enum. That is why the field can encode both ordinary player targets (`ENEMY_1`, `FRIENDLY_ALL_DEAD`) and AI/helper selectors (`AI_ENEMY_MIN_HP`, `FORWARDS_OR_BACKUPS_ALIVE`) in the same integer column.

##### `ESKILL_MODIFY_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6476`.
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
- `SkillAvailID.m_ModifyType` stores the raw numeric opcode from this enum, and that is the reason this single field dominates the interpretation of `m_Values`, `m_Turns`, `m_Counts`, `m_ModifyStatus`, reinforcement IDs, weakness-change IDs, shop/farm helper rows, and many other payloads.
- `SkillAvailID.m_CalcType` should be read together with the exact `ESKILL_CALC_TYPE` quote embedded above in `SkillCalcType`.
- `SkillAvailID.m_CalcTypeAilment` should be read together with the exact `ESKILL_AILMENT_CALC_TYPE` quote embedded below in `SkillAilmentType`; OT0 value usage matches that ailment-side enum family.

#### Field-by-field
- `m_id`: Primary key for the avail row.
- `m_InvokeTiming`: **Proven.** Timing enum from the richer `Kingship::ESKILL_INVOKE` block in the OT0 SDK dump. OT0 uses values such as `COMMAND`, `BATTLING`, `ACTION_DAMAGE`, `FIELD_ACTION`, and `ALWAYS`.
- `m_InvokeCondition`: **Proven.** `Skill/SkillConditionList.m_id`. The stage only resolves if this condition package passes.
- `m_TargetType`: **Proven.** Target selector from `ESKILL_TARGET_TYPE`. This is where you decide whether the stage asks for one enemy, all enemies, self, one dead ally, everyone, forward row, backup row, AI-selected lowest HP target, and so on.
- `m_ModifyType`: **Proven.** Main operation code from `ESKILL_MODIFY_TYPE`. `CHARACTER` is the general-purpose stat/damage/heal/buff/debuff path, while other values perform revive, weakness change, reinforcement, steal, village economy operations, and many more.
- `m_ModifyStatus`: **Proven.** `Character/CharaStatusID.m_id` when the modify opcode needs a concrete stat or resource selector. OT0 only uses seventeen unique status IDs here, and all of them match `CharaStatusID` cleanly.
- `m_AvailTag`: **Proven.** Seven-slot internal classification tag array. OT0 uses many recurring tuples. For instance, `16` tags the avail as magic-related, allowing it to be repeated with the Rechant ailment active
- `m_IsFoodAvail`: **Confirmed.** Rare OT0 flag for food/village-side avail behavior. Likely to keep the status effect after ending battle. 
- `m_HitTypes`: **Proven.** Array of up to four `SkillHitRateList.m_id` rows. These control multi-hit count distributions and related random-hit logic.
- `m_RandomChoiceCount`: **Observed.** Count of random choices/branches for opcodes that pick from multiple outcomes. Rare in OT0.
- `m_CalcType`: **Proven.** Calculation mode from `ESKILL_CALC_TYPE`. In OT0 the common values are `NONE`, `WEAPON`, `MAGIC`, `ADD_FIXED`, and `ADD_RATED`.
- `m_Values`: **Proven.** Main numeric payload array. Interpretation depends on `m_ModifyType` plus `m_CalcType`: damage power, heal amount, status delta, reward amount, or other opcode-specific value.
- `m_FluctuationValues`: **Observed.** Secondary variance payload. OT0 only uses nonzero values on three rows, so most skills ignore it.
- `m_SkillRatios`: **Observed but very strongly supported.** Per-boost ratio array. The common OT0 default is `(100,100,100,100)`, while many boostable attack families use progressive sets like `(100,180,260,340)` or `(100,200,300,400)`.
- `m_Turns`: **Proven.** Per-boost duration array. OT0 uses this for timed buffs, debuffs, counters, intercepts, and other stateful effects.
- `m_OverwriteCount`: **Observed.** Explicit overwrite count used by count-based effects when reapplying the stage.
- `m_Counts`: **Proven.** Per-boost count or stock array. Used by **count-based** mechanics such as limited reactions, repeat counts, or similar stage-local counters.
- `m_OrderPriority`: **Observed.** Rare battle order override. OT0 only uses a nonzero value on one row.
- `m_NextPriority`: **Observed.** Rare next-action priority adjustment. OT0 only uses it on seven rows.
- `m_TargetNextPriority`: **Observed.** Target-side next-action priority setting. OT0 mostly uses the sentinel value `-100`; when set to values like `1`, `2`, `30`, or `31` it is changing future turn order behavior.
- `m_ShieldDamage`: **Confirmed unused in OT0.** Always zero.
- `m_IgnoreWeak`: **Proven.** Ignore weakness interaction or weakness requirement for this stage. OT0 only uses it on seventy-three rows.
- `m_NoBreakable`: **Confirmed unused in OT0.** Always zero. Likely prevents an avail from breaking an enemy.
- `m_PhysicalHitRatio`: **Observed.** Rare physical-hit-rate override. OT0 only uses it on two rows.
- `m_CriticalRatio`: **Observed.** Critical-rate adjustment for this stage. OT0 uses it on a few hundred offensive rows.
- `m_Suicide`: **Observed.** Marks the stage as self-sacrificial or self-killing.
- `m_LotteryAilment`: **Proven.** Random ailment-selection helper. Rare in OT0 and only useful if the modify path is choosing an ailment package stochastically. Note: only one non-zero ailment listed in `m_AddAilment` is chosen.
- `m_AilmentInvokeCondition`: **Observed.** Extra condition gate specifically for the ailment portion of the stage. OT0 uses it on one row.
- `m_AddAilmentPick`: **Observed.** Rare selector flag for choosing from `m_AddAilment`. OT0 only uses it on four rows.
- `m_AddAilment`: **Proven.** Inline list of up to sixteen `SkillAilmentType.m_id` rows to add/apply.
- `m_CalcTypeAilment`: **Proven.** Parallel list of ailment calculation modes. The enum family is `ESKILL_AILMENT_CALC_TYPE`.
- `m_ValueAilment`: **Proven.** Parallel list of ailment potency/override values corresponding to `m_AddAilment`.
- `m_NotExtensionTarget`: **Observed.** Excludes the target state from ordinary duration-extension behavior. OT0 uses it on `557` rows, so it is not just a one-off debug flag.
- `m_WeakLockID`: **Proven.** `Enemy/EnemyWeakLockID.m_id`. Use it when the stage locks specific weakness slots.
- `m_DelayedSkill`: **Proven.** Follow-up `SkillID.m_id` that resolves later rather than immediately, needs to be paired with the `DELAYED_SKILL` ailment in that case. Also used to specify the intercept/counter skill when paired with that ailment.
- `m_DelaySkillPriority`: **Observed.** Priority bucket/timing helper for the delayed skill stage.
- `m_SubAilmentPick`: **Observed.** Rare selector flag for `m_SubAilment`.
- `m_SubAilment`: **Proven.** Secondary list of up to sixteen `SkillAilmentType.m_id` rows to be removed/subtracted from the targed.
- `m_ResistID`: **Confirmed.** `Enemy/EnemyWeakID.m_id`, **not** `SkillResistList`. This field swaps or injects a complete weakness-table row, because `EnemyWeakID` stores per-weapon/per-element `ResistType` classes. That is why the value set matches `EnemyWeakID` and not `SkillResistList`.
- `m_ResistAilment`: **Observed.** Inline ailment-resistance-rate array for the base ailment families. In OT0 this can coexist with `m_ResistAilmentID`, so do not assume one disables the other.
- `m_ResistAilmentID`: **Confirmed.** `Skill/SkillResistAilmentID.m_id`. This is the packaged form of ailment resistance, storing explicit ailment IDs and matching rates.
- `m_Reinforcement`: **Proven.** `Enemy/EnemyReinforcements.m_id` for stages that summon reinforcements. OT0 has one outlier value `140`, so always verify the target row before cloning rare boss behavior.
- `m_WeakChangeID`: **Proven.** `Enemy/EnemyWeakChangeID.m_id` for staged weakness-table modification. Untested whether this affects party members' resistance profiles when executed by them.
- `m_EnhancedSkillID`: **Proven; unused in OT0.** Always zero. Refers to `SkillEnhancedList.m_id`. Used in CotC to apply Potency buffs to Linde, Hägen, etc. which occupy the `SkillEnhancedList.m_id`s.
- `m_SummonGuestID`: **Proven.** `Character/CharaGuest.m_id` for guest summon behavior.
- `m_OverrideWeapon`: **Confirmed.** Direct `WeaponType.m_id` override.
- `m_OverrideMagic`: **Confirmed.** Direct `MagicType.m_id` override for the stage.
- `m_AppendType`: **Confirmed; unused in OT0.** Reserved append-behavior selector. Non-zero values appear to shave off shields from enemies regardless of what weakness is hit. CotC may use this to have a weapon hit for another weakness type.
- `m_SkillAvailMagnificationCondition`: **Confirmed.** `SkillAvailMagnificationConditionList.m_id`. This is the main lookup field for conditional **magnification** behavior.
- `m_SkillAvailMagnificationConditionArray`: **Confirmed.** Additional `SkillAvailMagnificationConditionList.m_id` rows. OT0 uses arrays of length seven here.
- `m_SkillAvailMagnification`: **Confirmed.** Direct magnification value in OT0, not a `SkillAvailMagnificationList` row ID. Typical OT0 values are `110`, `130`, `150`, `180`, `200`.
- `m_SkillAvailMagnificationArray`: **Confirmed.** Direct additional magnification values in OT0, not row IDs. OT0 uses arrays such as `[200,190,180,170,160,150,140]`.
- `m_InvocationProbability`: **Observed.** Extra probability gate. OT0 only uses it on one row.
- `m_ModeChangeID`: **Confirmed.** `Enemy/EnemyModeID.m_id`. This is a boss/AI phase-transition hook, not a generic player-form switch.
- `m_CountRefAilment`: **Confirmed unused in OT0.** Always zero.
- `m_CountRefValue`: **Confirmed unused in OT0.** Always zero.
- `m_CountRefValueLimit`: **Confirmed unused in OT0.** Always zero.

#### Concrete OT0 examples
- `m_ResistID`: `SkillAvailID#40085` and `#73697` both use `6527`, and `SkillAvailID#73698` uses `6528`. Those values exist as `EnemyWeakID` rows, which is why this field behaves like a full weakness-table replacement path rather than a simple percentage modifier.
- `m_OverrideWeapon` / `m_OverrideMagic`: `SkillAvailID#76127` through `#76134` are the weapon-family stages used by `Repeated Bash`, stepping through `m_OverrideWeapon = 1..8`. By contrast, `SkillAvailID#74047` and `#74051` from `Call for Help` use `m_OverrideMagic = 5`, showing that the same stage struct can forcibly render a magic family instead.
- `m_Reinforcement`: `SkillAvailID#20049` uses `m_Reinforcement = 43` on the enemy skill whose visible text is `<skl_user_name> calls for his lackeys!`. Its paired `SkillEffectiveID#20049` also carries the summon-success/summon-failure battle log strings, which is a good example of gameplay and presentation rows working together.
- `m_WeakChangeID` / `m_WeakLockID`: `SkillAvailID#20079` and `#20089` belong to the visible family `Weaknesses have been swapped!`, while `SkillAvailID#20048` belongs to `<skl_user_name> is protected by his lackeys.` and carries `m_WeakLockID = 11`. That is exactly the pattern you would expect for weakness-table mutation versus slot locking.
- `m_ModeChangeID`: `SkillAvailID#77350` from `Indestructible Flame`, `#74765` from `Dark Hex`, and `#75499` from `Vorpal Strike` all set nonzero mode-change IDs, which is why this field is best read as a boss/AI phase hook instead of a generic player stance switch.
- `m_SkillAvailMagnification*`: `SkillAvailID#74940` from `Surprise Attack` uses condition package `69` plus array packages `[68,67,59,61]` and direct magnifications `110/130/150/170/200`. `SkillAvailID#76297` from `Ulti-meow` uses packages `102..109` and direct magnifications up to `300`. Those are raw values, not `SkillAvailMagnificationList` row IDs.

#### CotC-only extra fields
- `m_HitRatio`: **Confirmed from CotC schema.** Extra hit-ratio scalar beyond OT0's simpler hit-rate tables.
- `m_LotteryAilmentNum`: **Confirmed from CotC schema.** Number of ailments to draw from the lottery path.
- `m_CompareValueAilment`: **Confirmed from CotC schema.** Comparison values used by ailment-side logic.
- `m_DelayedSkillActByOwner`: **Observed from name and CotC usage.** Run the delayed skill as the original owner rather than the eventual state holder.
- `m_SelfAfterSkillCheck`: **Observed from name and CotC usage.** Additional self-check after skill execution.
- `m_NoSelfAfterSkillCheckEffective`: **Observed from name and CotC usage.** Suppress the after-check presentation path when the self-check fails.
- `m_SubAilmentTag`: **Confirmed from CotC schema.** Tag ID for ailment subtraction grouping.
- `m_SubAilmentTagFlag`: **Observed from name and CotC usage.** Enable/tag behavior toggle for the previous field.
- `m_CalcTypeResist`: **Observed from name and CotC usage.** Separate calculation-mode selector for resistance manipulation.
- `m_AddResistWeapon`: **Confirmed from CotC schema.** Additive weapon-resistance payload array.
- `m_AddResistMagic`: **Confirmed from CotC schema.** Additive element-resistance payload array.
- `m_AilmentMagnificationConditions`: **Confirmed from CotC schema.** Ailment-side magnification condition IDs.
- `m_AilmentMagnifications`: **Confirmed from CotC schema.** Ailment-side direct magnification values.
- `m_CountRefAilmentTag`: **Confirmed from CotC schema.** Tag-based counter reference channel.
- `m_CountRefType`: **Observed from name and CotC usage.** Chooses what count family is referenced.
- `m_CountRefAttribute`: **Observed from name and CotC usage.** Attribute selector for the referenced counter.
- `m_IgnorePreviousSuccessful`: **Observed from name and CotC usage.** Ignore prior success state when resolving repeated checks.
- `m_IgnoreDodge`: **Observed from name and CotC usage.** Bypass dodge resolution.
- `m_IgnoreZeroDamage`: **Observed from name and CotC usage.** Continue effect processing even if damage becomes zero.
- `m_AddDamageLimitValue`: **Observed from name and CotC usage.** Additional damage-cap increase beyond the Surpassing Power ailment.
- `m_AddDamageLimitCondition`: **Observed from name and CotC usage.** Condition gate for the damage-limit increase.
- `m_UseAilmentMagnificationForAddDamageLimit`: **Observed from name and CotC usage.** Reuse ailment magnification values for damage-cap increase.
- `m_NonCondition`: **Observed from name and CotC usage.** Extra negation/no-condition switch.
- `m_IgnoreResistAilment`: **Observed from name and CotC usage.** Bypass ailment resistance.
- `m_IgnoreDefenceBuff`: **Observed from name and CotC usage.** Bypass defense-up buffs.
- `m_IgnoreReduceDamage`: **Observed from name and CotC usage.** Bypass general damage-reduction effects.
- `m_HitWeaponEnable`: **Observed from name and CotC usage.** Enable explicit hit-weapon typing.
- `m_HitWeaponType`: **Confirmed from CotC schema.** Weapon family used when hit-weapon typing is enabled.
- `m_NoConsumeAdditionWeak`: **Observed from name and CotC usage.** Preserve addition-weak state after the hit.
- `m_IsBuffDamageSupStrictlyCheck`: **Observed from name and CotC usage.** Stricter validation for buff-damage support interactions.

#### OT0 usage notes
- `Array sizes`: OT0 uses fixed sizes: `m_HitTypes` length `4`, `m_Values` length `4`, `m_SkillRatios` length `4`, `m_Turns` length `4`, `m_Counts` length `4`, `m_AddAilment` length `16`, `m_SubAilment` length `16`, `m_SkillAvailMagnificationConditionArray` length `7`, `m_SkillAvailMagnificationArray` length `7`.
- `Common opcodes`: OT0 overwhelmingly uses `m_ModifyType = 1 (CHARACTER)`. The next most common families are weakness change, revive, steal, result rewards, and village economy opcodes such as `ITEM_SUPPLY`.
- `Dormant counters`: `m_ShieldDamage`, `m_NoBreakable`, `m_EnhancedSkillID`, `m_AppendType`, and the `m_CountRef*` fields are effectively dormant in OT0.

### `SkillConditionList`
Reusable boolean condition packages for `SkillAvailID.m_InvokeCondition` and some other skill systems. Do not confuse this file with `SkillAvailMagnificationConditionList`; they are different opcode families.

- **OT0 rows:** `275`.
- **CotC rows:** `933`.
- **OT0 SDK row struct:** `FSkillConditionListBase`.
- The OT0 SDK dump used here does **not** expose a clean named enum for `m_Conditions` in this table. The observed opcode set is much larger than the magnification-condition enum and includes values such as `10723`, `10790`, and `10816`. An attempt at a datamine of these condition type IDs can be found [here](https://github.com/veganprimate/CotCPort/blob/main/Conditions/Conditions_Crossref_Atlas.md)

#### Field-by-field
- `m_id`: Primary key for the condition package.
- `m_Conditions`: **Proven shape, unresolved names.** Four condition opcodes interpreted slot-by-slot. This is the main condition family used by `SkillAvailID.m_InvokeCondition`. See the [Condition Crossref Atlas](https://github.com/veganprimate/CotCPort/blob/main/Conditions/Conditions_Crossref_Atlas.md) 
- `m_Params`: **Proven.** Four generic integer parameter slots. The meaning of each slot is determined by the matching `m_Conditions` entry.
- `m_AilmentTypes`: **Proven.** Four `SkillAilmentType.m_id` parameter slots for ailment-aware condition opcodes. OT0 uses `36` unique ailment IDs here.
- `m_StatusTypes`: **Proven shape.** Four `CharaStatusID` parameter slots for status-aware condition opcodes. OT0 leaves them unused, but CotC uses the same field family more aggressively.
- `m_WeaponTypes`: **Proven shape.** Four `WeaponType.m_id` parameter slots. Unused in OT0.
- `m_MagicTypes`: **Proven shape.** Four `MagicType.m_id` parameter slots. Unused in OT0.
- `m_Equipment`: **Proven shape.** Four equipment parameter slots. Unused in OT0.
- `m_ORFlag`: **Confirmed.** Combine occupied condition slots with OR instead of AND. OT0 rows `335` and `336` prove this directly by repeating the same ailment-check opcode across different ailment IDs.

#### CotC-only extra fields
- `m_AutoDungeonBuff`: **Confirmed from CotC schema.** Extra auto-dungeon-specific buff parameter channel.
- `m_AutoDungeonBuffType`: **Confirmed from CotC schema.** Type selector for the auto-dungeon buff channel.

### `SkillAvailMagnificationConditionList`
Reusable condition packages specifically for conditional magnification. Unlike `SkillConditionList`, this file **does** line up with a named OT0 SDK enum.

- **OT0 rows:** `89`.
- **CotC rows:** `217`.
- **OT0 SDK row struct:** `FSkillAvailMagnificationConditionListBase`.
- **Related OT0 SDK enums:** `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`.

#### Exact OT0 SDK enum quote used by this table
##### `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`
Quoted from `SDK/Kingship_structs.hpp:6226`.
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
- `m_Conditions` stores the raw IDs from this enum directly. `SkillAvailMagnificationConditionList` is therefore the exact condition-package table for those magnification opcodes, not a vague condition bucket.
- This is also why `SkillAvailID.m_SkillAvailMagnificationCondition` and `m_SkillAvailMagnificationConditionArray` can be described precisely as row IDs into this file: those fields point to a package whose occupied slots are themselves typed by the enum above.

#### Field-by-field
- `m_id`: Primary key for the magnification-condition package.
- `m_Conditions`: **Confirmed.** Four condition opcodes from `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`. OT0 uses values such as `SLD_GE`, `HAS_AILMENT`, `RACE_KILLER`, `BEFORE_ACTION`, `HP_SELF_LE`, `ENABLED_SUPPORTABILITY_SELF`, `DAY_OR_NIGHT`, `TURN_ORDER_FIXED_LE`, `PROGRESS`, `SP_BURST_GE`, and defense thresholds.
- `m_IntParams`: **Confirmed.** Four generic numeric thresholds corresponding to the occupied `m_Conditions` slots.
- `m_AilmentParams`: **Observed.** Ailment-related parameters. Many OT0 values match `SkillAilmentType.m_id`, but a handful of OT0 rows reference IDs not present in the current OT0 `SkillAilmentType` export, so do not assume every value is a live OT0 ailment row.
- `m_RaceParams`: **Observed.** Race filter/parameter slots. OT0 barely uses them.
- `m_Equipment`: **Confirmed shape.** Equipment filter slots. OT0 leaves them unused.

#### Concrete OT0 examples
- `SkillAvailMagnificationConditionList#7` stores `m_Conditions = [2,0,0,0]` with `m_IntParams = [10,0,0,0]`, i.e. a simple `SLD_GE 10` package.
- `#8` stores `m_Conditions = [5,0,0,0]` with `m_AilmentParams = [1,0,0,0]`, i.e. `HAS_AILMENT` with ailment row `1`.
- `#10` stores `m_Conditions = [7,0,0,0]`, i.e. a pure `BEFORE_ACTION` gate with no extra integer payload.
- `#13` stores `m_Conditions = [12,0,0,0]` with `m_IntParams = [100,0,0,0]`, i.e. a `HP_SELF_LT 100` style package.
- `#81` stores `m_Conditions = [8,0,0,0]` with `m_IntParams = [90,0,0,0]`, i.e. a `HP_SELF_LE 90` threshold package.

#### CotC-only extra fields
- `m_WeaponTypes`: **Confirmed from CotC schema.** Weapon-family filter slots for magnification conditions.
- `m_MagicTypes`: **Confirmed from CotC schema.** Element-family filter slots for magnification conditions.

### `SkillAvailMagnificationList`
Tiny OT0-only lookup table with four rows. It is real, but it is **not** the main linkage path used by OT0 `SkillAvailID` rows.

- **OT0 rows:** `4`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** no matching dedicated row struct was found in the OT0 SDK dump shipped in this repo.
- The critical practical point is that OT0 `SkillAvailID.m_SkillAvailMagnification` stores direct values such as `110` and `200`, while this table's IDs are only `0..3`.

#### Field-by-field
- `m_id`: Primary key for the small lookup row.
- `m_Conditions`: **Observed.** Single-slot condition selector in this tiny legend table.
- `m_Params`: **Observed.** Single-slot parameter payload for the lookup row.

### `SkillEffectiveID`
Presentation payload row. This is where cameras, battle actions, effects, sounds, voices, text popups, and visibility changes are staged in time.

- **OT0 rows:** `4381`.
- **CotC rows:** `11434`.
- **OT0 SDK row struct:** `FSkillEffectiveIDBase`.
- **Related OT0 SDK enums:** `ESKILLEFFECT_LAYOUT`, `ESKILLEFFECTIVE_CHARA_VISIBILITY`, `ESKILLEFFECTIVE_VISIBILITY_TARGET`.

#### Exact OT0 SDK enum quotes that directly drive this row
##### `ESKILLEFFECT_LAYOUT`
Quoted from `SDK/Kingship_structs.hpp:6406`.
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
- `m_EffectLayout` stores raw `ESKILLEFFECT_LAYOUT` values. That is why each effect slot can say exactly whether it spawns on `SELF`, `TARGET`, `TARGET_CENTER`, or `BATTLE_CENTER`.

##### `ESKILLEFFECTIVE_CHARA_VISIBILITY`
Quoted from `SDK/Kingship_structs.hpp:6371`.
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
- `m_CharaVisibility` stores raw values from this enum for the visibility action itself (`VISIBLE`, `INVISIBLE`, `FADE_VISIBLE`, `FADE_INVISIBLE`).

##### `ESKILLEFFECTIVE_VISIBILITY_TARGET`
Quoted from `SDK/Kingship_structs.hpp:6383`.
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
- `m_VisibilityTarget` stores raw values from this enum for *who* receives the visibility action (`SELF`, `TARGET`, `FRIENDLY_ALL`, `ENEMY_ALL`, `FORWARDS_AND_BACKUPS`, and so on).
- OT0 splits visibility staging into two dimensions on purpose: one enum describes the visibility operation, while the other describes the recipient set.

#### Field-by-field
- `m_id`: Primary key for the presentation stage.
- `m_Repeat`: **Confirmed.** Stage repeat count. OT0 sets it to `1` on every row.
- `m_HoldTime`: **Observed.** Total presentation hold time in seconds.
- `m_AvailTime`: **Observed.** Time in seconds when the gameplay stage is considered available/resolved relative to the presentation.
- `m_Cameras`: **Confirmed.** Up to three `Battle/BattleCameraParams.m_id` rows for camera choreography.
- `m_CameraTimes`: **Observed.** Trigger times for the camera stages.
- `m_Animations`: **Confirmed.** Up to three `Battle/BattleActionID.m_id` rows for the performed battle actions.
- `m_PlayTimes`: **Observed.** Trigger times for the battle actions.
- `m_PlayRates`: **Observed.** Playback-rate multipliers for the battle actions.
- `m_AnimationTarget`: **Observed.** Per-action target selector for who plays the animation.
- `m_AnimStartFrame`: **Confirmed unused in OT0.** Start-frame override field present in the SDK struct but never used by OT0 rows.
- `m_AnimEndFrame`: **Observed.** End-frame or end-section marker for the animation stage. OT0 populates it on every row.
- `m_AnimNoIdle`: **Observed.** Suppress idle return or idle blend after the animation. Rare in OT0.
- `m_Effects`: **Proven.** Up to three `Effect/EffectList.m_id` rows spawned by the presentation stage.
- `m_InvokeTimes`: **Observed.** Effect-spawn times.
- `m_EffectLayout`: **Confirmed.** Per-effect spawn layout from `ESKILLEFFECT_LAYOUT` (`SELF`, `TARGET`, `FRIENDLY_CENTER`, `TARGET_CENTER`, `BATTLE_CENTER`, and so on).
- `m_OffsetsH`: **Observed.** Horizontal offsets for the spawned effects.
- `m_OffsetsV`: **Observed.** Vertical offsets for the spawned effects.
- `m_Sounds`: **Confirmed.** Up to three `Sound/SoundList.m_id` rows triggered by the stage.
- `m_SoundTimes`: **Observed.** Trigger times for the sound rows.
- `m_SoundToVoice`: **Observed.** Rare routing/link flag controlling whether the sound is tied to voice-style timing/handling.
- `m_Voices1`: **Confirmed.** Primary voice slots pointing to `Sound/PartVoiceID.m_id`.
- `m_Voices2`: **Confirmed.** Secondary voice slots pointing to `Sound/PartVoiceID.m_id`.
- `m_Voices3`: **Confirmed unused in OT0.** Tertiary voice slots are present but zero in OT0.
- `m_VoiceTimes`: **Observed.** Voice trigger times.
- `m_Texts`: **Proven.** Up to ten floating text IDs. Most OT0 values resolve through `GameTextSkill`, some through `GameTextUI`, and a small residue currently sits outside the obvious OT0 banks.
- `m_TextTime`: **Observed.** Floating-text trigger times.
- `m_CharaVisibility`: **Confirmed.** Per-visibility-stage values from `ESKILLEFFECTIVE_CHARA_VISIBILITY`.
- `m_VisibilityTarget`: **Confirmed.** Per-visibility-stage values from `ESKILLEFFECTIVE_VISIBILITY_TARGET`.
- `m_VisibilityStartTime`: **Observed.** Start time for the visibility change.

#### Concrete OT0 examples
- Floating text is not hypothetical. `SkillEffectiveID#20049` carries the strings `<skl_user_name> calls for his lackeys!` and `However, the lackeys did not appear...`, which is the exact presentation companion to reinforcement-style enemy skills.
- Visibility staging is also real, not a leftover field. `SkillEffectiveID#65047` and `#65052`, both used by `Lotus Blade`, toggle target visibility with `INVISIBLE -> VISIBLE` on `TARGET`.
- `SkillEffectiveID#65954` and `#65956`, used by `Pilfer`, show the fade variants rather than the hard toggle variants, using `FADE_INVISIBLE` / `FADE_VISIBLE` on `TARGET`.
- `SkillEffectiveID#65572` and `#65631`, used by `Hunter's Drive`, target visibility selector `11`, which matches `FRIENDLY_ALL_WITHOUT_SELF` from the SDK enum. This is a good proof that the visibility target enum is really being consumed verbatim.
- `SkillEffectiveID#66152`, used by `Soaring Javelin` and `Take Aim`, applies a visibility stage to target selector `20`, i.e. `ENEMY_ALL`, which proves the same system is not limited to one-on-one stealth effects.

#### CotC-only extra fields
- `m_MagicStonePerformance`: **Confirmed from CotC schema.** CotC-specific magic-stone performance asset or mode.
- `m_MagicStonePerformanceStartTime`: **Confirmed from CotC schema.** Start time for the previous field.
- `m_MagicStoneLevel`: **Confirmed from CotC schema.** Level or tier selector for magic-stone presentation.
- `m_CameraShakeCurve`: **Confirmed from CotC schema.** Camera-shake asset/curve.
- `m_CameraShakeStartTime`: **Confirmed from CotC schema.** Start time for the camera shake.
- `m_Text2nd`: **Confirmed from CotC schema.** Second text bank/secondary floating text.
- `m_SkillEffectiveLinkageID`: **Confirmed from CotC schema and ID overlap.** `SkillEffectiveLinkageList.m_id` for chained effect groups.
- `m_IsSetVisibleUI`: **Observed from name and CotC usage.** Enable UI visibility control during the stage.
- `m_UIVisiblityFlag`: **Observed from name and CotC usage.** UI visibility mode/flag.
- `m_UIVisibilityTarget`: **Observed from name and CotC usage.** UI visibility target selector.
- `m_UIVisibilityStartTime`: **Observed from name and CotC usage.** Start time for the UI visibility change.

#### OT0 versus CotC note
- `Camera schema shift`: OT0 keeps camera rows inline through `m_Cameras` and `m_CameraTimes`. The CotC `SkillEffectiveID` schema in the exported `Skill/` folder drops those inline arrays and instead expands secondary UI/effect linkage fields.

### `SkillAilmentType`
Reusable ailment/state package. This file covers classic negative statuses, positive buffs, counters, intercepts, reraise states, evasion states, damage-limit auras, permanent states, and many other timed or triggered effects. Note: newly added entries to this file are not functional (beyond aesthetics, replacing character sprites, and appending weapon and magic types), even if they use the label of an existing ailment!

- **OT0 rows:** `490`.
- **CotC rows:** `968`.
- **OT0 SDK row struct:** `FSkillAilmentTypeBase`.
- **Related OT0 SDK enums:** `Kingship::ESKILL_INVOKE`, `ESKILL_AILMENT_CALC_TYPE`.
- This is one of the densest and most important tables in the entire skill system. `SkillAvailID` frequently does very little by itself beyond pointing here.

#### Exact OT0 SDK enum quotes that directly drive this row
##### `Kingship::ESKILL_INVOKE` (timing enum)
Quoted from `SDK/Kingship_structs.hpp:6436`.
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
- `SkillAilmentType.m_InvokeTiming` uses the same timing enum family that `SkillAvailID.m_InvokeTiming` uses. The engine therefore schedules ailment ticks and stage execution on the same global timing vocabulary.

##### `EAILMENT_CONDITION_ID`
Quoted from `SDK/Kingship_structs.hpp:531`.
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
- `m_RemoveConditions` stores raw values from this ailment-condition enum. This is why rows like sleep, countdown, or break-linked states can express precise removal triggers such as `PHYSICAL_DAMAGE`, `BREAK_OR_DEAD_ENFORCER`, or `CAN_NOT_ACTION`.

##### `ESKILL_AILMENT_CALC_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6208`.
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
- `m_AilmentCalc` stores raw values from this enum, and `SkillAvailID.m_CalcTypeAilment` appears to use the same family for the ailment rows it adds. This is the exact reason damage-derived buffs, limit modifiers, and related ailment computations share one numeric vocabulary.
- `m_ResistWeapon` and `m_ResistMagic` should be read against the exact `EWEAPON_TYPE` and `EMAGIC_TYPE` quotes embedded above in `WeaponType` and `MagicType`, because those arrays are keyed by the same family order even though the ailment row stores them as masks rather than scalar IDs.

#### Field-by-field
- `m_id`: **Proven.** Primary key for the ailment row. Referenced by `SkillAvailID.m_AddAilment`, `m_SubAilment`, `SkillConditionList.m_AilmentTypes`, `SkillResistAilmentID.m_ResistAilments`, `m_RemoveAilment`, and `m_ChildAilment`.
- `m_Label`: Internal/editor label. This is often the fastest human-readable way to identify what the row is meant to do.
- `m_Resist`: **Observed with strong evidence.** Whether normal ailment-resistance logic applies to this row. It is true on classic negative statuses such as poison, bleed, sleep, paralysis, fear, and darkness.
- `m_Minus`: **Observed.** Negative/debuff-like ailment flag.
- `m_Plus`: **Observed.** Positive/buff-like ailment flag.
- `m_Removable`: **Proven.** Whether ordinary cure/clear behavior can remove the ailment. Will also make the ailment permanent if true.
- `m_Multiply`: **Observed.** Stacking/coexistence behavior flag. Many ordinary buff and debuff states leave it true, while some singleton control states do not. Does not work for some ailments, like the Max HP debuff.
- `m_OverwriteTurn`: **Observed.** Reapply by overwriting stored turns instead of merely extending or ignoring the existing state.
- `m_ForceOverrideValue`: **Observed.** Reapply by overwriting the stored ailment value as well as the turn count.
- `m_InvalidateDebuff`: **Confirmed by usage pattern.** Marks the row as a debuff-class state for interactions that invalidate or suppress debuffs. OT0 sets it on debuff rows such as defense-down, attack-down, weapon-weakening, and element-weakening states.
- `m_CanInactiveFlag`: **Observed.** Allows an inactive/off-state representation. OT0 sets it on ordinary buff/debuff rows and their permanent variants, which is exactly where you would expect state toggling or remembered-but-inactive behavior.
- `m_InvokeTiming`: **Confirmed.** Trigger timing from the richer `Kingship::ESKILL_INVOKE` enum. OT0 uses timings such as `BATTLING`, `ACTION_DAMAGE`, `SLIP_DAMAGE`, `FORWARD`, `ALWAYS`, and more.
- `m_TargetStatus`: **Confirmed.** `Character/CharaStatusID.m_id` affected by the ailment when it modifies a concrete stat/resource.
- `m_TargetStatusArray`: **Confirmed shape.** Extra status targets for multi-status ailments. OT0 barely uses it.
- `m_AilmentCalc`: **Confirmed.** Ailment-side calculation mode from `ESKILL_AILMENT_CALC_TYPE`. OT0 mostly leaves it `NONE`, but damage-derived and limit-derived states do use the higher values.
- `m_EfficacyStepRate`: **Observed.** Step-rate scaling parameter for ailment efficacy. Rare in OT0.
- `m_ResistWeapon`: **Confirmed shape and observed meaning.** Boolean weapon-family mask. Despite the name, OT0 uses this heavily on counter and intercept rows to define which weapon hit families the state reacts to.
- `m_ResistMagic`: **Confirmed shape and observed meaning.** Boolean element-family mask used the same way for magic/element counters and intercepts.
- `m_RemoveAilment`: **Confirmed.** Up to four `SkillAilmentType.m_id` rows removed when this ailment is applied. Example: stronger variants or invalidate states can remove their weaker predecessors.
- `m_TargetType`: **Observed.** Small ailment-internal target selector. OT0 only uses nonzero values on three `...TARGET_IS_ENEMY_ALL` counter/intercept rows, so do not treat it as the full battle target enum.
- `m_RemoveConditions`: **Observed.** Condition opcodes that control how the ailment gets removed or broken. Sleep is a good example: its OT0 row uses a nonzero remove-condition so damage/action can clear it.
- `m_RemoveParams`: **Observed.** Parameters for the remove-condition slots.
- `m_ChildAilment`: **Confirmed.** `SkillAilmentType.m_id` of a child ailment chained to this one.
- `m_TextID`: **Confirmed.** Visible ailment name text ID, primarily in `GameTextUI`. Example: poison, darkness, and buff-state labels resolve here.
- `m_ExplanationTextID`: **Confirmed.** Visible ailment explanation text ID, again primarily in `GameTextUI`.
- `m_IconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the main ailment icon.
- `m_IconSptTexID`: **Confirmed.** `Texture/TextureID.m_id` for the support/secondary ailment icon. In OT0 it usually matches `m_IconTexID`.
- `m_AddEffect`: **Confirmed.** `Effect/EffectList.m_id` spawned when the ailment is added.
- `m_CharacterEffect`: **Confirmed.** `Effect/EffectList.m_id` attached to the afflicted character while the ailment remains active.
- `m_FieldEffect`: **Confirmed.** `Effect/EffectList.m_id` used by field-map variants of the ailment.
- `m_InvokeEffect`: **Confirmed.** `Effect/EffectList.m_id` spawned when the ailment triggers or ticks.
- `m_OnGroundInvoke`: **Observed.** Allow the ailment's invoke/tick behavior outside normal battle action timing, such as field-ground use cases.
- `m_AddEffectSound`: **Confirmed.** `Sound/SoundList.m_id` played when the ailment is applied.
- `m_InvokeSound`: **Confirmed.** `Sound/SoundList.m_id` played when the ailment triggers or ticks.
- `m_ActionID`: **Confirmed.** `Battle/BattleActionID.m_id` animation hook for the ailment state.
- `m_ActionPriority`: **Confirmed unused in OT0.** Present in the struct but OT0 leaves it zero.
- `m_ReplaceCharaID`: **Proven; unused in OT0.** Replaces the user's sprite with the one referenced here. Maps into `CharaID.m_id`.
- `m_CountDown`: **Observed.** Marks the ailment as countdown-style.
- `m_NoCount`: **Observed.** Prevent ordinary turn/count consumption. OT0 uses this on absorb, guaranteed/probabilistic evasion, guts, and mastery-like states.
- `m_StatusDetailSort`: **Observed.** UI ordering key for ailment/detail display.
- `m_IgnoreLimit`: **Observed.** Bypass ordinary buff/debuff cap rules. OT0 uses it on SP-cost modifiers, charm-like states, damage-limit increases, and similar special-case auras.
- `m_RemainDead`: **Observed.** Keep the ailment meaningful on dead units or across death. OT0 uses it on reraise-like states and a few special persistent states.
- `m_IsNotMemorize`: **Observed.** Rare memory/snapshot exclusion flag. OT0 only uses it on two rows.
- `m_AbsorbHealStatus`: **Confirmed.** `Character/CharaStatusID.m_id` restored by absorb/heal-style ailment logic.
- `m_NotRace`: **Observed.** Invert or negate the race filter in `m_EnemyRace`. Rare in OT0.
- `m_EnemyRace`: **Observed.** Small race-filter array for ailment logic. Rare in OT0.
- `m_ForceSupportAbility`: **Confirmed unused in OT0.** Always false.
- `m_ForceCommandAbility`: **Observed.** Rare flag set on standard negative statuses like poison, bleed, paralysis, and darkness. The safest reading is that it keeps these rows in a command-ability interaction path, but OT0 does not give a better named caller in the SDK dump.
- `m_WeaponAppendType`: **Proven.** Weapon-append behavior type ID. Despite the name, OT0 values are not a clean `EWEAPON_TYPE` mapping, so treat this as a higher-level append-behavior selector rather than a raw weapon enum.
- `m_MagicAppendType`: **Proven.** Element-append behavior type ID. Again, OT0 values are not a plain `EMAGIC_TYPE` mapping.
- `m_DuplicateAilment`: **Observed.** Allow duplicate instances of the same ailment. OT0 uses it on only one row.
- `m_SkillReplace`: **Confirmed unused in OT0.** Reserved skill-replacement hook, uses `SkillAilmentSkillReplaceList.m_id` most likely.

#### Concrete OT0 examples
- Plain status families are easy to see directly: `SkillAilmentType#1 POISON` is `Poison`, `#8 HIGH_POISON` is `Toxin`, `#2 BLEED` is `Bleeding`, and `#6 FEAR` is `Terror`.
- `m_RemoveConditions` is not decorative. `SkillAilmentType#4 SLEEP` uses `m_RemoveConditions = [10,0]`, while `#285 DEEP_SLEEP` uses `[1,0]`; OT0 is clearly distinguishing different removal/break logic families inside the same broad sleep theme.
- The counter/intercept families show why `m_ResistWeapon` and `m_ResistMagic` are better read as reaction masks than as ordinary resistance tables. `#52 COUNTER_SWORD` and `#70 INTERCEPT_SWORD` are representative weapon-specific rows in those families.
- `m_CountDown` is a real gameplay flag. `SkillAilmentType#20 COUNTDOWN` is `Fatal Sentence`, while `#43 DELAYED_SKILL` and `#44 DELAYED_RANGE_SKILL` are both visible `Countdown` variants that trigger queued skills at count end.
- `m_ForceCommandAbility` is not random noise. OT0 sets it on classic negative statuses such as `#1 POISON`, `#2 BLEED`, `#5 PARALYSIS`, and `#11 DARKNESS`.
- `m_IgnoreLimit` is also real. `#203 BUFF_SP_COST_REDUCE (SP Cost Down)`, `#226 BUFF_REDUCE_DAMAGE_HP (Reduce Damage)`, and `#397 INCREASE_DAMAGE_LIMIT (Surpassing Power)` all carry it, which fits the idea that they bypass normal buff/debuff-cap handling.
- `m_RemainDead` is not limited to one row. `#283 RERAISE_BUFF (Encore)` and `#503 RERAISE_BUFF_LAST_DIED (Last Man Standing)` both use it, which is exactly what you would expect from states that still matter after incapacitation.

#### CotC-only extra fields
- `m_IsBuffDebuff`: **Confirmed from CotC schema.** Explicit buff/debuff classification flag.
- `m_CompareTurn`: **Confirmed from CotC schema.** Additional turn-comparison parameter.
- `m_CompareOverrideValue`: **Confirmed from CotC schema.** Additional value-comparison parameter.
- `m_DisableBoostAnim`: **Observed from name and CotC usage.** Suppress boost animation when the ailment fires.
- `m_StatusDetailAllName`: **Confirmed from CotC schema.** Richer status-detail name text ID for all-target displays.
- `m_StatusDetailName`: **Confirmed from CotC schema.** Richer status-detail name text ID for single-target displays.
- `m_StatusDetailCommentTextID`: **Confirmed from CotC schema.** Comment text for status-detail UI.
- `m_StatusDetailCommentCalcTextID`: **Confirmed from CotC schema.** Calculation-aware comment text for status-detail UI.
- `m_StatusDetailCommentRatedTextID`: **Confirmed from CotC schema.** Rated comment text for status-detail UI.
- `m_StatusDetailCommentNowRatedTextID`: **Confirmed from CotC schema.** Current-value rated comment text for status-detail UI.
- `m_StatusDetailCommentFixedTextID`: **Confirmed from CotC schema.** Fixed-value comment text for status-detail UI.
- `m_StatusDetailAllCommentTextID`: **Confirmed from CotC schema.** All-target comment text.
- `m_StatusDetailAllCommentCalcTextID`: **Confirmed from CotC schema.** All-target calculation-aware comment text.
- `m_StatusDetailAllCommentRatedTextID`: **Confirmed from CotC schema.** All-target rated comment text.
- `m_StatusDetailAllCommentNowRatedTextID`: **Confirmed from CotC schema.** All-target current-rated comment text.
- `m_StatusDetailAllCommentFixedTextID`: **Confirmed from CotC schema.** All-target fixed-value comment text.
- `m_AllowAddAilmentOnDead`: **Confirmed from CotC schema.** Permit application to dead units.
- `m_MaxUseCount`: **Confirmed from CotC schema.** Maximum number of times the ailment can fire.
- `m_IsAttributeCheckAppendType`: **Observed from name and CotC usage.** Attribute-aware append-type validation.
- `m_BindWeakID`: **Observed from name and CotC usage.** Weakness-binding helper row or key.
- `m_IsAdditionWeak`: **Observed from name and CotC usage.** Marks the ailment as part of CotC's additional-weakness mechanics.
- `m_AilmentTagID`: **Confirmed from CotC schema.** Tag/group identifier for the ailment.
- `m_Attribute`: **Confirmed from CotC schema.** Attribute selector for CotC ailment logic.
- `m_TargetAilment`: **Confirmed from CotC schema.** Target ailment ID for transformation/interaction logic.
- `m_ToUnlimited`: **Observed from name and CotC usage.** Convert to unlimited duration.
- `m_Parallel`: **Observed from name and CotC usage.** Allow parallel coexistence of related states.
- `m_PhaseOver`: **Observed from name and CotC usage.** Persist through phase transitions.
- `m_SkillFilterType`: **Confirmed from CotC schema.** `SkillFilterType.m_id` used by CotC filter UI and categorization.

### `SkillAilmentOffsets`
Per-model ailment anchor preset. This table lives under `Skill/`, but its most obvious downstream OT0 consumer is `Enemy/EnemyTypeID.m_AilmentOffset` rather than `SkillID` itself.

- **OT0 rows:** `853`.
- **CotC rows:** `922`.
- **OT0 SDK row struct:** `FSkillAilmentOffsetsBase`.
- The row exists because ailment icons/effects need different anchor positions on different enemy body shapes. OT0 stores one preset row containing all ailment-family anchor pairs.

#### Field-by-field
- `m_id`: **Confirmed.** Offset preset ID. OT0 `Enemy/EnemyTypeID.m_AilmentOffset` points here.
- `m_PoisonX`: **Confirmed by field name and downstream usage.** Poison anchor X in the preset row for that battler model/type.
- `m_PoisonY`: **Confirmed by field name and downstream usage.** Poison anchor Y in the preset row for that battler model/type.
- `m_BleedX`: **Confirmed by field name and downstream usage.** Bleed anchor X in the preset row for that battler model/type.
- `m_BleedY`: **Confirmed by field name and downstream usage.** Bleed anchor Y in the preset row for that battler model/type.
- `m_SilenceX`: **Confirmed by field name and downstream usage.** Silence anchor X in the preset row for that battler model/type.
- `m_SilenceY`: **Confirmed by field name and downstream usage.** Silence anchor Y in the preset row for that battler model/type.
- `m_SleepX`: **Confirmed by field name and downstream usage.** Sleep anchor X in the preset row for that battler model/type.
- `m_SleepY`: **Confirmed by field name and downstream usage.** Sleep anchor Y in the preset row for that battler model/type.
- `m_PalalysisX`: **Confirmed by field name and downstream usage.** Paralysis anchor X in the preset row for that battler model/type.
- `m_PalalysisY`: **Confirmed by field name and downstream usage.** Paralysis anchor Y in the preset row for that battler model/type.
- `m_FearX`: **Confirmed by field name and downstream usage.** Fear anchor X in the preset row for that battler model/type.
- `m_FearY`: **Confirmed by field name and downstream usage.** Fear anchor Y in the preset row for that battler model/type.
- `m_WasteX`: **Confirmed by field name and downstream usage.** Waste/SP-waste anchor X in the preset row for that battler model/type.
- `m_WasteY`: **Confirmed by field name and downstream usage.** Waste/SP-waste anchor Y in the preset row for that battler model/type.
- `m_DarknessX`: **Confirmed by field name and downstream usage.** Darkness anchor X in the preset row for that battler model/type.
- `m_DarknessY`: **Confirmed by field name and downstream usage.** Darkness anchor Y in the preset row for that battler model/type.
- `m_HeadacheX`: **Confirmed by field name and downstream usage.** Headache anchor X in the preset row for that battler model/type.
- `m_HeadacheY`: **Confirmed by field name and downstream usage.** Headache anchor Y in the preset row for that battler model/type.
- `m_EnergyDrainX`: **Confirmed by field name and downstream usage.** Energy-drain anchor X in the preset row for that battler model/type.
- `m_EnergyDrainY`: **Confirmed by field name and downstream usage.** Energy-drain anchor Y in the preset row for that battler model/type.
- `m_RegenerateX`: **Confirmed by field name and downstream usage.** Regenerate anchor X in the preset row for that battler model/type.
- `m_RegenerateY`: **Confirmed by field name and downstream usage.** Regenerate anchor Y in the preset row for that battler model/type.
- `m_ProvokeX`: **Confirmed by field name and downstream usage.** Provoke anchor X in the preset row for that battler model/type.
- `m_ProvokeY`: **Confirmed by field name and downstream usage.** Provoke anchor Y in the preset row for that battler model/type.
- `m_CounterX`: **Confirmed by field name and downstream usage.** Counter anchor X in the preset row for that battler model/type.
- `m_CounterY`: **Confirmed by field name and downstream usage.** Counter anchor Y in the preset row for that battler model/type.
- `m_InterceptX`: **Confirmed by field name and downstream usage.** Intercept anchor X in the preset row for that battler model/type.
- `m_InterceptY`: **Confirmed by field name and downstream usage.** Intercept anchor Y in the preset row for that battler model/type.
- `m_BreakX`: **Confirmed by field name and downstream usage.** Break anchor X in the preset row for that battler model/type.
- `m_BreakY`: **Confirmed by field name and downstream usage.** Break anchor Y in the preset row for that battler model/type.
- `m_CountDownX`: **Confirmed by field name and downstream usage.** Countdown anchor X in the preset row for that battler model/type.
- `m_CountDownY`: **Confirmed by field name and downstream usage.** Countdown anchor Y in the preset row for that battler model/type.
- `m_PerfectInterceptScale`: **Observed.** Extra scale value for perfect-intercept style display. OT0 populates it on every row.

### `SkillAilmentSkillReplaceList`
Small ailment-driven skill-replacement helper. OT0 effectively leaves it dormant, but the schema is still worth documenting.

- **OT0 rows:** `1`.
- **CotC rows:** `12`.
- **OT0 SDK row struct:** `FSkillAilmentSkillReplaceListBase`.

#### Field-by-field
- `m_id`: Primary key for the replacement package.
- `m_SkillList`: **Observed.** Fixed list of replacement `SkillID` rows.
- `m_AilmentType`: **Confirmed.** `SkillAilmentType.m_id` that triggers the replacement package.

### `SkillResistList`
Inline percentage-based weapon/element resistance payloads. This table is often confused with `EnemyWeakID`, but the stored values prove that they are different systems.

- **OT0 rows:** `215`.
- **CotC rows:** `241`.
- **OT0 SDK row struct:** `FSkillResistListBase`.

#### Field-by-field
- `m_id`: Primary key for the resistance payload row.
- `m_ResistWeapon`: **Confirmed.** Direct percentage/delta array by weapon family. OT0 values include entries such as `50`, `27`, `22`, `12`, `2`, `-40`, `-50`, and `-100`, so this is clearly **not** a `ResistType` ID array.
- `m_ResistMagic`: **Confirmed.** Direct percentage/delta array by element family, with the same semantics as `m_ResistWeapon`.

### `SkillResistAilmentID`
Packaged ailment-resistance rows used by skills and enemy rows that want explicit ailment/rate pairs instead of one inline base-rate array.

- **OT0 rows:** `74`.
- **CotC rows:** `60`.
- **OT0 SDK row struct:** `FSkillResistAilmentIDBase`.

#### Field-by-field
- `m_id`: Primary key for the ailment-resistance package.
- `m_ResistAilments`: **Confirmed.** List of up to sixteen `SkillAilmentType.m_id` rows covered by the package.
- `m_ResistRate`: **Confirmed.** Matching rate array for `m_ResistAilments`. The arrays are parallel.

### `SkillHitRateList`
Reusable multi-hit and repeated-resolution probability table. `SkillAvailID.m_HitTypes` points here.

- **OT0 rows:** `68`.
- **CotC rows:** `72`.
- **OT0 SDK row struct:** `FSkillHitRateListBase`.

#### Field-by-field
- `m_id`: Primary key for the hit-rate package.
- `m_MinCount`: **Observed.** Minimum guaranteed count before random continuation is considered.
- `m_CountAilment`: **Confirmed shape.** Additional ailment/count reference channel. OT0 leaves it zero on every row, but CotC does use it.
- `m_HitRates`: **Observed.** Sixteen-slot probability table controlling extra hit rolls or repeated-resolution count.
- `m_Shuffle`: **Observed.** Randomize/shuffle behavior for the hit-rate pattern.

### `SkillIconList`
Small paired icon bank. Real, but not the primary target of `SkillID.m_SkillIconID` in OT0.

- **OT0 rows:** `11`.
- **CotC rows:** `12`.
- **OT0 SDK row struct:** `FSkillIconListBase`.

#### Field-by-field
- `m_id`: Primary key for the icon pair.
- `m_BattleIconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the in-battle icon.
- `m_MenuIconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the menu icon.

### `SkillUniqueIconID`
Unique icon/text override table for special command panels or buff displays.

- **OT0 rows:** `39`.
- **CotC rows:** `137`.
- **OT0 SDK row struct:** `FSkillUniqueIconIDBase`.
- OT0 `SkillID` rows do not actually use this table, but the table itself is fully populated and cleanly structured.

#### Field-by-field
- `m_id`: Primary key for the unique icon row.
- `m_TextID`: **Observed.** Text label for the unique icon. A few OT0 IDs resolve cleanly in `GameTextUI` (for example `65490 -> Hang Tough`), while many OT0 IDs are not present in the currently exported OT0 text banks. Treat that unresolved subset as legacy/shared content rather than as proof that the field is unused.
- `m_SkillIconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the unique icon texture.
- `m_OverrideCommandPanel`: **Observed.** Use this unique icon/text in the command panel.
- `m_OverrideInBattle`: **Observed.** Use this unique icon in battle UI.
- `m_OverrideBuff`: **Observed.** Use this unique icon for buff/state display.

### `SkillSupplyItemGroup`
OT0 village/supply helper table. It binds a skill opcode to a reward lottery group.

- **OT0 rows:** `45`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillSupplyItemGroupBase`.
- **Related OT0 SDK enums:** `ESKILL_MODIFY_TYPE`.

#### Concrete OT0 examples
- The first visible material family is already enough to show the pattern: `SkillSupplyItemGroup#100/#101` point to `Timber Supply` / `Timber Supply+`, and `#200/#201` point to `Top Timber Supply` / `Top Timber Supply+`.
- The same pattern repeats for stone, cloth, ore, and other village materials. In other words, the table is not a generic battle-reward list; it is a curated village supply lottery keyed by the triggering skill.
- OT0 uses `m_SkillModifyType = 201` for most of these rows, which lines up with the `ITEM_SUPPLY` branch of `ESKILL_MODIFY_TYPE`.

#### Field-by-field
- `m_id`: Primary key for the supply-item group.
- `m_SkillId`: **Confirmed.** `SkillID.m_id` of the skill that triggers this lottery group.
- `m_SkillModifyType`: **Confirmed by enum/value overlap.** Economy opcode from `ESKILL_MODIFY_TYPE`. OT0 uses mostly `201 ITEM_SUPPLY`, with a few `208 FARM_HARVEST_ITEM_AMOUNT_LOTUP` and `254 ITEM_SUPPLY_LOT_IN_GROUP` rows.
- `m_Lot`: **Observed.** Group-level lottery weight or multiplier. OT0 leaves it at `100` on every row.

### `SkillSupplyItemList`
OT0 village/supply lottery entries. This is the payload table that `SkillSupplyItemGroup` chooses from.

- **OT0 rows:** `166`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillSupplyItemListBase`.

#### Field-by-field
- `m_id`: Primary key for the lottery entry.
- `m_GroupId`: **Confirmed.** `SkillSupplyItemGroup.m_id` parent group.
- `m_RewardId`: **Observed with strong evidence.** Reward-type discriminator. OT0 uses `1000` for money/leaves entries and `1001` for item-reward entries.
- `m_ItemId`: **Observed with strong evidence.** `Item/ItemList.m_id` for item rewards, or `0` for money rewards.
- `m_Values`: **Observed.** Reward amount or item count. For `m_RewardId = 1000` this is the leaf amount; for `1001` it is the item quantity.
- `m_Lot`: **Observed.** Per-entry lottery weight inside the parent group.
- `m_IsUnique`: **Observed.** Unique reward flag. OT0 uses it on only two entries in group `4000`.

### `SkillTargetShopList`
OT0 target-shop helper rows for village/trade skills.

- **OT0 rows:** `24`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillTargetShopListBase`.
- **Related OT0 SDK enums:** `ESHOP_TYPE`.

#### Exact OT0 SDK enum quote used by this table
##### `ESHOP_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6100`.
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
- `SkillTargetShopList.m_ShopType` stores the raw `ESHOP_TYPE` numeric value. The helper row exists so a skill can bind not just a shop family but also the concrete map context that the skill should target.

#### Concrete OT0 examples
- Rows `#1..#16` cover trade-price skills such as `Savvy Shopper`, `Master Haggler`, `Savvy Seller`, `Master Salesman`, `Appraiser`, `Grand Appraiser`, `Wholesale Expert`, and `Master Tradesman`; all of those use `m_ShopType = 22`, i.e. `eTRADE`.
- Rows `#17..#24` cover village-base utility skills such as `Negotiator`, `Fixer`, `Cook`, and `Grand Chef`; those use `m_ShopType = 17`, i.e. `eVILLAGE_BASE`.
- Every stock OT0 row uses `m_MapId = 900002`, which is why the table reads less like a universal shop catalog and more like a village-system helper table scoped to one map context (that being Wishvale).

#### Field-by-field
- `m_id`: Primary key for the target-shop row.
- `m_SkillId`: **Confirmed.** `SkillID.m_id` of the shop-targeting skill.
- `m_ShopType`: **Confirmed.** `ESHOP_TYPE` selector for the target shop. OT0 uses `17 (eVILLAGE_BASE)` and `22 (eTRADE)`.
- `m_MapId`: **Confirmed.** `Map/MapListTable.m_id` providing the map context of the target shop. OT0 uses `900002`, the village base map, on every row.

### `SkillTargetFarmItemList`
OT0 ranch/farm helper rows for skills that append or target specific village farm items.

- **OT0 rows:** `2`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillTargetFarmItemListBase`.
- **Related OT0 SDK enums:** `ESKILL_MODIFY_TYPE`.

#### Concrete OT0 examples
- Both stock OT0 rows belong to the `Sheep King` family: `SkillTargetFarmItemList#1` points to `SkillID#900938 Sheep King`, and `#2` points to `SkillID#903901 Sheep King+`.
- Both rows target `m_VillageFarmItemId = 103`; the secondary IDs `105` and `106` exist as mostly empty placeholders in stock OT0, which is why this field should be treated cautiously when cloning the pattern.

#### Field-by-field
- `m_id`: Primary key for the farm-target row.
- `m_SkillId`: **Confirmed.** `SkillID.m_id` of the farm-targeting skill.
- `m_SkillModifyType`: **Confirmed.** OT0 uses `215`, which is `RANCH_APPEND_ITEM` in `ESKILL_MODIFY_TYPE`.
- `m_VillageFarmItemId`: **Confirmed.** `Village/VillageFarmItem.m_id` of the main target farm item.
- `m_AdditionalVillageFarmItemId`: **Confirmed.** Secondary `Village/VillageFarmItem.m_id`. In OT0 the referenced rows are empty placeholders (`105` and `106`), so do not assume this field already has meaningful live content in stock OT0.

### `SkillVisitorList`
Visitor/summon helper rows. OT0 barely uses the table, but the one live row is enough to show the intended shape.

- **OT0 rows:** `2`.
- **CotC rows:** `7`.
- **OT0 SDK row struct:** `FSkillVisitorListBase`.

#### Concrete OT0 examples
- OT0 has only one live nonzero visitor row: `SkillVisitorList#1`.
- That row summons `m_EnemyID = 33000`, whose enemy label is `Summon_PC072_Linde`.
- Its placement data is not trivial filler either: `m_OffsetsX[0] = -500.0` and `m_IconID = 1000`, which is a strong hint that the visitor table is handling both spawn layout and UI identity for summon-style guests.

#### Field-by-field
- `m_id`: Primary key for the visitor row.
- `m_EnemyID`: **Confirmed in OT0.** `Enemy/EnemyID.m_id` of the visitor battler. OT0's live nonzero row uses `33000`, which is `Summon_PC072_Linde`.
- `m_EscortNum`: **Observed.** Per-slot escort count array.
- `m_EscortType`: **Observed.** Per-slot escort/layout type array. OT0 leaves it zero.
- `m_OffsetsY`: **Observed.** Per-slot Y offsets for visitor placement.
- `m_OffsetsX`: **Observed.** Per-slot X offsets for visitor placement.
- `m_CallSE`: **Observed.** Per-slot call sound IDs. OT0 leaves them zero.
- `m_IconID`: **Unresolved.** Visitor UI icon identifier. OT0 only uses value `1000` on the live row, and the target table is not proven by the current exports.

#### CotC schema change
- `m_EnemyType`: **Confirmed from CotC schema.** CotC stores an enemy type here instead of a direct OT0-style `EnemyID`.

### `SkillEnhancedList`
Enhancement helper table. OT0 effectively leaves it empty, but the schema is straightforward.

- **OT0 rows:** `1`.
- **CotC rows:** `10`.
- **OT0 SDK row struct:** `FSkillEnhancedListBase`.

#### Field-by-field
- `m_id`: Primary key for the enhancement package.
- `m_EnhancedSkills`: **Observed.** Fixed list of enhanced skill IDs. OT0 only ships the zero row, so there is no stock content to model from.

### `SpecialSkillID`
Special-skill master key table. OT0 stores only the IDs here; the actual per-level mapping lives in `SpecialSkillGrowthList` and the player binding lives in `Character/CharaPlayerSpecialSkillList`.

- **OT0 rows:** `86`.
- **CotC rows:** `266`.
- **OT0 SDK row struct:** `FSpecialSkillIDBase`.

#### Field-by-field
- `m_id`: Primary key for the special-skill family.

### `SpecialSkillGrowthList`
Per-level mapping from a special-skill family to the concrete `SkillID` row granted at that level.

- **OT0 rows:** `497`.
- **CotC rows:** `5301`.
- **OT0 SDK row struct:** `FSpecialSkillGrowthListBase`.

#### Field-by-field
- `m_id`: Primary key for the growth row.
- `m_SpecialSkillID`: **Confirmed.** `SpecialSkillID.m_id` of the special-skill family being grown.
- `m_Level`: **Confirmed.** Growth level. OT0 usually uses `1..3` for player-facing EX/special skills, but the file itself contains rows up to level `10`.
- `m_SkillID`: **Confirmed.** `SkillID.m_id` granted or substituted at that special-skill level.
- `m_DisplayPriority`: **Observed.** UI sort key among growth rows. OT0 mostly leaves it `0`, but it also uses values `20..90`.

#### CotC-only extra fields
- `m_InitialGaugeValue`: **Confirmed from CotC schema.** Starting gauge for the special-skill level.
- `m_GaugeRegenerateValue`: **Confirmed from CotC schema.** Gauge regeneration per cycle/turn.
- `m_CountLimit`: **Confirmed from CotC schema.** Usage cap.
- `m_RewardTypes`: **Confirmed from CotC schema.** Reward-type array for the growth row.
- `m_ItemIDs`: **Confirmed from CotC schema.** Reward item IDs parallel to `m_RewardTypes`.
- `m_ItemCounts`: **Confirmed from CotC schema.** Reward counts parallel to the reward arrays.
- `m_AvailValues`: **Confirmed from CotC schema.** Per-level tuning values passed to the underlying avail logic.
- `m_AvailHitTypes`: **Confirmed from CotC schema.** Per-level hit-type overrides.
- `m_AilmentValues`: **Confirmed from CotC schema.** Per-level ailment potency overrides.
- `m_AilmentTurns`: **Confirmed from CotC schema.** Per-level ailment-duration overrides.
- `m_DamageLimitUp`: **Confirmed from CotC schema.** Per-level damage-cap increase.

### `ClassSkillConditionList` (CotC only)
CotC-only helper table for class-skill / EX skill use conditions. No matching dedicated OT0 SDK row struct was found in the SDK dump shipped here, so the field meanings below are derived from the CotC schema and field names.

#### Field-by-field
- `m_id`: Primary key for the class-skill condition row.
- `m_UseCondition`: **Observed from field name and CotC role.** Condition ID evaluated by the EX-/class-skill system.
- `m_ConditionText`: **Observed from field name and CotC role.** Text ID shown to the player for the condition.

### `SkillEffectiveLinkageList` (CotC only)
CotC-only chained-presentation helper. This table is referenced by `SkillEffectiveID.m_SkillEffectiveLinkageID`.

#### Field-by-field
- `m_id`: Primary key for the linkage row.
- `m_HoldTime`: Total hold time for the linked presentation group.
- `m_AvailTime`: Availability/resolution time for the linked presentation group.
- `m_OffsetX`: Group-level X offset for linked presentation placement.
- `m_OffsetY`: Group-level Y offset for linked presentation placement.
- `m_LimitMax`: Observed upper-limit field for the linkage group.
- `m_LinkageType`: Observed linkage mode selector.
- `m_Effectives`: **Confirmed by ID overlap.** Up to sixteen `SkillEffectiveID.m_id` rows emitted by this linkage package.

### `SkillFilterCategory` (CotC only)
CotC-only filter-category legend for the CotC skill filter UI.

#### Field-by-field
- `m_id`: Primary key for the filter category.
- `m_Name`: Text ID for the category name.
- `m_Priority`: UI sort priority of the category.

### `SkillFilterType` (CotC only)
CotC-only filter-type legend. `SkillID.m_ForcedSkillFilter` and `m_ExclusionSkillFilter` point here, and each filter type belongs to a filter category.

#### Field-by-field
- `m_id`: Primary key for the filter type.
- `m_Name`: Text ID for the filter name.
- `m_Priority`: UI sort priority of the filter type.
- `m_Category`: **Confirmed.** `SkillFilterCategory.m_id` parent category.

### `SkillModifyType` (CotC only)
CotC-only bridge table from modify opcodes to filter types. The row IDs line up exactly with the nonzero `SkillAvailID.m_ModifyType` values used by CotC.

#### Field-by-field
- `m_id`: **Confirmed.** Modify opcode key corresponding to `SkillAvailID.m_ModifyType`.
- `m_SkillFilterType`: **Confirmed by ID overlap.** Up to five `SkillFilterType.m_id` rows associated with that modify opcode.

### `SpecialSkillExtendCategory` (CotC only)
CotC-only legend table for special-skill extension categories.

#### Field-by-field
- `m_id`: Primary key for the extension category.
- `m_Name`: Text ID of the category name.

### `SpecialSkillExtendType` (CotC only)
CotC-only legend for extension effect types. `SpecialSkillExtendList.m_ExtendType` points here.

#### Field-by-field
- `m_id`: Primary key for the extension type.
- `m_Category`: **Confirmed.** `SpecialSkillExtendCategory.m_id`.
- `m_SubCategory`: Observed sub-type selector inside the parent category.
- `m_EffectName`: Text ID of the effect name.
- `m_DetailText`: Text ID of the effect detail/explanation.
- `m_EffectValue`: Primary numeric payload for this extension type.
- `m_SubEffectValue`: Secondary numeric payload for this extension type.
- `m_AutoLearn`: Auto-unlock flag.
- `m_SubText`: Extra text ID used alongside the main effect name/detail.

### `SpecialSkillExtendList` (CotC only)
CotC-only per-special-skill extension mapping. This is the payload table that says which extension type a special skill gets, how it is unlocked, and which rewards are bundled with it.

#### Field-by-field
- `m_id`: Primary key for the extension row.
- `m_TargetSSkill`: **Confirmed.** `SpecialSkillID.m_id` targeted by the extension.
- `m_ExtendType`: **Confirmed.** `SpecialSkillExtendType.m_id` describing the extension effect.
- `m_GetPlaceText`: Text ID telling the player where/how the extension is obtained.
- `m_PriorityItemRewardType`: Priority reward-type code.
- `m_PriorityItemID`: Priority reward item ID.
- `m_PriorityItemNum`: Priority reward item count.
- `m_RewardTypes`: General reward-type array.
- `m_ItemIDs`: Reward item IDs parallel to `m_RewardTypes`.
- `m_ItemCounts`: Reward counts parallel to the reward arrays.
- `m_OpenTimestamp`: Timestamp gate for opening/unlocking the extension.
- `m_AddSSkillLv`: Additional special-skill level granted or required by this extension row.

## Related folders that directly feed `Skill/`

### `Character/`
- ``JobID.m_BaseAttackSkill` and `m_SubAttackSkill``: **Confirmed.** These are direct `SkillID.m_id` references. For example, OT0 `JobID#1` (Warrior) uses base attack skill `700` and sub attack skill `724`.
- ``WeaponAnimationSet``: **Confirmed.** `WeaponType.m_AnimationSet` points here. This is the bridge from weapon family to battle animation family.
- ``CharaStatusID``: **Confirmed.** `SkillAvailID.m_ModifyStatus`, `SkillAilmentType.m_TargetStatus`, and `SkillAilmentType.m_AbsorbHealStatus` all point here.
- ``CharaGuest``: **Confirmed.** `SkillAvailID.m_SummonGuestID` points here for guest summon behavior.
- ``CharaPlayerSpecialSkillList``: **Confirmed.** This binds players to `SpecialSkillID` families. OT0 has `48` rows; `44` distinct `m_SpecialSkillID` values resolve cleanly to `Skill/SpecialSkillID`.

### `Enemy/`
- ``EnemyWeakID``: **Confirmed and crucial.** This is what `SkillAvailID.m_ResistID` points to. The arrays inside `EnemyWeakID` store `ResistType` IDs, which is why `m_ResistID` means “swap the weakness table”, not “add the percentage payload from `SkillResistList`”.
- ``EnemyWeakChangeID``: **Confirmed.** `SkillAvailID.m_WeakChangeID` points here for staged weakness changes.
- ``EnemyWeakLockID``: **Confirmed.** `SkillAvailID.m_WeakLockID` points here for weakness-slot locking.
- ``EnemyReinforcements``: **Confirmed.** `SkillAvailID.m_Reinforcement` points here for reinforcement summon tables.
- ``EnemyModeID``: **Confirmed.** `SkillAvailID.m_ModeChangeID` points here for enemy mode/phase changes.
- ``EnemyTypeID.m_AilmentOffset``: **Confirmed.** Points to `Skill/SkillAilmentOffsets.m_id`. This is why the ailment-offset preset table belongs in the skill ecosystem even though `SkillID` does not point to it directly.

### `Battle/`
- ``BattleActionID``: **Confirmed.** `SkillEffectiveID.m_Animations[*]` and `SkillAilmentType.m_ActionID` point here.
- ``BattleCameraParams``: **Confirmed.** OT0 `SkillEffectiveID.m_Cameras[*]` points here.

### `Effect/`, `Sound/`, and `Texture/`
- ``Effect/AttachEffect``: **Confirmed.** `SkillID.m_AttachEffect` points here.
- ``Effect/EffectList``: **Confirmed.** `SkillEffectiveID.m_Effects[*]` and multiple `SkillAilmentType` effect fields point here.
- ``Sound/SoundList``: **Confirmed.** `SkillEffectiveID.m_Sounds[*]` and `SkillAilmentType` sound fields point here.
- ``Sound/PartVoiceID``: **Confirmed.** `SkillID.m_VoiceID` and `SkillEffectiveID.m_Voices*` point here.
- ``Texture/TextureID``: **Confirmed.** `SkillID.m_SkillIconID`, `WeaponType`/`MagicType` icon fields, `SkillIconList`, `SkillUniqueIconID`, and ailment icon fields all resolve here.

### `GameText/`
- ``GameText/SystemText/GameTextSkill` and localized `GameText/Localize/*/SystemText/GameTextSkill``: **Confirmed.** Main text bank for `SkillID.m_Name`, `SkillID.m_Detail`, and most `SkillEffectiveID.m_Texts`.
- ``GameText/SystemText/GameTextUI` and localized `GameText/Localize/*/SystemText/GameTextUI``: **Confirmed.** Main text bank for `WeaponType.m_TextID`, `MagicType.m_TextID`, most `SkillAilmentType.m_TextID`/`m_ExplanationTextID`, and some `SkillID.m_Name` outliers.

### `AIBattle/` enemy-skill pipeline
- `Basic chain`: **Confirmed.** For ordinary enemies the chain is `EnemyID.m_SkillsID -> AIBattle/TacticalSkillList.m_id`, `EnemyID.m_TacticalAssignID -> AIBattle/TacticalAssignList.m_id`, `TacticalAssignList.m_Tactics[*] -> TacticalList.m_id`, and `TacticalActionList.m_SkillIndex` then indexes into the owning `TacticalSkillList.m_UseSkills` array.
- `Concrete example 1`: **Confirmed.** `EnemyID#32300` uses `m_SkillsID = 55000` and `m_TacticalAssignID = 30500`; `TacticalSkillList#55000` contains skill IDs such as `610001`, `610008`, `610009`, and `TacticalActionList` chooses them through `m_SkillIndex = 0/1/2`.
- `Concrete example 2`: **Confirmed.** `EnemyID#834` uses `m_SkillsID = 752` and `m_TacticalAssignID = 3`; `TacticalSkillList#752` contains `100405`, `100406`, `100407`, and the tactic rows choose by index.
- `Important exception`: **Confirmed.** OT0 also has enemies with `m_SkillsID = 0` but nonzero `m_TacticalAssignID` (for example some Cait and wildlife rows). So not every enemy action path is a plain tactical-skill-list lookup.
