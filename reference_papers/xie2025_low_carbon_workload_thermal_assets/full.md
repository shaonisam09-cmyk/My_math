# Online Low-Carbon Workload, Energy, and Temperature Management of Distributed Data Centers

Rui Xie<sup>1,2</sup>, Yue Chen<sup>1\*</sup>, and Xi Weng<sup>3</sup>

<sup>1</sup>Department of Mechanical and Automation Engineering, The Chinese University of Hong Kong, Hong Kong, China <sup>2</sup>The Shun Hing Institute of Advanced Engineering, The Chinese University of Hong Kong, Hong Kong, China. <sup>3</sup>Guanghua School of Management, Peking University, China. \*Email: yuechen@mae.cuhk.edu.hk

Keywords: Data center; low-carbon energy management; online algorithm; renewable energy; uncertainty.

## Abstract

Data centers have become one of the major energy consumers, making their low-carbon operations critical to achieving global carbon neutrality. Although distributed data centers have the potential to reduce costs and emissions through cooperation, they are facing challenges due to uncertainties. This paper proposes an online approach to co-optimize the workload, energy, and temperature strategies across distributed data centers, targeting minimal total cost, controlled carbon emissions, and adherence to operational constraints. Lyapunov optimization technique is adopted to derive a parametric real-time strategy that accommodates uncertainties in workload demands, ambient temperature, electricity prices, and carbon intensities, without requiring prior knowledge of their distributions. A theoretical upper bound for the optimality gap is derived, based on which a linear programming problem is proposed to optimize the strategy parameters, enhancing performance while ensuring operational constraints. Case studies and method comparison validate the proposed method’s efectiveness in reducing costs and carbon emissions.

## 1 Introduction

With the explosive growth of cloud-based services, there has been a surge in the establishment of distributed data centers worldwide. Data centers are crucial for supporting informa-<sup>[</sup> tion technology (IT) infrastructure; however, their significant energy consumption also exerts additional environmental pressure. Data center energy consumption is expected to become one of the major components of global electricity demand by 2030 [1]. Therefore, the low-carbon operation of data centers plays an important role in pursuing the goal of carbon neutrality [2].

Some research works tried to reduce the energy consumption and operation cost of a data center by scheduling workloads. For example, a mixed duration task assignment and migration algorithm was developed in [3] based on deep reinforcement learning for energy-eficient purposes in data centers. A data and energy migration optimization method was proposed in [4] for the green operation of a mini data center. A hybrid quantum-classical energy management method was introduced in [5] to minimize the operation cost of a data center.

Recently, distributed data centers have drawn attention, which could be coordinated to further reduce the total operation cost. A day-ahead and intraday scheduling method for Internet data centers was proposed in [6], considering the power losses of uninterruptible power supply. A multiobjective optimization problem was formulated in [7] for the operation of geo-distributed data centers under time-ofuse electricity prices, solved by an evolutionary algorithm. The coordination of data centers and their participation in the electricity market were jointly optimized in [8] and solved by a polytope cutting algorithm. However, the aforementioned operation strategies either adopt a deterministic paradigm or rely on the predictions for uncertainties, jeopardizing their performances in a strongly uncertain environ ment without efective predictions.

We accommodate strong uncertainties in the optimization problem by using Lyapunov optimization technique [9], which is a prediction-free stochastic programming technique that applies to infinite-time problems with time average objective functions and constraints. Lyapunov optimization requires no prior knowledge of distributions and can provide theoretical guarantees for the optimality gap of the obtained strategy. Therefore, it shows strength in various scenarios such as energy sharing [10], mobile cloud ofloading [11], and market bidding [12].

Lyapunov optimization was applied to the workload and energy management of data centers in [13] and then improved in [14]. However, there are still research gaps: 1) Although the total operation cost is minimized by these methods, there are no direct limits for the carbon emissions, leading to high-emission low-cost strategies in some scenarios. 2) These methods neglect the cooling demand from temperature management, which actually constitutes a non-negligible part of the data centers’ energy consumption [1]. 3) The parameter decisions in [13] and [14] rely on careful theoretical deduction, making it dificult to consider emission limits and temperature constraints further.

Aiming to fill the above research gaps, this paper proposes a Lyapunov optimization-based online coordination method for the low-carbon operation of distributed data centers, which jointly manages workload, energy, and temperature. The novelty of this paper is two-fold:

1) The proposed method improves traditional predictionfree distributed data center coordination methods [13,14] by considering emission limits and temperature management. The proposed method is able to control the average emission according to the requirement and maintain the proper temperature using a cooling system, under the uncertainties of workload demands, ambient temperature, electricity prices, and carbon intensities.

2) A linear programming (LP) problem is developed to determine the parameters of the Lyapunov optimizationbased parametric operation strategy, which minimizes the optimality gap of the strategy while guaranteeing operational feasibility. Unlike the traditional parameter determination methods that rely on substantial theoretical analysis [13,14], the proposed approach is easier to use and more suitable for complex problems such as the considered one.

The rest of this paper is organized as follows: The system structure is introduced in Section 2. The problem formulation and the proposed online algorithm are developed in Section 3 and Section 4, respectively. Case study is presented in Section 5. Finally, Section 6 concludes the paper.

## 2 System Structure

The structure of the distributed data center system is illustrated in Figure 1, which consists of the front end and the back end. The workload demand first arrives at the mapping nodes in the front end. Then they queue here waiting for transfer to the back end. After arriving at data centers in the back end, the workloads queue before they are processed by the IT facilities in the data center. The queues in the front and back ends are both served in the first-in-firstout fashion. Besides IT facilities, there is also an energy storage system and a cooling system in each data center. Data centers are connected to the power grid at diferent buses.

![](images/f0fd349e033cf275a185306f6ba62a0517aea32456db797de80356e4dcfc97e5.jpg)  
Figure 1: The structure of the distributed data center system.

## 3 Problem Formulation

In this section, we first establish the component model of the distributed data center system, based on which the ofline optimization problem is provided. Finally, the stochastic program that we want to solve is formulated.

## 3.1 Component Model

The workload flow, energy storage system, cooling system, electricity cost, and carbon emission are modeled one by one in this subsection.

Let $\tau , \tau .$ , and $\mathcal { I }$ be the index sets of the time slots, mapping nodes, and data centers, respectively. Then for any time slot $t \in \tau$ , the workload flow constraints are as follows:

$$
0 \leq a _ {i t} ^ {F} \leq \alpha_ {i t} ^ {F}, \forall i \in \mathcal {I},\tag{1a}
$$

$$
0 \leq m _ {i j t} ^ {R} \leq M _ {i j} ^ {R}, \forall i \in \mathcal {I}, \forall j \in \mathcal {J},\tag{1b}
$$

