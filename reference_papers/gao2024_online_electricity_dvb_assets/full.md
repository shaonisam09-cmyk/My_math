# Online Electricity Purchase for Data Center with Dynamic Virtual Battery from Flexibility Aggregation

Kekun Gao, Yuejun Yan, Yixuan Liu, Endong Liu, and Pengcheng You

Abstract— As a critical component of modern infrastructure, data centers account for a huge amount of power consumption and greenhouse gas emission. This paper studies the electricity purchase strategy for a data center to lower its energy cost while integrating local renewable generation under uncertainty. To facilitate efficient and scalable decision-making, we propose a two-layer hierarchy where the lower layer consists of the operation of all electrical equipment in the data center and the upper layer determines the procurement and dispatch of electricity. At the lower layer, instead of device-level scheduling in real time, we propose to exploit the inherent flexibility in demand, such as thermostatically controlled loads and flexible computing tasks, and aggregate them into virtual batteries. By this means, the upper-layer decision only needs to take into account these virtual batteries, the size of which is generally small and independent of the data center scale. We further propose an online algorithm based on Lyapunov optimization to purchase electricity from the grid with a manageable energy cost, even though the prices, renewable availability, and battery specifications are uncertain and dynamic. In particular, we show that, under mild conditions, our algorithm can achieve bounded loss compared with the offline optimal cost, while strictly respecting battery operational constraints. Extensive simulation studies validate the theoretical analysis and illustrate the tradeoff between optimality and conservativeness.

## I. INTRODUCTION

Data centers, serving as the crucial infrastructure within the contemporary digital economy, are experiencing a rapid increase in energy demand owing to their continuously expanding scale. As large energy consumers, data centers spend nearly 30% to 50% percent of their operating costs on energy bills, where a significant proportion of the electricity consumed originates from the electricity spot market. Due to the huge energy consumption of data centers, a 1% improvement in power efficiency would save millions of dollars for the cloud service providers, which has led to growing attention on minimizing energy costs through optimizing spot market transactions. To address this issue, one feasible option is to use the flexibility of data centers power equipments to reduce energy costs and absorb green power generation. Currently, data centers are commonly equipped with renewable energy generation, yet their energy supply is highly influenced by climatic conditions, rendering it intermittent and difficult to predict [1], [2]. Furthermore, electricity prices in the spot market fluctuate in real time, and the demand-side flexibility of data centers has great randomness. Faced with the randomness of flexible loads, volatility of electricity prices, and intermittency of renewable energy, minimizing the data center in spot market energy cost in the absence of future statistical knowledge poses a challenging problem.

There have been numerous related works addressing the optimization of electricity procurement for data centers. Some existing works use storage and price changes to address time-dimensional shifts in energy demand [3], [4]. Some works formulate these problems as a stochastic optimization problem and introduce Lyapunov optimization technologies to make real-time decisions. [5] and [6] take the uncertainty of electricity price and load into consideration during optimization, a two-time scale power market is considered in [7], and Zhang et al. investigate the problem of energy management for data centers with renewable resources and energy storages to minimize electricity cost by leveraging the diversity of these system states [8]. However, these works either fail to consider energy storage or only consider real battery-based energy storage. In other words, they do not aggregate other flexible loads and energy storage available in data centers to form virtual batteries, resulting in under-utilization of the flexibility of data center loads.

In reality, a data center comprises various heterogeneous classes of flexible loads. We can integrate these loads in the data center into system-level operation and control them by constructing a simple and user-friendly model. Among various modeling methods, the virtual battery model gains its popularity due to its simple and compact form. It aims to quantify the aggregate flexibility while considering the physical constraints of each individual load. Specifically, the virtual battery model constitutes a scalar linear system, akin to a simplified representation of battery dynamics, with parameters denoting charge and discharge power constraints, and energy capacity limits. Studies have demonstrated the ability of virtual battery models to effectively capture the aggregate flexibility of diverse classes of flexible loads. [9], [10], and [11] model the aggregate flexibility of deferrable tasks and Thermostatically Controlled Loads (TCLs), respectively. Hao et al. first consider four types of flexible loads: TCLs, distributed energy storages, residential pool pumps, and electric vehicles, and for each type of load, they derive a closed-form approximation for the aggregate flexibility set based on the individual load parameters [12]. [13] further considers the heterogeneous arrival time of deferrable loads, and also tries to get the close-form battery approximation.

