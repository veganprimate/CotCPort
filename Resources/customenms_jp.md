### **言語:**  [英語](customenms_en.md) | [日本語](customenms_jp.md) | [簡体中文](customenms_zh.md) | [한국어](customenms_kr.md)

> 本ガイドにおけるランタイム証拠の表記規則: 「直接利用」は、ゲームプレイまたは UI のコードが、そのフィールドを何らかの処理のために直接読み取ることを意味する。「行レイアウトの証拠」は、取得関数または実体化処理によって、当該フィールドのオフセットやデータ形状が確定していることを意味する。「実体化のみ」は、そのフィールドがランタイムオブジェクトへコピーされることは確認できるものの、引用した C の抜粋内では、その後に意味論的に使用するコードが復元されていないことを意味する。

『オクトパストラベラー0』に新しい敵やバトルを追加する作業は、主に `AIBattle`、`Enemy`、必要に応じて `Skill`、`Texture`、および `Battle` 配下の データベースファイルを編集することで成立する。以下では関連する各ファイルを説明する。見出しの星の数は、そのファイルを編集する必要性の高さを示す（星が少ないほど、より特殊な用途で使われる）。ただし、どの目的でどのファイルを変更すべきか把握するため、記事全体を読むことを推奨する。

## ■ `Enemy/` 配下
### ■ (***) `EnemyFormations`
フィールド上に配置される敵フォーメーションを指定する。`m_PositionID` は `BattlePositions.m_id` を入力として受け取る（`BattlePositions` は `Battle/` 配下にある）。多数の敵を含むカスタムバトルでは、非ゼロの `m_PositionID` が十分に含まれる既存フォーメーションを探すか、独自のフォーメーションを作成する必要がある。`PositionID` によっては、バトルマップに応じて敵の位置が大きく変化する場合がある。
`Kingship_structs.hpp` では、『オクトパストラベラー0』のフォーメーションに次のラベルが割り当てられている。
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
#### ■ ランタイム上の読み取り意味論
この小節では、上で言及した各キーについて、フィールド単位のランタイム上の位置付けを示す。すなわち、正確な名前付き C 代入／読み取り、曖昧でない行オフセット上のレーン、または C エクスポートがそのキーをライブなフィールドとして保持していない場合の否定的な復元結果のいずれかである。解釈は、引用した C が許容する範囲に意図的に限定している。

【一次 C 証拠】

##### ◇ C-FORM-01: EnemyFormations 行解決処理

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

##### ◇ C-FORM-02: フォーメーション位置の BattlePositions への解決

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

【フィールド単位の解釈】

| キー | 実行時 C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_PositionID` | `+0x10`（配列）。引用範囲では、より短い名前付き代入は残っていない。 | フォーメーション行は `FormID` により解決される。その後、ゲームは行 `+0x10` の `TArray<int>` をインデックス指定で読み取り、選択された要素を `BattlePositions` の行 ID として扱う。返された `BattlePositions` 行は XYZ 座標と優先度を供給する。 | 復元済み | `C-FORM-01`, `C-FORM-02` |

### ■ (***) `EnemyBattleAnimSet`
『オクトパストラベラー 大陸の覇者』では、敵に特定の フリップブックアセットを指す ID を割り当てるが、『オクトパストラベラー0』では ID 群を用いる。各 ID はそれぞれ フリップブックアセットを参照する（`m_EnemyTextureIDs` は `Textures` 配下の `EnemyTexID` の `m_id` を入力として取る）。
`m_EnemyTextureIDs[0]` は**通常**スプライトの待機アニメーション（接尾辞 `Idl00`）を指し、`m_EnemyTextureIDs[1]` から `m_EnemyTextureIDs[3]` は**通常**攻撃アニメーション（接尾辞 `Atk00` から `Atk02`）を指す。少なくとも待機アニメーション用の フリップブックアセットは追加する必要がある。すなわち、`EnemyTexID` に新しいパスを登録し、その新規エントリの `m_id` を、ここで新しく追加するエントリの `m_EnemyTextureIDs[0]` に指定する。それ以外は任意である。

ただし前述のとおり、これらのインデックスが Idl00、Atk00 などへ対応するのはあくまで**典型的な**運用であり、実行時コードがその対応を強制しているわけではない。`EnemyTypeID.m_AnimationSetIDs[index]` がまず `EnemyBattleAnimSet` の行を選択し、その選択された行の `m_EnemyTextureIDs` が `EnemyTexID` のリソース ID として走査またはコピーされる。アクションのディスパッチは、各テクスチャについて `EnemyTexID` テーブルに `EBATTLE_ACTION_ID` を問い合わせることで得られる。したがって、この配列順は意味論的には**優先順位／同値時のタイブレーク順**であり、**アクションコードの名前空間**ではない。

『オクトパストラベラー 大陸の覇者』の `EnemyType` における `m_FlipbookID` は、これらの追加配列要素を異なる形で利用しているように見える点にも※注意すること。インデックス 0 は引き続き待機テクスチャを指す一方、『オクトパストラベラー 大陸の覇者』では一部の敵に対して不可視テクスチャをインデックス 1 に割り当てている（例: 「双界の魔影」の影）。このインデックス 1 のテクスチャは、特にそれも待機テクスチャとして読まれる場合、インデックス 0 のものを上書きし得る。

フリップブックアセットは次のスケールを想定している場合がある（`Kingship_structs.hpp` 参照）。

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


NPC（非プレイヤーキャラクター）テクスチャを使用する戦闘では、`EnemyBattleAnimSet` に追加エントリを作成する**必要はない**。NPC は通常、戦闘用スプライトシート（しばしば `_B` 接尾辞で終わる）をすでに持っているためである。単に `EnemyType` の `CharaID` に NPC の `CharaID` を指定すればよい（下記、および必要に応じて [カスタムキャラクターの追加方法](customchars_en.md) を参照）。実行時のフォールバック経路もこれを裏付けている。通常の敵タイプについて敵用フリップブック をロードできない場合、`SetupEnemyFlipbook` はそのテクスチャインデックスに対して `EnemyTypeID.m_NpcCharaID` を試すことがある。

#### ■ 実行時の読み取り意味論（フィールド単位の Hex-Rays 証拠）
`FEnemyBattleAnimSetBase` は 32 バイトの行である。復元されたコピー演算子は、行 ID と、`TArray<int>` サイズのメンバ 1 個だけをコピーしている。後続の戦闘側のすべての使用箇所もこのレイアウトと一致しており、この行は `m_id` でキー指定され、`+0x10` の配列は `m_EnemyTextureIDs` として解釈される。

**主要な C 証拠:**

##### ◇ C-ANIM-01: 復元された構造体サイズとコピーされるフィールド

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

**解釈。** デコンパイラ上の `TArray<FPackedNormal>` という型はテンプレート推定の副産物であるが、メモリ操作自体は曖昧ではない。この行コピーはオフセット `+0x08` に 32 ビットのスカラーを書き込み、続いて `+0x10` から始まる `TArray<unsigned int>` をコピーしている。下記の名前付き実行時使用箇所では、このスカラーが `m_id`、配列が `m_EnemyTextureIDs` である。

##### ◇ C-ANIM-02: `m_id` による直接探索と 32 バイト行幅

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

**解釈。** `EnemyBattleAnimSet` は `m_id` の等価比較によって解決される。この経路ではラベル検索は行われない。終端ポインタが `32 * ArrayNum` を用いているため、行幅は正確に 32 バイトである。

##### ◇ C-ANIM-03: `EnemyTypeID.m_AnimationSetIDs[index]` がアニメーションセット行を選択する

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

**解釈。** `index` / `TextureIdx` 引数は、`EnemyTypeID.m_AnimationSetIDs`（`+0x10` のポインタと `+0x18` の個数）から 1 要素を選択する。これは `m_EnemyTextureIDs` へのインデックスではない。改造上の含意として、1 つのタイプは複数のアニメーションセット参照を持てる。外側のスロットは `EnemyTypeID` によって決まり、内側のテクスチャ ID は `EnemyBattleAnimSet` に存在する。

##### ◇ C-ANIM-04: アニメーションセットスロット数は `EnemyTypeID.m_AnimationSetIDs` の個数である

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

**解釈。** 通常の敵の初期化はこの個数をループし、アニメーションセットスロットごとに `SetupEnemyFlipbook` を 1 回呼び出す。これにより、`EnemyTypeID.m_AnimationSetIDs` と `EnemyBattleAnimSet.m_EnemyTextureIDs` がさらに明確に分離される。

##### ◇ C-ANIM-05: 一括取得は意味論的なインデックス解釈を行わず `m_EnemyTextureIDs` をコピーする

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

**解釈。** この経路は `m_EnemyTextureIDs` をリソース ID の生配列として扱う。このコピー経路では、配列インデックスにアクション上の意味は付与されない。

##### ◇ C-ANIM-06: アクション指定の検索は `EnemyTexID` のアクションメタデータに対する先頭一致走査である

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

**解釈。** `m_EnemyTextureIDs` の順序は、先頭一致の優先順位としてのみ意味を持つ。要求されたアクションは、`AResourceManager::GetEnemyTexActionID` によって復元される `EnemyTexID.m_ActionTypeID` と照合されるのであり、配列インデックスとは照合されない。

##### ◇ C-ANIM-07: `EnemyTexID` はアクション ID とユニークマテリアルフラグを供給する

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

**解釈。** ここでは `EnemyTexID` 行のストライドが 40 バイトとして復元されている。アクション ID は行 `+0x10` から読まれ、ユニークマテリアルフラグは行 `+0x14` から読まれる。したがって、`EnemyBattleAnimSet` 自体が待機／攻撃の意味を符号化しているのではなく、その意味を持つ `EnemyTexID` 行を参照している。

##### ◇ C-ANIM-08: ロード経路はアクションでキー付けされた フリップブックセットを構築する

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

**解釈。** 通常の敵タイプと敵パーツはいずれも同じ フリップブック 構築経路に合流する。各 `EnemyTexID` に対してリソースカテゴリ `2` のオブジェクトがロードされ、その後 `EBATTLE_ACTION_ID` をキーとする フリップブックセットへ挿入される。パーツのタイプ ID は、ゲームパラメータ `0x1B66` の加算／減算によって区別される。

【フィールド単位の解釈】

| キー | 実行時 C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_id` | `result->m_id != AnimSetID`; 行幅 `32` | `EnemyBattleAnimSet` の主要な実行時キー。検索はロード済みデータリストを ID で線形走査する。 | 復元済み | `C-ANIM-01`, `C-ANIM-02` |
| `m_EnemyTextureIDs` | `m_EnemyTextureIDs.ArrayNum`, `.AllocatorInstance.Data`; 行オフセット `+0x10` | `EnemyTexID.m_id` 値の `TArray<int>`。一括経路では生 ID としてコピーされ、アクション経路では走査されて `EnemyTexID` メタデータによりアクション意味が決定される。 | 復元済み | `C-ANIM-05`, `C-ANIM-06`, `C-ANIM-07`, `C-ANIM-08` |
| `TextureIdx` / アニメーションセットスロット | `EnemyTypeID` `+0x10/+0x18`, `GetEnemyTypeFlipbookSetNum` の個数 | `EnemyTypeID.m_AnimationSetIDs` の 1 要素を選択し、その値を `EnemyBattleAnimSet.m_id` として解決する。 | 復元済み | `C-ANIM-03`, `C-ANIM-04` |

### ■ (**) `EnemyParts`
カスタムボスが複数の視覚的／機械的パーツから構成される場合（例: オル・ガルデラ、祖竜アマツカミノオロチ）は、このファイルで各パーツを指定する。双界の魔影など、このファイルをまったく使用しないボスも存在する。C エクスポートからは、`EnemyParts` が独立した敵マスターテーブルではないことが分かる。パーツは、`EnemyTypeID.m_PartsID` から選択されるパーツ行を用いて、所有者となる敵に従属する `AQPBattleCharacterBase` としてインスタンス化される。

実行時には、敵の構築時に所有者の `EnemyTypeID.m_PartsID` 配列が読まれる。各正のパーツ ID は `UEnemyDB::GetEnemyPartsDB` によって解決される。行が存在する場合、バトルマネージャはそのパーツ用のバトルキャラクターを生成し、`PartsIndex` を割り当て、`m_PartsOwner` を通じて所有者へリンクし、所有者／マネージャ側のパーツコンテナへ登録する。その後、パーツのスプライトは上記の `EnemyBattleAnimSet` と同じ仕組みでロードされるが、その際、パーツ ID は `UGameParamsUtility::GetParamInt(0x1B66)` を加算することで特殊なタイプ ID 名前空間へ写像される。
#### ■ 実行時の読み取り意味論
`FEnemyPartsBase` は 56 バイトの行である。エクスポートは一部の意味名しか保持していないため、この節では C が元のフィールド識別子を保持していない箇所について行オフセットを用いる。改造上重要なのは、これらのオフセットが不活性なパディングではないという点である。エンジンはこれらを、アニメーション選択、配置、初期表示状態、ブレイク／選択時の視覚伝播、条件付き表示変更、フェード挙動のために読み取る。

**主要な C 証拠:**

##### ◇ C-PARTS-01: 復元された行サイズと行メンバのコピー順序

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

**解釈。** 行幅は 56 バイトである。コピーされるレーンは、`+0x08` の ID、`+0x0C` に始まる重なった 8 バイト組、`+0x14`、`+0x18`、`+0x1C`、`+0x20` のスカラーレーン、`+0x24`、`+0x25`、`+0x26` のバイト、および `+0x28`、`+0x2C`、`+0x30` のスカラーレーンである。後続の使用箇所が、これらのレーンに意味を割り当てている。

##### ◇ C-PARTS-02: `m_EnemyParts.m_DBAccess` を通じたキー検索

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

**解釈。** `PartsID` はテーブルの DB アクセスヘルパによって解決される。DB ヘルパがそのように写像しない限り、これは密な配列インデックスではない。この行が返されるのは、テーブルがロード済みで、キーが範囲内のインデックスへ解決された場合だけである。

##### ◇ C-PARTS-03: `+0x14` は `EnemyBattleAnimSet.m_id` 参照である

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

**解釈。** 行オフセット `+0x14` はテクスチャ ID ではない。これは `GetFlipbookSet` に渡されるため、`EnemyBattleAnimSet.m_id` である。つまり、これはパーツのアニメーションセット参照である。

##### ◇ C-PARTS-04: 所有者の `EnemyTypeID.m_PartsID` がパーツを列挙する

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

**解釈。** 所有者のタイプ行は `+0xC0/+0xC8` に `TArray<int>` を持ち、これは `EnemyTypeID.m_PartsID` に対応する。ゼロでない各エントリはパーツ ID であり、ループインデックスは `PartsIndex` として渡される。したがって、パーツは独立したエンカウントメンバではなく、特定の所有者／タイプ構築経路に従属する。

##### ◇ C-PARTS-05: 行オフセット `+0x18/+0x1C/+0x20` はパーツ位置に影響する

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

**解釈。** オフセット `+0x18` と `+0x1C` は 浮動小数点値として読まれ、オフセット `+0x20` は整数として読まれた後、`-0.1` 倍される。2 つの呼び出し箇所ではローカルベクトル名が異なるが、いずれも同じ 3 つの行レーンを消費し、所有者から導出された位置に対してパーツを再配置する。

##### ◇ C-PARTS-06: セットアップはパーツを所有者へリンクし、初期表示状態を読む

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

**解釈。** オフセット `+0x24` は初期のアニメーション表示フラグである。`m_PartsOwner`、`m_PartsID`、`m_PartsIndex` は、生成されたキャラクター上の実行時状態フィールドであり、データテーブルのフィールドではない。`PartsID + ParamInt` という変換は、フリップブックのロードを `GetEnemyPartsFlipbookID` 経路へ送るための識別子である。

##### ◇ C-PARTS-07: 条件付きの表示オン／オフは行オフセット `+0x28` と `+0x2C` を使用する

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

**解釈。** `+0x28` は表示オン条件 ID であり、パーツが非表示のときだけ評価される。`+0x2C` は表示オフ条件 ID であり、パーツが表示中のときだけ評価される。どちらも バトル中断条件／条件バンドルとして解決され、`UBattleUtility::CheckConditions` によって評価される。これらは生の真偽値ではない。

##### ◇ C-PARTS-08: フェード／ディザー挙動は行オフセット `+0x30` を読む

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

**解釈。** オフセット `+0x30` はフェード挙動を制御する 浮動小数点値として読まれる。非正値では即時非表示が許可され、正値では表示状態の変更がディザーフェード要求へ送られる。

##### ◇ C-PARTS-09: ブレイク／選択時の視覚伝播は `+0x25` と `+0x26` のバイトを使用する

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

**解釈。** バイト `+0x25` は、バイト `+0x26` が立っていない場合に、再生レート／ブレイクアニメーション連動経路を制御する。バイト `+0x26` は、パーツ固有のシールドブレイクおよび選択マテリアル挙動を制御する。これらのバイトは、戦闘状態の視覚的伝播によって直接消費される。

**復元された行オフセットマップ:**

| 行内位置 | 実行時の解釈 | 証拠 |
|---|---|---|
| `+0x08` | `EnemyParts.m_id`。`m_EnemyParts.m_DBAccess` により解決される。 | `C-PARTS-01`, `C-PARTS-02` |
| `+0x0C/+0x10` | 8 バイトのレーンとしてコピーされるが、引用したゲームプレイ側使用箇所から安全な意味名は割り当てられない。 | `C-PARTS-01` |
| `+0x14` | アニメーションセット ID。`GetFlipbookSet` に渡されるため、`EnemyBattleAnimSet.m_id` を参照する。 | `C-PARTS-03` |
| `+0x18` | パーツ配置へ適用される空間的な浮動小数点値。 | `C-PARTS-05` |
| `+0x1C` | パーツ配置へ適用される空間的な浮動小数点値。 | `C-PARTS-05` |
| `+0x20` | `-0.1` 倍されてパーツ配置へ適用される空間的整数。 | `C-PARTS-05` |
| `+0x24` | 初期アニメーション表示フラグ。 | `C-PARTS-06` |
| `+0x25` | ブレイク／再生レート連動フラグ。 | `C-PARTS-09` |
| `+0x26` | シールドブレイク／選択マテリアル連動フラグ。 | `C-PARTS-09` |
| `+0x28` | 非表示時にのみ評価される表示オン条件 ID。 | `C-PARTS-07` |
| `+0x2C` | 表示中にのみ評価される表示オフ条件 ID。 | `C-PARTS-07` |
| `+0x30` | 表示変更のためのフェード／ディザー用の浮動小数点値。 | `C-PARTS-08` |
### ■ (****) `EnemyTypeID`
このアセットは、『オクトパストラベラー0』における敵アーキタイプのマスター的な「戦闘スプライト表示記述子」である。ゲームに対して、おおむね次の情報を与える。
- 使用するアニメーションセット、または代替として関連付ける NPC／キャラクターのスプライトシート（`m_AnimationSetIDs` は `EnemyBattleAnimSet` の `m_id` を入力として取り、`m_NpcCharaID` は `Character/` 配下の `CharaID` の `m_id` を入力として取る）
- スプライトおよび各種オーバーレイの拡大率と位置補正:
  - ヒットスパーク（`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`）
  - シールドおよび弱点表示オーバーレイ
  - 敵エフェクト（`m_EffectOffsetX`,`m_EffectOffsetY`、および `m_EffectFromBottomOffsetY`: スプライト下端を基準にしたオフセットで、地面基準のエフェクトに多い）
  - ダメージ数値（`m_DamageOffsetX`,`m_DamageOffsetY`）
  - 状態異常表示（`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`）
  - カーソル（`m_CursorOffsetX`,`m_CursorOffsetY`）
- その敵アーキタイプに関連付ける敵パーツ（`m_PartsID`）
- その敵タイプで使用するサウンド／ボイス:
  - `m_CallSE`（すなわち「呼び出し効果音」）
  - `m_VoiceSymbol`（おそらく `Sound` 配下の `VoiceSymbolList` を参照する整数）
  - `m_CueSheet`（おそらく `Sound/` 配下の `SoundSheetList` を参照し、この敵のボイスセットに使うキューシートを選択する）
- どのアタッチエフェクト（VFX）を、どこに生成するか（`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`）
- 行動順アイコンに、参照テクスチャのどの位置（通常はフリップブックアセット／キャラクタースプライトシートの最初のフレーム）を使うか（`m_OrderTexUS`, `m_OrderTexVS`; 大きいアイコン、つまり指定キャラクターが行動中のときは `m_OrderTexUL` と `m_OrderTexVL` が使われる）、およびその参照テクスチャを行動順アイコン内でどの倍率で描画するか（`m_OrderScaleS`; `m_OrderScaleL`）。たとえば 「双界の魔影」戦の影は、全身が行動順アイコンに収まるよう非常に小さい 行動順アイコンの表示倍率 を使用している。
#### ■ 実行時に関係する追加フィールド
  - `m_OverrideOrderIconAnimSetID`: 行動順アイコンに特殊なテクスチャアセットを使いたい場合の指定。
  - `m_AttachEffectInGodbeastProduction`: 行 `+0xA5` のバイトとして実体化される。引用した C 経路では直接のゲームプレイ／UI 利用箇所は復元できていないため、その意味論は戦闘／フィールドの アタッチエフェクト系フィールドより弱い根拠に留まる。
  - `m_AddStartUV`: 行 `+0xA4` のバイトとして実体化される。引用した C 経路では直接の利用箇所は復元できていないため、意味論未確定の実行時フィールドとして扱う。
  - `m_KeepVisibleAttachEffectOnMotion`: `FEnemyTypeIDBase` の一部として実体化され、行 `+0xA6` のバイトとして復元される。後続の `EnemyTypeID` オフセットマップでは、`m_AddStartUV` および `m_AttachEffectInGodbeastProduction` とは別に分類している。正確な視覚挙動は、エクスポータ由来のノイズではなく、利用証拠が部分的な実行時フィールドとして扱うべきである。
  - `m_LinkOwnerAnim`: 行 `+0xD0` のバイトとして実体化される。C エクスポートはこれが `EnemyTypeID` 行まで保持されることを証明しているが、正確な下流の利用箇所は部分的にしか復元できていない。
  - `m_DefeatEffect`: 死亡時に再生されるエフェクト ID（例: 『全てを授けし者』の特殊な撃破エフェクト）。`Kingship_structs.hpp` では次のように定義されている。

```hpp
enum class EENEMY_DEFEATEFFECT : uint8
{
	NONE                                     = 0,
	EDE_AGM_CHP8_02                          = 1,
	EDE_AGM_CHP8_03_RING                     = 2,
	EENEMY_MAX                               = 3,
};
```

       
- `m_ChangeColorID`: 色変更テーブルへの ID。`Character/` 配下の `CharaColorChangeParams` と関係している可能性がある。本作では、主人公（公式文言では「指輪に選ばれしきみ」）が自身の影と対峙する場面（`ChangeColorID` 6）でのみ使われている。『オクトパストラベラー 大陸の覇者』の `EnemyType` にはこのキー自体が存在しない。SDK には、関連している可能性のある次の列挙型 が含まれている。

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
#### ■ 実行時の読み取り意味論（フィールド単位の Hex-Rays 根拠）
##### ◇ C-TYPE-01: EnemyTypeID 行解決処理

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

##### ◇ C-TYPE-02: EnemyTypeID の実体化フィールドコピー

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

##### ◇ C-TYPE-03: EnemyID から EnemyTypeID への接続、および選択された EnemyTypeID 利用箇所

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

【フィールド単位の解釈】

