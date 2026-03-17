## **LANGUAGE:**  [English](customskls_en.md) | [日本語](customskills_ja.md) | [中文](customskills_zh.md) | [한국어](customskills_ko.md)

# 如何添加或修改技能及相关系统

本页是一份以 OT0 为优先参照对象的逆向工程指南，覆盖 `Skill/` 文件夹以及所有直接为其供数的表。只要条件允许，文中就会区分：**可从 OT0 数据库与 SDK 确认的内容**、**只能从《歧路旅人：大陆的霸者》侧模式确认的内容**、**已经观察到但 SDK 转储尚未完整命名的内容**，以及**已通过游戏内测试证明的内容**。

- **已证明**：表示该关系或功能已经通过游戏内测试，证明其含义就是文中所述。
- **已确认**：表示该关系有导出行布局、OT0 SDK 结构体名或枚举名，以及/或者与目标表的精确 ID 重合，作为支撑证据。
- **已观察**：表示字段含义已经被行使用模式与命名强烈支持，但 OT0 的 SDK 转储并没有暴露出命名完全准确的枚举或调用方。
- **未解决**：表示该字段确实存在，而且在某些内容里很可能重要，但 OT0 还没有给出足以让我们有把握为其行为命名的直接证据，而且我也尚未在游戏内测试，或尚未见到任何变化。

## OT0 与手游版 `Skill/` 文件夹总览

| 项目 | OT0 | 手游版《歧路旅人：大陆的霸者》 |
| --- | --- | --- |
| 核心行数 | `SkillID 7330`、`SkillAvailID 7369`、`SkillEffectiveID 4381`、`SkillAilmentType 490` | `SkillID 23085`、`SkillAvailID 28023`、`SkillEffectiveID 11434`、`SkillAilmentType 968` |
| `Skill/` 下 OT0 独有的表 | `WeaponType`、`SkillCalcType`、`SkillAvailMagnificationList`、`SkillSupplyItemGroup`、`SkillSupplyItemList`、`SkillTargetShopList`、`SkillTargetFarmItemList` | 手游导出中不存在 |
| `Skill/` 下手游独有的表 | 原版 OT0 中不存在 | `ClassSkillConditionList`、`SkillEffectiveLinkageList`、`SkillFilterCategory`、`SkillFilterType`、`SkillModifyType`、`SpecialSkillExtendCategory`、`SpecialSkillExtendType`、`SpecialSkillExtendList` |
| `SkillEffectiveID` 的表现层结构 | 保留行内 `m_Cameras` 与 `m_CameraTimes` 数组 | 去掉这些行内镜头数组，加入联动、UI 可见性、镜头震动与魔石相关字段 |
| `SkillVisitorList` 的绑定方式 | 直接存储 `m_EnemyID` | 改为存储 `m_EnemyType` |
| `SpecialSkillGrowthList` | 使用 `m_DisplayPriority` 的紧凑成长行 | 更丰富的成长行，包含量表、奖励、Avail 值、异常状态值与伤害上限列 |
| `SkillID` 的侧重点 | 保留字段使用标志、直接语音钩子，以及 OT0 独有的武器替换辅助字段 | 新增过滤器/分类路由、Boost 显示变体，以及更偏自动战斗的控制元数据 |
| `SkillAvailID` 的侧重点 | 保留许多休眠的共享字段，以及 OT0 独有的村庄/增援/弱点辅助字段 | 积极使用标签计数、伤害上限、追加弱点、无视闪避与耐性操作通道 |

- OT0 的 `Skill/` 在结构上更贴近单机本篇的战斗流程、前后排机制和村庄管理系统。
- 手游版的 `Skill/` 导出则更像偏向持续运营的战斗数据栈：有额外的过滤 UI、额外的成长层、额外的伤害上限逻辑、额外的弱点系统，以及服务于自动展示/控制流程的更多元数据。
- **不要**因为某张表在其中一作里缺失，就推断对应枚举或运行时概念也从代码里消失了。很多地方其实是：运行时枚举仍然存在，只是只有其中一作把它导出成了数据表。

## 运行时模型

对于 OT0 中几乎所有处于激活状态的技能，运行时链为：

`SkillID` -> `SkillAvailID` + `SkillEffectiveID`

- `SkillID` 是根行：名称/详情文本、类别、成本、图标、Boost 家族，以及游戏玩法阶段与表现阶段的数组。
- `SkillAvailID` 是玩法载荷：目标选择、伤害/治疗/增益/减益/复活/弱点变更/增援/召唤逻辑、持续时间、次数以及次级机制。
- `SkillEffectiveID` 是表现载荷：战斗动作、镜头、效果、声音、语音、浮动文本以及可见性变化。每个 avail 都会被分配一个来自 `SkillEffectiveID` 的 ID。

异常状态是并行子系统，而不是第四个必备阶段。当技能会施加增益、减益、状态异常、反击、拦截、再起、伤害上限光环或类似状态时，通常路径是由 `SkillAvailID` 指向一个或多个 `SkillAilmentType` 行。

## 常见误解

- **已证明：** `SkillAvailID.m_ResistID` **不是** `Skill/SkillResistList.m_id`。在 OT0 中，非零值与 `Enemy/EnemyWeakID.m_id` 完全匹配，而且那个目标行的形状才真正符合“替换弱点表”这一用途。具体证明：`SkillAvailID#40085` 与 `SkillAvailID#73697` 都使用 `m_ResistID = 6527`；`EnemyWeakID#6527` 存在，而 `SkillResistList#6527` 不存在。
- **已确认：** `SkillAvailID.m_SkillAvailMagnificationCondition` 与 `m_SkillAvailMagnificationConditionArray` 的行为确实像是指向 `SkillAvailMagnificationConditionList` 的行 ID。
- **已确认：** OT0 的 `SkillAvailID.m_SkillAvailMagnification` 与 `m_SkillAvailMagnificationArray` **并不像** `SkillAvailMagnificationList.m_id`。OT0 使用的是 `110`、`130`、`150`、`180`、`200` 之类的直接值，数组值最高可到 `300`。具体示例：`Surprise Attack` 的 `SkillAvailID#74940` 使用直接值 `110/130/150/170/200`，而 `Ulti-meow` 的 `SkillAvailID#76297` 最高达到 `300`。
- **已确认：** `SkillID.m_SkillIconID` 直接指向 `Texture/TextureID`，而不是 `Skill/SkillIconList`。`SkillIconList` 依然是真实存在的，但它是一个**独立的**成对图标库。

## 主要外键样式字段的证明矩阵

