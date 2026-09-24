# Ghosh et al. (TCST 2025) 理论边界审读笔记

对象：A. Ghosh et al., "Adaptive Relaxation-Based Nonconservative Chance Constrained Stochastic MPC," IEEE TCST 33(5), 2025。
主材料：`Adaptive_Relaxation-Based_Nonconservative_Chance_Constrained_Stochastic_MPC.md`（下称 md，行号均指此文件）。
校对：公式歧义处对照了 `reference_paper/ghosh2025_adaptive_relaxation_ccsmpc_assets/full.md`，并用 PyMuPDF 渲染 `..._origin.pdf` 第 6–10、13–14 页（期刊页 1549–1553、1556）核对原图。

标记约定：
- **[原文]**：原文确实写了的内容，附引文与行号。
- **[推断]**：本审稿人的推导或判断，原文没有写。
- 符号说明：任务中所说的“违约指示 O”在原文里记作 $V_i$，“χ”在原文里记作 $\gamma_i$（原文没有出现 χ）。下文统一使用原文符号。

---

## 0. OCR 勘误（以 PDF 原图为准）

| md 行 | OCR 文本 | PDF 原文 |
|---|---|---|
| 391 | $\frac{\alpha_i-\bar Y_i(t)+\frac{-2Y_i(t)-1}{2(t+1)}}{\nu_i}>-1$ | $\frac{\alpha_i-Y_i(t)+\frac{2Y_i(t)-1}{2(t+1)}}{\gamma_i}>-1,\ \forall t$ |
| 473 | $\kappa'$ 的括号 OCR 成 $\lceil\cdot\rceil$ | 闭区间：$\kappa'(\alpha_i,t):=\Big[\frac{\alpha_i-\frac{1}{2(t+1)}}{1-\frac{1}{2(t+1)}},\ \frac{\alpha_i}{1-\frac{1}{2(t+1)}}\Big]$ |
| 393 | Theorem 1 陈述有碎片 | 见 §4.1 的完整引文（PDF p.1550） |
| 695–699 | SMPC Lit 更新律乱码 | $\tilde h_i(t)=\tilde h_i(t-1)\Big[1-\frac{\alpha_i-Y_i(t)+\frac{2\alpha_i-1}{2t}}{\gamma_i}\Big]$，$\tilde h_i(t)>0$，$\tilde h(0)=-h(0)$ |
| 489 | Lemma 1 证明 Case 1 乱码 | $p_i^*(t+1)=1$ 时 $\Delta_i^*(t)=\big|\alpha_i-t\frac{Y_i(t)}{t+1}-\frac{1}{t+1}\big|-|\alpha_i-Y_i(t)|$ |

---

## 1. 系统与约束设定

### 1.1 状态方程与不确定性
- **[原文]** 式 (1)（md 143–145）：$x(t+1)=Ax(t)+Bu(t)+Ew(t)$，这是离散 LTI 系统，扰动是**加性**的。
- **[原文]** Assumption 1（md 149）：“(a) At each time t, a measurement of the state is available. (b) The set of admissible control inputs U and states X are polytopes containing the origin.”
- **[原文]** Assumption 2（md 151）：“The set of uncertainties W is bounded and contains the origin.”
- **[原文]** 滤子（md 153）：$\mathcal F_t=\sigma(\{w(s)\}:s<t)$；III.D 中又写作 $\mathcal F_t=\sigma(\{w(0),\dots,w(t-1)\})$（md 325）。
- **[原文]** 分布假设：摘要（md 5）写“with unknown uncertainty distribution, statistics, or samples”；Contribution 3（md 107）写“does not require either any a priori assumption about the probability distribution ... or sample uncertainties from historical data”。
- **[推断]** 全文**没有**假设 $w$ 独立同分布、平稳或遍历，也没有给出条件分布的任何性质。唯一的假设是 $W$ 有界。
- **[原文]** 耦合等式 (3)（md 161–165）：$Mu(t)=c(t)+Fw(t)$。“if (3) is considered ... the $Ew(t)$ term in the RHS of (1) is dropped as the $Fw(t)$ term in the RHS of (3) accommodates the uncertainty”。脚注 2（md 179）说明 $E$ 仍用于后处理中分配控制输入。
- **[推断]** 算例中 $c(t)=\mathrm{PV}^f(t)-L^f(t)$（md 655）是外生的随机预测，但它不在 $\sigma(w)$ 生成的滤子中。严格说滤子应当把预测也包括进去，这是一个小的形式疏漏。

### 1.2 机会约束形式
- **[原文]** 式 (4)（md 167–171）：$\mathbb P[Gx(t)\le g]\ge \bar 1-\bar\alpha\ \forall t$，其中 $\alpha_i\in(0,0.5)$，这是**逐点**（pointwise-in-time）形式。个体形式为 $\mathbb P[G_ix\le g_i]\ge 1-\alpha_i$；联合形式（JCC，md 173–175）为 $\mathbb P[G_1x\le g_1\wedge\dots\wedge G_rx\le g_r]\ge 1-\alpha$。
- **[原文]** 重新解释（md 177）：“we reinterpret the maximum probability of violation of state constraints pointwise-in-time given by the chance constraints (4) as the maximum time average of state constraint violations in closed-loop”。也就是说，实际的控制目标是**闭环时间平均违约率**，不再是逐点概率。
- **[原文]** 算例（md 657）采用 JCC：$G=[1\ \ -1]^\top$，$g=[\mathrm{SOC_{max}}\ \ -\mathrm{SOC_{min}}]^\top$，并取 $h_1(t)=h_2(t)$。
- **[推断]** $\alpha_i<0.5$ 在证明中是实质性条件：它保证下文的 $b<\alpha$（见 §3.2），不只是为了记号方便。

