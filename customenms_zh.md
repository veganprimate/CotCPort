### **LANGUAGE:**  [English](customenms_en.md) | [日本語](customenms_jp.md) | [中文](customenms_zh.md) | [한국어](customenms_kr.md)

在《Octopath Traveler 0》中添加新敌人与新战斗，主要围绕编辑 `AIBattle`、`Enemy`，以及（可能还包括）`Skill`、`Texture`、`Battle` 下的 DataBase 文件展开。下面会解释所有相关文件；标题前星号数量表示“编辑该文件的必要程度”（星号越少＝只在更特殊的场景才会用到），但仍建议通读全文，以便了解在不同目的下应修改哪些文件。

## `Enemy/` 下
### (***) `EnemyFormations`
用于在战斗场景中指定新的敌方编队（Formation）。`m_PositionID` 以 `BattlePositions.m_id` 作为输入（`BattlePositions` 位于 `Battle/`）。如果你要做“很多敌人同场”的自定义战斗，可能需要查看这些文件，找到一个有足够多非零 `m_PositionID` 的编队，或自己新建编队。某些 `PositionID` 会因战斗地图不同而产生差异非常大的站位效果。

### (***) `EnemyBattleAnimSet`
CotC 会给敌人分配一个指向特定 Flipbook 资产的 ID；而 OT0 使用的是一组 ID，每个 ID 对应一个这样的资产（`m_EnemyTextureIDs` 以 `Textures` 下 `EnemyTexID` 的 `m_id` 作为输入）。
通常 `m_EnemyTextureIDs[0]` 指向精灵（sprite）的待机动画（后缀 `Idl00`），而 `m_EnemyTextureIDs[1]` 到 `m_EnemyTextureIDs[3]` 则是攻击动画（后缀 `Atk00` 到 `Atk02`）。你至少需要为待机动画添加一个 flipbook 资产（即：在 `EnemyTexID` 下新增一个指向新路径的条目，并在这里新增的 `EnemyBattleAnimSet` 条目中，把该新条目的 `m_id` 写到 `m_EnemyTextureIDs[0]`）。其他动画则是可选的。

请注意：CotC 的 `EnemyType` 里的 `m_FlipbookID` 似乎会以不同方式使用这个数组的额外元素：虽然索引 0 仍对应待机贴图，但 CotC 会在索引 1 给某些敌人分配一张“不可见贴图”（例如 “Shadow of Twin Worlds” 战里的 Shadow）。

使用 NPC 贴图的战斗 **不需要** 在 `EnemyBattleAnimSet` 下额外新增条目，因为 NPC 通常已经有自己的战斗用 spritesheet（经常以 `_B` 结尾）。你只需要在 `EnemyType`（见下方）里指定该 NPC 的 `CharaID` 即可（也可参见 [如何添加自定义角色](customchars_zh.md)）。

### (**) `EnemyParts`
如果你的自定义 Boss 由多个部位组成（例如 Or'Galdera、Amatsukami no Orochi），你需要在此文件中为每个部位分别指定。不过也有一些（例如 Shadow of Twin Worlds）完全不使用此文件的情况。

### (****) `EnemyTypeID`
这个资产基本上是 OT0 中某个敌人“战斗精灵表现（presentation）”的主描述符（针对一个敌人 archetype）。它会告诉游戏：
- 该用哪一套动画集，或改用 NPC/角色 spritesheet（`m_AnimationSetIDs` 以 `EnemyBattleAnimSet` 的 `m_id` 作为输入；`m_NpcCharaID` 以 `Character/` 下 `CharaID` 的 `m_id` 作为输入）
- 如何缩放与定位精灵以及各种叠加层（overlay）：
  - 受击火花（hit sparks）（`m_HitPosX`,`m_HitPosY`,`m_HitScaleX`,`m_HitScaleY`）
  - 护盾与弱点叠加层（shield/weakness overlay）
  - 敌方特效（`m_EffectOffsetX`,`m_EffectOffsetY`，以及 `m_EffectFromBottomOffsetY`：相对精灵底部的偏移，常用于地面类特效）
  - 伤害数字（`m_DamageOffsetX`,`m_DamageOffsetY`）
  - 状态异常（`m_AilmentInfoOffsetX`,`m_AilmentOffset`,`m_SupportOffsetY`,`m_SupportOffsetX`）
  - 光标（cursor）（`m_CursorOffsetX`,`m_CursorOffsetY`）
- 该敌人 archetype 关联哪些敌方部位（`m_PartsID`）
- 该敌人类型使用哪些音效/语音：
  - `m_CallSE`（即“呼叫音效 / call sound effect”）
  - `m_VoiceSymbol`（一个整数，可能索引到 `Sound` 下的 `VoiceSymbolList`）
  - `m_CueSheet`（可能索引到 `Sound/` 下的 `SoundSheetList`，并选择该敌人的语音 cuesheet）
