# B：候选更新的初始化、边界、路径恒等式与严格证明

角色：Agent B。日期：2026-09-24。

**范围说明**
- 本文只做数学推导，没有修改任何已有文件，也没有运行 MPC。
- 数值核验只用 numpy、fractions 与纯 Python，脚本放在 `B_scripts/`，只作辅助；结论以证明为准。
- 所有玩具序列都不是数据中心实验证据。

**符号**：沿用 `00_common_setting.md`。每个受调约束单独一套，下文省略下标 i。
- 违约指示 $O_t=\mathbf 1\{Gs_t>g+\varepsilon\}$，$\varepsilon\ge0$ 事先声明。
- EV 更新：$v_t=r_{t-1}+\kappa(\alpha-O_t)$，$r_t=\Pi_{[\underline r,\overline r]}(v_t)$，投影残差 $c_t=r_t-v_t$，$c_t^+=\max(c_t,0)$（下侧截断），$c_t^-=\max(-c_t,0)$（上侧截断）。
- 区间 $I=(a,b]$，$L=b-a$，$C_I^\pm=\sum_{t\in I}c_t^\pm$，$\bar O_I=L^{-1}\sum_{t\in I}O_t$。
- $D=\overline r-\underline r>0$，$\beta=\max(\alpha,1-\alpha)$，$0<\alpha<1$，$\kappa>0$。
- 用户写法 $r_{t+1}=\Pi[r_t+\kappa(\alpha-O_{t+1})]$ 与上式只差下标平移。

**标注**
- 【严格】：对任意二值路径成立，不需要概率模型，也不需要 MPC 的任何性质。
- 【条件】：需要明确写出的额外联系，例如单侧阈值支配、精确阈值响应、概率代理。
- 【更正】：推翻或收窄已有说法。

---

## 1. 索引与时序

### 命题 1.1【严格；与稿件逐行对照】EV 与稿件 MPC 执行时序一致

**对照对象**：稿件 Algorithm 1（第 574–588 行）、违约定义式 (Oi_def)（第 414–420 行）、“一步观测延迟”说明（第 425 行）、MPC 约束（第 400–407、526–534 行）。

**结论**：把稿件第 587 行的乘法更新换成 EV 后，稿件循环
“用 $h_t$ 求解 → 施加第一步 → 观测 $s_{t+1}$ → 计算 $O_{t+1}$ → 更新 $h_{t+1}$”
与下列顺序逐步相同（$r=-h$）：

| 步 | 时刻 t 的动作 | 用到的量 | 产生的量 |
|---|---|---|---|
| 0 | 初始化（只做一次）：选 $r_0\in[\underline r,\overline r]$，令 $t=0$ | 设计量 | $r_0$ |
| 1 | 测得实际状态 $s_t$ 与当期外生量 $\xi_t$ | 测量 | $s_t,\xi_t$ |
| 2 | 若 $t\ge1$：计算 $O_t=\mathbf 1\{Gs_t>g+\varepsilon\}$（它是 $t-1$ 时刻决策的结果） | $s_t$ | $O_t$ |
| 3 | 若 $t\ge1$：计算 $v_t=r_{t-1}+\kappa(\alpha-O_t)$ | $r_{t-1},O_t$ | $v_t$ |
| 4 | 投影：$r_t=\Pi(v_t)$；记录 $c_t=r_t-v_t$ | $v_t$ | $r_t,c_t$ |
| 5 | 施加：用 $r_t$、$s_t$、$\xi_t$ 与 H 步预测求解 MPC，约束 $G\hat s_{t+\ell\mid t}\le g+r_t$，$\ell=1,\dots,H$；$\hat s_{t\mid t}=s_t$ 不受新界约束 | $r_t$ 等 | 第一步指令 |
| 6 | 执行第一步；供电侧“可再生优先”修正只改 $p^r,p^{gd}$ | 指令 | — |
| 7 | 对象演化到 $s_{t+1}$；$t\leftarrow t+1$，回到步 1 | — | $s_{t+1}$ |

**证明**：
1. 稿件第 576 行在迭代 t 中用 $h_t$ 求解，对应步 5 用 $r_t$。
2. 稿件第 587 行在迭代 t 末尾由 $s_{t+1}$ 计算 $O_{t+1}$，再算 $h_{t+1}$。本表把这两件事放到迭代 $t+1$ 的开头（步 2–4）。两者之间没有任何别的计算，所以计算序列完全相同，只是循环切分点不同。
3. 因此 $r_t$ 只影响 $O_{t+1}$ 及以后的观测，而 $O_{t+1}$ 进入 $r_{t+1}$。反馈回路的固有延迟恰为一步，与稿件第 425 行一致。下文第 8 节的“额外延迟 d”是在此之外的延迟。证毕。

**信息集**：令 $\mathcal F_t=\sigma(r_0,s_0,\xi_0,\dots,s_t,\xi_t)$ 表示决策时刻 t 的信息。于是 $O_t$、$r_t$ 都是 $\mathcal F_t$ 可测的，$O_{t+1}$ 是 $\mathcal F_{t+1}$ 可测的。

**对稿件的两处更正**
- 【更正】稿件第 444 行写 $\mathcal F_t=\{\xi_1,\dots,\xi_{t-1}\}$，与 Algorithm 1 第 571 行“在时刻 t 已测得 $\xi_t$”不一致。决策信息应包含 $s_t$ 与 $\xi_t$。
- 【更正】稿件第 399、536 行的 $h\le0$（即 $r\ge0$）禁止收紧。若采用 $\underline r<0$，这两处需同步修改；这属于写作层面的后续改动，本文不改 TEX。

**适用对象**：EV、W0、G 共用这一时序，区别只在步 3–4 的公式。

**失效情形**：
- 若求解失败后启用 fallback、人工重置 r、冻结更新或补录缺测 O，实际存储的 r 就不再满足步 3–4。此时下文所有恒等式都必须把这些改动作为额外项记录，不能归入投影残差 $c_t$。
- 若事后修正已记录的 $O_t$，则 $r$ 的历史不会自动改变，恒等式对修正后的序列不成立。

**是否需要实际 MPC 满足额外联系**：不需要。时序一致性只涉及算法的计算顺序。

### 命题 1.2【条件】单侧阈值支配：储能通道由构造满足，温度通道需要失配界

下面的关系是第 3、7、8、9 节多个结论所需的“额外联系”，在此单独写清。

**定义（单侧阈值支配 $\mathrm D_\theta$）**：存在常数 θ，使得对所研究时段内所有 t 都有
$$
O_{t+1}\le\mathbf 1\{r_t>\theta\}.
$$
即：参数不超过 θ 时，下一步一定不违约。参数超过 θ 时，是否违约不作任何限制，可以依赖历史、电价、预测误差，甚至可以是对抗性的。它比 merged 报告定理 4 的“精确阈值 $O_{t+1}=\mathbf 1\{r_t>\theta\}$”弱得多。

**命题（储能）**：设
- (i) 时刻 t 的 MPC 以硬约束方式求解成功，返回的 $\ell=1$ 预测储能状态满足 $G\hat s_{t+1\mid t}\le g+r_t+\eta$，其中 $\eta\ge0$ 为求解器可行性容差；
- (ii) 储能指令 $(p^{ch}_t,p^{dis}_t)$ 按原样执行。稿件第 562 行的修正只改 $p^r,p^{gd}$，满足这一条；
- (iii) 储能递推（稿件式 (Ebs)，第 271–294 行）对实际对象精确成立。

则 $s^E_{t+1}=\hat s^E_{t+1\mid t}$，从而
$$
O_{t+1}\le\mathbf 1\{r_t>\varepsilon-\eta\},\qquad (Gs_{t+1}-g)_+\le(r_t+\eta)_+ .
$$
即储能通道满足 $\mathrm D_\theta$，$\theta=\varepsilon-\eta$，且违约幅度不超过 $r_t+\eta$。

**证明**：由 (ii)(iii)，实际下一状态等于计划的第一步状态。若 $O_{t+1}=1$，则 $g+\varepsilon<Gs_{t+1}\le g+r_t+\eta$，得 $r_t>\varepsilon-\eta$。幅度不等式同理。证毕。

**温度**：设实际出风温度等于控制模型的一步预测加失配，$Gs_{t+1}=G\hat s_{t+1\mid t}+e_{t+1}$，且失配有上界 $e_{t+1}\le\bar e$。同理得 $\mathrm D_\theta$，其中 $\theta=\varepsilon-\eta-\bar e$，幅度 $\le r_t+\eta+\bar e$。若失配没有可信上界，温度通道**不满足**任何 $\mathrm D_\theta$。

**失效情形**：
- MPC 不可行，或采用名义约束软化、fallback；
- 求解后改动了储能指令；
- 电池模型失配（效率、荷电状态估计误差）或储能量测噪声；
- 求解器实际越界量超过声明的 η。

以上任一情形出现时，$\mathrm D_\theta$ 需要按实际日志重新核实，否则只能使用不依赖 $\mathrm D_\theta$ 的【严格】结论。

**工程意义**：
- 储能通道的“阈值”由 MPC 的第一步硬约束直接给出，不需要假设经济 MPC 的响应形状。这是比“精确阈值”更可信的结构事实。
- 它只给出上侧（违约不超过预算）方向的结论。风险预算能否用满，取决于 MPC 是否真的利用放松，$\mathrm D_\theta$ 对此不作任何断言。

---

## 2. α 的含义：校准目标还是容许上限

### 2.1 结论先行

- EV 按构造是**校准目标**跟踪器：由第 3 节恒等式，只要两侧都不截断，任意长区间的违约率都被拉向 α；违约率低于 α 时它会主动放松。
- 若用户要的是**容许上限**（评价窗口内违约率不超过 α），EV 以 α 为目标时只能给出“α 加残差”的界。要得到上限语义，只需把目标改为 $p^\*=\alpha-m$（裕度 $m>0$）。命题 2.3 证明这与非对称增益完全等价，不需要新机制。
- 两种语义都要由用户决定；本文只给出各自可证明的内容。

### 命题 2.1【条件：同一时刻的 MPC 数据固定】双向更新放松的经济动机，以及它不是什么

**命题**：固定时刻 t 的测量、预测和其余约束，把 MPC 最优值记为 $J_t^\*(r)$（不可行时记为 $+\infty$）。若各通道满足 $r\le r'$（逐分量），则 $J_t^\*(r')\le J_t^\*(r)$。

**证明**：约束 $G\hat s_{t+\ell\mid t}\le g+r$ 的可行集随 r 逐分量增大而扩大，在更大的集合上取最小值不会更大。证毕。

**这说明什么**：放松边界在**单个时刻**不会使计划成本变差，这就是低风险时放松的经济动机。

**这不说明什么**：
1. **闭环成本不一定单调。** 放松改变了今天的状态（例如储能放得更深），会影响以后的可行集和成本。稿件没有、本文也没有闭环成本随 r 单调的证明。
2. **它不是安全保证。** 放松只会让违约更可能发生。在 $\mathrm D_\theta$ 下，违约幅度 $\le r_t+\eta$（命题 1.2），放松越多，可能的幅度越大。安全只能由独立保留的物理硬界和 $\overline r$ 的选取来保证（第 7 节）。

### 命题 2.2【严格】“低于 α”的分解与上侧饱和指标

**命题**：对任意区间 I，
$$
\alpha-\bar O_I=\frac{r_b-r_a}{\kappa L}+\frac{C_I^-}{\kappa L}-\frac{C_I^+}{\kappa L}.
$$
定义**上侧饱和未用预算比例** $U_I:=C_I^-/(\kappa\alpha L)$，则：
- (i) $0\le U_I\le1$；
- (ii) $U_I=1$ 当且仅当对所有 $t\in I$ 都有 $O_t=0$ 且 $r_{t-1}=\overline r$。

**证明**：
1. 第一式就是第 3 节定理 3.4 的恒等式移项。
2. 若 $O_t=1$，则 $v_t<r_{t-1}\le\overline r$，不会上侧截断，$c_t^-=0$。
3. 若 $O_t=0$，则 $v_t=r_{t-1}+\kappa\alpha$，$c_t^-=(v_t-\overline r)_+\le\kappa\alpha$，等号当且仅当 $r_{t-1}=\overline r$。
4. 求和得 $C_I^-\le\kappa\alpha L$，等号条件即 (ii)。证毕。

**如何识别“低于 α 且成本不恶化”不是失败**：
- 若违约率不足 $\alpha-\bar O_I$ 主要由 $C_I^-/(\kappa L)$ 解释（$U_I$ 接近 $(\alpha-\bar O_I)/\alpha$），且参数大部分时间停在 $\overline r$，说明**已允许的最大放松都没有被用成违约**。原因只有两种：约束当时不紧（放松在经济上不被利用），或 $\overline r$ 本身设得小。两者都不是算法失败。
- 若不足来自 $r_b-r_a>0$（参数仍在上升），则是暂态，不是饱和。
- 报告时应同时给出：停在 $\overline r$ 的时间比例、$U_I$、以及与名义界基线（$r\equiv0$）的成本差。成本不劣于基线时，“低于 α”可以如实写成“风险预算未用满”。
- 需要提醒的副作用：长期停在 $\overline r$ 之后，一旦约束重新变紧，会出现首段连续违约（第 9 节），其长度由 $\overline r$ 与 κ 决定。

### 命题 2.3【严格】非对称增益恰好等价于“改目标 + 改增益”的对称 EV

**命题**：设更新为
$$
r_{t}=\Pi\big[r_{t-1}+\kappa_u\alpha\,(1-O_{t})-\kappa_d(1-\alpha)\,O_{t}\big],\qquad \kappa_u,\kappa_d>0,
$$
即不违约时放松 $\kappa_u\alpha$，违约时收紧 $\kappa_d(1-\alpha)$。令
$$
K=\kappa_u\alpha+\kappa_d(1-\alpha),\qquad p^\*=\frac{\kappa_u\alpha}{K}.
$$
则对任意观测序列、任意初值、任意投影区间，它与目标为 $p^\*$、增益为 K 的对称 EV $r_t=\Pi[r_{t-1}+K(p^\*-O_t)]$ 产生**完全相同**的参数路径。反过来，任给 $p^\*\in(0,1)$、$K>0$，取 $\kappa_u=Kp^\*/\alpha$、$\kappa_d=K(1-p^\*)/(1-\alpha)$ 即可实现。

**证明**：投影前的增量是 O 的仿射函数。$O=0$ 时 $K(p^\*-0)=\kappa_u\alpha$；$O=1$ 时 $K(p^\*-1)=\kappa_u\alpha-K=-\kappa_d(1-\alpha)$。两者在 $O\in\{0,1\}$ 上逐点相等，投影相同，归纳即得。反向代入验证 $\kappa_u\alpha+\kappa_d(1-\alpha)=K$、$\kappa_u\alpha/K=p^\*$。证毕。

核验：`b_path_checks.py` 第 B 项，300 条随机路径逐步精确相等。

**推论 2.4【严格；带 $\mathrm D_\theta$ 的部分为条件】用“目标加裕度”表达容许上限**

