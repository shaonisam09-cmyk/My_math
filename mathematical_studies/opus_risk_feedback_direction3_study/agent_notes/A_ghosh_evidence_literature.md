# Agent A：Ghosh 原文、证据账本、文献定位与理论边界

日期：2026-09-24。遵守 `00_common_setting.md` 的符号与已更正说法。
行号约定：“md Lxxx”指 `Adaptive_Relaxation-Based_Nonconservative_Chance_Constrained_Stochastic_MPC.md`；“tex Lxxx”指 `Energy-and-Buildings.tex`。
标记：**[原文]** 为原文确有的内容；**[推断]** 为本笔记的推导或判断；**[数值]** 为本笔记的纯算术演示，不是数据中心实验证据。
关键引文已按 md 原文逐条抽查（L153、L297、L311–313、L331–333、L359–363、L387–391、L393–396、L475–479、L499–515、L519–553、L588、L637、L663、L671、L679、L755、L789）。既有审读笔记 `opus_adaptive_update_math_study/agent_notes/A_ghosh_theory_boundary.md`（下称 A 笔记）与 `sadaptive_update_math_study/Ghosh_Theory_Comparison.md`（下称 GTC）的结论与抽查一致，下文直接复用并注明。

---

## 第 1 部分　Ghosh 原文与本稿的关系

### 1.1 全历史累计平均的敏感度随 t 衰减

**[原文]** 统计量定义 (12b)（md L311–313）：$Y_i(t+1)=\frac{1}{t+1}\sum_{j=1}^{t+1}V_i(j)$。递推 (13)（md L331–333）：
$$Y_i(t+1)=\frac{t}{t+1}Y_i(t)+\frac{V_i(t+1)}{t+1}\quad\Longleftrightarrow\quad Y_i(t+1)-Y_i(t)=\frac{V_i(t+1)-Y_i(t)}{t+1}.$$
因此新观测对统计量的边际影响为 $\partial Y(t+1)/\partial V(t+1)=1/(t+1)$。本稿 tex L429 已写出同一事实。

**[推断] 敏感度一路传到更新律。** 原文 (18)（md L387–389）的括号项可以改写为
$$K(t)=\frac1\gamma\Big[\alpha-Y(t)+\frac{2Y(t)-1}{2(t+1)}\Big]=\frac1\gamma\Big[\alpha-\frac{S(t)+\tfrac12}{t+1}\Big],\qquad S(t)=\sum_{j\le t}V(j).$$
所以新事件 $V(t)$ 对当步乘子的影响是 $\partial K(t)/\partial V(t)=-1/[\gamma(t+1)]$，同样按 $1/t$ 衰减。

**[推断] 对数域展开：旧事件权重反而最大。** 因为 $|K|\le 1/\gamma$（算例 $\gamma=15$，md L675），可取一阶近似 $\ln(1+K)\approx K$，得到
$$\ln|h(T)|\approx\ln|h(0)|+\frac1\gamma\sum_{u=1}^{T}\Big[\alpha-\frac{S(u)+\tfrac12}{u+1}\Big]
=\ln|h(0)|+\frac1\gamma\sum_{s=1}^{T}\omega_T(s)\,[\alpha-V(s)]+(\text{与事件无关的 }O(\ln T)\text{ 项}),$$
其中 $\omega_T(s)=\sum_{u=s}^{T}\frac{1}{u+1}\approx\ln\frac{T+1}{s+1}$。这个权重关于事件时刻 $s$ **递减**：最早的事件权重约为 $\ln T$，最新事件的权重只有约 $1/(T+1)$。换句话说，(18) 在一阶近似下是一个按 $\ln(T/s)$ 加权的误差积分器，它不遗忘，而且越旧的事件累计影响越大。
[数值] 取 $T=35040$（一年 15 min 步长）：$s=96$ 时 $\omega\approx5.9$，$s=9600$ 时 $\omega\approx1.3$，$s=T$ 时 $\omega\approx0$。