$$
0 \leq p _ {j t} ^ {B} \leq P _ {j} ^ {B}, \forall j \in \mathcal {J},\tag{1c}
$$

$$
q _ {i t} ^ {F} \geq 0, \forall i \in \mathcal {I}, q _ {j t} ^ {B} \geq 0, \forall j \in \mathcal {J},\tag{1d}
$$

$$
q _ {i (t + 1)} ^ {F} = q _ {i t} ^ {F} + a _ {i t} ^ {F} - \sum_ {j \in \mathcal {J}} m _ {i j t} ^ {R}, \forall i \in \mathcal {I},\tag{1e}
$$

$$
q _ {j (t + 1)} ^ {B} = q _ {j t} ^ {B} + \sum_ {i \in \mathcal {I}} m _ {i j t} ^ {R} - p _ {j t} ^ {B}, \forall j \in \mathcal {J}.\tag{1f}
$$

In (1a), $\alpha _ { i t } ^ { F }$ denotes the workload demand arriving at mapping node i in time slot t, which is uncertain; $a _ { i t } ^ { F }$ is the workload demand that mapping node i accepts. In (1b), $m _ { i j t } ^ { R }$ is the workload transferred from mapping node i to data center $j ,$ whereas $M _ { i j } ^ { R }$ denotes the capacity of the link from i to $j$ . In (1c), $p _ { j t } ^ { B }$ is the workload processed at data center j in time slot t, which should be no larger than the capacity of the IT facilities at $j ,$ denoted by $P _ { j } ^ { B }$ . Constraint (1d) stipulates the nonnegativity of the queues in the front and back ends, denoted by $q _ { i t } ^ { F }$ and $q _ { j t } ^ { B }$ , respectively. Constraints (1e) and (1f) describe the dynamics of the queues. The operation cost caused by the workload flow is modeled as follows:

$$
f _ {t} ^ {W} = \sum_ {i \in \mathcal {I}} \sum_ {j \in \mathcal {J}} \gamma_ {i j} ^ {R} m _ {i j t} ^ {R} + \sum_ {i \in \mathcal {I}} \gamma_ {i} ^ {F} (\alpha_ {i t} ^ {F} - a _ {i t} ^ {F}),\tag{2}
$$

where the first term is the workload transfer cost, the second term is the penalty for rejecting workload demand, and $\gamma _ { i j } ^ { R }$ and $\gamma _ { i } ^ { F }$ are cost coeficients.

The constraints of the energy storage system in time slot t are as follows:

$$
0 \leq p _ {j t} ^ {S C} \leq P _ {j} ^ {S C}, 0 \leq p _ {j t} ^ {S D} \leq P _ {j} ^ {S D}, \forall j \in \mathcal {J},
$$

$$
\underline {{E}} _ {j} ^ {S} \leq e _ {j t} ^ {S} \leq \overline {{E}} _ {j} ^ {S}, \forall j \in \mathcal {J},\tag{3a}
$$

(3b)

$$
e _ {j (t + 1)} ^ {S} = e _ {j t} ^ {S} + p _ {j t} ^ {S C} \eta_ {j} ^ {S C} - p _ {j t} ^ {S D} / \eta_ {j} ^ {S D}, \forall j \in \mathcal {J}.\tag{3c}
$$

In $( 3 ) , p _ { j t } ^ { S C }$ and $p _ { j t } ^ { S D }$ are the charging and discharging power, respectively. The stored energy at the beginning of time slot t is denoted by $e _ { j t } ^ { S }$ . Therefore, constraints (3a) and (3b) stipulate bounds for these variables. Constraint $( \mathrm { 3 c } )$ models the change of stored energy, where $\eta _ { j } ^ { S C }$ and $\eta _ { j } ^ { \dot { S D } }$ are the eficiency coeficients. To consider battery degradation, the operation cost of the energy storage systems is formulated as follows:

$$
f _ {t} ^ {S} = \sum_ {j \in \mathcal {J}} \gamma_ {j} ^ {S} (p _ {j t} ^ {S C} + p _ {j t} ^ {S D}).\tag{4}
$$

The cooling system and the temperature control of the data center are modeled as follows:

$$
0 \leq p _ {j t} ^ {C} \leq P _ {j} ^ {C}, \forall j \in \mathcal {J},\tag{5a}
$$

$$
\underline {{T}} _ {j} ^ {H} \leq \tau_ {j t} ^ {H} \leq \overline {{T}} _ {j} ^ {H}, \forall j \in \mathcal {J},\tag{5b}
$$

$$
\tau_ {j (t + 1)} ^ {H} = \tau_ {j t} ^ {H} + \kappa_ {j} ^ {B} p _ {j t} ^ {B} - \kappa_ {j} ^ {C} p _ {j t} ^ {C} - \beta_ {j t} ^ {C}, \forall j \in \mathcal {J}.\tag{5c}
$$

Constraint (5a) bounds the power of the cooling system. Constraint (5b) is for the limits of the temperature $\tau _ { j t } ^ { H }$ in the data center. In (5c), the temperature changes are modeled, where the IT facilities increase the temperature and the cooling system lowers it with coeficients $\kappa _ { j } ^ { \hat { B } }$ and $\kappa _ { j } ^ { C }$ , respectively. The ambient temperature also afects $\tau _ { j t } ^ { H }$ , whose efect is represented by the uncertain term $\beta _ { j t } ^ { C } .$ . The operation cost of cooling system is primarily driven by electricity usage, which is included in (7).

The power demand of data center j is:

$$
p _ {j t} ^ {G} = p _ {j t} ^ {B} + p _ {j t} ^ {S C} - p _ {j t} ^ {S D} + p _ {j t} ^ {C}, \forall j \in \mathcal {J}.\tag{6}
$$

Therefore, the electricity cost is:

$$
f _ {t} ^ {G} = \sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {G} p _ {j t} ^ {G},\tag{7}
$$

where the electricity price $\gamma _ { j t } ^ { G }$ is uncertain. At the same time, the power demand causes carbon emissions in the power generation process. Due to the randomness of renewable energy generation, the carbon intensity $\gamma _ { j t } ^ { E }$ is also uncertain across time. To ensure low-carbon operation, we use the following constraint to upper bound the average carbon emission rate:

$$
\frac {1}{T} \sum_ {t \in \mathcal {T}} \sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} p _ {j t} ^ {G} \leq C ^ {E},\tag{8}
$$

where $T$ is the number of time slots.

## 3.2 Ofline Optimization Problem

Combining the models of all the components and minimizing the total operation cost, we have the following ofline optimization problem for the distributed data center coordination:

$$
\min \frac {1}{T} \sum_ {t \in \mathcal {T}} (f _ {t} ^ {W} + f _ {t} ^ {S} + f _ {t} ^ {G}),\tag{9a}
$$

