# Lookup Singularity

Lookup Argumentは肥大化しやすい算術回路への変換過程を効率化することができる仕組みです。

証明生成前に予め参照テーブルを計算しておき、そのデータを参照することで算術回路を節約できます。

このような工夫により計算量を抑えるだけでなく、メンテナンスコスト及び監査コストも下げることができるためzkVMのような複雑なアーキテクチャの開発ハードルを下げることができます。実際にJoltにおけるLassoなど、ほとんどのプロジェクトではLookup Argumentを用いた設計を採用しています。

この発想は2022年に[**Lookup Singularity**](https://zkresear.ch/t/lookup-singularity/65)というタイトルでbarry whitehatによって提唱され、現在に至ります。

## 参照

[https://zkresear.ch/t/lookup-singularity/65](https://zkresear.ch/t/lookup-singularity/65)\
[https://eprint.iacr.org/2023/1216](https://eprint.iacr.org/2023/1216)\
