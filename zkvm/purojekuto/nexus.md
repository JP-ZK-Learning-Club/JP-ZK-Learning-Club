---
description: Folding Schemeベースの並列化zkVM
---

# Nexus

Nexus は簡潔な証明圧縮を備えた並列化された SuperNova-HyperNova-CycleFold マシンです。

### Nexus Virtual Machine (NVM)

Nexus は上記の並列処理に適した命令セットアーキテクチャで構成された仮想マシンを有しています。基本的には vnTinyRAM と RISC-V からインスピレーションを得たシンプルかつ最小限な CPU、メモリ、I/O の構成を用いており、証明者のパフォーマンスを最大化するための工夫がされています。

CPU サイクル F に対し、(1)実行 (2)IVC (3)圧縮 の 3 つのステップで実行結果 y と証明 π を返します。圧縮ステップの最後に証明を生成するため、非常に効率的です。

<figure><img src="../../.gitbook/assets/スクリーンショット 2024-10-22 14.52.26.png" alt=""><figcaption><p><a href="https://whitepaper.nexus.xyz/">https://whitepaper.nexus.xyz/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/スクリーンショット 2024-10-22 14.54.11.png" alt=""><figcaption><p><a href="https://whitepaper.nexus.xyz/">https://whitepaper.nexus.xyz/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/スクリーンショット 2024-10-22 15.04.34.png" alt=""><figcaption><p><a href="https://whitepaper.nexus.xyz/">https://whitepaper.nexus.xyz/</a></p></figcaption></figure>

### zkVM co-processors

さらに、zkVM co-processors と呼ばれる EVM プリコンパイルに似た拡張機能を有しており、開発者は自身で CCS 回路を定義して NVM 命令セットを拡張することができます。

たとえば、SHA-256 ハッシュの計算は、zkVM で証明しようとすると 64,000CPU サイクルが必要となり、それは ASIC などの手動で作成された回路では 30,000CPU サイクルに落ち着きます。

zkVM co-processors はこの中間を取るアプローチで、ゲストプログラムとして事前定義された回路を実行します。

この zkVM co-processors によって、SHA-2、ECDSA 署名、行列の乗算、平方根の計算などの演算を高速化することが期待されます。

### CCS インスタンスの変換

customizable constraint system(CCS)に対し、下記のステップによって変換を施した後に証明を行います。

1. マルチフォールディングスキームを適用する
2. CycleFold 変換を適用する
3. Fiat-Shamir 変換を適用する
4. SuperNova non-uniform IVC 変換を適用する
5. 並列化を適用する

### 証明生成

証明時は 3 つのステップに分かれます。

1. IVC 証明を作成\
   (1)マシン状態の初期化 (2)実行トレースの生成 (3)IVC 証明を計算
2. 証明の圧縮\
   (1)マシンアウトプットを抽出 (2)IVC 検証者の実行のための instance-witness ペアを計算 (3)圧縮された zkSNARK 証明を生成
3. 出力: 最終証明、instance、出力を返す

### 参考文献

- [https://whitepaper.nexus.xyz/](https://whitepaper.nexus.xyz/)
- [https://github.com/nexus-xyz/nexus-zkvm](https://github.com/nexus-xyz/nexus-zkvm)
