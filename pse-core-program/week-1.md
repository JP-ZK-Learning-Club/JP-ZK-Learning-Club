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

AES（Advanced Encryption Standard）は、2001年にアメリカ国立標準技術研究所（NIST）によって標準化された、現在最も広く使用されている対称暗号アルゴリズムです。AESは、以前の標準であったDES（Data Encryption Standard）の後継として開発されました。

AESの主な特徴は以下の通りです：

1. ブロック暗号方式：データを128ビット（16バイト）の固定サイズのブロックに分けて処理します。
2. 可変鍵長：128、192、256ビットの鍵長をサポートしています。
3. ラウンド数：鍵長に応じて10（128ビット）、12（192ビット）、14（256ビット）ラウンドの処理を行います。
4. バイト単位の操作：AESはビット単位ではなく、バイト単位で操作を行います。

**AESの仕組み**

AESの暗号化プロセスは、一連の変換操作を繰り返すことで行われます。各ステップについて詳しく説明しましょう。

1. **初期ラウンド鍵加算（Add Round Key）**：
   * 平文ブロック（16バイト）と最初のラウンド鍵をXOR演算します。
2. **メインラウンド**：以下の4つの操作を繰り返します。
   1. **SubBytes**：
      * 各バイトを別の値に置き換えます（S-box使用）。
      * これにより、非線形性が導入され、線形解読攻撃や差分解読攻撃への耐性が高まります。
   2. **ShiftRows**：
      * 2行目を1バイト左へ、3行目を2バイト左へ、4行目を3バイト左へシフトします。
      * この操作により、バイト間の依存関係が生まれます。
   3. **MixColumns**：
      * 各列に対して線形変換を適用します。
      * この操作により、1バイトの変化が4バイトに影響を与えるため、強力な拡散効果が得られます。
   4. **AddRoundKey**：
      * 現在の状態とラウンド鍵をXOR演算します。
3. **最終ラウンド**：
   * MixColumns以外の操作（SubBytes、ShiftRows、AddRoundKey）を行います。

これらの操作により、強力な拡散（diffusion）と混乱（confusion）が実現され、暗号文の安全性が高められます。

**数学的背景**

AESは有限体 GF(2^8) 上で動作します。これは、8ビットのバイトを多項式として扱い、演算を行うことを意味します。この数学的構造により、効率的でありながら暗号学的に強力な操作が可能になっています。

例えば、SubBytes操作では、各バイトに対して有限体上の逆元を求め、さらにアフィン変換を適用します。これにより、線形暗号解読や差分解読に対する耐性が高まります。

**AESの強み**

* **強力な拡散と混乱**：各ラウンドの操作により、平文の小さな変化が暗号文全体に大きく影響します。
* **既知の攻撃に対する耐性**：現在知られている攻撃方法に対して十分な安全性を持っています。
* **広く採用され信頼されている**：多くの政府機関や企業で標準的に使用されています。
* **効率的な実装**：ハードウェアおよびソフトウェアの両方で効率的に実装できます。
* **鍵長の選択肢**：128、192、256ビットの鍵長をサポートしており、必要なセキュリティレベルに応じて選択できます。

**AESの応用例**

* **無線通信のセキュリティ**：Wi-Fiネットワークでは、ルーターとクライアントの認証にAESが使用されています。
* **ウェブブラウジングの暗号化**：SSL/TLS暗号化プロトコルの一部としてAESが使用され、ウェブサイトのサーバー認証を安全に行っています。
* **ファイル暗号化**：個人や企業が機密文書、写真、法的文書などを安全に保管・送信する際にAESが使用されます。
* **プロセッサーセキュリティ**：多くのプロセッサメーカーが、ハードウェアレベルの暗号化にAESを採用し、セキュリティを強化しています。
* **政府機関のデータ保護**：多くの政府機関が機密情報の保護にAESを使用しています。

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

