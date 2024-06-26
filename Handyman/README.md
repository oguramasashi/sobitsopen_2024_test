## **Handyman**

RoboCup Simulation Leagueで行われた競技の1つであるHandymanタスクについて説明していきます．

## **目次**

1. [競技内容(Handyman)](#1-競技内容handyman)
2. [競技手順(Handyman)](#2-競技手順handyman)
3. [競技の点数表(Handyman)](#3-競技の点数表handyman)
4. [競技関連資料(Handyman)](#4-競技関連資料handyman)



### 1. 競技内容(Handyman)

Handymanタスクでは，与えられた命令文を解析し、ロボットが自律的に移動し，注文された物を掴み，別のところまで運ぶことを行います．
今回は競技を簡単にするために，家具や把持物体などの位置を含めたファイルを共有します．
環境や把持物体はランダムに決定されます．

### 2. 競技手順(Handyman)

競技の手順は以下の通りになります．

1. モデレータの指示を聞く
2. 指定された部屋へ移動する
3. 指定された物体を探索する
4. その物体を把持する
5. 指示された置き位置へ物体を運ぶ
6. 置き位置に物体を置く

競技開始後，セッション中に動作しなくなった場合はそのセッションをスキップし、次セッションから再起動できます．[物体リスト](https://github.com/TeamSOBITS/robocup_sobits_open/blob/rcso_2023_srl/Handyman/object_list/graspable_object_list.txt)や[置き位置リスト](https://github.com/TeamSOBITS/robocup_sobits_open/blob/rcso_2023_srl/Handyman/object_list/destination_list.txt)や[家具などすべてのオブジェクトリスト](https://github.com/TeamSOBITS/robocup_sobits_open/blob/rcso_2023_srl/Handyman/object_list/other_object_list.txt)は，このパッケージに乗っております．
制限時間は各セッション600秒です．

#### 2.1. 競技手順の流れ

具体的な競技手順の流れは以下の通りになります．

1. モデレータは「Are_you_ready?」をロボットへ送信します。そして同時に「Environment」メッセージも発信します．
2. ロボットは「I_am_ready」メッセージをモデレータに送信します．
3. モデレータはロボットに「指示」のメッセージを送ります．(例: Go to the XXXX, grasp the YYYY and bring it here.)
4. 命令理解が完了した後に，ロボットは「Message_understood」メッセージをモデレータに送信します．
5. ロボットは指定された部屋に移動します．
6. ロボットは「Room_reached」メッセージをモデレータに送信します．
7. ロボットが物体を探します．
    - 物体が見つかった場合に，ロボットは「Object_recognized」メッセージをモデレータに送信します．
    - 物体が見つからなかった場合，ロボットはモデレータに「Does_not_exist」メッセージを送信する必要があります．
        - 指示が正しい場合，スコアが追加され，モデレータはロボットに，「Corrected_instruction」メッセージを送信します．その後，ロボットは新しいオブジェクトを探す必要があります．
        - 指示が正しくない場合，タスクは終了します．
8. ロボットは物体を把持します．
9. ロボットは「Object_grasped」メッセージをモデレータに送信します.
10. ロボットは置き位置に物体を置きます．
11. ロボットは「Task_finished」メッセージをモデレータに送信します.
- タスクが終了した場合（成功または失敗）: モデレータは「Task_succeeded」 (タスク成功) または「Task_failed」 (タスク失敗) メッセージをロボットに送信します．すべてのタスクが終了した際に，モデレータはロボットに「Mission_complete」メッセージを送信します．
- 制限時間が終了した場合: モデレータは，タスクが失敗したことを示す「Task_failed」メッセージをロボットに送信します．
- ロボットは、タスクを達成できない場合に「Give_up」メッセージを送信できます．その場合，タスクは中止され「Task_failed」メッセージが送信され，次のセッションに進みます．
### 3. 競技の点数表(Handyman)

<table>
    <tr>
        <th>タスク内容</th>
        <th>基準点数</th>
        <th>挑戦点数</th>
    </tr>
    <tr>
        <td>map1つで実施（挑戦：mapが2つ）</td>
        <td>10点</td>
        <td>20点</td>
    </tr>
    <tr>
        <td>命令文章を解析する（挑戦：多様な表現）</td>
        <td>10点</td>
        <td>20点</td>
    </tr>
    <tr>
        <td>指定された部屋へ移動する</td>
        <td>10点</td>
        <td>10点</td>
    </tr>
    <tr>
        <td>指定された物体を探索する</td>
        <td>20点</td>
        <td>40点</td>
    </tr>
    <tr>
        <td>指定された物体を把持する</td>
        <td>30点</td>
        <td>30点</td>
    </tr>
    <tr>
        <td>指定された場所を指定された場所に運ぶ</td>
        <td>10点</td>
        <td>10点</td>
    </tr>
    <tr>
        <td>指定された場所に物体を置く</td>
        <td>20点</td>
        <td>40点</td>
    </tr>
    <tr>
        <td>合計</td>
        <td>110点</td>
        <td>150点</td>
    </tr>
</table>

本競技は，1人3セッション挑戦することができ，3セッションの総合得点で順位を決めます．  
得点圏に至らない人が多かった場合は審査員側で実行過程に評価点を入れます．  
実機と違い、課題のスキップ等はできません．
得点獲得までに踏まなければならないステップが非常に多いため、teleop_key.launchで実行してみるなどしてイメージを掴んでみてください.

#### 3.1. タスクの詳細

**命令文章の解析**
- 命令理解が完了したあとに，ロボットは「Message_understood」メッセージをアバターに送信します．このとき，detailには「把持物体名 + # + 目的地」を入れてください．
    - 例えば把持物体名が”filled_plastic_bottle”で目的地が”wooden_bed”の場合は「”filled_plastic_bottle#wooden_bed”」となります．
    - 目的地が"me"の場合，detailには"Moderator"を入れてください．
- アバターは，ロボットが理解した把持物体と目的地が正しいかどうかをチェックします．成功するとスコアが加算されます．（10点）(挑戦課題用の文章に成功すると20点)
- 失敗した場合、タスクは終了し、セッションは次のセッションに進みます．

**指定物体の探索**
- 物体を認識できた場合，ロボットは「Object_recognized」メッセージをアバターに送信します．このとき，detailには「把持物体名」を入れてください．
    - 例えば把持物体名が”filled_plastic_bottle”の場合は「”filled_plastic_bottle”」となります．
- アバターは認識した物体が正しいかどうかをチェックします．成功するとスコアが加算されます．（20点）
- 失敗した場合、タスクは終了し、セッションは次のセッションに進みます．



### 4. 競技関連資料(Handyman)
#### 4.1. サーバー
競技に関連する資料はすべてサーバに置いています．下記にPathを記述したので，必要に応じて確認してください．
- ubuntu： smb://192.168.11.72/competitions/RCSO/2023_summeer/
- windows： \\192.168.11.72/competitions/RCSO/2023_summeer/

#### 4.1. esa
今回の競技で必要になるサイトを置いています．必要に応じて確認してください．
#### 4.1.1. 競技関係
- [How to use YOLO](https://esa-pages.io/p/sharing/19758/posts/82/148313344c3b93bac860.html)
#### 4.1.2. SIGVerese
- [SIGVerse環境でHSRを動かすためのセットアップ方法](https://esa-pages.io/p/sharing/19758/posts/70/db8eb166b734bfc79ab9.html)
- [Windows用の環境作成](https://esa-pages.io/p/sharing/19758/posts/71/4084cf2aa249a51915fc.html)
- [SIGVerseのレイアウト設定方法](https://esa-pages.io/p/sharing/19758/posts/81/3eb70fdabe437dcbf15d.html)


---

[トップに戻る](#handyman)
