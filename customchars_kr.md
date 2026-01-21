## **LANGUAGE:**  [English](customchars_en.md) | [日本語](customchars_jp.md) | [中文](customchars_zh.md) | [한국어](customchars_kr.md)
**참고:** 이 가이드는 신규 캐릭터용 스프라이트시트(spritesheets) 제작을 다루지 않습니다. 자세한 내용은 BravelyPath Modular Discord를 확인하고, 필요하면 #modding-help에서 질문해 주세요.

1. `/Game/Content/Local/DataBase/Character` 아래의 `CharaID`를 확인하세요. 이 파일은 기본적으로 NPC의 텍스처 경로 등 정보를 저장합니다. 추가하려는 NPC가 여기 엔트리가 없다면 새로 추가해야 합니다. 물론 이 파일을 이용해 특정 NPC의 스프라이트시트 텍스처 경로를 찾아볼 수도 있으며, 해당 파일들을 직접 확장하는 것만으로도 동작하는 경우가 많습니다.
2. `CharaPlayer`에 새 엔트리를 추가하고, `m_CharaID`에서 `CharaID.m_id`를 참조하도록 설정하세요. 이름은 `GameTextCharacter`에 추가해야 합니다. 아래에 나열된 파라미터는(제 추측으로는) 다음과 같습니다:
```
 "m_Param": [
        0, (없음)
        250, (최대 HP)
        60, (최대 SP)
        0, (최대 BP)
        0, (없음)
        0, (HP)
        0, (SP)
        0, (BP)
        0, (없음)
        72, (물리 공격)
        88, (물리 방어)
        88, (속성 공격)
        96, (속성 방어)
        64, (속도)
        72, (치명타)
        80, (명중)
        80, (회피)
        1, ("없음", 아마도 실드 대미지 배수)
        0, (없음)
        0, (없음)
        0 (없음)
      ]
```
여기서 "없음"은 `GameTextUI`에서 이 스탯들이 그렇게 표시되는 이름일 뿐이며, 실제로 쓸모가 없다는 뜻은 아닙니다.

3. `m_MaxParam`에도 비슷한 값을 지정하세요. 게임은 이 두 배열을 기반으로 각 레벨의 값을 보간(interpolate)합니다. `m_MaxParam`이 레벨 99의 스탯을 담고 있는지는 확실하지 않습니다. 대륙의 패자(CotC) 유닛 대부분에서 HP max param이 9999를 훨씬 넘지만, 테스트 결과 그 값에 도달하지는 않기 때문입니다.
4. 새 유닛이 학습한 스킬을 장착할 수 있도록 `m_InitialOpenType`을 2로 설정하고, XP를 획득하도록 `m_InfluenceValue`를 10으로 설정하세요.
5. `PlayerMemberSet` 아래에 새 엔트리를 만들고, 방금 추가한 `PlayerID`(즉 `CharaPlayer.m_id`)를 `m_PlayerID`로 참조하도록 하세요. 또한 새 엔트리의 `m_ValidCondition`은 `991202`로 설정되어야 하며, `m_PlayerIDs` 배열의 첫 번째 값이 0인지 반드시 확인하세요. 첫 값이 0이 아니면 이론상 Zero/링 베어러 초즌을 대체할 것 같지만, 이벤트 영입 커맨드 10029에서는 대체가 아니라 소프트락만 발생합니다(다만 그 `PlayerMemberSetID`를 적 그룹에 할당하면 동작하긴 합니다).
6. `SkillBoardData`(경로: `/Game/Content/Local/DataBase/SkillBoard`) 아래에 새 엔트리를 만들고, 새 `PlayerID`를 참조하도록 하세요. `m_CanNotEssential`은 스킬이 학습 가능한지 여부를 결정하지만, `ItemList`(경로: `/Game/Content/Local/DataBase/Item`)에 해당 스킬을 아이템으로도 추가하지 않으면 아이템을 획득하지 못합니다. `m_SupportLockCond1`은 반드시 `16`만 사용하고, `m_SupportLockCond2`는 `0`만 사용하세요. CotC는 가끔 다른 잠금 조건을 쓰지만 OT0에서는 동작하지 않는 것으로 보이고, `16`을 제외한 잠금 조건은 로컬라이제이션도 없습니다. `m_SupportLockParam1`은 지정된 서포트 스킬을 해금하기 위해 학습해야 하는 배틀 스킬 수를 결정합니다.
7. (선택) `SkillID`와 `SkillAvailID`를 수정해 커스텀 스킬 및 avail을 추가할 수 있습니다. 자세한 내용은 [커스텀 스킬 및 avail 추가 방법](customskls_kr.md)를 참고하세요.
8. 커스텀 필살기(special skills) 추가: `SpecialSkillID`(`/Game/Content/Local/DataBase/Skill/` 아래)에 새 엔트리를 추가하고, `CharaPlayerSpecialSkillList`(`/Game/Content/Local/DataBase/Character/` 아래)에 엔트리를 추가해 해당 유닛을 새 special skill ID에 연결하세요. 그리고 궁극기(ultimate)의 각 레벨마다 `SpecialSkillGrowthList`에 새 엔트리를 추가하여, 그 special skill ID를 스킬에 연결합니다. CotC 유닛은 종종 최대 레벨이 3보다 훨씬 높은 special skill을 갖는 것으로 보이지만, 게임이 그것들을 로드하게 만들 수 있는지는 확실하지 않습니다. 또한 서로 다른 레벨에 동일한 스킬을 지정했을 때, 게임이 avail/스킬의 더 높은 boost 레벨 버전을 로드하는지 여부도 확실하지 않습니다.
9. `RecruitEvent_p.pak`를 언팩(unpak)하세요([이 모드 및/또는 OT0에서 에셋을 추출하는 방법](Begin_kr.md)도 참고).
10. `/Game/Content/Local/DataBase/Event/VPartychat/EvPChatOth_01`를 편집해, `m_OptAry[0]`에서 새 `PlayerMemberSetID`를 참조하는 커맨드 10029 엔트리를 추가하세요. `m_OptAry[5]`의 1은 필요 없어 보입니다. 또한 [커스텀 퀘스트 및 이벤트 추가 방법](customqsts_kr.md)도 참고하세요.