In [14] and [15], the authors simplify the problem of identifying battery parameters to a polynomial complexity, which further contributes to the application of the virtual battery in the distributed energy resources’ flexibility management. Therefore, aggregating the individual flexible loads of the data center as a virtual battery can significantly improve the rationality of electricity purchase and reduce energy costs.

The objective of this paper is to devise an online strategy for managing the power purchasing policy of data centers that integrate time-varying virtual batteries and renewable energy resources, with the aim of minimizing long-term costs while meeting the energy demand. By leveraging Lyapunov optimization techniques, we develop an online algorithm to address this problem. We demonstrate that the proposed algorithm achieves close-to-optimal performance even in the presence of time-varying parameters of the virtual battery, without requiring prior knowledge of statistics. The cost incurred is at most higher than the optimal algorithm by $O ( 1 / V )$ , where $V$ is a controllable parameter balancing the trade-off between energy cost and virtual battery capacity. Furthermore, we provide explicit solutions for control actions under current system dynamics, ensuring the efficiency of our algorithm. Finally, the simulation results validate the effectiveness of the proposed algorithm.

## II. SYSTEM MODEL

Consider the system operation of a data center in a discrete-time horizon $t \in \{ 0 , 1 , 2 , \ldots \}$ , which uses electricity power from a utility grid and local renewable generation to meet its energy demand. We divide the overall demand into two categories: one is controllable, e.g., TCLs of cooling systems and flexible computing tasks of IT equipment, and the other is non-controllable including necessary power to support basic services, uninterruptible computing tasks with priority, etc. We propose to aggregate controllable demand into virtual batteries such that the flexibility can be exploited in a computationally efficient way to manage electricity purchase. A schematic diagram of the system model is given in Figure 1. We develop the detailed model for each component of the system below.

![](images/6e9b2bb8a7b079a987ec5d3fd02f766e331b36763ab88db72a6f270ed085c0d1.jpg)  
Fig. 1: Electricity flows in a data center with virtual batteries.

## A. Virtual Battery

In this subsection, we use TCLs and flexible computing tasks, the two most common loads in the data center, as examples to show how virtual batteries can be aggregated. In particular, a virtual battery is specified by a tuple of parameters $( B _ { c h a r } ( t ) , B _ { d i s } ( t ) , B _ { m i n } ( t ) , B _ { m a x } ( t ) , \alpha )$ where $B _ { c h a r } ( t ) / B _ { d i s } ( t )$ is the charge/discharge rate limit, $B _ { m i n } ( t ) / B _ { m a x } ( t )$ is the lower/upper bound of battery capacity, and α is the dissipation rate. Given such a specification, a virtual battery is modeled by two variables - the battery Stateof-Charge (SoC) $B ( t )$ and the energy injection/withdrawal $U ( t )$ - subject to the following:

$$
- B _ {d i s} (t) \leq U (t) \leq B _ {c h a r} (t), \quad \forall t,\tag{1a}
$$

$$
B (t + 1) = \alpha B (t) + U (t), \quad \forall t,\tag{1b}
$$

$$
B _ {m i n} (t) \leq B (t) \leq B _ {m a x} (t), \quad \forall t.\tag{1c}
$$

TCL: The role of a TCL, mainly a cooling system, of the data center is to maintain required room temperatures for IT equipment. Such a requirement is typically captured by a set of thermodynamics equations and operational constraints:

$$
\theta (t + 1) = \alpha \theta (t) + (1 - \alpha) (\theta_ {a} (t) + c r (t) - b p (t)),\tag{∀t,}
$$

(2a)

$$
\theta_ {r} - \Delta \leq \theta (t) \leq \theta_ {r} + \Delta , \quad \forall t,\tag{2b}
$$

$$
0 \leq p (t) \leq p _ {m}, \quad \forall t.\tag{2c}
$$

Here the variables are the room temperature $\theta ( t )$ and the operating power $p ( t )$ of the TCL. The external inputs include the operating power of IT devices in the room $r ( t )$ and the ambient temperature $\theta _ { a } ( t )$ . The parameters include the room temperature setpoint $\theta _ { r }$ with a tolerance gap Δ, the maximum TCL power $p _ { m }$ , the coefficient c that reflects the temperature rise per unit of IT power, and the coefficient b that reflects the temperature drop per unit of the TCL power [12], [16].

