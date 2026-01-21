## **LANGUAGE:**  [English](Sound_Prog_en.md) | [日本語](Sound_Prog_jp.md) | [中文](Sound_Prog_zh.md) | [한국어](Sound_Prog_kr.md)

Thanks to MarvinCx886 for discovering this workflow and Zy for writing an English guide here: https://github.com/zhangyucn2/Octopath-traveller-audio-replacement-tutorial

I hope this article can give more context on this workflow.
### How Octopath Traveler 0 audio is likely structured
In CRIWARE-based Unreal projects, audio typically consists of:
- `.acb`: the cue sheet database (cue names, cue IDs, metadata, routing, etc.)
- `.awb`: the wave bank (actual encoded audio payload)
- UE-side "wrapper assets" created by the CRIWARE UE plugin when you import `.acb` into the editor (these are `.uasset` files that reference the ACB/AWB)

At runtime, the game often triggers audio in one of these patterns:
- Cue name → cue ID → wave in .awb
- Cue ID directly
- Cue name within a specific cue sheet

It is (as of my knowledge) currently unclear whether modifying DataBase files related to Sound would allow one to import new sounds and integrate them into the same identifier/mapping system OT0 uses. Below is an explanation of each DataBase file under `Sound/`:

### `SoundSheetList`
appears to contain registries of CRI Atom CueSheet wrapper assets (the UE `.uasset` created when you import an `.acb` via the CRIWARE plugin following the guide above). If you add a new cuesheet (new `.acb` import), OT0 won't necessarily use it unless (this is speculation):
- it’s in this list, or
- some code loads it directly by path.

`m_InitialLoad` suggests boot-time preload behavior

### `SoundList`
The "game-facing" sound catalog. Gameplay asks for "play `bgm_theme_scholar`" (or play sound ID 1), and the runtime resolves that into:
 - which sheet to use (`m_Sheet` referencing `SoundSheetList.m_id`)
 - playback constraints (loop, max simultaneous, priority)
 - fade in/out

So, the sound label and/or sound ID is the API the game uses and a new cue in an `.acb` will never play unless there is a corresponding `SoundList` entry and the game calls it

### `ReplaceSoundList` and `ReplaceBGMList`
These are OT0-exclusive and replace sounds/BGM depending on a quest condition being fulfilled or a specific enemy group being fought.

### `VoiceSymbolList`
This looks like a mapping from a "voice symbol string" used by game scripts/data into a compact integer ID.

### `PartVoiceID`
Voice categories/"subtypes"

### `ConvPersonalityVoiceID`
Likely referring to the 6 different voices you can choose for Zero

### `VoiceEffectID`
Bus routing/DSP control (e.g. pick a CRI bus (BUS4, etc.), set parameters like volume/LPF/reverb send/etc.)

### `GramophoneList`
Tracks which Sound ID corresponds to which Music Score item.