| キー | 実行時 C 上の所在 | 解釈 | 状態 | 根拠 |
|---|---|---|---|---|
| `m_AddStartUV` | `C-TYPE-02:17041883` `this->m_AddStartUV = __that->m_AddStartUV;` | このキーは行オフセット `+0xA4` のバイト／ブール値 レーンとして読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentInfoOffsetX` | `C-TYPE-02:17041865` `this->m_AilmentInfoOffsetX = __that->m_AilmentInfoOffsetX;` | このキーは行オフセット `+0x5C` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_AilmentOffset` | `C-TYPE-02:17041866` `this->m_AilmentOffset = __that->m_AilmentOffset;` | このキーは行オフセット `+0x60` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_AnimationSetIDs` | `C-TYPE-02:17041822` `this->m_AnimationSetIDs.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041823` `ArrayNum = __that->m_AnimationSetIDs.ArrayNum;`<br>`C-TYPE-02:17041824` `Data = __that->m_AnimationSetIDs.AllocatorInstance.Data;` およびローカルでの追加 4 箇所 | このキーは行オフセット `+0x10` の `TArray` ヘッダとして読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffect` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;`<br>`C-TYPE-02:17041886` `this->m_AttachEffect = __that->m_AttachEffect;` | このキーは行オフセット `+0xA8` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_AttachEffectInGodbeastProduction` | `C-TYPE-02:17041884` `this->m_AttachEffectInGodbeastProduction = __that->m_AttachEffectInGodbeastProduction;` | このキーは行オフセット `+0xA5` のバイト／ブール値 レーンとして読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetX` | `C-TYPE-02:17041887` `this->m_BattleAttachEffectOffsetX = __that->m_BattleAttachEffectOffsetX;` | このキーは行オフセット `+0xAC` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_BattleAttachEffectOffsetY` | `C-TYPE-02:17041888` `this->m_BattleAttachEffectOffsetY = __that->m_BattleAttachEffectOffsetY;` | このキーは行オフセット `+0xB0` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | このキーは行オフセット `+0x7C` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | このキーは行オフセット `+0x30` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_CueSheet` | `C-TYPE-02:17041875` `this->m_CueSheet = __that->m_CueSheet;` | このキーは行オフセット `+0x84` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetX` | `C-TYPE-02:17041869` `this->m_CursorOffsetX = __that->m_CursorOffsetX;` | このキーは行オフセット `+0x6C` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_CursorOffsetY` | `C-TYPE-02:17041870` `this->m_CursorOffsetY = __that->m_CursorOffsetY;` | このキーは行オフセット `+0x70` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetX` | `C-TYPE-02:17041867` `this->m_DamageOffsetX = __that->m_DamageOffsetX;` | このキーは行オフセット `+0x64` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_DamageOffsetY` | `C-TYPE-02:17041868` `this->m_DamageOffsetY = __that->m_DamageOffsetY;` | このキーは行オフセット `+0x68` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_DefeatEffect` | `C-TYPE-02:17041909` `this->m_DefeatEffect = __that->m_DefeatEffect;` | このキーは行オフセット `+0xD4` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectFromBottomOffsetY` | `C-TYPE-02:17041864` `this->m_EffectFromBottomOffsetY = __that->m_EffectFromBottomOffsetY;` | このキーは行オフセット `+0x58` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetX` | `C-TYPE-02:17041862` `this->m_EffectOffsetX = __that->m_EffectOffsetX;` | このキーは行オフセット `+0x50` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_EffectOffsetY` | `C-TYPE-02:17041863` `this->m_EffectOffsetY = __that->m_EffectOffsetY;` | このキーは行オフセット `+0x54` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffect` | `C-TYPE-02:17041889` `this->m_FieldAttachEffect = __that->m_FieldAttachEffect;`<br>`C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;`<br>`C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | このキーは行オフセット `+0xB4` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetX` | `C-TYPE-02:17041890` `this->m_FieldAttachEffectOffsetX = __that->m_FieldAttachEffectOffsetX;` | このキーは行オフセット `+0xB8` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_FieldAttachEffectOffsetY` | `C-TYPE-02:17041891` `this->m_FieldAttachEffectOffsetY = __that->m_FieldAttachEffectOffsetY;` | このキーは行オフセット `+0xBC` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosX` | `C-TYPE-02:17041857` `this->m_HitPosX = __that->m_HitPosX;` | このキーは行オフセット `+0x3C` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitPosY` | `C-TYPE-02:17041858` `this->m_HitPosY = __that->m_HitPosY;` | このキーは行オフセット `+0x40` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleX` | `C-TYPE-02:17041859` `this->m_HitScaleX = __that->m_HitScaleX;` | このキーは行オフセット `+0x44` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_HitScaleY` | `C-TYPE-02:17041860` `this->m_HitScaleY = __that->m_HitScaleY;` | このキーは行オフセット `+0x48` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_KeepVisibleAttachEffectOnMotion` | `C-TYPE-02:17041885` `this->m_KeepVisibleAttachEffectOnMotion = __that->m_KeepVisibleAttachEffectOnMotion;` | このキーは行オフセット `+0xA6` のバイト／ブール値 レーンとして読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_LinkOwnerAnim` | `C-TYPE-02:17041908` `this->m_LinkOwnerAnim = __that->m_LinkOwnerAnim;` | このキーは行オフセット `+0xD0` のバイト／ブール値 レーンとして読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_NpcCharaID` | `C-TYPE-02:17041838` `this->m_NpcCharaID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041839` `v6 = __that->m_NpcCharaID.ArrayNum;`<br>`C-TYPE-02:17041840` `v7 = __that->m_NpcCharaID.AllocatorInstance.Data;` およびローカルでの追加 4 箇所 | このキーは行オフセット `+0x20` の `TArray` ヘッダとして読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleL` | `C-TYPE-02:17041882` `this->m_OrderScaleL = __that->m_OrderScaleL;` | このキーは行オフセット `+0xA0` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderScaleS` | `C-TYPE-02:17041879` `this->m_OrderScaleS = __that->m_OrderScaleS;` | このキーは行オフセット `+0x94` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUL` | `C-TYPE-02:17041880` `this->m_OrderTexUL = __that->m_OrderTexUL;` | このキーは行オフセット `+0x98` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexUS` | `C-TYPE-02:17041877` `this->m_OrderTexUS = __that->m_OrderTexUS;` | このキーは行オフセット `+0x8C` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVL` | `C-TYPE-02:17041881` `this->m_OrderTexVL = __that->m_OrderTexVL;` | このキーは行オフセット `+0x9C` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_OrderTexVS` | `C-TYPE-02:17041878` `this->m_OrderTexVS = __that->m_OrderTexVS;` | このキーは行オフセット `+0x90` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_OverrideOrderIconAnimSetID` | `C-TYPE-02:17041876` `this->m_OverrideOrderIconAnimSetID = __that->m_OverrideOrderIconAnimSetID;` | このキーは行オフセット `+0x88` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_PartsID` | `C-TYPE-02:17041892` `this->m_PartsID.AllocatorInstance.Data = nullptr;`<br>`C-TYPE-02:17041893` `v8 = __that->m_PartsID.ArrayNum;`<br>`C-TYPE-02:17041894` `v9 = __that->m_PartsID.AllocatorInstance.Data;` およびローカルでの追加 4 箇所 | このキーは行オフセット `+0xC0` の `TArray` ヘッダとして読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetX` | `C-TYPE-02:17041872` `this->m_SupportOffsetX = __that->m_SupportOffsetX;` | このキーは行オフセット `+0x78` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_SupportOffsetY` | `C-TYPE-02:17041871` `this->m_SupportOffsetY = __that->m_SupportOffsetY;` | このキーは行オフセット `+0x74` の32 ビット浮動小数点値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | このキーは行オフセット `+0x80` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_id` | `C-TYPE-02:17041821` `this->m_id = __that->m_id;` | このキーは行オフセット `+0x08` の32 ビットスカラー値として読み出される。行自体は `UEnemyDB::GetEnemyTypeDB(TypeID)` により解決され、その行サイズは `0xD8` バイトである。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |

#### ■ 追加の実行時根拠: 二次的な `EnemyTypeID` 利用箇所

一部の視覚／UI システムは、完全な `FEnemyTypeIDBase` 行を実体化しない。その代わり、小さな 取得関数から入り、まず `EnemyID.m_TypeID` を解決し、その後 `EnemyTypeID` から特定のバイト、浮動小数点値、または配列レーンを読み出す。

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

| キー | 実行時の解釈 |
|---|---|
| `m_EffectFromBottomOffsetY` | 敵 ID 経路で、まず `EnemyID.m_TypeID @ +0x18` を読み、続いて `EnemyTypeID +0x58` を読み出す。いずれかの 検索に失敗した場合、取得関数は `0.0` を返す。 |
| `m_AilmentInfoOffsetX` | 同じ `EnemyID -> EnemyTypeID` 経路を通って、`EnemyTypeID +0x5C` から読み出される。これは単なるコピー済みフィールドではなく、直接的なスカラー利用箇所である。 |
| `m_OrderTexUS`, `m_OrderTexVS`, `m_OrderScaleS`, `m_OrderTexUL`, `m_OrderTexVL`, `m_OrderScaleL` | `GetOrderIconParameter` は、行から 6 個の連続する浮動小数点値を `float[35]`, `float[36]`, `float[37]`, `float[38]`, `float[39]`, `float[40]` として読み出す。これはそれぞれ行オフセット `+0x8C`, `+0x90`, `+0x94`, `+0x98`, `+0x9C`, `+0xA0` に対応する。 |
| `m_VoiceSymbol` | サウンドサブシステムは、`EnemyID -> EnemyTypeID` を解決し、行 `+0x80` を `USoundUtility::GetVoiceSymbol` へ渡すことでボイスシンボルを取得する。 |


#### ■ 表示・テクスチャ・音声・VFX 記述子としての `EnemyTypeID`

上のフィールド表は、`EnemyTypeID` 行が実際に使用される実行時レコードであることを示している。以下のコード経路は、より上位の重要な利用箇所である。すなわち、敵の構築、フリップブック／NPC フォールバックの選択、行動順アイコンの上書き、戦闘／フィールドのアタッチエフェクト、ボイスシンボルの解決、呼び出し効果音再生、およびヒット判定／オフセット形状である。これらの経路から、このテーブルは単なるテクスチャ検索テーブルではなく、`EnemyID.m_TypeID` を通じて繰り返し到達されるコンパクトな表示記述子であることが分かる。

##### ◇ C-TYPE-CONSTRUCTION-01: 敵構築時にすべての `EnemyTypeID.m_AnimationSetIDs` スロットを走査し、撃破エフェクト状態を初期化する

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

解釈： `AQPBattleCharacterBase::SetupEnemy` の実行中、敵行はすでに `v12` として保持されている。行 `v12 + 0x18` は `EnemyID.m_TypeID` である。この初期化経路は `GetEnemyTypeFlipbookSetNum(TypeID)` にアニメーションセット要素数を問い合わせ、その後インデックスごとに `SetupEnemyFlipbook` を 1 回ずつ呼び出す。したがって、`m_AnimationSetIDs` は単一のデフォルトテクスチャ ID ではなく、順序付きの表示スロット配列である。同じ構築処理で `GetEnemyTypeDefeatEffectID(TypeID)` を通じて `m_DefeatEffect` も読み取り、キャラクターの実行時状態に格納する。この時点以降、キャラクターは データベース行を再読み込みせずに固有の撃破エフェクト用カメラ経路を再生できる。

##### ◇ C-TYPE-FLIPBOOK-FALLBACK-02: `SetupEnemyFlipbook` におけるアニメーションセットスロット解決と NPC フォールバック

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

解釈： フリップブック経路は、まず `m_AnimationSetIDs[TextureIdx]` から `UEnemyFlipbookSet` のロードを試みる。それに失敗し、かつその行が `EnemyParts` ID ではなく通常の敵種別である場合（`type < ParamInt`）、`m_NpcCharaID[TextureIdx]` を読み取り、そのキャラクター ID を表示ソースとして使用する。ヒットボックスのオフセットとスケールはテクスチャインデックス `0`、すなわち基礎となる可視本体スロットに対してのみ適用される。これは「NPC 戦では `EnemyBattleAnimSet` が不要」という慣例的説明より強い。コードは、明示的なフォールバック階層を証明しており、単なるスキーマ上の慣習ではない。

##### ◇ C-TYPE-FLIPBOOK-MATERIALIZATION-03: `StaticGetEnemyFlipbooks` は `EnemyID -> TypeID` を解決し、`GetEnemyFlipBook` は選択されたアニメーションセット内の全テクスチャ ID を実体化する

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

解釈： 呼び出し側が `TypeID <= 0` を渡した場合、実行時はタイプ ID を遅延解決する。通常の敵経路と 敵パーツ経路は、`UGameParamsUtility::GetParamInt(0x1B66)` によって明示的に分岐する。しきい値未満では `TypeID` は `EnemyTypeID` であり、しきい値以上ではその値からしきい値を引いて `EnemyParts` ID として扱う。アニメーションセット行が見つかった後、`m_EnemyTextureIDs` の各要素はリソースカテゴリ `2` を通じてロードされ、その `EnemyTexID` メタデータがアクション ID を与える。したがって、ゲームは `m_EnemyTextureIDs[1]` を位置だけで「攻撃 0」と解釈しているわけではない。アクションバインディングは `EnemyTexID` の アクション ID によってデータ駆動型で決まる。

##### ◇ C-TYPE-ORDERICON-04: 上書き行動順アイコンは `m_OverrideOrderIconAnimSetID` を使用し、UV／表示倍率は専用の行内の浮動小数点値を使用する

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

解釈： `m_OverrideOrderIconAnimSetID` は行 `+0x88` である。正の値の場合、エンジンはその別個のアニメーションセットからアイドル／アクション ID 1 テクスチャをロードし、その最初のスプライトを `m_OverrideOrderIconSprite` として格納する。UV とピクセルスケール の値はフリップブックから推定されるのではない。`GetOrderIconParameter` は、行 `+0x8C`, `+0x90`, `+0x94`, `+0x98`, `+0x9C`, `+0xA0` を、それぞれ `m_OrderTexUS`, `m_OrderTexVS`, `m_OrderScaleS`, `m_OrderTexUL`, `m_OrderTexVL`, `m_OrderScaleL` として読み出す。

##### ◇ C-TYPE-ATTACHEFFECT-05: 戦闘用アタッチエフェクトは行 `+0xA8/+0xAC/+0xB0` を使用し、フィールド用アタッチエフェクトは行 `+0xB4/+0xB8/+0xBC` を使用する

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

解釈： 戦闘中は、`+0xA8` の `m_AttachEffect` が `AttachEffect` 行を選択し、`+0xAC/+0xB0` の `m_BattleAttachEffectOffsetX` および `m_BattleAttachEffectOffsetY` が、`m_ScaleX/m_ScaleY` の適用後に、生成されるエフェクトの Y/Z 配置へ折り込まれる。フィールド／シンボルエネミー側では、`+0xB4` の `m_FieldAttachEffect` が `AttachEffect` 行を選択し、`+0xB8/+0xBC` の `m_FieldAttachEffectOffsetX/Y` が シンボル位置オフセット計算に入る。フィールド経路は半幅の横方向オフセットとして `m_EffectOffsetX`（`+0x50`）も読み出すため、フィールド用アタッチエフェクトの配置は戦闘用アタッチエフェクトの配置と同一ではない。

##### ◇ C-TYPE-AUDIO-06: サウンドおよびボイス利用箇所は `m_CallSE`, `m_VoiceSymbol`, `m_CueSheet` を区別する

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

解釈： 行 `+0x7C` の `m_CallSE` は効果音 ID であり、`ASoundManager::ExecRequestPlaySeById(..., volume_rate=100)` へ直接渡される。行 `+0x80` の `m_VoiceSymbol` はそれ自体が最終文字列ではない。`USoundUtility::GetVoiceSymbol` に渡され、戦闘ボイスコードで使用する `FString`／UTF-16 のボイスシンボルを実体化する。`m_CueSheet` は `FEnemyTypeIDBase` のコピーコンストラクタで `+0x84` として復元されるが、この確認範囲では同等に直接的な敵タイプ側の利用箇所は復元できていない。したがって本ガイドでは、`m_CueSheet` を未使用ではなく、利用証拠が不完全な実行時に実体化されるフィールドとして分類すべきである。

##### ◇ C-TYPE-OFFSETMAP-07: コピーコンストラクタと直接利用箇所から復元された、コンパクトな `EnemyTypeID` 行レイアウトマップ

| オフセット | キー | 実行時根拠 | 解釈 |
|---:|---|---|---|
| `+0x10/+0x18` | `m_AnimationSetIDs` | `GetEnemyTypeFlipbookSet` は配列をコピーし、`TextureIdx` によってインデックス指定する。`SetupEnemy` は `0..GetEnemyTypeFlipbookSetNum-1` を反復する。 | 順序付きの表示スロット。各スロットは `EnemyBattleAnimSet.m_id` である。 |
| `+0x20/+0x28` | `m_NpcCharaID` | `GetEnemyTypeCharaID(TypeID, TextureIdx)` は配列を範囲チェックし、`Data[TextureIdx]` を返す。 | フリップブックセットがロードできないスロットに対する NPC／キャラクターのフォールバック。 |
| `+0x30` | `m_ChangeColorID` | `GetEnemyTypeColorID` は `EnemyTypeDB[48]` を読み出す。 | タイプ行を通じて到達される色変更 ID。 |
| `+0x34/+0x38` | `m_ScaleX`, `m_ScaleY` | `GetEnemyTypeScale` は `+0x34/+0x38` の浮動小数点値を読み出す。アタッチエフェクトもこれらの値を乗算する。 | 表示／エフェクトのスケール基準。 |
| `+0x3C/+0x40` | `m_HitPosX`, `m_HitPosY` | `GetEnemyTypeHitOffset` は `+0x3C/+0x40` の浮動小数点値を読み出す。 | 基礎フリップブック初期化 中に適用される基礎ヒットボックスオフセット。 |
| `+0x44/+0x48` | `m_HitScaleX`, `m_HitScaleY` | `GetEnemyTypeHitScale` は `+0x44/+0x48` の浮動小数点値を読み出す。 | 基礎フリップブック初期化 中に適用される基礎ヒットボックススケール。 |
| `+0x4C` | `m_Size` | `GetEnemyTypeSize` は `+0x4C` のバイト値を返す。`GetEnemySize(EnemyID)` は `EnemyID.m_TypeID` を通じてそこへ到達する。 | 敵のサイズクラスは `EnemyID` のスカラーではなく タイプ側プロパティ である。 |
| `+0x50/+0x54/+0x58` | `m_EffectOffsetX`, `m_EffectOffsetY`, `m_EffectFromBottomOffsetY` | `GetEnemyEffectOffset` は `+0x50/+0x54` を読み、`GetEnemyBottomEffectOffset` は `+0x58` を読み出す。シンボルのフィールド用アタッチエフェクト は `+0x50` を使う。 | エフェクト配置レーン。 |
| `+0x5C/+0x60` | `m_AilmentInfoOffsetX`, `m_AilmentOffset` | `GetEnemyAilmentInfoOffsetX` は `+0x5C` を読み、`GetAilmentEffectOffsetID` は `+0x60` を読み出す。 | UI／状態異常エフェクトのオフセット。 |
| `+0x64/+0x68` | `m_DamageOffsetX`, `m_DamageOffsetY` | `GetEnemyDamageOffset` は `+0x64/+0x68` を読み出す。 | ダメージ数値の配置。 |
| `+0x6C/+0x70` | `m_CursorOffsetX`, `m_CursorOffsetY` | `GetEnemyCursorOffset` は `+0x6C/+0x70` を読み出す。 | ターゲットカーソルの配置。 |
| `+0x74/+0x78` | `m_SupportOffsetY`, `m_SupportOffsetX` | コピーコンストラクタ／実体化による証明。今回の解析 では直接利用箇所は復元できていない。 | 実行時フィールド。利用証拠は不完全。 |
| `+0x7C/+0x80/+0x84` | `m_CallSE`, `m_VoiceSymbol`, `m_CueSheet` | `PlayEnemyTypeCallSE` は `m_CallSE` を使用し、`GetVoiceCharaSymbol` は `m_VoiceSymbol` を使用する。`m_CueSheet` はコピー／実体化される。 | 効果音とボイスシンボルは直接利用される。キューシートの利用箇所は部分的なまま。 |
| `+0x88` | `m_OverrideOrderIconAnimSetID` | `LoadOverrideOrderIcon` は `EnemyTypeDB[136]` を読み、`GetActionTexID(..., EBATTLE_ACTION_ID::IDLE)` を呼ぶ。 | 任意指定の独立した行動順アイコン用アニメーションセット。 |
| `+0x8C..+0xA0` | 行動順アイコン用の UV／スケールフィールド | `GetOrderIconParameter` は浮動小数点配列上のインデックス `35..40` を読み出す。 | 明示的な行動順アイコン UV／表示倍率 レーン。 |
| `+0xA4..+0xA6` | `m_AddStartUV`, `m_AttachEffectInGodbeastProduction`, `m_KeepVisibleAttachEffectOnMotion` | コピーコンストラクタは 3 フィールドすべてを証明する。`IsVisibleAttachEffectOnMotion` は `+0xA6` を直接読み出す。 | `m_KeepVisibleAttachEffectOnMotion` には直接利用箇所がある。他の 2 つは、復元された経路では 実体化のみ である。 |
| `+0xA8/+0xAC/+0xB0` | 戦闘用アタッチエフェクトフィールド | `SetEnemyAttachEffect` は `+0xA8` の id と `+0xAC/+0xB0` のオフセットを読み出す。 | 戦闘キャラクターの アタッチエフェクト。 |
| `+0xB4/+0xB8/+0xBC` | フィールド用アタッチエフェクトフィールド | `ASymbolEnemy::UpdateAttachEffect` は `+0xB4` の id と `+0xB8/+0xBC` のオフセットを読み出す。 | シンボルエネミー／フィールド用アタッチエフェクト。 |
| `+0xC0/+0xC8` | `m_PartsID` | パーツ初期化 は配列の 要素数／データポインタ を使用する。前節で パーツ解決関数 を引用している。 | 敵パーツ構成。 |
| `+0xD0` | `m_LinkOwnerAnim` | コピーコンストラクタ／実体化による証明。パーツ処理コード は間接的な文脈証拠を与える。 | ライブフラグ。連動パーツを作成する場合、正確な利用箇所は実機検証すべきである。 |
| `+0xD4` | `m_DefeatEffect` | `SetupEnemy` は `GetEnemyTypeDefeatEffectID` を通じて読み取り、実行時の撃破エフェクト状態を格納する。 | 固有撃破エフェクト ID。 |

### ■ (**) `EnemyWeakID`
敵に割り当てられる弱点プロファイルを管理する。`m_ResistWeapon` と `m_ResistMagic` は、`Skill/` 配下の `ResistType.m_id` を入力として受け取る。`ResistType` が 1 の場合、その武器種または属性種は弱点として扱われる。指定された武器種・属性種の順序は、おそらく `Skill/` 配下の `WeaponType` / `MagicType` アセットにおける `m_id` の順序に従う。概略は次のとおりである。
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
**注:** 『オクトパストラベラー0』では、1 体の敵に表示できる弱点は最大 8 個である。

`Kingship_structs.hpp` では、『オクトパストラベラー0』の一部の `ResistType` に次のラベルが割り当てられている。

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

#### ■ ランタイム上の読み取り意味論
##### ◇ C-WEAK-01: `EnemyWeakID` の行解決処理と弱点 ID 取得関数

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

##### ◇ C-WEAK-02: 敵の弱点行選択と耐性配列レーンの選択

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

##### ◇ C-WEAK-03: `USkillDB` による `EnemyWeakID` 配列レーン取得

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

##### ◇ C-ENEMY-02: 具体的な行オフセットを伴う `EnemyID` 実体化処理

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_ResistMagic` | `+0x20`（配列）。引用箇所では、これより短い名前付き代入は残っていない。 | `isMagic=true` を渡すことで魔法・属性耐性ベクトルが選択される。`USkillDB::GetResistsByID` は `&EnemyWeakDB[0x20]`、すなわち行 `+0x20` の `TArray` ヘッダを返す。 | 復元済み | `C-WEAK-02`, `C-WEAK-03` |
| `m_ResistWeapon` | `C-WEAK-03:17974953` `return &EnemyWeakDB[16 * v2 + 16];` | `isMagic=false` を渡すことで武器耐性ベクトルが選択される。`USkillDB::GetResistsByID` は `&EnemyWeakDB[0x10]`、すなわち行 `+0x10` の `TArray` ヘッダを返す。 | 復元済み | `C-WEAK-02`, `C-WEAK-03` |
| `m_WeakID` | `+0x1C`（スカラー。実フィールドは `EnemyID` 側に属する）。引用箇所では、これより短い名前付き代入は残っていない。 | 明示的な弱点行オーバーライドが指定されていない場合、`UEnemyDB::GetEnemyResists` はまず敵行 `+0x1C` から `EnemyID.m_WeakID` を読み取り、その値に対応する `EnemyWeakID` 行を解決する。 | 復元済み | `C-WEAK-02`, `C-ENEMY-02` |
| `m_id` | `+0x08`（スカラー。引用元の表では `EnemyTypeID` と書かれているが、ここで解決されるのは `EnemyWeakID` 行の主キーである）。引用箇所では、これより短い名前付き代入は残っていない。 | キーは行オフセット `+0x08` の 32 ビットスカラーとして読み取られる。`WeakID` は `UEnemyDB::GetEnemyWeakDB` を通じて解決され、行幅は `0x30` バイトである。 | 復元済み | `C-WEAK-01` |


### ■ (**) `EnemyWeakChangeID`
敵が弱点構成をどのように変更するかを管理する。`m_WeakIndices` には `EnemyWeakID.m_id` を入力する。この弱点変更の `m_id` は、たとえばアベイルなどから参照できる（[カスタムスキルとアベイルの追加方法](customskls_en.md) も参照）。

`Kingship_structs.hpp` では、以下のラベルが『オクトパストラベラー0』の ID に割り当てられている。この列挙型は、実行時の網羅的な制約ではなく、標準行に対する SDK 上の便宜的な名前として理解するのが適切である。ランタイムは列挙された `EENEMY_WEAKCHANGE_ID` 定数を `switch` で分岐していない。要求された数値 `ChangeID` を `m_EnemyWeakChangeID.m_DBAccess` で解決し、キーが行へ解決できた場合に、その行の `m_WeakIndices` 配列と抽選フラグを読み取る。したがって、DB アクセス／インデックス情報を一貫して再構築し、呼び出し側に新しい ID を渡せるなら、このテーブルは拡張可能である。
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
`EENEMY_WEAKCHANGE_MAX` をエンジンの実際の上限として扱ってはならない。C 実装は、`m_EnemyWeakChangeID.m_DBAccess` 内に存在する任意の正の `ChangeID` を受け付ける。この列挙型は、SDK にコンパイルされた標準 ID の名前を記録しているにすぎない。
#### ■ ランタイム上の読み取り意味論
`EnemyWeakChangeID` 行は 40 バイトのレコードとして復元される。実際の行読み取り関数は、行 `+0x10` にある配列へのポインタを返し、行 `+0x20` から抽選用の真偽値を書き出す。行 ID は 列挙型ラベルの `switch` ではなく、`FDbAccessHelper` によって解決される。

【一次 C 証拠】

##### ◇ C-WCH-01: キー検索、40 バイト行幅、`m_WeakIndices`、および抽選フラグ

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

**解釈。** `ChangeID` は正の整数キーである。行幅は 40 バイトである。`m_WeakIndices` は `+0x10` の `TArray<int>` であり、抽選フラグは `+0x20` のバイトである。この経路には 列挙型の範囲チェックは存在しない。

##### ◇ C-WCH-02: スキル／アベイルが `m_WeakChangeID` を供給する

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

**解釈。** アベイルは弱点変更 ID を保持する。確認できる使用箇所の 1 つでは、`SetChangeWeak` を呼ぶ前に ID が `unsigned __int8` へキャストされている。これは呼び出し側での幅縮小であり、255 を超える ID を導入する場合はテストが必要である。これは、`int ChangeID` を受け取って データベース検索を行うテーブル検索関数とは別の問題である。

##### ◇ C-WCH-03: 有効な弱点変更 ID を設定するとランタイム状態が変化し、UI の弱点シャッフルが開始される

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

**解釈。** `SetChangeWeak` は選択されたテーブル ID をランタイム状態（`m_WeakChangeID`）へ保存し、ID が変化した場合には切り替えフラグを立てる。また、弱点変更 ID が未設定の状態から遷移した場合には初回抽選フラグを初期化し、ステータス UI に対して弱点変更アニメーションの開始を要求する。

##### ◇ C-WCH-04: 弱点変更の実行時に `m_WeakIndices` からキューを構築する

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

**解釈。** `m_WeakIndices` 配列は、キャラクター側の `m_ChangeWeaks` キューへコピーされる。この処理には重要なランタイム規則が 2 つある。第一に、正でない弱点 ID に到達すると走査を終了する。第二に、初回抽選遷移時には、敵の現在／基本弱点 ID が候補キューから除外される。

##### ◇ C-WCH-05: 抽選モードでは候補キューからランダムに選んだ値を繰り返し追加する

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

**解釈。** 行の抽選フラグが真であり、候補が 2 個以上ある場合、ランタイムは候補キューからランダム選択を繰り返し、キューを拡張する。したがって、抽選行と決定的な巡回行は同じ `m_WeakIndices` 配列を使用するが、キュー構築の挙動は異なる。

##### ◇ C-WCH-06: 実行時には次の弱点 ID を取り出し、`m_NowWeakID` に保存する

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

**解釈。** 選択される弱点プロファイルはキュー先頭の要素である。選択後、その要素はキューから削除され、`m_NowWeakID` に保存される。戻り値は、現在の弱点プロファイルが変化したかどうかを表す。

##### ◇ C-WCH-07: UI／インデックス取得経路は、現在の弱点 ID を `m_WeakIndices` 内で検索する

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

**解釈。** ランタイムは、元の `m_WeakIndices` 配列を検索することで、現在有効な弱点プロファイルの順序位置を復元できる。現在の弱点 ID が配列内に存在しない場合、この関数は `-1` を返す。

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., ChangeID)` | `m_EnemyWeakChangeID` への正の整数キー。標準 列挙型ラベルは、実行時の網羅的な分岐ではない。 | 復元済み | `C-WCH-01` |
| `m_WeakIndices` | 行 `+0x10`。`TArray<int>*` として返される。 | `EnemyWeakID.m_id` 値の順序付きリスト。`ChangeWeak` 内では、正でない要素に到達すると走査が打ち切られる。 | 復元済み | `C-WCH-01`, `C-WCH-04`, `C-WCH-06`, `C-WCH-07` |
| 抽選フラグ／行 `+0x20` | `*isLottery = *(_BYTE *)(v6 + 32)` | キュー構築を、決定的な直接キュー化から、ランダム選択によるキュー拡張へ切り替える真偽値。 | 復元済み | `C-WCH-01`, `C-WCH-05` |
| SDK 列挙型項目 | 引用したランタイムでは 列挙型定数に対する `switch` は確認されない。 | 標準 ID に対する名前でしかない。DB の拡張自体は `EENEMY_WEAKCHANGE_MAX` によって妨げられない。ただし、`unsigned __int8` を経由する呼び出し箇所では、その箇所を修正または検証しない限り、ID を 1 バイト範囲内に保つべきである。 | 復元済み／要注意 | `C-WCH-01`, `C-WCH-02` |

### ■ (**) `EnemyWeakLockID`
敵が弱点をどのようにロックするかを管理する。ここでは、無属性魔法ダメージや毒ダメージを個別の特殊レーンとしてロックする証拠は得られていない。ロック判定は、保存されている武器・魔法配列の長さと、渡された武器種／魔法種インデックスに対して単純に境界チェックを行う。

弱点ロックは通常、状態異常またはアベイルによって保持される。バトルキャラクターは、状態異常 ID `37` の有効な状態異常スロットを調べ、発生元のアベイル ID を読み取り、`SkillAvailID.m_WeakLockID` を取得したうえで、指定された武器または魔法の弱点が `EnemyWeakLockID` によってロックされているかを問い合わせる。UI コードも同じ武器／魔法ベクトルを取得し、ロック表示状態を開始または終了する。

#### ■ ランタイム上の読み取り意味論
`EnemyWeakLockID` 行は 80 バイトのレコードとして復元される。行 ID は `m_EnemyWeakLockID.m_DBAccess` を通じて解決される。オフセット `+0x10` と `+0x20` は 2 つの真偽値ロックベクトルであり、`+0x30` と `+0x40` は弱点ロック状態異常を解除すべきか判定する際に使われる条件・パラメータベクトルである。

【一次 C 証拠】

##### ◇ C-WLOCK-01: キー検索と 80 バイト行幅

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

**解釈。** `LockID` は データベースキーであり、列挙型に対する `switch` の `case` ではない。行幅は 80 バイトである。

##### ◇ C-WLOCK-02: 武器／魔法ロックベクトルは `isMagic` によって選択される

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

**解釈。** `isMagic=false` は行 `+0x10` の `TArray<bool>` ヘッダを返し、`isMagic=true` は行 `+0x20` の `TArray<bool>` ヘッダを返す。これが、このテーブルに武器用と魔法用のロック配列が分かれて存在する直接的な理由である。

##### ◇ C-WLOCK-03: 直接のロック述語は、選択されたベクトルを境界チェックする

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

**解釈。** 武器ロックは `+0x10/+0x18` のベクトルポインタ／要素数を使用し、魔法ロックは `+0x20/+0x28` を使用する。`WeaponType` が非ゼロであれば武器側が優先され、そうでなければ非ゼロの `MagicType` が検査される。型インデックス `0` は、両分岐が非ゼロの型引数を要求するため、この述語では検査されない。これは、一部の特殊な弱点レーンが通常の武器／魔法アイコンと同じ形では表現されていない、というガイド上の注意と一致する。

##### ◇ C-WLOCK-04: 解除条件配列は行 `+0x30` と `+0x40`

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

**解釈。** 行は 16 バイトの `TArray<int>` ヘッダ列として見られているため、`EnemyWeakLockDB + 3` は行 `+0x30`、`EnemyWeakLockDB + 4` は行 `+0x40` を意味する。これらは弱点ロック専用の解除条件配列と解除パラメータ配列である。

##### ◇ C-WLOCK-05: 状態異常 ID `37` では、弱点ロックの解除条件が状態異常既定値を上書きする

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

**解釈。** 弱点ロック状態異常の解除は、`SkillAilmentDB` の既定解除条件だけには依存しない。状態異常 ID が `37` の場合、アベイルの `m_WeakLockID` によって、条件配列とパラメータ配列を `EnemyWeakLockID` 行へ差し替えることができる。

##### ◇ C-WLOCK-06: 有効な状態異常経路は `SkillAvailID.m_WeakLockID` を取得する

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

**解釈。** ロック ID のランタイム上の供給源は、有効な弱点ロック状態異常を発生させた スキルアベイル である。弱点変更 ID と同様に、戻り値の型名には 列挙型名が使われているが、その値はデータとして データベース検索へ渡される。引用したコードは列挙子に対する `switch` を実装していない。

##### ◇ C-WLOCK-07: UI のロック状態は同じ武器／魔法ベクトルを読む

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

**解釈。** UI 表示は別テーブルを参照していない。`GetLockedWeakFlags` が返した同じ 2 つのロックベクトルを受け取るため、武器／魔法配列への変更は、論理的なロック判定と表示上のロック状態の両方を変化させる。

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_id` | `FDbAccessHelper::GetArrayIndex(..., LockID)`、行幅 `80` | `m_EnemyWeakLockID` への正の整数キー。標準 列挙型ラベルは、実行時の網羅的な分岐ではない。 | 復元済み | `C-WLOCK-01` |
| 武器ロック配列 | 行 `+0x10/+0x18` | `WeaponType != 0` のときに検査される `TArray<bool>`。UI のロック表示にも武器ベクトルとして渡される。 | 復元済み | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| 魔法ロック配列 | 行 `+0x20/+0x28` | `WeaponType == 0 && MagicType != 0` のときに検査される `TArray<bool>`。UI のロック表示にも魔法ベクトルとして渡される。 | 復元済み | `C-WLOCK-02`, `C-WLOCK-03`, `C-WLOCK-07` |
| 解除条件 | 行 `+0x30` | 弱点ロック状態異常の解除に使われる `TArray<int>` の条件 ID。 | 復元済み | `C-WLOCK-04`, `C-WLOCK-05` |
| 解除パラメータ | 行 `+0x40` | 解除条件と組になる `TArray<int>` のパラメータ。 | 復元済み | `C-WLOCK-04`, `C-WLOCK-05` |
| 無属性／毒のロック | `IsLockedWeak` 内に特殊分岐は確認されない。 | 引用した述語は、保存された 2 つのベクトル内で非ゼロの武器種または魔法種インデックスのみを検査する。ここでは別個の特殊ケース用レーンは復元されていない。 | 否定的／部分的 | `C-WLOCK-03` |

