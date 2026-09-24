# Joint Energy and Computation Workload Management for Geo-Distributed Data Centers in Smart Grid

Zizhen Li $^{*}$ , Ran Wang $^{*}$ , Kun Zhu $^{*}$ , Changyan Yi $^{*}$ , Linfeng Liu $^{\dagger}$ and Dusit Niyato $^{\ddagger}$

\* Nanjing University of Aeronautics and Astronautics, Nanjing, China

$^{\dagger}$ Nanjing University of Posts and Telecommunications, Nanjing, China

$^{\ddagger}$ Nanyang Technological University, Singapore

Emails: {lzz1997, wangran, zhukun, changyan.yi}@nuaa.edu.cn, liulf@njupt.edu.cn and dniyato@ntu.edu.sg

Abstract—With the increasing demands of data computation and storage for cloud-based services, the energy demand of data centers (DCs) is rising rapidly and becoming a noticeable challenge for current power networks. The smart grid (SG) is deemed as the future power system paradigm enabling more affordable and sustainable energy supply, which can effectively relieve the power load pressure of DCs. Moreover, with growing concerns regarding harmful emissions due to combustion of fossil fuels, the exploitation of renewable energy sources (RES) has attracted extensive attention, benefiting both SGs and DCs at large. However, the geo-distributed property of DCs and SGs, as well as the uncertain nature of RES production pose severe challenges to the optimal management of computation and energy resources in such coupling system. Focusing on these issues, a joint energy and computation workload management framework is proposed for enabling a sustainable DC paradigm with distributed RES. Specifically, to model the interactions between data center operator (DCO) and SGs, a Stackelberg game is formulated. The SG offers the DCO an electricity selling price to maximize the benefit. The DCO allocates the computation workload to geo-distributed DCs, aiming to minimize the costs of energy consumption and carbon emissions. We obtain the equilibrium state of the game and prove its uniqueness and optimality. Simulation experiments are conducted to evaluate the performance of the proposed scheme, showing that it can achieve considerable cost savings over counterparts. The proposed approach and obtained results provide useful insights for helping the DCO developing rational management strategies.

Index Terms—Data Centers (DCs), Smart Grid (SG), Workload Management, Energy Management, Game Theory.

## I. INTRODUCTION

With the popularity of cloud computing and Internet online services, it is now common for increasing quantities of data and computation loads to be moved to geographically distributed data centers (DCs). Each DC may include hundreds of thousands of servers, massive storage equipment, cooling facilities, and power transformers, which will undoubtedly lead to rapid growth of energy consumption. For example, DCs consume almost 200 terawatt-hours (TWh) of energy each year, which is sufficient to power some countries (e.g., Poland, Sweden, and Egypt) [1], [2]. Under such circumstances, increasing operation efficiency to reduce the energy consumption cost has become a practical challenge for DC operators (DCOs). In recent years, various technologies and standards have been proposed to make better use of the underlying resources of DC. For instance, Xie et al. in [3] tried to consolidate VMs onto fewer servers, with idle servers remaining in a sleeping state. In [4], Dynamic voltage and frequency scaling (DVFS) and node-varying on/off technologies were used to reduce the total power consumption of the server cluster during run time.

Moreover, DCOs must take into account the fact that geodistributed DCs may vary in terms of server utilization level, latency, and current electricity price, etc. The computation workload needs to be properly dispatched to geo-distributed DCs to fully utilize the computation resources. Moreover, as the next generation of the power grid, smart grid (SG), with the characteristics of geographical distribution, has a high degree of complementarity and consistency with geo-distributed DCs. It hence becomes necessary to study the interactions between the SG and the DC and implement a seamless integration between them. Some researchers start from the perspectives of demand response and dynamic pricing to realize sustainable and reliable interactions between cloud DCs and SGs $[5]$ . For instance, in $[6]$ , Rao et al. minimized the energy cost of geographical load-balancing by utilizing the spatial diversity of electricity prices. In $[7]$ , Wang et al. modelled the interaction between the SG and DC as a two-stage problem where the grid selects the appropriate price based on electric power load balance in the first stage and the DC responds to the price in the second stage to minimize the total energy cost.

For DCOs, however, simply reducing energy costs is not enough. According to the current study, DCs would result in the release of 150 million tons of harmful emissions per year, and the cost of reprocessing these emissions would approach \$13 billion annually [8]; DC emissions are forecasted to reach 36.7 billion tons by 2040 [9]. Obviously, DCs will significantly increase the energy consumption and carbon footprint of the local power grid. Under the framework of SG, the exploitation of renewable energy resources (RES) into the power system become easier, more secure and stable, which is very consistent with the current distributed deployment characteristics of DCs. On the one hand, the utilization of renewable energy such as wind and solar energy can reduce the total carbon emissions from the root. The home-grown renewable energy, on the other hand, is often much cheaper than the brown energy on the grid.

