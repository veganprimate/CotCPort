### **언어:**  [영어](customenms_en.md) | [일본어](customenms_jp.md) | [중국어](customenms_zh.md) | [한국어](customenms_kr.md)

> 이 가이드의 런타임 증거 표기 규칙: 「직접 사용」는 게임플레이/UI 코드가 어떤 작업을 위해 해당 필드를 읽는다는 뜻이다. 「행 레이아웃 증거」는 조회 함수 또는 구체화 처리(구체화 처리)가 오프셋/형태를 확정한다는 뜻이다. 「구체화 한정」는 해당 필드가 런타임 객체로 복사되는 것은 확인되지만, 인용한 C 발췌 안에서는 이후 이를 의미론적으로 소비하는 코드가 복원되지 않았다는 뜻이다.

『옥토패스 트래블러 0』에 새로운 적과 전투를 추가하는 작업은 주로 `AIBattle`, `Enemy`, 그리고 필요에 따라 `Skill`, `Texture`, `Battle` 아래의 DataBase 파일을 편집하는 방식으로 이루어진다. 아래에서는 관련 파일을 모두 설명한다. 제목의 별 개수는 해당 파일을 편집할 필요성이 어느 정도인지 나타낸다(별이 적을수록 더 특수한 상황에서 사용됨). 다만 어떤 목적에 어떤 파일을 수정해야 하는지 파악하려면 글 전체를 읽는 것을 권장한다.

## ■ `Enemy/` 아래
### ■ (***) `EnemyFormations`
필드 위에 배치되는 새로운 적 포메이션을 지정할 수 있다. `m_PositionID`는 `BattlePositions.m_id`를 입력으로 받는다(`BattlePositions`는 `Battle/` 아래에 있다). 적이 많은 커스텀 전투에서는 0이 아닌 `m_PositionID` 항목이 충분히 들어 있는 적절한 기존 포메이션을 찾거나 직접 포메이션을 만들어야 할 수 있다. 일부 `PositionID`는 전투 맵에 따라 적 위치가 크게 달라질 수 있다.
`Kingship_structs.hpp`는 OT0의 포메이션에 다음 라벨을 할당한다.
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
#### ■ 런타임 읽기 의미론
이 소절에서는 위에서 언급한 각 키에 대해 필드 단위의 런타임 위치를 제시한다. 즉 정확한 이름이 남아 있는 C 대입/읽기, 모호하지 않은 행 오프셋 레인, 또는 C 내보내기가 해당 키를 살아 있는 필드로 보존하지 않은 경우의 부정적 복원 진술 중 하나다. 해석은 의도적으로 인용된 C가 허용하는 범위로 제한한다.

【주요 C 증거】

##### ◆ C-FORM-01: EnemyFormations 행 해석기

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

##### ◆ C-FORM-02: 포메이션 위치를 BattlePositions로 해석

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_PositionID` | `+0x10`(배열). 인용된 블록에는 더 짧은 이름 있는 대입이 남아 있지 않다 | 포메이션 행은 `FormID`로 해석된다. 그다음 게임은 행 `+0x10`의 `TArray<int>`를 인덱스로 읽고, 선택된 요소를 `BattlePositions` 행 ID로 취급한다. 반환된 전투 위치 행은 XYZ 좌표와 우선순위를 제공한다. | 복원됨 | `C-FORM-01`, `C-FORM-02` |

### ■ (***) `EnemyBattleAnimSet`
『옥토패스 트래블러: 대륙의 패자』에서 CotC는 적에게 특정 Flipbook 에셋을 가리키는 ID를 할당하지만, OT0에서는 이러한 에셋을 각각 참조하는 ID 묶음을 사용한다(`m_EnemyTextureIDs`는 `Textures` 아래 `EnemyTexID`의 `m_id`를 입력으로 받는다).
`m_EnemyTextureIDs[0]`은 **일반적으로** 스프라이트의 대기 애니메이션(접미사 `Idl00`)을 가리키며, `m_EnemyTextureIDs[1]`부터 `m_EnemyTextureIDs[3]`까지는 **일반적으로** 스프라이트의 공격 애니메이션(접미사 `Atk00`부터 `Atk02`)을 가리킨다. 적어도 대기 애니메이션용 Flipbook 에셋은 추가해야 한다. 즉 `EnemyTexID` 아래에 새 경로를 참조시키고, 그 새 항목의 `m_id`를 여기서 새로 추가하는 항목의 `m_EnemyTextureIDs[0]`에 사용해야 한다. 그 외 항목은 선택 사항이다.

앞서 말했듯이 이 인덱스들이 Idl00, Atk00 등으로 해석되는 것은 어디까지나 **일반적인** 운용일 뿐이며, 런타임 코드가 그런 대응을 강제하지는 않는다. 먼저 `EnemyTypeID.m_AnimationSetIDs[index]`가 `EnemyBattleAnimSet` 행을 선택하고, 선택된 행의 `m_EnemyTextureIDs`가 `EnemyTexID` 리소스 ID로 스캔되거나 복사된다. 액션 디스패치는 각 텍스처에 대해 `EnemyTexID` 테이블에 `EBATTLE_ACTION_ID`를 질의하여 얻어진다. 따라서 이 배열 순서는 의미론적으로 **우선순위/동률 시 타이브레이크 순서**이지, **액션 코드 네임스페이스**가 아니다.

또한 CotC의 `EnemyType` 아래 `m_FlipbookID`는 이러한 추가 배열 구성요소를 다른 방식으로 사용하는 것처럼 보인다는 점에 유의하라. 인덱스 0은 여전히 대기 텍스처를 가리키지만, CotC는 일부 적에게 보이지 않는 텍스처를 인덱스 1에 할당한다(예: Shadow of Twin Worlds의 Shadow). 이 인덱스 1 텍스처는, 특히 그것도 대기 텍스처로 읽히는 경우, 인덱스 0의 텍스처를 덮어쓸 수 있다.

Flipbook 에셋은 다음 스케일을 가정할 수 있다(`Kingship_structs.hpp` 참조).

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


NPC 텍스처를 사용하는 전투에서는 `EnemyBattleAnimSet`에 별도 항목을 만들 **필요가 없다**. NPC는 보통 이미 전투용 스프라이트시트(종종 `_B` 접미사로 끝남)를 가지고 있기 때문이다. 단순히 `EnemyType` 아래에 NPC의 `CharaID`를 지정하면 된다(아래 및 필요하다면 [커스텀 캐릭터 추가 방법](customchars_en.md) 참조). 런타임 폴백 경로도 이를 확인해 준다. 일반 적 타입에서 적 Flipbook을 로드할 수 없는 경우, `SetupEnemyFlipbook`은 해당 텍스처 인덱스에 대해 대신 `EnemyTypeID.m_NpcCharaID`를 시도할 수 있다.

#### ■ 런타임 읽기 의미론(필드 단위 Hex-Rays 증거)
`FEnemyBattleAnimSetBase`는 32바이트 행이다. 복원된 복사 연산자는 행 ID와 `TArray<int>` 크기의 멤버 하나만 복사한다. 이후의 모든 전투 측 소비자도 이 레이아웃과 일치한다. 이 행은 `m_id`로 키 지정되며, `+0x10`의 배열은 `m_EnemyTextureIDs`로 해석된다.

【주요 C 증거】

##### ◆ C-ANIM-01: 복원된 구조체 크기와 복사되는 필드

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

**해석.** 디컴파일러의 `TArray<FPackedNormal>` 타입은 템플릿 추론 아티팩트이지만, 메모리 조작 자체는 모호하지 않다. 이 행 복사는 오프셋 `+0x08`에 32비트 스칼라를 쓰고, 이어서 `+0x10`에서 시작하는 `TArray<unsigned int>`를 복사한다. 아래의 이름 있는 런타임 소비자에서 이 스칼라는 `m_id`이고 배열은 `m_EnemyTextureIDs`다.

##### ◆ C-ANIM-02: `m_id` 직접 조회, 32바이트 행 스트라이드

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

**해석.** `EnemyBattleAnimSet`은 `m_id` 동등 비교로 해석된다. 이 경로에는 라벨 조회가 없다. 끝 포인터는 `32 * ArrayNum`을 사용하므로 행 스트라이드는 정확히 32바이트다.

##### ◆ C-ANIM-03: `EnemyTypeID.m_AnimationSetIDs[index]`가 애니메이션 세트 행을 선택함

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

**해석.** `index`/`TextureIdx` 인수는 `EnemyTypeID.m_AnimationSetIDs`(`+0x10` 포인터와 `+0x18` 개수)에서 한 항목을 선택한다. 이것은 `m_EnemyTextureIDs`에 대한 인덱스가 아니다. 모딩상 함의는, 한 타입이 여러 애니메이션 세트 참조를 가질 수 있다는 것이다. 바깥 슬롯은 `EnemyTypeID`가 결정하고, 안쪽 텍스처 ID는 `EnemyBattleAnimSet`에 존재한다.

##### ◆ C-ANIM-04: 애니메이션 세트 슬롯 수는 `EnemyTypeID.m_AnimationSetIDs`의 개수

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

**해석.** 일반 적 초기화는 이 개수를 순회하며 애니메이션 세트 슬롯마다 `SetupEnemyFlipbook`을 한 번 호출한다. 이는 `EnemyTypeID.m_AnimationSetIDs`와 `EnemyBattleAnimSet.m_EnemyTextureIDs`를 더욱 분명히 분리한다.

##### ◆ C-ANIM-05: 일괄 획득은 의미론적 인덱싱 없이 `m_EnemyTextureIDs`를 복사함

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

**해석.** 이 경로는 `m_EnemyTextureIDs`를 원시 리소스 ID 배열로 취급한다. 이 복사 경로에서는 배열 인덱스에 액션 의미를 부여하지 않는다.

##### ◆ C-ANIM-06: 액션 지정 조회는 `EnemyTexID` 액션 메타데이터에 대한 첫 번째 일치 스캔

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

**해석.** `m_EnemyTextureIDs`의 순서는 첫 번째 일치 우선순위로서만 의미가 있다. 요청된 액션은 `AResourceManager::GetEnemyTexActionID`로 복원되는 `EnemyTexID.m_ActionTypeID`와 대조되며, 배열 인덱스와 대조되는 것이 아니다.

##### ◆ C-ANIM-07: `EnemyTexID`가 액션 ID와 유니크 머티리얼 플래그를 제공함

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

**해석.** 여기서 `EnemyTexID` 행은 40바이트 스트라이드로 복원된다. 액션 ID는 행 `+0x10`에서 읽히고, 유니크 머티리얼 플래그는 행 `+0x14`에서 읽힌다. 따라서 `EnemyBattleAnimSet` 자체가 대기/공격 의미를 인코딩하는 것이 아니라, 그러한 의미를 가진 `EnemyTexID` 행을 참조하는 것이다.

##### ◆ C-ANIM-08: 로딩 경로는 액션을 키로 하는 Flipbook 세트를 구성함

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

**해석.** 일반 적 타입과 적 부품은 모두 같은 Flipbook 구성 경로로 합류한다. 각 `EnemyTexID`마다 리소스 카테고리 `2` 객체가 로드된 뒤, `EBATTLE_ACTION_ID`를 키로 하는 Flipbook 세트에 삽입된다. 부품 타입 ID는 게임 파라미터 `0x1B66`을 더하거나 빼서 구분된다.

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_id` | `result->m_id != AnimSetID`; 행 스트라이드 `32` | `EnemyBattleAnimSet`의 주요 런타임 키. 조회는 로드된 데이터 리스트를 ID로 선형 스캔한다. | 복원됨 | `C-ANIM-01`, `C-ANIM-02` |
| `m_EnemyTextureIDs` | `m_EnemyTextureIDs.ArrayNum`, `.AllocatorInstance.Data`; 행 오프셋 `+0x10` | `EnemyTexID.m_id` 값의 `TArray<int>`. 일괄 경로에서는 원시 ID로 복사하고, 액션 경로에서는 이를 스캔하여 `EnemyTexID` 메타데이터로 액션 의미를 결정한다. | 복원됨 | `C-ANIM-05`, `C-ANIM-06`, `C-ANIM-07`, `C-ANIM-08` |
| `TextureIdx` / 애니메이션 세트 슬롯 | `EnemyTypeID` `+0x10/+0x18`, `GetEnemyTypeFlipbookSetNum` 개수 | `EnemyTypeID.m_AnimationSetIDs`의 한 요소를 선택한 뒤 그 값을 `EnemyBattleAnimSet.m_id`로 해석한다. | 복원됨 | `C-ANIM-03`, `C-ANIM-04` |