### ■ (****) `EnemyID`
これはゲームにおける敵のマスターレコードである。敵のステータス、弱点、報酬、UI フラグを定義し、表示面（アニメーションセット、テクスチャ、オフセット、ボイス、エフェクト）は `EnemyTypeID` へ、敵 AI の挙動およびスキルセットは `TacticalAssignList` と `TacticalSkillList` へ接続する（下記「`AIBattle` 配下」参照）。
- `m_Label`: 敵のラベル。内部処理で使用されている可能性があるため、新規エントリを追加する場合は既存ラベルと衝突しないようにするのが望ましい。
- `m_DisplayName`: `GameTextEnemy.m_id` を参照する ID。すべてのローカライズで共通して用いられる。
- `m_WeakID`: `EnemyWeakID.m_id` を参照する ID。上記参照。
- `m_DisplayLevel`: 敵の表示レベル。ただし、`m_LevelUI` を `true` にしても実際には表示されない。
- `m_BreakRate`: ブレイク中の敵に与えるダメージへ乗算されるパーセント値。
- `m_DamageRate`: ブレイクしていない敵に与えるダメージへ乗算されるパーセント値。
- `m_MaxSLD`: 敵のシールド数。キー名だけを見ると最大値のように見えるが、一部の戦術やスキルはこれより高い値を設定できる（例: 栄光のリュシアン）。
- `m_MaxHP`: 敵の HP。上記と同様に、スキルや戦術によって増加し得る。
- `m_MaxSP`: 敵の SP。敵スキルの大半は SP 消費が 0 であるため、この値は主に特定の敵スキルの使用回数を制限するために使われる。
- `m_AtkP`: 敵の物理攻撃力。
- `m_AtkM`: 敵の属性攻撃力。
- `m_DefP`: 敵の物理防御力。
- `m_DefM`: 敵の属性防御力。
- `m_Agi`: 敵の速度／行動速度。
- `m_Crt`: 敵の会心値。
- `m_CrtDef`: 敵の会心防御値。このステータスを持つのは敵のみと見られる。
- `m_Hit`: 敵の命中値。
- `m_Avd`: 敵の回避値。
- `m_ResistAilmentID`: 状態異常耐性を管理する ID を敵へ割り当てる（`Skill/` 配下の `SkillResistAilmentID` を参照）。
- `m_ResistAilment`: 特定の状態異常に対する耐性率に対応する配列。割り当てられた `ResistAilmentID` 側の `m_ResistAilments` 配列が満杯の場合の補助的な値である可能性がある。使用例はキャットリン系にほぼ限られるように見える。
- `m_TacticalAssignID`: 敵に使用させる戦術リストに対応する `TacticalAssignList.m_id` を割り当てる。下記「`AIBattle` 配下」参照。
- `m_SkillsID`: 敵に使用させるスキルリストに対応する `TacticalSkillList.m_id` を割り当てる。下記「`AIBattle` 配下」参照。
- `m_IsBoss`: 多くのボスに加え、町中の NPC にも設定されているフラグ。機能上の意味はまだ確定していない。
- `m_Exp`, `m_Money`, `m_JP`: この敵を倒した後に得られる EXP、リーフ、JP。
- `m_PetExp`: 『オクトパストラベラー 大陸の覇者』と『オクトパストラベラー0』の双方で常に -1。-1 以外の値は効果を持たないように見える。
- `m_DropReward`: 敵がドロップする可能性のあるアイテム。利用可能な ID は `EnemyDropID` を参照。
- `m_StealReward`: 敵から盗めるアイテム。利用可能な ID は `Item/` 配下の `ItemList` を参照。
- `m_StealLeafNum`: 敵から盗めるリーフ数。
- `m_StatusOffset`: 敵のステータスバー（弱点とシールド数）の表示オフセット。
- `m_Bottle`: 意味は未確定。本作では 1〜3 の値が使われる。値 3 は本作ではワッパにのみ使われる。『オクトパストラベラー 大陸の覇者』では 4 と 5 も使用される。
- `m_NameUI`: 敵名を UI に表示するかどうか。
- `m_LevelUI`: 敵の `m_DisplayLevel` を UI に表示するかどうか（未使用、または実質的に機能していない）。
- `m_HpUI`: 本作では機能していない。
- `m_ShieldUI`: `true` にすると、敵のシールドアイコンが暗く表示されるように見える。
- `m_RaceIndices`: 敵に割り当てられる種族。ダメージ計算に影響し得る。
- `m_DisplaySpecialSkillGauge`: 敵の BP カウンターを表示するためのフラグ。
- `m_GenerateSpecialSkillValueOrverTurn`: 敵が 1 ターンごとに生成する BP 量。
- `m_ReduceSpecialSkillValueBreak`: ブレイク時に敵が失う BP 量。
- `m_MaxSpecialSkillValue`: 敵が保持できる BP の最大値。20 を超える値はゲームをクラッシュさせる。
- `m_OutsideTarget`: 敵をターゲット指定できなくする。その敵だけが場に残っている場合、味方キャラクターも行動をスキップする（『全てを授けし者』では、指輪が破壊されるまでこの状態を使う）。
- `m_NotAction`: `true` の場合、その敵は行動できない（『全てを授けし者』では、全ての指輪が倒される前に使用される）。
- `m_UnknownLv`: 敵のレベルを「??」として表示する。ただし敵レベル UI が存在しないため、未使用かつ視覚的には機能していない。とはいえ、未使用のパディングと断定してはならない。『オクトパストラベラー0』の逆コンパイル結果では、名前付きの `FEnemyIDBase` コピー処理／実体化処理がこれを実在する後半フラグとしてコピーしている（`Octopath_Traveler0-Win64-Shipping.exe.c`:17041778-17041806 および 17647345-17647360）。
- `m_Immortal`: `true` にすると、敵の HP は 1 未満にならない。

#### ■ 実行時の読み取り意味論（フィールド単位の Hex-Rays 証拠）
##### ◇ C-ENEMY-01: `EnemyID` 行の解決処理

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

##### ◇ C-ENEMY-02: 具体的な行オフセットを伴う `EnemyID` 実体化処理

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

##### ◇ C-ENEMY-03: `EnemyID` の後半状態フラグをバトルキャラクター側へ橋渡しする処理

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

##### ◇ C-ENEMY-04: `OutsideTarget`、`NotAction`、`Immortal` のランタイム変更処理および参照処理

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

##### ◇ C-ENEMY-05: `NotAction` と `OutsideTarget` のランタイム取得処理、および `Immortal` 設定処理

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

##### ◇ C-ENEMY-06: `RaceIndices` の所属判定

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

**【フィールド単位の解釈】**

| キー | ランタイム上の C 位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_Agi` | `C-ENEMY-02:17647319` `cEnemyListBase.m_Agi = *(_DWORD *)(v12 + 80);` | このキーは行オフセット `+0x50` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkM` | `C-ENEMY-02:17647317` `cEnemyListBase.m_AtkM = *(_DWORD *)(v12 + 72);` | このキーは行オフセット `+0x48` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_AtkP` | `C-ENEMY-02:17647315` `cEnemyListBase.m_AtkP = *(_DWORD *)(v12 + 64);` | このキーは行オフセット `+0x40` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Avd` | `C-ENEMY-02:17647323` `cEnemyListBase.m_Avd = *(_DWORD *)(v12 + 96);` | このキーは行オフセット `+0x60` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Bottle` | `C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | このキーは行オフセット `+0xC8` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_BreakRate` | `C-ENEMY-02:17647311` `cEnemyListBase.m_BreakRate = *(_DWORD *)(v12 + 48);` | このキーは行オフセット `+0x30` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CallSE` | `C-TYPE-02:17041873` `this->m_CallSE = __that->m_CallSE;` | これは `EnemyID` 行のフィールドではない。`EnemyID` セクションで言及される場合は、`EnemyID.m_TypeID -> EnemyTypeID +0x7C` を経由して到達する。 | 復元済み | `C-TYPE-02`, `C-TYPE-03` |
| `m_ChangeColorID` | `C-TYPE-02:17041854` `this->m_ChangeColorID = __that->m_ChangeColorID;` | これは `EnemyID` 行のフィールドではない。`GetEnemyTypeColorID` は `EnemyID.m_TypeID` を解決した後、型行 `EnemyTypeID +0x30` を読み取る。 | 復元済み | `C-TYPE-01`, `C-TYPE-02` |
| `m_Crt` | `C-ENEMY-02:17647320` `cEnemyListBase.m_Crt = *(_DWORD *)(v12 + 84);`<br>`C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | このキーは行オフセット `+0x54` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_CrtDef` | `C-ENEMY-02:17647321` `cEnemyListBase.m_CrtDef = *(_DWORD *)(v12 + 88);` | このキーは行オフセット `+0x58` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DamageRate` | `C-ENEMY-02:17647324` `cEnemyListBase.m_DamageRate = *(_DWORD *)(v12 + 100);` | このキーは行オフセット `+0x64` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefM` | `C-ENEMY-02:17647318` `cEnemyListBase.m_DefM = *(_DWORD *)(v12 + 76);` | このキーは行オフセット `+0x4C` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DefP` | `C-ENEMY-02:17647316` `cEnemyListBase.m_DefP = *(_DWORD *)(v12 + 68);` | このキーは行オフセット `+0x44` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayLevel` | `C-ENEMY-02:17647291` `memset(&cEnemyListBase.m_DisplayLevel, 0, 76);`<br>`C-ENEMY-02:17647309` `(TArray<unsigned short,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_DisplayLevel,`<br>`C-ENEMY-02:17647361` `if ( cEnemyListBase.m_DisplayLevel.Data.ArrayNum )` ほか 2 箇所の局所的な出現 | このキーは行オフセット `+0x20` の `FString`／UTF-16 配列ヘッダとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplayName` | `C-ENEMY-02:17647281` `FEnemyIDBase cEnemyListBase; // [rsp+20h] [rbp-E0h] BYREF`<br>`C-ENEMY-02:17647345` `cEnemyListBase.m_Bottle = *(_DWORD *)(v12 + 200);` | このキーは行オフセット `+0x14` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DisplaySpecialSkillGauge` | `C-ENEMY-02:17647353` `cEnemyListBase.m_DisplaySpecialSkillGauge = *(_BYTE *)(v12 + 224);` | このキーは行オフセット `+0xE0` のバイト／ブール値レーンとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_DropReward` | `C-ENEMY-02:17647336` `cEnemyListBase.m_DropReward = *(_DWORD *)(v12 + 156);` | このキーは行オフセット `+0x9C` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_EventDropRewards` | `C-ENEMY-02:17647340` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_EventDropRewards,` | このキーは行オフセット `+0xA8` の `TArray` ヘッダとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Exp` | `C-ENEMY-02:17647293` `memset(&cEnemyListBase.m_Exp, 0, 85);`<br>`C-ENEMY-02:17647332` `cEnemyListBase.m_Exp = *(_DWORD *)(v12 + 140);` | このキーは行オフセット `+0x8C` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_GenerateSpecialSkillValueOrverTurn` | `C-ENEMY-02:17647294` `*(_QWORD *)&cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = 0;`<br>`C-ENEMY-02:17647354` `cEnemyListBase.m_GenerateSpecialSkillValueOrverTurn = *(_DWORD *)(v12 + 228);` | このキーは行オフセット `+0xE4` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Hit` | `C-ENEMY-02:17647322` `cEnemyListBase.m_Hit = *(_DWORD *)(v12 + 92);` | このキーは行オフセット `+0x5C` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_HpUI` | `C-ENEMY-02:17647348` `cEnemyListBase.m_HpUI = *(_BYTE *)(v12 + 206);` | このキーは行オフセット `+0xCE` のバイト／ブール値レーンとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Immortal` | `C-ENEMY-02:17647360` `cEnemyListBase.m_Immortal = *(_BYTE *)(v12 + 243);` | `EnemyID +0xF3` のバイト値は、バトルキャラクターのセットアップ中に `SetImmortal` へ渡される。セットアップ後のランタイムコードは、データベース行を再読込するのではなく、キャラクターオブジェクト上の `m_IsImmortal` を読み書きする。 | 復元済み | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-05` |
| `m_IsBoss` | `C-ENEMY-02:17647331` `cEnemyListBase.m_IsBoss = *(_BYTE *)(v12 + 136);` | このキーは行オフセット `+0x88` のバイト／ブール値レーンとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_JP` | `C-ENEMY-02:17647335` `cEnemyListBase.m_JP = *(_DWORD *)(v12 + 152);` | このキーは行オフセット `+0x98` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Label` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | このキーは行オフセット `+0x0C` の `FName` として読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_LevelUI` | `C-ENEMY-02:17647347` `cEnemyListBase.m_LevelUI = *(_BYTE *)(v12 + 205);` | このキーは行オフセット `+0xCD` のバイト／ブール値レーンとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxHP` | `C-ENEMY-02:17647313` `cEnemyListBase.m_MaxHP = *(_DWORD *)(v12 + 56);` | このキーは行オフセット `+0x38` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSLD` | `C-ENEMY-02:17647312` `cEnemyListBase.m_MaxSLD = *(_DWORD *)(v12 + 52);` | このキーは行オフセット `+0x34` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSP` | `C-ENEMY-02:17647314` `cEnemyListBase.m_MaxSP = *(_DWORD *)(v12 + 60);` | このキーは行オフセット `+0x3C` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_MaxSpecialSkillValue` | `C-ENEMY-02:17647295` `*(_QWORD *)&cEnemyListBase.m_MaxSpecialSkillValue = 0;`<br>`C-ENEMY-02:17647356` `cEnemyListBase.m_MaxSpecialSkillValue = *(_DWORD *)(v12 + 236);` | このキーは行オフセット `+0xEC` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_Money` | `C-ENEMY-02:17647333` `cEnemyListBase.m_Money = *(_DWORD *)(v12 + 144);` | このキーは行オフセット `+0x90` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NameUI` | `C-ENEMY-02:17647346` `cEnemyListBase.m_NameUI = *(_BYTE *)(v12 + 204);` | このキーは行オフセット `+0xCC` のバイト／ブール値レーンとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_NotAction` | `C-ENEMY-02:17647358` `cEnemyListBase.m_NotAction = *(_BYTE *)(v12 + 241);` | `EnemyID +0xF1` のバイト値は `AQPBattleCharacterBase::m_IsNotAction` を初期化する。バトルイベントコマンドは `DisableNotAction` によってこの値を解除できるため、行の値は「行動不能」の初期状態を与えるシードである。この C エクスポートでは、ライブ側フラグはブール型として扱われている。 | 復元済み | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_OutsideTarget` | `C-ENEMY-02:17647357` `cEnemyListBase.m_OutsideTarget = *(_BYTE *)(v12 + 240);` | `EnemyID +0xF0` のバイト値は `AQPBattleCharacterBase::m_IsOutsideTarget` を初期化する。その後のターゲット選択コードはこのランタイムフラグを照会または解除するため、データベース側のフィールドは不変の恒久属性ではなく、初期状態のシードである。 | 復元済み | `C-ENEMY-02`, `C-ENEMY-03`, `C-ENEMY-04`, `C-ENEMY-05` |
| `m_PetExp` | `C-ENEMY-02:17647334` `cEnemyListBase.m_PetExp = *(_DWORD *)(v12 + 148);` | このキーは行オフセット `+0x94` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_RaceIndices` | `C-ENEMY-02:17647351` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_RaceIndices,`<br>`C-ENEMY-02:17647368` `Data = cEnemyListBase.m_RaceIndices.AllocatorInstance.Data;` | `m_RaceIndices` は `EnemyID +0xD0` に置かれた `TArray<int>` である。ランタイムの種族判定はスカラーの等価比較ではなく、`HasEnemyRace` が配列を走査し、いずれかの要素が要求された種族 ID と一致した場合に成功する。 | 復元済み | `C-ENEMY-02`, `C-ENEMY-06` |
| `m_ReduceSpecialSkillValueBreak` | `C-ENEMY-02:17647355` `cEnemyListBase.m_ReduceSpecialSkillValueBreak = *(_DWORD *)(v12 + 232);` | このキーは行オフセット `+0xE8` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilment` | `C-ENEMY-02:17647292` `memset(&cEnemyListBase.m_ResistAilment, 0, 25);`<br>`C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);`<br>`C-ENEMY-02:17647327` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_ResistAilment,` | このキーは行オフセット `+0x70` の `TArray` ヘッダとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilmentID` | `C-ENEMY-02:17647325` `cEnemyListBase.m_ResistAilmentID = *(_DWORD *)(v12 + 104);` | このキーは行オフセット `+0x68` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_ResistAilments` | 提供された C エクスポート内では、厳密な名前付きフィールドまたは曖昧でないオフセットレーンは復元されていない。 | 提供された C エクスポート内では、厳密な `m_ResistAilments` シンボル、または当該アセットに固有であると断定できるオフセットレーンは復元されなかった。そのため、このフィールドは推測せず、未復元として記録する。 | 否定的／部分的 | 未復元 |
| `m_ShieldUI` | `C-ENEMY-02:17647349` `cEnemyListBase.m_ShieldUI = *(_BYTE *)(v12 + 207);` | このキーは行オフセット `+0xCF` のバイト／ブール値レーンとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | このキーは行オフセット `+0x84` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StatusOffset` | `C-ENEMY-02:17647343` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&cEnemyListBase.m_StatusOffset,`<br>`C-ENEMY-02:17647369` `v8 = cEnemyListBase.m_StatusOffset.AllocatorInstance.Data;` | このキーは行オフセット `+0xB8` の `TArray` ヘッダとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealLeafNum` | `C-ENEMY-02:17647338` `cEnemyListBase.m_StealLeafNum = *(_DWORD *)(v12 + 164);` | このキーは行オフセット `+0xA4` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_StealReward` | `C-ENEMY-02:17647337` `cEnemyListBase.m_StealReward = *(_DWORD *)(v12 + 160);` | このキーは行オフセット `+0xA0` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_TacticalAssignID` | `C-ENEMY-02:17647329` `cEnemyListBase.m_TacticalAssignID = *(_DWORD *)(v12 + 128);` | このキーは行オフセット `+0x80` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_UnknownLv` | `C-ENEMY-02:17647359` `cEnemyListBase.m_UnknownLv = *(_BYTE *)(v12 + 242);` | このキーは行オフセット `+0xF2` のバイト／ブール値レーンとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |
| `m_VoiceSymbol` | `C-TYPE-02:17041874` `this->m_VoiceSymbol = __that->m_VoiceSymbol;` | これは `EnemyID` 行のフィールドではない。ランタイムは `EnemyID.m_TypeID` を解決し、その後 `m_VoiceSymbol` として `EnemyTypeID +0x80` を読み取る。 | 復元済み | `C-TYPE-02`, `C-TYPE-03` |
| `m_WeakID` | `+0x1C` （スカラー）。引用したブロック内には、より短い名前付き代入は残っていない | このキーは行オフセット `+0x1C` の 32 ビットスカラーとして読み取られる。`UEnemyDB::GetEnemyDB` はストライド `0xF8` で行を解決し、`ASymbolEnemy::GetTargetLevel` はこれらのオフセットを明示的に実体化する。 | 復元済み | `C-ENEMY-01`, `C-ENEMY-02` |

#### 追加ランタイム証拠: `EnemyID` の直接アクセサ、ステータス種別マッピング、および逆引きインデックス

`EnemyID` テーブルは、行全体としてコピーされるだけではない。バトルおよび UI コードの多くは、小さなアクセサを通じて個別フィールドを読み取る。これらのアクセサは行オフセットを独立に検証するうえで有用であり、どのフィールドがスカラー値、配列、または二次テーブル ID として問い合わせられるかも示している。
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

| キーまたはキー群 | ランタイム上の解釈 |
|---|---|
| `m_DisplayName` | 行 `+0x14` として読み取られる。`GetEnemyNameString` はこれを `AGameTextManager::GetGameText(..., 4u, DisplayName, 0)` に渡す。したがって、この経路ではゲームテキストの名前空間／カテゴリが `4u` としてハードコードされている。 |
| `m_TypeID` | 行 `+0x18` として読み取られる。`withID` のデバッグ／表示分岐では、接頭辞に用いる正の具体的な `EnemyID` が優先されない場合にのみ使われる。ゲームプレイ上では、`EnemyTypeID` への橋渡しでもある。 |
| `m_MaxHP`, `m_MaxSP`, `m_MaxSLD`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_Hit`, `m_Avd` | `GetEnemyStatus` はステータス ID を行内の `uint32` スロットへ対応付ける。確認できる対応は、`1 -> m_MaxHP`, `2 -> m_MaxSP`, `4 -> m_MaxSLD`, `9 -> m_AtkP`, `10 -> m_DefP`, `11 -> m_AtkM`, `12 -> m_DefM`, `13 -> m_Agi`, `14 -> m_Crt`, `15 -> m_Hit`, `16 -> m_Avd` である。`m_CrtDef` は行内に存在するが、この特定のステータス種別スイッチからは到達しない。 |
| `m_StealReward` | 行 `+0xA0` として読み取られる。ブール値を返すオーバーロードは、コピーした ID が正の場合にのみ成功を返す。 |
| `m_StealLeafNum` | 行 `+0xA4` として読み取られる。ブール値を返すオーバーロードは、コピーしたリーフ数が正の場合にのみ成功を返す。 |
| `m_TacticalAssignID` | 行 `+0x80` として読み取られ、戦術割当の参照 ID として扱われる。 |
| `m_SkillsID` | 行 `+0x84` として読み取られ、敵の `TacticalSkillList` 参照 ID として扱われる。スキル所属確認では、この ID でスキル配列を解決したうえで走査する。 |

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

| キー | ランタイム上の解釈 |
|---|---|
| `m_RaceIndices` | 行が所有する配列 `+0xD0/+0xD8` として格納される。`HasEnemyRace` は配列の所属判定であり、`GetSameRaceEnemies` はこのフィールドを走査して敵 ID の結果配列を構築する。したがって、これはスカラーの種族列挙型ではない。 |
| `m_TypeID` | `GetSameTypeEnemies` は全ての `EnemyID` 行を走査し、行 `+0x18` によって敵をグループ化する。これにより、型 ID は表示情報へのポインタであるだけでなく、大域的な分類キーでもあることが分かる。 |


#### `EnemyID` はバトルキャラクター生成時のシードレコードである

上記のフィールド表により、行レイアウトは確定している。以下の追加経路は、改造作業で重要になる高水準のランタイムセマンティクスを示している。すなわち、敵の構築、UI 表示可否、ステータス参照、レベル／名前のローカライズ、弱点フラグ初期化、ドロップ／盗む、種族／型の逆引き走査、特殊ゲージの変更、初期状態フラグである。これらの経路では、ゲームは `EnemyID` を `AQPBattleCharacterBase` インスタンス初期化のシードとして扱う。複数のフィールドは一度ランタイム状態へコピーされた後、データベース行とは独立して変更される。

##### ◇ C-ENEMY-CONSTRUCTION-01: 敵構築時に表示情報、弱点フラグ、パラメータ、不死／対象外／行動不能状態を初期化する処理

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

解釈： `SetupEnemy` は `UEnemyDB::GetEnemyDB` を通じて `EnemyID` を一度読み取る。`v12` は解決済みの行である。その行は、永続的なバトルキャラクター側フィールドを初期化するために使われる。`m_TypeID` は表示処理のセットアップを駆動する。`m_WeakID` はここで直接コピーされるのではなく、武器／魔法の各インデックスについて `AQPBattleManager::GetEnemyWeakFlags` を問い合わせることで弱点フラグが算出され、その先で弱点テーブルが解決される。`m_Immortal`、`m_OutsideTarget`、`m_NotAction` は、それぞれ `+0xF3`、`+0xF0`、`+0xF1` の初期状態シードである。シード後、ライブのキャラクターフィールドはスキルやイベントによって独立に変更され得る。

##### ◇ C-ENEMY-STATE-RESET-02: 敵行フラグ適用前に `SetupParams` がライブ状態を初期化する処理

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

解釈： `SetupParams` は、`SetupEnemy` がデータベース由来の値を書き込む前に、`m_IsOutsideTarget`、隣接する行動不能バイト、不死フラグを意図的にクリアする。この順序は重要である。データベース側のフィールドは不変の述語ではなく、汎用リセット後にキャラクターの開始時バトル状態を初期化する値である。その後のコードは、データベース行を変更せずに同じランタイムフラグを解除または設定できる。

##### ◇ C-ENEMY-DISPLAY-TEXT-03: 表示名とレベル文字列は別機構である

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

解釈： `m_DisplayName` は行 `+0x14` であり、`GameText` カテゴリ `4` を通じて解決される。したがって、これは生文字列ではなくローカライズ ID である。`m_DisplayLevel` は行 `+0x20` の `FString`／UTF-16 配列であり、`GetEnemyLevel` は `_wtoi` によってこれを解析する。このため、表示レベルが非数値文字列の場合、行の実体化処理がテキストを保持していても、キャラクターレベルを消費する経路では `0` になる。

##### ◇ C-ENEMY-STATUS-SWITCH-04: ステータス参照は連続公開配列ではなく、ステータス ID のスイッチである

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

解釈： 主要なスカラー・ステータス参照関数は、`ECHARA_STATUS` に類似する ID を行内の 32 ビット値 へ対応付ける。対応は `1 -> m_MaxHP`, `2 -> m_MaxSP`, `4 -> m_MaxSLD`, `9 -> m_AtkP`, `10 -> m_DefP`, `11 -> m_AtkM`, `12 -> m_DefM`, `13 -> m_Agi`, `14 -> m_Crt`, `15 -> m_Hit`, `16 -> m_Avd` である。`m_CrtDef` は `+0x58` に存在し、コピー／実体化もされるが、この特定の参照関数はそれを経由しない。したがって、物理的に近接している、または実体化されているという理由だけで、全てのステータスフィールドが `GetEnemyStatus` から参照可能だと推論してはならない。

