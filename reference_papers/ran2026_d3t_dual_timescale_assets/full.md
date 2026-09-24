# D3T: Dual-Timescale Optimization of Task Scheduling and Thermal Management for Energy Efficient Geo-Distributed Data Centers

Yongyi Ran , Member, IEEE, Hui Yin, Tongyao Sun, Xin Zhou , Member, IEEE, Jiangtao Luo , Senior Member, IEEE, and Shuangwu Chen

Abstract—The surge of artificial intelligence (AI) has intensified compute-intensive tasks, sharply increasing the need for energyefficient management in geo-distributed data centers. Existing approaches struggle to coordinate task scheduling and cooling control due to mismatched time constants, stochastic Information Technology (IT) workloads, variable renewable energy, and fluctuating electricity prices. To address these challenges, we propose D3T, a dual-timescale deep reinforcement learning (DRL) framework that jointly optimizes task scheduling and thermal management for energy-efficient geo-distributed data centers. At the fast timescale, D3T employs Deep Q-Network (DQN) to schedule tasks, reducing operational expenditure (OPEX) and task sojourn time. At the slow timescale, a QMIX-based multi-agent DRL method regulates cooling across distributed data centers by dynamically adjusting airflow rates, thereby preventing hotspots and reducing energy waste. Extensive experiments were conducted using TRNSYS with real-world traces, and the results demonstrate that, compared to baseline algorithms, D3T reduces OPEX by 13% in IT subsystems and 29% in cooling subsystems, improves power usage effectiveness (PUE) by 7%, and maintains more stable thermal safety across geo-distributed data centers.

Index Terms—Deep reinforcement learning, geo-distributed data center, task scheduling, thermal management.

## I. INTRODUCTION

in data centers, resulting in a dramatic rise in energy consumption. Globally, data centers consumed an estimated 240 to 340 terawatt-hours (TWh) of electricity in 2022, representing approximately 1% to 1.3% of the world’s total electricity demand. This consumption is projected to double by 2026, potentially surpassing 1,000 TWh [1]. In China, the power consumption of approximately 8.1 million in-use standard racks in data centers reached 150 billion kWh in 2023, accounting for 1.6% of the nation’s total electricity use, with projections to grow to approximately 5% by 2030 [2]. At the same time, the Power Usage Effectiveness (PUE) [3] of data centers indicates significant room for improvement. According to the Uptime Institute [4], the global average PUE for data centers in 2024 was 1.56, although many recent builds can achieve a PUE of 1.3 or even lower. Therefore, it is crucial to develop innovative energy-efficient designs and strategies to curb the growing energy impact of data centers.

In modern data centers, compute-intensive tasks (e.g., AI for science, distributed computing, and massive data processing) further exacerbate energy demand due to their high computational intensity, extended operational time, and increased cooling requirements. This also introduces several new critical challenges to improving data center energy efficiency. First, such computing-intensive tasks are not only highly energy-intensive but also thermal-intensive, with their execution potentially disrupting the thermal balance within data centers. As a result, task scheduling must account for not only resource availability and cost but also the thermal impact on facilities to prevent the generation of hotspots. Second, the primary contributors to energy consumption in a data center are the IT subsystem and the cooling subsystem [5], which have mismatched system time constants. Task scheduling in the IT subsystem occurs within seconds or milliseconds, while adjustments in the cooling subsystem, constrained by thermal inertia, typically require minutes (e.g., 15 minutes [5]). This discrepancy in time constants can lead to issues such as hotspots, unnecessary cooling fluctuations, and over-provisioning of cooling capacity. Third, the growing demand for large-scale applications, such as scientific computing [6] and distributed machine learning [7], has driven the deployment of these workloads across geo-distributed data centers rather than a single facility, offering improved scalability, reliability, and availability. Moreover, geo-distributed data centers can leverage the spatiotemporal diversity of renewable energy, thereby reducing operational costs and lowering the carbon footprint. However, their distributed nature introduces high-dimensional and highly dynamic optimization challenges. These challenges stem from the diversity of devices and facilities, as well as the variability of workloads, environmental conditions, and system states over time, further increasing the complexity of achieving energy-efficient operations.

Many efforts have been devoted to improving the energy efficiency of data centers, which can be classified into three categories: task scheduling, thermal management, and joint optimization of both. The existing task scheduling methods can improve resource utilization and reduce costs by reasonably allocating tasks, including heuristic-based [8], [9], [10], model-based [11], [12], and learning-based [13], [14] methods. However, only focusing on task scheduling often overlooks the impact of task computing workloads on the thermal balance of data centers. The existing thermal management methods [15], [16], [17], [18], [19] generally regulate the set-point temperature or airflow rate of Air Cooling Units (ACUs) in data centers, which usually ignore the dynamics of IT subsystems and only consider rack temperature or indoor air temperature for cooling subsystem control. Due to lack of IT- cooling coordination and pursuing safe operation, these prevailing siloed methods often lead to wasteful over-provisioning of cooling capacity or result in local hotspots. Therefore, it is highly desired and more promising to simultaneously optimize task scheduling and thermal management, where many model-based [20], [21] and learning-based [22], [23] approaches are investigated. These existing approaches typically fail to account for the mismatch in system time constants between IT and cooling subsystems. Furthermore, few studies have explored such joint optimization in the context of distributed data centers, leaving a critical gap in the field.

To tackle the above issues, we propose D3T, a dual-timescale joint optimization algorithm for task scheduling and thermal management, aimed at improving energy efficiency and reducing Operational Expenditure (OPEX) in geo-distributed data centers. D3T integrates compute-intensive task scheduling with thermal management to dynamically align cooling demand with supply, addressing the thermal impact of task execution on data centers. By adopting a dual-timescale control paradigm, D3T can mitigate the issue arising from inconsistent time constants. Due to the problem involves multiple geo-distributed data centers, heterogeneous electricity prices, variable renewable energy availability, and fluctuating workloads, D3T leverages advanced DRL to effectively handle such complexity and dynamics. Our major contributions can be summarized as follows.

We propose D3T, a dual-timescale deep reinforcement learning (DRL) framework that jointly optimizes task scheduling and thermal management in geo-distributed data centers. By explicitly modeling the thermal impact of task execution and addressing the mismatched time constants between IT and cooling subsystems, D3T can improve energy efficiency, reduce operational expenditure (OPEX), and prevent hotspots.

\- To tackle the high-dimensional and dynamic nature of geo-distributed data centers, D3T leverages learning-based methods to make sequential control decisions in a dualtimescale control paradigm. Specifically, D3T utilizes

Deep Q-Network (DQN) [24] to perform second- or millisecond-level task scheduling at the fast timescale, while employs QMIX-based multi-agent DRL [25] to achieve minute-level collaborative thermal management across multiple data centers.

\- We design a Python-based task scheduling engine integrated with TRNSYS thermal simulation to evaluate D3T under real-world traces, e.g., compute-intensive workloads [26], electricity prices [27], and renewable energy [28], [29]. The experimental results illustrate that, compared to the baseline algorithms, D3T can significantly reduce the OPEX of IT and cooling subsystems by up to 13% and 29%, respectively, improve PUE by 7%.

## II. RELATED WORK

In this section, we review related work on optimizing energy efficiency and OPEX in data centers, with a focus on three key aspects: task scheduling, thermal management, and the joint optimization of task scheduling and thermal management.

## A. Task Scheduling in Data Centers

The goal of task scheduling is to efficiently allocate tasks to appropriate data center servers. Task scheduling methods that address energy efficiency and OPEX can be generally classified into three categories: heuristic-based, model-based, and learning-based approaches.

Heuristic-based methods aim to quickly find solutions using predefined rules or experiential knowledge. For instance, Mohammadzadeh et al. [10] developed a hybrid multi-objective metaheuristic optimization algorithm for scientific workflow scheduling, which aims to jointly minimize makespan, energy consumption, and execution cost, thereby improving overall scheduling efficiency. Model-based approaches optimize task scheduling by constructing mathematical models to describe system behavior. To improve the energy efficiency of Building Energy Systems integrated with Photovoltaic (PV) systems, Qian et al. [11] proposed a scheduling method based on multitask model predictive control (MPC), which effectively reduced energy costs. Wei et al. [12] employed a Task Scheduling Strategy based on Queuing Networks, enhancing both task offloading efficiency and overall system performance. Learning-based approaches combine AI techniques with optimization algorithms to adapt to dynamic system environments and improve decisionmaking based on continuous feedback and data-driven insights. Mangalampalli et al. [13] optimizes resource allocation by employing a Deep Reinforcement Learning-Based Task Scheduling Algorithm. Cheng et al. [14] proposed an improved Asynchronous Advantage Actor-Critic algorithm for dynamic task scheduling in the edge-cloud collaborative computing systems, minimizes response time and energy consumption.

These siloed task scheduling approaches typically focus on optimizing IT resources without considering their close interconnection with the cooling system. Without dynamic coordination between task scheduling and cooling, the cooling system may overoperate or underperform, causing unnecessary energy consumption or inefficient heat dissipation. This misalignment can lead to higher energy costs, reduced system performance, and potential hardware damage due to thermal stress, ultimately undermining overall energy efficiency and system reliability.

## B. Thermal Management in Data Centers

Effective thermal management reduces cooling energy consumption and enhances system stability. Widely used control methods include Proportional-Integral-Derivative (PID), MPC, and Deep Reinforcement Learning (DRL).

Wang et al. [15] applied PID to HVAC systems, enhancing efficiency by dynamically adjusting component parameters. By forecasting future load and electricity price fluctuations based on MPC, Zhao et al. [16] dynamically adjusted and optimized the control strategies of the cooling system, thereby improving efficiency. Xin et al. [17] employed the Distributed MPC approach, significantly improved energy efficiency and overall system performance by adjusting parameters of cooling system. Wang et al. [18] employed Multi-Agent Deep Deterministic Policy Gradient (MADDPG) to reduce overall cooling system consumption by adjusting each tenants’ supply air temperatures. He et al. [19] combined DRL with Long Short-Term Memory (LSTM) networks to predict future system states and improve energy efficiency.

These siloed thermal management may lead to inefficiencies in both energy consumption and cooling control. The cooling subsystem might either overcompensate or undercompensate based on inaccurate assumptions about the IT load, causing either excessive cooling or inadequate heat dissipation. This lack of coordination can result in wasted energy, higher operational costs, and potential thermal instability, as the cooling system may not respond dynamically to fluctuations in workload. In addition, the thermal inertia issue can lead to delayed cooling adjustments, which also may result in the same results.

## C. Joint Optimization in Data Centers

As analyzed above, these siloed approaches have obvious defects, so it is highly desired and more promising to jointly optimize task scheduling and thermal management.

Pakbaznia et al. [30] develops an Integer Linear Programming (ILP) model to minimize power consumption in data centers by optimizing cooling temperature and task assignment to servers with appropriate voltage-frequency levels. Zhou et al. [31] studied the Machine Learning (ML) methods in cooling power modeling and cabinet temperature modeling, proposed an energy management scheme, reduces the total energy consumption of data centers. MirhoseiniNejad et al. [32] propose a low-complexity data center model that accounts for thermal interactions between IT and cooling units, optimizing both cooling parameters and workload scheduling to achieve significant energy savings. Ran et al. [5] proposed an optimization framework based on DRL, named DeepEE, which can jointly optimize the job scheduling of IT systems and air flow rate adjustment of cooling systems. Chi et al. [33] use a hybrid AC-DDPG multi-agent approach to address high-dimensional state spaces and large-scale action spaces, achieving a better balance between energy saving and service quality.

These joint optimization methods can effectively address the interdependencies between the IT and cooling subsystems. However, they still have some drawbacks. First, they often assume identical response times for both subsystems, overlooking their distinct dynamics. This assumption can lead to poor coordination and increased thermal risks. Second, joint optimization typically involves high-dimensional, highly dynamic states and a large-scale action space, which results in significant computational demands and may even encounter the ”curse of dimensionality,” especially in the case of geo-distributed data centers. To better highlight the differences between our proposed method and existing studies, we provide a comparative summary of the reviewed literature in Table I.

## III. SYSTEM ARCHITECTURE AND PROBLEM FORMULATION

In this section, we introduce the system architecture, and then formulate the optimization problem.

## A. System Architecture

As illustrated in Fig. 1, we consider a scenario for our proposed D3T approach involving multiple geo-distributed data centers, which contains the following components.

IT Subsystem: In geo-distributed data centers, the IT subsystem of each data center contains numerous rows of racks housing a large number of servers, storage devices, and networking equipment, working collectively to execute computational tasks. The IT subsystem requires significant power to operate and generates substantial heat during processing, necessitating effective thermal management to ensure optimal performance and prevent hardware degradation. As a result, optimizing IT task scheduling becomes essential for enhancing data center performance while improving energy efficiency and reducing operational costs.

