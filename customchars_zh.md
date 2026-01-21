## **LANGUAGE:**  [English](customchars_en.md) | [日本語](customchars_jp.md) | [中文](customchars_zh.md) | [한국어](customchars_kr.md)
**注意：** 本指南不会讲解如何为新角色制作 spritesheet。相关细节请查看 BravelyPath Modular Discord；如有需要，也可以在 #modding-help 里提问。

1. 查看 `/Game/Content/Local/DataBase/Character` 下的 `CharaID`。该文件基本上用于存储 NPC 的贴图路径等信息。如果你要添加的 NPC 在这里没有对应条目，你就需要新增一个条目。当然，你也可以用这个文件来查找特定 NPC 的 spritesheet 贴图路径；很多时候直接扩展这些文件也能正常工作。
2. 在 `CharaPlayer` 中新增一个条目，在 `m_CharaID` 下引用 `CharaID.m_id`。角色名字需要添加到 `GameTextCharacter`。下面列出的参数（我认为）如下：
```
 "m_Param": [
        0, (无)
        250, (最大HP)
        60, (最大SP)
        0, (最大BP)
        0, (无)
        0, (HP)
        0, (SP)
        0, (BP)
        0, (无)
        72, (物攻)
        88, (物防)
        88, (属攻)
        96, (属防)
        64, (速度)
        72, (会心)
        80, (命中)
        80, (闪避)
        1, ("无"，可能是破盾伤害倍率)
        0, (无)
        0, (无)
        0 (无)
      ]
```
这里的“无”只是这些数值在 `GameTextUI` 中显示出来的名称，并不是说它们没有用途。

3. 为 `m_MaxParam` 也设置类似的数值。游戏会根据这两个数组在每个等级对数值进行插值。我不确定 `m_MaxParam` 是否代表角色在 99 级时的属性，因为多数《大陆的霸者》（CotC）角色的 HP max param 远超 9999，但测试表明它们也不会达到这些数值。
4. 确保新角色的 `m_InitialOpenType` 设置为 2（这样才能装备已学会的技能），并将 `m_InfluenceValue` 设置为 10（这样才能获得经验值 XP）。
5. 在 `PlayerMemberSet` 下创建一个新条目，引用你刚新增的 `PlayerID`（即 `CharaPlayer.m_id`，填到 `m_PlayerID`）。新条目还需要将 `m_ValidCondition` 设为 `991202`，并确保 `m_PlayerIDs` 数组的第一个元素为 0。理论上，如果第一个元素非 0，会替换 Zero/“被戒指选中的人”，但它在事件招募命令 10029 下只会导致软锁（不过如果你把该 `PlayerMemberSetID` 分配给一个敌人组，则可以正常工作）。
6. 在 `SkillBoardData`（位于 `/Game/Content/Local/DataBase/SkillBoard`）下创建新条目，引用该新 `PlayerID`。`m_CanNotEssential` 决定技能是否可学习，但只有当你也在 `ItemList`（位于 `/Game/Content/Local/DataBase/Item`）中把该技能作为物品添加时，才能获得相应物品。请确保只使用 `m_SupportLockCond1` 的 `16`，以及 `m_SupportLockCond2` 的 `0`。CotC 有时会使用不同的锁定条件，但它们在 OT0 中似乎不可用，而且除了 `16` 以外的锁定条件也都没有本地化文本。`m_SupportLockParam1` 决定解锁指定支援技能所需学习的战斗技能数量。
7. （可选）修改 `SkillID` 与 `SkillAvailID` 来添加自定义技能与 avail，参见 [如何添加自定义技能与 avail](customskls_zh.md)。
8. 添加自定义必杀技（special skills）：在 `SpecialSkillID`（位于 `/Game/Content/Local/DataBase/Skill/`）下新增条目；然后在 `CharaPlayerSpecialSkillList`（位于 `/Game/Content/Local/DataBase/Character/`）下新增条目，将该角色关联到新的 special skill ID。接着，为必杀技的每个等级在 `SpecialSkillGrowthList` 中新增条目，把该 special skill ID 关联到某个技能。CotC 角色的 special skill 最大等级经常远高于 3，但我不确定是否能让游戏加载这些等级。另外，我也不确定在不同等级指定相同技能时，游戏是否会加载更高 boost 等级版本的 avails/skills。
9. 解包（unpak）`RecruitEvent_p.pak`（参见 [如何从本 MOD 和/或 OT0 提取资产](Begin_zh.md)）。
10. 编辑 `/Game/Content/Local/DataBase/Event/VPartychat/EvPChatOth_01`，新增一个命令 10029 条目，并在 `m_OptAry[0]` 下引用该新 `PlayerMemberSetID`。`m_OptAry[5]` 中的 1 似乎不是必须的。另见：[如何添加自定义任务与事件](customqsts_zh.md)。