##### ◇ C-ENEMY-UI-FLAGS-05: UI 表示フラグには、それぞれ独立した読取関数と追加のライブ状態条件がある

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

解釈： `m_HpUI`、`m_LevelUI`、`m_ShieldUI`、`m_DisplaySpecialSkillGauge` は、それぞれ `+0xCE`、`+0xCD`、`+0xCF`、`+0xE0` から独立に読み取られる。行が存在しない場合、HP／レベル／シールドの関数は表示あり（`1`）を既定値とするが、特殊ゲージ表示は `false` を既定値とする。シールド表示はさらに `HasSLD(this)` によってゲートされるため、ライブ側にシールド状態が存在しない場合、`m_ShieldUI=true` を設定するだけでは十分でない。

##### ◇ C-ENEMY-SPECIAL-GAUGE-06: 敵の特殊ゲージ関連フィールドは変更処理の時点で読み取られる

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

解釈： `m_MaxSpecialSkillValue`、`m_ReduceSpecialSkillValueBreak`、`m_GenerateSpecialSkillValueOrverTurn` は有効なランタイムパラメータである。構築済みの敵へ単にコピーされるだけではない。ゲージ変更ルーチンは、加算時、ブレイク時の減算時、ターン経過による回復時に `EnemyID` 行を再問い合わせする。したがって、これらのデータベース値の変更は、この取得関数群を呼ぶ計算経路に影響する。一方で、現在のライブゲージ値そのものは、別個のキャラクター／セーブ状態として保持される。

##### ◇ C-ENEMY-REWARD-STEAL-07: 報酬および「盗む」関連フィールドには直接の取得経路がある

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

解釈： 報酬および「盗む」関連フィールドはスキーマ上の飾りではない。`m_Exp`、`m_Money`、`m_JP` には、それぞれ `+0x8C`、`+0x90`、`+0x98` の単純なスカラー取得関数がある。`+0x9C` の `m_DropReward` は `EnemyDropID` の行 ID である。`+0xA0` の `m_StealReward` と `+0xA4` の `m_StealLeafNum` にはブール値を返すオーバーロードがあり、コピーした値が正でない限り `false` を返す。したがって、そのオーバーロードにおいて `0` や負の値は「存在しない」ことを意味する。

##### ◇ C-ENEMY-REVERSE-INDEX-08: `m_RaceIndices` と `m_TypeID` は逆引き走査を支える

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

解釈： `m_RaceIndices` はスカラーの種族フィールドではない。種族所属は `HasEnemyRace(enemyId, raceId)` を通じて判定され、その後、派生した敵 ID 配列を構築するために使われる。同様に、`m_TypeID` は表示情報へのポインタであるだけではない。`GetSameTypeEnemies` は全ての `EnemyID` 行を完全走査し、行 `+0x18` を要求された型 ID と比較する。これにより、`m_TypeID` は表示リンクであると同時に型分類キーでもある。

##### ◇ C-ENEMY-OFFSETMAP-09: `EnemyID` 行レイアウトと利用箇所分類の簡易表

| オフセット | キー | 証拠の強度 | ランタイム上の解釈 |
|---:|---|---|---|
| `+0x0C` | `m_Label` | ラベル索引／検索証拠 | `FDbAccessHelper` のラベルマップで使用される。ラベルから ID への検索は行の `m_id` を返す。 |
| `+0x14` | `m_DisplayName` | 直接利用 | ゲームテキスト ID。`AGameTextManager::GetGameText(..., 4u, DisplayName, 0)` として解決される。 |
| `+0x18` | `m_TypeID` | 直接利用 | 全ての `EnemyTypeID` 表示データへの橋渡し。さらに同一型の逆引き走査にも使われる。 |
| `+0x1C` | `m_WeakID` | 弱点フラグ／耐性経路を介した直接利用 | 明示的な弱点オーバーライドが指定されない場合の既定弱点行。 |
| `+0x20` | `m_DisplayLevel` | 直接利用 | `_wtoi` で解析される UTF-16 文字列。行内では単純な整数ではない。 |
| `+0x30/+0x64` | `m_BreakRate`, `m_DamageRate` | 直接取得関数／実体化済みスカラー | ダメージ倍率パラメータ。`m_BreakRate` には直接の取得関数がある。 |
| `+0x34..+0x60` | `m_MaxSLD`, `m_MaxHP`, `m_MaxSP`, `m_AtkP`, `m_DefP`, `m_AtkM`, `m_DefM`, `m_Agi`, `m_Crt`, `m_CrtDef`, `m_Hit`, `m_Avd` | 直接ステータススイッチおよび実体化 | 大半のフィールドは `GetEnemyStatus` から返される。`m_CrtDef` は実体化されるが、提示したスイッチからは到達しない。 |
| `+0x68/+0x70` | `m_ResistAilmentID`, `m_ResistAilment` | 実体化／隣接するスキル耐性証拠 | 実在する行フィールド。ただし、状態異常耐性の厳密な消費経路は、弱点／耐性の消費経路ほど直接的ではない。 |
| `+0x80/+0x84` | `m_TacticalAssignID`, `m_SkillsID` | 直接取得関数および戦術スキル所属判定 | 戦術およびスキルリストの ID。`m_SkillsID` は `TacticalSkillList` を通じて解決される。 |
| `+0x88` | `m_IsBoss` | 直接取得関数／実体化済みバイト | ボスフラグ。ボス条件および表示経路で使われるが、挙動は文脈依存のままである。 |
| `+0x8C/+0x90/+0x94/+0x98` | `m_Exp`, `m_Money`, `m_PetExp`, `m_JP` | EXP／リーフ／JP の直接取得関数、`PetExp` は実体化済み | リザルト報酬。`PetExp` は実体化されるものの、ゲームプレイ上の証拠は不完全である。 |
| `+0x9C/+0xA0/+0xA4` | `m_DropReward`, `m_StealReward`, `m_StealLeafNum` | 直接利用 | ドロップテーブル ID と「盗む」関連値。「盗む」のオーバーロードは正の値を要求する。 |
| `+0xA8` | `m_EventDropRewards` | 直接配列取得関数およびドロップ上書き証拠 | イベントドロップ上書き配列。ドロップリザルト処理で消費される。 |
| `+0xB8` | `m_StatusOffset` | UI オフセットの直接取得関数 | バトル中のステータス表示位置に使われる 2 要素の浮動小数点ベクトル。 |
| `+0xC8` | `m_Bottle` | 実体化／グループスロット相関証拠 | 実在する行スカラー。現時点で最も強い解釈は、多部位敵の序数、またはパネル部位インデックスである。 |
| `+0xCC/+0xCD/+0xCE/+0xCF` | `m_NameUI`, `m_LevelUI`, `m_HpUI`, `m_ShieldUI` | レベル／HP／シールドは直接利用、名前の利用箇所は不完全 | レベル／HP／シールドは UI 表示可否を変更する。`m_NameUI` は実体化されるが、復元済みの関数群では同様の直接取得関数パターンが見つかっていない。 |
| `+0xD0` | `m_RaceIndices` | 直接所属判定および逆引き走査利用 | 種族 ID の配列であり、スカラー列挙型ではない。 |
| `+0xE0/+0xE4/+0xE8/+0xEC` | `m_DisplaySpecialSkillGauge`, `m_GenerateSpecialSkillValueOrverTurn`, `m_ReduceSpecialSkillValueBreak`, `m_MaxSpecialSkillValue` | UI および変更処理における直接利用 | 敵 BP／特殊ゲージの表示および変更パラメータ。 |
| `+0xF0/+0xF1/+0xF2/+0xF3` | `m_OutsideTarget`, `m_NotAction`, `m_UnknownLv`, `m_Immortal` | `OutsideTarget`／`NotAction`／`Immortal` は直接セットアップシード、`UnknownLv` は実体化済みかつ過去メモに UI 経路証拠あり | 初期バトルキャラクターフラグ。`OutsideTarget`／`NotAction`／`Immortal` は `SetupParams` のリセット後に明示的にシードされる。`UnknownLv` の正確な消費箇所は、レベル UI 経路を実機検証しない限り保守的に扱うべきである。 |

### ■ (**) `EnemyReinforcements`
戦闘中に追加で出現させる敵（増援）と、その出現位置を定義するバトル遭遇補助テーブル。
- `m_Reinforcements`: `EnemyID.m_id` を入力として受け取る。
- `m_Positions`: `BattlePositions.m_id` を入力として受け取る（対象の敵グループ側フォーメーションに、この位置が含まれている必要はない）。
`EnemyReinforcements.m_id` を使用するアセットは、`Skill/` 配下の `SkillAvailID` のみである。
#### ■ 実行時の読み取り意味論
##### ◇ C-REINF-01: `EnemyReinforcements` 行の解決処理

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_Positions` | 行所有の `TArray`。デストラクタは `this->m_Positions` を解放する （配列）。引用したブロック内には、より短い名前付き代入は残っていない。 | 行解決処理は、行ストライド `0x38` の `EnemyReinforcements` 行を返す。デストラクタ／コピーシンボルにより、`m_Positions` が対応する行所有の `TArray` として保持されていることが分かる。正確な出現処理側の利用箇所はここでは復元されていない。 | 未復元／部分的 | `C-REINF-01` |
| `m_Reinforcements` | 行所有の `TArray`。デストラクタは `this->m_Reinforcements` を解放する （配列）。引用したブロック内には、より短い名前付き代入は残っていない。 | 行解決処理は、行ストライド `0x38` の `EnemyReinforcements` 行を返す。デストラクタ／コピーシンボルにより、`m_Reinforcements` が行所有の `TArray` として保持されていることが分かる。`SkillAvailID` より下流にある正確なゲームプレイ側の利用箇所は、これらの抜粋では復元されていない。 | 未復元／部分的 | `C-REINF-01` |

### ■ (****) `EnemyGroups`
すべての敵グループを記録するテーブルであり、主に次の内容を決定する。
- どの `EnemyID` が同時に出現するか。
- フォーメーション、およびバトルイベント・中断条件の文脈。

独自バトルをゲーム内で発生させるには、このテーブルにエントリを追加する必要がある。

- `m_Label`: 新規に追加する敵グループでは、このラベルが既存エントリと衝突しないようにすること。イベントはこのラベルを用いて敵グループとの戦闘を開始する。モンスター闘技場にエントリを追加する場合、そのエントリを他の用途で再利用すると、撃破後にモンスター闘技場メニューが表示されたり、クラッシュを引き起こしたりする可能性がある。
- `m_Inescapable`: 戦闘から逃走できるかどうかを決定するフラグ。
- `m_DisableRetire`: 『オクトパストラベラー0』では常に `false` に設定されており、本作では機能していないように見える。『オクトパストラベラー 大陸の覇者』における戦闘中の中断・復帰機能に関連する可能性がある。
- `m_SkipResult`: 戦闘結果画面（獲得 EXP、リーフ、JP など）をスキップするかどうかを決定するフラグ。
- `m_IgnoreDefeat`: この敵グループに敗北した場合にゲームオーバー画面を表示しないようにするフラグ。パーディス三世、巨富／権勢／名誉を護りしエドラス兵、巨腐蟻でのみ使用されている。敵がモンスター闘技場内にいる場合はこの挙動が上書きされ、敗北後に闘技場へ戻されるようである。
- `m_SkipBgmResult`: 勝利テーマを再生するかどうかを決定するフラグ。
- `m_BanGodBeast`: 戦闘中の神獣の使用を無効化する。『オクトパストラベラー0』では未使用。改造で何らかのキャラクターのスキルボードに神獣スキルを追加した場合、このフラグがそれを阻止する可能性がある（未検証）。
- `m_PlayerMemberSetID`: 参照先の `PlayerMemberSetID` で指定されたキャラクターでパーティを強制的に構成する（[カスタムキャラクターの追加方法](customchars_en.md) も参照）。
- `m_Bgm`: 戦闘中の BGM を設定する。利用可能な値は `Sound/` 配下の `SoundList` を参照。
- `m_NightBgm`: 夜間の戦闘 BGM を設定する。『オクトパストラベラー0』では未使用であり、機能するかどうかも未検証。
- `m_DarkBgm`: `m_NightBgm` と同じ用途の可能性がある。『オクトパストラベラー 大陸の覇者』のエントリでは、両キーが常に同じ値を持つように見える。
- `m_BgmType`: 正確な用途は不明だが、`Kingship_structs.hpp` では次のように定義されている。

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

- `m_ResultBgmWin`: 独自の勝利テーマを指定する。
- `m_ResultBgmLose`: 独自のゲームオーバーテーマを指定する。
- `m_CameraSet`: 使用するカメラセットを指定する（入力値は `BattleCameraSet.m_id`）。古いメモでは `m_Camera` と呼ばれている場合があるが、型名が復元された C エクスポートの実体化処理では `m_CameraSet` が使用されている（`Octopath_Traveler0-Win64-Shipping.exe.c`:17923518-17923533）。
- `m_Formation`: 敵グループのフォーメーションを指定する（入力値は `EnemyFormations.m_id`）。
- `m_EnemyID`: フィールド上に出現する敵を指定する（入力値は `EnemyID.m_id`）。この配列を 6 体より多く拡張しても即座にはクラッシュしないが、追加された敵はタイムラインに表示されず、弱点・シールド・状態表示のオーバーレイも持たず、その敵にターンが回った後にゲームがクラッシュする（破損したアロケータメタデータまたは破損ポインタに関係するエラー）。増援呼び出しによって回避できる可能性はある。現時点では、エンジン上の厳密な上限や安全な回避策として記述する前に、現在の環境で再現確認すべき編集上の危険として扱うこと。

- `m_EventIndices`: 戦闘開始時または戦闘中に再生される（バトル）イベント（戦闘中会話など）。指定値は `EventList.m_id` ではなく `BattleEventList.m_id` である可能性が高い。
- `m_AbortCondition`: 戦闘を中断するための条件。利用可能な ID は `BattleAbortConditions` にある可能性が高い。
- `m_DefeatPriority`: 『オクトパストラベラー0』および『オクトパストラベラー 大陸の覇者』では常に `false`。実際に何らかの機能を持つかは不明。
- `m_ForcedWinAbortCondition`: 戦闘を強制的に勝利扱いにするための独自条件を設定する。利用可能な ID は `BattleAbortConditions` にある可能性が高い。
- `m_IsAllEnemyDeadOnForceWin`: （未検証）`m_ForcedWinAbortCondition` で設定した条件を満たした際、全敵を死亡させるかどうかを決定するフラグと思われる。
- `m_ForcedLoseAbortCondition`: 戦闘を強制的に敗北扱いにするための独自条件を設定する。利用可能な ID は `BattleAbortConditions` にある可能性が高い。
- `m_ImportantFlags`: 6 個のフラグ。本作ではいずれも未使用であり、検証から意味を特定できなかった。配列長が `m_EnemyID` と同じであることから、6 個のフラグ間の差異は、どのフィールド上の敵に適用するかという点以外には存在しない可能性が高い。
  - フラグ 1: 主に『オクトパストラベラー 大陸の覇者』のジョブの塔系ボスで使用される。
  - フラグ 2: `Dice_Bolaven_Rank04_BossGr03`（イードル、ならず者のリヴェン兵 I、ならず者のリヴェン兵 II との戦闘）でのみ使用される。
  - フラグ 3: フラグ 2 と同じ敵グループでのみ使用される。
  - フラグ 4〜6: どちらの作品でも使用されていない。
-  `m_PetDear`: 値として -1、0、1 を取る。
-  `m_DisableBattleEndWipe`: 多くのラスボス級戦闘（『全てを授けし者』、オル・ガルデラ、サイドソリスティア編のガルデラ）および魔導機兵で使用される。
-  `m_DisableBattleEndTraining`: 訓練場のパーティメンバーが経験値、JP などを獲得しないようにする。蒼翠の大狼戦（ローラナがフィールド上にいる場合のみ）、サザントスの回想中に発生する『全てを授けし者』第 7 章の戦闘などで使用される。
-  `m_DisableSupporter`: 戦闘中に支援者を呼び出せるかどうかを決定するフラグ。
-  `m_EncountEffectTypeID`: 特殊なエンカウント演出（例: FF6 コラボのエンカウント）を指定する。闘技場バトルではこれを 0 に設定する必要があり、そうしないとゲームがソフトロックする。利用可能な ID は `EncountEffectTypeID` にある。SDK では、0=なし、2=ホワイトアウト、3=ブラックアウト と定義されている。
-  `m_EnablePlayerShield`: 『オクトパストラベラー0』および『オクトパストラベラー 大陸の覇者』のどちらでも常に `false`。
-  `m_DisableGrade`: 『オクトパストラベラー0』では常に `false` だが、『オクトパストラベラー 大陸の覇者』ではそうではない。武器のグレードを無視するためのフラグである可能性が高い。
-  `m_IsContainRareEnemy`: どの敵グループがレア敵を含むと見なされるかをゲームに通知するフラグと思われる。これにより、レア敵の遭遇率を上げるアクセサリの効果対象になる可能性がある。

#### ■ 実行時の読み取り意味論
このテーブルについて以前記載されていたアドレス単位のメモは、型情報つき C エクスポート内の `FEnemyGroupsBase`、`UEnemyDB`、`ASymbolEnemy` による証拠で置き換えられる。グループの実体化処理、グループ解決処理、シンボルエネミーのスロット選択経路はいずれも下で引用しているため、この節にアドレスのみを根拠とする主張を残す必要はない。

【一次 C 証拠】

##### ◇ C-GROUP-01: `EnemyGroups` 行の解決処理

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

##### ◇ C-GROUP-02: 具体的な行オフセットを伴う `EnemyGroups` の実体化処理

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

##### ◇ C-GROUP-03: `EnemyGroups` の直接的なスカラー利用箇所

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

##### ◇ C-SYMBOL-03: シンボルエネミーのランタイム代入、および `GroupId`/`Index` から `EnemyGroups` への橋渡し

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_AbortCondition` | `C-GROUP-02:17923540` `outData->m_AbortCondition = *(_DWORD *)(v18 + 88);` | このキーは、行オフセット `+0x58` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_BanGodBeast` | `C-GROUP-02:17923526` `outData->m_BanGodBeast = *(_BYTE *)(v18 + 25);` | このキーは、行オフセット `+0x19` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_Bgm` | `C-GROUP-02:17923528` `outData->m_Bgm = *(_DWORD *)(v18 + 32);`<br>`C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | このキーは、行オフセット `+0x20` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_BgmType` | `C-GROUP-02:17923531` `outData->m_BgmType = *(_DWORD *)(v18 + 44);` | このキーは、行オフセット `+0x2C` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_Camera` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | 『オクトパストラベラー0』の `EnemyGroups` では `m_Camera` フィールドは復元されていない。実体化処理では、行 `+0x30` のライブフィールド名が `m_CameraSet` になっているため、`m_Camera` は旧メモまたは古い別名として扱うべきである。 | 復元済み | `C-GROUP-02` |
| `m_CameraSet` | `C-GROUP-02:17923532` `outData->m_CameraSet = *(_DWORD *)(v18 + 48);` | このキーは、行オフセット `+0x30` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_DarkBgm` | `C-GROUP-02:17923530` `outData->m_DarkBgm = *(_DWORD *)(v18 + 40);` | このキーは、行オフセット `+0x28` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_DefeatPriority` | `C-GROUP-02:17923541` `outData->m_DefeatPriority = *(_BYTE *)(v18 + 92);` | このキーは、行オフセット `+0x5C` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndTraining` | `C-GROUP-02:17923550` `outData->m_DisableBattleEndTraining = *(_BYTE *)(v18 + 133);` | このキーは、行オフセット `+0x85` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableBattleEndWipe` | `C-GROUP-02:17923549` `outData->m_DisableBattleEndWipe = *(_BYTE *)(v18 + 132);` | このキーは、行オフセット `+0x84` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableGrade` | `C-GROUP-02:17923554` `outData->m_DisableGrade = *(_BYTE *)(v18 + 141);` | このキーは、行オフセット `+0x8D` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableRetire` | `C-GROUP-02:17923522` `outData->m_DisableRetire = *(_BYTE *)(v18 + 21);` | このキーは、行オフセット `+0x15` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_DisableSupporter` | `C-GROUP-02:17923551` `outData->m_DisableSupporter = *(_BYTE *)(v18 + 134);` | このキーは、行オフセット `+0x86` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnablePlayerShield` | `C-GROUP-02:17923553` `outData->m_EnablePlayerShield = *(_BYTE *)(v18 + 140);` | このキーは、行オフセット `+0x8C` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_EncountEffectTypeID` | `C-GROUP-02:17923552` `outData->m_EncountEffectTypeID = *(_DWORD *)(v18 + 136);` | このキーは、行オフセット `+0x88` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_EnemyID` | `C-GROUP-02:17923535` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EnemyID,` | このキーは、行オフセット `+0x38` の `TArray` ヘッダとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_EventIndices` | `C-GROUP-02:17923538` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outData->m_EventIndices,` | このキーは、行オフセット `+0x48` の `TArray` ヘッダとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedLoseAbortCondition` | `C-GROUP-02:17923544` `outData->m_ForcedLoseAbortCondition = *(_DWORD *)(v18 + 104);` | このキーは、行オフセット `+0x68` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_ForcedWinAbortCondition` | `C-GROUP-02:17923542` `outData->m_ForcedWinAbortCondition = *(_DWORD *)(v18 + 96);` | このキーは、行オフセット `+0x60` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_Formation` | `C-GROUP-02:17923533` `outData->m_Formation = *(_DWORD *)(v18 + 52);` | このキーは、行オフセット `+0x34` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_IgnoreDefeat` | `C-GROUP-02:17923524` `outData->m_IgnoreDefeat = *(_BYTE *)(v18 + 23);` | このキーは、行オフセット `+0x17` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_ImportantFlags` | `C-GROUP-02:17923546` `(TArray<enum ERigControlTransformChannel,TSizedDefaultAllocator<32> > *)&outData->m_ImportantFlags,` | このキーは、行オフセット `+0x70` の `TArray` ヘッダとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_Inescapable` | `C-GROUP-02:17923521` `outData->m_Inescapable = *(_BYTE *)(v18 + 20);` | このキーは、行オフセット `+0x14` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsAllEnemyDeadOnForceWin` | `C-GROUP-02:17923543` `outData->m_IsAllEnemyDeadOnForceWin = *(_BYTE *)(v18 + 100);` | このキーは、行オフセット `+0x64` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_IsContainRareEnemy` | `C-GROUP-02:17923555` `outData->m_IsContainRareEnemy = *(_BYTE *)(v18 + 142);` | このキーは、行オフセット `+0x8E` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_Label` | `C-GROUP-02:17923520` `outData->m_Label = *(FName *)(v18 + 12);` | このキーは、行オフセット `+0x0C` の `FName` として読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_NightBgm` | `C-GROUP-02:17923529` `outData->m_NightBgm = *(_DWORD *)(v18 + 36);` | このキーは、行オフセット `+0x24` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_PetDear` | `C-GROUP-02:17923548` `outData->m_PetDear = *(_DWORD *)(v18 + 128);` | このキーは、行オフセット `+0x80` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_PlayerMemberSetID` | `C-GROUP-02:17923527` `outData->m_PlayerMemberSetID = *(_DWORD *)(v18 + 28);` | このキーは、行オフセット `+0x1C` の 32 ビットスカラーとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_ResultBgmLose` | 提供された C エクスポートでは、正確な名前付きフィールドまたは明確なオフセットレーンは復元されていない。 | C エクスポート内では、`m_ResultBgmLose` という正確なシンボルや、アセット固有と断定できる明確なオフセットは復元されていない。したがって、このフィールドは推定ではなく未復元として記録する。 | 未復元／部分的 | 未復元 |
| `m_ResultBgmWin` | 提供された C エクスポートでは、正確な名前付きフィールドまたは明確なオフセットレーンは復元されていない。 | C エクスポート内では、`m_ResultBgmWin` という正確なシンボルや、アセット固有と断定できる明確なオフセットは復元されていない。したがって、このフィールドは推定ではなく未復元として記録する。 | 未復元／部分的 | 未復元 |
| `m_SkipBgmResult` | `C-GROUP-02:17923525` `outData->m_SkipBgmResult = *(_BYTE *)(v18 + 24);` | このキーは、行オフセット `+0x18` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |
| `m_SkipResult` | `C-GROUP-02:17923523` `outData->m_SkipResult = *(_BYTE *)(v18 + 22);` | このキーは、行オフセット `+0x16` のバイト／ブール値レーンとして読み取られる。グループ行は `GroupID` により、行ストライド `0x90` で解決され、`FDbAccessHelper<FEnemyGroupsBase>::GetData` がこのレーンをそのまま `outData` へコピーする。 | 復元済み | `C-GROUP-01`, `C-GROUP-02` |


### ■ (**) `EnemyModeID`
どのモード ID がフィールドまたは敵に適用されるどの効果を指すかをゲームに伝えるテーブル。これらの効果はアベイルへ割り当てられる。ボスがブーストモードに入り、特殊効果を表示する際に使用される。

#### ■ 実行時の読み取り意味論
`EnemyModeID` はランタイムでは列挙型に束縛されていない。エンジンは整数のモード ID を `EnemyModeID` のデータベースアクセス補助処理で解決し、その後 88 バイトの行を読み取る。実際のモード変更システムでは、アクティブなモードを `MODE_CHANGE` 状態異常として保持し、その `FAilmentInfo.m_LinkedEnemyModeID` を要求された `EnemyModeID` と比較する。したがって拡張時の制約は、固定的な SDK 列挙型ではなく、データベースのキー／インデックスの有無、およびモード ID を渡す呼び出し側にある。

【一次 C 証拠】

##### ◇ C-MODE-01: `EnemyModeID` 行の検索は DB キー解決と 88 バイトの行ストライドを使用する

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

##### ◇ C-MODE-02: テーブル全体のインターフェースは `ADatabaseDefineStatic` 経由で公開される

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

##### ◇ C-MODE-03: コピーされる行レイアウトとスカラー／配列レーン

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

##### ◇ C-MODE-04: モード変更解除条件は行 `+0x18/+0x28` から読み取られる

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

##### ◇ C-MODE-05: モード変更解除条件は、状態異常の標準解除条件レーンを上書きする

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

##### ◇ C-MODE-06: 行 `+0x38` はモード中に適用される付随エフェクト ID である

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

##### ◇ C-MODE-07: 行 `+0x14` は突入時に 3 種の行動阻害系状態異常を即時解除するかを制御する

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

##### ◇ C-MODE-08: アクティブなモードの同一性は `FAilmentInfo.m_LinkedEnemyModeID` として保持される

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

【フィールド単位の解釈】

| ランタイムレーン／キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_id` | `C-MODE-01` `GetArrayIndex(..., ModeID)` および `Data[88 * ArrayIndex]`; `C-MODE-03` 行 `+0x08` のコピー | `ModeID` はデータベースキーである。ランタイムは SDK の列挙型を閉じた領域として使用せず、与えられた整数を DB アクセス補助処理で解決し、`EnemyModeID` のデータリストへインデックスする。 | 復元済み | `C-MODE-01`, `C-MODE-03` |
| 行 `+0x14` のモード突入時状態異常解除フラグ | `C-MODE-07` `if ( EnemyModeDB && *(_BYTE *)&EnemyModeDB[20] )` | このバイトが非ゼロの場合、モード突入時に敵から `PARALYSIS`、`FASCINATION`、`SLEEP` を解除する。逆コンパイル結果では、このバイトについて利用箇所側に信頼できるエクスポート上のフィールド名が保持されていないため、本ガイドではオフセットと効果により識別する。 | 復元済み（利用箇所では名前が保持されていない） | `C-MODE-07` |
| 解除条件配列（おそらくテーブルの条件レーン） | `C-MODE-04` `*pConditions = ... (v8 + 24)`; `C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | この配列は、復元された利用箇所ではモード変更状態異常を解除するための述語集合として使用される。共通条件評価器とスロット互換であり、条件 ID 配列とパラメータ配列の組として扱われる。 | 復元済み | `C-MODE-04`, `C-MODE-05` |
| 解除パラメータ配列（おそらくテーブルのパラメータレーン） | `C-MODE-04` `*pParams = ... (v8 + 40)`; `C-MODE-05` `CheckRemoveAilment(pConditions[0], pParams, ...)` | この配列は、解除述語に対するスロット単位のパラメータを供給する。条件配列と並行して編集すべきであり、長さが一致しない場合、評価器のスロットが存在しないデフォルト値を読んだり、判定に失敗したりする危険がある。 | 復元済み | `C-MODE-04`, `C-MODE-05` |
| 行 `+0x38` の付随エフェクト ID | `C-MODE-06` `SetAttachEffect(this, *(_DWORD *)&EnemyModeDB[56])` | モード突入時、エンジンは行 `+0x38` の 32 ビット ID を読み取り、それを `AQPBattleCharacterBase::SetAttachEffect` へ直接渡す。これはアクティブモードのオーラ／付随 VFX 用レーンであり、`EnemyTypeID` の通常の付随エフェクトレーンとは別である。 | 復元済み（利用箇所では正確なプロパティ名が保持されていない） | `C-MODE-06` |
| アクティブモード状態 | `C-MODE-08` `info.m_AilmentType == MODE_CHANGE`; `info.m_LinkedEnemyModeID == EnemyModeID` | アクティブなモードは、`EnemyModeID` 行をキャラクターへ直接書き込むのではなく、`MODE_CHANGE` 状態異常レコードとして表現される。行は突入／解除時の挙動を設定し、ライブな同一性は `FAilmentInfo` 内に格納された紐付けモード ID で表される。 | 復元済み | `C-MODE-08` |
| `SkillAvailID.m_ModeChangeID` との連結 | `C-MODE-05` `SkillAvailDB->m_ModeChangeID` | `MODE_CHANGE` 状態異常を付与するアベイルは、モード固有の解除条件を取得するためのモード ID を提供する。これが、アベイルから `EnemyModeID` へ到達することが確認できる経路である。 | 復元済み | `C-MODE-05` |

### ■ (**) `EnemyDropID`
敵に割り当て可能な各種ドロップ ID を管理するテーブル。
#### ■ 実行時の読み取り意味論
`EnemyDropID` は独立したドロップテーブル用データベースである。`EnemyID.m_DropReward` が保持するのはアイテム ID ではなく、ドロップテーブル ID である。リザルト計算時、エンジンはその ID を 96 バイトの `EnemyDropID` 行へ解決し、アイテム ID、確率、固定個数、可変個数、および任意のイベントドロップ用クエストフィルタの並列配列を読み取り、選択されたリザルトアイテムをバトルセーブデータのリザルト配列へ記録する。
【一次 C 証拠】

##### ◇ C-DROP-01: `EnemyID.m_DropReward` は `EnemyID` 行 `+0x9C` からドロップテーブル ID として読み取られる

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

##### ◇ C-DROP-02: ドロップテーブル検索は DB キー解決と 96 バイトの行ストライドを使用する

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

##### ◇ C-DROP-03: テーブル全体のインターフェースは `ADatabaseDefineStatic` 経由で公開される

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

##### ◇ C-DROP-04: コピーされる行レイアウトは ID／名前接頭部の後に 5 本の配列レーンを持つ

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

##### ◇ C-DROP-05: アイテム ID、確率、個数、イベントクエストフィルタはオフセット別の取得関数で公開される

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

##### ◇ C-DROP-06: 記載されたクエストが進行中の場合、イベントドロップが `EnemyID.m_DropReward` を上書きし得る

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

##### ◇ C-DROP-07: リザルト計算はアイテム ID、確率、個数範囲をスロット単位で利用する

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

##### ◇ C-DROP-08: リザルト UI は選択済みのリザルトアイテム／個数をバトルセーブデータ配列から読み取る

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

【フィールド単位の解釈】

| ランタイムレーン／キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `EnemyID.m_DropReward` | `C-DROP-01` `*(_DWORD *)&result[156]` | この `EnemyID` フィールドは通常ドロップ用のテーブル ID であり、ドロップするアイテム ID ではない。得られた ID は `GetEnemyDropDB` へ渡される。 | 復元済み | `C-DROP-01`, `C-DROP-02` |
| `EnemyID.m_EventDropRewards` | `C-DROP-06` `UEnemyDB::GetEnemyEventDropIDs(v2)` によるイベントドロップ ID の走査 | イベントドロップ ID は通常ドロップ計算より先に試行される。イベントドロップ行のクエスト ID のうち、現在進行中のものが 1 つでもあれば、エンジンは `DropType = 1` を設定し、そのドロップ ID を通常の `m_DropReward` の代わりに使用する。 | 復元済み | `C-DROP-06` |
| `EnemyDropID.m_id` | `C-DROP-02` `GetArrayIndex(..., DropID)` および `Data[96 * ArrayIndex]`; `C-DROP-04` 行 `+0x08` のコピー | `DropID` はこのテーブルの DB アクセス補助処理で解決される。したがって、テーブル拡張は列挙型領域の問題ではなく、データベースキー／インデックスの問題である。ただし、呼び出し側が十分な幅を持つ ID フィールドを使用していることが前提になる。 | 復元済み | `C-DROP-02`, `C-DROP-04` |
| 行 `+0x10` のアイテム ID 配列 | `C-DROP-05` `*IDs = ... &EnemyDropDB[16]`; `C-DROP-07` `v46 = *(_DWORD *)Data` | 候補アイテム ID の並列配列である。リザルト計算では、アクティブなスロット内の 0 以下のアイテム ID は無視される。 | 復元済み（取得関数では正確なエクスポート上のプロパティ名が保持されていない） | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x20` の確率／重み配列 | `C-DROP-05` `return EnemyDropDB + 32`; `C-DROP-07` による `DropItemRates` の走査 | 候補重みの並列配列である。エンジンは確率値を合計し、正のアイテム ID を持つスロットに対して正規化したうえで、乱数抽選によりスロットを選択する。 | 復元済み（取得関数では正確なエクスポート上のプロパティ名が保持されていない） | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x30` の固定個数配列 | `C-DROP-05` `GetDropItemCounts(DropID, 0)` が `result + 48` を返す; `C-DROP-07` `v54 = fixedCounts[slot]` | ドロップに加算される基本個数である。 | 復元済み（取得関数では正確なエクスポート上のプロパティ名が保持されていない） | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x40` の可変個数配列 | `C-DROP-05` `GetDropItemCounts(DropID, 1)` が `result + 64` を返す; `C-DROP-07` `GetRandomRange(variableCounts[slot], 0)` | 加算型の乱数範囲である。最終的な選択個数は `fixedCount[slot] + random(0..variableCount[slot])` であり、その後、同じルーチン内で支援者／町関連のドロップ個数倍率が適用される。 | 復元済み（取得関数では正確なエクスポート上のプロパティ名が保持されていない） | `C-DROP-05`, `C-DROP-07` |
| 行 `+0x50` のイベントクエスト ID 配列 | `C-DROP-05` `GetEventDropQuestIDs` が `EnemyDropDB + 80` を返す; `C-DROP-06` `UQuestUtility::IsQuestInProgress(*v13)` | イベントドロップ行では、この配列がそのイベントドロップ行を使用するかどうかを制御する。復元された経路では、進行中の正のクエスト ID が 1 つでもあれば、イベントドロップ置換が有効になる。 | 復元済み（取得関数では正確なエクスポート上のプロパティ名が保持されていない） | `C-DROP-05`, `C-DROP-06` |
| リザルトアイテム／個数出力 | `C-DROP-08` が `ResultItemID[]` と `ResultItemCount[]` を読み出す | ドロップ計算後、UI 側の取得処理はもはや `EnemyDropID` を読まず、バトルセーブデータ内に実体化されたリザルト配列を読み取る。したがって、`EnemyDropID` の変更が影響するのは選択・個数生成であり、事後的な UI 参照ではない。 | 復元済み | `C-DROP-08` |

