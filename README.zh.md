# 霸者移植

<p align="center">
  <strong>歧路旅人0 大陆的霸者移植MOD</strong><br>
  <sub>原项目名：<code>CotCPort</code></sub>
</p>

## **语言：** [English](README.md) | [日本語](README.jp.md) | **中文** | [한국어](README.kr.md)

<br>

<p align="center">
<a href="#installation"><img alt="安装" src="https://img.shields.io/badge/%E5%AE%89%E8%A3%85-10363D?style=for-the-badge&logo=github&logoColor=white"></a>
<a href="Resources/HardMode_zh.md"><img alt="困难模式" src="https://img.shields.io/badge/%E5%9B%B0%E9%9A%BE%E6%A8%A1%E5%BC%8F-880808?style=for-the-badge&logo=undertale&logoColor=white"></a>
<a href="#how-to-recruit-the-new-travelers"><img alt="招募旅人" src="https://img.shields.io/badge/%E6%8B%9B%E5%8B%9F%E6%97%85%E4%BA%BA-10363D?style=for-the-badge&logo=bookstack&logoColor=white"></a>
</p>
<p align="center">
<a href="CONTRIBUTING.md"><img alt="贡献" src="https://img.shields.io/badge/%E8%B4%A1%E7%8C%AE-337A00?style=for-the-badge&logo=git&logoColor=white"></a>
<a href="https://discord.gg/7YmAFJ3h5Z"><img alt="联系方式" src="https://img.shields.io/badge/%E8%81%94%E7%B3%BB%E6%96%B9%E5%BC%8F-30363D?style=for-the-badge&logo=discord&logoColor=white"></a>
<a href="#acknowledgements"><img alt="致谢" src="https://img.shields.io/badge/%E8%87%B4%E8%B0%A2-484400?style=for-the-badge&logo=githubsponsors&logoColor=white"></a>
</p>

<p align="center"> <a href="#steam"><img alt="Steam" src="https://img.shields.io/badge/Steam-092226?style=flat-square&logo=steam&logoColor=white"></a> <a href="#microsoft-store"><img alt="Microsoft Store" src="https://img.shields.io/badge/Microsoft%20Store-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTUgNVY0YTMgMyAwIDAgMSA2IDB2MWgybDEgMTBIMkwzIDVoMlptMS4yIDBoMy42VjRhMS44IDEuOCAwIDEgMC0zLjYgMHYxWicvPjxwYXRoIGZpbGw9JyMwOTIyMjYnIGQ9J001IDhoMnYySDVWOFptNCAwaDJ2Mkg5VjhabS00IDNoMnYySDV2LTJAbTQgMGgydjJIOXYtMlonLz48L3N2Zz4%3D&logoColor=white"></a> <a href="#switch-real-hardwareatmosphère"><img alt="Switch 实机" src="https://img.shields.io/badge/Switch%20%E5%AE%9E%E6%9C%BA-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTYuNSAxSDNhMiAyIDAgMCAwLTIgMnYxMGEyIDIgMCAwIDAgMiAyaDMuNVYxWm0zIDBIMTNhMiAyIDAgMCAxIDIgMnYxMGEyIDIgMCAwIDEtMiAySDkuNVYxWk00LjQgNS4yYTEuMiAxLjIgMCAxIDAgMCAyLjQgMS4yIDEuMiAwIDAgMCAwLTIuNFptNy4yIDMuMmExLjIgMS4yIDAgMSAwIDAgMi40IDEuMiAxLjIgMCAwIDAgMC0yLjRaJy8%2BPHBhdGggZmlsbD0nIzA5MjIyNicgZD0nTTYuNSAyLjN2MTEuNEgzQS43LjcgMCAwIDEgMi4zIDEzVjNBLjcuNyAwIDAgMSAzIDIuM2gzLjVabTMgMEgxM2EuNy43IDAgMCAxIC43Ljd2MTBhLjcuNyAwIDAgMS0uNy43SDkuNVYyLjNaJy8%2BPC9zdmc%2B&logoColor=white"></a> <a href="#switch-emulator"><img alt="Switch 模拟器" src="https://img.shields.io/badge/Switch%20%E6%A8%A1%E6%8B%9F%E5%99%A8-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTUgNmg2YTQgNCAwIDAgMSAzLjcgMi41bC44IDIuMWEyLjMgMi4zIDAgMCAxLTMuNyAyLjVMMTAuNCAxMkg1LjZsLTEuNCAxLjFBMi4zIDIuMyAwIDAgMSAuNSAxMC42bC44LTIuMUE0IDQgMCAwIDEgNSA2Wm0tLjcgMnYxLjJIMy4xdjEuMmgxLjJ2MS4yaDEuMnYtMS4yaDEuMlY5LjJINS41VjhINC4zWm02LjcuN2EuOS45IDAgMSAwIDAgMS44LjkuOSAwIDAgMCAwLTEuOFptMiAxLjdhLjkuOSAwIDEgMCAwIDEuOC45LjkgMCAwIDAgMC0xLjhaJy8%2BPC9zdmc%2B&logoColor=white"></a> </p>


