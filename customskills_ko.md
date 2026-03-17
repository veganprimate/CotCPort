## **LANGUAGE:**  [English](customskls_en.md) | [日本語](customskills_ja.md) | [中文](customskills_zh.md) | [한국어](customskills_ko.md)

# 스킬 및 관련 시스템을 추가하거나 수정하는 방법

이 페이지는 `Skill/` 폴더와 여기에 직접 데이터를 공급하는 모든 테이블을 다루는, OT0 우선 기준의 리버스 엔지니어링 가이드입니다. 가능한 한 **OT0 데이터베이스와 SDK에서 확인되는 사항**, **『옥토패스 트래블러: 대륙의 패자』 쪽 스키마에서만 확인되는 사항**, **관찰되지만 SDK 덤프에서 완전히 이름 붙여지지 않은 사항**, 그리고 **게임 내에서 입증된 사항**을 구분해 서술합니다.

- **입증됨**은 해당 관계나 기능이 게임 내 테스트를 통해, 여기 적은 의미 그대로임이 입증되었다는 뜻입니다.
- **확인됨**은 내보낸 행 레이아웃, OT0 SDK 구조체/열거형 이름, 그리고/또는 대상 테이블과의 정확한 ID 겹침으로 그 관계가 뒷받침된다는 뜻입니다.
- **관찰됨**은 행 사용 패턴과 이름이 그 의미를 강하게 뒷받침하지만, OT0의 SDK 덤프에는 정확히 이름 붙은 열거형이나 호출자가 드러나지 않는다는 뜻입니다.
- **미해결**은 해당 필드가 실제로 존재하고 일부 콘텐츠에서는 중요할 가능성이 높지만, OT0가 그 동작을 자신 있게 명명할 만큼 직접적인 증거를 제공하지 않았고, 나 또한 게임 내 테스트를 하지 않았거나 아무 변화도 보지 못했다는 뜻입니다.

## OT0와 모바일판 `Skill/` 폴더 한눈에 보기

| 항목 | OT0 | 모바일판 『옥토패스 트래블러: 대륙의 패자』 |
| --- | --- | --- |
| 핵심 행 수 | `SkillID 7330`, `SkillAvailID 7369`, `SkillEffectiveID 4381`, `SkillAilmentType 490` | `SkillID 23085`, `SkillAvailID 28023`, `SkillEffectiveID 11434`, `SkillAilmentType 968` |
| `Skill/` 아래의 OT0 전용 테이블 | `WeaponType`, `SkillCalcType`, `SkillAvailMagnificationList`, `SkillSupplyItemGroup`, `SkillSupplyItemList`, `SkillTargetShopList`, `SkillTargetFarmItemList` | 모바일판 내보내기에는 존재하지 않음 |
| `Skill/` 아래의 모바일판 전용 테이블 | 기본 OT0에는 없음 | `ClassSkillConditionList`, `SkillEffectiveLinkageList`, `SkillFilterCategory`, `SkillFilterType`, `SkillModifyType`, `SpecialSkillExtendCategory`, `SpecialSkillExtendType`, `SpecialSkillExtendList` |
| `SkillEffectiveID` 연출 스키마 | 인라인 `m_Cameras` 및 `m_CameraTimes` 배열을 유지함 | 인라인 카메라 배열을 제거하고, 연계, UI 가시성, 카메라 흔들림, 마석 관련 필드를 추가함 |
| `SkillVisitorList` 바인딩 | `m_EnemyID`를 직접 저장함 | 대신 `m_EnemyType`를 저장함 |
| `SpecialSkillGrowthList` | `m_DisplayPriority`를 포함한 비교적 간결한 성장 행 | 게이지, 보상, avail, 상태 이상, 피해 상한 열을 포함한 훨씬 풍부한 성장 행 |
| `SkillID`의 초점 | 필드 사용 플래그, 직접 보이스 훅, OT0 전용 무기 교체 보조 필드를 유지함 | 필터/카테고리 라우팅, 부스트 표시 변형, 자동 전투 지향 제어 메타데이터를 추가함 |
| `SkillAvailID`의 초점 | 많은 휴면 공유 필드와 OT0 전용 마을/증원/약점 보조 필드를 유지함 | 태그 수, 피해 상한, 추가 약점, 회피 무시, 내성 조작 채널을 적극적으로 사용함 |

- OT0의 `Skill/`는 구조적으로 싱글플레이 본편의 전투 흐름, 전열/후열 메커니즘, 마을 관리 시스템에 더 가깝습니다.
- 모바일판 `Skill/` 내보내기는 추가 필터 UI, 추가 성장 레이어, 추가 피해 상한 로직, 추가 약점 시스템, 그리고 자동화된 표시/제어 경로를 위한 더 많은 메타데이터를 가진 라이브 서비스형 전투 스택에 더 가깝습니다.
- 한 작품에 테이블이 없다고 해서 그 열거형이 코드에서도 사라졌다고 단정하지 마십시오. 런타임 열거형 자체는 남아 있는데, 데이터 테이블을 내보내는 쪽이 한 작품뿐인 경우가 실제로 여러 군데 있습니다.

## 런타임 모델

OT0의 거의 모든 활성 스킬에 대해, 런타임 체인은 다음과 같습니다.

`SkillID` -> `SkillAvailID` + `SkillEffectiveID`

- `SkillID`는 루트 행입니다. 이름/세부 텍스트, 카테고리, 비용, 아이콘, 부스트 계열, 그리고 게임플레이 및 연출 단계 배열을 담습니다.
- `SkillAvailID`는 게임플레이 페이로드입니다. 대상 선택, 피해/회복/버프/디버프/부활/약점 변경/증원/소환 로직, 지속 시간, 횟수, 그리고 2차 메커니즘을 담습니다.
- `SkillEffectiveID`는 연출 페이로드입니다. 전투 액션, 카메라, 이펙트, 사운드, 보이스, 플로팅 텍스트, 가시성 변경을 담습니다. 각 avail 에는 `SkillEffectiveID` 에서 하나의 ID가 할당됩니다.

상태 이상은 네 번째 필수 단계가 아니라 병렬 하위 시스템입니다. 스킬이 버프, 디버프, 상태 이상, 카운터, 인터셉트, 리레이즈, 피해 상한 오라 또는 이와 유사한 상태를 적용하는 경우, 일반적인 경로는 `SkillAvailID`가 하나 이상의 `SkillAilmentType` 행을 지정하는 것입니다.

## 흔한 오해

- **입증됨:** `SkillAvailID.m_ResistID`는 **`Skill/SkillResistList.m_id`가 아닙니다**. OT0에서는 0이 아닌 값이 `Enemy/EnemyWeakID.m_id`와 정확히 일치하며, 그 대상 행 형태야말로 약점 테이블 교체라는 용도에 실제로 들어맞습니다. 구체적인 증거: `SkillAvailID#40085`와 `SkillAvailID#73697`는 모두 `m_ResistID = 6527`을 사용합니다. `EnemyWeakID#6527`는 존재하지만 `SkillResistList#6527`는 존재하지 않습니다.
- **확인됨:** `SkillAvailID.m_SkillAvailMagnificationCondition` 및 `m_SkillAvailMagnificationConditionArray`는 `SkillAvailMagnificationConditionList`를 가리키는 행 ID처럼 동작합니다.
- **확인됨:** OT0의 `SkillAvailID.m_SkillAvailMagnification` 및 `m_SkillAvailMagnificationArray`는 `SkillAvailMagnificationList.m_id`처럼 동작하지 않습니다. OT0은 `110`, `130`, `150`, `180`, `200` 같은 직접 값을 사용하며, 배열 값은 `300`까지 올라갑니다. 구체적인 예: `Surprise Attack`의 `SkillAvailID#74940`는 직접 값 `110/130/150/170/200`을 사용하고, `Ulti-meow`의 `SkillAvailID#76297`는 `300`까지 올라갑니다.
- **확인됨:** `SkillID.m_SkillIconID`는 `Skill/SkillIconList`가 아니라 `Texture/TextureID`를 직접 가리킵니다. `SkillIconList` 자체는 여전히 실제 테이블이지만, 이것은 **별개의** 쌍 아이콘 뱅크입니다.

## 주요 외래 키 스타일 필드에 대한 증명 행렬