Cooling Subsystem: The cooling subsystem is to dissipate the heat generated by servers and other equipment, ensuring reliable and efficient operation. It primarily consists of Electric Chiller (EC), Cooling Tower (CT), Chilled Water Pump (CHWP), Condensing Water Pump (CWP), and Computer Room Air Conditioning (CRAC) units. The EC generates chilled water for the data center, the CHWP and CWP circulate chilled and cooling water, respectively, while the CT dissipates heat through evaporation via the interaction between water and air. CRAC units supply cooled air to the cold aisle of the data center, which play a critical role, as their adjustable airflow rates and set-point temperatures, in providing precise control over cooling efficiency. In distributed data centers, cooling subsystems in multiple sites can collaboratively manage heat loads, improving overall energy efficiency. In this paper, we consider air-cooled data centers that implement cold aisle containment.

DRL Engine, Task Scheduler and Cooling Controller: In this paper, the DRL Engine is designed to provide control strategies for task scheduling and thermal management. After a computational task is submitted, it initially enters the task arrival queue. The Task Scheduler then processes tasks following the first-infirst-out principle, using scheduling strategies provided by the DRL engine. For thermal management, each data center features a dedicated Cooling Controller, which implements strategies from the DRL engine to regulate the airflow rate of the CRAC units.

TABLE I  
COMPARATIVE SUMMARY OF REVIEWED LITERATURE AND THE PROPOSED METHOD

<table><tr><td>References</td><td>Optimization Method</td><td>Task Scheduling</td><td>Thermal Mgmt.</td><td>Multi-Energy Sources</td><td>Timescale Coordination</td><td>Distributed DCs</td></tr><tr><td>[10]</td><td>MHA</td><td>√</td><td>×</td><td>×</td><td>×</td><td>×</td></tr><tr><td>[11]</td><td>MPC</td><td>√</td><td>×</td><td>×</td><td>×</td><td>√</td></tr><tr><td>[12]</td><td>Queue Model</td><td>√</td><td>×</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[13]</td><td>DQN</td><td>√</td><td>×</td><td>×</td><td>×</td><td>√</td></tr><tr><td>[14]</td><td>A3C</td><td>√</td><td>×</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[15]</td><td>PID</td><td>×</td><td>√</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[16]</td><td>MPC</td><td>×</td><td>√</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[17]</td><td>Distributed MPC</td><td>×</td><td>√</td><td>√</td><td>√</td><td>√</td></tr><tr><td>[18]</td><td>MADDPG</td><td>×</td><td>√</td><td>×</td><td>×</td><td>×</td></tr><tr><td>[19]</td><td>DRL + LSTM</td><td>×</td><td>√</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[30]</td><td>ILP + heuristic</td><td>√</td><td>√</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[31]</td><td>ML</td><td>√</td><td>√</td><td>×</td><td>×</td><td>×</td></tr><tr><td>[32]</td><td>Thermal-aware Model</td><td>√</td><td>√</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[5]</td><td>PADQN-D</td><td>√</td><td>√</td><td>×</td><td>√</td><td>×</td></tr><tr><td>[33]</td><td>AC-DDPG</td><td>√</td><td>√</td><td>×</td><td>√</td><td>×</td></tr><tr><td>Proposed</td><td>Dual-scale DRL (DQN + QMIX)</td><td>√</td><td>√</td><td>√</td><td>√</td><td>√</td></tr></table>

![](images/f567b3c3721ecbb8507bfb0201ae3238430ae5b168636cd13a4e97dd711a5574.jpg)  
Fig. 1. Overview of the system architecture. The architecture consists of multiple geo-distributed data centers, each with an IT subsystem and a cooling subsystem. A DRL engine provides optimal strategies for task scheduling and thermal management, which are executed by the Task Scheduler and Cooling Controller.

## B. System Models

1) Dual-Timescale Control Framework: Typically, the primary contributors to energy consumption in a data center are the IT subsystem and the cooling subsystem [5], thus we mainly focus on optimizing the energy efficiency of these two subsystems in this paper.

However, the time constants for task scheduling in the IT subsystem and thermal management in the cooling subsystem are mismatched. For task scheduling, in order to minimize the waiting time of computational tasks and enhance sensitivity to electricity prices and available renewable energy, the task scheduler must respond rapidly, typically within seconds or milliseconds [34]. In contrast, for thermal management, the cooling controller periodically adjusts the CRAC unit (e.g., airflow or set-point temperature) to regulate cooling capacity and ensure equipment safety. However, frequent adjustments to the CRAC unit can shorten its lifespan, and due to thermal inertia (e.g., it takes around 15 minutes to reach thermal equilibrium [35]), mismatches between cooling capacity and thermal load may occur, leading to overcooling or hotspots. If periodic control with a fixed timescale is employed, it may fail to effectively regulate these two subsystems, resulting in unnecessary actions or delayed responses.

![](images/2bdca74df2816f49b21218a57a9f7092faf6d53bf42817ee78fbdee85e69cf7a.jpg)  
Fig. 2. Illustration of dual-timescale control paradigm.

To address this issue, we introduce a dual-timescale control paradigm [36] to jointly regulate the task scheduling and thermal management, which is a nested scheduling structure under a unified global timeline. Within this structure, the slowtimescale thermal control sets the operational context, while the fast-timescale task scheduler operates within—and subsequently influences—this context, ensuring coordinated and efficient decision-making across timescales. As shown in Fig. 2, each workday is divided into N slow time slots, each with a duration of l. Furthermore, each slow time slot is subdivided into M fast time slots, each with a duration of d. For the fast timescale control, at the beginning of each fast time slot $m \in$ $\{ 1 , 2 , . . . , M \}$ , we employ a DRL technique called DQN [5] to provide centralized control strategies for the Task Scheduler. For the slow timescale control, At the beginning of each slow time slot $n \in \{ 1 , 2 , . . . , N \}$ , we utilize a MADRL technique called QMIX [25] to offer decentralized control strategies for the Cooling Controller in each data center. It is important to note that both the fast-timescale task scheduling and the slow-timescale thermal management are governed by periodic triggers, not event-driven mechanisms.

Given that the time interval d of the fast time slot $m \in$ $\{ 1 , 2 , . . . , M \}$ is very short, it is assumed that the system state remains constant throughout the m-th fast time slot. In this paper, the system state is periodically collected at the beginning of each fast time slot $\tau \in \{ 1 , 2 , . . . , M \times N \}$ , where $M \times N$ denotes the total number of fast time slots.

2) IT Subsystem Model: Let C be the set of data centers scattered in different geographical locations, and $\mathbf { V } ^ { c }$ be the set of severs inside the data center $c ( c \in \mathbf { C } )$ . For each server $v ^ { c } \in \mathbf { V } ^ { c }$ <sup>( )</sup>the power consumption can be modeled as the following (1), which is widely used in data centers [5].

$$
P ^ {v ^ {c}} (\tau) = P _ {\mathrm{idle}} + \left(P _ {\mathrm{full}} - P _ {\mathrm{idle}}\right) \times \varphi^ {v ^ {c}} (\tau)\tag{1}
$$

where $P _ { \mathrm { i d l e } }$ and $P _ { \mathrm { f u l l } }$ represent the idle and peak power of the server, $\varphi ^ { v ^ { c } }$ is the CPU utilization of the server $v ^ { c }$

Thus, the total energy consumption of the IT subsystem in c during the fast time slot τ can be expressed as:

$$
E _ {\mathrm{IT}} ^ {c} (\tau) = P _ {\mathrm{IT}} ^ {c} (\tau) \times d = \sum_ {v ^ {c} \in \mathbf {V} ^ {c}} P ^ {v ^ {c}} (\tau) \times d\tag{2}
$$

Note: The power consumption of servers with GPUs can also be modeled similarly to the Formula (1), as expressed in [37]. For simplicity, here we consider only the CPUs.

3) Cooling Subsystem Model: Electric Chiller (EC): In the data center $c \in \{ 1 , 2 , \ldots , C \}$ , the power consumption of EC $P _ { \mathrm { E C } } ^ { c } [ \mathrm { k J h } ^ { - 1 } ]$ during the fast time slot τ can be represented as [38]:

$$
P _ {\mathrm{EC}} ^ {c} (\tau) = \frac {Q _ {\mathrm{EC}} ^ {c} (\tau)}{\mathrm{COP} _ {\mathrm{EC}} ^ {c}}\tag{3}
$$

where ${ \mathrm { C O P } } _ { \mathrm { E C } } ^ { c }$ is the Coefficient Of Performance (COP) of the EC, and $Q _ { \mathrm { E C } } ^ { c } [ \mathrm { W a t t s } ]$ denotes the cooling output of the EC, which can be described as:

$$
Q _ {\mathrm{EC}} ^ {c} (\tau) = m _ {\mathrm{EC}} (\tau) \times c _ {p} \times \left(T _ {\mathrm{EC}, c} ^ {\mathrm{in}} (\tau) - T _ {\mathrm{EC}, c} ^ {\mathrm{out}} (\tau)\right)\tag{4}
$$