### 1.3 松弛参数 h 的定义、符号与作用位置
- **[原文]** 对照的收紧式 (9b)（md 233–237）：$Gx(t+k|t)\le g-\tilde h(t+k|t)$，其中 $\tilde h_i>0$。
- **[原文]** 松弛式 (10)（md 243–247）：$Gx(t+k|t)\le g-h(t),\ \forall k\in\mathbb N_1^N$，名义预测取 $\mathbf w(t)=\mathbf 0$。“$h\in\mathbb R^r$ is the adaptive relaxing parameter with $h_i<0$ ... the sign of $h_i$ in (10) is opposite to $\tilde h_i$ in (9b). ... decreasing h(t) in (10) expands the feasible state set ... while increasing h(t) contracts the feasible state set”。
- **[原文]** 预测域内保持不变（md 249–251）：$\mathbf h(t):=(h(t),h(t),\dots,h(t))^\top\in\mathbb R^{Nr}$。同一个 $h(t)$ 在整个预测域内复制使用。
- **[原文]** 进入 MPC 的方式是 (11e)（md 273–277）：$\mathbf G\mathbf x(t+1)\le\mathbf g-\mathbf h(t)$，只作用于 $k=1..N$，不作用于 $k=0$。原文：“not for the present state corresponding to k = 0 to allow for the present state to be outside of the feasible state set”。$h$ 只作用在**状态约束**上，输入约束 (11c) 是硬约束，不受影响。
- **[原文]** Assumption 3（md 279–289）：(a) 从任意 $x(t)$ 出发，都存在满足 $Su\le s$、$Mu=c$ 的 $u(t|t)$，使 $G(Ax(t)+Bu(t|t))\le g-h(t)$；(b) 系统一步可控。Remark 2（md 295）：(b) 意味着 $n\le m$ 且 $B$ 行满秩。Appendix (A.1)–(A.5)（md 799–821）用可行性问题 $f^*=\min 0$ 检验 3(a)。
- **[原文]** Assumption 4（md 297）：“$h_i(t):=h_i(t-1)[1+K_i(t)]$, where $K_i(t)>-1,\ \forall t$ ensures $h_i(t)<0,\ \forall t$”。
- **[原文]** 算例（md 663）对 $h$ 做物理截断：$h_1(t)=\max(\mathrm{SOC_{max}}-1,h_1(t))$，$h_2(t)=\max(-\mathrm{SOC_{min}},h_2(t))$。作者说明这一截断在实际运行中从未触发，原因是 $\gamma_i$ 取得很大，且 $h(0)$ 远离物理限值。

---

## 2. 违约指示 $V$ 与累计平均 $Y(t)$：定义与时序

- **[原文]** (12a)（md 307–309）：$V_i(t+1):=1$ 当 $G_i(Ax(t)+Bu^*(t|t)+Ew(t))>g_i$，否则为 0。(12b)（md 311–313）：$Y_i(t+1):=\frac{\sum_{j=1}^{t+1}V_i(j)}{t+1}$。
- **[原文]** 时序（md 305）：“the control input applied at time t (along with the uncertainty realized at t) is manifested with updated system states, which can be observed only at t + 1, i.e., there is one time-step delay in observing violations”。
- **[原文]** 违约是相对**原始约束** $g$ 判定的，不是相对松弛后的 $g-h$（md 177：“$Gx(t)\le g$ is referred to as the original constraint with respect to which violations are measured”）。在 JCC 下，任一约束被违反就计为一次违约（md 315）。
- **[原文]** 有后处理时，改用 (25)（md 631–633）：$V_1(t+1):=1$ 当 $G_1x(t+1)>g_1$，其中 $x(t+1)$ 是后处理后的真实状态。
- **[原文]** Algorithm 1（md 594–613）的步骤顺序：解 (11) 得到 $u^*(t|t)$ → 施加输入（或经后处理）得到 $x(t+1)$，计算 $V(t+1)$ → 由 (12b) 计算 $Y(t+1)$ → 由 (18) 计算 $h(t+1)$ → $t\leftarrow t+1$。
- **[推断]** 时序完整链条如下。时刻 $t$ 已知 $x(t)$、$Y(t)$ 和 $h(t)$，其中 $h(t)$ 由 $Y(t)$ 决定，是 $\mathcal F_t$ 可测的。用 $h(t)$ 解 (11) 并施加 $u(t)$，$w(t)$ 实现后，在 $t+1$ 测得 $x(t+1)$、$V(t+1)$，更新 $Y(t+1)$ 与 $h(t+1)$。(18) 中的 $h_i(t)$ 用 $Y_i(t)$ 和分母 $2(t+1)$，其用途正是影响下一次的违约 $V_i(t+1)$，因此时序自洽。$Y(0)$ 没有定义，但 Algorithm 1 由 $h(0)$ 直接启动，第一次调用 (18) 是 $h(1)$ 使用 $Y(1)$，所以不需要 $Y(0)$。

---

## 3. 更新律：完整形式、推导、$\gamma_i$、符号与初值

### 3.1 原文完整公式
- **[原文]** (18)（md 387–389，经 PDF 核对）：
  $h_i(t)=h_i(t-1)\Big[1+\dfrac{\alpha_i-Y_i(t)+\frac{2Y_i(t)-1}{2(t+1)}}{\gamma_i}\Big]$
- **[原文]** 对照方法 SMPC Lit（引自 [3]，md 695–699，经 PDF 核对）：$\tilde h_i(t)=\tilde h_i(t-1)\Big[1-\frac{\alpha_i-Y_i(t)+\frac{2\alpha_i-1}{2t}}{\gamma_i}\Big]$。两者的修正项不同：OA-SMPC 用 $\frac{2Y-1}{2(t+1)}$，[3] 用 $\frac{2\alpha-1}{2t}$。

