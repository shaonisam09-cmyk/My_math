# Distributed Real-Time Energy Management in Data Center Microgrids

Liang Yu, Member, IEEE, Tao Jiang, Senior Member, IEEE, and Yulong Zou, Senior Member, IEEE

Abstract—Data center operators are typically faced with three significant problems when running their data centers, i.e., rising electricity bills, growing carbon footprints, and unexpected power outages. To mitigate these issues, running data centers in microgrids is a good choice since microgrids can enhance the energy efficiency, sustainability, and reliability of electrical services. Thus, in this paper, we investigate the problem of energy management for multiple data center microgrids. Specifically, we intend to minimize the long-term operational cost of data center microgrids by taking into account the uncertainties in electricity prices, renewable outputs, and data center workloads. We first formulate a stochastic programming problem with the considerations of many factors, e.g., providing heterogeneous service delay guarantees for batch workloads, interactive workload allocation, batch workload shedding, electricity buying/selling, battery charging/discharging efficiency, and the ramping constraints of backup generators. Then, we design a realtime and distributed algorithm for the formulated problem based on Lyapunov optimization technique and a variant of alternating direction method of multipliers. Moreover, the performance guarantees provided by the proposed algorithm are analyzed. Extensive simulation results indicate the effectiveness of the proposed algorithm in operational cost reduction for data center microgrids.

Index Terms—Data centers, energy management, microgrids, realtime and distributed algorithm.

## I. INTRODUCTION

tions, massive geo-distributed data centers have been

Manuscript received August 5, 2016; revised October 17, 2016 and November 25, 2016; accepted December 10, 2016. Date of publication December 15, 2016; date of current version June 19, 2018. This work was supported in part by the National Natural Science Foundation of China under Grant 61502252, Grant 61471163, Grant 61471177, Grant 61401223, and Grant 61522109, in part by the Natural Science Foundation of Jiangsu Province under Grant BK20150869, Grant BK20140887, Grant BK20150040, and Grant BK20140883, in part by the Key Project of Hubei Province in China under Grant 2015BAA074, in part by the Key Project of Natural Science Research of Higher Education Institutions of Jiangsu Province under Grant 15KJA510003, in part by the General Program for Natural Science Research of Higher Education Institute of Jiangsu Province under Grant 15KJB110017, and in part by the Scientific Research Fund of Nanjing University of Posts and Telecommunications under Grant NY214187 and Grant NY214001. Paper no. TSG-01034-2016. (Corresponding authors: Liang Yu; Tao Jiang.)

L. Yu and Y. Zou are with the Key Laboratory of Broadband Wireless Communication and Sensor Network Technology of Ministry of Education, Nanjing University of Posts and Telecommunications, Nanjing 210003, China (e-mail: liang.yu@njupt.edu.cn).

T. Jiang is with the Wuhan National Laboratory for Optoelectronics, School of Electronics Information and Communications, Huazhong University of Science and Technology, Wuhan 430074, China (e-mail: tao.jiang@ieee.org).

Color versions of one or more of the figures in this paper are available online at http://ieeexplore.ieee.org.

Digital Object Identifier 10.1109/TSG.2016.2640453 deployed. When running these data centers, a data-center operator is typically faced with three significant problems: (1) rising electricity bills, e.g., Google consumed 2260 GWh in 2010 and the corresponding electricity bill was larger than 135 million dollars [1]; (2) growing carbon emission, e.g., data center carbon emissions are expected to reach 2.6% of the total emissions [1]; (3) unexpected power outages, e.g., Amazon experienced several power outages during 2010-2013 and knocked many customers offline [2]. Since microgrids could potentially provide cost savings, emission reduction and reliability enhancement for data centers [3]–[7], it is necessary to study the problem of energy management for data center microgrids.

There has been few work on the energy management in microgrids. Guan et al. [10] investigated the scheduling problem of building energy supplies in a microgrid. Erol-Kantarci et al. [11] developed the idea of resource sharing among microgrids for the sake of increased reliability. Huang et al. [12] presented a novel energy management framework to minimize the operational cost of a microgrid by introducing a model of QoSE (quality-of-service in electricity). Zhang et al. [13] considered an optimal energy management problem for both supply and demand of a grid-connected microgrid incorporating renewable energy sources. Rahbar et al. [14] proposed an online algorithm to minimize the total energy cost of the conventional energy drawn from the main grid over a finite horizon by scheduling energy storage devices in a microgrid. Ma et al. [15] designed a distributed algorithm for online energy management in networked microgrids with a high penetration of distributed energy resources using online ADMM with Regret. Guo and Zhao [16] proposed a two-stage adaptive robust optimization approach for the energy management of a microgrid. Salomonsson et al. [3] designed an adaptive control system for a dc microgrid with data center loads. Shi et al. [17] proposed an online energy management strategy for realtime operation of a microgrid with the considerations of the power flow and system operational constraints on a distribution network. Li and Qi [6] studied the problem of minimizing the operation cost of a data center microgrid. Chen et al. [18] proposed a cooling-aware realtime algorithm to minimize the long-term operational cost of a data center microgrid. Thompson et al. [7] presented a methodology for optimizing investment in data center battery storage capacity in a microgrid. Though some positive results have been obtained in the above works, there is no work that focuses on the realtime and distributed energy management for multiple data center microgrids. In our previous works [4], [5], [19], we mainly focus on the realtime energy management for multiple data center microgrids from different perspectives, e.g., energy cost reduction and carbon emission reduction. However, such previous works neglect heterogeneous service delay guarantees for batch workloads in all data centers [20] and distributed implementation for the proposed realtime algorithm.

Based on the above observation, this paper investigates the problem of realtime distributed energy management for multiple data center microgrids considering the drawbacks in our previous works. The resulting challenge consists of two aspects, i.e., spatial and temporal couplings [21]. On one hand, there are some spatial couplings among all microgrids due to the allocation of interactive workloads. On the other hand, to provide the heterogeneous service delay guarantees for batch workloads in all data centers and keep all energy storage systems stable, several temporal couplings are incurred.

To deal with the above challenge, we first formulate a stochastic programming problem to minimize the time average expected operational cost by jointly capturing the constraints with geographical load balancing, batch workload allocation/shedding, heterogeneous service delay guarantees for batch workloads, electricity buying/selling, battery charging/discharging management, backup generators, and power balancing. Since the formulated optimization problem is a large-scale nonlinear stochastic programming with “timecoupling” constraints, we propose a realtime and distributed algorithm based on Lyapunov optimization technique [8] and a variant of alternating direction method of multipliers (ADMM) [31].<sup>1</sup> The key idea of the proposed algorithm is given as follows. Firstly, we propose a realtime algorithm for the formulated problem based on Lyapunov optimization technique so that “time-coupling” constraints could be avoided. Then, we present the distributed implementation of the proposed realtime algorithm without considering the nonlinear constraints based on a variant of ADMM. Next, a feasible solution to the original problem could be obtained by adjustment so that the nonlinear constraints in the formulated problem could be satisfied. Furthermore, the performance analysis of the proposed algorithm is carried out.

The main contributions of this paper are summarized below:

• We formulate a stochastic programming to minimize the long-term operational cost of multiple data center microgrids with the considerations of many factors, e.g., providing heterogeneous service delay guarantees for batch workloads, interactive workload allocation, batch workload shedding, electricity buying/selling, battery charging/discharging efficiency, and the ramping constraints of backup generators.

• We propose a realtime and distributed algorithm to solve the formulated problem based on Lyapunov optimization technique and a variant of ADMM. Moreover, we analyze the performance guarantees provided by the proposed algorithm. Note that the proposed algorithm does not require any prior knowledge of statistical characteristics associated with system parameters and has low computational complexity.

• We conduct extensive simulations to evaluate the performance of the proposed algorithm. Simulation results show that the proposed algorithm outperforms other benchmark schemes in operational cost reduction.

The rest of this paper is organized as follows. In Section II, we describe the system model and problem formulation. Section III proposes a realtime and distributed algorithm to solve the formulated problem. Section IV gives the algorithmic performance analysis. Extensive simulations are conducted in Section V. Finally, conclusions are drawn in Section VI.

## II. MODEL AND FORMULATION

We consider a data center operator that has some geodistributed data centers located in different electric regions as shown in Fig. 1, where each data center operates in a smart microgrid (SMG) environment [11]. As far as the operation condition of a SMG is concerned, there are two modes, i.e., the islanded mode and the grid-connected mode. In the islanded mode, SMGs could supply their loads using multiple energy resources, e.g., energy storage devices, renewable and backup generators. In contrast, a SMG could sell (buy) energy to (from) a main grid in the grid-connected mode. A SMG considered in this paper consists of four main components, i.e., a generation system, a load, an energy storage system (ESS), and an energy management system (EMS). Specifically, a generation system consists of several renewable generators and a conventional generator (usually adopted as the backup generator), while the EMS is responsible for the energy scheduling of other components in the SMG. As the aggregated load in the SMG, a data center needs to finish the interactive workloads dispatched from front-end servers and the batch workloads within the data center. In this paper, we consider a timeslotted system and the length of each slot is assumed to be unit time. For easy reading, the main notations are introduced in Table I.

## A. Models Associated With Data Centers and Front-End Servers

Suppose that there are N data centers geographically distributed in N SMGs, which connected to N main grids. Therefore, a common index $i ( 1 \leq i \leq N )$ is adopted for data centers, SMGs and main grids. Moreover, we assume that data center i consists of $C _ { i }$ homogeneous servers.<sup>2</sup> In time slot t, the total quantity of interactive workloads (in the number of servers required) at the front-end server f $( 1 \leq f \leq F )$ is $\lambda _ { f , t } .$

![](yu2018_distributed_realtime_dcmg_assets/images/4a2ee5053703d1da1221e3f406adb3c355771230ff1439e5ae6957114c9619e6.jpg)  
Fig. 1. System model.

TABLE I NOTATIONS

<table><tr><td>Notation</td><td>Definition</td></tr><tr><td>t</td><td>Time slot index (1 ≤ t ≤ T)</td></tr><tr><td>f</td><td>front-end server index (1 ≤ f ≤ F)</td></tr><tr><td>i</td><td>A common index for data centers, SMGs and main grids</td></tr><tr><td>f</td><td>Front-end server f</td></tr><tr><td>λf,t</td><td>The number of interactive workloads at front-end server f at t</td></tr><tr><td>df,i,t</td><td>Interactive workload allocation from front-end server f to DC i at t</td></tr><tr><td>πi,q,t</td><td>The quantity of batch workloads with type q at t (1 ≤ q ≤ Mi)</td></tr><tr><td>Qi,q,t</td><td>Batch workload queue</td></tr><tr><td>xi,q,t</td><td>The served workloads in batch workload queue at t</td></tr><tr><td>ei,q,t</td><td>The quantity of dropped batch workloads at t</td></tr><tr><td>Rmaxi,q</td><td>The maximum queueing delay associated with πi,q,t</td></tr><tr><td>Ti,q</td><td>The tolerant service delay associated with πi,q,t</td></tr><tr><td>Pi,idle</td><td>Idle power of a server in data center i</td></tr><tr><td>Pi,peak</td><td>Peak power of a server in data center i</td></tr><tr><td>pi,t</td><td>Total power consumption in data center i at t</td></tr><tr><td>ri,t</td><td>The total power output of the renewable generators in SMG i at t</td></tr><tr><td>ci,t</td><td>The power output of the conventional generator in SMG i at t</td></tr><tr><td>εi</td><td>Ramping coefficient of the conventional generator in SMG i</td></tr><tr><td>uc,i,t</td><td>The charging power for the ESS in SMG i at t</td></tr><tr><td>ud,i,t</td><td>The discharging power for the ESS in SMG i at t</td></tr><tr><td>Di,t</td><td>The stored energy level of the ESS i at t</td></tr><tr><td>Si(t)</td><td>Purchasing electricity price from main grid i at t</td></tr><tr><td>Wi(t)</td><td>Selling electricity price to main grid i at t</td></tr><tr><td>gi,t</td><td>Energy transactions between SMG i and main grid i at t</td></tr><tr><td>Γ1,t</td><td>The cost incurred by electricity buying and selling at t</td></tr><tr><td>Γ2,t</td><td>Total revenue loss of serving interactive requests at t</td></tr><tr><td>Γ3,t</td><td>The penalty cost imposed on dropping batch workloads at t</td></tr><tr><td>Γ4,t</td><td>Battery depreciation cost at t</td></tr><tr><td>Γ5,t</td><td>Generation cost of the conventional generators at t</td></tr><tr><td>Hi,q,t</td><td>Delay-aware virtual queue</td></tr><tr><td>Zi,t</td><td>Virtual energy queue</td></tr><tr><td>Δt</td><td>one-slot conditional Lyapunov drift</td></tr><tr><td>ΔVt</td><td>drift-plus-penalty term</td></tr></table>

Let $d _ { f , i , t }$ be the quantity of interactive workloads allocated from front-end server $f$ to data center i at slot t. Then, we have [23], [24]

$$
\sum_ {i = 1} ^ {N} d _ {f, i, t} = \lambda_ {f, t}, \quad \forall f, t,\tag{1}
$$

$$
d _ {f, i, t} \geq 0, \quad \forall f, i, t.\tag{2}
$$

Besides interactive workloads, some resource elastic batch workloads are commonly processed within data centers, $\mathrm { e . g . }$ scientific applications, data mining jobs. Batch workloads could be scheduled at any time slot as long as they are processed before their deadlines. Thus, batch workloads could be buffered and served in proper time slot. Let $\pi _ { i , q , t }$ be the quantity of batch workloads at slot t (also in terms of the number of servers required) with type $q ~ ( 1 \leq q \leq M _ { i } )$ in data center i. By storing batch workloads $\pi _ { i , q , t }$ in a queue $\boldsymbol { Q } _ { i , q , t }$ according to its type q, we have

$$
Q _ {i, q, t + 1} = \max \left[ Q _ {i, q, t} - x _ {i, q, t}, 0 \right] + \pi_ {i, q, t}, \quad \forall i, q, t\tag{3}
$$

where $x _ { i , q , t }$ denotes the served workloads in the queue $q$ of data center i at slot t. Denote the maximum value of $x _ { i , q , t }$ by $x _ { i , q } ^ { \operatorname* { m a x } }$ , where $\begin{array} { r } { x _ { i , q } ^ { \operatorname* { m a x } } \geq \pi _ { i , q } ^ { \operatorname* { m a x } } ( \pi _ { i , q } ^ { \operatorname* { m a x } } = \operatorname* { m a x } _ { t } \pi _ { i , q , t } ) } \end{array}$ so that it is always possible to make the queue $\boldsymbol { Q } _ { i , q , t }$ stable (and this can be done with one slot delay if we choose $x _ { i , q , t } = x _ { i , q } ^ { \operatorname* { m a x } }$ for all t). In addition, by observing the structure of $\boldsymbol { Q } _ { i , q , t }$ , it can be found that there is no need to serve the batch workload that is larger than $\boldsymbol { Q } _ { i , q , t }$ . Thus, we have

$$
0 \leq x _ {i, q, t} \leq \min \left\{x _ {i, q} ^ {\max}, Q _ {i, q, t} \right\}, \quad \forall i, q, t.\tag{4}
$$

To keep workload queues $\boldsymbol { Q } _ { i , q , t }$ stable, the batch workloads should be served without waiting for a long time. Since the summation of served batch workloads $\textstyle \sum _ { q = 1 } ^ { M _ { i } } x _ { i , q , t }$ and arrived interactive workloads $\textstyle \sum _ { f = 1 } ^ { F } d _ { f , i , t }$ may exceed the processing capacity of data center $i ,$ some batch workloads have to be dropped at this time. Let $e _ { i , q , t }$ be the quantity of dropped batch workloads, we have

$$
\sum_ {f = 1} ^ {F} d _ {f, i, t} + \sum_ {q = 1} ^ {M _ {i}} \left(x _ {i, q, t} - e _ {i, q, t}\right) \leq C _ {i}, \quad \forall i, t\tag{5}
$$

$$
0 \leq e _ {i, q, t} \leq x _ {i, q, t}, \quad \forall i, q, t.\tag{6}
$$

For any control algorithm, it is necessary to ensure that the average length of the workload queue q in data center i is finite so that batch workloads could be finished without waiting an arbitrarily long time, i.e.,

$$
\overline {{Q}} _ {i, q} = \operatorname * {l i m s u p} _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \bigl \{Q _ {i, q, t} \bigr \} <   \infty .\tag{7}
$$

Note that (7) is not enough to ensure the heterogeneous service delay for batch workload $\pi _ { i , q , t } .$ , we adopt the following constraint,

$$
R _ {i, q} ^ {\max} \leq \mathcal {T} _ {i, q}, \quad \forall i, q, t\tag{8}
$$

