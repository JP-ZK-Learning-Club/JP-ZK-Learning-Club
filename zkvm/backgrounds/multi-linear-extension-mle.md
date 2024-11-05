# Multi Linear extension(MLE)

## MLEの定義

&#x20;関数 f: {0,1}^{\ell} \to \mathbb{F} に対して、\mathbb{F} 上の\ell 変数多項式 g が f を拡張すると言うのは、すべての x \in {0,1}^{\ell} に対して f(x) = g(x) が成り立つときである。

そして \[$ f: {0,1}^{\ell} \to \mathbb{F}] は、一意の多重線形拡張 (MLE) \[$ \tilde{f}] を持つ。

ここでmultilinerとは各変数の次数が高々1である多項式のことであり、(1-x\_1)(1-x\_2)はmultilinerですがx\_1^{2}x\_2はmultilinerではありません。



例としてf: {0,1}^{\ell} \to \mathbb{F}と\tilde{f}:\mathbb{F}^2 \to \mathbb{F}を以下のように表現します

緑部分を拡張したのがMLEを表しています。ここでは便宜上5×5の行列で表現します。

<figure><img src="../../.gitbook/assets/スクリーンショット 2024-11-05 20.28.49.png" alt=""><figcaption><p>made by author</p></figcaption></figure>



\tilde{f}(x\_1,x\_2)=(1-x\_1)(1-x\_2)+2(1-x\_1)x\_2+8x\_1(1-x\_2)+10x\_1x\_2であるとき

\tilde{f}(0,0)=1

\tilde{f}(0,1)=2

\tilde{f}(1,0)=8

\tilde{f}(1,1)=10

というように、元のfと同じ評価を得ることができます。

またnon MLE なg(x\_1,x\_2)=-x^2\_1+x\_1x\_2+8x\_1+x\_2+1は同様に元のfと同じ評価を得ることができます

g(0,0)=1

g(0,1)=2

g(1,0)=8

g(1,1)=10



## 効率的なMLEの評価

この表のように関数 f : {0,1}^\ell \rightarrow \mathbb{F} の全ての評価値（つまり f の全ての入力に対する出力）が与えられているとします。このとき、任意の点 r \in \mathbb{F}^\ell における拡張された関数 \tilde{f}(r) の値を効率的に計算したいとします。



各入力  w \in \\{0,1\\}^\ell  に対して、次のように多重線形ラグランジュ基底多項式  \delta\_w(r)  を定義します

$$
\delta_w(r) = \prod_{i=1}^\ell (r_i w_i + (1 - r_i)(1 - w_i))
$$

これは特定の  w  に対応する多項式です。次の式で  \tilde{f}(r)  を表現できます：\


$$
\tilde{f}(r) = \sum_{w \in \{0,1\}^\ell} f(w) \cdot \delta_w(r)
$$

ここで、各  w  に対する  f(w)  の値と基底多項式  \delta\_w(r)  を掛け合わせて合計を取ります



## 計算コストの評価

• 各  w \in \\{0,1\\}^\ell  に対して  \delta\_w(r)  を計算するには、 O(\ell)  のフィールド演算が必要です。

• よって、全体での計算量は  O(\ell 2^\ell)  となります。

• さらに、動的計画法を用いることで、計算時間を  O(2^\ell)  に減らすことができます。