- 应生成哪些附着特效（attach effects / VFX）以及生成位置（`m_AttachEffect`,`m_BattleAttachEffectOffsetX`,`m_BattleAttachEffectOffsetY`,`m_FieldAttachEffect`,`m_FieldAttachEffectOffsetX`,`m_FieldAttachEffectOffsetY`）
- 行动顺序图标（turn order icons）应使用引用贴图（通常是 flipbook/角色 spritesheet 的第一帧）的哪个位置（`m_OrderTexUS`, `m_OrderTexVS`；大图标——例如该角色正在行动时——使用 `m_OrderTexUL`、`m_OrderTexVL`），以及在顺序图标中渲染的缩放比例（`m_OrderScaleS`；`m_OrderScaleL`）。例如 Shadow of Twin Worlds 的 Shadow 使用了非常小的 order scale，以保证它的整个身体都能显示在顺序图标里。
- 杂项（Miscallaneous）：
  - `m_OverrideOrderIconAnimSetID`：用于顺序图标想改用特殊贴图资产的情况
  - `m_AttachEffectInGodbeastProduction`：OT0 与 CotC 中均未使用，与“神兽”特效相关
  - `m_AddStartUV`：是否添加起始 UV 偏移（位置）的标志
  - `m_LinkOwnerAnim`：若为 true，部位/附着物可能会跟随本体的动画时间
  - `m_DefeatEffect`：死亡时播放的 Effect ID（例如 Bestower of All 的特殊死亡特效）
  - `m_ChangeColorID`：指向某个颜色变化表的 ID，可能与 `Character/` 下 `CharaColorChangeParams` 相关。OT0 里仅在 Zero/“被戒指选中的人”与其影子对战时使用（`ChangeColorID` 6）。CotC 的 `EnemyType` 完全没有这个键

### (**) `EnemyWeakID`
用于管理可分配给敌人的不同“弱点/耐性配置”。`m_ResistWeapon` 与 `m_ResistMagic` 以 `Skill/` 下 `ResistType` 的 `m_id` 作为输入；`ResistType` 为 1 时，还会把指定武器/魔法类型作为弱点添加。指定的武器与魔法类型的顺序，很可能遵循 `Skill/` 下 `WeaponType`/`MagicType` 资产的 `m_id` 顺序，即：
```
{
            "m_id": 0,
            "m_ResistWeapon": [
              0, (Sword)
              0, (Polearm)
              0, (Dagger)
              0, (Axe)
              0, (Bow)
              0, (Staff)
              0, (Polearm)
              0, (Tome)
              0, (Fan)
              0 (无属性物理伤害；其他武器类型不计入；即使已分析，游戏内仍显示为 ?)
            ],
            "m_ResistMagic": [
              0, ("None"：似乎也不会显示弱点图标)
              0, (Fire)
              0, (Ice)
              0, (Lightning)
              0, (Wind)
              0, (Light)
              0, (Dark)
              0, (无属性魔法伤害；其他魔法类型不计入)
              0 (Poison：即中毒敌人的毒伤会削掉 1 点护盾)
            ]
          }
```
**注意：** OT0 在同一个敌人身上最多只能显示 8 个弱点。

### (**) `EnemyWeakChangeID`
用于管理敌人“弱点变化”的方式。`m_WeakIndices` 以 `m_EnemyWeakID.m_id` 作为输入；这些弱点变化的 `m_id` 可以被分配给例如某个 avail（也可参见 [如何添加自定义技能与 avail](customskls_zh.md)）。

### (**) `EnemyWeakLockID`
用于管理敌人“锁定弱点”的方式。看起来没有用于锁定无属性魔法伤害与毒伤害的字段。

