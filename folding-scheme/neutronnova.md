# NeutronNova

NeutronNovaはZeroCheck関係を畳み込むFolding Schemeの一種です。ZeroCheck関係を畳み込むことでJoltのようなLookup関係やSumcheck Protocolなどの色々な関係も畳み込むことができます。



Incrementally Verifiable Computation(IVC)だけでなくProof Carrying Data (PCD)も実現することができるため並列処理が可能で、n個の任意の関係を一つに畳み込むことができます。また、Movaと同じくコミットメントを作成せずに証明できるという性質を持っています。

### Idea

NeutronNovaはzkVMをより効率的に設計するための提案です。既存提案であるJoltは時間効率が良く、Nexusは空間効率が良いことで知られていますが、NeutronNovaはそれらを両立させることを目標としています。そのためのアイデアとして、Joltで用いられている命令ステップごとに存在するLookup関係を1つに畳み込むことで効率化しています。

{% hint style="info" %}
ZeroCheck関係とは、ある多項式f(x, w)があるとき、いくつかの点xにおいて多項式の評価が0であることを証明するための関係 {f, x, w} のことを指します。
{% endhint %}

NeutronNovaでは、以下のプロセスでLookup関係を畳み込みます。

1. Lookup関係(LKP)を4つのGrand Product関係(GP)に変換
2. GPをZeroCheck関係(ZC)に変換
3. ZCをNested SumCheck関係(NSC)とPowerCheck関係(PC)に変換
4. NSCとPCを畳み込み

4のプロセスをSumFoldといい、3と4のプロセスを合わせてZeroFoldといいます。

### SumFold

以下のようなSumCheck証明があるとします。

$$
T =\sum _{x \in \{ 0,1 \}^l}  Q(x_i, w) = \sum _{x \in \{ 0,1 \}^l} F(G(x_i, w))
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

どうように以下の様なZeroCheckの証明があるとします。

$$
0=Q(x_i, w) = F(G(x_i, w))
$$



このときのZeroCheck関係は以下のようになります。

$$
\text{ZC} = \{ pp, (F,G), (\vec{u}, \vec{x}), \vec{w} \}
$$

このZCをSumCheck関係SCに変換するために、以下のような多項式を定義します。τはランダムなチャレンジです。

$$
e_1(x_1) = \sum _{z_1 \in \{ 0,1 \}^l} eq(z_1,x_1) \cdot \tau ^{z_1}
$$

$$
e_2(x_2) = \sum _{z_2 \in \{ 0,1 \}^l} eq(z_2,x_2) \cdot \tau ^{ \sqrt{2^l} \cdot z_2}
$$

そしてe(x)も定義します。

$$
e(x)=e_1(x_1) \cdot e_2(x_2)
$$

proverはe1とe2のコミットメントをを送信し、verifierはe+ρeを計算し、folded witnessを計算することができます。しかし、ここで、コミットメントにpowers of τが適切に利用されていることも追加で検証する必要が生じます。つまりZCを変換しようとすると、以下のNested SumCheck関係(NSC)とPowerCheck関係(PC)の2つに変換されます。

$$
\text{NSC} = \{ pp, (F,G), (T, \vec{u}, \vec{x}, \bar{e}), (\vec{w},e) \}
$$

$$
\text{PC} = \{ pp, (\bar{e}, \tau),e \}
$$

このNSCとPCを同時にfoldingすることでZeroFoldを行なうことができます。

### Lookup関係のFolding

LassoのようなIndexed Lookup Argumentがあるとします。vはベクトル、tはテーブル、aはindicesを表します。

$$
v_i = t_{a_i}
$$

このためのLookup関係(LKP)は以下のようになります。

$$
\text{LKP}=\{ pp, (\bar{t}, \bar{a}, \bar{v}), (t,a,v)  \}
$$

ここで、proverは、テーブルtにベクトルvが存在することを証明するために以下が成り立つcとfを計算し、そのコミットメントをverifierに送信します。

$$
\Pi _{i \in n} (i + t_i \cdot X - Y) \cdot \Pi _{i \in m} (a_i + v_i \cdot x + (c_i + 1) \cdot X^2 - Y)
$$

$$
= \Pi _{i \in m} (a_i + v_i \cdot X + c_i \cdot X^2 - Y) \cdot \Pi (i + t_i \cdot X + f_i \cdot X^2 - Y)
$$

これは、合計で4つのGrandProductがあると解釈することができ、簡単に4つのGrandProduct関係(GP)として表現できます。

GrandProductの証明には大きく2つの方法があります。1つはSumcheck Protocolを用いた方法で、もう1つはGKRを用いた方法です。前者はvのエントリ数だけコミットメントしなければならないというダウンサイドがあり、後者は対数回のSumcheck Protocolの呼び出しが必要というダウンサイドがあります。

NeutronNovaでは、前者のSumcheck Protocolを用いた方法に照らし合わせてGrandProduct関係(GP)をZeroCheck関係(ZC)に変換しています。そうすれば、あとはZeroFoldを用いてfoldingすることができます。

### 改善できそうなポイント

* ZC→NSCのような関係の変換にはラグランジュ多項式補完を伴うため、なるべく変換回数が少なく済むように方式を改善する。
* GrandProductを変換する際のオプションであるGKR Protocolのfoldingバージョンを提案する。
* NebulaをZeroFoldで畳み込む。
* ZeroFold/SumFoldの計算を並列化してO(n log d)をO(logn \* logd)オーダーに削減する。

### Reference

* [https://eprint.iacr.org/2024/1606](https://eprint.iacr.org/2024/1606)
