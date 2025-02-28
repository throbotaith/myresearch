## 最適性

**方策勾配法のグローバルな収束特性を、正確な勾配が与えられた場合に理解することを目指す**。ここでは、例 11.1 の (表形式) **ソフトマックス方策クラスに主に限定**する。

状態に対する初期分布 $\rho$ が与えられた場合、目的は以下である。
$$
\max _{\theta \in \Theta} V^{\pi_\theta}(\rho) .
$$
ここで、$\left\{\pi_\theta \mid \theta \in \Theta \subset \mathbb{R}^d\right\}$ はパラメトリック方策のクラスである。
$\rho$ における良好なパフォーマンスに関心がある一方で、別の尺度 $\mu$ で最適化すると有益であることがわかる。具体的には、
$$
\max _{\theta \in \Theta} V^{\pi_\theta}(\mu),
$$
つまり $V^{\pi_\theta}(\mu)$ を最適化することを考える。$\rho$を用いる場合、初期状態が報酬状態よりもかなり離れている場合、一部のMDPで勾配消失問題等が発生するため、別の尺度を使って最適化したい。最終的な目標は $V^{\pi_\theta}(\rho)$ でのパフォーマンスであるにもかかわらず。

ここで、ソフトマックス方策パラメータ化 (0.1) を考える。ここでは、補題 11.5 に示されているように、一般的に**非凸最適化問題**が依然として存在する。ただし、**特定の正則条件下ではグローバルな最適性**を実現できることを示す。実用的な観点から、**方策のソフトマックスパラメータ化は、直接パラメータ化よりも好ましい**。なぜなら、**パラメータ $\theta$ は制約がなく、標準的な制約のない最適化アルゴリズムを使用できる**からである。ただし、この方策クラスでの最適化は、このセクションで検討するような別の課題を生み出す。最適な方策 (決定論的) は、パラメータを無限大に送ることで達成される。

この章では、この問題に対するソフトマックス方策クラスのアルゴリズムを 3 つ検討する。最初のアルゴリズムは、目的関数の直接的な方策勾配上昇を実行する。2 番目のアルゴリズムは、$\log$ バリア正則化項を追加して、パラメータが大きくなりすぎないようにする。これは、適切な探索を保証するための手段である。最後に、自然方策勾配アルゴリズムを検討し、次元依存要因に依存しないグローバルな最適性収束速度を確立する。

この章の内容は、主に [Agarwal et al., 2020d] の結果に基づいている。
### 12.1 勾配の消失と鞍点

$\rho$ **と異なる分布 $\mu$ で最適化する必要がある理由**を理解するために、まず、$\pi$ の状態分布、またはそれと等価な $\mu$ について、ある条件が、定常状態が最適性を意味するために必要であることを示す非公式な議論を行う。たとえば、**スパース報酬 MDP** (エージェントは、ある小さな状態の集合を訪問した場合にのみ報酬を得る) では、報酬状態を訪問しない方策は、値の観点から任意に劣っているにもかかわらず、勾配が 0 になる。以下では、この直感のより定量的バージョンを示す。これは、$\pi$ が**すべての行動を妥当な確率で選択する場合** (そのため、MDP が接続されていればエージェントはすべて状態を訪問する)、**勾配が非常に小さい方策 $\pi$ が多数存在する MDP が存在すること**を示している。これらの方策は、その値の観点から非常に劣っている。

具体的には、図 0.1 に示されている長さ $H+2$ のチェーン MDP を考える。関心のある初期状態は状態 $s_0$ であり、割引率 $\gamma=H /(H+1)$ である。直接パラメータ化を使用すると、$\pi_\theta(a \mid s)=\theta_{s, a}$ (ただし、$a=a_1, a_2, a_3$) かつ $\pi_\theta\left(a_4 \mid s\right)=1-\theta_{s, a_1}-\theta_{s, a_2}-\theta_{s, a_3}$ となる。方策を過剰にパラメータ化しないことに注意する。この MDP と方策構造では、行動に対する確率を初期化する場合 (たとえば、決定論的に)、すべての確率が $1 / 4$ 未満になる MDP (行動を置換することで得られる) が存在する。

次の結果は、勾配が $H$ に対して指数関数的に小さいことを示すだけでなく、$O(H / \log H)$ までの多くの高次導関数も $H$ に対して指数関数的に小さいことを示している。

命題 12.1 (最適でないパラメータにおける勾配の消失)。図 0.1 に示されている、$H+2$ 個の状態、$\gamma=H /(H+1)$、および直接方策パラメータ化 (テキストで説明されているように、$3|\mathcal{S}|$ 個のパラメータを持つ) を持つチェーン MDP を考える。$\theta$ が $0<\theta<1$ (要素ごとに) かつ $\theta_{s, a_1}<1 / 4$ (すべて状態 $s$ に対して) であると仮定する。すべて $k \leq \frac{H}{40 \log (2 H)}-$ 1 に対して、$\left\|\nabla_\theta^k V^{\pi_\theta}\left(s_0\right)\right\| \leq(1 / 3)^{H / 4}$ が成り立つ。ここで、$\nabla_\theta^k V^{\pi_\theta}\left(s_0\right)$ は、$V^{\pi_\theta}\left(s_0\right)$ の $k_{\text {th }}$ 次の導関数のテンソルであり、ノルムはテンソルの演算子ノルムである。 ${ }^1$ さらに、$V^{\star}\left(s_0\right)-V^{\pi_\theta}\left(s_0\right) \geq(H+1) / 8-(H+1)^2 / 3^H$ が成り立つ。

この補題はここでは証明しない (12.5 節を参照)。この補題は、適切な探索がなければ、方策勾配アルゴリズムでは実際にもたらす可能性があることを示している。なぜなら、勾配は、$\pi$ がほぼ最適であるか、または単に $\pi$ が有利な状態を十分に頻繁に訪問しないために、小さくなる可能性があるからである。さらに、この補題は、鞍点からの脱出に関する非凸最適化文献のさまざまな結果が、高次導関数が小さいことから、直接的にはグローバルな収束を意味しないことを示唆している。

* **適切な探索の必要性:** 方策勾配法は、方策の勾配に基づいて方策を更新する手法です。しかし、命題12.1で示されたように、勾配が小さいからといって、その方策が良い方策とは限りません。勾配が小さくなる理由は2つあります。
    1. **方策が（ほぼ）最適解である場合:** この場合は、勾配が小さくなるのは自然なことです。
    2. **方策が報酬の高い状態を十分に探索していない場合:** この場合、方策は最適解から遠く離れているにもかかわらず、勾配が小さくなってしまい、学習が停滞する可能性があります。
    つまり、勾配が小さいだけでは、その方策が最適解に近いのか、それとも単に探索が不十分なのかを判断できません。そのため、方策勾配法では、適切な探索戦略を組み込むことが重要になります。

* **鞍点問題:** 非凸最適化問題において、勾配がゼロになる点は、必ずしも最適解とは限りません。鞍点と呼ばれる、ある方向には目的関数の値が増加し、別の方向には目的関数の値が減少するような点も存在します。鞍点では勾配がゼロになるため、勾配ベースの最適化手法は鞍点にトラップされてしまう可能性があります。

    命題12.1は、高次導関数も小さい場合、鞍点から脱出するのが難しいことを示唆しています。非凸最適化の文献では、高次導関数を利用して鞍点から脱出する手法が提案されていますが、命題12.1のような状況では、これらの手法も効果を発揮しない可能性があります。

    したがって、方策勾配法を非凸な強化学習問題に適用する際には、鞍点にトラップされる可能性を考慮する必要があります。鞍点問題に対処するためには、探索を促進するメカニズムや、より高度な最適化アルゴリズムが必要となる場合があります。

図 0.1 に示されているチェーン MDP は、サンプルベースの勾配の推定値が、ランダム探索戦略下で 0 になる一般的な例である。ランダム探索戦略下では、目標状態に到達する確率は $H$ に対して指数関数的に小さい。この補題は、正確な勾配に関してであることに注意する。これは、正確な計算 (正確な高次導関数を使用することなど) を実行しても、数値不安定性が発生する可能性があることを示唆している。
![[Pasted image 20240804213706.png]]

図 0.1: (勾配の消失の例) 長さ $H+2$ の決定論的チェーン MDP。$\pi\left(a \mid s_i\right)=\theta_{s_i, a}$ (ただし、$i=1,2, \ldots, H$) である方策を考える。報酬は、$r\left(s_{H+1}, a_1\right)=1$ 以外ではすべて 0 である。命題 12.1 を参照。




### 12.2 方策勾配上昇