Note that there exists a nominal value $p _ { o } ( t )$ for $p ( t )$ that can set the immediate room temperature to the setpoint $\theta _ { r }$ given by

$$
p _ {0} (t) = \frac {\theta_ {a} (t) + c r (t) - \theta_ {r}}{b}.
$$

By defining

$$
B (t) := \frac {\theta_ {r} - \theta (t)}{(1 - \alpha) b}, \quad U (t) := p (t) - p _ {o} (t),
$$

we obtain

$$
- p _ {0} (t) \leq U (t) \leq p _ {m} - p _ {0} (t), \quad \forall t,\tag{3a}
$$

$$
B (t + 1) = \alpha B (t) + U (t), \quad \forall t,\tag{3b}
$$

$$
- \frac {\Delta}{(1 - \alpha) b} \leq B (t) \leq \frac {\Delta}{(1 - \alpha) b}, \quad \forall t.\tag{3c}
$$

It is obvious from (3) that the flexibility of a TCL can be represented as a virtual battery, parameterized by

$$
\left(p _ {m} - p _ {o} (t), p _ {o} (t), - \frac {\Delta}{(1 - \alpha) b}, \frac {\Delta}{(1 - \alpha) b}, \alpha\right).
$$

Delay-Tolerant Computing Task: A lot of SQL or machine learning tasks are flexible with the processing schedule, as long as they are completed before a specified deadline. Consider a set of such tasks, each (task j) parameterized by $( a ^ { j } , d ^ { j } , \bar { L } ^ { j } , E ^ { j } )$ , denoting the arrival time, the deadline, the power consumption corresponding to the maximum processing speed, and the total energy required to complete the task, respectively. Let $L ^ { j } ( t )$ be the allocated power to task $j$ at time t. Then the task can be characterized as

$$
0 \leq L ^ {j} (t) \leq \bar {L} ^ {j}, \quad \forall t \in [ a ^ {j}, d ^ {j})\tag{4a}
$$

$$
L ^ {j} (t) = 0, \quad \forall t \notin [ a ^ {j}, d ^ {j})\tag{4b}
$$

$$
\sum_ {t} L ^ {j} (t) = E ^ {j}.\tag{4c}
$$

The aggregate flexibility of this set of tasks can also be approximated as a virtual battery [12]. Define in this case

$$
B (t) := \sum_ {\tau <   t} \sum_ {j} L ^ {j} (t), \quad U (t) := \sum_ {j} L ^ {j} (t).
$$

Obviously, $U ( t )$ cannot exceed the sum of the maximum power consumption of all active tasks, i.e.,

$$
0 \leq U (t) \leq \sum_ {j: a ^ {j} \leq t <   d ^ {j}} \bar {L} ^ {j}.\tag{5}
$$

Meanwhile, to guarantee that all active tasks at time t can be completed by their deadlines, the SoC $B ( t )$ has to achieve a minimum level given by

$$
B (t) \geq \sum_ {j: d ^ {j} \leq t} E ^ {j} + \sum_ {j: a ^ {j} \leq t <   d ^ {j}} \max \left\{E ^ {j} - (d ^ {j} - t) \bar {L} ^ {j}, 0 \right\}
$$

where we have accounted for the energy delivered for all finished tasks up until time t. Note that the lower bound implies that each active task $j$ will be allocated the maximum processing power from now on and can receive at most $( d ^ { j } -$ $t ) \bar { L } ^ { j }$ amount of energy. Similarly, we can derive an upper bound for the SoC $B ( t )$ as

$$
B (t) \leq \sum_ {j: d ^ {j} \leq t} E ^ {j} + \sum_ {j: a ^ {j} \leq t <   d ^ {j}} \min \left\{E ^ {j}, \left(t - a ^ {j}\right) \bar {L} ^ {j} \right\}.
$$

From above, the parameters of this aggregated virtual battery are available with a dissipation rate $\alpha = 1$

Remark 1. As shown from the two virtual battery examples, the parameterization is likely time-varying and uncertain, depending on random ambient temperatures and computing task arrivals, etc. Therefore, managing such dynamic virtual batteries from flexibility aggregation is more challenging than operating real batteries.