where $R _ { i , q } ^ { \operatorname* { m a x } }$ and $\mathcal { T } _ { i , q }$ are the maximum queueing delay and the tolerant service delay associated with the batch workload added into the queue $\boldsymbol { Q } _ { i , q , t }$ of data center i at slot t, respectively. In Section V, we will provide the specific expression of $R _ { i , q } ^ { \operatorname* { m a x } }$

Let $\mathrm { P U E } _ { i }$ be the $\mathrm { P U E } ^ { 3 }$ of data center i, $P _ { i , \mathrm { i d l e } }$ and $P _ { i , \mathrm { p e a k } }$ represent the idle power and peak power of a server in data center i, respectively. Then, the total power consumption in data center i at slot $t \ p _ { i , t }$ could be estimated by [25]

$$
p _ {i, t} = \alpha_ {i} + \beta_ {i} \left(\sum_ {f = 1} ^ {F} d _ {f, i, t} + \sum_ {q = 1} ^ {M _ {i}} \left(x _ {i, q, t} - e _ {i, q, t}\right)\right), \quad \forall i, t,\tag{9}
$$

where $\alpha _ { i } \triangleq C _ { i } ( P _ { i , \mathrm { i d l e } } + ( \mathrm { P U E } _ { i } - 1 ) P _ { i , \mathrm { p e a k } } ) , \beta _ { i } \triangleq P _ { i , \mathrm { p e a k } } - P _ { i , \mathrm { i d l e } } .$

## B. Models Related to the Generation System and ESS

1) Generation Model: Let $r _ { i , t }$ and $c _ { i , t }$ be the total power output of the renewable generators and the power output of the conventional generator in SMG i at slot t, respectively. Then, we have

$$
0 \leq c _ {i, t} \leq c _ {i, \max}, \quad \forall i, t,\tag{10}
$$

where $c _ { i , \mathrm { m a x } }$ is the maximum power output associated with the conventional generator in SMG i. Considering the physical constraints of the conventional generator, the output change in two consecutive slots is limited instead of arbitrarily large, which is reflected by a so-called ramping constraint. Without loss of generality, the ramp-up and ramp-down constraints are regarded as the same [22]. Then, we have

$$
\left| c _ {i, t} - c _ {i, t - 1} \right| \leq \epsilon_ {i} c _ {i, \mathrm{max}}, \quad \forall i, t,\tag{11}
$$

where $\epsilon _ { i }$ is the ramping coefficient associated with the conventional generator in SMG i.

2) ESS Model: We define $u _ { c , i , t }$ and $u _ { d , i , t }$ to represent the charging and discharging power for the ESS in SMG i at slot t. Then, we have

$$
0 \leq u _ {c, i, t} \leq u _ {i, \mathrm{cmax}}, \quad \forall i, t,\tag{12}
$$

$$
0 \leq u _ {d, i, t} \leq u _ {i, \mathrm{dmax}}, \quad \forall i, t,\tag{13}
$$

where $u _ { i , \mathrm { c m a x } }$ and $u _ { i , \mathrm { d m a x } }$ are maximum charging power and discharging power, respectively. Denote $\eta _ { c , i }$ and $\eta _ { d , i }$ be the charging and discharging efficiency of the ESS in SMG i at slot t, respectively. In addition, simultaneous charging and discharging are not allowed considering the round-trip inefficiency, i.e.,

$$
u _ {c, i, t} \cdot u _ {d, i, t} = 0, \forall i, t.\tag{14}
$$

Let $D _ { i , t }$ be the stored energy of the ESS i, we have

$$
D _ {i, \min} \leq D _ {i, t} \leq D _ {i, \max}, \quad \forall i, t,\tag{15}
$$

where $D _ { i , \operatorname* { m a x } }$ and $D _ { i , \operatorname* { m i n } }$ denote the maximum and the minimum capacity of the ESS i, respectively. In addition, the storage dynamics of the ESS i could be modeled by

$$
D _ {i, t + 1} = D _ {i, t} + \eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t}, \quad \forall i, t.\tag{16}
$$

To satisfy the energy demand of data centers, SMGs may exchange energy with main grids. Denote the electricity price of buying and selling energy by $X _ { i , t } ~ \in ~ [ X _ { i , \operatorname* { m i n } } , ~ X _ { i , \operatorname* { m a x } } ]$ and $W _ { i , t } \in [ W _ { i , \operatorname* { m i n } } , ~ W _ { i , \operatorname* { m a x } } ] .$ , respectively. As in [13], the selling price is assumed to be strictly smaller than the purchasing price so that energy arbitrage could be avoided, i.e., $X _ { i , t } > W _ { i , t } .$ . To achieve the real-time power balancing, we have the following constraints, i.e.,

$$
g _ {i, t} + r _ {i, t} + c _ {i, t} + u _ {d, i, t} = p _ {i, t} + u _ {c, i, t}, \quad \forall i, t,\tag{17}
$$

where $g _ { i , t }$ denotes the energy transactions between SMG i and main grid i at slot t, which is bounded by

$$
G _ {i, \text { smax }} \leq g _ {i, t} \leq G _ {i, \text { bmax }}, \quad \forall i, t,\tag{18}
$$

where $G _ { i , \mathrm { b m a x } } \ > \ 0$ and $G _ { i , \mathrm { s m a x } } \ < \ 0$ are determined by the physical limitations, $\mathrm { e . g . }$ , transmission lines [12]. As in [4], $G _ { i , \mathrm { b m a x } }$ and $G _ { i , \mathrm { s m a x } }$ are assumed to be large enough to support the normal operation of SMG i in the grid-connected mode.

## C. Operational Cost Model

Denote the total operational cost of the data center operator at slot t by $\Gamma _ { t }$ , which includes several components, i.e., the cost of purchasing and selling electricity $\Gamma _ { 1 , t } ,$ revenue loss associated with workload allocation $\Gamma _ { 2 , i }$ <sub>t</sub> and $\Gamma _ { 3 , t }$ , the battery depreciation cost $\Gamma _ { 4 , t } ,$ and the total generation cost of conventional generators $\Gamma _ { 5 , t }$ . Specifically, the cost incurred by electricity buying and selling at slot $t \ \Gamma _ { 1 , t }$ is obtained below,

$$
\Gamma_ {1, t} = \sum_ {i = 1} ^ {N} \left(\frac {X _ {i , t} - W _ {i , t}}{2} | g _ {i, t} | + \frac {X _ {i , t} + W _ {i , t}}{2} g _ {i, t}\right).\tag{19}
$$

For interactive applications, latency is the most important performance metric and a moderate increase in user-perceived latency would translate into substantial revenue loss for the data center operator [26], [27]. To model the utility of the interactive workload, the convex function in [26] is adopted, which converts the mean propagation delay into revenue loss, i.e. $\begin{array} { r } { , \omega \sum _ { i = 1 } ^ { N } d _ { f , i , t } L _ { f , i } / \lambda _ { f , t } . } \end{array}$ , where ω is a conversion factor; $L _ { f , i }$ is the propagation latency between the front-end server $f$ and data center i. Then, the total revenue loss of serving interactive requests is described by $\begin{array} { r } { \Gamma _ { 2 , t } = \omega \sum _ { f = 1 } ^ { F } \sum _ { i = 1 } ^ { N } \stackrel { \textstyle } { d _ { f , i , t } } \bar { L } _ { f , i } } \end{array}$

In addition, to model the revenue loss of allocating processing servers for batch workload, the following function is adopted as in [27], $\begin{array} { r } { \Gamma _ { 3 , t } = \sum _ { i = 1 } ^ { N } \sum _ { q = 1 } ^ { M _ { i } } \theta _ { i } e _ { i , q , t } } \end{array}$ , where $\theta _ { i }$ is the penalty factor imposed on dropping batch workloads.

It is known that charging and discharging of batteries would affect their lifetime. To model such depreciation cost, the penalty function $B _ { i } ( u _ { c , i , t } , u _ { d , i , t } )$ is adopted. Continually, we have $\begin{array} { r } { \Gamma _ { 4 , t } = \sum _ { i = 1 } ^ { N } B _ { i } ( u _ { c , i , t } , u _ { d , i , t } ) } \end{array}$

Denote the generation cost function of the conventional generator at slot t by $A _ { i } ( c _ { i , t } )$ . Then, $\begin{array} { r } { \Gamma _ { 5 , t } = \sum _ { i = 1 } ^ { N } A _ { i } ( c _ { i , t } ) } \end{array}$

With the above-mentioned cost components, the total operational cost of the data center operator is calculated by $\begin{array} { r } { \Gamma _ { t } = \sum _ { l = 1 } ^ { 5 } \Gamma _ { l , t } } \end{array}$

## D. Operational Cost Minimization Problem

With the aforementioned models, we can formulate a stochastic programming problem to minimize the time average expected operational cost of data center microgrids as follows,

![](yu2018_distributed_realtime_dcmg_assets/images/ba50ce3f5cb4e5d0e504d90a4ac5fdaee88ece541f11757288620b74512c7112.jpg)  
Fig. 2. An illustration of the key idea of the proposed algorithm.

$$
\text {(P1)} \min \quad \operatorname * {l i m s u p} _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \{\Gamma_ {t} \},\tag{20a}
$$

$$
s. t. (1) - (1 8),\tag{20b}
$$

where <sup>E</sup>{·} is the expectation operator; the decision variables are $d _ { f , i , t } , \ x _ { i , q , t } , \ e _ { i , q , t } , \ c _ { i , t } , \ g _ { i , t } , \ u _ { c , i , t }$ and $u _ { d , i , t } ;$ the expectation in the objective function is taken over the randomness of the system parameters $\lambda _ { f , t } , \pi _ { i , q , t } , r _ { i , t } , X _ { i , t }$ and $W _ { i , t } ,$ and the possibly random control actions at each time slot.

For simplicity, the cost functions $A _ { i } ( \cdot )$ and $B _ { i } ( \cdot )$ are assumed to be continuously differentiable and convex, which is reasonable since many practical costs could be well approximated by such functions [14], [28]. Let $A _ { i } ^ { \prime } ( \cdot )$ and $B _ { i } ^ { \prime } ( \cdot )$ be the derivatives of $A _ { i } ( \cdot )$ and $B _ { i } ( \cdot )$ , respectively. In addition, we suppose that $A _ { i } ^ { \prime } ( c _ { i , t } )$ and $B _ { i } ^ { \prime } ( u _ { c , i , t } , u _ { d , i , t } )$ are bounded within the intervals $[ A _ { i , \operatorname* { m i n } } ^ { \prime } , ~ A _ { i , \operatorname* { m a x } } ^ { \prime } ]$ and $[ B _ { i , \operatorname* { m i n } } ^ { \prime } , ~ B _ { i , \operatorname* { m a x } } ^ { \prime } ]$ respectively.

## III. ALGORITHM DESIGN

There are three challenges to solve P1. Firstly, P1 is a large-scale nonlinear optimization problem as the data center operator may deploy tens of geo-distributed data centers and hundreds of thousands of front-end servers around the world. Secondly, the future parameters are not known, including workload, renewable generation output and electricity price. Thirdly, the constraints (11) and (16) bring the “time coupling” property to P1, which means that the current decision can impact the future decision. Previous methods to handle the “time coupling” problem are usually based on dynamic programming, which suffers from the curse of dimensionality problem. The structure and size of P1 motivates us to design a scalable distributed realtime algorithm that is applicable for practical applications.

The key idea of the proposed algorithm can be illustrated by Fig. 2. Specifically, we can first transform the original problem P1 into a stochastic programming problem P2 with time average constraints by removing the constraint (11). Then, we can transform P2 into one-slot minimization problem P3 using Lyapunov optimization technique. Next, by incorporating the constraint (11) into P3, we obtain P4. Since there are nonlinear constraints (14) in P4, we transform P4 into P5 by removing (14). After obtaining the solution of P5, we adjust the solution so that (14) could be satisfied. Finally, we provide the distributed implementation of the proposed online algorithm and prove that all constraints of P1 could be satisfied by the proposed algorithm.

Since Lyapunov optimization technique (LOT) could be used to solve a stochastic programming problem with time average constraints, we need to transform (15) and (16) into the time average constraints. To be specific, we define $\overline { { u _ { c , i } } }$ and $\overline { { u _ { d , i } } }$ as follows,

$$
\overline {{u _ {c , i}}} = \operatorname * {l i m s u p} _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \big \{u _ {c, i, t} \big \},\tag{21}
$$

$$
\overline {{u _ {d , i}}} = \limsup _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \bigl \{u _ {d, i, t} \bigr \}.\tag{22}
$$

It is not difficult to obtain that $\begin{array} { c c l } { { \eta _ { c , i } { \overline { { { u _ { c , i } } } } } } } & { { = } } & { { { \frac { 1 } { \eta _ { d , i } } } { \overline { { { u _ { d , i } } } } } . } } \end{array}$ Continually, P1 could be relaxed into P2 below,

$$
\begin{array}{l} \text {(P2) min} \quad \operatorname * {l i m s u p} _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \{\Gamma_ {t} \}, \\ \text {   s.t.   } (1), (2), (4) - (1 0), (1 2) - (1 4), (1 7), (1 8), \end{array} \tag {23a}
$$

$$
\eta_ {c, i} \overline {{u _ {c , i}}} = \frac {1}{\eta_ {d , i}} \overline {{u _ {d , i}}}, \quad \forall i.\tag{23b}
$$

(23c)

To solve P2, LOT intends to transform time average constraints into queue stability problems. Thus, a virtual energy queue $Z _ { i , t }$ is adopted to ensure the feasibility of $\eta _ { c , i } \overline { { { u _ { c , i } } } } =$ $\frac { 1 } { \eta _ { d , i } } \overline { { u _ { d , i } } } .$ , i.e.,

$$
Z _ {i, t} = D _ {i, t} - D _ {i, \mathrm{min}} - V \eta_ {d, i} \gamma_ {i, \mathrm{max}} - \frac {1}{\eta_ {d , i}} u _ {i, \mathrm{dmax}},\tag{24}
$$

where $\gamma _ { i , \operatorname* { m a x } } = \operatorname* { m a x } \{ X _ { i , \operatorname* { m a x } } , W _ { i , \operatorname* { m a x } } , A _ { i , \operatorname* { m a x } } ^ { \prime } \} ; V \in [ 0 , V _ { \operatorname* { m a x } } ]$ is a control parameter that would be specified later. Continually, the update equation of $Z _ { i , t }$ is obtained as follows,

$$
Z _ {i, t + 1} = Z _ {i, t} + \eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t}, \quad \forall i, t.\tag{25}
$$

Similarly, to ensure the feasibility of $( 7 ) .$ , we need to keep the workload queue $\boldsymbol { Q } _ { i , q , t }$ stable. In addition, to ensure the feasibility of (8), we adopt a delay-aware virtual queue $H _ { i , q , t } .$ Specifically, for each i and q, $H _ { i , q , i }$ <sub>t</sub> with $H _ { i , q , 0 } = 0$ and with dynamics as follows,