<p align="center">
<a href="CONTRIBUTING.md#mods-using-the-cotcport-as-a-basis"><img alt="霸者移植衍生 MOD" src="https://img.shields.io/badge/%E9%9C%B8%E8%80%85%E7%A7%BB%E6%A4%8D%E8%A1%8D%E7%94%9F%20MOD-1A4000?style=flat-square&logo=curseforge&logoColor=white"></a>
<a href="CONTRIBUTING.md#tutorials-and-resources"><img alt="教程与资源" src="https://img.shields.io/badge/%E6%95%99%E7%A8%8B%E4%B8%8E%E8%B5%84%E6%BA%90-1A4000?style=flat-square&logo=readthedocs&logoColor=white"></a>
<a href="CONTRIBUTING.md#variety-of-resources-unordered"><img alt="资源列表" src="https://img.shields.io/badge/%E8%B5%84%E6%BA%90%E5%88%97%E8%A1%A8-1A4000?style=flat-square&logo=files&logoColor=white"></a>
</p>

<br>
<table border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td width="50%">
      <img alt="1" src="https://github.com/user-attachments/assets/5aaa0224-4c69-47bf-9fe3-4d9f1f9ee429" width="100%" />
    </td>
    <td width="50%">
      <img alt="2" src="https://github.com/user-attachments/assets/35bbf069-f2ce-4ed9-b6d5-be0ec8975fa6" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="3" src="https://github.com/user-attachments/assets/1c96f251-98f2-49d4-870d-3e0ae2af4f97" width="100%" />
    </td>
    <td width="50%">
      <img alt="4" src="https://github.com/user-attachments/assets/9a385a1d-4448-496e-b977-db4bd23676ac" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="5" src="https://github.com/user-attachments/assets/20e1337d-4bbc-4fb4-b3ef-6c597d6652f4" width="100%" />
    </td>
    <td width="50%">
      <img alt="6" src="https://github.com/user-attachments/assets/c3c4286a-2f19-4574-b469-5ff4e750f81e" width="100%" />
    </td>
  </tr>
  <tr>
    <td colspan="2">
      <img alt="7" src="https://github.com/user-attachments/assets/2cd24e80-9b29-4376-a4d5-3d0a54be1529" width="100%" />
    </td>
  </tr>
</table>

这是面向 **《歧路旅人0》** 的 MOD，目标是尽可能将 **《歧路旅人：大陆的霸者》**（日服／国际服／国服）以及系列其他作品中的内容移植并整合进《歧路旅人0》。
- 将**《大陆的霸者》中登场的 280+ 名角色**做成可操作角色，并完整包含**技能盘**、**语音台词**、**角色立绘**以及**许愿谷专属整合**；此外还包括 **15 名完全自制的可操作角色**（_学者诺摩斯、弗拉、皮耶罗、弗利特、雨果、基梅尔船长、马提亚斯、米格尔、艾尔哈特、克里斯·克罗斯福德、泽利格、加尔戴拉的巫女莉安娜、荣光的卢西安、霜行者普罗梅，以及凡妮莎·海瑟尔_）。
- 可在**怪物竞技场**中与**《大陆的霸者》来源的数百名新增 Boss** 交战，其中包括两场**完全自制**的高难挑战：一场对阵**巴尔杰罗家族**，另一场对阵**Bravely 三人组**。
- 通过击败怪物竞技场中的新增敌人，解锁数十种**《大陆的霸者》来源的新饰品**和**装备**；敌人越强，获得的奖励越好。
- 游玩数十个**《大陆的霸者》来源的新增任务**，其中包括角色的**旅人故事**。
- 在回忆录地点查看以**队伍闲聊**形式整合进去的**数百段**回忆事件。
- 体验从**《大陆的霸者》移植、并通过 C++ MOD 实现的全新异常状态／状态效果**。