where $m _ { \mathrm { E C } } [ \mathrm { k g } \cdot \mathrm { h } ^ { - 1 } ]$ is refrigerant mass flow rate, $c _ { p } [ \mathrm { k J \cdot k g ^ { - 1 } }$ $\mathrm { K } ^ { - 1 } ]$ <sup>[kg h ] [kJ kg</sup>is the specific heat capacity of water at constant pressure, <sup>K</sup>and ${ \bar { T } } _ { \mathrm { E C } , c } ^ { \mathrm { i n } } [ ^ { \circ } C ]$ is the refrigerant inlet temperature to evaporator, $T _ { \mathrm { E C } , c } ^ { \mathrm { o u t } } [ ^ { \circ } C ]$ is the refrigerant evaporator exit temperature.

Cooling Tower (CT): The power consumption of CT $P _ { \mathrm { C T } } ^ { c } [ \mathrm { k J h ^ { - 1 } } ]$ can be estimated based on the frequency of the <sup>[kJh</sup>fan [39]:

$$
P _ {\mathrm{CT}} ^ {c} (\tau) = P _ {\mathrm{CT,base}} \times (R _ {\mathrm{CT}} (\tau) / R _ {\mathrm{CT,base}}) ^ {3}\tag{5}
$$

where $P _ { \mathrm { C T } , b a s e } [ k W ]$ is the idle power consumption of the cooling tower fan, $R _ { \mathrm { C T } } [ H z ]$ is the rotational speed of the cooling tower fan during τ , $R _ { \scriptscriptstyle \mathrm { C T } , b a s e } \big [ H z \big ]$ is the rated speed of the cooling tower fan.

Pumps: Let $\mathbb { U } ^ { c }$ be the set of pumps in the data center c. Based on flow-lift characteristics [39], the power of pump $u ( u \in \mathbb { U } ^ { c } )$ can be described as $P _ { \mathrm { p u m p } } ^ { u , c } [ \mathrm { k J h ^ { - 1 } } ]$ :

$$
P _ {\text { pump }} ^ {u, c} (\tau) = \frac {\gamma^ {u} (\tau) \times H ^ {u} \times \rho_ {\text { water }} \times g _ {g r a}}{\eta_ {\text { pump }}}\tag{6}
$$

where $\gamma ^ { u } [ \mathrm { m ^ { 3 } \cdot s ^ { - 1 } } ]$ is the flow rate of $u , H ^ { u } [ \mathrm { m } ]$ is the head of pump $u , \rho _ { \mathrm { w a t e r } } [ \mathrm { k g } \cdot \mathrm { m } ^ { - 3 } ]$ is the density of water, $g _ { g r a } [ m / s ^ { 2 } ]$ is the gravitational acceleration, and $\eta _ { \mathrm { p u m p } }$ indicates the pump efficiency.

CRAC Units: Let $\varpi ^ { c }$ be the set of racks and $\mathbb { B } ^ { c }$ be the set of CRAC units in the data center c. The set-point temperature and airflow rate of CRAC $r ( r \in \mathbb { B } ^ { c } )$ is $T _ { \mathrm { s u p } } ^ { r }$ and $f ^ { r } [ \mathrm { m } ^ { \hat { 3 } } \cdot \mathrm { s } ^ { - 1 } ]$ . The <sup>sup</sup>providing air flow rate subject to the following condition:

$$
0 \leq f ^ {r} \leq f ^ {m a x}\tag{7}
$$

which $f ^ { m a x }$ is the maximum providing air flow rate of CRAC units.

The cooling that r supplies to c can be represented as:

$$
Q _ {\mathrm{sup}} ^ {r} (\tau) = \rho_ {\mathrm{air}} \vartheta_ {p} f ^ {r} (\tau) T _ {\mathrm{sup}} ^ {r} (\tau)\tag{8}
$$

where $\rho _ { \mathrm { a i r } } [ \mathrm { k g \cdot m ^ { - 3 } } ]$ indicates the air density and $\vartheta _ { p } [ \mathrm { J } \cdot { } ^ { \circ } \mathrm { C } ^ { - 1 }$ $\mathrm { k g ^ { - 1 } } ]$ denotes the specific heat capacity of air.

The power consumption of CRAC unit r can be described as $P _ { \mathrm { C R A C } } ^ { r } [ \mathrm { J h ^ { - 1 } } ]$ [40]:

$$
P _ {\mathrm{CRAC}} ^ {r} (\tau) = \frac {Q _ {\mathrm{rem}} ^ {r} (\tau)}{\mathrm{COP} _ {\mathrm{CRAC}} (T _ {\mathrm{sup}} ^ {r} (\tau))}\tag{9}
$$

where $Q _ { \mathrm { r e m } } ^ { r } ( \tau )$ denotes the heat that needs to be eliminated by r, and $\mathrm { C O P } _ { \mathrm { C R A C } }$ is a monotone increasing function [41] related to the supply air temperature $T _ { \mathrm { s u p } } ^ { r }$ of CRAC unit $r .$

The cooled airflow from the CRAC unit flows through the cold aisle, passes through the racks, carries away the heat generated by the servers, and ultimately returns to the CRAC unit. The rate of the airflow, which enters rack $i ( i \in \varpi ^ { c } )$ and returns to CRAC units, can be expressed as:

$$
f _ {\text { ret }} ^ {i, r} = \gamma_ {i r} \left(1 - \sum_ {j \in \varpi^ {c}} \gamma_ {i j}\right) f _ {\text { in }} ^ {i}\tag{10}
$$

where $\gamma _ { i r }$ is the proportion of airflow recirculated from rack i to CRAC unit r and $\gamma _ { i j }$ is the proportion of airflow recirculated from rack i to $j , f _ { \mathrm { i n } } ^ { i } [ \mathrm { \bar { m } ^ { 3 } s ^ { - 1 } } ]$ represents the rate of the airflow entering rack i.

Thus, the heat returned to CRAC unit r from all racks is:

$$
Q _ {\mathrm{ret}} ^ {r} (\tau) = \rho_ {\mathrm{air}} \vartheta_ {p} \sum_ {i \in \varpi^ {c}} \left[ \left(1 - \sum_ {j \in \varpi^ {c}, j \neq i} \gamma_ {i j}\right) f _ {\mathrm{in}} ^ {i} T _ {\mathrm{out}} ^ {i} (\tau) \right]\tag{11}
$$

where $T _ { \mathrm { o u t } } ^ { i } [ ^ { \circ } C ]$ indicates the outlet temperature of rack i.

Therefore, the amount of heat that CRAC unit r must remove in $\tau$ is expressed as:

$$
Q _ {\mathrm{rem}} ^ {r} (\tau) = Q _ {\mathrm{ret}} ^ {r} (\tau) - Q _ {\mathrm{sup}} ^ {r} (\tau)\tag{12}
$$

The power consumption of the cooling subsystem of the data center c can be expressed as:

$$
\begin{array}{c} P _ {\text {cooling}} ^ {c} (\tau) = P _ {\text {EC}} ^ {c} (\tau) + P _ {\text {CT}} ^ {c} (\tau) \\ + \sum_ {u \in \mathbb {U} ^ {c}} P _ {\text {pump}} ^ {u} (\tau) + \sum_ {r \in \mathbb {B} ^ {c}} P _ {\text {CRAC}} ^ {r} (\tau) \end{array}\tag{13}
$$

We use $E _ { \mathrm { c o o l i n g } } ^ { c } ( \tau ) = P _ { \mathrm { c o o l i n g } } ^ { c } ( \tau ) \times d$ to denote the energy <sup>( ) = ( )</sup>consumption of the cooling subsystem in data center c over the fast time slot τ .

4) Task Model: In this paper, we assume that all the requested tasks are compute-intensive, thus the number of required CPU processors will be employed as the key factor for dispatching a task to an appropriate data center. The submitted tasks are first placed in an arrival queue, as shown in Fig. 1. At the beginning of each fast time slot $\tau ,$ the Task Scheduler selects a candidate task based on the First-In-First-Out (FIFO) principle and dispatches it to an appropriate data center according to the scheduling strategies generated by the DRL Engine. Each submitted task contains information such as the job index, submission time, requested number of processors, and requested computing time. Thus, a task $g$ can be represented as a quadruple $J _ { g } = ( i _ { g } , b _ { g } , u _ { g } , v _ { g } )$ where $i _ { g }$ is the task index, $b _ { g }$ indicates the submission time of task $g , u _ { g }$ denotes the requested number of processors for task $g , v _ { g }$ is the requested computing time.

Due to the limited computing resources of a data center, the following condition must be satisfied before a task can be assigned successfully to the data center c:

$$
u _ {g} \leq u ^ {c}, c \in \{1, 2, \dots , | \mathbf {C} | \}\tag{14}
$$

where $u ^ { c }$ is the total available number of processors inside the data center c.

The task g begins to be executed at time $\boldsymbol { e } _ { g } .$ The sojourn time of $g$ can be calculated as:

$$
w _ {g} = e _ {g} - b _ {g}\tag{15}
$$

It is assumed that the number of arrived tasks during the fast time slot τ is denoted as $| G ( \tau ) |$ |. The average sojourn time of all tasks can be described as:

$$
W _ {s} = \frac {1}{\sum_ {\tau = 1} ^ {M \times N} | G (\tau) |} \sum_ {\tau = 1} ^ {M \times N} \sum_ {g \in G (\tau)} w _ {g}\tag{16}
$$

where $M \times N$ represents the total number of fast time slots.

5) OPEX Model: In this paper, we assume that each data center is powered by a variety of energy sources, including grid power, solar power, and wind power, with a preference for on-site renewable energy, i.e., solar and wind power. Use the grid only when necessary to ensure the normal and smooth operation of the data centers.

Because grid prices and renewable energy prices vary across geographies, we defined a composite energy price to assess the Operational Expenditure (OPEX) of performing tasks in geographically distributed data centers. Herein the composite energy price for data center c during the time slot τ is defined as:

$$
p ^ {c} (\tau) = \omega_ {g} ^ {c} (\tau) p _ {g} ^ {c} (\tau) + \omega_ {s} ^ {c} (\tau) p _ {s} ^ {c} (\tau) + \omega_ {w} ^ {c} (\tau) p _ {w} ^ {c} (\tau)\tag{17}
$$

where $p _ { g } ^ { c } ( \tau ) , p _ { s } ^ { c } ( \tau )$ , and $p _ { w } ^ { c } ( \tau )$ represent the electricity price of grid, solar, and wind, respectively. $\omega _ { g } ^ { c } ( \tau ) , \omega _ { s } ^ { c } ( \tau )$ , and $\omega _ { w } ^ { c } ( \tau )$ represent the percentage of grid, solar energy and wind energy consumption, respectively, which can be defined as follows:

$$
\begin{array}{l} \omega_ {g} ^ {c} (\tau) = \frac {\max \left(\left(E _ {\mathrm{IT}} ^ {c} (\tau) + E _ {\text { cooling }} ^ {c} (\tau) - E _ {s} ^ {c} (\tau) - E _ {w} ^ {c} (\tau)\right) , 0\right)}{E _ {\mathrm{IT}} ^ {c} (\tau) + E _ {\text { cooling }} ^ {c} (\tau)} \\ \omega_ {s} ^ {c} (\tau) = \min \left(\frac {E _ {s} ^ {c} (\tau)}{E _ {\mathrm{IT}} ^ {c} (\tau) + E _ {\text { cooling }} ^ {c} (\tau)}, 1\right) \\ \omega_ {w} ^ {c} (\tau) = \max \left((1 - \omega_ {g} ^ {c} (\tau) - \omega_ {s} ^ {c} (\tau)), 0\right) \end{array} \tag {18}
$$

where $E _ { s } ^ { c } ( \tau )$ and $E _ { w } ^ { c } ( \tau )$ represent the available renewable en-<sup>( ) ( )</sup>ergy for the fast time slot τ in the data center c [42]:

$$
E _ {s} ^ {c} (\tau) = \kappa^ {c} A ^ {c} I ^ {c} (\tau) d\tag{19}
$$

$$
E _ {w} ^ {c} (\tau) = \frac {1}{2} \eta^ {c} \zeta^ {c} \rho_ {\mathrm{air}} (q ^ {c} (\tau)) ^ {3} d\tag{20}
$$

where $\kappa ^ { c }$ represents the conversion efficiency of solar-toelectricity, $\mathring { A ^ { c } } [ \mathrm { m } ^ { 2 } ]$ represents the active irradiation area of solar panels, $I ^ { c } [ \mathrm { W \cdot m ^ { \frac { \cdot } { \mu } } } ]$ represents the solar irradiance, $\eta ^ { c }$ represents the the conversion efficiency of wind-to-electricity, $\zeta ^ { c } [ \mathrm { m } ^ { 2 } ]$ represents the the rotor area of wind turbines, $\rho _ { \mathrm { a i r } } [ \mathrm { k g \cdot m ^ { - 3 } } ]$ <sup>[m ]</sup><sub>and</sub> $q ^ { c } [ \mathrm { m } \cdot \mathrm { s ^ { - 1 } } ]$ indicate air density and wind speed, d represents the length of τ .

The OPEX of the IT subsystem and the cooling subsystem in data center c during the fast time slot τ can be defined as:

$$
\Gamma_ {\mathrm{IT}} = \frac {1}{M \times N} \sum_ {\tau = 1} ^ {M \times N} \sum_ {c \in \mathbf {C}} E _ {\mathrm{IT}} ^ {c} (\tau) \times p _ {g} ^ {c} (\tau)\tag{21}
$$

$$
\Gamma_ {\mathrm{cooling}} = \frac {1}{M \times N} \sum_ {\tau = 1} ^ {M \times N} \sum_ {c \in \mathbf {C}} E _ {\mathrm{cooling}} ^ {c} (\tau) \times p _ {g} ^ {c} (\tau)\tag{22}
$$

In addition, in order to smooth out OPEX fluctuations over a short period of time, we calculate the average composite energy price of the last K time slots as the OPEX state of data center c for the current fast time slot $\tau { : }$ :

$$
\bar {p} ^ {c} (\tau) = \frac {1}{K} \sum_ {z = \tau - K + 1} ^ {\tau} p ^ {c} (z)\tag{23}
$$

6) PUE Model: Power Usage Effectiveness (PUE) is a commonly used metric employed to measure the energy efficiency of a data center. It is defined as the ratio of the total energy consumed by the data center (including cooling, lighting, power systems, and other infrastructure) to the energy consumed by the IT equipment (servers, storage devices, networking hardware) alone. Since IT and cooling subsystems account for the majority of data center energy consumption, here PUE is simplified as:

$$
\mathrm{PUE} ^ {\mathrm{c}} = \frac {1}{\mathrm{M} \times \mathrm{N}} \sum_ {\tau = 1} ^ {\mathrm{M} \times \mathrm{N}} \frac {\mathrm{E} _ {\mathrm{IT}} ^ {\mathrm{c}} (\tau) + \mathrm{E} _ {\text {cooling}} ^ {\mathrm{c}} (\tau)}{\mathrm{E} _ {\mathrm{IT}} ^ {\mathrm{c}} (\tau)}\tag{24}
$$

The average PUE of $C$ geo-distributed data centers can be calculated as:

$$
\mathrm{PUE} = \frac {1}{| \mathbf {C} |} \sum_ {\mathrm{c} \in \mathbf {C}} \mathrm{PUE} ^ {\mathrm{c}}\tag{25}
$$

## C. Problem Formulation

In order to minimize the OPEX, task waiting time as well as PUE, we design a two-level decision-making in this paper. In the upper level, the task scheduler assigns a candidate task to a suitable data center $c .$ For the assignment policy of tasks within a data center, we refer to the corresponding algorithm in [43]. In the lower level, the cooling controllers in every data center dynamically adjust the airflow rate of CRAC units.