### ■ (**) `EncountVolume`
危険度、遭遇し得る敵グループ（`m_EnemyGroup` および `m_FirstEncount`。後者は初回エンカウント用の敵グループを指定している可能性が高い）、ならびに各エンカウント候補の出現確率を定義する。`EncountVolume` は `EncountList` 内でマップに対応付けられる。マップ側の状態変化により危険度が変わり、その結果として参照されるエンカウントボリュームも変化する場合がある。`m_EncountRatio` の値の合計が 100 にならない場合、出現率の解釈や抽選処理に問題が生じる可能性がある。
#### ■ 実行時の読み取り意味論
【一次 C 証拠】

##### ◇ C-ENCOUNT-01: `EncountVolume` 行の解決処理

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

##### ◇ C-ENCOUNT-02: 現在エンカウントボリュームへの `EncountVolume` の実体化

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

##### ◇ C-ENCOUNT-03: `EncountVolume` の選択セマンティクス

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_EncountRatio` | `C-ENCOUNT-02:17643576` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EncountRatio,`<br>`C-ENCOUNT-02:17643579` `if ( outEncountVolume.m_EncountRatio.AllocatorInstance.Data )` | `FEncountVolumeBase` の一時オブジェクトへ、行所有の `TArray` としてコピーされる。`m_EnemyGroup` と同じ候補列に対応する比率／重みの列であり、抽選側の処理では敵グループ候補と組で扱われる。 | 復元済み | `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_EnemyGroup` | `C-ENCOUNT-02:17643567` `memset(&outEncountVolume.m_EnemyGroup, 0, 32);`<br>`C-ENCOUNT-02:17643573` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&outEncountVolume.m_EnemyGroup,` | `EnemyGroups.m_id` の配列として実体化される。遭遇候補の列であり、後続処理では各要素を `EnemyGroups` 行へ解決する。 | 復元済み | `C-ENCOUNT-02`, `C-ENCOUNT-03` |
| `m_FirstEncount` | `C-ENCOUNT-03:17641121` `int m_FirstEncount; // edi`<br>`C-ENCOUNT-03:17641196` `m_FirstEncount = EncountVolume->m_FirstEncount;`<br>`C-ENCOUNT-03:17641199` `if ( m_FirstEncount > 0 && UEnemyDB::GetEnemyGroupDB(m_FirstEncount) )` | 現在ボリュームに初回エンカウント用の敵グループが設定されている場合、その敵グループを優先的に検証する。通常候補列とは別の単一グループ参照として扱われる。 | 復元済み | `C-ENCOUNT-03` |

#### 追加ランタイム証拠: エンカウントボリュームは危険度と敵タイプ事前計算の双方に供給される

エンカウント系サブシステムは、`EncountList.m_EncountVolume` を `EncountVolume` 行へ解決し、その行から危険度および敵グループ／比率配列を読む。別の補助関数 `GetMaxEncountEnemyTypes` は、エンカウントボリューム、敵グループ、敵 ID、および増援を静的にたどり、エンカウント範囲で使用され得る敵タイプを事前計算する。これは、`EncountVolume` が単なる確率表ではなく、危険度表示や事前ロード／解析対象の決定にも関与することを示している。

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

| キー | ランタイム上の解釈 |
|---|---|
| `m_DangerLevel` | `EncountList.m_EncountVolume` が解決された後、`EncountVolume +0x0C` から読み出される。危険度は `EncountList` 自体ではなく、参照先ボリューム側の性質である。 |
| `m_EnemyGroup` | 行所有の配列 `+0x18/+0x20` として保持される。敵タイプの事前計算では、この配列が一時的なグループ ID リストへコピーされ、各 `EnemyGroups` 行が解決される。 |
| `m_FirstEncount` | ここで示した `GetDangerLevel` および `GetMaxEncountEnemyTypes` の経路では使用されない。この不在はグローバルな未使用を証明するものではなく、これら 2 つの一般的な補助経路が同フィールドを無視することだけを示す。 |
| `m_EncountRatio` | 行所有の配列 `+0x28/+0x30` として保持される。危険度関連処理用の `FEncountVolumeBase` 一時オブジェクトを構築する際、`m_EnemyGroup` とともにコピーされる。 |

### ■ (**) `EncountList`
『オクトパストラベラー0』のランダムエンカウント規則テーブル。どのマップでどのエンカウントボリュームを使用するか（`m_EncountVolume` は `EncountVolume.m_id`）、エンカウントまでの最小歩数（`m_EncountStepMin`）、およびエンカウント成立に必要な条件を指定する。

ランタイム上重要な補助キー:
- `m_DisableCondition`: このエンカウントを無効化するために満たすべき条件。
- `m_RandomStepA` および `m_RandomStepB`: ランダム歩数の範囲または曲線に関するパラメータ。両キーは『オクトパストラベラー0』および『オクトパストラベラー 大陸の覇者』の双方で、ほぼ常に同じ値に設定されている。
- `m_FirstEncountStepRatio`: マップまたはゾーンへ入った直後の初回エンカウント専用の倍率と見られる。通常より早く、または遅くエンカウントさせるための値である可能性が高い。

#### ■ 実行時の読み取り意味論
【一次 C 証拠】

##### ◇ C-ENCOUNT-01: `EncountVolume` 行の解決処理

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

##### ◇ C-ENCOUNTLIST-01: `EncountList` のコピーコンストラクタ

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_DisableCondition` | `C-ENCOUNTLIST-01:17639800` `this->m_DisableCondition = __that->m_DisableCondition;` | `FEncountListBase` のコピーコンストラクタは、`m_DisableCondition` をスカラー値としてコピーする。このフィールドは現在エンカウントリスト選択処理の一部であり、条件が真になった行を候補から除外するために使われる。 | 復元済み | `C-ENCOUNTLIST-01` |
| `m_EncountStepMin` | `C-ENCOUNTLIST-01:17639801` `this->m_EncountStepMin = __that->m_EncountStepMin;` | `FEncountListBase` のコピーコンストラクタは、`m_EncountStepMin` をスカラー値としてコピーする。選択された行のエンカウント歩数制御に残る値である。 | 復元済み | `C-ENCOUNTLIST-01` |
| `m_EncountVolume` | `C-ENCOUNTLIST-01:17639805` `this->m_EncountVolume = __that->m_EncountVolume;` | `FEncountListBase` のコピーコンストラクタは `m_EncountVolume` をコピーする。後続のエンカウント処理はこの ID から `EncountVolume` 行を解決し、危険度や敵グループ候補を取得する。 | 復元済み | `C-ENCOUNTLIST-01`, `C-ENCOUNT-01` |
| `m_FirstEncountStepRatio` | `C-ENCOUNTLIST-01:17639804` `this->m_FirstEncountStepRatio = __that->m_FirstEncountStepRatio;` | `FEncountListBase` のコピーコンストラクタは、`m_FirstEncountStepRatio` をスカラー値としてコピーする。引用箇所は値の保持を証明するが、具体的な歩数計算式までは示していない。 | 復元済み | `C-ENCOUNTLIST-01` |
| `m_RandomStepA` | `C-ENCOUNTLIST-01:17639802` `this->m_RandomStepA = __that->m_RandomStepA;` | `FEncountListBase` のコピーコンストラクタは、`m_RandomStepA` をスカラー値としてコピーする。 | 復元済み | `C-ENCOUNTLIST-01` |
| `m_RandomStepB` | `C-ENCOUNTLIST-01:17639803` `this->m_RandomStepB = __that->m_RandomStepB;` | `FEncountListBase` のコピーコンストラクタは、`m_RandomStepB` をスカラー値としてコピーする。 | 復元済み | `C-ENCOUNTLIST-01` |

#### 追加ランタイム証拠: 現在エンカウントリストの選択はマップ固有であり、条件により制限される

`AQPEncountManager::GetCurrentEncountList` は、`m_MapId`、`m_Condition`、`m_DisableCondition` の実際の意味を示す。行は現在マップ ID によって絞り込まれ、次に無効化条件が真であれば除外され、最後に通常条件がすべて成立する場合のみ候補として採用される。`m_Condition` のゼロ要素は除去され、残った条件は論理積として評価される。

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

| キー | ランタイム上の解釈 |
|---|---|
| `m_MapId` | `GetCurrentEncountList` における主フィルタ。`m_MapId == 現在マップ ID` の行だけが候補として残る。 |
| `m_DisableCondition` | 値が存在し、かつそのクエスト条件が真に評価される場合、その行は通常条件を評価する前に除外される。 |
| `m_Condition` | 一時配列へコピーされ、ゼロを取り除いた後、残るすべてのクエスト条件が成立する必要がある。ゼロ除去後は確率的な判定ではなく、論理積条件である。 |
| `m_EncountStepMin`, `m_RandomStepA`, `m_RandomStepB`, `m_FirstEncountStepRatio`, `m_EncountVolume`, `m_SoundList` | 選択済みの `FEncountListBase` 一時オブジェクトへ、行オフセット `+0x24`, `+0x28`, `+0x2C`, `+0x30`, `+0x34`, `+0x38` からそのままコピーされる。 |

### ■ (*) `EncountEffectTypeID`
特殊なエンカウント演出を管理するテーブル。`m_SeId` は再生する効果音を指定している可能性が高い。
#### ■ 実行時の読み取り意味論
【一次 C 証拠】

##### ◇ C-STATIC-01: 代表的な静的データベースアクセサの公開面

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_SeId` | 提供された C エクスポート内では、正確な名前付きフィールドまたは一意に特定できるオフセット列を復元できなかった。 | C エクスポートからは、`m_SeId` という正確なシンボルやキー単位の利用箇所は復元されていない。確認できるのは、テーブル公開面および静的データベースアクセサの存在にとどまる。 | 未復元 | `C-STATIC-01` |

### ■ (*) `EnemyGroupsByCondition`
特定条件が成立したときに出現させる敵グループの集合を定義する。『全てを授けし者』第 7 章で、指輪に選ばれし主人公とともに黒呪炎を出現させる用途に使われている。

#### ■ 実行時の読み取り意味論
`EnemyGroupsByCondition` は、条件 ID から敵グループ ID を選ぶセレクタである。C エクスポートでは、行から 2 本の対応配列をコピーし、条件を先頭から評価し、真になった条件と同じ配列インデックスの敵グループ ID を返す処理が確認できる。

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

| キー | ランタイム上の解釈 |
|---|---|
| `m_Conditions` | 行配列 `+0x18/+0x20` として保持される。各値はクエスト条件 ID として扱われ、`UQuestUtility::GetQuestConditionPtr` および `CheckQuestTaskCondition` に渡される。 |
| `m_EnemyGroups` | 行配列 `+0x28/+0x30` として保持される。`m_Conditions` と位置対応しており、最初に真になった条件に対して `m_EnemyGroups[i]` が返される。 |
| `m_id` | 示した関数では、指定された整数を `m_DataList` の配列インデックスとして使用しており、`GetArrayIndex` によるキー検索は行っていない。拡張時には、非連番 ID が期待通りに動くか慎重に検証すべき理由になる。この関数は、一般的な DB キー解決よりも配列インデックス的に振る舞う。 |

### ■ (**) `SymbolEnemyList`
フィールド上のシンボル敵／強敵について、使用するフィールド上スプライトまたはオブジェクト、対応する敵グループ、レベルなどを指定する。
- `m_GroupId`: このシンボル敵エントリに対応する敵グループ。
- `m_Index`: 対象の敵グループ内で、このシンボル敵が対応する敵のインデックス。複数のシンボル敵が同じ `GroupId` または同じスポーン制御を共有する場合によく使われる。
- `m_Dir`: シンボル敵が初期状態で向いている方向。
- `m_ObjectId`: シンボル敵のフィールド上スプライトを特定のオブジェクトで上書きする（例: 『権力を授けし者』第 3 章におけるタトゥロックの船）。
- `m_RepopCount`: 再出現／リポップ回数、すなわちシンボル敵が何回再出現するか。『オクトパストラベラー0』の敵では既定値は 0 だが、『オクトパストラベラー 大陸の覇者』の敵では必ずしもそうではない（マップを再読み込みしても敵が即座に再出現しない）。
- `m_Redrawing`: 『オクトパストラベラー0』および『オクトパストラベラー 大陸の覇者』の双方で常に 0 に設定されている。
- `m_TargetLevel1` および `m_TargetLevel2`: シンボル敵のレベル範囲である可能性がある。
- `m_Scale`: フィールド上の敵シンボルに適用されるスケール倍率（見た目および当たり判定サイズに関係する）。

#### ■ 実行時の読み取り意味論
【一次 C 証拠】
##### ◇ C-SYMBOL-01: `SymbolEnemyList` 行の実体化処理

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

##### ◇ C-SYMBOL-03: シンボル敵のランタイム割当と `GroupId` / `Index` から `EnemyGroups` への橋渡し

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_Dir` | `C-SYMBOL-01:17851302` `outData->m_Dir = *(_DWORD *)(v7 + 20);` | 行オフセット `+0x14` から 32 ビットスカラーとして読み出される。`FSymbolEnemyList::GetData` が行を実体化し、`ASymbolEnemy::SetSymbolData` がシンボル敵アクタへコピーする。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_GroupId` | `C-SYMBOL-01:17851300` `outData->m_GroupId = *(_DWORD *)(v7 + 12);` | シンボルオブジェクトへコピーされた後、`EnemyGroups` ID として使われる。続いて `m_Index` が `EnemyGroups.m_EnemyID` のどのスロットを選ぶかを決定する。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Index` | `C-SYMBOL-03:17650576` `int m_Index; // eax`<br>`C-SYMBOL-03:17650593` `this->m_data.m_Index = data->m_Index;`<br>`C-SYMBOL-03:17650657` `m_Index = this->m_data.m_Index;` ほか 3 箇所の局所的な出現 | `m_GroupId` で解決された `EnemyGroups` 行に対し、`m_EnemyID[m_Index]` を選択するためのグループ内スロット番号として使用される。これは単なる表示用番号ではなく、戦闘に入る敵 ID の解決に直接関与する。 | 復元済み | `C-SYMBOL-03` |
| `m_ObjectId` | `C-SYMBOL-01:17851303` `outData->m_ObjectId = *(_DWORD *)(v7 + 24);` | 行オフセット `+0x18` から 32 ビットスカラーとして読み出される。`FSymbolEnemyList::GetData` が実体化し、`ASymbolEnemy::SetSymbolData` がアクタ側の `m_data` へコピーする。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Redrawing` | `C-SYMBOL-01:17851305` `outData->m_Redrawing = *(_DWORD *)(v7 + 32);` | 行オフセット `+0x20` から 32 ビットスカラーとして読み出され、シンボル敵データへ保持される。引用箇所は値の実体化を証明するが、再描画ロジックの具体的な利用までは示していない。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_RepopCount` | `C-SYMBOL-01:17851304` `outData->m_RepopCount = *(_DWORD *)(v7 + 28);` | 行オフセット `+0x1C` から 32 ビットスカラーとして読み出され、シンボル敵データへ保持される。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_Scale` | `C-SYMBOL-01:17851308` `outData->m_Scale = *(float *)(v7 + 44);` | 行オフセット `+0x2C` から 32 ビット浮動小数点値として読み出される。`ASymbolEnemy::SetSymbolData` はこの値をアクタ状態へコピーする。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel1` | `C-SYMBOL-01:17851306` `outData->m_TargetLevel1 = *(_DWORD *)(v7 + 36);` | 行オフセット `+0x24` から 32 ビットスカラーとして読み出され、シンボル敵の目標レベル情報として保持される。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |
| `m_TargetLevel2` | `C-SYMBOL-01:17851307` `outData->m_TargetLevel2 = *(_DWORD *)(v7 + 40);` | 行オフセット `+0x28` から 32 ビットスカラーとして読み出され、シンボル敵の目標レベル情報として保持される。 | 復元済み | `C-SYMBOL-01`, `C-SYMBOL-03` |

#### 追加ランタイム証拠: `SymbolEnemyList.m_Index` はグループ内スロット選択子であり、任意のタイプ差し替えも存在する

シンボル敵の初期化処理は、シンボルグループ行とシンボル行をアクタ状態へコピーした後、`SymbolEnemyList.m_GroupId` を `EnemyGroups` 行へ解決する。`SymbolEnemyList.m_Index` は `EnemyGroups.m_EnemyID` の 1 要素を選択し、その敵 ID から `EnemyID.m_TypeID` が読み出される。ただし、呼び出し側が `nReplaceTypeID` を明示的に渡した場合、その差し替えタイプ ID が優先される。

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

| キー | ランタイム上の解釈 |
|---|---|
| `SymbolEnemyGroupList.m_StartSymbolEnemyId` | シンボルアクタの `m_group` 状態へ直接コピーされる。示した初期化処理では評価されず、グループメタデータとして保持される。 |
| `SymbolEnemyGroupList.m_SymbolEnemyId` | 配列としてアクタ状態へコピーされる。示した初期化処理では確率抽選は行われず、候補シンボル配列として保持される。 |
| `SymbolEnemyGroupList.m_probability` | `m_SymbolEnemyId` とともに配列としてアクタ状態へコピーされる。したがって、確率の具体的な意味論はこの初期化処理の外側にある。 |
| `SymbolEnemyList.m_GroupId` | `EnemyGroups.m_DBAccess` へのキーとして使用される。解決された `EnemyGroups` 行は、実際の戦闘で使う敵スロットを提供する。 |
| `SymbolEnemyList.m_Index` | `EnemyGroups.m_EnemyID` への配列インデックスとして使用される。これは、同フィールドがグループ内スロット選択子であることを示す最も強いランタイム証拠である。 |
| `m_Dir`, `m_ObjectId`, `m_RepopCount`, `m_Redrawing`, `m_TargetLevel1`, `m_TargetLevel2`, `m_Scale` | シンボルアクタの `m_data` 構造体へ直接コピーされる。これによりランタイム実体化は証明されるが、引用箇所は各値の後続のゲームプレイ上の消費までは示していない。 |
| `nReplaceTypeID` / `EnemyID.m_TypeID` | 明示的な差し替えタイプ ID が指定されている場合はそれを使用する。指定されていない場合、アクタは `(symbol id, index)` から敵 ID へ写像し、その敵の `m_TypeID` を読み出してタイプ行を解決する。 |

### ■ (**) `SymbolEnemyGroupList`
シンボル敵 ID（`SymbolEnemyList` テーブルの `m_id`）を、特定マップ上の特定シンボル敵を指す内部 ID 群へ対応付けているように見える。`m_SymbolEnemyId` 配列は、`m_StartSymbolEnemyId` のシンボル敵を倒した後、リポップ時にその地点へ出現し得るシンボル敵候補を決める可能性がある。各候補の確率は `m_probability` で指定されると思われる。ただし、『オクトパストラベラー 大陸の覇者』および『オクトパストラベラー0』のどちらでも、これらの配列は実質的には使用されていないように見える。
#### ■ 実行時の読み取り意味論
【一次 C 証拠】
##### ◇ C-SYMBOL-02: `SymbolEnemyGroupList` の実体化／代入演算子

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

##### ◇ C-SYMBOL-03: シンボル敵のランタイム割当と `GroupId` / `Index` から `EnemyGroups` への橋渡し

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

【フィールド単位の解釈】

