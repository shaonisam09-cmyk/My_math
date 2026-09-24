# MPC 与概率理论的一般性连接

日期：2026-09-08。仅研究指定论文中的 MPC 与乘法更新；不使用旧 MATLAB 代码，不修改论文原稿。详细辅助证明见同目录其他研究文件。

后续实例验证：2026-09-09 用户确认实际储能和温度不添加额外执行误差。基于该条件对当前论文逐项验证的结果见 [当前论文模型的证书验证](<../../theory_exploration_20260909/model_validation/当前论文模型的证书验证.md>)。本文件中的一般充分条件与新结论中的实例认证结果应分开阅读。

## 1. 本轮建立的连接

可以给出两个带明确、可检验条件的一般性连接：

$$
\boxed{
\text{完整 MPC 的最优解余量证书}
\ \longrightarrow\
\text{条件违约概率证书}
\ \longrightarrow\
\text{原乘法更新的长期风险保证}.
}
$$

$$
\boxed{
\text{MPC 余量对目标余量的偏离}
\ \longrightarrow\
\text{概率偏离}
\ \longrightarrow\
\text{Poisson 方程控制性能偏离}.
}
$$

第一条将前次定理 8 的抽象恢复假设转化为优化问题和预测误差的条件；第二条允许实际 MPC 依赖完整历史，并量化端点概率策略的误差界能以多大代价迁移过去。这里没有宣称任何凸 MPC 都自动满足所需条件。

## 2. 完整 MPC 的确定性输出如何决定概率

固定一个约束，写作 $G s_{t+1}\le g$。定义松弛幅值 $r_t=-h_t>0$，并令 $\zeta_t$ 包含决策时已知的所有其余信息：当前物理状态、未完成任务、预测、价格、其他松弛量等。整个预测时域的计划向量记为 $v$。

对数变量采用同单位的固定参考幅值 $r_{\rm ref}>0$ 归一化，即 $\theta_t=\log(r_t/r_{\rm ref})$，避免对带单位量直接取对数；所有对数增量及最终风险界均与参考幅值选择无关。

在论文的凸分段线性终端成本前提下，利用上图变量，完整 MPC 可写成 LP：

$$
J^*(\zeta,r)=\min_{v\in\mathcal P(\zeta,r)}c(\zeta)^\top v,
\qquad
\mathcal V^*(\zeta,r)=\arg\min_{v\in\mathcal P(\zeta,r)}c(\zeta)^\top v.
\tag{2.1}
$$

$\mathcal P$ 包括所有预测时域约束，而非仅第一步约束。假设讨论参数下最优值有限、最优解存在，实际使用可测的最优解选择。

第一步名义违约余量为

$$
m(\zeta,r,v)=G\widehat s_{t+1|t}(v)-g
=a(\zeta)^\top v+b(\zeta).
\tag{2.2}
$$

负余量表示名义安全裕度，正余量表示名义越界。定义实际误差 $e_{t+1}$，使

$$
G s_{t+1}-g=m_t+e_{t+1}.
$$

误差必须包括实际扰动、模型偏差、实施动作与计划动作之间的差异。这个分解是恒等式，并不要求独立加性误差模型。

令 $\mathcal F_t$ 包含当前求解结果和动作选择后的完整信息。$m_t$ 已知，条件 CDF 为
$F_t(z)=\mathbb P(e_{t+1}\le z\mid\mathcal F_t)$。因为违约按严格大于零定义，

$$
\boxed{p_{t+1}=1-F_t(-m_t).}
\tag{2.3}
$$

此式对离散分布及带原子的分布也正确，避免了直接使用广义分位数时的边界歧义。

## 3. 连接定理一：从最优面和条件 CDF 导出恢复方向

### 3.1 可检查的条件

假设误差的条件 CDF 有统一非减包络

$$
\underline F(z)\le F_t(z)\le\overline F(z).
\tag{3.1}
$$

这可以是一个精确模型，也可以是保守包络；不要求时间独立。但无条件残差直方图并不能自动证明对全部历史的条件包络。

再假设有 $0<r_L<r_H$、余量阈值 $\ell_m,u_m$，使在指定运行信息域内对所有 $\zeta$ 一致有

$$
\sup_{v\in\mathcal V^*(\zeta,r)}m(\zeta,r,v)\le\ell_m
\quad (0<r\le r_L),
\tag{3.2}
$$