Let $\vec { f } = \langle f ^ { 1 } , f ^ { 2 } , . . . , f ^ { | \mathbf { C } | } \rangle$ represent the airflow rate settings determined by the cooling controllers, where each $f ^ { c } =$ $\langle f _ { 1 } ^ { c } , f _ { 2 } ^ { c } , . . . , f _ { | \mathbb { B } ^ { c } | } ^ { c } \rangle , c \in \mathbb { B } ^ { c }$ specifies the airflow rate settings $f ^ { r }$ <sup>1 2</sup>for individual CRAC unit r in data center c.

Therefore, the joint optimization can be described as:

$$
\begin{array}{r l} \min _ {c, \vec {f}} \Omega & = \mu_ {1} \Gamma_ {\mathrm{IT}} + \mu_ {2} \Gamma_ {\text { cooling }} + \mu_ {3} W _ {s} \\ & + \mu_ {4} \text { PUE } + \mu_ {5} \Xi \\ & s. t. \quad (7) (1 4) \end{array}\tag{26}
$$

where $\mu _ { 1 } { - } \mu _ { 5 }$ are weight factors, is the penalty for data centers overheating, which are defined as:

$$
\Xi = \frac {1}{M \times N} \sum_ {\tau = 1} ^ {M \times N} \sum_ {c \in \mathbf {C}} \ln \left(1 + \exp \left(T ^ {c} (\tau) - \psi^ {T}\right)\right)\tag{27}
$$

where $T ^ { c } ( \tau )$ denotes the temperature of data center c at $\tau .$ $\psi ^ { T }$ denotes the safe temperature threshold for data center. The elements in formula (26) are normalized in our experiments.

## IV. PROPOSED DRL-BASED JOINT OPTIMIZATION ALGORITHM

To solve the above problem, we propose the D3T algorithm to jointly regulate task scheduling and thermal management for geo-distributed data centers. In D3T, a dual-timescale control paradigm is proposed to address the mismatch in time constants between the IT subsystem and the cooling subsystem.

## A. Fast Timescale Control via DQN

In the fast timescale, D3T utilizes Deep Q-Networks (DQN) to perform second- or millisecond-level task scheduling decisions.

1) Model the Task Scheduling Problem as an MDP: We model the task scheduling problem as a Markov Decision Process (MDP), the key elements of the MDP are represented as follows.

State space: The state vector of our considered geo-distributed data centers is defined as $S = < s _ { \mathrm { q } } , s ^ { 1 } , \ldots , s ^ { | \mathbf { C } | } > . ~ s _ { \mathrm { q } } = <$ $u _ { g } , q _ { l e n } >$ is the state of the arrival queue, where $u _ { g }$ denotes the number of required processors of the current candidate task, $q _ { l e n }$ is the current length of the arrival queue. $s ^ { c }$ is expressed as $s ^ { c } = < \phi ^ { c } , u ^ { c } , \bar { p } ^ { c } , f ^ { c } , T ^ { c } >$ , where $\phi ^ { c } = \{ \varphi ^ { v ^ { c } } \mid v ^ { c } \in \mathbf { V } ^ { c } \}$ <sup>= ¯</sup>denotes the utilization of servers in $c , u ^ { c }$ is the total available number of processors inside the data center $c , \bar { p } ^ { c }$ is the OPEX of data center $c , f ^ { c }$ <sup>¯</sup>denotes the set of airflow rate settings for all CRAC units in data center $c ,$ and $T ^ { c }$ indicates the temperature of c.

Action space: The task scheduler is to allocate a candidate task to an appropriate data center $k ,$ thus the corresponding action can be expressed as $k \in \{ 1 , 2 , \ldots , | \mathbf { C } | \}$

Reward function: The reward function $R ^ { f a s t }$ is defined as:

$$
R ^ {f a s t} = \xi_ {1} - \mu_ {1} \Gamma_ {\mathrm{IT}} - \mu_ {2} \Gamma_ {\mathrm{cooling}} - \mu_ {3} W _ {s}\tag{28}
$$

where $\xi _ { 1 }$ is a positive value to ensure the reward is positive. $\mu _ { 1 } .$ $\mu _ { 2 } .$ , and $\mu _ { 3 }$ are the weight factors. The elements in formula (28) are normalized in our experiments.

2) Solving MDP With DQN: Traditional Q-learning methods store action-value functions $Q ( s , a )$ in a Q-table, which becomes infeasible in high-dimensional state and action spaces due to extensive memory requirements and slow convergence. To overcome these limitations, DQN [24] employ Deep Neural Networks (DNNs) to approximate the Q-function, replacing explicit table-based storage.

DQN enhances learning stability through two essential techniques: (1) a target network $Q ^ { \prime }$ with parameters $\phi ^ { \prime }$ that calculates target Q-values separately from the main Q-network $Q$ with parameters φ; and (2) experience replay, which alleviates temporal correlations by storing and randomly sampling past transitions. In our task scheduling framework, we define the online and target networks as $Q _ { s c d }$ and $Q _ { s c d } ^ { \prime } .$ , respectively.

The computation of the target value $y _ { m }$ follows:

$$
y _ {m} = \left\{ \begin{array}{l l} R _ {m} ^ {\text { fast }}, & S _ {m + 1} \text {   is   terminal } \\ R _ {m} ^ {\text { fast }} + \gamma \max _ {k _ {m + 1}} Q ^ {\prime} (S _ {m + 1}, k; \phi^ {\prime}), & \text { otherwise } \end{array} \right.\tag{29}
$$

where $R _ { m } ^ { f a s t }$ denotes the immediate reward, and $\gamma$ represents the discount factor.

The network is trained by minimizing the loss function:

$$
L (\phi) = \frac {1}{B} \sum_ {i = 1} ^ {B} \left(y _ {i} - Q _ {s c d} (S _ {i}, k _ {i}; \phi)\right) ^ {2}.\tag{30}
$$

The parameter updates for $\phi$ employ stochastic gradient descent:

$$
\phi \leftarrow \phi - \alpha \nabla_ {\phi} L (\phi),\tag{31}
$$

where α is the learning rate.

To ensure stable training, the target network parameters $\phi ^ { \prime }$ are updated periodically by gradually adjusting towards the main network:

$$
\phi^ {\prime} \leftarrow \lambda \phi + (1 - \lambda) \phi^ {\prime},\tag{32}
$$

where λ is a small update coefficient.

## B. Slow Timescale Control via MARL

In the slow timescale, D3T employs QMIX [25], a type of multi-agent deep reinforcement learning (MADRL) method, to achieve minute-level collaborative thermal management across multiple data centers.

1) Model the Thermal Management Problem as a Markov Game: We model the thermal management problem of geo-distributed data centers as a Markov Game $\langle \mathcal { E } , S , \mathbf { O } , \mathcal { U } , R ^ { s l o w } , p , \gamma \rangle$ . Each data center is treated as an agent, denoted as $\mathcal { E } = \{ e _ { 1 } , e _ { 2 } , \ldots , e _ { | \mathbf { C } | } \}$ . is the global state space, which is the same as the state space in Section IV-A. The observation profile is the joint observation of all agents $\vec { \cal O } = \left( o ^ { e _ { 1 } } , . . . , o ^ { e _ { | { \bf C } | } } \right)$ , and all possible $\vec { o }$ constitute $\mathbf { O } = O _ { 1 } \times$ ${ \cal O } _ { 2 } \times \cdots \times { \cal O } _ { | \bf { C } | }$ The action profile is the joint action of all agents $\vec { f } = \langle f ^ { 1 } , . . . , f ^ { | \mathbf { C } | } \rangle$ , and all possible $\vec { f }$ constitute $\mathcal { U } =$ $\mathcal { A } _ { 1 } \times \mathcal { A } _ { 2 } \times \cdot \cdot \cdot \times \mathcal { A } _ { | \mathbf { C } | }$ The transition to a new state $S ^ { \prime }$ <sup>=</sup>is governed by the transition probability $p : S \times \mathcal { U } \times S  [ 0 , 1 ]$ All agents share a unified reward function $R ^ { s l o w } : S \times \mathcal { U } \to \mathbb { R }$ designed to optimize overall performance and encourage cooperative behavior. γ is the discount factor.

We now transform the optimization problem into the Dec-POSMDP by defining its components as follows.

Observation space: The observation of agent $e _ { c } ( c \in \mathbf { C } )$ is defined as $o ^ { e _ { c } } = \langle q _ { l e n } , \phi ^ { c } , T ^ { c } , f _ { \mathrm { p r e v } } ^ { c } \rangle \in { \cal O } _ { c } .$ where $q _ { l e n }$ is the length of the arrival queue, $\phi ^ { c }$ denotes the utilization of servers in data center $c , T ^ { c }$ is the average temperature inside the data center c, $f _ { \mathrm { p r e v } } ^ { c }$ is the set of airflow rate settings for all CRAC units in data center c during the last cooling control decision period, and $O _ { c }$ represents the observation space of data center c. The global observation vector $\vec { o }$ is then formed by aggregating the observations of all agents in the system.

Action space: Each agent $e _ { c }$ will take an action $f ^ { c } =$ $\langle f _ { 1 } ^ { c } , f _ { 2 } ^ { c } , . . . , f _ { | \mathbb { B } ^ { c } | } ^ { c } \rangle , c \in \mathbb { B } ^ { c } , f ^ { c } \in \mathcal { A } _ { c }$ to regulate the airflow rates <sup>1 2</sup>of the CRAC units in data center c.

Reward function: The global reward $R ^ { s l o w }$ is defined as:

$$
\begin{array}{r l} R ^ {s l o w} = \xi_ {2} - \mu_ {4} \mathrm{PUE} & \\ - \mu_ {5} \sum_ {c \in \mathbf {C}} \ln \left(1 + \exp \left(T ^ {c} (\tau) - \psi^ {T}\right)\right) \end{array}\tag{33}
$$

where $\xi _ { 2 }$ is a positive constant ensuring that $R ^ { s l o w }$ remains positive. $\mu _ { 4 }$ and $\mu _ { 5 }$ are weight factors, $T ^ { c } ( \tau )$ denotes the average temperature of data center c during $\tau , \psi ^ { T }$ denotes the safe temperature threshold for all data centers. The elements in formula (33) are normalized in our experiments.

2) Solve the Markov Game via the QMIX: MARL methods like QMIX [25] enable effective collaboration by decomposing the global value function Qtot $Q _ { \mathrm { t o t } }$ into individual Q-values $Q _ { e _ { c } }$ for each agent while ensuring monotonicity property between the local Q-values and the global Q-value: $\hat { \frac { \partial Q _ { \mathrm { t o t } } } { \partial Q _ { e c } } } \doteq 0$ 9 $\forall e _ { c } \in { \mathcal { E } }$ This approach supports centralized training with decentralized execution.

QMIX consists of three types of deep neural networks. Agent Networks: Each agent $e _ { c }$ has an Actor-Critic structure. Actor network determines actions $f _ { t } ^ { e _ { c } }$ based on observations $o _ { t } ^ { e _ { c } }$ . Critic network evaluates the Q-value $Q _ { e _ { c } } ( o _ { n } ^ { e _ { c } } , f _ { n } ^ { e _ { c } } )$ . Mixing Network: Combines all local Q-values $Q _ { e _ { c } }$ into the global Q-value $Q _ { \mathrm { t o t } } .$ Hypernetwork: Generates weights for the mixing network using the global state s.

Since the local $Q _ { e _ { c } }$ and the global $Q _ { \mathrm { t o t } }$ share the same monotonicity, each agent can greedily select the best action based on its own $Q _ { e _ { c } }$ , leading to a globally optimal action combination. This relationship is formally expressed as:

$$
\operatorname * {a r g m a x} _ {\vec {f}} Q _ {\text {tot}} \left(\vec {o}, \vec {f}\right) = \left( \begin{array}{c} \operatorname * {a r g m a x} _ {f ^ {1}} Q _ {e _ {1}} \left(o ^ {e _ {1}}, f ^ {1}\right) \\ \vdots \\ \operatorname * {a r g m a x} _ {f ^ {| c |}} Q _ {e _ {| C |}} \left(o ^ {e _ {| C |}}, f ^ {| C |}\right) \end{array} \right)\tag{34}
$$

The global Q-function $Q _ { \mathrm { t o t } }$ is computed as:

$$
\left\{ \begin{array}{l} \mathbf {z} = E L U (\mathbf {q} \cdot | M L P (\boldsymbol {S}) | + M L P (\boldsymbol {S})) \\ Q _ {\text {tot}} (\vec {o}, \vec {f}) = \mathbf {z} \cdot | M L P (\boldsymbol {S}) | + M L P (\text {ReLU} (M L P (\boldsymbol {S}))), \end{array} \right.\tag{35}
$$

where $\mathbf { q } = [ Q _ { e _ { 1 } } ( o ^ { e _ { 1 } } , f ^ { 1 } ) , \dots , Q _ { e _ { | C | } } ( o ^ { e _ { | C | } } , f ^ { | C | } ) ] .$ , ELU · and $R e L U ( \cdot )$ <sup>= [ ( ) ( )] ( )</sup>are activation functions. MLP (Multi-Layer Perceptron) is a type of feedforward neural network consisting of multiple layers of neurons, which is used to extract high-level representations from the global state $S .$

Mixing Network Training: The loss function of $Q _ { t o t }$ is:

$$
\begin{array}{l} L (\omega) = \sum_ {j = 1} ^ {D} \\ \left[ \left(R _ {j} ^ {\text { slow }} + \gamma \max _ {\vec {f} _ {j + 1}} Q _ {\text { tot }} ^ {\prime} (\vec {o} _ {j + 1}, \vec {f} _ {j + 1}) - Q _ {\text { tot }} (\vec {o} _ {j}, \vec {f} _ {j})\right) ^ {2} \right] \end{array}\tag{36}
$$

where $D$ is the size of the mini-batch sampled from the replay buffer $\mathcal { D }$

By using the chain rule, we can update parameters of $Q _ { \mathrm { t o t } } \mathrm { : }$

$$
\nabla_ {\omega} L (\omega) = \frac {\partial L (\omega)}{\partial Q _ {\mathrm{tot}}} \cdot \frac {\partial Q _ {\mathrm{tot}}}{\partial \omega}\tag{37}
$$

Critic Network Training: The local Q-values $Q _ { e _ { c } }$ are updated based on the gradient of $Q _ { e _ { c } }$ using the chain rule. Loss function:

$$
L (\omega^ {e _ {c}}) = \sum_ {j = 1} ^ {D}
$$

$$
\left[ \left(R _ {j} ^ {\text {slow}} + \gamma \max _ {\vec {f} _ {j + 1}} Q _ {t o t} ^ {\prime} (\vec {o} _ {j + 1}, \vec {f} _ {j + 1}) - Q _ {e _ {c}} (o _ {j} ^ {e _ {c}}, f _ {j} ^ {c})\right) ^ {2} \right]\tag{38}
$$

By using the chain rule, we can update the parameters of $\textstyle Q _ { e _ { c } } .$

$$
\nabla_ {\omega_ {e _ {c}}} L (\omega_ {e _ {c}}) = \frac {\partial L (\omega_ {e _ {c}})}{\partial Q _ {\mathrm{tot}}} \cdot \frac {\partial Q _ {\mathrm{tot}}}{\partial Q _ {e _ {c}}} \cdot \frac {\partial Q _ {e _ {c}}}{\partial \omega_ {e _ {c}}}\tag{39}
$$

Actor Network Training: The Actor network optimizes the policy $\pi ^ { e _ { c } } ( o ^ { e _ { c } } )$ to maximize returns:

$$
\nabla_ {\theta} J = \mathbb {E} \left[ \nabla_ {f} Q _ {e _ {c}} (o ^ {e _ {c}}, f ^ {c}; \omega_ {e _ {c}}) \nabla_ {\theta_ {e _ {c}}} \pi (o ^ {e _ {c}}; \theta_ {e _ {c}}) \right]\tag{40}
$$

To improve stability, we employs soft updates for target networks:

$$
\omega^ {\prime} \leftarrow \lambda \omega + (1 - \lambda) \omega^ {\prime}\tag{41}
$$

$$
\theta_ {e _ {c}} ^ {\prime} \leftarrow \lambda \theta_ {e _ {c}} + (1 - \lambda) \theta_ {e _ {c}} ^ {\prime}\tag{42}
$$

where λ is soft update coefficients.

## C. Training of the Proposed Dual-Timescale Control Algorithm

In this paper, we concurrently and centralizedly train the DQN-based agent for task scheduling and QMIX-based agents for thermal management.

Training starts by initializing the $Q$ network $Q _ { s c d } , Q _ { s c d } ^ { \prime } ,$ and $Q _ { e _ { c } }$ , the policy $\pi ^ { e _ { c } }$ , and the mixing network $Q _ { t o t }$ . Exploration noise $\epsilon _ { 1 }$ and $\epsilon _ { 2 }$ are set for the DQN-based agent and the <sup>1 2</sup>QMIX-based agents. Each episode lasts N slow time steps, each divided into M fast time slots. At the beginning of a slow time slot, the system state is randomized and given to the DQN-based agent, which selects an action with noise $\epsilon _ { 1 } .$ receives reward $R _ { m } ,$ transits to $S _ { m + 1 } ,$ , and stores experiences in buffer . After M interactions, the QMIX-based agents observe $o _ { n } ^ { e _ { c } }$ , explore actions, receive rewards $r _ { n } .$ , transit to $o _ { n + 1 } ^ { e _ { c } } ,$ <sup>+1</sup>and store experiences in . Experiences are then sampled to optimize critic networks (36), (37), mixing network (38), (39), and actor networks (40). Target networks are updated via the soft update rule. The detailed training procedure is provided in Algorithm 1.

## V. EVALUATION

To evaluate the proposed algorithm, we use TRNSYS [44] to simulate the IT and cooling subsystems of geo-distributed data centers (as illustrated in Fig. 3) and analyze the experimental results.

## A. Setup

We use real-world datasets of grid electricity prices [27], wind speed [28], and solar irradiance [29], collected on May 1, 2021, for three data centers located in Shanghai, Zhejiang, and Jiangsu in China, as illustrated in Fig. 4. We derive renewable energy information by importing weather files into TRNSYS. These weather files typically contain representative meteorological data, including solar radiation, wind speed and direction, as well as environmental conditions such as ambient temperature. Based on [45], we outline the parameter settings for wind energy, and solar energy in Table II. It is noted that the asymmetry in solar and wind capacity reflects realistic geographical diversity, where some regions are richer in solar resources while others are more suitable for wind. To account for such heterogeneity, we introduce a “composite energy price” in (17), which allows D3T to accurately evaluate the OPEX of the three data centers and remain adaptable under different renewable distributions. In addition, while the variability and forecast errors of renewable energy may affect the performance, this paper does not explicitly model the forecast errors. However, D3T’s closed-loop control and the composite energy price mechanism (17)–(23) naturally dampen the impact of short-term fluctuations, enhancing operational robustness against renewable uncertainty.

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1: Training of the Proposed Dual-Timescale Control Algorithm.

1: Initialize the network $Q_{scd}(S, k; \phi)$ with random weights, exploration noise $\epsilon_1$.

2: Initialize the network $Q_{e_c}(o^{e_c}, f^{e_c}; \omega_{e_c})$ and mixing network $Q_{tot}(\vec{o}, \vec{f}; \omega)$ with random weights, exploration noise $\epsilon_2$.

3: Initialize the replay buffer $\mathcal{B}$ to store experiences for the DQN-based agent, and the replay buffer $\mathcal{D}$ to store experiences for the QMIX-based agents. During training, mini-batches of size $B$ are sampled from $\mathcal{B}$, and mini-batches of size $D$ are sampled from $\mathcal{D}$.

4: for episode = 1 → T do

5:    for n = 1 → N do

6:    for m = 1 to M do

7:    Upper level agent obtains $S_m$

8:    Selects $k_m$ according to $k_m = \pi^{scd}(S_m; \phi) + \epsilon_1$

9:    Receives $R_m^{fast}$ and observes $S_{m+1}$

10:    Stores transition ($S_m, k_m, R_m^{fast}, S_{m+1}$) in $\mathcal{B}$.

11:    Update state $S_m = S_{m+1}$

12:    end for

13:    $S_n = S_{m+M}$

14:    for each agent $e_c$ do

15:    Obtain $o_n^{e_c}$

16:    Select $f_n^c$ according to $f_n^c = \pi^{e_c}(o_n^{e_c}; \theta_{e_c}) + \epsilon_2$

17:    Execute $f_n^c$ and observe reward $R_n^{slow}$

18:    Obtain new observation $o_{n+1}^{e_c}$

19:    end for

20:    The joint observation $\vec{o}_n := (o_n^{e_1}, \ldots, o_n^{e|c|})$

21:    The joint action $\vec{f}_n := (f_n^1, \ldots, f_n^{|c|})$

22:    Store ($S_n, \vec{o}_n, \vec{f}_n, R_n^{slow}, \vec{o}_{n+1}, S_{n+1}$) in $\mathcal{D}$

23:    Update state $S_m = S_{n+1}$

24:    end for

25:    Update parameter of upper level agent according to (36)-(40).

26:    Update parameter of lower level agents according to (29) and (30).

27:    Update target network weights:

28:    $\phi' ← λφ + (1 - λ)\phi'$

29:    $ω' ← λω + (1 - λ)\omega'$

30:    $θ'_{e_c} ← λθ_{e_c} + (1 - λ)\theta'_{e_c}$

31: end for
</div>

![](images/2c38257a567da98a19742e523f2fbb01db8ec1318eb39d1ab4fdc6df4efd66b5.jpg)  
Fig. 3. The experimental scenario in TRNSYS.

TABLE II  
PARAMETER SETTING OF ENERGY SOURCES

<table><tr><td rowspan="2"></td><td colspan="2">Solar energy</td><td colspan="3">Wind energy</td></tr><tr><td> $\kappa^c (m^2)$ </td><td> $\psi^c$ </td><td> $\zeta^c$ </td><td> $\eta^c (m^2)$ </td><td> $\alpha^c (kg/m^2)$ </td></tr><tr><td>DC1</td><td>0.2</td><td>15000</td><td>0.3</td><td>25000</td><td>1.225</td></tr><tr><td>DC2</td><td>0.25</td><td>18750</td><td>0.375</td><td>31250</td><td>1.5313</td></tr><tr><td>DC3</td><td>0.3</td><td>22500</td><td>0.45</td><td>37500</td><td>1.8375</td></tr></table>

For the IT subsystem, we simulate task scheduling and workload dynamics by using Python codes. Specifically, we utilize a real workload trace,“KIT FH2” [26] to generate task requests. We totally extract 14,400 task records, 10,000 records are used for training and the remaining 4,400 for evaluation. Each task record contains a task ID, arrival time, CPU core requirement (ranging from 12 to 36), and execution time (ranging from 2 to 60 minutes). For the three data centers (i.e., $| \mathbf { C } | = 3 )$ we assume that all data centers have the same number of servers $( | \mathbf { V } ^ { c } | = 5 1 2 )$ with the same configuration. Each server is <sup>= 512</sup>equipped with 24 CPU cores, and the idle power $P _ { i d l e } = 1 0 0 ~ \mathrm { W }$ and the full power for server $P _ { f u l l } = 3 0 0 ~ \mathrm { W }$ . Although our experimental setup involves only 3 data centers for proof of concept, the QMIX [25] algorithm is designed with scalability in mind. Its mixing network’s complexity scales linearly with the number of agents, and its decentralized execution paradigm ensures operational efficiency remains manageable as the system grows.

For the cooling subsystem, the chilled water outlet temperature threshold of the EC is $6 ^ { \circ } \mathrm { C - 1 2 ^ { \circ } C } .$ , the rated COP and cooling capacity are 3.8 and , respectively. Cooling tower fan idle energy consumption $P _ { b a s e } ^ { C T } = \bar { 5 } \mathrm { k W }$ , rated speed $R _ { b a s e } ^ { C T } = 6 0 \ : \mathrm { H z }$ . The head of chilled water pump and cooling water pump is . The number of pumps is $2 \left( \left| \mathbb { U } ^ { c } \right| \equiv 2 \right)$ . The number of CRAC units is $2 \left( \left| \mathbb { B } ^ { c } \right| \equiv 2 \right)$ <sup>2</sup>. The safe temperature for all data center rooms is set to $2 7 ^ { \circ } \mathrm { C }$

For the Q-Network of the task scheduler, we employ a 2- layer fully connected feedforward neural network, consisting of 256 neurons in the input layer and 64 neurons in the output layer, utilizing the “tanh” activation function. The mixing and critic networks of the cooling controller also follow a similar feedforward architecture with 256 and 64 neurons respectively, both using the “tanh” activation function. In addition, the actor network of the cooling controller features a 2-layer feedforward design, comprising 128 neurons in the first layer and 64 neurons in the second, with $\mathrm { \hat { \ s e L U } } ^ { \mathrm { \tiny { s } } }$ as the activation function. In addition, it is important to note that the D3T scheduler operates on data center-level aggregated state information (e.g., overall utilization), making its performance independent of the number of servers within a center. Similarly, the thermal model is based on scale-invariant physical principles, and the QMIX-based cooling controller naturally adapts to larger thermal loads or heterogeneous configurations through its decentralized, per-agent learning process.

The other key parameters are set as: the mini-batch sizes are $B = 6 4$ for the training of DQN-based agent and $D = 6 4$ for the training of QMIX-based agents, the discount factor $\gamma = 0 . 9 9$ the initial learning rate is 0.0001, reward constants are set to $\xi _ { 1 } = \xi _ { 2 } = 1$ , weight coefficients are set to $\mu _ { 1 } = \mu _ { 2 } = 0 . 1 , \mu _ { 3 } =$ $\mu _ { 4 } = 0 . 2 , \mu _ { 5 } = 0 . 4$ , the soft update coefficient $\lambda = 0 . 0 0 5$ <sup>3 =</sup>. A working day has $N = 4 8$ slow time steps with the length of <sup>= 48</sup>l  min and each slow time step have $M = 3 6 0$ fast time intervals with the length of $d = 5 \mathrm { s }$ <sup>= 360</sup>. In our simulation, the fast and slow control loops are synchronized to a centralized global clock for simplicity.

The overall simulation process is as follows. Task arrival and execution is simulated using Python codes, while TRNSYS is employed for modeling the cooling system and thermodynamic processes. When an IT task arrives, it first enters the task queue and waits for scheduling. At each decision interval, the fasttimescale scheduler selects the task at the head of the queue and assigns it to an appropriate data center. Once executed, the task contributes to the heat load of the corresponding data center, thereby affecting room temperature. Based on the thermal and workload conditions, the slow-timescale controller adjusts the cooling system to ensure energy efficiency while maintaining thermal safety.

## B. Perform Metrics

We adopt the following algorithms to evaluate the performance of our proposed algorithm:

\- OPEX of IT subsystem: the average OPEX of IT subsystem of all data centers, described in formula (21).

\- OPEX of cooling subsystem: the average OPEX of cooling subsystem of all data centers, which is described in formula (22).

\- Task sojourn time: the average sojourn time of all computational tasks, described in formula (16).

![](images/7eb96b135d2449c877b705fdeecded6282c4ce03448c9b93f8d1a8b85391cf94.jpg)  
(a) Grid electricity price

![](images/17aff0314c0e1e601f9ca29a9f73b7a3ccbe3cb4275a7a05ede9adeb5cf7ae4b.jpg)  
(b) Solar irradiance

![](images/05697433554efc235e2cf375ade2fbd5504ed6ae786be50001a09bde5c2f9ca7.jpg)  
(c) Wind speed  
Fig. 4. The grid electricity price, solar irradiance, and wind speed at three geo-distributed data centers.

\- PUE: the average PUE of all data centers, which was described in formula (25).

\- Average temperature: the average temperature inside a data center room.

## C. Baseline Algorithms

We compare our proposed algorithm with five baseline solutions:

\- Round Robin and Fixed airflow rate (RR-F): Tasks are dispatched using Round Robin (RR) for load balancing, while CRAC units hold a fixed airflow rate.

Geographical Scheduling of Multi-Application Tasks and Fixed airflow rate (GSMAT-F) [46]: Tasks are scheduled based on energy prices to reduce OPEX, while CRAC units hold a fixed airflow rate.

Deep Q-Network and Deep Deterministic Policy Gradient (DQN-MADDPG) [18]: DQN-based approach are used to allocate tasks for geo-distributed data centers with consideration of energy prices, while MADDPG-based approach are employed to adjust CRAC airflow rate dynamically.

\- Deep Q-Network and Distributed Model Predictive Control (DQN-DMPC) [17]: DQN-based approach are used to allocate tasks, while DMPC-based approach are utilized to control CRAC airflow rate.

Pseudo Slow-timescale DQN (PS-DQN): This DQN-based method operates on a fast timescale to jointly optimize task scheduling and airflow control. To approximate a dualtimescale effect, airflow actions are only really executed every 240 times task scheduling, and a penalty is introduced to its reward function to mitigate temperature volatility. Its reward function is defined as follows:

$$
R _ {p s} = R ^ {f a s t} - \mu_ {4} \mathrm{PUE} - \mu_ {5} \Xi + \mu_ {6} \Phi\tag{43}
$$

where $R ^ { f a s t }$ and $\Xi$ are defined in (28) and (27), respectively, encourages larger airflow adjustments when temperature deviations increase, which are defined as:

$$
\Phi = \frac {1}{M \times N} \sum_ {\tau = 1} ^ {M \times N} \sum_ {c \in \mathbf {C}} \exp \left(\left(| \Delta T _ {c} (\tau) | - T _ {0}\right) \cdot | \Delta f _ {c} (\tau) |\right)\tag{44}
$$

where $\Delta T _ { c } ( \tau )$ denotes the temperature increase of data center c at τ relative to the temperature after the last thermal management, $T _ { 0 }$ is the predefined threshold of temperature variation, $\Delta f _ { c } ( \tau )$ represents the adjustment decision for airflow rate in data center c at τ .

![](images/8b6df7c0b84ba811081f78fe7f9356b50af1d3808084096a1e64c49125bb90a3.jpg)  
Fig. 5. The training result for D3T and DQN-MADDPG.

## D. Results of Training

Here we compare the training results of two learning-based algorithms, i.e., D3T and DQN-MADDPG. The learning curves are illustrated in Fig. 5. For each algorithm, we run the simulation over 5000 epochs, sampling the reward values every 20 epochs. D3T begins to converge after around 600 epochs, while DQN-MADDPG begins to converge after about 2000 epochs. Compared to DQN-MADDPG, D3T converges faster and can obtain higher rewards. During training, we did not observe persistent or destabilizing decision conflicts (e.g., scheduling heavy workloads to a data center while its cooling is scaled down) between task scheduling and thermal management, this is because D3T introduce critical state sharing between DQN and QMIX.

## E. Performance Comparison

To evaluate the performance gain of our proposed D3T, we compare it against five baseline algorithms introduced in Section V-C.

As shown in Fig. $6 ( \mathrm { a } ) \mathrm { - } ( \mathrm { d } )$ , D3T outperforms the other five algorithms in terms of cooling OPEX, task sojourn time, and

![](images/b80184ed695c0ca952918d2e2d8cc224a873c85e90a9b376510faa3dd5e1f688.jpg)  
(a) OPEX of IT subsystem

![](images/0193a1d2680dc66c5e326fbfe6f924b8fa2be287e1b084d888071b5c61085921.jpg)  
(b) OPEX of cool subsystem

![](images/6610e14d5cb96c7b48f0a3ddabf426a9bd8bce1e07d89b3c973be953f2faccdf.jpg)  
(c) The task sojourn time

![](images/ed96876c8409ea456b0c8e9fee1ec101421ab2d631bfd9bcae250eba72f64ff7.jpg)  
(d) The PUE

![](images/54e82a36df12c0813d3e91f821e23424d50756f11151c6d87c757346e5e4eff4.jpg)  
(e) The avg. temperature of data center 1

![](images/09fc3164cd78795314485b58983c72b81e021e7ea331da36f264684db645bac6.jpg)  
(f) The avg. temperature of data center 2

![](images/ae527422ea594d13c52805552001ed7c19963257c2a1a841af73d150b9d1d973.jpg)  
(g) The avg. temperature of data center 3  
Fig. 6. The comparison results in terms of OPEX, the average task sojourn time, PUE and temperature.

PUE. However, the IT OPEX of D3T is slightly higher than GSMAT-F. As a heuristic method, GSMAT-F is difficult to well adapt to multi-objective optimization problem, which only greedily assigns tasks to a data center with the lowest electricity price, while ignoring queue dynamics and thermal effects. In contrast, D3T aims to schedule tasks in fast timescale to the geo-distributed data centers with the lowest composite energy price and sufficient available computing resources, reaches a good balance between the OPEX and the task sojourn time. RR-F emphasizes load balancing rather than OPEX optimization, resulting in longer average sojourn time and the highest IT OPEX. PS-DQN incurs relatively high IT OPEX (only slightly lower than RR-F) and long waiting times. This is because PS-DQN manages cooling primarily through temperature penalties, leading the scheduler to prioritize data centers less likely to trigger thermal overload, thereby reducing the emphasis on IT OPEX and waiting time. Moreover, since cooling adjustments are only triggered once the thermal penalty exceeds a certain threshold, PS-DQN achieves lower PUE and cooling costs (only higher than our proposed D3T).

For thermal management results as illustrated in Fig. 6(e)–(g), D3T ensures the thermal safety of data centers while reducing PUE by dynamically regulating the airflow rate of CRAC units, resulting in the lowest cooling system energy consumption. In contrast, RR-F and GSMAT-F use a fixed airflow rate, leading to excessive cooling (higher cooling system energy consumption) to maintain thermal safety, which results in a higher PUE. Additionally, due to their lack of adaptive control, they exhibit significant temperature fluctuations across all three data centers. DQN-MADDPG struggles to effectively coordinate cooperation among agents due to the lack of a value decomposition mechanism, leading to suboptimal performance. This results in noticeable temperature fluctuations, particularly in Fig. 6(e) and (f), where the lack of coordination causes inconsistent thermal conditions. Although DQN-DMPC focuses more on local control and optimization, it exhibits even greater temperature fluctuations than DQN-MADDPG, especially in Fig. 6(e), where its reliance on local decision-making fails to achieve stable thermal conditions. This instability leads to higher PUE and inefficient energy usage. In general, PS-DQN yields a higher average temperature than other baseline algorithms, as it adjusts CRAC units only when the temperature exceeds a predefined threshold. This mechanism drives the average temperature of PS-DQN closer to the safe temperature ( <sup>◦</sup>C in our experiments).

## F. Performance Sensitivity

1) The Influence of Fast Timescale Interval d: In this section, we carry out further experiments to investigate the performance sensitivity by setting the fast timescale interval d to { s,  s,  s}, <sup>1 5 9</sup>while keeping the setting of other parameters consistent with Section V-A.