- `SkillID.m_AttachEffect` -> `Effect/AttachEffect.m_id`: `10/10` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillID.m_SkillIconID` -> `Texture/TextureID.m_id`: `24/24` 고유한 0이 아닌 OT0 값이 일치합니다.
- `SkillID.m_VoiceID` -> `Sound/PartVoiceID.m_id`: `25/25` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillID.m_WeaponIconType` -> `Skill/WeaponType.m_id`: `9/9` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillID.m_MagicIconType` -> `Skill/MagicType.m_id`: `7/7` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_InvokeCondition` -> `Skill/SkillConditionList.m_id`: `37/37` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_ModifyStatus` -> `Character/CharaStatusID.m_id`: `17/17` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_HitTypes[*]` -> `Skill/SkillHitRateList.m_id`: `26/26` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_ResistID` -> `Enemy/EnemyWeakID.m_id`: `32/32` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_ResistAilmentID` -> `Skill/SkillResistAilmentID.m_id`: `26/26` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_WeakChangeID` -> `Enemy/EnemyWeakChangeID.m_id`: `71/71` 고유한 0이 아닌 OT0 값이 일치합니다.
- `SkillAvailID.m_WeakLockID` -> `Enemy/EnemyWeakLockID.m_id`: `64/64` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_Reinforcement` -> `Enemy/EnemyReinforcements.m_id`: `59/60` 고유한 0이 아닌 OT0 값이 일치합니다. 한 가지 이상치는 `SkillID#900504`의 `SkillAvailID#74111`에서 사용되는 `140` 값입니다.
- `SkillAvailID.m_ModeChangeID` -> `Enemy/EnemyModeID.m_id`: `28/28` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_SummonGuestID` -> `Character/CharaGuest.m_id`: `1/1` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_OverrideWeapon` -> `Skill/WeaponType.m_id`: `8/8` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_OverrideMagic` -> `Skill/MagicType.m_id`: `7/7` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_SkillAvailMagnificationCondition` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `9/9` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAvailID.m_SkillAvailMagnificationConditionArray[*]` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `28/28` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillEffectiveID.m_Cameras[*]` -> `Battle/BattleCameraParams.m_id`: `47/47` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillEffectiveID.m_Animations[*]` -> `Battle/BattleActionID.m_id`: `92/92` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillEffectiveID.m_Effects[*]` -> `Effect/EffectList.m_id`: `712/712` 고유한 0이 아닌 OT0 값이 일치합니다.
- `SkillEffectiveID.m_Sounds[*]` -> `Sound/SoundList.m_id`: `259/259` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillEffectiveID.m_Voices1[*]` 및 `m_Voices2[*]` -> `Sound/PartVoiceID.m_id`: 0이 아닌 모든 OT0 값이 일치합니다.
- `SkillAilmentType.m_ActionID` -> `Battle/BattleActionID.m_id`: `2/2` 0이 아닌 고유한 OT0 값이 일치합니다.
- `SkillAilmentType.m_TargetStatus` 및 `m_AbsorbHealStatus` -> `Character/CharaStatusID.m_id`: 0이 아닌 모든 OT0 값이 일치합니다.
- `SkillAilmentType.m_IconTexID` 및 `m_IconSptTexID` -> `Texture/TextureID.m_id`: 0이 아닌 모든 OT0 값이 일치합니다.
- `SkillAilmentType.m_AddEffect`, `m_CharacterEffect`, `m_FieldEffect`, `m_InvokeEffect` -> `Effect/EffectList.m_id`: 0이 아닌 모든 OT0 값이 일치합니다.
- `SkillAilmentType.m_AddEffectSound`, `m_InvokeSound` -> `Sound/SoundList.m_id`: 0이 아닌 모든 OT0 값이 일치합니다.
- `WeaponType.m_EquipType` -> `Item/ItemType.m_id`: `8/8` 0이 아닌 고유한 OT0 값이 일치합니다.
- `WeaponType.m_AnimationSet` -> `Character/WeaponAnimationSet.m_id`: `8/8` 0이 아닌 고유한 OT0 값이 일치합니다.
- `WeaponType.m_WeakIconTexID`, `WeaponType.m_SkillIconTexID`, `MagicType.m_WeakIconTexID`, `MagicType.m_SkillIconTexID`, `SkillUniqueIconID.m_SkillIconTexID` 및 `SkillIconList` 텍스처 필드는 모두 `Texture/TextureID.m_id`와 완전히 일치합니다.

## 다국어 교차 확인

- OT0은 일본어를 기본 텍스트 뱅크로 저장하고, 현지화된 텍스트는 `GameText/Localize/*` 아래에 둡니다. 예를 들어 `SkillID`의 이름/세부 텍스트는 JP/EN/ZH-CN/KO-KR 전반에서 깔끔하게 해석됩니다. `153236`은 `復活手当` / `Vivify` / `复活处理` / `부활처치`이고, `153376`은 대응되는 부활 설명입니다.
- 상태 이상 UI 텍스트의 경우, `SkillAilmentType`는 주로 `GameTextUI`를 가리키며 `GameTextSkill`은 **아닙니다**. 예: `62327`은 `毒` / `Poison` / `中毒` / `독`이고, `96365`는 상태 이상 설명 문자열입니다.

## `Skill/` 폴더 읽는 방법

- `Skill/` 폴더에는 **런타임 행**(`SkillID`, `SkillAvailID`, `SkillEffectiveID`, `SkillAilmentType`), **조회 범례**(`WeaponType`, `MagicType`, `ResistType`, `SkillCategory`, `SkillCalcType`), 그리고 **헬퍼 페이로드**(`SkillHitRateList`, `SkillResistAilmentID`, `SkillTargetShopList` 등)라는, 성격이 매우 다른 세 종류의 테이블이 섞여 있습니다.
- `...ID`라는 모든 정수 필드를 다른 `Skill/` 테이블에 대한 외래 키로 처리하지 마십시오. OT0은 많은 직접 열거형, 일부 인라인 백분율 배열 및 일부 교차 폴더 행 ID를 사용합니다.
- 공유된 `SkillAvailID`, `SkillEffectiveID` 또는 `SkillAilmentType` 행을 복제하거나 편집하는 경우 OT0이 페이로드 행을 많이 재사용하기 때문에 관련되지 않은 많은 기술을 자동으로 변경할 수 있습니다.

## 이 가이드에서 사용된 정확한 OT0 SDK 행 구조체 인용문

이 섹션은 OT0 SDK를 `SDK/Kingship_structs.hpp`에서 직접 인용하므로, 가이드가 의역에 머무르지 않습니다. 정확한 row-struct 정의는 길고, 독자들이 여러 테이블 스키마를 나란히 비교하고 싶어 하는 경우가 많기 때문에 여기에 모아 둡니다.

### 다음 `Skill/` 섹션에서 사용되는 정확한 OT0 행 구조 인용문

#### `FMagicTypeBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14381`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:9814`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:8760`에서 인용되었습니다.
```cpp
struct FResistTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DamageRate;                                      // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillCategoryBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16239`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12824`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17289`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17406`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14869`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17455`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14674`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:14518`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17264`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12898`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17569`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17517`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16382`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:13602`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17605`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12910`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:12926`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17641`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17691`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:16345`에서 인용되었습니다.
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
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:17824`에서 인용되었습니다.
```cpp
struct FSpecialSkillIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSpecialSkillGrowthListBase`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:13722`에서 인용되었습니다.
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

## 완전한 `Skill/` 파일 참조

아래의 각 테이블 섹션에는 이제 의존하는 정확한 OT0 SDK 열거형 인용문이 포함되어 있습니다. 바로 위의 중앙 집중식 섹션은 스키마 수준 교차 검사를 위해 정확한 OT0 행 구조 인용을 유지합니다.

### `MagicType`
요소 범례 테이블. OT0는 기술이나 상태 이상에 아이콘이나 재정의 동작을 위한 요소 계열이 필요한 모든 곳에서 이를 사용합니다.

- **OT0 행:** `9`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `8`.
- **OT0 SDK 행 구조:** `FMagicTypeBase`.
- **관련 OT0 SDK 열거형:** `EMAGIC_TYPE`.
- `m_id`는 `SkillID.m_MagicIconType` 및 `SkillAvailID.m_OverrideMagic`에 나타나는 동일한 요소 키 패밀리입니다.

#### 이 테이블에서 사용된 정확한 OT0 SDK 열거형 인용문
##### `EMAGIC_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:4295`에서 인용되었습니다.
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
- `MagicType.m_id`는 임의의 행 번호가 아닙니다. OT0은 내보낸 JSON 행 ID `0..8`가 위의 엔진 열거형에 의해 저장된 동일한 원시 값이 되도록 이 테이블에 키를 지정합니다.
- 이것이 바로 `SkillID.m_MagicIconType`, `SkillAvailID.m_OverrideMagic` 및 기타 요소 계열 필드를 변환 레이어 없이 직접 SDK 열거형 값과 `MagicType` 행 ID로 읽을 수 있는 이유입니다.

#### 필드별
- `m_id`: **확인됨.** 직접 요소 계열 키입니다. OT0 행 `0..8`는 `EMAGIC_TYPE`(`NONE`, `FIRE`, `ICE`, `THUNDER`, `WIND`, `LIGHT`, `DARK`, `NAUGHT`, `POISON`)에 정렬됩니다.
- `m_TextID`: **확인됨.** `GameTextUI`의 플레이어 쪽 요소 이름입니다. 예: OT0 `1 -> Fire`, `7 -> Non-Elemental`, `8 -> Poison`.
- `m_WeakIconTexID`: **확인됨.** 이 요소가 약점 또는 저항 계열로 나타날 때 사용되는 약점 패널 아이콘에 대한 `Texture/TextureID.m_id`입니다.
- `m_SkillIconTexID`: **확인되었습니다.** 이 요소 계열의 명령/스킬 오버레이 아이콘에 대한 `Texture/TextureID.m_id`입니다.

#### 대륙의 패자 전용 추가 필드
- `m_AdditionWeakIconTexID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 『옥토패스 트래블러: 대륙의 패자』의 추가 약점 시스템에서 사용되는 추가 약점 아이콘 텍스처입니다. OT0에는 해당 필드가 없습니다.

### `WeaponType`
무기 계열 전설 테이블. 이는 OT0 무기 라벨, 무기 아이콘, 해당 장비 유형 행 및 전투 애니메이션 계열에 대한 표준 위치입니다.

- **OT0 행:** `10`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** `FWeaponTypeBase`.
- **관련 OT0 SDK 열거형:** `EWEAPON_TYPE`.
- `WeaponType`는 단순한 화장품이 아닙니다. OT0 스킬 행, 아이템 행, 애니메이션 행이 모두 여기에 수렴됩니다.

#### 이 테이블에서 사용된 정확한 OT0 SDK 열거형 인용문
##### `EWEAPON_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:7327`에서 인용되었습니다.
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
- `WeaponType.m_id`는 마찬가지로 위의 원시 SDK 열거형에 맞춰져 있습니다. 내보낸 행은 엔진 무기 계열 상수의 데이터 테이블 미러입니다.
- 이것이 `SkillID.m_WeaponIconType`, `SkillAvailID.m_OverrideWeapon`, 약점 그리드 및 무기 애니메이션 라우팅이 모두 두 번째 리매핑 테이블 없이 동일한 숫자 계열을 공유할 수 있는 이유입니다.

#### 필드별
- `m_id`: **확인됨.** 직접 무기 계열 키입니다. OT0 행은 `EWEAPON_TYPE`(`NONE`, `SWORD`, `LANCE`, `DAGGER`, `AXE`, `BOW`, `ROD`, `PAPERS`, `FAN`, `NAUGHT`)에 정렬됩니다.
- `m_TextID`: **확인됨.** `GameTextUI`의 플레이어 쪽 무기 제품군 이름입니다. 예: `1 -> Sword`, `2 -> Polearm`, `7 -> Tome`, `8 -> Fan`.
- `m_WeakIconTexID`: **확인됨** 해당 무기 계열의 약점 패널 아이콘은 `Texture/TextureID.m_id`입니다.
- `m_SkillIconTexID`: **확인되었습니다.** 스킬 UI에 사용되는 무기 오버레이 아이콘은 `Texture/TextureID.m_id`입니다.
- `m_EquipType`: **확인됨** 일치하는 장착 가능한 무기 계열 아이템 유형에 대한 `Item/ItemType.m_id`입니다. 이것이 OT0 행이 무기 열거형의 두 번째 복사본 대신 `1`, `7`, `3`, `2`, `5`, `4`, `6`, `8`와 같은 항목 유형 ID를 사용하는 이유입니다.
- `m_AnimationSet`: **확인됨** `Character/WeaponAnimationSet.m_id`. 이는 스킬이 해당 무기 유형으로 렌더링될 때 전투 동작이 사용하는 애니메이션 계열입니다.

### `ResistType`
OT0 약점/저항률 범례. `EnemyWeakID`와 같은 약점 그리드 스타일 테이블에서 사용되는 백분율 클래스 테이블입니다.

- **OT0 행:** `10`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** `FResistTypeBase`.
- 이 테이블은 `SkillAvailID.m_ResistID`를 수정해야 했던 이유입니다. `EnemyWeakID`는 슬롯당 `ResistType` ID를 저장합니다. 대신 `SkillResistList`는 직접 백분율 델타를 저장합니다.

#### 필드별
- `m_id`: **확인됨.** `Enemy/EnemyWeakID.m_ResistWeapon` 및 `m_ResistMagic` 내부에 취약점 등급 키가 사용됩니다.
- `m_DamageRate`: **확인됨.** 해당 약점 등급에 대한 실제 피해율 비율입니다. OT0 행은 `100` 일반, `130` 약점, `50` 저항, `0` null, `-100` 흡수 및 중간 상태 `90`, `85`, `80`, `75` 및 `1`로 디코딩됩니다.

### `SkillCategory`
카테고리 범례 테이블. 실제 카테고리 번호는 `SkillID.m_Category`에 있습니다. 이 테이블은 런타임이 쿼리할 수 있는 범주 수준 부울 하나를 추가합니다.

- **OT0 행:** `9`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `9`.
- **OT0 SDK 행 구조:** `FSkillCategoryBase`.
- **관련 OT0 SDK 열거형:** `ESKILL_CATEGORY`.

#### 이 테이블에서 사용된 정확한 OT0 SDK 열거형 인용문
##### `ESKILL_CATEGORY`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6344`에서 인용되었습니다.
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
- `SkillID.m_Category`는 위 SDK 열거형의 원시 `ESKILL_CATEGORY` 번호를 저장합니다. `SkillCategory.json`는 OT0이 해당 열거형 위에 추가로 범주별 데이터 플래그(`m_BuffDebuffLimit`)를 추가하기를 원하기 때문에 존재합니다.
- 즉, `SkillCategory`는 관련 없는 외부 테이블이 아닙니다. 위에 인용된 정확한 엔진 카테고리 상수에 대한 데이터 기반 범례입니다.

#### 필드별
- `m_id`: **확인됨.** 직접 카테고리 키입니다. OT0 행은 `ESKILL_CATEGORY`(`COMMAND`, `SUPPORT`, `ENEMYACTION`, `ITEM` 등)에 맞춰 정렬됩니다.
- `m_BuffDebuffLimit`: **관찰됨.** 버프/디버프 상한 동작에 대한 카테고리 수준 부울입니다. OT0에서는 카테고리 ID `1`, `3` 및 `4`에 대해 true이고 다른 곳에서는 false이므로 모든 카테고리가 동일한 제한 규칙에 참여한다고 가정하지 마십시오.

### `SkillCalcType`
순수 열거형 범례 테이블입니다. OT0은 `m_id`만으로 `Skill/SkillCalcType.json`를 유지하며, 이는 `SkillAvailID.m_CalcType`에서 사용하는 계산 모드 열거형을 정확하게 반영합니다.

- **OT0 행:** `10`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** 이 저장소에 제공된 OT0 SDK 덤프에서 일치하는 전용 행 구조를 찾을 수 없습니다.
- **관련 OT0 SDK 열거형:** `ESKILL_CALC_TYPE`.
- 여기의 OT0 SDK 덤프에서는 전용 `FSkillCalcTypeBase` 행 구조체가 발견되지 않았지만 OT0 JSON은 여전히 ​​깨끗한 범례 테이블입니다.

#### 이 테이블에서 사용된 정확한 OT0 SDK 열거형 인용문
##### `ESKILL_CALC_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6327`에서 인용되었습니다.
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
- `SkillAvailID.m_CalcType`는 이러한 원시 숫자를 직접 저장합니다. 따라서 `SkillCalcType.json`는 독립적인 게임플레이 테이블이 아닌 SDK 열거형의 내보낸 범례 복사본으로 읽는 것이 가장 좋습니다.
- 이는 또한 테이블에 `m_id`만 있는 이유를 설명합니다. 의미 체계 이름은 SDK 열거형에 있는 반면 JSON은 데이터 내보내기에 숫자 도메인이 표시되도록 유지합니다.

#### 필드별
- `m_id`: **확인됨.** 계산 모드 키 일치 `ESKILL_CALC_TYPE` (`NONE`, `WEAPON`, `MAGIC`, `ADD_RATED`, `ADD_FIXED`, `ADD_NOW_RATED`, `LAST_1`, `FEAR_LAST_1`, `BP_SPECIAL_REGEN_RATE`, `MAGIC_REFLECTION`).

### `SkillID`
루트 스킬 행. 스킬을 복제할 때 하나의 테이블만 변경하는 경우 이 테이블을 먼저 검사하지만 편집해야 하는 유일한 테이블은 거의 없습니다.

- **OT0 행:** `7330`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `23085`.
- **OT0 SDK 행 구조:** `FSkillIDBase`.
- **관련 OT0 SDK 열거형:** `ESKILL_CATEGORY`, `ESKILL_RECOMEND_CATEGORY`.
- OT0 사용량은 적측 행에 크게 치우쳐 있습니다. `ENEMYACTION` 카테고리만 대부분의 행을 차지합니다. 플레이어 콘텐츠를 만들 때 임의의 `SkillID` 행을 선택하는 대신 올바른 플레이어 쪽 제품군에서 행을 복제하세요.

#### 이 행에서 직접 사용된 정확한 OT0 SDK 열거형 인용문
##### `ESKILL_RECOMEND_CATEGORY`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6655`에서 인용되었습니다.
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
- `SkillID.m_Category`는 이전 `SkillCategory` 섹션에 포함된 정확한 `ESKILL_CATEGORY` 인용문과 함께 읽어야 합니다. 기술 행은 해당 원시 열거형 값을 저장하고 `SkillCategory.json`는 동반 범례 테이블이기 때문입니다.
- `SkillID.m_RecommendCategory`에는 병렬 `Skill/` 범례 파일이 **없습니다**. 따라서 위의 정확한 SDK 열거형은 `ATTACK`, `SUPPORT` 및 `HEAL`와 같은 값에 대한 신뢰할 수 있는 명명 소스입니다.

#### 필드별
- `m_id`: 스킬 행의 기본 키입니다.
- `m_Name`: **확인되었습니다.** 스킬명 텍스트 ID입니다. 대부분의 OT0 행은 `GameText/Localize/*/SystemText/GameTextSkill.json`를 통해 확인되지만 더 작은 하위 집합은 대신 `GameTextUI`를 통해 확인됩니다. 보이는 이름을 확인할 때 현지화된 텍스트 뱅크를 사용하세요.
- `m_Detail`: **확인되었습니다.** 스킬 상세/설명 텍스트 ID입니다. OT0는 `GameTextUI`가 아닌 `GameTextSkill`를 통해 이를 압도적으로 해결합니다.
- `m_Job`: **확인되었습니다.** `Character/JobID.m_id` 설정 시. OT0은 여기서 8개의 기본 작업 ID만 사용하고 `44` 행에만 사용하므로 이는 범용 소유자 작업 필드가 **아닙니다**.
- `m_BoostSkills`: **확인됨.** 부스트 제품군 `[boost0, boost1, boost2, boost3]`에 대한 4개의 `SkillID` 행의 고정 길이 배열입니다. OT0에서는 일반적으로 부스트 가능한 제품군의 선두 행만 채웁니다.
- `m_Category`: **확인되었습니다.** `ESKILL_CATEGORY`의 스킬 카테고리입니다. 이는 플레이어 명령, 지원 기술, 적의 행동, 적의 패시브 및 아이템 기술을 구분하기 위해 검사해야 하는 첫 번째 필드입니다.
- `m_RequireRatio`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** SDK 구조에 있지만 OT0 기술 행에서는 항상 0/false입니다.
- `m_RequireValue`: **관찰됨.** 비용/가치 게이트. 일반 플레이어 명령에서는 표시된 SP 비용과 일치합니다. 비지휘 기술의 경우 의미는 카테고리와 발신자에 따라 다릅니다.
- `m_CountLimit`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 존재하지만 항상 0입니다.
- `m_UnableRecoverCount`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 존재하지만 항상 0/false입니다.
- `m_RequireBoost`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 존재하지만 항상 0입니다.
- `m_WeaponIconType`: **확인됨** 스킬과 함께 표시되는 무기 아이콘 오버레이에 대해 `WeaponType.m_id`를 직접 지정하세요.
- `m_MagicIconType`: **확인되었습니다.** 스킬과 함께 표시되는 요소 아이콘 오버레이에 대해 `MagicType.m_id`를 직접 지정하세요.
- `m_IsRandomReplace`: **관찰됨.** 무작위 변형 교체 플래그. OT0은 `Prism Mist` 스타일 기술과 같은 소수의 무작위 대체 제품군에 이를 사용합니다.
- `m_IsWeaponSelect`: **관찰됨.** 무기 형태 선택 플래그. 하나의 표시 명령이 무기 선택 또는 무기 컨텍스트 라우팅에 따라 서로 다른 구체적인 기술 행을 확인할 수 있을 때 사용됩니다.
- `m_IsReplaceNowSelectedWeapon`: **관찰됨.** 교체는 고정 계열 구성원이 아닌 현재 선택된 무기 형태를 사용해야 합니다.
- `m_IsReplaceMainWeapon`: **관찰됨.** 이전 필드와 유사하지만 주 무기 세트에 편향되어 있습니다. OT0에서는 드물다.
- `m_WeaponReplaceSkill`: **관찰됨.** 8개의 대체 `SkillID` 행으로 구성된 고정 길이 풀입니다. 이름에도 불구하고 OT0는 진정한 무기 형태 교체와 일부 무작위 변형 제품군 모두에 이를 사용합니다.
- `m_Avails`: **확인됨.** 최대 20개의 `SkillAvailID` 스테이지로 구성된 고정 길이 배열입니다. 스킬의 게임 플레이 순서입니다. 0개 슬롯은 간단히 건너뜁니다.
- `m_BeginEffective`: **확인됨.** 프리캐스트 `SkillEffectiveID` 스테이지 옵션.
- `m_Effectives`: **확인됨.** 최대 20개의 `SkillEffectiveID` 스테이지로 구성된 고정 길이 배열입니다. 스킬 발표 순서입니다.
- `m_EndEffective`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 포스트캐스트 `SkillEffectiveID` 후크가 구조체에 있지만 OT0 행에서는 사용되지 않았습니다.
- `m_AttachEffect`: **확인되었습니다.** `Effect/AttachEffect.m_id`. OT0은 소수의 행에만 이를 사용합니다.
- `m_SkillIconID`: **확인되었습니다.** 직접 `Texture/TextureID.m_id`입니다. 이는 OT0의 주요 플레이어 방향 명령 아이콘 필드이며 `SkillIconList` 행으로 다시 지정해서는 **안 되는** 이유입니다.
- `m_SkillUniqueIconID`: **확인되었습니다.** `Skill/SkillUniqueIconID.m_id`. 스키마에 존재하지만 OT0 `SkillID` 행에서 사용되지 않습니다.
- `m_CommandPhaseSkillIconID`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** 명령 단계 시스템용으로 예약된 대체 아이콘입니다.
- `m_OverrideCommandName`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** 명령 이름 지정을 위해 예약된 대체 텍스트 ID입니다.
- `m_CommandCategory`: **관찰됨.** 희귀한 UI/카테고리 도우미. 5개의 OT0 행만 이를 사용하며 모두 동일한 값 `62274`를 갖습니다. 의존하기 전에 이미 사용하고 있는 행을 복제하세요.
- `m_BlockedSkill`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 상호 배타적/차단 스킬 후크가 예약되어 있습니다.
- `m_ReplaceCondition`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 단일 교체 조건 후크가 예약되어 있습니다.
- `m_ReplaceConditionArray`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 예약된 교체 조건 배열입니다.
- `m_ReplaceSkill`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 예약된 교체 스킬 후크입니다.
- `m_ReplaceSkillArray`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 예약된 교체 스킬 배열입니다.
- `m_DisableReplaceTag`: **관찰됨.** 일부 교체 태그 지정 논리 또는 교체 관련 UI 레이블 지정을 억제합니다. OT0은 어디에서나 거짓으로 둡니다.
- `m_SpecialSkillInitialGaugeValue`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 특수 스킬 스타일 시스템을 위한 게이지 시드 필드입니다.
- `m_SpecialSkillRegenerateValue`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** 특수 스킬 스타일 시스템의 게이지 재생 필드입니다.
- `m_RecommendCategory`: **확인됨.** `ESKILL_RECOMEND_CATEGORY`(`ATTACK`, `SUPPORT`, `HEAL`)의 권장 버킷입니다. OT0은 행의 작은 하위 집합에서만 이를 사용합니다.
- `m_DuplicateAilment`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 스킬 행 레벨에서 예약된 상태 이상 중복 플래그입니다.
- `m_AfterAction`: **관찰됨.** 드문 후속 조치 코드입니다. OT0는 5개 행에만 이를 사용합니다. 해당 행에는 강제 조치 후 동작과 일치하는 단계적 Avail/유효 데이터도 있습니다.
- `m_InitialChargeTrun`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 자산에 의해 오타가 보존되었습니다. 다른 시스템용으로 예약된 충전 회전 매개변수입니다.
- `m_ReChargeTrun`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 다른 시스템용으로 예약된 재충전 회전 매개변수입니다.
- `m_MaxBoostLv`: **확인됨.** 최대 부스트 레벨. OT0은 모든 `SkillID` 행에서 이를 `3`로 설정합니다.
- `m_FieldUseable`: **확인됨.** 현장 상황에서 해당 스킬을 사용할 수 있는 것으로 표시합니다. OT0에서는 필드 소생 스킬 등 20개 행에만 설정합니다.
- `m_VoiceID`: **확인되었습니다.** 스킬 음성 호출을 위해 `Sound/PartVoiceID.m_id`를 직접 입력하세요.

#### 구체적인 OT0 예
- `m_FieldUseable`: `SkillID#607665 First Aid`, `#900556 Healing Light`, `#902332 Vivify` 및 `#902114 Revive`는 모두 이 플래그를 설정합니다. 원본 OT0에서 이는 일반적인 "어디서나 작동" 표시라기보다는 압도적으로 가족 치유/부활 신호입니다.
- `m_AfterAction`: `SkillID#900012 Retreating Strike`, `#900042 Lunar Arc`, `#900100 Expert First Aid` 및 `#900563/#901483 Song of Battle`는 모두 `m_AfterAction = 1`로 설정됩니다. 눈에 보이는 설명도 명시적으로 "다음 행으로 이동"으로 끝납니다. 이는 해당 필드가 강제된 조치 후 행 이동과 연결되어 있다는 강력한 증거입니다.
- `m_RecommendCategory`: `SkillID#902332 Vivify`, `#901894 Arcane Healing`, `#902108 Heal Wounds` 및 `#902114 Revive`와 같은 복원 행은 `ESKILL_RECOMEND_CATEGORY::HEAL`와 정확히 일치하는 `m_RecommendCategory = 3`를 사용합니다.

