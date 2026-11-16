---
title: 多目标优化（二）
category: Math
date: 2026-7-31
tag:
    - 优化
---

## 数值优化方法

### 总体思路

把标量化问题写成
$$
\min_{x\in X} g(x;\theta),
$$
改变 $\theta$，便得到一系列不同的单目标问题：
$$
\theta^{(1)},\theta^{(2)},\ldots,\theta^{(K)}
\quad\Longrightarrow\quad
x^{(1)},x^{(2)},\ldots,x^{(K)}.
$$
这种方法通常是逐点构造Pareto前沿，每求解一次单目标问题，得到一个候选Pareto点。标量化方法中所有方法都可以用这个思路来获得Pareto前沿。这里讨论另一种方法：直接求解刻画局部有效点的一阶条件，并通过延拓或同伦方法追踪整个解集。

**局部有效点**

$x^\star\in X$ 定义为局部有效点，当且仅当存在某个 $\varepsilon>0$，使得在以 $x^\star$ 为中心的开球
$$
B_\varepsilon(x^\star)
=
\{y\in X:\|y-x^\star\|<\varepsilon\}
$$
中，不存在另一个点能够 Pareto 支配它。

### Fritz–John条件

Fritz–John必要条件：
若 $x^\star$ 是局部有效点，则存在一个非负权重向量$\lambda=(\lambda_1,\ldots,\lambda_m)^\top\geq 0$，满足
$$
\sum_{i=1}^{m}\lambda_i=1,
$$
且
$$
\sum_{i=1}^{m}\lambda_i\nabla f_i(x^\star)=0.
$$
未知量$m+n$个，方程有$n+1$，因此方程应该刻画了一个$m-1$维的流形。
但是这不是充分条件，得到候选点后，还要检查局部有效性、二阶性质或邻域中的支配关系。

### Karush–Kuhn–Tucker(KKT)条件方法

为了简洁只讨论了无约束问题，并给出结论：
若$x^\star$满足Fritz–John条件，并且所有目标函数在某个邻域
$$
B_\varepsilon(x^\star)
$$
内都是凸函数，则 $x^\star$ 是局部有效点。


### 延拓法

定义方程组：
$$G(z)=\begin{pmatrix}\displaystyle\sum_{i=1}^{m}\lambda_i\nabla f_i(x)\\[6pt]\displaystyle\sum_{i=1}^{m}\lambda_i-1\end{pmatrix}=0.$$

从一个已知解出发，逐步扩展到附近的其他解。假设已经找到一个解$$z^{(0)}=\begin{pmatrix}x^{(0)}\\\lambda^{(0)}\end{pmatrix},\qquad G(z^{(0)})=0.$$算法需要维护一个档案集合 $A$，保存已经获得的解：

$$
A=\{x^{(1)},x^{(2)},\ldots,x^{(N)}\}.
$$
在当前解 $z$ 处，对方程 $G(z)=0$ 线性化：
$$
G(z+\Delta z)
\approx
G(z)+J_G(z)\Delta z,
$$
其中 $J_G(z)$ 是 $G$ 的 Jacobian 矩阵。

若希望 $\Delta z$ 沿解集的切向方向移动，应满足
$$
J_G(z)\Delta z=0.
$$
切空间可以写成$T_z\mathcal{M}=\ker J_G(z)$。该切空间是一个$m-1$维的超平面。

### 同伦法与预测—校正过程

从延拓法得到的切平面出发，选择一个切方向$v$，构造预测点$z_{\mathrm{pred}}=z+\delta v$，预测点一般不严格满足$G(z_{pred})=0$的条件，但这个点应该位于真实解流形附近。

选择尚未覆盖的方向，选择方向：$$v^\star=\arg\max_{v\in T_z\mathcal{M},\,\|v\|=1}D(z+\delta v,A),$$
其中 $D(\cdot,A)$ 表示候选预测点与档案中已有点之间的某种距离或覆盖度指标。

