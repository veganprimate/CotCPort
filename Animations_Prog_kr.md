## **LANGUAGE:**  [English](Animations_Prog_en.md) | [日本語](Animations_Prog_jp.md) | [中文](Animations_Prog_zh.md) | [한국어](Animations_Prog_kr.md)

대륙의 패자(CotC)의 **UE4.27 cooked** 에셋에서 **UE5.4** 에디터 프로젝트로 VFX를 이식하는 일은 구조적으로 어려운 편인데, 큰 이유가 두 가지 있습니다.

### (1) uncooked `.uasset` “그래프”가 없다
메시/텍스처는 **GLB/PNG + JSON**으로 어느 정도 재구성이 가능합니다(다행히 FModel은 메시를 GLB로 내보내는 기능이 있습니다). 하지만 이펙트는 그렇지 않습니다. 이펙트 에셋은 _그래프 자체가 곧 에셋_ 이기 때문입니다.

- **Cascade**의 `ParticleSystem`은 모듈 스택(Spawn, Lifetime, Velocity, Orbit, Drag, Color over Life, SubUV, Mesh TypeData…)으로 구성됩니다.
- UE5의 표준 시스템은 **Niagara**이며, Cascade 모듈을 1:1로 직렬화한 표현이 아닙니다.
- 예를 들어 FModel의 cooked 덤프는 **모듈 프로퍼티 값**은 제공하지만, UE5에서 바로 불러와 재임포트할 수 있는 “그래프” 형태로 주지는 않습니다.

즉, VFX 재구성은 단순한 에셋 임포트가 아니라 “의미(semantic) 변환” 작업에 가깝습니다.

### (2) 머티리얼은 VFX의 “동작” 일부다
대륙의 패자 이펙트는 다음 요소에 크게 의존합니다.
- Dynamic Parameters / 커브로 머티리얼 파라미터를 구동
- Masked vs Translucent 동작, 디더링(dithering), 노이즈 스크롤링, 굴절/왜곡(refraction/distortion) 등

대륙의 패자 cooked 머티리얼 `.uasset`(unversioned cooked packages)은 로드할 수 없습니다. 따라서 Niagara의 스폰/업데이트 동작을 완벽히 재현하더라도, 동일한 파라미터를 받도록 설계된 UE5 머티리얼을 함께 제공하지 않으면 최종 “룩(look)”이 맞지 않습니다.

---

그럼에도 불구하고, 저는 이 부분에서 어느 정도(중간 정도의) 진척을 이뤘고, VFX에도 결국 같은 패턴이 적용될 것이라고 생각합니다.

- **관대한 JSON 로더**(중요: FModel 덤프는 종종 JSON이 이어 붙어 있거나 “Extra data”가 섞여 있음)
- **원본 소스 임포트**(PNG → Texture2D, GLB → StaticMesh)를 `/Game/...` 구조를 미러링하는 방식으로 가져오기
- **플레이스홀더 마스터 머티리얼** + MIC를 생성하고, 휴리스틱으로 파라미터를 보정
- `StaticMeshEditorSubsystem` 없이 **메시에 머티리얼 할당**
- Paper2D 스프라이트/플립북 생성(이미 완료)

요약하면: 소스 포맷과 메타데이터로부터 에셋을 “재구성”해야 하며(바이너리 변환은 절대 시도하지 않기), 자동화 파이프라인을 만들수록 효율이 좋아집니다.

## 가능성은 있지만 아직 검증되지 않은 전략

여러 단계로 나눠 처리합니다.

### Stage 0 (스펙 추출 / Spec extraction)
입력: Cascade JSON  
출력: 정규화된 필드를 가진 `vfx_specs/*.json`
- emitter 목록
- emitter별: renderer 타입(sprite/mesh/ribbon), spawn rate/burst, lifetime, 초기 위치/속도, drag 등
- dynamic parameter 커브(샘플 배열, 이름, 대상 채널)
- 의존성: 텍스처/머티리얼 이름/메시 이름
목표: 스펙을 안정화하고, UE 내부 프로퍼티 네이밍의 자잘한 차이/특이점에 영향받지 않게 만들기.

### Stage 1 (리소스 임포트 / Resource import)
- VFX용 설정으로 텍스처 임포트
- 메시 임포트
- (선택) Niagara 머티리얼을 소스로 확보할 수 있으면 임포트, 아니면 재현용 마스터를 생성

### Stage 1.5 (VFX 재현용 머티리얼 세트 구성)
저는 예전에 더 단순한 이펙트에 대해 이 작업을 수행해, EnmAdd 아래의 적 스프라이트용 에셋을 만든 적이 있습니다.
- UE5 마스터 머티리얼을 소수로 구성(예):
  - `M_VFX_Sprite_Additive`
  - `M_VFX_Sprite_Translucent`
  - `M_VFX_Mesh_Additive/Translucent`
  - (선택) 대륙의 패자가 왜곡/굴절을 쓴다면 distortion/refraction 마스터(아직 확인 못 함)
- 대륙의 패자 이펙트가 기대하는 파라미터를 받도록 설계:
  - `NoiseTex1/2`, 스크롤 속도, intensity/power
  - `DynamicParam0-3`에 해당하는 값(또는 이름 있는 scalar 파라미터)
  - `MaskColor`, clip, dither alpha 등
- 그 다음, VFX 머티리얼 JSON(있다면)으로부터 MIC를 생성하거나, 휴리스틱(텍스처 네이밍 + 기본값)으로 MIC를 생성

이렇게 해두면 “스폰은 되는데 화면이 전혀 다르게 보이는” 상태에서 Niagara 작업을 반복하게 되는 낭비를 줄일 수 있습니다.

### Stage 2 (템플릿 emitter 라이브러리 / Template emitters library)
예: `/Game/_VfxTemplates` 아래에 고정 템플릿 세트를 유지합니다. 포함 내용:
- 표준 spawn/update + sprite renderer 바인딩을 갖춘 Sprite emitter 템플릿
- mesh renderer를 user param 또는 기본 메시 엔트리에 바인딩하는 Mesh emitter 템플릿
- (선택) 대륙의 패자가 ribbon/beam을 쓴다면 해당 템플릿
“빈 emitter를 만든 다음 스택을 쌓아 올리는 방식”에는 의존하지 않습니다.

### Stage 3 (Niagara 에셋 생성 / Niagara asset generation)
각 spec에 대해:
1) emitter 템플릿을 복제하여 예: `/Game/Effect/Generated/...`에 배치
2) dynamic 커브용 `CurveFloat` 에셋 생성
3) 아래 항목을 패치:
   - mesh renderer의 static mesh(예: `FxMD_Btl_Cylinder_A`)
   - renderer material override / MIC 할당
   - spawn rate, lifetime, size, color(단, 템플릿에서 user parameter로 노출해 둔 경우에 한함)
4) emitter를 조합해 Niagara System을 구성

### Stage 4 (쿠킹 및 패키징 / Cooking and packaging)
쿠킹 후 게임에서 비교하는 것보다, 가능하다면 쿠킹 전에 Unreal에서 이펙트를 재생해 보고 대륙의 패자와 비교하는 편이 시간 효율이 더 좋을 수 있습니다. Effect 관련 DataBase 에셋은 이미 병합되어 있으므로, 에셋 이름을 바꾸지만 않았다면 이펙트는 게임 내에 나타나야 합니다.