#### 대륙의 패자 전용 추가 필드
- `m_DispBoostLv`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 부스트 레벨을 명시적으로 표시하기 위한 UI 플래그입니다.
- `m_IsBasicAbility`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨** 행을 학습/특수 능력이 아닌 기본 능력으로 표시합니다.
- `m_NoChangeBoostType`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 교체 또는 변환 논리가 스킬의 부스트 계열/유형을 변경하는 것을 방지합니다.
- `m_Boost246`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 일반적인 OT0 스타일 `0/1/2/3` 제품군 프레젠테이션을 사용하지 않는 기술에 대한 대륙의 패자 관련 부스트 패턴 플래그입니다.
- `m_ForcedSkillFilter`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마와 ID 중복에서 확인되었습니다.** 특정 필터 버킷의 기술을 강제로 포함하는 `SkillFilterType.m_id` 행의 배열입니다.
- `m_ExclusionSkillFilter`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마와 ID 중복에서 확인되었습니다.** 특정 필터 버킷에서 스킬을 강제로 제외하는 `SkillFilterType.m_id` 행 배열입니다.
- `m_ForcedCategory`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마와 ID가 중복되는 것으로 확인되었습니다.** `SkillFilterCategory.m_id`는 『옥토패스 트래블러: 대륙의 패자』 쪽 필터 UI에 대한 스킬을 강제 적용합니다.
- `m_NotUsedAutoBattle`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 자동 전투 제외 플래그.
- `m_CmdPhaseTurnResetOnContinue`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 스킬이 다른 단계 또는 턴으로 계속되면 명령 단계 상태가 재설정됩니다.

#### OT0 사용 참고사항
- `Row-count bias`: OT0에는 `7330` `SkillID` 행이 있지만 `1614`만 범주 `COMMAND`입니다. 적 콘텐츠가 파일을 지배합니다.
- `Array sizes`: OT0은 고정 크기를 사용합니다: `m_BoostSkills` 길이 `4`, `m_Avails` 길이 `20`, `m_Effectives` 길이 `20`, `m_WeaponReplaceSkill` 길이 `8`.
- `True rarity`: OT0은 `5` 행에 `m_IsRandomReplace`, `23`에 `m_IsWeaponSelect`, `4`에 `m_IsReplaceMainWeapon`, `5`에 `m_AfterAction`, `m_FieldUseable`에만 설정합니다. `20`.

### `SkillAvailID`
게임플레이 페이로드 행. `SkillID`가 *이것이 어떤 스킬 계열인지* 알려주면, `SkillAvailID`는 *전투 또는 필드 로직에서 스테이지가 실제로 수행하는 작업*을 알려줍니다.

- **OT0 행:** `7369`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `28023`.
- **OT0 SDK 행 구조:** `FSkillAvailIDBase`.
- **관련 OT0 SDK 열거형:** `Kingship::ESKILL_INVOKE`, `ESKILL_TARGET_TYPE`, `ESKILL_MODIFY_TYPE`, `ESKILL_CALC_TYPE`, `ESKILL_AILMENT_CALC_TYPE`.
- OT0 리버스 엔지니어링의 경우 `m_ModifyType`는 전체 행에서 가장 중요한 단일 필드입니다. 대부분의 숫자 페이로드 배열을 읽어야 하는 방법을 결정합니다.

#### 이 행을 직접 구동하는 정확한 OT0 SDK 열거형 인용문
##### `Kingship::ESKILL_INVOKE`(컨텍스트 열거형)
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6360`에서 인용되었습니다.
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
- SDK 덤프는 인쇄된 이름이 동일한 두 개의 서로 다른 `Kingship::ESKILL_INVOKE` 열거형 블록을 노출합니다. 위의 컨텍스트 열거형은 더 넓은 패시브/전투/필드 호출 도메인을 설명합니다.

##### `Kingship::ESKILL_INVOKE`(타이밍 열거형)
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6436`에서 인용되었습니다.
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
- `SkillAvailID.m_InvokeTiming`는 이전 컨텍스트 열거형이 아닌 위의 타이밍 열거형을 사용합니다. 이것이 바로 라이브 OT0 행에 `COMMAND`, `ACTION_DAMAGE`, `FIELD_ACTION` 및 `ALWAYS`와 같은 값이 포함되는 이유입니다.

##### `ESKILL_TARGET_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6683`에서 인용되었습니다.
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
- `SkillAvailID.m_TargetType`는 이 열거형의 원시 숫자 선택기를 저장합니다. 이것이 바로 필드가 동일한 정수 열에 일반 플레이어 대상(`ENEMY_1`, `FRIENDLY_ALL_DEAD`)과 AI/헬퍼 선택기(`AI_ENEMY_MIN_HP`, `FORWARDS_OR_BACKUPS_ALIVE`)를 모두 인코딩할 수 있는 이유입니다.

##### `ESKILL_MODIFY_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6476`에서 인용되었습니다.
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
- `SkillAvailID.m_ModifyType`는 이 열거형의 원시 숫자 opcode를 저장합니다. 이것이 바로 이 단일 필드가 `m_Values`, `m_Turns`, `m_Counts`, `m_ModifyStatus`, 강화 ID, 약점 변경 ID, 상점/팜 도우미 행 및 기타 여러 페이로드의 해석을 지배하는 이유입니다.
- `SkillAvailID.m_CalcType`는 위에 `SkillCalcType`에 포함된 정확한 `ESKILL_CALC_TYPE` 인용문과 함께 읽어야 합니다.
- `SkillAvailID.m_CalcTypeAilment`는 아래 `SkillAilmentType`에 포함된 정확한 `ESKILL_AILMENT_CALC_TYPE` 인용문과 함께 읽어야 합니다. OT0 값 사용법은 해당 상태 이상 측 열거형 계열과 일치합니다.

#### 필드별
- `m_id`: 제공 목록 행의 기본 키입니다.
- `m_InvokeTiming`: **확인됨.** OT0 SDK 덤프에 있는 더 풍부한 `Kingship::ESKILL_INVOKE` 블록의 타이밍 열거형입니다. OT0은 `COMMAND`, `BATTLING`, `ACTION_DAMAGE`, `FIELD_ACTION` 및 `ALWAYS`와 같은 값을 사용합니다.
- `m_InvokeCondition`: **확인되었습니다.** `Skill/SkillConditionList.m_id`. 이 조건 패키지가 통과하는 경우에만 단계가 해결됩니다.
- `m_TargetType`: **확인되었습니다.** `ESKILL_TARGET_TYPE`의 대상 선택기입니다. 여기에서 스테이지에서 적 한 명, 모든 적, 자신, 죽은 아군 한 명, 모두, 앞줄, 백업 줄, AI가 선택한 최저 HP 대상 등을 요구할지 여부를 결정합니다.
- `m_ModifyType`: **확인되었습니다.** `ESKILL_MODIFY_TYPE`의 주요 작업 코드입니다. `CHARACTER`는 범용 능력치/피해/치유/버프/디버프 경로이며, 다른 값은 부활, 약점 변경, 강화, 도용, 마을 경제 작업 등을 수행합니다.
- `m_ModifyStatus`: **확인됨.** 수정 opcode에 구체적인 통계 또는 리소스 선택기가 필요한 경우 `Character/CharaStatusID.m_id`입니다. OT0은 여기서 17개의 고유 상태 ID만 사용하며 모두 `CharaStatusID`와 완전히 일치합니다.
- `m_AvailTag`: **관찰됨.** 7슬롯 내부 분류 태그 배열. OT0은 많은 반복 튜플을 사용하지만 OT0 SDK 덤프는 이에 대한 깨끗한 열거형을 노출하지 않습니다. 분명히 의존하는 기존 제품군을 복제하지 않는 한 내부 라우팅/태깅 필드로 처리하십시오.
- `m_IsFoodAvail`: **관찰됨.** 음식/마을측 이용 가능 동작에 대한 드문 OT0 플래그입니다.
- `m_HitTypes`: **확인됨.** 최대 4개의 `SkillHitRateList.m_id` 행 배열. 이는 다중 적중 횟수 분포 및 관련 무작위 적중 논리를 제어합니다.
- `m_RandomChoiceCount`: **관찰됨.** 여러 결과에서 선택하는 opcode에 대한 무작위 선택/분기 수입니다. OT0에서는 드물다.
- `m_CalcType`: **확인됨.** `ESKILL_CALC_TYPE`의 계산 모드입니다. OT0에서 공통 값은 `NONE`, `WEAPON`, `MAGIC`, `ADD_FIXED` 및 `ADD_RATED`입니다.
- `m_Values`: **확인됨.** 주요 숫자 페이로드 배열입니다. 해석은 `m_ModifyType`와 `m_CalcType`(데미지 파워, 치유량, 상태 델타, 보상량 또는 기타 opcode별 값)에 따라 달라집니다.
- `m_FluctuationValues`: **관찰됨.** 2차 분산 페이로드. OT0은 세 행에 0이 아닌 값만 사용하므로 대부분의 기술은 이를 무시합니다.
- `m_SkillRatios`: **관찰되었지만 매우 강력하게 지원됩니다.** 부스트당 비율 배열. 일반적인 OT0 기본값은 `(100,100,100,100)`인 반면, 많은 강화 가능한 공격 제품군은 `(100,180,260,340)` 또는 `(100,200,300,400)`와 같은 프로그레시브 세트를 사용합니다.
- `m_Turns`: **관찰됨.** 부스트당 기간 배열. OT0는 시간 제한 버프, 디버프, 카운터, 인터셉트 및 기타 상태 저장 효과에 이를 사용합니다.
- `m_OverwriteCount`: **관찰됨.** 스테이지를 다시 적용할 때 개수 기반 효과에 사용되는 명시적인 덮어쓰기 개수입니다.
- `m_Counts`: **관찰됨.** 부스트당 카운트 또는 스톡 배열. 제한된 반응, 반복 횟수 또는 유사한 스테이지 로컬 카운터와 같은 횟수 기반 메커니즘에 사용됩니다.
- `m_OrderPriority`: **관찰됨.** 희귀한 전투 명령 무시. OT0은 한 행에서만 0이 아닌 값을 사용합니다.
- `m_NextPriority`: **관찰됨.** 드물게 다음 작업 우선순위 조정. OT0에서는 7개 행에만 사용합니다.
- `m_TargetNextPriority`: **관찰됨.** 대상측 다음 작업 우선순위 설정. OT0은 주로 센티널 값 `-100`를 사용합니다. `1`, `2`, `30` 또는 `31`와 같은 값으로 설정하면 향후 턴 순서 동작이 변경됩니다.
- `m_ShieldDamage`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 항상 0입니다.
- `m_IgnoreWeak`: **관찰됨.** 이 단계에서는 약점 상호 작용 또는 약점 요구 사항을 무시합니다. OT0은 73개 행에만 이를 사용합니다.
- `m_NoBreakable`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 항상 0입니다.
- `m_PhysicalHitRatio`: **관찰됨.** 드물게 물리 적중률 오버라이드. OT0은 두 행에만 사용합니다.
- `m_CriticalRatio`: **관찰됨.** 이 단계의 임계율 조정. OT0는 수백 개의 공격 행에 이를 사용합니다.
- `m_Suicide`: **관찰됨.** 무대를 자기희생 또는 자살로 표시합니다.
- `m_LotteryAilment`: **관찰됨.** 무작위 상태 이상 선택 도우미. OT0에서는 드물며 수정 경로가 확률적으로 상태 이상 패키지를 선택하는 경우에만 유용합니다.
- `m_AilmentInvokeCondition`: **관찰됨.** 특히 스테이지의 상태 이상 부분을 위한 추가 조건 게이트입니다. OT0은 이를 한 행에 사용합니다.
- `m_AddAilmentPick`: **관찰됨.** `m_AddAilment`에서 선택할 수 있는 희귀한 선택기 플래그입니다. OT0은 4개의 행에만 사용합니다.
- `m_AddAilment`: **확인됨.** 추가/적용할 최대 16개의 `SkillAilmentType.m_id` 행의 인라인 목록입니다.
- `m_CalcTypeAilment`: **관찰됨.** 상태 이상 계산 모드의 병렬 목록. 가장 가능성이 높은 열거형 계열은 OT0 SDK가 노출하는 `ESKILL_AILMENT_CALC_TYPE`입니다.
- `m_ValueAilment`: **관찰됨.** `m_AddAilment`에 해당하는 상태 이상 효능/재정의 값의 병렬 목록입니다.
- `m_NotExtensionTarget`: **관찰됨.** 일반적인 기간 연장 동작에서 대상 상태를 제외합니다. OT0은 `557` 행에서 이를 사용하므로 일회성 디버그 플래그가 아닙니다.
- `m_WeakLockID`: **확인되었습니다.** `Enemy/EnemyWeakLockID.m_id`. 스테이지에서 특정 약점 슬롯을 잠글 때 사용하세요.
- `m_DelayedSkill`: **관찰됨.** 즉시 해결되지 않고 나중에 해결되는 후속 `SkillID.m_id`입니다.
- `m_DelaySkillPriority`: **관찰됨.** 지연된 스킬 단계에 대한 우선 순위 버킷/타이밍 도우미입니다.
- `m_SubAilmentPick`: **관찰됨.** `m_SubAilment`에 대한 희귀 선택기 플래그입니다.
- `m_SubAilment`: **확인됨.** 최대 16개의 `SkillAilmentType.m_id` 행으로 구성된 보조 목록입니다. OT0는 이를 조건부 또는 메인 스테이지에 계층화된 상태 이상 패키지 지원에 사용합니다.
- `m_ResistID`: **확인됨** `Enemy/EnemyWeakID.m_id`, **`SkillResistList` 아님**. `EnemyWeakID`는 무기별/요소별 `ResistType` 클래스를 저장하기 때문에 이 필드는 전체 약점 테이블 행을 교체하거나 주입합니다. 이것이 바로 값 세트가 `SkillResistList`가 아닌 `EnemyWeakID`와 일치하는 이유입니다.
- `m_ResistAilment`: **관찰됨.** 기본 상태 이상군에 대한 인라인 상태 이상 저항률 배열. OT0에서는 `m_ResistAilmentID`와 공존할 수 있으므로 하나가 다른 하나를 비활성화한다고 가정하지 마십시오.
- `m_ResistAilmentID`: **확인되었습니다.** `Skill/SkillResistAilmentID.m_id`. 이는 명시적인 상태 이상 ID와 일치율을 저장하는 상태 이상 저항의 패키지 형태입니다.
- `m_Reinforcement`: **확인됨** `Enemy/EnemyReinforcements.m_id`는 지원군을 소환하는 스테이지용입니다. OT0에는 하나의 이상값 `140`가 있으므로 희귀한 보스 동작을 복제하기 전에 항상 대상 행을 확인하세요.
- `m_WeakChangeID`: **확인되었습니다.** 단계적 약점 테이블 수정을 위한 `Enemy/EnemyWeakChangeID.m_id`입니다.
- `m_EnhancedSkillID`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 항상 0입니다.
- `m_SummonGuestID`: **확인되었습니다.** 손님 소환 동작에 대한 `Character/CharaGuest.m_id`입니다.
- `m_OverrideWeapon`: **확인되었습니다.** `WeaponType.m_id`를 직접 재정의합니다. 이는 스킬 단계에서 "소스 명령이 다른 것일지라도 히트 또는 아이콘에 이 무기 계열을 사용하십시오"라고 말하는 방식입니다.
- `m_OverrideMagic`: **확인됨.** 스테이지에 대한 `MagicType.m_id` 직접 재정의.
- `m_AppendType`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** 예약된 추가 동작 선택기입니다.
- `m_SkillAvailMagnificationCondition`: **확인되었습니다.** `SkillAvailMagnificationConditionList.m_id`. 조건부 확대 동작에 대한 기본 조회 필드입니다.
- `m_SkillAvailMagnificationConditionArray`: **확인되었습니다.** 추가 `SkillAvailMagnificationConditionList.m_id` 행입니다. OT0은 여기서 길이가 7인 배열을 사용합니다.
- `m_SkillAvailMagnification`: **확인됨.** `SkillAvailMagnificationList` 행 ID가 아닌 OT0의 직접 확대 값입니다. 일반적인 OT0 값은 `110`, `130`, `150`, `180`, `200`입니다.
- `m_SkillAvailMagnificationArray`: **확인됨.** 행 ID가 아닌 OT0에 추가 배율 값을 직접 지정합니다. OT0은 `[200,190,180,170,160,150,140]`와 같은 배열을 사용합니다.
- `m_InvocationProbability`: **관찰됨.** 추가 확률 게이트. OT0은 한 행에만 사용합니다.
- `m_ModeChangeID`: **확인되었습니다.** `Enemy/EnemyModeID.m_id`. 이것은 일반적인 플레이어 형식 스위치가 아닌 보스/AI 단계 전환 후크입니다.
- `m_CountRefAilment`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 항상 0입니다.
- `m_CountRefValue`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 항상 0입니다.
- `m_CountRefValueLimit`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 항상 0입니다.

