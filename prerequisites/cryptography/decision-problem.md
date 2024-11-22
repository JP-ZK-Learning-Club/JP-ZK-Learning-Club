# 決定問題

## 決定問題

ZKPの勉強に対して一番理解が必要な部分は決定問題になります。

> _決定問&#x984C;_&#x306F;、答えが「はい」か「いいえ」になる問題を指します。
>
> 決定問題ではない問題は、ゼロ知識証明の対象になりません。

<table><thead><tr><th width="460">決定問題例</th><th width="212">分類</th><th>複雑性<select><option value="WlWCzUmFO6hi" label="P" color="blue"></option><option value="JXBWTqEh1Ndu" label="NP" color="blue"></option><option value="I82x34RnMvmY" label="PSPACE" color="blue"></option></select></th></tr></thead><tbody><tr><td><code>33</code>は素数ですか？</td><td>素数判定問題</td><td><span data-option="WlWCzUmFO6hi">P</span></td></tr><tr><td>辺の長さが<code>3, 4, 5の</code>三角形は直角三角形ですか？</td><td>幾何学問題</td><td><span data-option="WlWCzUmFO6hi">P</span></td></tr><tr><td>377の素因数は13と29ですか？</td><td>素因数分解問題</td><td><span data-option="JXBWTqEh1Ndu">NP</span></td></tr><tr><td><p><span class="math">A \land \lnot B</span>の命題論理式に対して、</p><p> <span class="math">A=True, B=False</span>は命題論理式を満たしますか？</p></td><td><a href="https://ja.wikipedia.org/wiki/%E5%85%85%E8%B6%B3%E5%8F%AF%E8%83%BD%E6%80%A7%E5%95%8F%E9%A1%8C">充足可能性問題</a> (SAT)</td><td><span data-option="JXBWTqEh1Ndu">NP</span></td></tr><tr><td><p><span class="math">A\cdot(1-B)=1</span>の算術回路に対して、</p><p><span class="math">A=1, B= 1</span>は算術回路を満たしますか？</p></td><td>算術回路充足可能性問題(CircuitSAT, CSAT)</td><td><span data-option="JXBWTqEh1Ndu">NP</span></td></tr><tr><td><span class="math">3^x \equiv 13 \pmod{17}</span>に対して、<span class="math">x=4</span>は数式の解ですか？</td><td>離散対数問題 (DLP)</td><td><span data-option="JXBWTqEh1Ndu">NP</span></td></tr><tr><td><span class="math">E: y^2 = x^3 + 7</span> を有限体 <span class="math">\mathbb{F}_{17}</span>上の楕円曲線とした時、<span class="math">P =(4,10),Q=(12, 16), k=14</span><span class="math">k=14</span>は<span class="math">Q = kP</span>を満たしますか？</td><td>楕円曲線離散対数問題(ECDLP)</td><td><span data-option="JXBWTqEh1Ndu">NP</span></td></tr><tr><td>囲碁で次の石をD16に置くことは最善手ですか？</td><td>囲碁最適手問題</td><td><span data-option="I82x34RnMvmY">PSPACE</span></td></tr></tbody></table>

## 決定問題ではない例

1. 今日の夜ご飯何にしようか