### 3.2 推导链（原文步骤与本审稿人的补充）
1. **[原文]** 由 (13)（md 331–333）：$Y_i(t+1)=\frac{t}{t+1}Y_i(t)+\frac{V_i(t+1)}{t+1}$。
2. **[原文]** 令 $Z_i(t)=|\alpha_i-Y_i(t)|$（md 321），$\Delta_i(t)=\mathbb E[Z_i(t+1)|\mathcal F_t]-Z_i(t)$。又令 $p_i(t+1):=\mathbb P(V_i(t+1)=1|\mathcal F_t)$（md 341），得到 (15)–(17)（md 343–355）：
   $\Delta_i(t)=p_i(t+1)\beta_i(t)+\big[|\alpha_i-\tfrac{t}{t+1}Y_i(t)|-|\alpha_i-Y_i(t)|\big]$，
   $\beta_i(t)=|\zeta_i-\tfrac{1}{t+1}|-|\zeta_i|$，其中 $\zeta_i=\alpha_i-\tfrac{t}{t+1}Y_i(t)$（脚注 5，md 383）。
3. **[原文]** 脚注 5（md 383）：“Solving for $\beta_i(t)<0$ leads to $\zeta_i(t)>\frac{1}{2(t+1)}$, which implies $\alpha_i-Y_i(t)+\frac{2Y_i(t)-1}{2(t+1)}>0$”。
4. **[原文]** md 379–385：$\beta_i<0$ 对应“violation ... achieved by expanding”，$\beta_i>0$ 对应“nonviolation ... achieved by contracting”。因此“From Assumption 4, the $h_i$ update rule can be framed with $K_i(t)\propto[\alpha_i-Y_i(t)+\frac{2Y_i(t)-1}{2(t+1)}]$”，由此得到 (18)。
5. **[推断] 修正项的含义。** 恒等式如下：
   $\alpha_i-Y_i(t)+\dfrac{2Y_i(t)-1}{2(t+1)}=\alpha_i-\dfrac{tY_i(t)+\tfrac12}{t+1}$。
   右端的 $\frac{tY+1/2}{t+1}$ 是下一步两个可能取值 $Y(t+1)\in\{\frac{tY}{t+1},\frac{tY+1}{t+1}\}$（分别对应 $V=0$ 与 $V=1$）的**中点**。所以括号项的符号回答的问题是：“下一步违约和不违约，哪一个让 $Y(t+1)$ 更接近 $\alpha$？”修正项 $\frac{2Y-1}{2(t+1)}$ 是一步前瞻取整带来的偏移，量级为 $O(1/t)$。它把切换阈值从 $Y=\alpha$ 移到
   $b(t):=\dfrac{\alpha-\frac{1}{2(t+1)}}{1-\frac{1}{t+1}}$。在 $\alpha<0.5$ 时有 $b<\alpha$，因此当 $Y\in(b,\alpha)$ 时，(18) 已经开始收缩 $h$，尽管此时 $Y<\alpha$。
6. **[推断]** 原文只从 $\beta$ 推导出了 $K_i$ 的**符号**。$K_i$ 与括号项**成比例**是设计选择（“can be framed with $K_i\propto$”），比例形式和幅值都没有推导依据。

### 3.3 $\gamma_i$（任务中的 χ）的作用与取值条件
- **[原文]** md 391：“$\gamma_i\in\mathbb R_{0+}$ is a constant of proportionality that adjusts the rate of $h_i$ update ensuring $\frac{\alpha_i-Y_i(t)+\frac{2Y_i(t)-1}{2(t+1)}}{\gamma_i}>-1,\ \forall t$”。
- **[原文]** md 663：“setting a high value of $\gamma_i$ in (18), which is a design choice, at the cost of slower system adaptation”。md 671：“The satisfaction of Assumption 3(a) is ensured by choosing a sufficiently large $\gamma_i$ which constrains the rate of $h_i(t)$ increase depending on the available control input power to satisfy (A.5)”。Table I（md 675）：$\gamma_1=\gamma_2=15$，$\alpha=0.1$，$h(0)=[-0.1\ -0.1]^\top$。
- **[推断]** 括号项关于 $Y$ 是仿射函数，斜率为 $-1+\frac{1}{t+1}\le0$，因此在 $Y\in[0,1]$ 上的取值范围是 $\big[\alpha-1+\frac{1}{2(t+1)},\ \alpha-\frac{1}{2(t+1)}\big]$，恒大于 $-1$。所以只要 $\gamma_i\ge1$，“$>-1$”条件就自动成立。$\mathbb R_{0+}$ 包含 0，原文写法不严谨，应为 $\gamma_i>0$。
- **[推断]** 取 $\gamma=15$、$\alpha=0.1$ 时，每步乘子约在 $(0.94,\ 1.0067)$ 之间。收缩（$h$ 向 0 靠近）每步最多约 6%，放松每步最多约 0.67%，调节速度明显不对称。