$$
\inf_{v\in\mathcal V^*(\zeta,r)}m(\zeta,r,v)\ge u_m
\quad (r\ge r_H).
\tag{3.3}
$$

这里使用全部最优解，因而处理了 LP 多解与退化；若只分析一个指定解选择，必须在算法与证明中固定该选择。

定义

$$
p_L=1-\underline F(-\ell_m),\qquad
p_H=1-\overline F(-u_m).
$$

**定理 A。** 式 (3.1)–(3.3) 推出

$$
\boxed{
r_t\le r_L\Rightarrow p_{t+1}\le p_L,\qquad
r_t\ge r_H\Rightarrow p_{t+1}\ge p_H.
}
\tag{3.4}
$$

**证明。** 小松弛时 $m_t\le\ell_m$，所以
$F_t(-m_t)\ge\underline F(-m_t)\ge\underline F(-\ell_m)$。代入式 (2.3) 得到上界。大松弛时 $m_t\ge u_m$，所以
$F_t(-m_t)\le\overline F(-m_t)\le\overline F(-u_m)$，得到下界。证毕。

这个推导不需要第一步余量在全范围上随松弛量单调，也不要求概率仅取决于保留计数。

### 3.2 接入原乘法更新

令 $n=w-1$，并定义

$$
a_w=\frac{w\alpha-1/2}{w-1},\qquad
f_j=\log\left(1+\frac{\alpha-1/(2w)-j/w}{\chi}\right),
$$

$$
\Psi(p)=\sum_{j=0}^{n}\binom njp^j(1-p)^{n-j}f_j.
$$

采用前次研究的参数条件

$$
w\ge2,\quad \frac1{2w}<\alpha<1-\frac1{2w},
\quad \chi>1-\alpha-\frac1{2w}.
\tag{3.5}
$$

$f_j$ 严格递减，故二项计数的单调耦合说明 $\Psi$ 连续严格递减，且
$\Psi(0)=f_0>0>f_n=\Psi(1)$。记唯一零点为 $p_\dagger$，严格 Jensen 不等式给出 $0<p_\dagger<a_w$。

**定理 B：MPC 证书导出的长期风险保证。** 若定理 A 的条件沿闭环始终成立，且

$$
\boxed{p_L<p_\dagger<p_H,}
\tag{3.6}
$$

则从任意处于已认证范围内的有限确定初始对数幅值与二值初始记忆出发，原乘法更新满足

$$
\sup_t\mathbb E e^{\eta|\log((-h_t)/r_{\rm ref})|}\le C
$$

对某些 $\eta>0,C<\infty$ 成立，并且几乎必然有

$$
\boxed{
\frac{f_0}{f_0-f_n}
\le\liminf_N\frac1N\sum_{t=1}^{N}O_t
\le\limsup_N\frac1N\sum_{t=1}^{N}O_t
\le a_w.
}
\tag{3.7}
$$

平均条件风险的上下极限也满足同一区间。$\alpha\le1/2$ 时上界不超过名义目标 $\alpha$；离散支撑还允许进一步收紧上界。

**证明。** 令 $\theta_t=\log(r_t/r_{\rm ref})$。选足够大 $R$ 使 $r_{\rm ref}e^{-R}\le r_L$、$r_{\rm ref}e^R\ge r_H$。由定理 A，正负远端的条件概率分别至少为 $p_H$、至多为 $p_L$。

每步对数增量是 $f(S_t)$，$S_t$ 为最新 $n$ 位之和。记 $L=\max_j|f_j|$ 和
$\beta=\min\{\Psi(p_L),-\Psi(p_H)\}>0$。选 $m\ge n$，使
$d=(m-n+1)\beta-(n-1)L>0$，记 $M=mL$。

从 $\theta_t\ge R+M$ 出发，随后整个块的生成状态均处于正端。每位的条件概率至少为 $p_H$，所以初始记忆被替换后，最新 $n$ 位的计数随机占优于 $\mathrm{Bin}(n,p_H)$。由于 $f$ 递减，后续各步条件期望增量至多为 $-\beta$，前 $n-1$ 步至多为 $L$，故整块条件期望增量至多为 $-d$。负端同理至少为 $d$。

