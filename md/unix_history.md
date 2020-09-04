# ロボットシステム学第4回

上田 隆一

千葉工業大学

---

## 今日の内容

* 準備体操
* OSの役割、仕組みを歴史から知る
* その他歴史
* 練習

---

## 準備体操

* 前回の講義の内容を思い出してVimで要点をまとめてみましょう。
    * 箇条書きでも文章でもOKです。<br />　
* GitHubにリポジトリを用意して要点を書いたファイルをpushしてみましょう。

---

## オペレーティングシステム

* ハードウェアの複雑さを隠すためのソフトウェア階層
    * ハードウェアをいじる人間には邪魔かもしれない
    * でも使われる<br />$ $
* 仕事
  * 抽象化: 機械の世界から情報処理の世界へ
  * 資源（リソース）管理
      * 次々（同時）に来る「計算機（とその周辺機器）を使いたいという要求」をいかに捌くか


---

## マイコン/OSでの抽象化

* マイコン: メモリアドレスによる抽象化
  * 様々な機器のデータ読み書き口を同じアドレス空間に配置<br />（メモリマップドI/O）
    * 例: ある番地の01を操作すると、ピンの入出力が入れ替わる
    * メモリの番地を一つずつ書き換える操作<br />$ $
* OS: ファイルによる抽象化
  * 一連のデータを流し込んだり取り出したりできる<br />（ストリーム）
  * マイコン的なものはOSの本体が受け持ち、ユーザがなるべく触れなくて良いようになっている。

---

## なんでもファイル

* 機器の操作（例: ラズパイマウスを例に）
  * https://www.youtube.com/watch?v=_SDwb_MJ4SA
  * `/dev/rtmotor0`等、ファイルの読み書きで操作<br />$ $
* よくよく考えてみると、機器の操作というのはデータを書いてデータ読むだけで済む<br />$ $
* もちろんマイコンより余計なオーバーヘッドが発生


---

## こんなファイルも<br />（擬似デバイス）

* 乱数発生器 `/dev/random, /dev/urandom`
* ゴミ捨て場 `/dev/null`
* ひたすら0x00を吐く `/dev/zero`


---

## プロセス

* OSなしのマイコン制御: 一度に一つのプログラムだけ実行
* OSがある: 複数実行可能
    * 恩恵もあるが複雑になる
    * ここでは簡単な説明に止めて、歴史から仕組みを考える
        * なぜOSというものがあるのか
        * なぜこんな仕組みなのか？
        * ロボットとの親和性は？<br />$ $
* 話の流れ
  * UNIX 以前$\rightarrow$UNIX$\rightarrow$UNIXの派生物$\rightarrow$Linux


---

## UNIX以前

* 初期の計算機の使われ方（-1965 年）
  * 主に科学計算や集計
  * 使用者が計算機の使用時間を割り当てられて、その時間にプログラムを計算機室に持ち込み、プラグボードやパンチカードを持ち込む<br />$ $