$$
\text { s.t. } (1) - (7), \forall t \in \mathcal {T}, (8).\tag{9b}
$$

Problem (9) is an LP problem. However, there are uncertainties $\alpha _ { i t } ^ { F } , \ \beta _ { j t } ^ { C } , \ \gamma _ { j t } ^ { G }$ , and $\gamma _ { j t } ^ { E }$ that we cannot know in advance, making the direct solution of problem (9) impractical.

## 3.3 Stochastic Formulation

At time slot t, we know the uncertainty realizations at and before time slot t. This paper aims to propose a predictionfree strategy, which means that we decide the strategy at each time slot according to the current and previous uncertainty realizations as well as system status. In the following, we only consider such prediction-free strategies.

To consider the long-term expected efect, we change the time horizon to infinite, $\mathrm { i . e . , ~ } \mathcal { T } = \mathbb { N } _ { + } = \{ 1 , 2 , \dots , t \dots \}$ Then the accumulated values over time in the objective function and constraint (8) should be replaced by time average terms. In addition, the workload queues should be controlled to avoid linear increase. Therefore, the problem is changed into:

$$
\min \lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 1} ^ {T} \mathbb {E} [ f _ {t} ^ {W} + f _ {t} ^ {S} + f _ {t} ^ {G} ],\tag{10a}
$$

$$
\text { s.t. } (1) - (7), \forall t,\tag{10b}
$$

$$
\lim _ {T \to \infty} \frac {\mathbb {E} [ \tilde {q} _ {i T} ^ {F} ]}{T} = 0, \forall i \in \mathcal {I}, \lim _ {T \to \infty} \frac {\mathbb {E} [ \tilde {q} _ {j T} ^ {B} ]}{T} = 0, \forall j \in \mathcal {J},\tag{10c}
$$

$$
\lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 1} ^ {T} \sum_ {j \in \mathcal {J}} \mathbb {E} [ \gamma_ {j t} ^ {E} p _ {j t} ^ {G} ] \leq C ^ {E},\tag{10d}
$$

where $\mathbb { E } [ \cdot ]$ means taking expectation. Problem (10) is dificult to solve due to the uncertainties and the infinite time horizon. We need an online optimization algorithm to guarantee feasibility and ensure good optimality in the infinitetime case.

## 4 Online Optimization Algorithm

This section introduces the proposed online optimization al gorithm. A parametric online algorithm is developed to provide the operation strategy using the Lyapunov optimization technique. Then an LP-based method is proposed to optimize the parameters of the strategy. Meanwhile, feasibility guarantee and optimality gaps are analyzed and proven.

## 4.1 Parametric Online Algorithm

Following the theory of Lyapunov optimization [9], we first define virtual queues $\tilde { q } _ { i t } ^ { F } , \tilde { q } _ { j t } ^ { B } , \tilde { q } _ { j t } ^ { S } , \tilde { q } _ { j t } ^ { H }$ , and $\tilde { q } _ { t } ^ { E } $ : For any $t \in \mathbb { N } _ { + }$ 上，

$$
\tilde {q} _ {i t} ^ {F} = q _ {i t} ^ {F} + \theta_ {i} ^ {F}, \forall i \in \mathcal {I},\tag{11a}
$$

$$
\tilde {q} _ {j t} ^ {B} = q _ {j t} ^ {B} + \theta_ {j} ^ {B}, \tilde {q} _ {j t} ^ {S} = e _ {j t} ^ {S} + \theta_ {j} ^ {S}, \tilde {q} _ {j t} ^ {H} = \tau_ {j t} ^ {H} + \theta_ {j} ^ {H}, \forall j \in \mathcal {J},\tag{11b}
$$

$$
\tilde {q} _ {t + 1} ^ {E} = \max \left\{\tilde {q} _ {t} ^ {E} + \sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} p _ {j t} ^ {G} - C ^ {E}, 0 \right\},\tag{11c}
$$

where $\theta _ { i } ^ { F } , \theta _ { j } ^ { B } , \theta _ { j } ^ { S }$ , and $\theta _ { j } ^ { H }$ are parameters to be decided later; the initial state of $\tilde { q } ^ { E }$ is $\tilde { q } _ { 1 } ^ { E } = 0$ . In Lyapunov optimization, we require the virtual queues to be mean rate stable, i.e.,

$$
(1 0 \mathrm{c}), \lim _ {T \rightarrow \infty} \frac {\mathbb {E} [ \tilde {q} _ {j T} ^ {S} ]}{T} = 0, \lim _ {T \rightarrow \infty} \frac {\mathbb {E} [ \tilde {q} _ {j T} ^ {H} ]}{T} = 0, \forall j \in \mathcal {J},
$$

$$
\lim _ {T \to \infty} \mathbb {E} [ \tilde {q} _ {T} ^ {E} ] / T = 0.\tag{12a}
$$

(12b)

Lemma 1 is about the relationships between (12) and the constraints in (10), whose proof can be found in the appendix.

Lemma 1. Feasible solutions of problem (10) satisfy (12a). Constraint (12b) implies (10d).

To apply the Lyapunov optimization technique, we relax the bound constraints (1d), (3b), and (5b) of queues, revise (10d), and replace them by constraint (12). Then we have the following mean rate optimization problem:

$$
F ^ {m} = \min \lim _ {T \to \infty} \frac {1}{T} \sum_ {t = 1} ^ {T} \mathbb {E} [ f _ {t} ^ {W} + f _ {t} ^ {S} + f _ {t} ^ {G} ]\tag{13a}
$$

$$
\mathrm{s.t.} \tilde {q} _ {i (t + 1)} ^ {F} = \tilde {q} _ {i t} ^ {F} + a _ {i t} ^ {F} - \sum_ {j \in \mathcal {J}} m _ {i j t} ^ {R}, \forall i, \forall t,\tag{13b}
$$

$$
\tilde {q} _ {j (t + 1)} ^ {B} = \tilde {q} _ {j t} ^ {B} + \sum_ {i \in \mathcal {I}} m _ {i j t} ^ {R} - p _ {j t} ^ {B}, \forall j, \forall t,\tag{13c}
$$

$$
\tilde {q} _ {j (t + 1)} ^ {S} = \tilde {q} _ {j t} ^ {S} + p _ {j t} ^ {S C} \eta_ {j} ^ {S C} - p _ {j t} ^ {S D} / \eta_ {j} ^ {S D}, \forall j, \forall t,\tag{13d}
$$

$$
\tilde {q} _ {j (t + 1)} ^ {H} = \tilde {q} _ {j t} ^ {H} + \kappa_ {j} ^ {B} p _ {j t} ^ {B} - \kappa_ {j} ^ {C} p _ {j t} ^ {C} - \beta_ {j t} ^ {C}, \forall j, \forall t,\tag{13e}
$$