这个随机比较不要求实际位独立：可使用逐步条件 Bernoulli 的独立均匀创新表示进行比较，或逐步取条件期望，以后向二项递推证明占优。完整细节见前次研究的 Theorem 6g。

远端块内不越过零点，且块增量绝对值不超过 $M$。对 $W_t=e^{\eta|\theta_t|}$ 作 Taylor 上界，取充分小的 $\eta>0$，得到
$\mathbb E[W_{t+m}\mid\mathcal F_t]\le\lambda W_t+B$
其中 $\lambda<1,B<\infty$。迭代几何级数并控制中间步，即得均匀指数矩。指数 Markov 不等式与 Borel–Cantelli 推出 $\theta_N/N\to0$ 几乎必然。

原递推逐路径满足

$$
\bar O_N=a_w-\frac{\chi(\theta_N-\theta_0)}{(n/w)N}
-\frac{\chi}{n/w}\frac1N\sum_{t=1}^{N}
\left[\frac{b_t}{\chi}-\log(1+b_t/\chi)\right]
-\frac{D_N}{nN},
$$

其中 $|D_N|\le n(n-1)/2$，中括号非负，因而得上界。对凹函数
$H(z)=w[\alpha-1/(2w)-\chi(e^z-1)]$，连接 $(f_n,n)$ 与 $(f_0,0)$ 的弦位于曲线下方。逐时刻应用弦不等式并求和，得到

$$
\bar O_N\ge\frac{f_0}{f_0-f_n}
-\frac{\theta_N-\theta_0}{N(f_0-f_n)}
-\frac{D_N}{nN}.
$$

首尾项和对数项消失，得到式 (3.7)。最后 $O_t-p_t$ 是有界鞅差，时间平均趋零，故平均条件风险也有同样界。证毕。

**运行域条件。** 若 MPC 证书只在 $\mathcal D$ 内成立，就必须另行保证闭环一直留在该域且问题持续可解。可以把它明确列为假设，也可以由独立不变性/递归可行性证明支持；不能用有限采样替代。离开认证域后，不再有无条件的全时域保证。定理 B 并未证明整个物理系统或任务队列稳定。

## 4. 如何获得最优解证书

### 4.1 最优面上的辅助 LP

固定 $(\zeta,r)$，先求 $J^*$，再求

$$
m_{\min}=\inf\{a^\top v+b:
v\in\mathcal P(\zeta,r),\ c^\top v=J^*\},
$$

$$
m_{\max}=\sup\{a^\top v+b:
v\in\mathcal P(\zeta,r),\ c^\top v=J^*\}.
\tag{4.1}
$$

它们仍是 LP。若最优面上的余量有界且极值达到，就可以使用通常的 min/max 写法。证明小端 $m_{\max}\le\ell_m$ 与大端 $m_{\min}\ge u_m$，即得到所需证书。

固定参数可求解不等于全部参数的一致证明已经完成。对紧参数域，可使用经过证明的临界基分区、参数化对偶证书或解析界。细节与所需的固定矩阵/仿射参数条件见 mpc_response_certificate.md；一般变化目标和约束系数的情形不能自动简化为一次 LP。

### 4.2 经济成本间隙正是连接中的关键

高松弛时的余量下界通常必须来自最优性。若可行集随松弛嵌套，低松弛时的安全方案到高松弛时仍然可行，因此不可能只靠“高松弛可行集”迫使所有方案越界。

定义高松弛的安全受限问题

$$
J_{\rm bad}=\inf\{c^\top v:
v\in\mathcal P(\zeta,r),\ m(\zeta,r,v)\le u_m\}.
$$

若一个完整问题的可行方案成本为 $\overline J$，而受限问题有对偶下界 $\underline J_{\rm bad}$，且

$$
\boxed{\underline J_{\rm bad}>\overline J,}
\tag{4.2}
$$

则所有最优方案必定满足 $m>u_m$。

**证明。** 完整最优值不超过 $\overline J$；任何处在受限集合内的方案成本至少为 $\underline J_{\rm bad}>\overline J$，不可能最优。证毕。

若求解误差保证成本至多为 $J^*+\varepsilon_{\rm opt}$，将右侧加强为 $\overline J+\varepsilon_{\rm opt}$ 即可。低松弛时可将坏集合换成 $m\ge\ell_m$，获得对应的严格安全裕度证书。

