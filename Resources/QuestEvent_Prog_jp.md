## **LANGUAGE:**  [English](QuestEvent_Prog_en.md) | [日本語](QuestEvent_Prog_jp.md) | [中文](QuestEvent_Prog_zh.md) | [한국어](QuestEvent_Prog_kr.md)

### Update (2/07/2026)
MarvinCx886 が **MioJoin** MOD の作業中に発見した内容のおかげで、イベント（Events）の移植が可能になりました。原因は単純で、私がイベントアセットのメタデータを「新しい配置先パス」に合わせて書き換えるのを忘れていただけでした。これは [UAssetGUI](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest) と、『オクトパストラベラー0』の Unreal Mappings を使って修正できます。`EventList` 内の `m_Name` と `m_Version` は、確かに `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` というパスに解決されます。

さらに、OT0 のマップの多くには『オクトパストラベラー 大陸の覇者』（以下『大陸の覇者』）の「置き土産」のようなデータが残っている場合があります。たとえば、ゲーム本編に登場しないNPC（例：プロメ）の配置位置・コリジョン情報などです（`QuTr_PC148_01_01` 参照）。これらのオブジェクトは、該当する `NpcSetList` の `m_AppearLabel` から参照でき、定義済みのコリジョンデータを保ったまま、その地点にNPCをスポーンさせることができます。

<img width="1113" height="824" alt="image" src="https://github.com/user-attachments/assets/37b0af5c-c866-4ad6-b1c0-4d48986eba21" />


`.../Local/DataBase/Quest/` と `.../Local/DataBase/Event/` 配下ファイルの構造、および新しいイベント／クエストの追加方法に関する詳細チュートリアルは近日公開予定です：  
[How to add custom quests and events](customqsts_en.md)

#### Current Issues
現在の主な課題は、「既存マップへの新規NPCの追加」と「完全新規エリアの移植」に大きく移っています。詳細は [**Progress on Maps/Areas/Levels**](Maps_Prog_en.md) を参照してください。

また、OT0 上で正しく見せる／動かすために、イベント・クエスト・敵・NPCデータの多くを手動で調整する必要があります：
 - 移植したイベント内のNPCが、誤った方向を向いてしまうことがある

<img width="1167" height="649" alt="image" src="https://github.com/user-attachments/assets/a748fc6a-52a2-4223-ab8c-1e09743ae8e7" />

 - 『大陸の覇者』由来クエストの会話で「Yes / No」選択が出た場合、OT0 とは逆に、デフォルトで上側の選択肢が「No」になってしまい、どちらを選んでもSEが不自然になる。さらに、キャンセル時のカーソル位置も誤った位置に戻る
 - 推奨レベルが、ボスの強さに対して概ね20〜30レベル高すぎる
 - 『大陸の覇者』由来のボスは、概ねEXPが多すぎる（例：神官クラトスはHP約10万の弱めのボスだが、EXP 12,000を得られた。OT0のオル・サザントスの約3倍）
 - 移植したクエストNPCは、デフォルトで不自然なFCデータ（Field Command関連データ）を持っていることが多い。例：

<img width="1239" height="667" alt="image" src="https://github.com/user-attachments/assets/c56a5b05-862f-45f2-a40a-cabe477d5fe6" />

 - 『大陸の覇者』では、不可視NPCを「クエストノード」として利用することが多く、近づくとパスアクション／FCデータが表示されてしまう。例：

<img width="1272" height="714" alt="image" src="https://github.com/user-attachments/assets/b5296bd4-fd61-4d75-a98a-1e05e391b349" />

 - シーンが「会話を開始したパーティメンバー」を基準に進行してしまい、必ずしもゼロ／指輪の担い手（Ringbearer）にならない
 - クエストタスクのいくつかで目的文が欠けている、または目的文の代わりにタスクタイトルが表示され、OT0では不自然に見える
 - 『大陸の覇者』の報酬タイプの多くは、OT0では挙動が異なる／または機能しない。詳細は [How to add custom quests and events](customqsts_en.md) を参照

### Original Article (CORRECTED)
<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/226d0592-5823-490b-a37a-5684fdc9176b" />

_この雑然とした画像からも分かる通り、『大陸の覇者』からクエストを移植すること自体は確実に可能です。問題はイベントの移植にあります。イベントは、カットシーン、ボス戦の開始、戦闘中の会話、ダンジョン内のスイッチなど、さらに多くの要素を担っています。イベントがなければ、クエストは「クエストマーカー」と「報酬」だけの存在に過ぎません。_

_`/Game/Content/Local/DataBase/Event/` 配下の `EventList` アセットを見ると、ゲームがイベントIDをロードした時点で `m_Name` と `m_Version` が `/Game/Content/Local/DataBase/Event/V[m_Version]/[m_Name].[m_Name]` に解決されるように思えますが、~~残念ながらそうではありません~~：~~テストでは、ゲームはこれらの項目を参照しておらず、イベントIDをイベントアセットへのパスに内部的に紐付けているように見えました~~_ **（これらのキーは該当パスに解決されますが、既存イベントに対して変更しても無視されるようです）**。

_さらに、同じイベントでも、どのイベントアセットに新しい package properties（パッケージプロパティ）をパッチするか、そして特にそれがどのフォルダに置かれているかによって、ゲーム内での再生内容が変わってしまいます。_  
_たとえば VPartychat 配下のイベントは自動的に Wishvale の酒場が背景になり、VTest では再生できるイベントが非常に制限される等、実質的に `EventList` の `m_Kind` キーを無視している挙動になります。_  **（これは現在も同様のようです）**

_~~このことから、EventList に新規エントリを追加するだけではカスタムイベントをゲームに追加できず、エンジンが（あるいはこれから）完全な Unreal オブジェクトパスを使用する地点でパイプラインをフック（介入）する必要があると考えられます~~。_

_私は ~~現在~~ PolyHook2 を用いてそれを実現する UE4SS 向け dll MOD を開発していました。テンプレートとしては [このmod](https://github.com/UE4SS-RE/RE-UE4SS/tree/main/cppmods/KismetDebuggerMod) を使用しています。  
また、Dumper-7 で生成した OT0 の IDA mappings（BravelyPath Modular Discord の #resources にある SDK を参照）を使い、IDA で広範に解析していました。実行時にイベントIDを上書きするところまで到達しています。_ **（中止：dll MOD の詳細は過去コミットを参照してください）**
