# 贡献
## **LANGUAGE:**  [English](CONTRIBUTING.md) | [日本語](CONTRIBUTING.jp.md) | [中文](CONTRIBUTING.zh.md) | [한국어](CONTRIBUTING.kr.md)
欢迎任何人参与本项目。除 pak 文件外，本仓库还包含大量文档：详细说明本 MOD 各部分的工作原理、**Octopath Traveler 0** 的通用 MOD 制作资料，以及尚待完成的任务列表（附有对可行推进路径的简要讨论）。

以下是尚未从**《大陆的霸者》**移植的功能列表。每一项链接到本仓库中说明其当前进度的 markdown 文件：
- [~~任务与事件~~（已解决）](Resources/QuestEvent_Prog_zh.md)
- [特效与动画](Resources/Animations_Prog_zh.md)
- [物件（Objects）](Resources/Objects_Prog_zh.md)
- [地图／区域／关卡](Resources/Maps_Prog_zh.md)
- [音效与音乐](Resources/Sound_Prog_zh.md)
- [从《大陆的霸者》移植全新的系统／功能与代码](Resources/Custom_Prog_zh.md)

总体而言，这项工作包括：尽可能从基于 UE 4.27 的《歧路旅人：大陆的霸者》资产中提取数据，利用这些数据为 Octopath Traveler 0 制作新的 Unreal Engine 5.4 资产，并确保本 MOD 能正确加载这些资产——因为目前不存在将已烹饪（cooked）的 UE 4.27 资产通用地转换为 UE 5.4 的方法。

## 基于 CotCPort 制作的 MOD
如上所述，你可以自由地以本 MOD 为基础制作自己的 MOD，但请链接到本页面，以便将 **CotCPort** 相关的 bug 反馈集中在同一处。pak 会按文件名的字母顺序覆盖文件，因此只需相应调整你的 MOD 文件名，就能确保它在 **CotCPort** 之后加载。

## 教程与资源
以下是本 MOD 的**教程与资源**列表，方便你利用从《大陆的霸者》移植的数据制作自己的 MOD。
- [如何从本 MOD 和／或 OT0 提取资产并重新打包](Resources/Begin_zh.md)
- [如何添加或修改角色、职业以及主角的新自定义选项](Resources/customchars_zh.md)
- [如何添加或修改敌人和战斗](Resources/customenms_zh.md)
- [如何添加或修改任务与事件（WIP）](Resources/customqsts_zh.md)
- [如何添加或修改技能、异常状态与 avail（WIP）](Resources/customskills_zh.md)