$$
H _ {i, q, t + 1} = \left\{ \begin{array}{l l} \left[ H _ {i, q, t} - x _ {i, q, t} + \varepsilon_ {i, q} \right] ^ {+}, & Q _ {i, q, t} > x _ {i, q, t}, \\ 0, & Q _ {i, q, t} \leq x _ {i, q, t}, \end{array} \right.\tag{26}
$$

where $[ \diamond ] ^ { + } \triangleq \operatorname* { m a x } \{ \diamond , 0 \} ; \ \varepsilon _ { i , q }$ is a fixed parameter, which would be specified later. It can be observed that $H _ { i , q , t + 1 }$ has the same service rate as $Q _ { i , q , t + 1 }$ but has a new arrival rate $\varepsilon _ { i , q }$ when $Q _ { i , q , t } > x _ { i , q , t }$ , which can ensure that $H _ { i , q , t + 1 }$ grows when the batch workload added into the queue $\boldsymbol { Q } _ { i , q , t }$ at slot t is still waiting to be satisfied. If we can ensure that the queues $H _ { i , q , t }$ and $\boldsymbol { Q } _ { i , q , t }$ have finite upper bounds, then the maximum queueing delay in queue $\boldsymbol { Q } _ { i , q , t }$ defined in the following lemma could be guaranteed.

Lemma 1 (Maximum Queueing Delay): Suppose we can control the system so that $H _ { i , q , t } \leq H _ { i , q } ^ { \operatorname* { m a x } }$ and $Q _ { i , q , t } \leq Q _ { i , q } ^ { \operatorname* { m a x } }$ for all $i , q$ and t. Then, all energy demands in the queue $\bar { Q } _ { i , q , t }$ would be served with a maximum queueing delay $R _ { i , q } ^ { \operatorname* { m a x } }$ slots, where

$$
R _ {i, q} ^ {\max} \triangleq \left\lceil \left(H _ {i, q} ^ {\max} + Q _ {i, q} ^ {\max}\right) / \varepsilon_ {i, q} \right\rceil .\tag{27}
$$

Proof: See Appendix A. In addition, in Section V, it can be proved that the constants $H _ { i , q } ^ { \operatorname* { m a x } }$ and $Q _ { i , q } ^ { \operatorname* { m a x } }$ indeed exist. ■

According to the framework of LOT, solving P2 is equivalent to solving $\mathbf { P } 2 ^ { \bullet }$ as follows,

$$
\begin{array}{l} \left(\mathbf {P 2} ^ {\prime}\right) \min \quad \operatorname * {l i m s u p} _ {T \to \infty} \frac {1}{T} \sum_ {t = 0} ^ {T - 1} \mathbb {E} \{\Gamma_ {t} \}, \\ \text {   s.t.   } (1), (2), (4) - (6), (9), (1 0), (1 2) \\ \quad - (1 4), (1 7), (1 8), \text {   Queues   } Q _ {i, q, t}, H _ {i, q, t}, \\ \quad \text {   and   } Z _ {i, q, t} \text {   are   mean   rate   stable.   } \end{array}\tag{28a}
$$

## A. The Proposed Realtime Algorithm

Define $\Theta _ { t } \triangleq ( Q _ { t } , H _ { t } , Z _ { t } )$ as the concatenated vector of the real workload queue, virtual workload queue and virtual energy queue, where

$$
\begin{array}{l} \boldsymbol {Q} _ {t} = \big (Q _ {1, 1, t}, \ldots , Q _ {1, M _ {1}, t}, \ldots , Q _ {N, 1, t}, \ldots , Q _ {N, M _ {N}, t} \big), \\ \boldsymbol {H} _ {t} = \big (H _ {1, 1, t}, \ldots , H _ {1, M _ {1}, t}, \ldots , Z _ {N, 1, t}, \ldots , Z _ {N, M _ {N}, t} \big), \\ \boldsymbol {Z} _ {t} = \big (Z _ {1, t}, Z _ {2, t}, \ldots , Z _ {N, t} \big). \end{array}
$$

To keep the stability of all queues, we first define a weighted quadratic Lyapunov function as follows,

$$
\mathcal {L} _ {t} \triangleq \frac {1}{2} \sum_ {i = 1} ^ {N} \left(\sum_ {q = 1} ^ {M _ {i}} w \left(Q _ {i, q, t} ^ {2} + H _ {i, q, t} ^ {2}\right) + Z _ {i, t} ^ {2}\right),\tag{29}
$$

where w is a positive weight for workload queues, which indicates the relative importance of the workload queues with respect to the energy queues.

Then, a one-slot conditional Lyapunov drift could be obtained below,

$$
\Delta_ {t} = \mathbb {E} \{\mathcal {L} _ {t + 1} - \mathcal {L} _ {t} | \boldsymbol {\Theta} _ {t} \},\tag{30}
$$

where the expectation is taken with respect to the randomness of workloads, renewable generation outputs, electricity prices, and the randomness in control policies.

Next, by adding a function of the expected operational cost in a slot to (30), we can obtain a drift-plus-penalty term as follows,

$$
\Delta V _ {t} = \Delta_ {t} + V \mathbb {E} \{\Gamma_ {t} | \boldsymbol {\Theta} _ {t} \}.\tag{31}
$$

Lemma 2 (Drift Bound): The drift-plus-penalty term satisfies the following inequality for all slots,

$$
\begin{array}{l} \Delta V _ {t} \leq \Omega_ {0} + V \mathbb {E} \{\Gamma_ {t} | \boldsymbol {\Theta} _ {t} \} \\ \quad + \mathbb {E} \left\{\sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w Q _ {i, q, t} \big (\pi_ {i, q, t} - x _ {i, q, t} \big) \Bigg | \boldsymbol {\Theta} _ {t} \right\} \\ \quad + \mathbb {E} \left\{\sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w H _ {i, q, t} \big (\varepsilon_ {i, q} - x _ {i, q, t} \big) \Bigg | \boldsymbol {\Theta} _ {t} \right\} \\ \quad + \mathbb {E} \left\{\sum_ {i = 1} ^ {N} Z _ {i, t} \left(\eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t}\right) \Bigg | \boldsymbol {\Theta} _ {t} \right\}. \end{array}\tag{32}
$$

where $\Omega _ { 0 }$ is given by

$$
\begin{array}{l} \Omega_ {0} = \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} \left(w \frac {\left(\pi_ {i , q} ^ {\max}\right) ^ {2} + \left(x _ {i , q} ^ {\max}\right) ^ {2} + \max \left\{\varepsilon_ {i , q} ^ {2} , \left(x _ {i , q} ^ {\max}\right) ^ {2} \right\}}{2}\right) \\ + \sum_ {i = 1} ^ {N} \frac {\max \left\{\left(\eta_ {c , i} u _ {i , \mathrm{cmax}}\right) ^ {2} , \left(\frac {1}{\eta_ {d , i}} u _ {i , \mathrm{dmax}}\right) ^ {2} \right\}}{2}. \end{array} \tag {33}
$$

Proof: See Appendix B.

Minimizing the R.H.S. of the upper bound of drift-pluspenalty term in each slot t, we have the following optimization problem P3 as follows,

$$
\begin{array}{l} \text {(P3)} \min V \Gamma_ {t} - \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w (Q _ {i, q, t} + H _ {i, q, t}) x _ {i, q, t} \\ \quad + \sum_ {i = 1} ^ {N} Z _ {i, t} \left(\eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t}\right) \\ \text {s.t. (1), (2), (4) - (6), (9), (10), (12) - (14), (17), (18).} \end{array} \tag {34a}
$$

Since P3 neglects the constraint (11), we can obtain P4 by adding (11) into the constraints of P3, i.e.,

(P4)

$$
\begin{array}{l} \min V \Gamma_ {t} - \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w (Q _ {i, q, t} + H _ {i, q, t}) x _ {i, q, t} \\ \quad + \sum_ {i = 1} ^ {N} Z _ {i, t} \left(\eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t}\right) \\ s. t. (1), (2), (4) - (6), (9) - (1 4), (1 7) - (1 8) \end{array}\tag{35a}
$$

Since the constraint (14) is nonlinear, P4 is a nonlinear programming problem. To simplify the computation, we can first ignore the nonlinear constraint (14), and then adjust the obtained solution to satisfy (14). Based on the above description, an algorithm for P1 could be described by Algorithm 1, where P5 is defined as follows,

(P5) min

$$
\begin{array}{l} V \Gamma_ {t} - \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w (Q _ {i, q, t} + H _ {i, q, t}) x _ {i, q, t} \\ \quad + \sum_ {i = 1} ^ {N} Z _ {i, t} \left(\eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t}\right) \end{array}\tag{36a}
$$

$$
s. t. (1), (2), (4) - (6), (9) - (1 3), (1 7) - (1 8).\tag{36b}
$$

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1 Realtime Algorithm for Operational Cost Minimization Problem
1: For each slot t do
2: Observing system states at the starting point of time slot t: $Q_{i,q,t}$, $H_{i,q,t}$, $Z_{i,t}$, $\lambda_{f,t}$, $\pi_{i,q,t}$, $r_{i,t}$, $X_{i,t}$ and $W_{i,t}$;
3: Choose control decisions $d_{f,i,t}$, $x_{i,q,t}$, $c_{i,t}$, $u_{c,i,t}$, $u_{d,i,t}$, $e_{i,q,t}$, $g_{i,t}$, as the solution to P5;
4: Generate a new solution based on the following equations so that the constraint (14) could be satisfied: $\hat{u}_{c,i,t} = \max\{u_{c,i,t} - \frac{1}{\eta_{c,i}\eta_{d,i}} u_{d,i,t}, 0\}$, $\hat{u}_{d,i,t} = \max\{u_{d,i,t} - \eta_{c,i}\eta_{d,i} u_{c,i,t}, 0\}$, $\hat{d}_{f,i,t} = d_{f,i,t}$, $\hat{g}_{i,t} = g_{i,t}$, $\hat{e}_{i,q,t} = e_{i,q,t}$, $\hat{x}_{i,q,t} = x_{i,q,t}$, $\hat{c}_{i,t} = c_{i,t} + (\hat{u}_{c,i,t} - u_{c,i,t}) + (u_{d,i,t} - \hat{u}_{d,i,t})$.
5: Updating $Q_{i,q,t}$, $H_{i,q,t}$, $Z_{i,t}$ with the new solution according to (3), (25), and (24).
6: End
</div>

Remarks: Note that the constraints (7), (8), and (15) in P1 are not considered in Algorithm 1, the solution generated by Algorithm 1 may be infeasible to P1. In Section ${ \mathrm { V } } ,$ we will show that Algorithm 1 can guarantee the feasibilities of (7), (8), and (15).

## B. Distributed Implementation

To solve P5 efficiently, we propose a distributed implementation for the proposed realtime algorithm. A possible way of obtaining a distributed algorithm for P5 is based on dual decomposition, which decomposes the Lagrangian dual problem of P5 into independent subproblems that could be solved in parallel. Unfortunately, the objective function in P5 is not strictly convex since $\Gamma _ { 2 , t }$ and $\Gamma _ { 3 , t }$ are linear functions. As a result, dual decomposition cannot be applied, for otherwise the Lagrangian is unbounded below [9]. Since ADMM could be used to solve a large-scale convex optimization problem without assuming strict convexity of the separable objective function, we are thus motivated to design a ADMM-based distributed algorithm.

In order to utilize the ADMM framework, P5 is transformed into the following problem equivalently.

$$
\text {(P6)} \min V \Gamma_ {t} - \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w (Q _ {i, q, t} + H _ {i, q, t}) b _ {i, q, t}
$$

$$
+ \sum_ {i = 1} ^ {N} Z _ {i, t} \left(\eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t}\right)\tag{37a}
$$

$$
s. t. (1), (2), (4), (1 0) - (1 3), (1 8),\tag{37b}
$$

$$
\sum_ {f = 1} ^ {F} a _ {f, i, t} + \sum_ {q = 1} ^ {M _ {i}} \left(b _ {i, q, t} - e _ {i, q, t}\right) + h _ {i} = C _ {i},\tag{37c}
$$

$$
g _ {i, t} + c _ {i, t} + u _ {d, i, t} - u _ {c, i, t} + \beta_ {i} h _ {i} = m _ {i},\tag{37d}
$$

$$
e _ {i, q, t} + z _ {i, q} = b _ {i, q, t},\tag{37e}
$$

$$
d _ {f, i, t} = a _ {f, i, t},\tag{37f}
$$

$$
x _ {i, q, t} = b _ {i, q, t},\tag{37g}
$$

where $h _ { i }$ and $z _ { i , q }$ are a set of nonnegative slack variables; $_ { a _ { f , i , t } }$ and $b _ { i , q , t }$ are nonnegative auxiliary variables; the constant $m _ { i } = \alpha _ { i } + \beta _ { i } C _ { i } - r _ { i , t } ;$ the decision variables are $d _ { f , i , t } , a _ { i , q , t } , x _ { i , q , t } , b _ { i , q , t } , e _ { i , q , t } , c _ { i , t } , g _ { i , t } , u _ { c , i , t } , u _ { d , i , t } , h _ { i } , z _ { i , q } .$

If ADMM framework applies to P6 directly, eleven blocks would be generated since there are eleven kinds of variables. For ADMM with more than two blocks, the convergence is still an open question. In this paper, we adopt the algorithm in [31] to solve P6, which is called as ADM-G (ADM with Gaussian back substitution). The global convergence of ADM-G is provable under mild assumptions. Following the method in our previous work [32], [33], it is easy to check that ADM-G framework could result in an optimal solution of P6 if the optimal solution is non-empty. Due to the space limit, we omit the proof for simplicity. Following the framework of ADM-G, we can obtain a distributed implementation of the proposed realtime algorithm in Appendix C.

## IV. ALGORITHMIC PERFORMANCE ANALYSIS

In this section, we provide the performance analysis of the designed distributed realtime algorithm. Specifically, we first present a Lemma, which offers a sufficient condition for the charging and discharging of the ESS in SMG i at slot t under the proposed algorithm. Then, based on the Lemma, a Theorem is proposed to show the feasibility of the Algorithm 1 for P1.

Lemma 3: Define $\gamma _ { i , \mathrm { m i n } } \mathrm { = m i n } \{ X _ { i , \mathrm { m i n } } , W _ { i , \mathrm { m i n } } , A _ { i , \mathrm { m i n } } ^ { \prime } \}$ . Then, 1) $\mathrm { I f ~ } Z _ { i , t } < - V \eta _ { d , i } \gamma _ { i , \operatorname* { m a x } }$ , the optimal discharging decision is $u _ { d , i , t } ^ { * } = 0 ;$

2) If $\begin{array} { r } { Z _ { i , t } > - \frac { V } { \eta _ { c , i } } \gamma _ { i } } \end{array}$ <sub>,min</sub>, the optimal charging decision is $u _ { c , i , t } ^ { * } = 0 .$

With the above lemma, a theorem is provided to show the performance of the designed algorithm.

Theorem 1: Suppose $x _ { i , q } ^ { \operatorname* { m a x } } \geq$ max $[ \pi _ { i , q } ^ { \operatorname* { m a x } } , ~ \varepsilon _ { i , q } ]$ . If $Q _ { i , q , 0 } =$ $H _ { i , q , 0 } = 0$ , the proposed algorithm can provide the following guarantees:

1) The queues $\boldsymbol { Q } _ { i , q , t }$ and $H _ { i , q , t }$ are bounded by $Q _ { i . a } ^ { \mathrm { m a x } }$ and $H _ { i . a } ^ { \mathrm { m a x } } .$ , respectively. In particular, $Q _ { i , q } ^ { \operatorname* { m a x } } = V \bar { \beta } _ { i } X _ { i } ^ { \operatorname* { m a x } } / w +$ $\pi _ { i , q } ^ { \operatorname* { m a x } } , H _ { i , q } ^ { \operatorname* { m a x } } = V \beta _ { i } X _ { i } ^ { \operatorname* { m a x } } / w + \varepsilon _ { i , q } .$

2) The maximum queueing delay $\begin{array} { r l r l } { R _ { i , q } ^ { \operatorname* { m a x } } } & { { } } & { = } \end{array}$ $\Bigg \lceil \frac { 2 V \beta _ { i } X _ { i } ^ { \operatorname* { m a x } } / w + \pi _ { i , q } ^ { \operatorname* { m a x } } + \varepsilon _ { i , q } } { \varepsilon _ { i , q } } \Bigg \rceil$

3) The energy queue $D _ { i , t }$ satisfies the following for all time slot t: $D _ { i , \operatorname* { m i n } } \leq D _ { i , t } \leq D _ { i , \operatorname* { m a x } }$

4) The solution of the proposed algorithm is feasible to the original problem P1.

5) Compared with the optimal solution of P3, the maximum optimality loss due to the incorporation of ramping constraints in P4 is $\begin{array} { r } { \Omega _ { 1 } = \sum _ { i = 1 } ^ { N } V ( 1 - \epsilon _ { i } ) c _ { i , \mathrm { m a x } } \gamma _ { i , \mathrm { m a x } } } \end{array}$

6) Compared with the optimal solution of P5, the maximum optimality loss in the aspect of $\Gamma _ { t }$ caused by the online solution adjustment is $\begin{array} { r } { \dot { \Omega } _ { 2 } = \sum _ { i = 1 } ^ { N } ( \sigma _ { i } ( u _ { i , \mathrm { c m a x } } ^ { 2 } + } \end{array}$ u<sup>2</sup><sub>i,dmax</sub>) + δ<sub>1,i</sub>c<sup>2</sup><sub>i,max</sub> + δ<sub>2,i</sub>c<sub>i,max</sub>	.

7) If $\varepsilon _ { i , q } ~ \leq ~ \mathbb { E } \{ \pi _ { i , q , t } \}$ and the uncertain parameters $\lambda _ { f , t } ,$ $\pi _ { i , q , t } , r _ { i , t } , X _ { i , t }$ and $W _ { i , t }$ are i.i.d. over slots, the proposed algorithm offers the following performance guarantee, i.e., lim sup $\begin{array} { r } { \frac { 1 } { T } \sum _ { t = 0 } ^ { T - 1 } \mathbb { E } \{ \Gamma _ { t } \} \leq y _ { 1 } + \Omega _ { 2 } + \frac { \Omega _ { 0 } + \breve { \Omega } _ { 1 } } { V } } \end{array}$ , where T→∞ $y _ { 1 }$ is the optimal objective value of P1.

![](yu2018_distributed_realtime_dcmg_assets/images/68194a3ea2e92a595a1b2d21873ba1b1ff97c0b8c988737783815b303c6987d0.jpg)  
(a) Maximum queue length (MQL)