### 3.4 h 能否变号、能否为零、初值
- **[原文]** Assumption 4（md 297）要求 $K_i>-1$ 以保证 $h_i<0\ \forall t$。Remark 7（md 637）：“$h_i$ increases until $h_i\to0^-$ according to (18)”。
- **[推断]** 更新是乘性的：$h_i(t)=h_i(0)\prod_{s\le t}(1+K_i(s))$。因此：
  - **不能变号**：若 $h(0)<0$，则 $h$ 永远小于 0，本方法永远不会把名义约束收紧到 $g$ 以内。
  - **有限时间内不能到达 0**，只能趋近 $0^-$。若取 $h(0)=0$，则 $h\equiv0$，0 是吸收点。
  - 越靠近 0，步长 $|h|\cdot|K|$ 越小，恢复放松越慢。$|h|$ 的增长只受物理截断限制（md 663）。
  - $\log|h|\approx\log|h(0)|+\frac1\gamma\sum_s[\alpha-Y(s)+\dots]$，所以 (18) 本质上是作用在 $\log|h|$ 上、以“平均误差”为输入的积分器。$\gamma$ 是常数，不像随机逼近那样使用衰减步长。
- **[原文]** 初值（md 247）：“The initial value of h at t = 0 can be calculated based on domain knowledge [18] ... The initial value of h is not important since h gets adapted as the system evolves with time [7]”。Algorithm 1（md 599）：“Choose h(0) from domain knowledge”。算例 $h(0)=[-0.1,-0.1]^\top$（md 675），并要求“$g_i-h_i(0)$ is sufficiently far away from physical limits”（md 663）。
- **[推断]** “初值不重要”这一说法没有证明。在乘性律下，初值决定 $|h|$ 的量级，而且 $h(0)=0$ 是退化情形。

---

## 4. 理论结果逐条

**贯穿性的关键点。** Theorem 1、Lemma 1、Lemma 2、Theorem 2 的假设都写作“Assumptions 1–3 and 5”（PDF p.1550–1552），**不包含 Assumption 4，也不包含 (18)**。原文在 md 391 明说：“Theorem 1, discussed next, implicitly assumes that (18) is able to enforce Assumption 5”。

### 4.0 Assumption 5 与“理想策略”的控制对象
- **[原文]** Assumption 5（md 359）：“(a) There exists an ideal control policy (or ideal control input) at time t, applying which makes $p_i(t+1)=p_i^*(t+1)$, where $p_i^*(t+1)\in\{0,1\}$, ∀t. (b) When $\beta_i(t)<0$, we apply ideal control inputs at t leading to $p_i^*(t+1)=1$ whereas, if $\beta_i(t)>0$ ... $p_i^*(t+1)=0$.”
- **[原文]** md 363：“Assumption 5(a) ensures that there exists a control input which causes a constraint violation a.s. at the next time step t + 1 in closed-loop from any present state x(t)”。结论（md 793）：“under the assumption of ideal control inputs that can cause/prevent constraint violations almost surely”。
- **判断：** 形式上，Assumption 5 约束的是**条件违约概率** $p_i(t+1)$。但它把 $p_i$ 限制在 $\{0,1\}$，于是 $V_i(t+1)$ 变成 $\mathcal F_t$ 可测的量。**[推断]** 因此理想策略实际上直接选择**违约事件本身**：下一步违约还是不违约，由策略确定性地决定。它控制的**不是实际边界参数 $h$**，因为 $h$ 不出现在 Assumption 5、Theorem 1–2 或 Lemma 1–2 中。
- **[推断]** 在 Assumption 5 下，$Y^*$ 是一个**确定性序列**：给定 $Y(t_0)$ 后，每一步的 $V$ 都被唯一确定（$\beta=0$ 的平局情形除外）。它等价于贪心取整规则：选取 $V(t+1)\in\{0,1\}$，使 $|\alpha-Y(t+1)|$ 最小。定理中所有“a.s.”都是空洞的。这个策略还完全不顾经济目标 $J$，与 (11) 的最优输入 $u^*$ 没有关系。
- **[推断]** 可实现性方面，Remark 2 与 md 293 称 3(b) 是理想策略存在的“sufficient conditions”。但要在所有 $w\in W$ 下**确定地**制造或避免违约，还需要输入幅值相对于 $W$ 足够大、状态集合允许越界等条件，一步可控并不够。

### 4.1 Theorem 1（md 393–469；陈述按 PDF p.1550 核对）
- **陈述 [原文]：**“Let Assumptions 1–3 and 5 hold. Given $\alpha_i>\frac{1}{2(t_0+1)}$, $Z_i^*(t)$, which is the ideal surrogate of the desired supermartingale $Z_i(t)$, is monotonically decreasing a.s. $\forall t\ge t_0$, if and only if, $Y_i(t)\notin\kappa(\alpha_i,t),\ \forall t\ge t_0$”，其中
  $\kappa(\alpha_i,t):=\Big(\frac{\alpha_i-\frac{1}{2(t+1)}}{1-\frac{1}{2(t+1)}},\ \frac{\alpha_i}{1-\frac{1}{2(t+1)}}\Big)$。
- **证明思路 [原文]：** 按 $\beta$ 的符号分情形。
  - Case 1/4（$\beta<0$，$p^*=1$，(19)）：$\kappa_1=(a,b)$。
  - Case 2/5（$\beta>0$，$p^*=0$，(20)）：$\kappa_2=(b,c)$。
  - Case 3/6（$\beta=0$）：$\kappa_3=\{b\}$。
  - 取并集得到 $\kappa=(a,c)$。其中 $a=\frac{\alpha-\frac{1}{2(t+1)}}{1-\frac{1}{2(t+1)}}$，$b=\frac{\alpha-\frac{1}{2(t+1)}}{1-\frac{1}{t+1}}$，$c=\frac{\alpha}{1-\frac{1}{2(t+1)}}$。
- **[推断] 核验：** 我逐式复算了 (19b) 与 (20b)，区间端点正确，并且当 $\alpha<0.5$、$\alpha>\frac{1}{2(t+1)}$ 时有 $a<b<\alpha<c$。作为**逐时刻**的确定性命题，“$\Delta^*(t)\le0\iff Y(t)\notin(a,c)$”成立。

