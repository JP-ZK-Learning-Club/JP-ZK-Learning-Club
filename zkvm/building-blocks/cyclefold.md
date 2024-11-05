# CycleFold

## 概要

Cycle FoldはCycle of Curvesを踏襲しスカラー演算のみをSecondary Curveに外注するシンプルな設計にしたことで効率的なFolding Schemeを獲得しました。

このCycle FoldはHyper NovaのBuilding Blockになっていますが、NovaやProtoGalaxyなどにも応用することが可能です。また[Paranova](https://zkresear.ch/t/parallelizing-nova-visualizations-and-mental-models-behind-paranova/198)(Parallelizing Nova)のBuilding Blockになり得ることも示唆されています

## Recap: Cycle of curves

NovaのfoldingではPedersen Commitmentを使用するため、検証に際し以下のような2つの楕円曲線のスカラー乗算が発生します。同様の操作がHyper Novaでは1回、ProtoStarでは3回発生します。

$$
\bar{E} = \bar{E_1} + r \bar{T} + r^2 \bar{E_2}
$$

$$
\bar{W} = \bar{W_1} + r \bar{W_2}
$$

楕円曲線のスカラー乗算は何度も加算を行う必要があり、スカラーのサイズが大きければ大きいほど計算コストが膨れてしまいます。これをCycle of curvesというテクニックで緩和していました。このCycle of curvesは二つの楕円曲線上にfolding instanceを配置し、相互に証明・検証し合うという仕組みでした



Novaの実装では論文に明記せずとも暗黙的にCycle of curvesを採用していました。しかし不要なpublic inputを含んでいたり、constraintsの書き換えが可能であったりとその複雑さゆえに脆弱性が指摘されました。

## CycleFoldにおける改善点

Cycle Foldでは上記の反省を元にNova style FoldingへのCycle of curvesの適応を再検討しています。

アイデアとしてスカラー乗算のみSecondary curve E2に外注しています。これによってペアリングへの依存度が下がり、安全性証明の難易度も下がりました。

## スカラー乗算のみ外注する

NovaにおけるCycle of curvesはインスタンス全てを両方の楕円曲線上に配置していました。

しかし、そもそもnon-native arithmetic(異なるField上での操作)が必要なのはVerifierにおけるスカラー乗算の部分だけなのでこの設計は無駄な冗長性がありました。

そこでCycle Foldでは以下のように検証に必要な部分のみSecondary curve E2に外注しています。

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p><a href="https://eprint.iacr.org/2023/1192">https://eprint.iacr.org/2023/1192</a></p></figcaption></figure>

この図はHyper NovaのFoldingフローです。詳細は以下のようになります。

* uᵢ: ステップiでの計算の証明
* Uᵢ: それ以前のすべてのステップの証明
* uEC,ᵢ: アウトソースされた楕円曲線演算の証明
* C\_{EC}:スカラー乗算を行うCircuit

システムは2つの検証機能を実行します

1. HN(Hyper Nova) Partial Folding Verifier（メイン計算の検証用）
2. Nova Folding Verifier（アウトソースされた楕円曲線演算の検証用）

Foldingのフローは以下のようになります

1. uᵢとUᵢから必要な入力を抽出してCEC回路に渡します（図中の点線）
2. uᵢ₊₁は最新ステップの正当性を示します
3. (Uᵢ, uEC,ᵢ)のペアは、それまでのすべてのステップとアウトソースされた計算の正当性を表します
4. 1\~3のステップを繰り返します

このような設計により、検証に必要なインスタンスは全てPrimary curve上に集約されます。また、複数インスタンスのfoldingが容易なためPCDも構成できます。

### ペアリングへの依存を最小限にする

Cycle of curvesを提唱したBCTV14ではpairing-based SNARKsを想定していました。しかしペアリング可能なBLS12-381などの楕円曲線は一般的な楕円曲線に比べてField Sizeが大きくなり、計算量に影響します。この影響はNovaでも同様の課題でした。

しかしCycle Foldでは前述のようにPrimary curve上にインスタンスを集約し、どちらの楕円曲線にもインスタンスが配置されるようなことがなくなったのでhalf-pairing cyclesが実現可能になりました。

つまりSecondary curveはpairing-friendlyである必要がなく、Primary curveのみpairing-friendlyであれば良いのです。今まではSecondary curve上にもインスタンスを配置していたので複雑な安全性証明が必要でした。しかし、Cycle Foldで実行しているのはスカラー乗算だけなのでCycle of Curvesの安全性に依拠した設計になっています。

## 参考

[https://eprint.iacr.org/2023/1192](https://eprint.iacr.org/2023/1192)\
[https://eprint.iacr.org/2023/969](https://eprint.iacr.org/2023/969)\
[https://zk-learning.org/assets/lecture10.pdf#page=30.00](https://zk-learning.org/assets/lecture10.pdf#page=30.00)\
[https://zcash.github.io/halo2/background/curves.html?highlight=cycle#cycles-of-curves](https://zcash.github.io/halo2/background/curves.html?highlight=cycle#cycles-of-curves)\