$$
\tilde {q} _ {t + 1} ^ {E} = \max \left\{\tilde {q} _ {t} ^ {E} + \sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} p _ {j t} ^ {G} - C ^ {E}, 0 \right\}, \forall t,\tag{13f}
$$

$$
(1 \mathrm{a}) - (1 \mathrm{c}), (2), (3 \mathrm{a}), (4), (5 \mathrm{a}), (6), (7), \forall t, (1 2).\tag{13g}
$$

Following the Lyapunov optimization theory, the Lyapunov function is as follows:

$$
L _ {t} = \frac {\sum_ {i \in \mathcal {I}} (\tilde {q} _ {i t} ^ {F}) ^ {2} + \sum_ {j \in \mathcal {J}} ((\tilde {q} _ {j t} ^ {B}) ^ {2} + (\tilde {q} _ {j t} ^ {S}) ^ {2} + (\tilde {q} _ {j t} ^ {H}) ^ {2}) + (\tilde {q} _ {t} ^ {E}) ^ {2}}{2},\tag{∀t.}
$$

The Lyapunov drift is defined as $\Delta _ { t } = L _ { t + 1 } - L _ { t }$ . To derive an upper bound for $\Delta _ { t } ,$ we need assumptions about the range of uncertainties:

Assumption 1. $0 \leq \alpha _ { i t } ^ { F } \leq A _ { i } ^ { F } , \forall i \in \mathcal { T }$ , ∀t. The data center $j ^ { \prime } s$ ambient temperature is no lower than $\underline { { T } } _ { j } ^ { H } , \forall j \in \mathcal { I }$ $\underline { { \beta } } _ { j } ^ { C } \le \beta _ { j t } ^ { C } \le \overline { { \beta } } _ { j } ^ { C } , \gamma _ { j } ^ { { G } } \le \gamma _ { j t } ^ { { G } } \le \overline { { \gamma } } _ { j } ^ { { G } } , 0 \le \gamma _ { j } ^ { E } \le \gamma _ { j t } ^ { E } \le \overline { { \gamma } } _ { j } ^ { E } , \forall j ,$ ∀t.

Lemma 2 gives an upper bound for $\Delta _ { t } ,$ which is a linear function of the decision variables in time slot t (note that the queues $\tilde { q } _ { i t } ^ { F } , \tilde { q } _ { j t } ^ { B } , \tilde { q } _ { j t } ^ { S } , \tilde { q } _ { j t } ^ { H }$ , and $\tilde { q } _ { t } ^ { E }$ have been decided before time slot t). The proof of Lemma 2 is in the appendix.

Lemma 2. Suppose that Assumption 1 holds. Then $\Delta _ { t } \leq$ I + B, where B is a constant independent of t and

$$
\begin{array}{l} I = \sum_ {i \in \mathcal {I}} \tilde {q} _ {i t} ^ {F} \left(a _ {i t} ^ {F} - \sum_ {j \in \mathcal {J}} m _ {i j t} ^ {R}\right) + \sum_ {j \in \mathcal {J}} \tilde {q} _ {j t} ^ {B} \left(\sum_ {i \in \mathcal {I}} m _ {i j t} ^ {R} - p _ {j t} ^ {B}\right) \\ + \sum_ {j \in \mathcal {J}} \tilde {q} _ {j t} ^ {S} (p _ {j t} ^ {S C} \eta_ {j} ^ {S C} - p _ {j t} ^ {S D} / \eta_ {j} ^ {S D}) + \sum_ {j \in \mathcal {J}} \tilde {q} _ {j t} ^ {H} (\kappa_ {j} ^ {B} p _ {j t} ^ {B} - \kappa_ {j} ^ {C} p _ {j t} ^ {C} - \beta_ {j t} ^ {C}) \\ + \tilde {q} _ {t} ^ {E} \left(\sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} (p _ {j t} ^ {B} + p _ {j t} ^ {S C} - p _ {j t} ^ {S D} + p _ {j t} ^ {C}) - C ^ {E}\right). \end{array} \tag {14}
$$

The online optimization problem in time slot t minimizes the weighted sum of the Lyapunov drift’s upper bound and the operation cost in the current time slot, which is as follows:

$$
\begin{array}{l} \min I + V (f _ {t} ^ {W} + f _ {t} ^ {S} + f _ {t} ^ {G}) \\ \text {s.t. (1a) - (1c),(2),(3a),(4),(5a),(6),(7),} \end{array}\tag{15a}
$$

(15b)

where $V ~ \geq ~ 0$ is a parameter to be determined. Given parameters $V , \ \theta _ { i } ^ { F } , { \bf \bar { \theta } } \theta _ { j } ^ { B } , \ \theta _ { j } ^ { S }$ , and $\theta _ { j } ^ { H }$ , the steps of the proposed online algorithm are presented in Algorithm 1.

$$
\alpha_ {i t} ^ {F}, \beta_ {j t} ^ {C}, \gamma_ {j t} ^ {G},
$$

$$
\gamma_ {j t} ^ {E}
$$

$$
a _ {i t} ^ {F}, m _ {i j t} ^ {R}, p _ {j t} ^ {B}, p _ {j t} ^ {S C}, p _ {j t} ^ {S D},
$$

$$
p _ {j t} ^ {C}.
$$

$$
(1 3 \mathrm{b}) - (1 3 \mathrm{f})
$$

$$
\tilde {q} _ {i (t + 1)} ^ {F}, \tilde {q} _ {j (t + 1)} ^ {B}, \tilde {q} _ {j (t + 1)} ^ {S}, \tilde {q} _ {j (t + 1)} ^ {H},
$$

$$
\tilde {q} _ {t + 1} ^ {E}.
$$

$$
F ^ {l} (V, \theta)
$$

$$
\frac {1}{T} \sum_ {t = 1} ^ {T} \mathbb {E} [ f _ {t} ^ {\hat {W}} + f _ {t} ^ {S} + f _ {t} ^ {G} ]
$$

$$
\overline {{\theta}} = (\theta_ {i} ^ {F}, \theta_ {j} ^ {B}, \theta_ {j} ^ {S}, \theta_ {j} ^ {H})
$$

## 4.2 Performance Guarantee and Parameter Optimization

To analyze the performance guarantee, we assume that the uncertainties are independent and identically distributed:

Assumption 2. $\xi _ { t } = ( \alpha _ { i t } ^ { F } , i \in \mathcal { T } , \beta _ { j t } ^ { C } , \gamma _ { j t } ^ { G } , \gamma _ { j t } ^ { E } , j \in \mathcal { I } ) , \forall t \in \mathcal { T }$ are independent and identically distributed.

