## **LANGUAGE:**  [English](Sound_Prog_en.md) | [日本語](Sound_Prog_jp.md) | [中文](Sound_Prog_zh.md) | [한국어](Sound_Prog_kr.md)

MarvinCx886가 이 워크플로를 발견해 준 것과, Zy가 아래 링크에 영어 가이드를 작성해 준 것에 감사드립니다:
https://github.com/zhangyucn2/Octopath-traveller-audio-replacement-tutorial

이 글이 해당 워크플로를 이해하는 데 필요한 배경 설명을 더해 주길 바랍니다.

### 『옥토패스 트래블러 0』의 오디오가 (아마) 어떻게 구성되어 있는가
CRIWARE 기반 Unreal 프로젝트에서 오디오는 보통 다음으로 구성됩니다:
- `.acb`: 큐 시트(cue sheet) 데이터베이스(큐 이름, 큐 ID, 메타데이터, 라우팅 등)
- `.awb`: 웨이브 뱅크(wave bank)(실제 인코딩된 오디오 데이터 페이로드)
- 에디터에서 CRIWARE UE 플러그인으로 `.acb`를 임포트할 때 UE 측에서 생성되는 “래퍼(wrapper) 에셋” (ACB/AWB를 참조하는 `.uasset` 파일)

런타임에서 게임이 오디오를 트리거하는 방식은 보통 아래 패턴 중 하나입니다:
- 큐 이름 → 큐 ID → `.awb` 안의 웨이브
- 큐 ID를 직접 지정
- 특정 큐 시트 내에서 큐 이름을 지정

Sound 관련 DataBase 파일을 수정하면 새로운 사운드를 임포트해 OT0가 사용하는 동일한 식별자/매핑 시스템에 통합할 수 있는지 여부는(제가 아는 한) 현재로서는 불분명합니다. 아래는 `Sound/` 아래의 각 DataBase 파일에 대한 설명입니다.

### `SoundSheetList`
위 가이드처럼 CRIWARE 플러그인으로 `.acb`를 임포트했을 때 생성되는, CRI Atom CueSheet 래퍼 에셋(UE의 `.uasset`)들의 레지스트리를 담고 있는 것으로 보입니다. 새로운 cuesheet(새 `.acb` 임포트)를 추가하더라도 OT0가 반드시 이를 사용하진 않습니다. OT0가 사용하려면(추측이지만) 다음 중 하나가 필요할 수 있습니다:
- 이 리스트에 포함되어 있거나, 또는
- 어떤 코드가 경로로 직접 로드하는 경우.

`m_InitialLoad`는 부팅 시 프리로드(preload) 동작을 시사합니다.

### `SoundList`
게임 로직이 직접 참조하는 “사운드 카탈로그”입니다. 게임플레이는 “`bgm_theme_scholar`를 재생해라”(또는 sound ID 1을 재생해라)처럼 요청하고, 런타임은 이를 다음으로 해석(resolve)합니다:
- 어떤 sheet를 사용할지 (`m_Sheet`가 `SoundSheetList.m_id`를 참조)
- 재생 제약(루프, 최대 동시 재생 수, 우선순위)
- 페이드 인/아웃

즉 사운드 라벨 및/또는 사운드 ID가 게임이 사용하는 API입니다. `.acb`에 새 큐를 추가하더라도, 대응하는 `SoundList` 엔트리가 있고 게임이 그것을 호출하지 않는 한 그 큐는 절대 재생되지 않습니다.

### `ReplaceSoundList` 및 `ReplaceBGMList`
이 둘은 OT0 전용으로 보이며, 퀘스트 조건 충족 여부 또는 특정 적 그룹과의 전투 여부에 따라 사운드/BGM을 교체합니다.

### `VoiceSymbolList`
게임 스크립트/데이터에서 사용하는 “보이스 심볼 문자열(voice symbol string)”을 더 작은 정수 ID로 매핑하는 테이블처럼 보입니다.

### `PartVoiceID`
보이스 카테고리/“서브타입”.

### `ConvPersonalityVoiceID`
Zero에서 선택 가능한 6가지 보이스를 가리키는 것으로 보입니다.

### `VoiceEffectID`
버스 라우팅/DSP 제어(예: CRI 버스(BUS4 등)를 선택하고, 볼륨/LPF/리버브 send 등의 파라미터를 설정).

### `GramophoneList`
어떤 Sound ID가 어떤 악보(Music Score) 아이템에 대응하는지 기록합니다.
