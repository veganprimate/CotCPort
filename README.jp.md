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


『オクトパストラベラー0』向けのMODで、『オクトパストラベラー 大陸の覇者』（グローバル版／日本版／中国版。以下「大陸覇者」）から可能な限り多くのコンテンツを移植することを目的としています。
- 大陸覇者の270体以上のユニットを、完全なスキルボード付きで全て使用可能（カスタムの新規ユニット6体（学者ノモス、フラ、フリート、ヒューゴ、ギメル、マティアス、ミゲル）を含む）
- モンスターアリーナで、大陸覇者の数百体のボスと戦闘可能
- モンスターアリーナの新たな敵を倒すことで、大陸覇者由来の新アクセサリーを数十種類アンロック可能

本MODは現在、以下の言語に対応しています：
- 英語
- 日本語
- 韓国語
- 中国語（簡体字）
- 中国語（繁体字）

![chargif](https://github.com/user-attachments/assets/00c35b82-3e9c-4810-8d8b-657f2c2a1151)


## インストール
Releases から `notex` pak のいずれかと `texture` pak、さらに `RecruitEvent_p.pak` をダウンロードしてください。`notex` には DataBase ファイルが含まれ、`texture` には大陸覇者から移植したテクスチャとモデルが含まれます。また `RecruitEvent_p.pak` は、フェン、メイシィ、えらばれしもの の最初のパーティ会話イベント（「怪我の治療」）を、追加された260体以上の全ユニットの加入イベントに置き換えるMODです。このMODは、新規ユニットを全て加入させた後は削除できます。
これらのファイルはゲームの Paks フォルダに入れてください。場所は『オクトパストラベラー0』のバージョンおよびインストール形態によって異なります。

### Steam
3つの pak を `steamapps\common\Octopath_Traveler0\Octopath_Traveler0\Content\Paks` に配置してください。

### Microsoft Store
1. 全ての pak のサフィックス `_p` を `_P` に変更してください
2. PC の **Xbox アプリ**を開き、左サイドバーのインストール済みゲーム一覧で **Octopath Traveler 0** を右クリックし、**管理**を選択してください
3. **ファイル** タブをクリックしてください
4. **参照** をクリックしてください
5. pak ファイルを `<game install folder>\Octopath_Traveler0\Content\Paks\` に配置してください

### Switch（実機／Atmosphère）
3つの pak を `<SD Card>\atmosphere\Contents\01005270232F2000\romfs\Octopath_Traveler0\Content\Paks` に配置してください

### Switch（エミュレーター）
`romfs/Octopath_Traveler0/Content/Paks` のフォルダ構成で MOD を作成し、3つの pak を Paks に配置してください。

## 新たに追加された旅人（キャラクター）の加入方法
<img width="1286" height="726" alt="12" src="https://github.com/user-attachments/assets/f06205a2-5c30-4a8f-8718-5ee69d84db95" />

1. `RecruitEvent_p.pak` が Paks フォルダに入っていることを確認してください。
2. エンバーグロウにいるロングボウマスター（上の画像参照）に話しかけてください。イベントが発生し、本MODで追加されるプレイアブルキャラクターが全員加入します。

## （任意）ハードモード版
本MODは EnemyID アセットをはじめ複数のアセットを変更しているため、同じアセットを編集する他のMODとは互換性がありません。これらを併用したい方向けに、本MODのハードモード版（ファイル名に `_hard` サフィックスが付いたもの）を作成しました。変更内容は以下の通りです：
- 通常敵のHPが2倍、ボスのHPが3倍になります
- 通常敵のステータス（Phys. Atk.、Phys. Def.、Elem. Atk.、Elem. Def.、Speed、Accuracy、Crit.、Crit. Def.、最大シールド数）が25%増加し、ボスは50%増加します。ボス撃破で得られるXP、JP、お金も20%増加します。
- ソフトロックを防ぐため、個別の敵に調整を行いました（偽オルベリク、蒼翠の大狼、枢機卿テラグラフ）
- 一部ボスのステータス、スキル、戦術に変更を加えました（WIP。現時点では主に「全てを授けし者」系ボスに影響します）

## 貢献（Contributions）
どなたでも本プロジェクトへの貢献を歓迎します。このリポジトリには pak に加えて、本MODの各要素の仕組みに関する詳細なドキュメント、『オクトパストラベラー0』向けの一般的なMOD制作ドキュメント、そして未完了タスクの一覧（進め方として考えられる案の簡単な議論付き）が含まれています。
以下は、『オクトパストラベラー 大陸の覇者』からまだ移植されていない機能の一覧です。各機能は、現状の進捗を説明する本リポジトリ内の markdown ファイルにリンクしています：
- [クエストとイベント](QuestEvent_Prog_jp.md)
- [エフェクトとアニメーション](Animations_Prog_jp.md)
- [オブジェクト](Objects_Prog_jp.md)
- [マップ／エリア／レベル](Maps_Prog_jp.md)
- [効果音と音楽](Sound_Prog_jp.md)
- [大陸覇者から完全に新しい機能を移植](Custom_Prog_jp.md)

基本的には、UE 4.27 版『オクトパストラベラー 大陸の覇者』のアセットから可能な限りデータを抽出し、そのデータを使って『オクトパストラベラー0』向けに Unreal Engine 5.4 の新規アセットを作成し、MODがそれらのアセットを読み込むようにする作業になります。これは、cooked 済みの UE 4.27 アセットを UE 5.4 に一般的な方法で変換する手段が存在しないためです。

### CotCPort をベースにしたMOD
前述の通り、このMODを自作MODのベースとして自由に使用して構いませんが、大陸覇者移植に関するバグ報告を一箇所に集約するため、このページへのリンクをお願いします。pak はアルファベット順にファイルを上書きするため、あなたのMODのファイル名を変更することで CotCPort の後に読み込ませることができます。

### チュートリアルとリソース
以下は、本MODのチュートリアルおよびリソースの一覧です。大陸覇者から移植されたデータを使って独自MODを作りやすくするためのものです。
- [このMODおよび／またはOT0からアセットを抽出する方法](Begin_jp.md)
- [カスタムキャラクターを追加する方法](customchars_jp.md)
- [カスタム敵および戦闘を追加する方法](customenms_jp.md)
- [カスタムクエストとイベントを追加する方法](customqsts_jp.md)
- [カスタムスキルと avail を追加する方法](customskls_jp.md)

## 連絡先
BravelyPath Modular Discord の #cotc-characters-mod チャンネル（他の貢献者やMOD制作者も参加しています）で連絡可能です：
https://discord.com/invite/t43Kbrp

## 謝辞
ご協力いただいた皆さんに感謝します。
- BoltGSR：『オクトパストラベラー 大陸の覇者』日本版のデータを提供してくれました。
- Oersted：『オクトパストラベラー 大陸の覇者』中国版のデータに加え、完成版のスキルシート／スプライトシート、そしてカスタムユニットのアイデアまで提供してくれました。
- Vat：キャラクターや敵の拡張スプライトシートを提供してくれたほか、多数のテクスチャをゲーム内でクリアに見えるようにクック（Cooking）してくれました。
- Solo Traveler：さまざまな機能のプレイテストに協力してくれたほか、ハードモード版のアイデアや、カスタムのアリーナ戦について詳細な提案をしてくれました。
- igoticecream：『オクトパストラベラー0』向けのSDK生成を行い、カスタムイベント実装の方向性について助言してくれました。
- MarvinCx886／JohnZ：『オクトパストラベラー 大陸の覇者』からサウンドデータを移植する方法を解明し、その手順を非常に詳しくまとめてくれました。
