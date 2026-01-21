## **LANGUAGE:**  [English](customchars_en.md) | [日本語](customchars_jp.md) | [中文](customchars_zh.md) | [한국어](customchars_kr.md)
**注意:** 本ガイドでは、新規キャラクター用のスプライトシート（spritesheets）の作成方法は扱いません。詳細については BravelyPath Modular Discord を参照し、必要であれば #modding-help で質問してください。

1. `/Game/Content/Local/DataBase/Character` 配下の `CharaID` を確認してください。このファイルには主に NPC のテクスチャパス等が格納されています。追加したい NPC にここでのエントリが存在しない場合は、エントリを追加する必要があります。また当然ながら、このファイルを使って特定の NPC のスプライトシートのテクスチャパスを調べることもできますし、該当ファイルを直接拡張するだけで動くことも多いです。
2. `CharaPlayer` に新規エントリを追加し、`m_CharaID` で `CharaID.m_id` を参照させてください。名前は `GameTextCharacter` に追加する必要があります。以下に示すパラメータ（おそらく）は次の通りです：
```
 "m_Param": [
        0, (なし)
        250, (最大HP)
        60, (最大SP)
        0, (最大BP)
        0, (なし)
        0, (HP)
        0, (SP)
        0, (BP)
        0, (なし)
        72, (物攻)
        88, (物防)
        88, (属攻)
        96, (属防)
        64, (速度)
        72, (会心)
        80, (命中)
        80, (回避)
        1, ("なし"、おそらくシールド削り倍率)
        0, (なし)
        0, (なし)
        0 (なし)
      ]
```
ここでの「なし」は `GameTextUI` 上でこれらのステータスがその名称で表示されている、というだけで、用途がないことを意味するものではありません。

3. `m_MaxParam` にも同様の値を指定します。ゲームはこの2つの配列に基づいて、各レベルごとの値を補間して決定します。`m_MaxParam` がレベル99時点のステータスを含むかどうかは不明です。というのも、大陸の覇者（CotC）の多くのユニットでは HP の max param が 9999 を大きく超えている一方で、テストではそこまで到達しないためです。
4. 新規ユニットの `m_InitialOpenType` が 2 になっていることを確認してください（習得したスキルを装備できるようになります）。また、XP を獲得できるように `m_InfluenceValue` を 10 に設定してください。
5. `PlayerMemberSet` に新規エントリを作成し、追加した `PlayerID`（`CharaPlayer.m_id`）を `m_PlayerID` として参照させてください。この新規エントリでは `m_ValidCondition` を `991202` に設定する必要もあります。さらに、`m_PlayerIDs` 配列の先頭要素が 0 であることを必ず確認してください。理論上、先頭要素が 0 以外の場合は Zero／指輪に選ばれし者を置き換えるはずですが、イベント加入コマンド 10029 では置き換えは起きず、ソフトロックするだけになります（ただし、その `PlayerMemberSetID` を敵グループに割り当てた場合は動作します）。
6. `SkillBoardData`（`/Game/Content/Local/DataBase/SkillBoard` 配下）に新規エントリを作成し、その新しい `PlayerID` を参照させてください。`m_CanNotEssential` はスキルが習得可能かどうかを決めますが、`ItemList`（`/Game/Content/Local/DataBase/Item` 配下）にそのスキルをアイテムとしても追加しない限り、アイテムは入手できません。`m_SupportLockCond1` は必ず `16` のみを使用し、`m_SupportLockCond2` は `0` のみを使用してください。大陸の覇者（CotC）では別のロック条件が使われることもありますが、OT0 では動作しないように見えますし、`16` 以外のロック条件にはローカライズも存在しません。`m_SupportLockParam1` は、指定されたサポートスキルを解放するために習得が必要なバトルスキル数を決定します。
7. （任意）`SkillID` と `SkillAvailID` を変更してカスタムスキルや avail を追加します。詳細は [カスタムスキルと avail を追加する方法](customskls_jp.md) を参照してください。
8. カスタム必殺技（special skills）の追加：`SpecialSkillID`（`/Game/Content/Local/DataBase/Skill/` 配下）に新規エントリを追加し、`CharaPlayerSpecialSkillList`（`/Game/Content/Local/DataBase/Character/` 配下）にエントリを追加して、そのユニットを新しい special skill ID に紐付けます。さらに、必殺技の各レベルについて `SpecialSkillGrowthList` に新規エントリを追加し、その special skill ID をスキルに紐付けます。大陸の覇者（CotC）のユニットでは最大レベルが 3 を大きく超える special skill を持つことが多いようですが、ゲーム側にそれらをロードさせられるかは不明です。また、異なるレベルに同一のスキルを指定した場合に、avail／スキルのより高いブーストレベル版がロードされるかどうかも不明です。
9. `RecruitEvent_p.pak` を展開（unpak）してください（[このMODおよび／またはOT0からアセットを抽出する方法](Begin_jp.md) も参照）。
10. `/Game/Content/Local/DataBase/Event/VPartychat/EvPChatOth_01` を編集し、`m_OptAry[0]` でその新しい `PlayerMemberSetID` を参照するコマンド 10029 のエントリを追加してください。`m_OptAry[5]` の 1 は不要に見えます。あわせて [カスタムクエストとイベントを追加する方法](customqsts_jp.md) も参照してください。