Theorem 1 gives a performance guarantee for the parametric online algorithm, which follows from Theorem 4.8 in [9].

Theorem 1. Suppose that Assumption 1 and Assumption 2 hold. With parameter $V > 0$ and arbitrary θ, the optimality gap of the online algorithm is upper bounded by:

$$
F ^ {l} (V, \theta) - F ^ {m} \leq B / V.
$$

According to Theorem 1, the larger V , the better the performance. However, since relaxations are adopted while deriving problem (13), we should properly choose the parameters to ensure the feasibility of the strategy in problem (10). To analyze the feasibility, we consider the following assumption:

Assumption 3. $\tilde { q } _ { t } ^ { E } \le Q ^ { E } , \forall t . \kappa _ { j } ^ { C } P _ { j } ^ { C } \ge \kappa _ { j } ^ { B } P _ { j } ^ { B } - \underline { { \beta } } _ { i } ^ { C } , \forall j \in$ J always holds, i.e., the cooling systems’ capacities are large enough to prevent the temperature increase in all cases.

Theorem 2 provides the constraints that can guarantee the feasibility, whose proof is in the appendix.

Theorem 2. Suppose that Assumption 1 and Assumption 3 hold and the parameters $V \geq 0$ and θ satisfy (16). Then the strategy by Algorithm 1 is feasible in problem (10):

$$
- \theta_ {i} ^ {F} - \sum_ {j ^ {\prime} \in \mathcal {J}} M _ {i j ^ {\prime}} ^ {R} + \theta_ {j} ^ {B} + V \gamma_ {i j} ^ {R} \geq 0, \forall i \in \mathcal {I}, \forall j \in \mathcal {J},\tag{16a}
$$

$$
- \theta_ {j} ^ {B} - P _ {j} ^ {B} + (\underline {{T}} _ {j} ^ {H} + \theta_ {j} ^ {H}) \kappa_ {j} ^ {B} + V \underline {{\gamma}} _ {j} ^ {G} \geq 0, \forall j \in \mathcal {J},\tag{16b}
$$

$$
- \frac {\theta_ {j} ^ {S} + \underline {{E}} _ {j} ^ {S} + P _ {j} ^ {S D} / \eta_ {j} ^ {S D}}{\eta_ {j} ^ {S D}} - Q ^ {E} \overline {{\gamma}} _ {j} ^ {E} + V \gamma_ {j} ^ {S} - V \overline {{\gamma}} _ {j} ^ {G} \geq 0, \forall j \in \mathcal {J},\tag{16c}
$$

$$
(\theta_ {j} ^ {S} + \overline {{E}} _ {j} ^ {S} - P _ {j} ^ {S C} \eta_ {j} ^ {S C}) \eta_ {j} ^ {S C} + V \gamma_ {j} ^ {S} + V \underline {{\gamma}} _ {j} ^ {G} \geq 0, \forall j \in \mathcal {J},\tag{16d}
$$

$$
- (\theta_ {j} ^ {H} + \underline {{T}} _ {j} ^ {S} + \kappa_ {j} ^ {C} P _ {j} ^ {C}) \kappa_ {j} ^ {C} + V \underline {{\gamma}} _ {j} ^ {G} \geq 0, \forall j \in \mathcal {J},
$$

$$
- (\theta_ {j} ^ {H} + \overline {{T}} _ {j} ^ {S} - \kappa_ {j} ^ {B} P _ {j} ^ {B} + \underline {{\beta}} _ {j} ^ {C}) \kappa_ {j} ^ {C}\tag{16e}
$$

$$
+ Q ^ {E} \overline {{\gamma}} _ {j} ^ {E} + V \overline {{\gamma}} _ {j} ^ {G} \leq 0, \forall j \in \mathcal {J}.\tag{16f}
$$

According to Theorem 1 and Theorem 2, the following LP problem is proposed to optimize V and θ:

$$
\max _ {V, \theta} V \text {   s.t.   (16) }.\tag{17}
$$

Although the bounds of uncertainties can be easily estimated by the historical data, the value $Q ^ { E }$ is dificult to know in advance. Therefore, Algorithm 2 is developed to determine $Q ^ { E }$ and $( V , \theta )$ in an iterative manner.

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 2: Parameter optimization algorithm.

1 Let  $Q^{E} = 0$ .

2 Repeat:

3 Solve the LP problem (17) and obtain  $(V, \theta)$ .

4 Simulate the online algorithm using the historical dataset and let  $Q^{E} = \max_{t} \tilde{q}_{t}^{E}$ .

5 Until  $Q^{E}$  converges.
</div>

## 5 Case Study

In this section, we test the proposed method using a numerical simulation. The settings and results of the proposed method are shown in Section 5.1, method comparison is reported in Section 5.2, and parameter sensitivity analysis is conducted in Section 5.3. All the algorithms are implemented on a laptop with Intel(R) Core(TM) i5-1335U and 16 GB RAM. The LP problems are solved by Gurobi 11.0.3. The code and data can be found in [15].

## 5.1 Settings and Results

In the test case, there are 2 mapping nodes and 3 data centers. The upper bound $C ^ { E }$ of emission rate is set as 1.2 $\mathrm { { t C O } _ { 2 } / h }$ The historical data of uncertainties are generated from probability distributions, which include 10,000 time slots in total. We first use the 1,000 time slots of data to determine the parameters (V, θ). The iteration process of the proposed method is drawn in Figure 2, which shows that Algorithm 2 successfully converges after about 5 iterations.

![](images/c070db135946f5860ce493088f5dbd41cad35648d295770ccce2d6525599ce8f.jpg)  
Figure 2: The value of $Q ^ { E }$ in the iteration process.

![](images/6bfb4b5c1e78abdd6efd24574fb49a24439ad06f3d96c6d9602c336c68d42aab.jpg)  
Figure 3: The simulated queues $q ^ { F } , q ^ { B } , e ^ { S } ;$ , and $\tau ^ { H }$ in time slots 5,500–5,600. The bounds are represented by red dashed lines. All the queues are within the bounds.

The remaining 9,000 time slots are used for performance and feasibility tests. The test average cost rate is 335.3 \$/h. The emission rate is 1.160 $\mathrm { { t C O } _ { 2 } / h }$ , which is slightly lower than the specified upper bound $( C ^ { E } = 1 . 2 \mathrm { t C O _ { 2 } / h } )$ . Thus, the proposed method is efective in limiting the emissions. In addition, all the simulated queues are within their bounds, which verifies that the proposed method ensures feasibility. Part of the simulated queues are depicted in Figure 3.

## 5.2 Method Comparison

To further verify the efectiveness of the proposed method, we compare it with other methods:

C1 (ofline & low-carbon): Assume the knowledge of future uncertainty realizations and solve the ofline problem (9).

