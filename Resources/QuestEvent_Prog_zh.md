## **LANGUAGE:**  [English](QuestEvent_Prog_en.md) | [日本語](QuestEvent_Prog_jp.md) | [中文](QuestEvent_Prog_zh.md) | [한국어](QuestEvent_Prog_kr.md)

### Update (2/07/2026)
多亏 MarvinCx886 在制作他的 **MioJoin** mod 时的发现，现在已经可以移植事件（Events）了。问题其实很简单：我只是忘了修改事件资源（event assets）的元数据，让它反映新的路径。你可以使用 [UAssetGUI](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest) 以及《歧路旅人0》的 Unreal Mappings 来完成该修正。`EventList` 里的 `m_Name` 和 `m_Version` 的确会解析到路径 `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]`。

此外，许多 OT0 地图中仍残留《歧路旅人：大陆的霸者》（下称《大陆的霸者》）的“遗留数据”，例如游戏中并不存在的 NPC（如 Promme）的坐标与碰撞数据（见 `QuTr_PC148_01_01`）。这些对象可以在对应的 `NpcSetList` 中通过 `m_AppearLabel` 进行引用，从而让 NPC 在该位置按已定义的碰撞数据生成（spawn）。

<img width="1113" height="824" alt="image" src="https://github.com/user-attachments/assets/37b0af5c-c866-4ad6-b1c0-4d48986eba21" />


关于 `.../Local/DataBase/Quest/` 与 `.../Local/DataBase/Event/` 下文件结构，以及如何新增事件与任务（quests）的详细教程将很快发布：  
[How to add custom quests and events](customqsts_en.md)

#### Current Issues
当前主要问题已大体转移到：在现有地图中添加新的 NPC，以及移植全新的区域。参见：[**Progress on Maps/Areas/Levels**](Maps_Prog_en.md)。

另外，为了让事件、任务、敌人和 NPC 数据在 OT0 中正确显示/运行，还需要进行大量手动调整：
 - 移植的事件中，NPC 有时会朝向错误的方向

<img width="1167" height="649" alt="image" src="https://github.com/user-attachments/assets/a748fc6a-52a2-4223-ab8c-1e09743ae8e7" />

 - 在从《大陆的霸者》移植的任务对话中遇到“是 / 否”提示时，默认选中项会变成上方的“否”（与 OT0 相反），导致选择任意选项时播放的音效不正确；取消时光标默认位置也会回到错误的位置
 - 以 Boss 强度而言，推荐等级通常偏高约 20–30 级
 - 《大陆的霸者》的 Boss 通常给予过多 EXP（例如 Priest Kratos 是一个较弱的 Boss，约 100k HP，却给了 12k EXP，是 OT0 中 Or'sazantos 的 3 倍）
 - 从《大陆的霸者》移植的任务 NPC 往往默认带有异常的 FC 数据（Field Command 相关数据），见图：
   
<img width="1239" height="667" alt="image" src="https://github.com/user-attachments/assets/c56a5b05-862f-45f2-a40a-cabe477d5fe6" />

 - 《大陆的霸者》经常使用“不可见 NPC”作为任务节点（Quest Nodes），玩家靠近时会显示路径动作/FC 数据，见图：

<img width="1272" height="714" alt="image" src="https://github.com/user-attachments/assets/b5296bd4-fd61-4d75-a98a-1e05e391b349" />

 - 场景会以触发对话的任意队伍成员为主角展开，并不一定是 Zero/持环者（Ringbearer）
 - 部分任务步骤缺少“目的说明文本”，或者改用步骤标题代替，导致在 OT0 中看起来不自然
 - 《大陆的霸者》中许多奖励类型在 OT0 中要么机制不同，要么完全无法使用。参见 [How to add custom quests and events](customqsts_en.md)

### Original Article (CORRECTED)
<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/83c68154-8a8e-48b7-822c-c486f53cd60d" />

_从这张乱糟糟的图也能看出，把《大陆的霸者》的任务（Quests）移植过来确实可行。真正的难点在于移植事件（Events）：事件承载了过场动画、Boss 战触发、战斗中的对白、地牢开关等大量内容。没有事件，任务基本就只剩任务标记与奖励。_

_查看 `/Game/Content/Local/DataBase/Event/` 下的 `EventList` 资源时，你可能会以为当游戏加载某个事件 ID 后，`m_Name` 与 `m_Version` 会解析到路径 `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` ~~但遗憾的是并非如此~~：~~测试显示游戏并不在意这些条目，似乎在内部将事件 ID 与指向事件资源的路径关联起来~~_ **（这些键确实会解析到对应路径，但对既有事件进行修改时似乎会被忽略）**。

_此外，同一个事件在游戏中的表现也会不同，这取决于你给哪个事件资源打上新的 package properties（包属性）补丁，尤其取决于它所在的文件夹。_  
_例如，VPartychat 下的事件会自动以 Wishvale 的酒馆为背景；VTest 对能在其中播放的事件限制非常多等——这些行为实际上等同于无视 `EventList` 中的 `m_Kind` 键。_  **（看起来现在仍然如此）**

_~~这表明无法仅通过向 EventList 添加新条目来把自定义事件加入游戏；我们需要在引擎已经（或即将）使用完整 Unreal 对象路径的环节拦截该流程~~。_

_我 ~~曾在开发~~ 一个用于 UE4SS 的 dll mod，使用 PolyHook2 来实现这一点，并以 [这个mod](https://github.com/UE4SS-RE/RE-UE4SS/tree/main/cppmods/KismetDebuggerMod) 作为模板。  
我还大量使用了 IDA，并配合 Dumper-7 生成的 OT0 IDA mappings（参见 BravelyPath Modular Discord 的 #resources 中提供的 SDK）。我已经能够在运行时覆写事件 ID。_ **（已停止维护，dll mod 的细节请参阅更早的 commits）**
