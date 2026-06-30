### **LANGUAGE:**  [English](customenms_en.md) | [日本語](customenms_jp.md) | [中文](customenms_zh.md) | [한국어](customenms_kr.md)

> Runtime evidence convention in this guide: `Direct consumer` means gameplay/UI code reads the field for an operation; `row-layout proof` means a getter or materializer establishes offset/shape; `materialization-only` means the field is copied into a runtime object but no later semantic consumer was recovered in the cited C excerpt.

The process of adding new enemies and battles to Octopath Traveler 0 primarily revolves around editing DataBase files under `AIBattle`, `Enemy` and possibly `Skill`, `Texture` as well as `Battle`. Below an explanation of all the relevant files, headlines are marked with a number of stars depending on how necessary these files are to edit (fewer stars = used in more specialized contexts) but it's recommended to read the entire article anyway to know which files to modify for what purposes:

## Under `Enemy/`
### (***) `EnemyFormations`
Allows you to specify new Enemy Formations on the field, `m_PositionID` takes `BattlePositions.m_id` as input (`BattlePositions` can be found under `Battle/`). For custom battles with many enemies, you may want to look at these files to find an appropriate formation with enough non-zero `m_PositionID` entries or make your own. Some `PositionID`s may produce wildly different enemy positions depending on the battle map.
`Kingship_structs.hpp` assigns the following labels to OT0's formations
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
#### Runtime reading semantics
In this subsection, each key mentioned above is given a field-level runtime locus: either an exact named C assignment/read, an unambiguous row-offset lane, or a negative recovery statement when the C export does not preserve the key as a live field. Interpretations are deliberately limited to what the cited C permits.

**Primary C evidence:**

##### C-FORM-01: EnemyFormations row resolver

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

##### C-FORM-02: Formation position resolution into BattlePositions

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_PositionID` | `+0x10` (array); no shorter named assignment survived in the cited block | The formation row is resolved by `FormID`; the game then indexes the `TArray<int>` at row `+0x10` and treats the selected element as a `BattlePositions` row id. The returned battle-position row supplies XYZ coordinates and priority. | recovered | `C-FORM-01`, `C-FORM-02` |

### (***) `EnemyBattleAnimSet`
Where CotC assigns to enemies an ID referring to a specific Flipbook asset, OT0 uses these sets of IDs, each referring to such an asset (`m_EnemyTextureIDs` taking the `m_id`s of `EnemyTexID` under `Textures` as input).
`m_EnemyTextureIDs[0]` **typically** refers to the sprite's idle animation (suffix `Idl00`), whereas `m_EnemyTextureIDs[1]` to `m_EnemyTextureIDs[3]` would **typically** be the sprite's attack animations (suffixes `Atk00` to `Atk02`). You will have to add a flipbook asset for at least the idle animation (i.e. referencing a new path under `EnemyTexID` and using that new entry's `m_id` under `m_EnemyTextureIDs[0]` in your newly added entry there), everything else is optional.

As mentioned, these indices only **typically** resolve to Idl00, Atk00, etc., there is no runtime code that forces these indices to resolve to those: `EnemyTypeID.m_AnimationSetIDs[index]` first selects an `EnemyBattleAnimSet` row; the selected row's `m_EnemyTextureIDs` are then scanned or copied as `EnemyTexID` resource ids. Action dispatch is obtained by asking the `EnemyTexID` table for each texture's `EBATTLE_ACTION_ID`. Therefore, the array order is semantically a **priority/tie-break order**, **not** an **action-code namespace**.

Please note that CotC's `m_FlipbookID` under `EnemyType` appears to use these extra array components differently: while index 0 still refers to the idle texture, CotC assigns to some enemies an invisible texture in index 1 (e.g. the Shadow from Shadow of Twin Worlds), this index 1 texture can overwrite the index 0 one, especially if also read as an idle texture.

Flipbook assets may assume the following scales (see `Kingship_structs.hpp`):

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


Fights which use NPC textures do **not** need an extra entry to be made under `EnemyBattleAnimSet`, as NPCs typically already have their own in-battle spritesheets (often ending with the `_B` suffix). Simply specify the NPC's `CharaID` under `EnemyType` (see below and possibly [How to add custom characters](customchars_en.md)). The runtime fallback path confirms this: if enemy flipbooks cannot be loaded for a normal enemy type, `SetupEnemyFlipbook` may instead try `EnemyTypeID.m_NpcCharaID` for that texture index.

#### Runtime reading semantics (field-level Hex-Rays evidence)
`FEnemyBattleAnimSetBase` is a 32-byte row. Its recovered copy operator copies only the row id and a single `TArray<int>`-sized member. All later battle-side consumers agree with this layout: the row is keyed by `m_id`, and the array at `+0x10` is interpreted as `m_EnemyTextureIDs`.

**Primary C evidence:**

##### C-ANIM-01: recovered struct size and copied fields

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

**Interpretation.** The decompiler's `TArray<FPackedNormal>` type is a template artifact, but the memory operations are not ambiguous. The row copy writes a 32-bit scalar at offset `+0x08` and then copies a `TArray<unsigned int>` beginning at `+0x10`. In the named runtime consumers below, the scalar is `m_id` and the array is `m_EnemyTextureIDs`.

##### C-ANIM-02: direct `m_id` lookup, 32-byte row stride

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

**Interpretation.** `EnemyBattleAnimSet` is resolved by equality on `m_id`; there is no label lookup in this path. The end pointer uses `32 * ArrayNum`, so the row stride is precisely 32 bytes.

##### C-ANIM-03: `EnemyTypeID.m_AnimationSetIDs[index]` selects the animation-set row

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

**Interpretation.** The `index`/`TextureIdx` argument selects an entry from `EnemyTypeID.m_AnimationSetIDs` (`+0x10` pointer and `+0x18` count). It is not an index into `m_EnemyTextureIDs`. Modding implication: a type can have multiple animation-set references, and the outer slot is determined by `EnemyTypeID`, while the inner texture ids live in `EnemyBattleAnimSet`.

##### C-ANIM-04: number of animation-set slots is the `EnemyTypeID.m_AnimationSetIDs` count

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

**Interpretation.** Normal enemy initialization loops over this count and calls `SetupEnemyFlipbook` once per animation-set slot. This further separates `EnemyTypeID.m_AnimationSetIDs` from `EnemyBattleAnimSet.m_EnemyTextureIDs`.

##### C-ANIM-05: bulk retrieval copies `m_EnemyTextureIDs` without semantic indexing

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

**Interpretation.** This path treats `m_EnemyTextureIDs` as a raw array of resource ids. No action meaning is attached to array indices in this copy path.

##### C-ANIM-06: action-specific lookup is a first-match scan over `EnemyTexID` action metadata

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

**Interpretation.** `m_EnemyTextureIDs` order is significant only as a first-match priority. The requested action is matched against `EnemyTexID.m_ActionTypeID` recovered by `AResourceManager::GetEnemyTexActionID`; it is not matched against the array index.

##### C-ANIM-07: `EnemyTexID` supplies the action id and unique-material flag

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

**Interpretation.** `EnemyTexID` rows have a recovered stride of 40 bytes here. The action id is read at row `+0x10`; the unique-material flag is read at row `+0x14`. Thus `EnemyBattleAnimSet` does not itself encode idle/attack semantics; it references `EnemyTexID` rows that do.

##### C-ANIM-08: loading path constructs an action-keyed flipbook set

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

**Interpretation.** Both ordinary enemy types and enemy parts converge on the same flipbook construction path. A resource category `2` object is loaded for each `EnemyTexID`, then inserted into a flipbook set keyed by `EBATTLE_ACTION_ID`. Part type ids are distinguished by adding/subtracting the game parameter `0x1B66`.

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_id` | `result->m_id != AnimSetID`; row stride `32` | Primary runtime key for `EnemyBattleAnimSet`. Lookups linearly scan the loaded data list by id. | recovered | `C-ANIM-01`, `C-ANIM-02` |
| `m_EnemyTextureIDs` | `m_EnemyTextureIDs.ArrayNum`, `.AllocatorInstance.Data`; row offset `+0x10` | `TArray<int>` of `EnemyTexID.m_id` values. Bulk paths copy it as raw ids; action paths scan it and use `EnemyTexID` metadata to determine action semantics. | recovered | `C-ANIM-05`, `C-ANIM-06`, `C-ANIM-07`, `C-ANIM-08` |
| `TextureIdx` / animation-set slot | `EnemyTypeID` `+0x10/+0x18`, `GetEnemyTypeFlipbookSetNum` count | Selects an element of `EnemyTypeID.m_AnimationSetIDs`, then resolves that value as `EnemyBattleAnimSet.m_id`. | recovered | `C-ANIM-03`, `C-ANIM-04` |

