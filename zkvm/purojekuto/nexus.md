# Nexus

Nexusは簡潔な証明圧縮を備えた並列化されたSuperNova-HyperNova-CycleFoldマシンです。

#### Nexus Virtual Machine (NVM)

Nexusは上記の並列処理に適した命令セットアーキテクチャで構成された仮想マシンを有しています。基本的にはvnTinyRAMとRISC-Vからインスピレーションを得たシンプルかつ最小限なCPU、メモリ、I/Oの構成を用いており、証明者のパフォーマンスを最大化するための工夫がされています。

CPUサイクルFに対し、(1)実行 (2)IVC (3)圧縮 の3つのステップで実行結果yと証明πを返します。圧縮ステップの最後に証明を生成するため、非常に効率的です。

#### zkVM co-processors

さらに、zkVM co-processorsと呼ばれるEVMプリコンパイルに似た拡張機能を有しており、開発者は自身でCCS回路を定義してNVM命令セットを拡張することができます。

たとえば、SHA-256ハッシュの計算は、zkVMで証明しようとすると64,000CPUサイクルが必要となり、それはASICなどの手動で作成された回路では30,000CPUサイクルに落ち着きます。

zkVM co-processorsはこの中間を取るアプローチで、ゲストプログラムとして事前定義された回路を実行します。

このzkVM co-processorsによって、SHA-2、ECDSA署名、行列の乗算、平方根の計算などの演算を高速化することが期待されます。

#### CCSインスタンスの変換

customizable constraint system(CCS)に対し、下記のステップによって変換を施した後に証明を行います。

1. マルチフォールディングスキームを適用する
2. CycleFold変換を適用する
3. Fiat-Shamir変換を適用する
4. SuperNova non-uniform IVC変換を適用する
5. 並列化を適用する

#### 証明生成

証明時は3つのステップに分かれます。

1. IVC証明を作成\
   (1)マシン状態の初期化 (2)実行トレースの生成 (3)IVC証明を計算
2. 証明の圧縮\
   (1)マシンアウトプットを抽出 (2)IVC検証者の実行のためのinstance-witnessペアを計算 (3)圧縮されたzkSNARK証明を生成
3. 出力: 最終証明、instance、出力を返す

#### 参考文献

* [https://whitepaper.nexus.xyz/](https://whitepaper.nexus.xyz/)
* [https://github.com/nexus-xyz/nexus-zkvm](https://github.com/nexus-xyz/nexus-zkvm)
