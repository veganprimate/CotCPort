# 覇者移植

<p align="center">
  <strong>オクトラ0 大陸の覇者移植MOD</strong><br>
  <sub>原プロジェクト名: <code>CotCPort</code></sub>
</p>

## **言語:** [English](README.md) | **日本語** | [中文](README.zh.md) | [한국어](README.kr.md)

<br>

<p align="center">
<a href="#installation"><img alt="インストール" src="https://img.shields.io/badge/%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB-10363D?style=for-the-badge&logo=github&logoColor=white"></a>
<a href="Resources/HardMode_jp.md"><img alt="ハードモード" src="https://img.shields.io/badge/%E3%83%8F%E3%83%BC%E3%83%89%E3%83%A2%E3%83%BC%E3%83%89-880808?style=for-the-badge&logo=undertale&logoColor=white"></a>
<a href="#how-to-recruit-the-new-travelers"><img alt="旅人の加入" src="https://img.shields.io/badge/%E6%97%85%E4%BA%BA%E3%81%AE%E5%8A%A0%E5%85%A5-10363D?style=for-the-badge&logo=bookstack&logoColor=white"></a>
</p>
<p align="center">
<a href="CONTRIBUTING.md"><img alt="貢献" src="https://img.shields.io/badge/%E8%B2%A2%E7%8C%AE-337A00?style=for-the-badge&logo=git&logoColor=white"></a>
<a href="https://discord.gg/7YmAFJ3h5Z"><img alt="連絡先" src="https://img.shields.io/badge/%E9%80%A3%E7%B5%A1%E5%85%88-30363D?style=for-the-badge&logo=discord&logoColor=white"></a>
<a href="#acknowledgements"><img alt="謝辞" src="https://img.shields.io/badge/%E8%AC%9D%E8%BE%9E-484400?style=for-the-badge&logo=githubsponsors&logoColor=white"></a>
</p>

<p align="center"> <a href="#steam"><img alt="Steam" src="https://img.shields.io/badge/Steam-092226?style=flat-square&logo=steam&logoColor=white"></a> <a href="#microsoft-store"><img alt="Microsoft Store" src="https://img.shields.io/badge/Microsoft%20Store-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTUgNVY0YTMgMyAwIDAgMSA2IDB2MWgybDEgMTBIMkwzIDVoMlptMS4yIDBoMy42VjRhMS44IDEuOCAwIDEgMC0zLjYgMHYxWicvPjxwYXRoIGZpbGw9JyMwOTIyMjYnIGQ9J001IDhoMnYySDVWOFptNCAwaDJ2Mkg5VjhabS00IDNoMnYySDV2LTJAbTQgMGgydjJIOXYtMlonLz48L3N2Zz4%3D&logoColor=white"></a> <a href="#switch-real-hardwareatmosphère"><img alt="Switch実機" src="https://img.shields.io/badge/Switch%E5%AE%9F%E6%A9%9F-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTYuNSAxSDNhMiAyIDAgMCAwLTIgMnYxMGEyIDIgMCAwIDAgMiAyaDMuNVYxWm0zIDBIMTNhMiAyIDAgMCAxIDIgMnYxMGEyIDIgMCAwIDEtMiAySDkuNVYxWk00LjQgNS4yYTEuMiAxLjIgMCAxIDAgMCAyLjQgMS4yIDEuMiAwIDAgMCAwLTIuNFptNy4yIDMuMmExLjIgMS4yIDAgMSAwIDAgMi40IDEuMiAxLjIgMCAwIDAgMC0yLjRaJy8%2BPHBhdGggZmlsbD0nIzA5MjIyNicgZD0nTTYuNSAyLjN2MTEuNEgzQS43LjcgMCAwIDEgMi4zIDEzVjNBLjcuNyAwIDAgMSAzIDIuM2gzLjVabTMgMEgxM2EuNy43IDAgMCAxIC43Ljd2MTBhLjcuNyAwIDAgMS0uNy43SDkuNVYyLjNaJy8%2BPC9zdmc%2B&logoColor=white"></a> <a href="#switch-emulator"><img alt="Switchエミュレーター" src="https://img.shields.io/badge/Switch%E3%82%A8%E3%83%9F%E3%83%A5%E3%83%AC%E3%83%BC%E3%82%BF%E3%83%BC-092226?style=flat-square&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAxNiAxNic%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTUgNmg2YTQgNCAwIDAgMSAzLjcgMi41bC44IDIuMWEyLjMgMi4zIDAgMCAxLTMuNyAyLjVMMTAuNCAxMkg1LjZsLTEuNCAxLjFBMi4zIDIuMyAwIDAgMSAuNSAxMC42bC44LTIuMUE0IDQgMCAwIDEgNSA2Wm0tLjcgMnYxLjJIMy4xdjEuMmgxLjJ2MS4yaDEuMnYtMS4yaDEuMlY5LjJINS41VjhINC4zWm02LjcuN2EuOS45IDAgMSAwIDAgMS44LjkuOSAwIDAgMCAwLTEuOFptMiAxLjdhLjkuOSAwIDEgMCAwIDEuOC45LjkgMCAwIDAgMC0xLjhaJy8%2BPC9zdmc%2B&logoColor=white"></a> </p>