#### 구체적인 OT0 예
- `m_ResistID`: `SkillAvailID#40085` 및 `#73697`는 모두 `6527`를 사용하고, `SkillAvailID#73698`는 `6528`를 사용합니다. 해당 값은 `EnemyWeakID` 행으로 존재하므로 이 필드는 단순한 백분율 수정자가 아닌 전체 약점 테이블 대체 경로처럼 작동합니다.
- `m_OverrideWeapon` / `m_OverrideMagic`: `SkillAvailID#76127`부터 `#76134`까지는 `Repeated Bash`에서 사용하는 무기 계열 스테이지로, `m_OverrideWeapon = 1..8`를 거쳐 진행됩니다. 대조적으로, `Call for Help`의 `SkillAvailID#74047` 및 `#74051`는 `m_OverrideMagic = 5`를 사용하여 동일한 스테이지 구조체가 대신 매직 패밀리를 강제로 렌더링할 수 있음을 보여줍니다.
- `m_Reinforcement` : `SkillAvailID#20049`는 보이는 텍스트가 `<skl_user_name> calls for his lackeys!`인 적 스킬에 `m_Reinforcement = 43`를 사용합니다. 페어링된 `SkillEffectiveID#20049`에는 소환 성공/소환 실패 전투 로그 문자열도 포함되어 있는데, 이는 게임 플레이와 프레젠테이션 행이 함께 작동하는 좋은 예입니다.
- `m_WeakChangeID` / `m_WeakLockID`: `SkillAvailID#20079` 및 `#20089`는 보이는 계열 `Weaknesses have been swapped!`에 속하고, `SkillAvailID#20048`는 `<skl_user_name> is protected by his lackeys.`에 속하며 `m_WeakLockID = 11`를 전달합니다. 이것이 바로 약점 테이블 돌연변이 대 슬롯 잠금에 대해 기대할 수 있는 패턴입니다.
- `m_ModeChangeID`: `Indestructible Flame`의 `SkillAvailID#77350`, `Dark Hex`의 `#74765`, `Vorpal Strike`의 `#75499`는 모두 0이 아닌 모드 변경 ID를 설정하므로 이 필드는 일반 플레이어 자세 스위치 대신 보스/AI 단계 후크로 읽는 것이 가장 좋습니다.
- `m_SkillAvailMagnification*`: `Surprise Attack`의 `SkillAvailID#74940`는 조건 패키지 `69`와 배열 패키지 `[68,67,59,61]` 및 직접 확대 `110/130/150/170/200`를 사용합니다. `Ulti-meow`의 `SkillAvailID#76297`는 `102..109` 패키지와 최대 `300`의 직접 배율을 사용합니다. 이는 `SkillAvailMagnificationList` 행 ID가 아닌 원시 값입니다.

#### 대륙의 패자 전용 추가 필드
- `m_HitRatio`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** OT0의 단순한 적중률 테이블을 넘어서는 추가 적중률 스칼라입니다.
- `m_LotteryAilmentNum`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 복권 경로에서 추첨할 상태 이상 수입니다.
- `m_CompareValueAilment`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 상태 이상 측 논리에서 사용되는 비교 값입니다.
- `m_DelayedSkillActByOwner`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 최종 상태 보유자가 아닌 원래 소유자로 지연된 기술을 실행합니다.
- `m_SelfAfterSkillCheck`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 스킬 실행 후 추가 자체 점검.
- `m_NoSelfAfterSkillCheckEffective`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 자체 점검에 실패하면 점검 후 표시 경로를 억제합니다.
- `m_SubAilmentTag`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨** 2차 상태 이상 그룹화를 위한 태그 ID입니다.
- `m_SubAilmentTagFlag`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 이전 필드에 대한 활성화/태그 동작 토글.
- `m_CalcTypeResist`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 저항 조작을 위한 별도의 계산 모드 선택기입니다.
- `m_AddResistWeapon`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 추가 무기 저항 페이로드 배열.
- `m_AddResistMagic`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 추가 요소 저항 페이로드 배열.
- `m_AilmentMagnificationConditions`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 상태 이상측 확대 조건 ID입니다.
- `m_AilmentMagnifications`: **Confirmed from 대륙의 패자 schema.** Ailment-side direct magnification values.
- `m_CountRefAilmentTag`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 태그 기반 카운터 참조 채널.
- `m_CountRefType`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 참조되는 카운트 제품군을 선택합니다.
- `m_CountRefAttribute`: **Observed from name and 대륙의 패자 usage.** Attribute selector for the referenced counter.
- `m_IgnorePreviousSuccessful`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 반복 검사를 해결할 때 이전 성공 상태를 무시합니다.
- `m_IgnoreDodge`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 닷지 해결을 우회합니다.
- `m_IgnoreZeroDamage`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 손상이 0이 되어도 효과 처리를 계속합니다.
- `m_AddDamageLimitValue`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 추가 손상 한도가 증가합니다.
- `m_AddDamageLimitCondition`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 손상 한도 증가에 대한 조건 게이트입니다.
- `m_UseAilmentMagnificationForAddDamageLimit`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 손상 한도 증가를 위해 상태 이상 확대 값을 재사용합니다.
- `m_NonCondition`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 추가 부정/조건 없음 스위치입니다.
- `m_IgnoreResistAilment`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 상태 이상 저항을 우회합니다.
- `m_IgnoreDefenceBuff`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 방어력 향상 버프를 무시합니다.
- `m_IgnoreReduceDamage`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 일반적인 피해 감소 효과를 우회합니다.
- `m_HitWeaponEnable`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 명시적인 히트 무기 입력을 활성화합니다.
- `m_HitWeaponType`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨** 히트 무기 타이핑이 활성화될 때 사용되는 무기 계열입니다.
- `m_NoConsumeAdditionWeak`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 적중 후 추가-약한 상태를 유지합니다.
- `m_IsBuffDamageSupStrictlyCheck`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 버프-데미지 지원 상호 작용에 대한 더욱 엄격한 검증.

#### OT0 사용 참고사항
- `Array sizes`: OT0은 고정 크기를 사용합니다: `m_HitTypes` 길이 `4`, `m_Values` 길이 `4`, `m_SkillRatios` 길이 `4`, `m_Turns` 길이 `4`, `m_Counts` 길이 `4`, `m_AddAilment` 길이 `16`, `m_SubAilment` 길이 `16`, `m_SkillAvailMagnificationConditionArray` 길이 `7`, `m_SkillAvailMagnificationArray` 길이 `7`.
- `Common opcodes`: OT0에서는 `m_ModifyType = 1 (CHARACTER)`를 압도적으로 사용합니다. 다음으로 가장 일반적인 계열은 약점 변경, 소생, 도용, 결과 보상 및 `ITEM_SUPPLY`와 같은 마을 경제 opcode입니다.
- `Dormant counters`: `m_ShieldDamage`, `m_NoBreakable`, `m_EnhancedSkillID`, `m_AppendType` 및 `m_CountRef*` 필드는 OT0에서 사실상 휴면 상태입니다.

### `SkillConditionList`
`SkillAvailID.m_InvokeCondition` 및 기타 스킬 시스템에 대한 재사용 가능한 부울 조건 패키지입니다. 이 파일을 `SkillAvailMagnificationConditionList`와 혼동하지 마십시오. 그들은 다른 opcode 계열입니다.

- **OT0 행:** `275`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `933`.
- **OT0 SDK 행 구조:** `FSkillConditionListBase`.
- 여기에 사용된 OT0 SDK 덤프는 이 테이블의 `m_Conditions`에 대한 명확한 이름의 열거형을 노출하지 **않습니다**. 관찰된 opcode 세트는 확대 조건 열거형보다 훨씬 크며 `10723`, `10790` 및 `10816`와 같은 값을 포함합니다.

#### 필드별
- `m_id`: 조건 패키지의 기본 키입니다.
- `m_Conditions`: **모양은 확인되었지만 이름은 확인되지 않았습니다.** 4개의 조건 opcode가 슬롯별로 해석되었습니다. 이는 `SkillAvailID.m_InvokeCondition`에서 사용되는 주요 조건군입니다.
- `m_Params`: **확인됨.** 4개의 일반 정수 매개변수 슬롯. 각 슬롯의 의미는 일치하는 `m_Conditions` 항목에 의해 결정됩니다.
- `m_AilmentTypes`: **확인됨.** 상태 이상 인식 상태 opcode를 위한 4개의 `SkillAilmentType.m_id` 매개변수 슬롯. OT0은 여기서 `36` 고유 상태 이상 ID를 사용합니다.
- `m_StatusTypes`: **형태가 확인되었습니다.** 상태 인식 조건 opcode를 위한 4개의 `CharaStatusID` 매개변수 슬롯. OT0은 이를 사용하지 않은 상태로 두지만 『옥토패스 트래블러: 대륙의 패자』는 동일한 필드 제품군을 보다 적극적으로 사용합니다.
- `m_WeaponTypes`: **형태가 확인되었습니다.** `WeaponType.m_id` 매개변수 슬롯 4개. OT0에서는 사용되지 않습니다.
- `m_MagicTypes`: **형태가 확인되었습니다.** 4개의 `MagicType.m_id` 매개변수 슬롯. OT0에서는 사용되지 않습니다.
- `m_Equipment`: **형태가 확인되었습니다.** 장비 매개변수 슬롯 4개. OT0에서는 사용되지 않습니다.
- `m_ORFlag`: **확인됨.** 점유된 조건 슬롯을 AND 대신 OR로 결합합니다. OT0 행 `335` 및 `336`는 서로 다른 상태 이상 ID에 걸쳐 동일한 상태 이상 확인 opcode를 반복하여 이를 직접 증명합니다.

#### 대륙의 패자 전용 추가 필드
- `m_AutoDungeonBuff`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 추가 자동 던전 전용 버프 매개 변수 채널입니다.
- `m_AutoDungeonBuffType`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 자동 던전 버프 채널에 대한 유형 선택기입니다.

### `SkillAvailMagnificationConditionList`
조건부 확대를 위한 재사용 가능한 조건 패키지입니다. `SkillConditionList`와 달리 이 파일은 명명된 OT0 SDK 열거형과 **일치**합니다.

- **OT0 행:** `89`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `217`.
- **OT0 SDK 행 구조:** `FSkillAvailMagnificationConditionListBase`.
- **관련 OT0 SDK 열거형:** `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`.

#### 이 테이블에서 사용된 정확한 OT0 SDK 열거형 인용문
##### `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6226`에서 인용되었습니다.
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
- `m_Conditions`는 이 열거형의 원시 ID를 직접 저장합니다. 따라서 `SkillAvailMagnificationConditionList`는 모호한 조건 버킷이 아닌 해당 배율 opcode에 대한 정확한 조건 패키지 테이블입니다.
- 이는 또한 `SkillAvailID.m_SkillAvailMagnificationCondition` 및 `m_SkillAvailMagnificationConditionArray`가 이 파일의 행 ID로 정확하게 설명될 수 있는 이유이기도 합니다. 이러한 필드는 점유된 슬롯이 위의 열거형에 의해 자체적으로 유형화된 패키지를 가리킵니다.

#### 필드별
- `m_id`: 확대 조건 패키지의 기본 키입니다.
- `m_Conditions`: **확인됨.** `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`의 4가지 조건 opcode입니다. OT0은 `SLD_GE`, `HAS_AILMENT`, `RACE_KILLER`, `BEFORE_ACTION`, `HP_SELF_LE`, `ENABLED_SUPPORTABILITY_SELF`, `DAY_OR_NIGHT`, `TURN_ORDER_FIXED_LE`, `PROGRESS`, `SP_BURST_GE` 및 방어 임계값과 같은 값을 사용합니다.
- `m_IntParams`: **확인됨.** 점유된 `m_Conditions` 슬롯에 해당하는 4개의 일반 숫자 임계값입니다.
- `m_AilmentParams`: **관찰됨.** 상태 이상 관련 매개변수. 많은 OT0 값이 `SkillAilmentType.m_id`와 일치하지만 소수의 OT0 행 참조 ID가 현재 OT0 `SkillAilmentType` 내보내기에 없으므로 모든 값이 라이브 OT0 상태 이상 행이라고 가정하지 마십시오.
- `m_RaceParams`: **관찰됨.** 레이스 필터/매개변수 슬롯. OT0 barely uses them.
- `m_Equipment`: **형태가 확인되었습니다.** 장비 필터 슬롯. OT0은 사용되지 않은 상태로 둡니다.

#### 구체적인 OT0 예
- `SkillAvailMagnificationConditionList#7`는 `m_Conditions = [2,0,0,0]`를 `m_IntParams = [10,0,0,0]`와 함께 저장합니다. 즉, 간단한 `SLD_GE 10` 패키지입니다.
- `#8`는 `m_Conditions = [5,0,0,0]`를 `m_AilmentParams = [1,0,0,0]`와 함께 저장합니다. 즉, `HAS_AILMENT`를 상태 이상 행 `1`와 함께 저장합니다.
- `#10`는 `m_Conditions = [7,0,0,0]`, 즉 추가 정수 페이로드가 없는 순수 `BEFORE_ACTION` 게이트를 저장합니다.
- `#13`는 `m_Conditions = [12,0,0,0]`를 `m_IntParams = [100,0,0,0]`와 함께 저장합니다(예: `HP_SELF_LT 100` 스타일 패키지).
- `#81`는 `m_Conditions = [8,0,0,0]`를 `m_IntParams = [90,0,0,0]`와 함께 저장합니다(예: `HP_SELF_LE 90` 임계값 패키지).