In this context, the integration of DCs with RES is deemed as an effective way to realize power grid stability, reduce the cost and emission of DCs' power consumption. There are open questions and challenges to actually implementing the SG system to power DCs with RES: 1) There is a geographical distribution mismatch between the generation of RES and the task flow in DCs. How can the joint optimization of energy and workload in be realized in each DC? 2) In the system, there are SGs and DCs. How can these components coordinate and interact with each other to achieve their goals? 3) How does the coupling system adjust the strategy of each entity through price and other factors according to the changes in the external environment in order to cope with the dynamic and random characteristics?

In this paper, we propose an SGs-DCO architecture to address these challenges and study the joint energy and computation workload allocation problem under this framework. Specifically, DCO aims to minimize energy costs and emissions overhead by distributing computation tasks across distributed DCs. Then, SGs have some energy to sell, but the SG can only serve the DC within its region. SGs thus must make decisions about the price of electricity for the DC, which is based on the cost of generation and the amount of RES produced. To describe the corresponding strategic interaction between different participants, a Stackelberg game model is established. The main contributions of this paper are summarized as follows:

\- We integrate RES into the SG and create a framework that allows the DCO to minimize the weighted sum of energy costs and carbon emissions cost taking into account electricity prices, computation workloads, and renewable energy generation.

\- We formulate a Stackelberg game between DCO and SGs to determine the best strategy. In this game, each SG will set a real-time price to maximize its profit as the leader; and given these prices, as the follower, the DCO will minimize its cost via computation workload shifting and dynamic server allocation. We also utilize the backward induction method to obtain the Stackelberg equilibria of this two-stage game.

\- Finally, simulations are used to evaluate the performance of the joint energy and computation workload management scheme, and the effects of the parameters on the system operation are then studied.

The remainder of this paper is structured as follows. In Section II, we describe the system model and problem description. In Section III, we analyze the game and present the corresponding algorithm to solve the game. The performance evaluations will be shown in Section IV. Finally the paper is concluded in Section V.

## II. SYSTEM MODEL AND PROBLEM DESCRIPTION

## A. System Framework Overview

We consider the system consisting of SGs, and a DCO in one time period (such as 1 h). As illustrated in Fig. 1, let $I = \{1, ..., I\}$ denote the set of sites with different SGs, which is dedicated to providing electricity in the form of both brown energy and renewable energy to DCs within its area.

Because of the intermittent nature of renewable energy, we consider renewable energy as a supplementary energy source; that is, the energy demand of the DC in this system is greater than the production of renewable energy. The SG framework allows two-way communication between users and the grid. Therefore, in our system, SGs and DCs regularly exchange power information through power line communication. Next, we describe the benefit function of each component in detail.

![](images/22b5dc305c237e5ca3d87406f4690fe525a447db472e59cd56f78eeb024e95f4.jpg)  
Fig. 1: A two-layer system model.

## B. The Utility of the DCO

As mentioned above, the DCO has $|I|$ distributed DCs, which is a common setting in reality, including those at Google, Amazon, etc. Next, we discuss the operation constraints of the DCO.

1) Computing Workload Constraint: Let the aggregation of computational task requests arriving at the DCO be approximated as a Poisson process with an average rate $\Lambda$ . The DCO will then assign a certain amount of task volume $\lambda_{i}$ to the DC i so that they can jointly complete the total workload of $\Lambda$ :

$$
\sum_ {i \in \mathcal {I}} \lambda_ {i} = \Lambda , \lambda_ {i} \geq 0, \forall i \in \mathcal {I}.\tag{1}
$$

2) QoS Constraint: Quality of Service (QoS) is a very important indicator for DCO. Let $T_{QoS}$ be the maximum delay that the DCO's user can tolerate, which can be determined by the service level agreement (SLA) [10]. Specifically, the delay in SLA usually consists of two parts: the transmission delay experienced while the request is outside of DC and the queueing delay experienced while the request is at DC.

For simplicity, we assume the network resource from DCO to DCs is always sufficient, then $d_{i}$ can be defined as the transmission delay of a request from DCO to DC i during time slot t. To model the queueing process of tasks for each DC, an M/GI/1 Processor Sharing (PS) queue is employed [11]. Considering the assumption that the workload is perfectly parallelizable, and arrivals are Poisson, then we can obtain that the queueing delay is approximately $\frac{1}{\mu_{i}-\lambda_{i}/m_{i}}$ , where $m_{i}$ is the number of active servers and each server has homogeneous service rates $\mu_{i}$ . So, we can find that the queueing delay is finite, continuous and differentiable. At the same time, in order to ensure the stability of the queuing system, we must have that $\lambda_{i}=0$ or $\lambda_{i}<m_{i}\mu_{i}$ . Otherwise, we define the queueing delay as infinity for $\lambda_{i}\geq m_{i}\mu_{i}$ . Therefore, the QoS constraint is shown below:

$$
\frac {1}{\mu_ {i} - \lambda_ {i} / m _ {i}} + d _ {i} \leq T _ {Q o S}.\tag{2}
$$

3) Server Constraint: According to [12], since DCs typically have thousands of servers, we can relax the integer variable $m_{i}$ to a continuous variable, and such approach will not have a significant impact on its optimal value. Therefore, for the number of active servers $m_{i}$ , we have the upper and lower bounds shown below:

$$
0 \leq m _ {i} \leq M _ {i} ^ {m a x}, \forall i \in \mathcal {I},\tag{3}
$$

where $M_{i}^{max}$ represents the maximum number of servers available for DC i.

4) Energy Consumption: Energy consumption for a DC can generally be divided into IT equipment energy consumption (e.g., CPU, memory, and storage) and auxiliary equipment energy consumption (e.g., cooling, lighting, and power facility). Therefore, the energy consumption of DC i is [13]:

$$
e _ {i} = m _ {i} \left(P _ {i d l e} + \left(P _ {p e a k} - P _ {i d l e}\right) U _ {i} + (\eta - 1) P _ {p e a k}\right),\tag{4}
$$

where $P_{idle}$ and $P_{peak}$ are the idle and peak power of the server, respectively; $U_{i} = \frac{\lambda_{i}}{m_{i}\mu_{i}}$ is the average server utilization, and $\eta$ is the power usage effectiveness (PUE), which represents the ratio of total energy consumption to IT energy consumption.

5) Carbon Emission Cost: In our system, the DCO is a socially responsible user that considers the cost of carbon emission. To estimate the relationship between the marginal carbon abatement cost and carbon intensity parametrically, we model the cost of carbon emissions as a strictly increasing and convex function [14]. For analysis tractability, we choose a quadratic function:

$$
\vartheta_ {i} = \omega (e _ {i} - r _ {i}) ^ {2},\tag{5}
$$

where $\omega$ is a weighted parameter that transfers the carbon emission into cost; $e_{i}$ is the energy consumption required by DC i; $r_{i}$ is renewable energy generated by SG i itself.

To guarantee QoS, DCO determines the task allocation and the number of active servers so as to achieve the minimum energy costs and carbon emissions. Hence, the objective function is as follows:

$$
\min _ {\lambda , m} V = \sum_ {i \in \mathcal {I}} p _ {i} e _ {i} + \vartheta_ {i}\tag{6}
$$

$$
\begin{array}{l l} \text {s.t.} & (1) - (5), \\ & 0 \leq \lambda_ {i} \leq m _ {i} \mu_ {i}, \end{array}\tag{7}
$$

where constraints (7) are given by the stability of the queuing system.

## C. The Utility of Smart Grid

In the system, $|I|$ geo-distributed SGs will adopt brown or renewable energy to power DCs in their regions. As mentioned earlier, renewable energy is a supplementary energy source, which means that the total renewable energy of the SGs is less than the energy consumption demand of the DCO. Therefore, it can be expected that there is pricing competition among SGs. In other words, the electricity price of SG is not only determined by the energy consumption demand of DCO, but also affected by other SGs' electricity prices. Thus, in pricing competition, each SG needs to set its electricity price wisely based on the amount of renewable energy that it has, the cost of generating electricity, and the other SGs' prices. The cost function of each generator can be approximated as a quadratic function [15]. Therefore, for each SG $i, i \in I$ , its generation power is represented as $P_{i}$ , with a lower (upper) bound denoted by $\mathcal{P}_{i}^{m}(\mathcal{P}_{i}^{M})$ . Then, the cost function of each generator can be denoted as:

$$
\mathcal {C} _ {i} \left(P _ {i}\right) = a _ {i} \mathcal {P} _ {i} ^ {2} + b _ {i} \mathcal {P} _ {i} + c _ {i},\tag{8}
$$

where $a_{i}$ , $b_{i}$ , and $c_{i}$ are fitting parameters of the cost function for DC $i$ 's generator.

Therefore, the optimization problem for each SG is formulated as:

$$
\max _ {p _ {i}} u _ {i} = p _ {i} e _ {i} (\lambda_ {i}) - \mathcal {C} _ {i}\tag{9}
$$

$$
\mathrm{s.t.} \quad \mathcal {C} _ {i} = a _ {i} (e _ {i} - r _ {i}) ^ {2} + b _ {i} (e _ {i} - r _ {i}) + c _ {i},\tag{10}
$$

$$
\mathcal {P} _ {i} ^ {m} \leq e _ {i} - r _ {i} \leq P _ {i} ^ {M},
$$