### (**) `EnemyParts`
If your custom boss consists of several visual/mechanical parts (e.g. Or'Galdera, Amatsukami no Orochi), specify each part in this file. Some bosses, such as Shadow of Twin Worlds, do not use this file at all. The C export shows that `EnemyParts` is not an independent enemy master table. A part is instantiated as an `AQPBattleCharacterBase` subordinate to an owner enemy, using a parts row selected from `EnemyTypeID.m_PartsID`.

At runtime, an owner enemy's `EnemyTypeID.m_PartsID` array is read during enemy construction. Each positive part id is resolved through `UEnemyDB::GetEnemyPartsDB`; if the row exists, the battle manager creates a battle character for the part, assigns it a `PartsIndex`, links it back to the owner via `m_PartsOwner`, and registers it in the owner/manager parts containers. The part's sprite is then loaded through the same `EnemyBattleAnimSet` machinery described above, but with the part id mapped into a special type-id namespace by adding `UGameParamsUtility::GetParamInt(0x1B66)`.
#### Runtime reading semantics
`FEnemyPartsBase` is a 56-byte row. The export preserves only some semantic names, so this section uses row offsets where the C does not preserve the original field identifier. The important point for modding is that these offsets are not inert padding: the engine reads them for animation selection, placement, initial visibility, break/selection visual propagation, conditional display changes, and fade behavior.

**Primary C evidence:**

##### C-PARTS-01: recovered row size and row-member copy order

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

**Interpretation.** The row stride is 56 bytes. The copied lanes are: `+0x08` id, an overlapping 8-byte pair at `+0x0C`, scalar lanes at `+0x14`, `+0x18`, `+0x1C`, `+0x20`, bytes at `+0x24`, `+0x25`, `+0x26`, and scalar lanes at `+0x28`, `+0x2C`, `+0x30`. Later consumers assign meanings to those lanes.

##### C-PARTS-02: keyed lookup through `m_EnemyParts.m_DBAccess`

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

**Interpretation.** `PartsID` is resolved by the table's DB access helper. It is not a dense array index unless the DB helper maps it that way. The row is returned only when the table is loaded and the key resolves in range.

##### C-PARTS-03: `+0x14` is an `EnemyBattleAnimSet.m_id` reference

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

**Interpretation.** Row offset `+0x14` is not a texture id. It is passed to `GetFlipbookSet`, so it is an `EnemyBattleAnimSet.m_id`. This is the part's animation-set reference.

##### C-PARTS-04: owner `EnemyTypeID.m_PartsID` enumerates parts

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

**Interpretation.** The owner type row supplies a `TArray<int>` at `+0xC0/+0xC8`, which corresponds to `EnemyTypeID.m_PartsID`. Each nonzero entry is a part id; the loop index is passed as `PartsIndex`. A part is therefore subordinate to a specific owner/type construction path, not a separate encounter member.

##### C-PARTS-05: row offsets `+0x18/+0x1C/+0x20` affect part position

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

**Interpretation.** Offsets `+0x18` and `+0x1C` are read as floats; offset `+0x20` is read as an integer and scaled by `-0.1`. The two call sites differ in local vector naming, but both consume the same three row lanes to reposition the part relative to an owner-derived position.

##### C-PARTS-06: setup links the part to its owner and reads initial visibility

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

**Interpretation.** Offset `+0x24` is the initial visible flag. `m_PartsOwner`, `m_PartsID`, and `m_PartsIndex` are runtime state fields on the spawned character, not data-table fields. The `PartsID + ParamInt` transform is the discriminator used by flipbook loading to route part ids through `GetEnemyPartsFlipbookID`.

##### C-PARTS-07: conditional display on/off uses row offsets `+0x28` and `+0x2C`

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

**Interpretation.** `+0x28` is a display-on condition id, evaluated only when the part is hidden. `+0x2C` is a display-off condition id, evaluated only when the part is visible. Both are resolved as battle-abort/condition bundles and evaluated by `UBattleUtility::CheckConditions`; they are not raw booleans.

##### C-PARTS-08: fade/dither behavior reads row offset `+0x30`

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

**Interpretation.** Offset `+0x30` is read as a float controlling fade behavior. Non-positive values allow immediate hiding; positive values route visibility changes through a dither fade request.

##### C-PARTS-09: break/select visual propagation uses bytes `+0x25` and `+0x26`

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

**Interpretation.** Byte `+0x25` gates a play-rate/break-animation linkage path when byte `+0x26` is not set. Byte `+0x26` gates part-specific shield-break and selection-material behavior. These bytes are directly consumed by battle-state visual propagation.

**Recovered row-offset map:**

| Row locus | Runtime interpretation | Evidence |
|---|---|---|
| `+0x08` | `EnemyParts.m_id`, resolved by `m_EnemyParts.m_DBAccess`. | `C-PARTS-01`, `C-PARTS-02` |
| `+0x0C/+0x10` | Copied as an 8-byte lane, but not assigned a safe semantic name by the cited gameplay consumers. | `C-PARTS-01` |
| `+0x14` | Animation-set id; passed to `GetFlipbookSet`, so it references `EnemyBattleAnimSet.m_id`. | `C-PARTS-03` |
| `+0x18` | Spatial float applied to part placement. | `C-PARTS-05` |
| `+0x1C` | Spatial float applied to part placement. | `C-PARTS-05` |
| `+0x20` | Spatial integer scaled by `-0.1` and applied to part placement. | `C-PARTS-05` |
| `+0x24` | Initial animation-visible flag. | `C-PARTS-06` |
| `+0x25` | Break/play-rate linkage flag. | `C-PARTS-09` |
| `+0x26` | Shield-break/select-material linkage flag. | `C-PARTS-09` |
| `+0x28` | Display-on condition id, evaluated only while hidden. | `C-PARTS-07` |
| `+0x2C` | Display-off condition id, evaluated only while visible. | `C-PARTS-07` |
| `+0x30` | Fade/dither float for visibility changes. | `C-PARTS-08` |

### (****) `EnemyTypeID`
This asset is basically OT0's master "battle sprite presentation descriptor" for an enemy archetype, it tells the game:
- which animation sets to use or alternatively NPC/character spritesheet to associate (`m_AnimationSetIDs` taking the `m_id`s of `EnemyBattleAnimSet` as input, `m_NpcCharaID` taking the `m_id`s of `CharaID` under `Character/` as input)
- how to scale and position the sprite and all overlays:
  - hit sparks (`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`)
  - shield and weakness overlay
  - enemy effects (`m_EffectOffsetX`,`m_EffectOffsetY`, and `m_EffectFromBottomOffsetY`: offset relative to the bottom of the sprite, common for ground-based effects)
  - damage numbers (`m_DamageOffsetX`,`m_DamageOffsetY`)
  - ailments (`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`)
  - cursor (`m_CursorOffsetX`,`m_CursorOffsetY`)
- which enemy parts to associate with that enemy archetype (`m_PartsID`)
- which sounds/voices to use for that enemy type:
  - `m_CallSE` (i.e. "call sound effect")
  - `m_VoiceSymbol` (an integer that likely indexes into `VoiceSymbolList` under `Sound`)
  - `m_CueSheet` (likely indexes into `SoundSheetList` under `Sound/` and selects which cuesheet to use for this enemy's voice set)
- which attach effects (VFX) should be spawned and where (`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`)
- which position of the referenced texture (usually first frame of the flipbook asset/character spritesheet) should be used for the turn order icons (`m_OrderTexUS`, `m_OrderTexVS`; for large icons, i.e. when the specified character is taking their action, `m_OrderTexUL` and `m_OrderTexVL` are used) and at what scale the referenced texture should render in the order icon (`m_OrderScaleS`; `m_OrderScaleL`). For example, the Shadow in the Shadow of Twin Worlds fight uses a very small order scale to ensure its entire body appears on the order icon.
#### Additional runtime-relevant fields
  - `m_OverrideOrderIconAnimSetID`: in case you want to use special texture assets for the order icon
  - `m_AttachEffectInGodbeastProduction`: materialized as row byte `+0xA5`; no direct gameplay/UI consumer is recovered in the cited C paths, so its semantic role remains weaker than the battle/field attach-effect fields.
  - `m_AddStartUV`: materialized as row byte `+0xA4`; no direct consumer is recovered in the cited C paths, so treat it as a live field with unresolved semantics.
  - `m_KeepVisibleAttachEffectOnMotion`: materialized as part of `FEnemyTypeIDBase` and recovered as row byte `+0xA6`; the later `EnemyTypeID` offset map classifies it separately from `m_AddStartUV` and `m_AttachEffectInGodbeastProduction`. Treat the exact visual behavior as a runtime field with partial consumer evidence, not as exporter noise.
  - `m_LinkOwnerAnim`: materialized as row byte `+0xD0`; the C export proves it survives into the `EnemyTypeID` row, but the exact downstream consumer remains only partially recovered.
  - `m_DefeatEffect`: Effect ID to be played on death (e.g. Bestower of All's special defeat effect). `Kingship_structs.hpp` specifies them as follows:

```hpp
enum class EENEMY_DEFEATEFFECT : uint8
{
	NONE                                     = 0,
	EDE_AGM_CHP8_02                          = 1,
	EDE_AGM_CHP8_03_RING                     = 2,
	EENEMY_MAX                               = 3,
};
```

       
- `m_ChangeColorID`: an ID into a color-change table, might be related to `CharaColorChangeParams` under `Character/`. Is only used in OT0 when Zero/the Ringbearer Chosen faces off against his shadow (`ChangeColorID` 6). CotC's `EnemyType` lacks that key entirely. The SDK contains the following possibly related enum:

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
#### Runtime reading semantics (field-level Hex-Rays evidence)
##### C-TYPE-01: EnemyTypeID row resolver

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

##### C-TYPE-02: EnemyTypeID materialized field copy

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

##### C-TYPE-03: EnemyID to EnemyTypeID and selected EnemyTypeID consumers

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_AddStartUV` | `C-TYPE-02:17041883` `this->m_AddStartUV = __that->m_AddStartUV;` | The key is read as a byte/Boolean lane at row offset `+0xA4`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentInfoOffsetX` | `C-TYPE-02:17041865` `this->m_AilmentInfoOffsetX = __that->m_AilmentInfoOffsetX;` | The key is read as a 32-bit float at row offset `+0x5C`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentOffset` | `C-TYPE-02:17041866` `this->m_AilmentOffset = __that->m_AilmentOffset;` | The key is read as a 32-bit float at row offset `+0x60`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_AnimationSetIDs` | `C-TYPE-02:17041822` `this->m_AnimationSetIDs.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041823` `ArrayNum = __that->m_AnimationSetIDs.ArrayNum;`<br>`C-TYPE-02:17041824` `Data = __that->m_AnimationSetIDs.AllocatorInstance.Data;` plus 4 further local occurrences | The key is read as a `TArray` header at row offset `+0x10`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffect` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;`<br>`C-TYPE-02:17041886` `this->m_AttachEffect = __that->m_AttachEffect;` | The key is read as a 32-bit scalar at row offset `+0xA8`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffectInGodbeastProduction` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;` | The key is read as a byte/Boolean lane at row offset `+0xA5`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetX` | `C-TYPE-02:17041887` `this->m_BattleAttachEffectOffsetX = __that->m_BattleAttachEffectOffsetX;` | The key is read as a 32-bit float at row offset `+0xAC`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetY` | `C-TYPE-02:17041888` `this->m_BattleAttachEffectOffsetY = __that->m_BattleAttachEffectOffsetY;` | The key is read as a 32-bit float at row offset `+0xB0`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | The key is read as a 32-bit scalar at row offset `+0x7C`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | The key is read as a 32-bit scalar at row offset `+0x30`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_CueSheet` | `C-TYPE-02:17041875` `this->m_CueSheet = __that->m_CueSheet;` | The key is read as a 32-bit scalar at row offset `+0x84`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetX` | `C-TYPE-02:17041869` `this->m_CursorOffsetX = __that->m_CursorOffsetX;` | The key is read as a 32-bit float at row offset `+0x6C`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetY` | `C-TYPE-02:17041870` `this->m_CursorOffsetY = __that->m_CursorOffsetY;` | The key is read as a 32-bit float at row offset `+0x70`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetX` | `C-TYPE-02:17041867` `this->m_DamageOffsetX = __that->m_DamageOffsetX;` | The key is read as a 32-bit float at row offset `+0x64`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetY` | `C-TYPE-02:17041868` `this->m_DamageOffsetY = __that->m_DamageOffsetY;` | The key is read as a 32-bit float at row offset `+0x68`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_DefeatEffect` | `C-TYPE-02:17041909` `this->m_DefeatEffect = __that->m_DefeatEffect;` | The key is read as a 32-bit scalar at row offset `+0xD4`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectFromBottomOffsetY` | `C-TYPE-02:17041864` `this->m_EffectFromBottomOffsetY = __that->m_EffectFromBottomOffsetY;` | The key is read as a 32-bit float at row offset `+0x58`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetX` | `C-TYPE-02:17041862` `this->m_EffectOffsetX = __that->m_EffectOffsetX;` | The key is read as a 32-bit float at row offset `+0x50`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetY` | `C-TYPE-02:17041863` `this->m_EffectOffsetY = __that->m_EffectOffsetY;` | The key is read as a 32-bit float at row offset `+0x54`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffect` | `C-TYPE-02:17041889` `this->m_FieldAttachEffect = __that->m_FieldAttachEffect;`<br>`C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;`<br>`C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | The key is read as a 32-bit scalar at row offset `+0xB4`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetX` | `C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;` | The key is read as a 32-bit float at row offset `+0xB8`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetY` | `C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | The key is read as a 32-bit float at row offset `+0xBC`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosX` | `C-TYPE-02:17041857` `this->m_HitPosX = __that->m_HitPosX;` | The key is read as a 32-bit float at row offset `+0x3C`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosY` | `C-TYPE-02:17041858` `this->m_HitPosY = __that->m_HitPosY;` | The key is read as a 32-bit float at row offset `+0x40`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleX` | `C-TYPE-02:17041859` `this->m_HitScaleX = __that->m_HitScaleX;` | The key is read as a 32-bit float at row offset `+0x44`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleY` | `C-TYPE-02:17041860` `this->m_HitScaleY = __that->m_HitScaleY;` | The key is read as a 32-bit float at row offset `+0x48`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_KeepVisibleAttachEffectOnMotion` | `C-TYPE-02:17041885` `this->m_KeepVisibleAttachEffectOnMotion = __that->m_KeepVisibleAttachEffectOnMotion;` | The key is read as a byte/Boolean lane at row offset `+0xA6`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_LinkOwnerAnim` | `C-TYPE-02:17041908` `this->m_LinkOwnerAnim = __that->m_LinkOwnerAnim;` | The key is read as a byte/Boolean lane at row offset `+0xD0`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_NpcCharaID` | `C-TYPE-02:17041838` `this->m_NpcCharaID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041839` `v6 = __that->m_NpcCharaID.ArrayNum;`<br>`C-TYPE-02:17041840` `v7 = __that->m_NpcCharaID.AllocatorInstance.Data;` plus 4 further local occurrences | The key is read as a `TArray` header at row offset `+0x20`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleL` | `C-TYPE-02:17041882` `this->m_OrderScaleL = __that->m_OrderScaleL;` | The key is read as a 32-bit float at row offset `+0xA0`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleS` | `C-TYPE-02:17041879` `this->m_OrderScaleS = __that->m_OrderScaleS;` | The key is read as a 32-bit float at row offset `+0x94`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUL` | `C-TYPE-02:17041880` `this->m_OrderTexUL = __that->m_OrderTexUL;` | The key is read as a 32-bit scalar at row offset `+0x98`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUS` | `C-TYPE-02:17041877` `this->m_OrderTexUS = __that->m_OrderTexUS;` | The key is read as a 32-bit scalar at row offset `+0x8C`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVL` | `C-TYPE-02:17041881` `this->m_OrderTexVL = __that->m_OrderTexVL;` | The key is read as a 32-bit scalar at row offset `+0x9C`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVS` | `C-TYPE-02:17041878` `this->m_OrderTexVS = __that->m_OrderTexVS;` | The key is read as a 32-bit scalar at row offset `+0x90`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_OverrideOrderIconAnimSetID` | `C-TYPE-02:17041876` `this->m_OverrideOrderIconAnimSetID = __that->m_OverrideOrderIconAnimSetID;` | The key is read as a 32-bit scalar at row offset `+0x88`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_PartsID` | `C-TYPE-02:17041892` `this->m_PartsID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041893` `v8 = __that->m_PartsID.ArrayNum;`<br>`C-TYPE-02:17041894` `v9 = __that->m_PartsID.AllocatorInstance.Data;` plus 4 further local occurrences | The key is read as a `TArray` header at row offset `+0xC0`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetX` | `C-TYPE-02:17041872` `this->m_SupportOffsetX = __that->m_SupportOffsetX;` | The key is read as a 32-bit float at row offset `+0x78`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetY` | `C-TYPE-02:17041871` `this->m_SupportOffsetY = __that->m_SupportOffsetY;` | The key is read as a 32-bit float at row offset `+0x74`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | The key is read as a 32-bit scalar at row offset `+0x80`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_id` | `C-TYPE-02:17041821` `this->m_id = __that->m_id;` | The key is read as a 32-bit scalar at row offset `+0x08`. The row itself is resolved through `UEnemyDB::GetEnemyTypeDB(TypeID)`, whose stride is `0xD8` bytes. | recovered | `C-TYPE-01`, `C-TYPE-02` |

#### Additional runtime evidence: secondary `EnemyTypeID` consumers

Several visual/UI systems do not materialize the full `FEnemyTypeIDBase` row. Instead, they enter through small getter functions that first resolve `EnemyID.m_TypeID` and then read a specific byte, float, or array lane from `EnemyTypeID`.

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

| Key | Runtime interpretation |
|---|---|
| `m_EffectFromBottomOffsetY` | Read via the enemy id path as `EnemyID.m_TypeID @ +0x18` followed by `EnemyTypeID +0x58`. The getter returns `0.0` if either lookup fails. |
| `m_AilmentInfoOffsetX` | Read through the same `EnemyID -> EnemyTypeID` path from `EnemyTypeID +0x5C`. This is a direct scalar consumer, not merely a copied field. |
| `m_OrderTexUS`, `m_OrderTexVS`, `m_OrderScaleS`, `m_OrderTexUL`, `m_OrderTexVL`, `m_OrderScaleL` | `GetOrderIconParameter` reads six consecutive floats from the row as `float[35]`, `float[36]`, `float[37]`, `float[38]`, `float[39]`, and `float[40]`, corresponding to row offsets `+0x8C`, `+0x90`, `+0x94`, `+0x98`, `+0x9C`, and `+0xA0`. |
| `m_VoiceSymbol` | The sound subsystem obtains the voice symbol by resolving `EnemyID -> EnemyTypeID` and passing row `+0x80` to `USoundUtility::GetVoiceSymbol`. |


#### `EnemyTypeID` as the presentation, texture, audio, and VFX descriptor

The preceding field table proves that `EnemyTypeID` rows are live records. The following code paths are the important higher-order consumers: enemy construction, flipbook/NPC fallback selection, order-icon override, battle/field attach effects, voice-symbol resolution, call-sound playback, and hit/offset geometry. These paths show that the table is not merely a texture lookup table; it is a compact presentation descriptor whose row is repeatedly reached through `EnemyID.m_TypeID`.

##### C-TYPE-CONSTRUCTION-01: enemy construction walks every `EnemyTypeID.m_AnimationSetIDs` slot and seeds defeat-effect state

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

Interpretation: during `AQPBattleCharacterBase::SetupEnemy`, the enemy row is already resident as `v12`; row `v12 + 0x18` is `EnemyID.m_TypeID`. The setup path asks `GetEnemyTypeFlipbookSetNum(TypeID)` for the number of animation-set entries, then calls `SetupEnemyFlipbook` once per index. Consequently `m_AnimationSetIDs` is an ordered array of presentation slots, not a single default texture id. The same construction pass reads `m_DefeatEffect` through `GetEnemyTypeDefeatEffectID(TypeID)` and stores it into character runtime state; after this point the character can play the unique defeat-effect camera path without re-reading the DB row.

##### C-TYPE-FLIPBOOK-FALLBACK-02: animation-set slot resolution and NPC fallback in `SetupEnemyFlipbook`

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

Interpretation: the flipbook path first attempts to load an `UEnemyFlipbookSet` from `m_AnimationSetIDs[TextureIdx]`. If that fails, and the row is still an ordinary enemy type rather than an enemy part id (`type < ParamInt`), it reads `m_NpcCharaID[TextureIdx]` and uses that character id as the visual source. The hit-box offsets and hit-box scale are applied only for texture index `0`, which is the base visible body slot. This is stronger than “NPC fights do not need `EnemyBattleAnimSet`”: the code proves an explicit fallback hierarchy, not a schema convention.

##### C-TYPE-FLIPBOOK-MATERIALIZATION-03: `StaticGetEnemyFlipbooks` resolves `EnemyID -> TypeID`, while `GetEnemyFlipBook` materializes every texture id in the chosen anim set

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

Interpretation: the runtime resolves the type id lazily when the caller passes `TypeID <= 0`. The ordinary enemy path and the enemy-part path are explicitly separated by `UGameParamsUtility::GetParamInt(0x1B66)`: below the threshold, `TypeID` is an `EnemyTypeID`; at or above it, the code subtracts the threshold and treats it as an `EnemyParts` id. After an anim-set row is found, each `m_EnemyTextureIDs` element is loaded through resource category `2`, and its `EnemyTexID` metadata supplies the action id. Therefore the game does not interpret `m_EnemyTextureIDs[1]` as “attack 0” purely by position; action binding is data-driven by the `EnemyTexID` action id.

##### C-TYPE-ORDERICON-04: override order icon uses `m_OverrideOrderIconAnimSetID`; UV/scale uses dedicated row floats

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

Interpretation: `m_OverrideOrderIconAnimSetID` is row `+0x88`. When positive, the engine loads the idle/action-id-1 texture from that separate animation set and stores its first sprite as `m_OverrideOrderIconSprite`. The UV and pixel-scale values are not inferred from the flipbook: `GetOrderIconParameter` reads row `+0x8C`, `+0x90`, `+0x94`, `+0x98`, `+0x9C`, and `+0xA0` as `m_OrderTexUS`, `m_OrderTexVS`, `m_OrderScaleS`, `m_OrderTexUL`, `m_OrderTexVL`, and `m_OrderScaleL`, respectively.

##### C-TYPE-ATTACHEFFECT-05: battle attach effect uses row `+0xA8/+0xAC/+0xB0`; field attach effect uses row `+0xB4/+0xB8/+0xBC`

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

Interpretation: in battle, `m_AttachEffect` at `+0xA8` selects an `AttachEffect` row, while `m_BattleAttachEffectOffsetX` and `m_BattleAttachEffectOffsetY` at `+0xAC/+0xB0` are folded into the spawned effect's Y/Z placement after applying `m_ScaleX/m_ScaleY`. On the field/symbol-enemy side, `m_FieldAttachEffect` at `+0xB4` selects the `AttachEffect` row, and `m_FieldAttachEffectOffsetX/Y` at `+0xB8/+0xBC` enter the symbol offset calculation. The field path also reads `m_EffectOffsetX` (`+0x50`) for the half-width side offset, so field attach positioning is not identical to battle attach positioning.

##### C-TYPE-AUDIO-06: sound and voice consumers distinguish `m_CallSE`, `m_VoiceSymbol`, and `m_CueSheet`

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

Interpretation: `m_CallSE` at row `+0x7C` is a sound-effect id sent directly to `ASoundManager::ExecRequestPlaySeById(..., volume_rate=100)`. `m_VoiceSymbol` at row `+0x80` is not itself the final string; it is passed to `USoundUtility::GetVoiceSymbol`, which materializes the `FString`/UTF-16 voice symbol used by battle-voice code. `m_CueSheet` is recovered in the `FEnemyTypeIDBase` copy constructor at `+0x84`, but this pass did not recover an equally direct enemy-type consumer for it; the guide should therefore classify `m_CueSheet` as a live materialized field with incomplete consumer evidence, not as unused.

##### C-TYPE-OFFSETMAP-07: compact `EnemyTypeID` row layout map recovered from copy constructor and direct consumers

| Offset | Key(s) | Runtime evidence | Interpretation |
|---:|---|---|---|
| `+0x10/+0x18` | `m_AnimationSetIDs` | `GetEnemyTypeFlipbookSet` copies the array and indexes it by `TextureIdx`; `SetupEnemy` iterates `0..GetEnemyTypeFlipbookSetNum-1`. | Ordered visual slots. Each slot is an `EnemyBattleAnimSet.m_id`. |
| `+0x20/+0x28` | `m_NpcCharaID` | `GetEnemyTypeCharaID(TypeID, TextureIdx)` bounds-checks the array and returns `Data[TextureIdx]`. | NPC/character fallback for a slot when no flipbook set is loaded. |
| `+0x30` | `m_ChangeColorID` | `GetEnemyTypeColorID` reads `EnemyTypeDB[48]`. | Color-change id reached through the type row. |
| `+0x34/+0x38` | `m_ScaleX`, `m_ScaleY` | `GetEnemyTypeScale` reads floats at `+0x34/+0x38`; attach effects also multiply by these values. | Visual/effect scale basis. |
| `+0x3C/+0x40` | `m_HitPosX`, `m_HitPosY` | `GetEnemyTypeHitOffset` reads floats at `+0x3C/+0x40`. | Base hit-box offset applied during base flipbook setup. |
| `+0x44/+0x48` | `m_HitScaleX`, `m_HitScaleY` | `GetEnemyTypeHitScale` reads floats at `+0x44/+0x48`. | Base hit-box scale applied during base flipbook setup. |
| `+0x4C` | `m_Size` | `GetEnemyTypeSize` returns byte at `+0x4C`; `GetEnemySize(EnemyID)` reaches it through `EnemyID.m_TypeID`. | Enemy size class is a type property, not an `EnemyID` scalar. |
| `+0x50/+0x54/+0x58` | `m_EffectOffsetX`, `m_EffectOffsetY`, `m_EffectFromBottomOffsetY` | `GetEnemyEffectOffset` reads `+0x50/+0x54`; `GetEnemyBottomEffectOffset` reads `+0x58`; symbol field attach uses `+0x50`. | Effect placement lanes. |
| `+0x5C/+0x60` | `m_AilmentInfoOffsetX`, `m_AilmentOffset` | `GetEnemyAilmentInfoOffsetX` reads `+0x5C`; `GetAilmentEffectOffsetID` reads `+0x60`. | UI/ailment effect offsets. |
| `+0x64/+0x68` | `m_DamageOffsetX`, `m_DamageOffsetY` | `GetEnemyDamageOffset` reads `+0x64/+0x68`. | Damage-number placement. |
| `+0x6C/+0x70` | `m_CursorOffsetX`, `m_CursorOffsetY` | `GetEnemyCursorOffset` reads `+0x6C/+0x70`. | Target cursor placement. |
| `+0x74/+0x78` | `m_SupportOffsetY`, `m_SupportOffsetX` | Copy-constructor/materialization proof; no direct consumer recovered in this pass. | Live field; consumer evidence incomplete. |
| `+0x7C/+0x80/+0x84` | `m_CallSE`, `m_VoiceSymbol`, `m_CueSheet` | `PlayEnemyTypeCallSE` uses `m_CallSE`; `GetVoiceCharaSymbol` uses `m_VoiceSymbol`; `m_CueSheet` is copied/materialized. | Sound effect and voice-symbol are direct consumers; cuesheet consumer remains partial. |
| `+0x88` | `m_OverrideOrderIconAnimSetID` | `LoadOverrideOrderIcon` reads `EnemyTypeDB[136]` and calls `GetActionTexID(..., EBATTLE_ACTION_ID::IDLE)`. | Optional independent order-icon animation set. |
| `+0x8C..+0xA0` | order UV/scale fields | `GetOrderIconParameter` reads float indices `35..40`. | Explicit order-icon UV and scale lanes. |
| `+0xA4..+0xA6` | `m_AddStartUV`, `m_AttachEffectInGodbeastProduction`, `m_KeepVisibleAttachEffectOnMotion` | Copy constructor proves all three fields; `IsVisibleAttachEffectOnMotion` directly reads `+0xA6`. | `m_KeepVisibleAttachEffectOnMotion` has a direct consumer; the other two are materialized-only in the recovered paths. |
| `+0xA8/+0xAC/+0xB0` | battle attach fields | `SetEnemyAttachEffect` reads id at `+0xA8` and offsets at `+0xAC/+0xB0`. | Battle character attach effect. |
| `+0xB4/+0xB8/+0xBC` | field attach fields | `ASymbolEnemy::UpdateAttachEffect` reads id at `+0xB4` and offsets at `+0xB8/+0xBC`. | Symbol/field attach effect. |
| `+0xC0/+0xC8` | `m_PartsID` | part setup uses the array count/data; earlier section cites the part resolver. | Enemy part composition. |
| `+0xD0` | `m_LinkOwnerAnim` | Copy-constructor/materialization proof; part code gives indirect contextual support. | Live flag; exact consumer should be tested when authoring linked parts. |
| `+0xD4` | `m_DefeatEffect` | `SetupEnemy` reads through `GetEnemyTypeDefeatEffectID` and stores runtime defeat-effect state. | Unique defeat effect id. |
 
### (**) `EnemyWeakID`
Tracks different kinds of weakness profiles which can be assigned to enemies. `m_ResistWeapon` and `m_ResistMagic` take the `m_id`s of `ResistType` under `Skill/` as input; `ResistType` 1 also adds the specified weapon/magic type as a weakness. The order of the specified weapon and magic types likely follow the order of the `m_id`s in the `WeaponType`/`MagicType` assets under `Skill/`, i.e.:
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
**Note:** OT0 can't display more than 8 weaknesses on one enemy. 

`Kingship_structs.hpp` assigns the following labels to some of OT0's ResistTypes:

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

#### Runtime reading semantics
##### C-WEAK-01: EnemyWeakID resolver and weak-id getter

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

##### C-WEAK-02: Enemy weak row selection and Resist array lane selection

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

##### C-WEAK-03: USkillDB lane accessor for EnemyWeakID arrays

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

##### C-ENEMY-02: EnemyID materializer with concrete row offsets

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_ResistMagic` | `+0x20` (array); no shorter named assignment survived in the cited block | The magic-resistance vector is selected by passing `isMagic=true`; `USkillDB::GetResistsByID` returns `&EnemyWeakDB[0x20]`, i.e. the `TArray` header at row `+0x20`. | recovered | `C-WEAK-02`, `C-WEAK-03` |
| `m_ResistWeapon` | `C-WEAK-03:17974953` `return &EnemyWeakDB[16 * v2 + 16];` | The weapon-resistance vector is selected by passing `isMagic=false`; `USkillDB::GetResistsByID` returns `&EnemyWeakDB[0x10]`, i.e. the `TArray` header at row `+0x10`. | recovered | `C-WEAK-02`, `C-WEAK-03` |
| `m_WeakID` | `+0x1C` (scalar; live field belongs to EnemyID); no shorter named assignment survived in the cited block | When no explicit weak-row override is supplied, `UEnemyDB::GetEnemyResists` first reads `EnemyID.m_WeakID` from the enemy row at `+0x1C`, then resolves the corresponding `EnemyWeakID` row. | recovered | `C-WEAK-02`, `C-ENEMY-02` |
| `m_id` | `+0x08` (scalar; live field belongs to EnemyTypeID); no shorter named assignment survived in the cited block | The key is read as a 32-bit scalar at row offset `+0x08`. `WeakID` is resolved through `UEnemyDB::GetEnemyWeakDB`; the row stride is `0x30` bytes. | recovered | `C-WEAK-01` |


### (**) `EnemyWeakChangeID`
Tracks how enemies change weaknesses. `m_WeakIndices` takes `m_EnemyWeakID.m_id` as inputs and the `m_id` of those weakness changes can be assigned to e.g. an avail (see also [How to add custom skills and avails](customskls_en.md)).

`Kingship_structs.hpp` assigns the labels below to OT0's IDs. The enum is best understood as an SDK convenience enum for stock rows, not as a runtime exhaustiveness constraint. The runtime does not switch over the listed `EENEMY_WEAKCHANGE_ID` constants. It resolves the requested numeric `ChangeID` through `m_EnemyWeakChangeID.m_DBAccess`; if the key resolves to a row, it reads that row's `m_WeakIndices` array and lottery flag. Therefore the table can be expanded, provided the DB access/index data is rebuilt consistently and call sites receive the new id.
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
Do not treat `EENEMY_WEAKCHANGE_MAX` as the engine's actual upper bound. The C implementation accepts any positive `ChangeID` that exists in `m_EnemyWeakChangeID.m_DBAccess`. The enum simply documents named stock ids compiled into the SDK.
#### Runtime reading semantics
`EnemyWeakChangeID` rows are recovered as 40-byte records. The live row reader returns a pointer to the array at row `+0x10` and writes a lottery boolean from row `+0x20`. The row id is resolved by `FDbAccessHelper`, not by a switch over enum labels.

**Primary C evidence:**

##### C-WCH-01: key lookup, 40-byte row stride, `m_WeakIndices`, and lottery flag

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

**Interpretation.** `ChangeID` is a positive integer key. The row stride is 40 bytes. `m_WeakIndices` is the `TArray<int>` at `+0x10`; the lottery flag is the byte at `+0x20`. There is no enum-range check here.

##### C-WCH-02: skills/avails supply `m_WeakChangeID`

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

**Interpretation.** An avail carries the weak-change id. One observed use casts the id to `unsigned __int8` before calling `SetChangeWeak`; this is a caller-side narrowing artifact and should be tested if ids above 255 are introduced. It is distinct from the table lookup routine, which accepts an `int ChangeID` and performs DB lookup.

##### C-WCH-03: setting the active weak-change id changes runtime state and triggers UI shuffle

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

**Interpretation.** `SetChangeWeak` stores the selected table id in runtime state (`m_WeakChangeID`), marks a switch when the id changes, initializes a first-lottery flag when transitioning from no weak-change id, and starts the status-UI weak-shuffle animation.

##### C-WCH-04: weak-change execution builds a queue from `m_WeakIndices`

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

**Interpretation.** The `m_WeakIndices` array is copied into the character's `m_ChangeWeaks` queue, with two important runtime rules. First, the scan stops when it encounters a non-positive weak id. Second, during the first lottery transition, the enemy's current/base weak id is excluded from the candidate queue.

##### C-WCH-05: lottery mode appends repeated random picks from the candidate queue

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

**Interpretation.** If the row's lottery flag is true and there is more than one candidate, the runtime extends the queue with repeated random selections. This makes lottery rows behave differently from deterministic cycle rows even though both use the same `m_WeakIndices` array.

##### C-WCH-06: execution pops the next weak id and stores it in `m_NowWeakID`

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

**Interpretation.** The selected weak profile is the queue head. After selection, it is removed from the queue and stored as `m_NowWeakID`. A return value indicates whether the current weak profile changed.

##### C-WCH-07: UI/index path searches `m_WeakIndices` for the current weak id

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

**Interpretation.** The runtime can recover the ordinal of the currently active weak profile by searching the original `m_WeakIndices` array. If the current weak id is absent, the function returns `-1`.

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., ChangeID)` | Positive integer key into `m_EnemyWeakChangeID`; stock enum labels are not an exhaustive runtime switch. | recovered | `C-WCH-01` |
| `m_WeakIndices` | row `+0x10`, returned as `TArray<int>*` | Ordered weak-profile list of `EnemyWeakID.m_id` values. Non-positive entries terminate the scan in `ChangeWeak`. | recovered | `C-WCH-01`, `C-WCH-04`, `C-WCH-06`, `C-WCH-07` |
| lottery flag / row `+0x20` | `*isLottery = *(_BYTE *)(v6 + 32)` | Boolean that changes queue construction from direct deterministic queueing to random queue extension. | recovered | `C-WCH-01`, `C-WCH-05` |
| SDK enum entries | no switch over enum constants in cited runtime | Names for stock ids only. Expanding the DB is not blocked by `EENEMY_WEAKCHANGE_MAX`, but ids passed through `unsigned __int8` call sites should remain within byte range unless those call sites are patched/tested. | recovered / caveated | `C-WCH-01`, `C-WCH-02` |

### (**) `EnemyWeakLockID`
Tracks how enemies lock weaknesses. There is no evidence here for locking non-elemental magic damage and poison damage as distinct special lanes; the lock test is simply bounded by the lengths of the stored weapon and magic arrays and by the passed weapon/magic type indices.

A weak lock is typically carried by an ailment/avail. The battle character checks active ailment slots for ailment id `37`, reads the originating avail id, obtains `SkillAvailID.m_WeakLockID`, and then asks `EnemyWeakLockID` whether a given weapon or magic weakness is locked. UI code separately obtains the same weapon/magic vectors and starts or ends lock display state.

#### Runtime reading semantics
`EnemyWeakLockID` rows are recovered as 80-byte records. The row id is resolved through `m_EnemyWeakLockID.m_DBAccess`. Offsets `+0x10` and `+0x20` are the two boolean lock vectors; offsets `+0x30` and `+0x40` are condition/parameter vectors used when deciding whether the weak-lock ailment should be removed.

**Primary C evidence:**

##### C-WLOCK-01: key lookup and 80-byte row stride

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

**Interpretation.** `LockID` is a DB key, not an enum switch case. The row stride is 80 bytes.

##### C-WLOCK-02: weapon/magic lock vectors are selected by `isMagic`

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

**Interpretation.** `isMagic=false` returns the `TArray<bool>` header at row `+0x10`; `isMagic=true` returns the `TArray<bool>` header at row `+0x20`. This is the exact reason the table has separate weapon and magic lock arrays.

##### C-WLOCK-03: direct lock predicate bounds-checks the selected vector

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

**Interpretation.** Weapon locks use the vector pointer/count at `+0x10/+0x18`; magic locks use `+0x20/+0x28`. If `WeaponType` is nonzero it takes precedence; otherwise a nonzero `MagicType` is checked. Type index `0` is not checked by this predicate because both branches require a nonzero type argument. This matches the guide warning that some special weakness lanes are not represented in the same way as normal weapon/magic icons.

##### C-WLOCK-04: remove-condition arrays are row `+0x30` and `+0x40`

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

**Interpretation.** Since the row is viewed as a sequence of 16-byte `TArray<int>` headers, `EnemyWeakLockDB + 3` is row `+0x30` and `EnemyWeakLockDB + 4` is row `+0x40`. These are weak-lock-specific removal condition and parameter arrays.

##### C-WLOCK-05: weak-lock removal conditions override ailment defaults for ailment id `37`

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

**Interpretation.** Weak-lock ailment removal does not rely solely on `SkillAilmentDB` default remove conditions. When the ailment id is `37`, the avail's `m_WeakLockID` can redirect the condition/parameter arrays to the `EnemyWeakLockID` row.

##### C-WLOCK-06: active ailment path obtains `SkillAvailID.m_WeakLockID`

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

**Interpretation.** The runtime source of a lock id is the skill avail that produced an active weak-lock ailment. As with weak-change ids, the return type is an enum name, but the value is passed as data into a DB lookup; the cited code does not implement a switch over enumerators.

##### C-WLOCK-07: UI lock state reads the same weapon/magic vectors

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

**Interpretation.** UI presentation is not a separate table. It receives the same two lock vectors returned by `GetLockedWeakFlags`, so changes to the weapon/magic arrays alter both logical lock checks and the displayed lock state.

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., LockID)`, row stride `80` | Positive integer key into `m_EnemyWeakLockID`. Stock enum labels are not an exhaustive runtime switch. | recovered | `C-WLOCK-01` |
| weapon lock array | row `+0x10/+0x18` | `TArray<bool>` tested when `WeaponType != 0`; also passed to UI lock display as the weapon vector. | recovered | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| magic lock array | row `+0x20/+0x28` | `TArray<bool>` tested when `WeaponType == 0 && MagicType != 0`; also passed to UI lock display as the magic vector. | recovered | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| remove conditions | row `+0x30` | `TArray<int>` condition ids used for weak-lock ailment removal. | recovered | `C-WLOCK-04`, `C-WLOCK-05` |
| remove params | row `+0x40` | `TArray<int>` parameters paired with remove conditions. | recovered | `C-WLOCK-04`, `C-WLOCK-05` |
| non-elemental / poison locking | no special branch in `IsLockedWeak` | The cited predicate checks only nonzero weapon or magic type indices within the two stored vectors. No separate special-case lane is recovered here. | negative / partial | `C-WLOCK-03` |

### (****) `EnemyID`
This is the game's enemy master record: it defines stats/weaknesses/rewards/UI flags, points to `EnemyTypeID` for presentation (animation sets, textures, offsets, voice, and effects), and to `TacticalAssignList` and `TacticalSkillList` for enemy behavior and their skillset (see "under `AIBattle`" below).
- `m_Label`: label of the Enemy. May be used internally and it's likely a good idea to not add new entries with conflicting labels
- `m_DisplayName`: ID refers to `GameTextEnemy.m_id`, universal for all localizations.
- `m_WeakID`: ID refers to `EnemyWeakID.m_id`, see above
- `m_DisplayLevel`: The enemy's displayed level. Unfortunately does not show up even with `m_LevelUI` set to true
- `m_BreakRate`: Percentage value by which damage done to the enemy is multiplied when broken
- `m_DamageRate`: Percentage value by which damage done to the enemy is multiplied when not broken
- `m_MaxSLD`: Shield count of an enemy. Even if the name of the key may suggest it is the maximum value, some tactics and skills can set it to higher values (see Lucian the Glorious for example)
- `m_MaxHP`: HP of an enemy. As before, skills and tactics can increase it
- `m_MaxSP`: SP of an enemy. Most enemy skills use 0 SP, so this stat is primarily used to limit the number of usages on specific enemy skills
- `m_AtkP`: Phys. Atk. of the enemy
- `m_AtkM`: Elem. Atk. of the enemy
- `m_DefP`: Phys. Def. of the enemy
- `m_DefM`: Elem. Def. of the enemy
- `m_Agi`: Speed/Agility of the enemy
- `m_Crt`: Crit. stat of the enemy
- `m_CrtDef`: Crit. Def. of the enemy; only enemies appear to have this stat
- `m_Hit`: Accuracy stat of the enemy
- `m_Avd`: Evasion stat of the enemy
- `m_ResistAilmentID`: assigns ID which tracks resistance to status ailments to enemy (see `SkillResistAilmentID` under `Skill/`)
- `m_ResistAilment`: An array corresponding to resist rates to certain ailments, possibly when the corresponding `m_ResistAilments` array of the assigned `ResistAilmentID` is full. Appears to only be used by Caits.
- `m_TacticalAssignID`: Assigns `TacticalAssignList.m_id` to an enemy corresponding to a list of tactics used in battle, see "Under `AIBattle` below
- `m_SkillsID`: Assigns `TacticalSkllList.m_id` to an enemy corresponding to a list of skills used in battle, see "Under `AIBattle` below
- `m_IsBoss`: Flag used by many bosses but also NPCs in towns. Not sure what it does functionally
- `m_Exp`, `m_Money`, `m_JP`: XP, Money or JP gained from this enemy after defeating them.
- `m_PetExp`: Always set to -1 in both CotC and OT0. Values not equal to -1 appear to have no effect
- `m_DropReward`: Item the enemy may drop. (see `EnemyDropID` for viable IDs)
- `m_StealReward`: Item that can be stolen from the enemy (see `ItemList` under `Item/` for viable IDs)
- `m_StealLeafNum`: Number of leaves that can be stolen from the enemy
- `m_StatusOffset`: Offset of enemy's status bar (weaknesses and shield count)
- `m_Bottle`: Meaning unclear. OT0 uses values 1-3. Value 3 is only used by Wappa in OT0. CotC also makes use of values 4 and 5.
- `m_NameUI`: Whether an enemy's name shows up on the UI
- `m_LevelUI`: Whether an enemy's `m_DisplayLevel` shows up on the UI (unused/nonfunctional)
- `m_HpUI`: Not functional in OT0.
- `m_ShieldUI`: If set to true, appears to darken the enemy's shield icon
- `m_RaceIndices`: races assigned to enemy. Can influence the damage calculation
- `m_DisplaySpecialSkillGauge`: Flag for displaying the enemy's BP counter
- `m_GenerateSpecialSkillValueOrverTurn`: how many BP points the enemy generates per turn
- `m_ReduceSpecialSkillValueBreak`: how many BP points are lost by the enemy after break
- `m_MaxSpecialSkillValue`: maximum number of BP points the enemy has. Values higher than 20 crash the game.
- `m_OutsideTarget`: Enemy can't be targeted and if it's the only enemy on the field, party members will also skip their actions (Bestowr of All uses this until the rings are broken)
- `m_NotAction`: If true, does not allow the enemy to act (used by Bestower of All before all the rings are defeated).
- `m_UnknownLv`: Displays the enemy's level as "??," though unused and visually non-functional due to the lack of enemy level UI, do not treat this as proven-unused padding. The OT0 decomp's named `FEnemyIDBase` copy/materializer paths copy it as a real late flag (`Octopath_Traveler0-Win64-Shipping.exe.c`:17041778-17041806 and 17647345-17647360). 
- `m_Immortal`: If set to true, enemy's HP will never go below 1.

#### Runtime reading semantics (field-level Hex-Rays evidence)
##### C-ENEMY-01: EnemyID row resolver

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

##### C-ENEMY-02: EnemyID materializer with concrete row offsets

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

##### C-ENEMY-03: EnemyID late-state bridge into battle character flags

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

##### C-ENEMY-04: Runtime mutators/accessors for OutsideTarget, NotAction, Immortal

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

##### C-ENEMY-05: Runtime getters for NotAction and OutsideTarget plus Immortal setter

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

##### C-ENEMY-06: RaceIndices membership test

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_Agi` | `C-ENEMY-02:17647319` `cEnemyListBase.m_Agi = *(_DWORD *)(v12 + 80);` | The key is read as a 32-bit scalar at row offset `+0x50`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkM` | `C-ENEMY-02:17647317` `cEnemyListBase.m_AtkM = *(_DWORD *)(v12 + 72);` | The key is read as a 32-bit scalar at row offset `+0x48`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkP` | `C-ENEMY-02:17647315` `cEnemyListBase.m_AtkP = *(_DWORD *)(v12 + 64);` | The key is read as a 32-bit scalar at row offset `+0x40`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Avd` | `C-ENEMY-02:17647323` `cEnemyListBase.m_Avd = *(_DWORD *)(v12 + 96);` | The key is read as a 32-bit scalar at row offset `+0x60`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Bottle` | `C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | The key is read as a 32-bit scalar at row offset `+0xC8`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_BreakRate` | `C-ENEMY-02:17647311` `cEnemyListBase.m_BreakRate = *(_DWORD *)(v12 + 48);` | The key is read as a 32-bit scalar at row offset `+0x30`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | This is not an `EnemyID` row field. When mentioned in the `EnemyID` section it is reached through `EnemyID.m_TypeID -> EnemyTypeID +0x7C`. | recovered | `C-TYPE-02`, `C-TYPE-03` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | This is not an `EnemyID` row field. `GetEnemyTypeColorID` reads the type row at `EnemyTypeID +0x30` after resolving `EnemyID.m_TypeID`. | recovered | `C-TYPE-01`, `C-TYPE-02` |
| `m_Crt` | `C-ENEMY-02:17647320` `cEnemyListBase.m_Crt = *(_DWORD *)(v12 + 84);`<br>`C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | The key is read as a 32-bit scalar at row offset `+0x54`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CrtDef` | `C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | The key is read as a 32-bit scalar at row offset `+0x58`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DamageRate` | `C-ENEMY-02:17647324` `cEnemyListBase.m_DamageRate = *(_DWORD *)(v12 + 100);` | The key is read as a 32-bit scalar at row offset `+0x64`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefM` | `C-ENEMY-02:17647318` `cEnemyListBase.m_DefM = *(_DWORD *)(v12 + 76);` | The key is read as a 32-bit scalar at row offset `+0x4C`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefP` | `C-ENEMY-02:17647316` `cEnemyListBase.m_DefP = *(_DWORD *)(v12 + 68);` | The key is read as a 32-bit scalar at row offset `+0x44`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayLevel` | `C-ENEMY-02:17647291` `memset(&cEnemyListBase.m_DisplayLevel, 0, 76);`<br>`C-ENEMY-02:17647309` `(TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_DisplayLevel,`<br>`C-ENEMY-02:17647361` `if ( cEnemyListBase.m_DisplayLevel.Data.ArrayNum )` plus 2 further local occurrences | The key is read as an `FString`/UTF-16 array header at row offset `+0x20`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayName` | `C-ENEMY-02:17647281` `FEnemyIDBase cEnemyListBase; // [rsp+20h] [rbp-E0h] BYREF`<br>`C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | The key is read as a 32-bit scalar at row offset `+0x14`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplaySpecialSkillGauge` | `C-ENEMY-02:17647353` `cEnemyListBase.m_DisplaySpecialSkillGauge = *(_BYTE *)(v12 + 224);` | The key is read as a byte/Boolean lane at row offset `+0xE0`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DropReward` | `C-ENEMY-02:17647336` `cEnemyListBase.m_DropReward = *(_DWORD *)(v12 + 156);` | The key is read as a 32-bit scalar at row offset `+0x9C`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_EventDropRewards` | `C-ENEMY-02:17647340` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_EventDropRewards,` | The key is read as a `TArray` header at row offset `+0xA8`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Exp` | `C-ENEMY-02:17647293` `memset(&cEnemyListBase.m_Exp, 0, 85);`<br>`C-ENEMY-02:17647332` `cEnemyListBase.m_Exp = *(_DWORD *)(v12 + 140);` | The key is read as a 32-bit scalar at row offset `+0x8C`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_GenerateSpecialSkillValueOrverTurn` | `C-ENEMY-02:17647294` `*(_QWORD *)&cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = 0;`<br>`C-ENEMY-02:17647354` `cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = *(_DWORD *)(v12 + 228);` | The key is read as a 32-bit scalar at row offset `+0xE4`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Hit` | `C-ENEMY-02:17647322` `cEnemyListBase.m_Hit = *(_DWORD *)(v12 + 92);` | The key is read as a 32-bit scalar at row offset `+0x5C`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_HpUI` | `C-ENEMY-02:17647348` `cEnemyListBase.m_HpUI = *(_BYTE *)(v12 + 206);` | The key is read as a byte/Boolean lane at row offset `+0xCE`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Immortal` | `C-ENEMY-02:17647360` `cEnemyListBase.m_Immortal = *(_BYTE *)(v12 + 243);` | The byte at `EnemyID +0xF3` is passed through `SetImmortal` during battle-character setup; after setup, runtime code reads/writes `m_IsImmortal` on the character object rather than re-reading the DB row. | recovered | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-05` |
| `m_IsBoss` | `C-ENEMY-02:17647331` `cEnemyListBase.m_IsBoss = *(_BYTE *)(v12 + 136);` | The key is read as a byte/Boolean lane at row offset `+0x88`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_JP` | `C-ENEMY-02:17647335` `cEnemyListBase.m_JP = *(_DWORD *)(v12 + 152);` | The key is read as a 32-bit scalar at row offset `+0x98`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Label` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | The key is read as an `FName` at row offset `+0x0C`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_LevelUI` | `C-ENEMY-02:17647347` `cEnemyListBase.m_LevelUI = *(_BYTE *)(v12 + 205);` | The key is read as a byte/Boolean lane at row offset `+0xCD`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxHP` | `C-ENEMY-02:17647313` `cEnemyListBase.m_MaxHP = *(_DWORD *)(v12 + 56);` | The key is read as a 32-bit scalar at row offset `+0x38`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSLD` | `C-ENEMY-02:17647312` `cEnemyListBase.m_MaxSLD = *(_DWORD *)(v12 + 52);` | The key is read as a 32-bit scalar at row offset `+0x34`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSP` | `C-ENEMY-02:17647314` `cEnemyListBase.m_MaxSP = *(_DWORD *)(v12 + 60);` | The key is read as a 32-bit scalar at row offset `+0x3C`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSpecialSkillValue` | `C-ENEMY-02:17647295` `*(_QWORD *)&cEnemyListBase.m_MaxSpecialSkillValue = 0;`<br>`C-ENEMY-02:17647356` `cEnemyListBase.m_MaxSpecialSkillValue = *(_DWORD *)(v12 + 236);` | The key is read as a 32-bit scalar at row offset `+0xEC`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Money` | `C-ENEMY-02:17647333` `cEnemyListBase.m_Money = *(_DWORD *)(v12 + 144);` | The key is read as a 32-bit scalar at row offset `+0x90`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NameUI` | `C-ENEMY-02:17647346` `cEnemyListBase.m_NameUI = *(_BYTE *)(v12 + 204);` | The key is read as a byte/Boolean lane at row offset `+0xCC`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NotAction` | `C-ENEMY-02:17647358` `cEnemyListBase.m_NotAction = *(_BYTE *)(v12 + 241);` | The byte at `EnemyID +0xF1` initializes `AQPBattleCharacterBase::m_IsNotAction`; battle-event commands can clear it via `DisableNotAction`, so the row value is an initial no-action state. In this C export the live flag is Boolean-typed. | recovered | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_OutsideTarget` | `C-ENEMY-02:17647357` `cEnemyListBase.m_OutsideTarget = *(_BYTE *)(v12 + 240);` | The byte at `EnemyID +0xF0` initializes `AQPBattleCharacterBase::m_IsOutsideTarget`; later targeting code queries or clears that runtime flag, so the DB field is an initial-state seed, not a permanently immutable property. | recovered | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_PetExp` | `C-ENEMY-02:17647334` `cEnemyListBase.m_PetExp = *(_DWORD *)(v12 + 148);` | The key is read as a 32-bit scalar at row offset `+0x94`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_RaceIndices` | `C-ENEMY-02:17647351` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_RaceIndices,`<br>`C-ENEMY-02:17647368` `Data = cEnemyListBase.m_RaceIndices.AllocatorInstance.Data;` | `m_RaceIndices` is a `TArray<int>` at `EnemyID +0xD0`. Runtime membership is not scalar equality; `HasEnemyRace` iterates the array and succeeds only if any element equals the requested race id. | recovered | `C-ENEMY-02`, `C-ENEMY-06` |
| `m_ReduceSpecialSkillValueBreak` | `C-ENEMY-02:17647355` `cEnemyListBase.m_ReduceSpecialSkillValueBreak = *(_DWORD *)(v12 + 232);` | The key is read as a 32-bit scalar at row offset `+0xE8`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilment` | `C-ENEMY-02:17647292` `memset(&cEnemyListBase.m_ResistAilment, 0, 25);`<br>`C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);`<br>`C-ENEMY-02:17647327` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_ResistAilment,` | The key is read as a `TArray` header at row offset `+0x70`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilmentID` | `C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);` | The key is read as a 32-bit scalar at row offset `+0x68`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilments` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_ResistAilments` symbol or unambiguous asset-specific offset was recovered in the C export. The field is therefore documented as unrecovered, not inferred. | negative / partial | not recovered |
| `m_ShieldUI` | `C-ENEMY-02:17647349` `cEnemyListBase.m_ShieldUI = *(_BYTE *)(v12 + 207);` | The key is read as a byte/Boolean lane at row offset `+0xCF`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | The key is read as a 32-bit scalar at row offset `+0x84`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StatusOffset` | `C-ENEMY-02:17647343` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_StatusOffset,`<br>`C-ENEMY-02:17647369` `v8 = cEnemyListBase.m_StatusOffset.AllocatorInstance.Data;` | The key is read as a `TArray` header at row offset `+0xB8`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealLeafNum` | `C-ENEMY-02:17647338` `cEnemyListBase.m_StealLeafNum = *(_DWORD *)(v12 + 164);` | The key is read as a 32-bit scalar at row offset `+0xA4`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealReward` | `C-ENEMY-02:17647337` `cEnemyListBase.m_StealReward = *(_DWORD *)(v12 + 160);` | The key is read as a 32-bit scalar at row offset `+0xA0`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_TacticalAssignID` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | The key is read as a 32-bit scalar at row offset `+0x80`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_UnknownLv` | `C-ENEMY-02:17647359` `cEnemyListBase.m_UnknownLv = *(_BYTE *)(v12 + 242);` | The key is read as a byte/Boolean lane at row offset `+0xF2`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | This is not an `EnemyID` row field. The runtime resolves `EnemyID.m_TypeID`, then reads `EnemyTypeID +0x80` for `m_VoiceSymbol`. | recovered | `C-TYPE-02`, `C-TYPE-03` |
| `m_WeakID` | `+0x1C` (scalar); no shorter named assignment survived in the cited block | The key is read as a 32-bit scalar at row offset `+0x1C`. `UEnemyDB::GetEnemyDB` resolves the row with stride `0xF8`; `ASymbolEnemy::GetTargetLevel` materializes these offsets explicitly. | recovered | `C-ENEMY-01`, `C-ENEMY-02` |

### Additional runtime evidence: direct `EnemyID` accessors, status-type mapping, and reverse indices

The `EnemyID` table is not only copied as a whole row. A large part of battle and UI code reads individual fields through small accessors. These accessors provide a useful independent check on row offsets and also reveal which fields are queried as scalar values, arrays, or secondary table ids.

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

| Key or key group | Runtime interpretation |
|---|---|
| `m_DisplayName` | Read as row `+0x14`; `GetEnemyNameString` passes it to `AGameTextManager::GetGameText(..., 4u, DisplayName, 0)`. The game text namespace/category is therefore hard-coded in this path as `4u`. |
| `m_TypeID` | Read as row `+0x18`. In the `withID` debug/display branch it is used only when no positive concrete `EnemyID` is preferred for the prefix; in gameplay it is also the bridge to `EnemyTypeID`. |
| `m_MaxHP`, `m_MaxSP`, `m_MaxSLD`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_Hit`, `m_Avd` | `GetEnemyStatus` maps status ids to row `uint32` slots. The observed mapping is: `1 -> m_MaxHP`, `2 -> m_MaxSP`, `4 -> m_MaxSLD`, `9 -> m_AtkP`, `10 -> m_DefP`, `11 -> m_AtkM`, `12 -> m_DefM`, `13 -> m_Agi`, `14 -> m_Crt`, `15 -> m_Hit`, `16 -> m_Avd`. `m_CrtDef` is present in the row but not reached by this particular status-type switch. |
| `m_StealReward` | Read as row `+0xA0`; the boolean overload returns success only when the copied id is positive. |
| `m_StealLeafNum` | Read as row `+0xA4`; the boolean overload returns success only when the copied leaf count is positive. |
| `m_TacticalAssignID` | Read directly from row `+0x80`; this is the enemy-to-tactic assignment id. |
| `m_SkillsID` | Read directly from row `+0x84`; `HasTacticalSkill` resolves this id through `UTacticalDB::GetTacticalSkillIndices` and compares concrete `SkillID` values in the returned skill array. |

The C export also contains reverse-index helpers for type and race membership. These are relevant for modding because they show that `m_TypeID` and `m_RaceIndices` are used not only by a single enemy instance, but also by global lookup/cache structures.

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

| Key | Runtime interpretation |
|---|---|
| `m_RaceIndices` | Stored as the row-owned array at `+0xD0/+0xD8`. `HasEnemyRace` is an array membership test, and `GetSameRaceEnemies` builds enemy-id result arrays by scanning this field. It is therefore not a scalar race enum. |
| `m_TypeID` | `GetSameTypeEnemies` scans all `EnemyID` rows and groups enemies by row `+0x18`, proving that type id is also a global classification key, not only a presentation pointer. |


#### `EnemyID` as the battle-character seed record

The preceding field table establishes the row layout. The following additional paths are the higher-level runtime semantics that matter for modding: enemy construction, UI visibility, stat lookup, level/name localization, weak-flag initialization, drops/steals, race/type reverse scans, special-gauge mutation, and initial-state flags. In these paths the game treats `EnemyID` as the seed from which an `AQPBattleCharacterBase` instance is initialized; several fields are copied into runtime state once and are then mutated independently.

##### C-ENEMY-CONSTRUCTION-01: enemy construction seeds presentation, weak flags, params, immortal/outside/no-action state

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

Interpretation: `SetupEnemy` reads `EnemyID` once through `UEnemyDB::GetEnemyDB`; `v12` is the resolved row. The row is then used to seed persistent battle-character fields. `m_TypeID` drives presentation setup. `m_WeakID` is not copied directly here; instead, weak flags are computed by querying `AQPBattleManager::GetEnemyWeakFlags` for every weapon/magic index, which eventually resolves the weak table. `m_Immortal`, `m_OutsideTarget`, and `m_NotAction` are initial-state seeds at `+0xF3`, `+0xF0`, and `+0xF1`. After seeding, the live character fields can be modified independently by skills/events.

##### C-ENEMY-STATE-RESET-02: `SetupParams` resets live state before enemy row flags are applied

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

Interpretation: `SetupParams` deliberately clears `m_IsOutsideTarget`, the adjacent no-action byte, and immortality before `SetupEnemy` writes the DB-seeded values. This ordering is important: the DB fields do not represent immutable predicates; they initialize the character's starting battle state after the generic reset. Subsequent code can clear or set the same runtime flags without changing the database row.

##### C-ENEMY-DISPLAY-TEXT-03: display-name and level strings are separate mechanisms

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

Interpretation: `m_DisplayName` is row `+0x14` and is resolved through `GameText` category `4`, so it is a localization id, not a raw string. `m_DisplayLevel` is row `+0x20` and is an `FString`/UTF-16 array that `GetEnemyLevel` parses with `_wtoi`. This explains why a non-numeric display-level string becomes `0` in character-level consumers even if the row materializer preserves the text.

##### C-ENEMY-STATUS-SWITCH-04: stat lookup is a status-id switch, not a sequential exposed array

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

Interpretation: the primary scalar stat accessor maps `ECHARA_STATUS`-like ids onto row dwords. The mapping is: `1 -> m_MaxHP`, `2 -> m_MaxSP`, `4 -> m_MaxSLD`, `9 -> m_AtkP`, `10 -> m_DefP`, `11 -> m_AtkM`, `12 -> m_DefM`, `13 -> m_Agi`, `14 -> m_Crt`, `15 -> m_Hit`, and `16 -> m_Avd`. `m_CrtDef` is present at `+0x58` and is copied/materialized, but this particular accessor skips it. Therefore do not infer that every stat field is addressable through `GetEnemyStatus` merely because it is physically adjacent.

##### C-ENEMY-UI-FLAGS-05: UI flags are individually read; shield display also requires a live shield value

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

Interpretation: `m_HpUI`, `m_LevelUI`, `m_ShieldUI`, and `m_DisplaySpecialSkillGauge` have separate row reads at `+0xCE`, `+0xCD`, `+0xCF`, and `+0xE0`. If the row is missing, the HP/level/shield functions default to visible (`1`), but special-gauge display defaults to false. Shield display is additionally gated by `HasSLD(this)`, so setting `m_ShieldUI=true` is insufficient when the live shield state is absent.

##### C-ENEMY-SPECIAL-GAUGE-06: enemy special-gauge fields are read at mutation time, not merely copied into setup state

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

Interpretation: `m_MaxSpecialSkillValue`, `m_ReduceSpecialSkillValueBreak`, and `m_GenerateSpecialSkillValueOrverTurn` are active runtime parameters. They are not just copied into a constructed enemy. The gauge mutation routines re-query the `EnemyID` row when adding, reducing on break, or repairing over turn. This implies that changing these DB values affects the formula paths that call these getters, while the current live gauge value remains separate character/save state.

##### C-ENEMY-REWARD-DROP-STEAL-07: rewards, drops, and steals are direct row reads with positive-id/count gates

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

Interpretation: reward and steal fields are not schema-only. `m_Exp`, `m_Money`, and `m_JP` have simple scalar getters at `+0x8C`, `+0x90`, and `+0x98`. `m_DropReward` at `+0x9C` is the `EnemyDropID` row id. `m_StealReward` at `+0xA0` and `m_StealLeafNum` at `+0xA4` have boolean overloads that return false unless the copied value is positive, so `0` and negative values are semantically “not present” in those overloads.

##### C-ENEMY-REVERSE-INDEX-08: `m_RaceIndices` and `m_TypeID` support reverse scans across all enemy rows

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

Interpretation: `m_RaceIndices` is not a scalar race field. Race membership is tested through `HasEnemyRace(enemyId, raceId)` and then used to build a derived array of enemy ids. Likewise, `m_TypeID` is not only a presentation pointer; `GetSameTypeEnemies` performs a full scan over all `EnemyID` rows and compares row `+0x18` against the requested type id. This makes `m_TypeID` a presentation link and a type-classification key.

##### C-ENEMY-OFFSETMAP-09: compact `EnemyID` row layout and consumer classification

| Offset | Key(s) | Evidence grade | Runtime interpretation |
|---:|---|---|---|
| `+0x0C` | `m_Label` | label index / lookup proof | Used by `FDbAccessHelper` label maps; label-to-id lookup returns row `m_id`. |
| `+0x14` | `m_DisplayName` | direct consumer | Game-text id, resolved as `AGameTextManager::GetGameText(..., 4u, DisplayName, 0)`. |
| `+0x18` | `m_TypeID` | direct consumer | Bridge to all `EnemyTypeID` presentation data; also used by same-type reverse scans. |
| `+0x1C` | `m_WeakID` | direct consumer via weak-flag/resist paths | Default weakness row when no explicit weak override is supplied. |
| `+0x20` | `m_DisplayLevel` | direct consumer | UTF-16 string parsed with `_wtoi`; not a plain integer in the row. |
| `+0x30/+0x64` | `m_BreakRate`, `m_DamageRate` | direct getter/materialized scalar | Damage-rate parameters; `m_BreakRate` has a direct getter. |
| `+0x34..+0x60` | `m_MaxSLD`, `m_MaxHP`, `m_MaxSP`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_CrtDef`, `m_Hit`, `m_Avd` | direct status switch plus materialization | Most fields are returned by `GetEnemyStatus`; `m_CrtDef` is materialized but not reached by the shown switch. |
| `+0x68/+0x70` | `m_ResistAilmentID`, `m_ResistAilment` | materialized / adjacent skill-resist evidence | Real row fields; exact ailment-resistance consumer is less direct than weak/resist consumers. |
| `+0x80/+0x84` | `m_TacticalAssignID`, `m_SkillsID` | direct getter and tactical skill membership | Tactics and skill-list ids; `m_SkillsID` is resolved through `TacticalSkillList`. |
| `+0x88` | `m_IsBoss` | direct getter/materialized byte | Boss flag; used by boss-condition and presentation paths, but behavior remains context-dependent. |
| `+0x8C/+0x90/+0x94/+0x98` | `m_Exp`, `m_Money`, `m_PetExp`, `m_JP` | direct getters for Exp/Money/JP; materialized `PetExp` | Result rewards; `PetExp` remains materialized with incomplete gameplay evidence. |
| `+0x9C/+0xA0/+0xA4` | `m_DropReward`, `m_StealReward`, `m_StealLeafNum` | direct consumers | Drop table id and steal values; steal overloads require positive values. |
| `+0xA8` | `m_EventDropRewards` | direct array getter and drop override evidence | Event-drop override array; consumed with drop-result logic. |
| `+0xB8` | `m_StatusOffset` | direct UI offset getter | Two-float vector used for battle status display placement. |
| `+0xC8` | `m_Bottle` | materialized / group-slot correlation evidence | Real row scalar; current best interpretation remains multipart ordinal / panel-part index. |
| `+0xCC/+0xCD/+0xCE/+0xCF` | `m_NameUI`, `m_LevelUI`, `m_HpUI`, `m_ShieldUI` | direct consumers for level/HP/shield; name consumer incomplete | Level/HP/shield alter UI visibility; `m_NameUI` is materialized but lacks the same direct getter pattern in the recovered family. |
| `+0xD0` | `m_RaceIndices` | direct membership and reverse-scan consumer | Array of race ids, not a scalar enum. |
| `+0xE0/+0xE4/+0xE8/+0xEC` | `m_DisplaySpecialSkillGauge`, `m_GenerateSpecialSkillValueOrverTurn`, `m_ReduceSpecialSkillValueBreak`, `m_MaxSpecialSkillValue` | direct UI and mutation consumers | Enemy BP/special-gauge display and mutation parameters. |
| `+0xF0/+0xF1/+0xF2/+0xF3` | `m_OutsideTarget`, `m_NotAction`, `m_UnknownLv`, `m_Immortal` | direct setup seed for outside/not-action/immortal; materialized `UnknownLv` plus UI-path evidence from prior notes | Initial battle-character flags. Outside/not-action/immortal are explicitly seeded after `SetupParams` reset; exact `UnknownLv` consumer should be treated conservatively unless testing the level UI path. |


### (**) `EnemyReinforcements`
This is a battle encounter helper table that defines what extra enemies can be spawned mid-fight (reinforcements) and where they should appear
- `m_Reinforcements` takes `EnemyID.m_id` as input
- `m_Positions` takes `BattlePositions.m_id` as input (the respective enemy group's formation need not have this position specified)
The only asset which uses `EnemyReinforcements.m_id` is `SkillAvailID` under `Skill/`.
#### Runtime reading semantics
##### C-REINF-01: EnemyReinforcements row resolver

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_Positions` | `row-owned TArray, destructor frees this->m_Positions` (array); no shorter named assignment survived in the cited block | The row resolver returns an `EnemyReinforcements` row with stride `0x38`; destructor/copy symbols preserve `m_Positions` as the companion row-owned `TArray`. The precise spawn consumer is not recovered here. | negative / partial | `C-REINF-01` |
| `m_Reinforcements` | `row-owned TArray, destructor frees this->m_Reinforcements` (array); no shorter named assignment survived in the cited block | The row resolver returns an `EnemyReinforcements` row with stride `0x38`; destructor/copy symbols preserve `m_Reinforcements` as a row-owned `TArray`. The precise gameplay consumer beyond `SkillAvailID` was not recovered in these snippets. | negative / partial | `C-REINF-01` |

### (****) `EnemyGroups`
Chronicles all enemy groups, these primarily determine:
- which `EnemyID`s spawn together,
- formation and battle-event/abort context.

An entry has to be added here for your battle to trigger in-game.

- `m_Label`: ensure this label is unique/does not conflict with existing entries for newly added enemy groups. Events trigger fights against enemy groups using this label. If you plan on adding an entry to the Monster Arena, you will not be able to use that entry again for other purposes without causing the Monster Arena menu to appear after defeating it and possibly causing crashes
- `m_Inescapable`: Flag which determines whether you can flee from a fight
- `m_DisableRetire`: Always set to false in OT0, does not appear to be functional in OT0. Might be related to the ability of suspending your phone during the fight in CotC?
- `m_SkipResult`: Flag which determines whether the battle results screen (XP, Money, JP gained, etc.) is skipped
- `m_IgnoreDefeat`: Flag which stops the Game Over screen from appearing when defeated by the enemy group. Only used by King Pardis III, Edoras Defenders of Fame, Power, Wealth, and the Colossal Blightant. Appears to be overridden if the enemy is in the Monster Arena and will put you back there after defeat.
- `m_SkipBgmResult`: Flag which determines whether the victory theme plays
- `m_BanGodBeast`: Disables the usage of Divine Beast in battles. Unused in OT0. May block Divine Beast skills in the mod when added to some character's skill board (untested).
- `m_PlayerMemberSetID`: Forces your party to consist out of the characters specified in the referenced `PlayerMemberSetID` (see also [How to add custom characters](customchars_en.md))
- `m_Bgm`: Sets the background music for the fight, see `SoundList` under `Sound/` for viable options
- `m_NightBgm`: Sets the background music for the fight at night. Unused in OT0, untested if functional.
- `m_DarkBgm`: The same as `m_NightBgm`? CotC's entries appear to always have the same values in both keys.
- `m_BgmType`: Exact usage is unclear but `Kingship_structs.hpp` defines the following

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

- `m_ResultBgmWin`: Specifies a custom victory theme.
- `m_ResultBgmLose`: Specifies a custom Game Over theme.
- `m_CameraSet`: Specifies the camera set to use (input: `BattleCameraSet.m_id`). Some older notes call this `m_Camera`, but the typed C-export materializer uses `m_CameraSet` (`Octopath_Traveler0-Win64-Shipping.exe.c`:17923518-17923533).
- `m_Formation`: Specifies the formation of the enemy group (input: `EnemyFormations.m_id`)
- `m_EnemyID`: Specifies the enemies on the field (inputs: `EnemyID.m_id`). Game does not immediately crash if you extend this array to more than 6 enemies but the new enemies will not show up on the timeline, lack a weakness/shield/status overlay, and the game will crash after they get a turn (error is related to corrupted allocator metadata/a corrupted pointer). Can maybe be bypassed via reinforcement calls. Treat this as an authoring hazard that still needs a current local reproduction before documenting a hard engine cap or a safe bypass.

- `m_EventIndices`: (Battle) Events played at the beginning or during the fight (in-battle dialogue, etc.), possible values are likely `BattleEventList.m_id` and not `EventList.m_id`.
- `m_AbortCondition`: Condition for aborting the battle. Viable IDs can likely be found under `BattleAbortConditions`.
- `m_DefeatPriority`: Always set to false in OT0 and CotC. Not sure what it does if anything.
- `m_ForcedWinAbortCondition`: Sets a custom condition for forcing the battle to be won. Viable IDs can likely be found under `BattleAbortConditions`.
- `m_IsAllEnemyDeadOnForceWin`: (untested) Flag that determines whether all enemies perish upon fulfilling the condition set in `m_ForcedWinAbortCondition`?
- `m_ForcedLoseAbortCondition`: Sets a custom condition for forcibly losing the battle. Viable IDs can likely be found under `BattleAbortConditions`.
- `m_ImportantFlags`: Six different flags, all of which are unused in OT0 and whose meaning could not be determined through testing (given that the array has the same length as `m_EnemyID`, there likely isn't any difference between those 6 flags besides choosing which enemy on the field to apply it to)
  - Flag 1: used primarily by Job Tower bosses in CotC.
  - Flag 2: used only by `Dice_Bolaven_Rank04_BossGr03` (battle against Idore, Roguish Riven Soldier I, and Roguish Riven Soldier II)
  - Flag 3: only used by the same enemy group as Flag 2
  - Flag 4-6: never used in either game
-  `m_PetDear`: Takes -1, 0, and 1 as values
-  `m_DisableBattleEndWipe`: Used by many final bosses (Bestower of All, Or'Galdera, Side Solistia's Galdera) and also the Magitek Armor.
-  `m_DisableBattleEndTraining`: Prevents party members in the training grounds from gaining XP, JP, etc. Used in the Emerald Direwolf fight (only with Laurana on the field), the Bestower of All Ch. 7 fights during the Sazantos flashback, etc.
-  `m_DisableSupporter`: Flag which determines whether you can summon helpers in battle
-  `m_EncountEffectTypeID`: Specifies special encounter effects (e.g. FF6 collab encounters). Arena battles need this set to 0 or else they'll soft-lock the game. Viable IDs can be found under `EncountEffectTypeID`. The SDK specifies the following effects: 0=None, 2=Whiteout, 3=Blackout.
-  `m_EnablePlayerShield`: Always set to false in both OT0 and CotC. 
-  `m_DisableGrade`: Always set to false in OT0, not in CotC. Likely a flag to ignore weapons' grades.
-  `m_IsContainRareEnemy`: Likely a flag to tell the game which enemy groups are considered to contain rare enemies so accessories increasing the encounter rate of rare enemies increases.

#### Runtime reading semantics
The earlier address-level notes for this table are superseded here by named `FEnemyGroupsBase`, `UEnemyDB`, and `ASymbolEnemy` evidence from the typed C export. The group materializer, group resolver, and symbol-enemy slot-selection path are all cited below; no remaining address-only claim is needed for this section.

**Primary C evidence:**

##### C-GROUP-01: EnemyGroups row resolver

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

##### C-GROUP-02: EnemyGroups materializer with concrete row offsets

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

##### C-GROUP-03: EnemyGroups direct scalar consumers

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

##### C-SYMBOL-03: Symbol enemy runtime assignment and GroupId/Index bridge to EnemyGroups

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_AbortCondition` | `C-GROUP-02:17923540` `outData->m_AbortCondition = *(_DWORD *)(v18 + 88);` | The key is read as a 32-bit scalar at row offset `+0x58`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_BanGodBeast` | `C-GROUP-02:17923526` `outData->m_BanGodBeast = *(_BYTE *)(v18 + 25);` | The key is read as a byte/Boolean lane at row offset `+0x19`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_Bgm` | `C-GROUP-02:17923528` `outData->m_Bgm = *(_DWORD *)(v18 + 32);`<br>`C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | The key is read as a 32-bit scalar at row offset `+0x20`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_BgmType` | `C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | The key is read as a 32-bit scalar at row offset `+0x2C`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_Camera` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | No `m_Camera` field is recovered for OT0 `EnemyGroups`. The materializer names the live field `m_CameraSet` at row `+0x30`; `m_Camera` should be treated as an obsolete note or older alias. | recovered | `C-GROUP-02` |
| `m_CameraSet` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | The key is read as a 32-bit scalar at row offset `+0x30`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_DarkBgm` | `C-GROUP-02:17923530` `outData->m_DarkBgm = *(_DWORD *)(v18 + 40);` | The key is read as a 32-bit scalar at row offset `+0x28`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_DefeatPriority` | `C-GROUP-02:17923541` `outData->m_DefeatPriority = *(_BYTE *)(v18 + 92);` | The key is read as a byte/Boolean lane at row offset `+0x5C`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndTraining` | `C-GROUP-02:17923550` `outData->m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);` | The key is read as a byte/Boolean lane at row offset `+0x85`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndWipe` | `C-GROUP-02:17923549` `outData->m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);` | The key is read as a byte/Boolean lane at row offset `+0x84`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableGrade` | `C-GROUP-02:17923554` `outData->m_DisableGrade = *(_BYTE *)(v18 + 141);` | The key is read as a byte/Boolean lane at row offset `+0x8D`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableRetire` | `C-GROUP-02:17923522` `outData->m_DisableRetire = *(_BYTE *)(v18 + 21);` | The key is read as a byte/Boolean lane at row offset `+0x15`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableSupporter` | `C-GROUP-02:17923551` `outData->m_DisableSupporter = *(_BYTE *)(v18 + 134);` | The key is read as a byte/Boolean lane at row offset `+0x86`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnablePlayerShield` | `C-GROUP-02:17923553` `outData->m_EnablePlayerShield = *(_BYTE *)(v18 + 140);` | The key is read as a byte/Boolean lane at row offset `+0x8C`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_EncountEffectTypeID` | `C-GROUP-02:17923552` `outData->m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);` | The key is read as a 32-bit scalar at row offset `+0x88`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnemyID` | `C-GROUP-02:17923535` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EnemyID,` | The key is read as a `TArray` header at row offset `+0x38`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_EventIndices` | `C-GROUP-02:17923538` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EventIndices,` | The key is read as a `TArray` header at row offset `+0x48`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedLoseAbortCondition` | `C-GROUP-02:17923544` `outData->m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);` | The key is read as a 32-bit scalar at row offset `+0x68`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedWinAbortCondition` | `C-GROUP-02:17923542` `outData->m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);` | The key is read as a 32-bit scalar at row offset `+0x60`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_Formation` | `C-GROUP-02:17923533` `outData->m_Formation = *(_DWORD *)(v18 + 52);` | The key is read as a 32-bit scalar at row offset `+0x34`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_IgnoreDefeat` | `C-GROUP-02:17923524` `outData->m_IgnoreDefeat = *(_BYTE *)(v18 + 23);` | The key is read as a byte/Boolean lane at row offset `+0x17`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_ImportantFlags` | `C-GROUP-02:17923546` `(TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&outData->m_ImportantFlags,` | The key is read as a `TArray` header at row offset `+0x70`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_Inescapable` | `C-GROUP-02:17923521` `outData->m_Inescapable = *(_BYTE *)(v18 + 20);` | The key is read as a byte/Boolean lane at row offset `+0x14`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsAllEnemyDeadOnForceWin` | `C-GROUP-02:17923543` `outData->m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);` | The key is read as a byte/Boolean lane at row offset `+0x64`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsContainRareEnemy` | `C-GROUP-02:17923555` `outData->m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);` | The key is read as a byte/Boolean lane at row offset `+0x8E`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_Label` | `C-GROUP-02:17923520` `outData->m_Label = *(FName *)(v18 + 12);` | The key is read as an `FName` at row offset `+0x0C`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_NightBgm` | `C-GROUP-02:17923529` `outData->m_NightBgm = *(_DWORD *)(v18 + 36);` | The key is read as a 32-bit scalar at row offset `+0x24`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_PetDear` | `C-GROUP-02:17923548` `outData->m_PetDear = *(_DWORD *)(v18 + 128);` | The key is read as a 32-bit scalar at row offset `+0x80`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_PlayerMemberSetID` | `C-GROUP-02:17923527` `outData->m_PlayerMemberSetID = *(_DWORD *)(v18 + 28);` | The key is read as a 32-bit scalar at row offset `+0x1C`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_ResultBgmLose` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_ResultBgmLose` symbol or unambiguous asset-specific offset was recovered in the C export. The field is therefore documented as unrecovered, not inferred. | negative / partial | not recovered |
| `m_ResultBgmWin` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_ResultBgmWin` symbol or unambiguous asset-specific offset was recovered in the C export. The field is therefore documented as unrecovered, not inferred. | negative / partial | not recovered |
| `m_SkipBgmResult` | `C-GROUP-02:17923525` `outData->m_SkipBgmResult = *(_BYTE *)(v18 + 24);` | The key is read as a byte/Boolean lane at row offset `+0x18`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |
| `m_SkipResult` | `C-GROUP-02:17923523` `outData->m_SkipResult = *(_BYTE *)(v18 + 22);` | The key is read as a byte/Boolean lane at row offset `+0x16`. The group row is resolved by `GroupID` with stride `0x90`, and `FDbAccessHelper<FEnemyGroupsBase>::GetData` copies this exact lane into `outData`. | recovered | `C-GROUP-01`, `C-GROUP-02` |