- `SkillID.m_AttachEffect` -> `Effect/AttachEffect.m_id`：`10/10` 唯一的非零 OT0 值匹配。
- `SkillID.m_SkillIconID` -> `Texture/TextureID.m_id`：`24/24` 唯一的非零 OT0 值匹配。
- `SkillID.m_VoiceID` -> `Sound/PartVoiceID.m_id`：`25/25` 唯一的非零 OT0 值匹配。
- `SkillID.m_WeaponIconType` -> `Skill/WeaponType.m_id`：`9/9` 唯一的非零 OT0 值匹配。
- `SkillID.m_MagicIconType` -> `Skill/MagicType.m_id`：`7/7` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_InvokeCondition` -> `Skill/SkillConditionList.m_id`：`37/37` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_ModifyStatus` -> `Character/CharaStatusID.m_id`：`17/17` 唯一非零 OT0 值匹配。
- `SkillAvailID.m_HitTypes[*]` -> `Skill/SkillHitRateList.m_id`：`26/26` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_ResistID` -> `Enemy/EnemyWeakID.m_id`：`32/32` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_ResistAilmentID` -> `Skill/SkillResistAilmentID.m_id`：`26/26` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_WeakChangeID` -> `Enemy/EnemyWeakChangeID.m_id`：`71/71` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_WeakLockID` -> `Enemy/EnemyWeakLockID.m_id`：`64/64` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_Reinforcement` -> `Enemy/EnemyReinforcements.m_id`：`59/60` 唯一的非零 OT0 值匹配。第一个离群值是值 `140`，由 `SkillAvailID#74111` 从 `SkillID#900504` 使用。
- `SkillAvailID.m_ModeChangeID` -> `Enemy/EnemyModeID.m_id`：`28/28` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_SummonGuestID` -> `Character/CharaGuest.m_id`：`1/1` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_OverrideWeapon` -> `Skill/WeaponType.m_id`：`8/8` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_OverrideMagic` -> `Skill/MagicType.m_id`：`7/7` 唯一非零 OT0 值匹配。
- `SkillAvailID.m_SkillAvailMagnificationCondition` -> `Skill/SkillAvailMagnificationConditionList.m_id`：`9/9` 唯一的非零 OT0 值匹配。
- `SkillAvailID.m_SkillAvailMagnificationConditionArray[*]` -> `Skill/SkillAvailMagnificationConditionList.m_id`：`28/28` 唯一的非零 OT0 值匹配。
- `SkillEffectiveID.m_Cameras[*]` -> `Battle/BattleCameraParams.m_id`：`47/47` 唯一的非零 OT0 值匹配。
- `SkillEffectiveID.m_Animations[*]` -> `Battle/BattleActionID.m_id`：`92/92` 唯一的非零 OT0 值匹配。
- `SkillEffectiveID.m_Effects[*]` -> `Effect/EffectList.m_id`：`712/712` 唯一的非零 OT0 值匹配。
- `SkillEffectiveID.m_Sounds[*]` -> `Sound/SoundList.m_id`：`259/259` 唯一的非零 OT0 值匹配。
- `SkillEffectiveID.m_Voices1[*]` 和 `m_Voices2[*]` -> `Sound/PartVoiceID.m_id`：所有非零 OT0 值匹配。
- `SkillAilmentType.m_ActionID` -> `Battle/BattleActionID.m_id`：`2/2` 唯一的非零 OT0 值匹配。
- `SkillAilmentType.m_TargetStatus` 和 `m_AbsorbHealStatus` -> `Character/CharaStatusID.m_id`：所有非零 OT0 值匹配。
- `SkillAilmentType.m_IconTexID` 和 `m_IconSptTexID` -> `Texture/TextureID.m_id`：所有非零 OT0 值匹配。
- `SkillAilmentType.m_AddEffect`、`m_CharacterEffect`、`m_FieldEffect`、`m_InvokeEffect` -> `Effect/EffectList.m_id`：所有非零 OT0 值均匹配。
- `SkillAilmentType.m_AddEffectSound`、`m_InvokeSound` -> `Sound/SoundList.m_id`：匹配所有非零 OT0 值。
- `WeaponType.m_EquipType` -> `Item/ItemType.m_id`：`8/8` 唯一的非零 OT0 值匹配。
- `WeaponType.m_AnimationSet` -> `Character/WeaponAnimationSet.m_id`：`8/8` 唯一的非零 OT0 值匹配。
- `WeaponType.m_WeakIconTexID`、`WeaponType.m_SkillIconTexID`、`MagicType.m_WeakIconTexID`、`MagicType.m_SkillIconTexID`、`SkillUniqueIconID.m_SkillIconTexID` 和 `SkillIconList` 纹理字段都与 `Texture/TextureID.m_id` 完全匹配。

## 多语言交叉检查

- OT0 将日语保存为基础文本库，并将本地化文本放在 `GameText/Localize/*` 下。例如，`SkillID` 的名称/详情文本可以在 JP/EN/ZH-CN/KO-KR 之间稳定解析：`153236` 是 `復活手当` / `Vivify` / `复活处理` / `부활처치`，而 `153376` 是对应的复活说明。
- 对于异常状态 UI 文本，`SkillAilmentType` 主要指向的是 `GameTextUI`，**不是** `GameTextSkill`。示例：`62327` 是 `毒` / `Poison` / `中毒` / `독`，而 `96365` 是该状态异常的说明字符串。

## 如何读取 `Skill/` 文件夹

- `Skill/` 文件夹混合了三种截然不同的表：**运行时行**（`SkillID`、`SkillAvailID`、`SkillEffectiveID`、`SkillAilmentType`）、**查找图例**（`WeaponType`、`MagicType`、`ResistType`、`SkillCategory`、`SkillCalcType`）和**帮助程序有效负载**（`SkillHitRateList`、`SkillResistAilmentID`、`SkillTargetShopList` 等）。
- 不要将每个名为 `...ID` 的整数字段视为另一个 `Skill/` 表的外键。 OT0 使用许多直接枚举、一些内联百分比数组和一些跨文件夹行 ID。
- 如果您就地克隆或编辑共享的 `SkillAvailID`、`SkillEffectiveID` 或 `SkillAilmentType` 行，您可能会默默地更改许多不相关的技能，因为 OT0 大量重用有效负载行。

## 本指南使用的精确 OT0 SDK 行结构引用

本节直接引用 `SDK/Kingship_structs.hpp` 中的 OT0 SDK，避免本指南停留在释义层面。由于这些 row-struct 定义很长，而且读者往往希望并排比较多个表模式，所以把它们集中放在这里。

### 以下 `Skill/` 部分使用的精确 OT0 行结构引用

#### `FMagicTypeBase`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14381`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:9814`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:8760`。
```cpp
struct FResistTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DamageRate;                                      // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillCategoryBase`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16239`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12824`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17289`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17406`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14869`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17455`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14674`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14518`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17264`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12898`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17569`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17517`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16382`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:13602`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17605`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12910`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12926`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17641`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17691`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16345`。
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
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17824`。
```cpp
struct FSpecialSkillIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSpecialSkillGrowthListBase`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:13722`。
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

## 完整的`Skill/`文件参考

下面的每个表部分现在都嵌入了它所依赖的确切 OT0 SDK 枚举引用。上面的集中部分保留了精确的 OT0 行结构引用，用于模式级交叉检查。

### `MagicType`
元素图例表。 OT0 在技能或异常状态需要元素族进行图像或覆盖行为的任何地方使用它。

- **OT0 行：** `9`。
- **《歧路旅人：大陆的霸者》行数：** `8`。
- **OT0 SDK 行结构：** `FMagicTypeBase`。
- **相关 OT0 SDK 枚举：** `EMAGIC_TYPE`。
- `m_id` 与 `SkillID.m_MagicIconType` 和 `SkillAvailID.m_OverrideMagic` 中出现的元素密钥系列相同。

#### 此表使用的确切 OT0 SDK 枚举引用
##### `EMAGIC_TYPE`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:4295`。
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
- `MagicType.m_id` 不是任意行号。 OT0 对该表进行键控，以便导出的 JSON 行 ID `0..8` 与上面的引擎枚举存储的原始值相同。
- 这就是为什么 `SkillID.m_MagicIconType`、`SkillAvailID.m_OverrideMagic` 和其他元素族字段既可以作为直接 SDK 枚举值读取，也可以作为 `MagicType` 行 ID 读取，而无需任何转换层。

#### 逐个字段
- `m_id`：**已确认。** 直接元素族密钥。 OT0 行 `0..8` 与 `EMAGIC_TYPE` 对齐（`NONE`、`FIRE`、`ICE`、`THUNDER`、`WIND`、`LIGHT`、`DARK`、`NAUGHT`、`POISON`）。
- `m_TextID`：**已确认。** `GameTextUI` 中面向玩家的元素名称。示例：OT0 `1 -> Fire`、`7 -> Non-Elemental`、`8 -> Poison`。
- `m_WeakIconTexID`：**已确认。** `Texture/TextureID.m_id` 用于当该元素显示为弱点或阻力系列时使用的弱点面板图标。
- `m_SkillIconTexID`：**已确认。** `Texture/TextureID.m_id` 用于该元素系列的命令/技能叠加图标。

#### 大陆的霸者专用 额外字段
- `m_AdditionWeakIconTexID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 大陆的霸者 的附加弱点系统使用的附加弱点图标纹理。 OT0没有对应的字段。

### `WeaponType`
武器家族图例表。这是 OT0 武器标签、武器图标、相应的装备类型行和战斗动画系列的规范位置。

- **OT0 行：** `10`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** `FWeaponTypeBase`。
- **相关 OT0 SDK 枚举：** `EWEAPON_TYPE`。
- `WeaponType` 不仅仅是装饰性的。 OT0技能行、物品行、动画行全部汇聚于此。

#### 此表使用的确切 OT0 SDK 枚举引用
##### `EWEAPON_TYPE`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:7327`。
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
- `WeaponType.m_id` 同样与上面的原始 SDK 枚举相关。导出的行是引擎武器系列常量的数据表镜像。
- 这就是为什么 `SkillID.m_WeaponIconType`、`SkillAvailID.m_OverrideWeapon`、弱点网格和武器动画路由都可以共享相同的数字族，而无需第二个重新映射表。

#### 逐个字段
- `m_id`：**已确认。** 直接武器系列密钥。 OT0 行与 `EWEAPON_TYPE` 对齐（`NONE`、`SWORD`、`LANCE`、`DAGGER`、`AXE`、`BOW`、`ROD`、`PAPERS`、`FAN`、`NAUGHT`）。
- `m_TextID`：**已确认。** `GameTextUI` 中面向玩家的武器系列名称。示例：`1 -> Sword`、`2 -> Polearm`、`7 -> Tome`、`8 -> Fan`。
- `m_WeakIconTexID`：**已确认。** `Texture/TextureID.m_id` 为该武器系列的弱点面板图标。
- `m_SkillIconTexID`：**已确认。** `Texture/TextureID.m_id` 用于技能 UI 中使用的武器叠加图标。
- `m_EquipType`：**已确认。** `Item/ItemType.m_id` 用于匹配的可装备武器系列物品类型。这就是为什么 OT0 行使用 `1`、`7`、`3`、`2`、`5`、`4`、`6`、`8` 等项目类型 ID，而不是武器枚举的第二个副本的原因。
- `m_AnimationSet`：**已确认。** `Character/WeaponAnimationSet.m_id`。这是当技能渲染为该武器类型时战斗动作使用的动画系列。

### `ResistType`
OT0 弱点/阻力率图例。这是弱点网格样式表（例如 `EnemyWeakID`）使用的百分比类别表。

- **OT0 行：** `10`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** `FResistTypeBase`。
- 此表是必须更正 `SkillAvailID.m_ResistID` 的原因。 `EnemyWeakID` 存储每个插槽的 `ResistType` ID； `SkillResistList` 存储直接百分比增量。

#### 逐个字段
- `m_id`：**已确认。** `Enemy/EnemyWeakID.m_ResistWeapon` 和 `m_ResistMagic` 内部使用的弱点级密钥。
- `m_DamageRate`：**已确认。** 该弱点类别的实际伤害率百分比。 OT0 行解码为 `100` 正常、`130` 弱、`50` 抵抗、`0` 空、`-100` 吸收，以及中间状态 `90`、`85`、`80`、`75` 和 `1`。

### `SkillCategory`
类别图例表。实际类别号位于 `SkillID.m_Category` 上；该表添加了一个运行时可以查询的类别级布尔值。

- **OT0 行：** `9`。
- **《歧路旅人：大陆的霸者》行数：** `9`。
- **OT0 SDK 行结构：** `FSkillCategoryBase`。
- **相关 OT0 SDK 枚举：** `ESKILL_CATEGORY`。

#### 此表使用的确切 OT0 SDK 枚举引用
##### `ESKILL_CATEGORY`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6344`。
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
- `SkillID.m_Category` 存储来自上面 SDK 枚举的原始 `ESKILL_CATEGORY` 编号。 `SkillCategory.json` 的存在是因为 OT0 希望在该枚举之上添加一个额外的每类别数据标志 (`m_BuffDebuffLimit`)。
- 也就是说，`SkillCategory`不是一个不相关的外表；它是上面引用的确切引擎类别常量的数据支持图例。

#### 逐个字段
- `m_id`：**已确认。** 直接类别键。 OT0 行与 `ESKILL_CATEGORY` 对齐（`COMMAND`、`SUPPORT`、`ENEMYACTION`、`ITEM` 等）。
- `m_BuffDebuffLimit`：**观察到。** buff/debuff-cap 行为的类别级布尔值。在 OT0 中，类别 ID `1`、`3` 和 `4` 为 true，其他位置为 false，因此不要假设所有类别都参与相同的限制规则。

### `SkillCalcType`
纯枚举图例表。 OT0 仅保留 `Skill/SkillCalcType.json` 和 `m_id`，这完全反映了 `SkillAvailID.m_CalcType` 使用的计算模式枚举。

- **OT0 行：** `10`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** 在此存储库中提供的 OT0 SDK 转储中未找到匹配的专用行结构。
- **相关 OT0 SDK 枚举：** `ESKILL_CALC_TYPE`。
- 在此处的 OT0 SDK 转储中没有找到专用的 `FSkillCalcTypeBase` 行结构，但 OT0 JSON 仍然是一个干净的图例表。

#### 此表使用的确切 OT0 SDK 枚举引用
##### `ESKILL_CALC_TYPE`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6327`。
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
- `SkillAvailID.m_CalcType` 直接存储这些原始数字。因此，`SkillCalcType.json` 最好理解为 SDK 枚举的导出图例副本，而不是独立的游戏表。
- 这也解释了为什么该表只有 `m_id`：语义名称存在于 SDK 枚举中，而 JSON 使数字域在数据导出中可见。

#### 逐个字段
- `m_id`：**已确认。** 计算模式密钥匹配 `ESKILL_CALC_TYPE`（`NONE`、`WEAPON`、`MAGIC`、`ADD_RATED`、`ADD_FIXED`、`ADD_NOW_RATED`、`LAST_1`、`FEAR_LAST_1`、`BP_SPECIAL_REGEN_RATE`、 ZXQ代码11QXZ）。

### `SkillID`
根技能行。如果您在克隆技能时仅更改一个表，则这是您将首先检查的表，但它几乎从来不是您必须编辑的唯一表。

- **OT0 行：** `7330`。
- **《歧路旅人：大陆的霸者》行数：** `23085`。
- **OT0 SDK 行结构：** `FSkillIDBase`。
- **相关 OT0 SDK 枚举：** `ESKILL_CATEGORY`、`ESKILL_RECOMEND_CATEGORY`。
- OT0 的使用严重偏向敌方行：仅类别 `ENEMYACTION` 就占了大多数行。制作玩家内容时，从面向玩家的正确系列中克隆一行，而不是选择任意 `SkillID` 行。

#### 该行直接使用的精确 OT0 SDK 枚举引用
##### `ESKILL_RECOMEND_CATEGORY`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6655`。
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
- `SkillID.m_Category` 应与前面的 `SkillCategory` 部分中嵌入的确切 `ESKILL_CATEGORY` 引用一起阅读，因为技能行存储该原始枚举值，而 `SkillCategory.json` 是其伴随图例表。
- `SkillID.m_RecommendCategory` **没有** 有并行的 `Skill/` 图例文件。因此，上面的 SDK 枚举是 `ATTACK`、`SUPPORT` 和 `HEAL` 等值的权威命名源。

#### 逐个字段
- `m_id`：技能行的主键。
- `m_Name`：**已确认。** 技能名称文本 ID。大多数 OT0 行通过 `GameText/Localize/*/SystemText/GameTextSkill.json` 解析，但较小的子集通过 `GameTextUI` 解析。验证可见名称时使用本地化文本库。
- `m_Detail`：**已确认。** 技能详细信息/描述文本 ID。 OT0 通过 `GameTextSkill` 压倒性地解决了这个问题，而不是 `GameTextUI`。
- `m_Job`：**已确认。** 设置时为 `Character/JobID.m_id`。 OT0 此处仅使用八个基本作业 ID，并且仅在 `44` 行上使用，因此这**不是**通用所有者作业字段。
- `m_BoostSkills`：**已确认。** boost 系列 `[boost0, boost1, boost2, boost3]` 的四个 `SkillID` 行的固定长度数组。在 OT0 中，通常只有可提升家族的领先行才会填充它。
- `m_Category`：**已确认。** `ESKILL_CATEGORY` 的技能类别。这是您应该检查的第一个字段，以区分玩家命令、支持技能、敌人行动、敌人被动技能和物品技能。
- `m_RequireRatio`：**已确认在 OT0 中未使用。** 存在于 SDK 结构中，但在 OT0 技能行中始终为零/假。
- `m_RequireValue`：**观察到。**成本/价值门。在普通玩家命令中，这与显示的 SP 成本一致。对于非命令技能，其含义取决于类别和调用者。
- `m_CountLimit`：**确认在 OT0 中未使用。**存在但始终为零。
- `m_UnableRecoverCount`：**已确认在 OT0 中未使用。** 存在但始终为零/假。
- `m_RequireBoost`：**已确认在 OT0 中未使用。** 存在但始终为零。
- `m_WeaponIconType`：**已确认。** 直接 `WeaponType.m_id` 即可获得随技能显示的武器图标叠加层。
- `m_MagicIconType`：**已确认。** 直接 `MagicType.m_id` 用于技能显示的元素图标叠加。
- `m_IsRandomReplace`：**观察到。**随机变体替换标志。 OT0 将其用于少数随机替换系列，例如 `Prism Mist` 风格的技能。
- `m_IsWeaponSelect`：**观察到。** 武器形态选择标志。当一个可见命令可以根据武器选择或武器上下文路由解析为不同的具体技能行时使用。
- `m_IsReplaceNowSelectedWeapon`：**观察到。** 替换应使用当前选择的武器形态而不是固定的家族成员。
- `m_IsReplaceMainWeapon`：**观察到。** 与上一个字段类似，但偏向于主要武器组。 OT0 中很少见。
- `m_WeaponReplaceSkill`：**观察到。** 八个替换 `SkillID` 行的固定长度池。尽管有这个名字，OT0 将其用于真正的武器形式替换和一些随机变体系列。
- `m_Avails`：**已确认。** 最多 20 个 `SkillAvailID` 阶段的固定长度数组。这是该技能的游戏顺序。简单地跳过零槽。
- `m_BeginEffective`：**已确认。**可选的预制 `SkillEffectiveID` 阶段。
- `m_Effectives`：**已确认。** 最多 20 个 `SkillEffectiveID` 级的固定长度数组。这是技能的演示顺序。
- `m_EndEffective`：**确认在 OT0 中未使用。** 结构中存在后转换 `SkillEffectiveID` 挂钩，但从未在 OT0 行中使用。
- `m_AttachEffect`：**已确认。** `Effect/AttachEffect.m_id`。 OT0 仅在少数行上使用此功能。
- `m_SkillIconID`：**已确认。**直接`Texture/TextureID.m_id`。这是 OT0 中面向玩家的主要命令图标字段，这就是您**不**将其重新指向 `SkillIconList` 行的原因。
- `m_SkillUniqueIconID`：**已确认。** `Skill/SkillUniqueIconID.m_id`。存在于架构中，但不被 OT0 `SkillID` 行使用。
- `m_CommandPhaseSkillIconID`：**已确认在 OT0 中未使用。** 为命令阶段系统保留备用图标。
- `m_OverrideCommandName`：**已确认在 OT0 中未使用。** 为命令命名保留备用文本 ID。
- `m_CommandCategory`：**观察到。**罕见的 UI/类别助手。只有 5 个 OT0 行使用它，所有行都具有相同的值 `62274`。在依赖它之前克隆已经使用它的行。
- `m_BlockedSkill`：**确认在OT0中未使用。**保留互斥/阻塞技能挂钩。
- `m_ReplaceCondition`：**已确认在 OT0 中未使用。** 保留单一替换条件挂钩。
- `m_ReplaceConditionArray`：**已确认在 OT0 中未使用。** 保留的替换条件数组。
- `m_ReplaceSkill`：**已确认在 OT0 中未使用。** 保留替换技能挂钩。
- `m_ReplaceSkillArray`：**已确认在 OT0 中未使用。** 保留的替换技能数组。
- `m_DisableReplaceTag`：**观察到。** 抑制某些替换标记逻辑或替换相关的 UI 标记。 OT0 在任何地方都让它为假。
- `m_SpecialSkillInitialGaugeValue`：**已确认在 OT0 中未使用。** 用于特殊技能类型系统的计量播种字段。
- `m_SpecialSkillRegenerateValue`：**已确认在 OT0 中未使用。** 用于特殊技能类型系统的仪表再生字段。
- `m_RecommendCategory`：**已确认。**来自`ESKILL_RECOMEND_CATEGORY`（`ATTACK`、`SUPPORT`、`HEAL`）的推荐桶。 OT0 仅在一小部分行上使用此功能。
- `m_DuplicateAilment`：**已确认在 OT0 中未使用。** 在技能行级别保留异常状态重复标志。
- `m_AfterAction`：**观察到。**罕见的后续操作代码。 OT0 仅在五行上使用它；这些行还具有与强制行动后行为一致的暂存有用/有效数据。
- `m_InitialChargeTrun`：**已确认在 OT0 中未使用。** 资产保留的拼写错误。为其他系统保留的充电转参数。
- `m_ReChargeTrun`：**确认在OT0中未使用。**为其他系统保留的充值转参数。
- `m_MaxBoostLv`：**已确认。**最大升压级别。 OT0 在每个 `SkillID` 行上将其设置为 `3`。
- `m_FieldUseable`：**已确认。** 将技能标记为可在现场环境中使用。 OT0只在二十行上设置这个，比如野外复活技能。
- `m_VoiceID`：**已确认。** 直接 `Sound/PartVoiceID.m_id` 进行技能语音标注。

#### 具体 OT0 示例
- `m_FieldUseable`：`SkillID#607665 First Aid`、`#900556 Healing Light`、`#902332 Vivify` 和 `#902114 Revive` 均设置此标志。在原版 OT0 中，这绝大多数是一个治愈/复兴家庭的信号，而不是通用的“无处不在”标记。
- `m_AfterAction`：`SkillID#900012 Retreating Strike`、`#900042 Lunar Arc`、`#900100 Expert First Aid` 和 `#900563/#901483 Song of Battle` 均设置为 `m_AfterAction = 1`；他们的可见描述也明确以“然后移至后排”结尾。这是强有力的证据，表明该领域与强制的行动后划船运动有关。
- `m_RecommendCategory`：`SkillID#902332 Vivify`、`#901894 Arcane Healing`、`#902108 Heal Wounds` 和 `#902114 Revive` 等恢复行使用 `m_RecommendCategory = 3`，与 `ESKILL_RECOMEND_CATEGORY::HEAL` 完全匹配。

