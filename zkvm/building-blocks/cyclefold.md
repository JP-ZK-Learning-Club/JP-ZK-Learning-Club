# CycleFold

## モチベーション

NovaのfoldingではPedersen Commitmentを使用するため、検証に際し以下のような2つの楕円曲線のスカラー乗算が発生します。同様の操作がHyper Novaでは1回、ProtoStarでは3回発生します。

$$
\bar{E} = \bar{E_1} + r \bar{T} + r^2 \bar{E_2}
$$

$$
\bar{W} = \bar{W_1} + r \bar{W_2}
$$

楕円曲線のスカラー乗算は何度も加算を行う必要があり、スカラーのサイズが大きければ大きいほど計算コストが膨れてしまいます。



ここでBase fieldをF\_q、Scalar fieldをF\_pとすると上記の演算ではF\_p上でF\_qを扱う必要が出てきます。しかしq>pであり、F\_qによってF\_pを表現することはできますが`F_p​`によってF\_q`​`を直接表現することは基本的に難しく、実用的ではありません。

つまりProver側で生成したF\_p上のProofはVerifier側ではF\_qも使って計算する必要があり、この部分の計算コストを減らすことがゴールです。

## アイデア1. Non-Native Field Arithmetic

そこで、二つの楕円曲線を利用します。

\[$ E\_1] : base field\[$ F\_q], scalar field \[$ F\_p]&#x20;

\[$ E\_2] : base field\[$ F\_p], scalar field \[$ F\_q]

E1上の回路のverifierをE2上で実装し、同様にE2上の回路のverifeirをE1上に実装します。

\[$ E\_1]上の回路のverifierを\[$ E\_2]上に、\[$ E\_2]上の回路のverifierを\[$ E\_1]上に実装して、non-native arithmeticを回避している。



以下はHalo2におけるcycle curveのイメージです。

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p><a href="https://zcash.github.io/halo2/background/curves.html?highlight=cycle#cycles-of-curves">https://zcash.github.io/halo2/background/curves.html?highlight=cycle#cycles-of-curves</a></p></figcaption></figure>



このアイデアは元々ZCashのHalo2でも採用されていたもので、これをNova style foldingに適応させたのがCycleFoldです。Hal2でもIVCを実現していますが、こちらはFoldingではなくAccumulationという手法を適応させたものでした。

例えば

## アイデア2. Non-Native Field Arithmetic

以下の図のように楕円曲線E\_1とE\_2で別々の処理が走っている。

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## 参考

[https://eprint.iacr.org/2023/1192](https://eprint.iacr.org/2023/1192)\
[https://eprint.iacr.org/2023/969](https://eprint.iacr.org/2023/969)\
[https://zk-learning.org/assets/lecture10.pdf#page=30.00](https://zk-learning.org/assets/lecture10.pdf#page=30.00)\
[https://zcash.github.io/halo2/background/curves.html?highlight=cycle#cycles-of-curves](https://zcash.github.io/halo2/background/curves.html?highlight=cycle#cycles-of-curves)\