### ■ (**) `EnemyParts`
커스텀 보스가 여러 시각적/기계적 부품으로 구성되어 있다면(예: Or'Galdera, Amatsukami no Orochi), 이 파일에 각 부품을 지정한다. Shadow of Twin Worlds처럼 이 파일을 전혀 사용하지 않는 보스도 있다. C 내보내기는 `EnemyParts`가 독립된 적 마스터 테이블이 아님을 보여 준다. 부품은 `EnemyTypeID.m_PartsID`에서 선택된 부품 행을 사용해, 소유자 적에 종속된 `AQPBattleCharacterBase`로 인스턴스화된다.

런타임에서는 적 구성 중 소유자 적의 `EnemyTypeID.m_PartsID` 배열을 읽는다. 각 양수 부품 ID는 `UEnemyDB::GetEnemyPartsDB`로 해석된다. 행이 존재하면 배틀 매니저가 해당 부품의 전투 캐릭터를 생성하고, `PartsIndex`를 할당하며, `m_PartsOwner`를 통해 소유자에 연결하고, 소유자/매니저의 부품 컨테이너에 등록한다. 이후 부품의 스프라이트는 위에서 설명한 것과 같은 `EnemyBattleAnimSet` 기계장치를 통해 로드되지만, 그때 부품 ID는 `UGameParamsUtility::GetParamInt(0x1B66)`을 더해 특수한 타입 ID 네임스페이스로 매핑된다.
#### ■ 런타임 읽기 의미론
`FEnemyPartsBase`는 56바이트 행이다. 내보내기는 일부 의미 이름만 보존하므로, 이 절에서는 C가 원래 필드 식별자를 보존하지 않은 곳에 행 오프셋을 사용한다. 모딩에서 중요한 점은 이 오프셋들이 비활성 패딩이 아니라는 것이다. 엔진은 애니메이션 선택, 배치, 초기 가시성, 브레이크/선택 시각 전파, 조건부 표시 변경, 페이드 동작을 위해 이들을 읽는다.

【주요 C 증거】

##### ◆ C-PARTS-01: 복원된 행 크기와 행 멤버 복사 순서

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

**해석.** 행 스트라이드는 56바이트다. 복사되는 레인은 `+0x08` ID, `+0x0C`의 겹치는 8바이트 쌍, `+0x14`, `+0x18`, `+0x1C`, `+0x20`의 스칼라 레인, `+0x24`, `+0x25`, `+0x26`의 바이트, 그리고 `+0x28`, `+0x2C`, `+0x30`의 스칼라 레인이다. 이후 소비자들이 이 레인들에 의미를 부여한다.

##### ◆ C-PARTS-02: `m_EnemyParts.m_DBAccess`를 통한 키 조회

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

**해석.** `PartsID`는 테이블의 DB 접근 헬퍼로 해석된다. DB 헬퍼가 그렇게 매핑하지 않는 한 이것은 조밀한 배열 인덱스가 아니다. 행은 테이블이 로드되어 있고 키가 범위 안에서 해석될 때만 반환된다.

##### ◆ C-PARTS-03: `+0x14`는 `EnemyBattleAnimSet.m_id` 참조

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

**해석.** 행 오프셋 `+0x14`는 텍스처 ID가 아니다. 이것은 `GetFlipbookSet`에 전달되므로 `EnemyBattleAnimSet.m_id`다. 이것이 해당 부품의 애니메이션 세트 참조다.

##### ◆ C-PARTS-04: 소유자 `EnemyTypeID.m_PartsID`가 부품을 열거함

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

**해석.** 소유자 타입 행은 `+0xC0/+0xC8`에 `TArray<int>`를 제공하며, 이는 `EnemyTypeID.m_PartsID`에 해당한다. 0이 아닌 각 항목은 부품 ID이고, 루프 인덱스는 `PartsIndex`로 전달된다. 따라서 부품은 별개의 인카운터 멤버가 아니라 특정 소유자/타입 구성 경로에 종속된다.

##### ◆ C-PARTS-05: 행 오프셋 `+0x18/+0x1C/+0x20`이 부품 위치에 영향을 줌

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

**해석.** 오프셋 `+0x18`과 `+0x1C`는 부동소수점로 읽히고, 오프셋 `+0x20`은 정수로 읽힌 뒤 `-0.1`로 스케일된다. 두 호출 지점은 로컬 벡터 명명은 다르지만, 둘 다 동일한 세 행 레인을 소비하여 소유자에서 파생된 위치를 기준으로 부품을 재배치한다.

##### ◆ C-PARTS-06: setup은 부품을 소유자에 연결하고 초기 가시성을 읽음

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

**해석.** 오프셋 `+0x24`는 초기 가시 플래그다. `m_PartsOwner`, `m_PartsID`, `m_PartsIndex`는 생성된 캐릭터의 런타임 상태 필드이지 데이터 테이블 필드가 아니다. `PartsID + ParamInt` 변환은 Flipbook 로딩에서 부품 ID를 `GetEnemyPartsFlipbookID`로 라우팅하는 데 쓰이는 판별자다.

##### ◆ C-PARTS-07: 조건부 표시 켜기/끄기는 행 오프셋 `+0x28`과 `+0x2C`를 사용함

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

**해석.** `+0x28`은 표시 켜기 조건 ID이며, 부품이 숨겨져 있을 때만 평가된다. `+0x2C`는 표시 끄기 조건 ID이며, 부품이 보이는 상태일 때만 평가된다. 둘 다 전투 중단/조건 묶음으로 해석되고 `UBattleUtility::CheckConditions`로 평가된다. 원시 불리언이 아니다.

##### ◆ C-PARTS-08: 페이드/dither 동작은 행 오프셋 `+0x30`을 읽음

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

**해석.** 오프셋 `+0x30`은 페이드 동작을 제어하는 부동소수점로 읽힌다. 0 이하 값은 즉시 숨김을 허용하고, 양수 값은 가시성 변경을 dither fade 요청으로 라우팅한다.

##### ◆ C-PARTS-09: 브레이크/선택 시각 전파는 바이트 `+0x25`와 `+0x26`을 사용함

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

**해석.** 바이트 `+0x26`이 설정되어 있지 않을 때 바이트 `+0x25`는 재생 속도/브레이크 애니메이션 연결 경로를 게이트한다. 바이트 `+0x26`은 부품 전용 실드 브레이크와 선택 머티리얼 동작을 게이트한다. 이 바이트들은 전투 상태의 시각 전파에서 직접 소비된다.

**복원된 행 오프셋 맵:**

| 행 위치 | 런타임 해석 | 증거 |
|---|---|---|
| `+0x08` | `EnemyParts.m_id`, `m_EnemyParts.m_DBAccess`로 해석됨. | `C-PARTS-01`, `C-PARTS-02` |
| `+0x0C/+0x10` | 8바이트 레인으로 복사되지만, 인용된 게임플레이 소비자들은 안전한 의미 이름을 부여하지 않는다. | `C-PARTS-01` |
| `+0x14` | 애니메이션 세트 ID. `GetFlipbookSet`에 전달되므로 `EnemyBattleAnimSet.m_id`를 참조한다. | `C-PARTS-03` |
| `+0x18` | 부품 배치에 적용되는 공간 부동소수점. | `C-PARTS-05` |
| `+0x1C` | 부품 배치에 적용되는 공간 부동소수점. | `C-PARTS-05` |
| `+0x20` | `-0.1`로 스케일되어 부품 배치에 적용되는 공간 정수. | `C-PARTS-05` |
| `+0x24` | 초기 애니메이션 가시 플래그. | `C-PARTS-06` |
| `+0x25` | 브레이크/재생 속도 연결 플래그. | `C-PARTS-09` |
| `+0x26` | 실드 브레이크/선택 머티리얼 연결 플래그. | `C-PARTS-09` |
| `+0x28` | 표시 켜기 조건 ID. 숨겨져 있을 때만 평가됨. | `C-PARTS-07` |
| `+0x2C` | 표시 끄기 조건 ID. 보이는 상태일 때만 평가됨. | `C-PARTS-07` |
| `+0x30` | 가시성 변경을 위한 페이드/dither 부동소수점. | `C-PARTS-08` |

### ■ (****) `EnemyTypeID`
이 에셋은 기본적으로 OT0에서 적 원형의 마스터 “전투 스프라이트 표현 설명자”이며, 게임에 다음 사항을 알려 준다.
- 어떤 애니메이션 세트를 사용할지, 또는 대신 어떤 NPC/캐릭터 스프라이트시트를 연결할지(`m_AnimationSetIDs`는 `EnemyBattleAnimSet`의 `m_id`를 입력으로 받고, `m_NpcCharaID`는 `Character/` 아래 `CharaID`의 `m_id`를 입력으로 받음)
- 스프라이트와 모든 오버레이를 어떻게 스케일하고 배치할지:
  - 히트 스파크(`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`)
  - 실드와 약점 오버레이
  - 적 효과(`m_EffectOffsetX`,`m_EffectOffsetY`, 그리고 `m_EffectFromBottomOffsetY`: 스프라이트 하단을 기준으로 한 오프셋이며 지면 기반 효과에서 흔함)
  - 데미지 숫자(`m_DamageOffsetX`,`m_DamageOffsetY`)
  - 상태 이상(`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`)
  - 커서(`m_CursorOffsetX`,`m_CursorOffsetY`)
- 해당 적 원형과 연결할 적 부품(`m_PartsID`)
- 해당 적 타입에 사용할 사운드/보이스:
  - `m_CallSE`(즉 “call sound 효과”)
  - `m_VoiceSymbol`(`Sound` 아래 `VoiceSymbolList`를 인덱싱할 가능성이 있는 정수)
  - `m_CueSheet`(`Sound/` 아래 `SoundSheetList`를 인덱싱하고 이 적의 보이스 세트에 사용할 cuesheet를 선택할 가능성이 있음)
- 어떤 부착 효과(VFX)를 어디에 생성할지(`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`)
- 참조 텍스처(보통 Flipbook 에셋/캐릭터 스프라이트시트의 첫 프레임)의 어느 위치를 턴 순서 아이콘에 사용할지(`m_OrderTexUS`, `m_OrderTexVS`; 큰 아이콘, 즉 지정 캐릭터가 행동 중일 때는 `m_OrderTexUL`과 `m_OrderTexVL` 사용), 그리고 참조 텍스처를 순서 아이콘에 어떤 스케일로 렌더링할지(`m_OrderScaleS`; `m_OrderScaleL`). 예를 들어 Shadow of Twin Worlds 전투의 Shadow는 매우 작은 순서 스케일을 사용하여 몸 전체가 순서 아이콘에 들어오도록 한다.
#### ■ 추가 런타임 관련 필드
  - `m_OverrideOrderIconAnimSetID`: 순서 아이콘에 특수 텍스처 에셋을 사용하고 싶을 때 사용
  - `m_AttachEffectInGodbeastProduction`: 행 바이트 `+0xA5`로 구체화됨. 인용된 C 경로에서는 직접적인 게임플레이/UI 소비자가 복원되지 않았으므로, 그 의미론적 역할은 전투/필드 부착 효과 필드보다 약하다.
  - `m_AddStartUV`: 행 바이트 `+0xA4`로 구체화됨. 인용된 C 경로에서 직접 소비자가 복원되지 않았으므로, 의미가 미해결인 살아 있는 필드로 취급하라.
  - `m_KeepVisibleAttachEffectOnMotion`: `FEnemyTypeIDBase`의 일부로 구체화되고 행 바이트 `+0xA6`로 복원됨. 뒤의 `EnemyTypeID` 오프셋 맵은 이를 `m_AddStartUV` 및 `m_AttachEffectInGodbeastProduction`과 별도로 분류한다. 정확한 시각 동작은 내보내기 noise가 아니라 부분 소비자 증거가 있는 런타임 필드로 취급하라.
  - `m_LinkOwnerAnim`: 행 바이트 `+0xD0`로 구체화됨. C 내보내기는 이것이 `EnemyTypeID` 행에 남아 있음을 증명하지만, 정확한 하위 소비자는 아직 부분적으로만 복원되어 있다.
  - `m_DefeatEffect`: 사망 시 재생되는 효과 ID(예: Bestower of All의 특수 격파 효과). `Kingship_structs.hpp`는 이를 다음과 같이 지정한다.

```hpp
enum class EENEMY_DEFEATEFFECT : uint8
{
	NONE                                     = 0,
	EDE_AGM_CHP8_02                          = 1,
	EDE_AGM_CHP8_03_RING                     = 2,
	EENEMY_MAX                               = 3,
};
```

       
- `m_ChangeColorID`: 색상 변경 테이블의 ID이며, `Character/` 아래 `CharaColorChangeParams`와 관련이 있을 수 있다. OT0에서는 Zero/the Ringbearer Chosen이 자신의 그림자와 맞설 때만 사용된다(`ChangeColorID` 6). CotC의 `EnemyType`에는 이 키가 전혀 없다. SDK에는 다음과 같은 관련 가능성이 있는 enum이 있다.

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
#### ■ 런타임 읽기 의미론(필드 단위 Hex-Rays 증거)
##### ◆ C-TYPE-01: EnemyTypeID 행 해석기

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

##### ◆ C-TYPE-02: EnemyTypeID 구체화 필드 복사

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

##### ◆ C-TYPE-03: EnemyID에서 EnemyTypeID로, 그리고 선택된 EnemyTypeID 소비자

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_AddStartUV` | `C-TYPE-02:17041883` `this->m_AddStartUV = __that->m_AddStartUV;` | 이 키는 행 오프셋 `+0xA4`의 바이트/불리언 레인으로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentInfoOffsetX` | `C-TYPE-02:17041865` `this->m_AilmentInfoOffsetX = __that->m_AilmentInfoOffsetX;` | 이 키는 행 오프셋 `+0x5C`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentOffset` | `C-TYPE-02:17041866` `this->m_AilmentOffset = __that->m_AilmentOffset;` | 이 키는 행 오프셋 `+0x60`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AnimationSetIDs` | `C-TYPE-02:17041822` `this->m_AnimationSetIDs.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041823` `ArrayNum = __that->m_AnimationSetIDs.ArrayNum;`<br>`C-TYPE-02:17041824` `Data = __that->m_AnimationSetIDs.AllocatorInstance.Data;` 및 4개의 추가 로컬 출현 | 이 키는 행 오프셋 `+0x10`의 `TArray` 헤더로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffect` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;`<br>`C-TYPE-02:17041886` `this->m_AttachEffect = __that->m_AttachEffect;` | 이 키는 행 오프셋 `+0xA8`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffectInGodbeastProduction` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;` | 이 키는 행 오프셋 `+0xA5`의 바이트/불리언 레인으로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetX` | `C-TYPE-02:17041887` `this->m_BattleAttachEffectOffsetX = __that->m_BattleAttachEffectOffsetX;` | 이 키는 행 오프셋 `+0xAC`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetY` | `C-TYPE-02:17041888` `this->m_BattleAttachEffectOffsetY = __that->m_BattleAttachEffectOffsetY;` | 이 키는 행 오프셋 `+0xB0`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | 이 키는 행 오프셋 `+0x7C`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | 이 키는 행 오프셋 `+0x30`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CueSheet` | `C-TYPE-02:17041875` `this->m_CueSheet = __that->m_CueSheet;` | 이 키는 행 오프셋 `+0x84`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetX` | `C-TYPE-02:17041869` `this->m_CursorOffsetX = __that->m_CursorOffsetX;` | 이 키는 행 오프셋 `+0x6C`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetY` | `C-TYPE-02:17041870` `this->m_CursorOffsetY = __that->m_CursorOffsetY;` | 이 키는 행 오프셋 `+0x70`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetX` | `C-TYPE-02:17041867` `this->m_DamageOffsetX = __that->m_DamageOffsetX;` | 이 키는 행 오프셋 `+0x64`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetY` | `C-TYPE-02:17041868` `this->m_DamageOffsetY = __that->m_DamageOffsetY;` | 이 키는 행 오프셋 `+0x68`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_DefeatEffect` | `C-TYPE-02:17041909` `this->m_DefeatEffect = __that->m_DefeatEffect;` | 이 키는 행 오프셋 `+0xD4`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectFromBottomOffsetY` | `C-TYPE-02:17041864` `this->m_EffectFromBottomOffsetY = __that->m_EffectFromBottomOffsetY;` | 이 키는 행 오프셋 `+0x58`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetX` | `C-TYPE-02:17041862` `this->m_EffectOffsetX = __that->m_EffectOffsetX;` | 이 키는 행 오프셋 `+0x50`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetY` | `C-TYPE-02:17041863` `this->m_EffectOffsetY = __that->m_EffectOffsetY;` | 이 키는 행 오프셋 `+0x54`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffect` | `C-TYPE-02:17041889` `this->m_FieldAttachEffect = __that->m_FieldAttachEffect;`<br>`C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;`<br>`C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | 이 키는 행 오프셋 `+0xB4`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetX` | `C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;` | 이 키는 행 오프셋 `+0xB8`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetY` | `C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | 이 키는 행 오프셋 `+0xBC`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosX` | `C-TYPE-02:17041857` `this->m_HitPosX = __that->m_HitPosX;` | 이 키는 행 오프셋 `+0x3C`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosY` | `C-TYPE-02:17041858` `this->m_HitPosY = __that->m_HitPosY;` | 이 키는 행 오프셋 `+0x40`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleX` | `C-TYPE-02:17041859` `this->m_HitScaleX = __that->m_HitScaleX;` | 이 키는 행 오프셋 `+0x44`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleY` | `C-TYPE-02:17041860` `this->m_HitScaleY = __that->m_HitScaleY;` | 이 키는 행 오프셋 `+0x48`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_KeepVisibleAttachEffectOnMotion` | `C-TYPE-02:17041885` `this->m_KeepVisibleAttachEffectOnMotion = __that->m_KeepVisibleAttachEffectOnMotion;` | 이 키는 행 오프셋 `+0xA6`의 바이트/불리언 레인으로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_LinkOwnerAnim` | `C-TYPE-02:17041908` `this->m_LinkOwnerAnim = __that->m_LinkOwnerAnim;` | 이 키는 행 오프셋 `+0xD0`의 바이트/불리언 레인으로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_NpcCharaID` | `C-TYPE-02:17041838` `this->m_NpcCharaID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041839` `v6 = __that->m_NpcCharaID.ArrayNum;`<br>`C-TYPE-02:17041840` `v7 = __that->m_NpcCharaID.AllocatorInstance.Data;` 및 4개의 추가 로컬 출현 | 이 키는 행 오프셋 `+0x20`의 `TArray` 헤더로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleL` | `C-TYPE-02:17041882` `this->m_OrderScaleL = __that->m_OrderScaleL;` | 이 키는 행 오프셋 `+0xA0`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleS` | `C-TYPE-02:17041879` `this->m_OrderScaleS = __that->m_OrderScaleS;` | 이 키는 행 오프셋 `+0x94`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUL` | `C-TYPE-02:17041880` `this->m_OrderTexUL = __that->m_OrderTexUL;` | 이 키는 행 오프셋 `+0x98`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUS` | `C-TYPE-02:17041877` `this->m_OrderTexUS = __that->m_OrderTexUS;` | 이 키는 행 오프셋 `+0x8C`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVL` | `C-TYPE-02:17041881` `this->m_OrderTexVL = __that->m_OrderTexVL;` | 이 키는 행 오프셋 `+0x9C`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVS` | `C-TYPE-02:17041878` `this->m_OrderTexVS = __that->m_OrderTexVS;` | 이 키는 행 오프셋 `+0x90`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_OverrideOrderIconAnimSetID` | `C-TYPE-02:17041876` `this->m_OverrideOrderIconAnimSetID = __that->m_OverrideOrderIconAnimSetID;` | 이 키는 행 오프셋 `+0x88`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_PartsID` | `C-TYPE-02:17041892` `this->m_PartsID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041893` `v8 = __that->m_PartsID.ArrayNum;`<br>`C-TYPE-02:17041894` `v9 = __that->m_PartsID.AllocatorInstance.Data;` 및 4개의 추가 로컬 출현 | 이 키는 행 오프셋 `+0xC0`의 `TArray` 헤더로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetX` | `C-TYPE-02:17041872` `this->m_SupportOffsetX = __that->m_SupportOffsetX;` | 이 키는 행 오프셋 `+0x78`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetY` | `C-TYPE-02:17041871` `this->m_SupportOffsetY = __that->m_SupportOffsetY;` | 이 키는 행 오프셋 `+0x74`의 32비트 부동소수점로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | 이 키는 행 오프셋 `+0x80`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_id` | `C-TYPE-02:17041821` `this->m_id = __that->m_id;` | 이 키는 행 오프셋 `+0x08`의 32비트 스칼라로 읽힌다. 행 자체는 `UEnemyDB::GetEnemyTypeDB(TypeID)`를 통해 해석되며, 스트라이드는 `0xD8`바이트다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |

#### ■ 추가 런타임 증거: 보조 `EnemyTypeID` 소비자

여러 시각/UI 시스템은 전체 `FEnemyTypeIDBase` 행을 구체화하지 않는다. 대신 먼저 `EnemyID.m_TypeID`를 해석한 다음 `EnemyTypeID`에서 특정 바이트, 부동소수점, 배열 레인을 읽는 작은 조회 함수 함수들을 통해 진입한다.

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

| 키 | 런타임 해석 |
|---|---|
| `m_EffectFromBottomOffsetY` | 적 ID 경로를 통해 `EnemyID.m_TypeID @ +0x18`을 읽은 뒤 `EnemyTypeID +0x58`에서 읽는다. 조회 함수는 어느 쪽 조회라도 실패하면 `0.0`을 반환한다. |
| `m_AilmentInfoOffsetX` | 같은 `EnemyID -> EnemyTypeID` 경로를 통해 `EnemyTypeID +0x5C`에서 읽는다. 이는 단순히 복사된 필드가 아니라 직접 스칼라 소비자다. |
| `m_OrderTexUS`, `m_OrderTexVS`, `m_OrderScaleS`, `m_OrderTexUL`, `m_OrderTexVL`, `m_OrderScaleL` | `GetOrderIconParameter`는 행에서 여섯 개의 연속 부동소수점를 `float[35]`, `float[36]`, `float[37]`, `float[38]`, `float[39]`, `float[40]`으로 읽으며, 이는 각각 행 오프셋 `+0x8C`, `+0x90`, `+0x94`, `+0x98`, `+0x9C`, `+0xA0`에 해당한다. |
| `m_VoiceSymbol` | 사운드 서브시스템은 `EnemyID -> EnemyTypeID`를 해석하고 행 `+0x80`을 `USoundUtility::GetVoiceSymbol`에 전달하여 보이스 심볼을 얻는다. |


#### ■ 표현, 텍스처, 오디오, VFX 설명자로서의 `EnemyTypeID`

앞의 필드 표는 `EnemyTypeID` 행이 살아 있는 레코드임을 증명한다. 다음 코드 경로들은 중요한 고차 소비자다: 적 구성, Flipbook/NPC 폴백 선택, 순서 아이콘 덮어쓰기, 전투/필드 부착 효과, 보이스-심볼 해석, call-sound 재생, hit/offset 기하. 이 경로들은 해당 테이블이 단순한 텍스처 조회 테이블이 아니라, `EnemyID.m_TypeID`를 통해 반복적으로 도달하는 압축된 표현 설명자임을 보여 준다.

##### ◆ C-TYPE-CONSTRUCTION-01: 적 구성은 모든 `EnemyTypeID.m_AnimationSetIDs` 슬롯을 순회하고 격파 효과 상태를 초기화함

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

해석: `AQPBattleCharacterBase::SetupEnemy` 중 적 행은 이미 `v12`로 상주한다. 행 `v12 + 0x18`은 `EnemyID.m_TypeID`다. setup 경로는 `GetEnemyTypeFlipbookSetNum(TypeID)`에 애니메이션 세트 항목 수를 묻고, 각 인덱스마다 `SetupEnemyFlipbook`을 한 번 호출한다. 따라서 `m_AnimationSetIDs`는 단일 기본 텍스처 ID가 아니라 정렬된 표현 슬롯 배열이다. 같은 구성 패스는 `GetEnemyTypeDefeatEffectID(TypeID)`를 통해 `m_DefeatEffect`를 읽어 캐릭터 런타임 상태에 저장한다. 이 시점 이후 캐릭터는 DB 행을 다시 읽지 않고도 고유 격파 효과 카메라 경로를 재생할 수 있다.

##### ◆ C-TYPE-FLIPBOOK-FALLBACK-02: `SetupEnemyFlipbook`의 애니메이션 세트 슬롯 해석과 NPC 폴백

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

해석: Flipbook 경로는 먼저 `m_AnimationSetIDs[TextureIdx]`에서 `UEnemyFlipbookSet`을 로드하려고 시도한다. 실패하고 해당 행이 적 부품 ID가 아니라 여전히 일반 적 타입이면(`type < ParamInt`), `m_NpcCharaID[TextureIdx]`를 읽고 그 캐릭터 ID를 시각 소스로 사용한다. hit-box 오프셋과 hit-box 스케일은 기본적으로 보이는 몸체 슬롯인 텍스처 인덱스 `0`에 대해서만 적용된다. 이는 “NPC 전투에는 `EnemyBattleAnimSet`이 필요 없다”보다 강한 증거다. 코드가 스키마 관례가 아니라 명시적 폴백 계층을 증명하기 때문이다.

##### ◆ C-TYPE-FLIPBOOK-MATERIALIZATION-03: `StaticGetEnemyFlipbooks`는 `EnemyID -> TypeID`를 해석하고, `GetEnemyFlipBook`은 선택된 anim set의 모든 텍스처 ID를 구체화함

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

해석: 호출자가 `TypeID <= 0`을 넘기면 런타임은 타입 ID를 지연 해석한다. 일반 적 경로와 적 부품 경로는 `UGameParamsUtility::GetParamInt(0x1B66)`로 명확히 분리된다. 임계값 아래에서는 `TypeID`가 `EnemyTypeID`이고, 임계값 이상에서는 코드가 임계값을 빼고 이를 `EnemyParts` ID로 취급한다. anim-set 행이 발견되면 각 `m_EnemyTextureIDs` 요소는 리소스 카테고리 `2`를 통해 로드되고, 그 `EnemyTexID` 메타데이터가 액션 ID를 제공한다. 따라서 게임은 순수하게 위치만 보고 `m_EnemyTextureIDs[1]`을 “attack 0”으로 해석하지 않는다. 액션 바인딩은 `EnemyTexID` 액션 ID에 의해 데이터 주도적으로 이루어진다.

##### ◆ C-TYPE-ORDERICON-04: 덮어쓰기 순서 아이콘은 `m_OverrideOrderIconAnimSetID`를 사용하고, UV/스케일은 전용 행 부동소수점를 사용함

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

해석: `m_OverrideOrderIconAnimSetID`는 행 `+0x88`이다. 양수이면 엔진은 별도의 애니메이션 세트에서 idle/액션-ID-1 텍스처를 로드하고 첫 스프라이트를 `m_OverrideOrderIconSprite`로 저장한다. UV와 픽셀 스케일 값은 Flipbook에서 추론되지 않는다. `GetOrderIconParameter`는 행 `+0x8C`, `+0x90`, `+0x94`, `+0x98`, `+0x9C`, `+0xA0`을 각각 `m_OrderTexUS`, `m_OrderTexVS`, `m_OrderScaleS`, `m_OrderTexUL`, `m_OrderTexVL`, `m_OrderScaleL`로 읽는다.

##### ◆ C-TYPE-ATTACHEFFECT-05: 전투 부착 효과는 행 `+0xA8/+0xAC/+0xB0`을 사용하고, 필드 부착 효과는 행 `+0xB4/+0xB8/+0xBC`를 사용함

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

해석: 전투에서는 `+0xA8`의 `m_AttachEffect`가 `AttachEffect` 행을 선택하고, `+0xAC/+0xB0`의 `m_BattleAttachEffectOffsetX`와 `m_BattleAttachEffectOffsetY`가 `m_ScaleX/m_ScaleY` 적용 후 생성된 효과의 Y/Z 배치에 반영된다. 필드/심볼 적 쪽에서는 `+0xB4`의 `m_FieldAttachEffect`가 `AttachEffect` 행을 선택하고, `+0xB8/+0xBC`의 `m_FieldAttachEffectOffsetX/Y`가 심볼 오프셋 계산에 들어간다. 필드 경로는 절반 너비 측면 오프셋으로 `m_EffectOffsetX`(`+0x50`)도 읽으므로, 필드 부착 위치 지정은 전투 부착 위치 지정과 동일하지 않다.

##### ◆ C-TYPE-AUDIO-06: 사운드와 보이스 소비자는 `m_CallSE`, `m_VoiceSymbol`, `m_CueSheet`를 구분함

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

해석: 행 `+0x7C`의 `m_CallSE`는 `ASoundManager::ExecRequestPlaySeById(..., volume_rate=100)`로 직접 전달되는 사운드 효과 ID다. 행 `+0x80`의 `m_VoiceSymbol` 자체가 최종 문자열은 아니다. 이는 `USoundUtility::GetVoiceSymbol`에 전달되어 전투 보이스 코드가 사용하는 `FString`/UTF-16 보이스 심볼을 구체화한다. `m_CueSheet`는 `FEnemyTypeIDBase` 복사 생성자에서 `+0x84`로 복원되지만, 이번 패스에서는 그만큼 직접적인 적 타입 소비자가 복원되지 않았다. 따라서 가이드는 `m_CueSheet`를 미사용이 아니라 소비자 증거가 불완전한 살아 있는 구체화 필드로 분류해야 한다.

##### ◆ C-TYPE-OFFSETMAP-07: 복사 생성자와 직접 소비자로부터 복원된 압축 `EnemyTypeID` 행 레이아웃 맵

| 오프셋 | 키 | 런타임 증거 | 해석 |
|---:|---|---|---|
| `+0x10/+0x18` | `m_AnimationSetIDs` | `GetEnemyTypeFlipbookSet`가 배열을 복사하고 `TextureIdx`로 인덱싱한다. `SetupEnemy`는 `0..GetEnemyTypeFlipbookSetNum-1`을 순회한다. | 정렬된 시각 슬롯. 각 슬롯은 `EnemyBattleAnimSet.m_id`다. |
| `+0x20/+0x28` | `m_NpcCharaID` | `GetEnemyTypeCharaID(TypeID, TextureIdx)`가 배열을 범위 검사하고 `Data[TextureIdx]`를 반환한다. | Flipbook 세트가 로드되지 않았을 때 슬롯의 NPC/캐릭터 폴백. |
| `+0x30` | `m_ChangeColorID` | `GetEnemyTypeColorID`가 `EnemyTypeDB[48]`을 읽는다. | 타입 행을 통해 도달하는 색상 변경 ID. |
| `+0x34/+0x38` | `m_ScaleX`, `m_ScaleY` | `GetEnemyTypeScale`이 `+0x34/+0x38`의 부동소수점를 읽고, 부착 효과도 이 값들을 곱한다. | 시각/효과 스케일 기준. |
| `+0x3C/+0x40` | `m_HitPosX`, `m_HitPosY` | `GetEnemyTypeHitOffset`가 `+0x3C/+0x40`의 부동소수점를 읽는다. | 기본 Flipbook 설정 중 적용되는 기본 hit-box 오프셋. |
| `+0x44/+0x48` | `m_HitScaleX`, `m_HitScaleY` | `GetEnemyTypeHitScale`이 `+0x44/+0x48`의 부동소수점를 읽는다. | 기본 Flipbook 설정 중 적용되는 기본 hit-box 스케일. |
| `+0x4C` | `m_Size` | `GetEnemyTypeSize`가 `+0x4C`의 바이트를 반환한다. `GetEnemySize(EnemyID)`는 `EnemyID.m_TypeID`를 통해 여기에 도달한다. | 적 크기 클래스는 `EnemyID` 스칼라가 아니라 타입 속성이다. |
| `+0x50/+0x54/+0x58` | `m_EffectOffsetX`, `m_EffectOffsetY`, `m_EffectFromBottomOffsetY` | `GetEnemyEffectOffset`는 `+0x50/+0x54`를 읽고, `GetEnemyBottomEffectOffset`는 `+0x58`을 읽으며, 심볼 필드 부착 효과는 `+0x50`을 사용한다. | 효과 배치 레인. |
| `+0x5C/+0x60` | `m_AilmentInfoOffsetX`, `m_AilmentOffset` | `GetEnemyAilmentInfoOffsetX`가 `+0x5C`를 읽고, `GetAilmentEffectOffsetID`가 `+0x60`을 읽는다. | UI/상태 이상 효과 오프셋. |
| `+0x64/+0x68` | `m_DamageOffsetX`, `m_DamageOffsetY` | `GetEnemyDamageOffset`가 `+0x64/+0x68`을 읽는다. | 대미지 숫자 배치. |
| `+0x6C/+0x70` | `m_CursorOffsetX`, `m_CursorOffsetY` | `GetEnemyCursorOffset`가 `+0x6C/+0x70`을 읽는다. | 타깃 커서 배치. |
| `+0x74/+0x78` | `m_SupportOffsetY`, `m_SupportOffsetX` | 복사 생성자/구체화 증거. 이번 패스에서는 직접 소비자가 복원되지 않았다. | 살아 있는 필드. 소비자 증거는 불완전하다. |
| `+0x7C/+0x80/+0x84` | `m_CallSE`, `m_VoiceSymbol`, `m_CueSheet` | `PlayEnemyTypeCallSE`는 `m_CallSE`를 사용하고, `GetVoiceCharaSymbol`은 `m_VoiceSymbol`을 사용하며, `m_CueSheet`는 복사/구체화된다. | 사운드 효과와 보이스-심볼은 직접 소비자다. cuesheet 소비자는 아직 부분적이다. |
| `+0x88` | `m_OverrideOrderIconAnimSetID` | `LoadOverrideOrderIcon`가 `EnemyTypeDB[136]`을 읽고 `GetActionTexID(..., EBATTLE_ACTION_ID::IDLE)`를 호출한다. | 선택적 독립 행동 순서 아이콘 애니메이션 세트. |
| `+0x8C..+0xA0` | 행동 순서 UV/스케일 필드 | `GetOrderIconParameter`가 부동소수점 인덱스 `35..40`을 읽는다. | 명시적 행동 순서 아이콘 UV 및 스케일 레인. |
| `+0xA4..+0xA6` | `m_AddStartUV`, `m_AttachEffectInGodbeastProduction`, `m_KeepVisibleAttachEffectOnMotion` | 복사 생성자가 세 필드를 모두 증명한다. `IsVisibleAttachEffectOnMotion`은 `+0xA6`을 직접 읽는다. | `m_KeepVisibleAttachEffectOnMotion`에는 직접 소비자가 있다. 나머지 둘은 복원된 경로에서는 구체화 한정다. |
| `+0xA8/+0xAC/+0xB0` | 전투 부착 필드 | `SetEnemyAttachEffect`가 `+0xA8`의 ID와 `+0xAC/+0xB0`의 오프셋을 읽는다. | 전투 캐릭터 부착 효과. |
| `+0xB4/+0xB8/+0xBC` | 필드 부착 필드 | `ASymbolEnemy::UpdateAttachEffect`가 `+0xB4`의 ID와 `+0xB8/+0xBC`의 오프셋을 읽는다. | 심볼/필드 부착 효과. |
| `+0xC0/+0xC8` | `m_PartsID` | 파트 설정이 배열 count/데이터를 사용한다. 앞 절에서는 파트 해석기를 인용했다. | 적 파트 구성. |
| `+0xD0` | `m_LinkOwnerAnim` | 복사 생성자/구체화 증거. 파트 코드는 간접적인 문맥상 지원을 제공한다. | 살아 있는 플래그. 연결 파트를 작성할 때 정확한 소비자를 테스트해야 한다. |
| `+0xD4` | `m_DefeatEffect` | `SetupEnemy`가 `GetEnemyTypeDefeatEffectID`를 통해 읽고 런타임 패배 효과 상태를 저장한다. | 고유 패배 효과 ID. |
 
### ■ (**) `EnemyWeakID`
적에게 할당할 수 있는 여러 종류의 약점 프로필을 추적한다. `m_ResistWeapon`과 `m_ResistMagic`은 `Skill/` 아래 `ResistType`의 `m_id`를 입력으로 받는다. `ResistType` 1은 지정된 무기/마법 타입도 약점으로 추가한다. 지정된 무기 및 마법 타입의 순서는 아마도 `Skill/` 아래 `WeaponType`/`MagicType` 에셋의 `m_id` 순서를 따른다. 즉:
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
**참고:** OT0는 한 적에게 8개를 초과하는 약점을 표시할 수 없다.

`Kingship_structs.hpp`는 OT0의 일부 ResistType에 다음 라벨을 부여한다.

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

#### ■ 런타임 읽기 의미론
##### ◆ C-WEAK-01: EnemyWeakID 해석기 및 약점 ID 조회 함수

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

##### ◆ C-WEAK-02: 적 약점 행 선택 및 Resist 배열 레인 선택

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

##### ◆ C-WEAK-03: EnemyWeakID 배열용 USkillDB 레인 접근 함수

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

##### ◆ C-ENEMY-02: 구체적인 행 오프셋을 갖는 EnemyID 구체화 처리

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_ResistMagic` | `+0x20`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `isMagic=true`를 전달하여 마법 저항 벡터를 선택한다. `USkillDB::GetResistsByID`는 `&EnemyWeakDB[0x20]`, 즉 행 `+0x20`의 `TArray` 헤더를 반환한다. | 복원됨 | `C-WEAK-02`, `C-WEAK-03` |
| `m_ResistWeapon` | `C-WEAK-03:17974953` `return &EnemyWeakDB[16 * v2 + 16];` | `isMagic=false`를 전달하여 무기 저항 벡터를 선택한다. `USkillDB::GetResistsByID`는 `&EnemyWeakDB[0x10]`, 즉 행 `+0x10`의 `TArray` 헤더를 반환한다. | 복원됨 | `C-WEAK-02`, `C-WEAK-03` |
| `m_WeakID` | `+0x1C`(스칼라. 살아 있는 필드는 EnemyID에 속함). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 명시적인 약점-행 덮어쓰기가 주어지지 않으면 `UEnemyDB::GetEnemyResists`는 먼저 적 행 `+0x1C`에서 `EnemyID.m_WeakID`를 읽고, 그에 대응하는 `EnemyWeakID` 행을 해석한다. | 복원됨 | `C-WEAK-02`, `C-ENEMY-02` |
| `m_id` | `+0x08`(스칼라. 살아 있는 필드는 EnemyTypeID에 속함). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 키는 행 오프셋 `+0x08`의 32비트 스칼라로 읽힌다. `WeakID`는 `UEnemyDB::GetEnemyWeakDB`를 통해 해석되고, 행 스트라이드는 `0x30` 바이트다. | 복원됨 | `C-WEAK-01` |


### ■ (**) `EnemyWeakChangeID`
적이 약점을 어떻게 변경하는지 추적한다. `m_WeakIndices`는 `m_EnemyWeakID.m_id`를 입력으로 받으며, 이 약점 변경의 `m_id`는 예를 들어 avail에 할당할 수 있다([커스텀 스킬 및 avail을 추가하는 방법](customskls_en.md)도 참조).

`Kingship_structs.hpp`는 OT0의 ID에 아래 라벨을 부여한다. 이 enum은 런타임의 exhaustiveness 제약이 아니라 stock 행을 위한 SDK 편의 enum으로 이해하는 것이 가장 좋다. 런타임은 나열된 `EENEMY_WEAKCHANGE_ID` 상수들을 switch하지 않는다. 요청된 숫자 `ChangeID`를 `m_EnemyWeakChangeID.m_DBAccess`로 해석하며, 키가 행으로 해석되면 그 행의 `m_WeakIndices` 배열과 추첨 플래그를 읽는다. 따라서 DB 접근/인덱스 데이터가 일관되게 재빌드되고 호출 지점이 새 ID를 받는다면 이 테이블은 확장할 수 있다.
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
`EENEMY_WEAKCHANGE_MAX`를 엔진의 실제 상한으로 취급하지 말 것. C 구현은 `m_EnemyWeakChangeID.m_DBAccess`에 존재하는 임의의 양수 `ChangeID`를 받아들인다. enum은 단지 SDK에 컴파일된 명명 stock ID를 문서화할 뿐이다.
#### ■ 런타임 읽기 의미론
`EnemyWeakChangeID` 행은 40바이트 레코드로 복원된다. 살아 있는 행 reader는 행 `+0x10`의 배열 포인터를 반환하고 행 `+0x20`에서 추첨 불리언을 쓴다. 행 ID는 enum 라벨 switch가 아니라 `FDbAccessHelper`로 해석된다.

【주요 C 증거】

##### ◆ C-WCH-01: 키 조회, 40바이트 행 스트라이드, `m_WeakIndices`, 추첨 플래그

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

**해석.** `ChangeID`는 양의 정수 키다. 행 스트라이드는 40바이트다. `m_WeakIndices`는 `+0x10`의 `TArray<int>`이고, 추첨 플래그는 `+0x20`의 바이트다. 여기에는 enum 범위 검사가 없다.

##### ◆ C-WCH-02: 스킬/avails가 `m_WeakChangeID`를 제공함

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

**해석.** avail 하나가 약점 변경 ID를 운반한다. 관찰된 한 사용처는 `SetChangeWeak` 호출 전에 ID를 `unsigned __int8`로 캐스팅한다. 이는 호출자 측 narrowing artifact이며, 255보다 큰 ID를 도입한다면 테스트해야 한다. 이는 `int ChangeID`를 받고 DB 조회을 수행하는 테이블 조회 루틴과는 별개다.

##### ◆ C-WCH-03: 활성 약점 변경 ID 설정은 런타임 상태를 바꾸고 UI 셔플을 트리거함

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

**해석.** `SetChangeWeak`는 선택된 테이블 ID를 런타임 상태(`m_WeakChangeID`)에 저장하고, ID가 바뀌면 switch를 표시하며, 약점 변경 ID가 없는 상태에서 전환될 때 first-추첨 플래그를 초기화하고, 상태 UI의 약점 셔플 애니메이션을 시작한다.

##### ◆ C-WCH-04: 약점 변경 실행은 `m_WeakIndices`로부터 큐를 구성함

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

**해석.** `m_WeakIndices` 배열은 캐릭터의 `m_ChangeWeaks` 큐로 복사되며 두 가지 중요한 런타임 규칙을 갖는다. 첫째, 스캔은 양수가 아닌 약점 ID를 만나면 멈춘다. 둘째, 첫 추첨 전환 동안에는 적의 현재/기본 약점 ID가 후보 큐에서 제외된다.

##### ◆ C-WCH-05: 추첨 모드는 후보 큐에서 반복 무작위 선택을 추가함

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

**해석.** 행의 추첨 플래그가 true이고 후보가 둘보다 많으면, 런타임은 반복 무작위 선택으로 큐를 확장한다. 따라서 추첨 행은 결정적 cycle 행과 같은 `m_WeakIndices` 배열을 사용하더라도 다르게 동작한다.

##### ◆ C-WCH-06: 실행은 다음 약점 ID를 꺼내기하고 `m_NowWeakID`에 저장함

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

**해석.** 선택된 약점 프로필은 큐의 head다. 선택 후 큐에서 제거되어 `m_NowWeakID`에 저장된다. 반환값은 현재 약점 프로필이 변경되었는지를 나타낸다.

##### ◆ C-WCH-07: UI/인덱스 경로는 현재 약점 ID를 `m_WeakIndices`에서 검색함

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

**해석.** 런타임은 원래 `m_WeakIndices` 배열을 검색하여 현재 활성 약점 프로필의 ordinal을 복원할 수 있다. 현재 약점 ID가 없으면 함수는 `-1`을 반환한다.

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., ChangeID)` | `m_EnemyWeakChangeID`의 양의 정수 키. stock enum 라벨은 포괄적인 런타임 switch가 아니다. | 복원됨 | `C-WCH-01` |
| `m_WeakIndices` | 행 `+0x10`, `TArray<int>*`로 반환 | `EnemyWeakID.m_id` 값의 정렬된 약점 프로필 목록. `ChangeWeak`에서 양수가 아닌 항목은 스캔을 종료한다. | 복원됨 | `C-WCH-01`, `C-WCH-04`, `C-WCH-06`, `C-WCH-07` |
| 추첨 플래그 / 행 `+0x20` | `*isLottery = *(_BYTE *)(v6 + 32)` | 큐 구성을 직접적인 결정적 queueing에서 무작위 큐 확장으로 바꾸는 불리언. | 복원됨 | `C-WCH-01`, `C-WCH-05` |
| SDK enum entries | 인용된 런타임에는 enum 상수 switch가 없음 | stock ID에 대한 이름일 뿐이다. DB 확장은 `EENEMY_WEAKCHANGE_MAX`에 의해 막히지 않지만, `unsigned __int8` 호출 지점을 통해 전달되는 ID는 해당 호출 지점이 패치/테스트되지 않는 한 바이트 범위 안에 있어야 한다. | 복원됨 / caveat 있음 | `C-WCH-01`, `C-WCH-02` |

### ■ (**) `EnemyWeakLockID`
적이 약점을 어떻게 잠그는지 추적한다. 여기에는 비속성 마법 대미지와 독 대미지를 별개의 특수 레인으로 잠근다는 증거가 없다. 잠금 테스트는 단순히 저장된 무기 및 마법 배열의 길이와 전달된 무기/마법 타입 인덱스에 의해 제한된다.

약점 잠금은 보통 상태 이상/avail이 운반한다. 전투 캐릭터는 활성 상태 이상 슬롯에서 상태 이상 ID `37`을 확인하고, originating avail ID를 읽고, `SkillAvailID.m_WeakLockID`를 얻은 다음, 주어진 무기 또는 마법 약점이 잠겼는지 `EnemyWeakLockID`에 묻는다. UI 코드는 별도로 같은 무기/마법 벡터를 가져와 잠금 표시 상태를 시작하거나 끝낸다.

#### ■ 런타임 읽기 의미론
`EnemyWeakLockID` 행은 80바이트 레코드로 복원된다. 행 ID는 `m_EnemyWeakLockID.m_DBAccess`로 해석된다. 오프셋 `+0x10`과 `+0x20`은 두 불리언 잠금 벡터이고, 오프셋 `+0x30`과 `+0x40`은 약점 잠금 상태 이상를 제거해야 하는지를 결정할 때 사용되는 조건/매개변수 벡터다.

【주요 C 증거】

##### ◆ C-WLOCK-01: 키 조회 및 80바이트 행 스트라이드

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

**해석.** `LockID`는 DB 키이며 enum switch case가 아니다. 행 스트라이드는 80바이트다.

##### ◆ C-WLOCK-02: 무기/마법 잠금 벡터는 `isMagic`으로 선택됨

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

**해석.** `isMagic=false`는 행 `+0x10`의 `TArray<bool>` 헤더를 반환하고, `isMagic=true`는 행 `+0x20`의 `TArray<bool>` 헤더를 반환한다. 이것이 테이블에 별도의 무기 및 마법 잠금 배열이 있는 정확한 이유다.

##### ◆ C-WLOCK-03: 직접 잠금 판정식는 선택된 벡터를 범위 검사함

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

**해석.** 무기 잠금은 `+0x10/+0x18`의 벡터 포인터/count를 사용하고, 마법 잠금은 `+0x20/+0x28`을 사용한다. `WeaponType`이 0이 아니면 그것이 우선하며, 그렇지 않으면 0이 아닌 `MagicType`을 검사한다. 두 분기 모두 0이 아닌 타입 인수를 요구하므로 타입 인덱스 `0`은 이 판정식에서 검사되지 않는다. 이는 일부 특수 약점 레인이 일반 무기/마법 아이콘과 같은 방식으로 표현되지 않는다는 가이드 경고와 일치한다.

##### ◆ C-WLOCK-04: 제거 조건 배열은 행 `+0x30` 및 `+0x40`

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

**해석.** 행이 16바이트 `TArray<int>` 헤더의 연속으로 보이므로, `EnemyWeakLockDB + 3`은 행 `+0x30`이고 `EnemyWeakLockDB + 4`는 행 `+0x40`이다. 이들은 약점 잠금 전용 제거 조건 및 매개변수 배열이다.

##### ◆ C-WLOCK-05: 약점 잠금 제거 조건은 상태 이상 ID `37`에 대해 상태 이상 기본값을 덮어쓰기함

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

**해석.** 약점 잠금 상태 이상 제거는 `SkillAilmentDB` 기본 제거 조건에만 의존하지 않는다. 상태 이상 ID가 `37`이면 avail의 `m_WeakLockID`가 조건/매개변수 배열을 `EnemyWeakLockID` 행으로 redirect할 수 있다.

##### ◆ C-WLOCK-06: 활성 상태 이상 경로는 `SkillAvailID.m_WeakLockID`를 얻음

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

**해석.** 잠금 ID의 런타임 출처는 활성 약점 잠금 상태 이상를 만든 스킬 avail이다. 약점 변경 ID와 마찬가지로 반환 타입은 enum 이름이지만, 값은 DB 조회에 데이터로 전달된다. 인용된 코드는 enumerator switch를 구현하지 않는다.

##### ◆ C-WLOCK-07: UI 잠금 상태가 같은 무기/마법 벡터를 읽음

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

**해석.** UI 표시는 별도 테이블이 아니다. `GetLockedWeakFlags`가 반환한 동일한 두 잠금 벡터를 받으므로, 무기/마법 배열을 변경하면 논리적 잠금 검사와 표시되는 잠금 상태가 모두 달라진다.

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., LockID)`, 행 스트라이드 `80` | `m_EnemyWeakLockID`의 양의 정수 키. stock enum 라벨은 포괄적인 런타임 switch가 아니다. | 복원됨 | `C-WLOCK-01` |
| 무기 잠금 배열 | 행 `+0x10/+0x18` | `WeaponType != 0`일 때 테스트되는 `TArray<bool>`. UI 잠금 표시에도 무기 벡터로 전달된다. | 복원됨 | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| 마법 잠금 배열 | 행 `+0x20/+0x28` | `WeaponType == 0 && MagicType != 0`일 때 테스트되는 `TArray<bool>`. UI 잠금 표시에도 마법 벡터로 전달된다. | 복원됨 | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| 제거 조건 | 행 `+0x30` | 약점 잠금 상태 이상 제거에 사용되는 `TArray<int>` 조건 ID. | 복원됨 | `C-WLOCK-04`, `C-WLOCK-05` |
| 제거 매개변수 | 행 `+0x40` | 제거 조건와 쌍을 이루는 `TArray<int>` 매개변수. | 복원됨 | `C-WLOCK-04`, `C-WLOCK-05` |
| non-elemental / poison locking | `IsLockedWeak`에 특수 분기 없음 | 인용된 판정식는 저장된 두 벡터 안에서 0이 아닌 무기 또는 마법 타입 인덱스만 검사한다. 여기서는 별도의 특수 case 레인이 복원되지 않았다. | 부정적/부분 복원 | `C-WLOCK-03` |

### ■ (****) `EnemyID`
게임의 적 마스터 레코드다. stats/weaknesses/보상/UI 플래그를 정의하고, 표현(애니메이션 세트, 텍스처, 오프셋, 보이스, 효과)을 위한 `EnemyTypeID`, 그리고 적 행동과 스킬 세트를 위한 `TacticalAssignList` 및 `TacticalSkillList`를 가리킨다(아래 “`AIBattle` 아래” 참조).
- `m_Label`: 적의 label. 내부적으로 사용될 수 있으며, 충돌하는 label을 가진 새 엔트리는 추가하지 않는 것이 좋다.
- `m_DisplayName`: ID는 `GameTextEnemy.m_id`를 참조하며, 모든 로컬라이즈에 공통이다.
- `m_WeakID`: ID는 `EnemyWeakID.m_id`를 참조한다. 위 참조.
- `m_DisplayLevel`: 적의 표시 레벨. 안타깝게도 `m_LevelUI`를 true로 설정해도 표시되지 않는다.
- `m_BreakRate`: 적이 브레이크 상태일 때 적에게 주는 대미지에 곱해지는 백분율 값.
- `m_DamageRate`: 적이 브레이크 상태가 아닐 때 적에게 주는 대미지에 곱해지는 백분율 값.
- `m_MaxSLD`: 적의 실드 수. 키 이름이 최대값을 암시하더라도 일부 택틱스와 스킬는 이를 더 높은 값으로 설정할 수 있다(예: Lucian the Glorious).
- `m_MaxHP`: 적의 HP. 위와 마찬가지로 스킬와 택틱스가 이를 증가시킬 수 있다.
- `m_MaxSP`: 적의 SP. 대부분의 적 스킬은 0 SP를 사용하므로, 이 능력치은 주로 특정 적 스킬의 사용 횟수를 제한하는 데 쓰인다.
- `m_AtkP`: 적의 물리 공격.
- `m_AtkM`: 적의 속성 공격.
- `m_DefP`: 적의 물리 방어.
- `m_DefM`: 적의 속성 방어.
- `m_Agi`: 적의 속도/민첩성.
- `m_Crt`: 적의 크리티컬 능력치.
- `m_CrtDef`: 적의 크리티컬 방어. 이 능력치은 적에게만 있는 것으로 보인다.
- `m_Hit`: 적의 명중 능력치.
- `m_Avd`: 적의 회피 능력치.
- `m_ResistAilmentID`: 상태 이상 저항을 추적하는 ID를 적에게 할당한다(`Skill/` 아래 `SkillResistAilmentID` 참조).
- `m_ResistAilment`: 특정 상태 이상에 대한 저항률에 대응하는 배열. 할당된 `ResistAilmentID`의 대응 `m_ResistAilments` 배열이 가득 찼을 때 사용될 가능성이 있다. Cait만 사용하는 것으로 보인다.
- `m_TacticalAssignID`: 전투에서 사용하는 택틱스 목록에 대응하는 `TacticalAssignList.m_id`를 적에게 할당한다. 아래 “`AIBattle` 아래” 참조.
- `m_SkillsID`: 전투에서 사용하는 스킬 목록에 대응하는 `TacticalSkllList.m_id`를 적에게 할당한다. 아래 “`AIBattle` 아래” 참조.
- `m_IsBoss`: 많은 보스가 사용하지만 마을 NPC도 사용하는 플래그. 기능적으로 무엇을 하는지는 불확실하다.
- `m_Exp`, `m_Money`, `m_JP`: 이 적을 쓰러뜨린 후 얻는 XP, Money 또는 JP.
- `m_PetExp`: CotC와 OT0 모두에서 항상 -1로 설정된다. -1이 아닌 값은 효과가 없어 보인다.
- `m_DropReward`: 적이 드롭할 수 있는 아이템. (사용 가능한 ID는 `EnemyDropID` 참조)
- `m_StealReward`: 적에게서 훔칠 수 있는 아이템(사용 가능한 ID는 `Item/` 아래 `ItemList` 참조).
- `m_StealLeafNum`: 적에게서 훔칠 수 있는 리프 수.
- `m_StatusOffset`: 적 상태 바(약점과 실드 수)의 오프셋.
- `m_Bottle`: 의미 불명. OT0는 1-3 값을 사용한다. 값 3은 OT0에서 Wappa만 사용한다. CotC도 4와 5를 사용한다.
- `m_NameUI`: 적 이름이 UI에 표시되는지 여부.
- `m_LevelUI`: 적의 `m_DisplayLevel`이 UI에 표시되는지 여부(미사용/비기능).
- `m_HpUI`: OT0에서는 기능하지 않는다.
- `m_ShieldUI`: true로 설정하면 적 실드 아이콘을 어둡게 만드는 것으로 보인다.
- `m_RaceIndices`: 적에게 할당된 종족. 대미지 계산에 영향을 줄 수 있다.
- `m_DisplaySpecialSkillGauge`: 적의 BP 카운터를 표시하는 플래그.
- `m_GenerateSpecialSkillValueOrverTurn`: 적이 턴마다 생성하는 BP 포인트 수.
- `m_ReduceSpecialSkillValueBreak`: 브레이크 후 적이 잃는 BP 포인트 수.
- `m_MaxSpecialSkillValue`: 적이 가진 BP 포인트의 최대 수. 20보다 큰 값은 게임을 크래시시킨다.
- `m_OutsideTarget`: 적을 타깃할 수 없으며, 그것이 필드의 유일한 적이면 파티 멤버도 행동을 건너뛴다(Bestower of All은 반지가 부서질 때까지 이것을 사용한다).
- `m_NotAction`: true이면 적이 행동할 수 없게 한다(Bestower of All이 모든 반지가 격파되기 전 사용).
- `m_UnknownLv`: 적 레벨을 “??”로 표시한다. 적 레벨 UI가 없어 미사용이고 시각적으로 기능하지 않지만, 이를 proven-unused padding으로 취급하지 말 것. OT0 decomp의 명명된 `FEnemyIDBase` 복사/구체화 처리 경로는 이를 실제 late 플래그로 복사한다(`Octopath_Traveler0-Win64-Shipping.exe.c`:17041778-17041806 및 17647345-17647360).
- `m_Immortal`: true로 설정하면 적의 HP가 절대로 1 아래로 내려가지 않는다.

#### ■ 런타임 읽기 의미론(필드 단위 Hex-Rays 증거)
##### ◆ C-ENEMY-01: EnemyID 행 해석기

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

##### ◆ C-ENEMY-02: 구체적인 행 오프셋을 갖는 EnemyID 구체화 처리

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

##### ◆ C-ENEMY-03: EnemyID late-상태 브리지 into 전투 character 플래그

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

##### ◆ C-ENEMY-04: OutsideTarget, NotAction, Immortal의 런타임 mutators/접근 함수

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

##### ◆ C-ENEMY-05: NotAction 및 OutsideTarget용 런타임 조회 함수와 Immortal setter

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

##### ◆ C-ENEMY-06: RaceIndices 소속 관계 test

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_Agi` | `C-ENEMY-02:17647319` `cEnemyListBase.m_Agi = *(_DWORD *)(v12 + 80);` | 키는 행 오프셋 `+0x50`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkM` | `C-ENEMY-02:17647317` `cEnemyListBase.m_AtkM = *(_DWORD *)(v12 + 72);` | 키는 행 오프셋 `+0x48`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkP` | `C-ENEMY-02:17647315` `cEnemyListBase.m_AtkP = *(_DWORD *)(v12 + 64);` | 키는 행 오프셋 `+0x40`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Avd` | `C-ENEMY-02:17647323` `cEnemyListBase.m_Avd = *(_DWORD *)(v12 + 96);` | 키는 행 오프셋 `+0x60`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Bottle` | `C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | 키는 행 오프셋 `+0xC8`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_BreakRate` | `C-ENEMY-02:17647311` `cEnemyListBase.m_BreakRate = *(_DWORD *)(v12 + 48);` | 키는 행 오프셋 `+0x30`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | 이는 `EnemyID` 행 필드가 아니다. `EnemyID` 절에서 언급될 때는 `EnemyID.m_TypeID -> EnemyTypeID +0x7C`를 통해 도달한다. | 복원됨 | `C-TYPE-02`, `C-TYPE-03` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | 이는 `EnemyID` 행 필드가 아니다. `GetEnemyTypeColorID`는 `EnemyID.m_TypeID`를 해석한 뒤 `EnemyTypeID +0x30`의 타입 행을 읽는다. | 복원됨 | `C-TYPE-01`, `C-TYPE-02` |
| `m_Crt` | `C-ENEMY-02:17647320` `cEnemyListBase.m_Crt = *(_DWORD *)(v12 + 84);`<br>`C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | 키는 행 오프셋 `+0x54`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CrtDef` | `C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | 키는 행 오프셋 `+0x58`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DamageRate` | `C-ENEMY-02:17647324` `cEnemyListBase.m_DamageRate = *(_DWORD *)(v12 + 100);` | 키는 행 오프셋 `+0x64`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefM` | `C-ENEMY-02:17647318` `cEnemyListBase.m_DefM = *(_DWORD *)(v12 + 76);` | 키는 행 오프셋 `+0x4C`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefP` | `C-ENEMY-02:17647316` `cEnemyListBase.m_DefP = *(_DWORD *)(v12 + 68);` | 키는 행 오프셋 `+0x44`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayLevel` | `C-ENEMY-02:17647291` `memset(&cEnemyListBase.m_DisplayLevel, 0, 76);`<br>`C-ENEMY-02:17647309` `(TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_DisplayLevel,`<br>`C-ENEMY-02:17647361` `if ( cEnemyListBase.m_DisplayLevel.Data.ArrayNum )` plus 2 further local occurrences | 키는 행 오프셋 `+0x20`의 `FString`/UTF-16 배열 헤더로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayName` | `C-ENEMY-02:17647281` `FEnemyIDBase cEnemyListBase; // [rsp+20h] [rbp-E0h] BYREF`<br>`C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | 키는 행 오프셋 `+0x14`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplaySpecialSkillGauge` | `C-ENEMY-02:17647353` `cEnemyListBase.m_DisplaySpecialSkillGauge = *(_BYTE *)(v12 + 224);` | 키는 행 오프셋 `+0xE0`의 바이트/불리언 레인으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DropReward` | `C-ENEMY-02:17647336` `cEnemyListBase.m_DropReward = *(_DWORD *)(v12 + 156);` | 키는 행 오프셋 `+0x9C`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_EventDropRewards` | `C-ENEMY-02:17647340` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_EventDropRewards,` | 키는 행 오프셋 `+0xA8`의 `TArray` 헤더로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Exp` | `C-ENEMY-02:17647293` `memset(&cEnemyListBase.m_Exp, 0, 85);`<br>`C-ENEMY-02:17647332` `cEnemyListBase.m_Exp = *(_DWORD *)(v12 + 140);` | 키는 행 오프셋 `+0x8C`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_GenerateSpecialSkillValueOrverTurn` | `C-ENEMY-02:17647294` `*(_QWORD *)&cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = 0;`<br>`C-ENEMY-02:17647354` `cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = *(_DWORD *)(v12 + 228);` | 키는 행 오프셋 `+0xE4`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Hit` | `C-ENEMY-02:17647322` `cEnemyListBase.m_Hit = *(_DWORD *)(v12 + 92);` | 키는 행 오프셋 `+0x5C`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_HpUI` | `C-ENEMY-02:17647348` `cEnemyListBase.m_HpUI = *(_BYTE *)(v12 + 206);` | 키는 행 오프셋 `+0xCE`의 바이트/불리언 레인으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Immortal` | `C-ENEMY-02:17647360` `cEnemyListBase.m_Immortal = *(_BYTE *)(v12 + 243);` | `EnemyID +0xF3`의 바이트는 전투 캐릭터 설정 중 `SetImmortal`을 통해 전달된다. 설정 후 런타임 코드는 DB 행을 다시 읽지 않고 캐릭터 객체의 `m_IsImmortal`을 읽고 쓴다. | 복원됨 | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-05` |
| `m_IsBoss` | `C-ENEMY-02:17647331` `cEnemyListBase.m_IsBoss = *(_BYTE *)(v12 + 136);` | 키는 행 오프셋 `+0x88`의 바이트/불리언 레인으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_JP` | `C-ENEMY-02:17647335` `cEnemyListBase.m_JP = *(_DWORD *)(v12 + 152);` | 키는 행 오프셋 `+0x98`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Label` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | 키는 행 오프셋 `+0x0C`의 `FName`으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_LevelUI` | `C-ENEMY-02:17647347` `cEnemyListBase.m_LevelUI = *(_BYTE *)(v12 + 205);` | 키는 행 오프셋 `+0xCD`의 바이트/불리언 레인으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxHP` | `C-ENEMY-02:17647313` `cEnemyListBase.m_MaxHP = *(_DWORD *)(v12 + 56);` | 키는 행 오프셋 `+0x38`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSLD` | `C-ENEMY-02:17647312` `cEnemyListBase.m_MaxSLD = *(_DWORD *)(v12 + 52);` | 키는 행 오프셋 `+0x34`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSP` | `C-ENEMY-02:17647314` `cEnemyListBase.m_MaxSP = *(_DWORD *)(v12 + 60);` | 키는 행 오프셋 `+0x3C`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSpecialSkillValue` | `C-ENEMY-02:17647295` `*(_QWORD *)&cEnemyListBase.m_MaxSpecialSkillValue = 0;`<br>`C-ENEMY-02:17647356` `cEnemyListBase.m_MaxSpecialSkillValue = *(_DWORD *)(v12 + 236);` | 키는 행 오프셋 `+0xEC`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Money` | `C-ENEMY-02:17647333` `cEnemyListBase.m_Money = *(_DWORD *)(v12 + 144);` | 키는 행 오프셋 `+0x90`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NameUI` | `C-ENEMY-02:17647346` `cEnemyListBase.m_NameUI = *(_BYTE *)(v12 + 204);` | 키는 행 오프셋 `+0xCC`의 바이트/불리언 레인으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NotAction` | `C-ENEMY-02:17647358` `cEnemyListBase.m_NotAction = *(_BYTE *)(v12 + 241);` | `EnemyID +0xF1`의 바이트는 `AQPBattleCharacterBase::m_IsNotAction`을 초기화한다. 전투-이벤트 커맨드는 `DisableNotAction`으로 이를 지울 수 있으므로, 행 값은 초기 no-액션 상태다. 이 C 내보내기에서 활성 플래그는 불리언 타입이다. | 복원됨 | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_OutsideTarget` | `C-ENEMY-02:17647357` `cEnemyListBase.m_OutsideTarget = *(_BYTE *)(v12 + 240);` | `EnemyID +0xF0`의 바이트는 `AQPBattleCharacterBase::m_IsOutsideTarget`을 초기화한다. 이후 타기팅 코드는 그 런타임 플래그를 조회하거나 지우므로 DB 필드는 영구 불변 속성이 아니라 초기 상태 시드다. | 복원됨 | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_PetExp` | `C-ENEMY-02:17647334` `cEnemyListBase.m_PetExp = *(_DWORD *)(v12 + 148);` | 키는 행 오프셋 `+0x94`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_RaceIndices` | `C-ENEMY-02:17647351` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_RaceIndices,`<br>`C-ENEMY-02:17647368` `Data = cEnemyListBase.m_RaceIndices.AllocatorInstance.Data;` | `m_RaceIndices`는 `EnemyID +0xD0`의 `TArray<int>`다. 런타임 소속 관계은 스칼라 equality가 아니다. `HasEnemyRace`는 배열을 순회하며 요청한 종족 ID와 같은 요소가 있을 때만 성공한다. | 복원됨 | `C-ENEMY-02`, `C-ENEMY-06` |
| `m_ReduceSpecialSkillValueBreak` | `C-ENEMY-02:17647355` `cEnemyListBase.m_ReduceSpecialSkillValueBreak = *(_DWORD *)(v12 + 232);` | 키는 행 오프셋 `+0xE8`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilment` | `C-ENEMY-02:17647292` `memset(&cEnemyListBase.m_ResistAilment, 0, 25);`<br>`C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);`<br>`C-ENEMY-02:17647327` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_ResistAilment,` | 키는 행 오프셋 `+0x70`의 `TArray` 헤더로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilmentID` | `C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);` | 키는 행 오프셋 `+0x68`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilments` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | C 내보내기에서 정확한 `m_ResistAilments` 심볼이나 명확한 애셋별 offset은 복원되지 않았다. 따라서 이 필드는 추론된 것이 아니라 unrecovered로 문서화한다. | 부정적/부분 복원 | not 복원됨 |
| `m_ShieldUI` | `C-ENEMY-02:17647349` `cEnemyListBase.m_ShieldUI = *(_BYTE *)(v12 + 207);` | 키는 행 오프셋 `+0xCF`의 바이트/불리언 레인으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | 키는 행 오프셋 `+0x84`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StatusOffset` | `C-ENEMY-02:17647343` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_StatusOffset,`<br>`C-ENEMY-02:17647369` `v8 = cEnemyListBase.m_StatusOffset.AllocatorInstance.Data;` | 키는 행 오프셋 `+0xB8`의 `TArray` 헤더로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealLeafNum` | `C-ENEMY-02:17647338` `cEnemyListBase.m_StealLeafNum = *(_DWORD *)(v12 + 164);` | 키는 행 오프셋 `+0xA4`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealReward` | `C-ENEMY-02:17647337` `cEnemyListBase.m_StealReward = *(_DWORD *)(v12 + 160);` | 키는 행 오프셋 `+0xA0`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_TacticalAssignID` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | 키는 행 오프셋 `+0x80`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_UnknownLv` | `C-ENEMY-02:17647359` `cEnemyListBase.m_UnknownLv = *(_BYTE *)(v12 + 242);` | 키는 행 오프셋 `+0xF2`의 바이트/불리언 레인으로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | 이는 `EnemyID` 행 필드가 아니다. 런타임은 `EnemyID.m_TypeID`를 해석한 다음 `EnemyTypeID +0x80`에서 `m_VoiceSymbol`을 읽는다. | 복원됨 | `C-TYPE-02`, `C-TYPE-03` |
| `m_WeakID` | `+0x1C`(스칼라). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 키는 행 오프셋 `+0x1C`의 32비트 스칼라로 읽힌다. `UEnemyDB::GetEnemyDB`는 행 스트라이드 `0xF8`로 행을 해석하고, `ASymbolEnemy::GetTargetLevel`은 이 오프셋들을 명시적으로 구체화한다. | 복원됨 | `C-ENEMY-01`, `C-ENEMY-02` |

### ■ 추가 런타임 증거: 직접 `EnemyID` 접근 함수, 상태 타입 매핑, 역방향 인덱스

`EnemyID` 테이블은 전체 행으로 복사되기만 하는 것이 아니다. 전투와 UI 코드의 상당 부분은 작은 접근 함수를 통해 개별 필드를 읽는다. 이 접근 함수는 행 오프셋을 독립적으로 검증하는 데 유용하며, 어떤 필드가 스칼라 값, 배열, 또는 보조 테이블 ID로 조회되는지도 보여준다.

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

| 키 또는 키 그룹 | 런타임 해석 |
|---|---|
| `m_DisplayName` | 행 `+0x14`로 읽힌다. `GetEnemyNameString`은 이를 `AGameTextManager::GetGameText(..., 4u, DisplayName, 0)`에 전달한다. 따라서 game 텍스트 namespace/category는 이 경로에서 `4u`로 hard-code되어 있다. |
| `m_TypeID` | 행 `+0x18`로 읽힌다. `withID` 디버그/display 분기에서는 접두부로 사용할 양의 구체적 `EnemyID`가 선호되지 않을 때만 쓰인다. gameplay에서는 `EnemyTypeID`로 이어지는 브리지이기도 하다. |
| `m_MaxHP`, `m_MaxSP`, `m_MaxSLD`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_Hit`, `m_Avd` | `GetEnemyStatus`는 상태 ID를 행 `uint32` 슬롯으로 매핑한다. 관찰된 매핑은 `1 -> m_MaxHP`, `2 -> m_MaxSP`, `4 -> m_MaxSLD`, `9 -> m_AtkP`, `10 -> m_DefP`, `11 -> m_AtkM`, `12 -> m_DefM`, `13 -> m_Agi`, `14 -> m_Crt`, `15 -> m_Hit`, `16 -> m_Avd`다. `m_CrtDef`는 행에 존재하지만 이 특정 상태 타입 switch로는 도달하지 않는다. |
| `m_StealReward` | 행 `+0xA0`으로 읽힌다. 불리언 overload는 복사된 ID가 양수일 때만 success를 반환한다. |
| `m_StealLeafNum` | 행 `+0xA4`로 읽힌다. 불리언 overload는 복사된 leaf count가 양수일 때만 success를 반환한다. |
| `m_TacticalAssignID` | 행 `+0x80`에서 직접 읽힌다. 이는 적-to-tactic 대입 ID다. |
| `m_SkillsID` | 행 `+0x84`에서 직접 읽힌다. `HasTacticalSkill`은 이 ID를 `UTacticalDB::GetTacticalSkillIndices`로 해석하고, 반환된 스킬 배열의 구체적인 `SkillID` 값과 비교한다. |

C 내보내기에는 타입 및 종족 소속 관계을 위한 역방향-인덱스 helpers도 포함되어 있다. 이는 `m_TypeID`와 `m_RaceIndices`가 단일 적 인스턴스뿐 아니라 전역 조회/캐시 구조에서도 사용됨을 보여주므로 modding에 중요하다.

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

| 키 | 런타임 해석 |
|---|---|
| `m_RaceIndices` | `+0xD0/+0xD8`의 행 소유 배열로 저장된다. `HasEnemyRace`는 배열 소속 관계 test이고, `GetSameRaceEnemies`는 이 필드를 스캔하여 적 ID 결과 배열을 구성한다. 따라서 이는 스칼라 종족 enum이 아니다. |
| `m_TypeID` | `GetSameTypeEnemies`는 모든 `EnemyID` 행을 스캔하고 행 `+0x18`로 적을 그룹화한다. 이는 타입 ID가 표현 포인터일 뿐 아니라 전역 classification 키임을 증명한다. |


#### ■ 전투 캐릭터 시드 레코드로서의 `EnemyID`

앞의 필드 표는 행 레이아웃을 확립한다. 다음 추가 경로는 modding에서 중요한 상위 런타임 의미론이다: 적 생성, UI 표시, 능력치 조회, 레벨/이름 localization, 약점-플래그 initialization, 드롭/steals, 종족/타입 역방향 스캔, special-게이지 변동, initial-상태 플래그. 이 경로들에서 게임은 `EnemyID`를 `AQPBattleCharacterBase` 인스턴스를 초기화하는 시드로 취급한다. 여러 필드는 런타임 상태에 한 번 복사된 뒤 독립적으로 변경된다.

##### ◆ C-ENEMY-CONSTRUCTION-01: 적 생성이 표현, 약점 플래그, 매개변수, immortal/outside/no-액션 상태를 시드함

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

해석: `SetupEnemy`는 `UEnemyDB::GetEnemyDB`를 통해 `EnemyID`를 한 번 읽는다. `v12`는 해석된 행이다. 그다음 행은 지속 전투 캐릭터 필드를 시드하는 데 사용된다. `m_TypeID`는 표현 setup을 구동한다. 여기서 `m_WeakID`는 직접 복사되지 않는다. 대신 약점 플래그는 각 무기/마법 인덱스에 대해 `AQPBattleManager::GetEnemyWeakFlags`를 조회하여 계산되며, 이는 결국 약점 테이블을 해석한다. `m_Immortal`, `m_OutsideTarget`, `m_NotAction`은 각각 `+0xF3`, `+0xF0`, `+0xF1`의 initial-상태 시드다. 시드 후 활성 character 필드는 스킬/events에 의해 독립적으로 수정될 수 있다.

##### ◆ C-ENEMY-STATE-RESET-02: `SetupParams`는 적 행 플래그가 적용되기 전에 활성 상태를 reset함

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

해석: `SetupParams`는 `SetupEnemy`가 DB-seeded 값을 쓰기 전에 의도적으로 `m_IsOutsideTarget`, 인접 no-액션 바이트, immortality를 지운다. 이 순서가 중요하다. DB 필드는 불변 판정식를 나타내는 것이 아니라 generic reset 뒤 캐릭터의 시작 전투 상태를 초기화한다. 후속 코드는 데이터베이스 행을 변경하지 않고 같은 런타임 플래그를 지우거나 설정할 수 있다.

##### ◆ C-ENEMY-DISPLAY-TEXT-03: 표시 이름과 레벨 문자열은 별도 메커니즘임

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

해석: `m_DisplayName`은 행 `+0x14`이고 `GameText` category `4`를 통해 해석되므로 raw 문자열이 아니라 localization ID다. `m_DisplayLevel`은 행 `+0x20`이며 `_wtoi`로 파싱되는 `FString`/UTF-16 배열이다. 이는 행 구체화 처리가 텍스트를 보존하더라도 숫자가 아닌 display-레벨 문자열이 character-레벨 소비자에서 왜 `0`이 되는지 설명한다.

##### ◆ C-ENEMY-STATUS-SWITCH-04: 능력치 조회은 순차 노출 배열이 아니라 상태-ID switch임

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

해석: 주요 스칼라 능력치 접근 함수는 `ECHARA_STATUS`류 ID를 행 dword로 매핑한다. 매핑은 `1 -> m_MaxHP`, `2 -> m_MaxSP`, `4 -> m_MaxSLD`, `9 -> m_AtkP`, `10 -> m_DefP`, `11 -> m_AtkM`, `12 -> m_DefM`, `13 -> m_Agi`, `14 -> m_Crt`, `15 -> m_Hit`, `16 -> m_Avd`다. `m_CrtDef`는 `+0x58`에 존재하고 복사/구체화되지만, 이 특정 접근 함수는 이를 건너뛴다. 따라서 물리적으로 인접해 있다는 이유만으로 모든 능력치 필드가 `GetEnemyStatus`를 통해 주소 지정 가능하다고 추론하지 말 것.

##### ◆ C-ENEMY-UI-FLAGS-05: UI 플래그는 개별적으로 읽히며, 실드 표시는 활성 shield 값도 필요함

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

해석: `m_HpUI`, `m_LevelUI`, `m_ShieldUI`, `m_DisplaySpecialSkillGauge`는 각각 `+0xCE`, `+0xCD`, `+0xCF`, `+0xE0`에서 별도 행 read를 갖는다. 행이 없으면 HP/레벨/shield 함수는 visible(`1`)을 기본값으로 삼지만, special-게이지 표시는 false가 기본값이다. 실드 표시는 추가로 `HasSLD(this)`에 의해 게이트되므로, 활성 shield 상태가 없을 때는 `m_ShieldUI=true`만으로 충분하지 않다.

##### ◆ C-ENEMY-SPECIAL-GAUGE-06: 적 special-게이지 필드는 setup 상태에 단순 복사되는 것이 아니라 변동 시점에 읽힘

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

해석: `m_MaxSpecialSkillValue`, `m_ReduceSpecialSkillValueBreak`, `m_GenerateSpecialSkillValueOrverTurn`은 활성 런타임 매개변수다. 이들은 생성된 적에게 복사되기만 하는 값이 아니다. 게이지 변동 routines는 추가, 브레이크 시 감소, 턴 경과 회복 시 `EnemyID` 행을 다시 조회한다. 이는 이러한 DB 값을 바꾸면 해당 조회 함수를 호출하는 formula paths에 영향을 주지만, 현재 활성 게이지 값는 별도의 캐릭터/저장 상태로 남아 있음을 의미한다.

##### ◆ C-ENEMY-REWARD-DROP-STEAL-07: 보상, 드롭, steals는 positive-ID/count 게이트를 갖는 직접 행 read임

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

해석: 보상 및 훔치기 필드는 스키마-only가 아니다. `m_Exp`, `m_Money`, `m_JP`에는 `+0x8C`, `+0x90`, `+0x98`의 단순 스칼라 조회 함수가 있다. `+0x9C`의 `m_DropReward`는 `EnemyDropID` 행 ID다. `+0xA0`의 `m_StealReward`와 `+0xA4`의 `m_StealLeafNum`에는 복사된 값이 양수일 때만 false가 아닌 결과를 반환하는 불리언 overload가 있으므로, 그 overload들에서 `0`과 음수 값은 의미론적으로 “not present”다.

##### ◆ C-ENEMY-REVERSE-INDEX-08: `m_RaceIndices`와 `m_TypeID`가 모든 적 행에 대한 역방향 스캔을 지원함

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

해석: `m_RaceIndices`는 스칼라 종족 필드가 아니다. Race 소속 관계은 `HasEnemyRace(enemyId, raceId)`로 테스트된 뒤 파생 적 ID 배열을 만드는 데 사용된다. 마찬가지로 `m_TypeID`는 표현 포인터일 뿐이 아니다. `GetSameTypeEnemies`는 모든 `EnemyID` 행을 전체 스캔하고 행 `+0x18`을 요청된 타입 ID와 비교한다. 따라서 `m_TypeID`는 표현 link이자 타입-classification 키다.

##### ◆ C-ENEMY-OFFSETMAP-09: 압축 `EnemyID` 행 레이아웃과 소비자 분류

| 오프셋 | 키 | 증거 등급 | 런타임 해석 |
|---:|---|---|---|
| `+0x0C` | `m_Label` | label 인덱스 / 조회 proof | `FDbAccessHelper` label maps가 사용한다. label-to-ID 조회은 행 `m_id`를 반환한다. |
| `+0x14` | `m_DisplayName` | 직접 사용처 | Game-텍스트 ID. `AGameTextManager::GetGameText(..., 4u, DisplayName, 0)`로 해석된다. |
| `+0x18` | `m_TypeID` | 직접 사용처 | 모든 `EnemyTypeID` 표현 데이터로 가는 브리지이며, 동일 타입 역방향 스캔에서도 사용된다. |
| `+0x1C` | `m_WeakID` | 직접 사용처 via 약점-플래그/resist paths | 명시적 약점 덮어쓰기가 없을 때의 기본 약점 행. |
| `+0x20` | `m_DisplayLevel` | 직접 사용처 | `_wtoi`로 파싱되는 UTF-16 문자열. 행에서는 일반 정수가 아니다. |
| `+0x30/+0x64` | `m_BreakRate`, `m_DamageRate` | 직접 조회 함수/구체화된 스칼라 | 대미지 비율 파라미터. `m_BreakRate`에는 직접 조회 함수가 있다. |
| `+0x34..+0x60` | `m_MaxSLD`, `m_MaxHP`, `m_MaxSP`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_CrtDef`, `m_Hit`, `m_Avd` | direct 상태 switch plus 구체화 | 대부분의 필드는 `GetEnemyStatus`로 반환된다. `m_CrtDef`는 구체화되지만 표시된 switch로는 도달하지 않는다. |
| `+0x68/+0x70` | `m_ResistAilmentID`, `m_ResistAilment` | 구체화됨/인접 스킬 저항 증거 | 실제 행 필드. 정확한 상태 이상 저항 소비자는 약점/resist 소비자보다 덜 직접적이다. |
| `+0x80/+0x84` | `m_TacticalAssignID`, `m_SkillsID` | 직접 조회 함수 및 택티컬 스킬 소속 관계 | 택틱스 및 스킬 목록 ID. `m_SkillsID`는 `TacticalSkillList`를 통해 해석된다. |
| `+0x88` | `m_IsBoss` | 직접 조회 함수/구체화된 바이트 | Boss 플래그. boss-조건 및 표현 paths가 사용하지만, 동작은 여전히 문맥 의존적이다. |
| `+0x8C/+0x90/+0x94/+0x98` | `m_Exp`, `m_Money`, `m_PetExp`, `m_JP` | Exp/Money/JP 직접 조회 함수; 구체화된 `PetExp` | 결과 보상. `PetExp`는 gameplay evidence가 불완전한 구체화된 필드로 남는다. |
| `+0x9C/+0xA0/+0xA4` | `m_DropReward`, `m_StealReward`, `m_StealLeafNum` | 직접 사용처 | 드롭 테이블 ID와 훔치기 값. 훔치기 overload는 양수 값을 요구한다. |
| `+0xA8` | `m_EventDropRewards` | 직접 배열 조회 함수 및 드롭 덮어쓰기 증거 | 이벤트-드롭 덮어쓰기 배열. 드롭 결과 로직과 함께 소비된다. |
| `+0xB8` | `m_StatusOffset` | 직접 UI 오프셋 조회 함수 | 전투 상태 표시 배치에 사용되는 two-부동소수점 벡터. |
| `+0xC8` | `m_Bottle` | 구체화됨/그룹 슬롯 상관 증거 | 실제 행 스칼라. 현재 최선의 해석은 여전히 multipart ordinal / panel-part 인덱스다. |
| `+0xCC/+0xCD/+0xCE/+0xCF` | `m_NameUI`, `m_LevelUI`, `m_HpUI`, `m_ShieldUI` | 레벨/HP/shield 직접 사용처; 이름 사용처는 불완전 | Level/HP/shield는 UI visibility를 바꾼다. `m_NameUI`는 구체화되지만 복원된 family에서 같은 직접 조회 함수 패턴은 부족하다. |
| `+0xD0` | `m_RaceIndices` | 직접 소속 관계 및 역방향 스캔 사용처 | 종족 ID 배열이며 스칼라 enum이 아니다. |
| `+0xE0/+0xE4/+0xE8/+0xEC` | `m_DisplaySpecialSkillGauge`, `m_GenerateSpecialSkillValueOrverTurn`, `m_ReduceSpecialSkillValueBreak`, `m_MaxSpecialSkillValue` | 직접 UI 및 변동 사용처 | 적 BP/special-게이지 표시 및 변동 매개변수. |
| `+0xF0/+0xF1/+0xF2/+0xF3` | `m_OutsideTarget`, `m_NotAction`, `m_UnknownLv`, `m_Immortal` | outside/not-액션/immortal 직접 설정 시드; 구체화됨 `UnknownLv` plus UI-경로 evidence from prior notes | 초기 전투 캐릭터 플래그. Outside/not-액션/immortal은 `SetupParams` reset 후 명시적으로 시드된다. 정확한 `UnknownLv` 소비자는 레벨 UI 경로를 테스트하기 전까지 보수적으로 다루어야 한다. |


### ■ (**) `EnemyReinforcements`
전투 중간에 생성될 수 있는 추가 적(reinforcements)과 그들이 나타나야 할 위치를 정의하는 전투 인카운터 helper 테이블이다.
- `m_Reinforcements`는 `EnemyID.m_id`를 입력으로 받는다.
- `m_Positions`는 `BattlePositions.m_id`를 입력으로 받는다(해당 적 그룹의 formation이 이 위치를 지정할 필요는 없다).
`EnemyReinforcements.m_id`를 사용하는 유일한 asset은 `Skill/` 아래 `SkillAvailID`다.
#### ■ 런타임 읽기 의미론
##### ◆ C-REINF-01: EnemyReinforcements 행 해석기

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_Positions` | `row-owned TArray, destructor frees this->m_Positions`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 행 해석기는 스트라이드 `0x38`의 `EnemyReinforcements` 행을 반환한다. destructor/복사 symbols는 `m_Positions`를 동반 행 소유 `TArray`로 보존한다. 정확한 생성 소비자는 여기서 복원되지 않았다. | 부정적/부분 복원 | `C-REINF-01` |
| `m_Reinforcements` | `row-owned TArray, destructor frees this->m_Reinforcements`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 행 해석기는 스트라이드 `0x38`의 `EnemyReinforcements` 행을 반환한다. destructor/복사 symbols는 `m_Reinforcements`를 행 소유 `TArray`로 보존한다. `SkillAvailID` 이외의 정확한 gameplay 소비자는 이 snippets에서 복원되지 않았다. | 부정적/부분 복원 | `C-REINF-01` |

### ■ (****) `EnemyGroups`
모든 적 그룹을 기록한다. 주로 다음을 결정한다.
- 어떤 `EnemyID`가 함께 생성되는지,
- formation과 전투-이벤트/abort context.

전투가 게임 안에서 트리거되려면 여기에 엔트리를 추가해야 한다.

- `m_Label`: 새로 추가한 적 그룹의 경우 이 label이 고유하고 기존 엔트리와 충돌하지 않도록 한다. 이벤트는 이 label을 사용하여 적 그룹과의 전투를 트리거한다. Monster Arena에 엔트리를 추가할 계획이라면, 그 엔트리를 다른 용도로 다시 사용할 수 없다. 그렇지 않으면 격파 후 Monster Arena 메뉴가 나타나고 크래시가 발생할 수도 있다.
- `m_Inescapable`: 전투에서 도망칠 수 있는지 결정하는 플래그.
- `m_DisableRetire`: OT0에서는 항상 false이며, OT0에서는 기능하지 않는 것으로 보인다. CotC에서 전투 중 휴대폰을 suspend할 수 있는 기능과 관련이 있을지도 모른다?
- `m_SkipResult`: 전투 결과 화면(획득 XP, Money, JP 등)을 건너뛸지 결정하는 플래그.
- `m_IgnoreDefeat`: 적 그룹에게 패배했을 때 Game Over 화면이 나타나지 않게 하는 플래그. King Pardis III, Edoras Defenders of Fame/Power/Wealth, Colossal Blightant만 사용한다. 적이 Monster Arena에 있으면 덮어쓰기되어 패배 후 그곳으로 돌아가게 하는 것으로 보인다.
- `m_SkipBgmResult`: 승리 테마가 재생될지 결정하는 플래그.
- `m_BanGodBeast`: 전투에서 Divine Beast 사용을 비활성화한다. OT0에서는 미사용이다. mod에서 어떤 캐릭터의 스킬 board에 Divine Beast 스킬를 추가하면 이를 차단할 수 있다(미테스트).
- `m_PlayerMemberSetID`: 참조된 `PlayerMemberSetID`에 지정된 캐릭터들로 파티를 강제 구성한다([커스텀 캐릭터를 추가하는 방법](customchars_en.md)도 참조).
- `m_Bgm`: 전투의 배경 음악을 설정한다. 사용 가능한 옵션은 `Sound/` 아래 `SoundList` 참조.
- `m_NightBgm`: 밤 전투의 배경 음악을 설정한다. OT0에서는 미사용이며 기능 여부는 미테스트.
- `m_DarkBgm`: `m_NightBgm`과 같음? CotC 엔트리들은 두 키 모두 항상 같은 값을 가진 것으로 보인다.
- `m_BgmType`: 정확한 용도는 불명확하지만 `Kingship_structs.hpp`는 다음을 정의한다.

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

- `m_ResultBgmWin`: 커스텀 승리 테마를 지정한다.
- `m_ResultBgmLose`: 커스텀 Game Over 테마를 지정한다.
- `m_CameraSet`: 사용할 카메라 set을 지정한다(입력: `BattleCameraSet.m_id`). 일부 오래된 노트에서는 이를 `m_Camera`라고 부르지만, 타입 지정 C-내보내기 구체화 처리는 `m_CameraSet`을 사용한다(`Octopath_Traveler0-Win64-Shipping.exe.c`:17923518-17923533).
- `m_Formation`: 적 그룹의 formation을 지정한다(입력: `EnemyFormations.m_id`).
- `m_EnemyID`: 필드의 적들을 지정한다(입력: `EnemyID.m_id`). 이 배열을 6명을 초과하도록 확장해도 게임이 즉시 크래시하지는 않지만, 새 적들은 timeline에 표시되지 않고 약점/실드/상태 overlay가 없으며, 턴을 얻은 뒤 게임이 크래시한다(오류는 손상된 allocator metadata/손상된 포인터와 관련됨). reinforcement 호출로 우회할 수 있을지도 모른다. 하드 엔진 cap 또는 안전한 우회 방법을 문서화하기 전에, 이를 현재 로컬 재현이 여전히 필요한 authoring hazard로 취급할 것.

- `m_EventIndices`: 전투 시작 또는 중간에 재생되는 (전투) Events(전투 중 대사 등). 가능한 값은 `EventList.m_id`가 아니라 `BattleEventList.m_id`일 가능성이 높다.
- `m_AbortCondition`: 전투를 abort하기 위한 조건. 사용 가능한 ID는 아마 `BattleAbortConditions` 아래에서 찾을 수 있다.
- `m_DefeatPriority`: OT0와 CotC에서 항상 false다. 무언가를 한다면 무엇인지는 불확실하다.
- `m_ForcedWinAbortCondition`: 전투를 강제로 승리시키는 custom 조건을 설정한다. 사용 가능한 ID는 아마 `BattleAbortConditions` 아래에서 찾을 수 있다.
- `m_IsAllEnemyDeadOnForceWin`: (미테스트) `m_ForcedWinAbortCondition`에 설정된 조건을 충족했을 때 모든 적이 사망하는지를 결정하는 플래그?
- `m_ForcedLoseAbortCondition`: 전투를 강제로 패배시키는 custom 조건을 설정한다. 사용 가능한 ID는 아마 `BattleAbortConditions` 아래에서 찾을 수 있다.
- `m_ImportantFlags`: 여섯 개의 서로 다른 플래그. 모두 OT0에서는 미사용이며, 테스트로 의미를 확인할 수 없었다(`m_EnemyID`와 같은 길이의 배열이므로 필드의 어느 적에게 적용할지 선택하는 것 외에는 여섯 플래그 사이에 차이가 없을 가능성이 높다).
  - 플래그 1: CotC의 Job Tower bosses가 주로 사용한다.
  - 플래그 2: `Dice_Bolaven_Rank04_BossGr03`(Idore, Roguish Riven Soldier I, Roguish Riven Soldier II와의 전투)만 사용한다.
  - 플래그 3: 플래그 2와 같은 적 그룹만 사용한다.
  - 플래그 4-6: 두 게임 모두에서 사용되지 않는다.
-  `m_PetDear`: 값으로 -1, 0, 1을 사용한다.
-  `m_DisableBattleEndWipe`: 많은 최종 보스(Bestower of All, Or'Galdera, Side Solistia의 Galdera)와 Magitek Armor가 사용한다.
-  `m_DisableBattleEndTraining`: 훈련장에서 파티 멤버가 XP, JP 등을 얻지 못하게 한다. Emerald Direwolf 전투(필드에 Laurana가 있을 때만), Bestower of All Ch. 7의 Sazantos 회상 중 전투 등에서 사용된다.
-  `m_DisableSupporter`: 전투에서 helper를 소환할 수 있는지 결정하는 플래그.
-  `m_EncountEffectTypeID`: 특수 인카운트 효과를 지정한다(예: FF6 collab encounters). Arena battles에서는 이를 0으로 설정해야 하며, 그렇지 않으면 게임이 soft-잠금된다. 사용 가능한 ID는 `EncountEffectTypeID` 아래에서 찾을 수 있다. SDK는 다음 효과를 지정한다: 0=None, 2=Whiteout, 3=Blackout.
-  `m_EnablePlayerShield`: OT0와 CotC 모두에서 항상 false로 설정된다.
-  `m_DisableGrade`: OT0에서는 항상 false이며, CotC에서는 그렇지 않다. 무기 grades를 무시하는 플래그일 가능성이 높다.
-  `m_IsContainRareEnemy`: 어떤 적 그룹이 rare enemies를 포함한다고 간주되는지 게임에 알려, rare 적 인카운트 rate를 높이는 accessories가 증가하도록 하는 플래그일 가능성이 높다.

#### ■ 런타임 읽기 의미론
이 테이블에 대한 이전 address-레벨 notes는 여기서 타입 지정 C 내보내기의 명명된 `FEnemyGroupsBase`, `UEnemyDB`, `ASymbolEnemy` 증거로 대체된다. 그룹 구체화 처리, 그룹 해석기, 직접 스칼라 조회 함수, 심볼 적 브리지가 함께 행 레이아웃과 주요 소비자를 제공한다.

【주요 C 증거】

##### ◆ C-GROUP-01: EnemyGroups 행 해석기

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

##### ◆ C-GROUP-02: 구체적인 행 오프셋을 갖는 EnemyGroups 구체화 처리

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

##### ◆ C-GROUP-03: EnemyGroups 직접 스칼라 사용처

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

##### ◆ C-SYMBOL-03: 심볼 적 런타임 대입 및 GroupId/Index 브리지 to EnemyGroups

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_AbortCondition` | `C-GROUP-02:17923540` `outData->m_AbortCondition = *(_DWORD *)(v18 + 88);` | 키는 행 오프셋 `+0x58`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_BanGodBeast` | `C-GROUP-02:17923526` `outData->m_BanGodBeast = *(_BYTE *)(v18 + 25);` | 키는 행 오프셋 `+0x19`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Bgm` | `C-GROUP-02:17923528` `outData->m_Bgm = *(_DWORD *)(v18 + 32);`<br>`C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | 키는 행 오프셋 `+0x20`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_BgmType` | `C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | 키는 행 오프셋 `+0x2C`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Camera` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | OT0 `EnemyGroups`에서는 `m_Camera` 필드가 복원되지 않았다. 구체화 처리는 행 `+0x30`의 활성 필드를 `m_CameraSet`이라고 명명한다. `m_Camera`는 오래된 노트 또는 이전 alias로 취급해야 한다. | 복원됨 | `C-GROUP-02` |
| `m_CameraSet` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | 키는 행 오프셋 `+0x30`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DarkBgm` | `C-GROUP-02:17923530` `outData->m_DarkBgm = *(_DWORD *)(v18 + 40);` | 키는 행 오프셋 `+0x28`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DefeatPriority` | `C-GROUP-02:17923541` `outData->m_DefeatPriority = *(_BYTE *)(v18 + 92);` | 키는 행 오프셋 `+0x5C`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndTraining` | `C-GROUP-02:17923550` `outData->m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);` | 키는 행 오프셋 `+0x85`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndWipe` | `C-GROUP-02:17923549` `outData->m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);` | 키는 행 오프셋 `+0x84`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableGrade` | `C-GROUP-02:17923554` `outData->m_DisableGrade = *(_BYTE *)(v18 + 141);` | 키는 행 오프셋 `+0x8D`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableRetire` | `C-GROUP-02:17923522` `outData->m_DisableRetire = *(_BYTE *)(v18 + 21);` | 키는 행 오프셋 `+0x15`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableSupporter` | `C-GROUP-02:17923551` `outData->m_DisableSupporter = *(_BYTE *)(v18 + 134);` | 키는 행 오프셋 `+0x86`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnablePlayerShield` | `C-GROUP-02:17923553` `outData->m_EnablePlayerShield = *(_BYTE *)(v18 + 140);` | 키는 행 오프셋 `+0x8C`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EncountEffectTypeID` | `C-GROUP-02:17923552` `outData->m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);` | 키는 행 오프셋 `+0x88`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnemyID` | `C-GROUP-02:17923535` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EnemyID,` | 키는 행 오프셋 `+0x38`의 `TArray` 헤더로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_EventIndices` | `C-GROUP-02:17923538` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EventIndices,` | 키는 행 오프셋 `+0x48`의 `TArray` 헤더로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedLoseAbortCondition` | `C-GROUP-02:17923544` `outData->m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);` | 키는 행 오프셋 `+0x68`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedWinAbortCondition` | `C-GROUP-02:17923542` `outData->m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);` | 키는 행 오프셋 `+0x60`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Formation` | `C-GROUP-02:17923533` `outData->m_Formation = *(_DWORD *)(v18 + 52);` | 키는 행 오프셋 `+0x34`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_IgnoreDefeat` | `C-GROUP-02:17923524` `outData->m_IgnoreDefeat = *(_BYTE *)(v18 + 23);` | 키는 행 오프셋 `+0x17`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ImportantFlags` | `C-GROUP-02:17923546` `(TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&outData->m_ImportantFlags,` | 키는 행 오프셋 `+0x70`의 `TArray` 헤더로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Inescapable` | `C-GROUP-02:17923521` `outData->m_Inescapable = *(_BYTE *)(v18 + 20);` | 키는 행 오프셋 `+0x14`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsAllEnemyDeadOnForceWin` | `C-GROUP-02:17923543` `outData->m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);` | 키는 행 오프셋 `+0x64`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsContainRareEnemy` | `C-GROUP-02:17923555` `outData->m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);` | 키는 행 오프셋 `+0x8E`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_Label` | `C-GROUP-02:17923520` `outData->m_Label = *(FName *)(v18 + 12);` | 키는 행 오프셋 `+0x0C`의 `FName`으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_NightBgm` | `C-GROUP-02:17923529` `outData->m_NightBgm = *(_DWORD *)(v18 + 36);` | 키는 행 오프셋 `+0x24`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_PetDear` | `C-GROUP-02:17923548` `outData->m_PetDear = *(_DWORD *)(v18 + 128);` | 키는 행 오프셋 `+0x80`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_PlayerMemberSetID` | `C-GROUP-02:17923527` `outData->m_PlayerMemberSetID = *(_DWORD *)(v18 + 28);` | 키는 행 오프셋 `+0x1C`의 32비트 스칼라로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_ResultBgmLose` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | C 내보내기에서 정확한 `m_ResultBgmLose` 심볼이나 명확한 애셋별 offset은 복원되지 않았다. 따라서 이 필드는 추론된 것이 아니라 unrecovered로 문서화한다. | 부정적/부분 복원 | not 복원됨 |
| `m_ResultBgmWin` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | C 내보내기에서 정확한 `m_ResultBgmWin` 심볼이나 명확한 애셋별 offset은 복원되지 않았다. 따라서 이 필드는 추론된 것이 아니라 unrecovered로 문서화한다. | 부정적/부분 복원 | not 복원됨 |
| `m_SkipBgmResult` | `C-GROUP-02:17923525` `outData->m_SkipBgmResult = *(_BYTE *)(v18 + 24);` | 키는 행 오프셋 `+0x18`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |
| `m_SkipResult` | `C-GROUP-02:17923523` `outData->m_SkipResult = *(_BYTE *)(v18 + 22);` | 키는 행 오프셋 `+0x16`의 바이트/불리언 레인으로 읽힌다. 그룹 행은 `GroupID`로 스트라이드 `0x90`을 사용해 해석되고, `FDbAccessHelper<FEnemyGroupsBase>::GetData`는 이 정확한 레인을 `outData`에 복사한다. | 복원됨 | `C-GROUP-01`, `C-GROUP-02` |


### ■ (**) `EnemyModeID`
어떤 모드 ID가 필드 또는 적에게 적용될 어떤 효과를 가리키는지 게임에 알려준다. 이 효과들은 avails에 할당할 수 있다. 보스가 boost 모드에 들어가 special 효과를 표시할 때 사용된다.

#### ■ 런타임 읽기 의미론
`EnemyModeID`는 런타임에서 enum-bound가 아니다. 엔진은 `EnemyModeID` 데이터베이스 접근 helper를 통해 정수 모드 ID를 해석한 뒤 88바이트 행을 읽는다. 활성 모드 변경 system은 활성 모드를 `MODE_CHANGE` 상태 이상로 저장하며, 그 `FAilmentInfo.m_LinkedEnemyModeID`를 요청된 `EnemyModeID`와 비교한다. 따라서 확장은 고정 SDK enum이 아니라 데이터베이스 키/인덱스 availability와 모드 ID를 제공하는 callers에 의해 제약된다.

【주요 C 증거】

##### ◆ C-MODE-01: `EnemyModeID` 행 조회은 DB 키 해석과 88바이트 stride를 사용함

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

##### ◆ C-MODE-02: 전체 테이블 인터페이스가 `ADatabaseDefineStatic`을 통해 노출됨

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

##### ◆ C-MODE-03: 복사된 행 레이아웃과 스칼라/배열 레인

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

##### ◆ C-MODE-04: 모드 변경 제거 조건는 행 `+0x18/+0x28`에서 읽힘

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

##### ◆ C-MODE-05: 모드 변경 제거 조건는 기본 상태 이상 제거 조건 레인을 덮어쓰기함

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

##### ◆ C-MODE-06: 행 `+0x38`은 모드가 활성일 때 적용되는 attach-효과 ID임

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

##### ◆ C-MODE-07: 행 `+0x14`는 진입 시 세 제어 상태 이상의 즉시 제거를 제어함

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

##### ◆ C-MODE-08: 활성 모드 identity는 `FAilmentInfo.m_LinkedEnemyModeID`로 저장됨

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

【필드 단위 해석】

| 런타임 레인 / 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_id` | `C-MODE-01` `GetArrayIndex(..., ModeID)` 및 `Data[88 * ArrayIndex]`; `C-MODE-03` 행 `+0x08` 복사 | `ModeID`는 데이터베이스 키다. 런타임은 SDK enum을 닫힌 domain으로 사용하지 않는다. 공급된 정수를 DB 접근 helper로 해석하고 `EnemyModeID` 데이터 목록를 인덱싱한다. | 복원됨 | `C-MODE-01`, `C-MODE-03` |
| 행 `+0x14` enter-모드 상태 이상-clear 플래그 | `C-MODE-07` `if ( EnemyModeDB && *(_BYTE *)&EnemyModeDB[20] )` | 이 바이트가 0이 아니면 모드에 진입할 때 적에게서 `PARALYSIS`, `FASCINATION`, `SLEEP`을 제거한다. decomp는 소비자에서 이 바이트의 신뢰할 수 있는 내보낸 필드 이름을 보존하지 않으므로, 가이드는 오프셋과 효과로 이를 식별한다. | 복원됨, 소비자에서 이름 미보존 | `C-MODE-07` |
| 제거 조건 배열, 아마 테이블의 조건 레인 | `C-MODE-04` `*pConditions = ... (v8 + 24)`; `C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | 복원된 소비자에서 이 배열은 모드 변경 상태 이상 removal 판정식 set으로만 사용된다. common 조건 평가기와 슬롯-compatible하다: 조건 ID 배열 plus 매개변수 배열. | 복원됨 | `C-MODE-04`, `C-MODE-05` |
| 제거-매개변수 배열, 아마 테이블의 매개변수 레인 | `C-MODE-04` `*pParams = ... (v8 + 40)`; `C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | 이 배열은 removal 판정식의 슬롯별 매개변수를 제공한다. 조건 배열과 병렬로 편집해야 하며, 길이가 맞지 않으면 평가기 슬롯이 없는 기본값을 읽거나 검사에 실패할 위험이 있다. | 복원됨 | `C-MODE-04`, `C-MODE-05` |
| 행 `+0x38`의 attach-효과 ID | `C-MODE-06` `SetAttachEffect(this, *(_DWORD *)&EnemyModeDB[56])` | 모드에 진입할 때 엔진은 행 `+0x38`의 32비트 ID를 읽고 `AQPBattleCharacterBase::SetAttachEffect`에 직접 전달한다. 이는 활성 모드 aura/attached VFX 레인이며 `EnemyTypeID`의 일반 attach-효과 레인과 구분된다. | 복원됨, 소비자에서 정확한 속성명 미보존 | `C-MODE-06` |
| 활성 모드 상태 | `C-MODE-08` `info.m_AilmentType == MODE_CHANGE`; `info.m_LinkedEnemyModeID == EnemyModeID` | 활성 모드는 `EnemyModeID` 행을 캐릭터에 쓰는 것이 아니라 `MODE_CHANGE` 상태 이상 레코드로 표현된다. 행은 entry/exit behavior를 설정하고, 활성 identity는 `FAilmentInfo` 안에 저장된 linked 모드 ID다. | 복원됨 | `C-MODE-08` |
| `SkillAvailID.m_ModeChangeID` linkage | `C-MODE-05` `SkillAvailDB->m_ModeChangeID` | `MODE_CHANGE` 상태 이상를 적용하는 avails는 모드-특정 removal 조건를 가져오는 데 사용되는 모드 ID를 제공한다. 이것이 avail이 `EnemyModeID`를 가리키는 검증된 경로다. | 복원됨 | `C-MODE-05` |

