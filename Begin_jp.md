## **LANGUAGE:**  [English](Begin_en.md) | [日本語](Begin_jp.md) | [中文](Begin_zh.md) | [한국어](Begin_kr.md)

1. [FModel](https://fmodel.app/) をダウンロードしてインストールしてください（FModel は Wine 経由で Linux でも動作します）。
2. Directory -> Selector をクリックし、『Octopath Traveler 0』を新しいゲームとして追加します。DataBase ファイルを自動でデシリアライズできるよう、UE Version は必ず `GAME_OctopathTraveler0` を選択してください。最後に、Directory で Octopath 0 の `Paks/` フォルダを指定します。  <img width="488" height="348" alt="FModel" src="https://github.com/user-attachments/assets/211ef83b-3427-4526-a75f-2029cd840b72" />
3. Settings に移動し、`Local Mapping File` を有効化して『Octopath Traveler 0』の mappings をインポートします。mappings は BravelyPath Modular Discord で入手できるほか、UE4SS を使って手動で抽出することも可能です。
4. MOD 自体は暗号化されていませんが、Octopath 0 のメイン pak は暗号化されています。Directory -> AES に移動し、『Octopath Traveler 0』の AES キーを入力する必要があります。AES キーは BravelyPath Modular Discord で入手できるほか、IDA/Ghidra を使って手動で抽出することも可能です。
5. Octopath 0 の pak もしくは MOD の pak を開き、変更したいアセットへ移動してください。変更したいものを抽出します。DataBase アセットの場合は、アセットプロパティと raw data（`.uasset`）を抽出することが多いです。
6. 変更した JSON のプロパティファイルで DataBase アセットにパッチを当てるには `UAssetMessagePack.exe` を使用してください。ダウンロードリンクは BravelyPath Modular Discord の #resources にあります。
7. FluffyQuack 氏の [UnrealPak](https://github.com/Dmgvol/UE_Modding/raw/main/Tools/UnrealPak.zip) を使用して、アセットを pak ファイルに再パックしてください。pak 化するフォルダは、ゲーム内のパス構造を反映している必要があります。例えば `/Octopath_Traveler0/Content/Local/DataBase/Skill/` 配下の `SkillID` を変更した場合、UnrealPak.exe にドラッグするフォルダは、変更したファイルへ至るまでのパスが完全に同一になるように作成する必要があります（`uasset` と `uexp` の両方のファイルを含めてください）。
8. 最後に、新しく作成した pak ファイルをゲームの `Paks/` フォルダに入れてください（[インストール](https://github.com/veganprimate/CotCPort/blob/main/README.jp.md#%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB) も参照）。

その他のリソース：
- [カスタムジョブ向けに、ジョブデータおよびキャラクターのスキルセットを編集するための Johesy 氏のガイド](https://github.com/dustintchan/OT0-Modding-Job-Skills)
- Celes Corbin 氏の Modding Tutorial ZIP（ダウンロードリンク：BravelyPath Modular Discord の #resources）
