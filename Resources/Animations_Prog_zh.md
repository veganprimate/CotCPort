## **LANGUAGE:**  [English](Animations_Prog_en.md) | [日本語](Animations_Prog_jp.md) | [中文](Animations_Prog_zh.md) | [한국어](Animations_Prog_kr.md)

将《歧路旅人：大陆的霸者》（CotC）的 **UE4.27 cooked** 特效资产移植到 **UE5.4** 编辑器工程非常困难，主要有两个结构性原因：

### (1) 你没有未烹饪（uncooked）的 `.uasset` 图（graph）
对于网格/贴图，你可以用 **GLB/PNG + JSON** 来重建（而且 FModel 很幸运地支持把网格导出为 GLB）。但特效不行，因为特效资产 _本身就是那张图_：

- **Cascade** 的 `ParticleSystem` 包含一整套模块堆栈（Spawn、Lifetime、Velocity、Orbit、Drag、Color over Life、SubUV、Mesh TypeData……）。
- 在 UE5 中，正统（canonical）的系统是 **Niagara**，它并不是对 Cascade 模块的 1:1 序列化表示。
- 例如从 FModel 得到的 cooked dump 能给你 **模块属性值**，但并不会给出一份 UE5 可直接加载、可再导入的图（graph）。

因此，VFX 的重建本质上是“语义转换”，而不是“资产导入”。

### (2) 材质是 VFX 行为的一部分
大陆的霸者的特效在很大程度上依赖：
- 由 Dynamic Parameters / 曲线驱动的材质参数
- Masked 与 Translucent 的行为差异、抖动（dithering）、噪声滚动、折射/扭曲（refraction/distortion）等

你无法加载大陆的霸者 cooked 的材质 `.uasset`（unversioned cooked packages）。所以即便你把 Niagara 的生成行为完美重建了，如果没有同时提供能接收相同参数的 UE5 材质，最终观感依然会不对。

---

即便如此，我在这方面仍然取得了一些中等程度的进展，并且我认为下面这种核心模式也会驱动 VFX 的移植：

- **宽容的 JSON 加载器**（至关重要，因为 FModel 的 dump 往往包含拼接的 JSON / “Extra data”）
- **导入原始资源**（PNG → Texture2D，GLB → StaticMesh），并镜像到对应的 `/Game/...` 目录结构中
- **生成占位的主材质（master materials）** + MIC，然后用启发式规则修正参数
- 在不使用 `StaticMeshEditorSubsystem` 的情况下**为网格分配材质**
- 构建 Paper2D 精灵/翻页动画（flipbooks）（这部分已经完成）

换句话说：我们必须基于源文件格式与元数据来重建资产（绝不要尝试二进制转换）。

## 可行但目前尚未验证的策略

我们分多个阶段来处理：

### Stage 0（规格提取 / Spec extraction）
输入：Cascade JSON  
输出：字段规范化后的 `vfx_specs/*.json`：
- 发射器（emitters）列表
- 每个发射器：渲染器类型（sprite/mesh/ribbon）、生成速率/爆发（spawn rate/burst）、生命周期（lifetime）、初始位置/速度、阻力（drag）等
- 动态参数曲线（采样数组、名称、目标通道）
- 依赖项：贴图/材质名称/网格名称
目标：让规格稳定、并且不依赖 UE 内部属性命名的各种怪癖。

### Stage 1（资源导入 / Resource import）
- 按 VFX 所需设置导入贴图
- 导入网格
- （可选）如果我们能拿到 Niagara 材质的源文件，就导入；否则创建复刻用 master

### Stage 1.5（构建一套 VFX 复刻材质集）
我以前在一个更简单的特效上做过这件事，用来为 EnmAdd 下的敌人精灵创建资产：
- 创建少量 UE5 主材质，例如：
  - `M_VFX_Sprite_Additive`
  - `M_VFX_Sprite_Translucent`
  - `M_VFX_Mesh_Additive/Translucent`
  - （可选）如果大陆的霸者用到了扭曲/折射 master（我还没检查）
- 确保它们能接收大陆的霸者特效所期望的参数：
  - `NoiseTex1/2`、滚动速度、强度/功率（intensity/power）
  - `DynamicParam0-3` 的等价参数（或命名的标量参数）
  - `MaskColor`、clip、dither alpha 等
- 然后从你的 VFX 材质 JSON（如果有）生成 MIC，或者用启发式规则（贴图命名 + 默认值）生成 MIC。

这样可以避免出现“Niagara 做好了能生成，但画面完全不对”的情况，从而减少返工。

### Stage 2（模板发射器库 / Template emitters library）
在例如 `/Game/_VfxTemplates` 下维护一套固定模板，包含：
- 带标准 spawn/update + sprite renderer 绑定的 Sprite 发射器模板
- 带 mesh renderer 绑定（绑定到 user param 或默认 mesh 条目）的 Mesh 发射器模板
- （可选）如果大陆的霸者用到了 ribbon/beam，则提供对应模板
不要依赖“创建空发射器然后手动堆栈搭建”的方式。

### Stage 3（生成 Niagara 资产 / Niagara asset generation）
对每个 spec：
1) 复制发射器模板到例如 `/Game/Effect/Generated/...`
2) 为动态曲线创建 `CurveFloat` 资产
3) 打补丁/替换：
   - mesh renderer 的 static mesh（例如 `FxMD_Btl_Cylinder_A`）
   - renderer 的材质覆盖（material overrides）/ MIC 指派
   - spawn rate、lifetime、size、color（但仅限于这些在模板中作为 user parameters 暴露出来的情况）
4) 将发射器组装成一个 Niagara System

### Stage 4（烹饪与打包 / Cooking and packaging）
从时间效率上说，在烹饪（cook）之前先在 Unreal 里播放这些特效并与大陆的霸者资产对比（如果能做到可对比）可能更划算。由于特效相关的 DataBase 资产已经合并，只要你没有改动特效资产名，它们就应该会在游戏中显示出来。
