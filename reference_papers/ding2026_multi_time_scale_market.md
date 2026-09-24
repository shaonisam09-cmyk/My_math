# Multi-Time-Scale Joint Optimization of Data Center Market Transactions and Computing Resource Allocation

Zhaohao Ding , Senior Member, IEEE, Shijie Chen , Graduate Student Member, IEEE, Yimeng Sun , Graduate Student Member, IEEE, Yehan Wang , Graduate Student Member, IEEE, Xuan Wei , Member, IEEE, Jun Shu, Member, IEEE, and Payman Dehghanian , Senior Member, IEEE

Abstract—Due to the uncertainty of renewable energy generation, frequency fluctuations and supply-demand imbalance issues of the power grid are becoming increasingly prominent. Since computing jobs can be allocated varying amounts of resources within service level agreement constraints, the data centers possess temporal flexibility in energy consumption. By leveraging this flexibility, data centers can support the grid by participating in ancillary service markets while earning additional revenue. However, the multi-time-scale requirements of grid regulation demand create challenges for joint market bidding and resource allocation strategies. In this paper, we propose a multi-time-scale decision-making approach for data center bidding and resource allocation to provide both frequency regulation and reserve services. First, we model the hour-ahead market bid, real-time power decision, and real-time resource allocation processes as three-layer Markov Decision Processes. Next, we develop a multi-time-scale optimization method based on hierarchical reinforcement learning to solve the proposed model. At the upper layer, the market bid decision is optimized using the Deep Deterministic Policy Gradient algorithm. Based on the bidding, the second layer determines the target operation power. Based on the target power, the third layer optimizes job resource allocation. Finally, we validate the effectiveness of the proposed method for data center market bidding and resource allocation through numerical experiments and analysis.

Index Terms—Data center, demand response, electricity market, resource allocation, hierarchical reinforcement learning.

## I. INTRODUCTION

S RENEWABLE energy continues to grow rapidly, the penetration of wind and solar power in the power grid is steadily increasing [1]. However, due to the intermittency and variability of renewable energy, the frequency stability and power supply of the grid are affected [2]. This enables the grid to meet frequency regulation and reserve demands by leveraging flexible resources in the frequency regulation and reserve markets. As an industrial-grade power consumer and a flexible resource, the data center has the potential to provide regulation and reserve capacity to support the grid. Specifically, data centers can flexibly allocate resources for computing jobs within Service Level Agreement (SLA) constraints to regulate energy consumption patterns [3]. By leveraging this temporal flexibility in energy consumption, the data center can participate in frequency regulation and reserve markets to help the grid maintain frequency stability and supply-demand balance, while also earning additional revenue. While full-scale commercial deployment is still lacking, companies such as Google, Microsoft, and Alibaba have conducted preliminary explorations and pilot studies demonstrating the technical feasibility of leveraging the data center for frequency regulation and reserve services [4], [5], [6].

Recently, many researchers have explored data center participation in frequency regulation and reserve markets. For the frequency regulation market, Kaur et al. [7] propose a decision framework that coordinates data center workload scheduling and battery charging and discharging strategies, enabling the system to track frequency regulation signals while ensuring service quality. Li et al. [8] investigate the potential of the large data center to support frequency regulation by developing an aggregated power response model. Guruprasad et al. [9] develop an integrated data center and battery system designed to rapidly respond to frequency regulation requirements. Qi et al. [10] explore effective control strategies for the data center to participate in primary frequency response within islanded microgrids. Li et al. [11] propose a two-layer power management framework that enables the data center to track frequency regulation market signals. Brocanelli et al. [12] propose a novel power management scheme that leverages a data center to maximize the revenues that the data center receives from the regulation market. For the reserve market, Ghamkhari et al. [13] develop

Digital Object Identifier 10.1109/TIA.2025.3603519

Color versions of one or more figures in this article are available at https://doi.org/10.1109/TIA.2025.3603519.

an optimization framework for the data center participating in reserve market operations, aiming to maximize their profits. Their analysis confirmed that providing ancillary services could bring economic benefits to the data center. Aikema et al. [14] explore how the large data center could leverage energy management flexibility to participate in the reserve market, thereby reducing energy costs. Ghamkhari et al. [15] propose an energy portfolio optimization framework, formulated as a mixedinteger linear programming model, to help the data center manage the risks associated with participating in reserve services. Cao et al. [16] introduce a two-layer optimization workload scheduling approach that integrates energy storage and data center participation in reserve services, aiming to enhance economic efficiency for operators while improving energy storage utilization.

However, these studies consider data center participation in the frequency regulation or reserve market but do not address simultaneous participation in both markets. In real-world operation, the data center can fully leverage flexibility and increase revenue by participating in both markets simultaneously. However, the data center faces the challenge of maximizing the flexibility of energy consumption cross-time-scale sharing, particularly when dealing with potential conflicts across multi-time-scale market demands. Specifically, the frequency regulation market operator issues a regulation signal every 4 seconds, requiring the data center to adjust their power consumption accordingly [17]. Meanwhile, on a larger timescale of 10 minutes, the data center must also allocate energy reserves to meet the demands of the reserve market [18]. The demands of frequency regulation and reserve markets span multi-time-scale, and at times, they may even conflict. This creates a significant challenge for the data center, which needs to manage the conflicting demands of both markets while optimizing energy consumption. Therefore, the data center needs to leverage resource allocation to maximize the flexibility of energy consumption cross-time-scale sharing, striking a balance between the frequency regulation and reserve markets.

Furthermore, the aforementioned studies primarily focus on how the data center responds to market demands during realtime operation, without considering the coupling impact of bidding decisions with operational decisions. To address this gap, some researchers have conducted relevant research. Jahanshahi et al. [19] propose a Quality-of-Service-aware bidding and energy adjustment framework for the data center, which selects the optimal bidding value within the feasible range, aiming to provide practical frequency regulation services. Chen et al. [20] develop an optimization method for joint resource allocation and market bidding in the data center based on stochastic dynamic programming, ensuring service quality during frequency regulation participation. Brocanelli et al. [21] present a two-stage decision-making model that maximizes data center revenue from frequency regulation participation, where bidding and operational decisions are solved sequentially. Wang et al. [22] propose an integrated framework that enables the data center to provide frequency regulation services across both day-ahead and real-time markets. Zhang et al. [23] investigate frequency regulation services provided by the data center equipped with energy storage, optimizing the bidding strategy under deterministic load conditions. Chen et al. [24] introduce a dynamic server power regulation strategy that responds to market signals while maintaining service quality, jointly optimizing bidding and operational decisions with the objective of profit maximization. Borjeson et al. [25] evaluate the participation of the energy storage-equipped data center in system balancing services. Chen et al. [26] propose Energy and Quality-of-Service Aware Reserve Enabler, a mechanism that helps the data center participate in reserve services, where bidding decisions are determined based on the data center’s preferences. However, these studies either overlook the impact of real-time operation on bidding decisions or oversimplify this interaction by making strong assumptions or optimizing both stages simultaneously. In practice, however, the process of data center participation in markets consists of two sequential and tightly interconnected stages: hour-ahead bidding and real-time response. When market bidding, the data center needs to consider its real-time operation flexibility, while resource allocation is later guided by the bidding results. Therefore, the hour-ahead bidding decisions are influenced by real-time operational considerations while also constraining the operational decisions. The market bidding and resource allocation strategies are coupled, jointly determining the data center’s demand response performance and overall revenue.

In this paper, we establish a multi-time-scale decision model for the data center based on hierarchical reinforcement learning (HRL). This model aims to optimize data center participation in the frequency regulation market, reserve market, and spot market by refining bidding and resource allocation strategies to reduce operational costs and generate ancillary service revenue. The main contributions of this paper are summarized as follows:

1) We propose an optimization framework for data center market bidding and resource allocation, considering participation in the frequency regulation market, reserve market, and spot market. This framework explicitly accounts for the coupling effect between hour-ahead market bidding and real-time resource allocation strategies, aiming to increase the overall revenue of the data center.

2) We establish a multi-time-scale strategy optimization model for cross-time-scale sharing of data center energy consumption as three-layer Markov decision processes (MDPs). The data center allocates resources to jobs, adjusting energy consumption in response to multi-timescale market demand, which enhances energy efficiency and enables precise control of energy consumption.

3) We propose an online HRL-based data center market participation and resource allocation solution method, optimizing multi-time-scale strategies to efficiently provide multiple services. The data center is able to update decisions dynamically based on real-time market signals and operational states, thereby improving the adaptability of decision-making.

The rest of this paper is organized as follows. Section II illustrates the setup of the proposed MDP framework. Section III presents a solution method based on HDL. Case studies are presented in Section IV, and Section V concludes.

![](ding2026_multi_time_scale_market_assets/images/45b07f052662d67f7d8a4e3968b4705530520ba17542c62199e763abc00fd6e3.jpg)  
Fig. 1. Market participation framework and the corresponding timeline.

## II. PROBLEM FORMULATION