It can be found from Fig. 7 that the OPEX of the IT subsystem is influenced by both the decision interval d and fluctuations in energy prices. The experiments demonstrate that d has a significant impact on task scheduling efficiency and cooling energy consumption. As d increases (1 s → 9 s), task sojourn time grows due to less frequent scheduling, while PUE first decreases and then flattens and stabilizes, reaching its optimal value at d s. At d s, overly frequent scheduling can overload localized servers, causing the cooling controller to misinterpret thermal trends as rapid heating. This leads to excessive and inefficient cooling adjustments, thereby raising PUE. In contrast, at $d = 9 \mathrm { { s } }$ , the long intervals produce uneven workload distribution, requiring sustained high cooling power to prevent thermal accumulation, which also degrades PUE. The best performance at $d = 5 :$ s stems from balanced coordination: task scheduling aligns with workload dynamics to avoid resource contention, while cooling adjustments align with thermal inertia (approximately 15 minutes), minimizing both energy waste and temperature fluctuations. Moreover, all data centers maintained safe temperature thresholds $( < 2 7 ^ { \circ } C )$ , but $d = 5$ s achieved the lowest temperature variability, confirming the effectiveness of the dual-timescale framework in harmonizing fast task scheduling (seconds) with slow thermal management (minutes) for energy-efficient operations.

![](images/c6b59291493028f4e046ab855722967624860caebc9e4aa8bd53d50ee07bccf7.jpg)  
(a) OPEX of IT subsystem

![](images/917ac9de713350c392f6b9ea3e99aefbffc0ac651eb32d73b4cdbc4898db3820.jpg)  
(b) OPEX of cool subsystem

![](images/607ef1da26eba0ef00931d55a93687626eebcd7a97f29b3dc350d53dd1373126.jpg)  
(c) The task sojourn time

![](images/1b0e966fb9398519df1cc27df526c1d86f1eaa7f3a27d010db2ee456c24f75c2.jpg)  
(d) The PUE

![](images/0ab39b3625174a410bb9e6373310d02362850ff163309e9108f814470b1ce7fe.jpg)  
(e) The avg. temperature of data center 1

![](images/8dee3154e5e60c8fead3c72de00ac4e108c8769e695d205618212ef773a45a0d.jpg)  
(f) The avg. temperature of data center 2

![](images/ce599be543e3189b7b9fdfb0c1d9f630531aae384f75dc296b595ca044f7457e.jpg)  
(g) The avg. temperature of data center 3  
Fig. 7. The effect of the decision interval <sup>d</sup>. As <sup>d</sup> increases, the sojourn time rises, while PUE initially decreases then increases. Overall, <sup>d</sup> = 5 s yields the best performance, with the average computing room temperature consistently within safe limits, demonstrating the effectiveness of the thermal management strategy.