| キー | ランタイム C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_StartSymbolEnemyId` | `C-SYMBOL-02:17844263` `this->m_StartSymbolEnemyId = __that->m_StartSymbolEnemyId;` | 代入演算子により、`m_StartSymbolEnemyId` は生きたシンボルグループオブジェクトへ直接コピーされる。 | 復元済み | `C-SYMBOL-02` |
| `m_SymbolEnemyId` | `C-SYMBOL-02:17844265` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_SymbolEnemyId,`<br>`C-SYMBOL-02:17844266` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_SymbolEnemyId);` | 代入演算子は `m_SymbolEnemyId` を `TArray<int>` として生きたシンボルグループオブジェクトへコピーする。 | 復元済み | `C-SYMBOL-02`, `C-SYMBOL-03` |
| `m_id` | `C-SYMBOL-02:17844262` `this->m_id = __that->m_id;` | 行オフセット `+0x08` から 32 ビットスカラーとして読み出される。代入演算子は、実体化時にグループリスト ID を保持する。 | 復元済み | `C-SYMBOL-02` |
| `m_probability` | `C-SYMBOL-02:17844268` `(TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&this->m_probability,`<br>`C-SYMBOL-02:17844269` `(const TArray<FPackedNormal,TSizedDefaultAllocator<32> > *)&__that->m_probability);` | 代入演算子は `m_probability` を `TArray<int>` として生きたシンボルグループオブジェクトへコピーする。引用したカスタム敵経路では、確率選択の利用箇所は復元されていない。 | 復元済み | `C-SYMBOL-02`, `C-SYMBOL-03` |


## ■ `Battle/` 配下

## ■ `Battle/` 配下
### ■ (***) `BattleCameraParams`
戦闘カメラのプリセット／設定に対応する ID 群。値 `-1.0` は、ゲーム側では「デフォルト値を使用する」という意味に解釈されるものと見られる。
- `m_PosotionX/Y/Z`: 戦闘カメラの三次元空間上の位置。
- `m_RotationX/Y`: カメラの X/Y 方向の回転・傾き。
- `m_FOV`: 視野角。視野角が狭いほど、ズームインした望遠寄りの見え方になる。`m_FOV` を変更しても、パーティ側の位置は固定である点に注意。
- `m_SequencerPath`: シネマティック／シーケンサー用のバインディング。戦闘中のカメラカットやターゲット演出で使用する `Level Sequence`、またはシーケンサー制御のカメラリグ資産を指すものと考えられる。
- `m_FocalDistance`, `m_FocusDistance`: フォーカス面までの距離。一方が旧式名、もう一方が新式名である可能性、または別系統の機構である可能性がある（『オクトパストラベラー 大陸の覇者』には `m_FocalDistance` のみが存在する）。本作ではどちらも常に `-1.0` に設定されている一方、『オクトパストラベラー 大陸の覇者』では異なる焦点距離が使われている。
- `m_FocalRegion`: ピントが合う領域の大きさ。
- `m_NearBlurSize` と `m_FarBlurSize`: 距離に応じた近景／遠景のぼかし強度。
- `m_MaxBokehSize`: ボケの最大サイズ。
- `m_Scale`: カメラ距離またはショットスケールの上書き値と考えられる。

#### ■ 実行時の読み取り意味論
##### ◇ C-CAMERA-01: `BattleCameraParams` 行の解決

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

##### ◇ C-CAMERA-02: `BattleCameraParams` 構造体コピー

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

【フィールド単位の解釈】

| キー | ランタイム上の C における位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_FOV` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FarBlurSize` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalDistance` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocalRegion` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_FocusDistance` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_MaxBokehSize` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_NearBlurSize` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_Scale` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |
| `m_SequencerPath` | `FBattleCameraParamsBase::Copy` により順序どおりコピーされる、パック済みカメラ用スロット | `AQPBattleManager::GetCameraParam` は、ストライド `0x60` の `BattleCameraParams` 行を解決する。その後、復元された構造体コピー処理が、行を文字列レーンとスカラー値レーンの集合としてコピーする。逆コンパイル結果にはすべてのスカラー座標の信頼できるフィールド名が残っていないため、コピー列以降の個別オフセット解釈は慎重に扱うべきである。 | 復元済み | `C-CAMERA-01`, `C-CAMERA-02` |

#### `BattleCameraParams` の行セマンティクス、カメラセット経由参照、被写界深度フォールバック

カメラパラメータ表は二つの経路で消費される。第一に、現在有効な `BattleCameraSet` が、実行中のカメラ種別を具体的な `BattleCameraParams.m_id` へ写像する。第二に、解決された行は固定長 96 バイトのレコードとして解釈される。`m_id` は `+0x08`、`m_SequencerPath` は `+0x10` から始まる `FString`、トランスフォームおよび視野角の浮動小数点値は `+0x20..+0x34`、被写界深度の浮動小数点値は `+0x38..+0x58` に配置される。したがって、カメラパラメータ行は単なるエディタ用プリセットではなく、戦闘カメラ状態へ直接コピーされるランタイム上のペイロードである。

##### ◇ C-CAMERAPARAMS-LOOKUP-01: ID 解決、ロード済み表の要件、96 バイト行ストライド

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

解釈： `BattleCameraParams` はデータベースアクセスヘルパーによってキー引きされ、ライブ行のストライドは `0x60` である。`m_BattleCameraSet.m_Loaded` への一見意外な依存は意味を持つ。ほとんどの呼び出し元は現在のカメラセットを経由してカメラパラメータに到達するため、セット表もロード済みでなければパラメータ行は使用可能と見なされない。

##### ◇ C-CAMERAPARAMS-SET-INDIRECTION-02: カメラ種別からカメラパラメータ ID への間接参照

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

解釈： `BattleCameraParams` の各エントリは、通常、ハードコードされたカメラパラメータ ID ではなく `BattleCameraSet` を通じて選択される。カメラ種別 `1` はニュートラルカメラ、`3` は開始時カメラ、`4` はブレイク時カメラ、`5` は勝利時カメラ、というように対応する。したがって、あるカメラパラメータ行がデータとして妥当であっても、どのカメラセットからも参照されていなければ使用されない。

##### ◇ C-CAMERAPARAMS-TRANSFORM-CACHE-03: トランスフォームおよび視野角の浮動小数点値が戦闘マネージャのカメラキャッシュへコピーされる

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

解釈： トランスフォームブロックは正確に `+0x20..+0x34` であり、位置ベクトル、二つの回転値、視野角から成る。エクスポート上の綴り誤りを含む `m_PosotionX/Y/Z` は、未使用のエディタメタデータや象徴的ラベルではなく、文字どおりカメラ位置の三成分として読むべきである。

##### ◇ C-CAMERAPARAMS-SEQUENCER-PATH-04: シーケンサーバインディングは、空白を除去した非空パスとして判定される

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

解釈： この述語は、`m_SequencerPath` を単なる真偽値として扱わない。ランタイムは `+0x10/+0x18` の `FString` をコピーし、空白を除去したうえで、結果の文字列長が UTF-16 要素で 1 を超える場合に限り、そのカメラをシーケンサー駆動と見なす。したがって、空白だけのパスはこの判定ではシーケンサーパスなしと同義である。

##### ◇ C-CAMERAPARAMS-DOF-SENTINEL-05: 被写界深度フィールドは `INVALID_PARAM` センチネルの規則に従う

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

解釈： `-1.0` 風の値は、単なる慣用的なデフォルト指定ではない。`ABattleCameraBase::INVALID_PARAM` と比較され、それ以下のフィールドはスキップされ、現在のシネカメラ設定を上書きしない。この規則は、焦点距離、焦点領域、近景／遠景の遷移領域、近景／遠景のぼかし、スケール、最大ボケサイズ、フォーカス距離のそれぞれに独立して適用される。

##### ◇ C-CAMERAPARAMS-NEUTRAL-DOF-FALLBACK-06: 被写界深度補間はニュートラルカメラ行をフォールバック元として使用する

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

解釈： 被写界深度の補間は、浮動小数点インデックス `14..22`、すなわち行オフセット `+0x38..+0x58` を使用する。どちらかの端点がセンチネル値を含む場合、補間の前にニュートラルカメラ行の値がフォールバック値として供給される。したがって、ニュートラルカメラの被写界深度設定は、単なる別プリセットではなく、他のカメラモードに対する既定値の供給元でもある。

これらの消費箇所から復元できるフィールド対応表は次のとおりである。

| エクスポート上のキー／レーン | ランタイム上の位置 | 解釈 |
|---|---:|---|
| `m_id` | `+0x08`; DB キー解決 | データベースアクセスヘルパーで選択される。通常はカメラセットによる間接参照の後に到達する。 |
| `m_SequencerPath` | `+0x10` ポインタ、`+0x18` 長さ | `FString` としてコピーされ、空白除去後に非空であればシーケンサーカメラ挙動を有効化する。 |
| `m_PosotionX/Y/Z` | `+0x20/+0x24/+0x28` | 戦闘マネージャのカメラキャッシュへコピーされるカメラのワールド座標ベクトル。 |
| `m_RotationX/Y` | `+0x2C/+0x30` | 位置ベクトルとともにコピーされる二つの回転成分。 |
| `m_FOV` | `+0x34` | ニュートラル、開始、ブレイク、勝利などのカメラ状態へ、トランスフォームフィールドとともにコピーされる視野角。 |
| `m_FocalDistance` | `+0x38` / 浮動小数点インデックス `14` | 被写界深度フィールド。`<= INVALID_PARAM` の場合はスキップされ、それ以外ではシネカメラのポストプロセスへ適用される。 |
| `m_FocalRegion` | `+0x3C` / 浮動小数点インデックス `15` | 被写界深度フィールド。同じセンチネル／フォールバック規則に従う。 |
| 近景／遠景の被写界深度遷移レーン | `+0x40/+0x44` / インデックス `16/17` | ガイド向けスキーマでは目立った名前が付いていない場合でも、ランタイム上には存在する。 |
| `m_NearBlurSize`, `m_FarBlurSize` | `+0x48/+0x4C` / インデックス `18/19` | センチネルより大きい場合、シネカメラのポストプロセスへ個別に適用される。 |
| `m_Scale` | `+0x50` / インデックス `20` | 戦闘レイアウトのスケールではなく、被写界深度のスケールへ適用される。 |
| `m_MaxBokehSize` | `+0x54` / インデックス `21` | センチネルより大きい場合、シネカメラの最大ボケサイズへ適用される。 |
| `m_FocusDistance` | `+0x58` / インデックス `22` | ポストプロセス値の書き込み後、フォーカス設定側の分岐で処理される。 |

### ■ (***) `BattleCameraSet`
敵グループに割り当てられるカメラ設定の集合（`BattleCameraSet.m_id` = `EnemyGroups.m_Camera`）。『オクトパストラベラー 大陸の覇者』には存在せず、同作では戦闘中に一種類のニュートラルカメラ設定だけが使われる。

`Kingship_structs.hpp` は、本作の一部カメラセットに次のラベルを割り当てている。

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

#### `BattleCameraSet` はカメラ種別ディスパッチ表である

`BattleCameraSet` は、カメラパラメータ ID をまとめた小型の表である。`m_id` を探すために表が走査され、その後 `ABattleCameraBase::GetCameraParamID` がランタイム上のカメラ種別を該当フィールドへディスパッチする。したがって、`EnemyGroups.m_CameraSet` は単一のカメラ行ではなく、カメラパラメータ行の「ファミリ」を選択する。

##### ◇ C-CAMERASET-LOOKUP-01: カメラセット行は `m_id` で走査され、56 バイトのストライドを持つ

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

解釈： `BattleCameraParams` と異なり、この解決関数は `m_DataList` 上を線形走査する。ライブ行のストライドは `0x38` であり、`targetCameraSetID <= 0` はセットなしとして扱われる。

##### ◇ C-CAMERASET-TYPE-DISPATCH-02: カメラ種別ごとのフィールド単位ディスパッチ

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

解釈： `BattleCameraSet` の拡張や編集は、このディスパッチ表を編集するものとして理解すべきである。通常戦闘で到達するランタイムカメラ種別が存在しなければ、そのフィールドは実際には使われない場合がある。ただし、それでもセットレコード内のライブフィールドであることに変わりはない。

### ■ (***) `BattlePositions`
たとえば `EnemyFormations` や `EnemyReinforcements` で使用される位置 ID を定義する。`m_Priority` は、座標が競合した場合にどのスプライトを手前に表示するか、あるいは敵が選択される順序に関係している可能性がある。

#### ■ 実行時の読み取り意味論
##### ◇ C-FORM-02: フォーメーション位置から `BattlePositions` への解決

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

##### ◇ C-BPOS-01: `BattlePositions` 行の解決

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

【フィールド単位の解釈】

| キー | ランタイム上の C における位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_Priority` | `+0x20`（スカラー値）; 引用範囲内では、これより短い名前付き代入は残っていない | フォーメーションスロットが `BattlePositions` ID を返した後、`GetBattlePositionDB` は `0x28` バイトの行を解決する。`GetEnemyPlacement` は、`+0x10` の座標配列から XYZ をコピーした後、`BattlePositions +0x20` から優先度を読み取る。 | 復元済み | `C-BPOS-01`, `C-FORM-02` |

#### `BattlePositions` は共有座標表である

`BattlePositions` は敵フォーメーション専用ではない。ランタイムは、レイド表示、プレイヤー側の配置、増援配置、レイアウトセットのアンカーにもこの表を使用する。行はキー引きされ、40 バイトのストライドを持つ。

##### ◇ C-BATTLEPOSITIONS-COORDINATES-01: 位置行の解決と座標抽出

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

解釈： 公開座標ヘルパーは、行内の位置ベクトルレーンを逆参照し、`X/Y/Z` ベクトルへ書き込む。C エクスポートにより、この表が汎用座標サービスとして機能することが確認できる。フォーメーションの構成要素としての使用は、その消費箇所の一つにすぎない。

### ■ (***) `BattleEventList`
`EnemyGroups.m_EventIndices` から参照され得る戦闘イベントを定義するテーブルと考えられる。
- `m_IsMustPlay`: そのイベントがスキップ可能かどうかを制御するフラグと見られる。
- `m_EventCommand`: 条件成立時に実行するコマンドスクリプトの ID。`BattleEventCommand` を指す。
- `m_EventConditions`: 条件種別 ID。`TacticalList`、`SkillConditionList`、`TacticalActionList` などの `m_Conditions` 配列と同じ条件名前空間を用いる可能性が高い。
- `m_EventParams`: 各条件に渡されるパラメータ。
- `m_EventStatusTypes`: ステータス種別のフィルタ用レーン。型名が復元された C エクスポートでは、このレーンが `m_EventConditions`、`m_EventParams`、`m_EventEnemies` とともに `UBattleUtility::CheckCondition` へ渡されている（`Octopath_Traveler0-Win64-Shipping.exe.c`:17485857-17485873）。
- `m_EventEnemies`: 条件の対象となる敵スロット、敵インデックス、または部位を指定するための任意フィルタ。本作および『オクトパストラベラー 大陸の覇者』のいずれでも、実質的には未使用に見える。

関連する可能性があるものとして、`Kingship_structs.hpp` には次の定義がある:

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

#### ■ 実行時の読み取り意味論

【一次 C 証拠】

##### ◇ C-BEVENT-01: `BattleEventList` 行の解決

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

##### ◇ C-BEVENT-02: `BattleEventList` の条件評価

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

##### ◇ C-BEVENT-03: 共通の戦闘条件タプル評価器

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

##### ◇ C-BCMD-01: `BattleEventCommand` 行の解決

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

【フィールド単位の解釈】

| キー | ランタイム上の C 位置 | 解釈 | 状態 | 根拠 |
|---|---|---|---|---|
| `m_Conditions` | `FSkillConditionListBase` のデストラクタ内 16978495 行: `v8 = this->m_Conditions.AllocatorInstance.Data;`<br>`FSkillConditionListBase::FSkillConditionListBase` 内 17044430 行: `this->m_Conditions.AllocatorInstance.Data = nullptr;` | この節における `m_Conditions` は、`m_EventConditions` と同じ条件 ID 名前空間を指す。`BattleEventList` 上の実フィールド名は `m_EventConditions` である。 | 復元済み | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventCommand` | `AQPBattleManager::ExecuteBattleEventCommand` 内 17481721 行: `EventCommandDB = AQPBattleManager::GetEventCommandDB(this, EventListDB->m_EventCommand, m_NowEventCommandIndex);`<br>`AQPBattleManager::InitBattleEvent` 内 17483882 行: `int m_EventCommand; // eax` | イベント行が選択された後、`ExecuteBattleEventCommand` は `EventListDB->m_EventCommand` と現在のコマンドインデックスを `GetEventCommandDB` へ渡し、連続するコマンド行を解決する。 | 復元済み | `C-BEVENT-01`, `C-BCMD-01` |
| `m_EventConditions` | `C-BEVENT-02:17478613` `&EventListDB->m_EventConditions,` | 戦闘イベント評価器は、`m_EventConditions` を主要な条件 ID レーンとして `UBattleUtility::CheckConditions` へ渡す。 | 復元済み | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventEnemies` | `C-BEVENT-02:17478619` `&EventListDB->m_EventEnemies,` | イベント評価器は、`m_EventEnemies` を敵 ID／フィルタ用レーンとして共通条件評価器へ渡す。その評価器では、対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventParams` | `C-BEVENT-02:17478614` `&EventListDB->m_EventParams,` | イベント評価器は、`m_EventParams` を `m_EventConditions` とスロット単位で対応するパラメータレーンとして渡す。 | 復元済み | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_EventStatusTypes` | `C-BEVENT-02:17478615` `&EventListDB->m_EventStatusTypes,` | イベント評価器は、`m_EventStatusTypes` を `m_EventConditions` とスロット単位で対応するステータスフィルタレーンとして渡す。 | 復元済み | `C-BEVENT-02`, `C-BEVENT-03` |
| `m_IsMustPlay` | `AQPBattleManager::IsMustPlayEvent` 内 17485812 行: `if ( !pEvent->m_IsMustPlay )` | `m_IsMustPlay` は、イベント制御コードによって真偽値ゲートとして読み取られる。偽の場合、そのイベントは「必ず再生する」経路には入らない。 | 復元済み | `C-BEVENT-02`, `C-BEVENT-03` |

### ■ (***) `BattleEventCommand`
`BattleEventCommand` は、バトルイベント列に含まれるスクリプト上の各ステップを定義する。`BattleEventList` が「どの条件で何を実行するか」を記述するのに対し、`BattleEventCommand` は実際に実行される処理単位、すなわち会話表示、アニメーション再生、エフェクト生成、敵テクスチャ差し替え、BGM 変更などを担うテーブルと考えられる。

- `m_NextWait`: このコマンドを実行した後、次の処理へ進むまでの待機時間。単位は秒。
- `m_isWaitSkillTime`: 真の場合、スキル演出が完了するまで待機する。
- `m_isSwitchActionSkip`: アクションシーケンスのスキップを許可または強制するためのフラグと見られる。
- `m_PlayBgmBlock`: 何らかの処理が完了するまで BGM 変更をブロックするためのフラグと見られる。
- `m_PerformerID`: パーティ、ゲスト、敵などの実行者リストに対するインデックス、または特殊 ID と見られる。
- `m_PerformerIsLeader`: パーティリーダーを実行者として使用する。
- `m_PerformerEnemy`: 0 以外の場合、実行者が敵であることを示す。入力値として `EnemyID.m_id` を使うように見える。
- `m_TargetID`: 強制アクションまたはスキルの対象を表すものと見られる。関連する可能性のある列挙型は以下の通り。

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

- `m_EnableTarget`, `m_EnableAction`: このコマンド中、またはコマンド後に、プレイヤー側のターゲット選択や行動を有効化するための切り替えフラグ。『オクトパストラベラー0』の逆コンパイル結果では、`BattleEventCommand` の利用箇所において `m_EnableAction` が `AQPBattleCharacterBase::DisableNotAction(Character)` を呼び出し、状態が変化した場合はステータス UI を更新している。そのため、このフラグはスクリプト付きバトルイベント中に、行動不能状態を解除できる。
- `m_NameID`: 話者名のローカライズ ID。`GameTextCharacter` 配下にある。
- `m_TextID`: 会話本文のローカライズ ID。`GameTextEvent` 配下にある。
- `m_BalloonType`: バルーン、つまり吹き出しの形式。値 1 は通常の吹き出しである可能性が高い。
- `m_NoTail`: 吹き出しのしっぽを表示しない。
- `m_BaloonOffsetX/Y`: 実行者を基準にした吹き出し位置のオフセット。
- `m_NoName`: 真の場合、話者名を非表示にする。
- `m_NarrationID`: `NarrationList.m_id` の ID を取る。
- `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`: ナレーション表示形式に関係するキー。
- `m_AnimID`: 会話中、または会話の直前／直後に、実行者へ再生させるアニメーション。
- `m_DirID`: 実行者の向き、または向きプリセット。
- `m_isRewriteIdleMotion`: コマンド後も待機モーションの上書きを維持する。
- `m_SoundID`: サウンドを再生する。おそらく `SoundList` の ID。
- `m_SoundHandle`: 実行時ハンドル用のスロットと見られる。たとえば効果音を開始し、後続コマンドで停止または置換するためにハンドルを保持する用途が考えられる。
- `m_EffectID`: エフェクトを生成する。おそらく `EffectList` の ID。
- `m_EffectHandle`: 後続の操作や停止に使う、エフェクトの実行時ハンドル用スロットと見られる。
- `m_EffectPosition`: エフェクトを生成する位置。実行者、対象、画面上の位置などを表すと考えられる。
- `m_BgmID`: BGM を変更する。これもおそらく `SoundList` の ID。
- `m_FadeTime`: BGM 変更時のフェード時間。
- `m_IsRemoveAllAilments`: 状態異常を消去する。フェーズ遷移などで使われる可能性が高い。
- `m_EnableRetire`, `m_DisableGameOver`: 未検証。スクリプト付きバトル中に、リタイア UI や敗北時処理を強制的に有効／無効化するためのものと考えられる。
- `m_LastBattleMapColor`: バトル背景の色味制御と見られる。
- `m_TextureChangeEnemyIdx`: テクスチャを変更する敵スロット。`-1` は未使用を意味する。
- `m_ChangeEnemyTextureIdx`: 切り替え先のテクスチャ番号、またはスロット。`-1` は未使用を意味する。
- `m_TextureChangeTime`: テクスチャ差し替えのタイミング。遅延時間またはクロスフェード時間のいずれかと見られるが、未検証。
- `m_EnemyPartsIndex`: 影響を受ける敵パーツのインデックス。
- `m_EnemyPartsDisplay`: イベント中に参照された敵パーツを表示／非表示にする。
- `m_OnEventFlgIndex` / `m_OffEventFlgIndex`: `TacticalList` にも存在する。バトルエンジンはこれらのフラグに基づいてスクリプトを進行させている可能性が高い。つまり、発生済みの内容を追跡するための暗黙的な「次へ」の制御に近い。例として、`TacticalList` では双界の魔影戦において、リュシアンとリブラックのブレイク時に影の弱点を解放する戦術が実行済みかどうかを追跡し、両者がブレイクから復帰した後に弱点を再ロックするために用いられている。この状況専用の条件が別に存在するようには見えない。
- `m_JoinTrigger`: 「ユニットが今このバトルに参加する」系のイベントに結び付いている可能性が高い。例として、ゲストが戦闘途中に合流する場合が考えられる。
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`: 汎用構造体に収まりきらない特殊コマンド種別。`m_UniqueProcess` は以下の値を取る可能性が高い。

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

このアセットと関係する可能性のある列挙型は以下の通り。

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
#### ランタイム読解セマンティクス
【一次 C 証拠】

##### ◇ C-BCMD-01: `BattleEventCommand` 行の解決処理

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

##### ◇ C-BCMD-02: `BattleEventCommand` 基底コンストラクタと復元された名前付きコマンドフィールド

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

##### ◇ C-BCMD-03: `BattleEventCommand` の `m_EnableTarget` / `m_EnableAction` およびバトルマップ色の利用箇所

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

##### ◇ C-BCMD-04: `BattleEventCommand` の会話／ナレーション利用箇所

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

##### ◇ C-BCMD-05: `BattleEventCommand` のエフェクトおよびエフェクト位置の利用箇所

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

##### ◇ C-BCMD-06: `BattleEventCommand` の実行者／リーダー解決処理

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

【フィールド単位の解釈】