In this section, we focus on the optimization of the multitime-scale resource allocation strategy and market bidding strategy for the data center. The goal is to enable the data center energy consumption cross-time-scale sharing, while actively responding to market demand to support the power grid and earn additional revenue. First, we provide an overview of the data center’s participation in the frequency regulation, reserve, and spot markets, along with the resource allocation process. Then, we formulate the decision-making process as three-layer MDPs.

## A. Process Description

Fig. 1 illustrates the timelines for bidding and real-time response in the frequency regulation market, reserve market, and spot market. As shown in Fig. 1, the process of data center participation and resource allocation is divided into two stages: hour-ahead bidding and real-time operation.

In the hour-ahead bidding stage, the data center forecasts prices in the frequency regulation market, reserve market, and spot market, along with the workload for the next operational hour. Based on this forecast information, the data center determines its bidding quantities for the frequency regulation and reserve markets and signs contracts accordingly. We assume that the data center primarily purchases electricity in the spot market to offset the energy consumption caused by job execution [27], while participating in the frequency regulation and reserve markets to provide ancillary services and earn additional revenue [28]. Moreover, the data center is considered a price taker that submits only quantity bids [29].

In the real-time operation stage, the data center focuses on resource allocation and the response to market demand. In the data center, the incoming jobs are placed in a queue, each with different characteristics, including resource requirements, deadlines, and computational revenues. Resource requirements specify the computing resources a job needs to complete. Deadlines define the latest allowable completion time before penalties apply. Computational revenues represent the income from executing the job. Then, the data center makes resource allocation decisions every second, determining the resource allocation for each heterogeneous job in the queue to adjust energy consumption. While regulating energy consumption through resource allocation, the data center needs to consider the market adjustment signals. The data center receives frequency regulation signals every 4 seconds, which range from -1 to 1. Based on the regulation signal and hour-ahead bidding results, the data center can calculate the target power of market demand. Based on the market demand power, the data center flexibly adjusts power through resource allocation to the response frequency regulation market demand. In parallel, the data center also needs reserve capacity within 10 minutes based on the reserve market bids. In this paper, we do not consider the reserve dispatch condition [30]. The data center reserve capacity is defined as the lower value of the upward and downward reserves, while the upward and downward reserves represent the difference between the operation power and the upper and lower limits. By leveraging resource allocation, the data center needs to adjust its power consumption within a 10-minute timescale to meet the demands of the reserve market. At the same time, since job execution consumes energy, the data center also participates in the spot market during real-time operation, purchasing electricity to meet energy demands. Throughout the process, data center decision-making has the challenge of the coupled effect and the multi-time-scale problem:

On one hand, the data center’s market bidding strategy and resource allocation strategy influence each other. For the market bidding strategy, the data center must consider its real-time operational capacity. An inappropriate bidding strategy can result in insufficient flexibility during real-time operations, making it difficult for the data center to respond to market demands and reducing ancillary service revenue. Alternatively, it may underestimate the real-time flexibility, leading to the loss of potential market revenue. For the resource allocation strategy, the data center must adjust its energy consumption based on the market bidding and demand. A poor resource allocation strategy can lead to situations where the data center fails to adjust energy consumption flexibly to respond to market demand even within an acceptable range, ultimately reducing market revenue. Therefore, the data center needs to carefully balance the market bidding strategy and resource allocation strategy.

On the other hand, the data center’s resource allocation strategy is affected by multi-time-scale market demands. Since the frequency regulation and reserve market demands correspond to 4 seconds and 10 minutes time scales, respectively, the data center must adjust its resource allocation strategies accordingly. If the data center focuses solely on increasing revenue from the frequency regulation market, it may attempt to respond to market demands every 4 seconds. This could potentially lead to insufficient reserve energy for the reserve market within a 10-minute time scale. Conversely, if the data center sacrifices performance in the frequency regulation market to increase reserves in the reserve market, it may reduce its earnings from the frequency regulation market. Therefore, the data center needs to comprehensively consider and balance potential conflicts in market demands across multi-time-scale to achieve energy consumption cross-time-scale sharing.

Additionally, during real-time operation, the data center must also consider job resource demands and electricity prices in the spot market, aiming to reduce energy costs while meeting SLA requirements. The objective is to maximize the operational profit of the data center, which consists of job completion revenue, electricity costs in the spot market, and revenue from frequency regulation and reserve markets. Job completion revenue depends on whether jobs are completed before their deadlines.

<table><tr><td>Hour-Ahead Market Bid Layer</td></tr><tr><td>State: time step in hours, prices of reserve, frequency regulation and spot markets;Action: bidding in the frequency regulation and reserve markets;Reward: data center&#x27;s profit within an hour;</td></tr><tr><td>Real-Time Power Decision Layer</td></tr><tr><td>State: time step in hours, time step in seconds, bidding, signal of frequency regulation market, prices of reserve, frequency regulation and spot markets;Action: operation target power decision of the data center;Reward: data center&#x27;s profit within 4s;</td></tr><tr><td>Real-Time Resource Allocation Layer</td></tr><tr><td>State: time step in hours, time step in seconds, job characteristics, target power;Action: resource allocation to job;Reward: job completion revenue, cost, target power tracking performance;</td></tr></table>

Fig. 2. The framework of the data center market bidding and resource allocation model.

$$
\begin{array}{c c c c c c c c c c} & & \Delta t _ {1} = 1 & & \Delta t _ {2} = 1 & & \Delta t _ {3} = 1 & & T = 1 \\ & & & \Delta t _ {1} = 7 5 & & \Delta t _ {2} = 2 & & \Delta t _ {3} = 6 \\ & & & \dots & 2 9 9 & 3 0 0 & 3 0 1 & \dots & 5 9 9 & 6 0 0 & 6 0 1 \\ & & & & \dots & 5 9 9 & 6 0 0 & \dots & 3 5 9 9 & 3 6 0 0 \end{array}
$$

Fig. 3. Diagram of relevant time intervals.

Electricity costs are influenced by energy consumption and electricity prices, while settlement revenue from frequency regulation and reserve markets is determined by the data center’s tracking signal performance and bidding submitted.

Due to the above process, which includes hour-ahead markets bidding, 10-minute time scale reserve market demands, 4-second time scale frequency regulation market demands, and per-second resource allocation decisions, we divide the decisionmaking process into three layers: the hour-ahead market bid layer, the real-time power decision layer, and the real-time resource allocation layer as shown in Fig. 2. These layers are formulated as MDPs in subsections B, C, and D, respectively. In the following three subsections, we denote the current hour as $T$ and the seconds within the current hour as <sup>t</sup> to distinguish between different timescales. The frequency regulation market and the reserve market signals are received every 4 seconds and 10 minutes, respectively. Meanwhile, the spot market clears and updates prices every 5 minutes. The expressions for the relevant time intervals are defined as (1)–(2) and illustrated in Fig. 3 [31].

$$
t = 4 \times \Delta t _ {1} = 5 \times 6 0 \times \Delta t _ {2} = 1 0 \times 6 0 \times \Delta t _ {3},\tag{1}
$$

$$
0 \leq T \leq 2 4 \quad 0 \leq t \leq 6 0 \times 6 0.\tag{2}
$$

## B. MDP Formulation for Hour-Ahead Market Bid Layer

An hour before the real-time operation, the data center submits bids in the frequency regulation market and the reserve market. We model this process as an MDP, defined by a tuple $\Gamma = \langle S , P , A , R , \gamma \rangle$ , where <sup>S</sup>, <sup>P</sup> , <sup>A</sup>, <sup>R</sup>, and <sup>γ</sup> represent the sets of states, transition probability functions, sets of actions, reward functions, and the discount factor, respectively. The specific definitions are provided as follows.

State: We define the state to include time $T ,$ , the predicted spot market price $\bar { \gamma } _ { \Delta t _ { \gamma } } ^ { e l e }$ the frequency regulation market (including performance price $\bar { \gamma } _ { \Delta t _ { 2 } } ^ { p e r }$ and capacity price $\bar { \gamma } _ { \Delta t _ { 2 } } ^ { c a p } )$ , and the reserve market $\bar { \gamma } _ { T } ^ { r e s }$ for the next hour, as shown in (3). Based on the state information, the agent determines the data center bidding strategy for participating in the frequency regulation market and reserve market.

$$
s _ {T} ^ {m b} = \left(T, \bar {\gamma} _ {\Delta t _ {2}} ^ {e l e}, \bar {\gamma} _ {\Delta t _ {2}} ^ {p e r}, \bar {\gamma} _ {\Delta t _ {2}} ^ {c a p}, \bar {\gamma} _ {T} ^ {r e s}\right).\tag{3}
$$

Action: The action variable depends on the data center bidding in the frequency regulation market with frequency regulating capacity $p _ { T } ^ { \bar { c } a p }$ and mileage $r _ { T } ^ { c a p }$ , and in the reserve market with reserve energy $q _ { T } ^ { r e s } \ [ 3 2 ]$ , as shown in (4).

$$
a _ {T} ^ {m b} = (p _ {T} ^ {c a p}, r _ {T} ^ {c a p}, q _ {T} ^ {r e s}).\tag{4}
$$

Reward: The reward includes the data center’s job completion revenue, energy consumption cost, frequency regulation market revenue, and reserve market revenue, defined as:

$$
r _ {T} ^ {m b} = \sum_ {t} \sum_ {j} \left(\xi_ {j, t} ^ {r a} - \xi_ {j, t} ^ {e c}\right) + \sum_ {\Delta t _ {1}} \xi_ {\Delta t _ {1}} ^ {f r} + \sum_ {\Delta t _ {3}} \xi_ {\Delta t _ {3}} ^ {r e},\tag{5}
$$

where $\xi _ { j , t } ^ { r a }$ and $\xi _ { t } ^ { e c }$ represent the computational revenue and energy consumption cost of job $j$ at time $t . \xi _ { \Delta t _ { 1 } } ^ { f r }$ represents the revenue from the frequency regulation market at time $\Delta t _ { 1 } . \xi _ { \Delta t _ { 3 } } ^ { r e }$ represents the revenue from the reserve market at time $\Delta t _ { 3 }$

Policy and $\gamma .$ Policy $\pi ( a _ { T } ^ { m b } | s _ { T } ^ { m b } )$ refers to the probability of selecting action $a _ { T } ^ { m b }$ while in state $s _ { T } ^ { m b }$ . For each policy, the state-action value function $Q ( s _ { T } ^ { m b } , a _ { T } ^ { m \bar { b } } )$ is denoted in (6), and $\gamma ^ { b }$ is a discount rate.

$$
Q (s _ {T} ^ {m b}, a _ {T} ^ {m b}) = \mathbb {E} (r _ {T} ^ {m b} + \gamma^ {m b} Q (s _ {T + 1} ^ {m b}, a _ {T + 1} ^ {m b})).\tag{6}
$$

The market bidding agent aims to derive the optimal bidding strategy for the data center to maximize its total profit, as defined in (7).

$$
\pi^ {b *} (s _ {T} ^ {m b}, a _ {T} ^ {m b}) = \underset {a _ {T} ^ {m b}} {\arg \max} Q (s _ {T} ^ {m b}, a _ {T} ^ {m b}).\tag{7}
$$

## C. MDP Formulation for Real-Time Power Decision Layer

During real-time operation, the data center makes decisions about the target power for the 4 seconds period based on the market bidding results $a _ { T } ^ { m b } = ( p _ { T } ^ { c a p } , r _ { T } ^ { c a p } , \dot { q } _ { T } ^ { r e s } )$ , and we model this process as an MDP framework. The specific definitions are provided as follows.

State: We define the state $s _ { \Delta t _ { 1 } } ^ { p d }$ to include the following components: the time <sup>t</sup> and $T _ { \ast }$ , the total resource demand of jobs in the queue $d _ { \Delta t _ { 1 } } ^ { q u e }$ , market biddings $p _ { T } ^ { c a p } , r _ { T } ^ { c a p } , q _ { T } ^ { r e s }$ , the frequency regulation signal $s _ { \Delta t _ { 1 } } ^ { c a p }$ , the total energy consumption $q _ { \Delta t _ { 1 } } ^ { s u m }$ , and the market prices $\gamma _ { \Delta t _ { 2 } } ^ { \overline { { p e r } } } , \gamma _ { \Delta t _ { 2 } } ^ { c a p } , \gamma _ { T } ^ { r e s }$ . The state is expressed as:

$$
\begin{array}{c} s _ {\Delta t _ {1}} ^ {p d} = (t, T, d _ {t} ^ {q u e}, p _ {T} ^ {c a p}, r _ {T} ^ {c a p}, s _ {\Delta t _ {1}} ^ {c a p}, q _ {T} ^ {r e s}, q _ {\Delta t _ {1}} ^ {s u m}, \\ \gamma_ {\Delta t _ {2}} ^ {e l e}, \gamma_ {\Delta t _ {2}} ^ {p e r}, \gamma_ {\Delta t _ {2}} ^ {c a p}, \gamma_ {T} ^ {r e s}), \end{array}\tag{8}
$$

$$
d _ {t} ^ {q u e} = \sum_ {j \in Q _ {t}} (d _ {j} - \hat {d} _ {j, t}),\tag{9}
$$

$$
q _ {\Delta t _ {1}} ^ {s u m} = \sum_ {t \in \Gamma_ {t} ^ {r e}} p _ {t} ^ {d c},\tag{10}
$$

where $Q _ { t }$ represents the job queue at time $t , d _ { j }$ and $\hat { d } _ { j }$ denote the resource demand and allocated of the job $j . \Gamma _ { t } ^ { r e }$ and $q _ { \Delta t _ { 1 } } ^ { s u m }$ represent the set of <sup>t</sup> and the total energy consumption of the data center within the 10-minute window, respectively. $p _ { t } ^ { d c }$ represents the actual power of the data center at time <sup>t</sup>, which can be calculated by (11) [33].

$$
p _ {t} ^ {d c} = P U E \times (p _ {i d l e} + \sum_ {j} \varphi_ {j, t} (p _ {p e a k} - p _ {i d l e})),\tag{11}
$$

where <sup>P UE</sup> represents the power usage effectiveness of the data center, $p _ { i d l e }$ and $p _ { p e a k }$ denote the idle power and peak power respectively, and $\textstyle \sum _ { j } \varphi _ { j , t } ^ { r a }$ represents the resource utilization of job <sup>j</sup> at time <sup>t</sup>.

Action: The action is defined as the average target power for the data center in the next 4 seconds period as shown in (12). Based on (11), we can derive the constraint for $p _ { \Delta t _ { 1 } } ^ { t a r g e t }$ as (13).

$$
a _ {\Delta t _ {1}} ^ {p d} = (p _ {\Delta t _ {1}} ^ {t a r g e t}),\tag{12}
$$

$$
P U E \times p _ {i d l e} \leq a _ {\Delta t _ {1}} ^ {p d} \leq P U E \times p _ {p e a k}.\tag{13}
$$

Reward: The reward is defined as:

$$
r _ {\Delta t _ {1}} ^ {p d} = \sum_ {t \in \Gamma_ {t} ^ {f e}} \sum_ {j} (\xi_ {j, t} ^ {r a} - \xi_ {t} ^ {e c}) + \xi_ {\Delta t _ {1}} ^ {f r} + \xi_ {\Delta t _ {3}} ^ {r e},\tag{14}
$$

$$
\xi_ {\Delta t _ {1}} ^ {f r} = \gamma_ {\Delta t _ {2}} ^ {c a p} r _ {T} ^ {c a p} - \gamma_ {\Delta t _ {2}} ^ {p e r} r _ {T} ^ {c a p} \varepsilon_ {\Delta t _ {1}},\tag{15}
$$

where $\Gamma _ { t } ^ { f r }$ represents the set of <sup>t</sup> within the 4-second window. $\varepsilon _ { \Delta t _ { 1 } }$ is defined as the tracking error [34] and calculated by (16)– (17).

$$
\varepsilon_ {\Delta t _ {1}} = \frac {\left| \mathbb {E} _ {t \in \Gamma_ {t} ^ {f e}} p _ {t} ^ {d c} - p _ {\Delta t _ {1}} ^ {f r e} \right|}{r _ {T} ^ {c a p}},\tag{16}
$$

$$
p _ {\Delta t _ {1}} ^ {f r e} = p _ {T} ^ {c a p} + s _ {\Delta t _ {1}} ^ {c a p} r _ {T} ^ {c a p},\tag{17}
$$

where $p _ { \Delta t _ { 1 } } ^ { f r e }$ represents the target power required by the frequency regulation market for the data center during the 4-second period [35].

Additionally, when the data center’s energy consumption within 10 minutes satisfies the condition :

$$
\sum_ {t \in \Gamma_ {t}} P U E \times p _ {i d l e} + q _ {\Delta T} ^ {r e s} \leq q _ {\Delta t _ {1}} ^ {s u m} \leq \sum_ {t \in \Gamma_ {t}} P U E
$$

$$
\times p _ {p e a k} - q _ {\Delta T} ^ {r e s},\tag{18}
$$

the data center will earn revenue from the reserve market [36]. Otherwise, the revenue from the reserve market will be 0, defined as (19).

$$
\xi_ {\Delta t _ {3}} ^ {r e} = \left\{ \begin{array}{l l} \gamma_ {T} ^ {r e s} q _ {\Delta T} ^ {r e s}, & q _ {\Delta t _ {1}} ^ {s u m} \in \Psi . \\ 0, \quad e l s e. \end{array} \right.\tag{19}
$$

Policy and $\gamma .$ The power decision layer optimizes the operation power to maximize the total profit of the data center. The value function $Q \big ( s _ { \Delta t _ { 1 } } ^ { p d } , a _ { \Delta t _ { 1 } } ^ { p d } \big ) \big )$ and the optimal policy $\pi ^ { p d * }$ can be obtained from (20)–(21).

$$
Q (s _ {\Delta t _ {1}} ^ {p d}, a _ {\Delta t _ {1}} ^ {p d}) = \mathbb {E} (r _ {\Delta t _ {1}} ^ {p d} + \gamma^ {p d} Q (s _ {\Delta t _ {1} + 1} ^ {p d}, a _ {\Delta t _ {1} + 1} ^ {p d})),\tag{20}
$$