$$
0 \leq p _ {i} \leq \pi ,\tag{11}
$$

(12)

where $C_{i}$ is the cost of generating electricity from SG i; $\pi$ is the maximum unit price that DCO can accept (i.e., the benefit from processing tasks).

## III. GAME ANALYSES AND ALGORITHMS

In this section, we analyze the existence and uniqueness of the equilibrium solution of the Stackelberg game, as well as the algorithm to achieve such solution.

## A. Stackelberg Game Design

To combine the dynamics of SGs and DCs, we model their interactions as a Stackelberg game, in which SGs act as the leader, and the DCO acts as the follower. For convenience, let us represent the game between the SGs and DCO as S. The game can be formally defined as: $\mathcal{S} = \left\{(\mathcal{I} \cup \{\mathrm{DCO}\}), \{\mathrm{p}_{\mathrm{i}}\}_{\mathrm{i} \in \mathcal{I}}, \boldsymbol{\lambda}, \{\mathrm{u}_{\mathrm{i}}\}_{\mathrm{i} \in \mathcal{I}}, \mathrm{V}\right\}$

In this context, we present the formal definition of S from game theory:

Definition 1: The points $p^{*}$ and $\lambda^{*}$ are the equilibrium point $(p^{*}, \lambda^{*})$ of $S$ when the following conditions are satisfied:

$$
V \left(\boldsymbol {\lambda} ^ {*}, \boldsymbol {p} ^ {*}\right) \geq V \left(\boldsymbol {\lambda}, \boldsymbol {p} ^ {*}\right)\tag{13}
$$

$$
u _ {i} \left(p _ {i} ^ {*}, p _ {- i} ^ {*}, \lambda^ {*}\right) \geq u _ {i} \left(p _ {i}, p _ {- i} ^ {*}, \lambda^ {*}\right), \forall i \in \mathcal {I}.\tag{14}
$$

The goal of this game is to achieve the Stackelberg equilibrium point, where both the leaders (SGs) and the follower (DCO) are not motivated to deviate.

## B. Equilibrium Analysis

The most common method of analyzing Stackelberg games is the backward induction method $[16]$ . According to the principle of backward induction, the optimal response of the follower is first found from the DCO, and then, it is substituted into the utility function of each leader (SGs) and optimized accordingly. Thus, a non-cooperative pricing competition subgame is formulated at Stage I. At Stage II, since there is only one player (DCO), the best response of the DCO can be readily obtained by solving $(6)$ .

1) Optimal Solutions for the DCO at Stage II: We find that cost minimization of the DCO is an independent convex optimization problem by determining the task assignment $\lambda_{i}$ and the number of active server $m_{i}$ for each DC i. First, $m_{i}$ is constrained by (2) and we can get that :

$$
m _ {i} \geq \frac {\lambda_ {i}}{\mu_ {i} - \frac {1}{T _ {Q o S} - d _ {i}}},\tag{15}
$$

the energy consumption of the DC i (i.e., $e_{i}$ ) is a strictly increasing function of $m_{i}$ . Hence, when the DCO chooses the number of active servers as follows:

$$
m _ {i} (\lambda_ {i}) = \frac {\lambda_ {i}}{\mu_ {i} - \frac {1}{T _ {Q o S} - d _ {i}}},\tag{16}
$$

the cost of the DCO is minimized. When we substitute $m_{i}\left(\lambda_{i}\right)$ into the objective function (6) of the DCO, we have a new optimization problem as follows:

$$
\max _ {\lambda} V = \sum_ {i \in I} f _ {i} (\lambda_ {i})\tag{17}
$$

$$
\text { s.t. } \quad \sum_ {i \in I} \lambda_ {i} = \Lambda ,\tag{18}
$$

$$
0 \leq \lambda_ {i},\tag{19}
$$

where $A_{i} = \frac{P_{peak} - P_{idle}}{\mu_{i}} +\frac{P_{idle} + (\eta -1)P_{peak}}{\mu_{i} - 1 / (T_{QoS} - d_{i})}$ and

$$
f _ {i} (\lambda_ {i}) = \omega A _ {i} ^ {2} \lambda_ {i} ^ {2} + \lambda_ {i} (p _ {i} A _ {i} - 2 \omega A _ {i} r _ {i}) + \omega r _ {i} ^ {2}.\tag{20}
$$

By solving the above convex optimization problem, we have the unique solution of the DCO when p of the SGs is given:

$$
\lambda_ {i} ^ {*} = \frac {\theta - p _ {i} A _ {i}}{2 \omega A _ {i} ^ {2}} + \frac {r _ {i}}{A _ {i}},\tag{21}
$$

where $\theta = \left(2\omega (\Lambda -\sum_{i\in I}\frac{r_i}{A_i}) + \sum_{i\in I}\frac{p_i}{A_i}\right) / \sum_{i\in I}\frac{1}{A_i^2}.$