**[推断] 工况切换后的响应延迟随已运行时长增长。** 设 $T_0$ 时 $Y\approx\alpha$，之后在当前 $h$ 下的实际违约频率变为 $p'\neq\alpha$。经过 $\Delta$ 步后 $\alpha-Y\approx-\Delta(p'-\alpha)/(T_0+\Delta)$，累积到 $\ln|h|$ 的驱动约为 $(p'-\alpha)\Delta^2/(2\gamma T_0)$（$\Delta\ll T_0$ 时）。要让 $\ln|h|$ 变化固定量 $c$，所需步数约为
$$\Delta\approx\sqrt{2\gamma c\,T_0/|p'-\alpha|}\ \propto\ \sqrt{T_0}.$$
[数值] $\gamma=15$、$c=\ln2$（松弛量减半）、$|p'-\alpha|=0.1$ 时，逐项求和得到：$T_0=1$ 天需约 2.3 天，$T_0=10$ 天需约 5.4 天，$T_0=100$ 天需约 15 天，$T_0=1$ 年需约 29 天。这就是用户所说“全历史反馈对新工况越来越慢”的定量来源。
对照：EV 每个事件对 $r$ 的影响恒为 $\kappa$，工况切换后的期望漂移为 $\kappa(\alpha-p')$，延迟与 $T_0$ 无关；W0 在 $Y^w$ 中每个事件的权重恒为 $1/w$，延迟上限由 $w$ 决定，也与 $T_0$ 无关。

**[推断] 历史“欠账”必须偿还。** G 的驱动项是 $\alpha-Y(u)=-(S(u)-\alpha u)/u$。在时刻 $s$ 留下的计数偏差 $D$ 会在之后每步产生 $-D/(\gamma u)$ 的驱动，总量约 $-(D/\gamma)\ln(T/s)$，没有上界。所以 G 会持续收紧或放松，直到计数偏差被反向偿还。EV 的平衡只要求当前 $p(r)=\alpha$，累计偏差 $\sum(\alpha-O)=(r_T-r_0)/\kappa$ 只随 $r$ 的位移而变化，不需要偿还；W0 的欠账在 $w$ 步后移出窗口。对“近期表现考核”来说，G 的偿还机制会在一次超标事件之后，让后续窗口长期低于 α（过度保守）。

### 1.2 原文是否讨论过敏感度衰减，是否有遗忘机制

- **[原文] 没有把它当作问题讨论。** 与之相关的表述都把衰减当作优点：
  - Remark 5（md L475–479）：临界区宽度 $1/(2t+1)$ 随 t 缩小，因此“$Y\notin\kappa'$ is weak and can be easily satisfied as t increases”。
  - Theorem 3 之后（md L553）：“martingale-like behavior asymptotically which may be useful ... to avoid unpredictable violation behavior in the long run”。
  - 算例叙述（md L755）：Y“oscillates about α with lower frequency and magnitude, as the system evolves”；Fig. 3 说明（md L789）重复同一描述。md L755 还写“It is expected for Y to decrease below α if the OA-SMPC is run over a longer time period”。
- **[推断]** 这些现象正是 $1/(t+1)$ 敏感度的直接结果：振荡幅度按 $O(1/t)$ 缩小是平均算子的性质，不是控制器变好的证据。理想策略下 $|Y^*-\alpha|\le 1/(2t)$（GTC §3.4、A 笔记 §4.6 的独立证明）也来自同一个 $1/t$ 粒度。对“长期平均达标”是优点，对“近期表现跟踪”是缺点。
- **[原文] 没有任何递减、遗忘、窗口、折扣或重置机制。** 全文检索 forget/window/moving/recent/sliding/discount/restart 均无相关结果。唯一的“加权平均”出现在对前人工作 [6]（Korda et al., TAC 2014）的综述中（md L79：“a loss function empirically weighted averaged over time”），不属于本文方法。
- **[原文] 原文对时变分布的主张** 只落在后处理上：Contribution 3（md L107）称“robust to significant violation of chance constraints under time-varying uncertainty distribution ... provided an additional postprocessing step”；md L91 批评 [3]“not robust ... under time-varying uncertainty distribution”。**[推断]** 这里的“鲁棒”指后处理把状态夹在 $g-h$ 内、配合 Remark 7 的 ε 把违约压低（见 1.3 (e)），与反馈统计量能否及时反映新工况无关，原文也没有给出任何切换响应分析。
- **[原文] 唯一带时段性的人工机制** 是 OP 时段禁止 $h$ 增大（md L679），它是按时段覆盖 (18)，不是遗忘。

### 1.3 原文理论的其他边界（逐项给行号）

**(a) 定理控制的对象。** [原文] Theorem 1、Lemma 1–2、Theorem 2 的假设是“Assumptions 1–3 and 5”（md L393、L481、L495、L499），对象是理想替身 $Z^*(t)=|\alpha-Y^*(t)|$ 与 $Y^*(t)$（Definition 6，md L361）。Assumption 5（md L359）要求存在理想策略使 $p(t+1)=p^*(t+1)\in\{0,1\}$，并按 $\beta$ 符号施加（md L363：“causes a constraint violation a.s. ... prevent a constraint violation a.s.”）。[推断] 这相当于策略直接选择下一步违约事件本身，定理里没有 $h$、(18)、$\gamma$ 或 MPC 最优解（A 笔记 §4.0、GTC §3.1 同结论）。Theorem 3（md L521）只保留 Assumptions 1–3(a)，其内容 $|\mathbb E[Y(t+1)\mid\mathcal F_t]-Y(t)|\le 1/(t+1)$ 对任意 0/1 序列成立（A 笔记 §4.8、GTC §3.5）。

**(b) 实际更新与定理的关系：桥梁是被假设的。** [原文] md L391：“Theorem 1, discussed next, implicitly assumes that (18) is able to enforce Assumption 5 ... In practical applications, while (18) cannot guarantee satisfaction of Assumption 5 in closed-loop, it still encourages it”。III.D2（md L513–515）：“just the expansion of the feasible state set alone cannot guarantee violation ... asymptotic convergence of $Y_i$ to $\alpha_i$ cannot be guaranteed, it still is encouraged by (18)”。md L671：“the ideal control policy in Assumption 5 is relaxed for the case study”。[推断] 原文给出的是“松弛方向与一步误差下降方向一致”的代数动机（GTC §2 的措辞更准确：不是“完全没有桥梁”，而是“方向一致性 + 设计动机”），不是 $h\to p$ 的可实现性定理；脚注 3（md L253）还承认松弛“does not automatically lead ... to ... violate”。

**(c) h 不能变号，0 是吸收点。** [原文] md L247：“$h_i<0$”；Assumption 4（md L297）：$h_i(t)=h_i(t-1)[1+K_i(t)]$，$K_i>-1$ 保证 $h_i<0$。[推断] 乘性更新下 $h(t)=h(0)\prod(1+K)$：不能跨过 0 进入收紧；有限步内到不了 0；若 $h=0$ 则永远为 0。越靠近 0 步长 $|h||K|$ 越小。本稿 tex L399 与 (eq total_problem_h) 写成 $h\le0$，允许取 0，在乘法律下 0 同样是吸收点；tex L440 的“preserves $h<0$ once $h_{t_0}<0$”只保证不变号，没有处理吸收问题。

**(d) Remark 7 的 ε 处理。** [原文] md L637：连续超标时“$h_i$ increases until $h_i\to0^-$ ... Mathematical violations can still persist ... These violations can be ignored during practical implementation, by adding a small $\varepsilon>0$ to $g_i$ ... considering violations only if $G_ix(t+1)>g_i+\varepsilon$ ... can be removed by the user when $h_i$ decreases sufficiently”。[推断] 这是在 $h$ 无法变号、找不到可达平衡点时，改变违约的定义来止损；它依赖后处理把状态夹在 $g-h$ 内（(24b)，md L625–627），并非定理结论。本稿共同设定把 ε 定为“事先声明的测量/求解容差”，与原文“运行中可由用户移除”的用法不同，不能引用 Remark 7 作为依据。

**(e) 算例中的手动覆盖与截断。** [原文] OP 时段“we restrict $h_i$ increase ... overriding (18) when required ... if $h_i$ increases it is reset manually to the previous value”（md L679）；物理截断 $h_1=\max(\mathrm{SOC_{max}}-1,h_1)$ 等（md L663，作者称实际未触发）；对照方法 SMPC Lit 同样人工重置（md L699）。[推断] 算例实际运行的是“(18) + 时段覆盖 + 截断 + 后处理”的组合，定理并不覆盖；年末 $Y=10.1\%$（md L685）也只是单一年份单一数据集的结果。

**(f) 其他需记住的原文事实。** 滤子只由扰动生成：$\mathcal F_t=\sigma(w(s):s<t)$（md L153、L325），预测 $c(t)$ 不在其中（A 笔记 §1.1）。md L588 称 Theorems 1–3“independent of the postprocessing framework, and still hold”，md L590 称对非线性系统同样成立；[推断] 字面成立，原因恰是定理与系统动态、MPC 和 $h$ 都无关。md L247 称“The initial value of h is not important”，没有证明。

### 1.4 对本稿的直接含义

1. tex L429 的 $1/(t+1)$ 论断成立，可以保留；更有力的补充是 1.1 的对数权重 $\ln(T/s)$ 与 $\sqrt{T_0}$ 延迟，它们说明问题不只是“新观测权重小”，而是“旧事件累计权重大、需要偿还欠账”。
2. tex L427 称 Ghosh Theorem 2 在理想输入下给出收敛，表述准确；但不能暗示 Ghosh 对实际 (18) 有保证（1.3 (b)）。
3. 本稿若改为“窗口作为考核指标、更新用 EV”，与 Ghosh 的关系应表述为：保留其“按违约反馈调整名义界、违约相对原始界判定、一步观测延迟”的框架（md L177、L305），替换其“全历史平均 + 乘法律 + 只能放松”的更新结构。这一替换本身是否构成贡献，见第 3 部分。

---

## 第 2 部分　证据账本（EV / W0 / G）

### 2.0 读法与来源缩写

- 现状取值：**成立**（证明完整，或有理数精确核验）；**条件成立**（依赖写明的额外假设）；**已更正/推翻**（后续报告更正或推翻，以最新更正为准）；**仅数值**（玩具或数值现象，不是数据中心证据）。
- 来源缩写（按新到旧）：
  - **RL** = `窗口风险指标路线_方案与理论.md`（2026-09-24）；**MRD** = `Model_Revision_Decisions.md` 第三优先级及 2026-09-24 更新；
  - **R2nd** = `opus_window_mechanism_study/窗口自适应机制_第二轮研究报告.md`（文首“重要更正”优先）；R1、R2、R3、P1、D1、D2、M1 = 其 `agent_notes/` 下同名笔记；
  - **FP** = `自适应窗口反馈_最终方案与数学性质.md`（2026-09-23，其“窗口反馈即创新点”定位已被 RL 与 MRD 2026-09-24 取代）；
  - **MG** = `merged_adaptive_update_math_study/自适应风险反馈_综合研究与最终推荐.md`（第 2 节更正表、第 7 节阈值结论）；
  - **SA** = `sadaptive_update_math_study/自适应窗口更新公式与数学性质研究.md`；GTC = 同目录 `Ghosh_Theory_Comparison.md`；
  - **OA** = `opus_adaptive_update_math_study/自适应窗口更新公式与数学性质研究.md`；A 笔记 = 同目录 `agent_notes/A_ghosh_theory_boundary.md`。
- 最后一列“新方向下是否有用”指：窗口定位为考核指标（M1–M5，RL §2），更新公式不必含窗口均值。

### 2.1 EV：投影逐事件加法 $r_t=\Pi[r_{t-1}+\kappa(\alpha-O_t)]$

| # | 结论 | 出处 | 现状 | 更正来源 | 新方向下是否有用 |
|---|---|---|---|---|---|
| EV1 | 逐路径区间恒等式 $\sum_{(a,b]}(O_t-\alpha)=(r_a-r_b+\sum c_t)/\kappa$；由此得双侧界，下侧残差 $C^+\le\kappa(1-\alpha)N^{lo}$，上侧 $C^-\le\kappa\alpha N^{hi}$ | OA §6.1；MG 定理 1 式 (4)–(6)；RL 定理 1 | 成立（MG：384 条路径、10,752 个区间精确核验） | — | **核心**。对任意 0/1 序列成立，不需要概率模型、MPC 最优或独立性 |
| EV2 | 每个盒形窗口 $Y^w_t\le\alpha+(r_{t-w}-l+Q^+_{(t-w,t]})/(\kappa w)$ | RL 推论 1a；MG 式 (7) | 成立（含残差形式） | OA 摘要写成 $Y^w\le\alpha+1/\rho$，MG §2 更正为“仅在窗口内无下侧投影时” | **核心**：直接给考核指标 M1 的逐路径证书 |
| EV3 | 上侧投影（放松到顶）不削弱违约率上界，只可能使预算未用满 | OA；MG §2 | 成立 | — | 有用 |
| EV4 | 有界，单步 $\le\kappa\max(\alpha,1-\alpha)$；无零吸收；端点处一次相反观测即离开 | OA 性质 2；MG 命题 2；FP §4 | 成立，附限定：移动量为 $\kappa(1-\alpha)$ 或 $\kappa\alpha$ 仅当单步不跨越整个区间；“无吸收”只是算术性质，实际对象可能永远只给出同一种观测 | MG 命题 2 及其后说明 | 有用 |
| EV5 | 最少穿越步数 $\lceil(u-r)/(\kappa\alpha)\rceil$、$\lceil(r-l)/(\kappa(1-\alpha))\rceil$；α 小时放松慢、收紧快 | OA §6.2；MG 命题 2 | 成立 | — | 有用（限速、静默后首段长度设计） |
| EV6 | 平均条件违约概率高概率界 $\frac1L\sum p_t\le\alpha+\frac{r_a-l+C^+}{\kappa L}+\sqrt{\frac{\ln(1/\delta)}{2L}}$ | OA 性质 3；MG 定理 3；RL 定理 2 | 条件成立：需要概率模型和包含 $O$、$r$ 的信息流；区间须预先固定；多窗口同时成立要做并集校正 | P1（段起点预先固定）；RL §3.5（决策后信息流下储能 $p\in\{0,1\}$） | 次要；其对象与信息流见第 4 部分 |
| EV7 | 多约束并集界；储能上下界互斥，二者指示之和即“任一储能违约”的精确指示 | MG §6；RL 定理 3 | 成立（逐路径） | — | 有用（M5） |
| EV8 | 精确固定阈值 $O_t=\mathbf 1\{r_{t-1}>\theta\}$ 且 $J=[\theta-\kappa(1-\alpha),\theta+\kappa\alpha]\subseteq[l,u]$ 时：有限步进入 J，此后任意长度 L 的区间 $\lvert\bar O-\alpha\rvert\le1/L$ | OA 性质 5；MG 定理 4 | 条件成立：只对“实际递推 + 精确阈值响应”；数据中心 MPC 未证明此响应 | MG §7（“当前数据中心 MPC 并未证明这种响应关系”） | 可作机理说明，不能写成本文系统的性质 |
| EV9 | “储能下限必须 $\le-\kappa(1-\alpha)$” | OA §1.1、命题 5 | **已更正**：只是阈值模型下覆盖整个 J 的充分条件，不是必要条件（反例：下限 $-0.8\kappa$ 仍精确） | MG §2、§7.2；共同设定 | 只能作充分条件的设计参考 |
| EV10 | “零下界使违约率退化为 1/2” | OA 性质 5 | **已更正**：仅在 $l=\theta$、$r_0=\theta$、$u\ge\theta+\kappa\alpha$、$0<\alpha\le1/2$ 时成立；α=0.7 时为 3/4 | MG §7.1；共同设定 | 只作警示 |
| EV11 | 内部状态下限取 $l-\kappa(1-\alpha)$、输出另行截断到 $[l,u]$（EB 形式）时，储能可取 $l=0$，阈值情形下精确 | R2 §6.2；RL §5 | 仅数值 + 条件（阈值玩具中复现），未做一般证明 | R1、R3 复现 | 可作储能通道的实现选项，需实测 |
| EV12 | 单调响应且满足全区间强回复条件时的均方界 $\mathbb E(r_t-r_*)^2\le(1-2\kappa\mu)^t(\cdot)+\kappa v/(2\mu)$ | OA §6.5；MG §8 | 条件成立（理想响应层；证明正确，不缺 $\kappa^2L_F^2$ 项） | MG §2 第 7 行、§8 | 有限。储能事件在决策后确定，静态响应假设不成立 |
| EV13 | 均值代理线性化稳定 $0<\kappa k<2$；非对称耦合时稳定域为精确圆盘 $\lvert1-\kappa\lambda_J\rvert<1$ | MG 式 (17)；R3 §4.2 | 条件成立（标量或线性代理，无额外延迟） | R3 §3.3：额外延迟 d 时比例通道临界降为 $2\sin\frac{\pi}{2(2d+1)}$；EV 是同构的单积分器，适用同一式（[推断]） | 有用（增益设计，与 W0 对比） |
| EV14 | 同一块长下，W0 在稳定增益约束下的最坏证书容差下限约为 EV 的 $2/a_c(w)$ 倍（w=2880 时约 1167 倍） | P1 定理 5.1、推论 5.2；R2nd §2.3 | 条件成立（A1–A4：标量静态响应、线性稳定、比较的是最坏证书而非性能） | P1 §5.3 自注：同增益时两者长期容差相同，区别在稳定上限 | **有用**：说明“窗口做指标、EV 做更新”的证书更紧 |
| EV15 | 若要求对所有二值路径、所有 $t\ge w$ 都有精确逐窗口恒等式，增量必须等于 $\kappa(\alpha-O_t)$ 加一个以 w 为周期、周期内和为零的项 | D2 定理 E3 | 成立（相邻窗口相减即得） | — | **有用**：逐窗口精确证书只能来自逐事件更新，直接支撑新方向 |
| EV16 | EV 的 $r$ 同样记住全部历史：最近窗口相同的两条历史，$r$ 可相差整个区间 D；只有投影会截断旧记忆 | R1 C5、C6 | 成立（精确分解） | — | **有用，须写清**：“近期”属于考核指标和证书，不属于 $r$ 的记忆；EV 相对 G 的优势是每个新事件的影响恒为 κ（见 1.1），不是遗忘 |
| EV17 | ρ=10 作为默认值，以及由此得到的“30 天窗口率不超过 0.2”等数值 | OA §6.8 | **已更正**：只是示例 | MG §2、§3.2；共同设定 | 不可直接引用 |
| EV18 | 下侧饱和说明“目标不可达” | OA | **已更正**：只作诊断线索；积分饱和、耦合、噪声都可产生大残差 | MG §2；R1 §9 | 须如实报告 $Q^+$、$Q^-$，不作结论 |
| EV19 | 玩具对比：公平增益 EV($\Delta_P$) 在静默首段（80 对 225 步）、θ 阶跃、斜坡、不可达后锁死（1.5 天对 21 天）上优于 TB-PI；DC 结构玩具中 B1 无窗口延迟，比 W0 平稳 | R3 §4.3；R2nd 更正 2；M1 §6 | 仅数值 | R3 更正了 R2nd 初稿中不公平的慢增益基线 | 只能用来设计实验，不能写成结论 |

### 2.2 W0：窗口均值投影加法 $r_t=\Pi[r_{t-1}+\kappa_t(\alpha-Y^w_t)]$

| # | 结论 | 出处 | 现状 | 更正来源 | 新方向下是否有用 |
|---|---|---|---|---|---|
| W1 | 含首尾项恒等式 $\bar O_N-\alpha=\frac{r_0-r_N}{\kappa N}+\frac{\sum q}{\kappa N}-\frac{B_N}N$，$\lvert B_N\rvert\le\frac{w-1}2$（可取等） | SA 定理 1；FP 式 (5)–(8)；MG 式 (16) | 成立，但 $N\approx w$ 时首尾项约 1/2，单窗证书几乎无信息 | P1 定理 1.1 给出更紧的窗口原生版本 | 作为对照方法的性质有用 |
| W2 | 窗口原生恒等式（无首尾项）$\frac1L\sum(Y^w_t-\alpha)=(r_a-r_b+\sum q)/(\kappa L)$，等价于宽 $2w-1$ 的三角核加权违约率 $\bar Y^{(w)}$ 的证书 | P1 定理 1.1–命题 1.4；RL 推论 1b | 成立（常数不可改进；有理数核验） | — | 有用，但证书对象是 $\bar Y^{(w)}$，**不是**单个盒形窗口率 |
| W3 | “PA-Y 不能给出任何窗口级逐路径保证” | OA §6.6 推论 | **已更正**：过强；可给含残差的界（MG），对核加权率有线性阶证书（P1） | MG §2 第 11 行；P1；RL | 以 W2、W4 的精确说法替代 |
| W4 | 只依赖当前窗口率、且在 α 附近 Lipschitz 的更新，对**单个盒形窗口率**的逐路径界最好只有平方根阶 $\sqrt{2(D+Q^+)/(\kappa w)}$，且构造可达 | D2 命题 E1、E2 | 成立（证明 + 构造） | — | **有用**：若以盒形窗口率为考核对象，W0 天然吃亏 |
| W5 | $\kappa n_t/w$ 启动与“α 预填窗口 + 固定增益”逐步相同，恒等式从 t=1 起成立 | R1 §2；P1 命题 2.3；D2 (d) | 成立 | 更正了 FP/SA“不能直接除以最终 κ、从 t=w 起适用”的过度保守说法 | 有用（启动写法） |
| W6 | 均值代理线性稳定 $0<\kappa k<2w\sin^2\frac{\pi}{2w}\sim\frac{\pi^2}{2w}$ | SA 定理 3；FP 式 (12)；MG 式 (18) | 条件成立（标量静态响应、无延迟、无耦合）；P1 补齐了根连续性等证明步骤 | P1 §4；R1 §7（吸引域可以任意小） | 有用（W0 作对照时的选参依据） |
| W7 | W0 = 全历史逐事件积分 + 最近 $w-1$ 步的斜坡；“基于近期运行表现”不能作为数学依据 | R1 C5、C6 | 成立（精确分解）；FP 的解释被部分推翻 | R1 §6 | **有用**：W0 并不比 EV 更“近期”，只是把每个事件的作用摊在 w 步内，带来约 $w/2$ 的时滞 |
| W8 | 窗口锚定 $r_t=\Pi[r_{t-w}+\kappa w(\alpha-Y^w_t)]$ 无截断时与 EV 逐路径相同 | D1 §4；D2 E3；M1 W2 | 成立 | — | 有用：公式里出现 $Y^w$ 不等于窗口反馈 |
| W9 | 精确阈值响应下 W0 的违约序列只取决于 $l/\kappa$、$u/\kappa$（尺度不变，与增益无关）；$l=\theta$ 时渐近周期 2905、每周期 312 步连续违约、长期率 0.1074；$l<\theta$ 时 0.10–0.167、连续 576 步（w=2880，α=0.1） | P1 §6；R1 §4 | 条件成立（精确阈值模型；有理数精确复现） | — | 作为 W0 的已证缺陷披露（RL §4.4） |
| W10 | “下界只是工程选择，储能可取 l=0”；“上下界可独立于风险目标确定” | FP §2 | **已推翻**：阈值模型下 l 决定长期率；u 决定静默后首段长度 $\alpha w+\sqrt{2uw/\kappa}$ | R1 §4、§5 | u、l 必须与风险目标联合确定 |
| W11 | 停在下界时离开需窗口计数降到 $\lceil w\alpha\rceil-1$，按论文参数最长可达 2593 个时隙（约 27 天） | P1 定理 3.2 | 成立 | — | 有用（披露） |
| W12 | 超过稳定临界时累计率仍约为 α，窗口率在约 0.01–0.2 间摆动 | R1 §8；D2 | 仅数值（玩具） | — | **有用**：“累计达标不等于近期达标”，支撑以窗口作考核指标 |
| W13 | 逐通道满足稳定条件不充分；非对称耦合可失稳 | R1 §9；R2 §5.1 | 条件成立（线性反例）+ 仅数值（非线性玩具 g≥3 失稳） | — | 有用（多约束须估联合 Jacobian） |
| W14 | 下侧残差大 = 目标不可达 | FP §5 | **部分推翻**（可达时也会出现） | R1 §9 | 同 EV18 |
| W15 | 完整窗口、固定增益、$p=p(m)$ 连续时存在不变分布，且 $\mu-\alpha=\mathbb Eq/\eta$ | SA §7.2 | 条件成立（仅存在性，不含唯一性与收敛） | — | 作用小 |
| W16 | 若条件概率始终在固定区间 $[\ell,u]\subset(0,1)$ 内，任何策略下 $\mathbb E[\lvert Y^w_{t+1}-\alpha\rvert\mid\mathcal F_t]\ge\min(\ell,1-u)/w$，且 $\liminf Y^w=0$、$\limsup Y^w=1$ a.s. | SA 式 (17) 及其后 | 条件成立（需非退化概率模型） | — | **有用**：任何更新律都不能承诺窗口率精确收敛，只能给证书与经验跟踪 |
| W17 | DC 结构玩具中 W0（30 天）有月尺度慢振荡，温度阶跃后 86 天恢复 | M1 §0、§6 | 仅数值 | — | 只作实验设计参考 |
| W18 | “旧图长周期摆动由窗口造成” | OA §6.6 | **已更正**：只是假说，无日志证据 | MG §2 最后一行 | 不得写入论文 |
| W19 | （跨候选）把有限窗口作为**反馈信号**，在所有被测性质上都没有可证明或可测的优势；盒形窗口进积分通道带来时滞，进比例通道带来回声 | R2nd 文首更正；R2；R3 | 由证明与玩具共同支撑；R2nd 初稿“TB-PI 优于非窗口机制”已被推翻 | R3 §4；M1 §9 | **新方向的直接依据** |

### 2.3 G：Ghosh 原乘法（及稿件窗口乘法 $G_w$）

| # | 结论 | 出处 | 现状 | 更正来源 | 新方向下是否有用 |
|---|---|---|---|---|---|
| G1 | Theorem 1–2 只对理想事件策略成立；(18) 能否实现理想策略是被**假设**的（md L391）；III.D2 只给方向一致性（md L513–515） | A 笔记 §4–5；GTC §2–3；本笔记 1.3 | 成立（逐条对原文核实） | GTC 的更准确措辞：“不是完全没有桥梁，而是方向一致性加设计动机” | 有用（相关工作定位） |
| G2 | Theorem 2 的结论为真，有更简单的确定性证明：有限步后 $\lvert Y^*_t-\alpha\rvert\le1/(2t)$ | A 笔记 §4.6；GTC §3.4 | 成立 | — | 只作背景 |
| G3 | Remark 4 未证 $\inf=0$；Remark 5 的“弱假设”不成立（约 40–50% 的时刻 $Y\in\kappa'$）；Lemma 2 推理不成立；Theorem 2 缺偏离上界 | A 笔记 §4.2–4.6 | 逻辑缺口成立；40–50% 为仅数值 | GTC：结论本身不因此否定 | 论文无需展开 |
| G4 | Theorem 3 的 martingale-like 性质对任意 0/1 序列成立，不是实际更新的保证 | A 笔记 §4.8；GTC §3.5；SA §3 | 成立 | — | 有用（避免误引） |
| G5 | $\beta=0$ 回避条件被算例的 α=0.1、0.05、0.15 违反 | A 笔记 §4.7 | 成立 | — | 无关 |
| G6 | 乘法律 $h$ 不能变号，0 是吸收点，不能收紧 | A 笔记 §3.4；OA §6.7；SA §4.2；MG | 成立（逐路径算术） | — | **有用**：换成有符号加法的主要理由之一（M1 玩具中温度通道 l=0 的机制长期率停在 0.65，属仅数值） |
| G7 | 全历史 $Y$ 的新观测敏感度为 $1/(t+1)$；一阶近似下 $\ln\lvert h(T)\rvert$ 以权重 $\ln(T/s)$ 累加旧事件，旧事件权重最大；工况切换后响应延迟约 $\propto\sqrt{T_0}$；存在“欠账偿还”驱动 | 本笔记 1.1（tex L429 只写了第一句） | 成立（代数；一阶近似误差 $O(1/\gamma^2)$）；数值示例为算术演示 | — | **核心动机论据** |
| G8 | $G_w$：退出位中点修正被精确消去，实际零点为 $a_w=\alpha+(\alpha-\tfrac12)/(w-1)$ | SA 式 (1)–(2)；OA §6.7 | 成立（代数；均值表述需平稳） | — | 作用小（$G_w$ 已弃用） |
| G9 | $G_w$：乘法曲率使平稳均值 $\mu\le a_w$（偏保守） | SA 式 (3)；OA §6.7 | 条件成立（严格平稳、可积；不证明稳态存在） | — | 作用小 |
| G10 | $G_w$：长时段无违约时指数 windup（30 天约 $e^{28.7}$ 倍）；w=1 时反馈恒为 $\alpha-\tfrac12$；$\alpha\le1/(2w)$ 时分子恒非正；$t<w$ 时未定义 | OA §5、§6.7；SA §4.2 | 成立（算术） | — | 有用（替换理由） |
| G11 | $G_w$ 在对数尺度上等价于窗口积分器，稳定需 $\lvert h\rvert F'w/\chi\lesssim4.93$；“可能是图 5 摆动的原因” | OA §6.6 | 前半条件成立（线性代理）；后半**只是假说** | MG §2 最后一行 | 前半可作对照说明，后半不得写入 |
| G12 | 储能贴边时 $\lvert h\rvert$ 指数塌缩而违约率不降（0.29 或 1.0）；DC 结构玩具中 B0 在温度通道失效（0.65）、储能上限摆到上界 | OA 性质 5（agent B）；M1 §6 | 仅数值 | — | 只作实验设计参考 |
| G13 | 稿件现有 Proposition 1–3 与 Theorem 1：“一步最优”不能归给实际 h 更新；投影可翻转符号（w=10、α=0.1、[0.3,0.6] 反例）；内区间下界不是系统固有差距；常概率上界不是算法上界 | GTC §5；SA §9；MG §10 | 成立（反例与逻辑核查） | — | 有用：改写或删除 tex L446–493 的依据 |
| G14 | 原文算例是“(18) + OP 时段人工覆盖 + 物理截断 + 后处理”，定理不覆盖；ε 处理是运行中对违约定义的修改 | A 笔记 §6–7；本笔记 1.3 (d)(e) | 成立 | — | 有用（相关工作定位） |

### 2.4 对新方向最关键的六条

1. 以盒形窗口率为考核对象时，EV 对**每一个**窗口给出线性阶逐路径证书（EV2）；W0 只对三角核率有线性阶证书，对盒形率最好只有平方根阶（W2、W4）；逐窗口精确恒等式只能来自逐事件更新（EV15）。
2. 在同一稳定性约束下，W0 的最坏证书容差下限约为 EV 的 $2/a_c(w)$ 倍（EV14，条件成立）。
3. W0 与 EV 的 $r$ 都记住全部历史（EV16、W7）。“对新工况越来越慢”针对的是 G 的 $1/t$ 敏感度与 $\ln(T/s)$ 权重（G7），EV 与 W0 都没有这个问题。
4. 已撤回或须限定、不得原样使用：EV9、EV10、EV17、EV18、W3、W10、W14、W18、G11 后半、R2nd §4.3 静默行。
5. 所有“阈值模型”“静态响应”“线性代理”结论（EV8、EV12、EV13、W6、W9）都未在数据中心 MPC 上验证。
6. 所有玩具结果（EV19、W12、W17、G12）只能用于实验设计，不能写成论文结论。

---

## 第 3 部分　聚焦文献定位：投影逐事件更新是否为标准结构

### 3.0 检索方式与核实程度

- 检索范围只限四个领域：随机逼近、在线分位数跟踪、自适应保形推断及其后续、SMPC 自适应约束收紧。
- **原文核实**：ACI 与 Conformal PID 下载 PDF 全文后逐句核对公式与命题；Rolling RC 下载 arXiv v7 核对更新式与定理；DAD-MPC、DAD-DPC、Conformal Decision Theory 通过 arXiv HTML 核对更新式与定理结构；Robbins–Monro 核对了摘要。
- **DOI**：用 Crossref API 逐条核实（下表“DOI”列标“已核实”）。NeurIPS、JMLR、TMLR、PMLR 论文一般没有 DOI，注明“无 DOI”。
- **未能读原文**：Oldewurtel et al. 2013 与 Muñoz-Carpintero et al. 2018 付费墙；其更新式分别依据 Ghosh 原文的转述（md L87、L695–699）和 Long & Xie (2020) 的转述，已标注“转述”。

符号对照：本稿 $r$ 越大越放松、违约越多。ACI 的 $\alpha_t$、Rolling RC 的 $\theta_t$ 与 $r$ 同向（参数越大，集合越小或风险越高）；Conformal PID 的 $q_t$ 与 $r$ 反向（$q=-r$ 时公式一致）。

### 3.1 同构公式与已知保证

| 领域 | 工作（作者，年份，出处） | 与 EV 同构的公式 | 已知保证与所需条件 | DOI |
|---|---|---|---|---|
| 随机逼近 | H. Robbins, S. Monro, “A Stochastic Approximation Method,” *Ann. Math. Statist.* 22(3):400–407, 1951 | 求 $M(x)=\alpha$ 的根，$M$ 为单调未知响应均值：$x_{n+1}=x_n+a_n(\alpha-y_n)$（标准表述；摘要已核对，全文未在本轮直接读取） | 衰减步长（$\sum a_n=\infty$、$\sum a_n^2<\infty$）、$M$ 单调且有根时 $x_n\to\theta$（依概率）。需要概率模型与单调响应 | 10.1214/aoms/1177729586（已核实） |
| 随机逼近（常步长跟踪） | H. J. Kushner, G. G. Yin, *Stochastic Approximation and Recursive Algorithms and Applications*, 2nd ed., Springer, 2003；A. Benveniste, M. Métivier, P. Priouret, *Adaptive Algorithms and Stochastic Approximations*, Springer, 1990 | 同上，但 $a_n\equiv\kappa$；带投影的版本 $\Pi_B[\cdot]$ | 常步长用于跟踪时变根：迭代停留在根的 $O(\sqrt\kappa)$ 均方邻域（弱收敛意义），$\kappa$ 权衡噪声与滞后。需要响应模型与稳定性条件（MG §8 的均方界即此类结果的一个实例） | 10.1007/b97441；10.1007/978-3-642-75894-2（均已核实） |
| 在线分位数估计 | L. Tierney, “A Space-Efficient Recursive Procedure for Estimating a Quantile of an Unknown Distribution,” *SIAM J. Sci. Stat. Comput.* 4(4), 1983 | Robbins–Monro 用于分位数：$\hat q_{n+1}=\hat q_n-a_n(\mathbf 1\{X_{n+1}\le\hat q_n\}-p)$ 类形式 | i.i.d. 下的一致性（衰减步长） | 10.1137/0904048（已核实） |
| 自适应保形推断 | I. Gibbs, E. Candès, “Adaptive Conformal Inference Under Distribution Shift,” NeurIPS 34, 2021 | 式 (2)：$\alpha_{t+1}:=\alpha_t+\gamma(\alpha-\mathrm{err}_t)$，$\mathrm{err}_t=\mathbf 1\{Y_t\notin\hat C_t(\alpha_t)\}$。**与 EV 逐字同构** | Proposition 4.1：以概率 1 对所有 T，$\big\lvert\frac1T\sum_{t=1}^T\mathrm{err}_t-\alpha\big\rvert\le\frac{\max\{\alpha_1,1-\alpha_1\}+\gamma}{T\gamma}$，**对数据生成过程不作任何假设**。关键是 Lemma 4.1：$\alpha_t\in[-\gamma,1+\gamma]$，依据是 $\alpha_t<0$ 时集合为全集（必不出错）、$\alpha_t>1$ 时为空集（必出错）。附录 A.1：该更新即 pinball 损失上的在线次梯度下降 | 无正式 DOI（ACM DL 标识 10.5555/3540261.3540389） |
| 同上（与窗口的关系） | 同上，§2 | 式 (3)：$\alpha_{t+1}=\alpha_t+\gamma(\alpha-\sum_{s\le t}w_s\mathrm{err}_s)$，$w_s$ 递增、和为 1，原文称其“more directly evaluating the recent empirical miscoverage frequency” | 原文：“In practice, we find that (2) and (3) produce almost identical results”，(3) 只是更平滑；实验用“local coverage frequencies … over the most recent two years”评价。**即：逐事件更新 + 近期窗口作为评价指标** | — |
| 同上（后续） | I. Gibbs, E. Candès, “Conformal Inference for Online Prediction with Arbitrary Distribution Shifts,” *JMLR* 25(162):1–36, 2024（DtACI） | 在 ACI 之上在线调步长（多步长专家聚合） | 摘要：以往方法“suffer from over-weighting historical data and thus may fail to quickly react”；给出对**所有给定宽度的局部时间区间**的小 regret 保证 | 无 DOI |
| 同上（后续） | A. N. Angelopoulos, E. Candès, R. J. Tibshirani, “Conformal PID Control for Time Series Prediction,” NeurIPS 36, 2023 | 式 (7) quantile tracking：$q_{t+1}=q_t+\eta(\mathrm{err}_t-\alpha)$，即 pinball 损失的常步长在线次梯度法；附录 B.1 指出 ACI 是其特例 | Proposition 1：分数 $s_t\in[-b,b]$ 的任意序列，$\big\lvert\frac1T\sum(\mathrm{err}_t-\alpha)\big\rvert\le\frac{b+\eta}{\eta T}$（对任意 $\eta,T$）。另有带饱和函数的误差积分器（Prop. 2）与风险控制推广（Prop. 3，条件为 $q\le-b$ 时集合为空、$q\ge b$ 时为全集） | 无 DOI |
| 同上（与本稿动机直接相关） | 同上，GOOGL 实验与讨论 | 核加权积分器 $r_t\big(\sum_i(\mathrm{err}_i-\alpha)K(\cdot)\big)$，特例为长度 w 的尾随窗口 | 原文：积分器“measures coverage errors accumulated over all time—and by the end of a long sequence, the marginal coverage can still be close to 1−α even if the local coverage deviates more wildly. This can be addressed by using a local version of the integrator”。**这正是用户对全历史反馈的顾虑，在该文中以讨论和展望的形式出现，未给出窗口积分器的理论** | — |
| 在线风险控制 | S. Feldman, L. Ringel, S. Bates, Y. Romano, “Achieving Risk Control in Online Learning Settings,” *TMLR*, 2023（Rolling RC） | 式 (5)：$\theta_{t+1}=\theta_t+\gamma(l_t-r)$，原文称“exactly that of ACI … extended to our more general setting” | Theorem 1：存在 $m,M$，$\theta>M$ 时集合为全集、$\theta<m$ 时为空集，损失有界且 $L(y,\mathcal Y)<r<L(y,\emptyset)$，则长期风险受控；有限样本偏差为 $C/T$，$C=M-m+4\gamma B$。对任意分布序列成立 | 无 DOI |
| 在线保形（步长） | A. N. Angelopoulos, R. F. Barber, S. Bates, “Online Conformal Prediction with Decaying Step Sizes,” ICML 2024, PMLR 235:1616–1630 | 同一更新，步长衰减 | 对任意序列保留回溯覆盖保证；分布稳定时还能估计总体分位数 | 无 DOI |
| 控制中的保形决策 | J. Lekeufack, A. N. Angelopoulos, A. Bajcsy, M. I. Jordan, J. Malik, “Conformal Decision Theory: Safe Autonomous Decisions from Imperfect Predictions,” ICRA 2024, pp. 11668–11675 | 式 (5)：$\lambda_{t+1}=\lambda_t+\eta(\varepsilon-\ell_t)$，$\lambda$ 是控制器的保守程度参数（如规划的安全距离） | Theorem 1：在“eventually safe”假设下（存在 $\lambda^{\rm safe}$，参数越过它后在 K 步内风险足够低），实际平均损失 $\le\varepsilon+O(1/t)$，常数取决于 $\lambda_1-\lambda^{\rm safe}$、$\eta$、K；对任意（含对抗）序列成立 | 10.1109/ICRA57147.2024.10610041（已核实） |
| 控制中的 ACI | A. Dixit, L. Lindemann, S. X. Wei, M. Cleaveland, G. J. Pappas, J. W. Burdick, “Adaptive Conformal Prediction for Motion Planning among Dynamic Agents,” L4DC 2023, PMLR 211:300–314 | 用 ACI 在线校准多步预测的不确定集，并放入 MPC | 覆盖保证针对预测集（含观测延迟），不直接针对闭环约束违约 | 无 DOI |
| SMPC 自适应收紧 | F. Oldewurtel, D. Sturzenegger, P. M. Esfahani, G. Andersson, M. Morari, J. Lygeros, “Adaptively Constrained Stochastic MPC for Closed-Loop Constraint Satisfaction,” ACC 2013, pp. 4674–4681 | （转述，md L695–699）乘法收紧 $\tilde h(t)=\tilde h(t-1)[1-(\alpha-Y(t)+\frac{2\alpha-1}{2t})/\gamma]$，$Y$ 为全历史平均 | 在理想（一步可控）策略假设下直观论证时间平均违约率收敛（Ghosh md L91：“argued intuitively without rigorous proof”） | 10.1109/ACC.2013.6580560（已核实） |
| 同上 | D. Muñoz-Carpintero, G. Hu, C. J. Spanos, “Stochastic MPC with Adaptive Constraint Tightening for Non-Conservative Chance Constraints Satisfaction,” *Automatica* 96:32–39, 2018 | （转述）投影随机逼近 $\beta(k+1)=\Pi_B[\beta(k)+\eta(k)y(k)]$，衰减步长；按“ε 与时间平均违约率之差”调整（Long & Xie 2020 转述；Ghosh md L87） | 时间平均违约率依概率收敛到“最不保守”水平；需要先验分布假设与状态趋于终端区域（md L87；Long & Xie） | 10.1016/j.automatica.2018.06.026（已核实） |
| 同上 | M. Korda, R. Gondhalekar, F. Oldewurtel, C. N. Jones, “Stochastic MPC Framework for Controlling the Average Constraint Violation,” *IEEE TAC* 59(7):1706–1721, 2014 | 损失的加权时间平均驱动放松与收紧，配合 SRCI 集（md L79） | 平均损失的期望界或概率 1 鲁棒界、收敛时间界；需要分布或统计量先验 | 10.1109/TAC.2014.2310066（已核实） |
| 同上 | J. Fleming, M. Cannon, “Time-Average Constraints in Stochastic MPC,” ACC 2017, pp. 5648–5653 | 按违约次数或凸损失的时间平均自适应放松 | 有限时间鲁棒界；需要二次代价与鲁棒不变集等（md L81） | 10.23919/ACC.2017.7963834（已核实） |
| 同上（建筑） | Y. Long, L. Xie, “Iterative Learning Stochastic MPC with Adaptive Constraint Tightening for Building HVAC Systems,” IFAC-PapersOnLine 53(2):11577–11582, 2020 | 投影随机逼近，迭代（日）维度更新 | 依概率收敛（Kushner–Yin） | 10.1016/j.ifacol.2020.12.636（已核实） |
| **SMPC 中的逐事件更新（最接近的已有工作）** | J. Shi, C. N. Jones, “Disturbance-adaptive Model Predictive Control for Bounded Average Constraint Violations,” arXiv:2503.24169（v4 注明为已录用 IFAC 论文的扩展版；具体会议未确认） | 式 (7)：$\alpha_t=\alpha_{t-1}+\eta(\alpha-v_t)$，$v_t\in\{0,1\}$ 为违约指示；只对施加值截断 $\bar\alpha_t=\min(\max(\alpha_t,0),1)$，由 $\bar\alpha_t$ 决定扰动集 $\mathcal W(\bar\alpha_t)$ | Lemma 2（望远镜）：$\sum_{i\le t}v_i/t=\alpha+(\alpha_0-\alpha_t)/(t\eta)$。借鲁棒不变集保证递归可行和 $\alpha_t$ 有下界，得渐近界 $\lim\sum v_i/t\le\alpha$；若 $\alpha_0\le\alpha_{\rm low}$，得对所有 t 的鲁棒界 $\sum v_i/t\le\alpha$。原文未引用 ACI | arXiv，无 DOI |
| 同上（建筑） | J. Shi, C. Salzmann, C. N. Jones, “Disturbance-Adaptive Data-Driven Predictive Control: Trading Comfort Violations for Savings in Building Climate Control,” arXiv:2412.09238（v4，2026-02；未见录用信息） | 式 (3)：同一更新 $\alpha_t=\alpha_{t-1}+\eta(\alpha-v_t)$，用于舒适度违约 | 在备份控制器性质（Property 1）下保证渐近平均违约界；BOPTEST 四个案例与一栋实际建筑验证；报告全程累计平均 | arXiv，无 DOI |
| 本稿母文献 | A. Ghosh et al., “Adaptive Relaxation-Based Nonconservative Chance Constrained Stochastic MPC,” *IEEE TCST* 33(5), 2025 | 式 (18) 乘法、全历史平均 | 见第 1 部分 | 10.1109/TCST.2025.3547260（已核实） |
| 其他（仅列出） | A. Capone, T. Brüdigam, S. Hirche, *IEEE TAC* 70(2):736–750, 2025；F. Oldewurtel et al., ACC 2015, pp. 931–936 | 回归式在线收紧；SC-OPF 中的自适应收紧 | — | 10.1109/TAC.2024.3433988；10.1109/ACC.2015.7170853（均已核实） |

### 3.2 统一的证明结构（[推断]，但每一步都对应上表原文）

所有“对任意序列成立”的长期平均保证（ACI Prop. 4.1、Conformal PID Prop. 1、Rolling RC Thm 1、DAD-MPC Lemma 2 + Thm 1、Conformal Decision Theory Thm 1）都由两步组成：
1. **望远镜恒等式**：$\sum_{(a,b]}(\alpha-O_t)=(r_b-r_a)/\kappa$（无截断时）。这一步对任何序列都成立，本稿 EV1 即此式加投影残差。
2. **参数有界**：
   - ACI、Rolling RC、Conformal PID 依靠**端点强制**：参数越界时集合变成全集或空集，事件被强制为 0 或 1，于是参数自行弹回，不需要投影；
   - DAD-MPC 依靠鲁棒不变集给出下界；Conformal Decision Theory 依靠“安全参数” $\lambda^{\rm safe}$；
   - 本稿 EV 依靠显式投影，代价是残差 $C^+$ 进入界。

**端点强制在本稿中的对应物（[推断]）。** 若存在“安全水平” $\theta_0$，使 $r_t\le\theta_0\Rightarrow O_{t+1}=0$，且下限 $l\le\theta_0-\kappa(1-\alpha)$，则下侧投影永不触发：$r_t>\theta_0$ 时一次违约只把 $r$ 降到 $>\theta_0-\kappa(1-\alpha)\ge l$；$r_t\le\theta_0$ 时下一步不违约，$r$ 只增不减。于是 $C^+\equiv0$，EV2 对每个窗口给出无残差的 $Y^w_t\le\alpha+(r_{t-w}-l)/(\kappa w)$。这正是 ACI Lemma 4.1 与 Conformal Decision Theory“安全参数”在本稿中的实例，也说明 EV9 的“$l\le-\kappa(1-\alpha)$”（取 $\theta_0=0$）是**充分**条件，与已更正的结论一致；R2nd T3 的“安全水平”假设是同一结构。

**不能直接移用的部分。** ACI 与 Conformal PID 的 regret 解释依赖“反事实事件关于参数单调”，即存在与参数无关的临界值 $\beta_t$，使 $\mathrm{err}_t=\mathbf 1\{\alpha_t>\beta_t\}$。闭环 MPC 中，下一状态依赖整个 $r$ 历史，不同 $r$ 下的反事实事件没有定义，这一单调结构不成立。所以本稿只能移用第 1、2 步的逐路径保证，不能移用 regret 或局部区间 regret 保证（DtACI）。

### 3.3 判断：相对 Ghosh 改了什么，哪些是标准结构，可能的系统贡献

**(a) 相对 Ghosh 的具体修改（EV 对 G）**

| 项 | Ghosh (18) | EV |
|---|---|---|
| 反馈信号 | 全历史平均 $Y(t)$ 加中点修正 $\frac{2Y-1}{2(t+1)}$ | 最新单个事件 $O_t$ |
| 更新结构 | 乘法 $h(t)=h(t-1)[1+K/\gamma]$，无单位 | 加法 $r_t=r_{t-1}+\kappa(\alpha-O_t)$，$\kappa$ 与约束同单位 |
| 参数域 | $h<0$，只能放松，0 吸收 | $r\in[l,u]$，可跨过名义界收紧，显式投影并记录残差 |
| 对新事件的敏感度 | $1/[\gamma(t+1)]$，随 t 衰减（1.1） | 恒为 $\kappa$ |
| 理论对象 | 理想事件策略下 $Y^*\to\alpha$ | 实际更新的逐路径恒等式与每窗口证书；概率界另列 |
| 窗口 | 无 | 作为考核指标与证书对象（M1–M5），不进入更新 |
| 初值 | $h(0)$ 须非零（由经验给定） | $r_0=0$ 即可启动 |
| 时段人工覆盖 | 算例中 OP 时段人工重置（md L679） | 不需要；若加入须计入残差 |

**(b) 属于已有标准结构的部分（不得宣称首次）**

1. EV 递推本身：常步长 Robbins–Monro，即 pinball 损失的在线次梯度法，与 ACI (2)、quantile tracking (7)、Rolling RC (5)、Conformal Decision Theory (5) 逐字同构；**在 SMPC 中已有同构使用并附平均违约界**（Shi & Jones 的 DAD-MPC 与 DAD-DPC，后者就是建筑热舒适应用）。
2. 逐路径望远镜恒等式及 $O(1/(\kappa L))$ 长期平均界：与上述各文的证明相同。
3. 投影保证有界：投影随机逼近的标准做法（Kushner–Yin；Muñoz-Carpintero 等）。“内部状态不截断、只截断施加值”在 DAD-MPC 中已有（$\alpha_t$ 与 $\bar\alpha_t$ 分离），与 R2 的 EB 形式同构（[推断]）。
4. 鞅差 Azuma–Hoeffding 平均条件概率界：标准工具。
5. 以违约时间平均代替逐点机会约束：Oldewurtel 2013、Korda 2014、Muñoz-Carpintero 2018、Fleming & Cannon 2017、Ghosh 2025 均已采用。
6. “全历史累积反馈使局部表现变差，应改为局部或窗口”：Conformal PID 的讨论与 DtACI 的摘要都已明确指出；ACI 已用近期窗口覆盖率作评价指标。

**(c) 本数据中心应用可能形成的系统贡献（均需以实验支撑，措辞不得含“首次”）**

1. **异质事件机理下的多通道实现**：同一经济 MPC 中，储能上下界的违约由决策后确定的指令产生（阈值型响应），出水温度的违约由控制模型与实际对象失配产生；两类通道共用一种事件反馈，名义界与物理界分离，允许双向调整。已有同构工作（DAD-MPC、DAD-DPC）调节的是扰动集或置信水平，本稿调节的是名义状态界；这一差别本身不构成新理论，价值在于数据中心中的集成与验证。
2. **以窗口指标为考核对象的可审计风险核算**：M1–M5 加上对实际日志逐时刻可核验的证书曲线（EV2 含残差），以及储能“安全水平”条件的具体化（$\theta_0=0$ 需要 MPC 可行、储能递推精确、容差 ε 事先声明），使下侧残差为零时证书无残差。工具是标准的，贡献在于针对本系统的条件核查与报告方式。
3. **对 Ghosh 类全历史乘法反馈在长期运行中灵敏度衰减的定量分析**（1.1 的 $1/t$、$\ln(T/s)$ 权重、$\sqrt{T_0}$ 延迟、欠账偿还），并在数据中心的季节、负载阶跃、预测误差漂移中与 EV 实测对照。分析本身很短，但直接回应了为何替换母文献的更新律。
4. 若实际案例中出现，可报告仅看累计率或窗口率会漏掉的聚集与静默后集中违约（RL 的 C3）。

**(d) 必须通过新实验才能成立的主张**

1. EV 在本系统中的成本不劣于 W0 与 G，服务质量（SLA）不受损；
2. 实际闭环中各通道窗口率满足预先设定的 $\alpha+\epsilon_Y$，最长连续违约与超限积分可接受（RL §6 的判据须先定后跑）；
3. 工况切换后 EV 的恢复快于 G，且 G 的延迟随运行时长增长（验证 1.1 的 $\sqrt{T_0}$ 规律是否在实际数据中显现）；
4. 储能的安全水平 $\theta_0=0$ 在实现中成立（无不可行、无名义软化、容差设定合理），从而下侧残差为零；温度通道是否需要 $l<0$ 以及幅度；
5. 多通道耦合下的稳定性（需估计联合灵敏度）；
6. 下侧、上侧残差在实际日志中的大小；
7. 若论文要与 DAD 类方法比较优劣，必须实际对比，不能只凭结构论证。

---

## 第 4 部分　理论适用边界

### 4.1 稿件时序下两类通道的事件性质

依据共同设定的时序与稿件：tex L510“the current state and current exogenous inputs are measured”；Algorithm 1 输入含“realized $\xi_t$”（tex L571）；供电侧“可再生优先”修正只改 $(p^r,p^{gd})$（tex L562、L577–586），储能指令按 MPC 首步执行。

- **储能（决策后确定事件）。** 储能递推精确，执行的指令就是 MPC 首步，所以 $E_{t+1}=E_{t+1|t}$。MPC 约束为 $E_{t+1|t}\le E_{\max}+r_t$，因此 $O_{t+1}=\mathbf 1\{E_{t+1|t}>E_{\max}+\varepsilon\}$ 由时刻 t 的优化解直接决定：**违约等于 MPC 选择使用放松带**。给定决策时刻的全部信息（含 $\xi_t$），事件是确定的。[推断] 因此对储能而言，α 实际是“允许使用放松带的时间比例预算”，而不是不确定结果的概率上限；Ghosh 的储能违约来自后处理吸收预测误差（md L619–633），本稿没有这一机制，两者含义不同。
- **温度（失配驱动事件）。** 控制模型递推是确定的；实际违约只来自控制模型与实际对象的失配，而实际对象在稿件中没有定义（tex L560–564 只说有“cooling-model mismatch”和“thermal-state mismatch”）。若失配本身是确定的（例如用高保真仿真器作实际对象），温度事件在决策时同样是确定的；只有给失配一个随机模型，事件才有非退化的条件概率。

### 4.2 三层结论及其所需条件

| 层 | 需要什么 | 本笔记与账本中的结论 | 对数据中心两类通道的含义 |
|---|---|---|---|
| **L1 逐路径** | 只需实际记录的二值序列 $O$ 与按公式存储的 $r$；人工覆盖、冻结、软化须计入残差 | EV1–EV5、EV7（并集界）、EV15、EV16；W1、W2、W4、W5、W7、W8、W11；G7 的代数部分（$1/t$ 敏感度、$\ln(T/s)$ 权重） | 对储能、温度、联合事件都成立；与事件是否随机、MPC 是否最优或可行、通道是否耦合都无关。**只是记账，不保证 $r$ 能把违约率调到 α** |
| **L2 需要概率模型** | 一个概率空间和明确的信息流 $\mathcal F_t$，$p_{t+1}=\mathbb P(O_{t+1}=1\mid\mathcal F_t)$；段起点预先固定 | EV6（平均条件概率高概率界）、W16（非退化概率下窗口率不能精确收敛）、Ghosh Theorem 3、稿件 Assumption 1–2 与 Proposition 1–3、一切“机会约束”表述 | 储能在决策时刻信息下 $p\in\{0,1\}$，L2 界退化为 L1；在决策前信息下 $p$ 非退化，但需要 $\xi_t$ 条件分布的模型（稿件未给）。温度需要失配的随机模型 |
| **L3 需要 MPC 响应联系** | $r\mapsto O$ 的响应假设：固定阈值、静态单调 $F(r)$ 加回复条件、线性化斜率、安全水平 $\theta_0$、切换后频率 $p'$ 等 | EV8、EV12、EV13、EV14、W6、W9、W13；3.2 的安全水平结论；1.1 的 $\sqrt{T_0}$ 延迟（假设切换后在当前 $h$ 下频率为 $p'$）；Ghosh Theorem 1–2 与 (18) 的桥梁 | 储能的响应是阈值型且依赖荷电状态、电价、终端价值与任务，不是静态 $F(r)$；安全水平 $\theta_0=0$ 需要 MPC 在 $r\le0$ 时可行、无名义软化、容差 ε 事先声明。温度的响应取决于未定义的失配；$\theta_0=-\beta$ 需要失配有界 β |

五种必须区分的量落在哪一层：
- 单时刻条件违约概率 $p_{t+1}$：只在 L2 有定义，且依赖所选信息流；本稿任何层都不给出逐时刻保证。
- 有限窗口经验违约率 $Y^w_t$：L1 给出每窗口证书（EV2 对盒形率；W2 对三角核率）。
- 长期累计经验违约率：L1（$L=T$ 情形）。
- 一段时间内平均条件违约概率：L2（EV6）。
- 多约束联合违约：L1 并集界（EV7）；是否能由一个共同 MPC 动作同时实现各边际目标属于 L3，未证明。

### 4.3 稿件第 444 行 $\mathcal F_t$ 不含当期 $\xi_t$ 的影响

tex L444：“Let $\mathcal F_t=\{\xi_1,\xi_2,\dots,\xi_{t-1}\}$ represent the set of historical uncertainties up to time $t-1$ … $Z_{t+1}^{i,w}$ is random under $\mathcal F_t$”；tex L447、L463 用它定义 $p_{t+1}=P(O_{t+1}=1\mid\mathcal F_t)$。

1. **形式问题。** $\mathcal F_t$ 写成随机变量的集合，应写成它们生成的 σ-代数；若温度对象含 $\xi$ 以外的扰动，或预测不是过去数据的确定函数，这些也要放进 $\mathcal F_t$。
2. **$p_{t+1}$ 是决策前概率。** 控制器在时刻 t 已测得 $\xi_t$ 再求解 MPC（tex L510、L571），而 $\mathcal F_t$ 不含 $\xi_t$。所以 $p_{t+1}$ 是“对控制器已经看到的当期外生量取平均”后的概率：$p_{t+1}=\mathbb P\big(\text{MPC 在看到 }\xi_t\text{ 后会选择（储能）或导致（温度）违约}\mid\xi_{1:t-1}\big)$。这是一个合法的量，但不是控制器决策时面对的风险。
3. **储能的后果。** 在决策时刻信息 $\mathcal G_t=\sigma(\mathcal F_t,\xi_t,\text{预测},s_t)$ 下，储能事件是 $\mathcal G_t$ 可测的（$p\in\{0,1\}$）；在稿件的 $\mathcal F_t$ 下 $p$ 非退化，但完全来自 $\xi_t$ 的条件分布与 MPC 对 $\xi_t$ 的响应。稿件没有给这个条件分布（也不打算给），所以 $p_{t+1}$ 无法计算或检验。
4. **对 Assumption 1 的影响。** 要求 $P(O_{t+1}=1\mid\mathcal F_t)\in\{0,1\}$，意味着理想策略对所有可能的 $\xi_t$ 都产生同一事件，这比“能决定下一事件”更强；若改用 $\mathcal G_t$，储能事件天然是 0/1，存在性变得平凡，但实际 MPC 并不按理想策略选择事件。两种读法下，Theorem 1 都不是实际更新的性质（G1、G13）。
5. **对 Assumption 2 与 Proposition 1–3 的影响。** 控制器在看到 $\xi_t$ 后才行动，它选择的是从 $\xi_t$ 到动作的映射，不能直接“选择”一个关于 $\mathcal F_t$ 的条件概率；$p_{t+1}$ 由该映射与 $\xi_t$ 的条件分布共同决定。“控制器可在区间内选择 $p$”在这一信息结构下没有直接含义。
6. **不受影响的部分。** 鞅差 $O_{t+1}-p_{t+1}$ 在该信息流下仍是鞅差：只要 $O_{t+1}$ 是 $\mathcal F_{t+1}$ 可测（所有随机性都在 $\xi$ 中，预测与对象是其确定函数），Azuma–Hoeffding 类的 EV6 形式上成立，对象是“决策前平均条件概率”。L1 的全部结论与 $\mathcal F_t$ 的选择无关。
7. **既有报告的一致判断。** OA §6.4 与 RL §3.5 已指出：决策后信息流下储能 $p\in\{0,1\}$，决策前信息流下 $p$ 才非平凡，论文必须写明采用哪一个。稿件第 444 行实际采用的是决策前版本，但没有说明。

**建议写法（供主 agent 取舍）。** 新方向下正文以 L1 为主：窗口指标定义、EV 更新、逐路径恒等式与每窗口证书（含残差）、并集界。若保留概率表述，需要：把 $\mathcal F_t$ 写成决策前 σ-代数，并说明它包含哪些量；说明 $p_{t+1}$ 对当期 $\xi_t$ 取平均；说明储能在决策时刻信息下事件确定，其 α 是放松带使用时间的预算。L3 结论（阈值、静态响应、线性稳定、安全水平）只以带假设的命题或设计说明出现，并在案例中核查假设（例如记录 $r\le0$ 时是否出现储能违约、MPC 是否不可行或被软化）。


