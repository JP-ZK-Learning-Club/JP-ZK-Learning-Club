# Cycle Fold

## モチベーション

NovaのfoldingではPedersen Commitmentを使用するため、検証に際し以下のような2つの楕円曲線のスカラー乗算が発生します。同様の操作がHyper Novaでは1回、ProtoStarでは3回発生します。

$$
\bar{E} = \bar{E_1} + r \bar{T} + r^2 \bar{E_2}
$$

$$
\bar{E} = \bar{E_1} + r \bar{T} + r^2 \bar{E_2}
$$



