---
title: 最优潮流（AC-OPF与DC-OPF）
category: Math
date: 2026-11-16
tag:
    - 电力
    - 优化
---

在等值电路模型的基础上，我们可以建立完整的最优潮流模型。

### AC-OPF

记号：
- $N$：母线集合，母线索引为 $i,j$；
- $G$：发电机集合，发电机 $k$ 位于母线 $i(k)$；
- $E$：支路集合，每条支路规定一个 from 端 $i$ 和 to 端 $j$；
- $E^R$：反向弧集合。对一条物理支路 $(i,j)\in E$，功率流变量同时包含 $S_{ij}$ 和 $S_{ji}$；
- $P_i^d,Q_i^d$：母线 $i$ 的有功和无功负荷；
- $g_i^s,b_i^s$：母线并联电导和电纳；
- $y_{ij}=g_{ij}+\mathrm{i}b_{ij}$：支路串联导纳；
- $y_{ij}^{sh}=g_{ij}^{sh}+\mathrm{i}b_{ij}^{sh}$：支路端部并联导纳；
- $\tau_{ij}=t_{ij}e^{\mathrm{i}\phi_{ij}}$：变压器复变比；无变压器时 $t_{ij}=1,\phi_{ij}=0$；
- $\overline s_{ij}$：支路功率容量；$\underline\delta_{ij},\overline\delta_{ij}$：支路相角差界；
- $\underline p_k^g,\overline p_k^g,\underline q_k^g,\overline q_k^g$：发电机出力界；
- $\underline v_i,\overline v_i$：电压幅值界；
- $c_{2k},c_{1k},c_{0k}$：发电机成本系数。

优化目标为最小化发电成本：
$$
\min f(p^g)=\sum_{k\in G}
\left(c_{2k}(p_k^g)^2+c_{1k}p_k^g+c_{0k}\right).
$$
约束：

（1）发电机约束：
$$
\underline p_k^g\le p_k^g\le\overline p_k^g,
\qquad
\underline q_k^g\le q_k^g\le\overline q_k^g,
\qquad k\in G.
$$
（2）电压和参考母线相角约束：
$$
\underline v_i\le |V_i|\le\overline v_i,
\qquad i\in N.
$$
$$
\theta_r=0.
$$
（3）节点功率平衡，复数形式：
$$
\sum_{k\in G_i}(p_k^g+\mathrm{i}q_k^g)
-(P_i^d+\mathrm{i}Q_i^d)
-(y_{ij}^{sh})^*|V_i|^2
=\sum_{(i,j)\in E_i\cup E_i^R}S_{ij}.
$$
其中$(y_{ij}^{sh})^*|V_i|^2$是并联元件的功率（连在母线和地之间），推导：

$$S_i^{sh}=V_i (I_i^{sh})^*=V_i(y_{ij}^{sh}V_i)^*=(y_{ij}^{sh})^*|V_i|^2$$
展开成实部和虚部：
$$
\sum_{k\in G_i}p_k^g-P_i^d-g_{ij}^{sh}|V_i|^2
=\sum_{(i,j)\in E_i\cup E_i^R}P_{ij},
$$
$$
\sum_{k\in G_i}q_k^g-Q_i^d+b_{ij}^{sh}|V_i|^2
=\sum_{(i,j)\in E_i\cup E_i^R}Q_{ij}.
$$
 （4）线路容量和相角差限制：
 $$
P_{ij}^2+Q_{ij}^2\le \left(\overline s_{ij}\right)^2,
\qquad (i,j)\in E\cup E^R.
$$
$$
\underline\delta_{ij}\le\theta_i-\theta_j\le\overline\delta_{ij},
\qquad (i,j)\in E.
$$
（5）潮流方程：由等值电路模型得到
$$W_{ij}=V_i I_{ij}^*=(y_{ij}^*+(y_{ij}^{sh})^*)\frac{|V_i|^2}{|\tau_{ij}|^2}-y_{ij}^* \frac{V_i V_j^*}{\tau_{ij}}$$
$$W_{ji}=V_j I_{ji}^*=(y_{ij}^*+(y_{ji}^{sh})^*) |V_j|^2 -y_{ij}^* \frac{V_i^* V_j}{\tau_{ij}^*}$$
总结：AC-OPF模型为
$$
\begin{aligned}
\min_{p^g,q^g,v,\theta,P,Q}\quad
&\sum_{k\in G}\left(c_{2k}(p_k^g)^2+c_{1k}p_k^g+c_{0k}\right)\\
\text{s.t.}\quad
&\underline p_k^g\le p_k^g\le\overline p_k^g,\quad
  \underline q_k^g\le q_k^g\le\overline q_k^g;\\
&\underline v_i\le v_i\le\overline v_i,\quad \theta_r=0;\\
&\sum_{k\in G_i}p_k^g-P_i^d-g_i^s v_i^2
  =\sum_{(i,j)}P_{ij};\\
&\sum_{k\in G_i}q_k^g-Q_i^d+b_i^s v_i^2
  =\sum_{(i,j)}Q_{ij};\\