$$
\pi^ {p *} (s _ {\Delta t _ {1}} ^ {p d}, a _ {\Delta t _ {1}} ^ {p d}) = \underset {a _ {\Delta t _ {1}} ^ {p d}} {\arg \max} Q (s _ {\Delta t _ {1}} ^ {p d}, a _ {\Delta t _ {1}} ^ {p d}).\tag{21}
$$

## D. MDP Formulation for Real-Time Resource Allocation Layer

Based on the average target operating power $a _ { \Delta t _ { 1 } } ^ { p d } = ( p _ { \Delta t _ { 1 } } ^ { t a r g e t } )$ over the 4-second period set by the power decision layer, the data center decides on resource allocation strategy every 1 s. We model this process as an MDP framework. The specific definitions are provided as follows.

State: For job <sup>j</sup> at time <sup>t</sup>, the state includes time <sup>t,</sup> <sup>T</sup> , job characteristics $d _ { j } , t d _ { j } , i _ { j } , \hat { d } _ { j , t }$ , and target power $p _ { \Delta t _ { 1 } } ^ { t a r g e t }$ , as shown in the (22).

$$
s _ {j, t} ^ {r a} = (t, T, d _ {j}, t d _ {j}, i _ {j}, \hat {d} _ {j, t}, p _ {\Delta t _ {1}} ^ {t a r g e t}),\tag{22}
$$

where $d _ { j } , t d _ { j } , v _ { j }$ , and $\hat { d } _ { j , t }$ represent the resource demand, the deadline, the computation revenue, and the resources allocated of the job $j ,$ respectively. Due to the differences in the resource demand, the deadline, the computation revenue, and the resources allocated to the job, the state in which the resource allocation layer schedules heterogeneous jobs varies. Based on different state information, the resource allocation layer achieves optimized scheduling of heterogeneous jobs.

Action: The action $a _ { j , t } ^ { r a }$ is defined as resource allocation of job <sup>j</sup> at time step <sup>t</sup>, which represents the percentage (from 0 to 100%) of resources allocated to the job <sup>j</sup>, as expressed in (23).

$$
a _ {j, t} ^ {r a} = (\varphi_ {j, t}).\tag{23}
$$

The allocated resources must not exceed the job’s required resources, and the total allocation must not exceed the available resource limit, as shown in (24)–(25).

$$
0 \leq \varphi_ {j, t} \leq \hat {d} _ {j, t} - d _ {j},\tag{24}
$$

$$
\sum_ {j} \varphi_ {j, t} \leq 1, \forall t.\tag{25}
$$

When the resource $\varphi _ { j , t }$ is allocated to job $j ,$ the allocated resource is updated as $\hat { d } _ { j , t + 1 } = \hat { d } _ { j , t } + \varphi _ { j , t }$ <sub>t</sub>. Once $\hat { d } _ { j , t } = d _ { j }$ , the job $j$ is completed.

Reward: The reward depends on the revenue $\xi _ { j , t } ^ { r a }$ and energy consumption $\xi _ { j , t } ^ { e c }$ cost from executing job $j ,$ as well as the tracking performance of the target power $p ^ { t a r g e t \Delta t _ { 1 } }$ , as shown in (26)–(29)

$$
r _ {j, t} ^ {r a} = \xi_ {j, t} ^ {r a} - \xi_ {j, t} ^ {e c} - \xi_ {j, t} ^ {p t},\tag{26}
$$

$$
\xi_ {j, t} ^ {r a} = i _ {j} \frac {\varphi_ {j , t} ^ {r a}}{d _ {j}} - b _ {j, t},\tag{27}
$$

$$
\xi_ {j, t} ^ {e c} = \gamma_ {\Delta t _ {2}} ^ {e l e} \times P U E \times \varphi_ {j, t} (p _ {p e a k} - p _ {i d l e}),\tag{28}
$$

$$
\xi_ {j, t} ^ {p t} = \omega \left| p _ {t} ^ {d c} - p _ {\Delta t _ {1}} ^ {t a r g e t} \right|,\tag{29}
$$

![](ding2026_multi_time_scale_market_assets/images/24249cf690755d831ea6a571dca238a069fc61e66b40629094962b7697ea61ba.jpg)  
Fig. 4. The workflow of the proposed HRL-based data center market bidding and resource allocation algorithm.

where $\xi _ { j , t } ^ { p t }$ denotes the penalty imposed by the resource allocation layer for deviations in responding to the target operating power determined by the power decision layer, $b _ { j , t }$ represents the penalty for job <sup>j</sup> exceeding its deadline at time <sup>t</sup>, and <sup>ω</sup> is a constant indicating the weight assigned by the resource allocation layer to the response of the target operating power.

Policy and $\gamma .$ The goal of the resource allocation layer is to maximize job computing profits based on the target power. The value function $Q \big ( s _ { j , t } ^ { r a } , a _ { j , t } ^ { r a } \big ) \big )$ and the optimal policy $\pi ^ { r a * }$ can be <sup>(</sup>obtained from (30)–(31).

$$
Q (s _ {j, t} ^ {r a}, a _ {j, t} ^ {r a})) = \mathbb {E} (r _ {j, t} ^ {r a} + \gamma^ {r a} Q (s _ {j, t + 1} ^ {r a}, a _ {j, t + 1} ^ {r a})),\tag{30}
$$

$$
\pi^ {j *} (s _ {j, t} ^ {r a}, a _ {j, t} ^ {r a}) = \underset {a _ {j, t} ^ {r a}} {\arg \max} Q (s _ {j, t} ^ {r a}, a _ {j, t} ^ {r a}).\tag{31}
$$

## III. SOLUTION METHOD

In this section, we first describe the data center’s market bidding and resource allocation method. Then, we elaborate on the HRL-based multi-time-scale model’s environment interaction and training process.

## A. Method Description

Considering that a key feature of the proposed model is that decisions made at the upper layer are passed down to the next layer and become part of its state, directly influencing its decision-making process. Moreover, decisions at higher layers represent more abstract and long-term planning. For example, the market bid layer operates on an hourly timescale, the power decision layer works on a 4-second timescale, and the resource allocation layer follows a 1-second timescale. HRL is wellsuited for decomposing complex decision-making problems into multiple levels, with each layer focusing on its own sub-goal. This approach naturally aligns with the multi-stage strategic requirements of hour-ahead bidding and real-time resource allocation in the data center. Therefore, based on the characteristics of the proposed model, we develop an HRL-based approach to optimize market bids, power decisions, and resource allocation strategies.

The workflow of the proposed HRL-based data center market bidding and resource allocation algorithm is illustrated in Fig. 4.

We use the Deep Deterministic Policy Gradient (DDPG) algorithm [37] to train the decision-making model. The proposed data center participation in the market and resource allocation algorithm consists of three hierarchical layers, each with the same components. Based on the DDPG algorithm, we use deep neural networks to establish two approximation functions of the actor-critic algorithm: the actor network can be described as a policy function $\mu ( s | \theta _ { A } )$ with parameter $\mu ( s | \theta _ { A } )$ ; the critic network can be described as an action-value function $Q ( s , a | \theta _ { C } )$ For simplicity, Fig. 4 only depicts one actor-critic pair in each layer. To mitigate Q-value overestimation and enhance training stability, we also introduce separate target actor and target critic networks with parameters $\theta _ { t A }$ and $\theta _ { t C }$ respectively.

## B. Environment Interaction and Model Training

The proposed coordinated optimization model for data center market participation and resource allocation interacts with the environment through a three-stage process.

The first stage is the market bid layer: the agent determines the data center’s bidding strategy and submits bids to the ancillary service market. The state $s _ { T } ^ { m b }$ defined in (3) is fed into the actor network $\theta _ { A } ^ { m b }$ , which consists of four fully connected layers to process the state information. Since the bidding decision is a continuous variable, the policy is approximated by a Gaussian distribution $\mathcal { N }$ with the mean $\mu ( s _ { T } ^ { m b } )$ and variance $\sigma ( s _ { T } ^ { m b } )$ from which the action is sampled. The critic network takes the state-action pair $( s _ { T } ^ { m b } , a _ { T } ^ { m b } )$ as input and outputs the estimated Q-value $Q ( s _ { T } ^ { m b } , a _ { T } ^ { \bar { m b } } )$ , with the same fully connected structure as the actor. In the second stage, the power decision layer: the bidding decisions are passed down to this layer, as a part of the state $s _ { \Delta t _ { 1 } } ^ { \breve { p } d }$ . The actor network $\theta _ { A } ^ { p d }$ receives the state $s _ { \Delta t _ { 1 } } ^ { p i }$ defined in (8) and generates the target power for the next 4-second period, as described in (12). Since target power is also a continuous variable, it is similarly sampled from a Gaussian distribution $\mathcal { N } ( \mu ( s _ { \Delta t _ { 1 } } ^ { p d } ) , \sigma ( s _ { \Delta t _ { 1 } } ^ { p d } ) )$ , and subsequently passed to the resource allocation layer. The critic network in this stage maintains the same structure as in the previous stage. In the third stage, the resource allocation layer: the actor network $\theta _ { A } ^ { j b }$ receives the state $s _ { j , t } ^ { j b }$ defined in (22) and outputs the continuous action representing the allocation of computing resources to different jobs, sampled from a Gaussian distribution $\mathcal { N } ( \mu ( s _ { j , t } ^ { r a } ) , \sigma ( s _ { j , t } ^ { r a } ) )$ . Once the resource allocation decision is completed for the 4-second period, the reward $r _ { j , t } ^ { j b }$ is calculated based on (26). After 4-second period, the power decision layer calculates its reward $r _ { \Delta t _ { 1 } } ^ { p d }$ using (14), and after completing an hour of decisions, the market bid layer computes its reward $r _ { T } ^ { m b }$ according to (26). After the entire interaction process ends, the state, action, and reward data from all three layers are stored separately in their respective replay buffers $B _ { 1 } , B _ { 2 }$ , and $B _ { 3 }$

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1: HRL-Based Data Center Multi-Time-Scale Decision-Making Process.

