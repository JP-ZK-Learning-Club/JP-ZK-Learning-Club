# CycleFold

## モチベーション

NovaのfoldingではPedersen Commitmentを使用するため、以下のような楕円曲線のスカラー乗算実行が発生します。

$$
\bar{E} = \bar{E_1} + r \bar{T} + r^2 \bar{E_2}
$$

$$
\bar{W} = \bar{W_1} + r \bar{W_2}
$$

$$
\bar{W} = \bar{W_1} + r \bar{W_2}
$$

一般的にこのような計算は非効率です。

## アイデア



## 仕組み

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## 参考

[https://eprint.iacr.org/2023/1192](https://eprint.iacr.org/2023/1192)
