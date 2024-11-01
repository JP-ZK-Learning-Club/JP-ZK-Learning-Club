---
description: メモリ読み込み・書き込みの一貫性
---

# メモリ読み込み・書き込みの一貫性

## 概要(WIP)

Memory Consistency Checks (MCC)は、zkVMのread/write operationにおける一貫性を証明する重要なパーツです。

M: Memory Size\
N: Number of Memory Access



Note:  SpiceとNebulaのfixed costsは回路上で行う必要があるWSとRSのInitializeとFinalizeのコストで、Joltの方はGKRにこのコストがかかるはず。



<table><thead><tr><th width="114">Paper</th><th width="106">Access Efficiency</th><th>Fixed Costs</th><th width="78">Space Efficiency</th><th width="57">IVC</th><th width="121">Techniques</th><th>Proof System</th></tr></thead><tbody><tr><td>TinyRAM ..etc</td><td>O(log M)</td><td>-</td><td>O(M)</td><td>△</td><td>MerkleTree</td><td>Only R1CS, CCS, ..etc</td></tr><tr><td>Spice</td><td>O(1) hash</td><td>O(M) hash</td><td>O(1)</td><td>○</td><td>Multiset Hash</td><td>Only R1CS, CCS, ..etc</td></tr><tr><td>Nebula</td><td>(O1) filed ops</td><td>O(M) filed ops</td><td>O(1)</td><td>◎</td><td>Multiset Fingerprinting </td><td>Two-Layered IVC + Commitment-Carrying IVC</td></tr><tr><td>Jolt/Lasso</td><td>(O1) filed ops</td><td>?</td><td>O(N)</td><td>×</td><td>Multiset Fingerprinting + Ground Product</td><td>LogUp Optimisation + GKR</td></tr><tr><td>Proofs for Deep Thought</td><td>(O1) filed ops</td><td>?</td><td>O(1)</td><td>◎</td><td>Updating Lookup Table using differential Δ in LogUp</td><td>ProtoStar + IVC-Friendly GKR</td></tr></tbody></table>