### 4.2 Remark 4（md 471–473）
- **[原文]** 若改用闭区间 $\kappa'=[a,c]$，则 $\Delta^*<0$，$Z^*$ 严格递减；“As $Z_i^*(t)$ is bounded from below by 0, we conclude $\lim Z_i^*(t)=\inf\{Z_i^*(t)\}=0$ a.s. from the monotone convergence theorem”，条件为 $\alpha>\frac{1}{2(t_0+1)}$、$p=p^*$、$Y(t)\notin\kappa'\ \forall t\ge t_0$。
- **[推断] 缺口：** 单调收敛定理只给出 $\lim=\inf$，**不能给出 $\inf=0$**。另外，其前提“$Y\notin\kappa'$ 对所有 $t\ge t_0$ 成立”与 Lemma 1（进入 $\kappa'$ 几乎必然发生）直接矛盾，所以 Remark 4 的收敛结论是在一个不可能成立的前提下得出的。

### 4.3 Remark 5（md 475–479）
- **[原文]** $\kappa'$ 的宽度为 $\frac{1}{2t+1}$，$Y$ 的取值粒度为 $\frac1t$，因此区间内至多有一个临界值。“the assumption of $Y_i(t)\notin\kappa'(\alpha_i,t)$, is weak and can be easily satisfied as t increases”。
- **[推断] 与事实不符。** 在理想策略下，$Y$ 进入 $\alpha$ 的 $O(1/t)$ 邻域后，与 $\kappa'$ 处于同一尺度。我用数值实验验证：$\alpha\in\{0.05,0.1,0.37\}$ 时，$t>1000$ 后约 **50%** 的时刻 $Y\in\kappa'$；$\alpha=0.2$ 时约 **40%**。“随 $t$ 增大容易满足”这个说法不成立，而且与 Lemma 1、Lemma 2 自身的结论（反复进出）相矛盾。