![](yu2018_distributed_realtime_dcmg_assets/images/20f2e33c8e7512df38e6338648fa8629a9f69bab4a0f85c01addae2e5173c33e.jpg)  
(b) Maximum queueing delay (MQD)  
Fig. 3. The feasibility of the proposed algorithm.

![](yu2018_distributed_realtime_dcmg_assets/images/ccc260d9ea6312edbc0f893300f79543e3b3ed4cc89fd7f7156adac5bf76f33b.jpg)  
(c) Energy level

Proof: See Appendix E.

## V. PERFORMANCE EVALUATION

## A. Simulation Setup

We intend to evaluate the performance of the proposed algorithm in six months with 4320 1-hour slots. To model the generation cost of conventional generator i, a quadratic polynomial is adopted as in [14], $. . . , A _ { i } ( c _ { i , t } ) = \delta _ { 1 , i } c _ { i , t } ^ { 2 } + \delta _ { 2 , i } c _ { i , t } + \delta _ { 3 , i } .$ For simplicity, we set $\delta _ { 1 , i } = \delta _ { 3 , i } = 0 , \delta _ { 2 , i } = 2 7 3 \ S / M W \ [ 2 9 ]$ To model the battery depreciation cost, a function is considered as in [28], i.e., $B _ { i } ( u _ { c , i , t } , u _ { d , i , t } ) \ = \ \sigma _ { i } ( u _ { c , i , t } ^ { 2 } + u _ { d , i , t } ^ { 2 } )$ We set $\epsilon _ { i } = 1 , \sigma _ { i } = 1 0 0 , \eta _ { c , i } = \eta _ { d , i } = 1$ . The parameters associated with data centers and front-end servers are given as follows, i.e., $F ~ = ~ 1 , ~ N ~ = ~ 3 , ~ M _ { 1 } ~ = ~ 4 0 0 0 0 .$ M<sub>2</sub> = 30000, M<sub>3</sub> = 30000, P<sub>i,peak</sub> = 200 Watts, $P _ { i , \mathrm { i d l e } } =$ 140 Watts, $P U E _ { 1 } ~ = ~ 1 . 1 , ~ P U E _ { 2 } ~ = ~ 1 . 2 , ~ P U E _ { 3 } ~ = ~ 1 . 3 .$ $u _ { i , \mathrm { c m a x } } ~ = ~ u _ { i , \mathrm { d m a x } } ~ = ~ 0 . 5 ~ \mathrm { M W } ~ [ 4 ] . ~ \omega ~ = ~ 1 ~ \times ~ 1 0 ^ { - 4 } ~ [ 2 6 ]$ $\theta _ { i } \mathrm { = } 0 . 1 , V = V ^ { \operatorname* { m a x } } , \varepsilon _ { i , q } = ( 2 V \beta _ { i } X _ { i } ^ { \operatorname* { m a x } } / w + \pi _ { i , q } ^ { \operatorname* { m a x } } ) / ( \mathcal { T } _ { i , q } - 1 )$ $x _ { i , q } ^ { \mathrm { m a x } } ~ = ~ \pi _ { i , q } ^ { \mathrm { m a x } } \cdot ~ D _ { 1 , \mathrm { m a x } } ~ = ~ 8 . 8 ~ \mathrm { M W h } , ~ D _ { 2 , \mathrm { m a x } } ~ = ~ 7 . 2 ~ \mathrm { M W h }$ , $D _ { 3 , \mathrm { { m a x } } } ~ = ~ 7 . 8$ MWh (i.e., data centers could be supported by these ESSs for one hour). In addition, real-world workload traces<sup>4</sup> and dynamic electricity price $\mathrm { t r a c e s } ^ { 5 }$ are adopted in simulations. $W _ { i , t } ~ = ~ 0 . 9 X _ { i , t }$ [13]. Suppose that there are two types of batch workloads, i.e., $M _ { i } = 2$ . To evaluate the impacts of tolerant service delays on the cost reduction under the proposed algorithm, two cases are considered, i.e., case1: $\mathcal { T } _ { i , q } \in \{ 4 , 8 \} ;$ case2: $\mathcal { T } _ { i , q } \in \{ 1 2 , 2 4 \}$ . To model the batch workload with type q at data center i, we assume that it follows a uniform distribution with parameters 0 and $C _ { i } / ( 5 M _ { i } )$ .

To show the advantages of the proposed distributed realtime algorithm, three baselines are adopted.

• The first baseline (B1) intends to minimize the long-term operational cost with the considerations of energy storage and selling electricity, while batch workloads are processed immediately without delays.

• The second baseline (B2) intends to minimize the current operational cost considering selling electricity. Moreover, batch workloads are processed immediately. In addition, no energy storage is considered in B2.

• The three baseline (B3) intends to minimize the current operational cost without considering energy storage and selling electricity. Moreover, batch workloads are processed immediately.

For simplicity, Proposed-1 and Proposed-2 are adopted to denote the performance of the proposed algorithm under case1 and case2, respectively.

## B. Simulation Results

1) Algorithmic Feasibility: In this subsection, we show the feasibility of the proposed algorithm. Specifically, we need to show that the constraints (7), (8), (15) could be satisfied under the proposed algorithm. As indicated in Fig. 3 (a), the maximum queue lengths of $\boldsymbol { Q } _ { i , q , t }$ and $H _ { i , q , t }$ are always smaller than their respective upper bounds (i.e., the constraint (7) holds in all time slots). Moreover, in Fig. 3 (b), maximum queueing delays are smaller than the corresponding tolerant service delays, which means that the proposed algorithm could provide the heterogeneous service delay guarantees for all batch workloads, i.e., (8) could be satisfied. In addition, the cumulative distribution functions (CDFs) of energy levels in ESSs are provided (note that just the results under Proposed-2 with $w \stackrel { } { = } 1 0 ^ { - 1 2 }$ are given) in Fig. 3 (c), where energy levels fluctuate within their normal ranges, i.e., (15) could be guaranteed. Based on the above description, it can be known that the solution of the proposed algorithm is feasible to the original problem P1.

2) Convergence Results: Before giving the performance comparisons between the proposed algorithm and other baselines, we first provide the convergence results of the proposed algorithm, which are illustrated in Figs. 4 (a)-(c). In Fig. 4 (a), the iterative process of the total operational cost in a time slot is shown, while Figs. 4 (b) and (c) show the trajectory of the primal residual and feasibility violation metric (which are defined in Appendix C), respectively. It can be observed that the proposed algorithm converges to the same optimal value (which is the same as the result generated by the GAMS commercial solver)<sup>6</sup> given different penalty parameters ρ. Moreover, the computation complexity of the proposed algorithm is low since all subproblems in the distributed implementation could be solved in parallel based on closed-form expressions or binary search.

![](yu2018_distributed_realtime_dcmg_assets/images/674bcbef410f22fe18754ea752f996758ef9764ad3df5093ee7aa9e2720a3b07.jpg)  
(a) Total cost

![](yu2018_distributed_realtime_dcmg_assets/images/6236dd7bf12d7b2b1e3a9d64646a39e5dd9bc3a83116346c599acbd731418152.jpg)  
(b) Primal residual

![](yu2018_distributed_realtime_dcmg_assets/images/5c9b6f8a311902592d7ec2a73ddeccc537e567b586496295cf561605d49d92df.jpg)  
(c) Feasibility violation

Fig. 4. Convergence results of the proposed algorithm.  
![](yu2018_distributed_realtime_dcmg_assets/images/09c69efb94cd964f4984fa616949c76f4a955a3c1fb149bf0dce666b4be23f32.jpg)  
(a) Operational cost

![](yu2018_distributed_realtime_dcmg_assets/images/298b36b4176101c4151474a266c32073049c85db5a6d4b5665f20807db1b2342.jpg)  
(b) Profit of selling electricity

![](yu2018_distributed_realtime_dcmg_assets/images/d226167b6f4fb941b325645341c3546352c52c8562f15ac2fb01eca11f976af2.jpg)  
(c) AMQD

Fig. 5. Performances under varying queue weight w.  
![](yu2018_distributed_realtime_dcmg_assets/images/1676965a422110526957bc053b0495c059bbd1569aaa04bbcd4b5cdeaca32560.jpg)  
(a) Operational cost

![](yu2018_distributed_realtime_dcmg_assets/images/b4724e253bbfa056c44cb24f58acf8d93196eac89dab0f159621be535db28337.jpg)  
(b) Relative cost reduction

![](yu2018_distributed_realtime_dcmg_assets/images/fb67b739f578ff85aeec198e66ec29af14b23fb8a9dd35cd1af364c74e045dd1.jpg)  
(c) Dropping ratio

Fig. 6. Performances under varying penalty factor θ<sub>i</sub>.  
![](yu2018_distributed_realtime_dcmg_assets/images/d3b9d7696755be46b5bf5a12ef5cea52d4b78ec1dcae876c53d00f907e0a5d7d.jpg)  
(a) Operational cost

![](yu2018_distributed_realtime_dcmg_assets/images/cc39633e135c1e76ae819554904672e66f2f6f7450e44c5ad130f9e2c03891be.jpg)  
(b) Profit of selling electricity

![](yu2018_distributed_realtime_dcmg_assets/images/613345a4e8f65242ad686f3d22d19171b67f24221c951b78d295429c95cc59c0.jpg)  
(c) AMQD  
Fig. 7. Performances under varying tolerant service delay $\mathcal { T } _ { i , q } .$

Since we do not have enough hardware resources to conduct an experiment with a parallel implementation, the proposed algorithm is implemented on a single Intel Core i5-2410M

2.3GHz server (4G RAM), it takes 1.462 seconds to finish 600 iterations. Since the duration of a time slot is usually several minutes/hours (e.g., electricity prices in some deregulated electricity markets are updated every 5 minutes),<sup>7</sup> the time consumed by the proposed algorithm could be neglected when considering parallel implementation and “early braking” (i.e., terminating the algorithm before the convergence is reached once we obtain an acceptable solution, e.g., the primal residual and feasibility violation are small enough). Therefore, the proposed online distributed algorithm is very suitable for practical applications.

3) Queue Weight w: In Fig. 5 (a), the operational costs under different algorithms are provided, and we find that the proposed algorithm achieves the best performance. Compared with B1, B2, and B3, Proposed-2 with $w = 1 0 ^ { - 1 2 }$ can reduce the operational cost by 1.48%, 2.55%, and 15.15%, respectively. The reason is that the proposed algorithm can fully utilize the temporal diversity of electricity price by serving batch workloads in proper time slots without violating their deadlines, by controlling the discharging/charging of ESSs in proper time slots, and by selling electricity to main grids when there are excess renewable energies. Thus, the proposed algorithm could obtain the largest profit of selling electricity among all algorithms as shown in Fig. 5 (b). In addition, it can be observed that larger w results in smaller AMQD (The Average value of Maximum Queueing Delays experienced by all workloads $\pi _ { i , q , t } )$ , since larger w would lead to more frequent service for batch workloads as indicated in the objective function of P5 in Appendix $\mathrm { E , }$ which means that less temporal diversity of electricity price could be utilized to reduce operational cost. Consequently, the proposed algorithm shows better performances given a smaller w.

4) Dropping Penalty Factor θ<sub>i</sub>: We set $w \ = \ 1 0 ^ { - 1 2 }$ in this scenario. In Figs. 6 (a) and (b), it can be seen that Proposed-2 always achieves the lowest operational cost. By observing the objective function of P6, it can be known that the proposed algorithm intends to discard less batch workloads given a larger $\theta _ { i } ,$ resulting in a smaller dropping ratio $\begin{array} { r l } { ( \mathrm { i . e . , } \sum _ { i } \sum _ { q } \sum _ { t } ( e _ { i , q , t } / a _ { i , q , t } ) ) } & { { } } \end{array}$ as shown in Fig. 6 (c). Therefore, the proposed algorithm would reduce to be B1 if $\theta _ { i }$ is approaching to zero, since all batch workloads would be dropped and no energy queue is needed under this situation.

5) Tolerant Service Delay $\mathcal { T } _ { i , q } \dot { . }$ For simplicity, we assume that $\mathcal { T } _ { i , q }$ is the same for all i and q. As shown in Figs. 7 (a) and (b), the operational cost becomes lower and the profit of selling electricity become larger with the increase of tolerant service delay $\mathrm { i f } \stackrel { \cdot } { w } = 1 0 ^ { - 1 0 }$ , while those values are almost unchanged if $w = 1 0 ^ { - 5 }$ . The reason is that the proposed algorithm puts very large “weight” on maintaining the stability of workload queue $\boldsymbol { Q } _ { i , q , t }$ and virtual queue $H _ { i , q , t }$ <sub>t</sub> when $w \ = \ 1 0 ^ { - 5 }$ , resulting in very small queueing delay and AMQD as shown in Fig. 7 (c). Consequently, low utilization of temporal price diversity is incurred even the tolerant service delays of batch workloads are large. Thus, choosing a proper queue weight w is critical to utilize the heterogeneous tolerant service delays for operational cost reduction.

## VI. CONCLUSION

This paper proposed a distributed realtime algorithm for minimizing the long-term operational cost of multiple data center microgrids with the considerations of many factors, e.g., providing heterogeneous service delay guarantees for batch workloads, interactive workload allocation, batch workload shedding, electricity buying/selling, battery charging/discharging efficiency, and the ramping constraints of backup generators. The proposed algorithm does not require any prior knowledge of statistical characteristics related to system parameters and has low computational complexity. Extensive simulation results showed that the proposed algorithm could reduce the operational cost of data center microgrids effectively.

## APPENDIX A PROOF OF LEMMA 1

Given a slot t, it can be proved that the energy demand $\pi _ { i , q , t }$ could be satisfied before $t + R _ { i , q } ^ { \operatorname* { m a x } }$ . If the above declaration is not true (a contradiction would be reached), we have $Q _ { i , q , \tau } >$ $x _ { i , q , \tau }$ for all slots $\tau \in \{ t + 1 , t + 2 , \dots , t + R _ { i , q } ^ { \operatorname* { m a x } } \}$ . According to (17), we can obtain that $H _ { i , q , \tau + 1 } = [ H _ { i , q , \tau } - x _ { i , q , \tau } + \varepsilon _ { i , q } ] ^ { + }$ for all slots $\tau \in \{ t + 1 , t + 2 , \dots , t + R _ { i , q } ^ { \operatorname* { m a x } } \}$ . Continually, we have

$$
H _ {i, q, \tau + 1} \geq H _ {i, q, \tau} - x _ {i, q, \tau} + \varepsilon_ {i, q},\tag{38}
$$

Summing the above equation from slot $t + 1$ to $t + R _ { i , q } ^ { \operatorname* { m a x } }$ we have

$$
H _ {i, q, t + R _ {i, q} ^ {\max} + 1} - H _ {i, q, t + 1} \geq R _ {i, q} ^ {\max} \varepsilon_ {i, q} - \sum_ {\tau = t + 1} ^ {t + R _ {i, q} ^ {\max}} x _ {i, q, \tau}.\tag{39}
$$

Since $H _ { i , q , t + 1 } \geq 0$ and $H _ { i , q , t + R _ { i , q } ^ { \operatorname* { m a x } } + 1 } \leq H _ { i , q } ^ { \operatorname* { m a x } }$ , (39) could be transformed into (40),

$$
\sum_ {\tau = t + 1} ^ {t + R _ {i, q} ^ {\max}} x _ {i, q, \tau} + H _ {i, q} ^ {\max} \geq R _ {i, q} ^ {\max} \varepsilon_ {i, q}.\tag{40}
$$

In addition, the summation of $x _ { i , q , \ i }$ <sub>τ</sub> over the interval $\{ t + 1 , t + 2 , \dots , t + R _ { i , q } ^ { \operatorname* { m a x } } \}$ is strictly smaller than $Q _ { i , q , t + 1 }$ Otherwise, $\pi _ { i , q , t }$ would be served within the interval. Thus, we have

$$
\sum_ {\tau = t + 1} ^ {t + R _ {i, q} ^ {\max}} x _ {i, q, \tau} <   Q _ {i, q, t + 1} \leq Q _ {i, q} ^ {\max}.\tag{41}
$$

Finally, combining (40) and (41), we obtain

$$
R _ {i, q} ^ {\max} <   \left\lceil \left(H _ {i, q} ^ {\max} + Q _ {i, q} ^ {\max}\right) / \varepsilon_ {i, q} \right\rceil .\tag{42}
$$

Note that (42) contradicts the definition of $R _ { i , q } ^ { \operatorname* { m a x } }$ . Thus, the workload batch $\pi _ { i , q , t }$ must be served before $t + R _ { i , q } ^ { \operatorname* { m a x } }$

## APPENDIX B PROOF OF LEMMA 2

According to the definition of $Q _ { i , q , t } ,$ we have