#### 大陆的霸者专用 额外字段
- `m_DispBoostLv`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 用于显式显示升压级别的 UI 标志。
- `m_IsBasicAbility`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 将该行标记为基本能力，而不是学习/特殊能力。
- `m_NoChangeBoostType`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**防止替换或转换逻辑更改技能的提升系列/类型。
- `m_Boost246`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 《歧路旅人：大陆的霸者》专有的 boost-pattern 标志，用于不使用普通 OT0 风格 `0/1/2/3` 系列演示的技能。
- `m_ForcedSkillFilter`：**从 《歧路旅人：大陆的霸者》侧的数据库结构和 ID 重叠确认。** `SkillFilterType.m_id` 行数组，强制包含特定过滤器桶中的技能。
- `m_ExclusionSkillFilter`：**从 《歧路旅人：大陆的霸者》侧的数据库结构和 ID 重叠中确认。** `SkillFilterType.m_id` 行数组，强制从特定过滤器桶中排除技能。
- `m_ForcedCategory`：**从 《歧路旅人：大陆的霸者》侧的数据库结构和 ID 重叠确认。** `SkillFilterCategory.m_id` 强制使用 《歧路旅人：大陆的霸者》侧过滤器 UI 的技能。
- `m_NotUsedAutoBattle`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**自动战斗排除标志。
- `m_CmdPhaseTurnResetOnContinue`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**当技能继续进入另一个阶段或回合时，命令阶段状态重置。

#### OT0 使用说明
- `Row-count bias`：OT0 具有 `7330` `SkillID` 行，但只有 `1614` 属于类别 `COMMAND`。敌对内容占据了文件的主导地位。
- `Array sizes`：OT0 使用固定大小：`m_BoostSkills` 长度 `4`、`m_Avails` 长度 `20`、`m_Effectives` 长度 `20`、`m_WeaponReplaceSkill` 长度 `8`。
- `True rarity`：OT0 仅在 `5` 行上设置 `m_IsRandomReplace`，在 `23` 上设置 `m_IsWeaponSelect`，在 `4` 上设置 `m_IsReplaceMainWeapon`，在 `5` 上设置 `m_AfterAction`，在 `5` 上设置 `m_FieldUseable` ZXQ代码24QXZ。

### `SkillAvailID`
游戏有效负载行。如果 `SkillID` 告诉你*这是什么技能系列*，`SkillAvailID` 告诉你*舞台在战斗或战场逻辑中实际做什么*。

- **OT0 行：** `7369`。
- **《歧路旅人：大陆的霸者》行数：** `28023`。
- **OT0 SDK 行结构：** `FSkillAvailIDBase`。
- **相关 OT0 SDK 枚举：** `Kingship::ESKILL_INVOKE`、`ESKILL_TARGET_TYPE`、`ESKILL_MODIFY_TYPE`、`ESKILL_CALC_TYPE`、`ESKILL_AILMENT_CALC_TYPE`。
- 对于 OT0 逆向工程，`m_ModifyType` 是整行中最重要的字段。它确定应如何读取大多数数字有效负载数组。

#### 直接驱动该行的精确 OT0 SDK 枚举引号
##### `Kingship::ESKILL_INVOKE`（上下文枚举）
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6360`。
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
- SDK 转储公开两个具有相同打印名称的不同 `Kingship::ESKILL_INVOKE` 枚举块。上面的上下文枚举描述了更广泛的被动/战斗/现场调用域。

##### `Kingship::ESKILL_INVOKE`（时序枚举）
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6436`。
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
- `SkillAvailID.m_InvokeTiming` 使用上面的计时枚举，而不是早期的上下文枚举。这就是实时 OT0 行包含 `COMMAND`、`ACTION_DAMAGE`、`FIELD_ACTION` 和 `ALWAYS` 等值的原因。

##### `ESKILL_TARGET_TYPE`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6683`。
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
- `SkillAvailID.m_TargetType` 存储此枚举中的原始数字选择器。这就是为什么该字段可以在同一整数列中对普通玩家目标（`ENEMY_1`、`FRIENDLY_ALL_DEAD`）和AI/帮助选择器（`AI_ENEMY_MIN_HP`、`FORWARDS_OR_BACKUPS_ALIVE`）进行编码。

##### `ESKILL_MODIFY_TYPE`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6476`。
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
- `SkillAvailID.m_ModifyType` 存储来自该枚举的原始数字操作码，这就是这个单个字段主导 `m_Values`、`m_Turns`、`m_Counts`、`m_ModifyStatus`、强化 ID、弱点更改 ID、商店/农场辅助行和许多其他有效负载的解释的原因。
- `SkillAvailID.m_CalcType` 应与上面嵌入在 `SkillCalcType` 中的确切 `ESKILL_CALC_TYPE` 引用一起阅读。
- `SkillAvailID.m_CalcTypeAilment` 应与下面嵌入在 `SkillAilmentType` 中的确切 `ESKILL_AILMENT_CALC_TYPE` 引用一起阅读； OT0 值的使用与异常状态端枚举系列相匹配。

#### 逐个字段
- `m_id`：可用行的主键。
- `m_InvokeTiming`：**已确认。**来自 OT0 SDK 转储中更丰富的 `Kingship::ESKILL_INVOKE` 块的时序枚举。 OT0 使用 `COMMAND`、`BATTLING`、`ACTION_DAMAGE`、`FIELD_ACTION` 和 `ALWAYS` 等值。
- `m_InvokeCondition`：**已确认。** `Skill/SkillConditionList.m_id`。仅当此条件包通过时，该阶段才会解决。
- `m_TargetType`：**已确认。**来自 `ESKILL_TARGET_TYPE` 的目标选择器。您可以在此处决定该阶段是否要求一名敌人、所有敌人、自己、一名死去的盟友、所有人、前排、后排、AI选择的最低生命值目标等等。
- `m_ModifyType`：**已确认。**主要操作代码来自`ESKILL_MODIFY_TYPE`。 `CHARACTER` 是通用的 stat/damage/heal/buff/debuff 路径，而其他值则执行复活、弱点改变、强化、偷窃、村庄经济操作等等。
- `m_ModifyStatus`：**已确认。** `Character/CharaStatusID.m_id` 当修改操作码需要具体的统计或资源选择器时。 OT0 在这里只使用了 17 个唯一的状态 ID，并且所有这些 ID 都完全匹配 `CharaStatusID`。
- `m_AvailTag`：**观察到。** 七槽内部分类标签阵列。 OT0 使用许多重复元组，但 OT0 SDK 转储并未为其公开干净的枚举。将其视为内部路由/标记字段，除非您要克隆显然依赖于它的现有系列。
- `m_IsFoodAvail`：**观察到。** 食品/村庄侧可用行为的罕见 OT0 标志。
- `m_HitTypes`：**已确认。** 最多包含四行 `SkillHitRateList.m_id` 的数组。这些控制多重命中计数分布和相关的随机命中逻辑。
- `m_RandomChoiceCount`：**观察到。** 从多个结果中选取的操作码的随机选择/分支的计数。 OT0 中很少见。
- `m_CalcType`：**已确认。** `ESKILL_CALC_TYPE` 的计算模式。在 OT0 中，常见值为 `NONE`、`WEAPON`、`MAGIC`、`ADD_FIXED` 和 `ADD_RATED`。
- `m_Values`：**已确认。** 主要数字有效负载数组。解释取决于 `m_ModifyType` 加上 `m_CalcType`：伤害强度、治疗量、状态增量、奖励量或其他特定于操作码的值。
- `m_FluctuationValues`：**观察到。**次要方差有效负载。 OT0 仅在三行上​​使用非零值，因此大多数技能都会忽略它。
- `m_SkillRatios`：**观察到但非常强烈支持。**每升压比数组。常见的 OT0 默认值是 `(100,100,100,100)`，而许多可增强攻击系列使用渐进集，例如 `(100,180,260,340)` 或 `(100,200,300,400)`。
- `m_Turns`：**观察到。** 每升压持续时间数组。 OT0 将其用于定时 buff、debuff、计数器、拦截和其他状态效果。
- `m_OverwriteCount`：**观察到。** 重新应用舞台时基于计数的效果使用的显式覆盖计数。
- `m_Counts`：**观察到。** 每次升压计数或每个 Boost 阶段的计数数组。由基于计数的机制使用，例如有限反应、重复计数或类似的阶段本地计数器。
- `m_OrderPriority`：**观察到。**罕见的战斗命令覆盖。 OT0 仅在一行上使用非零值。
- `m_NextPriority`：**观察到。**罕见的下一步行动优先级调整。 OT0 仅在七行上使用它。
- `m_TargetNextPriority`：**观察到。**目标端下一步操作优先级设置。 OT0多采用哨兵值`-100`；当设置为 `1`、`2`、`30` 或 `31` 等值时，它将更改未来的顺序行为。
- `m_ShieldDamage`：**已确认在 OT0 中未使用。**始终为零。
- `m_IgnoreWeak`：**观察到。**忽略此阶段的弱点交互或弱点要求。 OT0 仅在七十三行上使用它。
- `m_NoBreakable`：**已确认在 OT0 中未使用。**始终为零。
- `m_PhysicalHitRatio`：**观察到。**罕见的物理命中率覆盖。 OT0 仅在两行上使用它。
- `m_CriticalRatio`：**观察到。**此阶段的临界速率调整。 OT0 在几百个进攻行中使用了它。
- `m_Suicide`：**观察到。** 将阶段标记为自我牺牲或自我毁灭。
- `m_LotteryAilment`：**观察到。**随机异常状态选择助手。在 OT0 中很少见，仅当修改路径随机选择异常状态包时才有用。
- `m_AilmentInvokeCondition`：**观察到。** 专门针对该阶段的异常状态部分的额外条件门。 OT0 在一行上使用它。
- `m_AddAilmentPick`：**观察到。**用于从 `m_AddAilment` 中进行选择的稀有选择器标志。 OT0 仅在四行上使用它。
- `m_AddAilment`：**已确认。** 要添加/应用的最多 16 行 `SkillAilmentType.m_id` 行的内联列表。
- `m_CalcTypeAilment`：**观察到。** 异常状态计算模式的并行列表。最可能的枚举系列是 `ESKILL_AILMENT_CALC_TYPE`，OT0 SDK 确实公开了该系列。
- `m_ValueAilment`：**观察到。** 对应于 `m_AddAilment` 的异常状态效力/覆盖值的并行列表。
- `m_NotExtensionTarget`：**观察到。**从普通持续时间延长行为中排除目标状态。 OT0 在 `557` 行上使用它，因此它不仅仅是一个一次性调试标志。
- `m_WeakLockID`：**已确认。** `Enemy/EnemyWeakLockID.m_id`。当阶段锁定特定的弱点插槽时使用它。
- `m_DelayedSkill`：**观察到。** 后续 `SkillID.m_id` 稍后解决而不是立即解决。
- `m_DelaySkillPriority`：**观察到。** 延迟技能阶段的优先级桶/计时助手。
- `m_SubAilmentPick`：**观察到。** `m_SubAilment` 的稀有选择器标志。
- `m_SubAilment`：**已确认。** 最多 16 行 `SkillAilmentType.m_id` 的辅助列表。 OT0 将其用于分层到主舞台上的条件或支持异常状态包。
- `m_ResistID`：**已确认。** `Enemy/EnemyWeakID.m_id`，**不是** `SkillResistList`。该字段交换或注入完整的弱点表行，因为 `EnemyWeakID` 存储每个武器/每个元素的 `ResistType` 类。这就是为什么值集匹配 `EnemyWeakID` 而不是 `SkillResistList`。
- `m_ResistAilment`：**观察到。**基本异常状态家族的内联异常状态抵抗率数组。在 OT0 中，这可以与 `m_ResistAilmentID` 共存，因此不要假设其中一个会禁用另一个。
- `m_ResistAilmentID`：**已确认。** `Skill/SkillResistAilmentID.m_id`。这是异常状态抵抗力的打包形式，存储明确的异常状态 ID 和匹配率。
- `m_Reinforcement`：**已确认。** `Enemy/EnemyReinforcements.m_id` 用于召唤援军的阶段。 OT0 有一个异常值 `140`，因此在克隆稀有 Boss 行为之前始终验证目标行。
- `m_WeakChangeID`：**已确认。** `Enemy/EnemyWeakChangeID.m_id` 用于分阶段弱点表修改。
- `m_EnhancedSkillID`：**已确认在 OT0 中未使用。**始终为零。
- `m_SummonGuestID`：**已确认。** `Character/CharaGuest.m_id` 用于宾客召唤行为。
- `m_OverrideWeapon`：**已确认。** 直接覆盖 `WeaponType.m_id`。这就是技能阶段所说的“使用该武器系列进行命中或图标，即使源命令是其他命令。”
- `m_OverrideMagic`：**已确认。** 直接覆盖该阶段的 `MagicType.m_id`。
- `m_AppendType`：**确认在 OT0 中未使用。**保留的附加行为选择器。
- `m_SkillAvailMagnificationCondition`：**已确认。** `SkillAvailMagnificationConditionList.m_id`。这是条件放大行为的主要查找字段。
- `m_SkillAvailMagnificationConditionArray`：**已确认。**额外的 `SkillAvailMagnificationConditionList.m_id` 行。 OT0 此处使用长度为 7 的数组。
- `m_SkillAvailMagnification`：**已确认。** OT0 中的直接放大值，而不是 `SkillAvailMagnificationList` 行 ID。典型的 OT0 值为 `110`、`130`、`150`、`180`、`200`。
- `m_SkillAvailMagnificationArray`：**已确认。** OT0 中的直接附加放大倍数值，而不是行 ID。 OT0 使用 `[200,190,180,170,160,150,140]` 等数组。
- `m_InvocationProbability`：**观察到。**额外的概率门。 OT0 仅在一行上使用它。
- `m_ModeChangeID`：**已确认。** `Enemy/EnemyModeID.m_id`。这是一个 Boss/AI 相变挂钩，而不是通用的玩家形态切换。
- `m_CountRefAilment`：**已确认在 OT0 中未使用。**始终为零。
- `m_CountRefValue`：**已确认在 OT0 中未使用。**始终为零。
- `m_CountRefValueLimit`：**已确认在 OT0 中未使用。**始终为零。

