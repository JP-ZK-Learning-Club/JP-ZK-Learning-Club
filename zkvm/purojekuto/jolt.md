---
description: Lookup Tableを利用したzkVM
---

# Jolt

Jolt (Just one Lookup Table)はa16z CryptoのArasu Arun、Srinath Setty、Justin Thalerによって開発されたRISC-V命令セットアーキテクチャ用のゼロ知識仮想マシン(zkVM)です。SP1やRISC-Zeroと比べ実装がシンプルです。

JoltのコアアイデアはルックアップテーブルとSumcheck Protocolで、それらを組み合わせることで回路サイズを抑え、証明を高速化しています。

### JoltにおけるRustプログラム実行と証明生成の流れ

Joltが証明するRISC-Vのプログラムは、Jolt自身によりコンパイルされ、内部のループでインタプリタ実行されます。ここで重要なのは、実行トレースを取る部分まではゼロ知識証明は使わず、普通のRustプログラムとして書かれており、最後の実行トレースの状態遷移を証明だけがゼロ知識証明です。

そのため、JoltはRustで書かれたプログラムに対して

* RISC-V用のバイトコード(ELF形式)へのコンパイル
* 内部のインタプリタによる実行トレースの取得
* 全ての状態遷移の証明

を行う機能を提供します。

具体的には、Joltの`attribute-macro`は、ホスト環境へのコンパイル時に`guest`クレートに定義されたRustプログラムを動的にRISC-V用のELFへコンパイルして保存、その実行・証明・検証を行う関数を生成します。

例えば、次の`add/guest/src/lib.rs`には`main.rs`で呼ばれている`build_add()`は定義されていませんが、この関数はマクロによってコンパイル時に定義されます。Joltを使った開発者は、証明したいプログラムに`#[jolt::provable]`をつけるだけで良いのです。

```rust
// add/guest/src/lib.rs
#![cfg_attr(feature = "guest", no_std)]
#![no_main]

#[jolt::provable]
fn add(a: u32, b: u32) -> u32 {
  let c = a + b;
  c
}
```

```rust
// add/src/main.rs
use guest::build_add;
pub fn main() {
    let (prove_add, verify_add) = build_add();
    let (output, proof) = prove_add(1, 2);
    let is_valid = verify_add(proof);
}
```

コンパイル時に得られたELFは、生成されたprove\_hoge()内のループで１命令ずつ実行され、プログラムカウンタ・レジスタ・メモリの読み書き、が全て記録されます。これが実行トレースです。

また、各実行では、以下が命令ごとに繰り返されます。

1. メモリのコード領域から命令をフェッチ
2. 命令をデコード
3. 命令を実行
4. 実行結果をレジスタ/メモリのデータ領域に書き込み

### Joltのコンポーネント

VMにおける上記の(1)\~(4)の処理が正しく行われていることを証明するために、4つのコンポーネントがあります。

1. 追加のR1CS: プログラムカウンタを更新し正しい命令をフェッチするR1CSインスタンスを用意してSpartanで証明する
2. オフラインメモリチェック1: バイトコードのデコード結果が正しいことを保証する
3. Lassoによる命令のルックアップ: 命令の実行結果を事前実行されたテーブルから検索して結果を返す
4. オフラインメモリチェック2: メモリへの読み込み/書き出し一貫性を保証する

上記を3つの証明にして、それを命令ごとに繰り返しています。さらに、命令毎の状態の遷移は、オフラインメモリチェックにより一貫性が保たれます。

<figure><img src="../../.gitbook/assets/figure2.png" alt=""><figcaption><p><a href="https://jolt.a16zcrypto.com/how/architecture.html">https://jolt.a16zcrypto.com/how/architecture.html</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/fetch_decode_execute (2).png" alt=""><figcaption><p><a href="https://jolt.a16zcrypto.com/how/architecture.html">https://jolt.a16zcrypto.com/how/architecture.html</a></p></figcaption></figure>

[アーキテクチャに関する詳細はこちら](https://jolt.a16zcrypto.com/how/architecture.html)を参考にしてください。

Joltの一番の工夫は命令のルックアップテーブルを分解可能(decomposable)にし、サブテーブルに分割したことです。これにより、命令のサイズにかかわらずテーブルのサイズを一定にすることが可能になりました。

[https://a16zcrypto.com/posts/article/building-on-lasso-and-jolt/](https://a16zcrypto.com/posts/article/building-on-lasso-and-jolt/)

### Joltの実装

ソースコードをビルドしてローカルでSHA-3を実行してみたところ、実行時間はsetupが20s、proveが2s、verifyが0.5s程度でした。メモリ利用料は3GBほどでした。setupでの実行内容にはcommitmentサイズを定義、preprocess、commitment schemeのsetupなどがあり、ほとんどの時間はcommitment schemeのsetupに費やされていました。

[ブログによれば](https://a16zcrypto.com/posts/article/faqs-on-jolts-initial-implementation/)、実行時間のうち1/5はコミットメントの計算に費やされ、残りの半分はSumcheck Protocolに費やされます。そして残りの時間は、メモリ割り当て、witness生成、メモリのシリアル化などのさまざまなタスクに分散されます。

Joltは現在は[32bitの整数にのみ対応](https://a16zcrypto.com/posts/article/building-jolt/)しています。論文では浮動小数点の手法も紹介されています。

また、[ブログによると](https://a16zcrypto.com/posts/article/building-jolt/)Joltはオーバーヘッドが 6 桁未満で、RISC-ZeroやSP1(8コア)よりも高速です。

<figure><img src="../../.gitbook/assets/zkVM-Proving-Time-vs-Jolt.svg" alt=""><figcaption><p><a href="https://a16zcrypto.com/posts/article/building-jolt/">https://a16zcrypto.com/posts/article/building-jolt/</a></p></figcaption></figure>

### Joltの今後の開発ロードマップ

[https://jolt.a16zcrypto.com/tasks.html](https://jolt.a16zcrypto.com/tasks.html)

* コミットメントスキームのBiniusへの移行
  * より小さいフィールドサイズでのコミットメント実行が可能となる
  * GitHubではすでに実装している
* プリコンパイル
  * keccakなどのEVMでよく使われる機能をプリコンパイルとして提供し、実行を高速化させる
* GPUによる高速化
  * [https://a16zcrypto.com/posts/article/faqs-on-jolts-initial-implementation/](https://a16zcrypto.com/posts/article/faqs-on-jolts-initial-implementation/)
* Recursive/Folding
  * [https://a16zcrypto.com/posts/article/faqs-on-jolts-initial-implementation/](https://a16zcrypto.com/posts/article/faqs-on-jolts-initial-implementation/)
* ...

### 参考文献

* [https://eprint.iacr.org/2023/1217](https://eprint.iacr.org/2023/1217)
* [https://jolt.a16zcrypto.com/](https://jolt.a16zcrypto.com/)
* [https://a16zcrypto.com/posts/article/faqs-on-jolts-initial-implementation/](https://a16zcrypto.com/posts/article/faqs-on-jolts-initial-implementation/)
* [https://a16zcrypto.com/posts/article/building-on-lasso-and-jolt/](https://a16zcrypto.com/posts/article/building-on-lasso-and-jolt/)
* [https://github.com/a16z/jolt](https://github.com/a16z/jolt?tab=readme-ov-file)