目前**开发中／未完成／计划实现**的功能包括但不限于：

- **即使在奥鲁斯特拉地图上也能运作**的**无缝实时**昼夜切换。夜间奥鲁斯特拉地图会准备符合《歧路旅人》世界观的**专属 NPC 配置预设**。
- 基于**《大陆的霸者》《歧路旅人》《歧路旅人II》**对应地图重建的 **500+** 张新地图，其中包括**索里苏提亚**和**IF 奥鲁斯特拉**的**新世界地图**。
- 将《歧路旅人》和《歧路旅人II》中的**支线任务**重构为**适配《歧路旅人0》的版本**。
- 一个_可选安装_的**困难模式版本**，会显著提高**本体游戏**以及**《大陆的霸者》／《歧路旅人》／《歧路旅人II》来源新增内容**的难度。更多信息请见[困难模式版本](https://github.com/veganprimate/CotCPort_Canary/blob/main/Resources/HardMode_zh.md)。
- 以《歧路旅人》初代为主要灵感、围绕克里斯·克罗斯福德展开的**自制任务线**，其中也包含**自制 Boss 战**。
- 用于实现**《大陆的霸者》大型子系统**的**自定义 C++ MOD**，例如：
  - **神兽**相关战斗选项
  - **潜力**
  - **宠物**
  - 维克塔霍洛斗技场**大会**用自定义 **UI** 与**玩法**逻辑
  - 武器与饰品的**等级（Grade）**、**IF 奥鲁斯特拉回廊**类 Rogue 要素
  - **试炼之塔**
  - **无名小镇**等**新增建筑类型**
  - 新增内容与**许愿谷**的更深度整合
  - ……等等

本 MOD 支持以下语言：
- **英语**
- **日语**
- **韩语**
- **简体中文**
- **繁体中文**

![chargif](https://github.com/user-attachments/assets/00c35b82-3e9c-4810-8d8b-657f2c2a1151)

[worldmap_showcase.mp4](https://github.com/user-attachments/assets/e669f527-8412-4f91-8799-483e572caa32)

<a id="installation"></a>
## 安装
前往 [Releases](https://github.com/veganprimate/CotCPort/releases)，下载最新（预）发布版本中的 `CotCPort_Base.7z` 和 `CotCPort_Extras.7z` 两个压缩包。

 - `CotCPort_Base.7z` 包含**必须安装的基础 MOD 文件**。其中 DLL MOD 彼此依赖。
 - `CotCPort_Extras.7z` 是 DLL MOD 和 pak MOD 的**可选集合**。**其中没有任何内容是必需的**，并且**彼此之间不相互依赖**。

压缩包的结构与游戏内部文件夹相对应：
```
OCTOPATH TRAVELER 0/
└── Octopath_Traveler0/
    ├── Binaries/
    │   └── Win64/
    └── Content/
        └── Paks/
```
目标是让压缩包中的文件可以直接**合并进**现有的 `Octopath_Traveler0` 文件夹。请不要误建如下额外嵌套文件夹：
```
Octopath_Traveler0/Octopath_Traveler0/...
```
或者：
```
Octopath_Traveler0/CotCPort_Base/Octopath_Traveler0/...
```
关于两个压缩包内部文件的更多说明，请查看 [v1.1.0-pre](https://github.com/veganprimate/CotCPort/releases/tag/v1.1.0-pre) 发布说明中的 “压缩包结构” 部分。

安装时可能需要你**替换** `Binaries/Win64/` 下的一些 DLL。启动游戏前，也请确保 霸者移植（CotCPort）v1.0.0 的 `CotCPort_notex_p.pak` 已从 `Content/Paks/` 中**删除**；`CotCPort_DataBase_p.pak` 本应替代它，但由于文件名不同，不会自动覆盖。

**警告：** 目前还没有霸者移植（CotCPort）所开发 **C++ MOD** 的 **Switch 兼容版本**，也没有用于安装 hook/detour 等功能的等价**原生运行时层**。Switch 版《歧路旅人0》基于 v1.0.4.0，而不是这些 MOD 所基于的 v1.0.8.0。

C++ MOD 加载器是否与 《歧路旅人0》v1.0.8.0 的 Microsoft Store 版本**兼容**仍然**未经测试**。MS Store Win64 Shipping 可执行文件可能与 Steam 版不同，从而导致 RVA 偏移。

Nintendo Switch 和 MS Store 玩家可能需要暂时**限制自己只使用预发布中的 pak**。更多信息见下文。

<a id="steam"></a>
### Steam
将压缩包解压到 `steamapps\common\OCTOPATH TRAVELER 0\Octopath_Traveler0\` 下。

<a id="microsoft-store"></a>
### Microsoft Store
1. 将所有 pak 的后缀从小写 `_p` 改为大写 `_P`。
2. 在 PC 上打开 **Xbox App**，在左侧栏的已安装游戏列表中右键 **OCTOPATH TRAVELER 0**，选择**管理**。
3. 点击**文件**选项卡。
4. 点击**浏览**。
5. 将压缩包解压到 `<game install folder>\Octopath_Traveler0\` 下。

**警告：** C++ Modding API 与 Microsoft Store 版《歧路旅人0》 的兼容性仍未**证实**。如果在你的系统上结果为**不兼容**（请检查 `Binaries/Win64/KingshipLogs/` 下是否生成日志），请从 `Content/Paks/` 文件夹中**移除 `CotCPort_BluePrints_P.pak`**，因为该 pak 依赖 C++ MOD `CotCPort_Menus` 和 `CotCPort_BattleUI`。

<a id="switch-real-hardwareatmosphère"></a>
### Switch（实机 / Atmosphère）
Switch 版本应视为**仅 pak**。**Windows C++ MOD 不会在 Switch 上工作**，并且**目前还没有替代方案**。
Switch 安装使用 **Atmosphère LayeredFS**。你不是直接替换游戏安装目录里的文件，而是把经过修改的 RomFS 风格文件放到 SD 卡上，由 Atmosphère 在游戏启动时进行 overlay。
《歧路旅人0》的 title ID 是 `01005270232F2000`，请在 SD 卡上创建以下文件夹：
```
sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/
```
将**除了 `CotCPort_BluePrints_p.pak` 之外**的所有 pak 放入其中：
```
sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_BluePrints_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Maps_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Sounds_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Textures_p.pak
```
`CotCPort_Extras.7z` 中的所有 pak 也兼容。

<a id="switch-emulator"></a>
### Switch（模拟器）
与实机类似，出于同样原因，此平台也应视为**仅 pak**。
请创建一个具有以下**内部**结构的 MOD：
```
romfs/
└── Octopath_Traveler0/
    └── Content/
        └── Paks/
             ├── CotCPort_DataBase_p.pak
             ├── CotCPort_Maps_p.pak
             ├── CotCPort_Sounds_p.pak
             ├── CotCPort_Textures_p.pak
             └── (+ CotCPort_Extras.7z 中的任意 pak MOD)
```

### 其他平台
除上述平台外，《OCTOPATH TRAVELER 0》也登陆 PS4、PS5、Xbox Series X|S 和 Switch 2。上述平台**均没有**可行且一致的方法来安装哪怕只是 pak MOD。

<details>
<summary><strong>查看霸者移植兼容性表</strong></summary>

| 平台 | 霸者移植支持状态 | 具体 Homebrew / Modding 栈名称 | 值得说明的具体路径 / 标识符 | 为什么这对 《歧路旅人0》的 Unreal `.pak` MOD 很重要 | 结论 |
| --- | --- | --- | --- | --- | --- |
| **Steam PC** | **完整支持目标** | Windows DLL 加载、`dwmapi.dll` 代理/加载行为、`OT0KingshipLoader.dll`、`KingshipMods`、`KingshipConfig` | `Octopath_Traveler0/Binaries/Win64/`; `Octopath_Traveler0/Content/Paks/`; Steam Win64 Shipping 可执行文件 | 这是唯一一个霸者移植（CotCPort）两个部分都成立的平台：Unreal pak 文件 **以及** Windows C++ 运行时 hook/detour。 | **主要支持平台** |
| **Microsoft Store / PC 上的 Xbox App** | **很可能仅支持 pak；DLL 层仍属实验性** | 同样是 Windows 文件系统，但 MSIX/Xbox App 安装结构可能不同；可执行文件可能不匹配 Steam RVA | `Octopath_Traveler0/Content/Paks/`; pak 后缀可能需要 `_P.pak` 而不是 `_p.pak` | 如果游戏接受 loose/额外 pak，pak 文件可能会加载，但**如果 Win64 可执行文件与 Steam 版不同，C++ hook 可能失效**。 | 作为 **仅 pak / 实验性** 支持 |
| **Switch 1 实机 / Atmosphère** | **仅 pak 支持较可行且可文档化**。Switch `.nso` 模块不太可能作为 DLL 替代方案 | Atmosphère LayeredFS；RomFS overlay；按 title 区分的 `contents` 文件夹 | `sd:/atmosphere/contents/<title_id>/romfs/`; 《歧路旅人0》为 `sd:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/` | Atmosphère LayeredFS 可以把文件 overlay 到游戏 RomFS 上。对仅 pak MOD 来说足够接近，但 Windows DLL MOD 不可能工作。 | **仅 pak** |
| **Switch 1 模拟器** | **仅 pak 支持较可行**，同上 | 以 `romfs/` 为根的模拟器 MOD 文件夹；Yuzu/Ryujinx/Ryubing/Eden 风格 RomFS 替换 | `<mod folder>/romfs/Octopath_Traveler0/Content/Paks/*.pak` | 概念上与 Atmosphère LayeredFS 相同，只是通过模拟器 MOD 目录处理。DLL 仍不适用，因为模拟器运行的是 Switch 版而不是 Windows 版。 | **仅 pak** |
| **Switch 2** | **不支持** | 没有公开的 Atmosphère 等效 CFW 栈；没有公开的 LayeredFS 等效 MOD 流程 | 不要复用 `sd:/atmosphere/contents/...`；尚无面向用户的既定 Switch 2 `romfs` overlay 路径 | 公开报道只描述过一个小型 Switch 2 userland ROP 演示，没有影响 kernel/root，也没有实际 jailbreak/homebrew 用途。 | **不支持** |
| **Xbox Series X\|S 零售主机** | **不支持** | Xbox 零售安全模型；零售游戏与 UWP Dev Mode 应用分离 | 无用户可访问的 `Content/Paks/`；无 LayeredFS 风格 `romfs` overlay；无 `sdmc` 等价物 | Xbox 零售主机不会为任意零售游戏内容暴露常规 MOD 文件夹。Xbox Dev Mode 是用于部署 UWP 应用的独立模式，不是用于修改已安装零售游戏。 | **不支持** |
| **Xbox Series X/S Dev Mode** | **对霸者移植无实际用途** | UWP 应用部署；Dev Home；Device Portal；Homebrew 风格 UWP 应用/模拟器 | Dev Mode 应用包，而不是零售游戏安装目录 | 你可以部署自己的 UWP 应用，但这并不提供把 `.pak` 文件注入 Xbox 零售版 《歧路旅人0》 安装目录的支持路径。 | **不支持：Dev Mode ≠ 零售游戏 Modding** |
| **PS4 零售 / stock firmware** | **不支持** | Stock Orbis OS；签名包；无用户 pak 文件夹 | 无向用户暴露的 `Octopath_Traveler0/Content/Paks/` 等价路径 | Stock PS4 不提供常规用户可写位置来把任意 Unreal pak 注入已安装的零售游戏。 | **不支持** |
| **PS4 Homebrew / GoldHEN 环境** | **仅研究用途，暂不适合作为 README 支持对象** | GoldHEN；PPPwn 时代支持；Itemzflow；PS4 Cheats Manager；GoldHEN XML patch engine | GoldHEN patches: `/user/data/GoldHEN/patches/xml/<TitleID>.xml`; Itemzflow app logs: `/user/app/ITEM00001/logs/itemzflow_app.log`; daemon logs: `/data/itemzflow_daemon/daemon.log`; Itemzflow title ID: `ITEM00001` | 该生态支持 dump、fake package、patch、trainer、XML memory patch 等，但**这并不等同于干净的 loose-pak overlay**。 | **不支持 / 仅研究用途**，除非有人构建并测试 PS4 package/patch 流程 |
| **PS4 包重建式流程** | **对专门移植理论上可行，但不是常规安装** | 已 dump/解密 package、GP4、fake package rebuild、app/update package 处理 | PS4 工具语境下运行时游戏挂载常被称为 `app0:/`，但面向用户的 patch 工具使用 `/user/data/GoldHEN/patches/xml/` 等路径 | 对 pak MOD 来说，可能的研究路线是替换或添加 dumped game/update package 内的 pak 文件并重新构建/安装。这远超普通“把 pak 复制到这里”的指南。 | 需要有 PS4 打包经验的维护者 |
| **PS5 零售 / stock firmware** | **不支持** | Stock PS5 OS；签名包；无用户 pak 文件夹 | 无 `Content/Paks/` 等价路径；无 LayeredFS 风格公开 MOD 路径 | 与 PS4 存在同类大问题，且 homebrew 场景更不成熟、更碎片化。 | **不支持** |
| **PS5 Homebrew / etaHEN 环境** | **仅研究用途** | etaHEN；Kstuff；DPIv2；Itemzflow PS5 builds；XML patch/plugin ecosystem | etaHEN release notes 提到 firmware `8.40–10.01` 的 cheats 支持、Kstuff menu、custom background package installer 和 DPIv2 相关功能。Itemzflow 1.09 PS5 notes 提到 `5.xx` 和 `6.xx` 支持、etaHEN toolbox settings、XML patches plugin 以及实验性 self-decrypter。 | PS5 homebrew 工具存在，但并不意味着已有 《歧路旅人0》 loose `.pak` 安装路径。现有支持更接近包管理、trainer、cheat、patch 和 dump，而不是 Atmosphère 风格 RomFS overlay。 | **不支持 / 仅研究用途** |
| **PS4/PS5 XML patch ecosystem** | **对霸者移植 pak 文件没有直接用途** | Illusion/GoldHEN patch XML；Itemzflow XML patches plugin；PS4 Cheats Manager | 示例格式：`<TitleID><ID>CUSA01765</ID></TitleID>` 加 `<Metadata ... AppVer="01.00" AppElf="eboot.bin">`; patch 存为 `CUSA00001.xml` 等 title-specific XML | 它可以 patch 内存/代码值、解锁 debug menu、改字节等，但不能安装 Unreal asset/data pak 文件。只有当 CotCPort 以后需要很小的可执行文件 patch 时才可能有用。 | **不适用于 pak 安装** |
| **PS4/PS5 Itemzflow** | **是有用信号，但不足以构成霸者移植支持** | Itemzflow Game Manager；PS4/PS5 downloads；`ITEM00001` package identity | PKG-Zone 将 Itemzflow 列为 PS4 home menu 替代方案，content ID 为 `IV0002-ITEM00001_00-STOREUPD00000000`，title ID 为 `ITEM00001`，并提供 PS4/PS5 downloads。 | 这说明 PS4/PS5 homebrew 工具存在，但不能证明 《歧路旅人0》 可以加载 loose pak overlay。 | **仅研究用途** |
</details>

## 与其他 MOD 的兼容性
霸者移植很可能与几乎所有其他 pak MOD 不兼容；DLL MOD 也在很大程度上依赖 `CotCPort_DataBase` 中的约定，不过这些内容可配置。
<details>
<summary><strong>查看 CotCPort_DataBase 文件树</strong></summary>

```
CotCPort_DataBase
├── AIBattle
│   ├── TacticalActionList
│   ├── TacticalAssignList
│   ├── TacticalList
│   └── TacticalSkillList
├── Battle
│   ├── BattleAbortConditions
│   ├── BattleActionID
│   ├── BattleCameraParams
│   ├── BattleCameraSet
│   ├── BattleEventCommand
│   ├── BattleEventList
│   ├── BattleParams
│   ├── BattlePositions
│   └── BattleVoiceList
├── Character
│   ├── CharacterAnimationID
│   ├── CharaGuest
│   ├── CharaID
│   ├── CharaNpcList
│   ├── CharaPlayer
│   ├── CharaPlayerSpecialSkillList
│   ├── CharaRuneInitialOpenTypeID
│   ├── CharaSkillLearningPoint
│   ├── CharaSpecialStrengthenPoint
│   ├── CharaStatusID
│   ├── JobID
│   ├── LevelParameterTable
│   ├── LevelPointTable
│   └── PlayerMemberSet
├── Effect
│   ├── AttachEffect
│   ├── EffectList
│   └── PlayerAttachEffectList
├── Enemy
│   ├── EncountEffectTypeID
│   ├── EncountVolume
│   ├── EnemyBattleAnimSet
│   ├── EnemyDropID
│   ├── EnemyFormations
│   ├── EnemyGroups
│   ├── EnemyID
│   ├── EnemyModeID
│   ├── EnemyParts
│   ├── EnemyReinforcements
│   ├── EnemyTypeID
│   ├── EnemyWeakChangeID
│   ├── EnemyWeakID
│   ├── EnemyWeakLockID
│   ├── SymbolEnemyGroupList
│   └── SymbolEnemyList
├── Event
│   ├── EventFinishProcessList
│   ├── EventFlagVariableList
│   ├── EventList
│   ├── VJoin
│   │   └── (已将 《大陆的霸者》日服/国服/国际服的全部 8288 个事件按 《歧路旅人0》格式导入为 Join 事件；未编辑任何 《歧路旅人0》原生 Join 事件) 
│   └── VSub
│        └── SQ 
│            └── (已编辑“英雄之岛·过去”事件，使各武器由对应职业塔 Boss 守护) 
├── GameText
│   ├── Localize
│   │   ├── EN-US
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   ├── KO-KR
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   ├── ZH-CN
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   └── ZH-TW
│   │       ├── SystemText
│   │       │   ├── GameTextCharacter
│   │       │   ├── GameTextEnemy
│   │       │   ├── GameTextFC
│   │       │   ├── GameTextItem
│   │       │   ├── GameTextMap
│   │       │   ├── GameTextPC
│   │       │   ├── GameTextQuest
│   │       │   ├── GameTextSkill
│   │       │   └── GameTextUI
│   │       └── TalkText
│   │           ├── GameTextEvent
│   │           └── GameTextNPC
│   ├── SystemText
│   │   ├── GameTextCharacter
│   │   ├── GameTextEnemy
│   │   ├── GameTextFC
│   │   ├── GameTextItem
│   │   ├── GameTextMap
│   │   ├── GameTextPC
│   │   ├── GameTextQuest
│   │   ├── GameTextSkill
│   │   └── GameTextUI
│   └── TalkText
│       ├── GameTextEvent
│       └── GameTextNPC
├── Item
│   └── ItemList
├── Level
│   └── ExistSublevelParam
├── Map
│   ├── MapLightSet
│   ├── MapListTable
│   ├── MapPathListTable
│   ├── MapRegionCategory
│   ├── MapReplaceList
│   ├── TownList
│   ├── WorldList
│   ├── WorldMapList
│   ├── WorldMapMaskList
│   └── WorldMapRouteList
├── MonsterArena
│   └── MonsterArenaList
├── Narration
│   └── NarrationList
├── Npc
│   └── (929 个资产；《歧路旅人0》与《大陆的霸者》共有的每个 NpcSetList/NpcTalkList 均已用《大陆的霸者》侧数据补全，《歧路旅人0》完全缺失的地图 NPC 数据已按 《歧路旅人0》格式导入) 
├── Object
│   ├── ObjectList
│   ├── RecoveryPointList
│   ├── TreasureBoxList
│   └── TreasureBoxRewardList
├── PartyChat
│   └── PartyChatList
├── Quest
│   ├── LimitConditions
│   ├── MainStoryRingList
│   ├── QuestAggregatedPoint
│   ├── QuestAggregatedTaskList
│   ├── QuestList
│   ├── QuestTaskList
│   └── QuestTrigCondition
├── Skill
│   ├── ResistType
│   ├── SkillAilmentOffsets
│   ├── SkillAilmentSkillReplaceList
│   ├── SkillAilmentType
│   ├── SkillAvailID
│   ├── SkillAvailMagnificationConditionList
│   ├── SkillConditionList
│   ├── SkillEffectiveID
│   ├── SkillEnhancedList
│   ├── SkillHitRateList
│   ├── SkillIconList
│   ├── SkillID
│   ├── SkillResistAilmentID
│   ├── SkillResistList
│   ├── SkillUniqueIconID
│   ├── SkillVisitorList
│   ├── SpecialSkillGrowthList
│   ├── SpecialSkillID
│   └── WeaponType
├── SkillBoard
│   └── SkillBoardData
├── Sound
│   ├── PartVoiceID
│   ├── SoundList
│   ├── SoundSheetList
│   ├── VoiceEffectID
│   └── VoiceSymbolList
├── Texture
│   ├── CharaTexID
│   ├── EnemyTexID
│   ├── LinkIconList
│   └── TextureID
├── Trigger
│   ├── CurveID
│   └── LightAreaPreset2
└── Village
    ├── VillageBuildingData
    ├── VillageResidentFavoriteBuilding
    ├── VillageResidentInterior
    └── VillageResidentSkill
```

</details>

<a id="how-to-recruit-the-new-travelers"></a>
## 如何招募新增旅人
目前，角色的**旅人故事**会作为其招募事件。不过，这只覆盖了 280+ 名新增角色中的 140 名。我计划为本 MOD 中的每一名新增可操作角色都加入**独特招募事件**，但这还需要一些时间。

现在，你可以使用 `CotCPort_Extras.7z` 压缩包中的**可选** pak MOD `RecruitEvent_p.pak`，一次性招募本 MOD 提供的所有角色。
为此，请前往下图所示的**许愿谷小径**，与**健忘的松鼠**对话：

<img width="1239" height="541" alt="image" src="https://github.com/user-attachments/assets/a39b0888-f820-462b-81c0-1a747ab59528" />

随后会出现包含**四个选项**的提示：
 1. 招募所有 MOD 角色 + 将他们加入许愿谷
 2. 招募所有 MOD 角色
 3. 解除所有 MOD 角色的招募
 4. 将所有 MOD 角色从许愿谷移除

当 MOD 更新加入新的可操作角色时，只要 `RecruitEvent_p.pak` 位于你的 pak 文件夹中，就可以通过**再次与同一 NPC 对话**来招募他们。
对于已经招募过的角色，可见的加人效果会被**抑制**，因为 `EntryPlayerCharacter` 会返回 `false`，且 opcode `10029  EvCmAddPartyMember` 不会强制弹出对话。但这并不意味着 `10029` 会对已招募角色跳过。只是它所做的事情非常轻微：已招募玩家仍可能被处理、HP/SP 被恢复、在队伍中被定位、刷新当前队伍 cue sheet 等。他们**不会**丢失装备或已装备技能，属性不会重置，也不会被重复招募。

**警告 1：** 由于招募事件现在位于**许愿谷小径**，请小心不要过早招募角色。在序章期间，你所有队伍成员的**装备栏都会被锁定**。在此任务状态下招募的任何角色，其装备栏也会被锁定。完成序章后这个效果**不会自动解除**，你必须将所有受影响角色**解除招募后重新招募**。

**警告 2：** 被招募角色的属性会根据**与松鼠互动的人**来缩放，也就是取决于谁是队伍领队（即编队菜单左上角的角色）。

## 联系方式
你可以在 **BravelyPath Modular** Discord 服务器的 **#cotc-characters-mod** 频道联系到我；那里也有其他贡献者和 MOD 作者。服务器邀请链接：https://discord.gg/7YmAFJ3h5Z

<a id="acknowledgements"></a>
## 致谢
感谢以下人员：
- **BoltGSR** 和 **MarvinCx886**：向我提供 《大陆的霸者》日服数据
- **Oersted**：提供 《大陆的霸者》中文版数据、完整技能表、精灵图和自定义单位创意，并提供帮助
- **Vat**：提供角色与敌人的扩展精灵图，并将许多贴图 cooking 到游戏内显示清晰锐利的状态
- **Solo Traveler**：协助测试大量功能，为困难模式版本提供想法，并详细描述自定义竞技场战斗
- **igoticecream**：为《歧路旅人0》生成 SDK，我在开发中频繁参考
- **MarvinCx886**：研究出如何正确实现自定义事件，完成最初几次地图移植尝试并提供宝贵见解，同时提出修复怪物竞技场滚动崩溃的思路
- **MarvinCx886** 和 **JohnZ**：研究出如何从《大陆的霸者》移植 Sound 数据，并撰写了详尽说明
