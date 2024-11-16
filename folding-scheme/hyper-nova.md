# HyperNova

HyperNovaはNovaを拡張した、CCS(Customizable Constraint System)フォーマットのインスタンスを畳み込むスキームです。

この解説は、こちらの\[SuperSpartan by Hand]\([https://anoma.net/research/superspartan-by-hand](https://anoma.net/research/superspartan-by-hand))と、\[HyperNova by Hand]\([https://anoma.net/research/hypernova-by-hand](https://anoma.net/research/hypernova-by-hand))を参考にしています。

## 概要 <a href="#transforming-any-check-into-a-sumcheck" id="transforming-any-check-into-a-sumcheck"></a>

HyperNovaは、CCSのMLEをSum-Check Protocolで証明する際に、内側の重いsum-checkをランダム線形結合して一番最後に一気に証明することで、各ステップの証明を減らすようなfolding-schemeです。

まずMLE (Multilinear extensions)とは、$$f: \{0,1\}^{\ell} \rightarrow \mathbb{F}$$ のような多変数多項式 ($${\ell}$$-variate polynomial)が、$$\tilde{f} : \mathbb{F}^{\ell} \rightarrow \mathbb{F}$$ のような多重線形多項式 (multilinear polynomial)となることを指します。また、MLEの多項式内の全ての項の次数は一次以下となるのが特徴です。

$$\tilde{f}(x)$$をboolean-hypercube上のどの点（$$\forall{x} \in \{0,1\}^{\ell}$$）で評価しても全て$$0$$になることを確認するのがzero-check、全ての合計が$$v$$になることを確認するのがsum-checkになります。

検証者がこの多項式$$\tilde{f}$$ を、boolean-hypercube上の全ての点で多項式を評価するのは、$$2^{\ell}$$回の評価が必要ですが、sum-check protocolでは、$$\ell$$回まで減らすことができます。

この記事の前半では、CCSをMLEへ拡張したsum-checkを証明する所まで解説し、後半ではsum-checkを畳み込む方法を解説します。

## MLE (Multilinear extensions)

多変数多項式 (multivariate polynomial) $$f: \{0,1\}^{\ell} \rightarrow \mathbb{F}$$ をMLEにするには、次の$$\tilde{eq}(x, e)$$を使います。これは、$$x=e$$ のときは$$1$$を、それ以外は$$0$$となる多重線形多項式(multilinear polynomial)です。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 18.42.42.png" alt=""><figcaption></figcaption></figure>

$$f(x)$$には次数が1次以上の項が含まれているので、$$f$$を$$x$$で評価してしまった値を$$\tilde{eq}$$で選ぶことで、全ての項の次数が1次以下になるようにします。次の式を見ると、$$f$$の中にある変数は$$\sum_{x \in \{0,1\}^{\ell}}$$によって既に評価され展開されています。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 18.44.14.png" alt=""><figcaption></figcaption></figure>

## CCSをMLEへ拡張

### CCSの定義

CCSでは、次の式を満たす$$c_i$$, $$S_i$$, $$M_j$$, $$z$$が必要となります。$$M$$は、R1CSでの$$A$$,$$B$$,$$C$$の行列に対応し、$$S$$はアダマール積を行う行列のグループ、$$c$$は定数項、$$z$$はinputやwitnessなどです。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 18.54.58.png" alt=""><figcaption></figcaption></figure>

### R1CSからCCSへ

この記事では、次のような、R1CSをCCSへ変換した形を扱っていきますので、$$M$$は$$[M_1, M_2, M_3]$$、$$S = [\{1,2\}, \{3\}]$$となります。また、$$M_1$$,$$M_2$$,$$M_3$$は、R1CSの$$A$$,$$B$$,$$C$$に対応しますが、MLEにするには行と列が2の倍数である必要があるので、4行目がゼロベクトルでパディングされています。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 19.08.24.png" alt=""><figcaption></figcaption></figure>

### CCSからsum-checkへ

次の行列の演算を含む式をmultilinear polynomialにするには、まず$$M_i$$と$$z$$を多項式として表現する必要があります。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 21.43.12.png" alt=""><figcaption></figcaption></figure>

$$M_i$$は$$m \times n$$なので、$$M_i$$を$$M_i(m,n)$$: $$\mathbb{F}^2 \rightarrow \{0,1\}$$の多項式として考えます。$$m,n$$を指定すれば、それに対応する$$\{0,1\}$$が返ってきます。

これをMLEにするので、次のように$$\tilde{eq}$$を使って$$\tilde{M_i}(X, Y),\tilde{z}(Y)$$を定義します。HyperNovaでは、$$m=s, n=s'$$です。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 22.03.23.png" alt=""><figcaption></figcaption></figure>

したがって、$$M_i \cdot z$$のmultivariate polynomialは、次のようになります。このとき、$$Y$$は全ての$$y \in \{0,1\}^{s'}$$で展開しておきます。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 22.08.53.png" alt=""><figcaption></figcaption></figure>

$$M$$は3つありますので、それらを合わせると、次のような多項式$$G$$を定義できます。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 22.14.53.png" alt=""><figcaption></figcaption></figure>

ここで注意すべきは、$$G$$の内部で多項式を掛けてしまっているので、multilinear polynomialではないということです。そこで、$$\tilde{eq}$$を使ってMLEにしていきます。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 22.16.52.png" alt=""><figcaption></figcaption></figure>

### Schwartz-Zippel lemmaでΣを減らす

全ての$$x \in \{0,1\}^2$$で$$G(x) = 0$$となることを確かめられれば、CCSの制約が成り立っていることを確認できます。Schwartz-Zippel lemmaにより、$$h(X1,X2)=0$$ となる確率が高いと分かります。

次の式では、$$(\beta_1, \beta_2)$$と一致する項が$$\tilde{eq}$$によって残され、それが$$0$$であることが確かめられます。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 22.34.17.png" alt=""><figcaption></figcaption></figure>

これを利用すると、$$\sum$$を減らすことができます。この$$Q(X_1, X_2)$$をsum-check protocolで証明していきます。

<figure><img src="../.gitbook/assets/Screenshot 2024-11-16 at 22.34.54.png" alt=""><figcaption></figcaption></figure>

## Sum-check protocol



### Reference

* [https://eprint.iacr.org/2023/573](https://eprint.iacr.org/2023/573)
* [https://github.com/privacy-scaling-explorations/multifolding-poc](https://github.com/privacy-scaling-explorations/multifolding-poc)
