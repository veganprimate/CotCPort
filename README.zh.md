# CotCPort
## **LANGUAGE:**  [English](README.md) | [日本語](README.jp.md) | [中文](README.zh.md) | [한국어](README.kr.md)
<table border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td width="50%">
      <img alt="1" src="https://github.com/user-attachments/assets/e27eeffe-7827-4039-8ef1-088e96d75e48" width="100%" />
    </td>
    <td width="50%">
      <img alt="2" src="https://github.com/user-attachments/assets/cdaecda7-2b2c-42a7-b980-7539948c64df" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="3" src="https://github.com/user-attachments/assets/f52aab44-c6e9-41e2-8a5d-9e8ad82a9007" width="100%" />
    </td>
    <td width="50%">
      <img alt="4" src="https://github.com/user-attachments/assets/6370561a-3653-4dcb-a0e5-2ab82f787b1e" width="100%" />
    </td>
  </tr>
  <tr>
    <td colspan="2">
      <img alt="5" src="https://github.com/user-attachments/assets/b0943896-d810-4fbd-bf78-f6a7568541e0" width="100%" />
    </td>
  </tr>
</table>


这是一个面向 Octopath Traveler 0 的 MOD，目标是尽可能多地移植《歧路旅人：大陆的霸者》（全球版 / 日版 / 中文版）中的内容。
- 可使用《大陆的霸者》中的 270+ 名旅人，并完整移植其技能盘（包含 6 名自制新增旅人：学者诺摩斯、弗拉、警备团长弗利特、雨果、游侠首领基梅尔、马提亚斯、米格尔）
- 在怪物竞技场（Monster Arena）中与《大陆的霸者》里的数百名 Boss 交战
- 通过在怪物竞技场击败新增敌人，解锁数十种来自《大陆的霸者》的新饰品

本 MOD 目前支持以下语言：
- 英语
- 日语
- 韩语
- 中文（简体）
- 中文（繁体）

