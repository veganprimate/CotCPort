## **LANGUAGE:**  [English](Begin_en.md) | [日本語](Begin_jp.md) | [中文](Begin_zh.md) | [한국어](Begin_kr.md)

1. Download and install [FModel](https://fmodel.app/) (FModel also works on Linux via Wine)
2. Click Directory -> Selector and add Octopath Traveler 0 as a new game; make sure to select `GAME_OctopathTraveler0` as the UE Version to have automatic deserialization of DataBase files; finally, select Octopath 0's `Paks/` folder under Directory  <img width="488" height="348" alt="FModel" src="https://github.com/user-attachments/assets/211ef83b-3427-4526-a75f-2029cd840b72" />
3. Head to Settings, enable `Local Mapping File` and import Octopath Traveler 0's mappings, these can be found in the BravelyPath Modular Discord or be manually extracted using UE4SS
4. While the mod itself is not encrypted, Octopath 0's main pak isn't. You will have to head to Directory -> AES and put in Octopath Traveler 0's AES key. It can be found in the BravelyPath Modular Discord or be manually extracted using IDA/Ghidra
5. Open the pak Octopath 0 or the mod and head to the assets you wish to modify. Extract whatever you wish to modify, for DataBase assets, that is often the asset properties and raw data (`.uasset`)
6. To patch DataBase assets with modified JSON property files, use `UAssetMessagePack.exe`. A download link is available under #resources in the BravelyPath Modular Discord
7. Use FluffyQuack's [UnrealPak](https://github.com/Dmgvol/UE_Modding/raw/main/Tools/UnrealPak.zip) to repack assets into pak files. Note that the folder you make a pak out of has to reflect game paths. So, say, you modify `SkillID` under `/Octopath_Traveler0/Content/Local/DataBase/Skill/`, then the folder which you drag into UnrealPak.exe has to have the exact same path leading to the modified file (you will have to include both `uasset` and `uexp` files).
8. Finally, put the newly created Pak file in the Game's `Paks/` folder (see also [Installation](https://github.com/veganprimate/CotCPort#installation))

Other resources:
- [Johesy's guide for editing job data and character skill sets for custom jobs](https://github.com/dustintchan/OT0-Modding-Job-Skills)
- Celes Corbin's Modding Tutorial ZIP (Download Link: #resources in the BravelyPath Modular Discord)
 
