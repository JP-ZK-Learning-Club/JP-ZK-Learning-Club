---
description: >-
  参照先:
  https://github.com/privacy-scaling-explorations/core-program/blob/main/2024/week1_cryptographic_basics.md
---

# Week 1

## 対称暗号と非対称暗号 (Symmetric & Asymmetric Cryptography)

対称暗号化アルゴリズムは、暗号化機能と復号化機能の両方を実行するために同じ鍵を使用します。

非対称暗号化アルゴリズムは、暗号化に使用される鍵は公開鍵と呼び、復号化に使用される鍵を秘密鍵と呼びます。

公開鍵と暗号鍵は、数学的の関係性を持ちます。

### AES (Advanced Encryption System)

AESは対称暗号の一種。鍵生成、暗号化、復号化の三つのアルゴリズムで定義される。

例：Aliceは機密データをBobに安全に送信したい。

<details>

<summary>鍵生成</summary>

1. Aliceは鍵長を選択する（128, 192, または 256ビット）
2. 選択した長さのランダムな鍵$$K$$を生成する
3. 鍵拡張アルゴリズムを使用して、ラウンド鍵$$K_0, K_1, ..., K_r$$を生成する （$$r$$は総ラウンド数：128ビット鍵で10、192ビット鍵で12、256ビット鍵で14）

</details>

<details>

<summary> 暗号化</summary>

1. 平文$$M$$を128ビット（16バイト）のブロックに分割する
2. 各ブロックに対して以下の操作を行う：
   1. AddRoundKey: ブロックと$$K_0$$を$$XOR$$
   2. $$r-1$$回の主ラウンドを実行:
      1. SubBytes: 各バイトをS-boxを用いて置換
      2. ShiftRows: 行ごとに左循環シフト
      3. MixColumns: 列ごとに線形変換
      4. AddRoundKey: 状態と$$K_i$$を$$XOR$$
   3. 最終ラウンド:
      1. SubBytes
      2. ShiftRows
      3. AddRoundKey（$$K_r$$を使用）
3. 暗号文$$C$$を出力

</details>

<details>

<summary>復号化</summary>

1. 暗号文$$C$$を128ビット（16バイト）のブロックに分割する
2. 各ブロックに対して以下の操作を行う：
   1. AddRoundKey: ブロックと$$K_r$$を$$XOR$$
   2. $$r-1$$回の逆主ラウンドを実行:
      1. InvShiftRows: 行ごとに右循環シフト
      2. InvSubBytes: 各バイトを逆S-boxを用いて置換
      3. AddRoundKey: 状態と$$K_i$$を$$XOR$$
      4. InvMixColumns: 列ごとに逆線形変換
   3. 最終ラウンド:
      1. InvShiftRows
      2. InvSubBytes
      3. AddRoundKey（$$K_0$$を使用）
3. 平文$$M$$を出力

</details>

<details>

<summary>注意点</summary>

* AESは有限体$$GF(2^8)$$上で動作する
* SubBytesステップでは、バイトの乗法逆元を求めた後にアフィン変換を適用
* MixColumnsステップでは、固定の多項式との乗算を行う
* 鍵拡張アルゴリズムは、元の鍵から各ラウンドで使用する鍵を生成する複雑なプロセスを含む

</details>

### 非対称暗号: RSA (Rivest Shamir Adleman)

RSAは非対称暗号の一種。鍵生成、鍵共有、暗号化、復号三つのアルゴリズムで定義される。

例：BobはAliceに、危険な環境で安全に秘密を伝えたい。

<details>

<summary>鍵生成（暗号鍵、公開鍵）</summary>