2) The Influence of Slow Timescale Interval l: In the previous experiment, we set the slow timescale interval l , meaning that the cooling controller adjusts the airflow rate of the CRAC units every 30 minutes. In this section, we investigate the impact of different l on the performance. Herein we set l to 20min, 30min, and 40min, respectively. All other settings remain the same as those described in Section V-A.

The results are shown in Fig. 8. When $l = 4 0 \mathrm { { m i n } }$ , the delayed cooling response often prevents the cooling capacity from matching the heat generated by the IT workload, leading to elevated room temperatures. This, in turn, increases the servers’ fan energy consumption. As a result, the IT OPEX becomes relatively high while the cooling OPEX remains low, yielding a lower PUE. However, under this setting, the room temperature frequently exceeds the threshold, as shown in Fig. 8(f) and (g). Conversely, when $l = 2 0 \mathrm { { m i n } }$ , the Cooling Controller adjusts

CRAC airflow more frequently, making it highly sensitive to IT workload fluctuations. To ensure thermal safety, it must deliver higher airflow, driving up cooling energy consumption and increasing PUE. The additional cooling demand also competes with the IT subsystem for energy, further raising IT OPEX. Nevertheless, the room temperature remains consistently within the safe threshold when l . $\mathrm { A t } \ l = 3 0 \mathrm { m i n }$ , the system achieves the best trade-off: cooling adjustments align with thermal inertia, balancing OPEX and PUE. This interval harmonizes slow-timescale thermal management with fast-timescale task scheduling, ensuring stable room temperatures $( < 2 7 ^ { \circ } C )$ across all data centers.

3) The Influence of Weight Coefficients $\mu _ { 1 } { - } \mu _ { 5 }$ in Reward Function: To investigate the impact of weight coefficients in reward functions on the performance of D3T, we conduct a sensitivity study by assigning different values to the coefficients $\mu _ { 1 } { - } \mu _ { 5 }$ in (28) and (33). The default configuration of D3T is $\mu _ { 1 } = 0 . 1 , \mu _ { 2 } = 0 . 1 , \mu _ { 3 } = 0 . 2 , \mu _ { 4 } = 0 . 2$ , and $\mu _ { 5 } = 0 . 4$ . Four <sup>1 = 0 1 2 = 0 1 3 = 0 2 4 = 0 2 5 =</sup>variants are derived for comparison: 1) D3T-Cost: $\mu _ { 1 } = 0 . 2$ $\mu _ { 2 } = 0 . 2 , \mu _ { 3 } = 0 . 1 5 , \mu _ { 4 } = 0 . 1 5 , \mu _ { 5 } = 0 . 3$ (higher weight on IT <sup>= 0 2 = 0 15 = 0 15</sup>and cooling cost). 2) D3T-Time: $\mu _ { 1 } = 0 . 1 , \mu _ { 2 } = 0 . 1 , \mu _ { 3 } = 0 . 4$ $\mu _ { 4 } = 0 . 1 , \mu _ { 5 } = 0 . 3$ (higher weight on task sojourn time). 3) <sup>4 = 0 1</sup>D3T-PUE: $\mu _ { 1 } = 0 . 1 , \mu _ { 2 } = 0 . 1 , \mu _ { 3 } = 0 . 1 , \mu _ { 4 } = 0 . 4 , \mu _ { 5 } = 0 . 3$ (higher weight on PUE). 4) D3T-Safety: $\mu _ { 1 } = 0 . 1 5 , \mu _ { 2 } = 0 . 1 5$ $\mu _ { 3 } = 0 . 3 , \mu _ { 4 } = 0 . 3 , \mu _ { 5 } = 0 . 1$ (lower weight on thermal safety).

As shown in Fig. 9, D3T-Cost achieves the lowest OPEX, but this marginal gain comes at the expense of the longest task sojourn time. With higherμ and $\mu _ { 2 }$ , the scheduler prioritizes data centers with lower electricity prices or more renewable energy, while disregarding latency. D3T-Time achieves the shortest sojourn time by favoring immediate execution, but its disregard

![](images/29d744a807a9be26ac1008ceeea27526af990c1b8fbee406d8beefae33b0b35e.jpg)  
(a) OPEX of IT subsystem

![](images/9ce6757e8923244f52bc95dc0d06ccafaf76247c65a48dae85124ac01c54c59b.jpg)  
(b) OPEX of cool subsystem

![](images/d5e3823c002a432e955cddd8050257c823bea669bf8bd99547be372fe53dd38e.jpg)  
(c) The task sojourn time

![](images/4ebcafe76c2dc0ddfff20f49dbd2c4d771988ec05e3b1a2badae066f322d3fbf.jpg)  
(d) The PUE

![](images/3ecedf0250dd9bf2419c1a1184179e405294b674473a4a236dc68067730b2791.jpg)  
(e) The avg. temperature of data center 1

![](images/201757e9735d8008dfdb2640b65d0b92adf2ea9ffe5ef095fd5c238fb2e110fd.jpg)  
(f) The avg. temperature of data center 2

![](images/aa5a57267d9c317127af9bc269ff0ecc712f6775f11a9df3223371827bfccf6f.jpg)  
(g) The avg. temperature of data center 3

Fig. 8. The influence of cooling controller decision interval <sup>l</sup>. The length of cooling controller decision interval will violate thermal safety either too long or too short. When l=30, thermal safety can be guaranteed and a relatively ideal cooling system cost can be achieved.  
![](images/83bf3bbf6222c88d27fe5491b5fb6bf3612526dc7c5e87cab63563f419c396a0.jpg)  
(a) OPEX of IT subsystem

![](images/2f90a0e142d1c978cd2e46f3be769b3a2fe1c772ac014641e06d7d3c5fd9ffe5.jpg)  
(b) OPEX of cool subsystem

![](images/1a3f35f76a9e0c06b48322726c90ed66dca9f0cf94f8768594344982545a13f4.jpg)  
(c) The task sojourn time

![](images/a266282a909d0500ee25d31e48b4fe246bff6ef0bf0e5ac0c795f9cd56663c81.jpg)  
(d) The PUE

![](images/5162c64ea77982dd332e2fa33021fb4a92bf077f1a016d86ab48d4e86eb7e7d8.jpg)  
(e) The avg. temperature of data center 1

![](images/83e1b62eaae20f6d54ef4b27fd9a17e277ae165922454e5a63715a51ad3035d8.jpg)  
(f) The avg. temperature of data center 2

![](images/5e1575e47dfb09d4c918238fe85142da1e6fd85908d942df7a66085d6626f96d.jpg)  
(g) The avg. temperature of data center 3  
Fig. 9. The influence of weight coefficients $\mu _ { 1 } { - } \mu _ { 5 }$ in reward function. The default configuration of D3T can achieve better balanced performance across all objectives including OPEX, sojourn time, PUE, and thermal safety.

for electricity price signals results in higher OPEX. In addition, contention between IT load and cooling further increases cooling expenditure. D3T-PUE improves PUE by suppressing cooling intensity and reducing cooling energy consumption, but at the cost of elevated overall data center temperatures. D3T-Safety reduces $\mu _ { 5 } .$ , weakening the thermal-safety penalty and allowing temperatures to approach the safety threshold, thereby increasing the risk of overheating. In contrast, the original D3T delivers balanced performance across all objectives.

## G. Ablation Study

In this subsection, we present an ablation study to better clarify the contributions of each component in D3T. Three variants of the proposed algorithm are designed: 1) RR-QMIX: The DQN-based task scheduler is disabled and replaced with a standard Round-Robin policy, while the QMIX-based thermal management module remains active. 2) DQN-F: The QMIX-based thermal management is disabled and replaced with a fixed airflow rate for the CRAC units, while the DQN-based task scheduler remains active. 3) D3T-NT: The thermal penalty term is removed from the reward function in D3T.

![](images/beb843f281236cf9a2aa43c39e7e5035c7083c00b871e91b1b2ea14c7bc74f6e.jpg)  
(a) OPEX of IT subsystem

![](images/0a6dff9c1f402e3406c3e680ec931c368f2ef7360ce1b97e089ee3d87a1dccc7.jpg)

![](images/a2e57beab224c29f17b76649a3ed401d1f526269f0cf206282ca10d41dc3a268.jpg)  
(e) The avg. temperature of data center 1

(b) OPEX of cool subsystem  
![](images/68eacbe87c64529e6ddd39967559b3d206872e891e679fe6152ff1ba7b254b18.jpg)  
(f) The avg. temperature of data center 2

![](images/aa4b6711c9b0669779465091fc3903dff120092649d9cfe72ebb138f3c7bc2fb.jpg)  
(c) The task sojourn time

![](images/40440ffaea83e903955b7953ed7166b28b5746dee1bc2f4026ad4657062b07b2.jpg)

![](images/3f174809e6b288128ed9b5294363a807ff599196f492c359a207ebdda4c0b665.jpg)  
(g) The avg. temperature of data center 3

(d) The PUE  
![](images/e0509570fd18e7a2cc246bba497828646afe56628249377308858c020fbcf6fe.jpg)  
(h) The avg. temperature of data centers with D3T-NT  
Fig. 10. The results of ablation study. Removing the DQN component increases IT cost and task sojourn time, removing QMIX leads to overcooling, while removing the temperature penalty in the reward function causes a catastrophic rise in data center temperature.

The results in Fig. 10 show that the IT cost of RR-QMIX is significantly higher than the others, as Round-Robin scheduling cannot account for real-time electricity prices and available renewable energy as effectively as DQN, thereby increasing cost. The cooling cost and PUE of DQN-F are the highest, since its fixed airflow strategy fails to adapt to workload fluctuations and must maintain a high airflow rate for safety, leading to prolonged over-cooling and excessive energy consumption. The cooling cost of RR-QMIX is slightly higher than that of D3T, because Round-Robin ignores task size, resulting in uneven workload distribution and reduced effectiveness of QMIX-based thermal management. Moreover, its task sojourn time is considerably longer, as it allocates tasks based only on available resources without considering workload conditions across data centers, leading to imbalances and unnecessary waiting. For D3T-NT, the removal of the thermal penalty term drives the agent to gradually set airflow to zero, which lowers cooling cost and PUE but causes uncontrolled heat accumulation, pushing temperatures far beyond the safety threshold.

## VI. CONCLUSION AND FUTURE WORK

In this paper, we proposed D3T, a dual-timescale DRL framework for jointly optimizing task scheduling and thermal management in geo-distributed data centers facing surging AI-driven compute-intensive workloads. The main contributions include: 1) D3T introduces a dual-timescale control paradigm to address the mismatch in time constants between the IT and the cooling subsystems, which can dynamically align cooling demand with supply. 2) At the fast timescale, D3T employs a DQN to make second- or millisecond-level task scheduling decisions, while at the slow timescale, D3T leverages QMIX to achieve minutelevel collaborative thermal management across multiple data centers. 3) Built on a TRNSYS-based simulation platform with real-world traces, D3T demonstrated significant improvements, including reductions of 13% in IT OPEX and 29% in cooling OPEX, a 7% gain in PUE, while achieving a better trade-off among OPEX, task sojourn time, and PUE, while maintaining thermal safety.

For future work, we intend to extend D3T for GPU-based data centers running both AI training and inference workloads, where the much higher thermal density poses stricter requirements on task scheduling and cooling control. Also, we plan to explore its applicability in liquid-cooled data centers.

## REFERENCES

[1] Data Centres and Data Transmission Networks, 2024. [Online]. Available: https://www.iea.org/energy-system/buildings/data-centres-and-datatransmission-networks

[2] Powering the Data-Center Boom with Low-Carbon Solutions, 2024. [Online]. Available: https://rmi.org/wp-content/uploads/dlm\_uploads/ 2024/11/Powering\_the\_Data\_Center\_Boom\_with\_Low\_Carbon\_ Solutions\_report.pdf

[3] Power Usage Effectiveness (PUE), 2024. [Online]. Available: https://en. wikipedia.org/wiki/Power\_usage\_effectiveness

[4] Uptime Institute Global Data Center Survey 2024, 2024. [Online]. Available: https://datacenter.uptimeinstitute.com/rs/711-RIA-145/ images/2024.GlobalDataCenterSurvey.Report.pdf?version=0

[5] Y. Ran, H. Hu, Y. Wen, and X. Zhou, “Optimizing energy efficiency for data center via parameterized deep reinforcement learning,” IEEE Trans. Serv. Comput., vol. 16, no. 2, pp. 1310–1323, Mar./Apr. 2023.

[6] A. Khan, T. Kim, H. Byun, and Y. Kim, “SciSpace: A scientific collaboration workspace for geo-distributed HPC data centers,” Future Gener. Comput. Syst., vol. 101, pp. 398–409, 2019. [Online]. Available: https: //www.sciencedirect.com/science/article/pii/S0167739X18326025

