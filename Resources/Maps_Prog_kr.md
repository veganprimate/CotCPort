## **LANGUAGE:**  [English](Maps_Prog_en.md) | [日本語](Maps_Prog_jp.md) | [中文](Maps_Prog_zh.md) | [한국어](Maps_Prog_kr.md)

[이펙트 및 애니메이션](Animations_Prog_kr.md) 및 [오브젝트](Objects_Prog_kr.md)와 마찬가지로, 대륙의 패자(CotC)의 레벨(맵)을 『옥토패스 트래블러 0』로 이식하는 것은 “제한적이지만 재구성 기반”이라는 의미에서는 가능합니다. 다만 이것은 메시/텍스처를 옮기는 문제와는 성격이 다릅니다.
이 작업은 메타데이터로부터 씬 그래프(scene graph)를 다시 조립하는 것에 더 가깝고, 결과를 충실하게 만들려면
(a) 참조되는 에셋들이 목표 프로젝트에서 먼저 재구성되어 있어야 하며,
(b) 일부 시스템은 그대로 옮겨지지 않거나 더 복잡한 해결이 필요함(Level Blueprint 로직, 베이크 조명, 내비게이션 등)을 받아들여야 합니다.

일반적인 Unreal 맵(`.umap`)은 대략 아래와 같은 계층 구조를 가집니다:

- UWorld: 최상위 월드 에셋
  - PersistentLevel(ULevel): 액터 리스트를 포함
    - AActor 인스턴스: `StaticMeshActor`, `DecalActor`, `PostProcessVolume`, Light 액터들, Blueprint 액터 등
      - Components: `StaticMeshComponent`, `DecalComponent`, `BrushComponent` 등
  - StreamingLevels(선택): 서브레벨, 또는 UE5의 World Partition/DataLayers

따라서 여기서 말하는 “맵 포팅”은 곧:
1. 타겟 프로젝트에 참조되는 모든 에셋이 준비되어 있고,
2. 액터 배치 + 컴포넌트 프로퍼티를 재현하는 것
을 의미합니다.

아주 제한적인 관찰 결과로는:
- 대륙의 패자 맵에는 데칼이나 볼륨 같은 “배치된 액터”가 많이 포함되어 있음(→재구성 가능)
- 브러시 기반 볼륨(`PostProcessVolume` 브러시)은 대략적으로만 재구성 가능:
  - UE4에서는 볼륨이 종종 BSP/Brush 지오메트리(Model + `BrushComponent`)를 사용
  - UE5에서도 브러시 지원은 존재하지만 자동화가 까다롭고, FModel로 추출한 JSON에서 Brush 모델 데이터가 불완전할 수 있음
- `BodySetup.ConvexElems.VertexData`는 볼륨의 볼록(convex) 형태를 제공하므로 유용할 수 있음
- **아마 가장 큰 블로커(장애 요소):**
  - Level Blueprint 로직(`LevelScriptActor` BPGC)은 포팅할 수 없음. `LevelScriptActor` 클래스가 cooked 된 `BlueprintGeneratedClass`이기 때문임(FModel의 “Blueprint를 pseudo C++로 디컴파일” 기능은 참고 자료로 매우 유용할 수 있음)
    - UE5 에디터는 대륙의 패자 덤프에서 이를 로드하지 못함
    - 클래스 이름을 재현해도 로직이 빠져 있음
  - 대륙의 패자는 PhysX cooked 충돌 데이터를 사용하고, OT0는 Chaos 물리를 사용함(→cooked PhysX는 무시)
  - 에셋 의존성이 먼저 갖춰져야 함: 맵은 대부분 레퍼런스의 집합으로, 소품/건물용 `StaticMesh`, 머티리얼(대개 컴포넌트 단위로 override), 데칼 머티리얼, Texture/Parameter Collections 등이 필요함(→맵 포팅은 Object/Character/VFX 등 재구성 이후에 가능)
  - 대륙의 패자는 맵에서 자유 이동을 허용하지 않으므로, 애초에 충돌 데이터가 많이 없을 수도 있음
  - 일부 맵 기능은 엔진 버전에 의존함(heightmap과 layer info는 단순 GLB/PNG로 내보내기 어려울 수 있음. Lightmass 빌드 데이터는 포팅 불가. UE5는 다른 파이프라인을 사용. HISM 액터. 앞서 언급한 PhysX vs. Chaos 등)