1. Aliceは $$p, q$$ 二つ素数をランダムに選び、下記二点を計算する
   1. &#x20;$$n = p \cdot q$$
   2. $$\lambda(n) =lcm(p -1,q-1)$$ ([カーマイケル関数](https://tjkendev.github.io/procon-library/python/prime/carmichael-function.html))
2. $$1<e<\lambda(n)$$、そして $$gcd(e, \lambda(n))=1$$を満たす公開鍵$$e$$を選択する
3. $$d \equiv e^{-1} \pmod{\lambda(n)}$$を満たす暗号鍵 $$d$$を選択する ([拡張ユークリッド互除法](https://ja.wikipedia.org/wiki/%E3%83%A6%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%83%89%E3%81%AE%E4%BA%92%E9%99%A4%E6%B3%95))
4. Aliceは $$d, p, q,\lambda(n)$$を秘密鍵として保管し、$$(n, e)$$を公開鍵として公開する

</details>

<details>

<summary>暗号化</summary>

1. BobはAliceから共有された公開鍵 $$(n, e)$$を取得する
2. 共有したい平文$$M$$を整数$$m$$に変換し、$$0 < m < n$$だと確認する（ $$m > n$$の場合、メッセージが $$m \pmod n$$に削減される）
3. $$c = m^e \pmod n$$を計算する
4. 暗号文$$c$$をAliceに送る

</details>

<details>

<summary>復号化</summary>

1. Aliceは暗号文$$c$$を取得する
2. $$c^d \equiv (m^e)^d \equiv m \pmod n$$を計算する
3. $$m$$から平文$$M$$に復元する

</details>

## ハッシュ関数 (Hash Function)

### SHA256 (Secure Hash Algorithm)

### マークルツリー (Merkle Tree)

### デジタル署名 (Digital Signature): Schnorr署名 (Schnorr Signature)

デジタル署名とは、非対称暗号を利用し、電子文書に対し暗号を用いた署名を付与することで、その電子文書が作成名義人によって作成されたこと、そして作成されて以降が改変されていないことを証明する技術です。

例: AliceはBobへあるメッセージ $$m$$ を送信し、そのメッセージが自分から作成し、改変されていないことを証明したい。

<details>

<summary>プロトコル</summary>

#### Aliceの事前準備

1. 大きな素数 $$p$$、そして$$p-1$$を割り切れる素数 $$q$$
2. 秘密鍵 $$x$$ を $$0 < x < q$$ から選択する
3. 公開鍵 $$y = g^x \pmod p$$ を作成する
4. $$q$$を法とする整数乗法群$$\mathbb{Z}_p^*$$
5. ハッシュ関数 $$H$$

#### Aliceはメッセージ $$m$$ に署名する

1. nonce $$k$$を $$0 < k < p -1$$から選択する
2. $$r=g^k \pmod p$$ を計算する
3. $$m$$ と $$r$$ を結合し、 $$e=H(m||r)$$を計算する
4. $$s=k-xe \pmod q$$を計算する
5. $$(s,e,m)$$をBobへ送信する

#### Bobは $$(s,e)$$に基づいて $$m$$ を検証する

1. $$\begin{align} r' &= g^sy^e \pmod p \\ &= g^{k-xe}g^{xe} \pmod p \\ &= g^{k-xe+xe} \pmod p \\ &= g^k \pmod p \end{align}$$
2. $$e'=H(m||r)$$を計算する

$$e' ==e$$ であれば、Bobはメッセージ $$m$$ はAliceが作成し、改変されず着信したことを証明できたとみなす。

</details>

### 離散対数に基づく公開鍵暗号学: Diffie-Hellman 鍵交換 (Diffie-Hellman Key Exchange)

AliceとBobの間、事前の秘密の共有無しに、盗聴の可能性のある通信路を使って、共通暗号鍵を構築する方法です。共通公開鍵を生成する過程までは非対称暗号を利用し、その後の交流は共通暗号鍵に基づく対照暗号を利用します。

<details>

<summary>プロトコル</summary>

#### 事前準備

1. 大きな素数 $$p$$
2. 整数乗法群$$\mathbb{Z}_p^*$$、生成元とする$$g$$と$$h \in \mathbb{Z}_p^*$$

#### 鍵生成

1. Aliceは秘密鍵 $$a$$ を $$1 < a < p-1$$からランダムに選択する
2. Bobは秘密鍵 $$b$$ を $$1 < b < p-1$$からランダムに選択する

#### 公開値を計算する

1. Aliceは公開鍵 $$A=g^a \pmod p$$を計算し、Bobへ送信する
2. Bobは公開鍵 $$B=g^b \pmod p$$を計算し、Aliceへ送信する

#### お互いローカルで共通秘密鍵を計算する

1. Aliceは共通秘密鍵 $$s=B^a \pmod p$$ を計算する
2. Bobは共通秘密鍵 $$s=A^b \pmod p$$ を計算する

そしてAliceとBobは共通秘密鍵$$s$$を共有することをできました

</details>

### Elgamal暗号化 (Elgamal Encryption)

## コミットメント (Cryptographic Commitment)

Cryptographic Commitmentは、ある値を選択し、その選択を**固定**しつつ、後で公開するまでその値を秘密にしておく方法です。簡単に言えば、デジタルの世界での**封筒**のようなものです。

コミットメントには、主に2つの重要な特性があります。

* **Binding（拘束性）**: 一度コミットした値を後から変更することはできません。これは、封筒に入れた手紙を後から書き換えられないのと同じです。&#x20;
* **Hiding（隠蔽性）**: コミットメントを受け取った人は、それが開示されるまで中身を知ることができません。これは、封筒を開けるまで中身が分からないのと同じです。

コミットメントの分かりやすい例として、AliceとBobがオンラインでコイントスをするアプリケーションを考えてみましょう。

1. アリスがコインの表裏を予想し、その予想をコミットメントとして送信します。
2. ボブがコインを投げ、結果を発表します。
3. アリスが自分の予想を公開し、コミットメントを開示します。
4. ボブはアリスの予想がコミットメントと一致するか確認します。

これにより、お互いに不正ができない公平なコイントスが実現できます。

Cryptographic Commitmentは、ゼロ知識証明においても重要な役割を果たします。Prover（証明者）は、証明の一部をコミットメントとして事前に提示し、後でVerifier（検証者）の選択に応じて必要な情報だけを開示することができます。

また、Lamport署名方式など、一部のデジタル署名システムでもCryptographic Commitmentが使用されています。署名者は秘密の値にコミットし、後で部分的に公開することで署名を生成します。

さらに、MPC（マルチパーティ計算）においてもCryptographic Commitmentは重要な役割を果たしています。

* **入力の確定**: 参加者は自身の入力にコミットすることで、計算途中で入力を変更できないようにします。
* **計算の公平性**: 全ての参加者が入力を提供した後にのみ計算が進行することを保証します。
* **検証可能性**: 計算過程や結果の正当性を検証する際に、コミットメントが重要な役割を果たします。

### Pedersenコミットメント (Pedersen Commitment)

Cryptographic Commitmentを実現する高度なコミットメントスキームの一つとしてPedersen Commitmentがあります。

Pedersen Commitmentは以下のような手順で機能します：

1. **セットアップ**:
   * 大きな素数位数$$q$$を持つ群$$G$$を選びます。
   * その群の中から二つの生成元$$g, h$$をランダムに選びます。
   * $$G, q, g, h$$は公開パラメータとなります。
2. **コミットメントの生成**:
   * コミットしたい値$$m$$と、ランダムな値$$r$$（$$0$$から$$q-1$$の範囲）を選びます。
   * コミットメント$$C = g^m \times h^r$$を計算します。
   * $$C$$を公開します。
3. **コミットメントの開示と検証**:
   * 後で、$$m$$と$$r$$を公開します。
   * 検証者は$$C == g^m \times h^r$$を確認します。

また、Pedersen Commitmentには、以下のような特徴があります：

* **Binding（拘束性）**: コミットメントを生成した後、異なる値$$m'$$と$$r'$$で同じ$$C$$を生成することは計算量的に不可能です。
* **Hiding（隠蔽性）**: コミットメント$$C$$から$$m$$の情報を得ることは、離散対数問題を解くのと同じくらい困難です。
* **準同型性**: 二つのコミットメント$$C1 = g^{m1} \times h^{r1}$$と$$C2 = g^{m2} \times h^{r2}$$があるとき、$$C1 \times C2 = g^{m1+m2} \times h^{r1+r2}$$となります。これは、コミットメントを開示せずに加算操作ができることを意味します。

ハッシュベースのコミットメントスキーム（例：$$C = H(m || r)$$）と比較すると、Pedersen Commitmentには以下のような利点があります：

1. **無条件の隠蔽性**: 計算能力に関係なく、$$C$$から$$m$$の情報を得ることは不可能です。
2. **準同型性**: コミットメントを開示せずに、加算などの演算が可能です。

一方で、Pedersen Commitmentは離散対数問題の難しさに依存しているため、将来的に量子コンピュータなどでこの問題が解決されると、Binding性が失われる可能性があります。

## 楕円曲線暗号 (Elliptic Curve Cryptography)

楕円曲線暗号は、有限体上の楕円曲線の整数点の集合をアーベル群として利用した非対称暗号です。

RSAと比べると、同じビット数のセキュリティーに対して、鍵の大きさが小さいメリットがあり、計算の効率化が重視されています。

### ECDSA (Elliptic Curve Digital Signature Algorithm)

ECDSAは楕円曲線暗号を

### BLS署名 (Boneh-Lynn-Shacham Signature)

## ペアリング暗号 (Pairing Based Cryptography)

