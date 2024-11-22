---
description: Nebulaのswitch-boardは、1つの回路内に切り替えできる複数の回路を混在させることができるテクニックです。
---

# Switch-board (Nebula)

Switch-boardは、HyperNovaやMovaなどのいくつかのfolding-schemeであれば、変更なしに導入できます。論文では、R1CSに沿って説明されていますが、CCSでも同様の手法を使って書くことができます。さらに、switch-boardを使うことで、使用していない回路にはコストがかからないpay-per-useをfolding-schemeの変更なしに行えます。

HyperNovaのようなすでにNon-uniformなIVCでは、このswitch-board回路をいくつかコピーして一つの回路にすることで、複数の回路を一つのfoldingで実行でき、foldingのオーバーヘッドを減らすことも期待できます。

なお、一つのswitch-board回路で一度に実行できる内部の回路の個数は1つだけです。



R1CSで書かれた$$\ell$$個の異なる回路、（ただし入力の長さは同じ）、があるとします。

$$
A_i \cdot z_i \circ B_i \cdot z_i = C_i, \cdot z_i, i \in [1, \ell]
$$

$$A_i$$を全て結合した$$A^*$$を考えますが、$$B_i, C_i$$も同様にuniversal-switchboard回路を作ることができます。

下の図で、$$A_i$$&#x3068;_&#x53;witch constrain&#x73;_&#x4EE5;外の場所は全て0となります。また、この行列に対する$$z^*$$は、図内の右側です。

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-22 at 18.15.38.png" alt=""><figcaption></figcaption></figure>

この行列に対するzは $$z^* = (1, \times, w^*)$$で定義されます。$$w^*$$には、選択したい回路のフラグと、その回路に対する入力とwitnessが含まれますが、それ以外は全て0となります。

これを実現する制約をswitch constrainsに定義していきます。まず、$$A_i$$に対応するzを$$z_i ' = (s_i, x_i, w_i)$$とします。

$$
w^* = (s_1, x_1, w_1, s_2, x_2, w_2,...,s_{\ell-1}, x_{\ell -1}, w_{\ell -1}, s_{\ell}, x_{\ell}, w_{\ell})
$$

$$s_i$$はセレクターで、次&#x306E;_&#x53;ingle switch constrain&#x74;_&#x3068;_Binary switch constrain&#x74;_&#x3092;満たすように制約します。

1. _Single switch constraint:_ Enforce $$\sum_{i = 1}^{\ell} s_i = 1.$$
2. _Binary switch constraint:_ For $$i \in [1, \ell]$$, enforce $$s_i \cdot (1-s_i) = 0$$

次に、このセレクター$$s_i$$を使って、使用したい回路以外の入力を0であることを強制したいので、次の制約を定義します。

3. _Input consistency constraint_: For $$i \in [1,\ell]$$, enforce $$\times \cdot s_i = x_i$$

もし、$$s_k = 1$$の場合は$$x_i = 0, i \neq k$$である必要があります。さらに、入力が0の回路はwitnessが0でも問題ないので、$$w_i = 0, i \neq k$$とすることができます。



HyperNovaでは0へのコミットは無料なので、使わない回路にコストはかかりません。

Novaの場合は、もう少し工夫が必要ですが、同様の方法で実現できます。



参考

* [https://eprint.iacr.org/2024/1605.pdf](https://eprint.iacr.org/2024/1605.pdf)