### (**) `EnemyModeID`
Tells the game which mode ID refers to which effect to be applied to the field or enemy. These effects can be assigned to avails. Used when bosses enter boost mode and display special effects.

#### Runtime reading semantics
`EnemyModeID` is not enum-bound at runtime. The engine resolves the integer mode id through the `EnemyModeID` database access helper and then reads an 88-byte row. The live mode-change system stores the active mode as a `MODE_CHANGE` ailment whose `FAilmentInfo.m_LinkedEnemyModeID` is compared against the requested `EnemyModeID`; therefore, expansion is constrained by database key/index availability and by callers that provide the mode id, not by a fixed SDK enum.

**Primary C evidence:**

##### C-MODE-01: `EnemyModeID` row lookup uses DB key resolution and an 88-byte stride

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

##### C-MODE-02: full table surface is exposed through `ADatabaseDefineStatic`

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

##### C-MODE-03: copied row layout and scalar/array lanes

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

##### C-MODE-04: mode-change remove conditions are read from row `+0x18/+0x28`

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

##### C-MODE-05: mode-change remove conditions override the default ailment remove-condition lanes

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

##### C-MODE-06: row `+0x38` is the attach-effect id applied while the mode is active

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

##### C-MODE-07: row `+0x14` controls immediate removal of three control ailments on entry

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

