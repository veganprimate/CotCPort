# 대륙패자 이식

<p align="center">
  <strong>옥토패스 트래블러 0 대륙의 패자 이식 모드</strong><br>
  <sub>원 프로젝트명: <code>CotCPort</code></sub>
</p>

## **언어:** [English](README.md) | [日本語](README.jp.md) | [中文](README.zh.md) | **한국어**

<br>

<p align="center">
<a href="#installation"><img alt="설치" src="https://img.shields.io/badge/%EC%84%A4%EC%B9%98-10363D?style=for-the-badge&logo=github&logoColor=white"></a>
<a href="Resources/HardMode_kr.md"><img alt="하드 모드" src="https://img.shields.io/badge/%ED%95%98%EB%93%9C%20%EB%AA%A8%EB%93%9C-880808?style=for-the-badge&logo=undertale&logoColor=white"></a>
<a href="#how-to-recruit-the-new-travelers"><img alt="여행자 영입" src="https://img.shields.io/badge/%EC%97%AC%ED%96%89%EC%9E%90%20%EC%98%81%EC%9E%85-10363D?style=for-the-badge&logo=bookstack&logoColor=white"></a>
</p>
<p align="center">
<a href="CONTRIBUTING.md"><img alt="기여" src="https://img.shields.io/badge/%EA%B8%B0%EC%97%AC-337A00?style=for-the-badge&logo=git&logoColor=white"></a>
<a href="https://discord.gg/7YmAFJ3h5Z"><img alt="연락처" src="https://img.shields.io/badge/%EC%97%B0%EB%9D%BD%EC%B2%98-30363D?style=for-the-badge&logo=discord&logoColor=white"></a>
<a href="#acknowledgements"><img alt="감사의 말" src="https://img.shields.io/badge/%EA%B0%90%EC%82%AC%EC%9D%98%20%EB%A7%90-484400?style=for-the-badge&logo=githubsponsors&logoColor=white"></a>
</p>

<p align="center"> <a href="#steam"><img alt="Steam" src="https://img.shields.io/badge/Steam-092226?style=flat-square&logo=steam&logoColor=white"></a> <a href="#microsoft-store"><img alt="Microsoft Store" src="https://img.shields.io/badge/Microsoft%20Store-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTUgNVY0YTMgMyAwIDAgMSA2IDB2MWgybDEgMTBIMkwzIDVoMlptMS4yIDBoMy42VjRhMS44IDEuOCAwIDEgMC0zLjYgMHYxWicvPjxwYXRoIGZpbGw9JyMwOTIyMjYnIGQ9J001IDhoMnYySDVWOFptNCAwaDJ2Mkg5VjhabS00IDNoMnYySDV2LTJAbTQgMGgydjJIOXYtMlonLz48L3N2Zz4%3D&logoColor=white"></a> <a href="#switch-real-hardwareatmosphère"><img alt="Switch 실기" src="https://img.shields.io/badge/Switch%20%EC%8B%A4%EA%B8%B0-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTYuNSAxSDNhMiAyIDAgMCAwLTIgMnYxMGEyIDIgMCAwIDAgMiAyaDMuNVYxWm0zIDBIMTNhMiAyIDAgMCAxIDIgMnYxMGEyIDIgMCAwIDEtMiAySDkuNVYxWk00LjQgNS4yYTEuMiAxLjIgMCAxIDAgMCAyLjQgMS4yIDEuMiAwIDAgMCAwLTIuNFptNy4yIDMuMmExLjIgMS4yIDAgMSAwIDAgMi40IDEuMiAxLjIgMCAwIDAgMC0yLjRaJy8%2BPHBhdGggZmlsbD0nIzA5MjIyNicgZD0nTTYuNSAyLjN2MTEuNEgzQS43LjcgMCAwIDEgMi4zIDEzVjNBLjcuNyAwIDAgMSAzIDIuM2gzLjVabTMgMEgxM2EuNy43IDAgMCAxIC43Ljd2MTBhLjcuNyAwIDAgMS0uNy43SDkuNVYyLjNaJy8%2BPC9zdmc%2B&logoColor=white"></a> <a href="#switch-emulator"><img alt="Switch 에뮬레이터" src="https://img.shields.io/badge/Switch%20%EC%97%90%EB%AE%AC%EB%A0%88%EC%9D%B4%ED%84%B0-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTUgNmg2YTQgNCAwIDAgMSAzLjcgMi41bC44IDIuMWEyLjMgMi4zIDAgMCAxLTMuNyAyLjVMMTAuNCAxMkg1LjZsLTEuNCAxLjFBMi4zIDIuMyAwIDAgMSAuNSAxMC42bC44LTIuMUE0IDQgMCAwIDEgNSA2Wm0tLjcgMnYxLjJIMy4xdjEuMmgxLjJ2MS4yaDEuMnYtMS4yaDEuMlY5LjJINS41VjhINC4zWm02LjcuN2EuOS45IDAgMSAwIDAgMS44LjkuOSAwIDAgMCAwLTEuOFptMiAxLjdhLjkuOSAwIDEgMCAwIDEuOC45LjkgMCAwIDAgMC0xLjhaJy8%2BPC9zdmc%2B&logoColor=white"></a> </p>