校正预测点，
$$
\min_{x,\lambda}
\left\|
\sum_{i=1}^{m}\lambda_i\nabla f_i(x)
\right\|^2
$$
意在让点能够重新回到Fritz–John/KKT解集附近。

接受或记录失败方向，如果成功获得了正确的解，那么记录到$A$中，否则将这个$v^*$记录为失败方向。下一次寻找方向时避免。

### 多层细分法

多层细分法的基本思想是将决策空间递归地划分为越来越小的区域。设初始搜索区域为$X^{(0)}$，第一层将它分成若干子区域：
$$
X^{(0)}
=
\bigcup_{j=1}^{N_1}X_j^{(1)}.
$$
随后，仅对可能包含Pareto解的区域继续细分：
$$
X_j^{(1)}
=
\bigcup_{k=1}^{N_{2,j}}X_{jk}^{(2)}.
$$
这一过程形成一个层次结构
$$
X^{(0)}
\supset
X^{(1)}
\supset
X^{(2)}
\supset
\cdots.
$$
目标是用一组小单元覆盖Pareto集：
$$
X_E
\subseteq
\bigcup_{C\in\mathcal{C}_k}C,
$$
并随着细分层数 $k$ 增加，使单元直径逐渐减小。

适用于全局搜索，但是受到维数限制，因为随着维度增加搜索空间急剧扩大。

### 贝叶斯优化

