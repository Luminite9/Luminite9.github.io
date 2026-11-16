---
title: 最优潮流（SOC-OPF与QC-OPF）
category: Math
date: 2026-11-16
tag:
    - 电力
    - 优化
---

### SOC-OPF

SOC-OPF通过把电压的平方、乘积单独设成新变量，再放宽这些变量必须满足的关系，得到一个凸优化问题。

AC-OPF的非凸性主要来源于潮流方程，因此通过设新变量来对潮流方程进行简化，定义：

$$
W_{ii}=|V_i|^2=v_i^2,
$$
$$
W_{ij}=V_iV_j^{*}=w_{ij}^{r}+\mathrm{i}w_{ij}^{i}.
$$
由定义式$W$ 必须满足 $W=VV^H$，即
$$
|W_{ij}|^2=W_{ii}W_{jj}=(w_{ij}^{r})^2+(w_{ij}^{i})^2,
$$
SOC松弛将这一等式改为不等式：
$$
(w_{ij}^{r})^2+(w_{ij}^{i})^2\le W_{ii}W_{jj}.
$$
改写为二阶锥形式，得到：
$$
\left\|
\begin{bmatrix}
2w_{ij}^{r}\\
2w_{ij}^{i}\\
W_{ii}-W_{jj}
\end{bmatrix}
\right\|_2
\le W_{ii}+W_{jj}.
$$
其他AC-OPF约束的处理方法：
（1）发电机约束：保持不变
（2）电压和参考母线相角约束：
电压幅值范围变成：
$$
\underline v_i^2\le W_{ii}\le\overline v_i^2.
$$
参考母线相角约束在求解过程中不再需要，只有从$W$解中还原出原始解时还需要参考母线相角约束$\theta_r=0$。

（3）节点功率平衡，直接替换变量即可：
$$
\sum_{k\in G_i}p_k^g-P_i^d-g_{ij}^{sh}W_{ii}
=\sum_{(i,j)\in E_i\cup E_i^R}P_{ij},
$$
$$
\sum_{k\in G_i}q_k^g-Q_i^d+b_{ij}^{sh}W_{ii}
=\sum_{(i,j)\in E_i\cup E_i^R}Q_{ij}.
$$
（4）线路容量和相角差限制，线路容量约束可以写成：
$$
\left\|\begin{bmatrix}P_{ij}\\Q_{ij}\end{bmatrix}\right\|_2
\le\overline s_{ij}.
$$
相角差满足 $\theta_i-\theta_j=\arg(W_{ij})$，因此有
$$
\tan(\underline\delta_{ij})w_{ij}^{r}
\le w_{ij}^{i}
\le \tan(\overline\delta_{ij})w_{ij}^{r},
$$
（5）潮流方程，直接替换变量：
$$W_{ij}=(y_{ij}^*+(y_{ij}^{sh})^*)\frac{W_{ii}}{|\tau_{ij}|^2}-y_{ij}^* \frac{W_{ij}}{\tau_{ij}}$$
$$W_{ji}=(y_{ij}^*+(y_{ji}^{sh})^*) W_{jj} -y_{ij}^* \frac{W_{ij}^*}{\tau_{ij}^*}$$
总结：SOC-OPF模型为
$$
\begin{aligned}
\min_{p^g,q^g,W,P,Q}\quad
&\sum_{k\in G}\left(c_{2k}(p_k^g)^2+c_{1k}p_k^g+c_{0k}\right)\\
\text{s.t.}\quad
&\underline p_k^g\le p_k^g\le\overline p_k^g,\quad
  \underline q_k^g\le q_k^g\le\overline q_k^g;\\