C2 (greedy & low-carbon): Minimize the total operation cost in the current time slot.

C3 (ofline): The ofline method with no emission bound. C4 (greedy): The greedy method with no emission bound.

C5 (no emission bound): Use the proposed method but do not consider the emission bound.

In the ofline and greedy methods C1–C4, upper bounds for the front- and back-end queues $( Q ^ { F } = 9 0 $ MWh and $Q ^ { B }$ = 70 MWh) are added to avoid endless increase of queues. Although ofline methods are impractical, they serve as references and provide lower bounds for the cost of online al gorithms. All the methods above provide feasible strategies, but their performances in cost and emissions difer, as com pared in Table 1. The proposed method achieves 133.5% cost and 96.67% emissions compared to the ofline method C1. While the ofline method is impractical, the proposed method is an online algorithm that achieves good performances, much better than the greedy algorithm C2. When the emission bound is not considered, the emission rates in methods C3–C5 become much higher than $C ^ { E } = 1 . 2$ $\mathrm { { t C O } _ { 2 } / h }$ , which emphasizes the need to consider the emission bound in the online optimization for the low-carbon operation of distributed data centers.

Table 1: Method Comparison

<table><tr><td>Method</td><td>Cost rate ($/h)</td><td>Emission rate (tCO2/h)</td></tr><tr><td>Proposed</td><td>335.3</td><td>1.160</td></tr><tr><td>C1</td><td>251.2</td><td>1.200</td></tr><tr><td>C2</td><td>1378</td><td>1.200</td></tr><tr><td>C3</td><td>231.7</td><td>2.784</td></tr><tr><td>C4</td><td>457.2</td><td>2.384</td></tr><tr><td>C5</td><td>251.6</td><td>2.803</td></tr></table>

## 5.3 Parameter Sensitivity Analysis

We analyze the impacts of the upper bounds $Q ^ { E }$ and $C ^ { E }$ $Q ^ { E }$ is the upper bound of the virtual queue $\overset { \cdot } { q } { E }$ , used in the parameter optimization (17). The test results under diferent $Q ^ { E }$ values are depicted in Figure 4. When $Q ^ { E }$ becomes larger, the cost rate increases and the emission rate decreases, where the emission rate is always lower than $C ^ { E }$ $= 1 . 2 \mathrm { \ t C O _ { 2 } / h }$ . Thus, a smaller $Q ^ { E }$ is preferred to decrease the cost. This is why we propose Algorithm 2 to find the best $Q ^ { E }$

![](images/df63ffc34004df7fcc2b9cb3af159f486f33ab6d6076f538848b907229a4329b.jpg)

![](images/8b41f9ac286b40d552dcdc51814b36beafc8bd71dba80175cae63e4da060fbbe.jpg)  
Figure 4: Test results under diferent $Q ^ { E }$

The proposed method is also tested under diferent $C ^ { E }$ values, as drawn in Figure 5. It shows that the proposed method has lower emission rates than the specified upper bound in all cases, while a lower $C ^ { E }$ value leads to a higher cost rate. The results verify the efectiveness of the proposed method in the low-carbon online operation of data centers.

![](images/64a6948ba555be76cb1913819a82f023a1e503a7663d883526e63ffa4766e985.jpg)

![](images/1a650c121c093fc4be8093c5aaca56047fb28fb6fcf117d854a7f52b00d90a2c.jpg)  
Figure 5: Test results under diferent $C ^ { E }$

## 6 Conclusion

This paper proposes a novel online low-carbon management method for distributed data centers. While minimizing the total operation cost, the proposed method jointly considers workload, energy, and temperature operational constraints and efectively deals with the uncertainties of workload demands, ambient temperature, electricity prices, and carbon intensities. The proposed method is prediction-free thanks to the adoption of the Lyapunov optimization technique. There are two contributions in this paper: 1) The emission rate upper bound and temperature management are considered in the proposed Lyapunov optimization-based online algorithm. 2) An LP-based method is proposed to optimize the parameters of the operation strategy and to enhance performance while ensuring feasibility. The case study and the method comparison demonstrate the proposed method’s efectiveness in low-carbon operation. Incorporating more detailed data center operation models will be our future research direction.

## 7 Acknowledgments

This work was supported by the National Natural Science Foundation of China (Grant No. 72225001), and the Shun Hing Institute of Advanced Engineering, The Chinese University of Hong Kong, through Project RNE-p2-23 (Grant No. 8115071).

## 8 References

[1] Mytton D., Ashtine M.: ‘Sources of data center energy estimates: A comprehensive review’, Joule, 2022, 6, pp. 2032–2056

[2] Liu J., Xu Z., Wu J., et al.: ‘Optimal planning of internet data centers decarbonized by hydrogen-water-based energy systems’, IEEE Transactions on Automation Science and Engineering, 2023, 20, (3), pp. 1577–1590

[3] Lou J., Tang Z., Jia W.: ‘Energy-eficient joint task assignment and migration in data centers: A deep reinforcement learning approach’, IEEE Transactions on Network and Service Management, 2023, 20, (2), pp. 961–973

[4] da Silva M. D. M., Gamati´e A., Sassatelli G., et al.: ‘Optimization of data and energy migrations in mini data centers for carbon-neutral computing’, IEEE Transactions on Sustainable Computing, 2023, 8, (1), pp. 68–81

[5] Zhao Z., Fan L., Han Z.: ‘Optimal data center energy management with hybrid quantum-classical multi-cuts Benders’ decomposition method’, IEEE Transactions on Sustainable Energy, 2024, 15, (2), pp. 847–858

[6] Ye G., Gao F., Fang J.: ‘Joint workload scheduling in geo-distributed data centers considering UPS power losses’, IEEE Transactions on Industry Applications, 2023, 59, (1), pp. 612–626

[7] Khalid S., Ahmad I.: ‘Dual optimization of revenue and expense in geo-distributed data centers using smart grid’, IEEE Transactions on Cloud Computing, 2023, 11, (2), pp. 1622–1635

[8] Sun J., Chen M., Liu H., et al.: ‘Workload transfer strategy of urban neighboring data centers with market power in local electricity market’, IEEE Transactions on Smart Grid, 2020, 11, (4), pp. 3083–3094

[9] Neely M. J.: ‘Stochastic network optimization with application to communication and queueing systems’ (Morgan & Claypool, 2010)

[10] Liu N., Yu X., Fan W., et al.: ‘Online energy sharing for nanogrid clusters: A Lyapunov optimization approach’, IEEE Transactions on Smart Grid, 2018, 9, (5), pp. 4624–4636

[11] Li Y., Xia S., Zheng M., et al.: ‘Lyapunov optimizationbased trade-of policy for mobile cloud ofloading in heterogeneous wireless networks’, IEEE Transactions on Cloud Computing, 2022, 10, (1), pp. 491–505

