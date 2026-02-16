# CotCPort
## **LANGUAGE:**  [English](README.md) | [日本語](README.jp.md) | [中文](README.zh.md) | [한국어](README.kr.md)
<table border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td width="50%">
      <img alt="1" src="https://github.com/user-attachments/assets/7de018fd-65fc-4fac-8da9-d877c3d1b3bf" width="100%" />
    </td>
    <td width="50%">
      <img alt="2" src="https://github.com/user-attachments/assets/35bbf069-f2ce-4ed9-b6d5-be0ec8975fa6" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="3" src="https://github.com/user-attachments/assets/f52aab44-c6e9-41e2-8a5d-9e8ad82a9007" width="100%" />
    </td>
    <td width="50%">
      <img alt="4" src="https://github.com/user-attachments/assets/6370561a-3653-4dcb-a0e5-2ab82f787b1e" width="100%" />
    </td>
  </tr>
  <tr>
    <td colspan="2">
      <img alt="5" src="https://github.com/user-attachments/assets/b0943896-d810-4fbd-bf78-f6a7568541e0" width="100%" />
    </td>
  </tr>
</table>


『옥토패스 트래블러 0』용 모드로, 『옥토패스 트래블러: 대륙의 패자』(글로벌/일본/중국 버전. 이하 “대륙의 패자”)의 콘텐츠를 가능한 한 많이 이식하는 것을 목표로 합니다.
- 대륙의 패자의 270+ 유닛을(플레이어블 캐릭터) 완전한 스킬 보드와 함께 모두 플레이 가능 (커스텀 신규 유닛: 노모스(학자), 프라, 프리드, 휴고, 기멜, 마티아스, 미겔 포함)
- 몬스터 아레나(Monster Arena)에서 대륙의 패자의 수백 종 보스와 전투 가능
- 몬스터 아레나의 신규 적을 처치해 대륙의 패자에서 온 신규 액세서리 수십 종을 해금

현재 본 모드는 다음 언어를 지원합니다:
- 영어
- 일본어
- 한국어
- 중국어(간체)
- 중국어(번체)

