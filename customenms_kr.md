### **LANGUAGE:**  [English](customenms_en.md) | [日本語](customenms_jp.md) | [中文](customenms_zh.md) | [한국어](customenms_kr.md)

『옥토패스 트래블러 0』에 새로운 적과 전투를 추가하는 과정은 주로 `AIBattle`, `Enemy`, 그리고 경우에 따라 `Skill`, `Texture`, `Battle` 아래의 DataBase 파일을 편집하는 데에 달려 있습니다. 아래에서는 관련된 모든 파일을 설명합니다. 제목 앞의 별(*) 개수는 “해당 파일을 편집해야 할 필요성”의 정도를 의미합니다(별이 적을수록 더 특수한 상황에서만 사용됨). 다만 어떤 목적에 어떤 파일을 수정해야 하는지 파악하기 위해, 전체 글을 읽는 것을 권장합니다.

## `Enemy/` 아래
### (***) `EnemyFormations`
필드(전투 화면)에 배치될 새로운 적 진형(Formation)을 지정할 수 있습니다. `m_PositionID`는 `BattlePositions.m_id`를 입력으로 받습니다(`BattlePositions`는 `Battle/` 아래에 있음). 많은 적이 등장하는 커스텀 전투를 만들 때는, `m_PositionID`에 0이 아닌 값이 충분히 들어 있는 기존 진형을 찾아 쓰거나 직접 만드는 것이 좋습니다. 또한 전투 맵에 따라 동일한 `PositionID`라도 적의 위치가 크게 달라질 수 있습니다.

### (***) `EnemyBattleAnimSet`
CotC에서는 적에게 특정 Flipbook 에셋을 가리키는 ID를 할당하는 반면, OT0에서는 이러한 에셋을 가리키는 ID들의 “세트”를 사용합니다(`m_EnemyTextureIDs`는 `Texture` 아래 `EnemyTexID`의 `m_id`를 입력으로 받음).
일반적으로 `m_EnemyTextureIDs[0]`는 스프라이트의 대기(Idle) 애니메이션(접미사 `Idl00`)을 가리키고, `m_EnemyTextureIDs[1]`부터 `m_EnemyTextureIDs[3]`까지는 공격 애니메이션(접미사 `Atk00`~`Atk02`)을 가리킵니다. 최소한 대기 애니메이션용 플립북 에셋은 추가해야 합니다(즉, `EnemyTexID`에 새 경로를 가리키는 엔트리를 추가하고, 여기서 새로 추가하는 엔트리의 `m_EnemyTextureIDs[0]`에 그 새 엔트리의 `m_id`를 넣는 방식). 나머지는 선택 사항입니다.

주의: CotC의 `EnemyType` 아래 `m_FlipbookID`는 이 배열의 추가 요소들을 OT0와 다르게 사용하는 것으로 보입니다. 인덱스 0이 대기 텍스처를 가리키는 점은 동일하지만, CotC는 일부 적에게 인덱스 1에 “보이지 않는(invisible)” 텍스처를 할당합니다(예: Shadow of Twin Worlds의 Shadow).

NPC 텍스처를 사용하는 전투는 `EnemyBattleAnimSet`에 별도의 엔트리를 만들 필요가 **없습니다**. NPC는 보통 이미 전투용 스프라이트시트(대개 `_B` 접미사)를 가지고 있기 때문입니다. `EnemyType`(아래 참조)에서 NPC의 `CharaID`를 지정하기만 하면 됩니다(필요하면 [커스텀 캐릭터 추가 방법](customchars_kr.md) 도 참고).