#### 具体 OT0 示例
- `m_ResistID`：`SkillAvailID#40085`和`#73697`均使用`6527`，`SkillAvailID#73698`使用`6528`。这些值以 `EnemyWeakID` 行的形式存在，这就是为什么该字段的行为类似于完整的弱点表替换路径，而不是简单的百分比修饰符。
- `m_OverrideWeapon` / `m_OverrideMagic`：`SkillAvailID#76127` 到 `#76134` 是 `Repeated Bash` 使用的武器系列阶段，逐步到 `m_OverrideWeapon = 1..8`。相比之下，`Call for Help` 中的 `SkillAvailID#74047` 和 `#74051` 使用 `m_OverrideMagic = 5`，这表明同一个 stage 结构可以强制渲染一个 magic family。
- `m_Reinforcement`：`SkillAvailID#20049`对可见文本为`<skl_user_name> calls for his lackeys!`的敌方技能使用`m_Reinforcement = 43`。其配对的 `SkillEffectiveID#20049` 还带有召唤成功/召唤失败战斗日志字符串，这是游戏玩法和演示行协同工作的一个很好的例子。
- `m_WeakChangeID` / `m_WeakLockID`：`SkillAvailID#20079`和`#20089`属于可见家族`Weaknesses have been swapped!`，而`SkillAvailID#20048`属于`<skl_user_name> is protected by his lackeys.`并带有`m_WeakLockID = 11`。这正是您所期望的弱点表突变与槽锁定的模式。
- `m_ModeChangeID`：`Indestructible Flame` 中的 `SkillAvailID#77350`、`Dark Hex` 中的 `#74765` 和 `Vorpal Strike` 中的 `#75499` 都设置了非零模式更改 ID，这就是为什么该字段最好被解读为 boss/AI 阶段钩子而不是通用玩家姿态切换。
- `m_SkillAvailMagnification*`：`Surprise Attack` 中的 `SkillAvailID#74940` 使用条件包 `69` 加上阵列包 `[68,67,59,61]` 和直接放大 `110/130/150/170/200`。 `Ulti-meow` 的 `SkillAvailID#76297` 使用 `102..109` 套件并直接放大至 `300`。这些是原始值，而不是 `SkillAvailMagnificationList` 行 ID。

#### 大陆的霸者专用 额外字段
- `m_HitRatio`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 超出 OT0 更简单的命中率表的额外命中率标量。
- `m_LotteryAilmentNum`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 从彩票路径中抽取的异常状态数量。
- `m_CompareValueAilment`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 异常状态侧逻辑使用的比较值。
- `m_DelayedSkillActByOwner`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 作为原始所有者而不是最终状态持有者运行延迟技能。
- `m_SelfAfterSkillCheck`：**从名称和《歧路旅人：大陆的霸者》侧使用情况观察。**技能执行后额外的自检。
- `m_NoSelfAfterSkillCheckEffective`：**从名称和《歧路旅人：大陆的霸者》侧使用情况观察。**自检失败时抑制检后呈现路径。
- `m_SubAilmentTag`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 用于次要异常状态分组的标签 ID。
- `m_SubAilmentTagFlag`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**启用/标记前一个字段的行为切换。
- `m_CalcTypeResist`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**用于阻力操作的单独计算模式选择器。
- `m_AddResistWeapon`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 附加武器抵抗有效载荷阵列。
- `m_AddResistMagic`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 附加元素耐性有效负载数组。
- `m_AilmentMagnificationConditions`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 异常状态侧放大条件 ID。
- `m_AilmentMagnifications`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 异常状态侧直接放大值。
- `m_CountRefAilmentTag`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 基于标签的计数器参考通道。
- `m_CountRefType`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**选择引用的计数系列。
- `m_CountRefAttribute`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 引用计数器的属性选择器。
- `m_IgnorePreviousSuccessful`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 解决重复检查时忽略先前的成功状态。
- `m_IgnoreDodge`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**绕过闪避解析。
- `m_IgnoreZeroDamage`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**即使伤害为零，也继续效果处理。
- `m_AddDamageLimitValue`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**额外伤害上限增加。
- `m_AddDamageLimitCondition`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 伤害限制增加的条件门。
- `m_UseAilmentMagnificationForAddDamageLimit`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 重复使用异常状态放大值以增加伤害上限。
- `m_NonCondition`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**额外的否定/无条件开关。
- `m_IgnoreResistAilment`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**绕过异常状态抵抗力。
- `m_IgnoreDefenceBuff`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**绕过防御增益。
- `m_IgnoreReduceDamage`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**绕过一般伤害减少效果。
- `m_HitWeaponEnable`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**启用显式命中武器输入。
- `m_HitWeaponType`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 启用命中武器输入时使用的武器系列。
- `m_NoConsumeAdditionWeak`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**命中后保留加法弱状态。
- `m_IsBuffDamageSupStrictlyCheck`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**对 buff 伤害支持交互进行更严格的验证。

#### OT0 使用说明
- `Array sizes`：OT0 使用固定大小：`m_HitTypes` 长度 `4`、`m_Values` 长度 `4`、`m_SkillRatios` 长度 `4`、`m_Turns` 长度 `4`、`m_Counts` 长度 `4`、`m_AddAilment`长度`16`、`m_SubAilment` 长度`16`、`m_SkillAvailMagnificationConditionArray` 长度`7`、`m_SkillAvailMagnificationArray` 长度`7`。
- `Common opcodes`：OT0 绝大多数使用 `m_ModifyType = 1 (CHARACTER)`。其次最常见的家庭是弱点改变、复活、偷窃、结果奖励和村庄经济操作码，例如 `ITEM_SUPPLY`。
- `Dormant counters`：`m_ShieldDamage`、`m_NoBreakable`、`m_EnhancedSkillID`、`m_AppendType` 和 `m_CountRef*` 字段在 OT0 中实际上处于休眠状态。

### `SkillConditionList`
`SkillAvailID.m_InvokeCondition` 和其他一些技能系统的可重用布尔条件包。不要将此文件与 `SkillAvailMagnificationConditionList` 混淆；它们是不同的操作码系列。

- **OT0 行：** `275`。
- **《歧路旅人：大陆的霸者》行数：** `933`。
- **OT0 SDK 行结构：** `FSkillConditionListBase`。
- 此处使用的 OT0 SDK 转储**不**公开此表中 `m_Conditions` 的干净命名枚举。观察到的操作码集比放大条件枚举大得多，并且包括 `10723`、`10790` 和 `10816` 等值。

#### 逐个字段
- `m_id`：条件包的主键。
- `m_Conditions`：**已确认的形状，未解析的名称。** 四个条件操作码逐个插槽解释。这是 `SkillAvailID.m_InvokeCondition` 使用的主要条件族。
- `m_Params`：**已确认。** 四个通用整数参数槽。每个槽的含义由匹配的 `m_Conditions` 条目决定。
- `m_AilmentTypes`：**已确认。** 四个 `SkillAilmentType.m_id` 参数槽用于异常状态感知条件操作码。 OT0 在此使用 `36` 唯一的异常状态 ID。
- `m_StatusTypes`：**已确认的形状。** 四个 `CharaStatusID` 参数槽，用于状态感知条件操作码。 OT0 未使用它们，但 大陆的霸者 更积极地使用相同的字段系列。
- `m_WeaponTypes`：**已确认的形状。** 四个 `WeaponType.m_id` 参数槽。 OT0 中未使用。
- `m_MagicTypes`：**已确认的形状。** 四个 `MagicType.m_id` 参数槽。 OT0 中未使用。
- `m_Equipment`：**已确认的形状。** 四个设备参数槽。 OT0 中未使用。
- `m_ORFlag`：**已确认。** 用 OR（而不是 AND）组合占用的条件槽。 OT0 行 `335` 和 `336` 通过在不同的异常状态 ID 上重复相同的异常状态检查操作码直接证明了这一点。

#### 大陆的霸者专用 额外字段
- `m_AutoDungeonBuff`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 额外的自动地下城特定的 buff 参数通道。
- `m_AutoDungeonBuffType`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 自动地下城增益通道的类型选择器。

### `SkillAvailMagnificationConditionList`
可重复使用的条件包专门用于条件放大。与 `SkillConditionList` 不同，此文件**确实**与命名的 OT0 SDK 枚举对齐。

- **OT0 行：** `89`。
- **《歧路旅人：大陆的霸者》行数：** `217`。
- **OT0 SDK 行结构：** `FSkillAvailMagnificationConditionListBase`。
- **相关 OT0 SDK 枚举：** `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`。

#### 此表使用的确切 OT0 SDK 枚举引用
##### `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6226`。
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
- `m_Conditions` 直接存储来自该枚举的原始 ID。因此，`SkillAvailMagnificationConditionList` 是这些放大操作码的精确条件包表，而不是模糊的条件桶。
- 这也是为什么 `SkillAvailID.m_SkillAvailMagnificationCondition` 和 `m_SkillAvailMagnificationConditionArray` 可以精确地描述为该文件中的行 ID：这些字段指向一个包，其占用的插槽本身由上面的枚举键入。

#### 逐个字段
- `m_id`：放大条件包的主键。
- `m_Conditions`：**已确认。** 来自 `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID` 的四个条件操作码。 OT0 使用 `SLD_GE`、`HAS_AILMENT`、`RACE_KILLER`、`BEFORE_ACTION`、`HP_SELF_LE`、`ENABLED_SUPPORTABILITY_SELF`、`DAY_OR_NIGHT`、`TURN_ORDER_FIXED_LE`、`PROGRESS`、`SP_BURST_GE` 等值和防御阈值。
- `m_IntParams`：**已确认。** 对应于占用的 `m_Conditions` 插槽的四个通用数字阈值。
- `m_AilmentParams`：**观察到。** 异常状态相关参数。许多 OT0 值与 `SkillAilmentType.m_id` 匹配，但少数 OT0 行引用 ID 不存在于当前 OT0 `SkillAilmentType` 导出中，因此不要假设每个值都是实时 OT0 异常状态行。
- `m_RaceParams`：**观察到。** 竞赛过滤器/参数槽。 OT0 几乎不使用它们。
- `m_Equipment`：**确认形状。**设备过滤槽。 OT0 未使用它们。

