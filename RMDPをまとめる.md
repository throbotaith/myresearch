## RMDP

MDPにおける遷移関数モデルや報酬関数モデルのパラメータが正確に把握できない状況により、各モデルに不確実性が生じる場合を想定します。RMDPは、モデルの不確実性を考慮しながら最適方策を求めるアプローチの1つです。RMDPは、真の遷移関数が不確実性集合という遷移関数の集合の中に属すると仮定し、不確実性集合内の最悪ケース(総報酬が低くなるor総コストが高くなる)遷移関数において、報酬が高くなるor総コストが低くなる方策(ロバスト方策)を求めます。



## 不確実性集合
不確実性集合に仮定を置かない一般的なRMDPを解くことはNP困難になる可能性があります(Wiesemann et al., 2013)[75]。これに対し、不確実性集合に対して$(s,a)or(s)$-rectangular、という仮定を置くことを考えます。仮定により、動的計画法を用いてRMDPを解くことが可能となり、near-optimalなロバスト方策を獲得することができます。

### 仮定：$(s,a),(s)$-rectangular

**$(s,a)$-rectangular**
不確実性集合を$\mathcal{P}$と仮定します。以下を満たす場合、$\mathcal{P}$は$(s,a)$-rectangular setsと呼ばれます。$X$は直積を示しています。$\mathcal{P}_{s, a} \subseteq \Delta(\mathcal{S})$(状態に対する確率単体の部分集合)

$$
\mathcal{P}=\underset{(s, a) \in \mathcal{S} \times \mathcal{A}}{X} \mathcal{P}_{s, a}
$$
**解説と例**
$\mathcal{P}_{s, a}$は、$s,a \in \mathcal{S},{A}$を入力した場合に、$s\in S$の確率を出力する関数の集合ととらえることができます。$\underset{(s, a) \in \mathcal{S} \times \mathcal{A}}{X} \mathcal{P}_{s, a}$は任意の$s,a$に対して$s\in S$の確率を出力する関数の集合が存在するということを意味しています。
$|S|=2,|A|=2$を考えます。$\mathcal{P}_{1, 1},\mathcal{P}_{1, 2},\mathcal{P}_{2, 1},\mathcal{P}_{2, 2} \subseteq \Delta(\mathcal{S})$とします。
- $\mathcal{P}_{1,1} = {(P(1|1,1)=0.1, P(2|1,1)=0.9), (P(1|1,1)=0.7, P(2|1,1)=0.3)}$
- $\mathcal{P}_{1,2} = {(P(1|1,2)=0.4, P(2|1,2)=0.6), (P(1|1,2)=0.8, P(2|1,2)=0.2)}$
- $\mathcal{P}_{2,1} = {(P(1|2,1)=0.5, P(2|2,1)=0.5), (P(1|2,1)=0.6, P(2|2,1)=0.4)}$
- $\mathcal{P}_{2,2} = {(P(1|2,2)=0.3, P(2|2,2)=0.7), (P(1|2,2)=0.2, P(2|2,2)=0.8)}$
のようにあらわされます。

**$(s)$-rectangular**
$\mathcal{P}_{s} \subseteq \Delta(\mathcal{S})^{|\mathcal{A}|}$ であり、$\Delta(\mathcal{S})^{|\mathcal{A}|}:=\left\{\left(P_{a}\right)_{a \in \mathcal{A}} \mid P_{a} \in \Delta(\mathcal{S})\right.$, for all $\left.a \in \mathcal{A}\right\}$
とします。同様に、以下を満たす場合、$\mathcal{P}$は$(s)$-rectangular setsと呼ばれます。
$$
\mathcal{P}=\underset{s \in \mathcal{S}}{X} \mathcal{P}_{s}
$$
**解説と例**
$\mathcal{P}_{s}$は任意の$s$を入力とし、その$s$のうえで実行できる$a$について、その遷移確率すべてを要素とした集合の部分集合といえます。各 $\mathcal{P}_{s} \subseteq \Delta(\mathcal{S})^{|\mathcal{A}|}$ は次のように表されます：
- $\mathcal{P}_{1} = {((P(1|1,1)=0.1, P(2|1,1)=0.9), (P(1|1,2)=0.4, P(2|1,2)=0.6)),\\((P(1|1,1)=0.7, P(2|1,1)=0.3), (P(1|1,2)=0.8, P(2|1,2)=0.2))}$
- $\mathcal{P}_{2} = {((P(1|2,1)=0.5, P(2|2,1)=0.5), (P(1|2,2)=0.3, P(2|2,2)=0.7)),\ ((P(1|2,1)=0.6, P(2|2,1)=0.4), (P(1|2,2)=0.2, P(2|2,2)=0.8))}$

