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
2. 選択した長さのランダムな鍵Kを生成する
3. 鍵拡張アルゴリズムを使用して、ラウンド鍵K\_0, K\_1, ..., K\_rを生成する （rは総ラウンド数：128ビット鍵で10、192ビット鍵で12、256ビット鍵で14）

</details>

<details>

<summary> 暗号化</summary>

1. 平文Mを128ビット（16バイト）のブロックに分割する
2. 各ブロックに対して以下の操作を行う：
   1. AddRoundKey: ブロックとK\_0をXOR
   2. r-1回の主ラウンドを実行:
      1. SubBytes: 各バイトをS-boxを用いて置換
      2. ShiftRows: 行ごとに左循環シフト
      3. MixColumns: 列ごとに線形変換
      4. AddRoundKey: 状態とK\_iをXOR
   3. 最終ラウンド:
      1. SubBytes
      2. ShiftRows
      3. AddRoundKey（K\_rを使用）
3. 暗号文Cを出力

</details>

<details>

<summary>復号化</summary>

1. 暗号文Cを128ビット（16バイト）のブロックに分割する
2. 各ブロックに対して以下の操作を行う：
   1. AddRoundKey: ブロックとK\_rをXOR
   2. r-1回の逆主ラウンドを実行:
      1. InvShiftRows: 行ごとに右循環シフト
      2. InvSubBytes: 各バイトを逆S-boxを用いて置換
      3. AddRoundKey: 状態とK\_iをXOR
      4. InvMixColumns: 列ごとに逆線形変換
   3. 最終ラウンド:
      1. InvShiftRows
      2. InvSubBytes
      3. AddRoundKey（K\_0を使用）
3. 平文Mを出力

</details>

<details>

<summary>注意点</summary>

* AESは有限体GF(2^8)上で動作する
* SubBytesステップでは、バイトの乗法逆元を求めた後にアフィン変換を適用する
* MixColumnsステップでは、固定の多項式との乗算を行う
* 鍵拡張アルゴリズムは、元の鍵から各ラウンドで使用する鍵を生成する複雑なプロセスを含む

</details>

### RSA (Rivest Shamir Adleman)

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

## デジタル署名 (Digital Signature)

### Schnorr署名 (Schnorr Signature)

## 離散対数に基づく公開鍵暗号学

### Diffie-Hellman 鍵交換 (Diffie-Hellman Key Exchange) [Anonymous Anonymous](https://app.gitbook.com/u/M0Ag2PM8qphSBEuVlTmprHj1RQ93 "mention")



### Elgamal暗号化 (Elgamal Encryption)

## コミットメント (Cryptographic Commitment)

### Pedersenコミットメント (Pedersen Commitment)

## 楕円曲線暗号 (Elliptic Curve Cryptography) [Anonymous Anonymous](https://app.gitbook.com/u/M0Ag2PM8qphSBEuVlTmprHj1RQ93 "mention")

### ECDSA (Elliptic Curve Digital Signature Algorithm) [Anonymous Anonymous](https://app.gitbook.com/u/M0Ag2PM8qphSBEuVlTmprHj1RQ93 "mention")

### BLS署名 (Boneh-Lynn-Shacham Signature)

## ペアリング暗号 (Pairing Based Cryptography)