<p align="center">
<a href="CONTRIBUTING.md#mods-using-the-cotcport-as-a-basis"><img alt="대륙패자 이식 기반 모드" src="https://img.shields.io/badge/%EB%8C%80%EB%A5%99%ED%8C%A8%EC%9E%90%20%EC%9D%B4%EC%8B%9D%20%EA%B8%B0%EB%B0%98%20%EB%AA%A8%EB%93%9C-1A4000?style=flat-square&logo=curseforge&logoColor=white"></a>
<a href="CONTRIBUTING.md#tutorials-and-resources"><img alt="튜토리얼 & 자료" src="https://img.shields.io/badge/%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC%20%26%20%EC%9E%90%EB%A3%8C-1A4000?style=flat-square&logo=readthedocs&logoColor=white"></a>
<a href="CONTRIBUTING.md#variety-of-resources-unordered"><img alt="리소스 목록" src="https://img.shields.io/badge/%EB%A6%AC%EC%86%8C%EC%8A%A4%20%EB%AA%A9%EB%A1%9D-1A4000?style=flat-square&logo=files&logoColor=white"></a>
</p>

<br>
<table border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td width="50%">
      <img alt="1" src="https://github.com/user-attachments/assets/5aaa0224-4c69-47bf-9fe3-4d9f1f9ee429" width="100%" />
    </td>
    <td width="50%">
      <img alt="2" src="https://github.com/user-attachments/assets/35bbf069-f2ce-4ed9-b6d5-be0ec8975fa6" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="3" src="https://github.com/user-attachments/assets/1c96f251-98f2-49d4-870d-3e0ae2af4f97" width="100%" />
    </td>
    <td width="50%">
      <img alt="4" src="https://github.com/user-attachments/assets/9a385a1d-4448-496e-b977-db4bd23676ac" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="5" src="https://github.com/user-attachments/assets/20e1337d-4bbc-4fb4-b3ef-6c597d6652f4" width="100%" />
    </td>
    <td width="50%">
      <img alt="6" src="https://github.com/user-attachments/assets/c3c4286a-2f19-4574-b469-5ff4e750f81e" width="100%" />
    </td>
  </tr>
  <tr>
    <td colspan="2">
      <img alt="7" src="https://github.com/user-attachments/assets/2cd24e80-9b29-4376-a4d5-3d0a54be1529" width="100%" />
    </td>
  </tr>
</table>

**『옥토패스 트래블러 0』**용 모드로, **『옥토패스 트래블러: 대륙의 패자』**(일본판/글로벌판/중국판)와 시리즈의 다른 작품에서 가져온 콘텐츠를 가능한 한 많이 이식하고 통합하는 것을 목표로 합니다.
- **『대륙의 패자』에 등장하는 280명 이상의 캐릭터**를 **어빌리티 보드**, **음성 대사**, **캐릭터 일러스트**, **위시베일 전용 연동**까지 포함해 플레이어블화합니다. 또한 **완전 신규 제작 플레이어블 캐릭터 15명**(_학자 노모스, 프라, 피에로, 프리드, 휴고, 기멜 선장, 마티아스, 미겔, 에어하르트, 크리스 크로스포드, 젤리그, 갈데라의 무녀 리아나, 영광의 루시안, 프로스트워커 프로메, 바네사 하이젤_)도 포함합니다.
- **마물 투기장**에서 **『대륙의 패자』 기반 신규 보스 수백 종**과 전투할 수 있습니다. 추가로 **완전 신규 제작** 고난도 전투도 2종 포함되어 있으며, 하나는 **바르젤로 패밀리**, 다른 하나는 **브레이블리 3인조**와의 전투입니다.
- 마물 투기장의 신규 적을 격파하면 **『대륙의 패자』 기반 신규 액세서리**와 **장비** 수십 종을 해금할 수 있습니다. 적이 강할수록 더 좋은 보상을 얻습니다.
- 캐릭터별 **트래블러 스토리**를 포함해 **『대륙의 패자』 기반 신규 퀘스트** 수십 개를 플레이할 수 있습니다.
- 회상록의 각 위치에 **파티 대화**로 편입한 **수백 개**의 회상 이벤트를 볼 수 있습니다.
- **『대륙의 패자』에서 이식하고 C++ 모드로 구현한 완전히 새로운 상태이상/상태 효과**를 경험할 수 있습니다.

현재 **개발 중 / 미완성 / 구현 예정** 기능은 다음을 포함합니다.

