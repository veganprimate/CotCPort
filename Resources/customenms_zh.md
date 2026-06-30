### **语言：**  [英语](customenms_en.md) | [日语](customenms_jp.md) | [中文](customenms_zh.md) | [韩语](customenms_kr.md)

> 本指南中的运行时证据约定：「直接读取」 表示玩法/UI 代码会为了某项操作读取该字段；「行布局证据」 表示某个 取值函数 或实体化器确立了偏移/形状；「仅实体化」 表示该字段会被复制到运行时对象中，但在所引用的 C 摘录中没有复原出后续的语义消费者。

在《歧路旅人0》中添加新敌人与战斗的流程，主要围绕编辑 `AIBattle`、`Enemy`，以及必要时 `Skill`、`Texture` 和 `Battle` 下的 DataBase 文件展开。下文会说明所有相关文件；标题中的星号数量表示编辑该文件的必要程度（星号越少，越偏向特殊用途），不过仍建议通读全文，以便了解为了不同目的应修改哪些文件。

## ■ `Enemy/` 下
### ■ (***) `EnemyFormations`
用于指定场地上的新敌人阵型。`m_PositionID` 接受 `BattlePositions.m_id` 作为输入（`BattlePositions` 位于 `Battle/` 下）。对于包含大量敌人的自定义战斗，你可能需要查看这些文件，寻找具有足够多非零 `m_PositionID` 条目的合适阵型，或者自行制作阵型。某些 `PositionID` 会根据战斗地图产生差异很大的敌人位置。
`Kingship_structs.hpp` 为 OT0 的阵型分配了以下标签：
```hpp
enum class EENEMY_FORMATION : uint8
{
	NONE                                     = 0,
	MAXENEMYTEST                             = 1,
	EnFr_Sx1_1                               = 72,
	EnFr_Sx2_1                               = 73,
	EnFr_Sx2_1_r                             = 160,
	EnFr_Mx1_1                               = 69,
	EnFr_Mx2_1                               = 70,
	EnFr_Mx3_1                               = 71,
	EnFr_Mx3_2                               = 105,
	EnFr_MLx1_1                              = 102,
	EnFr_MLx2_1                              = 103,
	EnFr_MLx3_1                              = 104,
	EnFr_MLx3_2                              = 106,
	EnFr_Lx1_1                               = 67,
	EnFr_Lx2_1                               = 68,
	EnFr_BOSS_LL_solo                        = 63,
	EnFr_BOSS_LL_duo1                        = 64,
	EnFr_BOSS_LL_duo2                        = 76,
	EnFr_BOSS_LL_duo3                        = 77,
	EnFr_BOSS_LL_trio1                       = 65,
	EnFr_BOSS_LL_trio2                       = 66,
	EnFr_BOSS_LL_trio3                       = 80,
	EnFr_BOSS_XL_solo                        = 59,
	EnFr_BOSS_XL_duo1                        = 60,
	EnFr_BOSS_XL_trio1                       = 61,
	EnFr_BOSS_XL_trio2                       = 62,
	EnFr_BOSS_SP                             = 58,
	BT_PLT_010                               = 18,
	BT_PLT_016                               = 24,
	BT_PLT_020                               = 78,
	BT_PLT_022                               = 30,
	BT_PLT_024                               = 32,
	BT_PLT_032                               = 40,
	BT_PLT_042                               = 75,
	BT_PLT_SE_001                            = 81,
	BT_PLT_SE_002                            = 82,
	BT_PLT_SE_003                            = 83,
	BT_PLT_SE_004                            = 84,
	BT_PLT_CEB_001                           = 85,
	BT_PLT_CEB_002                           = 86,
	BT_PLT_CEB_004                           = 88,
	BT_PLT_BOSS_Tytos2                       = 89,
	BT_PLT_BOSS_Auguste2                     = 90,
	BT_PLT_AM_Chap8_Boss02                   = 95,
	BT_PLT_AM_Chap8_Boss01_7                 = 96,
	EnFr_P010_Cp4EG01                        = 97,
	EnFr_P011_Cp4EG01                        = 98,
	EnFr_P012_Cp4EG01                        = 99,
	EnFr_P014_Cp3EG01                        = 100,
	EnFr_P018_Cp3EG02                        = 101,
	EnFr_P083_Cp4EG01                        = 109,
	EnFr_P137_Cp4EG01                        = 110,
	EnFr_P057_Cp4EG01                        = 111,
	EnFr_P120_Cp4EG01                        = 112,
	EnFr_P110_Cp4EG02                        = 113,
	EnFr_P110_Cp3EG01                        = 114,
	EnFr_P099_Cp4EG01                        = 115,
	EnFr_AB03_BOSS_01                        = 116,
	EnFr_P109_Cp4EG01                        = 118,
	EnFr_P131_Cp4EG01                        = 120,
	EnFr_P133_Cp4EG01                        = 130,
	EnFr_PC068                               = 139,
	EnFr_Mx2_1_r                             = 141,
	EnFr_Mx3_1_r                             = 142,
	EnFr_Mx3_2_r                             = 143,
	EnFr_MLx2_1_r                            = 144,
	EnFr_MLx3_1_r                            = 145,
	EnFr_MLx3_2_r                            = 146,
	EnFr_Lx2_1_r                             = 147,
	EnFr_PC098                               = 148,
	EnFr_BOSS_LL_selafina                    = 172,
	EENEMY_MAX                               = 173,
};
```
#### ■ 运行时读取语义
在本小节中，上文提到的每个键都会给出字段级的运行时位置：它可能是一个精确的具名 C 赋值/读取、一个明确无歧义的行偏移槽位，或者是在 C 导出未将该键保留为活字段时给出的否定性复原结论。解释会刻意限制在所引用 C 代码允许的范围内。

【主要 C 证据】

##### ◆ C-FORM-01：EnemyFormations 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644377-17644403
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyFormationDB(unsigned int FormID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_EnemyFormations.m_Loaded )
  {
    return nullptr;
  }
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v2 = ADatabaseDefineStatic::m_Self;
  else
    v2 = nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_EnemyFormations.m_DBAccess,
                 FormID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_EnemyFormations.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_EnemyFormations.m_DataList.AllocatorInstance.Data[32 * ArrayIndex];
}

//----- (00000001446049D0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyGroupByCondition(int EnemyGroupsByConditionID)
```

##### ◆ C-FORM-02：将阵型位置解析到 BattlePositions

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644973-17645075
//----- (00000001446053B0) ----------------------------------------------------
bool __fastcall UEnemyDB::GetEnemyPlacement(
        UE::Math::TVector<double> *Position,
        int *Priority,
        unsigned int EnemyID,
        int FormID,
        int Index)
{
  FScriptContainerElement *EnemyDB; // rax
  FScriptContainerElement *EnemyFormationDB; // rax
  signed int v10; // ecx
  FScriptContainerElement *BattlePositionDB; // rax
  FScriptContainerElement *v12; // rcx
  bool v13; // dl
  float *v14; // rax
  float v15; // xmm0_4
  long double v16; // xmm1_8

  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( !EnemyDB )
    return 0;
  if ( *(int *)&EnemyDB[8] <= 0 )
    return 0;
  if ( FormID <= 0 )
    return 0;
  EnemyFormationDB = UEnemyDB::GetEnemyFormationDB(FormID);
  if ( !EnemyFormationDB )
    return 0;
  v10 = *(_DWORD *)(*(_QWORD *)&EnemyFormationDB[16] + 4LL * Index);
  if ( v10 <= 0 )
    return 0;
  BattlePositionDB = UEnemyDB::GetBattlePositionDB(v10);
  v12 = BattlePositionDB;
  v13 = BattlePositionDB != nullptr;
  if ( BattlePositionDB )
  {
    v14 = *(float **)&BattlePositionDB[16];
    v15 = *v14;
    v16 = v14[1];
    Position->Z = v14[2];
    Position->Y = v16;
    Position->X = v15;
    *Priority = *(_DWORD *)&v12[32];
  }
  return v13;
}

//----- (0000000144605470) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyPositionCount(int FormID)
{
  unsigned int v1; // ebx
  FScriptContainerElement *EnemyFormationDB; // rax
  int *v3; // rcx
  int *i; // r8

  v1 = 0;
  if ( FormID > 0 )
  {
    EnemyFormationDB = UEnemyDB::GetEnemyFormationDB(FormID);
    if ( EnemyFormationDB )
    {
      v3 = *(int **)&EnemyFormationDB[16];
      for ( i = &v3[*(int *)&EnemyFormationDB[24]]; v3 != i; ++v3 )
      {
        if ( *v3 <= 0 )
          break;
        ++v1;
      }
    }
  }
  return v1;
}

//----- (00000001446054B0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyPositionID(int FormID, int Index)
{
  __int64 v2; // rbx
  FScriptContainerElement *EnemyFormationDB; // rax

  v2 = Index;
  if ( FormID > 0
    && (EnemyFormationDB = UEnemyDB::GetEnemyFormationDB(FormID)) != nullptr
    && (int)v2 >= 0
    && (int)v2 < *(_DWORD *)&EnemyFormationDB[24] )
  {
    return *(unsigned int *)(*(_QWORD *)&EnemyFormationDB[16] + 4 * v2);
  }
  else
  {
    return 0;
  }
}

//----- (00000001446054F0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyPositionIndex(int FormID, int PosID)
{
  FScriptContainerElement *EnemyFormationDB; // rax
  _DWORD *v4; // r8
  __int64 v5; // rcx
  _DWORD *v6; // rax
  _DWORD *v7; // rdx

  if ( FormID <= 0 )
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_PositionID` | `+0x10`（数组）；在引用片段中没有保留下更短的具名赋值 | 阵型行通过 `FormID` 解析；随后游戏按索引读取行 `+0x10` 处的 `TArray<int>`，并将选中的元素视为 `BattlePositions` 行 ID。返回的战斗位置行提供 XYZ 坐标与优先级。 | 已复原 | `C-FORM-01`, `C-FORM-02` |

### ■ (***) `EnemyBattleAnimSet`
在《歧路旅人：大陆的霸者》中，CotC 会为敌人分配一个指向特定 Flipbook 资产的 ID；而 OT0 使用的是一组 ID，每个 ID 都指向此类资产（`m_EnemyTextureIDs` 以 `Textures` 下 `EnemyTexID` 的 `m_id` 作为输入）。
`m_EnemyTextureIDs[0]` **通常**指向精灵的待机动画（后缀 `Idl00`），而 `m_EnemyTextureIDs[1]` 到 `m_EnemyTextureIDs[3]` **通常**指向精灵的攻击动画（后缀 `Atk00` 到 `Atk02`）。你至少需要为待机动画添加一个 Flipbook 资产（也就是在 `EnemyTexID` 下引用一个新路径，并在此处新建条目的 `m_EnemyTextureIDs[0]` 中使用该新条目的 `m_id`）；其他内容则是可选的。

如上所述，这些索引只是**通常**解析为 Idl00、Atk00 等；没有运行时代码强制这些索引必须解析为这些含义：`EnemyTypeID.m_AnimationSetIDs[index]` 会先选择一个 `EnemyBattleAnimSet` 行；随后所选行的 `m_EnemyTextureIDs` 会作为 `EnemyTexID` 资源 ID 被扫描或复制。动作分派是通过为每个贴图向 `EnemyTexID` 表查询 `EBATTLE_ACTION_ID` 获得的。因此，该数组顺序在语义上是**优先级/同值时的决胜顺序**，**不是**一个**动作代码命名空间**。

请注意，CotC 的 `EnemyType` 下的 `m_FlipbookID` 似乎会以不同方式使用这些额外的数组组件：虽然索引 0 仍指向待机贴图，但 CotC 会为某些敌人把不可见贴图分配到索引 1（例如 Shadow of Twin Worlds 中的 Shadow）。这个索引 1 贴图可能覆盖索引 0 贴图，尤其是在它也被作为待机贴图读取时。

Flipbook 资产可能假定以下缩放比例（参见 `Kingship_structs.hpp`）：

```hpp
// Enum Kingship.ECV_FLIPBOOK_SCALE
// NumValues: 0x0006
enum class ECV_FLIPBOOK_SCALE : uint8
{
	Scale_x1                                 = 0,
	Scale_x2                                 = 1,
	Scale_x3                                 = 2,
	Scale_x4                                 = 3,
	MAX_NUM                                  = 4,
	ECV_FLIPBOOK_MAX                         = 5,
};
```


使用 NPC 贴图的战斗**不需要**在 `EnemyBattleAnimSet` 下额外创建条目，因为 NPC 通常已经拥有自己的战斗用精灵表（常以 `_B` 后缀结尾）。只需在 `EnemyType` 下指定 NPC 的 `CharaID`（见下文，也可视需要参阅 [如何添加自定义角色](customchars_en.md)）。运行时回退路径也证实了这一点：如果普通敌人类型无法加载敌人 Flipbook，`SetupEnemyFlipbook` 可能会改为针对该贴图索引尝试 `EnemyTypeID.m_NpcCharaID`。

#### ■ 运行时读取语义（字段级 Hex-Rays 证据）
`FEnemyBattleAnimSetBase` 是 32 字节行。复原出的复制运算符只复制行 ID 和一个 `TArray<int>` 大小的成员。后续所有战斗侧消费者都与此布局一致：该行以 `m_id` 为键，`+0x10` 处的数组被解释为 `m_EnemyTextureIDs`。

【主要 C 证据】

##### ◆ C-ANIM-01：复原出的结构体大小与被复制字段

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17024449-17024488
char __fastcall UScriptStruct::TCppStructOps<FEnemyBattleAnimSetBase>::Copy(
        UScriptStruct::TCppStructOps<FVillageBuildingInitialGroupBase> *this,
        TArray<FPackedNormal,TSizedDefaultAllocator<32> > *Dest,
        _BYTE *Src,
        int ArrayDim)
{
  ...
  do
  {
    v7 = *(int *)((char *)&v5->ArrayNum + v6);
    v8 = v5;
    v9 = (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)((char *)v5 + v6 + 16);
    v5 += 2;
    v8->ArrayNum = v7;
    TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v8 + 1, v9);
    --v4;
  }
  while ( v4 );
  return 1;
}
```

**解释。** 反编译器显示的 `TArray<FPackedNormal>` 类型是模板推断伪影，但内存操作本身并不含糊。该行复制会在偏移 `+0x08` 写入一个 32 位标量，然后复制从 `+0x10` 开始的 `TArray<unsigned int>`。在下文具名的运行时消费者中，该标量是 `m_id`，该数组是 `m_EnemyTextureIDs`。

##### ◆ C-ANIM-02：按 `m_id` 直接查找，32 字节行跨度

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645736-17645760
const FEnemyBattleAnimSetBase *__fastcall UEnemyDB::GetFlipbookSet(int AnimSetID)
{
  ...
  p_m_DataList = &v2->m_EnemyBattleAnimSet.m_DataList;
  result = (const FEnemyBattleAnimSetBase *)p_m_DataList->AllocatorInstance.Data;
  v5 = (const FEnemyBattleAnimSetBase *)&p_m_DataList->AllocatorInstance.Data[32 * p_m_DataList->ArrayNum];
  if ( result == v5 )
    return nullptr;
  while ( result->m_id != AnimSetID )
  {
    if ( ++result == v5 )
      return nullptr;
  }
  return result;
}
```

**解释。** `EnemyBattleAnimSet` 通过 `m_id` 相等比较解析；此路径不会进行标签查找。结束指针使用 `32 * ArrayNum`，因此行跨度精确为 32 字节。

##### ◆ C-ANIM-03：`EnemyTypeID.m_AnimationSetIDs[index]` 选择动画集行

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645431-17645478
const FEnemyBattleAnimSetBase *__fastcall UEnemyDB::GetEnemyTypeFlipbookSet(int EnemyTypeID, int index)
{
  ...
  EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(EnemyTypeID);
  ...
  v6 = *(const void **)&EnemyTypeDB[16];
  v7 = *(int *)&EnemyTypeDB[24];
  ...
  memcpy_0(animSetIDs.AllocatorInstance.Data, v6, 4 * v7);
  ...
  if ( (int)v3 >= 0 && (int)v3 < (int)v7 )
  {
    FlipbookSet = UEnemyDB::GetFlipbookSet(*(_DWORD *)&Data[4 * v3]);
    ...
    v2 = FlipbookSet;
  }
  ...
  return v2;
}
```

**解释。** `index`/`TextureIdx` 参数会从 `EnemyTypeID.m_AnimationSetIDs`（`+0x10` 指针与 `+0x18` 计数）中选择一个条目。它不是 `m_EnemyTextureIDs` 的索引。对改造而言，这意味着一个类型可以有多个动画集引用；外层槽位由 `EnemyTypeID` 决定，而内层贴图 ID 存在于 `EnemyBattleAnimSet` 中。

##### ◆ C-ANIM-04：动画集槽位数量即 `EnemyTypeID.m_AnimationSetIDs` 的计数

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645482-17645491
__int64 __fastcall UEnemyDB::GetEnemyTypeFlipbookSetNum(int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax

  if ( TypeID > 0 && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID)) != nullptr )
    return *(unsigned int *)&EnemyTypeDB[24];
  else
    return 0;
}
```

**解释。** 普通敌人的初始化会遍历这个计数，并为每个动画集槽位调用一次 `SetupEnemyFlipbook`。这进一步区分了 `EnemyTypeID.m_AnimationSetIDs` 与 `EnemyBattleAnimSet.m_EnemyTextureIDs`。

##### ◆ C-ANIM-05：批量取回会复制 `m_EnemyTextureIDs`，不作语义索引解释

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645401-17645427
TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetEnemyTypeFlipbookIDs(
        TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *result,
        int TypeID,
        int TextureIdx)
{
  ...
  EnemyTypeFlipbookSet = UEnemyDB::GetEnemyTypeFlipbookSet(TypeID, TextureIdx);
  ...
  ArrayNum = EnemyTypeFlipbookSet->m_EnemyTextureIDs.ArrayNum;
  ...
  memcpy_0(&result->AllocatorInstance.Data[4 * v7], v5->m_EnemyTextureIDs.AllocatorInstance.Data, 4 * ArrayNum);
  return result;
}
```

**解释。** 此路径将 `m_EnemyTextureIDs` 视为原始资源 ID 数组。此复制路径不会给数组索引附加动作含义。

##### ◆ C-ANIM-06：按动作查询是对 `EnemyTexID` 动作元数据的首个匹配扫描

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17642852-17642884
__int64 __fastcall UEnemyDB::GetActionTexID(int AnimSetID, EBATTLE_ACTION_ID ActionID)
{
  ...
  FlipbookSet = UEnemyDB::GetFlipbookSet(AnimSetID);
  ...
  Data = (unsigned int *)FlipbookSet->m_EnemyTextureIDs.AllocatorInstance.Data;
  v6 = &Data[FlipbookSet->m_EnemyTextureIDs.ArrayNum];
  ...
  while ( 1 )
  {
    v7 = *Data;
    v8 = *Data;
    actionTypeID = 0;
    AResourceManager::GetEnemyTexActionID(v4, &actionTypeID, v8);
    if ( actionTypeID == ActionID )
      break;
    if ( ++Data == v6 )
      return 0;
  }
  return v7;
}
```

**解释。** `m_EnemyTextureIDs` 的顺序只作为首个匹配的优先级而有意义。请求的动作会与由 `AResourceManager::GetEnemyTexActionID` 复原出的 `EnemyTexID.m_ActionTypeID` 匹配，而不是与数组索引匹配。

##### ◆ C-ANIM-07：`EnemyTexID` 提供动作 ID 与唯一材质标志

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17950826-17950856
char __fastcall AResourceManager::GetEnemyTexActionID(
        AResourceManager *this,
        EBATTLE_ACTION_ID *actionTypeID,
        unsigned int resourceID)
{
  ...
  v8 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[40 * ArrayIndex];
  ...
  *actionTypeID = *(EBATTLE_ACTION_ID *)(v8 + 16);
  return 1;
}

// Octopath_Traveler0-Win64-Shipping.exe.c:17950960-17950985
bool __fastcall AResourceManager::GetEnemyTexUseUniqueMaterialFlag(AResourceManager *this, unsigned int resourceID)
{
  ...
  if ( ... && (v6 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[40 * ArrayIndex]) != 0 )
    return *(_BYTE *)(v6 + 20);
  else
    return 0;
}
```

**解释。** 此处复原出的 `EnemyTexID` 行跨度为 40 字节。动作 ID 从行 `+0x10` 读取；唯一材质标志从行 `+0x14` 读取。因此，`EnemyBattleAnimSet` 本身并不编码待机/攻击语义；它引用的是携带这些语义的 `EnemyTexID` 行。

##### ◆ C-ANIM-08：加载路径构造以动作为键的 Flipbook 集

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17450453-17450519
ParamInt = UGameParamsUtility::GetParamInt(0x1B66u);
if ( TypeID < ParamInt )
  EnemyTypeFlipbookSet = UEnemyDB::GetEnemyTypeFlipbookSet(TypeID, TextureIdx);
else
  EnemyTypeFlipbookSet = UEnemyDB::GetEnemyPartsFlipbookID(TypeID - ParamInt);
...
Data = (unsigned int *)EnemyTypeFlipbookSet->m_EnemyTextureIDs.AllocatorInstance.Data;
v11 = &Data[EnemyTypeFlipbookSet->m_EnemyTextureIDs.ArrayNum];
...
Resource = AResourceManager::GetResource(AResourceManager::m_Self, 2u, v12);
...
actionID = 0;
AResourceManager::GetEnemyTexActionID(v13, &actionID, v12);
EnemyTexUseUniqueMaterialFlag = AResourceManager::GetEnemyTexUseUniqueMaterialFlag(v13, v12);
...
TSet<TTuple<enum EBATTLE_ACTION_ID,UPaperFlipbook *>,...>::Emplace(...);
TSet<TTuple<enum EActionBindType,bool>,...>::Emplace(...);
```

**解释。** 普通敌人类型与敌人部件最终都会汇入同一条 Flipbook 构造路径。每个 `EnemyTexID` 都会加载一个资源类别 `2` 对象，然后插入到以 `EBATTLE_ACTION_ID` 为键的 Flipbook 集中。部件类型 ID 通过加/减游戏参数 `0x1B66` 来区分。

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_id` | `result->m_id != AnimSetID`; 行跨度 `32` | `EnemyBattleAnimSet` 的主要运行时键。查找会按 ID 线性扫描已加载的数据列表。 | 已复原 | `C-ANIM-01`, `C-ANIM-02` |
| `m_EnemyTextureIDs` | `m_EnemyTextureIDs.ArrayNum`, `.AllocatorInstance.Data`; 行偏移 `+0x10` | `EnemyTexID.m_id` 值的 `TArray<int>`。批量路径把它作为原始 ID 复制；动作路径会扫描它，并使用 `EnemyTexID` 元数据来决定动作语义。 | 已复原 | `C-ANIM-05`, `C-ANIM-06`, `C-ANIM-07`, `C-ANIM-08` |
| `TextureIdx` / 动画集槽位 | `EnemyTypeID` `+0x10/+0x18`, `GetEnemyTypeFlipbookSetNum` 计数 | 选择 `EnemyTypeID.m_AnimationSetIDs` 的一个元素，然后将该值解析为 `EnemyBattleAnimSet.m_id`。 | 已复原 | `C-ANIM-03`, `C-ANIM-04` |

### ■ (**) `EnemyParts`
如果你的自定义 Boss 由若干视觉/机制部件组成（例如 Or'Galdera、Amatsukami no Orochi），请在此文件中指定各部件。某些 Boss（例如 Shadow of Twin Worlds）完全不使用此文件。C 导出显示，`EnemyParts` 不是独立的敌人主表。部件会使用从 `EnemyTypeID.m_PartsID` 选择的部件行，作为隶属于拥有者敌人的 `AQPBattleCharacterBase` 被实例化。

运行时，在构造拥有者敌人时会读取其 `EnemyTypeID.m_PartsID` 数组。每个正的部件 ID 都会通过 `UEnemyDB::GetEnemyPartsDB` 解析；如果该行存在，战斗管理器会为该部件创建战斗角色，分配 `PartsIndex`，通过 `m_PartsOwner` 链接回拥有者，并将其注册到拥有者/管理器侧的部件容器中。随后，部件的精灵会通过上文所述相同的 `EnemyBattleAnimSet` 机制加载，但部件 ID 会通过加上 `UGameParamsUtility::GetParamInt(0x1B66)` 映射到一个特殊的类型 ID 命名空间。
#### ■ 运行时读取语义
`FEnemyPartsBase` 是 56 字节行。导出只保留了一部分语义名称，因此本节会在 C 未保留原始字段标识符的位置使用行偏移。对改造而言重要的是，这些偏移并非无效填充：引擎会为了动画选择、放置、初始可见性、破防/选择视觉传播、条件显示变化以及淡入淡出行为读取它们。

【主要 C 证据】

##### ◆ C-PARTS-01：复原出的行大小与行成员复制顺序

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17096799-17096831
char __fastcall UScriptStruct::TCppStructOps<FEnemyPartsBase>::Copy(
        UScriptStruct::TCppStructOps<FEnemyPartsBase> *this,
        char *Dest,
        _BYTE *Src,
        int ArrayDim)
{
  ...
  do
  {
    v5 = *(_DWORD *)&Dest[v4 + 8];
    v6 = &Dest[v4];
    v7 = Dest;
    Dest += 56;
    *((_DWORD *)v7 + 2) = v5;
    *(_QWORD *)(v7 + 12) = *(_QWORD *)(v6 + 12);
    *((_DWORD *)v7 + 5) = *((_DWORD *)v6 + 5);
    *((_DWORD *)v7 + 6) = *((_DWORD *)v6 + 6);
    *((_DWORD *)v7 + 7) = *((_DWORD *)v6 + 7);
    *((_DWORD *)v7 + 8) = *((_DWORD *)v6 + 8);
    v7[36] = v6[36];
    v7[37] = v6[37];
    v7[38] = v6[38];
    *((_DWORD *)v7 + 10) = *((_DWORD *)v6 + 10);
    *((_DWORD *)v7 + 11) = *((_DWORD *)v6 + 11);
    *((_DWORD *)v7 + 12) = *((_DWORD *)v6 + 12);
    --ArrayDim;
  }
  while ( ArrayDim );
  return 1;
}
```

**解释。** 行跨度为 56 字节。被复制的槽位包括：`+0x08` ID，`+0x0C` 处重叠的 8 字节组合，`+0x14`、`+0x18`、`+0x1C`、`+0x20` 的标量槽位，`+0x24`、`+0x25`、`+0x26` 的字节，以及 `+0x28`、`+0x2C`、`+0x30` 的标量槽位。后续消费者会给这些槽位赋予含义。

##### ◆ C-PARTS-02：通过 `m_EnemyParts.m_DBAccess` 进行键查找

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644910-17644932
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyPartsDB(unsigned int PartsID)
{
  ...
  if ( ...
    && ADatabaseDefineStatic::m_Self->m_EnemyParts.m_Loaded
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_EnemyParts.m_DBAccess,
                       PartsID),
        ArrayIndex >= 0)
    && ArrayIndex < v2->m_EnemyParts.m_DataList.ArrayNum )
  {
    return &v2->m_EnemyParts.m_DataList.AllocatorInstance.Data[56 * ArrayIndex];
  }
  ...
}
```

**解释。** `PartsID` 由该表的 DB 访问辅助器解析。除非 DB 辅助器将它映射为密集数组索引，否则它不是密集数组索引。只有在表已加载且键解析结果在范围内时，才会返回该行。

##### ◆ C-PARTS-03：`+0x14` 是 `EnemyBattleAnimSet.m_id` 引用

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644936-17644964
const FEnemyBattleAnimSetBase *__fastcall UEnemyDB::GetEnemyPartsFlipbookID(int PartsID)
{
  ...
  v5 = (__int64)&v3->m_EnemyParts.m_DataList.AllocatorInstance.Data[56 * ArrayIndex];
  if ( v5 )
    return UEnemyDB::GetFlipbookSet(*(_DWORD *)(v5 + 20));
  ...
}
```

**解释。** 行偏移 `+0x14` 不是贴图 ID。它会被传给 `GetFlipbookSet`，因此是一个 `EnemyBattleAnimSet.m_id`。这是该部件的动画集引用。

##### ◆ C-PARTS-04：拥有者 `EnemyTypeID.m_PartsID` 枚举部件

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17480372-17480418
if ( (unsigned __int8)UEnemyDB::GetEnemyTypeID(&typeID, v29) )
{
  EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(typeID);
  if ( EnemyTypeDB )
  {
    v46 = *(unsigned int **)&EnemyTypeDB[192];
    v47 = 0;
    v48 = &v46[*(int *)&EnemyTypeDB[200]];
    if ( v46 != v48 )
    {
      do
      {
        v49 = *v46;
        if ( (int)*v46 > 0 && UEnemyDB::GetEnemyPartsDB(v49) )
        {
          ...
          AQPBattleManager::CreateEnemyParts(..., pEnemy, FormID, v47, isSkipDebugName);
          AQPBattleCharacterBase::AddPartsEnemy(v54, pEnemyParts);
```

**解释。** 拥有者类型行在 `+0xC0/+0xC8` 提供一个 `TArray<int>`，对应 `EnemyTypeID.m_PartsID`。每个非零条目都是一个部件 ID；循环索引会作为 `PartsIndex` 传入。因此，部件隶属于特定的拥有者/类型构造路径，而不是独立的遭遇成员。

##### ◆ C-PARTS-05：行偏移 `+0x18/+0x1C/+0x20` 会影响部件位置

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17480537-17480561
EnemyPartsDB = UEnemyDB::GetEnemyPartsDB(PartsID);
if ( EnemyPartsDB )
{
  pos.X = pos.X + (float)((float)*(int *)&EnemyPartsDB[32] * -0.1);
  pos.Y = pos.Y + *(float *)&EnemyPartsDB[24];
  pos.Z = pos.Z + *(float *)&EnemyPartsDB[28];
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17490064-17490075
EnemyPartsDB = UEnemyDB::GetEnemyPartsDB(enemyPartsID);
if ( EnemyPartsDB )
{
  partsPosition.Y = partsPosition.Y + (float)((float)*(int *)&EnemyPartsDB[32] * -0.1);
  partsPosition.Z = partsPosition.Z + *(float *)&EnemyPartsDB[24];
  v75 = v75 + *(float *)&EnemyPartsDB[28];
}
```

**解释。** 偏移 `+0x18` 与 `+0x1C` 会作为 浮点数 读取；偏移 `+0x20` 会作为整数读取并乘以 `-0.1`。两个调用点的局部向量命名不同，但二者都会消费相同的三个行槽位，以相对拥有者派生位置重新定位部件。

##### ◆ C-PARTS-06：设置流程会将部件链接到其拥有者，并读取初始可见性

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413194-17413249
EnemyPartsDB = UEnemyDB::GetEnemyPartsDB(PartsID);
if ( !EnemyPartsDB )
  return 0;
AQPBattleCharacterBase::SetEnemyFlag(this, 1);
...
ParamInt = UGameParamsUtility::GetParamInt(0x1B66u);
this->m_UseEnemyFlipbook |= AQPBattleCharacterBase::SetupEnemyFlipbook(this, 0, 0, PartsID + ParamInt);
AQPBattleCharacterBase::SetAnimVisible(this, *(_BYTE *)&EnemyPartsDB[36], 0, v14);
...
this->m_EnemyPartsFlag = 1;
...
this->m_PartsOwner = OwnerEnemy;
this->m_PartsID = PartsID;
this->m_PartsIndex = PartsIndex;
return 1;
```

**解释。** 偏移 `+0x24` 是初始可见标志。`m_PartsOwner`、`m_PartsID` 与 `m_PartsIndex` 是生成角色上的运行时状态字段，而不是数据表字段。`PartsID + ParamInt` 变换是 Flipbook 加载使用的判别器，用于将部件 ID 通过 `GetEnemyPartsFlipbookID` 路由。

##### ◆ C-PARTS-07：条件显示开/关使用行偏移 `+0x28` 与 `+0x2C`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17479093-17479149
EnemyPartsDB = UEnemyDB::GetEnemyPartsDB(*(_DWORD *)(v4 + 2760));
v6 = EnemyPartsDB;
if ( !EnemyPartsDB )
  continue;
if ( *(int *)&EnemyPartsDB[40] <= 0 )
{
  if ( *(int *)&EnemyPartsDB[44] <= 0 )
    continue;
  if ( !AQPBattleCharacterBase::IsDisplay((AQPBattleCharacterBase *)v4) )
    continue;
  BattleAbortDB = (const TArray<int,TSizedDefaultAllocator<32> > *)AQPBattleManager::GetBattleAbortDB(
                                                                   this,
                                                                   *(_DWORD *)&v6[44]);
  if ( !BattleAbortDB || (int)UBattleUtility::CheckConditions(...) <= 0 )
    continue;
  v8 = 0;
}
else
{
  if ( AQPBattleCharacterBase::IsDisplay((AQPBattleCharacterBase *)v4) )
    continue;
  v7 = (const TArray<int,TSizedDefaultAllocator<32> > *)AQPBattleManager::GetBattleAbortDB(this, *(_DWORD *)&v6[40]);
  if ( !v7 || (int)UBattleUtility::CheckConditions(...) <= 0 )
    continue;
  v8 = 1;
}
AQPBattleCharacterBase::RequestPartsFadeDisplay((AQPBattleCharacterBase *)v4, v8);
```

**解释。** `+0x28` 是显示开启条件 ID，只在部件隐藏时求值。`+0x2C` 是显示关闭条件 ID，只在部件可见时求值。二者都会被解析为战斗中止/条件包，并由 `UBattleUtility::CheckConditions` 求值；它们不是原始布尔值。

##### ◆ C-PARTS-08：淡出/抖动行为读取行偏移 `+0x30`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17429014-17429051
EnemyPartsDB = UEnemyDB::GetEnemyPartsDB(this->m_PartsID);
if ( EnemyPartsDB )
{
  v6 = *(float *)&EnemyPartsDB[48];
  IsDisplay = AQPBattleCharacterBase::IsDisplay(this);
  if ( IsOn )
  {
    if ( !IsDisplay )
    {
      AQPBattleCharacterBase::SetAnimVisible(this, v8, 0, v9);
      ...
      if ( v6 > 0.0 )
        m_AnimCharacter->RequestDitherFade(...);
    }
  }
  else if ( IsDisplay )
  {
    if ( v6 <= 0.0 )
      AQPBattleCharacterBase::SetAnimVisible(this, 0, 0, v9);
    else
      m_AnimCharacter->RequestDitherFade(...);
  }
}
```

**解释。** 偏移 `+0x30` 会作为控制淡出行为的 浮点数 读取。非正值允许立即隐藏；正值会将可见性变化路由到一次 dither fade 请求。

##### ◆ C-PARTS-09：破防/选择视觉传播使用字节 `+0x25` 与 `+0x26`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17490377-17490400
EnemyPartsDB = UEnemyDB::GetEnemyPartsDB(*(_DWORD *)(v9 + 2760));
if ( !EnemyPartsDB )
  goto LABEL_14;
if ( EnemyPartsDB[38] )
  break;
if ( EnemyPartsDB[37] )
{
  v11 = AQPBattleCharacterBase::SetAnimationPlayRate((AQPBattleCharacterBase *)v9, IsOn);
  goto LABEL_13;
}
...
v11 = AQPBattleCharacterBase::PartsSetShieldBreak((AQPBattleCharacterBase *)v9, IsOn);
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17490429-17490433
EnemyPartsDB = UEnemyDB::GetEnemyPartsDB(*(_DWORD *)(v7 + 2760));
if ( EnemyPartsDB )
{
  if ( EnemyPartsDB[38] )
    v4 |= AQPBattleCharacterBase::PartsSetSelectMaterial((AQPBattleCharacterBase *)v7, Select);
}
```

**解释。** 当字节 `+0x26` 未设置时，字节 `+0x25` 会控制一条播放速率/破防动画联动路径。字节 `+0x26` 控制部件专用的盾牌破防与选择材质行为。这些字节会被战斗状态的视觉传播直接消费。

**复原出的行偏移图：**

| 行位置 | 运行时解释 | 证据 |
|---|---|---|
| `+0x08` | `EnemyParts.m_id`，通过 `m_EnemyParts.m_DBAccess` 解析。 | `C-PARTS-01`, `C-PARTS-02` |
| `+0x0C/+0x10` | 作为 8 字节槽位复制，但所引用的玩法消费者没有为其分配安全的语义名称。 | `C-PARTS-01` |
| `+0x14` | 动画集 ID；会传给 `GetFlipbookSet`，因此引用 `EnemyBattleAnimSet.m_id`。 | `C-PARTS-03` |
| `+0x18` | 应用于部件放置的空间 浮点数。 | `C-PARTS-05` |
| `+0x1C` | 应用于部件放置的空间 浮点数。 | `C-PARTS-05` |
| `+0x20` | 乘以 `-0.1` 后应用于部件放置的空间整数。 | `C-PARTS-05` |
| `+0x24` | 初始动画可见标志。 | `C-PARTS-06` |
| `+0x25` | 破防/播放速率联动标志。 | `C-PARTS-09` |
| `+0x26` | 盾牌破防/选择材质联动标志。 | `C-PARTS-09` |
| `+0x28` | 显示开启条件 ID，仅在隐藏时求值。 | `C-PARTS-07` |
| `+0x2C` | 显示关闭条件 ID，仅在可见时求值。 | `C-PARTS-07` |
| `+0x30` | 可见性变化的淡出/dither 浮点数。 | `C-PARTS-08` |

### ■ (****) `EnemyTypeID`
此资产基本上是 OT0 中敌人原型的主“战斗精灵呈现描述符”；它告诉游戏：
- 要使用哪些动画集，或者改为关联哪个 NPC/角色精灵表（`m_AnimationSetIDs` 以 `EnemyBattleAnimSet` 的 `m_id` 为输入，`m_NpcCharaID` 以 `Character/` 下 `CharaID` 的 `m_id` 为输入）
- 如何缩放并定位精灵与所有叠加层：
  - 命中特效火花（`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`）
  - 盾牌与弱点叠加层
  - 敌人特效（`m_EffectOffsetX`,`m_EffectOffsetY`，以及 `m_EffectFromBottomOffsetY`：相对于精灵底部的偏移，常用于地面类特效）
  - 伤害数字（`m_DamageOffsetX`,`m_DamageOffsetY`）
  - 异常状态（`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`）
  - 光标（`m_CursorOffsetX`,`m_CursorOffsetY`）
- 哪些敌人部件要与该敌人原型关联（`m_PartsID`）
- 该敌人类型要使用哪些声音/语音：
  - `m_CallSE`（即“call sound 效果”，呼叫音效）
  - `m_VoiceSymbol`（一个可能索引到 `Sound` 下 `VoiceSymbolList` 的整数）
  - `m_CueSheet`（可能索引到 `Sound/` 下 `SoundSheetList`，并选择该敌人语音集要使用的 cuesheet）
- 应生成哪些附加特效（VFX）以及生成位置（`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`）
- 被引用贴图的哪个位置（通常是 Flipbook 资产/角色精灵表的第一帧）应用于行动顺序图标（`m_OrderTexUS`, `m_OrderTexVS`；大型图标，即指定角色正在行动时，使用 `m_OrderTexUL` 与 `m_OrderTexVL`），以及被引用贴图在行动顺序图标中应以什么比例渲染（`m_OrderScaleS`; `m_OrderScaleL`）。例如，Shadow of Twin Worlds 战斗中的 Shadow 使用很小的顺序图标缩放，以确保其整个身体都出现在顺序图标上。
#### ■ 其他运行时相关字段
  - `m_OverrideOrderIconAnimSetID`：当你想为行动顺序图标使用特殊贴图资产时使用
  - `m_AttachEffectInGodbeastProduction`：实体化为行字节 `+0xA5`；在所引用的 C 路径中未复原出直接的玩法/UI 消费者，因此它的语义作用弱于战斗/场地附加特效字段。
  - `m_AddStartUV`：实体化为行字节 `+0xA4`；在所引用的 C 路径中未复原出直接消费者，因此应将其视为语义未解析的活字段。
  - `m_KeepVisibleAttachEffectOnMotion`：作为 `FEnemyTypeIDBase` 的一部分被实体化，并复原为行字节 `+0xA6`；后文 `EnemyTypeID` 偏移图将其与 `m_AddStartUV` 和 `m_AttachEffectInGodbeastProduction` 分开分类。应将确切视觉行为视为具有部分消费者证据的运行时字段，而不是导出器噪声。
  - `m_LinkOwnerAnim`：实体化为行字节 `+0xD0`；C 导出证明它会保留到 `EnemyTypeID` 行中，但确切的下游消费者仍只被部分复原。
  - `m_DefeatEffect`：死亡时播放的特效 ID（例如 Bestower of All 的特殊击败特效）。`Kingship_structs.hpp` 将它们指定如下：

```hpp
enum class EENEMY_DEFEATEFFECT : uint8
{
	NONE                                     = 0,
	EDE_AGM_CHP8_02                          = 1,
	EDE_AGM_CHP8_03_RING                     = 2,
	EENEMY_MAX                               = 3,
};
```

       
- `m_ChangeColorID`：指向颜色变化表的 ID，可能与 `Character/` 下的 `CharaColorChangeParams` 相关。OT0 中仅在 Zero/the Ringbearer Chosen 面对其影子时使用（`ChangeColorID` 6）。CotC 的 `EnemyType` 完全没有该键。SDK 包含以下可能相关的枚举：

```hpp
// Enum Kingship.ECHARA_COLOR_CHANGE_ID
// NumValues: 0x0008
enum class ECHARA_COLOR_CHANGE_ID : uint8
{
	eCOLOR_CHANGE_NONE                       = 0,
	eCOLOR_CHANGE_LIMBO_AUTO                 = 1,
	eCOLOR_CHANGE_LIMBO_DEFAULT              = 2,
	eCOLOR_CHANGE_LIMBO_RED                  = 3,
	eCOLOR_CHANGE_LIMBO_YELLOW               = 4,
	eCOLOR_CHANGE_LIMBO_BLUE                 = 5,
	eCOLOR_CHANGE_MIRROR_BATTLE              = 6,
	eCOLOR_CHANGE_MAX                        = 7,
};
```
#### ■ 运行时读取语义（字段级 Hex-Rays 证据）
##### ◆ C-TYPE-01：EnemyTypeID 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645336-17645405
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyTypeDB(unsigned int TypeID)
{
  __int64 v2; // rbx
  bool IsValidLowLevelFast; // al
  ADatabaseDefineStatic *v4; // rdi
  TArray<FEnemyTypeIDBase,TSizedDefaultAllocator<32> > *p_m_DataList; // rdi
  int ArrayNum; // edx
  bool v7; // al
  ADatabaseDefineStatic *v8; // rcx
  int ArrayIndex; // eax

  v2 = 0;
  if ( UEnemyDB::CheckEnemyDB() )
  {
    if ( !ADatabaseDefineStatic::m_Self
      || (IsValidLowLevelFast = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
          v4 = ADatabaseDefineStatic::m_Self,
          !IsValidLowLevelFast) )
    {
      v4 = nullptr;
    }
    p_m_DataList = &v4->m_EnemyTypeID.m_DataList;
    ArrayNum = p_m_DataList->ArrayNum;
    if ( UEnemyDB::s_EnemyIndices.ArrayMax != ArrayNum )
    {
      if ( ArrayNum < 0 )
        UE::Core::Private::OnInvalidArrayNum((unsigned int)p_m_DataList->ArrayNum);
      UEnemyDB::s_EnemyIndices.ArrayNum = 0;
      if ( ArrayNum > UEnemyDB::s_EnemyIndices.ArrayMax )
        TArray<long,TSizedDefaultAllocator<32>>::ResizeTo(
          (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&UEnemyDB::s_EnemyIndices,
          ArrayNum);
    }
    if ( p_m_DataList && UEnemyDB::CheckEnemyDB() )
    {
      if ( !ADatabaseDefineStatic::m_Self
        || (v7 = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
            v8 = ADatabaseDefineStatic::m_Self,
            !v7) )
      {
        v8 = nullptr;
      }
      ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                     (FDbAccessHelper<FNoteListBase> *)&v8->m_EnemyTypeID.m_DBAccess,
                     TypeID);
      if ( ArrayIndex >= 0 && ArrayIndex < p_m_DataList->ArrayNum )
        return &p_m_DataList->AllocatorInstance.Data[216 * ArrayIndex];
    }
  }
  return (FScriptContainerElement *)v2;
}
// 1446059F7: control flows out of bounds to 1446059FC

//----- (0000000144605A00) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyTypeDefeatEffectID(int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax

  if ( TypeID > 0 && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID)) != nullptr )
    return *(unsigned int *)&EnemyTypeDB[212];
  else
    return 0;
}

//----- (0000000144605A30) ----------------------------------------------------
TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetEnemyTypeFlipbookIDs(
        TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *result,
        int TypeID,
        int TextureIdx)
{
```

##### ◆ C-TYPE-02：EnemyTypeID 实体化字段复制

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17041811-17041912
FEnemyTypeIDBase *__fastcall FEnemyTypeIDBase::FEnemyTypeIDBase(FEnemyTypeIDBase *this, const FEnemyTypeIDBase *__that)
{
  __int64 ArrayNum; // rbp
  FScriptContainerElement *Data; // r14
  __int64 v6; // rbp
  FScriptContainerElement *v7; // r14
  __int64 v8; // rbp
  FScriptContainerElement *v9; // r14

  this->__vftable = (FEnemyTypeIDBase_vtbl *)FEnemyTypeIDBase::`vftable';
  this->m_id = __that->m_id;
  this->m_AnimationSetIDs.AllocatorInstance.Data = nullptr;
  ArrayNum = __that->m_AnimationSetIDs.ArrayNum;
  Data = __that->m_AnimationSetIDs.AllocatorInstance.Data;
  this->m_AnimationSetIDs.ArrayNum = ArrayNum;
  if ( (_DWORD)ArrayNum )
  {
    TArray<FVertexInstanceID,TSizedDefaultAllocator<32>>::ResizeForCopy(
      (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_AnimationSetIDs,
      ArrayNum,
      0);
    memcpy_0(this->m_AnimationSetIDs.AllocatorInstance.Data, Data, 4 * ArrayNum);
  }
  else
  {
    this->m_AnimationSetIDs.ArrayMax = 0;
  }
  this->m_NpcCharaID.AllocatorInstance.Data = nullptr;
  v6 = __that->m_NpcCharaID.ArrayNum;
  v7 = __that->m_NpcCharaID.AllocatorInstance.Data;
  this->m_NpcCharaID.ArrayNum = v6;
  if ( (_DWORD)v6 )
  {
    TArray<FVertexInstanceID,TSizedDefaultAllocator<32>>::ResizeForCopy(
      (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_NpcCharaID,
      v6,
      0);
    memcpy_0(this->m_NpcCharaID.AllocatorInstance.Data, v7, 4 * v6);
  }
  else
  {
    this->m_NpcCharaID.ArrayMax = 0;
  }
  this->m_ChangeColorID = __that->m_ChangeColorID;
  this->m_ScaleX = __that->m_ScaleX;
  this->m_ScaleY = __that->m_ScaleY;
  this->m_HitPosX = __that->m_HitPosX;
  this->m_HitPosY = __that->m_HitPosY;
  this->m_HitScaleX = __that->m_HitScaleX;
  this->m_HitScaleY = __that->m_HitScaleY;
  this->m_Size = __that->m_Size;
  this->m_EffectOffsetX = __that->m_EffectOffsetX;
  this->m_EffectOffsetY = __that->m_EffectOffsetY;
  this->m_EffectFromBottomOffsetY = __that->m_EffectFromBottomOffsetY;
  this->m_AilmentInfoOffsetX = __that->m_AilmentInfoOffsetX;
  this->m_AilmentOffset = __that->m_AilmentOffset;
  this->m_DamageOffsetX = __that->m_DamageOffsetX;
  this->m_DamageOffsetY = __that->m_DamageOffsetY;
  this->m_CursorOffsetX = __that->m_CursorOffsetX;
  this->m_CursorOffsetY = __that->m_CursorOffsetY;
  this->m_SupportOffsetY = __that->m_SupportOffsetY;
  this->m_SupportOffsetX = __that->m_SupportOffsetX;
  this->m_CallSE = __that->m_CallSE;
  this->m_VoiceSymbol = __that->m_VoiceSymbol;
  this->m_CueSheet = __that->m_CueSheet;
  this->m_OverrideOrderIconAnimSetID = __that->m_OverrideOrderIconAnimSetID;
  this->m_OrderTexUS = __that->m_OrderTexUS;
  this->m_OrderTexVS = __that->m_OrderTexVS;
  this->m_OrderScaleS = __that->m_OrderScaleS;
  this->m_OrderTexUL = __that->m_OrderTexUL;
  this->m_OrderTexVL = __that->m_OrderTexVL;
  this->m_OrderScaleL = __that->m_OrderScaleL;
  this->m_AddStartUV = __that->m_AddStartUV;
  this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;
  this->m_KeepVisibleAttachEffectOnMotion = __that->m_KeepVisibleAttachEffectOnMotion;
  this->m_AttachEffect = __that->m_AttachEffect;
  this->m_BattleAttachEffectOffsetX = __that->m_BattleAttachEffectOffsetX;
  this->m_BattleAttachEffectOffsetY = __that->m_BattleAttachEffectOffsetY;
  this->m_FieldAttachEffect = __that->m_FieldAttachEffect;
  this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;
  this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;
  this->m_PartsID.AllocatorInstance.Data = nullptr;
  v8 = __that->m_PartsID.ArrayNum;
  v9 = __that->m_PartsID.AllocatorInstance.Data;
  this->m_PartsID.ArrayNum = v8;
  if ( (_DWORD)v8 )
  {
    TArray<FVertexInstanceID,TSizedDefaultAllocator<32>>::ResizeForCopy(
      (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_PartsID,
      v8,
      0);
    memcpy_0(this->m_PartsID.AllocatorInstance.Data, v9, 4 * v8);
  }
  else
  {
    this->m_PartsID.ArrayMax = 0;
  }
  this->m_LinkOwnerAnim = __that->m_LinkOwnerAnim;
  this->m_DefeatEffect = __that->m_DefeatEffect;
  return this;
}

```

##### ◆ C-TYPE-03：EnemyID 到 EnemyTypeID 以及所选 EnemyTypeID 消费者

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645293-17645660
//----- (0000000144605880) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyTypeCallSE(int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax

  if ( TypeID > 0 && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID)) != nullptr )
    return *(unsigned int *)&EnemyTypeDB[124];
  else
    return 0;
}

//----- (00000001446058B0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyTypeCharaID(int TypeID, int TextureIdx)
{
  __int64 v2; // rbx
  FScriptContainerElement *EnemyTypeDB; // rax

  v2 = TextureIdx;
  if ( TypeID > 0
    && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID)) != nullptr
    && (int)v2 >= 0
    && (int)v2 < *(_DWORD *)&EnemyTypeDB[40] )
  {
    return *(unsigned int *)(*(_QWORD *)&EnemyTypeDB[32] + 4 * v2);
  }
  else
  {
    return 0;
  }
}

//----- (00000001446058F0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyTypeColorID(int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax

  if ( TypeID > 0 && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID)) != nullptr )
    return *(unsigned int *)&EnemyTypeDB[48];
  else
    return 0;
}

//----- (0000000144605920) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyTypeDB(unsigned int TypeID)
{
  __int64 v2; // rbx
  bool IsValidLowLevelFast; // al
  ADatabaseDefineStatic *v4; // rdi
  TArray<FEnemyTypeIDBase,TSizedDefaultAllocator<32> > *p_m_DataList; // rdi
  int ArrayNum; // edx
  bool v7; // al
  ADatabaseDefineStatic *v8; // rcx
  int ArrayIndex; // eax

  v2 = 0;
  if ( UEnemyDB::CheckEnemyDB() )
  {
    if ( !ADatabaseDefineStatic::m_Self
      || (IsValidLowLevelFast = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
          v4 = ADatabaseDefineStatic::m_Self,
          !IsValidLowLevelFast) )
    {
      v4 = nullptr;
    }
    p_m_DataList = &v4->m_EnemyTypeID.m_DataList;
    ArrayNum = p_m_DataList->ArrayNum;
    if ( UEnemyDB::s_EnemyIndices.ArrayMax != ArrayNum )
    {
      if ( ArrayNum < 0 )
        UE::Core::Private::OnInvalidArrayNum((unsigned int)p_m_DataList->ArrayNum);
      UEnemyDB::s_EnemyIndices.ArrayNum = 0;
      if ( ArrayNum > UEnemyDB::s_EnemyIndices.ArrayMax )
        TArray<long,TSizedDefaultAllocator<32>>::ResizeTo(
          (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&UEnemyDB::s_EnemyIndices,
          ArrayNum);
    }
    if ( p_m_DataList && UEnemyDB::CheckEnemyDB() )
    {
      if ( !ADatabaseDefineStatic::m_Self
        || (v7 = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
            v8 = ADatabaseDefineStatic::m_Self,
            !v7) )
      {
        v8 = nullptr;
      }
      ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                     (FDbAccessHelper<FNoteListBase> *)&v8->m_EnemyTypeID.m_DBAccess,
                     TypeID);
      if ( ArrayIndex >= 0 && ArrayIndex < p_m_DataList->ArrayNum )
        return &p_m_DataList->AllocatorInstance.Data[216 * ArrayIndex];
    }
  }
  return (FScriptContainerElement *)v2;
}
// 1446059F7: control flows out of bounds to 1446059FC

//----- (0000000144605A00) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyTypeDefeatEffectID(int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax

  if ( TypeID > 0 && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID)) != nullptr )
    return *(unsigned int *)&EnemyTypeDB[212];
  else
    return 0;
}

//----- (0000000144605A30) ----------------------------------------------------
TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetEnemyTypeFlipbookIDs(
        TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *result,
        int TypeID,
        int TextureIdx)
{
  const FEnemyBattleAnimSetBase *EnemyTypeFlipbookSet; // rax
  const FEnemyBattleAnimSetBase *v5; // rbp
  __int64 ArrayNum; // rdi
  __int64 v7; // rsi

  result->AllocatorInstance.Data = nullptr;
  *(_QWORD *)&result->ArrayNum = 0;
  if ( TypeID <= 0 )
    return result;
  EnemyTypeFlipbookSet = UEnemyDB::GetEnemyTypeFlipbookSet(TypeID, TextureIdx);
  v5 = EnemyTypeFlipbookSet;
  if ( !EnemyTypeFlipbookSet )
    return result;
  ArrayNum = EnemyTypeFlipbookSet->m_EnemyTextureIDs.ArrayNum;
  if ( !(_DWORD)ArrayNum )
    return result;
  v7 = result->ArrayNum;
  result->ArrayNum = v7 + ArrayNum;
  if ( (unsigned int)ArrayNum > result->ArrayMax - (int)v7 )
    TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(result, v7);
  memcpy_0(&result->AllocatorInstance.Data[4 * v7], v5->m_EnemyTextureIDs.AllocatorInstance.Data, 4 * ArrayNum);
  return result;
}

//----- (0000000144605AF0) ----------------------------------------------------
const FEnemyBattleAnimSetBase *__fastcall UEnemyDB::GetEnemyTypeFlipbookSet(int EnemyTypeID, int index)
{
  const FEnemyBattleAnimSetBase *v2; // rbx
  __int64 v3; // rsi
  FScriptContainerElement *EnemyTypeDB; // rax
  FScriptContainerElement *Data; // rcx
  const void *v6; // rbp
  __int64 v7; // rdi
  const FEnemyBattleAnimSetBase *FlipbookSet; // rax
  TArray<int,TSizedDefaultAllocator<32> > animSetIDs; // [rsp+20h] [rbp-18h] BYREF

  v2 = nullptr;
  v3 = index;
  if ( EnemyTypeID > 0 )
  {
    EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(EnemyTypeID);
    if ( EnemyTypeDB )
    {
      Data = nullptr;
      v6 = *(const void **)&EnemyTypeDB[16];
      v7 = *(int *)&EnemyTypeDB[24];
      animSetIDs.AllocatorInstance.Data = nullptr;
      animSetIDs.ArrayNum = v7;
      if ( (_DWORD)v7 )
      {
        TArray<FVertexInstanceID,TSizedDefaultAllocator<32>>::ResizeForCopy(
          (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&animSetIDs,
          v7,
          0);
        memcpy_0(animSetIDs.AllocatorInstance.Data, v6, 4 * v7);
        Data = animSetIDs.AllocatorInstance.Data;
        LODWORD(v7) = animSetIDs.ArrayNum;
      }
      else
      {
        animSetIDs.ArrayMax = 0;
      }
      if ( (int)v3 >= 0 && (int)v3 < (int)v7 )
      {
        FlipbookSet = UEnemyDB::GetFlipbookSet(*(_DWORD *)&Data[4 * v3]);
        Data = animSetIDs.AllocatorInstance.Data;
        v2 = FlipbookSet;
      }
      if ( Data )
        FMemory::Free(Data);
    }
  }
  return v2;
}

//----- (0000000144605BA0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyTypeFlipbookSetNum(int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax

  if ( TypeID > 0 && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID)) != nullptr )
    return *(unsigned int *)&EnemyTypeDB[24];
  else
    return 0;
}

//----- (0000000144605BD0) ----------------------------------------------------
bool __fastcall UEnemyDB::GetEnemyTypeHitOffset(UE::Math::TVector2<double> *Offset, int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax

  LOBYTE(EnemyTypeDB) = 0;
  *Offset = 0u;
  if ( TypeID > 0 )
  {
    EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID);
    if ( EnemyTypeDB )
    {
      Offset->X = *(float *)&EnemyTypeDB[60];
      Offset->Y = *(float *)&EnemyTypeDB[64];
      LOBYTE(EnemyTypeDB) = 1;
    }
  }
  return (char)EnemyTypeDB;
}

//----- (0000000144605C20) ----------------------------------------------------
char __fastcall UEnemyDB::GetEnemyTypeHitScale(UE::Math::TVector2<double> *Scale, int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax
  float v4; // xmm1_4

  Scale->Y = 1.0;
  Scale->X = 1.0;
  if ( TypeID <= 0 )
    return 0;
  EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID);
  if ( !EnemyTypeDB )
    return 0;
  Scale->X = *(float *)&EnemyTypeDB[68];
  v4 = *(float *)&EnemyTypeDB[72];
  Scale->Y = v4;
  return 1;
}

//----- (0000000144605C80) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyTypeID(int *TypeID, unsigned int EnemyID)
{
  FScriptContainerElement *result; // rax
  int v4; // eax

  *TypeID = 0;
  result = UEnemyDB::GetEnemyDB(EnemyID);
  if ( result )
  {
    v4 = *(_DWORD *)&result[24];
    if ( v4 > 0 )
      *TypeID = v4;
    return (FScriptContainerElement *)(v4 > 0);
  }
  return result;
}

//----- (0000000144605CC0) ----------------------------------------------------
bool __fastcall UEnemyDB::GetEnemyTypeNameString(FString *NameStr, int TypeID, bool withID, int EnemyID)
{
  FScriptContainerElement *Data; // rcx
  const wchar_t *v9; // rdx
  FString str; // [rsp+20h] [rbp-28h] BYREF
  char v12; // [rsp+30h] [rbp-18h] BYREF

  if ( NameStr != (FString *)&v12 )
  {
    Data = NameStr->Data.AllocatorInstance.Data;
    if ( Data )
      FMemory::Free(Data);
    NameStr->Data.AllocatorInstance.Data = nullptr;
    *(_QWORD *)&NameStr->Data.ArrayNum = 0;
  }
  UEnemyDB::GetEnemyTypeDB(TypeID);
  if ( withID )
  {
    v9 = L"T#";
    if ( EnemyID > 0 )
      v9 = L"E#";
    FString::FString(&str, v9);
    if ( EnemyID > 0 )
      TypeID = EnemyID;
    FString::AppendInt(&str, TypeID);
    FString::AppendChar(&str, 0x3Au);
    FString::InsertAt(NameStr, 0, &str);
    if ( str.Data.AllocatorInstance.Data )
      FMemory::Free(str.Data.AllocatorInstance.Data);
  }
  return NameStr->Data.ArrayNum > 1;
}
// 1472D186C: using guessed type wchar_t aT_1[3];

//----- (0000000144605D90) ----------------------------------------------------
_BOOL8 __fastcall UEnemyDB::GetEnemyTypeNameText(FText *NameText, unsigned int TypeID, bool withID, int EnemyID)
{
  int v7; // esi
  const wchar_t *v8; // rdx
  bool v9; // bl
  FText *v10; // rax
  FText *v11; // rax
  FString str; // [rsp+20h] [rbp-38h] BYREF
  FString Characters; // [rsp+30h] [rbp-28h] BYREF
  FText v15; // [rsp+40h] [rbp-18h] BYREF

  str.Data.AllocatorInstance.Data = nullptr;
  *(_QWORD *)&str.Data.ArrayNum = 0;
  v7 = TypeID;
  UEnemyDB::GetEnemyTypeDB(TypeID);
  if ( withID )
  {
    v8 = L"T#";
    if ( EnemyID > 0 )
      v8 = L"E#";
    FString::FString(&Characters, v8);
    if ( EnemyID > 0 )
      v7 = EnemyID;
    FString::AppendInt(&Characters, v7);
    FString::AppendChar(&Characters, 0x3Au);
    FString::InsertAt(&str, 0, &Characters);
    if ( Characters.Data.AllocatorInstance.Data )
      FMemory::Free(Characters.Data.AllocatorInstance.Data);
  }
  v9 = str.Data.ArrayNum > 1;
  if ( str.Data.ArrayNum <= 1 )
  {
    v11 = FText::FText(&v15);
    FText::operator=(NameText, v11);
    if ( v15.TextData.Reference )
      v15.TextData.Reference->Release(v15.TextData.Reference);
  }
  else
  {
    v10 = FText::FromString((FText *)&Characters, &str);
    FText::operator=(NameText, v10);
    if ( Characters.Data.AllocatorInstance.Data )
      (*(void (__fastcall **)(FScriptContainerElement *))(*(_QWORD *)Characters.Data.AllocatorInstance.Data + 16LL))(Characters.Data.AllocatorInstance.Data);
  }
  if ( str.Data.AllocatorInstance.Data )
    FMemory::Free(str.Data.AllocatorInstance.Data);
  return v9;
}
// 1472D186C: using guessed type wchar_t aT_1[3];

//----- (0000000144605EB0) ----------------------------------------------------
char __fastcall UEnemyDB::GetEnemyTypeScale(UE::Math::TVector2<double> *Scale, int TypeID)
{
  FScriptContainerElement *EnemyTypeDB; // rax
  float v4; // xmm1_4

  if ( TypeID <= 0 )
    return 0;
  EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(TypeID);
  if ( !EnemyTypeDB )
    return 0;
  Scale->X = *(float *)&EnemyTypeDB[52];
  v4 = *(float *)&EnemyTypeDB[56];
  Scale->Y = v4;
  return 1;
}

//----- (0000000144605F00) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyTypeSize(unsigned int TypeID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyTypeDB(TypeID);
  if ( result )
    return (FScriptContainerElement *)(unsigned __int8)result[76];
  return result;
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_AddStartUV` | `C-TYPE-02:17041883` `this->m_AddStartUV = __that->m_AddStartUV;` | 该键会作为行偏移 `+0xA4` 处的 字节/布尔 槽位读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentInfoOffsetX` | `C-TYPE-02:17041865` `this->m_AilmentInfoOffsetX = __that->m_AilmentInfoOffsetX;` | 该键会作为行偏移 `+0x5C` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentOffset` | `C-TYPE-02:17041866` `this->m_AilmentOffset = __that->m_AilmentOffset;` | 该键会作为行偏移 `+0x60` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AnimationSetIDs` | `C-TYPE-02:17041822` `this->m_AnimationSetIDs.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041823` `ArrayNum = __that->m_AnimationSetIDs.ArrayNum;`<br>`C-TYPE-02:17041824` `Data = __that->m_AnimationSetIDs.AllocatorInstance.Data;` 另有 4 处局部出现 | 该键会作为行偏移 `+0x10` 处的 `TArray` 头读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffect` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;`<br>`C-TYPE-02:17041886` `this->m_AttachEffect = __that->m_AttachEffect;` | 该键会作为行偏移 `+0xA8` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffectInGodbeastProduction` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;` | 该键会作为行偏移 `+0xA5` 处的 字节/布尔 槽位读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetX` | `C-TYPE-02:17041887` `this->m_BattleAttachEffectOffsetX = __that->m_BattleAttachEffectOffsetX;` | 该键会作为行偏移 `+0xAC` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetY` | `C-TYPE-02:17041888` `this->m_BattleAttachEffectOffsetY = __that->m_BattleAttachEffectOffsetY;` | 该键会作为行偏移 `+0xB0` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | 该键会作为行偏移 `+0x7C` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | 该键会作为行偏移 `+0x30` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CueSheet` | `C-TYPE-02:17041875` `this->m_CueSheet = __that->m_CueSheet;` | 该键会作为行偏移 `+0x84` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetX` | `C-TYPE-02:17041869` `this->m_CursorOffsetX = __that->m_CursorOffsetX;` | 该键会作为行偏移 `+0x6C` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetY` | `C-TYPE-02:17041870` `this->m_CursorOffsetY = __that->m_CursorOffsetY;` | 该键会作为行偏移 `+0x70` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetX` | `C-TYPE-02:17041867` `this->m_DamageOffsetX = __that->m_DamageOffsetX;` | 该键会作为行偏移 `+0x64` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetY` | `C-TYPE-02:17041868` `this->m_DamageOffsetY = __that->m_DamageOffsetY;` | 该键会作为行偏移 `+0x68` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_DefeatEffect` | `C-TYPE-02:17041909` `this->m_DefeatEffect = __that->m_DefeatEffect;` | 该键会作为行偏移 `+0xD4` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectFromBottomOffsetY` | `C-TYPE-02:17041864` `this->m_EffectFromBottomOffsetY = __that->m_EffectFromBottomOffsetY;` | 该键会作为行偏移 `+0x58` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetX` | `C-TYPE-02:17041862` `this->m_EffectOffsetX = __that->m_EffectOffsetX;` | 该键会作为行偏移 `+0x50` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetY` | `C-TYPE-02:17041863` `this->m_EffectOffsetY = __that->m_EffectOffsetY;` | 该键会作为行偏移 `+0x54` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffect` | `C-TYPE-02:17041889` `this->m_FieldAttachEffect = __that->m_FieldAttachEffect;`<br>`C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;`<br>`C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | 该键会作为行偏移 `+0xB4` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetX` | `C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;` | 该键会作为行偏移 `+0xB8` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetY` | `C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | 该键会作为行偏移 `+0xBC` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosX` | `C-TYPE-02:17041857` `this->m_HitPosX = __that->m_HitPosX;` | 该键会作为行偏移 `+0x3C` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosY` | `C-TYPE-02:17041858` `this->m_HitPosY = __that->m_HitPosY;` | 该键会作为行偏移 `+0x40` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleX` | `C-TYPE-02:17041859` `this->m_HitScaleX = __that->m_HitScaleX;` | 该键会作为行偏移 `+0x44` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleY` | `C-TYPE-02:17041860` `this->m_HitScaleY = __that->m_HitScaleY;` | 该键会作为行偏移 `+0x48` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_KeepVisibleAttachEffectOnMotion` | `C-TYPE-02:17041885` `this->m_KeepVisibleAttachEffectOnMotion = __that->m_KeepVisibleAttachEffectOnMotion;` | 该键会作为行偏移 `+0xA6` 处的 字节/布尔 槽位读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_LinkOwnerAnim` | `C-TYPE-02:17041908` `this->m_LinkOwnerAnim = __that->m_LinkOwnerAnim;` | 该键会作为行偏移 `+0xD0` 处的 字节/布尔 槽位读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_NpcCharaID` | `C-TYPE-02:17041838` `this->m_NpcCharaID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041839` `v6 = __that->m_NpcCharaID.ArrayNum;`<br>`C-TYPE-02:17041840` `v7 = __that->m_NpcCharaID.AllocatorInstance.Data;` 另有 4 处局部出现 | 该键会作为行偏移 `+0x20` 处的 `TArray` 头读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleL` | `C-TYPE-02:17041882` `this->m_OrderScaleL = __that->m_OrderScaleL;` | 该键会作为行偏移 `+0xA0` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleS` | `C-TYPE-02:17041879` `this->m_OrderScaleS = __that->m_OrderScaleS;` | 该键会作为行偏移 `+0x94` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUL` | `C-TYPE-02:17041880` `this->m_OrderTexUL = __that->m_OrderTexUL;` | 该键会作为行偏移 `+0x98` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUS` | `C-TYPE-02:17041877` `this->m_OrderTexUS = __that->m_OrderTexUS;` | 该键会作为行偏移 `+0x8C` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVL` | `C-TYPE-02:17041881` `this->m_OrderTexVL = __that->m_OrderTexVL;` | 该键会作为行偏移 `+0x9C` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVS` | `C-TYPE-02:17041878` `this->m_OrderTexVS = __that->m_OrderTexVS;` | 该键会作为行偏移 `+0x90` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OverrideOrderIconAnimSetID` | `C-TYPE-02:17041876` `this->m_OverrideOrderIconAnimSetID = __that->m_OverrideOrderIconAnimSetID;` | 该键会作为行偏移 `+0x88` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_PartsID` | `C-TYPE-02:17041892` `this->m_PartsID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041893` `v8 = __that->m_PartsID.ArrayNum;`<br>`C-TYPE-02:17041894` `v9 = __that->m_PartsID.AllocatorInstance.Data;` 另有 4 处局部出现 | 该键会作为行偏移 `+0xC0` 处的 `TArray` 头读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetX` | `C-TYPE-02:17041872` `this->m_SupportOffsetX = __that->m_SupportOffsetX;` | 该键会作为行偏移 `+0x78` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetY` | `C-TYPE-02:17041871` `this->m_SupportOffsetY = __that->m_SupportOffsetY;` | 该键会作为行偏移 `+0x74` 处的 32 位 浮点数 读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | 该键会作为行偏移 `+0x80` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_id` | `C-TYPE-02:17041821` `this->m_id = __that->m_id;` | 该键会作为行偏移 `+0x08` 处的 32 位标量读取。行本身通过 `UEnemyDB::GetEnemyTypeDB(TypeID)` 解析，其跨度为 `0xD8` 字节。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |

#### ■ 其他运行时证据：次级 `EnemyTypeID` 消费者

若干视觉/UI 系统不会实体化完整的 `FEnemyTypeIDBase` 行。相反，它们会经由小型 取值函数 函数进入，这些函数首先解析 `EnemyID.m_TypeID`，随后从 `EnemyTypeID` 读取特定 字节、浮点数 或数组槽位。

```cpp
// C export lines 17644078-17644124
float __fastcall UEnemyDB::GetEnemyAilmentInfoOffsetX(unsigned int EnemyID)
{
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( !EnemyDB )
    return 0.0;
  v3 = *(_DWORD *)&EnemyDB[24];
  if ( v3 <= 0 )
    return 0.0;
  EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(v3);
  if ( EnemyTypeDB )
    return *(float *)&EnemyTypeDB[92];
  return 0.0;
}

float __fastcall UEnemyDB::GetEnemyBottomEffectOffset(unsigned int EnemyID)
{
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB && *(int *)&EnemyDB[24] > 0 )
    if ( (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(*(_DWORD *)&EnemyDB[24])) != nullptr )
      return *(float *)&EnemyTypeDB[88];
  return 0.0;
}
```

```cpp
// C export lines 17646459-17646487
char __fastcall UEnemyDB::GetOrderIconParameter(
        UE::Math::TVector2<double> *UVOffsetS,
        UE::Math::TVector2<double> *UVOffsetL,
        UE::Math::TVector2<double> *PixelScale,
        int TypeID)
{
  *UVOffsetL = UE::Math::TVector2<double>::ZeroVector;
  *UVOffsetS = UE::Math::TVector2<double>::ZeroVector;
  *PixelScale = UE::Math::TVector2<double>::UnitVector;
  EnemyTypeDB = (float *)UEnemyDB::GetEnemyTypeDB(TypeID);
  if ( !EnemyTypeDB )
    return 0;
  UVOffsetS->X = EnemyTypeDB[35];
  UVOffsetS->Y = EnemyTypeDB[36];
  UVOffsetL->X = EnemyTypeDB[38];
  UVOffsetL->Y = EnemyTypeDB[39];
  PixelScale->X = EnemyTypeDB[37];
  PixelScale->Y = EnemyTypeDB[40];
  return 1;
}
```

```cpp
// C export lines 17647390-17647419
char __fastcall UEnemyDB::GetVoiceCharaSymbol(
        TArray<unsigned short,TSizedDefaultAllocator<32> > *Symbol,
        unsigned int EnemyID)
{
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
  {
    v4 = *(_DWORD *)&EnemyDB[24];
    if ( v4 > 0 )
    {
      EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(v4);
      if ( EnemyTypeDB )
        return USoundUtility::GetVoiceSymbol(Symbol, *(_DWORD *)&EnemyTypeDB[128]);
    }
  }
  ...
}
```

| 键 | 运行时解释 |
|---|---|
| `m_EffectFromBottomOffsetY` | 通过敌人 ID 路径读取：先读 `EnemyID.m_TypeID @ +0x18`，再读 `EnemyTypeID +0x58`。若任一查找失败，取值函数 返回 `0.0`。 |
| `m_AilmentInfoOffsetX` | 通过同一条 `EnemyID -> EnemyTypeID` 路径从 `EnemyTypeID +0x5C` 读取。这是直接标量消费者，而不只是被复制字段。 |
| `m_OrderTexUS`, `m_OrderTexVS`, `m_OrderScaleS`, `m_OrderTexUL`, `m_OrderTexVL`, `m_OrderScaleL` | `GetOrderIconParameter` 会从行中读取六个连续 浮点数，分别为 `float[35]`、`float[36]`、`float[37]`、`float[38]`、`float[39]`、`float[40]`，对应行偏移 `+0x8C`、`+0x90`、`+0x94`、`+0x98`、`+0x9C` 与 `+0xA0`。 |
| `m_VoiceSymbol` | 声音子系统通过解析 `EnemyID -> EnemyTypeID` 取得 语音 符号，并将行 `+0x80` 传给 `USoundUtility::GetVoiceSymbol`。 |


#### ■ `EnemyTypeID` 作为呈现、贴图、音频与 VFX 描述符

前面的字段表证明 `EnemyTypeID` 行是活记录。以下代码路径是重要的高阶消费者：敌人构造、Flipbook/NPC 回退选择、行动顺序图标覆盖、战斗/场地附加特效、语音-符号 解析、呼叫音效播放，以及命中/偏移几何。这些路径表明该表并非单纯的贴图查找表；它是一个紧凑的呈现描述符，并且会反复通过 `EnemyID.m_TypeID` 抵达其行。

##### ◆ C-TYPE-CONSTRUCTION-01：敌人构造会遍历每个 `EnemyTypeID.m_AnimationSetIDs` 槽位并初始化击败特效状态

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413024-17413042
EnemyTypeFlipbookSetNum = UEnemyDB::GetEnemyTypeFlipbookSetNum(*(_DWORD *)&v12[24]);
for ( i = 0; i < EnemyTypeFlipbookSetNum; ++i )
  this->m_UseEnemyFlipbook |= AQPBattleCharacterBase::SetupEnemyFlipbook(this, i, EnemyID, 0);
AQPBattleCharacterBase::LoadOverrideOrderIcon(this);
this->m_IsEnemyPlayingDefeatEffect = 0;
EnemyTypeDefeatEffectID = UEnemyDB::GetEnemyTypeDefeatEffectID(*(_DWORD *)&v12[24]);
if ( EnemyTypeDefeatEffectID > 0 )
{
  this->m_IsEnemyUseUniqueDefeatEffect = 1;
  this->m_EnemyUniqueDefeatEffectID = EnemyTypeDefeatEffectID;
}
```

解释：在 `AQPBattleCharacterBase::SetupEnemy` 期间，敌人行已经作为 `v12` 常驻；行 `v12 + 0x18` 是 `EnemyID.m_TypeID`。设置路径会向 `GetEnemyTypeFlipbookSetNum(TypeID)` 查询动画集条目数量，然后按每个索引调用一次 `SetupEnemyFlipbook`。因此，`m_AnimationSetIDs` 是有序呈现槽位数组，而不是单一默认贴图 ID。同一次构造过程还会通过 `GetEnemyTypeDefeatEffectID(TypeID)` 读取 `m_DefeatEffect` 并存入角色运行时状态；在此之后，角色无需重新读取 DB 行即可播放唯一击败特效镜头路径。

##### ◆ C-TYPE-FLIPBOOK-FALLBACK-02：`SetupEnemyFlipbook` 中的动画集槽位解析与 NPC 回退

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413106-17413141
if ( v17 <= 0 )
  UEnemyDB::GetEnemyTypeID(&v17, EnemyID);
pFlipbookSet = nullptr;
CharacterID = (unsigned int)AQPBattleCharacterBase::GetCharacterID(this);
EnemyFlipbooks = AQPBattleManager::StaticGetEnemyFlipbooks(&pFlipbookSet, EnemyID, TextureIdx, 0, v17, this);
v22 = EnemyFlipbooks;
if ( !EnemyFlipbooks )
{
  p_m_EnemyFlag = &this->m_EnemyFlag;
  if ( this->m_EnemyFlag )
  {
    p_m_EnemyFlag = &this->m_EnemyFlag;
    if ( type >= ParamInt
      || (EnemyTypeCharaID = UEnemyDB::GetEnemyTypeCharaID(v17, TextureIdx),
          p_m_EnemyFlag = &this->m_EnemyFlag,
          CharacterID = EnemyTypeCharaID,
          EnemyTypeCharaID <= 0) )
    {
      v6 = type;
      goto LABEL_13;
    }
    EnemyFlipbooks = AQPBattleCharacterBase::SetEnemyFlipbook(this, TextureIdx, pFlipbookSet, EnemyTypeCharaID);
    p_m_EnemyFlag = &this->m_EnemyFlag;
  }
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413144-17413160
if ( !TextureIdx )
{
  AQPBattleCharacterBase::SetAnimVisible(this, EnemyFlipbooks, 0, v12);
  if ( v6 < ParamInt )
  {
    if ( v5 && *p_m_EnemyFlag || CharacterID > 0 )
    {
      type = 0;
      v15 = 1;
      if ( (unsigned __int8)UEnemyDB::GetEnemyTypeID(&type, EnemyID) )
      {
        offset = UE::Math::TVector2<double>::ZeroVector;
        scale = UE::Math::TVector2<double>::ZeroVector;
        UEnemyDB::GetEnemyTypeHitOffset(&offset, type);
        UEnemyDB::GetEnemyTypeHitScale(&scale, type);
        AQPCharacterBattle::SetHitBoxOffsetAndScale(m_AnimCharacter, &offset, &scale);
```

解释：Flipbook 路径会先尝试从 `m_AnimationSetIDs[TextureIdx]` 加载一个 `UEnemyFlipbookSet`。如果失败，并且该行仍是普通敌人类型而不是敌人部件 ID（`type < ParamInt`），它会读取 `m_NpcCharaID[TextureIdx]` 并将该角色 ID 用作视觉来源。命中框偏移和命中框缩放只会应用于贴图索引 `0`，也就是基础可见身体槽位。这比“NPC 战斗不需要 `EnemyBattleAnimSet`”更强：代码证明了明确的回退层级，而不是单纯的 结构定义 约定。

##### ◆ C-TYPE-FLIPBOOK-MATERIALIZATION-03：`StaticGetEnemyFlipbooks` 解析 `EnemyID -> TypeID`，而 `GetEnemyFlipBook` 会实体化所选动画集中的每个贴图 ID

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17456372-17456390
char __fastcall AQPBattleManager::StaticGetEnemyFlipbooks(
        UEnemyFlipbookSet **FlipbookSet,
        unsigned int EnemyID,
        int TextureIdx,
        bool ForceLoad,
        int TypeID,
        AQPBattleCharacterBase *Enemy)
{
  AQPBattleManager *v7; // rbx

  v7 = AQPBattleManager::m_Self;
  if ( TypeID <= 0 && !(unsigned __int8)UEnemyDB::GetEnemyTypeID(&TypeID, EnemyID) )
    return 0;
  if ( v7 && (v7->ObjectFlags & 0x40000000) == 0 )
    return AQPBattleManager::GetEnemyFlipBook(v7, FlipbookSet, TypeID, TextureIdx, ForceLoad, Enemy);
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17443757-17443838
ParamInt = UGameParamsUtility::GetParamInt(0x1B66u);
if ( TypeID < ParamInt )
  EnemyTypeFlipbookSet = UEnemyDB::GetEnemyTypeFlipbookSet(TypeID, TextureIdx);
else
  EnemyTypeFlipbookSet = UEnemyDB::GetEnemyPartsFlipbookID(TypeID - ParamInt);
if ( !*v36 || !EnemyTypeFlipbookSet )
  return v6;
(*v36)->m_FlipbookSetData = EnemyTypeFlipbookSet;
(*v36)->TextureIndex = TextureIdx;
v39 = (unsigned int *)EnemyTypeFlipbookSet->m_EnemyTextureIDs.AllocatorInstance.Data;
v40 = (FScriptContainerElement *)&v39[EnemyTypeFlipbookSet->m_EnemyTextureIDs.ArrayNum];
v59.AllocatorInstance.Data = v40;
if ( v39 == (unsigned int *)v40 )
  goto LABEL_53;
while ( 1 )
{
  v41 = *v39;
  if ( (int)*v39 <= 0 || !AResourceManager::m_Self )
    goto LABEL_48;
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17443862-17443892
AResourceManager::GetEnemyTexActionID(v42, actionTypeID, v41);
EnemyTexUseUniqueMaterialFlag = AResourceManager::GetEnemyTexUseUniqueMaterialFlag(v42, v41);
v36 = v58;
actionTypeID[1] = EnemyTexUseUniqueMaterialFlag;
Args.Key = (const EKSCharacterDir *)actionTypeID;
p_m_ActionFlipbooks = (TSet<TTuple<enum EKSCharacterDir,UNiagaraComponent *>,TDefaultMapHashableKeyFuncs<enum EKSCharacterDir,UNiagaraComponent *,0>,FDefaultSetAllocator> *)&(*v58)->m_ActionFlipbooks;
Args.Value = (UNiagaraComponent *const *)&v60;
TSet<TTuple<enum EBATTLE_ACTION_ID,UPaperFlipbook *>,TDefaultMapHashableKeyFuncs<enum EBATTLE_ACTION_ID,UPaperFlipbook *,0>,FDefaultSetAllocator>::Emplace<TPairInitializer<enum EBATTLE_ACTION_ID const &,UPaperFlipbook * const &>>(
  p_m_ActionFlipbooks,
  result,
  &Args,
  nullptr);
```

解释：当调用者传入 `TypeID <= 0` 时，运行时会惰性解析类型 ID。普通敌人路径与敌人部件路径通过 `UGameParamsUtility::GetParamInt(0x1B66)` 明确区分：低于阈值时，`TypeID` 是 `EnemyTypeID`；达到或高于阈值时，代码减去阈值并将其视为 `EnemyParts` ID。找到动画集行后，每个 `m_EnemyTextureIDs` 元素都会通过资源类别 `2` 加载，而其 `EnemyTexID` 元数据会提供动作 ID。因此，游戏并不会仅凭位置把 `m_EnemyTextureIDs[1]` 解释为“attack 0”；动作绑定由 `EnemyTexID` 动作 ID 数据驱动。

##### ◆ C-TYPE-ORDERICON-04：覆盖行动顺序图标使用 `m_OverrideOrderIconAnimSetID`；UV/缩放使用专用行 浮点数

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17409599-17409627
if ( (unsigned __int8)UEnemyDB::GetEnemyTypeID(&typeID, v4) )
{
  EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(typeID);
  if ( EnemyTypeDB )
  {
    v6 = *(_DWORD *)&EnemyTypeDB[136];
    if ( v6 > 0 )
    {
      ActionTexID = UEnemyDB::GetActionTexID(v6, (EBATTLE_ACTION_ID)1);
      v8 = ActionTexID;
      if ( ActionTexID > 0 )
      {
        Resource = AResourceManager::GetResource(v3, 2u, ActionTexID);
        Single = (UPaperFlipbook *)Resource;
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17646459-17646483
char __fastcall UEnemyDB::GetOrderIconParameter(
        UE::Math::TVector2<double> *UVOffsetS,
        UE::Math::TVector2<double> *UVOffsetL,
        UE::Math::TVector2<double> *PixelScale,
        int TypeID)
{
  ...
  EnemyTypeDB = (float *)UEnemyDB::GetEnemyTypeDB(TypeID);
  if ( !EnemyTypeDB )
    return 0;
  UVOffsetS->X = EnemyTypeDB[35];
  UVOffsetS->Y = EnemyTypeDB[36];
  UVOffsetL->X = EnemyTypeDB[38];
  UVOffsetL->Y = EnemyTypeDB[39];
  PixelScale->X = EnemyTypeDB[37];
  v9 = EnemyTypeDB[40];
  PixelScale->Y = v9;
  return 1;
}
```

解释：`m_OverrideOrderIconAnimSetID` 位于行 `+0x88`。当其为正时，引擎会从该独立动画集加载 idle/动作-ID-1 贴图，并将其第一个 sprite 存为 `m_OverrideOrderIconSprite`。UV 与像素缩放值不是从 Flipbook 推断出来的：`GetOrderIconParameter` 会分别将行 `+0x8C`、`+0x90`、`+0x94`、`+0x98`、`+0x9C` 和 `+0xA0` 读取为 `m_OrderTexUS`、`m_OrderTexVS`、`m_OrderScaleS`、`m_OrderTexUL`、`m_OrderTexVL` 和 `m_OrderScaleL`。

##### ◆ C-TYPE-ATTACHEFFECT-05：战斗附加特效使用行 `+0xA8/+0xAC/+0xB0`；场地附加特效使用行 `+0xB4/+0xB8/+0xBC`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17430329-17430370
if ( bAttach
  && (bAttach & (unsigned __int8)UEnemyDB::GetEnemyTypeID(&typeID, EnemyID)) != 0
  && (EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(typeID), (v11 = EnemyTypeDB) != nullptr)
  && (v12 = *(_DWORD *)&EnemyTypeDB[168], v12 > 0)
  && UEnemyDB::GetAttachEffect(v12, &baf_data) )
{
  ...
  v13 = *(float *)&v11[52];
  v14 = *(float *)&v11[56];
  m_Scale = baf_data.m_Scale;
  v16 = (float)(v13 * baf_data.m_Scale);
  loop = 1;
  v17 = (float)(v14 * baf_data.m_Scale);
  v18 = (float)*(int *)&v11[172];
  pos.X = pos.X + baf_data.m_OffsetPosX;
  v19 = pos.Y + (float)((float)(v18 + baf_data.m_OffsetPosY) * v13);
  v20 = (float)*(int *)&v11[176];
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17652820-17652856
EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(this->m_SymbolEnemyTypeId);
v21 = EnemyTypeDB;
if ( EnemyTypeDB )
{
  if ( *(_DWORD *)&EnemyTypeDB[180] )
  {
    outData.m_AttachEffect = 0;
    if ( ADatabaseDefineStatic::m_Self )
    {
      if ( UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
        && ADatabaseDefineStatic::m_Self
        && FAttachEffect::GetData(
             &ADatabaseDefineStatic::m_Self->m_AttachEffect,
             *(_DWORD *)&v21[180],
             &outData) )
      {
        UAttachEffectComponent::DetachEffect(this->m_AttachEffectComponent, outData.m_AttachEffect, 0);
      }
    }
  }
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17652875-17652925
v6 = UEnemyDB::GetEnemyTypeDB(this->m_SymbolEnemyTypeId);
v7 = (int *)v6;
if ( v6 )
{
  if ( *(_DWORD *)&v6[180] )
  {
    memset(&outData.m_AttachEffect, 0, 20);
    if ( ADatabaseDefineStatic::m_Self )
    {
      if ( UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
        && ADatabaseDefineStatic::m_Self
        && FAttachEffect::GetData(&ADatabaseDefineStatic::m_Self->m_AttachEffect, v7[45], &outData) )
      {
        m_SymbolEnemyTypeId = this->m_SymbolEnemyTypeId;
        offset = UE::Math::TVector<double>::ZeroVector;
        if ( m_SymbolEnemyTypeId > 0 && (v9 = UEnemyDB::GetEnemyTypeDB(m_SymbolEnemyTypeId)) != nullptr )
          X = *(float *)&v9[80];
        else
          X = offset.X;
        ...
        v14 = (float)((float)v7[47] + outData.m_OffsetPosZ);
        ...
        v18 = (double)v7[46];
```

解释：在战斗中，`+0xA8` 处的 `m_AttachEffect` 会选择一个 `AttachEffect` 行，而 `+0xAC/+0xB0` 处的 `m_BattleAttachEffectOffsetX` 与 `m_BattleAttachEffectOffsetY` 会在应用 `m_ScaleX/m_ScaleY` 后折入所生成特效的 Y/Z 放置。场地/符号敌人侧，`+0xB4` 处的 `m_FieldAttachEffect` 会选择 `AttachEffect` 行，而 `+0xB8/+0xBC` 处的 `m_FieldAttachEffectOffsetX/Y` 会进入符号偏移计算。场地路径还会读取 `m_EffectOffsetX`（`+0x50`）作为半宽侧向偏移，因此场地附加特效定位并不等同于战斗附加特效定位。

##### ◆ C-TYPE-AUDIO-06：声音与语音消费者会区分 `m_CallSE`、`m_VoiceSymbol` 与 `m_CueSheet`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17486476-17486498
char __fastcall AQPBattleManager::PlayEnemyTypeCallSE(int TypeID)
{
  int v1; // edi
  char v2; // bl

  if ( TypeID <= 0 )
    return 0;
  v1 = UEnemyDB::GetEnemyTypeCallSE(TypeID);
  if ( v1 <= 0 )
    return 0;
  v2 = 0;
  if ( ASoundManager::m_Self
    && UObjectBase::IsValidLowLevelFast(ASoundManager::m_Self, 1)
    && ASoundManager::m_Self
    && (ASoundManager::m_Self->ObjectFlags & 0x40000000) == 0 )
  {
    return ASoundManager::ExecRequestPlaySeById(ASoundManager::m_Self, v1, 100);
  }
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17647390-17647410
char __fastcall UEnemyDB::GetVoiceCharaSymbol(
        TArray<unsigned short,TSizedDefaultAllocator<32> > *Symbol,
        unsigned int EnemyID)
{
  ...
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
  {
    v4 = *(_DWORD *)&EnemyDB[24];
    if ( v4 > 0 )
    {
      EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(v4);
      if ( EnemyTypeDB )
        return USoundUtility::GetVoiceSymbol(Symbol, *(_DWORD *)&EnemyTypeDB[128]);
    }
  }
```

解释：行 `+0x7C` 处的 `m_CallSE` 是一个音效 ID，会直接送入 `ASoundManager::ExecRequestPlaySeById(..., volume_rate=100)`。行 `+0x80` 处的 `m_VoiceSymbol` 本身不是最终字符串；它会传给 `USoundUtility::GetVoiceSymbol`，该函数会实体化战斗语音代码使用的 `FString`/UTF-16 语音 符号。`m_CueSheet` 在 `FEnemyTypeIDBase` 复制构造函数中于 `+0x84` 处被复原，但本轮未复原出同样直接的敌人类型消费者；因此本指南应将 `m_CueSheet` 分类为具有不完整消费者证据的活实体化字段，而不是未使用字段。

##### ◆ C-TYPE-OFFSETMAP-07：由复制构造函数与直接消费者复原出的紧凑 `EnemyTypeID` 行布局图

| 偏移 | 键 | 运行时证据 | 解释 |
|---:|---|---|---|
| `+0x10/+0x18` | `m_AnimationSetIDs` | `GetEnemyTypeFlipbookSet` 复制该数组并按 `TextureIdx` 索引；`SetupEnemy` 遍历 `0..GetEnemyTypeFlipbookSetNum-1`。 | 有序视觉槽位。每个槽位都是一个 `EnemyBattleAnimSet.m_id`。 |
| `+0x20/+0x28` | `m_NpcCharaID` | `GetEnemyTypeCharaID(TypeID, TextureIdx)` 会边界检查数组并返回 `Data[TextureIdx]`。 | 当没有加载 Flipbook 集时，作为某个槽位的 NPC/角色回退。 |
| `+0x30` | `m_ChangeColorID` | `GetEnemyTypeColorID` 读取 `EnemyTypeDB[48]`。 | 通过类型行抵达的颜色变化 ID。 |
| `+0x34/+0x38` | `m_ScaleX`, `m_ScaleY` | `GetEnemyTypeScale` 读取 `+0x34/+0x38` 处的 浮点数；附加特效也会乘以这些值。 | 视觉/特效缩放基准。 |
| `+0x3C/+0x40` | `m_HitPosX`, `m_HitPosY` | `GetEnemyTypeHitOffset` 读取 `+0x3C/+0x40` 处的 浮点数。 | 基础 Flipbook 设置期间应用的基础 hit-box 偏移。 |
| `+0x44/+0x48` | `m_HitScaleX`, `m_HitScaleY` | `GetEnemyTypeHitScale` 读取 `+0x44/+0x48` 处的 浮点数。 | 基础 Flipbook 设置期间应用的基础 hit-box 缩放。 |
| `+0x4C` | `m_Size` | `GetEnemyTypeSize` 返回 `+0x4C` 处的 字节；`GetEnemySize(EnemyID)` 通过 `EnemyID.m_TypeID` 抵达它。 | 敌人的体型类别是类型属性，而不是 `EnemyID` 标量。 |
| `+0x50/+0x54/+0x58` | `m_EffectOffsetX`, `m_EffectOffsetY`, `m_EffectFromBottomOffsetY` | `GetEnemyEffectOffset` 读取 `+0x50/+0x54`；`GetEnemyBottomEffectOffset` 读取 `+0x58`；符号敌人的场地附加特效会使用 `+0x50`。 | 特效放置槽位。 |
| `+0x5C/+0x60` | `m_AilmentInfoOffsetX`, `m_AilmentOffset` | `GetEnemyAilmentInfoOffsetX` 读取 `+0x5C`；`GetAilmentEffectOffsetID` 读取 `+0x60`。 | UI/异常状态特效偏移。 |
| `+0x64/+0x68` | `m_DamageOffsetX`, `m_DamageOffsetY` | `GetEnemyDamageOffset` 读取 `+0x64/+0x68`。 | 伤害数字放置。 |
| `+0x6C/+0x70` | `m_CursorOffsetX`, `m_CursorOffsetY` | `GetEnemyCursorOffset` 读取 `+0x6C/+0x70`。 | 目标光标放置。 |
| `+0x74/+0x78` | `m_SupportOffsetY`, `m_SupportOffsetX` | 复制构造函数/实体化证据；本轮未复原出直接消费者。 | 活字段；消费者证据不完整。 |
| `+0x7C/+0x80/+0x84` | `m_CallSE`, `m_VoiceSymbol`, `m_CueSheet` | `PlayEnemyTypeCallSE` 使用 `m_CallSE`；`GetVoiceCharaSymbol` 使用 `m_VoiceSymbol`；`m_CueSheet` 会被复制/实体化。 | 音效与 语音-符号 是直接消费者；cue sheet 消费者仍为部分证据。 |
| `+0x88` | `m_OverrideOrderIconAnimSetID` | `LoadOverrideOrderIcon` 读取 `EnemyTypeDB[136]` 并调用 `GetActionTexID(..., EBATTLE_ACTION_ID::IDLE)`。 | 可选的独立行动顺序图标动画集。 |
| `+0x8C..+0xA0` | 行动顺序 UV/缩放字段 | `GetOrderIconParameter` 读取 浮点数 索引 `35..40`。 | 显式的行动顺序图标 UV 与缩放槽位。 |
| `+0xA4..+0xA6` | `m_AddStartUV`, `m_AttachEffectInGodbeastProduction`, `m_KeepVisibleAttachEffectOnMotion` | 复制构造函数证明全部三个字段；`IsVisibleAttachEffectOnMotion` 直接读取 `+0xA6`。 | `m_KeepVisibleAttachEffectOnMotion` 具有直接消费者；另外两个在已复原路径中仅为实体化。 |
| `+0xA8/+0xAC/+0xB0` | 战斗附加字段 | `SetEnemyAttachEffect` 读取 `+0xA8` 处的 ID，以及 `+0xAC/+0xB0` 处的偏移。 | 战斗角色附加特效。 |
| `+0xB4/+0xB8/+0xBC` | 场地附加字段 | `ASymbolEnemy::UpdateAttachEffect` 读取 `+0xB4` 处的 ID，以及 `+0xB8/+0xBC` 处的偏移。 | 符号/场地附加特效。 |
| `+0xC0/+0xC8` | `m_PartsID` | 部件设置使用该数组的 count/数据；前文部件章节引用了部件解析器。 | 敌人部件构成。 |
| `+0xD0` | `m_LinkOwnerAnim` | 复制构造函数/实体化证据；部件代码给出间接语境支持。 | 活 标志；编写联动部件时应测试其确切消费者。 |
| `+0xD4` | `m_DefeatEffect` | `SetupEnemy` 通过 `GetEnemyTypeDefeatEffectID` 读取并存储运行时败退特效状态。 | 独特败退特效 ID。 |
 
### ■ (**) `EnemyWeakID`
追踪可分配给敌人的不同弱点配置。`m_ResistWeapon` 与 `m_ResistMagic` 以 `Skill/` 配下 `ResistType` 的 `m_id` 为输入；`ResistType` 1 还会将指定武器/魔法类型加入为弱点。指定的武器与魔法类型的顺序大概遵循 `Skill/` 配下 `WeaponType`/`MagicType` 资产中的 `m_id` 顺序，即：
```json
{
            "m_id": 0,
            "m_ResistWeapon": [
              0, (Sword)
              0, (Polearm)
              0, (Dagger)
              0, (Axe)
              0, (Bow)
              0, (Staff)
              0, (Polearm)
              0, (Tome)
              0, (Fan)
              0 (Non-elemental physical damage, other weapon types do not count; shows up as ? in-game even after analysis)
            ],
            "m_ResistMagic": [
              0, ("None", does not appear to show a weakness icon either)
              0, (Fire)
              0, (Ice)
              0, (Lightning)
              0, (Wind)
              0, (Light)
              0, (Dark)
              0, (Non-Elemental Magic Damage, other magic types do not count)
              0 (Poison, as in the poison damage of a poisoned enemy will shave off one shield)
            ]
          }
```
**注：** OT0 无法在一个敌人身上显示超过 8 个弱点。

`Kingship_structs.hpp` 为 OT0 的部分 ResistType 分配了以下标签：

```hpp
enum class ERESIST_TYPE : uint8
{
	NONE                                     = 0,
	WEAK                                     = 1,
	REDUCE                                   = 2,
	INVALID                                  = 3,
	ABSORB                                   = 4,
	ERESIST_MAX                              = 5,
};
```

#### ■ 运行时读取语义
##### ◆ C-WEAK-01：EnemyWeakID 解析器与 弱点 ID 取值函数

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645664-17645695
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyWeakDB(unsigned int WeakID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !UEnemyDB::CheckEnemyDB() )
    return nullptr;
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v2 = ADatabaseDefineStatic::m_Self;
  else
    v2 = nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_EnemyWeakID.m_DBAccess,
                 WeakID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_EnemyWeakID.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_EnemyWeakID.m_DataList.AllocatorInstance.Data[48 * ArrayIndex];
}

//----- (0000000144605FB0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyWeakID(int EnemyID)
{
  FScriptContainerElement *EnemyDB; // rax

  if ( EnemyID > 0 && (EnemyDB = UEnemyDB::GetEnemyDB(EnemyID)) != nullptr )
    return *(unsigned int *)&EnemyDB[28];
  else
    return 0;
}

//----- (0000000144605FE0) ----------------------------------------------------
```

##### ◆ C-WEAK-02：敌人弱点行选择与 Resist 数组槽位选择

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645108-17645128
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyResists(int EnemyID, bool isMagic, int WeakID)
{
  int v3; // ebx
  FScriptContainerElement *EnemyDB; // rax

  v3 = WeakID;
  if ( EnemyID <= 0 )
    return nullptr;
  if ( WeakID > 0 )
    return USkillDB::GetResistsByID(v3, isMagic);
  v3 = 0;
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
    v3 = *(_DWORD *)&EnemyDB[28];
  if ( v3 <= 0 )
    return nullptr;
  else
    return USkillDB::GetResistsByID(v3, isMagic);
}

//----- (00000001446055D0) ----------------------------------------------------
```

##### ◆ C-WEAK-03：EnemyWeakID 数组的 USkillDB 槽位访问器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17974946-17974956
FScriptContainerElement *__fastcall USkillDB::GetResistsByID(int ResistID, bool isMagic)
{
  _BOOL8 v2; // rbx
  FScriptContainerElement *EnemyWeakDB; // rcx

  v2 = isMagic;
  if ( ResistID > 0 && (EnemyWeakDB = UEnemyDB::GetEnemyWeakDB(ResistID)) != nullptr )
    return &EnemyWeakDB[16 * v2 + 16];
  else
    return nullptr;
}
```

##### ◆ C-ENEMY-02：带具体行偏移的 EnemyID 实体化器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17647272-17647369
void __fastcall ASymbolEnemy::GetTargetLevel(ASymbolEnemy *this, FString *outLevel, FString *outName)
{
  unsigned int m_EnemyListId; // edx
  FDbAccessHelper<FEnemyIDBase> *p_m_DBAccess; // rsi
  FScriptContainerElement *v8; // rdi
  FScriptContainerElement *Data; // rbx
  int ArrayIndex; // eax
  const TArray<FEnemyIDBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v12; // rbx
  FEnemyIDBase cEnemyListBase; // [rsp+20h] [rbp-E0h] BYREF

  if ( ADatabaseDefineStatic::m_Self
    && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    && ADatabaseDefineStatic::m_Self )
  {
    m_EnemyListId = this->m_EnemyListId;
    p_m_DBAccess = &ADatabaseDefineStatic::m_Self->m_EnemyID.m_DBAccess;
    v8 = nullptr;
    Data = nullptr;
    memset(&cEnemyListBase.m_DisplayLevel, 0, 76);
    memset(&cEnemyListBase.m_ResistAilment, 0, 25);
    memset(&cEnemyListBase.m_Exp, 0, 85);
    *(_QWORD *)&cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = 0;
    *(_QWORD *)&cEnemyListBase.m_MaxSpecialSkillValue = 0;
    ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                   (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_EnemyID.m_DBAccess,
                   m_EnemyListId);
    if ( ArrayIndex >= 0 )
    {
      m_DatabasePtr = p_m_DBAccess->m_DatabasePtr;
      if ( m_DatabasePtr )
      {
        if ( ArrayIndex < m_DatabasePtr->ArrayNum )
        {
          v12 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[248 * ArrayIndex];
          cEnemyListBase.m_id = *(_DWORD *)(v12 + 8);
          TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(
            (TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_DisplayLevel,
            (const TArray<unsigned short,TSizedDefaultAllocator<32> > *)(v12 + 32));
          cEnemyListBase.m_BreakRate = *(_DWORD *)(v12 + 48);
          cEnemyListBase.m_MaxSLD = *(_DWORD *)(v12 + 52);
          cEnemyListBase.m_MaxHP = *(_DWORD *)(v12 + 56);
          cEnemyListBase.m_MaxSP = *(_DWORD *)(v12 + 60);
          cEnemyListBase.m_AtkP = *(_DWORD *)(v12 + 64);
          cEnemyListBase.m_DefP = *(_DWORD *)(v12 + 68);
          cEnemyListBase.m_AtkM = *(_DWORD *)(v12 + 72);
          cEnemyListBase.m_DefM = *(_DWORD *)(v12 + 76);
          cEnemyListBase.m_Agi = *(_DWORD *)(v12 + 80);
          cEnemyListBase.m_Crt = *(_DWORD *)(v12 + 84);
          cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);
          cEnemyListBase.m_Hit = *(_DWORD *)(v12 + 92);
          cEnemyListBase.m_Avd = *(_DWORD *)(v12 + 96);
          cEnemyListBase.m_DamageRate = *(_DWORD *)(v12 + 100);
          cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_ResistAilment,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 112));
          cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);
          cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);
          cEnemyListBase.m_IsBoss = *(_BYTE *)(v12 + 136);
          cEnemyListBase.m_Exp = *(_DWORD *)(v12 + 140);
          cEnemyListBase.m_Money = *(_DWORD *)(v12 + 144);
          cEnemyListBase.m_PetExp = *(_DWORD *)(v12 + 148);
          cEnemyListBase.m_JP = *(_DWORD *)(v12 + 152);
          cEnemyListBase.m_DropReward = *(_DWORD *)(v12 + 156);
          cEnemyListBase.m_StealReward = *(_DWORD *)(v12 + 160);
          cEnemyListBase.m_StealLeafNum = *(_DWORD *)(v12 + 164);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_EventDropRewards,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 168));
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_StatusOffset,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 184));
          cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);
          cEnemyListBase.m_NameUI = *(_BYTE *)(v12 + 204);
          cEnemyListBase.m_LevelUI = *(_BYTE *)(v12 + 205);
          cEnemyListBase.m_HpUI = *(_BYTE *)(v12 + 206);
          cEnemyListBase.m_ShieldUI = *(_BYTE *)(v12 + 207);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_RaceIndices,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 208));
          cEnemyListBase.m_DisplaySpecialSkillGauge = *(_BYTE *)(v12 + 224);
          cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = *(_DWORD *)(v12 + 228);
          cEnemyListBase.m_ReduceSpecialSkillValueBreak = *(_DWORD *)(v12 + 232);
          cEnemyListBase.m_MaxSpecialSkillValue = *(_DWORD *)(v12 + 236);
          cEnemyListBase.m_OutsideTarget = *(_BYTE *)(v12 + 240);
          cEnemyListBase.m_NotAction = *(_BYTE *)(v12 + 241);
          cEnemyListBase.m_UnknownLv = *(_BYTE *)(v12 + 242);
          cEnemyListBase.m_Immortal = *(_BYTE *)(v12 + 243);
          if ( cEnemyListBase.m_DisplayLevel.Data.ArrayNum )
            LODWORD(v8) = cEnemyListBase.m_DisplayLevel.Data.ArrayNum - 1;
          FString::AssignRange(
            outLevel,
            (const wchar_t *)cEnemyListBase.m_DisplayLevel.Data.AllocatorInstance.Data,
            (int)v8);
          UEnemyDB::GetEnemyNameString(outName, cEnemyListBase.m_id, 0);
          Data = cEnemyListBase.m_RaceIndices.AllocatorInstance.Data;
          v8 = cEnemyListBase.m_StatusOffset.AllocatorInstance.Data;
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_ResistMagic` | `+0x20`（数组）；引用块中没有保留下更短的具名赋值 | 通过传入 `isMagic=true` 选择魔法抗性向量；`USkillDB::GetResistsByID` 返回 `&EnemyWeakDB[0x20]`，即行 `+0x20` 处的 `TArray` 头。 | 已复原 | `C-WEAK-02`, `C-WEAK-03` |
| `m_ResistWeapon` | `C-WEAK-03:17974953` `return &EnemyWeakDB[16 * v2 + 16];` | 通过传入 `isMagic=false` 选择武器抗性向量；`USkillDB::GetResistsByID` 返回 `&EnemyWeakDB[0x10]`，即行 `+0x10` 处的 `TArray` 头。 | 已复原 | `C-WEAK-02`, `C-WEAK-03` |
| `m_WeakID` | `+0x1C`（标量；活字段属于 EnemyID）；引用块中没有保留下更短的具名赋值 | 当未提供显式弱点行覆盖时，`UEnemyDB::GetEnemyResists` 会先从 `+0x1C` 处的敌人行读取 `EnemyID.m_WeakID`，然后解析对应的 `EnemyWeakID` 行。 | 已复原 | `C-WEAK-02`, `C-ENEMY-02` |
| `m_id` | `+0x08`（标量；活字段属于 EnemyTypeID）；引用块中没有保留下更短的具名赋值 | 键会作为行偏移 `+0x08` 处的 32 位标量读取。`WeakID` 通过 `UEnemyDB::GetEnemyWeakDB` 解析；行跨度为 `0x30` 字节。 | 已复原 | `C-WEAK-01` |


### ■ (**) `EnemyWeakChangeID`
追踪敌人如何改变弱点。`m_WeakIndices` 以 `m_EnemyWeakID.m_id` 为输入，这些弱点变化的 `m_id` 可以分配给例如 avail（另见 [如何添加自定义技能与 avails](customskls_en.md)）。

`Kingship_structs.hpp` 为 OT0 的 ID 分配了下列标签。这个 enum 最好理解为面向库存行的 SDK 便利 enum，而不是运行时穷尽性约束。运行时不会对列出的 `EENEMY_WEAKCHANGE_ID` 常量进行 switch。它会通过 `m_EnemyWeakChangeID.m_DBAccess` 解析所请求的数值 `ChangeID`；如果该键解析到一行，就读取该行的 `m_WeakIndices` 数组与抽选 标志。因此，只要一致地重建 DB 访问/索引 数据，并且调用点接收到新的 ID，就可以扩展该表。
```hpp
enum class EENEMY_WEAKCHANGE_ID : uint8
{
	None                                     = 0,
	Wch_Farme_Chap03_Boss02                  = 1,
	Wch_AM_Chap0_Boss01_01                   = 2,
	Wch_AM_Chap0_Boss01_02                   = 3,
	Wch_AM_Chap0_Boss01_03                   = 4,
	Wch_AM_Chap0_Boss01_04                   = 5,
	Wch_AM_Chap0_Boss01_05                   = 6,
	Wch_AM_Chap0_Boss01_06                   = 7,
	Wch_AM_Chap0_Boss01_07                   = 8,
	Wch_AM_Chap8_Boss02_01                   = 9,
	Wch_AM_Chap8_Boss02_02                   = 10,
	Wch_AM_Chap8_Boss02_03                   = 11,
	Wch_AM_Chap8_Boss02_04                   = 12,
	Wch_AM_Chap8_Boss03_01                   = 13,
	Wch_AM_Chap8_Boss03_02                   = 14,
	Wch_AM_Chap8_Boss03_03                   = 15,
	Wch_AM_Chap8_Boss03_04                   = 24,
	Wch_AM_Chap8_Boss04_01                   = 17,
	Wch_AM_Chap8_Boss04_02                   = 25,
	Wch_AM_Chap8_Boss04_03                   = 26,
	Wch_AM_Chap8_Boss04_04                   = 27,
	Wch_AM_Chap4_Boss01_01                   = 16,
	Wch_Farme_Chap3_Boss02_01                = 18,
	Wch_Farme_Chap3_Boss02_02                = 19,
	Wch_Farme_Chap3_Boss02_03                = 20,
	Wch_AM_Chap2_Boss01_01                   = 21,
	Wch_AM_Boss_Chp5_03_01                   = 22,
	Wch_AM_Boss_Chp7_01_01                   = 23,
	EENEMY_WEAKCHANGE_MAX                    = 28,
};
```
不要把 `EENEMY_WEAKCHANGE_MAX` 当作引擎的实际上限。C 实现会接受存在于 `m_EnemyWeakChangeID.m_DBAccess` 中的任意正 `ChangeID`。该 enum 只是记录编译进 SDK 的具名库存 ID。
#### ■ 运行时读取语义
`EnemyWeakChangeID` 行被复原为 40 字节记录。活行读取器返回指向行 `+0x10` 处数组的指针，并从行 `+0x20` 写出一个 抽选 布尔值。行 ID 通过 `FDbAccessHelper` 解析，而不是通过对 enum 标签的 switch。

【主要 C 证据】

##### ◆ C-WCH-01：键查找、40 字节行跨度、`m_WeakIndices` 与 抽选 标志

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644137-17644168
const TArray<int,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetEnemyChangeWeakIndices(
        bool *isLottery,
        int ChangeID)
{
  ...
  *isLottery = 0;
  if ( ChangeID <= 0 || !UEnemyDB::CheckEnemyDB() )
    return nullptr;
  ...
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v4->m_EnemyWeakChangeID.m_DBAccess,
                 ChangeID);
  if ( ArrayIndex < 0 )
    return nullptr;
  if ( ArrayIndex >= v4->m_EnemyWeakChangeID.m_DataList.ArrayNum )
    return nullptr;
  v6 = (__int64)&v4->m_EnemyWeakChangeID.m_DataList.AllocatorInstance.Data[40 * ArrayIndex];
  if ( !v6 )
    return nullptr;
  *isLottery = *(_BYTE *)(v6 + 32);
  return (const TArray<int,TSizedDefaultAllocator<32> > *)(v6 + 16);
}
```

**解释。** `ChangeID` 是一个正整数键。行跨度为 40 字节。`m_WeakIndices` 是 `+0x10` 处的 `TArray<int>`；抽选 标志 是 `+0x20` 处的 字节。这里没有 enum 范围检查。

##### ◆ C-WCH-02：技能/avails 提供 `m_WeakChangeID`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17982007-17982014
EENEMY_WEAKCHANGE_ID __fastcall USkillDB::GetWeakChangeID(const FSkillAvailIDBase *pAvail)
{
  if ( pAvail )
    return pAvail->m_WeakChangeID;
  else
    return 0;
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17463128-17463130
v45 = USkillDB::GetWeakChangeID(v16);
v28 = AQPBattleCharacterBase::SetChangeWeak(pEnforcer, (unsigned __int8)v45);
```

**解释。** 一个 avail 携带 弱点变更 ID。观察到的一处用法会在调用 `SetChangeWeak` 前把该 ID 转换为 `unsigned __int8`；这是调用方侧的收窄副产物，如果引入大于 255 的 ID，应当进行测试。它不同于表查找例程；后者接受 `int ChangeID` 并执行 DB 查找。

##### ◆ C-WCH-03：设置活动 弱点变更 ID 会改变运行时状态并触发 UI 洗牌

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17454321-17454352
_BOOL8 __fastcall AQPBattleCharacterBase::SetChangeWeak(AQPBattleCharacterBase *this, int ChangeID)
{
  m_WeakChangeID = this->m_WeakChangeID;
  v3 = m_WeakChangeID != ChangeID;
  ...
  this->m_IsSwitchWeakChange = v3;
  if ( m_WeakChangeID != ChangeID )
  {
    this->m_WeakLottery1st = m_WeakChangeID <= 0;
    this->m_WeakChangeID = ChangeID;
  }
  m_BattleStatusUI = this->m_BattleStatusUI;
  if ( this->m_EnemyFlag && m_BattleStatusUI && (m_BattleStatusUI->ObjectFlags & 0x40000000) == 0 )
    m_BattleStatusUI->StartChangeWeak(this->m_BattleStatusUI);
  return v3;
}
```

**解释。** `SetChangeWeak` 将所选表 ID 存入运行时状态（`m_WeakChangeID`），在 ID 改变时标记一次切换，在从无 弱点变更 ID 过渡时初始化 first-抽选 标志，并启动状态 UI 的弱点洗牌动画。

##### ◆ C-WCH-04：弱点变更 执行会从 `m_WeakIndices` 构建队列

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17439647-17439688
v6 = UEnemyDB::GetEnemyChangeWeakIndices(&isLottery, m_WeakChangeID);
if ( v6 )
{
  EnemyWeakID = 0;
  if ( v4->m_WeakLottery1st )
  {
    if ( isLottery )
    {
      EnemyID = AQPBattleCharacterBase::GetEnemyID(v4);
      EnemyWeakID = UEnemyDB::GetEnemyWeakID(EnemyID);
    }
    v4->m_WeakLottery1st = 0;
  }
  ArrayNum = v6->ArrayNum;
  ...
  Data = (int *)v6->AllocatorInstance.Data;
  v11 = (__int64)&v6->AllocatorInstance.Data[4 * v6->ArrayNum];
  ...
  do
  {
    v12 = *Data;
    if ( *Data <= 0 )
      break;
    if ( v12 != EnemyWeakID )
    {
      ...
      *(_DWORD *)&p_m_ChangeWeaks->AllocatorInstance.Data[4 * v13] = v12;
    }
    ++Data;
  }
  while ( Data != (int *)v11 );
```

**解释。** `m_WeakIndices` 数组会被复制到角色的 `m_ChangeWeaks` 队列中，并带有两条重要运行时规则。第一，扫描在遇到非正 弱点 ID 时停止。第二，在首次 抽选 过渡期间，敌人当前/基础 弱点 ID 会从候选队列中排除。

##### ◆ C-WCH-05：抽选 模式会从候选队列追加重复随机抽取结果

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17439691-17439760
if ( isLottery )
{
  v14 = v4->m_ChangeWeaks.ArrayNum;
  v30 = v14;
  if ( v14 > 1 )
  {
    v15 = 16;
    ...
    do
    {
      RandomRange = AQPBattleManager::GetRandomRange(v14, 0);
      ...
      v19 = *(_DWORD *)&p_m_ChangeWeaks->AllocatorInstance.Data[4 * RandomRange];
      ...
      v4->m_ChangeWeaks.ArrayNum = v18 + 1;
      ...
      *(_DWORD *)&v27[4 * (int)v18] = v19;
      ...
    }
    while ( v15 > 0 );
  }
}
```

**解释。** 如果该行的 抽选 标志 为 true 且候选数大于一，运行时会用重复随机选择来扩展队列。这使 抽选 行即便与确定性循环行使用同一个 `m_WeakIndices` 数组，也表现出不同的行为。

##### ◆ C-WCH-06：执行会弹出下一个 弱点 ID 并存入 `m_NowWeakID`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17439764-17439773
if ( v4->m_ChangeWeaks.ArrayNum > 0 )
{
LABEL_42:
  LODWORD(weakID) = *(_DWORD *)p_m_ChangeWeaks->AllocatorInstance.Data;
  TArray<unsigned int,TSizedDefaultAllocator<32>>::Remove(
    (TArray<enum EMeshLODIdentifier,TSizedDefaultAllocator<32> > *)p_m_ChangeWeaks,
    (const EMeshLODIdentifier *)&weakID);
  v3 = (int)weakID;
}
result = v4->m_NowWeakID != v3;
v4->m_NowWeakID = v3;
return result;
```

**解释。** 被选中的弱点配置是队列头。选择之后，它会从队列中移除并存入 `m_NowWeakID`。返回值表示当前弱点配置是否发生改变。

##### ◆ C-WCH-07：UI/索引 路径会在 `m_WeakIndices` 中搜索当前 弱点 ID

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17443091-17443125
__int64 __fastcall AQPBattleCharacterBase::GetChangeWeakIndex(AQPBattleCharacterBase *this)
{
  ...
  v3 = UEnemyDB::GetEnemyChangeWeakIndices(&isLottery, m_WeakChangeID);
  if ( !v3 )
    return 0xFFFFFFFFLL;
  Data = v3->AllocatorInstance.Data;
  ArrayNum = v3->ArrayNum;
  ...
  while ( *(_DWORD *)v6 != m_NowWeakID )
  {
    v6 += 4;
    if ( v6 == (char *)v7 )
      return 0xFFFFFFFFLL;
  }
  return (v6 - (char *)Data) >> 2;
}
```

**解释。** 运行时可以通过搜索原始 `m_WeakIndices` 数组来恢复当前活动弱点配置的序号。如果当前 弱点 ID 不存在，该函数返回 `-1`。

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., ChangeID)` | `m_EnemyWeakChangeID` 的正整数键；库存 enum 标签不是穷尽性的运行时 switch。 | 已复原 | `C-WCH-01` |
| `m_WeakIndices` | 行 `+0x10`，作为 `TArray<int>*` 返回 | `EnemyWeakID.m_id` 值构成的有序弱点配置列表。`ChangeWeak` 中非正条目会终止扫描。 | 已复原 | `C-WCH-01`, `C-WCH-04`, `C-WCH-06`, `C-WCH-07` |
| 抽选 标志 / 行 `+0x20` | `*isLottery = *(_BYTE *)(v6 + 32)` | 会将队列构建从直接确定性排队改为随机队列扩展的布尔值。 | 已复原 | `C-WCH-01`, `C-WCH-05` |
| SDK enum entries | 引用的运行时中没有对 enum 常量的 switch | 仅为库存 ID 提供名称。扩展 DB 不受 `EENEMY_WEAKCHANGE_MAX` 阻止，但除非修补/测试相关调用点，通过 `unsigned __int8` 调用点传递的 ID 应保持在 字节 范围内。 | 已复原 / 有条件说明 | `C-WCH-01`, `C-WCH-02` |

### ■ (**) `EnemyWeakLockID`
追踪敌人如何锁定弱点。这里没有证据表明非属性魔法伤害与毒伤害会作为不同特殊槽位被锁定；锁定测试仅受已存储武器/魔法数组的长度以及传入的武器/魔法类型索引约束。

弱点 锁定 通常由 异常状态/avail 携带。战斗角色会检查活动 异常状态 槽中是否存在 异常状态 ID `37`，读取其来源 avail ID，取得 `SkillAvailID.m_WeakLockID`，然后询问 `EnemyWeakLockID` 某个给定武器或魔法弱点是否被锁定。UI 代码会分别取得同一组武器/魔法向量，并开始或结束锁定显示状态。

#### ■ 运行时读取语义
`EnemyWeakLockID` 行被复原为 80 字节记录。行 ID 通过 `m_EnemyWeakLockID.m_DBAccess` 解析。偏移 `+0x10` 与 `+0x20` 是两个布尔锁定向量；偏移 `+0x30` 与 `+0x40` 是决定是否移除 弱点锁定 异常状态 时使用的 条件/参数 向量。

【主要 C 证据】

##### ◆ C-WLOCK-01：键查找与 80 字节行跨度

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645696-17645719
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyWeakLockDB(unsigned int LockID)
{
  ...
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_EnemyWeakLockID.m_DBAccess,
                 LockID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_EnemyWeakLockID.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_EnemyWeakLockID.m_DataList.AllocatorInstance.Data[80 * ArrayIndex];
}
```

**解释。** `LockID` 是 DB 键，而不是 enum switch case。行跨度为 80 字节。

##### ◆ C-WLOCK-02：武器/魔法锁定向量由 `isMagic` 选择

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17646173-17646184
FScriptContainerElement *__fastcall UEnemyDB::GetLockedWeakFlags(int LockID, bool isMagic)
{
  _BOOL8 v2; // rbx
  FScriptContainerElement *EnemyWeakLockDB; // rcx

  v2 = isMagic;
  if ( LockID > 0 && (EnemyWeakLockDB = UEnemyDB::GetEnemyWeakLockDB(LockID)) != nullptr )
    return &EnemyWeakLockDB[16 * v2 + 16];
  else
    return nullptr;
}
```

**解释。** `isMagic=false` 返回行 `+0x10` 处的 `TArray<bool>` 头；`isMagic=true` 返回行 `+0x20` 处的 `TArray<bool>` 头。这正是该表拥有独立武器与魔法锁定数组的原因。

##### ◆ C-WLOCK-03：直接锁定谓词会对所选向量做边界检查

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17648966-17648990
char __fastcall UEnemyDB::IsLockedWeak(int LockID, unsigned __int8 WeaponType, unsigned __int8 MagicType)
{
  ...
  if ( LockID > 0 )
  {
    EnemyWeakLockDB = UEnemyDB::GetEnemyWeakLockDB(LockID);
    if ( EnemyWeakLockDB )
    {
      if ( (_BYTE)v4 )
      {
        if ( (int)v4 < *(_DWORD *)&EnemyWeakLockDB[24] && *(_BYTE *)(v4 + *(_QWORD *)&EnemyWeakLockDB[16]) )
          return 1;
      }
      else if ( (_BYTE)v3
             && (int)v3 < *(_DWORD *)&EnemyWeakLockDB[40]
             && *(_BYTE *)(v3 + *(_QWORD *)&EnemyWeakLockDB[32]) )
      {
        return 1;
      }
    }
  }
  return 0;
}
```

**解释。** 武器锁定使用 `+0x10/+0x18` 处的向量 指针/count；魔法锁定使用 `+0x20/+0x28`。如果 `WeaponType` 非零，则其优先；否则检查非零 `MagicType`。类型索引 `0` 不会由该谓词检查，因为两个分支都要求非零类型实参。这与指南警告相符：某些特殊弱点槽位并不是以普通武器/魔法图标相同的方式表示。

##### ◆ C-WLOCK-04：解除条件 数组位于行 `+0x30` 与 `+0x40`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17647421-17647436
char __fastcall UEnemyDB::GetWeakLockRemoveConditions(
        const TArray<int,TSizedDefaultAllocator<32> > **pConditions,
        const TArray<int,TSizedDefaultAllocator<32> > **pParams,
        int LockID)
{
  ...
  EnemyWeakLockDB = (const TArray<int,TSizedDefaultAllocator<32> > *)UEnemyDB::GetEnemyWeakLockDB(LockID);
  if ( !EnemyWeakLockDB || !pConditions || !pParams )
    return 0;
  *pConditions = EnemyWeakLockDB + 3;
  *pParams = EnemyWeakLockDB + 4;
  return 1;
}
```

**解释。** 由于该行被视为 16 字节 `TArray<int>` 头的序列，`EnemyWeakLockDB + 3` 即行 `+0x30`，`EnemyWeakLockDB + 4` 即行 `+0x40`。它们是 弱点锁定 专用移除 条件 与 参数 数组。

##### ◆ C-WLOCK-05：弱点锁定 移除条件会针对 异常状态 ID `37` 覆盖 异常状态 默认值

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17986839-17986858
pConditions[0] = (const TArray<int,TSizedDefaultAllocator<32> > *)&SkillAilmentDB[120];
pParams = (const TArray<int,TSizedDefaultAllocator<32> > *)&SkillAilmentDB[136];
if ( AilmentID == 37 )
{
  if ( AvailID > 0 )
  {
    if ( SkillAvailDB )
      m_WeakLockID = SkillAvailDB->m_WeakLockID;
    else
      m_WeakLockID = 0;
    UEnemyDB::GetWeakLockRemoveConditions(pConditions, &pParams, m_WeakLockID);
  }
}
```

**解释。** 弱点锁定 异常状态 的移除并不只依赖 `SkillAilmentDB` 的默认 解除 条件。当 异常状态 ID 为 `37` 时，avail 的 `m_WeakLockID` 可以把 条件/参数 数组重定向到 `EnemyWeakLockID` 行。

##### ◆ C-WLOCK-06：活动 异常状态 路径取得 `SkillAvailID.m_WeakLockID`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17426685-17426724
char __fastcall AQPBattleCharacterBase::IsLockedWeak(
        AQPBattleCharacterBase *this,
        unsigned __int8 WeaponType,
        unsigned __int8 MagicType)
{
  ...
  if ( !this->m_EnemyFlag || !WeaponType && !MagicType )
    return 0;
  Data = this->m_AilmentInfo.AllocatorInstance.Data;
  ...
  while ( 1 )
  {
    if ( *(_DWORD *)Data == 37 && UCriFsLoaderComponent::GetStatus(*(AQPBattleCharacterBase **)&Data[56]) )
    {
      SkillAvailDB = USkillDB::GetSkillAvailDB(*(_DWORD *)&Data[36]);
      if ( SkillAvailDB )
      {
        WeakLockID = USkillDB::GetWeakLockID(SkillAvailDB);
        if ( WeakLockID )
        {
          result = UEnemyDB::IsLockedWeak((unsigned __int8)WeakLockID, WeaponType, MagicType);
          ...
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17982016-17982023
EENEMY_WEAKLOCK_ID __fastcall USkillDB::GetWeakLockID(const FSkillAvailIDBase *pAvail)
{
  if ( pAvail )
    return pAvail->m_WeakLockID;
  else
    return 0;
}
```

**解释。** 锁定 ID 的运行时来源，是产生了活动 弱点锁定 异常状态 的 技能 avail。与 弱点变更 ID 一样，返回类型是 enum 名称，但其值会作为数据传入 DB 查找；引用代码并未实现对枚举项的 switch。

##### ◆ C-WLOCK-07：UI 锁定状态读取同一组武器/魔法向量

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17454970-17454988
SkillAvailDB = USkillDB::GetSkillAvailDB(AvailID);
WeakLockID = (unsigned __int8)USkillDB::GetWeakLockID(SkillAvailDB);
LockedWeakFlags = UEnemyDB::GetLockedWeakFlags(WeakLockID, 0);
v8 = UEnemyDB::GetLockedWeakFlags(WeakLockID, 1);
if ( LockedWeakFlags )
{
  if ( v8 )
  {
    ...
    if ( isOn )
      m_BattleStatusUI->StartWeakLock(..., LockedWeakFlags, v8);
    else
      m_BattleStatusUI->EndWeakLock(..., LockedWeakFlags, v8);
  }
}
```

**解释。** UI 表示并不是单独的表。它接收 `GetLockedWeakFlags` 返回的同两个锁定向量，因此修改武器/魔法数组会同时改变逻辑锁定检查与显示的锁定状态。

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., LockID)`，行跨度 `80` | `m_EnemyWeakLockID` 的正整数键。库存 enum 标签不是穷尽性运行时 switch。 | 已复原 | `C-WLOCK-01` |
| 武器 锁定 数组 | 行 `+0x10/+0x18` | 当 `WeaponType != 0` 时测试的 `TArray<bool>`；也会作为武器向量传给 UI 锁定显示。 | 已复原 | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| 魔法 锁定 数组 | 行 `+0x20/+0x28` | 当 `WeaponType == 0 && MagicType != 0` 时测试的 `TArray<bool>`；也会作为魔法向量传给 UI 锁定显示。 | 已复原 | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| 解除 条件 | 行 `+0x30` | 用于移除 弱点锁定 异常状态 的 `TArray<int>` 条件 ID。 | 已复原 | `C-WLOCK-04`, `C-WLOCK-05` |
| 解除 参数 | 行 `+0x40` | 与 解除 条件 成对的 `TArray<int>` 参数。 | 已复原 | `C-WLOCK-04`, `C-WLOCK-05` |
| non-elemental / poison locking | `IsLockedWeak` 中没有特殊分支 | 引用的谓词只会在两个已存储向量内检查非零武器或魔法类型索引。这里未复原出单独的特殊情况槽位。 | 否定性/部分复原 | `C-WLOCK-03` |

### ■ (****) `EnemyID`
这是游戏的敌人主记录：它定义 stats/weaknesses/奖励/UI 标志，指向负责表现的 `EnemyTypeID`（动画集、贴图、偏移、语音与特效），并指向负责敌人行为及其技能集的 `TacticalAssignList` 与 `TacticalSkillList`（见下文“`AIBattle` 配下”）。
- `m_Label`：敌人的 label。可能在内部使用，通常最好不要添加 label 冲突的新条目。
- `m_DisplayName`：ID 指向 `GameTextEnemy.m_id`，对所有本地化通用。
- `m_WeakID`：ID 指向 `EnemyWeakID.m_id`，见上文。
- `m_DisplayLevel`：敌人的显示等级。遗憾的是，即使 `m_LevelUI` 设为 true 也不会显示。
- `m_BreakRate`：敌人破防时，对其造成的伤害会乘以的百分比值。
- `m_DamageRate`：敌人未破防时，对其造成的伤害会乘以的百分比值。
- `m_MaxSLD`：敌人的护盾数。即使键名看起来暗示最大值，某些 战术 和 技能 也可以把它设为更高值（例如 Lucian the Glorious）。
- `m_MaxHP`：敌人的 HP。与上文相同，技能 和 战术 可以提高它。
- `m_MaxSP`：敌人的 SP。多数敌人技能使用 0 SP，因此这个 能力值 主要用于限制特定敌人技能的使用次数。
- `m_AtkP`：敌人的物攻。
- `m_AtkM`：敌人的属攻。
- `m_DefP`：敌人的物防。
- `m_DefM`：敌人的属防。
- `m_Agi`：敌人的速度/敏捷。
- `m_Crt`：敌人的暴击 能力值。
- `m_CrtDef`：敌人的暴击防御；似乎只有敌人拥有这个 能力值。
- `m_Hit`：敌人的命中 能力值。
- `m_Avd`：敌人的回避 能力值。
- `m_ResistAilmentID`：为敌人分配追踪状态异常抗性的 ID（见 `Skill/` 配下的 `SkillResistAilmentID`）。
- `m_ResistAilment`：一个数组，对应特定 异常状态 的抗性率；可能在所分配 `ResistAilmentID` 的相应 `m_ResistAilments` 数组已满时使用。似乎只由 Caits 使用。
- `m_TacticalAssignID`：向敌人分配 `TacticalAssignList.m_id`，对应战斗中使用的 战术 列表；见下文“`AIBattle` 配下”。
- `m_SkillsID`：向敌人分配 `TacticalSkllList.m_id`，对应战斗中使用的技能列表；见下文“`AIBattle` 配下”。
- `m_IsBoss`：许多 boss 会使用的 标志，城镇 NPC 也会使用。不确定它在功能上做什么。
- `m_Exp`, `m_Money`, `m_JP`：击败该敌人后获得的 XP、金钱或 JP。
- `m_PetExp`：在 CotC 与 OT0 中始终设为 -1。不等于 -1 的值似乎没有效果。
- `m_DropReward`：敌人可能掉落的物品。（可用 ID 见 `EnemyDropID`）
- `m_StealReward`：可以从敌人处偷取的物品（可用 ID 见 `Item/` 配下的 `ItemList`）。
- `m_StealLeafNum`：可以从敌人处偷取的叶币数量。
- `m_StatusOffset`：敌人状态栏（弱点与护盾数）的偏移。
- `m_Bottle`：含义不明。OT0 使用 1-3。值 3 在 OT0 中只由 Wappa 使用。CotC 也使用 4 和 5。
- `m_NameUI`：敌人名称是否出现在 UI 上。
- `m_LevelUI`：敌人的 `m_DisplayLevel` 是否显示在 UI 上（未使用/不起作用）。
- `m_HpUI`：在 OT0 中不起作用。
- `m_ShieldUI`：如果设为 true，似乎会使敌人的护盾图标变暗。
- `m_RaceIndices`：分配给敌人的种族。可能影响伤害计算。
- `m_DisplaySpecialSkillGauge`：显示敌人 BP 计数器的 标志。
- `m_GenerateSpecialSkillValueOrverTurn`：敌人每回合生成多少 BP 点。
- `m_ReduceSpecialSkillValueBreak`：敌人破防后会失去多少 BP 点。
- `m_MaxSpecialSkillValue`：敌人拥有的 BP 点最大数量。高于 20 的值会使游戏崩溃。
- `m_OutsideTarget`：敌人不能被选为目标；如果它是场上唯一敌人，队伍成员也会跳过行动（Bestower of All 在戒指被破坏前会使用此项）。
- `m_NotAction`：若为 true，则不允许敌人行动（Bestower of All 在所有戒指被击败前使用）。
- `m_UnknownLv`：将敌人等级显示为“??”；虽然由于缺少敌人等级 UI 而未使用且视觉上不起作用，但不要把它当作已证明未使用的 padding。OT0 反编译中具名的 `FEnemyIDBase` 复制/实体化路径会把它作为真实的后期 标志 复制（`Octopath_Traveler0-Win64-Shipping.exe.c`:17041778-17041806 与 17647345-17647360）。
- `m_Immortal`：若设为 true，敌人的 HP 永远不会低于 1。

#### ■ 运行时读取语义（字段级 Hex-Rays 证据）
##### ◆ C-ENEMY-01：EnemyID 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644202-17644227
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyDB(unsigned int EnemyID)
{
  TArray<FEnemyIDBase,TSizedDefaultAllocator<32> > *EnemiesDB; // rbx
  ADatabaseDefineStatic *v3; // rcx
  int ArrayIndex; // eax

  EnemiesDB = UEnemyDB::GetEnemiesDB();
  if ( EnemiesDB
    && UEnemyDB::CheckEnemyDB()
    && (!ADatabaseDefineStatic::m_Self || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
      ? (v3 = nullptr)
      : (v3 = ADatabaseDefineStatic::m_Self),
        (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                        (FDbAccessHelper<FNoteListBase> *)&v3->m_EnemyID.m_DBAccess,
                        EnemyID),
         ArrayIndex >= 0)
     && ArrayIndex < EnemiesDB->ArrayNum) )
  {
    return &EnemiesDB->AllocatorInstance.Data[248 * ArrayIndex];
  }
  else
  {
    return nullptr;
  }
}

```

##### ◆ C-ENEMY-02：带具体行偏移的 EnemyID 实体化器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17647272-17647369
void __fastcall ASymbolEnemy::GetTargetLevel(ASymbolEnemy *this, FString *outLevel, FString *outName)
{
  unsigned int m_EnemyListId; // edx
  FDbAccessHelper<FEnemyIDBase> *p_m_DBAccess; // rsi
  FScriptContainerElement *v8; // rdi
  FScriptContainerElement *Data; // rbx
  int ArrayIndex; // eax
  const TArray<FEnemyIDBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v12; // rbx
  FEnemyIDBase cEnemyListBase; // [rsp+20h] [rbp-E0h] BYREF

  if ( ADatabaseDefineStatic::m_Self
    && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    && ADatabaseDefineStatic::m_Self )
  {
    m_EnemyListId = this->m_EnemyListId;
    p_m_DBAccess = &ADatabaseDefineStatic::m_Self->m_EnemyID.m_DBAccess;
    v8 = nullptr;
    Data = nullptr;
    memset(&cEnemyListBase.m_DisplayLevel, 0, 76);
    memset(&cEnemyListBase.m_ResistAilment, 0, 25);
    memset(&cEnemyListBase.m_Exp, 0, 85);
    *(_QWORD *)&cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = 0;
    *(_QWORD *)&cEnemyListBase.m_MaxSpecialSkillValue = 0;
    ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                   (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_EnemyID.m_DBAccess,
                   m_EnemyListId);
    if ( ArrayIndex >= 0 )
    {
      m_DatabasePtr = p_m_DBAccess->m_DatabasePtr;
      if ( m_DatabasePtr )
      {
        if ( ArrayIndex < m_DatabasePtr->ArrayNum )
        {
          v12 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[248 * ArrayIndex];
          cEnemyListBase.m_id = *(_DWORD *)(v12 + 8);
          TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(
            (TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_DisplayLevel,
            (const TArray<unsigned short,TSizedDefaultAllocator<32> > *)(v12 + 32));
          cEnemyListBase.m_BreakRate = *(_DWORD *)(v12 + 48);
          cEnemyListBase.m_MaxSLD = *(_DWORD *)(v12 + 52);
          cEnemyListBase.m_MaxHP = *(_DWORD *)(v12 + 56);
          cEnemyListBase.m_MaxSP = *(_DWORD *)(v12 + 60);
          cEnemyListBase.m_AtkP = *(_DWORD *)(v12 + 64);
          cEnemyListBase.m_DefP = *(_DWORD *)(v12 + 68);
          cEnemyListBase.m_AtkM = *(_DWORD *)(v12 + 72);
          cEnemyListBase.m_DefM = *(_DWORD *)(v12 + 76);
          cEnemyListBase.m_Agi = *(_DWORD *)(v12 + 80);
          cEnemyListBase.m_Crt = *(_DWORD *)(v12 + 84);
          cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);
          cEnemyListBase.m_Hit = *(_DWORD *)(v12 + 92);
          cEnemyListBase.m_Avd = *(_DWORD *)(v12 + 96);
          cEnemyListBase.m_DamageRate = *(_DWORD *)(v12 + 100);
          cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_ResistAilment,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 112));
          cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);
          cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);
          cEnemyListBase.m_IsBoss = *(_BYTE *)(v12 + 136);
          cEnemyListBase.m_Exp = *(_DWORD *)(v12 + 140);
          cEnemyListBase.m_Money = *(_DWORD *)(v12 + 144);
          cEnemyListBase.m_PetExp = *(_DWORD *)(v12 + 148);
          cEnemyListBase.m_JP = *(_DWORD *)(v12 + 152);
          cEnemyListBase.m_DropReward = *(_DWORD *)(v12 + 156);
          cEnemyListBase.m_StealReward = *(_DWORD *)(v12 + 160);
          cEnemyListBase.m_StealLeafNum = *(_DWORD *)(v12 + 164);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_EventDropRewards,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 168));
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_StatusOffset,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 184));
          cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);
          cEnemyListBase.m_NameUI = *(_BYTE *)(v12 + 204);
          cEnemyListBase.m_LevelUI = *(_BYTE *)(v12 + 205);
          cEnemyListBase.m_HpUI = *(_BYTE *)(v12 + 206);
          cEnemyListBase.m_ShieldUI = *(_BYTE *)(v12 + 207);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_RaceIndices,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 208));
          cEnemyListBase.m_DisplaySpecialSkillGauge = *(_BYTE *)(v12 + 224);
          cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = *(_DWORD *)(v12 + 228);
          cEnemyListBase.m_ReduceSpecialSkillValueBreak = *(_DWORD *)(v12 + 232);
          cEnemyListBase.m_MaxSpecialSkillValue = *(_DWORD *)(v12 + 236);
          cEnemyListBase.m_OutsideTarget = *(_BYTE *)(v12 + 240);
          cEnemyListBase.m_NotAction = *(_BYTE *)(v12 + 241);
          cEnemyListBase.m_UnknownLv = *(_BYTE *)(v12 + 242);
          cEnemyListBase.m_Immortal = *(_BYTE *)(v12 + 243);
          if ( cEnemyListBase.m_DisplayLevel.Data.ArrayNum )
            LODWORD(v8) = cEnemyListBase.m_DisplayLevel.Data.ArrayNum - 1;
          FString::AssignRange(
            outLevel,
            (const wchar_t *)cEnemyListBase.m_DisplayLevel.Data.AllocatorInstance.Data,
            (int)v8);
          UEnemyDB::GetEnemyNameString(outName, cEnemyListBase.m_id, 0);
          Data = cEnemyListBase.m_RaceIndices.AllocatorInstance.Data;
          v8 = cEnemyListBase.m_StatusOffset.AllocatorInstance.Data;
```

##### ◆ C-ENEMY-03：EnemyID 后期状态桥接到战斗角色 标志

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413040-17413073
        this->m_IsEnemyUseUniqueDefeatEffect = 1;
        this->m_EnemyUniqueDefeatEffectID = EnemyTypeDefeatEffectID;
      }
      if ( this->m_EnemyFlag )
      {
        m_CharacterID = this->m_CharacterID;
        if ( m_CharacterID > 0 )
        {
          v22 = 0;
          MaxWeaponType = USkillDB::GetMaxWeaponType();
          for ( j = 0; j < MaxWeaponType; ++j )
          {
            if ( (unsigned __int8)AQPBattleManager::GetEnemyWeakFlags(m_CharacterID, 0, j) )
              v22 |= 1 << j;
          }
          this->m_WeakWeaponFlags = v22;
          MaxMagicType = USkillDB::GetMaxMagicType();
          for ( k = 0; k < MaxMagicType; ++k )
          {
            if ( (unsigned __int8)AQPBattleManager::GetEnemyWeakFlags(m_CharacterID, 1, k) )
              v14 |= 1 << k;
          }
          this->m_WeakMagicFlags = v14;
        }
      }
      AQPBattleCharacterBase::SetupParams(this, v29, isSkipDebugName);
      AQPBattleCharacterBase::SetImmortal(this, *(_BYTE *)&v12[243]);
      this->m_IsOutsideTarget = (bool)v12[240];
      this->m_IsNotAction = (bool)v12[241];
      if ( pAnim && (pAnim->ObjectFlags & 0x40000000) == 0 )
        AKSCharacterBase::ExecPlayBreakAnimation(pAnim, 0);
      AQPBattleCharacterBase::InitSLD(this);
      AQPBattleCharacterBase::InitBP(this, 0, 0);
      AQPBattleCharacterBase::InitSpecialSkillGaugeValue(this);
```

##### ◆ C-ENEMY-04：OutsideTarget、NotAction、Immortal 的运行时 mutators/访问器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17442145-17442168
    TArray<FVulkanCommandListContext *,TSizedDefaultAllocator<32>>::ResizeTo(p_m_CommandStocks, 0);
  return 1;
}

//----- (0000000144511040) ----------------------------------------------------
char __fastcall AQPBattleCharacterBase::DisableNotAction(AQPBattleCharacterBase *this)
{
  if ( !this->m_IsNotAction )
    return 0;
  this->m_IsNotAction = 0;
  return 1;
}

//----- (0000000144511060) ----------------------------------------------------
char __fastcall AQPBattleCharacterBase::DisableOutsideTarget(AQPBattleCharacterBase *this)
{
  if ( !this->m_IsOutsideTarget )
    return 0;
  this->m_IsOutsideTarget = 0;
  return 1;
}

//----- (0000000144511080) ----------------------------------------------------
void __fastcall AQPBattleCharacterBase::EnableShieldLock(AQPBattleCharacterBase *this)
```

##### ◆ C-ENEMY-05：NotAction 与 OutsideTarget 的运行时 取值函数，以及 Immortal setter

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17449948-17449964
    }
  }
  return result;
}

//----- (0000000144519CF0) ----------------------------------------------------
_BOOL8 __fastcall AQPBattleCharacterBase::IsNotAction(AQPBattleCharacterBase *this)
{
  return this->m_IsNotAction;
}

//----- (0000000144519D00) ----------------------------------------------------
_BOOL8 __fastcall AQPBattleCharacterBase::IsOutsideTarget(AQPBattleCharacterBase *this)
{
  return this->m_IsOutsideTarget;
}

```

##### ◆ C-ENEMY-06：RaceIndices 成员测试

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17647438-17647464
//----- (0000000144607D30) ----------------------------------------------------
char __fastcall UEnemyDB::HasEnemyRace(int EnemyID, EENEMY_RACE_ID RaceID)
{
  FScriptContainerElement *EnemyDB; // rax
  FScriptContainerElement *v4; // rcx
  _DWORD *v5; // rax
  __int64 v6; // rdx

  if ( EnemyID <= 0 || RaceID <= 0 )
    return 0;
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  v4 = EnemyDB + 208;
  if ( !EnemyDB )
    v4 = nullptr;
  if ( !v4 )
    return 0;
  v5 = *(_DWORD **)v4;
  v6 = *(_QWORD *)v4 + 4LL * *(int *)&v4[8];
  if ( *(_QWORD *)v4 == v6 )
    return 0;
  while ( *v5 != RaceID )
  {
    if ( ++v5 == (_DWORD *)v6 )
      return 0;
  }
  return 1;
}
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_Agi` | `C-ENEMY-02:17647319` `cEnemyListBase.m_Agi = *(_DWORD *)(v12 + 80);` | 该键作为行偏移 `+0x50` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkM` | `C-ENEMY-02:17647317` `cEnemyListBase.m_AtkM = *(_DWORD *)(v12 + 72);` | 该键作为行偏移 `+0x48` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkP` | `C-ENEMY-02:17647315` `cEnemyListBase.m_AtkP = *(_DWORD *)(v12 + 64);` | 该键作为行偏移 `+0x40` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Avd` | `C-ENEMY-02:17647323` `cEnemyListBase.m_Avd = *(_DWORD *)(v12 + 96);` | 该键作为行偏移 `+0x60` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Bottle` | `C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | 该键作为行偏移 `+0xC8` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_BreakRate` | `C-ENEMY-02:17647311` `cEnemyListBase.m_BreakRate = *(_DWORD *)(v12 + 48);` | 该键作为行偏移 `+0x30` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | 这不是 `EnemyID` 行字段。当它在 `EnemyID` 节中被提及时，它是通过 `EnemyID.m_TypeID -> EnemyTypeID +0x7C` 抵达的。 | 已复原 | `C-TYPE-02`, `C-TYPE-03` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | 这不是 `EnemyID` 行字段。`GetEnemyTypeColorID` 在解析 `EnemyID.m_TypeID` 后读取 `EnemyTypeID +0x30` 处的类型行。 | 已复原 | `C-TYPE-01`, `C-TYPE-02` |
| `m_Crt` | `C-ENEMY-02:17647320` `cEnemyListBase.m_Crt = *(_DWORD *)(v12 + 84);`<br>`C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | 该键作为行偏移 `+0x54` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CrtDef` | `C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | 该键作为行偏移 `+0x58` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DamageRate` | `C-ENEMY-02:17647324` `cEnemyListBase.m_DamageRate = *(_DWORD *)(v12 + 100);` | 该键作为行偏移 `+0x64` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefM` | `C-ENEMY-02:17647318` `cEnemyListBase.m_DefM = *(_DWORD *)(v12 + 76);` | 该键作为行偏移 `+0x4C` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefP` | `C-ENEMY-02:17647316` `cEnemyListBase.m_DefP = *(_DWORD *)(v12 + 68);` | 该键作为行偏移 `+0x44` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayLevel` | `C-ENEMY-02:17647291` `memset(&cEnemyListBase.m_DisplayLevel, 0, 76);`<br>`C-ENEMY-02:17647309` `(TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_DisplayLevel,`<br>`C-ENEMY-02:17647361` `if ( cEnemyListBase.m_DisplayLevel.Data.ArrayNum )` plus 2 further local occurrences | 该键作为行偏移 `+0x20` 处的 `FString`/UTF-16 数组头读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayName` | `C-ENEMY-02:17647281` `FEnemyIDBase cEnemyListBase; // [rsp+20h] [rbp-E0h] BYREF`<br>`C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | 该键作为行偏移 `+0x14` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplaySpecialSkillGauge` | `C-ENEMY-02:17647353` `cEnemyListBase.m_DisplaySpecialSkillGauge = *(_BYTE *)(v12 + 224);` | 该键作为行偏移 `+0xE0` 处的 字节/布尔 槽位读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DropReward` | `C-ENEMY-02:17647336` `cEnemyListBase.m_DropReward = *(_DWORD *)(v12 + 156);` | 该键作为行偏移 `+0x9C` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_EventDropRewards` | `C-ENEMY-02:17647340` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_EventDropRewards,` | 该键作为行偏移 `+0xA8` 处的 `TArray` 头读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Exp` | `C-ENEMY-02:17647293` `memset(&cEnemyListBase.m_Exp, 0, 85);`<br>`C-ENEMY-02:17647332` `cEnemyListBase.m_Exp = *(_DWORD *)(v12 + 140);` | 该键作为行偏移 `+0x8C` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_GenerateSpecialSkillValueOrverTurn` | `C-ENEMY-02:17647294` `*(_QWORD *)&cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = 0;`<br>`C-ENEMY-02:17647354` `cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = *(_DWORD *)(v12 + 228);` | 该键作为行偏移 `+0xE4` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Hit` | `C-ENEMY-02:17647322` `cEnemyListBase.m_Hit = *(_DWORD *)(v12 + 92);` | 该键作为行偏移 `+0x5C` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_HpUI` | `C-ENEMY-02:17647348` `cEnemyListBase.m_HpUI = *(_BYTE *)(v12 + 206);` | 该键作为行偏移 `+0xCE` 处的 字节/布尔 槽位读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Immortal` | `C-ENEMY-02:17647360` `cEnemyListBase.m_Immortal = *(_BYTE *)(v12 + 243);` | `EnemyID +0xF3` 处的 字节 会在战斗角色设置期间通过 `SetImmortal` 传递；设置完成后，运行时代码会在角色对象上读写 `m_IsImmortal`，而不是重新读取 DB 行。 | 已复原 | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-05` |
| `m_IsBoss` | `C-ENEMY-02:17647331` `cEnemyListBase.m_IsBoss = *(_BYTE *)(v12 + 136);` | 该键作为行偏移 `+0x88` 处的 字节/布尔 槽位读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_JP` | `C-ENEMY-02:17647335` `cEnemyListBase.m_JP = *(_DWORD *)(v12 + 152);` | 该键作为行偏移 `+0x98` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Label` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | 该键作为行偏移 `+0x0C` 处的 `FName` 读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_LevelUI` | `C-ENEMY-02:17647347` `cEnemyListBase.m_LevelUI = *(_BYTE *)(v12 + 205);` | 该键作为行偏移 `+0xCD` 处的 字节/布尔 槽位读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxHP` | `C-ENEMY-02:17647313` `cEnemyListBase.m_MaxHP = *(_DWORD *)(v12 + 56);` | 该键作为行偏移 `+0x38` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSLD` | `C-ENEMY-02:17647312` `cEnemyListBase.m_MaxSLD = *(_DWORD *)(v12 + 52);` | 该键作为行偏移 `+0x34` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSP` | `C-ENEMY-02:17647314` `cEnemyListBase.m_MaxSP = *(_DWORD *)(v12 + 60);` | 该键作为行偏移 `+0x3C` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSpecialSkillValue` | `C-ENEMY-02:17647295` `*(_QWORD *)&cEnemyListBase.m_MaxSpecialSkillValue = 0;`<br>`C-ENEMY-02:17647356` `cEnemyListBase.m_MaxSpecialSkillValue = *(_DWORD *)(v12 + 236);` | 该键作为行偏移 `+0xEC` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Money` | `C-ENEMY-02:17647333` `cEnemyListBase.m_Money = *(_DWORD *)(v12 + 144);` | 该键作为行偏移 `+0x90` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NameUI` | `C-ENEMY-02:17647346` `cEnemyListBase.m_NameUI = *(_BYTE *)(v12 + 204);` | 该键作为行偏移 `+0xCC` 处的 字节/布尔 槽位读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NotAction` | `C-ENEMY-02:17647358` `cEnemyListBase.m_NotAction = *(_BYTE *)(v12 + 241);` | `EnemyID +0xF1` 处的 字节 会初始化 `AQPBattleCharacterBase::m_IsNotAction`；战斗-事件 指令 可通过 `DisableNotAction` 清除它，因此该行值是初始不可行动状态。在这个 C 导出 中，活 标志 为 布尔 类型。 | 已复原 | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_OutsideTarget` | `C-ENEMY-02:17647357` `cEnemyListBase.m_OutsideTarget = *(_BYTE *)(v12 + 240);` | `EnemyID +0xF0` 处的 字节 会初始化 `AQPBattleCharacterBase::m_IsOutsideTarget`；之后的目标选择代码会查询或清除该运行时 标志，因此该 DB 字段是初始状态种子，而不是永久不可变属性。 | 已复原 | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_PetExp` | `C-ENEMY-02:17647334` `cEnemyListBase.m_PetExp = *(_DWORD *)(v12 + 148);` | 该键作为行偏移 `+0x94` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_RaceIndices` | `C-ENEMY-02:17647351` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_RaceIndices,`<br>`C-ENEMY-02:17647368` `Data = cEnemyListBase.m_RaceIndices.AllocatorInstance.Data;` | `m_RaceIndices` 是 `EnemyID +0xD0` 处的 `TArray<int>`。运行时成员关系不是标量相等；`HasEnemyRace` 会遍历数组，只有任一元素等于请求的 种族 ID 时才成功。 | 已复原 | `C-ENEMY-02`, `C-ENEMY-06` |
| `m_ReduceSpecialSkillValueBreak` | `C-ENEMY-02:17647355` `cEnemyListBase.m_ReduceSpecialSkillValueBreak = *(_DWORD *)(v12 + 232);` | 该键作为行偏移 `+0xE8` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilment` | `C-ENEMY-02:17647292` `memset(&cEnemyListBase.m_ResistAilment, 0, 25);`<br>`C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);`<br>`C-ENEMY-02:17647327` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_ResistAilment,` | 该键作为行偏移 `+0x70` 处的 `TArray` 头读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilmentID` | `C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);` | 该键作为行偏移 `+0x68` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilments` | 供应的 C 导出 中未复原出精确具名字段或无歧义偏移槽位。 | C 导出 中未复原出精确的 `m_ResistAilments` 符号或无歧义的资产专用偏移。因此该字段记录为未复原，而不是推断出来的字段。 | 否定性/部分复原 | not 已复原 |
| `m_ShieldUI` | `C-ENEMY-02:17647349` `cEnemyListBase.m_ShieldUI = *(_BYTE *)(v12 + 207);` | 该键作为行偏移 `+0xCF` 处的 字节/布尔 槽位读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | 该键作为行偏移 `+0x84` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StatusOffset` | `C-ENEMY-02:17647343` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_StatusOffset,`<br>`C-ENEMY-02:17647369` `v8 = cEnemyListBase.m_StatusOffset.AllocatorInstance.Data;` | 该键作为行偏移 `+0xB8` 处的 `TArray` 头读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealLeafNum` | `C-ENEMY-02:17647338` `cEnemyListBase.m_StealLeafNum = *(_DWORD *)(v12 + 164);` | 该键作为行偏移 `+0xA4` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealReward` | `C-ENEMY-02:17647337` `cEnemyListBase.m_StealReward = *(_DWORD *)(v12 + 160);` | 该键作为行偏移 `+0xA0` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_TacticalAssignID` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | 该键作为行偏移 `+0x80` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_UnknownLv` | `C-ENEMY-02:17647359` `cEnemyListBase.m_UnknownLv = *(_BYTE *)(v12 + 242);` | 该键作为行偏移 `+0xF2` 处的 字节/布尔 槽位读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | 这不是 `EnemyID` 行字段。运行时会解析 `EnemyID.m_TypeID`，然后从 `EnemyTypeID +0x80` 读取 `m_VoiceSymbol`。 | 已复原 | `C-TYPE-02`, `C-TYPE-03` |
| `m_WeakID` | `+0x1C`（标量）；引用块中没有保留下更短的具名赋值 | 该键作为行偏移 `+0x1C` 处的 32 位标量读取。`UEnemyDB::GetEnemyDB` 以 `0xF8` 行跨度解析该行；`ASymbolEnemy::GetTargetLevel` 会显式实体化这些偏移。 | 已复原 | `C-ENEMY-01`, `C-ENEMY-02` |

### ■ 追加运行时证据：直接 `EnemyID` 访问器、状态类型 映射与反向索引

`EnemyID` 表并不只是作为整行复制。战斗与 UI 代码中有很大一部分会通过小型访问器读取各个字段。这些访问器可作为行偏移的有用独立核对，也揭示了哪些字段会被作为标量值、数组或二级表 ID 查询。

```cpp
// C export lines 17644827-17644869
EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
if ( EnemyDB )
{
  v10 = *(_DWORD *)&EnemyDB[20];
  if ( v10 > 0
    && AGameTextManager::m_Self
    && UObjectBase::IsValidLowLevelFast(AGameTextManager::m_Self, 1) )
  {
    GameText = AGameTextManager::GetGameText(AGameTextManager::m_Self, &result, 4u, v10, 0);
    ...
    v7 = 1;
  }
  if ( withID )
  {
    v12 = *(_DWORD *)&v9[24];
    if ( v12 > 0 )
    {
      v13 = L"T#";
      if ( EnemyID > 0 )
        v13 = L"E#";
      ...
    }
  }
}
```

```cpp
// C export lines 17645143-17645194
__int64 __fastcall UEnemyDB::GetEnemyStatus(unsigned int EnemyID, unsigned __int8 StatusType)
{
  EnemyDB = (unsigned int *)UEnemyDB::GetEnemyDB(EnemyID);
  if ( !EnemyDB )
    return 0;
  switch ( StatusType )
  {
    case 1:  return EnemyDB[14];
    case 2:  return EnemyDB[15];
    case 4:  return EnemyDB[13];
    case 9:  return EnemyDB[16];
    case 10: return EnemyDB[17];
    case 11: return EnemyDB[18];
    case 12: return EnemyDB[19];
    case 13: return EnemyDB[20];
    case 14: return EnemyDB[21];
    case 15: return EnemyDB[23];
    case 16: return EnemyDB[24];
    default: return 0;
  }
}
```

```cpp
// C export lines 17645232-17645274
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyStealItemDropID(unsigned int EnemyID)
{
  result = UEnemyDB::GetEnemyDB(EnemyID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[160];
  return result;
}

bool __fastcall UEnemyDB::GetEnemyStealLeafData(int *LeafNum, unsigned int EnemyID)
{
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
  {
    v4 = *(_DWORD *)&EnemyDB[164];
    *LeafNum = v4;
    return v4 > 0;
  }
  *LeafNum = 0;
  return 0;
}
```

```cpp
// C export lines 17647250-17647270
FScriptContainerElement *__fastcall UEnemyDB::GetTacticalAssignID(unsigned int EnemyID)
{
  result = UEnemyDB::GetEnemyDB(EnemyID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[128];
  return result;
}

FScriptContainerElement *__fastcall UEnemyDB::GetTacticalSkillsID(unsigned int EnemyID)
{
  result = UEnemyDB::GetEnemyDB(EnemyID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[132];
  return result;
}
```

```cpp
// C export lines 17647330-17647388
char __fastcall UEnemyDB::HasTacticalSkill(unsigned int EnemyID, int SkillID)
{
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
    v4 = *(_DWORD *)&EnemyDB[132];
  else
    v4 = 0;
  TacticalSkillIndices = UTacticalDB::GetTacticalSkillIndices(v4);
  if ( !TacticalSkillIndices )
    return 0;
  Data = TacticalSkillIndices->AllocatorInstance.Data;
  while ( *(_DWORD *)Data != SkillID )
  {
    Data += 4;
    if ( Data == end )
      return 0;
  }
  return 1;
}
```

| 键或键组 | 运行时解释 |
|---|---|
| `m_DisplayName` | 作为行 `+0x14` 读取；`GetEnemyNameString` 会把它传给 `AGameTextManager::GetGameText(..., 4u, DisplayName, 0)`。因此该路径中的 game 文本 namespace/category 被硬编码为 `4u`。 |
| `m_TypeID` | 作为行 `+0x18` 读取。在 `withID` 调试/display 分支中，仅当没有优先使用正数具体 `EnemyID` 作为前缀时才会使用它；在玩法中，它也是通往 `EnemyTypeID` 的桥。 |
| `m_MaxHP`, `m_MaxSP`, `m_MaxSLD`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_Hit`, `m_Avd` | `GetEnemyStatus` 将 状态 ID 映射到行内 `uint32` 槽。观察到的映射为：`1 -> m_MaxHP`，`2 -> m_MaxSP`，`4 -> m_MaxSLD`，`9 -> m_AtkP`，`10 -> m_DefP`，`11 -> m_AtkM`，`12 -> m_DefM`，`13 -> m_Agi`，`14 -> m_Crt`，`15 -> m_Hit`，`16 -> m_Avd`。`m_CrtDef` 存在于行中，但这个特定 状态类型 switch 不会抵达它。 |
| `m_StealReward` | 作为行 `+0xA0` 读取；布尔重载只有在复制出的 ID 为正时才返回 success。 |
| `m_StealLeafNum` | 作为行 `+0xA4` 读取；布尔重载只有在复制出的叶币数量为正时才返回 success。 |
| `m_TacticalAssignID` | 直接从行 `+0x80` 读取；这是敌人到 tactic 的 赋值 ID。 |
| `m_SkillsID` | 直接从行 `+0x84` 读取；`HasTacticalSkill` 会通过 `UTacticalDB::GetTacticalSkillIndices` 解析该 ID，并比较返回技能数组中的具体 `SkillID` 值。 |

C 导出 还包含按类型与种族成员关系建立反向索引的 helper。这些对 modding 很重要，因为它们表明 `m_TypeID` 与 `m_RaceIndices` 不仅被单个敌人实例使用，也会被全局 查找/缓存 结构使用。

```cpp
// C export lines 17645095-17645106 and 17647421-17647456
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyRaces(unsigned int EnemyID)
{
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  v2 = EnemyDB + 208;
  if ( !EnemyDB )
    return nullptr;
  return v2;
}

char __fastcall UEnemyDB::HasEnemyRace(int EnemyID, EENEMY_RACE_ID RaceID)
{
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  v4 = EnemyDB + 208;
  if ( !v4 )
    return 0;
  v5 = *(_DWORD **)v4;
  v6 = *(_QWORD *)v4 + 4LL * *(int *)&v4[8];
  while ( *v5 != RaceID )
  {
    if ( ++v5 == (_DWORD *)v6 )
      return 0;
  }
  return 1;
}
```

```cpp
// C export lines 17646855-17646911
for ( i = (__int64)&EnemiesDB->AllocatorInstance.Data[248 * EnemiesDB->ArrayNum];
      Data != (FScriptContainerElement *)i;
      Data += 248 )
{
  v44 = *(_DWORD **)&Data[208];
  v45 = v44;
  v46 = &v44[*(int *)&Data[216]];
  if ( v44 != v46 )
  {
    while ( *v44 != RaceID )
    {
      if ( ++v44 == v46 )
        goto LABEL_86;
    }
    ...
    *(_DWORD *)&UEnemyDB::s_EnemyIndices.AllocatorInstance.Data[4 * ArrayNum] = *(_DWORD *)&Data[8];
  }
}
```

```cpp
// C export lines 17647118-17647220, reduced to the initial uncached scan
const TArray<int,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetSameTypeEnemies(int TypeID)
{
  EnemiesDB = UEnemyDB::GetEnemiesDB();
  if ( EnemiesDB )
  {
    ...
    for ( i = (__int64)&EnemiesDB->AllocatorInstance.Data[248 * EnemiesDB->ArrayNum];
          Data != (FScriptContainerElement *)i;
          Data += 248 )
    {
      if ( *(_DWORD *)&Data[24] == TypeID )
      {
        ...
        *(_DWORD *)&UEnemyDB::s_EnemyIndices.AllocatorInstance.Data[4 * ArrayNum] = *(_DWORD *)&Data[8];
      }
    }
  }
  return &UEnemyDB::s_EnemyIndices;
}
```

| 键 | 运行时解释 |
|---|---|
| `m_RaceIndices` | 存储为行拥有的数组，位于 `+0xD0/+0xD8`。`HasEnemyRace` 是数组成员测试，而 `GetSameRaceEnemies` 会通过扫描该字段来构建 敌人 ID 结果数组。因此它不是标量 种族 enum。 |
| `m_TypeID` | `GetSameTypeEnemies` 会扫描所有 `EnemyID` 行并按行 `+0x18` 对敌人分组，证明 类型 ID 不只是表现指针，也是全局分类键。 |


#### ■ 作为战斗角色种子记录的 `EnemyID`

前面的字段表确立了行布局。以下追加路径是对 modding 重要的高层运行时语义：敌人构建、UI 可见性、能力值 查找、等级/名称本地化、弱点 标志 初始化、掉落/偷取、种族/类型反向扫描、特殊槽变动以及初始状态 标志。在这些路径中，游戏把 `EnemyID` 当作初始化 `AQPBattleCharacterBase` 实例的种子；若干字段会被复制进运行时状态一次，随后独立变动。

##### ◆ C-ENEMY-CONSTRUCTION-01：敌人构建会植入表现、弱点 标志、参数、immortal/outside/no-动作 状态

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413017-17413070
AQPBattleCharacterBase::SetEnemyFlag(this, 1);
v13 = HomePos;
v14 = 0;
this->m_CharacterID = EnemyID;
this->m_SameIndex = 0;
this->m_TargetPriority = TargetPriority;
this->m_UseAnimScale = 0;
*(_OWORD *)&this->m_HomePos.X = *(_OWORD *)&v13->X;
Z = v13->Z;
m_AnimCharacter = this->m_AnimCharacter;
this->m_HomePos.Z = Z;
this->m_nSortIndex = Index;
pAnim = m_AnimCharacter;
v17 = m_AnimCharacter && (m_AnimCharacter->ObjectFlags & 0x40000000) == 0
   || AQPBattleCharacterBase::GetAnimCharacter(this, &pAnim, 1);
EnemyTypeFlipbookSetNum = UEnemyDB::GetEnemyTypeFlipbookSetNum(*(_DWORD *)&v12[24]);
for ( i = 0; i < EnemyTypeFlipbookSetNum; ++i )
  this->m_UseEnemyFlipbook |= AQPBattleCharacterBase::SetupEnemyFlipbook(this, i, EnemyID, 0);
AQPBattleCharacterBase::LoadOverrideOrderIcon(this);
this->m_IsEnemyPlayingDefeatEffect = 0;
EnemyTypeDefeatEffectID = UEnemyDB::GetEnemyTypeDefeatEffectID(*(_DWORD *)&v12[24]);
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413043-17413070
if ( this->m_EnemyFlag )
{
  m_CharacterID = this->m_CharacterID;
  if ( m_CharacterID > 0 )
  {
    v22 = 0;
    MaxWeaponType = USkillDB::GetMaxWeaponType();
    for ( j = 0; j < MaxWeaponType; ++j )
    {
      if ( (unsigned __int8)AQPBattleManager::GetEnemyWeakFlags(m_CharacterID, 0, j) )
        v22 |= 1 << j;
    }
    this->m_WeakWeaponFlags = v22;
    MaxMagicType = USkillDB::GetMaxMagicType();
    for ( k = 0; k < MaxMagicType; ++k )
    {
      if ( (unsigned __int8)AQPBattleManager::GetEnemyWeakFlags(m_CharacterID, 1, k) )
        v14 |= 1 << k;
    }
    this->m_WeakMagicFlags = v14;
  }
}
AQPBattleCharacterBase::SetupParams(this, v29, isSkipDebugName);
AQPBattleCharacterBase::SetImmortal(this, *(_BYTE *)&v12[243]);
this->m_IsOutsideTarget = (bool)v12[240];
this->m_IsNotAction = (bool)v12[241];
```

解释：`SetupEnemy` 会通过 `UEnemyDB::GetEnemyDB` 读取一次 `EnemyID`；`v12` 是已解析的行。随后该行用于植入持久化战斗角色字段。`m_TypeID` 驱动表现设置。这里不会直接复制 `m_WeakID`；相反，弱点 标志 通过对每个武器/魔法索引查询 `AQPBattleManager::GetEnemyWeakFlags` 来计算，而该路径最终会解析弱点表。`m_Immortal`、`m_OutsideTarget` 与 `m_NotAction` 是 `+0xF3`、`+0xF0` 与 `+0xF1` 处的初始状态种子。植入后，活角色字段可以被 技能/事件 独立修改。

##### ◆ C-ENEMY-STATE-RESET-02：`SetupParams` 会在应用敌人行 标志 前重置活状态

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17413416-17413437
this->m_BattleWorkSlot = Index;
this->m_PartsOwner = nullptr;
this->m_PartsID = 0;
this->m_AnimationLinkOwner = nullptr;
*(_WORD *)&this->m_IsOutsideTarget = 0;
this->m_EnemyPartsFlag = 0;
IsEmptyHP = AQPBattleCharacterBase::IsEmptyHP(this);
AQPBattleCharacterBase::SetAliveFlag(this, !IsEmptyHP, 1);
AQPBattleCharacterBase::SetImmortal(this, 0);
AQPBattleCharacterBase::SetTemporaryHP(this, 0);
this->m_SP_Overflowed = 0;
*(_WORD *)&this->m_RevivedNow = 0;
*(_WORD *)&this->m_IsBreaking = 0;
this->m_BaseOrderPriority = 0;
*(_QWORD *)&this->m_CurrentOrderPriority = 0;
```

解释：`SetupParams` 会刻意在 `SetupEnemy` 写入 DB 植入值之前清除 `m_IsOutsideTarget`、相邻 no-动作 字节 与 immortality。这个顺序很重要：DB 字段并不表示不可变谓词；它们会在通用重置后初始化角色的起始战斗状态。后续代码可以清除或设置同一运行时 标志，而不改变数据库行。

##### ◆ C-ENEMY-DISPLAY-TEXT-03：显示名称 与 等级 字符串是不同机制

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645067-17645107
EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
v9 = EnemyDB;
if ( EnemyDB )
{
  v10 = *(_DWORD *)&EnemyDB[20];
  if ( v10 > 0
    && AGameTextManager::m_Self
    && UObjectBase::IsValidLowLevelFast(AGameTextManager::m_Self, 1)
    && AGameTextManager::m_Self
    && (AGameTextManager::m_Self->ObjectFlags & 0x40000000) == 0 )
  {
    GameText = AGameTextManager::GetGameText(AGameTextManager::m_Self, &result, 4u, v10, 0);
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644715-17644755
int __fastcall UEnemyDB::GetEnemyLevel(int EnemyID)
{
  ...
  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
  {
    v3 = (FString *)&EnemyDB[32];
    if ( &str != v3 )
    {
      ArrayNum = v3->Data.ArrayNum;
      Data = v3->Data.AllocatorInstance.Data;
      str.Data.ArrayNum = ArrayNum;
      if ( (_DWORD)ArrayNum )
      {
        TArray<FFloat16,TSizedDefaultAllocator<32>>::ResizeForCopy(
          (TArray<FFloat16,TSizedDefaultAllocator<32> > *)&str,
          ArrayNum,
          str.Data.ArrayMax);
        memcpy_0(str.Data.AllocatorInstance.Data, Data, 2 * ArrayNum);
      }
    }
  }
  v6 = &InExpression;
  if ( str.Data.ArrayNum )
    v6 = (const wchar_t *)str.Data.AllocatorInstance.Data;
  result = _wtoi(v6);
```

解释：`m_DisplayName` 是行 `+0x14`，并通过 `GameText` category `4` 解析，因此它是本地化 ID，而不是原始字符串。`m_DisplayLevel` 是行 `+0x20`，是 `FString`/UTF-16 数组，`GetEnemyLevel` 会用 `_wtoi` 解析它。这解释了为什么非数字 display-等级 字符串在角色等级消费者中会变成 `0`，即使行实体化器保留了该文本。

##### ◆ C-ENEMY-STATUS-SWITCH-04：能力值 查找是 状态-ID switch，而不是顺序暴露数组

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645143-17645194
__int64 __fastcall UEnemyDB::GetEnemyStatus(unsigned int EnemyID, unsigned __int8 StatusType)
{
  int v2; // edi
  unsigned int v3; // ebx
  unsigned int *EnemyDB; // rax
  __int64 result; // rax

  v2 = StatusType;
  v3 = 0;
  EnemyDB = (unsigned int *)UEnemyDB::GetEnemyDB(EnemyID);
  if ( !EnemyDB )
    return v3;
  switch ( v2 )
  {
    case 1:
      result = EnemyDB[14];
      break;
    case 2:
      result = EnemyDB[15];
      break;
    case 4:
      result = EnemyDB[13];
      break;
    case 9:
      result = EnemyDB[16];
      break;
    case 10:
      result = EnemyDB[17];
      break;
    case 11:
      result = EnemyDB[18];
      break;
    case 12:
      result = EnemyDB[19];
      break;
    case 13:
      result = EnemyDB[20];
      break;
    case 14:
      result = EnemyDB[21];
      break;
    case 15:
      result = EnemyDB[23];
      break;
    case 16:
      return EnemyDB[24];
```

解释：主标量 能力值 访问器会把类似 `ECHARA_STATUS` 的 ID 映射到行内 dword。映射为：`1 -> m_MaxHP`，`2 -> m_MaxSP`，`4 -> m_MaxSLD`，`9 -> m_AtkP`，`10 -> m_DefP`，`11 -> m_AtkM`，`12 -> m_DefM`，`13 -> m_Agi`，`14 -> m_Crt`，`15 -> m_Hit`，以及 `16 -> m_Avd`。`m_CrtDef` 存在于 `+0x58` 并会被复制/实体化，但这个特定访问器会跳过它。因此不要仅因某个 能力值 字段物理上相邻，就推断每个 能力值 字段都能通过 `GetEnemyStatus` 访问。

##### ◆ C-ENEMY-UI-FLAGS-05：UI 标志 会被分别读取；护盾显示还需要活护盾值

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17648627-17648667
bool __fastcall UEnemyDB::IsDisplaySpecialSkillGauge(unsigned int EnemyID)
{
  FScriptContainerElement *EnemyDB; // rax

  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  return EnemyDB && *(_BYTE *)&EnemyDB[224];
}

//----- (0000000144609070) ----------------------------------------------------
__int64 __fastcall UEnemyDB::IsDrawEnemyHpUI(unsigned int EnemyID)
{
  FScriptContainerElement *EnemyDB; // rax

  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
    return (unsigned __int8)EnemyDB[206];
  else
    return 1;
}

//----- (00000001446090A0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::IsDrawEnemyLevelUI(unsigned int EnemyID)
{
  FScriptContainerElement *EnemyDB; // rax

  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
    return (unsigned __int8)EnemyDB[205];
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17409113-17409125
bool __fastcall AQPBattleCharacterBase::IsDrawEnemyShieldUI(AQPBattleCharacterBase *this)
{
  unsigned int m_CharacterID; // ecx
  bool result; // al

  if ( this->m_EnemyFlag )
    m_CharacterID = this->m_CharacterID;
  else
    m_CharacterID = 0;
  result = UEnemyDB::IsDrawEnemyShieldUI(m_CharacterID);
  if ( result )
    return AQPBattleCharacterBase::HasSLD(this);
  return result;
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:18077949-18077978
IsDrawEnemyLevelUI = AQPBattleCharacterBase::IsDrawEnemyLevelUI(Character);
m_LevelName = v8->m_LevelName;
v34 = IsDrawEnemyLevelUI;
LOBYTE(v34) = 2 * IsDrawEnemyLevelUI;
if ( m_LevelName )
{
  UWidget::IsVisible(v8->m_LevelName, v31, v32);
  m_LevelName->SetVisibility(m_LevelName, (ESlateVisibility)(v34 + 2));
}
...
m_GaugeHP = v8->m_GaugeHP;
if ( m_GaugeHP && (m_GaugeHP->ObjectFlags & 0x40000000) == 0 )
{
  v37 = AQPBattleCharacterBase::IsDrawEnemyHpUI(Character) + 1;
  UWidget::IsVisible(m_GaugeHP, v38, v39);
  m_GaugeHP->SetVisibility(m_GaugeHP, (ESlateVisibility)(2 * v37));
}
```

解释：`m_HpUI`、`m_LevelUI`、`m_ShieldUI` 与 `m_DisplaySpecialSkillGauge` 分别有 `+0xCE`、`+0xCD`、`+0xCF` 与 `+0xE0` 的独立行读取。如果行缺失，HP/等级/shield 函数默认可见（`1`），但 special-量表 显示默认 false。护盾显示还会由 `HasSLD(this)` 额外 门控，因此当活护盾状态不存在时，仅设置 `m_ShieldUI=true` 并不足够。

##### ◆ C-ENEMY-SPECIAL-GAUGE-06：敌人 special-量表 字段在变动时读取，而不只是复制进 setup 状态

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17437883-17437905
if ( this->m_EnemyFlag )
{
  EnemyID = AQPBattleCharacterBase::GetEnemyID(this);
  MaxSpecialSkillValue = (unsigned int)UEnemyDB::GetMaxSpecialSkillValue(EnemyID);
}
else
{
  MaxSpecialSkillValue = UBattleUtility::GetMaxSpecialSkillGaugeValue();
}
v7 = SpecialSkillGaugeValue + Add;
v8 = MaxSpecialSkillValue;
AQPBattleCharacterBase::SetSpecialSkillGaugeValue(this, v7);
return SpecialSkillGaugeValue < v8 && v7 >= v8;
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17452396-17452408
void __fastcall AQPBattleCharacterBase::ReduceEnemySpecialSkillGaugeValueBreak(AQPBattleCharacterBase *this)
{
  unsigned int EnemyID; // eax
  unsigned int ReduceSpecialSkillValueBreak; // eax

  if ( this->m_EnemyFlag )
  {
    EnemyID = AQPBattleCharacterBase::GetEnemyID(this);
    ReduceSpecialSkillValueBreak = (unsigned int)UEnemyDB::GetReduceSpecialSkillValueBreak(EnemyID);
    AQPBattleCharacterBase::AddSpecialSkillGaugeValue(this, -ReduceSpecialSkillValueBreak);
  }
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17452500-17452514
void __fastcall AQPBattleCharacterBase::RepairEnemySpecialSkillGaugeValueOverTurn(AQPBattleCharacterBase *this)
{
  unsigned int EnemyID; // eax
  int SpecialSkillValueOrverTurn; // eax

  if ( this->m_EnemyFlag )
  {
    EnemyID = AQPBattleCharacterBase::GetEnemyID(this);
    SpecialSkillValueOrverTurn = (unsigned int)UEnemyDB::GetGenerateSpecialSkillValueOrverTurn(EnemyID);
    AQPBattleCharacterBase::AddSpecialSkillGaugeValue(this, SpecialSkillValueOrverTurn);
  }
}
```

解释：`m_MaxSpecialSkillValue`、`m_ReduceSpecialSkillValueBreak` 与 `m_GenerateSpecialSkillValueOrverTurn` 是活动运行时参数。它们不只是复制进已构建敌人。量表 变动例程会在增加、破防减少或每回合修复时重新查询 `EnemyID` 行。这意味着改变这些 DB 值会影响调用这些 取值函数 的公式路径，而当前活 量表 值仍然是单独的角色/存档状态。

##### ◆ C-ENEMY-REWARD-DROP-STEAL-07：奖励、掉落与偷取是带正 ID/count 门控 的直接行读取

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644355-17644364
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyExp(unsigned int EnemyID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyDB(EnemyID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[140];
  return result;
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645243-17645261
bool __fastcall UEnemyDB::GetEnemyStealItemDropID(int *DropID, unsigned int EnemyID)
{
  FScriptContainerElement *EnemyDB; // rax
  int v4; // eax

  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
  {
    v4 = *(_DWORD *)&EnemyDB[160];
    *DropID = v4;
    return v4 > 0;
  }
  else
  {
    *DropID = 0;
    return 0;
  }
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645274-17645292
bool __fastcall UEnemyDB::GetEnemyStealLeafData(int *LeafNum, unsigned int EnemyID)
{
  FScriptContainerElement *EnemyDB; // rax
  int v4; // eax

  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( EnemyDB )
  {
    v4 = *(_DWORD *)&EnemyDB[164];
    *LeafNum = v4;
    return v4 > 0;
  }
  else
  {
    *LeafNum = 0;
    return 0;
  }
}
```

解释：奖励 与 偷取 字段不是仅存在于 结构定义 中。`m_Exp`、`m_Money` 与 `m_JP` 在 `+0x8C`、`+0x90` 与 `+0x98` 处有简单标量 取值函数。`+0x9C` 处的 `m_DropReward` 是 `EnemyDropID` 行 ID。`+0xA0` 处的 `m_StealReward` 与 `+0xA4` 处的 `m_StealLeafNum` 具有布尔重载，只有复制出的值为正时才返回 false 以外的结果，因此 `0` 与负值在这些重载中语义上表示“不存在”。

##### ◆ C-ENEMY-REVERSE-INDEX-08：`m_RaceIndices` 与 `m_TypeID` 支持跨所有敌人行的反向扫描

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17646816-17646870
FScriptContainerElement *__fastcall UEnemyDB::GetSameRaceEnemies(int RaceID, int RaceID2, int RaceID3)
{
  ...
  EnemiesDB = UEnemyDB::GetEnemiesDB();
  ...
  Data = EnemiesDB->AllocatorInstance.Data;
  v9 = &Data[248 * EnemiesDB->ArrayNum];
  if ( Data != v9 )
  {
    do
    {
      if ( *(int *)&Data[8] > 0 )
      {
        if ( (unsigned __int8)UEnemyDB::HasEnemyRace(*(_DWORD *)&Data[8], RaceID)
          || (unsigned __int8)UEnemyDB::HasEnemyRace(*(_DWORD *)&Data[8], RaceID2)
          || (unsigned __int8)UEnemyDB::HasEnemyRace(*(_DWORD *)&Data[8], RaceID3) )
        {
          v10 = v7;
          v7 = (FScriptContainerElement *)(v7 + 1);
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17647118-17647163
const TArray<int,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetSameTypeEnemies(int TypeID)
{
  ...
  Data = EnemiesDB->AllocatorInstance.Data;
  v5 = &Data[248 * EnemiesDB->ArrayNum];
  if ( Data != v5 )
  {
    do
    {
      if ( *(int *)&Data[8] > 0 && *(_DWORD *)&Data[24] == TypeID )
      {
        v6 = v2;
        v2 = (FScriptContainerElement *)(v2 + 1);
```

解释：`m_RaceIndices` 不是标量 种族 字段。种族成员关系通过 `HasEnemyRace(enemyId, raceId)` 测试，然后用于构建派生的 敌人 ID 数组。同样，`m_TypeID` 不只是表现指针；`GetSameTypeEnemies` 会对所有 `EnemyID` 行执行完整扫描，并将行 `+0x18` 与请求的 类型 ID 比较。这使 `m_TypeID` 既是表现链接，也是类型分类键。

##### ◆ C-ENEMY-OFFSETMAP-09：紧凑 `EnemyID` 行布局与消费者分类

| 偏移 | 键 | 证据等级 | 运行时解释 |
|---:|---|---|---|
| `+0x0C` | `m_Label` | label 索引 / 查找 proof | 由 `FDbAccessHelper` label maps 使用；label-to-ID 查找会返回行 `m_id`。 |
| `+0x14` | `m_DisplayName` | 直接读取方 | Game-文本 ID，解析为 `AGameTextManager::GetGameText(..., 4u, DisplayName, 0)`。 |
| `+0x18` | `m_TypeID` | 直接读取方 | 通往所有 `EnemyTypeID` 表现数据的桥；也由 同类型 反向扫描使用。 |
| `+0x1C` | `m_WeakID` | 直接读取方 via 弱点-标志/resist paths | 未提供显式 弱点 覆盖 时的默认弱点行。 |
| `+0x20` | `m_DisplayLevel` | 直接读取方 | 以 `_wtoi` 解析的 UTF-16 字符串；在行中不是普通整数。 |
| `+0x30/+0x64` | `m_BreakRate`, `m_DamageRate` | 直接取值函数/已实体化标量 | 伤害倍率参数；`m_BreakRate` 有直接 取值函数。 |
| `+0x34..+0x60` | `m_MaxSLD`, `m_MaxHP`, `m_MaxSP`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_CrtDef`, `m_Hit`, `m_Avd` | direct 状态 switch plus 实体化 | 大多数字段由 `GetEnemyStatus` 返回；`m_CrtDef` 会实体化，但不会被所示 switch 抵达。 |
| `+0x68/+0x70` | `m_ResistAilmentID`, `m_ResistAilment` | 已实体化/相邻技能抗性证据 | 真实行字段；准确的 异常抗性 消费者不如 弱点/resist 消费者直接。 |
| `+0x80/+0x84` | `m_TacticalAssignID`, `m_SkillsID` | 直接取值函数与战术技能成员关系 | 战术 与 技能列表 ID；`m_SkillsID` 通过 `TacticalSkillList` 解析。 |
| `+0x88` | `m_IsBoss` | 直接取值函数/已实体化字节 | Boss 标志；由 boss-条件 与表现路径使用，但行为仍依赖上下文。 |
| `+0x8C/+0x90/+0x94/+0x98` | `m_Exp`, `m_Money`, `m_PetExp`, `m_JP` | Exp/Money/JP 的直接取值函数；已实体化 `PetExp` | 结果奖励；`PetExp` 仍为实体化字段，玩法证据不完整。 |
| `+0x9C/+0xA0/+0xA4` | `m_DropReward`, `m_StealReward`, `m_StealLeafNum` | 直接读取方 | 掉落表 ID 与偷取值；偷取 重载要求正值。 |
| `+0xA8` | `m_EventDropRewards` | 直接数组取值函数与掉落覆盖证据 | 事件-掉落 覆盖数组；与 掉落结果 逻辑一起消费。 |
| `+0xB8` | `m_StatusOffset` | 直接 UI 偏移取值函数 | 用于战斗状态显示放置的 two-浮点数 向量。 |
| `+0xC8` | `m_Bottle` | 已实体化/群组槽位关联证据 | 真实行标量；当前最佳解释仍为 multipart ordinal / panel-part 索引。 |
| `+0xCC/+0xCD/+0xCE/+0xCF` | `m_NameUI`, `m_LevelUI`, `m_HpUI`, `m_ShieldUI` | 等级/HP/shield 的直接读取方；名称 读取方不完整 | Level/HP/shield 会改变 UI 可见性；`m_NameUI` 会实体化，但在已复原 family 中缺少同样的直接 取值函数 模式。 |
| `+0xD0` | `m_RaceIndices` | 直接成员关系与反向扫描读取方 | 种族 ID 数组，而不是标量 enum。 |
| `+0xE0/+0xE4/+0xE8/+0xEC` | `m_DisplaySpecialSkillGauge`, `m_GenerateSpecialSkillValueOrverTurn`, `m_ReduceSpecialSkillValueBreak`, `m_MaxSpecialSkillValue` | 直接 UI 与变动读取方 | 敌人 BP/special-量表 显示与变动参数。 |
| `+0xF0/+0xF1/+0xF2/+0xF3` | `m_OutsideTarget`, `m_NotAction`, `m_UnknownLv`, `m_Immortal` | outside/not-动作/immortal 的直接设置种子; 已实体化 `UnknownLv` plus UI-路径 evidence from prior notes | 初始战斗角色 标志。Outside/not-动作/immortal 会在 `SetupParams` 重置后被显式植入；除非测试 等级 UI 路径，否则应保守看待 `UnknownLv` 的准确消费者。 |


### ■ (**) `EnemyReinforcements`
这是战斗遭遇 helper 表，用于定义战斗中途可以生成哪些额外敌人（reinforcements）以及它们应出现在哪里。
- `m_Reinforcements` 以 `EnemyID.m_id` 为输入。
- `m_Positions` 以 `BattlePositions.m_id` 为输入（相应敌人组的 formation 不一定需要指定该位置）。
唯一使用 `EnemyReinforcements.m_id` 的资产是 `Skill/` 配下的 `SkillAvailID`。
#### ■ 运行时读取语义
##### ◆ C-REINF-01：EnemyReinforcements 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17646652-17646745
//----- (0000000144607000) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetReinforcementDB(unsigned int ReinforcementID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_EnemyReinforcements.m_Loaded )
  {
    return nullptr;
  }
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v2 = ADatabaseDefineStatic::m_Self;
  else
    v2 = nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_EnemyReinforcements.m_DBAccess,
                 ReinforcementID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_EnemyReinforcements.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_EnemyReinforcements.m_DataList.AllocatorInstance.Data[56 * ArrayIndex];
}

//----- (00000001446070A0) ----------------------------------------------------
const TArray<int,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetReinforcementIndices(int EnemyID)
{
  FScriptContainerElement *EnemyDB; // rax
  int v3; // ecx
  const TArray<int,TSizedDefaultAllocator<32> > *TacticalSkillIndices; // rax
  int *Data; // r12
  __int64 v6; // r13
  const TArray<int,TSizedDefaultAllocator<32> > *SkillAvailIndices; // rax
  unsigned int *v8; // r14
  __int64 v9; // r15
  const FSkillAvailIDBase *SkillAvailDB; // rax
  signed int AvailReinforcementID; // edi
  ADatabaseDefineStatic *v12; // rbx
  int ArrayIndex; // eax
  __int64 v14; // rcx
  int *v15; // rdi
  int *v16; // rbp
  int ArrayNum; // esi
  int v18; // ebx
  FScriptContainerElement *v19; // rax
  FScriptContainerElement *v20; // rdx

  UEnemyDB::s_EnemyIndices.ArrayNum = 0;
  if ( UEnemyDB::s_EnemyIndices.ArrayMax < 0 )
    TArray<long,TSizedDefaultAllocator<32>>::ResizeTo(
      (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&UEnemyDB::s_EnemyIndices,
      0);
  if ( EnemyID > 0 )
  {
    EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
    if ( EnemyDB )
    {
      v3 = *(_DWORD *)&EnemyDB[132];
      if ( v3 > 0 )
      {
        TacticalSkillIndices = UTacticalDB::GetTacticalSkillIndices(v3);
        if ( TacticalSkillIndices )
        {
          Data = (int *)TacticalSkillIndices->AllocatorInstance.Data;
          v6 = (__int64)&TacticalSkillIndices->AllocatorInstance.Data[4 * TacticalSkillIndices->ArrayNum];
          if ( TacticalSkillIndices->AllocatorInstance.Data != (FScriptContainerElement *)v6 )
          {
            do
            {
              if ( *Data <= 0 )
                break;
              SkillAvailIndices = USkillDB::GetSkillAvailIndices(*Data);
              if ( SkillAvailIndices )
              {
                v8 = (unsigned int *)SkillAvailIndices->AllocatorInstance.Data;
                v9 = (__int64)&SkillAvailIndices->AllocatorInstance.Data[4 * SkillAvailIndices->ArrayNum];
                if ( SkillAvailIndices->AllocatorInstance.Data != (FScriptContainerElement *)v9 )
                {
                  do
                  {
                    if ( (int)*v8 > 0 )
                    {
                      SkillAvailDB = USkillDB::GetSkillAvailDB(*v8);
                      if ( SkillAvailDB )
                      {
                        AvailReinforcementID = USkillDB::GetAvailReinforcementID(SkillAvailDB);
                        if ( AvailReinforcementID > 0
                          && ADatabaseDefineStatic::m_Self
                          && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
                          && ADatabaseDefineStatic::m_Self
                          && ADatabaseDefineStatic::m_Self->m_EnemyReinforcements.m_Loaded )
                        {
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_Positions` | `row-owned TArray, destructor frees this->m_Positions`（数组）；引用块中没有保留下更短的具名赋值 | 行解析器返回一个 `0x38` 行跨度的 `EnemyReinforcements` 行；析构/复制符号把 `m_Positions` 保留为伴随的行拥有 `TArray`。这里未复原准确的 生成 消费者。 | 否定性/部分复原 | `C-REINF-01` |
| `m_Reinforcements` | `row-owned TArray, destructor frees this->m_Reinforcements`（数组）；引用块中没有保留下更短的具名赋值 | 行解析器返回一个 `0x38` 行跨度的 `EnemyReinforcements` 行；析构/复制符号把 `m_Reinforcements` 保留为行拥有的 `TArray`。除 `SkillAvailID` 以外的准确 gameplay 消费者未在这些片段中复原。 | 否定性/部分复原 | `C-REINF-01` |

### ■ (****) `EnemyGroups`
记录所有敌人组；它们主要决定：
- 哪些 `EnemyID` 会一起生成；
- formation 与 战斗-事件/abort 语境。

要让你的战斗在游戏中触发，必须在这里添加一个条目。

- `m_Label`：为新添加的敌人组确保此 label 唯一/不与既有条目冲突。事件会使用这个 label 触发与敌人组的战斗。如果打算把条目加入 Monster Arena，则在击败它后，该条目将无法再次用于其他目的，否则可能导致 Monster Arena 菜单出现并可能造成崩溃。
- `m_Inescapable`：决定是否能从战斗中逃跑的 标志。
- `m_DisableRetire`：在 OT0 中始终设为 false，似乎在 OT0 中不起作用。可能与 CotC 中战斗期间挂起手机的能力有关？
- `m_SkipResult`：决定是否跳过战斗结果画面（获得的 XP、Money、JP 等）的 标志。
- `m_IgnoreDefeat`：敌人组击败玩家时阻止 Game Over 画面出现的 标志。只由 King Pardis III、Edoras Defenders of Fame/Power/Wealth 与 Colossal Blightant 使用。如果敌人在 Monster Arena 中，似乎会被覆盖，并在战败后把你放回那里。
- `m_SkipBgmResult`：决定是否播放胜利主题的 标志。
- `m_BanGodBeast`：禁用战斗中的 Divine Beast 使用。OT0 未使用。若在 mod 中将 Divine Beast 技能 加到某个角色的 技能 board，可能会阻止这些技能（未测试）。
- `m_PlayerMemberSetID`：强制你的队伍由所引用 `PlayerMemberSetID` 中指定的角色组成（另见 [如何添加自定义角色](customchars_en.md)）。
- `m_Bgm`：设置战斗背景音乐；可用选项见 `Sound/` 配下的 `SoundList`。
- `m_NightBgm`：设置夜间战斗背景音乐。OT0 未使用，未测试是否起作用。
- `m_DarkBgm`：与 `m_NightBgm` 相同？CotC 条目似乎总是在两个键中使用相同值。
- `m_BgmType`：确切用途不明，但 `Kingship_structs.hpp` 定义如下：

```hpp
enum class EBATTLE_BGM_TYPE : uint8
{
	Default                                  = 0,
	EventBattle1                             = 1,
	EventBattle2                             = 2,
	Arena                                    = 3,
	EBATTLE_BGM_MAX                          = 4,
};
```

- `m_ResultBgmWin`：指定自定义胜利主题。
- `m_ResultBgmLose`：指定自定义 Game Over 主题。
- `m_CameraSet`：指定要使用的 摄像机 set（输入：`BattleCameraSet.m_id`）。一些旧笔记称其为 `m_Camera`，但类型化 C 导出 实体化器使用 `m_CameraSet`（`Octopath_Traveler0-Win64-Shipping.exe.c`:17923518-17923533）。
- `m_Formation`：指定敌人组的 formation（输入：`EnemyFormations.m_id`）。
- `m_EnemyID`：指定场上的敌人（输入：`EnemyID.m_id`）。如果把此数组扩展到超过 6 个敌人，游戏不会立即崩溃，但新增敌人不会出现在 timeline 上，缺少弱点/护盾/状态 overlay，并会在获得回合后使游戏崩溃（错误与损坏的 allocator metadata/损坏指针有关）。也许可通过 reinforcement 调用绕过。在记录硬性引擎上限或安全绕过方法之前，应把这当作仍需要当前本地复现的 authoring hazard。

- `m_EventIndices`：战斗开始或过程中播放的（战斗）Events（战斗内对话等）；可用值很可能是 `BattleEventList.m_id`，而不是 `EventList.m_id`。
- `m_AbortCondition`：中止战斗的条件。可用 ID 很可能可在 `BattleAbortConditions` 下找到。
- `m_DefeatPriority`：在 OT0 与 CotC 中始终设为 false。不确定它是否有任何作用。
- `m_ForcedWinAbortCondition`：设置强制战斗获胜的自定义条件。可用 ID 很可能可在 `BattleAbortConditions` 下找到。
- `m_IsAllEnemyDeadOnForceWin`：（未测试）决定在满足 `m_ForcedWinAbortCondition` 中设定条件时，是否所有敌人都会死亡的 标志？
- `m_ForcedLoseAbortCondition`：设置强制战斗失败的自定义条件。可用 ID 很可能可在 `BattleAbortConditions` 下找到。
- `m_ImportantFlags`：六个不同 标志；它们在 OT0 中全部未使用，其含义无法通过测试确定（鉴于该数组与 `m_EnemyID` 长度相同，这六个 标志 之间很可能除了选择应用到场上哪个敌人之外没有差别）。
  - 标志 1：主要由 CotC 的 Job Tower bosses 使用。
  - 标志 2：只由 `Dice_Bolaven_Rank04_BossGr03` 使用（对战 Idore、Roguish Riven Soldier I 与 Roguish Riven Soldier II）。
  - 标志 3：只由与 标志 2 相同的敌人组使用。
  - 标志 4-6：两部游戏中都从未使用。
-  `m_PetDear`：取值为 -1、0 与 1。
-  `m_DisableBattleEndWipe`：由许多最终 boss 使用（Bestower of All、Or'Galdera、Side Solistia 的 Galdera），Magitek Armor 也使用。
-  `m_DisableBattleEndTraining`：防止训练场中的队伍成员获得 XP、JP 等。用于 Emerald Direwolf 战（仅 Laurana 在场时）、Bestower of All 第 7 章中 Sazantos 回忆期间的战斗等。
-  `m_DisableSupporter`：决定是否可以在战斗中召唤 helper 的 标志。
-  `m_EncountEffectTypeID`：指定特殊遭遇特效（例如 FF6 联动遭遇）。竞技场战斗需要将其设为 0，否则会使游戏 soft-锁定。可用 ID 可在 `EncountEffectTypeID` 下找到。SDK 指定了以下特效：0=None，2=Whiteout，3=Blackout。
-  `m_EnablePlayerShield`：在 OT0 与 CotC 中始终设为 false。
-  `m_DisableGrade`：在 OT0 中始终设为 false，在 CotC 中并非如此。可能是忽略武器 grade 的 标志。
-  `m_IsContainRareEnemy`：可能是告知游戏哪些敌人组被视为包含稀有敌人的 标志，从而使提高稀有敌人遭遇率的饰品生效。

#### ■ 运行时读取语义
此处，关于该表的早期 address-等级 笔记被类型化 C 导出 中具名的 `FEnemyGroupsBase`、`UEnemyDB` 与 `ASymbolEnemy` 证据取代。群组 实体化器、群组 解析器、直接标量 取值函数 与符号敌人 桥接 共同给出了行布局与主要消费者。

【主要 C 证据】

##### ◆ C-GROUP-01：EnemyGroups 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644519-17644555
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyGroupDB(int GroupID)
{
  __int64 v1; // rbx
  TArray<FEnemyGroupsBase,TSizedDefaultAllocator<32> > *EnemyGroupsDB; // rdi
  bool IsValidLowLevelFast; // al
  ADatabaseDefineStatic *v5; // rcx
  int ArrayIndex; // eax

  v1 = 0;
  if ( GroupID > 0 )
  {
    EnemyGroupsDB = UEnemyDB::GetEnemyGroupsDB();
    if ( EnemyGroupsDB )
    {
      if ( ADatabaseDefineStatic::m_Self
        && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
        && ADatabaseDefineStatic::m_Self
        && ADatabaseDefineStatic::m_Self->m_EnemyGroups.m_Loaded )
      {
        if ( !ADatabaseDefineStatic::m_Self
          || (IsValidLowLevelFast = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
              v5 = ADatabaseDefineStatic::m_Self,
              !IsValidLowLevelFast) )
        {
          v5 = nullptr;
        }
        ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&v5->m_EnemyGroups.m_DBAccess,
                       GroupID);
        if ( ArrayIndex >= 0 && ArrayIndex < EnemyGroupsDB->ArrayNum )
          return &EnemyGroupsDB->AllocatorInstance.Data[144 * ArrayIndex];
      }
    }
  }
  return (FScriptContainerElement *)v1;
}

```

##### ◆ C-GROUP-02：带具体行偏移的 EnemyGroups 实体化器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17923448-17923558
char __fastcall FDbAccessHelper<FEnemyGroupsBase>::GetData(
        FDbAccessHelper<FEnemyGroupsBase> *this,
        int dbID,
        FEnemyGroupsBase *outData)
{
  int m_IdFlag; // eax
  int v7; // r11d
  FScriptContainerElement *Data; // r8
  TSizedInlineAllocator<1,32,TSizedDefaultAllocator<32> >::ForElementType<FSetElementId> *p_Hash; // rax
  int HashSize; // ecx
  int v11; // edx
  int v12; // eax
  FScriptContainerElement *v13; // rcx
  __int64 v14; // rax
  FScriptContainerElement *v16; // rax
  const TArray<FEnemyGroupsBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v18; // rbx

  m_IdFlag = this->m_IdFlag;
  v7 = -1;
  if ( m_IdFlag <= 0 )
  {
    if ( m_IdFlag < 0 )
    {
      v7 = dbID;
      goto LABEL_16;
    }
    return 0;
  }
  if ( this->m_AccessTable.Pairs.Elements.Data.ArrayNum == this->m_AccessTable.Pairs.Elements.NumFreeIndices )
    return 0;
  Data = this->m_AccessTable.Pairs.Hash.SecondaryData.Data;
  p_Hash = &this->m_AccessTable.Pairs.Hash;
  HashSize = this->m_AccessTable.Pairs.HashSize;
  if ( !Data )
    Data = (FScriptContainerElement *)p_Hash;
  v11 = *(_DWORD *)&Data[4 * (dbID & (HashSize - 1))];
  v12 = v11;
  if ( v11 != -1 )
  {
    v13 = this->m_AccessTable.Pairs.Elements.Data.AllocatorInstance.Data;
    while ( 1 )
    {
      v14 = 2LL * v12;
      if ( *(_DWORD *)&v13[8 * v14] == dbID )
        break;
      v12 = *(_DWORD *)&v13[8 * v14 + 8];
      if ( v12 == -1 )
        return 0;
    }
    while ( 1 )
    {
      v16 = &v13[16 * v11];
      if ( *(_DWORD *)v16 == dbID )
        break;
      v11 = *(_DWORD *)&v16[8];
      if ( v11 == -1 )
      {
        v16 = nullptr;
        break;
      }
    }
    v7 = *(_DWORD *)&v16[4];
  }
LABEL_16:
  if ( v7 < 0 )
    return 0;
  m_DatabasePtr = this->m_DatabasePtr;
  if ( !m_DatabasePtr || v7 >= m_DatabasePtr->ArrayNum )
    return 0;
  v18 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[144 * v7];
  outData->m_id = *(_DWORD *)(v18 + 8);
  outData->m_Label = *(FName *)(v18 + 12);
  outData->m_Inescapable = *(_BYTE *)(v18 + 20);
  outData->m_DisableRetire = *(_BYTE *)(v18 + 21);
  outData->m_SkipResult = *(_BYTE *)(v18 + 22);
  outData->m_IgnoreDefeat = *(_BYTE *)(v18 + 23);
  outData->m_SkipBgmResult = *(_BYTE *)(v18 + 24);
  outData->m_BanGodBeast = *(_BYTE *)(v18 + 25);
  outData->m_PlayerMemberSetID = *(_DWORD *)(v18 + 28);
  outData->m_Bgm = *(_DWORD *)(v18 + 32);
  outData->m_NightBgm = *(_DWORD *)(v18 + 36);
  outData->m_DarkBgm = *(_DWORD *)(v18 + 40);
  outData->m_BgmType = *(_DWORD *)(v18 + 44);
  outData->m_CameraSet = *(_DWORD *)(v18 + 48);
  outData->m_Formation = *(_DWORD *)(v18 + 52);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EnemyID,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 56));
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EventIndices,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 72));
  outData->m_AbortCondition = *(_DWORD *)(v18 + 88);
  outData->m_DefeatPriority = *(_BYTE *)(v18 + 92);
  outData->m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);
  outData->m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);
  outData->m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);
  TArray<unsigned char,TSizedDefaultAllocator<32>>::operator=(
    (TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&outData->m_ImportantFlags,
    (const TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)(v18 + 112));
  outData->m_PetDear = *(_DWORD *)(v18 + 128);
  outData->m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);
  outData->m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);
  outData->m_DisableSupporter = *(_BYTE *)(v18 + 134);
  outData->m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);
  outData->m_EnablePlayerShield = *(_BYTE *)(v18 + 140);
  outData->m_DisableGrade = *(_BYTE *)(v18 + 141);
  outData->m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);
  return 1;
}

```

##### ◆ C-GROUP-03：EnemyGroups 直接标量消费者

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645765-17646205
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyGroupDB(GroupID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[104];
  return result;
}

//----- (0000000144606130) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetForcedWinAbortConditionID(int GroupID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyGroupDB(GroupID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[96];
  return result;
}

//----- (0000000144606150) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetGenerateSpecialSkillValueOrverTurn(unsigned int EnemyID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyDB(EnemyID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[228];
  return result;
}

//----- (0000000144606170) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetGroupBgmID(int GroupID)
{
  FScriptContainerElement *EnemyGroupDB; // rax

  EnemyGroupDB = UEnemyDB::GetEnemyGroupDB(GroupID);
  if ( EnemyGroupDB )
    return *(unsigned int *)&EnemyGroupDB[32];
  else
    return 0xFFFFFFFFLL;
}

//----- (0000000144606190) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetGroupBgmType(int GroupID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyGroupDB(GroupID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[44];
  return result;
}

//----- (00000001446061B0) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetGroupCameraSetID(int GroupID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyGroupDB(GroupID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[48];
  return result;
}

//----- (00000001446061D0) ----------------------------------------------------
bool __fastcall UEnemyDB::GetGroupID(int *GroupID, const FName *GroupName)
{
  TArray<FEnemyGroupsBase,TSizedDefaultAllocator<32> > *EnemyGroupsDB; // rax
  ADatabaseDefineStatic *v5; // rdi
  FDbAccessHelper<FEnemyGroupsBase> *p_m_DBAccess; // rdi
  __int64 v7; // rbx
  int v8; // ebp
  int v9; // r8d
  _DWORD *Data; // rdx
  int v11; // eax
  FScriptContainerElement *v12; // rdx
  int v13; // r8d
  _DWORD *p_Hash; // rdx
  int v15; // eax
  FScriptContainerElement *v16; // rdx
  bool v17; // zf
  FScriptContainerElement *v18; // rax
  __int64 v19; // rcx

  *GroupID = 0;
  EnemyGroupsDB = UEnemyDB::GetEnemyGroupsDB();
  if ( !EnemyGroupsDB || EnemyGroupsDB->ArrayNum <= 0 )
    return 0;
  if ( ADatabaseDefineStatic::m_Self
    && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    && ADatabaseDefineStatic::m_Self
    && ADatabaseDefineStatic::m_Self->m_EnemyGroups.m_Loaded )
  {
    v5 = ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
       ? ADatabaseDefineStatic::m_Self
       : nullptr;
    p_m_DBAccess = &v5->m_EnemyGroups.m_DBAccess;
    if ( p_m_DBAccess )
    {
      v7 = (__int64)*GroupName;
      v8 = -1;
      v9 = HIDWORD(v7) + GetTypeHash((FNameEntryId)v7);
      if ( p_m_DBAccess->m_Label2Index.Pairs.Elements.Data.ArrayNum != p_m_DBAccess->m_Label2Index.Pairs.Elements.NumFreeIndices )
      {
        Data = p_m_DBAccess->m_Label2Index.Pairs.Hash.SecondaryData.Data;
        if ( !Data )
          Data = &p_m_DBAccess->m_Label2Index.Pairs.Hash;
        v11 = Data[v9 & (p_m_DBAccess->m_Label2Index.Pairs.HashSize - 1)];
        if ( v11 != -1 )
        {
          v12 = p_m_DBAccess->m_Label2Index.Pairs.Elements.Data.AllocatorInstance.Data;
          while ( *(_QWORD *)&v12[20 * v11] != v7 )
          {
            v11 = *(_DWORD *)&v12[20 * v11 + 12];
            if ( v11 == -1 )
              goto LABEL_30;
          }
          v13 = HIDWORD(v7) + GetTypeHash((FNameEntryId)v7);
          if ( p_m_DBAccess->m_Label2Index.Pairs.Elements.Data.ArrayNum == p_m_DBAccess->m_Label2Index.Pairs.Elements.NumFreeIndices )
            goto LABEL_27;
          p_Hash = p_m_DBAccess->m_Label2Index.Pairs.Hash.SecondaryData.Data;
          if ( !p_Hash )
            p_Hash = &p_m_DBAccess->m_Label2Index.Pairs.Hash;
          v15 = p_Hash[v13 & (p_m_DBAccess->m_Label2Index.Pairs.HashSize - 1)];
          if ( v15 == -1 )
          {
LABEL_27:
            v18 = nullptr;
          }
          else
          {
            v16 = p_m_DBAccess->m_Label2Index.Pairs.Elements.Data.AllocatorInstance.Data;
            while ( 1 )
            {
              v17 = *(_QWORD *)&v16[20 * v15] == v7;
              v18 = &v16[20 * v15];
              if ( v17 )
                break;
              v15 = *(_DWORD *)&v18[12];
              if ( v15 == -1 )
                goto LABEL_27;
            }
          }
          v19 = *(int *)&v18[8];
          if ( (int)v19 >= 0 )
            v8 = *(_DWORD *)&p_m_DBAccess->m_DatabasePtr->AllocatorInstance.Data[144 * v19 + 8];
        }
      }
LABEL_30:
      *GroupID = v8;
    }
  }
  return *GroupID > 0;
}

//----- (0000000144606390) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetIgnoreDefeat(int GroupID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyGroupDB(GroupID);
  if ( result )
    return (FScriptContainerElement *)(unsigned __int8)result[23];
  return result;
}

//----- (00000001446063B0) ----------------------------------------------------
TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *__fastcall UEnemyDB::GetImportantEnemyIDs(
        TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *result,
        int GroupID)
{
  int v2; // esi
  FScriptContainerElement *EnemyGroupDB; // rax
  FScriptContainerElement *v5; // rbp
  __int64 v6; // rbx
  const void *v7; // r14
  size_t v8; // r14
  FScriptContainerElement *Data; // rbx
  const void *v10; // rbp
  FScriptContainerElement *v11; // r12
  __int64 v12; // rbp
  FScriptContainerElement *v13; // r15
  __int64 ArrayNum; // r14
  TArray<bool,TSizedDefaultAllocator<32> > flags; // [rsp+20h] [rbp-38h] BYREF
  TArray<int,TSizedDefaultAllocator<32> > enemies; // [rsp+30h] [rbp-28h] BYREF

  v2 = 0;
  result->AllocatorInstance.Data = nullptr;
  *(_QWORD *)&result->ArrayNum = 0;
  EnemyGroupDB = UEnemyDB::GetEnemyGroupDB(GroupID);
  v5 = EnemyGroupDB;
  if ( EnemyGroupDB )
  {
    v6 = *(int *)&EnemyGroupDB[64];
    v7 = *(const void **)&EnemyGroupDB[56];
    enemies.AllocatorInstance.Data = nullptr;
    enemies.ArrayNum = v6;
    if ( (_DWORD)v6 )
    {
      TArray<FVertexInstanceID,TSizedDefaultAllocator<32>>::ResizeForCopy(
        (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&enemies,
        v6,
        0);
      memcpy_0(enemies.AllocatorInstance.Data, v7, 4 * v6);
    }
    else
    {
      enemies.ArrayMax = 0;
    }
    v8 = *(int *)&v5[120];
    Data = nullptr;
    v10 = *(const void **)&v5[112];
    flags.AllocatorInstance.Data = nullptr;
    flags.ArrayNum = v8;
    if ( (_DWORD)v8 )
    {
      TArray<enum UCharacterCreateProfileWidget::CATEGORY_SELECT_TYPE,TSizedDefaultAllocator<32>>::ResizeForCopy(
        (TArray<Metasound::FLiteral::FNone,TSizedDefaultAllocator<32> > *)&flags,
        v8,
        0);
      memcpy_0(flags.AllocatorInstance.Data, v10, v8);
      LODWORD(v8) = flags.ArrayNum;
      Data = flags.AllocatorInstance.Data;
    }
    else
    {
      flags.ArrayMax = 0;
    }
    v11 = &Data[(int)v8];
    if ( Data != v11 )
    {
      v12 = 0;
      do
      {
        if ( *Data )
        {
          if ( v12 >= 0 && v2 < enemies.ArrayNum )
          {
            v13 = &enemies.AllocatorInstance.Data[4 * v12];
            if ( *(_DWORD *)v13 > 0 )
            {
              ArrayNum = result->ArrayNum;
              result->ArrayNum = ArrayNum + 1;
              if ( (unsigned int)(ArrayNum + 1) > result->ArrayMax )
                TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                  result,
                  ArrayNum);
              *(_DWORD *)&result->AllocatorInstance.Data[4 * ArrayNum] = *(_DWORD *)v13;
            }
          }
        }
        ++v2;
        ++v12;
        ++Data;
      }
      while ( Data != v11 );
      Data = flags.AllocatorInstance.Data;
    }
    if ( Data )
      FMemory::Free(Data);
    if ( enemies.AllocatorInstance.Data )
      FMemory::Free(enemies.AllocatorInstance.Data);
  }
  return result;
}

//----- (0000000144606520) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetInescapable(int GroupID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyGroupDB(GroupID);
  if ( result )
    return (FScriptContainerElement *)(unsigned __int8)result[20];
  return result;
}

//----- (0000000144606540) ----------------------------------------------------
char __fastcall UDLCGiftUtility::GetIsEnableGift()
{
  ADatabaseDefineStatic *v0; // rax
  FScriptContainerElement *Data; // rbx
  FScriptContainerElement *i; // rbp
  UQPSaveData *GameSaveData; // rax
  FScriptContainerElement *SaveDataDLCGift; // rdi
  int v5; // eax
  __int64 v7; // rdi
  __int64 v8; // rax
  __int64 v9; // rsi
  unsigned __int8 (__fastcall *v10)(__int64, _QWORD); // r14
  const wchar_t *v11; // rcx
  unsigned int v12; // eax

  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v0 = ADatabaseDefineStatic::m_Self;
  else
    v0 = nullptr;
  Data = v0->m_DLCGiftList.m_DataList.AllocatorInstance.Data;
  for ( i = &Data[160 * v0->m_DLCGiftList.m_DataList.ArrayNum]; Data != i; Data += 160 )
  {
    if ( Data[52] )
    {
      GameSaveData = UKSSaveDataStatics::GetGameSaveData(nullptr);
      SaveDataDLCGift = UQPSaveData::GetSaveDataDLCGift(GameSaveData, *(_DWORD *)&Data[8]);
      if ( UKSSaveDataStatics::GetSysSaveDataFlag(nullptr, *(_DWORD *)&Data[76] + 35) )
        return 1;
      v5 = *(_DWORD *)&SaveDataDLCGift[4];
      if ( (unsigned int)v5 <= 2 && v5 >= 1 )
        return 1;
    }
    else
    {
      v7 = *(_QWORD *)&Data[144];
      if ( (unsigned __int8)SteamAPI_Init()
        && *(_QWORD *)SteamInternal_ContextInit(&`SteamUser'::`2'::s_CallbackCounterAndContext) )
      {
        v8 = SteamInternal_ContextInit(&`SteamApps'::`2'::s_CallbackCounterAndContext);
        v9 = *(_QWORD *)v8;
        v10 = *(unsigned __int8 (__fastcall **)(__int64, _QWORD))(**(_QWORD **)v8 + 56LL);
        v11 = *(_DWORD *)(v7 + 8) ? *(const wchar_t **)v7 : &InExpression;
        v12 = _wtoi(v11);
        if ( v10(v9, v12) )
          return 1;
      }
    }
  }
  return 0;
}
// 1480EF6B0: using guessed type void (__fastcall *`SteamUser'::`2'::s_CallbackCounterAndContext)(struct ISteamUser **);
// 1480EF770: using guessed type void (__fastcall *`SteamApps'::`2'::s_CallbackCounterAndContext)(struct ISteamApps **);
// 1481E85B8: using guessed type __int64 SteamAPI_Init(void);
// 1481E8618: using guessed type __int64 __fastcall SteamInternal_ContextInit(_QWORD);

//----- (0000000144606670) ----------------------------------------------------
bool __fastcall UDLCGiftUtility::GetIsPreOrdered(const FDLCGiftListBase *dlcGift)
{
  FScriptContainerElement *Data; // rbx
  __int64 v3; // rax
  __int64 v4; // rdi
  bool (__fastcall *v5)(__int64, _QWORD); // rsi
  const wchar_t *v6; // rcx
  unsigned int v7; // eax

  Data = dlcGift->m_SteamIds.AllocatorInstance.Data;
  if ( !(unsigned __int8)SteamAPI_Init()
    || !*(_QWORD *)SteamInternal_ContextInit(&`SteamUser'::`2'::s_CallbackCounterAndContext) )
  {
    return 0;
  }
  v3 = SteamInternal_ContextInit(&`SteamApps'::`2'::s_CallbackCounterAndContext);
  v4 = *(_QWORD *)v3;
  v5 = *(bool (__fastcall **)(__int64, _QWORD))(**(_QWORD **)v3 + 56LL);
  if ( *(_DWORD *)&Data[8] )
    v6 = *(const wchar_t **)Data;
  else
    v6 = &InExpression;
  v7 = _wtoi(v6);
  return v5(v4, v7);
}
// 1480EF6B0: using guessed type void (__fastcall *`SteamUser'::`2'::s_CallbackCounterAndContext)(struct ISteamUser **);
// 1480EF770: using guessed type void (__fastcall *`SteamApps'::`2'::s_CallbackCounterAndContext)(struct ISteamApps **);
// 1481E85B8: using guessed type __int64 SteamAPI_Init(void);
// 1481E8618: using guessed type __int64 __fastcall SteamInternal_ContextInit(_QWORD);

//----- (0000000144606700) ----------------------------------------------------
char __fastcall AQPEncountManager::GetIsThisNowDefultEncount(AQPEncountManager *this, FName strLevelName)
{
  int v3; // ebx
  __int64 i; // rdi
  __int64 v5; // rax
  AKSCharacterBase *pPlayer; // [rsp+30h] [rbp+8h] BYREF

  if ( this->m_CEncount.ArrayNum <= 0 )
    return 1;
  if ( !ALevelManager::m_Self )
    return 1;
  if ( !UObjectBase::IsValidLowLevelFast(ALevelManager::m_Self, 1) )
    return 1;
  if ( !ALevelManager::m_Self )
    return 1;
  if ( (ALevelManager::m_Self->ObjectFlags & 0x40000000) != 0 )
    return 1;
  if ( !ALevelManager::IsLevelChangeEnd(ALevelManager::m_Self) )
    return 1;
  v3 = 0;
  pPlayer = nullptr;
  AKSCharacterManager::GetPlayerInstance(&pPlayer);
  if ( this->m_CEncount.ArrayNum <= 0 )
    return 1;
  for ( i = 0; ; i += 8 )
  {
    v5 = *(_QWORD *)&this->m_CEncount.AllocatorInstance.Data[i];
    if ( v5 )
    {
      if ( (*(_DWORD *)(v5 + 8) & 0x40000000) == 0
        && AActor::IsOverlappingActor(*(AActor **)&this->m_CEncount.AllocatorInstance.Data[i], pPlayer) )
      {
        break;
      }
    }
    if ( ++v3 >= this->m_CEncount.ArrayNum )
      return 1;
  }
  return 0;
}

//----- (00000001446067F0) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetLockedWeakFlags(int LockID, bool isMagic)
{
  _BOOL8 v2; // rbx
  FScriptContainerElement *EnemyWeakLockDB; // rcx

  v2 = isMagic;
  if ( LockID > 0 && (EnemyWeakLockDB = UEnemyDB::GetEnemyWeakLockDB(LockID)) != nullptr )
    return &EnemyWeakLockDB[16 * v2 + 16];
  else
    return nullptr;
}

//----- (0000000144606830) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetMaxEncountEnemyTypes()
{
  unsigned int v0; // ebx
  TArray<FEncountListBase,TSizedDefaultAllocator<32> > *EncountListsDB; // rax
  FScriptContainerElement *Data; // r13
  __int64 v3; // rax
  unsigned int v4; // ecx
  FScriptContainerElement *EncountVolumeDB; // rsi
  __int64 v6; // rdi
  __int64 ArrayNum; // rbx
  int v8; // eax
  int *v9; // r15
  FScriptContainerElement *v10; // r12
  FScriptContainerElement *EnemyGroupDB; // rax
  FScriptContainerElement *v12; // rcx
  unsigned int *v13; // rsi
  __int64 v14; // r14
  FScriptContainerElement *EnemyDB; // rax
  int v16; // ebx
  FScriptContainerElement *v17; // rcx
```

##### ◆ C-SYMBOL-03：符号敌人运行时 赋值 与 GroupId/Index 到 EnemyGroups 的桥接

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17650570-17650715
  unsigned int m_GroupId; // edx
  FScriptContainerElement *v14; // rbx
  FDbAccessHelper<FEnemyGroupsBase> *p_m_DBAccess; // rsi
  int ArrayIndex; // eax
  const TArray<FEnemyGroupsBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v18; // rbx
  int m_Index; // eax
  signed int v20; // ecx
  unsigned int EnemyIdFromSymbolId; // eax
  FScriptContainerElement *EnemyDB; // rax
  FScriptContainerElement *EnemyTypeDB; // rax
  FEnemyGroupsBase enemyGroupData; // [rsp+20h] [rbp-41h] BYREF

  this->m_group.m_id = groupData->m_id;
  this->m_group.m_StartSymbolEnemyId = groupData->m_StartSymbolEnemyId;
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_group.m_SymbolEnemyId,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&groupData->m_SymbolEnemyId);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_group.m_probability,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&groupData->m_probability);
  this->m_data.m_id = data->m_id;
  this->m_data.m_GroupId = data->m_GroupId;
  this->m_data.m_Index = data->m_Index;
  this->m_data.m_Dir = data->m_Dir;
  this->m_data.m_ObjectId = data->m_ObjectId;
  this->m_data.m_RepopCount = data->m_RepopCount;
  this->m_data.m_Redrawing = data->m_Redrawing;
  this->m_data.m_TargetLevel1 = data->m_TargetLevel1;
  this->m_data.m_TargetLevel2 = data->m_TargetLevel2;
  m_Scale = data->m_Scale;
  v10 = 0;
  this->m_data.m_Scale = m_Scale;
  this->m_bBoss = bBoss;
  if ( !ADatabaseDefineStatic::m_Self
    || (v11 = !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
        v12 = ADatabaseDefineStatic::m_Self,
        v11) )
  {
    v12 = nullptr;
  }
  if ( v12 )
  {
    m_GroupId = this->m_data.m_GroupId;
    v14 = nullptr;
    p_m_DBAccess = &v12->m_EnemyGroups.m_DBAccess;
    memset(&enemyGroupData.m_EnemyID, 0, 37);
    enemyGroupData.m_ForcedWinAbortCondition = 0;
    enemyGroupData.m_IsAllEnemyDeadOnForceWin = 0;
    enemyGroupData.m_ForcedLoseAbortCondition = 0;
    memset(&enemyGroupData.m_ImportantFlags, 0, 23);
    enemyGroupData.m_EncountEffectTypeID = 0;
    *(_WORD *)&enemyGroupData.m_EnablePlayerShield = 0;
    enemyGroupData.m_IsContainRareEnemy = 0;
    ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                   (FDbAccessHelper<FNoteListBase> *)&v12->m_EnemyGroups.m_DBAccess,
                   m_GroupId);
    if ( ArrayIndex >= 0 )
    {
      m_DatabasePtr = p_m_DBAccess->m_DatabasePtr;
      if ( m_DatabasePtr )
      {
        if ( ArrayIndex < m_DatabasePtr->ArrayNum )
        {
          v18 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[144 * ArrayIndex];
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_EnemyID,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 56));
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_EventIndices,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 72));
          enemyGroupData.m_AbortCondition = *(_DWORD *)(v18 + 88);
          enemyGroupData.m_DefeatPriority = *(_BYTE *)(v18 + 92);
          enemyGroupData.m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);
          enemyGroupData.m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);
          enemyGroupData.m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);
          TArray<unsigned char,TSizedDefaultAllocator<32>>::operator=(
            (TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_ImportantFlags,
            (const TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)(v18 + 112));
          enemyGroupData.m_PetDear = *(_DWORD *)(v18 + 128);
          enemyGroupData.m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);
          enemyGroupData.m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);
          enemyGroupData.m_DisableSupporter = *(_BYTE *)(v18 + 134);
          enemyGroupData.m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);
          enemyGroupData.m_EnablePlayerShield = *(_BYTE *)(v18 + 140);
          enemyGroupData.m_DisableGrade = *(_BYTE *)(v18 + 141);
          enemyGroupData.m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);
          m_Index = this->m_data.m_Index;
          if ( m_Index >= 0 && m_Index < enemyGroupData.m_EnemyID.ArrayNum )
            this->m_EnemyListId = *(_DWORD *)&enemyGroupData.m_EnemyID.AllocatorInstance.Data[4 * this->m_data.m_Index];
          v14 = enemyGroupData.m_ImportantFlags.AllocatorInstance.Data;
        }
      }
    }
    if ( v14 )
      FMemory::Free(v14);
    if ( enemyGroupData.m_EventIndices.AllocatorInstance.Data )
      FMemory::Free(enemyGroupData.m_EventIndices.AllocatorInstance.Data);
    if ( enemyGroupData.m_EnemyID.AllocatorInstance.Data )
      FMemory::Free(enemyGroupData.m_EnemyID.AllocatorInstance.Data);
  }
  v20 = nReplaceTypeID;
  if ( nReplaceTypeID > 0
    || (EnemyIdFromSymbolId = UQPMapObjectUtility::GetEnemyIdFromSymbolId(this->m_data.m_id, this->m_data.m_Index),
        (EnemyDB = UEnemyDB::GetEnemyDB(EnemyIdFromSymbolId)) != nullptr)
    && (v20 = *(_DWORD *)&EnemyDB[24], v20 > 0) )
  {
    EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(v20);
    if ( EnemyTypeDB )
      v10 = *(_DWORD *)&EnemyTypeDB[124];
  }
  this->m_nSE = v10;
}

//----- (000000014460BA80) ----------------------------------------------------
void __fastcall UDLCGiftUtility::SetSystemSaveDataLink(char saveDataLinkIndex, bool flag)
{
  UKSSaveDataStatics::SetSysSaveDataFlag(nullptr, saveDataLinkIndex + 35, flag);
}

//----- (000000014460BAA0) ----------------------------------------------------
void __fastcall AFieldEnemyBase::SetupCollition(AFieldEnemyBase *this)
{
  UPaperFlipbook *m_UseFlipbook; // r8
  float v3; // xmm1_4
  UCapsuleComponent *m_CapsuleComponent; // rbx
  float v5; // xmm1_4
  float v6; // xmm6_4
  __m128d v7; // xmm8
  double v8; // xmm9_8
  __m128d v9; // xmm6
  const std::nothrow_t *v10; // rdx
  FRotationConversionCache::FPayload *Payload; // rax
  FRotationConversionCache::FPayload *v12; // rdi
  UE::Math::TRotator<double> *p_CachedRotator; // rcx
  __m128d v15; // xmm8
  __m128d v16; // xmm4
  __m128 v17; // xmm1
  __m128 v18; // xmm2
  __m128d v19; // xmm3
  __m128d v20; // xmm5
  __int128 v21; // xmm0
  UE::Math::TVector<double> v22; // [rsp+30h] [rbp-A8h] BYREF
  UE::Math::TQuat<double> result; // [rsp+50h] [rbp-88h] BYREF
  UE::Math::TQuat<double> v24; // [rsp+70h] [rbp-68h] BYREF
  float w; // [rsp+E0h] [rbp+8h] BYREF
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_AbortCondition` | `C-GROUP-02:17923540` `outData->m_AbortCondition = *(_DWORD *)(v18 + 88);` | 该键作为行偏移 `+0x58` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_BanGodBeast` | `C-GROUP-02:17923526` `outData->m_BanGodBeast = *(_BYTE *)(v18 + 25);` | 该键作为行偏移 `+0x19` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Bgm` | `C-GROUP-02:17923528` `outData->m_Bgm = *(_DWORD *)(v18 + 32);`<br>`C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | 该键作为行偏移 `+0x20` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_BgmType` | `C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | 该键作为行偏移 `+0x2C` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Camera` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | OT0 `EnemyGroups` 未复原出 `m_Camera` 字段。实体化器将行 `+0x30` 处的活字段命名为 `m_CameraSet`；应将 `m_Camera` 视为过时笔记或旧别名。 | 已复原 | `C-GROUP-02` |
| `m_CameraSet` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | 该键作为行偏移 `+0x30` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DarkBgm` | `C-GROUP-02:17923530` `outData->m_DarkBgm = *(_DWORD *)(v18 + 40);` | 该键作为行偏移 `+0x28` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DefeatPriority` | `C-GROUP-02:17923541` `outData->m_DefeatPriority = *(_BYTE *)(v18 + 92);` | 该键作为行偏移 `+0x5C` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndTraining` | `C-GROUP-02:17923550` `outData->m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);` | 该键作为行偏移 `+0x85` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndWipe` | `C-GROUP-02:17923549` `outData->m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);` | 该键作为行偏移 `+0x84` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableGrade` | `C-GROUP-02:17923554` `outData->m_DisableGrade = *(_BYTE *)(v18 + 141);` | 该键作为行偏移 `+0x8D` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableRetire` | `C-GROUP-02:17923522` `outData->m_DisableRetire = *(_BYTE *)(v18 + 21);` | 该键作为行偏移 `+0x15` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableSupporter` | `C-GROUP-02:17923551` `outData->m_DisableSupporter = *(_BYTE *)(v18 + 134);` | 该键作为行偏移 `+0x86` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnablePlayerShield` | `C-GROUP-02:17923553` `outData->m_EnablePlayerShield = *(_BYTE *)(v18 + 140);` | 该键作为行偏移 `+0x8C` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EncountEffectTypeID` | `C-GROUP-02:17923552` `outData->m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);` | 该键作为行偏移 `+0x88` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnemyID` | `C-GROUP-02:17923535` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EnemyID,` | 该键作为行偏移 `+0x38` 处的 `TArray` 头读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EventIndices` | `C-GROUP-02:17923538` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EventIndices,` | 该键作为行偏移 `+0x48` 处的 `TArray` 头读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedLoseAbortCondition` | `C-GROUP-02:17923544` `outData->m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);` | 该键作为行偏移 `+0x68` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedWinAbortCondition` | `C-GROUP-02:17923542` `outData->m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);` | 该键作为行偏移 `+0x60` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Formation` | `C-GROUP-02:17923533` `outData->m_Formation = *(_DWORD *)(v18 + 52);` | 该键作为行偏移 `+0x34` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_IgnoreDefeat` | `C-GROUP-02:17923524` `outData->m_IgnoreDefeat = *(_BYTE *)(v18 + 23);` | 该键作为行偏移 `+0x17` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ImportantFlags` | `C-GROUP-02:17923546` `(TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&outData->m_ImportantFlags,` | 该键作为行偏移 `+0x70` 处的 `TArray` 头读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Inescapable` | `C-GROUP-02:17923521` `outData->m_Inescapable = *(_BYTE *)(v18 + 20);` | 该键作为行偏移 `+0x14` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsAllEnemyDeadOnForceWin` | `C-GROUP-02:17923543` `outData->m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);` | 该键作为行偏移 `+0x64` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsContainRareEnemy` | `C-GROUP-02:17923555` `outData->m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);` | 该键作为行偏移 `+0x8E` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Label` | `C-GROUP-02:17923520` `outData->m_Label = *(FName *)(v18 + 12);` | 该键作为行偏移 `+0x0C` 处的 `FName` 读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_NightBgm` | `C-GROUP-02:17923529` `outData->m_NightBgm = *(_DWORD *)(v18 + 36);` | 该键作为行偏移 `+0x24` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_PetDear` | `C-GROUP-02:17923548` `outData->m_PetDear = *(_DWORD *)(v18 + 128);` | 该键作为行偏移 `+0x80` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_PlayerMemberSetID` | `C-GROUP-02:17923527` `outData->m_PlayerMemberSetID = *(_DWORD *)(v18 + 28);` | 该键作为行偏移 `+0x1C` 处的 32 位标量读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ResultBgmLose` | 供应的 C 导出 中未复原出精确具名字段或无歧义偏移槽位。 | C 导出 中未复原出精确的 `m_ResultBgmLose` 符号或无歧义的资产专用偏移。因此该字段记录为未复原，而不是推断出来的字段。 | 否定性/部分复原 | not 已复原 |
| `m_ResultBgmWin` | 供应的 C 导出 中未复原出精确具名字段或无歧义偏移槽位。 | C 导出 中未复原出精确的 `m_ResultBgmWin` 符号或无歧义的资产专用偏移。因此该字段记录为未复原，而不是推断出来的字段。 | 否定性/部分复原 | not 已复原 |
| `m_SkipBgmResult` | `C-GROUP-02:17923525` `outData->m_SkipBgmResult = *(_BYTE *)(v18 + 24);` | 该键作为行偏移 `+0x18` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |
| `m_SkipResult` | `C-GROUP-02:17923523` `outData->m_SkipResult = *(_BYTE *)(v18 + 22);` | 该键作为行偏移 `+0x16` 处的 字节/布尔 槽位读取。群组 行按 `GroupID` 以 `0x90` 行跨度解析，`FDbAccessHelper<FEnemyGroupsBase>::GetData` 会把这个确切槽位复制进 `outData`。 | 已复原 | `C-GROUP-01`, `C-GROUP-02` |


### ■ (**) `EnemyModeID`
告诉游戏哪个 模式 ID 对应要应用到场地或敌人的哪个特效。这些效果可以分配给 avails。用于 boss 进入 boost 模式 并显示特殊特效时。

#### ■ 运行时读取语义
`EnemyModeID` 在运行时不受 enum 绑定。引擎通过 `EnemyModeID` 数据库访问 helper 解析整数 模式 ID，然后读取一个 88 字节行。活 模式切换 系统会把活动 模式 存储为一个 `MODE_CHANGE` 异常状态，其 `FAilmentInfo.m_LinkedEnemyModeID` 会与所请求的 `EnemyModeID` 比较；因此，扩展受数据库 键/索引 可用性以及提供 模式 ID 的调用方约束，而不受固定 SDK enum 约束。

【主要 C 证据】

##### ◆ C-MODE-01：`EnemyModeID` 行查找使用 DB 键解析与 88 字节跨度

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644761-17644785
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyModeDB(unsigned int ModeID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    return nullptr;
  v2 = ADatabaseDefineStatic::m_Self;
  if ( ADatabaseDefineStatic::m_Self
    && (ADatabaseDefineStatic::m_Self->ObjectFlags & 0x40000000) == 0
    && ADatabaseDefineStatic::m_Self->m_EnemyModeID.m_Loaded
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_EnemyModeID.m_DBAccess,
                       ModeID),
        ArrayIndex >= 0)
    && ArrayIndex < v2->m_EnemyModeID.m_DataList.ArrayNum )
  {
    return &v2->m_EnemyModeID.m_DataList.AllocatorInstance.Data[88 * ArrayIndex];
  }
```

##### ◆ C-MODE-02：完整表面通过 `ADatabaseDefineStatic` 暴露

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17064096-17064131
void __fastcall ADatabaseDefineStatic::execGetEnemyModeID(UObject *Context, FFrame *Stack, char *Z_Param__Result)
{
  Stack->Code += Stack->Code != nullptr;
  TSet<TTuple<unsigned int,float>,TDefaultMapHashableKeyFuncs<unsigned int,float,0>,FDefaultSetAllocator>::operator=(
    (TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)(Z_Param__Result + 8),
    (const TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)&Context[139].OuterPrivate);
  TSet<TTuple<FName,bool>,TDefaultMapHashableKeyFuncs<FName,bool,0>,FDefaultSetAllocator>::operator=(
    (TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)(Z_Param__Result + 88),
    (const TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)&Context[141].OuterPrivate);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 168),
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&Context[143].OuterPrivate);
  *((_DWORD *)Z_Param__Result + 46) = Context[144].ObjectFlags;
  *((_QWORD *)Z_Param__Result + 24) = Context[144].ClassPrivate.ObjectPtr.ObjectPtr.Handle;
  TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 200),
    (const TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32> > *)&Context[144].NamePrivate);
  Z_Param__Result[216] = (char)Context[145].__vftable;
}

void __fastcall ADatabaseDefineStatic::execGetEnemyModeIDDataList(
        UObject *Context,
        FFrame *Stack,
        TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32> > *Z_Param__Result)
```

##### ◆ C-MODE-03：复制出的行布局与标量/数组槽位

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17052737-17052934
TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32> > *__fastcall TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32>>::operator=(
        TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32> > *this,
        const TArray<FEnemyModeIDBase,TSizedDefaultAllocator<32> > *Other)
{
  ...
        *(_QWORD *)v12 = FEnemyModeIDBase::`vftable';
        *(_DWORD *)&v14[-28] = *(_DWORD *)&v13[-40];  // +0x08: m_id
        *(_DWORD *)&v14[-24] = *(_DWORD *)&v13[-36];  // +0x0C
        *(_DWORD *)&v14[-20] = *(_DWORD *)&v13[-32];  // +0x10
        v14[-16] = v13[-28];                          // +0x14
        *(_QWORD *)&v14[-12] = 0;
        v15 = *(int *)&v13[-16];
        Src = *(void **)&v13[-24];                     // +0x18 TArray pointer
        *(_DWORD *)&v14[-4] = v15;                     // +0x20 TArray count
        ...
        *(_QWORD *)&v14[4] = 0;
        v16 = *(_DWORD *)v13;
        Srca = *(void **)&v13[-8];                     // +0x28 TArray pointer
        *(_DWORD *)&v14[12] = v16;                     // +0x30 TArray count
        ...
        *(_DWORD *)&v14[20] = *(_DWORD *)&v13[8];      // +0x38
        *(_DWORD *)&v14[24] = *(_DWORD *)&v13[12];     // +0x3C
        *(_DWORD *)&v14[28] = *(_DWORD *)&v13[16];     // +0x40
        *(_DWORD *)&v14[32] = *(_DWORD *)&v13[20];     // +0x44
        *(_DWORD *)&v14[36] = *(_DWORD *)&v13[24];     // +0x48
        *(_DWORD *)&v14[40] = *(_DWORD *)&v13[28];     // +0x4C
        v17 = v13[32];                                 // +0x50
```

##### ◆ C-MODE-04：模式切换 解除 条件 从行 `+0x18/+0x28` 读取

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17646395-17646440
bool __fastcall UEnemyDB::GetModeChangeRemoveConditions(
        const TArray<int,TSizedDefaultAllocator<32> > **pConditions,
        const TArray<int,TSizedDefaultAllocator<32> > **pParams,
        int ModeID)
{
  ...
  v8 = (__int64)&v6->m_EnemyModeID.m_DataList.AllocatorInstance.Data[88 * ArrayIndex];
  if ( !v8 || !pConditions || !pParams )
    return 0;
  result = 1;
  *pConditions = (const TArray<int,TSizedDefaultAllocator<32> > *)(v8 + 24);
  *pParams = (const TArray<int,TSizedDefaultAllocator<32> > *)(v8 + 40);
  return result;
}
```

##### ◆ C-MODE-05：模式切换 解除 条件 会覆盖默认 异常状态 解除条件 槽位

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17986820-17986867
if ( AilmentID == 37 )
{
  ...
  UEnemyDB::GetWeakLockRemoveConditions(pConditions, &pParams, m_WeakLockID);
}
else if ( AilmentID == 306 && AvailID > 0 )
{
  if ( SkillAvailDB )
    UEnemyDB::GetModeChangeRemoveConditions(pConditions, &pParams, SkillAvailDB->m_ModeChangeID);
  else
    UEnemyDB::GetModeChangeRemoveConditions(pConditions, &pParams, 0);
}
LOBYTE(v15) = USkillDB::CheckRemoveAilment(
                pConditions[0],
                pParams,
                Weapon,
                Magic,
                Character,
                Enforcer,
                AvailID,
                Index);
```

##### ◆ C-MODE-06：行 `+0x38` 是 模式 活动期间应用的 attach-效果 ID

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17430542-17430576
__int64 __fastcall AQPBattleCharacterBase::SetModeChangeAttachEffect(
        AQPBattleCharacterBase *this,
        unsigned int EnemyModeID,
        bool bAttach)
{
  ...
  if ( bAttach )
  {
    EnemyID = AQPBattleCharacterBase::GetEnemyID(this);
    UEnemyDB::GetEnemyTypeID(&typeID, EnemyID);
    UEnemyDB::GetEnemyTypeDB(typeID);
    EnemyModeDB = UEnemyDB::GetEnemyModeDB(EnemyModeID);
    if ( EnemyModeDB )
      return AQPBattleCharacterBase::SetAttachEffect(this, *(_DWORD *)&EnemyModeDB[56]);
  }
```

##### ◆ C-MODE-07：行 `+0x14` 控制进入时立即移除三个控制类 异常状态

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17448196-17448211
bool __fastcall AQPBattleCharacterBase::InitEnemyModeChange(AQPBattleCharacterBase *this, unsigned int EnemyModeID)
{
  unsigned int EnemyID; // eax
  FScriptContainerElement *EnemyModeDB; // rax

  EnemyID = AQPBattleCharacterBase::GetEnemyID(this);
  AQPBattleCharacterBase::SetEnemyAttachEffect(this, EnemyID, 0, 0);
  AQPBattleCharacterBase::SetModeChangeAttachEffect(this, EnemyModeID, 1);
  EnemyModeDB = UEnemyDB::GetEnemyModeDB(EnemyModeID);
  if ( EnemyModeDB && *(_BYTE *)&EnemyModeDB[20] )
  {
    AQPBattleCharacterBase::SubAilment(this, PARALYSIS, 0, 0, 0, 0, nullptr, 0, 0);
    AQPBattleCharacterBase::SubAilment(this, FASCINATION, 0, 0, 0, 0, nullptr, 0, 0);
    AQPBattleCharacterBase::SubAilment(this, SLEEP, 0, 0, 0, 0, nullptr, 0, 0);
  }
```

##### ◆ C-MODE-08：活动 模式 身份存储为 `FAilmentInfo.m_LinkedEnemyModeID`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17443834-17443924
__int64 __fastcall AQPBattleCharacterBase::GetEnemyModeID(AQPBattleCharacterBase *this)
{
  ...
  m_LinkedEnemyModeID = 0;
  ...
  while ( 1 )
  {
    FAilmentInfo::FAilmentInfo(&info, (const FAilmentInfo *)v5);
    if ( info.m_AilmentType == MODE_CHANGE )
      break;
    ...
  }
  m_LinkedEnemyModeID = info.m_LinkedEnemyModeID;
  ...
  return m_LinkedEnemyModeID;
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17449617-17449673
_BOOL8 __fastcall AQPBattleCharacterBase::IsEnemyModeChange(AQPBattleCharacterBase *this, int EnemyModeID)
{
  Ailment = AQPBattleCharacterBase::FindAilment(this, MODE_CHANGE);
  v5 = Ailment >= 0;
  if ( Ailment >= 0 && EnemyModeID > 0 )
  {
    ...
    while ( 1 )
    {
      FAilmentInfo::FAilmentInfo(&info, (const FAilmentInfo *)v9);
      if ( info.m_AilmentType == MODE_CHANGE )
        break;
      ...
    }
    v5 = info.m_LinkedEnemyModeID == EnemyModeID;
```

【字段级解释】

| 运行时槽位 / 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_id` | `C-MODE-01` `GetArrayIndex(..., ModeID)` 与 `Data[88 * ArrayIndex]`；`C-MODE-03` 行 `+0x08` 复制 | `ModeID` 是数据库键。运行时不会把 SDK enum 用作封闭域；它会通过 DB 访问 helper 解析所供应整数，并索引 `EnemyModeID` 数据列表。 | 已复原 | `C-MODE-01`, `C-MODE-03` |
| 行 `+0x14` enter-模式 异常状态-clear 标志 | `C-MODE-07` `if ( EnemyModeDB && *(_BYTE *)&EnemyModeDB[20] )` | 当这个 字节 非零时，进入 模式 会从敌人身上移除 `PARALYSIS`、`FASCINATION` 与 `SLEEP`。反编译在消费者中没有保留可靠的导出字段名，因此本指南通过偏移与效果识别它。 | 已复原，消费者中名称未保留 | `C-MODE-07` |
| 解除条件 数组，可能是表的 条件 槽位 | `C-MODE-04` `*pConditions = ... (v8 + 24)`；`C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | 在已复原消费者中，该数组只作为 模式切换 异常状态 的移除谓词集合使用。它与通用 条件 求值器 的槽位兼容：条件 ID 数组加 参数 数组。 | 已复原 | `C-MODE-04`, `C-MODE-05` |
| 解除-参数 数组，可能是表的 参数 槽位 | `C-MODE-04` `*pParams = ... (v8 + 40)`；`C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | 该数组为移除谓词提供每槽参数。应与 条件 数组并行编辑；长度不匹配可能导致 求值器 槽读取缺失默认值或检查失败。 | 已复原 | `C-MODE-04`, `C-MODE-05` |
| 行 `+0x38` 处的 attach-效果 ID | `C-MODE-06` `SetAttachEffect(this, *(_DWORD *)&EnemyModeDB[56])` | 进入 模式 时，引擎读取行 `+0x38` 处的 32 位 ID，并直接传给 `AQPBattleCharacterBase::SetAttachEffect`。这是活动 模式 的 aura/附加 VFX 槽位，区别于 `EnemyTypeID` 的普通 attach-效果 槽位。 | 已复原，消费者中精确属性名未保留 | `C-MODE-06` |
| 活动 模式 状态 | `C-MODE-08` `info.m_AilmentType == MODE_CHANGE`；`info.m_LinkedEnemyModeID == EnemyModeID` | 活动 模式 表示为一个 `MODE_CHANGE` 异常状态 记录，而不是把 `EnemyModeID` 行写进角色。该行配置进入/退出行为；活身份是存储在 `FAilmentInfo` 内的 linked 模式 ID。 | 已复原 | `C-MODE-08` |
| `SkillAvailID.m_ModeChangeID` linkage | `C-MODE-05` `SkillAvailDB->m_ModeChangeID` | 应用 `MODE_CHANGE` 异常状态 的 avails 提供 模式 ID，用于取得 模式 专用移除条件。这是 avail 指向 `EnemyModeID` 的已证明路径。 | 已复原 | `C-MODE-05` |

### ■ (**) `EnemyDropID`
追踪所有可分配给敌人的不同掉落 ID。
#### ■ 运行时读取语义
`EnemyDropID` 是独立的掉落表数据库。`EnemyID.m_DropReward` 存储的是掉落表 ID，而不是物品 ID。在结算计算时，引擎会将该 ID 解析为 96 字节 `EnemyDropID` 行，读取物品 ID、倍率、固定数量、可变数量以及可选 事件掉落 任务 过滤器的并行数组，然后把所选结果物品记录进战斗存档数据的结果数组。
【主要 C 证据】

##### ◆ C-DROP-01：`EnemyID.m_DropReward` 作为掉落表 ID 从 `EnemyID` 行 `+0x9C` 读取

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644296-17644318
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyDropID(unsigned int EnemyID)
{
  FScriptContainerElement *result; // rax

  result = UEnemyDB::GetEnemyDB(EnemyID);
  if ( result )
    return (FScriptContainerElement *)*(_DWORD *)&result[156];
  return result;
}
```

##### ◆ C-DROP-02：掉落表查找使用 DB 键解析与 96 字节行跨度

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644272-17644294
FScriptContainerElement *__fastcall UEnemyDB::GetEnemyDropDB(unsigned int DropID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !UEnemyDB::CheckEnemyDB() )
    return nullptr;
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v2 = ADatabaseDefineStatic::m_Self;
  else
    v2 = nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_EnemyDropID.m_DBAccess,
                 DropID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_EnemyDropID.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_EnemyDropID.m_DataList.AllocatorInstance.Data[96 * ArrayIndex];
}
```

##### ◆ C-DROP-03：完整表面通过 `ADatabaseDefineStatic` 暴露

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17063881-17063916
void __fastcall ADatabaseDefineStatic::execGetEnemyDropID(UObject *Context, FFrame *Stack, char *Z_Param__Result)
{
  Stack->Code += Stack->Code != nullptr;
  TSet<TTuple<unsigned int,float>,TDefaultMapHashableKeyFuncs<unsigned int,float,0>,FDefaultSetAllocator>::operator=(
    (TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)(Z_Param__Result + 8),
    (const TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)&Context[123]);
  TSet<TTuple<FName,bool>,TDefaultMapHashableKeyFuncs<FName,bool,0>,FDefaultSetAllocator>::operator=(
    (TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)(Z_Param__Result + 88),
    (const TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)&Context[125]);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 168),
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&Context[127]);
  *((_DWORD *)Z_Param__Result + 46) = Context[127].ClassPrivate.ObjectPtr.ObjectPtr.Handle;
  *((_QWORD *)Z_Param__Result + 24) = Context[127].NamePrivate;
  TArray<FSkillAvailMagnificationConditionListBase,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FSkillAvailMagnificationConditionListBase,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 200),
    (const TArray<FSkillAvailMagnificationConditionListBase,TSizedDefaultAllocator<32> > *)&Context[127].OuterPrivate);
  Z_Param__Result[216] = Context[128].ObjectFlags;
}
```

##### ◆ C-DROP-04：复制出的行布局在 ID/名称 前缀之后包含五个数组槽位

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17039090-17039252
void __fastcall TArray<FEnemyDropIDBase,TSizedDefaultAllocator<32>>::CopyToEmpty<FEnemyDropIDBase,int>(
        TArray<FSkillAvailMagnificationConditionListBase,TSizedDefaultAllocator<32> > *this,
        const FSkillAvailMagnificationConditionListBase *OtherData,
        int OtherNum,
        int PrevMax)
{
  ...
        *(_QWORD *)Data = FEnemyDropIDBase::`vftable';
        *(_DWORD *)&v8[-20] = *(p_ArrayNum - 8);       // +0x08: m_id
        *(_QWORD *)&v8[-12] = 0;
        v10 = *(p_ArrayNum - 4);
        v11 = *((const void **)p_ArrayNum - 3);        // +0x10 TArray pointer
        *(_DWORD *)&v8[-4] = v10;                      // +0x18 TArray count
        ...
        v12 = *p_ArrayNum;
        v13 = *((const void **)p_ArrayNum - 1);        // +0x20 TArray pointer
        *(_DWORD *)&v8[12] = v12;                      // +0x28 TArray count
        ...
        v14 = p_ArrayNum[4];
        v15 = *((const void **)p_ArrayNum + 1);        // +0x30 TArray pointer
        *(_DWORD *)&v8[28] = v14;                      // +0x38 TArray count
        ...
        v16 = p_ArrayNum[8];
        v17 = *((const void **)p_ArrayNum + 3);        // +0x40 TArray pointer
        *(_DWORD *)&v8[44] = v16;                      // +0x48 TArray count
        ...
        v18 = p_ArrayNum[12];
        v19 = *((const void **)p_ArrayNum + 5);        // +0x50 TArray pointer
        *(_DWORD *)&v8[60] = v18;                      // +0x58 TArray count
```

##### ◆ C-DROP-05：道具 ID、概率/权重、counts 与 事件 任务 过滤器 通过偏移专用 取值函数 暴露

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17643640-17643679
FScriptContainerElement *__fastcall UEnemyDB::GetDropItemCounts(unsigned int DropID, bool isVariable)
{
  _BOOL8 v2; // rbx
  FScriptContainerElement *result; // rax

  v2 = isVariable;
  result = UEnemyDB::GetEnemyDropDB(DropID);
  if ( result )
    result += 16 * v2 + 48;
  return result;
}

char __fastcall UEnemyDB::GetDropItemData(const TArray<int,TSizedDefaultAllocator<32> > **IDs, int DropID)
{
  FScriptContainerElement *EnemyDropDB; // rax

  *IDs = nullptr;
  if ( DropID <= 0 )
    return 0;
  EnemyDropDB = UEnemyDB::GetEnemyDropDB(DropID);
  if ( !EnemyDropDB )
    return 0;
  *IDs = (const TArray<int,TSizedDefaultAllocator<32> > *)&EnemyDropDB[16];
  return 1;
}

FScriptContainerElement *__fastcall UEnemyDB::GetDropItemRates(unsigned int DropID)
{
  FScriptContainerElement *EnemyDropDB; // rax
  FScriptContainerElement *v2; // rcx

  EnemyDropDB = UEnemyDB::GetEnemyDropDB(DropID);
  v2 = EnemyDropDB + 32;
  if ( !EnemyDropDB )
    return nullptr;
  return v2;
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17645723-17645734
FScriptContainerElement *__fastcall UEnemyDB::GetEventDropQuestIDs(unsigned int DropID)
{
  FScriptContainerElement *EnemyDropDB; // rax
  FScriptContainerElement *v2; // rcx

  EnemyDropDB = UEnemyDB::GetEnemyDropDB(DropID);
  v2 = EnemyDropDB + 80;
  if ( !EnemyDropDB )
    return nullptr;
  return v2;
}
```

##### ◆ C-DROP-06：当列出的 任务 正在进行时，事件 掉落 可以覆盖 `EnemyID.m_DropReward`

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17513709-17513745
EnemyID = v2;
DropType = 0;
EnemyDropID = (unsigned int)UEnemyDB::GetEnemyDropID(v2);
EnemyEventDropIDs = UEnemyDB::GetEnemyEventDropIDs(v2);
if ( EnemyEventDropIDs )
{
  v10 = *(unsigned int **)EnemyEventDropIDs;
  if ( *(_QWORD *)EnemyEventDropIDs != *(_QWORD *)EnemyEventDropIDs + 4LL * *(int *)&EnemyEventDropIDs[8] )
  {
    v11 = *v10;
    if ( (int)*v10 > 0 )
    {
      EventDropQuestIDs = UEnemyDB::GetEventDropQuestIDs(v11);
      if ( EventDropQuestIDs )
      {
        v13 = *(int **)EventDropQuestIDs;
        v14 = *(_QWORD *)EventDropQuestIDs + 4LL * *(int *)&EventDropQuestIDs[8];
        if ( *(_QWORD *)EventDropQuestIDs != v14 )
        {
          while ( *v13 <= 0 || !UQuestUtility::IsQuestInProgress(*v13) )
          {
            if ( ++v13 == (int *)v14 )
              goto LABEL_15;
          }
          DropType = 1;
          EnemyDropID = v11;
        }
```

##### ◆ C-DROP-07：结果计算按槽消费 道具 ID、概率/权重 与 count ranges

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17513745-17513913
if ( !UEnemyDB::GetDropItemData(&pIDs, EnemyDropID) )
{
  LODWORD(v2) = 0;
  goto LABEL_65;
}
DropItemRates = UEnemyDB::GetDropItemRates(EnemyDropID);
...
ArrayNum = pIDs->ArrayNum;
...
if ( *(int *)((char *)v17->m128i_i32 + (_QWORD)v34) > 0 && v31 < (int)v19 )
  v30 += v17->m128i_i32[0];
...
RandomRange = AQPBattleManager::GetRandomRange(v18 - 1, 0);
...
v44 = *(int **)v16;
while ( 1 )
{
  v45 = *v44;
  v46 = *(_DWORD *)Data;
  v47 = (float)*v44;
  v48 = (int)(float)((float)((float)((float)v18 * v47) * (float)(1.0 / (float)v30)) + 0.5);
  if ( *(_DWORD *)Data > 0 )
    v45 = (int)(float)((float)(v47 * v38) + 0.5);
  ...
}
DropItemCounts = UEnemyDB::GetDropItemCounts(EnemyDropID, 0);
v51 = UEnemyDB::GetDropItemCounts(EnemyDropID, 1);
...
v52 = AQPBattleManager::GetRandomRange(*(_DWORD *)(*(_QWORD *)v51 + 4 * v2), 0);
v53 = *(_QWORD *)DropItemCounts;
v54 = *(_DWORD *)(v53 + 4 * v2);
...
v56 = v52 + v54;
v60 = AQPBattleManager::AddResultDropItem(this, TotalTypes, v46, v56, EnemyID, DropType);
```

##### ◆ C-DROP-08：结果 UI 从战斗存档数据数组读取所选结果 道具/数量

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17394813-17394854
char __fastcall UBattleUtility::GetDropItem(int *RewardID, int *ItemID, int *Count, int Index)
{
  ...
  *Count = 0;
  *ItemID = 0;
  *RewardID = 0;
  GameSaveData = UKSSaveDataStatics::GetGameSaveData(nullptr);
  ResultItemTypes = UQPSaveData::GetSaveDataWorkBattle(GameSaveData)->ResultItemTypes;
  if ( (unsigned int)ResultItemTypes > 0x63 )
    ResultItemTypes = 0;
  if ( (int)v4 < 0 || (int)v4 >= ResultItemTypes )
    return 0;
  ...
  *ItemID = *(_DWORD *)&SaveDataWorkBattle->ResultItemID.AllocatorInstance.Data[4 * v4];
  ...
  v13 = *(_DWORD *)&UQPSaveData::GetSaveDataWorkBattle(v12)->ResultItemCount.AllocatorInstance.Data[4 * v4];
  *Count = v13;
  return 1;
}
```

【字段级解释】

| 运行时槽位 / 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `EnemyID.m_DropReward` | `C-DROP-01` `*(_DWORD *)&result[156]` | 该 `EnemyID` 字段是普通掉落表 ID。它不是掉落物品 ID。所得 ID 会送入 `GetEnemyDropDB`。 | 已复原 | `C-DROP-01`, `C-DROP-02` |
| `EnemyID.m_EventDropRewards` | `C-DROP-06` `UEnemyDB::GetEnemyEventDropIDs(v2)` 然后迭代 事件 掉落 IDs | 事件 掉落 IDs 会先于普通掉落计算尝试。如果某个 事件 掉落 行中至少有一个 任务 ID 正在进行，引擎会设置 `DropType = 1`，并用该掉落 ID 替换普通 `m_DropReward`。 | 已复原 | `C-DROP-06` |
| `EnemyDropID.m_id` | `C-DROP-02` `GetArrayIndex(..., DropID)` 与 `Data[96 * ArrayIndex]`；`C-DROP-04` 行 `+0x08` 复制 | `DropID` 通过该表的 DB 访问 helper 解析。扩展该表是数据库 键/索引 问题，而不是 enum-domain 问题，前提是调用方使用足够宽的 ID 字段。 | 已复原 | `C-DROP-02`, `C-DROP-04` |
| 行 `+0x10` 处的 道具 ID 数组 | `C-DROP-05` `*IDs = ... &EnemyDropDB[16]`；`C-DROP-07` `v46 = *(_DWORD *)Data` | 这是候选 道具 ID 的并行数组。结果计算会忽略活动槽位中的非正 道具 ID。 | 已复原，取值函数 中未保留精确导出属性名 | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x20` 处的 概率/权重 数组 | `C-DROP-05` `return EnemyDropDB + 32`；`C-DROP-07` 迭代 `DropItemRates` | 这是候选权重的并行数组。引擎会对 概率/权重 求和，基于正 道具 ID 槽位归一化，并使用随机 掷值 选择槽位。 | 已复原，取值函数 中未保留精确导出属性名 | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x30` 处的固定数量数组 | `C-DROP-05` `GetDropItemCounts(DropID, 0)` 返回 `result + 48`；`C-DROP-07` `v54 = fixedCounts[slot]` | 这是加到掉落中的基础数量。 | 已复原，取值函数 中未保留精确导出属性名 | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x40` 处的可变数量数组 | `C-DROP-05` `GetDropItemCounts(DropID, 1)` 返回 `result + 64`；`C-DROP-07` `GetRandomRange(variableCounts[slot], 0)` | 这是附加随机范围。最终所选数量为 `fixedCount[slot] + random(0..variableCount[slot])`，随后在同一例程中还会应用 支援/村庄 掉落数量缩放。 | 已复原，取值函数 中未保留精确导出属性名 | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x50` 处的 事件 任务 ID 数组 | `C-DROP-05` `GetEventDropQuestIDs` 返回 `EnemyDropDB + 80`；`C-DROP-06` `UQuestUtility::IsQuestInProgress(*v13)` | 对于 事件掉落 行，此数组 门控 该 事件掉落 行是否使用。在已复原路径中，任一正 任务 ID 正在进行就足以激活 事件 掉落 替换。 | 已复原，取值函数 中未保留精确导出属性名 | `C-DROP-05`, `C-DROP-06` |
| result 道具/数量 output | `C-DROP-08` 读取 `ResultItemID[]` 与 `ResultItemCount[]` | 掉落计算之后，面向 UI 的取得不再读取 `EnemyDropID`；而是读取战斗存档数据中已实体化的结果数组。因此，改变 `EnemyDropID` 会影响选择/数量生成，而不是事后 UI 查找。 | 已复原 | `C-DROP-08` |

### ■ (**) `EncountVolume`
决定危险等级、可遭遇的敌人组（`m_EnemyGroup` 与 `m_FirstEncount`，后者很可能指定初次遭遇的敌人组），以及相应遭遇概率。Encount Volumes 在 `EncountList` 中与地图关联。地图可能改变其危险等级，从而可能改变 encount volumes。如果 `m_EncountRatio` 中的值合计不为 100，可能会出现问题。
#### ■ 运行时读取语义
【主要 C 证据】

##### ◆ C-ENCOUNT-01：EncountVolume 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17643970-17644035
      return v3;
    }
  }
  else
  {
    *nGroupNum = 0;
    return 0;
  }
}

//----- (0000000144604280) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetEncountVolumeDB(unsigned int nID)
{
  __int64 v2; // rbx
  ADatabaseDefineStatic *v3; // rcx
  bool IsValidLowLevelFast; // al
  ADatabaseDefineStatic *v5; // rsi
  bool v6; // al
  ADatabaseDefineStatic *v7; // rdi
  TArray<FEncountVolumeBase,TSizedDefaultAllocator<32> > *p_m_DataList; // rdi
  int ArrayIndex; // eax

  v2 = 0;
  if ( ADatabaseDefineStatic::m_Self )
  {
    if ( UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    {
      v3 = ADatabaseDefineStatic::m_Self;
      if ( ADatabaseDefineStatic::m_Self )
      {
        if ( ADatabaseDefineStatic::m_Self->m_EncountList.m_Loaded )
        {
          if ( !ADatabaseDefineStatic::m_Self
            || (IsValidLowLevelFast = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
                v3 = ADatabaseDefineStatic::m_Self,
                v5 = ADatabaseDefineStatic::m_Self,
                !IsValidLowLevelFast) )
          {
            v5 = nullptr;
          }
          if ( v3
            && UObjectBase::IsValidLowLevelFast(v3, 1)
            && ADatabaseDefineStatic::m_Self
            && ADatabaseDefineStatic::m_Self->m_EncountList.m_Loaded )
          {
            if ( !ADatabaseDefineStatic::m_Self
              || (v6 = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
                  v7 = ADatabaseDefineStatic::m_Self,
                  !v6) )
            {
              v7 = nullptr;
            }
            p_m_DataList = &v7->m_EncountVolume.m_DataList;
            if ( p_m_DataList )
            {
              ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                             (FDbAccessHelper<FNoteListBase> *)&v5->m_EncountVolume.m_DBAccess,
                             nID);
              if ( ArrayIndex >= 0 && ArrayIndex < p_m_DataList->ArrayNum )
                return &p_m_DataList->AllocatorInstance.Data[56 * ArrayIndex];
            }
          }
        }
      }
    }
  }
```

##### ◆ C-ENCOUNT-02：EncountVolume 实体化为当前 遇敌区域

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17643555-17643588
  FScriptContainerElement *EncountVolumeDB; // rbx
  FEncountVolumeBase outEncountVolume; // [rsp+20h] [rbp-29h] BYREF
  FEncountListBase outEncountList; // [rsp+60h] [rbp+17h] BYREF

  m_DangerLevel = 0;
  outEncountList.__vftable = (FEncountListBase_vtbl *)FSoundListBase::`vftable';
  memset(&outEncountList.m_id, 0, 52);
  Data = nullptr;
  if ( AQPEncountManager::m_Self )
  {
    if ( (unsigned __int8)AQPEncountManager::GetCurrentEncountList(AQPEncountManager::m_Self, &outEncountList, MapID) )
    {
      memset(&outEncountVolume.m_EnemyGroup, 0, 32);
      if ( outEncountList.m_EncountVolume > 0 )
      {
        EncountVolumeDB = UEnemyDB::GetEncountVolumeDB(outEncountList.m_EncountVolume);
        outEncountVolume.m_DangerLevel = *(_DWORD *)&EncountVolumeDB[12];
        TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
          (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EnemyGroup,
          (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&EncountVolumeDB[24]);
        TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
          (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EncountRatio,
          (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&EncountVolumeDB[40]);
        m_DangerLevel = outEncountVolume.m_DangerLevel;
        if ( outEncountVolume.m_EncountRatio.AllocatorInstance.Data )
          FMemory::Free(outEncountVolume.m_EncountRatio.AllocatorInstance.Data);
        if ( outEncountVolume.m_EnemyGroup.AllocatorInstance.Data )
          FMemory::Free(outEncountVolume.m_EnemyGroup.AllocatorInstance.Data);
      }
    }
    Data = outEncountList.m_Condition.AllocatorInstance.Data;
  }
  if ( Data )
    FMemory::Free(Data);
```

##### ◆ C-ENCOUNT-03：EncountVolume 选择语义

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17641114-17641225
__int64 __fastcall AQPEncountManager::ChoiceEnemyData(
        AQPEncountManager *this,
        int *EnemyGroupID,
        const FEncountVolumeBase *EncountVolume)
{
  int v3; // ebx
  unsigned __int8 v4; // si
  int m_FirstEncount; // edi
  UQPSaveData *GameSaveData; // r8
  int m_MapId; // eax
  int v11; // ecx
  unsigned int v12; // eax
  FSaveDataPlaySummary *SaveDataPlaySummary; // rax
  const TArray<int,TSizedDefaultAllocator<32> > *m_CurrentGroups; // r15
  const TArray<int,TSizedDefaultAllocator<32> > *m_CurrentRatios; // rdi
  FScriptContainerElement *Data; // rdx
  int v17; // r10d
  __int64 v18; // r9
  int *v19; // r8
  int v20; // eax
  int v21; // ecx
  int ArrayMax; // eax
  TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *p_m_ReplacedRatios; // r12
  int v24; // r13d
  const TArray<int,TSizedDefaultAllocator<32> > *v25; // rax
  __int64 v26; // r15
  FScriptContainerElement *EnemyGroupDB; // rax
  int v28; // esi
  const TArray<int,TSizedDefaultAllocator<32> > *PartyMemberPlayerIndices; // rax
  int *v30; // rdi
  __int64 ArrayNum; // r12
  int *v32; // r12
  const TArray<int,TSizedDefaultAllocator<32> > *v33; // rbx
  int RareEnemyEncountRate; // eax
  __int64 v35; // rdi
  __int64 v36; // rdi
  FScriptContainerElement *v37; // rsi
  __int64 v38; // rdi
  unsigned __int64 v39; // rdi
  double v40; // xmm2_8
  int v41; // edx
  __int128 v42; // xmm7
  __int64 v43; // r9
  __int64 v44; // r10
  FScriptContainerElement *v45; // r11
  int v46; // edi
  FScriptContainerElement *v47; // rcx
  FScriptContainerElement *v48; // r8
  int v49; // eax
  int v50; // esi
  int v51; // edx
  unsigned int PrimIDValue; // r13d
  __int64 v53; // r9
  __int64 v54; // r10
  FScriptContainerElement *v55; // rcx
  FScriptContainerElement *v56; // r8
  int v57; // eax
  __int16 v58; // ax
  int v59; // ecx
  double v60; // xmm6_8
  __int128 v61; // xmm0
  int m_TotalAppearWeight; // [rsp+30h] [rbp-59h]
  FPrimitiveComponentId Args; // [rsp+38h] [rbp-51h] BYREF
  const TArray<int,TSizedDefaultAllocator<32> > *v65; // [rsp+40h] [rbp-49h]
  const TArray<int,TSizedDefaultAllocator<32> > *v66; // [rsp+48h] [rbp-41h]
  void *Src; // [rsp+50h] [rbp-39h]
  TArray<int,TSizedDefaultAllocator<32> > rareEnemyIndex; // [rsp+58h] [rbp-31h] BYREF
  TArray<double,TSizedDefaultAllocator<32> > calculatedRatio; // [rsp+68h] [rbp-21h] BYREF
  TArray<int,TSizedDefaultAllocator<32> > playerMembers; // [rsp+78h] [rbp-11h] BYREF
  char v72; // [rsp+108h] [rbp+7Fh]

  v3 = 0;
  v4 = 0;
  *EnemyGroupID = 0;
  v72 = 0;
  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_EnemyGroups.m_Loaded )
  {
    return v4;
  }
  m_FirstEncount = EncountVolume->m_FirstEncount;
  if ( this->m_bFirstEncount )
  {
    if ( m_FirstEncount > 0 && UEnemyDB::GetEnemyGroupDB(m_FirstEncount) )
    {
      v4 = 1;
      *EnemyGroupID = m_FirstEncount;
      v72 = 1;
    }
    GameSaveData = UKSSaveDataStatics::GetGameSaveData(nullptr);
    if ( GameSaveData )
    {
      if ( !AQPEncountManager::m_Self || (AQPEncountManager::m_Self->ObjectFlags & 0x40000000) != 0 )
        m_MapId = 0;
      else
        m_MapId = AQPEncountManager::m_Self->m_CurrentEncountList.m_MapId;
      v11 = 999999999;
      if ( m_MapId < 999999999 )
        v11 = m_MapId;
      v12 = 0;
      if ( v11 > 0 )
        v12 = v11;
      Args.PrimIDValue = v12;
      SaveDataPlaySummary = UQPSaveData::GetSaveDataPlaySummary(GameSaveData);
      TArray<FPrimitiveComponentId,TSizedDefaultAllocator<32>>::AddUniqueImpl<FPrimitiveComponentId>(
        (TArray<FPrimitiveComponentId,TSizedDefaultAllocator<32> > *)&SaveDataPlaySummary->FirstEncountedMapIDs,
        &Args);
    }
    this->m_bFirstEncount = 0;
    if ( v4 )
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_EncountRatio` | `C-ENCOUNT-02:17643576` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EncountRatio,`<br>`C-ENCOUNT-02:17643579` `if ( outEncountVolume.m_EncountRatio.AllocatorInstance.Data )`<br>`C-ENCOUNT-02:17643580` `FMemory::Free(outEncountVolume.m_EncountRatio.AllocatorInstance.Data);` | 遇敌-volume 行以 `0x38` 行跨度解析；当前-遇敌 实体化器会把行 `+0x28` 处的 `m_EncountRatio` 作为 `TArray<int>` 复制。选择期间它会成为 `m_CurrentRatios`。 | 已复原 | `C-ENCOUNT-01`, `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_EnemyGroup` | `C-ENCOUNT-02:17643567` `memset(&outEncountVolume.m_EnemyGroup, 0, 32);`<br>`C-ENCOUNT-02:17643573` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EnemyGroup,`<br>`C-ENCOUNT-02:17643581` `if ( outEncountVolume.m_EnemyGroup.AllocatorInstance.Data )` plus 1 further local occurrences | 遇敌-volume 行以 `0x38` 行跨度解析；当前-遇敌 实体化器会把行 `+0x18` 处的 `m_EnemyGroup` 作为 `TArray<int>` 复制。选择期间它会成为 `m_CurrentGroups`。 | 已复原 | `C-ENCOUNT-01`, `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_FirstEncount` | `C-ENCOUNT-03:17641121` `int m_FirstEncount; // edi`<br>`C-ENCOUNT-03:17641196` `m_FirstEncount = EncountVolume->m_FirstEncount;`<br>`C-ENCOUNT-03:17641199` `if ( m_FirstEncount > 0 && UEnemyDB::GetEnemyGroupDB(m_FirstEncount) )` plus 1 further local occurrences | `m_FirstEncount` 从已实体化的 `FEncountVolumeBase` 读取。如果 first-遇敌 标志 仍已设置，并且该 群组 ID 可通过 `GetEnemyGroupDB` 解析，函数会在随机加权选择前返回该组。 | 已复原 | `C-ENCOUNT-03` |

#### ■ 追加运行时证据：遇敌区域 同时供给危险等级与敌人类型预计算

遇敌 子系统会把 `EncountList.m_EncountVolume` 解析为 `EncountVolume` 行，然后读取危险等级以及敌人组/倍率数组。另一个 helper `GetMaxEncountEnemyTypes` 会静态遍历 遇敌 列表、volumes、groups、敌人 IDs 与潜在 reinforcements，以确定可能需要预分配的不同敌人类型最大数量。

```cpp
// C export lines 17643981-17644038
FScriptContainerElement *__fastcall UEnemyDB::GetEncountVolumeDB(unsigned int nID)
{
  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_EncountVolume.m_Loaded )
    return nullptr;
  ArrayIndex = FDbAccessHelper<...>::GetArrayIndex(
                 &v2->m_EncountVolume.m_DBAccess,
                 nID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_EncountVolume.m_DataList.ArrayNum )
    return nullptr;
  return &v2->m_EncountVolume.m_DataList.AllocatorInstance.Data[56 * ArrayIndex];
}
```

```cpp
// C export lines 17643680-17643737 and 17646495-17646549, reduced
if ( outEncountList.m_EncountVolume > 0 )
{
  EncountVolumeDB = UEnemyDB::GetEncountVolumeDB(outEncountList.m_EncountVolume);
  outEncountVolume.m_DangerLevel = *(_DWORD *)&EncountVolumeDB[12];
  TArray<unsigned int,...>::operator=(
    &outEncountVolume.m_EnemyGroup,
    (const TArray<...> *)&EncountVolumeDB[24]);
  TArray<unsigned int,...>::operator=(
    &outEncountVolume.m_EncountRatio,
    (const TArray<...> *)&EncountVolumeDB[40]);
  m_DangerLevel = outEncountVolume.m_DangerLevel;
}
```

```cpp
// C export lines 17646186-17646382, reduced
for ( Data = EncountListsDB->AllocatorInstance.Data; Data != encountEnd; Data += 64 )
{
  v4 = *(_DWORD *)&Data[52];
  EncountVolumeDB = UEnemyDB::GetEncountVolumeDB(v4);
  memcpy_0(mapAllEnemyGroups.Data, *(const void **)&EncountVolumeDB[24], 4 * *(int *)&EncountVolumeDB[32]);
  for ( each group id in mapAllEnemyGroups )
  {
    EnemyGroupDB = UEnemyDB::GetEnemyGroupDB(groupId);
    for ( each enemy id in EnemyGroupDB + 56 )
    {
      EnemyDB = UEnemyDB::GetEnemyDB(enemyId);
      v16 = *(_DWORD *)&EnemyDB[24];
      append_unique(typeIDs, v16);
    }
  }
  for ( each type candidate's enemy id )
  {
    ReinforcementIndices = UEnemyDB::GetReinforcementIndices(enemyId);
    ... // append unique type ids from reinforcement enemies
  }
}
```

| 键 | 运行时解释 |
|---|---|
| `m_DangerLevel` | 在 `EncountList.m_EncountVolume` 被解析后，从 `EncountVolume +0x0C` 读取。 |
| `m_EnemyGroup` | 存储为 `+0x18/+0x20` 处的行拥有数组；类型预计算路径会把该数组复制到临时 群组-ID 列表，并解析每个 `EnemyGroups` 行。 |
| `m_FirstEncount` | 不由所示 `GetDangerLevel` 或 `GetMaxEncountEnemyTypes` 路径消费。它在这些路径中缺席并不证明全局未使用；只证明这两个常用 helper 路径会忽略它。 |
| `m_EncountRatio` | 存储为 `+0x28/+0x30` 处的行拥有数组；在为危险等级操作构建 `FEncountVolumeBase` 暂存对象时，会与 `m_EnemyGroup` 一起复制。 |

### ■ (**) `EncountList`
OT0 的随机遭遇规则表：指定哪些 encount volumes 可在哪些地图上遭遇（`m_EncountVolume` 为 `m_EncountVolume.m_id`）、遭遇前最少步数（`m_EncountStepMin`），以及遭遇发生必须满足的条件。

其他与运行时相关的键：
- `m_DisableCondition`：满足后会禁用该遭遇的 条件。
- `m_RandomStepA` 与 `m_RandomStepB`：随机步数范围/曲线的参数。这两个键似乎在 OT0 与 CotC 中总是具有相同值。
- `m_FirstEncountStepRatio`：很可能是专门用于进入地图/区域后首次遭遇的倍率，使其比普通遭遇更早或更晚。

#### ■ 运行时读取语义
【主要 C 证据】

##### ◆ C-ENCOUNT-01：EncountVolume 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17643970-17644035
      return v3;
    }
  }
  else
  {
    *nGroupNum = 0;
    return 0;
  }
}

//----- (0000000144604280) ----------------------------------------------------
FScriptContainerElement *__fastcall UEnemyDB::GetEncountVolumeDB(unsigned int nID)
{
  __int64 v2; // rbx
  ADatabaseDefineStatic *v3; // rcx
  bool IsValidLowLevelFast; // al
  ADatabaseDefineStatic *v5; // rsi
  bool v6; // al
  ADatabaseDefineStatic *v7; // rdi
  TArray<FEncountVolumeBase,TSizedDefaultAllocator<32> > *p_m_DataList; // rdi
  int ArrayIndex; // eax

  v2 = 0;
  if ( ADatabaseDefineStatic::m_Self )
  {
    if ( UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    {
      v3 = ADatabaseDefineStatic::m_Self;
      if ( ADatabaseDefineStatic::m_Self )
      {
        if ( ADatabaseDefineStatic::m_Self->m_EncountList.m_Loaded )
        {
          if ( !ADatabaseDefineStatic::m_Self
            || (IsValidLowLevelFast = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
                v3 = ADatabaseDefineStatic::m_Self,
                v5 = ADatabaseDefineStatic::m_Self,
                !IsValidLowLevelFast) )
          {
            v5 = nullptr;
          }
          if ( v3
            && UObjectBase::IsValidLowLevelFast(v3, 1)
            && ADatabaseDefineStatic::m_Self
            && ADatabaseDefineStatic::m_Self->m_EncountList.m_Loaded )
          {
            if ( !ADatabaseDefineStatic::m_Self
              || (v6 = UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
                  v7 = ADatabaseDefineStatic::m_Self,
                  !v6) )
            {
              v7 = nullptr;
            }
            p_m_DataList = &v7->m_EncountVolume.m_DataList;
            if ( p_m_DataList )
            {
              ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                             (FDbAccessHelper<FNoteListBase> *)&v5->m_EncountVolume.m_DBAccess,
                             nID);
              if ( ArrayIndex >= 0 && ArrayIndex < p_m_DataList->ArrayNum )
                return &p_m_DataList->AllocatorInstance.Data[56 * ArrayIndex];
            }
          }
        }
      }
    }
  }
```

##### ◆ C-ENCOUNTLIST-01：EncountList 复制构造函数

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17639776-17639809
FEncountListBase *__fastcall FEncountListBase::FEncountListBase(FEncountListBase *this, const FEncountListBase *__that)
{
  __int64 ArrayNum; // rbp
  FScriptContainerElement *Data; // r14

  this->__vftable = (FEncountListBase_vtbl *)FSoundListBase::`vftable';
  this->m_id = __that->m_id;
  this->m_MapId = __that->m_MapId;
  this->m_Condition.AllocatorInstance.Data = nullptr;
  ArrayNum = __that->m_Condition.ArrayNum;
  Data = __that->m_Condition.AllocatorInstance.Data;
  this->m_Condition.ArrayNum = ArrayNum;
  if ( (_DWORD)ArrayNum )
  {
    TArray<FVertexInstanceID,TSizedDefaultAllocator<32>>::ResizeForCopy(
      (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_Condition,
      ArrayNum,
      0);
    memcpy_0(this->m_Condition.AllocatorInstance.Data, Data, 4 * ArrayNum);
  }
  else
  {
    this->m_Condition.ArrayMax = 0;
  }
  this->m_DisableCondition = __that->m_DisableCondition;
  this->m_EncountStepMin = __that->m_EncountStepMin;
  this->m_RandomStepA = __that->m_RandomStepA;
  this->m_RandomStepB = __that->m_RandomStepB;
  this->m_FirstEncountStepRatio = __that->m_FirstEncountStepRatio;
  this->m_EncountVolume = __that->m_EncountVolume;
  this->m_SoundList = __that->m_SoundList;
  return this;
}

```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_DisableCondition` | `C-ENCOUNTLIST-01:17639800` `this->m_DisableCondition = __that->m_DisableCondition;` | `FEncountListBase` 复制构造函数会把 `m_DisableCondition` 作为标量字段复制。该字段是 遇敌-volume 解析前已实体化的 遇敌列表 行的一部分。 | 已复原 | `C-ENCOUNTLIST-01` |
| `m_EncountStepMin` | `C-ENCOUNTLIST-01:17639801` `this->m_EncountStepMin = __that->m_EncountStepMin;` | `FEncountListBase` 复制构造函数会把 `m_EncountStepMin` 作为标量字段复制。 | 已复原 | `C-ENCOUNTLIST-01` |
| `m_EncountVolume` | `C-ENCOUNTLIST-01:17639805` `this->m_EncountVolume = __that->m_EncountVolume;` | `FEncountListBase` 复制构造函数会复制 `m_EncountVolume`；之后的 遇敌 代码会使用该 ID 解析一个 `EncountVolume` 行。 | 已复原 | `C-ENCOUNTLIST-01`, `C-ENCOUNT-01` |
| `m_FirstEncountStepRatio` | `C-ENCOUNTLIST-01:17639804` `this->m_FirstEncountStepRatio = __that->m_FirstEncountStepRatio;` | `FEncountListBase` 复制构造函数会把 `m_FirstEncountStepRatio` 作为标量字段复制。 | 已复原 | `C-ENCOUNTLIST-01` |
| `m_RandomStepA` | `C-ENCOUNTLIST-01:17639802` `this->m_RandomStepA = __that->m_RandomStepA;` | `FEncountListBase` 复制构造函数会把 `m_RandomStepA` 作为标量字段复制。 | 已复原 | `C-ENCOUNTLIST-01` |
| `m_RandomStepB` | `C-ENCOUNTLIST-01:17639803` `this->m_RandomStepB = __that->m_RandomStepB;` | `FEncountListBase` 复制构造函数会把 `m_RandomStepB` 作为标量字段复制。 | 已复原 | `C-ENCOUNTLIST-01` |

#### ■ 追加运行时证据：当前 遇敌列表 选择受 条件 门控 且特定于地图

`AQPEncountManager::GetCurrentEncountList` 展示了 `m_MapId`、`m_Condition` 与 `m_DisableCondition` 的操作含义。行会先按当前 map ID 过滤；若 disable 条件 为 true，则被拒绝；最后，只有 `m_Condition` 中全部非零条件都求值为 true 时才会被接受。

```cpp
// C export lines 17643198-17643450, reduced
TArray<FEncountListBase,...> *__fastcall AQPEncountManager::GetCurrentEncountList(
        AQPEncountManager *this,
        FEncountListBase *EncountList,
        unsigned int MapID)
{
  result = UEnemyDB::GetEncountListsDB();
  for ( Data = result->AllocatorInstance.Data; Data != end; ++Data )
  {
    if ( MapID != Data->m_MapId )
      continue;
    m_DisableCondition = Data->m_DisableCondition;
    if ( m_DisableCondition )
    {
      if ( UQuestUtility::GetQuestCondition(m_DisableCondition, &condData)
        && UQuestUtility::CheckQuestTaskCondition(&condData, 0, 0, 0, 0) )
        continue;
    }
    conditions = Data->m_Condition;
    TArray<unsigned int,...>::Remove(&conditions, 0);
    for ( each condition in conditions )
    {
      if ( !UQuestUtility::GetQuestCondition(condition, &condData) )
        reject;
      if ( !UQuestUtility::CheckQuestTaskCondition(&condData, 0, 0, 0, 0) )
        reject;
    }
    append_candidate(encList, Data);
  }
  if ( encList.ArrayNum )
    FEncountListBase::copy(EncountList, encList[0]);
}
```

```cpp
// C export lines 17643518-17643527
EncountList->m_id = *(_DWORD *)&encList.AllocatorInstance.Data[8];
EncountList->m_MapId = *(_DWORD *)&v11[12];
TArray<unsigned int,...>::operator=(
  &EncountList->m_Condition,
  (const TArray<...> *)&encList.AllocatorInstance.Data[16]);
EncountList->m_DisableCondition = *(_DWORD *)&v11[32];
EncountList->m_EncountStepMin = *(_DWORD *)&v11[36];
EncountList->m_RandomStepA = *(_DWORD *)&v11[40];
EncountList->m_RandomStepB = *(_DWORD *)&v11[44];
EncountList->m_FirstEncountStepRatio = *(float *)&v11[48];
EncountList->m_EncountVolume = *(_DWORD *)&v11[52];
EncountList->m_SoundList = *(_DWORD *)&v11[56];
```

| 键 | 运行时解释 |
|---|---|
| `m_MapId` | `GetCurrentEncountList` 的主过滤器；只有 `m_MapId == current map id` 的行会保留为候选。 |
| `m_DisableCondition` | 如果存在且其 任务 条件 求值为 true，则在测试普通条件前拒绝该行。 |
| `m_Condition` | 复制到临时数组，移除零，然后所有剩余 任务 条件 都必须通过。这是在移除零之后的合取条件，而不是概率性条件。 |
| `m_EncountStepMin`, `m_RandomStepA`, `m_RandomStepB`, `m_FirstEncountStepRatio`, `m_EncountVolume`, `m_SoundList` | 从行偏移 `+0x24`、`+0x28`、`+0x2C`、`+0x30`、`+0x34` 与 `+0x38` 原样复制到所选 `FEncountListBase` 暂存对象中。所示函数会选择并复制这些值；它本身不执行随机步数 掷值。 |


### ■ (*) `EncountEffectTypeID`
追踪不同 encount 效果，`m_SeId` 很可能指定要播放的音效。
#### ■ 运行时读取语义
【主要 C 证据】

##### ◆ C-STATIC-01：代表性的静态 DB 访问器表面

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17061381-17061417
void __fastcall ADatabaseDefineStatic::execGetBattleCameraParams(
        UObject *Context,
        FFrame *Stack,
        char *Z_Param__Result)
{
  Stack->Code += Stack->Code != nullptr;
  TSet<TTuple<unsigned int,float>,TDefaultMapHashableKeyFuncs<unsigned int,float,0>,FDefaultSetAllocator>::operator=(
    (TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)(Z_Param__Result + 8),
    (const TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)&Context[313].ClassPrivate);
  TSet<TTuple<FName,bool>,TDefaultMapHashableKeyFuncs<FName,bool,0>,FDefaultSetAllocator>::operator=(
    (TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)(Z_Param__Result + 88),
    (const TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)&Context[315].ClassPrivate);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 168),
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&Context[317].ClassPrivate);
  *((_DWORD *)Z_Param__Result + 46) = Context[317].OuterPrivate.ObjectPtr.ObjectPtr.Handle;
  *((_QWORD *)Z_Param__Result + 24) = Context[318].__vftable;
  TArray<FBattleCameraParamsBase,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FBattleCameraParamsBase,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 200),
    (const TArray<FBattleCameraParamsBase,TSizedDefaultAllocator<32> > *)&Context[318].ObjectFlags);
  Z_Param__Result[216] = Context[318].NamePrivate.ComparisonIndex.Value;
}

//----- (0000000144374FB0) ----------------------------------------------------
void __fastcall ADatabaseDefineStatic::execGetBattleCameraParamsDataList(
        UObject *Context,
        FFrame *Stack,
        TArray<FBattleCameraParamsBase,TSizedDefaultAllocator<32> > *Z_Param__Result)
{
  Stack->Code += Stack->Code != nullptr;
  TArray<FBattleCameraParamsBase,TSizedDefaultAllocator<32>>::operator=(
    Z_Param__Result,
    (const TArray<FBattleCameraParamsBase,TSizedDefaultAllocator<32> > *)&Context[318].ObjectFlags);
}

//----- (0000000144374FE0) ----------------------------------------------------
void __fastcall ADatabaseDefineStatic::execGetBattleCameraParamsIsLoaded(
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_SeId` | 供应的 C 导出 中未复原出精确具名字段或无歧义偏移槽位。 | C 导出 中未复原出精确的 `m_SeId` 符号或键级消费者。只能基于此 导出 断言表暴露/静态 DB 表面。 | 否定性/部分复原 | `C-STATIC-01` |

### ■ (*) `EnemyGroupsByCondition`
满足特定条件时要生成的敌人组集合。用于在 Bestower of All 第 7 章中，与 Ringbearer 一起生成 Accurst Flame。

#### ■ 运行时读取语义
`EnemyGroupsByCondition` 是 条件-to-群组 选择器。C 导出 显示它会从该行复制两个对齐数组，按顺序求值每个 条件，并返回与第一个满足的 任务 条件 相同数组索引处的 敌人-群组 ID。

```cpp
// C export lines 17644403-17644517, reduced
__int64 __fastcall UEnemyDB::GetEnemyGroupByCondition(int EnemyGroupsByConditionID)
{
  if ( UEnemyDB::CheckEnemyDB() )
  {
    p_m_DataList = &v4->m_EnemyGroupsByCondition.m_DataList;
    if ( (int)EnemyGroupsByConditionID >= 0
      && (int)EnemyGroupsByConditionID < v4->m_EnemyGroupsByCondition.m_DataList.ArrayNum )
    {
      v7 = (__int64)&p_m_DataList->AllocatorInstance.Data[56 * EnemyGroupsByConditionID];
      enemyGroupsByCondition.m_Conditions.ArrayNum = *(int *)(v7 + 32);
      memcpy_0(enemyGroupsByCondition.m_Conditions.AllocatorInstance.Data, *(const void **)(v7 + 24), 4 * count);
      enemyGroupsByCondition.m_EnemyGroups.ArrayNum = *(int *)(v7 + 48);
      memcpy_0(enemyGroupsByCondition.m_EnemyGroups.AllocatorInstance.Data, *(const void **)(v7 + 40), 4 * count2);
      for ( each condition index v14 )
      {
        QuestConditionPtr = UQuestUtility::GetQuestConditionPtr(*v13);
        if ( QuestConditionPtr
          && UQuestUtility::CheckQuestTaskCondition(QuestConditionPtr, 0, 0, 0, 0) )
        {
          if ( v14 >= 0 && v14 < enemyGroupsByCondition.m_EnemyGroups.ArrayNum )
            return enemyGroupsByCondition.m_EnemyGroups[v14];
        }
      }
    }
  }
  return 0;
}
```

| 键 | 运行时解释 |
|---|---|
| `m_Conditions` | 存储为行数组 `+0x18/+0x20`。每个值都被当作 任务 条件 ID，并通过 `UQuestUtility::GetQuestConditionPtr` 与 `CheckQuestTaskCondition`。 |
| `m_EnemyGroups` | 存储为行数组 `+0x28/+0x30`。它与 `m_Conditions` 按位置对齐；第一个为 true 的 条件 会返回 `m_EnemyGroups[i]`。 |
| `m_id` | 在所示路径中，函数会用所供应整数索引 `m_DataList`，而不是使用 `GetArrayIndex` 查找。对于扩展而言，这是应谨慎测试非连续 ID 的理由：该函数表现得像数组索引访问器，而不是键查找访问器。 |

### ■ (**) `SymbolEnemyList`
指定哪些大地图 sprite 或对象与敌人组被分配给符号/精英敌人，以及其等级。
- `m_GroupId`：与此符号敌人条目关联的 敌人 群组。
- `m_Index`：此敌人在 敌人群组 中的索引。多个符号敌人共享同一个 GroupId 或 生成 controller 时常用。
- `m_Dir`：符号敌人默认面向的方向。
- `m_ObjectId`：用特定对象覆盖符号敌人的大地图 sprite（例如 Bestower of Power 第 3 章中 Tatloch 的船）。
- `m_RepopCount`：重生/再填充计数，即符号敌人重生的频率。OT0 敌人默认为 0；CotC 敌人并非如此（重新加载地图后敌人不会立刻重新出现）。
- `m_Redrawing`：在 OT0 与 CotC 中始终设为 0。
- `m_TargetLevel1` 与 `m_TargetLevel2`：可能是符号敌人的等级范围。
- `m_Scale`：场地中敌人符号的缩放倍率（纯视觉/碰撞大小）。
- 
#### ■ 运行时读取语义
【主要 C 证据】
##### ◆ C-SYMBOL-01：SymbolEnemyList 行实体化器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17851282-17851325
char __fastcall FSymbolEnemyList::GetData(FSymbolEnemyList *this, unsigned int dbID, FSymbolEnemyListBase *outData)
{
  FDbAccessHelper<FSymbolEnemyListBase> *p_m_DBAccess; // rdi
  int ArrayIndex; // eax
  const TArray<FSymbolEnemyListBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rdx
  __int64 v7; // rcx

  p_m_DBAccess = &this->m_DBAccess;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&this->m_DBAccess,
                 dbID);
  if ( ArrayIndex < 0 )
    return 0;
  m_DatabasePtr = p_m_DBAccess->m_DatabasePtr;
  if ( !m_DatabasePtr || ArrayIndex >= m_DatabasePtr->ArrayNum )
    return 0;
  v7 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[48 * ArrayIndex];
  outData->m_id = *(_DWORD *)(v7 + 8);
  outData->m_GroupId = *(_DWORD *)(v7 + 12);
  outData->m_Index = *(_DWORD *)(v7 + 16);
  outData->m_Dir = *(_DWORD *)(v7 + 20);
  outData->m_ObjectId = *(_DWORD *)(v7 + 24);
  outData->m_RepopCount = *(_DWORD *)(v7 + 28);
  outData->m_Redrawing = *(_DWORD *)(v7 + 32);
  outData->m_TargetLevel1 = *(_DWORD *)(v7 + 36);
  outData->m_TargetLevel2 = *(_DWORD *)(v7 + 40);
  outData->m_Scale = *(float *)(v7 + 44);
  return 1;
}

//----- (0000000144705CE0) ----------------------------------------------------
char __fastcall FTreasureBoxList::GetData(FTreasureBoxList *this, int dbID, FTreasureBoxListBase *outData)
{
  int m_IdFlag; // eax
  int v7; // r10d
  FScriptContainerElement *Data; // rdx
  TSizedInlineAllocator<1,32,TSizedDefaultAllocator<32> >::ForElementType<FSetElementId> *p_Hash; // rax
  int HashSize; // ecx
  int v11; // r8d
  int v12; // eax
  FScriptContainerElement *v13; // rdx
  __int64 v14; // rax
  FScriptContainerElement *v16; // rcx
  const TArray<FTreasureBoxListBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
```

##### ◆ C-SYMBOL-03：符号敌人运行时 赋值 与 GroupId/Index 到 EnemyGroups 的桥接

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17650570-17650715
  unsigned int m_GroupId; // edx
  FScriptContainerElement *v14; // rbx
  FDbAccessHelper<FEnemyGroupsBase> *p_m_DBAccess; // rsi
  int ArrayIndex; // eax
  const TArray<FEnemyGroupsBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v18; // rbx
  int m_Index; // eax
  signed int v20; // ecx
  unsigned int EnemyIdFromSymbolId; // eax
  FScriptContainerElement *EnemyDB; // rax
  FScriptContainerElement *EnemyTypeDB; // rax
  FEnemyGroupsBase enemyGroupData; // [rsp+20h] [rbp-41h] BYREF

  this->m_group.m_id = groupData->m_id;
  this->m_group.m_StartSymbolEnemyId = groupData->m_StartSymbolEnemyId;
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_group.m_SymbolEnemyId,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&groupData->m_SymbolEnemyId);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_group.m_probability,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&groupData->m_probability);
  this->m_data.m_id = data->m_id;
  this->m_data.m_GroupId = data->m_GroupId;
  this->m_data.m_Index = data->m_Index;
  this->m_data.m_Dir = data->m_Dir;
  this->m_data.m_ObjectId = data->m_ObjectId;
  this->m_data.m_RepopCount = data->m_RepopCount;
  this->m_data.m_Redrawing = data->m_Redrawing;
  this->m_data.m_TargetLevel1 = data->m_TargetLevel1;
  this->m_data.m_TargetLevel2 = data->m_TargetLevel2;
  m_Scale = data->m_Scale;
  v10 = 0;
  this->m_data.m_Scale = m_Scale;
  this->m_bBoss = bBoss;
  if ( !ADatabaseDefineStatic::m_Self
    || (v11 = !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
        v12 = ADatabaseDefineStatic::m_Self,
        v11) )
  {
    v12 = nullptr;
  }
  if ( v12 )
  {
    m_GroupId = this->m_data.m_GroupId;
    v14 = nullptr;
    p_m_DBAccess = &v12->m_EnemyGroups.m_DBAccess;
    memset(&enemyGroupData.m_EnemyID, 0, 37);
    enemyGroupData.m_ForcedWinAbortCondition = 0;
    enemyGroupData.m_IsAllEnemyDeadOnForceWin = 0;
    enemyGroupData.m_ForcedLoseAbortCondition = 0;
    memset(&enemyGroupData.m_ImportantFlags, 0, 23);
    enemyGroupData.m_EncountEffectTypeID = 0;
    *(_WORD *)&enemyGroupData.m_EnablePlayerShield = 0;
    enemyGroupData.m_IsContainRareEnemy = 0;
    ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                   (FDbAccessHelper<FNoteListBase> *)&v12->m_EnemyGroups.m_DBAccess,
                   m_GroupId);
    if ( ArrayIndex >= 0 )
    {
      m_DatabasePtr = p_m_DBAccess->m_DatabasePtr;
      if ( m_DatabasePtr )
      {
        if ( ArrayIndex < m_DatabasePtr->ArrayNum )
        {
          v18 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[144 * ArrayIndex];
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_EnemyID,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 56));
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_EventIndices,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 72));
          enemyGroupData.m_AbortCondition = *(_DWORD *)(v18 + 88);
          enemyGroupData.m_DefeatPriority = *(_BYTE *)(v18 + 92);
          enemyGroupData.m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);
          enemyGroupData.m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);
          enemyGroupData.m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);
          TArray<unsigned char,TSizedDefaultAllocator<32>>::operator=(
            (TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_ImportantFlags,
            (const TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)(v18 + 112));
          enemyGroupData.m_PetDear = *(_DWORD *)(v18 + 128);
          enemyGroupData.m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);
          enemyGroupData.m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);
          enemyGroupData.m_DisableSupporter = *(_BYTE *)(v18 + 134);
          enemyGroupData.m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);
          enemyGroupData.m_EnablePlayerShield = *(_BYTE *)(v18 + 140);
          enemyGroupData.m_DisableGrade = *(_BYTE *)(v18 + 141);
          enemyGroupData.m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);
          m_Index = this->m_data.m_Index;
          if ( m_Index >= 0 && m_Index < enemyGroupData.m_EnemyID.ArrayNum )
            this->m_EnemyListId = *(_DWORD *)&enemyGroupData.m_EnemyID.AllocatorInstance.Data[4 * this->m_data.m_Index];
          v14 = enemyGroupData.m_ImportantFlags.AllocatorInstance.Data;
        }
      }
    }
    if ( v14 )
      FMemory::Free(v14);
    if ( enemyGroupData.m_EventIndices.AllocatorInstance.Data )
      FMemory::Free(enemyGroupData.m_EventIndices.AllocatorInstance.Data);
    if ( enemyGroupData.m_EnemyID.AllocatorInstance.Data )
      FMemory::Free(enemyGroupData.m_EnemyID.AllocatorInstance.Data);
  }
  v20 = nReplaceTypeID;
  if ( nReplaceTypeID > 0
    || (EnemyIdFromSymbolId = UQPMapObjectUtility::GetEnemyIdFromSymbolId(this->m_data.m_id, this->m_data.m_Index),
        (EnemyDB = UEnemyDB::GetEnemyDB(EnemyIdFromSymbolId)) != nullptr)
    && (v20 = *(_DWORD *)&EnemyDB[24], v20 > 0) )
  {
    EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(v20);
    if ( EnemyTypeDB )
      v10 = *(_DWORD *)&EnemyTypeDB[124];
  }
  this->m_nSE = v10;
}

//----- (000000014460BA80) ----------------------------------------------------
void __fastcall UDLCGiftUtility::SetSystemSaveDataLink(char saveDataLinkIndex, bool flag)
{
  UKSSaveDataStatics::SetSysSaveDataFlag(nullptr, saveDataLinkIndex + 35, flag);
}

//----- (000000014460BAA0) ----------------------------------------------------
void __fastcall AFieldEnemyBase::SetupCollition(AFieldEnemyBase *this)
{
  UPaperFlipbook *m_UseFlipbook; // r8
  float v3; // xmm1_4
  UCapsuleComponent *m_CapsuleComponent; // rbx
  float v5; // xmm1_4
  float v6; // xmm6_4
  __m128d v7; // xmm8
  double v8; // xmm9_8
  __m128d v9; // xmm6
  const std::nothrow_t *v10; // rdx
  FRotationConversionCache::FPayload *Payload; // rax
  FRotationConversionCache::FPayload *v12; // rdi
  UE::Math::TRotator<double> *p_CachedRotator; // rcx
  __m128d v15; // xmm8
  __m128d v16; // xmm4
  __m128 v17; // xmm1
  __m128 v18; // xmm2
  __m128d v19; // xmm3
  __m128d v20; // xmm5
  __int128 v21; // xmm0
  UE::Math::TVector<double> v22; // [rsp+30h] [rbp-A8h] BYREF
  UE::Math::TQuat<double> result; // [rsp+50h] [rbp-88h] BYREF
  UE::Math::TQuat<double> v24; // [rsp+70h] [rbp-68h] BYREF
  float w; // [rsp+E0h] [rbp+8h] BYREF
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_Dir` | `C-SYMBOL-01:17851302` `outData->m_Dir = *(_DWORD *)(v7 + 20);` | 该键作为行偏移 `+0x14` 处的 32 位标量读取。`FSymbolEnemyList::GetData` 会实体化该行；`ASymbolEnemy::SetSymbolData` 会把实体化值复制进活符号敌人对象。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_GroupId` | `C-SYMBOL-01:17851300` `outData->m_GroupId = *(_DWORD *)(v7 + 12);` | `m_GroupId` 会被复制进符号对象，然后用作 `EnemyGroups` ID。之后 `m_Index` 用于选择 `EnemyGroups.m_EnemyID[m_Index]`，该值成为符号敌人的 `m_EnemyListId`。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Index` | `C-SYMBOL-03:17650576` `int m_Index; // eax`<br>`C-SYMBOL-03:17650593` `this->m_data.m_Index = data->m_Index;`<br>`C-SYMBOL-03:17650657` `m_Index = this->m_data.m_Index;` plus 3 further local occurrences | `m_Index` 是符号敌人路径中的 群组槽位 选择器：`m_GroupId` 解析出 `EnemyGroups` 行后，运行时会读取 `EnemyGroups.m_EnemyID[m_Index]`。 | 已复原 | `C-SYMBOL-03` |
| `m_ObjectId` | `C-SYMBOL-01:17851303` `outData->m_ObjectId = *(_DWORD *)(v7 + 24);` | 该键作为行偏移 `+0x18` 处的 32 位标量读取。`FSymbolEnemyList::GetData` 会实体化该行；`ASymbolEnemy::SetSymbolData` 会把实体化值复制进活符号敌人对象。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Redrawing` | `C-SYMBOL-01:17851305` `outData->m_Redrawing = *(_DWORD *)(v7 + 32);` | 该键作为行偏移 `+0x20` 处的 32 位标量读取。`FSymbolEnemyList::GetData` 会实体化该行；`ASymbolEnemy::SetSymbolData` 会把实体化值复制进活符号敌人对象。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_RepopCount` | `C-SYMBOL-01:17851304` `outData->m_RepopCount = *(_DWORD *)(v7 + 28);` | 该键作为行偏移 `+0x1C` 处的 32 位标量读取。`FSymbolEnemyList::GetData` 会实体化该行；`ASymbolEnemy::SetSymbolData` 会把实体化值复制进活符号敌人对象。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Scale` | `C-SYMBOL-01:17851308` `outData->m_Scale = *(float *)(v7 + 44);` | 该键作为行偏移 `+0x2C` 处的 32 位 浮点数 读取。`FSymbolEnemyList::GetData` 会实体化该行；`ASymbolEnemy::SetSymbolData` 会把实体化值复制进活符号敌人对象。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel1` | `C-SYMBOL-01:17851306` `outData->m_TargetLevel1 = *(_DWORD *)(v7 + 36);` | 该键作为行偏移 `+0x24` 处的 32 位标量读取。`FSymbolEnemyList::GetData` 会实体化该行；`ASymbolEnemy::SetSymbolData` 会把实体化值复制进活符号敌人对象。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel2` | `C-SYMBOL-01:17851307` `outData->m_TargetLevel2 = *(_DWORD *)(v7 + 40);` | 该键作为行偏移 `+0x28` 处的 32 位标量读取。`FSymbolEnemyList::GetData` 会实体化该行；`ASymbolEnemy::SetSymbolData` 会把实体化值复制进活符号敌人对象。 | 已复原 | `C-SYMBOL-01`, `C-SYMBOL-03` |

#### ■ 追加运行时证据：`SymbolEnemyList.m_Index` 是 群组槽位 选择器，可选 类型 replacement

符号敌人初始化器会把 符号 群组 与 符号 行 复制进 actor 状态，然后将 `SymbolEnemyList.m_GroupId` 解析为 `EnemyGroups` 行。`SymbolEnemyList.m_Index` 会从 `EnemyGroups.m_EnemyID` 选择一个条目；所选 敌人 ID 成为符号 actor 上的 `m_EnemyListId`。

```cpp
// C export lines 17650559-17650624
void __fastcall ASymbolEnemy::SetSymbolData(
        ASymbolEnemy *this,
        const FSymbolEnemyGroupListBase *groupData,
        const FSymbolEnemyListBase *data,
        bool bBoss,
        int nReplaceTypeID)
{
  this->m_group.m_id = groupData->m_id;
  this->m_group.m_StartSymbolEnemyId = groupData->m_StartSymbolEnemyId;
  TArray<unsigned int,...>::operator=(&this->m_group.m_SymbolEnemyId, &groupData->m_SymbolEnemyId);
  TArray<unsigned int,...>::operator=(&this->m_group.m_probability, &groupData->m_probability);
  this->m_data.m_id = data->m_id;
  this->m_data.m_GroupId = data->m_GroupId;
  this->m_data.m_Index = data->m_Index;
  this->m_data.m_Dir = data->m_Dir;
  this->m_data.m_ObjectId = data->m_ObjectId;
  this->m_data.m_RepopCount = data->m_RepopCount;
  this->m_data.m_Redrawing = data->m_Redrawing;
  this->m_data.m_TargetLevel1 = data->m_TargetLevel1;
  this->m_data.m_TargetLevel2 = data->m_TargetLevel2;
  this->m_data.m_Scale = data->m_Scale;
  this->m_bBoss = bBoss;
```

```cpp
// C export lines 17650625-17650715
m_GroupId = this->m_data.m_GroupId;
ArrayIndex = FDbAccessHelper<...>::GetArrayIndex(
               &v12->m_EnemyGroups.m_DBAccess,
               m_GroupId);
if ( ArrayIndex >= 0 && ArrayIndex < m_DatabasePtr->ArrayNum )
{
  v18 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[144 * ArrayIndex];
  TArray<unsigned int,...>::operator=(
    &enemyGroupData.m_EnemyID,
    (const TArray<...> *)(v18 + 56));
  ...
  m_Index = this->m_data.m_Index;
  if ( m_Index >= 0 && m_Index < enemyGroupData.m_EnemyID.ArrayNum )
    this->m_EnemyListId = *(_DWORD *)&enemyGroupData.m_EnemyID.AllocatorInstance.Data[4 * this->m_data.m_Index];
}
```

```cpp
// C export lines 17650716-17650738
v20 = nReplaceTypeID;
if ( nReplaceTypeID > 0
  || (EnemyIdFromSymbolId = UQPMapObjectUtility::GetEnemyIdFromSymbolId(this->m_data.m_id, this->m_data.m_Index),
      (EnemyDB = UEnemyDB::GetEnemyDB(EnemyIdFromSymbolId)) != nullptr)
  && (v20 = *(_DWORD *)&EnemyDB[24], v20 > 0) )
{
  EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(v20);
  if ( EnemyTypeDB )
    v10 = *(_DWORD *)&EnemyTypeDB[124];
}
this->m_nSE = v10;
```

| 键 | 运行时解释 |
|---|---|
| `SymbolEnemyGroupList.m_StartSymbolEnemyId` | 直接复制进符号 actor 的 `m_group` 状态。所示初始化器不会求值它；而是把它作为 群组 metadata 保留。 |
| `SymbolEnemyGroupList.m_SymbolEnemyId` | 作为数组复制进 actor 状态。所示初始化器不会抽取概率；而是保留 候选-符号 数组。 |
| `SymbolEnemyGroupList.m_probability` | 与 `m_SymbolEnemyId` 一起作为数组复制进 actor 状态；因此概率语义位于该初始化器之外。 |
| `SymbolEnemyList.m_GroupId` | 用作 `EnemyGroups.m_DBAccess` 的键；得到的 `EnemyGroups` 行会提供具体战斗敌人槽位。 |
| `SymbolEnemyList.m_Index` | 用作 `EnemyGroups.m_EnemyID` 的数组索引；这是它作为 群组槽位 选择器 的最强运行时证据。 |
| `m_Dir`, `m_ObjectId`, `m_RepopCount`, `m_Redrawing`, `m_TargetLevel1`, `m_TargetLevel2`, `m_Scale` | 直接复制进符号 actor 的 `m_data` 结构。这证明了运行时实体化。该摘录本身并不证明每个字段后续的行为消费者。 |
| `nReplaceTypeID` / `EnemyID.m_TypeID` | 如果提供了显式 replacement 类型 ID，就使用它；否则 actor 会把 `(symbol id, index)` 映射为 敌人 ID，读取该敌人的 `m_TypeID`，并解析 类型 行以取得 call SE。 |

### ■ (**) `SymbolEnemyGroupList`
似乎会把符号敌人 ID（`SymbolEnemyList` 表中的 `m_id`）分配给特定内部 ID，这些内部 ID 指代特定地图上的某些符号敌人。`m_SymbolEnemyId` 数组可能决定在 `m_StartSymbolEnemyId` 下的符号敌人被击败后、再填充时某个位置会出现哪些符号敌人，并通过 `m_probability` 为每个符号敌人指定概率。CotC 与 OT0 似乎都没有使用这些数组。
#### ■ 运行时读取语义
【主要 C 证据】
##### ◆ C-SYMBOL-02：SymbolEnemyGroupList 实体化器/运算符=

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17844258-17844273
FSymbolEnemyGroupListBase *__fastcall FSymbolEnemyGroupListBase::operator=(
        FSymbolEnemyGroupListBase *this,
        const FSymbolEnemyGroupListBase *__that)
{
  this->m_id = __that->m_id;
  this->m_StartSymbolEnemyId = __that->m_StartSymbolEnemyId;
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_SymbolEnemyId,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_SymbolEnemyId);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_probability,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_probability);
  return this;
}

//----- (00000001446FDA40) ----------------------------------------------------
```

##### ◆ C-SYMBOL-03：符号敌人运行时 赋值 与 GroupId/Index 到 EnemyGroups 的桥接

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17650570-17650715
  unsigned int m_GroupId; // edx
  FScriptContainerElement *v14; // rbx
  FDbAccessHelper<FEnemyGroupsBase> *p_m_DBAccess; // rsi
  int ArrayIndex; // eax
  const TArray<FEnemyGroupsBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v18; // rbx
  int m_Index; // eax
  signed int v20; // ecx
  unsigned int EnemyIdFromSymbolId; // eax
  FScriptContainerElement *EnemyDB; // rax
  FScriptContainerElement *EnemyTypeDB; // rax
  FEnemyGroupsBase enemyGroupData; // [rsp+20h] [rbp-41h] BYREF

  this->m_group.m_id = groupData->m_id;
  this->m_group.m_StartSymbolEnemyId = groupData->m_StartSymbolEnemyId;
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_group.m_SymbolEnemyId,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&groupData->m_SymbolEnemyId);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_group.m_probability,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&groupData->m_probability);
  this->m_data.m_id = data->m_id;
  this->m_data.m_GroupId = data->m_GroupId;
  this->m_data.m_Index = data->m_Index;
  this->m_data.m_Dir = data->m_Dir;
  this->m_data.m_ObjectId = data->m_ObjectId;
  this->m_data.m_RepopCount = data->m_RepopCount;
  this->m_data.m_Redrawing = data->m_Redrawing;
  this->m_data.m_TargetLevel1 = data->m_TargetLevel1;
  this->m_data.m_TargetLevel2 = data->m_TargetLevel2;
  m_Scale = data->m_Scale;
  v10 = 0;
  this->m_data.m_Scale = m_Scale;
  this->m_bBoss = bBoss;
  if ( !ADatabaseDefineStatic::m_Self
    || (v11 = !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
        v12 = ADatabaseDefineStatic::m_Self,
        v11) )
  {
    v12 = nullptr;
  }
  if ( v12 )
  {
    m_GroupId = this->m_data.m_GroupId;
    v14 = nullptr;
    p_m_DBAccess = &v12->m_EnemyGroups.m_DBAccess;
    memset(&enemyGroupData.m_EnemyID, 0, 37);
    enemyGroupData.m_ForcedWinAbortCondition = 0;
    enemyGroupData.m_IsAllEnemyDeadOnForceWin = 0;
    enemyGroupData.m_ForcedLoseAbortCondition = 0;
    memset(&enemyGroupData.m_ImportantFlags, 0, 23);
    enemyGroupData.m_EncountEffectTypeID = 0;
    *(_WORD *)&enemyGroupData.m_EnablePlayerShield = 0;
    enemyGroupData.m_IsContainRareEnemy = 0;
    ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                   (FDbAccessHelper<FNoteListBase> *)&v12->m_EnemyGroups.m_DBAccess,
                   m_GroupId);
    if ( ArrayIndex >= 0 )
    {
      m_DatabasePtr = p_m_DBAccess->m_DatabasePtr;
      if ( m_DatabasePtr )
      {
        if ( ArrayIndex < m_DatabasePtr->ArrayNum )
        {
          v18 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[144 * ArrayIndex];
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_EnemyID,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 56));
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_EventIndices,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 72));
          enemyGroupData.m_AbortCondition = *(_DWORD *)(v18 + 88);
          enemyGroupData.m_DefeatPriority = *(_BYTE *)(v18 + 92);
          enemyGroupData.m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);
          enemyGroupData.m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);
          enemyGroupData.m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);
          TArray<unsigned char,TSizedDefaultAllocator<32>>::operator=(
            (TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&enemyGroupData.m_ImportantFlags,
            (const TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)(v18 + 112));
          enemyGroupData.m_PetDear = *(_DWORD *)(v18 + 128);
          enemyGroupData.m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);
          enemyGroupData.m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);
          enemyGroupData.m_DisableSupporter = *(_BYTE *)(v18 + 134);
          enemyGroupData.m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);
          enemyGroupData.m_EnablePlayerShield = *(_BYTE *)(v18 + 140);
          enemyGroupData.m_DisableGrade = *(_BYTE *)(v18 + 141);
          enemyGroupData.m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);
          m_Index = this->m_data.m_Index;
          if ( m_Index >= 0 && m_Index < enemyGroupData.m_EnemyID.ArrayNum )
            this->m_EnemyListId = *(_DWORD *)&enemyGroupData.m_EnemyID.AllocatorInstance.Data[4 * this->m_data.m_Index];
          v14 = enemyGroupData.m_ImportantFlags.AllocatorInstance.Data;
        }
      }
    }
    if ( v14 )
      FMemory::Free(v14);
    if ( enemyGroupData.m_EventIndices.AllocatorInstance.Data )
      FMemory::Free(enemyGroupData.m_EventIndices.AllocatorInstance.Data);
    if ( enemyGroupData.m_EnemyID.AllocatorInstance.Data )
      FMemory::Free(enemyGroupData.m_EnemyID.AllocatorInstance.Data);
  }
  v20 = nReplaceTypeID;
  if ( nReplaceTypeID > 0
    || (EnemyIdFromSymbolId = UQPMapObjectUtility::GetEnemyIdFromSymbolId(this->m_data.m_id, this->m_data.m_Index),
        (EnemyDB = UEnemyDB::GetEnemyDB(EnemyIdFromSymbolId)) != nullptr)
    && (v20 = *(_DWORD *)&EnemyDB[24], v20 > 0) )
  {
    EnemyTypeDB = UEnemyDB::GetEnemyTypeDB(v20);
    if ( EnemyTypeDB )
      v10 = *(_DWORD *)&EnemyTypeDB[124];
  }
  this->m_nSE = v10;
}

//----- (000000014460BA80) ----------------------------------------------------
void __fastcall UDLCGiftUtility::SetSystemSaveDataLink(char saveDataLinkIndex, bool flag)
{
  UKSSaveDataStatics::SetSysSaveDataFlag(nullptr, saveDataLinkIndex + 35, flag);
}

//----- (000000014460BAA0) ----------------------------------------------------
void __fastcall AFieldEnemyBase::SetupCollition(AFieldEnemyBase *this)
{
  UPaperFlipbook *m_UseFlipbook; // r8
  float v3; // xmm1_4
  UCapsuleComponent *m_CapsuleComponent; // rbx
  float v5; // xmm1_4
  float v6; // xmm6_4
  __m128d v7; // xmm8
  double v8; // xmm9_8
  __m128d v9; // xmm6
  const std::nothrow_t *v10; // rdx
  FRotationConversionCache::FPayload *Payload; // rax
  FRotationConversionCache::FPayload *v12; // rdi
  UE::Math::TRotator<double> *p_CachedRotator; // rcx
  __m128d v15; // xmm8
  __m128d v16; // xmm4
  __m128 v17; // xmm1
  __m128 v18; // xmm2
  __m128d v19; // xmm3
  __m128d v20; // xmm5
  __int128 v21; // xmm0
  UE::Math::TVector<double> v22; // [rsp+30h] [rbp-A8h] BYREF
  UE::Math::TQuat<double> result; // [rsp+50h] [rbp-88h] BYREF
  UE::Math::TQuat<double> v24; // [rsp+70h] [rbp-68h] BYREF
  float w; // [rsp+E0h] [rbp+8h] BYREF
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_StartSymbolEnemyId` | `C-SYMBOL-02:17844263` `this->m_StartSymbolEnemyId = __that->m_StartSymbolEnemyId;` | 赋值 运算符 会把 `m_StartSymbolEnemyId` 直接复制进活符号组对象。 | 已复原 | `C-SYMBOL-02`, `C-SYMBOL-03` |
| `m_SymbolEnemyId` | `C-SYMBOL-02:17844265` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_SymbolEnemyId,`<br>`C-SYMBOL-02:17844266` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_SymbolEnemyId);` | 赋值 运算符 会把 `m_SymbolEnemyId` 作为 `TArray<int>` 复制进活符号组对象。 | 已复原 | `C-SYMBOL-02`, `C-SYMBOL-03` |
| `m_id` | `C-SYMBOL-02:17844262` `this->m_id = __that->m_id;` | 该键作为行偏移 `+0x08` 处的 32 位标量读取。赋值 运算符 会在实体化时保留 群组-列表 ID。 | 已复原 | `C-SYMBOL-02` |
| `m_probability` | `C-SYMBOL-02:17844268` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_probability,`<br>`C-SYMBOL-02:17844269` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_probability);` | 赋值 运算符 会把 `m_probability` 作为 `TArray<int>` 复制进活符号组对象。在引用的 custom-敌人 路径中未复原出概率选择消费者。 | 已复原 | `C-SYMBOL-02`, `C-SYMBOL-03` |


## ■ `Battle/` 配下
### ■ (***) `BattleCameraParams`
对应战斗摄像机预设/配置的 ID；值 `-1.0` 似乎会被游戏解释为“使用默认值”：
- `m_PosotionX/Y/Z`：战斗摄像机在 3D 空间中的位置。
- `m_RotationX/Y`：摄像机在 X/Y 方向上的旋转/倾斜。
- `m_FOV`：视野。较窄视野 = 放大/长焦观感。请记住，改变 FOV 时队伍位置是固定的。
- `m_SequencerPath`：Cinematic/Sequencer 绑定，很可能指向用于战斗摄像机切换/瞄准的 Level Sequence 或某种 Sequencer 驱动摄像机 rig 资产。
- `m_FocalDistance`, `m_FocusDistance`：到焦平面的距离（一个可能是旧命名 vs 新命名（CotC 只有 `m_FocalDistance`），或用于不同系统）。两者在 OT0 中始终设为 `-1.0`，CotC 使用不同焦距。
- `m_FocalRegion`：对焦区域大小。
- `m_NearBlurSize` 与 `m_FarBlurSize`：随距离变化的模糊强度。
- `m_MaxBokehSize`：bokeh 模糊上限。
- `m_Scale`：可能是摄像机距离缩放/镜头比例覆盖。

#### ■ 运行时读取语义
##### ◆ C-CAMERA-01：BattleCameraParams 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482680-17482745

//----- (000000014453FEE0) ----------------------------------------------------
FScriptContainerElement *__fastcall AQPBattleManager::GetCameraParam(unsigned int CameraParamID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_BattleCameraParams.m_Loaded
    || !ADatabaseDefineStatic::m_Self->m_BattleCameraSet.m_Loaded )
  {
    return nullptr;
  }
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v2 = ADatabaseDefineStatic::m_Self;
  else
    v2 = nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_BattleCameraParams.m_DBAccess,
                 CameraParamID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_BattleCameraParams.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_BattleCameraParams.m_DataList.AllocatorInstance.Data[96 * ArrayIndex];
}

//----- (000000014453FF90) ----------------------------------------------------
FScriptContainerElement *__fastcall AQPBattleManager::GetCameraParam(unsigned __int8 CameraType)
{
  ABattleCameraBase *m_BattleCamera; // rcx
  unsigned int CameraParamID; // eax

  m_BattleCamera = AQPBattleManager::m_Self->m_BattleCamera;
  if ( !m_BattleCamera || (m_BattleCamera->ObjectFlags & 0x40000000) != 0 )
    return nullptr;
  CameraParamID = ABattleCameraBase::GetCameraParamID(m_BattleCamera, CameraType);
  return AQPBattleManager::GetCameraParam(CameraParamID);
}

//----- (000000014453FFD0) ----------------------------------------------------
FScriptContainerElement *__fastcall AQPBattleManager::GetCameraSet(int targetCameraSetID)
{
  __int64 v3; // rdi
  bool v4; // zf
  ADatabaseDefineStatic *v5; // rax
  FScriptContainerElement *Data; // rcx
  FScriptContainerElement *v7; // rdx

  if ( targetCameraSetID <= 0 )
    return nullptr;
  v3 = 0;
  if ( ADatabaseDefineStatic::m_Self
    && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    && ADatabaseDefineStatic::m_Self
    && ADatabaseDefineStatic::m_Self->m_BattleCameraParams.m_Loaded
    && ADatabaseDefineStatic::m_Self->m_BattleCameraSet.m_Loaded )
  {
    if ( !ADatabaseDefineStatic::m_Self
      || (v4 = !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1),
          v5 = ADatabaseDefineStatic::m_Self,
          v4) )
    {
      v5 = nullptr;
    }
```

##### ◆ C-CAMERA-02：BattleCameraParams 结构体复制

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:16981518-16981570
char __fastcall UScriptStruct::TCppStructOps<FBattleCameraParamsBase>::Copy(
        UScriptStruct::TCppStructOps<FBattleCameraParamsBase> *this,
        TArray<unsigned short,TSizedDefaultAllocator<32> > *Dest,
        _BYTE *Src,
        int ArrayDim)
{
  int v4; // ebp
  TArray<unsigned short,TSizedDefaultAllocator<32> > *v5; // rsi
  signed __int64 v6; // r14
  int v7; // eax
  _DWORD *v8; // rbx
  TArray<unsigned short,TSizedDefaultAllocator<32> > *v9; // rdi
  const TArray<unsigned short,TSizedDefaultAllocator<32> > *v10; // rdx

  v4 = ArrayDim;
  v5 = Dest;
  if ( ArrayDim )
  {
    v6 = Src - (_BYTE *)Dest;
    do
    {
      v7 = *(int *)((char *)&v5->ArrayNum + v6);
      v8 = (_DWORD *)((char *)v5 + v6);
      v9 = v5;
      v10 = (TArray<unsigned short,TSizedDefaultAllocator<32> > *)((char *)v5 + v6 + 16);
      v5 += 6;
      v9->ArrayNum = v7;
      TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(v9 + 1, v10);
      LODWORD(v9[2].AllocatorInstance.Data) = v8[8];
      HIDWORD(v9[2].AllocatorInstance.Data) = v8[9];
      v9[2].ArrayNum = v8[10];
      v9[2].ArrayMax = v8[11];
      LODWORD(v9[3].AllocatorInstance.Data) = v8[12];
      HIDWORD(v9[3].AllocatorInstance.Data) = v8[13];
      v9[3].ArrayNum = v8[14];
      v9[3].ArrayMax = v8[15];
      LODWORD(v9[4].AllocatorInstance.Data) = v8[16];
      HIDWORD(v9[4].AllocatorInstance.Data) = v8[17];
      v9[4].ArrayNum = v8[18];
      v9[4].ArrayMax = v8[19];
      LODWORD(v9[5].AllocatorInstance.Data) = v8[20];
      HIDWORD(v9[5].AllocatorInstance.Data) = v8[21];
      v9[5].ArrayNum = v8[22];
      --v4;
    }
    while ( v4 );
  }
  return 1;
}

//----- (000000014431F810) ----------------------------------------------------
UFunction *__fastcall Z_Construct_UFunction_UBattleCommandPanelBase_SetDeathblowGaugeLevel()
{
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_FOV` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FarBlurSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalDistance` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalRegion` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocusDistance` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_MaxBokehSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_NearBlurSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_Scale` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_SequencerPath` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`（打包）；引用块中没有保留下更短的具名赋值 | `AQPBattleManager::GetCameraParam` 会以 `0x60` 行跨度解析 `BattleCameraParams` 行；已复原的 结构体复制 代码随后把该行复制为 打包 标量/字符串槽位。反编译没有为所有标量摄像机坐标保留可靠的逐字段名称，因此应谨慎处理复制序列之外的偏移。 | 已复原 | `C-CAMERA-01`, `C-CAMERA-02` |


#### ■ 深入运行时分析：`BattleCameraParams` 行语义、摄像机集 间接层与 DOF 回退

摄像机参数表通过两条不同路径被消费。第一，活动 `BattleCameraSet` 会把执行摄像机类型映射到具体 `BattleCameraParams.m_id`。第二，解析出的行会被解释为固定 96 字节记录：`+0x08` 处的 `m_id`，从 `+0x10` 开始作为 `FString` 的 `m_SequencerPath`，`+0x20..+0x34` 处的 变换/FOV 浮点数，以及 `+0x38..+0x58` 处的 景深 浮点数。这意味着 摄像机-参数 行不只是编辑器预设；它是直接复制进战斗摄像机状态的运行时 payload。

##### ◆ C-CAMERAPARAMS-LOOKUP-01：ID 查找、已加载表要求与 96 字节行跨度

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482682-17482707
FScriptContainerElement *__fastcall AQPBattleManager::GetCameraParam(unsigned int CameraParamID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_BattleCameraParams.m_Loaded
    || !ADatabaseDefineStatic::m_Self->m_BattleCameraSet.m_Loaded )
  {
    return nullptr;
  }
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v2 = ADatabaseDefineStatic::m_Self;
  else
    v2 = nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_BattleCameraParams.m_DBAccess,
                 CameraParamID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_BattleCameraParams.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_BattleCameraParams.m_DataList.AllocatorInstance.Data[96 * ArrayIndex];
}
```

解释：`BattleCameraParams` 通过数据库访问 helper 按键索引，活行跨度为 `0x60`。对 `m_BattleCameraSet.m_Loaded` 的意外依赖具有重要语义：除非 set 表也已加载，否则 参数 行不会被视为可用，因为多数调用方会通过当前 摄像机 set 抵达 摄像机 参数。

##### ◆ C-CAMERAPARAMS-SET-INDIRECTION-02：摄像机 类型 到 摄像机参数 ID 的间接层

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482709-17482720
FScriptContainerElement *__fastcall AQPBattleManager::GetCameraParam(unsigned __int8 CameraType)
{
  ABattleCameraBase *m_BattleCamera; // rcx
  unsigned int CameraParamID; // eax

  m_BattleCamera = AQPBattleManager::m_Self->m_BattleCamera;
  if ( !m_BattleCamera || (m_BattleCamera->ObjectFlags & 0x40000000) != 0 )
    return nullptr;
  CameraParamID = ABattleCameraBase::GetCameraParamID(m_BattleCamera, CameraType);
  return AQPBattleManager::GetCameraParam(CameraParamID);
}

// Octopath_Traveler0-Win64-Shipping.exe.c:17394729-17394764
m_CurrentCameraSet = this->m_CurrentCameraSet;
v3 = cameraType;
if ( !m_CurrentCameraSet )
  return 0;
switch ( v3 )
{
  case 1:  result = (unsigned int)m_CurrentCameraSet->m_Neutral; break;
  case 2:  result = (unsigned int)m_CurrentCameraSet->m_Swing; break;
  case 3:  result = (unsigned int)m_CurrentCameraSet->m_Start; break;
  case 4:  result = (unsigned int)m_CurrentCameraSet->m_Break; break;
  case 5:  result = (unsigned int)m_CurrentCameraSet->m_Victory; break;
  case 6:  result = (unsigned int)m_CurrentCameraSet->m_VictorySpecial; break;
  case 8:  result = (unsigned int)m_CurrentCameraSet->m_Deathblow; break;
  case 9:  result = (unsigned int)m_CurrentCameraSet->m_DeathblowRotation; break;
  case 10: result = (unsigned int)m_CurrentCameraSet->m_FullBoostAbilityAtk; break;
  case 11: m_FullBoostAbilitySupport = m_CurrentCameraSet->m_FullBoostAbilitySupport;
}
```

解释：`BattleCameraParams` 条目通常通过 `BattleCameraSet` 选择，而不是通过硬编码 摄像机参数 ID 选择。Camera 类型 `1` 是 中立 摄像机，`3` 为 start，`4` 为 break，`5` 为 victory，依此类推。因此，某个 摄像机参数 行作为数据可能完全有效，但除非有 摄像机 set 指向它，否则仍不会使用。

##### ◆ C-CAMERAPARAMS-TRANSFORM-CACHE-03：变换/FOV 浮点数 被复制进 战斗-manager 摄像机 caches

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17484228-17484250
CameraParamID = ABattleCameraBase::GetCameraParamID(m_BattleCamera, 1u);
CameraParam = AQPBattleManager::GetCameraParam(CameraParamID);
...
if ( CameraParam )
{
  this->m_CameraPosNeautral.X = *(float *)&CameraParam[32];
  this->m_CameraPosNeautral.Y = *(float *)&CameraParam[36];
  this->m_CameraPosNeautral.Z = *(float *)&CameraParam[40];
  this->m_CameraRotNeautral.X = *(float *)&CameraParam[44];
  this->m_CameraRotNeautral.Y = *(float *)&CameraParam[48];
  if ( this != (AQPBattleManager *)-4744LL )
    this->m_CameraFov = *(float *)&CameraParam[52];
}

// Octopath_Traveler0-Win64-Shipping.exe.c:17484264-17484274
v9 = ABattleCameraBase::GetCameraParamID(v8, 3u);
v10 = (float *)AQPBattleManager::GetCameraParam(v9);
if ( v10 )
{
  this->m_CameraPosStart.X = v10[8];
  this->m_CameraPosStart.Y = v10[9];
  this->m_CameraPosStart.Z = v10[10];
  this->m_CameraRotStart.X = v10[11];
  this->m_CameraRotStart.Y = v10[12];
  this->m_CameraFovStart = v10[13];
}
```

解释：变换 block 恰好是 `+0x20..+0x34`：位置 vector、两个 rotation 浮点数 与 FOV。因此，拼写错误的导出键 `m_PosotionX/Y/Z` 应理解为字面上的摄像机位置三元组，而不是符号标签或未使用的编辑器 metadata。

##### ◆ C-CAMERAPARAMS-SEQUENCER-PATH-04：序列器 绑定 被测试为非空 已裁剪路径

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17485973-17486019
_BOOL8 __fastcall AQPBattleManager::IsSequencerCamera(AQPBattleManager *this, unsigned int cameraParamID)
{
  FScriptContainerElement *CameraParam; // rax
  __int64 v5; // rbx
  const void *v6; // rdi
  FString sequencerPath; // [rsp+20h] [rbp-18h] BYREF

  CameraParam = AQPBattleManager::GetCameraParam(cameraParamID);
  if ( CameraParam )
  {
    v5 = *(int *)&CameraParam[24];
    v6 = *(const void **)&CameraParam[16];
    sequencerPath.Data.ArrayNum = v5;
    ...
    memcpy_0(sequencerPath.Data.AllocatorInstance.Data, v6, 2 * v5);
    FString::RemoveSpacesInline(&sequencerPath);
    v3 = sequencerPath.Data.ArrayNum > 1;
  }
  return v3;
}
```

解释：该谓词不会把 `m_SequencerPath` 解释为任意布尔值。运行时会复制 `+0x10/+0x18` 处的 `FString`，移除空格，并且只有结果字符串长度超过一个 UTF-16 元素时，才将摄像机视为 序列器-driven。因此，对于此检查而言，仅含空白的路径等同于没有 序列器 路径。

##### ◆ C-CAMERAPARAMS-DOF-SENTINEL-05：景深 字段使用 `INVALID_PARAM` sentinel 语义

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17399987-17400033
_BOOL8 __fastcall ABattleCameraBase::SetDOF(
        ABattleCameraBase *this,
        float FocalDistance,
        float FocalRegion,
        float NearTransitionRegion,
        float FarTransitionRegion,
        float NearBlurSize,
        float FarBlurSize,
        float Scale,
        float MaxBokehSize,
        float FocusDistance)
{
  ...
  if ( FocalDistance > this->INVALID_PARAM )
    v13[400] = FocalDistance;
  if ( FocalRegion > this->INVALID_PARAM )
    v13[414] = FocalRegion;
  if ( NearTransitionRegion > this->INVALID_PARAM )
    v13[415] = NearTransitionRegion;
  if ( FarTransitionRegion > this->INVALID_PARAM )
    v13[416] = FarTransitionRegion;
  if ( NearBlurSize > this->INVALID_PARAM )
    v13[418] = NearBlurSize;
  if ( FarBlurSize > this->INVALID_PARAM )
    v13[419] = FarBlurSize;
  if ( Scale > this->INVALID_PARAM )
    v13[417] = Scale;
  if ( MaxBokehSize > this->INVALID_PARAM )
    v13[403] = MaxBokehSize;
  if ( FocusDistance > this->INVALID_PARAM )
  {
    ...
  }
}
```

解释：`-1.0` 样式的值不只是约定默认值。它们会与 `ABattleCameraBase::INVALID_PARAM` 比较；等于或低于该 sentinel 的字段会被跳过，并且不会覆盖活动 cine-摄像机 设置。这会独立应用于 focal distance、focal region、near/far transition regions、near/far blur、scale、max bokeh size 与 focus distance。

##### ◆ C-CAMERAPARAMS-NEUTRAL-DOF-FALLBACK-06：DOF 插值通过 中立 摄像机 行回退

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17401853-17401944
v5 = (float *)AQPBattleManager::GetCameraParam(this->m_PrevExecuteType);
v7 = (float *)AQPBattleManager::GetCameraParam(this->m_ExecuteType);
v8 = (float *)AQPBattleManager::GetCameraParam(this->m_NeutralCameraID);
if ( !v5 || !v7 || !v8 )
  return 0;
...
v10 = v5[14];
v11 = v7[14];
if ( v10 <= FocusDistance )
  v10 = v8[14];
if ( v11 <= FocusDistance )
  v11 = v8[14];
...
v36 = v5[22];
v37 = v7[22];
if ( v36 <= FocusDistance )
  v36 = v8[22];
if ( v37 <= FocusDistance )
  v37 = v8[22];
return ABattleCameraBase::SetDOF(this, v14, INVALID_PARAM, v19, FarTransitionRegion,
                                 NearBlurSize, FarBlurSize, Scale, MaxBokehSize,
                                 FocusDistance);
```

解释：DOF 插值使用 浮点数 索引 `14..22`，即行偏移 `+0x38..+0x58`。如果任一端点包含 sentinel 值，则 中立 摄像机 行会在插值前提供 回退 值。因此，中立-摄像机 DOF 是其他摄像机模式的默认来源，而不只是另一个摄像机预设。

从这些消费者复原出的字段映射：

| 导出键 / 槽位 | 运行时位置 | 解释 |
|---|---:|---|
| `m_id` | `+0x08`；DB 键 查找 | 由 DB 访问 helper 选择，通常发生在 摄像机集 间接层之后。 |
| `m_SequencerPath` | `+0x10` 指针、`+0x18` 长度 | 作为 `FString` 复制，移除空格，非空路径会启用 序列器-摄像机 行为。 |
| `m_PosotionX/Y/Z` | `+0x20/+0x24/+0x28` | 摄像机世界位置向量，被复制到 战斗-manager 摄像机 caches。 |
| `m_RotationX/Y` | `+0x2C/+0x30` | 两个旋转分量与位置向量一同复制。 |
| `m_FOV` | `+0x34` | FOV 与 中立/start/break/victory 等摄像机状态的 变换 字段一同复制。 |
| `m_FocalDistance` | `+0x38` / 浮点数 索引 `14` | DOF 字段；若 `<= INVALID_PARAM` 则跳过，否则应用到 cine-摄像机 post-process。 |
| `m_FocalRegion` | `+0x3C` / 浮点数 索引 `15` | DOF 字段；使用相同的 sentinel/回退 规则。 |
| near/far DOF transition 槽位 | `+0x40/+0x44` / 索引 `16/17` | 即便面向指南的 结构定义 可能未突出命名，这些槽位也存在于运行时。 |
| `m_NearBlurSize`, `m_FarBlurSize` | `+0x48/+0x4C` / 索引 `18/19` | 高于 sentinel 时会分别应用到 cine-摄像机 post-process。 |
| `m_Scale` | `+0x50` / 索引 `20` | 应用于 景深 scale，而不是 战斗-布局 scale。 |
| `m_MaxBokehSize` | `+0x54` / 索引 `21` | 高于 sentinel 时应用到 cine-摄像机 max bokeh size。 |
| `m_FocusDistance` | `+0x58` / 索引 `22` | 在 post-process 写入之后由 focus-settings 分支处理。 |

### ■ (***) `BattleCameraSet`
分配给敌人组的摄像机配置集合（`BattleCameraSet.m_id`=`EnemyGroups.m_Camera`）。CotC 中不存在此表（CotC 在战斗中只使用一个 中立 摄像机 配置）。

`Kingship_structs.hpp` 为 OT0 中的一些 摄像机 sets 提供以下标签：

```hpp
// Enum Kingship.EBATTLE_CAMERA_SET
// NumValues: 0x000A
enum class EBATTLE_CAMERA_SET : uint8
{
	None                                     = 0,
	DEFAULT                                  = 1,
	BCP_BOSS_LL                              = 2,
	BCP_BOSS_XL                              = 3,
	BCP_BOSS_EX                              = 4,
	BCP_BOSS_EX2                             = 5,
	BCP_RAID                                 = 6,
	BCP_RAID_ALL                             = 7,
	BCP_RAID_PC_FOCUS_BASE                   = 8,
	EBATTLE_CAMERA_MAX                       = 9,
};
```

#### ■ `BattleCameraSet` 作为 摄像机类型 分派 表

`BattleCameraSet` 是一个紧凑的 摄像机参数 ID 表。运行时会扫描该表查找 `m_id`，随后 `ABattleCameraBase::GetCameraParamID` 会把运行时 摄像机 类型 分派到相应字段。因此，`EnemyGroups.m_CameraSet` 选择的是一组 摄像机-参数 行的*家族*，而不是单一 摄像机 行。

##### ◆ C-CAMERASET-LOOKUP-01：摄像机集 行通过 `m_id` 扫描，行跨度为 56 字节

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482722-17482761
FScriptContainerElement *__fastcall AQPBattleManager::GetCameraSet(int targetCameraSetID)
{
  FScriptContainerElement *Data; // rcx
  FScriptContainerElement *v7; // rdx

  if ( targetCameraSetID <= 0 )
    return nullptr;
  ...
  Data = v5->m_BattleCameraSet.m_DataList.AllocatorInstance.Data;
  v7 = &Data[56 * v5->m_BattleCameraSet.m_DataList.ArrayNum];
  if ( Data != v7 )
  {
    while ( *(_DWORD *)&Data[8] != targetCameraSetID )
    {
      Data += 56;
      if ( Data == v7 )
        return nullptr;
    }
    return Data;
  }
  return nullptr;
}

// Octopath_Traveler0-Win64-Shipping.exe.c:17399980-17399984
void __fastcall ABattleCameraBase::SetCurrentCameraSet(ABattleCameraBase *this, int cameraSetId)
{
  this->m_CurrentCameraSet = (const FBattleCameraSetBase *)AQPBattleManager::GetCameraSet(cameraSetId);
}
```

解释：与 `BattleCameraParams` 不同，这个 解析器 会对 `m_DataList` 执行线性行扫描。活行跨度为 `0x38`，且 `targetCameraSetID <= 0` 会被视为没有 set。

##### ◆ C-CAMERASET-TYPE-DISPATCH-02：按 摄像机 类型 进行字段级 分派

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17394729-17394764
m_CurrentCameraSet = this->m_CurrentCameraSet;
if ( !m_CurrentCameraSet )
  return 0;
switch ( cameraType )
{
  case 1:  return (unsigned int)m_CurrentCameraSet->m_Neutral;
  case 2:  return (unsigned int)m_CurrentCameraSet->m_Swing;
  case 3:  return (unsigned int)m_CurrentCameraSet->m_Start;
  case 4:  return (unsigned int)m_CurrentCameraSet->m_Break;
  case 5:  return (unsigned int)m_CurrentCameraSet->m_Victory;
  case 6:  return (unsigned int)m_CurrentCameraSet->m_VictorySpecial;
  case 8:  return (unsigned int)m_CurrentCameraSet->m_Deathblow;
  case 9:  return (unsigned int)m_CurrentCameraSet->m_DeathblowRotation;
  case 10: return (unsigned int)m_CurrentCameraSet->m_FullBoostAbilityAtk;
  case 11: return (unsigned int)m_CurrentCameraSet->m_FullBoostAbilitySupport;
}
```

解释：扩展或编辑 `BattleCameraSet` 时，应把它理解为编辑这个 分派 表。如果没有运行时 摄像机 类型 会抵达某个字段，那么该字段在普通战斗中可能不被使用，但它仍然是 set 记录中的活字段。

### ■ (***) `BattlePositions`
定义位置 ID，用于例如 `EnemyFormations` 或 `EnemyReinforcements`。`m_Priority` 可能决定坐标冲突时哪个 sprite 显示在前方，或决定敌人被选中的顺序。
#### ■ 运行时读取语义
##### ◆ C-FORM-02：Formation 位置 解析 into BattlePositions

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17644973-17645075
//----- (00000001446053B0) ----------------------------------------------------
bool __fastcall UEnemyDB::GetEnemyPlacement(
        UE::Math::TVector<double> *Position,
        int *Priority,
        unsigned int EnemyID,
        int FormID,
        int Index)
{
  FScriptContainerElement *EnemyDB; // rax
  FScriptContainerElement *EnemyFormationDB; // rax
  signed int v10; // ecx
  FScriptContainerElement *BattlePositionDB; // rax
  FScriptContainerElement *v12; // rcx
  bool v13; // dl
  float *v14; // rax
  float v15; // xmm0_4
  long double v16; // xmm1_8

  EnemyDB = UEnemyDB::GetEnemyDB(EnemyID);
  if ( !EnemyDB )
    return 0;
  if ( *(int *)&EnemyDB[8] <= 0 )
    return 0;
  if ( FormID <= 0 )
    return 0;
  EnemyFormationDB = UEnemyDB::GetEnemyFormationDB(FormID);
  if ( !EnemyFormationDB )
    return 0;
  v10 = *(_DWORD *)(*(_QWORD *)&EnemyFormationDB[16] + 4LL * Index);
  if ( v10 <= 0 )
    return 0;
  BattlePositionDB = UEnemyDB::GetBattlePositionDB(v10);
  v12 = BattlePositionDB;
  v13 = BattlePositionDB != nullptr;
  if ( BattlePositionDB )
  {
    v14 = *(float **)&BattlePositionDB[16];
    v15 = *v14;
    v16 = v14[1];
    Position->Z = v14[2];
    Position->Y = v16;
    Position->X = v15;
    *Priority = *(_DWORD *)&v12[32];
  }
  return v13;
}

//----- (0000000144605470) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyPositionCount(int FormID)
{
  unsigned int v1; // ebx
  FScriptContainerElement *EnemyFormationDB; // rax
  int *v3; // rcx
  int *i; // r8

  v1 = 0;
  if ( FormID > 0 )
  {
    EnemyFormationDB = UEnemyDB::GetEnemyFormationDB(FormID);
    if ( EnemyFormationDB )
    {
      v3 = *(int **)&EnemyFormationDB[16];
      for ( i = &v3[*(int *)&EnemyFormationDB[24]]; v3 != i; ++v3 )
      {
        if ( *v3 <= 0 )
          break;
        ++v1;
      }
    }
  }
  return v1;
}

//----- (00000001446054B0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyPositionID(int FormID, int Index)
{
  __int64 v2; // rbx
  FScriptContainerElement *EnemyFormationDB; // rax

  v2 = Index;
  if ( FormID > 0
    && (EnemyFormationDB = UEnemyDB::GetEnemyFormationDB(FormID)) != nullptr
    && (int)v2 >= 0
    && (int)v2 < *(_DWORD *)&EnemyFormationDB[24] )
  {
    return *(unsigned int *)(*(_QWORD *)&EnemyFormationDB[16] + 4 * v2);
  }
  else
  {
    return 0;
  }
}

//----- (00000001446054F0) ----------------------------------------------------
__int64 __fastcall UEnemyDB::GetEnemyPositionIndex(int FormID, int PosID)
{
  FScriptContainerElement *EnemyFormationDB; // rax
  _DWORD *v4; // r8
  __int64 v5; // rcx
  _DWORD *v6; // rax
  _DWORD *v7; // rdx

  if ( FormID <= 0 )
```

##### ◆ C-BPOS-01：BattlePositions 行 解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17643140-17643164
FScriptContainerElement *__fastcall UEnemyDB::GetBattlePositionDB(unsigned int PosID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_BattlePositions.m_Loaded )
  {
    return nullptr;
  }
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v2 = ADatabaseDefineStatic::m_Self;
  else
    v2 = nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_BattlePositions.m_DBAccess,
                 PosID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_BattlePositions.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_BattlePositions.m_DataList.AllocatorInstance.Data[40 * ArrayIndex];
}

```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_Priority` | `+0x20`（标量）；引用块中没有保留下更短的具名赋值 | 当 formation 槽位 产出 `BattlePositions` ID 后，`GetBattlePositionDB` 会解析一个 `0x28` 字节行。`GetEnemyPlacement` 在从 `+0x10` 的坐标数组复制 XYZ 后，从 `BattlePositions +0x20` 读取 priority。 | 已复原 | `C-BPOS-01`, `C-FORM-02` |


#### ■ `BattlePositions` 作为共享坐标表

`BattlePositions` 不限于敌人编队。运行时也将它用于 讨伐视图、玩家侧 staging、reinforcement placement 与 布局-set anchoring。行本身按 键 索引，行跨度为 40 字节。

##### ◆ C-BATTLEPOSITIONS-COORDINATES-01：位置行 查找 与坐标提取

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17643140-17643164
FScriptContainerElement *__fastcall UEnemyDB::GetBattlePositionDB(unsigned int PosID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_BattlePositions.m_Loaded )
  {
    return nullptr;
  }
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&v2->m_BattlePositions.m_DBAccess,
                 PosID);
  if ( ArrayIndex < 0 || ArrayIndex >= v2->m_BattlePositions.m_DataList.ArrayNum )
    return nullptr;
  else
    return &v2->m_BattlePositions.m_DataList.AllocatorInstance.Data[40 * ArrayIndex];
}

// Octopath_Traveler0-Win64-Shipping.exe.c:17643117-17643138
bool __fastcall UEnemyDB::GetBattlePosition(UE::Math::TVector<double> *Position, unsigned int PosID)
{
  FScriptContainerElement *BattlePositionDB; // rax
  float *v5; // rdx

  BattlePositionDB = UEnemyDB::GetBattlePositionDB(PosID);
  if ( !BattlePositionDB )
    return false;
  v5 = *(float **)&BattlePositionDB[16];
  Position->X = *v5;
  Position->Y = v5[1];
  Position->Z = v5[2];
  return true;
}
```

解释：公共坐标 helper 会解引用行中的 位置-vector 槽位，并写入 `X/Y/Z` 向量。C 导出 证明该表承担通用坐标服务的角色；formation 成员关系 只是其中一个消费者。

### ■ (***) `BattleEventList`
很可能是可在 `EnemyGroups.m_EventIndices` 下分配给敌人组的事件。
- `m_IsMustPlay`：事件是否可跳过（？）
- `m_EventCommand`：触发时要执行的 指令 script ID（指向 `BattleEventCommand`）
- `m_EventConditions`：Condition 类型 IDs，可能与其他文件（`TacticalList`、`SkillConditionList`、`TacticalActionList` 等）里的 `m_Conditions` 数组相同
- `m_EventParams`：条件参数
- `m_EventStatusTypes`：状态类型 过滤器 槽位。带类型的 C 导出 会把此槽位与 `m_EventConditions`、`m_EventParams`、`m_EventEnemies` 一起传入 `UBattleUtility::CheckCondition`（`Octopath_Traveler0-Win64-Shipping.exe.c`:17485857-17485873）。
- `m_EventEnemies`：可选 过滤器，用于指定条件适用于哪个 敌人 槽位/哪个 敌人 索引/哪个 part（OT0 与 CotC 中似乎都未使用）。

可能相关：在 `Kingship_structs.hpp` 中：

```hpp
// Enum Kingship.EBATTLE_EVENT_ID
// NumValues: 0x0051
enum class EBATTLE_EVENT_ID : uint8
{
	None                                     = 0,
	BE_B01_03_EG03_OPN                       = 124,
	BE_B02_03_EG04_OPN                       = 121,
	BE_B03_03_EG02_OPN                       = 122,
	BE_B04_05_EG08_OPN                       = 1,
	BE_B04_08_EG12_OPN                       = 2,
	BE_B04_08_EG13_OPN_1                     = 7,
	BE_B04_08_EG13_OPN_2                     = 8,
	BE_B04_08_EG13_OPN_3                     = 9,
	BE_B04_08_EG13_OPN_4                     = 10,
	BE_B04_08_EG13_OPN_5                     = 11,
	BE_B04_08_EG13_OPN_6                     = 12,
	BE_B04_08_EG13_OPN_7                     = 13,
	BE_B04_08_EG14_OPN                       = 3,
	BE_B04_08_EG14_ENHP_L25                  = 4,
	BE_B05_03_EG02_010                       = 14,
	BE_B05_03_EG03_010                       = 15,
	BE_B05_03_EG03_020                       = 16,
	BE_B06_03_EG03_010                       = 123,
	BE_B07_03_EG03_010                       = 17,
	BE_B07_03_EG03_020                       = 18,
	BE_B07_03_EG03_030                       = 19,
	BE_B08_03_EG04_010                       = 21,
	BE_B08_03_EG04_020                       = 22,
	BE_B08_03_EG04_030                       = 23,
	BE_B08_03_EG04_040                       = 24,
	BE_B08_03_EG04_050                       = 25,
	BE_B08_04_EG01_010                       = 26,
	BE_B08_04_EG01_020                       = 27,
	BE_B08_04_EG01_030                       = 28,
	BE_B08_04_EG01_040                       = 29,
	BE_B08_06_EG03_030                       = 129,
	BE_B08_06_EG03_010                       = 32,
	BE_B08_06_EG03_040                       = 130,
	BE_B08_06_EG03_050                       = 131,
	BE_B08_06_EG03_020                       = 33,
	BE_B08_06_EG03_060                       = 132,
	BE_B08_08_EG02_000                       = 50,
	BE_B08_08_EG02_010                       = 36,
	BE_B08_08_EG02_020                       = 37,
	BE_B08_08_EG02_030                       = 38,
	BE_B08_08AEnd_010                        = 114,
	BE_B08_08_EG03_Ring_020                  = 46,
	BE_B08_08_EG03_Ring_030                  = 47,
	BE_B08_08AEnd_020                        = 115,
	BE_B08_08_EG03_010                       = 41,
	BE_B08_08_EG03_020                       = 56,
	BE_B08_08AEnd_030                        = 118,
	BE_B08_08AEnd_040                        = 137,
	BE_TEST_00                               = 20,
	BE_T00_01_00_OPN                         = 100,
	BE_T00_01_00_END                         = 101,
	BE_SAZANTOS_SKILL_LEAN_01                = 102,
	BE_SAZANTOS_SKILL_LEAN_02                = 103,
	BE_SAZANTOS_SKILL_LEAN_03                = 104,
	BE_SAZANTOS_SKILL_LEAN_04                = 105,
	BE_SAZANTOS_SKILL_LEAN_05                = 106,
	BE_SAZANTOS_SKILL_LEAN_06                = 107,
	BE_T05_01_EG07_010                       = 108,
	BE_T05_01_EG07_020                       = 119,
	BE_T05_01_EG07_030                       = 120,
	BE_B08_07_EG09_010                       = 109,
	BE_B08_07_EG09_020                       = 110,
	BE_B08_07_EG09_030                       = 111,
	BE_B08_07_EG10_010                       = 112,
	BE_B08_07_EG10_020                       = 128,
	BE_B08_08_BEnd_010                       = 116,
	BE_B08_08_BEnd_020                       = 117,
	BE_B08_08_BEnd_030                       = 138,
	BE_J10201_01_05_010                      = 125,
	BE_OneOff_vsDragon_01_010                = 133,
	BE_OneOff_vsDragon_02_010                = 134,
	BE_OneOff_vsDragon_03_010                = 135,
	BE_OneOff_vsDragon_04_010                = 136,
	BE_EvQuSQ070_02a_01_010                  = 139,
	BE_EvQuSQ070_02a_02_010                  = 140,
	BE_EvQuSQ070_02a_03_010                  = 141,
	BE_OneOff_vsDragon_05_010                = 142,
	BE_OneOff_vsDragon_06_010                = 143,
	BE_OneOff_vsDragon_07_010                = 144,
	EBATTLE_EVENT_MAX                        = 145,
};
```
#### ■ 运行时读取语义

【主要 C 证据】

##### ◆ C-BEVENT-01：BattleEventList 行 解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482935-17482993
const FBattleEventListBase *__fastcall AQPBattleManager::GetEventListDB(AQPBattleManager *this, int EventID)
{
  __int64 v4; // rbp
  ADatabaseDefineStatic *v5; // rbx
  int ArrayIndex; // eax
  TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *p_m_BattleEventEmptyConditions; // rbx
  int v8; // edi
  __int64 ArrayNum; // rsi

  v4 = 0;
  if ( EventID > 0 )
  {
    if ( ADatabaseDefineStatic::m_Self )
    {
      if ( UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
      {
        v5 = ADatabaseDefineStatic::m_Self;
        if ( ADatabaseDefineStatic::m_Self )
        {
          if ( ADatabaseDefineStatic::m_Self->m_BattleEventList.m_Loaded )
          {
            ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                           (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_BattleEventList.m_DBAccess,
                           EventID);
            if ( ArrayIndex >= 0 && ArrayIndex < v5->m_BattleEventList.m_DataList.ArrayNum )
            {
              v4 = (__int64)&v5->m_BattleEventList.m_DataList.AllocatorInstance.Data[88 * ArrayIndex];
              p_m_BattleEventEmptyConditions = (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&this->m_BattleEventEmptyConditions;
              v8 = *(_DWORD *)(v4 + 24);
              if ( this->m_BattleEventEmptyConditions.ArrayNum < v8 )
              {
                if ( v8 < 0 )
                  UE::Core::Private::OnInvalidArrayNum(*(unsigned int *)(v4 + 24));
                this->m_BattleEventEmptyConditions.ArrayNum = 0;
                if ( v8 > this->m_BattleEventEmptyConditions.ArrayMax )
                  TArray<long,TSizedDefaultAllocator<32>>::ResizeTo(
                    (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&this->m_BattleEventEmptyConditions,
                    v8);
                for ( ; v8 > 0; *(_DWORD *)&p_m_BattleEventEmptyConditions->AllocatorInstance.Data[4 * ArrayNum] = 0 )
                {
                  ArrayNum = p_m_BattleEventEmptyConditions->ArrayNum;
                  --v8;
                  p_m_BattleEventEmptyConditions->ArrayNum = ArrayNum + 1;
                  if ( (unsigned int)(ArrayNum + 1) > p_m_BattleEventEmptyConditions->ArrayMax )
                    TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                      p_m_BattleEventEmptyConditions,
                      ArrayNum);
                }
              }
            }
          }
        }
      }
    }
  }
  return (const FBattleEventListBase *)v4;
}
// 1445404F7: control flows out of bounds to 1445404FC

```

##### ◆ C-BEVENT-02：BattleEventList 条件 求值

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17478586-17478649
_BOOL8 __fastcall AQPBattleManager::CheckBattleEvent(AQPBattleManager *this, bool IsBeforeResult)
{
  FScriptContainerElement *Data; // rbx
  TArray<int,TSizedDefaultAllocator<32> > *Weapons; // r15
  bool v4; // bp
  FScriptContainerElement *v7; // r14
  int v8; // esi
  const FBattleEventListBase *EventListDB; // rax
  int v10; // eax
  int *p_m_NowEventID; // rdx

  Data = this->m_BattleEventIDs.AllocatorInstance.Data;
  Weapons = &this->m_BattleEventEmptyConditions;
  v4 = 0;
  v7 = &Data[4 * this->m_BattleEventIDs.ArrayNum];
  if ( Data == v7 )
    return v4;
  while ( 1 )
  {
    v8 = *(_DWORD *)Data;
    if ( *(_DWORD *)Data <= 0 )
      goto LABEL_6;
    EventListDB = AQPBattleManager::GetEventListDB(this, v8);
    if ( !EventListDB )
      goto LABEL_6;
    this->m_IsBeforeResultEvent = IsBeforeResult;
    v10 = UBattleUtility::CheckConditions(
            &EventListDB->m_EventConditions,
            &EventListDB->m_EventParams,
            &EventListDB->m_EventStatusTypes,
            Weapons,
            Weapons,
            Weapons,
            &EventListDB->m_EventEnemies,
            Weapons,
            nullptr,
            nullptr,
            nullptr,
            nullptr);
    v4 = v10 > 0;
    if ( v10 > 0 )
      break;
    this->m_IsBeforeResultEvent = 0;
LABEL_6:
    Data += 4;
    if ( Data == v7 )
    {
      p_m_NowEventID = &this->m_NowEventID;
      goto LABEL_8;
    }
  }
  p_m_NowEventID = &this->m_NowEventID;
  this->m_NowEventCommandIndex = -1;
  this->m_NowEventID = v8;
  this->m_EventOutPhase = 0;
  this->m_IsEventContinue = 0;
LABEL_8:
  if ( v4 )
    TArray<unsigned int,TSizedDefaultAllocator<32>>::Remove(
      (TArray<enum EMeshLODIdentifier,TSizedDefaultAllocator<32> > *)&this->m_BattleEventIDs,
      (const EMeshLODIdentifier *)p_m_NowEventID);
  return v4;
}

```

##### ◆ C-BEVENT-03：共享 战斗-条件 tuple 求值器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17389550-17389705
  unsigned int v13; // r13d
  int v15; // edx
  int v16; // edi
  FScriptContainerElement *v18; // rax
  __int64 v20; // rbx
  __int64 v21; // rax
  int v22; // edx
  int v23; // r8d
  ESKILL_AILMENT_TYPE v24; // r9d
  int Weapon; // r10d
  int Magic; // r11d
  int v27; // ebp
  unsigned int v28; // eax
  bool v29; // zf
  int v30; // ecx
  int v32; // [rsp+60h] [rbp-48h]
  FScriptContainerElement *v33; // [rsp+68h] [rbp-40h]

  Data = (int *)Conditions->AllocatorInstance.Data;
  v13 = 0;
  v15 = 0;
  v16 = 0;
  v32 = 0;
  v18 = &Conditions->AllocatorInstance.Data[4 * Conditions->ArrayNum];
  v33 = v18;
  if ( Conditions->AllocatorInstance.Data == v18 )
    goto LABEL_33;
  v20 = 0;
  do
  {
    if ( *Data <= 0 )
      goto LABEL_29;
    if ( v20 < 0 || v16 >= Params->ArrayNum )
    {
      v22 = 0;
      v21 = 4 * v20;
      if ( v20 < 0 )
        goto LABEL_10;
    }
    else
    {
      v21 = 4 * v20;
      v22 = *(_DWORD *)&Params->AllocatorInstance.Data[4 * v20];
    }
    if ( v16 < Statuses->ArrayNum )
    {
      v23 = *(_DWORD *)&Statuses->AllocatorInstance.Data[v21];
      goto LABEL_11;
    }
LABEL_10:
    LOBYTE(v23) = 0;
    if ( v20 < 0 )
      goto LABEL_13;
LABEL_11:
    if ( v16 >= Ailments->ArrayNum )
    {
LABEL_13:
      v24 = 0;
      if ( v20 < 0 )
        goto LABEL_16;
      goto LABEL_14;
    }
    v24 = *(_DWORD *)&Ailments->AllocatorInstance.Data[v21];
LABEL_14:
    if ( v16 < Weapons->ArrayNum )
    {
      Weapon = *(_DWORD *)&Weapons->AllocatorInstance.Data[v21];
      goto LABEL_17;
    }
LABEL_16:
    LOBYTE(Weapon) = 0;
    if ( v20 < 0 )
      goto LABEL_19;
LABEL_17:
    if ( v16 >= Magics->ArrayNum )
    {
LABEL_19:
      LOBYTE(Magic) = 0;
      if ( v20 < 0 )
        goto LABEL_22;
      goto LABEL_20;
    }
    Magic = *(_DWORD *)&Magics->AllocatorInstance.Data[v21];
LABEL_20:
    if ( v16 < EnemyID->ArrayNum )
    {
      v27 = *(_DWORD *)&EnemyID->AllocatorInstance.Data[v21];
      goto LABEL_23;
    }
LABEL_22:
    v27 = 0;
    if ( v20 < 0 )
      goto LABEL_25;
LABEL_23:
    if ( v16 >= EquipID->ArrayNum )
    {
LABEL_25:
      v28 = 0;
      goto LABEL_26;
    }
    v28 = *(_DWORD *)&EquipID->AllocatorInstance.Data[v21];
LABEL_26:
    ++v16;
    ++v20;
    v29 = (unsigned __int8)UBattleUtility::CheckCondition(
                             *Data,
                             v22,
                             (ECHARA_STATUS)v23,
                             v24,
                             Weapon,
                             Magic,
                             v27,
                             v28,
                             Enforcer,
                             Target,
                             pEnemies,
                             pFriendlies) == 0;
    v18 = v33;
    v30 = v32 + 1;
    if ( v29 )
      v30 = v32;
    v15 = v30;
    v32 = v30;
LABEL_29:
    ++Data;
  }
  while ( Data != (int *)v18 );
  v13 = 0;
  if ( v16 > 0 )
  {
    v13 = -1;
    if ( v15 == v16 )
      v13 = 1;
  }
LABEL_33:
  UBattleUtility::s_bIsORCondition = 0;
  UBattleUtility::s_JobCountID = 0;
  return v13;
}

//----- (00000001444D3550) ----------------------------------------------------
bool __fastcall UBattleTacticalComponent::CheckExtensionPresage(
        UBattleTacticalComponent *this,
        bool isActionSkip,
        bool updatedTurn)
{
  int m_PresageTurn; // eax
  int m_PresageLeft; // ecx
  __int64 ArrayNum; // rbx
  TArray<int,TSizedDefaultAllocator<32> > *p_m_ActionIDs; // r14
  __int64 v10; // r15
  size_t v11; // rbx
  bool result; // al
  int v13; // eax
  int v14; // ebx
  unsigned int v15; // edx
```

##### ◆ C-BCMD-01：BattleEventCommand 行 解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482904-17482934
FScriptContainerElement *__fastcall AQPBattleManager::GetEventCommandDB(
        AQPBattleManager *this,
        int CommandID,
        int Index)
{
  ADatabaseDefineStatic *v5; // rbx
  int ArrayIndex; // eax
  int v7; // ecx

  if ( CommandID <= 0
    || !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
  {
    return nullptr;
  }
  v5 = ADatabaseDefineStatic::m_Self;
  if ( !ADatabaseDefineStatic::m_Self || !ADatabaseDefineStatic::m_Self->m_BattleEventCommand.m_Loaded )
    return nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_BattleEventCommand.m_DBAccess,
                 CommandID);
  v7 = ArrayIndex + Index;
  if ( ArrayIndex < 0 )
    v7 = ArrayIndex;
  if ( v7 >= 0 && v7 < v5->m_BattleEventCommand.m_DataList.ArrayNum )
    return &v5->m_BattleEventCommand.m_DataList.AllocatorInstance.Data[200 * v7];
  else
    return nullptr;
}

//----- (00000001445403F0) ----------------------------------------------------
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_Conditions` | `line 16978495` `v8 = this->m_Conditions.AllocatorInstance.Data;` in `void __fastcall FSkillConditionListBase::~FSkillConditionListBase(FSkillConditionListBase *this)`<br>`line 17044430` `this->m_Conditions.AllocatorInstance.Data = nullptr;` in `FSkillConditionListBase *__fastcall FSkillConditionListBase::FSkillConditionListBase(` | 在本节中，`m_Conditions` 指的是与 `m_EventConditions` 使用同一 条件-ID 命名空间的字段；`BattleEventList` 上的活字段名是 `m_EventConditions`。 | 已复原 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventCommand` | `line 17481721` `EventCommandDB = AQPBattleManager::GetEventCommandDB(this, EventListDB->m_EventCommand, m_NowEventCommandIndex);` in `char __fastcall AQPBattleManager::ExecuteBattleEventCommand(AQPBattleManager *this)`<br>`line 17483882` `int m_EventCommand; // eax` in `void __fastcall AQPBattleManager::InitBattleEvent(AQPBattleManager *this, bool forceFlag)` | 事件行被选中后，`ExecuteBattleEventCommand` 会把 `EventListDB->m_EventCommand` 与当前 指令 索引 传给 `GetEventCommandDB`，后者解析 指令-行 sequence。 | 已复原 | `C-BEVENT-01`, `C-BCMD-01` |
| `m_EventConditions` | `C-BEVENT-02:17478613` `&EventListDB->m_EventConditions,` | 战斗-事件 求值器 会把 `m_EventConditions` 作为主要 条件-ID 槽位传给 `UBattleUtility::CheckConditions`。 | 已复原 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventEnemies` | `C-BEVENT-02:17478619` `&EventListDB->m_EventEnemies,` | 事件 求值器 会把 `m_EventEnemies` 作为 敌人 ID/过滤器 槽位传给共享 条件 求值器。该 求值器 中缺失的 槽位 会默认置零。 | 已复原 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventParams` | `C-BEVENT-02:17478614` `&EventListDB->m_EventParams,` | 事件 求值器 会把 `m_EventParams` 作为与 `m_EventConditions` 按 槽位 对齐的参数槽位传入。 | 已复原 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventStatusTypes` | `C-BEVENT-02:17478615` `&EventListDB->m_EventStatusTypes,` | 事件 求值器 会把 `m_EventStatusTypes` 作为与 `m_EventConditions` 按 槽位 对齐的状态 过滤器 槽位传入。 | 已复原 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_IsMustPlay` | `line 17485812` `if ( !pEvent->m_IsMustPlay )` in `char __fastcall AQPBattleManager::IsMustPlayEvent(AQPBattleManager *this, const FBattleEventListBase *pEvent)` | `m_IsMustPlay` 被 事件-控制 代码作为 布尔 门控 读取。若为 false，事件不会被强制走 must-play 路径。 | 已复原 | `C-BEVENT-02`, `C-BEVENT-03` |

### ■ (***) `BattleEventCommand`
包含 战斗 事件 sequence 中的脚本步骤。`BattleEventList` 描述在什么条件下应该运行什么，而 `BattleEventCommand` 似乎执行实际的脚本步骤（显示对话、播放动画、生成特效、切换敌人贴图、更改 BGM 等）。

- `m_NextWait`：执行该 指令 后继续前的延迟秒数
- `m_isWaitSkillTime`：如果为 true，则等待 技能 animation 完成
- `m_isSwitchActionSkip`：允许/强制跳过 动作 sequences
- `m_PlayBgmBlock`：可能会阻止 BGM 变化，直到某事完成
- `m_PerformerID`：可能是 party/guest/敌人 执行者 列表 的索引，或特殊 ID
- `m_PerformerIsLeader`：以 party leader 作为 执行者
- `m_PerformerEnemy`：如果非零，表示 执行者 是敌人（似乎以 `EnemyID.m_id` 作为输入）
- `m_TargetID`：可能表示 forced 动作/技能 的目标。可能相关：

```hpp
// Enum Kingship.EBATTLE_TARGET_TYPE
// NumValues: 0x0008
enum class EBATTLE_TARGET_TYPE : uint8
{
	eSELF                                    = 0,
	ePARTY_SINGLE                            = 1,
	ePARTY_ALL                               = 2,
	eENEMY_SINGLE                            = 3,
	eENEMY_ALL                               = 4,
	eALL                                     = 5,
	eALL_SINGLE                              = 6,
	EBATTLE_TARGET_MAX                       = 7,
};
```

- `m_EnableTarget`,`m_EnableAction`：用于在该 指令 期间/之后启用玩家 targeting/动作 的开关。OT0 反编译中有一个 `BattleEventCommand` 读取方，其中 `m_EnableAction` 会调用 `AQPBattleCharacterBase::DisableNotAction(Character)`，并在状态变化时刷新 状态 UI，因此该 标志 可以在脚本化 战斗 事件 中清除 no-动作 状态（`Octopath_Traveler0-Win64-Shipping.exe.c`:17477565-17477624）。
- `m_NameID`：说话人姓名的本地化 ID（位于 `GameTextCharacter` 下）
- `m_TextID`：对话行的本地化 ID（位于 `GameTextEvent` 下）
- `m_BalloonType`：气泡/对话框样式（1 = 可能为普通 speech bubble）
- `m_NoTail`：无 speech-bubble tail
- `m_BaloonOffsetX/Y`：气泡相对于 执行者 的位置偏移
- `m_NoName`：如果为 true，则隐藏说话人姓名
- `m_NarrationID`：取 `NarrationList.m_id` 中的 ID
- - `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`：narration-style 相关键
- `m_AnimID`：说话期间（或前后）在 执行者 上播放哪个动画
- `m_DirID`：执行者 的 direction/facing preset
- `m_isRewriteIdleMotion`：在 指令 后保留 idle 覆盖
- `m_SoundID`：播放声音（可能是 `SoundList` ID）
- `m_SoundHandle`：可能是运行时 handle 槽位（例如启动 SFX 并保存 handle，以便后续 指令 停止/替换它）（？）
- `m_EffectID`：生成特效（可能是 `EffectList` ID）
- `m_EffectHandle`：可能是供后续操作/停止使用的运行时 handle 槽位（？）
- `m_EffectPosition`：特效生成位置（执行者、target、screen 等）
- `m_BgmID`：更改 BGM（同样可能是 `SoundList` ID）
- `m_FadeTime`：BGM 变化的 fade duration
- `m_IsRemoveAllAilments`：清除状态（可能用于 phase transitions）
- `m_EnableRetire`, `m_DisableGameOver`：（未测试）在 scripted battles 中强制启用/禁用 UI options 和 fail states（？）
- `m_LastBattleMapColor`：可能是 战斗 background tint 控制
- `m_TextureChangeEnemyIdx`：要更改 texture 的 敌人 槽位（`-1` 表示 unused）
- `m_ChangeEnemyTextureIdx`：要切换到的 texture 索引/槽位（`-1` unused）
- `m_TextureChangeTime`：texture swap 的 时机（可能是 delay 或 crossfade time，未测试）
- `m_EnemyPartsIndex`：受影响的 敌人 part 索引
- `m_EnemyPartsDisplay`：在事件中显示/隐藏引用的 敌人 part
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`：也存在于 `TacticalList` 中。战斗 engine 很可能根据这些 标志 推进脚本（即它们是用于追踪已发生事件的隐式“next”）。示例：Shadow of Twin Worlds 在 `TacticalList` 中使用它来追踪 Lucian 和 Lyblac break 时解锁 Shadow 弱点的 tactic 是否已经发生，以便 Lucian 和 Lyblac 从 break 中恢复后重新 锁定 弱点，因为似乎不存在专门针对该情况的 条件。
- `m_JoinTrigger`：可能与“unit joins 战斗 now”事件有关（例如 guest 在战斗中途加入）
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`：不适合通用 结构体 的特殊 指令 类型。`m_UniqueProcess` 可能取以下值：
```hpp
// Enum Kingship.EBATTLE_EVENT_COMMAND_UNIQUE_PROCESS
// NumValues: 0x0006
enum class EBATTLE_EVENT_COMMAND_UNIQUE_PROCESS : uint8
{
	NONE                                     = 0,
	RAID_PARTY_MEMEBER_CHANGE                = 1,
	SKILL_LEAN_TEMPORARY                     = 2,
	RAID_SPECIAL_SKILL_ONLY                  = 3,
	RAID_ALL_BUFF                            = 4,
	EBATTLE_EVENT_COMMAND_UNIQUE_MAX         = 5,
};
```

可能与该 asset 相关的 enum：

```hpp
// Enum Kingship.EBattleCommandStep
// NumValues: 0x0009
enum class EBattleCommandStep : uint8
{
	NO_COMMAND_CONTROL                       = 0,
	COMMAND_CHARA_SELECT                     = 1,
	BATTLE_COMMAND_SELECT                    = 2,
	TARGET_SELECT                            = 3,
	FINISH_COMMAND_DECIDE                    = 4,
	ALL_ACTION_COMMAND_SELECT                = 5,
	AILMENT_LIST_VIEW                        = 6,
	VIEW_MODE                                = 7,
	EBattleCommandStep_MAX                   = 8,
};
```
#### ■ 运行时读取语义
【主要 C 证据】

##### ◆ C-BCMD-01：BattleEventCommand 行 解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482904-17482934
FScriptContainerElement *__fastcall AQPBattleManager::GetEventCommandDB(
        AQPBattleManager *this,
        int CommandID,
        int Index)
{
  ADatabaseDefineStatic *v5; // rbx
  int ArrayIndex; // eax
  int v7; // ecx

  if ( CommandID <= 0
    || !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
  {
    return nullptr;
  }
  v5 = ADatabaseDefineStatic::m_Self;
  if ( !ADatabaseDefineStatic::m_Self || !ADatabaseDefineStatic::m_Self->m_BattleEventCommand.m_Loaded )
    return nullptr;
  ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                 (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_BattleEventCommand.m_DBAccess,
                 CommandID);
  v7 = ArrayIndex + Index;
  if ( ArrayIndex < 0 )
    v7 = ArrayIndex;
  if ( v7 >= 0 && v7 < v5->m_BattleEventCommand.m_DataList.ArrayNum )
    return &v5->m_BattleEventCommand.m_DataList.AllocatorInstance.Data[200 * v7];
  else
    return nullptr;
}

//----- (00000001445403F0) ----------------------------------------------------
```

##### ◆ C-BCMD-02：BattleEventCommand 基类构造函数：复原出的具名 指令 字段

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:16987004-16987044
//----- (0000000144325520) ----------------------------------------------------
FBattleEventCommandBase *__fastcall FBattleEventCommandBase::FBattleEventCommandBase(FBattleEventCommandBase *this)
{
  this->__vftable = (FBattleEventCommandBase_vtbl *)FBattleEventCommandBase::`vftable';
  *(_QWORD *)&this->m_id = 0;
  this->m_PerformerID = 0;
  *(_WORD *)&this->m_JoinTrigger = 0;
  this->m_PerformerEnemy = 0;
  this->m_NoName = 0;
  *(_QWORD *)&this->m_NameID = 0;
  this->m_NoTail = 0;
  *(_QWORD *)&this->m_BaloonOffsetX = 0;
  *(_QWORD *)&this->m_TextID = 0;
  *(_QWORD *)&this->m_DirID = 0;
  *(_QWORD *)&this->m_SoundHandle = 0;
  *(_QWORD *)&this->m_EffectHandle = 0;
  *(_QWORD *)&this->m_BgmID = 0;
  *(_QWORD *)&this->m_SkillID = 0;
  *(_WORD *)&this->m_IsRemoveAllAilments = 0;
  this->m_isWaitSkillTime = 0;
  this->m_PlayBgmBlock = 0;
  this->m_isRewriteIdleMotion = 0;
  *(_QWORD *)&this->m_OnEventFlgIndex = 0;
  *(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;
  this->m_TextureChangeTime = 0.0;
  *(_WORD *)&this->m_EnableTarget = 0;
  *(_QWORD *)&this->m_Fade = 0;
  this->m_EnemyPartsIndex = 0;
  this->m_EnemyPartsDisplay = 0;
  this->m_NarrationID = 0;
  *(_WORD *)&this->m_IsNextNarrationPage = 0;
  *(_QWORD *)&this->m_NarrationColorR = 0;
  *(_QWORD *)&this->m_NarrationColorB = 0;
  *(_QWORD *)&this->m_DisableGameOver = 0;
  *(_QWORD *)&this->m_UniqueProcess = 0;
  this->m_OptSkillID = 0;
  return this;
}

//----- (00000001443255D0) ----------------------------------------------------
UBattleIconBase *__fastcall UBattleIconBase::UBattleIconBase(
```

##### ◆ C-BCMD-03：BattleEventCommand EnableTarget/EnableAction 与 战斗-map-color 读取方

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17477565-17477751
__int64 __fastcall AQPBattleManager::BeginBattleEventEnemyStatus(
        AQPBattleManager *this,
        const FBattleEventCommandBase *Command,
        AQPBattleCharacterBase *Character)
{
  unsigned __int8 v3; // si
  AQPBattleCharacterBase **Data; // rbx
  char v8; // r14
  AQPBattleCharacterBase **i; // rsi
  AQPBattleCharacterBase *v10; // rdi
  AQPBattleCharacterBase **v11; // rbx
  AQPBattleCharacterBase **j; // r14
  AQPBattleCharacterBase *v13; // rdi
  AQPBattleCharacterBase **v14; // rbx
  AQPBattleCharacterBase **k; // r14
  AQPBattleCharacterBase *v16; // rdi

  v3 = 0;
  if ( Command && Character && (Character->ObjectFlags & 0x40000000) == 0 && AQPBattleCharacterBase::isEnemy(Character) )
  {
    if ( Command->m_EnableTarget )
    {
      Data = (AQPBattleCharacterBase **)this->m_BattleEnemies.AllocatorInstance.Data;
      v8 = 0;
      for ( i = &Data[this->m_BattleEnemies.ArrayNum]; Data != i; ++Data )
      {
        v10 = *Data;
        if ( *Data
          && (v10->ObjectFlags & 0x40000000) == 0
          && UCriFsLoaderComponent::GetStatus(*Data)
          && !AQPBattleCharacterBase::IsOutsideTarget(v10) )
        {
          v8 = 1;
        }
      }
      v3 = AQPBattleCharacterBase::DisableOutsideTarget(Character);
      if ( !v8 )
      {
        v11 = (AQPBattleCharacterBase **)this->m_BattleForwards.AllocatorInstance.Data;
        for ( j = &v11[this->m_BattleForwards.ArrayNum]; v11 != j; ++v11 )
        {
          v13 = *v11;
          if ( *v11 && (v13->ObjectFlags & 0x40000000) == 0 && UCriFsLoaderComponent::GetStatus(*v11) )
            AQPBattleManager::ResetTarget(this, v13);
        }
        v14 = (AQPBattleCharacterBase **)this->m_BattleBackups.AllocatorInstance.Data;
        for ( k = &v14[this->m_BattleBackups.ArrayNum]; v14 != k; ++v14 )
        {
          v16 = *v14;
          if ( *v14 && (v16->ObjectFlags & 0x40000000) == 0 && UCriFsLoaderComponent::GetStatus(*v14) )
            AQPBattleManager::ResetTarget(this, v16);
        }
      }
    }
    if ( Command->m_EnableAction )
      v3 |= AQPBattleCharacterBase::DisableNotAction(Character);
    if ( v3 )
      AQPBattleCharacterBase::RefreshStatusUI(Character, 1, 1);
  }
  return v3;
}

//----- (000000014453A490) ----------------------------------------------------
// local variable allocation has failed, the output may be wrong!
char __fastcall AQPBattleManager::BeginBattleEventLastBattleMapColor(
        AQPBattleManager *this,
        const FBattleEventCommandBase *Command)
{
  TMap<int,TArray<FLinearColor,TSizedDefaultAllocator<32> >,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<int,TArray<FLinearColor,TSizedDefaultAllocator<32> >,0> > *p_m_ModifyMaterialColor; // r15
  int NumBits; // r10d
  TBitArray<FDefaultBitArrayAllocator> *p_AllocationFlags; // rdi
  int v7; // ecx
  int v8; // r8d
  int *Data; // r9
  int v10; // edx
  unsigned __int64 v11; // rax
  int v12; // eax
  int v13; // r12d
  int v14; // r8d
  unsigned int v15; // r9d
  _DWORD *v16; // r11
  int v17; // edx
  unsigned __int64 v18; // rax
  __int64 v19; // rdx
  __int64 m_LastBattleMapColor; // rax
  __int128 v22; // [rsp+28h] [rbp-39h]
  _BYTE v23[48]; // [rsp+38h] [rbp-29h] OVERLAPPED BYREF
  long double v24; // [rsp+68h] [rbp+7h]
  _BYTE v25[40]; // [rsp+70h] [rbp+Fh]

  if ( !Command || !this->m_pModifyMaterialComponent || Command->m_LastBattleMapColor < 0 )
    return 0;
  p_m_ModifyMaterialColor = &this->m_ModifyMaterialColor;
  NumBits = this->m_ModifyMaterialColor.Pairs.Elements.AllocationFlags.NumBits;
  p_AllocationFlags = &this->m_ModifyMaterialColor.Pairs.Elements.AllocationFlags;
  v7 = 0;
  *(_QWORD *)&v22 = 0x100000000LL;
  v8 = 0;
  *((_QWORD *)&v22 + 1) = p_AllocationFlags;
  *(_DWORD *)v23 = -1;
  *(_QWORD *)&v23[4] = 0;
  if ( NumBits )
  {
    Data = (int *)p_AllocationFlags->AllocatorInstance.SecondaryData.Data;
    if ( !Data )
      Data = (int *)p_AllocationFlags;
    v10 = *Data;
    if ( *Data )
    {
LABEL_10:
      DWORD1(v22) = v10 & -v10;
      _BitScanReverse64(&v11, 2LL * DWORD1(v22) + 1);
      *(_DWORD *)&v23[4] = v8 + v11 - 1;
      if ( *(int *)&v23[4] <= NumBits )
        goto LABEL_12;
    }
    else
    {
      while ( 1 )
      {
        ++v7;
        v8 += 32;
        LODWORD(v22) = v7;
        *(_DWORD *)&v23[8] = v8;
        if ( v7 > (NumBits - 1) / 32 )
          break;
        *(_DWORD *)v23 = -1;
        v10 = Data[v7];
        if ( v10 )
          goto LABEL_10;
      }
    }
    *(_DWORD *)&v23[4] = NumBits;
  }
LABEL_12:
  v12 = p_m_ModifyMaterialColor->Pairs.Elements.AllocationFlags.NumBits;
  *(_QWORD *)v25 = p_m_ModifyMaterialColor;
  *(_OWORD *)&v25[24] = *(_OWORD *)v23;
  v13 = -1 << (v12 & 0x1F);
  *(_OWORD *)&v25[8] = v22;
  v14 = v12 >> 5;
  v15 = v12 & 0xFFFFFFE0;
  v24 = _mm_unpackhi_pd(*(__m128d *)&v25[24], *(__m128d *)&v25[24]).m128d_f64[0];
  *(_DWORD *)&v23[4] = v12;
  *(_OWORD *)&v23[16] = *(_OWORD *)v25;
  *(_OWORD *)&v23[32] = *(_OWORD *)&v25[16];
  if ( v12 == NumBits )
    goto LABEL_20;
  v16 = p_AllocationFlags->AllocatorInstance.SecondaryData.Data;
  if ( !v16 )
    v16 = p_AllocationFlags;
  v17 = v13 & v16[v14];
  if ( v17 )
  {
LABEL_18:
    _BitScanReverse64(&v18, 2LL * (v17 & (unsigned int)-v17) + 1);
    *(_DWORD *)&v23[4] = v15 + v18 - 1;
    if ( *(int *)&v23[4] <= NumBits )
      goto LABEL_20;
  }
  else
  {
    while ( 1 )
    {
      ++v14;
      v15 += 32;
      if ( v14 > (NumBits - 1) / 32 )
        break;
      v17 = v16[v14];
      if ( v17 )
        goto LABEL_18;
    }
  }
  *(_DWORD *)&v23[4] = NumBits;
LABEL_20:
  while ( *(_DWORD *)&v23[44] != *(_DWORD *)&v23[4]
       || *(TBitArray<FDefaultBitArrayAllocator> **)&v23[32] != p_AllocationFlags
       || *(TMap<int,TArray<FLinearColor,TSizedDefaultAllocator<32> >,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<int,TArray<FLinearColor,TSizedDefaultAllocator<32> >,0> > **)&v23[16] != p_m_ModifyMaterialColor )
  {
    v19 = **(_QWORD **)&v23[16] + 32LL * *(int *)&v23[44];
    m_LastBattleMapColor = Command->m_LastBattleMapColor;
    if ( (int)m_LastBattleMapColor >= 0 && (int)m_LastBattleMapColor < *(_DWORD *)(v19 + 16) )
      UBattleModifyMaterialComponent::ModifyMaterialParameterCollectionColor(
        this->m_pModifyMaterialComponent,
        *(_DWORD *)v19,
        (const FLinearColor *)(*(_QWORD *)(v19 + 8) + 16 * m_LastBattleMapColor),
        2.0);
```

##### ◆ C-BCMD-04：BattleEventCommand talk/narration 读取方

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17477760-17477838
char __fastcall AQPBattleManager::BeginBattleEventTalk(
        AQPBattleManager *this,
        const FBattleEventCommandBase *Command,
        AQPBattleCharacterBase *Character)
{
  signed int m_TextID; // edi
  int m_BalloonType; // ecx
  UBalloonBase *m_TalkBalloon; // rax
  char v8; // bl
  EObjectFlags *p_ObjectFlags; // rdi
  UBalloonManager *BalloonManager; // r9
  UTutorialMessage *v11; // rax
  float NarrationColorR; // xmm2_4
  bool IsChangeColorNarration; // al
  bool IsBackoutInvisible; // cl
  BOOL v16; // r9d

  if ( !Command || !Character || (Character->ObjectFlags & 0x40000000) != 0 )
    return 0;
  m_TextID = Command->m_TextID;
  if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )
    return 0;
  if ( !AQPBattleManager::SetupBattleEventTalk(AQPBattleManager::m_Self, Command, Character) )
    return AQPBattleManager::OpenActionInfo(m_TextID, 0, 1, nullptr);
  m_BalloonType = Command->m_BalloonType;
  if ( (unsigned int)(m_BalloonType - 7) > 1 && (unsigned int)(m_BalloonType - 12) > 1 )
  {
    m_TalkBalloon = this->m_TalkBalloon;
    if ( !m_TalkBalloon || (m_TalkBalloon->ObjectFlags & 0x40000000) != 0 )
    {
      v8 = 0;
      p_ObjectFlags = &AKSGameMode::s_Self->ObjectFlags;
      if ( !AKSGameMode::s_Self || (*p_ObjectFlags & 0x40000000) != 0 )
      {
        BalloonManager = nullptr;
        if ( !AKSGameMode::s_Self )
          return v8;
      }
      else
      {
        BalloonManager = AKSGameMode::GetBalloonManager();
      }
      if ( (*p_ObjectFlags & 0x40000000) != 0 )
        return v8;
      v11 = UBalloonManager::AddBalloon(BalloonManager, &this->m_BalloonParam);
      this->m_TalkBalloon = v11;
      if ( !v11 || (v11->ObjectFlags & 0x40000000) != 0 )
        return v8;
    }
    return 1;
  }
  NarrationColorR = Command->m_NarrationColorR;
  IsChangeColorNarration = 0;
  if ( NarrationColorR != 1.0 || 1.0 != Command->m_NarrationColorG || 1.0 != Command->m_NarrationColorB )
    IsChangeColorNarration = 1;
  IsBackoutInvisible = Command->m_BlackoutNarration;
  v16 = this->m_BalloonParam.Text.Type == NARRATION_NOFRAME;
  if ( !IsBackoutInvisible && !IsChangeColorNarration )
  {
    AQPBattleManager::OpenNarrationC(
      AQPBattleManager::m_Self,
      Command->m_NarrationID,
      Command->m_IsNextNarrationPage,
      v16);
    return 0;
  }
  AQPBattleManager::OpenNarrationEXC(
    AQPBattleManager::m_Self,
    Command->m_NarrationID,
    Command->m_IsNextNarrationPage,
    v16,
    IsBackoutInvisible,
    IsChangeColorNarration,
    NarrationColorR,
    Command->m_NarrationColorG,
    Command->m_NarrationColorB);
  return 0;
}

```

##### ◆ C-BCMD-05：BattleEventCommand 效果 与 效果位置 读取方

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17477465-17477520
//----- (000000014453A060) ----------------------------------------------------
_BOOL8 __fastcall AQPBattleManager::BeginBattleEventEffect(
        AQPBattleManager *this,
        const FBattleEventCommandBase *Command)
{
  bool v2; // r14
  __int64 m_EffectHandle; // r13
  int m_EffectID; // r15d
  int v7; // r12d
  AEffectManager *v8; // rbp
  AQPBattleCharacterBase *m_PlayerPerformer; // r9
  int tagMax; // eax
  int performerTagIndex; // [rsp+60h] [rbp-78h] BYREF
  UNiagaraComponent *pPSC; // [rsp+68h] [rbp-70h] BYREF
  UE::Math::TVector<double> Scale; // [rsp+70h] [rbp-68h] BYREF
  UE::Math::TVector<double> pos; // [rsp+88h] [rbp-50h] BYREF
  UNiagaraComponent *temp; // [rsp+E8h] [rbp+10h] BYREF
  bool flag; // [rsp+F0h] [rbp+18h] BYREF
  bool loop; // [rsp+F8h] [rbp+20h] BYREF

  v2 = 0;
  if ( Command )
  {
    m_EffectHandle = Command->m_EffectHandle;
    m_EffectID = Command->m_EffectID;
    v7 = m_EffectHandle - 1;
    v2 = m_EffectID > 0;
    if ( m_EffectID > 0 || (int)m_EffectHandle - 1 >= 0 )
    {
      if ( AEffectManager::m_Self )
      {
        if ( UObjectBase::IsValidLowLevelFast(AEffectManager::m_Self, 1) )
        {
          v8 = AEffectManager::m_Self;
          if ( AEffectManager::m_Self )
          {
            if ( (AEffectManager::m_Self->ObjectFlags & 0x40000000) == 0 )
            {
              if ( m_EffectID <= 0 )
              {
                if ( (int)m_EffectHandle - 1 >= 0 )
                {
                  pPSC = *(UNiagaraComponent **)&this->m_EventEffects.AllocatorInstance.Data[8 * v7];
                  AEffectManager::DestroyEffect(AEffectManager::m_Self, pPSC);
                  *(_QWORD *)&this->m_EventEffects.AllocatorInstance.Data[8 * v7] = 0;
                }
              }
              else if ( UEnemyDB::GetBattlePosition(&pos, Command->m_EffectPosition) )
              {
                if ( this->m_IsRaidBattle && Command->m_EffectPosition == 1 )
                {
                  m_PlayerPerformer = this->m_PlayerPerformer;
                  performerTagIndex = 0;
                  LOBYTE(temp) = 0;
                  if ( AQPBattleManager::GetTagIndex(this, &performerTagIndex, (bool *)&temp, m_PlayerPerformer) )
                  {
```

##### ◆ C-BCMD-06：BattleEventCommand 执行者/leader 解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482200-17482225
  int ArrayMax; // ecx
  __int64 v12; // rbx
  AQPBattleCharacterBase **v13; // rbx
  FScriptContainerElement *v14; // r15
  AQPBattleCharacterBase *v15; // rdi
  int UniqueID; // eax
  AQPBattleCharacterBase **v17; // rdi
  AQPBattleCharacterBase **v18; // r12
  AQPBattleCharacterBase *v19; // rbx
  AQPBattleCharacterBase **Data; // rdi
  AQPBattleCharacterBase **i; // rbp
  AQPBattleCharacterBase *v22; // rbx
  TArray<AQPBattleCharacterBase *,TSizedDefaultAllocator<32> > allChara; // [rsp+20h] [rbp-38h] BYREF
  int playerID; // [rsp+68h] [rbp+10h] BYREF

  v3 = 0;
  v4 = 0;
  *Character = nullptr;
  if ( Command->m_PerformerIsLeader )
  {
    if ( UPlayerDataUtility::GetParty1stPlayerID(&playerID, 1, -1) )
    {
      result = AQPBattleManager::GetPlayer(this, Character, playerID);
      v4 = result;
      if ( result )
        return result;
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_AnimID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;`（`m_AnimID` 的合并 anchor） | 在此 导出 中，`m_AnimID` 未作为单独的 `Command->m_AnimID` 读取方 输出。最好将其视为 构造函数 中与 `m_DirID` 成对的相邻 指令 槽位；在 BattleEventCommand 符号 下未复原出运行时 animation 读取方。 | 否定性/部分复原 | `C-BCMD-02` |
| `m_BalloonType` | `C-BCMD-04:17477766` `int m_BalloonType; // ecx`<br>`C-BCMD-04:17477784` `m_BalloonType = Command->m_BalloonType;`<br>`C-BCMD-04:17477785` `if ( (unsigned int)(m_BalloonType - 7) > 1 && (unsigned int)(m_BalloonType - 12) > 1 )` | `m_BalloonType` 在 `BeginBattleEventTalk` 中选择普通气泡路径或 narration 路径。 | 已复原 | `C-BCMD-04` |
| `m_BgmID` | `C-BCMD-02:16987020` `*(_QWORD *)&this->m_BgmID = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_BlackoutNarration` | `C-BCMD-04:17477815` `IsBackoutInvisible = Command->m_BlackoutNarration;` | `m_BlackoutNarration` 控制 narration 路径是否使用带 blackout/invisibility 行为的扩展 narration 调用。 | 已复原 | `C-BCMD-04` |
| `m_ChangeEnemyTextureIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;`（`m_ChangeEnemyTextureIdx` 的合并 anchor） | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_DirID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_DisableGameOver` | `C-BCMD-02:16987037` `*(_QWORD *)&this->m_DisableGameOver = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_EffectHandle` | `C-BCMD-05:17477471` `__int64 m_EffectHandle; // r13`<br>`C-BCMD-05:17477488` `m_EffectHandle = Command->m_EffectHandle;`<br>`C-BCMD-05:17477490` `v7 = m_EffectHandle - 1;` plus 2 further local occurrences | `m_EffectHandle` 在 效果 指令 路径中与 `m_EffectID` 一起读取。如果 `m_EffectID` 不存在，该 handle 仍可寻址现有 效果 槽位。 | 已复原 | `C-BCMD-05` |
| `m_EffectID` | `C-BCMD-05:17477472` `int m_EffectID; // r15d`<br>`C-BCMD-05:17477489` `m_EffectID = Command->m_EffectID;`<br>`C-BCMD-05:17477491` `v2 = m_EffectID > 0;` plus 2 further local occurrences | `m_EffectID` 与 `m_EffectHandle` 一起读取；正的 效果 ID 会在 事件 效果 路径中激活 效果 creation。 | 已复原 | `C-BCMD-05` |
| `m_EffectPosition` | `C-BCMD-05:17477512` `else if ( UEnemyDB::GetBattlePosition(&pos, Command->m_EffectPosition) )`<br>`C-BCMD-05:17477514` `if ( this->m_IsRaidBattle && Command->m_EffectPosition == 1 )` | `m_EffectPosition` 由 `BeginBattleEventEffect` 消费：正/非零位置会通过 `UEnemyDB::GetBattlePosition` 解析，raid battles 对 位置 `1` 有特殊处理。 | 已复原 | `C-BCMD-05` |
| `m_EnableAction` | `C-BCMD-03:17477619` `if ( Command->m_EnableAction )` | `m_EnableAction` 由 `BeginBattleEventEnemyStatus` 直接消费：若为 true，它会通过 `DisableNotAction` 清除目标角色的 no-动作 状态。 | 已复原 | `C-BCMD-03` |
| `m_EnableRetire` | 提供的 C 导出 中没有复原出准确的具名字段或明确的偏移槽位。 | 引用的 指令-行 代码中未复原出准确的 `m_EnableRetire` 符号。应将此键视为在该 导出 中未复原。 | 否定性/部分复原 | `C-BCMD-02` |
| `m_EnableTarget` | `C-BCMD-03:17477585` `if ( Command->m_EnableTarget )` | `m_EnableTarget` 由 `BeginBattleEventEnemyStatus` 直接消费：若为 true，该 指令 会清除目标角色的 outside-target 状态，并可能重置玩家 targets。 | 已复原 | `C-BCMD-03` |
| `m_EnemyPartsDisplay` | `C-BCMD-02:16987032` `this->m_EnemyPartsDisplay = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_EnemyPartsIndex` | `C-BCMD-02:16987031` `this->m_EnemyPartsIndex = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_FadeTime` | `C-BCMD-02:16987030` `*(_QWORD *)&this->m_Fade = 0;`（`m_FadeTime` 的合并 anchor） | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_IsNextNarrationPage` | `C-BCMD-04:17477822` `Command->m_IsNextNarrationPage,`<br>`C-BCMD-04:17477829` `Command->m_IsNextNarrationPage,` | `m_IsNextNarrationPage` 作为 page-continuation 标志 传给 narration-opening functions。 | 已复原 | `C-BCMD-04` |
| `m_IsRemoveAllAilments` | `C-BCMD-02:16987022` `*(_WORD *)&this->m_IsRemoveAllAilments = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_JoinTrigger` | `C-BCMD-02:16987010` `*(_WORD *)&this->m_JoinTrigger = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_LastBattleMapColor` | `C-BCMD-03:17477649` `__int64 m_LastBattleMapColor; // rax`<br>`C-BCMD-03:17477655` `if ( !Command \|\| !this->m_pModifyMaterialComponent \|\| Command->m_LastBattleMapColor < 0 )`<br>`C-BCMD-03:17477745` `m_LastBattleMapColor = Command->m_LastBattleMapColor;` plus 2 further local occurrences | `m_LastBattleMapColor` 由 `BeginBattleEventLastBattleMapColor` 消费；负值会禁用该路径，否则该值会在应用 material color 前索引 战斗-map color 表。 | 已复原 | `C-BCMD-03` |
| `m_NameID` | `C-BCMD-02:16987013` `*(_QWORD *)&this->m_NameID = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_NarrationID` | `C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )`<br>`C-BCMD-04:17477821` `Command->m_NarrationID,`<br>`C-BCMD-04:17477828` `Command->m_NarrationID,` | 当 指令 使用 narration balloon 类型 时，`m_NarrationID` 由 `OpenNarrationC`/`OpenNarrationEXC` 消费。 | 已复原 | `C-BCMD-04` |
| `m_NextWait` | 提供的 C 导出 中没有复原出准确的具名字段或明确的偏移槽位。 | 未复原出准确的 `m_NextWait` 符号。构造函数 暴露了合并的 `m_Fade`/时机 区域，但该 导出 并不能证明名为 `m_NextWait` 的独立字段。 | 否定性/部分复原 | `C-BCMD-02` |
| `m_NoName` | `C-BCMD-02:16987012` `this->m_NoName = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_NoTail` | `C-BCMD-02:16987014` `this->m_NoTail = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_OffEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;`（`m_OffEventFlgIndex` 的合并 anchor） | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_OnEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_OptCharacterID` | 提供的 C 导出 中没有复原出准确的具名字段或明确的偏移槽位。 | 未复原出准确的 `m_OptCharacterID` 符号；只有相邻的 unique-process option 区域和 `m_OptSkillID` 被命名。 | 否定性/部分复原 | `C-BCMD-02` |
| `m_OptSkillID` | `C-BCMD-02:16987039` `this->m_OptSkillID = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerEnemy` | `C-BCMD-02:16987011` `this->m_PerformerEnemy = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerID` | `C-BCMD-02:16987009` `this->m_PerformerID = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerIsLeader` | `C-BCMD-06:17482218` `if ( Command->m_PerformerIsLeader )` | `m_PerformerIsLeader` 由 `FindBattleEventCharacter` 消费；为 true 时，执行者 解析 会返回 party leader 路径，而不是普通 执行者-ID 路径。 | 已复原 | `C-BCMD-06` |
| `m_PlayBgmBlock` | `C-BCMD-02:16987024` `this->m_PlayBgmBlock = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundHandle` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundID` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;`（`m_SoundID` 的合并 anchor） | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_TargetID` | 提供的 C 导出 中没有复原出准确的具名字段或明确的偏移槽位。 | 未复原出准确的 `m_TargetID` 符号 或明确的 指令-行 读取方。targeting 由 事件 executor 处理，但该 导出 未保留该名称下的字段。 | 否定性/部分复原 | `C-BCMD-01` |
| `m_TextID` | `C-BCMD-04:17477765` `signed int m_TextID; // edi`<br>`C-BCMD-04:17477779` `m_TextID = Command->m_TextID;`<br>`C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )` plus 1 further local occurrences | `m_TextID` 由 talk/narration 路径读取；如果 `m_TextID` 和 `m_NarrationID` 均为非正值，该 指令 不会打开 文本。 | 已复原 | `C-BCMD-04` |
| `m_TextureChangeEnemyIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_TextureChangeTime` | `C-BCMD-02:16987028` `this->m_TextureChangeTime = 0.0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_UniqueProcess` | `C-BCMD-02:16987038` `*(_QWORD *)&this->m_UniqueProcess = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_isRewriteIdleMotion` | `C-BCMD-02:16987025` `this->m_isRewriteIdleMotion = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |
| `m_isSwitchActionSkip` | 提供的 C 导出 中没有复原出准确的具名字段或明确的偏移槽位。 | C 导出 中未复原出准确的 `m_isSwitchActionSkip` 符号。没有单独 读取方 时，不应把它描述为已证明的活字段。 | 否定性/部分复原 | `C-BCMD-02` |
| `m_isWaitSkillTime` | `C-BCMD-02:16987023` `this->m_isWaitSkillTime = 0;` | 指令 行被解析为 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`。此字段要么在 `FBattleEventCommandBase` 初始化中被命名，要么由 事件 executor 消费；只有在 读取方 确认时，Hex-Rays 合并的相邻字段才会被解释为成对槽位。 | 已复原 | `C-BCMD-01`, `C-BCMD-02` |

### ■ (*) `BattlePlaybackData` 与 `BattlePlayback`
本节需要更严格地区分三个名称相近的事物：

1. **`BattlePlaybacks`** 是已复原的运行时数据库表。其行类型为 `FBattlePlaybacksBase`，运行时通过 `ADatabaseDefineStatic::m_BattlePlaybacks.m_DataList` 读取它。
2. **`FPlaybackData`** 不是 DataBase 行。它是 `ABattlePlayback` 在 save-数据 层与 `BattlePlaybacks` 表 join 之后使用的瞬时运行时对象。
3. **`BattlePlaybackData`**，也就是某些 资源导出 中带有 `m_EnemySkillID_01` 等字段的 转储 记录，并未在 C 导出 中复原为活的 `ADatabaseDefineStatic` DB 接口。除非找到单独 读取方，否则不要把这些奇怪字段等同于活的 `BattlePlaybacks` 表。

实际结果是：对于 战斗 播放，有意义的改造表是 `BattlePlaybacks`：它把 播放 ID 映射到一个 敌人 技能、一个可选 敌人 执行者 槽位，以及一个 效果可见性 标志。party composition、敌人群组、raid 标志、guest ID 和 main-character appearance 由 save/调试 播放 数据 提供；随后 技能/动作 payload 从 `BattlePlaybacks` 获取。

#### ■ `BattlePlaybacks`
活行具有如下已复原结构：

| 字段 | C 行偏移 | 运行时角色 | 证据等级 |
|---|---:|---|---|
| `m_id` | `+0x08` | 与 `FSaveDataBattlePlayback::Id` 比较的 键。 | direct 行 扫描 |
| `m_VisibleAttackEffect` | `+0x0C` | 字节 标志，复制到 `FPlaybackData::IsVisibleSkillEffect`；之后传给 `AQPActionCommand::ProceedEffectiveOnlyLooks`。 | 直接玩法读取方 |
| `m_EnemySkillID` | `+0x10` | Skill ID，复制到 `FPlaybackData::EnemyUseSkillID`；之后传给 `AQPActionCommand::SetupActionCommand`。 | 直接玩法读取方 |
| `m_SkillPlayEnemyIndex` | `+0x14` | 可选 敌人 槽位 覆盖，复制到 `FPlaybackData::SkillUseEnemyIndex`；`-1` 表示没有行指定的固定 执行者。 | 直接玩法读取方 |

结构体 大小为 24 字节。SDK enum `EBATTLE_PLAYBACKS` 命名 stock 播放 IDs，但下方展示的主要运行时路径会对 DB 数组 执行线性扫描并比较行 ID。该路径不是 enum-domain switch。例外是 调试 回退 路径：当不存在已创建的 save 播放 时，它对少数 ID 含有硬编码 cases。

#### ■ `BattlePlaybackData`
带有如下值的 转储 `BattlePlaybackData` records：

```json
"m_EnemySkillID_01": "m_EnemySkillID_01"
```

应继续归类为未解析 template/结构定义 数据。C 导出 注册了 `BattlePlaybacksBase`、`BattlePlaybacks` 和瞬时 `PlaybackData`；它并未暴露出一个与 `m_EnemySkillID_01` / `m_EnemySkillID_02` 对应的活 `ADatabaseDefineStatic::GetBattlePlaybackData` 表。符号中还存在一个拼写错误的独立 end-credit 结构体，名为 `FEndCreditBattlePlayackData`，但那是 end-credit manager 的数据结构，而不是这里所述的 战斗 播放 DB。

#### ■ 运行时读取语义：已编译结构体 与 表接口

编译后的注册数据将 `BattlePlaybacksBase` 与 `BattlePlaybacks` 标识为 `BattlePlaybacks` 的 DB structs。另一个方面，`ABattlePlayback` 拥有一个瞬时 `FPlaybackData` 结构体。这个区别很重要，因为 表 行 与运行时 动作 对象 不是同一个对象。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:1346084-1346104
const FEnumRegisterCompiledInInfo Z_CompiledInDeferFile_FID_project_Source_Kingship_Battle_Define_BattlePlaybacks_h_Statics::EnumInfo[1] =
{
  {
    &EBATTLE_PLAYBACKS_StaticEnum,
    L"EBATTLE_PLAYBACKS",
    &Z_Registration_Info_UEnum_EBATTLE_PLAYBACKS,
  }
}; // idb
const FStructRegisterCompiledInInfo Z_CompiledInDeferFile_FID_project_Source_Kingship_Battle_Define_BattlePlaybacks_h_Statics::ScriptStructInfo[2] =
{
  {
    &FBattlePlaybacksBase::StaticStruct,
    &Z_Construct_UScriptStruct_FBattlePlaybacksBase_Statics::NewStructOps,
    L"BattlePlaybacksBase",
    &Z_Registration_Info_UScriptStruct_BattlePlaybacksBase,
  },
  {
    &FBattlePlaybacks::StaticStruct,
    &Z_Construct_UScriptStruct_FBattlePlaybacks_Statics::NewStructOps,
    L"BattlePlaybacks",
    &Z_Registration_Info_UScriptStruct_BattlePlaybacks,
  }
}; // idb
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:1346184-1346204
const FStructRegisterCompiledInInfo Z_CompiledInDeferFile_FID_project_Source_Kingship_Battle_BattlePlayback_BattlePlayback_h_Statics::ScriptStructInfo[2] =
{
  {
    &FPlaybackData::StaticStruct,
    &Z_Construct_UScriptStruct_FPlaybackData_Statics::NewStructOps,
    L"PlaybackData",
    &Z_Registration_Info_UScriptStruct_PlaybackData,
  },
  {
    &FCharaCreateChunk::StaticStruct,
    &Z_Construct_UScriptStruct_FCharaCreateChunk_Statics::NewStructOps,
    L"CharaCreateChunk",
    &Z_Registration_Info_UScriptStruct_CharaCreateChunk,
  }
}; // idb
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:1379457-1379464
const FStructRegisterCompiledInInfo Z_CompiledInDeferFile_FID_project_Source_Kingship_UI_Ending_EndCreditBattlePlaybackData_h_Statics::ScriptStructInfo[1] =
{
  {
    &FEndCreditBattlePlayackData::StaticStruct,
    &Z_Construct_UScriptStruct_FEndCreditBattlePlayackData_Statics::NewStructOps,
    L"EndCreditBattlePlayackData",
    &Z_Registration_Info_UScriptStruct_EndCreditBattlePlayackData,
  }
}; // idb
```

解释：活的 战斗 播放 DB 接口 是 `BattlePlaybacks`，不是多列 `BattlePlaybackData` 表。end-credit 结构体 是独立的 UI/ending subsystem artifact；它的存在并不能验证奇怪的 转储 `m_EnemySkillID_01` 表是 战斗-运行时 input。

#### ■ 运行时读取语义：`FBattlePlaybacksBase` 行布局

`FBattlePlaybacksBase` 作为紧凑的 24 字节行被构造和复制。复制 routine 证明了活 payload 槽位：`+0x08` 处的行 ID、`+0x0C` 处的 visibility 字节、`+0x10` 处的 敌人 技能 ID，以及 `+0x14` 处的 执行者 索引。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:16988528-16988576
void __fastcall UScriptStruct::TCppStructOps<FBattlePlaybacksBase>::Construct(
        UScriptStruct::TCppStructOps<FBattlePlaybacksBase> *this,
        char *Dest)
{
  *(_WORD *)(Dest + 13) = 0;
  Dest[15] = 0;
  *(_QWORD *)Dest = FSimpleStatusData::`vftable';
  *((_DWORD *)Dest + 2) = 0;
  Dest[12] = 0;
  *((_QWORD *)Dest + 2) = 0;
}

char __fastcall UScriptStruct::TCppStructOps<FBattlePlaybacksBase>::Copy(
        UScriptStruct::TCppStructOps<FBattlePlaybacksBase> *this,
        char *Dest,
        _BYTE *Src,
        int ArrayDim)
{
  signed __int64 v4; // r10
  int v5; // eax
  char *v6; // rcx
  char *v7; // r8

  if ( ArrayDim )
  {
    v4 = Src - Dest;
    do
    {
      v5 = *(_DWORD *)&Dest[v4 + 8];
      v6 = &Dest[v4];
      v7 = Dest;
      Dest += 24;
      *((_DWORD *)v7 + 2) = v5;
      v7[12] = v6[12];
      *((_DWORD *)v7 + 4) = *((_DWORD *)v6 + 4);
      *((_DWORD *)v7 + 5) = *((_DWORD *)v6 + 5);
      --ArrayDim;
    }
    while ( ArrayDim );
  }
  return 1;
}
```

解释：`m_VisibleAttackEffect` 是 字节 槽位，而不是 整数 ID。`m_EnemySkillID` 与 `m_SkillPlayEnemyIndex` 是 32-bit 槽位。此行布局中没有任何内容对应 `m_EnemySkillID_01` 或 `m_EnemySkillID_02`。

#### ■ 运行时读取语义：静态 DB 访问 与 加载状态 检查

静态 DB class 暴露完整 `BattlePlaybacks` 包装器 与 数据列表 数组。战斗 播放 actor 自身使用 `GetBattlePlaybacksDB`，而该函数在 `m_BattlePlaybacks.m_Loaded` 为 true 之前拒绝返回 数据 指针。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17061737-17061767
void __fastcall ADatabaseDefineStatic::execGetBattlePlaybacks(UObject *Context, FFrame *Stack, char *Z_Param__Result)
{
  Stack->Code += Stack->Code != nullptr;
  TSet<TTuple<unsigned int,float>,TDefaultMapHashableKeyFuncs<unsigned int,float,0>,FDefaultSetAllocator>::operator=(
    (TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)(Z_Param__Result + 8),
    (const TSet<TTuple<unsigned int,int>,TDefaultMapHashableKeyFuncs<unsigned int,int,0>,FDefaultSetAllocator> *)&Context[369].ClassPrivate);
  TSet<TTuple<FName,bool>,TDefaultMapHashableKeyFuncs<FName,bool,0>,FDefaultSetAllocator>::operator=(
    (TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)(Z_Param__Result + 88),
    (const TMap<FName,FInterchangePipelinePropertyStates,FDefaultSetAllocator,TDefaultMapHashableKeyFuncs<FName,FInterchangePipelinePropertyStates,0> > *)&Context[371].ClassPrivate);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 168),
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&Context[373].ClassPrivate);
  *((_DWORD *)Z_Param__Result + 46) = Context[373].OuterPrivate.ObjectPtr.ObjectPtr.Handle;
  *((_QWORD *)Z_Param__Result + 24) = Context[374].__vftable;
  TArray<FBattlePlaybacksBase,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FBattlePlaybacksBase,TSizedDefaultAllocator<32> > *)(Z_Param__Result + 200),
    (const TArray<FBattlePlaybacksBase,TSizedDefaultAllocator<32> > *)&Context[374].ObjectFlags);
  Z_Param__Result[216] = Context[374].NamePrivate.ComparisonIndex.Value;
}
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17394369-17394386
TArray<FBattlePlaybacksBase,TSizedDefaultAllocator<32> > *__fastcall ABattlePlayback::GetBattlePlaybacksDB()
{
  ADatabaseDefineStatic *v0; // rbx

  v0 = nullptr;
  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_BattlePlaybacks.m_Loaded )
  {
    return nullptr;
  }
  if ( ADatabaseDefineStatic::m_Self )
  {
    if ( UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
      v0 = ADatabaseDefineStatic::m_Self;
  }
  return &v0->m_BattlePlaybacks.m_DataList;
}
```

解释：该表是普通的已加载 DB asset，具有 `FBattlePlaybacksBase` 的 数据列表 数组。扩展受到 DB/索引 机制和使用 播放 IDs 的 callers 约束，而不是受到 `EBATTLE_PLAYBACKS` enum 定义本身约束。如果 播放 save/调试 caller 提供了某个新行 ID 且表已加载，则此路径可以扫描到该新行 ID。

#### ■ 运行时读取语义：已保存播放数据 与 表行关联

`ABattlePlayback::CreatePlaybackData` 通过 join 两个来源构造 `FPlaybackData` 对象。它从 `FSaveDataBattlePlayback` 复制 party/群组/appearance 值，但 动作 payload 则从匹配的 `BattlePlaybacks` 行取得。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17393505-17393580
  FSaveDataBattlePlayback::FSaveDataBattlePlayback(&result->SaveData);
  result->SkillUseEnemyIndex = -1;
  result->EnemyUseSkillID = 0;
  result->IsVisibleSkillEffect = 0;
  result->BossIndex = -1;
  Id = savePlayback->Id;
  BattlePlaybacksDB = ABattlePlayback::GetBattlePlaybacksDB();
  if ( BattlePlaybacksDB )
  {
    Data = BattlePlaybacksDB->AllocatorInstance.Data;
    v7 = (__int64)&BattlePlaybacksDB->AllocatorInstance.Data[24 * BattlePlaybacksDB->ArrayNum];
    if ( Data != (FScriptContainerElement *)v7 )
    {
      while ( Id != *(_DWORD *)&Data[8] )
      {
        Data += 24;
        if ( Data == (FScriptContainerElement *)v7 )
          goto LABEL_17;
      }
      if ( Data )
      {
        v8 = *(_OWORD *)&savePlayback->MainCharacterPartsInfo.JobId;
        ArrayNum = savePlayback->BackupPlayerIDs.ArrayNum;
        GuestUniqueID = savePlayback->GuestUniqueID;
        v11 = Data[12];
        EnemyGroupID = savePlayback->EnemyGroupID;
        v13 = *(_DWORD *)&Data[16];
        v14 = *(_DWORD *)&Data[20];
        /* save arrays copied here */
        result->SaveData.EnemyGroupID = EnemyGroupID;
        result->EnemyUseSkillID = v13;
        result->SkillUseEnemyIndex = v14;
        result->IsVisibleSkillEffect = (bool)v11;
        result->SaveData.GuestUniqueID = playbackData;
```

解释：dataflow 是 `FSaveDataBattlePlayback::Id -> BattlePlaybacks.m_id -> FPlaybackData.{EnemyUseSkillID, SkillUseEnemyIndex, IsVisibleSkillEffect}`。因此，`m_EnemySkillID` 并不存储在 save 播放 记录 中；它是通过用 save 播放 ID 作为 DB 键 访问 `BattlePlaybacks` 而恢复出的。

#### ■ 运行时读取语义：请求/预加载阶段

播放 datum 构造完后，`RequestBattlePlayback` 会将其复制进 actor，配置 raid-vs-normal environment visibility，把 敌人群组 写入 `AQPBattleManager`，为 `(EnemyGroupID, EnemyUseSkillID)` 预加载 效果，请求该 群组 的 敌人 flipbooks，然后构建 main-character texture。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17398808-17398865
  ABattlePlayback::ClearPlaybackData(this);
  this->m_PlaybackData.SaveData.Id = playbackData->SaveData.Id;
  this->m_PlaybackData.SaveData.IsCreated = playbackData->SaveData.IsCreated;
  this->m_PlaybackData.SaveData.IsRaidBattle = playbackData->SaveData.IsRaidBattle;
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_PlaybackData.SaveData.ForwardPlayerIDs,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&playbackData->SaveData.ForwardPlayerIDs);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_PlaybackData.SaveData.BackupPlayerIDs,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&playbackData->SaveData.BackupPlayerIDs);
  this->m_PlaybackData.SaveData.GuestUniqueID = playbackData->SaveData.GuestUniqueID;
  this->m_PlaybackData.SaveData.EnemyGroupID = playbackData->SaveData.EnemyGroupID;
  this->m_PlaybackData.SaveData.MainCharacterPartsInfo = playbackData->SaveData.MainCharacterPartsInfo;
  this->m_PlaybackData.EnemyUseSkillID = playbackData->EnemyUseSkillID;
  this->m_PlaybackData.SkillUseEnemyIndex = playbackData->SkillUseEnemyIndex;
  this->m_PlaybackData.IsVisibleSkillEffect = playbackData->IsVisibleSkillEffect;
  this->m_PlaybackData.BossIndex = playbackData->BossIndex;
  /* environment selection omitted */
  AQPBattleManager::m_Self->m_IsRaidBattle = this->m_PlaybackData.SaveData.IsRaidBattle;
  v7->m_EnemyGroupID = this->m_PlaybackData.SaveData.EnemyGroupID;
  AQPBattleManager::CreateBattleCamera(v8);
  /* camera neutralization omitted */
  AQPBattleManager::BattlePlaybackPreloadEffectAsync(
    v7,
    this->m_PlaybackData.SaveData.EnemyGroupID,
    this->m_PlaybackData.EnemyUseSkillID);
```

解释：`m_EnemySkillID` 在 动作 执行前就参与 resource preparation。因此，如果在所选 敌人群组 context 中该 技能 ID 无法支持所需 效果/animation preloading，那么即使 指令 还未运行，播放 行也可能在语义上失败。

#### ■ 运行时读取语义：执行者选择 与 技能校验

`PlayAction` 首先在 `m_SkillPlayEnemyIndex` 是有效 敌人-数组 索引 时尊重该值。如果行未指定有效 槽位，函数会搜索所选 敌人群组，寻找 战术 技能 列表 包含 `m_EnemySkillID` 的敌人。若当前 boss 可以使用该 技能，则优先使用当前 boss；否则第一个匹配 敌人 ID 会被转换回 群组 槽位 并用作 执行者。只有在这之后，代码才会发出 `AQPActionCommand::SetupActionCommand`。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17398333-17398375
  EnemyUseSkillID = this->m_PlaybackData.EnemyUseSkillID;
  SkillUseEnemyIndex = this->m_PlaybackData.SkillUseEnemyIndex;
  v33 = EnemyUseSkillID;
  /* current boss discovery omitted */
  v14 = nullptr;
  if ( EnemyUseSkillID <= 0 )
    goto LABEL_57;
  if ( (int)SkillUseEnemyIndex >= 0 && (int)SkillUseEnemyIndex < this->m_Enemies.ArrayNum )
  {
    v14 = *(AQPBattleCharacterBase **)&this->m_Enemies.AllocatorInstance.Data[8 * SkillUseEnemyIndex];
    if ( v14 )
      goto LABEL_56;
  }
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17398380-17398418
  v16 = this->m_PlaybackData.SaveData.EnemyGroupID;
  Data = nullptr;
  v18 = 0;
  enemyIDs.AllocatorInstance.Data = nullptr;
  *(_QWORD *)&enemyIDs.ArrayNum = 0;
  if ( v16 > 0 )
  {
    v19 = UEnemyDB::GetEnemyIndices(v16);
    if ( v19 )
    {
      v20 = *(unsigned int **)v19;
      v21 = *(_QWORD *)v19 + 4LL * *(int *)&v19[8];
      if ( *(_QWORD *)v19 != v21 )
      {
        do
        {
          v22 = *v20;
          if ( UEnemyDB::HasTacticalSkill(*v20, EnemyUseSkillID) )
          {
            ArrayNum = enemyIDs.ArrayNum;
            if ( ++enemyIDs.ArrayNum > (unsigned int)enemyIDs.ArrayMax )
              TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&enemyIDs,
                ArrayNum);
            *(_DWORD *)&enemyIDs.AllocatorInstance.Data[4 * ArrayNum] = v22;
            EnemyUseSkillID = v33;
          }
```

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17398465-17398477
  if ( Data )
    FMemory::Free(Data);
  if ( v14 || (v14 = (AQPBattleCharacterBase *)v13) != nullptr )
  {
    EnemyUseSkillID = v33;
LABEL_56:
    AQPActionCommand::SetupActionCommand(this->m_EnemyActionCommand, v14, EnemyUseSkillID, 0, nullptr, 0, 0);
    this->m_IsActionStarted = 1;
  }
```

解释：`m_SkillPlayEnemyIndex` 比 战术-技能 成员关系 search 更强，因为它可以直接选择具体 敌人 对象。没有有效显式 索引 时，`m_EnemySkillID` 不仅被视为要 preview 的 动作，也被视为选择可信 敌人 执行者 的约束。这意味着 播放 行 通常应使用 `m_EnemyGroupID` 中至少一个敌人能使用的 技能，除非已知 槽位 覆盖 有效且 视觉 指令 能容忍 mismatch。

#### ■ 运行时读取语义：效果可见性 标志

visibility 标志 不只是被复制。在 播放 tick 期间，它会传入 动作-指令 仅视觉 progression function。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17401280-17401317
void __fastcall ABattlePlayback::Tick(ABattlePlayback *this, float DeltaTime)
{
  /* initialization and resource-loading state omitted */
  if ( this->m_IsActionStarted )
  {
    m_EnemyActionCommand = this->m_EnemyActionCommand;
    if ( m_EnemyActionCommand )
      this->m_IsActionStarted = (unsigned __int8)AQPActionCommand::ProceedEffectiveOnlyLooks(
                                                   m_EnemyActionCommand,
                                                   DeltaTime,
                                                   this->m_PlaybackData.IsVisibleSkillEffect);
  }
  /* character playback progression omitted */
}
```

解释：该 标志 控制 动作 指令 推进时的 视觉/效果 表现。因此，源名称 `m_VisibleAttackEffect` 在方向上是正确的，但 C 读取方 将运行时字段命名为 `IsVisibleSkillEffect`；应把它描述为 动作效果 visibility switch，而不是 时机 或 技能选择 参数。

#### ■ 运行时读取语义：调试 UI 与 临时对象布局

调试 widget 使用 `FPlaybackData` records 的 map。其 extraction 路径 确认了 transient 对象 offsets：`EnemyUseSkillID` 位于 `+0x58`，`SkillUseEnemyIndex` 位于 `+0x5C`，`IsVisibleSkillEffect` 位于 `+0x60`，`BossIndex` 位于 `+0x64`。

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17628635-17628704
FPlaybackData *__fastcall UBattlePlaybackDebugWidget::GetPlaybackData(
        UBattlePlaybackDebugWidget *this,
        FPlaybackData *result)
{
  FPlaybackData::FPlaybackData(result);
  FSaveDataBattlePlayback::ClearData(&result->SaveData);
  result->SkillUseEnemyIndex = -1;
  result->BossIndex = -1;
  result->EnemyUseSkillID = 0;
  result->IsVisibleSkillEffect = 0;
  /* map lookup omitted */
  if ( v12 )
  {
    result->SaveData.Id = *(_DWORD *)v12;
    result->SaveData.IsCreated = *(_BYTE *)(v12 + 4);
    result->SaveData.IsRaidBattle = *(_BYTE *)(v12 + 5);
    /* save-data arrays and appearance copied from +0x08..+0x50 */
    result->EnemyUseSkillID = *(_DWORD *)(v12 + 88);
    result->SkillUseEnemyIndex = *(_DWORD *)(v12 + 92);
    result->IsVisibleSkillEffect = *(_BYTE *)(v12 + 96);
    result->BossIndex = *(_DWORD *)(v12 + 100);
  }
  return result;
}
```

解释：`FPlaybackData` 为 104 字节，包含 save 播放 状态，后接 动作 preview 字段。该 transient structure 有助于理解执行过程，但不应把它误认为数据库表行。

#### ■ 字段级解释

| 键 | 运行时 C 位置 | 准确解释 | 状态 |
|---|---|---|---|
| `m_EnemySkillID` | `FBattlePlaybacksBase +0x10`；由 `CreatePlaybackData` 复制；由 `BattlePlaybackPreloadEffectAsync`、`UEnemyDB::HasTacticalSkill`、`AQPActionCommand::SetupActionCommand` 消费。 | 要在 播放 中以视觉形式执行的 技能 ID。它是 `SkillID` 命名空间中的 ID，但 动作 执行者 解析 仍取决于所选 敌人群组 与可选 槽位 覆盖。 | 已复原，直接读取方 |
| `m_SkillPlayEnemyIndex` | `FBattlePlaybacksBase +0x14`；复制到 `FPlaybackData::SkillUseEnemyIndex`；与 `m_Enemies.ArrayNum` 比较检查。 | 可选 敌人 槽位 覆盖。小于零或超出 活 敌人 数组 的值会 回退 到按 战术-技能 成员关系 发现 执行者。 | 已复原，直接读取方 |
| `m_VisibleAttackEffect` | `FBattlePlaybacksBase +0x0C`；复制到 `FPlaybackData::IsVisibleSkillEffect`；传给 `ProceedEffectiveOnlyLooks`。 | 布尔 风格的 动作效果 visibility switch。运行时的 C 名称为 `IsVisibleSkillEffect`，因此应描述为控制 仅视觉 动作 播放 期间的 技能/效果 表现。 | 已复原，直接读取方 |
| `m_EnemySkillID_01` | 未复原出 `BattlePlaybacksBase` 槽位；未复原出 `ADatabaseDefineStatic::GetBattlePlaybackData` 接口。 | 未证明为活的 战斗 播放 运行时 数据。应视为未解析 转储 结构定义/template material。 | 否定性 / unresolved |
| `m_EnemySkillID_02` | 与 `m_EnemySkillID_01` 相同。 | 未证明为活的 战斗 播放 运行时 数据。 | 否定性 / unresolved |

#### ■ 编写含义

- 有效的 `BattlePlaybacks` 行应将 `m_EnemySkillID` 与该行意图使用的 `m_EnemyGroupID` context 配对，因为运行时会在该 群组 中搜索 战术 技能 set 包含所请求 技能 的敌人。
- 当 视觉 执行者 必须确定时，使用 `m_SkillPlayEnemyIndex`。否则，运行时可能会选择匹配的当前 boss，或选择第一个其 `EnemyID` 拥有该 技能 的 群组 敌人。
- `m_VisibleAttackEffect = false` 不会抑制 技能 选择。它影响的是 动作 指令 已创建之后的 视觉 progression 路径。
- `EBATTLE_PLAYBACKS` enum 最好视为 vanilla 行 的 stock-名称 文档。可复原的 DB 路径会按行 ID 扫描 loaded 数组，因此本质上不受 enum 值 限制，尽管 调试 回退 cases 对少数 stock IDs 做了硬编码。



### ■ (*) BattleResultBonus
追踪不同 战斗 result bonuses（“No Damage”“Overkill”“Break”“1-Turn Kill”等）的倍率与本地化 ID。`m_Param` 似乎追踪哪些 stats（XP、JP 或金钱）获得 bonus：
- 0：Money 
- 1：JP
- 2：XP
未测试 2 之外是否存在未使用 参数。

#### ■ 运行时读取语义
`BattleResultBonus` 行按 bonus 类型 选择，而不是按 `m_Param` 选择。已复原的 gameplay 谓词 在 `GetBattleResultBonusParam` 定位行之后，只读取该行偏移 `+0x10` 处的 阈值 槽位。


【主要 C 证据】

##### ◆ C-BRESULT-01：BattleResultBonus 行查找 by bonus 类型

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17542770-17542803
FScriptContainerElement *__fastcall AQPBattleManager::GetBattleResultBonusParam(unsigned __int8 bonusType)
{
  int v1; // ebx
  TArray<FBattleResultBonusBase,TSizedDefaultAllocator<32> > *p_m_DataList; // rax
  FScriptContainerElement *Data; // rdx
  FScriptContainerElement *v4; // r8

  v1 = bonusType;
  if ( !ADatabaseDefineStatic::m_Self )
    return nullptr;
  if ( !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    return nullptr;
  if ( !ADatabaseDefineStatic::m_Self )
    return nullptr;
  if ( !ADatabaseDefineStatic::m_Self->m_BattleResultBonus.m_Loaded )
    return nullptr;
  p_m_DataList = &ADatabaseDefineStatic::m_Self->m_BattleResultBonus.m_DataList;
  if ( ADatabaseDefineStatic::m_Self == (ADatabaseDefineStatic *)-14520LL )
    return nullptr;
  Data = p_m_DataList->AllocatorInstance.Data;
  v4 = &ADatabaseDefineStatic::m_Self->m_BattleResultBonus.m_DataList.AllocatorInstance.Data[24
                                                                                           * ADatabaseDefineStatic::m_Self->m_BattleResultBonus.m_DataList.ArrayNum];
  if ( p_m_DataList->AllocatorInstance.Data == v4 )
    return nullptr;
  while ( *(_DWORD *)&Data[8] != v1 )
  {
    Data += 24;
    if ( Data == v4 )
      return nullptr;
  }
  return Data;
}

//----- (00000001445868B0) ----------------------------------------------------
```

##### ◆ C-BRESULT-02：bonus 谓词 read 行偏移 +0x10

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17549610-17549631
  FScriptContainerElement *BattleResultBonusParam; // rax

  v2 = bonusType;
  BattleResultBonusParam = AQPBattleManager::GetBattleResultBonusParam(bonusType);
  if ( BattleResultBonusParam )
  {
    switch ( v2 )
    {
      case 1:
        return this->m_BonusParamCounter.DamagedAmount <= *(_DWORD *)&BattleResultBonusParam[16];
      case 2:
        if ( (float)*(int *)&BattleResultBonusParam[16] <= this->m_BonusParamCounter.OverDamageRatio )
          return 1;
        break;
      case 3:
        return this->m_BonusParamCounter.BreakCount >= *(_DWORD *)&BattleResultBonusParam[16];
      default:
        if ( v2 == 4 && this->m_BonusParamCounter.TurnCount <= *(_DWORD *)&BattleResultBonusParam[16] )
          return 1;
        break;
    }
  }
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_Param` | `C-BRESULT-01:17542787-17542801` 扫描 `m_BattleResultBonus.m_DataList`，按偏移 `+0x08` 处的行 ID 匹配。<br>`C-BRESULT-02:17549619-17549627` 在已复原 谓词 阈值 中只读取 `BattleResultBonusParam[16]`。 | 在已复原的运行时路径中，准确的 `m_Param` 字段不是 查找 键；行 是通过 `bonusType` 与行偏移 `+0x08` 比较来选择的。随后 `IsBattleResultBonus` 会把 战斗 counter 与行偏移 `+0x10` 比较。C 导出 没有为一个独立的 `m_Param` 槽位识别出 资源专属 读取方，因此 Money/JP/XP 解释仍是 编写/结构定义假设，而不是已证明的 谓词 behavior。 | 否定性/部分复原，并有活表证据 | `C-BRESULT-01`, `C-BRESULT-02` |



#### ■ 深入运行时分析：`BattleResultBonus` 条件 阈值

如果只把 `m_Param` 视为 奖励通道 选择器，则现有 结构定义 级描述并不完整。至少有一个直接运行时 读取方 会把行值 `+0x10` 读作判断 result bonus 条件是否满足的 阈值。

##### ◆ C-BATTLERESULTBONUS-LOOKUP-01：result-bonus 行解析器 与 24-字节 stride

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17542770-17542802
FScriptContainerElement *__fastcall AQPBattleManager::GetBattleResultBonusParam(unsigned __int8 bonusType)
{
  FScriptContainerElement *Data; // rbx
  FScriptContainerElement *v3; // rdi

  if ( !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    || !ADatabaseDefineStatic::m_Self
    || !ADatabaseDefineStatic::m_Self->m_BattleResultBonus.m_Loaded )
  {
    return nullptr;
  }
  Data = ADatabaseDefineStatic::m_Self->m_BattleResultBonus.m_DataList.AllocatorInstance.Data;
  v3 = &Data[24 * ADatabaseDefineStatic::m_Self->m_BattleResultBonus.m_DataList.ArrayNum];
  while ( Data != v3 )
  {
    if ( *(_DWORD *)&Data[8] == bonusType )
      return Data;
    Data += 24;
  }
  return nullptr;
}
```

##### ◆ C-BATTLERESULTBONUS-THRESHOLD-02：行阈值 由 `IsBattleResultBonus` 使用

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17549607-17549634
bool __fastcall AQPBattleManager::IsBattleResultBonus(AQPBattleManager *this, unsigned __int8 bonusType)
{
  FScriptContainerElement *BattleResultBonusParam; // rax
  bool result; // al

  BattleResultBonusParam = AQPBattleManager::GetBattleResultBonusParam(bonusType);
  result = 0;
  if ( BattleResultBonusParam )
  {
    switch ( bonusType )
    {
      case 1u:
        result = this->DamagedAmount <= *(int *)&BattleResultBonusParam[16];
        break;
      case 2u:
        result = *(int *)&BattleResultBonusParam[16] <= this->OverDamageRatio;
        break;
      case 3u:
        result = *(int *)&BattleResultBonusParam[16] <= this->BreakCount;
        break;
      case 4u:
        result = *(int *)&BattleResultBonusParam[16] >= this->TurnCount;
        break;
    }
  }
  return result;
}
```

解释：`bonusType` 通过 `+0x08` 处的 `m_id` 选择行。`+0x10` 处的整数是 类型特定 阈值：根据 bonus 类型，可能是“No Damage”的承受伤害、overdamage ratio、break count 或 turn count。如果另一个导出字段还控制 奖励 channels 或 UI 文本，那是独立槽位；不应与这里展示的 阈值 读取方 混同。

### ■ (**) `BattleVoiceList` 与 `BattleVoiceArenaList`
定义战斗中由队伍成员或旁白播放的各种语音台词（例如 Or'Galdera 战斗中的特殊语音台词）。

可能相关：
```hpp
// Enum Kingship.EBATTLE_VOICE_ARENA_ID
// NumValues: 0x0003
enum class EBATTLE_VOICE_ARENA_ID : uint8
{
	None                                     = 0,
	Chairman_Monster_Arena                   = 1,
	EBATTLE_VOICE_ARENA_MAX                  = 2,
};

// Enum Kingship.EBATTLE_VOICETYPE_ID
// NumValues: 0x0038
enum class EBATTLE_VOICETYPE_ID : uint8
{
	NONE                                     = 0,
	BATTLE_1ST                               = 1,
	BATTLE_1ST_TOUGH                         = 2,
	BATTLE_1ST_BOSS                          = 3,
	BATTLE_1ST_BOSS2                         = 4,
	TURN_1ST                                 = 5,
	TURN_1ST_PINCH                           = 6,
	TURN_1ST_BREAK                           = 7,
	VICTORY                                  = 8,
	VICTORY_NARROW                           = 9,
	VICTORY_BOSS1                            = 10,
	VICTORY_BOSS2                            = 11,
	BOOST_LV1                                = 12,
	BOOST_LV2                                = 13,
	BOOST_LV3                                = 14,
	BOOST_LV3_EX1                            = 15,
	BOOST_LV3_EX2                            = 16,
	FINAL_BLOW                               = 17,
	FINAL_BLOW_BOSS                          = 18,
	WEAK_HIT                                 = 19,
	SHIELD_BREAK                             = 20,
	TO_BACK                                  = 21,
	TO_FORWARD                               = 22,
	TO_FORWARD_PINCH                         = 23,
	TO_FORWARD_DEAD                          = 24,
	AILMENTED                                = 25,
	RECOVERED                                = 26,
	AVOIDANCE                                = 27,
	REVIVE                                   = 28,
	DAMAGED_S                                = 29,
	DAMAGED_M                                = 30,
	DAMAGED_L                                = 31,
	DEAD                                     = 32,
	WIPEOUT                                  = 33,
	ATTACK                                   = 34,
	ATTACK_LV3                               = 35,
	SKILL_01                                 = 36,
	SKILL_02                                 = 37,
	SKILL_03                                 = 38,
	SKILL_04                                 = 39,
	SKILL_05                                 = 40,
	SKILL_06                                 = 41,
	SKILL_07                                 = 42,
	SKILL_08                                 = 43,
	SKILL_09                                 = 44,
	SPECIAL_SKILL_SET                        = 45,
	SPECIAL_SKILL_START                      = 46,
	ATTACK_MISS                              = 47,
	AVOID_AILMENT                            = 48,
	ENEMY_COUNTER                            = 49,
	USE_ITEM_SELF                            = 50,
	USE_ITEM_FRIEND                          = 51,
	USE_ITEM_ENEMY                           = 52,
	DEFENCE                                  = 53,
	BUFFED                                   = 54,
	EBATTLE_VOICETYPE_MAX                    = 55,
};
```
#### ■ 运行时分析：`BattleVoiceList` 与 `BattleVoiceArenaList`
语音 表 在运行时是活动的，但本轮复原中最强的 直接读取方 是 竞技场 覆盖 路径。普通 战斗语音 路径接收一个已实体化的 语音 ID 列表，在适当时应用 竞技场 replacement，移除受 repeat-limit 限制的候选项，然后选择具体 语音 ID。

##### ◆ C-BATTLEVOICEARENA-LOOKUP-01：竞技场 语音 行按 键 索引，行跨度 208 字节

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482414-17482441
FScriptContainerElement *__fastcall AQPBattleManager::GetArenaVoiceDB(int VoiceID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( VoiceID <= 0
    || !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
  {
    return nullptr;
  }
  v2 = ADatabaseDefineStatic::m_Self;
  if ( ADatabaseDefineStatic::m_Self
    && ADatabaseDefineStatic::m_Self->m_BattleVoiceArenaList.m_Loaded
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_BattleVoiceArenaList.m_DBAccess,
                       VoiceID), ArrayIndex >= 0)
    && ArrayIndex < v2->m_BattleVoiceArenaList.m_DataList.ArrayNum )
  {
    return &v2->m_BattleVoiceArenaList.m_DataList.AllocatorInstance.Data[208 * ArrayIndex];
  }
  else
  {
    return nullptr;
  }
}
```

##### ◆ C-BATTLEVOICEARENA-CANDIDATES-02：竞技场 行可以 覆盖 候选语音 ID 列表

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17482461-17482519
__int64 __fastcall AQPBattleManager::GetBattleVoiceID(
        AQPBattleManager *this,
        FString **VoiceSymbol,
        EBATTLE_VOICETYPE_ID VoiceType,
        int Index,
        const TArray<int,TSizedDefaultAllocator<32> > *VoiceIDs,
        int VoiceID,
        AQPBattleCharacterBase *Character,
        int **ComponentIndex)
{
  ...
  ArrayNum = VoiceIDs->ArrayNum;
  Data = VoiceIDs->AllocatorInstance.Data;
  IDs.ArrayNum = ArrayNum;
  memcpy_0(IDs.AllocatorInstance.Data, Data, 4 * ArrayNum);
  IsArenaBattle = AQPBattleManager::IsArenaBattle(this);
  if ( !IsArenaBattle )
    goto LABEL_15;
  ...
  v28 = (unsigned __int8)AQPBattleManager::CheckArenaVoiceID(
                           this, &VoiceID, VoiceSymbol,
                           (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&arenaIDs,
                           VoiceType, v11);
  if ( !v28 || !VoiceID )
    goto LABEL_12;
  IDs.ArrayNum = arenaIDs.ArrayNum;
  memcpy_0(IDs.AllocatorInstance.Data, arenaIDs.AllocatorInstance.Data, 4 * v17);
```

解释：`BattleVoiceArenaList` 是叠加在普通 战斗-语音 候选 列表 上的 overlay。它不会替换整个 语音-选择 subsystem；在战斗被识别为 竞技场 战斗 时，它会有条件地替换或过滤候选项。

### ■ (***) BattleAbortConditions
OT0 的 战斗 termination/forced-outcome 条件 表：每个条目定义一小组 谓词。战斗中这些 谓词 满足时，会导致战斗 abort（提前结束），或根据 `EnemyGroups` 中引用它的字段被视为 forced win/forced lose。
- `m_Conditions`：条件 类型 IDs（与 `TacticalList`、`TacticalActionList`、`SkillConditionList`、`BattleEventList` 中相同的数值 条件 命名空间共享）
- `m_Params`：每个 条件 的参数（含义取决于 条件 类型）
- 以及仅对特定 条件 类型 有意义的 过滤器：
   - `m_AilmentTypes`（以 `SkillAilmentType.m_id` 作为输入）
   - `m_StatusTypes`（可能以 `CharaStatusID.m_id` 作为输入）
   - `m_WeaponTypes`（以 `WeaponType.m_id` 作为输入）
   - `m_MagicTypes`（以 `Magictype.m_id` 作为输入）
   - `m_EnemyID`（以 `EnemyID.m_id` 作为输入）

#### ■ 运行时证据整合说明
- 来自 `Octopath_Traveler0-Win64-Shipping.exe.c` 的 decomp-only anchor：
    - `AQPBattleManager::CheckBattleAbort` 会解析相关 `BattleAbortConditions` 行，把该行的第七个 过滤器 数组（`v17 + 7`，即 `m_EnemyID` 槽位）传给 `UBattleUtility::CheckConditions`，而 `CheckConditions` 会把该槽位作为 `EnemyID` 参数转发给 `UBattleUtility::CheckCondition`；这证明该字段是活动 条件 过滤器，而不只是被复制的 结构定义 槽位（`Octopath_Traveler0-Win64-Shipping.exe.c`:17493321-17493429, 17493527-17493542, and 17389535-17389666）
- 精确 IDA anchors：
    - `0x144373530` `ADatabaseDefineStatic::execGetBattleAbortConditions`：该表的具名 运行时 访问器
#### ■ 运行时读取语义
【主要 C 证据】

##### ◆ C-GROUP-02：EnemyGroups 实体化器 with 具体 行偏移

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17923448-17923558
char __fastcall FDbAccessHelper<FEnemyGroupsBase>::GetData(
        FDbAccessHelper<FEnemyGroupsBase> *this,
        int dbID,
        FEnemyGroupsBase *outData)
{
  int m_IdFlag; // eax
  int v7; // r11d
  FScriptContainerElement *Data; // r8
  TSizedInlineAllocator<1,32,TSizedDefaultAllocator<32> >::ForElementType<FSetElementId> *p_Hash; // rax
  int HashSize; // ecx
  int v11; // edx
  int v12; // eax
  FScriptContainerElement *v13; // rcx
  __int64 v14; // rax
  FScriptContainerElement *v16; // rax
  const TArray<FEnemyGroupsBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v18; // rbx

  m_IdFlag = this->m_IdFlag;
  v7 = -1;
  if ( m_IdFlag <= 0 )
  {
    if ( m_IdFlag < 0 )
    {
      v7 = dbID;
      goto LABEL_16;
    }
    return 0;
  }
  if ( this->m_AccessTable.Pairs.Elements.Data.ArrayNum == this->m_AccessTable.Pairs.Elements.NumFreeIndices )
    return 0;
  Data = this->m_AccessTable.Pairs.Hash.SecondaryData.Data;
  p_Hash = &this->m_AccessTable.Pairs.Hash;
  HashSize = this->m_AccessTable.Pairs.HashSize;
  if ( !Data )
    Data = (FScriptContainerElement *)p_Hash;
  v11 = *(_DWORD *)&Data[4 * (dbID & (HashSize - 1))];
  v12 = v11;
  if ( v11 != -1 )
  {
    v13 = this->m_AccessTable.Pairs.Elements.Data.AllocatorInstance.Data;
    while ( 1 )
    {
      v14 = 2LL * v12;
      if ( *(_DWORD *)&v13[8 * v14] == dbID )
        break;
      v12 = *(_DWORD *)&v13[8 * v14 + 8];
      if ( v12 == -1 )
        return 0;
    }
    while ( 1 )
    {
      v16 = &v13[16 * v11];
      if ( *(_DWORD *)v16 == dbID )
        break;
      v11 = *(_DWORD *)&v16[8];
      if ( v11 == -1 )
      {
        v16 = nullptr;
        break;
      }
    }
    v7 = *(_DWORD *)&v16[4];
  }
LABEL_16:
  if ( v7 < 0 )
    return 0;
  m_DatabasePtr = this->m_DatabasePtr;
  if ( !m_DatabasePtr || v7 >= m_DatabasePtr->ArrayNum )
    return 0;
  v18 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[144 * v7];
  outData->m_id = *(_DWORD *)(v18 + 8);
  outData->m_Label = *(FName *)(v18 + 12);
  outData->m_Inescapable = *(_BYTE *)(v18 + 20);
  outData->m_DisableRetire = *(_BYTE *)(v18 + 21);
  outData->m_SkipResult = *(_BYTE *)(v18 + 22);
  outData->m_IgnoreDefeat = *(_BYTE *)(v18 + 23);
  outData->m_SkipBgmResult = *(_BYTE *)(v18 + 24);
  outData->m_BanGodBeast = *(_BYTE *)(v18 + 25);
  outData->m_PlayerMemberSetID = *(_DWORD *)(v18 + 28);
  outData->m_Bgm = *(_DWORD *)(v18 + 32);
  outData->m_NightBgm = *(_DWORD *)(v18 + 36);
  outData->m_DarkBgm = *(_DWORD *)(v18 + 40);
  outData->m_BgmType = *(_DWORD *)(v18 + 44);
  outData->m_CameraSet = *(_DWORD *)(v18 + 48);
  outData->m_Formation = *(_DWORD *)(v18 + 52);
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EnemyID,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 56));
  TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
    (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EventIndices,
    (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v18 + 72));
  outData->m_AbortCondition = *(_DWORD *)(v18 + 88);
  outData->m_DefeatPriority = *(_BYTE *)(v18 + 92);
  outData->m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);
  outData->m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);
  outData->m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);
  TArray<unsigned char,TSizedDefaultAllocator<32>>::operator=(
    (TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&outData->m_ImportantFlags,
    (const TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)(v18 + 112));
  outData->m_PetDear = *(_DWORD *)(v18 + 128);
  outData->m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);
  outData->m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);
  outData->m_DisableSupporter = *(_BYTE *)(v18 + 134);
  outData->m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);
  outData->m_EnablePlayerShield = *(_BYTE *)(v18 + 140);
  outData->m_DisableGrade = *(_BYTE *)(v18 + 141);
  outData->m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);
  return 1;
}

```

##### ◆ C-BEVENT-03：Shared 战斗-条件 tuple 求值器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17389550-17389705
  unsigned int v13; // r13d
  int v15; // edx
  int v16; // edi
  FScriptContainerElement *v18; // rax
  __int64 v20; // rbx
  __int64 v21; // rax
  int v22; // edx
  int v23; // r8d
  ESKILL_AILMENT_TYPE v24; // r9d
  int Weapon; // r10d
  int Magic; // r11d
  int v27; // ebp
  unsigned int v28; // eax
  bool v29; // zf
  int v30; // ecx
  int v32; // [rsp+60h] [rbp-48h]
  FScriptContainerElement *v33; // [rsp+68h] [rbp-40h]

  Data = (int *)Conditions->AllocatorInstance.Data;
  v13 = 0;
  v15 = 0;
  v16 = 0;
  v32 = 0;
  v18 = &Conditions->AllocatorInstance.Data[4 * Conditions->ArrayNum];
  v33 = v18;
  if ( Conditions->AllocatorInstance.Data == v18 )
    goto LABEL_33;
  v20 = 0;
  do
  {
    if ( *Data <= 0 )
      goto LABEL_29;
    if ( v20 < 0 || v16 >= Params->ArrayNum )
    {
      v22 = 0;
      v21 = 4 * v20;
      if ( v20 < 0 )
        goto LABEL_10;
    }
    else
    {
      v21 = 4 * v20;
      v22 = *(_DWORD *)&Params->AllocatorInstance.Data[4 * v20];
    }
    if ( v16 < Statuses->ArrayNum )
    {
      v23 = *(_DWORD *)&Statuses->AllocatorInstance.Data[v21];
      goto LABEL_11;
    }
LABEL_10:
    LOBYTE(v23) = 0;
    if ( v20 < 0 )
      goto LABEL_13;
LABEL_11:
    if ( v16 >= Ailments->ArrayNum )
    {
LABEL_13:
      v24 = 0;
      if ( v20 < 0 )
        goto LABEL_16;
      goto LABEL_14;
    }
    v24 = *(_DWORD *)&Ailments->AllocatorInstance.Data[v21];
LABEL_14:
    if ( v16 < Weapons->ArrayNum )
    {
      Weapon = *(_DWORD *)&Weapons->AllocatorInstance.Data[v21];
      goto LABEL_17;
    }
LABEL_16:
    LOBYTE(Weapon) = 0;
    if ( v20 < 0 )
      goto LABEL_19;
LABEL_17:
    if ( v16 >= Magics->ArrayNum )
    {
LABEL_19:
      LOBYTE(Magic) = 0;
      if ( v20 < 0 )
        goto LABEL_22;
      goto LABEL_20;
    }
    Magic = *(_DWORD *)&Magics->AllocatorInstance.Data[v21];
LABEL_20:
    if ( v16 < EnemyID->ArrayNum )
    {
      v27 = *(_DWORD *)&EnemyID->AllocatorInstance.Data[v21];
      goto LABEL_23;
    }
LABEL_22:
    v27 = 0;
    if ( v20 < 0 )
      goto LABEL_25;
LABEL_23:
    if ( v16 >= EquipID->ArrayNum )
    {
LABEL_25:
      v28 = 0;
      goto LABEL_26;
    }
    v28 = *(_DWORD *)&EquipID->AllocatorInstance.Data[v21];
LABEL_26:
    ++v16;
    ++v20;
    v29 = (unsigned __int8)UBattleUtility::CheckCondition(
                             *Data,
                             v22,
                             (ECHARA_STATUS)v23,
                             v24,
                             Weapon,
                             Magic,
                             v27,
                             v28,
                             Enforcer,
                             Target,
                             pEnemies,
                             pFriendlies) == 0;
    v18 = v33;
    v30 = v32 + 1;
    if ( v29 )
      v30 = v32;
    v15 = v30;
    v32 = v30;
LABEL_29:
    ++Data;
  }
  while ( Data != (int *)v18 );
  v13 = 0;
  if ( v16 > 0 )
  {
    v13 = -1;
    if ( v15 == v16 )
      v13 = 1;
  }
LABEL_33:
  UBattleUtility::s_bIsORCondition = 0;
  UBattleUtility::s_JobCountID = 0;
  return v13;
}

//----- (00000001444D3550) ----------------------------------------------------
bool __fastcall UBattleTacticalComponent::CheckExtensionPresage(
        UBattleTacticalComponent *this,
        bool isActionSkip,
        bool updatedTurn)
{
  int m_PresageTurn; // eax
  int m_PresageLeft; // ecx
  __int64 ArrayNum; // rbx
  TArray<int,TSizedDefaultAllocator<32> > *p_m_ActionIDs; // r14
  __int64 v10; // r15
  size_t v11; // rbx
  bool result; // al
  int v13; // eax
  int v14; // ebx
  unsigned int v15; // edx
```

##### ◆ C-BABORT-01：BattleAbortConditions 结构体 复制 of seven 条件/过滤 槽位

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:16976367-16976425
char __fastcall UScriptStruct::TCppStructOps<FBattleAbortConditionsBase>::Copy(
        UScriptStruct::TCppStructOps<FBattleAbortConditionsBase> *this,
        TArray<FPackedNormal,TSizedDefaultAllocator<32> > *Dest,
        _BYTE *Src,
        int ArrayDim)
{
  int v4; // ebp
  TArray<FPackedNormal,TSizedDefaultAllocator<32> > *v5; // rsi
  signed __int64 v6; // r14
  int v7; // eax
  const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *v8; // rbx
  TArray<FPackedNormal,TSizedDefaultAllocator<32> > *v9; // rdi
  const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *v10; // rdx

  v4 = ArrayDim;
  v5 = Dest;
  if ( ArrayDim )
  {
    v6 = Src - (_BYTE *)Dest;
    do
    {
      v7 = *(int *)((char *)&v5->ArrayNum + v6);
      v8 = (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)((char *)v5 + v6);
      v9 = v5;
      v10 = (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)((char *)v5 + v6 + 16);
      v5 += 8;
      v9->ArrayNum = v7;
      TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v9 + 1, v10);
      TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v9 + 2, v8 + 2);
      TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v9 + 3, v8 + 3);
      TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v9 + 4, v8 + 4);
      TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v9 + 5, v8 + 5);
      TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v9 + 6, v8 + 6);
      TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(v9 + 7, v8 + 7);
      --v4;
    }
    while ( v4 );
  }
  return 1;
}

//----- (000000014431A300) ----------------------------------------------------
UFunction *__fastcall Z_Construct_UFunction_ABattleCameraBase_GetComponentCamera()
{
  UFunction *result; // rax

  result = ReturnFunction_7761;
  if ( !ReturnFunction_7761 )
  {
    UECodeGen_Private::ConstructUFunction(
      (USparseDelegateFunction **)&ReturnFunction_7761,
      &Z_Construct_UFunction_ABattleCameraBase_GetComponentCamera_Statics::FuncParams);
    return ReturnFunction_7761;
  }
  return result;
}

//----- (000000014431A330) ----------------------------------------------------
UFunction *__fastcall Z_Construct_UFunction_ABattleCameraBase_GetCurrentCameraType()
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_AilmentTypes` | `ailment lane`（数组）；引用块中没有保留下更短的具名赋值 | `BattleAbortConditions` 行被复制为七个并行 `TArray<int>` 槽位。共享 `UBattleUtility::CheckConditions` 求值器 会按 槽位 消费 条件、参数、状态、异常状态、武器、魔法 与 敌人 ID 槽位；缺失 槽位 默认置零。 | 已复原 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Conditions` | `condition lane`（数组）；引用块中没有保留下更短的具名赋值 | `BattleAbortConditions` 行被复制为七个并行 `TArray<int>` 槽位。共享 `UBattleUtility::CheckConditions` 求值器 会按 槽位 消费 条件、参数、状态、异常状态、武器、魔法 与 敌人 ID 槽位；缺失 槽位 默认置零。 | 已复原 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_EnemyID` | `enemy-id lane`（数组）；引用块中没有保留下更短的具名赋值 | `BattleAbortConditions` 行被复制为七个并行 `TArray<int>` 槽位。共享 `UBattleUtility::CheckConditions` 求值器 会按 槽位 消费 条件、参数、状态、异常状态、武器、魔法 与 敌人 ID 槽位；缺失 槽位 默认置零。 | 已复原 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_MagicTypes` | `magic lane`（数组）；引用块中没有保留下更短的具名赋值 | `BattleAbortConditions` 行被复制为七个并行 `TArray<int>` 槽位。共享 `UBattleUtility::CheckConditions` 求值器 会按 槽位 消费 条件、参数、状态、异常状态、武器、魔法 与 敌人 ID 槽位；缺失 槽位 默认置零。 | 已复原 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Params` | `parameter lane`（数组）；引用块中没有保留下更短的具名赋值 | `BattleAbortConditions` 行被复制为七个并行 `TArray<int>` 槽位。共享 `UBattleUtility::CheckConditions` 求值器 会按 槽位 消费 条件、参数、状态、异常状态、武器、魔法 与 敌人 ID 槽位；缺失 槽位 默认置零。 | 已复原 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_StatusTypes` | `status lane`（数组）；引用块中没有保留下更短的具名赋值 | `BattleAbortConditions` 行被复制为七个并行 `TArray<int>` 槽位。共享 `UBattleUtility::CheckConditions` 求值器 会按 槽位 消费 条件、参数、状态、异常状态、武器、魔法 与 敌人 ID 槽位；缺失 槽位 默认置零。 | 已复原 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_WeaponTypes` | `weapon lane`（数组）；引用块中没有保留下更短的具名赋值 | `BattleAbortConditions` 行被复制为七个并行 `TArray<int>` 槽位。共享 `UBattleUtility::CheckConditions` 求值器 会按 槽位 消费 条件、参数、状态、异常状态、武器、魔法 与 敌人 ID 槽位；缺失 槽位 默认置零。 | 已复原 | `C-BABORT-01`, `C-BEVENT-03` |

### ■ (*) `BattleActionID`
一个小型 查找 表，把“动作 ID”（可能是 `SkillAilmentType.m_ActionID`）映射到具体 战斗 animation/motion，并带有一个 标志，用于说明该动画是否应使用 NPC/字段 character rig 而不是标准 战斗 rig 播放。`m_UseNPC` 可能决定该 动作 是否应使用 NPC character animation set，而不是通常的 战斗-sprite。
可能相关：`Kingship_structs.hpp` 为一些 动作 IDs 分配了以下标签：
```hpp
enum class EBATTLE_ACTION_ID : uint8
{
	NONE                                     = 0,
	IDLE                                     = 1,
	BOOST_IDLE                               = 2,
	BATTLE_START                             = 3,
	STEP_IN                                  = 4,
	STEP_OUT                                 = 5,
	BOOST_START                              = 6,
	BOOST_LEVEL_UP                           = 7,
	BOOST_CANCEL                             = 8,
	KNOCKBACK                                = 9,
	PINCH                                    = 10,
	DEAD                                     = 11,
	GUARD                                    = 12,
	ESCAPE                                   = 13,
	VICTORY                                  = 14,
	LEVELUP                                  = 15,
	USE_ITEM                                 = 16,
	AILMENT                                  = 17,
	ATTACK_SWORD                             = 18,
	ATTACK_AXE                               = 19,
	ATTACK_DAGGER                            = 20,
	ATTACK_PAPERS                            = 21,
	ATTACK_ROD                               = 22,
	ATTACK_BOW                               = 23,
	ATTACK_LANCE                             = 24,
	ATTACK_FAN                               = 25,
	PRE_ACTION                               = 74,
	ATTACK_NORMAL                            = 75,
	ATTACK_DEATHBLOW                         = 76,
	EBATTLE_ACTION_MAX                       = 77,
};
```
#### ■ 运行时读取语义
`BattleActionID` 行不只是 label 表。player-character 战斗-动作 路径会按 `ActionID` 解析行，然后把 字节 偏移 `+0x10` 作为 `m_UseNPC` 门控 读取。该 门控 只会对 `m_bNpcFlag` 已设置的 character objects 求值。

【主要 C 证据】

##### ◆ C-BACTION-01：BattleActionID 行解析 by ActionID

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17579929-17579966
FScriptContainerElement *__fastcall AQPCharacterBattlePlayer::GetActionDB(
        AQPCharacterBattlePlayer *this,
        unsigned __int8 ActionID)
{
  unsigned int v2; // edi
  ADatabaseDefineStatic *v3; // rbx
  int ArrayIndex; // eax

  v2 = ActionID;
  if ( !ADatabaseDefineStatic::m_Self || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    return nullptr;
  v3 = ADatabaseDefineStatic::m_Self;
  if ( ADatabaseDefineStatic::m_Self
    && ADatabaseDefineStatic::m_Self->m_BattleActionID.m_Loaded
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_BattleActionID.m_DBAccess,
                       v2),
        ArrayIndex >= 0)
    && ArrayIndex < v3->m_BattleActionID.m_DataList.ArrayNum )
  {
    return &v3->m_BattleActionID.m_DataList.AllocatorInstance.Data[24 * ArrayIndex];
  }
  else
  {
    return nullptr;
  }
}

//----- (00000001445B56C0) ----------------------------------------------------
UMaterialInterface *__fastcall UKSCharacterFunction::GetActionMaterial(
        const FName inActionName,
        ECHARACTER_ANIMATION_ID ActionID,
        bool isTranslucent)
{
  _DWORD *v5; // rdi
  UMaterialInterface *v6; // rcx
  UMaterialInterface *v7; // rdx
  int v8; // ebx
```

##### ◆ C-BACTION-02：m_UseNPC 作为 NPC-only 动作 门控

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17577500-17577542

//----- (00000001445B24A0) ----------------------------------------------------
void __fastcall AQPCharacterBattlePlayer::ExecPlayBattleAction(
        AQPCharacterBattlePlayer *this,
        bool *isValid,
        unsigned __int8 ActionID,
        bool StackClear)
{
  unsigned __int8 v5; // di
  FScriptContainerElement *ActionDB; // rax
  AQPCharacterBattlePlayer_vtbl *v9; // rax
  ECHARACTER_ANIMATION_ID v10; // eax
  __int64 v11; // rdx
  EKSCharacterDir dir; // [rsp+70h] [rbp+18h] BYREF
  ECHARACTER_ANIMATION_ID action; // [rsp+78h] [rbp+20h] BYREF

  v5 = ActionID;
  if ( this->m_bNpcFlag )
  {
    ActionDB = AQPCharacterBattlePlayer::GetActionDB(this, ActionID);
    if ( !ActionDB || !*(_BYTE *)&ActionDB[16] )
      v5 = 1;
  }
  dir = this->m_bInvertFlipbook + 2;
  if ( StackClear )
    this->ExecClearActionStack(this);
  v9 = this->AQPCharacterBattle::AKSCharacterBase::APaperCharacter::ACharacter::APawn::AActor::UObject::UObjectBaseUtility::UObjectBase::__vftable;
  if ( v5 == 1 )
  {
    v10 = v9->ExecGetIdle(this, 0);
    v11 = (unsigned int)v10;
    action = v10;
  }
  else
  {
    v9->ExecGetCharacterActionInfo(this, (EBATTLE_ACTION_ID)v5, &action, &dir);
    v11 = (unsigned int)action;
  }
  ((void (__fastcall *)(AQPCharacterBattlePlayer *, __int64, _QWORD))this->ExecPushActionStack)(
    this,
    v11,
    (unsigned __int8)dir);
  *isValid = 1;
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_UseNPC` | `C-BACTION-02:17577515` `ActionDB = AQPCharacterBattlePlayer::GetActionDB(this, ActionID);`<br>`C-BACTION-02:17577516` `if ( !ActionDB || !*(_BYTE *)&ActionDB[16] )`<br>`C-BACTION-01:17579948` `m_BattleActionID.m_DBAccess`<br>`C-BACTION-01:17579958` `Data[24 * ArrayIndex]` | `m_UseNPC` 是 24 字节 `BattleActionID` 行偏移 `+0x10` 处的 字节。在 `AQPCharacterBattlePlayer::ExecPlayBattleAction` 中，仅当 `this->m_bNpcFlag` 为 true 时才测试该字段。若行缺失或该 字节 为零，请求的 动作 ID 在 animation 解析 前会被强制为 `1`，即 NPC/字段-rig character 会 回退 到 idle 动作。非 NPC player characters 在引用函数中不走该 guard 路径。 | 已复原 | `C-BACTION-01`, `C-BACTION-02` |

### ■ (*) BattleParams
多种 战斗-related 参数；根据 Dumper-7 SDK 中的 `Kingship_structs.hpp`（见 BravelyPath Modular Discord 的 #file-dump），其含义如下：
```hpp
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
#### ■ 运行时读取语义
`BattleParams` 通过两阶段机制读取。在 战斗-manager 初始化/使用期间，数据库行被规范化到 `m_AllBattleParamList`；之后的运行时调用点使用 `EBATTLE_PARAMS` 数值作为 unsigned-字节 索引 访问该缓存向量。缓存会为同一个 authoring 值 存储四种类型视图：字符串、整数、浮点数 与 boolean。

【主要 C 证据】

##### ◆ C-BPARAM-01：BattleParams 行 are 已复制 from the 数据库表

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17537187-17537233
            v5 = v1->m_AllBattleParamList.ArrayNum;
            v39 = &v1->m_AllBattleParamList;
            if ( (int)ArrayNum <= (int)v5 )
            {
              if ( (ArrayNum & 0x80000000) != 0LL )
                UE::Core::Private::OnInvalidArrayNum(ArrayNum);
              if ( (int)ArrayNum < (int)v5 )
              {
                v15 = v5 - ArrayNum;
                if ( v15 )
                {
                  TArray<BattleParam,TSizedDefaultAllocator<32>>::RemoveAtImpl(&v1->m_AllBattleParamList, ArrayNum, v15);
                  TArray<TTuple<UE::MovieScene::FPreAnimatedStorageIndex,UE::MovieScene::FPreAnimatedSkeletalAnimationState>,TSizedDefaultAllocator<32>>::ResizeShrink((TArray<TTuple<FName,Metasound::MetasoundGeneratorPrivate::FParameterSetter>,TSizedDefaultAllocator<32> > *)&v1->m_AllBattleParamList);
                }
              }
            }
            else
            {
              ArrayMax = v1->m_AllBattleParamList.ArrayMax;
              v7 = ArrayNum - v5;
              p_m_AllBattleParamList->ArrayNum = ArrayNum;
              if ( (int)ArrayNum - (int)v5 > (unsigned int)(ArrayMax - v5) )
                TArray<Audio::FQuartzClockProxy,TSizedDefaultAllocator<32>>::ResizeGrow(p_m_AllBattleParamList, v5);
              v8 = &p_m_AllBattleParamList->AllocatorInstance.Data[40 * v5];
              if ( v7 )
              {
                v9 = v8 + 20;
                do
                {
                  *v8 = 0;
                  v8 += 40;
                  *(_QWORD *)&v9[-12] = 0;
                  *(_QWORD *)&v9[-4] = 0;
                  *(_QWORD *)&v9[4] = 0;
                  v9[12] = 0;
                  v9 += 40;
                  --v7;
                }
                while ( v7 );
              }
              v1 = this;
            }
            Data = v2->m_BattleParams.m_DataList.AllocatorInstance.Data;
            v11 = &Data[32 * v2->m_BattleParams.m_DataList.ArrayNum];
            v38 = v11;
            if ( Data != v11 )
            {
```

##### ◆ C-BPARAM-02：数值 字符串 conversion into 已缓存 int/浮点数/布尔 槽位

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17537270-17537335
                    if ( *(_DWORD *)&v12[8] )
                      v18 = *(const wchar_t **)v12;
                    else
                      v18 = &InExpression;
                    v19 = _wtof(v18);
                    *((float *)&battleParam.BoolValue + 1) = v19;
                  }
                  else
                  {
                    *((_DWORD *)&battleParam.BoolValue + 1) = 0;
                  }
                  v20 = FString::ToBool((FString *)v12);
                  v21 = p_m_AllBattleParamList->AllocatorInstance.Data;
                  v36 = v20;
                  v22 = 5LL * *(int *)&v12[-8];
                  v21[8 * v22] = (FScriptContainerElement)battleParam.StringValue.Data.AllocatorInstance.Data;
                  v23 = &v21[8 * v22 + 8];
                  if ( v23 != (FScriptContainerElement *)&battleParam.StringValue.Data.ArrayNum )
                  {
                    IntValue = battleParam.IntValue;
                    v25 = *(const void **)&battleParam.StringValue.Data.ArrayNum;
                    v26 = *(_DWORD *)&v23[12];
                    *(_DWORD *)&v23[8] = battleParam.IntValue;
                    if ( (_DWORD)IntValue || v26 )
                    {
                      TArray<FFloat16,TSizedDefaultAllocator<32>>::ResizeForCopy(
                        (TArray<FFloat16,TSizedDefaultAllocator<32> > *)&v21[8 * v22 + 8],
                        IntValue,
                        v26);
                      if ( (_DWORD)IntValue )
                        memcpy_0(*(void **)v23, v25, 2 * IntValue);
                    }
                    else
                    {
                      *(_DWORD *)&v23[12] = 0;
                    }
                    v11 = v38;
                  }
                  *(_DWORD *)&v21[8 * v22 + 24] = *(_DWORD *)&battleParam.BoolValue;
                  *(_DWORD *)&v21[8 * v22 + 28] = *((_DWORD *)&battleParam.BoolValue + 1);
                  v21[8 * v22 + 32] = (FScriptContainerElement)v36;
                  v27 = nullptr;
                  Package = FindPackage(nullptr, L"/Script/Kingship");
                  PrivateStaticClass = UEnum::GetPrivateStaticClass();
                  Object = (UEnum *)StaticFindObject(PrivateStaticClass, Package, L"EBATTLE_PARAMS", 0);
                  if ( Object )
                  {
                    NameStringByIndex = UEnum::GetNameStringByIndex(
                                          Object,
                                          &result,
                                          LOBYTE(battleParam.StringValue.Data.AllocatorInstance.Data));
                    if ( &name.Data.ArrayNum != (int *)NameStringByIndex )
                    {
                      v27 = NameStringByIndex->Data.AllocatorInstance.Data;
                      NameStringByIndex->Data.AllocatorInstance.Data = nullptr;
                      *(_QWORD *)&NameStringByIndex->Data.ArrayNum = 0;
                    }
                    if ( result.Data.AllocatorInstance.Data )
                      FMemory::Free(result.Data.AllocatorInstance.Data);
                    if ( v27 )
                      FMemory::Free(v27);
                  }
                  if ( *(_QWORD *)&battleParam.StringValue.Data.ArrayNum )
                    FMemory::Free(*(void **)&battleParam.StringValue.Data.ArrayNum);
                  p_m_AllBattleParamList = (TArray<Audio::TSampleBuffer<float>,TSizedDefaultAllocator<32> > *)v39;
                }
```

##### ◆ C-BPARAM-03：EBATTLE_PARAMS ID indexes m_AllBattleParamList

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17542732-17542755
FScriptContainerElement *__fastcall AQPBattleManager::GetBattleParam(
        AQPBattleManager *this,
        unsigned __int8 battleParam)
{
  int v2; // edi
  UPackage *Package; // rbx
  UClass *PrivateStaticClass; // rax
  UEnum *Object; // rax
  FString *NameStringByIndex; // rax
  FScriptContainerElement *Data; // rbx
  FString result; // [rsp+20h] [rbp-28h] BYREF
  FString name; // [rsp+30h] [rbp-18h] BYREF

  v2 = battleParam;
  if ( battleParam < this->m_AllBattleParamList.ArrayNum )
    return &this->m_AllBattleParamList.AllocatorInstance.Data[40 * battleParam];
  Package = FindPackage(nullptr, L"/Script/Kingship");
  PrivateStaticClass = UEnum::GetPrivateStaticClass();
  Object = (UEnum *)StaticFindObject(PrivateStaticClass, Package, L"EBATTLE_PARAMS", 0);
  if ( Object )
  {
    NameStringByIndex = UEnum::GetNameStringByIndex(Object, &result, v2);
    Data = nullptr;
    if ( &name != NameStringByIndex )
```

##### ◆ C-BPARAM-04：浮点数 访问器 reads the 已缓存 浮点数 槽位

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17394976-17395018
float __fastcall UBattleUtility::GetFloatBattleParam(unsigned __int8 ParamsID)
{
  FScriptContainerElement *Data; // rcx
  float v3; // xmm6_4
  FScriptContainerElement *BattleParam; // rax
  FScriptContainerElement *v5; // rbx
  BattleParam param; // [rsp+20h] [rbp-48h] BYREF

  Data = nullptr;
  v3 = 0.0;
  memset(&param.StringValue, 0, 25);
  if ( AQPBattleManager::m_Self )
  {
    if ( UObjectBase::IsValidLowLevelFast(AQPBattleManager::m_Self, 1) )
    {
      if ( AQPBattleManager::m_Self )
      {
        if ( (AQPBattleManager::m_Self->ObjectFlags & 0x40000000) == 0 )
        {
          BattleParam = AQPBattleManager::GetBattleParam(AQPBattleManager::m_Self, ParamsID);
          v5 = BattleParam;
          if ( BattleParam )
          {
            TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(
              (TArray<unsigned short,TSizedDefaultAllocator<32> > *)&param.StringValue,
              (const TArray<unsigned short,TSizedDefaultAllocator<32> > *)&BattleParam[8]);
            param.IntValue = *(_DWORD *)&v5[24];
            v3 = *(float *)&v5[28];
            param.FloatValue = v3;
            param.BoolValue = (bool)v5[32];
          }
        }
      }
    }
    Data = param.StringValue.Data.AllocatorInstance.Data;
  }
  if ( Data )
    FMemory::Free(Data);
  return v3;
}

//----- (00000001444D9640) ----------------------------------------------------
float __fastcall UBattleUtility::GetGameSpeedParam(bool isQuick)
```

##### ◆ C-BPARAM-05：整数 访问器 reads the 已缓存 整数 槽位

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17395099-17395120
          {
            TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(
              (TArray<unsigned short,TSizedDefaultAllocator<32> > *)&param.StringValue,
              (const TArray<unsigned short,TSizedDefaultAllocator<32> > *)&BattleParam[8]);
            v1 = *(_DWORD *)&v5[24];
            param.IntValue = v1;
            param.FloatValue = *(float *)&v5[28];
            param.BoolValue = (bool)v5[32];
          }
        }
      }
    }
    Data = param.StringValue.Data.AllocatorInstance.Data;
  }
  if ( Data )
    FMemory::Free(Data);
  return v1;
}

//----- (00000001444D9880) ----------------------------------------------------
UE::Math::TRotator<double> *__fastcall ABattleCameraBase::GetLastLookatTrackingRotation(
        ABattleCameraBase *this,
```

**运行时解释：**

| 运行时元素 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `EBATTLE_PARAMS` ID / 行 ID | `C-BPARAM-01:17537220` `v13 = *(_DWORD *)&v12[-8];`<br>`C-BPARAM-03:17542742` `if ( battleParam < this->m_AllBattleParamList.ArrayNum )` | 数据库行 ID 被用作 `m_AllBattleParamList` 的密集 索引。因此，指南中的 enum constants 是运行时 键，而不只是描述性 labels。 | 已复原 | `C-BPARAM-01`, `C-BPARAM-03` |
| 字符串 值 | `C-BPARAM-01:17537225` `v16 = *(int *)&v12[8];`<br>`C-BPARAM-01:17537226` `v17 = *(const wchar_t **)v12;` | 源表值以数据库行 字符串 槽位处的类 `FString` payload 存储，并被复制到缓存的 `BattleParam.StringValue`。 | 已复原 | `C-BPARAM-01` |
| 整数 值 | `C-BPARAM-02:17537302` `*(_DWORD *)&v21[8 * v22 + 24] = *(_DWORD *)&battleParam.BoolValue;`<br>`C-BPARAM-05:17395117` `v1 = *(_DWORD *)&v5[24];` | 缓存期间，数值 strings 会通过 `_wtoi` 解析；之后缓存的 整数 访问器 读取 已缓存 `BattleParam` 记录 的偏移 `+0x18`。 | 已复原 | `C-BPARAM-02`, `C-BPARAM-05` |
| 浮点数 值 | `C-BPARAM-02:17537274` `v19 = _wtof(v18);`<br>`C-BPARAM-02:17537303` `*(_DWORD *)&v21[8 * v22 + 28] = *((_DWORD *)&battleParam.BoolValue + 1);`<br>`C-BPARAM-04:17395003` `v3 = *(float *)&v5[28];` | 缓存期间，数值 strings 会通过 `_wtof` 解析；之后缓存的 浮点数 访问器 读取 已缓存 `BattleParam` 记录 的偏移 `+0x1C`。 | 已复原 | `C-BPARAM-02`, `C-BPARAM-04` |
| boolean 值 | `C-BPARAM-02:17537281` `v20 = FString::ToBool((FString *)v12);`<br>`C-BPARAM-02:17537304` `v21[8 * v22 + 32] = (FScriptContainerElement)v36;` | boolean 视图在 缓存 construction 期间由 `FString::ToBool` 获得，并存储在 已缓存 offset `+0x20`。 | 已复原 | `C-BPARAM-02` |
#### ■ `BattleParams` caching 与 类型化访问器
`BattleParams` 不是每次都 ad hoc 地从数据库读取。战斗 manager 首先把每一行缓存到按 `EBATTLE_PARAMS` 索引的 类型化 运行时 数组 中，之后 helpers 从固定偏移读取 已缓存 字符串/int/浮点数/布尔 views。这就是 enum-like ID 列表 即便源数据是 DB 表 也具有运行意义的原因。

##### ◆ C-BATTLEPARAMS-CACHE-BUILD-01：行值 are 已缓存 by 参数 ID

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17537132-17537351
void __fastcall AQPBattleManager::CacheAllBattleParam(AQPBattleManager *this)
{
  ...
  v5 = v4->m_BattleParams.m_DataList.ArrayNum;
  Data = v4->m_BattleParams.m_DataList.AllocatorInstance.Data;
  ...
  for ( ; Data != v7; Data += 32 )
  {
    // row +0x08 is the EBATTLE_PARAMS id; row +0x10 is the exported string value
    paramID = *(_DWORD *)&Data[8];
    TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(
      (TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cache.StringValue,
      (const TArray<unsigned short,TSizedDefaultAllocator<32> > *)&Data[16]);
    ...
  }
}
```

##### ◆ C-BATTLEPARAMS-CACHE-INDEX-02：战斗-param IDs 直接索引 运行时 缓存

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17542732-17542768
FScriptContainerElement *__fastcall AQPBattleManager::GetBattleParam(
        AQPBattleManager *this,
        unsigned __int8 battleParam)
{
  int v2; // edi
  ...

  v2 = battleParam;
  if ( battleParam < this->m_AllBattleParamList.ArrayNum )
    return &this->m_AllBattleParamList.AllocatorInstance.Data[40 * battleParam];
  ...
  StaticFindObject(PrivateStaticClass, Package, L"EBATTLE_PARAMS", 0);
  ...
  return nullptr;
}
```

##### ◆ C-BATTLEPARAMS-TYPED-WRAPPERS-03：类型化封装 暴露 已缓存 字段

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17394976-17395016
float __fastcall UBattleUtility::GetFloatBattleParam(unsigned __int8 ParamsID)
{
  BattleParam = AQPBattleManager::GetBattleParam(AQPBattleManager::m_Self, ParamsID);
  if ( BattleParam )
  {
    TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(
      (TArray<unsigned short,TSizedDefaultAllocator<32> > *)&param.StringValue,
      (const TArray<unsigned short,TSizedDefaultAllocator<32> > *)&BattleParam[8]);
    param.IntValue = *(_DWORD *)&v5[24];
    v3 = *(float *)&v5[28];
    param.FloatValue = v3;
    param.BoolValue = (bool)v5[32];
  }
  return v3;
}

// Octopath_Traveler0-Win64-Shipping.exe.c:17395077-17395117
__int64 __fastcall UBattleUtility::GetIntBattleParam(unsigned __int8 ParamsID)
{
  BattleParam = AQPBattleManager::GetBattleParam(AQPBattleManager::m_Self, ParamsID);
  if ( BattleParam )
  {
    v1 = *(_DWORD *)&v5[24];
    param.FloatValue = *(float *)&v5[28];
    param.BoolValue = (bool)v5[32];
  }
  return v1;
}
```

解释：`BattleParams` 行会被解析为多种 类型化 representations。原始数据库值类似字符串，但 运行时 缓存 会在 已缓存 `BattleParam` 条目的 `+0x18`、`+0x1C`、`+0x20` 处暴露 整数、浮点数 与 boolean 视图。因此，添加新的 参数 IDs 不只是添加 行；caller code 必须请求那些 IDs，并且 缓存 必须足够大以容纳该数值 ID。


### ■ (*) `BattleDeathBlowPoint`
“Death Blow”（特殊攻击）量表 gain 的 查找 表。每一行本质上定义某个 检查 条件 是否满足（类型 + 值），以及 Death Blow 量表 应增加多少。
- `m_CheckType`：要检查的事物类型。
- `m_CheckValue`：该 检查 类型 的 阈值/amount。看起来是 count。
- `m_IncreasedPoint`：该 检查 匹配时要添加多少“Death Blow points”。

#### ■ 运行时读取语义
`BattleDeathBlowPoint` 由 `AQPBattleManager::CheckSpecialSkillPoint` 消费。反编译没有在 hot loop 中保留字段名，但复制的行跨度与偏移使用使三个 payload 字段 毫无歧义：偏移 `+0x0C` 是 检查类型 选择器，偏移 `+0x10` 是 检查 值，偏移 `+0x14` 是 point increment。

【主要 C 证据】

##### ◆ C-BDEATH-01：BattleDeathBlowPoint 表 复制 and 24-字节 行跨度

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17515498-17515534
              v9 = v4->m_BattleDeathBlowPoint.m_DataList.ArrayNum;
              v10 = 0;
              v11 = v4->m_BattleDeathBlowPoint.m_DataList.AllocatorInstance.Data;
              risePoint.AllocatorInstance.Data = nullptr;
              *(_QWORD *)&risePoint.ArrayNum = 0;
              damageResults.AllocatorInstance.Data = nullptr;
              *(_QWORD *)&damageResults.ArrayNum = 0;
              dataList.AllocatorInstance.Data = nullptr;
              dataList.ArrayNum = v9;
              if ( v9 )
              {
                TArray<FVillageFarmSupplyBase,TSizedDefaultAllocator<32>>::ResizeForCopy(
                  (TArray<FEpicSynth1Patch,TSizedDefaultAllocator<32> > *)&dataList,
                  v9,
                  0);
                v12 = v11 + 12;
                v13 = dataList.AllocatorInstance.Data + 12;
                do
                {
                  *(_QWORD *)&v13[-12] = FSimpleStatusData::`vftable';
                  v13 += 24;
                  v14 = *(_DWORD *)&v12[-4];
                  v12 += 24;
                  *(_DWORD *)&v13[-28] = v14;
                  *(_DWORD *)&v13[-24] = *(_DWORD *)&v12[-24];
                  *(_DWORD *)&v13[-20] = *(_DWORD *)&v12[-20];
                  *(_DWORD *)&v13[-16] = *(_DWORD *)&v12[-16];
                  --v9;
                }
                while ( v9 );
              }
              else
              {
                dataList.ArrayMax = 0;
              }
              v15 = (AQPBattleCharacterBase **)targetList.AllocatorInstance.Data;
              cachedMaxHP.Pairs.Elements.Data.AllocatorInstance.Data = nullptr;
```

##### ◆ C-BDEATH-02：检查类型 分派 and use of +0x10/+0x14

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17515780-17516005
                InOutElementIndex = v55;
                risePoint.ArrayNum = 0;
                if ( risePoint.ArrayMax < 0 )
                  TArray<long,TSizedDefaultAllocator<32>>::ResizeTo(
                    (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&risePoint,
                    0);
                if ( (unsigned __int8)v56 == 1 )
                  break;
                if ( (unsigned __int8)v56 == 2 )
                {
                  if ( isEnemy )
                  {
                    v76 = (AQPBattleCharacterBase **)targetList.AllocatorInstance.Data;
                    v77 = &targetList.AllocatorInstance.Data[8 * targetList.ArrayNum];
                    if ( targetList.AllocatorInstance.Data != v77 )
                    {
                      do
                      {
                        v78 = *v76;
                        if ( AQPBattleCharacterBase::HasGuardian(*v76) )
                          v78 = AQPBattleCharacterBase::GetGuardian(v78);
                        if ( v78 )
                        {
                          if ( (int)AQPBattleCharacterBase::GetReservedDamage((UMapObjComponent *)v78) > 0
                            && AQPBattleCharacterBase::IsGuard(v78, nullptr, 0) )
                          {
                            v79 = risePoint.ArrayNum;
                            v10 = 1;
                            if ( ++risePoint.ArrayNum > (unsigned int)risePoint.ArrayMax )
                              TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                                (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&risePoint,
                                v79);
                            *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v79] = v55;
                          }
                          else
                          {
                            v80 = risePoint.ArrayNum;
                            v10 = 0;
                            if ( ++risePoint.ArrayNum > (unsigned int)risePoint.ArrayMax )
                              TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                                (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&risePoint,
                                v80);
                            *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v80] = 0;
                          }
                        }
                        ++v76;
                      }
                      while ( v76 != (AQPBattleCharacterBase **)v77 );
                    }
                  }
                  goto LABEL_173;
                }
                if ( (unsigned __int8)v56 != 3 )
                {
                  if ( (unsigned __int8)v56 == 4 )
                  {
                    if ( !isEnemy && v50->m_MissedAction == HIT )
                    {
                      v66 = v50->m_UseBoostLevel == v54 && !v53;
                      IsGuard = AQPBattleCharacterBase::IsGuard(enforcer, nullptr, 0);
                      IsSupportAbility = USkillDB::IsSupportAbility(v50->m_UseSkill, 0);
                      v69 = (IsGuard ^ 1) & (IsSupportAbility ^ 1) & !USkillDB::IsItemSkill(v50->m_UseSkill) & v66;
                      v70 = v50->m_ActionCommand;
                      v10 = v69;
                      if ( (unsigned __int8)v122 >= BATTLING )
                        v10 = 0;
                      if ( v70
                        && (v70->ObjectFlags & 0x40000000) == 0
                        && AQPActionCommand::IsDelayedSkill(v50->m_ActionCommand) )
                      {
                        USkillDB::GetSkillAddAilment(
                          (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&ailments,
                          v50->m_UseSkill);
                        v71 = ailments.AllocatorInstance.Data;
                        v72 = &ailments.AllocatorInstance.Data[4 * ailments.ArrayNum];
                        if ( ailments.AllocatorInstance.Data != v72 )
                        {
                          while ( *(_DWORD *)v71 != 23 )
                          {
                            v71 += 4;
                            if ( v71 == v72 )
                              goto LABEL_102;
                          }
                          v10 = 0;
                        }
LABEL_102:
                        if ( ailments.AllocatorInstance.Data )
                          FMemory::Free(ailments.AllocatorInstance.Data);
                      }
                      v73 = 0;
                      v74 = risePoint.ArrayNum;
                      if ( v10 )
                        v73 = v55;
                      ++risePoint.ArrayNum;
                      if ( (unsigned int)(v74 + 1) > risePoint.ArrayMax )
                        TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                          (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&risePoint,
                          v74);
                      *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v74] = v73;
                    }
                  }
                  else if ( (unsigned __int8)v56 == 5 && !isEnemy )
                  {
                    v57 = (AQPBattleCharacterBase **)targetList.AllocatorInstance.Data;
                    v58 = 0;
                    v59 = &targetList.AllocatorInstance.Data[8 * targetList.ArrayNum];
                    if ( targetList.AllocatorInstance.Data != v59 )
                    {
                      do
                      {
                        v60 = *v57;
                        IsJustBreaking = AQPBattleCharacterBase::IsJustBreaking(*v57);
                        v62 = v58 + 1;
                        if ( !IsJustBreaking )
                          v62 = v58;
                        v58 = v62;
                        if ( !v60->m_bIsOccurredSpecialGaugeByBreak )
                        {
                          v63 = AQPBattleCharacterBase::IsJustBreaking(v60);
                          v10 |= v63;
                          if ( v63 )
                            v60->m_bIsOccurredSpecialGaugeByBreak = 1;
                        }
                        ++v57;
                      }
                      while ( v57 != (AQPBattleCharacterBase **)v59 );
                    }
                    if ( v10 )
                      v64 = v58 * v55;
                    else
                      v64 = 0;
                    v65 = risePoint.ArrayNum;
                    if ( ++risePoint.ArrayNum > (unsigned int)risePoint.ArrayMax )
                      TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                        (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&risePoint,
                        v65);
                    *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v65] = v64;
                  }
LABEL_173:
                  if ( v10 )
                  {
LABEL_174:
                    LODWORD(v139) = 1065353216;
                    FloatBattleParam = UBattleUtility::GetFloatBattleParam(0xBu);
                    v140 = UBattleUtility::GetFloatBattleParam(0x15u);
                    bonusRates.AllocatorInstance.Data = nullptr;
                    bonusRates.ArrayNum = 3;
                    TArray<FVertexInstanceID,TSizedDefaultAllocator<32>>::ResizeForCopy(
                      (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&bonusRates,
                      3,
                      0);
                    v101 = nullptr;
                    v102 = bonusRates.AllocatorInstance.Data;
                    v103 = v140;
                    v104 = _mm_shuffle_ps((__m128)v139, (__m128)v139, 225);
                    v104.m128_f32[0] = FloatBattleParam;
                    v104.m128_u64[0] = _mm_shuffle_ps(v104, v104, 225).m128_u64[0];
                    *(_QWORD *)bonusRates.AllocatorInstance.Data = v104.m128_u64[0];
                    *(float *)&v102[8] = v103;
                    riseTargets.AllocatorInstance.Data = nullptr;
                    *(_QWORD *)&riseTargets.ArrayNum = 0;
                    v139 = v104.m128_u64[0];
                    if ( !isEnemy )
                    {
                      riseTargets.ArrayNum = 1;
                      TArray<ImmediatePhysics_Chaos::FActorHandle *,TSizedDefaultAllocator<32>>::ResizeGrow(
                        (TArray<TObjectPtr<UWorldMetricsExtension>,TSizedDefaultAllocator<32> > *)&riseTargets,
                        0);
                      *(_QWORD *)riseTargets.AllocatorInstance.Data = enforcer;
                      goto LABEL_178;
                    }
                    v105 = targetList.ArrayNum;
                    if ( targetList.ArrayNum )
                    {
                      riseTargets.ArrayNum = targetList.ArrayNum;
                      TArray<ImmediatePhysics_Chaos::FActorHandle *,TSizedDefaultAllocator<32>>::ResizeGrow(
                        (TArray<TObjectPtr<UWorldMetricsExtension>,TSizedDefaultAllocator<32> > *)&riseTargets,
                        0);
                      memcpy_0(riseTargets.AllocatorInstance.Data, targetList.AllocatorInstance.Data, 8 * v105);
LABEL_178:
                      if ( riseTargets.ArrayNum )
                      {
                        v101 = (AQPBattleCharacterBase **)riseTargets.AllocatorInstance.Data;
                        v106 = 0;
                        v107 = 0;
                        v108 = &riseTargets.AllocatorInstance.Data[8 * riseTargets.ArrayNum];
                        if ( riseTargets.AllocatorInstance.Data != v108 )
                        {
                          while ( 1 )
                          {
                            v109 = *v101;
                            if ( AQPBattleCharacterBase::HasGuardian(*v101) )
                              v109 = AQPBattleCharacterBase::GetGuardian(v109);
                            if ( v109 )
                            {
                              if ( v107 < 0 || v106 >= risePoint.ArrayNum )
                                goto LABEL_196;
                              if ( AQPBattleCharacterBase::isPlayer(v109, 0, 0) )
                              {
                                SpecialSkillGaugeLevel = AQPBattleCharacterBase::GetSpecialSkillGaugeLevel(v109);
                                if ( SpecialSkillGaugeLevel < 0 || SpecialSkillGaugeLevel >= bonusRates.ArrayNum )
                                  v111 = *(float *)&FLOAT_1_0.InWorldToScreen;
                                else
                                  v111 = *(float *)&bonusRates.AllocatorInstance.Data[4 * SpecialSkillGaugeLevel];
                                v112 = (int)(float)((float)*(int *)&risePoint.AllocatorInstance.Data[4 * v107] * v111);
                                v113 = AQPBattleCharacterBase::GetPlayerID(v109);
                                SupportAbillityAvailValue = UPlayerDataUtility::GetSupportAbillityAvailValue(
                                                              v113,
                                                              DEATHBLOWPOINT_UP,
                                                              0);
                                if ( SupportAbillityAvailValue > 0 )
                                  v112 = (int)(float)((float)((float)SupportAbillityAvailValue * 0.0099999998)
                                                    * (float)v112);
                                if ( v112 )
                                  AQPBattleCharacterBase::AddSpecialSkillGaugeValue(v109, v112);
                              }
                              ++v106;
                              ++v107;
                            }
                            if ( ++v101 == (AQPBattleCharacterBase **)v108 )
                              goto LABEL_196;
                          }
                        }
                      }
                      else
                      {
```

##### ◆ C-BDEATH-03：敌方 阈值 branch and 最终 rise-point storage

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17516085-17516148
                  if ( v95 == -1 )
                  {
LABEL_154:
                    v96 = nullptr;
                  }
                  else
                  {
                    while ( 1 )
                    {
                      v96 = &cachedMaxHP.Pairs.Elements.Data.AllocatorInstance.Data[16 * v95];
                      if ( *(_DWORD *)v96 == v86 )
                        break;
                      v95 = *(_DWORD *)&v96[8];
                      if ( v95 == -1 )
                        goto LABEL_154;
                    }
                  }
                  v90 = *(_DWORD *)&v96[4];
                }
                v91 = (int)(float)((float)((float)v54 * 0.0099999998) * (float)v90);
                ReservedDamage = AQPBattleCharacterBase::GetReservedDamage((UMapObjComponent *)v85);
                v93 = ReservedDamage > 0 && ReservedDamage <= v91;
                v97 = damageResults.ArrayNum;
                if ( v83 < 0 || v82 >= damageResults.ArrayNum )
                {
                  ++damageResults.ArrayNum;
                  if ( (unsigned int)(v97 + 1) > damageResults.ArrayMax )
                    TArray<unsigned char,TSizedDefaultAllocator<32>>::ResizeGrow(
                      (TArray<enum ESourceEffectMotionFilterModDestination,TSizedDefaultAllocator<32> > *)&damageResults,
                      v97);
                  damageResults.AllocatorInstance.Data[v97] = (FScriptContainerElement)v93;
                }
                else
                {
                  damageResults.AllocatorInstance.Data[v83] = (FScriptContainerElement)v93;
                }
                if ( !v93 )
                {
LABEL_167:
                  v99 = risePoint.ArrayNum;
                  if ( ++risePoint.ArrayNum > (unsigned int)risePoint.ArrayMax )
                    TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                      (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&risePoint,
                      v99);
                  *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v99] = 0;
                }
                else
                {
                  v98 = risePoint.ArrayNum;
                  if ( ++risePoint.ArrayNum > (unsigned int)risePoint.ArrayMax )
                    TArray<enum USkinnedMeshComponent::EBoneTransformUpdateMethod,TSizedDefaultAllocator<32>>::ResizeGrow(
                      (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&risePoint,
                      v98);
                  v10 = v93;
                  *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v98] = InOutElementIndex;
                }
                v54 = v128;
                ++v82;
                ++v83;
LABEL_171:
                if ( ++v81 == (AQPBattleCharacterBase **)v84 )
                  goto LABEL_172;
              }
            }
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_CheckType` | `C-BDEATH-01:17515519-17515523` 将源偏移 `+0x0C/+0x10/+0x14` 复制到本地 24 字节行副本。<br>`C-BDEATH-02:17515786` `v56 = *(_DWORD *)&v52[12];`<br>`C-BDEATH-02:17515793` `if ( (unsigned __int8)v56 == 1 )`<br>`C-BDEATH-02:17515795` `if ( (unsigned __int8)v56 == 2 )`，同一块稍后还有 类型 `3`、`4`、`5` branches。 | `m_CheckType` 存储在行偏移 `+0x0C` 的 32-bit 槽位中，但 求值器 在语义上按其 low 字节 分派。已复原 cases 是 branch-coded，并未在 C 导出 中以 enum 命名：类型 `1` 进入 敌方 projected-damage 阈值 路径；类型 `2` 检查带 reserved damage 的 guarded targets；类型 `3` 是 non-敌人 hit/boost/武器 路径；类型 `4` 是 non-敌人 hit/boost/non-武器 技能 路径，并带额外 guard/support/道具 exclusions；类型 `5` 统计新近被 broken 的 targets。 | 已复原 | `C-BDEATH-01`, `C-BDEATH-02`, `C-BDEATH-03` |
| `m_CheckValue` | `C-BDEATH-02:17515784` `v54 = *(_DWORD *)&v52[16];`<br>`C-BDEATH-02:17515991` `if ( v50->m_UseBoostLevel == v54 && v53 )`<br>`C-BDEATH-03:17516102` `v91 = (int)(float)((float)((float)v54 * 0.0099999998) * (float)v90);` | `m_CheckValue` 是行偏移 `+0x10`。它的含义依 类型 而定。在 类型-3/类型-4 的玩家侧分支中，它与 `m_UseBoostLevel` 比较；在 类型-1 的敌人侧分支中，它被解释为目标 已缓存 最大 HP 的百分比 阈值。 | 已复原 | `C-BDEATH-02`, `C-BDEATH-03` |
| `m_IncreasedPoint` | `C-BDEATH-02:17515785` `v55 = *(_DWORD *)&v52[20];`<br>`C-BDEATH-02:17515822` `risePoint[...] = v55;`<br>`C-BDEATH-02:17515955` `v64 = v58 * v55;`<br>`C-BDEATH-03:17516143` `risePoint[...] = InOutElementIndex;` | `m_IncreasedPoint` 是行偏移 `+0x14`。它是写入临时 `risePoint` 数组的基础 量表 increment。对于 类型 `5`，代码会把该值乘以新近 broken 的 targets 数量。随后 最终 increment 会根据当前 special-技能-量表 等级 bonus 与 `DEATHBLOWPOINT_UP` support ability 进行缩放，之后调用 `AddSpecialSkillGaugeValue`。 | 已复原 | `C-BDEATH-02`, `C-BDEATH-03` |



#### ■ 深入运行时分析：`BattleDeathBlowPoint` 分派 与 量表 变动

`BattleDeathBlowPoint` 是活的 combat 表。运行时会复制该表，遍历每个 24 字节行，把 检查 类型 的 low 字节 解释为 分派 选择器，然后向 `risePoint` 追加候选 量表 increases。随后在同一函数中，每个非零 rise point 都会按 战斗 参数 和 support abilities 缩放，然后 `AddSpecialSkillGaugeValue` 会改变玩家 量表。

##### ◆ C-BATTLEDEATHBLOW-WORKLIST-01：表 is 已复制 into an invoke-time work 列表

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17515498-17515524
v9 = v4->m_BattleDeathBlowPoint.m_DataList.ArrayNum;
v11 = v4->m_BattleDeathBlowPoint.m_DataList.AllocatorInstance.Data;
dataList.ArrayNum = v9;
if ( v9 )
{
  TArray<...>::ResizeForCopy((TArray<...> *)&dataList, v9, 0);
  v12 = v11 + 12;
  v13 = dataList.AllocatorInstance.Data + 12;
  do
  {
    *(_QWORD *)&v13[-12] = FSimpleStatusData::`vftable';
    v13 += 24;
    v14 = *(_DWORD *)&v12[-4];
    v12 += 24;
    *(_DWORD *)&v13[-28] = v14;
    *(_DWORD *)&v13[-24] = *(_DWORD *)&v12[-24];
    *(_DWORD *)&v13[-20] = *(_DWORD *)&v12[-20];
    *(_DWORD *)&v13[-16] = *(_DWORD *)&v12[-16];
    --v9;
  }
  while ( v9 );
}
```

##### ◆ C-BATTLEDEATHBLOW-ROW-FIELDS-02：each 行 supplies 检查 类型, 检查 值, and increased point

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17515774-17515839
while ( 1 )
{
  v54 = *(_DWORD *)&v52[16];  // check value lane
  v55 = *(_DWORD *)&v52[20];  // point-increase lane
  v56 = *(_DWORD *)&v52[12];  // check-type lane
  ...
  if ( (unsigned __int8)v56 == 1 )
    break;
  if ( (unsigned __int8)v56 == 2 )
  {
    if ( isEnemy )
    {
      ...
      if ( (int)AQPBattleCharacterBase::GetReservedDamage((UMapObjComponent *)v78) > 0
        && AQPBattleCharacterBase::IsGuard(v78, nullptr, 0) )
      {
        *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v79] = v55;
      }
    }
    goto LABEL_173;
  }
  if ( (unsigned __int8)v56 == 4 )
  {
    if ( !isEnemy && v50->m_MissedAction == HIT )
    {
      v66 = v50->m_UseBoostLevel == v54 && !v53;
      ...
    }
  }
}
```

##### ◆ C-BATTLEDEATHBLOW-CHECK-BRANCHES-03：break、boosted 动作 与 damage-阈值 branches feed 量表 gain

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17515881-17515917
else if ( (unsigned __int8)v56 == 5 && !isEnemy )
{
  ...
  IsJustBreaking = AQPBattleCharacterBase::IsJustBreaking(*v57);
  ...
  if ( !v60->m_bIsOccurredSpecialGaugeByBreak )
  {
    v63 = AQPBattleCharacterBase::IsJustBreaking(v60);
    v10 |= v63;
    if ( v63 )
      v60->m_bIsOccurredSpecialGaugeByBreak = 1;
  }
  ...
  if ( v10 )
    v64 = v58 * v55;
  else
    v64 = 0;
  *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v65] = v64;
}

// Octopath_Traveler0-Win64-Shipping.exe.c:17516104-17516139
v91 = (int)(float)((float)((float)v54 * 0.0099999998) * (float)v90);
ReservedDamage = AQPBattleCharacterBase::GetReservedDamage((UMapObjComponent *)v85);
v93 = ReservedDamage > 0 && ReservedDamage <= v91;
...
if ( v93 )
{
  v10 = v93;
  *(_DWORD *)&risePoint.AllocatorInstance.Data[4 * v98] = InOutElementIndex;
}
```

##### ◆ C-BATTLEDEATHBLOW-GAUGE-APPLY-04：最终 point application scales and 变动 the special-技能 量表

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17515921-17515994
if ( v10 )
{
  FloatBattleParam = UBattleUtility::GetFloatBattleParam(0xBu);
  v140 = UBattleUtility::GetFloatBattleParam(0x15u);
  ...
  SpecialSkillGaugeLevel = AQPBattleCharacterBase::GetSpecialSkillGaugeLevel(v109);
  v111 = *(float *)&bonusRates.AllocatorInstance.Data[4 * SpecialSkillGaugeLevel];
  v112 = (int)(float)((float)*(int *)&risePoint.AllocatorInstance.Data[4 * v107] * v111);
  SupportAbillityAvailValue = UPlayerDataUtility::GetSupportAbillityAvailValue(
                                v113, DEATHBLOWPOINT_UP, 0);
  if ( SupportAbillityAvailValue > 0 )
    v112 = (int)(float)((float)((float)SupportAbillityAvailValue * 0.0099999998) * (float)v112);
  if ( v112 )
    AQPBattleCharacterBase::AddSpecialSkillGaugeValue(v109, v112);
}
```

解释：即使 检查类型 names 在 C 导出 中仍是 anonymous，已复原的字段角色也很强：`m_CheckType` 是行 `+0x0C` 处的 分派 选择器，`m_CheckValue` 是 `+0x10` 处的 comparison/percentage/boost-等级 值，`m_IncreasedPoint` 是 `+0x14` 处的基础 point payload。分派 branches 不是通用 阈值 表；它们绑定到具体 combat states，例如 guard damage、boost 等级、just-breaking targets 与 reserved-damage 阈值。

### ■ (*) `BattleLayoutSet`
可能是一个小型 战斗 staging/布局 preset 表，将 摄像机 preset 与敌方 positioning 绑定在一起。不清楚游戏中哪里使用它（例如，没有 formation 或 reinforcements 似乎使用 `PositionID` 270）。

`Kingship_structs.hpp` 为一些 布局 sets 提供以下标签：

```hpp
// Enum Kingship.EBATTLE_LAYOUT_SET
// NumValues: 0x0005
enum class EBATTLE_LAYOUT_SET : uint8
{
	None                                     = 0,
	RAID_DEFAULT                             = 1,
	RAID_PLAYER_ALL                          = 2,
	RAID_AoE_ATTACK_OFFSET                   = 3,
	EBATTLE_LAYOUT_MAX                       = 4,
};
```
#### ■ 运行时读取语义
C 导出 证明 `BattleLayoutSet` 在 raid-视图 摄像机 路径 中是活运行时数据。它通过 数据库 访问 helper 按 布局 ID 解析，随后该行作为至少包含 `m_CameraParamID` 与 `m_EnemyPosition` 的 `FBattleLayoutSetBase` 被消费。

【主要 C 证据】

##### ◆ C-BLAYOUT-01：BattleLayoutSet 行查找

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17542705-17542731
FScriptContainerElement *__fastcall AQPBattleManager::GetBattleLayoutDB(AQPBattleManager *this, int layoutID)
{
  ADatabaseDefineStatic *v3; // rbx
  int ArrayIndex; // eax

  if ( layoutID <= 0 )
    return nullptr;
  if ( ADatabaseDefineStatic::m_Self && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    v3 = ADatabaseDefineStatic::m_Self;
  else
    v3 = nullptr;
  if ( v3->m_BattleLayoutSet.m_Loaded
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&v3->m_BattleLayoutSet.m_DBAccess,
                       layoutID),
        ArrayIndex >= 0)
    && ArrayIndex < v3->m_BattleLayoutSet.m_DataList.ArrayNum )
  {
    return &v3->m_BattleLayoutSet.m_DataList.AllocatorInstance.Data[24 * ArrayIndex];
  }
  else
  {
    return nullptr;
  }
}

//----- (0000000144586780) ----------------------------------------------------
```

##### ◆ C-BLAYOUT-02：m_CameraParamID and m_EnemyPosition 被读取 by 讨伐视图

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17510692-17510783
        const FBattleLayoutSetBase *battleLayout,
        float surplus,
        int tagIndex,
        const FBattleLayoutSetBase *offset)
{
  int m_CameraParamID; // ebp
  bool v8; // si
  ABattleCameraRaid *BattleCamera; // rax
  int TagMax; // eax
  float v11; // xmm6_4
  float v12; // xmm6_4
  ABattleCameraRaid *v13; // r14
  unsigned __int32 v14; // esi
  signed int m_EnemyPosition; // edi
  __m128d v16; // xmm2
  __m128d v17; // xmm1
  UCurveFloat *OffsetCurve; // rcx
  bool hasCam; // [rsp+30h] [rbp-98h] BYREF
  float MaxTime; // [rsp+34h] [rbp-94h] BYREF
  float MinTime; // [rsp+38h] [rbp-90h] BYREF
  UE::Math::TVector<double> Position; // [rsp+40h] [rbp-88h] BYREF
  UE::Math::TVector<double> furthestPos; // [rsp+58h] [rbp-70h] BYREF
  UE::Math::TVector<double> nearestPos; // [rsp+70h] [rbp-58h] BYREF
  bool isValid; // [rsp+D8h] [rbp+10h] BYREF

  if ( !battleLayout )
    return 0;
  this->m_RaidLastFocusTagIndex = tagIndex;
  m_CameraParamID = battleLayout->m_CameraParamID;
  v8 = tagIndex >= 0 || surplus > 0.0;
  if ( m_CameraParamID <= 0 )
    return 0;
  BattleCamera = AQPBattleManager::GetBattleCamera(this, &hasCam);
  if ( !hasCam )
    return 0;
  if ( v8 )
  {
    furthestPos = UE::Math::TVector<double>::ZeroVector;
    nearestPos = UE::Math::TVector<double>::ZeroVector;
    UEnemyDB::GetBattlePosition(&furthestPos, 0xFAu);
    UEnemyDB::GetBattlePosition(&nearestPos, 0xFBu);
    TagMax = AQPBattleManager::GetTagMax(this, 1);
    if ( surplus <= 0.0 )
      v11 = (float)(TagMax - tagIndex - 1);
    else
      v11 = (float)((float)TagMax - 1.0) - fminf((float)(surplus * 0.06666667) + 3.0, (float)(TagMax - 1));
    v12 = v11 * this->m_FormationInterval;
    isValid = 0;
    v13 = AQPBattleManager::GetBattleCamera(this, &isValid);
    if ( !isValid )
      return 0;
    v14 = 0;
    m_EnemyPosition = 0;
    if ( offset )
    {
      v14 = offset->m_CameraParamID;
      m_EnemyPosition = offset->m_EnemyPosition;
    }
    if ( v13->GetPlayingFlag(v13, 0, 0, 0, 1, 0) )
    {
      return 0;
    }
    else
    {
      ABattleCameraRaid::StartMoveArc(v13, COERCE_FLOAT(LODWORD(v12) ^ _xmm), v14);
      if ( this->m_RaidRelativeLocations.Enemies.ArrayNum > 0 )
      {
        this->m_IsUpdateRaidSyncEnemies = 1;
        if ( m_EnemyPosition > 0 )
        {
          Position = UE::Math::TVector<double>::ZeroVector;
          if ( UEnemyDB::GetBattlePosition(&Position, m_EnemyPosition) )
          {
            v16 = *(__m128d *)&Position.X;
            v17 = (__m128d)*(unsigned __int64 *)&Position.Z;
            this->m_RaidEnemyOffsetMove.OffsetRelativeTransform.Rotation.XY = _mm_cvtps_pd(GlobalVectorConstants::Float0001);
            this->m_RaidEnemyOffsetMove.OffsetRelativeTransform.Rotation.ZW = _mm_cvtps_pd(
                                                                                _mm_movehl_ps(
                                                                                  GlobalVectorConstants::Float0001,
                                                                                  GlobalVectorConstants::Float0001));
            this->m_RaidEnemyOffsetMove.OffsetRelativeTransform.Translation.XY = v16;
            this->m_RaidEnemyOffsetMove.OffsetRelativeTransform.Translation.ZW = v17;
            this->m_RaidEnemyOffsetMove.OffsetRelativeTransform.Scale3D.XY = _mm_cvtps_pd(GlobalVectorConstants::Float1110);
            this->m_RaidEnemyOffsetMove.OffsetRelativeTransform.Scale3D.ZW = _mm_cvtps_pd(
                                                                               _mm_movehl_ps(
                                                                                 GlobalVectorConstants::Float1110,
                                                                                 GlobalVectorConstants::Float1110));
            OffsetCurve = this->m_RaidEnemyOffsetMove.OffsetCurve;
            this->m_RaidEnemyOffsetMove.MoveState = MovingTo;
            this->m_RaidEnemyOffsetMove.IsOffsetMoving = 1;
            if ( OffsetCurve )
            {
```

##### ◆ C-BLAYOUT-03：布局 IDs 1、0 与 caller-supplied offsets in UpdateRaidView

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17510784-17510849
              MinTime = 0.0;
              MaxTime = 0.0;
              UCurveBase::GetTimeRange(OffsetCurve, &MinTime, &MaxTime);
              this->m_RaidEnemyOffsetMove.MoveSec = MaxTime;
            }
          }
        }
      }
      return 1;
    }
  }
  else
  {
    ABattleCameraBase::StartFreeMove(BattleCamera, m_CameraParamID, &UE::Math::TVector<double>::ZeroVector);
    return 1;
  }
}
// 14631D6D0: using guessed type __int128 _xmm;

//----- (0000000144561600) ----------------------------------------------------
char __fastcall AQPBattleManager::UpdateRaidView(
        AQPBattleManager *this,
        int focusTagIndex,
        float surplus,
        unsigned __int8 offset)
{
  int v4; // esi
  FScriptContainerElement *BattleLayoutDB; // rbp
  FScriptContainerElement *v8; // rsi
  int v9; // edi
  TArray<int,TSizedDefaultAllocator<32> > result; // [rsp+30h] [rbp-28h] BYREF

  v4 = offset;
  if ( focusTagIndex < 0 )
    return 0;
  BattleLayoutDB = AQPBattleManager::GetBattleLayoutDB(this, 1);
  v8 = AQPBattleManager::GetBattleLayoutDB(this, v4);
  AQPBattleManager::GetActiveAreaTagIndices(
    this,
    (TArray<enum EMediaEvent,TSizedDefaultAllocator<32> > *)&result,
    focusTagIndex,
    0);
  v9 = *(_DWORD *)&result.AllocatorInstance.Data[4 * result.ArrayNum - 4];
  if ( result.AllocatorInstance.Data )
    FMemory::Free(result.AllocatorInstance.Data);
  return AQPBattleManager::UpdateRaidView(
           this,
           (const FBattleLayoutSetBase *)BattleLayoutDB,
           surplus,
           v9,
           (const FBattleLayoutSetBase *)v8);
}

//----- (00000001445616B0) ----------------------------------------------------
void __fastcall AQPBattleManager::UpdateRaidView(AQPBattleManager *this, AQPBattleCharacterBase *targetPlayer)
{
  int v4; // ebx
  FScriptContainerElement *BattleLayoutDB; // rsi
  FScriptContainerElement *offset; // rbp
  int v7; // ebx
  TArray<int,TSizedDefaultAllocator<32> > result; // [rsp+30h] [rbp-28h] BYREF
  bool temp; // [rsp+68h] [rbp+10h] BYREF
  int tagIndex; // [rsp+70h] [rbp+18h] BYREF

  if ( targetPlayer )
  {
```

【字段级解释】

| 键 / 行字段 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_id` / 布局 ID | `C-BLAYOUT-01:17542710` `if ( layoutID <= 0 )`<br>`C-BLAYOUT-01:17542720` `m_BattleLayoutSet.m_DBAccess`<br>`C-BLAYOUT-01:17542729` `Data[24 * ArrayIndex]` | 布局 ID 通过 `m_BattleLayoutSet.m_DBAccess` 解析；ID `0` 会被 取值函数 拒绝。返回行的跨度为 24 字节。 | 已复原 | `C-BLAYOUT-01` |
| `m_CameraParamID` | `C-BLAYOUT-02:17510719` `m_CameraParamID = battleLayout->m_CameraParamID;`<br>`C-BLAYOUT-02:17510721` `if ( m_CameraParamID <= 0 ) return 0;`<br>`C-BLAYOUT-02:17510778` `StartFreeMove(..., m_CameraParamID, ...)` | `m_CameraParamID` 是 布局 选择的主要 摄像机 preset。若其非正，更新失败。在 non-raid-offset 模式中，它会直接传给 `ABattleCameraBase::StartFreeMove`；在 raid-offset 模式中，第二个 布局 的 摄像机 ID 会作为 arc offset ID 使用。 | 已复原 | `C-BLAYOUT-02` |
| `m_EnemyPosition` | `C-BLAYOUT-02:17510747` `m_EnemyPosition = offset->m_EnemyPosition;`<br>`C-BLAYOUT-02:17510759` `if ( m_EnemyPosition > 0 )`<br>`C-BLAYOUT-02:17510762` `UEnemyDB::GetBattlePosition(&Position, m_EnemyPosition)` | `m_EnemyPosition` 是从 offset 布局 读取的可选 战斗-位置 ID。为正时，它通过 `UEnemyDB::GetBattlePosition` 解析，并存入 raid 敌人 offset 变换。这是直接运行时使用，不是推断出的 formation reference。 | 已复原 | `C-BLAYOUT-02` |

## ■ `AIBattle` 下
这些文件负责敌人 AI 行为以及敌人使用的 技能（以及使用方式）。

对于敌人 AI，engine 读取：

```text
EnemyGroups
  -> EnemyID
      -> EnemyTypeID / EnemyWeakID / SkillResistAilmentID
      -> TacticalAssignList
          -> TacticalList
              -> TacticalActionList
                  -> TacticalSkillList
                      -> SkillID
                          -> SkillAvailID
                              -> optional SkillConditionList (→invoke conditions)
```

### ■ (****) `TacticalList`, `TacticalAssignList`, and `TacticalSkillList`
OT0 的敌人 AI “rule clause”表：每个条目定义一组条件；当这些条件满足时，AI 会选择或启用某种行为。敌人通过 `TacticalAssignList` 文件分配这些“rules”，其 `m_id` 对应 `EnemyID.m_TacticalAssignID`；战斗 技能 通过 `TacticalSkillList` 分配给它们，其 `m_id` 对应 `EnemyID.m_SkillsID`。
- `m_Presage`：可能是 enum，表示该 tactic 是否会显示 presage（telegraphed 动作）
- `m_PresageSkillID`：与 presage 关联的 技能 ID（即正在 telegraph 的内容）。例如，boss 宣告 boost 模式 时会使用它（这些 技能 IDs 随后明显**不会**出现在敌人的 `TacticalSkillList.m_UseSkills` 数组中）
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`：事件 标志 gating 与 side 效果，见 `BattleEventCommand`。可能用于 AI 行为的 branching/sequencing。
- `m_Conditions`：很可能与 `TacticalActionList`、`BattleEventList`、`SkillConditionList` 相同的 条件，并带有相同的 `m_Params` 与 过滤器 数组
   - `m_AilmentTypes`（以 `SkillAilmentType.m_id` 作为输入）
   - `m_StatusTypes`（可能以 `CharaStatusID.m_id` 作为输入）
   - `m_WeaponTypes`（以 `WeaponType.m_id` 作为输入）
   - `m_MagicTypes`（以 `Magictype.m_id` 作为输入）

每个 条件 的含义尚未完全命名。请使用本地 [Condition Crossref Atlas](Octopath_Condition_Crossref_Atlas.md) 作为当前 guide-bundle inventory；当该 atlas 只能证明 shape、source 表 或 参数 槽位 时，请让 条件 IDs 保持 unresolved。`../raw_evidence/` 下导入的 CotCPort snapshot 是历史 source evidence，而不是当前 OT0 v1.0.8 名称的 authority。

#### ■ 运行时证据整合说明
早期 address-等级 战术 notes 已被整合进下方具名的 `UTacticalDB`、`UBattleTacticalComponent` 和 `UBattleUtility` 证据。C 导出 现在提供 类型化 行 访问器 与具名 条件-求值 calls，因此本节避免旧的 `sub_...` 命名，除非地址是某个引用 C excerpt 固有的一部分。条件 IDs 仍应通过引用的 求值器 paths 解释，而不是仅通过 结构定义 names 解释。

### ■ (****) `TacticalActionList`
看起来是在 tactic 适用时告诉游戏实际要做什么：使用哪个 技能（按 索引）、以谁为目标、如何在可能 targets 中选择，以及额外 条件/priority rules。
- `m_SkillIndex`：这本身不是 SkillID。它是 技能 列表/数组 的索引（测试显示为敌人 `m_SkillsID` 对应的 `TacticalSkillList.m_UseSkills[]`）
- `m_SelectType`：选择 模式 enum，`Kingship_structs.hpp` 定义如下：

```hpp
enum class EACTION_SELECT_TYPE : uint8
{
	None                                     = 0,
	FixedTurn                                = 1,
	MultipleTurn                             = 2,
	Random                                   = 3,
	Random_Recommend                         = 5,
	Random_NoRecommend                       = 6,
	Sequential                               = 4,
	EACTION_SELECT_MAX                       = 7,
};
```

- `m_FriendlyIndex`：以“friendly”侧为目标的对象（从敌人视角？）。这里 `-1` 可能表示“没有固定 friendly；也许基于 `SelectType` 选择（？）”
- `m_PrioritySkill`：（未测试）若设置，可能 覆盖 正常 选择 并强制使用特定 技能 索引（或把某个 技能 索引 标记为 preferred？）
- `m_TurnCount`：（未测试）只要指定 战术 ID 的 条件 满足，就每 `N` 回合重复一个 动作，其中 `N` 是此键的值。根据 SelectType 也可能具有不同含义。
- `m_GroupIndex`：grouping 键，可能用于按 phase 分隔 动作，或限制每 turn 每 群组 的 动作 数量，或协调 群组 中多个敌人的行为
- 额外 动作-等级 条件（类似 `TacticalList`，但特定于指定 动作）：`m_Conditions`、`m_Params`、`m_AilmentTypes`、`m_StatusTypes`、`m_WeaponTypes`、`m_MagicTypes`

#### ■ 运行时读取语义
【主要 C 证据】

##### ◆ C-ENEMY-02：EnemyID 实体化器 with 具体 行偏移

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17647272-17647369
void __fastcall ASymbolEnemy::GetTargetLevel(ASymbolEnemy *this, FString *outLevel, FString *outName)
{
  unsigned int m_EnemyListId; // edx
  FDbAccessHelper<FEnemyIDBase> *p_m_DBAccess; // rsi
  FScriptContainerElement *v8; // rdi
  FScriptContainerElement *Data; // rbx
  int ArrayIndex; // eax
  const TArray<FEnemyIDBase,TSizedDefaultAllocator<32> > *m_DatabasePtr; // rcx
  __int64 v12; // rbx
  FEnemyIDBase cEnemyListBase; // [rsp+20h] [rbp-E0h] BYREF

  if ( ADatabaseDefineStatic::m_Self
    && UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1)
    && ADatabaseDefineStatic::m_Self )
  {
    m_EnemyListId = this->m_EnemyListId;
    p_m_DBAccess = &ADatabaseDefineStatic::m_Self->m_EnemyID.m_DBAccess;
    v8 = nullptr;
    Data = nullptr;
    memset(&cEnemyListBase.m_DisplayLevel, 0, 76);
    memset(&cEnemyListBase.m_ResistAilment, 0, 25);
    memset(&cEnemyListBase.m_Exp, 0, 85);
    *(_QWORD *)&cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = 0;
    *(_QWORD *)&cEnemyListBase.m_MaxSpecialSkillValue = 0;
    ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                   (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_EnemyID.m_DBAccess,
                   m_EnemyListId);
    if ( ArrayIndex >= 0 )
    {
      m_DatabasePtr = p_m_DBAccess->m_DatabasePtr;
      if ( m_DatabasePtr )
      {
        if ( ArrayIndex < m_DatabasePtr->ArrayNum )
        {
          v12 = (__int64)&m_DatabasePtr->AllocatorInstance.Data[248 * ArrayIndex];
          cEnemyListBase.m_id = *(_DWORD *)(v12 + 8);
          TArray<unsigned short,TSizedDefaultAllocator<32>>::operator=(
            (TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_DisplayLevel,
            (const TArray<unsigned short,TSizedDefaultAllocator<32> > *)(v12 + 32));
          cEnemyListBase.m_BreakRate = *(_DWORD *)(v12 + 48);
          cEnemyListBase.m_MaxSLD = *(_DWORD *)(v12 + 52);
          cEnemyListBase.m_MaxHP = *(_DWORD *)(v12 + 56);
          cEnemyListBase.m_MaxSP = *(_DWORD *)(v12 + 60);
          cEnemyListBase.m_AtkP = *(_DWORD *)(v12 + 64);
          cEnemyListBase.m_DefP = *(_DWORD *)(v12 + 68);
          cEnemyListBase.m_AtkM = *(_DWORD *)(v12 + 72);
          cEnemyListBase.m_DefM = *(_DWORD *)(v12 + 76);
          cEnemyListBase.m_Agi = *(_DWORD *)(v12 + 80);
          cEnemyListBase.m_Crt = *(_DWORD *)(v12 + 84);
          cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);
          cEnemyListBase.m_Hit = *(_DWORD *)(v12 + 92);
          cEnemyListBase.m_Avd = *(_DWORD *)(v12 + 96);
          cEnemyListBase.m_DamageRate = *(_DWORD *)(v12 + 100);
          cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_ResistAilment,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 112));
          cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);
          cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);
          cEnemyListBase.m_IsBoss = *(_BYTE *)(v12 + 136);
          cEnemyListBase.m_Exp = *(_DWORD *)(v12 + 140);
          cEnemyListBase.m_Money = *(_DWORD *)(v12 + 144);
          cEnemyListBase.m_PetExp = *(_DWORD *)(v12 + 148);
          cEnemyListBase.m_JP = *(_DWORD *)(v12 + 152);
          cEnemyListBase.m_DropReward = *(_DWORD *)(v12 + 156);
          cEnemyListBase.m_StealReward = *(_DWORD *)(v12 + 160);
          cEnemyListBase.m_StealLeafNum = *(_DWORD *)(v12 + 164);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_EventDropRewards,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 168));
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_StatusOffset,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 184));
          cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);
          cEnemyListBase.m_NameUI = *(_BYTE *)(v12 + 204);
          cEnemyListBase.m_LevelUI = *(_BYTE *)(v12 + 205);
          cEnemyListBase.m_HpUI = *(_BYTE *)(v12 + 206);
          cEnemyListBase.m_ShieldUI = *(_BYTE *)(v12 + 207);
          TArray<unsigned int,TSizedDefaultAllocator<32>>::operator=(
            (TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_RaceIndices,
            (const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)(v12 + 208));
          cEnemyListBase.m_DisplaySpecialSkillGauge = *(_BYTE *)(v12 + 224);
          cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = *(_DWORD *)(v12 + 228);
          cEnemyListBase.m_ReduceSpecialSkillValueBreak = *(_DWORD *)(v12 + 232);
          cEnemyListBase.m_MaxSpecialSkillValue = *(_DWORD *)(v12 + 236);
          cEnemyListBase.m_OutsideTarget = *(_BYTE *)(v12 + 240);
          cEnemyListBase.m_NotAction = *(_BYTE *)(v12 + 241);
          cEnemyListBase.m_UnknownLv = *(_BYTE *)(v12 + 242);
          cEnemyListBase.m_Immortal = *(_BYTE *)(v12 + 243);
          if ( cEnemyListBase.m_DisplayLevel.Data.ArrayNum )
            LODWORD(v8) = cEnemyListBase.m_DisplayLevel.Data.ArrayNum - 1;
          FString::AssignRange(
            outLevel,
            (const wchar_t *)cEnemyListBase.m_DisplayLevel.Data.AllocatorInstance.Data,
            (int)v8);
          UEnemyDB::GetEnemyNameString(outName, cEnemyListBase.m_id, 0);
          Data = cEnemyListBase.m_RaceIndices.AllocatorInstance.Data;
          v8 = cEnemyListBase.m_StatusOffset.AllocatorInstance.Data;
```

##### ◆ C-TACT-01：TacticalList/TacticalSkillList 行 解析器 and 技能列表 访问器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17395970-17396040
}

//----- (00000001444DA6E0) ----------------------------------------------------
FScriptContainerElement *__fastcall UTacticalDB::GetTacticalDB(unsigned int TacticalID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    return nullptr;
  v2 = ADatabaseDefineStatic::m_Self;
  if ( ADatabaseDefineStatic::m_Self
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_TacticalList.m_DBAccess,
                       TacticalID),
        ArrayIndex >= 0)
    && ArrayIndex < v2->m_TacticalList.m_DataList.ArrayNum )
  {
    return &v2->m_TacticalList.m_DataList.AllocatorInstance.Data[128 * (__int64)ArrayIndex];
  }
  else
  {
    return nullptr;
  }
}

//----- (00000001444DA760) ----------------------------------------------------
FScriptContainerElement *__fastcall UTacticalDB::GetTacticalSkillDB(unsigned int SkillsID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !UTacticalDB::CheckTacticalDB()
    || !ADatabaseDefineStatic::m_Self
    || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
  {
    return nullptr;
  }
  v2 = ADatabaseDefineStatic::m_Self;
  if ( ADatabaseDefineStatic::m_Self
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_TacticalSkillList.m_DBAccess,
                       SkillsID),
        ArrayIndex >= 0)
    && ArrayIndex < v2->m_TacticalSkillList.m_DataList.ArrayNum )
  {
    return &v2->m_TacticalSkillList.m_DataList.AllocatorInstance.Data[32 * ArrayIndex];
  }
  else
  {
    return nullptr;
  }
}

//----- (00000001444DA7E0) ----------------------------------------------------
const TArray<int,TSizedDefaultAllocator<32> > *__fastcall UTacticalDB::GetTacticalSkillIndices(int SkillsID)
{
  const FTacticalSkillListBase *TacticalSkillDB; // rax
  TArray<int,TSizedDefaultAllocator<32> > *p_m_UseSkills; // rcx

  TacticalSkillDB = USkillDataUtility::GetTacticalSkillDB(SkillsID);
  p_m_UseSkills = &TacticalSkillDB->m_UseSkills;
  if ( !TacticalSkillDB )
    return nullptr;
  return p_m_UseSkills;
}

//----- (00000001444DA800) ----------------------------------------------------
void __fastcall ABattleCameraBase::GetTargetPosition(
        ABattleCameraBase *this,
        UE::Math::TVector<double> *targetPoisition)
```

##### ◆ C-TACT-02：TacticalActionList 行解析器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17395890-17395915
// 1444DA57E: conditional instruction was optimized away because rcx.8==0

//----- (00000001444DA5A0) ----------------------------------------------------
FScriptContainerElement *__fastcall UTacticalDB::GetTacticalActionDB(unsigned int ActionID)
{
  ADatabaseDefineStatic *v2; // rbx
  int ArrayIndex; // eax

  if ( !ADatabaseDefineStatic::m_Self || !UObjectBase::IsValidLowLevelFast(ADatabaseDefineStatic::m_Self, 1) )
    return nullptr;
  v2 = ADatabaseDefineStatic::m_Self;
  if ( ADatabaseDefineStatic::m_Self
    && (ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_TacticalActionList.m_DBAccess,
                       ActionID),
        ArrayIndex >= 0)
    && ArrayIndex < v2->m_TacticalActionList.m_DataList.ArrayNum )
  {
    return &v2->m_TacticalActionList.m_DataList.AllocatorInstance.Data[136 * ArrayIndex];
  }
  else
  {
    return nullptr;
  }
}

```

##### ◆ C-TACT-03：TacticalActionList 条件槽位 读取方

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17387053-17387084
bool __fastcall UBattleTacticalComponent::CheckActionCondition(
        UBattleTacticalComponent *this,
        bool *Interrupted,
        int ActionID,
        int TacticalID)
{
  FScriptContainerElement *TacticalActionDB; // rax
  bool dummy; // [rsp+90h] [rbp+18h] BYREF

  if ( ActionID <= 0 )
    return 0;
  TacticalActionDB = UTacticalDB::GetTacticalActionDB(ActionID);
  if ( !TacticalActionDB )
    return 0;
  if ( TacticalID <= 0 )
    TacticalID = this->m_TacticalID;
  return UBattleTacticalComponent::CheckConditions(
           this,
           Interrupted,
           &dummy,
           (const TArray<int,TSizedDefaultAllocator<32> > *)&TacticalActionDB[40],
           (const TArray<int,TSizedDefaultAllocator<32> > *)&TacticalActionDB[56],
           (const TArray<int,TSizedDefaultAllocator<32> > *)&TacticalActionDB[72],
           (const TArray<int,TSizedDefaultAllocator<32> > *)&TacticalActionDB[88],
           (const TArray<int,TSizedDefaultAllocator<32> > *)&TacticalActionDB[104],
           (const TArray<int,TSizedDefaultAllocator<32> > *)&TacticalActionDB[120],
           TacticalID,
           *(_DWORD *)&TacticalActionDB[16],
           nullptr,
           nullptr);
}

```

##### ◆ C-TACT-04：战术 条件 tuple 求值器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17389408-17389545
bool __fastcall UBattleTacticalComponent::CheckConditions(
        UBattleTacticalComponent *this,
        bool *Interrupted,
        bool *IsIgnoreProvoke,
        const TArray<int,TSizedDefaultAllocator<32> > *Conditions,
        const TArray<int,TSizedDefaultAllocator<32> > *Params,
        const TArray<int,TSizedDefaultAllocator<32> > *AilmentTypes,
        const TArray<int,TSizedDefaultAllocator<32> > *StatusTypes,
        const TArray<int,TSizedDefaultAllocator<32> > *WeaponTypes,
        const TArray<int,TSizedDefaultAllocator<32> > *MagicTypes,
        int TacticalID,
        int SkillIndex,
        TArray<AQPBattleCharacterBase *,TSizedDefaultAllocator<32> > *pEnemies,
        TArray<AQPBattleCharacterBase *,TSizedDefaultAllocator<32> > *pFriendlies)
{
  char v14; // dl
  bool v15; // si
  int v16; // r15d
  FScriptContainerElement *Data; // r14
  int v18; // r13d
  __int64 v19; // r12
  int v20; // ebx
  int m_ConditionFriendlyIndex; // edx
  char TacticalComponent; // di
  AQPBattleCharacterBase *m_Thinker; // r8
  UBattleTacticalComponent *v24; // rcx
  char v25; // al
  FScriptContainerElement *v26; // rax
  FScriptContainerElement *v27; // rcx
  char v28; // al
  bool result; // al
  const UBattleTacticalComponent *pTgt; // [rsp+70h] [rbp-68h] BYREF
  AQPBattleCharacterBase *Friendly; // [rsp+78h] [rbp-60h] BYREF
  FScriptContainerElement *v32; // [rsp+80h] [rbp-58h]
  char v33; // [rsp+E0h] [rbp+8h]
  bool flag; // [rsp+F8h] [rbp+20h] BYREF

  *Interrupted = 0;
  *IsIgnoreProvoke = 0;
  v14 = 0;
  v33 = 0;
  v15 = 0;
  flag = 0;
  this->m_ConditionFriendlyIndex = -1;
  v16 = 0;
  Data = Conditions->AllocatorInstance.Data;
  v18 = 0;
  v32 = &Conditions->AllocatorInstance.Data[4 * Conditions->ArrayNum];
  if ( Data == v32 )
    return 1;
  v19 = 0;
  do
  {
    v20 = *(_DWORD *)Data;
    if ( *(_DWORD *)Data <= 0 )
      break;
    m_ConditionFriendlyIndex = this->m_ConditionFriendlyIndex;
    TacticalComponent = 0;
    pTgt = nullptr;
    if ( m_ConditionFriendlyIndex >= 0 )
    {
      m_Thinker = this->m_Thinker;
      Friendly = nullptr;
      if ( (unsigned __int8)AQPBattleManager::StaticGetFriendly(&Friendly, m_ConditionFriendlyIndex, m_Thinker, 0) )
        TacticalComponent = AQPBattleCharacterBase::GetTacticalComponent(Friendly, (UBattleTacticalComponent **)&pTgt);
    }
    v24 = this;
    if ( TacticalComponent )
      v24 = (UBattleTacticalComponent *)pTgt;
    pTgt = v24;
    if ( v15 )
    {
      this->m_ConditionFriendlyIndex = -1;
      v15 = 0;
    }
    v25 = UBattleTacticalComponent::CheckCondition(
            v24,
            &flag,
            v20,
            *(_DWORD *)&Params->AllocatorInstance.Data[v19],
            (ECHARA_STATUS)*(_BYTE *)&StatusTypes->AllocatorInstance.Data[v19],
            (ESKILL_AILMENT_TYPE)*(_DWORD *)&AilmentTypes->AllocatorInstance.Data[v19],
            *(_BYTE *)&WeaponTypes->AllocatorInstance.Data[v19],
            (EMAGIC_TYPE)*(_BYTE *)&MagicTypes->AllocatorInstance.Data[v19],
            TacticalID,
            SkillIndex,
            nullptr,
            (TArray<TObjectPtr<UWorldMetricsExtension>,TSizedDefaultAllocator<32> > *)pEnemies,
            pFriendlies);
    v14 = v33;
    if ( v25 )
    {
      v26 = ABattleConditionStatic::m_DataList.AllocatorInstance.Data;
      ++v18;
      v14 = flag | v33;
      v15 = this->m_ConditionFriendlyIndex >= 0;
      v27 = &ABattleConditionStatic::m_DataList.AllocatorInstance.Data[16 * ABattleConditionStatic::m_DataList.ArrayNum];
      v33 |= flag;
      if ( ABattleConditionStatic::m_DataList.AllocatorInstance.Data == v27 )
        goto LABEL_15;
      while ( *(_DWORD *)&v26[8] != v20 )
      {
        v26 += 16;
        if ( v26 == v27 )
          goto LABEL_15;
      }
      if ( v26 && *(_BYTE *)&v26[14] )
        v28 = 1;
      else
LABEL_15:
        v28 = 0;
      *IsIgnoreProvoke |= v28;
    }
    ++v16;
    v19 += 4;
    Data += 4;
  }
  while ( Data != v32 );
  if ( v16 <= 0 )
    return 1;
  result = v18 == v16;
  if ( v18 == v16 )
    *Interrupted = v14;
  return result;
}

//----- (00000001444D3350) ----------------------------------------------------
__int64 __fastcall UBattleUtility::CheckConditions(
        const TArray<int,TSizedDefaultAllocator<32> > *Conditions,
        const TArray<int,TSizedDefaultAllocator<32> > *Params,
        const TArray<int,TSizedDefaultAllocator<32> > *Statuses,
        const TArray<int,TSizedDefaultAllocator<32> > *Ailments,
        const TArray<int,TSizedDefaultAllocator<32> > *Weapons,
        const TArray<int,TSizedDefaultAllocator<32> > *Magics,
        const TArray<int,TSizedDefaultAllocator<32> > *EnemyID,
        const TArray<int,TSizedDefaultAllocator<32> > *EquipID,
        AQPBattleCharacterBase *Enforcer,
        AQPBattleCharacterBase *Target,
```

##### ◆ C-TACT-05：TacticalActionList 技能索引 与 SelectType 语义 in 候选 decision

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17393856-17393884
      v29->ArrayNum = v16;
      v6 = this;
    }
  }
  TacticalActionDB = UTacticalDB::GetTacticalActionDB(v12);
  v24 = TacticalActionDB;
  if ( !TacticalActionDB )
    goto LABEL_37;
  v25 = *(int *)&TacticalActionDB[16];
  m_pSkillIDs = v6->m_pSkillIDs;
  if ( (int)v25 >= 0 && (int)v25 < m_pSkillIDs->ArrayNum && (int)v25 < v6->m_MaxSkillIDs )
  {
    v11 = *(_DWORD *)&v24[20];
    v9 = *(_DWORD *)&m_pSkillIDs->AllocatorInstance.Data[4 * v25];
  }
  if ( *(_BYTE *)&v24[24] == 5 )
  {
    v6->m_RecommendSkillIndex = *(_DWORD *)&v24[28];
  }
  else if ( *(_BYTE *)&v24[24] == 6 )
  {
    v6->m_NoRecommendSkillIndex = *(_DWORD *)&v24[28];
  }
  else
  {
    *(_QWORD *)&v6->m_RecommendSkillIndex = -1;
  }
  if ( v9 > 0 )
  {
```

##### ◆ C-TACT-06：TacticalActionList 推荐 过滤器

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17393345-17393365
    {
      v62 = ADatabaseDefineStatic::m_Self;
      if ( ADatabaseDefineStatic::m_Self )
      {
        ArrayIndex = FDbAccessHelper<FBattleVoiceArenaListBase>::GetArrayIndex(
                       (FDbAccessHelper<FNoteListBase> *)&ADatabaseDefineStatic::m_Self->m_TacticalActionList.m_DBAccess,
                       v61);
        if ( ArrayIndex < 0 || ArrayIndex >= v62->m_TacticalActionList.m_DataList.ArrayNum )
          goto LABEL_104;
        v60 = (__int64)&v62->m_TacticalActionList.m_DataList.AllocatorInstance.Data[136 * ArrayIndex];
      }
      if ( v60 )
      {
        m_RecommendSkillIndex = this->m_RecommendSkillIndex;
        if ( m_RecommendSkillIndex < 0 || *(_DWORD *)(v60 + 16) == m_RecommendSkillIndex )
        {
          m_NoRecommendSkillIndex = this->m_NoRecommendSkillIndex;
          if ( m_NoRecommendSkillIndex < 0 || *(_DWORD *)(v60 + 16) != m_NoRecommendSkillIndex )
          {
            v66 = tmp.ArrayNum;
            if ( ++tmp.ArrayNum > (unsigned int)tmp.ArrayMax )
```

##### ◆ C-TACT-07：顺序预兆/行动对群组与回合字段的读取

```cpp
// Octopath_Traveler0-Win64-Shipping.exe.c:17395360-17395415
  v8 = -1;
  v9 = -1;
  if ( TacticalID > 0 )
  {
    TacticalActions = UTacticalDB::GetTacticalActions(TacticalID);
    if ( TacticalActions )
    {
      v11 = *(unsigned int **)TacticalActions;
      v27 = *(_QWORD *)TacticalActions + 4LL * *(int *)&TacticalActions[8];
      if ( *(_QWORD *)TacticalActions != v27 )
      {
        do
        {
          v12 = *v11;
          if ( (int)*v11 <= 0 )
            break;
          TacticalActionDB = UTacticalDB::GetTacticalActionDB(v12);
          if ( !TacticalActionDB || *(_BYTE *)&TacticalActionDB[24] != 4 )
            break;
          v14 = 0;
          v15 = UTacticalDB::GetTacticalActionDB(v12);
          if ( v15 )
            v14 = *(_DWORD *)&v15[32];
          v16 = v9 == v14;
          v17 = v8 + 1;
          v18 = v14;
          v19 = 0;
          if ( v16 )
          {
            v17 = v8;
            v18 = v9;
          }
          v8 = v17;
          if ( this->m_IsOrderedActionMode )
          {
            v20 = UTacticalDB::GetTacticalActionDB(v12);
            if ( v20 )
            {
              if ( *(_BYTE *)&v20[24] == 4 )
              {
                v21 = UTacticalDB::GetTacticalActionDB(v12);
                if ( v21 )
                  v19 = *(_DWORD *)&v21[36];
              }
            }
          }
          if ( v19 < 0 )
          {
            v22 = UTacticalDB::GetTacticalActionDB(v12);
            if ( v22 )
            {
              v23 = *(int *)&v22[16];
              m_pSkillIDs = this->m_pSkillIDs;
              if ( (int)v23 >= 0 && (int)v23 < m_pSkillIDs->ArrayNum && v8 >= SkipIndex )
                return *(unsigned int *)&m_pSkillIDs->AllocatorInstance.Data[4 * v23];
            }
```

【字段级解释】

| 键 | 运行时 C 位置 | 解释 | 状态 | 证据 |
|---|---|---|---|---|
| `m_AilmentTypes` | `+0x48`（数组）；引用块中没有保留下更短的具名赋值 | 动作 行从行 `+0x28` 开始存储 条件/过滤 槽位；`CheckActionCondition` 将这六个并行数组传给 战术 条件 求值器。 | 已复原 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_Conditions` | `+0x28`（数组）；引用块中没有保留下更短的具名赋值 | 动作 行从行 `+0x28` 开始存储 条件/过滤 槽位；`CheckActionCondition` 将这六个并行数组传给 战术 条件 求值器。 | 已复原 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_FriendlyIndex` | `+0x14`（标量）；引用块中没有保留下更短的具名赋值 | 动作 行在 候选 decision 期间把行 `+0x14` 读入本地 选择器。Hex-Rays 在这里没有保留 DB 字段名；其角色是 target/friendly 选择器 槽位，而不是 技能 ID。 | 已复原 | `C-TACT-05` |
| `m_GroupIndex` | `+0x20`（标量）；引用块中没有保留下更短的具名赋值 | Sequential-presage 逻辑读取行 `+0x20`，并将其与先前看到的 群组 值比较；这是对 动作 grouping 槽位最强的已复原解释。 | 已复原 | `C-TACT-07` |
| `m_MagicTypes` | `+0x78`（数组）；引用块中没有保留下更短的具名赋值 | 动作 行从行 `+0x28` 开始存储 条件/过滤 槽位；`CheckActionCondition` 将这六个并行数组传给 战术 条件 求值器。 | 已复原 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_NoRecommendSkillIndex` | `C-TACT-05:17393877` `v6->m_NoRecommendSkillIndex = *(_DWORD *)&v24[28];` | 这是运行时 component 字段，而不是 DB 键。当 `SelectType == 6` 时，它由 `TacticalActionList +0x1C` 设置；之后 候选 collection 会拒绝 `m_SkillIndex` 等于它的 动作。 | cross-reference | `C-TACT-05`, `C-TACT-06` |
| `m_Params` | `+0x38`（数组）；引用块中没有保留下更短的具名赋值 | 动作 行从行 `+0x28` 开始存储 条件/过滤 槽位；`CheckActionCondition` 将这六个并行数组传给 战术 条件 求值器。 | 已复原 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_PrioritySkill` | `+0x1C`（标量）；引用块中没有保留下更短的具名赋值 | 动作 行在 `SelectType` 为 `5` 或 `6` 时读取行 `+0x1C`；该值会写入 `m_RecommendSkillIndex` 或 `m_NoRecommendSkillIndex`。除这一 推荐 operand 外，导出 并未证明存在单独的 priority mechanism。 | 否定性/部分复原 | `C-TACT-05`, `C-TACT-06` |
| `m_RecommendSkillIndex` | `C-TACT-05:17393873` `v6->m_RecommendSkillIndex = *(_DWORD *)&v24[28];`<br>`C-TACT-05:17393881` `*(_QWORD *)&v6->m_RecommendSkillIndex = -1;` | 这是运行时 component 字段，而不是 DB 键。当 `SelectType == 5` 时，它由 `TacticalActionList +0x1C` 设置；之后 候选 collection 只保留 `m_SkillIndex` 等于它的 动作。 | cross-reference | `C-TACT-05`, `C-TACT-06` |
| `m_SelectType` | `+0x18`（字节）；引用块中没有保留下更短的具名赋值 | 动作 行把行 `+0x18` 读作 字节 选择 模式。已复原的 branches 将它与 `4` 比较以处理 sequential behavior，并与 `5`/`6` 比较以处理 recommend/no-recommend filtering。 | 已复原 | `C-TACT-05`, `C-TACT-07` |
| `m_SkillIndex` | `+0x10`（标量）；引用块中没有保留下更短的具名赋值 | 动作 行把行 `+0x10` 读作当前敌人 `m_pSkillIDs`/`TacticalSkillList.m_UseSkills` 数组的 索引。它不是直接 `SkillID`。 | 已复原 | `C-TACT-05`, `C-TACT-01` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | `m_SkillsID` 不是 `TacticalActionList` 字段。它从 `EnemyID +0x84` 读取，解析 `TacticalSkillList` 行，并提供由 `m_SkillIndex` 索引的 `m_UseSkills` 数组。 | cross-reference | `C-ENEMY-02`, `C-TACT-01`, `C-TACT-05` |
| `m_StatusTypes` | `+0x58`（数组）；引用块中没有保留下更短的具名赋值 | 动作 行从行 `+0x28` 开始存储 条件/过滤 槽位；`CheckActionCondition` 将这六个并行数组传给 战术 条件 求值器。 | 已复原 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_TurnCount` | `+0x24`（标量）；引用块中没有保留下更短的具名赋值 | Ordered/sequential 逻辑把行 `+0x24` 读作额外 cadence/order operand。准确字段名未保留下来，但该行槽位会在 sequential presage/动作 选择 中被消费。 | 已复原 | `C-TACT-07` |
| `m_WeaponTypes` | `+0x68`（数组）；引用块中没有保留下更短的具名赋值 | 动作 行从行 `+0x28` 开始存储 条件/过滤 槽位；`CheckActionCondition` 将这六个并行数组传给 战术 条件 求值器。 | 已复原 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
