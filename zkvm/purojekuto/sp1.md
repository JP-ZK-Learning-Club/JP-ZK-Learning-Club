---
description: SP1はRISC-V の命令セットをサポートしているzkVMです。
---

# SP1

開発者は Rust で記述した任意のプログラムを SP1 zkVM でコンパイルし、実行することでそのプログラムの excution proof を作成することができます。

例えば zkTLS(Webproof)を SP1 で実装したりできます。[https://x.com/CremaLabs/status/1847182768306053583](https://x.com/CremaLabs/status/1847182768306053583)

## Architecture

基本的な Proof システムは以下のような Risc 0 のアーキテクチャを模倣しています。

なぜ STARK Proof System なのかは次の項で説明します。

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p><a href="https://risczero.com/blog/designing-high-performance-zkVMs#aa7a0ec142e844d899a4482066cf33f1">https://risczero.com/blog/designing-high-performance-zkVMs#aa7a0ec142e844d899a4482066cf33f1</a></p></figcaption></figure>

もう少し深く見ていくと下図のように Rust のプログラムをコンパイルして得られる[ELF](https://ja.wikipedia.org/wiki/Executable\_and\_Linkable\_Format) File を元に Excution が始まります。

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

そして黄色の Proof の部分で以下の順序に従った処理が行われます。

1. RISC-V Prooving
2. Aggregation Prooving
3. STARK-to-SNARK Prooving

大まかなアーキテクチャと実行順序このようになります。詳細を見ていく前に SP1 の STARK Proof System(Plonky3)について解説します。

## Plonky3

SP1 の STARK Proof System には[Plonky3](https://github.com/Plonky3/Plonky3)を使用しています。

Plonky3 は Plonky2 の Field size(64bit)を 32bit に小さくしたもので、ハードウェアフレンドリーなフィールドサイズを持ちながら Plonky2 と同等の安全性を保っています。

このように**Field Size を小さくしつつ安全性を保つ**というアイデアは[Binius](https://vitalik.eth.limo/general/2024/04/29/binius.html)や[Circle STARK](https://vitalik.eth.limo/general/2024/07/23/circlestarks.html)でも共通しており、昨今のトレンドとも言えます。

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p><a href="https://risczero.com/blog/designing-high-performance-zkVMs">https://risczero.com/blog/designing-high-performance-zkVMs</a></p></figcaption></figure>

この Plonky3(および Plonky2)は Plonkish な証明システムに zk-STARKs の FRI コミットメントを適応させたものであり、R1CS とは異なる AIR(Arithmetic Intermediate Representation)という形でステートメントを表現します。

AIR や FRI の具体的な仕組みついては[こちらの記事](https://zenn.dev/qope/articles/8d60f77e3a7630#stark%E3%81%A8%E3%81%AF)をご参照ください。

記事内で記述されている用に、繰り返し処理や条件分岐といった複雑な状態遷移を表現する上で、zk-STARKs ベースな証明システムは zkVM において zk-SNARKs よりも効率的と言えます。

## Building Circuits with Plonky3 <a href="#id-2175" id="id-2175"></a>

VM を構成する要素は Chip と呼ばれています。

ここでの Chip とはハードウェア的な意味ではありません。特定の機能の効率的な組み込み済み zk 実装と捉えるとわかりやすいかと思います。Halo2 に馴染みがある方であれば、ピンと来るかもです。そうではない人は[この記事](https://trapdoortech.medium.com/zero-knowledge-proof-a-guide-to-halo2-source-code-9be0cf792f18)を読むとイメージつくかと思います。

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

各 chip は MachineAir Trait と依存関係を持っており、STARK Machine における実行内容を AIR のステートメントとして変換する処理が行われます。

この図で注目して欲しいのが Precompiled Chips の存在です。\
これらは EVM で言うところの[Precompiled Contact](https://www.evm.codes/precompiled)のような存在だと考えられます。\
つまり、SP1 は汎用的な zkVM でありながら zkEVM への応用も見越しているようです。

zkEVM 開発者側から見ると zkEVM そのものを作る場合に発生する仕様変更への対応コストを削減することができるため、良いこの設計思想だと思います。

ちなみに SP1 はオープンソースなので自由に Precompiled Chip を追加できます。

## Interaction <a href="#id-7432" id="id-7432"></a>

任意の命令実行に対し各 Chips は他の Chips と相互にやり取りする場合あり、CpuChip を中心にこの相互接続関係を制約しています。

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p><a href="https://miro.medium.com/v2/resize:fit:1400/format:webp/1*aLbVUC4L_EG9i0sctxnplQ.jpeg">https://miro.medium.com/v2/resize:fit:1400/format:webp/1*aLbVUC4L_EG9i0sctxnplQ.jpeg</a></p></figcaption></figure>

ではどのように相互通信の順列を保証しているのでしょうか？

この課題を解決しているのが[Logup(Log Derivative Lookup Argument)](https://eprint.iacr.org/2022/1530.pdf)という仕組みです。これは証明者が「自分の持つ秘密の値が特定のテーブルの中に存在すること」をゼロ知識証明で示すものです。元々[Lookup Argument](https://eprint.iacr.org/2023/1518)という仕組みがあり、Logup はこれを応用しています。これは zkVM のように複雑で大規模な証明システムにおいて証明サイズ(および証明時間)の大幅な削減につながります。

**どんなに複雑な Circuit でも Lookup ベースで構築可能である**というアイデアは[Lookup Singularity](https://zkresear.ch/t/lookup-singularity/65)と呼ばれており、barry whitehat が残した功績の中でも特に大きいものです。

{% hint style="info" %}
todo:Logup のページを書き上げ、リンクを貼る。
{% endhint %}

{% hint style="info" %}
todo:permutation のページを書き上げ、リンクを貼る。
{% endhint %}

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p><a href="https://miro.medium.com/v2/resize:fit:1400/format:webp/1*bii5A9CF8-JIgLoSy5oThQ.jpeg">https://miro.medium.com/v2/resize:fit:1400/format:webp/1*bii5A9CF8-JIgLoSy5oThQ.jpeg</a></p></figcaption></figure>

## Memory consistency <a href="#id-49df" id="id-49df"></a>

命令の割り込みなどが発生していないことを保証するためには、メモリーの一貫性を保証する必要があります。

つまり「メモリが読み出すデータは前に書き込まれたデータである」という保証が欲しいのです。

Logup や Lookup を使った Permutation check は Chip の相互接続の保証だけでなく、メモリアクセスの一貫性を保証するためにも使うことができます。これはデータの読み出しと書き込みが順列化されていることを証明する問題に置き換えるというテクニックです。

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
moemory consistency check の項目を追加する
{% endhint %}

## Aggregation Proving

実行トレースはシャード化されており、シャード内の各チップのロジックやチップ間の相互接続が正しいことを証明している。複数の異なるシーケンスの順列(Permutation)は Logup によって解消される。

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

SP1 が生成する STARK proof は on-chain で検証するにはコストがかかるため、一つの STARK proof にした上でそれを SNARK proof として再帰的に証明するテクニックが用いられています。これにより STARK proof は groth16 もしくは Plonk の SNARK proof に変換され、on-chain で現実的なコストで検証できるようになります。このように、SNARK proof でラップするテクニックは[Polygon zkEVM](https://docs.polygon.technology/zkEVM/concepts/circom-intro-brief/#what-is-circom)始まり[Intmax](https://github.com/InternetMaximalism/intmax2-mining/blob/main/gnark-server/README.md?plain=1#L3)でも採用されています。

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

SP1 では gnark を用いて SNARK proof に変換しています。[gnark](https://github.com/Consensys/gnark)は Groth16/Plonk とそれらの Solidity Verifier をサポートした Go 実装のライブラリであり Polygon zkEVM で採用されている[Circom 実装よりも高速](https://docs.gnark.consensys.io/overview#whats-gnark)です。

## **on-chain Verification**

SP1 では Ethereum などのスマートコントラクトを使って onchain で Proof 検証できるようにしています。この機能自体は Circom などでも広くサポートされている機能であり、一般的にこれは[**Solidity Verifier**](https://docs.succinct.xyz/onchain-verification/solidity-sdk.html)必要とします。

SP1 では[ICICLE](https://github.com/ingonyama-zk/icicle)と呼ばれる GPU-accelerated なライブラリを[採用](https://github.com/succinctlabs/sp1/blob/dev/crates/recursion/gnark-ffi/go/go.mod#L18)することで、prooving time のさらなる高速化を目指しています。[この記事](https://medium.com/@ingonyama/user-guide-zk-acceleration-of-gnark-using-icicle-381f4efd13e4)に書いてある通り、gnark+ICICLE の組み合わせは現時点のベストプラクティスに思えます。

しかし SP1(および Plonky3)は Rust 実装なので Rust->Go の[FFI](https://ja.wikipedia.org/wiki/Foreign\_function\_interface)を実行する必要があり、オーバーヘッドが Prooving Time に影響する可能性がありそうです。

## 参考資料

* [https://docs.succinct.xyz/introduction.html](https://docs.succinct.xyz/introduction.html)
* [https://github.com/succinctlabs/sp1](https://github.com/succinctlabs/sp1)
* [https://trapdoortech.medium.com/zero-knowledge-proof-introduction-to-sp1-zkvm-source-code-d26f88f90ce4](https://trapdoortech.medium.com/zero-knowledge-proof-introduction-to-sp1-zkvm-source-code-d26f88f90ce4)