[12] Xie R., Chen Y.: ‘Real-time bidding strategy of energy storage in an energy market with carbon emission allocation based on Aumann-Shapley prices’, IEEE Transactions on Energy Markets, Policy and Regulation, 2024, 2, (3), pp. 350–367

[13] Sun J., Chen S., You P., et al.: ‘Battery-assisted online operation of distributed data centers with uncertain workload and electricity prices’, IEEE Transactions on Cloud Computing, 2023, 11, (2), pp. 1303–1314

[14] Huang S., Yan D., Chen Y.: ‘An online algorithm for combined computing workload and energy coordination within a regional data center cluster’, International Journal of Electrical Power and Energy Systems, 2024, 158, pp. 109971

[15] Xie R.: ‘xieruijx/low-carbon-data-center’, https://github.com/xieruijx/low-carbon-data-center, accessed 25 October 2024

## Appendix

## Proof of Lemma 1

We first consider a feasible solution to problem (10). By constraints (3b) and (5b), $\tilde { q } _ { j t } ^ { S }$ , ∀t and $\tilde { q } _ { j t } ^ { H }$ , ∀t are bounded, so lim $1 _ { T  \infty } \mathbb { E } [ \tilde { q } _ { j T } ^ { S } ] / T = 0$ and lim $_ { \perp  \infty } \mathbb { E } [ \tilde { q } _ { j T } ^ { H } ] / T = 0$ . Therefore, constraint (12a) is satisfied.

Next, assume that constraint (12b) holds. Because $\begin{array} { r } { \tilde { q } _ { t + 1 } ^ { E } \ \ge \ \tilde { q } _ { t } ^ { E } + \sum _ { j \in \mathcal { I } } \gamma _ { j t } ^ { E } p _ { j t } ^ { G } - C ^ { E } } \end{array}$ and $\tilde { q } _ { 1 } ^ { E } ~ = ~ 0$ , we have $\begin{array} { r } { \tilde { q } _ { T } ^ { E } \geq \sum _ { t = 1 } ^ { T - 1 } ( \sum _ { j \in \mathcal { T } } \gamma _ { j t } ^ { E } p _ { j t } ^ { G } { - } C ^ { E } ) } \end{array}$ ). Therefore, constraint (12b) implies (10d).

## Proof of Lemma 2

Using constraint (13b), we have

$$
\begin{array}{l} (\tilde {q} _ {i (t + 1)} ^ {F}) ^ {2} - (\tilde {q} _ {i t} ^ {F}) ^ {2} \\ = \left(a _ {i t} ^ {F} - \sum_ {j \in \mathcal {J}} m _ {i j t} ^ {R}\right) ^ {2} + 2 \tilde {q} _ {i t} ^ {F} \left(a _ {i t} ^ {F} - \sum_ {j \in \mathcal {J}} m _ {i j t} ^ {R}\right) \\ \leq \left(A _ {i} ^ {F} + \sum_ {j \in \mathcal {J}} M _ {i j} ^ {R}\right) ^ {2} + 2 \tilde {q} _ {i t} ^ {F} \left(a _ {i t} ^ {F} - \sum_ {j \in \mathcal {J}} m _ {i j t} ^ {R}\right). \end{array}
$$

By constraint (13f),

$$
(\tilde {q} _ {t + 1} ^ {E}) ^ {2} - (\tilde {q} _ {t} ^ {E}) ^ {2}
$$

$$
\begin{array}{l l} \leq & \left(\tilde {q} _ {t} ^ {E} + \sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} p _ {j t} ^ {G} - C ^ {E}\right) ^ {2} - (\tilde {q} _ {t} ^ {E}) ^ {2} \\ = & \left(\sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} p _ {j t} ^ {G} - C ^ {E}\right) ^ {2} + 2 \tilde {q} _ {t} ^ {E} \left(\sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} p _ {j t} ^ {G} - C ^ {E}\right) \\ \leq & \left(\sum_ {j \in \mathcal {J}} \overline {{\gamma}} _ {j} ^ {E} \left(P _ {j} ^ {B} + P _ {j} ^ {S C} + P _ {j} ^ {S D} + P _ {j} ^ {C}\right) + C ^ {E}\right) ^ {2} \\ & + 2 \tilde {q} _ {t} ^ {E} \left(\sum_ {j \in \mathcal {J}} \gamma_ {j t} ^ {E} (p _ {j t} ^ {B} + p _ {j t} ^ {S C} - p _ {j t} ^ {S D} + p _ {j t} ^ {C}) - C ^ {E}\right). \end{array}
$$

Other terms can be handled similarly. Then we have $\Delta _ { t } \leq$ I + B, where

$$
\begin{array}{l} B = \sum_ {i \in \mathcal {I}} \left(A _ {i} ^ {F} + \sum_ {j \in \mathcal {J}} M _ {i j} ^ {R}\right) ^ {2} + \sum_ {j \in \mathcal {J}} \left(\sum_ {i \in \mathcal {I}} M _ {i j} ^ {R} + P _ {j} ^ {B}\right) ^ {2} \\ \quad + \sum_ {j \in \mathcal {J}} \left(P _ {j} ^ {S C} \eta_ {j} ^ {S C} + P _ {j} ^ {S D} / \eta_ {j} ^ {S D}\right) ^ {2} \\ \quad + \sum_ {j \in \mathcal {J}} \left(\kappa_ {j} ^ {B} P _ {j} ^ {B} + \kappa_ {j} ^ {C} P _ {j} ^ {C} + \max \left\{\left| \underline {{\beta}} _ {j} ^ {C} \right|, \left| \overline {{\beta}} _ {j} ^ {C} \right| \right\}\right) ^ {2} \\ \quad + \left(\sum_ {j \in \mathcal {J}} \overline {{\gamma}} _ {j} ^ {E} \left(P _ {j} ^ {B} + P _ {j} ^ {S C} + P _ {j} ^ {S D} + P _ {j} ^ {C}\right) + C ^ {E}\right) ^ {2}. \end{array}
$$

## Proof of Theorem 2

To make the strategy by Algorithm 1 feasible in problem (10), we only need to ensure the following constraints:

$$
\begin{array}{r l} & {\tilde {q} _ {i t} ^ {F} - \theta_ {i} ^ {F} \geq 0, \forall i \in \mathcal {I}, \tilde {q} _ {j t} ^ {B} - \theta_ {j} ^ {B} \geq 0, \forall j \in \mathcal {J},} \\ & {\tilde {q} _ {j t} ^ {S} - \theta_ {j} ^ {S} \in \left[ \underline {{E}} _ {j} ^ {S}, \overline {{E}} _ {j} ^ {S} \right], \tilde {q} _ {j t} ^ {H} \in \left[ \underline {{T}} _ {j} ^ {H}, \overline {{T}} _ {j} ^ {H} \right], \forall j \in \mathcal {J}.} \end{array}
$$

