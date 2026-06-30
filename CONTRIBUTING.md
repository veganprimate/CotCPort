# Contributions
## **LANGUAGE:**  [English](CONTRIBUTING.md) | [日本語](CONTRIBUTING.jp.md) | [中文](CONTRIBUTING.zh.md) | [한국어](CONTRIBUTING.kr.md)

Everyone is welcome to contribute to this project. Besides the releases, this repository contains extensive documentation on how the various aspects of the mod work, general modding documentation for **Octopath Traveler 0**, and a list of tasks yet to be completed, along with brief discussions on viable paths for progress.
Below is a list of the features that have yet to be ported from **Champions of the Continent**. Each feature links to a markdown file in this repository that explains the current progress on it:
- [~~Quests and Events~~ (SOLVED)](Resources/QuestEvent_Prog_en.md)
- [Effects and Animations](Resources/Animations_Prog_en.md)
- [~~Objects~~ (SOLVED)](Resources/Objects_Prog_en.md)
- [Maps/Areas/Levels](Resources/Maps_Prog_en.md)
- [~~Sound Effects and Music~~ (SOLVED)](Resources/Sound_Prog_en.md)
- [_Porting entirely new features and code from CotC_ (MOSTLY SOLVED)](Resources/Custom_Prog_en.md)


### Mods using the CotCPort as a basis
As mentioned above, you are free to use this mod as a basis for your own, but please link to this page to keep bug reports for the **CotCPort** in one place. Paks override files in alphabetical order, so changing the file name of your mod accordingly will ensure that it loads after **CotCPort**.
### Tutorials and Resources
Below is a list of **tutorials and resources** for this mod, to make it easier for you to create your own mod using the ported data from CotC.
- [How to extract assets from this mod and/or OT0 and repak them](Resources/Begin_en.md)
- [How to add or modify characters, jobs, and new customization options for the main character](Resources/customchars_en.md)
- [How to add or modify enemies and battles](Resources/customenms_en.md)
- [How to add or modify quests and events](Resources/customqsts_en.md)
- [How to add or modify skills, ailments, and avails](Resources/customskills_en.md)

#### Variety of resources (unordered)
 - [Asset naming conventions in OT0 and CotC](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/naming_convention_glossary.csv)
 - ["World Area → Folder" dependencies (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/area_dependencies.csv)
 - [Asset Inventory](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/asset_inventory.csv): contains all 68,354 assets from JP CotC with metadata
 - [Unreal Object Class frequency (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/class_frequency.csv)
 - [Unreal Object Type frequency (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/type_frequency.csv)
 - [Top 200 most connected assets (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/critical_shared_assets.csv)
 - [Folder-to-folder reference counts (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/cross_folder_ref_matrix.csv)
 - [All 894 hub assets connecting 3+ folders (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/hub_assets.csv)
 - [Assets with no resolved referenced (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/isolated_assets.csv)
 - [VFX files ranked by complexity (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/particle_complexity_rating.csv)
 - [DataBase assets and their keys with `m_id` ranges + number of entries (CotC JP)](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/Database_schema_CotC.txt): might be useful for quicker reference, and comparing keys across different DataBase assets
 - Player IDs (`m_PlayerID`, `CharaPlayerID.m_id`, etc.) and their associated English character names in [the CotCPort](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/PlayerID_names_CotCPort.txt) and [OT0](https://github.com/veganprimate/CotCPort/blob/main/Resources/MiscResources/PlayerID_names_OT0.txt)
