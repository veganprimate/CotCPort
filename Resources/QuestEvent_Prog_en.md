## **LANGUAGE:**  [English](QuestEvent_Prog_en.md) | [日本語](QuestEvent_Prog_jp.md) | [中文](QuestEvent_Prog_zh.md) | [한국어](QuestEvent_Prog_kr.md)

### Update (2/07/2026)
Thanks to MarvinCx886's discovery while working on his **MioJoin** mod, porting Events is now possible. I have simply forgotten to modify the event assets' metadata to reflect the new paths. This is possible using [UAssetGUI](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest) and the Unreal Mappings of Octopath Traveler 0. `m_Name` and `m_Version` in `EventList` do indeed resolve into the path `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]`. 

Furthermore, a lot of OT0 maps still contain left-over data from CotC corresponding to e.g. position and collision data of NPCs not in the game, like Promme (see `QuTr_PC148_01_01`). These objects can be referenced in the respective `NpcSetList` under `m_AppearLabel` to have an NPC spawn there with the defined collision data.
<img width="1113" height="824" alt="image" src="https://github.com/user-attachments/assets/37b0af5c-c866-4ad6-b1c0-4d48986eba21" />


A detailed tutorial on the structure of the files under `.../Local/DataBase/Quest/` and `.../Local/DataBase/Event/` and how to add new events and quests will follow soon: [How to add custom quests and events](customqsts_en.md)

#### Current Issues
The primary now largely revolves around adding new NPCs to existing maps and porting entirely new areas. See [**Progress on Maps/Areas/Levels**](Maps_Prog_en.md).

A lot of event, quest, enemy, and NPC data also has to be manually adjusted to appear correctly in OT0:
 - NPCs in the ported events sometimes face the wrong direction

<img width="1167" height="649" alt="image" src="https://github.com/user-attachments/assets/a748fc6a-52a2-4223-ab8c-1e09743ae8e7" />

 - When facing a "Yes or No"-prompt in dialogue in a quest ported from CotC, the top option is by default "No," contrary to OT0, causing the wrong sound to play when selecting either option. The cursor also defaults to the wrong position when canceling.
 - The recommended levels are usually 20-30 levels too high given the strength of the bosses
 - CotC bosses generally give far too much XP (Priest Kratos is a weak boss with about 100k HP and gave 12k XP, 3x as much as Or'sazantos in OT0)
 - Ported Quest NPCs from CotC appear to often have odd FC data by default, see pic:
   
<img width="1239" height="667" alt="image" src="https://github.com/user-attachments/assets/c56a5b05-862f-45f2-a40a-cabe477d5fe6" />

 - CotC often uses invisible NPCs as Quest Nodes, which display path action/FC data when near, see:

<img width="1272" height="714" alt="image" src="https://github.com/user-attachments/assets/b5296bd4-fd61-4d75-a98a-1e05e391b349" />

 - Scenes play out with whatever party member engages the dialogue, not necessarily Zero/the Ringbearer.
 - Several quest tasks lack purpose text and/or instead use the task title which looks off in OT0.
 - Many reward types in CotC either work differently in OT0 or aren't functional at all. See [How to add custom quests and events](customqsts_en.md)

### Original Article (CORRECTED)
<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/83c68154-8a8e-48b7-822c-c486f53cd60d" />

_As can be seen in this messy picture, it is definitely possible to port quests from CotC. The problem lies in porting events, which account for cutscenes, boss fight initiations, dialogue during fights, switches in dungeons and much more besides. Without events, quests are nothing more than their quest markers and rewards._

_When looking at the EventList asset under `/Game/Content/Local/DataBase/Event/`, one may think once the game loads an event ID, the `m_Name` and `m_Version` keys will resolve into the path `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` ~~but that unfortunately is not true~~: ~~Testing shows that the game does not care about these entries and appears to internally associate Event IDs to paths leading to Event assets~~_ **(these keys do resolve into the respective path but appear to be ignored when changed for existing events)**.

_Furthermore, the same event plays out differently in-game depending on which event asset you patch with new package properties, and especially in which folder it lies._ 
_For instance, events under VPartychat automatically get the Wishvale tavern as background, VTest is very restrictive in terms of what events can play out there, etc. effectively ignoring the `m_Kind` key in EventList._  **(This seems to still be the case)**

_~~This suggests that it is not possible to add custom events to the game by adding new entries to EventList and we require intercepting the pipeline at the point where the engine has (or is about to use) a full Unreal object path~~._

_I ~~am currently~~ developing a dll mod for UE4SS which does exactly that using PolyHook2, I am using [this mod](https://github.com/UE4SS-RE/RE-UE4SS/tree/main/cppmods/KismetDebuggerMod) as a template.
I have been extensively using IDA with OT0 IDA mappings generated via Dumper-7 (see the SDK available in the BravelyPath Modular Discord under #resources). I have managed to override Event IDs at runtime._ **(DISCONTINUED, see earlier commits for details on the dll mod)**
