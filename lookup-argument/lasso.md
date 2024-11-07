# Lasso

Lassoは、ルックアップテーブルTをいくつかの小さなサブテーブルに分解し、サブテーブルのみからルックアップを構築して結果を再構成することで、従来課題だった「テーブルサイズが肥大する」という課題を解決しました。

### Indexed Lookup Argument

LassoではIndexed Lookup Tableをサポートします。インデックスbをテーブルTの中から検索し、結果aを取得します:

$$
a_i = T[b_i]
$$

このインデックスはスパース行列Mとして表されます。

<figure><img src="../.gitbook/assets/スクリーンショット 2024-11-07 午後7.20.18.png" alt=""><figcaption><p><a href="https://youtu.be/iDcXj9Vx3zY?si=XECewMzri4gh_Ama">https://youtu.be/iDcXj9Vx3zY?si=XECewMzri4gh_Ama</a></p></figcaption></figure>

Lassoでは、結果aが正しいことをSumCheckプロトコルによって証明します。

Verifierがランダムにピックアップしたrに対して以下の式を証明します。

\~は多重線形拡張を表します。

$$
\sum _{y \in \{ 0,1 \}^{log N}} \tilde{M}(r,y) \cdot \tilde{T}(y)=\tilde{a}(r)
$$

これはブールハイパーキューブ上で評価することで計算量を削減することができます。

$$
\sum _{j \in \{ 0,1 \}^{log s}} \tilde{eq}(j,r) \cdot T[nz(j)]=\tilde{a}(r)
$$

nzはインデックス行列Mに対してこのように対応します。スパース行列を小さな要素のベクトルで表現しています。(Spartanのアイデアに近い)

<figure><img src="../.gitbook/assets/スクリーンショット 2024-11-07 午後7.45.09.png" alt=""><figcaption></figcaption></figure>

### Table Decomposition

さらに、テーブルサイズを効率的に表現するために、テーブルTをc個のサブテーブルT1, ..., Tcに分割します。そうすると、証明すべきことはこのようになります。gはサブテーブルを合成するための関数です。ADDやMULのようなオペレーションによって定義されています。

$$
\sum _{j \in \{ 0,1 \}^{log s}} \tilde{eq}(j,r) \cdot g(T_1[nz_1(j)], ..., T_c[nz_c(j)]) =\tilde{a}(r)
$$

そうすると、コミットメントすべき値のサイズもN^{1/c}になるため、コミットメントコストを小さくすることができます。最終的に、このnzを多重線形表現に直したEi(j)を用いて以下のように定義されます。

$$
\sum _{j \in \{ 0,1 \}^{log s}} \tilde{eq}(j,r) \cdot g(E_1(j), ..., E_c(j)) =\tilde{a}(r)
$$

### Lassoプロトコル

上記を実際に証明するには以下のステップを必要とします。

1. ProverがVerifierにE, read\_cts, final\_cts, nzのコミットメントを送る
   1. ctsはメモリチェックで使われるcounter polynomial
2. VerifierがProverにlog(s)個の乱数を送る
3. Primary SumCheckプロトコルを実行する
4. Memory-consistency Checkプロトコルを実行する

Primary SumCheckでは、上記の証明をEi(r)=viの小さな証明に落とし込むために、事前に上記を証明してしまいます。viはSumCheckの最後のラウンドに得られた値です。

しかし、そうするとviが本当にテーブルからルックアップされた値であることを保証できません。なので、それを証明するために追加でMemory-consistency Checkを実行しています。

### Reference

* [https://youtu.be/iDcXj9Vx3zY?si=XECewMzri4gh\_Ama](https://youtu.be/iDcXj9Vx3zY?si=XECewMzri4gh\_Ama)
* [https://eprint.iacr.org/2023/1216](https://eprint.iacr.org/2023/1216)
