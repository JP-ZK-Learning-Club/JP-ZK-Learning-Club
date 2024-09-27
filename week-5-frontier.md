# Week 5 Frontier

Week5では”frontier”ということで以下のマテリアルをベースにMPC、FHE、TLSNotary、ZKEmailについて学習

{% embed url="https://github.com/privacy-scaling-explorations/core-program/blob/main/2024/week5_frontier.md" %}

## MPC

(Secure) Multi Party Computationのことで、複数の参加者が各々が持つ秘密情報を開示せずに、協働して計算を可能にする方法です。期末テストの答案が返ってきた際に自分の点数は開示しないけれど、友人と比較してどうか、、、ということはよくあることかと思います。現実的にはこっそり教えあうのですが、MPCがあることでどちらが上であるか、や仲良しグループの平均点を知ることができることになります。

MPCの起源は1980年代からシャミアの秘密分散やGarbled circuitsなどからスタートしており、今日に至るまで研究が進んでいます。

最近のユースケースとしては以下のようなものが例として挙げられます。

1. 機械学習のモデル作成時にデータ提供者が自らの原データを開示せず、学習（計算）を実行する
2. 暗号資産管理で秘密鍵の生成を分散して管理する

実際の例としてGarbled Circuitsを見ていきます。Garbled circuitsでは 1 out of 2 と呼ばれるMPCの先駆けで、

Obvlious Transfer (紛失通信、OT)を用いる；OTはSenderとReciever間の情報のやりとりで、⓵SenderはRecieverにメッセージ $$m_0$$ , $$m_1$$を暗号化したうえで送信するが、②Receiverは予め決めていた $$b \in \{0,1\}$$について $$m_b$$のみ内容を確認でき(つまり $$m_{1-b}$$をReceiverはわからない）、③Senderは $$b$$ について知ることはない、というものになる

## FHE



## TLS Notary



## ZKEmail