##### C-MODE-08: active mode identity is stored as `FAilmentInfo.m_LinkedEnemyModeID`

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

**Field-level interpretation:**

| Runtime lane / key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_id` | `C-MODE-01` `GetArrayIndex(..., ModeID)` and `Data[88 * ArrayIndex]`; `C-MODE-03` row `+0x08` copy | `ModeID` is a database key. The runtime does not use an SDK enum as a closed domain; it resolves the supplied integer through the DB access helper and indexes the `EnemyModeID` data list. | recovered | `C-MODE-01`, `C-MODE-03` |
| row `+0x14` enter-mode ailment-clear flag | `C-MODE-07` `if ( EnemyModeDB && *(_BYTE *)&EnemyModeDB[20] )` | When this byte is nonzero, entering the mode removes `PARALYSIS`, `FASCINATION`, and `SLEEP` from the enemy. The decomp does not preserve a reliable exported field name for this byte in the consumer, so the guide identifies it by offset and effect. | recovered, name not preserved in consumer | `C-MODE-07` |
| remove-condition array, likely the table's condition lane | `C-MODE-04` `*pConditions = ... (v8 + 24)`; `C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | This array is used only as a mode-change ailment removal predicate set in the recovered consumer. It is slot-compatible with the common condition evaluator: condition id array plus parameter array. | recovered | `C-MODE-04`, `C-MODE-05` |
| remove-parameter array, likely the table's parameter lane | `C-MODE-04` `*pParams = ... (v8 + 40)`; `C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | This array supplies per-slot parameters for the removal predicates. It should be edited in parallel with the condition array; mismatched lengths risk evaluator slots reading absent defaults or failing checks. | recovered | `C-MODE-04`, `C-MODE-05` |
| attach-effect id at row `+0x38` | `C-MODE-06` `SetAttachEffect(this, *(_DWORD *)&EnemyModeDB[56])` | While entering a mode, the engine reads the 32-bit id at row `+0x38` and passes it directly to `AQPBattleCharacterBase::SetAttachEffect`. This is the active mode aura/attached VFX lane, distinct from `EnemyTypeID`'s normal attach-effect lane. | recovered, exact property name not preserved in consumer | `C-MODE-06` |
| active mode state | `C-MODE-08` `info.m_AilmentType == MODE_CHANGE`; `info.m_LinkedEnemyModeID == EnemyModeID` | The active mode is represented by a `MODE_CHANGE` ailment record rather than by writing the `EnemyModeID` row into the character. The row configures entry/exit behavior; the live identity is the linked mode id stored inside `FAilmentInfo`. | recovered | `C-MODE-08` |
| `SkillAvailID.m_ModeChangeID` linkage | `C-MODE-05` `SkillAvailDB->m_ModeChangeID` | Avails that apply the `MODE_CHANGE` ailment provide the mode id used to retrieve mode-specific removal conditions. This is the proven path by which an avail points at `EnemyModeID`. | recovered | `C-MODE-05` |

