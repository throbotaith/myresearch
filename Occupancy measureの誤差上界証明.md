* コスト最小化→報酬最大化
* ベクトル同士の比較、スカラー同士の比較になっているか
* 最大値ノルムとか
$P_{r}$ は、以下の条件を満たす $P \in \mathcal{U}$ として定義される。

$$
P_{r} \in \underset{P \in \mathcal{U}}{\operatorname{argmin}}  J_{C,P}(\pi)
$$
劣勾配
$$
\nabla J_{C,P_{r}} (\pi) = H d_{P_{r}}^\pi(s) Q_{C, P_{r}}^\pi(s, a)
$$

劣微分
$$
\partial J_{c, \mathcal{U}}(\pi) = \operatorname{conv} \left\{ \nabla_\pi J_{C,P}(\pi) \mid P \in \mathcal{U}(\pi) \right\}
$$


本研究では、RCMDPにおける劣勾配推定機を生成モデルを用いてシミュレーションから獲得する場合における、シミュレーション誤差を示す。また生成モデルを用いた場合に$\epsilon$最適な劣勾配推定を行うためのサンプル複雑度を導出する。
以下のような劣微分のシミュレーション誤差を考える。
$$
    \left\|\partial J_{C,\mathcal{U}}, -\partial \hat{J}_{C,\hat{\mathcal{U}}}, \right\|_{\infty}
    $$
本研究では、劣微分の要素である劣勾配のシミュレーション誤差をバウンドすることに焦点を当てる。
$$\left\|\nabla J_{C,P_{r}} (\pi)-\nabla \hat{J}_{C,\hat{P}_{r}} (\pi)\right\|_{\infty}$$

定義から、劣勾配は以下のように表される。
$$ \max_{s,a} \left| H d_{P_{r}}^\pi(s) Q_{C, P_{r}}^\pi(s, a)-H d_{\hat{P}_{r}}^\pi(s) \hat{Q}_{C, \hat{P}_{r}}^\pi(s, a)\right|$$


三角不等式を用いると、

$$
\begin{aligned} 
& \max_{s,a} H\left|d_{P_r}^\pi(s) Q_{C, P_{r}}^\pi(s, a)-d_{\hat{P}_{r}}^\pi(s) \hat{Q}_{C, \hat{P}_{r}}^\pi(s, a)\right| \\
& \leqq \max_{s,a} H\left|d_{P_{r}}^\pi(s) Q_{C, P_{r}}^\pi(s, a)-d_{\hat{P}_{r}}^\pi(s) Q_{C, P_{r}}^\pi(s, a)\right|\quad(1)\\
& + \max_{s,a} H\left|d_{\hat{P}_{r}}^\pi(s) Q_{C, P_{r}}^\pi(s, a)-d_{\hat{P}_{r}}^\pi(s) \hat{Q}_{C, \hat{P}_{r}}^\pi(s, a)\right| \quad(2) \end{aligned} $$

$(1)$について、$c(s,a) \in [0,1]$より、$|Q^\pi(s,a)|\leq \frac{1}{1-\gamma}$となるので、
$$ H \max_{s, a} \left| Q_{C, P_{r}}^\pi(s, a)-Q_{\hat{C}, \hat{P}_{r}}^\pi(s, a) \right| \leq \frac{H}{1-\gamma} \max_{s} \left| d_{P_r}^\pi(s)-d_{\hat{P}_{r}}^\pi(s) \right| $$

$H=\frac{1}{1-\gamma}$から、
$$ H \max_{s, a} \left| Q_{C, P_{r}}^\pi(s, a)-Q_{\hat{C}, \hat{P}_{r}}^\pi(s, a) \right| \leq \frac{1}{(1-\gamma)^2} \max_{s} \left| d_{P_r}^\pi(s)-d_{\hat{P}_{r}}^\pi(s) \right| $$
次に、Occupancy measureのシミュレーション誤差上界を導出する。ノイマン級数展開を用いることで、以下のように変形できる。
$$
\begin{aligned}
d_\mu^\pi &= (1-\gamma) \sum_{t=0}^\infty \gamma^t (P^\pi)^t \mu \\
&= (1-\gamma) \left\{ I + \gamma P^\pi + \gamma^2 (P^\pi)^2 + \dots \right\} \mu \\
&= (1-\gamma) (I - \gamma P^\pi)^{-1} \mu
\end{aligned}
$$
ここで、
$$
P^\pi(s'|s)=\sum_{a}P(s'|s,a)\pi(a|s)
$$
であり、
$$
\begin{aligned}
& P_{r}^\pi(s'|s)=\sum_{a}P_{r}(s'|s,a)\pi(a|s) \\