适用于一次函数评估代价很高的场景。先构建概率代理模型。
$$
f_i(x)\sim\mathcal{GP}\bigl(\mu_i(x),k_i(x,x')\bigr),
$$
其中$\mu_i(x)$ 是先验或后验均值，$k_i(x,x')$ 是协方差核，观测数据用于更新目标函数在未知区域中的预测分布。

算法在第 $t$ 轮利用采集函数选择下一个评估点：
$$
x^{(t+1)}
=
\arg\max_{x\in X}\alpha_t(x).
$$
采集函数 $\alpha_t(x)$通常在探索和利用之间权衡。

同样适用于全局搜索，但同样在高维场景下失效。

### Lipschitz全局优化

简而言之，如果确定函数有Lipschitz连续性，那么可以利用这一条性质排除一些不可能改进现有非支配解集的空间。同样只适用于低维情形。

### 无导数直接搜索方法

不算梯度，通过在当前点附近直接试探函数值来决定移动方向。通常适用于目标函数无法计算梯度的情形。

## 进化多目标优化算法(MOEA)

### 共同结构

典型MOEA通过维护一个种群
$$
P_t=\left\{x_t^{(1)},x_t^{(2)},\ldots,x_t^{(\mu)}\right\}
$$
来近似Pareto前沿，其中 $t$ 表示代数，$\mu$ 表示种群规模。其基本循环通常包括：

从当前种群选择父代；通过重组和变异产生候选解；评价候选解的多个目标函数；根据某种多目标选择准则构造下一代；重复上述过程。

### 基于Pareto的方法：NSGA-II

NSGA-II 的选择机制由两个层级组成：
**收敛性层级**：利用Pareto支配关系执行非支配排序；
**多样性层级**：对处于同一非支配层的个体，利用拥挤距离进行排序。
试图同时实现将种群推向 Pareto 前沿和使保留下来的解沿 Pareto 前沿尽量分散两个目标。

**非支配排序：** 记 $\operatorname{ND}(P)$ 为种群 $P$ 中所有非支配个体的集合。第一层定义为$R_1=\operatorname{ND}(P).$ 移除第一层后，再从剩余个体中提取非支配解：
$$R_{k+1}
=
\operatorname{ND}
\left(
P\setminus\bigcup_{i=1}^{k}R_i
\right),
\quad k=1,2,\ldots
$$
于是种群被划分为
$$
P=R_1\cup R_2\cup\cdots\cup R_\ell,
$$
层号越大，Pareto排名越差，而同一层中的个体排名则无法比较。

**拥挤距离：** 设$R$是某个非支配层，$x\in R$。对于第$i$个目标，定义$x$左右两侧最近邻的目标函数值为
$$
l_i(x)=
\max
\left(
\left\{
f_i(y)
\mid
y\in R\setminus\{x\},
\ f_i(y)\leq f_i(x)
\right\}
\cup\{-\infty\}
\right),
$$
$$
u_i(x)=
\min
\left(
\left\{
f_i(y)
\mid
y\in R\setminus\{x\},
\ f_i(y)\geq f_i(x)
\right\}
\cup\{+\infty\}
\right).
$$
第 $i$ 个目标上的局部间距为$c_i(x)=u_i(x)-l_i(x).$论文给出的总体拥挤距离为$c(x)=\frac{1}{m}\sum_{i=1}^{m}c_i(x).$直观上，$c(x)$ 越大，说明 $x$ 周围越稀疏，这些个体倾向被保留下来，对于边界点，通常具有无限大的拥挤距离。

**完整流程：** 对每个待生成的子代，选择两个父代（优先选择非支配层号较小、拥挤距离较大的父代个体）。然后进行重组$r_t^{(i)}=\operatorname{recombine}\left(x^{(1)},x^{(2)}\right)$和变异$q_t^{(i)}=\operatorname{mutate}\left(r_t^{(i)}\right)$产生子代。生成 $\mu$ 个子代后，形成
$$
Q_t
=
\left\{
q_t^{(1)},\ldots,q_t^{(\mu)}
\right\}.
$$
然后进行父代子代合并：$U_t=P_t\cup Q_t$。对 $U_t$ 执行非支配排序，得到
$$
R_1,R_2,\ldots,R_\ell.
$$
从 $R_1$ 开始依次加入下一代，直到遇到第一个无法整体放入的层 $R_{i_\mu}$，然后从$R_{i_\mu}$中按拥挤距离从大到小选择，得到集合 $H$，并令
$$
P_{t+1}
=
\left(
\bigcup_{j=1}^{i_\mu-1}R_j
\right)
\cup H.
$$
即可得到下一代。

**其他Pareto型算法：** MOGA，PAES，SPEA/SPEA2，Multiobjective Micro-GA，DEMO

### 基于指标的方法：SMS-EMOA

基于指标的方法除了比较Pareto层级之外，还要用一个标量指标来评价整个近似集的质量。

**超体积指标：** 设 $A\subset\mathbb R^m$ 是一个Pareto前沿近似集，$r\in\mathbb R^m$ 是参考点。对于最小化问题，参考点通常位于近似集的某个较差方向。
$$
\operatorname{HI}(A)
=
\operatorname{Vol}
\left(
\left\{
y\in\mathbb R^m
\mid
y\preceq_{\mathrm P}r
\ \land\
\exists a\in A:\ a\preceq_{\mathrm P}y
\right\}
\right).
$$
其中 $\operatorname{Vol}(\cdot)$ 表示勒贝格测度。其意义为由近似集 $A$ 中至少一个点支配、同时又被参考点 $r$ 截断的空间区域大小。
超体积可以同时反映收敛性和分布性：近似点越靠近真实Pareto前沿，支配区域通常越大，近似点覆盖的前沿范围越广、重叠越少，超体积通常越大。

**超体积贡献：** 给定点集 $Y$ 和其中一点 $y\in Y$，$y$ 的独占超体积贡献定义为
$$
\Delta\operatorname{HI}(y;Y)
=
\operatorname{HI}(Y)
-
\operatorname{HI}
\left(
Y\setminus\{y\}
\right).
$$
**完整流程：** 设当前种群为 $P_t$，规模为 $\mu$。从 $P_t$ 中选择两个父代，进行重组和变异后，临时扩充种群：
$$
U_t=P_t\cup\{q_t\}.
$$
理想目标是选择大小为 $\mu$ 且超体积最大的子集，这等价于删除超体积贡献最小的个体
$$
y^-=
\arg\min_{y\in U_t}
\Delta\operatorname{HI}(y;U_t),
$$
**其他基于指标的方法：** IBEA，HypE，MO-CMA-ES，R2指标型方法，平均Hausdorff距离指标，$\alpha$-指标方法，POSEA。
### 基于分解的方法：MOEA/D

核心思想是把一个多目标问题分解为多个标量子问题。

设有 $\mu$ 个权重向量：
$$
\Lambda
=
\left\{
\lambda^{(1)},
\lambda^{(2)},
\ldots,
\lambda^{(\mu)}
\right\}.
$$
每个权重向量 $\lambda^{(i)}$ 定义一个标量子问题，并对应一个当前解 $x^{(i)}$。整个种群可以写成
$$
P_t
=
\left\{
x_t^{(1)},
x_t^{(2)},
\ldots,
x_t^{(\mu)}
\right\}.
$$
例如对于双目标问题，给出的一个均匀权重示例为
$$
\lambda^{(i)}
=
\left(
\frac{k-i}{k},
\frac{i}{k}
\right)^{\mathsf T},
\quad
i=0,\ldots,k.
$$
MOEA/D主要采用增强Chebyshev标量化。设 $z^\ast$ 为参考点或理想点，$\lambda^{(i)}$ 为第 $i$ 个权重向量，则第 $i$ 个子问题可写成
$$
g
\left(
x\mid\lambda^{(i)},z^\ast
\right)
=
\max_{j\in\{1,\ldots,m\}}
\left\{
\lambda_j^{(i)}
\left|
f_j(x)-z_j^\ast
\right|
\right\}
+
\varepsilon
\sum_{j=1}^{m}
\left|
f_j(x)-z_j^\ast
\right|
$$
第 $i$ 个子问题的目标是
$$
\min_{x\in X}
g
\left(
x\mid\lambda^{(i)},z^\ast
\right).
$$
不同的 $\lambda^{(i)}$ 对目标偏好不同，因此对应Pareto前沿的不同区域。

**权重邻域：** 对于第 $i$ 个权重向量 $\lambda^{(i)}$，根据欧氏距离定义权重距离：$d_{ij}=\left\|\lambda^{(i)}-\lambda^{(j)}\right\|_2$。
选择距离 $\lambda^{(i)}$ 最近的 $k$ 个权重向量所对应的子问题，形成邻域$B(i)$，其中通常包括子问题 $i$ 本身。

**完整流程：** 初始化权重与种群；初始化邻域（对每个 $i$，计算 $\lambda^{(i)}$ 与其他权重向量的欧氏距离，并取最近的 $k$ 个子问题形成 $B(i)$）；

逐个处理子问题：
1. 对于每个$i$，从 $B(i)$ 对应的当前解中随机选择两个父代
2. 执行问题相关的重组，产生候选解$y$（使用局部搜索或启发式改进$y$）
3. 比较候选解和当前解在第 $i$ 个标量子问题上的值
若$$g\left(y\mid\lambda^{(i)},z^\ast\right)<g\left(x^{(i)}\mid\lambda^{(i)},z^\ast\right)$$
则执行替换：$x^{(i)}\leftarrow y$。
然后更新参考点：若新解在某个目标上优于当前参考点，则更新对应分量
$$
z_j^\ast
\leftarrow
\min
\left\{
z_j^\ast,f_j(y)
\right\},
\quad
j=1,\ldots,m.
$$
这种设计的假设是权重向量相近的子问题，其优良解也往往具有相近结构。

**其他基于分解的方法：** VEGA，Kursawe的向量优化方法，NSGA-III，Generalized Decomposition，Directed Search

## 群智能多目标优化算法

### 共同框架

1. 随机生成初始智能体种群，并设置参数。
2. 计算各智能体的目标函数值或适应度。
3. 根据当前种群及其环境信息更新智能体。
4. 产生新的候选智能体。
5. 保留适应度较好或非支配的解。
6. 重复上述过程，直到满足终止条件。

这里的方法基本上和传统的群体智能算法差别不大，只在判断标准上做了一些改变（单目标优化直接以目标函数值作为判断标准，多目标版本则以Pareto最优性作为判断标准）