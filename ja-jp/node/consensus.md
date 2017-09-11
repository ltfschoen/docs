# コンセンサス

## 1 - 用語一覧

* **Proof of Stake** `PoS` - ネットワークコンセンサスを使用してフォールトトレランスを実現するアルゴリズムの一種。

* **Proof of Work** `PoW` - 演算能力を使用してフォールトトレランスを実現するアルゴリズムの一種。

* **Byzantine Fault（ビザンチン障害）** `BF` - ノードは機能し続けるが、不正な方法で動作する障害。

* **Delegated Byzantine Fault Tolerance** `DBFT` - フォールトトレランスを保証するためのNEOブロックチェーン内で実装されたコンセンサスアルゴリズム。

* **ビュー** `v` - NEOのコンセンサスアクティビティに使用されたデータセット DBFT

## 2 - 役割
**NEOコンセンサスアルゴリズムでは、NEO保有者によってコンセンサスノードが選出され、取引の有効性について投票が行われる。これらのノードは、「帳簿係（ブックキーパー）」とも呼ばれています。今後、コンセンサスノードと呼びます。**

  - <img style="vertical-align: middle" src="/assets/nNode.png" width="25">**コンセンサスノード** - このノードはコンセンサスアクティビティに参加します。コンセンサスアクティビティの間、コンセンサスノードは次の2つの役割を代わる代わる行います：
  - <img style="vertical-align: middle" src="/assets/speakerNode.png" width="25">**スピーカー** `(One)` - **スピーカー**はブロックの提案をシステムに送信します。
  - <img style="vertical-align: middle" src="/assets/cNode.png" width="25">**デリゲート（代表者）** `(Multiple)` - **デリゲート**は、トランザクションのコンセンサス達成に対して責任を持ちます。


## 3 - はじめに

誤った情報の伝達が存在するネットワーク上において、どのようにしてフォールトトレランスを保証するかが、ブロックチェーンごとの大きな違いの1つとしてあげられます。

PoWを実装した従来の方法は、ネットワークの演算能力の大部分が正当である限り、これを保証することができます。しかし、このスキーマは演算に依存するため、非常に非効率的（演算能力は​​エネルギーを消費し、ハードウェアを必要とする）です。これらの依存関係により、PoWネットワークはいくつかの制限をもちます。主な制限としてスケーリングのコストが挙げられます。

NEOは、いくつかの問題を避けながら最小限のリソースを使用してビザンチン障害からネットワークを保護するPoSのような機能（NEO保有者がコンセンサスノードに投票する）を利用するDelegated Byzantine Fault Toleranceコンセンサスアルゴリズムを実装します。このソリューションは、フォールトトレランスに大きな影響を与えることなく、現在のブロックチェーン実装に関連するパフォーマンスとスケーラビリティの問題に対処します。



## 4 - 理論

ビザンチンの一般問題は、分散コンピューティングの古典的な問題です。この問題は、**スピーカー**の発する命令の結果について多数の**デリゲート**が合意しなければならなと定義されます。このシステムでは、**スピーカー**やいくらかの**デリゲート**が反逆的（誤った情報の提供者）になる可能性があるため、注意する必要があります。不誠実なノードは、各受信者に同一のメッセージを送信しないことがあります。これは最もやっかいな状況と考えられています。**スピーカー**は誠実であり、グループとして実際のコマンドは何であるかを**デリゲート**が識別できるかという問題の解決策が必要です。

DBFTの仕組みを説明するために、セクション5で使用されている66.66％のコンセンサスレートの正当性について、このセクションを中心に説明します。不誠実なノードは積極的に悪意がある必要はありません。意図したとおりに機能しないだけです。

議論のために、いくつかのシナリオを説明します。これらの簡単な例では、各ノードが**スピーカー**から受信したメッセージに沿って送信すると仮定します。このメカニックはDBFTでも使用されており、システムにとって非常に重要です。機能システムと機能不全システムの違いについてのみ説明します。詳細な説明については、参考文献を参照してください。