### (**) `EnemyParts`
커스텀 보스가 여러 파트로 구성되어 있다면(예: Or'Galdera, Amatsukami no Orochi) 각 파트를 이 파일에 지정해야 합니다. 다만 Shadow of Twin Worlds처럼 이 파일을 전혀 사용하지 않는 경우도 있습니다.

### (****) `EnemyTypeID`
이 에셋은 OT0에서 적 아키타입(archetype)에 대한 “전투 스프라이트 표시(프레젠테이션) 마스터 기술자(descriptor)”에 해당합니다. 게임에 다음을 알려줍니다:
- 어떤 애니메이션 세트를 사용할지, 또는 NPC/캐릭터 스프라이트시트로 연동할지 (`m_AnimationSetIDs`는 `EnemyBattleAnimSet`의 `m_id`를 입력으로 받고, `m_NpcCharaID`는 `Character/` 아래 `CharaID`의 `m_id`를 입력으로 받음)
- 스프라이트 및 각종 오버레이의 스케일/위치:
  - 히트 스파크(hit sparks) (`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`)
  - 실드 및 약점 오버레이
  - 적 이펙트(`m_EffectOffsetX`,`m_EffectOffsetY`, 그리고 `m_EffectFromBottomOffsetY`: 스프라이트 하단 기준 오프셋. 바닥 기반 이펙트에서 흔함)
  - 데미지 숫자(`m_DamageOffsetX`,`m_DamageOffsetY`)
  - 상태 이상(`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`)
  - 커서(`m_CursorOffsetX`,`m_CursorOffsetY`)
- 해당 적 아키타입에 연결할 적 파트(`m_PartsID`)
- 그 적 타입에 사용할 사운드/보이스:
  - `m_CallSE`(즉 “호출 SE / call sound effect”)
  - `m_VoiceSymbol`(정수 값. `Sound` 아래 `VoiceSymbolList`를 인덱싱하는 것으로 보임)
  - `m_CueSheet`(아마 `Sound/` 아래 `SoundSheetList`를 인덱싱하며, 해당 적의 보이스 세트가 사용할 cuesheet를 선택)
- 어떤 부착 이펙트(VFX)를 어디에 생성할지 (`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`)
- 행동 순서 아이콘(턴 오더/타임라인 아이콘)에 사용할 텍스처 위치(보통 플립북/캐릭터 스프라이트시트의 첫 프레임)를 지정 (`m_OrderTexUS`, `m_OrderTexVS`; 큰 아이콘—해당 캐릭터가 행동 중일 때—는 `m_OrderTexUL`, `m_OrderTexVL`)하고, 아이콘에서의 렌더 스케일을 지정 (`m_OrderScaleS`; `m_OrderScaleL`). 예를 들어 Shadow of Twin Worlds 전투의 Shadow는 행동 순서 아이콘에 몸 전체가 보이도록 매우 작은 order scale을 사용합니다.
- 기타:
  - `m_OverrideOrderIconAnimSetID`: 행동 순서 아이콘에 특수 텍스처 에셋을 쓰고 싶을 때
  - `m_AttachEffectInGodbeastProduction`: OT0와 CotC 모두에서 미사용. 신수(신수 연출) 이펙트 관련
  - `m_AddStartUV`: 시작 UV 오프셋(위치)을 더할지 여부
  - `m_LinkOwnerAnim`: true이면 파트/부착물이 본체의 애니메이션 타이밍을 따라갈 수 있음
  - `m_DefeatEffect`: 사망 시 재생할 Effect ID(예: Bestower of All의 특수 처치 이펙트)
  - `m_ChangeColorID`: 컬러 변경 테이블에 대한 ID. `Character/` 아래 `CharaColorChangeParams`와 연관되었을 수 있습니다. OT0에서는 Zero/링베어러 초즌이 자신의 그림자와 맞설 때(`ChangeColorID` 6)만 사용됩니다. CotC의 `EnemyType`에는 이 키가 아예 없습니다.

### (**) `EnemyWeakID`
적에게 할당할 수 있는 다양한 약점(내성) 프로필을 관리합니다. `m_ResistWeapon`과 `m_ResistMagic`은 `Skill/` 아래 `ResistType`의 `m_id`를 입력으로 받습니다. `ResistType`이 1이면 지정된 무기/마법 타입을 약점으로도 추가합니다. 지정된 무기 및 마법 타입의 순서는 `Skill/` 아래 `WeaponType`/`MagicType`의 `m_id` 순서를 따르는 것으로 보입니다. 즉:
```
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
              0 (무속성 물리 데미지. 다른 무기 타입은 카운트되지 않음. 분석 후에도 게임 내에서는 ?로 표시됨)
            ],
            "m_ResistMagic": [
              0, ("None", 약점 아이콘도 표시되지 않는 것으로 보임)
              0, (Fire)
              0, (Ice)
              0, (Lightning)
              0, (Wind)
              0, (Light)
              0, (Dark)
              0, (무속성 마법 데미지. 다른 마법 타입은 카운트되지 않음)
              0 (Poison: 중독된 적이 받는 독 데미지가 실드를 1 깎음)
            ]
          }
```
**주의:** OT0는 한 적에게 8개를 초과하는 약점을 표시할 수 없습니다.

### (**) `EnemyWeakChangeID`
적의 약점이 어떻게 변하는지(약점 변화)를 추적합니다. `m_WeakIndices`는 `m_EnemyWeakID.m_id`를 입력으로 받으며, 이 약점 변화의 `m_id`는 예를 들어 avail에 할당할 수 있습니다([커스텀 스킬 및 avail 추가 방법](customskls_kr.md)도 참고).

### (**) `EnemyWeakLockID`
적의 약점 잠금(weakness lock) 방식을 추적합니다. 무속성 마법 데미지 및 독 데미지를 잠그는 필드는 존재하지 않는 것으로 보입니다.

### (****) `EnemyID`
게임의 적 마스터 레코드입니다: 스탯/약점/보상/UI 플래그를 정의하고, 프레젠테이션(애니메이션 세트, 텍스처, 오프셋, 보이스, 이펙트)을 위해 `EnemyTypeID`를 참조하며, 행동과 스킬셋은 `TacticalAssignList` 및 `TacticalSkillList`를 참조합니다(아래 “`AIBattle` 아래” 참조).
- `m_Label`: 적의 라벨. 내부적으로 사용될 수 있으므로 라벨이 충돌하는 새 엔트리는 추가하지 않는 것이 좋습니다
- `m_DisplayName`: `GameTextEnemy.m_id`를 참조하는 ID(모든 로컬라이즈 공통)
- `m_WeakID`: `EnemyWeakID.m_id`를 참조하는 ID(위 참조)
- `m_DisplayLevel`: 표시 레벨. 안타깝게도 `m_LevelUI`를 true로 해도 표시되지 않습니다
- `m_BreakRate`: 브레이크 상태에서 적에게 주는 데미지에 곱해지는 배율(퍼센트)
- `m_DamageRate`: 브레이크가 아닐 때 적에게 주는 데미지에 곱해지는 배율(퍼센트)
- `m_MaxSLD`: 적의 실드 수. 키 이름은 최대값처럼 보이지만, 일부 전술/스킬은 이를 더 높은 값으로 설정할 수 있습니다(예: Lucian the Glorious)
- `m_MaxHP`: 적의 HP. 마찬가지로 스킬/전술로 증가할 수 있습니다
- `m_MaxSP`: 적의 SP. 대부분의 적 스킬은 SP를 0 사용하므로, 이 스탯은 특정 적 스킬의 사용 횟수를 제한하는 데 주로 쓰입니다
- `m_AtkP`: 적의 물리 공격
- `m_AtkM`: 적의 속성 공격
- `m_DefP`: 적의 물리 방어
- `m_DefM`: 적의 속성 방어
- `m_Agi`: 적의 속도/민첩
- `m_Crt`: 적의 치명타 스탯
- `m_CrtDef`: 적의 치명타 방어 스탯(이 스탯은 적만 가진 것으로 보임)
- `m_Hit`: 적의 명중
- `m_Avd`: 적의 회피
- `m_ResistAilmentID`: 적에게 상태 이상 저항을 추적하는 ID를 할당(`Skill/` 아래 `SkillResistAilmentID` 참조)
- `m_ResistAilment`: 특정 상태 이상에 대한 저항률 배열. 할당된 `ResistAilmentID`의 `m_ResistAilments` 배열이 가득 찼을 때 쓰이는 것일 수 있습니다. Cat(캐트) 계열만 사용하는 것으로 보입니다.
- `m_TacticalAssignID`: 적에게 `TacticalAssignList.m_id`를 할당(전투에서 사용하는 전술 목록) — 아래 “`AIBattle` 아래” 참조
- `m_SkillsID`: 적에게 `TacticalSkllList.m_id`를 할당(전투에서 사용하는 스킬 목록) — 아래 “`AIBattle` 아래” 참조
- `m_IsBoss`: 많은 보스에서 쓰이지만 마을 NPC에도 존재합니다. 기능적으로 무엇을 하는지는 불명
- `m_Exp`, `m_Money`, `m_JP`: 처치 시 획득 EXP/리프/JP
- `m_PetExp`: CotC와 OT0 모두에서 항상 -1. -1이 아닌 값도 효과가 없는 것으로 보입니다
- `m_DropReward`: 적이 드롭할 수 있는 아이템(`EnemyDropID`에서 유효 ID 확인)
- `m_StealReward`: 훔칠 수 있는 아이템(`Item/` 아래 `ItemList`에서 유효 ID 확인)
- `m_StealLeafNum`: 훔칠 수 있는 리프(Leaves) 양
- `m_StatusOffset`: 적 상태바(약점 및 실드 수) 오프셋
- `m_Bottle`: 의미 불명. OT0는 1~3을 사용. 값 3은 OT0에서 Wappa만 사용. CotC는 4와 5도 사용
- `m_NameUI`: UI에 적 이름을 표시할지 여부
- `m_LevelUI`: UI에 `m_DisplayLevel`을 표시할지 여부(미사용/비기능)
- `m_HpUI`: OT0에서는 비기능
- `m_ShieldUI`: true이면 적의 실드 아이콘이 어둡게 표시되는 것으로 보임
- `m_RaceIndices`: 적에게 할당된 종족. 데미지 계산에 영향을 줄 수 있음
- `m_DisplaySpecialSkillGauge`: 적의 BP 카운터 표시 플래그
- `m_GenerateSpecialSkillValueOrverTurn`: 턴당 적이 생성하는 BP 포인트
- `m_ReduceSpecialSkillValueBreak`: 브레이크 후 적이 잃는 BP 포인트
- `m_MaxSpecialSkillValue`: 적의 최대 BP 포인트. 20을 초과하면 게임이 크래시합니다
- `m_OutsideTarget`: 적을 타겟팅할 수 없으며, 필드에 그 적만 남아 있으면 파티도 행동을 스킵합니다(Bestower of All이 반지가 부서지기 전까지 사용)
- `m_NotAction`: true이면 적이 행동하지 못함(Bestower of All이 모든 반지가 처치되기 전 사용)
- `m_UnknownLv`: OT0에서는 미사용
- `m_Immortal`: true이면 적 HP가 1 아래로 내려가지 않음

### (**) `EnemyReinforcements`
전투 중 추가 적(증원)을 스폰할 수 있게 하는 보조 테이블로, 어떤 적을 어떤 위치에 등장시킬지를 정의합니다.
- `m_Reinforcements`는 `EnemyID.m_id`를 입력으로 받음
- `m_Positions`는 `BattlePositions.m_id`를 입력으로 받음(해당 적 그룹 진형에 그 위치가 명시되어 있을 필요는 없음)
`EnemyReinforcements.m_id`를 사용하는 유일한 에셋은 `Skill/` 아래 `SkillAvailID`입니다.

### (****) `EnemyGroups`
모든 적 그룹을 기록합니다. 전투가 게임 내에서 트리거되려면 여기에 엔트리를 추가해야 합니다.
- `m_Label`: 새로 추가하는 적 그룹의 라벨이 고유하며 기존과 충돌하지 않도록 하세요. 이벤트는 이 라벨을 사용해 적 그룹과의 전투를 트리거합니다. 몬스터 아레나에 엔트리를 추가할 계획이라면, 그 엔트리를 다른 용도로 재사용하면 처치 후 몬스터 아레나 메뉴가 뜨거나 크래시를 유발할 수 있습니다
- `m_Inescapable`: 전투에서 도망칠 수 있는지 결정
- `m_DisableRetire`: OT0에서는 항상 false이며 OT0에서는 기능하지 않는 것으로 보임. CotC에서 전투 중 휴대폰을 일시정지할 수 있는 기능과 연관이 있을 수도?
- `m_SkipResult`: 전투 결과 화면(EXP/리프/JP 등)을 스킵할지 여부
- `m_IgnoreDefeat`: 해당 적 그룹에 패배해도 게임 오버 화면을 띄우지 않게 함. King Pardis III, Edoras Defenders of Fame/Power/Wealth, Colossal Blightant만 사용. 몬스터 아레나에 있을 경우 이 동작이 덮어써져 패배 후 아레나로 돌아가는 것으로 보임
- `m_SkipBgmResult`: 승리 테마를 재생할지 여부
- `m_BanGodBeast`: 전투에서 신수(디바인 비스트) 사용을 비활성화. OT0에서는 미사용. 모드에서 어떤 캐릭터의 스킬 보드에 신수 스킬을 추가했을 때 이를 막을 수 있음(미검증).
- `m_PlayerMemberSetID`: 파티를 참조된 `PlayerMemberSetID`에 지정된 캐릭터들로 강제 구성([커스텀 캐릭터 추가 방법](customchars_kr.md)도 참고)
- `m_Bgm`: 전투 BGM 설정(가능한 값은 `Sound/` 아래 `SoundList` 참고)
- `m_NightBgm`: 밤 전투의 BGM 설정. OT0에서는 미사용, 기능 여부 미검증
- `m_DarkBgm`: `m_NightBgm`와 동일? CotC에서는 두 키가 항상 같은 값으로 보임
- `m_BgmType`:
- `m_ResultBgmWin`: 커스텀 승리 테마 지정
- `m_ResultBgmLose`: 커스텀 게임오버 테마 지정
- `m_Camera`: 사용할 카메라 세트 지정(입력: `BattleCameraSet.m_id`)
- `m_Formation`: 적 그룹의 진형 지정(입력: `EnemyFormations.m_id`)
- `m_EnemyID`: 필드에 배치할 적 지정(입력: `EnemyID.m_id`). 이 배열을 6마리보다 크게 늘려도 즉시 크래시하진 않지만, 추가된 적은 타임라인에 표시되지 않고 약점/실드/상태 오버레이도 없으며, 그 적에게 턴이 오면 게임이 크래시합니다(에러는 allocator 메타데이터 손상/포인터 손상 관련). 증원 호출로 우회할 수 있을지도 모릅니다.
<img width="829" height="459" alt="image" src="https://github.com/user-attachments/assets/433a1c36-e857-4b4c-aaae-07399ddac40b" />

- `m_EventIndices`: (전투) 시작 시 또는 전투 중에 재생되는 이벤트(전투 중 대화 등). 가능한 값은 `EventList.m_id`가 아니라 `BattleEventList.m_id`일 가능성이 큼
- `m_AbortCondition`: 전투 중단 조건. 유효 ID는 `BattleAbortConditions` 아래에서 찾을 수 있을 것
- `m_DefeatPriority`: OT0/CotC에서 항상 false. 기능 불명
- `m_ForcedWinAbortCondition`: 전투를 강제 승리로 처리하는 커스텀 조건. 유효 ID는 `BattleAbortConditions` 아래에서 찾을 수 있을 것
- `m_IsAllEnemyDeadOnForceWin`: (미검증) `m_ForcedWinAbortCondition`의 조건이 충족되면 모든 적이 즉시 사망하는지 여부?
- `m_ForcedLoseAbortCondition`: 전투를 강제 패배로 처리하는 커스텀 조건. 유효 ID는 `BattleAbortConditions` 아래에서 찾을 수 있을 것
- `m_ImportantFlags`: 6개의 플래그. 모두 OT0에서 미사용이며, 테스트로 의미를 파악할 수 없었습니다
  - Flag 1: CotC에서 주로 Job Tower 보스가 사용
  - Flag 2: `Dice_Bolaven_Rank04_BossGr03`(Idore, Roguish Riven Soldier I, Roguish Riven Soldier II 전투)만 사용
  - Flag 3: Flag 2와 동일한 적 그룹만 사용
  - Flag 4-6: 두 게임 모두에서 전혀 사용되지 않음
- `m_PetDear`: -1, 0, 1을 값으로 가짐
- `m_DisableBattleEndWipe`: 많은 최종 보스(Bestower of All, Or'Galdera, Side Solistia의 Galdera)와 Magitek Armor도 사용
- `m_DisableBattleEndTraining`: 훈련장(training grounds)에서 파티가 EXP/JP 등을 얻지 못하게 함. Emerald Direwolf 전투(필드에 Laurana가 있을 때만), Sazantos 회상 중 Bestower of All Ch.7 전투 등에서 사용
- `m_DisableSupporter`: 전투에서 조력자(helpers)를 소환할 수 있는지 여부
- `m_EncountEffectTypeID`: 특수 조우(인카운트) 연출(예: FF6 콜라보 조우)을 지정. 아레나 전투는 이를 0으로 설정하지 않으면 소프트락이 발생합니다. 유효 ID는 `EncountEffectTypeID` 아래에서 찾을 수 있음
- `m_EnablePlayerShield`: OT0/CotC에서 항상 false
- `m_DisableGrade`: OT0에서는 항상 false이나 CotC에서는 그렇지 않음. 무기의 그레이드(grade)를 무시하는 플래그일 가능성
- `m_IsContainRareEnemy`: 레어 적을 포함하는 적 그룹을 표시하기 위한 플래그일 가능성(레어 적 조우율 증가 액세서리의 영향을 받기 위해)

### (**) `EnemyModeID`
게임에 “어떤 모드 ID가 어떤 이펙트를 필드/적에 적용하는지”를 알려줍니다. 이 이펙트들은 avail에 할당될 수 있습니다. 보스가 부스트 모드에 들어가며 특수 이펙트를 표시할 때 사용됩니다.

### (**) `EnemyDropID`
적에게 할당 가능한 다양한 드롭 ID를 관리합니다.

### (**) `EncountVolume`
위험도, 조우 가능한 적 그룹, 각 조우 확률을 결정합니다. encount volume은 `EncountList`에서 맵과 연동됩니다. 맵은 위험도가 바뀔 수 있으므로 가능한 encount volume도 바뀔 수 있습니다.

### (**) `EncountList`
OT0의 랜덤 인카운트 규칙 테이블: 어떤 맵에서 어떤 encount volume이 등장할 수 있는지(`m_EncountVolume`는 `m_EncountVolume.m_id`), 조우까지 필요한 최소 걸음 수(`m_EncountStepMin`), 조우가 발생하기 위한 조건을 지정합니다.

기타 키:
- `m_DisableCondition`: 이 조우를 비활성화하기 위한 조건
- `m_RandomStepA` 및 `m_RandomStepB`: 랜덤 걸음 범위/커브 파라미터. OT0/CotC에서 두 키는 항상 같은 값인 것으로 보입니다
- `m_FirstEncountStepRatio`: 맵/존 진입 후 첫 조우에 대한 배율(첫 조우를 더 빠르거나 더 늦게 만들기 위한 값)로 보입니다

### (*) `EncountEffectTypeID`
각종 인카운트 이펙트를 추적합니다. `m_SeId`는 이 이펙트가 발생하는 맵을 설정하는 것일 수 있습니다.

### (*) `EnemyGroupsByCondition`
특정 조건이 충족될 때 스폰되는 적 그룹 세트. Bestower of All Chapter 7에서 Ringbearer와 함께 Accurst Flame을 스폰하는 데 사용됩니다.

### (**) `SymbolEnemyList`
필드(오버월드)에서 심볼/정예(Symbol/Elite) 적에 할당되는 스프라이트/오브젝트 및 적 그룹, 레벨을 지정합니다.
- `m_GroupId`: 이 심볼 적 엔트리와 연동되는 적 그룹
- `m_Index`: 적 그룹 내 이 적의 인덱스. 여러 심볼 적이 동일한 GroupId 또는 스폰 컨트롤러를 공유할 때 흔히 사용
- `m_Dir`: 기본적으로 심볼 적이 바라보는 방향
- `m_ObjectId`: 심볼 적의 오버월드 스프라이트를 특정 오브젝트로 오버라이드(예: Bestower of Power Ch.3의 Tatloch 선박)
- `m_RepopCount`: 리스폰/재출현 횟수(주기). OT0 적은 기본값이 0이지만, CotC 적은 그렇지 않음(맵 재로딩 후 즉시 재출현하지 않음)
- `m_Redrawing`: OT0/CotC 모두에서 항상 0
- `m_TargetLevel1` 및 `m_TargetLevel2`: 심볼 적의 레벨 범위일 가능성
- `m_Scale`: 필드에서 적 심볼의 스케일 배율(시각적/충돌 크기)

### (**) `SymbolEnemyGroupList`
`SymbolEnemyList` 테이블의 심볼 적 ID(`m_id`)를, 특정 맵의 특정 지점의 심볼 적을 가리키는 내부 ID에 할당하는 것으로 보입니다. `m_SymbolEnemyId` 배열은 `m_StartSymbolEnemyId`로 지정된 심볼 적이 쓰러진 뒤 리포퓰레이션 시, 그 자리에서 어떤 심볼 적이 등장할지와 그 확률(`m_probability`)을 정하는 것일 수 있습니다. 다만 CotC/OT0 모두 이 배열을 사용하는 것으로 보이진 않습니다.

## `Battle/` 아래
### (***) `BattleCameraParams`
전투 카메라 프리셋/구성에 해당하는 ID들입니다. 값 `-1.0`은 게임에서 “기본값 사용”으로 해석되는 것으로 보입니다:
- `m_PosotionX/Y/Z`: 3D 공간에서 전투 카메라 위치
- `m_RotationX/Y`: X/Y 방향의 카메라 회전/틸트
- `m_FOV`: 시야각. 시야각이 좁을수록 줌인/망원 느낌. FOV 변경 시 파티 위치는 고정이라는 점에 유의
- `m_SequencerPath`: 시네마틱/시퀀서 바인딩. 전투 카메라 컷/타겟팅에 쓰이는 Level Sequence 또는 시퀀서 기반 카메라 리그 에셋을 가리킬 가능성
- `m_FocalDistance`, `m_FocusDistance`: 포커스 평면까지의 거리(하나는 레거시, 하나는 새 명명일 수 있음(CotC에는 `m_FocalDistance`만 존재) 또는 시스템이 다를 수 있음). OT0에서는 둘 다 항상 `-1.0`, CotC는 다른 초점 거리를 사용
- `m_FocalRegion`: 초점이 맞는 영역 크기
- `m_NearBlurSize` 및 `m_FarBlurSize`: 거리별 블러 강도
- `m_MaxBokehSize`: 보케 블러 상한
- `m_Scale`: 카메라 거리 스케일/샷 스케일 오버라이드일 가능성

### (***) `BattleCameraSet`
적 그룹에 할당되는 카메라 구성 세트(`BattleCameraSet.m_id`=`EnemyGroups.m_Camera`). CotC에는 존재하지 않습니다(CotC는 전투에서 중립 카메라 설정 1개만 사용).

### (***) `BattlePositions`
`EnemyFormations` 또는 `EnemyReinforcements` 등에 쓰이는 PositionID를 정의합니다. `m_Priority`는 좌표가 겹칠 때 어떤 스프라이트가 앞에 보일지, 또는 적 선택 순서를 결정할 수 있습니다.

### (***) `BattleEventList`
`EnemyGroups.m_EventIndices`로 적 그룹에 할당될 수 있는 이벤트로 보입니다.
- `m_IsMustPlay`: 이벤트가 스킵 가능한지 여부(?)
- `m_EventCommand`: 트리거 시 실행할 커맨드 스크립트 ID(`BattleEventCommand`를 참조)
- `m_EventConditions`: 조건 타입 ID(다른 파일의 `m_Conditions` 배열(`TacticalList`, `SkillConditionList`, `TacticalActionList` 등)과 동일할 가능성)
- `m_EventParams`: 조건 파라미터
- `m_EventEnemies`: 조건이 적용될 적 슬롯/적 인덱스/파트를 지정하는 선택적 필터(두 게임 모두에서 미사용으로 보임)

### (***) `BattleEventCommand`
전투 이벤트 시퀀스의 스크립트 단계들을 포함합니다. `BattleEventList`가 어떤 조건에서 무엇을 실행할지 기술한다면, `BattleEventCommand`는 실제 스크립트 단계(대사 표시, 애니 재생, 이펙트 스폰, 적 텍스처 교체, BGM 변경 등)를 실행하는 것으로 보입니다.

- `m_NextWait`: 이 커맨드 실행 후 다음으로 진행하기까지의 지연(초)
- `m_isWaitSkillTime`: true이면 스킬 애니메이션이 끝날 때까지 대기
- `m_isSwitchActionSkip`: 액션 시퀀스 스킵을 허용/강제
- `m_PlayBgmBlock`: 무언가가 완료될 때까지 BGM 변경을 막는 것일 수 있음
- `m_PerformerID`: 파티/게스트/적 performer 리스트의 인덱스 또는 특수 ID일 가능성
- `m_PerformerIsLeader`: 파티 리더를 performer로 사용
- `m_PerformerEnemy`: 0이 아니면 performer가 적임을 의미(입력으로 `EnemyID.m_id`를 쓰는 것으로 보임)
- `m_TargetID`: 강제 액션/스킬의 대상 지정으로 보임
- `m_EnableTarget`,`m_EnableAction`: 이 커맨드 중/후에 플레이어 타겟팅/행동을 활성화하는 토글
- `m_NameID`: 화자 이름 로컬라이즈 ID(`GameTextCharacter` 아래)
- `m_TextID`: 대사 로컬라이즈 ID(`GameTextEvent` 아래)
- `m_BalloonType`: 말풍선 타입(1 = 아마 일반 말풍선)
- `m_NoTail`: 말풍선 꼬리 없음
- `m_BaloonOffsetX/Y`: performer 기준 말풍선 위치 오프셋
- `m_NoName`: true이면 화자 이름 숨김
- `m_NarrationID`: `NarrationList.m_id`의 ID를 사용
- - `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`: 나레이션 스타일 관련 키
- `m_AnimID`: 말하는 동안(또는 직전/직후) performer가 재생할 애니메이션
- `m_DirID`: performer 방향 프리셋
- `m_isRewriteIdleMotion`: 커맨드 이후에도 아이들(Idle) 오버라이드를 유지
- `m_SoundID`: 사운드 재생(아마 `SoundList` ID)
- `m_SoundHandle`: 런타임 핸들 슬롯일 가능성(예: SFX 시작 후 핸들을 저장하고, 뒤 커맨드가 정지/교체)(?)
- `m_EffectID`: 이펙트 스폰(아마 `EffectList` ID)
- `m_EffectHandle`: 후속 조작/정지를 위한 런타임 핸들 슬롯일 가능성(?)
- `m_EffectPosition`: 이펙트 스폰 위치(performer, target, screen 등)
- `m_BgmID`: BGM 변경(역시 `SoundList` ID일 가능성)
- `m_FadeTime`: BGM 변경 페이드 시간
- `m_IsRemoveAllAilments`: 상태 이상 제거(페이즈 전환 등에서 사용될 수 있음)
- `m_EnableRetire`, `m_DisableGameOver`: (미검증) 연출 전투 중 UI 옵션 및 실패 상태를 강제 활성/비활성(?)
- `m_LastBattleMapColor`: 전투 배경 틴트 제어로 보임
- `m_TextureChangeEnemyIdx`: 텍스처를 바꿀 적 슬롯(`-1`은 미사용)
- `m_ChangeEnemyTextureIdx`: 바꿀 텍스처 인덱스/슬롯(`-1` 미사용)
- `m_TextureChangeTime`: 텍스처 교체 타이밍(지연 또는 크로스페이드 시간, 미검증)
- `m_EnemyPartsIndex`: 영향을 받는 적 파트 인덱스
- `m_EnemyPartsDisplay`: 이벤트 중 해당 파트를 표시/숨김
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: `TacticalList`에도 존재. 전투 엔진이 이 플래그를 기준으로 스크립트를 진행(암묵적인 “다음 단계”로 사용)할 가능성이 큼. 예: Shadow of Twin Worlds에서 Lucian과 Lyblac이 모두 브레이크되었을 때 Shadow 약점이 해제되는 전술이 발생했는지 추적하여, Lucian과 Lyblac이 브레이크에서 회복한 뒤 다시 약점을 잠그는 데 사용(그 상황에 특화된 조건이 없는 것으로 보이기 때문)
- `m_JoinTrigger`: “유닛이 전투에 합류” 이벤트(예: 게스트 중도 합류)와 연관될 가능성
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`: 일반 구조에 맞지 않는 특수 커맨드 타입

### (*) `BattlePlaybackData` 및 `BattlePlayback`
OT0의 전투 프리뷰/전투 데모 설정으로 보입니다. 작은 DB로, “프리뷰 프리셋 X에서 적 Y가 스킬 Z를 수행하고(필요 시) 공격 이펙트 표시를 켜거나 끈다”를 게임에 알려줍니다.

#### `BattlePlaybacks`
구조는 기본적으로 `PlaybackID` → `SkillID`(+ performer 슬롯 오버라이드) + VFX 표시 토글입니다.
- `m_EnemySkillID`: 재생할 (적) 스킬(다만 모든 스킬은 동일 파일 `SkillID`에 저장됨)
- `m_SkillPlayEnemyIndex`: 프리뷰에서 어떤 적 슬롯/인덱스가 스킬을 수행할지(`-1`="자동/기본값")
- `m_VisibleAttackEffect`: 프리뷰에서 “공격 이펙트” 레이어를 표시할지 여부(예: 베기/충격 오버레이, 히트 플래시, 추가 VFX 등을 표시/숨김하여 “클린” vs “풀” 프리뷰)

#### `BattlePlaybackData`
실제 런타임 데이터가 아닐 수 있습니다. `BattlePlaybacks`와 비슷한 레이아웃이지만, 각 엔트리에 다음과 같은 이상한 줄이 있습니다:
```
"m_EnemySkillID_01": "m_EnemySkillID_01"
```
가능한 해석은 다음 중 하나일 수 있습니다:
1. FModel이 스키마/템플릿/struct 기본값을 혼란스럽게 덤프한 경우(예: 해당 필드가 FName인 기본 오브젝트/struct가 이상하게 직렬화됨)
2. 원래 `m_EnemySkillID_01`, `m_EnemySkillID_02` 같은 필드를 두는 “멀티 슬롯” 구형 테이블이었는데 dump/export 과정에서 값이 망가진 경우
3. `m_EnemySkillID_01`이 실제로는 다른 테이블에 대한 row handle/key(FName)여야 하는데, dump가 저장된 값 대신 프로퍼티 이름으로 치환한 경우
**주의:** Switch 버전에서는 이 테이블의 `m_SkillPlayEnemyIndex` 값이 다릅니다. Steam 데모 버전에는 일부 엔트리가 없습니다.

### (*) `BattleResultBonus`
전투 결과 보너스(“No Damage”, “Overkill”, “Break”, “1Turn Kill” 등)의 배율과 로컬라이즈 ID를 추적합니다. `m_Param`은 어떤 항목(EXP/JP/리프)에 보너스가 적용되는지를 나타내는 것으로 보입니다:
- 0: 리프
- 1: JP
- 2: EXP
2를 초과하는 미사용 파라미터 존재 여부는 미검증입니다.

### (**) `BattleVoiceList` 및 `BattleVoiceArenaList`
전투 중 파티 멤버 또는 내레이터가 재생하는 다양한 보이스 라인을 정의합니다(예: Or'Galdera 전투의 특수 보이스).

### (***) `BattleAbortConditions`
OT0의 전투 종료/강제 결과 조건 테이블입니다. 각 엔트리는 작은 조건 묶음을 정의하며, 전투 중 조건이 충족되면 전투가 중단(조기 종료)되거나, `EnemyGroups`에서 어떤 필드가 이를 참조하느냐에 따라 강제 승리/강제 패배로 처리됩니다.
- `m_Conditions`: 조건 타입 ID(아마 `TacticalList`, `TacticalActionList`, `SkillConditionList`, `BattleEventList`와 동일)
- `m_Params`: 각 조건의 파라미터(의미는 조건 타입에 따라 달라짐)
- 특정 조건 타입에서만 의미가 있는 필터:
   - `m_AilmentTypes`(`SkillAilmentType.m_id` 입력)
   - `m_StatusTypes`(아마 `CharaStatusID.m_id` 입력일 수 있음)
   - `m_WeaponTypes`(`WeaponType.m_id` 입력)
   - `m_MagicTypes`(`Magictype.m_id` 입력)
   - `m_EnemyID`(`EnemyID.m_id` 입력)

### (*) `BattleActionID`
“액션 ID”(아마 `SkillAilmentType.m_ActionID`)를 구체적인 전투 애니메이션/모션에 매핑하는 작은 테이블입니다. 또한 해당 애니메이션을 표준 전투 리그 대신 NPC/필드 캐릭터 리그로 재생할지 여부를 나타내는 플래그도 포함합니다. `m_UseNPC`는 이 액션을 일반 전투 스프라이트가 아닌 NPC 캐릭터 애니메이션 세트로 재생할지 여부를 결정할 수 있습니다.

### (*) `BattleParams`
다양한 전투 관련 파라미터이며, Dumper-7 SDK의 `Kingship_structs.hpp`(BravelyPath Modular Discord의 #file-dump 참고)에 따르면 의미는 다음과 같습니다:
```
enum class EBATTLE_PARAMS : uint8
{
    ...
};
```
(열거형 본문은 원문 그대로 유지)

### (*) `BattleDeathBlowPoint`
“Death Blow”(특수 공격) 게이지 획득량을 위한 룩업 테이블입니다. 각 행은 특정 체크 조건(타입 + 값)이 만족되었을 때 Death Blow 게이지를 얼마나 증가시킬지를 정의합니다.
- `m_CheckType`: 무엇을 체크하는지
- `m_CheckValue`: 해당 체크 타입의 임계값/수량(카운트처럼 보임)
- `m_IncreasedPoint`: 조건이 맞을 때 더할 “Death Blow 포인트” 양

### (*) `BattleLayoutSet`
카메라 프리셋과 적 측 포지셔닝을 묶는 소규모 전투 스테이징/레이아웃 프리셋 테이블로 보입니다. 게임에서 어디에 쓰이는지는 불명확합니다(예: 어떤 진형/증원도 `PositionID` 270을 쓰지 않는 것으로 보임).

## `AIBattle` 아래
이 파일들은 적 AI 행동과 적이 사용하는 스킬(및 사용 방식)을 담당합니다.

### (****) `TacticalList`, `TacticalAssignList`, `TacticalSkillList`
OT0의 적 AI “규칙(룰) 조항(rule clause)” 테이블입니다. 각 엔트리는 조건 세트를 정의하고, 조건이 만족되면 AI가 어떤 행동을 선택하거나 활성화합니다. 적에게는 `TacticalAssignList`에서 이 “규칙”이 할당되며(`m_id`가 `EnemyID.m_TacticalAssignID`에 대응), 전투 스킬은 `TacticalSkillList`를 통해 할당됩니다(`m_id`가 `EnemyID.m_SkillsID`에 대응).
- `m_Presage`: 이 전술이 예고(telegraph/presage) 표시를 띄우는지 나타내는 boolean/enum일 가능성
- `m_PresageSkillID`: 예고에 연결된 스킬 ID(무엇을 예고하는지). 예: 보스가 부스트 모드를 선언할 때 사용(이 스킬 ID는 적의 `TacticalSkillList.m_UseSkills` 배열에 **존재하지 않는** 것이 특징)
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`: 이벤트 플래그 게이팅 및 부작용. `BattleEventCommand` 참조. AI 동작의 분기/시퀀싱에 쓰일 가능성
- `m_Conditions`: `TacticalActionList`, `BattleEventList`, `SkillConditionList`와 동일한 조건(그리고 동일한 `m_Params` 및 필터 배열)일 가능성
   - `m_AilmentTypes`(`SkillAilmentType.m_id` 입력)
   - `m_StatusTypes`(아마 `CharaStatusID.m_id` 입력일 수 있음)
   - `m_WeaponTypes`(`WeaponType.m_id` 입력)
   - `m_MagicTypes`(`Magictype.m_id` 입력)
각 조건의 의미는 아직 엄밀히 규명되지 않았습니다. 아래 리소스가 도움이 될 수 있습니다:
https://github.com/veganprimate/CotCPort/tree/main/Conditions/Conditions_Datamine_KR

기타:
- 28은 OT0 전용 조건입니다
- 조건 타입 ID는 OT0와 CotC에서 동일해 보이지만, CotC에만 있고 OT0에는 없는 조건이 있을 수 있습니다
- 10000+ 범위의 ID는 행동 실행 순서와 관련된 경우가 많아 보입니다:
  - Condition 10100은 적의 부스트 모드를 예고하는 스킬에서 자주 사용
  - Condition 10304는 다른 조건이 충족되는 “그 순간”에 트리거(예: Lucian과 Lyblac이 모두 브레이크된 직후 Shadow의 베일이 즉시 해제됨. Condition 718과 `m_params`는 `EnemyGroup` 엔트리에서의 해당 인덱스)
  - 반면 Condition 10301은 베일 해제가 다음 턴에 일어나도록 만듦
  - 10711은 턴 시작 시점에 행동이 발생하는 것으로 보임(예: Lucian과 Lyblac이 첫 브레이크에서 회복한 뒤 Shadow가 사용하는 경우)

### (****) `TacticalActionList`
전술이 적용될 때 실제로 무엇을 할지(어떤 스킬을 어떤 방식으로 누구에게 쓰는지)를 게임에 지시하는 것으로 보입니다. 사용할 스킬(인덱스로), 타겟, 타겟 선택 방식, 추가 조건/우선순위 규칙 등을 정의합니다.
- `m_SkillIndex`: SkillID 자체가 아닙니다. 스킬 리스트/배열에 대한 인덱스입니다(테스트 기준: 적의 `m_SkillsID`에 해당하는 `TacticalSkillList.m_UseSkills[]`)
- `m_SelectType`: 타겟 선택 모드 enum이거나, 스킬 인덱스 같은 값이 선택되는 방식과 관련된 값. 테스트에서는 연결된 스킬이 발동하려면 `m_SelectType`을 3으로 설정해야 했습니다(`SelectType` 1은 첫 번째(?) 스킬로 고정되는 동작)
- `m_FriendlyIndex`: “아군 측”(적 관점?)에서 누구를 타겟으로 할지. 여기서 `-1`은 “고정 아군 없음; `SelectType`에 따라 선택” 같은 의미일 수 있음(?)
- `m_PrioritySkill`: 설정 시 일반 선택을 덮어써 특정 스킬 인덱스를 강제(또는 우선 표시?)할 수 있음
- `m_TurnCount`: 지정 전술 ID의 조건이 충족되는 동안, 이 값 `N`마다 행동을 반복
- `m_GroupIndex`: 그룹 키. 페이즈 분리, 턴당 그룹별 행동 수 제한, 그룹 내 다수 적의 협동 행동 조정 등에 쓰일 가능성
- 추가 액션 단위 조건(`TacticalList`와 유사하지만 해당 액션에만 적용): `m_Conditions`, `m_Params`, `m_AilmentTypes`, `m_StatusTypes`, `m_WeaponTypes`, `m_MagicTypes`