这说明需要证明的是“经济上会选择使用松弛”，而非仅仅“允许使用松弛”。

### 4.3 大松弛的饱和化简

若所有独立物理约束定义的可行集为 $\mathcal B(\zeta)$，待放松行的余量在其中有统一有限上界 $r_{\rm sat}$，则 $r\ge r_{\rm sat}$ 时这些行全部冗余。高端证书因此可以在一个与 $r$ 无关的饱和问题上检查。

若该饱和问题仍然偏好安全方案，高端证书就会失败。这是正确识别“风险预算无法通过经济优化被用满”，不是证明方法的问题。

## 5. 一个从经济 MPC 出发、连接确实闭合的例子

考虑两步预测：

$$
\min_m -m,\qquad
-2\le m\le0.5,\quad m\le r,\quad m+2\le r,\quad r>0.
\tag{5.1}
$$

$m$ 是第一步名义余量，第二步有一个必须承受的已知增量 2；两步共享同一松弛量。线性目标代表增大余量具有经济收益。假设第一步误差是独立标准正态创新。

**命题。** 对所有 $r>0$，问题可行、最优解唯一，且

$$
m^*(r)=\min(0.5,r-2),\qquad p(r)=\Phi(m^*(r)).
$$

**证明。** 约束 $m+2\le r$ 比 $m\le r$ 更紧，且其上界 $r-2>-2$，所以可行区间非空。目标严格偏好最大 $m$，故最优解取可行区间上端。概率式来自标准正态误差。证毕。

取 $r_L=0.25,r_H=1.25$，则

$$
r\le r_L\Rightarrow m^*(r)\le-1.75,\qquad
r\ge r_H\Rightarrow m^*(r)\ge-0.75.
$$

因而

$$
p_L=\Phi(-1.75)\approx0.040059,\quad
p_H=\Phi(-0.75)\approx0.226627.
$$

在数学例子的参数 $w=2880,\alpha=0.1,\chi=10$ 下，
$p_\dagger\approx0.0998595023$，严格位于两者之间。定理 B 因此成立。

也可以不依赖根的数值近似验证跨越。标准正态 Mills 界给出
$p_L<\varphi(1.75)/1.75<0.05$。另一方面，
$p_H=1/2-\int_0^{0.75}\varphi(z)\,dz
\ge1/2-0.75/\sqrt{2\pi}>0.2>0.1>a_w>p_\dagger$，其中 $\varphi$ 为标准正态密度。
对于 $p<0.05$，令 $b=\alpha-1/(2w)-K/w$、$K\sim\mathrm{Bin}(2879,p)$。此时 $\mathbb E b>0.0498$，且 $|b|<0.9$。利用
$\log(1+b/10)\ge b/10-b^2/[200(1-0.09)]$，得到
$\Psi(p)>0.00498-0.81/182>0$，因此低端也严格小于根。

这是真实优化条件推出概率条件的构造例子，但不是用户数据中心参数与误差分布的实测结果。其机制是未来约束在小松弛时强制第一步保留负余量，而经济目标在大松弛时推动使用放宽空间。

## 6. 连接定理二：不再要求保留计数充分性的性能迁移

### 6.1 从余量偏差到概率偏差

固定参考区间 $0<\ell<u<1$，取离 $w\alpha$ 最近的整数 $k$，中点时固定一个有效选择。参考端点规则为 $q_s=u$（$s<k$）及 $q_s=\ell$（$s\ge k$）。Poisson 连接对这个参考链成立；迁移 $O(1/w)$ 阶时另要求固定的严格内部目标 $\ell<\alpha<u$。

参考端点策略记为 $q_s$。选 Lipschitz 参考 CDF $F_0$，常数为 $L_F$，并选参考余量 $\bar m_s$ 满足
$1-F_0(-\bar m_s)=q_s$。若

$$
\sup_z|F_t(z)-F_0(z)|\le\delta_{F,t},
\qquad |m_t-\bar m_{S_t}|\le\delta_{m,t},
$$

则

$$
\boxed{|p_{t+1}-q_{S_t}|\le\delta_{F,t}+L_F\delta_{m,t}.}
\tag{6.1}
$$