![chargif](https://github.com/user-attachments/assets/00c35b82-3e9c-4810-8d8b-657f2c2a1151)

## 安装
请在 Releases 中下载任意一个 `notex` pak、`texture` pak，以及 `RecruitEvent_p.pak`。`notex` 包含 DataBase 文件；`texture` 包含从《大陆的霸者》移植的贴图与模型；`RecruitEvent_p.pak` 则是一个替换 MOD：它会将 弗恩、梅西 与 被选中之人 的首次队伍闲聊事件（“疗伤”）替换为一个招募事件，用于招募所有新增的 260+ 名旅人。在你招募完所有新增角色后，可以移除该 MOD。
随后，将这些文件放入游戏的 Paks 文件夹。具体路径取决于你的 Octopath Traveler 0 版本与安装方式。

### Steam
将这三个 pak 放入 `steamapps\common\Octopath_Traveler0\Octopath_Traveler0\Content\Paks`。

### Microsoft Store
1. 将所有 pak 的后缀 `_p` 改为 `_P`
2. 在 PC 上打开 **Xbox App**，在左侧栏的已安装游戏列表中右键 **Octopath Traveler 0**，选择 **管理**
3. 点击 **文件** 选项卡
4. 点击 **浏览**
5. 将 pak 文件放入 `<game install folder>\Octopath_Traveler0\Content\Paks\`

### Switch（实机 / Atmosphère）
将这三个 pak 放入 `<SD Card>\atmosphere\Contents\01005270232F2000\romfs\Octopath_Traveler0\Content\Paks`

### Switch（模拟器）
按如下目录结构创建一个 MOD：`romfs/Octopath_Traveler0/Content/Paks`，并将三个 pak 放入 Paks 文件夹中。

## 如何招募新增的旅人（可操作角色）
<img width="1286" height="726" alt="12" src="https://github.com/user-attachments/assets/f06205a2-5c30-4a8f-8718-5ee69d84db95" />

1. 请确认 `RecruitEvent_p.pak` 已放入你的 Paks 文件夹中。
2. 与恩波格洛的长弓大师对话（见上图）。对话后会触发一个事件，并招募本 MOD 中新增的所有可操作角色。

## （可选）困难模式版本
由于本 MOD 会修改 EnemyID 等多个资产（asset），因此会与任何同样修改这些资产的 MOD 不兼容。为方便想要将两者结合使用的玩家，我们制作了本 MOD 的困难模式版本（文件名带 `_hard` 后缀）。改动如下：
- 普通敌人的 HP 变为 2 倍，Boss 的 HP 变为 3 倍
- 普通敌人的属性（Phys. Atk.、Phys. Def.、Elem. Atk.、Elem. Def.、Speed、Accuracy、Crit.、Crit. Def. 以及最大护盾数）提高 25%，Boss 的对应属性提高 50%。击败 Boss 获得的 XP、JP 与金钱也提高 20%。
- 为避免软锁（soft-lock），对个别敌人进行了单独调整（假欧尔贝克、苍绿色的巨狼、枢机卿特拉格拉夫）
- 对个别 Boss 的属性、技能与战术进行了调整（WIP，目前主要影响 授予一切之人 系列 Boss）

## 贡献
欢迎任何人参与本项目。除 pak 文件外，本仓库还包含大量文档：介绍本 MOD 各部分如何运作、Octopath Traveler 0 的通用 MOD 制作资料、以及尚待完成的任务列表（并附有对可行推进路径的简要讨论）。
以下是尚未从《大陆的霸者》移植的功能列表。每一项都会链接到本仓库内的 markdown 文件，说明其当前进度：
- [任务与事件](QuestEvent_Prog_zh.md)
- [特效与动画](Animations_Prog_zh.md)
- [物件（Objects）](Objects_Prog_zh.md)
- [地图 / 区域 / 关卡](Maps_Prog_zh.md)
- [音效与音乐](Sound_Prog_zh.md)
- [从《大陆的霸者》移植全新的系统/功能](Custom_Prog_zh.md)

总体而言，这项工作包括：尽可能从基于 UE 4.27 的《歧路旅人：大陆的霸者》资产中提取数据，用这些数据为 Octopath Traveler 0 制作新的 Unreal Engine 5.4 资产，并确保本 MOD 能正确加载这些资产——因为目前并不存在将已烹饪（cooked）的 UE 4.27 资产通用地转换到 UE 5.4 的方法。

### 基于 CotCPort 制作的 MOD
如上所述，你可以自由地以本 MOD 为基础制作自己的 MOD，但请链接到本页面，以便将《大陆的霸者》移植相关的 bug 反馈集中在同一处。pak 会按文件名的字母顺序覆盖文件，因此只要调整你自己的 MOD 文件名，就能确保它在 CotCPort 之后加载。

### 教程与资源
下面列出了一些与本 MOD 相关的教程与资源，方便你基于《大陆的霸者》移植数据制作自己的 MOD。
- [如何从本 MOD 和/或 OT0 提取资产](Begin_zh.md)
- [如何添加自定义角色](customchars_zh.md)
- [如何添加自定义敌人和战斗](customenms_zh.md)
- [如何添加自定义任务与事件](customqsts_zh.md)
- [如何添加自定义技能与 avail](customskls_zh.md)

## 联系方式
你可以在 BravelyPath Modular Discord 的 #cotc-characters-mod 频道联系到我（也有多位贡献者与 MOD 作者在该频道活动）：
https://discord.com/invite/t43Kbrp 

## 致谢
感谢以下人员的帮助与贡献：
- BoltGSR：提供了《歧路旅人：大陆的霸者》日版的数据。
- Oersted：提供了《歧路旅人：大陆的霸者》中文版的数据，以及完整的技能表、精灵图（spritesheets），并分享了自制角色的想法。
- Vat：提供了角色与敌人的扩展精灵图，并将大量贴图进行了烹饪处理（cooking），使其在游戏内显示得更加清晰。
- Solo Traveler：协助测试了大量功能，提出了困难模式版本的点子，并提供了一场自定义竞技场战斗的详细设计说明。
- igoticecream：为 Octopath Traveler 0 生成了 SDK，并在实现自定义事件方面为我指明了方向。
- MarvinCx886 与 JohnZ：研究并整理出从《大陆的霸者》移植声音数据的方法，并撰写了非常详尽的说明文档。
