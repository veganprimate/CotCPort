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


A mod for **Octopath Traveler 0** that aims to port as much content as possible from **Octopath Traveler: Champions of the Continent** (Global, Japanese, and Chinese versions) and possibly the other Octopath games too.
- Play all **270+ units** from CotC with their **complete skill boards**, including **7 custom new units** (Nomos the Scholar, Fra, Pierro, Fried, Hugo, Gimel, Mattias, and Miguel from Octopath Traveler 1)
- Fight against hundreds of **new bosses** from CotC in the **Monster Arena**
- Unlock dozens of **new accessories** and **equipment** from CotC by beating the new enemies in the Monster Arena
- Play through several **new quests** from CotC including characters' **Traveler Stories**

The mod currently supports the following languages:
- English
- Japanese
- Korean
- Chinese (Simplified)
- Chinese (Traditional)

![chargif](https://github.com/user-attachments/assets/00c35b82-3e9c-4810-8d8b-657f2c2a1151)

## Installation
Download one of paks ending with `notex` and the `texture` pak under **Releases**, as well as `RecruitEvent_p.pak`. `notex` contains **DataBase files**, `texture` ported **textures and models** from CotC, and `RecruitEvent_p.pak` is a mod that adds a **recruitment event** for **all** newly added 270+ units (see [How to recruit the new travelers](https://github.com/veganprimate/CotCPort/tree/main?tab=readme-ov-file#how-to-recruit-the-new-travelers)). This mod can be removed after recruiting all new units.

These files are then to be put in the game's **Paks** folder, where this is depends on your **Octopath Traveler 0** version and installation
### Steam
Put the three paks under `steamapps\common\Octopath_Traveler0\Octopath_Traveler0\Content\Paks`.
### Microsoft Store
1. Change the `_p` suffix to `_P` for all of the paks
2. Open the **Xbox App** on your PC, right-click **Octopath Traveler 0** in your installed games list on the left sidebar, and select **Manage**
3. Click on the **Files** tab
4. Click **Browse**
5. Place the pak files under `<game install folder>\Octopath_Traveler0\Content\Paks\`
### Switch (real hardware/Atmosphère)
Place the paks under `<SD Card>\atmosphere\Contents\01005270232F2000\romfs\Octopath_Traveler0\Content\Paks`
### Switch (Emulator)
Create a mod with the following folder structure `romfs/Octopath_Traveler0/Content/Paks` and place the three paks in Paks.

## (Optional) Hard Mode Version
Given that this mod changes the `EnemyID` asset among several others and would therefore be incompatible with any mod that also edits these assets, a hard mode version of this mod was created for anyone wishing to combine these two, denoted with the `_hard` suffix. Changes are as follows (changes marked with **(WIP)** are not fully implemented):
- **Normal enemies** now have **2x** the HP, **bosses** **3x** the HP
- The stats of normal enemies (Phys. Atk., Phys. Def., Elem. Atk., Elem. Def., Speed, Accuracy, Crit. and Crit. Def. and max shield count) were increased by **25%**, while those of bosses were increased by **50%**
- The XP, JP and money gained from defeating bosses has been increased by **20%**
- Individual changes have been done to enemies to **prevent soft-locks** (Imposter Olberic, Wishvale Ch. 5 Emerald Direwolf, and Cardinal Teragraf)
- Changes have been made to the **stats**, **skills**, and **tactics** of individual **enemies** and **bosses** (**WIP**, so far this largely affects only **Bestower of All** bosses and giving several encounters resource-draining skills)
- **(WIP)** **Very rare mini-boss type encounters** to make hunting for caits more dangerous and give the accessories and support skills increasing rare enemy encounter rates a side effect
- **(WIP)** Changes to **skills** and **skill sets** of **playable characters**: only affects the SP cost of some skills, the usage limit of `Parry`, and (strong) AoE healing skills being turned into delayed skills for the time being
- **(WIP)** Changes to individual status effects (Evade Phys. Atk removed upon attacking physically, Evade Elem. Atk. removed upon attacking with magic to encourage more specialized playstyles)
- **(WIP)** Item usage limit in battle (possibly down to 7 times per battle) to encourage smarter resource management and use of Skills and Helpers (whose count I may increase)
- **(WIP)** New food for more **specialized pre-buffs** (e.g. weapon/magic type specific attack or resistance buffs, specific status effects)
- To cope with the increased damage done by enemies and to encourage more frequently switching party members as well as using weapon/magic type resistance buffs, all playable characters (including ones added by this mod) have **weapon and magic type specific resistant rates** _(Note: the values specified in the referenced arrays specify how much damage is taken by the weapon/magic type in percent, so higher values→more damage, lower values→less damage)_. These are inspired by the **SaGa** series:
  - **Physical resistance rates** (`m_ResistWeapon`): SaGa cares about **broad** physical pressures/attribute types (Slash/Pierce/Strike/Missile/...) more than individual weapon brands. Since OT0 splits weapons into 8 entries, the numbers were set by projecting 3 SaGa-like pressures onto OT0's weapons:
    - _**Slash**-leaning pressure_: Sword / Axe / Fan / Dagger (slightly)
    - _**Pierce**-leaning pressure_: Polearm / Dagger / Bow
    - _**Blunt**-leaning pressure_: Staff / Tome / Axe (very slightly)

     Then each unit's values were pushed based on their implied END/VIT vs AGI profile and Job (which accounts for weapon proficiencies):
      - **High END/VIT "tanks"** → lower (better) vs slash/blunt; sometimes worse vs pierce (armor gaps/joints idea)
      - **High AGI "dodgers"** → lower vs pierce/ranged (bow/dagger/polearm), but often higher vs blunt (hard-to-dodge impacts)
      - **Frailer casters/supports** → higher across physical, especially pierce (they "get punished" if caught).
      - Usually slightly resistant towards weapon types they are **proficient in**; slightly worse off against **"opposing" weapon types** (i.e. of opposing physical pressure type)
  - **Magic resistance rates** (`m_ResistMagic`): Magic resist numbers were set primarily from an implied WIL/SPR/MND-like attribute, then shaped by **elemental affinity/weakness** (althugh each SaGa game implements it differently, mostly inspired by Romancing SaGa 2's magic attribute types: Heat/Cold/Electricity/Dark/Status) and the unit's **magic proficiencies** (determined by skill set):
      - **High WIL/faithful/cleric types** → much lower (better) across elements, often very strong vs Light and weaker vs Dark (or vice versa for occult/dark types)
      - **High INT mage types** → not necessarily tanky physically, but can have selective elemental toughness (affinity) and sharp weaknesses elsewhere
      - The 7th slot of the `m_ResistMagic` array (likely **Poison** or **non-elemental** magic damage) was generally treated like Poison/Status resistance (END/WIL leaning): sturdy/medical types get low values; frail/dark/rogue types often get higher values
  - **(WIP)** Future versions will give **enemies** and **equipment** similar resistant rates if feasible. The goal is to make breaking enemies rarer and require much more pre-planning to achieve and give you other kinds of weaknesses to exploit instead. This will hopefully also encourage the usage of other avenues of stunning an enemy (e.g. Enchant, Paralysis, Sleep, possibly Silence, Terror, and Blindness) and/or build more specialized parties to prepare a break.
  - **(WIP)** **SaGa LP-like consequence for KOs** (attrition pressure): a post-revive "injury" status that lasts until battle end that reduces Max HP/defenses/etc.
  - **(WIP)** Enemies and bosses get **more "fast" moves/priority moves** to make turn-order manipulation and speed buffs more important
  - **(WIP)** Enemies and bosses get **more conditional intercept** and **counter** buffs to **discourage repetitive patterns** (kind of an anti-cheese measure)
  - **(WIP)** Slight **changes to dungeon design**, possibly trap chests
  - **(WIP)** More trade-offs on gear but more specialized
  - **(WIP)** To discourage AoE healing: Make **single-target** healing spells **stronger** (passive buffs, etc.) and give enemies **tactics to punish AoE healing** (possible if OT0 has a condition type ID that checks whether a certain skill has been used or similar)
  - **(WIP)** Increase prices of the most abusable consumables to encourage usage of alternatives like (new) foods, new items. There will likely also be new items that are cheap but narrow/specialized




## How to recruit the new travelers
In the future, characters' traveler stories will serve as their recruitment events. So far, only Promme's traveler story has been ported.

For now, proceed as follows:

<img width="1286" height="726" alt="12" src="https://github.com/user-attachments/assets/f06205a2-5c30-4a8f-8718-5ee69d84db95" />

1. Make sure `RecruitEvent_p.pak` is in your Paks folder
2. Talk to the Longbow Master in Emberglow (pictured above). An event will trigger which should recruit all the playable characters in this mod.

When a new update introduces new units, you can recruit them by talking to the same NPC again provided `RecruitEvent_p.pak` is in your Paks folder. This does not appear to reset the progress or equipment of travellers who have already been recruited.

## Contributions
Everyone is welcome to contribute to this project. Besides the pak, this repository contains extensive documentation on how the various aspects of the mod work, general modding documentation for Octopath Traveler 0, and a list of tasks yet to be completed, along with brief discussions on viable paths for progress.
Below is a list of the features that have yet to be ported from Champions of the Continent. Each feature links to a markdown file in this repository that explains the current progress on it:
- [~~Quests and Events~~ (SOLVED)](QuestEvent_Prog_en.md)
- [Effects and Animations](Animations_Prog_en.md)
- [Objects](Objects_Prog_en.md)
- [Maps/Areas/Levels](Maps_Prog_en.md)
- [Sound Effects and Music](Sound_Prog_en.md)
- [Porting entirely new features and code from CotC](Custom_Prog_en.md)


Essentially, it involves extracting as much data as possible from the UE 4.27 Champions of the Continent assets, using this data to create new Unreal Engine 5.4 assets for Octopath Traveler 0, and ensuring that the mod loads these assets as there is no general way of converting cooked UE 4.27 assets to UE 5.4.

### Mods using the CotCPort as a basis
As mentioned above, you are free to use this mod as a basis for your own, but please link to this page to keep bug reports for the CotCPort in one place. Paks override files in alphabetical order, so changing the file name of your mod will ensure that it loads after CotCPort.
### Tutorials and Resources
Below is a list of tutorials and resources for this mod, to make it easier for you to create your own mod using the ported data from CotC.
- [How to extract assets from this mod and/or OT0 and repak them](Begin_en.md)
- [How to add custom characters, jobs, and new customization options for the main character](customchars_en.md)
- [How to add custom enemies and battles](customenms_en.md)
- [How to add custom quests and events](customqsts_en.md)
- [How to add custom skills and avails](customskls_en.md)

## Contact
I can be reached in the BravelyPath Modular Discord under the #cotc-characters-mod channel alongside several other contributors and modders: https://discord.com/invite/t43Kbrp 

## Acknowledgements
Thanks to:
- BoltGSR and MarvinCx886 for providing me with data of the Japanese version of CotC 
- Oersted for providing me with data of the Chinese verison of CotC and complete skillsheets, spritesheets, and ideas for custom units
- Vat for providing me with expanded spritesheets of characters and enemies, as well as cooking many of the textures to appear crystal clear in-game
- Solo Traveler for helping me playtest a large variety of features, giving me ideas for the Hard Mode version of the mod, and a detailed description of a custom arena fight
- igoticecream for generating an SDK for Octopath Traveler 0 and leading me in the right direction for implementing custom events
- MarvinCx886 and JohnZ for figuring out how to port Sound data from CotC and their detailed write-up on it