**证明。** 在 $1-F_t(-m_t)$ 中加减 $F_0(-m_t)$，使用 CDF 偏差界与 Lipschitz 界。实际 CDF 可以不连续。证毕。

余量管道可以通过最优面上下界验证：
$\bar m_s-\delta_m\le m_{\min}\le m_{\max}\le\bar m_s+\delta_m$。
这不由乘法更新的反馈符号自动成立；它是需要量化的 MPC 响应误差。

### 6.2 精确的 Poisson 连接

设 $X_t$ 为完整二值窗口，$T_bx$ 表示移出旧位、添加 $b$。参考链的核为

$$
(PH)(x)=(1-q_{S(x)})H(T_0x)+q_{S(x)}H(T_1x).
$$

设窗口误差函数 $g_w(x)=\left|\frac{|x|}{w}-\alpha\right|$，参考稳态均值 $m_w=\pi g_w$。参考链有限、不可约、非周期，因此

$$
H-PH=g_w-m_w
\tag{6.2}
$$

有解，例如绝对收敛级数 $H=\sum_{j\ge0}(P^jg_w-m_w)$。记

$$
B_w=\max H-\min H,\qquad
D_w=\max_x|H(T_1x)-H(T_0x)|.
$$

**定理 C。** 对任何完整历史依赖的实际控制器，令
$e_t=p_{t+1}-q_{S_t}$，都有

$$
\begin{aligned}
\frac1N\sum_{t=0}^{N-1}g_w(X_t)-m_w
={}&\frac{H(X_0)-H(X_N)}N\\
&+\frac1N\sum_{t=0}^{N-1}e_t
[H(T_1X_t)-H(T_0X_t)]\\
&+\frac1N\sum_{t=0}^{N-1}M_{t+1},
\end{aligned}
\tag{6.3}
$$

其中 $M_{t+1}=H(X_{t+1})-\mathbb E[H(X_{t+1})\mid\mathcal F_t]$ 是有界鞅差。因此

$$
\boxed{
\left|\frac1N\sum_{t=0}^{N-1}\mathbb E g_w(X_t)-m_w\right|
\le\frac{B_w}{N}
+D_w\frac1N\sum_{t=0}^{N-1}\mathbb E|e_t|.
}
\tag{6.4}
$$

**证明。** 完整历史下下一步只有两种可能字串，故

$$
\mathbb E[H(X_{t+1})\mid\mathcal F_t]
=(PH)(X_t)+e_t[H(T_1X_t)-H(T_0X_t)].
$$

代入式 (6.2)，加减 $H(X_{t+1})$ 并求和得到式 (6.3)。取期望，鞅差为零；分别使用 $B_w,D_w$ 控制首尾项与概率项，得式 (6.4)。证毕。

若真实窗口边缘分布平稳，首尾期望消去，联合式 (6.1) 得

$$
\boxed{
\mathbb E_{\rm MPC}|Y-\alpha|
\le m_w+D_w\,\mathbb E(\delta_{F,t}+L_F\delta_{m,t}).
}
\tag{6.5}
$$

不要求实际窗口过程是马尔可夫链，也不要求真实概率只依赖保留计数。非平稳时，鞅强大数律同样给出

$$
\limsup_N\frac1N\sum_{t=0}^{N-1}|Y_t-\alpha|
\le m_w+D_w\limsup_N\frac1N\sum_{t=0}^{N-1}|e_t|
\quad\text{几乎必然}.
\tag{6.6}
$$

这约束长期平均的窗口跟踪误差，不是每个窗口都满足同一逐路径界。

### 6.3 迁移 $O(1/w)$ 所需的定量条件

固定严格内部目标时，已知 $m_w=O(1/w)$。因此通过上述通用扰动界保留同一阶的充分条件为

$$
\boxed{D_w\,\mathbb E(\delta_{F,t}+L_F\delta_{m,t})=O(1/w),}
\tag{6.7}
$$

或其长期平均版本。有限时段还必须保留 $B_w/N$；高概率版本另有鞅偏差项。

尚未证明 $D_w$ 对所有 $w$ 一致有界。一般严格上界为

$$
D_w\le B_w\le\frac{w}{(1-u+\ell)^w},
$$

来自参考链的 $w$ 步共同概率成分，对大窗口很保守。若将来可证明 $D_w\le C$，则 $O(1/w)$ 概率响应误差就足以迁移原阶。