### (**) `EnemyDropID`
Keeps track of all the different drop IDs which can be assigned to enemies.
#### Runtime reading semantics
`EnemyDropID` is a separate drop-table database. `EnemyID.m_DropReward` stores the drop-table id, not an item id. At result calculation time, the engine resolves that id to a 96-byte `EnemyDropID` row, reads parallel arrays for item ids, rates, fixed counts, variable counts, and optional event-drop quest filters, then records the selected result item into battle save-data result arrays.
**Primary C evidence:**

##### C-DROP-01: `EnemyID.m_DropReward` is read as the drop-table id from `EnemyID` row `+0x9C`

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

##### C-DROP-02: drop-table lookup uses DB key resolution and a 96-byte stride

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

##### C-DROP-03: full table surface is exposed through `ADatabaseDefineStatic`

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

##### C-DROP-04: copied row layout contains five array lanes after the id/name prefix

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

##### C-DROP-05: item ids, rates, counts, and event quest filters are exposed by offset-specific getters

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

##### C-DROP-06: event drops can override `EnemyID.m_DropReward` when a listed quest is in progress

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

##### C-DROP-07: result calculation consumes item ids, rates, and count ranges slot-wise

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

##### C-DROP-08: result UI reads the selected result item/count from battle save-data arrays

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

**Field-level interpretation:**

| Runtime lane / key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `EnemyID.m_DropReward` | `C-DROP-01` `*(_DWORD *)&result[156]` | This `EnemyID` field is the normal drop-table id. It is not the dropped item id. The resulting id is fed to `GetEnemyDropDB`. | recovered | `C-DROP-01`, `C-DROP-02` |
| `EnemyID.m_EventDropRewards` | `C-DROP-06` `UEnemyDB::GetEnemyEventDropIDs(v2)` then iterates event drop ids | Event drop ids are tried before normal drop calculation. If one event drop row has at least one quest id currently in progress, the engine sets `DropType = 1` and substitutes that drop id for the normal `m_DropReward`. | recovered | `C-DROP-06` |
| `EnemyDropID.m_id` | `C-DROP-02` `GetArrayIndex(..., DropID)` and `Data[96 * ArrayIndex]`; `C-DROP-04` row `+0x08` copy | `DropID` is resolved through the table's DB access helper. Expanding the table is a database-key/index problem rather than an enum-domain problem, subject to callers using a sufficiently wide id field. | recovered | `C-DROP-02`, `C-DROP-04` |
| item id array at row `+0x10` | `C-DROP-05` `*IDs = ... &EnemyDropDB[16]`; `C-DROP-07` `v46 = *(_DWORD *)Data` | This is the parallel array of candidate item ids. Result calculation ignores nonpositive item ids in the active slot. | recovered, exact exported property name not preserved in getter | `C-DROP-05`, `C-DROP-07` |
| rate/weight array at row `+0x20` | `C-DROP-05` `return EnemyDropDB + 32`; `C-DROP-07` iterates `DropItemRates` | This is the parallel array of candidate weights. The engine sums rates, normalizes against positive item-id slots, and uses a random roll to choose a slot. | recovered, exact exported property name not preserved in getter | `C-DROP-05`, `C-DROP-07` |
| fixed count array at row `+0x30` | `C-DROP-05` `GetDropItemCounts(DropID, 0)` returns `result + 48`; `C-DROP-07` `v54 = fixedCounts[slot]` | This is the base count added to the drop. | recovered, exact exported property name not preserved in getter | `C-DROP-05`, `C-DROP-07` |
| variable count array at row `+0x40` | `C-DROP-05` `GetDropItemCounts(DropID, 1)` returns `result + 64`; `C-DROP-07` `GetRandomRange(variableCounts[slot], 0)` | This is an additive random range. The final selected count is `fixedCount[slot] + random(0..variableCount[slot])`, followed by support/village drop-count scaling in the same routine. | recovered, exact exported property name not preserved in getter | `C-DROP-05`, `C-DROP-07` |
| event quest id array at row `+0x50` | `C-DROP-05` `GetEventDropQuestIDs` returns `EnemyDropDB + 80`; `C-DROP-06` `UQuestUtility::IsQuestInProgress(*v13)` | For event-drop rows, this array gates whether the event-drop row is used. Any positive quest id in progress is enough to activate the event drop substitution in the recovered path. | recovered, exact exported property name not preserved in getter | `C-DROP-05`, `C-DROP-06` |
| result item/count output | `C-DROP-08` reads `ResultItemID[]` and `ResultItemCount[]` | After drop calculation, UI-facing retrieval no longer reads `EnemyDropID`; it reads the materialized result arrays in battle save data. Thus changes to `EnemyDropID` affect selection/count generation, not post-hoc UI lookup. | recovered | `C-DROP-08` |

### (**) `EncountVolume`
Determines danger levels, which enemy groups can be encountered (`m_EnemyGroup` and `m_FirstEncount`, where the latter likely specifies an enemy group as the initial encounter), and the respective encounter probabilities. Encount Volumes are associated with maps in `EncountList`. Maps may change their danger level and therefore possibly encount volumes. There may be issues if the values in `m_EncountRatio` do not add up to 100.
#### Runtime reading semantics
**Primary C evidence:**

##### C-ENCOUNT-01: EncountVolume row resolver

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

##### C-ENCOUNT-02: EncountVolume materialization into current encounter volume

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

##### C-ENCOUNT-03: EncountVolume selection semantics

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_EncountRatio` | `C-ENCOUNT-02:17643576` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EncountRatio,`<br>`C-ENCOUNT-02:17643579` `if ( outEncountVolume.m_EncountRatio.AllocatorInstance.Data )`<br>`C-ENCOUNT-02:17643580` `FMemory::Free(outEncountVolume.m_EncountRatio.AllocatorInstance.Data);` | The encounter-volume row is resolved with stride `0x38`; the current-encounter materializer copies `m_EncountRatio` from row `+0x28` as a `TArray<int>`. During selection it becomes `m_CurrentRatios`. | recovered | `C-ENCOUNT-01`, `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_EnemyGroup` | `C-ENCOUNT-02:17643567` `memset(&outEncountVolume.m_EnemyGroup, 0, 32);`<br>`C-ENCOUNT-02:17643573` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EnemyGroup,`<br>`C-ENCOUNT-02:17643581` `if ( outEncountVolume.m_EnemyGroup.AllocatorInstance.Data )` plus 1 further local occurrences | The encounter-volume row is resolved with stride `0x38`; the current-encounter materializer copies `m_EnemyGroup` from row `+0x18` as a `TArray<int>`. During selection it becomes `m_CurrentGroups`. | recovered | `C-ENCOUNT-01`, `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_FirstEncount` | `C-ENCOUNT-03:17641121` `int m_FirstEncount; // edi`<br>`C-ENCOUNT-03:17641196` `m_FirstEncount = EncountVolume->m_FirstEncount;`<br>`C-ENCOUNT-03:17641199` `if ( m_FirstEncount > 0 && UEnemyDB::GetEnemyGroupDB(m_FirstEncount) )` plus 1 further local occurrences | `m_FirstEncount` is read from the materialized `FEncountVolumeBase`. If the first-encounter flag is still set and this group id resolves through `GetEnemyGroupDB`, the function returns that group before random weighted selection. | recovered | `C-ENCOUNT-03` |

#### Additional runtime evidence: encounter volumes feed both danger level and enemy-type precomputation

The encounter subsystem resolves `EncountList.m_EncountVolume` into an `EncountVolume` row and then reads the danger level and enemy-group/rate arrays. A separate helper, `GetMaxEncountEnemyTypes`, statically walks encounter lists, volumes, groups, enemy ids, and potential reinforcements to determine the maximum number of distinct enemy types that may need preallocation.

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

| Key | Runtime interpretation |
|---|---|
| `m_DangerLevel` | Read from `EncountVolume +0x0C` after `EncountList.m_EncountVolume` is resolved. |
| `m_EnemyGroup` | Stored as the row-owned array at `+0x18/+0x20`; the type-precomputation path copies this array into a temporary group-id list and resolves each `EnemyGroups` row. |
| `m_FirstEncount` | Not consumed by the shown `GetDangerLevel` or `GetMaxEncountEnemyTypes` paths. Its absence in these paths does not prove non-use globally; it only proves these two common helper paths ignore it. |
| `m_EncountRatio` | Stored as the row-owned array at `+0x28/+0x30`; it is copied with `m_EnemyGroup` when building the `FEncountVolumeBase` scratch object for danger-level operations. |

### (**) `EncountList`
OT0's random encounter rule table: Specifies which encount volumes can be encountered on which map (`m_EncountVolume` being `m_EncountVolume.m_id`), minimum number of steps until encounter (`m_EncountStepMin`), and conditions which have to be met for the encounter to occur. 

Additional runtime-relevant keys:
- `m_DisableCondition`: condition to be met to disable this encounter
- `m_RandomStepA` and `m_RandomStepB`: parameters for the random step range/curve. Both keys appear to always have the same values in both OT0 and CotC.
- `m_FirstEncountStepRatio`: Likely a multiplier specifically for the first encounter after entering the map/zone, making it earlier or later than normal.

#### Runtime reading semantics
**Primary C evidence:**

##### C-ENCOUNT-01: EncountVolume row resolver

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

##### C-ENCOUNTLIST-01: EncountList copy constructor

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_DisableCondition` | `C-ENCOUNTLIST-01:17639800` `this->m_DisableCondition = __that->m_DisableCondition;` | The `FEncountListBase` copy constructor copies `m_DisableCondition` as a scalar field. This field is part of the materialized encounter-list row before encounter-volume resolution. | recovered | `C-ENCOUNTLIST-01` |
| `m_EncountStepMin` | `C-ENCOUNTLIST-01:17639801` `this->m_EncountStepMin = __that->m_EncountStepMin;` | The `FEncountListBase` copy constructor copies `m_EncountStepMin` as a scalar field. | recovered | `C-ENCOUNTLIST-01` |
| `m_EncountVolume` | `C-ENCOUNTLIST-01:17639805` `this->m_EncountVolume = __that->m_EncountVolume;` | The `FEncountListBase` copy constructor copies `m_EncountVolume`; later encounter code uses this id to resolve an `EncountVolume` row. | recovered | `C-ENCOUNTLIST-01`, `C-ENCOUNT-01` |
| `m_FirstEncountStepRatio` | `C-ENCOUNTLIST-01:17639804` `this->m_FirstEncountStepRatio = __that->m_FirstEncountStepRatio;` | The `FEncountListBase` copy constructor copies `m_FirstEncountStepRatio` as a scalar field. | recovered | `C-ENCOUNTLIST-01` |
| `m_RandomStepA` | `C-ENCOUNTLIST-01:17639802` `this->m_RandomStepA = __that->m_RandomStepA;` | The `FEncountListBase` copy constructor copies `m_RandomStepA` as a scalar field. | recovered | `C-ENCOUNTLIST-01` |
| `m_RandomStepB` | `C-ENCOUNTLIST-01:17639803` `this->m_RandomStepB = __that->m_RandomStepB;` | The `FEncountListBase` copy constructor copies `m_RandomStepB` as a scalar field. | recovered | `C-ENCOUNTLIST-01` |

#### Additional runtime evidence: current encounter-list selection is condition-gated and map-specific

`AQPEncountManager::GetCurrentEncountList` shows the operational meaning of `m_MapId`, `m_Condition`, and `m_DisableCondition`. Rows are filtered by current map id, then rejected if the disable condition is true, and finally accepted only if all non-zero conditions in `m_Condition` evaluate true.

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

| Key | Runtime interpretation |
|---|---|
| `m_MapId` | The primary filter for `GetCurrentEncountList`; only rows with `m_MapId == current map id` remain candidates. |
| `m_DisableCondition` | If present and its quest condition evaluates true, the row is rejected before normal conditions are tested. |
| `m_Condition` | Copied into a temporary array, with zero removed, then all remaining quest conditions must pass. This is conjunctive after zero-removal, not probabilistic. |
| `m_EncountStepMin`, `m_RandomStepA`, `m_RandomStepB`, `m_FirstEncountStepRatio`, `m_EncountVolume`, `m_SoundList` | Copied verbatim into the selected `FEncountListBase` scratch object from row offsets `+0x24`, `+0x28`, `+0x2C`, `+0x30`, `+0x34`, and `+0x38`. The shown function selects and copies these values; it does not itself perform the random-step roll. |


### (*) `EncountEffectTypeID`
Keeps track of different encount effects, `m_SeId` likely specifies the sound effect to play.
#### Runtime reading semantics
**Primary C evidence:**

##### C-STATIC-01: Representative static DB accessor surface

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_SeId` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_SeId` symbol or key-level consumer was recovered in the C export. Only table exposure/static DB surfaces can be asserted from this export. | negative / partial | `C-STATIC-01` |

### (*) `EnemyGroupsByCondition`
Sets of enemy groups to spawn when certain conditions are met. Used to spawn the Accurst Flame alongside the Ringbearer in Bestower of All Chapter 7.

#### Runtime reading semantics
`EnemyGroupsByCondition` is a condition-to-group selector. The C export shows it copying two aligned arrays from the row, evaluating each condition in order, and returning the enemy-group id at the same array index as the first satisfied quest condition.

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

| Key | Runtime interpretation |
|---|---|
| `m_Conditions` | Stored as row array `+0x18/+0x20`. Each value is treated as a quest condition id and passed through `UQuestUtility::GetQuestConditionPtr` and `CheckQuestTaskCondition`. |
| `m_EnemyGroups` | Stored as row array `+0x28/+0x30`. It is positionally aligned with `m_Conditions`; the first true condition returns `m_EnemyGroups[i]`. |
| `m_id` | The function indexes `m_DataList` by the supplied integer rather than using a `GetArrayIndex` lookup in the shown path. For expansion, this is a reason to test non-contiguous ids carefully: this function behaves like an array-index accessor, not a key lookup accessor. |

### (**) `SymbolEnemyList`
Specifies what overworld sprites or objects and enemy groups are assigned to symbol/elite enemies as well as their level.
- `m_GroupId`: Enemy Group associated with this symbol enemy entry
- `m_Index`: Index of this enemy within the enemy gorup. Commonly used when multiple symbol enemies share the same GroupId or spawn controller.
- `m_Dir`: The direction the symbol enemy faces by default
- `m_ObjectId`: Overrides the symbol enemy overworld sprite with a specific object (e.g. Tatloch's ships in Bestower of Power Ch. 3)
- `m_RepopCount`: Respawn/repopulation count, i.e., how often a symbol enemy respawns. 0 by default for OT0 enemies, not true for CotC enemies (enemies do not re-appear immediately after reloading the map)
- `m_Redrawing`: Always set to 0 in both OT0 and CotC
- `m_TargetLevel1` and `m_TargetLevel2`: possibly a level range for the symbol enemy
- `m_Scale`: Scale multiplier for the enemy symbol in the field (purely visual/collision size)
- 
#### Runtime reading semantics
**Primary C evidence:**
##### C-SYMBOL-01: SymbolEnemyList row materializer

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

##### C-SYMBOL-03: Symbol enemy runtime assignment and GroupId/Index bridge to EnemyGroups

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_Dir` | `C-SYMBOL-01:17851302` `outData->m_Dir = *(_DWORD *)(v7 + 20);` | The key is read as a 32-bit scalar at row offset `+0x14`. `FSymbolEnemyList::GetData` materializes the row; `ASymbolEnemy::SetSymbolData` copies the materialized values into the live symbol enemy object. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_GroupId` | `C-SYMBOL-01:17851300` `outData->m_GroupId = *(_DWORD *)(v7 + 12);` | `m_GroupId` is copied into the symbol object, then used as the `EnemyGroups` id. `m_Index` is later used to select `EnemyGroups.m_EnemyID[m_Index]`, which becomes the symbol enemy’s `m_EnemyListId`. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Index` | `C-SYMBOL-03:17650576` `int m_Index; // eax`<br>`C-SYMBOL-03:17650593` `this->m_data.m_Index = data->m_Index;`<br>`C-SYMBOL-03:17650657` `m_Index = this->m_data.m_Index;` plus 3 further local occurrences | `m_Index` is the group-slot selector in the symbol-enemy path: after `m_GroupId` resolves an `EnemyGroups` row, the runtime reads `EnemyGroups.m_EnemyID[m_Index]`. | recovered | `C-SYMBOL-03` |
| `m_ObjectId` | `C-SYMBOL-01:17851303` `outData->m_ObjectId = *(_DWORD *)(v7 + 24);` | The key is read as a 32-bit scalar at row offset `+0x18`. `FSymbolEnemyList::GetData` materializes the row; `ASymbolEnemy::SetSymbolData` copies the materialized values into the live symbol enemy object. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Redrawing` | `C-SYMBOL-01:17851305` `outData->m_Redrawing = *(_DWORD *)(v7 + 32);` | The key is read as a 32-bit scalar at row offset `+0x20`. `FSymbolEnemyList::GetData` materializes the row; `ASymbolEnemy::SetSymbolData` copies the materialized values into the live symbol enemy object. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_RepopCount` | `C-SYMBOL-01:17851304` `outData->m_RepopCount = *(_DWORD *)(v7 + 28);` | The key is read as a 32-bit scalar at row offset `+0x1C`. `FSymbolEnemyList::GetData` materializes the row; `ASymbolEnemy::SetSymbolData` copies the materialized values into the live symbol enemy object. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Scale` | `C-SYMBOL-01:17851308` `outData->m_Scale = *(float *)(v7 + 44);` | The key is read as a 32-bit float at row offset `+0x2C`. `FSymbolEnemyList::GetData` materializes the row; `ASymbolEnemy::SetSymbolData` copies the materialized values into the live symbol enemy object. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel1` | `C-SYMBOL-01:17851306` `outData->m_TargetLevel1 = *(_DWORD *)(v7 + 36);` | The key is read as a 32-bit scalar at row offset `+0x24`. `FSymbolEnemyList::GetData` materializes the row; `ASymbolEnemy::SetSymbolData` copies the materialized values into the live symbol enemy object. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel2` | `C-SYMBOL-01:17851307` `outData->m_TargetLevel2 = *(_DWORD *)(v7 + 40);` | The key is read as a 32-bit scalar at row offset `+0x28`. `FSymbolEnemyList::GetData` materializes the row; `ASymbolEnemy::SetSymbolData` copies the materialized values into the live symbol enemy object. | recovered | `C-SYMBOL-01`, `C-SYMBOL-03` |

#### Additional runtime evidence: `SymbolEnemyList.m_Index` is a group-slot selector, with optional type replacement

The symbol-enemy initializer copies the symbol group and symbol row into actor state, then resolves `SymbolEnemyList.m_GroupId` into an `EnemyGroups` row. `SymbolEnemyList.m_Index` selects an entry from `EnemyGroups.m_EnemyID`; the selected enemy id becomes `m_EnemyListId` on the symbol actor.

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

| Key | Runtime interpretation |
|---|---|
| `SymbolEnemyGroupList.m_StartSymbolEnemyId` | Copied directly into the symbol actor's `m_group` state. The shown initializer does not evaluate it; it preserves it as group metadata. |
| `SymbolEnemyGroupList.m_SymbolEnemyId` | Copied as an array into actor state. The shown initializer does not sample probabilities; it preserves the candidate-symbol array. |
| `SymbolEnemyGroupList.m_probability` | Copied as an array into actor state alongside `m_SymbolEnemyId`; probability semantics are therefore outside this initializer. |
| `SymbolEnemyList.m_GroupId` | Used as a key into `EnemyGroups.m_DBAccess`; the resulting `EnemyGroups` row provides the concrete battle enemy slots. |
| `SymbolEnemyList.m_Index` | Used as an array index into `EnemyGroups.m_EnemyID`; this is the strongest runtime proof that it is a group-slot selector. |
| `m_Dir`, `m_ObjectId`, `m_RepopCount`, `m_Redrawing`, `m_TargetLevel1`, `m_TargetLevel2`, `m_Scale` | Copied directly into the symbol actor's `m_data` struct. This proves runtime materialization. The excerpt does not by itself prove each field's later behavioral consumer. |
| `nReplaceTypeID` / `EnemyID.m_TypeID` | If an explicit replacement type id is supplied it is used; otherwise the actor maps `(symbol id, index)` to an enemy id, reads that enemy's `m_TypeID`, and resolves the type row for call SE. |

### (**) `SymbolEnemyGroupList`
Appears to assign symbol enemy IDs (`m_id` from the `SymbolEnemyList` table) to specific internal IDs that refer to particular symbol enemies on certain maps. The `m_SymbolEnemyId` array may determine which symbol enemies appear in a given spot after repopulation, once the symbol enemy under `m_StartSymbolEnemyId` has been defeated, with probabilities specified for each symbol enemy under `m_probability`. Neither CotC nor OT0 appear to use these arrays.
#### Runtime reading semantics
**Primary C evidence:**
##### C-SYMBOL-02: SymbolEnemyGroupList materializer/operator=

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

##### C-SYMBOL-03: Symbol enemy runtime assignment and GroupId/Index bridge to EnemyGroups

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_StartSymbolEnemyId` | `C-SYMBOL-02:17844263` `this->m_StartSymbolEnemyId = __that->m_StartSymbolEnemyId;` | The assignment operator copies `m_StartSymbolEnemyId` directly into the live symbol group object. | recovered | `C-SYMBOL-02`, `C-SYMBOL-03` |
| `m_SymbolEnemyId` | `C-SYMBOL-02:17844265` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_SymbolEnemyId,`<br>`C-SYMBOL-02:17844266` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_SymbolEnemyId);` | The assignment operator copies `m_SymbolEnemyId` as a `TArray<int>` into the live symbol group object. | recovered | `C-SYMBOL-02`, `C-SYMBOL-03` |
| `m_id` | `C-SYMBOL-02:17844262` `this->m_id = __that->m_id;` | The key is read as a 32-bit scalar at row offset `+0x08`. The assignment operator preserves the group-list id when materialized. | recovered | `C-SYMBOL-02` |
| `m_probability` | `C-SYMBOL-02:17844268` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_probability,`<br>`C-SYMBOL-02:17844269` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_probability);` | The assignment operator copies `m_probability` as a `TArray<int>` into the live symbol group object. No probability-selection consumer is recovered in the cited custom-enemy path. | recovered | `C-SYMBOL-02`, `C-SYMBOL-03` |


