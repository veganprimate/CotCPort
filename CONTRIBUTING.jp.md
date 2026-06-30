# 開発への参加
## **LANGUAGE:**  [English](CONTRIBUTING.md) | [日本語](CONTRIBUTING.jp.md) | [中文](CONTRIBUTING.zh.md) | [한국어](CONTRIBUTING.kr.md)

このプロジェクトへの貢献はどなたでも歓迎します。本リポジトリにはpakのほか、MODの各要素の仕組みに関する詳細ドキュメント、『オクトパストラベラー0』の一般的なMOD制作ドキュメント、そして未完了タスクの一覧（進め方として有力なアプローチの簡潔な議論付き）が含まれています。

以下は、『**大陸の覇者**』から未移植の機能一覧です。各項目は、現在の進捗状況を説明する本リポジトリ内のmarkdownファイルにリンクしています：
- [~~クエストとイベント~~（解決済み）](Resources/QuestEvent_Prog_jp.md)
- [エフェクトとアニメーション](Resources/Animations_Prog_jp.md)
- [オブジェクト](Resources/Objects_Prog_jp.md)
- [マップ／エリア／レベル](Resources/Maps_Prog_jp.md)
- [効果音と音楽](Resources/Sound_Prog_jp.md)
- [『大陸の覇者』の完全新規機能・コードの移植](Resources/Custom_Prog_jp.md)

基本的な作業内容は、UE 4.27版『大陸の覇者』のアセットから可能な限りデータを抽出し、そのデータを元に『オクトパストラベラー0』向けのUnreal Engine 5.4アセットを新たに作成し、MODがそれらのアセットを正しく読み込むようにすることです。cooked済みのUE 4.27アセットをUE 5.4に汎用的に変換する手段が存在しないため、このような工程が必要になります。

## CotCPortをベースにしたMOD
前述の通り、このMODを自作MODのベースとして自由にお使いいただけます。ただし、**CotCPort** のバグ報告を一箇所に集約するため、このページへのリンクをお願いします。pakはファイル名のアルファベット順に上書き読み込みされるため、ご自身のMODのファイル名を適切に変更すれば、**CotCPort** の後に読み込ませることができます。

## チュートリアルとリソース
以下は、本MODの**チュートリアルおよびリソース**の一覧です。『大陸の覇者』から移植されたデータを使って独自MODを作成しやすくすることを目的としています。
- [本MODや『オクトパストラベラー0』のアセットを抽出・再pak化する方法](Resources/Begin_jp.md)
- [キャラクター・ジョブの追加・変更、および主人公の新カスタマイズオプションの追加方法](Resources/customchars_jp.md)
- [敵やバトルの追加・変更方法](Resources/customenms_jp.md)
- [クエスト・イベントの追加・変更方法（WIP）](Resources/customqsts_ja.md)
- [スキル・状態異常・Availの追加・変更方法（WIP）](Resources/customskills_ja.md)
