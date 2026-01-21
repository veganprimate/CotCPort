## **LANGUAGE:**  [English](Animations_Prog_en.md) | [日本語](Animations_Prog_jp.md) | [中文](Animations_Prog_zh.md) | [한국어](Animations_Prog_kr.md)

大陸覇者の **cooked UE4.27** アセットから **UE5.4** のエディタプロジェクトへ VFX を移植するのが難しいのは、構造的に主に2つの理由があります。

### (1) 未クックの `.uasset` グラフが存在しない
メッシュ／テクスチャであれば **GLB/PNG + JSON** から再構築できます（FModel にはメッシュを GLB として書き出す機能があり、これは幸いです）。しかしエフェクトはそうはいきません。というのも、エフェクトのアセットは _それ自体がグラフ_ だからです。

- **Cascade** の `ParticleSystem` は、モジュールのスタック（Spawn, Lifetime, Velocity, Orbit, Drag, Color over Life, SubUV, Mesh TypeData…）を内包しています。
- UE5 では標準（canonical）のシステムは **Niagara** であり、Cascade のモジュール群を 1:1 で直列化した表現にはなっていません。
- FModel などから得られる cooked ダンプには **モジュールのプロパティ値** は含まれますが、UE5 が読み込める「再インポート可能なグラフ」にはなっていません。

したがって VFX の再構築は、アセットのインポートではなく、必然的に「意味（semantic）の変換」になります。

### (2) マテリアルはVFXの挙動の一部である
大陸覇者のエフェクトは、以下に強く依存しています：
- Dynamic Parameters / カーブによるマテリアルパラメータ駆動
- Masked と Translucent の挙動差、ディザ、ノイズスクロール、屈折／歪み（refraction/distortion）など

大陸覇者の cooked マテリアル `.uasset`（unversioned cooked packages）は読み込めません。そのため、たとえ Niagara 側の Spawn 挙動を完璧に再現できたとしても、同じパラメータを受け取れる UE5 マテリアルも用意しない限り、見た目は正しくなりません。

---

それでも、この分野についてはある程度の進捗があり、また VFX にも適用されるであろう中核パターンは次の通りだと考えています：

- **寛容な JSON ローダ**（FModel のダンプは JSON の連結や「Extra data」を含むことが多く、これが重要）
- **生ソースのインポート**（PNG → Texture2D、GLB → StaticMesh）を、`/Game/...` 構造をミラーした形で取り込む
- **プレースホルダのマスターマテリアル** + MIC を生成し、ヒューリスティクスでパラメータを修正する
- `StaticMeshEditorSubsystem` を使わずに **メッシュへマテリアルを割り当てる**
- Paper2D のスプライト／フリップブックを構築（これは既に実施済み）

つまり、ソース形式とメタデータからアセットを再構築する必要があります（バイナリ変換を試みてはいけません）。

## 有望だが、現時点では未検証の戦略

複数ステージに分けて扱います：

### Stage 0（仕様抽出）
入力：Cascade JSON  
出力：正規化フィールドを持つ `vfx_specs/*.json`
- エミッタ一覧
- エミッタごと：レンダラ種別（sprite/mesh/ribbon）、spawn rate/burst、lifetime、初期位置/速度、drag など
- dynamic parameter カーブ（サンプル配列、名前、対象チャンネル）
- 依存関係：テクスチャ名／マテリアル名／メッシュ名
目的：仕様を安定化し、UE 内部のプロパティ命名の癖に依存しない形にすること。

### Stage 1（リソースのインポート）
- VFX 向け設定でテクスチャをインポート
- メッシュをインポート
- （任意）Niagara 用マテリアルをソースとして確保できるならインポート、もしくは再現用のマスターを作成

### Stage 1.5（VFX再現用マテリアルセットの構築）
以前、より単純なエフェクトに対してこれを行い、EnmAdd 配下の敵スプライト用アセットを作成しました：
- UE5 のマスターマテリアルを少数用意（例）：
  - `M_VFX_Sprite_Additive`
  - `M_VFX_Sprite_Translucent`
  - `M_VFX_Mesh_Additive/Translucent`
  - （任意）大陸覇者が使用しているなら歪み／屈折用マスター（未確認）
- 大陸覇者のエフェクトが期待するパラメータを受け取れるようにする：
  - `NoiseTex1/2`、スクロール速度、intensity/power
  - `DynamicParam0-3` 相当（または名前付きスカラーパラメータ）
  - `MaskColor`、clip、dither alpha など
- そのうえで、VFX マテリアル JSON（もし入手できているなら）から MIC を生成するか、ヒューリスティクス（テクスチャ命名 + デフォルト値）で MIC を生成する。

これにより、「スポーンはするが見た目が合わない」エフェクトに対して、Niagara 側の作業が無駄になるのを防げます。

### Stage 2（テンプレートエミッタライブラリ）
例として `/Game/_VfxTemplates` のような場所に固定セットを保持します。内容：
- 標準的な spawn/update と sprite renderer のバインディングを備えた Sprite エミッタ
- Mesh renderer を user param あるいはデフォルトの mesh エントリへバインドした Mesh エミッタ
- （任意）大陸覇者が使っているなら ribbon/beam テンプレート
空のエミッタを作ってスタックを組み立てる方式には依存しません。

### Stage 3（Niagaraアセット生成）
各 spec について：
1) エミッタテンプレートを複製し、例えば `/Game/Effect/Generated/...` に配置
2) dynamic カーブ用の `CurveFloat` アセットを作成
3) 以下をパッチ：
   - mesh renderer の static mesh（例：`FxMD_Btl_Cylinder_A`）
   - renderer の material override / MIC 割り当て
   - spawn rate、lifetime、size、color（ただしテンプレート側で user parameter として露出している場合に限る）
4) エミッタを組み合わせて Niagara System を構築

### Stage 4（クックとパッケージング）
クックしてゲームに入れて比較するより、可能であれば Unreal 上で先に再生し、大陸覇者のアセットと見比べる方が時間効率は良いかもしれません。Effect 関連の DataBase アセットは既にマージされているため、アセット名を変更していなければ、エフェクトはゲーム内に表示されるはずです。