### ■ (**) `EnemyDropID`
적에게 할당할 수 있는 모든 다양한 드롭 ID를 추적한다.
#### ■ 런타임 읽기 의미론
`EnemyDropID`는 별도의 드롭 테이블 데이터베이스다. `EnemyID.m_DropReward`는 아이템 ID가 아니라 드롭 테이블 ID를 저장한다. 결과 계산 시 엔진은 그 ID를 96바이트 `EnemyDropID` 행으로 해석하고, 아이템 ID, 비율/가중치, fixed counts, variable counts, 선택적 이벤트 드롭 퀘스트 필터의 병렬 배열을 읽은 다음, 선택된 결과 아이템을 전투 save-데이터 result 배열에 기록한다.
【주요 C 증거】

##### ◆ C-DROP-01: `EnemyID.m_DropReward`는 `EnemyID` 행 `+0x9C`에서 드롭 테이블 ID로 읽힘

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

##### ◆ C-DROP-02: 드롭 테이블 조회은 DB 키 해석과 96바이트 stride를 사용함

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

##### ◆ C-DROP-03: 전체 테이블 인터페이스가 `ADatabaseDefineStatic`을 통해 노출됨

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

##### ◆ C-DROP-04: 복사된 행 레이아웃은 ID/이름 접두부 뒤에 다섯 배열 레인을 포함함

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