#### 대륙의 패자 전용 추가 필드
- `m_WeaponTypes`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 확대 조건을 위한 무기 계열 필터 슬롯.
- `m_MagicTypes`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 배율 조건을 위한 요소군 필터 슬롯.

### `SkillAvailMagnificationList`
4개의 행이 있는 작은 OT0 전용 조회 테이블입니다. 실제이지만 OT0 `SkillAvailID` 행에서 사용되는 기본 연결 경로는 **아닙니다**.

- **OT0 행:** `4`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** 이 저장소에 제공된 OT0 SDK 덤프에서 일치하는 전용 행 구조를 찾을 수 없습니다.
- 중요한 실무 포인트는 OT0 `SkillAvailID.m_SkillAvailMagnification`가 `110`, `200`와 같은 직접 값을 저장하는 반면, 이 테이블의 ID는 `0..3`뿐이라는 점입니다.

#### 필드별
- `m_id`: 작은 조회 행의 기본 키입니다.
- `m_Conditions`: **관찰됨.** 이 작은 범례 테이블의 단일 슬롯 조건 선택기입니다.
- `m_Params`: **관찰됨.** 조회 행에 대한 단일 슬롯 매개변수 페이로드입니다.

### `SkillEffectiveID`
프레젠테이션 페이로드 행. 여기에서는 카메라, 전투 동작, 효과, 사운드, 음성, 텍스트 팝업 및 가시성 변경 사항이 시간에 맞춰 진행됩니다.

- **OT0 행:** `4381`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `11434`.
- **OT0 SDK 행 구조:** `FSkillEffectiveIDBase`.
- **관련 OT0 SDK 열거형:** `ESKILLEFFECT_LAYOUT`, `ESKILLEFFECTIVE_CHARA_VISIBILITY`, `ESKILLEFFECTIVE_VISIBILITY_TARGET`.

#### 이 행을 직접 구동하는 정확한 OT0 SDK 열거형 인용문
##### `ESKILLEFFECT_LAYOUT`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6406`에서 인용되었습니다.
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
- `m_EffectLayout`는 원시 `ESKILLEFFECT_LAYOUT` 값을 저장합니다. 이것이 바로 각 효과 슬롯이 `SELF`, `TARGET`, `TARGET_CENTER` 또는 `BATTLE_CENTER`에서 생성되는지 여부를 정확하게 말할 수 있는 이유입니다.

##### `ESKILLEFFECTIVE_CHARA_VISIBILITY`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6371`에서 인용되었습니다.
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
- `m_CharaVisibility`는 가시성 작업 자체(`VISIBLE`, `INVISIBLE`, `FADE_VISIBLE`, `FADE_INVISIBLE`)에 대해 이 열거형의 원시 값을 저장합니다.

##### `ESKILLEFFECTIVE_VISIBILITY_TARGET`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6383`에서 인용되었습니다.
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
- `m_VisibilityTarget`는 가시성 작업(`SELF`, `TARGET`, `FRIENDLY_ALL`, `ENEMY_ALL`, `FORWARDS_AND_BACKUPS` 등)을 수신하는 *누가*에 대해 이 열거형의 원시 값을 저장합니다.
- OT0은 의도적으로 가시성 스테이징을 두 가지 차원으로 분할합니다. 하나의 열거형은 가시성 작업을 설명하고 다른 열거형은 수신자 세트를 설명합니다.

#### 필드별
- `m_id`: 프레젠테이션 단계의 기본 키입니다.
- `m_Repeat`: **확인됨.** 단계 반복 횟수입니다. OT0은 모든 행에서 이를 `1`로 설정합니다.
- `m_HoldTime`: **관찰됨.** 총 프레젠테이션 보류 시간(초)입니다.
- `m_AvailTime`: **관찰됨.** 프레젠테이션과 관련하여 게임 플레이 단계가 사용 가능/해결된 것으로 간주되는 시간(초)입니다.
- `m_Cameras`: **확인됨.** 카메라 안무에는 최대 3개의 `Battle/BattleCameraParams.m_id` 행이 있습니다.
- `m_CameraTimes`: **관찰됨.** 카메라 단계의 트리거 시간입니다.
- `m_Animations`: **확인됨.** 수행된 전투 동작에 대해 최대 3개의 `Battle/BattleActionID.m_id` 행이 있습니다.
- `m_PlayTimes`: **관찰됨.** 전투 행동의 트리거 시간입니다.
- `m_PlayRates`: **관찰됨.** 전투 행동에 대한 재생 속도 승수입니다.
- `m_AnimationTarget`: **관찰됨.** 애니메이션을 재생하는 사람을 위한 작업별 대상 선택기입니다.
- `m_AnimStartFrame`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** SDK 구조에 있지만 OT0 행에서는 사용되지 않는 시작 프레임 재정의 필드입니다.
- `m_AnimEndFrame`: **관찰됨.** 애니메이션 단계의 끝 프레임 또는 끝 섹션 마커입니다. OT0은 모든 행에 이를 채웁니다.
- `m_AnimNoIdle`: **관찰됨.** 애니메이션 후 유휴 복귀 또는 유휴 블렌드를 억제합니다. OT0에서는 드물다.
- `m_Effects`: **확인됨.** 프레젠테이션 단계에서 최대 3개의 `Effect/EffectList.m_id` 행이 생성됩니다.
- `m_InvokeTimes`: **관찰됨.** 효과 생성 시간.
- `m_EffectLayout`: **확인됨.** `ESKILLEFFECT_LAYOUT`(`SELF`, `TARGET`, `FRIENDLY_CENTER`, `TARGET_CENTER`, `BATTLE_CENTER` 등)의 효과별 생성 레이아웃입니다.
- `m_OffsetsH`: **관찰됨.** 생성된 효과에 대한 수평 오프셋입니다.
- `m_OffsetsV`: **관찰됨.** 생성된 효과에 대한 수직 오프셋입니다.
- `m_Sounds`: **확인됨.** 스테이지에 의해 최대 3개의 `Sound/SoundList.m_id` 행이 트리거됩니다.
- `m_SoundTimes`: **관찰됨.** 사운드 행의 트리거 시간입니다.
- `m_SoundToVoice`: **관찰됨.** 사운드가 음성 스타일 타이밍/처리에 연결되는지 여부를 제어하는 ​​드문 라우팅/링크 플래그입니다.
- `m_Voices1`: **확인되었습니다.** `Sound/PartVoiceID.m_id`를 가리키는 기본 음성 슬롯입니다.
- `m_Voices2`: **확인됨.** `Sound/PartVoiceID.m_id`를 가리키는 보조 음성 슬롯.
- `m_Voices3`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** 3차 음성 슬롯이 있지만 OT0에는 0이 있습니다.
- `m_VoiceTimes`: **관찰됨.** 음성 트리거 시간.
- `m_Texts`: **강력한 증거로 관찰됨.** 최대 10개의 부동 텍스트 ID. 대부분의 OT0 값은 `GameTextSkill`를 통해 확인되고 일부는 `GameTextUI`를 통해 확인되며 작은 잔여물은 현재 명백한 OT0 뱅크 외부에 있습니다.
- `m_TextTime`: **관찰됨.** 부동 텍스트 트리거 시간.
- `m_CharaVisibility`: **확인됨.** `ESKILLEFFECTIVE_CHARA_VISIBILITY`의 가시성 단계별 값입니다.
- `m_VisibilityTarget`: **확인됨.** `ESKILLEFFECTIVE_VISIBILITY_TARGET`의 가시성 단계별 값입니다.
- `m_VisibilityStartTime`: **관찰됨.** 가시성 변경 시작 시간입니다.

#### 구체적인 OT0 예
- 플로팅 텍스트는 가상이 아닙니다. `SkillEffectiveID#20049`는 `<skl_user_name> calls for his lackeys!` 및 `However, the lackeys did not appear...` 문자열을 전달하며 이는 강화 스타일 적 기술의 정확한 프레젠테이션 동반자입니다.
- 가시성 스테이징도 잔여 필드가 아닌 실제입니다. `Lotus Blade`에서 사용되는 `SkillEffectiveID#65047` 및 `#65052`는 `TARGET`에서 `INVISIBLE -> VISIBLE`를 사용하여 대상 가시성을 전환합니다.
- `Pilfer`에서 사용되는 `SkillEffectiveID#65954` 및 `#65956`는 `TARGET`에서 `FADE_INVISIBLE`/`FADE_VISIBLE`를 사용하여 하드 토글 변형이 아닌 페이드 변형을 보여줍니다.
- `Hunter's Drive`에서 사용되는 `SkillEffectiveID#65572` 및 `#65631`, SDK 열거형의 `FRIENDLY_ALL_WITHOUT_SELF`와 일치하는 대상 가시성 선택기 `11`. 이는 가시성 목표 열거형이 실제로 그대로 소비되고 있다는 좋은 증거입니다.
- `Soaring Javelin` 및 `Take Aim`에서 사용되는 `SkillEffectiveID#66152`는 대상 선택기 `20`, 즉 `ENEMY_ALL`에 가시성 단계를 적용합니다. 이는 동일한 시스템이 일대일 스텔스 효과에 국한되지 않음을 증명합니다.

#### 대륙의 패자 전용 추가 필드
- `m_MagicStonePerformance`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 대륙의 패자 특정 마법석 성능 자산 또는 모드.
- `m_MagicStonePerformanceStartTime`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 이전 필드의 시작 시간입니다.
- `m_MagicStoneLevel`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 마법석 프레젠테이션을 위한 레벨 또는 계층 선택기입니다.
- `m_CameraShakeCurve`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 카메라 흔들림 자산/곡선.
- `m_CameraShakeStartTime`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 카메라 흔들림이 시작되는 시간입니다.
- `m_Text2nd`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 두 번째 텍스트 뱅크/보조 부동 텍스트.
- `m_SkillEffectiveLinkageID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마와 ID 중복에서 확인되었습니다.** 연결된 효과 그룹의 경우 `SkillEffectiveLinkageList.m_id`입니다.
- `m_IsSetVisibleUI`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 스테이지 중에 UI 가시성 제어를 활성화합니다.
- `m_UIVisiblityFlag`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** UI 가시성 모드/플래그.
- `m_UIVisibilityTarget`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** UI 가시성 대상 선택기.
- `m_UIVisibilityStartTime`: **이름 및 대륙의 패자 사용에서 관찰됨.** UI 가시성 변경 시작 시간.

#### OT0 대 대륙의 패자 참고
- `Camera schema shift`: OT0은 `m_Cameras` 및 `m_CameraTimes`를 통해 카메라 행을 인라인으로 유지합니다. 내보낸 `Skill/` 폴더의 대륙의 패자 `SkillEffectiveID` 스키마는 해당 인라인 배열을 삭제하고 대신 보조 UI/효과 연결 필드를 확장합니다.

### `SkillAilmentType`
재사용 가능한 상태 이상/상태 패키지. 이 파일은 전형적인 부정적인 상태, 긍정적인 버프, 카운터, 차단, 재발생 상태, 회피 상태, 손상 제한 오라, 영구 상태 및 기타 여러 시간 제한 또는 트리거 효과를 다룹니다.

- **OT0 행:** `490`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `968`.
- **OT0 SDK 행 구조:** `FSkillAilmentTypeBase`.
- **관련 OT0 SDK 열거형:** `Kingship::ESKILL_INVOKE`, `ESKILL_AILMENT_CALC_TYPE`.
- 전체 스킬 시스템에서 가장 조밀하고 중요한 테이블 중 하나입니다. `SkillAvailID`는 여기를 가리키는 것 외에는 자체적으로 거의 수행하지 않는 경우가 많습니다.

#### 이 행을 직접 구동하는 정확한 OT0 SDK 열거형 인용문
##### `Kingship::ESKILL_INVOKE`(타이밍 열거형)
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6436`에서 인용되었습니다.
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
- `SkillAilmentType.m_InvokeTiming`는 `SkillAvailID.m_InvokeTiming`가 사용하는 것과 동일한 타이밍 열거형 제품군을 사용합니다. 따라서 엔진은 동일한 전역 타이밍 어휘에 대해 상태 이상 틱 및 단계 실행을 예약합니다.

##### `EAILMENT_CONDITION_ID`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:531`에서 인용되었습니다.
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
- `m_RemoveConditions`는 이 상태 이상 상태 열거형의 원시 값을 저장합니다. 이것이 바로 절전, 카운트다운 또는 연결 중단 상태와 같은 행이 `PHYSICAL_DAMAGE`, `BREAK_OR_DEAD_ENFORCER` 또는 `CAN_NOT_ACTION`와 같은 정확한 제거 트리거를 표현할 수 있는 이유입니다.

##### `ESKILL_AILMENT_CALC_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6208`에서 인용되었습니다.
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
- `m_AilmentCalc`는 이 열거형의 원시 값을 저장하고 `SkillAvailID.m_CalcTypeAilment`는 추가된 상태 이상 행에 대해 동일한 계열을 사용하는 것으로 보입니다. 이것이 피해 기반 버프, 제한 수정치, 관련 상태 이상 계산이 하나의 숫자 어휘를 공유하는 정확한 이유입니다.
- `m_ResistWeapon` 및 `m_ResistMagic`는 위에 `WeaponType` 및 `MagicType`에 포함된 정확한 `EWEAPON_TYPE` 및 `EMAGIC_TYPE` 따옴표와 비교하여 읽어야 합니다. 왜냐하면 해당 배열은 상태 이상 행이 스칼라 ID가 아닌 마스크로 저장하더라도 동일한 계열 순서로 키가 지정되기 때문입니다.