&\underline v_i^2\le W_{ii}\le\overline v_i^2,\quad W_{rr}\text{ is referenced by }\theta_r=0;\\
&\sum_{k\in G_i}p_k^g-P_i^d-g_i^sW_{ii}=\sum_{(i,j)}P_{ij};\\
&\sum_{k\in G_i}q_k^g-Q_i^d+b_i^sW_{ii}=\sum_{(i,j)}Q_{ij};\\
&P_{ij}=(g_{ij}+g_{ij}^{sh})W_{ii}-g_{ij}w_{ij}^r-b_{ij}w_{ij}^i;\\
&Q_{ij}=-(b_{ij}+b_{ij}^{sh})W_{ii}+b_{ij}w_{ij}^r-g_{ij}w_{ij}^i;\\
&P_{ji}=(g_{ij}+g_{ji}^{sh})W_{jj}-g_{ij}w_{ij}^r+b_{ij}w_{ij}^i;\\
&Q_{ji}=-(b_{ij}+b_{ji}^{sh})W_{jj}+b_{ij}w_{ij}^r+g_{ij}w_{ij}^i;\\
&\left\|[P_{ij},Q_{ij}]^T\right\|_2\le\overline s_{ij};\\
&\left\|[2w_{ij}^{r},2w_{ij}^{i},W_{ii}-W_{jj}]^T\right\|_2
\le W_{ii}+W_{jj};\\
&\tan(\underline\delta_{ij})w_{ij}^r\le w_{ij}^i
\le\tan(\overline\delta_{ij})w_{ij}^r.
\end{aligned}
$$

### QC-OPF

和SOC-OPF进行类似的变量代换，但是需要对每个非凸项使用带边界的二次凸包络。

#### McCormick 包络

先考虑一个通用问题：
$$z=xy,\qquad x\in[x_L,x_U],\quad y\in[y_L,y_U].$$
显然有$(x-x_L)(y-y_L)\ge 0$，展开得到
$$xy\ge x_L y+y_L x -x_L y_L$$
于是有：
$$z\ge x_L y+y_L x -x_L y_L$$
利用其他端点组合，同样得到另外三条，组合起来是
合起来是：
 $$\begin{aligned} z&\ge x_Ly+y_Lx-x_Ly_L,\\ z&\ge x_Uy+y_Ux-x_Uy_U,\\ z&\le x_Uy+y_Lx-x_Uy_L,\\ z&\le x_Ly+y_Ux-x_Ly_U. \end{aligned} $$
 记这四条线性不等式为$\mathcal{M}(x,y,z)$，围成的区域包含所有真实乘积点。此即为McCormick 包络。

#### 问题改写

AC 模型中的电压乘积是：
$$W_{ii}=|V_i|^2=v_i^2, W_{ij}=V_iV_j^*=w_{ij}^r + \mathrm{i} w_{ij}^i$$
于是$$w_{ij}^r=v_iv_j\cos(\theta_i-\theta_j), \qquad w_{ij}^i=v_iv_j\sin(\theta_i-\theta_j). $$
在此基础上，引入更多的辅助变量：
$$\delta_{ij}=\theta_i - \theta_j$$
$$w_i=v_i^2,u_{ij}=v_iv_j,c_{ij}=\cos \delta_{ij},s_{ij}=\sin \delta_{ij},w_{ij}^r=u_{ij}c_{ij}, w_{ij}^i=u_{ij}s_{ij}$$
$$W_{ij}=w_{ij}^r+\mathrm{i}w_{ij}^i$$
如果全部保留这些等式，仍然是非凸模型，因此接下来用凸区域包住每个等式对应的曲线或曲面。