取 $p^\*=\alpha-m$，$0<m<\alpha$，增益 K。由第 3 节定理 3.4（把 α 换成 $p^\*$、κ 换成 K）：

$$
\bar O_I\le\alpha-m+\frac{r_a-\underline r+C_I^+}{KL}.
$$

因此：
- (a)【严格】只要 $L\ge(r_a-\underline r+C_I^+)/(Km)$，就有 $\bar O_I\le\alpha$。这个长度依赖路径上的 $C_I^+$。
- (b)【条件：$\mathrm D_\theta$ 且 $\underline r\le\theta-K(1-p^\*)$】由第 3 节定理 3.7，$C^+\equiv0$。记
$$
N_{\rm ex}:=\frac{\overline r-\theta}{K}+(1-p^\*),\qquad L_m:=\frac{N_{\rm ex}}{m},
$$
则**每一个**长度 $L\ge L_m$ 的区间都满足 $\bar O_I\le\alpha$。这是确定性的、不含残差的上限证书。

**工程意义**：
- 裕度与证书长度的乘积固定：$m\cdot L_m=N_{\rm ex}$。裕度越小，需要的评价长度越长。
- 代价是：不截断时长期违约率为 $\alpha-m$，即有比例 m 的预算不用。
- 用非对称增益写出，就是“放松慢、收紧快”：$\kappa_u/\kappa_d=\dfrac{p^\*(1-\alpha)}{(1-p^\*)\alpha}<1$。这正是命题 2.3 的内容，不需要引入抗饱和、滤波等新机制。

**失效情形**：(b) 依赖 $\mathrm D_\theta$。温度通道若没有失配上界，只能用 (a)，而 (a) 的长度依赖实测的 $C^+$，不能事先保证。

**是否需要 MPC 满足额外联系**：命题 2.2、2.3 与推论 2.4(a) 不需要；推论 2.4(b) 需要 $\mathrm D_\theta$。

---

## 3. EV 不依赖概率模型的性质

本节 3.1–3.6 都是【严格】结论：对任意二值序列成立，不需要独立性、平稳性、马尔可夫性、MPC 最优性或可行性。3.7 需要 $\mathrm D_\theta$。

### 命题 3.1【严格】有界与单步变化界

对任意 $r_0\in[\underline r,\overline r]$ 与任意观测，
$$
\underline r\le r_t\le\overline r,\qquad |r_t-r_{t-1}|\le\kappa|\alpha-O_t|\le\kappa\beta .
$$
更细地，$O_t=0$ 时 $0\le r_t-r_{t-1}\le\kappa\alpha$；$O_t=1$ 时 $0\le r_{t-1}-r_t\le\kappa(1-\alpha)$。

**证明**：投影的像在区间内；投影非扩张且 $\Pi(r_{t-1})=r_{t-1}$，故 $|\Pi(v_t)-\Pi(r_{t-1})|\le|v_t-r_{t-1}|$。投影单调，保持增量符号。证毕。

**工程意义**：收紧方向的单步量不超过 $\kappa(1-\alpha)$，放松方向不超过 $\kappa\alpha$。第 7 节的可行性条件只需要约束收紧方向。

### 命题 3.2【严格】零初值不吸收；对照 G 的吸收与符号锁定

- **EV**：$r=0$ 不是特殊点。若 $r_0=0$、$\overline r>0$ 且 $O_1=0$，则 $r_1=\min(\overline r,\kappa\alpha)>0$；若 $\underline r<0$ 且 $O_1=1$，则 $r_1=\max(\underline r,-\kappa(1-\alpha))<0$。
- **G**：$h_t=h_{t-1}(1+b_t/\gamma)$，$\gamma>\sup|b_t|$。于是 $h_t=h_0\prod_{s\le t}(1+b_s/\gamma)$，每个因子为正，所以
  - $h_0=0\Rightarrow h_t\equiv0$，名义界是吸收点，无法从名义界启动；
  - $h_0<0\Rightarrow h_t<0$ 对所有 t 成立，在有限时间内既不能回到名义界，也不能收紧到名义界以内。

**证明**：直接由递推式得到。证毕。

### 命题 3.3【严格】边界退出条件与所需步数

1. **退出**：若 $r_{t-1}=\overline r$ 且 $O_t=1$，则 $r_t=\overline r-\min(\kappa(1-\alpha),D)<\overline r$。若 $r_{t-1}=\underline r$ 且 $O_t=0$，则 $r_t=\underline r+\min(\kappa\alpha,D)>\underline r$。即**一次相反观测就离开端点**。
2. **到达端点的最少步数**：从 r 出发，到达 $\overline r$ 至少需要 $\lceil(\overline r-r)/(\kappa\alpha)\rceil$ 步，连续不违约时恰好取到。到达 $\underline r$ 至少需要 $\lceil(r-\underline r)/(\kappa(1-\alpha))\rceil$ 步，连续违约时恰好取到。

**证明**：第 1 条是命题 3.1 的单步量代入。第 2 条：每步上升至多 κα，下降至多 κ(1−α)；连续同向观测每步都取到最大量，最后一步被裁剪到端点。证毕。

**对照**：
- W0 停在下界时，窗口计数须降到 $\lceil w\alpha\rceil-1$ 才会离开。即使此后全不违约，也可能要等 $w-\lceil w\alpha\rceil+1$ 步；稿件参数下为 2593 步，约 27 天（P1 定理 3.2）。
- G 按命题 3.2 永远到不了名义界。

### 定理 3.4【严格】任意区间的校准恒等式与单侧界（独立复核 merged 定理 1）

对任意二值路径与所有 $0\le a<b$ 同时成立：
$$
\boxed{\ \bar O_I-\alpha=\frac{r_a-r_b+C_I^+-C_I^-}{\kappa L}\ }
$$
$$
\bar O_I\le\alpha+\frac{r_a-\underline r+C_I^+}{\kappa L}\le\alpha+\frac{D}{\kappa L}+\frac{(1-\alpha)N_I^{lo}}{L},
$$
$$
\bar O_I\ge\alpha-\frac{\overline r-r_a+C_I^-}{\kappa L}\ge\alpha-\frac{D}{\kappa L}-\frac{\alpha N_I^{hi}}{L},
$$
其中 $N_I^{lo}$、$N_I^{hi}$ 为区间内严格下侧、上侧截断的次数。

**证明**：
1. 由定义，$r_t-r_{t-1}=\kappa(\alpha-O_t)+c_t$。对 $t=a+1,\dots,b$ 求和，左边望远镜消去为 $r_b-r_a$，整理即得恒等式。
2. 下侧截断只在 $O_t=1$ 且 $v_t<\underline r$ 时发生，此时 $r_{t-1}\ge\underline r$，故 $0<c_t=\underline r-v_t\le\kappa(1-\alpha)$。上侧截断同理，$0<c_t^-\le\kappa\alpha$。
3. 上界：恒等式中用 $r_b\ge\underline r$，并丢掉非正的 $-C_I^-$。下界：用 $r_b\le\overline r$，并丢掉非负的 $C_I^+$。再代入第 2 步的计数界。证毕。

**复核结论**：merged 定理 1 的陈述与证明逐步无误。`b_path_checks.py` 第 A 项：300 条随机路径、90,294 个区间，恒等式与两侧界全部精确成立。

### 推论 3.5【严格】滚动窗口的残差界

对 $t\ge w$，取 $a=t-w$、$b=t$：
$$
\alpha-\frac{\overline r-r_{t-w}+C^-_{(t-w,t]}}{\kappa w}\ \le\ Y^w_t\ \le\ \alpha+\frac{r_{t-w}-\underline r+C^+_{(t-w,t]}}{\kappa w}.
$$
对 $t<w$ 的部分窗口，取 $a=0$、$L=t$，得同形式的界，并应标注“部分窗口”，不填充虚构历史。

这个界对**每一个**盒形窗口成立，包括事后挑出的最坏窗口，不需要任何并集修正。

### 注 3.6【严格】持续违约时，残差不能从结论中删去

- 例：$O_t\equiv1$。参数到达 $\underline r$ 后，每步 $c_t^+=\kappa(1-\alpha)$，$Y^w=1$。此时上界右端为 $\alpha+(r_{t-w}-\underline r)/(\kappa w)+(1-\alpha)\cdot(\text{截断步数})/w$，同样趋于 1。
- 界仍然正确，但已失去约束力。$C^+/\kappa$ 的含义是：**参数已处在允许的最紧位置之后，仍然发生的超预算违约次数**。
- 因此推论 3.5 是“带可计算残差的运行证书”，不是无条件的风险保证。论文中必须同时报告 $C^+$，不能只写 $\alpha+D/(\kappa w)$。
- 反过来，$C^+$ 很大也不能单独证明“目标不可达”：增益过大、暂态、通道耦合都可能产生它（merged §4、R1）。

### 定理 3.7【条件：$\mathrm D_\theta$】储能通道不含残差的上侧证书

**假设**：
- (i) 所研究时段内 $O_{t+1}\le\mathbf 1\{r_t>\theta\}$（命题 1.2；储能通道 $\theta=\varepsilon-\eta$）；
- (ii) $\underline r\le m^\*:=\theta-\kappa(1-\alpha)<\overline r$；
- (iii) $r_0\in[\underline r,\overline r]$。

对参数超过 θ 时是否违约，不作任何假设（可依赖历史，可对抗）。

**结论**：
1. 从不发生下侧截断：$C^+\equiv0$。
2. 下包络：对所有 $a\le b$，$r_b\ge\min(r_a,m^\*)$。特别地，若 $r_0\ge m^\*$，参数永远不低于 $m^\*$；比 $m^\*$ 更低的下界永远不会被触及。
3. 任意区间的超预算违约次数：
$$
\sum_{t\in I}(O_t-\alpha)\le\frac{(r_a-m^\*)_+}{\kappa}\le\frac{\overline r-\theta}{\kappa}+(1-\alpha).
$$
4. 每个盒形窗口：
$$
Y^w_t\le\alpha+\frac{\overline r-\theta}{\kappa w}+\frac{1-\alpha}{w}.
$$
5. 最长连续违约不超过 $\big\lceil(\overline r-\theta)/(\kappa(1-\alpha))\big\rceil$。
6. 若 $\mathrm D_\theta$ 来自命题 1.2，违约幅度不超过 $\overline r+\eta$（温度通道为 $\overline r+\eta+\bar e$）。

**证明**：
1. 下侧截断要求 $O_t=1$ 且 $v_t<\underline r$。由 (i)，$O_t=1$ 意味着 $r_{t-1}>\theta$，于是 $v_t=r_{t-1}-\kappa(1-\alpha)>m^\*\ge\underline r$，矛盾。
2. 若 $O_t=1$：由上一步，$r_t=v_t>m^\*$。若 $O_t=0$：$r_t=\min(\overline r,r_{t-1}+\kappa\alpha)\ge r_{t-1}$。两种情形都有 $r_t\ge\min(r_{t-1},m^\*)$，归纳即得。
3. 由定理 3.4 与 $C^+=0$，$\sum_I(O-\alpha)=(r_a-r_b-C_I^-)/\kappa\le(r_a-r_b)/\kappa$。再由第 2 条，$r_b\ge\min(r_a,m^\*)$，故 $r_a-r_b\le(r_a-m^\*)_+\le\overline r-m^\*$。
4. 第 3 条取 $L=w$。
5. 设 $O_{t+1}=\dots=O_{t+k}=1$。由 (i)，$r_t,\dots,r_{t+k-1}>\theta$；这些步不截断，$r_{t+j}=r_t-j\kappa(1-\alpha)$。由 $r_{t+k-1}>\theta$ 得 $k-1<(r_t-\theta)/(\kappa(1-\alpha))\le X:=(\overline r-\theta)/(\kappa(1-\alpha))$，故 $k\le\lceil X\rceil$。
6. 由命题 1.2 与 $r_t\le\overline r$。证毕。

**紧性**：精确阈值响应且恢复时 $r=\overline r$，首段连续违约恰为 $\lceil X\rceil$（`b_path_checks.py` 第 C2 项，300 例全部相等）。第 3 条在该首段末尾取到 $(\overline r-r_b)/\kappa$，与上界相差不到一个网格。

**核验**：第 C 项，400 条路径（随机、“凡允许就违约”的对抗、块状三类响应），309,720 个区间，结论 1–5 全部成立；最长连续违约与上界之比的最大值为 1。

**适用对象**：满足 $\mathrm D_\theta$ 的通道。储能通道在命题 1.2 的条件 (i)–(iii) 下由构造满足；温度通道只在失配有上界时满足。

**失效情形**：
- $\mathrm D_\theta$ 不成立：MPC 不可行或软化、fallback、储能模型失配、温度失配无界。此时退回定理 3.4，残差 $C^+$ 必须保留。
- $\underline r>m^\*$：可能出现下侧截断。例如 $\underline r=\theta$ 的精确阈值情形会出现 merged §7.1 的两点周期。此时结论 1 失效，但定理 3.4 仍然成立。

**工程意义**：
- 在 $\mathrm D_\theta$ 下，上侧证书只依赖**放松权限** $\overline r-\theta$ 与 κ，与 $\underline r$ 无关，只要 $\underline r\le m^\*$。
- 由第 2 条，储能下界比 $m^\*$ 更负不会带来任何代价，也不会被触及。“储能下界需要多负”的问题因此简化为：不高于 $\theta-\kappa(1-\alpha)$ 即可（第 7、8 节讨论其必要性与延迟修正）。
- $(\overline r-\theta)/\kappa$ 可以直接读作“任意区间内超出预算的违约次数上限”（再加不到一次）。

### 命题 3.8【严格】与 W0 的比较：窗口率时间平均恒等式没有首尾项，但不等于逐窗口的更强控制

**W0 的恒等式**（P1 定理 1.1）：满窗阶段，
$$
\frac1L\sum_{t\in I}(Y^w_t-\alpha)=\frac{r_a-r_b+\sum_{t\in I}q_t}{\kappa L}.
$$
- 它没有首尾项。原因是被校准的量换成了控制器本身积分的“窗口率的时间平均”。
- $L=w$ 时，这个量是过去 $2w-1$ 个观测的三角核加权率，峰值在约 $w-1$ 步之前（P1 命题 1.2），不是论文指标中的单个盒形窗口率。

**反例（逐窗口控制并不更强）**：取 $w=10$，$\alpha=1/10$，$\kappa=1$，$[\underline r,\overline r]=[0,9/2]$。之前的窗口全为 0，$r=\overline r$，随后连续 10 次违约。
- W0：增量依次为 $\kappa(\alpha-k/w)=0,-0.1,\dots,-0.9$，合计 $-4.5$。参数恰好从 4.5 降到 0，**全程没有截断**（$q\equiv0$）。
- 该窗口的违约率为 $Y^w=1$，而 EV 型的界 $\alpha+D/(\kappa w)=11/20$。
- EV 在相同数据、相同 κ 与 D 下，要出现这个全违约窗口，必须有 $C^+=9/2$ 的下侧截断，这会在证书中如实显示（`b_path_checks.py` 第 E 项）。