1 Initialize the environment, online net parameters $\theta_A^{mb}$, $\theta_C^{mb}$, $\theta_A^{pd}$, $\theta_C^{pd}$, $\theta_A^{ra}$, $\theta_C^{ra}$, and target net parameters $\theta_{tA}^{mb}$, $\theta_{tC}^{mb}$, $\theta_{tA}^{pd}$, $\theta_{tC}^{pd}$, $\theta_{tA}^{ra}$, $\theta_{tC}^{ra}$;

2 for episode = 1 to MaxEpisodes do

3    for T = 1 to 24 do

4    Get $s_{mb}^T$ by (3);
5    Use actor network $\theta_A^{mb}$ to select action $a_T^{mb}$ according to the ε-greedy policy and execute;
6    for $\Delta t_1 = 1$ to $60 \times 15$ do

7    Get $s_{\Delta t_1}^{pt}$ by (8);
8    Use actor network $\theta_A^{pt}$ to select action $a_{\Delta t_1}^{pt}$ according to the ε-greedy policy;
9    for i=1 to 4 do

10    $t = 4\Delta t_1 + i$;

11    for j=1 to len($Q_t$) do

12    Get $s_{j,t}^{jb}$ by (22);
13    Use actor network $\theta_A^{jb}$ to select action $a_{j,t}^{jb}$ according to the ε-greedy policy and execute;
14    State $s_{j,t}^{jb}$ transit to the next state $s_{j+1,t}^{jb}$;
15    Calculate the reward $r_{j,t}^{jb}$ by (26)-(29);

16    State $s_{\Delta t_1}^{pt}$ transit to the next state $s_{\Delta t_1+1}^{pt}$;
17    Calculate the reward $r_{\Delta t_1}^{pd}$ by (11)-(15);

18    State $s_{mb}^T$ transit to the next state $s_{mb}^{T+1}$;
19    Calculate the reward $r_T^{mb}$ by (5);

20    Sample $N_b$ trajectories from $B_1$, $B_2$, $B_3$;
21    Calculate the Policy Gradient and the Loss Function by (32)-(38);
22    Update the online network $\theta_A^{mb}$, $\theta_C^{mb}$, $\theta_A^{pd}$, $\theta_C^{pd}$, $\theta_A^{ra}$, $\theta_C^{ra}$;
23    if episode % $N_f$ = 0 then

24    Update the target network $\theta_{tA}^{mb}$, $\theta_{tC}^{mb}$, $\theta_{tA}^{pd}$, $\theta_{tC}^{pd}$, $\theta_{tA}^{ra}$, $\theta_{tC}^{ra}$ by (39)-(40);
</div>

Additionally, the training processes for the market bid layer, power decision layer, and resource allocation layer are conducted simultaneously. Utilizing the DDPG algorithm, the objective of each layer’s network is to maximize the value function in order to obtain the optimal policy as shown in (7), (21), and (30). Therefore, the networks of each layer need to optimize policy by updating the weight parameters after interacting with the environment. Specifically, the market bid layer’s actor network interacts with the environment once an hour, storing the observed state, action, and reward information in the replay buffer $B _ { 1 }$ . After that, $N _ { b }$ trajectories are randomly drawn from $B _ { 1 } ,$ which contain states, actions, rewards, and next states. Using this batch of data, the actor network for market bid, denoted as $\theta _ { A } ^ { m b }$ , calculates the loss and updates its network parameters according to (32), which provides the gradient direction to adjust the bidding strategy, aiming to increase the expected long-term revenue through interaction with the market environment.

$$
\nabla_ {\pi_ {\theta_ {A} ^ {m b}}} J = \nabla_ {a _ {T} ^ {m b}} Q _ {\theta_ {C} ^ {m b}} (s _ {T} ^ {m b}, a _ {T} ^ {m b}) \nabla_ {\theta_ {A} ^ {m b}} \pi_ {\theta_ {A} ^ {m b}} (s _ {T} ^ {m b}).\tag{32}
$$

Next, the critic network for the market bid layer, denoted as $\theta _ { C } ^ { m b }$ , evaluates the accuracy of value estimation and refines its parameters according to (33)–(34), which minimizes the error between predicted Q-values and the target values derived from observed rewards.

$$
L (\theta_ {C} ^ {m b}) = \mathbb {E} [ Q _ {\theta_ {C} ^ {m b}} (s _ {T} ^ {m b}, a _ {T} ^ {m b}) - y _ {T} ],\tag{33}
$$

$$
y _ {T} = r _ {T} ^ {m b} + \gamma Q _ {\theta_ {t C} ^ {m b}} (s _ {T} ^ {m b}, \pi_ {\theta_ {t A} ^ {m b}} (s _ {T} ^ {m b})).\tag{34}
$$

Every 4 seconds, the power decision layer makes a decision and interacts with the environment. After the interaction, the network is learning and updated. The actor network for power decision layer, denoted as $\theta _ { A } ^ { p d }$ , adjusts its policy using (35), which guides how control actions should be modified to improve long-term performance under dynamic power conditions.

$$
\nabla_ {\pi_ {\theta_ {A} ^ {p d}}} J = \nabla_ {a _ {\Delta t _ {1}} ^ {p d}} Q _ {\theta_ {C} ^ {p d}} (s _ {\Delta t _ {1}} ^ {p d}, a _ {\Delta t _ {1}} ^ {p d}) \nabla_ {\theta_ {A} ^ {p d}} \pi_ {\theta_ {A} ^ {p d}} (s _ {\Delta t _ {1}} ^ {p d}).\tag{35}
$$

Next, the critic network for the power decision layer, denoted as $\theta _ { C } ^ { p d }$ , refines its value function using (36), which reduces the discrepancy between predicted and target values to ensure accurate evaluation of power decisions.

$$
\begin{array}{c} L (\theta_ {C} ^ {p d}) = \mathbb {E} [ Q _ {\theta_ {C} ^ {p d}} (s _ {\Delta t _ {1}} ^ {p d}, a _ {\Delta t _ {1}} ^ {p d}) - \\ (r _ {\Delta t _ {1}} ^ {p d} + \gamma Q _ {\theta_ {t C} ^ {p d}} (s _ {\Delta t _ {1}} ^ {p d}, \pi_ {\theta_ {t A} ^ {p d}} (s _ {\Delta t _ {1}} ^ {p d}))) ]. \end{array}\tag{36}
$$

Every second, the resource allocation layer makes a decision and interacts with the environment. After each interaction, the network undergoes learning and updates its parameters. The actor network for the resource allocation layer, denoted as $\theta _ { A } ^ { r a } .$ improves its action selection using (37), which determines the adjustment direction for allocating computing resources more effectively based on the current state.

$$
\nabla_ {\pi_ {\theta_ {A} ^ {r a}}} J = \nabla_ {a _ {j, t} ^ {r a}} Q _ {\theta_ {C} ^ {r a}} (s _ {j, t} ^ {r a}, a _ {j, t} ^ {r a}) \nabla_ {\theta_ {A} ^ {r a}} \pi_ {\theta_ {A} ^ {r a}} (s _ {j, t} ^ {r a}).\tag{37}
$$

Next, the critic network for the resource allocation layer, denoted as $\theta _ { C } ^ { r a }$ , updates its value estimate using (38), which evaluates how well a resource allocation action contributes to future system performance.