##### ◆ C-DROP-05: 아이템 ID, 비율/가중치, counts, 이벤트 퀘스트 필터가 offset-특정 조회 함수로 노출됨

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

##### ◆ C-DROP-06: listed 퀘스트가 진행 중이면 이벤트 드롭가 `EnemyID.m_DropReward`를 덮어쓰기할 수 있음

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

##### ◆ C-DROP-07: result 계산은 아이템 ID, 비율/가중치, count ranges를 슬롯별로 소비함

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

##### ◆ C-DROP-08: result UI는 전투 save-데이터 배열에서 선택된 result 아이템/개수를 읽음

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

【필드 단위 해석】

| 런타임 레인 / 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `EnemyID.m_DropReward` | `C-DROP-01` `*(_DWORD *)&result[156]` | 이 `EnemyID` 필드는 일반 드롭 테이블 ID다. dropped 아이템 ID가 아니다. 결과 ID는 `GetEnemyDropDB`로 전달된다. | 복원됨 | `C-DROP-01`, `C-DROP-02` |
| `EnemyID.m_EventDropRewards` | `C-DROP-06` `UEnemyDB::GetEnemyEventDropIDs(v2)` 후 이벤트 드롭 ID를 순회 | 이벤트 드롭 ID는 일반 드롭 계산 전에 시도된다. 이벤트 드롭 행 하나라도 현재 진행 중인 퀘스트 ID를 하나 이상 가지고 있으면, 엔진은 `DropType = 1`을 설정하고 일반 `m_DropReward` 대신 그 드롭 ID를 사용한다. | 복원됨 | `C-DROP-06` |
| `EnemyDropID.m_id` | `C-DROP-02` `GetArrayIndex(..., DropID)` 및 `Data[96 * ArrayIndex]`; `C-DROP-04` 행 `+0x08` 복사 | `DropID`는 테이블의 DB 접근 helper로 해석된다. 테이블 확장은 enum-domain 문제가 아니라 데이터베이스-키/인덱스 문제이며, callers가 충분히 넓은 ID 필드를 사용하는지에 달려 있다. | 복원됨 | `C-DROP-02`, `C-DROP-04` |
| 행 `+0x10`의 아이템 ID 배열 | `C-DROP-05` `*IDs = ... &EnemyDropDB[16]`; `C-DROP-07` `v46 = *(_DWORD *)Data` | 후보 아이템 ID의 병렬 배열이다. Result 계산은 활성 슬롯의 양수가 아닌 아이템 ID를 무시한다. | 복원됨, 조회 함수에서 정확한 내보낸 property 이름 미보존 | `C-DROP-05`, `C-DROP-07` |
| 행 `+0x20`의 비율/가중치 배열 | `C-DROP-05` `return EnemyDropDB + 32`; `C-DROP-07`가 `DropItemRates`를 순회 | 후보 가중치의 병렬 배열이다. 엔진은 비율/가중치를 합산하고, 양수 아이템-ID 슬롯에 대해 normalize한 다음, 무작위 굴림값로 슬롯을 선택한다. | 복원됨, 조회 함수에서 정확한 내보낸 property 이름 미보존 | `C-DROP-05`, `C-DROP-07` |
| 행 `+0x30`의 고정 개수 배열 | `C-DROP-05` `GetDropItemCounts(DropID, 0)`가 `result + 48` 반환; `C-DROP-07` `v54 = fixedCounts[slot]` | 드롭에 더해지는 기본 count다. | 복원됨, 조회 함수에서 정확한 내보낸 property 이름 미보존 | `C-DROP-05`, `C-DROP-07` |
| 행 `+0x40`의 가변 개수 배열 | `C-DROP-05` `GetDropItemCounts(DropID, 1)`가 `result + 64` 반환; `C-DROP-07` `GetRandomRange(variableCounts[slot], 0)` | additive 무작위 range다. 최종 선택 count는 `fixedCount[slot] + random(0..variableCount[slot])`이고, 같은 루틴에서 지원/마을 드롭-count scaling이 이어진다. | 복원됨, 조회 함수에서 정확한 내보낸 property 이름 미보존 | `C-DROP-05`, `C-DROP-07` |
| 행 `+0x50`의 이벤트 퀘스트 ID 배열 | `C-DROP-05` `GetEventDropQuestIDs`가 `EnemyDropDB + 80` 반환; `C-DROP-06` `UQuestUtility::IsQuestInProgress(*v13)` | 이벤트 드롭 행에서는 이 배열이 이벤트 드롭 행 사용 여부를 게이트한다. 복원된 경로에서는 진행 중인 양수 퀘스트 ID 하나만 있어도 이벤트 드롭 substitution이 활성화된다. | 복원됨, 조회 함수에서 정확한 내보낸 property 이름 미보존 | `C-DROP-05`, `C-DROP-06` |
| result 아이템/개수 output | `C-DROP-08`가 `ResultItemID[]` 및 `ResultItemCount[]`를 읽음 | 드롭 계산 후 UI-facing retrieval은 더 이상 `EnemyDropID`를 읽지 않고 전투 save 데이터의 구체화된 결과 배열를 읽는다. 따라서 `EnemyDropID` 변경은 선택/count generation에 영향을 주며 post-hoc UI 조회에는 영향을 주지 않는다. | 복원됨 | `C-DROP-08` |