* 参考
  * [プラグボード](https://en.wikipedia.org/wiki/File:IBM402plugboard.Shrigley.wireside.jpg)（CC BY 2.5）
  * [プラグボードの使用風景](https://en.wikipedia.org/wiki/File:UNIVAC-120_BRL61-0890.jpg)（パブリックドメイン）
  * [パンチカード](https://en.wikipedia.org/wiki/File:FortranCardPROJ039.agr.jpg)（CC BY-SA 2.5）


---

## このころの計算機の使い方

* 初期の頃の計算機のオペレーション（バッチシステム）
    * プログラマがオペレータにプログラムを渡す
        * 古くはプラグボード、その後パンチカードや紙テープで
    * オペレータはもらった順に手でプログラムを計算機にセット
    * オペレータは計算結果をプログラマに渡す

要は流れ作業。<br ><span style="font-size:70%">当然ながらロボットには組み込めない。</span>

---

## バッチシステムを改善したい


* 1台の計算機で1つの計算しかしない
   * 割り込みのない（できない）マイコンのようなもの
   * 割り込んだらオペレータが怒る
* 人が列をなして待っていないと計算機が止まる
   * 高価なマシンを遊ばせておきたくない

どうすればよいか

---

## マルチプログラミング

* 1964年ごろ実用
  * [このころの計算機（IBM System/360）](https://en.wikipedia.org/wiki/File:DM_IBM_S360.jpg)（CC BY 2.5）<br />$ $
* どんなものか
  * メモリをパーティション分けして複数のジョブを置き、実行
      * <span style="color:red">今はOSがやっている</span>
  * メモリをジョブがお互いに覗かないようにする（ハードで）
      * <span style="color:red">これも現在のOSの仕事</span><br />$ $
* 課題
  * メモリの量が動的に変わらない


---

## タイムシェアリングへ

* 1960年代<br />$ $
* マルチプログラミングをさらに発展
  * プログラムを持ち込むバッチ処理から端末による対話式へ
      * プロセス中心に話をしているが、計算機内でのデータの置き方も重要に
  * <span style="color:red">オペレータの仕事の一部がOSの機能に置き換わった</span><br />$ $
* 主なプロジェクト
  * CTSS（compatible time sharing system）
  * MULTICS（multipexed information and computing service）<br />$ $
* [この頃の計算機（PDP-1）](https://en.wikipedia.org/wiki/File:PDP-1.jpg)（CC BY 2.0）


---

## <span style="text-transform:none">UNIX（Unix）

* MULTICS プロジェクトに参加していたベル研のKen Thompsonらが[PDP-7](https://commons.wikimedia.org/wiki/File:Pdp7-oslo-2005.jpeg)（CC SA 1.0）上で開発
    * 1960 年代末<br />$ $
* 特許ドキュメント管理・作成用
  * （単にゲームを動かしたかっただけという説も）<br />$ $
* MULTI（多方向）→UNI（単方向）
  * MULTICSの開発: たくさんの研究組織、研究員
  * UNIXの開発（注意: 私の独自解釈です）
    * ドキュメント管理したい（＋ゲームやりたい）という明確な目標
    * 3人+αで早く作りたい→シンプルに作りたい


---

## UNIXが具現化/受け継いだ機能


* 階層型ファイルシステム
  * ファイル: データ（バイナリ）の塊に名前をつけて管理
  * ディレクトリ: ファイルを整理するファイル
  * デバイスファイル: 機器もファイル<br />$ $
* プロセス・タイムシェアリング
  * 複数の処理を同時に走らせる
  * 仮想記憶, プロセススケジューラ<br />$ $
* シェル
  * ファイルの操作とプロセスの連携

<span style="font-size:50%">注意: 最初から全てが実装されていったわけではないです。</span>


---

## ここまでで重要なこと

* OSのプロセスの処理は、最初人間がやっていた
    * 1個の処理を人が順番に投入$\rightarrow$いつでもコマンド実行
    * 「いつでもコマンド実行」のためには・・・
        * 交通整理が必要
        * データを計算機内に置いておくことが必要<br />　
        $\Longrightarrow$OSの仕事<br />　
* <span style="color:red">Unix以後、OSの基本的な構造はそんなに変わっていない</span>
    * 次のページ以降も歴史の話ですが、OSの仕組みからは離れます

---

## OSの機能以外に重要なこと

* オープンソースの走り
  * 当時AT&T はコンピュータで商売できない（独禁法）
  * そこでコードを配布
  * 結果、企業、研究機関、教育機関に広まる<br />$ $
* 1984年AT&T商売解禁
  * ライセンス業を始め、クローズ化
  * 「[UNIX 戦争](http://www.unix.org/what_is_unix/history_timeline.html)」が始まる
    * http://juangotoh.hatenablog.com/entry/2017/09/19/201202 <br />$ $
* 余波: 配布されたUNIX から様々な亜種が誕生
  * UNIX系OSと呼ばれるもの
  * ソースコードの流用
  * 機能の再現


---

## [UNIX系OSの系譜](https://en.wikipedia.org/wiki/File:Unix_history-simple.svg)<span style="font-size:50%">（CC BY-SA 3.0）</span>

* UNIX直系: 大雑把に言ってSystem V系とBSD系
  * ものによって使用感が異なる<br />$ $
* 我々がよく使うもの
  * 組み込みの世界だとBSD系が多い
    * FreeBSD, NetBSD, OpenBSD, OS X, iOS…<br />$ $
* MINIX, Linux: UNIXのコードを含まないが動作はUNIX
  * MINIX（1987年〜）: UNIX がクローズ化したためタネンバウムによって教育用に開発
  * Linux: 次のページ


---

## <span style="text-transform:none">Linux

* リーナス・トーバルズがMINIX 上で開発（1991年）
  * 当時ヘルシンキ大学の大学生
  * 冬季に部屋に引きこもって開発
  * メーリングリストで助言、協力を得ながらゼロから開発<br />$ $
* **バザール方式**
  * 「[伽藍とバザール](http://cruel.org/freeware/cathedral.html#1)」<br />$ $
* 広まった理由（一部私見）
  * ゼロから開発したことで制約が少ない
  * 協力者が多い
    * 上からしゃしゃり出てくる人（老害）がいないと成功する
  * タイミング（PCの普及, Microsoftの影響, ライセンスの問題）


---

## <span style="text-transform:none">Linuxと<br />ディストリビューション

* Linuxの構成
  * Linuxカーネル（OS本体）
  * 付属のソフトウェア（コマンド、パッケージシステム、GUI等）<br />$ $ 
* 付属のソフトウェアをどういう構成にするかで多数の「ディストリビューション」が派生
  * [系統図](https://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB:Linux_Distribution_Timeline.svg)（GFDL 1.3）


---

## 主なディストリビューション

* 会話のためにおさえておきましょう<br />$ $ 
* 三大系統
  * Red Hat系
    * RedHat linux から派生しているもの。ビジネス用途でシェア
  * slackware系
    * 最古のディストリビューションslackware から派生<br />$ $ 
  * debian系
    * Debian GNU/Linux から派生しているもの
    * UbuntuもRaspbianもこの系統


---

## 後半のまとめ

* ソフトウェアには隠して売るという考えのほかに、公開するという考えが存在
    * GitHubの使い方そのもの<br />　
* ソフトウェアの亜種の発生は技術そのものよりも社会的な理由から
    * 著作権やライセンスに詳しくないと、このへんは理解不可能<br />　
* 我々はリーナスさん+世界中の無数の開発者が作ったものをUbuntuを通して使用

<span style="color:red">何を使って世に何をするのか、社会的な視点を</span>