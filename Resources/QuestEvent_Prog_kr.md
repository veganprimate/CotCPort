## **LANGUAGE:**  [English](QuestEvent_Prog_en.md) | [日本語](QuestEvent_Prog_jp.md) | [中文](QuestEvent_Prog_zh.md) | [한국어](QuestEvent_Prog_kr.md)

### Update (2/07/2026)
MarvinCx886가 자신의 **MioJoin** 모드를 작업하던 중 발견한 내용 덕분에, 이제 이벤트(Events) 이식이 가능해졌습니다. 원인은 단순히 제가 이벤트 애셋(event assets)의 메타데이터를 “새 경로”에 맞게 수정하는 것을 잊어버렸기 때문입니다. 이는 [UAssetGUI](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest)와 『옥토패스 트래블러 0』의 Unreal Mappings를 이용해 수정할 수 있습니다. `EventList`의 `m_Name` 및 `m_Version`은 실제로 `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` 경로로 해석(resolve)됩니다.

또한 OT0의 많은 맵에는 『옥토패스 트래블러: 대륙의 패자』(이하 『대륙의 패자』/CotC)에서 넘어온 “잔여 데이터”가 아직도 남아 있습니다. 예를 들어 게임에 등장하지 않는 NPC(예: 프로메/Promme)의 위치 및 콜리전(collision) 데이터 등이 그렇습니다(예: `QuTr_PC148_01_01`). 이런 오브젝트는 해당 `NpcSetList`의 `m_AppearLabel`에서 참조할 수 있으며, 정의되어 있는 콜리전 데이터를 그대로 사용해 해당 위치에 NPC를 스폰(spawn)시킬 수 있습니다.

<img width="1113" height="824" alt="image" src="https://github.com/user-attachments/assets/37b0af5c-c866-4ad6-b1c0-4d48986eba21" />


`.../Local/DataBase/Quest/` 및 `.../Local/DataBase/Event/` 하위 파일 구조와, 신규 이벤트/퀘스트를 추가하는 방법에 대한 상세 튜토리얼은 곧 공개될 예정입니다:  
[How to add custom quests and events](customqsts_en.md)

#### Current Issues
현재의 핵심 이슈는, 기존 맵에 신규 NPC를 추가하는 것과 완전히 새로운 지역(areas)을 이식하는 작업 쪽으로 대부분 이동했습니다. 자세한 내용은 [**Progress on Maps/Areas/Levels**](Maps_Prog_en.md) 를 참고하세요.

또한 OT0에서 정상적으로 보이도록/동작하도록 하기 위해, 이벤트/퀘스트/적/NPC 데이터의 상당 부분을 수동으로 조정해야 합니다:
 - 이식된 이벤트에서 NPC가 가끔 잘못된 방향을 바라봄