### ■ (**) `EncountVolume`
위험도, 조우 가능한 적 그룹(`m_EnemyGroup` 및 `m_FirstEncount`, 후자는 아마 최초 조우 적 그룹 지정), 그리고 각각의 조우 확률을 결정한다. Encount Volumes는 `EncountList`에서 맵과 연결된다. 맵은 위험도를 변경할 수 있으며, 따라서 encount volumes도 바뀔 수 있다. `m_EncountRatio` 값들의 합이 100이 아니면 문제가 생길 수 있다.
#### ■ 런타임 읽기 의미론
【주요 C 증거】

##### ◆ C-ENCOUNT-01: EncountVolume 행 해석기

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

##### ◆ C-ENCOUNT-02: EncountVolume을 현재 인카운트 영역으로 구체화

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

##### ◆ C-ENCOUNT-03: EncountVolume 선택 의미

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_EncountRatio` | `C-ENCOUNT-02:17643576` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EncountRatio,`<br>`C-ENCOUNT-02:17643579` `if ( outEncountVolume.m_EncountRatio.AllocatorInstance.Data )`<br>`C-ENCOUNT-02:17643580` `FMemory::Free(outEncountVolume.m_EncountRatio.AllocatorInstance.Data);` | 인카운트-volume 행은 스트라이드 `0x38`로 해석된다. 현재-인카운트 구체화 처리는 행 `+0x28`에서 `m_EncountRatio`를 `TArray<int>`로 복사한다. 선택 동안 이는 `m_CurrentRatios`가 된다. | 복원됨 | `C-ENCOUNT-01`, `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_EnemyGroup` | `C-ENCOUNT-02:17643567` `memset(&outEncountVolume.m_EnemyGroup, 0, 32);`<br>`C-ENCOUNT-02:17643573` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EnemyGroup,`<br>`C-ENCOUNT-02:17643581` `if ( outEncountVolume.m_EnemyGroup.AllocatorInstance.Data )` plus 1 further local occurrences | 인카운트-volume 행은 스트라이드 `0x38`로 해석된다. 현재-인카운트 구체화 처리는 행 `+0x18`에서 `m_EnemyGroup`을 `TArray<int>`로 복사한다. 선택 동안 이는 `m_CurrentGroups`가 된다. | 복원됨 | `C-ENCOUNT-01`, `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_FirstEncount` | `C-ENCOUNT-03:17641121` `int m_FirstEncount; // edi`<br>`C-ENCOUNT-03:17641196` `m_FirstEncount = EncountVolume->m_FirstEncount;`<br>`C-ENCOUNT-03:17641199` `if ( m_FirstEncount > 0 && UEnemyDB::GetEnemyGroupDB(m_FirstEncount) )` plus 1 further local occurrences | `m_FirstEncount`는 구체화된 `FEncountVolumeBase`에서 읽힌다. first-인카운트 플래그가 아직 설정되어 있고 이 그룹 ID가 `GetEnemyGroupDB`를 통해 해석되면, 함수는 무작위 weighted 선택 전에 그 그룹을 반환한다. | 복원됨 | `C-ENCOUNT-03` |

#### ■ 추가 런타임 증거: 인카운트 영역는 위험도과 적 타입 사전 계산 모두에 입력됨

인카운트 subsystem은 `EncountList.m_EncountVolume`을 `EncountVolume` 행으로 해석한 뒤 위험도 및 적-그룹/rate 배열를 읽는다. 별도의 helper `GetMaxEncountEnemyTypes`는 인카운트 목록, volumes, groups, 적 ID, potential reinforcements를 정적으로 순회하여 preallocation이 필요할 수 있는 distinct 적 타입의 최대 개수를 결정한다.

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

| 키 | 런타임 해석 |
|---|---|
| `m_DangerLevel` | `EncountList.m_EncountVolume`이 해석된 뒤 `EncountVolume +0x0C`에서 읽힌다. |
| `m_EnemyGroup` | `+0x18/+0x20`의 행 소유 배열로 저장된다. 타입-사전 계산 경로는 이 배열을 임시 그룹-ID 목록로 복사하고 각 `EnemyGroups` 행을 해석한다. |
| `m_FirstEncount` | 표시된 `GetDangerLevel` 또는 `GetMaxEncountEnemyTypes` 경로에서는 소비되지 않는다. 이 경로들에서 없다는 사실은 전역 미사용을 증명하지 않고, 이 두 common helper paths가 이를 무시한다는 것만 증명한다. |
| `m_EncountRatio` | `+0x28/+0x30`의 행 소유 배열로 저장된다. danger-레벨 operations를 위한 `FEncountVolumeBase` scratch 객체를 만들 때 `m_EnemyGroup`과 함께 복사된다. |

### ■ (**) `EncountList`
OT0의 무작위 인카운트 rule 테이블이다. 어떤 encount volumes가 어떤 맵에서 조우될 수 있는지(`m_EncountVolume`은 `m_EncountVolume.m_id`), 조우까지의 최소 걸음 수(`m_EncountStepMin`), 그리고 조우가 발생하기 위해 충족해야 할 조건를 지정한다.

추가 런타임 관련 키:
- `m_DisableCondition`: 이 조우를 비활성화하기 위해 충족해야 하는 조건.
- `m_RandomStepA` 및 `m_RandomStepB`: 무작위 step range/curve의 매개변수. 두 키 모두 OT0와 CotC에서 항상 같은 값을 갖는 것으로 보인다.
- `m_FirstEncountStepRatio`: 맵/존에 진입한 뒤 첫 조우에 특화된 multiplier일 가능성이 높으며, 일반보다 더 빠르거나 늦게 만든다.

#### ■ 런타임 읽기 의미론
【주요 C 증거】

##### ◆ C-ENCOUNT-01: EncountVolume 행 해석기

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

##### ◆ C-ENCOUNTLIST-01: EncountList 복사 생성자

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_DisableCondition` | `C-ENCOUNTLIST-01:17639800` `this->m_DisableCondition = __that->m_DisableCondition;` | `FEncountListBase` 복사 생성자는 `m_DisableCondition`을 스칼라 필드로 복사한다. 이 필드는 인카운트-volume 해석 전 구체화됨 인카운트 목록 행의 일부다. | 복원됨 | `C-ENCOUNTLIST-01` |
| `m_EncountStepMin` | `C-ENCOUNTLIST-01:17639801` `this->m_EncountStepMin = __that->m_EncountStepMin;` | `FEncountListBase` 복사 생성자는 `m_EncountStepMin`을 스칼라 필드로 복사한다. | 복원됨 | `C-ENCOUNTLIST-01` |
| `m_EncountVolume` | `C-ENCOUNTLIST-01:17639805` `this->m_EncountVolume = __that->m_EncountVolume;` | `FEncountListBase` 복사 생성자는 `m_EncountVolume`을 복사한다. 이후 인카운트 code는 이 ID를 사용하여 `EncountVolume` 행을 해석한다. | 복원됨 | `C-ENCOUNTLIST-01`, `C-ENCOUNT-01` |
| `m_FirstEncountStepRatio` | `C-ENCOUNTLIST-01:17639804` `this->m_FirstEncountStepRatio = __that->m_FirstEncountStepRatio;` | `FEncountListBase` 복사 생성자는 `m_FirstEncountStepRatio`를 스칼라 필드로 복사한다. | 복원됨 | `C-ENCOUNTLIST-01` |
| `m_RandomStepA` | `C-ENCOUNTLIST-01:17639802` `this->m_RandomStepA = __that->m_RandomStepA;` | `FEncountListBase` 복사 생성자는 `m_RandomStepA`를 스칼라 필드로 복사한다. | 복원됨 | `C-ENCOUNTLIST-01` |
| `m_RandomStepB` | `C-ENCOUNTLIST-01:17639803` `this->m_RandomStepB = __that->m_RandomStepB;` | `FEncountListBase` 복사 생성자는 `m_RandomStepB`를 스칼라 필드로 복사한다. | 복원됨 | `C-ENCOUNTLIST-01` |

#### ■ 추가 런타임 증거: 현재 인카운트 목록 선택은 조건 게이트 적용이고 맵별임

`AQPEncountManager::GetCurrentEncountList`는 `m_MapId`, `m_Condition`, `m_DisableCondition`의 운용상 의미를 보여준다. 행은 현재 map ID로 필터링되고, disable 조건이 true이면 거부되며, 마지막으로 `m_Condition`의 모든 non-zero 조건가 true로 평가될 때만 accepted된다.

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

| 키 | 런타임 해석 |
|---|---|
| `m_MapId` | `GetCurrentEncountList`의 primary 필터. `m_MapId == current map id`인 행만 후보로 남는다. |
| `m_DisableCondition` | 존재하고 그 퀘스트 조건이 true로 평가되면, 일반 조건이 테스트되기 전에 행이 거부된다. |
| `m_Condition` | 임시 배열로 복사되고 0이 제거된 뒤, 남은 모든 퀘스트 조건가 통과해야 한다. 이는 0 제거 후의 conjunctive 조건이지 probabilistic 조건이 아니다. |
| `m_EncountStepMin`, `m_RandomStepA`, `m_RandomStepB`, `m_FirstEncountStepRatio`, `m_EncountVolume`, `m_SoundList` | 선택된 `FEncountListBase` scratch 객체에 행 오프셋 `+0x24`, `+0x28`, `+0x2C`, `+0x30`, `+0x34`, `+0x38`에서 그대로 복사된다. 표시된 함수는 이 값들을 선택하고 복사하지만 무작위-step 굴림값 자체는 수행하지 않는다. |


### ■ (*) `EncountEffectTypeID`
여러 encount 효과를 추적하며, `m_SeId`는 재생할 sound 효과를 지정할 가능성이 높다.
#### ■ 런타임 읽기 의미론
【주요 C 증거】

##### ◆ C-STATIC-01: 대표적인 정적 DB 접근 함수 인터페이스

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_SeId` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | C 내보내기에서 정확한 `m_SeId` 심볼이나 키-레벨 사용처는 복원되지 않았다. 이 내보내기에서는 테이블 exposure/정적 DB 인터페이스만 단언할 수 있다. | 부정적/부분 복원 | `C-STATIC-01` |

### ■ (*) `EnemyGroupsByCondition`
특정 조건이 충족될 때 생성할 적 groups의 집합이다. Bestower of All Chapter 7에서 Ringbearer와 함께 Accurst Flame을 생성하는 데 사용된다.

#### ■ 런타임 읽기 의미론
`EnemyGroupsByCondition`은 조건-to-그룹 선택기다. C 내보내기는 행에서 두 aligned 배열를 복사하고, 각 조건을 순서대로 평가하며, 처음 만족한 퀘스트 조건과 같은 배열 인덱스의 적-그룹 ID를 반환함을 보여준다.

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

| 키 | 런타임 해석 |
|---|---|
| `m_Conditions` | 행 배열 `+0x18/+0x20`에 저장된다. 각 값은 퀘스트 조건 ID로 취급되어 `UQuestUtility::GetQuestConditionPtr` 및 `CheckQuestTaskCondition`을 거친다. |
| `m_EnemyGroups` | 행 배열 `+0x28/+0x30`에 저장된다. `m_Conditions`와 위치상 정렬되어 있으며, 처음 true인 조건은 `m_EnemyGroups[i]`를 반환한다. |
| `m_id` | 표시된 경로에서 함수는 `GetArrayIndex` 조회을 사용하지 않고 공급된 정수로 `m_DataList`를 인덱싱한다. 확장 시 이는 non-contiguous ID를 신중히 테스트해야 할 이유다. 이 함수는 키 조회 접근 함수가 아니라 배열-인덱스 접근 함수처럼 동작한다. |

### ■ (**) `SymbolEnemyList`
어떤 overworld sprites 또는 objects와 적 groups가 심볼/elite enemies에 할당되는지, 그리고 그들의 레벨을 지정한다.
- `m_GroupId`: 이 심볼 적 entry와 연결된 적 그룹.
- `m_Index`: 적 그룹 안에서 이 적의 인덱스. 여러 심볼 enemies가 같은 GroupId 또는 생성 controller를 공유할 때 흔히 사용된다.
- `m_Dir`: 심볼 적가 기본적으로 바라보는 방향.
- `m_ObjectId`: 특정 객체로 심볼 적 overworld sprite를 덮어쓰기한다(예: Bestower of Power Ch. 3의 Tatloch ships).
- `m_RepopCount`: respawn/repopulation count, 즉 심볼 적가 얼마나 자주 respawn하는지. OT0 적은 기본값이 0이며, CotC 적은 그렇지 않다(맵을 다시 로드해도 적이 즉시 다시 나타나지 않는다).
- `m_Redrawing`: OT0와 CotC 모두에서 항상 0으로 설정된다.
- `m_TargetLevel1` 및 `m_TargetLevel2`: 심볼 적의 레벨 range일 수 있다.
- `m_Scale`: 필드에서 적 심볼의 scale multiplier(순수 시각/충돌 크기).
- 
#### ■ 런타임 읽기 의미론
【주요 C 증거】
##### ◆ C-SYMBOL-01: SymbolEnemyList 행 구체화 처리

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

##### ◆ C-SYMBOL-03: 심볼 적 런타임 대입 및 GroupId/Index 브리지 to EnemyGroups

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_Dir` | `C-SYMBOL-01:17851302` `outData->m_Dir = *(_DWORD *)(v7 + 20);` | 키는 행 오프셋 `+0x14`의 32비트 스칼라로 읽힌다. `FSymbolEnemyList::GetData`가 행을 구체화하고, `ASymbolEnemy::SetSymbolData`가 구체화된 값을 활성 심볼 적 객체로 복사한다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_GroupId` | `C-SYMBOL-01:17851300` `outData->m_GroupId = *(_DWORD *)(v7 + 12);` | `m_GroupId`는 심볼 객체에 복사된 뒤 `EnemyGroups` ID로 사용된다. 이후 `m_Index`가 `EnemyGroups.m_EnemyID[m_Index]`를 선택하는 데 사용되며, 이 값이 심볼 적의 `m_EnemyListId`가 된다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Index` | `C-SYMBOL-03:17650576` `int m_Index; // eax`<br>`C-SYMBOL-03:17650593` `this->m_data.m_Index = data->m_Index;`<br>`C-SYMBOL-03:17650657` `m_Index = this->m_data.m_Index;` plus 3 further local occurrences | `m_Index`는 심볼-적 경로의 그룹 슬롯 선택기다. `m_GroupId`가 `EnemyGroups` 행으로 해석된 뒤, 런타임은 `EnemyGroups.m_EnemyID[m_Index]`를 읽는다. | 복원됨 | `C-SYMBOL-03` |
| `m_ObjectId` | `C-SYMBOL-01:17851303` `outData->m_ObjectId = *(_DWORD *)(v7 + 24);` | 키는 행 오프셋 `+0x18`의 32비트 스칼라로 읽힌다. `FSymbolEnemyList::GetData`가 행을 구체화하고, `ASymbolEnemy::SetSymbolData`가 구체화된 값을 활성 심볼 적 객체로 복사한다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Redrawing` | `C-SYMBOL-01:17851305` `outData->m_Redrawing = *(_DWORD *)(v7 + 32);` | 키는 행 오프셋 `+0x20`의 32비트 스칼라로 읽힌다. `FSymbolEnemyList::GetData`가 행을 구체화하고, `ASymbolEnemy::SetSymbolData`가 구체화된 값을 활성 심볼 적 객체로 복사한다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_RepopCount` | `C-SYMBOL-01:17851304` `outData->m_RepopCount = *(_DWORD *)(v7 + 28);` | 키는 행 오프셋 `+0x1C`의 32비트 스칼라로 읽힌다. `FSymbolEnemyList::GetData`가 행을 구체화하고, `ASymbolEnemy::SetSymbolData`가 구체화된 값을 활성 심볼 적 객체로 복사한다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Scale` | `C-SYMBOL-01:17851308` `outData->m_Scale = *(float *)(v7 + 44);` | 키는 행 오프셋 `+0x2C`의 32비트 부동소수점로 읽힌다. `FSymbolEnemyList::GetData`가 행을 구체화하고, `ASymbolEnemy::SetSymbolData`가 구체화된 값을 활성 심볼 적 객체로 복사한다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel1` | `C-SYMBOL-01:17851306` `outData->m_TargetLevel1 = *(_DWORD *)(v7 + 36);` | 키는 행 오프셋 `+0x24`의 32비트 스칼라로 읽힌다. `FSymbolEnemyList::GetData`가 행을 구체화하고, `ASymbolEnemy::SetSymbolData`가 구체화된 값을 활성 심볼 적 객체로 복사한다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel2` | `C-SYMBOL-01:17851307` `outData->m_TargetLevel2 = *(_DWORD *)(v7 + 40);` | 키는 행 오프셋 `+0x28`의 32비트 스칼라로 읽힌다. `FSymbolEnemyList::GetData`가 행을 구체화하고, `ASymbolEnemy::SetSymbolData`가 구체화된 값을 활성 심볼 적 객체로 복사한다. | 복원됨 | `C-SYMBOL-01`, `C-SYMBOL-03` |

#### ■ 추가 런타임 증거: `SymbolEnemyList.m_Index`는 그룹 슬롯 선택기이며 선택 사항 타입 replacement도 있음

심볼-적 initializer는 심볼 그룹과 심볼 행를 actor 상태에 복사한 뒤, `SymbolEnemyList.m_GroupId`를 `EnemyGroups` 행으로 해석한다. `SymbolEnemyList.m_Index`는 `EnemyGroups.m_EnemyID`에서 엔트리를 선택하고, 선택된 적 ID는 심볼 actor의 `m_EnemyListId`가 된다.

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

| 키 | 런타임 해석 |
|---|---|
| `SymbolEnemyGroupList.m_StartSymbolEnemyId` | 심볼 actor의 `m_group` 상태에 직접 복사된다. 표시된 initializer는 이를 평가하지 않고 그룹 metadata로 보존한다. |
| `SymbolEnemyGroupList.m_SymbolEnemyId` | 배열로 actor 상태에 복사된다. 표시된 initializer는 probabilities를 sample하지 않고 후보-심볼 배열를 보존한다. |
| `SymbolEnemyGroupList.m_probability` | `m_SymbolEnemyId`와 함께 배열로 actor 상태에 복사된다. 따라서 probability 의미는 이 initializer 밖에 있다. |
| `SymbolEnemyList.m_GroupId` | `EnemyGroups.m_DBAccess`의 키로 사용된다. resulting `EnemyGroups` 행은 구체적인 전투 적 슬롯를 제공한다. |
| `SymbolEnemyList.m_Index` | `EnemyGroups.m_EnemyID`에 대한 배열 인덱스로 사용된다. 이것이 그룹 슬롯 선택기라는 가장 강력한 런타임 증거다. |
| `m_Dir`, `m_ObjectId`, `m_RepopCount`, `m_Redrawing`, `m_TargetLevel1`, `m_TargetLevel2`, `m_Scale` | 심볼 actor의 `m_data` 구조체에 직접 복사된다. 이는 런타임 구체화를 증명한다. excerpt 자체만으로 각 필드의 후속 behavioral 사용처를 증명하지는 않는다. |
| `nReplaceTypeID` / `EnemyID.m_TypeID` | 명시적인 replacement 타입 ID가 supplied되면 그것을 사용한다. 그렇지 않으면 actor는 `(symbol id, index)`를 적 ID로 매핑하고, 그 적의 `m_TypeID`를 읽고, call SE를 위해 타입 행를 해석한다. |

### ■ (**) `SymbolEnemyGroupList`
심볼 적 IDs(`SymbolEnemyList` 테이블의 `m_id`)를 특정 맵의 특정 심볼 enemies를 가리키는 내부 ID에 할당하는 것으로 보인다. `m_SymbolEnemyId` 배열은 `m_StartSymbolEnemyId` 아래의 심볼 적가 격파된 뒤 repopulation 시 주어진 지점에 어떤 심볼 enemies가 나타나는지 결정할 수 있으며, 각 심볼 적의 probabilities는 `m_probability` 아래에 지정된다. CotC도 OT0도 이 배열들을 사용하는 것으로 보이지 않는다.
#### ■ 런타임 읽기 의미론
【주요 C 증거】
##### ◆ C-SYMBOL-02: SymbolEnemyGroupList 구체화 처리/연산자=

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

##### ◆ C-SYMBOL-03: 심볼 적 런타임 대입 및 GroupId/Index 브리지 to EnemyGroups

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_StartSymbolEnemyId` | `C-SYMBOL-02:17844263` `this->m_StartSymbolEnemyId = __that->m_StartSymbolEnemyId;` | 대입 연산자는 `m_StartSymbolEnemyId`를 활성 심볼 그룹 객체로 직접 복사한다. | 복원됨 | `C-SYMBOL-02`, `C-SYMBOL-03` |
| `m_SymbolEnemyId` | `C-SYMBOL-02:17844265` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_SymbolEnemyId,`<br>`C-SYMBOL-02:17844266` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_SymbolEnemyId);` | 대입 연산자는 `m_SymbolEnemyId`를 `TArray<int>`로 활성 심볼 그룹 객체에 복사한다. | 복원됨 | `C-SYMBOL-02`, `C-SYMBOL-03` |
| `m_id` | `C-SYMBOL-02:17844262` `this->m_id = __that->m_id;` | 키는 행 오프셋 `+0x08`의 32비트 스칼라로 읽힌다. 대입 연산자는 구체화 시 그룹-목록 ID를 보존한다. | 복원됨 | `C-SYMBOL-02` |
| `m_probability` | `C-SYMBOL-02:17844268` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_probability,`<br>`C-SYMBOL-02:17844269` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_probability);` | 대입 연산자는 `m_probability`를 `TArray<int>`로 활성 심볼 그룹 객체에 복사한다. 인용된 custom-적 경로에서는 probability-선택 사용처가 복원되지 않았다. | 복원됨 | `C-SYMBOL-02`, `C-SYMBOL-03` |