ここで、以下の式で表されるソフトマックス方策クラスに戻ろう。
$$
\pi_\theta(a \mid s)=\frac{\exp \left(\theta_{s, a}\right)}{\sum_{a^{\prime}} \exp \left(\theta_{s, a^{\prime}}\right)}
$$
この方策クラスのパラメータ数は $|\mathcal{S} \| \mathcal{A}|$ である。この式の対数を計算すると、
$$
\log \pi_\theta(a \mid s) = \theta_{s, a} - \log \left( \sum_{a'} \exp(\theta_{s, a'}) \right)
$$
となる。次に、この式をパラメータ $\theta_{s', a'}$ で偏微分する。
**ケース1:  $s \neq s'$ かつ $a = a'$ or$s \neq s'$ かつ $a \neq a'$  の場合**
状態 $s$ と $s'$ が異なる場合、$\log \pi_\theta(a \mid s)$ は $\theta_{s', a'}$ を含まないため、偏微分は 0 になる。
$$
\frac{\partial \log \pi_\theta(a \mid s)}{\partial \theta_{s', a'}} = 0
$$
**ケース2: $s = s'$ かつ $a = a'$ の場合**
状態 $s$ と $s'$ が同じ場合、以下のようになる。$$
    \frac{\partial \log \pi_\theta(a \mid s)}{\partial \theta_{s', a'}} = 1 - \pi_\theta(a' \mid s') = 1 - \pi_\theta(a \mid s)=1-\pi_\theta(a' \mid s)
   $$**ケース 2-1: $s = s'$ かつ $a \neq a'$ の場合**
$$
\log \pi_\theta(a \mid s) = \theta_{s, a} - \log\left(\sum_b \exp(\theta_{s, b})\right)
$$なので、$a \ne a'$ のもとで $\theta_{s, a'}$ で偏微分すると、第一項は $\theta_{s, a}$ を含んでいないため 0 になり、第二項は連鎖律を用いて
$$
\begin{aligned}
\frac{\partial}{\partial \theta_{s', a'}} \left[- \log\left(\sum_a \exp(\theta_{s, a})\right)\right] &= -\frac{1}{\sum_a \exp(\theta_{s, a})} \cdot \frac{\partial}{\partial \theta_{s', a'}} \left(\sum_a \exp(\theta_{s', a})\right) \\
&= -\frac{1}{\sum_a \exp(\theta_{s, a})} \cdot \exp(\theta_{s', a'}) \\
&= -\frac{\exp(\theta_{s', a'})}{\sum_a \exp(\theta_{s, a})} \\
&= -\pi_\theta(a' \mid s')\\
&= -\pi_\theta(a' \mid s)
\end{aligned}
$$
となる。二番目の等式は、
$$
\sum_{a} \exp(\theta_{s, a}) = \exp(\theta_{s, a_1}) + \exp(\theta_{s, a_2}) + \dots + \exp(\theta_{s, a_n})
$$
となり、$s=s'$かつ$a=a'$とる場合のみ微分値を得る。よって自然対数の微分の性質より、$\exp(\theta_{s', a'})$となる。 
#### 結論
これらの条件をまとめると、
$$
\frac{\partial \log \pi_\theta(a \mid s)}{\partial \theta_{s^{\prime}, a^{\prime}}}=\mathbb{1}\left[s=s^{\prime}\right]\left(\mathbb{1}\left[a=a^{\prime}\right]-\pi_\theta\left(a^{\prime} \mid s\right)\right)
$$
ここで、$\mathbb{1}[\mathcal{E}]$ は、$\mathcal{E}$ が真である場合のインジケータである。

#### 補題 12.2. 
ソフトマックス方策クラスでは、以下が成り立つ。
$$
\frac{\partial V^{\pi_\theta}(\mu)}{\partial \theta_{s, a}}=\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a)
$$
#### 補題 12.2証明: 
方策勾配のアドバンテージ式 (定理 11.4 を参照) を使用すると、
$$
\begin{aligned}
\frac{\partial V^{\pi_\theta}(\mu)}{\partial \theta_{s^{\prime}, a^{\prime}}} & =\frac{1}{1-\gamma} \mathbb{E}_{s \sim d_\mu^{\pi_\theta}} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s)}\left[A^{\pi_\theta}(s, a) \frac{\partial \log \pi_\theta(a \mid s)}{\partial \theta_{s^{\prime}, a^{\prime}}}\right] \\
& =\frac{1}{1-\gamma} \mathbb{E}_{s \sim d_\mu^{\pi_\theta}} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s)}\left[A^{\pi_\theta}(s, a) \mathbb{1}\left[s=s^{\prime}\right]\left(\mathbb{1}\left[a=a^{\prime}\right]-\pi_\theta\left(a^{\prime} \mid s\right)\right)\right] \\
\text{step 3}& =\frac{1}{1-\gamma} d_\mu^{\pi_\theta}\left(s^{\prime}\right) \mathbb{E}_{a \sim \pi_\theta\left(\cdot \mid s^{\prime}\right)}\left[A^{\pi_\theta}\left(s^{\prime}, a\right)\left(\mathbb{1}\left[a=a^{\prime}\right]-\pi_\theta\left(a^{\prime} \mid s^{\prime}\right)\right)\right] \\
& =\frac{1}{1-\gamma} d_\mu^{\pi_\theta}\left(s^{\prime}\right)\left(\mathbb{E}_{a \sim \pi \theta\left(\cdot \mid s^{\prime}\right)}\left[A^{\pi_\theta}\left(s^{\prime}, a\right) \mathbb{1}\left[a=a^{\prime}\right]\right]-\pi_\theta\left(a^{\prime} \mid s^{\prime}\right) \mathbb{E}_{a \sim \pi_\theta\left(\cdot \mid s^{\prime}\right)}\left[A^{\pi_\theta}\left(s^{\prime}, a\right)\right]\right) \\
\text{step 5}& =\frac{1}{1-\gamma} d_\mu^{\pi_\theta}\left(s^{\prime}\right) \pi_\theta\left(a^{\prime} \mid s^{\prime}\right) A^{\pi_\theta}\left(s^{\prime}, a^{\prime}\right)-0,
\end{aligned}
$$
ここで、$\text{step}3$での等式は、外側の期待値を以下のように展開する。
$$
\frac{1}{1-\gamma} \sum_s d_\mu^{\pi_\theta}(s) \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s)}\left[A^{\pi_\theta}(s, a) \mathbb{1}\left[s=s^{\prime}\right]\left(\mathbb{1}\left[a=a^{\prime}\right]-\pi_\theta\left(a^{\prime} \mid s\right)\right)\right]
$$
ここで、$\mathbb{1}[s=s']$ は $s=s'$ のときのみ 1 となり、それ以外の場合は 0 となるため、
$$
\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s') \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s')}\left[A^{\pi_\theta}(s', a) \left(\mathbb{1}\left[a=a^{\prime}\right]-\pi_\theta\left(a^{\prime} \mid s'\right)\right)\right]
$$
となる。
$\text{step}5$の手前では、
期待値を以下のように展開する。
$$
\begin{aligned}
&\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s') \sum_a \pi_\theta(a \mid s') A^{\pi_\theta}(s', a) \left(\mathbb{1}\left[a=a^{\prime}\right]-\pi_\theta\left(a^{\prime} \mid s'\right)\right)\\

&=\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s') \left[ \underbrace{\sum_a \pi_\theta(a \mid s') A^{\pi_\theta}(s', a) \mathbb{1}\left[a=a^{\prime}\right]}_{\text{第一項}} - \\
\underbrace{\sum_a \pi_\theta(a \mid s') A^{\pi_\theta}(s', a) \pi_\theta(a' \mid s')}_{\text{第二項}} \right]
\end{aligned}
$$