### 4.4 Lemma 1（md 481–493）
- **陈述 [原文]：**“Let Assumptions 1–3 and 5 hold. Given any initial time $t_0$, with $\alpha_i>\frac{1}{2(t_0+1)}$ and $Y_i(t_0)\notin\kappa'(\alpha_i,t_0)$, there exists some time $t'=\inf\{t>t_0\mid Y_i^*(t)\in\kappa'(\alpha_i,t)\}$ a.s.”
- **证明思路 [原文]：** 比较一阶差分：$\delta\hat w=\frac{-2}{(2t+1)(2t+3)}=O(1/t^2)$，并称 $Z^*$ 在 $\kappa'$ 外“decreases most modestly ... in the order of $O(1/t)$”，由此得到 $\frac{\delta\hat w}{\delta t}>\frac{\delta Z^*}{\delta t}$。
- **[推断]** 结论成立，但证明不完整。比较差分的相对大小并不能推出击中时间存在。正确的论证应是：在 $\kappa'$ 外，每步 $Z^*$ 至少下降 $\min(\alpha,1-\alpha)/(t+1)$，而 $\sum 1/t$ 发散，所以有限时间内必然进入。原文没有写出“$O(1/t)$ 下降”的具体计算。

### 4.5 Lemma 2（md 495–497）
- **陈述 [原文]：** 若 $Y_i(t_0)\in\kappa'(\alpha_i,t_0)$，则存在 $t'=\inf\{t>t_0\mid Y_i^*(t)\notin\kappa'(\alpha_i,t)\}$ a.s.
- **证明 [原文]：**“When $Y_i(t)\in\kappa'$, $\frac{\delta Z_i^*}{\delta t}\ge0$, while $\frac{\delta\hat w_i}{\delta t}<0$, thus leading to $\frac{\delta Z_i^*}{\delta t}>\frac{\delta\hat w_i}{\delta t}$, which completes the proof.”
- **[推断] 推理不成立（non sequitur）。** 两个差分的符号比较不能推出“离开时刻存在”。结论本身可以用另一种方式证明：令 $e(t)=S(t)-\alpha t$，$\kappa'$ 大约对应 $e\in[-\frac{1-\alpha}{2},\frac{\alpha}{2}]$，长度为 1/2；贪心策略下 $e$ 在长度为 1 的区间上做步长 $\alpha$ 的旋转，不可能永远停留在长度 1/2 的子区间内。原文没有给出这类论证。

### 4.6 Theorem 2（md 499–509）
- **陈述 [原文]：**“Let Assumptions 1–3, and 5 hold. Given any initial time $t_0$ with $\alpha_i>\frac{1}{2(t_0+1)}$, and time average of violations $Y_i(t_0)$, $Y_i^*$ asymptotically converges to $\alpha_i$ a.s.”
- **证明思路 [原文]：** Lemma 1 与 Lemma 2 交替作用，$Y^*$ 反复进出 $\kappa'$；“as the width of the critical region vanishes at $t\to\infty$, making the critical region the point $\alpha_i$, we conclude $Y_i^*$ converges to $\alpha_i$”。原文还称“Theorem 2 also rigorously proves the result which was intuitively argued in [3]”。
- **[推断] 缺口：** 离开 $\kappa'$ 后 $Z^*$ 会增大多少，原文没有给出上界（Lemma 2 只说“no closer”）。“区间宽度趋于 0”不能推出“$Y^*$ 趋于 $\alpha$”：$Y^*$ 可以在区间外越走越远。所以“rigorously proves”的说法过度。
- **[推断] 结论本身正确，且有更简单的完整证明。** 令 $S(t)=tY(t)$、$e(t)=S(t)-\alpha t$。贪心规则等价于在 $\{S,S+1\}$ 中选择最接近 $\alpha(t+1)$ 的值。若 $|e(t)|\le\frac12$，由于 $\alpha\in(0,\frac12)$，有 $\alpha(t+1)-S\in(-\frac12,1)$，因此 $|e(t+1)|\le\frac12$ 保持成立。若 $|e|>\frac12$，则 $e$ 每步以 $\alpha$ 或 $1-\alpha$ 的步长单调靠近。所以有限步后 $|Y(t)-\alpha|\le\frac{1}{2t}$ **确定性地**成立。数值验证：$\alpha\in\{0.05,0.1,0.15,0.2\}$、多种初值下，$t>2000$ 后 $\max|S-\alpha t|\le0.5$。这个论证也表明，条件 $\alpha>\frac{1}{2(t_0+1)}$ 并非必需。

### 4.7 $\beta=0$ 的回避条件（md 365–377）
- **[原文]**“$(t+1)\alpha_i-(1/2)\notin\mathbb N,\ \forall t$”，可“by appropriate choice of $\alpha_i$”保证。
- **[推断]** 该条件等价于：$\alpha$ 不能写成 $\frac{2n+1}{2(t+1)}$ 的形式。当 $\alpha$ 为分母为偶数的有理数时，条件不成立。**算例使用的 $\alpha=0.1,\,0.05,\,0.15$ 都不满足**（例如 $\alpha=0.1$ 时，在 $t=4,14,24,\dots$ 处 $\beta=0$ 可以取到，$S(4)=0$ 即可触发），只有 $\alpha=0.2$ 满足。由于算例已经放弃了 Assumption 5，这一点只影响理论与参数选择之间的一致性。

### 4.8 Theorem 3（md 517–549）
- **陈述 [原文]：**“Let Assumptions 1–3(a) hold. The expected value of $Y_i(t+1)|\mathcal F_t$ asymptotically converges to $Y_i(t)$.”
- **证明思路 [原文]：** 对 (16) 取 $t\to\infty$ 得 (21)，从而 $\lim\Delta_i(t)=0$；分 Case 1（$\lim Y=\lim\mathbb E[Y(t+1)|\mathcal F_t]$）与 Case 2（极限值为 $\alpha\pm l$）；由 (22)–(23) 推出 $l=0$。
- **[推断] 这一结论是平均算子的恒等式，不包含关于系统或控制的任何信息。** 由 (13) 直接得到
  $\mathbb E[Y(t+1)|\mathcal F_t]-Y(t)=\dfrac{p(t+1)-Y(t)}{t+1}\in\big[-\tfrac{1}{t+1},\tfrac{1}{t+1}\big]$，
  对**任意** 0/1 序列、任意控制器都确定性地趋于 0。它不涉及 $h$、(18)、$\alpha$，也不需要 Assumptions 1–3(a)。此外，证明中 Case 1/2 默认 $\lim Y$ 存在，这一点没有被证明；称其为“martingale-like”也有误导性，因为 $Y$ 并非鞅，差分只是因分母 $t+1$ 而消失。

---

## 5. III.E：是否建立了“实际 h 更新 → 违约概率”的桥梁

- **[原文]** III.E 开头（md 519）：“It is important to determine the asymptotic behavior ... of the practical system in which the simplifying assumption of applying an ideal control policy ... is dropped.”其唯一结果是 Theorem 3。
- **判断：III.E 没有建立从 $h$ 到违约概率的桥梁。**
  - Theorem 3 的陈述和证明中都**没有出现 $h$、(18) 或 $\gamma$**，也没有关于 $p_i(t+1)$ 与 $h_i(t)$ 关系的任何假设：没有单调性，没有连续性，没有平稳性或遍历性，也没有灵敏度下界。
  - 结论 $\mathbb E[Y(t+1)|\mathcal F_t]-Y(t)\to0$ 对任何控制器都成立（见 §4.8），所以它对“更新律是否有效”没有任何区分能力。
  - 证明本身存在缺口：默认 $\lim Y$ 存在，Case 2 的构造也是任意的。不过其结论可以一行证出，并且是平凡的。
- **原文中最接近“桥梁”的内容在 III.D2（md 513–515），不在 III.E：**
  - [原文]“The decrease of $h_i$ expands the feasible state set for $x(t+1)$ in (11) and thereby encourages constraint violations at t + 1 in closed-loop. While under the ideal control policy (Assumption 5), $h_i(t)<h_i(t-1)$ would have guaranteed constraint violation at time t + 1 a.s., in practical scenarios, just the expansion of the feasible state set alone cannot guarantee violation. ... Thus, under deviation of the practical control policy from the ideal, while asymptotic convergence of $Y_i$ to $\alpha_i$ cannot be guaranteed, it still is encouraged by (18).”
  - **强度 [推断]：** 这只是**符号一致性**的定性论证：$h$ 下降时可行集扩张，可行集扩张“encourages”违约。它隐含依赖“违约概率关于 $h$ 单调”这一假设，但没有陈述，也没有证明。可行集关于 $h$ 按包含关系单调是对的；但 MPC 最优解及闭环违约概率是否关于 $h$ 单调，原文没有论证。脚注 3（md 253）反而承认“relaxing the state constraints ... does not automatically lead the system to predicted nominal solutions that violate”。
  - **缺口 [推断]：**
    - (a) 缺少 $p(h)$ 的单调性或连续性假设。
    - (b) 即使 $p$ 关于 $h$ 单调，(18) 每步只改变 $h$ 的 $O(1/\gamma)$ 比例，一步之内不可能把 $p$ 从 0 切换到 1，所以 (18) 在一般情况下**无法实现** Assumption 5 要求的逐步 bang-bang 选择。md 391 的“implicitly assumes that (18) is able to enforce Assumption 5”恰好说明，这里的桥梁是被**假设**的，并非由证明得到。
    - (c) “$h(t)<h(t-1)$ would have guaranteed violation under Assumption 5”混淆了两件事：在 Assumption 5 下违约由理想策略决定，与 $h$ 的方向无关。
    - (d) $h$ 只能取负值（§3.4）。若 $h\to0^-$ 时违约率仍大于 $\alpha$，则不存在可达的平衡点。原文靠后处理的截断与 $\varepsilon$ 来处理这种情形（§7），但也没有给出证明。

---

## 6. 作者如何表述理论与实际算法的关系

原文整体上**承认**理论只在理想策略下成立，但在几处用词上有过度表述：

- 坦白的部分 **[原文]**：
  - 摘要（md 5）：“Under an ideal control policy assumption, it is proven that the time average of constraint violations asymptotically converges to the maximum allowed violation probability.”
  - Contribution 1（md 105）：“for practical implementation (i.e., without the simplifying ideal control policy assumption), while the proposed method cannot guarantee the aforementioned convergence, it still encourages it.”
  - md 391：“In practical applications, while (18) cannot guarantee satisfaction of Assumption 5 in closed-loop, it still encourages it.”
  - md 515：“asymptotic convergence of $Y_i$ to $\alpha_i$ cannot be guaranteed”。
  - md 671：“the ideal control policy in Assumption 5 is relaxed for the case study for realistic simulations.”
  - md 91 批评 [3] 的理想策略“may be unmet for practical implementation”，本文沿用了同样的假设。
- 过度表述 **[原文引用 + 推断评价]**：
  - Contribution 2（md 106）：“proves that the time average ... exhibits martingale-like behavior asymptotically for practical implementation”；结论（md 793）：“even without the ideal control input assumption”；md 553：“may be useful for practical operation ... to avoid unpredictable violation behavior in the long run”。**[推断]** 该性质对任何序列都成立，不能用来支持“避免不可预测违约”。
  - Contribution 3（md 107）：“robust to significant violation of chance constraints under time-varying uncertainty distribution for practical implementation, provided an additional postprocessing step”；Remark 7（md 637）：“satisfied in a ‘practical sense’”。**[推断]** 这些说法没有定理支撑。
  - md 501：“Theorem 2 also rigorously proves the result which was intuitively argued in [3]”。**[推断]** 证明并不严格（§4.6）。
  - md 588：“The results from Theorems 1–3 are independent of the postprocessing framework, and still hold with postprocessing”；md 590：“the results hold for nonlinear systems too”。**[推断]** 这两句话字面上是对的，但原因是定理本身不涉及系统动态（Theorem 1–2 只是关于 0/1 序列的算术，Theorem 3 是平凡恒等式），所以它们恰好说明理论与系统、MPC 和 $h$ 都没有关联。
  - md 759：“it is the adaptive relaxation rule in OA-SMPC rather than the nature of uncertainties that ensure nonconservative chance constraint satisfaction”。这是基于单一年份、单一数据集的经验结论。md 755：“It is expected for Y to decrease below α if the OA-SMPC is run over a longer time period”，这是没有依据的推测。
- 算例结果 **[原文]**：Table II（md 685）中 OA-SMPC 年末 $Y=10.1\%$，略高于 $\alpha=0.1$；SMPC Lit 为 2.2%；Traditional 2 为 20.4%。Table III（md 763）中 $\alpha=0.05/0.15/0.2$ 对应的年末 $Y$ 为 $4.5\%/14.3\%/19.1\%$。Fig. 2(b) 中 $h$ 大致在 $[-0.1,0]$ 之间来回摆动。
- 算例实际使用的更新律不是纯 (18) **[原文]**：IV.D（md 679）规定在 OP 时段内“we restrict $h_i$ increase ... overriding (18) when required ... if $h_i$ increases it is reset manually to the previous value”，另外还有物理截断（md 663）。**[推断]** 因此，即便有理论，也不直接覆盖算例中的实现。

---

## 7. 后处理（III.F）对违约统计的影响

- **[原文]** Assumption 6（md 559）：存在主、从两个耦合控制源。主源（BESS）在“time-varying design limitations” $\mathbf s_{1:q}$ 与 $\mathbf g_{1:r}-\mathbf h_{1:r}(t)$ 内吸收不确定性，剩余部分由从源（电网）通过 (3) 吸收。(24a)（md 619–621）：$u_1(t):=\min(u_1^*(t|t)+D_1w(t),\tilde u_1)$，$D=B^\dagger E$。(24b)（md 625–627）：$x_1(t+1)=\min(Ax_1(t)+B_{11}u_1(t),\tilde x_1(t+1))$。违约按 (25) 相对原始 $g$ 判定（md 631–633）。
- **[推断] 影响 1：违约深度被 $|h|$ 截断。** 实际状态被夹在松弛边界 $g-h(t)$ 以内，因此一次违约只能落在 $(g,\,g-h(t)]$ 这个带内。违约的发生取决于状态能否进入这个带，而带宽恰好是 $|h|$。
- **[推断] 影响 2：后处理构造了一个单侧的 $h\to$违约联系。** 当 $h\to0^-$ 时带宽趋于 0，违约几乎被截断掉，只剩数值意义上的违约。这正是 Remark 7 的 $\varepsilon$ 处理：[原文] md 637，“Mathematical violations can still persist in $V_i(t+1)$ after $h_i\to0^-$ ... These violations can be ignored ... by adding a small $\varepsilon>0$ to $g_i$ resulting in considering violations only if $G_ix(t+1)>g_i+\varepsilon$”。所以在“后处理 + $\varepsilon$”下，把 $h$ 推向 0 可以把违约率压到接近 0。这是 Contribution 3 所称“robust”的真正机制。但它只给出“能压低”的方向，不能保证“能提高到 $\alpha$”，原文也没有把它形式化为定理。
- **[推断] 影响 3：违约的定义可能被操作性地修改。** 引入 $\varepsilon$ 后，统计的 $V$ 已不是 (12a)/(25) 所定义的量。原文说 $\varepsilon$ 可以“removed by the user when $h_i$ decreases sufficiently”，但算例没有说明是否使用了 $\varepsilon$。
- **[推断] 影响 4：从源吸收了原本会造成状态越界的不确定性，这直接改变了违约的分布。** 此时 $p(t+1)$ 同时取决于 $h$、$\tilde u_1$ 和从源容量；md 588 称 Theorem 1–3“still hold”，但这些定理与这些量无关。
- **[推断] 影响 5：对比公平性。** SMPC Lit 的后处理可行集被改成物理限值 $[0,1]$（md 695–699），而 OA-SMPC 被夹在 $g-h$ 内，两者的违约机制不同，年末 $Y$ 的比较不是同口径。
- **[原文]** Remark 6（md 635）：后处理避免了 [3] 中大扰动把状态推出可行域后，下一步需要高代价控制把状态拉回的问题。

---

## 8. 独立发现的证明缺口与表述过度（汇总）

以下均为 **[推断]**，并逐条指向原文位置。

1. **理想策略下问题是确定性的，“a.s.”和“supermartingale”框架是空洞的**（md 323–327、359、393）。$p^*\in\{0,1\}$ 使 $V(t+1)$ 可以预先确定，$Z^*$ 是确定性数列，从未使用任何鞅定理（只用了实数单调收敛），实际过程 $Z$ 的上鞅性质也从未被证明。
2. **Theorem 1–2 与 $h$、(18)、MPC 都无关**：它们的假设中没有 Assumption 4。它们证明的是“贪心地选择下一步违约与否，时间平均会趋于 $\alpha$”，这是一个取整类型的算术事实，有 $O(1/t)$ 的完整初等证明（§4.6）。
3. **Remark 4：** “$\lim=\inf=0$”中的 $\inf=0$ 没有证明；其前提与 Lemma 1 矛盾，是空真命题（§4.2）。
4. **Remark 5：** “$Y\notin\kappa'$ is weak and easily satisfied as t increases”与事实相反。数值上约 40–50% 的时刻 $Y\in\kappa'$（§4.3）。
5. **Lemma 1：** 只比较了一阶差分的量级，没有给出击中时间的论证，“$O(1/t)$ 下降”也没有计算（§4.4）。
6. **Lemma 2：** 证明是 non sequitur，符号比较推不出离开时刻存在（§4.5）。
7. **Theorem 2：** 离开 $\kappa'$ 后的偏离没有上界；“区间宽度→0 ⇒ 收敛”不成立；“rigorously proves”属于过度表述（§4.6）。
8. **$\beta=0$ 的回避条件与算例参数冲突：** $\alpha=0.1,0.05,0.15$ 都不满足 $(t+1)\alpha-\tfrac12\notin\mathbb N$（§4.7）。
9. **Theorem 3 是平均算子的平凡恒等式**：$|\mathbb E[Y(t+1)|\mathcal F_t]-Y(t)|\le\frac{1}{t+1}$ 对任何序列成立。证明默认极限存在。“martingale-like … for practical implementation”的说法把平凡性质包装成了对实际算法的保证（§4.8）。
10. **(18) 能实现 Assumption 5 只是假设，没有证明**（md 391 自承）。在一般情况下它也不可能实现：乘性小步长无法做到逐步 bang-bang（§5）。
11. **$p(h)$ 单调性这一隐含假设从未陈述**；脚注 3（md 253）承认松弛不一定导致违约（§5）。
12. **$h$ 不能变号**：若 $h\to0^-$ 时违约率仍大于 $\alpha$，则不存在可达平衡点，此时只能依靠后处理截断与 $\varepsilon$ 重新定义违约（§3.4、§7）。
13. **Assumption 3(b) ⇒ 理想策略存在（md 293、Remark 2）不成立**：还需要输入幅值相对 $W$ 足够大，JCC 下也不能同时迫使上下界违约（§4.0）。
14. **滤子只由 $w$ 生成，而 $c(t)$ 是外生的随机预测**，形式上有疏漏（§1.1）。
15. **算例实现与理论对象不一致**：OP 时段覆盖 (18)、物理截断、后处理截断、可能使用的 $\varepsilon$、$\alpha$ 取值违反回避条件、Assumption 5 被放弃（md 663、671、679、637）。
16. **$\gamma_i\in\mathbb R_{0+}$ 应为 $\gamma_i>0$**；“$>-1$”条件在 $\gamma\ge1$ 时自动成立，不构成实际约束（§3.3）。

**[推断] 如需补上这座桥，一个可行方向（供参考，原文没有）：** 令 $M(t)=\sum_{j\le t}(V(j)-p(j))$。它是有界增量鞅，由鞅强大数律，$Y(t)-\frac1t\sum_{j\le t}p(j)\to0$ a.s.，这一步不需要任何分布假设。这样问题就被转化为控制条件违约概率的时间平均 $\bar p$。在此基础上，再加入“$p(t+1)=\pi(h(t),\cdot)$ 关于 $h$ 单调、存在满足 $\pi(h^\star)=\alpha$ 的内点 $h^\star$、并有灵敏度下界”这类假设，再配合随机逼近（衰减步长）论证。这是原文缺失的那一环。