![chargif](https://github.com/user-attachments/assets/00c35b82-3e9c-4810-8d8b-657f2c2a1151)

## 설치
Releases에서 `notex` pak 중 하나와 `texture` pak, 그리고 `RecruitEvent_p.pak`를 다운로드하세요. `notex`에는 DataBase 파일이 들어 있으며, `texture`에는 대륙의 패자에서 이식한 텍스처와 모델이 포함되어 있습니다. `RecruitEvent_p.pak`는 모드로, 펜/메이시/반지 운반자 선정 사이의 첫 파티 잡담 이벤트(“상처 치료”)를 새로 추가된 260+ 유닛 전체를 영입할 수 있는 영입 이벤트로 교체합니다. 새 유닛을 모두 영입한 뒤에는 이 모드를 제거해도 됩니다.
이 파일들을 게임의 Paks 폴더에 넣어야 합니다. 정확한 경로는 『옥토패스 트래블러 0』의 버전 및 설치 방식에 따라 달라집니다.

### Steam
세 개의 pak를 `steamapps\common\Octopath_Traveler0\Octopath_Traveler0\Content\Paks`에 넣으세요.

### Microsoft Store
1. 모든 pak의 접미사 `_p`를 `_P`로 변경하세요
2. PC에서 **Xbox App**을 열고, 왼쪽 사이드바의 설치된 게임 목록에서 **Octopath Traveler 0**를 우클릭한 뒤 **관리**를 선택하세요
3. **파일** 탭을 클릭하세요
4. **찾아보기**를 클릭하세요
5. pak 파일을 `<game install folder>\Octopath_Traveler0\Content\Paks\`에 넣으세요

### Switch(실기기/Atmosphère)
세 개의 pak를 `<SD Card>\atmosphere\Contents\01005270232F2000\romfs\Octopath_Traveler0\Content\Paks`에 넣으세요

### Switch(에뮬레이터)
`romfs/Octopath_Traveler0/Content/Paks` 구조로 모드를 만들고, 세 개의 pak를 Paks 폴더에 넣으세요.

## 새로 추가된 여행자(플레이어블 캐릭터) 영입 방법
<img width="1286" height="726" alt="12" src="https://github.com/user-attachments/assets/f06205a2-5c30-4a8f-8718-5ee69d84db95" />

1. `RecruitEvent_p.pak`가 Paks 폴더에 들어 있는지 확인하세요.
2. 엠버글로우 에 있는 롱보우 마스터(위 이미지 참고)에게 말을 거세요. 이벤트가 발생하며, 이 모드에 추가된 모든 플레이어블 캐릭터가 영입됩니다.

## (선택 사항) 하드 모드 버전
본 모드는 EnemyID 에셋을 포함한 여러 에셋을 변경하므로, 동일한 에셋을 수정하는 다른 모드와는 호환되지 않습니다. 이 둘을 함께 사용하고 싶은 분들을 위해, 파일명에 `_hard` 접미사가 붙은 하드 모드 버전을 별도로 만들었습니다. 변경 사항은 다음과 같습니다:
- 일반 적 HP는 2배, 보스 HP는 3배로 증가
- 일반 적 스탯(Phys. Atk., Phys. Def., Elem. Atk., Elem. Def., Speed, Accuracy, Crit., Crit. Def., 최대 실드 수)을 25% 상향, 보스는 50% 상향. 또한 보스 처치 시 획득하는 XP/JP/돈이 20% 증가
- 소프트락 방지를 위해 일부 적을 개별 조정(가짜 올베릭, 푸른 늑대, 추기경 테라그래프)
- 일부 보스의 스탯/스킬/전술을 조정(WIP. 현재까지는 주로 Bestower of All 보스에 큰 영향)

## 기여
누구나 이 프로젝트에 기여할 수 있습니다. 이 저장소에는 pak 외에도, 모드의 각 요소가 어떻게 동작하는지에 대한 상세 문서, 『옥토패스 트래블러 0』 모딩 전반에 대한 문서, 그리고 아직 완료되지 않은 작업 목록(진행 방향에 대한 간단한 논의 포함)이 들어 있습니다.
아래는 『옥토패스 트래블러: 대륙의 패자』에서 아직 이식되지 않은 기능 목록입니다. 각 항목은 현재 진행 상황을 설명하는 본 저장소의 markdown 파일로 연결됩니다:
- [퀘스트 및 이벤트](QuestEvent_Prog_kr.md)
- [이펙트 및 애니메이션](Animations_Prog_kr.md)
- [오브젝트](Objects_Prog_kr.md)
- [맵/지역/레벨](Maps_Prog_kr.md)
- [효과음 및 음악](Sound_Prog_kr.md)
- [대륙의 패자에서 완전히 새로운 기능을 이식](Custom_Prog_kr.md)

핵심은 UE 4.27 기반 『옥토패스 트래블러: 대륙의 패자』 에셋에서 가능한 한 많은 데이터를 추출하고, 그 데이터를 사용해 『옥토패스 트래블러 0』용 Unreal Engine 5.4 신규 에셋을 만든 뒤, 모드가 이를 로드하도록 구성하는 것입니다. 일반적으로는 “쿠킹된(cooked)” UE 4.27 에셋을 UE 5.4로 변환하는 방법이 존재하지 않기 때문입니다.

### CotCPort를 기반으로 한 모드
위에서 언급했듯이 이 모드를 기반으로 여러분의 모드를 만들어도 되지만, 대륙의 패자 이식 관련 버그 리포트를 한 곳에 모으기 위해 이 페이지로의 링크를 부탁드립니다. pak는 파일명이 알파벳 순서로 정렬되어 뒤에서 로드되는 쪽이 앞을 덮어쓰므로, 여러분의 모드 파일명을 바꾸면 CotCPort 뒤에 로드되도록 할 수 있습니다.

### 튜토리얼 및 리소스
아래는 대륙의 패자에서 이식된 데이터를 활용해 여러분의 모드를 만들기 쉽게 해 주는 튜토리얼/리소스 목록입니다.
- [이 모드 및/또는 OT0에서 에셋을 추출하는 방법](Begin_kr.md)
- [커스텀 캐릭터 추가 방법](customchars_kr.md)
- [커스텀 적 및 전투 추가 방법](customenms_kr.md)
- [커스텀 퀘스트 및 이벤트 추가 방법](customqsts_kr.md)
- [커스텀 스킬 및 avail 추가 방법](customskls_kr.md)

## 연락처
BravelyPath Modular Discord의 #cotc-characters-mod 채널에서(여러 기여자 및 모더와 함께) 연락할 수 있습니다:
https://discord.com/invite/t43Kbrp 

## 감사의 말
도움을 주신 분들께 감사드립니다:
- BoltGSR: 『옥토패스 트래블러: 대륙의 패자』 일본 버전 데이터를 제공해 주었습니다.
- Oersted: 『옥토패스 트래블러: 대륙의 패자』 중국 버전 데이터와 함께, 완성된 스킬 시트/스프라이트 시트, 그리고 커스텀 유닛 아이디어를 제공해 주었습니다.
- Vat: 캐릭터 및 적의 확장 스프라이트 시트를 제공해 주었고, 게임 내에서 매우 선명하게 보이도록 많은 텍스처를 쿠킹해 주었습니다.
- Solo Traveler: 다양한 기능의 플레이 테스트를 도와주었고, 하드 모드 버전에 대한 아이디어와 커스텀 아레나 전투에 대한 상세한 설명을 제공해 주었습니다.
- igoticecream: 『옥토패스 트래블러 0』용 SDK를 생성해 주었고, 커스텀 이벤트 구현 방향을 잡는 데 도움을 주었습니다.
- MarvinCx886, JohnZ: 대륙의 패자에서 사운드 데이터를 이식하는 방법을 밝혀냈고, 그 과정을 매우 자세히 정리해 주었습니다.