Note that multiple batteries can be readily aggregated into one [12]. Without loss of generality, we consider only one virtual battery with no dissipation $( \alpha ~ = ~ 1 )$ in this work for ease of presentation. The analysis and results also generalize to the concurrent management of multiple virtual batteries. Specifically, in our setting, electricity from both local renewable generation and the utility grid can be used to charge the virtual battery. We use $R _ { b } ( t )$ and $G _ { b } ( t )$ to denote respectively the electricity from the two sources. Besides, we define $B _ { e } ( t )$ to represent the energy withdrawal from the virtual battery to serve the non-controllable demand in the data center. Therefore, the characterization (1) of the virtual battery can be explicitly written as

$$
0 \leq G _ {b} (t) + R _ {b} (t) \leq B _ {c h a r} (t), \quad \forall t,\tag{6a}
$$

$$
G _ {b} (t) \geq 0, \quad R _ {b} (t) \geq 0, \quad \forall t,\tag{6b}
$$

$$
0 \leq B _ {e} (t) \leq B _ {d i s} (t), \quad \forall t,\tag{6c}
$$

$$
B (t + 1) = B (t) + G _ {b} (t) + R _ {b} (t) - B _ {e} (t), \quad \forall t,\tag{6d}
$$

$$
B _ {m i n} (t) \leq B (t) \leq B _ {m a x} (t), \quad \forall t.\tag{6e}
$$

Furthermore, simultaneous charge and discharge are not allowed:

$$
G _ {b} (t) + R _ {b} (t) > 0 \perp B _ {e} (t) > 0\tag{7}
$$

## B. Problem Formulation

Suppose the data center is equipped with local renewable generators, such as roof-top solar panels, which can provide an $R ( t )$ amount of renewable energy at time $t . ~ R ( t )$ is random in real time, but will always be upper bounded by solar panels’ inverter capacity $R _ { m a x } .$ , i.e.,

$$
0 \leq R (t) \leq R _ {m a x}.
$$

The electricity generated from renewables is divided into three parts: $R _ { e } ( t )$ is directly supplied to the data center to meet its (non-controllable ) demand; $R _ { b } ( t )$ can be stored in the virtual battery whenever necessary; the rest is just abandoned. This is captured by

$$
0 \leq R _ {e} (t) + R _ {b} (t) \leq R (t), \quad \forall t,
$$

$$
R _ {e} (t) \geq 0, \quad \forall t.\tag{8a}
$$

(8b)

Denote the (non-controllable) demand of the data center at time t as $E ( t )$ . In total, it is satisfied by a combination of electricity supply from the local renewable generation, the virtual battery, and the utility grid. We assume the utility grid is able to offer any amount of electricity that the data center requests to purchase, which yields

$$
E (t) = R _ {e} (t) + B _ {e} (t) + G _ {e} (t), \quad \forall t,\tag{9}
$$

where $G _ { e } ( t )$ denotes the supply from the utility grid that directly serves the energy demand. Meanwhile, the data center can also purchase electricity $G _ { b } ( t )$ from the utility grid to store in the virtual battery. Note that selling electricity back to the utility grid is temporarily not allowed, i.e.,

$$
G _ {e} (t) \geq 0, \quad \forall t.\tag{10}
$$

Let $P ( t ) \geq 0$ denote the unit price of electricity at time t. Then the corresponding energy cost for electricity purchase is

$$
C (t) = P (t) [ G _ {e} (t) + G _ {b} (t) ].\tag{11}
$$

The offline problem of the data center over a long (infinite) time horizon can be formulated to minimize the time-average energy cost while satisfying the energy demand at each time t:

$$
\begin{array}{l} \text { P1: } \min \lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \{C (t) \} \\ \text { s.t. } (6), (7), (8), (9), (1 0) \end{array}\tag{12}
$$

However, in practice, the electricity prices, renewable generation, and energy demand of data centers are all random but will be sequentially revealed online. Upon observing the current price $P ( t )$ renewable availability R(t), and energy demand $E ( t )$ , the data center has to make an irrevocable electricity purchase decision on $\{ R _ { e } ( t ) , R _ { b } ( t ) , G _ { e } ( t ) , G _ { b } ( t ) , B _ { e } ( t ) \}$ in real time without knowing exactly these future information.

## III. ONLINE ALGORITHM

## A. Problem Relaxation

In P1, the energy demand $E ( t )$ , electricity prices $P ( t )$ , and renewable availability $R ( t )$ are not known in advance until time t. Therefore, solving the problem directly is impractical. Meanwhile, we can observe that the decision variables $G _ { b } ( t )$ $G _ { e } ( t )$ , and $B _ { e } ( t )$ are subject to constraints imposed by the battery’s SoC at each time t, which results in the problem being temporally coupled and challenging to resolve. To address the above issues, we first relax it to ensure constraint satisfaction over the long term. Define the time-average rate of charge and discharge as follows:

$$
\overline {{G _ {b}}} = \lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} G _ {b} (t)\tag{13}
$$

$$
\overline {{R _ {b}}} = \lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} R _ {b} (t)\tag{14}
$$

$$
\overline {{B _ {e}}} = \lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} B _ {e} (t).\tag{15}
$$

Therefore, we have $\overline { { G _ { b } } } + \overline { { R _ { b } } } = \overline { { B _ { e } } }$ . This is because if our charging/discharging consistently satisfies the constraints of the original problem, it will also satisfy such equations in the long term. We first relax the problem P1 to obtain the problem P2:

$$
\mathbf {P 2}: \min _ {T \rightarrow \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \{C (t) \}\tag{16}
$$

$$
\mathrm{s.t.} (6 \mathrm{a}), (6 \mathrm{b}), (6 \mathrm{c}), (7), (8), (9), (1 0)
$$

$$
\overline {{G _ {b}}} + \overline {{R _ {b}}} = \overline {{B _ {e}}}.\tag{17}
$$

It is clear that P2 is the optimization problem obtained by relaxing the constraints of P1. Therefore, any solution satisfying the constraints of P1 is feasible for P2. The optimal value of P2 is guaranteed to be less than that of P1. Denote the optimal objective value of P1 as $Y ^ { O P T }$ and the optimal objective value of P2 as $Y ^ { R E L } ,$ , that is $Y ^ { R E L } \leq \dot { Y } ^ { O P T }$ Then we employ the Lyapunov optimization technique [17] to transform the offline optimization problem into an online optimization problem that is independent of the statistical properties of $E ( t ) , \ R ( t )$ , and $P ( t )$ , thereby obtaining an approximate solution to the original problem.

B. Decoupling and Real-Time Decision via Lyapunov Optimization

We employ the idea of Lyapunov optimization to enable real-time decisions. We define a virtual queue based on the SoC $B ( t )$ of the virtual battery as

$$
Q (t) = B (t) - \bar {B} _ {m i n} - V P _ {m a x} - B _ {d i s},\tag{18}
$$

which is essentially a constant shift from $B ( t )$ and where $\begin{array} { r l r } { B _ { d i s } } & { : = } & { \operatorname* { m a x } _ { t } B _ { d i s } ( t ) , \bar { B } _ { m i n } : = \operatorname* { m a x } _ { t } B _ { m i n } ( t ) } \end{array}$ and $P _ { m a x } : = \operatorname* { m a x } _ { t } P ( t )$ . Therefore, the virtual queue involves <sup>m</sup>according to

$$
Q (t + 1) = Q (t) + G _ {b} (t) + R _ {b} (t) - B _ {e} (t).\tag{19}
$$

The virtual queue can decompose the long-term constraints in problem P2 into each time t, thus removing the long-term constraints in P2. By setting specific relationships and weight parameter V , we can ensure that real-time decisions based on Lyapunov optimization satisfy all constraints of P1.

Before introducing our online algorithm, we design the Lyapunov function as $\begin{array} { r } { L ( Q ( t ) ) = \frac { 1 } { 2 } \bar { Q ^ { 2 } } ( t ) } \end{array}$ and the conditional <sup>2</sup>one-slot Lyapunov drift as follows:

$$
\Delta (Q (t)) = \mathbb {E} \{L (Q (t + 1)) - L (Q (t)) | Q (t) \}.\tag{20}
$$

Note that taking square of both sides of (19) yields

$$
\begin{array}{c} Q ^ {2} (t + 1) = Q ^ {2} (t) + [ G _ {b} (t) + R _ {b} (t) - B _ {e} (t) ] ^ {2} \\ \qquad + 2 Q (t) [ G _ {b} (t) + R _ {b} (t) - B _ {e} (t) ]. \end{array}
$$

Due to (6a), (6c) and (7), we have

$$
\begin{array}{l} [ G _ {b} (t) + R _ {b} (t) - B _ {e} (t) ] ^ {2} \\ = [ G _ {b} (t) + R _ {b} (t) ] ^ {2} + B _ {e} (t) ^ {2} - 2 [ G _ {b} (t) + R _ {b} (t) ] B _ {e} (t) \\ \leq [ G _ {b} (t) + R _ {b} (t) ] ^ {2} + B _ {e} (t) ^ {2} \\ \leq \max \{B _ {c h a r} ^ {2} (t), B _ {d i s} ^ {2} (t) \} \\ \leq \max \{B _ {c h a r} ^ {2}, B _ {d i s} ^ {2} \} \end{array}
$$

with $B _ { c h a r } : = \operatorname* { m a x } _ { t } B _ { c h a r } ( t )$ and $B _ { d i s } : = \operatorname* { m a x } _ { t } B _ { d i s } ( t )$ Define $\begin{array} { r } { B : = \frac { 1 } { 2 } \operatorname* { m a x } \{ B _ { c h a r } ^ { 2 } , B _ { d i s } ^ { 2 } \} } \end{array}$ . We can further have

$$
\frac {1}{2} (Q ^ {2} (t + 1) - Q ^ {2} (t)) \leq B + Q (t) [ G _ {b} (t) + R _ {b} (t) - B _ {e} (t) ].
$$

Taking the conditional expectation of both sides gives the following bound on the one-step Lyapunov drift:

$$
\Delta (Q (t)) \leq B + Q (t) \mathbb {E} \left\{G _ {b} (t) + R _ {b} (t) - B _ {e} (t) | Q (t) \right\}.\tag{21}
$$

By minimizing the Lyapunov drift plus the energy cost, we can lower the energy cost and simultaneously ensure the stability of the virtual queue. Instead of directly controlling the exact Lyapunov drift, we take a conservative alternative and limit its upper bound, i.e., the right-hand side of (21). To this end, we develop an online algorithm that decides $\{ R _ { e } ( t ) , R _ { b } ( t ) , G _ { e } ( t ) , G _ { b } ( t ) , B _ { e } ( t ) \}$ in real time based on the observation of $\{ R ( t ) , P ( t ) , E ( t ) \}$ by solving

$$
\begin{array}{r l} \mathbf {P 3 :} & \min [ Q (t) + V P (t) ] G _ {b} (t) + Q (t) R _ {b} (t) \\ & - [ Q (t) + V P (t) ] B _ {e} (t) - V P (t) R _ {e} (t). \\ & \text {s.t. (6a),(6b),(6c),(7),(8),(9),(10)} \end{array}\tag{22}
$$

where the objective reflects the weighted sum of the upper bound on the Lyapunov drift and the energy cost (with a constant shift). We summarize the proposed online algorithm in Algorithm 1:

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1 Online Algorithm
At each time t:
1. Observe  $R(t)$ ,  $P(t)$ ,  $E(t)$  and  $Q(t)$ ;
2. Determine  $(R_{e}^{*}(t), R_{b}^{*}(t), G_{e}^{*}(t), G_{b}^{*}(t), B_{e}^{*}(t))$  by solving P3;
3. Implement the decision and update  $B(t+1)$  following the dynamics (6d).
</div>

## C. Performance Analysis

Before summarizing the properties of the algorithm, we first analyze the optimal solution of P3.

Lemma 1. The optimal solution to P3 satisfies the following:

1) $I f Q ( t ) + V P ( t ) \leq 0$ holds, we have $B _ { e } ^ { * } ( t ) = 0 .$