### (****) `EnemyID`
这是游戏的敌人主记录：定义属性/弱点/奖励/UI 标志，并指向 `EnemyTypeID` 以决定表现（动画集、贴图、偏移、语音、特效），同时指向 `TacticalAssignList` 与 `TacticalSkillList` 以决定敌人行为与技能组（见下方 “`AIBattle` 下”）。
- `m_Label`：敌人的标签。可能被内部使用；建议不要新增与现有标签冲突的条目
- `m_DisplayName`：指向 `GameTextEnemy.m_id` 的 ID（对所有语言通用）
- `m_WeakID`：指向 `EnemyWeakID.m_id` 的 ID（见上文）
- `m_DisplayLevel`：敌人显示等级。遗憾的是，即使 `m_LevelUI` 设为 true 也不会显示
- `m_BreakRate`：敌人“破盾/Break”状态下承受伤害的倍率（百分比）
- `m_DamageRate`：敌人未破盾时承受伤害的倍率（百分比）
- `m_MaxSLD`：敌人的护盾数。尽管键名暗示是最大值，但某些战术与技能可以把它设置得更高（例如 Lucian the Glorious）
- `m_MaxHP`：敌人的 HP。同样，技能与战术也可提高它
- `m_MaxSP`：敌人的 SP。多数敌方技能 SP 消耗为 0，因此该数值主要用于限制某些敌方技能的使用次数
- `m_AtkP`：物攻
- `m_AtkM`：属攻/元素攻击
- `m_DefP`：物防
- `m_DefM`：属防/元素防御
- `m_Agi`：速度/敏捷
- `m_Crt`：会心
- `m_CrtDef`：会心防御；看起来只有敌人有这个数值
- `m_Hit`：命中
- `m_Avd`：闪避
- `m_ResistAilmentID`：给敌人分配一个“状态异常抗性”ID（见 `Skill/` 下的 `SkillResistAilmentID`）
- `m_ResistAilment`：一个与特定异常抗性率对应的数组；可能在所分配 `ResistAilmentID` 的 `m_ResistAilments` 数组被填满时使用。看起来只被 Caits 使用
- `m_TacticalAssignID`：为敌人分配 `TacticalAssignList.m_id`，对应战斗中使用的一组战术（见下方 “`AIBattle` 下”）
- `m_SkillsID`：为敌人分配 `TacticalSkllList.m_id`，对应战斗中使用的一组技能（见下方 “`AIBattle` 下”）
- `m_IsBoss`：许多 Boss 使用，但城镇 NPC 也会用。不确定其功能效果
- `m_Exp`, `m_Money`, `m_JP`：击败后获得的 EXP、金钱、JP
- `m_PetExp`：在 CotC 与 OT0 中始终为 -1。设为非 -1 看起来也没有效果
- `m_DropReward`：可能掉落的道具（可用 ID 见 `EnemyDropID`）
- `m_StealReward`：可偷取的道具（可用 ID 见 `Item/` 下的 `ItemList`）
- `m_StealLeafNum`：可偷取的 Leaves（金钱）数量
- `m_StatusOffset`：敌人状态条（弱点与护盾数）的偏移
- `m_Bottle`：含义不明。OT0 使用 1-3。值 3 在 OT0 中只用于 Wappa。CotC 还使用 4 与 5
- `m_NameUI`：是否在 UI 上显示敌名
- `m_LevelUI`：是否在 UI 上显示 `m_DisplayLevel`（未使用/无效）
- `m_HpUI`：在 OT0 中无效
- `m_ShieldUI`：若为 true，看起来会让敌人的护盾图标变暗
- `m_RaceIndices`：给敌人分配的种族，可能影响伤害计算
- `m_DisplaySpecialSkillGauge`：显示敌人的 BP 计数器/槽的标志
- `m_GenerateSpecialSkillValueOrverTurn`：敌人每回合生成的 BP 点数
- `m_ReduceSpecialSkillValueBreak`：敌人在破盾后损失的 BP 点数
- `m_MaxSpecialSkillValue`：敌人的最大 BP 点数；高于 20 会导致游戏崩溃
- `m_OutsideTarget`：敌人不可被选为目标；若场上只剩该敌人，队伍成员也会跳过行动（Bestower of All 在戒指被打破前会使用此标志）
- `m_NotAction`：若为 true，不允许敌人行动（Bestower of All 在所有戒指被击败前会使用）
- `m_UnknownLv`：OT0 未使用
- `m_Immortal`：若为 true，敌人的 HP 永远不会低于 1

### (**) `EnemyReinforcements`
用于战斗遭遇的辅助表：定义战斗中途可生成的额外敌人（增援）以及它们应出现的位置。
- `m_Reinforcements` 以 `EnemyID.m_id` 作为输入
- `m_Positions` 以 `BattlePositions.m_id` 作为输入（对应敌群的编队不一定需要包含该位置）
唯一使用 `EnemyReinforcements.m_id` 的资产是 `Skill/` 下的 `SkillAvailID`。