（1）平方关系 $w_i=v_i^2$：
因为$v_i$满足范围：$$ \underline v_i\le v_i\le\overline v_i. $$
利用二次函数凸性，其曲线位于两端点连线的下方，而两端点的连线可以表示为：
$$w_i=(\underline v_i+\overline v_i)v_i -\underline v_i\overline v_i.$$
所以可以将等式$w_i=v_i^2$放松为：
$$
v_i^2 \le w_i \le (\underline v_i+\overline v_i)v_i -\underline v_i\overline v_i \tag{QC-SQ}
$$
（2）乘积关系$u_{ij}=v_iv_j$：
在McCormick 包络公式中，将$$ x=v_i,\qquad y=v_j,\qquad z=u_{ij} $$
代入，即可得到对$u_{ij}=v_iv_j$的凸松弛$\mathcal{M}(v_i,v_j,u_{ij})$。同理可得$w_{ij}^r=u_{ij}c_{ij}, w_{ij}^i=u_{ij}s_{ij}$的凸松弛$\mathcal{M}(u_{ij},c_{ij},w_{ij}^r)$和$\mathcal{M}(u_{ij},s_{ij},w_{ij}^i)$。记：
$$

\mathcal M(v_i,v_j,u_{ij}),\qquad
\mathcal M(u_{ij},c_{ij},w_{ij}^r),\qquad
\mathcal M(u_{ij},s_{ij},w_{ij}^i).

\tag{QC-PROD}
$$
（3）正弦和余弦：
$$
\delta_{ij}=\theta_i-\theta_j,\qquad a\le\delta_{ij}\le b.
\tag{QC-ANGLE}
$$
对正弦和余弦函数在特定段上的图像取凸包：
$$
(\delta_{ij},s_{ij}) \in \operatorname{conv} \{(\delta,\sin\delta):\delta\in[a,b]\}
$$
$$
(\delta_{ij},c_{ij}) \in \operatorname{conv} \{(\delta,\cos\delta):\delta\in[a,b]\}
$$
 有的时候为了计算方便不会真正的取闭包，可能会做更进一步的近似，对$a<b$，定义
$$
d=\max(|a|,|b|)>0,
$$
$$
L_h(\delta)=h(a)+\frac{h(b)-h(a)}{b-a}(\delta-a),
\qquad h\in\{\sin,\cos\},
$$
$$
T_+(\delta)=\cos(d/2)(\delta-d/2)+\sin(d/2),
$$
$$
T_-(\delta)=\cos(d/2)(\delta+d/2)-\sin(d/2).
$$
$L_h$ 是端点弦线；$T_+$、$T_-$ 是正弦在 $d/2$、$-d/2$ 处的切线。

那么正弦包络：
$$

\begin{cases}
T_-(\delta_{ij})\le s_{ij}\le T_+(\delta_{ij}),
& a<0<b,\\
T_-(\delta_{ij})\le s_{ij}\le L_{\sin}(\delta_{ij}),
& b\le0,\\
L_{\sin}(\delta_{ij})\le s_{ij}\le T_+(\delta_{ij}),
& a\ge0.
\end{cases}

\tag{QC-SIN}
$$
余弦包络：
$$
L_{\cos}(\delta_{ij})\le c_{ij}
\le1-\frac{1-\cos d}{d^2}\delta_{ij}^2.

\tag{QC-COS}
$$
潮流方程，直接进行符号替换：
$$
S_{ij}=
\left(y_{ij}^*+(y_{ij}^{sh})^*\right)\frac{w_i}{t_{ij}^2}
-\frac{y_{ij}^*}{\tau_{ij}}W_{ij},
$$
$$
S_{ji}=
\left(y_{ij}^*+(y_{ji}^{sh})^*\right)w_j
-\frac{y_{ij}^*}{\tau_{ij}^*}W_{ij}^*.

\tag{QC-FLOW}
$$
功率平衡，直接进行符号替换：
$$
\begin{aligned}
\sum_{k\in G_i}p_k^g-P_i^d-g_i^s w_i
&=\sum_{(i,j)}P_{ij},\\
\sum_{k\in G_i}q_k^g-Q_i^d+b_i^s w_i
&=\sum_{(i,j)}Q_{ij}.
\end{aligned}
\tag{QC-KCL}
$$
线路容量：
$$
P_{ij}^2+Q_{ij}^2\le\overline s_{ij}^{\,2}.
\tag{QC-THERMAL}
$$

