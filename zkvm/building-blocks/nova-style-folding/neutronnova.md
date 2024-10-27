# NeutronNova

NeutronNovaはZeroCheck関係を畳み込むFolding Schemeの一種です。ZeroCheck関係を畳み込むことでJoltのようなLookup関係やSumcheck Protocolなどの色々な関係も畳み込むことができます。

Incrementally Verifiable Computation(IVC)だけでなくProof Carrying Data (PCD)も実現することができるため並列処理が可能で、n個の任意の関係を一つに畳み込むことができます。また、Movaと同じくコミットメントを作成せずに証明できるという性質を持っています。

### Idea

NeutronNovaはzkVMをより効率的に設計するための提案です。既存提案であるJoltは時間効率が良く、Nexusは空間効率が良いことで知られていますが、NeutronNovaはそれらを両立させることを目標としています。そのためのアイデアとして、Joltで用いられている命令ステップごとに存在するLookup関係を1つに畳み込むことで効率化しています。

{% hint style="info" %}
ZeroCheck関係とは、ある多項式f(x, w)があるとき、いくつかの点xにおいて多項式の評価が0であることを証明するための関係 {f, x, w} のことを指します。
{% endhint %}

1つのLookup関係は4つのGrand Product関係に変換することができ、それらはSumcheck Protocolを用いるかGKR Protocolを用いて効率的に証明することが知られています。NeutronNovaでは、以下のプロセスでLookup関係を畳み込みます。

1. Lookup関係(LKP)を4つのGrand Product関係(GP)に変換
2. GPをZeroCheck関係(ZC)に変換
3. ZCをNested SumCheck関係(NSC)とPowerCheck関係(PC)に変換
4. NSCとPCを畳み込み

4のプロセスをSumFoldといい、3と4のプロセスを合わせてZeroFoldといいます。

### SumFold

以下のようなSumCheck証明があるとします。

$$
\sum _{x \in \{ 0,1 \}^l}  Q(x_i, w) = \sum _{x \in \{ 0,1 \}^l} F(G(x_i, w)) =T
$$

このとき、Q(x,w)=F(G(x,w))で表せる構造的な多項式であると仮定します。これをSumCheck関係で表すと以下のようになります。ppはprover parameter、uはインスタンス、xとwはインプットです。

$$
\text{SC} = \{ pp, (F,G), (T, \vec{u}, \vec{x}), \vec{w} \}
$$

この関係を畳み込むには、Gに着目します。Gは複数の出力を持つ多項式なので、以下のようにgを用いて定義します。これを簡単にランダム線形結合を用いて畳み込むことはできません。そこで、畳み込みの結果の多項式をラグランジュ補完によって求めます。

$$
G(x,w) = \{g_0(x),g_1(x),...,g_{t-1}(x)\}
$$

まず、ゴールはある2つのSumCheck関係にあるGをgとhとおいたとき、それを一つのfという関数に畳み込むことです。まず、それをするために以下のようなfを定義します。これはb=0のときにgを、b=1のときにhを表現します。

$$
f(b,x) = (1-b) \cdot g(x) + b \cdot h(x)
$$

そうすると証明すべきことは以下の2つのSumCheckに置き換え可能です。

$$
T_0 = \sum _{x \in \{ 0,1 \}^l}  f(0,x)
$$

$$
T_1 = \sum _{x \in \{ 0,1 \}^l}  f(1,x)
$$

この2つの式をラグランジュ係数として埋め込むと、SumCheckは以下のようになります。

$$
T' = \sum _{x \in \{ 0,1 \}^l}  eq(X,b) \cdot T_b = \sum _{b} eq(X,b) \cdot \sum _{x} f(b,x)
$$

この法則に従ってu,x,wなども畳み込むことができます。Schwarz-Zippelの補題により、実際にはランダムなβをxの中から選んでチェックします。

### ZeroFold

上記のSumFoldを用いてZeroCheck関係(ZC)を畳み込みます。

TODO