2) $I f Q ( t ) > 0 h o l d s ,$ we have $G _ { b } ^ { * } ( t ) = 0$ and $R _ { b } ^ { * } ( t ) = 0 .$

See Appendix A in [18] for the proof. Lemma 1 contributes to more insights into Algorithm 1 as follows.

Theorem 1. If R(t), E(t), P (t), t, are i.i.d., Algorithm 1 achieves a time-average energy cost more than the optimum $Y ^ { O P T }$ by at most a constant $B / V , i . e .$

$$
\lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \{C (t) \} \leq Y ^ {O P T} + B / V.
$$

$$
B _ {c h a r} := \max _ {t} B _ {c h a r} (t)
$$

$$
\bar {B} _ {m a x} :=
$$

$$
B _ {m a x} (t), i f 0 <   V \leq V _ {m a x}
$$

$$
V _ {m a x} := \frac {\bar {B} _ {m a x} - \bar {B} _ {m i n} - B _ {d i s} - B _ {c h a r}}{P _ {m a x}},\tag{23}
$$

it is guaranteed that

$$
B _ {m i n} (t) \leq B (t) \leq B _ {m a x} (t), \quad \forall t,
$$

holds.

See Appendix B in [18] for the proof.

Remark 2. In Theorem 1, as V increases, the long-term average energy cost will get closer to the optimal value of the original problem P1. However, there exists an upper bound for the choice of V in order to guarantee the feasibility of virtual battery operation. It can always be achieved by setting a sufficiently small V . The tradeoff implies that a good estimate of $V _ { m a x }$ is necessary. In practice, even if we set $V > V _ { m a x } ,$ we can project any infeasible real-time decision onto the feasible region of the virtual battery given the latest observation.

