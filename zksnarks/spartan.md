# Spartan

SpartanはMultilinear-IOPベースのzkSNARKです。SumCheckProtocol、Polynomial Commitment、Computation Commitmentの3つの要素から成り立っています。

SpartanはProver Timeが高速で、少ない制約ではVerifier Timeも高速です。

<figure><img src="../.gitbook/assets/スクリーンショット 2024-10-31 18.07.32.png" alt=""><figcaption></figcaption></figure>

### SumCheck Protocol

ある多線形多項式Gのすべての点に対する評価の合計を証明します。

$$
T = \sum _{x \in \{0,1\}^l} G(x_1,x_2,...,x_l)
$$

この合計を効率的に証明するのがSumCheck Protocolです。

### R1CSインスタンスのSumCheckインスタンスへのエンコード

R1CSインスタンスとは、次元mの行列A, B, Cに対して以下のようなzが存在するという関係を示すインスタンス {A, B, C, z}のことです。

$$
Az \circ Bz = Cz
$$

これをSumCheckインスタンスに変換しようとすると以下のようになります。(s=logm)

$$
0 = \sum _{x \in \{0,1\}^l}  eq(x, \tau) \cdot F(x)
$$

where

$$
F(x) =  \sum _{x \in \{0,1\}^s} \tilde{A}(x,y) \cdot \tilde{z}(y) *   \sum _{x \in \{0,1\}^s} \tilde{B}(x,y) \cdot \tilde{z}(y) -  \sum _{x \in \{0,1\}^s} \tilde{C}(x,y) \cdot \tilde{z}(y)
$$

### SPARK

ただ、A, B, Cは行列なので、これを変換しようとするとO(m^2)のコストがかかります。しかし、A, B, Cはほとんどの要素が0となるスパース行列です。なので、これを3つのDense Polynomialに分解することができます。このDense表現に対してコミットメントを適用することで、計算量を減らすことができます。

<figure><img src="../.gitbook/assets/スクリーンショット 2024-10-31 18.24.54.png" alt=""><figcaption></figcaption></figure>

### References

* [https://eprint.iacr.org/2019/550](https://eprint.iacr.org/2019/550)
* [https://github.com/microsoft/Spartan](https://github.com/microsoft/Spartan)
* [https://youtu.be/FPQs7T7f\_AU?si=x-YwrbS2fXFN1l-H](https://youtu.be/FPQs7T7f\_AU?si=x-YwrbS2fXFN1l-H)