#### 필드별
- `m_id`: **확인됨.** 상태 이상 행의 기본 키입니다. `SkillAvailID.m_AddAilment`, `m_SubAilment`, `SkillConditionList.m_AilmentTypes`, `SkillResistAilmentID.m_ResistAilments`, `m_RemoveAilment` 및 `m_ChildAilment`에서 참조됩니다.
- `m_Label`: 내부/편집기 라벨. 이는 행의 용도를 식별하는 사람이 읽을 수 있는 가장 빠른 방법인 경우가 많습니다.
- `m_Resist`: **강력한 증거로 관찰됨** 이 행에 일반적인 상태 이상 저항 논리가 적용되는지 여부. 독, 출혈, 수면, 마비, 공포, 어둠과 같은 고전적인 부정적인 상태에 대해서는 사실입니다.
- `m_Minus`: **관찰됨.** 부정적/디버프 유사 상태 이상 플래그.
- `m_Plus`: **관찰됨.** 양성/버프 유사 상태 이상 플래그.
- `m_Removable`: **관찰됨.** 일반적인 치료/제거 행동으로 상태 이상을 제거할 수 있는지 여부.
- `m_Multiply`: **관찰됨.** 스태킹/공존 동작 플래그. 많은 일반 버프 및 디버프 상태는 이를 true로 유지하지만 일부 싱글톤 제어 상태는 그렇지 않습니다.
- `m_OverwriteTurn`: **관찰됨.** 단순히 기존 상태를 확장하거나 무시하는 대신 저장된 턴을 덮어써서 다시 적용합니다.
- `m_ForceOverrideValue`: **관찰됨.** 저장된 상태 이상 값과 턴 수를 덮어써서 다시 적용합니다.
- `m_InvalidateDebuff`: **사용 패턴으로 확인됨** 행을 디버프를 무효화하거나 억제하는 상호 작용에 대한 디버프 클래스 상태로 표시합니다. OT0는 방어력 감소, 공격 감소, 무기 약화, 요소 약화 상태와 같은 디버프 행에 설정합니다.
- `m_CanInactiveFlag`: **관찰됨.** 비활성/오프 상태 표현을 허용합니다. OT0은 이를 일반 버프/디버프 행과 영구 변형에 설정합니다. 이는 정확히 상태 전환 또는 기억되지만 비활성 동작이 예상되는 위치입니다.
- `m_InvokeTiming`: **확인됨.** 더 풍부한 `Kingship::ESKILL_INVOKE` 열거형의 트리거 타이밍입니다. OT0은 `BATTLING`, `ACTION_DAMAGE`, `SLIP_DAMAGE`, `FORWARD`, `ALWAYS` 등과 같은 타이밍을 사용합니다.
- `m_TargetStatus`: **확인됨.** `Character/CharaStatusID.m_id`는 구체적인 능력치/자원을 수정할 때 상태 이상에 영향을 받습니다.
- `m_TargetStatusArray`: **형태가 확인되었습니다.** 다중 상태 상태 이상에 대한 추가 상태 목표입니다. OT0은 거의 사용하지 않습니다.
- `m_AilmentCalc`: **확인됨.** `ESKILL_AILMENT_CALC_TYPE`의 상태 이상측 계산 모드입니다. OT0은 대부분 `NONE`를 유지하지만 손상 파생 및 한계 파생 상태는 더 높은 값을 사용합니다.
- `m_EfficacyStepRate`: **관찰됨.** 상태 이상 효능에 대한 단계적 스케일링 매개변수입니다. OT0에서는 드물다.
- `m_ResistWeapon`: **모양이 확인되고 의미가 관찰되었습니다.** 부울 무기 계열 마스크입니다. 이름에도 불구하고 OT0는 이를 카운터 및 요격 행에 많이 사용하여 국가가 반응하는 무기 히트 계열을 정의합니다.
- `m_ResistMagic`: **모양을 확인하고 의미를 관찰했습니다.** 부울 요소 계열 마스크는 마법/요소 카운터 및 인터셉트에 대해 동일한 방식을 사용했습니다.
- `m_RemoveAilment`: **확인됨.** 이 상태 이상이 적용되면 최대 4개의 `SkillAilmentType.m_id` 행이 제거됩니다. 예: 더 강력한 변형 또는 무효화 상태는 더 약한 선행 항목을 제거할 수 있습니다.
- `m_TargetType`: **관찰됨.** 작은 상태 이상-내부 대상 선택기. OT0은 3개의 `...TARGET_IS_ENEMY_ALL` 카운터/가로채기 행에서 0이 아닌 값만 사용하므로 이를 전체 전투 대상 열거형으로 처리하지 마십시오.
- `m_RemoveConditions`: **관찰됨.** 상태 이상이 제거되거나 손상되는 방법을 제어하는 ​​조건 opcode입니다. Sleep이 좋은 예입니다. OT0 행은 0이 아닌 제거 조건을 사용하므로 피해/작업을 통해 이를 지울 수 있습니다.
- `m_RemoveParams`: **관찰됨.** 제거 조건 슬롯에 대한 매개변수입니다.
- `m_ChildAilment`: **확인되었습니다.** 이 상태 이상과 관련된 아동 상태 이상의 `SkillAilmentType.m_id`입니다.
- `m_TextID`: **확인됨.** 주로 `GameTextUI`에서 표시되는 상태 이상 이름 텍스트 ID입니다. 예: 독, 어둠, 버프 상태 라벨이 여기서 해결됩니다.
- `m_ExplanationTextID`: **확인됨.** 주로 `GameTextUI`에서 나타나는 상태 이상 설명 텍스트 ID입니다.
- `m_IconTexID`: **확인됨** 주요 상태 이상 아이콘은 `Texture/TextureID.m_id`입니다.
- `m_IconSptTexID`: **확인되었습니다.** 지원/보조 상태 이상 아이콘에 대한 `Texture/TextureID.m_id`입니다. OT0에서는 일반적으로 `m_IconTexID`와 일치합니다.
- `m_AddEffect`: **확인됨.** 상태 이상이 추가되면 `Effect/EffectList.m_id`가 스폰됩니다.
- `m_CharacterEffect`: **확인됨.** 상태 이상이 활성화된 동안 영향을 받은 캐릭터에 `Effect/EffectList.m_id`가 부착됩니다.
- `m_FieldEffect`: **확인됨.** `Effect/EffectList.m_id`는 상태 이상의 필드 맵 변형에 사용됩니다.
- `m_InvokeEffect`: **확인됨.** `Effect/EffectList.m_id`는 상태 이상이 유발되거나 틱일 때 생성됩니다.
- `m_OnGroundInvoke`: **관찰됨.** 현장 사용 사례와 같은 일반적인 전투 동작 타이밍을 벗어난 상태 이상의 호출/틱 동작을 허용합니다.
- `m_AddEffectSound`: **확인되었습니다.** 상태 이상이 적용될 때 `Sound/SoundList.m_id`가 재생됩니다.
- `m_InvokeSound`: **확인되었습니다.** `Sound/SoundList.m_id`는 상태 이상이 발생하거나 틱할 때 재생됩니다.
- `m_ActionID`: **확인됨.** 상태 이상 상태에 대한 `Battle/BattleActionID.m_id` 애니메이션 후크입니다.
- `m_ActionPriority`: **OT0에서 사용되지 않는 것으로 확인되었습니다.** 구조체에는 있지만 OT0은 0으로 둡니다.
- `m_ReplaceCharaID`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 구조체에는 있지만 OT0은 0으로 둡니다.
- `m_CountDown`: **관찰됨.** 상태 이상을 카운트다운 스타일로 표시합니다.
- `m_NoCount`: **관찰됨.** 일반적인 턴/카운트 소비를 방지합니다. OT0는 이를 흡수, 보장/확률적 회피, 용기 및 숙달 유사 상태에 사용합니다.
- `m_StatusDetailSort`: **관찰됨.** 상태 이상/상세 표시를 위한 UI 주문 키입니다.
- `m_IgnoreLimit`: **관찰됨.** 일반적인 버프/디버프 한도 규칙을 우회합니다. OT0는 SP 비용 수정자, 매력과 같은 상태, 피해 제한 증가 및 유사한 특수 사례 오라에 이를 사용합니다.
- `m_RemainDead`: **관찰됨.** 죽은 유닛이나 사망 후에도 상태 이상을 의미 있게 유지합니다. OT0는 재발급 상태와 몇 가지 특별한 지속 상태에 이를 사용합니다.
- `m_IsNotMemorize`: **관찰됨.** 희귀 메모리/스냅샷 제외 플래그. OT0은 두 행에만 사용합니다.
- `m_AbsorbHealStatus`: **확인되었습니다.** `Character/CharaStatusID.m_id`는 흡수/치유 스타일 상태 이상 로직에 의해 복원되었습니다.
- `m_NotRace`: **관찰됨.** `m_EnemyRace`의 레이스 필터를 반전하거나 무효화합니다. OT0에서는 드물다.
- `m_EnemyRace`: **관찰됨.** 상태 이상 로직을 위한 작은 레이스 필터 배열. OT0에서는 드물다.
- `m_ForceSupportAbility`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 항상 거짓입니다.
- `m_ForceCommandAbility`: **관찰됨.** 독, 출혈, 마비, 어둠과 같은 표준 부정적인 상태에 드물게 플래그가 설정됩니다. 가장 안전한 방법은 이러한 행을 명령 기능 상호 작용 경로에 유지하지만 OT0은 SDK 덤프에서 더 나은 이름의 호출자를 제공하지 않는다는 것입니다.
- `m_WeaponAppendType`: **관찰됨.** 무기 추가 동작 유형 ID입니다. 이름에도 불구하고 OT0 값은 완전한 `EWEAPON_TYPE` 매핑이 아니므로 이를 원시 무기 열거형이 아닌 상위 수준 추가 동작 선택기로 처리합니다.
- `m_MagicAppendType`: **관찰됨.** 요소 추가 동작 유형 ID입니다. 다시 말하지만, OT0 값은 일반 `EMAGIC_TYPE` 매핑이 아닙니다.
- `m_DuplicateAilment`: **관찰됨.** 동일한 상태 이상의 중복 인스턴스를 허용합니다. OT0은 이를 한 행에만 사용합니다.
- `m_SkillReplace`: **OT0에서 사용되지 않은 것으로 확인되었습니다.** 예약된 스킬 교체 후크입니다.

#### 구체적인 OT0 예
- 일반 상태 패밀리는 직접 확인하기 쉽습니다. `SkillAilmentType#1 POISON`는 `Poison`, `#8 HIGH_POISON`는 `Toxin`, `#2 BLEED`는 `Bleeding`, `#6 FEAR`는 `Terror`입니다.
- `m_RemoveConditions`는 장식용이 아닙니다. `SkillAilmentType#4 SLEEP`는 `m_RemoveConditions = [10,0]`를 사용하고 `#285 DEEP_SLEEP`는 `[1,0]`를 사용합니다. OT0은 동일한 광범위한 수면 테마 내에서 다양한 제거/중단 논리 제품군을 명확하게 구분합니다.
- 카운터/인터셉트 제품군은 `m_ResistWeapon` 및 `m_ResistMagic`가 일반 저항 테이블보다 반응 마스크로 더 잘 읽히는 이유를 보여줍니다. `#52 COUNTER_SWORD` 및 `#70 INTERCEPT_SWORD`는 해당 계열의 대표적인 무기별 행입니다.
- `m_CountDown`는 ​​실제 게임플레이 플래그입니다. `SkillAilmentType#20 COUNTDOWN`는 `Fatal Sentence`이고, `#43 DELAYED_SKILL` 및 `#44 DELAYED_RANGE_SKILL`는 모두 카운트 종료 시 대기 중인 기술을 트리거하는 눈에 보이는 `Countdown` 변형입니다.
- `m_ForceCommandAbility`는 랜덤 노이즈가 아닙니다. OT0은 이를 `#1 POISON`, `#2 BLEED`, `#5 PARALYSIS` 및 `#11 DARKNESS`와 같은 전형적인 부정적인 상태로 설정합니다.
- `m_IgnoreLimit`도 진짜입니다. `#203 BUFF_SP_COST_REDUCE (SP Cost Down)`, `#226 BUFF_REDUCE_DAMAGE_HP (Reduce Damage)` 및 `#397 INCREASE_DAMAGE_LIMIT (Surpassing Power)`는 모두 이 기능을 가지고 있는데, 이는 일반적인 버프/디버프 캡 처리를 우회한다는 아이디어에 맞습니다.
- `m_RemainDead`는 한 행으로 제한되지 않습니다. `#283 RERAISE_BUFF (Encore)` 및 `#503 RERAISE_BUFF_LAST_DIED (Last Man Standing)` 모두 이를 사용하며, 이는 무력화 후에도 여전히 중요한 상태에서 기대할 수 있는 것과 정확히 같습니다.

#### 대륙의 패자 전용 추가 필드
- `m_IsBuffDebuff`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 명시적인 버프/디버프 분류 플래그.
- `m_CompareTurn`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 추가 회전 비교 매개변수.
- `m_CompareOverrideValue`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 추가 값 비교 매개변수입니다.
- `m_DisableBoostAnim`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 상태 이상이 발생할 때 부스트 애니메이션을 억제합니다.
- `m_StatusDetailAllName`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 모든 대상 디스플레이에 대한 더 풍부한 상태 세부 이름 텍스트 ID입니다.
- `m_StatusDetailName`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 단일 대상 디스플레이에 대한 더 풍부한 상태 세부 이름 텍스트 ID입니다.
- `m_StatusDetailCommentTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 상태 세부 UI에 대한 설명 텍스트입니다.
- `m_StatusDetailCommentCalcTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 상태 세부 정보 UI에 대한 계산 인식 주석 텍스트입니다.
- `m_StatusDetailCommentRatedTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 상태 세부 정보 UI에 대한 평가된 설명 텍스트입니다.
- `m_StatusDetailCommentNowRatedTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 상태 세부 정보 UI에 대한 현재 값 평가 설명 텍스트입니다.
- `m_StatusDetailCommentFixedTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 상태 세부 정보 UI에 대한 고정 값 설명 텍스트입니다.
- `m_StatusDetailAllCommentTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 모든 대상 주석 텍스트입니다.
- `m_StatusDetailAllCommentCalcTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 모든 대상 계산 인식 주석 텍스트입니다.
- `m_StatusDetailAllCommentRatedTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 모든 대상으로 평가된 댓글 텍스트입니다.
- `m_StatusDetailAllCommentNowRatedTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 모든 대상의 현재 등급 설명 텍스트입니다.
- `m_StatusDetailAllCommentFixedTextID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 모든 대상 고정 값 주석 텍스트입니다.
- `m_AllowAddAilmentOnDead`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 죽은 유닛에 적용을 허용합니다.
- `m_MaxUseCount`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 상태 이상이 발생할 수 있는 최대 횟수입니다.
- `m_IsAttributeCheckAppendType`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됨.** 속성 인식 추가 유형 유효성 검사.
- `m_BindWeakID`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 약점 바인딩 도우미 행 또는 키입니다.
- `m_IsAdditionWeak`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 상태 이상을 『옥토패스 트래블러: 대륙의 패자』의 추가 약점 메커니즘의 일부로 표시합니다.
- `m_AilmentTagID`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 상태 이상에 대한 태그/그룹 식별자입니다.
- `m_Attribute`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 대륙의 패자 상태 이상 논리에 대한 속성 선택기입니다.
- `m_TargetAilment`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 변환/상호작용 논리에 대한 대상 상태 이상 ID입니다.
- `m_ToUnlimited`: **이름 및 대륙의 패자 사용량에서 관찰됩니다.** 무제한 기간으로 변환됩니다.
- `m_Parallel`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 관련 상태의 병렬 공존을 허용합니다.
- `m_PhaseOver`: **이름 및 『옥토패스 트래블러: 대륙의 패자』 쪽 사용례로 보아 관찰됩니다.** 단계 전환을 통해 지속됩니다.
- `m_SkillFilterType`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨** `SkillFilterType.m_id`는 ​​『옥토패스 트래블러: 대륙의 패자』 쪽 필터 UI 및 분류에 사용됩니다.

### `SkillAilmentOffsets`
모델별 상태 이상 앵커 사전 설정. 이 테이블은 `Skill/` 아래에 있지만 가장 확실한 다운스트림 OT0 소비자는 `SkillID` 자체가 아닌 `Enemy/EnemyTypeID.m_AilmentOffset`입니다.

- **OT0 행:** `853`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `922`.
- **OT0 SDK 행 구조:** `FSkillAilmentOffsetsBase`.
- 상태 이상 아이콘/효과는 적의 체형에 따라 앵커 위치가 달라야 하기 때문에 이 행이 존재합니다. OT0은 모든 상태 이상 계열 앵커 쌍을 포함하는 하나의 미리 설정된 행을 저장합니다.