## Under `Battle/`
### (***) `BattleCameraParams`
IDs which correspond to battle camera presets/configurations, value `-1.0` appears to be interpreted as "use default" by the game:
- `m_PosotionX/Y/Z`: position of the battle camera in 3D space
- `m_RotationX/Y`: Rotation/Tilt of the camera in X/Y direction
- `m_FOV`: Field of view. A narrow field of view = zoomed-in/telephoto look. Keep in mind that the party's position is fixed when changing the FOV.
- `m_SequencerPath` Cinematic/Sequencer binding, likely points to a Level Sequence or some Sequencer-driven camera rig asset used for battle camera cuts/targeting
- `m_FocalDistance`, `m_FocusDistance` distance to the focus plane (one may be legacy vs new naming (CotC only has `m_FocalDistance`), or different systems). Both are always set to `-1.0` in OT0, CotC uses different focal distances.
- `m_FocalRegion`: size of the in-focus region
- `m_NearBlurSize` and `m_FarBlurSize`: blur intensities depending on distance
- `m_MaxBokehSize`: cap for bokeh blur
- `m_Scale`: likely a camera distance scale/shot scale override

#### Runtime reading semantics
##### C-CAMERA-01: BattleCameraParams row resolver

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

##### C-CAMERA-02: BattleCameraParams struct copy

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_FOV` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FarBlurSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalDistance` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalRegion` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocusDistance` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_MaxBokehSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_NearBlurSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_Scale` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_SequencerPath` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy` (packed); no shorter named assignment survived in the cited block | `AQPBattleManager::GetCameraParam` resolves a `BattleCameraParams` row with stride `0x60`; the recovered struct-copy code then copies the row as packed scalar/string lanes. The decomp does not retain reliable per-field names for all scalar camera coordinates, so offsets beyond the copied sequence should be treated cautiously. | recovered | `C-CAMERA-01`, `C-CAMERA-02` |


#### Deep runtime analysis: `BattleCameraParams` row semantics, camera-set indirection, and DOF fallback

The camera parameter table is consumed through two distinct paths. First, the active `BattleCameraSet` maps an execution camera type to a concrete `BattleCameraParams.m_id`. Second, the resolved row is interpreted as a fixed 96-byte record: `m_id` at `+0x08`, `m_SequencerPath` as an `FString` beginning at `+0x10`, transform/FOV floats at `+0x20..+0x34`, and depth-of-field floats at `+0x38..+0x58`. This means the camera-parameter row is not merely an editor preset; it is the direct runtime payload copied into battle camera state.

##### C-CAMERAPARAMS-LOOKUP-01: id lookup, loaded-table requirements, and 96-byte row stride

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

Interpretation: `BattleCameraParams` is key-indexed through the database access helper and the live row stride is `0x60`. The surprising dependency on `m_BattleCameraSet.m_Loaded` is semantically important: parameter rows are not considered usable unless the set table has also been loaded, because most callers reach camera params through the current camera set.

##### C-CAMERAPARAMS-SET-INDIRECTION-02: camera type to camera-param id indirection

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

Interpretation: `BattleCameraParams` entries are normally selected through `BattleCameraSet`, not by hard-coded camera-param ids. Camera type `1` is the neutral camera, `3` start, `4` break, `5` victory, and so forth. Therefore a camera-param row may be perfectly valid as data while still unused unless some camera set points to it.

##### C-CAMERAPARAMS-TRANSFORM-CACHE-03: transform/FOV floats copied into battle-manager camera caches

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

Interpretation: the transform block is exactly `+0x20..+0x34`: position vector, two rotation floats, and FOV. The misspelled exported key `m_PosotionX/Y/Z` should therefore be read as a literal camera position triple, not as a symbolic label or unused editor metadata.

##### C-CAMERAPARAMS-SEQUENCER-PATH-04: sequencer binding is tested as a non-empty trimmed path

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

Interpretation: `m_SequencerPath` is not interpreted by this predicate as an arbitrary boolean. The runtime copies the `FString` at `+0x10/+0x18`, removes spaces, and treats the camera as sequencer-driven only when the resulting string length exceeds one UTF-16 element. A whitespace-only path is therefore equivalent to no sequencer path for this check.

##### C-CAMERAPARAMS-DOF-SENTINEL-05: depth-of-field fields use `INVALID_PARAM` sentinel semantics

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

Interpretation: `-1.0`-style values are not merely conventional defaults. They are compared against `ABattleCameraBase::INVALID_PARAM`; fields at or below that sentinel are skipped and do not overwrite the active cine-camera setting. This applies independently to focal distance, focal region, near/far transition regions, near/far blur, scale, max bokeh size, and focus distance.

##### C-CAMERAPARAMS-NEUTRAL-DOF-FALLBACK-06: DOF interpolation falls back through the neutral camera row

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

Interpretation: DOF interpolation uses float indices `14..22`, i.e. row offsets `+0x38..+0x58`. If either endpoint contains a sentinel value, the neutral camera row supplies the fallback value before interpolation. Thus neutral-camera DOF is the default source for other camera modes, not merely another camera preset.

Field map recovered from these consumers:

| Exported key / lane | Runtime locus | Interpretation |
|---|---:|---|
| `m_id` | `+0x08`; DB key lookup | Selected by DB access helper, usually after camera-set indirection. |
| `m_SequencerPath` | `+0x10` pointer, `+0x18` length | Copied as `FString`, spaces removed, non-empty path enables sequencer-camera behavior. |
| `m_PosotionX/Y/Z` | `+0x20/+0x24/+0x28` | Camera world-position vector copied into battle-manager camera caches. |
| `m_RotationX/Y` | `+0x2C/+0x30` | Two rotation components copied with the position vector. |
| `m_FOV` | `+0x34` | FOV copied alongside transform fields for neutral/start/break/victory/etc. camera states. |
| `m_FocalDistance` | `+0x38` / float index `14` | DOF field; skipped if `<= INVALID_PARAM`, otherwise applied to cine-camera post-process. |
| `m_FocalRegion` | `+0x3C` / float index `15` | DOF field; uses the same sentinel/fallback discipline. |
| near/far DOF transition lanes | `+0x40/+0x44` / indices `16/17` | Present in runtime even though the guide-facing schema may not name them prominently. |
| `m_NearBlurSize`, `m_FarBlurSize` | `+0x48/+0x4C` / indices `18/19` | Applied independently to cine-camera post-process when above sentinel. |
| `m_Scale` | `+0x50` / index `20` | Applied to depth-of-field scale, not to battle-layout scale. |
| `m_MaxBokehSize` | `+0x54` / index `21` | Applied to cine-camera max bokeh size when above sentinel. |
| `m_FocusDistance` | `+0x58` / index `22` | Handled by the focus-settings branch after the post-process writes. |

### (***) `BattleCameraSet`
Sets of camera configurations which are assigned to enemy groups (`BattleCameraSet.m_id`=`EnemyGroups.m_Camera`), does not exist in CotC (CotC only uses one neutral camera configuration in battle).

`Kingship_structs.hpp` gives the following labels to some camera sets in OT0:

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

#### `BattleCameraSet` as a camera-type dispatch table

`BattleCameraSet` is a compact table of camera-param ids. It is looked up by scanning the table for `m_id`, then `ABattleCameraBase::GetCameraParamID` dispatches runtime camera types to the relevant field. Consequently, `EnemyGroups.m_CameraSet` selects a *family* of camera-parameter rows, not a single camera row.

##### C-CAMERASET-LOOKUP-01: camera-set rows are scanned by `m_id` and have a 56-byte stride

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

Interpretation: unlike `BattleCameraParams`, this particular resolver performs a linear row scan over `m_DataList`. The live row stride is `0x38`, and `targetCameraSetID <= 0` is treated as no set.

##### C-CAMERASET-TYPE-DISPATCH-02: field-level dispatch by camera type

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

Interpretation: expanding or editing `BattleCameraSet` should be understood as editing this dispatch table. A field can be unused in ordinary combat if no runtime camera type reaches it, but it is still a live field in the set record.

### (***) `BattlePositions`
Defines position IDs used in e.g. `EnemyFormations` or `EnemyReinforcements`. `m_Priority` may determine which sprite appears in front of the other in case of conflicting coordinates or the order in which enemies are selected.
#### Runtime reading semantics
##### C-FORM-02: Formation position resolution into BattlePositions

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

##### C-BPOS-01: BattlePositions row resolver

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_Priority` | `+0x20` (scalar); no shorter named assignment survived in the cited block | After a formation slot yields a `BattlePositions` id, `GetBattlePositionDB` resolves a `0x28`-byte row. `GetEnemyPlacement` reads the priority from `BattlePositions +0x20` after copying XYZ from the coordinate array at `+0x10`. | recovered | `C-BPOS-01`, `C-FORM-02` |


#### `BattlePositions` as a shared coordinate table

`BattlePositions` is not restricted to enemy formations. The runtime also uses it for raid view, player-side staging, reinforcement placement, and layout-set anchoring. The row itself is key-indexed and has a 40-byte stride.

##### C-BATTLEPOSITIONS-COORDINATES-01: position row lookup and coordinate extraction

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

Interpretation: the public coordinate helper dereferences the position-vector lane from the row and writes an `X/Y/Z` vector. The C export proves the table’s role as a general coordinate service; formation membership is only one consumer.

### (***) `BattleEventList`
Likely the events which can be assigned to enemy groups under `EnemyGroups.m_EventIndices`. 
- `m_IsMustPlay`: whether an event is skippable(?)
- `m_EventCommand`: the command script ID to execute when triggered (points into `BattleEventCommand`)
- `m_EventConditions`: Condition type IDs, likely the same as in `m_Conditions` arrays in other files (`TacticalList`, `SkillConditionList`, `TacticalActionList`, etc.)
- `m_EventParams`: Parameters for the conditions
- `m_EventStatusTypes`: status-type filter lane. The typed C export passes this lane into `UBattleUtility::CheckCondition` alongside `m_EventConditions`, `m_EventParams`, and `m_EventEnemies` (`Octopath_Traveler0-Win64-Shipping.exe.c`:17485857-17485873).
- `m_EventEnemies`: Optional filter to specify which enemy slot/which enemy index/which part the condition applies to (seemingly unused in both OT0 and CotC).

Possibly related: in `Kingship_structs.hpp`:

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
#### Runtime reading semantics

**Primary C evidence:**

##### C-BEVENT-01: BattleEventList row resolver

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

##### C-BEVENT-02: BattleEventList condition evaluation

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

##### C-BEVENT-03: Shared battle-condition tuple evaluator

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

