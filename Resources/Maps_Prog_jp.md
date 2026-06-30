## **LANGUAGE:**  [English](Maps_Prog_en.md) | [日本語](Maps_Prog_jp.md) | [中文](Maps_Prog_zh.md) | [한국어](Maps_Prog_kr.md)

[エフェクトとアニメーション](Animations_Prog_jp.md) や [オブジェクト](Objects_Prog_jp.md) と同様に、大陸覇者のレベル（マップ）を『オクトパストラベラー0』へ移植することは、限定的ではあるものの「再構築ベース」という意味では可能です。ただし、メッシュ／テクスチャの移植と同種の問題ではありません。
これはメタデータからシーングラフを組み直す作業に近く、忠実な結果を得るには、
(a) 参照されているアセットが再構築されていること、そして
(b) 一部のシステムはそのまま移行できない／より踏み込んだ解決が必要になる（Level Blueprint のロジック、ベイク済みライティング、ナビゲーション等）ことを受け入れる必要があります。

典型的な Unreal のマップ（`.umap`）には、おおむね次のような階層があります：

- UWorld：最上位のワールドアセット
  - PersistentLevel（ULevel）：Actor リストを保持
    - AActor インスタンス：`StaticMeshActor`、`DecalActor`、`PostProcessVolume`、各種 Light actor、Blueprint actor 等
      - Components：`StaticMeshComponent`、`DecalComponent`、`BrushComponent` 等
  - StreamingLevels（任意）：サブレベル、または UE5 の World Partition / DataLayers

したがって、ここで言う「マップ移植」とは：
1. 参照されている全アセットがターゲットプロジェクト内で利用可能であること、そして
2. Actor の配置＋各コンポーネントのプロパティを再作成すること
を意味します。

ごく限られた観察から得られた点は以下の通りです：
- 大陸覇者のマップには、デカールやボリュームなど多数の配置済み Actor が含まれている（→再構築可能）
- ブラシベースのボリューム（`PostProcessVolume` の brush）は概ね近似的にしか再構築できない：
  - UE4 では、ボリュームが BSP/Brush ジオメトリ（Model + `BrushComponent`）を使うことが多い
  - UE5 でもブラシのサポートはあるが自動化が扱いづらい。また、FModel で抽出した JSON では Brush の model データが不完全である可能性がある
- `BodySetup.ConvexElems.VertexData` はボリュームの凸形状（convex shape）を与えるため、有用かもしれない
- **おそらく最大のブロッカー：**
  - Level Blueprint のロジック（`LevelScriptActor` の BPGC）を移植できない。というのも `LevelScriptActor` クラスは cooked の `BlueprintGeneratedClass` であるため（FModel の「Blueprint を疑似 C++ にデコンパイルする」機能は参照として非常に有用になり得る）
    - UE5 エディタは大陸覇者のダンプからそれをロードできない
    - たとえクラス名を再現しても、ロジック自体が欠落している
  - 大陸覇者は PhysX の cooked コリジョンデータを使用している一方で、OT0 は Chaos 物理を使用している（→cooked PhysX は無視する）
  - アセット依存関係が先に揃っていなければならない：マップは参照の塊であり、プロップや建物の `StaticMesh`、Material（しばしばコンポーネント単位で override される）、Decal 用マテリアル、Texture/Parameter Collections 等が必要（→マップ移植は Object/Character/VFX 等の再構築の後でないと行えない）
  - 大陸覇者はマップ上で自由移動を許可していないため、そもそも多くのコリジョンデータが存在しない可能性がある
  - マップの一部機能はエンジンバージョン依存である（heightmap と layer info は単純な GLB/PNG としてエクスポートできない可能性がある。Lightmass のビルドデータは移植不可能。UE5 は別のパイプラインを使用する。HISM actor。前述の PhysX vs Chaos など）