#### 具体 OT0 示例
- `SkillAvailMagnificationConditionList#7` 将 `m_Conditions = [2,0,0,0]` 与 `m_IntParams = [10,0,0,0]` 一起存储，即一个简单的 `SLD_GE 10` 包。
- `#8` 将 `m_Conditions = [5,0,0,0]` 与 `m_AilmentParams = [1,0,0,0]` 一起存储，即 `HAS_AILMENT` 与异常状态行 `1` 一起存储。
- `#10` 存储 `m_Conditions = [7,0,0,0]`，即没有额外整数有效负载的纯 `BEFORE_ACTION` 门。
- `#13` 将 `m_Conditions = [12,0,0,0]` 与 `m_IntParams = [100,0,0,0]` 一起存储，即 `HP_SELF_LT 100` 样式包。
- `#81` 存储 `m_Conditions = [8,0,0,0]` 和 `m_IntParams = [90,0,0,0]`，即 `HP_SELF_LE 90` 阈值包。

#### 大陆的霸者专用 额外字段
- `m_WeaponTypes`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 用于放大条件的武器系列过滤器插槽。
- `m_MagicTypes`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 用于放大条件的元素系列过滤器插槽。

### `SkillAvailMagnificationList`
有四行的小型仅 OT0 查找表。它是真实的，但它**不是** OT0 `SkillAvailID` 行使用的主要链接路径。

- **OT0 行：** `4`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** 在此存储库中提供的 OT0 SDK 转储中未找到匹配的专用行结构。
- 关键的实用点是OT0 `SkillAvailID.m_SkillAvailMagnification`存储直接值，例如`110`和`200`，而该表的ID只有`0..3`。

#### 逐个字段
- `m_id`：小查找行的主键。
- `m_Conditions`：**观察到。** 这个小图例表中的单槽条件选择器。
- `m_Params`：**观察到。** 查找行的单槽参数负载。

### `SkillEffectiveID`
演示有效负载行。这是摄像机、战斗动作、效果、声音、语音、文本弹出窗口和可见性变化及时上演的地方。

- **OT0 行：** `4381`。
- **《歧路旅人：大陆的霸者》行数：** `11434`。
- **OT0 SDK 行结构：** `FSkillEffectiveIDBase`。
- **相关 OT0 SDK 枚举：** `ESKILLEFFECT_LAYOUT`、`ESKILLEFFECTIVE_CHARA_VISIBILITY`、`ESKILLEFFECTIVE_VISIBILITY_TARGET`。

#### 直接驱动该行的精确 OT0 SDK 枚举引号
##### `ESKILLEFFECT_LAYOUT`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6406`。
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
- `m_EffectLayout` 存储原始 `ESKILLEFFECT_LAYOUT` 值。这就是为什么每个效果槽都可以准确地说出它是否在 `SELF`、`TARGET`、`TARGET_CENTER` 或 `BATTLE_CENTER` 上生成。

##### `ESKILLEFFECTIVE_CHARA_VISIBILITY`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6371`。
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
- `m_CharaVisibility` 存储来自此枚举的原始值以用于可见性操作本身（`VISIBLE`、`INVISIBLE`、`FADE_VISIBLE`、`FADE_INVISIBLE`）。

##### `ESKILLEFFECTIVE_VISIBILITY_TARGET`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6383`。
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
- `m_VisibilityTarget` 存储来自此枚举的原始值，以便*谁*接收可见性操作（`SELF`、`TARGET`、`FRIENDLY_ALL`、`ENEMY_ALL`、`FORWARDS_AND_BACKUPS` 等）。
- OT0 故意将可见性分级分为两个维度：一个枚举描述可见性操作，而另一个描述接收者集。

#### 逐个字段
- `m_id`：演示阶段的主键。
- `m_Repeat`：**已确认。** 阶段重复计数。 OT0 在每一行将其设置为 `1`。
- `m_HoldTime`：**观察到。** 总演示保持时间（以秒为单位）。
- `m_AvailTime`：**观察到。** 游戏阶段相对于演示被认为可用/已解决的时间（以秒为单位）。
- `m_Cameras`：**已确认。** 最多三行 `Battle/BattleCameraParams.m_id` 用于摄像机编排。
- `m_CameraTimes`：**观察到。** 相机阶段的触发时间。
- `m_Animations`：**已确认。** 最多三行 `Battle/BattleActionID.m_id` 用于执行战斗动作。
- `m_PlayTimes`：**观察到。** 战斗动作的触发时间。
- `m_PlayRates`：**观察到。** 战斗动作的播放速率乘数。
- `m_AnimationTarget`：**观察到。**播放动画的每个动作目标选择器。
- `m_AnimStartFrame`：**确认在 OT0 中未使用。** SDK 结构中存在起始帧覆盖字段，但 OT0 行从未使用过。
- `m_AnimEndFrame`：**观察到。** 动画阶段的结束帧或结束部分标记。 OT0 将其填充到每一行。
- `m_AnimNoIdle`：**观察到。** 在动画之后抑制空闲返回或空闲混合。 OT0 中很少见。
- `m_Effects`：**已确认。** 演示阶段最多生成三行 `Effect/EffectList.m_id` 行。
- `m_InvokeTimes`：**观察到。** 效果生成时间。
- `m_EffectLayout`：**已确认。** `ESKILLEFFECT_LAYOUT` 的每个效果生成布局（`SELF`、`TARGET`、`FRIENDLY_CENTER`、`TARGET_CENTER`、`BATTLE_CENTER` 等）。
- `m_OffsetsH`：**观察到。**生成效果的水平偏移。
- `m_OffsetsV`：**观察到。**生成效果的垂直偏移。
- `m_Sounds`：**已确认。** 该阶段最多触发三个 `Sound/SoundList.m_id` 行。
- `m_SoundTimes`：**观察到。** 声音行的触发时间。
- `m_SoundToVoice`：**观察到。** 罕见的路由/链接标志控制声音是否与语音风格的计时/处理相关。
- `m_Voices1`：**已确认。** 指向 `Sound/PartVoiceID.m_id` 的主语音槽。
- `m_Voices2`：**已确认。** 指向 `Sound/PartVoiceID.m_id` 的辅助语音插槽。
- `m_Voices3`：**确认在 OT0 中未使用。** 第三语音时隙存在，但在 OT0 中为零。
- `m_VoiceTimes`：**观察到。** 语音触发时间。
- `m_Texts`：**通过强有力的证据观察。** 最多十个浮动文本 ID。大多数 OT0 值通过 `GameTextSkill` 解析，一些通过 `GameTextUI` 解析，并且目前有一小部分残留位于明显的 OT0 组之外。
- `m_TextTime`：**观察到。** 浮动文本触发时间。
- `m_CharaVisibility`：**已确认。** `ESKILLEFFECTIVE_CHARA_VISIBILITY` 中的每个可见性阶段值。
- `m_VisibilityTarget`：**已确认。** `ESKILLEFFECTIVE_VISIBILITY_TARGET` 中的每个可见性阶段值。
- `m_VisibilityStartTime`：**观察到。**可见性变化的开始时间。

#### 具体 OT0 示例
- 浮动文本不是假设的。 `SkillEffectiveID#20049` 携带字符串 `<skl_user_name> calls for his lackeys!` 和 `However, the lackeys did not appear...`，它是强化型敌人技能的精确呈现伴侣。
- 可见性舞台也是真实的，而不是剩余的领域。 `SkillEffectiveID#65047` 和 `#65052` 均由 `Lotus Blade` 使用，在 `TARGET` 上使用 `INVISIBLE -> VISIBLE` 切换目标可见性。
- `Pilfer` 使用的 `SkillEffectiveID#65954` 和 `#65956` 显示淡入淡出变体，而不是硬切换变体，在 `TARGET` 上使用 `FADE_INVISIBLE` / `FADE_VISIBLE`。
- `SkillEffectiveID#65572` 和 `#65631`，由 `Hunter's Drive` 使用，目标可见性选择器 `11`，与 SDK 枚举中的 `FRIENDLY_ALL_WITHOUT_SELF` 匹配。这是可见性目标枚举确实被逐字消耗的一个很好的证明。
- `Soaring Javelin`和`Take Aim`使用的`SkillEffectiveID#66152`，将可见性阶段应用于目标选择器`20`，即`ENEMY_ALL`，这证明同一系统不限于一对一的隐身效果。

#### 大陆的霸者专用 额外字段
- `m_MagicStonePerformance`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 《歧路旅人：大陆的霸者》专有的魔法石性能资产或模式。
- `m_MagicStonePerformanceStartTime`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 前一个字段的开始时间。
- `m_MagicStoneLevel`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 用于魔石呈现的级别或层选择器。
- `m_CameraShakeCurve`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 相机抖动资产/曲线。
- `m_CameraShakeStartTime`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 相机抖动的开始时间。
- `m_Text2nd`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 第二文本库/第二浮动文本。
- `m_SkillEffectiveLinkageID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构和 ID 重叠确认。** `SkillEffectiveLinkageList.m_id` 用于连锁效应组。
- `m_IsSetVisibleUI`：**从名称和 大陆的霸者 使用情况观察。** 在该阶段启用 UI 可见性控制。
- `m_UIVisiblityFlag`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** UI 可见性模式/标志。
- `m_UIVisibilityTarget`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** UI 可见性目标选择器。
- `m_UIVisibilityStartTime`：**从名称和 大陆的霸者 使用情况观察。** UI 可见性更改的开始时间。

#### OT0 与 大陆的霸者 注释
- `Camera schema shift`：OT0 通过 `m_Cameras` 和 `m_CameraTimes` 保持相机行内联。导出的 `Skill/` 文件夹中的 大陆的霸者 `SkillEffectiveID` 架构会删除这些内联数组，而是扩展辅助 UI/效果链接字段。

### `SkillAilmentType`
可重复使用的异常状态/状态包。该文件涵盖了经典的负面状态、正面增益、反击、拦截、重新加注状态、躲避状态、伤害限制光环、永久状态以及许多其他定时或触发效果。

- **OT0 行：** `490`。
- **《歧路旅人：大陆的霸者》行数：** `968`。
- **OT0 SDK 行结构：** `FSkillAilmentTypeBase`。
- **相关 OT0 SDK 枚举：** `Kingship::ESKILL_INVOKE`、`ESKILL_AILMENT_CALC_TYPE`。
- 这是整个技能系统中最密集、最重要的表格之一。除了指向这里之外，`SkillAvailID` 本身通常只做很少的事情。

#### 直接驱动该行的精确 OT0 SDK 枚举引号
##### `Kingship::ESKILL_INVOKE`（时序枚举）
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6436`。
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
- `SkillAilmentType.m_InvokeTiming` 使用与 `SkillAvailID.m_InvokeTiming` 使用相同的时序枚举系列。因此，引擎会在相同的全局计时词汇表上安排故障滴答声和阶段执行。

##### `EAILMENT_CONDITION_ID`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:531`。
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
- `m_RemoveConditions` 存储来自该异常状态条件枚举的原始值。这就是为什么像睡眠、倒计时或断开链接状态这样的行可以表达精确的删除触发器，例如 `PHYSICAL_DAMAGE`、`BREAK_OR_DEAD_ENFORCER` 或 `CAN_NOT_ACTION`。

##### `ESKILL_AILMENT_CALC_TYPE`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6208`。
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
- `m_AilmentCalc` 存储来自该枚举的原始值，并且 `SkillAvailID.m_CalcTypeAilment` 似乎对其添加的异常状态行使用相同的系列。这就是伤害衍生增益、限制修正和相关异常状态计算共享一个数字词汇的确切原因。
- `m_ResistWeapon` 和 `m_ResistMagic` 应根据上面嵌入的 `WeaponType` 和 `MagicType` 中的确切 `EWEAPON_TYPE` 和 `EMAGIC_TYPE` 引号进行读取，因为这些数组按相同的系列顺序进行键控，即使异常状态行将它们存储为掩码而不是标量 ID。