## ■ `Battle/` 아래
### ■ (***) `BattleCameraParams`
전투 카메라 presets/configurations에 대응하는 ID다. 값 `-1.0`은 게임에서 “use default”로 해석되는 것으로 보인다.
- `m_PosotionX/Y/Z`: 3D 공간에서 전투 카메라 위치.
- `m_RotationX/Y`: X/Y 방향의 카메라 rotation/tilt.
- `m_FOV`: Field of 뷰. 좁은 필드 of 뷰 = zoomed-in/telephoto 느낌. FOV를 변경할 때 party 위치가 고정되어 있음을 유념할 것.
- `m_SequencerPath` Cinematic/Sequencer 바인딩, 아마 전투 카메라 cuts/targeting에 사용되는 Level Sequence 또는 Sequencer-driven 카메라 rig asset을 가리킨다.
- `m_FocalDistance`, `m_FocusDistance`: focus plane까지의 거리(하나는 legacy vs new naming일 수 있음(CotC에는 `m_FocalDistance`만 있음), 또는 다른 시스템일 수 있음). 둘 다 OT0에서는 항상 `-1.0`으로 설정되고, CotC는 다른 focal distances를 사용한다.
- `m_FocalRegion`: in-focus region의 크기.
- `m_NearBlurSize` 및 `m_FarBlurSize`: 거리에 따른 blur intensities.
- `m_MaxBokehSize`: bokeh blur 상한.
- `m_Scale`: 카메라 distance scale/shot scale 덮어쓰기일 가능성이 높다.

#### ■ 런타임 읽기 의미론
##### ◆ C-CAMERA-01: BattleCameraParams 행 해석기

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

##### ◆ C-CAMERA-02: BattleCameraParams 구조체 복사

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_FOV` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FarBlurSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalDistance` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalRegion` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocusDistance` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_MaxBokehSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_NearBlurSize` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_Scale` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_SequencerPath` | `packed camera-copy slot; copied in order by FBattleCameraParamsBase::Copy`(패킹된). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `AQPBattleManager::GetCameraParam`은 스트라이드 `0x60`으로 `BattleCameraParams` 행을 해석한다. 복원된 구조체 복사 코드는 그 뒤 행을 패킹된 스칼라/문자열 레인로 복사한다. decomp는 모든 스칼라 카메라 좌표의 신뢰할 수 있는 필드별 names를 보존하지 않으므로, 복사된 sequence 밖의 오프셋은 신중히 다루어야 한다. | 복원됨 | `C-CAMERA-01`, `C-CAMERA-02` |


#### ■ 심층 런타임 분석: `BattleCameraParams` 행 의미론, 카메라 세트 indirection, DOF 폴백

카메라 매개변수 테이블은 두 가지 경로로 소비된다. 첫째, 활성 `BattleCameraSet`은 execution 카메라 타입을 구체적인 `BattleCameraParams.m_id`로 매핑한다. 둘째, 해석된 행은 고정 96바이트 레코드로 해석된다. `+0x08`의 `m_id`, `+0x10`부터 시작하는 `FString`인 `m_SequencerPath`, `+0x20..+0x34`의 트랜스폼/FOV 부동소수점, `+0x38..+0x58`의 심도 부동소수점다. 즉 카메라-매개변수 행은 단순한 editor preset이 아니라 전투 카메라 상태로 직접 복사되는 런타임 payload다.

##### ◆ C-CAMERAPARAMS-LOOKUP-01: ID 조회, loaded-테이블 requirements, 96바이트 행 스트라이드

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

해석: `BattleCameraParams`는 데이터베이스 접근 helper를 통해 키 인덱스 방식되고 활성 행 스트라이드는 `0x60`이다. `m_BattleCameraSet.m_Loaded`에 대한 의외의 dependency는 의미상 중요하다. 대부분의 callers가 현재 카메라 set을 통해 카메라 매개변수에 도달하므로, set 테이블도 loaded 상태가 아니면 매개변수 행는 usable로 간주되지 않는다.

##### ◆ C-CAMERAPARAMS-SET-INDIRECTION-02: 카메라 타입에서 카메라 매개변수 ID로의 indirection

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

해석: `BattleCameraParams` 엔트리는 보통 hard-coded 카메라 매개변수 ID가 아니라 `BattleCameraSet`을 통해 선택된다. Camera 타입 `1`은 중립 카메라, `3`은 start, `4`는 break, `5`는 victory 등이다. 따라서 카메라 매개변수 행은 데이터로서 완전히 valid하더라도, 어떤 카메라 set도 이를 가리키지 않으면 사용되지 않을 수 있다.

##### ◆ C-CAMERAPARAMS-TRANSFORM-CACHE-03: 트랜스폼/FOV 부동소수점가 전투-manager 카메라 caches로 복사됨

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

해석: 트랜스폼 block은 정확히 `+0x20..+0x34`다. 위치 vector, 두 rotation 부동소수점, FOV. 따라서 철자가 틀린 내보낸 키 `m_PosotionX/Y/Z`는 symbolic label이나 unused editor metadata가 아니라 literal 카메라 위치 triple로 읽어야 한다.

##### ◆ C-CAMERAPARAMS-SEQUENCER-PATH-04: 시퀀서 바인딩은 비어 있지 않은 공백 제거 경로로 테스트됨

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

해석: 이 판정식는 `m_SequencerPath`를 임의의 불리언으로 해석하지 않는다. 런타임은 `+0x10/+0x18`의 `FString`을 복사하고, 공백을 제거하며, 결과 문자열 길이가 UTF-16 element 하나를 초과할 때만 카메라를 시퀀서-driven으로 취급한다. 따라서 whitespace-only 경로는 이 검사에서 no 시퀀서 경로와 동일하다.

##### ◆ C-CAMERAPARAMS-DOF-SENTINEL-05: 심도 필드는 `INVALID_PARAM` sentinel 의미를 사용함

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

해석: `-1.0`식 값은 단순히 관례적 defaults가 아니다. `ABattleCameraBase::INVALID_PARAM`와 비교되며, 그 sentinel 이하의 필드는 건너뛰고 활성 cine-카메라 setting을 overwrite하지 않는다. 이는 focal distance, focal region, near/far transition regions, near/far blur, scale, max bokeh size, focus distance에 독립적으로 적용된다.

##### ◆ C-CAMERAPARAMS-NEUTRAL-DOF-FALLBACK-06: DOF 보간은 중립 카메라 행을 통해 폴백함

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

해석: DOF 보간은 부동소수점 인덱스 `14..22`, 즉 행 오프셋 `+0x38..+0x58`을 사용한다. 어느 endpoint든 sentinel 값을 포함하면, 중립 카메라 행이 보간 전에 폴백 값을 제공한다. 따라서 중립-카메라 DOF는 단순히 또 하나의 카메라 preset이 아니라 다른 카메라 modes의 default source다.

이 사용처들에서 복원된 필드 map:

| Exported 키 / 레인 | 런타임 위치 | 해석 |
|---|---:|---|
| `m_id` | `+0x08`; DB 키 조회 | DB 접근 helper가 선택하며, 보통 카메라 세트 indirection 뒤에 선택된다. |
| `m_SequencerPath` | `+0x10` 포인터, `+0x18` length | `FString`으로 복사되고 spaces가 제거되며, non-empty 경로는 시퀀서-카메라 behavior를 활성화한다. |
| `m_PosotionX/Y/Z` | `+0x20/+0x24/+0x28` | Camera world-위치 vector가 전투-manager 카메라 caches로 복사된다. |
| `m_RotationX/Y` | `+0x2C/+0x30` | 두 rotation components가 위치 vector와 함께 복사된다. |
| `m_FOV` | `+0x34` | FOV가 중립/start/break/victory/etc. 카메라 states의 트랜스폼 필드와 함께 복사된다. |
| `m_FocalDistance` | `+0x38` / 부동소수점 인덱스 `14` | DOF 필드. `<= INVALID_PARAM`이면 skip되고, 그렇지 않으면 cine-카메라 post-process에 적용된다. |
| `m_FocalRegion` | `+0x3C` / 부동소수점 인덱스 `15` | DOF 필드. 동일한 sentinel/폴백 규칙을 사용한다. |
| near/far DOF transition 레인 | `+0x40/+0x44` / 인덱스 `16/17` | guide-facing 스키마에서 두드러지게 명명하지 않을 수 있어도 런타임에는 존재한다. |
| `m_NearBlurSize`, `m_FarBlurSize` | `+0x48/+0x4C` / 인덱스 `18/19` | sentinel보다 클 때 각각 cine-카메라 post-process에 적용된다. |
| `m_Scale` | `+0x50` / 인덱스 `20` | 전투-레이아웃 scale이 아니라 심도 scale에 적용된다. |
| `m_MaxBokehSize` | `+0x54` / 인덱스 `21` | sentinel보다 클 때 cine-카메라 max bokeh size에 적용된다. |
| `m_FocusDistance` | `+0x58` / 인덱스 `22` | post-process writes 이후 focus-settings branch에서 처리된다. |

### ■ (***) `BattleCameraSet`
적 groups에 할당되는 카메라 configurations의 집합(`BattleCameraSet.m_id`=`EnemyGroups.m_Camera`)이다. CotC에는 존재하지 않는다(CotC는 전투에서 하나의 중립 카메라 configuration만 사용한다).

`Kingship_structs.hpp`는 OT0의 일부 카메라 sets에 다음 labels를 부여한다.

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

#### ■ `BattleCameraSet` as a 카메라 타입 디스패치 테이블

`BattleCameraSet`은 카메라 매개변수 ID의 compact 테이블이다. 런타임은 `m_id`를 찾기 위해 테이블을 스캔하고, 그 뒤 `ABattleCameraBase::GetCameraParamID`가 런타임 카메라 타입를 관련 필드로 디스패치한다. 따라서 `EnemyGroups.m_CameraSet`은 단일 카메라 행가 아니라 카메라-매개변수 행의 *family*를 선택한다.

##### ◆ C-CAMERASET-LOOKUP-01: 카메라 세트 행는 `m_id`로 스캔되며 56-바이트 stride를 가짐

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

해석: `BattleCameraParams`와 달리 이 특정 해석기는 `m_DataList`에 대해 linear 행 스캔을 수행한다. 활성 행 스트라이드는 `0x38`이고, `targetCameraSetID <= 0`은 set 없음으로 취급된다.

##### ◆ C-CAMERASET-TYPE-DISPATCH-02: 카메라 타입별 필드 단위 디스패치

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

해석: `BattleCameraSet`을 확장하거나 편집하는 것은 이 디스패치 테이블을 편집하는 것으로 이해해야 한다. 런타임 카메라 타입이 어떤 필드에 도달하지 않는다면 일반 전투에서 그 필드가 사용되지 않을 수 있지만, 그래도 set 레코드 안에서는 활성 필드다.

### ■ (***) `BattlePositions`
`EnemyFormations` 또는 `EnemyReinforcements` 등에 쓰이는 위치 IDs를 정의한다. `m_Priority`는 좌표가 충돌할 때 어떤 sprite가 앞에 표시되는지, 또는 적이 선택되는 순서를 결정할 수 있다.
#### ■ 런타임 읽기 의미론
##### ◆ C-FORM-02: Formation 위치 해석 into BattlePositions

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

##### ◆ C-BPOS-01: BattlePositions 행 해석기

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_Priority` | `+0x20`(스칼라). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | formation 슬롯이 `BattlePositions` ID를 산출한 뒤, `GetBattlePositionDB`는 `0x28`바이트 행을 해석한다. `GetEnemyPlacement`는 `+0x10`의 좌표 배열에서 XYZ를 복사한 뒤 `BattlePositions +0x20`에서 priority를 읽는다. | 복원됨 | `C-BPOS-01`, `C-FORM-02` |


#### ■ `BattlePositions` as a 공유 좌표 테이블

`BattlePositions`는 적 formations에 한정되지 않는다. 런타임은 레이드 뷰, player-side staging, reinforcement placement, 레이아웃-set anchoring에도 이를 사용한다. 행 자체는 키 인덱스 방식이며 40-바이트 stride를 갖는다.

##### ◆ C-BATTLEPOSITIONS-COORDINATES-01: 위치 행 조회 및 좌표 extraction

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

해석: public 좌표 helper는 행의 위치-vector 레인을 역참조하여 `X/Y/Z` vector를 쓴다. C 내보내기는 이 테이블이 general 좌표 service 역할을 함을 증명한다. formation 소속 관계은 그중 하나의 사용처일 뿐이다.

### ■ (***) `BattleEventList`
`EnemyGroups.m_EventIndices` 아래 적 groups에 할당될 수 있는 events일 가능성이 높다.
- `m_IsMustPlay`: 이벤트가 skippable인지 여부(?)
- `m_EventCommand`: trigger될 때 실행할 커맨드 script ID(`BattleEventCommand`를 가리킴)
- `m_EventConditions`: Condition 타입 IDs. 다른 파일(`TacticalList`, `SkillConditionList`, `TacticalActionList` 등)의 `m_Conditions` 배열와 같을 가능성이 높다.
- `m_EventParams`: 조건의 매개변수.
- `m_EventStatusTypes`: 상태 타입 필터 레인. 타입 지정 C 내보내기는 이 레인을 `m_EventConditions`, `m_EventParams`, `m_EventEnemies`와 함께 `UBattleUtility::CheckCondition`으로 전달한다(`Octopath_Traveler0-Win64-Shipping.exe.c`:17485857-17485873).
- `m_EventEnemies`: 조건이 어느 적 슬롯/어느 적 인덱스/어느 part에 적용되는지 지정하는 선택 사항 필터(OT0와 CotC 모두에서 사용되지 않는 것으로 보임).

관련 가능성: `Kingship_structs.hpp`에는 다음이 있다.

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
#### ■ 런타임 읽기 의미론

【주요 C 증거】

##### ◆ C-BEVENT-01: BattleEventList 행 해석기

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

##### ◆ C-BEVENT-02: BattleEventList 조건 평가

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

##### ◆ C-BEVENT-03: Shared 전투-조건 tuple 평가기

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