### (****) `EnemyGroups`
记录所有敌人组（enemy groups）。要让你的战斗能在游戏中触发，必须在这里添加条目。
- `m_Label`：请确保新增敌人组的 label 唯一且不与现有条目冲突。事件通过这个 label 触发与某个敌人组的战斗。如果你打算把该条目用于怪物竞技场（Monster Arena），就不要再把同一条目用于其他目的，否则在击败后可能会弹出竞技场菜单，甚至导致崩溃
- `m_Inescapable`：决定战斗是否可逃跑
- `m_DisableRetire`：OT0 中始终为 false，在 OT0 中似乎无效。可能与 CotC 中“战斗中挂起手机”之类功能相关？
- `m_SkipResult`：决定是否跳过战斗结算界面（获得 EXP/金钱/JP 等）
- `m_IgnoreDefeat`：决定被该敌人组击败时是否不出现 Game Over。仅用于 King Pardis III、Edoras Defenders of Fame/Power/Wealth、以及 Colossal Blightant。若敌人处于怪物竞技场中，看起来会被覆盖：失败后会把你送回竞技场
- `m_SkipBgmResult`：决定是否播放胜利主题曲
- `m_BanGodBeast`：禁止在战斗中使用 Divine Beast。OT0 未使用。在本 MOD 中如果你把 Divine Beast 技能加到某些角色的技能盘里，这个标志可能会阻止其使用（未测试）
- `m_PlayerMemberSetID`：强制你的队伍由引用的 `PlayerMemberSetID` 中指定的角色组成（也可参见 [如何添加自定义角色](customchars_zh.md))）
- `m_Bgm`：设置战斗 BGM；可用项见 `Sound/` 下 `SoundList`
- `m_NightBgm`：设置夜间战斗 BGM。OT0 未使用；是否有效未测试
- `m_DarkBgm`：与 `m_NightBgm` 相同？CotC 的条目看起来这两个键总是相同值
- `m_BgmType`：
- `m_ResultBgmWin`：指定自定义胜利主题
- `m_ResultBgmLose`：指定自定义 Game Over 主题
- `m_Camera`：指定使用的相机套装（输入：`BattleCameraSet.m_id`）
- `m_Formation`：指定敌群编队（输入：`EnemyFormations.m_id`）
- `m_EnemyID`：指定场上的敌人（输入：`EnemyID.m_id`）。把该数组扩展到 6 个敌人以上不会立刻崩溃，但新增敌人不会出现在时间线/行动顺序中，也不会有弱点/护盾/状态叠加层；且在它们轮到行动后游戏会崩溃（错误与 allocator 元数据损坏/指针损坏有关）。可能可以通过“增援调用”绕过
<img width="829" height="459" alt="image" src="https://github.com/user-attachments/assets/433a1c36-e857-4b4c-aaae-07399ddac40b" />

- `m_EventIndices`：（战斗）开始时或战斗中播放的事件（战斗中对话等）。可用值很可能是 `BattleEventList.m_id` 而不是 `EventList.m_id`
- `m_AbortCondition`：中止战斗的条件。可用 ID 可能在 `BattleAbortConditions` 下
- `m_DefeatPriority`：OT0 与 CotC 中始终为 false。不确定是否有任何效果
- `m_ForcedWinAbortCondition`：设定强制胜利的自定义条件。可用 ID 可能在 `BattleAbortConditions` 下
- `m_IsAllEnemyDeadOnForceWin`：（未测试）是否在满足 `m_ForcedWinAbortCondition` 的条件时令所有敌人直接死亡？
- `m_ForcedLoseAbortCondition`：设定强制失败的自定义条件。可用 ID 可能在 `BattleAbortConditions` 下
- `m_ImportantFlags`：6 个不同标志，OT0 中全部未使用，测试也无法确定其含义
  - Flag 1：CotC 中主要由 Job Tower Boss 使用
  - Flag 2：仅由 `Dice_Bolaven_Rank04_BossGr03` 使用（与 Idore、Roguish Riven Soldier I、Roguish Riven Soldier II 的战斗）
  - Flag 3：同样只用于 Flag 2 的那个敌群
  - Flag 4-6：两作中都从未使用
- `m_PetDear`：取值为 -1、0、1
- `m_DisableBattleEndWipe`：被许多最终 Boss 使用（Bestower of All、Or'Galdera、Side Solistia 的 Galdera）以及 Magitek Armor
- `m_DisableBattleEndTraining`：阻止训练场中的队伍成员获得 EXP、JP 等。用于 Emerald Direwolf 战（仅在 Laurana 在场时）、Bestower of All 第7章（Sazantos 回忆）中的战斗等
- `m_DisableSupporter`：决定战斗中是否可召唤帮手
- `m_EncountEffectTypeID`：指定特殊遭遇演出（例如 FF6 联动遭遇）。竞技场战斗需要把它设为 0，否则会软锁。可用 ID 可在 `EncountEffectTypeID` 下找到
- `m_EnablePlayerShield`：OT0 与 CotC 中始终为 false
- `m_DisableGrade`：OT0 中始终为 false，而 CotC 不是。可能是用于忽略武器“品级/等级”的标志
- `m_IsContainRareEnemy`：可能用于告诉游戏哪些敌群包含稀有敌人，从而让“提高稀有敌遭遇率”的饰品生效