& \hat{P}_{r}^\pi(s'|s)=\sum_{a}\hat{P}_{r}(s'|s,a)\pi(a|s)
\end{aligned}
$$
と定義すると、
$$
\begin{aligned}
& d_{P_{r}}^\pi=(1-\gamma)\left(I-\gamma P_{r}^\pi\right)^{-1}\mu \\
& d_{\hat{P}_{r}}^\pi=(1-\gamma)\left(I-\gamma  \hat{P}_{r}^\pi\right)^{-1}\mu
\end{aligned}
$$
より、
$$
\begin{aligned}
  \left\| d_{P_{r}^\pi}^\pi-d_{\hat{P}_{r}^\pi}^\pi \right\|_{\infty} & =\left\|\mu(1-\gamma)\left(\left(I-\gamma P_{r}^\pi\right)^{-1}-\left(I-\gamma \hat{P}_{r}^\pi\right)^{-1}\right)\right\|_{\infty}  \\
& =\left\|\mu(1-\gamma)\left(I-\gamma P_{r}^\pi\right)^{-1}\left(I-\left(I-\gamma P_{r}^\pi\right)\left(I-\gamma \hat{P}_{r}^\pi\right)^{-1}\right)\right\|_{\infty} \\
& =\left\|\mu(1-\gamma)\left(I-\gamma P_{r}^\pi\right)^{-1}\left(I-\gamma \hat{P}_{r}^\pi\right)^{-1}\left(\left(I-\gamma \hat{P}_{r}^\pi\right)-\left(I- \gamma P_{r}^\pi\right)\right)\right\|_{\infty}\\
& =\left\|\mu \gamma(1-\gamma)\left(I-\gamma P_{r}^\pi\right)^{-1}\left(I-\gamma \hat{P}_{r}^\pi\right)^{-1}\left(P_{r}^\pi-\hat{P}_{r}^\pi\right)\right\|_{\infty} \\
& \leqq\left\| \mu \gamma\left(I-\gamma \hat{P}_{r}^\pi\right)^{-1}\left(P_{r}^\pi-\hat{P}_{r}^\pi\right)\right\|_{\infty} \\
& \leqq \left\| \frac{\mu \gamma}{1-\gamma}\left(P_{r}^\pi-\hat{P}_{r}^\pi\right)\right\|_{\infty}\\
& \leqq \left\| \frac{\mu \gamma}{1-\gamma}\left(P_{r}-\hat{P}_{r}\right)\right\|_{\infty}
\end{aligned}
$$
なお、最後の不等式は
$$
\begin{aligned}
&\max_{s} \left| P_{r}^\pi(s'|s)-\hat{P}_{r}^\pi(s'|s) \right|\\
& =\max_{s} \left| \sum_{a}P_{r}(s'|s,a)\pi(a|s)-\sum_{a}\hat{P}_{r}^\pi(s'|s,a)\pi(a|s)\right|\\
&= \sum_{a}\pi(a|s)\max_{s,a} \left|\left(P_{r}(s'|s,a)-\hat{P}_{r}(s'|s,a)    \right)\right| \\
& \leqq \max_{s,a}\left|\left(P_{r}(s'|s,a)-\hat{P}_{r}(s'|s,a)    \right)\right|  \\
& = \left\|(P_{r}-\hat{P}_{r}) \right\|_{\infty}
\end{aligned}
$$
を用いた。

最終的に、以下の不等式が成り立つ。

よって、
$$ H \max_{s, a} \left| Q_{C, P_{r}}^\pi(s, a)-Q_{\hat{C}, \hat{P}_{r}}^\pi(s, a) \right| \leq \frac{\gamma}{(1-\gamma)^2} $$

$(2)$について、

$$
H \max_{s,a}|d_{\hat{P}_{r}}^\pi(s)(Q_{C, P_{r}}^\pi(s, a)-\hat{Q}_{C, \hat{P}_{r}}^\pi(s, a))| \leqq H\max_{s,a}|Q_{C, P_{r}}^\pi(s, a)-\hat{Q}_{C, \hat{P}_{r}}^\pi(s, a)|
$$ここで、$$
Q_{C, P_{r}}^\pi(s, a)=c(s, a)+\gamma \mathbb{E}_{s^{\prime} \sim P_{r}(\cdot | s, a)}\left[ V_{C, P_{r}}^\pi(s^{\prime})\right]
$$
より、
$$
\begin{aligned}
& Q_{C, P_{r}}^\pi(s, a)-\hat{Q}_{C, \hat{P}_{r}}^\pi(s, a) \\
& = \gamma(\mathbb{E}_{s^{\prime} \sim P_{r}(\cdot | s, a)}\left[ V_{C, P_{r}}^\pi(s^{\prime})\right]-\mathbb{E}_{s^{\prime} \sim \hat{P}_{r}(\cdot | s, a)}\left[ V_{C, \hat{P}_{r}}^\pi(s^{\prime})\right])
\end{aligned}
$$
であるので、
$$
\begin{aligned}
& H\max_{s,a}\left|Q_{C, P_{r}}^\pi(s, a)-\hat{Q}_{C, \hat{P}_{r}}^\pi(s, a)\right|\\