#### 逐个字段
- `m_id`：**已确认。** 异常状态行的主键。由 `SkillAvailID.m_AddAilment`、`m_SubAilment`、`SkillConditionList.m_AilmentTypes`、`SkillResistAilmentID.m_ResistAilments`、`m_RemoveAilment` 和 `m_ChildAilment` 引用。
- `m_Label`：内部/编辑器标签。这通常是识别该行的用途的最快的人类可读方式。
- `m_Resist`：**观察到有强有力的证据。**正常的抗病逻辑是否适用于这一行。对于中毒、流血、睡眠、麻痹、恐惧和黑暗等经典的负面状态来说也是如此。
- `m_Minus`：**观察到。** 负面/类似减益的异常状态标志。
- `m_Plus`：**观察到。** 阳性/类似 buff 的异常状态标志。
- `m_Removable`：**观察到。**普通的治愈/清除行为是否可以消除异常状态。
- `m_Multiply`：**观察到。** 堆叠/共存行为标志。许多普通的 buff 和 debuff 状态都保持这一点，而一些单例控制状态则不然。
- `m_OverwriteTurn`：**观察到。** 通过覆盖存储的回合来重新应用，而不是仅仅扩展或忽略现有状态。
- `m_ForceOverrideValue`：**观察到。** 通过覆盖存储的异常值以及转弯计数来重新应用。
- `m_InvalidateDebuff`：**由使用模式确认。** 将该行标记为 debuff 类状态，用于使 debuff 无效或抑制的交互。 OT0将其设置为减益行，例如防御下降、攻击下降、武器削弱和元素削弱状态。
- `m_CanInactiveFlag`：**观察到。** 允许非活动/关闭状态表示。 OT0 将其设置在普通的 buff/debuff 行及其永久变体上，这正是您期望状态切换或记住但不活动的行为的地方。
- `m_InvokeTiming`：**已确认。** 来自更丰富的 `Kingship::ESKILL_INVOKE` 枚举的触发时序。 OT0 使用 `BATTLING`、`ACTION_DAMAGE`、`SLIP_DAMAGE`、`FORWARD`、`ALWAYS` 等时序。
- `m_TargetStatus`：**已确认。** `Character/CharaStatusID.m_id` 在修改具体统计/资源时会受到该异常状态的影响。
- `m_TargetStatusArray`：**确认形状。**多状态异常状态的额外状态目标。 OT0 几乎不使用它。
- `m_AilmentCalc`：**已确认。**来自`ESKILL_AILMENT_CALC_TYPE`的异常状态侧计算模式。 OT0 大多保留 `NONE`，但损伤衍生和极限衍生状态确实使用更高的值。
- `m_EfficacyStepRate`：**观察到。** 异常状态功效的步进比例缩放参数。 OT0 中很少见。
- `m_ResistWeapon`：**确认的形状和观察到的含义。**布尔武器系列面具。尽管有这个名字，OT0 在反击和拦截行中大量使用这个来定义国家对哪些武器打击系列做出反应。
- `m_ResistMagic`：**确认的形状和观察到的含义。**布尔元素系列掩码对魔法/元素计数器和拦截使用相同的方式。
- `m_RemoveAilment`：**已确认。** 应用此故障时，最多会删除四行 `SkillAilmentType.m_id`。示例：更强的变体或无效状态可以删除其较弱的前身。
- `m_TargetType`：**观察到。** 小异常状态内部目标选择器。 OT0 仅在三个 `...TARGET_IS_ENEMY_ALL` 计数器/拦截行上使用非零值，因此不要将其视为完整的战斗目标枚举。
- `m_RemoveConditions`：**观察到。**控制异常状态如何被移除或破坏的条件操作码。 Sleep 是一个很好的例子：它的 OT0 行使用非零删除条件，因此损坏/操作可以清除它。
- `m_RemoveParams`：**观察到。** 删除条件槽的参数。
- `m_ChildAilment`：**已确认。** `SkillAilmentType.m_id` 与此相关的一种儿童异常状态。
- `m_TextID`：**已确认。** 可见异常状态名称文本ID，主要在`GameTextUI`中。例如：毒药、黑暗和增益状态标签在这里解析。
- `m_ExplanationTextID`：**已确认。** 可见的异常状态解释文本 ID，同样主要在 `GameTextUI` 中。
- `m_IconTexID`：**已确认。** `Texture/TextureID.m_id` 为主要异常状态图标。
- `m_IconSptTexID`：**已确认。** `Texture/TextureID.m_id` 用于支持/次要异常状态图标。在 OT0 中，它通常匹配 `m_IconTexID`。
- `m_AddEffect`：**已确认。** `Effect/EffectList.m_id` 在添加异常状态时生成。
- `m_CharacterEffect`：**已确认。** `Effect/EffectList.m_id` 附着在受影响的角色上，同时异常状态仍然有效。
- `m_FieldEffect`：**已确认。** `Effect/EffectList.m_id` 用于该异常状态的现场地图变体。
- `m_InvokeEffect`：**已确认。** `Effect/EffectList.m_id` 在异常状态触发或滴答时生成。
- `m_OnGroundInvoke`：**观察到。** 允许异常状态在正常战斗行动时间之外的调用/勾选行为，例如野战地面用例。
- `m_AddEffectSound`：**已确认。** `Sound/SoundList.m_id` 在应用异常状态时播放。
- `m_InvokeSound`：**已确认。** `Sound/SoundList.m_id` 在异常状态触发或滴答时播放。
- `m_ActionID`：**已确认。** `Battle/BattleActionID.m_id` 异常状态状态动画挂钩。
- `m_ActionPriority`：**确认在 OT0 中未使用。**存在于结构中，但 OT0 将其保留为零。
- `m_ReplaceCharaID`：**确认在 OT0 中未使用。**存在于结构中，但 OT0 将其保留为零。
- `m_CountDown`：**观察到。** 将异常状态标记为倒计时样式。
- `m_NoCount`：**观察到。** 防止普通转动/计数消耗。 OT0 将其用于吸收、保证/概率逃避、胆量和类似掌握的状态。
- `m_StatusDetailSort`：**观察到。** 用于异常状态/详细信息显示的 UI 订购键。
- `m_IgnoreLimit`：**观察到。**绕过普通的增益/减益上限规则。 OT0 将其用于 SP 成本修正、类魅力状态、伤害限制增加以及类似的特殊情况光环。
- `m_RemainDead`：**观察到。** 保持异常状态对死亡单位或死亡有意义。 OT0 将其用于类似重新加注的状态和一些特殊的持久状态。
- `m_IsNotMemorize`：**观察到。**稀有内存/快照排除标志。 OT0 仅在两行上使用它。
- `m_AbsorbHealStatus`：**已确认。** `Character/CharaStatusID.m_id` 通过吸收/治疗式异常状态逻辑恢复。
- `m_NotRace`：**观察到。** 反转或否定 `m_EnemyRace` 中的竞争过滤器。 OT0 中很少见。
- `m_EnemyRace`：**观察到。** 用于异常状态逻辑的小型竞赛过滤器阵列。 OT0 中很少见。
- `m_ForceSupportAbility`：**已确认在 OT0 中未使用。** 始终为 false。
- `m_ForceCommandAbility`：**观察到。** 在标准负面状态（如中毒、流血、麻痹和黑暗）上设置稀有标志。最安全的解读是，它将这些行保留在命令能力交互路径中，但 OT0 不会在 SDK 转储中提供更好命名的调用者。
- `m_WeaponAppendType`：**观察到。** 武器附加行为类型 ID。尽管有这个名称，OT0 值并不是一个干净的 `EWEAPON_TYPE` 映射，因此请将其视为更高级别的追加行为选择器而不是原始武器枚举。
- `m_MagicAppendType`：**观察到。**元素追加行为类型 ID。同样，OT0 值不是普通的 `EMAGIC_TYPE` 映射。
- `m_DuplicateAilment`：**观察到。** 允许相同异常状态的重复实例。 OT0 仅在一行上使用它。
- `m_SkillReplace`：**确认在OT0中未使用。**保留的技能替换挂钩。

#### 具体 OT0 示例
- 普通状态族很容易直接看到：`SkillAilmentType#1 POISON`是`Poison`，`#8 HIGH_POISON`是`Toxin`，`#2 BLEED`是`Bleeding`，`#6 FEAR`是`Terror`。
- `m_RemoveConditions` 不是装饰性的。 `SkillAilmentType#4 SLEEP` 使用 `m_RemoveConditions = [10,0]`，而 `#285 DEEP_SLEEP` 使用 `[1,0]`； OT0 清楚地区分了同一广泛睡眠主题内的不同移除/中断逻辑系列。
- 计数器/拦截系列显示了为什么 `m_ResistWeapon` 和 `m_ResistMagic` 作为反应掩模比作为普通耐性表更好地读取。 `#52 COUNTER_SWORD` 和 `#70 INTERCEPT_SWORD` 是这些系列中具有代表性的武器专用行。
- `m_CountDown` 是一个真正的游戏标志。 `SkillAilmentType#20 COUNTDOWN`是`Fatal Sentence`，而`#43 DELAYED_SKILL`和`#44 DELAYED_RANGE_SKILL`都是可见的`Countdown`变体，在计数结束时触发排队技能。
- `m_ForceCommandAbility` 不是随机噪声。 OT0 将其设置为经典的负状态，例如 `#1 POISON`、`#2 BLEED`、`#5 PARALYSIS` 和 `#11 DARKNESS`。
- `m_IgnoreLimit` 也是真实的。 `#203 BUFF_SP_COST_REDUCE (SP Cost Down)`、`#226 BUFF_REDUCE_DAMAGE_HP (Reduce Damage)` 和 `#397 INCREASE_DAMAGE_LIMIT (Surpassing Power)` 都携带它，这符合它们绕过正常 buff/debuff-cap 处理的想法。
- `m_RemainDead` 不限于一行。 `#283 RERAISE_BUFF (Encore)` 和 `#503 RERAISE_BUFF_LAST_DIED (Last Man Standing)` 都使用它，这正是您所期望的在失能后仍然重要的状态。

#### 大陆的霸者专用 额外字段
- `m_IsBuffDebuff`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 显式 buff/debuff 分类标志。
- `m_CompareTurn`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 附加回合比较参数。
- `m_CompareOverrideValue`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 附加值比较参数。
- `m_DisableBoostAnim`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 当异常状态触发时抑制增强动画。
- `m_StatusDetailAllName`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。**用于所有目标显示的更丰富的状态详细信息名称文本 ID。
- `m_StatusDetailName`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。**用于单目标显示的更丰富的状态详细信息名称文本 ID。
- `m_StatusDetailCommentTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 状态详细信息 UI 的注释文本。
- `m_StatusDetailCommentCalcTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 状态详细信息 UI 的计算感知注释文本。
- `m_StatusDetailCommentRatedTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。**状态详细信息 UI 的评级评论文本。
- `m_StatusDetailCommentNowRatedTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。**状态详细信息 UI 的当前值评级评论文本。
- `m_StatusDetailCommentFixedTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。**状态详细信息 UI 的固定值注释文本。
- `m_StatusDetailAllCommentTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 全目标注释文本。
- `m_StatusDetailAllCommentCalcTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 全目标计算感知注释文本。
- `m_StatusDetailAllCommentRatedTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 全目标评级评论文本。
- `m_StatusDetailAllCommentNowRatedTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 所有目标当前评级评论文本。
- `m_StatusDetailAllCommentFixedTextID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 全目标固定值注释文本。
- `m_AllowAddAilmentOnDead`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 允许应用于失效单元。
- `m_MaxUseCount`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 异常状态可以触发的最大次数。
- `m_IsAttributeCheckAppendType`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**属性感知附加类型验证。
- `m_BindWeakID`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 弱点绑定辅助行或键。
- `m_IsAdditionWeak`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。** 将异常状态标记为 大陆的霸者 附加弱点机制的一部分。
- `m_AilmentTagID`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 异常状态的标签/组标识符。
- `m_Attribute`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 大陆的霸者 异常状态逻辑的属性选择器。
- `m_TargetAilment`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 转换/交互逻辑的目标异常状态 ID。
- `m_ToUnlimited`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**转换为无限持续时间。
- `m_Parallel`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**允许相关状态并行共存。
- `m_PhaseOver`：**从名称和《歧路旅人：大陆的霸者》侧用法观察。**在相变中持续存在。
- `m_SkillFilterType`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 《歧路旅人：大陆的霸者》侧过滤器 UI 和分类使用的 `SkillFilterType.m_id`。

### `SkillAilmentOffsets`
每个模型的异常状态锚预设。该表位于 `Skill/` 下，但其最明显的下游 OT0 消费者是 `Enemy/EnemyTypeID.m_AilmentOffset`，而不是 `SkillID` 本身。

- **OT0 行：** `853`。
- **《歧路旅人：大陆的霸者》行数：** `922`。
- **OT0 SDK 行结构：** `FSkillAilmentOffsetsBase`。
- 该行之所以存在，是因为异常状态图标/效果需要在不同的敌人体型上有不同的锚定位置。 OT0 存储包含所有异常状态家族锚对的预设行。

#### 逐个字段
- `m_id`：**已确认。** 偏移预设 ID。 OT0 `Enemy/EnemyTypeID.m_AilmentOffset` 指向这里。
- `m_PoisonX`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中毒害锚点 X。
- `m_PoisonY`：**通过字段名称和下游用途确认。** 该战斗者模型/类型的预设行中的毒锚 Y。
- `m_BleedX`：**通过字段名称和下游用途确认。** 在该战斗模型/类型的预设行中出血锚点 X。
- `m_BleedY`：**通过字段名称和下游用途确认。** 在该战斗模型/类型的预设行中出血锚点 Y。
- `m_SilenceX`：**通过字段名称和下游用途确认。** 使该战斗者模型/类型的预设行中的锚点 X 静音。
- `m_SilenceY`：**通过字段名称和下游用途确认。** 沉默该战斗者模型/类型的预设行中的锚点 Y。
- `m_SleepX`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中睡眠锚点 X。
- `m_SleepY`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中睡眠锚点 Y。
- `m_PalalysisX`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的麻痹锚点 X。
- `m_PalalysisY`：**通过字段名称和下游用途确认。** 该战斗者模型/类型的预设行中的麻痹锚点 Y。
- `m_FearX`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中恐惧锚点 X。
- `m_FearY`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中恐惧锚点 Y。
- `m_WasteX`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的废物/SP废物锚点X。
- `m_WasteY`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的废物/SP-废物锚点 Y。
- `m_DarknessX`：**通过字段名称和下游用途确认。** 该战斗者模型/类型的预设行中的黑暗锚点 X。
- `m_DarknessY`：**通过字段名称和下游用途确认。** 该战斗者模型/类型的预设行中的黑暗锚点 Y。
- `m_HeadacheX`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的头痛锚点 X。
- `m_HeadacheY`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的头痛锚点 Y。
- `m_EnergyDrainX`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的能量消耗锚点 X。
- `m_EnergyDrainY`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的能量消耗锚点 Y。
- `m_RegenerateX`：**通过字段名称和下游用途确认。** 在该战斗模型/类型的预设行中重新生成锚点 X。
- `m_RegenerateY`：**通过字段名称和下游用途确认。** 在该战斗模型/类型的预设行中重新生成锚点 Y。
- `m_ProvokeX`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中激发锚点 X。
- `m_ProvokeY`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中激发锚点 Y。
- `m_CounterX`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的计数器锚点 X。
- `m_CounterY`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的计数器锚点 Y。
- `m_InterceptX`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中拦截锚点 X。
- `m_InterceptY`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中拦截锚点 Y。
- `m_BreakX`：**通过字段名称和下游用途确认。** 在该战斗者模型/类型的预设行中断开锚点 X。
- `m_BreakY`：**通过字段名称和下游用途确认。** 在该战斗模型/类型的预设行中断开锚点 Y。
- `m_CountDownX`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的倒计时锚点 X。
- `m_CountDownY`：**通过字段名称和下游用途确认。** 该战斗模型/类型的预设行中的倒计时锚点 Y。
- `m_PerfectInterceptScale`：**观察到。** 用于完美截距样式显示的额外比例值。 OT0 将其填充到每一行。

