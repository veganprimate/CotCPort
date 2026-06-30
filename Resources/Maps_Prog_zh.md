## **LANGUAGE:**  [English](Maps_Prog_en.md) | [日本語](Maps_Prog_jp.md) | [中文](Maps_Prog_zh.md) | [한국어](Maps_Prog_kr.md)

和 [特效与动画](Animations_Prog_zh.md) 以及 [物件（Objects）](Objects_Prog_zh.md) 一样，把《歧路旅人：大陆的霸者》（CotC）的关卡/地图移植到《Octopath Traveler 0》在某种“受限的、基于重建”的意义上是可行的，但它并不是移植网格/贴图那一类问题。
它更接近于根据元数据重建一个场景图（scene graph）。只有在以下条件满足时，你才能得到较为忠实的结果：
(a) 被引用的资产已经在目标工程中重建完成；并且  
(b) 接受某些系统无法直接迁移/需要更复杂的解决方案（例如 Level Blueprint 逻辑、烘焙光照、导航等）。

一个典型的 Unreal 地图（`.umap`）大致包含如下层级结构：

- UWorld：顶层的世界资产
  - PersistentLevel（ULevel）：包含 actor 列表
    - AActor 实例：`StaticMeshActor`、`DecalActor`、`PostProcessVolume`、各种光照 actor、蓝图 actor 等
      - Components：`StaticMeshComponent`、`DecalComponent`、`BrushComponent` 等
  - StreamingLevels（可选）：子关卡，或在 UE5 中的 World Partition / DataLayers

因此，这里所说的“地图移植”意味着：
1. 目标工程中具备所有被引用的资产；以及
2. 重新创建 actor 的摆放位置 + 各组件的属性。

根据我非常有限的观察：
- CotC 地图中放置了大量 actor（如 decals、volumes 等）（→可重建）
- 基于 brush 的 volume（`PostProcessVolume` 的 brush）只能做近似重建：
  - 在 UE4 中，volume 常使用 BSP/Brush 几何（Model + `BrushComponent`）
  - 在 UE5 中虽然仍支持 brush，但自动化操作很别扭；而且从 FModel 提取的 JSON 中，Brush 的 model 数据可能并不完整
- `BodySetup.ConvexElems.VertexData` 可能有用，因为它能给出一个 volume 的凸形（convex）形状
- **可能最大的阻碍（blockers）：**
  - 你无法移植 Level Blueprint 逻辑（`LevelScriptActor` 的 BPGC），因为 `LevelScriptActor` 类是 cooked 的 `BlueprintGeneratedClass`（FModel 的“将 Blueprint 反编译为伪 C++”功能可能非常适合作为参考）
    - UE5 编辑器无法从 CotC dumps 中加载它
    - 即使你复刻了类名，逻辑本身仍然缺失
  - CotC 使用 PhysX cooked 的碰撞数据，而 OT0 使用 Chaos 物理（→我们忽略 cooked PhysX）
  - 必须先满足资产依赖：地图基本上都是引用：用于道具与建筑的 `StaticMesh`、材质（常常在组件级别被覆盖）、Decal 材质、Texture/Parameter Collections 等（→地图移植只能在 Object/Character/VFX 等资产重建之后进行）
  - CotC 不允许在地图上自由移动，因此可能本来就缺少大量碰撞数据
  - 某些地图特性依赖引擎版本（heightmap 与 layer info 可能无法作为简单的 GLB/PNG 导出；Lightmass 构建数据不可移植；UE5 使用不同的管线；HISM actors；以及前面提到的 PhysX vs. Chaos 等）