**一般结论**：之前窗口为空、从 $\overline r$ 出发时，W0 让一个全违约窗口不发生下侧截断只需 $D\ge\kappa\big(\tfrac{w+1}2-w\alpha\big)$；EV 需要 $D\ge\kappa w(1-\alpha)$，约为前者的 2 倍。所以同样的 κ 与 D 下，W0 对单个盒形窗口的最坏控制更弱，而不是更强。D2 另证：凡只依赖当前窗口率、在 α 附近 Lipschitz 的更新，逐窗口界最好也只是平方根阶（本文未独立复核 D2 的一般证明，只给出上面的具体反例）。

**适用对象与意义**：论文指标 M1 是单个盒形窗口率。对这个量，EV 有逐窗口、只含 $C^+$ 的证书（推论 3.5），储能通道在 $\mathrm D_\theta$ 下还不含残差（定理 3.7）。W0 的无首尾项恒等式针对的是另一个量。

---

## 4. 响应速度的时间不变性（对应用户的原始动机）

### 定理 4.1【严格】EV 的区间响应与起始时刻无关

**结论**：对每个 $L\ge1$，存在只依赖 $(L,\kappa,\alpha,\underline r,\overline r)$ 的映射 $\Phi_L$，使对**所有** a 都有
$$
r_{a+L}=\Phi_L\big(r_a;\,O_{a+1},\dots,O_{a+L}\big).
$$
区间内没有截断时，$r_{a+L}-r_a=\kappa\sum_{t\in I}(\alpha-O_t)$。

**证明**：单步映射 $r\mapsto\Pi[r+\kappa(\alpha-o)]$ 不含 t，L 次复合即得 $\Phi_L$。无截断时逐步相加。证毕。

**推论**：若 $t_0$ 之后连续违约，参数每步下降 $\kappa(1-\alpha)$；下降 Δ 需要 $\lceil\Delta/(\kappa(1-\alpha))\rceil$ 步（不截断时），与 $t_0$ 无关。一般序列下，下降量等于 $\kappa$ 乘以区间内的超预算违约数，同样与 $t_0$ 无关。

### 定理 4.2【严格；只需 $Y_{t_0}=\alpha$】G 的参数响应至少按 $\sqrt{t_0}$ 变慢

**对象**：$h_t=h_{t-1}(1+b_t/\gamma)$，$b_t=\alpha-Y_t+\dfrac{2Y_t-1}{2(t+1)}$，$Y_t=\frac1t\sum_{j\le t}O_j$，$h<0$。

**假设**：
- (i) $Y_{t_0}=\alpha$，即 $t_0$ 前的累计违约数为 $\alpha t_0$；
- (ii) $\gamma>\bar b:=\beta+\dfrac1{2(t_0+2)}$。

$t_0$ 之后的观测序列**任意**，可以依赖历史，可以对抗。

**结论**：对任意 $\tau\ge1$，
$$
\Big|\log\frac{h_{t_0+\tau}}{h_{t_0}}\Big|\le\frac{1}{\gamma-\bar b}\Big[\beta\,\frac{\tau(\tau+1)}{2t_0}+\frac12\log\frac{t_0+\tau+1}{t_0+1}\Big]\le\frac{(\tau+1)^2}{2t_0(\gamma-\bar b)}.
$$
因此，要使参数的对数变化达到 $\lambda>0$（例如 $\lambda=\log2$，即放松量减半），至少需要
$$
\boxed{\ \tau\ \ge\ \sqrt{2\lambda(\gamma-\bar b)\,t_0}\;-\;1\ }
$$
步。这个下界随 $\sqrt{t_0}$ 无界增长。

**证明**：
1. 记 $j=s-t_0$，$N_j$ 为 $t_0$ 之后前 j 步的违约数，$0\le N_j\le j$。由 (i)，$Y_s=(\alpha t_0+N_j)/(t_0+j)$，所以
$$
|\alpha-Y_s|=\frac{|\alpha j-N_j|}{t_0+j}\le\frac{\beta j}{t_0+j}\le\frac{\beta j}{t_0}.
$$
2. $|2Y_s-1|\le1$，故 $|b_s|\le\beta j/t_0+\frac1{2(s+1)}$；同时 $|b_s|\le\bar b$。
3. 对 $|x|<1$，$|\log(1+x)|\le|x|/(1-|x|)$。取 $x=b_s/\gamma$、$|x|\le\bar b/\gamma<1$，得 $|\log(1+b_s/\gamma)|\le|b_s|/(\gamma-\bar b)$。
4. 求和：$\sum_{j=1}^{\tau}j/t_0=\tau(\tau+1)/(2t_0)$；$\sum_{s=t_0+1}^{t_0+\tau}\frac1{s+1}\le\log\frac{t_0+\tau+1}{t_0+1}\le\frac{\tau}{t_0}$。
5. 由 $\beta\le1$，$\beta\tau(\tau+1)+\tau\le(\tau+1)^2$，得第二个不等式。令其 $\ge\lambda$ 即得步数下界。证毕。

**细化**：若 $t_0$ 之后违约频率为 $p'$，在 $|N_j-p'j|\le B$ 的意义下成立，则第 1 步可改为 $|\alpha-Y_s|\le(|p'-\alpha|j+B)/t_0$，下界主项变为 $\sqrt{2\lambda(\gamma-\bar b)t_0/|p'-\alpha|}$。p′ 越接近 α，放慢越严重。

### 定理 4.3【严格】上述 $\sqrt{t_0}$ 的阶是紧的

**假设**：定理 4.2 的 (i)(ii)，且 $t_0$ 之后全部违约（$p'=1$）。

**结论**：对 $\tau\le t_0$，
$$
\Big|\log\frac{h_{t_0+\tau}}{h_{t_0}}\Big|\ge\frac{\tau}{4\gamma t_0}\big[(1-\alpha)(\tau+1)-2\big].
$$
所以当 $t_0$ 足够大、使所需步数落在 $\tau\le t_0$ 内（约 $t_0\ge4\gamma\lambda/(1-\alpha)+O(1)$）时，对数变化达到 λ 所需步数为 $\Theta(\sqrt{t_0})$，渐近约为 $\sqrt{2\gamma\lambda t_0/(1-\alpha)}$。

**证明**：$\log(1+x)\le x$，故 $\Delta\log h\le\gamma^{-1}\sum b_s$。此时 $\alpha-Y_s=-(1-\alpha)j/(t_0+j)$，且 $(2Y_s-1)/(2(s+1))\le1/(2(s+1))$。又 $\sum_{j\le\tau}\frac{j}{t_0+j}\ge\frac{\tau(\tau+1)}{2(t_0+\tau)}\ge\frac{\tau(\tau+1)}{4t_0}$，$\frac12\sum\frac1{s+1}\le\frac{\tau}{2t_0}$。合并后，括号为正时 $\Delta\log h\le-(\text{括号})/\gamma<0$。证毕。

### 命题 4.4【严格】G 的反馈信号本身按 $t_0$ 线性变慢

