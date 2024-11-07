# 概要とLasso以前の提案

Lookup Argumentは、事前に計算されたテーブルから値を参照することで計算コストを削減する手法です。

SHA-256回路などは、R1CSにすると2^20もの回路サイズになってしまいますが、テーブルに保存することで回路サイズを小さくすることができます。

Lookup Argumentでは、あるベクトルaがテーブルTの中に存在することを証明することで、計算結果が正しいことを証明します。

### Lookup Argumentの分類

<table><thead><tr><th width="188">Lookup Argument</th><th>特徴</th><th>証明者の計算コスト</th></tr></thead><tbody><tr><td><a href="https://discovery.ucl.ac.uk/id/eprint/10063978/1/ZKRAM-Asiacrypt2018-Final.pdf">Arya</a></td><td></td><td>m+2N*log(m)</td></tr><tr><td><a href="https://eprint.iacr.org/2020/315.pdf">plookup</a></td><td>Plonk-IOPに適したLookup</td><td>5 * max(m, N)</td></tr><tr><td><a href="https://eprint.iacr.org/2022/1355">HyperPlonk</a></td><td>Plonkをブールハイパーキューブに適応させた</td><td>4 * max(m, N)</td></tr><tr><td><a href="https://eprint.iacr.org/2022/621">Caulk</a></td><td>計算コストが線形以下</td><td>O(m^2 + m*log(N))</td></tr><tr><td><a href="https://eprint.iacr.org/2022/957">Caulk+</a></td><td>Caulkのサブプロトコルを多項式除算チェックに置き換えた</td><td>O(m^2)</td></tr><tr><td><a href="https://eprint.iacr.org/2022/1447">flookup</a></td><td></td><td>O(m*log^2(m))</td></tr><tr><td>nlookup</td><td>HyperNovaに適したLookup</td><td></td></tr></tbody></table>

(mはルックアップ回数、Nはテーブルサイズ)

(計算コストはフィールド計算の回数)

### References

1. Arya: [https://discovery.ucl.ac.uk/id/eprint/10063978/1/ZKRAM-Asiacrypt2018-Final.pdf](https://discovery.ucl.ac.uk/id/eprint/10063978/1/ZKRAM-Asiacrypt2018-Final.pdf)
2. plookup: [https://eprint.iacr.org/2020/315.pdf](https://eprint.iacr.org/2020/315.pdf)
   1. Presentation: [https://www.youtube.com/watch?v=Vdlc1CmRYRY](https://www.youtube.com/watch?v=Vdlc1CmRYRY)
3. HyperPlonk: [https://eprint.iacr.org/2022/1355](https://eprint.iacr.org/2022/1355)
4. Caulk: [https://eprint.iacr.org/2022/621](https://eprint.iacr.org/2022/621)
5. Caulk+: [https://eprint.iacr.org/2022/957](https://eprint.iacr.org/2022/957)
6. flookup: [https://eprint.iacr.org/2022/1447](https://eprint.iacr.org/2022/1447)