## IV. NUMERICAL RESULTS

In this section, we evaluate the proposed algorithm through simulation experiments. We consider a total time duration of 30 days, where each time t corresponds to 1 hour. The electricity prices $P ( t )$ , fluctuate randomly within the range [0.5, 1.5]. The electricity price is in the unit of \$/kWh. The hourly energy requirement $E ( t )$ fluctuates within the interval [10000, 20000] in terms of kWh. The renewable energy generation $R ( t )$ fluctuates hourly within the range of [0, 3000] kWh. The maximum capacity $B _ { m a x } ( t )$ of the aggregated virtual battery fluctuates randomly within the range of 3000 kWh to 4000 kWh, while its minimum capacity $B _ { m i n } ( t )$ varies randomly within the range of 1000 kWh to 2000 kWh. The charging rate $B _ { c h a r } ( t )$ and discharging rate $B _ { d i s } ( t )$ can fluctuate arbitrarily between 100 kW and 200 kW. We assume that we know $\bar { B } _ { m a x } = 3 0 0 0 , \bar { B } _ { m i n } =$ $2 0 0 0 , B _ { c h a r } = 2 0 0 , B _ { d i s } = 2 0 0$ in advance. Hence we have $V _ { m a x } = 4 0 0 .$

Impact of parameter V . First, we observe how the parameter V influences the energy cost reduction. It is seen in Fig.2 that the average cost decreases with increasing V . The results align with our algorithmic performance demonstrated in Theorem 1. As intuitively expected, our algorithm discharges from the virtual battery during periods of high electricity prices and charges it during periods of low electricity prices. Additionally, it stores excess renewable energy for subsequent use when electricity prices become excessively high.

![](images/b88525d2ca7aaf43e1937fa1bcd54310c6eba2f12a7604db1637ac7f1a15044d.jpg)  
Fig. 2: Average energy cost with different V

Variation of virtual battery SoC. Then, we observe whether the virtual battery always remains between its upper and lower bounds under our algorithm, and under what circumstances it may exceed these bounds. When we know in advance $\bar { B } _ { m a x } , \bar { B } _ { m i n } , B _ { d i s } , B _ { c h a r }$ , and $P _ { m a x }$ , we can compute the upper limit of V , and set $V$ to its maximum attainable value. In this scenario, the virtual battery remains within its SoC bounds at all times. However, without prior knowledge of these parameters, we may not accurately determine the value of $V _ { m a x }$ . When we do not know the upper bound of $V ,$ , inappropriate selection of V may result in the SoC of the virtual battery exceeding its bounds. As illustrated in Fig.3, when we set V = 10 and V = 300, that is $V \leq V _ { m a x }$ , we observe that the SoC of the virtual battery remains within its bounds. However, when $V = 8 0 0 > V _ { m a x }$ it can be observed that the SoC of the virtual battery may exceed its limits. Therefore, by setting an appropriate V , we can achieve bounded loss compared with the offline optimal cost.

![](images/392ed96194c75e91686f01e9e4caa63256788699590356fb85d3e354b8f81b9d.jpg)  
Fig. 3: Variation of SoC under different settings of V

## V. CONCLUSIONS

In this work, we propose a two-layer hierarchy where the lower layer consists of the operation of all electrical equipment in the data center and the upper layer determines the procurement and dispatch of electricity to minimize the long-term time-averaged energy cost. We further design an online algorithm based on Lyapunov optimization which can assist data centers in purchasing electricity from the grid under the uncertainties and fluctuations in energy prices, renewable energy availability, and battery specifications. With the increasing parameter V , our algorithm can achieve a cost that is close to the offline optimal. Simulation results validate the theoretical analysis and show the effectiveness of our algorithm in reducing the long-term cost. In future work, we will consider a more realistic virtual battery model with dissipation.

## REFERENCES