第一項は、$\mathbb{1}[a=a']$ が $a=a'$ のときのみ 1 になるため、
$$
\sum_a \pi_\theta(a \mid s') A^{\pi_\theta}(s', a) \mathbb{1}[a=a'] = \pi_\theta(a' \mid s') A^{\pi_\theta}(s', a')
$$
となる。第二項は、$\pi_\theta(a' \mid s')$ が $a$ に依存しないため、
$$
\sum_a \pi_\theta(a \mid s') A^{\pi_\theta}(s', a) \pi_\theta(a' \mid s') = \pi_\theta(a' \mid s') \sum_a \pi_\theta(a \mid s') A^{\pi_\theta}(s', a)
$$
となり、アドバンテージ関数の定義により、
$$
\sum_a \pi_\theta(a \mid s) A^{\pi_\theta}(s, a) = 0
$$
であることから、
$$
\sum_a \pi_\theta(a \mid s') A^{\pi_\theta}(s', a) \pi_\theta(a' \mid s') = \pi_\theta(a' \mid s') \cdot0
$$
以上から、式は
$$
\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s') \pi_\theta(a' \mid s') A^{\pi_\theta}(s', a')
$$
となる。

#### 勾配上昇更新規則の話

勾配上昇の更新規則は次のとおりである。
$$
\theta^{(t+1)}=\theta^{(t)}+\eta \nabla_\theta V^{(t)}(\mu)
$$
補題 11.5 から、**ソフトマックス方策クラス**の場合でも (すべての定常方策を含む)、**最適化問題は非凸**であることを思い出そう。
* 非凸の時に発生する問題は？
	* 勾配が0になる点が最適解とは限らない
	* 凸最適化のツールを使えない
	* 鞍点問題が発生(ミニマックス問題なら問題ないが)
	* ソフトマックス関数は多変数関数

さらに、ソフトマックスパラメータ化におけるパラメータ $\theta$ に対する指数関数的なスケーリングにより、**ほぼ決定論的な方策はすべて、勾配が 0 に近い**。具体的には、決定論的になる方策 $\pi^{\theta_t}$ のシーケンスに対して、**$t$が大きくなるにつれて、$\left\|\nabla V^{\pi^{\theta_t}}\right\| \rightarrow 0$** となる。
* 具体的な話：
	ソフトマックス方策の勾配は以下のようになる。$$
	\frac{\partial \log \pi_\theta(a|s)}{\partial \theta_{s,a'}} = \mathbb{1}[a=a'] - \pi_\theta(a'|s)
	$$	ここで、ほぼ決定論的な方策 $\pi_\theta(a^*|s) \approx 1$ を考えると、
	* $a' = a^*$ の場合: $\frac{\partial \log \pi_\theta(a|s)}{\partial \theta_{s,a^*}} \approx \mathbb{1}[a=a^*] - 1\fallingdotseq 0$
	* $a' \ne a^*$ の場合: $\frac{\partial \log \pi_\theta(a|s)}{\partial \theta_{s,a'}} \approx \mathbb{1}[a=a'] - 0\fallingdotseq 0$
	どちらも限りなく0に近い値になる。

これらの**困難にもかかわらず、勾配上昇が、ソフトマックスパラメータ化に対して漸近的にグローバルな最適解に収束する**という、肯定的な結果が得られている。

#### 定理 12.3 
(ソフトマックスパラメータ化に対するグローバルな収束)
式 (0.2) で指定されている勾配上昇更新規則($\theta^{(t+1)}=\theta^{(t)}+\eta \nabla_\theta V^{(t)}(\mu)$)に従い、分布 $\mu$ が厳密に正 (つまり、すべて状態 $s$ に対して $\mu(s)>0$) であると仮定する。$\eta \leq \frac{(1-\gamma)^3}{8}$ であると仮定すると、すべて状態 $s$ に対して、$t \rightarrow \infty$ のとき、$V^{(t)}(s) \rightarrow V^{\star}(s)$ となる。

#### 定理12.3の証明


証明はやや技術的で、ここでは証明を示さない (12.5 節を参照)。

#### 定理12.3に関する話

いくつか注意すべき点がある。定理 12.3 では、**最適化分布 $\mu$ が厳密に正 (つまり、すべて状態 $s$ に対して $\mu(s)>0$) であることを仮定**した。この条件が**満たされない場合、勾配上昇はグローバルに収束しない可能性がある**と推測する。懸念点は、この条件が満たされない場合、$d_\mu^{\pi_\theta}(s)$ は、状態 $s$ に関連付けられたパラメータに対する学習率を効果的にスケールダウンする。$d$が0の場合、勾配はそれに比例するため、0になってしまう。)ため、勾配上昇がグローバルに収束しない可能性があることである (式 0.1 を参照)。
* なぜ $\mu(s) > 0$ が必要なのか？
	すべての状態 $s$ に対して $\mu(s) > 0$ であれば、$d_\mu^{\pi_\theta}(s) > 0$ となることが保証される。これは、どんな方策 $\pi_\theta$ を用いても、全ての状態を訪問する可能性があることを意味する。
	
	例えば、図0.1を考える。
	![[Pasted image 20240804213706.png]]
	1. $\mu$ を、$\mu(s_0) = 1$、その他は $\mu(s) = 0$ と設定する。 つまり、エージェントは常に $s_0$ から開始する。
	2. 初期方策 $\pi_\theta$ を、すべての状態 $s$ で $\theta_{s, a_1} = 0$、$\theta_{s, a_2} = \theta_{s, a_3} = \theta_{s, a_4} = C$ ($C$ は大きな正の定数) と設定する。 これは、$s_0$ では $a_1$ のみが選択可能なので常に $a_1$ を選択し、$s_1$ から $s_H$ までは $a_2, a_3, a_4$ をほぼ等確率で選択し、$a_1$ をほとんど選択しない方策である。
	この設定では、$s_0$ から $s_{H+1}$ に到達する確率はほぼゼロである。なぜなら、$s_1$ から $s_H$ のいずれかの状態で $a_1$ を選択する必要があるからである。結果として、$d_\mu^{\pi_\theta}(s_{H+1}) = 0$ となり、$s_{H+1}$ での報酬 $r(s_{H+1}, a_1)$が $V^{\pi_\theta}(\mu)$ に反映されない。
	このとき、勾配$$
	\frac{\partial V^{\pi_\theta}(\mu)}{\partial \theta_{s, a}}=\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a)
	$$
	において、$d_\mu^{\pi_\theta}(s)$ は $s_0$ から到達可能な状態でのみ正の値を取り、$s_{H+1}$ やそれに近い状態では 0 になる。したがって、$\theta_{s_{H+1}, a_1}$ やそれに近い状態のパラメータは更新されず、方策は改善されない。つまり、勾配上昇は大域最適解には収束しない。

さらに、このアルゴリズムの収束速度 (最悪の場合) は、**状態空間のサイズなどの関連する量に対して指数関数的に遅い**可能性が高いという強い理由がある。
* 収束するために必要な反復回数が、状態空間のサイズなどに指数関数的に比例して大きくなる。
次に、すべての関連する量に対して**多項式的な速度で収束を保証**するための、**正則化に基づくアプローチ**に焦点を当てる。
* 多項式的ってなんだっけ
	* $O(n),O(n^2)$などを多項式時間という。理想
	* 一方で、 $O(2^n)$などは指数時間である。現実

### 12.3 $\log$ バリア正則化

#### ソフトマックス方策における課題
パラメータ $\theta$ に対する指数関数的なスケーリングにより、**ソフトマックスパラメータ化で最適化すると、方策は急速にほぼ決定論的になる**可能性があり、**これは収束速度の低下につながる**可能性がある。
* 指数関数的なスケーリングとは？
	ソフトマックス方策は、
	$$
	\pi_\theta(a|s) = \frac{\exp(\theta_{s,a})}{\sum_{b} \exp(\theta_{s,b})}
	$$
	とあらわされる。
	例えば、状態 $s$ において、行動 $a_1$ と $a_2$ を選択する確率を決定するパラメータ $\theta_{s,a_1}$ と $\theta_{s,a_2}$ を考える。 $\theta_{s,a_1} = 1$、$\theta_{s,a_2} = 0$ の場合、
	$$
	\pi_\theta(a_1|s) = \frac{\exp(1)}{\exp(1) + \exp(0)} \approx 0.73
	$$
	$$
	\pi_\theta(a_2|s) = \frac{\exp(0)}{\exp(1) + \exp(0)} \approx 0.27
	$$
	
	となる。ここで、$\theta_{s,a_1}$ を 2 に増加させると、
	$$
	\pi_\theta(a_1|s) = \frac{\exp(2)}{\exp(2) + \exp(0)} \approx 0.88
	$$
	$$
	\pi_\theta(a_2|s) = \frac{\exp(0)}{\exp(2) + \exp(0)} \approx 0.12
	$$
	となる。 $\theta_{s,a_1}$ が 1 から 2 に増加しただけで、$\pi_\theta(a_1|s)$ は約0.15増加し、$\pi_\theta(a_2|s)$ は約0.15減少した。
	よって、指数関数的なスケーリングによってパラメータの少しの変化が、方策における大きな変化に増大させる可能性がある。結果として、ある行動の選択確率がほかの行動に比べて非常に高くなるという問題が発生する。

実際、前のセクションの漸近解析における重要な課題は、パラメータの絶対値が無限大に近づくにつれて、その成長を処理することだった。分布 $p$ と $q$ の相対エントロピーは、以下のように定義されていることを思い出そう。
$$
\mathrm{KL}(p, q):=\mathbb{E}_{x \sim p}[-\log q(x) / p(x)] .
$$
####  $\log$ バリア正則化された目的関数
集合 $\mathcal{X}$ 上の均一分布を Unif $_{\mathcal{X}}$ と表し、以下の $\log$ バリア正則化された目的関数を定義する。
$$
\begin{aligned}
L_\lambda(\theta) & :=V^{\pi_\theta}(\mu)-\lambda \mathbb{E}_{s \sim \text { Unif }_{\mathcal{S}}}\left[\operatorname{KL}\left(\operatorname{Unif}_{\mathcal{A}}, \pi_\theta(\cdot \mid s)\right)\right] \\
& =V^{\pi \theta}(\mu)+\frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \sum_{s, a} \log \pi_\theta(a \mid s)+\lambda \log |\mathcal{A}|,
\end{aligned}
$$
ここで、$\lambda$ は正則化パラメータである。
####  $\log$ バリア正則化された目的関数の導出

KLダイバージェンスの定義を思い出す。確率分布 $P$ と $Q$ のKLダイバージェンスは、
$$
\operatorname{KL}(P || Q) = \mathbb{E}_{x \sim P}\left[ \log \frac{P(x)}{Q(x)} \right] = \sum_x P(x) \log \frac{P(x)}{Q(x)}
$$
で定義される。(教科書での表記が分かりにくかったため改変)この定義において、$P$ を行動空間 $\mathcal{A}$ 上の均一分布 $\text{Unif}_\mathcal{A}$、$Q$ を方策 $\pi_\theta(\cdot | s)$ とすると、
$$
\operatorname{KL}(\text{Unif}_\mathcal{A} || \pi_\theta(\cdot | s)) = \sum_{a \in \mathcal{A}} \text{Unif}_\mathcal{A}(a) \log \frac{\text{Unif}_\mathcal{A}(a)}{\pi_\theta(a | s)}
$$
となる。均一分布なので、$\text{Unif}_\mathcal{A}(a) = \frac{1}{|\mathcal{A}|}$ である。よって、
$$
\operatorname{KL}(\text{Unif}_\mathcal{A} || \pi_\theta(\cdot | s)) = \sum_{a \in \mathcal{A}} \frac{1}{|\mathcal{A}|} \log \frac{1/|\mathcal{A}|}{\pi_\theta(a | s)} = \frac{1}{|\mathcal{A}|} \sum_{a \in \mathcal{A}} \left( -\log |\mathcal{A}| - \log \pi_\theta(a | s) \right)
$$
となる。次に、状態 $s$ に関する期待値 $\mathbb{E}_{s \sim \text{Unif}_\mathcal{S}}$ を展開する。 $\mathcal{S}$ 上の均一分布なので、$\text{Unif}_\mathcal{S}(s) = \frac{1}{|\mathcal{S}|}$ となる。

$$
\begin{aligned}
\mathbb{E}_{s \sim \text { Unif }_{\mathcal{S}}}\left[\operatorname{KL}\left(\operatorname{Unif}_{\mathcal{A}}, \pi_\theta(\cdot \mid s)\right)\right] &= \sum_{s \in \mathcal{S}} \frac{1}{|\mathcal{S}|} \operatorname{KL}(\text{Unif}_\mathcal{A} || \pi_\theta(\cdot | s)) \\
&= \frac{1}{|\mathcal{S}|} \sum_{s \in \mathcal{S}} \frac{1}{|\mathcal{A}|} \sum_{a \in \mathcal{A}} \left( -\log |\mathcal{A}| - \log \pi_\theta(a | s) \right) \\
&= -\log|\mathcal{A}| - \frac{1}{|\mathcal{S}||\mathcal{A}|} \sum_{s \in \mathcal{S}} \sum_{a \in \mathcal{A}} \log \pi_\theta(a|s)
\end{aligned}
$$最後に、$L_\lambda(\theta)$ に代入すると、
$$
\begin{aligned}
L_\lambda(\theta) &= V^{\pi_\theta}(\mu) - \lambda \left( -\log|\mathcal{A}| - \frac{1}{|\mathcal{S}||\mathcal{A}|} \sum_{s, a} \log \pi_\theta(a|s) \right) \\
&= V^{\pi_\theta}(\mu) + \frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \sum_{s, a} \log \pi_\theta(a|s) + \lambda \log|\mathcal{A}|
\end{aligned}
$$

となる。定数 (つまり、最後の項) は最適化に関して関係ない。この正則化項は、より一般的に使用されているエントロピー正則化項とは異なる。後ほど、この点について再び言及する。
#### $\log$バリア正則化の更新規則
$L_\lambda(\theta)$ の方策勾配上昇更新は、以下で与えられる。
$$
\theta^{(t+1)}=\theta^{(t)}+\eta \nabla_\theta L_\lambda\left(\theta^{(t)}\right) .
$$

ここで、エントロピー正則化された目的関数の近似的な **1 次定常点は、正則化が十分に小さい場合、ほぼグローバルに最適**であることがわかる。

#### 定理 12.4.
($\log$ バリア正則化) 以下の条件を満たす $\theta$ を考える。
$$
\left\|\nabla_\theta L_\lambda(\theta)\right\|_2 \leq \epsilon_{\text {opt }}
$$
かつ $\epsilon_{\text {opt }} \leq \lambda /(2|\mathcal{S}||\mathcal{A}|)$ である。

この場合、すべての**初期状態分布** $\rho$ に対して、以下が成り立つ。

$$
V^{\pi_\theta}(\rho) \geq V^{\star}(\rho)-\frac{2 \lambda}{1-\gamma}\left\|\frac{d_\rho^{\pi^{\star}}}{\mu}\right\|_{\infty}
$$

$\left\|\frac{d_\rho^{\pi^*}}{\mu}\right\|_{\infty}$ を分布不一致係数と呼ぶ。上記の定理は、近似的な 1 次定常点がほぼ最適になるために、適切な尺度 $\mu(s)$ を持つことが重要であることを示している。

##### 直感(変更予定)

この定理は、正則化された目的関数の勾配が十分に小さければ、得られる方策 $\pi_\theta$ の期待収益は、最適な期待収益 $V^*(\rho)$ に近いことを保証しています。

特に、分布不一致係数 $\left\|\frac{d_\rho^{\pi^*}}{\mu}\right\|_{\infty}$ が小さいほど、$V^{\pi_\theta}(\rho)$ は $V^*(\rho)$ に近づきます。これは、最適化に用いる尺度 $\mu$ が、最適方策の状態分布 $d_\rho^{\pi^*}$ に近いほど、良い方策が得られることを意味します。

また、正則化パラメータ $\lambda$ が小さいほど、$V^{\pi_\theta}(\rho)$ は $V^*(\rho)$ に近づきます。しかし、$\lambda$ が小さすぎると、正則化の効果が弱まり、方策が決定論的になりやすくなるため、注意が必要です。

この定理は、logバリア正則化を用いた方策勾配法が、適切な尺度 $\mu$ と正則化パラメータ $\lambda$ を選択することで、任意の初期状態分布 $\rho$ においてほぼ最適な方策を学習できることを示唆しています。


#### 定理12.4の証明
証明は、すべての状態 $s$ に対して、$\max _a A^{\pi_\theta}(s, a) \leq 2 \lambda /(\mu(s)|\mathcal{S}|)$ であることを示すことから成る。これが十分であることを確認するために、パフォーマンス差補題 (補題 1.16) を観察しよう。
$$
\begin{aligned}
V^{\star}(\rho)-V^{\pi_\theta}(\rho) & =\frac{1}{1-\gamma} \sum_{s, a} d_\rho^{\pi^{\star}}(s) \pi^{\star}(a \mid s) A^{\pi_\theta}(s, a) \\
& \leq \frac{1}{1-\gamma} \sum_s d_\rho^{\pi^{\star}}(s) \max _{a \in \mathcal{A}} A^{\pi_\theta}(s, a) \\
& \leq \frac{1}{1-\gamma} \sum_s 2 d_\rho^{\pi^{\star}}(s) \lambda /(\mu(s)|\mathcal{S}|) \\
& \leq \frac{2 \lambda}{1-\gamma} \max _s\left(\frac{d_\rho^{\pi^{\star}}(s)}{\mu(s)}\right) .
\end{aligned}
$$
これにより、証明が完了する。
##### $\max _a A^{\pi \theta}(s, a) \leq 2 \lambda /(\mu(s)|\mathcal{S}|)$の証明
次に、$\max _a A^{\pi \theta}(s, a) \leq 2 \lambda /(\mu(s)|\mathcal{S}|)$ であることを示す。これには、$A^{\pi_\theta}(s, a) \geq 0$ であるすべて状態行動ペア $s, a$ に対して、$A^{\pi \theta}(s, a)$ をバウンドすれば十分である。そうでなければ、主張は自明に成り立つ。
* $A^{\pi \theta}(s, a)$ が0以下である時点で、$2 \lambda /(\mu(s)|\mathcal{S}|)\geq 0$である事実($\lambda$も$\mu$も$|S|$もすべて0以上)から、不等式が自明に成り立つ。

$A^{\pi_\theta}(s, a)>0$ であるような $(s, a)$ ペアを考える。ソフトマックスパラメータ化の方策勾配式 (式 0.1 を参照) を使用すると、$L_{\lambda}(\theta)$の勾配は、

$$
\frac{\partial L_\lambda(\theta)}{\partial \theta_{s, a}}=\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a)+\frac{\lambda}{|\mathcal{S}|}\left(\frac{1}{|\mathcal{A}|}-\pi_\theta(a \mid s)\right) .
$$
となる。
###### **$L_{\lambda}(\theta)$の勾配導出**
$$
L_\lambda(\theta) = V^{\pi_\theta}(\mu) + \frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \sum_{s, a} \log \pi_\theta(a \mid s) + \lambda \log |\mathcal{A}|
$$
を $\theta_{s, a}$ で偏微分すると、