$$
\begin{array}{l} Q _ {i, q, t + 1} ^ {2} = \bigl (\max \bigl \{Q _ {i, q, t} - x _ {i, q, t}, 0 \bigr \} + \pi_ {i, q, t} \bigr) ^ {2} \\ \qquad \leq Q _ {i, q, t} ^ {2} + x _ {i, q, t} ^ {2} + \pi_ {i, q, t} ^ {2} + 2 Q _ {i, q, t} \bigl (\pi_ {i, q, t} - x _ {i, q, t} \bigr). \end{array}
$$

Then, we can obtain

$$
\begin{array}{l} \frac {Q _ {i , q , t + 1} ^ {2} - Q _ {i , q , t} ^ {2}}{2} \leq \frac {\left(x _ {i , q} ^ {\max}\right) ^ {2} + \left(\pi_ {i , q} ^ {\max}\right) ^ {2}}{2} \\ \quad + Q _ {i, q, t} (\pi_ {i, q, t} - x _ {i, q, t}). \end{array}
$$

For the queue $H _ { i , q , t }$ , we have

$$
\begin{array}{l} H _ {i, q, t + 1} ^ {2} \leq \big (\max \big [ H _ {i, q, t} - x _ {i, q, t} + \varepsilon_ {i, q}, 0 \big ] \big) ^ {2} \\ \leq \big (H _ {i, q, t} - x _ {i, q, t} + \varepsilon_ {i, q} \big) ^ {2}. \end{array}
$$

Then, we have

$$
\begin{array}{l} \frac {H _ {i , q , t + 1} ^ {2} - H _ {i , q , t} ^ {2}}{2} \leq \frac {\left(\varepsilon_ {i , q} - x _ {i , q , t}\right) ^ {2}}{2} + H _ {i, q, t} \big (\varepsilon_ {i, q} - x _ {i, q, t} \big), \\ \leq \frac {\max \left\{\varepsilon_ {i , q} ^ {2} , \left(x _ {i , q} ^ {\max}\right) ^ {2} \right\}}{2} + H _ {i, q, t} \big (\varepsilon_ {i, q} - x _ {i, q, t} \big) \end{array}
$$

Similarly, for the queue $Z _ { i , t }$ , we have

$$
\frac {Z _ {i , t + 1} ^ {2} - Z _ {i , t} ^ {2}}{2} \leq \frac {\max \left\{\left(\eta_ {c , i} u _ {i , \mathrm{cmax}}\right) ^ {2} , \left(\frac {1}{\eta_ {d , i}} u _ {i , \mathrm{dmax}}\right) ^ {2} \right\}}{2}   +   Z _ {i, t} \biggl (\eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d, i}} u _ {d, i, t} \biggr).
$$

Combining three upper bounds mentioned above together, we have the following inequality,

$$
\begin{array}{l} \Delta_ {t} \leq \mathbb {E} \left\{\sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w Q _ {i, q, t} \big (\pi_ {i, q, t} - x _ {i, q, t} \big) \Bigg | \boldsymbol {\Theta} _ {t} \right\} \\ \quad + \mathbb {E} \left\{\sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w H _ {i, q, t} \big (\varepsilon_ {i, q} - x _ {i, q, t} \big) \Bigg | \boldsymbol {\Theta} _ {t} \right\} \\ \quad + \mathbb {E} \left\{\sum_ {i = 1} ^ {N} Z _ {i, t} \bigg (\eta_ {c, i} u _ {c, i, t} - \frac {1}{\eta_ {d , i}} u _ {d, i, t} \bigg) \Bigg | \boldsymbol {\Theta} _ {t} \right\} + \Omega_ {0}. \end{array}\tag{43}
$$

By adding $V \mathbb { E } \{ \Gamma _ { t } | \Theta _ { t } \}$ to the both sides of the above equation, we could complete the proof.

## APPENDIX C THE DISTRIBUTED IMPLEMENTATION OF ALGORITHM 1

1. Initialization: Decision variables of P6 are initialized with zero. In each iteration k, two steps (i.e., prediction step and correction step) are repeated until convergence.

2. ADMM step (prediction step). Obtain all decision variables in the forwarding order:

2.1 $d _ { f , i , t }$ -minimization: each front-end server f solves P7 in parallel to obtain $\tilde { d } _ { f , i , t } ^ { k } .$

$$
\text {(P7)} \min \Phi_ {1} \left(d _ {f, i, t}, \chi_ {f, i} ^ {k}, a _ {f, i, t} ^ {k}\right)\tag{44a}
$$

$$
s. t. (1), (2),\tag{44b}
$$

where $\begin{array} { c c c } { \Phi _ { 1 } ( d _ { f , i , t } , \chi _ { i , q } ^ { k } , a _ { f , i , t } ^ { k } ) } & { = } & { \sum _ { i = 1 } ^ { N } ( ( V \omega L _ { f , i } + \chi _ { f , i } ^ { k } - } \end{array}$ $\rho { a } _ { f , i , t } ^ { k } ) { { d } _ { f , i , t } } \mathrm { ~ + ~ } \frac { \rho } { 2 } { a } _ { f , i , t } ^ { 2 } ) ; \mathrm { ~ } \rho$ is the penalty parameter in the augmented Lagrangian for P6, while $\phi _ { i } , \varphi _ { i } , \kappa _ { i , q } , \chi _ { f , i } , \psi _ { i , q }$ are dual variables associated with $( 3 7 \mathrm { c } , - ( 3 7 \mathrm { g } ) )$ , respectively.

2.2 $x _ { i , q , t }$ -minimization: each queue controller q in data center i solves P8 in parallel to obtain $\tilde { x } _ { i , q , t } ^ { k }$

$$
\text {(P8)} \min \Phi_ {2} \left(x _ {i, q, t}, \psi_ {i, q} ^ {k}, b _ {i, q, t} ^ {k}\right)\tag{45a}
$$

$$
s. t. \quad (4),\tag{45b}
$$

where $\begin{array} { r } { \Phi _ { 2 } ( x _ { i , q , t } , \psi _ { i , q } ^ { k } , b _ { i , q , t } ^ { k } ) = \psi _ { i , q } ^ { k } x _ { i , q , t } + \frac { \rho } { 2 } ( x _ { i , q , t } - b _ { i , q , t } ^ { k } ) ^ { 2 } . } \end{array}$

2.3 c<sub>i,t</sub>-minimization: each conventional generator in SMG i solves P9 in parallel to obtain $\tilde { c } _ { i , t } ^ { k }$

$$
\text {(P9)} \min \Phi_ {3} \left(c _ {i, t}, \varphi_ {i} ^ {k}, g _ {i, t} ^ {k}, u _ {d, i, t} ^ {k}, u _ {c, i, t} ^ {k}, h _ {i} ^ {k}\right)\tag{46a}
$$

(46b)

where $\begin{array} { r } { \Phi _ { 3 } ( c _ { i , t } , \varphi _ { i } ^ { k } , g _ { i , t } ^ { k } , u _ { d , i , t } ^ { k } , u _ { c , i , t } ^ { k } , h _ { i } ^ { k } ) = V A _ { i } ( c _ { i , t } ) + \frac { \rho } { 2 } c _ { i , t } ^ { 2 } + } \end{array}$ $( { \varphi } _ { i } ^ { k } + \rho ( g _ { i , t } ^ { k } + u _ { d , i , t } ^ { k } - u _ { c , i , t } ^ { k } + \beta _ { i } h _ { i } ^ { k } - m _ { i } ) ) c _ { i , t } .$

2.4 $u _ { c , i , t } .$ -minimization: each ESS in SMG i solves P10 in parallel to obtain $\tilde { u } _ { c , i , t } ^ { k } .$

$$
\text {(P10)} \min \Phi_ {4} \left(u _ {c, i, t}, \varphi_ {i} ^ {k}, u _ {d, i, t} ^ {k}, g _ {i, t} ^ {k}, \tilde {c} _ {i, t} ^ {k}, h _ {i} ^ {k}\right)\tag{47a}
$$

$$
s. t. (1 2),\tag{47b}
$$

where $\begin{array} { r l r l r } { \Phi _ { 4 } ( u _ { c , i , t } , \varphi _ { i } ^ { k } , u _ { d , i , t } ^ { k } , g _ { i , t } ^ { k } , \tilde { c } _ { i , t } ^ { k } , h _ { i } ^ { k } ) } & { { } \ } & { = } & { { } } & { \frac { \rho } { 2 } u _ { c , i , t } ^ { 2 } \quad + } \end{array}$ $V B _ { i } ( u _ { c , i , t } , u _ { d , i , t } ^ { k } ) ~ + ~ ( Z _ { i , t } \eta _ { c , i } ~ - ~ \varphi _ { i } ^ { k } ~ - ~ \rho ( g _ { i , t } ^ { k } ~ + ~ \tilde { c } _ { i , t } ^ { k } ~ + ~ u _ { d , i , t } ^ { k } ~ + ~ $ $\beta _ { i } h _ { i } ^ { k } - m _ { i } ) ) u _ { c , i , t } .$

2.5 $u _ { d , i , t } .$ -minimization: each ESS in SMG i solves P11 in parallel to obtain $\tilde { u } _ { d , i , t } ^ { k }$

$$
\begin{array}{l} \text {(P11)} \min \Phi_ {5} \Big (u _ {d, i, t}, \tilde {u} _ {c, i, t} ^ {k}, \varphi_ {i} ^ {k}, g _ {i, t} ^ {k}, \tilde {c} _ {i, t} ^ {k}, h _ {i} ^ {k} \Big) \\ s. t. (1 3), \end{array}\tag{48a}
$$

(48b)

where $\begin{array} { r l r l r } { \Phi _ { 5 } ( u _ { d , i , t } , \tilde { u } _ { c , i , t } ^ { k } , \varphi _ { i } ^ { k } , g _ { i , t } ^ { k } , \tilde { c } _ { i , t } ^ { k } , h _ { i } ^ { k } ) } & { { } \ } & { = } & { { } } & { \frac { \rho } { 2 } u _ { d , i , t } ^ { 2 } + } \end{array}$ $V B _ { i } ( \tilde { u } _ { c , i , t } ^ { k } , u _ { d , i , t } ) - ( \check { Z } _ { i , t } / \eta _ { d , i } - \overset  { \varphi } _ { i } ^ { k } - \rho ( \overset  { g } _ { i , t } ^ { k } + \tilde { c } _ { i , t } ^ { k } - \tilde { u } _ { c , i , t } ^ { k } + \overset \sim { \beta } _ { i } h _ { i } ^ { k } -$ $m _ { i } ) ) u _ { d , i , t } .$

2.6 $_ { a _ { f , i , t } }$ -minimization: each EMS in SMG i solves P12 in parallel to obtain $\tilde { a } _ { f , i , t } ^ { k } .$

$$
\text { (P12) } \min \Phi_ {6} \left(a _ {f, i, t}, \phi_ {i} ^ {k}, b _ {i, q, t} ^ {k}, e _ {i, q, t} ^ {k}, h _ {i} ^ {k}, \chi_ {f, i} ^ {k}, \tilde {d} _ {f, i, t} ^ {k}\right)\tag{49a}
$$

$$
s. t. a _ {f, i, t} \geq 0,\tag{49b}
$$

where $\begin{array} { r c l } { \Phi _ { 6 } ( a _ { f , i , t } , \phi _ { i } ^ { k } , b _ { i , q , t } ^ { k } , e _ { i , q , t } ^ { k } , h _ { i } ^ { k } , \chi _ { f , i } ^ { k } , \tilde { d } _ { f , i , t } ^ { k } ) } & { = } & { \frac { \rho } { 2 } ( { \sum _ { f = 1 } ^ { F } } } \end{array}$ $\begin{array} { r c l } { a _ { f , i , t } ^ { 2 } ~ + ~ ( \sum _ { f = 1 } ^ { F } a _ { f , i , t } ) ^ { 2 } ) ^ { \cdot } ~ + ~ \sum _ { f = 1 } ^ { F } ( \phi _ { i } ^ { k } ~ - ~ \chi _ { f , i } ^ { k } ~ - ~ \rho \tilde { d } _ { f , i , t } ^ { k } ~ + ~ } \end{array}$ $\begin{array} { r } { \bar { \rho } ( \sum _ { q = 1 } ^ { M _ { i } } ( b _ { i , q , t } ^ { k } - e _ { i , q , t } ^ { k } ) + h _ { i } ^ { k } - C _ { i } ) a _ { f , i , t } . } \end{array}$

2.7 $b _ { i , q , t }$ -minimization: each EMS in SMG i solves P13 in parallel to obtain $\tilde { b } _ { i , q , t } ^ { k } .$

$$
\text { (P13) } \min \Phi_ {7} \Big (b _ {i, q, t}, \phi_ {i} ^ {k}, \kappa_ {i, q} ^ {k}, \psi_ {i, q} ^ {k}, \tilde {a} _ {f, i, t} ^ {k}, e _ {i, q, t} ^ {k}, h _ {i} ^ {k}, z _ {i, q} ^ {k} \Big)\tag{50a}
$$

$$
s. t. b _ {i, q, t} \geq 0,\tag{50b}
$$

$$
\Phi_ {7} (b _ {i, q, t}, \phi_ {i} ^ {k}, \kappa_ {i, q} ^ {k}, \tilde {a} _ {f, i, t} ^ {k}, e _ {i, q, t} ^ {k}, h _ {i} ^ {k}, z _ {i, q} ^ {k}) = \frac {\rho}{2} (\sum_ {q = 1} ^ {M _ {i}}
$$

$$
2 b _ {i, q, t} ^ {2} + (\sum_ {q = 1} ^ {M _ {i}} b _ {i, q, t}) ^ {2}) - \sum_ {q = 1} ^ {M _ {i}} (w (Q _ {i, q, t} + H _ {i, q, t}) - \phi_ {i} ^ {k} + \kappa_ {i, q} ^ {k} +
$$

$$
\begin{array}{l} \psi_ {i, q} ^ {k} + \rho (e _ {i, q, t} ^ {k} + z _ {i, q} ^ {k} + \tilde {x} _ {i, q, t} ^ {k}) - \rho (\sum_ {f = 1} ^ {F} \tilde {a} _ {f, i, t} ^ {k} - \sum_ {q = 1} ^ {M _ {i}} e _ {i, q, t} ^ {k} + \\ h _ {i} ^ {k} - C _ {i})) b _ {i, q, t}. \end{array}
$$

2.8 $e _ { i , q , \mathrm { ~ } }$ <sub>t</sub>-minimization: each EMS in SMG i solves P14 in parallel to obtain $\tilde { e } _ { i , q , t } ^ { k } .$

$$
\text {(P14)} \min \Phi_ {8} (e _ {i, q, t}, \kappa_ {i, q} ^ {k}, \phi_ {i} ^ {k}, z _ {i, q} ^ {k}, \tilde {b} _ {i, q, t} ^ {k}, \tilde {a} _ {f, i, t} ^ {k}, h _ {i} ^ {k})\tag{51a}
$$

$$
s. t. e _ {i, q, t} \geq 0,\tag{51b}
$$

where $\begin{array} { r l r } { \Phi _ { 8 } ( e _ { i , q , t } , \kappa _ { i , q } ^ { k } , \phi _ { i } ^ { k } , z _ { i , q } ^ { k } , \tilde { b } _ { i , q , t } ^ { k } , \tilde { a } _ { f , i , t } ^ { k } , h _ { i } ^ { k } ) } & { { } = } & { \frac { \rho } { 2 } ( \sum _ { q = 1 } ^ { M _ { i } } } \end{array}$ $\begin{array} { r } { e _ { i , q , t } ^ { 2 } + ( \sum _ { q = 1 } ^ { M _ { i } } e _ { i , q , t } ) ^ { 2 } ) + \sum _ { q = 1 } ^ { M _ { i } } ( V \dot { \theta _ { i } } - \phi _ { i } ^ { k } + \kappa _ { i , q } ^ { k } + \rho ( z _ { i , q } ^ { k } - \tilde { b } _ { i , q , t } ^ { k } ) - } \end{array}$ $\begin{array} { r } { \rho ( \sum _ { f = 1 } ^ { F } \tilde { a } _ { f , i , t } ^ { \hat { k } } + \sum _ { q = 1 } ^ { M _ { i } } \tilde { b } _ { i , q , t } ^ { k } + h _ { i } ^ { k } - C _ { i } ) ) e _ { i , q , t } ) . } \end{array}$

2.9 h -minimization: each EMS in SMG i solves P15 in parallel to obtain $\tilde { h } _ { i } ^ { k }$

$$
\text {(P15)} \min \Phi_ {9} \left(h _ {i}, \phi_ {i} ^ {k}, \varphi_ {i} ^ {k}, g _ {i, t} ^ {k}, \tilde {c} _ {i, t} ^ {k}, \tilde {u} _ {c, i, t} ^ {k}, \tilde {u} _ {d, i, t} ^ {k}, \tilde {a} _ {f, i, t} ^ {k}, \tilde {b} _ {i, q, t} ^ {k}, \tilde {e} _ {i, q, t} ^ {k}\right)\tag{52a}
$$