<img width="1167" height="649" alt="image" src="https://github.com/user-attachments/assets/a748fc6a-52a2-4223-ab8c-1e09743ae8e7" />

 - 『대륙의 패자』에서 이식한 퀘스트의 대화 중 “Yes / No” 선택지가 나올 때, OT0와는 반대로 기본 선택이 위쪽 항목의 “No”로 잡혀 잘못된 사운드가 재생됨(어느 쪽을 선택해도 어색해짐). 또한 취소 시 커서가 기본적으로 잘못된 위치로 돌아감
 - 보스의 강도 대비 권장 레벨이 보통 20~30레벨 정도 과도하게 높게 잡혀 있음
 - 『대륙의 패자』 보스는 전반적으로 EXP를 너무 많이 줌(예: 사제 크라토스/Priest Kratos는 약한 보스인데 HP가 약 100k이고 EXP 12k를 주었으며, 이는 OT0의 Or'sazantos보다 3배 많음)
 - 『대륙의 패자』에서 이식된 퀘스트 NPC는 기본적으로 이상한 FC 데이터(게임 내 용어 확인 필요)를 갖는 경우가 많음(아래 이미지 참고):

<img width="1239" height="667" alt="image" src="https://github.com/user-attachments/assets/c56a5b05-862f-45f2-a40a-cabe477d5fe6" />

 - 『대륙의 패자』는 종종 “보이지 않는 NPC”를 퀘스트 노드(Quest Nodes)로 사용하며, 가까이 가면 패스 액션(Path Action) / FC 데이터가 표시되어 버림(아래 이미지 참고):

<img width="1272" height="714" alt="image" src="https://github.com/user-attachments/assets/b5296bd4-fd61-4d75-a98a-1e05e391b349" />

 - 장면(Scene)이 “대화를 건 파티 멤버”를 기준으로 진행되며, 반드시 Zero/ the Ringbearer가 되는 것은 아님
 - 여러 퀘스트 과업(task)에 목적 문구(purpose text)가 없거나, 대신 과업 제목을 목적 문구처럼 사용해 OT0에서는 표시가 어색함
 - 『대륙의 패자』의 보상 타입은 OT0에서 동작이 다르거나 아예 기능하지 않는 경우가 많음. 자세한 내용은 [How to add custom quests and events](customqsts_en.md) 참고

### Original Article (CORRECTED)
<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/83c68154-8a8e-48b7-822c-c486f53cd60d" />

_이 지저분한 이미지에서도 알 수 있듯이, 『대륙의 패자』에서 퀘스트를 이식하는 것 자체는 분명히 가능합니다. 문제는 이벤트(Events) 이식이며, 이벤트는 컷신, 보스전 개시, 전투 중 대화, 던전 스위치 등 훨씬 다양한 요소를 담당합니다. 이벤트가 없다면 퀘스트는 사실상 퀘스트 마커와 보상만 남게 됩니다._

_`/Game/Content/Local/DataBase/Event/` 아래의 `EventList` 애셋을 보면, 게임이 이벤트 ID를 로드하는 시점에 `m_Name`과 `m_Version`이 `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` 경로로 해석될 것처럼 보이지만 ~~불행히도 그렇지 않습니다~~: ~~테스트 결과 게임은 이러한 항목을 신경 쓰지 않으며, 이벤트 ID를 이벤트 애셋으로 이어지는 경로에 내부적으로 연결해 둔 것으로 보였습니다~~_ **(이 키들은 해당 경로로 해석되지만, 기존 이벤트에 대해 값을 바꿔도 무시되는 것으로 보입니다)**.

_또한 동일한 이벤트라도, 어떤 이벤트 애셋에 새로운 package properties(패키지 프로퍼티)를 패치하느냐, 특히 어떤 폴더에 위치하느냐에 따라 게임 내에서의 재생 방식이 달라집니다._  
_예를 들어 VPartychat 아래의 이벤트는 배경이 자동으로 Wishvale 술집으로 설정되고, VTest는 그 안에서 재생될 수 있는 이벤트가 매우 제한되는 등, `EventList`의 `m_Kind` 키를 사실상 무시하는 동작을 보입니다._  **(이 현상은 현재도 여전히 그런 것으로 보입니다)**

_~~이는 EventList에 새 항목을 추가하는 것만으로는 커스텀 이벤트를 게임에 추가할 수 없으며, 엔진이 (혹은 곧) 완전한 Unreal 오브젝트 경로를 사용하게 되는 지점에서 파이프라인을 가로채야 함을 시사합니다~~._

_저는 ~~현재~~ PolyHook2를 사용해 이를 수행하는 UE4SS용 dll mod를 개발했으며, [이 모드](https://github.com/UE4SS-RE/RE-UE4SS/tree/main/cppmods/KismetDebuggerMod)를 템플릿으로 사용했습니다.  
또한 Dumper-7로 생성한 OT0 IDA 매핑(BravelyPath Modular Discord의 #resources에 있는 SDK 참고)을 활용해 IDA로 광범위하게 분석했고, 런타임에서 이벤트 ID를 오버라이드하는 데 성공했습니다._ **(중단됨 — dll mod에 대한 자세한 내용은 이전 커밋을 참고하세요)**