$$
\frac{\partial L_\lambda(\theta)}{\partial \theta_{s, a}} = \frac{\partial V^{\pi_\theta}(\mu)}{\partial \theta_{s, a}} + \frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \sum_{s', a'} \frac{\partial \log \pi_\theta(a' \mid s')}{\partial \theta_{s, a}}
$$
ここで、右辺第一項は、ソフトマックス方策の勾配より、
$$
\frac{\partial V^{\pi_\theta}(\mu)}{\partial \theta_{s, a}} = \frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a)
$$
である。
また、右辺第二項は、ソフトマックス方策の対数勾配より、
$$
\frac{\partial \log \pi_\theta(a' \mid s')}{\partial \theta_{s, a}} = \mathbb{1}[s' = s] (\mathbb{1}[a' = a] - \pi_\theta(a \mid s))
$$
である。これを代入すると、
$$
\begin{aligned}
\frac{\partial L_\lambda(\theta)}{\partial \theta_{s, a}} &= \frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a) + \frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \sum_{s', a'} \mathbb{1}[s' = s] (\mathbb{1}[a' = a] - \pi_\theta(a \mid s)) \\
&= \frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a) + \frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \sum_{a'} (\mathbb{1}[a' = a] - \pi_\theta(a \mid s)) \\
&= \frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a) + \frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \left( \sum_{a'} \mathbb{1}[a' = a] - \sum_{a'} \pi_\theta(a \mid s) \right) \\
&= \frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a) + \frac{\lambda}{|\mathcal{S}||\mathcal{A}|} \left( 1 - |\mathcal{A}|\pi_\theta(a \mid s) \right) \\
&= \frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a) + \frac{\lambda}{|\mathcal{S}|} \left( \frac{1}{|\mathcal{A}|} - \pi_\theta(a \mid s) \right)
\end{aligned}
$$
ここで、最後に前のステップについて、$\pi_{\theta}(a|s)$は$a'$に依存しないので、$\sum_{a' } \pi_\theta(a|s) = |\mathcal{A}| \pi_\theta(a|s)$である。

勾配を用いて、話を進める。勾配ノルムの仮定 $\left\|\nabla_\theta L_\lambda(\theta)\right\|_2 \leq \epsilon_{\text {opt }}$ は、以下を意味する。