另一方面，全零、全一字串的 Poisson 方程给出

$$
D_w\ge\max\left\{
\frac{|\alpha-m_w|}{u},
\frac{|1-\alpha-m_w|}{1-\ell}
\right\}.
$$

所以不能指望该系数随窗口缩到零，以便让固定偏差自动得到 $1/w$ 阶。这是本通用绝对值上界的限制；某些有结构的固定偏差仍可能保留原阶，不能反向宣称它们必然破坏性能。

式 (6.5)–(6.7) 已将原来缺少的“历史依赖 MPC 到概率策略性能”写成精确的一般性连接。尚需验证的是实际余量管道、条件分布偏差，以及足够有用的 Poisson 灵敏度界。

## 7. 三个不能绕过的反例

**共享时域的松弛可能降低第一步风险。**

$$
\min_{x,y}3x+y,\quad
0\le x,y\le2,\quad x+y\ge2,\quad x,y\le1+r.
$$

对 $0\le r\le1$，唯一解为 $(x,y)=(1-r,1+r)$：正成本使总量取 2，较便宜的 $y$ 应取上界。成本 $4-2r$ 下降，但第一步违约
$x+\epsilon>1$、$\epsilon\sim N(0,\sigma^2)$ 的概率为
$1-\Phi(r/\sigma)$，严格下降。因此经济值函数单调不等于第一步概率单调。

**只放松、不收紧可能使目标不可达。**

$$
\min_u U-u,\quad0\le u\le U,\quad u\le r.
$$

唯一最优 $u=\min(r,U)$。若违约为 $u+\epsilon>0$、误差为中心对称正态，则
$p(r)=\Phi(\min(r,U)/\sigma)\ge1/2$。对 $\alpha=0.1$ 这样的目标，任何仅调整 $r>0$ 的算法都无法实现平均风险低于目标。这一系统具有凸性、唯一最优解和光滑正确方向的概率响应，仍然失败，因为最紧端的风险已过高。原公式在该例中会驱动 $r$ 指数塌缩而风险趋于 $1/2$，完整证明见独立审查文件。

**相同保留计数不决定实际松弛量。**

$w=2$ 时，历史 $00$ 与 $10$ 都留下最新位 0，但对数幅值分别为
$\theta_0+2f_0$ 与 $\theta_0+f_1+f_0$，二者不同。严格响应函数因而给出不同下一步概率。不能仅凭相同窗口统计量把实际更新认定为固定的 $q_{S_t}$。

## 8. 对论文的实际意义与剩余边界

最值得使用的是定理 A–B：证明完整经济 MPC 的最优解余量在两个端点区域满足可检查条件，再用条件误差包络推出原乘法更新的长期风险保证。高端的严格经济间隙证书解释了“为什么优化器会使用风险预算”，是此前缺失的重要环节。

定理 C 则回答更强的问题：即便真实 MPC 不满足保留计数模型，仍可用概率误差乘以 Poisson 灵敏度来迁移性能。它给出了严格桥梁，但不会自动让原乘法更新获得 $\Theta(1/w)$ 的稳态误差。

本轮尚未证明用户数据中心模型对全部可达状态、预测和价格满足证书，也没有证明大窗口下实用的 $D_w$ 上界、全系统稳定性、递归可行性或经济性能比界。新的任务已被转化为具体的优化证书与误差模型核验，不再只能笼统假设概率响应具有理想结构。

## 9. 文件与独立检查

- mpc_response_certificate.md：最优面、CDF、对偶证书、经济间隙、饱和化简和参数分区证明。
- perturbation_bridge.md：Poisson 连接的稳态、非平稳、路径平均、高概率和近似残差版本。
- connection_audit.md：独立证明审查与反例。
- verify_connection.py、connection_verification.json：构造例子与历史依赖扰动的可复现计算检查。

主 agent 独立计算了上述构造 MPC 的证书，并在 $w=2,\ldots,8$ 下加入影响下一步概率的持续模式，使实际过程不满足保留计数充分性。稳态性能差与 Poisson 恒等式右侧相符，最大数值残差约 $4.1\times10^{-16}$。

数值只补充证明，不是用户数据中心的实测或仿真结果。证明经过多个 agent 交叉核验；没有完成文献首创性核查或形式化证明器验证。