- **오르스테라 맵에서도 작동하는** **실시간 심리스** 낮/밤 전환. 밤의 오르스테라 맵에는 『옥토패스 트래블러』 세계관에 맞춘 **전용 NPC 배치 프리셋**을 준비할 예정입니다.
- **『대륙의 패자』, 『옥토패스 트래블러』, 『옥토패스 트래블러 II』**의 대응 맵을 바탕으로 재구성한 **500개 이상**의 신규 맵. **솔리스티아** 및 **이프 오르스테라**의 **신규 월드맵**도 포함됩니다.
- 『옥토패스 트래블러』 및 『옥토패스 트래블러 II』의 **사이드 퀘스트**를 **『옥토패스 트래블러 0』용으로 재구성**한 버전.
- **기본 게임**과 **『대륙의 패자』／『옥토패스 트래블러』／『옥토패스 트래블러 II』 기반 추가 콘텐츠**의 난도를 크게 올리는 _선택 설치_ **하드 모드 버전**. 자세한 내용은 [하드 모드 버전](https://github.com/veganprimate/CotCPort_Canary/blob/main/Resources/HardMode_kr.md)을 참조하세요.
- 『옥토패스 트래블러』 1편에서 강하게 영감을 받은, 크리스 크로스포드를 중심으로 한 **신규 퀘스트라인**. **신규 보스전**도 포함됩니다.
- **『대륙의 패자』의 대규모 서브시스템**을 구현하기 위한 **커스텀 C++ 모드**. 예:
  - **신수** 관련 전투 옵션
  - **저력**
  - **펫**
  - 빅터스 할로우 투기장의 **토너먼트**용 커스텀 **UI** 및 **게임플레이** 로직
  - 무기·액세서리의 **등급(Grade)**, **이프 오르스테라 회랑**의 로그라이크 요소
  - **시련의 탑**
  - **이름 없는 마을** 등 **새 건축 유형**
  - 신규 콘텐츠의 **위시베일 심화 연동**
  - ……등

본 모드는 다음 언어를 지원합니다.
- **영어**
- **일본어**
- **한국어**
- **중국어(간체)**
- **중국어(번체)**

![chargif](https://github.com/user-attachments/assets/00c35b82-3e9c-4810-8d8b-657f2c2a1151)

[worldmap_showcase.mp4](https://github.com/user-attachments/assets/e669f527-8412-4f91-8799-483e572caa32)

<a id="installation"></a>
## 설치
[Releases](https://github.com/veganprimate/CotCPort/releases)에서 최신 (프리)릴리스의 `CotCPort_Base.7z`와 `CotCPort_Extras.7z` 아카이브를 다운로드하세요.

 - `CotCPort_Base.7z`에는 **반드시 설치해야 하는 기본 모드 파일**이 들어 있습니다. DLL 모드들은 서로 의존합니다.
 - `CotCPort_Extras.7z`는 DLL 모드와 pak 모드를 모아 둔 **선택 사항 모음**입니다. **필수인 것은 없으며**, **서로 의존하지도 않습니다**.

아카이브는 게임 내부 폴더 구조를 반영하도록 구성되어 있습니다.
```
OCTOPATH TRAVELER 0/
└── Octopath_Traveler0/
    ├── Binaries/
    │   └── Win64/
    └── Content/
        └── Paks/
```
목표는 아카이브의 파일이 기존 `Octopath_Traveler0` 폴더에 바로 **병합**되도록 하는 것입니다. 다음처럼 불필요하게 중첩된 폴더를 만들지 않도록 주의하세요.
```
Octopath_Traveler0/Octopath_Traveler0/...
```
또는:
```
Octopath_Traveler0/CotCPort_Base/Octopath_Traveler0/...
```
두 아카이브 내부 파일에 대한 자세한 설명은 [v1.1.0-pre](https://github.com/veganprimate/CotCPort/releases/tag/v1.1.0-pre) 릴리스 노트의 “아카이브 구성” 섹션을 참조하세요.

설치 과정에서 `Binaries/Win64/` 아래의 여러 DLL을 **교체**해야 할 수 있습니다. 또한 게임을 시작하기 전에 대륙패자 이식(CotCPort) v1.0.0의 `CotCPort_notex_p.pak`가 `Content/Paks/`에서 **제거**되어 있는지 확인하세요. `CotCPort_DataBase_p.pak`가 그것을 대체하도록 되어 있지만, 파일명이 다르기 때문에 자동으로 대체되지는 않습니다.

**경고:** 현재 대륙패자 이식(CotCPort)용으로 개발된 **C++ 모드**의 **Switch 호환 버전**도, hook/detour 등을 설치하기 위한 동등한 **네이티브 런타임 계층**도 없습니다. Switch 버전은 모드가 기반으로 한 v1.0.8.0이 아니라 『옥토패스 트래블러 0』 v1.0.4.0을 기반으로 합니다.

C++ 모드 로더가 『옥토패스 트래블러 0』 v1.0.8.0의 Microsoft Store 버전과 **호환되는지**는 아직 **테스트되지 않았습니다**. MS Store Win64 Shipping 실행 파일은 Steam판과 다를 수 있으며, 이 경우 RVA가 밀릴 수 있습니다.

Nintendo Switch 및 MS Store 플레이어는 프리릴리스에서 **pak만** 사용하는 것으로 제한해야 할 수도 있습니다. 자세한 내용은 아래를 참조하세요.

<a id="steam"></a>
### Steam
`steamapps\common\OCTOPATH TRAVELER 0\Octopath_Traveler0\` 아래에 아카이브를 압축 해제하세요.

<a id="microsoft-store"></a>
### Microsoft Store
1. 모든 pak의 접미사를 소문자 `_p`에서 대문자 `_P`로 변경하세요.
2. PC에서 **Xbox 앱**을 열고, 왼쪽 사이드바의 설치된 게임 목록에서 **OCTOPATH TRAVELER 0**를 우클릭한 뒤 **관리**를 선택하세요.
3. **파일** 탭을 클릭하세요.
4. **찾아보기**를 클릭하세요.
5. `<game install folder>\Octopath_Traveler0\` 아래에 아카이브를 압축 해제하세요.

**경고:** MS Store판 『옥토패스 트래블러 0』와 C++ Modding API의 호환성은 아직 **입증되지 않았습니다**. 사용자의 시스템에서 결과가 **부정적**이라면(`Binaries/Win64/KingshipLogs/` 아래에 로그가 생성되었는지 확인), `CotCPort_BluePrints_P.pak`는 C++ 모드 `CotCPort_Menus` 및 `CotCPort_BattleUI`에 의존하므로 `Content/Paks/` 폴더에서 **제거**하세요.

<a id="switch-real-hardwareatmosphère"></a>
### Switch(실기 / Atmosphère)
Switch 버전은 **pak 전용**으로 취급해야 합니다. **Windows C++ 모드는 Switch에서 작동하지 않으며**, **대체물도 아직 만들어지지 않았습니다**.
Switch 설치는 **Atmosphère LayeredFS**를 사용합니다. 게임 설치 내부의 파일을 직접 교체하는 것이 아닙니다. 대신 수정된 RomFS 스타일 파일을 SD 카드에 배치하면, 게임 시작 시 Atmosphère가 이를 overlay합니다.
『옥토패스 트래블러 0』 타이틀 ID는 `01005270232F2000`입니다. SD 카드에 다음 폴더를 만드세요.
```
sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/
```
그 안에 **`CotCPort_BluePrints_p.pak`를 제외한** 모든 pak를 넣으세요.
```
sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_BluePrints_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Maps_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Sounds_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Textures_p.pak
```
`CotCPort_Extras.7z` 안의 모든 pak도 호환됩니다.

<a id="switch-emulator"></a>
### Switch(에뮬레이터)
실기와 마찬가지로, 같은 이유에서 이 플랫폼도 **pak 전용**으로 취급해야 합니다.
다음 **내부** 구조를 가진 모드를 만드세요.
```
romfs/
└── Octopath_Traveler0/
    └── Content/
        └── Paks/
             ├── CotCPort_DataBase_p.pak
             ├── CotCPort_Maps_p.pak
             ├── CotCPort_Sounds_p.pak
             ├── CotCPort_Textures_p.pak
             └── (+ CotCPort_Extras.7z 안의 임의 pak 모드)
```

### 기타 플랫폼
여기에서 언급한 플랫폼 외에도 『OCTOPATH TRAVELER 0』는 PS4, PS5, Xbox Series X|S, Switch 2에서 이용할 수 있습니다. 이 플랫폼들에는 pak 모드조차 설치할 수 있는 실용적이고 일관된 방법이 **없습니다**.

<details>
<summary><strong>대륙패자 이식 호환성 표 보기</strong></summary>

| 플랫폼 | 대륙패자 이식 지원 상태 | 구체적인 Homebrew / 모딩 스택 명칭 | 언급할 가치가 있는 구체적 경로 / 식별자 | 『옥토패스 트래블러 0』 Unreal `.pak` 모드에서 중요한 이유 | 판정 |
| --- | --- | --- | --- | --- | --- |
| **Steam PC** | **완전 지원 대상** | Windows DLL 로딩, `dwmapi.dll` 프록시/로딩 동작, `OT0KingshipLoader.dll`, `KingshipMods`, `KingshipConfig` | `Octopath_Traveler0/Binaries/Win64/`; `Octopath_Traveler0/Content/Paks/`; Steam Win64 Shipping 실행 파일 대상 | 대륙패자 이식(CotCPort)의 두 축, 즉 Unreal pak 파일과 Windows C++ 런타임 hook/detour가 모두 성립하는 유일한 플랫폼입니다. | **주 지원 플랫폼** |
| **Microsoft Store / PC용 Xbox 앱** | **pak 전용 가능성이 높으며, DLL 계층은 실험적** | 동일한 Windows 파일 시스템. 다만 MSIX/Xbox 앱 설치 구조가 다를 수 있고 실행 파일이 Steam RVA와 일치하지 않을 수 있음 | `Octopath_Traveler0/Content/Paks/`; pak 접미사는 `_p.pak` 대신 `_P.pak`가 필요할 수 있음 | 게임이 loose/추가 pak를 받아들인다면 pak는 로드될 수 있지만, **Win64 실행 파일이 Steam판과 다르면 C++ hook이 깨질 수 있습니다**. | **pak 전용 / 실험적** 지원 |
| **Switch 1 실기 / Atmosphère** | **pak 전용 지원은 그럴듯하고 문서화 가능**. Switch `.nso` 모듈은 DLL 대체가 되기 어려움 | Atmosphère LayeredFS, RomFS overlay, 타이틀별 `contents` 폴더 | `sd:/atmosphere/contents/<title_id>/romfs/`; 『옥토패스 트래블러 0』: `sd:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/` | Atmosphère LayeredFS는 게임 RomFS 위에 파일을 덮어씌울 수 있습니다. pak 전용 모드에는 충분히 가까운 방식이지만 Windows DLL 모드는 불가능합니다. | **pak 전용** |
| **Switch 1 에뮬레이터** | **pak 전용 지원은 그럴듯함**, 위와 동일 | `romfs/` 루트를 가진 에뮬레이터 모드 폴더, Yuzu/Ryujinx/Ryubing/Eden 계열 RomFS 교체 | `<mod folder>/romfs/Octopath_Traveler0/Content/Paks/*.pak` | 개념적으로 Atmosphère LayeredFS와 같지만 에뮬레이터 모드 디렉터리 처리를 거칩니다. 에뮬레이터는 Windows 빌드가 아니라 Switch 빌드를 실행하므로 DLL은 적용되지 않습니다. | **pak 전용** |
| **Switch 2** | **미지원** | 공개된 Atmosphère 상당 CFW 스택 없음, 공개된 LayeredFS 상당 모드 워크플로 없음 | `sd:/atmosphere/contents/...`를 재사용하지 말 것. 사용자를 위한 확립된 Switch 2 `romfs` 오버레이 경로 없음 | 공개 보도는 kernel/root에 영향을 주지 않고 jailbreak/homebrew 실용성도 없는 소규모 Switch 2 userland ROP 시연 정도에 그칩니다. | **미지원** |
| **Xbox Series X\|S 리테일 콘솔** | **미지원** | 리테일 Xbox 보안 모델. 리테일 게임과 UWP Dev Mode 앱은 별개 | 사용자가 접근할 수 있는 `Content/Paks/` 없음, LayeredFS식 `romfs` overlay 없음, `sdmc` 상당 없음 | Xbox 리테일 콘솔은 임의의 리테일 게임 콘텐츠를 위한 일반 모드 폴더를 공개하지 않습니다. Dev Mode는 UWP 앱 배포용이지 설치된 리테일 게임 패치용이 아닙니다. | **미지원** |
| **Xbox Series X/S Dev Mode** | **대륙패자 이식에는 유용하지 않음** | UWP 앱 배포, Dev Home, Device Portal, homebrew식 UWP 앱/에뮬레이터 | Dev Mode 앱 패키지이며 리테일 게임 설치 디렉터리가 아님 | 자체 UWP 앱을 배포할 수는 있지만, 리테일 Xbox 『옥토패스 트래블러 0』 설치본에 `.pak` 파일을 주입하는 지원 경로는 아닙니다. | **미지원: Dev Mode ≠ 리테일 게임 모딩** |
| **PS4 리테일 / stock firmware** | **미지원** | stock Orbis OS, 서명된 패키지, 사용자 pak 폴더 없음 | 사용자에게 노출되는 `Octopath_Traveler0/Content/Paks/` 상당 경로 없음 | stock PS4는 설치된 리테일 게임에 임의의 Unreal pak를 주입하기 위한 일반 사용자 쓰기 위치를 제공하지 않습니다. | **미지원** |
| **PS4 homebrew / GoldHEN 환경** | **연구 전용, README 지원 대상으로 삼기 어려움** | GoldHEN, PPPwn 계열 지원, Itemzflow, PS4 Cheats Manager, GoldHEN XML patch engine | GoldHEN patches: `/user/data/GoldHEN/patches/xml/<TitleID>.xml`; Itemzflow app logs: `/user/app/ITEM00001/logs/itemzflow_app.log`; daemon logs: `/data/itemzflow_daemon/daemon.log`; Itemzflow title ID: `ITEM00001` | 이 생태계는 dump, fake package, patch, trainer, XML memory patch 등을 지원하지만, **깔끔한 loose-pak overlay와는 다릅니다**. | **미지원 / 연구 전용**. 누군가 PS4 패키지/패치 워크플로를 구축하고 테스트해야 함 |
| **PS4 패키지 재구축 방식 워크플로** | **전용 포트라면 이론상 가능, 일반 설치는 아님** | dump/복호화된 package, GP4, fake package rebuild, app/update package 처리 | 런타임 게임 mount는 PS4 도구 문맥에서 `app0:/`로 자주 언급되지만, 사용자용 patch 도구는 `/user/data/GoldHEN/patches/xml/` 같은 경로를 사용 | pak 모드라면 dump한 game/update package 안의 pak 파일을 교체하거나 추가한 뒤 재구축/재설치하는 방식이 연구 경로가 될 가능성이 큽니다. 이는 일반적인 “여기에 pak 복사” 가이드를 훨씬 벗어납니다. | PS4 패키징 경험이 있는 유지보수자 필요 |
| **PS5 리테일 / stock firmware** | **미지원** | stock PS5 OS, 서명된 package, 사용자 pak 폴더 없음 | `Content/Paks/` 상당 경로 없음, LayeredFS식 공개 mod path 없음 | PS4와 같은 큰 문제가 있으며, homebrew 장면은 더 미성숙하고 파편화되어 있습니다. | **미지원** |
| **PS5 homebrew / etaHEN 환경** | **연구 전용** | etaHEN, Kstuff, DPIv2, Itemzflow PS5 builds, XML patch/plugin ecosystem | etaHEN 릴리스 노트는 firmware `8.40–10.01` cheats 지원, Kstuff 메뉴, custom background package installer, DPIv2 관련 기능을 언급합니다. Itemzflow 1.09 PS5 노트는 `5.xx` 및 `6.xx` 지원, etaHEN toolbox 설정, XML patches plugin, 실험적 self-decrypter를 언급합니다. | PS5 homebrew 도구는 존재하지만, 그것이 『옥토패스 트래블러 0』의 loose `.pak` 설치 경로를 뜻하지는 않습니다. 기존 지원은 Atmosphère식 RomFS overlay보다 package 관리, trainer, cheat, patch, dump에 가깝습니다. | **미지원 / 연구 전용** |
| **PS4/PS5 XML patch ecosystem** | **대륙패자 이식 pak 파일에는 직접 유용하지 않음** | Illusion/GoldHEN patch XML, Itemzflow XML patches plugin, PS4 Cheats Manager | 예: `<TitleID><ID>CUSA01765</ID></TitleID>` 및 `<Metadata ... AppVer="01.00" AppElf="eboot.bin">`; patch는 `CUSA00001.xml` 같은 타이틀별 XML에 저장 | 메모리/코드 값, 디버그 메뉴, 바이트 등을 patch할 수는 있지만 Unreal asset/data pak 파일을 설치하지는 못합니다. CotCPort에 나중에 작은 실행 파일 patch가 필요할 때만 유용할 수 있습니다. | **pak 설치에는 해당 없음** |
| **PS4/PS5 Itemzflow** | **유용한 신호지만 대륙패자 이식 지원에는 불충분** | Itemzflow Game Manager, PS4/PS5 downloads, `ITEM00001` package identity | PKG-Zone은 Itemzflow를 PS4 home menu 대체로 소개하며 content ID `IV0002-ITEM00001_00-STOREUPD00000000`, title ID `ITEM00001`, PS4/PS5 downloads를 제시합니다. | PS4/PS5 homebrew 도구가 존재한다는 증거는 되지만, 『옥토패스 트래블러 0』가 loose pak overlay를 로드할 수 있다는 증거는 아닙니다. | **연구 전용** |
</details>

## 다른 모드와의 호환성
대륙패자 이식은 거의 모든 다른 pak 모드와 호환되지 않을 가능성이 큽니다. DLL 모드 역시 대부분 `CotCPort_DataBase` 내부 규약에 의존하지만, 이는 설정으로 조정할 수 있습니다.
<details>
<summary><strong>CotCPort_DataBase 파일 트리 보기</strong></summary>

```
CotCPort_DataBase
├── AIBattle
│   ├── TacticalActionList
│   ├── TacticalAssignList
│   ├── TacticalList
│   └── TacticalSkillList
├── Battle
│   ├── BattleAbortConditions
│   ├── BattleActionID
│   ├── BattleCameraParams
│   ├── BattleCameraSet
│   ├── BattleEventCommand
│   ├── BattleEventList
│   ├── BattleParams
│   ├── BattlePositions
│   └── BattleVoiceList
├── Character
│   ├── CharacterAnimationID
│   ├── CharaGuest
│   ├── CharaID
│   ├── CharaNpcList
│   ├── CharaPlayer
│   ├── CharaPlayerSpecialSkillList
│   ├── CharaRuneInitialOpenTypeID
│   ├── CharaSkillLearningPoint
│   ├── CharaSpecialStrengthenPoint
│   ├── CharaStatusID
│   ├── JobID
│   ├── LevelParameterTable
│   ├── LevelPointTable
│   └── PlayerMemberSet
├── Effect
│   ├── AttachEffect
│   ├── EffectList
│   └── PlayerAttachEffectList
├── Enemy
│   ├── EncountEffectTypeID
│   ├── EncountVolume
│   ├── EnemyBattleAnimSet
│   ├── EnemyDropID
│   ├── EnemyFormations
│   ├── EnemyGroups
│   ├── EnemyID
│   ├── EnemyModeID
│   ├── EnemyParts
│   ├── EnemyReinforcements
│   ├── EnemyTypeID
│   ├── EnemyWeakChangeID
│   ├── EnemyWeakID
│   ├── EnemyWeakLockID
│   ├── SymbolEnemyGroupList
│   └── SymbolEnemyList
├── Event
│   ├── EventFinishProcessList
│   ├── EventFlagVariableList
│   ├── EventList
│   ├── VJoin
│   │   └── (『대륙의 패자』 JP/CN/GL의 이벤트 8288개 전체를 『옥토패스 트래블러 0』 형식의 Join 이벤트로 가져옴. 『옥토패스 트래블러 0』 기본 Join 이벤트는 수정하지 않음) 
│   └── VSub
│        └── SQ 
│            └── (「영웅의 섬・과거」 이벤트를 수정하여 각 무기를 해당 직업 타워 보스가 지키도록 변경) 
├── GameText
│   ├── Localize
│   │   ├── EN-US
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   ├── KO-KR
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   ├── ZH-CN
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   └── ZH-TW
│   │       ├── SystemText
│   │       │   ├── GameTextCharacter
│   │       │   ├── GameTextEnemy
│   │       │   ├── GameTextFC
│   │       │   ├── GameTextItem
│   │       │   ├── GameTextMap
│   │       │   ├── GameTextPC
│   │       │   ├── GameTextQuest
│   │       │   ├── GameTextSkill
│   │       │   └── GameTextUI
│   │       └── TalkText
│   │           ├── GameTextEvent
│   │           └── GameTextNPC
│   ├── SystemText
│   │   ├── GameTextCharacter
│   │   ├── GameTextEnemy
│   │   ├── GameTextFC
│   │   ├── GameTextItem
│   │   ├── GameTextMap
│   │   ├── GameTextPC
│   │   ├── GameTextQuest
│   │   ├── GameTextSkill
│   │   └── GameTextUI
│   └── TalkText
│       ├── GameTextEvent
│       └── GameTextNPC
├── Item
│   └── ItemList
├── Level
│   └── ExistSublevelParam
├── Map
│   ├── MapLightSet
│   ├── MapListTable
│   ├── MapPathListTable
│   ├── MapRegionCategory
│   ├── MapReplaceList
│   ├── TownList
│   ├── WorldList
│   ├── WorldMapList
│   ├── WorldMapMaskList
│   └── WorldMapRouteList
├── MonsterArena
│   └── MonsterArenaList
├── Narration
│   └── NarrationList
├── Npc
│   └── (929개 에셋. 『옥토패스 트래블러 0』와 『대륙의 패자』가 공유하는 모든 NpcSetList/NpcTalkList에 『대륙의 패자』 측 데이터를 보강했고, 『옥토패스 트래블러 0』에 없는 맵의 NPC 데이터는 『옥토패스 트래블러 0』 형식으로 가져옴) 
├── Object
│   ├── ObjectList
│   ├── RecoveryPointList
│   ├── TreasureBoxList
│   └── TreasureBoxRewardList
├── PartyChat
│   └── PartyChatList
├── Quest
│   ├── LimitConditions
│   ├── MainStoryRingList
│   ├── QuestAggregatedPoint
│   ├── QuestAggregatedTaskList
│   ├── QuestList
│   ├── QuestTaskList
│   └── QuestTrigCondition
├── Skill
│   ├── ResistType
│   ├── SkillAilmentOffsets
│   ├── SkillAilmentSkillReplaceList
│   ├── SkillAilmentType
│   ├── SkillAvailID
│   ├── SkillAvailMagnificationConditionList
│   ├── SkillConditionList
│   ├── SkillEffectiveID
│   ├── SkillEnhancedList
│   ├── SkillHitRateList
│   ├── SkillIconList
│   ├── SkillID
│   ├── SkillResistAilmentID
│   ├── SkillResistList
│   ├── SkillUniqueIconID
│   ├── SkillVisitorList
│   ├── SpecialSkillGrowthList
│   ├── SpecialSkillID
│   └── WeaponType
├── SkillBoard
│   └── SkillBoardData
├── Sound
│   ├── PartVoiceID
│   ├── SoundList
│   ├── SoundSheetList
│   ├── VoiceEffectID
│   └── VoiceSymbolList
├── Texture
│   ├── CharaTexID
│   ├── EnemyTexID
│   ├── LinkIconList
│   └── TextureID
├── Trigger
│   ├── CurveID
│   └── LightAreaPreset2
└── Village
    ├── VillageBuildingData
    ├── VillageResidentFavoriteBuilding
    ├── VillageResidentInterior
    └── VillageResidentSkill
```

</details>

<a id="how-to-recruit-the-new-travelers"></a>
## 신규 여행자 영입 방법
현재 캐릭터의 **트래블러 스토리**가 영입 이벤트로 기능하지만, 이는 새로 추가된 280명 이상 캐릭터 중 140명만 해당합니다. 이 모드의 모든 신규 플레이어블 캐릭터에 **고유 영입 이벤트**를 추가할 계획이지만, 아직 시간이 더 필요합니다.

현재는 `CotCPort_Extras.7z` 아카이브 안의 **선택 사항** pak 모드 `RecruitEvent_p.pak`를 사용해 이 모드가 제공하는 모든 캐릭터를 한 번에 영입할 수 있습니다.
이를 위해 아래 그림의 **위시베일 길**로 가서 **깜빡하는 다람쥐**에게 말을 거세요.

<img width="1239" height="541" alt="image" src="https://github.com/user-attachments/assets/a39b0888-f820-462b-81c0-1a747ab59528" />

**네 가지 선택지**가 표시됩니다.
 1. 모든 모드 캐릭터 영입 + 위시베일에 추가
 2. 모든 모드 캐릭터 영입
 3. 모든 모드 캐릭터 영입 해제
 4. 모든 모드 캐릭터를 위시베일에서 제거

모드 업데이트로 신규 플레이어블 캐릭터가 추가되면, `RecruitEvent_p.pak`가 pak 폴더에 있는 한 **같은 NPC에게 다시 말을 걸어** 영입할 수 있습니다.
이미 영입된 캐릭터의 경우 `EntryPlayerCharacter`가 `false`를 반환하므로 눈에 보이는 가입 연출은 **억제**됩니다. 또한 opcode `10029  EvCmAddPartyMember`는 대사를 강제하지 않습니다. 그렇다고 이미 영입된 캐릭터에 대해 `10029`가 건너뛰어진다는 뜻은 아닙니다. 실제로 하는 일이 거의 없을 뿐입니다. 이미 영입된 플레이어도 처리 대상이 되어 HP/SP 회복, 파티 내 위치 지정, 활성 파티 cue sheet 갱신 등이 일어날 수 있습니다. 장비나 장착 스킬을 잃거나, 스탯이 초기화되거나, 두 번 영입되지는 **않습니다**.

**경고 1:** 영입 이벤트가 **위시베일 길**에 있으므로 캐릭터를 너무 이르게 영입하지 않도록 주의하세요. 프롤로그 중에는 모든 파티원의 **장비 슬롯이 잠겨** 있습니다. 이 퀘스트 상태에서 영입한 캐릭터도 장비 슬롯이 잠깁니다. 이 효과는 프롤로그 완료 후에도 **자동으로 해제되지 않습니다**. 영향을 받은 캐릭터는 모두 **영입 해제 후 재영입**해야 합니다.

**경고 2:** 영입되는 캐릭터의 스탯은 **다람쥐와 상호작용한 캐릭터**, 즉 파티 리더(편성 메뉴 왼쪽 위 캐릭터)에 따라 스케일됩니다.

## 연락처
**BravelyPath Modular** Discord 서버의 **#cotc-characters-mod** 채널에서 저에게 연락할 수 있습니다. 이 채널에는 다른 기여자와 모더들도 함께 있습니다. 서버는 다음 링크로 참여할 수 있습니다: https://discord.gg/7YmAFJ3h5Z

<a id="acknowledgements"></a>
## 감사의 말
다음 분들께 감사드립니다.
- **BoltGSR** 및 **MarvinCx886**: 『대륙의 패자』 일본판 데이터 제공
- **Oersted**: 『대륙의 패자』 중국판 데이터, 완전한 스킬시트, 스프라이트시트, 커스텀 유닛 아이디어 제공 및 도움
- **Vat**: 캐릭터와 적의 확장 스프라이트시트 제공, 많은 텍스처가 게임 내에서 선명하게 보이도록 cooking 작업
- **Solo Traveler**: 다양한 기능의 플레이테스트, 하드 모드 버전에 대한 아이디어 제공, 커스텀 아레나 전투에 대한 상세한 설명
- **igoticecream**: 개발 중 자주 참고한 『옥토패스 트래블러 0』 SDK 생성
- **MarvinCx886**: 커스텀 이벤트를 올바르게 구현하는 방법 규명, 초기 지도 이식 시도, 귀중한 통찰 제공, 마물 투기장 스크롤 크래시 수정 아이디어 제공
- **MarvinCx886** 및 **JohnZ**: 『대륙의 패자』에서 Sound 데이터를 이식하는 방법 규명과 그에 대한 자세한 문서 작성