$$
\begin{aligned}
\epsilon_{\mathrm{opt}} \geq \frac{\partial L_\lambda(\theta)}{\partial \theta_{s, a}} & =\frac{1}{1-\gamma} d_\mu^{\pi_\theta}(s) \pi_\theta(a \mid s) A^{\pi_\theta}(s, a)+\frac{\lambda}{|\mathcal{S}|}\left(\frac{1}{|\mathcal{A}|}-\pi_\theta(a \mid s)\right) \\
& \geq \frac{\lambda}{|\mathcal{S}|}\left(\frac{1}{|\mathcal{A}|}-\pi_\theta(a \mid s)\right),
\end{aligned}
$$
右辺第第一項は、$A^{\pi_\theta}(s, a) \geq 0$より、0以上なので、この項を0とすれば、右辺全体はは最小値となるため、不等式が成立する。
**勾配ノルムの上界は、各勾配の上界と同義？**
* $A^{\pi_\theta}(s, a) \geq 0$なら成立する？

上の式を変形して、$\epsilon_{\mathrm{opt}} \leq \lambda /(2|\mathcal{S}||\mathcal{A}|)$ という仮定を使用すると、
$$
\pi_\theta(a \mid s) \geq \frac{1}{|\mathcal{A}|}-\frac{\epsilon_{\text {opt }}|\mathcal{S}|}{\lambda} \geq \frac{1}{2|\mathcal{A}|}
$$
となる。
(0.5) で $A^{\pi_\theta}(s, a)$ について解くと、
$$
\begin{aligned}
A^{\pi_\theta}(s, a) & =\frac{1-\gamma}{d_\mu^{\pi_\theta}(s)}\left(\frac{1}{\pi_\theta(a \mid s)} \frac{\partial L_\lambda(\theta)}{\partial \theta_{s, a}}+\frac{\lambda}{|\mathcal{S}|}\left(1-\frac{1}{\pi_\theta(a \mid s)|\mathcal{A}|}\right)\right) \\
& \leq \frac{1-\gamma}{d_\mu^{\pi_\theta}(s)}\left(2|\mathcal{A}| \epsilon_{\mathrm{opt}}+\frac{\lambda}{|\mathcal{S}|}\right) \\
& \leq 2 \frac{1-\gamma}{d_\mu^{\pi_\theta}(s)} \frac{\lambda}{|\mathcal{S}|} \\
& \leq 2 \lambda /(\mu(s)|\mathcal{S}|)
\end{aligned}
$$
ここで、前の最後のステップでは $\epsilon_{\mathrm{opt}} \leq \lambda /(2|\mathcal{S}||\mathcal{A}|)$ であることを使用し、最後のステップでは $d_\mu^{\pi_\theta}(s) \geq(1-\gamma) \mu(s)$ であることを使用している。これで、証明は完了した。

#### $\log$バリア方策勾配の上昇更新
$L_\lambda(\theta)$ の方策勾配上昇更新は、以下で与えられる。
$$
\theta^{(t+1)}=\theta^{(t)}+\eta \nabla_\theta L_\lambda\left(\theta^{(t)}\right) .
$$

上記の定理と勾配上昇の 1 次定常点への収束 (補題 11.6) を組み合わせることで、次の系が得られる。
#### 系12.5
 ($\log$ バリア正則化による反復回数)$\beta_\lambda:=\frac{8 \gamma}{(1-\gamma)^3}+\frac{2 \lambda}{|\mathcal{S}|}$ とする。任意の初期 $\theta^{(0)}$ から開始して、$\lambda=\frac{\epsilon(1-\gamma)}{2\left\|\frac{d_\rho^{\pi^*}}{\mu}\right\|_{\infty}}$ と $\eta=1 / \beta_\lambda$ を持つ更新 (0.6) を考える。この場合、すべて初期状態分布 $\rho$ に対して、以下が成り立つ。
$$
\min _{t<T}\left\{V^{\star}(\rho)-V^{(t)}(\rho)\right\} \leq \epsilon \quad \text { whenever } \quad T \geq \frac{320|\mathcal{S}|^2|\mathcal{A}|^2}{(1-\gamma)^6 \epsilon^2}\left\|\frac{d_\rho^{\pi^{\star}}}{\mu}\right\|_{\infty}^2 .
$$

この系は、グローバルな最適性を実現するために、所望の精度 $\epsilon$ に対して正則化パラメータ $\lambda$ をどのように設定するか、および初期分布 $\mu$ の重要性を示している。

#### 系12.5の証明
##### 滑らかさの補題
[系 12.5 の] $L_\lambda(\theta)$ の滑らかさを $\beta_\lambda$ とする。$\beta_\lambda$ の妥当な上限は、
$$
\beta_\lambda=\frac{8 \gamma}{(1-\gamma)^3}+\frac{2 \lambda}{|\mathcal{S}|},
$$
である。
##### 滑らかさの補題の証明
まず、$V^{\pi_\theta}(\mu)$ の滑らかさの上界を求める。単位ベクトル $u$ を考える。$\theta_s \in \mathbb{R}^{|\mathcal{A}|}$ を、状態 $s$ に関連付けられたパラメータとする。以下の式が成り立つ。

$$
\nabla_{\theta_s} \pi_\theta(a \mid s)=\pi_\theta(a \mid s)\left(e_a-\pi(\cdot \mid s)\right)
$$
および
$$
\nabla_{\theta_s}^2 \pi_\theta(a \mid s)=\pi_\theta(a \mid s)\left(e_a e_a^{\top}-e_a \pi(\cdot \mid s)^{\top}-\pi(\cdot \mid s) e_a^{\top}+2 \pi(\cdot \mid s) \pi(\cdot \mid s)^{\top}-\operatorname{diag}(\pi(\cdot \mid s))\right)
$$

ここで、$e_a$ は標準基底ベクトル、$\pi(\cdot \mid s)$ は確率のベクトルである。また、$\pi_\alpha(a \mid s)$ を $\alpha$ に関して一度微分することで、以下が得られる。

$$
\begin{aligned}
\left.\sum_{a \in \mathcal{A}}\left|\frac{d \pi_\alpha(a \mid s)}{d \alpha}\right|_{\alpha=0} \right\rvert\, & \leq \sum_{a \in \mathcal{A}}\left|u^{\top} \nabla_{\theta+\alpha u} \pi_\alpha(a \mid s)\right|_{\alpha=0} \mid \\
& \leq \sum_{a \in \mathcal{A}} \pi_\theta(a \mid s)\left|u_s^{\top} e_a-u_s^{\top} \pi(\cdot \mid s)\right| \\
& \leq \max _{a \in \mathcal{A}}\left(\left|u_s^{\top} e_a\right|+\left|u_s^{\top} \pi(\cdot \mid s)\right|\right) \leq 2
\end{aligned}
$$

同様に、$\alpha$ に関してもう一度微分すると、

$$
\begin{aligned}
\left.\sum_{a \in \mathcal{A}}\left|\frac{d^2 \pi_\alpha(a \mid s)}{(d \alpha)^2}\right|_{\alpha=0} \right\rvert\, \leq & \sum_{a \in \mathcal{A}}\left|u^{\top} \nabla_{\theta+\alpha u}^2 \pi_\alpha(a \mid s)\right|_{\alpha=0} u \mid \\
\leq & \max _{a \in \mathcal{A}}\left(\left|u_s^{\top} e_a e_a^{\top} u_s\right|+\left|u_s^{\top} e_a \pi(\cdot \mid s)^{\top} u_s\right|+\left|u_s^{\top} \pi(\cdot \mid s) e_a^{\top} u_s\right|\right. \\
& \left.+2\left|u_s^{\top} \pi(\cdot \mid s) \pi(\cdot \mid s)^{\top} u_s\right|+\left|u_s^{\top} \operatorname{diag}(\pi(\cdot \mid s)) u_s\right|\right) \\
\leq & 6
\end{aligned}
$$

これを、$C_1=2$、$C_2=6$ として補題D.2に適用すると、

$$
\max _{\|u\|_2=1}\left|\frac{d^2 \tilde{V}(\alpha)}{(d \alpha)^2}\right|_{\alpha=0} \left\lvert\, \leq \frac{C_2}{(1-\gamma)^2}+\frac{2 \gamma C_1^2}{(1-\gamma)^3} \leq \frac{6}{(1-\gamma)^2}+\frac{8 \gamma}{(1-\gamma)^3} \leq \frac{8}{(1-\gamma)^3}\right.
$$

または、それと等価的に、すべての初期状態 $s$、したがってすべての初期状態分布 $\mu$ に対して、

$$
\left\|\nabla_\theta V^{\pi_\theta}(\mu)-\nabla_\theta V^{\pi_{\theta^{\prime}}}(\mu)\right\|_2 \leq \beta\left\|\theta-\theta^{\prime}\right\|_2
$$

ここで、$\beta=\frac{8}{(1-\gamma)^3}$ である。

次に、正則化項 $\frac{\lambda}{|\mathcal{S}|} R(\theta)$ の滑らかさの上界を求める。ここで、

$$
R(\theta):=\frac{1}{|\mathcal{A}|} \sum_{s, a} \log \pi_\theta(a \mid s)
$$

である。

以下が成り立つ。

$$
\frac{\partial R(\theta)}{\partial \theta_{s, a}}=\frac{1}{|\mathcal{A}|}-\pi_\theta(a \mid s)
$$

それと等価的に、

$$
\nabla_{\theta_s} R(\theta)=\frac{1}{|\mathcal{A}|} \mathbf{1}-\pi_\theta(\cdot \mid s)
$$

したがって、

$$
\nabla_{\theta_s}^2 R(\theta)=-\operatorname{diag}\left(\pi_\theta(\cdot \mid s)\right)+\pi_\theta(\cdot \mid s) \pi_\theta(\cdot \mid s)^{\top}
$$

