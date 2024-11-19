# 算術回路

算術回路は、NP 問題をモデル化するための方程式のシステムであり、ブール回路と並んで利用されます。一般にブール回路では基本的な算術操作（加算や乗算）が煩雑になりがちです。
例えば、10 進数の`10`をブール回路で扱うには 2 進数に変換して`1010`と表記する必要があり、これを計算に利用する際には 4 つの入力が必要です。

さらに、単純な足し算を AND, OR, NOT だけで表すには、回路の表現が非常に長くなります。

そのため、算術回路が代替手段として提案されています。算術回路は加算、乗算、等式のみを使ってシステムを構築し、与えられた入力が正しいかどうかをチェックします

以下は算術回路の例です。

$$
\begin{aligned}
7 &= a + b \\
10 &= a \cdot b
\end{aligned}
$$

この回路は、$$ a = 2, b = 5 $$ のときに満たされます（どちらの方程式も成立）。一方で、$$ a = 1, b = 2 $$ のときには満たされません。

つまり、算術回路は「方程式の集まり」として考えることができます。

注意点としては、この方程式は解を求めているのではなく、入力が正しいかどうかだけを確認していることに留意してください。

## ブール回路との違い

| **項目**         | **算術回路**                                                                             | **Boolean 回路**                                                                                       |
| ---------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **表現力**       | 数値計算（加算、乗算、多項式評価など）に特化し、数値演算を効率的に表現可能。             | 論理演算に特化し、論理式や条件分岐の表現に適している。                                                 |
| **計算の対象**   | 実数や整数を扱うため、数値データの処理が容易。                                           | ビット（0, 1）を扱うため、真理値や離散データの処理に適している。                                       |
| **ゲートの種類** | 加算ゲート、乗算ゲートが中心。場合によっては定数ゲートも含む。                           | AND、OR、NOT などの論理ゲートが基本。                                                                  |
| **構成の複雑さ** | 設計が比較的シンプルで、多項式の評価に適しているが、条件分岐や論理計算の表現には不向き。 | 条件分岐や複雑な論理を直接的に表現できるが、多くのビット演算を必要とする計算（例: 数値演算）は非効率。 |

## Circom

算術回路を表現するプログラミング言語 Circom について解説します。

Circom では回路で扱う変数を Signal と呼び、等価であることを表すのに`===`を用います。

例えば、`a`が 3 と等価であることを表すのには以下のように表記します。

```text
a === 3
```

これは assert(a, 3)とチェックしていると同義です。 この場合、a は 3 以外の解はありませんが、以下を満たす変数の組み合わせは無限に存在します。（Circom 上では有限体上の計算となるので、有限個の組み合わせになります）

```
a === b + c
```

## ブール回路を算術回路に変換

ブール回路を算術回路で表現するには、まずは 0,1 の入力のみを受け付けるようにします。

```
A * (A - 1) === 0
B * (B - 1) === 0
```

これにより、`A` と `B` が 0 または 1 以外の値を取らないように制約を追加します。

AND, OR, NOT ゲートに関して、それぞれ次のように表すことができます。（`A`や`B`に 0 もしくは 1 を代入してチェックしてみてください）

```
AND_result === A * B
OR_result === A + B - A * B
NOT_result === 1 - A // もしくは1 - B
```

これにより任意のブール回路を算術回路に変換することができることが示せました。
任意の NP 問題はブール回路で表現できるので、これはつまり任意の NP 問題は算術回路で表せることを示しています。