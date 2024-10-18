---
description: SP1はRISC-V の命令セットをサポートしているzkVMです。
---

# SP1(wip)

開発者はRustで記述した任意のプログラムをSP1 zkVMでコンパイルし、実行することでそのプログラムのexcution proofを作成することができます。

## Plonky3

SP1の証明システムにはPlonky3を使用しています。

Plonky3はPlonky2のField size(64bit)を32bitに小さくしたもので、ハードウェアフレンドリーなフィールドサイズを持ちながらPlonky2と同等の安全性を保っています。

このように「Field Sizeを小さくしつつ安全性を保つ」というアイデアは後述する[Binius](https://vitalik.eth.limo/general/2024/04/29/binius.html)や[Circle STARK](https://vitalik.eth.limo/general/2024/07/23/circlestarks.html)でも共通しており、昨今のトレンドとも言えます。

このPlonky3(およびPlonky2)はPlonkishな証明システムにzk-STARKsのFRIコミットメントを適応させたものであり、R1CSとは異なるAIR(Arithmetic Intermediate Representation)という形でステートメントを表現します。

AIRやFRIの仕組みついては[こちらの記事](https://zenn.dev/qope/articles/8d60f77e3a7630#stark%E3%81%A8%E3%81%AF)をご参照ください。記事内で記述されている用に、zk-STARKsはVM friendlyな証明システムと言えるわけです。



## アーキテクチャ

まず、Rustのプログラムをコンパイルした結果得られる[ELF](https://ja.wikipedia.org/wiki/Executable\_and\_Linkable\_Format) Fileを元にSP1 RISC-V Runtimeが走ります。

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

ここでのChipとはハードウェア的な意味ではありません。特定の機能の効率的な組み込み済みzk実装と捉えるとわかりやすいかと思います。Halo2に馴染みがある方であれば、ピンと来るかもです。そうではない人は[この記事](https://trapdoortech.medium.com/zero-knowledge-proof-a-guide-to-halo2-source-code-9be0cf792f18)を読むとイメージつくかと思います



各chipはMachineAir Traitと依存関係を持っており、STARK Machineにおける実行内容をAIRのステートメントとして変換する処理が行われます。

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Precompiled ChipsとSP1 baed zkEVMの可能性

ここで注目して欲しいのがPrecompiled Chipsの存在です。\
これらはEVMで言うところの[Precompiled Contact](https://www.evm.codes/precompiled)のような存在だと考えられます。\
つまり、SP1は汎用的なzkVMでありながらzkEVMへの応用も見越しているようです。

zkEVM開発者側から見るとzkEVMそのものを作る場合に発生する仕様変更への対応コストを削減することができるため、良いこの設計思想だと思います。