<p align="center">
<a href="CONTRIBUTING.md#mods-using-the-cotcport-as-a-basis"><img alt="覇者移植ベースMOD" src="https://img.shields.io/badge/%E8%A6%87%E8%80%85%E7%A7%BB%E6%A4%8D%E3%83%99%E3%83%BC%E3%82%B9MOD-1A4000?style=flat-square&logo=curseforge&logoColor=white"></a>
<a href="CONTRIBUTING.md#tutorials-and-resources"><img alt="チュートリアル＆資料" src="https://img.shields.io/badge/%E3%83%81%E3%83%A5%E3%83%BC%E3%83%88%E3%83%AA%E3%82%A2%E3%83%AB%EF%BC%86%E8%B3%87%E6%96%99-1A4000?style=flat-square&logo=readthedocs&logoColor=white"></a>
<a href="CONTRIBUTING.md#variety-of-resources-unordered"><img alt="リソース一覧" src="https://img.shields.io/badge/%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E4%B8%80%E8%A6%A7-1A4000?style=flat-square&logo=files&logoColor=white"></a>
</p>

<br>
<table border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td width="50%">
      <img alt="1" src="https://github.com/user-attachments/assets/5aaa0224-4c69-47bf-9fe3-4d9f1f9ee429" width="100%" />
    </td>
    <td width="50%">
      <img alt="2" src="https://github.com/user-attachments/assets/35bbf069-f2ce-4ed9-b6d5-be0ec8975fa6" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="3" src="https://github.com/user-attachments/assets/1c96f251-98f2-49d4-870d-3e0ae2af4f97" width="100%" />
    </td>
    <td width="50%">
      <img alt="4" src="https://github.com/user-attachments/assets/9a385a1d-4448-496e-b977-db4bd23676ac" width="100%" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <img alt="5" src="https://github.com/user-attachments/assets/20e1337d-4bbc-4fb4-b3ef-6c597d6652f4" width="100%" />
    </td>
    <td width="50%">
      <img alt="6" src="https://github.com/user-attachments/assets/c3c4286a-2f19-4574-b469-5ff4e750f81e" width="100%" />
    </td>
  </tr>
  <tr>
    <td colspan="2">
      <img alt="7" src="https://github.com/user-attachments/assets/2cd24e80-9b29-4376-a4d5-3d0a54be1529" width="100%" />
    </td>
  </tr>
</table>

**『オクトパストラベラー0』**向けに、**『オクトパストラベラー 大陸の覇者』**（日本版／グローバル版／中国版）およびシリーズ他作品のコンテンツを、可能なかぎり移植・統合するMODです。
- **『大陸の覇者』に登場する280人以上のキャラクター**を、**アビリティボード**、**ボイス台詞**、**キャラクターイラスト**、**ウィッシュベールへの独自組み込み**込みでプレイアブル化します。さらに、**独自制作のプレイアブルキャラクター15人**（_学者ノモス、フラ、ピエロ、フリート、ヒューゴ、ギメル船長、マティアス、ミゲル、エアハルト、クリス・クロスフォード、ゼリグ、ガルデラの巫女リアナ、栄光のルシアン、霜踏みのプロメ、ヴァネッサ・ヒゼル_）も追加します。
- **魔物闘技場**では、**『大陸の覇者』由来の新規ボス数百体**と戦えます。さらに、**独自制作**の高難度バトルも2種類あり、片方は**バルジェロ一家**、もう片方は**ブレイブリー三人組**との戦いです。
- 魔物闘技場の新たな敵を撃破することで、**『大陸の覇者』由来の新規アクセサリー**や**装備**を数十種解放できます。敵が強いほど、より優れた報酬を入手できます。
- **『大陸の覇者』由来の新規クエスト**を多数プレイできます。各キャラクターの**トラベラーストーリー**も含まれます。
- 回想録の各地点に**パーティーチャット**として組み込んだ、**数百本**の回想イベントを閲覧できます。
- **『大陸の覇者』から移植し、C++ MODで実装した新規状態異常／ステータス効果**を体験できます。