$$
s. t. h _ {i} \geq 0,\tag{52b}
$$

where $\Phi _ { 9 } ( h _ { i } , \phi _ { i } ^ { k } , \varphi _ { i } ^ { k } , g _ { i , t } ^ { k } , \tilde { c } _ { i , t } ^ { k } , \tilde { u } _ { c , i , t } ^ { k } , \tilde { u } _ { d , i , t } ^ { k } , \tilde { a } _ { f , i , t } ^ { k } , \tilde { b } _ { i , q , t } ^ { k } , \tilde { e } _ { i , q , t } ^ { k } ) \ =$ $\begin{array} { r } { \frac { \rho } { 2 } ( 1 + \beta _ { i } ^ { 2 } ) h _ { i } ^ { 2 } + ( \phi _ { i } ^ { k } + \beta _ { i } \varphi _ { i } ^ { k } + \rho \beta _ { i } ( g _ { i , t } ^ { k } + \tilde { c } _ { i , t } ^ { k } + \tilde { u } _ { d , i , t } ^ { k } - \tilde { u } _ { c , i , t } ^ { k } - } \\ { - } \end{array}$ $\begin{array} { r } { m _ { i } ) + \rho ( \sum _ { f = 1 } ^ { F } \tilde { a } _ { f , i , t } ^ { k } + \sum _ { q = 1 } ^ { M _ { i } } ( \tilde { b } _ { i , q , t } ^ { k } - \tilde { e } _ { i , q , t } ^ { k } ) - C _ { i } ) ) h _ { i } . } \end{array}$

2.10 $z _ { i , q }$ -minimization: each EMS in SMG i solves P16 in parallel to obtain $\tilde { z } _ { i , q } ^ { k } .$

$$
\text { (P16) } \min \Phi_ {1 0} \Big (z _ {i, q}, \tilde {e} _ {i, q, t} ^ {k}, \tilde {b} _ {i, q, t} ^ {k}, \kappa_ {i, q} ^ {k} \Big)\tag{53a}
$$

$$
s. t. z _ {i, q} \geq 0,\tag{53b}
$$

where <sub>10</sub>(z<sub>i,q</sub>, e˜<sup>k</sup><sub>i,q,t</sub>, b<sup>˜k</sup><sub>i,q,t</sub>, κ<sup>k</sup><sub>i,q</sub>) = <sup>ρ</sup><sub>2</sub> z<sup>2</sup><sub>i,q</sub> + (ρ(e˜<sup>k</sup><sub>i,q,t</sub> − b<sup>˜ k</sup><sub>i,q,t</sub>) + $\kappa _ { i , q } ^ { k } ) z _ { i , q } .$

2.11 g<sub>i,t</sub>-minimization: each EMS in SMG i solves P17 in parallel to obtain $\tilde { g } _ { i , t } ^ { k }$

$$
\text { (P17) } \min \Phi_ {1 1} \Big (g _ {i, t}, \varphi_ {i} ^ {k}, \tilde {c} _ {i, t} ^ {k}, \tilde {u} _ {c, i, t} ^ {k}, \tilde {u} _ {d, i, t} ^ {k}, \tilde {h} _ {i} ^ {k} \Big)\tag{54a}
$$

s.t. (18),

(54b)

where $\begin{array} { r } { \Phi _ { 1 1 } ( g _ { i , t } , \tilde { u } _ { c , i , t } ^ { k } , \tilde { u } _ { d , i , t } ^ { k } , \tilde { h } _ { i \dots } ^ { k } ) = \frac { \rho } { 2 } g _ { i , t } ^ { 2 } + . ( \varphi _ { i } ^ { k } + \rho ( \tilde { c } _ { i , t } ^ { k } + \tilde { u } _ { d , i , t } ^ { k } - } \end{array}$ u˜ <sup>k</sup><sub>c,i,t</sub> + β<sub>i</sub>h<sup>˜ k</sup><sub>i</sub> − m<sub>i</sub>))g<sub>i,t</sub> + <sup>Xi,t−Wi,t</sup><sub>2</sub> |g<sub>i,t</sub> | + <sup>Xi,t+Wi,t</sup><sub>2</sub> g<sub>i,t</sub> .

Note that P7-P17 are convex optimization problems and their solutions could be obtained easily based on closedform expressions or binary search. Thus, the algorithms for them are omitted for brevity. Similar algorithms could be found in [33].

2.12 Dual update: the EMS in SMG i updates $\tilde { \phi } _ { i } ^ { k } , \tilde { \varphi } _ { i } ^ { k } , \tilde { \kappa } _ { i , q } ^ { k }$ as follows: $\begin{array} { r } { \tilde { \phi } _ { i } ^ { k } = \phi _ { i } ^ { k } + \rho ( \sum _ { f = 1 } ^ { F } \tilde { a } _ { f , i , t } + \sum _ { q = 1 } ^ { M _ { i } } ( \tilde { b } _ { i , q , t } - \tilde { e } _ { i , q , t } ) ^ { \dagger } + } \end{array}$ $\tilde { h } _ { i } - C _ { i } ) ; \tilde { \varphi } _ { i } ^ { k } = \varphi _ { i } ^ { k } + \rho ( \tilde { g } _ { i , t } ^ { k } + \tilde { c } _ { i , t } ^ { k } + \tilde { u } _ { d , i , t } ^ { k } - \tilde { u } _ { c , i , t } ^ { k } + \beta _ { i } \tilde { h } _ { i } ^ { k } - m _ { i } )$ $\tilde { \kappa } _ { i , q } ^ { k } = \kappa _ { i , q } ^ { k } + \rho ( \tilde { e } _ { i , q , t } ^ { k } + \tilde { z } _ { i , q } ^ { k } - \tilde { b } _ { i , q , t } ^ { k } ) ;$ ; each front-end server f updates $\tilde { \chi } _ { f , i } ^ { \hat { k } }$ as follows, i.e., $\tilde { { x } } _ { f , i } ^ { k } = { x } _ { f , i } ^ { k } + \rho ( \tilde { d } _ { f , i , t } ^ { k } - \tilde { a } _ { f , i , t } ^ { k } ) ;$ ; each queue controller $q$ in data center i updates $\tilde { \psi } _ { i , q } ^ { k }$ as follows: $\tilde { \psi } _ { i , q } ^ { k } = \psi _ { i , q } ^ { k } + \rho ( \tilde { x } _ { i , q , t } ^ { k } - \tilde { b } _ { i , q , t } ^ { k } )$

3. Gaussian back substitution step (correction step): Obtain the input parameters of iteration $k + 1$ according to the Gaussian back substitution step (3.5b) in [31], where the constant α in (3.5b) is set to one based on the practical experience [33]. Then, we have

$$
\begin{array}{r} \phi_ {i} ^ {k + 1} = \tilde {\phi} _ {i} ^ {k}, \varphi_ {i} ^ {k + 1} = \tilde {\varphi} _ {i} ^ {k}, \kappa_ {i, q} ^ {k + 1} = \tilde {\kappa} _ {i, q} ^ {k}, \\ \tilde {\chi} _ {f, i} ^ {k} = \chi_ {f, i} ^ {k}, \psi_ {i, q} ^ {k + 1} = \tilde {\psi} _ {i, q} ^ {k}, g _ {i, t} ^ {k + 1} = \tilde {g} _ {i, t} ^ {k}, z _ {i, q} ^ {k + 1} = \tilde {z} _ {i, q} ^ {k}, \end{array}
$$

$$
\begin{array}{l} h _ {i} ^ {k + 1} = \tilde {h} _ {i} ^ {k} - \frac {\beta_ {i}}{1 + \beta_ {i} ^ {2}} \Big (\tilde {g} _ {i, t} ^ {k} - g _ {i, t} ^ {k} \Big), \\ e _ {i, q, t} ^ {k + 1} = \tilde {e} _ {i, q, t} ^ {k} + \frac {\Big (h _ {i} ^ {k + 1} - h _ {i} ^ {k} \Big) + \sum_ {q = 1} ^ {M _ {i}} \Big (\tilde {z} _ {i , q} ^ {k} - z _ {i , q} ^ {k} \Big)}{M _ {i} + 1} \\ \quad - \Big (\tilde {z} _ {i, q} ^ {k} - z _ {i, q} ^ {k} \Big), \\ b _ {i, q, t} ^ {k + 1} = \tilde {b} _ {i, q, t} ^ {k} \\ \quad + \frac {\sum_ {q = 1} ^ {M _ {i}} \Big (e _ {i , q , t} ^ {k + 1} - e _ {i , q , t} ^ {k} - z _ {i , q} ^ {k + 1} + z _ {i , q} ^ {k} \Big) - 2 \Big (h _ {i} ^ {k + 1} - h _ {i} ^ {k} \Big)}{2 (M _ {i} + 2)} \\ \quad + \frac {1}{2} \Big (z _ {i, q} ^ {k + 1} - z _ {i, q} ^ {k} + e _ {i, q, t} ^ {k + 1} - e _ {i, q, t} ^ {k} \Big), \\ a _ {f, i, t} ^ {k + 1} = \tilde {a} _ {f, i, t} ^ {k} \\ \quad + \frac {\sum_ {q = 1} ^ {M _ {i}} \Big (e _ {i , q , t} ^ {k + 1} - e _ {i , q , t} ^ {k} - b _ {i , q , t} ^ {k + 1} + b _ {i , q , t} ^ {k} \Big) - \Big (h _ {i} ^ {k + 1} - h _ {i} ^ {k} \Big)}{F + 1} \\ u _ {d, i, t} ^ {k + 1} = \tilde {u} _ {d, i, t} ^ {k} - \beta_ {i} \Big (h _ {i} ^ {k + 1} - h _ {i} ^ {k} \Big) - \Big (g _ {i, t} ^ {k + 1} - g _ {i, t} ^ {k} \Big), \\ u _ {c, i, t} ^ {k + 1} = \tilde {u} _ {c, i, t} ^ {k} + \Big (\tilde {u} _ {d, i, t} ^ {k} - u _ {d, i, t} ^ {k} \Big), c _ {i, t} ^ {k + 1} = \tilde c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c & x _ {i, q, t} ^ {k + 1} = \tilde {x} _ {i, q, t} ^ {k} + \Big (b _ {i, q, t} ^ {k + 1} - b _ {i, q, t} ^ {k} \Big), d _ {f, i, t} ^ {k + 1} = \tilde {d} _ {f, i, t} ^ {k}. \end{array}
$$

4. Stopping criterion: As in our previous work [33], we terminate the designed algorithm before the convergence is reached once we obtain an acceptable feasible solution, $\mathrm { e . g . , }$ when the primal residual  is small enough and the obtained solution is feasible. Specifically, the primal residual is defined in (55). Moreover, a feasibility metric  is adopted as in (56) to indicate the feasibility of the obtained solution.

$$
\begin{array}{l} \Xi^ {2} = \sum_ {i = 1} ^ {N} \left(\sum_ {f = 1} ^ {F} a _ {f, i, t} ^ {k} + \sum_ {q = 1} ^ {M _ {i}} \left(b _ {i, q, t} ^ {k} - e _ {i, q, t} ^ {k}\right) + h _ {i} ^ {k} - C _ {i}\right) ^ {2} \\ \quad + \sum_ {i = 1} ^ {N} \left(g _ {i, t} ^ {k} + c _ {i, t} ^ {k} + u _ {d, i, t} ^ {k} - u _ {c, i, t} ^ {k} + \beta_ {i} h _ {i} ^ {k} - m _ {i}\right) ^ {2} \\ \quad + \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} \left(e _ {i, q, t} ^ {k} + z _ {i, q} ^ {k} - b _ {i, q, t} ^ {k}\right) ^ {2} \\ \quad + \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} \left(x _ {i, q, t} ^ {k} - b _ {i, q, t} ^ {k}\right) ^ {2} \\ \quad + \sum_ {i = 1} ^ {N} \sum_ {f = 1} ^ {F} \left(d _ {f, i, t} ^ {k} - a _ {f, i, t} ^ {k}\right) ^ {2}. \\ \ell = \sum_ {i = 1} ^ {N} \max \left(\sum_ {f = 1} ^ {F} d _ {f, i, t} ^ {k} + \sum_ {q = 1} ^ {M _ {i}} \left(x _ {i, q, t} ^ {k} - e _ {i, q, t} ^ {k}\right) - C _ {i}, 0\right) \\ \quad + \sum_ {i = 1} ^ {N} \left| g _ {i, t} ^ {k} + r _ {i, t} + c _ {i, t} ^ {k} + u _ {d, i, t} ^ {k} - p _ {i, t} ^ {k} - u _ {c, i, t} ^ {k} \right| \\ \quad + \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} \max \left(e _ {i, q, t} ^ {k} - x _ {i, q, t} ^ {k}, 0\right). \end{array}\tag{55}
$$

(56)

Note that the implementation of one iteration in the proposed algorithm could be described as follows. At initial iteration, each front-end server $f ,$ each queue controller $q$ in data center i, each conventional generator in SMG i make their local and parallel decisions independently to obtain $\tilde { a } _ { f , i , t } ^ { k } , \tilde { x } _ { i , q , t } ^ { k } ,$ $\tilde { c } _ { i , t } ^ { k }$ , respectively. Then, such decisions are broadcasted to other components in SMGs, e.g., ESSs and EMS. After receiving the broadcasted decisions, each ESS and EMS in SMG i make their local decisions on $\tilde { a } _ { f , i , t } ^ { k }$ and $\tilde { u } _ { c , i , t } ^ { k } ,$ and $\tilde { u } _ { d , i , t } ^ { k } .$ . Then, ESS i broadcasts $\tilde { u } _ { c , i , t } ^ { k } ,$ and $\tilde { u } _ { d , i , t } ^ { k ^ { \prime } }$ to the EMS i. Next, EMS i could obtains other decisions on $\ddot { \tilde { b } } _ { i , q , t } ^ { k } , \tilde { e } _ { i , q , t } ^ { k } , \tilde { h } _ { i } ^ { k } , \tilde { z } _ { i , q } ^ { k }$ and $\tilde { g } _ { i , t } ^ { k }$ . Finally, EMS i broadcasts all obtained decision variables at iteration $\begin{array} { r } { k + 1 \ ( \mathrm { i . e . , ~ } g _ { i , t } ^ { k + 1 } , \ z _ { i , q } ^ { k + 1 } , \ h _ { i } ^ { k + 1 } , \ e _ { i , q , t } ^ { k + 1 } , \ b _ { i , q , t } ^ { k + 1 } , \ a _ { f , i , t } ^ { k + 1 } , \ u _ { d , i , t } ^ { k + 1 } , u _ { c , i , t } ^ { k + 1 } ) } \end{array}$ so that all entities (i.e., front-end servers, queue controllers, conventional generators, and ESSs) could update their respective decisions in iteration $k + 1$ according to the Gaussian back substitution step.

## APPENDIX D PROOF OF LEMMA 3