#### 필드별
- `m_id`: **확인됨.** 오프셋 사전 설정 ID입니다. OT0 `Enemy/EnemyTypeID.m_AilmentOffset`가 여기를 가리킵니다.
- `m_PoisonX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 포이즌 앵커 X.
- `m_PoisonY`: **필드 이름 및 다운스트림 사용법으로 확인됨.** 해당 배틀러 모델/유형에 대한 사전 설정 행의 포이즌 앵커 Y.
- `m_BleedX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 앵커 X를 블리드합니다.
- `m_BleedY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 앵커 Y를 블리드합니다.
- `m_SilenceX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 앵커 X를 침묵시킵니다.
- `m_SilenceY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 앵커 Y를 침묵시킵니다.
- `m_SleepX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 수면 앵커 X.
- `m_SleepY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 수면 앵커 Y.
- `m_PalalysisX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 마비 앵커 X.
- `m_PalalysisY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 마비 앵커 Y는 해당 배틀러 모델/유형에 대한 사전 설정 행에 있습니다.
- `m_FearX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 공포 앵커 X.
- `m_FearY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 전투 모델/유형에 대한 사전 설정 행의 공포 앵커 Y.
- `m_WasteX`: **필드 이름 및 다운스트림 사용량으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 폐기물/SP-폐기물 앵커 X.
- `m_WasteY`: **필드 이름 및 다운스트림 사용량으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 폐기물/SP-폐기물 앵커 Y.
- `m_DarknessX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 전투 모델/유형에 대한 사전 설정 행의 어둠 앵커 X.
- `m_DarknessY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 전투 모델/유형에 대한 사전 설정 행의 어둠 앵커 Y.
- `m_HeadacheX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 두통 앵커 X.
- `m_HeadacheY`: **필드 이름 및 다운스트림 사용법으로 확인됨.** 두통 앵커 Y는 해당 배틀러 모델/유형에 대한 사전 설정 행에 있습니다.
- `m_EnergyDrainX`: **필드 이름 및 다운스트림 사용량으로 확인됨** 해당 전투 모델/유형에 대한 사전 설정 행의 에너지 배출 앵커 X.
- `m_EnergyDrainY`: **필드 이름 및 다운스트림 사용량으로 확인됨** 해당 전투 모델/유형에 대한 사전 설정 행의 에너지 배출 앵커 Y.
- `m_RegenerateX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 X를 재생성합니다.
- `m_RegenerateY`: **필드 이름 및 다운스트림 사용법으로 확인됩니다.** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 Y를 재생성합니다.
- `m_ProvokeX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 X를 유발합니다.
- `m_ProvokeY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 Y를 유발합니다.
- `m_CounterX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 카운터 앵커 X.
- `m_CounterY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 카운터 앵커 Y.
- `m_InterceptX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 X를 차단합니다.
- `m_InterceptY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 Y를 차단합니다.
- `m_BreakX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 X를 끊습니다.
- `m_BreakY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행에서 앵커 Y를 끊습니다.
- `m_CountDownX`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 카운트다운 앵커 X.
- `m_CountDownY`: **필드 이름 및 다운스트림 사용법으로 확인됨** 해당 배틀러 모델/유형에 대한 사전 설정 행의 카운트다운 앵커 Y.
- `m_PerfectInterceptScale`: **관찰됨.** 완벽한 절편 스타일 표시를 위한 추가 스케일 값입니다. OT0은 모든 행에 이를 채웁니다.

### `SkillAilmentSkillReplaceList`
소규모 "상태 이상 기반 스킬 교체" 보조 테이블입니다. OT0에서는 사실상 휴면 상태지만, 스키마 자체는 문서화할 가치가 있습니다.

- **OT0 행:** `1`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `12`.
- **OT0 SDK 행 구조:** `FSkillAilmentSkillReplaceListBase`.

#### 필드별
- `m_id`: 대체 패키지의 기본 키입니다.
- `m_SkillList`: **관찰됨.** 대체 대상으로 쓰이는 `SkillID` 행 목록입니다.
- `m_AilmentType`: **확인되었습니다.** 교체 패키지를 트리거하는 `SkillAilmentType.m_id`입니다.

### `SkillResistList`
인라인 백분율 기반 무기/요소 저항 페이로드. 이 테이블은 종종 `EnemyWeakID`와 혼동되지만 저장된 값은 서로 다른 시스템임을 증명합니다.

- **OT0 행:** `215`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `241`.
- **OT0 SDK 행 구조:** `FSkillResistListBase`.

#### 필드별
- `m_id`: 저항 페이로드 행의 기본 키입니다.
- `m_ResistWeapon`: **확인됨.** 무기 계열별 직접 백분율/델타 배열입니다. OT0 값에는 `50`, `27`, `22`, `12`, `2`, `-40`, `-50` 및 `-100`와 같은 항목이 포함되므로 이는 분명히 `ResistType` ID 배열이 **아닙니다**.
- `m_ResistMagic`: **확인됨.** `m_ResistWeapon`와 동일한 의미를 갖는 요소군별 직접 백분율/델타 배열입니다.

### `SkillResistAilmentID`
하나의 인라인 기본 비율 배열 대신 명시적인 상태 이상/비율 쌍을 원하는 스킬 및 적 행에 사용되는 패키지된 상태 이상 저항 행입니다.

- **OT0 행:** `74`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `60`.
- **OT0 SDK 행 구조:** `FSkillResistAilmentIDBase`.

#### 필드별
- `m_id`: 상태 이상 저항 패키지의 기본 키입니다.
- `m_ResistAilments`: **확인됨.** 패키지에 포함되는 최대 16개의 `SkillAilmentType.m_id` 행 목록입니다.
- `m_ResistRate`: **확인되었습니다.** `m_ResistAilments`에 대한 일치율 배열입니다. 배열은 병렬입니다.

### `SkillHitRateList`
재사용 가능한 다중 적중 및 반복 해결 확률 테이블입니다. `SkillAvailID.m_HitTypes`가 여기를 가리킵니다.

- **OT0 행:** `68`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `72`.
- **OT0 SDK 행 구조:** `FSkillHitRateListBase`.

#### 필드별
- `m_id`: 적중률 패키지의 기본 키입니다.
- `m_MinCount`: **관찰됨.** 무작위 연속이 고려되기 전 최소 보장 횟수입니다.
- `m_CountAilment`: **형태가 확인되었습니다.** 상태 이상/카운트 참조 채널이 추가되었습니다. OT0은 모든 행에서 0을 유지하지만 『옥토패스 트래블러: 대륙의 패자』는 이를 사용합니다.
- `m_HitRates`: **관찰됨.** 추가 적중 판정 또는 반복 해결 횟수를 제어하는 ​​16개 슬롯 확률 테이블입니다.
- `m_Shuffle`: **관찰됨.** 적중률 패턴에 대한 무작위화/셔플 동작.

### `SkillIconList`
작은 쌍을 이루는 아이콘 뱅크. 실제이지만 OT0의 `SkillID.m_SkillIconID`의 주요 대상은 아닙니다.

- **OT0 행:** `11`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `12`.
- **OT0 SDK 행 구조:** `FSkillIconListBase`.

#### 필드별
- `m_id`: 아이콘 쌍의 기본 키입니다.
- `m_BattleIconTexID`: **확인됨** 전투 중 아이콘용 `Texture/TextureID.m_id`입니다.
- `m_MenuIconTexID`: **확인됨** 메뉴 아이콘의 경우 `Texture/TextureID.m_id`입니다.

### `SkillUniqueIconID`
특수 명령 패널 또는 버프 표시를 위한 고유한 아이콘/텍스트 재정의 테이블입니다.

- **OT0 행:** `39`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `137`.
- **OT0 SDK 행 구조:** `FSkillUniqueIconIDBase`.
- OT0 `SkillID` 행은 실제로 이 테이블을 사용하지 않지만 테이블 자체는 완전히 채워지고 깔끔하게 구성됩니다.

#### 필드별
- `m_id`: 고유 아이콘 행의 기본 키입니다.
- `m_TextID`: **관찰됨.** 고유 아이콘의 텍스트 레이블입니다. 일부 OT0 ID는 `GameTextUI`(예: `65490 -> Hang Tough`)에서 완전히 확인되는 반면, 현재 내보낸 OT0 텍스트 뱅크에는 많은 OT0 ID가 없습니다. 해결되지 않은 하위 집합을 필드가 사용되지 않는다는 증거가 아닌 레거시/공유 콘텐츠로 처리합니다.
- `m_SkillIconTexID`: **확인되었습니다.** 고유한 아이콘 질감을 위한 `Texture/TextureID.m_id`입니다.
- `m_OverrideCommandPanel`: **관찰됨.** 명령 패널에서 이 고유 아이콘/텍스트를 사용하십시오.
- `m_OverrideInBattle`: **관찰됨.** 전투 UI에서 이 고유 아이콘을 사용하세요.
- `m_OverrideBuff`: **관찰됨.** 버프/상태 표시에 이 고유 아이콘을 사용합니다.

### `SkillSupplyItemGroup`
OT0 마을/보급 도우미 테이블입니다. 기술 opcode를 보상 복권 그룹에 바인딩합니다.

- **OT0 행:** `45`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** `FSkillSupplyItemGroupBase`.
- **관련 OT0 SDK 열거형:** `ESKILL_MODIFY_TYPE`.

#### 구체적인 OT0 예
- 첫 번째 가시적 재료군은 이미 패턴을 표시하기에 충분합니다. `SkillSupplyItemGroup#100/#101`는 `Timber Supply` / `Timber Supply+`를 가리키고 `#200/#201`는 `Top Timber Supply` / `Top Timber Supply+`를 가리킵니다.
- 돌, 천, 광석, 기타 마을 재료에도 동일한 패턴이 반복됩니다. 즉, 이 표는 일반적인 전투 보상 목록이 아닙니다. 발동 스킬에 따라 선별된 마을 보급품 복권입니다.
- OT0은 대부분의 행에 `m_SkillModifyType = 201`를 사용하며 이는 `ESKILL_MODIFY_TYPE`의 `ITEM_SUPPLY` 분기와 일치합니다.

#### 필드별
- `m_id`: 공급 품목 그룹의 기본 키입니다.
- `m_SkillId`: **확인되었습니다.** 이 복권 그룹을 발동시키는 스킬의 `SkillID.m_id`입니다.
- `m_SkillModifyType`: **열거/값 중복으로 확인됨** `ESKILL_MODIFY_TYPE`의 경제 opcode입니다. OT0은 주로 `201 ITEM_SUPPLY`를 사용하고 몇 개의 `208 FARM_HARVEST_ITEM_AMOUNT_LOTUP` 및 `254 ITEM_SUPPLY_LOT_IN_GROUP` 행을 사용합니다.
- `m_Lot`: **관찰됨.** 그룹 수준 복권 가중치 또는 승수. OT0은 모든 행의 `100`에 그대로 둡니다.

### `SkillSupplyItemList`
OT0 마을/보급품 추첨에 응모합니다. `SkillSupplyItemGroup`가 선택하는 페이로드 테이블입니다.

- **OT0 행:** `166`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** `FSkillSupplyItemListBase`.

#### 필드별
- `m_id`: 복권 응모를 위한 기본 키입니다.
- `m_GroupId`: **확인되었습니다.** `SkillSupplyItemGroup.m_id` 상위 그룹입니다.
- `m_RewardId`: **강력한 증거로 관찰됨** 보상 유형 판별자. OT0은 돈/휴가 항목에 `1000`를 사용하고 항목 보상 항목에 `1001`를 사용합니다.
- `m_ItemId`: **강력한 증거로 관찰됨** 아이템 보상의 경우 `Item/ItemList.m_id`, 금전 보상의 경우 `0`.
- `m_Values`: **관찰됨.** 보상 금액 또는 아이템 개수입니다. `m_RewardId = 1000`의 경우 이는 리프 양입니다. `1001`의 경우 품목 수량입니다.
- `m_Lot`: **관찰됨.** 상위 그룹 내 복권당 참가 가중치.
- `m_IsUnique`: **관찰됨.** 고유한 보상 플래그. OT0은 `4000` 그룹의 두 항목에만 이를 사용합니다.

### `SkillTargetShopList`
마을/무역 스킬을 위한 OT0 대상 상점 도우미 행입니다.

- **OT0 행:** `24`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** `FSkillTargetShopListBase`.
- **관련 OT0 SDK 열거형:** `ESHOP_TYPE`.

#### 이 테이블에서 사용된 정확한 OT0 SDK 열거형 인용문
##### `ESHOP_TYPE`
`GAMESTUFF/5.4.2-0+UE5-Octopath_Traveler0/CppSDK/SDK/Kingship_structs.hpp:6100`에서 인용되었습니다.
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
- `SkillTargetShopList.m_ShopType`는 원시 `ESHOP_TYPE` 숫자 값을 저장합니다. 도우미 행이 존재하므로 스킬은 상점 계열뿐만 아니라 스킬이 대상으로 삼아야 하는 구체적인 지도 컨텍스트도 바인딩할 수 있습니다.
- 이것이 OT0이 여관이나 대장간과 같은 일반 시스템과 `eVILLAGE_BASE` 및 `eTRADE`와 같은 마을별 도우미에 대해 동일한 열거형 패밀리를 사용할 수 있는 이유입니다.

#### 구체적인 OT0 예
- `#1..#16` 행은 `Savvy Shopper`, `Master Haggler`, `Savvy Seller`, `Master Salesman`, `Appraiser`, `Grand Appraiser`, `Wholesale Expert` 및 `Master Tradesman`와 같은 거래 가격 기술을 다룹니다. 모두 `m_ShopType = 22`, 즉 `eTRADE`를 사용합니다.
- `#17..#24` 행은 `Negotiator`, `Fixer`, `Cook` 및 `Grand Chef`와 같은 마을 기반 유틸리티 기술을 다룹니다. `m_ShopType = 17`, 즉 `eVILLAGE_BASE`를 사용합니다.
- 모든 원본 OT0 행은 `m_MapId = 900002`를 사용합니다. 이는 테이블이 범용 상점 카탈로그라기보다는 하나의 지도 컨텍스트 범위로 지정된 마을 시스템 도우미 테이블과 더 유사하게 읽히는 이유입니다.

#### 필드별
- `m_id`: 대상 상점 행의 기본 키입니다.
- `m_SkillId` : **확인되었습니다.** 상점 타겟팅 스킬의 `SkillID.m_id`입니다.
- `m_ShopType`: **확인되었습니다.** 대상 상점의 `ESHOP_TYPE` 선택기입니다. OT0은 `17 (eVILLAGE_BASE)` 및 `22 (eTRADE)`를 사용합니다.
- `m_MapId`: **확인됨.** `Map/MapListTable.m_id`는 대상 상점의 지도 컨텍스트를 제공합니다. OT0은 모든 행에 마을 기본 맵인 `900002`를 사용합니다.

### `SkillTargetFarmItemList`
특정 마을 농장 항목을 추가하거나 대상으로 하는 기술에 대한 OT0 목장/농장 도우미 행.

- **OT0 행:** `2`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** 대륙의 패자 `Skill/` 폴더에 없습니다.
- **OT0 SDK 행 구조:** `FSkillTargetFarmItemListBase`.
- **관련 OT0 SDK 열거형:** `ESKILL_MODIFY_TYPE`.

#### 구체적인 OT0 예
- 두 스톡 OT0 행 모두 `Sheep King` 제품군에 속합니다. `SkillTargetFarmItemList#1`는 `SkillID#900938 Sheep King`를 가리키고 `#2`는 `SkillID#903901 Sheep King+`를 가리킵니다.
- 두 행 모두 `m_VillageFarmItemId = 103`를 대상으로 합니다. 보조 ID `105` 및 `106`는 ​​원본 OT0에서 대부분 빈 자리 표시자로 존재하므로 패턴을 복제할 때 이 필드를 주의해서 처리해야 합니다.

#### 필드별
- `m_id`: 팜 대상 행의 기본 키입니다.
- `m_SkillId` : **확인되었습니다.** 농장 타겟팅 스킬의 `SkillID.m_id`입니다.
- `m_SkillModifyType`: **확인됨.** OT0은 `ESKILL_MODIFY_TYPE`의 `RANCH_APPEND_ITEM`인 `215`를 사용합니다.
- `m_VillageFarmItemId`: **확인되었습니다.** 주요 대상 팜 아이템의 `Village/VillageFarmItem.m_id`입니다.
- `m_AdditionalVillageFarmItemId`: **확인됨.** 보조 `Village/VillageFarmItem.m_id`. OT0에서 참조된 행은 빈 자리 표시자(`105` 및 `106`)이므로 이 필드에 이미 원본 OT0에 의미 있는 라이브 콘텐츠가 있다고 가정하지 마십시오.

### `SkillVisitorList`
방문자/소환 도우미 행. OT0는 테이블을 거의 사용하지 않지만 하나의 라이브 행만으로도 의도한 모양을 보여주기에 충분합니다.

- **OT0 행:** `2`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `7`.
- **OT0 SDK 행 구조:** `FSkillVisitorListBase`.

#### 구체적인 OT0 예
- OT0에는 0이 아닌 실시간 방문자 행(`SkillVisitorList#1`)이 하나만 있습니다.
- 해당 행은 적 라벨이 `Summon_PC072_Linde`인 `m_EnemyID = 33000`를 소환합니다.
- 배치 데이터도 사소한 필러가 아닙니다. `m_OffsetsX[0] = -500.0` 및 `m_IconID = 1000`는 방문자 테이블이 소환 스타일 게스트의 생성 레이아웃과 UI ID를 모두 처리하고 있다는 강력한 힌트입니다.

#### 필드별
- `m_id`: 방문자 행의 기본 키입니다.
- `m_EnemyID`: **OT0에서 확인되었습니다.** 방문자 배틀러의 `Enemy/EnemyID.m_id`입니다. OT0의 0이 아닌 라이브 행은 `33000`, 즉 `Summon_PC072_Linde`를 사용합니다.
- `m_EscortNum`: **관찰됨.** 슬롯별 호위 카운트 배열.
- `m_EscortType`: **관찰됨.** 슬롯별 에스코트/레이아웃 유형 배열. OT0은 0으로 둡니다.
- `m_OffsetsY`: **관찰됨.** 방문자 배치를 위한 슬롯별 Y 오프셋입니다.
- `m_OffsetsX`: **관찰됨.** 방문자 배치를 위한 슬롯당 X 오프셋입니다.
- `m_CallSE`: **관찰됨.** 슬롯별 호출음 ID입니다. OT0은 0으로 둡니다.
- `m_IconID`: **해결되지 않음.** 방문자 UI 아이콘 식별자입니다. OT0은 라이브 행에서 `1000` 값만 사용하며 대상 테이블은 현재 내보내기로 입증되지 않습니다.

#### 『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마 변경
- `m_EnemyType`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨** 『옥토패스 트래블러: 대륙의 패자』는 직접적인 OT0 스타일 `EnemyID` 대신 여기에 적 유형을 저장합니다.