在定理 4.2 的 (i) 下，若 $t_0$ 之后 $N_j\le p'j+B$，则 $Y_{t_0+\tau}-\alpha=(N_\tau-\alpha\tau)/(t_0+\tau)\le((p'-\alpha)\tau+B)/(t_0+\tau)$。累计率超过 $\alpha+\delta$（$0<\delta<p'-\alpha$）至少需要
$$
\tau\ge\frac{\delta t_0-B}{p'-\alpha-\delta}.
$$
**证明**：直接整理不等式。证毕。

**两种放慢的关系**：信号 $Y$ 的偏离按 $t_0$ 线性放慢；参数的对数变化是信号偏离的累加，所以按 $\sqrt{t_0}$ 放慢。另外，G 是乘法更新，参数的绝对变化还要再乘以 $|h_{t_0}|$，放松量已经很小时会更慢。

### 定理 4.5【严格】W0 的响应不随 $t_0$ 变慢，但有约 $(w-1)/2$ 的等效延迟

**结论**：
1. 满窗阶段，W0 的映射 $(r_a,\ \text{a 时刻窗口内容},\ O_{a+1..b})\mapsto r_b$ 与 a 无关。
2. 无截断且 $\tau\ge w-1$ 时（κ 为 W0 的满窗增益），
$$
r_{t_0+\tau}-r_{t_0}=-\kappa\Big[\sum_{j=1}^{\tau}\omega_j\,(O_{t_0+j}-\alpha)+P_{t_0}\Big],\qquad
\omega_j=\frac{\min(\tau-j+1,w)}{w},
$$
其中 $P_{t_0}=\frac1w\sum_{i=0}^{w-2}(w-1-i)(O_{t_0-i}-\alpha)$ 是切换前窗口留下的记忆项。
3. 切换后全部违约时，上式为 $-\kappa[(1-\alpha)(\tau-\tfrac{w-1}2)+P_{t_0}]$；EV 为 $-\kappa(1-\alpha)\tau$。所以 W0 相当于 EV 延迟了
$$
\frac{w-1}{2}+\frac{P_{t_0}}{1-\alpha}\quad\text{步},\qquad \frac{P_{t_0}}{1-\alpha}\in\Big[-\frac{\alpha(w-1)}{2(1-\alpha)},\ \frac{w-1}{2}\Big].
$$
4. $\tau\le w$ 时，切换后部分的贡献为 $(1-\alpha)\tau(\tau+1)/(2w)$，起步是二次的。忽略切换前的记忆项，参数变化 Δ 约需 $\sqrt{2w\Delta/(\kappa(1-\alpha))}$ 步（该值不超过 w 时）：尺度是固定的 w，而不是不断增大的 $t_0$。

**证明**：$\sum_{s=1}^{\tau}Y_{t_0+s}=\frac1w\sum_s\sum_{i=0}^{w-1}O_{t_0+s-i}$。交换求和次序：
- 切换后的观测 $O_{t_0+j}$ 出现在 $s\in[j,\min(\tau,j+w-1)]$，共 $\min(\tau-j+1,w)$ 次；
- 切换前的观测 $O_{t_0-i}$（$0\le i\le w-2$）出现在 $s\in[1,w-1-i]$，共 $w-1-i$ 次（此处用到 $\tau\ge w-1$）。

α 项的权重合计为 $(\tau-w+1)+\frac{w-1}2+\frac{w-1}2=\tau$，与 $\sum_s\alpha$ 相符。第 3 条代入 $O\equiv1$，并用 $\sum_j\omega_j=\tau-\frac{w-1}{2}$。证毕。

`b_response_checks.py` 用有理数对 6 个 w、120 个随机情形核验第 2 条，全部精确相等。

### 数值示意（玩具，非数据中心证据）

G 取 α=0.1、γ=10、λ=log 2，$Y_{t_0}=\alpha$ 后全部违约：

| $t_0$ | 实际步数 | 定理 4.2 下界 | 定理 4.3 上界 | 步数 / $\sqrt{t_0}$ |
|---|---|---|---|---|
| $10^2$ | 44 | 34.5 | 57 | 4.40 |
| $10^3$ | 129 | 111.3 | 177 | 4.08 |
| $10^4$ | 397 | 354.2 | 556 | 3.97 |
| $10^5$ | 1246 | 1122.2 | 1756 | 3.94 |
| $10^6$ | 3929 | 3550.8 | 5551 | 3.93 |

比值收敛到 $\sqrt{2\gamma\lambda/(1-\alpha)}=3.92$。累计率超过 α+0.05 所需步数为 6、59、589、5883，与 $0.05t_0/0.85$ 一致，按 $t_0$ 线性增长。EV 的相应步数与 $t_0$ 无关。

**工程意义**：
- 用户“全历史累计反馈对新工况越来越不敏感”的直觉可以精确化：G 的参数时间尺度按 $\sqrt{t_0}$ 增长，信号时间尺度按 $t_0$ 增长。稿件参数下（15 分钟时隙），运行一年后 $t_0\approx3.5\times10^4$，半衰约需 735 步（约 7.7 天）；运行十年后约需 2320 步（约 24 天）。此数值只是渐近式在 γ=10、α=0.1、λ=log 2 下的代入。
- EV 的响应速度对所有 $t_0$ 相同。
- W0 同样与 $t_0$ 无关，但固定地慢约 $(w-1)/2$ 步；$w=2880$ 时约 15 天。

**是否需要 MPC 满足额外联系**：定理 4.1–4.5 都是参数递推的代数性质，不需要。把“参数变化”转化为“风险变化”则需要风险响应关系，见第 11 节。

---

## 5. 固定增益与递减增益

对象：$r_t=\Pi[r_{t-1}+\kappa_t(\alpha-O_t)]$，$\kappa_t>0$ 关于 t 不增。$\kappa_t\equiv\kappa$ 即 EV。

### 定理 5.1【严格】递减增益下的区间恒等式（Abel 求和）

对任意 $0\le a<b$，
$$
\sum_{t\in I}(O_t-\alpha)=-\sum_{t\in I}\frac{r_t-r_{t-1}}{\kappa_t}+\sum_{t\in I}\frac{c_t}{\kappa_t},
\qquad
\Big|\sum_{t\in I}\frac{r_t-r_{t-1}}{\kappa_t}\Big|\le\frac{D}{\kappa_b},
$$
并且有单侧界
$$
\sum_{t\in I}(O_t-\alpha)\le\frac{r_a-\underline r}{\kappa_{a+1}}+D\Big(\frac1{\kappa_b}-\frac1{\kappa_{a+1}}\Big)+\sum_{t\in I}\frac{c_t^+}{\kappa_t}.
$$

**证明**：
1. 由 $r_t-r_{t-1}=\kappa_t(\alpha-O_t)+c_t$，两边除以 $\kappa_t$ 后求和，得第一式。
2. 增量对平移不变，记 $x_t=r_t-\underline r\in[0,D]$。Abel 求和：
$$
\sum_{t=a+1}^{b}\frac{x_t-x_{t-1}}{\kappa_t}=\frac{x_b}{\kappa_b}-\frac{x_a}{\kappa_{a+1}}-\sum_{t=a+1}^{b-1}x_t\Big(\frac1{\kappa_{t+1}}-\frac1{\kappa_t}\Big).
$$
由增益不增，括号非负。
3. 上界：丢掉后两项，得 $\le D/\kappa_b$。下界：取 $x_b\ge0$、$x_a\le D$、$x_t\le D$，得 $\ge-D/\kappa_{a+1}-D(1/\kappa_b-1/\kappa_{a+1})=-D/\kappa_b$。
4. 单侧界：用第 2 步的下界时保留 $x_a$，并用 $c_t\le c_t^+$。证毕。

固定增益时，第二项为零，单侧界退回定理 3.4。

**核验**：`b_gain_checks.py` 对 $\kappa_t=\kappa_0/t$ 与 $\kappa_0/\lceil\sqrt t\rceil$ 两类增益、200 条随机路径、109,301 个区间，精确核验恒等式、$D/\kappa_b$ 界与单侧界。

### 推论 5.2【严格】递减增益失去近期响应

1. **区间界随时间变松**：长度为 w 的最近窗口 $(t-w,t]$，定理 5.1 的残差为 $D/(\kappa_t w)$ 量级。取 $\kappa_t=\kappa_0t^{-p}$（$0<p\le1$），它等于 $Dt^p/(\kappa_0w)$，随 t 无界增长。固定增益则对所有窗口位置都是 $D/(\kappa w)$。
2. **这个松弛是真实可达的，不只是证明的缺陷**：设参数在 $t_s$ 时停在 $\overline r$，此后对象持续违约，直到参数降到 $\underline r$。所需违约次数约为 $D/(\kappa_{t_s}(1-\alpha))$，随 $t_s$ 增长。`b_gain_checks.py`：$\kappa_t=1/\sqrt t$、$D=1$、α=0.1 时，$t_s=10^2,10^3,10^4,10^5$ 对应 12、36、112、352 次，与 $D/(\kappa_{t_s}(1-\alpha))$ 一致。固定增益下这个数与 $t_s$ 无关。
3. **移动 Δ 所需时间**：每步至多移动 $\kappa_t\beta$，所以 $t_0$ 之后移动 Δ 至少需要 τ 满足 $\beta\sum_{t_0<t\le t_0+\tau}\kappa_t\ge\Delta$。
   - $p=1$：$\tau\ge t_0\big(e^{\Delta/(\beta\kappa_0)}-1\big)$，按 $t_0$ 线性增长；
   - $p=1/2$：$\tau\ge\Delta\sqrt{t_0}/(\beta\kappa_0)+\Delta^2/(4\beta^2\kappa_0^2)$，按 $\sqrt{t_0}$ 增长，与 G（定理 4.2）同阶。

**证明**：第 1 条由定理 5.1 代入。第 3 条用 $\sum_{t=t_0+1}^{t_0+\tau}t^{-p}\le\int_{t_0}^{t_0+\tau}x^{-p}dx$ 后解不等式。证毕。

### 命题 5.3【条件：静态代理与单调回复】递减增益的收敛优势

**假设**（第 11 节的附加代理）：$\mathbb P(O_{t+1}=1\mid\mathcal F_t)=F(r_t)$；存在 $r_\*\in[\underline r,\overline r]$ 与 $\mu>0$，使 $(r-r_\*)(F(r)-\alpha)\ge\mu(r-r_\*)^2$ 对所有 $r\in[\underline r,\overline r]$ 成立；$2\mu\kappa_t\le1$。

**结论**：若 $\kappa_t\to0$ 且 $\sum_t\kappa_t=\infty$，则 $e_t:=\mathbb E(r_t-r_\*)^2\to0$。固定增益只能得到 $\limsup e_t\le\kappa v/(2\mu)$（第 11 节）。

**证明**：
1. 与第 11 节命题 11.2 相同的推导，把 κ 换成 $\kappa_{t+1}$，得 $e_{t+1}\le(1-a_t)e_t+b_t$，其中 $a_t=2\mu\kappa_{t+1}\in(0,1]$，$b_t=\kappa_{t+1}^2v$。
2. **引理**：若 $a_t\in(0,1]$、$\sum a_t=\infty$、$b_t/a_t\to0$，则 $e_t\to0$。证：任给 $\epsilon>0$，存在 T 使 $t\ge T$ 时 $b_t\le\epsilon a_t$，于是 $e_{t+1}-\epsilon\le(1-a_t)(e_t-\epsilon)$，从而 $(e_{t+1}-\epsilon)_+\le\prod_{s=T}^{t}(1-a_s)(e_T-\epsilon)_+\to0$。所以 $\limsup e_t\le\epsilon$。
3. 此处 $b_t/a_t=\kappa_{t+1}v/(2\mu)\to0$。证毕。

几乎必然收敛还需要 $\sum\kappa_t^2<\infty$，这是 Robbins–Monro 随机逼近的经典结果（Robbins & Monro 1951，Ann. Math. Statist. 22:400–407；Robbins & Siegmund 1971 的几乎超鞅收敛定理）。本文不重证。

### 注 5.4 二者的冲突

- 递减增益的优势（$F(r_t)\to\alpha$ 精确成立）与它失去近期响应（推论 5.2）来自同一个事实：$\kappa_t\to0$。
- 这个优势只在**工况平稳**、回复条件长期成立时才有意义。一旦 $r_\*$ 随季节或负载漂移，递减增益的跟踪误差上界 $\rho/(\kappa_t\mu)$ 趋于无穷（第 11 节命题 11.4）。
- 用户的动机是“工况变化下的近期风险”。因此固定增益与动机一致；递减增益只适用于已知平稳的对象，而数据中心的负载、电价与气候都不满足这一点。
- 固定增益的代价是稳态波动为 $O(\kappa)$，需要按第 7、11 节选 κ。

**适用对象**：定理 5.1、推论 5.2 对任意路径成立；命题 5.3 需要静态代理与回复条件。

---

## 6. 初始化与校准阶段

### 命题 6.1【严格】初始化误差等于暂态中的超预算违约次数

对任意 $b\ge1$，
$$
\sum_{t=1}^{b}(O_t-\alpha)=\frac{r_0-r_b+C^+_{(0,b]}-C^-_{(0,b]}}{\kappa}.
$$
若暂态结束时 $r_b$ 已接近响应平衡点 $r^\*$，则暂态累计的超预算违约数约为 $(r_0-r^\*)/\kappa$（为负时表示预算未用）。

**证明**：定理 3.4 取 $a=0$。证毕。

**工程意义**：初始化误差以参数单位计，除以 κ 就是违约次数。这是判断是否需要离线校准的直接依据（命题 6.4）。

### 命题 6.2 $r_0=0$ 与保守初始化

**(a)【严格】保守初始化使上侧证书从一开始就最紧。** 取 $r_0=\underline r$，则对所有 b，
$$
\bar O_{(0,b]}\le\alpha+\frac{C^+_{(0,b]}}{\kappa b},
$$
即初始松弛项 $r_0-\underline r=0$。注意：这只对从 0 开始的区间成立；对 $a>0$ 的区间，松弛项是 $r_a-\underline r$，一般不为零。

**(b)【条件：$\mathrm D_\theta$，$\underline r\le\theta$】代价是早期预算必然不用。** 从 $r_0=\underline r$ 出发，前
$$
k^\*=\Big\lfloor\frac{\theta-\underline r}{\kappa\alpha}\Big\rfloor+1
$$
个观测必然为 0。这段时间未用的预算为 $\alpha k^\*\approx(\theta-\underline r)/\kappa$ 次。同时，这段时间 MPC 在比名义界更紧的约束下运行，由命题 2.1，每个时刻的计划成本不低于名义界。

证明：$O_1=\dots=O_k=0$ 时参数为 $\underline r+k\kappa\alpha$。由 $\mathrm D_\theta$，$O_{k+1}=1$ 要求 $\underline r+k\kappa\alpha>\theta$，即 $k>(\theta-\underline r)/(\kappa\alpha)$。证毕。

**(c)【条件：$\mathrm D_\theta$】对储能通道，“保守”应取 $m^\*$ 而不是 $\underline r$。** 由定理 3.7，只要 $r_0\ge m^\*=\theta-\kappa(1-\alpha)$，参数就永远不低于 $m^\*$，且上侧证书的松弛项是 $(r_a-m^\*)_+$。
- 取 $r_0=m^\*$，松弛项为零，由 (b) 未用预算只有 $(\theta-m^\*)/\kappa=1-\alpha$ 次左右，不到一次；
- 取 $r_0=\underline r<m^\*$，只会多出 $(m^\*-\underline r)/\kappa$ 次未用预算，没有任何证书上的好处；
- 取 $r_0=0$ 且 $\theta=0$（储能，$\varepsilon=\eta=0$），松弛项为 $\kappa(1-\alpha)$，即最多多出不到一次超预算违约。

所以对满足 $\mathrm D_\theta$ 的储能通道，$r_0=0$ 与最保守初始化的差别可以忽略。

**(d) $r_0=0$ 的合理性**：
- 名义界是设计者的参考点，$r_0=0$ 表示“开始时既不放松也不收紧”，不需要任何先验；
- 加法更新在 0 处不吸收（命题 3.2），而 G 不能从名义界启动；
- 对不满足 $\mathrm D_\theta$ 的通道（温度失配无界），$r_0=0$ 只是中性起点，不保证起始风险达标，也不保证初次优化可行。

### 命题 6.3 从 $r_0$ 到 $r^\*$ 的暂态步数

- **(a)【严格】下界**：每步上升至多 κα、下降至多 κ(1−α)。所以进入 $r^\*$ 的 δ 邻域，至少需要 $(r_0-r^\*-\delta)_+/(\kappa(1-\alpha))$ 或 $(r^\*-r_0-\delta)_+/(\kappa\alpha)$ 步。
- **(b)【条件：精确阈值 θ】上界**：进入不变区间 $J=[\theta-\kappa(1-\alpha),\theta+\kappa\alpha]$ 至多需要
$$
\Big\lceil\frac{(r_0-\theta-\kappa\alpha)_+}{\kappa(1-\alpha)}\Big\rceil+\Big\lceil\frac{(\theta-\kappa(1-\alpha)-r_0)_+}{\kappa\alpha}\Big\rceil
$$
步（merged 定理 4，第 8 节复核）。两项中至多一项非零。
- **(c)【条件：静态代理与回复条件（第 11 节）】均方意义**：$e_t\le(1-2\kappa\mu)^te_0+\kappa v/(2\mu)$。要使 $e_t$ 不超过稳态水平的两倍，只需
$$
t\ge\frac{\log\big(2\mu e_0/(\kappa v)\big)}{-\log(1-2\kappa\mu)}\approx\frac{1}{2\kappa\mu}\log\frac{2\mu e_0}{\kappa v}.
$$
即暂态时间与 $1/(\kappa\mu)$ 成正比，只对初始误差取对数。

### 命题 6.4 何时需要离线校准阶段

以下任一情形出现时，建议在评价期之前加入离线校准：
1. 预计的暂态超预算违约数 $(r_0-\hat r^\*)_+/\kappa$（命题 6.1）超过第一个评价窗口允许的超额次数 $N_{\rm tol}$。$N_{\rm tol}$ 由用户的考核口径决定，例如 $\epsilon_Y w$。
2. 预计的暂态步数 $|r_0-\hat r^\*|/(\kappa\min(\alpha,1-\alpha))$ 与 w 同量级，第一个窗口主要由暂态决定。
3. 名义界处的违约率远高于 α 的通道（例如温度通道有持续模型偏差，$r^\*$ 明显为负）。从 $r_0=0$ 出发，暂态期间违约率高，超额次数约为 $|r^\*|/\kappa$。

**校准方法**：
- 在历史数据上用同一 MPC、同一预测离线回放闭环，估计 $\hat r^\*$；
- 取 $r_0=\hat r^\*$，或为了保守取 $\hat r^\*$ 再减一个裕度；
- 校准期与评价期分开报告。

恒等式对任何 $r_0$ 都成立，校准只减小 $r_0-r_b$ 一项，不改变任何证书的形式。

**失效情形**：若工况在校准后发生变化，$\hat r^\*$ 过时；此时固定增益的时间不变响应（定理 4.1）会自动重新跟踪，代价仍按命题 6.1 计入超额次数。

---

## 7. 允许区间与设备动态

本节量纲约定：$r,\underline r,\overline r,\theta,\kappa,\eta,\bar e$ 与设备一步能力 $c_{\rm dev}$ 均与约束同单位 U（储能 MWh，温度 K）；响应斜率 $F'$ 的单位为 $U^{-1}$；违约次数无量纲。

### 命题 7.1【条件】储能：收紧一步后的第一步约束可行性

**假设**：储能模型精确（命题 1.2 (iii)）；上一步计划满足 $\hat E_{t\mid t-1}\ge E_{\min}-r^1_{t-1}-\eta$，于是实际 $E_t$ 也满足；记 $d_+^{\rm eff}(t)$ 为时刻 t 在其余全部约束（功率平衡、购电上限、可再生出力、SLA 等）下**保证能实现**的一步最大充电增量。

**结论**：若 $r^1_{t-1}-r^1_t+\eta\le d_+^{\rm eff}(t)$，则 $\ell=1$ 的下界约束 $\hat E_{t+1\mid t}\ge E_{\min}-r^1_t$ 可满足。由命题 3.1，充分条件为
$$
\kappa_1(1-\alpha_1)+\eta\le d_+^{\rm eff}.
$$
上界通道对称：$\kappa_2(1-\alpha_2)+\eta\le d_-^{\rm eff}$。

**证明**：需要的一步抬升量为 $E_{\min}-r^1_t-E_t\le(E_{\min}-r^1_t)-(E_{\min}-r^1_{t-1}-\eta)=r^1_{t-1}-r^1_t+\eta$。证毕。

**说明**：
- 额定值只给出上限：$d_+^{\rm eff}\le\eta_{ch}P^{bs}_{\max}\Delta\tau=97/80$ MWh，$d_-^{\rm eff}\le P^{bs}_{\max}\Delta\tau/\eta_{dis}=125/98$ MWh（稿件表 1 参数）。实际 $d^{\rm eff}$ 可能更小，例如高负载时充电受购电上限限制。
- 只有收紧方向需要这个条件；放松会扩大可行集。merged §3.2 的 $\kappa\max(\alpha,1-\alpha)\le d$ 对 α<1/2 与本条一致，但一般应只约束收紧步 $\kappa(1-\alpha)$。【收窄】
- 储能上下界的违约事件互斥，所以**同一步至多一侧收紧**，另一侧放松。两侧同时收紧只会通过多步累积发生，需要带宽条件：$\underline r^1+\underline r^2\ge-(E_{\max}-E_{\min}-W_{\min})$，$W_{\min}$ 为所需最小运行带宽。
- 第一步可行不等于整个预测域可行，也不保证其他硬约束不冲突。

### 命题 7.2【条件】温度：收紧一步需要的一步降温能力

**假设**：控制模型 $T_{t+1}=T_t+d_1p^{ser}_t+d_2T^{chw}_t$（稿件式 (Tchw)）；实际温度与计划之差不超过 $\bar e$；记
$$
c_T:=\inf_t\ \max_{\text{可行 }u_t}\big[-(d_1p^{ser}_t+d_2T^{chw}_t)\big]
$$
为保证能实现的一步降温量。可行动作受 $T^{chw}$ 的上下限、爬坡 $\zeta\Delta\tau$，以及不可延迟负载的最小 $p^{ser}$ 限制。

**结论**：若
$$
\kappa_3(1-\alpha_3)+\eta+\bar e\le c_T,
$$
则 $\ell=1$ 温度约束在收紧后仍可满足。

**证明**：$T_t\le T_{\max}+r_{t-1}+\eta+\bar e$。要求 $\hat T_{t+1\mid t}\le T_{\max}+r_t$，需要的降温量不超过 $r_{t-1}-r_t+\eta+\bar e\le\kappa_3(1-\alpha_3)+\eta+\bar e$。证毕。

**失效情形**：
- 若 $c_T\le0$（模型在某些时刻无法降温），任何收紧都可能不可行；
- 若 $\bar e$ 没有可信上界，无法事先保证，只能依赖软化与日志记录。

### 命题 7.3【条件：$\mathrm D_\theta$】$\overline r$ 由物理界与裕度决定，并决定违约幅度上限

- 储能：$\overline r^1\le E_{\min}-E^{\rm phys}_{\min}-m_E$，$\overline r^2\le E^{\rm phys}_{\max}-E_{\max}-m_E$。
- 温度：$\overline r^3\le T^{\rm safe}_{\max}-T_{\max}-\bar e-m_T$。
- 在 $\mathrm D_\theta$ 下，违约幅度 $\le\overline r+\eta$（温度再加 $\bar e$），所以 $\overline r$ 就是幅度上限的设计量（命题 1.2）。
- $\overline r$ 还与 κ 一起决定超额次数 $(\overline r-\theta)/\kappa$ 与恢复首段长度（定理 3.7、第 9 节）。

**证明**：由命题 1.2 的幅度不等式与 $r_t\le\overline r$。证毕。

### 命题 7.4 负下界何时必要

**(a)【条件：静态代理，F 单调不减】名义界处违约率高于目标时必须允许收紧。** 设 $\mathbb P(O_{t+1}=1\mid\mathcal F_t)=F(r_t)$。若 $\underline r\ge0$ 且 $F(0)>\alpha$，则对所有 t 有 $F(r_t)\ge F(0)>\alpha$，平均条件违约概率不低于 $F(0)$，目标不可达。反之，若 F 连续、$F(0)\le\alpha\le F(\overline r)$，则 $[0,\overline r]$ 内存在 $r^\*$，不需要负下界即可有平衡点。

证明：单调性与介值定理。证毕。

**(b)【条件：精确阈值】即使名义界处不违约，离散步长也需要一步余量。** 设 $O_{t+1}=\mathbf 1\{r_t>\theta\}$，$\overline r>\theta$。则
$$
\text{“从每个初值出发都永不发生下侧截断”}\iff\underline r\le\theta-\kappa(1-\alpha).
$$
证明：充分性见定理 3.7（精确阈值满足 $\mathrm D_\theta$）。必要性：设 $c:=\theta-\underline r<\kappa(1-\alpha)$，取 $r_0\in\big(\theta,\ \min(\overline r,\ \theta+\kappa(1-\alpha)-c)\big)$，则 $O_1=1$，$v_1=r_0-\kappa(1-\alpha)<\theta-c=\underline r$，第一步即严格截断。证毕。`b_path_checks.py` 第 D 项 300 例核验。

**(c)【条件：$\mathrm D_\theta$】** 若 $\theta\ge\kappa(1-\alpha)$，则 $\underline r=0$ 已满足定理 3.7。对储能且 $\varepsilon=\eta=0$，$\theta=0$，需要 $\underline r\le-\kappa(1-\alpha)$，只是一步收紧量，不是因为名义界风险过高。

**(d) 与旧说法的关系【收窄】**：
- “储能下界必须 $\le-\kappa(1-\alpha)$”：在精确阈值下，它是“对所有初值都不截断”的充要条件；对特定轨道不必要（merged 的 $-0.8\kappa$ 反例仍成立）。
- 在更一般的 $\mathrm D_\theta$ 下它是充分条件；而且由定理 3.7，比 $m^\*$ 更低的下界永远不会被触及，所以取得更负没有代价，也没有好处。
- 用 ε 抬高 θ 来避免负下界，是对违约定义的改变（只计超过 ε 的违约），必须事先声明，不能作为调参手段。

### 7.5 选参原则：超额次数、单步幅度、稳定性的三方约束

**三个量**：
- 放松权限 $A$：满足 $\mathrm D_\theta$ 时 $A=\overline r-\theta$，否则用 $D=\overline r-\underline r$。
- 超额次数上限 $N_{\rm ex}$：满足 $\mathrm D_\theta$ 时 $N_{\rm ex}=A/\kappa+(d+1)(1-\alpha)$（定理 3.7；有额外延迟 d 时见定理 9.2，此时要求 $\underline r\le\theta-(d+1)\kappa(1-\alpha)$）；一般情形 $N_{\rm ex}=D/\kappa$，**不计下侧截断项**（定理 3.4）。
- 恢复首段长度 $L_{\rm burst}=\lceil A/(\kappa(1-\alpha))\rceil+d$（第 8、9 节，d 为额外延迟）。

**三方约束**（用户给定容许值 $N_{\rm tol}$、$L_{\max}$、方差容许 $V_{\rm tol}$、稳定裕度 $\varsigma\in(0,1)$）：

| 约束 | 条件 | 对 κ 的作用 |
|---|---|---|
| C1 证书与首段 | $N_{\rm ex}\le N_{\rm tol}$，$L_{\rm burst}\le L_{\max}$ | $\kappa\ge\dfrac{A}{N_{\rm tol}-(d+1)(1-\alpha)}$，$\kappa\ge\dfrac{A}{(1-\alpha)(L_{\max}-d)}$ |
| C2 单步幅度 | $\kappa(1-\alpha)+\eta+\bar e\le c_{\rm dev}$（命题 7.1、7.2） | $\kappa\le\dfrac{c_{\rm dev}-\eta-\bar e}{1-\alpha}$ |
| C3 稳定与噪声（光滑通道） | $\kappa F'_{\max}\le(1-\varsigma)\,2\sin\dfrac{\pi}{2(2d+1)}$；$\dfrac{\kappa\sigma^2}{F'(2-\kappa F')}\le V_{\rm tol}$（第 11 节） | κ 的上界 |
| C3′ 阈值型通道 | 没有线性稳定条件；改为 $\underline r\le\theta-(d+1)\kappa(1-\alpha)$（第 8 节） | 约束 $\underline r$ |

**可行性判据**：κ 存在当且仅当 C1 的下界不超过 C2、C3 的上界。消去 κ，得到与参数无关的**物理一致性条件**：
$$
A\le(L_{\max}-d)\,(c_{\rm dev}-\eta-\bar e),\qquad
A\le\frac{\big(N_{\rm tol}-(d+1)(1-\alpha)\big)(c_{\rm dev}-\eta-\bar e)}{1-\alpha}.
$$
第一式的含义是：放松权限不能超过设备在允许的首段时长内能“收回”的量。若不满足，任何 κ 都无效，只能减小 $\overline r$、放宽 $L_{\max}$ 或 $N_{\rm tol}$，或提高设备能力。

**选参顺序**：
1. 由物理界、幅度容许与失配定 $\overline r$（命题 7.3）；
2. 在需要的通道定 $\underline r$：不高于 $\theta-(d+1)\kappa(1-\alpha)$（阈值型、带延迟时），不低于带宽与设备允许的最紧值（命题 7.1）；
3. 在 C1–C3 给出的区间内选 κ；
4. 区间为空时回到第 1 步。

本文不给任何统一数值。$N_{\rm tol}$、$L_{\max}$、$V_{\rm tol}$、$\varsigma$ 属于用户的考核口径；$c_{\rm dev}$、$\bar e$、$F'_{\max}$、σ 须由设备数据与日志估计。

**量纲核对**：C1 中 $A/\kappa$ 无量纲；C2 两边都是 U；C3 中 $\kappa F'$ 无量纲，方差为 $U^2$。

---

## 8. 阈值响应、观测延迟与容差

本节的响应模型都是【条件】：它们是对实际闭环的代理，不能由储能递推的确定性自动推出（Threshold_Response_Audit 已说明储能中部存在“一步安全区”，阈值不是常数）。满足 $\mathrm D_\theta$ 的通道可以只用第 3 节定理 3.7，不需要精确阈值。

### 定理 8.1【条件：精确阈值，无额外延迟】复核 merged 定理 4

**假设**：所研究时段内精确有 $O_{t+1}=\mathbf 1\{r_t>\theta\}$；$J=[\theta-\kappa(1-\alpha),\theta+\kappa\alpha]\subseteq[\underline r,\overline r]$，即**同时**要求 $\underline r\le\theta-\kappa(1-\alpha)$ 与 $\overline r\ge\theta+\kappa\alpha$。

**结论**：
1. J 不变，J 内不发生投影；
2. 从任意 $r_a\in[\underline r,\overline r]$ 进入 J 至多需要 $\big\lceil(r_a-\theta-\kappa\alpha)_+/(\kappa(1-\alpha))\big\rceil+\big\lceil(\theta-\kappa(1-\alpha)-r_a)_+/(\kappa\alpha)\big\rceil$ 步；
3. 进入 J 之后，**完全位于该阈值时段内**的任意长度 L 的区间满足 $|\sum_I(O-\alpha)|\le1$，即 $|\bar O_I-\alpha|\le1/L$。

**证明**：
1. $r\in[\theta-\kappa(1-\alpha),\theta]$ 时不违约，下一值 $r+\kappa\alpha\in[\theta-\kappa(1-2\alpha),\theta+\kappa\alpha]\subseteq J$；$r\in(\theta,\theta+\kappa\alpha]$ 时违约，下一值 $r-\kappa(1-\alpha)\in(\theta-\kappa(1-\alpha),\theta-\kappa(1-2\alpha)]\subseteq J$。两支都在 J 内，故不投影。严格不等号 $r>\theta$ 很重要：$r=\theta$ 时不违约。
2. 高于 J 时 $r>\theta$，每步恰减 $\kappa(1-\alpha)$；首次降到 $\theta+\kappa\alpha$ 以下时，值大于 $\theta+\kappa\alpha-\kappa(1-\alpha)\ge\theta-\kappa(1-\alpha)$，落在 J 内且不被截断。低于 J 时对称。
3. 无投影时 $\sum_I(O-\alpha)=(r_a-r_b)/\kappa$，而 $r_a,r_b\in J$，$|J|=\kappa$。证毕。

**复核结论**：merged 定理 4 正确，且其限定条件（两端都包含 J、进入后才计、完全在阈值时段内、阈值精确）都必不可少。`b_threshold_checks.py`：300 组随机参数、549,000 个区间，进入时间、不变性与计数界全部成立。

补充：J 的包含条件中，下侧 $\underline r\le\theta-\kappa(1-\alpha)$ 是“对所有初值都不截断”的充要条件（命题 7.4(b)）；对特定初值不必要（merged 的 $-0.8\kappa$ 反例）。

### 定理 8.2【条件：精确阈值，额外延迟 d】不变区间扩大为 $(d+1)$ 倍

**模型**：$O_{t+1+d}=\mathbf 1\{r_t>\theta\}$，即 $O_t=\mathbf 1\{r_{t-1-d}>\theta\}$，$d\ge0$ 为整数。初始历史 $(r_{-d},\dots,r_0)$ 满足：都在 $J_d$ 内，且相邻两项的增量满足 EV 的单步界（上升不超过 κα，下降不超过 κ(1−α)），例如由同一更新从更早的观测生成。

记
$$
J_d=\big[\theta-(d+1)\kappa(1-\alpha),\ \theta+(d+1)\kappa\alpha\big],\qquad |J_d|=(d+1)\kappa .
$$

**结论**：若 $J_d\subseteq[\underline r,\overline r]$，则
1. 对所有 $t\ge0$，$r_t\in J_d$，且从不投影；
2. 任意区间 $|\sum_I(O_t-\alpha)|\le d+1$，即 $|\bar O_I-\alpha|\le(d+1)/L$；
3. 最长连续违约 $\le d+\big\lceil(d+1)\alpha/(1-\alpha)\big\rceil$；当 $\alpha\le1/(d+2)$ 时等于 $d+1$；
4. 最长连续不违约 $\le d+\big\lceil(d+1)(1-\alpha)/\alpha\big\rceil$。

**证明**：
1. **上包络**。设第 $t+1$ 步参数上升，则 $O_{t+1}=0$，即 $r_{t-d}\le\theta$。令 $u-1$ 为 $[t-d,t]$ 中最后一个满足 $r\le\theta$ 的下标。从 $u-1$ 到 $t+1$ 共 $t+2-u\le d+1$ 步，每步至多上升 κα，所以 $r_{t+1}\le\theta+(d+1)\kappa\alpha$。下降不会抬高最大值。
2. **下包络**。设第 $t+1$ 步下降，则 $r_{t-d}>\theta$。令 $u-1$ 为 $[t-d,t]$ 中最后一个满足 $r>\theta$ 的下标，同理 $r_{t+1}>\theta-(d+1)\kappa(1-\alpha)$。
3. 第 1、2 步对投影前的值 $v_{t+1}$ 同样成立。于是只要 $J_d\subseteq[\underline r,\overline r]$，归纳可知从不投影，结论 1 成立。
4. 无投影时 $\sum_I(O-\alpha)=(r_a-r_b)/\kappa$，两端都在 $J_d$ 内，得结论 2。
5. **连续违约**。$O_{t+1}=\dots=O_{t+k}=1$ 意味着 $r_{t-d},\dots,r_{t+k-1-d}$ 连续 k 个值都大于 θ。考虑一段极大的 “>θ” 连续段，起点 u（$r_{u-1}\le\theta<r_u$）。对 $s-1-d\in[u,\text{段末}]$，$O_s=1$，所以从 $u+d$ 起参数每步下降 $\kappa(1-\alpha)$，直到降到 θ 以下。由上包络 $r_{u+d}\le\theta+(d+1)\kappa\alpha$，从 $u+d$ 起大于 θ 的值至多 $\lceil(d+1)\alpha/(1-\alpha)\rceil$ 个，再加上 $r_u,\dots,r_{u+d-1}$ 这 d 个，得结论 3。$\alpha\le1/(d+2)$ 等价于 $(d+1)\alpha/(1-\alpha)\le1$。
6. 结论 4 对称。证毕。

**核验**：`b_threshold_checks.py`，d=0,…,6，240 条由动态生成的一致历史，777,600 个区间，结论 1–3 全部成立。α=0.1、κ=1/20 时：

| d | $\underline r=\theta-\kappa(1-\alpha)$（无延迟余量） | $\underline r=\theta-(d+1)\kappa(1-\alpha)$ |
|---|---|---|
| 1 | 违约率 0.1539，最长连续违约 2 | 0.1000，2 |
| 4 | 0.2633，5 | 0.1000，5 |
| 8 | 0.3336，9 | 0.1002，9 |

左列复现了 R3 报告 EV($\Delta_P$) 在延迟下的失效（0.1539、0.2631、0.3334），右列说明失效只来自下侧余量不够，与“逐事件”本身无关。

### 命题 8.3【条件：精确阈值、延迟 d、稳态循环】所需下侧余量的精确值

**问题**：需要的下侧余量是否约为 $(d+1)\kappa(1-\alpha)$？

**结论**：
- $(d+1)\kappa(1-\alpha)$ 对所有 α、所有一致历史都**充分**（定理 8.2）。
- 设 $\alpha\le1/(d+2)$，并设轨道处于稳态循环：每段 “≤θ” 连续段的长度至少为 d。一旦某一段满足这一点，此后所有段都满足。此时对所有 t，
$$
r_t>\theta-\kappa\big[(d+1)(1-\alpha)-d\alpha\big],
$$
并且适当的相位可以任意接近这个值。所以精确余量比 $(d+1)\kappa(1-\alpha)$ 少 $d\kappa\alpha$。
- 暂态中（前一段 ≤θ 连续段短于 d），最小值可能更低，但由定理 8.2 不会低于 $\theta-(d+1)\kappa(1-\alpha)$。

**证明**：
1. 设 “>θ” 段起点 u，$r_u=\theta+\epsilon_1$，$\epsilon_1\in(0,\kappa\alpha]$。前一段 ≤θ 连续段长度至少为 d，所以 $O_{u+1},\dots,O_{u+d}$ 对应的参数都 ≤θ，参数连升 d 步：$r_{u+d}=\theta+\epsilon_1+d\kappa\alpha$。
2. $O_{u+d+1}=\mathbf 1\{r_u>\theta\}=1$，下降一步得 $r_{u+d+1}=\theta+\epsilon_1+d\kappa\alpha-\kappa(1-\alpha)$。由 $\alpha\le1/(d+2)$，$(d+1)\kappa\alpha\le\kappa(1-\alpha)$，所以已经 ≤θ，“>θ” 段长恰为 d+1。
3. 此后 d 步的观测对应 $r_{u+1},\dots,r_{u+d}$，都大于 θ，参数再降 d 步，得到最小值
$$
r_{\min}=\theta+\epsilon_1+d\kappa\alpha-(d+1)\kappa(1-\alpha)>\theta-\kappa[(d+1)(1-\alpha)-d\alpha].
$$
随后的 ≤θ 段长度为 $d+1+(\text{上升段})\ge d+1$，稳态条件保持。$\epsilon_1\to0^+$ 时取到下确界。证毕。

**核验**：d=0,1,2,4、α∈{1/10,1/20,1/7}，扫描 200 个相位，最小值均严格高于预测下确界，并从下方逼近，例如 d=1、α=0.1 时 1.6995κ 对 1.7κ。

**工程意义**：设计时直接用 $\underline r\le\theta-(d+1)\kappa(1-\alpha)$ 即可，它对暂态也成立，比精确值只多 $d\kappa\alpha$。

**计数误差与连续违约**：在 $J_d$ 包含于区间内时，任意区间的计数误差不超过 $d+1$ 次；α≤1/(d+2) 时最长连续违约为 $d+1$。与 R3 报告中 TB-PI 的 “最长 d+1” 相同，所以 EV 在正确余量下同样对延迟稳健。

### 命题 8.4【条件：精确阈值】容差不一致导致的永久违约，以及两种修正

**情形**：违约判定取 $\varepsilon=0$，而求解器在约束贴边时返回 $g+r_t+\eta$（$\eta>0$，例如 $10^{-9}$）。在 $\mathrm D_\theta$ 意义下 $\theta=\varepsilon-\eta=-\eta<0$。

**结论**：
1. 若 $\underline r>\theta$（例如 $\underline r=0$），且对象在允许时总会贴边（精确阈值），则从某一步起 $O_t\equiv1$：参数停在 $\underline r$，每步 $c_t^+=\kappa(1-\alpha)$，违约率为 1，连续违约贯穿全程。
2. 修正一：把违约定义的容差设为 $\varepsilon\ge\eta$（事先声明的求解与量测容差），使 $\theta=\varepsilon-\eta\ge0$；并仍取 $\underline r\le\theta-\kappa(1-\alpha)$。
3. 修正二：保留 $\varepsilon=0$，取 $\underline r\le-\eta-\kappa(1-\alpha)$。

**证明**：$r_t\ge\underline r>\theta$ 对所有 t 成立，故 $O_{t+1}=1$ 对所有 t 成立；参数单调下降至 $\underline r$ 后，每步投影前值为 $\underline r-\kappa(1-\alpha)$。修正一、二都使 $\underline r\le\theta-\kappa(1-\alpha)$，由定理 3.7 不再截断。证毕。

**核验**：α=0.1、κ=1/20、$\eta=10^{-9}$：$\underline r=0$ 时违约率 1.0、最长连续违约 3000（整个统计段）；两种修正下违约率 0.1、最长连续违约 1。

**说明**：
- 这是违约定义与求解器容差不一致的问题，W0、EV、TB-PI 都会遇到（R3 已指出），不是更新律的问题。
- ε 必须事先声明，并与求解器的可行性容差（例如 Gurobi 的 FeasibilityTol 参数）和量测分辨率一致，不能事后调整来改善指标（merged §11）。
- 温度通道还应把量测噪声计入 ε 或 $\bar e$。

---

## 9. 静默期后的恢复

“静默”指一段时间内约束不紧、对象不违约（例如季节切换、通道被其他约束压住）；“恢复”指约束重新变紧。

### 命题 9.1【严格】静默期把参数推到上界

若 $O_t=0$ 对 $t\in(a,b]$ 成立，则 $r_t=\min\big(\overline r,\ r_a+(t-a)\kappa\alpha\big)$。参数在 $\lceil(\overline r-r_a)/(\kappa\alpha)\rceil$ 步后到达 $\overline r$，此后每步上侧截断 $c_t^-=\kappa\alpha$，$U_I\to1$（命题 2.2）。

**证明**：命题 3.3 第 2 条。证毕。

### 定理 9.2【条件：$\mathrm D_\theta$，允许额外延迟 d】恢复后的连续违约与幅度

**假设**：恢复之后，每个决策满足 $O_{t+1+d}\le\mathbf 1\{r_t>\theta\}$（$d\ge0$；d=0 即 $\mathrm D_\theta$）；$\underline r\le\theta$。对恢复前已在途的 d 个观测不作要求。

**结论**：
1. 恢复后的**每一段**连续违约（包括首段）长度不超过
$$
d+\Big\lceil\frac{\overline r-\theta}{\kappa(1-\alpha)}\Big\rceil .
$$
2. 若 $\mathrm D_\theta$ 来自命题 1.2，违约幅度不超过 $\overline r+\eta$（温度通道再加 $\bar e$）。
3. 若还有 $\underline r\le m^\*_d:=\theta-(d+1)\kappa(1-\alpha)$，则从不下侧截断，任意区间的超预算违约数 $\le(\overline r-\theta)/\kappa+(d+1)(1-\alpha)$。这是定理 3.7 的延迟版本。
4. **紧性**：精确阈值、恢复时 $r=\overline r$、在途观测为静默期的 0，则首段恰为 $d+\lceil(\overline r-\theta)/(\kappa(1-\alpha))\rceil$。

**证明**：
1. 设 $O_{t+1}=\dots=O_{t+k}=1$，且 $k>d$。由假设，$r_{t+k-1-d}>\theta$。这 k 步都是违约，参数逐步下降，且由 $\underline r\le\theta$，被截断的值不超过 θ。因此只要仍大于 θ，就有 $r_{t+j}=r_t-j\kappa(1-\alpha)$。于是 $r_t-(k-1-d)\kappa(1-\alpha)>\theta$，结合 $r_t\le\overline r$ 得 $k-1-d<X:=(\overline r-\theta)/(\kappa(1-\alpha))$，即 $k\le d+\lceil X\rceil$。$k\le d$ 时结论显然。
2. 由命题 1.2 与 $r\le\overline r$。
3. 下包络：参数在第 $t+1$ 步下降，要求 $O_{t+1}=1$，从而 $r_{t-d}>\theta$。令 $u-1$ 为 $[t-d,t]$ 中最后一个大于 θ 的下标，则 $r_{t+1}>\theta-(d+1)\kappa(1-\alpha)=m^\*_d$。所以 $r_b\ge\min(r_a,m^\*_d)$，且下降步的投影前值高于 $m^\*_d\ge\underline r$，不截断。其余同定理 3.7 第 3 步。这里只用到“违约 ⇒ 延迟前的参数大于 θ”，不需要精确阈值。
4. 在途观测为 0，参数在 $\overline r$ 停留 d 步；此后 $r_{t_r},\dots,r_{t_r+d}$ 都等于 $\overline r$，对应 d+1 次违约，再加上 $\overline r-j\kappa(1-\alpha)>\theta$ 的 $\lceil X\rceil-1$ 个值。证毕。

**核验**：`b_path_checks.py` 第 C2 项（d=0，300 例）与 `b_threshold_checks.py` 的恢复项（d=0,…,6，200 例），首段长度与 $d+\lceil X\rceil$ 全部相等。

**对题设 $\lceil(\overline r-\theta)/(\kappa(1-\alpha))\rceil+1$ 的收窄【更正】**：无额外延迟时，紧界是 $\lceil X\rceil$，题设的“+1”偏松一次；“+1”恰好对应 d=1。一般为 $\lceil X\rceil+d$。

### 9.3 这是不是“无法靠参数处理”的缺陷

**结论：在 $\mathrm D_\theta$ 下不是。**
- 首段长度与超额次数都是 $(\overline r-\theta)/\kappa$ 的显式函数。要把首段限制在 $L_{\max}$ 内，取 $\kappa\ge(\overline r-\theta)/\big((1-\alpha)(L_{\max}-d)\big)$，或减小 $\overline r$；幅度直接由 $\overline r$ 控制。
- 对照：W0 的首段有与 κ 无关的结构下界，约为 $w\alpha$ 量级（P1 定理 6.10、R3 表 5.1：稿件参数下 312 至 1892 步）；TB-PI 约为 $w\alpha/2$（R3 命题 R3.2，148 步）。这两者确实无法靠调增益处理，EV 可以。
- EV 唯一的障碍是 7.5 节的物理一致性条件：若用户要求的首段上限 $L_{\max}$ 与设备一步能力、稳态方差上限互相矛盾（$\overline r-\theta>(L_{\max}-d)(c_{\rm dev}-\eta-\bar e)$，或所需 κ 超过稳定与方差给出的上界），则任何 κ 都不能同时满足。这是否发生，取决于实际通道的 $c_{\rm dev}$、$\bar e$、$F'$ 与用户的容许值，本文无法事先确认。
- 温度通道若不满足 $\mathrm D_\theta$（失配无界），任何更新都没有连续违约的上界，这与静默无关。

**与“按暴露时段计 α”的关系**：
- 按全部时隙计 α 时，静默时隙算作合规，EV 在静默期把放松权限积累到 $\overline r$，恢复时一次用出。含首段的每个窗口仍满足 $Y^w\le\alpha+[(\overline r-\theta)/\kappa+(1-\alpha)]/w$（定理 3.7），所以首段处在全时隙口径声明的预算之内。
- 按暴露时段计 α 时，静默时隙不应“挣得”放松。此时首段的全部违约都计入暴露时段，首段就成了语义上的问题，而不仅是参数问题。
- 因此是否需要改动更新律，取决于用户对 α 口径的选择（`窗口风险指标路线_方案与理论.md` §9 的待决事项 1），而不是数学上的必要。

### 9.4 备用（未推荐）：活动门控更新

仅当用户选择“按暴露时段计 α”，或 9.3 节的冲突在实际数据中得到确认时，才考虑下面这一个替代。

**更新**：$r_{t+1}=\Pi\big[r_t+\kappa(\alpha a_t-O_{t+1})\big]$，$a_t\in\{0,1\}$ 在决策时刻 t 可观测（$\mathcal F_t$ 可测）。

**命题 9.4【严格】**：对任意 $a$、$O$ 序列与任意区间，
$$
\sum_{t\in I}(O_t-\alpha a_{t-1})=\frac{r_a-r_b+C_I^+-C_I^-}{\kappa},\qquad
\frac{\sum_{t\in I}O_t}{\sum_{t\in I}a_{t-1}}\le\alpha+\frac{r_a-\underline r+C_I^+}{\kappa\sum_{t\in I}a_{t-1}} .
$$
**证明**：与定理 3.4 相同的望远镜求和，只把 α 换成 $\alpha a_{t-1}$。证毕。`b_path_checks.py` 第 F 项在 60,553 个区间上精确核验。

**语义**：α 变为“暴露时段内的违约率上限（目标）”。$a=0$ 且 $O=0$ 时参数不动，静默期不再把参数推到 $\overline r$。

**$a_t$ 的可观测定义（示例）**：$a_t=\mathbf 1\{G\hat s_{t+1\mid t}\ge g-\delta\}$，即下一步计划状态进入名义界的 δ 邻域；δ 必须事先声明。对储能通道（命题 1.2），$O_{t+1}=1$ 意味着 $Gs_{t+1}=G\hat s_{t+1\mid t}>g+\varepsilon\ge g-\delta$，所以违约必然伴随 $a_t=1$，任何 $\delta\ge-\varepsilon$ 都满足。温度通道需要 $\delta\ge\bar e-\varepsilon$ 才有同样性质。

**代价**：多了一个需要论证的定义 $a_t$；暴露时隙少时，参数在日历时间上响应变慢；证书的分母变成暴露时隙数，需要单独报告。

---

## 10. 多通道

通道 $i=1,2,3$（储能下界、储能上界、温度上界）各有 $(\alpha_i,\kappa_i,\underline r^i,\overline r^i)$，各自按 EV 更新，只使用本通道的 $O^i$。

### 命题 10.1【严格】逐通道恒等式不受耦合影响

对每个 i 与所有区间同时成立 $\bar O^i_I-\alpha_i=(r^i_a-r^i_b+C^{i,+}_I-C^{i,-}_I)/(\kappa_iL)$。

**证明**：$r^i$ 的递推只含 $O^i$，定理 3.4 逐通道适用。通道之间如何通过 MPC 耦合，只影响 $O^i$ 序列本身，而恒等式对任意序列成立。证毕。

### 命题 10.2【严格】并集界；储能上下界互斥时为精确和

令 $U_t=\mathbf 1\{\exists i:O^i_t=1\}$。
1. 对任意区间，
$$
\bar U_I\le\min\Big\{1,\ \sum_i\Big[\alpha_i+\frac{r^i_a-\underline r^i+C^{i,+}_I}{\kappa_iL}\Big]\Big\}.
$$
2. 储能上下界：$E_{\min}-\varepsilon<E_{\max}+\varepsilon$，事件 $\{E<E_{\min}-\varepsilon\}$ 与 $\{E>E_{\max}+\varepsilon\}$ 互斥，$O^1_tO^2_t\equiv0$。所以“任一储能违约”的指示恰为 $O^1_t+O^2_t$，其频率恰为 $\bar O^1_I+\bar O^2_I$，证书是两个通道证书的精确和。
3. 若两个储能通道都满足定理 3.7 的条件，则“任一储能违约”在任意区间内的超预算次数不超过 $\sum_{i=1,2}\big[(\overline r^i-\theta^i)/\kappa_i+(1-\alpha_i)\big]$。

**证明**：逐时刻 $U_t\le\sum_iO^i_t$，求和后代入各通道界。互斥由名义界 $E_{\min}<E_{\max}$ 直接得到。第 3 条是定理 3.7 相加。证毕。

**补充**：储能两侧违约互斥，所以同一步至多一侧收紧（命题 7.1 的说明）。

### 定理 10.3【条件：确定性均值代理】线性化耦合稳定性

**代理**：$\bar r_{t+1}=\bar r_t+K\big(\alpha-F(\bar r_t)\big)$，$K=\mathrm{diag}(\kappa_i)>0$，$F:\mathbb R^n\to[0,1]^n$ 为各通道的条件违约概率，在内点平衡点 $r^\*$（$F(r^\*)=\alpha$，投影不激活）附近 $C^1$，$J=DF(r^\*)$ 为风险响应 Jacobian。

**结论**：
1. 线性化 $x_{t+1}=(I-KJ)x_t$ 渐近稳定，当且仅当 KJ 的每个特征值 λ 都满足 $|1-\lambda|<1$；写成 $\lambda=a+ib$，即 $a^2+b^2<2a$。
2. 此时 $r^\*$ 是非线性代理的局部指数稳定平衡点。

**证明**：
1. $\det\big(zI-(I-KJ)\big)=\det\big(KJ-(1-z)I\big)$，所以 z 是 $I-KJ$ 的特征值当且仅当 $1-z$ 是 KJ 的特征值，重数相同。
2. 线性系统 $x_{t+1}=Ax_t$ 对所有初值收敛到 0，当且仅当谱半径 $\rho(A)<1$（Jordan 标准形，或 Gelfand 公式 $\|A^t\|^{1/t}\to\rho(A)$）。合并两步得结论 1。
3. 若 $\rho(I-KJ)<1$，对任意 $\epsilon>0$ 存在诱导范数使 $\|I-KJ\|\le\rho+\epsilon<1$。$\Phi(r)=r+K(\alpha-F(r))$ 在 $r^\*$ 附近 $C^1$，由连续性，在某邻域内 $\|D\Phi\|\le q<1$，由中值不等式 Φ 在该邻域内是压缩映射。证毕。

`b_stochastic_checks.py`：2000 个随机 (J, K) 上，$\rho(I-KJ)$ 与 $\max|1-\lambda(KJ)|$ 的差不超过 $1.1\times10^{-14}$。

### 推论 10.4【条件：同上】需要的额外条件与反例

1. **解耦**：J 为对角阵时，条件化为 $0<\kappa_iJ_{ii}<2$，即逐通道的标量条件。
2. **逐通道稳定不够**：$J=\begin{pmatrix}s&g\-g&s\end{pmatrix}$，$K=\kappa I$，特征值 $s\pm ig$。条件为 $\kappa<2s/(s^2+g^2)$。取 s=1、g=3、κ=1.5：每个通道 $\kappa J_{ii}=1.5<2$，但 $\rho(I-\kappa J)=4.53$，不稳定。非对称耦合越强，允许的增益越小。
3. **任何增益都救不了的情形**：J 有实部非正的特征值时（例如强对称耦合 $s=3$、$g=4$，特征值 −1 与 7），对所有 $\kappa>0$ 都不稳定（κ=0.001、0.01、0.1 时谱半径 1.001、1.01、1.1）。这是联合目标的鞍点，与更新律无关（R3 §6.2）。
4. **异质增益需要结构条件**：即使 J 的特征值实部全为正、对角元全为正，某些正对角 K 仍可能使 KJ 出现负实部特征值，此时把增益整体缩小也无效。数值反例（3×3）：
$$
J=\begin{pmatrix}2.602&-0.256&-1.789\-0.587&1.648&0.227\2.296&1.056&0.114\end{pmatrix},\quad
\mathrm{eig}(J)=\{1.17\pm1.54i,\ 2.02\},
$$
取 $D=\mathrm{diag}(0.0448,84.52,17.99)$，$\mathrm{eig}(DJ)$ 含 $-0.227\pm1.895i$；$\epsilon=10^{-4},10^{-3},10^{-2}$ 时 $\rho(I-\epsilon DJ)=1.00002,1.0002,1.0024$。
5. **可检验的充分条件**：
   - (a) **Gershgorin**：若对每个 i，$J_{ii}>\sum_{j\ne i}|J_{ij}|$ 且 $\kappa_i\big(J_{ii}+\sum_{j\ne i}|J_{ij}|\big)<2$，则稳定。证明：KJ 第 i 行的 Gershgorin 圆心 $c_i=\kappa_iJ_{ii}$、半径 $R_i=\kappa_i\sum_{j\ne i}|J_{ij}|$；闭圆盘含于开圆盘 $|1-\lambda|<1$ 当且仅当 $|c_i-1|+R_i<1$。$c_i\le1$ 时等价于 $R_i<c_i$；$c_i>1$ 时等价于 $c_i+R_i<2$（此时 $R_i<c_i$ 自动成立）。所有特征值都在这些圆盘的并中。证毕。数值：20,000 个随机实例中该条件成立 1026 次，全部稳定。
   - (b) **对角稳定**：若存在正对角 P 使 $PJ+J^\top P\succ0$，则对任意正对角 D，DJ 的特征值实部全为正；从而对任意增益比例，整体尺度足够小时稳定。证明：取 $S=PD^{-1}\succ0$，$S(DJ)+(DJ)^\top S=PJ+J^\top P\succ0$，由 Lyapunov 定理（若 $S\succ0$ 且 $SA+A^\top S\succ0$，则 A 的特征值实部全为正）得证；再由 $|1-\epsilon\lambda|^2=1-2\epsilon\,\mathrm{Re}\lambda+\epsilon^2|\lambda|^2<1$ 对小 ε 成立。证毕。$J+J^\top\succ0$ 是 $P=I$ 的特例。

### 10.5 已建立与尚未建立的内容

| 内容 | 状态 |
|---|---|
| 逐通道恒等式、并集界、储能互斥的精确和 | 【严格】已建立，对实际日志成立 |
| 耦合线性化稳定的充要条件与充分条件 | 【条件】只对确定性均值代理、内点平衡、光滑 F 成立 |
| 实际 MPC 的 F 是否光滑、J 是否满足上述条件 | **未建立**；需要从日志或阶跃试验估计 J |
| 每个时刻的联合条件违约概率 $\mathbb P(\exists i:O^i_{t+1}=1\mid\mathcal F_t)$ 的保证 | **未建立**；只有第 11 节的“时段平均”形式 |
| 随机闭环（非均值代理）的联合收敛 | **未建立** |

---

## 11. 概率假设下的性质

### 11.0 附加代理假设（必须明写）

下面 11.2–11.5 使用**静态风险代理**：
$$
\mathbb P(O_{t+1}=1\mid\mathcal F_t)=F(r_t),
$$
即条件违约概率只通过当前参数依赖历史，并且不随时间变化（11.4 允许漂移时单独说明）。这是**附加假设**，不是从稿件模型推出的：
- 储能通道在决策信息下 $O_{t+1}$ 是确定的，条件概率只取 0 或 1，并且还依赖荷电状态、电价、预测，不只依赖 $r_t$；
- 温度通道的违约来自模型失配，失配的分布与热惯性使风险带有状态记忆；
- 因此 11.2–11.5 的结论只适用于这个代理，是理解增益作用的工具，不是实际 MPC 的保证。

11.1 不需要这个代理。

### 命题 11.1【严格（任意信息流）】平均条件违约概率的鞅界

**设定**：$\mathcal F_t$ 为任意信息流，使 $O_t,r_t$ 为 $\mathcal F_t$ 可测；$p_t:=\mathbb P(O_t=1\mid\mathcal F_{t-1})$，可以任意依赖历史。区间 $I=(a,b]$ 的 a 与 L **预先固定**，不能从数据中挑选。

**结论**：以至少 $1-\delta$ 的概率，
$$
\frac1L\sum_{t\in I}p_t\le\bar O_I+\sqrt{\frac{\log(1/\delta)}{2L}}\le\alpha+\frac{r_a-\underline r+C_I^+}{\kappa L}+\sqrt{\frac{\log(1/\delta)}{2L}} .
$$
对 K 个预先指定的区间（可以重叠）同时成立时，把 $\log(1/\delta)$ 换成 $\log(K/\delta)$；对长度为 w 的全部 T 个滑动窗口，用 $\log(T/\delta)$。

**证明**：$D_t=p_t-O_t$ 是鞅差，条件取值区间长度为 1。条件 Hoeffding 引理给出 $\mathbb E[e^{\lambda D_t}\mid\mathcal F_{t-1}]\le e^{\lambda^2/8}$；逐层取条件期望得 $\mathbb Ee^{\lambda\sum_ID_t}\le e^{\lambda^2L/8}$；指数 Markov 不等式并取 $\lambda=4x$，得 $\mathbb P(\sum_ID_t\ge Lx)\le e^{-2Lx^2}$。第二个不等号由定理 3.4 逐路径成立，所以与鞅事件同时成立。多个区间用并集界。证毕。

**说明**：
- 不需要独立性或平稳性；重叠窗口通过并集界处理，**不能**把重叠窗口中的条目当作独立样本。数值：α=0.1 的 logistic 代理、L=500、δ=0.05，正确半径 0.055 的超出频率为 0；若把样本数虚增 50 倍，半径 0.0077，超出频率为 0.28，远大于 δ。
- 储能通道在决策信息下 $p_t=O_t$，此界退化为恒等式，没有额外信息；它主要对温度通道有意义。
- 违约率小时 Hoeffding 偏松。可改用 Freedman 不等式（Freedman 1975，Ann. Probab. 3:100–118）得到依赖条件方差的半径；本文未推导其在此处的具体形式。

### 命题 11.2【条件：静态代理 + 单调回复】均方界（独立复核 merged 定理）

**假设**：存在 $r_\*\in[\underline r,\overline r]$、$\mu>0$，使 $(r-r_\*)(F(r)-\alpha)\ge\mu(r-r_\*)^2$ 对所有 $r\in[\underline r,\overline r]$ 成立，且 $0<2\kappa\mu\le1$。记 $v=\beta^2$。

**结论**：$e_t:=\mathbb E(r_t-r_\*)^2\le(1-2\kappa\mu)^te_0+\kappa v/(2\mu)$；更精确的末项为 $\kappa v[1-(1-2\kappa\mu)^t]/(2\mu)$。

**证明**：
1. $r_\*$ 在区间内，投影不增加到它的距离：$(r_{t+1}-r_\*)^2\le(v_{t+1}-r_\*)^2$。
2. 展开 $(r_t-r_\*+\kappa(\alpha-O_{t+1}))^2$ 并取条件期望：交叉项为 $-2\kappa(r_t-r_\*)(F(r_t)-\alpha)\le-2\kappa\mu(r_t-r_\*)^2$。
3. 平方项 $\kappa^2\mathbb E[(\alpha-O)^2\mid\mathcal F_t]=\kappa^2[\alpha^2+F(1-2\alpha)]$，它关于 $F\in[0,1]$ 是线性的，最大值在端点，为 $\kappa^2\max(\alpha^2,(1-\alpha)^2)=\kappa^2v$。
4. 得 $e_{t+1}\le(1-2\kappa\mu)e_t+\kappa^2v$，迭代。证毕。

**复核结论**：merged 定理正确。v 控制的是完整增量的二阶矩（含漂移项的平方），不漏项。“F 单调”不够，必须是全区间的定量回复条件。数值（logistic，κ=0.2，μ=0.0499）：4000 条路径的经验均方在全部 3000 步都低于界（界很松，稳态界 1.62）。

### 命题 11.3【条件：静态代理 + 线性化 + 常数条件方差】固定增益的稳态波动

**模型**：内点 $r_\*$，$F(r_\*)=\alpha$，$F'=F'(r_\*)>0$，不截断。令 $x_t=r_t-r_\*$，$\xi_{t+1}=O_{t+1}-F(r_t)$，线性化为
$$
x_{t+1}=(1-\kappa F')x_t-\kappa\xi_{t+1},\qquad \mathbb E[\xi_{t+1}\mid\mathcal F_t]=0,\ \ \mathbb E[\xi_{t+1}^2\mid\mathcal F_t]=\sigma^2:=\alpha(1-\alpha).
$$

**结论**：若 $0<\kappa F'<2$，
1. 参数的稳态方差精确为
$$
V=\frac{\kappa\sigma^2}{F'(2-\kappa F')},
$$
一阶自相关为 $1-\kappa F'$。
2. 窗口率的稳态方差精确为
$$
\mathrm{Var}(Y^w)=\frac{2V\big(1-(1-\kappa F')^w\big)}{(\kappa w)^2}=\frac{2\sigma^2\big(1-(1-\kappa F')^w\big)}{\kappa F'(2-\kappa F')\,w^2}.
$$
当 $\kappa F'w\gg1$ 时约为 $\sigma^2/(\kappa F'w^2)$，比独立 Bernoulli 的 $\sigma^2/w$ 小 $\kappa F'w$ 倍；当 $\kappa F'w\ll1$ 时回到 $\sigma^2/w$。