任意のベクトル $u_s$ に対して、

$$
\left|u_s^{\top} \nabla_{\theta_s}^2 R(\theta) u_s\right|=\left|u_s^{\top} \operatorname{diag}\left(\pi_\theta(\cdot \mid s)\right) u_s-\left(u_s \cdot \pi_\theta(\cdot \mid s)\right)^2\right| \leq 2\left\|u_s\right\|_{\infty}^2
$$

$s \neq s^{\prime}$ に対して $\nabla_{\theta_s} \nabla_{\theta_{s^{\prime}}} R(\theta)=0$ であるため、

$$
\left|u^{\top} \nabla_\theta^2 R(\theta) u\right|=\left|\sum_s u_s^{\top} \nabla_{\theta_s}^2 R(\theta) u_s\right| \leq 2 \sum_s\left\|u_s\right\|_{\infty}^2 \leq 2\|u\|_2^2
$$

したがって、$R$ は2滑らかであり、$\frac{\lambda}{|\mathcal{S}|} R$ は $\frac{2 \lambda}{|\mathcal{S}|}$ 滑らかである。これで証明は完了する。


定理 12.4 を使用すると、$\lambda=\frac{\epsilon(1-\gamma)}{2 \| \frac{d_\rho^{\pi^*}}{\mu}\|_{\infty}}$ とし、$\left\|\nabla_\theta L_\lambda(\theta)\right\|_2 \leq$ $\lambda /(2|\mathcal{S}||\mathcal{A}|)$ であれば、所望の最適性ギャップ $\epsilon$ が得られる。証明を完了するために、勾配を十分に小さくするのに必要な反復回数をバウンドする必要がある。

補題 11.6 により、ステップサイズ $1 / \beta_\lambda$ を持つ勾配上昇を $T$ 回反復実行すると、
$$
\min _{t \leq T}\left\|\nabla_\theta L_\lambda\left(\theta^{(t)}\right)\right\|_2^2 \leq \frac{2 \beta_\lambda\left(L_\lambda\left(\theta^{\star}\right)-L_\lambda\left(\theta^{(0)}\right)\right)}{T} \leq \frac{2 \beta_\lambda}{(1-\gamma) T},
$$
が成り立つ。ここで、$L_\lambda(\theta^*) - L_\lambda(\theta^{(0)}) \le \frac{1}{1-\gamma}$であるらしい。ここで、$\beta_\lambda$ は $L_\lambda(\theta)$ の滑らかさの上限である。

以下を保証することを目指す。
$$
\epsilon_{\text {opt }} \leq \sqrt{\frac{2 \beta_\lambda}{(1-\gamma) T}} \leq \frac{\lambda}{2|\mathcal{S}||\mathcal{A}|}
$$

$T \geq \frac{8 \beta_\lambda|\mathcal{S}|^2|\mathcal{A}|^2}{(1-\gamma) \lambda^2}$ を選択すると、上記の不等式が満たされる。したがって、
$$
\begin{aligned}
\frac{8 \beta_\lambda|\mathcal{S}|^2|\mathcal{A}|^2}{(1-\gamma) \lambda^2} & \leq \frac{64|\mathcal{S}|^2|\mathcal{A}|^2}{(1-\gamma)^4 \lambda^2}+\frac{16|\mathcal{S}||\mathcal{A}|^2}{(1-\gamma) \lambda} \\
& \leq \frac{80|\mathcal{S}|^2|\mathcal{A}|^2}{(1-\gamma)^4 \lambda^2} \\
& =\frac{320|\mathcal{S}|^2|\mathcal{A}|^2}{(1-\gamma)^6 \epsilon^2}\left\|\frac{d_\rho^{\pi^*}}{\mu}\right\|_{\infty}^2
\end{aligned}
$$
ここで、最初の不等式は$\beta_\lambda=\frac{8 \gamma}{(1-\gamma)^3}+\frac{2 \lambda}{|\mathcal{S}|}$を代入して上界を取ったものである。
この上界は、右辺第二項にかけた値$\frac{|\mathcal{S}|}{(1-\gamma)^4 \lambda}\geq 1$であることを用いている。なお、$\lambda<1$ である。最後の等式は、$\lambda=\frac{\epsilon(1-\gamma)}{2 \| \frac{d_\rho^{\pi^*}}{\mu}\|_{\infty}}$を代入しているだけ。これで、証明は完了した。

#### エントロピーと $\log$ バリア正則化の比較
一般的に使用されている正則化項はエントロピーであり、正則化項は以下で与えられる。
$$
\frac{1}{|\mathcal{S}|} \sum_s H\left(\pi_\theta(\cdot \mid s)\right)=\frac{1}{|\mathcal{S}|} \sum_s \sum_a-\pi_\theta(a \mid s) \log \pi_\theta(a \mid s) .
$$

エントロピーは、小さな確率をペナルティ化する際に、$\log$ バリア (相対エントロピーと同等) と比較してはるかに攻撃的ではないことに注意する。
* ペナルティは正則化項のことね

特に、**エントロピー正則化項は常に 0 から $\log |\mathcal{A}|$ の間にバウンドされる**一方、**相対エントロピー (行動に対する均一分布に対して) は 0 から無限大の間にバウンド**され、**確率が 0 に近づくにつれて無限大になる**。ここでは、収束速度は漸近的に $O(1 \epsilon)$ であることが示されている (12.5 節を参照)。ただし、この方法の収束速度が、$|\mathcal{S}|,|\mathcal{A}|, 1 /(1-\gamma)$、および分布不一致係数などの他の関連する量に対して多項式である可能性は低い。$\log$ バリア (KL) 正則化項を使用した多項式的な収束速度は、相対エントロピーが小さな確率を防ぐ攻撃的な性質に大きく依存している。

### 12.4 自然方策勾配
#### イントロダクション
方策は、分布の族 $\left\{\pi_\theta(\cdot \mid s) \mid s \in \mathcal{S}\right\}$ を構成することに注意する。ここでは、この分布族に基づいた、事前条件付けされた勾配降下法を考える。パラメータ化された密度 $p_\theta(x)$ のフィッシャー情報行列は、$\mathbb{E}_{x \sim p_\theta}\left[\nabla \log p_\theta(x) \nabla \log p_\theta(x)^{\top}\right]$ で定義されていることを思い出そう。期待値を展開すると、
$$
F(\theta) = \sum_x p_\theta(x) \nabla_\theta \log p_\theta(x) \nabla_\theta \log p_\theta(x)^\top
$$
ここで、分布族 $\left\{\pi_\theta(\cdot \mid s) \mid s \in \mathcal{S}\right\}$ の (平均) フィッシャー情報行列を $\mathcal{F}_\rho^\theta$ として、以下のように定義する。
$$
\mathcal{F}_\rho^\theta:=\mathbb{E}_{s \sim d_\rho^{\pi_\theta}} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s)}\left[\left(\nabla \log \pi_\theta(a \mid s)\right) \nabla \log \pi_\theta(a \mid s)^{\top}\right] .
$$
平均は、状態行動の訪問頻度($d_\rho^{\pi_\theta}(s) \pi_\theta(a|s)$)に対して取られることに注意する。期待値展開すると、以下のようになる。
$$
\mathcal{F}_\rho^\theta = \sum_{s \in \mathcal{S}} d_\rho^{\pi_\theta}(s) \sum_{a \in \mathcal{A}} \pi_\theta(a | s) \left(\nabla \log \pi_\theta(a \mid s)\right) \nabla \log \pi_\theta(a \mid s)^{\top}
$$

#### NPGの更新規則
NPG アルゴリズムは、以下のように、この行列によって誘導されたジオメトリで勾配更新を実行する。
$$
\theta^{(t+1)}=\theta^{(t)}+\eta F_\rho\left(\theta^{(t)}\right)^{\dagger} \nabla_\theta V^{(t)}(\rho),
$$
ここで、$M^{\dagger}$ は行列 $M$ のムーア・ペンローズ擬似逆行列を表す。このセクション全体では、更新規則 (0.8) で**初期状態分布 $\rho \in \Delta(\mathcal{S})$ を使用することに限定**する (そのため、最適化尺度 $\mu$ とパフォーマンス尺度 $\rho$ は同一になる)。また、**$\rho$ から到達可能な状態 $s \in \mathcal{S}$ にのみ着目**する。なぜなら、一般性を失うことなく、この初期状態分布下で到達できない状態を除外できるからである。 ${ }^2$

ソフトマックスパラメータ化の場合、この方法(更新規則)は特に便利な形式を取る。これは、ソフトポリシー反復更新と見なすことができる。

#### 補題 12.6. 
(ソフトポリシー反復としてのソフトマックス NPG)
ソフトマックスパラメータ化 (0.1) の場合、NPG 更新 (0.8) は以下の形式になる。
$$
\theta^{(t+1)}=\theta^{(t)}+\frac{\eta}{1-\gamma} A^{(t)}+\eta v \quad \text { and } \quad \pi^{(t+1)}(a \mid s)=\pi^{(t)}(a \mid s) \frac{\exp \left(\eta A^{(t)}(s, a) /(1-\gamma)\right)}{Z_t(s)},
$$
ここで、$Z_t(s)=\sum_{a \in \mathcal{A}} \pi^{(t)}(a \mid s) \exp \left(\eta A^{(t)}(s, a) /(1-\gamma)\right)$ である。ここで、$v$ は、状態に依存するオフセットのみである (つまり、各状態 $s$ に対して $v_{s, a}=c_s$ (ただし、$c_s \in \mathbb{R}$) となる)。また、正規化 $Z_t(s)$ により、$v$ は更新規則に影響を与えない。

