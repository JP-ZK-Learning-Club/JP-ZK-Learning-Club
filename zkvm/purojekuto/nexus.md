---
description: Folding Schemeベースの並列化zkVM
---

# Nexus

Nexusは簡潔な証明圧縮を備えた並列化されたSuperNova-HyperNova-CycleFoldマシンです。customizable constraint system(CCS)に対し、下記のステップによって変換を施した後に証明を行います。

1. マルチフォールディングスキームを適用する
2. CycleFold変換を適用する
3. Fiat-Shamir変換を適用する
4. SuperNova non-uniform IVC変換を適用する
5. 並列化を適用する

### 証明生成

証明時は3つのステップに分かれます。

1. IVC証明を作成\
   (1)マシン状態の初期化 (2)実行トレースの生成 (3)IVC証明を計算
2. 証明の圧縮\
   (1)マシンアウトプットを抽出 (2)IVC検証者の実行のためのinstance-witnessペアを計算 (3)圧縮されたzkSNARK証明を生成
3. 出力: 最終証明、instance、出力を返す

### 参考文献

* [https://whitepaper.nexus.xyz/](https://whitepaper.nexus.xyz/)
* [https://github.com/nexus-xyz/nexus-zkvm](https://github.com/nexus-xyz/nexus-zkvm)
