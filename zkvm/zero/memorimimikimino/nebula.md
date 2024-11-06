---
description: Commitment-Carry IVCを使った方法
---

# Nebula

## 概要

[Nebula](https://eprint.iacr.org/2024/1605.pdf)では、`commitment-carrying IVC`をIVCのstepで受け渡しいくダイジェストとして用いることで、zkVMのMemory Consistency Checkを行う手法が提案されいます。

Memory Checkingの立ち位置としてはSpiceの後継で、主にMultisetの表現について改良がなされています。

Spiceでは、Multisetを表現するためにHash(Poseidonなど)の楕円曲線上の加算を用いていましたが、Nebulaでは、衝突耐性を持つ一方向性の関数としてJoltと同じfingerprintingを使い、`commitment-carrying IVC`でMultisetを表現しています。なお、fingerprintingは、ランダム線型結合で計算されます。

Nebulaのもう一つの特徴であるswitchboardは、MemoryCheckingからは外れるため、このページでは解説しません。

## 前提知識

### Committed Relation

この$$R(com)$$は、structure $$s$$、instance $$u$$, witness $$w$$がとある関係$$R$$にあり、さらにcommit $$C$$と public-param $$pp_{com}$$が、$$com = (Gen, Commit)$$によって生成されたものであることを示す関係です。

また、structure $$s$$ は、folding schemeでたたみ込まれるインスタンスの構造の種類を表しています。

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-05 at 14.33.12.png" alt=""><figcaption></figcaption></figure>

### Incrementally Verifiable Computation (IVC) & Non-uniform IVC (NIVC)

$$(i, z_0, z)$$を入力に受け取って$$z_{i+1} \leftarrow F^i (z_0)$$を証明する$$\Pi_{i}$$を生成する$$F$$を使い、$$(i_{i+1}, z_0, z_{i+1})$$と$$\Pi_{i+1}$$を再帰的に計算していくことで、繰り返しの計算を再帰的に証明していきます。

NIVC は、IVC の一般化で、各ステップで実行される関数が固定ではなく、決まった関数集合 $$F_1,F_2,…,F_\ell$$​ から選ばれます。$$F$$の選択には補助的な関数$$\phi$$を利用し、全体では$$z_{j+1} \leftarrow F^j_{\phi(z_j, w_j)} (z_j, w_j)$$を計算します。

### Multi-folding schemes

Folding schemesでは、構造sを持つ関係Rの2つのインスタンスを1つのインスタンスの検証に削減しますが、HyperNovaなどのMulti-folding schemesでは、$$s_1$$の構造を持つ関係$$R_1$$のインスタンス$$u_1$$と、$$s_2$$の構造を持つ関係$$R_2$$のインスタンス$$u_2$$を、関係$$R_1$$のインスタンスの検証に削減します。

2つの関係 $$R_1$$ と $$R_2$$に対して、ある互換性述語（predicate） $$compat$$ があり、この述語が $$R_1$$​ と $$R_2$$のインスタンス間で満たされる必要があります。また、$$\mu, \nu \in \mathbb{N}$$ はそれぞれのインスタンスの数を表すパラメータです。この$$(R_1, R_2, compat, \mu, \nu)$$対して、Multi-foldingでは次の4つの関数が定義されます。

* $$g(1^{\lambda}, N) = pp$$ : 　security param $$\lambda$$ とサイズ上限 $$N$$からpublic param $$pp$$を求める。
* $$Enc(pp,(s_1,s_2)) → (pk, vk)$$ : 　 $$pp$$と構造$$s_1, s_2$$から証明・検証の為の鍵を求める。
* $$P(pk,(\overrightarrow{u_1}, \overrightarrow{w_1}),(\overrightarrow{u_2}, \overrightarrow{w_2})) → (u,w)$$ :　 $$\mu$$個の$$R_1$$のinstance $$u_1$$とwitness $$w_1$$の配列と、$$\nu$$個の$$R_2$$のinstance $$u_2$$とwitness $$w_2$$の配列を、$$R_1$$のinstance $$u$$とwitness $$w$$に畳み込む。
* $$V(vk,(\overrightarrow{u1}, \overrightarrow{u2})) → u$$ :　 $$u_1$$と$$u_2$$の配列から新しいinstance $$u$$を作る。



### NIVC-compatible multi-folding scheme

&#x20;$$R_1$$と、$$R'_2$$に基づくコミット関係$$R_2$$ を考えた時、決定論的な $$V$$を持つmulti-folding scheme $$( Gen , Enc , 𝑃 , 𝑉 )$$ が、以下のプロパティを満たす場合にNIVC互換であるとします。

* NP-completness:　任意の算術回路 $$F$$のinput $$x$$, $$w$$、output $$y$$に対して、構造・インスタンス・witness のタプル$$( s _2 , u , w )$$ が $$R'_2$$​ に属し、 $$F(x,w)=y$$ が成り立つ。
* Partial functions:　$$\text{compat}(s_1 ​ ,s_2 ​ )=1$$を満たす$$compat(s_1 ​ ,s_2 ​ )=1$$$$(s_1, s_2)$$を生成する$$\text{enc}_{str}(F)$$と、$$R'_2$$ のinstance $$u$$を生成する$$\text{enc}_{inst}((x, y))$$が存在し、ある$$R'_2$$のwitness $$w$$に対して$$(s_2, u,w) = \text{enc}(F,(x, y), w)$$となる。
* Monotonicity:　任意の算術回路 ( F ) と ( G ) について、もし $$|F| \leq |G|$$であれば、$$|\text{enc}_{\text{str}}(F)| \leq |\text{enc}_{\text{str}}(G)|$$ が成り立つ。ここで、$$|F|$$は $$F$$のゲートの総数を表し、$$|\text{enc}_{\text{str}}(F)|$$ _は、_$$\text{enc}_{\text{str}}(F)$$内の制約の数を示す。
* Default instances:　あるデフォルトインスタンス $$(u_{\bot}, w_{\bot})$$ が存在し、任意の公開パラメータ $$\text{pp}$$と構造 $$\text{s}$$に対して、$$(\text{pp}, s, u_{\bot}, w_{\bot}) \in R_1$$ が成り立つ。

## Commitment-carrying NIVC from multi-folding schemes