### 不確実性集合の例




#### (コラム)なぜ不確実性集合に仮定を置かないとNP困難なのか



## RMDPを解く(関連研究)


### 動的計画法

(Iyengar, 2005; Nilim & El Ghaoui, 2005; Kaufman & Schaefer, 2013; Ho et al., 2021)

### 非凸二重ループアルゴリズム(RMDPではない)

Stochastic Recursive Gradient Descent Ascent for Stochastic Nonconvex-Strongly-Concave Minimax Problems

Solving a Class of Non-Convex Min-Max Games Using Iterative First Order Methods

RMDPにおける最適方策を求めるためには、ミニマックス問題を解く必要があります。ミニマックス問題はゲーム理論におけるゼロサムゲームに対応します。ミニマックス問題の目標はナッシュ均衡となる鞍点を求めることです。最適化したい関数が2変数について、それぞれ凸かつ凹であればナッシュ均衡を計算することができます。一方でRMDPは最適化したい目標関数である状態価値関数が非凸であるため、困難です。一般的に、目的関数が非凸のミニマックス問題を解くことはNP困難です。この困難に対して、RMDPの研究では、非凸二重ループアルゴリズムの概念を用いてロバスト最適方策を求めるアルゴリズムを研究しています。







(Jin et al., 2020; Luo et al., 2020; Razaviyayn et al., 2020; Zhang et al., 2020）
* 内側ループを一定以上の精度で解ければ、rectangluarの仮定がなくても最適方策を学習できます(←まじ？)。
### 方策勾配法 for RMDP


最初に、MDPを以下のようなタプルで定義します。
$\langle\mathcal{S}, \mathcal{A}, p, c, \gamma, \rho\rangle$
*   $\mathcal{S} = \{1, 2, \cdots, S\}$: 有限状態集合 
*   $\mathcal{A} = \{1, 2, \cdots, A\}$: 有限行動集合 
*   $p = (\boldsymbol{p}_{s a})_{s \in \mathcal{S}, a \in \mathcal{A}} \in (\Delta^S)^{S \times A}$: 遷移確率カーネル
*   $c_{s a s'}$ for each $(s, a, s') \in \mathcal{S} \times \mathcal{A} \times \mathcal{S}$: コスト関数 
*   $\gamma \in (0, 1)$: 割引率
*   $\rho \in \Delta^S$: 初期状態分布 
*   $\Delta^S$:  $S$次元確率単体

次のようなミニマックス問題を考えます。
$$
\min _{\boldsymbol{\pi} \in \Pi}\left\{\Phi(\boldsymbol{\pi}):=\max _{\boldsymbol{p} \in \mathcal{P}} J_{\boldsymbol{\rho}}(\boldsymbol{\pi}, \boldsymbol{p})\right\} \tag{4}.
$$
ここで、$\boldsymbol{\pi}:=\left(\boldsymbol{\pi}_s\right)_{s \in \mathcal{S}}$、$\Pi=\left(\Delta^A\right)^{\dot{S}}$、$v_s^{\boldsymbol{\pi}, \boldsymbol{p}}=\mathbb{E}_{\boldsymbol{\pi}, \boldsymbol{p}}\left[\sum_{t=0}^{\infty} \gamma^t \cdot c_{s_t a_t s_{t+1}} \mid s_0=s\right]$、$J_{\boldsymbol{\rho}}(\boldsymbol{\pi}, \boldsymbol{p}):=\boldsymbol{\rho}^{\top} \boldsymbol{v}^{\boldsymbol{\pi}, \boldsymbol{p}}=\sum_{s \in \mathcal{S}} \rho_s v_s^{\boldsymbol{\pi}, \boldsymbol{p}}$です。Wangらは$(4)$に対して、二重ループのロバスト勾配降下アルゴリズム(DRPG)を開発しました。DRPGは2つのループを持つアルゴリズムであり、内側のループでは、最悪ケースの遷移確率カーネルを更新し、外側ループでは方策を更新します。
![[Pasted image 20250212005752.png]]
**$J_{\boldsymbol{\rho}}(\boldsymbol{\pi}, \boldsymbol{p})$の性質**
*   **凸性、凹性:** × 
*   **リプシッツ連続性:** ✓(Lemma3.1) 
*   **微分可能性:** ×
*   **スムーズ性:** ✓(Lemma3.1)
**$\Phi(\boldsymbol{\pi})$の性質**
* **凸性: weakly convex**
*   **スムーズ性:** ×
#### 収束解析
Lemma3.1は$J_{\boldsymbol{\rho}}(\boldsymbol{\pi}, \boldsymbol{p})$がいくつかの性質を保持していることを示しています。これらの性質は、アルゴリズムの収束性を議論するために用いられることがあります。しかし、weakly convexityだけでは大域的最適解を示すには不十分です。そこで、勾配支配条件を用います。この条件を用いることで、勾配の消失を避けることができ、一次停留点が大域的最適解となります。Theorem3.2では、勾配支配条件を用いて、$\Phi(\boldsymbol{\pi})$を、凸かつスムーズな凸包：モロー包絡線でカバーをしてもその一時停留点は大域的最適解になるということを示しています。Theorem3.3では、アルゴリズムが$\epsilon$-大域的最適になるためには$\mathcal{O}\left(\epsilon^{-4}\right)$ステップで十分であること示しています。
##### Theorem3.2の証明
##### Theorem3.3の証明


##### 既存研究の課題













* 拡張Mirror discent法Li et al., 2022
	* (s,a)-rectangularを仮定したうえで解かれている。









#### ロバスト動的計画法(Iyengar,2005)
動的計画法(DP)は、遷移が不確実なモデルにおいて、環境との相互作用から最適な方策を獲得する手法の一つです。DPは価値関数に対して、「将来の報酬」関数の形式で作用素を適用し即時報酬と将来の報酬を最大化するような行動を選ぶ方法をとります。既存のDPにおいては、経験から推定された遷移確率が真の遷移確率であるという仮定を置き、その誤差を考慮しない研究があります。ロバストDPでは、子の誤差とその影響を明示的に考慮したDPモデルを提案しました。











状態価値関数を以下のように定義します。$$
v_{s}^{\boldsymbol{\pi}, \boldsymbol{p}}=\mathbb{E}_{\boldsymbol{\pi}, \boldsymbol{p}}\left[\sum_{t=0}^{\infty} \gamma^{t} \cdot c_{s_{t} a_{t} s_{t+1}} \mid s_{0}=s\right]
$$
RMDPでは、総コストが最も高くなるような遷移関数において、コストを最小化するような方策を求めるという以下のようなミニマックス問題を考えます。
$$
\min _{\boldsymbol{\pi} \in \Pi} \max _{\boldsymbol{p} \in \mathcal{P}} J_{\boldsymbol{\rho}}(\boldsymbol{\pi}, \boldsymbol{p}):=\boldsymbol{\rho}^{\top} \boldsymbol{v}^{\boldsymbol{\pi}, \boldsymbol{p}}=\sum_{s \in \mathcal{S}} \rho_{s} v_{s}^{\boldsymbol{\pi}, \boldsymbol{p}} 
$$
この最適化問題は、総コストが高くなるような遷移関数を求める内側ループと、最適方策を求める外側ループから構成されています。
$$
\min _{\boldsymbol{\pi} \in \Pi}\left\{\Phi(\boldsymbol{\pi}):=\max _{\boldsymbol{p} \in \mathcal{P}} J_{\boldsymbol{\rho}}(\boldsymbol{\pi}, \boldsymbol{p})\right\} 
$$
内側を関数として定義し、勾配法を使って最適方策を求めることが自然ですが、関数は微分可能でない且つ凸ではないため、劣勾配が存在せず、困難です（Nouiehed et al., 2019; Lin et al., 2020）。対して、関数にモロー包絡線という凸性を持つ関数で近似します。この関数近似は、その一次停留点が存在し、勾配支配を満たすことでグローバル収束を示すことができます。

## 情報の断片
**遷移、報酬の推定誤差が及ぼす影響**
Mannorらは、価値関数が、遷移関数や報酬関数の推定誤差に敏感であるという可能性を示しました。


**RMDPは何に対してロバストか**
RMDPの解(最適価値関数)は遷移確率や報酬関数の推定誤差に鈍感です。つまり推定誤差に対してロバストです。

**rectangularあれこれ**
* 低ランクMDP（線形MDP）の場合、$r$-rectangularという仮定を使う。
* $s$-rectangularは(s,a)-rectangularより保守的[75]であり、この仮定を用いた研究は、(Le Tallec, 2007; Wiesemann et al., 2013; Derman et al., 2021; Wang et al., 2022)です。

**RMDPの課題**
* RMDPの総報酬(=価値関数)は方策に関して微分可能ではないし、凸でもないです。つまり劣勾配は存在しない。(劣勾配は、必ずしも微分可能でない凸関数の上で定義されるため)
* 価値関数をモロー包絡線という凸性をもった関数で近似することを考えますが、RMDPにおいては最適方策を獲得するために十分であることが示されています。
* 近似することで凸になるので劣勾配を求めることができ、射影勾配法を使えます。
* モロー包絡線は凸性や平滑さを示しているだけで任意の点での微分可能性は仮定していいません。よって勾配は必ずしも求まらないと思います。


**RMDPについて**
$$
\min _{\boldsymbol{\pi} \in \Pi} \max _{\boldsymbol{p} \in \mathcal{P}} J_{\boldsymbol{\rho}}(\boldsymbol{\pi}, \boldsymbol{p}):=\boldsymbol{\rho}^{\top} \boldsymbol{v}^{\boldsymbol{\pi}, \boldsymbol{p}}=\sum_{s \in \mathcal{S}} \rho_{s} v_{s}^{\boldsymbol{\pi}, \boldsymbol{p}} \tag{2}
$$
未知の真の遷移カーネルが含まれるようにすることで、(2)の最適方策は実際には信頼性の高いパフォーマンスを実現できます（Russell & Petrik, 2019; Behzadian et al., 2021b; Panaganti et al., 2022）

**既存研究の課題**
ε最適に勾配計算が行えると仮定している。

Agarwal, A., Kakade, S. M., Lee, J. D., and Mahajan, G. On the theory of policy gradient methods: Optimality, approximation, and distribution shift. Journal of Machine Learning Research, 22(98):1-76, 2021.

Archibald, T., McKinnon, K., and Thomas, L. On the generation of Markov decision processes. Journal of the Operational Research Society, 46(3):354-361, 1995.

Badrinath, K. P. and Kalathil, D. Robust reinforcement learning using least squares policy iteration with provable performance guarantees. In International Conference on Machine Learning, pp. 511-520. PMLR, 2021.

Beck, A. First-order methods in optimization. SIAM, 2017.

Behzadian, B., Petrik, M., and Ho, C. P. Fast algorithms for $l_{\infty}$-constrained s-rectangular robust MDPs. Advances in Neural Information Processing Systems, 34, 2021a.

Behzadian, B., Russel, R., Ho, C. P., and Petrik, M. Optimizing percentile criterion using robust MDPs. In International Conference on Artificial Intelligence and Statistics (AIStats), 2021b.

Bertsekas, D. P. Nonlinear Programming. Athena scientific, 3rd edition, 2016.

Bhandari, J. and Russo, D. On the linear convergence of policy gradient methods for finite MDPs. In International Conference on Artificial Intelligence and Statistics, pp. 2386-2394. PMLR, 2021.

Bhatnagar, S., Sutton, R. S., Ghavamzadeh, M., and Lee, M. Natural actor-critic algorithms. Automatica, 45(11): 2471-2482, 2009.

Chen, J. and Jiang, N. Information-theoretic considerations in batch reinforcement learning. In International Conference on Machine Learning, pp. 1042-1051. PMLR, 2019.

Chen, Z., Yu, P., and Haskell, W. B. Distributionally robust optimization for sequential decision-making. Optimization, 68(12):2397-2426, 2019.

Condon, A. On algorithms for simple stochastic games. Advances in computational complexity theory, 13:51-72, 1990.

Daskalakis, C., Foster, D. J., and Golowich, N. Independent policy gradient methods for competitive reinforcement learning. Advances in neural information processing systems, 33:5527-5540, 2020.

Davis, D. and Drusvyatskiy, D. Stochastic model-based minimization of weakly convex functions. SIAM Journal on Optimization, 29(1):207-239, 2019.

Delgado, K. V., De Barros, L. N., Dias, D. B., and Sanner, S. Real-time dynamic programming for Markov decision processes with imprecise probabilities. Artificial Intelligence, 230:192-223, 2016.

Derman, E., Geist, M., and Mannor, S. Twice regularized MDPs and the equivalence between robustness and regularization. Advances in Neural Information Processing Systems, 34, 2021.

Ghadimi, S. and Lan, G. Accelerated gradient methods for nonconvex nonlinear and stochastic programming. Mathematical Programming, 156(1):59-99, 2016.

Goyal, V. and Grand-Clément, J. Robust Markov decision processes: Beyond rectangularity. Mathematics of Operations Research, 2022.

Grand-Clément, J. and Kroer, C. First-order methods for Wasserstein distributionally robust MDPs. In International Conference on Machine Learning, pp. 2010-2019. PMLR, 2021a.

Grand-Clément, J. and Kroer, C. Scalable first-order methods for robust MDPs. In AAAI Conference on Artificial Intelligence, volume 35, pp. 12086-12094, 2021b.

Heusel, M., Ramsauer, H., Unterthiner, T., Nessler, B., and Hochreiter, S. Gans trained by a two time-scale update rule converge to a local nash equilibrium. Advances in neural information processing systems, 30, 2017.

Ho, C. P., Petrik, M., and Wiesemann, W. Fast bellman updates for robust MDPs. In International Conference on Machine Learning, pp. 1979-1988. PMLR, 2018.

Ho, C. P., Petrik, M., and Wiesemann, W. Partial policy iteration for 11-robust Markov decision processes. Journal of Machine Learning Research, 22(275):1-46, 2021.

Iyengar, G. N. Robust dynamic programming. Mathematics of Operations Research, 30(2):257-280, 2005.

Jin, C., Netrapalli, P., and Jordan, M. What is local optimality in nonconvex-nonconcave minimax optimization? In International Conference on Machine Learning, pp. 4880-4889. PMLR, 2020.

Kakade, S. and Langford, J. Approximately optimal approximate reinforcement learning. In International Conference on Machine Learning. Citeseer, 2002.

Kaufman, D. L. and Schaefer, A. J. Robust modified policy iteration. INFORMS Journal on Computing, 25(3):396410, 2013.

Konda, V. and Tsitsiklis, J. Actor-critic algorithms. Advances in neural information processing systems, 12, 1999.

Kruger, A. Y. On fréchet subdifferentials. Journal of Mathematical Sciences, 116(3):3325-3358, 2003.

Le Tallec, Y. Robust, risk-sensitive, and data-driven control of Markov decision processes. PhD thesis, Massachusetts Institute of Technology, 2007.

Leonardos, S., Overman, W., Panageas, I., and Piliouras, G. Global convergence of multi-agent policy gradient in Markov potential games. arXiv preprint arXiv:2106.01969, 2021.

Li, Y., Zhao, T., and Lan, G. First-order policy optimization for robust Markov decision process. arXiv preprint arXiv:2209.10579, 2022.

Lim, S. H., Xu, H., and Mannor, S. Reinforcement learning in robust markov decision processes. Advances in Neural Information Processing Systems, 26, 2013.

Lin, T., Jin, C., and Jordan, M. On gradient descent ascent for nonconvex-concave minimax problems. In International Conference on Machine Learning, pp. 6083-6093. PMLR, 2020.

Liu, Z., Bai, Q., Blanchet, J., Dong, P., Xu, W., Zhou, Z., and Zhou, Z. Distributionally robust $q$-learning. In International Conference on Machine Learning, pp. 1362313643. PMLR, 2022.

Luo, L., Ye, H., Huang, Z., and Zhang, T. Stochastic recursive gradient descent ascent for stochastic nonconvex-strongly-concave minimax problems. Advances in Neural Information Processing Systems, 33:20566-20577, 2020.

Mai, V. and Johansson, M. Convergence of a stochastic gradient method with momentum for non-smooth nonconvex optimization. In International conference on machine learning, pp. 6630-6639. PMLR, 2020.

Mannor, S., Mebel, O., and Xu, H. Robust MDPs with k-rectangular uncertainty. Mathematics of Operations Research, 41(4):1484-1509, 2016.

Mei, J., Xiao, C., Szepesvari, C., and Schuurmans, D. On the global convergence rates of softmax policy gradient methods. In International Conference on Machine Learning, pp. 6820-6829. PMLR, 2020.

Mokhtari, A., Ozdaglar, A., and Pattathil, S. A unified analysis of extra-gradient and optimistic gradient methods for saddle point problems: Proximal point approach. In International Conference on Artificial Intelligence and Statistics, pp. 1497-1507. PMLR, 2020.

Nilim, A. and El Ghaoui, L. Robust control of Markov decision processes with uncertain transition matrices. $O p$ erations Research, 53(5):780-798, 2005.

Nouiehed, M., Sanjabi, M., Huang, T., Lee, J. D., and Razaviyayn, M. Solving a class of non-convex min-max games using iterative first order methods. Advances in Neural Information Processing Systems, 32, 2019.

Panaganti, K. and Kalathil, D. Sample complexity of robust reinforcement learning with a generative model. arXiv:2112.01506 [cs, stat], 2021.

Panaganti, K. and Kalathil, D. Sample complexity of robust reinforcement learning with a generative model. In International Conference on Artificial Intelligence and Statistics, pp. 9582-9602. PMLR, 2022.

Panaganti, K., Xu, Z., Kalathil, D., and Ghavamzadeh, M. Robust reinforcement learning using offline data. In Advances in Neural Information Processing Systems (NeurIPS), 2022.

Petrik, M. Approximate dynamic programming by minimizing distributionally robust bounds. In International Conference on Machine Learning, pp. 497-504, 2012.

Petrik, M. and Subramanian, D. Raam: The benefits of robustness in approximating aggregated MDPs in reinforcement learning. Advances in Neural Information Processing Systems, 27, 2014.

Petrik, M., Ghavamzadeh, M., and Chow, Y. Safe policy improvement by minimizing robust baseline regret. Advances in Neural Information Processing Systems, 29, 2016.

Pirotta, M., Restelli, M., and Bascetta, L. Policy gradient in Lipschitz Markov decision processes. Machine Learning, 100(2):255-283, 2015.

Porteus, E. L. Foundations of stochastic inventory theory. Stanford University Press, 2002.

Puterman, M. L. Markov decision processes: discrete stochastic dynamic programming. John Wiley \& Sons, 2014.

Razaviyayn, M., Huang, T., Lu, S., Nouiehed, M., Sanjabi, M., and Hong, M. Nonconvex min-max optimization: Applications, challenges, and recent theoretical advances. IEEE Signal Processing Magazine, 37(5):55-66, 2020.

Rockafellar, R. T. Monotone operators and the proximal point algorithm. SIAM journal on control and optimization, 14(5):877-898, 1976.

Rockafellar, R. T. and Wets, R. J.-B. Variational analysis, volume 317. Springer Science \& Business Media, 2009.

Roy, A., Xu, H., and Pokutta, S. Reinforcement learning under model mismatch. Advances in neural information processing systems, 30, 2017.

Russel, R. H., Benosman, M., and Van Baar, J. Robust constrained-MDPs: Soft-constrained robust policy optimization under model uncertainty. arXiv preprint arXiv:2010.04870, 2020.

Russell, R. H. and Petrik, M. Beyond confidence regions: Tight Bayesian ambiguity sets for robust MDPs. In Advances in Neural Information Processing Systems (NeurIPS), 2019.

Ruszczyński, A. Risk-averse dynamic programming for Markov decision processes. Mathematical programming, 125(2):235-261, 2010.

Scherrer, B. Approximate policy iteration schemes: a comparison. In International Conference on Machine Learning, pp. 1314-1322. PMLR, 2014.

Schulman, J., Levine, S., Abbeel, P., Jordan, M., and Moritz, P. Trust region policy optimization. In International conference on machine learning, pp. 1889-1897. PMLR, 2015.

Schulman, J., Levine, S., Abbeel, P., Jordan, M., and Moritz, P. Proximal policy optimization algorithms. arXiv preprint arXiv:1707.06347, 2017.

Shapiro, A. Rectangular sets of probability measures. $O p$ erations Research, 64(2):528-541, 2016.

Shapiro, A. Distributionally robust optimal control and MDP modeling. Operations Research Letters, 49(5):809814, 2021.

Silver, D., Lever, G., Heess, N., Degris, T., Wierstra, D., and Riedmiller, M. Deterministic policy gradient algorithms. In International conference on machine learning, pp. 387395. PMLR, 2014.

Sutton, R. S. and Barto, A. G. Reinforcement learning: An introduction. MIT press, 2018.

Sutton, R. S., McAllester, D., Singh, S., and Mansour, Y. Policy gradient methods for reinforcement learning with function approximation. Advances in neural information processing systems, 12, 1999.

Thekumparampil, K. K., Jain, P., Netrapalli, P., and Oh, S. Efficient algorithms for smooth minimax optimization. Advances in Neural Information Processing Systems, 32, 2019.

Vial, J.-P. Strong and weak convexity of sets and functions. Mathematics of Operations Research, 8(2):231259, 1983.

Wang, K., Kumar, N., Zhou, K., Hooi, B., Feng, J., and Mannor, S. The geometry of robust value functions. arXiv preprint arXiv:2201.12929, 2022.

Wang, Y. and Zou, S. Online robust reinforcement learning with model uncertainty. Advances in Neural Information Processing Systems, 34:7193-7206, 2021.

Wang, Y. and Zou, S. Policy gradient method for robust reinforcement learning. In International Conference on Machine Learning, pp. 23484-23526, 17-23 Jul 2022.

Wiesemann, W., Kuhn, D., and Rustem, B. Robust Markov decision processes. Mathematics of Operations Research, 38(1):153-183, 2013.
$\mathrm{Xu}, \mathrm{H}$. and Mannor, S. Parametric regret in uncertain Markov decision processes. In IEEE Conference on Decision and Control (CDC), pp. 3606-3613. IEEE, 2009.
$\mathrm{Xu}, \mathrm{H}$. and Mannor, S. Distributionally robust Markov decision processes. Advances in Neural Information Processing Systems, 23, 2010.

Xu, X., Zuo, L., and Huang, Z. Reinforcement learning algorithms with function approximation: Recent advances and applications. Information Sciences, 261:1-31, 2014.

Policy Gradient in Robust MDPs
Zhang, J., Xiao, P., Sun, R., and Luo, Z. A singleloop smoothed gradient descent-ascent algorithm for nonconvex-concave min-max problems. Advances in Neural Information Processing Systems, 33:7377-7389, 2020.
