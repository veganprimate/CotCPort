### **LANGUAGE:**  [English](QuestEvent_Prog_en.md) | [日本語](QuestEvent_Prog_jp.md) | [中文](QuestEvent_Prog_zh.md) | [한국어](QuestEvent_Prog_kr.md)
<img width="1920" height="1080" alt="pic-window-260119-1350-16" src="https://github.com/user-attachments/assets/9370d2ef-1087-4475-b040-7f1f2ac82368" />

从这张很“凌乱”的图里也能看出来：把《歧路旅人：大陆的霸者》（下称“大陆的霸者”）的**任务（Quest）**移植过来，确实是可行的。真正的难点在于移植**事件（Event）**——事件负责过场动画、Boss 战的触发、战斗中的对话、地牢里的开关交互等等。没有事件，任务就只剩下任务标记和奖励，几乎等同于空壳。

查看 `/Game/Content/Local/DataBase/Event/` 下的 EventList 资产时，你可能会以为：游戏在加载某个事件 ID 之后，会通过 `m_Name` 和 `m_Version` 这两个键解析出路径 `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]`。但很遗憾，并非如此：测试表明，游戏并不在意这些条目，而是看起来在内部将事件 ID 与指向事件资产的路径进行了绑定。

此外，同一个事件在游戏内的表现也会因情况而异：取决于你给哪个事件资产打了新的 package 属性补丁，尤其取决于它被放在**哪个文件夹**里。
例如，VPartychat 下的事件会自动以 Wishvale 酒馆作为背景；VTest 对能在其中播放的事件类型限制非常严格等——这些行为在事实上无视了 EventList 里的 `m_Kind` 键。

这说明：仅通过向 EventList 添加新条目，无法把自定义事件添加进游戏。我们需要在引擎已经（或即将）使用完整的 Unreal 对象路径的那个环节拦截管线。

我目前正在为 UE4SS 开发一个 dll 模组，使用 PolyHook2 来实现这一点，参考模板是这个模组：
https://github.com/UE4SS-RE/RE-UE4SS/tree/main/cppmods/KismetDebuggerMod
我也在大量使用 IDA 进行分析，并使用 Dumper-7 为 OT0 生成的 IDA mappings（参见 BravelyPath Modular Discord 的 #resources 里提供的 SDK）。到目前为止，我只成功实现了在运行时覆盖 Event ID。

**已完成的工作：**
- 已确认解释器/处理器链：事件命令分发器（`sub_144634800`）会将命令 1000（`EvCmPlayEvent`，负责在事件内加载事件）路由到 `sub_144623F60`（PlayEvent 原生处理器）
- 但遗憾的是，这个 resolver 并不是路径解析器：在 `sub_144623F60` 内部，它会调用 `sub_140EFEC60` 将 EventID（int）转换为字符串；其输出是一个*固定长度的短字符串*（例如 `L"9919"`），无法容纳 `/Game/Content/Local/DataBase/Event/...` 这种路径
- 我们已经知道这个短 key 会被保存以供后续系统使用：通过激进的日志记录，我发现 `sub_144623F60` 会把 key 字符串写入事件管理器的一个 slot 结构中：
  - `SlotPtr = BasePtr + SlotIndex * 0x23A0`
  - key 字段位置：`SlotPtr + 0x250`
  因此，下游系统不会重新运行 resolver：它会直接读取 slot 里保存的 key。

由于保存的只有短 key（很可能是因为游戏存在多种加载 Event ID 的入口函数，例如来自任务、NPC、战斗、事件内部等；并且/或者是为了让事件系统支持延迟绑定（late-bound）、缓存、复制、排队，或在之后由其他子系统处理），游戏必然会在更后面的某个环节做类似这样的事：
1. 从 `SlotPtr + 0x250` 读取 key `L"9919"`
2. 基于该 key 构造完整资产路径，或进行一次查表/查找
3. 加载/解析一个对象（事件资产、包、类实例等）
我们必须找出的正是这个“后续”函数。一旦 hook 到该函数，我们就能安全地替换完整路径（或对象引用），从而真正支持自定义资产。

如下图所示，直接从《大陆的霸者》移植过来的事件基本都能正常播放，因此当前真正的核心问题是如何支持**自定义的事件资产**。
<img width="869" height="479" alt="image" src="https://github.com/user-attachments/assets/a7cbaf77-d0c2-44f2-81ec-d9a54d865f87" />



另一个日后可能变得重要的问题是：如何向 OT0 的现有地图添加新的 PathActor 数据，例如新增 NPC 和物件。这是因为来自《大陆的霸者》的自定义任务往往有自己的一套 NPC，并且其对话列表会引用新的 PathActor。实现这点可能非常直接，只是我还没实际尝试过。

关于 Local/DataBase/Quest 与 Local/DataBase/Event 下文件结构的说明，以及如何添加新的事件与任务，请参阅：[如何添加自定义任务与事件](customqsts_zh.md)。