**证明**：
1. 由鞅差性质交叉项为零，$V_{t+1}=(1-\kappa F')^2V_t+\kappa^2\sigma^2$。$|1-\kappa F'|<1$ 时收敛到不动点 $V=\kappa^2\sigma^2/(1-(1-\kappa F')^2)$，化简即得。自相关由 $\mathbb E[x_{t+1}x_t]=(1-\kappa F')V$。
2. 不截断时由定理 3.4，$Y^w_t-\alpha=(x_{t-w}-x_t)/(\kappa w)$。稳态下 $\mathrm{Cov}(x_t,x_{t-w})=(1-\kappa F')^wV$，故 $\mathrm{Var}(x_t-x_{t-w})=2V(1-(1-\kappa F')^w)$。证毕。

**数值**（logistic 代理，α=0.1，$F'=0.27$，w=96，400 条路径）：

| κ | 参数方差：模拟 / 公式 | 窗口率方差：模拟 / 公式 / Bernoulli |
|---|---|---|
| 0.02 | 0.00336 / 0.00334 | 0.000737 / 0.000735 / 0.000938 |
| 0.05 | 0.00845 / 0.00839 | 0.000530 / 0.000531 / 0.000938 |
| 0.2 | 0.0353 / 0.0343 | 0.000190 / 0.000185 / 0.000938 |

**工程意义**：
- 增大 κ 会**减小**窗口率的方差（约与 1/κ 成正比），同时**增大**参数本身的方差（约与 κ 成正比）。前者关系到考核指标的平稳，后者关系到边界抖动、违约幅度和成本。这是一个明确的权衡，应由 7.5 节的 C3 决定，而不是一味追求小增益。
- 这也精确说明了 merged §3.2 的提醒：负相关反馈可以使窗口率比独立 Bernoulli 更集中。
- 这只是线性化代理；截断、阈值型响应或状态记忆都会改变结论。

### 命题 11.4 工况漂移下的跟踪

**(a)【严格】违约率偏差由参数的净移动量决定。** 对任意区间，
$$
\bar O_I-\alpha=-\frac{r_b-r_a}{\kappa L}+\frac{C_I^+-C_I^-}{\kappa L}.
$$
若平衡点以速率 ρ 漂移，而参数平均跟上（$r_b-r_a\approx\rho L$，不截断），则违约率偏差为 $-\rho/\kappa$，与响应模型无关。风险变高的方向（例如夏季冷却能力下降，温度通道的 $r^\*$ 下降，$\rho<0$）对应违约率**高于** α，超出 $|\rho|/\kappa$。

**(b)【条件：时变静态代理，回复条件对每个 $r^\*_t$ 以同一 μ 成立，$r^\*_t\in[\underline r,\overline r]$，$|r^\*_{t+1}-r^\*_t|\le\rho$，$2\kappa\mu\le1$】参数的均方根跟踪误差。** 记 $D_t=\sqrt{\mathbb E(r_t-r^\*_t)^2}$，则
$$
\limsup_{t\to\infty}D_t\le\frac{\rho}{\kappa\mu}+\sqrt{\frac{\kappa v}{2\mu}} .
$$

**证明**：
1. 以 $r^\*_t$ 为目标套用命题 11.2 的一步不等式：$\mathbb E(r_{t+1}-r^\*_t)^2\le(1-2\kappa\mu)D_t^2+\kappa^2v$。
2. 由 $L^2$ 中的 Minkowski 不等式，$D_{t+1}\le\sqrt{(1-2\kappa\mu)D_t^2+\kappa^2v}+\rho=:\varphi(D_t)$。
3. φ 单调增，且 $\varphi'(D)\le\sqrt{1-2\kappa\mu}<1$，是压缩映射。归纳得 $D_t\le\varphi^{(t)}(D_0)\to D^\*$，$D^\*$ 为唯一不动点。
4. 解 $D-\rho=\sqrt{(1-2\kappa\mu)D^2+\kappa^2v}$，得 $D^\*=\big[\rho+\sqrt{\rho^2(1-2\kappa\mu)+2\kappa^3\mu v}\big]/(2\kappa\mu)\le\rho/(\kappa\mu)+\sqrt{\kappa v/(2\mu)}$。证毕。

**两项权衡**：漂移项 $\rho/(\kappa\mu)$ 随 κ 减小，噪声项随 κ 增大。最优 κ 约为 $(8\rho^2/(\mu v))^{1/3}$，误差阶为 $\rho^{1/3}$。递减增益下漂移项 $\rho/(\kappa_t\mu)\to\infty$（注 5.4）。

**(c)【条件：线性化】平均滞后。** 线性化给出参数平均滞后 $\rho/(\kappa F')$；F 有曲率时再加二阶偏移 $F''V/(2F')$。

**数值**（logistic，α=0.1，κ=0.05，ρ=2×10⁻⁴）：违约率 0.0960，与 $\alpha-\rho/\kappa=0.096$ 一致；平均滞后 0.026，线性化 0.0148 加曲率修正 0.0100 为 0.0249；均方根误差 0.096，低于 (b) 的界 0.717（界偏松，因为全局 μ 很小）。

### 命题 11.5【条件：线性化标量代理，额外延迟 d】延迟下的线性稳定条件

**模型**：$x_{t+1}=x_t-g\,x_{t-d}$，$g=\kappa F'>0$。d=0 对应命题 1.1 中固有的一步反馈；d 为额外延迟。

**结论**：渐近稳定当且仅当
$$
0<g<2\sin\frac{\pi}{2(2d+1)} .
$$
d=0 时为 $g<2$；d=1、2、4 时分别为 1、0.618、0.347。

**证明**：特征多项式 $P_g(z)=z^{d+1}-z^d+g$。
1. **小 g 时全部根在圆内**。g=0 时根为单根 1 与 d 重根 0。$\partial_zP(1,0)=1\ne0$，由隐函数定理 $z(g)=1-g+O(g^2)\in(0,1)$；靠近 0 的根满足 $|z|^d|z-1|=g$，模长约 $g^{1/d}$。由根对系数的连续性，小正 g 时全部 $d+1$ 个根在单位圆内。
2. **单位圆上的根**。$z=e^{i\omega}$ 为根当且仅当 $g=z^d(1-z)=2\sin\frac\omega2\,e^{i((d+\frac12)\omega-\frac\pi2)}$。$\omega\in(0,2\pi)$ 时 $\sin\frac\omega2>0$，g 为正实数当且仅当 $(d+\tfrac12)\omega-\tfrac\pi2\in2\pi\mathbb Z$，即 $\omega_k=\frac{(4k+1)\pi}{2d+1}$，$k=0,\dots,d$，对应 $g_k=2\sin\frac{(4k+1)\pi}{2(2d+1)}$。最小值在 $k=0$（与 $k=d$ 为共轭对）取到，为 $g_c=2\sin\frac{\pi}{2(2d+1)}$。$\omega=0$ 只对应 g=0。
3. $0<g<g_c$ 时圆上无根，根连续变化，圆内根数保持为 $d+1$，故稳定。
4. **穿越方向**。$\partial_zP=z^{d-1}((d+1)z-d)$ 在单位圆上不为零，穿越根是单根。利用 $z^d=g/(1-z)$，
$$
\frac{d\log|z|}{dg}=\mathrm{Re}\Big(\frac{1}{z}\frac{dz}{dg}\Big)=-\frac1g\,\mathrm{Re}\frac{1-z}{(d+1)z-d},\qquad
\mathrm{Re}\frac{1-z}{(d+1)z-d}=\frac{(2d+1)(\cos\omega-1)}{|(d+1)z-d|^2}<0 .
$$
所以每次穿越都是从圆内到圆外，圆内根数关于 g 不增，并在 $g_c$ 处减少，$g>g_c$ 时不稳定。证毕。

**出处**：这是时滞差分方程的经典结果。Levin & May（1976，Theoretical Population Biology 9:178–187）给出 $x_{t+1}=x_t-ax_{t-k}$ 的稳定条件 $0<a<2\cos\frac{k\pi}{2k+1}$，与上式相同，因为 $\cos\frac{k\pi}{2k+1}=\sin\frac{\pi}{2(2k+1)}$；一般形式 $x_{n+1}-ax_n+bx_{n-k}=0$ 见 Kuruklis（1994，J. Math. Anal. Appl. 188:719–731）。上面的证明是独立写出的。

**数值**：d=0,…,8，np.roots 二分得到的临界值与公式之差不超过 $1.7\times10^{-15}$，且临界值之后在网格上全部不稳定。

**工程意义**：温度通道从参数到违约可能有一步以上的额外延迟，C3 应使用 $2\sin\frac{\pi}{2(2d+1)}$ 而不是 2（R3 §3.3 对 TB-PI 已指出，这里对 EV 给出证明）。阈值型通道没有 $F'$，延迟的影响改由第 8 节的下侧余量处理。

---

## 12. 汇总

### 12.1 严格证明的命题（逐路径，不需要概率模型或 MPC 性质）

| 编号 | 内容 |
|---|---|
| 命题 1.1 | EV 与稿件 Algorithm 1 的时序逐步一致；反馈固有延迟为一步 |
| 命题 2.2、2.3，推论 2.4(a) | “低于 α”的分解与上侧饱和比例 $U_I$；非对称增益恰好等价于目标 $p^\*$、增益 K 的对称 EV；目标加裕度给出上限语义 |
| 命题 3.1–3.3 | 有界、单步界、零初值不吸收（G 在 0 处吸收且符号锁定）、一次相反观测即离开端点 |
| 定理 3.4，推论 3.5，注 3.6 | 任意区间恒等式与单侧界（独立复核 merged 定理 1），每个滚动窗口的残差界；持续违约时 $C^+$ 必须保留 |
| 命题 3.8 | W0 的无首尾项恒等式针对窗口率时间平均；反例说明它对单个盒形窗口的最坏控制弱于 EV |
| 定理 4.1–4.5 | EV 与 W0 响应与 $t_0$ 无关；G 的参数响应时间 $\ge\sqrt{2\lambda(\gamma-\bar b)t_0}-1$ 且阶为 $\Theta(\sqrt{t_0})$；G 的信号按 $t_0$ 线性变慢；W0 相对 EV 的等效延迟为 $(w-1)/2+P_{t_0}/(1-\alpha)$ |
| 定理 5.1，推论 5.2 | 递减增益的 Abel 恒等式，残差按 $D/\kappa_b$ 增长，且可达 |
| 命题 6.1、6.2(a)、6.3(a) | 初始化误差除以 κ 等于暂态超额违约数；保守初始化使证书初始松弛为零；暂态步数下界 |
| 命题 9.1、9.4 | 静默把参数推到 $\overline r$；门控更新的恒等式 |
| 命题 10.1、10.2 | 逐通道恒等式；并集界；储能上下界互斥时为精确和 |
| 命题 11.1 | 平均条件违约概率的鞅界（任意历史依赖，预先固定区间，重叠窗口用并集界） |
| 命题 11.4(a) | 平衡点漂移时，违约率偏差等于参数净移动量除以 κL，稳态为 $-\rho/\kappa$ |

### 12.2 条件性命题（需要写明的额外联系）

| 额外联系 | 命题 | 结论要点 |
|---|---|---|
| 单侧阈值支配 $\mathrm D_\theta$：$O_{t+1}\le\mathbf 1\{r_t>\theta\}$。储能由“第一步硬约束可行 + 储能指令不改 + 储能模型精确”得到，$\theta=\varepsilon-\eta$；温度需要失配上界 | 命题 1.2，定理 3.7，推论 2.4(b)，命题 6.2(b)(c)，定理 9.2 | 不含残差的上侧证书：任意区间超额 $\le(\overline r-\theta)/\kappa+(1-\alpha)$；参数不低于 $m^\*=\theta-\kappa(1-\alpha)$；首段连续违约 $\le\lceil(\overline r-\theta)/(\kappa(1-\alpha))\rceil+d$；幅度 $\le\overline r+\eta$ |
| 精确阈值 | 定理 8.1，命题 7.4(b) | 复核 merged 定理 4；“对所有初值都不截断” ⇔ $\underline r\le\theta-\kappa(1-\alpha)$ |
| 精确阈值 + 额外延迟 d | 定理 8.2，命题 8.3 | 不变区间 $J_d$ 长 $(d+1)\kappa$；计数误差 $\le d+1$；最长违约 $\le d+\lceil(d+1)\alpha/(1-\alpha)\rceil$；稳态精确余量 $\kappa[(d+1)(1-\alpha)-d\alpha]$ |
| 容差不一致 | 命题 8.4 | $\theta<\underline r$ 时永久违约；用 $\varepsilon\ge\eta$ 或 $\underline r\le-\eta-\kappa(1-\alpha)$ 修正 |
| 设备能力与失配界 | 命题 7.1–7.3，7.5 节 | 收紧步 $\kappa(1-\alpha)+\eta+\bar e\le c_{\rm dev}$；$\overline r$ 由物理界定并决定幅度；三方约束与物理一致性条件 |
| 静态代理 $\mathbb P(O_{t+1}=1\mid\mathcal F_t)=F(r_t)$ | 命题 5.3、6.3(c)、7.4(a)、11.2–11.5 | 递减增益均方收敛；均方界（复核 merged）；稳态方差 $\kappa\sigma^2/(F'(2-\kappa F'))$ 与窗口率方差；漂移 RMS 界 $\rho/(\kappa\mu)+\sqrt{\kappa v/(2\mu)}$；延迟稳定 $\kappa F'<2\sin\frac{\pi}{2(2d+1)}$ |
| 确定性均值代理（多通道） | 定理 10.3，推论 10.4 | 稳定 ⇔ KJ 的特征值都在 $|1-\lambda|<1$ 内；需要 Gershgorin 或对角稳定等结构条件；实际联合概率保证尚未建立 |

