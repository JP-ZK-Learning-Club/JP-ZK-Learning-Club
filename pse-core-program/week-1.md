---
description: >-
  参照先:
  https://github.com/privacy-scaling-explorations/core-program/blob/main/2024/week1_cryptographic_basics.md
---

# Week 1

### 対称暗号と非対称暗号 (Symmetric & Asymmetric Cryptography)

対称暗号化アルゴリズムは、暗号化機能と復号化機能の両方を実行するために同じ鍵を使用します。

非対称暗号化アルゴリズムは、暗号化に使用される鍵は公開鍵と呼び、復号化に使用される鍵を秘密鍵と呼びます。

公開鍵と暗号鍵は、数学的の関係性を持ちます。

### AES (Advanced Encryption System)

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