| キー | ランタイム上の C における位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_AnimID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;`（`m_AnimID` に対する合成された基準箇所） | この C 出力では、`m_AnimID` が個別の `Command->m_AnimID` 利用箇所としては出力されていない。コンストラクタ内で `m_DirID` と隣接するコマンドレーンとして扱うのが安全であり、`BattleEventCommand` シンボル配下ではランタイム上のアニメーション消費箇所は復元されていない。 | 未確定／部分的 | `C-BCMD-02` |
| `m_BalloonType` | `C-BCMD-04:17477766` `int m_BalloonType; // ecx`<br>`C-BCMD-04:17477784` `m_BalloonType = Command->m_BalloonType;`<br>`C-BCMD-04:17477785` `if ( (unsigned int)(m_BalloonType - 7) > 1 && (unsigned int)(m_BalloonType - 12) > 1 )` | `m_BalloonType` は `BeginBattleEventTalk` において、通常の吹き出し経路とナレーション経路の分岐に用いられる。 | 復元済み | `C-BCMD-04` |
| `m_BgmID` | `C-BCMD-02:16987020` `*(_QWORD *)&this->m_BgmID = 0;` | コマンド行は `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)` として解決される。このフィールドは `FBattleEventCommandBase` の初期化で名前付きフィールドとして現れるか、イベント実行器で消費される。Hex-Rays によって隣接フィールドが合成されている場合は、利用箇所が確認できる場合に限ってペアのレーンとして解釈する。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_BlackoutNarration` | `C-BCMD-04:17477815` `IsBackoutInvisible = Command->m_BlackoutNarration;` | `m_BlackoutNarration` は、ナレーション経路が暗転／不可視化挙動を伴う拡張ナレーション呼び出しを使うかどうかを制御する。 | 復元済み | `C-BCMD-04` |
| `m_ChangeEnemyTextureIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;`（`m_ChangeEnemyTextureIdx` に対する合成された基準箇所） | コマンド行は `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)` として解決される。このフィールドは名前付き初期化またはイベント実行器での消費により確認される。隣接フィールドの合成は、利用箇所で裏付けられる場合のみペアのレーンとして扱う。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_DirID` | `C-BCMD-02:16987017` `*(_QWORD *)&this->m_DirID = 0;` | コマンド行は `m_BattleEventCommand.m_DataList + 0xC8 * (baseIndex + commandIndex)` として解決される。`m_DirID` はコンストラクタで名前付きレーンとして初期化される。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_DisableGameOver` | `C-BCMD-02:16987037` `*(_QWORD *)&this->m_DisableGameOver = 0;` | `m_DisableGameOver` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。引用範囲では、これを消費する後続処理までは確定しない。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_EffectHandle` | `C-BCMD-05:17477471` `__int64 m_EffectHandle; // r13`<br>`C-BCMD-05:17477488` `m_EffectHandle = Command->m_EffectHandle;`<br>`C-BCMD-05:17477490` `v7 = m_EffectHandle - 1;` ほか 2 箇所の局所的な出現 | `m_EffectHandle` は `m_EffectID` とともにエフェクトコマンド経路で読み取られる。`m_EffectID` が存在しない場合でも、このハンドルは既存エフェクトスロットを参照できる。 | 復元済み | `C-BCMD-05` |
| `m_EffectID` | `C-BCMD-05:17477472` `int m_EffectID; // r15d`<br>`C-BCMD-05:17477489` `m_EffectID = Command->m_EffectID;`<br>`C-BCMD-05:17477491` `v2 = m_EffectID > 0;` ほか 2 箇所の局所的な出現 | `m_EffectID` は `m_EffectHandle` とともに読み取られる。正のエフェクト ID は、イベントエフェクト経路でエフェクト生成を有効化する。 | 復元済み | `C-BCMD-05` |
| `m_EffectPosition` | `C-BCMD-05:17477512` `else if ( UEnemyDB::GetBattlePosition(&pos, Command->m_EffectPosition) )`<br>`C-BCMD-05:17477514` `if ( this->m_IsRaidBattle && Command->m_EffectPosition == 1 )` | `m_EffectPosition` は `BeginBattleEventEffect` で消費される。位置値は `UEnemyDB::GetBattlePosition` により解決され、レイドバトルでは位置 `1` が特別扱いされる。 | 復元済み | `C-BCMD-05` |
| `m_EnableAction` | `C-BCMD-03:17477619` `if ( Command->m_EnableAction )` | `m_EnableAction` は `BeginBattleEventEnemyStatus` で直接消費される。真の場合、`DisableNotAction` を通じて対象キャラクターの行動不能状態を解除する。 | 復元済み | `C-BCMD-03` |
| `m_EnableRetire` | 引用された C 出力では、厳密な名前付きフィールドまたは一意に対応するオフセットレーンは復元されていない。 | この出力では `m_EnableRetire` シンボルを確認できない。現時点では未復元として扱うべきである。 | 未確定／部分的 | `C-BCMD-02` |
| `m_EnableTarget` | `C-BCMD-03:17477585` `if ( Command->m_EnableTarget )` | `m_EnableTarget` は `BeginBattleEventEnemyStatus` で直接消費される。真の場合、対象キャラクターの対象外状態を解除し、必要に応じてプレイヤー側のターゲットを再設定する。 | 復元済み | `C-BCMD-03` |
| `m_EnemyPartsDisplay` | `C-BCMD-02:16987032` `this->m_EnemyPartsDisplay = 0;` | `m_EnemyPartsDisplay` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。引用範囲では表示切り替えの具体的な消費箇所までは確定しない。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_EnemyPartsIndex` | `C-BCMD-02:16987031` `this->m_EnemyPartsIndex = 0;` | `m_EnemyPartsIndex` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。敵パーツを指定するためのレーンとして保持されるが、引用範囲では具体的な消費箇所までは確定しない。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_FadeTime` | `C-BCMD-02:16987030` `*(_QWORD *)&this->m_Fade = 0;`（`m_FadeTime` に対する合成された基準箇所） | コンストラクタでは `m_Fade` 領域として合成されている。`m_FadeTime` としての個別消費は、この引用範囲だけでは確定しない。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_IsNextNarrationPage` | `C-BCMD-04:17477822` `Command->m_IsNextNarrationPage,`<br>`C-BCMD-04:17477829` `Command->m_IsNextNarrationPage,` | `m_IsNextNarrationPage` は、ナレーションを開く関数へページ継続フラグとして渡される。 | 復元済み | `C-BCMD-04` |
| `m_IsRemoveAllAilments` | `C-BCMD-02:16987022` `*(_WORD *)&this->m_IsRemoveAllAilments = 0;` | `m_IsRemoveAllAilments` は `FBattleEventCommandBase` の名前付き領域として初期化される。引用範囲では全状態異常解除の消費箇所までは確定しない。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_JoinTrigger` | `C-BCMD-02:16987010` `*(_WORD *)&this->m_JoinTrigger = 0;` | `m_JoinTrigger` はコマンド基底構造体で初期化される。引用範囲では、合流イベントとしての具体的な分岐までは復元されていない。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_LastBattleMapColor` | `C-BCMD-03:17477649` `__int64 m_LastBattleMapColor; // rax`<br>`C-BCMD-03:17477655` `if ( !Command || !this->m_pModifyMaterialComponent || Command->m_LastBattleMapColor < 0 )`<br>`C-BCMD-03:17477745` `m_LastBattleMapColor = Command->m_LastBattleMapColor;` ほか 2 箇所の局所的な出現 | `m_LastBattleMapColor` は `BeginBattleEventLastBattleMapColor` で消費される。負値はこの処理を無効化し、それ以外の値はバトルマップ色テーブルを索引してマテリアル色を適用する。 | 復元済み | `C-BCMD-03` |
| `m_NameID` | `C-BCMD-02:16987013` `*(_QWORD *)&this->m_NameID = 0;` | `m_NameID` は `FBattleEventCommandBase` の名前付き領域として初期化される。会話表示で話者名を参照するためのローカライズ ID として扱うのが自然である。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_NarrationID` | `C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )`<br>`C-BCMD-04:17477821` `Command->m_NarrationID,`<br>`C-BCMD-04:17477828` `Command->m_NarrationID,` | `m_NarrationID` は、コマンドがナレーション系の吹き出しタイプを使う場合に `OpenNarrationC` / `OpenNarrationEXC` へ渡される。 | 復元済み | `C-BCMD-04` |
| `m_NextWait` | 引用された C 出力では、厳密な名前付きフィールドまたは一意に対応するオフセットレーンは復元されていない。 | `m_NextWait` シンボルは確認できない。コンストラクタは `m_Fade` などのタイミング領域を示すが、`m_NextWait` という別個のフィールドを証明するものではない。 | 未確定／部分的 | `C-BCMD-02` |
| `m_NoName` | `C-BCMD-02:16987012` `this->m_NoName = 0;` | `m_NoName` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。話者名非表示の制御レーンとして保持される。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_NoTail` | `C-BCMD-02:16987014` `this->m_NoTail = 0;` | `m_NoTail` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。吹き出しのしっぽ表示に関わるレーンとして保持される。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_OffEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;`（`m_OffEventFlgIndex` に対する合成された基準箇所） | `m_OnEventFlgIndex` と同じ 64 ビット初期化領域に含まれる隣接レーンと見られる。利用箇所が確認できない場合、個別の意味論は控えめに扱うべきである。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_OnEventFlgIndex` | `C-BCMD-02:16987026` `*(_QWORD *)&this->m_OnEventFlgIndex = 0;` | `m_OnEventFlgIndex` は `FBattleEventCommandBase` の名前付き領域として初期化される。イベントフラグのオン操作に関係するレーンと考えられる。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_OptCharacterID` | 引用された C 出力では、厳密な名前付きフィールドまたは一意に対応するオフセットレーンは復元されていない。 | `m_OptCharacterID` シンボルは確認できない。隣接する特殊処理オプション領域と `m_OptSkillID` は名前付きで現れるが、このフィールド自体は未確定である。 | 未確定／部分的 | `C-BCMD-02` |
| `m_OptSkillID` | `C-BCMD-02:16987039` `this->m_OptSkillID = 0;` | `m_OptSkillID` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。特殊処理用の任意スキル ID として保持される。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerEnemy` | `C-BCMD-02:16987011` `this->m_PerformerEnemy = 0;` | `m_PerformerEnemy` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。実行者を敵として扱うためのレーンと見られる。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerID` | `C-BCMD-02:16987009` `this->m_PerformerID = 0;` | `m_PerformerID` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。通常の実行者解決で参照される ID レーンである。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_PerformerIsLeader` | `C-BCMD-06:17482218` `if ( Command->m_PerformerIsLeader )` | `m_PerformerIsLeader` は `FindBattleEventCharacter` で消費される。真の場合、通常の実行者 ID 経路ではなく、パーティリーダー経路で実行者を解決する。 | 復元済み | `C-BCMD-06` |
| `m_PlayBgmBlock` | `C-BCMD-02:16987024` `this->m_PlayBgmBlock = 0;` | `m_PlayBgmBlock` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。BGM 変更のブロック制御に関係するレーンと見られる。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundHandle` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;` | `m_SoundHandle` は `FBattleEventCommandBase` の名前付き領域として初期化される。サウンドハンドルを保持するためのレーンと見られる。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_SoundID` | `C-BCMD-02:16987018` `*(_QWORD *)&this->m_SoundHandle = 0;`（`m_SoundID` に対する合成された基準箇所） | `m_SoundID` は `m_SoundHandle` と隣接する領域で初期化されていると見られる。引用範囲だけでは、サウンド再生側の直接消費箇所までは復元されていない。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_TargetID` | 引用された C 出力では、厳密な名前付きフィールドまたは一意に対応するオフセットレーンは復元されていない。 | `m_TargetID` シンボル、または一意に対応するコマンド行の消費箇所は確認できない。対象選択そのものはイベント実行器側で処理されるが、この出力ではその名前のフィールドとしては保存されていない。 | 未確定／部分的 | `C-BCMD-01` |
| `m_TextID` | `C-BCMD-04:17477765` `signed int m_TextID; // edi`<br>`C-BCMD-04:17477779` `m_TextID = Command->m_TextID;`<br>`C-BCMD-04:17477780` `if ( m_TextID <= 0 && Command->m_NarrationID <= 0 )` ほか 1 箇所の局所的な出現 | `m_TextID` は会話／ナレーション経路で読み取られる。`m_TextID` と `m_NarrationID` がどちらも非正値の場合、テキストは開かれない。 | 復元済み | `C-BCMD-04` |
| `m_TextureChangeEnemyIdx` | `C-BCMD-02:16987027` `*(_QWORD *)&this->m_TextureChangeEnemyIdx = 0;` | `m_TextureChangeEnemyIdx` は `FBattleEventCommandBase` の名前付き領域として初期化される。対象となる敵スロットを表すレーンと見られる。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_TextureChangeTime` | `C-BCMD-02:16987028` `this->m_TextureChangeTime = 0.0;` | `m_TextureChangeTime` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。テクスチャ変更の時間制御レーンとして扱われる。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_UniqueProcess` | `C-BCMD-02:16987038` `*(_QWORD *)&this->m_UniqueProcess = 0;` | `m_UniqueProcess` は `FBattleEventCommandBase` の名前付き領域として初期化される。通常の汎用コマンド処理では表しにくい特殊処理の種別を保持する。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_isRewriteIdleMotion` | `C-BCMD-02:16987025` `this->m_isRewriteIdleMotion = 0;` | `m_isRewriteIdleMotion` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。待機モーション上書きの維持に関わるレーンである。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |
| `m_isSwitchActionSkip` | 引用された C 出力では、厳密な名前付きフィールドまたは一意に対応するオフセットレーンは復元されていない。 | `m_isSwitchActionSkip` シンボルは C 出力内で確認できない。別の消費箇所がない限り、実証済みのライブフィールドとして記述するべきではない。 | 未確定／部分的 | `C-BCMD-02` |
| `m_isWaitSkillTime` | `C-BCMD-02:16987023` `this->m_isWaitSkillTime = 0;` | `m_isWaitSkillTime` は `FBattleEventCommandBase` の名前付きフィールドとして初期化される。スキル時間待機に関わるレーンとして保持される。 | 復元済み | `C-BCMD-01`, `C-BCMD-02` |

### ■ (*) `BattlePlaybackData` と `BattlePlayback`
この節では、名前がよく似た三つの要素をより厳密に区別する必要がある。

1. **`BattlePlaybacks`** は、復元された実行時データベーステーブルである。行型は `FBattlePlaybacksBase` であり、実行時コードは `ADatabaseDefineStatic::m_BattlePlaybacks.m_DataList` を通じてこれを読み取る。
2. **`FPlaybackData`** はデータベース行ではない。これは、セーブデータ層と `BattlePlaybacks` テーブルを結合した後に `ABattlePlayback` が使用する、実行時の一時オブジェクトである。
3. **`BattlePlaybackData`** は、一部のアセット出力で `m_EnemySkillID_01` のようなフィールドを伴ってダンプされるが、C 出力上では、生きた `ADatabaseDefineStatic` のデータベース面としては復元されていない。別個の利用箇所が見つからない限り、これらの奇妙なフィールドを実行時の `BattlePlaybacks` テーブルと同一視してはならない。

実用上の帰結として、バトル再生において改造上意味を持つテーブルは `BattlePlaybacks` である。このテーブルは、再生 ID を、敵スキル一つ、任意の敵実行者スロット、およびエフェクト表示フラグへ対応付ける。パーティ構成、敵グループ、レイドフラグ、ゲスト ID、主人公の外見は、セーブ／デバッグ用の再生データから供給される。その後、スキルおよびアクションのペイロードは `BattlePlaybacks` から取得される。

#### ■ `BattlePlaybacks`
実行時の行は、以下の構造として復元される。

| フィールド | C 行オフセット | 実行時の役割 | 証拠分類 |
|---|---:|---|---|
| `m_id` | `+0x08` | `FSaveDataBattlePlayback::Id` と比較されるキー。 | 行の直接走査 |
| `m_VisibleAttackEffect` | `+0x0C` | `FPlaybackData::IsVisibleSkillEffect` へコピーされるバイトフラグ。後に `AQPActionCommand::ProceedEffectiveOnlyLooks` へ渡される。 | ゲーム処理での直接利用 |
| `m_EnemySkillID` | `+0x10` | `FPlaybackData::EnemyUseSkillID` へコピーされるスキル ID。後に `AQPActionCommand::SetupActionCommand` へ渡される。 | ゲーム処理での直接利用 |
| `m_SkillPlayEnemyIndex` | `+0x14` | `FPlaybackData::SkillUseEnemyIndex` へコピーされる任意の敵スロット上書き値。`-1` は、行側で固定実行者が指定されていないことを意味する。 | ゲーム処理での直接利用 |

構造体サイズは 24 バイトである。SDK の列挙型 `EBATTLE_PLAYBACKS` は標準行の再生 ID に名前を与えているが、下記の主要な実行時経路は、データベース配列を線形走査して行 ID と比較する。この経路は、列挙型の値域を網羅する分岐ではない。例外はデバッグ用フォールバック経路であり、作成済みのセーブ再生が存在しない場合に、少数の ID に対するハードコードされた分岐を含む。

#### ■ `BattlePlaybackData`
ダンプされた `BattlePlaybackData` レコードのうち、次のような値を持つものは、

```json
"m_EnemySkillID_01": "m_EnemySkillID_01"
```

未解決のテンプレート／スキーマデータとして分類しておくべきである。C 出力は `BattlePlaybacksBase`、`BattlePlaybacks`、および一時的な `PlaybackData` を登録しているが、`m_EnemySkillID_01` / `m_EnemySkillID_02` に対応する生きた `ADatabaseDefineStatic::GetBattlePlaybackData` テーブルは公開していない。また、シンボル上では `FEndCreditBattlePlayackData` と綴りが誤っている別個のエンドクレジット用構造体も存在するが、これはここで扱うバトル再生データベースではなく、エンドクレジット管理側のデータ構造である。

#### ■ 実行時の読み取り意味論：コンパイル済み構造体とテーブル面

コンパイル済み登録データは、`BattlePlaybacksBase` と `BattlePlaybacks` を `BattlePlaybacks` 用のデータベース構造体として識別している。これとは別に、`ABattlePlayback` は一時構造体 `FPlaybackData` を保持する。この区別が重要なのは、テーブル行と実行時のアクションオブジェクトが同一オブジェクトではないためである。

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

解釈：実行時のバトル再生データベース面は `BattlePlaybacks` であり、複数列の `BattlePlaybackData` テーブルではない。エンドクレジット用構造体は、UI／エンディング側の別サブシステムに属する成果物であり、その存在は、奇妙にダンプされた `m_EnemySkillID_01` テーブルがバトル実行時入力であることを裏付けない。

#### ■ 実行時の読み取り意味論：`FBattlePlaybacksBase` の行レイアウト

`FBattlePlaybacksBase` は、24 バイトの小さな行として構築・コピーされる。コピー処理により、生きたペイロードのレーンは、行 ID が `+0x08`、表示バイトが `+0x0C`、敵スキル ID が `+0x10`、実行者インデックスが `+0x14` であることが確認できる。

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

解釈：`m_VisibleAttackEffect` は整数 ID ではなくバイトレーンである。`m_EnemySkillID` と `m_SkillPlayEnemyIndex` は 32 ビットのレーンである。この行レイアウト内には、`m_EnemySkillID_01` または `m_EnemySkillID_02` に相当する領域は存在しない。

#### ■ 実行時の読み取り意味論：静的データベースアクセスとロード済み判定

静的データベースクラスは、完全な `BattlePlaybacks` ラッパーとデータリスト配列の両方を公開する。バトル再生アクター自身は `GetBattlePlaybacksDB` を使用し、`m_BattlePlaybacks.m_Loaded` が真になるまではデータポインタを返さない。

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

解釈：このテーブルは、`FBattlePlaybacksBase` のデータリスト配列を持つ通常のロード済みデータベースアセットである。拡張可能性は、`EBATTLE_PLAYBACKS` 列挙型定義そのものではなく、データベース／インデックス機構と、再生 ID を渡す呼び出し側によって制約される。再生用のセーブデータまたはデバッグ呼び出し側がその ID を供給し、かつテーブルがロード済みであれば、この経路は新規行 ID も走査できる。

#### ■ 実行時の読み取り意味論：セーブ再生データとテーブル行の結合

`ABattlePlayback::CreatePlaybackData` は、二つの情報源を結合して `FPlaybackData` オブジェクトを構築する。パーティ、グループ、外見に関する値は `FSaveDataBattlePlayback` からコピーするが、アクションのペイロードは、一致する `BattlePlaybacks` 行から取得する。

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

解釈：データフローは `FSaveDataBattlePlayback::Id -> BattlePlaybacks.m_id -> FPlaybackData.{EnemyUseSkillID, SkillUseEnemyIndex, IsVisibleSkillEffect}` である。したがって、`m_EnemySkillID` はセーブ再生レコード内に保存されているのではなく、セーブ再生 ID を `BattlePlaybacks` へのデータベースキーとして用いることで復元される。

#### ■ 実行時の読み取り意味論：要求／プリロード段階

再生データが構築された後、`RequestBattlePlayback` はそれをアクターへコピーし、レイドと通常バトルの環境表示を設定し、敵グループを `AQPBattleManager` へ書き込み、`(EnemyGroupID, EnemyUseSkillID)` に対するエフェクトをプリロードし、そのグループの敵フリップブックを要求した後、主人公テクスチャを構築する。

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

解釈：`m_EnemySkillID` は、アクション実行前のリソース準備に関与する。したがって、ある再生行は、選択された敵グループの文脈で、そのスキル ID が必要なエフェクト／アニメーションのプリロードを支えられない場合、コマンド実行前の段階で意味論的に失敗し得る。

#### ■ 実行時の読み取り意味論：実行者選択とスキル検証

`PlayAction` は、`m_SkillPlayEnemyIndex` が有効な敵配列インデックスである場合、まずそれを優先する。行が有効なスロットを指定していない場合、この関数は、選択された敵グループ内から、戦術スキルリストに `m_EnemySkillID` を含む敵を検索する。現在のボスがそのスキルを使用可能であれば優先される。そうでない場合は、最初に一致した敵 ID がグループスロットへ戻され、その敵が実行者として使用される。その後になって初めて、コードは `AQPActionCommand::SetupActionCommand` を発行する。

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

解釈：`m_SkillPlayEnemyIndex` は、具体的な敵オブジェクトを直接選択できるため、戦術スキル所属の検索よりも強い指定である。有効な明示インデックスがない場合、`m_EnemySkillID` は、プレビューするアクションそのものとして扱われるだけでなく、妥当な敵実行者を選ぶための制約としても扱われる。したがって、再生行では通常、スロット上書きが有効であり、かつ視覚コマンドが不一致を許容することが分かっている場合を除き、`m_EnemyGroupID` 内の少なくとも一体が使用できるスキルを指定すべきである。

#### ■ 実行時の読み取り意味論：エフェクト表示フラグ

表示フラグは単にコピーされるだけではない。再生のティック処理中、この値はアクションコマンドの「見た目のみ」の進行関数へ渡される。

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

解釈：このフラグは、アクションコマンドが進行している間の視覚的／効果的な表示を制御する。ソース上の名称 `m_VisibleAttackEffect` は方向性としては正しいが、C の利用側では実行時フィールド名が `IsVisibleSkillEffect` になっている。そのため、この値は、タイミング指定やスキル選択パラメータではなく、見た目のみのアクション再生中にスキル／エフェクト表示を制御するスイッチとして説明すべきである。

#### ■ 実行時の読み取り意味論：デバッグ UI と一時オブジェクトのレイアウト

デバッグウィジェットは `FPlaybackData` レコードのマップを使用する。その抽出経路により、一時オブジェクト内のオフセットは、`EnemyUseSkillID` が `+0x58`、`SkillUseEnemyIndex` が `+0x5C`、`IsVisibleSkillEffect` が `+0x60`、`BossIndex` が `+0x64` であることが確認できる。

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

解釈：`FPlaybackData` は 104 バイトであり、セーブ再生状態の後ろにアクションプレビュー用フィールドを含む。この一時構造体は実行を理解するうえでは有用だが、データベーステーブル行と混同してはならない。

#### ■ フィールド単位の解釈

| キー | 実行時 C 上の位置 | 正確な解釈 | 状態 |
|---|---|---|---|
| `m_EnemySkillID` | `FBattlePlaybacksBase +0x10`; `CreatePlaybackData` によってコピーされ、`BattlePlaybackPreloadEffectAsync`、`UEnemyDB::HasTacticalSkill`、`AQPActionCommand::SetupActionCommand` によって利用される。 | 再生中に視覚的に実行するスキル ID。これは `SkillID` 名前空間の ID であるが、アクション実行者の解決は、選択された敵グループと任意のスロット上書きに依存する。 | 復元済み、直接利用 |
| `m_SkillPlayEnemyIndex` | `FBattlePlaybacksBase +0x14`; `FPlaybackData::SkillUseEnemyIndex` へコピーされ、`m_Enemies.ArrayNum` と照合される。 | 任意の敵スロット上書き値。0 未満、または生存している敵配列の範囲外の値は、戦術スキル所属に基づく実行者探索へフォールバックする。 | 復元済み、直接利用 |
| `m_VisibleAttackEffect` | `FBattlePlaybacksBase +0x0C`; `FPlaybackData::IsVisibleSkillEffect` へコピーされ、`ProceedEffectiveOnlyLooks` へ渡される。 | 真偽値形式のアクションエフェクト表示スイッチ。実行時の C 名は `IsVisibleSkillEffect` であるため、「見た目のみ」のアクション再生中にスキル／エフェクト表示を制御するものとして説明すべきである。 | 復元済み、直接利用 |
| `m_EnemySkillID_01` | 復元された `BattlePlaybacksBase` のレーンなし。復元された `ADatabaseDefineStatic::GetBattlePlaybackData` 面もなし。 | 生きたバトル再生実行時データであることは証明されていない。未解決のダンプ済みスキーマ／テンプレート素材として扱う。 | 否定的／未解決 |
| `m_EnemySkillID_02` | `m_EnemySkillID_01` と同じ。 | 生きたバトル再生実行時データであることは証明されていない。 | 否定的／未解決 |

#### ■ 作成上の含意

- 有効な `BattlePlaybacks` 行では、`m_EnemySkillID` を、その行が想定する `m_EnemyGroupID` の文脈と対応させるべきである。実行時は、そのグループ内から、要求されたスキルを戦術スキルセットに含む敵を検索するためである。
- 視覚上の実行者を決定論的に固定したい場合は、`m_SkillPlayEnemyIndex` を使用する。指定しない場合、実行時は、条件に合う現在のボス、またはそのスキルを持つ最初のグループ内敵を選ぶ可能性がある。
- `m_VisibleAttackEffect = false` はスキル選択を抑制しない。これは、アクションコマンドが既に作成された後の視覚進行経路に作用する。
- `EBATTLE_PLAYBACKS` 列挙型は、標準行に対する既定名称の資料として扱うのが最も安全である。復元可能なデータベース経路は、ロード済み配列を行 ID によって走査するため、本質的には列挙型値に制限されない。ただし、デバッグ用フォールバックの一部は、少数の標準 ID に対してハードコードされている。

### ■ (*) BattleResultBonus
各種の戦闘リザルトボーナス（「ノーダメージ」「オーバーキル」「ブレイク」「1ターンキル」など）について、倍率およびローカライズ ID を管理する。`m_Param` は、どのステータス（経験値、JP、リーフ）がボーナス対象になるかを示しているように見える。
- 0: リーフ
- 1: JP
- 2: 経験値
2 を超える未使用パラメータが存在するかどうかは未検証である。

#### ■ 実行時の読み取り意味論
`BattleResultBonus` の行は、`m_Param` ではなくボーナス種別によって選択される。復元されたゲーム処理上の述語は、`GetBattleResultBonusParam` が行を特定した後、オフセット `+0x10` のしきい値レーンのみを読み取る。

【一次 C 証拠】

##### ◇ C-BRESULT-01: ボーナス種別による BattleResultBonus 行の検索

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

##### ◇ C-BRESULT-02: ボーナス述語は行オフセット +0x10 を読む

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

【フィールド単位の解釈】

| キー | 実行時 C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_Param` | `C-BRESULT-01:17542787-17542801` は `m_BattleResultBonus.m_DataList` を、オフセット `+0x08` の行 ID によって走査する。<br>`C-BRESULT-02:17549619-17549627` は、復元された述語しきい値として `BattleResultBonusParam[16]` のみを読み取る。 | 復元された実行時経路では、正確な `m_Param` フィールドは検索キーではない。行は、`bonusType` と行オフセット `+0x08` の比較によって選択される。その後 `IsBattleResultBonus` は、戦闘中のカウンタを行オフセット `+0x10` と比較する。C 出力上では、独立した `m_Param` レーンをアセット固有に消費する箇所は特定されていないため、リーフ／JP／経験値という解釈は、証明済みの述語動作ではなく、作成時スキーマ上の仮説として扱うべきである。 | 否定的／部分的。ただし生きたテーブルである証拠あり | `C-BRESULT-01`, `C-BRESULT-02` |

#### ■ 詳細な実行時解析：`BattleResultBonus` の条件しきい値

既存のスキーマ水準の説明は、`m_Param` だけを報酬チャンネル選択子として扱う場合、不十分である。少なくとも一つの直接的な実行時利用箇所は、行の `+0x10` にある値を、リザルトボーナス条件が満たされたかを判定するしきい値として読み取る。

##### ◇ C-BATTLERESULTBONUS-LOOKUP-01: リザルトボーナス行の解決と 24 バイトのストライド

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

##### ◇ C-BATTLERESULTBONUS-THRESHOLD-02: 行のしきい値は `IsBattleResultBonus` によって使用される

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

解釈：`bonusType` は、`+0x08` の `m_id` によって行を選択する。`+0x10` の整数は、ボーナス種別に応じたしきい値である。すなわち、「ノーダメージ」では被ダメージ量、オーバーダメージでは比率、ブレイクではブレイク回数、ターン数系ではターン数に対応する。別のエクスポート済みフィールドが報酬チャンネルや UI テキストを制御しているとしても、それは別レーンであり、ここに示したしきい値の利用箇所と混同してはならない。

### ■ (**) `BattleVoiceList` と `BattleVoiceArenaList`
戦闘中にパーティメンバーまたはナレーターが再生する各種ボイス行を定義する（例：オル・ガルデラ戦における特殊ボイス行）。

関連している可能性が高い列挙型：
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
#### ■ 実行時解析：`BattleVoiceList` と `BattleVoiceArenaList`
ボイステーブルは実行時に有効なテーブルである。ただし、この確認パスで最も強く復元できた直接利用箇所は、闘技場用の上書き経路である。通常の戦闘ボイス経路は、すでに実体化されたボイス ID 候補リストを受け取り、必要に応じて闘技場用の差し替えを適用し、連続再生制限に該当する候補を除外したうえで、具体的なボイス ID を一つ選択する。

##### ◇ C-BATTLEVOICEARENA-LOOKUP-01: 闘技場ボイス行はキーインデックスで検索され、ストライドは 208 バイトである

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

##### ◇ C-BATTLEVOICEARENA-CANDIDATES-02: 闘技場行は候補ボイス ID リストを上書きできる

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

解釈：`BattleVoiceArenaList` は、通常の戦闘ボイス候補リストに重ねるオーバーレイである。これはボイス選択サブシステム全体を置き換えるものではない。戦闘が闘技場戦として認識された場合に限り、候補を条件付きで差し替え、または絞り込む。

### ■ (***) BattleAbortConditions
『オクトパストラベラー0』における戦闘終了／強制結果条件テーブルである。各行は小さな述語の束を定義し、戦闘中にそれらが満たされると、参照元の `EnemyGroups` フィールドに応じて、戦闘を中断（早期終了）させる、または強制勝利／強制敗北として扱う。
- `m_Conditions`: 条件種別 ID。`TacticalList`、`TacticalActionList`、`SkillConditionList`、`BattleEventList` と同じ数値条件名前空間を共有する。
- `m_Params`: 各条件に対するパラメータ。意味は条件種別によって異なる。
- 特定の条件種別においてのみ意味を持つフィルタ：
   - `m_AilmentTypes`: `SkillAilmentType.m_id` を入力として取る。
   - `m_StatusTypes`: `CharaStatusID.m_id` を入力として取る可能性がある。
   - `m_WeaponTypes`: `WeaponType.m_id` を入力として取る。
   - `m_MagicTypes`: `Magictype.m_id` を入力として取る。
   - `m_EnemyID`: `EnemyID.m_id` を入力として取る。

#### ■ 実行時証拠の統合メモ
- `Octopath_Traveler0-Win64-Shipping.exe.c` の逆コンパイル出力に基づくアンカー：
    - `AQPBattleManager::CheckBattleAbort` は、該当する `BattleAbortConditions` 行を解決し、その行の 7 番目のフィルタ配列（`v17 + 7`、すなわち `m_EnemyID` レーン）を `UBattleUtility::CheckConditions` へ渡す。さらに `CheckConditions` は、そのレーンを `EnemyID` 引数として `UBattleUtility::CheckCondition` へ転送する。これにより、このフィールドが単にコピーされたスキーマ要素ではなく、能動的な条件フィルタであることが証明される（`Octopath_Traveler0-Win64-Shipping.exe.c`:17493321-17493429, 17493527-17493542, 17389535-17389666）。
- 補足の名前付きランタイムアクセサ：
    - `0x144373530` `ADatabaseDefineStatic::execGetBattleAbortConditions`: このテーブルに対する名前付きの実行時アクセサ。

#### ■ 実行時の読み取り意味論
【一次 C 証拠】

##### ◇ C-GROUP-02: EnemyGroups の実体化処理と具体的な行オフセット

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

##### ◇ C-BEVENT-03: 共有される戦闘条件タプル評価器

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

##### ◇ C-BABORT-01: BattleAbortConditions 構造体は七つの条件／フィルタレーンをコピーする

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

【フィールド単位の解釈】

