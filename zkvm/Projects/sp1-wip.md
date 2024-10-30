---
description: SP1はRISC-V の命令セットをサポートしているzkVMです。
---

# SP1

開発者はRustで記述した任意のプログラムをSP1 zkVMでコンパイルし、実行することでそのプログラムのexcution proofを作成することができます。

例えばzkTLS(Webproof)をSP1で実装したりできます。[https://x.com/CremaLabs/status/1847182768306053583](https://x.com/CremaLabs/status/1847182768306053583)

## Architecture

基本的なProofシステムは以下のようなRisc 0のアーキテクチャを模倣しています。

なぜSTARK Proof Systemなのかは次の項で説明します。

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p><a href="https://risczero.com/blog/designing-high-performance-zkVMs#aa7a0ec142e844d899a4482066cf33f1">https://risczero.com/blog/designing-high-performance-zkVMs#aa7a0ec142e844d899a4482066cf33f1</a></p></figcaption></figure>

もう少し深く見ていくと下図のようにRustのプログラムをコンパイルして得られる[ELF](https://ja.wikipedia.org/wiki/Executable\_and\_Linkable\_Format) Fileを元にExcutionが始まります。

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

そして黄色のProofの部分で以下の順序に従った処理が行われます。

1. RISC-V Prooving
2. Aggregation Prooving
3. STARK-to-SNARK Prooving

大まかなアーキテクチャと実行順序このようになります。詳細を見ていく前にSP1のSTARK Proof System(Plonky3)について解説します。

## Plonky3

SP1のSTARK Proof Systemには[Plonky3](https://github.com/Plonky3/Plonky3)を使用しています。

Plonky3はPlonky2のField size(64bit)を32bitに小さくしたもので、ハードウェアフレンドリーなフィールドサイズを持ちながらPlonky2と同等の安全性を保っています。

このように**Field Sizeを小さくしつつ安全性を保つ**というアイデアは[Binius](https://vitalik.eth.limo/general/2024/04/29/binius.html)や[Circle STARK](https://vitalik.eth.limo/general/2024/07/23/circlestarks.html)でも共通しており、昨今のトレンドとも言えます。

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p><a href="https://risczero.com/blog/designing-high-performance-zkVMs">https://risczero.com/blog/designing-high-performance-zkVMs</a></p></figcaption></figure>

このPlonky3(およびPlonky2)はPlonkishな証明システムにzk-STARKsのFRIコミットメントを適応させたものであり、R1CSとは異なるAIR(Arithmetic Intermediate Representation)という形でステートメントを表現します。

AIRやFRIの具体的な仕組みついては[こちらの記事](https://zenn.dev/qope/articles/8d60f77e3a7630#stark%E3%81%A8%E3%81%AF)をご参照ください。

記事内で記述されている用に、繰り返し処理や条件分岐といった複雑な状態遷移を表現する上で、zk-STARKsベースな証明システムはzkVMにおいてzk-SNARKsよりも効率的と言えます。

## Building Circuits with Plonky3 <a href="#id-2175" id="id-2175"></a>

VMを構成する要素はChipと呼ばれています。

ここでのChipとはハードウェア的な意味ではありません。特定の機能の効率的な組み込み済みzk実装と捉えるとわかりやすいかと思います。Halo2に馴染みがある方であれば、ピンと来るかもです。そうではない人は[この記事](https://trapdoortech.medium.com/zero-knowledge-proof-a-guide-to-halo2-source-code-9be0cf792f18)を読むとイメージつくかと思います。

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

各chipはMachineAir Traitと依存関係を持っており、STARK Machineにおける実行内容をAIRのステートメントとして変換する処理が行われます。



この図で注目して欲しいのがPrecompiled Chipsの存在です。\
これらはEVMで言うところの[Precompiled Contact](https://www.evm.codes/precompiled)のような存在だと考えられます。\
つまり、SP1は汎用的なzkVMでありながらzkEVMへの応用も見越しているようです。

zkEVM開発者側から見るとzkEVMそのものを作る場合に発生する仕様変更への対応コストを削減することができるため、良いこの設計思想だと思います。

ちなみにSP1はオープンソースなので自由にPrecompiled Chipを追加できます。



## Interaction <a href="#id-7432" id="id-7432"></a>

任意の命令実行に対し各Chipsは他のChipsと相互にやり取りする場合あり、CpuChipを中心にこの相互接続関係を制約しています。

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p><a href="https://miro.medium.com/v2/resize:fit:1400/format:webp/1*aLbVUC4L_EG9i0sctxnplQ.jpeg">https://miro.medium.com/v2/resize:fit:1400/format:webp/1*aLbVUC4L_EG9i0sctxnplQ.jpeg</a></p></figcaption></figure>

ではどのように相互通信の順列を保証しているのでしょうか？

この課題を解決しているのが[Logup(Log Derivative Lookup Argument)](https://eprint.iacr.org/2022/1530.pdf)という仕組みです。これは証明者が「自分の持つ秘密の値が特定のテーブルの中に存在すること」をゼロ知識証明で示すものです。元々[Lookup Argument](https://eprint.iacr.org/2023/1518)という仕組みがあり、Logupはこれを応用しています。これはzkVMのように複雑で大規模な証明システムにおいて証明サイズ(および証明時間)の大幅な削減につながります。

**どんなに複雑なCircuitでもLookupベースで構築可能である**というアイデアは[Lookup Singularity](https://zkresear.ch/t/lookup-singularity/65)と呼ばれており、barry whitehatが残した功績の中でも特に大きいものです。

{% hint style="info" %}
todo:Logupのページを書き上げ、リンクを貼る。
{% endhint %}



{% hint style="info" %}
todo:permutationのページを書き上げ、リンクを貼る。
{% endhint %}

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p><a href="https://miro.medium.com/v2/resize:fit:1400/format:webp/1*bii5A9CF8-JIgLoSy5oThQ.jpeg">https://miro.medium.com/v2/resize:fit:1400/format:webp/1*bii5A9CF8-JIgLoSy5oThQ.jpeg</a></p></figcaption></figure>



## Memory consistency <a href="#id-49df" id="id-49df"></a>

命令の割り込みなどが発生していないことを保証するためには、メモリーの一貫性を保証する必要があります。

つまり「メモリが読み出すデータは前に書き込まれたデータである」という保証が欲しいのです。

LogupやLookupを使ったPermutation checkはChipの相互接続の保証だけでなく、メモリアクセスの一貫性を保証するためにも使うことができます。これはデータの読み出しと書き込みが順列化されていることを証明する問題に置き換えるというテクニックです。

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>



{% hint style="info" %}
moemory consistency checkの項目を追加する
{% endhint %}

## Aggregation Proving

実行トレースはシャード化されており、シャード内の各チップのロジックやチップ間の相互接続が正しいことを証明している。複数の異なるシーケンスの順列(Permutation)はLogupによって解消される。

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

SP1が生成するSTARK proofはon-chainで検証するにはコストがかかるため、一つのSTARK proofにした上でそれをSNARK proofとして再帰的に証明するテクニックが用いられています。これによりSTARK proofはgroth16もしくはPlonkのSNARK proofに変換され、on-chainで現実的なコストで検証できるようになります。このように、SNARK proofでラップするテクニックは[Polygon zkEVM](https://docs.polygon.technology/zkEVM/concepts/circom-intro-brief/#what-is-circom)始まり[Intmax](https://github.com/InternetMaximalism/intmax2-mining/blob/main/gnark-server/README.md?plain=1#L3)でも採用されています。

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

SP1ではgnarkを用いてSNARK proofに変換しています。[gnark](https://github.com/Consensys/gnark)はGroth16/PlonkとそれらのSolidity VerifierをサポートしたGo実装のライブラリでありPolygon zkEVMで採用されている[Circom実装よりも高速](https://docs.gnark.consensys.io/overview#whats-gnark)です。

## **on-chain Verification**

SP1ではEthereumなどのスマートコントラクトを使ってonchainでProof検証できるようにしています。この機能自体はCircomなどでも広くサポートされている機能であり、一般的にこれは[**Solidity Verifier**](https://docs.succinct.xyz/onchain-verification/solidity-sdk.html)必要とします。

SP1では[ICICLE](https://github.com/ingonyama-zk/icicle)と呼ばれるGPU-acceleratedなライブラリを[採用](https://github.com/succinctlabs/sp1/blob/dev/crates/recursion/gnark-ffi/go/go.mod#L18)することで、prooving timeのさらなる高速化を目指しています。[この記事](https://medium.com/@ingonyama/user-guide-zk-acceleration-of-gnark-using-icicle-381f4efd13e4)に書いてある通り、gnark+ICICLEの組み合わせは現時点のベストプラクティスに思えます。

しかしSP1(およびPlonky3)はRust実装なのでRust->Goの[FFI](https://ja.wikipedia.org/wiki/Foreign\_function\_interface)を実行する必要があり、オーバーヘッドがProoving Timeに影響する可能性がありそうです。



## 参考資料

* [https://docs.succinct.xyz/introduction.html](https://docs.succinct.xyz/introduction.html)
* [https://github.com/succinctlabs/sp1](https://github.com/succinctlabs/sp1)
* [https://trapdoortech.medium.com/zero-knowledge-proof-introduction-to-sp1-zkvm-source-code-d26f88f90ce4](https://trapdoortech.medium.com/zero-knowledge-proof-introduction-to-sp1-zkvm-source-code-d26f88f90ce4)

