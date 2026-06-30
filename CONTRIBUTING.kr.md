# 기여
## **LANGUAGE:**  [English](CONTRIBUTING.md) | [日本語](CONTRIBUTING.jp.md) | [中文](CONTRIBUTING.zh.md) | [한국어](CONTRIBUTING.kr.md)

누구나 이 프로젝트에 기여할 수 있습니다. 본 저장소에는 pak 외에도, 모드의 각 요소가 어떻게 동작하는지에 대한 상세 문서, **『옥토패스 트래블러 0』** 모딩 전반에 대한 문서, 그리고 아직 완료되지 않은 작업 목록(진행 방향에 대한 간단한 논의 포함)이 포함되어 있습니다.

아래는 **『대륙의 패자』**에서 아직 이식되지 않은 기능 목록입니다. 각 항목은 현재 진행 상황을 설명하는 본 저장소의 markdown 파일로 연결됩니다:
- [~~퀘스트 및 이벤트~~ (해결 완료)](Resources/QuestEvent_Prog_kr.md)
- [이펙트 및 애니메이션](Resources/Animations_Prog_kr.md)
- [오브젝트](Resources/Objects_Prog_kr.md)
- [맵/지역/레벨](Resources/Maps_Prog_kr.md)
- [효과음 및 음악](Resources/Sound_Prog_kr.md)
- [『대륙의 패자』의 완전히 새로운 기능/코드 이식](Resources/Custom_Prog_kr.md)

핵심 작업은 UE 4.27 기반 『대륙의 패자』 에셋에서 가능한 한 많은 데이터를 추출하고, 그 데이터를 기반으로 『옥토패스 트래블러 0』용 Unreal Engine 5.4 신규 에셋을 작성한 뒤, 모드가 이를 올바르게 로드하도록 구성하는 것입니다. cooked된 UE 4.27 에셋을 UE 5.4로 범용 변환하는 수단이 존재하지 않기 때문에 이러한 과정이 필요합니다.

## CotCPort를 기반으로 한 모드
전술한 바와 같이, 이 모드를 기반으로 자유롭게 모드를 제작할 수 있습니다. 다만 **CotCPort** 관련 버그 리포트를 한 곳에 모으기 위해 이 페이지로의 링크를 부탁드립니다. pak는 파일명 알파벳순으로 덮어쓰기 로드되므로, 여러분의 모드 파일명을 적절히 변경하면 **CotCPort** 이후에 로드되도록 할 수 있습니다.

## 튜토리얼 및 리소스
아래는 본 모드의 **튜토리얼 및 리소스** 목록으로, 『대륙의 패자』에서 이식된 데이터를 활용해 여러분만의 모드를 제작하기 쉽게 해 줍니다.
- [본 모드 및/또는 OT0에서 에셋을 추출하고 다시 pak로 만드는 방법](Resources/Begin_kr.md)
- [캐릭터, 직업 추가·변경 및 주인공의 새 커스터마이즈 옵션 추가 방법](Resources/customchars_kr.md)
- [적 및 전투 추가·변경 방법](Resources/customenms_kr.md)
- [퀘스트 및 이벤트 추가·변경 방법 (WIP)](Resources/customqsts_ko.md)
- [스킬, 상태이상, Avail 추가·변경 방법 (WIP)](Resources/customskills_ko.md)