現在**開発中・未完成・実装予定**の機能には、以下が含まれます。

- **オルステラのマップ上でも機能する**、**シームレスなリアルタイム**昼夜切り替え。夜のオルステラ用には、『オクトパストラベラー』の世界観に沿った**独自NPC配置プリセット**を予定しています。
- **『大陸の覇者』、『オクトパストラベラー』、『オクトパストラベラーII』**の対応マップをもとに再構築した**500以上**の新規マップ。**ソリスティア**および**畏孵・オルステラ**の**新ワールドマップ**も含まれます。
- 『オクトパストラベラー』および『オクトパストラベラーII』の**サイドストーリー**を**『オクトパストラベラー0』向けに再構成**した版。
- **本編**および**『大陸の覇者』／『オクトパストラベラー』／『オクトパストラベラーII』由来の追加コンテンツ**の難度を大幅に引き上げる、_任意導入_の**ハードモード版**。詳細は [ハードモード版](https://github.com/veganprimate/CotCPort_Canary/blob/main/Resources/HardMode_jp.md) を参照してください。
- 『オクトパストラベラー』第1作から強く着想を得た、クリス・クロスフォードを中心とする**独自クエストライン**。**独自ボス戦**も含まれます。
- **『大陸の覇者』の大規模サブシステム**を実装するための**カスタムC++ MOD**。例：
  - **神獣**関連のバトルオプション
  - **底力**
  - **ペット**
  - ヴィクターホロウ闘技場での**大会**用のカスタム**UI**および**ゲームプレイ**ロジック
  - 武器・アクセサリーの**等級（Grade）**、**畏孵・オルステラ回廊**のローグライク要素
  - **試練の塔**
  - **名もなき町**などの**新しい建築タイプ**
  - 新規コンテンツの**ウィッシュベールへのより深い組み込み**
  - ……など

本MODは以下の言語に対応しています。
- **英語**
- **日本語**
- **韓国語**
- **中国語（簡体字）**
- **中国語（繁体字）**

![chargif](https://github.com/user-attachments/assets/00c35b82-3e9c-4810-8d8b-657f2c2a1151)

[worldmap_showcase.mp4](https://github.com/user-attachments/assets/e669f527-8412-4f91-8799-483e572caa32)

<a id="installation"></a>
## インストール
[Releases](https://github.com/veganprimate/CotCPort/releases) から、最新の（プレ）リリースの `CotCPort_Base.7z` と `CotCPort_Extras.7z` をダウンロードしてください。

 - `CotCPort_Base.7z` には、**必ず導入する必要があるベースMODファイル**が含まれています。DLL MODは相互に依存しています。
 - `CotCPort_Extras.7z` は、DLL MODおよびpak MODの**任意導入コレクション**です。**必須のものはなく**、また**互いに依存していません**。

アーカイブは、ゲーム内部フォルダを模した構成になっています。
```
OCTOPATH TRAVELER 0/
└── Octopath_Traveler0/
    ├── Binaries/
    │   └── Win64/
    └── Content/
        └── Paks/
```
目的は、アーカイブ内のファイルが既存の `Octopath_Traveler0` フォルダへそのまま**マージ**されるようにすることです。以下のような余分な入れ子フォルダを誤って作らないでください。
```
Octopath_Traveler0/Octopath_Traveler0/...
```
または：
```
Octopath_Traveler0/CotCPort_Base/Octopath_Traveler0/...
```
両アーカイブに含まれるファイルの詳細は、[v1.1.0-pre](https://github.com/veganprimate/CotCPort/releases/tag/v1.1.0-pre) のリリースノートにある「アーカイブ構成」セクションを参照してください。

インストール時には `Binaries/Win64/` 下の複数のDLLを**置き換える**必要がある場合があります。また、ゲーム起動前に、覇者移植（CotCPort）v1.0.0の `CotCPort_notex_p.pak` が `Content/Paks/` から**削除済み**であることも確認してください。`CotCPort_DataBase_p.pak` がこれを置き換える想定ですが、ファイル名が異なるため自動では置き換わりません。

**警告：** 現時点では、覇者移植（CotCPort）向けに開発された**C++ MOD**の**Switch対応版**も、hook/detour等を導入するための同等の**ネイティブランタイム層**も存在しません。Switch版『オクトパストラベラー0』は v1.0.8.0 ではなく v1.0.4.0 ベースでもあります。

C++ MODローダーが『オクトパストラベラー0』v1.0.8.0のMicrosoft Store版と**互換性があるかどうか**は、まだ**未検証**です。MS Store版のWin64 Shipping実行ファイルはSteam版と異なる可能性があり、その場合RVAがずれます。

Nintendo SwitchおよびMicrosoft Store版のプレイヤーは、当面プレリリース中の**pakのみ**の利用に制限する必要があるかもしれません。詳細は以下を参照してください。

<a id="steam"></a>
### Steam
`steamapps\common\OCTOPATH TRAVELER 0\Octopath_Traveler0\` 以下にアーカイブを展開してください。

<a id="microsoft-store"></a>
### Microsoft Store
1. すべてのpakについて、接尾辞を小文字の `_p` から大文字の `_P` に変更してください。
2. PCで **Xboxアプリ** を開き、左サイドバーのインストール済みゲーム一覧から **OCTOPATH TRAVELER 0** を右クリックし、**管理** を選択してください。
3. **ファイル** タブをクリックします。
4. **参照** をクリックします。
5. `<game install folder>\Octopath_Traveler0\` 以下にアーカイブを展開してください。

**警告：** MS Store版『オクトパストラベラー0』に対するC++ Modding APIの互換性はまだ**未証明**です。もしお使いの環境で**動作しない**ことが判明した場合（`Binaries/Win64/KingshipLogs/` 下にログが生成されているか確認してください）、`CotCPort_BluePrints_P.pak` は C++ MOD `CotCPort_Menus` と `CotCPort_BattleUI` に依存しているため、`Content/Paks/` フォルダから**削除**してください。

<a id="switch-real-hardwareatmosphère"></a>
### Switch（実機 / Atmosphère）
Switch版は**pakのみ**として扱ってください。**Windows用C++ MODはSwitchでは動作せず**、**代替手段もまだ作成されていません**。
Switch版のインストールには **Atmosphère LayeredFS** を使用します。ゲーム本体内のファイルを直接置き換えるのではありません。代わりに、改変済みのRomFS形式ファイルをSDカードに配置し、ゲーム起動時にAtmosphèreがそれをオーバーレイします。
『オクトパストラベラー0』のタイトルIDは `01005270232F2000` です。SDカード上に以下のフォルダを作成してください。
```
sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/
```
その中に、**`CotCPort_BluePrints_p.pak` 以外**のすべてのpakを配置してください。
```
sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_BluePrints_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Maps_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Sounds_p.pak sdmc:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/CotCPort_Textures_p.pak
```
`CotCPort_Extras.7z` 内のすべてのpakも互換性があります。

<a id="switch-emulator"></a>
### Switch（エミュレーター）
実機と同様、このプラットフォームも同じ理由で**pakのみ**として扱ってください。
以下の**内部**構成を持つMODを作成してください。
```
romfs/
└── Octopath_Traveler0/
    └── Content/
        └── Paks/
             ├── CotCPort_DataBase_p.pak
             ├── CotCPort_Maps_p.pak
             ├── CotCPort_Sounds_p.pak
             ├── CotCPort_Textures_p.pak
             └── (+ CotCPort_Extras.7z 内の任意のpak MOD)
```

### その他のプラットフォーム
ここで挙げたもの以外に、『OCTOPATH TRAVELER 0』はPS4、PS5、Xbox Series X|S、Switch 2でも提供されています。これらのプラットフォームには、pak MODでさえ導入するための実用的かつ一貫した方法は**ありません**。

<details>
<summary><strong>覇者移植の互換性テーブルを表示</strong></summary>

| プラットフォーム | 覇者移植対応状況 | 具体的なHomebrew / MODスタック名 | 言及しておくべき具体的なパス / 識別子 | 『オクトパストラベラー0』のUnreal `.pak` MODにとって重要な理由 | 判定 |
| --- | --- | --- | --- | --- | --- |
| **Steam PC** | **完全対応の主対象** | Windows DLLロード、`dwmapi.dll`プロキシ/ロード挙動、`OT0KingshipLoader.dll`、`KingshipMods`、`KingshipConfig` | `Octopath_Traveler0/Binaries/Win64/`; `Octopath_Traveler0/Content/Paks/`; Steam版Win64 Shipping実行ファイル | 覇者移植（CotCPort）の両半分、つまりUnreal pakファイルとWindows C++ランタイムのhook/detourが成立する唯一のプラットフォームです。 | **主対応プラットフォーム** |
| **Microsoft Store / PC版Xboxアプリ** | **pakのみの可能性が高く、DLL層は実験的** | 同じWindowsファイルシステム。ただしMSIX/Xboxアプリのインストール構造は異なる可能性があり、実行ファイルもSteam版RVAと一致しない可能性あり | `Octopath_Traveler0/Content/Paks/`; pak接尾辞は `_p.pak` ではなく `_P.pak` が必要な場合あり | ゲームがloose/追加pakを受け付けるならpakはロードされる可能性がありますが、**Win64実行ファイルがSteam版と異なるとC++ hookは壊れます**。 | **pakのみ / 実験的**として扱う |
| **Switch 1 実機 / Atmosphère** | **pakのみなら実用的に記載可能**。Switch `.nso` モジュールはDLLの代替にはなりにくい | Atmosphère LayeredFS、RomFSオーバーレイ、タイトル別`contents`フォルダ | `sd:/atmosphere/contents/<title_id>/romfs/`; 『オクトパストラベラー0』では `sd:/atmosphere/contents/01005270232F2000/romfs/Octopath_Traveler0/Content/Paks/` | Atmosphère LayeredFSはゲームのRomFS上にファイルを重ねられます。pakのみのMODには十分近い方式ですが、Windows DLL MODは不可能です。 | **pakのみ** |
| **Switch 1 エミュレーター** | **pakのみなら実用的な可能性あり**。上と同様 | `romfs/` ルートを持つエミュレーターMODフォルダ、Yuzu/Ryujinx/Ryubing/Eden系のRomFS置換 | `<mod folder>/romfs/Octopath_Traveler0/Content/Paks/*.pak` | 概念的にはAtmosphère LayeredFSと同じですが、エミュレーターのMODディレクトリ処理を経由します。Switchビルドを動かすためDLLは適用されません。 | **pakのみ** |
| **Switch 2** | **非対応** | 公開されたAtmosphère相当CFWスタックなし、公開されたLayeredFS相当MODワークフローなし | `sd:/atmosphere/contents/...` を流用しないこと。ユーザー向けの確立されたSwitch 2 `romfs` オーバーレイパスはありません | 公開情報では、カーネル/rootに影響せずHomebrew用途の実用性もない小規模なSwitch 2 userland ROP実証にとどまります。 | **非対応** |
| **Xbox Series X\|S リテール本体** | **非対応** | リテールXboxセキュリティモデル。リテールゲームとUWP Dev Modeアプリは別物 | ユーザーが触れる`Content/Paks/`なし、LayeredFS風`romfs`オーバーレイなし、`sdmc`相当なし | Xboxリテール本体には、任意のリテールゲームコンテンツ用の通常MODフォルダが公開されていません。Dev ModeはUWPアプリの展開用で、インストール済みリテールゲームのパッチ用ではありません。 | **非対応** |
| **Xbox Series X/S Dev Mode** | **覇者移植には有用ではない** | UWPアプリ展開、Dev Home、Device Portal、Homebrew風UWPアプリ/エミュレーター | Dev Modeアプリパッケージであり、リテールゲームのインストールディレクトリではない | 自作UWPアプリは展開できますが、リテールXbox版『オクトパストラベラー0』に`.pak`を注入する対応経路にはなりません。 | **非対応：Dev Mode ≠ リテールゲームMOD** |
| **PS4リテール / stock firmware** | **非対応** | stock Orbis OS、署名済みパッケージ、ユーザー用pakフォルダなし | ユーザーに公開された`Octopath_Traveler0/Content/Paks/`相当なし | stock PS4は、インストール済みリテールゲームへ任意のUnreal pakを注入する通常のユーザー書き込み先を提供していません。 | **非対応** |
| **PS4 Homebrew / GoldHEN環境** | **研究用のみ。READMEでサポート対象にはできない** | GoldHEN、PPPwn世代サポート、Itemzflow、PS4 Cheats Manager、GoldHEN XML patch engine | GoldHEN patches: `/user/data/GoldHEN/patches/xml/<TitleID>.xml`; Itemzflow app logs: `/user/app/ITEM00001/logs/itemzflow_app.log`; daemon logs: `/data/itemzflow_daemon/daemon.log`; Itemzflow タイトルID: `ITEM00001` | dump、fake package、patch、trainer、XMLメモリパッチ等は可能でも、**cleanなloose-pakオーバーレイと同じではありません**。 | **非対応 / 研究用**。誰かがPS4パッケージ/パッチ手順を構築・検証する必要あり |
| **PS4パッケージ再構築型ワークフロー** | **専用移植なら理論上可能。通常インストールではない** | ダンプ/復号済みパッケージ、GP4、fake package再構築、app/update package処理 | ランタイムのゲームマウントは`app0:/`として語られることが多いが、ユーザー向けpatchツールは`/user/data/GoldHEN/patches/xml/`などを使う | pak MODなら、ダンプしたゲーム/アップデートパッケージ内のpakを置換・追加して再構築/再インストールするのが研究経路になりそうです。これは通常の「ここにpakをコピー」ガイドの範囲外です。 | PS4パッケージング経験のある保守担当が必要 |
| **PS5リテール / stock firmware** | **非対応** | stock PS5 OS、署名済みパッケージ、ユーザー用pakフォルダなし | `Content/Paks/`相当なし、LayeredFS風の公開MODパスなし | PS4と同じ広い問題があり、Homebrew環境もさらに未成熟で断片的です。 | **非対応** |
| **PS5 Homebrew / etaHEN環境** | **研究用のみ** | etaHEN、Kstuff、DPIv2、Itemzflow PS5 builds、XML patch/plugin ecosystem | etaHENのリリースノートではFW `8.40–10.01`のcheats対応、Kstuffメニュー、custom background package installer、DPIv2関連機能が言及されています。Itemzflow 1.09 PS5では`5.xx`/`6.xx`対応、etaHEN toolbox設定、XML patches plugin、実験的self-decrypterが言及されています。 | PS5 Homebrewツールはありますが、『オクトパストラベラー0』のloose `.pak` インストール経路を意味するものではありません。既存支援はAtmosphère風RomFSオーバーレイより、パッケージ管理・trainer・cheat・patch・dump寄りです。 | **非対応 / 研究用** |
| **PS4/PS5 XML patch ecosystem** | **覇者移植のpakファイルには直接有用ではない** | Illusion/GoldHEN patch XML、Itemzflow XML patches plugin、PS4 Cheats Manager | 例：`<TitleID><ID>CUSA01765</ID></TitleID>` と `<Metadata ... AppVer="01.00" AppElf="eboot.bin">`; patchは`CUSA00001.xml`のようなタイトル別XMLに保存 | メモリ/コード値、debug menu、バイト列等はpatchできますが、Unreal asset/data pakファイルはインストールできません。将来CotCPortに小さな実行ファイルpatchが必要になった場合のみ有用です。 | **pakインストールには不適用** |
| **PS4/PS5 Itemzflow** | **有用な兆候だが、覇者移植対応には不十分** | Itemzflow Game Manager、PS4/PS5 downloads、`ITEM00001`パッケージID | PKG-ZoneはItemzflowをPS4 home menu代替として掲載し、content ID `IV0002-ITEM00001_00-STOREUPD00000000`、タイトルID `ITEM00001`、PS4/PS5 downloadsを示しています。 | PS4/PS5 Homebrewツールの存在を示す材料にはなりますが、『オクトパストラベラー0』がloose pak overlayをロードできる証拠ではありません。 | **研究用のみ** |
</details>

## 他のMODとの互換性
覇者移植は、ほぼすべての他のpak MODと互換性がない可能性が高いです。DLL MODも、おおむね `CotCPort_DataBase` 内の規約に依存していますが、これらは設定可能です。
<details>
<summary><strong>CotCPort_DataBaseのファイルツリーを表示</strong></summary>

```
CotCPort_DataBase
├── AIBattle
│   ├── TacticalActionList
│   ├── TacticalAssignList
│   ├── TacticalList
│   └── TacticalSkillList
├── Battle
│   ├── BattleAbortConditions
│   ├── BattleActionID
│   ├── BattleCameraParams
│   ├── BattleCameraSet
│   ├── BattleEventCommand
│   ├── BattleEventList
│   ├── BattleParams
│   ├── BattlePositions
│   └── BattleVoiceList
├── Character
│   ├── CharacterAnimationID
│   ├── CharaGuest
│   ├── CharaID
│   ├── CharaNpcList
│   ├── CharaPlayer
│   ├── CharaPlayerSpecialSkillList
│   ├── CharaRuneInitialOpenTypeID
│   ├── CharaSkillLearningPoint
│   ├── CharaSpecialStrengthenPoint
│   ├── CharaStatusID
│   ├── JobID
│   ├── LevelParameterTable
│   ├── LevelPointTable
│   └── PlayerMemberSet
├── Effect
│   ├── AttachEffect
│   ├── EffectList
│   └── PlayerAttachEffectList
├── Enemy
│   ├── EncountEffectTypeID
│   ├── EncountVolume
│   ├── EnemyBattleAnimSet
│   ├── EnemyDropID
│   ├── EnemyFormations
│   ├── EnemyGroups
│   ├── EnemyID
│   ├── EnemyModeID
│   ├── EnemyParts
│   ├── EnemyReinforcements
│   ├── EnemyTypeID
│   ├── EnemyWeakChangeID
│   ├── EnemyWeakID
│   ├── EnemyWeakLockID
│   ├── SymbolEnemyGroupList
│   └── SymbolEnemyList
├── Event
│   ├── EventFinishProcessList
│   ├── EventFlagVariableList
│   ├── EventList
│   ├── VJoin
│   │   └── (『大陸の覇者』JP/CN/GL 由来の全8288イベントを『オクトパストラベラー0』形式の加入イベントとしてインポート。『オクトパストラベラー0』標準の加入イベントは未編集) 
│   └── VSub
│        └── SQ 
│            └── (「英雄の島：過去」イベントを編集し、各武器を対応するジョブタワーのボスが守るように変更) 
├── GameText
│   ├── Localize
│   │   ├── EN-US
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   ├── KO-KR
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   ├── ZH-CN
│   │   │   ├── SystemText
│   │   │   │   ├── GameTextCharacter
│   │   │   │   ├── GameTextEnemy
│   │   │   │   ├── GameTextFC
│   │   │   │   ├── GameTextItem
│   │   │   │   ├── GameTextMap
│   │   │   │   ├── GameTextPC
│   │   │   │   ├── GameTextQuest
│   │   │   │   ├── GameTextSkill
│   │   │   │   └── GameTextUI
│   │   │   └── TalkText
│   │   │       ├── GameTextEvent
│   │   │       └── GameTextNPC
│   │   └── ZH-TW
│   │       ├── SystemText
│   │       │   ├── GameTextCharacter
│   │       │   ├── GameTextEnemy
│   │       │   ├── GameTextFC
│   │       │   ├── GameTextItem
│   │       │   ├── GameTextMap
│   │       │   ├── GameTextPC
│   │       │   ├── GameTextQuest
│   │       │   ├── GameTextSkill
│   │       │   └── GameTextUI
│   │       └── TalkText
│   │           ├── GameTextEvent
│   │           └── GameTextNPC
│   ├── SystemText
│   │   ├── GameTextCharacter
│   │   ├── GameTextEnemy
│   │   ├── GameTextFC
│   │   ├── GameTextItem
│   │   ├── GameTextMap
│   │   ├── GameTextPC
│   │   ├── GameTextQuest
│   │   ├── GameTextSkill
│   │   └── GameTextUI
│   └── TalkText
│       ├── GameTextEvent
│       └── GameTextNPC
├── Item
│   └── ItemList
├── Level
│   └── ExistSublevelParam
├── Map
│   ├── MapLightSet
│   ├── MapListTable
│   ├── MapPathListTable
│   ├── MapRegionCategory
│   ├── MapReplaceList
│   ├── TownList
│   ├── WorldList
│   ├── WorldMapList
│   ├── WorldMapMaskList
│   └── WorldMapRouteList
├── MonsterArena
│   └── MonsterArenaList
├── Narration
│   └── NarrationList
├── Npc
│   └── (929アセット。『オクトパストラベラー0』と『大陸の覇者』で共有されるすべてのNpcSetList/NpcTalkListに『大陸の覇者』側データを補完し、『オクトパストラベラー0』に存在しないマップのNPCデータは『オクトパストラベラー0』形式でインポート) 
├── Object
│   ├── ObjectList
│   ├── RecoveryPointList
│   ├── TreasureBoxList
│   └── TreasureBoxRewardList
├── PartyChat
│   └── PartyChatList
├── Quest
│   ├── LimitConditions
│   ├── MainStoryRingList
│   ├── QuestAggregatedPoint
│   ├── QuestAggregatedTaskList
│   ├── QuestList
│   ├── QuestTaskList
│   └── QuestTrigCondition
├── Skill
│   ├── ResistType
│   ├── SkillAilmentOffsets
│   ├── SkillAilmentSkillReplaceList
│   ├── SkillAilmentType
│   ├── SkillAvailID
│   ├── SkillAvailMagnificationConditionList
│   ├── SkillConditionList
│   ├── SkillEffectiveID
│   ├── SkillEnhancedList
│   ├── SkillHitRateList
│   ├── SkillIconList
│   ├── SkillID
│   ├── SkillResistAilmentID
│   ├── SkillResistList
│   ├── SkillUniqueIconID
│   ├── SkillVisitorList
│   ├── SpecialSkillGrowthList
│   ├── SpecialSkillID
│   └── WeaponType
├── SkillBoard
│   └── SkillBoardData
├── Sound
│   ├── PartVoiceID
│   ├── SoundList
│   ├── SoundSheetList
│   ├── VoiceEffectID
│   └── VoiceSymbolList
├── Texture
│   ├── CharaTexID
│   ├── EnemyTexID
│   ├── LinkIconList
│   └── TextureID
├── Trigger
│   ├── CurveID
│   └── LightAreaPreset2
└── Village
    ├── VillageBuildingData
    ├── VillageResidentFavoriteBuilding
    ├── VillageResidentInterior
    └── VillageResidentSkill
```

</details>

<a id="how-to-recruit-the-new-travelers"></a>
## 新規トラベラーの加入方法
現在、各キャラクターの**トラベラーストーリー**が加入イベントとして機能しています。ただし、これは新たに追加された280人以上のキャラクターのうち140人分に限られます。将来的には、本MODの新規プレイアブルキャラクター全員に**固有の加入イベント**を追加する予定ですが、まだ時間がかかります。

現時点では、`CotCPort_Extras.7z` アーカイブ内の**任意導入**pak MOD `RecruitEvent_p.pak` を使うことで、本MODが提供するすべてのキャラクターを一括加入できます。
そのためには、下図の**ウィッシュベール街道**へ向かい、**物忘れのリス**に話しかけてください。

<img width="1239" height="541" alt="image" src="https://github.com/user-attachments/assets/a39b0888-f820-462b-81c0-1a747ab59528" />

**4つの選択肢**が表示されます。
 1. MODキャラクター全員を加入させる + ウィッシュベールへ追加
 2. MODキャラクター全員を加入させる
 3. MODキャラクター全員の加入を解除
 4. MODキャラクター全員をウィッシュベールから削除

MODのアップデートで新たなプレイアブルキャラクターが追加された場合、`RecruitEvent_p.pak` がpakフォルダに入っていれば、**同じNPCに再度話しかける**ことで加入できます。
すでに加入済みのキャラクターについては、`EntryPlayerCharacter` が `false` を返すため、目に見える加入演出は**抑制**されます。また、opcode `10029  EvCmAddPartyMember` はダイアログを強制しません。ただし、これは加入済みキャラクターに対して `10029` がスキップされるという意味ではありません。実際に行われる処理がごく軽微なだけです。加入済みプレイヤーでも、処理対象となり、HP/SP回復、パーティー内位置の設定、アクティブパーティーのキューシート更新などが行われる場合があります。装備や装備中スキルを失ったり、ステータスがリセットされたり、二重加入したりすることは**ありません**。

**警告1：** 加入イベントが**ウィッシュベール街道**にあるため、キャラクターを早く加入させすぎないよう注意してください。プロローグ中は、すべてのパーティーメンバーの**装備スロットがロック**されています。このクエスト状態で加入したキャラクターも装備スロットがロックされます。この効果はプロローグ完了後も**自動では解除されません**。影響を受けたキャラクターは全員、**加入解除してから再加入**させる必要があります。

**警告2：** 加入するキャラクターのステータスは、**リスに話しかけたキャラクター**、つまりパーティーリーダー（編成メニュー左上のキャラクター）に応じてスケーリングされます。

## 連絡先
**BravelyPath Modular** Discordサーバーの **#cotc-characters-mod** チャンネルで連絡できます。このチャンネルには他のコントリビューターやMOD制作者も参加しています。サーバーにはこちらから参加できます：https://discord.gg/7YmAFJ3h5Z

<a id="acknowledgements"></a>
## 謝辞
以下の方々に感謝します。
- **BoltGSR** と **MarvinCx886**：『大陸の覇者』日本版のデータ提供
- **Oersted**：『大陸の覇者』中国版のデータ、完全なスキルシート、スプライトシート、カスタムユニット案の提供、および支援
- **Vat**：キャラクターと敵の拡張スプライトシート提供、またゲーム内で非常に鮮明に表示されるよう多数のテクスチャをcookしてくれたこと
- **Solo Traveler**：多種多様な機能のプレイテスト、ハードモード版のアイデア提供、カスタム闘技場戦の詳細な説明
- **igoticecream**：開発中に頻繁に参照した『オクトパストラベラー0』用SDKの生成
- **MarvinCx886**：カスタムイベントの正しい実装方法の解明、マップ移植の初期試行、貴重な知見の提供、および魔物闘技場スクロールクラッシュ修正案の考案
- **MarvinCx886** と **JohnZ**：『大陸の覇者』からSoundデータを移植する方法の解明と、その詳細な解説文書の作成