### (**) `EnemyModeID`
告诉游戏：哪个 mode ID 对应要对场地或敌人施加的哪种效果。这些效果可被分配给 avails。用于 Boss 进入增幅(Boost)模式并显示特殊效果时。

### (**) `EnemyDropID`
用于记录所有可分配给敌人的掉落 ID。

### (**) `EncountVolume`
决定危险度、可遭遇的敌群以及各自遭遇概率。encount volume 会在 `EncountList` 中与地图关联。地图可能改变危险度，因此对应可能会切换到不同的 encount volume。

### (**) `EncountList`
OT0 的随机遭遇规则表：指定每张地图可遭遇哪些 encount volume（`m_EncountVolume` 为 `m_EncountVolume.m_id`）、遭遇前最少步数（`m_EncountStepMin`），以及遭遇发生所需满足的条件。

杂项键：
- `m_DisableCondition`：满足该条件时禁用该遭遇
- `m_RandomStepA` 与 `m_RandomStepB`：随机步数范围/曲线的参数。两作中这两个键看起来总是相同值
- `m_FirstEncountStepRatio`：可能是“进入地图/区域后的第一次遭遇”的倍率，用于让首次遭遇更早或更晚

### (*) `EncountEffectTypeID`
记录不同的遭遇演出效果；`m_SeId` 可能用于设置该演出发生在哪张地图上。

### (*) `EnemyGroupsByCondition`
在满足特定条件时生成的敌群集合。用于在 Bestower of All Chapter 7 中，让 Accurst Flame 与 Ringbearer 同时出现。

### (**) `SymbolEnemyList`
指定符号敌/精英敌在大地图上的精灵/物件，以及其对应敌群与等级。
- `m_GroupId`：该符号敌条目关联的敌群
- `m_Index`：该敌人在敌群中的索引。当多个符号敌共享同一个 GroupId 或生成控制器时常用
- `m_Dir`：符号敌默认面朝方向
- `m_ObjectId`：用特定 object 覆盖符号敌的地图外观（例如 Bestower of Power Ch. 3 中 Tatloch 的船）
- `m_RepopCount`：重生/再刷次数（即符号敌多频繁重生）。OT0 敌人默认 0；CotC 并非如此（重新加载地图后敌人不会立刻再出现）
- `m_Redrawing`：OT0 与 CotC 中始终为 0
- `m_TargetLevel1` 与 `m_TargetLevel2`：可能表示符号敌的等级范围
- `m_Scale`：野外敌人符号的缩放倍率（纯视觉/碰撞尺寸）

### (**) `SymbolEnemyGroupList`
看起来是把 `SymbolEnemyList` 表中的符号敌 ID（`m_id`）分配给某些内部 ID，这些内部 ID 对应特定地图上的特定符号敌点位。`m_SymbolEnemyId` 数组可能决定：当 `m_StartSymbolEnemyId` 的符号敌被击败后，在再刷时该点位会出现哪些符号敌，并为每个符号敌在 `m_probability` 下指定概率。看起来 CotC 与 OT0 都没有使用这些数组。

## `Battle/` 下
### (***) `BattleCameraParams`
与战斗相机预设/配置对应的一组 ID。值 `-1.0` 似乎会被游戏解释为“使用默认值”：
- `m_PosotionX/Y/Z`：战斗相机在 3D 空间中的位置
- `m_RotationX/Y`：相机在 X/Y 方向的旋转/倾斜
- `m_FOV`：视野角。视野角越窄越像“拉近/长焦”。注意：改变 FOV 时队伍位置是固定的
- `m_SequencerPath`：Cinematic/Sequencer 绑定，可能指向某个 Level Sequence 或由 Sequencer 驱动、用于战斗镜头切换/锁定目标的相机 rig 资产
- `m_FocalDistance`, `m_FocusDistance`：焦平面距离（可能一个是旧命名一个是新命名（CotC 只有 `m_FocalDistance`），或属于不同系统）。OT0 中两者总是 `-1.0`，CotC 使用不同的焦距
- `m_FocalRegion`：对焦区域大小
- `m_NearBlurSize` 与 `m_FarBlurSize`：根据距离决定的模糊强度
- `m_MaxBokehSize`：散景模糊的上限
- `m_Scale`：可能是相机距离缩放/镜头尺度覆盖

### (***) `BattleCameraSet`
一组相机配置集合，用于分配给敌群（`BattleCameraSet.m_id` = `EnemyGroups.m_Camera`）。CotC 中不存在（CotC 战斗只使用一个中性的相机配置）。