2) Noncooperative Pricing Game for SGs at Stage I: Given the electricity price $p_{i}$ for SGs, we can determine the closed optimal strategy of the DCO. Because of the leader's first-move advantage, SGs can predict the optimal strategy of the DCO. From (9), we have

$$
\begin{array}{r} u _ {i} (p _ {i}, p _ {- i}) = e _ {i} ^ {*} p _ {i} - a _ {i} (e _ {i} ^ {*} - r _ {i}) ^ {2} \\ - b _ {i} (e _ {i} ^ {*} - r _ {i}) - c _ {i}. \end{array}\tag{22}
$$

Combining the expression in (22) with that in (23), we can obtain a formula in which energy consumption is only related to the electricity price:

$$
e _ {i} ^ {*} (p _ {i}, p _ {- i}) = p _ {i} N _ {i} + h (p _ {- i})\tag{23}
$$

and $N_{i} = \left(1 / (A_{i}^{2}\sum_{i\in I}\frac{1}{A_{i}^{2}}) - 1\right) / 2\omega ,$

$$
h \left(p _ {- i}\right) = \frac {1}{2 \omega A _ {i} \sum_ {i \in I} \frac {1}{A _ {i} ^ {2}}} \sum_ {j \neq i} \frac {p _ {j}}{A _ {j}} + \frac {\Lambda - \sum_ {i \in I} \frac {r _ {i}}{A _ {i}}}{A _ {i} \sum_ {i \in I} \frac {1}{A _ {i} ^ {2}}} + r _ {i}.
$$

Lemma 1: The utility function $u_{i}(p_{i}, p_{-i}, \lambda)$ of SG i is strictly concave.

Proof Taking the second derivatives of $u_{i}(p_{i}, p_{-i}, \lambda)$ with respect to $p_{i}$ , we have

$$
\frac {\partial^ {2} u _ {i} (p _ {i} , p _ {- i} , \lambda)}{\partial p _ {i} ^ {2}} = 2 N _ {i} - 2 a _ {i} N _ {i} ^ {2}.\tag{24}
$$

The right-hand side of (24) is always negative. Therefore, the utility function $u_{i}(p_{i}, p_{-i}, \lambda)$ of SG i is strictly concave.

Proposition 1: There exists a Nash equilibrium among the SGs in the noncooperative game.

Proof: Since the strategy of each DC $i \in I$ , $p_{i}$ must be in $[0, \pi]$ , the strategy set P is a closed, bounded, and convex subset of a finite-dimensional Euclidean space, and the utility functions are continuous and concave; the noncooperative game among the SGs has at least one Nash equilibrium [16].

Now we prove the uniqueness of the Nash equilibrium by showing that the best response function for each SG i is a contraction mapping.

Definition 2: The mapping $T: \Omega \to \Omega$ is a contraction if there is a scalar $0 \leqslant \sigma < 1$ such that [17]:

$$
\left\| \boldsymbol {T} (x) - \boldsymbol {T} (y) \right\| \leq \sigma \| x - y \|, \forall x, y \in \Omega .\tag{25}
$$

Proposition 2: The best response function $BR_{i}(p_{-i})$ of SG i is a contraction mapping of $p_{-i}$ .

Proof: By setting $\frac{\partial u_i(p_i,p_{-i},\lambda)}{\partial p_i} = 0$ , we can obtain the best response function $BR_i(p_{-i})$ of SG:

$$
B R _ {i} (p _ {- i}) = h (p _ {- i}) \frac {2 a _ {i} N _ {i} - 1}{2 N _ {i} - 2 a _ {i} N _ {i} ^ {2}} + \frac {b _ {i} - 2 a _ {i} r _ {i}}{2 - 2 a _ {i} N _ {i}}.\tag{26}
$$

According to [18], we can state that $BR_{i}(p_{-i})$ is a contraction mapping when

$$
\left\| \frac {\partial B R (p)}{\partial p} \right\| _ {\infty} <   1.\tag{27}
$$

The Jacobian $\partial BR(p)/\partial p$ is:

$$
\frac {\partial B R _ {i} (p _ {- i})}{\partial p _ {j}} = \left\{ \begin{array}{l l} 0, & \forall j = i \\ \frac {2 a _ {i} N _ {i} - 1}{2 - 2 a _ {i} N _ {i}} \frac {1}{2 \omega N _ {i} A _ {i} A _ {j} \sum_ {i \in I} 1 / A _ {i} ^ {2}}, & \forall j \neq i \end{array} \right.
$$

Therefore, we need to prove that

$$
\max _ {i} \left| \sum_ {j \neq i} \frac {2 a _ {i} N _ {i} - 1}{2 - 2 a _ {i} N _ {i}} \frac {1}{2 \omega N _ {i} A _ {i} A _ {j} \sum_ {i \in I} 1 / A _ {i} ^ {2}} \right| <   1.\tag{28}
$$

Firstly, since $N_{i}$ is always negative, we can get that:

$$
\left| \frac {2 a _ {i} N _ {i} - 1}{2 - 2 a _ {i} N _ {i}} \right| = \left| \frac {1 - 2 a _ {i} N _ {i}}{2 - 2 a _ {i} N _ {i}} \right| <   1\tag{29}
$$

Next:

$$
\left| \frac {1}{2 \omega N _ {i} A _ {i} \sum_ {i \in I} \frac {1}{A _ {i} ^ {2}}} \right| = \left| \frac {1}{\left(\frac {1}{A _ {i} ^ {2} \sum_ {i \in I} \frac {1}{A _ {i} ^ {2}}} - 1\right) A _ {i} \sum_ {i \in I} \frac {1}{A _ {i} ^ {2}}} \right|\tag{30}
$$

It is straightforward to see that we only need to prove:

$$
\max _ {i} \left| \frac {1}{A _ {i} \sum_ {j \neq i} \frac {1}{A _ {j} ^ {2}}} \sum_ {j \neq i} \frac {1}{A _ {j}} \right| <   1,\tag{31}
$$

Assuming that $A_{l}$ is the largest in the set $\{A_{i}\}$ , we can find that:

$$
\max _ {i} \left| \sum_ {j \neq i} \frac {1}{A _ {j}} \right| \leq \left| \sum_ {j \neq l} \frac {1}{A _ {j}} \right|\tag{32}
$$

then

$$
\max _ {i} \left| \frac {1}{A _ {i} \sum_ {j \neq i} \frac {1}{A _ {j} ^ {2}}} \right| \leq \left| \frac {1}{A _ {l} \sum_ {j \neq l} \frac {1}{A _ {j} ^ {2}}} \right| <   \left| \frac {1}{\sum_ {j \neq l} \frac {1}{A _ {j}}} \right|\tag{33}
$$

combined with (32) and (33), we find that (28) always holds, and as such, we complete the proof.

## C. Distributed Algorithm to Find Equilibrium Solution

We described how we find the equilibrium solution to S, and a distributed Algorithm 1 is proposed, which can achieve Nash equilibrium.

It is assumed that Algorithm 1 starts running at each hourly updated cycle, and that the algorithm runs multiple iterations (parameter k is the number of iterations) until it converges to a pricing equilibrium. Here, the DCO calculates the energy consumption (23) under the optimal task assignment based on the electricity price collected from each SG and transmits this energy consumption to each SG (step 4). Each SG achieves optimal efficiency by updating its own electricity price with the best response and then rebroadcasting that price to its local DC (Step 5). Even though by adopting Algorithm 1, SGs can work synchronously (i.e., all the SGs update and broadcast their prices at the same time), yet the asynchronous distributed algorithm is preferred since messaging between DCO and SGs often produces heterogeneous latency in reality. Moreover, in (27), the contraction mapping of $\|\cdot\|_{\infty}$ is always valid, so the asynchronous convergence of the mapping sequence is guaranteed [17, p. 431]

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1 Computation Workload Allocation of DCO with Dynamic Pricing

Input:
Parameters of each SG  $i \in I$  and the DCO; RES production  $r_i$ ;  $k = 0, p_i^{(0)} = \pi, \epsilon$  is arbitrarily small.

Output:
The optimal strategy:  $\lambda^*$  and  $\{p_i^*\}_{i \in I}$ .

1: Begin
2: while  $|p^{(k+1)} - p^{(k)}| &gt; \epsilon$  do
3: SG i broadcasts its  $p_i^{(k)}$ ;
4: The DCO collects  $p^{(k)}$  from all SGs, updates  $e_i^*(p)^{(k)}$  as (23) and sends it back to all SGs i;
5: SG i obtains all the information for the k phase system and then updates  $p_i^{(k+1)} := BR_i(p_{-i}^{(k)})$  as (26).
6:  $k := k + 1$ 
7: end while
8: End
</div>

## IV. SIMULATION RESULTS

In this section, we present trace-based simulation results for assessing the performance of the proposed joint energy and computation workload management scheme and evaluate the effects of different parameters.

TABLE I: Generator cost parameters

<table><tr><td>SGs</td><td>a ($/MW $^{2}$ h)</td><td>b ($/MWh)</td><td>c ($/h)</td><td> $\mathcal{P}_{i}^{m}$ </td><td> $\mathcal{P}_{i}^{M}$ </td></tr><tr><td>1, 2</td><td>0.096</td><td>1.22</td><td>51</td><td>0</td><td>200</td></tr><tr><td>3</td><td>0.105</td><td>2.53</td><td>78</td><td>0</td><td>200</td></tr></table>

## A. Setups

We consider 3 DCs (DC1, DC2, DC3) that are geographically located in 3 different regions. At each location, there is one DC powered by an SG, which means that DC i has corresponding SG i. We set PUE as 1.5 for all DCs, and the server has a peak power of 200 W and idle power of 100 W [7]. Each server's service rate is between 1.1 and 1.2 requests per second. The DCO must guarantee the QoS of 100 ms-300 ms [18]. The carbon weight $\omega$ is set to 1 if not specified. Table I gives the cost function parameters of the generators [15], and in order to simplify the model and facilitate the experimental comparison, only two sets of power generation parameters are selected, which means that SG 1 and SG 2 are with the same generation parameters. For renewable energy production, we only consider solar energy to simplify the model, and solar data are from NREL [19].

For comparison, we consider static allocation scheme as the benchmark (i.e., the DCO distributes tasks evenly according to the computing power of the distributed DCs). In such scheme, long-term contracts will be signed with SGs with fixed electricity prices, which is very commonly adopted by companies like Google $[20]$ . It is unfair to compare dynamic pricing with snapshot static pricing in terms of profit, so we will explore the cost of the whole system (i.e., the aggregated energy and emission cost).

## B. Results and Discussion

First, Fig. 2 examines the convergence of the proposed algorithm. We can find that the DCO's benefit function converges quickly, and SGs can also reach the equilibrium point within five iterations. Besides, due to the heterogeneities among SGs in terms of their RES production, generation costs, and geo-DCs, the optimal benefit on different SGs eventually converge to different values.

![](images/5ae64367b0e746f7a9c1bcd521c9609914308904f5bfa3509c675e7312ed66a5.jpg)  
Fig. 2: The profit of different participants of iteration.

TABLE II: Comparison of system costs with respect to the static scheme and the proposed game-based scheme (unit: \$)

<table><tr><td>Scheme</td><td>region 1</td><td>region 2</td><td>region 3</td><td>average</td></tr><tr><td>Static</td><td>192.01</td><td>69.59</td><td>146.30</td><td>135.97</td></tr><tr><td>game-based</td><td>113.74</td><td>104.75</td><td>136.44</td><td>118.31</td></tr><tr><td>Comparison</td><td>↓40.76%</td><td>↑50.50%</td><td>↓6.74%%</td><td>↓12.99%</td></tr></table>

We further compare the system cost (i.e., the aggregated energy and emission cost) under the proposed game-based scheme and the static scheme (i.e., task distributed according to DCs' computing capacities), as depicted in Table II. It is observed that system cost may rise or fall by regions when adopting the proposed game-based scheme, but the average system cost is decreased by approximately 13%, indicating its merit over the static scheme.

In Fig. 3, we investigate the impact of carbon emission cost weighting $\omega$ on task distribution, pricing strategies, and profit function, respectively. From Fig. 3(a), it can be found that weight $\omega$ has little influence on the workload distribution of DCO on geo-DCs. Correspondingly, with the increase of $\omega$ , the electricity price of each SG presents a linear characteristic that is positively correlated with $\omega$ in Fig. 3(b). In Fig. 3(c), since $\omega$ only affects the price of electricity, the quadratic term of $e_{i}$ in the benefit function of DCO and SGs is almost unchanged. Thus, the value of benefit function is also linear with $\omega$ .

Fig.4(c) shows the RES output of SGs over time. The solar capacity peaks mainly from 9 a.m. to 6 p.m. In Fig.4(a), we can find that when the output of renewable energy increased, the number of tasks assigned to DC 3 also increased and even exceeded that of DC 2 for a time period. Since SG 2 is cheaper to generate electricity than SG 3, yet this advantage is diminishing as renewable production increases. For SG 1, it has the least RES, so it needs to reduce the price of electricity to obtain more tasks.

![](images/baa416153e94ed8b790e1b35ea6baa9048afb69e452ab633f5cfa56ea09e1393.jpg)

(a)  
![](images/2529b298004900e02f76bfcadc8d37bd7b87968e0dfefb287d63c68d666b1fb8.jpg)

(b)  
![](images/226bf5285280c05ee5eace4f33d1c01f4cd7eae955c8c93158a4f519f4ce7046.jpg)  
(c)  
Fig. 3: Impact of factor $\omega$ on (a) the computation workload allocation; (b) the pricing strategies; (c) the profits

## V. CONCLUSIONS

In this paper, we establish a joint energy and computation workload management framework for distributed DCs in SG. A Stackelberg game is formulated to model the interactions between DCO and SGs. As the leader, SGs need to decide their electricity price. Then, DCO migrates tasks across geodistributed DCs based on energy costs and carbon emissions. We further prove the existence and uniqueness of the equilibrium solution from the perspectives of backward induction method and contraction mapping. Numerical analysis is conducted to evaluate the performance of the proposed scheme and shows that it can achieve considerable cost savings over counterparts.

## REFERENCES

[1] N. Jones, “How to stop data centres from gobbling up the world’s electricity.” Nature, vol. 561, no. 7722, pp. 163–167, 2018.

[2] B. Dudley et al., “Bp statistical review of world energy,” BP Statistical Review, vol. 6, no. 2018, p. 116, 2018.

[3] R. Xie, X. Jia, K. Yang, and B. Zhang, “Energy saving virtual machine allocation in cloud computing,” in IEEE Int. Conf. on Distributed Comput. Syst. Workshops, 2013, pp. 132–137.

[4] E. M. Elnozahy, M. Kistler, and R. Rajamony, “Energy-efficient server clusters,” in Int. Workshop on Power-Aware Comput. Syst. Springer, 2002, pp. 179–197.

![](images/4e9b1ba4125d86eeb1ea2ec50c59b1a6f5517b06dbe455308d7daa748b123230.jpg)  
(a)

![](images/c300e8776eb631cdb066cb3d625d5848fee62f4651615677c5b6a13bd2add4e3.jpg)  
(b)

![](images/ac568a5597a9824d5d2f4ef5b97dcb1d5c5a4a28cb3e5fcba657ce6b1286a7b1.jpg)  
(c)  
Fig. 4: The impact of RES generation on (a) the computation workload allocation; (b) the pricing strategies. The RES generation is shown in (c).

[5] F. Luo, J. Zhao, Z. Y. Dong, Y. Chen, Y. Xu, X. Zhang, and K. P. Wong, "Cloud-based information infrastructure for next-generation power grid: Conception, architecture, and applications," IEEE Trans. Smart Grid, vol. 7, no. 4, pp. 1896–1912, 2015.

[6] L. Rao, X. Liu, L. Xie, and W. Liu, “Minimizing electricity cost: optimization of distributed internet data centers in a multi-electricity-market environment,” in IEEE Int. Conf. on Comput. Commun., 2010, pp. 1–9.

[7] H. Wang, J. Huang, X. Lin, and H. Mohsenian-Rad, “Proactive demand response for data centers: A win-win solution,” IEEE Trans. Smart Grid, vol. 7, no. 3, pp. 1584–1596, 2015.

[8] D. Whitney et al., “Scaling up energy efficiency across the data center industry: evaluating key drivers and barriers,” Natural Resources Defense Council, 2014.

[9] N. Scenario, M. East, and P. Cedex, “World energy outlook 2014 factsheet,” Paris: Int. Energy Agency, 2015.

[10] P. Patel, A. H. Ranabahu, and A. P. Sheth, “Service level agreement in cloud computing,” 2009.

[11] Z. Liu, M. Lin, A. Wierman, S. H. Low, and L. L. Andrew, “Greening geographical load balancing,” ACM SIGMETRICS Perform. Eval. Rev., vol. 39, no. 1, pp. 193–204, 2011.

[12] Z. Liu, M. Lin, A. Wierman, S. Low, and L. L. Andrew, “Greening

geographical load balancing," IEEE/ACM Trans. Netw., vol. 23, no. 2, pp. 657–671, 2014.

[13] M. Dayarathna, Y. Wen, and R. Fan, “Data center energy consumption modeling: A survey,” IEEE Commun. Surv. Tut., vol. 18, no. 1, pp. 732–794, 2015.

[14] L. Du, A. Hanley, and C. Wei, “Estimating the marginal abatement cost curve of co2 emissions in china: provincial panel data analysis,” Energy Econ., vol. 48, pp. 217–229, 2015.

[15] G. Chen and Z. Zhao, “Delay effects on consensus-based distributed economic dispatch algorithm in microgrid,” IEEE Trans. Power Syst., vol. 33, no. 1, pp. 602–612, 2017.

[16] D. Fudenberg and J. Tirole, Game theory. MIT press, 1991.

[17] D. P. Bertsekas and J. N. Tsitsiklis, Parallel and distributed computation: numerical methods. Prentice hall Englewood Cliffs, NJ, 1989, vol. 23.

[18] N. H. Tran, D. H. Tran, S. Ren, Z. Han, E.-N. Huh, and C. S. Hong, “How geo-distributed data centers do demand response: A game-theoretic approach,” IEEE Trans. Smart Grid, vol. 7, no. 2, pp. 937–947, 2015.

[19] “National renewable energy laboratory,” https://midcdmz.nrel.gov.

[20] H. Xu and B. Li, “Reducing electricity demand charge for data centers with partial execution,” in Proc. of the 5th Int. Conf. on Future energy Syst., 2014, pp. 51–61.