&P_{ij}=\frac{g_{ij}+g_{ij}^{sh}}{t_{ij}^2}v_i^2
 -\frac{v_iv_j}{t_{ij}}
 [g_{ij}\cos(\Delta\theta_{ij})+b_{ij}\sin(\Delta\theta_{ij})];\\
&Q_{ij}=-\frac{b_{ij}+b_{ij}^{sh}}{t_{ij}^2}v_i^2
 +\frac{v_iv_j}{t_{ij}}
 [b_{ij}\cos(\Delta\theta_{ij})-g_{ij}\sin(\Delta\theta_{ij})];\\
&P_{ji}=(g_{ij}+g_{ji}^{sh})v_j^2
 -\frac{v_iv_j}{t_{ij}}
 [g_{ij}\cos(\Delta\theta_{ij})-b_{ij}\sin(\Delta\theta_{ij})];\\
&Q_{ji}=-(b_{ij}+b_{ji}^{sh})v_j^2
 +\frac{v_iv_j}{t_{ij}}
 [b_{ij}\cos(\Delta\theta_{ij})+g_{ij}\sin(\Delta\theta_{ij})];\\
&P_{ij}^2+Q_{ij}^2\le\overline s_{ij}^2;\\
&\underline\delta_{ij}\le\theta_i-\theta_j\le\overline\delta_{ij}.
\end{aligned}
$$

### DC-OPF

DC-OPF对物理方程做近似，具体如下：

（1）固定电压幅值，即假设输电网电压幅值接近额定值：
$$
 v_i\approx 1,
\qquad v_i^2\approx 1,
\qquad v_iv_j\approx 1.
$$
（2）小相角差近似，即对于相邻母线，假设 $|\Delta\theta_{ij}|$ 较小：
$$
\sin(\Delta\theta_{ij})\approx\Delta\theta_{ij},
\qquad
\cos(\Delta\theta_{ij})\approx 1.
$$
（3）忽略无功和电阻损耗，即只保留有功方程，并假设 $r_{ij}\ll x_{ij}$，忽略电导项和有功损耗。

在这些近似的共同的作用下，原模型约束变为：
（1）发电机约束，只保留有功项：
$$
\underline p_k^g\le p_k^g\le\overline p_k^g,
\qquad k\in G.
$$
（2）电压和参考母线相角约束，只保留参考母线相角约束：
$$
\theta_r=0.
$$
（3）节点功率平衡，只保留有功方程：
$$
\sum_{k\in G_i}p_k^g-P_i^d-g_{ij}^{sh}|V_i|^2
=\sum_{(i,j)\in E_i\cup E_i^R}P_{ij}.
$$
由于忽略电导项，因此$g_{ij}^{sh}\approx 0$，所以：
$$
\sum_{k\in G_i}p_k^g-P_i^d =\sum_{(i,j)\in E_i\cup E_i^R}P_{ij}.
$$
（4）线路容量和相角差限制，直接换成仅有功：
$$
-\overline s_{ij}\le P_{ij}\le \overline s_{ij}.
$$
（5）潮流方程，只保留有功：
$$P_{ij} = \frac{g_{ij}+g_{ij}^{sh}}{t_{ij}^{2}}v_i^2 -\frac{v_iv_j}{t_{ij}} \left[ g_{ij}\cos\alpha_{ij} +b_{ij}\sin\alpha_{ij} \right]$$
其中：$$\alpha_{ij}=\theta_i-\theta_j-\phi_{ij}.$$
进一步忽略电导项，$g_{ij}\approx 0, g_{ij}^{sh} \approx 0, b_{ij} \approx -\frac{1}{x_{ij}}, v_i v_j \approx 1$，$\sin \alpha_{ij}\approx \alpha_{ij}$，于是：
$$P_{ij}\approx \frac{\theta_i-\theta_j-\phi_{ij}}{t_{ij} x_{ij}}$$
进一步定义$b_{ij}^{dc}=\frac{1}{t_{ij}x_{ij}}$，则
$$P_{ij}=b_{ij}^{dc}(\theta_i-\theta_j-\phi_{ij})$$

总结：DC-OPF模型为
$$
\begin{aligned}
\min_{p^g,\theta,P}\quad
&\sum_{k\in G}\left(c_{2k}(p_k^g)^2+c_{1k}p_k^g+c_{0k}\right)\\
\text{s.t.}\quad
&\underline p_k^g\le p_k^g\le\overline p_k^g,\\
&\theta_r=0,\\
&P_{ij}=b_{ij}^{dc}(\theta_i-\theta_j-\phi_{ij}),\\
&\sum_{k\in G_i}p_k^g-P_i^d
=\sum_{(i,j)\in E_i\cup E_i^R}P_{ij},\\
&-\overline s_{ij}\le P_{ij}\le\overline s_{ij},\\
&\underline\delta_{ij}\le\theta_i-\theta_j\le\overline\delta_{ij}.
\end{aligned}
$$
