### **LANGUAGE:**  [English](QuestEvent_Prog_en.md) | [日本語](QuestEvent_Prog_jp.md) | [中文](QuestEvent_Prog_zh.md) | [한국어](QuestEvent_Prog_kr.md)
<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/83c68154-8a8e-48b7-822c-c486f53cd60d" />

As can be seen in this messy picture, it is definitely possible to port quests from CotC. The problem lies in porting events, which account for cutscenes, boss fight initiations, dialogue during fights, switches in dungeons and much more besides. Without events, quests are nothing more than their quest markers and rewards.

When looking at the EventList asset under `/Game/Content/Local/DataBase/Event/`, one may think once the game loads an event ID, the `m_Name` and `m_Version` keys will resolve into the path `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` but that unfortunately is not true: Testing shows that the game does not care about these entries and appears to internally associate Event IDs to paths leading to Event assets.
Furthermore, the same event plays out differently in-game depending on which event asset you patch with new package properties, and especially in which folder it lies.
For instance, events under VPartychat automatically get the Wishvale tavern as background, VTest is very restrictive in terms of what events can play out there, etc. effectively ignoring the `m_Kind` key in EventList.
This suggests that it is not possible to add custom events to the game by adding new entries to EventList and we require intercepting the pipeline at the point where the engine has (or is about to use) a full Unreal object path.

I am currently developing a dll mod for UE4SS which does exactly that using PolyHook2, I am using [this mod](https://github.com/UE4SS-RE/RE-UE4SS/tree/main/cppmods/KismetDebuggerMod) as a template.
I have been extensively using IDA with OT0 IDA mappings generated via Dumper-7 (see the SDK available in the BravelyPath Modular Discord under #resources). So far, I have only been successful with overriding Event IDs at runtime.

**What has been done:**
- Interpreter/handler chain is confirmed: The event command dispatcher (`sub_144634800`) routes command 1000 (`EvCmPlayEvent` which is responsible for loading events within events) to: `sub_144623F60` (PlayEvent native handler) 
- Unfortunately, the resolver is not a path resolver: Inside `sub_144623F60`, it calls `sub_140EFEC60` to convert EventID (int) to a string, that output is a *fixed-size short string* (e.g. `L"9919"`) and cannot hold `/Game/Content/Local/DataBase/Event/...` paths
- We know where the short key is stored for later systems: Through aggressive logging, I found that `sub_144623F60` stores the key string into an event-manager slot structure:
 - `SlotPtr = BasePtr + SlotIndex * 0x23A0`
 - Key field location: `SlotPtr + 0x250`
So the downstream system does not re-run the resolver: it consumes the stored key from the slot.

Since only the short key is stored (likely because the game has several functions for loading an Event ID, e.g. whether it's through a quest, an NPC, in a battle, in an event itself, etc. and/or so the event system can be late-bound, cached, replicated, queued, or processed by another subsystem later), the game must later do something like:
1. Read key `L"9919"` from `SlotPtr + 0x250`
2. Build a full asset path or perform a lookup based on that key
3. Load/resolve an object (event asset, package, class instance, etc.)
That "later" function is what we must find. Once we hook that function, we can safely replace the full path (or the object reference) and truly support custom assets.

As the picture below shows, events directly ported from CotC should play out mostly fine so the question of supporting custom event assets is the main problem at play here.
<img width="869" height="479" alt="image" src="https://github.com/user-attachments/assets/8c7f5483-c143-47fd-9922-88d9fa3093e6" />



Another issue that may become significant later is how to add new PathActor data to existing OT0 maps, for example by adding new NPCs and objects. This is because custom quests from CotC often have their own set of NPCs and talk lists referencing new PathActors. Implementing this may be very straightforward; I just have yet to try it.

For a guide on the structure of the files under Local/DataBase/Quest and Local/DataBase/Event and how to add new events and quests, please see [How to add custom quests and events](customqsts_en.md).
