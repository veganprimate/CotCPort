## **LANGUAGE:**  [English](Objects_Prog_en.md) | [日本語](Objects_Prog_jp.md) | [中文](Objects_Prog_zh.md) | [한국어](Objects_Prog_kr.md)

`/Object` 文件夹主要包含世界/玩法用的道具（props）以及它们的展示相关资产，通常包括：
- **静态网格（Static Meshes）**（道具、环境部件、可交互物）
- 这些网格使用的 **贴图（Textures）**（albedo/color、normal、masks 等）
- 用于应用这些贴图的 **材质（Materials）/材质实例（Material Instances）**
- （有时）存放在对象资源文件夹中的 Paper2D **sprites/flipbooks**（例如《大陆的霸者》里的 `Obj_Dice`）

与 [特效与动画](Animations_Prog_zh.md) 一样，你无法把《大陆的霸者》的资产文件直接导入 UE5，我们只能使用 JSON、GLB、PNG 来重建这些资产。
我们也同样构建一个多阶段流程（虽然也尚未验证，但看起来比 `Effect` 的那套流程更有希望）：

### Stage 0（构建清单 / manifest）
cooked dump 往往非常混乱；清单能提供一张确定性的映射表：
- Object → 网格 JSON 路径
- Object → 找到的贴图
- Object → 材质配置
- Object → 精灵帧 JSON 路径
我们还需要再次实现一个“宽容的”JSON 加载器，因为 FModel 导出的 JSON 经常包含拼接在一起的 JSON 对象或因“extra data”导致的解析失败。

### Stage 1（导入原始资源：PNG → `Texture2D`，GLB → StaticMesh）
将 PNG 作为 `Texture2D` 导入 UE5，将 GLB 作为 `StaticMesh` 导入。同时根据用途应用不同的导入设置：
- Sprites / Paper2D
  - UI2D 压缩
  - 不生成 mipmaps
  - nearest filter（最近点采样）
  - clamp（钳制）
  - TEXTUREGROUP_Pixels2D
- Objects / World / VFX
  - 默认压缩
  - 启用 mipmaps
  - 选择合适的 texture group（World、Effects 等）
这一步至关重要：物件贴图需要在 3D 中看起来正确，而精灵贴图则不应该被模糊化。

注意：FModel 有时会导出带 `_LOD0`、`_LOD1` 等后缀的 GLB 文件，但 JSON 往往只期望 `ObjMD_X`，并不会显式引用 `ObjMD_X_LOD0` 等名称。

### Stage 2（材质 / Materials）
如 [特效与动画](Animations_Prog_zh.md) 中所述，Unreal 的材质是节点图（node graph）。cooked dump 不会给你：
- 完整的表达式图（expression graph）
- 自定义 HLSL
- 正确的函数图（function graphs）
- 仅编辑器使用的信息（editor-only info）
即使你拿到了一些 `CachedExpressionData`，也很少能干净地重建成一张图。所以你仍然需要自己做一小套主材质，例如：
- `M_Obj_Master_Opaque`
- `M_Obj_Master_Masked`
- （一个安全的 Paper2D sprite 主材质）
然后针对每个 object，创建一个 `MaterialInstanceConstant` 并设置参数：
- BaseColor 贴图（后缀 `_cl`）
- Normal 贴图（`_no`）
- Masks（`_mk`）等

### Stage 2b（参数修复 / parameter repair）
我发现即便有 JSON 元数据，参数有时也不会匹配你预期的名称，导致贴图最终变成 `DefaultTexture`。
因此，这个阶段理想情况下应当应用启发式规则：
- 如果 MIC 的参数指向 `DefaultTexture`，就扫描同目录下的兄弟文件，查找 `*_cl`、`*_no`
- 自动把这些贴图分配上去
这样可以把成千上万资产的手工清理工作量降到（希望是）可控的范围。

### Stage 3（把材质分配到网格 / assign materials to meshes）
静态网格保存了一份材质槽（material slots）列表：`StaticMaterials`，理想情况下应使用编辑器子系统来操作它。如果做不到（例如在 commandlet 中批量创建资产时），则需要：
- 加载网格
- 构建新的 `static_materials` 列表
- `mesh.set_editor_property("static_materials", ...)`
- 保存网格

这样网格才能在关卡中正确渲染。

### Stage 4（Paper2D sprites 与 flipbooks）
基本就是：
- 读取已烘焙（baked）的源尺寸/UV
- 恢复已烘焙的渲染数据（对裁剪/对齐很重要）
- 生成 flipbook 与关键帧（keyframes）
这些 sprites 有时会引用 `/Game/Character/...` 下的贴图（`ChrTX_*`），因此理想情况下你应该先为这些贴图创建 uncooked 资产；否则它们会显示为 `DefaultTexture`（在游戏里就是空白）。
