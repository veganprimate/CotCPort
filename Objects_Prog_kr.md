## **LANGUAGE:**  [English](Objects_Prog_en.md) | [日本語](Objects_Prog_jp.md) | [中文](Objects_Prog_zh.md) | [한국어](Objects_Prog_kr.md)

`/Object` 폴더에는 주로 월드/게임플레이용 소품(props)과 그 표현(프레젠테이션)용 에셋이 들어 있습니다. 보통은 다음과 같은 것들입니다:
- **Static Mesh**(소품, 환경 조각, 상호작용 오브젝트)
- 해당 메시가 사용하는 **텍스처**(알베도/컬러, 노멀, 마스크 등)
- 텍스처를 적용하기 위한 **머티리얼**/**머티리얼 인스턴스**
- (가끔) 오브젝트 리소스 폴더 안에 함께 들어 있는 Paper2D **스프라이트/플립북**(예: 대륙의 패자의 `Obj_Dice`)

[이펙트 및 애니메이션](Animations_Prog_kr.md)와 마찬가지로, 대륙의 패자 에셋 파일을 UE5로 직접 임포트할 수는 없고, JSON/GLB/PNG 파일만을 사용해 에셋을 “재구성”해야 합니다.
또한 이에 맞춰 다단계 프로세스를 구성합니다(이 역시 아직 미검증이지만, `Effect`에 대한 방식보다는 더 유망해 보입니다):

### Stage 0 (매니페스트 만들기 / build a manifest)
cooked 덤프는 상당히 혼란스럽기 때문에, 매니페스트가 있으면 결정적(deterministic)인 맵을 얻을 수 있습니다:
- Object → 메시 JSON 경로
- Object → 발견된 텍스처 목록
- Object → 머티리얼 설정
- Object → 스프라이트 프레임 JSON 경로
또한 FModel이 내보낸 JSON은 JSON 오브젝트가 이어 붙어 있거나 “extra data” 때문에 파싱에 실패하는 경우가 자주 있으므로, 다시 한 번 “관대한(lenient) JSON 로더”를 만드는 것이 필요합니다.

### Stage 1 (원본 리소스 임포트: PNG → `Texture2D`, GLB → StaticMesh)
PNG는 UE5에 `Texture2D`로, GLB는 `StaticMesh`로 임포트합니다. 또한 용도에 따라 임포트 설정을 달리 적용합니다:
- Sprites / Paper2D
  - UI2D 압축
  - mips 비활성화
  - nearest 필터
  - clamp
  - TEXTUREGROUP_Pixels2D
- Objects / World / VFX
  - 기본 압축
  - mips 활성화
  - 적절한 texture group(World, Effects 등)
이 단계는 매우 중요합니다. 오브젝트 텍스처는 3D에서 올바르게 보여야 하고, 스프라이트 텍스처는 블러가 생기면 안 되기 때문입니다.

참고로 FModel은 가끔 GLB를 `_LOD0`, `_LOD1` 등의 접미사를 붙여 내보내기도 하지만, JSON은 대체로 `ObjMD_X`만 기대하고 `ObjMD_X_LOD0` 같은 이름을 명시적으로 참조하지 않는 경우가 많습니다.

### Stage 2 (머티리얼 / Materials)
[이펙트 및 애니메이션](Animations_Prog_kr.md)에서 설명했듯, Unreal 머티리얼은 노드 그래프입니다. cooked 덤프만으로는 다음을 얻을 수 없습니다:
- 전체 expression 그래프
- 커스텀 HLSL
- 올바른 함수 그래프(function graphs)
- 에디터 전용 정보(editor-only info)
`CachedExpressionData`가 일부 있더라도 그래프로 깔끔하게 재구성되는 경우는 드뭅니다. 따라서 다시 한 번 소수의 마스터 머티리얼을 직접 만들어야 합니다. 예:
- `M_Obj_Master_Opaque`
- `M_Obj_Master_Masked`
- (안전한 Paper2D 스프라이트용 마스터)
그런 다음 각 오브젝트에 대해 `MaterialInstanceConstant`를 만들고 파라미터를 설정합니다:
- BaseColor 텍스처(접미사 `_cl`)
- Normal 텍스처(`_no`)
- Masks(`_mk`) 등

### Stage 2b (파라미터 복구 / parameter repair)
JSON 메타데이터가 있어도 파라미터명이 예상과 다르게 들어있는 경우가 있어, 텍스처가 `DefaultTexture`로 떨어지는 일이 있습니다.
따라서 이 단계에서는 이상적으로 다음과 같은 휴리스틱을 적용합니다:
- MIC 파라미터가 `DefaultTexture`를 가리키면, 같은 폴더의 “형제 파일”을 스캔해서 `*_cl`, `*_no`를 찾기
- 해당 텍스처를 자동으로 할당하기
이렇게 하면 수천 개 에셋에 대한 수작업 정리를 (바람직하게는) 감당 가능한 수준으로 줄일 수 있습니다.

### Stage 3 (메시에 머티리얼 할당 / assign materials to meshes)
Static mesh는 머티리얼 슬롯 리스트 `StaticMaterials`를 저장합니다. 이상적으로는 에디터 서브시스템을 사용하는 것이 좋습니다. 하지만 그게 불가능한 경우(예: commandlet 환경에서 대량 에셋을 생성하는 경우)에는:
- 메시 로드
- 새 `static_materials` 리스트 구성
- `mesh.set_editor_property("static_materials", ...)`
- 메시 저장
과 같은 절차가 필요합니다.

이 과정을 거쳐야 레벨에서 메시가 실제로 렌더링됩니다.

### Stage 4 (Paper2D 스프라이트 및 플립북 / sprites and flipbooks)
사실상 다음 작업입니다:
- 베이크된 소스 크기/UV 읽기
- 베이크된 렌더 데이터 복원(트리밍/정렬에 중요)
- 플립북과 키프레임 생성
이 스프라이트들은 가끔 `/Game/Character/...` 아래 텍스처(`ChrTX_*`)를 참조합니다. 따라서 이상적으로는 먼저 그 텍스처들에 대한 uncooked 에셋을 만들어 두어야 하며, 그렇지 않으면 `DefaultTexture`(게임 내에서는 빈 텍스처)로 표시됩니다.