&= \gamma H \max_{s,a}\left|\mathbb{E}_{s^{\prime} \sim P_{r}(\cdot | s, a)}\left[ V_{C, P_{r}}^\pi(s^{\prime})\right]-\mathbb{E}_{s^{\prime} \sim \hat{P}_{r}(\cdot | s, a)}\left[ V_{C, \hat{P}_{r}}^\pi(s^{\prime})\right] \right|\\

& =\gamma H\max_{s'}\left|\sum_{s^{\prime}} P_{r}(s^{\prime} | s, a) V_{C, P_{r}}^\pi(s^{\prime})-\sum_{s^{\prime}}\hat{P}_{r}(s^{\prime} | s, a) \hat{V}_{C, \hat{P}_{r}}^\pi(s^{\prime})\right|\\

& =\gamma H\max_{s'}| \sum_{s^{\prime}}\left(P_{r}\left(s^{\prime} \mid s, a\right)-\hat{P}_{r}\left(s^{\prime} \mid s, a\right)\right) V_{C, P_{r}}^\pi(s^{\prime})+\sum_s \hat{P}_{r}(s^{\prime} | s, a)\left(V_{C, P_{r}}^\pi(s^{\prime})-\hat{V}_{C, \hat{P}_{r}}^\pi(s^{\prime}) \right| \\

& \leqq \frac{\gamma}{1-\gamma} \left( \frac{|S|}{(1-\gamma)}+\max_{s}\left|V_{C, P_{r}}^\pi(s^{\prime})-\hat{V}_{C, \hat{P}_{r}}^\pi(s^{\prime})\right| \right)\\

\end{aligned}
$$
ここで、不等式は、$\sum_{s^{\prime}}\left(P_{r}\left(s^{\prime} \mid s, a\right)-\hat{P}_{r}\left(s^{\prime} \mid s, a\right)\right)$が$\left|S \right|$で抑えることができるという事実と、$V_{C, P_{r}}^\pi(s^{\prime})$の上界が$\frac{1}{1-\gamma}$であることから与えられる。
また、右辺第2項について、$V$をベクトル形式で表記する。
ここで、wangらの研究より、ベルマン作用素で上界を与えることができる。
$$
\left\|V_{C, P_{r}}^\pi-\hat{V}_{C, \hat{P}_{r}}\right\|_{\infty}\leq \frac{1}{1-\gamma}\underset{V \in \mathcal{V}}{\sup }\left\|\tau_{C, P_{r}}^\pi V-\hat{\tau}_{C, \hat{P}_{r}}^\pi V\right\|_{\infty}
$$
なお、$\tau_{C, P_{r}}^\pi V=R^\pi+\gamma P_{r} V$である。
Wangらは、KL球を$\mathcal{U}$とし、$(s,a)-$rectangularを$\mathcal{U}$に仮定している。本研究でもそれを用いる。$f(t)=t\log t$ とした場合、以下のように書くことができる。
$$
\frac{1}{1-\gamma}\underset{V \in \mathcal{V}}{\sup }\left\|\tau_{C, P_{r}}^\pi V-\hat{\tau}_{C, \hat{P}_{r}}^\pi V\right\|_{\infty}　\leq \frac{4 \gamma \sqrt{|\mathcal{S}|}}{\rho(1-\gamma)^{2} \underline{p} \sqrt{n}}\left(1+\sqrt{\log \frac{2|\mathcal{S}|^{2}|\mathcal{A}|^{2}[1+\rho \underline{p} \sqrt{n}]}{\delta}}\right)
$$
よって、$(1)$と$(2)$を合わせて、

$$
\begin{aligned}
& H\max_{s,a}\left|  d_{P_{r}}^\pi(s) Q_{C, P_{r}}^\pi(s, a)- d_{\hat{P}_{r}}^\pi(s) \hat{Q}_{C, \hat{P}_{r}}^\pi(s, a)\right| \\


& \leq \frac{\gamma}{1-\gamma}\left( \frac{|S|+1}{1-\gamma}+\frac{4  \sqrt{|\mathcal{S}|}}{\rho(1-\gamma)^{2} \underline{p} \sqrt{n}}\left(1+\sqrt{\log \frac{2|\mathcal{S}|^{2}|\mathcal{A}|^{2}[1+\rho \underline{p} \sqrt{n}]}{\delta}}\right)\right)
\end{aligned}
$$
右辺を$\epsilon$とおいて、サンプル複雑度を導出する。
$$
\sqrt{n} = \frac{4\sqrt{S}\left(1 + \sqrt{\log \frac{2S^2 A^2(1+\rho\underline{p}\sqrt{n})}{\delta}}\right)}{\rho (1-\gamma)^2 \underline{p} (\frac{(1-\gamma)\epsilon}{\gamma} - \frac{S+1}{1-\gamma})}
$$

$\log$ の項の中の 1 は無視できるほど小さいと仮定し、さらにルートの項を定数 $C$ で近似する。つまり、

$$
\sqrt{\log \frac{2S^2 A^2 \rho\underline{p}\sqrt{n}}{\delta}} \approx \sqrt{\frac{1}{2} \log n + \log \frac{2S^2A^2\rho\underline{p}}{\delta}} \le C
$$

と近似する。すると、式は以下のように簡略化される。

$$
\sqrt{n} \approx \frac{4\sqrt{S}(1+C)}{\rho (1-\gamma)^2 \underline{p} (\frac{(1-\gamma)\epsilon}{\gamma} - \frac{S+1}{1-\gamma})}
$$

両辺を2乗すると、

$$
n \approx \frac{16S(1+C)^2}{\rho^2 (1-\gamma)^4 \underline{p}^2 (\frac{(1-\gamma)\epsilon}{\gamma} - \frac{S+1}{1-\gamma})^2}
$$
となる。


$d_{P_{r}}^\pi(s)$
$Q_{C, P_{r}}^\pi(s, a)$
$V_{C, P_{r}}^\pi(s)$
$V_{C, P_{r}}^\pi(s^{\prime})$
$P_{r}(\cdot | s, a)$
$P_{r}(s^{\prime} | s, a)$
$\tau_{C, P_{r}}^\pi$

$d_{\hat{P}_{r}}^\pi(s)$
$\hat{Q}_{C, \hat{P}_{r}}^\pi(s, a)$
$\hat{V}_{C, \hat{P}_{r}}^\pi(s)$
$\hat{V}_{C, \hat{P}_{r}}^\pi(s^{\prime})$
$\hat{P}_{r}(\cdot | s, a)$
$\hat{P}_{r}(s^{\prime} | s, a)$
$\hat{\tau}_{C, \hat{P}_{r}}$

ここで、
*   $m$ は不確実性集合 $\mathcal{U}$ の要素番号。
*   $M$ は不確実性集合 $\mathcal{U}$ の要素数。
*   $\hat{m}$ は推定された不確実性集合 $\hat{\mathcal{U}}$ の要素番号。
*   $\hat{M}$ は推定された不確実性集合 $\hat{\mathcal{U}}$ の要素数。
*   $mr$ は、与えられた方策$\pi$に対して、真の不確実性集合$\mathcal{U}$の中で、目的関数を最大にする要素の番号。
*  $\hat{mr}$は、与えられた方策$\pi$に対して、推定された不確実性集合$\hat{\mathcal{U}}$の中で、推定された目的関数を最大にする要素の番号。
*   $\nabla J_{c} P_{r}(\pi)$ は、遷移確率 $P_{r}$ の下での方策 $\pi$ の目的関数の勾配。
*   $\nabla \hat{J}_{c} \hat{P_{\hat{m}_{r}}}(\pi)$ は、遷移確率 $\hat{P}_{\hat{m}_{r}}$ の下での方策 $\pi$ の推定された目的関数の勾配。
*   $\operatorname{conv}\{\cdot\}$ は、指定された勾配の集合に対する凸包を表します。
*   $\left\| \cdot \right\|_{\infty}$: 無限大ノルム（ベクトルの要素の中で絶対値が最大のものを表す）
*  $d_{P_{r}}^\pi(s)$: 遷移確率 $P_{r}$ の下での、方策$\pi$における状態 $s$ の占有測度
*  $d_{\hat{P}_{r}}^\pi(s)$: 遷移確率 $\hat{P}_{r}$ の下での、方策$\pi$における状態 $s$ の占有測度
*  $\gamma \in (0, 1)$: 割引率
*  $\mathbb{E}[\cdot \mid s_{0} \sim \mu, \pi, P]$: 初期状態が $\mu$ に従い、方策 $\pi$と遷移確率 $P$ に従って行動する場合の期待値
*  $\mathbb{1}\{s_{h}=s\}$: 時刻 $h$ の状態が $s$ の場合に1、そうでない場合に0をとる指示関数
*   $\mathcal{S}$: 状態空間

