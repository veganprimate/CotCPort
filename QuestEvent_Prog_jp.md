## **LANGUAGE:**  [English](QuestEvent_Prog_en.md) | [日本語](QuestEvent_Prog_jp.md) | [中文](QuestEvent_Prog_zh.md) | [한국어](QuestEvent_Prog_kr.md)

### Update (2/07/2026)
**MioJoin** mod の制作中に MarvinCx886 が発見した内容のおかげで、イベントの移植が可能になりました。単に私が、イベントアセットのメタデータを新しいパスに合わせて変更し忘れていただけでした。これは [UAssetGUI](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest) と、『オクトパストラベラー0』の Unreal Mappings を使えば修正できます。`EventList` 内の `m_Name` と `m_Version` は、確かに `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` というパスに解決されます。

さらに、OT0 のマップの多くには CotC（大陸の覇者）のデータが未使用のまま残っていることがあります。たとえば、ゲーム内に存在しないNPC（例：プロメ）の配置位置やコリジョン情報などです（`QuTr_PC148_01_01` 参照）。これらのオブジェクトは、該当する `NpcSetList` の `m_AppearLabel` から参照でき、定義済みのコリジョンデータ付きでその場所にNPCをスポーンさせられます。

<img width="1113" height="824" alt="image" src="https://github.com/user-attachments/assets/37b0af5c-c866-4ad6-b1c0-4d48986eba21" />

`.../Local/DataBase/Quest/` および `.../Local/DataBase/Event/` 配下ファイルの構造と、新しいイベント／クエストの追加方法に関する詳細チュートリアルは近日公開予定です：  
[How to add custom quests and events](customqsts_en.md)

#### Current Issues
現在の主な課題は、既存マップへの新規NPC追加と、完全新規エリアの移植に大きく移っています。詳細は [**マップ／エリア／レベル**](Maps_Prog_jp.md) を参照してください。

また、OT0 上で正しく表示・動作させるために、イベント／クエスト／敵／NPCデータの多くを手動で調整する必要があります：
 - 移植したイベント内のNPCが、誤った方向を向いてしまうことがある

<img width="1167" height="649" alt="image" src="https://github.com/user-attachments/assets/a748fc6a-52a2-4223-ab8c-1e09743ae8e7" />

 - CotCから移植したクエストの会話で「**はい／いいえ**」の選択肢が出る場合、OT0とは逆に、デフォルトで上側の選択肢が「いいえ」になってしまい、どちらを選んでもSEが不自然になる。キャンセル時のカーソル位置も誤った位置に戻る
 - 推奨レベルが、ボスの強さに対して概ね20〜30レベル高すぎる
 - CotC由来のボスは、概ねEXPが多すぎる（例：神官クラトスはHP約10万の弱めのボスだが、EXP 12,000を得られた。OT0のオル・サザントスの約3倍）
 - CotCから移植したクエストNPCは、デフォルトで奇妙なFCデータ（フィールドコマンド関連データ？）を持っていることが多い。例：

<img width="1239" height="667" alt="image" src="https://github.com/user-attachments/assets/c56a5b05-862f-45f2-a40a-cabe477d5fe6" />

 - CotCでは、不可視NPCを「クエストノード」として使うことが多く、近づくとパスアクション／FCデータが表示されてしまう。例：

<img width="1272" height="714" alt="image" src="https://github.com/user-attachments/assets/b5296bd4-fd61-4d75-a98a-1e05e391b349" />

 - シーンが、会話を開始したパーティメンバーを基準に進行してしまい、必ずしもゼロ／指輪の担い手（Ringbearer）にならない
 - クエストタスクのいくつかで「目的文」が存在しない、または目的文の代わりにタスクタイトルが表示され、OT0では不自然に見える
 - CotCの報酬タイプの多くは、OT0では挙動が異なる／あるいはそもそも機能しない。詳細は [How to add custom quests and events](customqsts_en.md) を参照

### Original Article (CORRECTED)
<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/226d0592-5823-490b-a37a-5684fdc9176b" />

_この見づらい画像からも分かる通り、CotC（大陸の覇者）からクエストを移植すること自体は確実に可能です。問題はイベントの移植にあります。イベントは、カットシーン、ボス戦の開始、戦闘中の会話、ダンジョン内スイッチなど、さらに多くの要素を担っています。イベントがなければ、クエストはクエストマーカーと報酬だけの存在に過ぎません。_

_`/Game/Content/Local/DataBase/Event/` 配下の `EventList` アセットを見ると、ゲームがイベントIDをロードした時点で `m_Name` と `m_Version` が `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` に解決されるように思えますが、~~残念ながらそうではありません~~：~~テストの結果、ゲームはこれらのエントリを参照しておらず、イベントIDをイベントアセットへのパスに内部的に関連付けているように見えます~~_ **（これらのキーは該当パスに解決されますが、既存イベントに対して変更しても無視されるようです）**。

_さらに、同じイベントであっても、どのイベントアセットに新しい package プロパティをパッチするか、そして特にどのフォルダに置かれているかによって、ゲーム内での再生内容が変化します。_  
_たとえば VPartychat 配下のイベントは自動的に Wishvale の酒場が背景になり、VTest はそこで再生できるイベントが非常に制限されるなど、実質的に `EventList` の `m_Kind` キーを無視している挙動になります。_ **（これは現在も同様のようです）**

_~~これは、EventList に新規エントリを追加するだけではカスタムイベントを追加できず、エンジンが（あるいはこれから）完全な Unreal オブジェクトパスを使用する地点でパイプラインをフック（介入）する必要があることを示唆します~~。_

_私は ~~現在~~ PolyHook2 を用いてそれを実現する UE4SS 用の dll mod を開発していました。テンプレートとしては [このmod](https://github.com/UE4SS-RE/RE-UE4SS/tree/main/cppmods/KismetDebuggerMod) を使用しています。  
また、Dumper-7 で生成したOT0のIDAマッピング（BravelyPath Modular Discord の #resources にあるSDKを参照）を使い、IDAで広範に解析していました。実行時にイベントIDを上書きするところまで到達しています。_ **（中止：dll mod の詳細は過去コミットを参照してください）**