##### ◆ C-BCMD-01: BattleEventCommand 행 해석기

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_Conditions` | `line 16978495` `v8 = this->m_Conditions.AllocatorInstance.Data;` in `void __fastcall FSkillConditionListBase::~FSkillConditionListBase(FSkillConditionListBase *this)`<br>`line 17044430` `this->m_Conditions.AllocatorInstance.Data = nullptr;` in `FSkillConditionListBase *__fastcall FSkillConditionListBase::FSkillConditionListBase(` | 이 section에서 `m_Conditions`는 `m_EventConditions`가 사용하는 것과 같은 조건-ID namespace를 뜻한다. `BattleEventList`의 활성 필드 이름은 `m_EventConditions`다. | 복원됨 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventCommand` | `line 17481721` `EventCommandDB = AQPBattleManager::GetEventCommandDB(this, EventListDB->m_EventCommand, m_NowEventCommandIndex);` in `char __fastcall AQPBattleManager::ExecuteBattleEventCommand(AQPBattleManager *this)`<br>`line 17483882` `int m_EventCommand; // eax` in `void __fastcall AQPBattleManager::InitBattleEvent(AQPBattleManager *this, bool forceFlag)` | 이벤트 행가 선택된 뒤, `ExecuteBattleEventCommand`는 `EventListDB->m_EventCommand`와 현재 커맨드 인덱스를 `GetEventCommandDB`에 전달하고, 그 함수가 커맨드-행 sequence를 해석한다. | 복원됨 | `C-BEVENT-01`, `C-BCMD-01` |
| `m_EventConditions` | `C-BEVENT-02:17478613` `&EventListDB->m_EventConditions,` | 전투-이벤트 평가기는 `m_EventConditions`를 primary 조건-ID 레인으로 `UBattleUtility::CheckConditions`에 전달한다. | 복원됨 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventEnemies` | `C-BEVENT-02:17478619` `&EventListDB->m_EventEnemies,` | 이벤트 평가기는 `m_EventEnemies`를 적 ID/필터 레인으로 공유 조건 평가기에 전달한다. 그 평가기에서 누락된 슬롯는 zero로 default된다. | 복원됨 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventParams` | `C-BEVENT-02:17478614` `&EventListDB->m_EventParams,` | 이벤트 평가기는 `m_EventParams`를 `m_EventConditions`와 슬롯별로 aligned된 매개변수 레인으로 전달한다. | 복원됨 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventStatusTypes` | `C-BEVENT-02:17478615` `&EventListDB->m_EventStatusTypes,` | 이벤트 평가기는 `m_EventStatusTypes`를 `m_EventConditions`와 슬롯별로 aligned된 상태 필터 레인으로 전달한다. | 복원됨 | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_IsMustPlay` | `line 17485812` `if ( !pEvent->m_IsMustPlay )` in `char __fastcall AQPBattleManager::IsMustPlayEvent(AQPBattleManager *this, const FBattleEventListBase *pEvent)` | `m_IsMustPlay`는 이벤트-제어 code에서 불리언 게이트로 읽힌다. false이면 이벤트가 must-play 경로를 통해 강제되지 않는다. | 복원됨 | `C-BEVENT-02`, `C-BEVENT-03` |

### ■ (***) `BattleEventCommand`
전투 이벤트 sequence 안의 scripted steps를 포함한다. `BattleEventList`가 어떤 조건에서 무엇을 실행할지를 설명한다면, `BattleEventCommand`는 실제 script steps(대화 표시, 애니메이션 재생, 효과 생성, 적 textures 교체, BGM 변경 등)를 실행하는 것으로 보인다.

- `m_NextWait`: 이 커맨드를 실행한 뒤 진행하기 전 delay, 단위는 seconds.
- `m_isWaitSkillTime`: true이면 스킬 animation이 끝날 때까지 기다린다.
- `m_isSwitchActionSkip`: 액션 sequences의 skip을 허용/강제한다.
- `m_PlayBgmBlock`: 무언가가 완료될 때까지 BGM changes를 block할 가능성이 높다.
- `m_PerformerID`: party/guest/적 수행자 목록에 대한 인덱스이거나 special ID일 수 있다.
- `m_PerformerIsLeader`: party leader를 수행자로 사용한다.
- `m_PerformerEnemy`: non-zero이면 수행자가 적임을 나타낸다(`EnemyID.m_id`를 input으로 쓰는 것으로 보임).
- `m_TargetID`: forced 액션/스킬의 target일 가능성이 높다. 관련 가능성:

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

- `m_EnableTarget`,`m_EnableAction`: 이 커맨드 도중/이후 player targeting/액션을 enable하는 toggles. OT0 decomp에는 `m_EnableAction`이 `AQPBattleCharacterBase::DisableNotAction(Character)`를 호출하고 상태가 바뀌면 상태 UI를 refresh하는 `BattleEventCommand` 사용처가 있으므로, 이 플래그는 scripted 전투 events 중 no-액션 상태를 clear할 수 있다(`Octopath_Traveler0-Win64-Shipping.exe.c`:17477565-17477624).
- `m_NameID`: speaker 이름의 localization ID(`GameTextCharacter` 아래에 있음).
- `m_TextID`: dialogue line의 localization ID(`GameTextEvent` 아래에 있음).
- `m_BalloonType`: balloon/speech bubble의 style(1 = 아마 normal speech bubble).
- `m_NoTail`: speech-bubble tail 없음.
- `m_BaloonOffsetX/Y`: 수행자 기준 balloon의 위치 offset.
- `m_NoName`: true이면 speaker 이름을 숨김.
- `m_NarrationID`: `NarrationList.m_id`의 IDs를 받음.
- - `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`: narration-style 관련 키.
- `m_AnimID`: 말하는 동안(또는 직전/직후) 수행자에서 재생할 animation.
- `m_DirID`: 수행자의 direction/facing preset.
- `m_isRewriteIdleMotion`: 커맨드 이후에도 idle 덮어쓰기를 유지.
- `m_SoundID`: sound를 재생(아마 `SoundList` ID).
- `m_SoundHandle`: 런타임 handle 슬롯일 가능성(예: SFX를 시작하고 handle을 저장하여 이후 커맨드가 stop/replace할 수 있게 함)(?).
- `m_EffectID`: 효과를 생성(아마 `EffectList` ID).
- `m_EffectHandle`: 이후 조작/정지용 런타임 handle 슬롯일 가능성(?).
- `m_EffectPosition`: 효과를 생성할 위치(수행자, target, screen 등).
- `m_BgmID`: BGM 변경(역시 `SoundList` ID일 가능성).
- `m_FadeTime`: BGM 변경의 fade duration.
- `m_IsRemoveAllAilments`: statuses를 clear(phase transitions용일 가능성).
- `m_EnableRetire`, `m_DisableGameOver`: (미검증) scripted battles 중 UI options와 fail states를 강제로 enable/disable(?)
- `m_LastBattleMapColor`: 전투 background tint 제어일 가능성.
- `m_TextureChangeEnemyIdx`: texture를 바꿀 적 슬롯(`-1`은 unused).
- `m_ChangeEnemyTextureIdx`: 전환할 texture 인덱스/슬롯(`-1` unused).
- `m_TextureChangeTime`: texture swap 타이밍(delay 또는 crossfade time일 수 있음, 미검증).
- `m_EnemyPartsIndex`: 영향을 받는 적 part 인덱스.
- `m_EnemyPartsDisplay`: 이벤트 중 참조된 적 part를 show/hide.
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: `TacticalList`에도 존재한다. 전투 engine은 아마 이 플래그를 기반으로 scripts를 진행한다(즉 무엇이 발생했는지 추적하는 implicit “next”다). 예: Shadow of Twin Worlds가 `TacticalList`에서 Lucian과 Lyblac의 break 시 Shadow의 약점을 unlock하는 tactic이 발생했는지 추적하여 Lucian과 Lyblac이 break에서 회복한 뒤 다시 잠금하는 데 사용된다. 해당 상황만을 위한 조건은 존재하지 않는 것으로 보인다.
- `m_JoinTrigger`: “unit joins 전투 now” events와 관련될 가능성(예: guest가 전투 중간에 합류).
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`: generic 구조체에 맞지 않는 special 커맨드 타입. `m_UniqueProcess`는 다음 값을 받을 가능성이 높다.
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

이 asset과 관련될 수 있는 enum:

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
#### ■ 런타임 읽기 의미론
【주요 C 증거】

##### ◆ C-BCMD-01: BattleEventCommand 행 해석기

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

##### ◆ C-BCMD-02: BattleEventCommand 기본 생성자: 복원된 명명 커맨드 필드

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

##### ◆ C-BCMD-03: BattleEventCommand EnableTarget/EnableAction 및 전투-map-color 사용처

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

##### ◆ C-BCMD-04: BattleEventCommand talk/narration 사용처

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

##### ◆ C-BCMD-05: BattleEventCommand 효과 및 효과 위치 사용처

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

##### ◆ C-BCMD-06: BattleEventCommand 수행자/leader 해석기

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_AnimID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;` (`m_AnimID`에 대한 coalesced anchor) | 이 내보내기에서는 `m_AnimID`가 별도의 `Command->m_AnimID` 사용처로 emitted되지 않았다. 생성자에서 `m_DirID`와 paired된 adjacent 커맨드-레인으로 취급하는 것이 최선이며, BattleEventCommand 심볼 아래에서는 런타임 animation consumption이 복원되지 않았다. | 부정적/부분 복원 | `C-BCMD-02` |
| `m_BalloonType` | `C-BCMD-04:17477766` `int m_BalloonType; // ecx`<br>`C-BCMD-04:17477784` `m_BalloonType = Command->m_BalloonType;`<br>`C-BCMD-04:17477785` `if ( (unsigned int)(m_BalloonType - 7) > 1 && (unsigned int)(m_BalloonType - 12) > 1 )` | `m_BalloonType`은 `BeginBattleEventTalk`에서 normal balloon 경로와 narration 경로를 선택한다. | 복원됨 | `C-BCMD-04` |
| `m_BgmID` | `C-BCMD-02:16987020` `*(_QWORD *)&this->m_BgmID = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_BlackoutNarration` | `C-BCMD-04:17477815` `IsBackoutInvisible = Command->m_BlackoutNarration;` | `m_BlackoutNarration`은 narration 경로가 blackout/invisibility behavior가 있는 extended narration call을 사용할지 제어한다. | 복원됨 | `C-BCMD-04` |
| `m_ChangeEnemyTextureIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;` (`m_ChangeEnemyTextureIdx`에 대한 coalesced anchor) | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_DirID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_DisableGameOver` | `C-BCMD-02:16987037` `*(_QWORD *)&this->m_DisableGameOver = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_EffectHandle` | `C-BCMD-05:17477471` `__int64 m_EffectHandle; // r13`<br>`C-BCMD-05:17477488` `m_EffectHandle = Command->m_EffectHandle;`<br>`C-BCMD-05:17477490` `v7 = m_EffectHandle - 1;` plus 2 further local occurrences | `m_EffectHandle`은 효과 커맨드 경로에서 `m_EffectID`와 함께 읽힌다. `m_EffectID`가 없더라도 handle은 기존 효과 슬롯을 address할 수 있다. | 복원됨 | `C-BCMD-05` |
| `m_EffectID` | `C-BCMD-05:17477472` `int m_EffectID; // r15d`<br>`C-BCMD-05:17477489` `m_EffectID = Command->m_EffectID;`<br>`C-BCMD-05:17477491` `v2 = m_EffectID > 0;` plus 2 further local occurrences | `m_EffectID`는 `m_EffectHandle`과 함께 읽힌다. positive 효과 ID는 이벤트 효과 경로에서 효과 creation을 활성화한다. | 복원됨 | `C-BCMD-05` |
| `m_EffectPosition` | `C-BCMD-05:17477512` `else if ( UEnemyDB::GetBattlePosition(&pos, Command->m_EffectPosition) )`<br>`C-BCMD-05:17477514` `if ( this->m_IsRaidBattle && Command->m_EffectPosition == 1 )` | `m_EffectPosition`은 `BeginBattleEventEffect`가 소비한다. positive/nonzero 위치은 `UEnemyDB::GetBattlePosition`을 통해 해석되고, raid battles는 위치 `1`을 special-case한다. | 복원됨 | `C-BCMD-05` |
| `m_EnableAction` | `C-BCMD-03:17477619` `if ( Command->m_EnableAction )` | `m_EnableAction`은 `BeginBattleEventEnemyStatus`가 직접 소비한다. true이면 `DisableNotAction`을 통해 target character의 no-액션 상태를 clear한다. | 복원됨 | `C-BCMD-03` |
| `m_EnableRetire` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | 인용된 커맨드-행 code에서 정확한 `m_EnableRetire` 심볼은 복원되지 않았다. 이 키는 이 내보내기에서 unrecovered로 취급한다. | 부정적/부분 복원 | `C-BCMD-02` |
| `m_EnableTarget` | `C-BCMD-03:17477585` `if ( Command->m_EnableTarget )` | `m_EnableTarget`은 `BeginBattleEventEnemyStatus`가 직접 소비한다. true이면 커맨드는 target character의 outside-target 상태를 clear하고 player targets를 reset할 수 있다. | 복원됨 | `C-BCMD-03` |
| `m_EnemyPartsDisplay` | `C-BCMD-02:16987032` `this->m_EnemyPartsDisplay = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_EnemyPartsIndex` | `C-BCMD-02:16987031` `this->m_EnemyPartsIndex = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_FadeTime` | `C-BCMD-02:16987030` `*(_QWORD *)&this->m_Fade = 0;` (`m_FadeTime`에 대한 coalesced anchor) | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_IsNextNarrationPage` | `C-BCMD-04:17477822` `Command->m_IsNextNarrationPage,`<br>`C-BCMD-04:17477829` `Command->m_IsNextNarrationPage,` | `m_IsNextNarrationPage`는 page-continuation 플래그로 narration-opening functions에 전달된다. | 복원됨 | `C-BCMD-04` |
| `m_IsRemoveAllAilments` | `C-BCMD-02:16987022` `*(_WORD *)&this->m_IsRemoveAllAilments = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_JoinTrigger` | `C-BCMD-02:16987010` `*(_WORD *)&this->m_JoinTrigger = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_LastBattleMapColor` | `C-BCMD-03:17477649` `__int64 m_LastBattleMapColor; // rax`<br>`C-BCMD-03:17477655` `if ( !Command \|\| !this->m_pModifyMaterialComponent \|\| Command->m_LastBattleMapColor < 0 )`<br>`C-BCMD-03:17477745` `m_LastBattleMapColor = Command->m_LastBattleMapColor;` plus 2 further local occurrences | `m_LastBattleMapColor`는 `BeginBattleEventLastBattleMapColor`가 소비한다. 부정적 값는 경로를 disable하고, 그렇지 않으면 material color를 적용하기 전에 전투-map color 테이블을 인덱스한다. | 복원됨 | `C-BCMD-03` |
| `m_NameID` | `C-BCMD-02:16987013` `*(_QWORD *)&this->m_NameID = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_NarrationID` | `C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )`<br>`C-BCMD-04:17477821` `Command->m_NarrationID,`<br>`C-BCMD-04:17477828` `Command->m_NarrationID,` | 커맨드가 narration balloon 타입을 사용할 때 `m_NarrationID`는 `OpenNarrationC`/`OpenNarrationEXC`가 소비한다. | 복원됨 | `C-BCMD-04` |
| `m_NextWait` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | 정확한 `m_NextWait` 심볼은 복원되지 않았다. 생성자는 coalesced `m_Fade`/타이밍 region을 노출하지만, 이 내보내기는 `m_NextWait`라는 별도 필드를 증명하지 않는다. | 부정적/부분 복원 | `C-BCMD-02` |
| `m_NoName` | `C-BCMD-02:16987012` `this->m_NoName = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_NoTail` | `C-BCMD-02:16987014` `this->m_NoTail = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_OffEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;` (`m_OffEventFlgIndex`에 대한 coalesced anchor) | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_OnEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_OptCharacterID` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | 정확한 `m_OptCharacterID` 심볼은 복원되지 않았고, adjacent unique-process option region과 `m_OptSkillID`만 명명되어 있다. | 부정적/부분 복원 | `C-BCMD-02` |
| `m_OptSkillID` | `C-BCMD-02:16987039` `this->m_OptSkillID = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerEnemy` | `C-BCMD-02:16987011` `this->m_PerformerEnemy = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerID` | `C-BCMD-02:16987009` `this->m_PerformerID = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerIsLeader` | `C-BCMD-06:17482218` `if ( Command->m_PerformerIsLeader )` | `m_PerformerIsLeader`는 `FindBattleEventCharacter`가 소비한다. true이면 수행자 해석이 ordinary 수행자-ID 경로가 아니라 party leader 경로를 반환한다. | 복원됨 | `C-BCMD-06` |
| `m_PlayBgmBlock` | `C-BCMD-02:16987024` `this->m_PlayBgmBlock = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundHandle` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundID` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;` (`m_SoundID`에 대한 coalesced anchor) | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_TargetID` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | 정확한 `m_TargetID` 심볼이나 명확한 커맨드-행 사용처는 복원되지 않았다. targeting은 이벤트 executor가 처리하지만, 이 내보내기는 해당 이름의 필드를 보존하지 않는다. | 부정적/부분 복원 | `C-BCMD-01` |
| `m_TextID` | `C-BCMD-04:17477765` `signed int m_TextID; // edi`<br>`C-BCMD-04:17477779` `m_TextID = Command->m_TextID;`<br>`C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )` plus 1 further local occurrences | `m_TextID`는 talk/narration 경로에서 읽힌다. `m_TextID`와 `m_NarrationID`가 모두 non-positive이면 커맨드는 텍스트를 열지 않는다. | 복원됨 | `C-BCMD-04` |
| `m_TextureChangeEnemyIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_TextureChangeTime` | `C-BCMD-02:16987028` `this->m_TextureChangeTime = 0.0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_UniqueProcess` | `C-BCMD-02:16987038` `*(_QWORD *)&this->m_UniqueProcess = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_isRewriteIdleMotion` | `C-BCMD-02:16987025` `this->m_isRewriteIdleMotion = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |
| `m_isSwitchActionSkip` | 제공된 C 내보내기에서 정확한 명명 필드나 명확한 오프셋 레인이 복원되지 않았다. | C 내보내기에서 정확한 `m_isSwitchActionSkip` 심볼은 복원되지 않았다. 별도 사용처 없이 proven 활성 필드로 설명하지 말아야 한다. | 부정적/부분 복원 | `C-BCMD-02` |
| `m_isWaitSkillTime` | `C-BCMD-02:16987023` `this->m_isWaitSkillTime = 0;` | 커맨드 행는 `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)`로 해석된다. 이 필드는 `FBattleEventCommandBase` 초기화에서 명명되었거나 이벤트 executor가 소비한다. Hex-Rays가 coalesce한 adjacent 필드는 사용처가 확인할 때만 paired 레인로 해석된다. | 복원됨 | `C-BCMD-01`, `C-BCMD-02` |

### ■ (*) `BattlePlaybackData` 및 `BattlePlayback`
이 section은 이름이 비슷한 세 가지를 더 엄격하게 분리해야 한다.

1. **`BattlePlaybacks`** 는 복원된 런타임 데이터베이스 테이블이다. 행 타입은 `FBattlePlaybacksBase`이며, 런타임은 `ADatabaseDefineStatic::m_BattlePlaybacks.m_DataList`를 통해 이를 읽는다.
2. **`FPlaybackData`** 는 DataBase 행가 아니다. save-데이터 layer와 `BattlePlaybacks` 테이블이 join된 뒤 `ABattlePlayback`이 사용하는 transient 런타임 객체다.
3. **`BattlePlaybackData`** 는 일부 애셋 내보내기에서 `m_EnemySkillID_01` 같은 필드와 함께 dump되는 것으로, C 내보내기에서는 활성 `ADatabaseDefineStatic` DB 인터페이스로 복원되지 않았다. 별도의 사용처가 발견되지 않는 한, 이러한 이상한 필드를 활성 `BattlePlaybacks` 테이블과 동일하게 취급하지 말아야 한다.

실질적인 결론은 전투 재생에서 의미 있는 modding 테이블이 `BattlePlaybacks`라는 점이다. 이 테이블은 재생 ID를 하나의 적 스킬, 선택 사항 적 수행자 슬롯, 효과 가시성 플래그에 매핑한다. party composition, 적 그룹, raid 플래그, guest ID, main-character appearance는 save/디버그 재생 데이터가 제공하며, 스킬/액션 payload는 그 뒤 `BattlePlaybacks`에서 얻어진다.

#### ■ `BattlePlaybacks`
활성 행는 다음과 같은 복원 구조를 갖는다.

| Field | C 행 오프셋 | Runtime role | Evidence grade |
|---|---:|---|---|
| `m_id` | `+0x08` | `FSaveDataBattlePlayback::Id`와 비교되는 키. | direct 행 스캔 |
| `m_VisibleAttackEffect` | `+0x0C` | `FPlaybackData::IsVisibleSkillEffect`로 복사되는 바이트 플래그. 이후 `AQPActionCommand::ProceedEffectiveOnlyLooks`로 전달된다. | 직접 게임플레이 사용처 |
| `m_EnemySkillID` | `+0x10` | `FPlaybackData::EnemyUseSkillID`로 복사되는 스킬 ID. 이후 `AQPActionCommand::SetupActionCommand`로 전달된다. | 직접 게임플레이 사용처 |
| `m_SkillPlayEnemyIndex` | `+0x14` | `FPlaybackData::SkillUseEnemyIndex`로 복사되는 선택 사항 적 슬롯 덮어쓰기. `-1`은 고정 행 지정 수행자 없음. | 직접 게임플레이 사용처 |

구조체 size는 24바이트다. SDK enum `EBATTLE_PLAYBACKS`는 stock 재생 ID를 명명하지만, 아래 표시된 principal 런타임 경로는 DB 배열를 linear 스캔하고 행 ID와 비교한다. 그 경로는 enum-domain switch가 아니다. 예외는 디버그 폴백 경로로, created save 재생이 없을 때 일부 IDs에 대한 hard-coded cases를 포함한다.

#### ■ `BattlePlaybackData`
다음과 같은 값을 가진 덤프된 `BattlePlaybackData` records는:

```json
"m_EnemySkillID_01": "m_EnemySkillID_01"
```

unresolved template/스키마 데이터로 계속 분류해야 한다. C 내보내기는 `BattlePlaybacksBase`, `BattlePlaybacks`, transient `PlaybackData`를 등록하지만, `m_EnemySkillID_01` / `m_EnemySkillID_02`에 대응하는 활성 `ADatabaseDefineStatic::GetBattlePlaybackData` 테이블은 노출하지 않는다. 또한 symbols에 `FEndCreditBattlePlayackData`라고 misspell된 별도의 end-credit 구조체가 있지만, 이는 여기서 설명하는 전투 재생 DB가 아니라 end-credit manager 데이터 structure다.

#### ■ 런타임 읽기 의미론: 컴파일된 구조체 및 테이블 인터페이스

compiled registration 데이터는 `BattlePlaybacksBase`와 `BattlePlaybacks`를 `BattlePlaybacks`의 DB structs로 식별한다. 별도로, `ABattlePlayback`은 transient `FPlaybackData` 구조체를 owns한다. 테이블 행와 런타임 액션 객체가 같은 객체가 아니기 때문에 이 구분이 중요하다.

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

해석: 활성 전투 재생 DB 인터페이스는 multi-column `BattlePlaybackData` 테이블이 아니라 `BattlePlaybacks`다. end-credit 구조체는 별도의 UI/ending subsystem artifact이며, 그것의 존재가 이상한 덤프된 `m_EnemySkillID_01` 테이블을 전투-런타임 input으로 입증하지는 않는다.

#### ■ 런타임 읽기 의미론: `FBattlePlaybacksBase` 행 레이아웃

`FBattlePlaybacksBase`는 compact 24-바이트 행로 constructed/복사됨된다. 복사 routine은 활성 payload 레인를 증명한다. `+0x08`의 행 ID, `+0x0C`의 visibility 바이트, `+0x10`의 적 스킬 ID, `+0x14`의 수행자 인덱스다.

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

해석: `m_VisibleAttackEffect`는 정수 ID가 아니라 바이트 레인이다. `m_EnemySkillID`와 `m_SkillPlayEnemyIndex`는 32-bit 레인다. 이 행 레이아웃에는 `m_EnemySkillID_01` 또는 `m_EnemySkillID_02`에 대응하는 항목이 없다.

#### ■ 런타임 읽기 의미론: 정적 DB 접근 및 로드 상태 검사

정적 DB class는 full `BattlePlaybacks` 래퍼와 데이터 목록 배열를 모두 expose한다. 전투 재생 actor 자체는 `GetBattlePlaybacksDB`를 사용하며, 이는 `m_BattlePlaybacks.m_Loaded`가 true가 되기 전에는 데이터 포인터 반환을 거부한다.

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

해석: 이 테이블은 `FBattlePlaybacksBase`의 데이터 목록 배열를 가진 일반 loaded DB asset이다. 확장은 `EBATTLE_PLAYBACKS` enum definition 자체가 아니라 DB/인덱스 machinery와 재생 ID를 사용하는 callers에 의해 제한된다. 재생 save/디버그 caller가 새 행 ID를 공급하고 테이블이 loaded되어 있다면, 이 경로는 새 행 ID를 스캔할 수 있다.

#### ■ 런타임 읽기 의미론: 저장된 재생 데이터 plus 테이블 행 결합

`ABattlePlayback::CreatePlaybackData`는 두 source를 join하여 `FPlaybackData` 객체를 구성한다. party/그룹/appearance 값는 `FSaveDataBattlePlayback`에서 복사하지만, 액션 payload는 matching `BattlePlaybacks` 행에서 얻는다.

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

해석: dataflow는 `FSaveDataBattlePlayback::Id -> BattlePlaybacks.m_id -> FPlaybackData.{EnemyUseSkillID, SkillUseEnemyIndex, IsVisibleSkillEffect}`다. 따라서 `m_EnemySkillID`는 save 재생 레코드에 저장되지 않는다. save 재생 ID를 DB 키로 `BattlePlaybacks`에 사용하여 복원된다.

#### ■ 런타임 읽기 의미론: 요청/프리로드 단계

재생 datum이 constructed되면, `RequestBattlePlayback`은 이를 actor에 복사하고, raid-vs-normal environment visibility를 설정하고, 적 그룹을 `AQPBattleManager`에 쓰고, `(EnemyGroupID, EnemyUseSkillID)`에 대한 효과를 preload하고, 그룹의 적 flipbooks를 request한 뒤 main-character texture를 구축한다.

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

해석: `m_EnemySkillID`는 액션 execution 전에 resource preparation에 참여한다. 그러므로 재생 행는 커맨드가 실행되기 전이라도, 선택된 적 그룹 context 안에서 스킬 ID가 필요한 효과/animation preloading을 지원하지 못하면 의미상 실패할 수 있다.

#### ■ 런타임 읽기 의미론: 수행자 선택 및 스킬 검증

`PlayAction`은 `m_SkillPlayEnemyIndex`가 valid 적-배열 인덱스일 때 먼저 이를 따른다. 행가 valid 슬롯을 지정하지 않으면, 함수는 선택된 적 그룹에서 택티컬 스킬 목록에 `m_EnemySkillID`를 포함하는 enemies를 검색한다. 현재 boss가 그 스킬을 사용할 수 있으면 우선된다. 그렇지 않으면 첫 matching 적 ID가 다시 그룹 슬롯으로 변환되어 수행자로 사용된다. 그 후에야 code가 `AQPActionCommand::SetupActionCommand`를 issue한다.

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

해석: `m_SkillPlayEnemyIndex`는 구체적 적 객체를 직접 선택할 수 있기 때문에 택티컬-스킬 소속 관계 search보다 더 강하다. valid explicit 인덱스가 없으면 `m_EnemySkillID`는 preview할 액션일 뿐 아니라 plausible 적 수행자를 고르는 constraint로도 취급된다. 이는 재생 행가 보통 `m_EnemyGroupID`의 적어도 한 적가 사용할 수 있는 스킬을 사용해야 함을 의미한다. 슬롯 덮어쓰기가 valid하고 시각 커맨드가 mismatch를 tolerate한다고 알려진 경우는 예외다.

#### ■ 런타임 읽기 의미론: 효과 가시성 플래그

visibility 플래그는 단순히 복사되는 것만이 아니다. 재생 tick 동안 액션-커맨드 시각 전용 progression function으로 전달된다.

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

해석: 이 플래그는 액션 커맨드가 advanced되는 동안 시각/효과 표현을 제어한다. 따라서 source 이름 `m_VisibleAttackEffect`는 방향상 맞지만, C 사용처는 런타임 필드를 `IsVisibleSkillEffect`라고 부른다. 타이밍 또는 스킬 선택 매개변수가 아니라 액션 효과 visibility switch로 설명해야 한다.

#### ■ 런타임 읽기 의미론: 디버그 UI 및 임시 객체 레이아웃

Debug widget은 `FPlaybackData` records의 map을 사용한다. extraction 경로는 transient 객체 offsets를 확인한다. `EnemyUseSkillID`는 `+0x58`, `SkillUseEnemyIndex`는 `+0x5C`, `IsVisibleSkillEffect`는 `+0x60`, `BossIndex`는 `+0x64`다.

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

해석: `FPlaybackData`는 104바이트이며 save 재생 상태 뒤에 액션 preview 필드를 포함한다. 이 transient structure는 execution을 이해하는 데 유용하지만 데이터베이스 테이블 행로 착각해서는 안 된다.

#### ■ 필드 단위 해석

| 키 | 런타임 C 위치 | 정확한 해석 | 상태 |
|---|---|---|---|
| `m_EnemySkillID` | `FBattlePlaybacksBase +0x10`; `CreatePlaybackData`가 복사함; `BattlePlaybackPreloadEffectAsync`, `UEnemyDB::HasTacticalSkill`, `AQPActionCommand::SetupActionCommand`가 소비함. | 재생에서 시각적으로 실행할 스킬 ID. `SkillID` namespace ID이지만, 액션 수행자 해석은 여전히 선택된 적 그룹과 선택 사항 슬롯 덮어쓰기에 의존한다. | 복원됨, 직접 사용처 |
| `m_SkillPlayEnemyIndex` | `FBattlePlaybacksBase +0x14`; `FPlaybackData::SkillUseEnemyIndex`로 복사됨; `m_Enemies.ArrayNum`과 검사됨. | 선택 사항 적 슬롯 덮어쓰기. 0보다 작거나 활성 적 배열 밖의 값은 택티컬-스킬 소속 관계에 의한 수행자 discovery로 fall back한다. | 복원됨, 직접 사용처 |
| `m_VisibleAttackEffect` | `FBattlePlaybacksBase +0x0C`; `FPlaybackData::IsVisibleSkillEffect`로 복사됨; `ProceedEffectiveOnlyLooks`로 전달됨. | 불리언식 액션 효과 visibility switch. 런타임에서 C 이름은 `IsVisibleSkillEffect`이므로, 시각 전용 액션 재생 중 스킬/효과 표현을 제어한다고 설명해야 한다. | 복원됨, 직접 사용처 |
| `m_EnemySkillID_01` | 복원된 `BattlePlaybacksBase` 레인 없음; 복원된 `ADatabaseDefineStatic::GetBattlePlaybackData` 인터페이스 없음. | 활성 전투 재생 런타임 데이터로 증명되지 않았다. unresolved 덤프된 스키마/template material로 취급한다. | 부정적 / unresolved |
| `m_EnemySkillID_02` | `m_EnemySkillID_01`와 동일. | 활성 전투 재생 런타임 데이터로 증명되지 않았다. | 부정적 / unresolved |

#### ■ 작성 시 유의점

- valid `BattlePlaybacks` 행는 `m_EnemySkillID`를 행가 intended한 `m_EnemyGroupID` context와 pair해야 한다. 런타임이 해당 그룹에서 requested 스킬을 포함하는 택티컬 스킬 set을 가진 적를 검색하기 때문이다.
- 시각 수행자가 deterministic해야 할 때는 `m_SkillPlayEnemyIndex`를 사용한다. 그렇지 않으면 런타임은 matching 현재 boss를 선택하거나, 해당 스킬을 가진 `EnemyID`의 first 그룹 적를 선택할 수 있다.
- `m_VisibleAttackEffect = false`는 스킬 선택을 suppress하지 않는다. 액션 커맨드가 이미 created된 뒤 시각 progression 경로에 영향을 준다.
- `EBATTLE_PLAYBACKS` enum은 vanilla 행의 stock-이름 documentation으로 취급하는 것이 가장 좋다. recoverable DB 경로는 loaded 배열를 행 ID로 스캔하므로 enum 값에 본질적으로 제한되지 않는다. 다만 디버그 폴백 cases는 일부 stock ID에 hard-code되어 있다.



### ■ (*) BattleResultBonus
서로 다른 전투 result bonuses(“No Damage”, “Overkill”, “Break”, “1-Turn Kill” 등)의 ratios와 localization IDs를 추적한다. `m_Param`은 어떤 stats(XP, JP, money)가 bonus를 받는지 추적하는 것으로 보인다.
- 0: Money 
- 1: JP
- 2: XP
2를 넘는 unused 매개변수가 존재하는지는 미검증이다.

#### ■ 런타임 읽기 의미론
`BattleResultBonus` 행는 `m_Param`이 아니라 bonus 타입으로 선택된다. 복원된 gameplay 판정식는 `GetBattleResultBonusParam`이 행를 찾은 뒤, 행의 offset `+0x10` 임계값 레인만 읽는다.


【주요 C 증거】

##### ◆ C-BRESULT-01: BattleResultBonus 행 조회 by bonus 타입

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

##### ◆ C-BRESULT-02: bonus 판정식 read 행 오프셋 +0x10

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_Param` | `C-BRESULT-01:17542787-17542801`은 offset `+0x08`의 행 ID로 `m_BattleResultBonus.m_DataList`를 스캔한다.<br>`C-BRESULT-02:17549619-17549627`은 복원된 판정식 임계값에 대해 `BattleResultBonusParam[16]`만 읽는다. | 정확한 `m_Param` 필드는 복원된 런타임 경로의 조회 키가 아니다. 행는 `bonusType`을 행 오프셋 `+0x08`과 비교하여 선택된다. 그 뒤 `IsBattleResultBonus`는 전투 counter를 행 오프셋 `+0x10`과 비교한다. C 내보내기는 별도 `m_Param` 레인에 대한 애셋별 사용처를 식별하지 않으므로, Money/JP/XP 해석은 proven 판정식 behavior가 아니라 작성/스키마 가설로 남는다. | 부정적/부분 복원, 활성 테이블 증거 있음 | `C-BRESULT-01`, `C-BRESULT-02` |



#### ■ 심층 런타임 분석: `BattleResultBonus` 조건 임계값

기존 스키마 수준 description이 `m_Param`을 보상 채널 선택기로만 취급한다면 불완전하다. 적어도 하나의 direct 런타임 사용처는 행 값 at `+0x10`을 result bonus 조건이 satisfied되는지 결정하는 임계값로 읽는다.

##### ◆ C-BATTLERESULTBONUS-LOOKUP-01: result-bonus 행 해석기 및 24-바이트 stride

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

##### ◆ C-BATTLERESULTBONUS-THRESHOLD-02: 행 임계값는 `IsBattleResultBonus`에서 사용됨

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

해석: `bonusType`은 `+0x08`의 `m_id`로 행를 선택한다. `+0x10`의 정수는 타입별 임계값다. bonus 타입에 따라 “No Damage”의 damage taken, overdamage ratio, break count, turn count가 된다. 또 다른 내보낸 필드가 보상 channels나 UI 텍스트도 제어한다면 그것은 별도 레인이며, 여기 표시된 임계값 사용처와 혼동해서는 안 된다.