1) Let $( u _ { c , i , t } ^ { * } , u _ { d , i , t } ^ { * } , x _ { i , q , t } ^ { * } , e _ { i , q , t } ^ { * } , d _ { f , i , t } ^ { * } , \pi _ { i , t } ^ { * } , g _ { i , t } ^ { * } , c _ { i , t } ^ { * } )$ be the optimal decision vector obtained from the Algorithm 1. For SMG $i ,$ suppose $Z _ { i , t } < - V \eta _ { d , i } \gamma _ { i , \mathrm { { m a x } } }$ and $u _ { d , i , t } ^ { * } > 0 ;$ then, $u _ { c , i , t } ^ { * } = 0$ . Then, we can prove the non-optimality of the above decision by choosing another decision vector $( 0 , 0 , x _ { i , q , t } ^ { * } , e _ { i , q , t } ^ { * } , d _ { f , i , t } ^ { * } , \pi _ { i , t } ^ { * } , \tilde { g } _ { i , t } ^ { * } , \tilde { c } _ { i , t } ^ { * } )$ . Suppose the objective values corresponding to the above decision vectors under the Algorithm 1 are $\Upsilon _ { 1 , i }$ and $\Upsilon _ { 2 , i } ,$ respectively. Given the same energy demand $p _ { i , t }$ , there are three kinds of the decisions for energy supply: Case 1: If $g _ { i , t } ^ { * } = 0$ , we choose $\tilde { g } _ { i , t } ^ { * } = 0$ , then, $\tilde { c } _ { i , t } ^ { * } =$ $c _ { i , t } ^ { * } + u _ { d , i , t } ^ { * }$ . Next, $\begin{array} { r } { \Upsilon _ { 1 , i } - \Upsilon _ { 2 , i } > ( - \frac { Z _ { i , t } } { \eta _ { d } { \mathrm { \Omega } } _ { i } } - V A _ { i , \operatorname* { m a x } } ^ { \prime } ) u _ { d , i , t } ^ { * } > 0 . } \end{array}$ Case $2 \colon \mathrm { H } \ g _ { i , t } ^ { * } \ > \ 0$ , we choose $\ddot { \tilde { g } } _ { i , t } ^ { * } = 0 , \tilde { c } _ { i , t } ^ { * } = c _ { i , t } ^ { * } ,$ then, $\tilde { g } _ { i , t } ^ { * } = g _ { i , t } ^ { * } + u _ { d , i , t } ^ { * }$ . Next, $\begin{array} { r } { \Upsilon _ { 1 , i } - \Upsilon _ { 2 , i } > ( - \frac { Z _ { i , t } } { \eta _ { d , i } } - } \end{array}$ $V X _ { i , \operatorname* { m a x } } ) u _ { d , i , t } ^ { * } > 0$ Case 3: If $g _ { i , t } ^ { * } ~ < ~ 0$ , we choose $\tilde { g } _ { i , t } ^ { * } = 0 , \tilde { c } _ { i , t } ^ { * } = c _ { i , t } ^ { * } ,$ then, $\tilde { g } _ { i , t } ^ { * } = g _ { i , t } ^ { * } - u _ { d , i , t } ^ { * }$ . Next, $\Upsilon _ { 1 , i } - \Upsilon _ { 2 , i } > ( - \frac { Z _ { i , t } } { \eta _ { d , i } } -$ $V W _ { i , \operatorname* { m a x } } ) u _ { d , i , t } ^ { * } > 0$ In summary, when $Z _ { i , t } ~ < ~ - V \eta _ { d , i } \gamma _ { i , \mathrm { { m a x } } }$ , the optimal discharging decision is $u _ { d , i , t } ^ { * } = 0$ 2) The proof of part 2 is similar to that of part 1. Thus, it is omitted for brevity.

## APPENDIX E PROOF OF THEOREM 1

1) The objective value of P5 could be rewritten as follows by discarding some constant items,

$$
\begin{array}{l} \bigsqcup (d _ {f, i, t}, e _ {i, q, t}, c _ {i, t}, g _ {i, t}, u _ {c, i, t}, u _ {d, i, t}) \\ \quad + \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} \bigl (V \beta_ {i} X _ {i, t} - w \bigl (Q _ {i, q, t} + H _ {i, q, t} \bigr) \bigr) x _ {i, q, t} \mathcal {I} _ {1}, \\ \quad + \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} \bigl (V \beta_ {i} W _ {i, t} - w \bigl (Q _ {i, q, t} + H _ {i, q, t} \bigr) \bigr) x _ {i, q, t} \mathcal {I} _ {2}, \end{array}
$$

$$
+ \sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} \left(- w \left(Q _ {i, q, t} + H _ {i, q, t}\right)\right) x _ {i, q, t} \mathcal {I} _ {3},
$$

where (υ) is the function of $\upsilon ; \ \mathcal { T } _ { 1 } , \ \mathcal { T } _ { 2 } , \ \mathcal { T } _ { 3 }$ denote $g _ { i , t } > 0 , g _ { i , t } < 0 .$ , and $g _ { i , t } = 0$ , respectively. It can be observed that the proposed algorithm would choose the maximum possible $x _ { i , q , t }$ when $Q _ { i , q , t } > V \beta _ { i } X _ { i } ^ { \operatorname* { m a x } } / w$ . In the following parts, we would use the induction method to prove $Q _ { i , q } ^ { \mathrm { m a x } } = V \beta _ { i } X _ { i } ^ { \mathrm { m a x } } / w + \pi _ { i , q } ^ { \mathrm { m a x } }$ for all slots. It is obvious that $Q _ { i , q , 0 } \leq Q _ { i , q } ^ { \operatorname* { m a x } }$ . Suppose $Q _ { i , q , t } \leq Q _ { i , a } ^ { \operatorname* { m a x } }$ , we will show that $\bar { Q } _ { i , q , t + 1 } \overset { \triangledown } { \le } Q _ { i , q } ^ { \operatorname* { m a x } }$ . If $Q _ { i , q , t } \leq V \beta _ { i } \ddot { X } _ { i } ^ { \mathrm { i n a x } } / w$ the maximum queue growth is $\pi _ { i , q } ^ { \mathrm { m a x } }$ . Thus, we have $Q _ { i , q , t + 1 } ~ \le ~ Q _ { i , q , t } + \pi _ { i , q } ^ { \operatorname* { m a x } } ~ \le ~ V \beta _ { i } ^ { \cdots } \mathrm { m a x } _ { / w } + \pi _ { i , q } ^ { \operatorname* { m a x } }$ . If $Q _ { i , q , t } ~ \ge ~ V \beta _ { i } X _ { i } ^ { \operatorname* { m a x } } / w$ , the proposed algorithm would choose $x _ { i , q , t } ~ = ~ \operatorname* { m i n } \{ Q _ { i , q , t } , x _ { i , q } ^ { \operatorname* { m a x } } \}$ . Thus, $Q _ { i , q , t + 1 } \leq$ max $\{ Q _ { i , q , t } , \pi _ { i , q } ^ { \mathrm { m a x } } \} \le Q _ { i , q } ^ { \mathrm { m a x } }$ . Similarly, we can prove that $Z _ { i , q , t } \leq Z _ { i , q } ^ { \operatorname* { m a x } }$ for any slot t. The proof detail is omitted for brevity. Continually, it can be known that (7) could be satisfied.

2) According to Lemma 1 and the part 1 of Theorem 1, we have $R _ { i , q } ^ { \operatorname* { m a x } } = \lceil ( 2 V \beta _ { i } X _ { i , q } ^ { \operatorname* { m a x } } / w + \pi _ { i , q } ^ { \operatorname* { m a x } } + \varepsilon _ { i , q } ) / \varepsilon _ { i , q } \rceil$ Therefore, we can construct an algorithm to ensure that all charging requests have delay less than or equal to $R _ { i \mathrm { ~ } a } ^ { \operatorname* { m a x } }$ slots, where $R _ { i , q } ^ { \operatorname* { m a x } } \ \geq \ 2$ . When choosing $x _ { i , q , t } ~ =$ $x _ { i , q } ^ { \mathrm { m a x } }$ in each time slot t, we can guarantee that all charging requests have one slot delay. In summary, the proposed algorithm could be constructed to ensure the heterogeneous service delays for all EV charging requests, i.e., (8) could be satisfied under the proposed algorithm.

3) Proving $D _ { i , t } \ \in \ [ D _ { i , \operatorname* { m i n } } , \ D _ { i , \operatorname* { m a x } } ]$ is equivalent to satisfying the following constraints: $Z _ { i , t } \ge - V \eta _ { d , i } \gamma _ { i , \operatorname* { m a x } } -$ $\frac { \mathrm { ~ 1 ~ } } { \eta _ { d , i } } \bar { u _ { i , \mathrm { d m a x } } }$ , and $Z _ { i , t } \ \le D _ { i , \operatorname* { m a x } } - D _ { i , \operatorname* { m i n } } - V \eta _ { d , i } \gamma _ { i , \operatorname* { m a x } } \ -$ $\frac { 1 } { \eta _ { d , i } } u _ { i , \mathrm { d m a x } }$ . Because $D _ { i , \operatorname* { m i n } } \leq D _ { i , 0 } \leq D _ { i , \operatorname* { m a x } }$ , the above inequalities hold for $\scriptstyle t = 0$ . Suppose the above-mentioned inequalities hold for the time slot $t ,$ we should verify that they hold for the time slot $t { + } 1$

$\begin{array} { r } { \bullet \operatorname { I f } \ - V \eta _ { d , i } \gamma _ { i , \operatorname* { m a x } } - \frac { 1 } { \eta _ { d , i } } u _ { i , \mathrm { d m a x } } \leq Z _ { i , t } < - V \eta _ { d , i } \gamma _ { i } , } \end{array}$ <sub>,max</sub>, then, according to the Lemma 3, $u _ { d , i , t } ^ { * } ~ = ~ 0$ . As a result, $Z _ { i , t + 1 } ~ = ~ Z _ { i , t } + \eta _ { c , i } u _ { c , i , t } ^ { * } \geq Z _ { i , t } ~ \geq$ $\begin{array} { r } { - V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } - \frac { 1 } { \eta _ { d , i } } u _ { i , \mathrm { d m a x } } . \mathrm { I f } \ - V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } \leq Z _ { i , t } < } \end{array}$ $\begin{array} { r } { D _ { i , \mathrm { m a x } } - D _ { i , \mathrm { m i n } } - V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } - \frac { 1 } { \eta _ { d , i } } u _ { i , \mathrm { d m a x } } . } \end{array}$ , then, $\begin{array} { r } { Z _ { i , t + 1 } \ge - V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } - \frac { 1 } { \eta _ { d , i } } u _ { d , i , t } ^ { * } > - V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } - } \end{array}$ ${ \frac { 1 } { \eta _ { d , i } } } u _ { i , \mathrm { d m a x } } .$

$\begin{array} { r } { \mathrm { I f } - \frac { V } { \eta _ { d , i } } \gamma _ { i , \mathrm { m i n } } < Z _ { i , t } \leq D _ { i , \mathrm { m a x } } - D _ { i , \mathrm { m i n } } - V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } - } \end{array}$ ${ \frac { 1 } { \eta _ { d , i } } } u _ { i , \mathrm { d m a x } } ,$ then, $u _ { c , i , t } ^ { * } = 0$ . Consequently, $Z _ { i , t + 1 } \leq$ $\begin{array} { r } { Z _ { i , t } \le D _ { i , \operatorname* { m a x } } - D _ { i , \operatorname* { m i n } } - V \eta _ { d , i } \gamma _ { i , \operatorname* { m a x } } - \frac { 1 } { \eta _ { d , i } } u _ { i , \mathrm { d m a x } } . } \end{array}$ . If $\begin{array} { r } { - V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } - \frac { 1 } { \eta _ { d , i } } u _ { i , \mathrm { d m a x } } \leq Z _ { i , t } \leq - \frac { V ^ { \cdots } } { \eta _ { c , i } } \gamma _ { i , \mathrm { m i n } } . } \end{array}$ , then, $\begin{array} { r } { Z _ { i , t + 1 } \le - \frac { V } { \eta _ { c , i } } \gamma _ { i , \mathrm { m i n } } + \eta _ { c , i } u _ { i , \mathrm { c m a x } } \le D _ { i , \mathrm { m a x } } - D _ { i , \mathrm { m i n } } - } \end{array}$ $\begin{array} { r } { V \eta _ { d , i } \gamma _ { i , \mathrm { m a x } } - \frac { 1 } { \eta _ { d , i } } u _ { i , \mathrm { d m a x } } } \end{array}$ , where

$$
V \leq \frac {D _ {i , \max} - D _ {i , \min} - \left(\eta_ {c , i} u _ {i , \operatorname * {c m a x}} + \frac {1}{\eta_ {d , i}} u _ {i , \operatorname * {d m a x}}\right)}{\eta_ {d , i} \gamma_ {i , \max} - \frac {1}{\eta_ {c , i}} \gamma_ {i , \min}}.
$$

Continually, $V _ { \mathrm { m a x } }$ is obtained as follows,

$$
V _ {\max} = \min _ {i} \frac {D _ {i , \max} - D _ {i , \min} - \left(\eta_ {c , i} u _ {i , \operatorname * {c m a x}} + \frac {1}{\eta_ {d , i}} u _ {i , \operatorname * {d m a x}}\right)}{\eta_ {d , i} \gamma_ {i , \max} - \frac {1}{\eta_ {c , i}} \gamma_ {i , \min}}.
$$

Based on the above proof, it can be known that (15) could be satisfied.

4) From the parts 1-3, we know that the constraints (7), (8), (15) could be satisfied under the proposed algorithm. Since other constraints in P1 could be guaranteed, the solution of the proposed algorithm is feasible to the original problem P1.

5) Let $( { x } _ { i , q , t } ^ { * } , \ { u } _ { c , i , t } ^ { * } , { u } _ { d , i , t } ^ { * } , { d } _ { f , i , t } ^ { * } , \ { \pi } _ { i , t } ^ { * } , { g } _ { i , t } ^ { * } , { c } _ { i , t } ^ { * } )$ and $( \bar { x } _ { i , q , t } ,$ $\bar { u } _ { c , i , t } , \bar { u } _ { d , i , t } , d _ { f , i , t } , \bar { \pi } _ { i , t } , \bar { g } _ { i , t } , \bar { c } _ { i , t } )$ denote the optimal solution of P3 and P4, respectively. Since the adoption of ramping constraints in P4 would or would not change the value of $\bar { c } _ { i , t }$ , three cases would be incurred.

Case 1: when $c _ { i , t } ^ { * } = \bar { c } _ { i , t }$ , we have $y _ { 3 , i } ^ { * } = \bar { y } _ { 4 , i } .$ , where $y _ { 3 , i } ^ { * }$ and ${ \bar { y } } _ { 4 , i }$ are the optimal objective value associated with the SMG i, respectively.

Case 2: when $c _ { i , t } ^ { * } > \bar { c } _ { i , t } .$ , the effective range of $c _ { i , t }$ in P4 is max $\{ c _ { i , t - 1 } - \epsilon _ { i } c _ { i , \operatorname* { m a x } } , 0 \} \le c _ { i , t } \le c _ { i , t - 1 } + \epsilon _ { i } c _ { i , \operatorname* { m a x } }$ . We choose a feasible solution to P4 as follows, i.e., $( \boldsymbol { x } _ { i , q , t } ^ { * } ,$ $u _ { c , i , t } ^ { * } , u _ { d , i , t } ^ { * } , d _ { f , i , t } ^ { * } , \pi _ { i , t } ^ { * } , g _ { i , t } ^ { * } + c _ { i , t } ^ { * } - c _ { i , t - 1 } - \epsilon _ { i } c _ { i , \operatorname* { m a x } } , c _ { i , t - 1 } +$ $\epsilon _ { i } c _ { i , \operatorname* { m a x } } ) ,$ , which means that the conventional generator must generate less energy due to the ramping constraint and more energy should be purchased from the main grid i to balance power. Then, we have $\bar { y } _ { 4 , i } - y _ { 3 , i } ^ { * } \ \leq$ $V ( 1 - \epsilon _ { i } ) c _ { i , \operatorname* { m a x } } X _ { i } ,$ <sub>max</sub>.

Case 3: when $c _ { i , t } ^ { * } < \bar { c } _ { i , t } ,$ the effective range of $c _ { i , t }$ in P4 is $c _ { i , t - 1 } - \epsilon _ { i } c _ { i , \mathrm { m a x } } \leq c _ { i , t } \leq \operatorname* { m i n } \{ c _ { i , \mathrm { m a x } } , c _ { i , t - 1 } +$ $\epsilon _ { i } c _ { i , \operatorname* { m a x } } \}$ . Set a feasible solution of P4 as $( \boldsymbol { x } _ { i , q , t } ^ { * } ,$ $u _ { c , i , t } ^ { * } , u _ { d , i , t } ^ { * } , d _ { f , i , t } ^ { * } , \pi _ { i , t } ^ { * } , g _ { i , t } ^ { * } + c _ { i , t } ^ { * } - c _ { i , t - 1 } + \epsilon _ { i } c _ { i , \operatorname* { m a x } } , c _ { i , t - 1 } -$ $\epsilon _ { i } c _ { i , \operatorname* { m a x } } ) ,$ , which means that the conventional generator must generate more energy due to the ramping constraint and more energy should be sold to the main grid i to balance power. As a result, $\bar { y } _ { 4 , i } - y _ { 3 , i } ^ { * } \ \leq$ $V ( 1 - \epsilon _ { i } ) c _ { i , \operatorname* { m a x } } A _ { i , \operatorname* { m a x } } ^ { \prime }$

$$
\bar {y} _ {4} \leq y _ {3} ^ {*} + \sum_ {i = 1} ^ {N} V (1 - \epsilon_ {i}) c _ {i, \max} \gamma_ {i, \max},
$$

6) Let $( x _ { i , q , t } , u _ { c , i , t } , u _ { d , i , t } , d _ { f , i , t } , \pi _ { i , t } , g _ { i , t } , c _ { i , t } )$ and $( \hat { x } _ { i , q , t } ,$ $\hat { u } _ { c , i , t } , \hat { u } _ { d , i , t } , \hat { d } _ { f , i , t } , \hat { \pi } _ { i , t } , \hat { g } _ { i , t } , \hat { c } _ { i , t } )$ denote the optimal solution of P5 and the proposed algorithm, respectively. According to the online adjustment in Algorithm 1, we have $\begin{array} { r } { \Gamma _ { p , t } - \Gamma _ { 5 , t } \leq \sum _ { i = 1 } ^ { N } \tilde { \left( \sigma _ { i } ( \hat { u } _ { c , i , t } ^ { 2 } + \hat { u } _ { d , i , t } ^ { 2 } ) + \delta _ { 1 , i } \hat { c } _ { i , t } ^ { 2 } + \right. } } \end{array}$ $\delta _ { 2 , i } \hat { c } _ { i , t } ) \leq \Omega _ { 2 }$ , where $\Gamma _ { 5 , t }$ and $\Gamma _ { p , t }$ are the values of $\Gamma _ { t }$ corresponding to the solutions of P5 and the proposed algorithm, respectively.