[1] Yaqin Zhou, David KY Yau, Pengcheng You, and Peng Cheng. Optimal-cost scheduling of electrical vehicle charging under uncertainty. IEEE Transactions on Smart Grid, 9(5):4547–4554, 2017.

[2] Zhe Wang, Heng Zhang, Xianghui Cao, Endong Liu, Hongran Li, and Jian Zhang. Modeling and detection scheme for zero-dynamics attack on wind power system. IEEE Transactions on Smart Grid, 15(1):934– 943, 2024.

[3] Adel Nadjaran Toosi and Rajkumar Buyya. A fuzzy logic-based controller for cost and energy efficient load balancing in geo-distributed data centers. In 8th International Conference on Utility and Cloud Computing, pages 186–194. IEEE, 2015.

[4] Tianyi Chen, Aryan Mokhtari, Xin Wang, Alejandro Ribeiro, and Georgios B Giannakis. Stochastic averaging for constrained optimization with application to online resource allocation. IEEE Transactions on Signal Processing, 65(12):3078–3093, 2017.

[5] Michael J Neely, Arash Saber Tehrani, and Alexandros G Dimakis. Efficient algorithms for renewable energy allocation to delay tolerant consumers. In 1st International Conference on Smart Grid Communications, pages 549–554. IEEE, 2010.

[6] Xiaohan Wei and Michael J Neely. Data center server provision: Distributed asynchronous control for coupled renewal systems. IEEE/ACM Transactions on Networking, 25(4):2180–2194, 2017.

[7] Yuan Yao, Longbo Huang, Abhishek B Sharma, Leana Golubchik, and Michael J Neely. Power cost reduction in distributed data centers: A two-time-scale approach for delay tolerant workloads. IEEE Transactions on Parallel and Distributed Systems, 25(1):200–211, 2012.

[8] Guanglin Zhang, Shun Zhang, Wenqian Zhang, Zhirong Shen, and Lin Wang. Distributed energy management for multiple data centers with renewable resources and energy storages. IEEE Transactions on Cloud Computing, 10(4):2469–2480, 2020.

[9] Ashutosh Nayyar, Josh Taylor, Anand Subramanian, Kameshwar Poolla, and Pravin Varaiya. Aggregate flexibility of a collection of loads. In 52nd Conference on Decision and Control, pages 5600– 5607. IEEE, 2013.

[10] He Hao, Borhan M Sanandaji, Kameshwar Poolla, and Tyrone L Vincent. Aggregate flexibility of thermostatically controlled loads. IEEE Transactions on Power Systems, 30(1):189–198, 2014.

[11] He Hao and Wei Chen. Characterizing flexibility of an aggregation of deferrable loads. In 53rd Conference on Decision and Control, pages 4059–4064. IEEE, 2014.

[12] He Hao, Abhishek Somani, Jianming Lian, and Thomas E Carroll. Generalized aggregation and coordination of residential loads in a smart community. In 6th International Conference on Smart Grid Communications, pages 67–72. IEEE, 2015.

[13] Daria Madjidian, Mardavij Roozbehani, and Munther A Dahleh. Energy storage from aggregate deferrable demand: Fundamental tradeoffs and scheduling policies. IEEE Transactions on Power Systems, 33(4):3573–3586, 2017.

[14] Lin Zhao, Wei Zhang, He Hao, and Karanjit Kalsi. A geometric approach to aggregate flexibility modeling of thermostatically controlled loads. IEEE Transactions on Power Systems, 32(6):4721–4731, 2017.

[15] Feras Al Taha, Tyrone Vincent, and Eilyan Bitar. An efficient method for quantifying the aggregate flexibility of plug-in electric vehicle populations. arXiv preprint arXiv:2207.07067, 2022.

[16] Qiu Fang, Jun Wang, Qi Gong, and Mengxuan Song. Thermal-aware energy management of an hpc data center via two-time-scale control. IEEE Transactions on Industrial Informatics, 13(5):2260–2269, 2017.

[17] Michael Neely. Stochastic network optimization with application to communication and queueing systems. Springer Nature, 2022.

[18] Kekun Gao, Yuejun Yan, Yixuan Liu, Endong Liu, and Pengcheng You. Online electricity purchase for data center with dynamic virtual battery from flexibility aggregation. Tech. Rep. https://pengcheng-you.github.io/desires-lab/ papers/GYLLY\_YAC.pdf, 2024.