### ■ (**) `BattleVoiceList` 및 `BattleVoiceArenaList`
전투 중 party members 또는 narrator가 재생할 다양한 보이스 lines를 정의한다(예: Or'Galdera fight 중 special 보이스 lines).

관련 가능성:
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
#### ■ 런타임 analysis: `BattleVoiceList` 및 `BattleVoiceArenaList`
보이스 테이블는 런타임 활성지만, 이번 pass에서 복원된 strongest 직접 사용처는 아레나 덮어쓰기 경로다. 일반 전투 보이스 경로는 이미 구체화됨된 보이스 ID 목록를 받아, 적절할 때 아레나 replacement를 적용하고, repeat-limited 후보를 제거한 뒤 구체적 보이스 ID를 선택한다.

##### ◆ C-BATTLEVOICEARENA-LOOKUP-01: 아레나 보이스 행는 키 인덱스 방식이며 208-바이트 stride를 가짐

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

##### ◆ C-BATTLEVOICEARENA-CANDIDATES-02: 아레나 행는 후보 보이스 ID 목록를 덮어쓰기할 수 있음

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

해석: `BattleVoiceArenaList`는 ordinary 전투-보이스 후보 목록 위의 overlay다. 전체 보이스-선택 subsystem을 대체하는 것이 아니라, 전투이 아레나 전투로 인식될 때 후보를 conditionally substitute/필터한다.

### ■ (***) BattleAbortConditions
OT0의 전투 termination/forced-outcome 조건 테이블이다. 각 entry는 fight 중 충족되면 전투을 abort(조기 종료)시키거나, `EnemyGroups`에서 어떤 필드가 참조하느냐에 따라 forced win/forced lose로 처리하게 하는 작은 판정식 bundle을 정의한다.
- `m_Conditions`: 조건 타입 IDs(`TacticalList`, `TacticalActionList`, `SkillConditionList`, `BattleEventList`와 같은 숫자 조건 namespace를 공유)
- `m_Params`: 각 조건의 매개변수(의미는 조건 타입에 따라 달라짐)
- 특정 조건 타입에만 의미 있는 필터:
   - `m_AilmentTypes`(`SkillAilmentType.m_id`를 input으로 받음)
   - `m_StatusTypes`(`CharaStatusID.m_id`를 input으로 받을 수 있음)
   - `m_WeaponTypes`(`WeaponType.m_id`를 input으로 받음)
   - `m_MagicTypes`(`Magictype.m_id`를 input으로 받음)
   - `m_EnemyID`(`EnemyID.m_id`를 input으로 받음)

#### ■ 런타임 증거 통합 노트
- `Octopath_Traveler0-Win64-Shipping.exe.c`의 decomp-only anchor:
    - `AQPBattleManager::CheckBattleAbort`는 관련 `BattleAbortConditions` 행를 해석하고, 행의 seventh 필터 배열(`v17 + 7`, 즉 `m_EnemyID` 레인)를 `UBattleUtility::CheckConditions`에 전달한다. `CheckConditions`는 이 레인을 `EnemyID` argument로 `UBattleUtility::CheckCondition`에 forwarding한다. 이는 이 필드가 단순히 복사됨 스키마 슬롯이 아니라 활성 조건 필터임을 증명한다(`Octopath_Traveler0-Win64-Shipping.exe.c`:17493321-17493429, 17493527-17493542, and 17389535-17389666).
- exact IDA anchors:
    - `0x144373530` `ADatabaseDefineStatic::execGetBattleAbortConditions`: 테이블의 named 런타임 접근 함수.
#### ■ 런타임 읽기 의미론
【주요 C 증거】

##### ◆ C-GROUP-02: EnemyGroups 구체화 처리 with 구체적 행 오프셋

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

##### ◆ C-BEVENT-03: Shared 전투-조건 tuple 평가기

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

##### ◆ C-BABORT-01: BattleAbortConditions 구조체 복사 of seven 조건/필터 레인

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_AilmentTypes` | `ailment lane`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `BattleAbortConditions` 행는 seven 병렬 `TArray<int>` 레인로 복사된다. 공유 `UBattleUtility::CheckConditions` 평가기는 조건, 매개변수, 상태, 상태 이상, 무기, 마법, 적 ID 레인를 슬롯별로 소비한다. missing 슬롯는 zero로 default된다. | 복원됨 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Conditions` | `condition lane`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `BattleAbortConditions` 행는 seven 병렬 `TArray<int>` 레인로 복사된다. 공유 `UBattleUtility::CheckConditions` 평가기는 조건, 매개변수, 상태, 상태 이상, 무기, 마법, 적 ID 레인를 슬롯별로 소비한다. missing 슬롯는 zero로 default된다. | 복원됨 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_EnemyID` | `enemy-id lane`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `BattleAbortConditions` 행는 seven 병렬 `TArray<int>` 레인로 복사된다. 공유 `UBattleUtility::CheckConditions` 평가기는 조건, 매개변수, 상태, 상태 이상, 무기, 마법, 적 ID 레인를 슬롯별로 소비한다. missing 슬롯는 zero로 default된다. | 복원됨 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_MagicTypes` | `magic lane`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `BattleAbortConditions` 행는 seven 병렬 `TArray<int>` 레인로 복사된다. 공유 `UBattleUtility::CheckConditions` 평가기는 조건, 매개변수, 상태, 상태 이상, 무기, 마법, 적 ID 레인를 슬롯별로 소비한다. missing 슬롯는 zero로 default된다. | 복원됨 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Params` | `parameter lane`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `BattleAbortConditions` 행는 seven 병렬 `TArray<int>` 레인로 복사된다. 공유 `UBattleUtility::CheckConditions` 평가기는 조건, 매개변수, 상태, 상태 이상, 무기, 마법, 적 ID 레인를 슬롯별로 소비한다. missing 슬롯는 zero로 default된다. | 복원됨 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_StatusTypes` | `status lane`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `BattleAbortConditions` 행는 seven 병렬 `TArray<int>` 레인로 복사된다. 공유 `UBattleUtility::CheckConditions` 평가기는 조건, 매개변수, 상태, 상태 이상, 무기, 마법, 적 ID 레인를 슬롯별로 소비한다. missing 슬롯는 zero로 default된다. | 복원됨 | `C-BABORT-01`, `C-BEVENT-03` |
| `m_WeaponTypes` | `weapon lane`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | `BattleAbortConditions` 행는 seven 병렬 `TArray<int>` 레인로 복사된다. 공유 `UBattleUtility::CheckConditions` 평가기는 조건, 매개변수, 상태, 상태 이상, 무기, 마법, 적 ID 레인를 슬롯별로 소비한다. missing 슬롯는 zero로 default된다. | 복원됨 | `C-BABORT-01`, `C-BEVENT-03` |

### ■ (*) `BattleActionID`
“액션 ID”(아마 `SkillAilmentType.m_ActionID`)를 구체적 전투 animation/motion에 매핑하는 작은 조회 테이블이며, 해당 animation을 standard 전투 rig 대신 NPC/필드 character rig로 재생해야 하는지 알려 주는 플래그를 포함한다. `m_UseNPC`는 이 액션을 usual 전투-sprite가 아니라 NPC character animation set으로 재생해야 하는지 결정할 수 있다.
관련 가능성: `Kingship_structs.hpp`는 일부 액션 IDs에 다음 labels를 부여한다.
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
#### ■ 런타임 읽기 의미론
`BattleActionID` 행는 단순한 label 테이블이 아니다. player-character 전투-액션 경로는 `ActionID`로 행를 해석한 뒤 바이트 offset `+0x10`을 `m_UseNPC` 게이트로 읽는다. 이 게이트는 `m_bNpcFlag`가 set된 character objects에 대해서만 평가된다.

【주요 C 증거】

##### ◆ C-BACTION-01: BattleActionID 행 해석 by ActionID

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

##### ◆ C-BACTION-02: m_UseNPC as the NPC-only 액션 게이트

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_UseNPC` | `C-BACTION-02:17577515` `ActionDB = AQPCharacterBattlePlayer::GetActionDB(this, ActionID);`<br>`C-BACTION-02:17577516` `if ( !ActionDB || !*(_BYTE *)&ActionDB[16] )`<br>`C-BACTION-01:17579948` `m_BattleActionID.m_DBAccess`<br>`C-BACTION-01:17579958` `Data[24 * ArrayIndex]` | `m_UseNPC`는 24-바이트 `BattleActionID` 행의 offset `+0x10`에 있는 바이트다. `AQPCharacterBattlePlayer::ExecPlayBattleAction`에서 이 필드는 `this->m_bNpcFlag`가 true일 때만 테스트된다. 행가 없거나 이 바이트가 zero이면, requested 액션 ID는 animation 해석 전에 `1`로 coerced된다. 즉 NPC/필드-rig character는 idle 액션으로 폴백한다. Non-NPC player characters는 인용 함수에서 이 guard 경로를 타지 않는다. | 복원됨 | `C-BACTION-01`, `C-BACTION-02` |

### ■ (*) BattleParams
Dumper-7 SDK의 `Kingship_structs.hpp`에 따르면(BravelyPath Modular Discord의 #file-dump 참조), 여러 전투-related 매개변수의 의미는 다음과 같다.
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
#### ■ 런타임 읽기 의미론
`BattleParams`는 two-stage mechanism으로 읽힌다. 전투-manager initialization/use 시 데이터베이스 행는 `m_AllBattleParamList`로 normalized된다. 이후 런타임 call sites는 `EBATTLE_PARAMS` 숫자 값를 캐시됨 vector에 대한 unsigned-바이트 인덱스로 사용한다. 캐시는 같은 authoring 값의 네 가지 타입 views, 즉 문자열, 정수, 부동소수점, boolean을 저장한다.

【주요 C 증거】

##### ◆ C-BPARAM-01: BattleParams 행 are 복사됨 from the 데이터베이스 테이블

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

##### ◆ C-BPARAM-02: 숫자 문자열 conversion into 캐시됨 int/부동소수점/불리언 슬롯

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

##### ◆ C-BPARAM-03: EBATTLE_PARAMS ID indexes m_AllBattleParamList

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

##### ◆ C-BPARAM-04: 부동소수점 접근 함수 reads the 캐시됨 부동소수점 슬롯

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

##### ◆ C-BPARAM-05: 정수 접근 함수 reads the 캐시됨 정수 슬롯

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

**런타임 해석:**

| 런타임 요소 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `EBATTLE_PARAMS` ID / 행 ID | `C-BPARAM-01:17537220` `v13 = *(_DWORD *)&v12[-8];`<br>`C-BPARAM-03:17542742` `if ( battleParam < this->m_AllBattleParamList.ArrayNum )` | 데이터베이스 행 ID는 `m_AllBattleParamList`에 대한 dense 인덱스로 사용된다. 따라서 guide의 enum constants는 descriptive-only labels가 아니라 런타임 키다. | 복원됨 | `C-BPARAM-01`, `C-BPARAM-03` |
| 문자열 값 | `C-BPARAM-01:17537225` `v16 = *(int *)&v12[8];`<br>`C-BPARAM-01:17537226` `v17 = *(const wchar_t **)v12;` | source 테이블 값는 데이터베이스-행 문자열 레인의 문자열-like `FString` payload로 저장된다. 캐시됨 `BattleParam.StringValue`로 복사된다. | 복원됨 | `C-BPARAM-01` |
| 정수 값 | `C-BPARAM-02:17537302` `*(_DWORD *)&v21[8 * v22 + 24] = *(_DWORD *)&battleParam.BoolValue;`<br>`C-BPARAM-05:17395117` `v1 = *(_DWORD *)&v5[24];` | caching 중 숫자 strings는 `_wtoi`로 parse된다. 캐시됨 정수 접근 함수는 그 뒤 캐시됨 `BattleParam` 레코드의 offset `+0x18`을 읽는다. | 복원됨 | `C-BPARAM-02`, `C-BPARAM-05` |
| 부동소수점 값 | `C-BPARAM-02:17537274` `v19 = _wtof(v18);`<br>`C-BPARAM-02:17537303` `*(_DWORD *)&v21[8 * v22 + 28] = *((_DWORD *)&battleParam.BoolValue + 1);`<br>`C-BPARAM-04:17395003` `v3 = *(float *)&v5[28];` | caching 중 숫자 strings는 `_wtof`로 parse된다. 캐시됨 부동소수점 접근 함수는 그 뒤 캐시됨 `BattleParam` 레코드의 offset `+0x1C`를 읽는다. | 복원됨 | `C-BPARAM-02`, `C-BPARAM-04` |
| boolean 값 | `C-BPARAM-02:17537281` `v20 = FString::ToBool((FString *)v12);`<br>`C-BPARAM-02:17537304` `v21[8 * v22 + 32] = (FScriptContainerElement)v36;` | boolean 뷰는 캐시 construction 중 `FString::ToBool`로 얻어지며 캐시됨 offset `+0x20`에 저장된다. | 복원됨 | `C-BPARAM-02` |
#### ■ `BattleParams` caching 및 타입 지정 접근 함수
`BattleParams`는 매번 데이터베이스에서 ad hoc으로 읽히지 않는다. 전투 manager는 먼저 모든 행를 `EBATTLE_PARAMS`로 인덱스되는 타입 지정 런타임 배열에 캐시하고, later helpers는 fixed offsets에서 캐시됨 문자열/int/부동소수점/불리언 views를 읽는다. source 데이터가 DB 테이블임에도 enum-like ID 목록가 운영상 중요한 이유가 바로 이것이다.

##### ◆ C-BATTLEPARAMS-CACHE-BUILD-01: 행 값 are 캐시됨 by 매개변수 ID

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

##### ◆ C-BATTLEPARAMS-CACHE-INDEX-02: 전투-param ID 인덱스 the 런타임 캐시 directly

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

##### ◆ C-BATTLEPARAMS-TYPED-WRAPPERS-03: 타입 지정 래퍼 expose 캐시됨 필드

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

해석: `BattleParams` 행는 여러 타입 지정 representations로 parse된다. raw 데이터베이스 값는 문자열-like이지만, 런타임 캐시는 캐시됨 `BattleParam` entry의 `+0x18`, `+0x1C`, `+0x20`에서 정수, 부동소수점, boolean views를 expose한다. 따라서 새 매개변수 ID를 추가하는 것은 단순히 행를 추가하는 문제가 아니다. caller code가 해당 ID를 request해야 하며, 캐시가 숫자 ID를 담을 만큼 충분히 커야 한다.


### ■ (*) `BattleDeathBlowPoint`
“Death Blow”(special attack) 게이지 gain용 조회 테이블이다. 각 행는 본질적으로 특정 검사 조건이 충족되는지(타입 + 값), 그리고 Death Blow 게이지를 얼마나 증가시킬지를 정의한다.
- `m_CheckType`: 무엇을 검사하는지의 종류.
- `m_CheckValue`: 해당 검사 타입의 임계값/amount. count로 보인다.
- `m_IncreasedPoint`: 해당 검사가 match될 때 추가할 “Death Blow points” 수.

#### ■ 런타임 읽기 의미론
`BattleDeathBlowPoint`는 `AQPBattleManager::CheckSpecialSkillPoint`가 소비한다. decompilation은 hot loop에서 필드 names를 보존하지 않지만, 복사됨 행 스트라이드와 offset usage가 세 payload 필드를 명확하게 만든다. offset `+0x0C`는 검사 타입 선택기, offset `+0x10`은 검사 값, offset `+0x14`는 point increment다.

【주요 C 증거】

##### ◆ C-BDEATH-01: BattleDeathBlowPoint 테이블 복사 and 24-바이트 행 스트라이드

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

##### ◆ C-BDEATH-02: 검사 타입 디스패치 and use of +0x10/+0x14

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

##### ◆ C-BDEATH-03: 적 측 임계값 branch and 최종 rise-point storage

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_CheckType` | `C-BDEATH-01:17515519-17515523` copies source offsets `+0x0C/+0x10/+0x14` into a local 24-바이트 행 복사.<br>`C-BDEATH-02:17515786` `v56 = *(_DWORD *)&v52[12];`<br>`C-BDEATH-02:17515793` `if ( (unsigned __int8)v56 == 1 )`<br>`C-BDEATH-02:17515795` `if ( (unsigned __int8)v56 == 2 )` plus 타입 `3`, `4`, and `5` branches later in the same block. | `m_CheckType`은 행 오프셋 `+0x0C`의 32-bit 레인에 저장되지만, 평가기는 의미상 그 low 바이트로 디스패치한다. 복원된 cases는 C 내보내기에서 enum-named가 아니라 branch-coded다. 타입 `1`은 적 측 projected-damage 임계값 경로로 진입한다. 타입 `2`는 reserved damage가 있는 guarded targets를 검사한다. 타입 `3`은 non-적 hit/boost/무기 경로다. 타입 `4`는 additional guard/support/아이템 exclusions가 있는 non-적 hit/boost/non-무기 스킬 경로다. 타입 `5`는 newly broken targets를 count한다. | 복원됨 | `C-BDEATH-01`, `C-BDEATH-02`, `C-BDEATH-03` |
| `m_CheckValue` | `C-BDEATH-02:17515784` `v54 = *(_DWORD *)&v52[16];`<br>`C-BDEATH-02:17515991` `if ( v50->m_UseBoostLevel == v54 && v53 )`<br>`C-BDEATH-03:17516102` `v91 = (int)(float)((float)((float)v54 * 0.0099999998) * (float)v90);` | `m_CheckValue`는 행 오프셋 `+0x10`이다. 의미는 타입-dependent다. 타입-3/타입-4 player-side branches에서는 `m_UseBoostLevel`과 비교되고, 타입-1 적 측 branch에서는 target의 캐시됨 maximum HP에 대한 percentage 임계값로 해석된다. | 복원됨 | `C-BDEATH-02`, `C-BDEATH-03` |
| `m_IncreasedPoint` | `C-BDEATH-02:17515785` `v55 = *(_DWORD *)&v52[20];`<br>`C-BDEATH-02:17515822` `risePoint[...] = v55;`<br>`C-BDEATH-02:17515955` `v64 = v58 * v55;`<br>`C-BDEATH-03:17516143` `risePoint[...] = InOutElementIndex;` | `m_IncreasedPoint`는 행 오프셋 `+0x14`다. temporary `risePoint` 배열에 쓰이는 기본 게이지 increment다. 타입 `5`에서는 code가 이 값을 newly broken targets 수와 곱한다. 최종 increment는 이후 현재 special-스킬-게이지 레벨 bonus와 `DEATHBLOWPOINT_UP` support ability로 scale된 뒤 `AddSpecialSkillGaugeValue`가 호출된다. | 복원됨 | `C-BDEATH-02`, `C-BDEATH-03` |



#### ■ 심층 런타임 분석: `BattleDeathBlowPoint` 디스패치 및 게이지 변동

`BattleDeathBlowPoint`는 활성 combat 테이블이다. 런타임은 테이블을 복사하고, 각 24-바이트 행를 iterate하며, 검사 타입의 low 바이트를 디스패치 선택기로 interpret한 뒤 후보 게이지 increases를 `risePoint`에 append한다. 같은 function 뒤쪽에서 각 nonzero rise point는 전투 매개변수와 support abilities로 scale된 뒤 `AddSpecialSkillGaugeValue`가 player 게이지를 mutate한다.

##### ◆ C-BATTLEDEATHBLOW-WORKLIST-01: the 테이블 is 복사됨 into an invoke-time work 목록

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

##### ◆ C-BATTLEDEATHBLOW-ROW-FIELDS-02: each 행 supplies 검사 타입, 검사 값, and increased point

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

##### ◆ C-BATTLEDEATHBLOW-CHECK-BRANCHES-03: break, boosted 액션, and damage-임계값 branches feed 게이지 gain

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

##### ◆ C-BATTLEDEATHBLOW-GAUGE-APPLY-04: 최종 point application scales and 변동시킴 the special-스킬 게이지

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

해석: 검사 타입 names가 C 내보내기에서 anonymous로 남아 있는 곳에서도 복원된 필드 roles는 강하다. `m_CheckType`은 행 `+0x0C`의 디스패치 선택기, `m_CheckValue`는 `+0x10`의 comparison/percentage/boost-레벨 값, `m_IncreasedPoint`는 `+0x14`의 기본 point payload다. 디스패치 branches는 generic 임계값 테이블이 아니며 guard damage, boost 레벨, just-breaking targets, reserved-damage 임계값 같은 구체적 combat states에 bind된다.

### ■ (*) `BattleLayoutSet`
카메라 preset과 적 측 positioning을 함께 묶는 작은 전투 staging/레이아웃 preset 테이블일 가능성이 높다. 게임에서 어디에 쓰이는지는 명확하지 않다(예컨대 formation이나 reinforcements 중 `PositionID` 270을 사용하는 것은 없어 보인다).

`Kingship_structs.hpp`는 일부 레이아웃 sets에 다음 labels를 부여한다.

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
#### ■ 런타임 읽기 의미론
C 내보내기는 `BattleLayoutSet`이 raid-뷰 카메라 경로에서 활성 런타임 데이터임을 증명한다. 레이아웃 ID를 통해 데이터베이스 접근 helper로 해석되며, 행는 최소한 `m_CameraParamID`와 `m_EnemyPosition`을 포함하는 `FBattleLayoutSetBase`로 소비된다.

【주요 C 증거】

##### ◆ C-BLAYOUT-01: BattleLayoutSet 행 조회

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

##### ◆ C-BLAYOUT-02: m_CameraParamID and m_EnemyPosition 소비됨 by 레이드 뷰

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

##### ◆ C-BLAYOUT-03: 레이아웃 ID 1, 0, and caller-supplied offsets in UpdateRaidView

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

【필드 단위 해석】

| Key / 행 필드 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_id` / 레이아웃 ID | `C-BLAYOUT-01:17542710` `if ( layoutID <= 0 )`<br>`C-BLAYOUT-01:17542720` `m_BattleLayoutSet.m_DBAccess`<br>`C-BLAYOUT-01:17542729` `Data[24 * ArrayIndex]` | 레이아웃 ID는 `m_BattleLayoutSet.m_DBAccess`를 통해 해석된다. ID `0`은 조회 함수에서 rejected된다. 반환 행 스트라이드는 24 bytes다. | 복원됨 | `C-BLAYOUT-01` |
| `m_CameraParamID` | `C-BLAYOUT-02:17510719` `m_CameraParamID = battleLayout->m_CameraParamID;`<br>`C-BLAYOUT-02:17510721` `if ( m_CameraParamID <= 0 ) return 0;`<br>`C-BLAYOUT-02:17510778` `StartFreeMove(..., m_CameraParamID, ...)` | `m_CameraParamID`는 레이아웃이 선택하는 primary 카메라 preset이다. non-positive이면 update가 실패한다. non-raid-offset 모드에서는 `ABattleCameraBase::StartFreeMove`에 직접 전달되고, raid-offset 모드에서는 second 레이아웃의 카메라 ID가 arc offset ID로 사용된다. | 복원됨 | `C-BLAYOUT-02` |
| `m_EnemyPosition` | `C-BLAYOUT-02:17510747` `m_EnemyPosition = offset->m_EnemyPosition;`<br>`C-BLAYOUT-02:17510759` `if ( m_EnemyPosition > 0 )`<br>`C-BLAYOUT-02:17510762` `UEnemyDB::GetBattlePosition(&Position, m_EnemyPosition)` | `m_EnemyPosition`은 offset 레이아웃에서 읽은 선택 사항 전투-위치 ID다. positive이면 `UEnemyDB::GetBattlePosition`으로 해석되어 raid 적 offset 트랜스폼에 저장된다. 이는 inferred formation reference가 아니라 direct 런타임 use다. | 복원됨 | `C-BLAYOUT-02` |

## ■ `AIBattle` 아래
이 파일들은 적 AI 행동과 적이 사용하는 스킬(및 그 방식)를 담당한다.

적 AI에 대해 engine은 다음을 읽는다.

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
OT0의 적 AI “rule clause” 테이블이다. 각 entry는 조건 집합을 정의하며, 그 조건이 satisfied되면 AI가 어떤 behavior를 select 또는 enable하게 한다. enemies는 `TacticalAssignList` 파일에서 이 “rules”를 할당받으며, 그 `m_id`는 `EnemyID.m_TacticalAssignID`에 대응한다. 전투 스킬는 `TacticalSkillList`를 통해 할당되고, 그 `m_id`는 `EnemyID.m_SkillsID`에 대응한다.
- `m_Presage`: 이 tactic이 presage(telegraphed 액션)를 표시하게 하는지 나타내는 enum일 가능성이 높다.
- `m_PresageSkillID`: presage와 연결된 스킬 ID(telegraph되고 있는 것). 예를 들어 boss가 boost 모드를 announce할 때 사용된다. 이 스킬 IDs는 특히 적의 `TacticalSkillList.m_UseSkills` 배열에 **존재하지 않는다**.
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: 이벤트 플래그 gating 및 side 효과. `BattleEventCommand` 참조. AI behavior의 branching/sequencing에 사용될 가능성이 높다.
- `m_Conditions`: `TacticalActionList`, `BattleEventList`, `SkillConditionList`와 같은 조건일 가능성이 높으며, 같은 `m_Params`와 필터 배열를 사용한다.
   - `m_AilmentTypes`(`SkillAilmentType.m_id`를 input으로 받음)
   - `m_StatusTypes`(`CharaStatusID.m_id`를 input으로 받을 수 있음)
   - `m_WeaponTypes`(`WeaponType.m_id`를 input으로 받음)
   - `m_MagicTypes`(`Magictype.m_id`를 input으로 받음)

각 조건의 의미는 아직 완전히 명명되지 않았다. local [Condition Crossref Atlas](Octopath_Condition_Crossref_Atlas.md)를 현재 guide-bundle inventory로 사용하고, 해당 atlas가 shape, source 테이블, 매개변수 레인만 증명하는 곳에서는 조건 IDs를 unresolved로 둔다. `../raw_evidence/` 아래 imported CotCPort snapshot은 historical source evidence이지, 현재 OT0 v1.0.8 names의 authority가 아니다.

#### ■ 런타임 증거 통합 메모
기존 address-레벨 택티컬 notes는 아래 named `UTacticalDB`, `UBattleTacticalComponent`, `UBattleUtility` evidence에 통합되었다. C 내보내기가 이제 타입 지정 행 접근 함수와 named 조건-평가 calls를 제공하므로, 이 section은 address가 quoted C excerpt에 inherent한 경우를 제외하고 older `sub_...` naming을 피한다. Condition ID는 여전히 스키마 names만이 아니라 cited 평가기 paths로 해석해야 한다.

### ■ (****) `TacticalActionList`
tactic이 적용될 때 game이 실제로 무엇을 할지 알려 주는 것으로 보인다. 어떤 스킬을 사용할지(인덱스 기준), 누구를 target할지, 가능한 targets 중 어떻게 select할지, additional 조건/priority rules를 지정한다.
- `m_SkillIndex`: 이것 자체는 SkillID가 아니다. 스킬 목록/배열에 대한 인덱스다(테스트상 적의 `m_SkillsID`에 대한 `TacticalSkillList.m_UseSkills[]`).
- `m_SelectType`: 선택 모드 enum. `Kingship_structs.hpp`는 다음을 정의한다.

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

- `m_FriendlyIndex`: “friendly” side에서 누구를 target할지(적 관점에서?). 여기서 `-1`은 “fixed friendly 없음; 아마 `SelectType`에 따라 선택(?)”을 뜻할 가능성이 높다.
- `m_PrioritySkill`: (미검증) 설정되면 normal 선택을 덮어쓰기하고 특정 스킬 인덱스를 force하거나, 스킬 인덱스를 preferred로 mark할 수 있다(?)
- `m_TurnCount`: (미검증) specified 택틱스 ID의 조건가 met된 경우, 이 키에 주어진 값 `N`마다 액션을 repeat한다. SelectType에 따라 다른 의미일 수 있다.
- `m_GroupIndex`: grouping 키. phase별 액션 분리, turn당 그룹별 액션 수 제한, 또는 그룹 내 multi-적 behavior coordination에 쓰일 가능성이 높다.
- Additional 액션-레벨 조건(`TacticalList`와 비슷하지만 specified 액션에 특화): `m_Conditions`, `m_Params`, `m_AilmentTypes`, `m_StatusTypes`, `m_WeaponTypes`, `m_MagicTypes`

#### ■ 런타임 읽기 의미론
【주요 C 증거】

##### ◆ C-ENEMY-02: EnemyID 구체화 처리 with 구체적 행 오프셋

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

##### ◆ C-TACT-01: TacticalList/TacticalSkillList 행 해석기 and 스킬 목록 접근 함수

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

##### ◆ C-TACT-02: TacticalActionList 행 해석기

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

##### ◆ C-TACT-03: TacticalActionList 조건 레인 사용처

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

##### ◆ C-TACT-04: 택티컬 조건 tuple 평가기

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

##### ◆ C-TACT-05: TacticalActionList 스킬 인덱스 and SelectType 의미 in 후보 decision

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

##### ◆ C-TACT-06: TacticalActionList 추천 필터

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

##### ◆ C-TACT-07: 순차 예고/액션에서 그룹/턴 계열 필드 읽기

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

【필드 단위 해석】

| 키 | 런타임 C 위치 | 해석 | 상태 | 증거 |
|---|---|---|---|---|
| `m_AilmentTypes` | `+0x48`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x28`부터 조건/필터 레인를 저장한다. `CheckActionCondition`은 이 six 병렬 배열를 택티컬 조건 평가기에 전달한다. | 복원됨 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_Conditions` | `+0x28`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x28`부터 조건/필터 레인를 저장한다. `CheckActionCondition`은 이 six 병렬 배열를 택티컬 조건 평가기에 전달한다. | 복원됨 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_FriendlyIndex` | `+0x14`(스칼라). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 후보 decision 동안 행 `+0x14`를 local 선택기로 읽는다. Hex-Rays는 여기서 DB 필드 이름을 보존하지 않았다. role은 스킬 ID가 아니라 target/friendly 선택기 레인이다. | 복원됨 | `C-TACT-05` |
| `m_GroupIndex` | `+0x20`(스칼라). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | Sequential-presage logic은 행 `+0x20`을 읽고 previously seen 그룹 값와 비교한다. 이것이 액션 grouping 레인에 대해 복원된 가장 강한 해석이다. | 복원됨 | `C-TACT-07` |
| `m_MagicTypes` | `+0x78`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x28`부터 조건/필터 레인를 저장한다. `CheckActionCondition`은 이 six 병렬 배열를 택티컬 조건 평가기에 전달한다. | 복원됨 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_NoRecommendSkillIndex` | `C-TACT-05:17393877` `v6->m_NoRecommendSkillIndex = *(_DWORD *)&v24[28];` | 이는 DB 키가 아니라 런타임 component 필드다. `SelectType == 6`일 때 `TacticalActionList +0x1C`에서 설정되며, 후보 collection은 `m_SkillIndex`가 이 값과 같은 액션를 reject한다. | cross-reference | `C-TACT-05`, `C-TACT-06` |
| `m_Params` | `+0x38`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x28`부터 조건/필터 레인를 저장한다. `CheckActionCondition`은 이 six 병렬 배열를 택티컬 조건 평가기에 전달한다. | 복원됨 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_PrioritySkill` | `+0x1C`(스칼라). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 `SelectType`이 `5` 또는 `6`일 때 행 `+0x1C`을 읽는다. 값은 `m_RecommendSkillIndex` 또는 `m_NoRecommendSkillIndex`에 쓰인다. 내보내기는 이 추천 operand 외의 별도 priority mechanism을 증명하지 않는다. | 부정적/부분 복원 | `C-TACT-05`, `C-TACT-06` |
| `m_RecommendSkillIndex` | `C-TACT-05:17393873` `v6->m_RecommendSkillIndex = *(_DWORD *)&v24[28];`<br>`C-TACT-05:17393881` `*(_QWORD *)&v6->m_RecommendSkillIndex = -1;` | 이는 DB 키가 아니라 런타임 component 필드다. `SelectType == 5`일 때 `TacticalActionList +0x1C`에서 설정되며, 후보 collection은 `m_SkillIndex`가 이 값과 같은 액션만 유지한다. | cross-reference | `C-TACT-05`, `C-TACT-06` |
| `m_SelectType` | `+0x18`(바이트). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x18`을 바이트 선택 모드로 읽는다. 복원된 branches는 sequential behavior에 대해 `4`와 비교하고, recommend/no-recommend filtering에 대해 `5`/`6`과 비교한다. | 복원됨 | `C-TACT-05`, `C-TACT-07` |
| `m_SkillIndex` | `+0x10`(스칼라). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x10`을 현재 적의 `m_pSkillIDs`/`TacticalSkillList.m_UseSkills` 배열에 대한 인덱스로 읽는다. direct `SkillID`가 아니다. | 복원됨 | `C-TACT-05`, `C-TACT-01` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | `m_SkillsID`는 `TacticalActionList` 필드가 아니다. `EnemyID +0x84`에서 읽히고, `TacticalSkillList` 행를 해석하며, `m_SkillIndex`가 인덱스하는 `m_UseSkills` 배열를 제공한다. | cross-reference | `C-ENEMY-02`, `C-TACT-01`, `C-TACT-05` |
| `m_StatusTypes` | `+0x58`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x28`부터 조건/필터 레인를 저장한다. `CheckActionCondition`은 이 six 병렬 배열를 택티컬 조건 평가기에 전달한다. | 복원됨 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_TurnCount` | `+0x24`(스칼라). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | Ordered/sequential logic은 행 `+0x24`를 additional cadence/order operand로 읽는다. 정확한 필드 이름은 남아 있지 않지만, 행 레인은 sequential presage/액션 선택에서 소비된다. | 복원됨 | `C-TACT-07` |
| `m_WeaponTypes` | `+0x68`(배열). 인용 블록에서는 더 짧은 명명된 대입이 남아 있지 않다. | 액션 행는 행 `+0x28`부터 조건/필터 레인를 저장한다. `CheckActionCondition`은 이 six 병렬 배열를 택티컬 조건 평가기에 전달한다. | 복원됨 | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