| キー | 実行時 C 上の位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_AilmentTypes` | 異常種別レーン（配列）。引用ブロック内では、より短い名前付き代入は残っていない。 | `BattleAbortConditions` 行は、七つの並列な `TArray<int>` レーンとしてコピーされる。共有の `UBattleUtility::CheckConditions` 評価器は、条件、パラメータ、ステータス、異常、武器、魔法、敵 ID の各レーンをスロット単位で消費する。対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Conditions` | 条件レーン（配列）。引用ブロック内では、より短い名前付き代入は残っていない。 | `BattleAbortConditions` 行は、七つの並列な `TArray<int>` レーンとしてコピーされる。共有の `UBattleUtility::CheckConditions` 評価器は、条件、パラメータ、ステータス、異常、武器、魔法、敵 ID の各レーンをスロット単位で消費する。対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BABORT-01`, `C-BEVENT-03` |
| `m_EnemyID` | 敵 ID レーン（配列）。引用ブロック内では、より短い名前付き代入は残っていない。 | `BattleAbortConditions` 行は、七つの並列な `TArray<int>` レーンとしてコピーされる。共有の `UBattleUtility::CheckConditions` 評価器は、条件、パラメータ、ステータス、異常、武器、魔法、敵 ID の各レーンをスロット単位で消費する。対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BABORT-01`, `C-BEVENT-03` |
| `m_MagicTypes` | 魔法種別レーン（配列）。引用ブロック内では、より短い名前付き代入は残っていない。 | `BattleAbortConditions` 行は、七つの並列な `TArray<int>` レーンとしてコピーされる。共有の `UBattleUtility::CheckConditions` 評価器は、条件、パラメータ、ステータス、異常、武器、魔法、敵 ID の各レーンをスロット単位で消費する。対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BABORT-01`, `C-BEVENT-03` |
| `m_Params` | パラメータレーン（配列）。引用ブロック内では、より短い名前付き代入は残っていない。 | `BattleAbortConditions` 行は、七つの並列な `TArray<int>` レーンとしてコピーされる。共有の `UBattleUtility::CheckConditions` 評価器は、条件、パラメータ、ステータス、異常、武器、魔法、敵 ID の各レーンをスロット単位で消費する。対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BABORT-01`, `C-BEVENT-03` |
| `m_StatusTypes` | ステータス種別レーン（配列）。引用ブロック内では、より短い名前付き代入は残っていない。 | `BattleAbortConditions` 行は、七つの並列な `TArray<int>` レーンとしてコピーされる。共有の `UBattleUtility::CheckConditions` 評価器は、条件、パラメータ、ステータス、異常、武器、魔法、敵 ID の各レーンをスロット単位で消費する。対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BABORT-01`, `C-BEVENT-03` |
| `m_WeaponTypes` | 武器種別レーン（配列）。引用ブロック内では、より短い名前付き代入は残っていない。 | `BattleAbortConditions` 行は、七つの並列な `TArray<int>` レーンとしてコピーされる。共有の `UBattleUtility::CheckConditions` 評価器は、条件、パラメータ、ステータス、異常、武器、魔法、敵 ID の各レーンをスロット単位で消費する。対応するスロットが存在しない場合は 0 として扱われる。 | 復元済み | `C-BABORT-01`, `C-BEVENT-03` |

### ■ (*) `BattleActionID`
「アクション ID」（おそらく `SkillAilmentType.m_ActionID`）を、具体的なバトルアニメーション／モーションへ対応付ける小規模な参照テーブル。あわせて、そのアニメーションを標準のバトル用リグではなく、NPC／フィールドキャラクター用リグで再生するべきかを示すフラグも持つ。`m_UseNPC` は、このアクションを通常のバトルスプライトではなく、NPC キャラクター用のアニメーションセットで再生するかどうかを決める可能性がある。
関連が想定されるものとして、`Kingship_structs.hpp` は一部のアクション ID に次のラベルを割り当てている:
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
#### ■ 実行時の読み取り意味論
`BattleActionID` 行は単なるラベル表ではない。プレイヤーキャラクター側のバトルアクション経路は `ActionID` で行を解決し、バイトオフセット `+0x10` を `m_UseNPC` のゲートとして読み取る。このゲートは、対象キャラクターオブジェクトの `m_bNpcFlag` が立っている場合にのみ評価される。

【一次 C 証拠】

##### ◇ C-BACTION-01: `ActionID` による `BattleActionID` 行解決
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

##### ◇ C-BACTION-02: NPC 専用アクションゲートとしての `m_UseNPC`
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

【フィールド単位の解釈】

| キー | ランタイム上の C 位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_UseNPC` | `C-BACTION-02:17577515` `ActionDB = AQPCharacterBattlePlayer::GetActionDB(this, ActionID);`<br>`C-BACTION-02:17577516` `if ( !ActionDB || !*(_BYTE *)&ActionDB[16] )`<br>`C-BACTION-01:17579948` `m_BattleActionID.m_DBAccess`<br>`C-BACTION-01:17579958` `Data[24 * ArrayIndex]` | `m_UseNPC` は 24 バイト長の `BattleActionID` 行における `+0x10` のバイトである。`AQPCharacterBattlePlayer::ExecPlayBattleAction` では、`this->m_bNpcFlag` が真の場合に限りこのフィールドが検査される。行が存在しない、またはこのバイトが 0 の場合、アニメーション解決前に要求されたアクション ID は `1` へ強制される。すなわち、NPC／フィールドリグのキャラクターは待機アクションへフォールバックする。引用箇所の関数では、NPC ではないプレイヤーキャラクターはこのガード経路を通らない。 | 復元済み | `C-BACTION-01`, `C-BACTION-02` |

### ■ (*) `BattleParams`
バトル関連のさまざまなパラメータ群。Dumper-7 SDK の `Kingship_structs.hpp` によれば、各値の意味は次のとおり:
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
#### ■ 実行時の読み取り意味論
`BattleParams` は二段階の仕組みで読み取られる。バトルマネージャの初期化または使用時に、データベース行が `m_AllBattleParamList` へ正規化される。その後、ランタイム側の呼び出し箇所は `EBATTLE_PARAMS` の数値を符号なしバイトの添字として用い、このキャッシュ済みベクタを参照する。キャッシュは、同一の編集値に対して文字列、整数、浮動小数点値、真偽値という 4 種類の型付きビューを保持する。

【一次 C 証拠】

##### ◇ C-BPARAM-01: `BattleParams` 行はデータベーステーブルからコピーされる
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

##### ◇ C-BPARAM-02: 数値文字列からキャッシュ済み整数／浮動小数点値／真偽値への変換
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

##### ◇ C-BPARAM-03: `EBATTLE_PARAMS` ID は `m_AllBattleParamList` の添字として使われる
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

##### ◇ C-BPARAM-04: 浮動小数点アクセサはキャッシュ済みの浮動小数点スロットを読む
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

##### ◇ C-BPARAM-05: 整数アクセサはキャッシュ済みの整数スロットを読む
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

【ランタイム上の解釈】

| ランタイム要素 | ランタイム上の C 位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `EBATTLE_PARAMS` ID / 行 ID | `C-BPARAM-01:17537220` `v13 = *(_DWORD *)&v12[-8];`<br>`C-BPARAM-03:17542742` `if ( battleParam < this->m_AllBattleParamList.ArrayNum )` | データベース行 ID は、`m_AllBattleParamList` に対する密な添字として使われる。したがって、本ガイドに示した列挙型定数は説明用のラベルにとどまらず、ランタイム上のキーでもある。 | 復元済み | `C-BPARAM-01`, `C-BPARAM-03` |
| 文字列値 | `C-BPARAM-01:17537225` `v16 = *(int *)&v12[8];`<br>`C-BPARAM-01:17537226` `v17 = *(const wchar_t **)v12;` | 元テーブルの値は、データベース行の文字列レーンに `FString` に近いペイロードとして格納される。それがキャッシュ済みの `BattleParam.StringValue` へコピーされる。 | 復元済み | `C-BPARAM-01` |
| 整数値 | `C-BPARAM-02:17537302` `*(_DWORD *)&v21[8 * v22 + 24] = *(_DWORD *)&battleParam.BoolValue;`<br>`C-BPARAM-05:17395117` `v1 = *(_DWORD *)&v5[24];` | キャッシュ構築時、数値文字列は `_wtoi` で解析される。後続のキャッシュ済み整数アクセサは、キャッシュ済み `BattleParam` レコードの `+0x18` を読み取る。 | 復元済み | `C-BPARAM-02`, `C-BPARAM-05` |
| 浮動小数点値 | `C-BPARAM-02:17537274` `v19 = _wtof(v18);`<br>`C-BPARAM-02:17537303` `*(_DWORD *)&v21[8 * v22 + 28] = *((_DWORD *)&battleParam.BoolValue + 1);`<br>`C-BPARAM-04:17395003` `v3 = *(float *)&v5[28];` | キャッシュ構築時、数値文字列は `_wtof` で解析される。後続のキャッシュ済み浮動小数点アクセサは、キャッシュ済み `BattleParam` レコードの `+0x1C` を読み取る。 | 復元済み | `C-BPARAM-02`, `C-BPARAM-04` |
| 真偽値 | `C-BPARAM-02:17537281` `v20 = FString::ToBool((FString *)v12);`<br>`C-BPARAM-02:17537304` `v21[8 * v22 + 32] = (FScriptContainerElement)v36;` | 真偽値ビューは、キャッシュ構築時に `FString::ToBool` によって得られ、キャッシュ上の `+0x20` に格納される。 | 復元済み | `C-BPARAM-02` |
#### ■ `BattleParams` のキャッシュ化と型付きアクセサ
`BattleParams` は、毎回データベースから場当たり的に読み取られるわけではない。バトルマネージャはまず全行を `EBATTLE_PARAMS` で添字付けされた型付きランタイム配列へキャッシュし、その後のヘルパーは固定オフセットから文字列／整数／浮動小数点値／真偽値のビューを読む。元データが DB テーブルであるにもかかわらず、列挙型風の ID 一覧が実運用上重要なのはこのためである。

##### ◇ C-BATTLEPARAMS-CACHE-BUILD-01: 行の値はパラメータ ID ごとにキャッシュされる
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

##### ◇ C-BATTLEPARAMS-CACHE-INDEX-02: バトルパラメータ ID はランタイムキャッシュを直接添字付けする
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

##### ◇ C-BATTLEPARAMS-TYPED-WRAPPERS-03: 型付きラッパーはキャッシュ済みフィールドを公開する
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

解釈： `BattleParams` 行は複数の型付き表現へ解析される。生のデータベース値は文字列に近いが、ランタイムキャッシュはキャッシュ済み `BattleParam` エントリの `+0x18`、`+0x1C`、`+0x20` に、それぞれ整数、浮動小数点値、真偽値のビューを公開する。したがって、新しいパラメータ ID を追加することは、単に行を追加するだけでは済まない。呼び出し側コードがその ID を要求し、さらにキャッシュがその数値 ID を収容できる大きさでなければならない。


### ■ (*) `BattleDeathBlowPoint`
「必殺技」ゲージ増加量の参照テーブル。各行は、特定の判定条件（種類 + 値）が満たされたときに、必殺技ゲージをどれだけ増加させるかを定義する。
- `m_CheckType`: 何を判定するかを示す種類。
- `m_CheckValue`: その種類に対するしきい値または量。カウント値として扱われているように見える。
- `m_IncreasedPoint`: 条件に一致したときに加算される「必殺技ポイント」の量。

#### ■ 実行時の読み取り意味論
`BattleDeathBlowPoint` は `AQPBattleManager::CheckSpecialSkillPoint` によって消費される。逆コンパイル結果のホットループ内ではフィールド名が保持されていないが、コピーされる行ストライドとオフセット使用法から、3 つのペイロードフィールドは明確に特定できる。オフセット `+0x0C` は判定種別セレクタ、`+0x10` は判定値、`+0x14` はポイント増加量である。

【一次 C 証拠】

##### ◇ C-BDEATH-01: `BattleDeathBlowPoint` テーブルコピーと 24 バイトの行ストライド
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

##### ◇ C-BDEATH-02: 判定種別ディスパッチと `+0x10/+0x14` の使用
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

##### ◇ C-BDEATH-03: 敵側しきい値分岐と最終的な `risePoint` 格納
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

【フィールド単位の解釈】

| キー | ランタイム上の C 位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_CheckType` | `C-BDEATH-01:17515519-17515523` ソース側オフセット `+0x0C/+0x10/+0x14` をローカルの 24 バイト行コピーへ複写する。<br>`C-BDEATH-02:17515786` `v56 = *(_DWORD *)&v52[12];`<br>`C-BDEATH-02:17515793` `if ( (unsigned __int8)v56 == 1 )`<br>`C-BDEATH-02:17515795` `if ( (unsigned __int8)v56 == 2 )`。同じブロックの後方には種類 `3`、`4`、`5` の分岐も存在する。 | `m_CheckType` は、行オフセット `+0x0C` の 32 ビットレーンに格納される。ただし評価器は意味論的にはその下位バイトで分岐する。復元されたケースは C エクスポート上で列挙型名として残っているのではなく、分岐コードとして現れている。種類 `1` は敵側の予測ダメージしきい値経路へ入る。種類 `2` は予約済みダメージを伴うガード対象を検査する。種類 `3` は敵ではない対象に対するヒット／ブースト／武器経路である。種類 `4` は敵ではない対象に対するヒット／ブースト／非武器スキル経路であり、さらにガード／支援／アイテムの除外条件を持つ。種類 `5` は新たにブレイクした対象数を数える。 | 復元済み | `C-BDEATH-01`, `C-BDEATH-02`, `C-BDEATH-03` |
| `m_CheckValue` | `C-BDEATH-02:17515784` `v54 = *(_DWORD *)&v52[16];`<br>`C-BDEATH-02:17515991` `if ( v50->m_UseBoostLevel == v54 && v53 )`<br>`C-BDEATH-03:17516102` `v91 = (int)(float)((float)((float)v54 * 0.0099999998) * (float)v90);` | `m_CheckValue` は行オフセット `+0x10` である。その意味は種類に依存する。種類 3／種類 4 のプレイヤー側分岐では `m_UseBoostLevel` と比較され、種類 1 の敵側分岐では対象のキャッシュ済み最大 HP に対する百分率しきい値として解釈される。 | 復元済み | `C-BDEATH-02`, `C-BDEATH-03` |
| `m_IncreasedPoint` | `C-BDEATH-02:17515785` `v55 = *(_DWORD *)&v52[20];`<br>`C-BDEATH-02:17515822` `risePoint[...] = v55;`<br>`C-BDEATH-02:17515955` `v64 = v58 * v55;`<br>`C-BDEATH-03:17516143` `risePoint[...] = InOutElementIndex;` | `m_IncreasedPoint` は行オフセット `+0x14` である。一時配列 `risePoint` へ書き込まれる基本ゲージ増加量として機能する。種類 `5` では、新たにブレイクした対象数によってこの値が乗算される。最終的な増加量は、現在の必殺技ゲージレベルによるボーナスと、サポートアビリティ `DEATHBLOWPOINT_UP` によってさらに補正されたうえで、`AddSpecialSkillGaugeValue` が呼び出される。 | 復元済み | `C-BDEATH-02`, `C-BDEATH-03` |



#### ■ 詳細ランタイム解析: `BattleDeathBlowPoint` のディスパッチとゲージ変異

`BattleDeathBlowPoint` は実戦闘で使用されるテーブルである。ランタイムはテーブルをコピーし、24 バイト単位の各行を反復し、判定種別の下位バイトをディスパッチセレクタとして解釈したうえで、候補となるゲージ増加量を `risePoint` へ追加する。同じ関数内の後段では、0 ではない各 `risePoint` がバトルパラメータおよびサポートアビリティによって補正され、その後 `AddSpecialSkillGaugeValue` がプレイヤー側ゲージを変異させる。

##### ◇ C-BATTLEDEATHBLOW-WORKLIST-01: テーブルは呼び出し時の作業リストへコピーされる
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

##### ◇ C-BATTLEDEATHBLOW-ROW-FIELDS-02: 各行は判定種別、判定値、増加ポイントを供給する
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

##### ◇ C-BATTLEDEATHBLOW-CHECK-BRANCHES-03: ブレイク、ブースト行動、ダメージしきい値分岐がゲージ増加へ接続される
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

##### ◇ C-BATTLEDEATHBLOW-GAUGE-APPLY-04: 最終ポイント適用は補正後に必殺技ゲージを変異させる
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

解釈： 判定種別名が C エクスポート内で匿名のままであっても、復元されたフィールドの役割は強く裏付けられている。`m_CheckType` は行 `+0x0C` のディスパッチセレクタ、`m_CheckValue` は `+0x10` の比較値／百分率値／ブーストレベル値、`m_IncreasedPoint` は `+0x14` の基本ポイントペイロードである。これらの分岐は汎用的なしきい値テーブルではなく、ガードダメージ、ブーストレベル、直前にブレイクした対象、予約済みダメージしきい値といった具体的な戦闘状態に結び付いている。

### ■ (*) `BattleLayoutSet`
カメラプリセットと敵側の位置指定を結び付ける、小規模なバトル演出／配置プリセットテーブルと思われる。ゲーム内のどこで使われるかは完全には明確でない（たとえば、フォーメーションや増援には `PositionID` 270 を使用しているものが見当たらない）。

`Kingship_structs.hpp` は、一部のレイアウトセットに次のラベルを割り当てている:
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
#### ■ 実行時の読み取り意味論
C エクスポートから、`BattleLayoutSet` がレイド表示カメラ経路における実際のランタイムデータであることが確認できる。レイアウト ID はデータベースアクセスヘルパーを通じて解決され、その行は少なくとも `m_CameraParamID` と `m_EnemyPosition` を含む `FBattleLayoutSetBase` として消費される。

【一次 C 証拠】

##### ◇ C-BLAYOUT-01: `BattleLayoutSet` 行の検索
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

##### ◇ C-BLAYOUT-02: レイド表示によって消費される `m_CameraParamID` と `m_EnemyPosition`
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

##### ◇ C-BLAYOUT-03: `UpdateRaidView` におけるレイアウト ID 1、0、および呼び出し側指定オフセット
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

【フィールド単位の解釈】

| キー／行フィールド | ランタイム上の C 位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_id` / レイアウト ID | `C-BLAYOUT-01:17542710` `if ( layoutID <= 0 )`<br>`C-BLAYOUT-01:17542720` `m_BattleLayoutSet.m_DBAccess`<br>`C-BLAYOUT-01:17542729` `Data[24 * ArrayIndex]` | レイアウト ID は `m_BattleLayoutSet.m_DBAccess` を通じて解決される。ID `0` はこの取得関数で拒否される。返される行ストライドは 24 バイトである。 | 復元済み | `C-BLAYOUT-01` |
| `m_CameraParamID` | `C-BLAYOUT-02:17510719` `m_CameraParamID = battleLayout->m_CameraParamID;`<br>`C-BLAYOUT-02:17510721` `if ( m_CameraParamID <= 0 ) return 0;`<br>`C-BLAYOUT-02:17510778` `StartFreeMove(..., m_CameraParamID, ...)` | `m_CameraParamID` は、そのレイアウトが選択する主要なカメラプリセットである。0 以下の場合、更新は失敗する。レイドオフセットモードでない場合は `ABattleCameraBase::StartFreeMove` へ直接渡され、レイドオフセットモードでは別レイアウトのカメラ ID が弧状オフセット ID として使われる。 | 復元済み | `C-BLAYOUT-02` |
| `m_EnemyPosition` | `C-BLAYOUT-02:17510747` `m_EnemyPosition = offset->m_EnemyPosition;`<br>`C-BLAYOUT-02:17510759` `if ( m_EnemyPosition > 0 )`<br>`C-BLAYOUT-02:17510762` `UEnemyDB::GetBattlePosition(&Position, m_EnemyPosition)` | `m_EnemyPosition` は、オフセット用レイアウトから読み取られる任意のバトル位置 ID である。正の値の場合、`UEnemyDB::GetBattlePosition` によって解決され、レイド用の敵オフセット変換へ格納される。これは推測上のフォーメーション参照ではなく、直接的なランタイム使用である。 | 復元済み | `C-BLAYOUT-02` |

## ■ `AIBattle` 配下
これらのファイルは、敵 AI の挙動、および敵が使用するスキルとその使い方を担う。

敵 AI について、エンジンは次の順に読み取る:
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

### ■ (****) `TacticalList`, `TacticalAssignList`, `TacticalSkillList`
本作における敵 AI の「ルール節」に相当するテーブルである。各エントリは条件集合を定義し、その条件が満たされたときに、AI が特定の挙動を選択または有効化できるようにする。敵には `TacticalAssignList` でこれらの「ルール」が割り当てられ、その `m_id` は `EnemyID.m_TacticalAssignID` に対応する。戦闘スキルは `TacticalSkillList` を通じて割り当てられ、その `m_id` は `EnemyID.m_SkillsID` に対応する。
- `m_Presage`: このタクティクスが予兆（行動の事前提示）を表示させるかどうかを示す列挙値と見られる。
- `m_PresageSkillID`: 予兆に対応するスキル ID、すなわち事前に提示される行動の ID。たとえばボスがブーストモード突入を告知する場合に使われる。この種のスキル ID は、敵の `TacticalSkillList.m_UseSkills` 配列には存在しないことが目立つ。
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: イベントフラグによるゲートおよび副作用。`BattleEventCommand` も参照。AI の分岐やシーケンス制御に使われている可能性が高い。
- `m_Conditions`: `TacticalActionList`、`BattleEventList`、`SkillConditionList` と同じ条件体系を使うと見られる。おそらく `m_Params` および各フィルタ配列も同じ並列レーンとして扱われる。
   - `m_AilmentTypes`（`SkillAilmentType.m_id` を入力として取る）
   - `m_StatusTypes`（おそらく `CharaStatusID.m_id` を入力として取る）
   - `m_WeaponTypes`（`WeaponType.m_id` を入力として取る）
   - `m_MagicTypes`（`Magictype.m_id` を入力として取る）

各条件 ID の意味は、まだ完全には命名されていない。現在のガイド一式における索引として、ローカルの [Condition Crossref Atlas](Octopath_Condition_Crossref_Atlas.md) を参照すること。そこでも形状、参照元テーブル、パラメータレーンまでしか証明されていない条件 ID は、未解決のまま扱うべきである。`../raw_evidence/` 配下に取り込まれた移植資料リポジトリのスナップショットは歴史的な根拠資料であり、現行の『オクトパストラベラー0』v1.0.8 における名称の最終的な典拠ではない。

#### ■ 実行時証拠の統合メモ
過去のアドレス単位のタクティカル解析メモは、下記の名前付き `UTacticalDB`、`UBattleTacticalComponent`、`UBattleUtility` の証拠へ統合した。現在の C エクスポートでは、型付きの行アクセサと名前付きの条件評価呼び出しが得られるため、本節では、引用 C 抜粋にアドレスが本質的に含まれる場合を除いて、古い `sub_...` 名称を用いない。条件 ID は、スキーマ上の名前だけではなく、引用した評価器経路によって解釈する必要がある。

### ■ (****) `TacticalActionList`
タクティクスが適用されたとき、ゲームが実際に何を行うかを指定するテーブルと見られる。すなわち、どのスキルを使うか（インデックス指定）、誰を対象にするか、候補対象をどのように選ぶか、さらに追加の条件や優先規則を保持する。
- `m_SkillIndex`: これは `SkillID` そのものではない。敵の `m_SkillsID` が指すスキルリスト、すなわち `TacticalSkillList.m_UseSkills[]` へのインデックスである。
- `m_SelectType`: 選択モードの列挙型。`Kingship_structs.hpp` では次のように定義されている。

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

- `m_FriendlyIndex`: 敵側の視点から見た「味方」側の対象指定と考えられる。ここでの `-1` は、おそらく「固定された味方対象なし」、あるいは `SelectType` に従って選ぶことを意味する。
- `m_PrioritySkill`: 未検証。設定されている場合、通常の選択を上書きして特定のスキルインデックスを強制する、または特定のスキルインデックスを優先候補としてマークする可能性がある。
- `m_TurnCount`: 未検証。指定されたタクティクス ID の条件が満たされている場合、このキーの値 `N` ごとに行動を繰り返すものと考えられる。ただし、`SelectType` によって意味が変化する可能性がある。
- `m_GroupIndex`: グループ化キー。フェーズごとに行動を分ける、1 ターンあたりのグループ内行動数を制限する、または同一グループ内の複数敵の行動を協調させる用途が考えられる。
- 行動単位の追加条件（`TacticalList` と同様だが、指定された行動に固有）: `m_Conditions`, `m_Params`, `m_AilmentTypes`, `m_StatusTypes`, `m_WeaponTypes`, `m_MagicTypes`

#### ランタイム読み取りセマンティクス
【一次 C 証拠】

##### ◇ C-ENEMY-02: 具体的な行オフセットを伴う `EnemyID` 実体化処理

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

##### ◇ C-TACT-01: `TacticalList` / `TacticalSkillList` の行解決とスキルリストアクセサ

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

##### ◇ C-TACT-02: `TacticalActionList` の行解決

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

##### ◇ C-TACT-03: `TacticalActionList` の条件レーン消費処理

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

##### ◇ C-TACT-04: タクティカル条件タプルの評価器

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

##### ◇ C-TACT-05: 候補決定時における `TacticalActionList` のスキルインデックスと `SelectType` セマンティクス

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

##### ◇ C-TACT-06: `TacticalActionList` の推奨フィルタ

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

##### ◇ C-TACT-07: シーケンシャル予兆／行動におけるグループ・ターン系フィールドの読み取り

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

【フィールド単位の解釈】

| キー | ランタイム上の C 位置 | 解釈 | 状態 | 証拠 |
|---|---|---|---|---|
| `m_AilmentTypes` | `+0x48`（配列） | 行動行は、行 `+0x28` 以降に条件／フィルタレーンを格納する。`CheckActionCondition` は、これら 6 本の並列配列をタクティカル条件評価器へ渡す。 | 復元済み | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_Conditions` | `+0x28`（配列） | 行動行は、行 `+0x28` 以降に条件／フィルタレーンを格納する。`CheckActionCondition` は、これら 6 本の並列配列をタクティカル条件評価器へ渡す。 | 復元済み | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_FriendlyIndex` | `+0x14`（スカラー） | 行動行の `+0x14` は、候補決定時にローカルな選択子へ読み込まれる。Hex-Rays はここで DB フィールド名を保持していないが、このレーンの役割は対象／味方側の選択子であり、スキル ID ではない。 | 復元済み | `C-TACT-05` |
| `m_GroupIndex` | `+0x20`（スカラー） | シーケンシャル予兆のロジックは行 `+0x20` を読み取り、直前に見たグループ値と比較する。これが行動グループ化レーンに対する、現時点で最も強い復元結果である。 | 復元済み | `C-TACT-07` |
| `m_MagicTypes` | `+0x78`（配列） | 行動行は、行 `+0x28` 以降に条件／フィルタレーンを格納する。`CheckActionCondition` は、これら 6 本の並列配列をタクティカル条件評価器へ渡す。 | 復元済み | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_NoRecommendSkillIndex` | `C-TACT-05:17393877` `v6->m_NoRecommendSkillIndex = *(_DWORD *)&v24[28];` | これは DB キーではなく、ランタイムコンポーネント側のフィールドである。`SelectType == 6` のとき `TacticalActionList +0x1C` から設定され、その後の候補収集では、この値と等しい `m_SkillIndex` を持つ行動が拒否される。 | 相互参照 | `C-TACT-05`, `C-TACT-06` |
| `m_Params` | `+0x38`（配列） | 行動行は、行 `+0x28` 以降に条件／フィルタレーンを格納する。`CheckActionCondition` は、これら 6 本の並列配列をタクティカル条件評価器へ渡す。 | 復元済み | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_PrioritySkill` | `+0x1C`（スカラー） | `SelectType` が `5` または `6` の場合、行動行は行 `+0x1C` を読み取り、その値を `m_RecommendSkillIndex` または `m_NoRecommendSkillIndex` へ書き込む。この C エクスポートは、この推奨オペランドとは別個の優先度機構までは証明していない。 | 否定的／部分的 | `C-TACT-05`, `C-TACT-06` |
| `m_RecommendSkillIndex` | `C-TACT-05:17393873` `v6->m_RecommendSkillIndex = *(_DWORD *)&v24[28];`<br>`C-TACT-05:17393881` `*(_QWORD *)&v6->m_RecommendSkillIndex = -1;` | これは DB キーではなく、ランタイムコンポーネント側のフィールドである。`SelectType == 5` のとき `TacticalActionList +0x1C` から設定され、その後の候補収集では、この値と等しい `m_SkillIndex` を持つ行動だけが残される。 | 相互参照 | `C-TACT-05`, `C-TACT-06` |
| `m_SelectType` | `+0x18`（バイト） | 行動行は、行 `+0x18` をバイト幅の選択モードとして読み取る。復元された分岐では、`4` はシーケンシャル挙動、`5`/`6` は推奨／非推奨フィルタとして比較される。 | 復元済み | `C-TACT-05`, `C-TACT-07` |
| `m_SkillIndex` | `+0x10`（スカラー） | 行動行は、行 `+0x10` を現在の敵が持つ `m_pSkillIDs` / `TacticalSkillList.m_UseSkills` 配列へのインデックスとして読み取る。直接の `SkillID` ではない。 | 復元済み | `C-TACT-05`, `C-TACT-01` |
| `m_SkillsID` | `C-ENEMY-02:17647330` `cEnemyListBase.m_SkillsID = *(_DWORD *)(v12 + 132);` | `m_SkillsID` は `TacticalActionList` のフィールドではない。`EnemyID +0x84` から読み取られ、`TacticalSkillList` 行を解決し、`m_SkillIndex` によって参照される `m_UseSkills` 配列を供給する。 | 相互参照 | `C-ENEMY-02`, `C-TACT-01`, `C-TACT-05` |
| `m_StatusTypes` | `+0x58`（配列） | 行動行は、行 `+0x28` 以降に条件／フィルタレーンを格納する。`CheckActionCondition` は、これら 6 本の並列配列をタクティカル条件評価器へ渡す。 | 復元済み | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
| `m_TurnCount` | `+0x24`（スカラー） | 順序付き／シーケンシャルのロジックは、行 `+0x24` を追加の周期／順序オペランドとして読み取る。正確なフィールド名は残っていないが、この行レーンはシーケンシャル予兆／行動選択で消費される。 | 復元済み | `C-TACT-07` |
| `m_WeaponTypes` | `+0x68`（配列） | 行動行は、行 `+0x28` 以降に条件／フィルタレーンを格納する。`CheckActionCondition` は、これら 6 本の並列配列をタクティカル条件評価器へ渡す。 | 復元済み | `C-TACT-02`, `C-TACT-03`, `C-TACT-04` |