### **誠実なスピーカー**

  <p align="center"><img src="/assets/n3.png" width="300"><br> <b>図1:</b> n = 3における不誠実な<b>デリゲート</b>の例</p>
  
  **図1**では、一つのノードは誠実な**デリゲート**です（50％）。両**デリゲート**は、誠実な**スピーカー**から同じメッセージを受け取りました。しかし、片方の**デリゲート**は不誠実なので、誠実なデリゲートは不誠実なノードがある所は判断することができますが、ブロック生成者（**スピーカー**）と**デリゲート**のどちらが不誠実なのかは特定できません。このため、**デリゲート**は投票を棄権し、ビュー変更しなければなりません。

  <p align="center"><img src="/assets/n4.png" width="400"><br> <b>図2:</b> n = 4における不誠実な<b>デリゲート</b>の例</p>

  **図2**では、2人の誠実な**デリゲート**が存在します（66％）。すべての**デリゲート**は、誠実な**スピーカー**から同じメッセージを受け取り、スピーカーから受け取ったメッセージとともに、検証結果を他の**デリゲート**に送信します。2人の誠実な**デリゲート**の合意に基づいて、**スピーカー**または右の**デリゲート**のどちらかがシステム内で不誠実であると判断することができます。
  
  
  
  
### **不誠実なスピーカー**

  <p align="center"><img src="/assets/g3.png" width="300"><br> <b>図3:</b> n = 3における不誠実な<b>スピーカー</b>の例</p>
  
**図3**のように不誠実な**スピーカー**が存在する場合、**図1**に示したものと同じ結論になります。**デリゲート**はどちらのノードが不誠実であるか判断できません。

  <p align="center"><img src="/assets/g4.png" width="400"><br> <b>図4:</b> n = 4における不誠実な<b>スピーカー</b>の例</p>
  
**図4**の例では、中央ノードと右ノードの両方によって受信されたブロックは有効ではありません。彼らは多数派である66％を送信しているため、新しい**スピーカー**を選ぶという新たなビューは延期されることになります。この例では、不誠実な**スピーカー**が3人の**デリゲート**のうちの2人に誠実なデータを送った場合、意見の変更を必要とせずに有効とされているはずです。



## 5 - 実用的な実装

NEOでのDBFTの実用的な実装には、コンセンサスに達することを保証するためにイテレーション（反復的な）コンセンサスメソッドを使用します。アルゴリズムの性能は、システム内の誠実なノードの割合に依存します。**図5**は、不誠実なノードの割合に応じて予想されるイテレーションを示しています。

**図5**において、**コンセンサスノード**の誠実さは66.66%を下回らないことに注意してください。上記の重要なポイントと33％の**コンセンサスノード**の誠実さとの間には、合意が得られない「ノーマンズランド」があります。**コンセンサスノード**の誠実さが33.33％未満では、不誠実なノード（コンセンサスで調整されていると仮定）は、コンセンサスに達し、システム内の新しい真実のポイントになることができます。


<img src="/assets/consensus.iterations.png" width="800">

**図5:** コンセンサスに達するのに必要なイテレーションを示すDBFTアルゴリズムのモンテカルロシミュレーション {100ノード;誠実なノード選択の乱数を持つ100,000個のシミュレートされたブロック}


### 5.1 - 定義

**アルゴリズム内では、次のように定義します。**

  - `t`: ブロック生成に割り当てられた時間の長さ（単位：秒）
    - 現在: `t = 15 seconds`
      - この値は、コンセンサスアクティビティと通信イベントがこの時定数に対して高速であるため、単一のビューイテレーションの期間をおおよその近似値として使用できます。
      
      
  - `n`: アクティブな**コンセンサスノード**の数。
  
  
  - `f`: システム内における欠陥のあるコンセンサスノードの最小しきい値。
  	- `f = (n - 1) / 3`


  - `h` : コンセンサスアクティビティの現在のブロックの高さ。
  
  
  - `i` : **コンセンサスノード**インデックス。
  
  
  - `v` : **コンセンサスノード**のビュー。ビューには、コンセンサスラウンド中にノードが受け取った情報が集約されたものが含まれています。これには、すべてのデリゲートによって発行された投票（`prepareResponse`または`ChangeView`）が含まれます。
  
  
  - `k` : ビューのインデックス`v`。コンセンサスアクティビティは複数回のラウンドを必要とする可能性があります。コンセンサスの失敗時において、`k`は増加し、新たなコンセンサスのラウンドがはじまります。
  
  
  - `p` : **スピーカー**として選出された**コンセンサスノード**のインデックス。このインデックスのための計算メカニズムは、単一のノードがシステム内の独裁者として動作するのを避けるために**コンセンサスノード**を順に交代させます。
  	- `p = (h - k) mod (n)`


  - `s`: 安全なコンセンサスのしきい値。このしきい値を下回ると、ネットワークに障害が発生します。
  	- `s = ((n - 1) - f)`