### `SkillAilmentSkillReplaceList`
小异常状态驱动的技能替代帮手。 OT0 实际上使其处于休眠状态，但该该结构仍值得记录。

- **OT0 行：** `1`。
- **《歧路旅人：大陆的霸者》行数：** `12`。
- **OT0 SDK 行结构：** `FSkillAilmentSkillReplaceListBase`。

#### 逐个字段
- `m_id`：替换包的主键。
- `m_SkillList`：**观察到。**替换后的 `SkillID` 行列表。
- `m_AilmentType`：**已确认。** `SkillAilmentType.m_id` 触发替换包。

### `SkillResistList`
这是一个内联的、以百分比为基础的武器/属性耐性载荷表。它经常和 `EnemyWeakID` 混淆，但其实际存储值已经足以证明两者属于不同系统。

- **OT0 行：** `215`。
- **《歧路旅人：大陆的霸者》行数：** `241`。
- **OT0 SDK 行结构：** `FSkillResistListBase`。

#### 逐个字段
- `m_id`：耐性载荷行的主键。
- `m_ResistWeapon`：**已确认。** 按武器系列直接百分比/增量阵列。 OT0 值包括 `50`、`27`、`22`、`12`、`2`、`-40`、`-50` 和 `-100` 等条目，因此这显然不是** `ResistType` ID 数组。
- `m_ResistMagic`：**已确认。** 按元素族直接进行百分比/增量数组，与 `m_ResistWeapon` 具有相同的语义。

### `SkillResistAilmentID`
技能和敌人行使用的打包异常状态抗性行，需要明确的异常状态/比率对而不是一个内联基本比率数组。

- **OT0 行：** `74`。
- **《歧路旅人：大陆的霸者》行数：** `60`。
- **OT0 SDK 行结构：** `FSkillResistAilmentIDBase`。

#### 逐个字段
- `m_id`：异常状态抵抗包的主键。
- `m_ResistAilments`：**已确认。** 包所涵盖的最多 16 行 `SkillAilmentType.m_id` 行的列表。
- `m_ResistRate`：**已确认。** `m_ResistAilments` 的匹配速率数组。阵列是平行的。

### `SkillHitRateList`
可重复使用的多次命中和重复分辨率概率表。 `SkillAvailID.m_HitTypes` 指向这里。

- **OT0 行：** `68`。
- **《歧路旅人：大陆的霸者》行数：** `72`。
- **OT0 SDK 行结构：** `FSkillHitRateListBase`。

#### 逐个字段
- `m_id`：命中率包的主键。
- `m_MinCount`：**观察到。** 考虑随机继续之前的最小保证计数。
- `m_CountAilment`：**确认形状。**附加异常状态/计数参考通道。 OT0 在每一行将其保留为零，但 大陆的霸者 确实使用它。
- `m_HitRates`：**观察到。** 十六槽概率表控制额外的命中掷骰或重复分辨率计数。
- `m_Shuffle`：**观察到。** 随机/随机播放命中率模式的行为。

### `SkillIconList`
小配对图标库。真实，但不是 OT0 中 `SkillID.m_SkillIconID` 的主要目标。

- **OT0 行：** `11`。
- **《歧路旅人：大陆的霸者》行数：** `12`。
- **OT0 SDK 行结构：** `FSkillIconListBase`。

#### 逐个字段
- `m_id`：图标对的主键。
- `m_BattleIconTexID`：**已确认。** `Texture/TextureID.m_id` 为战斗中图标。
- `m_MenuIconTexID`：**已确认。** `Texture/TextureID.m_id` 为菜单图标。

### `SkillUniqueIconID`
用于特殊命令面板或增益显示的独特图标/文本覆盖表。

- **OT0 行：** `39`。
- **《歧路旅人：大陆的霸者》行数：** `137`。
- **OT0 SDK 行结构：** `FSkillUniqueIconIDBase`。
- OT0 `SkillID` 行实际上并不使用此表，但表本身已完全填充且结构清晰。

#### 逐个字段
- `m_id`：唯一图标行的主键。
- `m_TextID`：**观察到。** 唯一图标的文本标签。一些 OT0 ID 在 `GameTextUI` 中完全解析（例如 `65490 -> Hang Tough`），而许多 OT0 ID 不存在于当前导出的 OT0 文本库中。将未解析的子集视为遗留/共享内容，而不是作为该字段未使用的证据。
- `m_SkillIconTexID`：**已确认。** `Texture/TextureID.m_id` 用于独特的图标纹理。
- `m_OverrideCommandPanel`：**观察到。** 在命令面板中使用这个独特的图标/文本。
- `m_OverrideInBattle`：**观察到。** 在战斗用户界面中使用这个独特的图标。
- `m_OverrideBuff`：**观察到。** 使用这个独特的图标进行增益/状态显示。

### `SkillSupplyItemGroup`
OT0 村庄/补给辅助表。它将技能操作码绑定到奖励抽奖组。

- **OT0 行：** `45`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** `FSkillSupplyItemGroupBase`。
- **相关 OT0 SDK 枚举：** `ESKILL_MODIFY_TYPE`。

#### 具体 OT0 示例
- 第一个可见材质系列已经足以显示图案：`SkillSupplyItemGroup#100/#101` 指向 `Timber Supply` / `Timber Supply+`，`#200/#201` 指向 `Top Timber Supply` / `Top Timber Supply+`。
- 石头、布料、矿石和其他村庄材料重复相同的模式。换句话说，该表并不是一个通用的战斗奖励列表；而是一个普通的战斗奖励列表。这是一个精心策划的村庄供应彩票，以触发技能为关键。
- OT0 对这些行中的大部分使用 `m_SkillModifyType = 201`，这与 `ESKILL_MODIFY_TYPE` 的 `ITEM_SUPPLY` 分支对齐。

#### 逐个字段
- `m_id`：供应项目组的主键。
- `m_SkillId`：**已确认。**触发该抽奖组的技能为`SkillID.m_id`。
- `m_SkillModifyType`：**通过枚举/值重叠确认。**来自 `ESKILL_MODIFY_TYPE` 的经济操作码。 OT0 主要使用 `201 ITEM_SUPPLY`，还有少量 `208 FARM_HARVEST_ITEM_AMOUNT_LOTUP` 和 `254 ITEM_SUPPLY_LOT_IN_GROUP` 行。
- `m_Lot`：**观察到。** 团体级彩票权重或乘数。 OT0 将其保留在每一行的 `100` 处。

### `SkillSupplyItemList`
OT0 村/提供彩票条目。这是`SkillSupplyItemGroup`从中选择的有效负载表。

- **OT0 行：** `166`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** `FSkillSupplyItemListBase`。

#### 逐个字段
- `m_id`：彩票条目的主键。
- `m_GroupId`：**已确认。** `SkillSupplyItemGroup.m_id` 父组。
- `m_RewardId`：**观察到有强有力的证据。**奖励型判别器。 OT0 使用 `1000` 进行金钱/叶子条目，使用 `1001` 进行物品奖励条目。
- `m_ItemId`：**观察到有强有力的证据。** `Item/ItemList.m_id` 表示物品奖励，或 `0` 表示金钱奖励。
- `m_Values`：**观察到。**奖励金额或物品数量。对于 `m_RewardId = 1000`，这是叶子数量；对于 `1001`，它是商品数量。
- `m_Lot`：**观察到。**父组内的每项彩票权重。
- `m_IsUnique`：**观察到。**独特的奖励标志。 OT0 仅在组 `4000` 中的两个条目上使用它。

### `SkillTargetShopList`
OT0 目标商店助手行用于村庄/贸易技能。

- **OT0 行：** `24`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** `FSkillTargetShopListBase`。
- **相关 OT0 SDK 枚举：** `ESHOP_TYPE`。

#### 此表使用的确切 OT0 SDK 枚举引用
##### `ESHOP_TYPE`
引用自 `GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6100`。
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
- `SkillTargetShopList.m_ShopType` 存储原始 `ESHOP_TYPE` 数值。辅助行的存在使得技能不仅可以绑定商店系列，还可以绑定技能应该针对的具体地图上下文。
- 这就是为什么 OT0 可以对旅馆或铁匠等通用系统以及 `eVILLAGE_BASE` 和 `eTRADE` 等村庄特定助手使用相同的枚举系列。

#### 具体 OT0 示例
- `#1..#16` 行涵盖 `Savvy Shopper`、`Master Haggler`、`Savvy Seller`、`Master Salesman`、`Appraiser`、`Grand Appraiser`、`Wholesale Expert` 和 `Master Tradesman` 等交易价格技巧；所有这些都使用 `m_ShopType = 22`，即 `eTRADE`。
- `#17..#24`行涵盖基于村庄的实用技能，例如`Negotiator`、`Fixer`、`Cook`和`Grand Chef`；那些使用 `m_ShopType = 17`，即 `eVILLAGE_BASE`。
- 每个股票 OT0 行都使用 `m_MapId = 900002`，这就是为什么该表读起来不太像通用商店目录，而更像是一个仅限于一个地图上下文的村庄系统帮助表。

#### 逐个字段
- `m_id`：目标商店行的主键。
- `m_SkillId`：**已确认。** `SkillID.m_id` 商店定位技能。
- `m_ShopType`：**已确认。**目标商店的 `ESHOP_TYPE` 选择器。 OT0 使用 `17 (eVILLAGE_BASE)` 和 `22 (eTRADE)`。
- `m_MapId`：**已确认。** `Map/MapListTable.m_id` 提供目标商店的地图上下文。 OT0 在每一行使用 `900002`（村庄底图）。

### `SkillTargetFarmItemList`
OT0 牧场/农场助手行用于附加或针对特定村庄农场项目的技能。

- **OT0 行：** `2`。
- **《歧路旅人：大陆的霸者》行数：** 不存在于 大陆的霸者 `Skill/` 文件夹中。
- **OT0 SDK 行结构：** `FSkillTargetFarmItemListBase`。
- **相关 OT0 SDK 枚举：** `ESKILL_MODIFY_TYPE`。

#### 具体 OT0 示例
- 两个原版 OT0 行都属于 `Sheep King` 系列：`SkillTargetFarmItemList#1` 指向 `SkillID#900938 Sheep King`，`#2` 指向 `SkillID#903901 Sheep King+`。
- 两行都以 `m_VillageFarmItemId = 103` 为目标；辅助 ID `105` 和 `106` 在原版 OT0 中主要作为空占位符存在，这就是为什么在克隆模式时应谨慎对待该字段的原因。

#### 逐个字段
- `m_id`：场目标行的主键。
- `m_SkillId`：**已确认。** `SkillID.m_id` 农场瞄准技能。
- `m_SkillModifyType`：**已确认。** OT0 使用 `215`，即 `ESKILL_MODIFY_TYPE` 中的 `RANCH_APPEND_ITEM`。
- `m_VillageFarmItemId`：**已确认。**主要目标农场物品的`Village/VillageFarmItem.m_id`。
- `m_AdditionalVillageFarmItemId`：**已确认。** 次要 `Village/VillageFarmItem.m_id`。在 OT0 中，引用的行是空占位符（`105` 和 `106`），因此不要假设该字段在原版 OT0 中已具有有意义的实时内容。

### `SkillVisitorList`
访客/召唤助手行。 OT0 几乎不使用该表，但一个活动行足以显示预期的形状。

- **OT0 行：** `2`。
- **《歧路旅人：大陆的霸者》行数：** `7`。
- **OT0 SDK 行结构：** `FSkillVisitorListBase`。

#### 具体 OT0 示例
- OT0 只有一个活动的非零访问者行：`SkillVisitorList#1`。
- 该行召唤`m_EnemyID = 33000`，其敌人标签为`Summon_PC072_Linde`。
- 它的放置数据也不是微不足道的填充物：`m_OffsetsX[0] = -500.0` 和 `m_IconID = 1000`，这强烈暗示访客表正在处理召唤式客人的生成布局和 UI 标识。

#### 逐个字段
- `m_id`：访问者行的主键。
- `m_EnemyID`：**在OT0中确认。** 访客战斗者的`Enemy/EnemyID.m_id`。 OT0 的实时非零行使用 `33000`，即 `Summon_PC072_Linde`。
- `m_EscortNum`：**观察到。** 每时隙护送计数数组。
- `m_EscortType`：**观察到。** 每插槽护送/布局类型数组。 OT0 将其保留为零。
- `m_OffsetsY`：**观察到。** 用于访客放置的每槽 Y 偏移。
- `m_OffsetsX`：**观察到。** 用于访客放置的每槽 X 偏移。
- `m_CallSE`：**观察到。** 每插槽呼叫声音 ID。 OT0 使它们为零。
- `m_IconID`：**未解决。** 访客 UI 图标标识符。 OT0 仅在活动行上使用值 `1000`，并且当前导出未证明目标表。

