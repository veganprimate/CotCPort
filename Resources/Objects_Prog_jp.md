## **LANGUAGE:**  [English](Objects_Prog_en.md) | [日本語](Objects_Prog_jp.md) | [中文](Objects_Prog_zh.md) | [한국어](Objects_Prog_kr.md)

`/Object` フォルダには主に、ワールド／ゲームプレイ用の小物（プロップ）と、それらを表示するためのアセットが入っています。典型的には次のようなものです：
- **Static Mesh**（プロップ、環境パーツ、インタラクト可能オブジェクト）
- それらのメッシュが使用する **Texture**（アルベド／カラー、法線、マスク等）
- それらのテクスチャを適用するための **Material**／**Material Instance**
- （場合によっては）オブジェクトのリソースフォルダ内に格納されている Paper2D の **sprites/flipbooks**（例：大陸覇者における `Obj_Dice`）

[エフェクトとアニメーション](Animations_Prog_jp.md) と同様に、大陸覇者のアセットファイルを UE5 に直接インポートすることはできないため、JSON・GLB・PNG ファイルのみを使ってこれらのアセットを再構築していきます。
また、同様にマルチステージの手順を構築します（こちらも未検証ではあるものの、`Effect` に対するものよりは有望に見えます）：

### Stage 0（マニフェストを作る）
cooked のダンプはかなり混沌としているため、マニフェストがあると決定的（deterministic）な対応表になります：
- Object → メッシュ JSON パス
- Object → 見つかったテクスチャ
- Object → マテリアル設定
- Object → スプライトフレームの JSON パス
また、FModel のエクスポート JSON には JSON オブジェクトの連結や「extra data」によるパース失敗がしばしば含まれるため、再び寛容な JSON ローダも用意したいところです。

### Stage 1（生リソースのインポート：PNG → `Texture2D`、GLB → StaticMesh）
PNG は UE5 に `Texture2D` として、GLB は `StaticMesh` としてインポートします。また用途に応じて、インポート設定も切り替えます：
- Sprites / Paper2D
  - UI2D compression
  - no mips
  - nearest filter
  - clamp
  - TEXTUREGROUP_Pixels2D
- Objects / World / VFX
  - default compression
  - mips enabled
  - 適切な texture group（World、Effects など）
これは重要です。オブジェクト用テクスチャは 3D で正しく見える必要があり、スプライト用テクスチャはボケてはいけないからです。

なお、FModel は GLB を `_LOD0`、`_LOD1` といったサフィックス付きで書き出すことがありますが、JSON 側は `ObjMD_X` のみを期待していることが多く、`ObjMD_X_LOD0` のような明示的参照は行われない場合があります。

### Stage 2（マテリアル）
[エフェクトとアニメーション](Animations_Prog_jp.md) で説明した通り、Unreal のマテリアルはノードグラフです。cooked ダンプからは以下が得られません：
- 完全な expression グラフ
- カスタム HLSL
- 正しい function グラフ
- エディタ専用情報
`CachedExpressionData` が得られる場合でも、それがきれいにグラフとして再構成できることは稀です。そのため、再び自前で少数のマスターマテリアルを用意する必要があります。例：
- `M_Obj_Master_Opaque`
- `M_Obj_Master_Masked`
- （安全な Paper2D スプライト用マスター）
そして各オブジェクトについて `MaterialInstanceConstant` を作成し、以下のパラメータを設定します：
- BaseColor テクスチャ（サフィックス `_cl`）
- Normal テクスチャ（`_no`）
- Masks（`_mk`）など

### Stage 2b（パラメータ修復）
JSON のメタデータがあっても、パラメータ名が想定と一致せず、テクスチャが `DefaultTexture` になってしまうことがあります。
そこでこのステージでは理想的にはヒューリスティクスを適用します：
- MIC のパラメータが `DefaultTexture` を指している場合、同一フォルダ内の兄弟ファイルから `*_cl`、`*_no` を探索する
- それらのテクスチャを自動的に割り当てる
これにより、数千アセットに対する手作業での後処理を、（願わくば）管理可能な規模まで減らせます。

### Stage 3（メッシュにマテリアルを割り当てる）
Static mesh はマテリアルスロットのリスト `StaticMaterials` を保持しています。理想的にはこれにエディタサブシステムを使います。もしそれが不可能な場合（例：commandlet でアセットを一括生成している場合）は、
- メッシュをロードする
- 新しい `static_materials` リストを構築する
- `mesh.set_editor_property("static_materials", ...)`
- メッシュを保存する
という手順になります。

これにより、メッシュが実際にレベル内で描画されるようになります。

### Stage 4（Paper2D の sprites と flipbooks）
実質的には次の作業です：
- 焼き込み済みのソース寸法／UV を読み取る
- 焼き込み済みのレンダーデータを復元する（トリミング／アラインメントに重要）
- flipbook と keyframe を生成する
これらのスプライトは `/Game/Character/...`（`ChrTX_*`）配下のテクスチャを参照していることがあるため、理想的にはそれらの uncooked アセットを先に作成しておくべきです。そうしないと `DefaultTexture`（ゲーム内では空白表示）になってしまいます。