### 5.2 - 要件

**NEOにおいては、コンセンサスフォールトトレランスのための3つの主要な要件があります。**

1. ブロックがコミットされる前に、`s`の**デリゲート**がトランザクションについてコンセンサスを達成させる必要がある。

2. 不誠実な**コンセンサスノード**は、不正なトランザクションで、誠実なコンセンサスノードを説得できてはならない。

3. 少なくとも、`s`の**デリゲート**がコンセンサスアクティビティを開始するために同一の状態（`h`、`k`）にいる。



### 5.3 - アルゴリズム
**アルゴリズムは次のように動作します。**

1. **コンセンサスノード**は送信者の署名付きのトランザクションをネットワーク全体に配信します。

    <p align="center"><img src="/assets/consensus1.png" width="450"><br> <b>図6:</b> <b>コンセンサスノード</b>がトランザクションを受信し、システムに配信します。</p>

2. **コンセンサスノード**はトランザクションデータをローカルメモリに記録します。

3. コンセンサスアクティビティの最初のビュー`v`は初期化されます。

4. **スピーカー**が識別されます。

    <p align="center"><img src="/assets/consensus2.png" width="450"><br> <b>図7:</b> <b>スピーカー</b>が識別され、ビューが設定されています。<p/>

   `t` 秒間**待機**

5. **スピーカー**は、提案を配信します：
    <!-- -->
        <prepareRequest, h, k, p, bloc, [block]sigp>

    <p align="center"><img src="/assets/consensus3.png" width="450"><br> <b>図8:</b> <b>スピーカー</b>は<b>デリゲート</b>達による確認のためBlock Proposalを生成する。</p>
   
6. **デリゲート**は提案を受信し、検証します。

    - データフォーマットはシステムルールと一致しているか？
    - トランザクションはすでにブロックチェーン上にあるか？
    - コントラクトスクリプトは正しく実行されているか？
    - トランザクションには1回の費用のみが含まれているか？（トランザクションは二重支払いとなることを避けているか？）

    - **検証された（有効な）プロポーザルブロードキャストの場合:**

        <!-- -->
            <prepareResponse, h, k, i, [block]sigi>

    - **無効なプロポーザルブロードキャストの場合:**

        <!-- -->
            <ChangeView, h,k,i,k+1>

    <p align="center"><img src="/assets/consensus4.png" width="500"><br> <b>図9:</b> <b>デリゲート</b>達は、Block Proposalを検証し、返答します。</p>

7. `s`('prepareResponse'が配信され数）を受信した後、**デリゲート**はコンセンサスに達し、ブロックを発行します。

8. **デリゲート**達はブロックに署名します。

    <p align="center"><img src="/assets/consensus5.png" width="500"><br> <b>図10:</b> コンセンサスに達すると、<b>デリゲート</b>達がブロックに署名し、チェーンに繋がれます。</p>

9. **コンセンサスノード**が完全なブロックを受け取った時、現在のビューデータが消去され、新たなコンセンサスのラウンドが開始されます。
	- `k = 0`

---

**注釈:**

 もし、同一のビューにおいてコンセンサスなしに(![timeout](/assets/consensus.timeout.png) )秒を経過した後：
  - **コンセンサスノード**は以下を配信：

	<!-- -->
	    <ChangeView, h,k,i,k+1>

  - いったん**コンセンサスノード**が少なくとも`s`の数の同一のビューの変更を表す配信を受け取ると、ビュー`v`の値が増加し、新たなコンセンサスのラウンドの引き金となります。
  
  
  
  
  
## 6 - 参考文献
1. [ブロックチェーンのビザンチン耐性トレランスアルゴリズム](https://www.neo.org/Files/A8A0E2.pdf)
2. [実用ビザンチンフォールトトレランス](http://pmg.csail.mit.edu/papers/osdi99.pdf)
3. [ビザンチンの一般問題](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/12/The-Byzantine-Generals-Problem.pdf)
