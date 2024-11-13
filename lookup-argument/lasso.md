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
4. メモリ一貫性チェックプロトコルを実行する

Primary SumCheckでは、上記の証明をEi(r)=viの小さな証明に落とし込むために、事前に上記を証明してしまいます。viはSumCheckの最後のラウンドに得られた値です。

しかし、そうするとviが本当にテーブルからルックアップされた値であることを保証できません。なので、それを証明するために追加でメモリ一貫性チェックを実行しています。

### メモリ一貫性チェック

メモリにおけるI∪W = R∪Fを証明します。Iは初期の集合、Wは書き込みの集合、Rは読み取りの集合、Fは終了時の集合です。

Lassoではフィンガープリント(ハッシュのようなもの)を利用してこれを効率的に証明します。ある値t,v,aフィンガープリントは以下のように計算されます。これはReed-solomonフィンガープリントと呼ばれます。

$$
h_{\gamma}(t,v,a)= \gamma ^2 \cdot a + \gamma \cdot v + t
$$

もしt,v,aがベクトルのときは、このようにフィンガープリントの積を計算します。

$$
H_{\gamma , \tau}(a,v,t)= \Pi _{(a,v,t) \in s} (h_{\gamma}(a,v,t) - \tau)
$$

このフィンガープリントを用いることでI∪W = R∪Fを効率的に証明します。

$$
H_{\gamma , \tau}(I) \cdot H_{\gamma , \tau}(W) = H_{\gamma , \tau}(R) \cdot H_{\gamma , \tau}(F)
$$

ひとつのマルチセットフィンガープリントHに注目すると、フィンガープリントhのバイナリツリーのような乗算回路として表現できます。この乗算回路の証明は[Thaler13で提案された効率的な回路評価](https://eprint.iacr.org/2013/351.pdf)を用いて証明できます。

<figure><img src="../.gitbook/assets/スクリーンショット 2024-11-07 午後9.46.43.png" alt=""><figcaption></figcaption></figure>

多重線形拡張に直すと、R, W, I, Fは以下のようになります。

<figure><img src="../.gitbook/assets/スクリーンショット 2024-11-07 午後9.49.29.png" alt=""><figcaption></figcaption></figure>

### Reference

* [https://youtu.be/iDcXj9Vx3zY?si=XECewMzri4gh\_Ama](https://youtu.be/iDcXj9Vx3zY?si=XECewMzri4gh\_Ama)
* [https://eprint.iacr.org/2023/1216](https://eprint.iacr.org/2023/1216)
