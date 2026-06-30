## **LANGUAGE:**  [English](Sound_Prog_en.md) | [日本語](Sound_Prog_jp.md) | [中文](Sound_Prog_zh.md) | [한국어](Sound_Prog_kr.md)

感谢 MarvinCx886 发现了这套工作流，也感谢 Zy 在这里编写了英文教程：
https://github.com/zhangyucn2/Octopath-traveller-audio-replacement-tutorial

希望这篇文章能为这套工作流提供更多背景与上下文。

### 《Octopath Traveler 0》的音频很可能是如何组织的
在基于 CRIWARE 的 Unreal 项目中，音频通常由以下部分组成：
- `.acb`：cue sheet 数据库（cue 名称、cue ID、元数据、路由等）
- `.awb`：wave bank（实际的编码音频数据载荷）
- 当你在编辑器中通过 CRIWARE 的 UE 插件导入 `.acb` 时，UE 侧会生成的“封装（wrapper）资产”（这些是引用 ACB/AWB 的 `.uasset` 文件）

在运行时，游戏触发音频通常会走以下几种模式之一：
- cue 名称 → cue ID → `.awb` 中的波形
- 直接使用 cue ID
- 在某个特定 cue sheet 内通过 cue 名称触发

就我目前所知，修改 Sound 相关的 DataBase 文件是否能让我们导入新的声音，并将其整合进 OT0 使用的同一套标识符/映射系统，目前仍不明确。下面会解释 `Sound/` 目录下每个 DataBase 文件的作用：

### `SoundSheetList`
看起来包含了 CRI Atom 的 CueSheet 封装资产注册表（也就是按上述教程，通过 CRIWARE 插件导入 `.acb` 时生成的 UE `.uasset`）。即便你添加了一个新的 cuesheet（导入新的 `.acb`），OT0 也不一定会使用它，除非（此处为推测）：
- 它在这个列表里，或
- 有代码通过路径直接加载它。

`m_InitialLoad` 暗示了开机/启动时预加载行为。

### `SoundList`
面向“游戏逻辑”的声音目录。游戏玩法层会请求“播放 `bgm_theme_scholar`”（或播放 sound ID 1），然后运行时会把它解析成：
- 使用哪一个 sheet（`m_Sheet` 引用 `SoundSheetList.m_id`）
- 播放约束（循环、最大同时播放数、优先级）
- 淡入/淡出

因此，sound label 和/或 sound ID 才是游戏使用的 API；而 `.acb` 里新增的 cue 如果没有对应的 `SoundList` 条目、并且游戏没有调用该条目，就永远不会被播放。

### `ReplaceSoundList` 与 `ReplaceBGMList`
这两个看起来是 OT0 独有的：根据是否满足某个任务条件，或是否在与某个特定敌群战斗，来替换声音/BGM。

### `VoiceSymbolList`
看起来是把游戏脚本/数据里使用的“语音符号字符串（voice symbol string）”映射到一个紧凑的整数 ID。

### `PartVoiceID`
语音类别/“子类型”。

### `ConvPersonalityVoiceID`
很可能指的是你为 Zero 可选择的 6 种不同声音。

### `VoiceEffectID`
总线路由（bus routing）/DSP 控制（例如选择一个 CRI bus（BUS4 等），并设置音量/LPF/混响发送等参数）。

### `GramophoneList`
记录哪个 Sound ID 对应哪个“乐谱（Music Score）”道具。