### 电流平方约束

Recall前面的等值电路中：
$$
U_a=\frac{V_i}{\tau_{ij}},\quad
I_s=y_{ij}(U_a-V_j),\quad
I_a=I_s+y_{ij}^{sh}U_a.
$$
$$P_{ij}^2+Q_{ij}^2=|S_{ij}|^2=|U_a I_a^*|^2=|U_a|^2 |I_a|^2=\frac{|V_i|^2}{t_{ij}^2}|I_a|^2=\frac{w_i}{t_{ij}^2}|I_a|^2$$
设一个新的提升变量$l_{ij}=|I_a|^2$，则
$$P_{ij}^2+Q_{ij}^2 =\frac{w_i}{t_{ij}^2} l_{ij}$$
QC使用松弛：
$$

P_{ij}^2+Q_{ij}^2
\le\frac{w_i}{t_{ij}^2}\ell_{ij},\qquad
w_i\ge0,\quad\ell_{ij}\ge0.

\tag{QC-CURRENT-SOC}
$$

由 $I_s=I_a-y_{ij}^{sh}U_a$，
$$
|I_a|^2
=|I_s|^2-|y_{ij}^{sh}|^2|U_a|^2
+2\Re(y_{ij}^{sh}S_{ij}).
$$
又由$I_s=y_{ij}(U_a-V_j)$，
$$
|I_s|^2
=|y_{ij}|^2\left(
\frac{|V_i|^2}{t_{ij}^2}+|V_j|^2
-2\Re\frac{V_iV_j^*}{\tau_{ij}}
\right).
$$
替换电压乘积后，得到必须保留的线性关联式：
$$

\begin{aligned}
\ell_{ij}
&={}(g_{ij}^2+b_{ij}^2)
\left(\frac{w_i}{t_{ij}^2}+w_j-2\Re\left(\frac{W_{ij}}{\tau_{ij}}\right)\right)\\
&-\left((g_{ij}^{sh})^2+(b_{ij}^{sh})^2\right)\frac{w_i}{t_{ij}^2}
+2\left(g_{ij}^{sh}P_{ij}-b_{ij}^{sh}Q_{ij}\right).
\end{aligned}

\tag{QC-CURRENT-LINK}
$$
显式保留电压乘积锥约束：
$$
(w_{ij}^r)^2+(w_{ij}^i)^2\le w_iw_j.
\tag{QC-W-SOC}
$$

引入的辅助变量的取值范围的合集称为QC-BOUNDS，并保留与SOC一致的相角约束：
$$
\tan(a)w_{ij}^r\le w_{ij}^i\le\tan(b)w_{ij}^r.
\tag{QC-SECTOR}
$$
综上，QC-OPF的完整模型为：

$$
\begin{aligned}
\min\quad&
\sum_{k\in G}\left(c_{2k}(p_k^g)^2+c_{1k}p_k^g+c_{0k}\right)\\
\text{s.t.}\quad&
\underline p_k^g\le p_k^g\le\overline p_k^g,\quad
\underline q_k^g\le q_k^g\le\overline q_k^g
&&k\in G,\\
&\theta_r=0,\\
&\text{(QC-SQ), (QC-BOUNDS)}
&&i\in N,\\
&\text{(QC-ANGLE), (QC-SIN), (QC-COS), (QC-PROD)}
&&(i,j)\in E,\\
&\text{(QC-BOUNDS)}
&&(i,j)\in E,\\
&\text{(QC-FLOW)}
&&(i,j)\in E,\\
&\text{(QC-KCL)}
&&i\in N,\\
&\text{(QC-THERMAL)}
&&(i,j)\in E,\\
&\text{(QC-CURRENT-LINK), (QC-CURRENT-SOC), (QC-W-SOC)}
&&(i,j)\in E,\\
&\text{(QC-SECTOR)}
&&(i,j)\in E.\\
\end{aligned}
$$