[7] N. Hogade and S. Pasricha, “A survey on machine learning for geodistributed cloud data center management,” IEEE Trans. Sustain. Comput., vol. 8, no. 1, pp. 15–31, First Quarter 2023.

[8] A. Mohammadzadeh, M. Masdari, F. S. Gharehchopogh, and A. Jafarian, “Improved chaotic binary grey wolf optimization algorithm for workflow scheduling in green cloud computing,” Evol. Intell., vol. 14, pp. 1997–2025, 2021.

[9] A. Mohammadzadeh, M. Masdari, and F. S. Gharehchopogh, “Energy and cost-aware workflow scheduling in cloud computing data centers using a multi-objective optimization algorithm,” J. Netw. Syst. Manage., vol. 29, 2021, Art. no. 31.

[10] A. Mohammadzadeh, M. Masdari, F. S. Gharehchopogh, and A. Jafarian, “A hybrid multi-objective metaheuristic optimization algorithm for scientific workflow scheduling,” Cluster Comput., vol. 24, no. 2, pp. 1479–1503, 2021.

[11] C. Qian et al., “Optimal scheduling of building energy system with integrated virtual energy storage based on multi-task model predictive control,” J. Building Eng., vol. 98, 2024, Art. no. 111185.

[12] J. Wei and X. Liang, “Research on task-offloading delay in the IoV based on a queuing network,” IEEE Access, vol. 12, pp. 31324–31333, 2024.

[13] S. Mangalampalli, G. R. Karri, M. Kumar, O. I. Khalaf, C. A. T. Romero, and G. A. Sahib, “DRLBTSA: Deep reinforcement learning based task-scheduling algorithm in cloud computing,” Multimedia Tools Appl., vol. 83, no. 3, pp. 8359–8387, 2024.

[14] Y. Cheng, Z. Cao, X. Zhang, Q. Cao, and D. Zhang, “Multi objective dynamic task scheduling optimization algorithm based on deep reinforcement learning,” J. Supercomputing, vol. 80, no. 5, pp. 6917–6945, 2024.

[15] D. Wang, W. Zheng, Z. Wang, Y. Wang, X. Pang, and W. Wang, “Comparison of reinforcement learning and model predictive control for building energy system optimization,” Appl. Thermal Eng., vol. 228, 2023, Art. no. 120430.

[16] J. Zhao, D. Liu, X. Yuan, and P. Wang, “Model predictive control for the ice-storage air-conditioning system coupled with multi-objective optimization,” Appl. Thermal Eng., vol. 243, 2024, Art. no. 122595.

[17] X. Xin et al., “A comprehensive review of predictive control strategies in heating, ventilation, and air-conditioning (HVAC): Model-free vs model,” J. Building Eng., vol. 94, 2024, Art. no. 110013.

[18] R. Wang, D. Van Le, J. Kang, R. Tan, and X. Liu, “Incentive temperature control for green colocation data centers via reinforcement learning,” in Proc. IEEE/ACM 32nd Int. Symp. Qual. Serv., 2024, pp. 1–6.

[19] K. He et al., “Predictive control optimization of chiller plants based on deep reinforcement learning,” J. Building Eng., vol. 76, 2023, Art. no. 107158.

[20] S. Rostami, D. G. Down, and G. Karakostas, “Linearized data center workload and cooling management,” IEEE Trans. Autom. Sci. Eng., vol. 22, pp. 3502–3514, 2025.

[21] L. Zhang et al., “Research on data center computing resources and energy load co-optimization considering spatial-temporal allocation,” Comput. Elect. Eng., vol. 116, 2024, Art. no. 109206.

[22] S. Sarkar et al., “Carbon footprint reduction for sustainable data centers in real-time,” in Proc. AAAI Conf. Artif. Intell., 2024, pp. 22322–22330.

[23] Y. Ran, X. Zhou, H. Hu, and Y. Wen, “Optimizing data center energy efficiency via event-driven deep reinforcement learning,” IEEE Trans. Serv. Comput., vol. 16, no. 2, pp. 1296–1309, Mar./Apr. 2023.

[24] V. Mnih et al., “Human-level control through deep reinforcement learning,” Nature, vol. 518, no. 7540, pp. 529–533, 2015.

[25] T. Rashid et al., “Monotonic value function factorisation for deep multiagent reinforcement learning,” J. Mach. Learn. Res., vol. 21, no. 178, pp. 1–51, 2020.

[26] D. Feitelson, “The standard workload format,” Website, 2006. [Online]. Available: http://www.cs.huji.ac.il/labs/parallel/workload/swf.html

[27] NYISO, “Day-ahead scheduled outages,” Website, 2024. [Online]. Available: https://www.nyiso.com/power-grid-data

[28] NASA, “Atmosphere,” 2024. Accessed: Oct. 29, 2024. [Online]. Available: https://www.earthdata.nasa.gov/topics/atmosphere

[29] NASA, “Weather,” Website, 2024. [Online]. Available: https://www. earthdata.nasa.gov

[30] E. Pakbaznia and M. Pedram, “Minimizing data center cooling and server power costs,” in Proc. 2009 ACM/IEEE Int. Symp. Low Power Electron. Des., 2009, pp. 145–150.

[31] B. Zhou, X. Song, X. Shi, Y. Lu, and S. Hu, “Bridging server and cooling: Toward effective energy management in data centers,” in Proc. IEEE 25th Int. Conf. Parallel Distrib. Syst., 2019, pp. 45–52.

[32] S. MirhoseiniNejad, H. Moazamigoodarzi, G. Badawy, and D. G. Down, “Joint data center cooling and workload management: A thermal-aware approach,” Future Gener. Comput. Syst., vol. 104, pp. 174–186, 2020.

[33] C. Chi et al., “Jointly optimizing the IT and cooling systems for data center energy efficiency based on multi-agent deep reinforcement learning,” in Proc. 11th ACM Int. Conf. Future Energy Syst., 2020, pp. 489–495.

[34] Y. Ran, H. Hu, X. Zhou, and Y. Wen, “DeepEE: Joint optimization of job scheduling and cooling control for data center energy efficiency using deep reinforcement learning,” in Proc. IEEE 39th Int. Conf. Distrib. Comput. Syst., 2019, pp. 645–655.

[35] H. Cui, W. Xia, and S. Yang, “Two-time-scale dispatch strategy for networked micro-grids considering uncertainties and demand response,” Int. J. Elect. Power Energy Syst., vol. 145, 2023, Art. no. 108620.

[36] D. Cao et al., “Deep reinforcement learning enabled physical-model-free two-timescale voltage control method for active distribution systems,” IEEE Trans. Smart Grid, vol. 13, no. 1, pp. 149–165, Jan. 2022.

[37] X. Mei, X. Chu, H. Liu, Y.-W. Leung, and Z. Li, “Energy efficient real-time task scheduling on CPU-GPU hybrid clusters,” in Proc. 2017 IEEE Conf. Comput. Commun., 2017, pp. 1–9.

[38] L. Li, S. Yu, H. Mu, and H. Li, “Optimization and evaluation of CCHP systems considering incentive policies under different operation strategies,” Energy, vol. 162, pp. 825–840, 2018.

[39] Q. Zhang et al., “A survey on data center cooling systems: Technology, power consumption modeling and control strategy optimization,” J. Syst. Archit., vol. 119, 2021, Art. no. 102253.

[40] T. Van Damme, C. De Persis, and P. Tesi, “Optimized thermal-aware job scheduling and control of data centers,” IEEE Trans. Control Syst. Technol., vol. 27, no. 2, pp. 760–771, Mar. 2019.

[41] J. D. Moore, J. S. Chase, P. Ranganathan, and R. K. Sharma, “Making scheduling “cool”: Temperature-aware workload placement in data centers,” in Proc. USENIX Annu. Tech. Conf. Gen. Track, 2005, pp. 61–75.

[42] H. Yuan, J. Bi, J. Zhang, and M. Zhou, “Energy consumption and performance optimized task scheduling in distributed data centers,” IEEE Trans. Syst., Man, Cybern. Syst., vol. 52, no. 9, pp. 5506–5517, Sep. 2022.

[43] S. K. Zaman, T. Maqsood, M. Ali, K. Bilal, S. A. Madani, and A. Khan, “A load balanced task scheduling heuristic for large-scale computing systems,” Comput. Syst. Sci. Eng., vol. 34, 2019, Art. no. 4.

[44] TRNSYS, 2024. [Online]. Available: https://www.trnsys.com

[45] M. Ghamkhari and H. Mohsenian-Rad, “Energy and performance management of green data centers: A profit maximization approach,” IEEE Trans. Smart Grid, vol. 4, no. 2, pp. 1017–1025, Jun. 2013.

[46] J. Bi, H. Yuan, and M. Zhou, “Geographical scheduling of multiapplication tasks for cost minimization in distributed green data centers,” in Proc. 2018 IEEE Int. Conf. Syst. Man Cybern., 2018, pp. 3171–3176.

![](images/e4737b976a2da70b2d46fa7ed7e7bcbe36907a146e561b7a66577489cf1ff3fd.jpg)  
Yongyi Ran (Member, IEEE) received the BS and PhD degrees from the University of Science and Technology of China (USTC), Hefei, China, in 2008 and 2014, respectively. He is currently a lecturer with the Chongqing University of Posts and Telecommunications, China. He worked as a research fellow with the Cloud Computing and Application Platform (CAP) Group, Nanyang Technological University from 2019 to 2020 and worked as a postdoctoral research associate with the University of Massachusetts Lowell from 2017 to 2018. His research interests include deep

reinforcement learning, green data center, and intelligent satellite network.

![](images/dae56a6d888a52dacf10cc07e736bb912c1475f54bb1d7972bc2a692aa3547a5.jpg)

Hui Yin received the BE degree in computer science and technology from Chongqing Three Gorges University, Chongqing, China, in 2024. He is currently working toward the MS degree in communication and information engineering with the Chongqing University of Posts and Telecommunications, Chongqing, China. His current research interests include green data centers, thermal management, and task scheduling.

![](images/7c384426eac135b3ddc36993f59cd28fa6bbe870cc89bb8252ac82ba40ecf700.jpg)

Tongyao Sun received the master’s degree in information and communication engineering from the Chongqing University of Posts and Telecommunications, Chongqing, China, in 2025. Her research interests include green data centers, thermal management, and task scheduling.

![](images/05f4d2aed1f23c0ebcdb94470637b997d5069e1397b5c85c7af20ef9ecf56f0a.jpg)

Jiangtao Luo (Senior Member, IEEE) received the BS degree from Nankai University, in 1993, and the PhD degree from the Chinese Academy of Science, in 1998. Currently, he is a full professor, PhD supervisor and deputy dean with the Electronic Information and Networking Research Institute, Chongqing University of Posts and Telecommunications (CQUPT), China. He has been visiting scholars with the University of Hamburg, Germany and the University of Southern California, USA during 2015 and 2016. His major research interests include vehicular networks,

![](images/f1ac7e8b3053f05c3c8ea641025e0689bcf9753372992a3e184662b638f90c36.jpg)

space-air-ground integrated networking, network data analysis, and future Internet architecture. He has published more than 210 papers and owned 40 patents in these fields. He was awarded the Chinese State Award of Scientific and Technological Progress in 2011, the Chongqing Provincial Award of Scientific and Technological Progress twice in 2010 and 2007, respectively, and the Chongqing Science and Technology Award for Youth in 2010.

and green data center. He received the Industrial Technical Excellence Award of the IEEE Technical Committee on Cyber-Physical Systems in 2020.

Xin Zhou (Member, IEEE) received the ME and PhD degrees from the Department of Information Engineering, Hiroshima University, Japan, in 2013 and 2016, respectively. He is now an associate professor with the School of Artificial Intelligence, Jiangxi Science and Technology Normal University, China. His current research focuses on the learning-based optimization of ICT and cooling subsystems in the data center. His research interests include reconfigurable architectures, parallel computing, parallel architecture, FPGA computing, deep reinforcement learning,

![](images/d020dc08f97a5308b856859d556d923d5f07da2a6c33ebb78c40c0c814c0672c.jpg)

Shuangwu Chen received the BS and PhD degrees from the University of Science and Technology of China (USTC), Hefei, China, in 2011 and 2016, respectively. He is currently an associate professor of the USTC, Hefei, China, and an associate research fellow with the Institute of Artificial Intelligence, Hefei Comprehensive National Science Center, Hefei, China. His research interests include future network, multimedia communication, and stochastic optimization.