### (***) `BattlePositions`
定义用于 `EnemyFormations` 或 `EnemyReinforcements` 等的 PositionID。`m_Priority` 可能决定在坐标冲突时哪个精灵显示在前，或决定选择敌人的顺序。

### (***) `BattleEventList`
很可能是可在 `EnemyGroups.m_EventIndices` 中分配给敌群的（战斗）事件。
- `m_IsMustPlay`：事件是否可跳过（？）
- `m_EventCommand`：触发时执行的命令脚本 ID（指向 `BattleEventCommand`）
- `m_EventConditions`：条件类型 ID，可能与其他文件中的 `m_Conditions` 数组相同（例如 `TacticalList`、`SkillConditionList`、`TacticalActionList` 等）
- `m_EventParams`：条件参数
- `m_EventEnemies`：可选过滤器，用于指定条件适用的敌方槽位/敌方索引/部位（看起来两作都未使用）

### (***) `BattleEventCommand`
包含战斗事件序列中的脚本步骤。`BattleEventList` 描述“在什么条件下运行什么”，而 `BattleEventCommand` 看起来负责执行实际步骤（显示对话、播放动画、生成特效、切换敌方贴图、改变 BGM 等）。

- `m_NextWait`：执行该命令后到进入下一步的延迟（秒）
- `m_isWaitSkillTime`：若为 true，等待技能动画结束
- `m_isSwitchActionSkip`：允许/强制跳过行动序列
- `m_PlayBgmBlock`：可能在某些事情完成前阻止 BGM 变更
- `m_PerformerID`：可能是队伍/同行/敌方 performer 列表的索引，或特殊 ID
- `m_PerformerIsLeader`：以队长作为 performer
- `m_PerformerEnemy`：若非 0，表示 performer 是敌人（看起来以 `EnemyID.m_id` 为输入）
- `m_TargetID`：可能指定强制行动/技能的目标
- `m_EnableTarget`,`m_EnableAction`：在该命令期间/之后启用玩家选目标/行动的开关
- `m_NameID`：说话者名字的本地化 ID（在 `GameTextCharacter` 下）
- `m_TextID`：对白文本的本地化 ID（在 `GameTextEvent` 下）
- `m_BalloonType`：气泡/对话框样式（1 = 可能是普通气泡）
- `m_NoTail`：无气泡尾巴
- `m_BaloonOffsetX/Y`：相对 performer 的气泡位置偏移
- `m_NoName`：若为 true，隐藏说话者名字
- `m_NarrationID`：取自 `NarrationList.m_id`
- - `m_IsNextNarrationPage`, `m_BlackoutNarration`, `m_NarrationColorR/G/B`：旁白样式相关键
- `m_AnimID`：说话时（或前后）performer 播放的动画
- `m_DirID`：performer 的朝向/方向预设
- `m_isRewriteIdleMotion`：命令结束后继续保持 idle 覆盖
- `m_SoundID`：播放音效（可能是 `SoundList` ID）
- `m_SoundHandle`：可能是运行时 handle 槽（例如启动 SFX 并保存 handle，后续命令可停止/替换）（？）
- `m_EffectID`：生成特效（可能是 `EffectList` ID）
- `m_EffectHandle`：可能是用于后续操作/停止的运行时 handle 槽（？）
- `m_EffectPosition`：特效生成位置（performer、target、屏幕等）
- `m_BgmID`：更换 BGM（同样可能是 `SoundList` ID）
- `m_FadeTime`：BGM 变更的淡入淡出时间
- `m_IsRemoveAllAilments`：清除状态（可能用于阶段切换）
- `m_EnableRetire`, `m_DisableGameOver`：（未测试）在脚本战中强制启用/禁用 UI 选项与失败状态（？）
- `m_LastBattleMapColor`：可能用于战斗背景的色调控制
- `m_TextureChangeEnemyIdx`：要更换贴图的敌方槽位（`-1` 表示未使用）
- `m_ChangeEnemyTextureIdx`：切换到哪个贴图索引/槽位（`-1` 未使用）
- `m_TextureChangeTime`：贴图切换时机（延迟或交叉淡化时间，未测试）
- `m_EnemyPartsIndex`：受影响的敌方部位索引
- `m_EnemyPartsDisplay`：事件中显示/隐藏所引用的敌方部位
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`：`TacticalList` 中也存在。战斗引擎可能基于这些 flag 推进脚本（即隐式“下一步”标记）。例如：Shadow of Twin Worlds 用它来记录“在 Lucian 与 Lyblac 同时破盾后解锁 Shadow 弱点”的战术是否发生，并在 Lucian 与 Lyblac 从破盾恢复后再次锁回弱点（因为看起来不存在专门针对该情况的条件）
- `m_JoinTrigger`：可能与“单位现在加入战斗”的事件相关（例如同伴在战斗中途加入）
- `m_UniqueProcess`, `m_OptCharacterID`, `m_OptSkillID`：不符合通用结构的特殊命令类型

### (*) `BattlePlaybackData` 与 `BattlePlayback`
看起来像 OT0 的战斗预览/战斗演示配置：一个小型数据库，用于告诉游戏“对预览预设 X，让敌人 Y 施放技能 Z，并可选地显示/隐藏攻击特效”。

#### `BattlePlaybacks`
结构基本是：`PlaybackID` → `SkillID`（+ performer 槽位覆盖）+ VFX 可见性开关
- `m_EnemySkillID`：要播放的（敌方）技能（尽管所有技能都存放在同一个文件：`SkillID`）
- `m_SkillPlayEnemyIndex`：预览中由哪个敌方槽位/索引来施放（`-1`=“自动/默认”）
- `m_VisibleAttackEffect`：预览中是否显示“攻击特效”层（例如斩击/冲击叠加层、受击闪光、或额外 VFX，使动画可以以“干净”或“完整”方式预览）

#### `BattlePlaybackData`
这可能不是真正的运行时数据；它的布局与 `BattlePlaybacks` 类似，但每个条目都有一行奇怪的内容：
```
"m_EnemySkillID_01": "m_EnemySkillID_01"
```
可能原因包括：
1. FModel 以令人困惑的方式 dump 出了某种 schema/template/struct 默认值（例如默认对象/结构体里该字段是 FName，序列化表现异常）
2. 这是一个“多槽位”的旧表，原本应有 `m_EnemySkillID_01`、`m_EnemySkillID_02` 等字段，但 dump/export 把值弄乱了
3. 这是一个表，其中 `m_EnemySkillID_01` 实际上应当是指向另一张表的行句柄/键（FName），而 dump 用了属性名替代了真实存储值
**注意：** Switch 版本中该表的 `m_SkillPlayEnemyIndex` 值不同；Steam demo 版本还缺少部分条目。

### (*) `BattleResultBonus`
记录不同战斗结算加成（“No Damage”“Overkill”“Break”“1Turn Kill”等）的倍率与本地化 ID。`m_Param` 看起来表示哪个数值（EXP、JP 或金钱）获得加成：
- 0：金钱
- 1：JP
- 2：EXP
是否存在 2 以上的未使用参数尚未测试。

### (**) `BattleVoiceList` 与 `BattleVoiceArenaList`
定义战斗中由队伍成员或旁白播放的各类语音台词（例如 Or'Galdera 战的特殊语音）。

### (***) `BattleAbortConditions`
OT0 的战斗终止/强制结局条件表：每个条目定义一小组谓词（predicates），当战斗中满足时，会使战斗中止（提前结束），或依据 `EnemyGroups` 中引用它的字段，被当作强制胜利/强制失败。
- `m_Conditions`：条件类型 ID（很可能与 `TacticalList`、`TacticalActionList`、`SkillConditionList`、`BattleEventList` 相同）
- `m_Params`：各条件的参数（含义取决于条件类型）
- 以及仅对某些条件类型有效的过滤器：
   - `m_AilmentTypes`（以 `SkillAilmentType.m_id` 为输入）
   - `m_StatusTypes`（可能以 `CharaStatusID.m_id` 为输入）
   - `m_WeaponTypes`（以 `WeaponType.m_id` 为输入）
   - `m_MagicTypes`（以 `Magictype.m_id` 为输入）
   - `m_EnemyID`（以 `EnemyID.m_id` 为输入）

### (*) `BattleActionID`
一个小型查表：把“action ID”（可能是 `SkillAilmentType.m_ActionID`）映射到具体的战斗动画/动作，并带有一个标志，表示该动画是否应使用 NPC/地图角色 rig 而不是标准战斗 rig 来播放。`m_UseNPC` 可能决定该 action 是否应使用 NPC 角色的动画集而不是通常的战斗精灵。

### (*) `BattleParams`
一组战斗相关参数。根据 Dumper-7 SDK 的 `Kingship_structs.hpp`（见 BravelyPath Modular Discord 的 #file-dump），其含义如下：
（原枚举保持不变）
```
enum class EBATTLE_PARAMS : uint8
{
    ...
};
```

### (*) `BattleDeathBlowPoint`
“Death Blow（特殊攻击）”量表获得点数的查表。每行本质上定义：当满足某个检查条件（类型 + 值）时，Death Blow 量表应增加多少。
- `m_CheckType`：检查的内容类型
- `m_CheckValue`：该类型的阈值/数量。看起来是计数
- `m_IncreasedPoint`：满足条件时增加多少 “Death Blow points”

### (*) `BattleLayoutSet`
可能是一个小型的战斗布景/布局预设表，把相机预设与敌方站位绑定在一起。不清楚它在游戏中的使用位置（例如似乎没有任何编队或增援使用 `PositionID` 270）。

## `AIBattle` 下
这些文件负责敌方 AI 行为，以及敌人使用哪些技能（以及如何使用）。

### (****) `TacticalList`、`TacticalAssignList`、`TacticalSkillList`
OT0 的敌方 AI “规则条款（rule clause）”表：每个条目定义一组条件，当满足时会让 AI 选择或启用某种行为。敌人通过 `TacticalAssignList` 被分配这些“规则”（其 `m_id` 对应 `EnemyID.m_TacticalAssignID`）；战斗技能通过 `TacticalSkillList` 被分配（其 `m_id` 对应 `EnemyID.m_SkillsID`）。
- `m_Presage`：可能是一个 boolean/enum，表示该战术是否会显示“预兆”（telegraphed action）
- `m_PresageSkillID`：与预兆关联的技能 ID（即预告的内容）。例如用于 Boss 宣告增幅(Boost)模式时（这些技能 ID **不会** 出现在该敌人的 `TacticalSkillList.m_UseSkills` 数组中）
- `m_OnEventFlgIndex`/`m_OffEventFlgIndex`：事件 flag 的门控与副作用，见 `BattleEventCommand`。可能用于 AI 行为的分支/编排
- `m_Conditions`：很可能与 `TacticalActionList`、`BattleEventList`、`SkillConditionList` 使用相同的条件，并且 `m_Params` 与过滤数组也相同
   - `m_AilmentTypes`（以 `SkillAilmentType.m_id` 为输入）
   - `m_StatusTypes`（可能以 `CharaStatusID.m_id` 为输入）
   - `m_WeaponTypes`（以 `WeaponType.m_id` 为输入）
   - `m_MagicTypes`（以 `Magictype.m_id` 为输入）
各条件的具体含义仍未被严格整理出来；以下资源可能有帮助：
https://github.com/veganprimate/CotCPort/tree/main/Conditions/Conditions_Datamine_ZH

杂项：
- 28 是唯一 OT0 独有的条件
- 条件类型 ID 在 OT0 与 CotC 中看起来相同，但 CotC 可能有一些 OT0 缺少的条件
- 10000+ 范围的 ID 往往与行动执行顺序有关：
  - Condition 10100 常用于“预告敌人进入增幅(Boost)模式”的技能
  - Condition 10304 会在其他条件满足的瞬间触发，例如 Lucian 与 Lyblac 同时破盾后 Shadow 的“面纱”立刻解除（Condition 718，`m_params` 为其在 `EnemyGroup` 条目中的对应索引）
  - 相比之下，Condition 10301 会让“面纱解除”延后到下一回合
  - 10711 看起来会让行动发生在回合开始时（例如 Shadow 在 Lucian 与 Lyblac 从第一次破盾恢复后使用）

### (****) `TacticalActionList`
看起来用于告诉游戏：当某个战术生效时，具体要做什么——用哪个技能（用索引指定）、打谁、如何在可能目标中选择，以及额外条件/优先级规则。
- `m_SkillIndex`：这不是 SkillID 本身，而是技能列表/数组中的索引（根据测试：是该敌人 `m_SkillsID` 对应的 `TacticalSkillList.m_UseSkills[]`）
- `m_SelectType`：要么是目标选择模式 enum，要么与如何选择诸如 skill index 之类的索引有关。根据测试：必须把 `m_SelectType` 设为 3，相关技能才会触发（`SelectType` 设为 1 时，会默认使用其第一个（？）技能）
- `m_FriendlyIndex`：在“友方侧”（从敌人视角？）要以谁为目标。此处为 `-1` 可能表示“没有固定友方；可能依据 `SelectType` 选择”（？）
- `m_PrioritySkill`：若设置，可能覆盖常规选择并强制某个技能索引（或把某技能索引标记为优先？）
- `m_TurnCount`：在指定战术 ID 的条件满足时，每隔 `N` 回合重复一次动作（`N` 为该键的值）
- `m_GroupIndex`：分组键，可能用于按阶段划分动作、限制每回合每组动作数量，或协调同一组内多个敌人的行为
- 额外的“动作级”条件（类似 `TacticalList`，但针对该动作）：`m_Conditions`, `m_Params`, `m_AilmentTypes`, `m_StatusTypes`, `m_WeaponTypes`, `m_MagicTypes`