**疑惑の文章**
$\rho$ に依存する勾配 $\nabla_\theta V^{(t)}(\rho)$ を使用して上昇方向が導出された一方で、**NPG 更新規則は実際には尺度 $\rho$ に依存しない**ことに注意することが重要である。さらに、**状態分布 $d_\rho^{(t)}$ にも依存しない**。これは、フィッシャー情報行列の擬似逆行列が、NPG の**状態分布の影響を打ち消す**ためである。

#### 補題12.6の証明
ムーア・ペンローズ擬似逆行列の定義により、$\left(\mathcal{F}_\rho^\theta\right)^{\dagger} \nabla V^{\pi_\theta}(\rho)=w_{\star}$ となるのは、$w_{\star}$ が以下の最小ノルム解である場合のみである。
$$
\min _w\left\|\nabla V^{\pi_\theta}(\rho)-\mathcal{F}_\rho^\theta w\right\|^2
$$
まず、$\mathcal{F}_\rho^\theta w$ を評価する。ソフトマックス方策パラメータ化の場合、補題 0.1 は以下を意味する。
$$
w^{\top} \nabla_\theta \log \pi_\theta(a \mid s)=w_{s, a}-\sum_{a^{\prime} \in \mathcal{A}} w_{s, a^{\prime}} \pi_\theta\left(a^{\prime} \mid s\right):=w_{s, a}-\bar{w}_s
$$
最初の等式は、以下のようになる。
$$
\begin{aligned}
w^\top \nabla_\theta \log \pi_\theta(a|s) &= \sum_{s', a'} w_{s', a'} \frac{\partial \log \pi_\theta(a|s)}{\partial \theta_{s', a'}} \\
&= \sum_{s', a'} w_{s', a'} \mathbb{1}[s=s'] (\mathbb{1}[a=a'] - \pi_\theta(a'|s)) \\
&= \sum_{a'} w_{s, a'} (\mathbb{1}[a=a'] - \pi_\theta(a'|s)) \\
&= \sum_{a'} w_{s, a'} \mathbb{1}[a=a'] - \sum_{a'} w_{s, a'} \pi_\theta(a'|s) \\
&= w_{s, a} - \sum_{a'} w_{s, a'} \pi_\theta(a'|s)
\end{aligned}
$$
ここで、$\bar{w}_s$ は $a$ の関数ではない。これは、以下を意味する。
$$
\begin{aligned}
\mathcal{F}_\rho^\theta w & =\mathbb{E}_{s \sim d_\rho^{\pi_\theta}} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s)}\left[\nabla \log \pi_\theta(a \mid s)\left(w^{\top} \nabla_\theta \log \pi_\theta(a \mid s)\right)\right] \\
& =\mathbb{E}_{s \sim d_\rho^{\pi_\theta}} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s)}\left[\nabla \log \pi_\theta(a \mid s)\left(w_{s, a}-\bar{w}_s\right)\right]=\mathbb{E}_{s \sim d_\rho^{\pi_\theta}} \mathbb{E}_{a \sim \pi \theta(\cdot \mid s)}\left[w_{s, a} \nabla \log \pi_\theta(a \mid s)\right],
\end{aligned}
$$
ここで、最後の等式では、**$\bar{w}_s$ が $s$ の関数ではない**ことを使用している。ソフトマックス方策パラメータ化の導関数の関数形式(特定の$s'$、$a'$に関するFwの値の形式)を再び使用すると、
$$
\left[\mathcal{F}_\rho^\theta w\right]_{s^{\prime}, a^{\prime}}=d^{\pi_\theta}\left(s^{\prime}\right) \pi_\theta\left(a^{\prime} \mid s^{\prime}\right)\left(w_{s^{\prime}, a^{\prime}}-\bar{w}_{s^{\prime}}\right) .
$$
これは、以下を意味する。
$$
\begin{aligned}
\left\|\nabla V^{\pi_\theta}(\rho)-\mathcal{F}_\rho^\theta w\right\|^2 & =\sum_{s, a}\left(d^{\pi_\theta}(s) \pi_\theta(a \mid s)\left(\frac{1}{1-\gamma} A^{\pi \theta}(s, a)-\left[\mathcal{F}_\rho^\theta w\right]_{s, a}\right)\right)^2 \\
& =\sum_{s, a}\left(d^{\pi \theta}(s) \pi_\theta(a \mid s)\left(\frac{1}{1-\gamma} A^{\pi \theta}(s, a)-w_{s, a}-\sum_{a^{\prime} \in \mathcal{A}} w_{s, a^{\prime}} \pi_\theta\left(a^{\prime} \mid s\right)\right)\right)^2 .
\end{aligned}
$$

$w=\frac{1}{1-\gamma} A^{\pi \theta}$ が 0 エラーとなるため、上記は、すべての 0 エラー解が $w=\frac{1}{1-\gamma} A^{\pi \theta}+v$ の形式であることを意味する。
* $w$こそがパラメータである。パラメータを$w=\frac{1}{1-\gamma} A^{\pi \theta}+v$の形式にできれば、誤差を0にできる。

ここで、$v$ は、状態に依存するオフセットのみである (つまり、各状態 $s$ に対して $v_{s, a}=c_s$ (ただし、$c_s \in \mathbb{R}$) となる)。最小ノルム解はこれらの解の 1 つであるため、最初の主張が成り立つ。2 番目の主張の証明は、NPG更新規則の定義から、および $v$ は**正規化定数 $Z_t(s)$ により更新規則に影響を与えない**ことから明らかである。


ここで、このアルゴリズムが次元フリーの収束速度を持つことを確認する。
#### 定理 12.7 
(NPG のグローバルな収束)。$\rho \in \Delta(\mathcal{S})$ を使用して、かつ $\theta^{(0)}=0$ で NPG 更新 (0.8) を実行すると仮定する。$\eta>0$ を固定する。すべての $T>0$ に対して、以下が成り立つ。
$$
V^{(T)}(\rho) \geq V^*(\rho)-\frac{\log |\mathcal{A}|}{\eta T}-\frac{1}{(1-\gamma)^2 T} .
$$

上記で、NPG アルゴリズムは、パフォーマンス尺度 $V^\pi(\rho)$ に直接適用され、保証も $\rho$ に関して行われていることに注意する。特に、収束速度に分布不一致係数は存在しない。

次に、$\eta \geq(1-\gamma)^2 \log |\mathcal{A}|$ と設定すると、NPG は、以下の反復回数で $\epsilon$ 最適な方策を見つけることがわかる。
$$
T \leq \frac{2}{(1-\gamma)^2 \epsilon},
$$
これは、基礎となる最適化問題の非凸性にもかかわらず、**状態数や行動数に依存しない**。採用する証明戦略は、古典的な乗法重みアルゴリズム (12.5 節を参照) のアイデアを借用している。

まず、次の改善補題が役立つ。
#### 補題 12.8 (NPG の改善下限)
NPG 更新 (0.8) によって生成される反復 $\pi^{(t)}$ に対して、すべて初期状態分布 $\mu$ について、以下が成り立つ。
$$
V^{(t+1)}(\mu)-V^{(t)}(\mu) \geq \frac{(1-\gamma)}{\eta} \mathbb{E}_{s \sim \mu} \log Z_t(s) \geq 0 .
$$

#### 補題12.8の証明
まず、$\log Z_t(s) \geq 0$ であることを示す。これを確認するために、以下を観察する。
$$
\begin{aligned}
\log Z_t(s)=\log \sum_a \pi^{(t)}(a \mid s) & \exp \left(\eta A^{(t)}(s, a) /(1-\gamma)\right) \\
& \geq \sum_a \pi^{(t)}(a \mid s) \log \exp \left(\eta A^{(t)}(s, a) /(1-\gamma)\right)=\frac{\eta}{1-\gamma} \sum_a \pi^{(t)}(a \mid s) A^{(t)}(s, a)=0 .
\end{aligned}
$$
ここで、凹関数 $\log x$ に対するイェンセンの不等式$\log \left( \sum_a \pi^{(t)}(a|s) \exp\left( \frac{\eta A^{(t)}(s,a)}{1-\gamma} \right) \right) \ge \sum_a \pi^{(t)}(a|s) \log \left( \exp\left( \frac{\eta A^{(t)}(s,a)}{1-\gamma} \right) \right)$と、$\sum_a \pi^{(t)}(a \mid s) A^{(t)}(s, a)=0$ であることを使用している。

