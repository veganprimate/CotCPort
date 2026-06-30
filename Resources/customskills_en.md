# How to add or modify skills and related systems

**OT0 C-contract field-runtime edition.** This guide keeps the reference-atlas style while making field meanings auditable: schema-only claims are separated from runtime-proven behavior, and major fields include caller/dataflow, paired-field, and authoring-consequence notes. OT0 C-export contracts are used wherever the native reader is clear enough to support a field-level authoring rule.

This is a standalone OT0-first guide for custom skill authoring. It preserves the fuller reference-atlas style of the original guide: broad enum comparisons, row-by-row field notes, concrete runtime warnings, and practical authoring rules. The field reference is written as a runtime atlas: each important field is described by what native code or exported data actually does with it, not only by its name. C-export evidence is kept in short collapsible C++ callouts so the guide remains readable instead of becoming a decompiler listing.

This page is an OT0-first reverse-engineering guide for the `Skill/` folder and every table that directly feeds it. Wherever possible, it distinguishes between things that are **confirmed from the OT0 database and SDK**, things that are **confirmed from CotC schema only**, things that are still **observed but not fully named by the SDK dump**, things which have been **proven in-game**, and things that have been **proven via reverse-engineering of the binary**.

- **Proven (in-game/RE)** means the relationship or feature has been proven to have the stated meaning via in-game testing or reverse-engineering of the binary respectively. **(in-game+RE)** will be used when the relationship or feature has been proven in both lanes.
- **Confirmed** means the relationship is backed by the exported row layout, the OT0 SDK struct or enum names, and/or exact ID overlap against the target table.
- **Observed** means the field meaning is strongly supported by row usage patterns and names, but OT0 does not expose a perfectly named enum or caller in the SDK dump.
- **Unresolved** means the field is real and probably important in some content, but OT0 does not provide enough direct evidence to name the behavior with confidence and I have not tested it in-game or not seen any changes.

## OT0 versus the mobile-game `Skill/` folder at a glance

| Pattern | OT0 | Mobile game |
| --- | --- | --- |
| Core row counts | OT0 v1.0.8 export totals: `SkillID 7336`, `SkillAvailID 7369`, `SkillEffectiveID 4381`, `SkillAilmentType 490` | comparison mobile export totals: `SkillID 23085`, `SkillAvailID 28023`, `SkillEffectiveID 11434`, `SkillAilmentType 968` |
| OT0-only tables under `Skill/` | `WeaponType`, `SkillCalcType`, `SkillAvailMagnificationList`, `SkillSupplyItemGroup`, `SkillSupplyItemList`, `SkillTargetShopList`, `SkillTargetFarmItemList` | not present in the mobile export |
| Mobile-only tables under `Skill/` | not present in stock OT0 | `ClassSkillConditionList`, `SkillEffectiveLinkageList`, `SkillFilterCategory`, `SkillFilterType`, `SkillModifyType`, `SpecialSkillExtendCategory`, `SpecialSkillExtendType`, `SpecialSkillExtendList` |
| `SkillEffectiveID` presentation schema | Keeps inline `m_Cameras` and `m_CameraTimes` arrays | Drops those inline camera arrays and adds linkage, UI-visibility, camera-shake, and magic-stone fields |
| `SkillVisitorList` binding | Stores direct `m_EnemyID` | Stores `m_EnemyType` instead |
| `SpecialSkillGrowthList` | Compact OT0 growth row with `m_DisplayPriority` | Much richer growth row with gauge, reward, avail, ailment, and damage-cap columns |
| `SkillID` focus | Keeps field-use flags, direct voice hooks, and OT0 weapon-replacement helpers | Adds filter/category routing, boost-display variants, and auto-battle-facing controls |
| `SkillAvailID` focus | Many dormant shared fields, plus OT0 village/reinforcement/weakness helpers | Activates tag-count, damage-limit, additional-weakness, dodge-ignore, and resist-manipulation channels |

- OT0 `Skill/` is structurally closer to the single-player game's battle flow, front/back-row mechanics, and village-management systems.
- The mobile-game `Skill/` export is structurally closer to a live-service combat stack: extra filter UI, extra progression layers, extra damage-cap logic, extra weakness systems, and more metadata for automated presentation/control paths.
- Do **not** assume that a table being absent from one title means the enum vanished from code. In several places the runtime enum still exists, but only one title exports a data table for it.
- CotC Global 4.2.3 exposes the major skill row surfaces this guide relies on, including `SkillIDBase`, `SkillAvailIDBase`, `SkillEffectiveIDBase`, `SkillEffectiveLinkageListBase`, `SkillAilmentTypeBase`, `SkillConditionListBase`, `SkillAvailMagnificationConditionListBase`, `SkillVisitorListBase`, `SpecialSkillGrowthListBase`, and the CotC-only filter/extension helper rows. It also confirms that several CotC Global 3.22.10 extraction gaps are not true schema absences: 4.2.3 includes `SkillEffectiveIDBase.m_SkillEffectiveLinkageID`, a reflected `SkillEffectiveLinkageListBase`, and the wider `SkillAvailIDBase` late tail. Treat function addresses, stripped helper names, and enum-tail limits as build-local evidence rather than portable authoring names.

## OT0 vs CotC enum comparison quick sheets

These are the high-value OT0-versus-CotC enum comparisons that most directly affect skill modding. The tables below were originally anchored on CotC Global 3.22.10. Where the CotC 4.2.3 header has already been rechecked, the 4.2.3 delta is called out immediately under the relevant enum instead of silently replacing the older column.

### `ESKILL_CATEGORY`

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `COMMAND` | `COMMAND` |
| `2` | `SUPPORT` | `SUPPORT` |
| `3` | `WEAPON` | `WEAPON` |
| `4` | `ENEMYACTION` | `ENEMYACTION` |
| `5` | `ENEMYPASSIVE` | `ENEMYPASSIVE` |
| `6` | `ENEMYEVENT` | `ENEMYEVENT` |
| `7` | `MAXBOOST_COMMAND` | `MAXBOOST_COMMAND` |
| `8` | `ITEM` | `SHIELD_ABILITY` |
| `9` | `ESKILL_MAX` | `ESKILL_MAX` |

- Values `0..7` and the max sentinel still line up cleanly.
- The important drift is value `8`: OT0 uses `ITEM`, while CotC Global 3.22.10 uses `SHIELD_ABILITY`.

### `ESKILL_CALC_TYPE`

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `WEAPON` | `WEAPON` |
| `2` | `MAGIC` | `MAGIC` |
| `3` | `ADD_RATED` | `ADD_RATED` |
| `4` | `ADD_FIXED` | `ADD_FIXED` |
| `5` | `ADD_NOW_RATED` | `ADD_NOW_RATED` |
| `6` | `LAST_1` | `LAST` |
| `7` | `FEAR_LAST_1` | `FEAR_LAST` |
| `8` | `BP_SPECIAL_REGEN_RATE` | `BP_SPECIAL_REGEN_RATE` |
| `9` | `MAGIC_REFLECTION` | `HP_RECOVER_OVER_FIXED` |
| `10` | `ESKILL_CALC_MAX` | `ESKILL_CALC_MAX` |

- This enum is mostly aligned, but OT0-only wording such as `LAST_1` / `FEAR_LAST_1` is already normalized in CotC Global 3.22.10.
- The dangerous mismatch is value `9`: OT0 `MAGIC_REFLECTION` is **not** CotC Global 3.22.10 `HP_RECOVER_OVER_FIXED`.

### `ESKILL_RECOMEND_CATEGORY`

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `ATTACK` | `ATTACK` |
| `2` | `SUPPORT` | `SUPPORT` |
| `3` | `HEAL` | `HEAL` |
| `4` | `NUM` | `NUM` |
| `5` | `ESKILL_RECOMEND_MAX` | `ESKILL_RECOMEND_MAX` |

- `ESKILL_RECOMEND_CATEGORY` is stable between OT0 and CotC Global 3.22.10.

### `ESKILL_AFTER_ACTION`

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `CHANGE` | `CHANGE` |
| `2` | `ESKILL_AFTER_MAX` | `CHANGE_WITH_EFFECT` |
| `3` | — | `SELF_DESTRUCTION` |
| `4` | — | `ESKILL_AFTER_MAX` |

- OT0 only proves values `0` and `1` in practice.
- CotC Global 3.22.10 makes the family more explicit: there is a real `CHANGE_WITH_EFFECT` branch and a real `SELF_DESTRUCTION` branch before the max sentinel.

### `ESKILL_INVOKE` high-impact delta

- OT0 and CotC Global 3.22.10 still align on values `0..27`, `50`, `51`, `100`, and `101`.
- The important drift starts at the late combat-timing block:

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `28` | `JUST_DYING` | `ACTION_ATTACK_RECEIVE` |
| `29` | `ANY_CHARA_ACTION_LAST` | `SLIP_DAMAGE_EACH_ACTION` |
| `30` | — | `ACTION_DAMAGE_CRITICAL` |
| `31` | — | `MOVED_FWD` |
| `32` | — | `MOVED_BAK` |
| `33` | — | `IGNITION_TURN` |
| `34` | — | `JOB_CHECK` |
| `35` | — | `DEBUFF_LIMIT_CALCULATING` |
| `36` | — | `ON_DEAD` |
| `37` | — | `DAMAGE_LIMIT_CALCULATING` in CotC 4.2.3 |

- Do not reuse late timing values from CotC Global 3.22.10 as if they were OT0 timing constants.
- CotC 4.2.3 keeps the same late drift and adds `DAMAGE_LIMIT_CALCULATING = 37` before `FIELD_ACTION = 50` (`libUE4.so.h`:18041-18072).

### `ESKILL_TARGET_TYPE` high-impact delta

- OT0 and CotC Global 3.22.10 share most of the core range through `29` plus most of the status-selector band `150..214`.
- The important drifts are:

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `1` | `ENEMY_1` | `ENEMY` |
| `6` | `FRIENDLY_1` | `FRIENDLY` |
| `30` | `FRIENDLY_1_EVERYONE` | `FORWARDS_WITHOUTME` |
| `31` | — | `FRIENDLY_1_WITHOUTME_AB` |
| `215` | `FRIENDLY_ALL_NOALIVECHECK` | `JOB_ALL_WARRIOR` |
| `216` | `FRIENDLY_MIN_HP` | `JOB_ALL_APOTHECARY` |
| `217` | `ENEMY_BACKUPS_RND` | `JOB_ALL_THIEF` |
| `218` | `FORWARDS_RND_SIDE_BY_SIDE` | `JOB_ALL_SCHOLAR` |
| `219` | `BACKUPS_RND_SIDE_BY_SIDE` | `JOB_ALL_CLERIC` |
| `220` | `PAIR_RND` | `JOB_ALL_HUNTER` |
| `221` | `PAIR_RND_SIDE_BY_SIDE` | `JOB_ALL_MERCHANT` |
| `222` | `ENEMY_HP_MAX_ORDER` | `JOB_ALL_DANCER` |
| `223` | `ENEMY_FORWARDS_HP_MAX_ORDER` | — |
| `224` | `FORWARDS_OR_BACKUPS` | — |
| `225` | `FRIENDLY_1_WITHOUTME_ALL` | — |
| `226` | `FORWARDS_OR_BACKUPS_ALIVE` | — |
| `227` | `CONTINUE_NOSELF_OR_ENEMY1` | — |
| `228` | `CONTINUE_NOSELF_FRIEND` | — |
| `229` | `LAST_RECOVER` | — |
| `230` | `ESKILL_TARGET_MAX` | `MOVED_PLAYER` |
| `231` | — | `AFTERSKILL_ENFORCER` |
| `232` | — | `ENEMY_PAIR` in CotC 4.2.3 |
| `233` | — | `ESKILL_TARGET_MAX` in CotC 4.2.3 |

- This is not just a renamed enum. The tail end is materially different, so target-type integers should not be copied across titles without remapping. Although labels like `ENEMY` and `ENEMY_1` likely refer to the same target type, OT0's is just more explicit and specifies that one selected enemy is being targeted.
- CotC 4.2.3 shifts the current max again: `ENEMY_PAIR = 232`, `MOVED_PLAYER = 230`, `AFTERSKILL_ENFORCER = 231`, and `ESKILL_TARGET_MAX = 233` (`libUE4.so.h`:18221-18335). Treat the older 3.22.10 max-sentinel row as version-specific.

### `ESKILL_MODIFY_TYPE` early divergence sheet

- Only values `0..3` still match by both number and meaning between OT0 and CotC Global 3.22.10.
- Divergence starts immediately at value `4`, and CotC Global 3.22.10 keeps a much smaller `0..97` combat-centric range while OT0 later jumps into the result, economy, village, ranch, and training helper space.
- CotC 4.2.3 widens this family again through `ESKILL_MODIFY_MAX = 106`, adding lanes such as `ACT_ENEMY_COUNT`, command/cast/receive add-turn variants, attack-and-skill variants, `DAMAGE_LIMIT_UP`, and `ADDITION_JOB_IN_BATTLE` (`libUE4.so.h`:18076-18182). The early-drift lesson still stands, but the 3.22.10 column below is not a current 4.2.3 tail map.

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `CHARACTER` | `CHARACTER` |
| `2` | `STEAL_CURE` | `STEAL_CURE` |
| `3` | `FORCE_CHANGE` | `FORCE_CHANGE` |
| `4` | `WEAK_CHANGE` | `IMMEDIATELY_CHANGE` |
| `5` | `ESCAPE` | `WEAK_CHANGE` |
| `6` | `BOOST_LEVEL` | `ESCAPE` |
| `7` | `REVIVE` | `BOOST_LEVEL` |
| `8` | `CALL_VISITOR` | `REVIVE` |
| `9` | `SHORTEN_TURN_MINUS` | `CALL_VISITOR` |
| `10` | `SHORTEN_TURN_DEBUFF` | `SHORTEN_TURN_MINUS` |
| `11` | `SHORTEN_TURN_AILMENT` | `SHORTEN_TURN_DEBUFF` |
| `12` | `GUARD` | `SHORTEN_TURN_AILMENT` |
| `13` | `RERAISE` | `GUARD` |
| `14` | `COUNT_RECOVER_ONE` | `RERAISE` |
| `15` | `COUNT_RECOVER_ALL` | `COUNT_RECOVER_ONE` |
| `16` | `COUNT_RECOVER_SP_SKILL` | `COUNT_RECOVER_ALL` |
| `17` | `DECREASE_AILMENT_COUNT` | `COUNT_RECOVER_SP_SKILL` |
| `18` | `MAGIC_STONE_GAUGE` | `DECREASE_AILMENT_COUNT` |
| `19` | `INTENSIVE` | `MAGIC_STONE_GAUGE` |
| `20` | `GUEST_CALLABLE_COUNT` | `INTENSIVE` |
| `21` | `SHIELD_ONLY` | `GUEST_CALLABLE_COUNT` |
| `22` | `STEAL_ITEM` | `SHIELD_ONLY` |
| `23` | `STEAL_LEAF` | `REPEAT_CONSUME` |
| `24` | `DETECT_HP` | `POSITION_SHUFFLE` |
| `25` | `STEAL_JP` | `STATUS_COPY` |
| `26` | `PASS_SP` | `AILMENT_COPY` |
| `27` | `BUFF_EAT` | `COMMAND_PHASE_TURN` |
| `28` | `SUCTION_LEAF` | `COUNT_RECOVER_EX_SKILL` |
| `29` | `DETECT_ITEM` | `SHORTEN_PET_TURN` |
| `30` | `SP_BURST` | `RESET_INTENSIVE` |
| `31` | `BUFF_COPY` | `COUNT_RECOVER_GOD_BEAST` |
| `32` | `ADD_TURN_PLUS_AILMENT` | `RESULT_EXP` |
| `33` | `ADD_TURN_MINUS_AILMENT` | `RESULT_MONEY` |
| `34` | `MAKE_LEAVE_FROM_BATTLE` | `RESULT_ITEM` |
| `35` | `PASS_SSP` | `AILMENT_RATE` |
| `36` | `REINFORCEMENT_PARTS` | `STEAL_CURE_RATE` |

- Practical rule: do **not** port raw `m_ModifyType` numbers between OT0 and CotC Global 3.22.10. Map by semantics, not by integer.

### `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID` tail divergence

- OT0 and CotC Global 3.22.10 still agree through value `41`.
- Starting at `42`, CotC Global 3.22.10 both repurposes OT0 slots and adds a large new tail:

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `42` | `PROGRESS` | `REPEAT_CONSUME_COUNT_GE` |
| `43` | `SP_BURST_GE` | `REPEAT_CONSUME_COUNT_LE` |
| `44` | `COUNT_DAMAGE_SELF_GE` | `AILMENT_TAG` |
| `45` | `COUNT_DAMAGE_SELF_LE` | `NoAILMENT_TAG` |
| `46` | `TARGET_FRIEND` | `AILMENT_TAG_SELF` |
| `47` | `TARGET_ENEMY` | `NoAILMENT_TAG_SELF` |
| `48` | `P_DEF_SELF_GE` | `CMD_BP_SELF_GE` |
| `49` | `P_DEF_SELF_LE` | `CMD_BP_SELF_LE` |
| `50` | `ESKILL_AVAIL_MAGNIFICATION_CONDITION_MAX` | `TSP_SELF_FIXED_LE` |
| `51` | — | `TSP_SELF_FIXED_GE` |
| `52` | — | `TSP_TARGET_FIXED_LE` |
| `53` | — | `TSP_TARGET_FIXED_GE` |
| `54` | — | `PLAYER_ID_SELF` |
| `55` | — | `NoPLAYER_ID_SELF` |
| `56` | — | `ALIVE_ENEMY_LE` |
| `57` | — | `ALIVE_ENEMY_GE` |
| `58` | — | `FORWARD_SELF` |
| `59` | — | `BACKUP_SELF` |
| `60` | — | `NoFORWARD_SELF` |
| `61` | — | `NoBACKUP_SELF` |
| `62` | — | `JOB_SELF` |
| `63` | — | `NoJOB_SELF` |
| `64` | — | `JOB_BUDDY` |
| `65` | — | `NoJOB_BUDDY` |
| `66` | — | `OPEN_WEAK_TARGET_GE` |
| `67` | — | `OPEN_WEAK_TARGET_LE` |
| `68` | — | `OPEN_WEAK_ANY_ENEMY_GE` |
| `69` | — | `OPEN_WEAK_ANY_ENEMY_LE` |
| `70` | — | `NoOPEN_WEAK_ANY_ENEMY_GE` |
| `71` | — | `NoOPEN_WEAK_ANY_ENEMY_LE` |
| `72` | — | `ALIVE_TARGETABLE_ENEMY_LE` |
| `73` | — | `ALIVE_TARGETABLE_ENEMY_GE` |
| `74` | — | `WEAK_ENEMY` |
| `75` | — | `NoWEAK_ENEMY` |
| `76` | — | `WEAK_DAMAGE_ENEMY` |
| `77` | — | `NoWEAK_DAMAGE_ENEMY` |
| `78` | — | `AILMENT_TURN_SELF_GE` |
| `79` | — | `AILMENT_TURN_SELF_LE` |
| `80` | — | `AILMENT_TURN_TARGET_GE` |
| `81` | — | `AILMENT_TURN_TARGET_LE` |

- This is another enum family where OT0 tail values should **not** be interpreted through CotC Global 3.22.10 names.
- CotC 4.2.3 extends this enum far beyond the old `81` tail, through `AILMENT_TAG_PC_TOTAL_LE = 114` and `ESKILL_AVAIL_MAGNIFICATION_CONDITION_MAX = 115` (`libUE4.so.h`:17837-17951). Do not treat the table above as the full current CotC tail.

### `ESKILLEFFECTIVE_CHARA_VISIBILITY`

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `VISIBLE` | `VISIBLE` |
| `2` | `INVISIBLE` | `INVISIBLE` |
| `10` | `FADE_VISIBLE` | `FADE_VISIBLE` |
| `11` | `FADE_INVISIBLE` | `FADE_INVISIBLE` |
| `12` | `ESKILLEFFECTIVE_CHARA_MAX` | `ESKILLEFFECTIVE_CHARA_MAX` |

- This visibility-operation enum is stable between OT0 and CotC Global 3.22.10.

### `ESKILLEFFECTIVE_VISIBILITY_TARGET`

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `SELF` | `SELF` |
| `2` | `TARGET` | `TARGET` |
| `3` | `GUEST` | `GUEST` |
| `4` | `FRIENDLY_1` | `FRIENDLY` |
| `10` | `FRIENDLY_ALL` | `FRIENDLY_ALL` |
| `11` | `FRIENDLY_ALL_WITHOUT_SELF` | `FRIENDLY_ALL_WITHOUT_SELF` |
| `12` | `FRIENDLY_ALL_GUEST` | `FRIENDLY_ALL_GUEST` |
| `13` | `FRIENDLY_ALL_GUEST_WO_SELF` | `FRIENDLY_ALL_GUEST_WO_SELF` |
| `20` | `ENEMY_ALL` | `ENEMY_ALL` |
| `21` | `ENEMY_ALL_WITHOUT_TARGET` | `ENEMY_ALL_WITHOUT_TARGET` |
| `30` | `FORWARDS` | `FORWARDS` |
| `31` | `FORWARDS_WO_SELF` | `FORWARDS_WO_SELF` |
| `32` | `BACKUPS` | `BACKUPS` |
| `33` | `FORWARDS_AND_BACKUPS` | `FORWARDS_AND_BACKUPS` |
| `34` | `FORWARDS_AND_BACKUPS_WO_SELF` | `FORWARDS_AND_BACKUPS_WO_SELF` |
| `35` | `ESKILLEFFECTIVE_VISIBILITY_MAX` | — |
| `100` | — | `AFTERSKILL_ENFORCER` |
| `101` | — | `ESKILLEFFECTIVE_VISIBILITY_MAX` |

- CotC Global 3.22.10 adds a real `AFTERSKILL_ENFORCER` target and shifts the max sentinel accordingly.

### `ESKILL_AILMENT_CALC_TYPE`

| Value | OT0 | CotC Global 3.22.10 |
| --- | --- | --- |
| `0` | `NONE` | `NONE` |
| `1` | `CALC_DAMAGE` | `CALC_DAMAGE` |
| `2` | `CALC_CRITICAL_DAMAGE` | `CALC_CRITICAL_DAMAGE` |
| `3` | `CALC_WEAK_DAMAGE` | `CALC_WEAK_DAMAGE` |
| `4` | `CALC_BREAK_DAMAGE` | `CALC_BREAK_DAMAGE` |
| `5` | `CALC_ATRIBUTE_BUFF_DAMAGE` | `CALC_ATRIBUTE_BUFF_DAMAGE` |
| `6` | `CALC_ATRIBUTE_DEBUFF_DAMAGE` | `CALC_ATRIBUTE_DEBUFF_DAMAGE` |
| `7` | `CALC_ALLY_DAMAGE` | `CALC_ALLY_DAMAGE` |
| `8` | `CALC_RACE_BUFF_DAMAGE` | `CALC_RACE_BUFF_DAMAGE` |
| `9` | `CALC_BUFF_LIMIT_STATUS` | `CALC_BUFF_LIMIT_STATUS` |
| `10` | `CALC_BUFF_LIMIT_STATUS_SUP` | `CALC_BUFF_LIMIT_STATUS_SUP` |
| `11` | `ESKILL_AILMENT_CALC_MAX` | `CALC_CRITICAL_DAMAGERATE` |
| `12` | — | `CALC_BUFF_LIMIT_ATK` |
| `13` | — | `CALC_BUFF_LIMIT_ATK_SUP` |
| `14` | — | `CALC_SPECIFIC_SHIELD_DAMAGE` |
| `15` | — | `CALC_WEAK_DAMAGE_RATE_PLUS` |
| `16` | — | `CALC_SLIP_DAMAGE_UP` |
| `17` | — | `ESKILL_AILMENT_CALC_MAX` |

- OT0 and CotC Global 3.22.10 agree on `0..10`, but CotC Global 3.22.10 extends the tail significantly.
- CotC 4.2.3 extends the same family further again, through `CALC_MULTI_ATTR_SP_COST_REDUCE = 27` and `ESKILL_AILMENT_CALC_MAX = 28` (`libUE4.so.h`:17797-17826). The `11..16` rows below remain valid 3.22.10-backed names, but they are no longer the complete current CotC tail.
- For CotC Global 3.22.10, the official enum names `0..16` are SDK-confirmed. Runtime evidence confirms that values `1..8` are live lanes rather than dead enum tail entries.
- Safe guide wording for the `6..8` family: these are live damage-modifier lanes whose strongest labels are attribute-debuff/reduction damage, ally-side damage, and race-targeted damage. Exact helper names and raw addresses in stripped mobile exports are build-local audit evidence, not authoring semantics. Use the enum labels and the recovered dataflow, not function addresses, when documenting or porting them.

### CotC Global 3.22.10-only or sparse companion enums worth noting

#### `ESKILL_FILTER_TYPE`

| Value | CotC Global 3.22.10 |
| --- | --- |
| `0` | `SKILL_FILTER_TYPE_NONE` |
| `1` | `SKILL_FILTER_TYPE_HP_RECOVER` |
| `2` | `SKILL_FILTER_TYPE_SP_RECOVER` |
| `3` | `SKILL_FILTER_TYPE_BP_RECOVER` |
| `4` | `SKILL_FILTER_TYPE_HP_BARRIER` |
| `5` | `SKILL_FILTER_TYPE_SP_STOCK` |
| `6` | `SKILL_FILTER_TYPE_REMOVE_ABNORMAL_STATUS` |
| `7` | `SKILL_FILTER_TYPE_REMOVE_DEBUFF` |
| `8` | `SKILL_FILTER_TYPE_DAMAGE_REDUCE` |
| `9` | `SKILL_FILTER_TYPE_ROBUST` |
| `10` | `SKILL_FILTER_TYPE_PENETRATION_SHIELD_DAMMAGE` |
| `11` | `SKILL_FILTER_TYPE_BUFF_CRT_ALWAYS` |
| `12` | `SKILL_FILTER_TYPE_SPECIAL_SKILL_GAUGE_UP` |
| `13` | `SKILL_FILTER_TYPE_MAX` |

#### `ESSKILL_EXTEND_CATEGORY`

| Value | CotC Global 3.22.10 |
| --- | --- |
| `0` | `SSKILL_EXTEND_CATEGORY_NONE` |
| `1` | `SSKILL_EXTEND_CATEGORY_USE_ADD` |
| `2` | `SSKILL_EXTEND_CATEGORY_GAUGE_UP` |
| `3` | `SSKILL_EXTEND_CATEGORY_DAMAGE_UP` |
| `4` | `SSKILL_EXTEND_CATEGORY_BACK_ACTIVATE` |
| `5` | `SSKILL_EXTEND_CATEGORY_GAUGE_UP_BY_BP` |
| `6` | `SSKILL_EXTEND_CATEGORY_MAX` |

#### Sparse or empty CotC Global 3.22.10 companion enums

- `ECLASS_SKILL_CONDITION_ID`: empty in the checked CotC Global 3.22.10 dump.
- `ESKILL_VISITOR_ID`: empty in the checked CotC Global 3.22.10 dump.
- `ESKILL_HIT_RATE_ID`: only `Default = 0` plus the max sentinel in the checked CotC Global 3.22.10 dump.


## Runtime model

For almost every active skill in OT0, the runtime chain is:

`SkillID` -> `SkillAvailID` + `SkillEffectiveID`

- `SkillID` is the root row: name/detail text, category, cost, icon, boost family, and the arrays of gameplay and presentation stages.
- `SkillAvailID` is the gameplay payload: target selection, damage/heal/buff/debuff/revive/weakness change/reinforcement/summon logic, durations, counts, and secondary mechanics.
- `SkillEffectiveID` is the presentation payload: battle actions, cameras, effects, sounds, voices, floating text, and visibility changes. Every avail gets assigned one ID from `SkillEffectiveID`

Ailments are a parallel subsystem rather than a fourth mandatory stage. When a skill applies a buff, debuff, status ailment, counter, intercept, reraise, damage cap aura, or similar state, the usual path is that `SkillAvailID` names one or more `SkillAilmentType` rows.

For OT0 ailments, the row lookup chain is concrete and ID-based:

`SkillID` -> `SkillAvailID.m_AddAilment[index]` -> numeric `SkillAilmentType.m_id` -> active `FAilmentInfo` entry on the battler

- `SkillAilmentType` is the master definition table. It is loaded into `ADatabaseDefineStatic::m_SkillAilmentType`, and `USkillDB::GetSkillAilmentDB(AilmentID)` resolves the numeric ID through the table access helper before returning the row.
- The active battle state is not stored in `SkillAilmentType`; it is stored per character in `AQPBattleCharacterBase.m_AilmentInfo`, an array of `FAilmentInfo` records. Runtime functions such as `FindAilment`, `SetupAilment`, `SetAilmentTurn`, `UpdateAilmentTurn`, `ClearAilment`, and `SubAilment` operate on that live array.
- `m_Label` is not the behavior key. It is an authoring/editor label attached to the row. Native lookup and many special cases compare the numeric `m_id`, so adding a new row with the same label as an existing ailment does not inherit that ailment's hardcoded behavior.

The runtime formula claims below are backed by the PDB-named OT0 v1.0.8 decomp and by direct C-export call paths. The guide quotes only short evidence excerpts where the exact reader behavior is surprising or easy to mis-author.

## Common misconceptions

- **Proven:** `SkillAilmentType.m_Label` is not an ailment behavior key. OT0 initializes the ailment DB access table from `m_id`, `USkillDB::GetSkillAilmentDB` resolves rows by numeric ID, and live `FAilmentInfo` records store the numeric ailment type. Duplicating an existing label on a new row only duplicates the human-readable row name; it does not enter poison, sleep, paralysis, countdown, reflect, skip-action, or other hardcoded ID branches.
- **Confirmed:** a stock-data-zero field is not automatically runtime-unused. `SkillAvailID.m_ShieldDamage` is zero in all checked stock OT0 rows, but native damage code still calls `USkillDB::GetSkillFixedShieldDamage`, whose helper returns `pAvail->m_ShieldDamage` when the avail row exists. Treat it as stock-data-unused unless you have tested a nonzero authoring case.
- **Proven:** `SkillAvailID.m_ResistID` is **not** `Skill/SkillResistList.m_id`. In OT0 the nonzero values match `Enemy/EnemyWeakID.m_id` exactly, and that target row shape is the one that actually makes sense for weakness-table replacement. Concrete proof: `SkillAvailID#40085` and `SkillAvailID#73697` both use `m_ResistID = 6527`; `EnemyWeakID#6527` exists, while `SkillResistList#6527` does not.
- **Confirmed:** `SkillAvailID.m_SkillAvailMagnificationCondition` and `m_SkillAvailMagnificationConditionArray` do behave like row IDs into `SkillAvailMagnificationConditionList`.
- **Confirmed:** OT0 `SkillAvailID.m_SkillAvailMagnification` and `m_SkillAvailMagnificationArray` do **not** behave like `SkillAvailMagnificationList.m_id`. OT0 uses direct values such as `110`, `130`, `150`, `180`, `200`, and array values up to `300`. Concrete examples: `SkillAvailID#74940` from `Surprise Attack` uses direct values `110/130/150/170/200`, and `SkillAvailID#76297` from `Ulti-meow` climbs as high as `300`.
- **Confirmed:** `SkillID.m_SkillIconID` points directly into `Texture/TextureID`, not into `Skill/SkillIconList`. `SkillIconList` is still real, but it is a **separate** paired-icon bank.

## Runtime formula and caller notes

The formula details below are the skill-system facts most likely to affect custom authoring:

- `USkillDB::GetAvailValue` is the raw boost-lane reader: it reads `m_Values[BoostLevel]`, optionally applies inclusive integer fluctuation from the paired `m_FluctuationValues[BoostLevel]`, and returns that raw payload. It does **not** apply enhancement, skill-available magnification, or battle-state scaling by itself.
- `USkillDB::CalcAvailValue` is the battle-aware wrapper. Outside battle it returns `GetAvailValue`; during battle it passes that raw value through `AQPBattleManager::CalcAvailBuff`, where eligible enhancement and conditional magnification are applied. `m_Values` is therefore a payload input, not always the final battle number.
- `CalcAvailBuff` first applies enhanced-rate logic only for eligible calc/modify families, then enters the magnification block only when scalar `m_SkillAvailMagnificationCondition > 0`. The scalar and array magnifications are direct percentages, and the maximum satisfied magnification wins.
- Weapon, magic, and magic-reflection avails enter the ordinary hit/miss/critical/damage branch. `m_PhysicalHitRatio` is a late additive physical-hit term; `m_CriticalRatio` is a critical-chance additive term, not the entire final chance.
- Negative fixed damage routes through `CalcFixedDamage` when live battle characters exist. Breaking skips the resist/weak/shield sub-branch, but it does not skip the avail-tag damage multiplier. Non-breaking fixed damage can still deal shield damage through `m_ShieldDamage` or the weak/force-weak fallback.
- SP cost is not a single flat percentage. OT0 applies fixed/rated SP-cost increases first, then rated reductions, then fixed reductions. Reduction percentages use the strongest negative value per source bucket, while fixed reductions are summed.
- `m_AddAilmentPick` and `m_SubAilmentPick` are real runtime selectors. The pick helper compacts zero gaps and treats pick `0` as unlimited/no truncation.
- `SkillAvailID.m_CountRef*` is stock-data-zero in OT0 but runtime-supported. The active ailment record can store a value equal to `m_CountRefValue * referenced_count`, where the referenced count is assembled from normal, support, and special active-ailment buckets.

## Less-obvious skill-runtime surfaces checked from the C export

The most obvious skill path is `SkillID -> SkillAvailID -> SkillEffectiveID`, but OT0 also reads skill data from UI, village, sorting, support-passive, telop, command-availability, and delayed-skill helper code. These secondary readers matter because they decide what the player sees, which skills appear selectable, which skills are treated as support/passive, and which non-battle systems a skill can affect.

### Skill detail text is computed from live skill data, not only from static text

`USkillDB::GetCommandHelpText` is a wrapper around `USkillDB::GetSkillDetail`. The latter resolves the boost row first, scans the boosted skill's `m_Avails`, checks whether any avail has `m_DelayedSkill > 0`, then marks the detail as delayed-turn text when the first add-ailment is one of the delayed-skill families:

```text
AddAilment[0] == 43
AddAilment[0] == 44
AddAilment[0] == 289
AddAilment[0] == 290
```

If delayed-turn text is active, the engine runs the text replacement pass both for the displayed skill and for the delayed skill referenced by `SkillAvailID.m_DelayedSkill`. Only after those replacement strings are registered does it load the final `GameTextSkill` detail text. This means the description text path is an additional runtime data consumer: it can expose mismatches between the shown skill, the delayed payload, ailment turn values, and boost-colored values.

The important text-tag readers are not cosmetic guesses; they call the same skill/avail helpers used by battle code:

| Text helper | Runtime meaning |
| --- | --- |
| `SetTagTargetType` | Reads `SkillAvailID.m_TargetType` and formats target-scope tags. |
| `SetTagCalcType` | Reads the avail calculation branch, so a description can change wording based on `m_CalcType`. |
| `SetTagExecCount` | Reads use/count limit data and formats execution-count tags. |
| `SetTagModifyStatus` | Reads `m_ModifyStatus` or ailment target status and resolves status-name tags. |
| `SetTagAilmentValue` | Reads `m_AddAilment[index]`, `m_CalcTypeAilment[index]`, and `m_ValueAilment[index]`; delayed skills use a smaller delayed-tag set. |
| `SetTagAilmentHitRatio` | Reads `m_ResistAilmentID`, then the first populated `SkillResistAilmentID` ailment/rate pair and prints the ailment hit probability. |
| `SetTagAvailValue` | Reads the boost-aware avail value, including the same random/fluctuation and conditional-value machinery used by normal skill value helpers. |
| `SetTagAvailValuePercentage` | Formats avail values as percentage-style tags instead of raw integer tags. |
| `SetTagAvailValueMagnification` | Exposes the conditional magnification value path, not a separate table lookup. |
| `SetTagAvailTurn` | Reads boost-aware turns, invalidation turns, delayed-skill turns, and ailment add/sub lists. |
| `SetTagAvailUseCount` | Reads `m_Counts`, overwrite/count text rules, and boost coloring. |
| `SetTagResistName` / `SetTagResistRate` | Resolve weapon/magic resist names and rates for weakness/resist display text. |
| `SetTagMultiHit` | Reads the hit-rate package ID and formats multi-hit text. |

Two practical consequences follow:

1. Description tags can become wrong when the skill's data is changed without changing the relevant delayed skill, ailment row, or resist package.
2. Some values shown in descriptions are recomputed at formatting time, not simply copied from `m_Values[boost]`. For example, delayed-turn text can come from `m_DelayedSkill`, and ailment-hit text comes from `SkillResistAilmentID`, not directly from `SkillAvailID.m_ResistAilment`.

### Telop/large-skill announcement detection reads `SkillEffectiveID`

`USkillDB::HasTelopSkill` is a small but useful presentation-side checker. It loads the `SkillID` row and scans the skill's `m_Effectives` array. For each positive effective ID it loads `SkillEffectiveID`; the first effective slot is enough to pass, and later slots pass when the effective row has text data. The helper also returns true when the skill has a positive `m_BeginEffective` or positive `m_EndEffective`.

In authoring terms, telop/announcement behavior is not controlled only by the root `SkillID` name/detail. It is inferred from the effective-program layout:

```text
BeginEffective > 0        -> telop-capable
Effectives[0] > 0         -> telop-capable
Effectives[i > 0].Texts   -> telop-capable
EndEffective > 0          -> telop-capable
```

So an otherwise ordinary gameplay skill can be pulled into order-panel/telop behavior by its presentation row choices.

### Conditional skill replacement has two layers

The replacement code has a pre-replacement finder and an actual replacement resolver.

`GetConditionPreReplaceSkill(BaseSkillID, SkillID, Character)` scans the character's equipped skills. It looks for equipped skills whose replacement family points back to the queried skill. If it finds one, the equipped skill becomes the `BaseSkillID`. This is why replacement-aware enhancement and support checks can normalize a visible/replaced command back to the base equipped skill.

`GetConditionReplaceSkill(ReplacedSkillID, SkillData, BoostLevel, Character, pIsDisableTag)` then performs the active replacement:

```text
1. Resolve the boosted skill row for the current BoostLevel.
2. Check scalar m_ReplaceCondition first.
3. If scalar condition passes and m_ReplaceSkill > 0, replace with m_ReplaceSkill.
4. If scalar condition fails, scan m_ReplaceConditionArray.
5. For the first array condition that passes and has a paired positive m_ReplaceSkillArray entry, replace with that paired skill.
6. When a replacement succeeds, copy m_DisableReplaceTag to the output flag.
```

The array path is paired by index. It is not a free list where every condition can choose any replacement. Also, the array path is subordinate to the scalar branch: if the scalar condition passes and scalar replacement exists, the scalar replacement wins first.

### Charge-turn skills are rooted in `SkillID`, not `SkillAvailID`

`USkillDB::IsSkillChargeTurnType(SkillID, Initial, Recharge)` reads two fields on `SkillID`:

```text
m_InitialChargeTrun
m_ReChargeTrun
```

The helper returns true only when `m_ReChargeTrun > 0`; when true, it outputs both the initial charge turn and the recharge turn. This means the charge/recharge identity of a skill is a root-skill property, not an avail-stage property. The avails still define the actual payload, but recharge UI and action-order code can classify the whole skill before any specific avail is executed.

### Order-priority detection is broader than `m_OrderPriority`

`USkillDB::IsOrderPriority(SkillID, isNextTurn, outIsTargetAffect, outIsEnforcerAffect)` proves that action-order influence has three independent triggers:

1. The skill uses the `GUARD` modify type.
2. Any avail has nonzero `m_OrderPriority` or, when checking next-turn behavior, nonzero `m_NextPriority`.
3. In next-turn mode, the skill adds one of the order-affecting ailment IDs:
   - `BUFF_X_AGI`
   - `DEBUFF_X_AGI`
   - `FORCE_ORDER_END`

The output flags are meaningful. The helper marks the enforcer-affecting side for guard/order-priority cases, and separately marks target-affecting behavior for next-turn AGI/order ailments. This explains why some skills can force a turn-order refresh even when the visible payload looks like a normal buff/debuff.

### Support/passive classification uses category plus first-avail behavior

Support skills are not classified by `SkillID.m_Category` alone once the caller asks for add-parameter behavior. The support classifier starts from category `SUPPORT`, then inspects the first avail and its modify/invoke settings.

High-value checks from the native helpers:

| Helper | Native condition |
| --- | --- |
| `IsSupportInfluence` | First avail exists and `m_ModifyType == 110`. |
| `IsSupportAddStatusAll` | First avail exists and `m_ModifyType == 111`. |
| `IsSupportInvokeJust` | Tests support avails for the just-invocation family rather than treating all support rows the same. |
| `IsForceAvailableSupportAbility` | For support skills, an invoke timing of `100` returns a special force-available code; modify types `100`, `101`, `102`, and `119` also force availability. |
| `IsForceSupportAbility(AilmentID)` | Reads `SkillAilmentType.m_ForceSupportAbility`, but rejects rows where `m_ForceCommandAbility` is set. |
| `IsForceCommandAbility(AilmentID)` | Reads `SkillAilmentType.m_ForceCommandAbility`. |

The practical rule is that a support skill can be category-correct but still not behave as an add-parameter support if its first avail is not one of the expected support modify/invoke patterns. Conversely, certain support modify types are force-available even when ordinary command availability would reject the skill.

### Result-rate and steal classification is hardcoded by modify type

Several UI and result systems do not execute the whole skill payload. They use lightweight modify-type classifiers.

`USkillDB::IsResultRateAvail(modifyType)` returns true only for:

```text
RESULT_EXP
RESULT_MONEY
RESULT_ITEM
RESULT_JP
```

`USkillDB::IsStealModifyType(SkillID)` scans the skill for steal-related modify types rather than reading a separate steal flag. Therefore result and steal UI can be correct only if the skill's `m_ModifyType` is in the expected family. Changing a skill to perform a steal-like payload through some other modify branch will not automatically make every result/steal-side UI helper recognize it.

### Skill sorting is a runtime helper, not just table order

`USkillDB::SortSkills(SkillIDs, Order, PlayerID)` supports four order modes:

| Order | Native behavior |
| ---: | --- |
| `1` | Sort raw IDs through the default less-than comparator. |
| `2` | Stable-sort by skill category through `SortComparerCategory`. |
| `3` | Stable-sort by name through `SortComparerName`. |
| `4` | Stable-sort by high priority through `SortComparerHighPriority`, using `UPlayerDB::GetInfoSkillIDs(PlayerID, 0)` and `(PlayerID, 1)` as command/support priority lists. |

This matters when testing custom skills from menus. A row may be valid and usable but appear in an unexpected position because the menu is not using file order; it may use category, name, or player-specific priority lists.

### Item skills, max-boost skills, and preparation skills are root-skill classifiers

Several small helpers classify the entire skill from `SkillID` and its first avail:

- `IsItemSkill(SkillID)` checks `SkillID.m_Category == ITEM` in OT0. Because CotC uses value `8` for `SHIELD_ABILITY`, this classifier is another place where raw category integers do not port safely.
- `IsRequireMaxBoost(SkillData, BoostLevel)` checks whether `SkillID.m_Category == MAXBOOST_COMMAND`, and when a boost level is supplied, it compares it to `AQPBattleManager::StaticGetMaxBoostLevel()`.
- `IsPreparation(SkillData)` recognizes first-avail `m_ModifyType == 120`. Preparation is therefore a modify-family identity, not a generic “has delayed skill” identity.

### Village and economy helper tables have exact C readers

The OT0-only village/economy skill tables are not loose documentation. They are searched by exact helper functions:

| Table | Native reader | Exact key behavior |
| --- | --- | --- |
| `SkillTargetShopList` | `IsSkillTargetShopExists(skillId, shopType, mapId)` | Scans rows and returns true only when all three fields match. |
| `SkillTargetFarmItemList` | `GetAdditionalVillageFarmItemId(skillId, villageFarmItemId, modifyType)` | Scans rows for `(m_SkillId, m_SkillModifyType, m_VillageFarmItemId)` and returns `m_AdditionalVillageFarmItemId`. |
| `SkillSupplyItemGroup` | `GetSupplyItemGroup(skillId, modifyType, buffer)` | Clears the output buffer, then appends every group row whose skill and modify type match. |
| `SkillSupplyItemList` | `GetSupplyItemList(supplyGroupId, buffer)` | Clears the output buffer, then appends every item row whose `m_GroupId` matches the selected group. |

This gives a precise model for OT0 village-supply skills:

```text
SkillID + ESKILL_MODIFY_TYPE
  -> matching SkillSupplyItemGroup rows
  -> matching SkillSupplyItemList rows by group ID
  -> reward type / item ID / count / lot / unique flag
```

And for shop/farm targeting:

```text
SkillID + shop type + map ID -> target-shop existence
SkillID + modify type + farm item ID -> additional farm item ID
```

So these helper assets are gameplay data, not unused metadata. Their keys include the modify type, which means cloning the same skill ID under a different modify type can silently disconnect it from its village reward or target-shop support row.

### Visitor helpers expose the placement/audio side of summon skills

`USkillDB::GetVisitorGuestID(pAvail)` returns the guest visitor ID stored by the avail-side visitor/summon field. Once the visitor ID is known, the visitor helper table is read through separate accessors:

| Helper | Data read |
| --- | --- |
| `GetVisitorDB(VisitorID)` | Loads the visitor row by ID. |
| `GetVisitors(VisitorID, BoostLevel, IDs)` | Reads the visitor/enemy IDs for the current boost level. |
| `GetVisitorOffset(VisitorID, isY)` | Returns the X or Y offset array. |
| `GetVisitorSE(VisitorID, BoostLevel)` | Reads the call sound for the boost level. |

This is why visitor behavior has two parts: the avail picks the visitor package, while `SkillVisitorList` controls the spawned visitor identity, boost-indexed entries, offsets, and call sound.

### Target classifiers are reused by UI, AI, and mixed-target checks

The target-type helpers are called outside the core target picker. Their exact behavior explains some UI and AI decisions:

- `IsEnemyTargetType` first removes AI wrappers through `RemovedTargetTypeAI`, then accepts ordinary enemy targets, enemy random/all-random families, buffed/debuffed enemy selectors, side-by-side enemy selectors, pair selectors, and HP-order selectors. If none of those match, it calls `GetTargetAilment(TargetType)` and treats ailment-target shorthand types as enemy-targeting when they synthesize a positive ailment ID.
- `IsRandomTargetType(TargetType, allOnly)` separates random single-target selectors from random all/ordered selectors. `ENEMY_RND`, `FRIENDLY_RND`, `FRIENDLY_RND_WITHME`, `FORWARDS_RND_SIDE_BY_SIDE`, and `ENEMY_FORWARDS_HP_MAX_ORDER` return true only when `allOnly` is false. `ENEMY_ALL_RND`, `FRIENDLY_ALL_RND`, `FRIENDLY_ALL_RND_WITHME`, `ENEMY_BACKUPS_RND`, `BACKUPS_RND_SIDE_BY_SIDE`, `PAIR_RND`, `PAIR_RND_SIDE_BY_SIDE`, and `ENEMY_HP_MAX_ORDER` are treated as random in both broad and all-only modes.
- `IsDeadTargetType` is true only for `FRIENDLY_1_DEAD`, `FRIENDLY_ALL_DEAD`, `FRIENDLY_EVERYONE_DEAD`, `BACKUPS_DEAD`, and `FRIENDLY_1_ANY`.

These helpers are why target-type integers affect more than target collection. They also decide whether a skill is considered enemy-facing, dead-targeting, random, mixed, or AI-normalized by other systems.

### Ailment category and display sorting are derived from buff/debuff flags

`USkillDB::GetAilmentCategory(AilmentID)` first calls `GetBfDbfFlag`. Positive buff/debuff classification returns category `0`, negative classification returns category `1`. If the row is neither, the helper reads the row's plus/minus bytes directly: a plus row returns category `2`; a minus row returns category `3`; a neutral row returns category `4`.

That gives the runtime category order:

```text
0 = buff/debuff positive family from GetBfDbfFlag
1 = buff/debuff negative family from GetBfDbfFlag
2 = plus non-BfDbf row
3 = minus non-BfDbf row
4 = neutral/other
```

This category is used by ailment sorting and status-detail grouping. It is a derived runtime property; it is not a standalone enum field stored on the row.

### Hit-rate count packages can be ailment-count-based

`USkillDB::IsHitTypeAilmentCountBase(HitTypeID, AilmentType)` loads `SkillHitRateList` and reads its `m_CountAilment` field. When that field is positive, the helper returns true and outputs the ailment type. This confirms that the hit-rate table can switch from ordinary random hit-count behavior into an active-ailment-count-driven hit-count behavior. OT0 stock rows barely use this lane, but the runtime branch exists and CotC is structurally better positioned to use it.

### Append-weak, element, and multi-type classifiers scan all avails

The small helpers used by UI and AI do not always execute a skill; they often scan all avails and classify the skill:

- `IsSkillHasAppendWeak(SkillID)` returns true if any avail has `m_AppendType > 0`.
- `IsSkillHasElement(skillID, status, modifyType, isPositive)` scans avails and checks whether the skill contains the requested status/modify/sign family.
- `IsSkillHasMultipleType(skillID)` scans avails to decide whether more than one relevant weapon/magic/target class is represented.
- `IsSkillHasTag(SkillID, SkillAvailTag)` scans `m_AvailTag` arrays.

This distinction matters when a skill has multiple avails. One avail can make the whole skill appear as append-weak, tagged, elemental, or multi-type even if another avail is the one that actually performs the visible main hit.

### CotC 4.2.3 obscure skill surfaces checked in the header/decomp

CotC Global 4.2.3 adds several helper layers that OT0 does not have. The checked `libUE4.so.h` confirms the following as real reflected row surfaces, not just old spreadsheet columns:

| Surface | Gameplay-facing role |
| --- | --- |
| `ClassSkillConditionListBase.m_UseCondition` / `m_ConditionText` | Separates class/EX-skill use gating from ordinary `SkillConditionList`; the text field gives a dedicated failure/condition explanation lane. |
| `SkillIDBase.m_ForcedSkillFilter` / `m_ExclusionSkillFilter` | Lets a skill force or suppress skill-filter classifications independently of its modify type. |
| `SkillIDBase.m_ForcedCategory` | Lets CotC override category grouping for filter/UI purposes. |
| `SkillIDBase.m_NotUsedAutoBattle` | Exposes an explicit auto-battle exclusion flag. OT0 has no equivalent field in `SkillID`. |
| `SkillIDBase.m_CmdPhaseTurnResetOnContinue` | Adds command-phase turn reset behavior to continue-style skills. |
| `SkillModifyTypeBase.m_SkillFilterType` | Data bridge from modify opcode to one or more filter type rows. CotC can therefore classify skills for UI/filtering without hardcoding every modify type in UI code. |
| `SkillFilterCategoryBase` / `SkillFilterTypeBase` | Filter UI legend and sorting data. `SkillFilterType.m_Category` points back to the category row. |
| `SkillEffectiveLinkageListBase` | Chained presentation package with hold time, avail time, offsets, limit, linkage type, and linked effective IDs. |
| `SkillEffectiveLinkageTypeBase` | Linkage-type legend for the linkage package. |
| `SpecialSkillExtendCategoryBase`, `SpecialSkillExtendTypeBase`, `SpecialSkillExtendListBase` | Special-skill extension UI/effect/reward system absent from stock OT0. |

The header also confirms a useful cross-title caution: several CotC arrays appear in the recovered C header as `unsigned __int8[16]`, but those are decompiler/header artifacts for reflected `TArray` storage, not proof that every authored list has exactly sixteen live entries. Treat the field name and presence as confirmed; treat exact authored length as data-dependent unless the runtime caller bounds the loop.

## Additional C-export skill mechanics that are easy to miss

The following mechanics are not separate schemas. They are native call paths that read existing `Skill/` rows or active battle state in ways that are easy to miss when only looking at `SkillID`, `SkillAvailID`, and `SkillAilmentType` as static tables.

### Ailment-side skill replacement is an active-state permission system

`SkillAilmentType.m_SkillReplace` does not directly replace every skill while the ailment is present. The runtime treats it as a pointer into `SkillAilmentSkillReplaceList`, and that list must agree with the active ailment ID.

The important native chain is:

```text
active FAilmentInfo.m_AilmentType
  -> USkillDB::IsAilmentSkillReplace(AilmentID)
  -> SkillAilmentType.m_SkillReplace
  -> SkillAilmentSkillReplaceList row
  -> row's source ailment must equal AilmentID
  -> row's skill array must contain the queried SkillID
```

`USkillDB::GetAilmentSkillReplaceArray(SkillID)` also proves the authoring direction from the skill side. It scans the queried skill's avails, then the avail's `m_AddAilment` array. The scan stops at the first nonpositive ailment entry. For each positive ailment it loads `SkillAilmentType`; if `m_SkillReplace > 0`, it loads the `SkillAilmentSkillReplaceList` row and returns the replacement skill array only when the row's ailment field matches the same ailment ID.

The active-character gate is stricter still. `AQPBattleCharacterBase::IsAvailableSkill_AilmentSkillReplace(SkillID, rememberCheck)` uses `m_CacheAilmentSkillReplace` as a candidate list:

```text
if m_CacheAilmentSkillReplace is empty:
    skill is not restricted by ailment-skill-replace
elif SkillID is in m_CacheAilmentSkillReplace:
    require an active ailment with m_SkillReplace
    require SkillAilmentSkillReplaceList to contain SkillID
elif rememberCheck is true:
    allow only when no active ailment-skill-replace exists
else:
    allow
```

So ailment-skill-replacement is not just a transform. It can become a temporary availability filter: cached affected skills may be hidden or rejected unless the currently strongest active replacement ailment permits them.

`AQPBattleCharacterBase::GetAilmentSkillReplace()` scans active ailments and selects an active ailment whose `SkillAilmentType.m_SkillReplace` is positive. If more than one replacement ailment is active, it keeps the one with the largest active-record comparison value at offset `+0x1C`. The SDK dump does not name that `FAilmentInfo` member cleanly, so the safest authoring rule is: do not assume multiple simultaneous skill-replacement ailments merge; the runtime selects one active replacement ailment as the current replacement authority.

### Ailment-side sprite replacement is first-positive, not strongest-value

`AQPBattleCharacterBase::GetAilmentSpriteChangeCharaID()` copies the active ailment list and scans it in active-record order. For each ailment it calls `USkillDB::GetReplaceCharaIDFromAilment(AilmentType)`, which reads `SkillAilmentType.m_ReplaceCharaID`. The first positive replacement character ID ends the scan.

That means sprite replacement is not resolved the same way as ailment skill replacement:

```text
skill replacement: choose active replacement ailment by comparison value
sprite replacement: first active ailment with positive m_ReplaceCharaID wins
```

When designing custom transformation states, do not rely on the same priority behavior for both systems.

### Weak-lock state is checked against actual weak slots, not only against the ailment ID

`AQPBattleCharacterBase::IsAllLockedWeaks()` is enemy-only. It scans the enemy's current resist/weak arrays for slots that are presently weak. For each weak slot, it searches the enemy's active ailments for ailment ID `37`, verifies that the ailment's source/overrider is alive, loads the source `SkillAvailID`, reads `m_WeakLockID`, and asks `UEnemyDB::IsLockedWeak(WeakLockID, weaponIndex, magicIndex)`.

The function returns false as soon as it finds a current weak slot that is not covered by the active weak-lock data. In gameplay terms, a weak-lock ailment does not merely mark the enemy as “weak locked”; it must map through the source avail's `m_WeakLockID` to the exact weak slot currently being inspected.

This also explains why `SkillAvailID.m_WeakLockID` belongs to the source avail rather than to `SkillAilmentType`: the same weak-lock ailment ID can use different lock masks depending on the skill that applied it.

### Ailment use-count queries have three source buckets

`AQPBattleCharacterBase::GetAilmentUseCount(AilmentType, SkillID, AvailID, SupportFlag, SpecialFlag)` filters active ailments very narrowly:

```text
same ailment type
source/overrider still alive
turn count <= 0
not the internal blocked/future flag at active offset +0x59
optional SkillID match
optional AvailID match
support flag filter
special-skill flag filter
```

It returns the active record's use-count value, or `-1` when no matching record exists. The support and special filters are tri-state-like in practice: positive requires the flag, negative rejects the flag, and zero does not require it.

`GetAilmentUseCountMerged()` then performs three separate searches for the same ailment family:

```text
normal bucket:  not support, not special
support bucket: support, not special
special bucket: not support, special
```

It sums only positive values from the buckets. If all three buckets are missing or nonpositive, the merged result is `-1`. This is the same bucket model used by count-reference formulas. It is why an ordinary ailment, support-source ailment, and special-source ailment can coexist as separate use-count contributors even when they share an ailment ID.

### Count-reference values are clamped inside `FAilmentInfo::GetAilmentValue`

`FAilmentInfo::GetAilmentValue(CalcType)` first selects the stored lane:

| `CalcType` requested | Stored lane returned before count-ref additions |
| --- | --- |
| `WEAPON` / `MAGIC` | `m_CalcValue` |
| `ADD_RATED` | `m_RatedValue` |
| `ADD_FIXED` | `m_FixedValue` |
| `ADD_NOW_RATED` | `m_NowRatedValue` |

If the active ailment has `m_CountRefInfo`, every count-ref entry whose stored calc type equals the requested calc type contributes to the result. The function adds those count-ref values to the base lane, then checks the source `SkillAvailID.m_CountRefValueLimit` from each count-ref entry's source avail.

The clamp keeps the sign class stable:

```text
positive count-ref value:
    result is capped at m_CountRefValueLimit when the limit is lower
    if the original base lane was positive, final result cannot fall below 1

negative count-ref value:
    result is floored at m_CountRefValueLimit when the limit is higher
    if the original base lane was negative, final result cannot become nonnegative
```

This is more precise than “count ref adds value.” It is an additive lane with a per-source limit and sign-preservation safeguards.

### Slip damage flags are derived from ailment row shape plus the source avail

`USkillDB::GetAilmentSlipDamageFlag(AilmentID, AvailID)` shows a hidden classification path for damage-over-time style ailments. It first loads `SkillAilmentType` and only enters the special logic when the row has:

```text
m_TargetStatus == HP-like status 5
m_InvokeTiming in {12, 17, 22}
```

For neutral rows that are neither plus nor minus, the function finds the ailment's index in the source avail's `m_AddAilment` array. It then reads the paired `m_ValueAilment[index]` and `m_CalcTypeAilment[index]`. If the calc type is weapon/magic-like and the value is negative, it returns the positive magnitude of that negative value. Otherwise it returns the value as stored.

For plus/minus rows, it again reads the paired value, but a positive value on a minus row is returned as negative. There is also a child-ailment fallback: if the ailment is not directly present in `m_AddAilment` and the row's invoke timing matches the child-ailment route, the helper can find a parent row whose `m_ChildAilment` equals the queried ailment and use the parent's avail index.

The practical rule is that slip-damage classification is not only the ailment ID. It is the combination of:

```text
SkillAilmentType.m_TargetStatus
SkillAilmentType.m_InvokeTiming
SkillAilmentType.m_Plus / m_Minus
SkillAvailID.m_AddAilment[index]
SkillAvailID.m_CalcTypeAilment[index]
SkillAvailID.m_ValueAilment[index]
optional parent-child ailment relationship
```

### Ailment detail text and icon readers have special cases

Ailment display is mostly row-driven, but not completely.

`USkillDB::GetAilmentTypeDetail(AilmentID, AvailID)` normally reads the text ID from `SkillAilmentType`. Two special cases are visible in the C export:

| Case | Runtime behavior |
| --- | --- |
| `AilmentID == 306` and `AvailID > 0` | Load the source `SkillAvailID.m_ModeChangeID`, then load `EnemyModeDB`; if the enemy-mode row has a text ID, use that instead of the ailment row text. |
| `AilmentID == 111` and `AvailID > 0` | Read the first `m_ValueAilment` value; if `AQPBattleManager::IsAlwaysCriticalValue(value)` is true, use text ID `62740` instead of the normal row text. |

`USkillDB::GetAilmentTypeIconTextureID(Avails, isSupportAbility, Index, pAilmentType)` scans every avail in a skill, then every positive add-ailment entry in each avail. It chooses `m_IconSptTexID` for support display and `m_IconTexID` otherwise. The `Index` argument is an ordinal over icon-bearing ailments, not an array index into a single row. This is why changing only one avail's ailment icon can shift which icon appears in multi-ailment skill displays.

### Physical fumble, parry, and avoidance are separate systems

OT0 has at least three different “attack does not land normally” systems that are easy to collapse together by mistake.

`AQPBattleCharacterBase::GetFumbleSkillRatio(SkillID, isMagic, pAffected)` is a physical-only ailment-11 reader. It ignores magic hits entirely. For living characters, it scans active ailment ID `11` and keeps the smallest `ADD_RATED` value as the fumble ratio. Several battle contexts suppress this reader, including command-phase action, damage-and-skill action, certain countdown-delayed player actions, counter phase, friendly-dead action phase, and turn-delayed action phase.

`AQPBattleCharacterBase::IsParry()` is not the same reader. It returns true if any of these active ailment IDs are present:

```text
494
510
223
```

`GetConsumeParryAilment()` is narrower: it returns the first active `PARRY` or `P_AVOID_PROBABILITY_PHYSICAL_ATTACK` record. The damage branch can then consume or mark that specific ailment record.

`IsAvoidance()` is broader again. It scans active ailments and calls `USkillDB::GetAvoidanceType(AilmentID)`; any nonzero avoidance type makes the character avoidance-active. The avoidance type helper has its own hardcoded ID map, so simply cloning the label of an avoidance ailment does not make a new ailment enter this branch.

### Counter and intercept skills are delayed-skill packages with ratios and priority

`AQPBattleCharacterBase::GetCounterAttackFlag(WeaponType, MagicType, TargetType, pCounterInfoList, isCritical)` is the main collector for counter/intercept candidates.

The branch first checks whether the character is currently skip-action-blocked. If a concrete weapon or magic attribute is supplied and the character is not skip-blocked, it tests whether the incoming attribute is weak on the defender, then calls:

```text
USkillDB::GetCounterAttackFlag(AilmentID, WeaponType, MagicType, TargetType, isWeak, isCritical)
```

For a counter ailment to produce an executable counter package, the source avail must exist and must provide a positive delayed skill:

```text
source active ailment -> FAilmentInfo.m_AvailID
  -> SkillAvailID
  -> GetAvailValue(sourceAvail, stored boost/index) as invoke ratio
       if value == 0, use 100
       if value < 0, reject
  -> SkillAvailID.m_DelayedSkill must be positive
  -> SkillAvailID delayed-skill priority becomes counter priority
```

When `pCounterInfoList` is supplied, the runtime appends `FCounterInfo` records containing invoke ratio, delayed/intercept skill ID, priority, and source ailment type. The primary branch avoids adding multiple entries with the same priority, then sorts the final list by priority. Additional weaked/nonweaked/noncritical counter families are appended through separate `IsWeakedCounter`, `IsNonWeakedCounter`, and `IsNonCriticaledCounter` checks.

This explains the authoring model: counter/intercept ailments do not directly execute their own skill. They authorize a delayed skill from the source avail, with chance and priority also coming from that source avail.

### Weak-hit damage bonuses can be active-ailment-driven

`AQPBattleCharacterBase::GetDebuffWeakDamage(WeaponType, MagicType)` checks whether the defender is currently weak to the incoming weapon or magic type. If not, it returns `0`. If yes, it searches for active ailment ID `502`; when present, it returns the active ailment value at offset `+0x54` as a percentage multiplier value divided by `100`.

So this family is not a generic damage-up aura. It only contributes when the incoming attack's attribute is currently weak on the target.

### Repeat-skill support is hit-table-driven

`AQPBattleManager::CheckRepeatSkill(HitRateID, Character, PrevSkillID)` is more concrete than “repeat previous skill.” It asks the character for a repeat package through:

```text
AQPBattleCharacterBase::GetRepeatSkillHitTypeID(
    &spRate,
    &useBoost,
    &nameID,
    &availID,
    PrevSkillID)
```

If that returns a positive hit-rate ID, the runtime calls `MakeHitData` using that `SkillHitRateList` row. Each successful hit flag increments `m_SupportRepeatCount`. If at least one hit passes, the source avail's `m_InvokeCondition` is checked through `CheckInvokeCondition`. Only after that does the runtime:

```text
if useBoost is false:
    reset m_UseBoostLevel to 0
if spRate > 0:
    SetRepeatSpRate(character, spRate)
else:
    SetRepeatSpRate(character, -1)
if PrevSkillID has a count limit:
    increment the previous skill's count limit by 1
rerun USkillDB::IsAvailableSkill on PrevSkillID
if still available:
    set skip-skill-voice flag
```

This means repeat skills are gated by the hit-rate package, invoke condition, SP-repeat rate, count-limit compensation, and a second availability check. A repeat support can fail at any one of those layers.

### Pursuit skills are support-triggered action commands

`AQPBattleManager::ProceedPursuitSkill(Character)` shows that pursuit is not a normal immediate follow-up. It first finds the acting character's buddy and requires at least one enemy to have `GetIsDamagedByNowAct()`. It then asks `UPlayerDataUtility::GetSupportAbilitySkillAfterSkill(PlayerID, PURSUIT_SKILL)` for delayed skill info.

Before executing, the first delay-skill entry's hit-rate row is checked. If the hit-rate roll fails, the pending pursuit list is cleared and nothing executes. If it passes, the buddy physically steps in, the battle manager creates a normal `AQPActionCommand` for the stored skill ID, suppresses boost-camera behavior, runs pre-action, command, and post-action stages, and can process multiple queued pursuit entries before stepping the buddy out.

Gameplay-facing consequence: pursuit skills are real action-command executions by the buddy, but their eligibility comes from support ability data plus a hit-rate gate, not from the original skill's avails.

### Turn-delayed skills execute through the normal action pipeline

`AQPBattleManager::ProceedTurnDelayedSkill(InvokeTiming, Phase)` activates only for positive invoke timings. When the phase is negative it clears the pending turn-delayed skill/order lists and, for `TURN_BEGIN` and `TURN_FINISH`, marks `m_IsTurnDelayedAction` true. It then collects characters with delayed skills for that invoke timing, appends their delayed skill IDs to `m_TurnDelayedSkills`, and records which interrupt-character index owns each skill.

Execution then iterates the pending list. For each entry it:

```text
writes the delayed skill into m_ConfirmedCommandSkills for the owning character
sets m_TurnDelayedActionChr
temporarily freezes switch-tag state
calls ProceedAction(..., owner, InvokeTiming, ...)
restores switch-tag state after action
runs ProceedEndAction
clears last-damage and breaked-enemy work arrays between entries
stops early if battle win/loss is reached or no targetable enemy remains
```

So turn-delayed skills are not a separate mini-effect. They become confirmed command skills and pass through the ordinary action execution path, with special phase bookkeeping around them.

### Target collection has stateful fallbacks and previous-target routes

`AQPBattleManager::GetBattleTargets` first gives `GetAilmentedTargets` a chance to populate `m_TargetSelection`. If that produces targets, the normal target-type switch is skipped. Several target types then use stateful fallback arrays instead of recalculating targets from scratch:

| Target type family | Runtime source |
| --- | --- |
| `CONTINUE_NOSELF_OR_ENEMY1` | Uses `m_RndAppendTargets` when present; otherwise uses `m_PrevPrevTargets` and `m_PrevPrevTargetType`. |
| `LAST_RECOVER` | Uses `m_LastRecoverTargets`. |
| `ENEMY_ALL_RND`, side-by-side random, pair random, backup random | Builds a candidate list, then uses `SkillAvailID.m_RandomChoiceCount` when more than one target is available. |
| `FRIENDLY_MIN_HP` | Collects forward allies and selects the one with the lowest HP ratio. |
| `ENEMY_HP_MAX_ORDER` / `ENEMY_FORWARDS_HP_MAX_ORDER` | Builds candidates and uses `m_RandomChoiceCount` for the ordered/randomized HP-max path. |
| `FRIENDLY_ALL_NOALIVECHECK` | Collects forward and backup allies without the usual alive check. |

This is why target types are not pure one-shot selectors. Some of them reuse prior targets, append-random targets, last-recovery targets, or delayed state saved by previous phases.

### CotC 4.2.3 exposes additional skill-state variables through battle wrappers

The CotC Global 4.2.3 C export is stripped, but reflected wrappers still reveal several live skill/ailment state variables and battle constants. These are not final formulas by themselves, but they show which concepts are mutable runtime parameters in CotC:

| CotC wrapper/state surface | Meaning exposed by the C export |
| --- | --- |
| `execGetAilmentAvailValue` / `execSetAilmentAvailValue` | Active/current-party ailment value exposed to Blueprint/runtime wrappers. |
| `execGetAilmentCalcType` / `execSetAilmentCalcType` | Active/current-party ailment calc type is mutable and inspectable. |
| `execGetAilmentDelayedSkillID` / `execSetAilmentDelayedSkillID` | Ailment-linked delayed skill ID is a live value. |
| `execGetAilmentSupportAbilityFlg` / `execSetAilmentSupportAbilityFlg` | Support-ability origin flag is exposed separately from the ailment ID. |
| `execGetAilmentWasteSpRate` / `execSetAilmentWasteSpRate` | Waste-SP rate is a first-class ailment/battle value in CotC. |
| `execGetCalcAilmentPoisonBase`, `execGetCalcAilmentPoisonPlayerRatio` | Poison damage constants are runtime parameters. |
| `execGetCalcAilmentBleedBase`, `execGetCalcAilmentBleedPlayerRatio` | Bleed damage constants are runtime parameters. |
| `execGetCalcAilmentRegenerateBase`, `execGetCalcAilmentRegenerateDivisor` | Regeneration constants are runtime parameters. |
| `execGetMaxAilmentCount`, `execGetMaxAilmentTurn` | CotC exposes global ailment count/turn caps. |
| `execGetUnassignedIdOffsetAilmentTypeID`, `execGetUnassignedIdOffsetAilmentResistID` | CotC has explicit unassigned-ID offset controls for ailment type/resist ID spaces. |

This strengthens the cross-title caution: CotC did not merely add more table columns. It also exposes more of the ailment engine's working state through wrappers, so a column such as `m_MaxUseCount`, `m_AilmentTagID`, `m_TargetAilment`, `m_Parallel`, or `m_PhaseOver` should be treated as a live-system surface even when the final stripped formula has not been recovered.

## Additional runtime-resolved gameplay systems

This section collects skill behavior that is easy to misread from the row names alone. These are not new table schemas; they are gameplay paths recovered from the OT0 C export and, where explicitly marked, from CotC Global 4.2.3 wrapper/header surfaces.

### Player skill lists are constructed from board rows, save slots, equipment, and runtime filters

`UPlayerDataUtility::GetSkillIndices` does not simply return every row that points at a player. It builds three lists and then optionally merges equipment-provided skills.

- Command skills are read from the player's learned skill area. Each positive skill ID is accepted only if `USkillDB::IsSupportAbility(skillID, false)` is false and the caller requested command skills.
- Support-skill and support-ability entries are read from the player's set-skill save area, not from the command-skill learned-skill array. The recovered loop scans up to the saved support slots and builds a temporary `FSkillData` for each positive skill ID.
- The split between support skill and support ability is decided by `USkillDB::IsSupportAbility(&skillData, true)`. If true, the ID goes to the support-ability list; otherwise, if the row is still support-style and support skills were requested, it goes to the support-skill list.
- If `CheckEquipments` is true, equipment-provided command/support/support-ability skills are appended afterward. In battle, the call goes through `AQPBattleManager::GetEquipmentSkills`; outside battle it goes through `UPlayerDataUtility::GetEquipmentSkills`. This matters because battle-time equipment skill reads can respect battle caches and temporary selection state.

`UPlayerDataUtility::GetAllSkillIndices` is a higher-level collection helper. It starts from board skill indices, appends equipment skills if requested, optionally removes status/influence/all-status support abilities, removes blocked skills, and finally sorts the result. The status-removal step is real gameplay filtering: it builds `FSkillData` for each support ability, scans its avails, and removes the skill if an avail has a nonzero `m_ModifyStatus`, if it is an influence support ability and the caller asked to remove influence rows, or if it is an all-status support and the caller asked to remove all-status rows. `USkillDB::RemoveBlockedSkillID` then applies `SkillID.m_BlockedSkill` style hiding. Only after those removals does the helper build a sort-order list and call `USkillDB::SortSkills`.

This has two practical consequences:

1. A skill can exist in the database, be learned, and still not appear in a particular runtime list because the caller used one of these filters.
2. Equipment-granted skills are appended late; if a mod makes an equipment skill also appear in a board list, duplicate-management depends on the caller and should not be assumed to happen automatically.

### `BUFF_COPY` copies active ailment state, not just row IDs

`AQPBattleManager::AvailBuffCopy` is the concrete command branch for `m_ModifyType = BUFF_COPY`. The skill's add-ailment list supplies the ailment types eligible to be copied. For each eligible ailment ID, the function scans the enforcer's active `m_AilmentInfo`; when it finds a matching active record, it attempts to add that same ailment to the target.

The copied state is richer than “same ailment type”:

- The source active record supplies the source skill ID, source avail ID, invoke timing, special/support state, count/use information, and stored value lanes.
- The destination skill's `m_Turns[BoostLevel]` supplies the copied turn count, but if the target already had that ailment, the old target turn count is remembered and added back after the copy succeeds. This is why buff-copy effects can appear to preserve or extend an existing target-side buff instead of replacing it cleanly.
- If `USkillDB::GetAilmentCalcType(sourceAvailID, ailmentID) == CALC_BREAK_DAMAGE`, the copy branch loops according to the source active record's use/count value and applies the copied ailment repeatedly. Other calc types take a single `AddAilment` path.
- A successful copy calls `BeginAilmentInfo` for presentation and status refresh.

Therefore, `BUFF_COPY` should be documented as a live-state copier. It is not a simple “apply the same `SkillAilmentType` rows that the source has” effect; it copies selected active records with source identity and value context.

### `DECREASE_AILMENT_COUNT` and count-recovery branches operate on active use-count buckets

`AQPBattleManager::AvailChangeAilmentUseCount` handles use-count-changing skills, including the `DECREASE_AILMENT_COUNT` family. It only runs if the enforcer exists, the avail exists, and the earlier skill branch succeeded. The raw magnitude comes from `USkillDB::GetAvailValue(pAvail, BoostLevel)`.

The sign is branch-dependent:

- For `m_ModifyType = DECREASE_AILMENT_COUNT`, the avail value is negated before use.
- For the positive count-recovery families, the value remains positive.

The target list is not `m_AddAilment`; it scans `pAvail->m_SubAilment`. For each positive ailment ID in that array, it checks active use-count buckets in this order:

1. ordinary bucket, with `SupportFlag = -1` and `SpecialFlag = -1`;
2. support bucket, with `SupportFlag = 1`;
3. special bucket, with `SpecialFlag = 1`.

The function consumes the remaining delta as it walks those buckets. If the new use count is still positive, it calls `SetAilmentUseCount`. If the new count reaches zero or below, it calls `SubAilment` for that bucket. Positive count recovery can therefore restore use counts; negative count reduction can remove the active ailment entirely when the count is exhausted.

This is separate from turn shortening. A row that reduces use count is not equivalent to `SHORTEN_TURN_AILMENT`, even if both make a status disappear sooner.

### `SHORTEN_TURN_MINUS`, `SHORTEN_TURN_DEBUFF`, and `SHORTEN_TURN_AILMENT` are three filters over active ailments

`AQPBattleManager::AvailShortenAilmentTurn` implements the three OT0 command branches with a shared loop over the target's active `m_AilmentInfo`, but the filter differs by modify type.

| Modify type | Runtime filter before shortening |
| --- | --- |
| `SHORTEN_TURN_MINUS` | Shortens active ailments whose `USkillDB::GetAilmentFlag` reports the negative side. |
| `SHORTEN_TURN_DEBUFF` | Requires the negative-side flag and then requires `USkillDB::GetBfDbfFlag(ailment, true)` to be nonzero. |
| `SHORTEN_TURN_AILMENT` | Requires the negative-side flag and then requires `USkillDB::GetPlusMinusFlag(ailment, true)` to be nonzero. |

The amount removed is `USkillDB::GetAvailValue(pAvail, BoostLevel)`. The loop walks active ailment records from the end toward the beginning, reads the current turn count from the active record, and subtracts the avail value. If the result is positive, it calls `SetAilmentTurn`. If the result is zero or negative, it calls `SubAilment`. Special-skill active records pass a special flag into `SubAilment`, so special-skill and ordinary ailments of the same ID are not necessarily removed through exactly the same bucket.

The important gameplay reading is that the three modify types are not synonyms. They are increasingly specific negative-status filters, and the narrowing is done through `SkillAilmentType`-derived classification helpers rather than by the skill row itself.

### `REQUIRE_INVALID` restores spent resources once per player/status lane

`AQPBattleManager::AvailRequireInvalid` is the branch used when a skill should refund or invalidate a requirement after the skill effect is rejected. It is not a generic “skill failed” flag.

The function first looks at `USkillDB::GetModifyStatus(pAvail)`:

- If the status is SP, it determines how much SP was consumed. For skills whose original use skill has a `PASS_SP` or `SP_BURST` avail, it uses `Target->m_PrevUseSP`; otherwise it calls `USkillDB::GetConsumeSP(useSkillID, Target)`.
- If the status is BP and the target is in a skip-turn BP-repair state, it can restore reserved BP. If rent-BP mode is active and the target has a buddy, the buddy can receive the reserved-BP restoration. The refund value is based on the target's last reserved boost level.
- Other modify statuses do not receive this special recovery path.

The helper then uses `AQPBattleManager::m_RequireInvalidFlags`, keyed by player ID, to prevent the same player/status lane from being refunded repeatedly in the same invalidation context. Once a lane is accepted, it calls `RecoveryStatus(Target, ModifyStatus, refundValue, ...)`.

For modding, this means `REQUIRE_INVALID` should be paired carefully with the status lane it is expected to restore. A row pointed at the wrong `m_ModifyStatus` may still run, but it will not refund the resource the author expected.

### `STEAL_CURE` uses stored damage and command-chain flags

`AQPBattleManager::AvailStealCure` is more complex than “heal for a percentage of damage.” It reads:

- `m_TargetType`, because zero target type rejects the branch;
- `m_CalcType`, which must be `ADD_RATED` or `ADD_FIXED` for the normal branch;
- `m_ModifyStatus`, which must be HP, SP, or BP;
- the battle manager's cached steal-cure damage and previously stored steal-cure values.

For HP steal-cure, the function normally uses the current command's steal-cure target damage. If the battle manager marked that damage as updated, it recomputes the source damage as `m_LastDamageHP - m_RefilLastDamageHP`; on the last avail it copies `m_LastDamageHP` into `m_RefilLastDamageHP` and clears the update flag. This is why multi-avail drain skills can avoid counting the same damage more than once.

For `ADD_RATED`, the formula is effectively:

```text
healBase = cachedDamage
payload = CalcAvailValue(...)
healing = int(payload * healBase / 100)
```

For `ADD_FIXED`, the payload itself becomes the healing amount. HP and SP steal-cure enforce a minimum of `1` when the computed value is nonpositive but the branch is otherwise valid. The result then passes through `AQPBattleCharacterBase::GetStealStatus` and `AQPBattleManager::ClampCure` before `RecoveryStatus` is called.

The branch also carries a command-chain flag:

- HP steal-cure sets bit `1`.
- SP steal-cure checks whether HP steal-cure already happened and can mark the recovery as chained.
- BP steal-cure checks whether HP or SP steal-cure already happened.

This explains why HP/SP/BP drain packages in one command can display or behave differently from three unrelated separate skills even when their table rows look similar.

### `FORCE_CHANGE` is an immediate tag-swap branch with cleanup side effects

`AQPBattleManager::AvailForceChange` starts by calling `SwapTagImmediately(Target, true)`. Only after that succeeds does the branch perform the side effects that make forced change behave like a full battle-system swap:

- It finds the target's buddy.
- It clears reserved BP and boost lights on both the target and the buddy if either had reserved BP.
- If either character had a special skill selected as a standby skill, the branch calls `EndSpecialSkillSelectPerform` for that character.
- It removes cover-style ailments from both characters: `COVERED_ONE`, `COVERED_FORWARDS`, and `COVERED_FORWARD_BUDDY`.
- If this was a reserved swap, it sets the switch-tag state. Otherwise, outside the special insert-0th-turn case, it refreshes the battle order with `ACTION_SWAP`.
- It sets `Target->m_IsPursuitDisable`, which suppresses pursuit behavior after the forced change.
- For a particular target type route, it can enable the finished step-out flag and confirm a maintained praying skill on the buddy. In the ordinary branch it adds `OFF_BALANCE` to the buddy if absent and updates praying ailments on both characters.
- It refreshes forward status, processes change-forward countdown ailments, can execute change-forward slip ailments, can hand off ailments to the buddy, and adjusts countdown handoff colors.

So `FORCE_CHANGE` is not merely a target-position operation. It touches boost reservation, special-skill selection, cover state, action order, pursuit suppression, praying state, countdown handoff, and support-trigger checks.

### `MAKE_LEAVE_FROM_BATTLE` removes the character from multiple battle collections

`AQPBattleManager::AvailMakeLeftFromBattle` is the command branch behind leave-from-battle behavior. It does not only set a flag on the target.

When the target is a forward player with a buddy, the function moves the buddy to the target's current position and swaps order. If the target is a forward player without a buddy, it removes that forward slot from the control panel and clears the forward array entry. If the target is a backup, it removes the backup status panel and clears the backup array entry.

After that, the function calls order and active-character cleanup:

```text
RefreshOrders(order type 8)
LeaveActiveCharacter(target)
RemoveNextOrder(target)
remove target from m_NowPeriodOrders
remove target from m_NextPeriodOrders
remove target from m_NextAllMemberOrders
remove target from m_BattleCharacters
if raid turn control:
    remove from raid now-turn and next-turn arrays
SetIsLeftFromBattle(target, false)
Destroy(target)
```

A leave-from-battle skill therefore changes the living battle roster, control-panel state, and order lists. It should not be modeled as a normal death, escape, or ailment removal effect.

### Support ability targeting has an explicit target-validity gate

`UPlayerDataUtility::IsSupportAbilityTarget` is one of the stricter places where support ability behavior is resolved. The support ability being equipped is not enough; the candidate must pass target tests that consider:

- the support ability's own `SkillData` and cached constant info;
- the enforcer player ID and target player ID;
- whether the enforcer and target are alive;
- whether the row is a support ability rather than a support skill;
- whether the support ability's invoke condition can operate when the owner is dead;
- party index and parent target routing;
- the support ability invoke timing.

`IsValidSupportAbilityEffective` wraps this check and `IsValidSupportAbilityInvokeTiming`; the battle manager's support-skill activation paths therefore do not simply scan every support-category `SkillID`. A support ability can be present, equipped, and have a matching modify type, but still not be a valid target for a given support activation because the support-target gate rejects its owner/target/timing context.

### `GetEffectiveSupportAbility` separates mounted abilities, party-effective abilities, and equipment effects

The support ability list used during battle is not identical to the support list shown in the menu. `UPlayerDataUtility::GetEffectiveSupportAbility` works from party support arrays and effective-support arrays, then filters by support flag and invoke timing. Equipment-provided support abilities can be included through separate helper calls. This is why functions such as `GetSupportAbilityValue`, `GetSupportAbilityTurn`, `GetSupportAbilityAilment`, `GetSupportAbilityAilmentResist`, and `GetSupportAbilityResults` all need to specify whether equipment support should be included and whether the player or party-wide support set is being queried.

In practice, there are several different “support skill” lists:

| Runtime list | What it is used for |
| --- | --- |
| mounted support skills | player-selected support skills in save data |
| support abilities | passive support abilities distinguished by `IsSupportAbility(..., true)` |
| equipment skills | command/support/passive skills granted by equipment |
| effective support ability cache | battle-time list after party, equipment, timing, target, and support-flag filtering |
| no-ailment support cache | support abilities that are intentionally kept out of ailment-style state injection |

This explains apparent discrepancies where a support ability exists in the data but does not show up in a given battle calculation.

### Support ability values are usually gathered by modify type and invoke timing, not by skill label

Most OT0 player-side support helpers use the tuple `(PlayerID, ModifyType, InvokeTiming)` or a closely related tuple. Examples include:

- `GetEquipmentSkillValue(PlayerID, ModifyType, InvokeTiming, bIgnoreSelectAbility)`;
- `GetSelectAbilityValue(PlayerID, ModifyType, InvokeTiming)`;
- `GetSupportAbilityValue(PlayerID, ModifyType, InvokeTiming)`;
- `GetSupportAbilityTurn(PlayerID, ModifyType, EnemyFlag)`;
- `GetSupportAbillityAvailValue(PlayerID, ModifyType, isLessThan)`;
- `GetSupportAbilityHitRatioList(PlayerID, ModifyType)`;
- `GetSupportAbilitySkillAfterSkill(PlayerID, ModifyType)`.

The gameplay-facing rule is that support ability semantics are usually opcode-driven. Renaming a skill or copying presentation fields does not make the support helper pick it up. The first avail's modify type, invoke timing, target restrictions, support classification, and target validity are the important pieces.

### CotC 4.2.3 exposes more script-facing skill overrides than OT0

The CotC Global 4.2.3 C export exposes many `QPBattleManager` and `QPBattleCharacterBase` blueprint-callable wrappers around skill state. These should be treated as runtime knobs, not as extra fields in the OT0 tables.

Concrete wrapper surfaces include:

| CotC wrapper family | Gameplay meaning |
| --- | --- |
| `SetPlayerSkillNoCost` / `GetPlayerSkillNoCost` | per-player no-cost override for ordinary player skills |
| `SetClassSkillNoCostFlag` / `GetClassSkillNoCostFlag` | no-cost override for class-skill routes |
| `SetPetSkillNoCostFlag` / `GetPetSkillNoCostFlag` | no-cost override for pet-skill routes |
| `SetOverrideSkillID` / `GetOverrideSkillID` | runtime replacement of the selected skill ID |
| `SetOverrideSpecialSkillID` / `GetOverrideSpecialSkillID` | runtime special-skill ID override |
| `SetOverrideSpecialSkillLevel` / `GetOverrideSpecialSkillLevel` | runtime special-skill level override |
| `SetAilmentAvailValue` / `GetAilmentAvailValue` | script-facing override/readback for active ailment value payloads |
| `SetAilmentCalcType` / `GetAilmentCalcType` | script-facing override/readback for active ailment calc type |
| `SetAilmentDelayedSkillID` / `GetAilmentDelayedSkillID` | script-facing delayed-skill payload attached to ailment state |
| `SetAilmentSupportAbilityFlg` / `GetAilmentSupportAbilityFlg` | script-facing support-ability flag on ailment state |
| `SetReviveRateTouchedTarget`, `SetDeadTouchedTarget`, `SetDumpStatusTouchedTarget` | target-touch state used by mobile skill/debug flows |

The important correction is that CotC's richer skill behavior is not only table-driven. Some mobile systems deliberately expose runtime overrides for cost, skill ID, special-skill level, and active ailment payloads. When porting concepts between OT0 and CotC, separate table schema (`SkillAvailID`, `SkillAilmentType`, etc.) from these script-managed runtime variables.

## Additional under-explained field behavior from the C export

This section covers fields that are easy to misread because they are present in the SDK row layout but are either read through small helper functions, read by byte offset, or only matter when another system has already selected the row. The important distinction is whether a field is **row identity**, **command availability**, **payload math**, **presentation**, or **classification/cache state**.

### `SkillID` sparse fields that are real runtime gates

Several `SkillID` columns have little stock-data visibility but are still read by native code:

| Field | C-export behavior | Gameplay consequence |
| --- | --- | --- |
| `m_RequireRatio` / `m_RequireValue` | The SP-cost path treats `m_RequireValue` as the base cost. When `m_RequireRatio` is true, the cost is interpreted as a percentage-style payload rather than a flat number. | A custom row can create max-SP-scaling costs, but the field is only meaningful through command-cost callers. It is not a general “resource ratio” switch. |
| `m_RequireBoost` | `USkillDB::GetRequireBoostLevel(SkillID)` reads the row's minimum boost requirement. Command validation compares the reserved boost/BP against this value. | A skill can be listed but not executable unless enough boost is reserved. This is separate from max-boost-only category logic. |
| `m_CountLimit` | `USkillDB::GetCountLimits(SkillID)` reads the row limit. `AQPBattleCharacterBase::SetupSkillCountLimit`, `GetSkillCountLimit`, `SetSkillCountLimit`, and `DecreaseSkillCountLimit` store live remaining counts per normalized/base skill. | Limited-use skills are not just data text. The battle character owns a live counter keyed by the boost-normalized skill ID. |
| `m_UnableRecoverCount` | Count-recovery helpers check the row before restoring skill-count usage. | Recovery effects can intentionally skip some limited skills. Count recovery is not a blind refill of every `m_CountLimit` row. |
| `m_BlockedSkill` | `USkillDB::GetBlockedSkillID` reads the field directly. Player skill-list construction removes or suppresses blocked rows. | This is a mutual-exclusion/filter hook, not merely metadata. It can make another skill disappear from selectable/equipment lists. |
| `m_OverrideCommandName` | `USkillDB::GetSkillNameID(..., GetOverrideName=true)` returns this text ID instead of `m_Name` when the override is positive. | The same skill can display a different command name in command contexts while keeping its ordinary name elsewhere. |
| `m_SpecialSkillRegenerateValue` | `USkillDB::GetSpecialSkillRegenerateValue` reads it and `BP_SPECIAL_REGEN_RATE` scales this resolved value. | This field is part of the special-gauge/BP-special regeneration formula. It should not be documented as simply unused in OT0. |
| `m_SpecialSkillInitialGaugeValue` | The field exists, but no convincing live OT0 gameplay reader was recovered in the checked C paths. | Keep this one bounded as schema-present/no-live-reader, unlike `m_SpecialSkillRegenerateValue`. |
| `m_AfterAction` | `USkillDB::GetAfterActionID` reads the row. `AQPBattleManager::IsChangeAfterAction` treats value `1` as post-action row-change eligibility after alive/front-row/action/buddy checks. | The value is a post-action behavior code. In OT0 stock usage it is best understood as “change after action,” not a generic script integer. |
| `m_InitialChargeTrun` / `m_ReChargeTrun` | `USkillDB::IsSkillChargeTurnType` succeeds only when recharge turn is positive, then outputs initial and recharge turns. Command-phase ability-turn code clamps these into `0..99`. | These are not merely unused typo fields. They are command-phase charge/recharge counters when the skill family uses that system. |
| `m_FieldUseable` plus `m_RecommendCategory` | Field-skill discovery looks for `m_RecommendCategory == HEAL` together with `m_FieldUseable`. | Field recovery menus are not just category-driven. Heal recommendation alone is insufficient without the field-useable flag. |
| `m_CommandCategory` / `m_CommandPhaseSkillIconID` | No strong live OT0 gameplay reader was recovered by exact field/offset search. | These should be described as reserved or weakly evidenced in OT0, not as proven gameplay fields. |

`USkillDB::GetMaxBoostLevel` is also more complicated than the `m_MaxBoostLv` name suggests. The function first follows the boost-family leader, scans `m_BoostSkills` until the first zero, and has fallback logic that inspects avail value/count/ailment arrays and `MAXBOOST_COMMAND` category. In normal OT0 command play, the global `AQPBattleManager::StaticGetMaxBoostLevel()` still dominates max-boost behavior. Treat `m_MaxBoostLv` as a row surface, but do not assume that changing only this field fully expands the battle boost range.

### `SkillAvailID` fields that are small but gameplay-significant

| Field | Native reader | Exact behavior |
| --- | --- | --- |
| `m_AilmentInvokeCondition` | `USkillDB::GetAilmentInvokeCondition`, `AQPBattleManager::AvailAilment`, `StaticCheckAvailAilmentCondition` | This is an ailment-side condition gate, separate from `m_InvokeCondition`. `AvailAilment` checks it with `(Victimizer, Character)` before the ailment tick/effect is allowed to run. |
| `m_InvocationProbability` | `USkillDB::GetInvocationProbablity`, `AQPBattleManager::CheckAvailSuccess` | Used instead of the ordinary hit ratio only for a narrow modify-type band: OT0 raw modify types `148`, `149`, `150`, `151`, and `152` (`DEBUFF_DAGGER`, `DEBUFF_AXE`, `DEBUFF_BOW`, `DEBUFF_ROD`, `DEBUFF_PAPERS`). Outside that band, `CheckAvailSuccess` uses `GetHitRatio`. |
| `m_HitTypes` | `USkillDB::GetHitRatio`, `GetHitTypeID`, `GetHitTypeInfo`, `MakeHitData` | The first hit-type row can provide the fallback single success ratio, but full multi-hit execution uses the hit table's `m_MinCount`, `m_CountAilment`, `m_HitRates`, and `m_Shuffle`. |
| `m_IgnoreWeak` | `USkillDB::GetSkillFixedShieldDamage` | The helper writes this into `isForceWeak` and returns `m_ShieldDamage`. In fixed and ordinary damage callers it is a force-weak/shield fallback flag, despite the misleading name. |
| `m_NoBreakable` | exact-name audit | The checked OT0 C export shows constructor/copy/property evidence but no strong direct gameplay reader. | Treat as reserved/no-live-reader unless a future caller is recovered. Do not document it as proven break immunity. |
| `m_OverwriteCount` | `USkillDB::IsOverwriteAvailUseCount` | Existing active ailments can overwrite use-count state instead of using the ordinary update/merge path. It is a use-count update rule, not a general turn overwrite rule. |
| `m_LotteryAilment` | `USkillDB::GetAvailAilmentLottery` | If true, the helper returns the avail's `m_AddAilment` array as the lottery candidate set. The actual selected lottery ailment is later filtered against this candidate list during application. |
| `m_Suicide` | `USkillDB::GetSuicide(SkillID)` | Scans all avails on the skill and returns true if any positive avail has the flag. It is skill-level in effect, even though authored on the avail row. |
| `m_DelaySkillPriority` | `USkillDB::GetDelayedSkillPriority` | Returns this priority only when `m_DelayedSkill` is positive; otherwise the helper returns `-1`. | A priority value without a delayed skill is ignored by this path. |
| `m_NotExtensionTarget` | `USkillDB::GetSkillAvailExtensionTargetFlag` | If the explicit avail ID is zero, the helper falls back to the first positive avail in the skill. It then returns that avail's flag. | A single flagged first avail can make the whole skill behave as not-extension-target for callers that do not pass a concrete avail ID. |
| `m_TargetNextPriority` | `USkillDB::GetTargetNextOrderPriority` | Directly read as the target-side next-order priority payload. | This is separate from `m_OrderPriority` and `m_NextPriority`, which affect the enforcer-side priority helper. |
| `m_EnhancedSkillID` | `USkillDB::GetEnhancedSkills` | Loads `SkillEnhancedList` and returns its `m_EnhancedSkills` array. | This is a whitelist pointer used by enhancement checks; it is not itself a percentage. |
| `m_SkillRatios` | `USkillDB::GetSkillRatio` | Reads the boost-indexed ratio directly. Damage and healing callers apply it later as a multiplier around `/100`. | It is a late skill-ratio multiplier, not another `m_Values` entry. |
| `m_Turns` | `USkillDB::GetAvailTurn` | Reads the boost-indexed turn. At boost `0`, if `m_CalcTypeAilment[0]` is nonzero and `m_Turns[1]` is greater than the base turn while `m_Turns[2] <= 0`, it randomizes between the base turn and `m_Turns[1]`. If any `m_AddAilment` entry is ailment `281`, the function returns `0`. | Turn arrays can encode a small random range, and ailment `281` forcibly creates a no-turn result through this getter. |
| `m_Values` / `m_FluctuationValues` | `USkillDB::GetAvailValue` | Reads `m_Values[boost]`; if fluctuation is nonzero, it draws inclusively between `value` and `value + fluctuation`, regardless of which one is larger. | Negative fluctuations are valid; the code normalizes low/high before drawing. |

The probability gate deserves special caution. `CheckAvailSuccess` first tries precomputed hit flags when `AvailIndex` indexes the battle manager hit arrays. Only when it cannot use those flags does it fall back to a single probability check. That fallback uses `m_InvocationProbability` only for the raw `148..152` debuff-weapon-family modify types; otherwise it calls `GetHitRatio`. Therefore `m_InvocationProbability` is not a universal per-avail hit chance.

### `SkillEffectiveID` fields are timed program arrays, not passive presentation labels

The C export makes the effect row behave like a compact time program executed by `AQPActionCommand`:

| Field group | Native reader family | Gameplay-facing consequence |
| --- | --- | --- |
| `m_Animations`, `m_PlayTimes`, `m_PlayRates`, `m_AnimationTarget`, `m_AnimStartFrame`, `m_AnimEndFrame`, `m_AnimNoIdle` | `AQPActionCommand::CheckAnimation` and initialization paths | Animation playback is time-indexed. Start/end frames and no-idle flags can change how a command animates without touching damage data. |
| `m_Cameras`, `m_CameraTimes` | `CheckCamera`, `InitEffective` | Camera changes are independently timed. A skill can share gameplay avails but still have different camera staging. |
| `m_Effects`, `m_InvokeTimes`, `m_EffectLayout`, `m_OffsetsH`, `m_OffsetsV` | `CheckEffect` | Effect ID, timing, layout, and offsets are parallel arrays. A nonzero effect without a matching sensible time/layout can appear broken even if the gameplay resolves. |
| `m_Sounds`, `m_SoundTimes`, `m_SoundToVoice` | `ProceedEffective` / init | Sound playback is also timed; `m_SoundToVoice` controls sound/voice interaction rather than battle math. |
| `m_Voices1`, `m_Voices2`, `m_Voices3`, `m_VoiceTimes` | `InitEffective`, `ProceedEffective` | These are presentation voice lanes. They do not replace `SkillID.m_VoiceID`; both systems can matter. |
| `m_Texts`, `m_TextTime` | `ProceedEffective` | Battle text/telop-like presentation can be attached to the effective row, independent of skill detail text. |
| `m_CharaVisibility`, `m_VisibilityTarget`, `m_VisibilityStartTime` | `CheckVisibility` | Visibility changes are timed and targeted; this is why some skills hide/show actors without any modify-type involvement. |
| `m_HoldTime`, `m_AvailTime`, `m_Repeat` | `ProceedEffective`, `ProceedEffectiveOnlyLooks`, repeat helpers | Hold/avail timing controls when gameplay payloads and presentation completion advance; `m_Repeat` also intersects with repeat-skill handling. |

For CotC 4.2.3, the same base effective row is widened with `m_MagicStonePerformance`, camera-shake fields, `m_Text2nd`, and `m_SkillEffectiveLinkageID`. Those are confirmed schema surfaces. The linkage row then has its own hold time, avail time, offset, limit, linkage type, and linked effectives. This is a separate presentation-composition layer, not an OT0 backport.

### `SkillAilmentType` fields with weak-looking names but specific boundaries

Some ailment fields should be kept more bounded than their names suggest:

| Field | Correct bounded reading |
| --- | --- |
| `m_Label` | DB lookup and hardcoded logic use `m_id`; label is not the behavior key. Duplicate labels do not inherit native logic. |
| `m_EfficacyStepRate` | Present and copied, but the checked OT0 C evidence remains weaker than for `m_AilmentCalc`, `m_TargetStatus`, turn/count/value, remove, and child fields. Keep it as a suspected step-rate/data-carry field unless a stronger caller is recovered. |
| `m_AddEffect`, `m_AddEffectSound`, `m_CharacterEffect`, `m_FieldEffect`, `m_InvokeEffect`, `m_InvokeSound` | Foreign-key matches are clean, and effect/sound behavior exists around ailment setup/invoke, but some exact member reads are offset/decompiler-obscured. Describe them as effect/sound payloads, not as formula-bearing fields. |
| `m_ActionPriority` | The checked OT0 stock data leaves it zero and no strong gameplay reader was recovered. Do not infer priority behavior from the name alone. |
| `m_AbsorbHealStatus` | Foreign-key match to `CharaStatusID` is clean, but direct exact-name gameplay evidence is weaker than `m_TargetStatus`. Document as absorb/heal status selector with bounded confidence, not as a universal heal-routing field. |
| `m_ForceSupportAbility` / `m_ForceCommandAbility` | The helpers exist, and the bytes are readable as row flags, but their effect is classification/force-routing. They do not by themselves make a row execute as a support or command action. |
| `m_AllowAddAilmentOnDead` in CotC 4.2.3 | CotC-only schema field absent from OT0. It should be treated as a mobile dead-target/add-state surface until the stripped caller is fully isolated. |
| `m_MaxUseCount`, `m_AilmentTagID`, `m_TargetAilment`, `m_ToUnlimited`, `m_Parallel`, `m_PhaseOver` in CotC 4.2.3 | Confirmed row fields and likely live-service state-machine additions. They are not present in OT0 and should not be projected onto OT0's `FAilmentInfo` behavior without a mobile caller. |

### CotC 4.2.3 fields that should be called out separately from OT0

CotC Global 4.2.3 widens several rows in ways that change what modders should look for, even when the stripped C does not yield a full formula:

| CotC field | Why it matters |
| --- | --- |
| `SkillIDBase.m_DispBoostLv` | UI can display authored boost level separately from the raw boost-family structure. |
| `m_IsBasicAbility`, `m_NoChangeBoostType`, `m_Boost246` | Basic/boost-classification surfaces absent from OT0's row. These are likely command-list and boost-display controls. |
| `m_ForcedSkillFilter`, `m_ExclusionSkillFilter`, `m_ForcedCategory`, `m_NotUsedAutoBattle` | CotC has data-driven filter and auto-battle exclusion surfaces; OT0 mostly relies on category/modify/target classifiers. |
| `SkillAvailIDBase.m_HitRatio` | CotC promotes a direct hit-ratio field, while OT0 derives fallback ratio from `SkillHitRateList`. Do not copy OT0 `m_PhysicalHitRatio` assumptions onto this field. |
| `m_LotteryAilmentNum` | CotC separates lottery enablement from number/count selection. OT0 only has the boolean and add-ailment candidate array. |
| `m_CompareValueAilment` | CotC adds an explicit compare flag near ailment value lanes, complementing ailment-side compare/override bytes. |
| `m_SubAilmentTag` / `m_SubAilmentTagFlag` | CotC can remove by ailment tags, not only explicit sub-ailment IDs. |
| `m_CalcTypeResist`, `m_AddResistWeapon`, `m_AddResistMagic` | CotC has explicit resist-addition payloads on avails. OT0's nearest analogues are weakness/resist replacement and ailment resist tables, not the same schema. |
| `m_AilmentMagnificationConditions`, `m_AilmentMagnifications` | Ailment-side potency package surfaces absent from OT0. Treat as real schema, formula pending. |
| `m_CountRefAilmentTag`, `m_CountRefType`, `m_CountRefAttribute` | CotC expands count-reference from a single ailment ID into tag/type/attribute forms. |
| `m_IgnorePreviousSuccessful`, `m_IgnoreDodge`, `m_IgnoreZeroDamage`, `m_IgnoreResistAilment`, `m_IgnoreDefenceBuff`, `m_IgnoreReduceDamage` | Explicit bypass flags for success/dodge/zero-damage/resist/defense/reduction systems. These make CotC avails much more self-contained than OT0 avails. |
| `m_HitWeaponEnable`, `m_HitWeaponType` | Mobile hit resolution can be forced through a weapon-family lane independent of ordinary attribute routing. |
| `m_NoConsumeAdditionWeak` | Explicit added-weakness consumption control. OT0's appended weakness behavior is split between avail-side `m_AppendType` and active ailment append types. |

## Proof matrix for the main foreign-key-style fields

- `SkillID.m_AttachEffect` -> `Effect/AttachEffect.m_id`: `10/10` unique nonzero OT0 values matched.
- `SkillID.m_SkillIconID` -> `Texture/TextureID.m_id`: `24/24` unique nonzero OT0 values matched.
- `SkillID.m_VoiceID` -> `Sound/PartVoiceID.m_id`: `25/25` unique nonzero OT0 values matched.
- `SkillID.m_WeaponIconType` -> `Skill/WeaponType.m_id`: `9/9` unique nonzero OT0 values matched.
- `SkillID.m_MagicIconType` -> `Skill/MagicType.m_id`: `7/7` unique nonzero OT0 values matched.
- `SkillAvailID.m_InvokeCondition` -> `Skill/SkillConditionList.m_id`: `37/37` unique nonzero OT0 values matched.
- `SkillAvailID.m_ModifyStatus` -> `Character/CharaStatusID.m_id`: `17/17` unique nonzero OT0 values matched.
- `SkillAvailID.m_HitTypes[*]` -> `Skill/SkillHitRateList.m_id`: `26/26` unique nonzero OT0 values matched.
- `SkillAvailID.m_ResistID` -> `Enemy/EnemyWeakID.m_id`: `32/32` unique nonzero OT0 values matched.
- `SkillAvailID.m_ResistAilmentID` -> `Skill/SkillResistAilmentID.m_id`: `26/26` unique nonzero OT0 values matched.
- `SkillAvailID.m_WeakChangeID` -> `Enemy/EnemyWeakChangeID.m_id`: `71/71` unique nonzero OT0 values matched.
- `SkillAvailID.m_WeakLockID` -> `Enemy/EnemyWeakLockID.m_id`: `64/64` unique nonzero OT0 values matched.
- `SkillAvailID.m_Reinforcement` -> `Enemy/EnemyReinforcements.m_id`: `59/60` unique nonzero OT0 values matched. The one outlier is value `140`, used by `SkillAvailID#74111` from `SkillID#900504`.
- `SkillAvailID.m_ModeChangeID` -> `Enemy/EnemyModeID.m_id`: `28/28` unique nonzero OT0 values matched.
- `SkillAvailID.m_SummonGuestID` -> `Character/CharaGuest.m_id`: `1/1` unique nonzero OT0 values matched.
- `SkillAvailID.m_OverrideWeapon` -> `Skill/WeaponType.m_id`: `8/8` unique nonzero OT0 values matched.
- `SkillAvailID.m_OverrideMagic` -> `Skill/MagicType.m_id`: `7/7` unique nonzero OT0 values matched.
- `SkillAvailID.m_SkillAvailMagnificationCondition` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `9/9` unique nonzero OT0 values matched.
- `SkillAvailID.m_SkillAvailMagnificationConditionArray[*]` -> `Skill/SkillAvailMagnificationConditionList.m_id`: `28/28` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Cameras[*]` -> `Battle/BattleCameraParams.m_id`: `47/47` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Animations[*]` -> `Battle/BattleActionID.m_id`: `92/92` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Effects[*]` -> `Effect/EffectList.m_id`: `712/712` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Sounds[*]` -> `Sound/SoundList.m_id`: `259/259` unique nonzero OT0 values matched.
- `SkillEffectiveID.m_Voices1[*]` and `m_Voices2[*]` -> `Sound/PartVoiceID.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_ActionID` -> `Battle/BattleActionID.m_id`: `2/2` unique nonzero OT0 values matched.
- `SkillAilmentType.m_TargetStatus` and `m_AbsorbHealStatus` -> `Character/CharaStatusID.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_IconTexID` and `m_IconSptTexID` -> `Texture/TextureID.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_AddEffect`, `m_CharacterEffect`, `m_FieldEffect`, `m_InvokeEffect` -> `Effect/EffectList.m_id`: all nonzero OT0 values matched.
- `SkillAilmentType.m_AddEffectSound`, `m_InvokeSound` -> `Sound/SoundList.m_id`: all nonzero OT0 values matched.
- `WeaponType.m_EquipType` -> `Item/ItemType.m_id`: `8/8` unique nonzero OT0 values matched.
- `WeaponType.m_AnimationSet` -> `Character/WeaponAnimationSet.m_id`: `8/8` unique nonzero OT0 values matched.
- `WeaponType.m_WeakIconTexID`, `WeaponType.m_SkillIconTexID`, `MagicType.m_WeakIconTexID`, `MagicType.m_SkillIconTexID`, `SkillUniqueIconID.m_SkillIconTexID`, and `SkillIconList` texture fields all match `Texture/TextureID.m_id` cleanly.

## Multilingual cross-checks

- OT0 stores Japanese as the base text bank and localized text under `GameText/Localize/*`. For example, `SkillID` name/detail text resolves cleanly across JP/EN/ZH-CN/KO-KR: `153236` is `復活手当` / `Vivify` / `复活处理` / `부활처치`, and `153376` is the corresponding revive description.
- For ailment UI text, `SkillAilmentType` primarily points into `GameTextUI`, **not** `GameTextSkill`. Example: `62327` is `毒` / `Poison` / `中毒` / `독`, and `96365` is the ailment explanation string.
- 
## How to read the `Skill/` folder

- The `Skill/` folder mixes three very different kinds of tables: **runtime rows** (`SkillID`, `SkillAvailID`, `SkillEffectiveID`, `SkillAilmentType`), **lookup legends** (`WeaponType`, `MagicType`, `ResistType`, `SkillCategory`, `SkillCalcType`), and **helper payloads** (`SkillHitRateList`, `SkillResistAilmentID`, `SkillTargetShopList`, etc.).
- Do not treat every integer field named `...ID` as a foreign key to another `Skill/` table. OT0 uses many direct enums, some inline percentage arrays, and some cross-folder row IDs.
- If you clone or edit a shared `SkillAvailID`, `SkillEffectiveID`, or `SkillAilmentType` row in place, you may silently alter many unrelated skills, because OT0 reuses payload rows heavily.

## Exact OT0 SDK row-struct quotations used by this guide

This section quotes the OT0 SDK directly from `SDK/Kingship_structs.hpp` so the guide does not stop at paraphrases. The exact row-struct definitions stay centralized here because they are long and readers often want to compare several table schemas side by side.

### Exact OT0 row-struct quotations used by the following `Skill/` sections

#### `FMagicTypeBase`
Quoted from `SDK/Kingship_structs.hpp:14381`.
```cpp
struct FMagicTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeakIconTexID;                                   // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconTexID;                                  // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FWeaponTypeBase`
Quoted from `SDK/Kingship_structs.hpp:9814`.
```cpp
struct FWeaponTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeakIconTexID;                                   // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconTexID;                                  // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EquipType;                                       // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AnimationSet;                                    // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FResistTypeBase`
Quoted from `SDK/Kingship_structs.hpp:8760`.
```cpp
struct FResistTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DamageRate;                                      // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillCategoryBase`
Quoted from `SDK/Kingship_structs.hpp:16239`.
```cpp
struct FSkillCategoryBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_BuffDebuffLimit;                                 // 0x000C(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_D[0x3];                                        // 0x000D(0x0003)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillIDBase`
Quoted from `SDK/Kingship_structs.hpp:12824`.
```cpp
struct FSkillIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Name;                                            // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Detail;                                          // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Job;                                             // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_BoostSkills;                                     // 0x0018(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_Category;                                        // 0x0028(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_RequireRatio;                                    // 0x002C(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_2D[0x3];                                       // 0x002D(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_RequireValue;                                    // 0x0030(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountLimit;                                      // 0x0034(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_UnableRecoverCount;                              // 0x0038(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_39[0x3];                                       // 0x0039(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_RequireBoost;                                    // 0x003C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeaponIconType;                                  // 0x0040(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MagicIconType;                                   // 0x0044(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsRandomReplace;                                 // 0x0048(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsWeaponSelect;                                  // 0x0049(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsReplaceNowSelectedWeapon;                      // 0x004A(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsReplaceMainWeapon;                             // 0x004B(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_4C[0x4];                                       // 0x004C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_WeaponReplaceSkill;                              // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Avails;                                          // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_BeginEffective;                                  // 0x0070(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_74[0x4];                                       // 0x0074(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Effectives;                                      // 0x0078(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_EndEffective;                                    // 0x0088(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AttachEffect;                                    // 0x008C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconID;                                     // 0x0090(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillUniqueIconID;                               // 0x0094(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CommandPhaseSkillIconID;                         // 0x0098(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_OverrideCommandName;                             // 0x009C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CommandCategory;                                 // 0x00A0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_BlockedSkill;                                    // 0x00A4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ReplaceCondition;                                // 0x00A8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_AC[0x4];                                       // 0x00AC(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ReplaceConditionArray;                           // 0x00B0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ReplaceSkill;                                    // 0x00C0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C4[0x4];                                       // 0x00C4(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ReplaceSkillArray;                               // 0x00C8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_DisableReplaceTag;                               // 0x00D8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_D9[0x3];                                       // 0x00D9(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_SpecialSkillInitialGaugeValue;                   // 0x00DC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SpecialSkillRegenerateValue;                     // 0x00E0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_RecommendCategory;                               // 0x00E4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_DuplicateAilment;                                // 0x00E8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_E9[0x3];                                       // 0x00E9(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AfterAction;                                     // 0x00EC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InitialChargeTrun;                               // 0x00F0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ReChargeTrun;                                    // 0x00F4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MaxBoostLv;                                      // 0x00F8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_FieldUseable;                                    // 0x00FC(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_FD[0x3];                                       // 0x00FD(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_VoiceID;                                         // 0x0100(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_104[0x4];                                      // 0x0104(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillAvailIDBase`
Quoted from `SDK/Kingship_structs.hpp:17289`.
```cpp
struct FSkillAvailIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeTiming;                                    // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeCondition;                                 // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TargetType;                                      // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ModifyType;                                      // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ModifyStatus;                                    // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AvailTag;                                        // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_IsFoodAvail;                                     // 0x0030(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_31[0x7];                                       // 0x0031(0x0007)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_HitTypes;                                        // 0x0038(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_RandomChoiceCount;                               // 0x0048(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CalcType;                                        // 0x004C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Values;                                          // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_FluctuationValues;                               // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_SkillRatios;                                     // 0x0070(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Turns;                                           // 0x0080(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_OverwriteCount;                                  // 0x0090(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_91[0x7];                                       // 0x0091(0x0007)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Counts;                                          // 0x0098(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_OrderPriority;                                   // 0x00A8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_NextPriority;                                    // 0x00AC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TargetNextPriority;                              // 0x00B0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ShieldDamage;                                    // 0x00B4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IgnoreWeak;                                      // 0x00B8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_NoBreakable;                                     // 0x00B9(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_BA[0x2];                                       // 0x00BA(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_PhysicalHitRatio;                                // 0x00BC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CriticalRatio;                                   // 0x00C0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Suicide;                                         // 0x00C4(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_LotteryAilment;                                  // 0x00C5(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C6[0x2];                                       // 0x00C6(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AilmentInvokeCondition;                          // 0x00C8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AddAilmentPick;                                  // 0x00CC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AddAilment;                                      // 0x00D0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_CalcTypeAilment;                                 // 0x00E0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_ValueAilment;                                    // 0x00F0(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_NotExtensionTarget;                              // 0x0100(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_101[0x3];                                      // 0x0101(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_WeakLockID;                                      // 0x0104(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DelayedSkill;                                    // 0x0108(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DelaySkillPriority;                              // 0x010C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SubAilmentPick;                                  // 0x0110(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_114[0x4];                                      // 0x0114(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SubAilment;                                      // 0x0118(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ResistID;                                        // 0x0128(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_12C[0x4];                                      // 0x012C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ResistAilment;                                   // 0x0130(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ResistAilmentID;                                 // 0x0140(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Reinforcement;                                   // 0x0144(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_WeakChangeID;                                    // 0x0148(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EnhancedSkillID;                                 // 0x014C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SummonGuestID;                                   // 0x0150(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_OverrideWeapon;                                  // 0x0154(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_OverrideMagic;                                   // 0x0158(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AppendType;                                      // 0x015C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillAvailMagnificationCondition;                // 0x0160(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_164[0x4];                                      // 0x0164(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SkillAvailMagnificationConditionArray;           // 0x0168(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_SkillAvailMagnification;                         // 0x0178(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_17C[0x4];                                      // 0x017C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SkillAvailMagnificationArray;                    // 0x0180(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_InvocationProbability;                           // 0x0190(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ModeChangeID;                                    // 0x0194(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountRefAilment;                                 // 0x0198(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountRefValue;                                   // 0x019C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountRefValueLimit;                              // 0x01A0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1A4[0x4];                                      // 0x01A4(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillConditionListBase`
Quoted from `SDK/Kingship_structs.hpp:17406`.
```cpp
struct FSkillConditionListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Conditions;                                      // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Params;                                          // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AilmentTypes;                                    // 0x0030(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_StatusTypes;                                     // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_WeaponTypes;                                     // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_MagicTypes;                                      // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Equipment;                                       // 0x0070(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_ORFlag;                                          // 0x0080(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_81[0x7];                                       // 0x0081(0x0007)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillAvailMagnificationConditionListBase`
Quoted from `SDK/Kingship_structs.hpp:14869`.
```cpp
struct FSkillAvailMagnificationConditionListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_Conditions;                                      // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_IntParams;                                       // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AilmentParams;                                   // 0x0030(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_RaceParams;                                      // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Equipment;                                       // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillEffectiveIDBase`
Quoted from `SDK/Kingship_structs.hpp:17455`.
```cpp
struct FSkillEffectiveIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Repeat;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_HoldTime;                                        // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_AvailTime;                                       // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Cameras;                                         // 0x0018(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_CameraTimes;                                     // 0x0028(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Animations;                                      // 0x0038(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_PlayTimes;                                       // 0x0048(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_PlayRates;                                       // 0x0058(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AnimationTarget;                                 // 0x0068(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AnimStartFrame;                                  // 0x0078(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_AnimEndFrame;                                    // 0x0088(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_AnimNoIdle;                                      // 0x0098(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Effects;                                         // 0x00A8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_InvokeTimes;                                     // 0x00B8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_EffectLayout;                                    // 0x00C8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsH;                                        // 0x00D8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsV;                                        // 0x00E8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Sounds;                                          // 0x00F8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_SoundTimes;                                      // 0x0108(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_SoundToVoice;                                    // 0x0118(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Voices1;                                         // 0x0128(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Voices2;                                         // 0x0138(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Voices3;                                         // 0x0148(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_VoiceTimes;                                      // 0x0158(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_Texts;                                           // 0x0168(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_TextTime;                                        // 0x0178(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_CharaVisibility;                                 // 0x0188(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_VisibilityTarget;                                // 0x0198(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_VisibilityStartTime;                             // 0x01A8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillAilmentTypeBase`
Quoted from `SDK/Kingship_structs.hpp:14674`.
```cpp
struct FSkillAilmentTypeBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	class FName                                   m_Label;                                           // 0x000C(0x0008)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Resist;                                          // 0x0014(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Minus;                                           // 0x0015(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Plus;                                            // 0x0016(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Removable;                                       // 0x0017(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_Multiply;                                        // 0x0018(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverwriteTurn;                                   // 0x0019(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_ForceOverrideValue;                              // 0x001A(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_InvalidateDebuff;                                // 0x001B(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_CanInactiveFlag;                                 // 0x001C(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1D[0x3];                                       // 0x001D(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_InvokeTiming;                                    // 0x0020(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TargetStatus;                                    // 0x0024(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_TargetStatusArray;                               // 0x0028(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_AilmentCalc;                                     // 0x0038(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EfficacyStepRate;                                // 0x003C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_ResistWeapon;                                    // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<bool>                                  m_ResistMagic;                                     // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_RemoveAilment;                                   // 0x0060(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_TargetType;                                      // 0x0070(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_74[0x4];                                       // 0x0074(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_RemoveConditions;                                // 0x0078(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_RemoveParams;                                    // 0x0088(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_ChildAilment;                                    // 0x0098(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x009C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ExplanationTextID;                               // 0x00A0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_IconTexID;                                       // 0x00A4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_IconSptTexID;                                    // 0x00A8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AddEffect;                                       // 0x00AC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CharacterEffect;                                 // 0x00B0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_FieldEffect;                                     // 0x00B4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeEffect;                                    // 0x00B8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OnGroundInvoke;                                  // 0x00BC(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_BD[0x3];                                       // 0x00BD(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AddEffectSound;                                  // 0x00C0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_InvokeSound;                                     // 0x00C4(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ActionID;                                        // 0x00C8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ActionPriority;                                  // 0x00CC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                        m_ReplaceCharaID;                                  // 0x00D0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_CountDown;                                       // 0x00D4(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_NoCount;                                         // 0x00D5(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_D6[0x2];                                       // 0x00D6(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_StatusDetailSort;                                // 0x00D8(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IgnoreLimit;                                     // 0x00DC(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_RemainDead;                                      // 0x00DD(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsNotMemorize;                                   // 0x00DE(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_DF[0x1];                                       // 0x00DF(0x0001)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_AbsorbHealStatus;                                // 0x00E0(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_NotRace;                                         // 0x00E4(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_E5[0x3];                                       // 0x00E5(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_EnemyRace;                                       // 0x00E8(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_ForceSupportAbility;                             // 0x00F8(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_ForceCommandAbility;                             // 0x00F9(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_FA[0x2];                                       // 0x00FA(0x0002)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_WeaponAppendType;                                // 0x00FC(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MagicAppendType;                                 // 0x0100(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_DuplicateAilment;                                // 0x0104(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_105[0x3];                                      // 0x0105(0x0003)(Fixing Size After Last Property [ Dumper-7 ])
	int32                                         m_SkillReplace;                                    // 0x0108(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_10C[0x4];                                      // 0x010C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillAilmentOffsetsBase`
Quoted from `SDK/Kingship_structs.hpp:14518`.
```cpp
struct FSkillAilmentOffsetsBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PoisonX;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PoisonY;                                         // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BleedX;                                          // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BleedY;                                          // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SilenceX;                                        // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SilenceY;                                        // 0x0020(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SleepX;                                          // 0x0024(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_SleepY;                                          // 0x0028(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PalalysisX;                                      // 0x002C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PalalysisY;                                      // 0x0030(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_FearX;                                           // 0x0034(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_FearY;                                           // 0x0038(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_WasteX;                                          // 0x003C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_WasteY;                                          // 0x0040(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_DarknessX;                                       // 0x0044(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_DarknessY;                                       // 0x0048(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_HeadacheX;                                       // 0x004C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_HeadacheY;                                       // 0x0050(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_EnergyDrainX;                                    // 0x0054(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_EnergyDrainY;                                    // 0x0058(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_RegenerateX;                                     // 0x005C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_RegenerateY;                                     // 0x0060(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_ProvokeX;                                        // 0x0064(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_ProvokeY;                                        // 0x0068(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CounterX;                                        // 0x006C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CounterY;                                        // 0x0070(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_InterceptX;                                      // 0x0074(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_InterceptY;                                      // 0x0078(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_PerfectInterceptScale;                           // 0x007C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BreakX;                                          // 0x0080(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_BreakY;                                          // 0x0084(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CountDownX;                                      // 0x0088(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	float                                         m_CountDownY;                                      // 0x008C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillAilmentSkillReplaceListBase`
Quoted from `SDK/Kingship_structs.hpp:17264`.
```cpp
struct FSkillAilmentSkillReplaceListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_SkillList;                                       // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_AilmentType;                                     // 0x0020(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_24[0x4];                                       // 0x0024(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillResistListBase`
Quoted from `SDK/Kingship_structs.hpp:12898`.
```cpp
struct FSkillResistListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ResistWeapon;                                    // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_ResistMagic;                                     // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillResistAilmentIDBase`
Quoted from `SDK/Kingship_structs.hpp:17569`.
```cpp
struct FSkillResistAilmentIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_ResistAilments;                                  // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_ResistRate;                                      // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSkillHitRateListBase`
Quoted from `SDK/Kingship_structs.hpp:17517`.
```cpp
struct FSkillHitRateListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MinCount;                                        // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_CountAilment;                                    // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_14[0x4];                                       // 0x0014(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_HitRates;                                        // 0x0018(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	bool                                          m_Shuffle;                                         // 0x0028(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_29[0x7];                                       // 0x0029(0x0007)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillIconListBase`
Quoted from `SDK/Kingship_structs.hpp:16382`.
```cpp
struct FSkillIconListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_BattleIconTexID;                                 // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MenuIconTexID;                                   // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_14[0x4];                                       // 0x0014(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillUniqueIconIDBase`
Quoted from `SDK/Kingship_structs.hpp:13602`.
```cpp
struct FSkillUniqueIconIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_TextID;                                          // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillIconTexID;                                  // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverrideCommandPanel;                            // 0x0014(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverrideInBattle;                                // 0x0015(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_OverrideBuff;                                    // 0x0016(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_17[0x1];                                       // 0x0017(0x0001)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillSupplyItemGroupBase`
Quoted from `SDK/Kingship_structs.hpp:17605`.
```cpp
struct FSkillSupplyItemGroupBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillModifyType;                                 // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Lot;                                             // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillSupplyItemListBase`
Quoted from `SDK/Kingship_structs.hpp:12910`.
```cpp
struct FSkillSupplyItemListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_GroupId;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_RewardId;                                        // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ItemId;                                          // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Values;                                          // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Lot;                                             // 0x001C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	bool                                          m_IsUnique;                                        // 0x0020(0x0001)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_21[0x7];                                       // 0x0021(0x0007)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillTargetShopListBase`
Quoted from `SDK/Kingship_structs.hpp:12926`.
```cpp
struct FSkillTargetShopListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_ShopType;                                        // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_MapId;                                           // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
};
```

#### `FSkillTargetFarmItemListBase`
Quoted from `SDK/Kingship_structs.hpp:17641`.
```cpp
struct FSkillTargetFarmItemListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillModifyType;                                 // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_VillageFarmItemId;                               // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_AdditionalVillageFarmItemId;                     // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1C[0x4];                                       // 0x001C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillVisitorListBase`
Quoted from `SDK/Kingship_structs.hpp:17691`.
```cpp
struct FSkillVisitorListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_EnemyID;                                         // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_EscortNum;                                       // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_EscortType;                                      // 0x0020(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsY;                                        // 0x0030(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<float>                                 m_OffsetsX;                                        // 0x0040(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	TArray<int32>                                 m_CallSE;                                          // 0x0050(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
	int32                                         m_IconID;                                          // 0x0060(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_64[0x4];                                       // 0x0064(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSkillEnhancedListBase`
Quoted from `SDK/Kingship_structs.hpp:16345`.
```cpp
struct FSkillEnhancedListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Size After Last Property [ Dumper-7 ])
	TArray<int32>                                 m_EnhancedSkills;                                  // 0x0010(0x0010)(Edit, BlueprintVisible, ZeroConstructor, NativeAccessSpecifierPublic)
};
```

#### `FSpecialSkillIDBase`
Quoted from `SDK/Kingship_structs.hpp:17824`.
```cpp
struct FSpecialSkillIDBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_C[0x4];                                        // 0x000C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

#### `FSpecialSkillGrowthListBase`
Quoted from `SDK/Kingship_structs.hpp:13722`.
```cpp
struct FSpecialSkillGrowthListBase final : public FTableRowBase
{
public:
	int32                                         m_id;                                              // 0x0008(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SpecialSkillID;                                  // 0x000C(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_Level;                                           // 0x0010(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_SkillID;                                         // 0x0014(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	int32                                         m_DisplayPriority;                                 // 0x0018(0x0004)(Edit, BlueprintVisible, ZeroConstructor, IsPlainOldData, NoDestructor, HasGetValueTypeHash, NativeAccessSpecifierPublic)
	uint8                                         Pad_1C[0x4];                                       // 0x001C(0x0004)(Fixing Struct Size After Last Property [ Dumper-7 ])
};
```

## Complete `Skill/` file reference


### How to read the field-by-field runtime notes

Each field entry in this reference is intended to answer four practical authoring questions:

1. **What is the field keyed to?** Many fields are not free integers. They point to another table (`SkillAilmentType`, `EnemyWeakID`, `TextureID`, `BattleActionID`, and so on), or they store an enum value with title-specific drift.
2. **Which runtime path consumes it?** A field can be visible in exported schema but still have no recovered OT0 caller. Conversely, a stock-zero field can still be live because a helper reads it when authored nonzero.
3. **Is it scalar, parallel, or staged?** Most skill payload arrays are parallel by index. `m_AddAilment[i]`, `m_CalcTypeAilment[i]`, and `m_ValueAilment[i]` are one indexed package; the same rule applies to many effect, sound, camera, condition, reward, and magnification arrays.
4. **What breaks when cloning?** Runtime meaning often depends on multiple rows agreeing. A cloned `SkillID` can look valid but fail because the avail, ailment, resist package, text tag, or effective stage still points back to the original behavior.

A field marked **runtime-proven** is one where the C export exposes a direct reader or call path. A field marked **confirmed** may still be useful, but its meaning is being inferred from schema names, row overlap, enum labels, or exact table identity rather than from a complete recovered gameplay caller.

#### Evidence grading used in the field atlas

The field notes use stricter grades when a claim depends on runtime behavior rather than schema shape:

| Grade | Meaning | What is allowed to be claimed | What is not allowed to be claimed |
| --- | --- | --- | --- |
| **R1 direct reader** | A named or clearly identified native caller reads the field and returns or branches on it. | Exact lookup key, direct flag meaning, direct table link, direct returned value. | Full gameplay result if other callers later reinterpret the value. |
| **R2 execution dataflow** | The field is read inside a larger execution path such as damage, ailment setup, target resolution, or presentation scheduling. | The field participates in that runtime system and has the stated local effect. | That the field alone determines the whole result. |
| **S1 schema/enum confirmed** | SDK/header/schema names and enum families prove the field identity. | Table relation, enum family, array shape, title-specific field presence. | Live gameplay behavior in a title whose caller has not been recovered. |
| **D1 data-pattern supported** | Stock rows, ID overlap, and naming strongly support the meaning. | Safe authoring hypothesis and examples. | Hard runtime behavior. |
| **U unresolved/dormant** | The field exists, but no adequate caller or stock pattern was recovered. | Presence and cautionary notes. | Any gameplay meaning beyond the cautious description. |

A rigorous field entry should answer: **field**, **evidence grade**, **native consumer or data source**, **runtime meaning**, **paired fields**, and **authoring consequence**. When one of those pieces is missing, the guide should say so instead of silently upgrading the claim.
### OT0 C-export runtime contracts for the field atlas

The readable OT0 C export is unusually useful because many skill-system functions retain native class and function names. The contracts below are stricter than ordinary field notes: each contract ties a field or field group to a named native consumer, the local runtime behavior, the fields that must agree with it, and the concrete authoring consequence.

The format is deliberately conservative:

| Column | Meaning |
| --- | --- |
| **Field group** | The exported fields being interpreted. |
| **Native consumer** | The named OT0 function that reads or executes the field. |
| **Runtime contract** | The exact local behavior visible in the C export. |
| **Authoring consequence** | What a custom-skill author must keep synchronized. |

#### Contract: boost-indexed scalar lanes are three different systems, not one generic number array

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `SkillAvailID.m_Values` + `m_FluctuationValues` | `USkillDB::GetAvailValue` | Reads the value for the requested boost index, then applies an inclusive random roll between `value` and `value + fluctuation`, automatically handling negative fluctuation by swapping min/max. | Put deterministic values in `m_Values`; put only intentional randomness in `m_FluctuationValues`. A negative fluctuation is valid and means “random below the base,” not an invalid value. |
| `SkillAvailID.m_Turns` | `USkillDB::GetAvailTurn` | Reads the boost-indexed turn. Boost level `0` has an additional random-turn lane when the first ailment calc type is nonzero; the helper can also force the turn to `0` when ailment `281` is present in `m_AddAilment`. | Do not treat `m_Turns` as a plain duration array for every ailment. Some ailment-style avails can randomize or suppress the returned turn. |
| `SkillAvailID.m_Counts` | `USkillDB::GetAvailUseCount` | Direct boost-indexed count reader; no fluctuation and no secondary scan. | Use `m_Counts` for true use-count payloads, not duration or value payloads. |

<details><summary>OT0 C export proof: boost-indexed value, turn, and count readers</summary>

```cpp
__int64 __fastcall USkillDB::GetAvailTurn(const FSkillAvailIDBase *pAvail, int BoostLevel)
{
  RandomRange = 0;
  if ( !pAvail )
    return RandomRange;
  if ( BoostLevel >= 0 && BoostLevel < pAvail->m_Turns.ArrayNum )
    RandomRange = *(_DWORD *)&pAvail->m_Turns.AllocatorInstance.Data[4 * BoostLevel];

  if ( BoostLevel == 0 && pAvail->m_CalcTypeAilment.ArrayNum > 0 )
  {
    if ( *pAvail->m_CalcTypeAilment.AllocatorInstance.Data )
    {
      v6 = *(_DWORD *)&pAvail->m_Turns.AllocatorInstance.Data[4];
      if ( (int)RandomRange < v6 && *(int *)&pAvail->m_Turns.AllocatorInstance.Data[8] <= 0 )
        RandomRange = AQPBattleManager::GetRandomRange(v6 + 1, RandomRange);
    }
  }

  for ( v7 = pAvail->m_AddAilment.AllocatorInstance.Data;
        v7 != &pAvail->m_AddAilment.AllocatorInstance.Data[4 * pAvail->m_AddAilment.ArrayNum];
        v7 += 4 )
  {
    if ( *(_DWORD *)v7 == 281 )
      return 0;
  }
  return RandomRange;
}

__int64 __fastcall USkillDB::GetAvailUseCount(const FSkillAvailIDBase *pAvail, int BoostLevel)
{
  if ( pAvail && BoostLevel >= 0 && BoostLevel < pAvail->m_Counts.ArrayNum )
    return *(unsigned int *)&pAvail->m_Counts.AllocatorInstance.Data[4 * BoostLevel];
  return 0;
}

__int64 __fastcall USkillDB::GetAvailValue(const FSkillAvailIDBase *pAvail, int BoostLevel)
{
  if ( !pAvail )
    return 0;
  if ( BoostLevel >= 0 && BoostLevel < pAvail->m_Values.ArrayNum )
    value = *(_DWORD *)&pAvail->m_Values.AllocatorInstance.Data[4 * BoostLevel];

  if ( BoostLevel < pAvail->m_FluctuationValues.ArrayNum )
  {
    fluct = *(_DWORD *)&pAvail->m_FluctuationValues.AllocatorInstance.Data[4 * BoostLevel];
    if ( fluct )
    {
      min = value;
      max = value + fluct;
      if ( (int)value > (int)(value + fluct) )
      {
        min = value + fluct;
        max = value;
      }
      return min + random_inclusive(max - min);
    }
  }
  return value;
}
```

</details>

#### Contract: `SkillID.m_Avails` has exact-index, first-nonzero, and modify-type lookup modes

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `SkillID.m_Avails[index]` | `USkillDB::GetSkillAvailID(..., bSearch=false)` | Returns the exact slot when the index is valid. Invalid exact lookup returns the helper's sentinel, not a searched fallback. | Presentation/effective stages that expect slot alignment should keep zero holes intentional and documented. |
| `SkillID.m_Avails` with `bSearch=true` | `USkillDB::GetSkillAvailID(..., bSearch=true)` | If the requested slot is zero, scans forward from the start and returns the first nonzero avail. | Zero-slot fallback can make a caller execute or describe the first avail instead of “nothing.” Do not use holes casually in cloned multi-avail skills. |
| `SkillID.m_Avails` by opcode | `USkillDB::GetSkillAvailFromModify` and `GetSkillAvailIDFromModify` | Scans avails in order and returns the first avail whose `m_ModifyType` equals the requested opcode. | When a skill has multiple avails of the same modify type, lightweight UI/helper callers may see only the first one. Put the canonical helper-facing avail first. |

<details><summary>OT0 C export proof: avail lookup modes</summary>

```cpp
char __fastcall USkillDB::GetSkillAvailFromModify(
        const FSkillAvailIDBase **pAvail,
        int SkillID,
        ESKILL_MODIFY_TYPE ModifyType)
{
  SkillDB = USkillDataUtility::GetSkillDB(SkillID);
  if ( !SkillDB )
    return 0;

  for ( Data = (int *)SkillDB->m_Avails.AllocatorInstance.Data;
        Data != &Data[SkillDB->m_Avails.ArrayNum];
        ++Data )
  {
    if ( *Data > 0 )
    {
      SkillAvailDB = USkillDataUtility::GetSkillAvailDB(*Data);
      if ( SkillAvailDB && LOBYTE(SkillAvailDB->m_ModifyType) == ModifyType )
      {
        *pAvail = SkillAvailDB;
        return 1;
      }
    }
  }
  return 0;
}

__int64 __fastcall USkillDB::GetSkillAvailID(int SkillID, int Index, bool bSearch)
{
  result = -1;
  SkillDB = USkillDataUtility::GetSkillDB(SkillID);
  if ( SkillDB && Index >= 0 && Index < SkillDB->m_Avails.ArrayNum )
    result = SkillDB->m_Avails[Index];

  if ( !bSearch )
    return result;
  if ( result )
    return result;

  for ( Data = SkillDB->m_Avails.AllocatorInstance.Data;
        Data != end;
        Data += 4 )
  {
    if ( *(_DWORD *)Data )
      return *(_DWORD *)Data;
  }
  return result;
}
```

</details>

#### Contract: conditional replacement is boost-aware and scalar replacement dominates the array path

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `m_BoostSkills` | `USkillDB::GetConditionReplaceSkill` | Replacement is checked against the boosted concrete skill row when the requested boost level resolves to a boosted variant. | Put replacement data on the row that the boosted skill actually resolves to, or ensure the base row maps correctly. |
| `m_ReplaceCondition` + `m_ReplaceSkill` | `USkillDB::GetConditionReplaceSkill` | The scalar condition is checked first. If it passes and `m_ReplaceSkill > 0`, that replacement wins. | Do not expect array replacements to override a passing scalar replacement. |
| `m_ReplaceConditionArray` + `m_ReplaceSkillArray` | `USkillDB::GetConditionReplaceSkill` | Array conditions are paired by index. In the recovered OT0 path, an array replacement also requires a positive scalar `m_ReplaceSkill` as a gate before assigning the paired array replacement. | For robust custom rows, set a valid scalar replacement family when relying on array replacements, and keep each condition and replacement aligned by index. |
| `m_DisableReplaceTag` | `USkillDB::GetConditionReplaceSkill` | Copied to the output flag only when an actual replacement is chosen. | This is replacement-display metadata, not a replacement condition. |

<details><summary>OT0 C export proof: replacement resolution order</summary>

```cpp
_BOOL8 __fastcall USkillDB::GetConditionReplaceSkill(
        int *ReplacedSkillID,
        const FSkillData *SkillData,
        int BoostLevel,
        AQPBattleCharacterBase *Character,
        bool *pIsDisableTag)
{
  *ReplacedSkillID = SkillData->SkillID;

  MaxBoostLevel = USkillDB::GetMaxBoostLevel(SkillData, &BaseSkillID);
  if ( BoostLevel >= 0 && BoostLevel <= MaxBoostLevel )
  {
    SkillID = BaseSkillID;
    SkillDB = USkillDataUtility::GetSkillDB(BaseSkillID);
    if ( SkillDB && SkillDB->m_BoostSkills[BoostLevel] > 0 )
      SkillID = SkillDB->m_BoostSkills[BoostLevel];
  }

  SkillDB = USkillDataUtility::GetSkillDB(SkillID);
  if ( SkillDB && SkillDB->m_ReplaceCondition > 0 )
  {
    if ( UBattleUtility::CheckSkillConditions(SkillDB->m_ReplaceCondition, Character, nullptr) > 0 )
    {
      if ( SkillDB->m_ReplaceSkill > 0 )
      {
        *ReplacedSkillID = SkillDB->m_ReplaceSkill;
        if ( pIsDisableTag )
          *pIsDisableTag = SkillDB->m_DisableReplaceTag;
      }
      return true;
    }

    for each condition in SkillDB->m_ReplaceConditionArray:
      if ( condition > 0 && paired_replace_skill > 0 )
        if ( CheckSkillConditions(condition, Character, nullptr) > 0
          && SkillDB->m_ReplaceSkill > 0 )
        {
          *ReplacedSkillID = paired_replace_skill;
          if ( pIsDisableTag )
            *pIsDisableTag = SkillDB->m_DisableReplaceTag;
          return true;
        }
  }
  return false;
}
```

</details>

#### Contract: `SkillConditionList` is a small condition VM with parallel arrays and OR/AND mode

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| condition IDs + parameter arrays | `UBattleUtility::CheckSkillConditions` | Loads the condition row, then feeds parallel arrays into `CheckCondition`/`CheckConditions`: condition, param, status, ailment, weapon, magic, enemy placeholders, and equipment ID. | Treat every condition row as an indexed tuple table. Editing only one array corrupts the tuple. |
| OR flag | `UBattleUtility::CheckSkillConditions` | When the row's OR flag is set, the function returns success as soon as any populated condition tuple succeeds. | OR rows are not “all conditions”; they are “any condition.” Keep failure text and authoring expectations aligned. |
| default/AND mode | `UBattleUtility::CheckSkillConditions` | Non-OR rows call `CheckConditions` with the same parallel arrays and a small zeroed enemy-ID array. | For normal rows, the full condition package is evaluated together rather than early-returning on the first success. |

<details><summary>OT0 C export proof: condition row execution</summary>

```cpp
__int64 __fastcall UBattleUtility::CheckSkillConditions(
        int ConditionID,
        AQPBattleCharacterBase *Enforcer,
        AQPBattleCharacterBase *Target)
{
  if ( ConditionID <= 0 )
    return 0;
  SkillConditionDB = USkillDB::GetSkillConditionDB(ConditionID);
  if ( !SkillConditionDB )
    return 0;

  if ( SkillConditionDB[128] )
  {
    UBattleUtility::s_bIsORCondition = 1;
    for each tuple index:
      if ( CheckCondition(condition, param, status, ailment, weapon, magic,
                          0, equipID, Enforcer, Target, nullptr, nullptr) )
      {
        UBattleUtility::s_bIsORCondition = 0;
        return 1;
      }
    UBattleUtility::s_bIsORCondition = 0;
    return no_tuple ? 0 : -1;
  }

  return UBattleUtility::CheckConditions(
           conditions, params, statuses, ailments, weapons, magics,
           zero_enemy_ids, equip_ids, Enforcer, Target, nullptr, nullptr);
}
```

</details>

#### Contract: conditional magnification is a maximum-satisfied percentage, not an additive stack

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `m_SkillAvailMagnificationCondition` + `m_SkillAvailMagnification` | `AQPBattleManager::CalcAvailBuff` | Scalar condition is checked first. If it passes, the scalar magnification becomes the current candidate percentage. | Scalar magnification is a direct percentage such as `150`, not a row ID. |
| `m_SkillAvailMagnificationConditionArray` + `m_SkillAvailMagnificationArray` | `AQPBattleManager::CalcAvailBuff` | Each satisfied array condition proposes a percentage. The largest satisfied percentage wins. | Multiple satisfied conditions do not add together. Put the strongest desired value in the highest matching row, or rely on the max selection. |
| enhanced-rate ordering | `AQPBattleManager::CalcAvailBuff` | Eligible enhanced-rate adjustments are applied before conditional magnification. | When testing a value, separate enhancement effects from condition-magnification effects. |

<details><summary>OT0 C export proof: magnification max selection</summary>

```cpp
__int64 __fastcall AQPBattleManager::CalcAvailBuff(
        AQPBattleManager *this,
        unsigned int BaseValue,
        int SkillID,
        const FSkillAvailIDBase *pAvail,
        int *pRatio,
        AQPBattleCharacterBase *Enforcer,
        AQPBattleCharacterBase *Target)
{
  if ( Enforcer && SkillID > 0 )
  {
    ModifyType = USkillDB::GetModifyType(pAvail);
    if ( eligible_calc_type && ModifyType != STEAL_CURE )
      BaseValue = AQPBattleCharacterBase::GetEnhancedRate(Enforcer, SkillID, BaseValue, pRatio);
  }

  magnification = 0;
  condition = USkillDB::GetSkillAvailMagnificationConditionID(pAvail, -1);
  if ( condition > 0 && CheckSkillAvailMagnificationCondition(condition, Enforcer, Target) )
    magnification = USkillDB::GetSkillAvailMagnification(pAvail, -1);

  for each condition in pAvail->m_SkillAvailMagnificationConditionArray:
    if ( condition > 0 && CheckSkillAvailMagnificationCondition(condition, Enforcer, Target) )
      magnification = max(magnification, USkillDB::GetSkillAvailMagnification(pAvail, index));

  if ( magnification )
    return BaseValue * magnification / AQPBattleCharacterBase::s_DefaultParameterRatioInt;
  return BaseValue;
}
```

</details>

#### Contract: ailment add/remove arrays are indexed packages

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `m_AddAilment[index]` | `USkillDB::GetAvailAilment` | Directly returns the add-ailment ID at the requested index. | Add rows are numeric `SkillAilmentType.m_id`, not labels. |
| `m_CalcTypeAilment[index]` | `USkillDB::GetAilmentCalcType` | Directly returns the calc-type lane at the same index. | Keep this parallel to `m_AddAilment`; a shifted calc-type changes how the value is stored or applied. |
| `m_ValueAilment[index]` | `USkillDB::GetAilmentValue` | Directly returns the value lane at the same index. | A value without the matching ailment/calc lane is ambiguous or ignored. |
| `m_AddAilment` vs `m_SubAilment` | `USkillDB::GetAvailAilmentIndices` | The helper returns the add array for `isAdd=true` and the sub array for `isAdd=false`. | Add and remove lists are distinct package surfaces; they may be evaluated by different probability and condition paths. |
| `m_LotteryAilment` | `USkillDB::GetAvailAilmentLottery` | When true, the add-ailment array is returned as the lottery candidate list. | Lottery selection acts on the add list. Do not put mutually exclusive lottery candidates only in child or remove arrays. |

<details><summary>OT0 C export proof: ailment package readers</summary>

```cpp
FScriptContainerElement __fastcall USkillDB::GetAilmentCalcType(const FSkillAvailIDBase *pAvail, int Index)
{
  if ( pAvail && Index >= 0 && Index < pAvail->m_CalcTypeAilment.ArrayNum )
    return pAvail->m_CalcTypeAilment[Index];
  return 0;
}

__int64 __fastcall USkillDB::GetAilmentValue(const FSkillAvailIDBase *pAvail, int Index)
{
  if ( pAvail && Index >= 0 && Index < pAvail->m_ValueAilment.ArrayNum )
    return pAvail->m_ValueAilment[Index];
  return 0;
}

__int64 __fastcall USkillDB::GetAvailAilment(const FSkillAvailIDBase *pAvail, int Index)
{
  if ( pAvail && Index >= 0 && Index < pAvail->m_AddAilment.ArrayNum )
    return pAvail->m_AddAilment[Index];
  return 0;
}

const TArray<int> *__fastcall USkillDB::GetAvailAilmentIndices(
        const FSkillAvailIDBase *pAvail,
        bool isAdd)
{
  if ( !pAvail )
    return nullptr;
  return isAdd ? &pAvail->m_AddAilment : &pAvail->m_SubAilment;
}

bool __fastcall USkillDB::GetAvailAilmentLottery(
        const FSkillAvailIDBase *pAvail,
        TArray<int> **pIndices)
{
  if ( pAvail && pAvail->m_LotteryAilment )
  {
    if ( pIndices )
      *pIndices = &pAvail->m_AddAilment;
    return true;
  }
  return false;
}
```

</details>

#### Contract: ailment-resistance packages are scanned by ailment ID, and direct removal has a default-success route

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `SkillAvailID.m_ResistAilmentID` | `USkillDB::GetAvailAilmentResist` | Loads a `SkillResistAilmentID` package, scans `m_ResistAilments` for the requested ailment ID, then returns the rate at the same index in `m_ResistRate`. | The package is a parallel array map. Every ailment ID must have a matching rate at the same index. |
| high/low poison and bleed IDs | `USkillDB::GetAilmentResistRate` | High poison is normalized to poison; high bleed is normalized to bleed before package scanning. | A resistance package for ordinary poison/bleed can also cover the high variants through this reader. |
| multiple avails | `USkillDB::GetAilmentResistRate` | Scans all avails on the skill whose invoke condition passes and sums nonzero package rates. | A skill with several valid avails can accumulate ailment resistance rates. |
| `SkillAvailID.m_SubAilment` | `USkillDB::GetAilmentResistRate` | If an eligible avail directly lists the ailment in its remove list, the helper returns the default random ratio immediately. | Direct sub-ailment removal is not the same probability model as add-ailment application. |

<details><summary>OT0 C export proof: ailment resistance and direct removal</summary>

```cpp
__int64 __fastcall USkillDB::GetAvailAilmentResist(int ResistAilmentID, ESKILL_AILMENT_TYPE AilmentID)
{
  if ( ResistAilmentID <= 0 || AilmentID <= 0 )
    return 0;
  db = USkillDB::GetSkillResistAilmentDB(ResistAilmentID);
  if ( !db )
    return 0;

  for ( i = 0; i < db->m_ResistAilments.Num(); ++i )
  {
    if ( db->m_ResistAilments[i] == AilmentID )
      return db->m_ResistRate[i];
  }
  return 0;
}

__int64 __fastcall USkillDB::GetAilmentResistRate(
        int *Rated,
        int SkillID,
        ESKILL_AILMENT_TYPE AilmentID,
        AQPBattleCharacterBase *Enforcer)
{
  *Rated = 0;
  if ( AilmentID == HIGH_POISON )
    normalized = POISON;
  else if ( AilmentID == HIGH_BLEED )
    normalized = BLEED;
  else
    normalized = AilmentID;

  for each avail in SkillID.m_Avails:
    if ( CheckInvokeCondition(avail, Enforcer, nullptr, nullptr) >= 0 )
    {
      if ( avail->m_ResistAilmentID > 0 )
        total += matching SkillResistAilmentID rate;

      for each sub in avail->m_SubAilment:
        if ( sub == normalized || sub == AilmentID )
          return AQPBattleManager::c_DefaultRandomRatio;
    }
  return total;
}
```

</details>

#### Contract: fixed shield damage is live even when stock OT0 data leaves it unused

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `SkillAvailID.m_ShieldDamage` | `USkillDB::GetSkillFixedShieldDamage` | Returned directly when the avail exists. | A nonzero custom value is runtime-readable. Treat stock-zero as stock-data-unused, not code-unused. |
| `SkillAvailID.m_IgnoreWeak` | `USkillDB::GetSkillFixedShieldDamage` | Copied to the `isForceWeak` output flag. | This field is paired with fixed shield damage behavior and can change weak/force-weak handling. |

<details><summary>OT0 C export proof: fixed shield helper</summary>

```cpp
__int64 __fastcall USkillDB::GetSkillFixedShieldDamage(bool *isForceWeak, const FSkillAvailIDBase *pAvail)
{
  if ( pAvail )
  {
    *isForceWeak = pAvail->m_IgnoreWeak;
    return (unsigned int)pAvail->m_ShieldDamage;
  }
  *isForceWeak = 0;
  return 0;
}
```

</details>

#### Contract: active ailment values are stored in typed lanes and later merged with count-reference state

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `m_CalcTypeAilment[index]` | `FAilmentInfo::SetAilmentValue` | Calc type `3` stores into the rated lane, `4` into the fixed lane, `5` into the now-rated lane; weapon/magic-style slip damage can store through the calc lane. | The calc type does not merely affect text. It selects the active value lane used later by status and damage code. |
| active support ailment values | `FAilmentInfo::SetAilmentValue` | Some support-source values scale with the number of opened support abilities before clamping. | Support/passive cloning can change magnitude if source skill identity is wrong. |
| `m_CountRef*` fields on source avails | `FAilmentInfo::GetAilmentValue` | Count-reference entries matching the requested calc lane are added to the stored value and then clamped by the source avail's limit while preserving sign class. | Count-ref authoring is lane-specific. A count-ref value on the wrong calc lane will not affect the requested value. |

<details><summary>OT0 C export proof: active ailment value lanes and count-ref merge</summary>

```cpp
__int64 __fastcall FAilmentInfo::SetAilmentValue(
        FAilmentInfo *this,
        bool isNew,
        AQPBattleCharacterBase *Character,
        unsigned int Value,
        unsigned __int8 CalcType,
        int SkillID,
        int AvailID,
        int BoostLevel,
        AQPBattleCharacterBase *Overrider)
{
  switch ( CalcType )
  {
    case 3: pValue = &this->m_RatedValue; break;
    case 4: pValue = &this->m_FixedValue; break;
    case 5: pValue = &this->m_NowRatedValue; break;
    default: pValue = &this->m_CalcValue; break;
  }

  if ( pValue )
    *pValue = computed_and_clamped_value;

  FAilmentInfo::UpdateSkill(this, Overrider, SkillID, AvailID);
  this->m_BoostLevel = BoostLevel;
  return computed_and_clamped_value;
}

__int64 __fastcall FAilmentInfo::GetAilmentValue(FAilmentInfo *this, ESKILL_CALC_TYPE CalcType)
{
  switch ( CalcType )
  {
    case WEAPON:
    case MAGIC:         value = this->m_CalcValue; break;
    case ADD_RATED:     value = this->m_RatedValue; break;
    case ADD_FIXED:     value = this->m_FixedValue; break;
    case ADD_NOW_RATED: value = this->m_NowRatedValue; break;
  }

  for each count_ref in this->m_CountRefInfo:
    if ( count_ref.CalcType == CalcType )
      value += count_ref.Value;

  for each matching count_ref:
  {
    sourceAvail = USkillDB::GetSkillAvailDB(count_ref.AvailID);
    if ( sourceAvail && sourceAvail->m_CountRefValueLimit )
      value = clamp_toward_limit_without_crossing_sign(value, sourceAvail);
  }
  return value;
}
```

</details>

#### Contract: skill names and icons are computed, not copied directly from one field

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `m_Name` | `USkillDB::GetSkillNameID` | Name lookup resolves the boosted concrete skill row first. | Boost variants can change the displayed name if their row differs. |
| `m_OverrideCommandName` | `USkillDB::GetSkillNameID` | If override-name lookup is requested and this field is positive, it overrides `m_Name`. | A skill can display a different command name than its base text field suggests. |
| `m_SkillIconID` | `USkillDB::GetSkillIconTextureID` | The direct root-skill icon is the initial icon candidate. | This is the primary command icon surface, not `SkillIconList`. |
| `m_SkillUniqueIconID` | `USkillDB::GetSkillIconTextureID` | When a unique-icon row resolves, it can replace the direct root icon and provide context-specific override flags. | If a cloned skill unexpectedly uses another icon, check the unique-icon row before blaming the texture table. |

<details><summary>OT0 C export proof: skill name and icon resolution</summary>

```cpp
__int64 __fastcall USkillDB::GetSkillNameID(const FSkillData *SkillData, int BoostLevel, bool GetOverrideName)
{
  SkillBase = SkillData->SkillBase;
  MaxBoostLevel = USkillDB::GetMaxBoostLevel(SkillData, &BaseSkillID);
  if ( BoostLevel >= 0 && BoostLevel <= MaxBoostLevel )
  {
    SkillDB = USkillDataUtility::GetSkillDB(BaseSkillID);
    if ( SkillDB && SkillDB->m_BoostSkills[BoostLevel] > 0 )
      SkillBase = USkillDataUtility::GetSkillDB(SkillDB->m_BoostSkills[BoostLevel]);
  }

  name = SkillBase ? SkillBase->m_Name : 0;
  if ( GetOverrideName && SkillBase && SkillBase->m_OverrideCommandName > 0 )
    return SkillBase->m_OverrideCommandName;
  return name;
}

__int64 __fastcall USkillDB::GetSkillIconTextureID(
        int SkillID,
        bool isBattle,
        bool isSimple,
        bool isAilment,
        bool isCommandPanel)
{
  SkillDB = USkillDB::GetSkillDB(SkillID);
  if ( SkillDB )
    icon = SkillDB->m_SkillIconID;

  if ( SkillDB && USkillDB::GetSkillUniqueIconData(SkillDB->m_SkillUniqueIconID, &OutData) )
  {
    icon = OutData.m_SkillIconTexID;
    overrideCommandPanel = OutData.m_OverrideCommandPanel;
    overrideInBattle = OutData.m_OverrideInBattle;
    overrideBuff = OutData.m_OverrideBuff;
  }
  return resolved_icon_for_context(icon, overrideCommandPanel, overrideInBattle, overrideBuff);
}
```

</details>

#### Contract: root skill charge and special-skill regeneration fields have direct native readers

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `m_InitialChargeTrun` + `m_ReChargeTrun` | `USkillDB::IsSkillChargeTurnType` | The helper returns true only when recharge is positive, then outputs both initial and recharge values. | A positive initial value alone does not make a skill charge-turn typed; set recharge when authoring this family. |
| `m_SpecialSkillRegenerateValue` | `USkillDB::GetSpecialSkillRegenerateValue` | Directly read from the skill row. | This field is not a schema-only guess; gauge-regeneration helpers can consume it. |

<details><summary>OT0 C export proof: charge and special regeneration readers</summary>

```cpp
char __fastcall USkillDB::IsSkillChargeTurnType(int SkillID, int *Initial, int *Recharge)
{
  SkillDB = USkillDB::GetSkillDB(SkillID);
  if ( !SkillDB || SkillDB->m_ReChargeTrun <= 0 )
    return 0;
  *Initial = SkillDB->m_InitialChargeTrun;
  *Recharge = SkillDB->m_ReChargeTrun;
  return 1;
}

FScriptContainerElement *__fastcall USkillDB::GetSpecialSkillRegenerateValue(int SkillID)
{
  result = USkillDB::GetSkillDB(SkillID);
  if ( result )
    return (FScriptContainerElement *)result->m_SpecialSkillRegenerateValue;
  return result;
}
```

</details>

#### Contract: order-priority classification has three independent triggers

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `m_ModifyType == GUARD` | `USkillDB::IsOrderPriority` | Guard modify type marks the skill as order-affecting on the enforcer side. | Guard-like skills can affect order UI even without explicit priority fields. |
| `m_OrderPriority` / `m_NextPriority` | `USkillDB::IsOrderPriority` | The helper scans all avails and checks the appropriate priority field depending on whether next-turn behavior is being queried. | Priority is avail-side, but the helper classifies the whole skill if any avail has a priority. |
| AGI/order ailments | `USkillDB::IsOrderPriority` | In next-turn mode, adding `BUFF_X_AGI`, `DEBUFF_X_AGI`, or `FORCE_ORDER_END` marks target-affecting order behavior. | A pure buff/debuff can still be order-affecting because of the ailment it applies. |

<details><summary>OT0 C export proof: order-priority classifier</summary>

```cpp
__int64 __fastcall USkillDB::IsOrderPriority(
        int SkillID,
        bool isNextTurn,
        bool *outIsTargetAffect,
        bool *outIsEnforcerAffect)
{
  isGuard = USkillDB::CheckSkillModifyType(SkillID, GUARD);

  for each avail in SkillID.m_Avails:
  {
    SkillAvailDB = USkillDB::GetSkillAvailDB(avail);
    priority = isNextTurn ? SkillAvailDB->m_NextPriority : SkillAvailDB->m_OrderPriority;
    if ( priority )
      break;
  }

  enforcerAffect = isGuard || priority;
  targetAffect = false;

  if ( isNextTurn )
  {
    if ( USkillDB::IsSkillAddAilment(SkillID, BUFF_X_AGI)
      || USkillDB::IsSkillAddAilment(SkillID, DEBUFF_X_AGI)
      || USkillDB::IsSkillAddAilment(SkillID, FORCE_ORDER_END) )
    {
      targetAffect = true;
      result = true;
    }
  }

  if ( outIsTargetAffect )
    *outIsTargetAffect = targetAffect;
  if ( outIsEnforcerAffect )
    *outIsEnforcerAffect = enforcerAffect;
  return result;
}
```

</details>

#### Contract: generic calc types `ADD_RATED`, `ADD_FIXED`, and `ADD_NOW_RATED` have exact arithmetic in OT0

| Field group | Native consumer | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| `ADD_RATED` | `AQPBattleManager::CalcValue` | Uses `Max` as the percentage base: `current + Param * Max / 100`. | Use this when the value is a percentage of maximum, not current. |
| `ADD_NOW_RATED` | `AQPBattleManager::CalcValue` | Uses the current `Value` as the percentage base: `current + Param * Value / 100`. | Use this for current-value percentages. |
| `ADD_FIXED` | `AQPBattleManager::CalcValue` | Returns `Value + Param`. | Use for flat additions/subtractions. |
| `LAST_1` | `AQPBattleManager::CalcValue` | Returns `1 - Value`. | This is a special last-one style transform, not a percentage. |

<details><summary>OT0 C export proof: exact generic calc arithmetic</summary>

```cpp
__int64 __fastcall AQPBattleManager::CalcValue(
        ESKILL_CALC_TYPE CalcType,
        unsigned int Value,
        unsigned int Max,
        int Param)
{
  switch ( CalcType )
  {
    case ADD_RATED:
      Value = Max;
      break;
    case ADD_FIXED:
      return Value + Param;
    case ADD_NOW_RATED:
      break;
    case LAST_1:
      return 1 - Value;
    default:
      return Value;
  }

  return current + Param * Value / AQPBattleCharacterBase::s_DefaultParameterRatioInt;
}
```

</details>

#### Contract: helper-facing skill classifiers are usually first-match scans, not full execution

| Classifier | Native reader | Runtime contract | Authoring consequence |
| --- | --- | --- | --- |
| steal skill | `USkillDB::IsStealModifyType` | Returns true if the skill has a `STEAL_ITEM` or `STEAL_LEAF` avail. | Result/UI steal helpers can miss custom steal-like behavior if it uses another modify opcode. |
| append-weak skill | `USkillDB::IsSkillHasAppendWeak` | Scans avails and returns true on the first positive `m_AppendType`. | One secondary avail can classify the whole skill as append-weak. |
| charge-turn skill | `USkillDB::IsSkillChargeTurnType` | Root-skill recharge gate, not avail execution. | Charge identity belongs on `SkillID`, not on a payload avail. |
| order-priority skill | `USkillDB::IsOrderPriority` | Scans modify type, avail priority fields, and specific ailments. | A skill can be order-affecting even when no single field named “order” is set on the root row. |

#### Field-reference consequences for rigorous custom authoring

Use the contracts above as hard checks when editing the field-by-field atlas rows:

1. **Every boost-indexed array must be audited by consumer.** `m_Values`, `m_Turns`, and `m_Counts` have different readers and different side behavior.
2. **Every parallel tuple must stay aligned.** Condition arrays, ailment arrays, resist arrays, replacement arrays, and magnification arrays are not independent lists.
3. **Every helper-facing classifier should be tested with the helper that will read it.** Menu, text, sorting, special-skill, support, order, and result systems often scan fields rather than executing the skill.
4. **Every stock-zero live field should be described as live-but-unused-in-stock.** `m_ShieldDamage` is the model example: the reader exists even though stock data rarely exercises it.
5. **Every cross-title enum must be mapped by meaning, not by integer.** The OT0 C export proves local behavior for OT0; CotC schema can confirm additional fields, but it does not make OT0 integers portable.
6. **Every claim should name its proof type.** Use `R1` for direct readers like `GetAvailValue`, `R2` for execution dataflow like `CalcAvailBuff` or `SetAilmentValue`, `S1` for schema-only surfaces, `D1` for data-pattern inference, and `U` for fields that still lack a recovered caller.


### `MagicType`
Element legend table. OT0 uses it anywhere a skill or ailment needs an element family for iconography or override behavior.

- **OT0 rows:** `9`.
- **CotC rows:** `8`.
- **OT0 SDK row struct:** `FMagicTypeBase`.
- **Related OT0 SDK enums:** `EMAGIC_TYPE`.
- `m_id` is the same element key family that appears in `SkillID.m_MagicIconType` and `SkillAvailID.m_OverrideMagic`.

#### Exact OT0 SDK enum quote used by this table
##### `EMAGIC_TYPE`
Quoted from `SDK/Kingship_structs.hpp:4295`.
```cpp
enum class EMAGIC_TYPE : uint8
{
	MAGT_NONE                                = 0,
	MAGT_FIRE                                = 1,
	MAGT_ICE                                 = 2,
	MAGT_THUNDER                             = 3,
	MAGT_WIND                                = 4,
	MAGT_LIGHT                               = 5,
	MAGT_DARK                                = 6,
	MAGT_NAUGHT                              = 7,
	MAGT_POISON                              = 8,
	MAGT_MAX                                 = 9,
};
```
- `MagicType.m_id` is not an arbitrary row number. OT0 keys this table so that the exported JSON row IDs `0..8` are the same raw values stored by the engine enum above.
- That is why `SkillID.m_MagicIconType`, `SkillAvailID.m_OverrideMagic`, and other element-family fields can be read both as direct SDK enum values and as `MagicType` row IDs without any translation layer.
- It is untested whether `MagicType` can be expanded to add new magic types
- Because `POISON` is a magic type, its damage appears to scale with the Elemental Attack stat, and the Elemental Defense stat may decrease the received poison damage. Solo Traveler's testing on JP CotC suggests the latter is true in that game, however, the enum `EMAGIC_TYPE` dumped from CotC Global 3.22.10 lists no such magic type. This might be a change introduced in JP after finishing the Side Solistia quest-line, or the observed changes to poison damage based on the party member's Elem. Def. are entirely independent from the weakness system.

#### Field-by-field
- `m_id`: **Proven.** Direct element family key. OT0 rows `0..8` align to `EMAGIC_TYPE` (`NONE`, `FIRE`, `ICE`, `THUNDER`, `WIND`, `LIGHT`, `DARK`, `NAUGHT`, `POISON`).
- `m_TextID`: **Proven.** Player-facing element name in `GameTextUI`. Example: OT0 `1 -> Fire`, `7 -> Non-Elemental`, `8 -> Poison`.
- `m_WeakIconTexID`: **Proven.** `Texture/TextureID.m_id` for the weakness-panel icon used when this element appears as a weakness (or resistance family) on the enemy status and shield UI.
- `m_SkillIconTexID`: **Proven.** `Texture/TextureID.m_id` for the command/skill overlay icon for this element family.


#### Runtime meaning
- Magic rows are not merely UI labels. The same numeric element family is reused by skill icons, weakness icons, enemy resist/weak arrays, target classifiers, and magic-damage calculation paths.
- `m_id` should be treated as an enum-backed family, not as an expandable content list. Adding a new row may give you a text/icon row, but native weakness arrays, SDK enum values, and battle classifiers still expect the fixed `EMAGIC_TYPE` family.
- When a `SkillAvailID` uses `m_CalcType = MAGIC`, the element usually comes from the skill/avail's magic-type context, while the visible icon can come from `SkillID.m_MagicIconType`. Keep those in agreement when cloning elemental skills.
- `m_WeakIconTexID` and `m_SkillIconTexID` intentionally differ: the former is for target weakness panels/status displays; the latter is for command/skill presentation.

#### CotC-only extra field
- `m_AdditionWeakIconTexID`: **Confirmed from CotC schema.** Additional weakness-icon texture used by CotC's added-weakness systems. OT0 has no corresponding field.

### `WeaponType`
Weapon-family legend table. This is the canonical place for OT0 weapon labels, weapon icons, the corresponding equipment-type row, and the battle-animation family.

- **OT0 rows:** `10`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FWeaponTypeBase`.
- **Related OT0 SDK enums:** `EWEAPON_TYPE`.
- It is untested whether `WeaponType` can be expanded to add new weapon types

#### Exact OT0 SDK enum quote used by this table
##### `EWEAPON_TYPE`
Quoted from `SDK/Kingship_structs.hpp:7327`.
```cpp
enum class EWEAPON_TYPE : uint8
{
	WPNT_NONE                                = 0,
	WPNT_SWORD                               = 1,
	WPNT_LANCE                               = 2,
	WPNT_DAGGER                              = 3,
	WPNT_AXE                                 = 4,
	WPNT_BOW                                 = 5,
	WPNT_ROD                                 = 6,
	WPNT_PAPERS                              = 7,
	WPNT_FAN                                 = 8,
	WPNT_NAUGHT                              = 9,
	WPNT_MAX                                 = 10,
};
```
- `WeaponType.m_id` is likewise keyed to the raw SDK enum above. The exported rows are the data-table mirror of the engine's weapon-family constants.
- This is why `SkillID.m_WeaponIconType`, `SkillAvailID.m_OverrideWeapon`, weakness grids, and weapon-animation routing can all share the same numeric family without a second remapping table.

#### Field-by-field
- `m_id`: **Proven.** Direct weapon-family key. OT0 rows align to `EWEAPON_TYPE` (`NONE`, `SWORD`, `LANCE`, `DAGGER`, `AXE`, `BOW`, `ROD`, `PAPERS`, `FAN`, `NAUGHT`).
- `m_TextID`: **Proven.** Player-facing weapon-family name in `GameTextUI`. Example: `1 -> Sword`, `2 -> Polearm`, `7 -> Tome`, `8 -> Fan`.
- `m_WeakIconTexID`: **Proven.** `Texture/TextureID.m_id` for the weakness-panel icon of that weapon family.
- `m_SkillIconTexID`: **Proven.** `Texture/TextureID.m_id` for the weapon overlay icon used in skill UI.
- `m_EquipType`: **Confirmed.** `Item/ItemType.m_id` for the matching equippable weapon-family item type. This is why the OT0 rows use item-type IDs like `1`, `7`, `3`, `2`, `5`, `4`, `6`, `8` rather than a second copy of the weapon enum.
- `m_AnimationSet`: **Confirmed.** `Character/WeaponAnimationSet.m_id`. This is the animation family that battle actions use when the skill is rendered as that weapon type.


#### Runtime meaning
- `WeaponType` is the bridge between authoring labels, equipment categories, weakness panels, and animation families. A weapon skill can therefore be visually correct but gameplay-wrong if `SkillID.m_WeaponIconType`, `SkillAvailID` weapon context, and `WeaponType.m_EquipType` do not describe the same family.
- `m_EquipType` is not cosmetic. It is the table-level reason weapon families line up with equippable item categories and weapon-form replacement logic.
- `m_AnimationSet` is the presentation half of the same identity. If a skill is routed to a different weapon family but its effective row still plays an action authored for another family, the command can resolve but animate incorrectly.
- Like magic types, weapon-family expansion is not proven safe. Native enums and arrays expect the fixed OT0 family; author new weapon-like behavior by reusing/remapping an existing family unless you are prepared to patch code.

### `ResistType`
OT0 weakness/resistance-rate legend. This is the percentage class table used by weakness-grid style tables such as `EnemyWeakID`.

- **OT0 rows:** `10`.
- **CotC rows:** not present in the CotC `Skill/` folder. CotC assigns resistance rates directly
- **OT0 SDK row struct:** `FResistTypeBase`.
- **Warning**: `EnemyWeakID` stores `ResistType` IDs per slot; `SkillResistList` stores direct percentage deltas instead. `SkillAvailID.m_ResistID` takes `EnemyWeakID.m_id` as input, **NOT** `SkillResistList.m_id`

#### Field-by-field
- `m_id`: **Proven.** Weakness-class key used inside `Enemy/EnemyWeakID.m_ResistWeapon` and `m_ResistMagic`.
- `m_DamageRate`: **Proven.** Actual damage-rate percentage for that weakness class. OT0 rows decode to `100` normal, `130` weakness, `50` resist, `0` null, `-100` absorb, plus intermediate states `90`, `85`, `80`, `75`, and `1`.


#### Runtime meaning
- `ResistType` is a damage-rate class, not the skill-side weakness-replacement table. `EnemyWeakID` stores `ResistType` IDs per weapon/magic slot; skill weakness replacement uses `SkillAvailID.m_ResistID` to point at an `EnemyWeakID` row.
- Negative `m_DamageRate` values mean absorb-style behavior in the weakness grid family. Do not reuse those IDs as generic debuff percentages.
- Because the row is a class table, changing a `ResistType` row changes every enemy weakness package that references that class. For custom skills, prefer creating/changing the `EnemyWeakID` package used by `m_ResistID` rather than modifying global resist classes.

### `SkillCategory`
Category legend table. The actual category number lives on `SkillID.m_Category`; this table adds one category-level boolean that the runtime can query.

- **OT0 rows:** `9`.
- **CotC rows:** `9`.
- **OT0 SDK row struct:** `FSkillCategoryBase`.
- **Related OT0 SDK enums:** `ESKILL_CATEGORY`.

#### Exact OT0 SDK enum quote used by this table
##### `ESKILL_CATEGORY`
Quoted from `SDK/Kingship_structs.hpp:6344`.
```cpp
enum class ESKILL_CATEGORY : uint8
{
	NONE                                     = 0,
	COMMAND                                  = 1,
	SUPPORT                                  = 2,
	WEAPON                                   = 3,
	ENEMYACTION                              = 4,
	ENEMYPASSIVE                             = 5,
	ENEMYEVENT                               = 6,
	MAXBOOST_COMMAND                         = 7,
	ITEM                                     = 8,
	ESKILL_MAX                               = 9,
};
```
- `SkillID.m_Category` stores the raw `ESKILL_CATEGORY` number from the SDK enum above. `SkillCategory.json` exists because OT0 wants one extra per-category data flag (`m_BuffDebuffLimit`) layered on top of that enum.
- In other words, `SkillCategory` is not an unrelated foreign table; it is the data-backed legend for the exact engine category constants quoted above.
- CotC Global 3.22.10 still exposes the same enum family, but value `8` is `SHIELD_ABILITY` there rather than OT0 `ITEM`. That is one of the places where same-width enums stop being safely portable by raw integer alone.

#### Field-by-field
- `m_id`: **Confirmed.** Direct category key. OT0 rows align to `ESKILL_CATEGORY` (`COMMAND`, `SUPPORT`, `ENEMYACTION`, `ITEM`, and so on).
- `m_BuffDebuffLimit`: **Observed.** Category-level boolean for buff/debuff-cap behavior. In OT0 it is true for category IDs `1`, `3`, and `4` and false elsewhere, so do not assume all categories participate in the same limit rules.


#### Runtime meaning
- `SkillCategory` is a category-level legend; the live category on a skill is `SkillID.m_Category`.
- Native helpers use category as an early classifier for item skills, support skills, enemy skills, max-boost commands, and command availability. Category mistakes can make an otherwise valid payload appear in the wrong menu, be skipped by support scanners, or be treated as an item/enemy action.
- `m_BuffDebuffLimit` should be read as a category policy bit. It is not a replacement for ailment-level limit fields, and it should not be copied onto a skill row because it belongs to the category table.

### `SkillCalcType`
Pure enum legend table. OT0 keeps `Skill/SkillCalcType` with only `m_id`, which exactly mirrors the calculation-mode enum used by `SkillAvailID.m_CalcType`.

- **OT0 rows:** `10`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** no matching dedicated row struct was found in the OT0 SDK dump shipped in this repo.
- **Related OT0 SDK enums:** `ESKILL_CALC_TYPE`.
- No dedicated `FSkillCalcTypeBase` row struct was found in the OT0 SDK dump here, but the OT0 JSON is still a clean legend table.

#### Exact OT0 SDK enum quote used by this table
##### `ESKILL_CALC_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6327`.
```cpp
enum class ESKILL_CALC_TYPE : uint8
{
	NONE                                     = 0,
	WEAPON                                   = 1,
	MAGIC                                    = 2,
	ADD_RATED                                = 3,
	ADD_FIXED                                = 4,
	ADD_NOW_RATED                            = 5,
	LAST_1                                   = 6,
	FEAR_LAST_1                              = 7,
	BP_SPECIAL_REGEN_RATE                    = 8,
	MAGIC_REFLECTION                         = 9,
	ESKILL_CALC_MAX                          = 10,
};
```
- `SkillAvailID.m_CalcType` stores these raw numbers directly. `SkillCalcType` is therefore best read as the exported legend copy of the SDK enum rather than as an independent gameplay table.
- This also explains why the table has only `m_id`: the semantic names live in the SDK enum, while the JSON keeps the numeric domain visible in the data export.
- CotC Global 3.22.10 keeps the same family but diverges at the tail: OT0 value `9` is `MAGIC_REFLECTION`, while CotC Global 3.22.10 value `9` is `HP_RECOVER_OVER_FIXED`.

The generic scalar helper is narrow and exact in the native code. `AQPBattleManager::CalcValue` handles only `ADD_RATED`, `ADD_FIXED`, `ADD_NOW_RATED`, and `LAST_1` itself, using `AQPBattleCharacterBase::s_DefaultParameterRatioInt` as the percentage denominator (`Octopath_Traveler0-Win64-Shipping.exe.c`:17466284-17466308):

```text
ADD_RATED      => Value + floor(Param * Max / 100)
ADD_FIXED      => Value + Param
ADD_NOW_RATED  => Value + floor(Param * Value / 100)
LAST_1         => 1 - Value
```

`WEAPON`, `MAGIC`, `FEAR_LAST_1`, `BP_SPECIAL_REGEN_RATE`, and `MAGIC_REFLECTION` are not implemented as generic `CalcValue` cases. They are handled by the higher-level battle paths that know the attacker, target, skill attribute, fear state, reflection source, or gauge context. In ordinary damage calculation, `WEAPON` selects the physical attack/defense lanes and `MAGIC` selects the elemental attack/defense lanes before formula-specific percentage and ailment adjustments are applied.

#### Field-by-field
- `m_id`: **Confirmed.** Calculation-mode key matching `ESKILL_CALC_TYPE` (`NONE`, `WEAPON`, `MAGIC`, `ADD_RATED`, `ADD_FIXED`, `ADD_NOW_RATED`, `LAST_1`, `FEAR_LAST_1`, `BP_SPECIAL_REGEN_RATE`, `MAGIC_REFLECTION`).


#### Runtime meaning
- `SkillCalcType` is the legend for `SkillAvailID.m_CalcType` and for the parallel `SkillAvailID.m_CalcTypeAilment` lanes. It is not the same enum as `SkillAilmentType.m_AilmentCalc`.
- `WEAPON` and `MAGIC` route into damage-style formulas; `ADD_RATED`, `ADD_FIXED`, and `ADD_NOW_RATED` route into active ailment value lanes; `BP_SPECIAL_REGEN_RATE` is a special-skill gauge scaling family; `MAGIC_REFLECTION` is OT0-specific at value `9` and should not be ported from CotC's value-9 meaning.
- For authoring, treat `m_CalcType` as the interpretation key for `m_Values`, while `m_ModifyType` chooses the operation. Both are needed: the same numeric value can mean power, percent, fixed amount, duration payload, or a no-op depending on the opcode.

### `SkillID`
Root skill row. If you change only one table when cloning a skill, this is the table you will inspect first, but it is almost never the only table you must edit.

- **OT0 rows:** `7336` total in the v1.0.8 export (`7335` nonzero IDs plus one zero/sentinel row).
- **CotC rows:** `23085`.
- **OT0 SDK row struct:** `FSkillIDBase`.
- **Related OT0 SDK enums:** `ESKILL_CATEGORY`, `ESKILL_RECOMEND_CATEGORY`.
- OT0 usage is heavily skewed toward enemy-side rows: category `ENEMYACTION` alone accounts for most rows. When making player content, clone a row from the correct player-facing family instead of picking an arbitrary `SkillID` row.

#### Exact OT0 SDK enum quote used directly by this row
##### `ESKILL_RECOMEND_CATEGORY`
Quoted from `SDK/Kingship_structs.hpp:6655`.
```cpp
enum class ESKILL_RECOMEND_CATEGORY : uint8
{
	NONE                                     = 0,
	ATTACK                                   = 1,
	SUPPORT                                  = 2,
	HEAL                                     = 3,
	NUM                                      = 4,
	ESKILL_RECOMEND_MAX                      = 5,
};
```
- `SkillID.m_Category` should be read together with the exact `ESKILL_CATEGORY` quote embedded in the preceding `SkillCategory` section, because the skill row stores that raw enum value and `SkillCategory` is its companion legend table.
- `SkillID.m_RecommendCategory` does **not** have a parallel `Skill/` legend file. The exact SDK enum above is therefore the authoritative naming source for values such as `ATTACK`, `SUPPORT`, and `HEAL`.
- CotC Global 3.22.10 keeps `ESKILL_RECOMEND_CATEGORY` stable, so this is one of the few skill enums whose raw values remain straightforwardly comparable between titles.

#### Field-by-field
- `m_id`: Primary key for the skill row.
- `m_Name`: **Proven.** Skill name text ID. Most OT0 rows resolve through `GameText/Localize/*/SystemText/GameTextSkill.json`, but a smaller subset resolves through `GameTextUI` instead. Use localized text banks when verifying visible names.
- `m_Detail`: **Proven.** Skill detail/description text ID. OT0 overwhelmingly resolves this through `GameTextSkill`, not `GameTextUI`.
- `m_Job`: **Proven.** `Character/JobID.m_id` when set. OT0 only uses the eight base job IDs here, and only on `44` rows, so this is **not** a universal owner-job field.
- `m_BoostSkills`: **Proven.** Fixed-length array of four `SkillID` rows for the boost family `[boost0, boost1, boost2, boost3]`. In OT0 only the lead rows of boostable families usually populate it.
- `m_Category`: **Proven.** Skill category from `ESKILL_CATEGORY`. This is the first field you should inspect to separate player commands, support skills, enemy actions, enemy passives, and item skills.
- `m_RequireRatio`: **Confirmed unused in OT0.** Present in the SDK struct but always zero/false in OT0 skill rows.
- `m_RequireValue`: **Proven.** SP Cost. For non-command skills the meaning depends on category and caller.
- `m_CountLimit`: **Proven; unused in OT0.** Usage limit of the skill. Present but always zero.
- `m_UnableRecoverCount`: **Confirmed unused in OT0.** Present but always zero/false.
- `m_RequireBoost`: **Proven; unused in OT0.** Minimum boost level required to execute skill. Present but always zero.
- `m_WeaponIconType`: **Proven.** Direct `WeaponType.m_id` for the weapon icon overlay shown with the skill.
- `m_MagicIconType`: **Proven.** Direct `MagicType.m_id` for the element icon overlay shown with the skill.
- `m_IsRandomReplace`: **Observed.** Random-variant replacement flag. OT0 uses it on a handful of random replacement families such as `Prism Mist`-style skills.
- `m_IsWeaponSelect`: **Observed.** Weapon-form selection flag. Used when one visible command can resolve to different concrete skill rows depending on weapon choice or weapon-context routing (i.e. selecting a weapon to execute the skill with). 
- `m_IsReplaceNowSelectedWeapon`: **Observed.** Replacement should use the currently selected weapon form rather than a fixed family member.
- `m_IsReplaceMainWeapon`: **Observed.** Similar to the previous field, but biased to the main weapon set. Rare in OT0.
- `m_WeaponReplaceSkill`: **Proven.** Fixed-length pool of eight replacement `SkillID` rows. Despite the name, OT0 uses it both for true weapon-form replacement and for some random-variant families.
- `m_Avails`: **Proven.** Fixed-length array of up to twenty `SkillAvailID` stages. This is the gameplay sequence for the skill. Zero slots are simply skipped.
- `m_BeginEffective`: **Proven.** Optional pre-cast `SkillEffectiveID` stage.
- `m_Effectives`: **Proven.** Fixed-length array of up to twenty `SkillEffectiveID` stages. This is the presentation sequence for the skill, each avail is assigned one effect.
- `m_EndEffective`: **Confirmed; unused in OT0.** Post-cast `SkillEffectiveID` hook present in the struct but never used in OT0 rows.
- `m_AttachEffect`: **Confirmed.** `Effect/AttachEffect.m_id`. OT0 only uses this on a minority of rows.
- `m_SkillIconID`: **Proven.** Direct `Texture/TextureID.m_id`. This is the main player-facing command icon field in OT0, **not** `SkillIconList` rows.
- `m_SkillUniqueIconID`: **Confirmed.** `Skill/SkillUniqueIconID.m_id`. Present in the schema but unused by OT0 `SkillID` rows.
- `m_CommandPhaseSkillIconID`: **No live OT0 reader recovered.** Reserved alternate icon surface for command-phase systems; keep it bounded as schema-present unless a stronger reader is recovered.
- `m_OverrideCommandName`: **Runtime-proven.** `USkillDB::GetSkillNameID(..., GetOverrideName=true)` returns this text ID instead of `m_Name` when the override is positive.
- `m_CommandCategory`: **No strong live OT0 reader recovered.** Rare schema/UI-looking helper. Only a few OT0 rows populate it, so do not rely on it as a battle category gate.
- `m_BlockedSkill`: **Runtime-proven.** `USkillDB::GetBlockedSkillID` reads this field and skill-list construction uses it as a mutually exclusive/blocking skill hook.
- `m_ReplaceCondition`: **Runtime-proven.** Scalar condition checked by `GetConditionReplaceSkill` before returning `m_ReplaceSkill`.
- `m_ReplaceConditionArray`: **Runtime-proven.** Fallback condition array scanned by `GetConditionReplaceSkill`, matched by index to `m_ReplaceSkillArray`.
- `m_ReplaceSkill`: **Runtime-proven.** Scalar replacement target, and in this decompilation also a positive gate for the array-replacement path.
- `m_ReplaceSkillArray`: **Runtime-proven.** Replacement target array used when the corresponding replacement-condition array entry passes.
- `m_DisableReplaceTag`: **Observed.** Suppresses some replacement-tagging logic or replacement-related UI labeling. OT0 leaves it false everywhere.
- `m_SpecialSkillInitialGaugeValue`: **No live OT0 reader recovered.** Gauge-seeding-looking field; unlike `m_SpecialSkillRegenerateValue`, the checked C-export call paths do not expose a convincing gameplay reader.
- `m_SpecialSkillRegenerateValue`: **Runtime-proven.** `USkillDB::GetSpecialSkillRegenerateValue` reads this field, and `BP_SPECIAL_REGEN_RATE` scales the resolved value.
- `m_RecommendCategory`: **Confirmed.** Recommendation bucket from `ESKILL_RECOMEND_CATEGORY` (`ATTACK`, `SUPPORT`, `HEAL`). OT0 only uses this on a small subset of rows.
- `m_DuplicateAilment`: **Runtime-proven.** `SetupAilment` changes duplicate/use-info identity when either this skill-row flag or the ailment-row duplicate flag is set.
- `m_AfterAction`: **Observed in OT0; enum-backed by CotC runtime.** Rare follow-up action code. OT0 only uses it on five rows. CotC Global still names the family `ESKILL_AFTER_ACTION` with `NONE = 0`, `CHANGE = 1`, `CHANGE_WITH_EFFECT = 2`, and `SELF_DESTRUCTION = 3`. OT0's live rows only use value `1`, so the strongest current reading is a post-action change/row-move opcode rather than an arbitrary unknown flag.
- `m_InitialChargeTrun`: **Runtime-proven.** Typo preserved by the asset. `USkillDB::IsSkillChargeTurnType` outputs it as the initial command-phase ability turn when recharge is positive.
- `m_ReChargeTrun`: **Runtime-proven.** `USkillDB::IsSkillChargeTurnType` gates on this field being positive and outputs it as the recharge turn.
- `m_MaxBoostLv`: **Confirmed.** Maximum boost level. OT0 sets this to `3` on every `SkillID` row. Higher values may require edits to `BattleParams` too.
- `m_FieldUseable`: **Confirmed.** Marks the skill as usable in field context. OT0 only sets this on twenty rows, such as field revive, field healing skills, etc.
- `m_VoiceID`: **Confirmed.** Direct `Sound/PartVoiceID.m_id` for the skill's voice callout.


#### Runtime meaning by field group
- **Identity fields** (`m_id`, `m_Name`, `m_Detail`, `m_Job`) are mostly lookup and presentation fields. Only `m_id` is the behavior key; text labels do not carry behavior.
- **Boost and replacement fields** (`m_BoostSkills`, `m_WeaponReplaceSkill`, `m_ReplaceCondition`, `m_ReplaceSkill`, and their arrays) are resolved before many UI and battle readers inspect the final skill. Do not clone only the visible skill row; clone or retarget the boosted/replaced rows too.
- **Availability fields** (`m_Category`, `m_RequireValue`, `m_RequireBoost`, `m_CountLimit`, `m_FieldUseable`) are consumed by different callers. `m_FieldUseable` is field-menu permission, not battle permission; `m_RequireValue` is SP cost for ordinary command contexts, but non-command categories can give it context-specific meaning.
- **Gameplay-stage fields** (`m_Avails`) are the real execution sequence. Each positive avail slot can change gameplay state. A skill with a correct name/icon but stale avails is only a reskinned old skill.
- **Presentation-stage fields** (`m_BeginEffective`, `m_Effectives`, `m_EndEffective`, `m_AttachEffect`, `m_VoiceID`) drive camera/action/effect/text/voice behavior. Gameplay can occur even with weak presentation data, but telop, timing, and actor visibility can be wrong.
- **Mutual-exclusion fields** (`m_BlockedSkill`) are read when building skill lists. A clone can disappear or suppress another command if this still points to the original blocked family.
- **Ailment-policy fields** (`m_DuplicateAilment`) affect active ailment identity and reapplication behavior. This is root-skill policy layered on top of the ailment row's own duplicate/use-info fields.

<details>
<summary>Evidence: root skill fields are resolved before execution</summary>

```cpp
// Simplified from OT0 C-export call paths.
// Root skill data is normalized through boost/replacement helpers before
// availability and execution readers inspect the concrete avail/effective rows.
SkillID
  -> m_BoostSkills[boost]
  -> conditional replacement / weapon replacement / blocked-skill checks
  -> m_Avails[] for gameplay stages
  -> m_Effectives[] for presentation stages
```

</details>

#### Concrete OT0 examples
- `m_FieldUseable`: `SkillID#607665 First Aid`, `#900556 Healing Light`, `#902332 Vivify`, and `#902114 Revive` all set this flag.
- `m_AfterAction`: `SkillID#900012 Retreating Strike`, `#900042 Lunar Arc`, `#900100 Expert First Aid`, and `#900563/#901483 Song of Battle` all set `m_AfterAction = 1`; their visible descriptions also explicitly end with "then move to back row." That is strong evidence that the field is tied to forced post-action row movement, and it lines up exactly with CotC's `ESKILL_AFTER_ACTION::CHANGE = 1`.
- `m_RecommendCategory`: restorative rows such as `SkillID#902332 Vivify`, `#901894 Arcane Healing`, `#902108 Heal Wounds`, and `#902114 Revive` use `m_RecommendCategory = 3`, which matches `ESKILL_RECOMEND_CATEGORY::HEAL` exactly.

#### CotC-only extra fields
- CotC 4.2.3 header-confirmed: `SkillIDBase` directly carries the whole current late helper block from `m_DispBoostLv` through `m_CmdPhaseTurnResetOnContinue` (`libUE4.so.h`:60068-60123). Older notes that only had these as schema or runtime-string evidence should be read as weaker historical evidence, not absence from current CotC.
- `m_DispBoostLv`: **Confirmed from CotC 4.2.3 reflected header.** UI flag for explicitly displaying boost level.
- `m_IsBasicAbility`: **Confirmed from CotC 4.2.3 reflected header.** Marks the row as a basic ability rather than a learned/special one.
- `m_NoChangeBoostType`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Prevents replacement or conversion logic from changing the skill's boost family/type.
- `m_Boost246`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** CotC-specific boost-pattern flag for skills that do not use the ordinary OT0-style `0/1/2/3` family presentation.
- `m_ForcedSkillFilter`: **Confirmed from CotC 4.2.3 reflected header, schema, and ID overlap.** Array of `SkillFilterType.m_id` rows that forcibly include the skill in specific filter buckets.
- `m_ExclusionSkillFilter`: **Confirmed from CotC 4.2.3 reflected header, schema, and ID overlap.** Array of `SkillFilterType.m_id` rows that forcibly exclude the skill from specific filter buckets.
- `m_ForcedCategory`: **Confirmed from CotC 4.2.3 reflected header, schema, and ID overlap.** `SkillFilterCategory.m_id` forced on the skill for CotC filter UI.
- `m_NotUsedAutoBattle`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Auto-battle exclusion flag.
- `m_CmdPhaseTurnResetOnContinue`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Command-phase state reset when the skill continues into another phase or turn.

#### OT0 usage notes
- `Row-count bias`: OT0 v1.0.8 has `7336` total `SkillID` rows (`7335` nonzero), but only `1614` nonzero rows are category `COMMAND`. Enemy content dominates the file.
- `Array sizes`: OT0 uses fixed sizes: `m_BoostSkills` length `4`, `m_Avails` length `20`, `m_Effectives` length `20`, `m_WeaponReplaceSkill` length `8`.
- `True rarity`: OT0 only sets `m_IsRandomReplace` on `5` rows, `m_IsWeaponSelect` on `23`, `m_IsReplaceMainWeapon` on `4`, `m_AfterAction` on `5`, and `m_FieldUseable` on `20`.

### `SkillAvailID`
Gameplay payload row. If `SkillID` tells you *what skill family this is*, `SkillAvailID` tells you *what the stage actually does in battle or field logic*.

- **OT0 rows:** `7369`.
- **CotC rows:** `28023`.
- **OT0 SDK row struct:** `FSkillAvailIDBase`.
- **Related OT0 SDK enums:** `Kingship::ESKILL_INVOKE`, `ESKILL_TARGET_TYPE`, `ESKILL_MODIFY_TYPE`, and `ESKILL_CALC_TYPE`. `SkillAvailID` also points into `SkillAilmentType`, whose own `m_AilmentCalc` uses `ESKILL_AILMENT_CALC_TYPE`.
- For OT0 reverse engineering, `m_ModifyType` is the single most important field in the entire row. It determines how most of the numeric payload arrays should be read.

#### Exact OT0 SDK enum quotes that directly drive this row
##### `Kingship::ESKILL_INVOKE` (context enum)
Quoted from `SDK/Kingship_structs.hpp:6360`.
```cpp
enum class Kingship::ESKILL_INVOKE : uint8
{
	eNONE                                    = 0,
	ePASSIVE                                 = 1,
	eBATTLE                                  = 2,
	eFIELD                                   = 3,
	SKILL_MAX                                = 4,
};
```
- The SDK dump exposes two different `Kingship::ESKILL_INVOKE` enum blocks with the same printed name. The context enum above describes the broader passive/battle/field invocation domain.

##### `Kingship::ESKILL_INVOKE` (timing enum)
Quoted from `SDK/Kingship_structs.hpp:6436`.
```cpp
enum class Kingship::ESKILL_INVOKE : uint8
{
	NONE                                     = 0,
	COMMAND                                  = 1,
	BATTLING                                 = 2,
	BATTLE_1ST                               = 3,
	BATTLE_END                               = 4,
	TURN_1ST                                 = 5,
	TURN_END                                 = 6,
	ACTION_1ST                               = 7,
	ACTION_BEGIN                             = 8,
	ACTION_DAMAGE                            = 9,
	ACTION_FINISH                            = 10,
	ACTION_LAST                              = 11,
	CHANGE_FWD                               = 12,
	CHANGE_BAK                               = 13,
	FORWARD                                  = 14,
	BACKUP                                   = 15,
	JUST_BREAK                               = 16,
	SLIP_DAMAGE                              = 17,
	GUARDING                                 = 18,
	TURN_BEGIN                               = 19,
	TURN_FINISH                              = 20,
	BEFORE_COMMAND_SELECTION                 = 21,
	BATTLING_SLIP_DAMAGE                     = 22,
	BATTLETURN_MODECHANGE                    = 23,
	FORWARD_AND_BACKUP                       = 24,
	COMMAND_PHASE                            = 25,
	ON_BREAKED                               = 26,
	BUFF_LIMIT_CALCULATING                   = 27,
	JUST_DYING                               = 28,
	ANY_CHARA_ACTION_LAST                    = 29,
	FIELD_ACTION                             = 50,
	FIELDING                                 = 51,
	ALWAYS                                   = 100,
	ESKILL_MAX                               = 101,
};
```
- `SkillAvailID.m_InvokeTiming` uses the timing enum above, not the earlier context enum. This is why live OT0 rows contain values such as `COMMAND`, `ACTION_DAMAGE`, `FIELD_ACTION`, and `ALWAYS`.
- CotC Global 3.22.10 still agrees on the core timing range, but it repurposes OT0 values `28` and `29` and then extends the tail with additional values such as `ACTION_DAMAGE_CRITICAL`, `MOVED_FWD`, `MOVED_BAK`, `IGNITION_TURN`, `JOB_CHECK`, `DEBUFF_LIMIT_CALCULATING`, and `ON_DEAD`.

##### `ESKILL_TARGET_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6683`.
```cpp
enum class ESKILL_TARGET_TYPE : uint8
{
	NONE                                     = 0,
	ENEMY_1                                  = 1,
	ENEMY_ALL                                = 2,
	ENEMY_RND                                = 3,
	ENEMY_ALL_RND                            = 4,
	SELF                                     = 5,
	FRIENDLY_1                               = 6,
	FRIENDLY_ALL                             = 7,
	FRIENDLY_RND                             = 8,
	FRIENDLY_RND_WITHME                      = 9,
	FRIENDLY_ALL_RND                         = 10,
	FRIENDLY_ALL_RND_WITHME                  = 11,
	FRIENDLY_1_DEAD                          = 12,
	FRIENDLY_ALL_DEAD                        = 13,
	CONTINUE                                 = 14,
	FORWARDS                                 = 15,
	BACKUPS                                  = 16,
	BUDDY                                    = 17,
	PAIR                                     = 18,
	ALL                                      = 19,
	FRIENDLY_EVERYONE                        = 20,
	FRIENDLY_EVERYONE_DEAD                   = 21,
	FRIENDLY_1_WITHOUTME                     = 22,
	BACKUPS_DEAD                             = 23,
	FRIENDRY_ALL_RERAISEABLE                 = 24,
	FRIENDLY_1_ANY                           = 25,
	FRIENDLY_ALL_WITHOUTME                   = 26,
	FRIENDLY_1_HAS_SPSKILL                   = 27,
	FRIENDLY_EVERYONE_WITHOUTME              = 28,
	BACKUPS_ALIVE                            = 29,
	FRIENDLY_1_EVERYONE                      = 30,
	AI_CONTINUE                              = 50,
	AI_SELF                                  = 51,
	AI_ENEMY_RND                             = 52,
	AI_ENEMY_ALL                             = 53,
	AI_ENEMY_MIN_HP                          = 54,
	AI_ENEMY_MAX_HP                          = 55,
	AI_ENEMY_MIN_SP                          = 56,
	AI_ENEMY_MAX_SP                          = 57,
	AI_ENEMY_MIN_BP                          = 58,
	AI_ENEMY_MAX_BP                          = 59,
	AI_ENEMY_MIN_SLD                         = 60,
	AI_ENEMY_MAX_SLD                         = 61,
	AI_FRIENDLY_RND                          = 62,
	AI_FRIENDLY_RND_WITHME                   = 63,
	AI_FRIENDLY_ALL_RND                      = 64,
	AI_FRIENDLY_ALL_RND_WITHME               = 65,
	AI_FRIENDLY_MIN_HP                       = 66,
	AI_FRIENDLY_MAX_HP                       = 67,
	AI_FRIENDLY_MIN_SP                       = 68,
	AI_FRIENDLY_MAX_SP                       = 69,
	AI_FRIENDLY_MIN_BP                       = 70,
	AI_FRIENDLY_MAX_BP                       = 71,
	AI_FRIENDLY_MIN_SLD                      = 72,
	AI_FRIENDLY_MAX_SLD                      = 73,
	ENEMY_1_BUFFED                           = 150,
	ENEMY_1_DEBUFFED                         = 151,
	ENEMY_ALL_BUFFED                         = 152,
	ENEMY_ALL_DEBUFFED                       = 153,
	ENEMY_1_B_PATK                           = 154,
	ENEMY_1_B_MATK                           = 155,
	ENEMY_1_B_PDEF                           = 156,
	ENEMY_1_B_MDEF                           = 157,
	ENEMY_1_B_AGI                            = 158,
	ENEMY_1_B_CRT                            = 159,
	ENEMY_1_D_PATK                           = 160,
	ENEMY_1_D_MATK                           = 161,
	ENEMY_1_D_PDEF                           = 162,
	ENEMY_1_D_MDEF                           = 163,
	ENEMY_1_D_AGI                            = 164,
	ENEMY_1_D_CRT                            = 165,
	ENEMY_1_B_SWORD                          = 166,
	ENEMY_1_B_AXE                            = 167,
	ENEMY_1_B_DAGGER                         = 168,
	ENEMY_1_B_PAPER                          = 169,
	ENEMY_1_B_ROD                            = 170,
	ENEMY_1_B_BOW                            = 171,
	ENEMY_1_B_LANCE                          = 172,
	ENEMY_1_B_FAN                            = 173,
	ENEMY_1_B_FIRE                           = 174,
	ENEMY_1_B_ICE                            = 175,
	ENEMY_1_B_THUNDER                        = 176,
	ENEMY_1_B_WIND                           = 177,
	ENEMY_1_B_LIGHT                          = 178,
	ENEMY_1_B_DARK                           = 179,
	ENEMY_1_B_NAUGHT                         = 180,
	ENEMY_1_D_SWORD                          = 181,
	ENEMY_1_D_AXE                            = 182,
	ENEMY_1_D_DAGGER                         = 183,
	ENEMY_1_D_PAPER                          = 184,
	ENEMY_1_D_ROD                            = 185,
	ENEMY_1_D_BOW                            = 186,
	ENEMY_1_D_LANCE                          = 187,
	ENEMY_1_D_FAN                            = 188,
	ENEMY_1_D_FIRE                           = 189,
	ENEMY_1_D_ICE                            = 190,
	ENEMY_1_D_THUNDER                        = 191,
	ENEMY_1_D_WIND                           = 192,
	ENEMY_1_D_LIGHT                          = 193,
	ENEMY_1_D_DARK                           = 194,
	ENEMY_1_D_NAUGHT                         = 195,
	ENEMY_1_POISON                           = 196,
	ENEMY_1_BLEED                            = 197,
	ENEMY_1_SILENCE                          = 198,
	ENEMY_1_SLEEP                            = 199,
	ENEMY_1_PARALYSYS                        = 200,
	ENEMY_1_FEAR                             = 201,
	ENEMY_1_WASTE                            = 202,
	ENEMY_1_HEADACHE                         = 203,
	ENEMY_1_ENERGYDRAIN                      = 204,
	ENEMY_1_COUNTDOWN                        = 205,
	ENEMY_1_PROVOKE                          = 206,
	ENEMY_1_REGENERATE                       = 207,
	ENEMY_1_SP_REGENERATE                    = 208,
	ENEMY_1_BP_REPAIR                        = 209,
	ENEMY_1_SHIELD_REPAIR                    = 210,
	ENEMY_1_ROBUST                           = 211,
	ENEMY_1_COUNTER                          = 212,
	ENEMY_1_INTERCEPT                        = 213,
	FORWARDS_AND_BACKUPS                     = 214,
	FRIENDLY_ALL_NOALIVECHECK                = 215,
	FRIENDLY_MIN_HP                          = 216,
	ENEMY_BACKUPS_RND                        = 217,
	FORWARDS_RND_SIDE_BY_SIDE                = 218,
	BACKUPS_RND_SIDE_BY_SIDE                 = 219,
	PAIR_RND                                 = 220,
	PAIR_RND_SIDE_BY_SIDE                    = 221,
	ENEMY_HP_MAX_ORDER                       = 222,
	ENEMY_FORWARDS_HP_MAX_ORDER              = 223,
	FORWARDS_OR_BACKUPS                      = 224,
	FRIENDLY_1_WITHOUTME_ALL                 = 225,
	FORWARDS_OR_BACKUPS_ALIVE                = 226,
	CONTINUE_NOSELF_OR_ENEMY1                = 227,
	CONTINUE_NOSELF_FRIEND                   = 228,
	LAST_RECOVER                             = 229,
	ESKILL_TARGET_MAX                        = 230,
};
```

##### `ESKILL_MODIFY_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6476`.
```cpp
enum class ESKILL_MODIFY_TYPE : uint8
{
	NONE                                     = 0,
	CHARACTER                                = 1,
	STEAL_CURE                               = 2,
	FORCE_CHANGE                             = 3,
	WEAK_CHANGE                              = 4,
	ESCAPE                                   = 5,
	BOOST_LEVEL                              = 6,
	REVIVE                                   = 7,
	CALL_VISITOR                             = 8,
	SHORTEN_TURN_MINUS                       = 9,
	SHORTEN_TURN_DEBUFF                      = 10,
	SHORTEN_TURN_AILMENT                     = 11,
	GUARD                                    = 12,
	RERAISE                                  = 13,
	COUNT_RECOVER_ONE                        = 14,
	COUNT_RECOVER_ALL                        = 15,
	COUNT_RECOVER_SP_SKILL                   = 16,
	DECREASE_AILMENT_COUNT                   = 17,
	MAGIC_STONE_GAUGE                        = 18,
	INTENSIVE                                = 19,
	GUEST_CALLABLE_COUNT                     = 20,
	SHIELD_ONLY                              = 21,
	STEAL_ITEM                               = 22,
	STEAL_LEAF                               = 23,
	DETECT_HP                                = 24,
	STEAL_JP                                 = 25,
	PASS_SP                                  = 26,
	BUFF_EAT                                 = 27,
	SUCTION_LEAF                             = 28,
	DETECT_ITEM                              = 29,
	SP_BURST                                 = 30,
	BUFF_COPY                                = 31,
	ADD_TURN_PLUS_AILMENT                    = 32,
	ADD_TURN_MINUS_AILMENT                   = 33,
	MAKE_LEAVE_FROM_BATTLE                   = 34,
	PASS_SSP                                 = 35,
	REINFORCEMENT_PARTS                      = 36,
	RESULT_EXP                               = 100,
	RESULT_MONEY                             = 101,
	RESULT_ITEM                              = 102,
	AILMENT_RATE                             = 103,
	STEAL_CURE_RATE                          = 104,
	ADDTURN_BUFF_CAST                        = 105,
	ADDTURN_DEBUFF_CAST                      = 106,
	ADDTURN_BUFF_RECEIVE                     = 107,
	ADDTURN_DEBUFF_RECEIVE                   = 108,
	ADDTURN_M_AILMENT_CAST                   = 109,
	ADD_INFLUENCE                            = 110,
	ADD_STATUS_ALL                           = 111,
	ENHANCED_RATE                            = 112,
	ADDTURN_PLUS_CAST                        = 113,
	ADDTURN_MINUS_CAST                       = 114,
	ADDTURN_PLUS_RECEIVE                     = 115,
	ADDTURN_MINUS_RECEIVE                    = 116,
	GUARD_UPGRADE                            = 117,
	RESULT_RECOVER_STATUS                    = 118,
	RESULT_JP                                = 119,
	DETECT_WEAK                              = 120,
	ATTACKED_AILMENT                         = 121,
	DAMAGED_AILMENT                          = 122,
	DAMAGED_AILMENT_SWORD                    = 123,
	DAMAGED_AILMENT_LANCE                    = 124,
	DAMAGED_AILMENT_DAGGER                   = 125,
	DAMAGED_AILMENT_AXE                      = 126,
	DAMAGED_AILMENT_BOW                      = 127,
	DAMAGED_AILMENT_ROD                      = 128,
	DAMAGED_AILMENT_PAPERS                   = 129,
	DAMAGED_AILMENT_FAN                      = 130,
	ATTACK_CURE                              = 131,
	AUTOMATIC_CURE                           = 132,
	DAMAGED_CURE                             = 133,
	GUTS                                     = 134,
	ATTACK_AND_SKILL                         = 135,
	ATTACK_AND_SKILL_SWORD                   = 136,
	ATTACK_AND_SKILL_LANCE                   = 137,
	ATTACK_AND_SKILL_DAGGER                  = 138,
	ATTACK_AND_SKILL_AXE                     = 139,
	ATTACK_AND_SKILL_BOW                     = 140,
	ATTACK_AND_SKILL_ROD                     = 141,
	ATTACK_AND_SKILL_PAPERS                  = 142,
	ATTACK_AND_SKILL_FAN                     = 143,
	SKILL_AND_SKILL                          = 144,
	INCIDENTALLY_SKILL                       = 145,
	BREAKED_AFTER_SKILL                      = 146,
	SACRIFICE                                = 147,
	REPEAT_WEAPON_ATTACK                     = 148,
	REPEAT_MAGIC_ATTACK                      = 149,
	REPEAT_ATTACK                            = 150,
	REPEAT_SKILL_ATTACK                      = 151,
	REPEAT_SKILL_ANY                         = 152,
	DAMAGE_AND_SKILL                         = 153,
	ENCOUNT_RATE                             = 154,
	SKILLBOARD_MAX_COST                      = 155,
	AVAIL_VALUES_INCREASE                    = 156,
	BP_RENT_BUDDY                            = 157,
	AILMENT_MEMOLIZE                         = 158,
	TURN_LAST_AND_SKILL                      = 159,
	REQUIRE_INVALID                          = 160,
	RESULT_DROP                              = 161,
	EQUIP_REVERSAL                           = 162,
	BREAK_ACT                                = 163,
	LAST_ACT                                 = 164,
	WEAK_AFTER_SKILL                         = 165,
	AVAIL_TYPE_AND_SKILL                     = 166,
	REFLECT_AILMENT                          = 167,
	PURSUIT_SKILL                            = 168,
	ESCAPE_RATE                              = 169,
	PLUS_AILMENT_MEMOLIZE                    = 170,
	BATTLE_1ST_ACT                           = 171,
	P_ATK_M_ATK_COPY                         = 172,
	P_ATK_M_ATK_CHANGE                       = 173,
	DAMAGE_LIMIT_BREAK                       = 174,
	DEBUFF_COUNT_ACCEL                       = 175,
	PRE_EMPTIVE_RATE_UP                      = 176,
	SUCKER_PUNCH_DISABLE                     = 177,
	DEATHBLOWPOINT_UP                        = 178,
	RARE_ENCOUNT_UP                          = 179,
	TRIBUTE                                  = 200,
	ITEM_SUPPLY                              = 201,
	RENAME_UNLOCK                            = 202,
	SHOP_TRADE_PLUS                          = 203,
	SHOP_TRADE_SPEED                         = 204,
	SHOP_TRADE_RARITY                        = 205,
	FARM_GROWTH_SPEEDUP                      = 206,
	FARM_HARVEST_ALL_ITEM_UP                 = 207,
	FARM_HARVEST_ITEM_UP                     = 208,
	FARM_HARVEST_ITEM_AMOUNT_LOTUP           = 209,
	SHOP_DISCOUNT                            = 210,
	SHOP_SELL_UP                             = 211,
	NO_COUNT_GUESTCALL                       = 212,
	RANCH_ITEM_AMOUNT_LOTUP                  = 213,
	RANCH_GROWTH_SPEEDUP                     = 214,
	RANCH_APPEND_ITEM                        = 215,
	ITEM_SUPPLY_SPEED_UP                     = 216,
	SHOP_ALCHEMY_ITEM_BONUS                  = 217,
	GUESTCALL_COUNT_UP                       = 218,
	VILLAGE_BONUS_TYPE_SUPPORTER_ENTRY_COUNT = 219,
	VILLAGE_BONUS_TYPE_BATTLE_EXP_UP         = 220,
	VILLAGE_BONUS_TYPE_FARM_SLOT_COUNT       = 221,
	VILLAGE_BONUS_TYPE_SUPPORTER_CALL_COUNT  = 222,
	VILLAGE_BONUS_TYPE_SHOP_TRADE_SPEED      = 223,
	VILLAGE_BONUS_TYPE_SHOP_TRADE_RARITY     = 224,
	VILLAGE_BONUS_TYPE_SHOP_TRADE_COUNT      = 225,
	VILLAGE_BONUS_TYPE_SALON_REDO_BODY       = 226,
	VILLAGE_BONUS_TYPE_SALON_REDO_FACE_AND_EYE = 227,
	VILLAGE_BONUS_TYPE_SALON_REDO_HAIR_AND_HAIR_COLOR = 228,
	VILLAGE_BONUS_TYPE_SALON_REDO_SKIN_COLOR = 229,
	VILLAGE_BONUS_TYPE_SALON_REDO_GESTURE    = 230,
	VILLAGE_BONUS_TYPE_SALON_REDO_VOICE      = 231,
	VILLAGE_BONUS_TYPE_HARVEST_STORAGE_ITEM  = 232,
	VILLAGE_BONUS_TYPE_SUPPLY_ITEM_STORAGE_ITEM = 233,
	VILLAGE_BONUS_TYPE_HARVEST_STORAGE_CAPITAL = 234,
	VILLAGE_BONUS_TYPE_SUPPLY_ITEM_STORAGE_CAPITAL = 235,
	VILLAGE_BONUS_TYPE_TRAINING_COUNT        = 236,
	VILLAGE_BONUS_TYPE_TRAINING_OPEN_SPECIAL = 237,
	VILLAGE_BONUS_TYPE_TRAINING_OPEN_ESSENTIAL = 238,
	TRAINING_EXP_UP                          = 239,
	TRAINING_JP_UP                           = 240,
	FARM_FERTILIZER                          = 241,
	RANCH_FEED                               = 242,
	RESULT_EXP_ZERO                          = 243,
	VILLAGE_BONUS_TYPE_TRAINING_BATTLE_EXP_RATE = 244,
	VILLAGE_BONUS_TYPE_TRAINING_BATTLE_JP_RATE = 245,
	VILLAGE_BONUS_RESIDENT_ADD_COUNT         = 246,
	FARM_SPECIAL_GROWTH_SPEEDUP              = 247,
	RANCH_SPECIAL_GROWTH_SPEEDUP             = 248,
	FARM_HARVEST_ITEM_FINAL_AMOUNT_LOTUP     = 249,
	RANCH_ITEM_FINAL_AMOUNT_LOTUP            = 250,
	SHOP_PRICE_HIKE                          = 251,
	ARENA_EXP_UP                             = 252,
	RECOVER_DISCOUNT                         = 253,
	ITEM_SUPPLY_LOT_IN_GROUP                 = 254,
	ESKILL_MODIFY_MAX                        = 255,
};
```
- `SkillAvailID.m_ModifyType` stores the raw numeric opcode from this enum, and that is the reason this single field dominates the interpretation of `m_Values`, `m_Turns`, `m_Counts`, `m_ModifyStatus`, reinforcement IDs, weakness-change IDs, shop/farm helper rows, and many other payloads.
- `SkillAvailID.m_CalcType` should be read together with the exact `ESKILL_CALC_TYPE` quote embedded above in `SkillCalcType`.
- `SkillAvailID.m_CalcTypeAilment` should be read as an `ESKILL_CALC_TYPE`-style value/storage selector for the same index in `m_AddAilment` / `m_ValueAilment`. Do not read it as `ESKILL_AILMENT_CALC_TYPE`; that ailment-damage enum belongs to `SkillAilmentType.m_AilmentCalc`.
- CotC Global 3.22.10 diverges from OT0 almost immediately here. Only values `0..3` still match by both name and meaning; value `4` is already `WEAK_CHANGE` in OT0 but `IMMEDIATELY_CHANGE` in CotC Global 3.22.10. Do **not** port raw modify integers across titles.

#### Field-by-field
- `m_id`: Primary key for the avail row.
- `m_InvokeTiming`: **Proven.** Timing enum from the richer `Kingship::ESKILL_INVOKE` block in the OT0 SDK dump. OT0 uses values such as `COMMAND`, `BATTLING`, `ACTION_DAMAGE`, `FIELD_ACTION`, and `ALWAYS`.
- `m_InvokeCondition`: **Proven.** `Skill/SkillConditionList.m_id`. The stage only resolves if this condition package passes.
- `m_TargetType`: **Proven.** Target selector from `ESKILL_TARGET_TYPE`. This is where you decide whether the stage asks for one enemy, all enemies, self, one dead ally, everyone, forward row, backup row, AI-selected lowest HP target, and so on.
- `m_ModifyType`: **Proven.** Main operation code from `ESKILL_MODIFY_TYPE`. `CHARACTER` is the general-purpose stat/damage/heal/buff/debuff path, while other values perform revive, weakness change, reinforcement, steal, village economy operations, and many more.
- `m_ModifyStatus`: **Proven.** `Character/CharaStatusID.m_id` when the modify opcode needs a concrete stat or resource selector. OT0 only uses seventeen unique status IDs here, and all of them match `CharaStatusID` cleanly.
- `m_AvailTag`: **Proven.** Seven-slot internal classification tag array. OT0 uses many recurring tuples. For instance, `16` tags the avail as magic-related, allowing it to be repeated with the Rechant ailment active
- `m_IsFoodAvail`: **Runtime-proven.** `USkillDB::IsAvailFood` reads it, and ailment setup/update paths use it to mark food-origin active state. Treat it as an origin/classification flag, not merely village metadata. 
- `m_HitTypes`: **Proven.** Array of up to four `SkillHitRateList.m_id` rows. These control multi-hit count distributions and related random-hit logic.
- `m_RandomChoiceCount`: **Observed.** Count of random choices/branches for opcodes that pick from multiple outcomes. Rare in OT0.
- `m_CalcType`: **Proven.** Calculation mode from `ESKILL_CALC_TYPE`. In OT0 the common values are `NONE`, `WEAPON`, `MAGIC`, `ADD_FIXED`, and `ADD_RATED`.
- `m_Values`: **Proven.** Main numeric payload array. Interpretation depends on `m_ModifyType` plus `m_CalcType`: damage power, heal amount, status delta, reward amount, or other opcode-specific value.
- `m_FluctuationValues`: **Observed.** Secondary variance payload. OT0 only uses nonzero values on three rows, so most skills ignore it.
- `m_SkillRatios`: **Observed but very strongly supported.** Per-boost ratio array. The common OT0 default is `(100,100,100,100)`, while many boostable attack families use progressive sets like `(100,180,260,340)` or `(100,200,300,400)`.
- `m_Turns`: **Proven.** Per-boost duration array. OT0 uses this for timed buffs, debuffs, counters, intercepts, and other stateful effects.
- `m_OverwriteCount`: **Observed.** Explicit overwrite count used by count-based effects when reapplying the stage.
- `m_Counts`: **Proven.** Per-boost count or stock array. Used by **count-based** mechanics such as limited reactions, repeat counts, or similar stage-local counters.
- `m_OrderPriority`: **Observed.** Rare battle order override. OT0 only uses a nonzero value on one row.
- `m_NextPriority`: **Observed.** Rare next-action priority adjustment. OT0 only uses it on seven rows.
- `m_TargetNextPriority`: **Observed.** Target-side next-action priority setting. OT0 mostly uses the sentinel value `-100`; when set to values like `1`, `2`, `30`, or `31` it is changing future turn order behavior.
- `m_ShieldDamage`: **Stock-data-zero but runtime-supported.** OT0 authors this as `0` in all checked stock rows, but native fixed-damage and ordinary damage paths call `USkillDB::GetSkillFixedShieldDamage`, and that helper returns `pAvail->m_ShieldDamage` from the live row (`Octopath_Traveler0-Win64-Shipping.exe.c`:17465680-17465705, 17466190-17466210, and 17976714-17976725). If you set it nonzero, treat it as an authoring change that needs battle testing, not as exporter noise.
- `m_IgnoreWeak`: **Proven.** Ignore weakness interaction or weakness requirement for this stage. OT0 only uses it on seventy-three rows.
- `m_NoBreakable`: **No live OT0 reader recovered.** Always zero in stock data and no strong gameplay reader was found by exact field search; do not describe break prevention as proven.
- `m_PhysicalHitRatio`: **Runtime-proven.** Late additive modifier in the physical hit-vs-avoid check. For physical, non-critical HP-affecting hits, OT0 computes a chance from attacker hit, target evasion, the target's break state, `m_PhysicalHitRatio`, and a darkness-style multiplier; this field is not a simple standalone hit percent.
- `m_CriticalRatio`: **Runtime-proven.** Additive critical-chance modifier for the stage. The runtime adds it after the stat-derived `CRT - AGI` term and after the last matching `BUFF_CRITICAL_RATE` value; that buff contribution is last-match-wins in the recovered critical-data path, not a sum.
- `m_Suicide`: **Observed.** Marks the stage as self-sacrificial or self-killing.
- `m_LotteryAilment`: **Proven.** Random ailment-selection helper. Rare in OT0 and only useful if the modify path is choosing an ailment package stochastically. Note: only one non-zero ailment listed in `m_AddAilment` is chosen.
- `m_AilmentInvokeCondition`: **Observed.** Extra condition gate specifically for the ailment portion of the stage. OT0 uses it on one row.
- `m_AddAilmentPick`: **Runtime-proven.** Selector/count for the add-ailment list. `USkillDB::CheckAilmentPick` copies the add list, compacts out zero gaps, then truncates the surviving list to this count. Pick `0` means no truncation, not "pick none".
- `m_AddAilment`: **Proven.** Inline list of up to sixteen `SkillAilmentType.m_id` rows to add/apply.
- `m_CalcTypeAilment`: **Proven.** Parallel list of ailment value/storage selectors for the same indexes in `m_AddAilment` and `m_ValueAilment`. Native `USkillDB::GetAilmentCalcType(AvailID, AilmentType)` searches `m_AddAilment`, finds the matching index, then returns `m_CalcTypeAilment[index]`; the overload taking `(pAvail, Index)` returns the same indexed lane directly (`Octopath_Traveler0-Win64-Shipping.exe.c`:17969156-17969189). Stock data uses `0`, `1`, `2`, `3`, `4`, and one `9`, matching `ESKILL_CALC_TYPE` names such as `WEAPON`, `MAGIC`, `ADD_RATED`, `ADD_FIXED`, and `MAGIC_REFLECTION`, not the ailment-damage enum.
- `m_ValueAilment`: **Proven.** Parallel list of ailment potency/override values corresponding to `m_AddAilment`.
- `m_NotExtensionTarget`: **Observed.** Excludes the target state from ordinary duration-extension behavior. OT0 uses it on `557` rows, so it is not just a one-off debug flag.
- `m_WeakLockID`: **Proven.** `Enemy/EnemyWeakLockID.m_id`. Use it when the stage locks specific weakness slots.
- `m_DelayedSkill`: **Proven.** Follow-up `SkillID.m_id` that resolves later rather than immediately, needs to be paired with the `DELAYED_SKILL` ailment in that case. Also used to specify the intercept/counter skill when paired with that ailment.
- `m_DelaySkillPriority`: **Observed.** Priority bucket/timing helper for the delayed skill stage.
- `m_SubAilmentPick`: **Runtime-proven.** Selector/count for the sub/remove-ailment list. The runtime first intersects `m_SubAilment` with currently active target ailments, sorts through the same pick helper when needed, and then truncates to this count. Pick `0` means no truncation.
- `m_SubAilment`: **Proven.** Secondary list of up to sixteen `SkillAilmentType.m_id` rows to be removed/subtracted from the targed.
- `m_ResistID`: **Confirmed.** `Enemy/EnemyWeakID.m_id`, **not** `SkillResistList`. This field swaps or injects a complete weakness-table row, because `EnemyWeakID` stores per-weapon/per-element `ResistType` classes. That is why the value set matches `EnemyWeakID` and not `SkillResistList`.
- `m_ResistAilment`: **Observed.** Inline ailment-resistance-rate array for the base ailment families. In OT0 this can coexist with `m_ResistAilmentID`, so do not assume one disables the other.
- `m_ResistAilmentID`: **Confirmed.** `Skill/SkillResistAilmentID.m_id`. This is the packaged form of ailment resistance, storing explicit ailment IDs and matching rates.
- `m_Reinforcement`: **Proven.** `Enemy/EnemyReinforcements.m_id` for stages that summon reinforcements. OT0 has one outlier value `140`, so always verify the target row before cloning rare boss behavior.
- `m_WeakChangeID`: **Proven.** `Enemy/EnemyWeakChangeID.m_id` for staged weakness-table modification. Untested whether this affects party members' resistance profiles when executed by them.
- `m_EnhancedSkillID`: **Proven; unused in OT0.** Always zero. Refers to `SkillEnhancedList.m_id`. Used in CotC to apply Potency buffs to Linde, Hägen, etc. which occupy the `SkillEnhancedList.m_id`s.
- `m_SummonGuestID`: **Proven.** `Character/CharaGuest.m_id` for guest summon behavior.
- `m_OverrideWeapon`: **Confirmed.** Direct `WeaponType.m_id` override.
- `m_OverrideMagic`: **Confirmed.** Direct `MagicType.m_id` override for the stage.
- `m_AppendType`: **Runtime-supported.** Per-avail appended-weakness/resist-routing selector. OT0 damage resistance calls `GetResistIDByAvailAppend` before setting the weak flag, so this field belongs to immediate per-avail weakness routing rather than to the persistent active-ailment append system. The persistent version lives on `SkillAilmentType.m_WeaponAppendType` / `m_MagicAppendType`.
- `m_SkillAvailMagnificationCondition`: **Runtime-proven.** `SkillAvailMagnificationConditionList.m_id` and the scalar gate for the whole avail-magnification block. If this field is `<= 0`, OT0 returns the base value immediately and does not scan `m_SkillAvailMagnificationConditionArray`.
- `m_SkillAvailMagnificationConditionArray`: **Runtime-proven.** Additional `SkillAvailMagnificationConditionList.m_id` rows, scanned only after the scalar condition field is positive. The scan stops at the first nonpositive condition value.
- `m_SkillAvailMagnification`: **Runtime-proven.** Direct magnification percentage in OT0, not a `SkillAvailMagnificationList` row ID. If the scalar condition passes, this becomes a candidate magnification value.
- `m_SkillAvailMagnificationArray`: **Runtime-proven.** Direct additional magnification percentages in OT0, not row IDs. Multiple matching conditions do not multiply; the runtime keeps the maximum matching magnification and applies `base * mag / 100` once.
- `m_InvocationProbability`: **Runtime-proven.** Extra probability gate used by `CheckAvailSuccess` for raw modify types `148..152` (`DEBUFF_DAGGER`, `DEBUFF_AXE`, `DEBUFF_BOW`, `DEBUFF_ROD`, `DEBUFF_PAPERS`). Other modify types fall back to `GetHitRatio` in this path.
- `m_ModeChangeID`: **Confirmed.** `Enemy/EnemyModeID.m_id`. This is a boss/AI phase-transition hook, not a generic player-form switch.
- `m_CountRefAilment`: **Stock-data-zero but runtime-supported.** OT0 stock rows leave it zero, but the `FAilmentInfo` count-reference path can watch a referenced active ailment type and multiply the referenced count by `m_CountRefValue`.
- `m_CountRefValue`: **Stock-data-zero but runtime-supported.** Multiplier used by the count-reference path when `m_CountRefAilment` is active. The recovered runtime sums positive normal/support/special referenced counts before multiplying by this value.
- `m_CountRefValueLimit`: **Stock-data-zero but runtime-supported.** Upper-limit companion for the count-reference value path. Stock OT0 data does not author it, but the runtime surface exists.

#### Concrete OT0 examples
- `m_ResistID`: `SkillAvailID#40085` and `#73697` both use `6527`, and `SkillAvailID#73698` uses `6528`. Those values exist as `EnemyWeakID` rows, which is why this field behaves like a full weakness-table replacement path rather than a simple percentage modifier.
- `m_OverrideWeapon` / `m_OverrideMagic`: `SkillAvailID#76127` through `#76134` are the weapon-family stages used by `Repeated Bash`, stepping through `m_OverrideWeapon = 1..8`. By contrast, `SkillAvailID#74047` and `#74051` from `Call for Help` use `m_OverrideMagic = 5`, showing that the same stage struct can forcibly render a magic family instead.
- `m_Reinforcement`: `SkillAvailID#20049` uses `m_Reinforcement = 43` on the enemy skill whose visible text is `<skl_user_name> calls for his lackeys!`. Its paired `SkillEffectiveID#20049` also carries the summon-success/summon-failure battle log strings, which is a good example of gameplay and presentation rows working together.
- `m_WeakChangeID` / `m_WeakLockID`: `SkillAvailID#20079` and `#20089` belong to the visible family `Weaknesses have been swapped!`, while `SkillAvailID#20048` belongs to `<skl_user_name> is protected by his lackeys.` and carries `m_WeakLockID = 11`. That is exactly the pattern you would expect for weakness-table mutation versus slot locking.
- `m_ModeChangeID`: `SkillAvailID#77350` from `Indestructible Flame`, `#74765` from `Dark Hex`, and `#75499` from `Vorpal Strike` all set nonzero mode-change IDs, which is why this field is best read as a boss/AI phase hook instead of a generic player stance switch.
- `m_SkillAvailMagnification*`: `SkillAvailID#74940` from `Surprise Attack` uses condition package `69` plus array packages `[68,67,59,61]` and direct magnifications `110/130/150/170/200`. `SkillAvailID#76297` from `Ulti-meow` uses packages `102..109` and direct magnifications up to `300`. Those are raw values, not `SkillAvailMagnificationList` row IDs.

#### CotC-only extra fields
- CotC 4.2.3 reflected `SkillAvailIDBase` is wider than the 3.22.10 metadata surface. The 4.2.3 header directly supports the late tail from `m_HitRatio` through `m_NoConsumeAdditionWeak` (`libUE4.so.h`:59700-59782). For authoring, use the field-presence claim only for schema compatibility; do not import the fields into OT0 unless an OT0 caller exists.
- `m_HitRatio`: **Confirmed from CotC 4.2.3 reflected header.** Extra hit-ratio scalar beyond OT0's simpler hit-rate tables.
- `m_LotteryAilmentNum`: **Confirmed from CotC 4.2.3 reflected header.** Companion count/limit for lottery ailment selection.
- `m_CompareValueAilment`: **Confirmed from CotC 4.2.3 reflected header.** Extra comparison flag/value lane beside `m_ValueAilment`.
- `m_DelayedSkillActByOwner`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Run the delayed skill as the original owner rather than the eventual state holder.
- `m_SelfAfterSkillCheck`: **Confirmed from CotC 4.2.3 reflected header.** Self-after-skill check flag.
- `m_NoSelfAfterSkillCheckEffective`: **Confirmed from CotC 4.2.3 reflected header.** Blocks or suppresses the effective/presentation side of the previous check.
- `m_SubAilmentTag`: **Confirmed from CotC 4.2.3 reflected header.** Tag ID for ailment subtraction grouping.
- `m_SubAilmentTagFlag`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Enable/tag behavior toggle for the previous field.
- `m_CalcTypeResist`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Separate calculation-mode selector for resistance manipulation.
- `m_AddResistWeapon`: **Confirmed from CotC 4.2.3 reflected header.** Additive weapon-resistance payload array.
- `m_AddResistMagic`: **Confirmed from CotC 4.2.3 reflected header.** Additive element-resistance payload array.
- `m_AilmentMagnificationConditions`: **Confirmed from CotC 4.2.3 reflected header.** Ailment-side magnification condition package array.
- `m_AilmentMagnifications`: **Confirmed from CotC 4.2.3 reflected header.** Ailment-side magnification value array.
- `m_CountRefAilment`: **Confirmed from CotC 4.2.3 reflected header.** Counter-reference ailment selector used by the late count-reference tail.
- `m_CountRefAilmentTag`: **Confirmed from CotC 4.2.3 reflected header.** Counter-reference ailment-tag selector.
- `m_CountRefType`: **Confirmed from CotC 4.2.3 reflected header.** Count-reference mode selector; the companion enum exists separately as `ESKILL_COUNT_REF_TYPE`.
- `m_CountRefValue`: **Confirmed from CotC 4.2.3 reflected header.** Main numeric value used by the late count-reference tail.
- `m_CountRefValueLimit`: **Confirmed from CotC 4.2.3 reflected header.** Upper-limit payload for the late count-reference tail.
- `m_CountRefAttribute`: **Confirmed from CotC 4.2.3 reflected header.** Attribute selector for the current count-reference tail.
- `m_IgnorePreviousSuccessful`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Ignore prior success state when resolving repeated checks.
- `m_IgnoreDodge`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Bypass dodge resolution.
- `m_IgnoreZeroDamage`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Continue effect processing even if damage becomes zero.
- `m_AddDamageLimitValue`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Additional damage-cap increase beyond the base ailment/trait path.
- `m_AddDamageLimitCondition`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Condition gate for the damage-limit increase.
- `m_NonCondition`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Extra negation/no-condition switch.
- `m_IgnoreResistAilment`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Bypass ailment resistance.
- `m_IgnoreDefenceBuff`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Bypass defense-up buffs.
- `m_IgnoreReduceDamage`: **Confirmed field name from CotC 4.2.3 reflected header; semantic role observed from name and CotC usage.** Bypass general damage-reduction effects.
- `m_HitWeaponEnable`: **Confirmed from CotC 4.2.3 reflected header.** Enables weapon-family hit filtering or hit override behavior.
- `m_HitWeaponType`: **Confirmed from CotC 4.2.3 reflected header.** Weapon-family selector paired with `m_HitWeaponEnable`.
- `m_NoConsumeAdditionWeak`: **Confirmed from CotC 4.2.3 reflected header.** Prevents consuming an additional-weakness state or charge.
- The older names `m_IsBuffDamageSupStrictlyCheck` and `m_UseAilmentMagnificationForAddDamageLimit` were still not found in the supplied CotC 4.2.3 header/decomp recheck. Keep them unresolved/schema-only until a current binary or export proves them.


#### Runtime meaning by field group
- **When the stage can run:** `m_InvokeTiming`, `m_InvokeCondition`, and `m_AilmentInvokeCondition` are separate gates. A stage can be present in `SkillID.m_Avails` but not execute because its timing or condition package fails.
- **Who the stage sees:** `m_TargetType`, AI target wrappers, continue-target selectors, dead-target selectors, and ailment-derived target shorthand feed both the target picker and UI/AI classifiers. Target type is therefore not only a battle cursor setting.
- **What operation runs:** `m_ModifyType` chooses the opcode. It controls whether the row is read as damage/heal, revive, weakness change, steal, result reward, village supply, support passive, preparation, reinforcement, and so on.
- **How the operation is measured:** `m_CalcType`, `m_Values`, `m_FluctuationValues`, `m_SkillRatios`, `m_Turns`, and `m_Counts` are the common numeric package, but their exact meaning is opcode-specific.
- **How the operation interacts with hit logic:** `m_HitTypes`, `m_PhysicalHitRatio`, `m_CriticalRatio`, `m_ShieldDamage`, `m_IgnoreWeak`, and `m_NoBreakable` feed late damage/hit/shield branches. Stock-zero fields can still be live if the native helper reads them.
- **How the operation creates active state:** `m_AddAilment`, `m_CalcTypeAilment`, `m_ValueAilment`, `m_Turns`, `m_Counts`, duplicate policy, count-ref data, and food/support/special flags become `FAilmentInfo` fields on the target. Once applied, the active record—not the static row alone—is what later code reads.
- **How the operation removes active state:** `m_SubAilment`, `m_SubAilmentPick`, `m_RemoveConditionAilment`, `m_RemoveConditionParam`, and resist/default-success behavior form a separate removal package. Do not assume add and remove lists share the same probability or resistance path.
- **How the operation scales conditionally:** `m_SkillAvailMagnificationCondition`, `m_SkillAvailMagnification`, and their arrays are direct conditional percent packages. The magnification values are percentages such as `110`, `130`, `150`, not row IDs into `SkillAvailMagnificationList`.
- **How the operation links to other systems:** fields such as `m_DelayedSkill`, `m_WeakLockID`, `m_ResistID`, `m_ModeChangeID`, `m_EnhancedSkillID`, `m_GuestID`, village helper IDs, supply groups, and shop/farm target rows make the avail a hub into other folders.

<details>
<summary>Evidence: an avail is a staged operation, not a flat value row</summary>

```cpp
// Compact authoring model distilled from the OT0 C export.
SkillID.m_Avails[slot]
  -> SkillAvailID.m_InvokeTiming / m_InvokeCondition
  -> target collection from m_TargetType
  -> opcode dispatch from m_ModifyType
  -> value interpretation from m_CalcType + m_Values[boost]
  -> optional ailment package from m_AddAilment[i]
  -> optional presentation timing through paired SkillEffectiveID slot
```

</details>

#### OT0 usage notes
- `Array sizes`: OT0 uses fixed sizes: `m_HitTypes` length `4`, `m_Values` length `4`, `m_SkillRatios` length `4`, `m_Turns` length `4`, `m_Counts` length `4`, `m_AddAilment` length `16`, `m_SubAilment` length `16`, `m_SkillAvailMagnificationConditionArray` length `7`, `m_SkillAvailMagnificationArray` length `7`.
- `Common opcodes`: OT0 overwhelmingly uses `m_ModifyType = 1 (CHARACTER)`. The next most common families are weakness change, revive, steal, result rewards, and village economy opcodes such as `ITEM_SUPPLY`.
- `Dormant or sparse counters`: `m_NoBreakable` has no strong live reader in the checked OT0 export. `m_EnhancedSkillID`, `m_AppendType`, and the `m_CountRef*` fields are sparse in stock OT0 but have native readers. `m_ShieldDamage` is stock-data-zero but runtime-owned through `GetSkillFixedShieldDamage`.

### `SkillConditionList`
Reusable boolean condition packages for `SkillAvailID.m_InvokeCondition` and some other skill systems. Do not confuse this file with `SkillAvailMagnificationConditionList`; they are different opcode families.

- **OT0 rows:** `275`.
- **CotC rows:** `933`.
- **OT0 SDK row struct:** `FSkillConditionListBase`.
- The OT0 SDK dump used here does **not** expose a clean named enum for `m_Conditions` in this table. The observed opcode set is much larger than the magnification-condition enum and includes values such as `10723`, `10790`, and `10816`. Use the local [Condition Crossref Atlas](Octopath_Condition_Crossref_Atlas.md) for the current guide-bundle condition inventory; leave names unresolved where that atlas does not prove a semantic label.

#### Field-by-field
- `m_id`: Primary key for the condition package.
- `m_Conditions`: **Proven shape, unresolved names.** Four condition opcodes interpreted slot-by-slot. This is the main condition family used by `SkillAvailID.m_InvokeCondition`. See the local [Condition Crossref Atlas](Octopath_Condition_Crossref_Atlas.md).
- `m_Params`: **Proven.** Four generic integer parameter slots. The meaning of each slot is determined by the matching `m_Conditions` entry.
- `m_AilmentTypes`: **Proven.** Four `SkillAilmentType.m_id` parameter slots for ailment-aware condition opcodes. OT0 uses `36` unique ailment IDs here.
- `m_StatusTypes`: **Proven shape.** Four `CharaStatusID` parameter slots for status-aware condition opcodes. OT0 leaves them unused, but CotC uses the same field family more aggressively.
- `m_WeaponTypes`: **Proven shape.** Four `WeaponType.m_id` parameter slots. Unused in OT0.
- `m_MagicTypes`: **Proven shape.** Four `MagicType.m_id` parameter slots. Unused in OT0.
- `m_Equipment`: **Proven shape.** Four equipment parameter slots. Unused in OT0.
- `m_ORFlag`: **Confirmed.** Combine occupied condition slots with OR instead of AND. OT0 rows `335` and `336` prove this directly by repeating the same ailment-check opcode across different ailment IDs.


#### Runtime meaning
- A `SkillConditionList` row is a reusable condition package, not a single boolean. The runtime evaluates occupied slots and then combines them according to `m_ORFlag`.
- `m_Conditions[i]` chooses the condition opcode, while `m_Params[i]`, `m_AilmentTypes[i]`, `m_StatusTypes[i]`, `m_WeaponTypes[i]`, `m_MagicTypes[i]`, and `m_Equipment[i]` are parameter lanes for that same index.
- Empty or zero slots are not equivalent to passing conditions; they are skipped. A row with no meaningful occupied slot is dangerous because its behavior depends on the caller's fallback path.
- For custom skills, reuse a condition row whose exact package already matches the intended gating behavior. Renaming or reusing an unknown opcode with different params is much riskier than cloning a known pattern.

#### CotC-only extra fields
- `m_AutoDungeonBuff`: **Confirmed from CotC schema.** Extra auto-dungeon-specific buff parameter channel.
- `m_AutoDungeonBuffType`: **Confirmed from CotC schema.** Type selector for the auto-dungeon buff channel.

### `SkillAvailMagnificationConditionList`
Reusable condition packages specifically for conditional magnification. Unlike `SkillConditionList`, this file **does** line up with a named OT0 SDK enum.

- **OT0 rows:** `89`.
- **CotC rows:** `217`.
- **OT0 SDK row struct:** `FSkillAvailMagnificationConditionListBase`.
- **Related OT0 SDK enums:** `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`.

#### Exact OT0 SDK enum quote used by this table
##### `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`
Quoted from `SDK/Kingship_structs.hpp:6226`.
```cpp
enum class ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID : uint8
{
	NONE                                     = 0,
	SLD_EQ                                   = 1,
	SLD_GE                                   = 2,
	SLD_LE                                   = 3,
	SLD_MUL                                  = 4,
	HAS_AILMENT                              = 5,
	RACE_KILLER                              = 6,
	BEFORE_ACTION                            = 7,
	HP_SELF_LE                               = 8,
	HP_SELF_GE                               = 9,
	HP_TARGET_LE                             = 10,
	HP_TARGET_GE                             = 11,
	HP_SELF_LT                               = 12,
	HP_TARGET_LT                             = 13,
	HAS_AILMENT_SELF                         = 14,
	AILMENT_ICON_COUNT_SELF_GE               = 15,
	AILMENT_ICON_COUNT_TARGET_GE             = 16,
	AILMENT_ICON_COUNT_TARGET_LE             = 26,
	EQUIP_SELF                               = 17,
	NoEQUIP_SELF                             = 18,
	HAS_AILMENT_COUNT_GE                     = 19,
	HAS_AILMENT_COUNT_SELF_GE                = 20,
	INTENSIVE_COUNT_GE                       = 21,
	BHP_SELF_FIXED_LE                        = 22,
	BHP_SELF_FIXED_GE                        = 23,
	BHP_TARGET_FIXED_LE                      = 24,
	BHP_TARGET_FIXED_GE                      = 25,
	ENABLED_SUPPORTABILITY_SELF              = 27,
	DAY_OR_NIGHT                             = 30,
	TURN_ORDER_FIXED_LE                      = 31,
	TURN_ORDER_FIXED_GE                      = 32,
	PET_TAG                                  = 33,
	NoPET_TAG                                = 34,
	AILMENT_PC_COUNT_GE                      = 35,
	AILMENT_PC_COUNT_LE                      = 36,
	AILMENT_ENEMY_COUNT_GE                   = 37,
	AILMENT_ENEMY_COUNT_LE                   = 38,
	JOB_COUNT_SET_ID                         = 39,
	JOB_COUNT_GE                             = 40,
	JOB_COUNT_LE                             = 41,
	PROGRESS                                 = 42,
	SP_BURST_GE                              = 43,
	COUNT_DAMAGE_SELF_GE                     = 44,
	COUNT_DAMAGE_SELF_LE                     = 45,
	TARGET_FRIEND                            = 46,
	TARGET_ENEMY                             = 47,
	P_DEF_SELF_GE                            = 48,
	P_DEF_SELF_LE                            = 49,
	ESKILL_AVAIL_MAGNIFICATION_CONDITION_MAX = 50,
};
```
- `m_Conditions` stores the raw IDs from this enum directly. `SkillAvailMagnificationConditionList` is therefore the exact condition-package table for those magnification opcodes, not a vague condition bucket.
- This is also why `SkillAvailID.m_SkillAvailMagnificationCondition` and `m_SkillAvailMagnificationConditionArray` can be described precisely as row IDs into this file: those fields point to a package whose occupied slots are themselves typed by the enum above.
- CotC Global 3.22.10 matches OT0 through value `41`, but then repurposes OT0 `42..50` and adds a large new tail up through `81`. That tail includes repeat-consume, ailment-tag, command-BP, targetable-alive, job, open-weak, weak-state, and ailment-turn conditions that do not exist in the OT0 enum under the same numbers.

#### Field-by-field
- `m_id`: Primary key for the magnification-condition package.
- `m_Conditions`: **Confirmed.** Four condition opcodes from `ESKILL_AVAIL_MAGNIFICATION_CONDITION_ID`. OT0 uses values such as `SLD_GE`, `HAS_AILMENT`, `RACE_KILLER`, `BEFORE_ACTION`, `HP_SELF_LE`, `ENABLED_SUPPORTABILITY_SELF`, `DAY_OR_NIGHT`, `TURN_ORDER_FIXED_LE`, `PROGRESS`, `SP_BURST_GE`, and defense thresholds.
- `m_IntParams`: **Confirmed.** Four generic numeric thresholds corresponding to the occupied `m_Conditions` slots.
- `m_AilmentParams`: **Observed.** Ailment-related parameters. Many OT0 values match `SkillAilmentType.m_id`, but a handful of OT0 rows reference IDs not present in the current OT0 `SkillAilmentType` export, so do not assume every value is a live OT0 ailment row.
- `m_RaceParams`: **Observed.** Race filter/parameter slots. OT0 barely uses them.
- `m_Equipment`: **Confirmed shape.** Equipment filter slots. OT0 leaves them unused.


#### Runtime meaning
- Magnification conditions are evaluated after the base avail value is read and before final battle value use. They do not decide whether a stage exists; they decide how strongly a stage is scaled.
- `m_Conditions[i]` is the opcode; `m_IntParams[i]`, `m_AilmentParams[i]`, `m_RaceParams[i]`, and `m_Equipment[i]` are its same-index parameters.
- The scalar field on `SkillAvailID` is checked first as the basic conditional magnification lane, and the array lane supplies additional candidate packages. The strongest satisfied magnification is the safest authoring assumption.
- Do not confuse the condition row ID with the magnification value. The row says *when* to scale; `SkillAvailID.m_SkillAvailMagnification*` says *how much* to scale.

#### Concrete OT0 examples
- `SkillAvailMagnificationConditionList#7` stores `m_Conditions = [2,0,0,0]` with `m_IntParams = [10,0,0,0]`, i.e. a simple `SLD_GE 10` package.
- `#8` stores `m_Conditions = [5,0,0,0]` with `m_AilmentParams = [1,0,0,0]`, i.e. `HAS_AILMENT` with ailment row `1`.
- `#10` stores `m_Conditions = [7,0,0,0]`, i.e. a pure `BEFORE_ACTION` gate with no extra integer payload.
- `#13` stores `m_Conditions = [12,0,0,0]` with `m_IntParams = [100,0,0,0]`, i.e. a `HP_SELF_LT 100` style package.
- `#81` stores `m_Conditions = [8,0,0,0]` with `m_IntParams = [90,0,0,0]`, i.e. a `HP_SELF_LE 90` threshold package.

#### CotC-only extra fields
- `m_WeaponTypes`: **Confirmed from CotC schema.** Weapon-family filter slots for magnification conditions.
- `m_MagicTypes`: **Confirmed from CotC schema.** Element-family filter slots for magnification conditions.

### `SkillAvailMagnificationList`
Tiny OT0-only lookup table with four rows. It is real, but it is **not** the main linkage path used by OT0 `SkillAvailID` rows.

- **OT0 rows:** `4`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** no matching dedicated row struct was found in the OT0 SDK dump shipped in this repo.
- The critical practical point is that OT0 `SkillAvailID.m_SkillAvailMagnification` stores direct values such as `110` and `200`, while this table's IDs are only `0..3`.

#### Field-by-field
- `m_id`: Primary key for the small lookup row.

- Runtime meaning: OT0 does not use `SkillAvailID.m_SkillAvailMagnification` as a row ID into this table. The active scalar/array magnification fields on `SkillAvailID` are direct percentages. Treat this table as a legacy/helper legend unless you recover a specific caller for your target build.
- `m_Conditions`: **Observed.** Single-slot condition selector in this tiny legend table.
- `m_Params`: **Observed.** Single-slot parameter payload for the lookup row.

### `SkillEffectiveID`
Presentation payload row. This is where cameras, battle actions, effects, sounds, voices, text popups, and visibility changes are staged in time.

- **OT0 rows:** `4381`.
- **CotC rows:** `11434`.
- **OT0 SDK row struct:** `FSkillEffectiveIDBase`.
- **Related OT0 SDK enums:** `ESKILLEFFECT_LAYOUT`, `ESKILLEFFECTIVE_CHARA_VISIBILITY`, `ESKILLEFFECTIVE_VISIBILITY_TARGET`.

#### Exact OT0 SDK enum quotes that directly drive this row
##### `ESKILLEFFECT_LAYOUT`
Quoted from `SDK/Kingship_structs.hpp:6406`.
```cpp
enum class ESKILLEFFECT_LAYOUT : uint8
{
	NONE                                     = 0,
	SELF                                     = 1,
	TARGET                                   = 2,
	FRIENDLY_CENTER                          = 3,
	TARGET_CENTER                            = 4,
	BATTLE_CENTER                            = 5,
	SELF_NO_REVERSE                          = 6,
	ESKILLEFFECT_MAX                         = 7,
};
```
- `m_EffectLayout` stores raw `ESKILLEFFECT_LAYOUT` values. That is why each effect slot can say exactly whether it spawns on `SELF`, `TARGET`, `TARGET_CENTER`, or `BATTLE_CENTER`.

##### `ESKILLEFFECTIVE_CHARA_VISIBILITY`
Quoted from `SDK/Kingship_structs.hpp:6371`.
```cpp
enum class ESKILLEFFECTIVE_CHARA_VISIBILITY : uint8
{
	NONE                                     = 0,
	VISIBLE                                  = 1,
	INVISIBLE                                = 2,
	FADE_VISIBLE                             = 10,
	FADE_INVISIBLE                           = 11,
	ESKILLEFFECTIVE_CHARA_MAX                = 12,
};
```
- `m_CharaVisibility` stores raw values from this enum for the visibility action itself (`VISIBLE`, `INVISIBLE`, `FADE_VISIBLE`, `FADE_INVISIBLE`).
- This enum is stable in CotC Global 3.22.10; the operation constants themselves still line up one-for-one.

##### `ESKILLEFFECTIVE_VISIBILITY_TARGET`
Quoted from `SDK/Kingship_structs.hpp:6383`.
```cpp
enum class ESKILLEFFECTIVE_VISIBILITY_TARGET : uint8
{
	NONE                                     = 0,
	SELF                                     = 1,
	TARGET                                   = 2,
	GUEST                                    = 3,
	FRIENDLY_1                               = 4,
	FRIENDLY_ALL                             = 10,
	FRIENDLY_ALL_WITHOUT_SELF                = 11,
	FRIENDLY_ALL_GUEST                       = 12,
	FRIENDLY_ALL_GUEST_WO_SELF               = 13,
	ENEMY_ALL                                = 20,
	ENEMY_ALL_WITHOUT_TARGET                 = 21,
	FORWARDS                                 = 30,
	FORWARDS_WO_SELF                         = 31,
	BACKUPS                                  = 32,
	FORWARDS_AND_BACKUPS                     = 33,
	FORWARDS_AND_BACKUPS_WO_SELF             = 34,
	ESKILLEFFECTIVE_VISIBILITY_MAX           = 35,
};
```
- `m_VisibilityTarget` stores raw values from this enum for *who* receives the visibility action (`SELF`, `TARGET`, `FRIENDLY_ALL`, `ENEMY_ALL`, `FORWARDS_AND_BACKUPS`, and so on).
- OT0 splits visibility staging into two dimensions on purpose: one enum describes the visibility operation, while the other describes the recipient set.
- CotC Global 3.22.10 keeps most of this family intact but adds `AFTERSKILL_ENFORCER = 100` and shifts the max sentinel to `101`. It also normalizes OT0's `FRIENDLY_1` label to `FRIENDLY` at value `4`.

#### Field-by-field
- `m_id`: Primary key for the presentation stage.
- `m_Repeat`: **Confirmed.** Stage repeat count. OT0 sets it to `1` on every row.
- `m_HoldTime`: **Observed.** Total presentation hold time in seconds.
- `m_AvailTime`: **Observed.** Time in seconds when the gameplay stage is considered available/resolved relative to the presentation.
- `m_Cameras`: **Confirmed.** Up to three `Battle/BattleCameraParams.m_id` rows for camera choreography.
- `m_CameraTimes`: **Observed.** Trigger times for the camera stages.
- `m_Animations`: **Confirmed.** Up to three `Battle/BattleActionID.m_id` rows for the performed battle actions.
- `m_PlayTimes`: **Observed.** Trigger times for the battle actions.
- `m_PlayRates`: **Observed.** Playback-rate multipliers for the battle actions.
- `m_AnimationTarget`: **Observed.** Per-action target selector for who plays the animation.
- `m_AnimStartFrame`: **Confirmed unused in OT0.** Start-frame override field present in the SDK struct but never used by OT0 rows.
- `m_AnimEndFrame`: **Observed.** End-frame or end-section marker for the animation stage. OT0 populates it on every row.
- `m_AnimNoIdle`: **Observed.** Suppress idle return or idle blend after the animation. Rare in OT0.
- `m_Effects`: **Proven.** Up to three `Effect/EffectList.m_id` rows spawned by the presentation stage.
- `m_InvokeTimes`: **Observed.** Effect-spawn times.
- `m_EffectLayout`: **Confirmed.** Per-effect spawn layout from `ESKILLEFFECT_LAYOUT` (`SELF`, `TARGET`, `FRIENDLY_CENTER`, `TARGET_CENTER`, `BATTLE_CENTER`, and so on).
- `m_OffsetsH`: **Observed.** Horizontal offsets for the spawned effects.
- `m_OffsetsV`: **Observed.** Vertical offsets for the spawned effects.
- `m_Sounds`: **Confirmed.** Up to three `Sound/SoundList.m_id` rows triggered by the stage.
- `m_SoundTimes`: **Observed.** Trigger times for the sound rows.
- `m_SoundToVoice`: **Observed.** Rare routing/link flag controlling whether the sound is tied to voice-style timing/handling.
- `m_Voices1`: **Confirmed.** Primary voice slots pointing to `Sound/PartVoiceID.m_id`.
- `m_Voices2`: **Confirmed.** Secondary voice slots pointing to `Sound/PartVoiceID.m_id`.
- `m_Voices3`: **Confirmed unused in OT0.** Tertiary voice slots are present but zero in OT0.
- `m_VoiceTimes`: **Observed.** Voice trigger times.
- `m_Texts`: **Proven.** Up to ten floating text IDs. Most OT0 values resolve through `GameTextSkill`, some through `GameTextUI`, and a small residue currently sits outside the obvious OT0 banks.
- `m_TextTime`: **Observed.** Floating-text trigger times.
- `m_CharaVisibility`: **Confirmed.** Per-visibility-stage values from `ESKILLEFFECTIVE_CHARA_VISIBILITY`.
- `m_VisibilityTarget`: **Confirmed.** Per-visibility-stage values from `ESKILLEFFECTIVE_VISIBILITY_TARGET`.
- `m_VisibilityStartTime`: **Observed.** Start time for the visibility change.


#### Runtime meaning by field group
- **Stage clock fields** (`m_HoldTime`, `m_AvailTime`, trigger-time arrays) coordinate gameplay resolution with presentation. `m_AvailTime` is the important bridge: it tells the skill program when the paired gameplay stage should be considered available relative to the animation/effect sequence.
- **Camera/action fields** (`m_Cameras`, `m_CameraTimes`, `m_Animations`, `m_PlayTimes`, `m_PlayRates`, `m_AnimationTarget`) define the visible actor choreography. These do not change the damage formula by themselves, but bad timing can make a skill appear to hit before or after its actual avail resolution.
- **Effect fields** (`m_Effects`, `m_InvokeTimes`, `m_EffectLayout`, `m_OffsetsH`, `m_OffsetsV`) are per-slot spawn programs. Layout and offsets are interpreted together; the same effect asset can look completely different when spawned at `SELF`, `TARGET`, or `BATTLE_CENTER`.
- **Sound/voice fields** (`m_Sounds`, `m_SoundTimes`, `m_Voices1`, `m_Voices2`, `m_VoiceTimes`, `m_SoundToVoice`) are timed audio events. `SkillID.m_VoiceID` is a root voice hook, while these arrays are stage-level timed voice slots.
- **Text fields** (`m_Texts`, `m_TextTime`) are floating/telop text events. They are not the same as `SkillID.m_Name` or `m_Detail`.
- **Visibility fields** (`m_CharaVisibility`, `m_VisibilityTarget`, `m_VisibilityStartTime`) are timed operations over actor sets. They are how skills hide/fade actors during presentation and should be cloned with the same target assumptions as the source skill.

<details>
<summary>Evidence: effective stages are timed presentation programs</summary>

```cpp
// Compact model of the SkillEffectiveID program.
SkillEffectiveID
  -> hold/avail timing
  -> camera events[]
  -> animation events[]
  -> effect events[]
  -> sound/voice events[]
  -> floating text events[]
  -> character visibility events[]
```

</details>

#### Concrete OT0 examples
- Floating text is not hypothetical. `SkillEffectiveID#20049` carries the strings `<skl_user_name> calls for his lackeys!` and `However, the lackeys did not appear...`, which is the exact presentation companion to reinforcement-style enemy skills.
- Visibility staging is also real, not a leftover field. `SkillEffectiveID#65047` and `#65052`, both used by `Lotus Blade`, toggle target visibility with `INVISIBLE -> VISIBLE` on `TARGET`.
- `SkillEffectiveID#65954` and `#65956`, used by `Pilfer`, show the fade variants rather than the hard toggle variants, using `FADE_INVISIBLE` / `FADE_VISIBLE` on `TARGET`.
- `SkillEffectiveID#65572` and `#65631`, used by `Hunter's Drive`, target visibility selector `11`, which matches `FRIENDLY_ALL_WITHOUT_SELF` from the SDK enum. This is a good proof that the visibility target enum is really being consumed verbatim.
- `SkillEffectiveID#66152`, used by `Soaring Javelin` and `Take Aim`, applies a visibility stage to target selector `20`, i.e. `ENEMY_ALL`, which proves the same system is not limited to one-on-one stealth effects.

#### CotC-only extra fields
- `m_MagicStonePerformance`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** CotC-specific magic-stone performance asset or mode.
- `m_MagicStonePerformanceStartTime`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Start time for the previous field.
- `m_MagicStoneLevel`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Level or tier selector for magic-stone presentation.
- `m_CameraShakeCurve`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Camera-shake asset/curve.
- `m_CameraShakeStartTime`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Start time for the camera shake.
- `m_Text2nd`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Second text bank/secondary floating text.
- CotC Global 3.22.10 reflected `SkillEffectiveIDBase` stopped at `m_Text2nd` in the older IDA-applied lane. Do not read that as current CotC absence: the supplied CotC 4.2.3 reflected header adds `m_SkillEffectiveLinkageID` immediately after `m_Text2nd` (`libUE4.so.h`:59939-59946).
- `m_SkillEffectiveLinkageID`: **Confirmed from CotC 4.2.3 reflected header.** Linkage-row selector into `SkillEffectiveLinkageListBase`.
- `m_IsSetVisibleUI`: **Still unresolved as a data-table field in the supplied CotC 4.2.3 header/decomp recheck.** The binary contains `AcqWidgetBase::execSetVisibleUI` and `BattleWidgetBase::execSetVisibleUI`, so UI visibility logic exists in code, but the checked schema does not prove this exact field name in `SkillEffectiveIDBase`.
- `m_UIVisiblityFlag`: **Still unresolved as a data-table field in the supplied CotC 4.2.3 header/decomp recheck.**
- `m_UIVisibilityTarget`: **Still unresolved as a data-table field in the supplied CotC 4.2.3 header/decomp recheck.**
- `m_UIVisibilityStartTime`: **Still unresolved as a data-table field in the supplied CotC 4.2.3 header/decomp recheck.**

#### OT0 versus CotC note
- `Camera schema shift`: OT0 keeps camera rows inline through `m_Cameras` and `m_CameraTimes`. The CotC `SkillEffectiveID` schema in the exported `Skill/` folder drops those inline arrays and instead expands secondary UI/effect linkage fields.

### `SkillAilmentType`
Reusable ailment/state package. This file covers classic negative statuses, positive buffs, counters, intercepts, reraise states, evasion states, damage-limit auras, permanent states, and many other timed or triggered effects. Newly added entries can participate in the data-driven parts of this table, but they do not inherit hardcoded native behavior from an existing ailment merely by copying its label. Native behavior is keyed by numeric `m_id`, while `m_Label` is an authoring label.

- **OT0 rows:** `490`.
- **CotC rows:** `968`.
- **OT0 SDK row struct:** `FSkillAilmentTypeBase`.
- **Related OT0 SDK enums:** `Kingship::ESKILL_INVOKE`, `ESKILL_AILMENT_CALC_TYPE`.
- This is one of the densest and most important tables in the entire skill system. `SkillAvailID` frequently does very little by itself beyond pointing here.

#### Exact OT0 SDK enum quotes that directly drive this row
##### `Kingship::ESKILL_INVOKE` (timing enum)
Quoted from `SDK/Kingship_structs.hpp:6436`.
```cpp
enum class Kingship::ESKILL_INVOKE : uint8
{
	NONE                                     = 0,
	COMMAND                                  = 1,
	BATTLING                                 = 2,
	BATTLE_1ST                               = 3,
	BATTLE_END                               = 4,
	TURN_1ST                                 = 5,
	TURN_END                                 = 6,
	ACTION_1ST                               = 7,
	ACTION_BEGIN                             = 8,
	ACTION_DAMAGE                            = 9,
	ACTION_FINISH                            = 10,
	ACTION_LAST                              = 11,
	CHANGE_FWD                               = 12,
	CHANGE_BAK                               = 13,
	FORWARD                                  = 14,
	BACKUP                                   = 15,
	JUST_BREAK                               = 16,
	SLIP_DAMAGE                              = 17,
	GUARDING                                 = 18,
	TURN_BEGIN                               = 19,
	TURN_FINISH                              = 20,
	BEFORE_COMMAND_SELECTION                 = 21,
	BATTLING_SLIP_DAMAGE                     = 22,
	BATTLETURN_MODECHANGE                    = 23,
	FORWARD_AND_BACKUP                       = 24,
	COMMAND_PHASE                            = 25,
	ON_BREAKED                               = 26,
	BUFF_LIMIT_CALCULATING                   = 27,
	JUST_DYING                               = 28,
	ANY_CHARA_ACTION_LAST                    = 29,
	FIELD_ACTION                             = 50,
	FIELDING                                 = 51,
	ALWAYS                                   = 100,
	ESKILL_MAX                               = 101,
};
```
- `SkillAilmentType.m_InvokeTiming` uses the same timing enum family that `SkillAvailID.m_InvokeTiming` uses. The engine therefore schedules ailment ticks and stage execution on the same global timing vocabulary.

##### `EAILMENT_CONDITION_ID`
Quoted from `SDK/Kingship_structs.hpp:531`.
```cpp
enum class EAILMENT_CONDITION_ID : uint8
{
	None                                     = 0,
	BREAK_ENFORCER                           = 1,
	BREAK_DAMAGE                             = 2,
	SKILL_DAMAGE                             = 10,
	PHYSICAL_DAMAGE                          = 11,
	MAGICAL_DAMAGE                           = 12,
	TURN_PROBABLITY                          = 13,
	ENFORCER_ALIVE_LE_AILMENT                = 20,
	ENFORCER_ALIVE_LE_AVAIL                  = 21,
	DEAD_ENFORCER                            = 30,
	BREAK_OR_DEAD_ENFORCER                   = 31,
	CONCURRENT_LINKAGE                       = 40,
	CONCURRENT_LINKAGE_WITHOUT_PERMANENT     = 41,
	CAN_NOT_ACTION                           = 42,
	EAILMENT_CONDITION_MAX                   = 43,
};
```
- `m_RemoveConditions` stores raw values from this ailment-condition enum. This is why rows like sleep, countdown, or break-linked states can express precise removal triggers such as `PHYSICAL_DAMAGE`, `BREAK_OR_DEAD_ENFORCER`, or `CAN_NOT_ACTION`.

##### `ESKILL_AILMENT_CALC_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6208`.
```cpp
enum class ESKILL_AILMENT_CALC_TYPE : uint8
{
	NONE                                     = 0,
	CALC_DAMAGE                              = 1,
	CALC_CRITICAL_DAMAGE                     = 2,
	CALC_WEAK_DAMAGE                         = 3,
	CALC_BREAK_DAMAGE                        = 4,
	CALC_ATRIBUTE_BUFF_DAMAGE                = 5,
	CALC_ATRIBUTE_DEBUFF_DAMAGE              = 6,
	CALC_ALLY_DAMAGE                         = 7,
	CALC_RACE_BUFF_DAMAGE                    = 8,
	CALC_BUFF_LIMIT_STATUS                   = 9,
	CALC_BUFF_LIMIT_STATUS_SUP               = 10,
	ESKILL_AILMENT_CALC_MAX                  = 11,
};
```
- `m_AilmentCalc` stores raw values from this enum. This is separate from `SkillAvailID.m_CalcTypeAilment`: the avail-side field chooses how an ailment value is stored, while this ailment-side field chooses whether an active ailment contributes to damage rating and under which condition.
- `m_ResistWeapon` and `m_ResistMagic` should be read against the exact `EWEAPON_TYPE` and `EMAGIC_TYPE` quotes embedded above in `WeaponType` and `MagicType`, because those arrays are keyed by the same family order even though the ailment row stores them as masks rather than scalar IDs.
- CotC Global 3.22.10 extends this family beyond OT0 with additional tail values such as `CALC_CRITICAL_DAMAGERATE`, `CALC_BUFF_LIMIT_ATK`, `CALC_BUFF_LIMIT_ATK_SUP`, `CALC_SPECIFIC_SHIELD_DAMAGE`, `CALC_WEAK_DAMAGE_RATE_PLUS`, and `CALC_SLIP_DAMAGE_UP`.
- Do not overread those tail names as already-solved formulas. The safe guide wording is: `1..5` are formula-backed damage-rating lanes; `6..8` are live runtime lanes with clear condition families, but their final ratio/cap composition should remain conservative unless the target build has a fully named caller chain. Apparent matches for later values can be false positives when they come from struct strides, allocation sizes, or row-builder helpers rather than gameplay dispatch.

In the OT0 v1.0.8 native damage path, `AQPBattleCharacterBase::GetRatedDamage` switches on `SkillAilmentType.m_AilmentCalc`, not on `SkillAvailID.m_CalcTypeAilment` (`Octopath_Traveler0-Win64-Shipping.exe.c`:17445633-17446245). The implemented damage-rating cases are:

```text
1 CALC_DAMAGE                  all qualifying damage
2 CALC_CRITICAL_DAMAGE         critical hits only
3 CALC_WEAK_DAMAGE             weakness hits only
4 CALC_BREAK_DAMAGE            broken / no-shield damage window
5 CALC_ATRIBUTE_BUFF_DAMAGE    matching weapon/magic attribute buff
6 CALC_ATRIBUTE_DEBUFF_DAMAGE  matching attribute debuff amplifier
7 CALC_ALLY_DAMAGE             same-side target
8 CALC_RACE_BUFF_DAMAGE        matching enemy race, honoring m_NotRace
```

Enum cases `9` and `10` are real OT0 enum labels, but this damage-rating switch does not implement them as ordinary damage multipliers. Document them as buff/debuff-limit lanes unless another native caller proves a more specific formula.

#### Field-by-field
- `m_id`: **Runtime-proven.** Primary key for the ailment row and the behavior key used by native lookup. `SkillAvailID.m_AddAilment`, `m_SubAilment`, `SkillConditionList.m_AilmentTypes`, `SkillResistAilmentID.m_ResistAilments`, `m_RemoveAilment`, and `m_ChildAilment` all ultimately point at this numeric ID. Hardcoded cases such as poison, sleep, paralysis, countdown, reflect, skip-action, reraise, and avoidance also compare numeric IDs rather than labels.
- `m_Label`: Internal/editor label. This is often the fastest human-readable way to identify what the row is meant to do, but it is not used as the runtime behavior key. Duplicating a label on a new row does not copy the old row's hardcoded logic.
- `m_Resist`: **Runtime-proven.** Enables normal ailment-resistance handling for this row. It is true on classic negative statuses such as poison, bleed, sleep, paralysis, fear, and darkness, and is consulted by functions that decide whether resistance/invalidation should apply.
- `m_Minus`: **Runtime-proven.** Negative/debuff-like ailment flag. `USkillDB::GetAilmentFlag` and plus/minus helpers return a negative sign from this byte when the row passes the requested removable/resist gates.
- `m_Plus`: **Runtime-proven.** Positive/buff-like ailment flag. `USkillDB::GetAilmentFlag` and plus/minus helpers return a positive sign from this byte when the row passes the requested removable/resist gates.
- `m_Removable`: **Runtime-proven.** Whether ordinary cure/clear behavior can remove the ailment. It also participates in plus/minus and buff/debuff classification helpers when callers request removable-only checks.
- `m_Multiply`: **Runtime-proven.** Stacking/coexistence search-key flag. In `SetupAilment`, multiplied ailments with turns or use-counts can clear the concrete skill/avail identity before `FindAilment`, allowing related applications to coexist or merge differently from singleton control states.
- `m_OverwriteTurn`: **Runtime-proven.** Reapplication turn behavior. When true, reapplication can overwrite stored turns instead of using the ordinary extension/reduction path.
- `m_ForceOverrideValue`: **Runtime-proven.** Value override flag read by `USkillDB::IsAilmentForceOverrideValue`. In `FAilmentInfo::SetAilmentValue`, it bypasses ordinary accumulation/strongest-value comparison and writes the incoming value lane directly, with special handling for reraise and value-smaller families.
- `m_InvalidateDebuff`: **Confirmed by usage pattern.** Marks the row as a debuff-class state for interactions that invalidate or suppress debuffs. OT0 sets it on debuff rows such as defense-down, attack-down, weapon-weakening, and element-weakening states.
- `m_CanInactiveFlag`: **Runtime-proven.** Narrow inactive/cancel-state bookkeeping flag, not a general expiration switch. On new ailment setup, ordinary buff/debuff signs can clear the new record's inactive flag if the opposing marker ailment is already present (`522` for one sign, `523` for the other).
- `m_InvokeTiming`: **Runtime-proven.** Trigger timing from the richer `Kingship::ESKILL_INVOKE` enum. OT0 uses timings such as `BATTLING`, `ACTION_DAMAGE`, `SLIP_DAMAGE`, `FORWARD`, `ALWAYS`, and more. Child-ailment propagation has a specific dependency on parent `m_InvokeTiming == 14`.
- `m_TargetStatus`: **Runtime-proven.** `Character/CharaStatusID.m_id` affected by the ailment when it modifies a concrete stat/resource. Status formulas use this field to decide whether an active ailment enters HP, SP, BP, attack, defense, speed, critical, shield, or other status lanes.
- `m_TargetStatusArray`: **Runtime-proven shape.** Extra status targets for multi-status ailments. It is also checked by the ailment-variable and buff/debuff helper paths when a single ailment should affect more than one status.
- `m_AilmentCalc`: **Runtime-proven.** Ailment-side damage-condition mode from `ESKILL_AILMENT_CALC_TYPE`. OT0 stock rows with values `1..8` feed damage-rating families such as all damage, critical damage, weak damage, break damage, attribute buff/debuff damage, same-side damage, and race-filtered damage. Values `9` and `10` are enum-backed limit lanes rather than ordinary `GetRatedDamage` multiplier cases.
- `m_EfficacyStepRate`: **Observed.** Step-rate scaling parameter for ailment efficacy. The row field is copied/defaulted and appears in status-variable helpers, but its final gameplay caller is still weaker than the main value/turn/count fields.
- `m_ResistWeapon`: **Runtime-proven.** Boolean weapon-family mask. In `CheckAvoidableSkills`, the runtime resolves the skill's weapon attribute, respecting `SkillAvailID.m_OverrideWeapon`, and checks this mask to decide whether the ailment can avoid/react to that weapon family. Counter and intercept rows use the same mask family.
- `m_ResistMagic`: **Runtime-proven.** Boolean element-family mask used like `m_ResistWeapon` after resolving the skill's magic attribute and `SkillAvailID.m_OverrideMagic`.
- `m_RemoveAilment`: **Runtime-proven.** Up to four `SkillAilmentType.m_id` rows removed or blocked by the current ailment. `CheckInvalidationAilment` scans existing rows and their remove lists, so these IDs can act as mutual-exclusion and replacement rules.
- `m_TargetType`: **Observed.** Small ailment-internal target selector. OT0 only uses nonzero values on three `...TARGET_IS_ENEMY_ALL` counter/intercept rows, so do not treat it as the full battle target enum.
- `m_RemoveConditions`: **Runtime-proven.** Condition opcode array passed into `USkillDB::CheckRemoveAilment`. The remove check is an all-matched positive-condition test: at least one recognized condition must exist, and every recognized condition must be satisfied.
- `m_RemoveParams`: **Runtime-proven.** Parameter array paired with `m_RemoveConditions`. Conditions such as probabilistic weapon/magic-hit removal and alive-count thresholds read these slots; some alive-count conditions fall back to `SkillAvailID.m_ValueAilment[index]` when the param is the sentinel value.
- `m_ChildAilment`: **Runtime-proven.** `SkillAilmentType.m_id` of a secondary ailment propagated by parent ailments whose invoke timing is `FORWARD` (`14`). Child propagation still runs invalidation, direct ailment resist, support-ability resist, food resist, and support-ability sub-ailment suppression before calling `SetupAilment` for the child.
- `m_TextID`: **Confirmed.** Visible ailment name text ID, primarily in `GameTextUI`. Example: poison, darkness, and buff-state labels resolve here.
- `m_ExplanationTextID`: **Confirmed.** Visible ailment explanation text ID, again primarily in `GameTextUI`.
- `m_IconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the main ailment icon.
- `m_IconSptTexID`: **Confirmed.** `Texture/TextureID.m_id` for the support/secondary ailment icon. In OT0 it usually matches `m_IconTexID`.
- `m_AddEffect`: **Confirmed.** `Effect/EffectList.m_id` spawned when the ailment is added.
- `m_CharacterEffect`: **Confirmed.** `Effect/EffectList.m_id` attached to the afflicted character while the ailment remains active.
- `m_FieldEffect`: **Confirmed.** `Effect/EffectList.m_id` used by field-map variants of the ailment.
- `m_InvokeEffect`: **Confirmed.** `Effect/EffectList.m_id` spawned when the ailment triggers or ticks.
- `m_OnGroundInvoke`: **Observed.** Allow the ailment's invoke/tick behavior outside normal battle action timing, such as field-ground use cases.
- `m_AddEffectSound`: **Confirmed.** `Sound/SoundList.m_id` played when the ailment is applied.
- `m_InvokeSound`: **Confirmed.** `Sound/SoundList.m_id` played when the ailment triggers or ticks.
- `m_ActionID`: **Confirmed.** `Battle/BattleActionID.m_id` animation hook for the ailment state.
- `m_ActionPriority`: **Confirmed unused in OT0.** Present in the struct but OT0 leaves it zero.
- `m_ReplaceCharaID`: **Proven; unused in OT0.** Replaces the user's sprite with the one referenced here. Maps into `CharaID.m_id`.
- `m_CountDown`: **Runtime-proven.** Countdown-style flag read by `USkillDB::IsCountDownAilment`. Countdown ailments bypass ordinary `CalcAilmentTurn` recalculation during setup and later route into countdown UI/death handling. `ReplaceCountDownAilment` is a no-op in this OT0 export.
- `m_NoCount`: **Runtime-proven.** No-turn-count flag read by `USkillDB::IsNoTurnCountAIlment` and copied to `FAilmentInfo.m_IsNoTurnCount`. This is adjacent to `m_CountDown` but not the same thing; `TurnCount == 0` separately creates permanent/no-turn-count style active records.
- `m_StatusDetailSort`: **Observed.** UI ordering key for ailment/detail display. Keep this as a presentation/status-detail field unless a stronger battle caller is recovered.
- `m_IgnoreLimit`: **Runtime-proven.** Clamp bypass for buff/debuff-like variable-ratio values. When false, `FAilmentInfo::SetAilmentValue` calls `AQPBattleManager::ClampVariableRatio`; when true, that clamp is skipped.
- `m_RemainDead`: **Runtime-proven.** Dead-state retention flag checked in death cleanup paths. It should be read as retention across death/dead cleanup, not as general immunity to ailment removal.
- `m_IsNotMemorize`: **Observed.** Rare memory/snapshot exclusion flag. OT0 only uses it on two rows; keep it as data-carry or status-memory behavior unless a stronger caller is recovered.
- `m_AbsorbHealStatus`: **Confirmed.** `Character/CharaStatusID.m_id` restored by absorb/heal-style ailment logic.
- `m_NotRace`: **Runtime-proven.** Inverts or negates the race filter in `m_EnemyRace` for race-targeted damage reduction and damage-rating logic.
- `m_EnemyRace`: **Runtime-proven.** Race-filter array for race-targeted ailment logic. `CALC_RACE_BUFF_DAMAGE` and race-reduction helpers check this array, honoring `m_NotRace`.
- `m_ForceSupportAbility`: **Confirmed unused in OT0.** Always false in the checked stock data.
- `m_ForceCommandAbility`: **Observed.** Rare flag set on standard negative statuses like poison, bleed, paralysis, and darkness. The safest reading is that it keeps these rows in a command-ability interaction path, but OT0 does not give a better named caller in the SDK dump.
- `m_WeaponAppendType`: **Runtime-proven.** Active-state weapon appended-weakness selector. During damage, the enforcer's active ailment list is scanned; if this field names an appended weakness row, the runtime can upgrade the current weak rate based on weapon slots in that weakness row.
- `m_MagicAppendType`: **Runtime-proven.** Active-state magic appended-weakness selector, parallel to `m_WeaponAppendType` for element slots.
- `m_DuplicateAilment`: **Observed.** Allow duplicate instances of the same ailment. OT0 uses it on only one row.
- `m_SkillReplace`: **Confirmed unused in OT0.** Reserved skill-replacement hook, most likely using `SkillAilmentSkillReplaceList.m_id`.

#### Concrete OT0 examples
- Plain status families are easy to see directly: `SkillAilmentType#1 POISON` is `Poison`, `#8 HIGH_POISON` is `Toxin`, `#2 BLEED` is `Bleeding`, and `#6 FEAR` is `Terror`.
- `m_RemoveConditions` is not decorative. `SkillAilmentType#4 SLEEP` uses `m_RemoveConditions = [10,0]`, while `#285 DEEP_SLEEP` uses `[1,0]`; OT0 is clearly distinguishing different removal/break logic families inside the same broad sleep theme.
- The counter/intercept families show why `m_ResistWeapon` and `m_ResistMagic` are better read as reaction masks than as ordinary resistance tables. `#52 COUNTER_SWORD` and `#70 INTERCEPT_SWORD` are representative weapon-specific rows in those families.
- `m_CountDown` is a real gameplay flag. `SkillAilmentType#20 COUNTDOWN` is `Fatal Sentence`, while `#43 DELAYED_SKILL` and `#44 DELAYED_RANGE_SKILL` are both visible `Countdown` variants that trigger queued skills at count end.
- `m_ForceCommandAbility` is not random noise. OT0 sets it on classic negative statuses such as `#1 POISON`, `#2 BLEED`, `#5 PARALYSIS`, and `#11 DARKNESS`.
- `m_IgnoreLimit` is also real. `#203 BUFF_SP_COST_REDUCE (SP Cost Down)`, `#226 BUFF_REDUCE_DAMAGE_HP (Reduce Damage)`, and `#397 INCREASE_DAMAGE_LIMIT (Surpassing Power)` all carry it, which fits the idea that they bypass normal buff/debuff-cap handling.
- `m_RemainDead` is not limited to one row. `#283 RERAISE_BUFF (Encore)` and `#503 RERAISE_BUFF_LAST_DIED (Last Man Standing)` both use it, which is exactly what you would expect from states that still matter after incapacitation.

#### OT0 runtime lookup, storage, and active state
`SkillAilmentType` is loaded as a master-data table, not as live battle state. OT0 stores the definition table under `ADatabaseDefineStatic::m_SkillAilmentType`, with `m_DBAccess`, `m_DataList`, and `m_Loaded`. The master-data loader treats it as database ID `38`, and `USkillDB::CheckAilmentDB` requires the ailment type table, the ailment offsets table, and the ailment-resist table to be loaded before ailment DB helpers are considered safe.

The lookup path is numeric:

```text
USkillDB::GetSkillAilmentDB(AilmentID)
  -> ADatabaseDefineStatic::m_SkillAilmentType.m_DBAccess
  -> FDbAccessHelper<FSkillAilmentTypeBase>::GetArrayIndex(AilmentID)
  -> m_DataList[ArrayIndex]
```

`FDbAccessHelper<FSkillAilmentTypeBase>::Init` builds its ID array from `row.m_id`, not `row.m_Label`. If IDs are contiguous, lookup can use direct indexing; if not, it builds an access table keyed by `m_id`. This is the direct mechanical reason that cloned labels do not clone behavior.

The live record is `FAilmentInfo`, stored on each battler in `AQPBattleCharacterBase.m_AilmentInfo`. `FindAilment` searches that active array by the stored numeric ailment type. Setup, refresh, turn decrement, child propagation, use-count updates, and clear/removal all mutate the live `FAilmentInfo` records rather than the master table.

#### OT0 ailment application and lifecycle
The usual apply path is:

```text
AQPBattleManager::AddAilments
  -> USkillDB::GetAvailAilment(pAvail, index)
  -> target resist / invalidation / pick / lottery checks
  -> AQPBattleCharacterBase::AddAilment
  -> AQPBattleCharacterBase::SetupAilment
```

`SetupAilment` is best read as a multi-stage state machine:

1. It rejects invalidated ailments and immediate-only removal pseudo-ailments that are not allowed to become persistent state.
2. It normalizes no-move cover and condition-pre-replaced skill IDs before deciding whether the ailment originates from command, support, or special-skill logic.
3. If `m_Multiply` is active and either turns or use-counts are present, the search key changes. Most multiplied ailments clear concrete skill/avail identity before `FindAilment`, while special exceptions retain stricter matching.
4. Non-countdown ailments run turn recalculation through the applier and receiver. Countdown ailments explicitly bypass that recalculation branch.
5. New `FAilmentInfo` records are allocated with type, skill ID, avail ID, ailment index, source/overrider, no-turn-count flags, support/special flags, and can-inactive bookkeeping.
6. Existing records run value/update logic through `FAilmentInfo::SetAilmentValue`, `UpdateSkill`, `UpdateInfo`, use-info matching, count-reference updates, and optional denial/removal checks.

`FAilmentInfo::SetAilmentValue` is where many row flags become formula-visible. `m_ForceOverrideValue` bypasses ordinary strongest-value/accumulation behavior and writes the incoming lane directly. `m_IgnoreLimit` bypasses `ClampVariableRatio` for buff/debuff-like variable-ratio values. Value-smaller families such as paralysis/fascination-like rows invert the usual strongest-value comparison so that lower values can be the stronger result.

#### OT0 remove-condition system
For ordinary rows, `m_RemoveConditions` and `m_RemoveParams` are passed directly into `USkillDB::CheckRemoveAilment`. Two runtime overrides exist before that check:

```text
AilmentID == 37  -> use EnemyDB weak-lock remove conditions from SkillAvailID.m_WeakLockID
AilmentID == 306 -> use EnemyDB mode-change remove conditions from SkillAvailID.m_ModeChangeID
```

The condition check is an all-matched positive-condition test. It counts recognized active conditions and satisfied conditions, then returns true only when at least one recognized condition exists and every recognized condition is satisfied.

| Condition | Meaning recovered from OT0 C |
| --- | --- |
| `1` | enforcer is breaking |
| `2` | target/character is breaking |
| `10` | probabilistic removal when a weapon or magic attribute is present; param is the probability threshold |
| `11` | weapon attribute present |
| `12` | magic attribute present |
| `20` / `21` | alive-count condition; param supplies the threshold, or falls back to `SkillAvailID.m_ValueAilment[index]` when param is sentinel `0x7FFFFFFF` |
| `30` | enforcer is dead |
| `31` | enforcer is breaking or dead |
| `42` | enforcer is dead/charmed/skip-action after targetability checks |

If the remove-condition check fails and the avail is not command timing, `IsRemoveAilment` can fall back to the avail invoke-condition failure path. That makes removal a fixed native condition switch, not a freeform scripting array.

#### OT0 child-ailment propagation
`m_ChildAilment` is not applied just because the field is populated. `SetupChildAilment` requires:

```text
GetAilmentInvoke(parent) == 14
GetChildAilment(parent) > 0
```

When applying the child, the runtime iterates a target array, checks duplicates by child ailment ID plus parent skill ID, parent avail ID, and special/support flag, then runs invalidation and resistance checks before calling `SetupAilment` for the child. The resistance side includes direct ailment resist, support-ability resist, food ailment resist, and support-ability sub-ailment suppression. When removing, the same target pipeline calls `SubAilment(child)`. This is why child ailments can feel attached to the parent while still behaving as real independent ailment applications.

#### Exact OT0 native ailment cases and hardcoded ID families
Most row fields are data-driven, but many important families also have hardcoded numeric-ID behavior. The following table lists the native ID families that should not be cloned by label alone.

##### Target selector to ailment ID bridge
`USkillDB::GetTargetAilment` maps specific target selectors to fixed ailment IDs:

| Target selector family | Returned ailment ID |
| --- | ---: |
| `ENEMY_1_POISON` | `1` |
| `ENEMY_1_BLEED` | `2` |
| `ENEMY_1_SILENCE` | `3` |
| `ENEMY_1_SLEEP` | `4` |
| `ENEMY_1_PARALYSYS` | `5` |
| `ENEMY_1_FEAR` | `6` |
| `ENEMY_1_WASTE` | `7` |
| `ENEMY_1_HEADACHE` | `18` |
| `ENEMY_1_ENERGYDRAIN` | `19` |
| `ENEMY_1_COUNTDOWN` | `20` |
| `ENEMY_1_PROVOKE` | `21` |
| `ENEMY_1_REGENERATE` | `22` |
| `ENEMY_1_SP_REGENERATE` | `23` |
| `ENEMY_1_BP_REPAIR` | `24` |
| `ENEMY_1_SHIELD_REPAIR` | `25` |
| `ENEMY_1_ROBUST` | `26` |
| `ENEMY_1_COUNTER` | `52` |
| `ENEMY_1_INTERCEPT` | `70` |
| stat-buff target selectors | `106..111` |
| stat-debuff target selectors | `122..127` |
| weapon-buff target selectors | `138..145` |
| weapon-debuff target selectors | `146..153` |
| magic-buff target selectors | `154..160` |
| magic-debuff target selectors | `161..167` |

##### Hardcoded status families
| Ailment ID / family | Native behavior |
| --- | --- |
| `1 POISON` / `8 HIGH_POISON` | Poison/high-poison conversion is handled by `ReplaceAllogeanicAilment`. Both map to `INVALIDATE_POISON`, share poison effect offsets, and run HP-ratio slip damage through `CalcPoisonDamage`. Poison damage uses target max HP, caps player max HP by the player status DB cap, and divides by `BattleParam[7]` for bosses or `BattleParam[8]` for non-bosses. |
| `2 BLEED` / `9 HIGH_BLEED` | Bleed/high-bleed conversion mirrors poison through `ReplaceAllogeanicAilment`, maps to `INVALIDATE_BLEED`, shares bleed effect offsets, and uses bleed-specific battle params in the poison-damage helper for enemy targets. |
| `3 SILENCE` | Maps to `INVALIDATE_SILENCE`, uses the silence effect-offset group, and participates in `IsSkipActionCommand`. Quiet/cursed silence variants use related IDs such as `271`, `286`, and `287`. |
| `4 SLEEP`, `285 DEEP_SLEEP`, `1003 NO_ACTION` | `IsSleeping` returns true for these IDs. Sleep maps to `INVALIDATE_SLEEP`, uses the sleep effect-offset group, and can cause action skip/removal from active action queues. `395 FASCINATION` shares part of the sleep effect-offset family but uses its own skip chance path. |
| `5 PARALYSIS` | Maps to `INVALIDATE_PARALYSIS`, can be reflected by support abilities, and can skip action through `CheckParalysis`. It is a value-smaller family for ailment-value comparison. |
| `6 FEAR` | Maps to `INVALIDATE_FEAR` and can skip boosted actions: if the current skill has a positive required boost level, fear can block the command. |
| `7 WASTE` | Maps to `INVALIDATE_WASTE`, has its own wasting check, and uses its own effect-offset group. Most value effects remain row-driven. |
| `10 DARKNESS` | Maps to `INVALIDATE_DARKNESS`, can be reflected by support abilities, and contributes to the physical-hit darkness/blindness multiplier through related hit logic. |
| `11`, `430`, `431`, `432` | Effect-switching family. `IsAilmentEffectSwitching` returns true only for these IDs, and `IsAilmentValueSmaller` also includes them. |
| `18 HEADACHE` | Maps to `INVALIDATE_HEADACHE` and has a specific effect-offset group. |
| `19 ENERGYDRAIN` | Maps to `INVALIDATE_ENERGYDRAIN` and has a specific effect-offset group. |
| `20 COUNTDOWN` | Countdown case in `AQPBattleManager::AvailAilment`. On expiry/tick, it can set HP damage to current HP, temporarily disable no-death protection, apply lethal damage, then restore no-death state if needed. Rows with `m_CountDown` also use countdown effect offsets. |
| `21 PROVOKE` | Has a specific effect-offset group. Provoke-like child states can be added through the `AvailAilment` child branch. |
| `22 REGENERATE` | Calls `CalcAilmentRegenerate`; HP regeneration applies cure modifiers and tracks `m_RegenerateHP` before writing the status delta. |
| `23 SP_REGENERATE` | Parallel SP regeneration path, tracking `m_RegenerateSP` and applying cure modifiers for SP. |
| `24 BP_REPAIR` / `25 SHIELD_REPAIR` | Mostly data-driven repair families. BP repair also adjusts reserved BP/boost state when the target status is the BP lane. |
| `26 ROBUST` | Blocks incoming removable negative ailments while robust is active. `SetupAilment` also treats robust as an exception in the special/support classification branch. |
| `42 ABSORB_MAGIC` | Rotates absorber type through resist data during turn updates. Matching incoming magic can heal through the magic absorber healing path. |
| `43 DELAYED_SKILL` / `44 DELAYED_RANGE_SKILL` | End-turn delayed-skill route. When active, the runtime reads the delayed skill ID from the source avail and stores delayed skill info. `DELAYED_RANGE_SKILL` has duplicate guards. |
| `50 OFF_BALANCE` | Action-skip family unless ignored by the caller. Break-related poison logic can add an off-balance/poison follow-up. |
| `52 COUNTER` / `70 INTERCEPT` | Target-selector bridge IDs for counter and intercept families. Effect offsets depend on `GetCounterAttackFlag`, and row masks decide weapon/magic reaction families. |
| `222`, `263`, `264`, `265`, `266`, `1005`, `1006`, `1007` | Avoidance-type hardcoded family. `GetAvoidanceType` returns positive or negative avoidance polarity for these IDs. |
| `283`, `284` | Reraise family. `USkillDB::IsReraiseAilment` hardcodes these two IDs, and clear/death cleanup treats reraise-like records specially. |
| `395 FASCINATION` | Skip-action family using a random/value check similar to paralysis. Also included in `IsAilmentValueSmaller` and the sleep-like effect-offset group. |

##### Support-reflect whitelist
`UPlayerDataUtility::CheckSupportAbilityAilmentReflect` only reflects the following ailment IDs in the recovered OT0 path:

```text
1 POISON
2 BLEED
4 SLEEP
5 PARALYSIS
8 HIGH_POISON
9 HIGH_BLEED
10 DARKNESS
```

A new row with the same label as poison, bleed, sleep, paralysis, or darkness will not reflect unless its numeric ID is added to the native whitelist or mapped to one of those IDs.

##### Invalidation and effect-offset tables
`USkillDB::GetInvalidateAilment` hardcodes canonical invalidation mappings for poison/high-poison, bleed/high-bleed, silence, sleep, paralysis, fear, waste, darkness, headache, and energy drain. `USkillDB::GetAilmentEffectOffset` also hardcodes effect-offset families for poison, bleed, silence, sleep/deep-sleep/fascination/no-action, paralysis, fear/darkness, waste, effect-switching IDs, headache, energy drain, provoke, regenerate, countdown rows, counter rows, and one special `51` group.

These tables are another reason labels are not behavior. A cloned row can inherit icons/text/effects through data, but it will not enter these native mapping tables unless the numeric ID is one of the checked IDs or the row has a data-driven flag that the mapping function honors, such as `m_CountDown` for countdown offsets.

#### CotC Global 4.2.3 schema and target-family corridor
CotC Global 4.2.3 keeps the same broad `SkillAilmentType` concept but widens the row. The 4.2.3 header confirms `SkillAilmentTypeBase` fields absent from OT0, including `m_CompareTurn`, `m_CompareOverrideValue`, `m_DisableBoostAnim`, the status-detail text family, `m_AllowAddAilmentOnDead`, `m_MaxUseCount`, `m_IsAttributeCheckAppendType`, `m_BindWeakID`, `m_IsAdditionWeak`, `m_AilmentTagID`, `m_Attribute`, `m_TargetAilment`, `m_ToUnlimited`, `m_Parallel`, `m_PhaseOver`, and `m_SkillFilterType`.

CotC Global 4.2.3 also extends `ESKILL_AILMENT_CALC_TYPE` through:

```text
11 CALC_CRITICAL_DAMAGERATE
12 CALC_BUFF_LIMIT_ATK
13 CALC_BUFF_LIMIT_ATK_SUP
14 CALC_SPECIFIC_SHIELD_DAMAGE
15 CALC_WEAK_DAMAGE_RATE_PLUS
16 CALC_SLIP_DAMAGE_UP
17 CALC_HP_DOWN_RATE_VALUE_UP
18 CALC_BUFF_LIMIT_RESIST
19 CALC_BUFF_LIMIT_RESIST_SUP
20 CALC_BIND_WEAK_COUNT_PLUS
21 CALC_BIND_WEAK_COUNT_MINUS
22 CALC_BUFF_LIMIT_WEAK_ATK
23 CALC_BUFF_LIMIT_WEAK_ATK_SUP
24 CALC_WEAPON_ATTACK_AND_SKILL
25 CALC_MAGIC_ATTACK_AND_SKILL
26 CALC_MULTI_ATTR_VALUE_RATE_UP
27 CALC_MULTI_ATTR_SP_COST_REDUCE
28 ESKILL_AILMENT_CALC_MAX
```

Treat these as exact CotC 4.2.3 enum surfaces, not OT0 backports. The header proves the names and values; the stripped mobile C still needs separate caller recovery before every tail formula should be described as fully solved.

CotC's target-selector ailment band is also shifted from OT0. In 4.2.3, the ailment target selectors occupy `0xC4..0xD5`:

```text
196 ENEMY_1_POISON
197 ENEMY_1_BLEED
198 ENEMY_1_SILENCE
199 ENEMY_1_SLEEP
200 ENEMY_1_PARALYSYS
201 ENEMY_1_FEAR
202 ENEMY_1_WASTE
203 ENEMY_1_HEADACHE
204 ENEMY_1_ENERGYDRAIN
205 ENEMY_1_COUNTDOWN
206 ENEMY_1_PROVOKE
207 ENEMY_1_REGENERATE
208 ENEMY_1_SP_REGENERATE
209 ENEMY_1_BP_REPAIR
210 ENEMY_1_SHIELD_REPAIR
211 ENEMY_1_ROBUST
212 ENEMY_1_COUNTER
213 ENEMY_1_INTERCEPT
```

Do not port OT0 target-type integers directly into CotC. OT0's `GetTargetAilment` maps its own selector values to ailment IDs, while CotC 4.2.3 uses a later selector band and a wider target enum tail with `MOVED_PLAYER = 230`, `AFTERSKILL_ENFORCER = 231`, `ENEMY_PAIR = 232`, and `ESKILL_TARGET_MAX = 233`.

#### CotC-only extra fields
- `m_CompareTurn`: **Confirmed from CotC Global 4.2.3 header.** Additional turn-comparison parameter beside `m_OverwriteTurn`.
- `m_CompareOverrideValue`: **Confirmed from CotC Global 4.2.3 header.** Additional value-comparison parameter beside `m_ForceOverrideValue`.
- `m_DisableBoostAnim`: **Confirmed from CotC Global 4.2.3 header.** Suppress boost animation when the ailment fires or presents itself.
- `m_StatusDetailAllName`: **Confirmed from CotC Global 4.2.3 header.** Richer status-detail name text ID for all-target displays.
- `m_StatusDetailName`: **Confirmed from CotC Global 4.2.3 header.** Richer status-detail name text ID for single-target displays.
- `m_StatusDetailCommentTextID`: **Confirmed from CotC Global 4.2.3 header.** Comment text for status-detail UI.
- `m_StatusDetailCommentCalcTextID`: **Confirmed from CotC Global 4.2.3 header.** Calculation-aware comment text for status-detail UI.
- `m_StatusDetailCommentRatedTextID`: **Confirmed from CotC Global 4.2.3 header.** Rated comment text for status-detail UI.
- `m_StatusDetailCommentNowRatedTextID`: **Confirmed from CotC Global 4.2.3 header.** Current-value rated comment text for status-detail UI.
- `m_StatusDetailCommentFixedTextID`: **Confirmed from CotC Global 4.2.3 header.** Fixed-value comment text for status-detail UI.
- `m_StatusDetailAllCommentTextID`: **Confirmed from CotC Global 4.2.3 header.** All-target comment text.
- `m_StatusDetailAllCommentCalcTextID`: **Confirmed from CotC Global 4.2.3 header.** All-target calculation-aware comment text.
- `m_StatusDetailAllCommentRatedTextID`: **Confirmed from CotC Global 4.2.3 header.** All-target rated comment text.
- `m_StatusDetailAllCommentNowRatedTextID`: **Confirmed from CotC Global 4.2.3 header.** All-target current-rated comment text.
- `m_StatusDetailAllCommentFixedTextID`: **Confirmed from CotC Global 4.2.3 header.** All-target fixed-value comment text.
- `m_AllowAddAilmentOnDead`: **Confirmed from CotC Global 4.2.3 header.** Permit application to dead units.
- `m_MaxUseCount`: **Confirmed from CotC Global 4.2.3 header.** Maximum number of times the ailment can fire.
- `m_IsAttributeCheckAppendType`: **Confirmed from CotC Global 4.2.3 header; semantic role observed from name.** Attribute-aware append-type validation.
- `m_BindWeakID`: **Confirmed from CotC Global 4.2.3 header.** Weakness-binding helper row or key.
- `m_IsAdditionWeak`: **Confirmed from CotC Global 4.2.3 header; semantic role observed from name.** Marks the ailment as part of CotC's additional-weakness mechanics.
- `m_AilmentTagID`: **Confirmed from CotC Global 4.2.3 header.** Tag/group identifier array for the ailment.
- `m_Attribute`: **Confirmed from CotC Global 4.2.3 header.** Attribute selector for CotC ailment logic.
- `m_TargetAilment`: **Confirmed from CotC Global 4.2.3 header.** Target ailment ID for transformation/interaction logic.
- `m_ToUnlimited`: **Confirmed from CotC Global 4.2.3 header; semantic role observed from name.** Convert to unlimited duration.
- `m_Parallel`: **Confirmed from CotC Global 4.2.3 header; semantic role observed from name.** Allow parallel coexistence of related states.
- `m_PhaseOver`: **Confirmed from CotC Global 4.2.3 header; semantic role observed from name.** Persist through phase transitions.
- `m_SkillFilterType`: **Confirmed from CotC Global 4.2.3 header.** `SkillFilterType.m_id` array used by CotC filter UI and categorization.
- `m_IsBuffDebuff`: **Not present in the supplied CotC Global 4.2.3 header.** Treat older mentions of this name as version-specific or schema-export-specific until a current 4.2.3 row layout proves it.


#### Runtime meaning by field group
- **Identity fields** (`m_id`, `m_Label`, text/icon fields) are not equivalent. `m_id` is the native behavior key; `m_Label` is an authoring label; icon/text fields are presentation.
- **Timing fields** (`m_InvokeTiming`, `m_InvokeCondition`, turn/count fields, invalidation fields) decide when an active ailment contributes or expires. The static row defines defaults, while the active `FAilmentInfo` record stores the applied source, value, turn, count, and flags.
- **Value fields** (`m_TargetStatus`, `m_Plus`, `m_Minus`, `m_AilmentCalc`, buff/debuff limit fields, fixed/rated/now-rated lanes) decide how the active record participates in damage, status, cost, cap, and display calculations.
- **Force/permission fields** (`m_ForceSupportAbility`, `m_ForceCommandAbility`, skill-replace fields, no-action/avoid/counter-like families) are often consumed by hardcoded helpers keyed by numeric ailment ID or exact row fields. Copying a label does not copy those native branches.
- **Child/chain fields** (`m_ChildAilment`, switch fields, duplicate/use-info fields) make ailments interact with each other. When cloning a status family, clone the entire chain or deliberately break it.
- **Removal/resistance fields** interact with both the target's resist package and the source avail. A status can be present in `m_AddAilment` but fail because resistance or remove-condition logic rejects it.

<details>
<summary>Evidence: active ailments are source-aware runtime records</summary>

```cpp
SkillAvailID.m_AddAilment[i]
  -> SkillAilmentType.m_id
  -> FAilmentInfo { AilmentType, source SkillID, source AvailID, turns, counts, values }
```

</details>

### `SkillAilmentOffsets`
Per-model ailment anchor preset. This table lives under `Skill/`, but its most obvious downstream OT0 consumer is `Enemy/EnemyTypeID.m_AilmentOffset` rather than `SkillID` itself.

- **OT0 rows:** `853`.
- **CotC rows:** `922`.
- **OT0 SDK row struct:** `FSkillAilmentOffsetsBase`.
- The row exists because ailment icons/effects need different anchor positions on different enemy body shapes. OT0 stores one preset row containing all ailment-family anchor pairs.

#### Field-by-field
- `m_id`: **Confirmed.** Offset preset ID. OT0 `Enemy/EnemyTypeID.m_AilmentOffset` points here.
- `m_PoisonX`: **Confirmed by field name and downstream usage.** Poison anchor X in the preset row for that battler model/type.
- `m_PoisonY`: **Confirmed by field name and downstream usage.** Poison anchor Y in the preset row for that battler model/type.
- `m_BleedX`: **Confirmed by field name and downstream usage.** Bleed anchor X in the preset row for that battler model/type.
- `m_BleedY`: **Confirmed by field name and downstream usage.** Bleed anchor Y in the preset row for that battler model/type.
- `m_SilenceX`: **Confirmed by field name and downstream usage.** Silence anchor X in the preset row for that battler model/type.
- `m_SilenceY`: **Confirmed by field name and downstream usage.** Silence anchor Y in the preset row for that battler model/type.
- `m_SleepX`: **Confirmed by field name and downstream usage.** Sleep anchor X in the preset row for that battler model/type.
- `m_SleepY`: **Confirmed by field name and downstream usage.** Sleep anchor Y in the preset row for that battler model/type.
- `m_PalalysisX`: **Confirmed by field name and downstream usage.** Paralysis anchor X in the preset row for that battler model/type.
- `m_PalalysisY`: **Confirmed by field name and downstream usage.** Paralysis anchor Y in the preset row for that battler model/type.
- `m_FearX`: **Confirmed by field name and downstream usage.** Fear anchor X in the preset row for that battler model/type.
- `m_FearY`: **Confirmed by field name and downstream usage.** Fear anchor Y in the preset row for that battler model/type.
- `m_WasteX`: **Confirmed by field name and downstream usage.** Waste/SP-waste anchor X in the preset row for that battler model/type.
- `m_WasteY`: **Confirmed by field name and downstream usage.** Waste/SP-waste anchor Y in the preset row for that battler model/type.
- `m_DarknessX`: **Confirmed by field name and downstream usage.** Darkness anchor X in the preset row for that battler model/type.
- `m_DarknessY`: **Confirmed by field name and downstream usage.** Darkness anchor Y in the preset row for that battler model/type.
- `m_HeadacheX`: **Confirmed by field name and downstream usage.** Headache anchor X in the preset row for that battler model/type.
- `m_HeadacheY`: **Confirmed by field name and downstream usage.** Headache anchor Y in the preset row for that battler model/type.
- `m_EnergyDrainX`: **Confirmed by field name and downstream usage.** Energy-drain anchor X in the preset row for that battler model/type.
- `m_EnergyDrainY`: **Confirmed by field name and downstream usage.** Energy-drain anchor Y in the preset row for that battler model/type.
- `m_RegenerateX`: **Confirmed by field name and downstream usage.** Regenerate anchor X in the preset row for that battler model/type.
- `m_RegenerateY`: **Confirmed by field name and downstream usage.** Regenerate anchor Y in the preset row for that battler model/type.
- `m_ProvokeX`: **Confirmed by field name and downstream usage.** Provoke anchor X in the preset row for that battler model/type.
- `m_ProvokeY`: **Confirmed by field name and downstream usage.** Provoke anchor Y in the preset row for that battler model/type.
- `m_CounterX`: **Confirmed by field name and downstream usage.** Counter anchor X in the preset row for that battler model/type.
- `m_CounterY`: **Confirmed by field name and downstream usage.** Counter anchor Y in the preset row for that battler model/type.
- `m_InterceptX`: **Confirmed by field name and downstream usage.** Intercept anchor X in the preset row for that battler model/type.
- `m_InterceptY`: **Confirmed by field name and downstream usage.** Intercept anchor Y in the preset row for that battler model/type.
- `m_BreakX`: **Confirmed by field name and downstream usage.** Break anchor X in the preset row for that battler model/type.
- `m_BreakY`: **Confirmed by field name and downstream usage.** Break anchor Y in the preset row for that battler model/type.
- `m_CountDownX`: **Confirmed by field name and downstream usage.** Countdown anchor X in the preset row for that battler model/type.
- `m_CountDownY`: **Confirmed by field name and downstream usage.** Countdown anchor Y in the preset row for that battler model/type.
- `m_PerfectInterceptScale`: **Observed.** Extra scale value for perfect-intercept style display. OT0 populates it on every row.


#### Runtime meaning
- `SkillAilmentOffsets` is a coordinate/helper table for ailment families that need positional offsets. It should be treated as presentation/placement support for specific ailment behavior, not as a generic status-value table.
- Because the consuming ailment family decides how offsets are interpreted, clone the source ailment and its offset row together when copying transformation, summon, or presentation-heavy states.

### `SkillAilmentSkillReplaceList`
Small ailment-driven skill-replacement helper. OT0 effectively leaves it dormant, but the schema is still worth documenting.

- **OT0 rows:** `1`.
- **CotC rows:** `12`.
- **OT0 SDK row struct:** `FSkillAilmentSkillReplaceListBase`.

#### Field-by-field
- `m_id`: Primary key for the replacement package.
- `m_SkillList`: **Observed.** Fixed list of replacement `SkillID` rows.
- `m_AilmentType`: **Confirmed.** `SkillAilmentType.m_id` that triggers the replacement package.


#### Runtime meaning
- `SkillAilmentSkillReplaceList` is an authorization list. It does not globally replace a skill by itself; an active ailment must point here, and the list must include the queried skill.
- The active character's replacement-cache checks make this table behave like a temporary availability filter as well as a replacement mapping. A skill can be hidden/rejected if the active replacement ailment does not permit it.

### `SkillResistList`
Inline percentage-based weapon/element resistance payloads. This table is often confused with `EnemyWeakID`, but the stored values prove that they are different systems.

- **OT0 rows:** `215`.
- **CotC rows:** `241`.
- **OT0 SDK row struct:** `FSkillResistListBase`.

#### Field-by-field
- `m_id`: Primary key for the resistance payload row.
- `m_ResistWeapon`: **Confirmed.** Direct percentage/delta array by weapon family. OT0 values include entries such as `50`, `27`, `22`, `12`, `2`, `-40`, `-50`, and `-100`, so this is clearly **not** a `ResistType` ID array.
- `m_ResistMagic`: **Confirmed.** Direct percentage/delta array by element family, with the same semantics as `m_ResistWeapon`.


#### Runtime meaning
- `SkillResistList` is a skill-side resist-rate package, not the enemy weakness grid. Its values are direct percentage adjustments paired with weapon/magic selectors.
- Do not point `SkillAvailID.m_ResistID` here in OT0. The guide's verified OT0 cases point `m_ResistID` to `EnemyWeakID`, while this table is used by resist-name/rate display and skill-side resist helper paths.

### `SkillResistAilmentID`
Packaged ailment-resistance rows used by skills and enemy rows that want explicit ailment/rate pairs instead of one inline base-rate array.

- **OT0 rows:** `74`.
- **CotC rows:** `60`.
- **OT0 SDK row struct:** `FSkillResistAilmentIDBase`.

#### Field-by-field
- `m_id`: Primary key for the ailment-resistance package.
- `m_ResistAilments`: **Confirmed.** List of up to sixteen `SkillAilmentType.m_id` rows covered by the package.
- `m_ResistRate`: **Confirmed.** Matching rate array for `m_ResistAilments`. The arrays are parallel.


#### Runtime meaning
- `SkillResistAilmentID` is an ailment-probability package used by add-ailment application and by text helpers that display ailment hit chance.
- The arrays are parallel. `m_Ailments[i]` and `m_Rates[i]` must be edited together, and empty slots should remain zeroed rather than partially filled.
- A direct `m_SubAilment` removal path can bypass this package depending on caller. Do not assume add and remove probabilities are symmetric.

#### Runtime proof note
- The OT0 ailment-resistance reader loads a `SkillResistAilmentID` row, scans the ailment-ID array, and returns the rate at the same index in the rate array. This is a direct parallel-array map, not a label lookup. See the OT0 C-export runtime contracts section for the compact proof.

### `SkillHitRateList`
Reusable multi-hit and repeated-resolution probability table. `SkillAvailID.m_HitTypes` points here.

- **OT0 rows:** `68`.
- **CotC rows:** `72`.
- **OT0 SDK row struct:** `FSkillHitRateListBase`.

#### Field-by-field
- `m_id`: Primary key for the hit-rate package.
- `m_MinCount`: **Observed.** Minimum guaranteed count before random continuation is considered.
- `m_CountAilment`: **Confirmed shape.** Additional ailment/count reference channel. OT0 leaves it zero on every row, but CotC does use it.
- `m_HitRates`: **Observed.** Sixteen-slot probability table controlling extra hit rolls or repeated-resolution count.
- `m_Shuffle`: **Observed.** Randomize/shuffle behavior for the hit-rate pattern.


#### Runtime meaning
- `SkillHitRateList` does more than choose random hit counts. The `m_CountAilment` field can turn the hit-count package into an ailment-count-driven package.
- `SkillAvailID.m_HitTypes` can hold multiple hit packages. Runtime repeat/multi-hit helpers may inspect the hit package independently of the main damage value, so a copied skill can keep the wrong multi-hit behavior even when `m_Values` were changed correctly.

#### CotC enum note
- CotC still exposes `ESKILL_HIT_RATE_ID`, but the current enum dump only contains `Default = 0`. That is **not** enough to assign named semantic labels to OT0's concrete `SkillHitRateList.m_id` rows, so keep interpreting this table from row usage and call patterns rather than from the sparse enum.

### `SkillIconList`
Small paired icon bank. Real, but not the primary target of `SkillID.m_SkillIconID` in OT0.

- **OT0 rows:** `11`.
- **CotC rows:** `12`.
- **OT0 SDK row struct:** `FSkillIconListBase`.

#### Field-by-field
- `m_id`: Primary key for the icon pair.
- `m_BattleIconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the in-battle icon.
- `m_MenuIconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the menu icon.


#### Runtime meaning
- `SkillIconList` is a paired-icon bank, not the main command icon table. `SkillID.m_SkillIconID` points directly to `TextureID` in OT0.
- Use this table only when the caller or UI surface expects a paired icon row. For ordinary command icons, edit `SkillID.m_SkillIconID` instead.

### `SkillUniqueIconID`
Unique icon/text override table for special command panels or buff displays.

- **OT0 rows:** `39`.
- **CotC rows:** `137`.
- **OT0 SDK row struct:** `FSkillUniqueIconIDBase`.
- OT0 `SkillID` rows do not actually use this table, but the table itself is fully populated and cleanly structured.

#### Field-by-field
- `m_id`: Primary key for the unique icon row.
- `m_TextID`: **Observed.** Text label for the unique icon. A few OT0 IDs resolve cleanly in `GameTextUI` (for example `65490 -> Hang Tough`), while many OT0 IDs are not present in the currently exported OT0 text banks. Treat that unresolved subset as legacy/shared content rather than as proof that the field is unused.
- `m_SkillIconTexID`: **Confirmed.** `Texture/TextureID.m_id` for the unique icon texture.
- `m_OverrideCommandPanel`: **Observed.** Use this unique icon/text in the command panel.
- `m_OverrideInBattle`: **Observed.** Use this unique icon in battle UI.
- `m_OverrideBuff`: **Observed.** Use this unique icon for buff/state display.


#### Runtime meaning
- `SkillUniqueIconID` is a schema-supported unique-icon helper. Stock OT0 does not use it through ordinary `SkillID.m_SkillUniqueIconID` rows, so treat it as a reserved/UI surface unless your target build proves a specific caller.

### `SkillSupplyItemGroup`
OT0 village/supply helper table. It binds a skill opcode to a reward lottery group.

- **OT0 rows:** `45`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillSupplyItemGroupBase`.
- **Related OT0 SDK enums:** `ESKILL_MODIFY_TYPE`.

#### Concrete OT0 examples
- The first visible material family is already enough to show the pattern: `SkillSupplyItemGroup#100/#101` point to `Timber Supply` / `Timber Supply+`, and `#200/#201` point to `Top Timber Supply` / `Top Timber Supply+`.
- The same pattern repeats for stone, cloth, ore, and other village materials. In other words, the table is not a generic battle-reward list; it is a curated village supply lottery keyed by the triggering skill.
- OT0 uses `m_SkillModifyType = 201` for most of these rows, which lines up with the `ITEM_SUPPLY` branch of `ESKILL_MODIFY_TYPE`.

#### Field-by-field
- `m_id`: Primary key for the supply-item group.
- `m_SkillId`: **Confirmed.** `SkillID.m_id` of the skill that triggers this lottery group.
- `m_SkillModifyType`: **Confirmed by enum/value overlap.** Economy opcode from `ESKILL_MODIFY_TYPE`. OT0 uses mostly `201 ITEM_SUPPLY`, with a few `208 FARM_HARVEST_ITEM_AMOUNT_LOTUP` and `254 ITEM_SUPPLY_LOT_IN_GROUP` rows.
- `m_Lot`: **Observed.** Group-level lottery weight or multiplier. OT0 leaves it at `100` on every row.

### `SkillSupplyItemList`
OT0 village/supply lottery entries. This is the payload table that `SkillSupplyItemGroup` chooses from.

- **OT0 rows:** `166`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillSupplyItemListBase`.

#### Field-by-field
- `m_id`: Primary key for the lottery entry.
- `m_GroupId`: **Confirmed.** `SkillSupplyItemGroup.m_id` parent group.
- `m_RewardId`: **Observed with strong evidence.** Reward-type discriminator. OT0 uses `1000` for money/leaves entries and `1001` for item-reward entries.
- `m_ItemId`: **Observed with strong evidence.** `Item/ItemList.m_id` for item rewards, or `0` for money rewards.
- `m_Values`: **Observed.** Reward amount or item count. For `m_RewardId = 1000` this is the leaf amount; for `1001` it is the item quantity.
- `m_Lot`: **Observed.** Per-entry lottery weight inside the parent group.
- `m_IsUnique`: **Observed.** Unique reward flag. OT0 uses it on only two entries in group `4000`.


#### Runtime meaning
- Supply rewards are resolved in two stages: `SkillSupplyItemGroup` selects one or more groups by `(SkillID, ModifyType)`, and `SkillSupplyItemList` expands each group into concrete reward entries.
- The group/list split means changing only the skill ID or only the modify type can silently disconnect the supply reward. Clone both rows when copying village supply skills.

### `SkillTargetShopList`
OT0 target-shop helper rows for village/trade skills.

- **OT0 rows:** `24`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillTargetShopListBase`.
- **Related OT0 SDK enums:** `ESHOP_TYPE`.

#### Exact OT0 SDK enum quote used by this table
##### `ESHOP_TYPE`
Quoted from `SDK/Kingship_structs.hpp:6100`.
```cpp
enum class ESHOP_TYPE : uint8
{
	eNONE                                    = 0,
	eINN                                     = 1,
	eBLACKSMITH                              = 2,
	eBAR                                     = 3,
	eARENA                                   = 4,
	eTOWER                                   = 5,
	eITEM                                    = 6,
	eGENERAL_MARCHANT                        = 7,
	eJOBSELECTER                             = 8,
	eRECOVERY_POINT                          = 9,
	eVILLAGE_EDIT                            = 10,
	eSAVE_POINT                              = 11,
	eCook                                    = 12,
	eFarm                                    = 13,
	eLINER                                   = 14,
	eRANCH                                   = 15,
	eACHIEVE                                 = 16,
	eVILLAGE_BASE                            = 17,
	eGramophone                              = 18,
	eMixing                                  = 19,
	eBEAUTY_SALON                            = 20,
	eEVENT_ROLLBACK                          = 21,
	eTRADE                                   = 22,
	eSACRED_TORCH                            = 23,
	eTRAINING_CENTER                         = 24,
	eMUSEUM                                  = 25,
	eNOTE                                    = 26,
	eMONSTER_ARENA                           = 27,
	eBUILDING_WORKSHOP                       = 28,
	eNOTE_BOOKSHELF                          = 29,
	ESHOP_MAX                                = 30,
};
```
- `SkillTargetShopList.m_ShopType` stores the raw `ESHOP_TYPE` numeric value. The helper row exists so a skill can bind not just a shop family but also the concrete map context that the skill should target.

#### Concrete OT0 examples
- Rows `#1..#16` cover trade-price skills such as `Savvy Shopper`, `Master Haggler`, `Savvy Seller`, `Master Salesman`, `Appraiser`, `Grand Appraiser`, `Wholesale Expert`, and `Master Tradesman`; all of those use `m_ShopType = 22`, i.e. `eTRADE`.
- Rows `#17..#24` cover village-base utility skills such as `Negotiator`, `Fixer`, `Cook`, and `Grand Chef`; those use `m_ShopType = 17`, i.e. `eVILLAGE_BASE`.
- Every stock OT0 row uses `m_MapId = 900002`, which is why the table reads less like a universal shop catalog and more like a village-system helper table scoped to one map context (that being Wishvale).

#### Field-by-field
- `m_id`: Primary key for the target-shop row.
- `m_SkillId`: **Confirmed.** `SkillID.m_id` of the shop-targeting skill.
- `m_ShopType`: **Confirmed.** `ESHOP_TYPE` selector for the target shop. OT0 uses `17 (eVILLAGE_BASE)` and `22 (eTRADE)`.
- `m_MapId`: **Confirmed.** `Map/MapListTable.m_id` providing the map context of the target shop. OT0 uses `900002`, the village base map, on every row.


#### Runtime meaning
- `SkillTargetShopList` is a three-key whitelist: skill, shop type, and map. A matching skill ID is not enough.
- This table supports field/village targeting rather than battle resolution. If a shop-targeting skill appears valid but does nothing in a specific map, this table is one of the first places to check.

### `SkillTargetFarmItemList`
OT0 ranch/farm helper rows for skills that append or target specific village farm items.

- **OT0 rows:** `2`.
- **CotC rows:** not present in the CotC `Skill/` folder.
- **OT0 SDK row struct:** `FSkillTargetFarmItemListBase`.
- **Related OT0 SDK enums:** `ESKILL_MODIFY_TYPE`.

#### Concrete OT0 examples
- Both stock OT0 rows belong to the `Sheep King` family: `SkillTargetFarmItemList#1` points to `SkillID#900938 Sheep King`, and `#2` points to `SkillID#903901 Sheep King+`.
- Both rows target `m_VillageFarmItemId = 103`; the secondary IDs `105` and `106` exist as mostly empty placeholders in stock OT0, which is why this field should be treated cautiously when cloning the pattern.

#### Field-by-field
- `m_id`: Primary key for the farm-target row.
- `m_SkillId`: **Confirmed.** `SkillID.m_id` of the farm-targeting skill.
- `m_SkillModifyType`: **Confirmed.** OT0 uses `215`, which is `RANCH_APPEND_ITEM` in `ESKILL_MODIFY_TYPE`.
- `m_VillageFarmItemId`: **Confirmed.** `Village/VillageFarmItem.m_id` of the main target farm item.
- `m_AdditionalVillageFarmItemId`: **Confirmed.** Secondary `Village/VillageFarmItem.m_id`. In OT0 the referenced rows are empty placeholders (`105` and `106`), so do not assume this field already has meaningful live content in stock OT0.


#### Runtime meaning
- `SkillTargetFarmItemList` maps `(SkillID, ModifyType, VillageFarmItemId)` to an additional farm item. The modify opcode is part of the key, so changing a skill from one village opcode to another can break the reward mapping.
- This is a village-system table, not a battle item-drop table. Do not use it to model ordinary combat loot.

### `SkillVisitorList`
Visitor/summon helper rows. OT0 barely uses the table, but the one live row is enough to show the intended shape.

- **OT0 rows:** `2`.
- **CotC rows:** `7`.
- **OT0 SDK row struct:** `FSkillVisitorListBase`.

#### Concrete OT0 examples
- OT0 has only one live nonzero visitor row: `SkillVisitorList#1`.
- That row summons `m_EnemyID = 33000`, whose enemy label is `Summon_PC072_Linde`.
- Its placement data is not trivial filler either: `m_OffsetsX[0] = -500.0` and `m_IconID = 1000`, which is a strong hint that the visitor table is handling both spawn layout and UI identity for summon-style guests.

#### CotC runtime surface
- Headless IDA on CotC Global 3.22.10 exposes `GetSkillVisitorList`, `GetSkillVisitorListDataList`, `GetSkillVisitorListIsLoaded`, the backing member `m_SkillVisitorList`, and row metadata including `m_id`, `m_EnemyType`, `m_EscortNum`, and `m_EscortType`.
- CotC's current `ESKILL_VISITOR_ID` enum dump is empty, so the reliable naming here comes from runtime row metadata rather than from a populated companion enum.

#### Field-by-field
- `m_id`: Primary key for the visitor row.
- `m_EnemyID`: **Confirmed in OT0.** `Enemy/EnemyID.m_id` of the visitor battler. OT0's live nonzero row uses `33000`, which is `Summon_PC072_Linde`.
- `m_EscortNum`: **Observed.** Per-slot escort count array.
- `m_EscortType`: **Observed.** Per-slot escort/layout type array. OT0 leaves it zero.
- `m_OffsetsY`: **Observed.** Per-slot Y offsets for visitor placement.
- `m_OffsetsX`: **Observed.** Per-slot X offsets for visitor placement.
- `m_CallSE`: **Observed.** Per-slot call sound IDs. OT0 leaves them zero.


#### Runtime meaning
- `SkillVisitorList` is a summon package. The avail chooses the visitor package; this table chooses the concrete guest/enemy IDs, offsets, and call sounds for each boost level.
- In OT0 the table stores direct enemy IDs; CotC stores enemy type instead. This is a real schema drift, not a cosmetic label change.
- Keep offsets and call sounds with the same visitor package when cloning summons; otherwise the summon may appear in the wrong place or use the wrong call audio even if the spawned unit is correct.
- `m_IconID`: **Unresolved.** Visitor UI icon identifier. OT0 only uses value `1000` on the live row, and the target table is not proven by the current exports.

#### CotC schema change
- `m_EnemyType`: **Confirmed from CotC schema and runtime metadata.** CotC stores an enemy type here instead of a direct OT0-style `EnemyID`.

### `SkillEnhancedList`
Enhancement helper table. OT0 effectively leaves it empty, but the schema is straightforward.

- **OT0 rows:** `1`.
- **CotC rows:** `10`.
- **OT0 SDK row struct:** `FSkillEnhancedListBase`.

#### Field-by-field
- `m_id`: Primary key for the enhancement package.
- `m_EnhancedSkills`: **Observed.** Fixed list of enhanced skill IDs. OT0 only ships the zero row, so there is no stock content to model from.


#### Runtime meaning
- `SkillEnhancedList` is an enhancement mapping layer. It is not the same as boost replacement and should not be used as a substitute for `SkillID.m_BoostSkills`.
- Enhancement helpers can normalize replaced/boosted skills before lookup, so the practical target may be the base equipped skill rather than the visible skill row currently shown in the command list.

### `SpecialSkillID`
Special-skill master key table. OT0 stores only the IDs here; the actual per-level mapping lives in `SpecialSkillGrowthList` and the player binding lives in `Character/CharaPlayerSpecialSkillList`.

- **OT0 rows:** `86`.
- **CotC rows:** `266`.
- **OT0 SDK row struct:** `FSpecialSkillIDBase`.

#### Field-by-field
- `m_id`: Primary key for the special-skill family.


#### Runtime meaning
- `SpecialSkillID` separates special-skill identity from the command skill it produces. UI, gauge, growth, and produced command ID are therefore not a single row.
- The runtime has special-case identity maps for some special skills, so clone special-skill families carefully: edit the special ID row, the produced `SkillID`, growth rows, and any gauge/regeneration fields together.

### `SpecialSkillGrowthList`
Per-level mapping from a special-skill family to the concrete `SkillID` row granted at that level.

- **OT0 rows:** `497`.
- **CotC rows:** `5301`.
- **OT0 SDK row struct:** `FSpecialSkillGrowthListBase`.

#### Field-by-field
- `m_id`: Primary key for the growth row.
- `m_SpecialSkillID`: **Confirmed.** `SpecialSkillID.m_id` of the special-skill family being grown.
- `m_Level`: **Confirmed.** Growth level. OT0 usually uses `1..3` for player-facing EX/special skills, but the file itself contains rows up to level `10`.
- `m_SkillID`: **Confirmed.** `SkillID.m_id` granted or substituted at that special-skill level.
- `m_DisplayPriority`: **Observed.** UI sort key among growth rows. OT0 mostly leaves it `0`, but it also uses values `20..90`.


#### Runtime meaning
- `SpecialSkillGrowthList` is the OT0 growth/unlock curve for special skills. It controls per-level skill identity, text, and display ordering rather than ordinary command boost.
- CotC's equivalent table is much wider. Do not backport CotC gauge/reward/damage-cap fields into OT0 unless you also have a native reader for those fields.

#### CotC-only extra fields
- `m_InitialGaugeValue`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Starting gauge for the special-skill level.
- `m_GaugeRegenerateValue`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Gauge regeneration per cycle/turn.
- `m_CountLimit`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Usage cap.
- `m_RewardTypes`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Reward-type array for the growth row.
- `m_ItemIDs`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Reward item IDs parallel to `m_RewardTypes`.
- `m_ItemCounts`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Reward counts parallel to the reward arrays.
- `m_AvailValues`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Per-level tuning values passed to the underlying avail logic.
- `m_AvailHitTypes`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Per-level hit-type overrides.
- `m_AilmentValues`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Per-level ailment potency overrides.
- `m_AilmentTurns`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Per-level ailment-duration overrides.
- `m_DamageLimitUp`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Per-level damage-cap increase.

### `ClassSkillConditionList` (CotC only)
CotC-only helper table for class-skill / EX skill use conditions. No matching dedicated OT0 SDK row struct was found in the SDK dump shipped here, but this row surface is now backed by CotC runtime metadata as well as schema exports: headless IDA exposes `GetClassSkillConditionList`, `GetClassSkillConditionListDataList`, `GetClassSkillConditionListIsLoaded`, the backing member `m_ClassSkillConditionList`, and row metadata for `m_id`, `m_UseCondition`, and `m_ConditionText`.

#### Field-by-field
- `m_id`: Primary key for the class-skill condition row.
- `m_UseCondition`: **Confirmed field name from CotC runtime metadata; semantic role observed.** Condition ID evaluated by the EX-/class-skill system.
- `m_ConditionText`: **Confirmed field name from CotC runtime metadata.** Text ID shown to the player for the condition.


#### Runtime meaning
- `ClassSkillConditionList` is a CotC use-gating surface for class/EX-style skills. It is distinct from ordinary `SkillConditionList`, which gates avail execution.
- `m_ConditionText` makes this table player-facing: it can explain why a skill is unavailable rather than merely hiding the command.

#### CotC enum note
- CotC's current `ECLASS_SKILL_CONDITION_ID` enum dump is empty, so condition-ID naming still has to come from the table/runtime side rather than from enum labels.

### `SkillEffectiveLinkageList` (CotC only)
CotC-only chained-presentation helper. This table is referenced by `SkillEffectiveID.m_SkillEffectiveLinkageID`.

- **Current evidence status:** CotC Global 3.22.10 only gave this as schema/string-surface evidence in the older lane, but CotC 4.2.3 now exposes a reflected `SkillEffectiveLinkageListBase` row header in the supplied `libUE4.so.h` (`libUE4.so.h`:59958-59969). Treat the field names below as 4.2.3 header-confirmed, while any older offset/function-address claims remain 3.22.10-specific unless re-anchored.

#### Field-by-field
- `m_id`: Primary key for the linkage row.
- `m_HoldTime`: Total hold time for the linked presentation group.
- `m_AvailTime`: Availability/resolution time for the linked presentation group.
- `m_OffsetX`: Group-level X offset for linked presentation placement.
- `m_OffsetY`: Group-level Y offset for linked presentation placement.
- `m_LimitMax`: Observed upper-limit field for the linkage group.
- `m_LinkageType`: Observed linkage mode selector.
- `m_Effectives`: **Confirmed by ID overlap.** Up to sixteen `SkillEffectiveID.m_id` rows emitted by this linkage package.


#### Runtime meaning
- `SkillEffectiveLinkageList` is a CotC presentation-chain package. It lets one effective stage link to additional effective IDs with its own hold/avail timing, offsets, limit, and linkage type.
- OT0 keeps cameras inline on `SkillEffectiveID`; CotC moves more orchestration into linkage rows. Port presentation by behavior, not by field position.

### `SkillFilterCategory` (CotC only)
CotC-only filter-category legend for the CotC skill filter UI.

- CotC Global 3.22.10 runtime metadata also exposes `GetSkillFilterCategory`, `GetSkillFilterCategoryDataList`, `GetSkillFilterCategoryIsLoaded`, the backing member `m_SkillFilterCategory`, and row metadata for `m_id`, `m_Name`, and `m_Priority`.

#### Field-by-field
- `m_id`: Primary key for the filter category.
- `m_Name`: Text ID for the category name.
- `m_Priority`: UI sort priority of the category.


#### Runtime meaning
- `SkillFilterCategory` is CotC UI taxonomy. It does not by itself make a skill behave differently in battle.
- Skill inclusion/exclusion is driven by `SkillID` forced/exclusion filter arrays and `SkillModifyType.m_SkillFilterType`, with this table providing the displayed category grouping.

### `SkillFilterType` (CotC only)
CotC-only filter-type legend. `SkillID.m_ForcedSkillFilter` and `m_ExclusionSkillFilter` point here, and each filter type belongs to a filter category.

- CotC runtime metadata also exposes `GetSkillFilterType`, `GetSkillFilterTypeDataList`, `GetSkillFilterTypeIsLoaded`, and the backing member `m_SkillFilterType`, so this table is definitely still live in code.
- CotC code also keeps a separate enum family `ESKILL_FILTER_TYPE` for coarse categories such as `HP_RECOVER`, `SP_RECOVER`, `BP_RECOVER`, `REMOVE_ABNORMAL_STATUS`, `REMOVE_DEBUFF`, `DAMAGE_REDUCE`, `ROBUST`, and `SPECIAL_SKILL_GAUGE_UP`. Do **not** confuse that enum with this table's row IDs; the enum names a filter kind, while `SkillFilterType` is still a data table.

#### Field-by-field
- `m_id`: Primary key for the filter type.
- `m_Name`: Text ID for the filter name.
- `m_Priority`: UI sort priority of the filter type.
- `m_Category`: **Confirmed.** `SkillFilterCategory.m_id` parent category.


#### Runtime meaning
- `SkillFilterType` is the CotC filter leaf. A skill can enter this leaf through modify-type mapping, forced filters, or explicit exclusion/override rules.
- Treat filter type as UI/search metadata unless you recover a combat caller for a specific filter family.

### `SkillModifyType` (CotC only)
CotC-only bridge table from modify opcodes to filter types. The row IDs line up exactly with the nonzero `SkillAvailID.m_ModifyType` values used by CotC.

- CotC runtime metadata cleanly exposes `GetSkillModifyType`, `GetSkillModifyTypeDataList`, `GetSkillModifyTypeIsLoaded`, the backing member `m_SkillModifyType`, and row metadata for `m_id` plus `m_SkillFilterType`.

#### Field-by-field
- `m_id`: **Confirmed.** Modify opcode key corresponding to `SkillAvailID.m_ModifyType`.
- `m_SkillFilterType`: **Confirmed field name from CotC runtime metadata and schema.** Link field into `SkillFilterType`. The checked binary pass proves that this linkage field exists, but does not by itself settle the exact fixed-array length, so treat any specific "up to five" shape as schema-backed rather than binary-proven.


#### Runtime meaning
- `SkillModifyType` is CotC's bridge from modify opcode to filter UI. It helps CotC classify skills without hardcoding every opcode in the UI layer.
- It should not be used as an OT0 opcode legend. OT0 and CotC modify-type numbers diverge early, and a filter mapping does not prove gameplay semantics.

### `SpecialSkillExtendCategory` (CotC only)
CotC-only legend table for special-skill extension categories.

- CotC code also exposes the companion enum `ESSKILL_EXTEND_CATEGORY`, currently named as `USE_ADD`, `GAUGE_UP`, `DAMAGE_UP`, `BACK_ACTIVATE`, and `GAUGE_UP_BY_BP`. That enum is useful for sanity-checking `SpecialSkillExtendType.m_Category` values.
- CotC Global 3.22.10 runtime metadata also exposes `GetSpecialSkillExtendCategory`, `GetSpecialSkillExtendCategoryDataList`, `GetSpecialSkillExtendCategoryIsLoaded`, the backing member `m_SpecialSkillExtendCategory`, and row metadata for `m_id` plus `m_Name`.

#### Field-by-field
- `m_id`: Primary key for the extension category.
- `m_Name`: Text ID of the category name.


#### Runtime meaning
- `SpecialSkillExtendCategory` is a CotC special-skill extension taxonomy row. It groups extension types for UI/explanation purposes.
- The category row does not grant the extension by itself; `SpecialSkillExtendList` maps actual special skills to extension types and reward/unlock data.

### `SpecialSkillExtendType` (CotC only)
CotC-only legend for extension effect types. `SpecialSkillExtendList.m_ExtendType` points here.

- CotC runtime metadata exposes `GetSpecialSkillExtendType`, `GetSpecialSkillExtendTypeDataList`, `GetSpecialSkillExtendTypeIsLoaded`, the backing member `m_SpecialSkillExtendType`, and row metadata including `m_id`, `m_Category`, `m_SubCategory`, `m_EffectName`, and `m_DetailText`.

#### Field-by-field
- `m_id`: Primary key for the extension type.
- `m_Category`: **Confirmed.** `SpecialSkillExtendCategory.m_id`.
- `m_SubCategory`: **Confirmed field name from CotC runtime metadata; semantic role observed.** Sub-type selector inside the parent category.
- `m_EffectName`: **Confirmed field name from CotC runtime metadata.** Text ID of the effect name.
- `m_DetailText`: **Confirmed field name from CotC runtime metadata.** Text ID of the effect detail/explanation.
- `m_EffectValue`: Primary numeric payload for this extension type.
- `m_SubEffectValue`: Secondary numeric payload for this extension type.
- `m_AutoLearn`: Auto-unlock flag.
- `m_SubText`: Extra text ID used alongside the main effect name/detail.


#### Runtime meaning
- `SpecialSkillExtendType` describes what kind of extension effect exists and what values/text should be shown for it.
- `m_EffectValue` and `m_SubEffectValue` are type-specific payloads. Their meaning depends on `m_Category` and `m_SubCategory`, so do not read them as universal percentages.

### `SpecialSkillExtendList` (CotC only)
CotC-only per-special-skill extension mapping. This is the payload table that says which extension type a special skill gets, how it is unlocked, and which rewards are bundled with it.

- CotC Global 3.22.10 runtime metadata also exposes `GetSpecialSkillExtendList`, `GetSpecialSkillExtendListDataList`, `GetSpecialSkillExtendListIsLoaded`, the backing member `m_SpecialSkillExtendList`, and row metadata that at least includes `m_id`, `m_TargetSSkill`, `m_ExtendType`, and `m_GetPlaceText`.

#### Field-by-field
- `m_id`: Primary key for the extension row.
- `m_TargetSSkill`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** `SpecialSkillID.m_id` targeted by the extension.
- `m_ExtendType`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** `SpecialSkillExtendType.m_id` describing the extension effect.
- `m_GetPlaceText`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Text ID telling the player where/how the extension is obtained.
- `m_PriorityItemRewardType`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Priority reward-type code.
- `m_PriorityItemID`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Priority reward item ID.
- `m_PriorityItemNum`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Priority reward item count.
- `m_RewardTypes`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** General reward-type array.
- `m_ItemIDs`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Reward item IDs parallel to `m_RewardTypes`.
- `m_ItemCounts`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Reward counts parallel to the reward arrays.
- `m_OpenTimestamp`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Timestamp gate for opening/unlocking the extension.
- `m_AddSSkillLv`: **Confirmed from CotC Global 3.22.10 runtime metadata and schema.** Additional special-skill level granted or required by this extension row.


#### Runtime meaning
- `SpecialSkillExtendList` is the CotC payload map: it says which special skill gets which extension type, where the extension is obtained, and which priority/general rewards are bundled with it.
- Reward arrays are parallel: `m_RewardTypes[i]`, `m_ItemIDs[i]`, and `m_ItemCounts[i]` describe one reward entry. Keep them aligned when editing.
- `m_OpenTimestamp` makes this table time-gated in live-service contexts; it should be ignored for OT0 unless a ported system deliberately implements time gates.

- Per-target skip behavior is acceptable.
- Telop/announcement behavior is expected.
- Mounted/preloaded visitor/effect resources are present.