$$
\begin{array}{c} L (\theta_ {C} ^ {r a}) = \mathbb {E} [ Q _ {\theta_ {C} ^ {r a}} (s _ {j, t} ^ {r a}, a _ {j, t} ^ {r a}) - (r _ {j, t} ^ {r a} + \\ \gamma Q _ {\theta_ {t C} ^ {r a}} (s _ {j, t} ^ {r a}, \pi_ {\theta_ {t A} ^ {r a}} (s _ {j, t} ^ {r a})) ]. \end{array}\tag{38}
$$

TABLE I  
THE PARAMETER SETTINGS OF THE PROPOSED MODEL

<table><tr><td>Parameter</td><td>Value</td><td>Parameter</td><td>Value</td></tr><tr><td> $\gamma^{pd}$ </td><td>0.99</td><td> $\gamma^{mb}$ </td><td>0.99</td></tr><tr><td> $\gamma^{ra}$ </td><td>0.99</td><td>PUE</td><td>1.08</td></tr><tr><td> $p_{idle}$ </td><td>55</td><td> $p_{peak}$ </td><td>240</td></tr><tr><td> $N_b$ </td><td>32</td><td> $N_f$ </td><td>10</td></tr><tr><td>Learning rate</td><td>0.001</td><td>Fully connected layers</td><td>128, 256, 256, 128</td></tr></table>

TABLE II

THE PARAMETER SETTING OF COMPUTING JOBS

<table><tr><td>Parameters</td><td>Value</td></tr><tr><td>Job arrival pattern</td><td> $\mathcal{N}(12 \times 60 \times 60, 4 \times 3600^2)$ </td></tr><tr><td>Resource demand  $d_j$ </td><td>0.075-0.2</td></tr><tr><td>Deadline  $td_j$ </td><td>1h-2h</td></tr><tr><td>Computation revenue  $i_n$ </td><td>2500</td></tr></table>

After a specified number $N _ { f }$ of online network updates, soft updates are performed using equations (39) to (40), gradually synchronizing the target networks with their corresponding online networks.

$$
\theta_ {t A} = \tau \theta_ {A} + (1 - \tau) \theta_ {t A},\tag{39}
$$

$$
\theta_ {t C} = \tau \theta_ {C} + (1 - \tau) \theta_ {t C}.\tag{40}
$$

The HRL-based model training process for data center participation in multiple markets and resource allocation is illustrated in Algorithm 1.

## IV. CASE STUDY

## A. Experimental Setting

In this section, we conduct numerical experiments to demonstrate the effectiveness of the proposed joint decision-making method for the data center participating in frequency regulation, reserve, and spot markets, and coordinating resource allocation. The key parameters used in the numerical case are shown in Table I. The experiments treat electricity prices and job parameters as stochastic parameters. The electricity price data is obtained from PJM [38], as shown in Fig. 5. The characteristics of heterogeneous jobs processed by the data center are listed in Table II.

## B. Data Center Resource Allocation Results Analysis

Fig. 6 illustrates the arrival job resource demand, allocated resources, and the resource demand of queued jobs in the data center. It is evident that between 8:00 and 9:00, when spot market prices are significantly higher, the data center reduces the resources allocated to jobs, causing some jobs to accumulate in the queue. As a result, the resource demand for queued jobs increases. After 13:00, the data center allocates resources to both arriving jobs and jobs in the queue to avoid penalties caused by overdue job completion. Therefore, the data center can consider real-time spot market prices to optimize the resource allocation strategy under the proposed model.

Fig. 7 shows the target power sent from the power decision layer to the resource allocation layer, and the actual operating power of the data center. From the figure, we can observe that the difference between the actual operating power and the target power determined by the model is slight. This indicates that the resource allocation layer in the proposed method can effectively make resource allocation decisions based on the target power provided by the power decision layer, improving the flexibility of the data center’s power adjustment.

![](ding2026_multi_time_scale_market_assets/images/2bcb8e0c76edc95af5443473d1e6d6a988f6110b2e50c82cf4e243150e24d0af.jpg)

![](ding2026_multi_time_scale_market_assets/images/24e56e686da4abfa9c1eec7a29d801c3f56cd831bb1bf98dcab950a24f120c41.jpg)

![](ding2026_multi_time_scale_market_assets/images/f438e597f6c2a2b0ff710c210283bef86e035bd6cdb70b42da181aa6365b9449.jpg)

![](ding2026_multi_time_scale_market_assets/images/dcd9cc850e305894587ad6e3a4350cda37f1dc3153355ad07cbab706bc2f7cb2.jpg)  
Fig. 5. The prices of the spot market, frequency regulation market, and reserve market.

![](ding2026_multi_time_scale_market_assets/images/2fdbd877c55b17043efb41184c27ff7cfb5b35ce2c0491133be82d667efb0b47.jpg)  
Fig. 6. Data center resource allocation results.

## C. Data Center Market Transactions and Demand Response Results Analysis

We demonstrate the effectiveness of the proposed strategy in coordinating reserve market bidding with resource allocation optimization at a 10-minute time scale, as illustrated in Fig. 8. The figure shows the reserve capacity bids submitted to the hour-ahead reserve market, the actual reserve capacity provided during real-time operation, and the reserve market price. The data center has fewer jobs in the queue from 1:00 to 6:00 as shown in Fig. 7, which limits the amount of reserve capacity the data center can provide according to (18), leading to lower reserve bids in the reserve market as shown in Fig. 8. In contrast, between 10:00 and 14:00, the number of jobs in the queue gradually increases, expanding the adjustable energy consumption range through resource allocation. As a result, the reserve bids submitted during the period are relatively higher. After 19:00, the number of arriving jobs in the data center gradually decreases, resulting in insufficient reserves. Consequently, the reserve bidding volume is reduced, as indicated by (18). Therefore, under the proposed strategy, the reserve bids can dynamically account for the job execution condition, thereby improving both the data center’s performance and revenue in the reserve market.

![](ding2026_multi_time_scale_market_assets/images/9785fe5f2268599058f8cebc4738e20bd6f18ce51a5f069e8d0fbdeba8aa02ea.jpg)  
Fig. 7. The data center operation power and target power of the proposed model.

![](ding2026_multi_time_scale_market_assets/images/091c22043bc4b260941e928444f27d47ea14ba5b7bccf93cdd88b9ada84373cd.jpg)  
Fig. 8. The data center’s bidding capacity, actual capacity, and prices in the reserve market.

Moreover, at 8:00 and 18:00, when the reserve market price is high, the proposed strategy submits higher reserve bids in the reserve market to maximize potential revenue. However, during certain 5-minute periods with high spot market prices, the data center reduces its power consumption through resource allocation, which decreases the actual reserve capacity it can provide in real-time. Nevertheless, during periods when spot market prices are lower, the data center can still provide a relatively high reserve capacity. Given that reserve market revenue is directly linked to the bid capacity, as shown in (18)–(19), the proposed strategy continues to submit higher reserve bids to increase the revenue of other periods. Therefore, the proposed strategy enables the data center to effectively integrate price signals from both the reserve market and the spot market across different time scales, leveraging resource allocation to optimize the 10-minute time scale power consumption curve.

Fig. 9 illustrates the performance price, regulation price, and the bidding of the data center in the frequency regulation market. In Fig. 9, the yellow rectangles and the shaded background represent the regulation capacity bid $p _ { T } ^ { c a p }$ and the corresponding regulation range $p _ { T } ^ { c a p } \pm \bar { r } _ { T } ^ { c a p }$ by the data center. It is evident that the regulation capacity follows a trend consistent with the normal distribution of job arrivals. Between 13:00 and 15:00, the frequency regulation mileage bid volume is relatively low. This is because, as shown in Fig. 7, the data center’s job queue is relatively full. If the bid for frequency regulation mileage is too high, the data center may need to reduce its operating power, potentially causing some jobs to fail to meet SLA requirements and resulting in penalties for the data center. Therefore, the proposed strategy takes the actual operational status and regulation capability of the data center into account when submitting bids to the frequency regulation market. This ensures that the market regulation demand response target aligns better with the actual job execution situation, ultimately enhancing both the response performance and revenue in the frequency regulation market.

![](ding2026_multi_time_scale_market_assets/images/b4c35aea0a3aabb1d1b4cba6f82b2218b37f8f62c39a948f79daca6cfddd1a2b.jpg)  
Fig. 9. The data center’s bids in the frequency regulation market under the proposed strategy.

![](ding2026_multi_time_scale_market_assets/images/93f6a35dd0c1341413ea114697e3985d5c0e234a521193d9d4ec56a33cba7210.jpg)  
Fig. 10. The data center tracks the market signal conditions during high-price periods under the proposed strategy.

Furthermore, during periods such as 2:00-5:00, when the capacity price is relatively high, the regulation range is larger. In contrast, between 13:00 and 15:00, the capacity price is lower, and the regulation range shrinks accordingly. As shown in (15), the bidding strategy helps increase the data center’s revenue in the frequency regulation market. Therefore, the proposed strategy can dynamically adjust bidding behavior based on the regulation market price.

To demonstrate how the data center responds to frequency regulation market signals using the proposed strategy, we present in Figs. 10 and 11 the market target power calculated using (17), the actual power of the data center, and the power difference at a 4-second time scale, for both high and low performance prices. From these figures, it can be seen that when the performance price is higher, the gap between the data center’s operating power and the market demand, as shown in Fig. 10, is significantly smaller compared to when the performance price is lower, as shown in Fig. 11. This result indicates that under the proposed strategy, the data center can effectively consider market prices and optimize job resource allocation decisions at the 4-second time scale.

![](ding2026_multi_time_scale_market_assets/images/fc7d0571f9fcd237d70d248ea5db717c59eef8254143a6104df98729e2bbe8fc.jpg)  
Fig. 11. The data center tracks the market signal conditions during low-price periods under the proposed strategy.

![](ding2026_multi_time_scale_market_assets/images/56d7a2eac38d06302da00f074e654ca8e2e8d97a77d6738c6545c93a16048f61.jpg)  
Fig. 12. The job execution and market demand response performance.

To further evaluate the effectiveness of the proposed method under heterogeneous job compositions, the average tracking error defined in (41) is employed as the performance metric. Three cases with varying proportions of delay-sensitive and delay-tolerant jobs are considered, namely Case 1 with a 1-to-9 ratio, Case 2 with a balanced 1-to-1 ratio, and Case 3 with a 9-to-1 ratio.

$$
\overline {{\varepsilon}} = \mathbb {E} _ {T \in [ 0, 2 3 ], t \in [ 0, 3 5 9 9 ]} \left[ \frac {\left| \mathbb {E} _ {t ^ {\prime} \in \Gamma_ {t} ^ {f e}} p _ {t ^ {\prime}} ^ {d c} - p _ {\Delta t _ {1}} ^ {f r e} \right|}{r _ {T} ^ {c a p}} \right].\tag{41}
$$

Fig. 12 illustrates the SLA violation rates and the responsiveness to frequency regulation and reserve market demands under different cases. As the proportion of latency-sensitive jobs increases, we observe a gradual rise in the SLA violation rate, although it remains relatively low overall. Meanwhile, the average tracking error exhibits an upward trend, and the time of meeting reserve market demands declines. These patterns suggest that the data center’s job execution performance and market responsiveness are influenced by job heterogeneity. As the tolerance for job delay decreases, the likelihood of deadline violations increases, and scheduling flexibility is reduced, thereby reducing the ability to respond effectively to frequency regulation and reserve market demands. Consequently, the performance of the proposed model in both job execution and market participation is significantly affected by the configuration of heterogeneous job parameters.

![](ding2026_multi_time_scale_market_assets/images/4b05a18a996674de64e9b6df6e491f05414cf5b6c20e92b30561a51f36f17d83.jpg)  
Fig. 13. The data center’s revenue and cost results.

![](ding2026_multi_time_scale_market_assets/images/eedb5a1d1820eb84448939056eaa69d393ead3bc4bb819b56f33990c401958fc.jpg)  
Fig. 14. The proposed model training results.

## D. The Revenue and Training Reward Results Analysis

Fig. 13 shows the hourly job execution revenue, participation revenue in the frequency regulation market and reserve market, and energy consumption cost in the spot market for the data center. Job execution revenue is relatively high between 10:00 and 16:00, when the job arrival peaks, and lower during the rest of the day. Since electricity consumption is closely linked to resource usage during job execution, the electricity purchase cost is also higher during peak job arrival times. Notably, although the spot market price is high during some periods between 8:00 and 9:00, the data center delays job execution to reduce energy consumption, thus decreasing the energy cost. As a result, the energy cost does not significantly increase during 8:00-9:00. The revenue from participating in the reserve market is higher between 7:00 and 8:00, which aligns with the trend shown in Fig. 8. This is because the reserve market price is relatively high during this period, resulting in higher earnings for the data center after fulfilling the reserve demand. The revenue from participating in the frequency regulation market is higher between 11:00 and 12:00, which is consistent with the findings in Fig. 9. This is due to the higher bidding $r _ { c a p }$ by the data center during this period, which leads to increased settlement revenue in the frequency regulation market according to (15).

Fig. 14 illustrates the reward variations of the market bid layer, power decision layer, and resource allocation layer of the proposed model during training. The reward of the resource allocation layer is determined by job revenue, energy cost, and target power tracking performance at a 1-second time scale. The power decision layer’s reward is based on job revenue, energy cost, and ancillary service revenue at a 4-second time scale. The market bid layer’s reward represents the total profit of the data center over a 1-hour time scale. Due to the differing timescales of these three layers, their rewards have different orders of magnitude. Moreover, the strategies of the market bid layer and power decision layer directly influence the resource allocation layer, while the resource allocation layer’s decisions directly impact job revenue, energy cost, and ancillary service revenue. These factors collectively form the primary components of the rewards for the resource allocation layer, power decision layer, and market bid layer. Therefore, the decisions of these three layers influence each other and directly or indirectly impact their respective rewards. As a result, the rewards of the three layers fluctuate significantly, and a relatively long training period is required for convergence.

## V. CONCLUSION

This paper proposes a joint approach to data center resource allocation and bidding across multiple markets, aiming to support the grid and earn additional revenue. First, The decisionmaking process is formulated as three-layer MDPs, encompassing hour-ahead market bidding, real-time power decisions, and resource allocation layers. To solve the proposed model, an HRL-based optimization method is developed, where the DDPG algorithm is used to train the model. Finally, numerical experiments are conducted to evaluate the performance of the proposed method. The results show that the proposed approach effectively coordinates market bidding decisions with multi-time-scale resource allocation strategies. In addition, some assumptions in this paper may limit the applicability of the results to systems with different market conditions or regulatory frameworks, despite these assumptions being commonly adopted in the literature. Future work could relax or modify these assumptions to further enhance the model’s generalizability and applicability to a wider range of market designs and operational scenarios.

## REFERENCES

[1] M. Hossain, N. Madlool, N. Rahim, J. Selvaraj, A. Pandey, and A. F. Khan, “Role of smart grid in renewable energy: An overview,” Renewable Sustain. Energy Rev., vol. 60, pp. 1168–1184, 2016.

[2] L. G. Meegahapola, S. Bu, D. P. Wadduwage, C. Y. Chung, and X. Yu, “Review on oscillatory stability in power grids with renewable energy sources: Monitoring, analysis, and control using synchrophasor technology,” IEEE Trans. Ind. Electron., vol. 68, no. 1, pp. 519–531, Jan. 2021.

[3] Y. Sun, Z. Ding, Y. Yan, Z. Wang, P. Dehghanian, and W.-J. Lee, “Privacypreserving energy sharing among cloud service providers via collaborative job scheduling,” in IEEE Trans. Smart Grid, vol. 16, no. 2, pp. 1168–1180, Mar. 2025.

[4] “Fluence and centrica work with data centre in Belgium to provide and optimise its first zero-emission backup power system,” Apr.21, 2022 [Online] Available: https://ir.fluenceenergy.com/news-releases/news-releasedetails/fluence-and-centrica-work-data-centre-belgium-provide-and

[5] “Grid-interactive data centers: Enabling decarbonization and system stability,” May 4, 2025 [Online]. Available: https://www.eaton.com/gb/engb/markets/data-centers/transform-your-power/energy-aware/eatonmicrosoft-grid-interactive-data-center-whitepaper.html

[6] “Support the green and low-carbon Alibaba and North China Electric Power University Data Center Computing - Power collaborative Dispatching Project,” Jan. 10, 2023 [Online]. Available: https://developer.aliyun. com/article/1135139

[7] K. Kaur, S. Garg, N. Kumar, G. S. Aujla, K.-K. R. Choo, and M. S. Obaidat, “An adaptive grid frequency support mechanism for energy management in cloud data centers,” IEEE Syst. J., vol. 14, no. 1, pp. 1195–1205, Mar. 2020.

[8] S. Li, M. Brocanelli, W. Zhang, and X. Wang, “Data center power control for frequency regulation,” in Proc. IEEE Power Energy Soc. Gen. Meeting, 2013, pp. 1–5.

[9] R. Guruprasad, P. Murali, D. Krishnaswamy, and S. Kalyanaraman, “Coupling a small battery with a datacenter for frequency regulation,” in Proc. IEEE Power Energy Soc. Gen. Meeting, 2017, pp. 1–5.

[10] W. Qi and J. Li, “Towards green data center microgrids by leveraging data center loads in providing frequency regulation,” Scientia Iranica, Computer Science and Engineering, Electrical, vol. 26, no. 6, pp. 3559– 3570, Dec. 2019, doi: 10.24200/sci.2019.53454.3246.

[11] S. Li, M. Brocanelli, W. Zhang, and X. Wang, “Integrated power management of data centers and electric vehicles for energy and regulation market participation,” IEEE Trans. Smart Grid, vol. 5, no. 5, pp. 2283–2294, Sep. 2014.

[12] M. Brocanelli, S. Li, X. Wang, and W. Zhang, “Joint management of data centers and electric vehicles for maximized regulation profits,” in Proc. Int. Green Comput. Conf. Proc., 2013, pp. 1–10.

[13] M. Ghamkhari and H. Mohsenian-Rad, “Data centers to offer ancillary services,” in Proc. IEEE 3rd Int. Conf. Smart Grid Commun., 2012, pp. 436–441.

[14] D. Aikema, R. Simmonds, and H. Zareipour, “Data centres in the ancillary services market,” in Proc. Int. Green Comput. Conf., 2012, pp. 1–10.

[15] M. Ghamkhari, A. Wierman, and H. Mohsenian-Rad, “Energy portfolio optimization of data centers,” IEEE Trans. Smart Grid, vol. 8, no. 4, pp. 1898–1910, Jul. 2017.

[16] C. Cao, Y. Sun, K. Ding, and M. Jiang, “Operation strategy for active distribution network with energy storage considering Internet data center,” in Proc. 3rd Asia Power Elect. Technol. Conf., 2024, pp. 531–536.

[17] J. Cho and A. N. Kleit, “Energy storage systems in energy and ancillary markets: A backwards induction approach,” Appl. Energy, vol. 147, pp. 176–183, 2015.

[18] T. Zheng and E. Litvinov, “Contingency-based zonal reserve modeling and pricing in a co-optimized energy and reserve market,” IEEE Trans. Power Syst., vol. 23, no. 2, pp. 277–286, May 2008.

[19] A. Jahanshahi, N. Yu, and D. Wong, “PowerMorph: Qos-aware server power reshaping for data center regulation service,” ACM Trans. Archit. Code Optim., vol. 19, no. 3, pp. 1–27, 2022.

[20] H. Chen, B. Zhang, M. C. Caramanis, and A. K. Coskun, “Data center optimal regulation service reserve provision with explicit modeling of quality of service dynamics,” in Proc. 54th IEEE Conf. Decis. Control, 2015, pp. 7207–7213.

[21] M. Brocanelli, S. Li, X. Wang, and W. Zhang, “Maximizing the revenues of data centers in regulation market by coordinating with electric vehicles,” Sustain. Comput.: Inform. Syst., vol. 6, pp. 26–38, 2015.

[22] W. Wang, A. Abdolrashidi, N. Yu, and D. Wong, “Frequency regulation service provision in data center with computational flexibility,” Appl. Energy, vol. 251, 2019, Art. no. 113304.

[23] B. Zhang and E. N. Senior, “Distributed redundant integration of data center battery storage with the grid for regulation services,” in Proc. IEEE Power Energy Soc. Gen. Meeting, 2021, pp. 1–5.

[24] H. Chen, A. K. Coskun, and M. C. Caramanis, “Real-time power control of data centers for providing regulation service,” in Proc. 52nd IEEE Conf. Decis. Control, 2013, pp. 4314–4321.

[25] L. Östbom and S. Börjeson, “Balance the Swedish transmission system by using data centers: A study whether ups-systems can operate as frequency regulators,” B.Sc. thesis, Dept. Eng. Sci., Uppsala Univ., Uppsala, Sweden, 2018. [Online]. Available: http://urn.kb.se/resolve?urn=urn:nbn: se:uu:diva-353249

[26] H. Chen, Y. Zhang, M. C. Caramanis, and A. K. Coskun, “EnergyQARE: QoS-aware data center participation in smart grid regulation service reserve provision,” ACM Trans. Model. Perform. Eval. Comput. Syst., vol. 4, no. 1, pp. 1–31, 2019.

[27] T. N. Le, J. Liang, Z. Liu, R. K. Sitaraman, J. Nair, and B. J. Choi, “Optimal energy procurement for Geo-distributed data centers in multi-timescale electricity markets,” ACM SIGMETRICS Perform. Eval. Rev., vol. 45, no. 2, pp. 58–63, 2017.

[28] M. T. Takci, M. Qadrdan, J. Summers, and J. Gustafsson, “Data centres as a source of flexibility for power systems,” Energy Rep., vol. 13, pp. 3661–3671, 2025.

[29] M. Ghamkhari, H. Mohsenian-Rad, and A. Wierman, “Optimal risk-aware power procurement for data centers in day-ahead and real-time electricity markets,” in Proc. IEEE Conf. Comput. Commun. Workshops, 2014, pp. 610–615.

[30] T. Xu, J. Chen, and F. Wang, “Design of joint electricity energy and reserve market mechanism with bilateral participation based on VCG mechanism,” Sino- Glob. Energy, vol. 30, no. 02, pp. 13–21, 2025.

[31] T. Ochoa, E. Gil, A. Angulo, and C. Valle, “Multi-agent deep reinforcement learning for efficient multi-timescale bidding of a hybrid power plant in day-ahead and real-time markets,” Appl. Energy, vol. 317, 2022, Art. no. 119067.

[32] J. Wang, H. Zhong, Q. Xia, Z. Ma, Z. Wang, and X. Wu, “Robust bidding strategy for microgrids in joint energy, reserve and regulation markets,” in Proc. IEEE Power Energy Soc. Gen. Meeting, 2017, pp. 1–5.

[33] M. Dayarathna, Y. Wen, and R. Fan, “Data center energy consumption modeling: A survey,” IEEE Commun. Surveys Tut., vol. 18, no. 1, pp. 732–794, firstquarter 2016.

[34] Y. Zhang, I. C. Paschalidis, and A. K. Coskun, “Data center participation in demand response programs with quality-of-service guarantees,” in Proc. 10th ACM Int. Conf. Future Energy Syst., 2019, pp. 285–302.

[35] S. Chen, Y. Sun, Y. Wang, and Z. Ding, “Multi-service provision oriented data center job scheduling scheme,” in Proc. IEEE/IAS Ind. Commercial Power Syst. Asia, 2024, pp. 742–747.

[36] W. Jun et al., “Bi-level stochastic optimization for a virtual power plant participating in energy and reserve market based on conditional value at risk,” Power Syst. Technol., vol. 48, no. 6, pp. 2502–2510, 2024.

[37] T. P. Lillicrap et al., “Continuous control with deep reinforcement learning,” 2015, arXiv:1509.02971.

[38] “PJM market data,” Jun. 10, 2023 [Online]. Available: https://www.pjm. com/

![](ding2026_multi_time_scale_market_assets/images/9e664f17971d8f7bc8bd6b21daa826b48088d4399564887f15b19a068c291dc4.jpg)

![](ding2026_multi_time_scale_market_assets/images/ab2d575bf4e5e02fa2b10410395e5108dfd7adcb630d77466423ae029592420e.jpg)

![](ding2026_multi_time_scale_market_assets/images/992f8fe932023b9ce635f6388ee622450ba0e813d88d2efda9ed77ecfbaee4ca.jpg)  
Zhaohao Ding (Senior Member, IEEE) received the B.S. degree in electrical engineering and the B.A. degree in finance from Shandong University, Jinan, China, in 2010, and the Ph.D. degree in electrical engineering from the University of Texas at Arlington, Arlington, TX, USA, in 2015. He is currently a Professor with North China Electric Power University, Beijing, China. His research interests include power system planning and operation, power market, distributed resource management, and electric transportation system.

![](ding2026_multi_time_scale_market_assets/images/4d7086db34a13548c5e2ab6b4034c1a212de627df9caf473aef1964404adc603.jpg)

![](ding2026_multi_time_scale_market_assets/images/0da832dd757282fa514863d2767bbe6e80a722162727b61ae8f637d2752b7c1a.jpg)  
Shijie Chen (Graduate Student Member, IEEE) received the B.S. degree in the electrical engineering and automation in 2023 from North China Electric Power University, Beijing, China, where she is currently working toward the M.S. degree. Her current research interests include data center job scheduling, and participation in the market.

Yehan Wang (Graduate Student Member, IEEE) received the B.S. degree in the electrical engineering and automation in 2023 from North China Electric Power University, China, where she is currently working toward the M.S. degree. Her current research interests include demand side management, and load forecasting.

Jun Shu (Member, IEEE) received the M.S. and Ph.D. degrees in electrical engineering from North China Electric Power University, Beijing, China, in 1998 and 2006, respectively. In 1998, he joined the North China Electric Power University, where he is an Associate Professor. His research interests include regulated markets and optimization in power system.

Xuan Wei (Member, IEEE) received the B.S. and M.S. degrees in electrical engineering from North China Electric Power University, Beijing, China, in 2017 and 2020, respectively, and the Ph.D. degree in electrical engineering from Tsinghua University, Beijing, China, in 2024. She is currently a Lecturer with North China Electric Power University, Beijing, China. Her research interests include distributed optimization and operation of virtual power plants, and the integration of electric vehicles.

![](ding2026_multi_time_scale_market_assets/images/269fdfdb2ee78241c30df5474824635c4d3afc32a5816bdf4ce491241a02da26.jpg)

![](ding2026_multi_time_scale_market_assets/images/7548c92da3440be9a45f43c899a446e9a38c38a8d281fcb5c3fe2cfcd837115c.jpg)

Yimeng Sun (Graduate Student Member, IEEE) received the B.S. degree in the electrical engineering and automation in 2021 from North China Electric Power University, Beijing, China, where she is currently working toward the Ph.D. degree in electrical engineering with School of Electrical and Electronic Engineering. Her current research interests include power system optimization and demand side management.

Payman Dehghanian (Senior Member, IEEE) received the B.Sc. degree in electrical engineering from the University of Tehran, Tehran, Iran, in 2009, the M.Sc. degree in electrical engineering from the Sharif University of Technology, Tehran, Iran, in 2011, and the Ph.D. degree in electrical engineering from Texas A&M University, College Station, TX, USA, in 2017. He is currently an Assistant Professor with the Department of Electrical and Computer Engineering, George Washington University, Washington, DC, USA. His research interests include power system reliability and resilience assessment, data-informed decision-making for maintenance and asset management in electrical systems, and smart electricity grid applications. He was the recipient of the 2014 and 2015 IEEE Region 5 Outstanding Professional Achievement Award, 2015 IEEE-HKN Outstanding Young Professional Award, 2021 Early Career Award from the Washington Academy of Sciences, and 2022 Early Career Researcher Award from George Washington University.