次にパフォーマンス差補題により、
$$
\begin{aligned}
V^{(t+1)}(\mu)-V^{(t)}(\mu) & =\frac{1}{1-\gamma} \mathbb{E}_{s \sim d_\mu^{(t+1)}} \sum_a \pi^{(t+1)}(a \mid s) A^{(t)}(s, a) \\
& =\frac{1}{\eta} \mathbb{E}_{s \sim d_\mu^{(t+1)}} \sum_a \pi^{(t+1)}(a \mid s) \log \frac{\pi^{(t+1)}(a \mid s) Z_t(s)}{\pi^{(t)}(a \mid s)} \\
& =\frac{1}{\eta} \mathbb{E}_{s \sim d_\mu^{(t+1)}} \operatorname{KL}\left(\pi_s^{(t+1)}|| \pi_s^{(t)}\right)+\frac{1}{\eta} \mathbb{E}_{s \sim d_\mu^{(t+1)}} \log Z_t(s) \\
& \geq \frac{1}{\eta} \mathbb{E}_{s \sim d_\mu^{(t+1)}} \log Z_t(s) \geq \frac{1-\gamma}{\eta} \mathbb{E}_{s \sim \mu} \log Z_t(s),
\end{aligned}
$$
ここで、最初のステップでは、勾配更新の式から、
$$
A^{(t)}(s,a) = \frac{1-\gamma}{\eta} \left( \log \frac{\pi^{(t+1)}(a|s)}{\pi^{(t)}(a|s)} + \log Z_t(s) \right) = \frac{1-\gamma}{\eta} \log \frac{\pi^{(t+1)}(a|s)Z_t(s)}{\pi^{(t)}(a|s)}
$$
であることを利用する。
その次のステップでは、KLダイバージェンスの定義を用いている。$Z_t$は対数の性質から足し算として外に出る。
$$
\operatorname{KL}(\pi_s^{(t+1)} || \pi_s^{(t)}) = \sum_a \pi^{(t+1)}(a \mid s) \log \frac{\pi^{(t+1)}(a \mid s)}{\pi^{(t)}(a \mid s)}
$$
その次の不等式はKLダイバージェンスが0以上であるという事実を用いる。
最後のステップでは、$d_\mu^{(t+1)} \geq(1-\gamma) \mu$ (0.9 により) と、$\log Z_t(s) \geq 0$ であることを使用している。

この補題を使用して、次に定理 12.7 を証明する。
#### 定理12.7の証明:
 $\rho$ は固定されているため、$d^{\star}$ を $d_\rho^{\pi^{\star}}$ の略記として使用する。また、$\pi_s$ を $\pi(\cdot \mid s)$ のベクトルの略記として使用する。パフォーマンス差補題 (補題 1.16) により、
$$
\begin{aligned}
V^{\pi^{\star}}(\rho) & -V^{(t)}(\rho)=\frac{1}{1-\gamma} \mathbb{E}_{s \sim d^{\star}} \sum_a \pi^{\star}(a \mid s) A^{(t)}(s, a) \\
& =\frac{1}{\eta} \mathbb{E}_{s \sim d^{\star}} \sum_a \pi^{\star}(a \mid s) \log \frac{\pi^{(t+1)}(a \mid s) Z_t(s)}{\pi^{(t)}(a \mid s)} \\
& =\frac{1}{\eta} \mathbb{E}_{s \sim d^{\star}}\left(\mathrm{KL}\left(\pi_s^{\star}|| \pi_s^{(t)}\right)-\mathrm{KL}\left(\pi_s^{\star}|| \pi_s^{(t+1)}\right)+\sum_a \pi^*(a \mid s) \log Z_t(s)\right) \\
& =\frac{1}{\eta} \mathbb{E}_{s \sim d^{\star}}\left(\mathrm{KL}\left(\pi_s^{\star}|| \pi_s^{(t)}\right)-\mathrm{KL}\left(\pi_s^{\star}|| \pi_s^{(t+1)}\right)+\log Z_t(s)\right),
\end{aligned}
$$
ここで、2 番目のステップで補題 12.6 の更新式の閉形式を使用している。
3番目のステップは、
$$
\frac{1}{\eta} \mathbb{E}_{s \sim d^*} \sum_a \pi^*(a|s) \left[ \log \frac{\pi^{(t+1)}(a|s)}{\pi^{(t)}(a|s)} + \log Z_t(s) \right]
$$
と分解し、
$$
\frac{1}{\eta} \mathbb{E}_{s \sim d^*} \left[ \sum_a \pi^*(a|s) \log \frac{\pi^{(t+1)}(a|s)}{\pi^{(t)}(a|s)} + \sum_a \pi^*(a|s) \log Z_t(s)  \right]
$$
と変形する。ここで、
$$
\begin{aligned}
\sum_a \pi^*(a|s) \log \frac{\pi^{(t+1)}(a|s)}{\pi^{(t)}(a|s)} &= \sum_a \pi^*(a|s) \log \frac{\pi^*(a|s)}{\pi^{(t)}(a|s)} - \sum_a \pi^*(a|s) \log \frac{\pi^*(a|s)}{\pi^{(t+1)}(a|s)} \\
&= \text{KL}(\pi_s^* || \pi_s^{(t)}) - \text{KL}(\pi_s^* || \pi_s^{(t+1)})
\end{aligned}
$$
を用いる。

補題 12.8 を初期状態分布として $d^{\star}$ を使用して適用すると、右辺第三項は、
$$
\frac{1}{\eta} \mathbb{E}_{s \sim d^{\star}} \log Z_t(s) \leq \frac{1}{1-\gamma}\left(V^{(t+1)}\left(d^{\star}\right)-V^{(t)}\left(d^{\star}\right)\right)
$$
が得られ、$\mathbb{E}_{s \sim d^*} \log Z_t(s)$ の上限が得られる。

上記の式と、$V^{(t+1)}(\rho) \geq V^{(t)}(\rho)$ (補題 12.8 により、すべて状態 $s$ に対して $V^{(t+1)}(s) \geq V^{(t)}(s)$ であるため) を使用すると、
$$
\begin{aligned}
V^{\pi^{\star}}(\rho) & -V^{(T-1)}(\rho) \leq \frac{1}{T} \sum_{t=0}^{T-1}\left(V^{\pi^{\star}}(\rho)-V^{(t)}(\rho)\right) \\
& =\frac{1}{\eta T} \sum_{t=0}^{T-1} \mathbb{E}_{s \sim d^{\star}}\left(\mathrm{KL}\left(\pi_s^{\star}|| \pi_s^{(t)}\right)-\mathrm{KL}\left(\pi_s^{\star}|| \pi_s^{(t+1)}\right)\right)+\frac{1}{\eta T} \sum_{t=0}^{T-1} \mathbb{E}_{s \sim d^{\star}} \log Z_t(s) \\
& \leq \frac{\mathbb{E}_{s \sim d^{\star}} \mathrm{KL}\left(\pi_s^{\star}|| \pi^{(0)}\right)}{\eta T}+\frac{1}{(1-\gamma) T} \sum_{t=0}^{T-1}\left(V^{(t+1)}\left(d^{\star}\right)-V^{(t)}\left(d^{\star}\right)\right) \\
& =\frac{\mathbb{E}_{s \sim d^{\star}} \mathrm{KL}\left(\pi_s^{\star}|| \pi^{(0)}\right)}{\eta T}+\frac{V^{(T)}\left(d^{\star}\right)-V^{(0)}\left(d^{\star}\right)}{(1-\gamma) T} \\
& \leq \frac{\log |\mathcal{A}|}{\eta T}+\frac{1}{(1-\gamma)^2 T} .
\end{aligned}
$$
最初の不等式は、  
$$
   \begin{aligned}
   \sum_{t=0}^{T-1} \left( \text{KL}(\pi_s^* || \pi_s^{(t)}) - \text{KL}(\pi_s^* || \pi_s^{(t+1)}) \right) &= \text{KL}(\pi_s^* || \pi_s^{(0)}) - \text{KL}(\pi_s^* || \pi_s^{(1)}) + \text{KL}(\pi_s^* || \pi_s^{(1)}) - \text{KL}(\pi_s^* || \pi_s^{(2)}) + \dots \\
   &\quad + \text{KL}(\pi_s^* || \pi_s^{(T-1)}) - \text{KL}(\pi_s^* || \pi_s^{(T)}) \\
   &= \text{KL}(\pi_s^* || \pi_s^{(0)}) - \text{KL}(\pi_s^* || \pi_s^{(T)})
   \end{aligned}
   $$
 KLダイバージェンスは非負なので、$\text{KL}(\pi_s^* || \pi_s^{(T)}) \ge 0$ である。よって、$$
   \sum_{t=0}^{T-1} \left( \text{KL}(\pi_s^* || \pi_s^{(t)}) - \text{KL}(\pi_s^* || \pi_s^{(t+1)}) \right) \le \text{KL}(\pi_s^* || \pi_s^{(0)})
   $$   となる。


$V^{(T)}(\rho) \geq V^{(T-1)}(\rho)$ を使用することで、証明は完了した。



12.5 参考文献とさらなる調査

自然方策勾配法は、もともと [Kakade, 2001] で発表されました。この方法の多くの議論は、情報幾何学に基づいています [Kakade, 2001, Bagnell and Schneider, 2003, Peters and Schaal, 2008]。

この章の収束速度は、主に [Agarwal et al., 2020d] から得られています。NPG 解析の証明戦略は、変化する MDP におけるオンライン後悔枠組み [Even-Dar et al., 2009] に由来しており、[Agarwal et al., 2020d] と比較して、最悪の速度をもたらします。[Even-Dar et al., 2009] の証明戦略が NPG の収束速度を提供するという観察は、[Neu et al., 2017] で行われました。ここで示すより高速な NPG 速度は、[Agarwal et al., 2020d] によるものです。MD-MPI アルゴリズムの解析 [Geist et al., 2019] も、NPG の $O(1 / T)$ 速度を意味しますが、他のパラメータに対する依存性が悪くなっています。
[Agarwal et al., 2020d] のアイデアに基づいて、[Mei et al., 2020] は、ソフトマックス方策クラスに対して、勾配上昇とエントロピー正則化された勾配上昇の両方が漸近的に $O(1 / t)$ で収束することを示しました。これらの方法は、$|\mathcal{S}|,|\mathcal{A}|, 1 /(1-\gamma)$、および分布不一致係数などの量に対して多項式である有限速度を持つ可能性は低いと考えられます。
[Mnih et al., 2016] は、エントロピー正則化項を導入しています (より詳細な経験的調査については、[Ahmed et al., 2019] を参照)。