### 12.3 被推翻或收窄的说法

1. 【收窄】“储能下界必须 $\le-\kappa(1-\alpha)$”：精确阈值下，它是“对所有初值都不截断”的充要条件；在更一般的 $\mathrm D_\theta$ 下只是充分条件，而且比 $m^\*$ 更负的下界永远不会被触及；对特定轨道不必要。
2. 【更正】题设的首段上界 $\lceil(\overline r-\theta)/(\kappa(1-\alpha))\rceil+1$：无额外延迟时紧界是 $\lceil\cdot\rceil$；一般为 $\lceil\cdot\rceil+d$。
3. 【收窄】题设的延迟余量“约 $(d+1)\kappa(1-\alpha)$”：对所有历史充分；稳态循环中精确值少 $d\kappa\alpha$（α≤1/(d+2)）。
4. 【推翻】“W0 恒等式无首尾项，所以逐窗口控制更强”：$w=10$ 的反例中 W0 无截断却出现全违约窗口，而 EV 型界为 11/20。
5. 【收窄】merged §3.2 的单步条件 $\kappa\max(\alpha,1-\alpha)\le d$：可行性只需约束收紧步 $\kappa(1-\alpha)$，但必须加上求解容差 η 与失配 $\bar e$。
6. 【推翻】“逐通道满足 $\kappa_iJ_{ii}<2$ 即多通道稳定”：非对称耦合反例谱半径 4.53；异质增益下即使 J 正稳定，也可能任何小增益都不稳定（3×3 反例）。
7. 【收窄】线性化的漂移滞后 $\rho/(\kappa F')$ 在 F 有曲率时低估参数滞后；但违约率偏差 $-\rho/\kappa$ 是精确的、与模型无关的。
8. 【更正】稿件第 444 行的 $\mathcal F_t$ 未包含当期测量，与 Algorithm 1 不一致；第 399、536 行的 $h\le0$ 与负下界冲突。
9. 【结论】恢复后的集中违约在 $\mathrm D_\theta$ 下可以由 $\overline r$ 与 κ 控制，不属于“无法靠参数处理”的缺陷（与 W0、TB-PI 的结构下界不同）；门控更新只在用户选择“按暴露时段计 α”或 7.5 节的物理一致性条件在实际数据中不满足时才需要。
10. 【确认并定量】用户的原始动机：G 的参数时间尺度按 $\sqrt{t_0}$、信号按 $t_0$ 增长；稿件参数下运行一年后参数半衰约需 7.7 天，十年后约需 24 天；EV 与 $t_0$ 无关。

### 12.4 脚本索引（`B_scripts/`，只用 numpy、fractions 与纯 Python）

| 脚本 | 内容 | 结果文件 |
|---|---|---|
| `b_path_checks.py` | 有理数精确：恒等式与单侧界（90,294 个区间）、非对称增益等价（300 条）、$\mathrm D_\theta$ 证书（309,720 个区间）、首段紧性、均匀不截断必要性、W0 反例、门控恒等式（60,553 个区间） | `b_path_checks_output.txt` |
| `b_response_checks.py` | G 的 $\sqrt{t_0}$ 放慢与上下界、信号的 $t_0$ 线性放慢、W0 延迟恒等式（120 例精确） | `b_response_checks_output.txt` |
| `b_gain_checks.py` | 递减增益 Abel 恒等式与界（109,301 个区间）、切换后违约数随切换时刻增长 | `b_gain_checks_output.txt` |
| `b_threshold_checks.py` | merged 定理 4 复核（549,000 个区间）、延迟不变区间与计数/连续违约界（777,600 个区间）、精确余量扫描、无延迟余量下的失效、容差永久违约、恢复首段 $\lceil X\rceil+d$ | `b_threshold_checks_output.txt` |
| `b_stochastic_checks.py` | 耦合稳定的特征值映射、反例与 Gershgorin 条件、D 稳定反例、延迟临界增益、稳态方差与窗口率方差、均方界、漂移跟踪、鞅界的超出频率 | `b_stochastic_checks_output.txt` |

运行方式：在 `B_scripts/` 下执行 `python <脚本名>`，每个脚本约 15 秒以内。所有数值只是对证明的辅助核验，不是数据中心实验证据。