### `SkillEnhancedList`
강화 도우미 테이블. OT0은 사실상 공백으로 남겨두지만 스키마는 간단합니다.

- **OT0 행:** `1`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `10`.
- **OT0 SDK 행 구조:** `FSkillEnhancedListBase`.

#### 필드별
- `m_id`: 강화 패키지의 기본 키입니다.
- `m_EnhancedSkills`: **관찰됨.** 강화 후 스킬 ID 목록입니다. OT0에는 0행만 있어서 직접 참조할 기본 콘텐츠가 없습니다.

### `SpecialSkillID`
특수스킬 마스터키 테이블입니다. OT0은 여기에 ID만 저장합니다. 실제 레벨별 매핑은 `SpecialSkillGrowthList`에 있고 플레이어 바인딩은 `Character/CharaPlayerSpecialSkillList`에 있습니다.

- **OT0 행:** `86`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `266`.
- **OT0 SDK 행 구조:** `FSpecialSkillIDBase`.

#### 필드별
- `m_id`: 특수 스킬 계열의 기본 키입니다.

### `SpecialSkillGrowthList`
특수 기술 계열에서 해당 수준에 부여된 구체적인 `SkillID` 행으로의 수준별 매핑입니다.

- **OT0 행:** `497`.
- **『옥토패스 트래블러: 대륙의 패자』 행 수:** `5301`.
- **OT0 SDK 행 구조:** `FSpecialSkillGrowthListBase`.

#### 필드별
- `m_id`: 성장 행의 기본 키입니다.
- `m_SpecialSkillID` : **확인되었습니다.** 특수 스킬 계열의 `SpecialSkillID.m_id`가 성장하고 있습니다.
- `m_Level`: **확인됨.** 성장 수준. OT0은 일반적으로 플레이어 대상 EX/특수 기술에 `1..3`를 사용하지만 파일 자체에는 최대 `10` 레벨까지의 행이 포함되어 있습니다.
- `m_SkillID`: **확인됨.** `SkillID.m_id`는 ​​해당 특수 기술 레벨에서 부여되거나 대체됩니다.
- `m_DisplayPriority`: **관찰됨.** 성장 행 간의 UI 정렬 키입니다. OT0은 대부분 `0`를 유지하지만 `20..90` 값도 사용합니다.

#### 대륙의 패자 전용 추가 필드
- `m_InitialGaugeValue`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 특수 기술 레벨의 시작 게이지입니다.
- `m_GaugeRegenerateValue`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 주기/회전당 게이지 재생성.
- `m_CountLimit`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 사용량 한도.
- `m_RewardTypes`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 성장 행에 대한 보상 유형 배열입니다.
- `m_ItemIDs`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** `m_RewardTypes`와 병렬로 아이템 ID를 보상합니다.
- `m_ItemCounts`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 보상은 보상 배열과 병렬로 계산됩니다.
- `m_AvailValues`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인되었습니다.** 기본 제공 로직에 전달된 수준별 조정 값입니다.
- `m_AvailHitTypes`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 레벨별 적중 유형이 재정의됩니다.
- `m_AilmentValues`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 레벨별 상태 이상 효능이 재정의됩니다.
- `m_AilmentTurns`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 레벨별 상태 이상 기간이 재정의됩니다.
- `m_DamageLimitUp`: **『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마에서 확인됨.** 레벨당 피해 한도가 증가합니다.

### `ClassSkillConditionList`(『옥토패스 트래블러: 대륙의 패자』 전용)
클래스 스킬 사용 조건에 대한 대륙의 패자 전용 도우미 표입니다. 여기에 제공된 SDK 덤프에서 일치하는 전용 OT0 SDK 행 구조를 찾을 수 없으므로 아래 필드 의미는 『옥토패스 트래블러: 대륙의 패자』 쪽 DB 스키마 및 필드 이름에서 파생됩니다.

#### 필드별
- `m_id` : 클래스 스킬 조건 열의 기본 키입니다.
- `m_UseCondition`: **필드 이름 및 대륙의 패자 역할에서 관찰됩니다.** 클래스 스킬 시스템에 의해 평가된 조건 ID입니다.
- `m_ConditionText`: **필드 이름 및 대륙의 패자 역할에서 관찰됩니다.** 조건에 대해 플레이어에게 표시되는 텍스트 ID입니다.

### `SkillEffectiveLinkageList`(『옥토패스 트래블러: 대륙의 패자』 전용)
대륙의 패자 전용 체인 프레젠테이션 도우미. 이 테이블은 `SkillEffectiveID.m_SkillEffectiveLinkageID`에서 참조됩니다.

#### 필드별
- `m_id`: 연결 행의 기본 키입니다.
- `m_HoldTime`: 연결된 프레젠테이션 그룹의 총 대기 시간입니다.
- `m_AvailTime`: 연결된 프레젠테이션 그룹의 Avail/해결 시간입니다.
- `m_OffsetX`: 연결된 프리젠테이션 배치를 위한 그룹 수준 X 오프셋.
- `m_OffsetY`: 연결된 프리젠테이션 배치를 위한 그룹 수준 Y 오프셋입니다.
- `m_LimitMax`: 연결 그룹에 대해 관찰된 상한 필드입니다.
- `m_LinkageType`: 관찰된 연결 모드 선택기.
- `m_Effectives`: **ID 중복으로 확인됨** 이 연결 패키지에서 방출되는 최대 16개의 `SkillEffectiveID.m_id` 행입니다.

### `SkillFilterCategory`(『옥토패스 트래블러: 대륙의 패자』 전용)
대륙의 패자 기술 필터 UI에 대한 대륙의 패자 전용 필터 범주 범례입니다.

#### 필드별
- `m_id`: 필터 카테고리의 기본 키입니다.
- `m_Name`: 카테고리 이름에 대한 텍스트 ID입니다.
- `m_Priority`: 카테고리의 UI 정렬 우선순위입니다.

### `SkillFilterType`(『옥토패스 트래블러: 대륙의 패자』 전용)
대륙의 패자 전용 필터 유형 범례. `SkillID.m_ForcedSkillFilter` 및 `m_ExclusionSkillFilter`가 여기를 가리키며 각 필터 유형은 필터 카테고리에 속합니다.

#### 필드별
- `m_id`: 필터 유형의 기본 키입니다.
- `m_Name`: 필터 이름의 텍스트 ID입니다.
- `m_Priority`: 필터 유형의 UI 정렬 우선순위.
- `m_Category`: **확인되었습니다.** `SkillFilterCategory.m_id` 상위 카테고리입니다.

### `SkillModifyType`(『옥토패스 트래블러: 대륙의 패자』 전용)
Opcode 수정부터 유형 필터링까지 대륙의 패자 전용 브리지 테이블입니다. 행 ID는 『옥토패스 트래블러: 대륙의 패자』에서 사용하는 0이 아닌 `SkillAvailID.m_ModifyType` 값과 정확하게 일치합니다.

#### 필드별
- `m_id`: **확인되었습니다.** `SkillAvailID.m_ModifyType`에 해당하는 opcode 키를 수정하세요.
- `m_SkillFilterType`: **ID 중복으로 확인됨** 수정 opcode와 연결된 최대 5개의 `SkillFilterType.m_id` 행.

### `SpecialSkillExtendCategory`(『옥토패스 트래블러: 대륙의 패자』 전용)
특수 스킬 확장 카테고리에 대한 대륙의 패자 전용 범례표입니다.

#### 필드별
- `m_id`: 확장 카테고리의 기본 키입니다.
- `m_Name`: 카테고리 이름의 텍스트 ID입니다.

### `SpecialSkillExtendType`(『옥토패스 트래블러: 대륙의 패자』 전용)
확장 효과 유형에 대한 대륙의 패자 전용 범례입니다. `SpecialSkillExtendList.m_ExtendType`가 여기를 가리킵니다.

#### 필드별
- `m_id`: 확장 유형에 대한 기본 키입니다.
- `m_Category`: **확인되었습니다.** `SpecialSkillExtendCategory.m_id`.
- `m_SubCategory`: 상위 카테고리 내에서 관찰된 하위 유형 선택기입니다.
- `m_EffectName`: 효과 이름의 텍스트 ID입니다.
- `m_DetailText`: 효과 세부정보/설명의 텍스트 ID입니다.
- `m_EffectValue`: 이 확장 유형에 대한 기본 숫자 페이로드입니다.
- `m_SubEffectValue`: 이 확장 유형에 대한 보조 숫자 페이로드입니다.
- `m_AutoLearn`: 자동 잠금 해제 플래그.
- `m_SubText`: 주 효과 이름/세부 사항과 함께 사용되는 추가 텍스트 ID입니다.

### `SpecialSkillExtendList`(『옥토패스 트래블러: 대륙의 패자』 전용)
대륙의 패자 전용 특수 스킬별 확장 매핑입니다. 이것은 특수 스킬이 어떤 확장 유형을 얻게 되는지, 어떻게 잠금 해제되는지, 어떤 보상이 번들로 제공되는지를 알려주는 페이로드 테이블입니다.

#### 필드별
- `m_id`: 확장 행의 기본 키입니다.
- `m_TargetSSkill`: **확인됨** 확장 프로그램의 대상이 되는 `SpecialSkillID.m_id`입니다.
- `m_ExtendType`: **확인되었습니다.** 확장 효과를 설명하는 `SpecialSkillExtendType.m_id`입니다.
- `m_GetPlaceText`: 플레이어에게 확장 프로그램을 어디서/어떻게 얻었는지 알려주는 텍스트 ID입니다.
- `m_PriorityItemRewardType`: 우선 보상 유형 코드입니다.
- `m_PriorityItemID`: 우선 보상 아이템 ID입니다.
- `m_PriorityItemNum`: 우선 보상 아이템 개수입니다.
- `m_RewardTypes`: 일반 보상형 배열입니다.
- `m_ItemIDs`: `m_RewardTypes`와 평행한 보상 아이템 ID입니다.
- `m_ItemCounts`: 보상은 보상 배열과 평행하게 계산됩니다.
- `m_OpenTimestamp`: 확장 프로그램 열기/잠금 해제를 위한 타임스탬프 게이트입니다.
- `m_AddSSkillLv`: 이 확장 행에 의해 부여되거나 요구되는 추가 특수 기술 레벨입니다.

## `Skill/`를 직접 공급하는 관련 폴더

### `Character/`
- ``JobID.m_BaseAttackSkill` and `m_SubAttackSkill``: **확인됨.** 직접 `SkillID.m_id` 참조입니다. 예를 들어 OT0 `JobID#1`(워리어)는 기본 공격 스킬 `700`와 서브 공격 스킬 `724`를 사용합니다.
- ``WeaponAnimationSet``: **확인됨.** `WeaponType.m_AnimationSet`가 여기를 가리킵니다. 이는 무기 계열에서 전투 애니메이션 계열로의 연결 고리입니다.
- ``CharaStatusID``: **확인됨** `SkillAvailID.m_ModifyStatus`, `SkillAilmentType.m_TargetStatus` 및 `SkillAilmentType.m_AbsorbHealStatus`는 모두 여기를 가리킵니다.
- ``CharaGuest``: **확인됨** `SkillAvailID.m_SummonGuestID`는 게스트 소환 동작에 대해 여기를 가리킵니다.
- ``CharaPlayerSpecialSkillList``: **확인됨.** 이는 플레이어를 `SpecialSkillID` 제품군에 바인딩합니다. OT0에는 `48` 행이 있습니다. `44` 고유 `m_SpecialSkillID` 값은 `Skill/SpecialSkillID`로 완전히 확인됩니다.

### `Enemy/`
- ``EnemyWeakID``: **확인되었으며 중요합니다.** 이것이 `SkillAvailID.m_ResistID`가 가리키는 것입니다. `EnemyWeakID` 내부 배열은 `ResistType` ID를 저장합니다. 이것이 바로 `m_ResistID`가 "`SkillResistList`에서 백분율 페이로드 추가"가 아니라 "약점 테이블 교체"를 의미하는 이유입니다.
- ``EnemyWeakChangeID``: **확인됨** `SkillAvailID.m_WeakChangeID`는 단계적 약점 변경에 대해 여기를 나타냅니다.
- ``EnemyWeakLockID``: **확인됨.** `SkillAvailID.m_WeakLockID`는 약점 슬롯 잠금을 위해 여기를 가리킵니다.
- ``EnemyReinforcements``: **확인됨.** `SkillAvailID.m_Reinforcement`는 강화 소환 테이블을 위해 여기에 포인트를 줍니다.
- ``EnemyModeID``: **확인됨.** `SkillAvailID.m_ModeChangeID`는 적 모드/단계 변경에 대해 여기를 가리킵니다.
- ``EnemyTypeID.m_AilmentOffset``: **확인됨.** `Skill/SkillAilmentOffsets.m_id`를 가리킵니다. 이것이 `SkillID`가 직접적으로 지정하지 않더라도 상태 이상 오프셋 사전 설정 테이블이 스킬 생태계에 속하는 이유입니다.

### `Battle/`
- ``BattleActionID``: **확인됨** `SkillEffectiveID.m_Animations[*]` 및 `SkillAilmentType.m_ActionID`는 여기를 가리킵니다.
- ``BattleCameraParams``: **확인됨.** OT0 `SkillEffectiveID.m_Cameras[*]`가 여기를 가리킵니다.

### `Effect/`, `Sound/` 및 `Texture/`
- ``Effect/AttachEffect``: **확인됨.** `SkillID.m_AttachEffect`가 여기를 가리킵니다.
- ``Effect/EffectList``: **확인됨** `SkillEffectiveID.m_Effects[*]` 및 여러 `SkillAilmentType` 효과 필드가 여기를 가리킵니다.
- ``Sound/SoundList``: **확인됨.** `SkillEffectiveID.m_Sounds[*]` 및 `SkillAilmentType` 음장은 여기를 가리킵니다.
- ``Sound/PartVoiceID``: **확인됨.** `SkillID.m_VoiceID` 및 `SkillEffectiveID.m_Voices*`는 ​​여기를 가리킵니다.
- ``Texture/TextureID``: **확인됨.** `SkillID.m_SkillIconID`, `WeaponType`/`MagicType` 아이콘 필드, `SkillIconList`, `SkillUniqueIconID` 및 상태 이상 아이콘 필드가 모두 여기에서 해결됩니다.

### `GameText/`
- ``GameText/SystemText/GameTextSkill` and localized `GameText/Localize/*/SystemText/GameTextSkill``: **확인되었습니다.** `SkillID.m_Name`, `SkillID.m_Detail` 및 대부분의 `SkillEffectiveID.m_Texts`에 대한 기본 텍스트 뱅크입니다.
- ``GameText/SystemText/GameTextUI` and localized `GameText/Localize/*/SystemText/GameTextUI``: **확인되었습니다.** `WeaponType.m_TextID`, `MagicType.m_TextID`, 대부분의 `SkillAilmentType.m_TextID`/`m_ExplanationTextID` 및 일부 `SkillID.m_Name` 이상값에 대한 기본 텍스트 뱅크입니다.

### `AIBattle/` 적 스킬 파이프라인
- `Basic chain`: **확인됨.** 일반 적의 경우 체인은 `EnemyID.m_SkillsID -> AIBattle/TacticalSkillList.m_id`, `EnemyID.m_TacticalAssignID -> AIBattle/TacticalAssignList.m_id`, `TacticalAssignList.m_Tactics[*] -> TacticalList.m_id` 및 `TacticalActionList.m_SkillIndex`이며 소유한 `TacticalSkillList.m_UseSkills` 배열로 인덱싱됩니다.
- `Concrete example 1`: **확인됨.** `EnemyID#32300`는 `m_SkillsID = 55000` 및 `m_TacticalAssignID = 30500`를 사용합니다. `TacticalSkillList#55000`에는 `610001`, `610008`, `610009`와 같은 스킬 ID가 포함되어 있으며 `TacticalActionList`는 `m_SkillIndex = 0/1/2`를 통해 선택합니다.
- `Concrete example 2`: **확인됨.** `EnemyID#834`는 `m_SkillsID = 752` 및 `m_TacticalAssignID = 3`를 사용합니다. `TacticalSkillList#752`에는 `100405`, `100406`, `100407` 및 인덱스별로 선택되는 전술 행이 포함됩니다.
- `Important exception`: **확인됨.** OT0에는 `m_SkillsID = 0`가 있지만 0이 아닌 `m_TacticalAssignID`가 있는 적도 있습니다(예: 일부 Cait 및 야생 동물 행). 따라서 모든 적의 행동 경로가 단순한 전술-기술 목록 조회인 것은 아닙니다.