7) Let $y _ { 1 }$ and $y _ { 2 }$ denote the optimal solution of P1 and P2, respectively. Since P2 is a relaxation of P1, we have $y _ { 2 } ~ \le ~ y _ { 1 }$ . Since P5 is a relaxation of P4, we have

$$
\Delta_ {t} + V \mathbb {E} \left\{\Gamma_ {5, t} | \boldsymbol {\Theta} _ {t} \right\}\tag{57}
$$

$$
\leq \Delta_ {t} + V \mathbb {E} \left\{\Gamma_ {4, t} | \boldsymbol {\Theta} _ {t} \right\}\tag{58}
$$

$$
\begin{array}{l} \leq \Omega_ {1} + \Omega_ {0} + V \mathbb {E} \Bigl \{\tilde {\Gamma} _ {3, t} | \boldsymbol {\Theta} _ {t} \Bigr \} \\ \quad + \mathbb {E} \left\{\sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w Q _ {i, q, t} \Bigl (\pi_ {i, q, t} - x _ {i, q, t} ^ {*} \Bigr) | \boldsymbol {\Theta} _ {t} \right\} \\ \quad + \mathbb {E} \left\{\sum_ {i = 1} ^ {N} \sum_ {q = 1} ^ {M _ {i}} w H _ {i, q, t} \Bigl (\varepsilon_ {i, q} - x _ {i, q, t} ^ {*} \Bigr) | \boldsymbol {\Theta} _ {t} \right\} \end{array}
$$

$$
+ \mathbb {E} \left\{\sum_ {i = 1} ^ {N} Z _ {i, t} \left(\eta_ {c, i} u _ {c, i, t} ^ {*} - \frac {1}{\eta_ {d , i}} u _ {d, i, t} ^ {*}\right) | \boldsymbol {\Theta} _ {t} \right\}
$$

$$
\leq \Omega_ {1} + \Omega_ {0} + V y _ {2}\tag{59}
$$

$$
\leq \Omega_ {1} + \Omega_ {0} + V y _ {1},\tag{60}
$$

(61)

where $\Gamma _ { 4 , t }$ and $\Gamma _ { 3 , t }$ are the values of $\Gamma _ { t }$ corresponding to the solutions of P4 and P3, respectively; $x _ { i , q , t } ^ { * } , u _ { c , i , t } ^ { * } , u _ { d , i , t } ^ { * }$ are the elements in the solution vector of P3; (58) is derived by the part 5 of Theorem 1; (59) is obtained by incorporating the results of a stationary, randomized control strategy associated with P2 [8]. By arranging the both sides of the above equations, we have $\mathbb { E } [ \Delta _ { t } ] + V \mathbb { E } [ \Gamma _ { 5 , t } ] \le \Omega _ { 1 } + \Omega _ { 0 } + V y _ { 1 }$ Continually, we have $\begin{array} { r } { V { \sum _ { t = 0 } ^ { T - 1 } } \mathbb { E } \{ \Gamma _ { 5 , t } \} \le \Omega _ { 1 } T + Y T + } \end{array}$ $V T y _ { 1 } { - } \mathbb { E } \{ L _ { T } \} { + } \mathbb { E } \{ L _ { 0 } \}$ . Dividing both side by VT, and taking a lim sup of both sides. Then, let $T \to \infty$ , we have lim su $\begin{array} { r } { \mathfrak { \backslash } _ { T  \infty } \overset { \cdot } { T } \sum _ { t = 0 } ^ { T - 1 } \mathbb { E } \{ \Gamma \varsigma , t \} \le y _ { 1 } + \frac { \Omega _ { 0 } + \Omega _ { 1 } } { V } } \end{array}$ . By taking the part 6 of Theorem 1 into consideration, we have lim $\begin{array} { r } { \stackrel { \cdot } { \operatorname* { s u p } } _ { T  \infty } \frac { 1 } { T } \sum _ { t = 0 } ^ { T - 1 } \mathbb { E } \{ \Gamma _ { p , t } \} \leq y _ { 1 } + \Omega _ { 2 } + \frac { \Omega _ { 0 } + \Omega _ { 1 } } { V } } \end{array}$ , which completes the proof.

## REFERENCES

[1] P. X. Gao, A. R. Curtis, B. Wong, and S. Keshav, “It’s not easy being green,” in Proc. ACM SIGCOMM, Helsinki, Finland, Aug. 2012, pp. 211–222.

[2] (2016). Amazon Addresses EC2 Power Outages. [Online]. Available: http://www.datacenterknowledge.com

[3] D. Salomonsson, L. Soder, and A. Sannino, “An adaptive control system for a DC microgrid for data centers,” IEEE Trans. Ind. Appl., vol. 44, no. 6, pp. 1910–1917, Nov./Dec. 2008.

[4] L. Yu, T. Jiang, and Y. Cao, “Energy cost minimization for distributed Internet data centers in smart microgrids considering power outages,” IEEE Trans. Parallel Distrib. Syst., vol. 26, no. 1, pp. 120–130, Jan. 2015.

[5] L. Yu, T. Jiang, and Y. Zou, “Real-time energy management for cloud data centers in smart microgrids,” IEEE Access, vol. 4, pp. 941–950, 2016.

[6] J. Li and W. Qi, “Towards optimal operation of Internet data center microgrid,” IEEE Trans. Smart Grid, to be published, doi: 10.1109/TSG.2016.25722402.

[7] C. C. Thompson, P. E. K. Oikonomou, A. H. Etemadi, and V. J. Sorger, “Optimization of data center battery storage investments for microgrid cost savings, emissions reduction, and reliability enhancement,” IEEE Trans. Ind. Appl., vol. 52, no. 3, pp. 2053–2060, May/Jun. 2016.

[8] M. J. Neely, Stochastic Network Optimization With Application to Communication and Queueing Systems. San Rafael, CA, USA: Morgan & Claypool, 2010.

[9] S. Boyd, N. Parikh, E. Chu, B. Peleato, and J. Eckstein, “Distributed optimization and statistical learning via the alternating direction method of multipliers,” Found. Trends Mach. Learn., vol. 3, no. 1, pp. 1–122, 2011.

[10] X. Guan, Z. Xu, and Q.-S. Jia, “Energy-efficient buildings facilitated by microgrid,” IEEE Trans. Smart Grid, vol. 1, no. 3, pp. 243–252, Dec. 2010.

[11] M. Erol-Kantarci, B. Kantarci, and H. T. Mouftah, “Reliable overlay topology design for the smart microgrid network,” IEEE Netw., vol. 25, no. 5, pp. 38–43, Sep./Oct. 2011.

[12] Y. Huang, S. Mao, and R. M. Nelms, “Adaptive electricity scheduling in microgrids,” IEEE Trans. Smart Grid, vol. 5, no. 1, pp. 270–281, Jan. 2014.

[13] Y. Zhang, N. Gatsis, and G. B. Giannakis, “Robust energy management for microgrids with high-penetration renewables,” IEEE Trans. Sustain. Energy, vol. 4, no. 4, pp. 944–953, Oct. 2013.

[14] K. Rahbar, J. Xu, and R. Zhang, “Real-time energy storage management for renewable integration in microgrid: An off-line optimization approach,” IEEE Trans. Smart Grid, vol. 6, no. 1, pp. 124–134, Jan. 2015.

[15] W. Ma, J. Wang, V. Gupta, and C. Chen, “Distributed energy management for networked microgrids using online ADMM with regret,” IEEE Trans. Smart Grid, to be published, doi: 10.1109/TSG.2016.2569604.

[16] Y. Guo and C. Zhao, “Islanding-aware robust energy management for microgrids,” IEEE Trans. Smart Grid, to be published, doi: 10.1109/TSG.2016.2585092.

[17] W. Shi, N. Li, C.-C. Chu, and R. Gadh, “Real-time energy management in microgrids,” IEEE Trans. Smart Grid, vol. 8, no. 1, pp. 228–238, Jan. 2017.

[18] T. Chen, X. Wang, and G. B. Giannakis, “Cooling-aware energy and workload management in data centers via stochastic optimization,” IEEE J. Sel. Topics Signal Process., vol. 10, no. 2, pp. 402–415, Mar. 2016.

[19] L. Yu, T. Jiang, Y. Cao, and Q. Qi, “Carbon-aware energy cost minimization for distributed Internet data centers in smart microgrids,” IEEE Internet Things J., vol. 1, no. 3, pp. 255–264, Jun. 2014.

[20] L. Yu, T. Jiang, Y. Cao, and Q. Qi, “Joint workload and battery scheduling with heterogeneous service delay guarantees for data center energy cost minimization,” IEEE Trans. Parallel Distrib. Syst., vol. 26, no. 7, pp. 1937–1947, Jul. 2015.

[21] R. Deng, G. Xiao, R. Lu, and J. Chen, “Fast distributed demand response with spatially and temporally coupled constraints in smart grid,” IEEE Trans. Ind. Informat., vol. 11, no. 6, pp. 1597–1606, Dec. 2015.

[22] S. Sun, M. Dong, and B. Liang, “Distributed real-time power balancing in renewable-integrated power grids with storage and flexible loads,” IEEE Trans. Smart Grid, vol. 7, no. 5, pp. 2337–2349, Sep. 2016.

[23] L. Yu, T. Jiang, and Y. Zou, “Price-sensitivity aware load balancing for geographically distributed Internet data centers in smart grid environment,” IEEE Trans. Cloud Comput., to be published, doi: 10.1109/TCC.2016.2564406.

[24] L. Yu, T. Jiang, Y. Cao, and Q. Zhang, “Risk-constrained operation for Internet data centers in deregulated electricity markets,” IEEE Trans. Parallel Distrib. Syst., vol. 25, no. 5, pp. 1306–1316, May 2014.

[25] A. Qureshi, R. Weber, H. Balakrishnan, J. Guttag, and B. Maggs, “Cutting the electric bill for Internet-scale systems,” in Proc. ACM SIGCOMM, Barcelona, Spain, Aug. 2009, pp. 123–134.

[26] H. Xu and B. Li, “Joint request mapping and response routing for geodistributed cloud services,” in Proc. IEEE INFOCOM, Turin, Italy, 2013, pp. 854–862.

[27] Z. Zhou, F. Liu, Z. Li, and H. Jin, “When smart grid meets geo-distributed cloud: An auction approach to datacenter demand response,” in Proc. IEEE INFOCOM, Hong Kong, 2015, pp. 2650–2658.

[28] J. Rivera, P. Wolfrum, S. Hirche, C. Goebel, and H.-A. Jacobsen, “Alternating direction method of multipliers for decentralized electric vehicle charging control,” in Proc. IEEE CDC, Florence, Italy, 2013, pp. 6960–6965.

[29] (2016). The Fuel Consumption of a Diesel Generator. [Online]. Available: http://generatorjoe.net/html/fueluse.asp

[30] D. P. Bertsekas, Dynamic Programming and Optimal Control, 2nd ed. Belmont, MA, USA: Athena Sci., 2000.

[31] B. He, M. Tao, and X. Yuan, “Alternating direction method with Gaussian back substitution for separable convex programming,” SIAM J. Optim., vol. 22, no. 2, pp. 313–340, 2012.

[32] L. Yu, T. Jiang, and Y. Zou, “Distributed online energy management for data centers and electric vehicles in smart grid,” IEEE Internet Things J., vol. 3, no. 6, pp. 1371–1384, Dec. 2016.

[33] L. Yu, T. Jiang, Y. Zou, and Z. Sun, “Joint energy management strategy for geo-distributed data centers and electric vehicles in smart grid environment,” IEEE Trans. Smart Grid, vol. 7, no. 5, pp. 2378–2392, Sep. 2016.

![](yu2018_distributed_realtime_dcmg_assets/images/dcb6a5ed8437f050fc4699e7e4052f95ef6be0923ba3eb97f8ccd7c398452bf5.jpg)

Liang Yu (M’16) received the B.S. and M.S. degrees from Yangtze University, Jingzhou, China, in 2007 and 2010, respectively, and the Ph.D. degree in information and communication engineering from the Huazhong University of Science and Technology, Wuhan, China, in 2014. He is currently an Assistant Professor with the Nanjing University of Posts and Telecommunications, Nanjing, China. His current research interests include energy management of cyber-physical systems, such as data centers, smart grids, and intelligent buildings, cloud-fog comput-

ing, and software defined networks. He is the Principal Investigator of four projects funded by the NSFC, the Science and Technology Department of Jiangsu Province, the Education Department of Jiangsu Province, and NJUPT. He has authored or co-authored 20 technical papers in the IEEE journals/conferences, such as the IEEE TRANSACTIONS ON SMART GRID, the IEEE TRANSACTIONS ON PARALLEL AND DISTRIBUTED SYSTEMS, and the IEEE TRANSACTIONS ON CLOUD COMPUTING, and one book in the areas of communications and networks. He was a recipient of the Prize for Outstanding Doctoral Dissertation of Hubei Province in 2015. He served or is serving as a TPC Member or a Reviewer for several journals and conferences, such as IGESC 2016, IECON 2016, WCSP 2016, the IEEE JOURNAL ON SELECTED AREAS IN COMMUNICATIONS, the IEEE TRANSACTIONS ON SUSTAINABLE ENERGY, and the IEEE TRANSACTIONS ON SUSTAINABLE COMPUTING.

![](yu2018_distributed_realtime_dcmg_assets/images/e9874a7ecd476061264dc0b7a40655f84b8183f7cddddc4fe23698368ad93b7b.jpg)

Tao Jiang (M’06–SM’10) received the B.S. and M.S. degrees in applied geophysics from the China University of Geosciences, Wuhan, China, in 1997 and 2000, respectively, and the Ph.D. degree in information and communication engineering from the Huazhong University of Science and Technology, Wuhan, China, in 2004, where he is currently a Distinguished Professor with the School of Electronics Information and Communications. From 2004 to 2007, he researched in some universities, such as Brunel University and the University of

Michigan-Dearborn. He has authored or co-authored over 200 technical papers in major journals and conferences and eight books/chapters in the areas of communications and networks. He was a recipient of the NSFC for Distinguished Young Scholars Award in 2013, the Young and Middle-Aged Leading Scientists, Engineers and Innovators by the Ministry of Science and Technology of China in 2014, and the Most Cited Chinese Researchers in Computer Science announced by Elsevier in 2014. He is currently serving as the Associate Editor-in-Chief for China Communications and an Associate Editor of some technical journals in communications, including the IEEE TRANSACTIONS ON SIGNAL PROCESSING, the IEEE COMMUNICATIONS SURVEYS AND TUTORIALS, the IEEE TRANSACTIONS ON VEHICULAR TECHNOLOGY, and the IEEE INTERNET OF THINGS JOURNAL. He was invited to serve as the TPC Symposium Chair for the IEEE GLOBECOM 2013, the IEEE WCNC 2013, and ICCC 2013. He served as a Symposium TPC Member of some major IEEE conferences, including INFOCOM, GLOBECOM, and ICC.

![](yu2018_distributed_realtime_dcmg_assets/images/befcb662a965e56ebb10cf83401f43b95292a8538984ac94bb310bf3e7e6b122.jpg)

Yulong Zou (SM’13) received the B.Eng. degree in information engineering from the Nanjing University of Posts and Telecommunications (NUPT), Nanjing, China, in 2006, the first Ph.D. degree in electrical engineering from the Stevens Institute of Technology, NJ, USA, in 2012, and the second Ph.D. degree in signal and information processing from NUPT, in 2012, where he is a Full Professor and a Doctoral Supervisor. His research interests span a wide range of topics in wireless communications and signal processing, including the cooperative

communications, cognitive radio, wireless security, and energy-efficient communications. He was a recipient of the 9th IEEE Communications Society Asia-Pacific Best Young Researcher in 2014 and a co-recipient of the Best Paper Award at the 80th IEEE Vehicular Technology Conference in 2014. He is currently serving as an Editor for the IEEE COMMUNICATIONS SURVEYS AND TUTORIALS, the IEEE COMMUNICATIONS LETTERS, the EURASIP Journal on Advances in Signal Processing, IET Communications, and China Communications. He has acted as a TPC Member for various IEEE sponsored conferences, such as the IEEE ICC/GLOBECOM/WCNC/VTC/ICCC.