According to the dynamic equations of these queues in (13b)–(13f), the following constraints constitute a suficient condition (note that the temperature cannot drop below the lower bound without the cooling system, according to Assumption 1; the cooling system is able to prevent temperature increase according to Assumption 3):

$$
\tilde {q} _ {i t} ^ {F} - \theta_ {i} ^ {F} \in \left[ 0, \sum_ {j \in \mathcal {J}} M _ {i j} ^ {R}\right) \implies m _ {i j t} ^ {R *} = 0, \forall j,
$$

$$
\begin{array}{r l} & {\tilde {q} _ {j t} ^ {B} - \theta_ {j} ^ {B} \in \left[ 0, P _ {j} ^ {B}\right) \implies p _ {j t} ^ {B *} = 0,} \\ & {\tilde {q} _ {j t} ^ {S} - \theta_ {j} ^ {S} \in \left[ \underline {{E}} _ {j} ^ {S}, \underline {{E}} _ {j} ^ {S} + P _ {j} ^ {S D} / \eta_ {j} ^ {S D}\right) \implies p _ {j t} ^ {S D *} = 0,} \\ & {\tilde {q} _ {j t} ^ {S} - \theta_ {j} ^ {S} \in \left(\overline {{E}} _ {j} ^ {S} - P _ {j} ^ {S C} \eta_ {j} ^ {S C}, \overline {{E}} _ {j} ^ {S} \right] \implies p _ {j t} ^ {S C *} = 0,} \\ & {\tilde {q} _ {j t} ^ {H} - \theta_ {j} ^ {H} \in \left[ \underline {{T}} _ {j} ^ {S}, \underline {{T}} _ {j} ^ {S} + \kappa_ {j} ^ {C} P _ {j} ^ {C}\right) \implies p _ {j t} ^ {C *} = 0,} \\ & {\tilde {q} _ {j t} ^ {H} - \theta_ {j} ^ {H} \in \left(\overline {{T}} _ {j} ^ {S} - \kappa_ {j} ^ {B} P _ {j} ^ {B} + \underline {{\beta}} _ {j} ^ {C}, \overline {{T}} _ {j} ^ {S} \right] \implies p _ {j t} ^ {C *} = P _ {j} ^ {C}.} \end{array}
$$

The objective function of problem (15) can be written as:

$$
\begin{array}{r l} & {\sum_ {i \in \mathcal {I}} (\tilde {q} _ {i t} ^ {F} - V \gamma_ {i} ^ {F}) a _ {i t} ^ {F} + \sum_ {i \in \mathcal {I}} \sum_ {j \in \mathcal {J}} (- \tilde {q} _ {i t} ^ {F} + \tilde {q} _ {j t} ^ {B} + V \gamma_ {i j} ^ {R}) m _ {i j t} ^ {R}} \\ & {+ \sum_ {j \in \mathcal {J}} (- \tilde {q} _ {j t} ^ {B} + \tilde {q} _ {j t} ^ {H} \kappa_ {j} ^ {B} + \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j t} ^ {G}) p _ {j t} ^ {B}} \\ & {+ \sum_ {j \in \mathcal {J}} (\tilde {q} _ {j t} ^ {S} \eta_ {j} ^ {S C} + \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j} ^ {S} + V \gamma_ {j t} ^ {G}) p _ {j t} ^ {S C}} \\ & {+ \sum_ {j \in \mathcal {J}} (- \tilde {q} _ {j t} ^ {S} / \eta_ {j} ^ {S D} - \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j} ^ {S} - V \gamma_ {j t} ^ {G}) p _ {j t} ^ {S D}} \\ & {+ \sum_ {j \in \mathcal {J}} (- \tilde {q} _ {j t} ^ {H} \kappa_ {j} ^ {C} + \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j t} ^ {G}) p _ {j t} ^ {C}} \\ & {- \tilde {q} _ {t} ^ {E} C ^ {E} + V \sum_ {i \in \mathcal {I}} \gamma_ {i} ^ {F} \alpha_ {i t} ^ {F}.} \end{array}
$$

Therefore, we only need the following conditions to ensure feasibility:

$$
\begin{array}{r l} & {\tilde {q} _ {i t} ^ {F} - \theta_ {i} ^ {F} <   \sum_ {j} M _ {i j} ^ {R} \implies - \tilde {q} _ {i t} ^ {F} + \tilde {q} _ {j t} ^ {B} + V \gamma_ {i j} ^ {R} > 0, \forall j,} \\ & {\tilde {q} _ {j t} ^ {B} - \theta_ {j} ^ {B} <   P _ {j} ^ {B} \implies - \tilde {q} _ {j t} ^ {B} + \tilde {q} _ {j t} ^ {H} \kappa_ {j} ^ {B} + \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j t} ^ {G} > 0,} \\ & {\tilde {q} _ {j t} ^ {S} - \theta_ {j} ^ {S} <   \underline {{E}} _ {j} ^ {S} + P _ {j} ^ {S D} / \eta_ {j} ^ {S D} \implies} \\ & {- \tilde {q} _ {j t} ^ {S} / \eta_ {j} ^ {S D} - \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j} ^ {S} - V \gamma_ {j t} ^ {G} > 0,} \\ & {\tilde {q} _ {j t} ^ {S} - \theta_ {j} ^ {S} > \overline {{E}} _ {j} ^ {S} - P _ {j} ^ {S C} \eta_ {j} ^ {S C} \implies} \\ & {\tilde {q} _ {j t} ^ {S} \eta_ {j} ^ {S C} + \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j} ^ {S} + V \gamma_ {j t} ^ {G} > 0,} \\ & {\tilde {q} _ {j t} ^ {H} - \theta_ {j} ^ {H} <   \underline {{T}} _ {j} ^ {S} + \kappa_ {j} ^ {C} P _ {j} ^ {C} \implies} \\ & {- \tilde {q} _ {j t} ^ {H} \kappa_ {j} ^ {C} + \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j t} ^ {G} > 0,} \\ & {\tilde {q} _ {j t} ^ {H} - \theta_ {j} ^ {H} <   \overline {{T}} _ {j} ^ {S} - \kappa_ {j} ^ {B} P _ {j} ^ {B} + \underline {{\beta}} _ {j} ^ {C} \implies} \\ & {- \tilde {q} _ {j t} ^ {H} \kappa_ {j} ^ {C} + \tilde {q} _ {t} ^ {E} \gamma_ {j t} ^ {E} + V \gamma_ {j t} ^ {G} > 0,} \end{array}
$$

which can be guaranteed by (16) according to the bounds of uncertainties and queues.