#### 《歧路旅人：大陆的霸者》侧的数据库结构更改
- `m_EnemyType`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 大陆的霸者 在此存储敌人类型，而不是直接 OT0 样式的 `EnemyID`。

### `SkillEnhancedList`
增强辅助表。 OT0 实际上将其留空，但架构很简单。

- **OT0 行：** `1`。
- **《歧路旅人：大陆的霸者》行数：** `10`。
- **OT0 SDK 行结构：** `FSkillEnhancedListBase`。

#### 逐个字段
- `m_id`：增强包的主键。
- `m_EnhancedSkills`：**观察到。**强化后技能 ID 的列表。OT0 只有 0 行，因此没有可直接参照的原版内容。

### `SpecialSkillID`
特殊技能主密钥表。 OT0 仅存储 ID；实际的每级映射位于 `SpecialSkillGrowthList` 中，而玩家绑定位于 `Character/CharaPlayerSpecialSkillList` 中。

- **OT0 行：** `86`。
- **《歧路旅人：大陆的霸者》行数：** `266`。
- **OT0 SDK 行结构：** `FSpecialSkillIDBase`。

#### 逐个字段
- `m_id`：特殊技能系列的主键。

### `SpecialSkillGrowthList`
从特殊技能系列到该级别授予的具体 `SkillID` 行的每级别映射。

- **OT0 行：** `497`。
- **《歧路旅人：大陆的霸者》行数：** `5301`。
- **OT0 SDK 行结构：** `FSpecialSkillGrowthListBase`。

#### 逐个字段
- `m_id`：增长行的主键。
- `m_SpecialSkillID`：**已确认。** `SpecialSkillID.m_id`正在成长的特殊技能家族。
- `m_Level`：**已确认。** 增长水平。 OT0 通常使用 `1..3` 来实现面向玩家的 EX/特殊技能，但文件本身包含高达 `10` 级别的行。
- `m_SkillID`：**已确认。** `SkillID.m_id` 在该特殊技能级别上被授予或替代。
- `m_DisplayPriority`：**观察到。** 增长行中的 UI 排序键。 OT0 主要保留 `0`，但它也使用值 `20..90`。

#### 大陆的霸者专用 额外字段
- `m_InitialGaugeValue`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 特殊技能级别的起始量表。
- `m_GaugeRegenerateValue`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 每个周期/转的仪表再生。
- `m_CountLimit`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 使用上限。
- `m_RewardTypes`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 增长行的奖励类型数组。
- `m_ItemIDs`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 奖励项目 ID 与 `m_RewardTypes` 平行。
- `m_ItemCounts`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 奖励计数与奖励数组并行。
- `m_AvailValues`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 传递到底层可用逻辑的每级调整值。
- `m_AvailHitTypes`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 每级命中类型覆盖。
- `m_AilmentValues`：**从《歧路旅人：大陆的霸者》侧数据库结构确认。**每级异常状态效力覆盖。
- `m_AilmentTurns`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 每个级别的异常状态持续时间覆盖。
- `m_DamageLimitUp`：**从 《歧路旅人：大陆的霸者》侧的数据库结构确认。** 每级伤害上限增加。

### `ClassSkillConditionList`（仅限 大陆的霸者）
用于职业技能使用条件的 大陆的霸者专用 帮助表。在此处提供的 SDK 转储中找不到匹配的专用 OT0 SDK 行结构，因此下面的字段含义源自 《歧路旅人：大陆的霸者》侧的数据库结构和字段名称。

#### 逐个字段
- `m_id`：职业技能条件行的主键。
- `m_UseCondition`：**从字段名称和 大陆的霸者 角色观察。** 由职业技能系统评估的条件 ID。
- `m_ConditionText`：**从字段名称和 大陆的霸者 角色观察。**向玩家显示条件的文本 ID。

### `SkillEffectiveLinkageList`（仅限 大陆的霸者）
仅《歧路旅人：大陆的霸者》链式演示帮助程序。该表由 `SkillEffectiveID.m_SkillEffectiveLinkageID` 引用。

#### 逐个字段
- `m_id`：链接行的主键。
- `m_HoldTime`：链接演示组的总保持时间。
- `m_AvailTime`：链接的演示组的可用性/解决时间。
- `m_OffsetX`：用​​于链接演示文稿放置的组级 X 偏移。
- `m_OffsetY`：用​​于链接演示文稿放置的组级 Y 偏移。
- `m_LimitMax`：观察到的联动组上限字段。
- `m_LinkageType`：观察到的联动模式选择器。
- `m_Effectives`：**通过 ID 重叠确认。** 此链接包最多发出十六个 `SkillEffectiveID.m_id` 行。

### `SkillFilterCategory`（仅限 大陆的霸者）
大陆的霸者 技能过滤器 UI 的仅限 大陆的霸者 过滤器类别图例。

#### 逐个字段
- `m_id`：过滤器类别的主键。
- `m_Name`：类别名称的文本 ID。
- `m_Priority`：类别的 UI 排序优先级。

### `SkillFilterType`（仅限 大陆的霸者）
仅《歧路旅人：大陆的霸者》过滤器类型图例。 `SkillID.m_ForcedSkillFilter`和`m_ExclusionSkillFilter`指向这里，每个过滤器类型都属于一个过滤器类别。

#### 逐个字段
- `m_id`：过滤器类型的主键。
- `m_Name`：过滤器名称的文本 ID。
- `m_Priority`：过滤器类型的 UI 排序优先级。
- `m_Category`：**已确认。** `SkillFilterCategory.m_id` 父类别。

### `SkillModifyType`（仅限 大陆的霸者）
从修改操作码到过滤器类型的仅限 大陆的霸者 的桥接表。行 ID 与 大陆的霸者 使用的非零 `SkillAvailID.m_ModifyType` 值完全一致。

#### 逐个字段
- `m_id`：**已确认。**修改`SkillAvailID.m_ModifyType`对应的操作码密钥。
- `m_SkillFilterType`：**通过 ID 重叠确认。** 最多 5 行与该修改操作码关联的 `SkillFilterType.m_id` 行。

### `SpecialSkillExtendCategory`（仅限 大陆的霸者）
特殊技能扩展类别的仅限 大陆的霸者 的图例表。

#### 逐个字段
- `m_id`：扩展类别的主键。
- `m_Name`：类别名称的文本ID。

### `SpecialSkillExtendType`（仅限 大陆的霸者）
扩展效果类型的仅限 大陆的霸者 图例。 `SpecialSkillExtendList.m_ExtendType` 指向这里。

#### 逐个字段
- `m_id`：扩展类型的主键。
- `m_Category`：**已确认。** `SpecialSkillExtendCategory.m_id`。
- `m_SubCategory`：在父类别内观察到的子类型选择器。
- `m_EffectName`：效果名称的文本ID。
- `m_DetailText`：效果详细信息/说明的文本 ID。
- `m_EffectValue`：此扩展类型的主要数字有效负载。
- `m_SubEffectValue`：此扩展类型的辅助数字有效负载。
- `m_AutoLearn`：自动解锁标志。
- `m_SubText`：与主要效果名称/详细信息一起使用的额外文本 ID。

### `SpecialSkillExtendList`（仅限 大陆的霸者）
仅《歧路旅人：大陆的霸者》每个特殊技能扩展映射。这是有效负载表，说明特殊技能获得哪种扩展类型、如何解锁以及与其捆绑的奖励。

#### 逐个字段
- `m_id`：扩展行的主键。
- `m_TargetSSkill`：**已确认。** `SpecialSkillID.m_id` 是扩展程序的目标。
- `m_ExtendType`：**已确认。** `SpecialSkillExtendType.m_id` 描述扩展效果。
- `m_GetPlaceText`：告诉玩家扩展名在哪里/如何获得的文本ID。
- `m_PriorityItemRewardType`：优先奖励型代码。
- `m_PriorityItemID`：优先奖励物品ID。
- `m_PriorityItemNum`：优先奖励物品数量。
- `m_RewardTypes`：通用奖励型数组。
- `m_ItemIDs`：与`m_RewardTypes`平行的奖励物品ID。
- `m_ItemCounts`：奖励计数与奖励数组并行。
- `m_OpenTimestamp`：用​​于打开/解锁扩展的时间戳门。
- `m_AddSSkillLv`：此扩展行授予或要求的附加特殊技能级别。

## 直接喂入`Skill/`的相关文件夹

### `Character/`
- ``JobID.m_BaseAttackSkill` and `m_SubAttackSkill``：**已确认。** 这些是直接的 `SkillID.m_id` 参考。例如OT0 `JobID#1`（战士）使用基础攻击技能`700`和副攻击技能`724`。
- ``WeaponAnimationSet``：**已确认。** `WeaponType.m_AnimationSet` 指向此处。这是从武器家族到战斗动画家族的桥梁。
- ``CharaStatusID``：**已确认。** `SkillAvailID.m_ModifyStatus`、`SkillAilmentType.m_TargetStatus` 和 `SkillAilmentType.m_AbsorbHealStatus` 均指向此处。
- ``CharaGuest``：**已确认。** `SkillAvailID.m_SummonGuestID` 指向此处来宾召唤行为。
- ``CharaPlayerSpecialSkillList``：**已确认。** 这将玩家与 `SpecialSkillID` 家族绑定。 OT0 有 `48` 行； `44` 不同的 `m_SpecialSkillID` 值完全解析为 `Skill/SpecialSkillID`。

### `Enemy/`
- ``EnemyWeakID``：**已确认且至关重要。**这就是`SkillAvailID.m_ResistID`所指向的。 `EnemyWeakID` 内部的数组存储 `ResistType` ID，这就是为什么 `m_ResistID` 的意思是“交换弱点表”，而不是“从 `SkillResistList` 添加百分比有效负载”。
- ``EnemyWeakChangeID``：**已确认。** `SkillAvailID.m_WeakChangeID` 在此指出分阶段的弱点更改。
- ``EnemyWeakLockID``：**已确认。** `SkillAvailID.m_WeakLockID` 此处指向弱点槽锁定。
- ``EnemyReinforcements``：**已确认。** `SkillAvailID.m_Reinforcement` 指向此处用于增援召唤表。
- ``EnemyModeID``：**已确认。** `SkillAvailID.m_ModeChangeID` 指向此处的敌人模式/阶段变化。
- ``EnemyTypeID.m_AilmentOffset``：**已确认。**指向`Skill/SkillAilmentOffsets.m_id`。这就是为什么异常状态抵消预设表属于技能生态系统，尽管 `SkillID` 没有直接指向它。

### `Battle/`
- ``BattleActionID``：**已确认。** `SkillEffectiveID.m_Animations[*]` 和 `SkillAilmentType.m_ActionID` 指向此处。
- ``BattleCameraParams``：**已确认。** OT0 `SkillEffectiveID.m_Cameras[*]` 指向此处。

### `Effect/`、`Sound/` 和 `Texture/`
- ``Effect/AttachEffect``：**已确认。** `SkillID.m_AttachEffect` 指向此处。
- ``Effect/EffectList``：**已确认。** `SkillEffectiveID.m_Effects[*]` 和多个 `SkillAilmentType` 效果字段指向此处。
- ``Sound/SoundList``：**已确认。** `SkillEffectiveID.m_Sounds[*]` 以及 `SkillAilmentType` 的声音字段都指向这里。
- ``Sound/PartVoiceID``：**已确认。** `SkillID.m_VoiceID` 和 `SkillEffectiveID.m_Voices*` 指向此处。
- ``Texture/TextureID``：**已确认。** `SkillID.m_SkillIconID`、`WeaponType`/`MagicType` 图标字段、`SkillIconList`、`SkillUniqueIconID` 和异常状态图标字段均在此处解析。

### `GameText/`
- ``GameText/SystemText/GameTextSkill` and localized `GameText/Localize/*/SystemText/GameTextSkill``：**已确认。** `SkillID.m_Name`、`SkillID.m_Detail` 和大多数 `SkillEffectiveID.m_Texts` 的主文本库。
- ``GameText/SystemText/GameTextUI` and localized `GameText/Localize/*/SystemText/GameTextUI``：**已确认。** `WeaponType.m_TextID`、`MagicType.m_TextID`、大多数 `SkillAilmentType.m_TextID`/`m_ExplanationTextID` 和一些 `SkillID.m_Name` 异常值的主要文本库。

### `AIBattle/` 敌人技能管道
- `Basic chain`：**已确认。** 对于普通敌人，链是 `EnemyID.m_SkillsID -> AIBattle/TacticalSkillList.m_id`、`EnemyID.m_TacticalAssignID -> AIBattle/TacticalAssignList.m_id`、`TacticalAssignList.m_Tactics[*] -> TacticalList.m_id` 和 `TacticalActionList.m_SkillIndex`，然后索引到所属的 `TacticalSkillList.m_UseSkills` 数组中。
- `Concrete example 1`：**已确认。** `EnemyID#32300` 使用 `m_SkillsID = 55000` 和 `m_TacticalAssignID = 30500`； `TacticalSkillList#55000`包含`610001`、`610008`、`610009`等技能ID，`TacticalActionList`通过`m_SkillIndex = 0/1/2`选择它们。
- `Concrete example 2`：**已确认。** `EnemyID#834` 使用 `m_SkillsID = 752` 和 `m_TacticalAssignID = 3`； `TacticalSkillList#752`包含`100405`、`100406`、`100407`，战术行按索引选择。
- `Important exception`：**已确认。** OT0 也有 `m_SkillsID = 0` 但非零 `m_TacticalAssignID` 的敌人（例如一些凯特和野生动物行）。因此，并非每个敌人的行动路径都是简单的战术技能列表查找。
