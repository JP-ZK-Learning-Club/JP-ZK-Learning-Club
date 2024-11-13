# Cycle of Curves

## モチベーション

楕円曲線のスカラー乗算は何度も加算を行う必要があり、スカラーのサイズが大きければ大きいほど計算コストが膨れてしまいます。ここでBase fieldをF\_q、Scalar fieldをF\_pとすると上記の演算ではF\_p上でF\_qを扱う必要が出てきます。しかしq>pであり、F\_qによってF\_pを表現することはできますが`F_p​`によってF\_qを直接表現することは基本的に難しく実用的ではありません。

このように異なるFieldにまたがる計算を**Non-Native Field Arithmetic**と呼んだりします。

Pedersen Commitmentを使うSNARK、特にHalo2やNovaなどIVCを構成する際にはProver側で生成したF\_p上のProofはVerifier側ではF\_qも使って計算する必要があり、この部分の制約を減らすことが重要になってきます。

## アイデア

この問題に対する「異なる二つの楕円曲線上で相互に証明・検証し合う」という解法がCycle of Curvesです

ここで二つの楕円曲線のペア(E1,E2)は以下のような特徴を有しています。

* E1のBase FieldがFqであり、Scaler FieldがFpである。
* E2のBase FieldがFpであり、Scaler FieldがFqである。

そしてE\_1上の回路のverifierをE\_2上に、E\_2上の回路のverifierをE\_1上に実装することで異なるFieldで発生するスカラー演算を回避しています。

元のアイデアは[BCTV14](https://eprint.iacr.org/2014/595)にて提唱されました。

## Halo2

この手法はZcashのHalo2でも採用されており、Pallas curveとVesta curveという二つの楕円曲線を用いて実現されています。つまり、Pallas curveのScaler FieldがVesta curveのBase Field、Vesta curveのScaler FieldがPallas curveのBase Fieldになります。

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p><a href="https://zcash.github.io/halo2/background/curves.html?highlight=curve#cycles-of-curves">https://zcash.github.io/halo2/background/curves.html?highlight=curve#cycles-of-curves</a></p></figcaption></figure>



{% hint style="info" %}
todo: accumulatorについて言及する
{% endhint %}

以下のようなパラーメータになっています。

* p = 2^254 + 45560315531419706090280762371685220353
* q = 2^254 + 45560315531506369815346746415080538113
* Ep : y^2 = x^3 + 5 over GF(p) of order q, called Pallas;
* Eq : y^2 = x^3 + 5 over GF(q) of order p, called Vesta;

ちなみに二つの曲線を文字ってPasta Curvesと呼ばれています。

## Nova

NovaでもPasta Curvesを用いたCycle of curvesを採用しています。

以下の図はNovaの表記をベースに抽象化したCycle of curvesのフローです。

<figure><img src="../../.gitbook/assets/スクリーンショット 2024-11-03 20.34.30.png" alt=""><figcaption><p>made by author</p></figcaption></figure>

元々提案されていた[Nova](https://eprint.iacr.org/2021/370)は単一の楕円曲線上で構築されていましたが効率向上のためにCycle of Curvesを使用するように変更されました。当初この変更は実装内でのみ記述されていたため安全性は証明されておらず[脆弱性](https://eprint.iacr.org/2023/969.pdf)が指摘されました。現在では安全性証明が証明された上でCycle of Curvesを用いて効率性を獲得しています。

この脆弱性については[こちらの記事](https://www.zksecurity.xyz/blog/posts/nova-attack/)にて解説されているので興味があればご覧ください。



## 参照資料

[https://hackmd.io/@JkY-zACaSqerTtn\_UwFjKg/SJZw6x75o](https://hackmd.io/@JkY-zACaSqerTtn\_UwFjKg/SJZw6x75o)\
[https://blog.icme.io/the-cost-of-recursion-explorations-in-the-state-of-the-art-of-foreign-arithmetic/](https://blog.icme.io/the-cost-of-recursion-explorations-in-the-state-of-the-art-of-foreign-arithmetic/)\
[https://medium.com/delendum/field-selection-for-recursive-snarks-726ad56c3a3c](https://medium.com/delendum/field-selection-for-recursive-snarks-726ad56c3a3c)\
[https://www.slideshare.net/slideshow/zkstudyclub-improving-performance-of-nonnative-arithmetic-in-snarks-ivo-kubjas-consensys-gnark/259623794#14](https://www.slideshare.net/slideshow/zkstudyclub-improving-performance-of-nonnative-arithmetic-in-snarks-ivo-kubjas-consensys-gnark/259623794#14)\