##### C-BCMD-01: BattleEventCommand row resolver

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_Conditions` | `line 16978495` `v8 = this->m_Conditions.AllocatorInstance.Data;` in `void __fastcall FSkillConditionListBase::~FSkillConditionListBase(FSkillConditionListBase *this)`<br>`line 17044430` `this->m_Conditions.AllocatorInstance.Data = nullptr;` in `FSkillConditionListBase *__fastcall FSkillConditionListBase::FSkillConditionListBase(` | In this section, `m_Conditions` denotes the same condition-id namespace used by `m_EventConditions`; the live field name on `BattleEventList` is `m_EventConditions`. | recovered | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventCommand` | `line 17481721` `EventCommandDB = AQPBattleManager::GetEventCommandDB(this, EventListDB->m_EventCommand, m_NowEventCommandIndex);` in `char __fastcall AQPBattleManager::ExecuteBattleEventCommand(AQPBattleManager *this)`<br>`line 17483882` `int m_EventCommand; // eax` in `void __fastcall AQPBattleManager::InitBattleEvent(AQPBattleManager *this, bool forceFlag)` | After an event row is selected, `ExecuteBattleEventCommand` passes `EventListDB->m_EventCommand` and the current command index to `GetEventCommandDB`, which resolves the command-row sequence. | recovered | `C-BEVENT-01`, `C-BCMD-01` |
| `m_EventConditions` | `C-BEVENT-02:17478613` `&EventListDB->m_EventConditions,` | The battle-event evaluator passes `m_EventConditions` as the primary condition-id lane to `UBattleUtility::CheckConditions`. | recovered | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventEnemies` | `C-BEVENT-02:17478619` `&EventListDB->m_EventEnemies,` | The event evaluator passes `m_EventEnemies` as the enemy-id/filter lane to the shared condition evaluator. Missing slots default to zero in that evaluator. | recovered | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventParams` | `C-BEVENT-02:17478614` `&EventListDB->m_EventParams,` | The event evaluator passes `m_EventParams` as the parameter lane aligned by slot with `m_EventConditions`. | recovered | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventStatusTypes` | `C-BEVENT-02:17478615` `&EventListDB->m_EventStatusTypes,` | The event evaluator passes `m_EventStatusTypes` as the status filter lane aligned by slot with `m_EventConditions`. | recovered | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_IsMustPlay` | `line 17485812` `if ( !pEvent->m_IsMustPlay )` in `char __fastcall AQPBattleManager::IsMustPlayEvent(AQPBattleManager *this, const FBattleEventListBase *pEvent)` | `m_IsMustPlay` is read by event-control code as a Boolean gate. If false, the event is not forced through the must-play path. | recovered | `C-BEVENT-02`, `C-BEVENT-03` |

### (***) `BattleEventCommand`
Contains scripted steps in a battle event sequence. Where `BattleEventList` describes what should run under what conditions, `BattleEventCommand` appears to execute the actual script steps (show dialogue, play animation, spawn effects, swap enemy textures, change BGM, etc.)

- `m_NextWait`: delay after executing this command before proceeding in seconds
- `m_isWaitSkillTime`: : if true, wait until a skill animation finishes
- `m_isSwitchActionSkip`: allow/force skipping action sequences
- `m_PlayBgmBlock`: likely blocks BGM changes until something completes
- `m_PerformerID`: likely an index into party/guest/enemy performer lists, or a special ID
- `m_PerformerIsLeader`: use party leader as performer
- `m_PerformerEnemy`: : if non-zero, indicates the performer is an enemy (appears to use `EnemyID.m_id` as input)
- `m_TargetID`: likely who to target for a forced action/skill. Possibly related:

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

- `m_EnableTarget`,`m_EnableAction`: toggles to enable player targeting/action during/after this command. The OT0 decomp has a `BattleEventCommand` consumer where `m_EnableAction` calls `AQPBattleCharacterBase::DisableNotAction(Character)` and then refreshes status UI if state changed, so this flag can clear a no-action state during scripted battle events (`Octopath_Traveler0-Win64-Shipping.exe.c`:17477565-17477624).
- `m_NameID`: localization ID for the speaker name (found under `GameTextCharacter`)
- `m_TextID`: localization ID for the dialogue line (found under `GameTextEvent`)
- `m_BalloonType`: style of balloon/speech bubble (1 = probably normal speech bubble)
- `m_NoTail`: no speech-bubble tail
- `m_BaloonOffsetX/Y`: position offset for the balloon relative to performer
- `m_NoName`: hide speaker name if true
- `m_NarrationID`: takes IDs from `NarrationList.m_id`
- - `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`: narration-style related keys
- `m_AnimID`: which animation to play on the performer while speaking (or immediately before/after)
- `m_DirID`: direction/facing preset for the performer
- `m_isRewriteIdleMotion`: keep an idle override after the command
- `m_SoundID`: play a sound (likely `SoundList` ID)
- `m_SoundHandle`: possibly a runtime handle slot (e.g. start SFX and store handle so later command can stop/replace it) (?)
- `m_EffectID`: spawn an effect (likely `EffectList` ID)
- `m_EffectHandle`: possibly a runtime handle slot for later manipulation/stop (?)
- `m_EffectPosition`: where to spawn the effect (performer, target, screen, etc.)
- `m_BgmID`: change BGM (again likely `SoundList` ID)
- `m_FadeTime`: fade duration for BGM changes
- `m_IsRemoveAllAilments`: clear statuses (likely for phase transitions)
- `m_EnableRetire`, `m_DisableGameOver`: (untested) force-enable/disable UI options and fail states during scripted battles(?)
- `m_LastBattleMapColor`: likely battle background tint control
- `m_TextureChangeEnemyIdx`: which enemy slot to change texture on (`-1` means unused)
- `m_ChangeEnemyTextureIdx`: which texture index/slot to switch to (`-1` unused)
- `m_TextureChangeTime`: timing for texture swap (either delay or crossfade time, untested)
- `m_EnemyPartsIndex`: which enemy part index is being affected
- `m_EnemyPartsDisplay`: show/hide referenced enemy part during the event
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: Also exists in `TacticalList`. The battle engine likely advances scripts based on those flags (i.e. they are an implicit "next" to track what's occurred). Example: used in `TacticalList` by Shadow of Twin Worlds to track whether the tactic unlocking the Shadow's weaknesses upon break of Lucian and Lyblac has occured to re-lock them after Lucian and Lyblac recover from their break as there does not appear to exist a condition specifically for that situation.
- `m_JoinTrigger`: likely tied to "unit joins battle now" events (e.g. guest joins mid-fight)
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`: special command types that do not fit the generic struct. `m_UniqueProcess` likely takes the following values:
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

An enum that may be related to this asset:

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
#### Runtime reading semantics
**Primary C evidence:**

##### C-BCMD-01: BattleEventCommand row resolver

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

##### C-BCMD-02: BattleEventCommand base constructor: recovered named command fields

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

##### C-BCMD-03: BattleEventCommand EnableTarget/EnableAction and battle-map-color consumers

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

##### C-BCMD-04: BattleEventCommand talk/narration consumer

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

##### C-BCMD-05: BattleEventCommand effect and effect-position consumer

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

##### C-BCMD-06: BattleEventCommand performer/leader resolver

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_AnimID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;` (coalesced anchor for `m_AnimID`) | `m_AnimID` is not emitted as a separate `Command->m_AnimID` consumer in this export. It is best treated as the adjacent command-lane paired with `m_DirID` in the constructor; runtime animation consumption is not recovered under the BattleEventCommand symbol. | negative / partial | `C-BCMD-02` |
| `m_BalloonType` | `C-BCMD-04:17477766` `int m_BalloonType; // ecx`<br>`C-BCMD-04:17477784` `m_BalloonType = Command->m_BalloonType;`<br>`C-BCMD-04:17477785` `if ( (unsigned int)(m_BalloonType - 7) > 1 && (unsigned int)(m_BalloonType - 12) > 1 )` | `m_BalloonType` selects the normal balloon path versus the narration path in `BeginBattleEventTalk`. | recovered | `C-BCMD-04` |
| `m_BgmID` | `C-BCMD-02:16987020` `*(_QWORD *)&this->m_BgmID = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_BlackoutNarration` | `C-BCMD-04:17477815` `IsBackoutInvisible = Command->m_BlackoutNarration;` | `m_BlackoutNarration` controls whether the narration path uses the extended narration call with blackout/invisibility behavior. | recovered | `C-BCMD-04` |
| `m_ChangeEnemyTextureIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;` (coalesced anchor for `m_ChangeEnemyTextureIdx`) | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_DirID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_DisableGameOver` | `C-BCMD-02:16987037` `*(_QWORD *)&this->m_DisableGameOver = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_EffectHandle` | `C-BCMD-05:17477471` `__int64 m_EffectHandle; // r13`<br>`C-BCMD-05:17477488` `m_EffectHandle = Command->m_EffectHandle;`<br>`C-BCMD-05:17477490` `v7 = m_EffectHandle - 1;` plus 2 further local occurrences | `m_EffectHandle` is read together with `m_EffectID` in the effect command path. If `m_EffectID` is absent, the handle can still address an existing effect slot. | recovered | `C-BCMD-05` |
| `m_EffectID` | `C-BCMD-05:17477472` `int m_EffectID; // r15d`<br>`C-BCMD-05:17477489` `m_EffectID = Command->m_EffectID;`<br>`C-BCMD-05:17477491` `v2 = m_EffectID > 0;` plus 2 further local occurrences | `m_EffectID` is read together with `m_EffectHandle`; a positive effect id activates effect creation in the event effect path. | recovered | `C-BCMD-05` |
| `m_EffectPosition` | `C-BCMD-05:17477512` `else if ( UEnemyDB::GetBattlePosition(&pos, Command->m_EffectPosition) )`<br>`C-BCMD-05:17477514` `if ( this->m_IsRaidBattle && Command->m_EffectPosition == 1 )` | `m_EffectPosition` is consumed by `BeginBattleEventEffect`: a positive/nonzero position is resolved through `UEnemyDB::GetBattlePosition`, and raid battles special-case position `1`. | recovered | `C-BCMD-05` |
| `m_EnableAction` | `C-BCMD-03:17477619` `if ( Command->m_EnableAction )` | `m_EnableAction` is directly consumed by `BeginBattleEventEnemyStatus`: if true, it clears the target character’s no-action state via `DisableNotAction`. | recovered | `C-BCMD-03` |
| `m_EnableRetire` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_EnableRetire` symbol is recovered in the cited command-row code. Treat the key as unrecovered in this export. | negative / partial | `C-BCMD-02` |
| `m_EnableTarget` | `C-BCMD-03:17477585` `if ( Command->m_EnableTarget )` | `m_EnableTarget` is directly consumed by `BeginBattleEventEnemyStatus`: if true, the command clears the target character’s outside-target state and may reset player targets. | recovered | `C-BCMD-03` |
| `m_EnemyPartsDisplay` | `C-BCMD-02:16987032` `this->m_EnemyPartsDisplay = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_EnemyPartsIndex` | `C-BCMD-02:16987031` `this->m_EnemyPartsIndex = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_FadeTime` | `C-BCMD-02:16987030` `*(_QWORD *)&this->m_Fade = 0;` (coalesced anchor for `m_FadeTime`) | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_IsNextNarrationPage` | `C-BCMD-04:17477822` `Command->m_IsNextNarrationPage,`<br>`C-BCMD-04:17477829` `Command->m_IsNextNarrationPage,` | `m_IsNextNarrationPage` is passed to the narration-opening functions as the page-continuation flag. | recovered | `C-BCMD-04` |
| `m_IsRemoveAllAilments` | `C-BCMD-02:16987022` `*(_WORD *)&this->m_IsRemoveAllAilments = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_JoinTrigger` | `C-BCMD-02:16987010` `*(_WORD *)&this->m_JoinTrigger = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_LastBattleMapColor` | `C-BCMD-03:17477649` `__int64 m_LastBattleMapColor; // rax`<br>`C-BCMD-03:17477655` `if ( !Command \|\| !this->m_pModifyMaterialComponent \|\| Command->m_LastBattleMapColor < 0 )`<br>`C-BCMD-03:17477745` `m_LastBattleMapColor = Command->m_LastBattleMapColor;` plus 2 further local occurrences | `m_LastBattleMapColor` is consumed by `BeginBattleEventLastBattleMapColor`; negative values disable the path, otherwise the value indexes the battle-map color table before applying a material color. | recovered | `C-BCMD-03` |
| `m_NameID` | `C-BCMD-02:16987013` `*(_QWORD *)&this->m_NameID = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_NarrationID` | `C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )`<br>`C-BCMD-04:17477821` `Command->m_NarrationID,`<br>`C-BCMD-04:17477828` `Command->m_NarrationID,` | `m_NarrationID` is consumed by `OpenNarrationC`/`OpenNarrationEXC` when the command uses a narration balloon type. | recovered | `C-BCMD-04` |
| `m_NextWait` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_NextWait` symbol is recovered. The constructor exposes a coalesced `m_Fade`/timing region, but this export does not prove a distinct field named `m_NextWait`. | negative / partial | `C-BCMD-02` |
| `m_NoName` | `C-BCMD-02:16987012` `this->m_NoName = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_NoTail` | `C-BCMD-02:16987014` `this->m_NoTail = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_OffEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;` (coalesced anchor for `m_OffEventFlgIndex`) | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_OnEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_OptCharacterID` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_OptCharacterID` symbol is recovered; only the adjacent unique-process option region and `m_OptSkillID` are named. | negative / partial | `C-BCMD-02` |
| `m_OptSkillID` | `C-BCMD-02:16987039` `this->m_OptSkillID = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerEnemy` | `C-BCMD-02:16987011` `this->m_PerformerEnemy = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerID` | `C-BCMD-02:16987009` `this->m_PerformerID = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerIsLeader` | `C-BCMD-06:17482218` `if ( Command->m_PerformerIsLeader )` | `m_PerformerIsLeader` is consumed by `FindBattleEventCharacter`; when true, performer resolution returns the party leader path rather than the ordinary performer-id path. | recovered | `C-BCMD-06` |
| `m_PlayBgmBlock` | `C-BCMD-02:16987024` `this->m_PlayBgmBlock = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundHandle` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundID` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;` (coalesced anchor for `m_SoundID`) | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_TargetID` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_TargetID` symbol or unambiguous command-row consumer was recovered. Targeting is handled by the event executor, but this export does not preserve a field under that name. | negative / partial | `C-BCMD-01` |
| `m_TextID` | `C-BCMD-04:17477765` `signed int m_TextID; // edi`<br>`C-BCMD-04:17477779` `m_TextID = Command->m_TextID;`<br>`C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )` plus 1 further local occurrences | `m_TextID` is read by the talk/narration path; if both `m_TextID` and `m_NarrationID` are non-positive the command does not open text. | recovered | `C-BCMD-04` |
| `m_TextureChangeEnemyIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_TextureChangeTime` | `C-BCMD-02:16987028` `this->m_TextureChangeTime = 0.0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_UniqueProcess` | `C-BCMD-02:16987038` `*(_QWORD *)&this->m_UniqueProcess = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_isRewriteIdleMotion` | `C-BCMD-02:16987025` `this->m_isRewriteIdleMotion = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |
| `m_isSwitchActionSkip` | No exact named field or unambiguous offset lane recovered in the supplied C export. | No exact `m_isSwitchActionSkip` symbol is recovered in the C export. Do not describe it as a proven live field without a separate consumer. | negative / partial | `C-BCMD-02` |
| `m_isWaitSkillTime` | `C-BCMD-02:16987023` `this->m_isWaitSkillTime = 0;` | The command row is resolved as `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`. This field is either named in `FBattleEventCommandBase` initialization or consumed by the event executor; adjacent fields coalesced by Hex-Rays are interpreted as paired lanes only when the consumer confirms them. | recovered | `C-BCMD-01`, `C-BCMD-02` |

### (*) `BattlePlaybackData` and `BattlePlayback`
This section needs a stricter separation of three similarly named things:

1. **`BattlePlaybacks`** is the recovered runtime database table. Its row type is `FBattlePlaybacksBase`, and the runtime reads it through `ADatabaseDefineStatic::m_BattlePlaybacks.m_DataList`.
2. **`FPlaybackData`** is not a DataBase row. It is a transient runtime object used by `ABattlePlayback` after the save-data layer and the `BattlePlaybacks` table have been joined.
3. **`BattlePlaybackData`**, as dumped in some asset exports with fields such as `m_EnemySkillID_01`, is not recovered as a live `ADatabaseDefineStatic` DB surface in the C export. Do not treat those odd fields as equivalent to the live `BattlePlaybacks` table unless a separate consumer is found.

The practical consequence is that, for battle playback, the meaningful modding table is `BattlePlaybacks`: it maps a playback id to one enemy skill, an optional enemy performer slot, and an effect-visibility flag. The party composition, enemy group, raid flag, guest id, and main-character appearance are supplied by save/debug playback data; the skill/action payload is then obtained from `BattlePlaybacks`.

#### `BattlePlaybacks`
The live row has the following recovered structure:

| Field | C row offset | Runtime role | Evidence grade |
|---|---:|---|---|
| `m_id` | `+0x08` | The key compared against `FSaveDataBattlePlayback::Id`. | direct row scan |
| `m_VisibleAttackEffect` | `+0x0C` | Byte flag copied to `FPlaybackData::IsVisibleSkillEffect`; later passed to `AQPActionCommand::ProceedEffectiveOnlyLooks`. | direct gameplay consumer |
| `m_EnemySkillID` | `+0x10` | Skill id copied to `FPlaybackData::EnemyUseSkillID`; later passed to `AQPActionCommand::SetupActionCommand`. | direct gameplay consumer |
| `m_SkillPlayEnemyIndex` | `+0x14` | Optional enemy slot override copied to `FPlaybackData::SkillUseEnemyIndex`; `-1` means no fixed row-specified performer. | direct gameplay consumer |

The struct size is 24 bytes. The SDK enum `EBATTLE_PLAYBACKS` names stock playback ids, but the principal runtime path shown below performs a linear scan over the DB array and compares against the row id. That path is not an enum-domain switch. The exception is the debug fallback path, which contains hard-coded cases for a few ids when no created save playback exists.

#### `BattlePlaybackData`
The dumped `BattlePlaybackData` records with values such as:

```json
"m_EnemySkillID_01": "m_EnemySkillID_01"
```

should remain classified as unresolved template/schema data. The C export registers `BattlePlaybacksBase`, `BattlePlaybacks`, and the transient `PlaybackData`; it does not expose a live `ADatabaseDefineStatic::GetBattlePlaybackData` table corresponding to `m_EnemySkillID_01` / `m_EnemySkillID_02`. There is also a separate end-credit struct, misspelled in the symbols as `FEndCreditBattlePlayackData`, but that is an end-credit manager data structure rather than the battle playback DB described here.

#### Runtime reading semantics:: compiled structs and table surfaces

The compiled registration data identifies `BattlePlaybacksBase` and `BattlePlaybacks` as the DB structs for `BattlePlaybacks`. Separately, `ABattlePlayback` owns a transient `FPlaybackData` struct. This distinction matters because the table row and the runtime action object are not the same object.

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

Interpretation: the live battle playback DB surface is `BattlePlaybacks`, not a multi-column `BattlePlaybackData` table. The end-credit struct is a separate UI/ending subsystem artifact; its presence does not validate the odd dumped `m_EnemySkillID_01` table as battle-runtime input.

#### Runtime reading semantics: `FBattlePlaybacksBase` row layout

`FBattlePlaybacksBase` is constructed and copied as a compact 24-byte row. The copy routine proves the live payload lanes: row id at `+0x08`, visibility byte at `+0x0C`, enemy skill id at `+0x10`, and performer index at `+0x14`.

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

Interpretation: `m_VisibleAttackEffect` is a byte lane rather than an integer id. `m_EnemySkillID` and `m_SkillPlayEnemyIndex` are 32-bit lanes. Nothing in this row layout corresponds to `m_EnemySkillID_01` or `m_EnemySkillID_02`.

#### Runtime reading semantics: static DB access and loaded-state check

The static DB class exposes both the full `BattlePlaybacks` wrapper and the data-list array. The battle playback actor itself uses `GetBattlePlaybacksDB`, which refuses to return a data pointer until `m_BattlePlaybacks.m_Loaded` is true.

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

Interpretation: the table is a normal loaded DB asset with a data-list array of `FBattlePlaybacksBase`. Expansion is constrained by the DB/index machinery and by callers that use playback ids, not by the `EBATTLE_PLAYBACKS` enum definition itself. A new row id can be scanned by this path if a playback save/debug caller supplies that id and the table is loaded.

#### Runtime reading semantics: saved playback plus table row join

`ABattlePlayback::CreatePlaybackData` constructs an `FPlaybackData` object by joining two sources. It copies party/group/appearance values from `FSaveDataBattlePlayback`, but it obtains the action payload from the matching `BattlePlaybacks` row.

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

Interpretation: the dataflow is `FSaveDataBattlePlayback::Id -> BattlePlaybacks.m_id -> FPlaybackData.{EnemyUseSkillID, SkillUseEnemyIndex, IsVisibleSkillEffect}`. Thus `m_EnemySkillID` is not stored in the save playback record; it is recovered by using the save playback id as a DB key into `BattlePlaybacks`.

#### Runtime reading semantics: request/preload stage

After a playback datum is constructed, `RequestBattlePlayback` copies it into the actor, configures raid-vs-normal environment visibility, writes the enemy group to `AQPBattleManager`, preloads effects for `(EnemyGroupID, EnemyUseSkillID)`, requests enemy flipbooks for the group, and then builds the main-character texture.

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

Interpretation: `m_EnemySkillID` participates in resource preparation before action execution. A playback row can therefore fail semantically even before the command is run if the skill id cannot support required effect/animation preloading in the selected enemy group context.

#### Runtime reading semantics: performer selection and skill validation

`PlayAction` first honors `m_SkillPlayEnemyIndex` when it is a valid enemy-array index. If the row does not specify a valid slot, the function searches the selected enemy group for enemies whose tactical skill list contains `m_EnemySkillID`. The current boss, if it can use the skill, is preferred; otherwise the first matching enemy id is converted back into a group slot and used as the performer. Only then does the code issue `AQPActionCommand::SetupActionCommand`.

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

Interpretation: `m_SkillPlayEnemyIndex` is stronger than the tactical-skill membership search because it can select a concrete enemy object directly. Without a valid explicit index, `m_EnemySkillID` is treated not only as the action to preview but also as a constraint for choosing a plausible enemy performer. This means that playback rows should normally use a skill that at least one enemy in `m_EnemyGroupID` can use, unless the slot override is known to be valid and the visual command tolerates the mismatch.

#### Runtime reading semantics: effect-visibility flag

The visibility flag is not merely copied. During the playback tick, it is passed into the action-command visual-only progression function.

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

Interpretation: the flag controls the visual/effective presentation while the action command is being advanced. The source name `m_VisibleAttackEffect` is therefore directionally correct, but the C consumer names the runtime field `IsVisibleSkillEffect`; it should be described as an action-effect visibility switch rather than as a timing or skill-selection parameter.

#### Runtime reading semantics: debug UI and transient object layout

The debug widget uses a map of `FPlaybackData` records. Its extraction path confirms the transient object offsets: `EnemyUseSkillID` at `+0x58`, `SkillUseEnemyIndex` at `+0x5C`, `IsVisibleSkillEffect` at `+0x60`, and `BossIndex` at `+0x64`.

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

Interpretation: `FPlaybackData` is 104 bytes and contains the save playback state followed by the action preview fields. This transient structure is useful for understanding execution, but it should not be mistaken for a database table row.

#### Field-level interpretation

| Key | Runtime C locus | Accurate interpretation | Status |
|---|---|---|---|
| `m_EnemySkillID` | `FBattlePlaybacksBase +0x10`; copied by `CreatePlaybackData`; consumed by `BattlePlaybackPreloadEffectAsync`, `UEnemyDB::HasTacticalSkill`, and `AQPActionCommand::SetupActionCommand`. | The skill id to visually execute in the playback. It is a `SkillID`-namespace id, but action performer resolution still depends on the selected enemy group and optional slot override. | recovered, direct consumer |
| `m_SkillPlayEnemyIndex` | `FBattlePlaybacksBase +0x14`; copied to `FPlaybackData::SkillUseEnemyIndex`; checked against `m_Enemies.ArrayNum`. | Optional enemy slot override. Values below zero or outside the live enemy array fall back to performer discovery by tactical-skill membership. | recovered, direct consumer |
| `m_VisibleAttackEffect` | `FBattlePlaybacksBase +0x0C`; copied to `FPlaybackData::IsVisibleSkillEffect`; passed to `ProceedEffectiveOnlyLooks`. | Boolean-style action-effect visibility switch. The C name at runtime is `IsVisibleSkillEffect`, so describe it as controlling skill/effect presentation during visual-only action playback. | recovered, direct consumer |
| `m_EnemySkillID_01` | No recovered `BattlePlaybacksBase` lane; no recovered `ADatabaseDefineStatic::GetBattlePlaybackData` surface. | Not proven as live battle playback runtime data. Treat as unresolved dumped schema/template material. | negative / unresolved |
| `m_EnemySkillID_02` | Same as `m_EnemySkillID_01`. | Not proven as live battle playback runtime data. | negative / unresolved |

#### Authoring implications

- A valid `BattlePlaybacks` row should pair `m_EnemySkillID` with the row's intended `m_EnemyGroupID` context, because the runtime searches that group for an enemy whose tactical skill set contains the requested skill.
- Use `m_SkillPlayEnemyIndex` when the visual performer must be deterministic. Otherwise, the runtime may choose the current boss if it matches, or the first group enemy whose `EnemyID` has the skill.
- `m_VisibleAttackEffect = false` does not suppress skill selection. It affects the visual progression path after the action command has already been created.
- The `EBATTLE_PLAYBACKS` enum is best treated as stock-name documentation for vanilla rows. The recoverable DB path scans the loaded array by row id and is therefore not intrinsically limited to enum values, although debug fallback cases are hard-coded for a few stock ids.



### (*) BattleResultBonus
Tracks ratios and localization IDs of different battle result bonuses (the "No Damage", "Overkill", "Break", "1-Turn Kill", etc. bonuses). `m_Param` appears to track which stats (XP, JP, or money) gets the bonus:
- 0: Money 
- 1: JP
- 2: XP
Untested whether unused params beyond 2 exist.

#### Runtime reading semantics
`BattleResultBonus` rows are selected by bonus type, not by `m_Param`. The recovered gameplay predicate reads only the row's threshold lane at offset `+0x10` after `GetBattleResultBonusParam` locates the row.


**Primary C evidence:**

##### C-BRESULT-01: BattleResultBonus row lookup by bonus type

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

##### C-BRESULT-02: bonus predicates read row offset +0x10

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_Param` | `C-BRESULT-01:17542787-17542801` scans `m_BattleResultBonus.m_DataList` by row id at offset `+0x08`.<br>`C-BRESULT-02:17549619-17549627` reads only `BattleResultBonusParam[16]` for the recovered predicate thresholds. | The exact `m_Param` field is not the lookup key in the recovered runtime path; rows are selected by `bonusType` compared against row offset `+0x08`. `IsBattleResultBonus` then compares the battle counter against row offset `+0x10`. The C export does not identify an asset-specific consumer for a distinct `m_Param` lane, so the Money/JP/XP interpretation remains an authoring/schema hypothesis rather than a proven predicate behavior. | negative / partial, with live-table evidence | `C-BRESULT-01`, `C-BRESULT-02` |



#### Deep runtime analysis: `BattleResultBonus` condition thresholds

The existing schema-level description is incomplete if it treats `m_Param` only as a reward-channel selector. At least one direct runtime consumer reads the row value at `+0x10` as the threshold used to decide whether a result bonus condition is satisfied.

##### C-BATTLERESULTBONUS-LOOKUP-01: result-bonus row resolver and 24-byte stride

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

##### C-BATTLERESULTBONUS-THRESHOLD-02: the row threshold is used by `IsBattleResultBonus`

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

Interpretation: `bonusType` selects the row by `m_id` at `+0x08`. The integer at `+0x10` is a type-specific threshold: damage taken for "No Damage," overdamage ratio, break count, or turn count depending on the bonus type. If another exported field also controls reward channels or UI text, that is a separate lane; it should not be conflated with the threshold consumer shown here.

### (**) `BattleVoiceList` and `BattleVoiceArenaList`
Define a variety of voice lines to be played by party members or a narrator during battle (e.g. special voice lines during the Or'Galdera fight).

Likely related:
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
#### runtime analysis: `BattleVoiceList` and `BattleVoiceArenaList`
The voice tables are runtime-active, but the strongest recovered direct consumer in this pass is the arena override path. The normal battle voice path accepts an already materialized voice-id list, applies arena replacement when appropriate, removes repeat-limited candidates, then selects a concrete voice id.

##### C-BATTLEVOICEARENA-LOOKUP-01: arena voice rows are key-indexed with a 208-byte stride

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

##### C-BATTLEVOICEARENA-CANDIDATES-02: arena rows can override the candidate voice-id list

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

Interpretation: `BattleVoiceArenaList` is an overlay on the ordinary battle-voice candidate list. It does not replace the entire voice-selection subsystem; it conditionally substitutes or filters candidates when the battle is recognized as an arena battle.

### (***) BattleAbortConditions
OT0's battle termination/forced-outcome condition table: each entry defines a small bundle of predicates that, when satisfied during a fight, cause the battle to abort (end early) or be treated as a forced win/forced lose depending on which field references it in `EnemyGroups`.
- `m_Conditions`: condition type IDs (shares the same numeric condition namespace as in `TacticalList`, `TacticalActionList`, `SkillConditionList`, and `BattleEventList`)
- `m_Params`: parameter for each condition (meaning depends on the condition type)
- and filters that only matter for certain condition types:
   - `m_AilmentTypes` (takes `SkillAilmentType.m_id` as input)
   - `m_StatusTypes` (possibly takes `CharaStatusID.m_id` as input)
   - `m_WeaponTypes` (takes `WeaponType.m_id` as input)
   - `m_MagicTypes` (takes `Magictype.m_id` as input)
   - `m_EnemyID` (takes `EnemyID.m_id` as input)

#### Runtime-evidence integration note
- decomp-only anchor from `Octopath_Traveler0-Win64-Shipping.exe.c`:
    - `AQPBattleManager::CheckBattleAbort` resolves the relevant `BattleAbortConditions` row, passes the row's seventh filter array (`v17 + 7`, the `m_EnemyID` lane) into `UBattleUtility::CheckConditions`, and `CheckConditions` forwards that lane as the `EnemyID` argument to `UBattleUtility::CheckCondition`; this proves the field is an active condition filter, not just a copied schema slot (`Octopath_Traveler0-Win64-Shipping.exe.c`:17493321-17493429, 17493527-17493542, and 17389535-17389666)
- exact IDA anchors:
    - `0x144373530` `ADatabaseDefineStatic::execGetBattleAbortConditions`: the named runtime accessor for the table
#### Runtime reading semantics
**Primary C evidence:**

##### C-GROUP-02: EnemyGroups materializer with concrete row offsets

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

##### C-BEVENT-03: Shared battle-condition tuple evaluator

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

##### C-BABORT-01: BattleAbortConditions struct copy of seven condition/filter lanes

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_AilmentTypes` | `ailment lane` (array); no shorter named assignment survived in the cited block | `BattleAbortConditions` rows are copied as seven parallel `TArray<int>` lanes. The shared `UBattleUtility::CheckConditions` evaluator consumes condition, parameter, status, ailment, weapon, magic, and enemy-id lanes slot-wise; missing slots default to zero. | recovered | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Conditions` | `condition lane` (array); no shorter named assignment survived in the cited block | `BattleAbortConditions` rows are copied as seven parallel `TArray<int>` lanes. The shared `UBattleUtility::CheckConditions` evaluator consumes condition, parameter, status, ailment, weapon, magic, and enemy-id lanes slot-wise; missing slots default to zero. | recovered | `C-BABORT-01`, `C-BEVENT-03` |
| `m_EnemyID` | `enemy-id lane` (array); no shorter named assignment survived in the cited block | `BattleAbortConditions` rows are copied as seven parallel `TArray<int>` lanes. The shared `UBattleUtility::CheckConditions` evaluator consumes condition, parameter, status, ailment, weapon, magic, and enemy-id lanes slot-wise; missing slots default to zero. | recovered | `C-BABORT-01`, `C-BEVENT-03` |
| `m_MagicTypes` | `magic lane` (array); no shorter named assignment survived in the cited block | `BattleAbortConditions` rows are copied as seven parallel `TArray<int>` lanes. The shared `UBattleUtility::CheckConditions` evaluator consumes condition, parameter, status, ailment, weapon, magic, and enemy-id lanes slot-wise; missing slots default to zero. | recovered | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Params` | `parameter lane` (array); no shorter named assignment survived in the cited block | `BattleAbortConditions` rows are copied as seven parallel `TArray<int>` lanes. The shared `UBattleUtility::CheckConditions` evaluator consumes condition, parameter, status, ailment, weapon, magic, and enemy-id lanes slot-wise; missing slots default to zero. | recovered | `C-BABORT-01`, `C-BEVENT-03` |
| `m_StatusTypes` | `status lane` (array); no shorter named assignment survived in the cited block | `BattleAbortConditions` rows are copied as seven parallel `TArray<int>` lanes. The shared `UBattleUtility::CheckConditions` evaluator consumes condition, parameter, status, ailment, weapon, magic, and enemy-id lanes slot-wise; missing slots default to zero. | recovered | `C-BABORT-01`, `C-BEVENT-03` |
| `m_WeaponTypes` | `weapon lane` (array); no shorter named assignment survived in the cited block | `BattleAbortConditions` rows are copied as seven parallel `TArray<int>` lanes. The shared `UBattleUtility::CheckConditions` evaluator consumes condition, parameter, status, ailment, weapon, magic, and enemy-id lanes slot-wise; missing slots default to zero. | recovered | `C-BABORT-01`, `C-BEVENT-03` |

### (*) `BattleActionID`
A small lookup table that maps an "action ID" (likely `SkillAilmentType.m_ActionID`) to a concrete battle animation/motion, with a flag that says whether that animation should be played using the NPC/field character rig instead of the standard battle rig. `m_UseNPC` may determine whether this action should be played using an NPC character animation set rather than the usual battle-sprite.
Likely related: `Kingship_structs.hpp` assigns the following labels to some action IDs:
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
#### Runtime reading semantics
The `BattleActionID` row is not merely a label table. The player-character battle-action path resolves a row by `ActionID`, then reads byte offset `+0x10` as the `m_UseNPC` gate. The gate is only evaluated for character objects whose `m_bNpcFlag` is set.

**Primary C evidence:**

##### C-BACTION-01: BattleActionID row resolution by ActionID

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

##### C-BACTION-02: m_UseNPC as the NPC-only action gate

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_UseNPC` | `C-BACTION-02:17577515` `ActionDB = AQPCharacterBattlePlayer::GetActionDB(this, ActionID);`<br>`C-BACTION-02:17577516` `if ( !ActionDB || !*(_BYTE *)&ActionDB[16] )`<br>`C-BACTION-01:17579948` `m_BattleActionID.m_DBAccess`<br>`C-BACTION-01:17579958` `Data[24 * ArrayIndex]` | `m_UseNPC` is the byte at row offset `+0x10` in a 24-byte `BattleActionID` row. In `AQPCharacterBattlePlayer::ExecPlayBattleAction`, the field is tested only when `this->m_bNpcFlag` is true. If the row is missing or this byte is zero, the requested action ID is coerced to `1` before animation resolution, i.e. the NPC/field-rig character falls back to the idle action. Non-NPC player characters do not take this guard path in the cited function. | recovered | `C-BACTION-01`, `C-BACTION-02` |

### (*) BattleParams
A variety of battle-related parameters whose meaning is as follows according to `Kingship_structs.hpp` from the Dumper-7 SDK (see #file-dump in the BravelyPath Modular Discord):
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
#### Runtime reading semantics
`BattleParams` is read through a two-stage mechanism. At battle-manager initialization/use, the database rows are normalized into `m_AllBattleParamList`; runtime call sites subsequently use the `EBATTLE_PARAMS` numeric value as an unsigned-byte index into that cached vector. The cache stores four type views of the same authoring value: string, integer, float, and boolean.

**Primary C evidence:**

##### C-BPARAM-01: BattleParams rows are copied from the database table

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

##### C-BPARAM-02: numeric string conversion into cached int/float/bool slots

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

##### C-BPARAM-03: EBATTLE_PARAMS id indexes m_AllBattleParamList

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

##### C-BPARAM-04: float accessor reads the cached float slot

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

##### C-BPARAM-05: integer accessor reads the cached integer slot

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

**Runtime interpretation:**

| Runtime element | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `EBATTLE_PARAMS` id / row id | `C-BPARAM-01:17537220` `v13 = *(_DWORD *)&v12[-8];`<br>`C-BPARAM-03:17542742` `if ( battleParam < this->m_AllBattleParamList.ArrayNum )` | The database row id is used as the dense index into `m_AllBattleParamList`. The enum constants in the guide are therefore runtime keys, not descriptive-only labels. | recovered | `C-BPARAM-01`, `C-BPARAM-03` |
| string value | `C-BPARAM-01:17537225` `v16 = *(int *)&v12[8];`<br>`C-BPARAM-01:17537226` `v17 = *(const wchar_t **)v12;` | The source table value is stored as a string-like `FString` payload at the database-row string lane. It is copied into the cached `BattleParam.StringValue`. | recovered | `C-BPARAM-01` |
| integer value | `C-BPARAM-02:17537302` `*(_DWORD *)&v21[8 * v22 + 24] = *(_DWORD *)&battleParam.BoolValue;`<br>`C-BPARAM-05:17395117` `v1 = *(_DWORD *)&v5[24];` | During caching, numeric strings are parsed with `_wtoi`; the cached integer accessor later reads offset `+0x18` of the cached `BattleParam` record. | recovered | `C-BPARAM-02`, `C-BPARAM-05` |
| float value | `C-BPARAM-02:17537274` `v19 = _wtof(v18);`<br>`C-BPARAM-02:17537303` `*(_DWORD *)&v21[8 * v22 + 28] = *((_DWORD *)&battleParam.BoolValue + 1);`<br>`C-BPARAM-04:17395003` `v3 = *(float *)&v5[28];` | During caching, numeric strings are parsed with `_wtof`; the cached float accessor later reads offset `+0x1C` of the cached `BattleParam` record. | recovered | `C-BPARAM-02`, `C-BPARAM-04` |
| boolean value | `C-BPARAM-02:17537281` `v20 = FString::ToBool((FString *)v12);`<br>`C-BPARAM-02:17537304` `v21[8 * v22 + 32] = (FScriptContainerElement)v36;` | The boolean view is obtained by `FString::ToBool` during cache construction and stored at cached offset `+0x20`. | recovered | `C-BPARAM-02` |
#### `BattleParams` caching and typed accessors
`BattleParams` are not read ad hoc from the database every time. The battle manager first caches every row into a typed runtime array indexed by `EBATTLE_PARAMS`, and later helpers read cached string/int/float/bool views from fixed offsets. This is why the enum-like id list is operationally important even though the source data is a DB table.

##### C-BATTLEPARAMS-CACHE-BUILD-01: row values are cached by parameter id

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

##### C-BATTLEPARAMS-CACHE-INDEX-02: battle-param ids index the runtime cache directly

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

##### C-BATTLEPARAMS-TYPED-WRAPPERS-03: typed wrappers expose cached fields

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

Interpretation: a `BattleParams` row is parsed into multiple typed representations. The raw database value is string-like, but the runtime cache exposes integer, float, and boolean views at `+0x18`, `+0x1C`, and `+0x20` of the cached `BattleParam` entry. Adding new parameter ids is therefore not just a matter of adding rows; caller code must request those ids and the cache must be large enough for the numeric id.


### (*) `BattleDeathBlowPoint`
A lookup table for "Death Blow" (special attack) gauge gain. Each row essentially defines if a certain check condition is met (type + value), by how much the Death Blow gauge is to be increased.
- `m_CheckType`: what kind of thing is being checked.
- `m_CheckValue`: the threshold/amount for that check type. Appears to be a count
- `m_IncreasedPoint`: how many "Death Blow points" to add when that check matches.

#### Runtime reading semantics
`BattleDeathBlowPoint` is consumed by `AQPBattleManager::CheckSpecialSkillPoint`. The decompilation does not preserve the field names in the hot loop, but the copied row stride and offset usage make the three payload fields unambiguous: offset `+0x0C` is the check-type selector, offset `+0x10` is the check value, and offset `+0x14` is the point increment.

**Primary C evidence:**

##### C-BDEATH-01: BattleDeathBlowPoint table copy and 24-byte row stride

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

##### C-BDEATH-02: check-type dispatch and use of +0x10/+0x14

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

##### C-BDEATH-03: enemy-side threshold branch and final rise-point storage

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_CheckType` | `C-BDEATH-01:17515519-17515523` copies source offsets `+0x0C/+0x10/+0x14` into a local 24-byte row copy.<br>`C-BDEATH-02:17515786` `v56 = *(_DWORD *)&v52[12];`<br>`C-BDEATH-02:17515793` `if ( (unsigned __int8)v56 == 1 )`<br>`C-BDEATH-02:17515795` `if ( (unsigned __int8)v56 == 2 )` plus type `3`, `4`, and `5` branches later in the same block. | `m_CheckType` is stored in the 32-bit lane at row offset `+0x0C`, but the evaluator semantically dispatches on its low byte. The recovered cases are branch-coded, not enum-named in the C export: type `1` enters an enemy-side projected-damage threshold path; type `2` checks guarded targets with reserved damage; type `3` is a non-enemy hit/boost/weapon path; type `4` is a non-enemy hit/boost/non-weapon skill path with additional guard/support/item exclusions; type `5` counts newly broken targets. | recovered | `C-BDEATH-01`, `C-BDEATH-02`, `C-BDEATH-03` |
| `m_CheckValue` | `C-BDEATH-02:17515784` `v54 = *(_DWORD *)&v52[16];`<br>`C-BDEATH-02:17515991` `if ( v50->m_UseBoostLevel == v54 && v53 )`<br>`C-BDEATH-03:17516102` `v91 = (int)(float)((float)((float)v54 * 0.0099999998) * (float)v90);` | `m_CheckValue` is row offset `+0x10`. Its meaning is type-dependent. In the type-3/type-4 player-side branches it is compared against `m_UseBoostLevel`; in the type-1 enemy-side branch it is interpreted as a percentage threshold of the target's cached maximum HP. | recovered | `C-BDEATH-02`, `C-BDEATH-03` |
| `m_IncreasedPoint` | `C-BDEATH-02:17515785` `v55 = *(_DWORD *)&v52[20];`<br>`C-BDEATH-02:17515822` `risePoint[...] = v55;`<br>`C-BDEATH-02:17515955` `v64 = v58 * v55;`<br>`C-BDEATH-03:17516143` `risePoint[...] = InOutElementIndex;` | `m_IncreasedPoint` is row offset `+0x14`. It is the base gauge increment written into the temporary `risePoint` array. For type `5`, the code multiplies this value by the number of newly broken targets. The final increment is then scaled by the current special-skill-gauge level bonus and by the `DEATHBLOWPOINT_UP` support ability before `AddSpecialSkillGaugeValue` is called. | recovered | `C-BDEATH-02`, `C-BDEATH-03` |



#### Deep runtime analysis: `BattleDeathBlowPoint` dispatch and gauge mutation

`BattleDeathBlowPoint` is a live combat table. The runtime copies the table, iterates each 24-byte row, interprets the low byte of the check type as a dispatch selector, then appends candidate gauge increases to `risePoint`. Later in the same function, each nonzero rise point is scaled by battle parameters and support abilities before `AddSpecialSkillGaugeValue` mutates the player gauge.

##### C-BATTLEDEATHBLOW-WORKLIST-01: the table is copied into an invoke-time work list

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

##### C-BATTLEDEATHBLOW-ROW-FIELDS-02: each row supplies check type, check value, and increased point

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

##### C-BATTLEDEATHBLOW-CHECK-BRANCHES-03: break, boosted action, and damage-threshold branches feed gauge gain

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

##### C-BATTLEDEATHBLOW-GAUGE-APPLY-04: final point application scales and mutates the special-skill gauge

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

Interpretation: the recovered field roles are strong even where the check-type names remain anonymous in the C export: `m_CheckType` is the dispatch selector at row `+0x0C`, `m_CheckValue` is the comparison/percentage/boost-level value at `+0x10`, and `m_IncreasedPoint` is the base point payload at `+0x14`. The dispatch branches are not a generic threshold table; they bind to concrete combat states such as guard damage, boost level, just-breaking targets, and reserved-damage thresholds.

### (*) `BattleLayoutSet`
Likely a small battle staging/layout preset table that ties together a camera preset and enemy-side positioning. It is unclear where in the game this is used (no formation or reinforcements appear to make use of `PositionID` 270, for instance).

`Kingship_structs.hpp` assigns the following labels to some layout sets:

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
#### Runtime reading semantics
The C export proves that `BattleLayoutSet` is live runtime data in the raid-view camera path. It is resolved by layout id through the database access helper, and the row is then consumed as an `FBattleLayoutSetBase` containing at least `m_CameraParamID` and `m_EnemyPosition`.

**Primary C evidence:**

##### C-BLAYOUT-01: BattleLayoutSet row lookup

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

##### C-BLAYOUT-02: m_CameraParamID and m_EnemyPosition consumed by raid view

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

##### C-BLAYOUT-03: layout ids 1, 0, and caller-supplied offsets in UpdateRaidView

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

**Field-level interpretation:**

| Key / row field | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_id` / layout id | `C-BLAYOUT-01:17542710` `if ( layoutID <= 0 )`<br>`C-BLAYOUT-01:17542720` `m_BattleLayoutSet.m_DBAccess`<br>`C-BLAYOUT-01:17542729` `Data[24 * ArrayIndex]` | The layout id is resolved through `m_BattleLayoutSet.m_DBAccess`; id `0` is rejected by the getter. The returned row stride is 24 bytes. | recovered | `C-BLAYOUT-01` |
| `m_CameraParamID` | `C-BLAYOUT-02:17510719` `m_CameraParamID = battleLayout->m_CameraParamID;`<br>`C-BLAYOUT-02:17510721` `if ( m_CameraParamID <= 0 ) return 0;`<br>`C-BLAYOUT-02:17510778` `StartFreeMove(..., m_CameraParamID, ...)` | `m_CameraParamID` is the primary camera preset selected by the layout. If it is non-positive, the update fails. In non-raid-offset mode it is passed directly to `ABattleCameraBase::StartFreeMove`; in raid-offset mode a second layout's camera id is used as the arc offset id. | recovered | `C-BLAYOUT-02` |
| `m_EnemyPosition` | `C-BLAYOUT-02:17510747` `m_EnemyPosition = offset->m_EnemyPosition;`<br>`C-BLAYOUT-02:17510759` `if ( m_EnemyPosition > 0 )`<br>`C-BLAYOUT-02:17510762` `UEnemyDB::GetBattlePosition(&Position, m_EnemyPosition)` | `m_EnemyPosition` is an optional battle-position id read from the offset layout. When positive, it is resolved through `UEnemyDB::GetBattlePosition` and stored into the raid enemy offset transform. This is a direct runtime use, not an inferred formation reference. | recovered | `C-BLAYOUT-02` |

## Under `AIBattle`
These files are responsible for enemy AI behavior and the skills used by them (and how).

For enemy AI, the engine reads:

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

### (****) `TacticalList`, `TacticalAssignList`, and `TacticalSkillList`
OT0's enemy AI "rule clause" table: each entry defines a set of conditions that, when satisfied, causes the AI to select or enable some behavior. Enemies are assigned these "rules" in the file `TacticalAssignList` whose `m_id`s correspond to `EnemyID.m_TacticalAssignID`; battle skills are assigned to them through `TacticalSkillList`, whose `m_id`s correspond to `EnemyID.m_SkillsID`.
- `m_Presage`: likely a enum indicating whether this tactic causes a presage (telegraphed action) to show
- `m_PresageSkillID`: skill ID associated with the presage  (what's being telegraphed). Used, for instance, when a boss is announcing boost mode (these skill IDs then notably **not** present in the enemy's `TacticalSkillList.m_UseSkills` array)
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: Event flag gating and side effects, see `BattleEventCommand`. Likely used for branching/sequencing AI behavior.
- `m_Conditions`: same conditions as `TacticalActionList`, `BattleEventList`, `SkillConditionList` most likely with same `m_Params` and filter arrays
   - `m_AilmentTypes` (takes `SkillAilmentType.m_id` as input)
   - `m_StatusTypes` (possibly takes `CharaStatusID.m_id` as input)
   - `m_WeaponTypes` (takes `WeaponType.m_id` as input)
   - `m_MagicTypes` (takes `Magictype.m_id` as input)

The meaning of each condition has not been fully named yet. Use the local [Condition Crossref Atlas](Octopath_Condition_Crossref_Atlas.md) as the current guide-bundle inventory, and leave condition IDs unresolved where that atlas only proves shape, source table, or parameter lanes. The imported CotCPort snapshot under `../raw_evidence/` is historical source evidence, not the authority for current OT0 v1.0.8 names.

#### Runtime-evidence integration note
Earlier address-level tactical notes have been folded into the named `UTacticalDB`, `UBattleTacticalComponent`, and `UBattleUtility` evidence below. The C export now gives typed row accessors and named condition-evaluation calls, so this section avoids the older `sub_...` naming except where an address is inherent to a quoted C excerpt. Condition ids should still be interpreted by the cited evaluator paths, not by schema names alone.

### (****) `TacticalActionList`
Appears to tell the game what to actually do when a tactic applies: which skill to use (by index), who to target, how to select among possible targets, and additional conditions/priority rules.
- `m_SkillIndex`: This is not a SkillID itself. It's an index into a skill list/array (from testing: `TacticalSkillList.m_UseSkills[]` for the enemy's `m_SkillsID`)
- `m_SelectType`: A selection mode enum, `Kingship_structs.hpp` defines the following:

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

- `m_FriendlyIndex`: who to target on the "friendly" side (from the enemy's perspective?). `-1` here would likely mean "no fixed friendly; maybe: pick based on `SelectType`(?)"
- `m_PrioritySkill`: (untested) if set, it may override normal selection and force a specific skill index (or mark a skill index as preferred?)
- `m_TurnCount`: (untested) repeats an action every `N` turns where `N` is the value given to this key, provided the specified tactics ID's conditions are met. May have a different meaning depending on the SelectType.
- `m_GroupIndex`: grouping key, likely used to separate actions by phase or restrict number of actions per group per turn or coordinate multi-enemy behavior in a group
- Additional action-level conditions (like `TacticalList`, but specific to the specified action): `m_Conditions`, `m_Params`, `m_AilmentTypes`, `m_StatusTypes`, `m_WeaponTypes`, `m_MagicTypes`

#### Runtime reading semantics
**Primary C evidence:**

##### C-ENEMY-02: EnemyID materializer with concrete row offsets

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

##### C-TACT-01: TacticalList/TacticalSkillList row resolvers and skill-list accessor

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

##### C-TACT-02: TacticalActionList row resolver

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

##### C-TACT-03: TacticalActionList condition-lane consumer

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

##### C-TACT-04: Tactical condition tuple evaluator

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

##### C-TACT-05: TacticalActionList skill-index and SelectType semantics in candidate decision

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

##### C-TACT-06: TacticalActionList recommendation filter

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

##### C-TACT-07: Sequential presage/action read of group/turn fields

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

**Field-level interpretation:**

| Key | Runtime C locus | Interpretation | Status | Evidence |
|---|---|---|---|---|
| `m_AilmentTypes` | `+0x48` (array); no shorter named assignment survived in the cited block | The action row stores condition/filter lanes beginning at row `+0x28`; `CheckActionCondition` passes these six parallel arrays to the tactical condition evaluator. | recovered | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_Conditions` | `+0x28` (array); no shorter named assignment survived in the cited block | The action row stores condition/filter lanes beginning at row `+0x28`; `CheckActionCondition` passes these six parallel arrays to the tactical condition evaluator. | recovered | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_FriendlyIndex` | `+0x14` (scalar); no shorter named assignment survived in the cited block | The action row reads row `+0x14` into a local selector during candidate decision. Hex-Rays did not preserve the DB field name here; the role is a target/friendly selector lane, not a skill id. | recovered | `C-TACT-05` |
| `m_GroupIndex` | `+0x20` (scalar); no shorter named assignment survived in the cited block | Sequential-presage logic reads row `+0x20` and compares it with the previously seen group value; this is the strongest recovered interpretation for the action grouping lane. | recovered | `C-TACT-07` |
| `m_MagicTypes` | `+0x78` (array); no shorter named assignment survived in the cited block | The action row stores condition/filter lanes beginning at row `+0x28`; `CheckActionCondition` passes these six parallel arrays to the tactical condition evaluator. | recovered | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_NoRecommendSkillIndex` | `C-TACT-05:17393877` `v6->m_NoRecommendSkillIndex = *(_DWORD *)&v24[28];` | This is a runtime component field, not a DB key. It is set from `TacticalActionList +0x1C` when `SelectType == 6`, then candidate collection rejects actions whose `m_SkillIndex` equals it. | cross-reference | `C-TACT-05`, `C-TACT-06` |
| `m_Params` | `+0x38` (array); no shorter named assignment survived in the cited block | The action row stores condition/filter lanes beginning at row `+0x28`; `CheckActionCondition` passes these six parallel arrays to the tactical condition evaluator. | recovered | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_PrioritySkill` | `+0x1C` (scalar); no shorter named assignment survived in the cited block | The action row reads row `+0x1C` when `SelectType` is `5` or `6`; the value is written into `m_RecommendSkillIndex` or `m_NoRecommendSkillIndex`. The export does not prove a separate priority mechanism beyond this recommendation operand. | negative / partial | `C-TACT-05`, `C-TACT-06` |
| `m_RecommendSkillIndex` | `C-TACT-05:17393873` `v6->m_RecommendSkillIndex = *(_DWORD *)&v24[28];`<br>`C-TACT-05:17393881` `*(_QWORD *)&v6->m_RecommendSkillIndex = -1;` | This is a runtime component field, not a DB key. It is set from `TacticalActionList +0x1C` when `SelectType == 5`, then candidate collection keeps only actions whose `m_SkillIndex` equals it. | cross-reference | `C-TACT-05`, `C-TACT-06` |
| `m_SelectType` | `+0x18` (byte); no shorter named assignment survived in the cited block | The action row reads row `+0x18` as a byte selection mode. The recovered branches compare it to `4` for sequential behavior and to `5`/`6` for recommend/no-recommend filtering. | recovered | `C-TACT-05`, `C-TACT-07` |
| `m_SkillIndex` | `+0x10` (scalar); no shorter named assignment survived in the cited block | The action row reads row `+0x10` as an index into the current enemy’s `m_pSkillIDs`/`TacticalSkillList.m_UseSkills` array. It is not a direct `SkillID`. | recovered | `C-TACT-05`, `C-TACT-01` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | `m_SkillsID` is not a `TacticalActionList` field. It is read from `EnemyID +0x84`, resolves a `TacticalSkillList` row, and supplies the `m_UseSkills` array indexed by `m_SkillIndex`. | cross-reference | `C-ENEMY-02`, `C-TACT-01`, `C-TACT-05` |
| `m_StatusTypes` | `+0x58` (array); no shorter named assignment survived in the cited block | The action row stores condition/filter lanes beginning at row `+0x28`; `CheckActionCondition` passes these six parallel arrays to the tactical condition evaluator. | recovered | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_TurnCount` | `+0x24` (scalar); no shorter named assignment survived in the cited block | Ordered/sequential logic reads row `+0x24` as an additional cadence/order operand. The exact field name does not survive, but the row lane is consumed in sequential presage/action selection. | recovered | `C-TACT-07` |
| `m_WeaponTypes` | `+0x68` (array); no shorter named assignment survived in the cited block | The action row stores condition/filter lanes beginning at row `+0x28`; `CheckActionCondition` passes these six parallel arrays to the tactical condition evaluator. | recovered | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
