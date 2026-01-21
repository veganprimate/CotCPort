## **LANGUAGE:**  [English](Begin_en.md) | [日本語](Begin_jp.md) | [中文](Begin_zh.md) | [한국어](Begin_kr.md)

1. 下载并安装 [FModel](https://fmodel.app/)（FModel 也可以通过 Wine 在 Linux 上运行）。
2. 点击 Directory -> Selector，将 Octopath Traveler 0 作为新游戏添加；请务必将 UE Version 选择为 `GAME_OctopathTraveler0`，以便自动反序列化（deserialization）DataBase 文件；最后，在 Directory 中选择 Octopath 0 的 `Paks/` 文件夹。 <img width="488" height="348" alt="image" src="https://github.com/user-attachments/assets/2499b686-2cf9-406d-96bc-03c32c8b0dfa" />
3. 进入 Settings，启用 `Local Mapping File` 并导入 Octopath Traveler 0 的 mappings。这些 mappings 可以在 BravelyPath Modular Discord 中获取，也可以使用 UE4SS 手动提取。
4. 虽然本 MOD 本身未加密，但 Octopath 0 的主 pak 是加密的。你需要进入 Directory -> AES，并填入 Octopath Traveler 0 的 AES key。该 key 可在 BravelyPath Modular Discord 中获取，也可以用 IDA/Ghidra 手动提取。
5. 打开 Octopath 0 的 pak 或本 MOD 的 pak，找到你想修改的资产。把需要修改的内容导出/提取出来；对于 DataBase 资产，通常需要提取资产属性和原始数据（`.uasset`）。
6. 若要用修改后的 JSON 属性文件为 DataBase 资产打补丁，请使用 `UAssetMessagePack.exe`。下载链接可在 BravelyPath Modular Discord 的 #resources 中找到。
7. 使用 FluffyQuack 的 [UnrealPak](https://github.com/Dmgvol/UE_Modding/raw/main/Tools/UnrealPak.zip) 将资产重新打包成 pak 文件。注意：你用于打包的文件夹结构必须与游戏内路径一致。比如你修改了 `/Octopath_Traveler0/Content/Local/DataBase/Skill/` 下的 `SkillID`，那么你拖入 UnrealPak.exe 的文件夹中，必须包含通往该修改文件的**完全相同的路径**（你需要同时包含 `uasset` 和 `uexp` 文件）。
8. 最后，把新生成的 pak 文件放入游戏的 `Paks/` 文件夹中（也可参见 [安装](https://github.com/veganprimate/CotCPort/blob/main/README.zh.md#%E5%AE%89%E8%A3%85)）。

其他资源：
- [Johesy 的指南：为自定义职业编辑职业数据与角色技能组](https://github.com/dustintchan/OT0-Modding-Job-Skills)
- Celes Corbin 的 Modding Tutorial ZIP（下载链接：BravelyPath Modular Discord 的 #resources）
