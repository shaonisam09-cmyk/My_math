# A Multicriteria Optimal Operation Framework for a Data Center Microgrid Considering Renewable Energy and Waste Heat Recovery

USE OF BALANCED DECISION MAKING

![](images/a343f12714ecad77729d594b48b47be35ab736119b5949286ae652715c0e9924.jpg)  
By Yuanzheng Li , Jingjing Huang , Yun Liu , Hao Wang , Yongzhen Wang , and Xiaomeng Ai

THE RAPID DEVELOPMENT OF DATA CENTERS (DCS) leads to a huge challenge in their energy consumption and environmental impact. It is promising to establish DC microgrids (DCMGs) for solving these issues, considering utilizing renewable energy (RE) generation and waste heat recovery systems. However, the efficient energy management of a DCMG is a topic to be pursued. In this article, we propose a multicriteria optimal operation framework for the DCMG by coordinatively scheduling the energy supply and demand. In this framework, multiple criteria are comprehensively considered via a multicriteria optimization (MCO) approach. Then, we adopt an augmented e-constraint algorithm to address the corresponding MCO problem. Afterward, a balanced decisionmaking (BDM) method is further proposed to determine the optimal scheduling solution. Finally, a case study and results analysis verify the effectiveness of the proposed multicriteria optimal operation framework for the DCMG.

## Introduction

In recent years, DCs have developed rapidly with the digitization of our society [1], [2]. However, they contribute to a huge amount of electricity consumption. For instance, DCs in the United States consumed about 2% of the total power demand in 2013 [3], and this value grows by about 15%–20% per year. Evidently, the tremendous power consumption of DCs leads to a considerable electricity cost [4]. Furthermore, extensive amounts of carbon dioxide are emitted owing to the rising power usage. It is estimated that the carbon emissions caused by DCs are expected to reach 2.6% of that of our world [5].

DCMGs have been recently established to address these challenging issues of electricity cost and environmental impact. Various works have contributed to reduce the cost of DCs, and they can be primarily categorized into two approaches. One is the schedule of DCMGs on the energy supply side [6], [7], [8], [9]. Specifically, [6] presents an operation model for a DCMG that effectively minimizes the electricity cost, via scheduling generation units, as well as the power purchase from the utility grid. Using the strategy of scheduling the power supply system, a stochastic planning scheme for the DCMG is proposed in [7]. This scheme aims to optimize the operational cost under the uncertainties of electricity price and power demand. Also, the electricity cost of a DCMG has been reduced by addressing the problem of optimal power supply by stochastic programming [8]. Moreover, by managing the power supply in distributed DCMGs, optimal short-term operation and long-term planning for minimal electricity cost can be effectively achieved [9].

The other approach is managing energy consumption from the demand side of DCs. In this approach, the IT workloads processed by DCs are commonly classified into real-time and delay-tolerable types. The real-time workload should be handled immediately when it arrives. By contrast, the delay-tolerable workload can be completed at any time before its deadline [10]. Therefore, proper scheduling of such a workload can adjust the profile of energy consumption, thus providing flexibility for reducing the electricity cost [11]. However, it is noted that the real-time workload is stochastic and uncontrollable. Therefore, the length of the waiting queue [12], i.e., the unprocessed delay-tolerable workload, should be as short as possible. This would help DCs provide sufficient serving redundancy for real-time workloads.

Furthermore, to weaken the environmental impact, DCs usually integrate RE generation for better operations [13], [14]. Nevertheless, the uncertain nature of RE leads to difficulties in its full utilization; e.g., some RE power generation might be curtailed. Therefore, to address the problem of RE accommodation in the DCMG, some approaches have been proposed, such as the usage of electric storage system (ESS) management [15], power transactions [16], and IT workload schedules [17]. For instance, [15] has demonstrated that an ESS with sufficient capacity can satisfactorily accommodate RE power generation in a DCMG. Meanwhile, [16] has utilized the approach of power transaction to deal with the varying RE power generation sources, and their utilizations are enhanced. In [17] it was verified that scheduling the IT workload under a serving deadline constraint can contribute to flexible operation of the DCMG while supporting RE accommodation. Note that it is required that more RE should be utilized with the promotion of “green DCs” [18]. Therefore, RE accommodation must be keenly considered for DCMG operations.

However, the aforementioned works refer only to power management, neglecting thermal aspects in DCs. The electricity consumed by DC servers is eventually mainly transformed into heat. The amount of this heat is usually huge, and there exists a great potential for recycling such waste heat. For instance, Stockholm Data Parks, a famous project in Sweden [19], can generate sufficient heat to satisfy 10,000 residential apartments through waste heat recovery. Some researchers have also studied the coordinated operations for DCMGs considering waste heat recovery. For instance, the work in [20] has indicated that DCs could increase the flexibility of system operation and facilitate more RE integration via waste heat recovery. In addition, [21] has proposed an optimization framework to minimize the operational cost of a DCMG, considering the scheduling of waste heat recovery with a thermal storage system (TSS). However, it should be mentioned that the quantity of recycled heat is highly related to the DC power usage, and some heat would not be fully utilized because of the uncertain heat load and waste heat production. For example, the recovered heat is required to be partially abandoned when it is more than the heat load. To make use of this heat as much as possible, the utilization ratio of waste heat should also be enhanced in DCMG operations [22].

In general, the integration of RE power generation and waste heat recovery could enhance the energy efficiency of DCMGs. This could contribute to potential economic and environmental profits. Nevertheless, RE power generation is usually stochastic, which has a significant impact on the reliability of DCMG operations [21]. Therefore, its reliability should be emphasized.

In conclusion, to the best of the author’s knowledge, from investigating previous studies, there are mainly two aspects of research gaps.

1) As we have previously mentioned, the length of the waiting queue regarding the delay-tolerable workload should be minimized to provide sufficient serving redundancy. However, the serving deadline of the delay-tolerable workload has usually been deemed as a constraint in previous studies. Therefore, the length of the waiting queue has not been well studied, and it will be interesting to investigate its impacts on DCMG operations if we regard it as an optimization criterion rather than a constraint.

2) Previous works have usually considered a single criterion in the DCMG operation, i.e., the operational cost. However, because of the complexity of its energy management, pursuing a single criterion might worsen the performance of other aspects, such as the environmental impact or system reliability. Thus, it would be interesting to consider multiple criteria in the DCMG operation.

Therefore, to deal with these gaps, we conduct the following work.

1) Inspired by the calculus theory in the area of wireless data transmission [23], we introduce an arrival–departure curve to describe the delay-tolerable workload. On this basis, we propose the index of a waiting-queue-based quality of service (WQoS) as an optimization criterion.

2) We propose a multicriteria optimal operation framework for an RE-integrated DCMG with waste heat recovery in which the multiple criteria are taken into account. This framework consists of an MCO problem, a solving optimization algorithm, and a BDM method.

3) An augmented e-constraint optimization (AUGMECON) algorithm is introduced to tackle the MCO problem. In addition, a novel BDM method is proposed to determine the final decision for the MCO solutions.

The rest of this article is organized as follows. The next section describes the multicriteria optimal operation framework for the DCMG. The MCO problem formulation of the DCMG is then presented in the section “Formulations of MCO.” The section “Solving Algorithm and Decision-Making Method” describes the solving algorithm and the decisionmaking method. Simulation studies are then presented in the section “Case Study.” The final section draws the conclusions.

## Framework Description

In this article, we propose a multicriteria optimal operation framework for an RE-integrated DCMG with waste heat recovery. First, we briefly introduce the structure of the DCMG in the next section. Then, the section “Multicriteria Optimal Operation Framework” shows the multicriteria optimal operation framework, which is proposed to conduct joint day-ahead scheduling of the energy supply and demand in DCMG operations, considering multiple criteria.

## Structure of DCMG

The structure of the DCMG is shown in Figure  1(a). It mainly comprises three parts, i.e., the power supply system, the thermal supply system, and the DC. The power consumption of the DC mainly stems from servers, and it could be modeled by an IT workload handling process [24]. This power is satisfied via the power supply system and usually transformed into heat that can be recycled by the waste heat recovery system [25]. Furthermore, this recycled heat as well as other thermal resources could be supplied for heat demands, for example, for residential districts. Indeed, DCMG operations can be improved through the coordinated scheduling of power and thermal supply and DC power consumption. The power and thermal energy supply systems as well as the DC power consumption are briefly presented as follows.

## Power and Thermal Supply Systems

In the power supply system, as shown in Figure 1(a), RE resources, including wind and photovoltaic (PV) generation, are major power providers in the DCMG. Also, the ESS is usually equipped as it can provide for flexibility and improve operational reliability. Furthermore, the electricity could be supplemented by purchasing from the main grid and generation units, including power-only units and combined heat and power (CHP) units.

As for the thermal supply system, the recycled heat profile of the DC may deviate from the heat demand since it is highly related to the behaviors of consumers. To better utilize heat resources, a TSS is installed to reserve or release the heat. The CHP units could produce heat energy as well [26]. To ensure a stable heat supply, an electric boiler, which performs as a power consumer as well as a thermal producer, could also provide a heat resource.

## DC Power Consumption

The major power consumption of the DC is contributed by the servers as they should handle various IT workloads. Specifically, the IT workload is divided into two categories, i.e., the real-time and delay-tolerable types. The stochastic real-time workload cannot be scheduled because it should be served immediately. By contrast, the delay-tolerable workload, which is described by an arrival–departure curve in this article, could be scheduled flexibly before the deadline. In other words, this kind of workload can provide time elasticity for power consumption. Then, the total IT workload to be processed is obtained by summing the two types of workloads. Afterward, it is used to model the power consumption of the DC by means of the metric of power usage effectiveness (PUE), which is the ratio of the DC power consumption to the IT workload [27]. Therefore, the DCMG operator could arrange the DC power consumption by scheduling the delay-tolerable workload. This would better adapt to the fluctuating electricity price and accommodate the RE generation. For instance, the DC could handle more workload when the electricity price is low for reducing purchase cost or the RE generation is high.

![](images/7b8508ee1f95f27a8e15e5f278fa38aabf81713a2483243bcd3b4a82062992c2.jpg)  
tolerable; RT: real time; CHP: combined heat and power; VoCE: value of curtailed energy; VoLL: value of loss load. FIGURE 1. Multicriteria optimal operation framework for the DCMG. (a) DCMG structure. (b) Multicriteria optimal operation framework. PV: photovoltaic; PUE: power usage effectiveness; DT: delay

## Multicriteria Optimal Operation Framework

Figure 1(b) illustrates the multicriteria optimal operation framework for the DCMG. This framework consists of the MCO problem, the AUGMECON algorithm, and the BDM method.

## MCO for DCMG Operation

From the perspective of the DCMG operator, the operational cost should be reduced as much as possible. Meanwhile, with the rising popularity of the “green DC,” the reduction of carbon emissions is greatly encouraged. Furthermore, the DC should be instantly ready to deal with the stochastic real-time workload [21]; thus, the server redundancy for handling this workload should be reserved [15]. In other words, the waiting queue of the delay-tolerable workload in the servers should be as short as possible [11]. Therefore, in this article, we propose another criterion, i.e., the WQoS, which is used to quantify the ability of the DC to deal with the real-time workload.

It should also be mentioned that the reliability of the energy supply is a very important aspect in DCMG operations. To be specific, considering the uncertainties of stochastic variables, such as RE power generation, thermal demand, etc., the power and thermal sources might be excessive or insufficient compared with the corresponding load demands. An excessive part would lead to curtailed energy, while an insufficient part would cause a loss load as the energy should be balanced between the supply and demand sides. Therefore, we set two criteria to represent the reliability of the energy supply, i.e., the value of curtailed energy (VoCE) and the value of loss load (VoLL).

As mentioned previously, the MCO problem consists of multiple criteria: the operation cost, carbon emissions, the WQoS, VoCE, and VoLL.

## Solving Algorithm and BDM

The weighting summation method is usually adopted to solve the MCO problem [28]. However, we have to select different combinations of weighting coefficients and assign them on each optimization criterion, and then the MCO problem is converted into a singlecriterion one. Nevertheless, it is not easy to efficiently determine the combinations of such coefficients for a specific problem, and multiple trials are needed, which leads to the inefficiency of this weighting summation method [28]. Therefore, we implement an efficient solving algorithm to solve our proposed MCO problem: the AUGMECON algorithm. This algorithm avoids selecting the multiple combinations of weighting coefficients, and it can directly obtain the Pareto solutions [29]. (Pareto solutions are usually obtained for an MCO problem, and they refer to a set of optimal solutions without a dominated relationship. Note that the dominated relationship exists only if all criterion values for one solution are superior to those of the others.)

After obtaining the Pareto solutions via the algorithm of AUGMECON, we should select the final scheduling solution. This is referred to as the decision-making problem. A traditional fuzzy decision-making (FDM) method [30] is usually used, in which a membership function is adopted and corresponding weights for the multiple criteria are subjectively chosen by system operators. Then, membership values are obtained, and the solution corresponding to the maximum membership value is selected. However, it is largely impacted by criteria weights, which shows that this decision-making method is quite subjective [30]. To overcome this drawback to some extent, we propose a BDM method. Using this method, the weights are automatically obtained by minimizing their impacts on choosing the final solution, rather than by manual selection. This would be better for decision making when considering these multiple criteria.

## Formulations of MCO

In this section, formulations of the proposed MCO problem are presented. The arrival–departure model of the delaytolerable workload and the corresponding WQoS index are explained in the next section. Then, the section “Formulations of Multiple Criteria” describes the objective functions of the multiple criteria. Finally, we present constraints of the DCMG operation in the section “System Constraints.”

## Arrival–Departure Curve Modeling

As we have mentioned, the IT workloads handled by the DC are usually classified into real-time and delay-tolerable types. Of these, the real-time workload should be dealt with instantly, while the delay-tolerable workload should be completed before its due time, which is related to the QoS of the DC. In previous studies, the QoS is usually set as a constraint to be satisfied [11], [31], [32]. However, the DC should be ready to handle the uncertain real-time workload, and thus as much server redundancy as possible is needed [15]. To tackle this problem, we aim to minimize the length of the waiting queue for the delay-tolerable workload, to enhance the server redundancy. Therefore, the WQoS index is proposed as an optimization criterion in the MCO problem. It is defined as the average waiting queue of each time interval and will be expressed in this section.

To better describe the WQoS, we first formulate the delay-tolerable workload based on the network calculus theory [23]. This theory stems from the area of wireless data transmission; the network calculus method is utilized to describe the characteristics of dataflow. Specifically, the arriving packets of data could well be represented by the arrival curve through the cumulation of these data. Meanwhile, all data packets should depart before the transmitting deadline. Then, if those data are transmitted exactly within the deadline constraints, the function of transmitted data quantity with time is defined as the minimum departure curve. It should be mentioned that the delay-tolerable workload in the DC is also characterized by similar features. The delay-tolerable workload arrives in packets while satisfying the departure time limitations. To this end, the arrival–departure curve model is used for describing the delay-tolerable workload in this article.

We define $A ( t ^ { \prime } ) , D ( t ^ { \prime } )$ , and $D _ { \mathrm { m i n } } ( t ^ { \prime } )$ as the arrival, departure, and minimum departure curves for the delaytolerable workload. They are assumed to be right-continuous functions [23] with time $t ^ { \prime } ,$ where $0 \leq t ^ { \prime } \leq T ^ { \prime } , t ^ { \prime } \in \mathbb { R }$ and $T ^ { \prime }$ is the end of the scheduling period. They will be specifically explained as follows.

## Definition 1 (Arrival Curve)

The arrival curve $A ( t ^ { \prime } )$ is the cumulative quantity of delay-tolerable workload that has arrived before $t ^ { \prime } .$ It is expressed as follows:

$$
A (t ^ {\prime}) = \left\{ \begin{array}{l l} 0 & 0 \leq t ^ {\prime} <   1 \\ \sum_ {t = 1} ^ {\text { floor } (t ^ {\prime})} \mathrm{IT} _ {t} ^ {\mathrm{DT}} & 1 \leq t ^ {\prime} \leq T ^ {\prime} \end{array} \right.\tag{1}
$$

where $\Pi _ { t } ^ { \mathrm { D T } }$ is the total delay-tolerable workload that has arrived during the tth time interval, $t = 1 , 2 , . . . , T ,$ and $_ T$ is the number of scheduling time intervals. Since we study the day-ahead scheduling of DCMG operations, the value of $T$ is 24. Note that $A ( t ^ { \prime } ) = 0$ when $t ^ { \prime } \in [ 0 , 1 )$ because of the assumption that the delay-tolerable workload at initial time is zero. floor ( )tl is a function that obtains an integer less than or equal to $t ^ { \prime } .$ Therefore, the arrival curve A t( )l is presented as the piecewise-constant function shown in Figure 2, in the form of the blue line.

## Definition 2 (Departure Curve)

The departure curve D t( )l is defined as the cumulative quantity of delay-tolerable workload that is completed before tl. It is illustrated by the bold red line in Figure 2 and expressed as the following formula:

$$
D (t ^ {\prime}) = \left\{ \begin{array}{l l} 0 & 0 \leq t ^ {\prime} <   1 \\ \sum_ {t = 1} ^ {\text { floor } (t ^ {\prime})} \mathrm{IT} _ {t} ^ {\mathrm{DT}, \mathrm{D}} & 1 \leq t ^ {\prime} \leq T ^ {\prime} \end{array} \right.\tag{2}
$$

![](images/670335926c96ae2a269f319cb04f3084cf7276a83782e9d2948017344f501420.jpg)  
FIGURE 2. Arrival–departure curve modeling.

where I $\mathbf { \chi } _ { t } ^ { \mathrm { { D T } , D } }$ represents the total workload that is handled during the tth time interval.

## Definition 3 (Minimum Departure Curve)

Usually, the delay-tolerable workload should be completed before its deadline; such information will be given when it arrives. Then, we define the minimum departure curve $D _ { \mathrm { m i n } } ( t ^ { \prime } )$ as the departure curve of this workload when it is exactly completed under the deadline constraints. For an $\Pi _ { t } ^ { \mathrm { D T } }$ arriving during the tth time interval, the corresponding time duration for completion ${ \bf d } _ { t }$ can be obtained by subtracting the arrival time from the deadline. For instance, if a batch of workloads arrived during the third interval, and the corresponding deadline is at the end of the eighth interval, its time duration is a time span of five intervals. Herein, we adopt d<sub>floor(</sub> <sub>)t</sub>l to denote the time duration for the workload arriving during the floor ( ) tl th time interval. Therefore, $D _ { \mathrm { m i n } } ( t ^ { \prime } )$ is represented by the black line in Figure 2. It can also be expressed by the following formula:

$$
D _ {\min} \left(t ^ {\prime}\right) = \left\{ \begin{array}{l l} 0 & 0 \leq t ^ {\prime} <   \mathrm{d} _ {1} + 1 \\ A \left(t ^ {\prime} - \mathrm{d} _ {\text { floor } \left(t ^ {\prime}\right)}\right) & \mathrm{d} _ {1} + 1 \leq t ^ {\prime} <   T ^ {\prime} \\ A \left(T ^ {\prime}\right) & t ^ {\prime} = T ^ {\prime} \end{array} \right.\tag{3}
$$

where d<sub>1</sub> stands for the time duration of the delay-tolerable workload that arrived during the first time interval.

Since the quantity of delay-tolerable workload completed before $t ^ { ' }$ cannot exceed that of the arrived one, and the workload should be executed within its time duration, the departure curve of the workload is constrained by the following formulation:

$$
D _ {\min} (t ^ {\prime}) \leq D (t ^ {\prime}) \leq A (t ^ {\prime}).\tag{4}
$$

Note that the difference between $A ( t ^ { \prime } )$ and $D ( t ^ { \prime } )$ is the quantity of delay-tolerable workload that is not handled. Therefore, the length of the waiting queue for the delaytolerable workload at time $t ^ { ' }$ is formulated as follows [33]:

$$
Q ^ {\mathrm{DT}} (t ^ {\prime}) = A (t ^ {\prime}) - D (t ^ {\prime}).\tag{5}
$$

We can see that $\boldsymbol { Q } ^ { \mathrm { D T } } ( t ^ { \prime } )$ is also a right-continuous function, which is illustrated by the height of the shaded area in Figure 2. Therefore, the length of the waiting queue at the end of the tth time interval, represented as ${ Q } ^ { \mathrm { { D T } } } ( t ) .$ , can be obtained based on (5). As we have mentioned in the section “Framework Description,” the server redundancy in the DC should be as high as possible, which means that ${ \boldsymbol { Q } } ^ { \mathrm { { D T } } } ( t )$ , should be reduced. The shorter the waiting queue, the better the QoS of the DC, so we define the WQoS index as follows:

$$
\mathrm{WQoS} = \frac {\sum_ {t = 1} ^ {T} Q ^ {\mathrm{DT}} (t)}{T}.\tag{6}
$$

It can be seen that the departure curve $D ( t ^ { \prime } )$ will change by arranging the $\Pi _ { t } ^ { \mathrm { D T } , \mathrm { D } }$ , and thus the WQoS will be influenced. As also shown in Figure  2, we present another departure curve $D ^ { * } ( t ^ { \prime } )$ with a different scheduling of $\Pi _ { t } ^ { \mathrm { D T } , \mathrm { D } }$ .  It is observed that the waiting queue regarding $D ^ { * } ( t ^ { \prime } )$ during each time interval is shorter; thus, the WQoS is evidently better than that of $D ( t ^ { \prime } )$ However, the optimization of WQoS may worsen other criteria. For instance, the operational cost corresponding to $D ^ { * } ( t ^ { \prime } )$ may be higher if the workload is handled in higher priced time intervals. Therefore, we should consider the WQoS as well as other criteria for DCMG operations.

## Formulations of Multiple Criteria

In this section, we present formulations of multiple criteria in the proposed MCO problem. The multiple criteria include operational cost, carbon emissions, WQoS, abandoned heat resource, and RE curtailment. Note that these criteria are formulated based on the forecasting information of DCMG operations, such as wind and PV power generation, real-time workload, and heat demand. However, it is not easy to accurately forecast these variables because of their associated uncertainties [34]. In this article, these uncertainties are represented via forecasting errors, which are assumed to obey a Gaussian distribution [35]. Using the information of forecasting values and errors, probabilistic scenarios $\xi _ { t , s }$ are generated by a Latin hypercube sampling simulation [21], [36], expressed as follows:

$$
\xi_ {t, s} = [ P _ {t, s} ^ {\text { wind }}, P _ {t, s} ^ {\text { PV }}, \text { IT } _ {t, s} ^ {\text { RT }}, H _ {t, s} ^ {\text { load }} ] \quad \forall t, s\tag{7}
$$

where $P _ { t , s } ^ { \mathrm { w i n d } } , P _ { t , s } ^ { \mathrm { P V } } , \Pi _ { t , s } ^ { \mathrm { R T } }$ , and $H _ { t , s } ^ { \mathrm { l o a d } }$ represent the wind and $\mathbf { P V }$ power generation, real-time workload, and heat load in the time interval t regarding scenario $s \left( s = 1 , 2 , . . . , S \right)$ respectively. S is the total number of scenarios.

In the proposed MCO problem, x represents the vector of decision variables, including the power output of generation units $P _ { u , t } ^ { \mathrm { u n i t } }$ ,  power usage of boilers $P _ { t } ^ { \mathrm { b o i } }$ , electricity purchases $P _ { t } ^ { \mathrm { g r i d } }$ ,  power charging of the ESS $P _ { t } ^ { \mathrm { c h a r } }$ ,  power discharging of the ESS $P _ { t } ^ { \mathrm { d i s c } }$ ,  heat reserving of the TSS $H _ { t } ^ { \mathrm { i n } }$ , and heat releasing of the TSS $H _ { t } ^ { \mathrm { o u t } }$ as well as all related binary variables x, which will be introduced accordingly with details. Therefore, the vector of decision variables is denoted as $\pmb { x } = [ P _ { u , t } ^ { \mathrm { u n i t } } , P _ { t } ^ { \mathrm { b o i } } , P _ { t } ^ { \mathrm { g r i d } } , P _ { t } ^ { \mathrm { d i s c } } , P _ { t } ^ { \mathrm { c h a r } } , H _ { t } ^ { \mathrm { o u t } } , H _ { t } ^ { \mathrm { i n } } , \Pi _ { t } ^ { \mathrm { D T , D } }$ , ]. x Note that the generation units in the DCMG include power-only and CHP units, which are denoted as $P _ { m , t } ^ { \mathrm { p o w e t } }$ and $P _ { n , t } ^ { \mathrm { C H P } }$ , respectively. In this way, the total operational cost is formulated as follows and is to be minimized as an optimization criterion:

$$
F _ {1} (\boldsymbol {x}, \xi_ {t, s}) = \sum_ {t = 1} ^ {T} \sum_ {u = 1} ^ {U} C _ {u, t} ^ {\text { unit }} (\boldsymbol {x}) + \sum_ {t = 1} ^ {T} C _ {t} ^ {\text { grid }} (\boldsymbol {x}).\tag{8}
$$

The operational costs with respect to the generation units and electricity purchases are presented as follows:

$$
\begin{array}{c} C _ {u, t} ^ {\text {unit}} (\boldsymbol {x}) = \alpha_ {u} + \beta_ {u} \cdot P _ {u, t} ^ {\text {unit}} + \gamma_ {u} \cdot H _ {u, t} ^ {\text {unit}} \\ \qquad + \mathrm{SU} _ {u} \cdot \tau_ {u, t} ^ {\text {su}} + \mathrm{SD} _ {u} \cdot \tau_ {u, t} ^ {\text {sd}} \\ C _ {t} ^ {\text {grid}} (\boldsymbol {x}) = \pi_ {t} ^ {\text {grid}} \cdot P _ {t} ^ {\text {grid}} \end{array}\tag{9}
$$

(10)

where $\alpha _ { u } , ~ \beta _ { u } ,$ and $\gamma _ { u }$ are coefficients of operational cost for generation unit $u , u = 1 , 2 , . . . , U .$ U is the total number of generation units. $\operatorname { s u }$ and $\operatorname { s D } _ { u }$ represent the start-up and shutdown cost for unit $^ { u , }$ respectively. $\tau _ { u , t } ^ { \mathrm { s u } }$ and $\tau _ { u , t } ^ { \mathrm { s d } }$ are binaries indicating if unit u is start-up or shutdown at time $t . \ \pi _ { t } ^ { \mathrm { g r i d } }$ denotes the electricity price in time interval t.

Meanwhile, the carbon emissions of the DCMG should be reduced with the promotion of “green DCs.” Specifically, the carbon emission is mainly caused by diesel-based power generation and coal-based electricity purchases [37]. It is formulated as follows:

$$
F _ {2} (\boldsymbol {x}, \xi_ {t, s}) = \sum_ {t = 1} ^ {T} \sum_ {u = 1} ^ {U} (e _ {u} ^ {\text { unit }} \cdot P _ {u, t} ^ {\text { unit }}) + \sum_ {t = 1} ^ {T} (e ^ {\text { grid }} \cdot P _ {t} ^ {\text { grid }})\tag{11}
$$

where $e _ { u } ^ { \mathrm { u n i t } }$ and $e ^ { \mathrm { g r i d } }$ represent the carbon emission rates for power generated by unit u and electricity purchased from the main grid, respectively.

As mentioned in the section “Arrival–Departure Curve Modeling,” the length of the waiting queue for the delay-tolerable workload should be as short as possible. Therefore, we propose the WQoS index as another optimization criterion.

$$
F _ {3} (\boldsymbol {x}, \xi_ {t, s}) = \frac {\sum_ {t = 1} ^ {T} Q ^ {\mathrm{DT}} (t)}{T}.\tag{12}
$$

As the reliability of the energy supply is important for DCMG operations, we set two criteria to represent the reliability of the energy supply: VoCE and VoLL. They are denoted as $F _ { 4 }$ and $F _ { 5 } ,$ respectively. VoCE is composed of RE curtailment and heat abandonment and is formulated as follows:

$$
F _ {4} (\boldsymbol {x}, \xi_ {t, s}) = \sum_ {t = 1} ^ {T} \sum_ {s = 1} ^ {S} \lambda_ {s} \cdot [ H _ {t, s} ^ {\text { sur }} (\boldsymbol {x}, \xi_ {t, s}) + P _ {t, s} ^ {\text { cur }} (\boldsymbol {x}, \xi_ {t, s}) ]\tag{13}
$$

where $\lambda _ { s }$ denotes the probability of scenario s. $H _ { t , s } ^ { \mathrm { s u r } } ( { \pmb x } , { \pmb \xi } _ { t , s } )$ and $P _ { t , s } ^ { \mathrm { c u r } } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ represent the heat surplus to be abandoned and the RE curtailment for scenario s in time interval t.

In addition, the VoLL is formulated as follows:

$$
F _ {5} (\boldsymbol {x}, \xi_ {t, s}) = \sum_ {t = 1} ^ {T} \sum_ {s = 1} ^ {S} \lambda_ {s} \cdot [ H _ {t, s} ^ {\text { loss }} (\boldsymbol {x}, \xi_ {t, s}) + P _ {t, s} ^ {\text { loss }} (\boldsymbol {x}, \xi_ {t, s}) ]\tag{14}
$$

where $H _ { t , s } ^ { \mathrm { l o s s } } ( { \pmb x } , { \pmb \xi } _ { t , s } )$ and $P _ { t , s } ^ { \mathrm { l o s s } } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ represent the heat load loss and the power load loss regarding scenario $s .$ $H _ { t , s } ^ { \mathrm { s u r } } ( \pmb { x } , \xi _ { t , s } ) , P _ { t , s } ^ { \mathrm { c u r } } ( \pmb { x } , \xi _ { t , s } ) , H _ { t , s } ^ { \mathrm { l o s s } } ( \pmb { x } , \xi _ { t , s } )$ ,  and $P _ { t , s } ^ { \mathrm { l o s s } } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ are specifically formulated as follows:

$$
H _ {t, s} ^ {\mathrm{sur}} = \max \{0, \mathrm{UH} _ {t, s} \} \quad \forall t, s\tag{15}
$$

$$
P _ {t, s} ^ {\text { cur }} = \max \{0, \mathrm{UP} _ {t, s} \} \quad \forall t, s\tag{16}
$$

$$
H _ {t, s} ^ {\text { loss }} = - \min \{0, \mathrm{UH} _ {t, s} \} \quad \forall t, s\tag{17}
$$

$$
P _ {t, s} ^ {\text { loss }} = - \min \{0, \mathrm{UP} _ {t, s} \} \quad \forall t, s\tag{18}
$$

where

$$
\begin{array}{l} \mathrm{UH} _ {t, s} = \left(\sum_ {n = 1} ^ {N} H _ {n, t} ^ {\mathrm{CHP}} + H _ {t, s} ^ {\mathrm{DC}} + \tau_ {t} ^ {\text {out}} H _ {t} ^ {\text {out}} + H _ {t} ^ {\text {boi}}\right) \\ - \left(H _ {t, s} ^ {\text {load}} + \tau_ {t} ^ {\text {in}} H _ {t} ^ {\text {in}}\right) \quad \forall t, s \\ \mathrm{UP} _ {t, s} = \left(\sum_ {m = 1} ^ {M} P _ {m, t} ^ {\text {power}} + \sum_ {n = 1} ^ {N} P _ {n, t} ^ {\mathrm{CHP}} + P _ {t, s} ^ {\text {wind}} + P _ {t, s} ^ {\mathrm{PV}} + P _ {t} ^ {\text {grid}} + \tau_ {t} ^ {\text {disc}} P _ {t} ^ {\text {disc}}\right) \\ - \left(P _ {t, s} ^ {\mathrm{DC}} + \tau_ {t} ^ {\text {char}} P _ {t} ^ {\text {char}} + P _ {t} ^ {\text {boi}}\right) \quad \forall t, s \end{array} \tag {20}
$$

in which $\mathrm { U H } _ { t , s }$ and $\mathrm { U P } _ { t , s }$ are the differences between supply and demand for the heat and power, respectively. Moreover, $\tau _ { t } ^ { \mathrm { i n } }$ and $\tau _ { t } ^ { \mathrm { { o u t } } }$ are binary variables indicating if the TSS is reserving or releasing heat in time interval t. Likewise, $\tau _ { t } ^ { \mathrm { c h a r } }$ and $\tau _ { t } ^ { \mathrm { d i s c } }$ are binary variables indicating if the ESS is charging or discharging. Note that $P _ { t , s } ^ { \mathrm { D C } }$ and $H _ { t , s } ^ { \mathrm { { D C } } }$ are quantities of power usage and recycled heat production of the DC, the details of which are shown in the section “System Constraints.”

It is noted that (15)–(18) are nonlinear equations that are not easy to address directly. Then, we introduce $\tau _ { t , s } ^ { \mathrm { s u r } }$ and $\tau _ { t , s } ^ { \mathrm { c u r } }$ to indicate whether the heat and power supply are excessive or not, as shown in (21). When these binary variables are equal to one, it means that the supply needs to be abandoned or curtailed. They can then be linearized by the integer programming technique [38], formulated as (22) and (23).

$$
\begin{array}{l l} \left\{ \begin{array}{l l} 0 \leq \mathrm{UH} _ {t, s} ^ {+} \leq \tau_ {t, s} ^ {\text { sur }} \mathrm{UH} _ {\max} \\ 0 \leq \mathrm{UH} _ {t, s} ^ {-} \leq (1 - \tau_ {t, s} ^ {\text { sur }}) \mathrm{UH} _ {\max} \end{array} \right. & \forall t, s \\ \left\{ \begin{array}{l l} 0 \leq \mathrm{UP} _ {t, s} ^ {+} \leq \tau_ {t, s} ^ {\text { cur }} \mathrm{UP} _ {\max} \\ 0 \leq \mathrm{UP} _ {t, s} ^ {-} \leq (1 - \tau_ {t, s} ^ {\text { cur }}) \mathrm{UP} _ {\max} \end{array} \right. & \forall t, s \end{array}\tag{21}
$$

$$
\begin{array}{l} \mathrm{UH} _ {t, s} ^ {+} - \mathrm{UH} _ {t, s} ^ {-} = \left(\sum_ {n = 1} ^ {N} H _ {n, t} ^ {\mathrm{CHP}} + H _ {t, s} ^ {\mathrm{DC}} + \tau_ {t} ^ {\text { out }} H _ {t} ^ {\text { out }} + H _ {t} ^ {\text { boi }}\right) \\ \quad - \left(H _ {t, s} ^ {\text { load }} + \tau_ {t} ^ {\text { in }} H _ {t} ^ {\text { in }}\right) \quad \forall t, s \end{array}\tag{22}
$$

$$
\begin{array}{l} \mathrm{UP} _ {t, s} ^ {+} - \mathrm{UP} _ {t, s} ^ {-} = \left(\sum_ {m = 1} ^ {M} P _ {m, t} ^ {\text { power }} + \sum_ {n = 1} ^ {N} P _ {n, t} ^ {\text { CHP }} + P _ {t} ^ {\text { grid }} + P _ {t, s} ^ {\text { wind }} + P _ {t, s} ^ {\text { PV }} \right. \\ \left. + \tau_ {t} ^ {\text { disc }} P _ {t} ^ {\text { disc }}\right) - (P _ {t, s} ^ {\text { DC }} + \tau_ {t} ^ {\text { char }} P _ {t} ^ {\text { char }} + P _ {t} ^ {\text { boi }}) \quad \forall t, s \end{array}\tag{23}
$$

where $\mathrm { U H } _ { t , s } ^ { + }$ and $\mathrm { U P } _ { t , s } ^ { + }$ are the ancillary variables denoting the quantities of excessive heat and power supply. UH<sub>t</sub> <sub>s,</sub> and $\mathrm { U P } _ { t , s } ^ { - }$ represent insufficient heat and power supply, with upper bounds of $\mathrm { U H } _ { \operatorname* { m a x } }$ and $\mathrm { U P } _ { \operatorname* { m a x } }$ . With the help of these variables, $F _ { 4 } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ and $F _ { 5 } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ are reformulated in deterministic form.

$$
F _ {4} (\boldsymbol {x}, \xi_ {t, s}) = \sum_ {t = 1} ^ {T} \sum_ {s = 1} ^ {S} \lambda_ {s} \cdot \left(\mathrm{UH} _ {t, s} ^ {+} + \mathrm{UP} _ {t, s} ^ {+}\right)\tag{24}
$$

$$
F _ {5} (\pmb {x}, \xi_ {t, s}) = \sum_ {t = 1} ^ {T} \sum_ {s = 1} ^ {S} \lambda_ {s} \cdot (\mathrm{UH} _ {t, s} ^ {-} + \mathrm{UP} _ {t, s} ^ {-}).\tag{25}
$$

## System Constraints

The main constraints for the DCMG operation are introduced as follows.

## Power Balance

The power supply should be sufficient and satisfy the demand for all of the time intervals [39]. This constraint is shown as (26).

$$
\begin{array}{l} \sum_ {m = 1} ^ {M} P _ {m, t} ^ {\text { power }} + \sum_ {n = 1} ^ {N} P _ {n, t} ^ {\text { CHP }} + \tau_ {t} ^ {\text { disc }} P _ {t} ^ {\text { disc }} + P _ {t} ^ {\text { grid }} + P _ {t} ^ {\text { wind }} + P _ {t} ^ {\text { PV }} \\ = P _ {t} ^ {\text { DC }} + \tau_ {t} ^ {\text { char }} P _ {t} ^ {\text { char }} + P _ {t} ^ {\text { boi }} \quad \forall t \end{array}\tag{26}
$$

where $P _ { t } ^ { \mathrm { w i n d } }$ and $P _ { t } ^ { \mathrm { P V } }$ are the output forecasts for wind and PV power at time interval t.

## Heat Balance

Likewise, thermal resources are required to satisfy the heat demand for all time intervals, as described by

$$
\sum_ {n = 1} ^ {N} H _ {n, t} ^ {\mathrm{CHP}} + \tau_ {t} ^ {\text { out }} H _ {t} ^ {\text { out }} + H _ {t} ^ {\mathrm{DC}} + H _ {t} ^ {\text { boi }} = H _ {t} ^ {\text { load }} + \tau_ {t} ^ {\text { in }} H _ {t} ^ {\text { in }} \quad \forall t\tag{27}
$$

in which $H _ { t } ^ { \mathrm { { D C } } }$ and $H _ { t } ^ { \mathrm { l o a d } }$ are forecasted values for recycled heat in the DC and heat load.

## Constraints of Generation Units

The capacity and ramping rate limitations of the generation units [40], including both power-only and CHP units, are presented here and formulated as (28)–(30).

$$
\tau_ {u, t} P _ {u, \min} ^ {\text { unit }} \leq P _ {u, t} ^ {\text { unit }} \leq \tau_ {u, t} P _ {u, \max} ^ {\text { unit }} \quad \forall t, u\tag{28}
$$

$$
- \mathrm{R} _ {u} \leq P _ {u, t + 1} ^ {\text { unit }} - P _ {u, t} ^ {\text { unit }} \leq \mathrm{R} _ {u} \quad \forall t, u\tag{29}
$$

$$
P _ {n, t} ^ {\mathrm{CHP}} = \phi_ {n} H _ {n, t} ^ {\mathrm{CHP}}\tag{30}
$$

where $\tau _ { u , t }$ indicates whether unit u is operating or not in time interval $t . \ P _ { u , \operatorname* { m i n } } ^ { \mathrm { u n i t } }$ and $P _ { u , \mathrm { m a x } } ^ { \mathrm { u n i t } }$ are the minimum and maximum power outputs of unit u. ${ \bf R } _ { u }$ is the corresponding ramping up/down rate. Moreover, $\phi _ { n }$ is the coefficient factor for CHP unit n.

## ESS and TSS

The ESS and TSS are widely used in the electric–thermal hybrid system to support the reliability of the energy supply and scheduling flexibility. To illustrate the charging (reserving) and discharging (releasing) process of the ESS (TSS), binary variables are introduced to indicate their operation states. Therefore, the process of charging and discharging of the ESS and TSS are constrained by the following formulations [41], [42]:

$$
\left\{ \begin{array}{l} \mathrm{ES} _ {t + 1} = \mathrm{ES} _ {t} + \eta_ {\text {char}} P _ {t} ^ {\text {char}} - \eta_ {\text {disc}} P _ {t} ^ {\text {disc}} \\ \mathrm{ES} _ {\min} \leq \mathrm{ES} _ {t} \leq \mathrm{ES} _ {\max} \\ \tau_ {t} ^ {\text {char}} + \tau_ {t} ^ {\text {disc}} \leq 1 \\ 0 \leq P _ {t} ^ {\text {char}} \leq \tau_ {t} ^ {\text {char}} P _ {\max} ^ {\text {char}} \\ 0 \leq P _ {t} ^ {\text {disc}} \leq \tau_ {t} ^ {\text {disc}} P _ {\max} ^ {\text {disc}} \end{array} \right. \quad \forall t\tag{31}
$$

$$
\left\{ \begin{array}{l} \mathrm{TS} _ {t + 1} = \mathrm{TS} _ {t} + \eta_ {\text { in }} H _ {t} ^ {\text { in }} - \eta_ {\text { out }} H _ {t} ^ {\text { out }} \\ \mathrm{TS} _ {\min} \leq \mathrm{TS} _ {t} \leq \mathrm{TS} _ {\max} \\ \tau_ {t} ^ {\text { in }} + \tau_ {t} ^ {\text { out }} \leq 1 \\ 0 \leq H _ {t} ^ {\text { in }} \leq \tau_ {t} ^ {\text { in }} H _ {\max} ^ {\text { in }} \\ 0 \leq H _ {t} ^ {\text { out }} \leq \tau_ {t} ^ {\text { out }} H _ {\max} ^ {\text { out }} \end{array} \right. \quad \forall t\tag{32}
$$

where $\mathbf { E S } _ { t }$ is the electricity storage state in time interval t. $\eta _ { \mathrm { c h a r } }$ and $\eta _ { \mathrm { d i s c } }$ refer to the charging and discharging efficiencies of the ESS. $\mathbf { E S } _ { \mathrm { m i n } }$ and $\mathbf { E S } _ { \operatorname* { m a x } }$ are the minimum and maximum electricity storage of the ESS, and $P _ { \mathrm { m a x } } ^ { \mathrm { c h a r } }$ and $P _ { \mathrm { m a x } } ^ { \mathrm { d i s c } }$ represent the maximum charging and discharging rates of the ESS. In addition, $\mathbf { T } \mathbf { S } _ { t }$ is the quantity of the thermal storage state in time interval t. h<sub>in</sub> and $\eta _ { \mathrm { o u t } }$ refer to the reserving and releasing efficiencies of the TSS. Also, $\mathbf { T S } _ { \mathrm { m i n } }$ and $\mathrm { T S } _ { \mathrm { m a x } }$ are the minimum and maximum electricity storage of the TSS. $H _ { \mathrm { m a x } } ^ { \mathrm { i n } }$ and $H _ { \mathrm { m a x } } ^ { \mathrm { o u t } }$ represent the maximum reserving and releasing rates of the TSS, respectively.

## Constraints of Workload

The total workload to be processed in each time interval is calculated as follows [27]:

$$
\mathrm{IT} _ {t, s} ^ {\text { load }} = \mathrm{IT} _ {t, s} ^ {\text { RT }} + \mathrm{IT} _ {t} ^ {\text { DT,D }} \quad \forall t, s\tag{33}
$$

$$
0 \leq \mathrm{IT} _ {t, s} ^ {\text { load }} \leq C ^ {\mathrm{IT}} \quad \forall t, s\tag{34}
$$

in which $C ^ { \mathrm { I T } }$ is the maximum processing capacity for handling the IT workload. With the adoption of the PUE metric, the power consumption of the DC is obtained by the IT workload, formulated as

$$
P _ {t, s} ^ {\mathrm{DC}} = L \left[ P _ {\text {idle}} + (\mathrm{PUE} - 1) P _ {\text {peak}} + (P _ {\text {peak}} - P _ {\text {idle}}) \mathrm{IT} _ {t, s} ^ {\text {load}} \right] \forall t, s\tag{35}
$$

where L is the number of servers in the $\mathrm { D C } ,$ and $P _ { \mathrm { p e a k } }$ and $P _ { \mathrm { i d l e } }$ are the power consumption of a single server in the peak and the idle state, respectively.

Moreover, the quantity of recycled waste heat is related to the power usage of the DC, which is formulated as follows:

$$
H _ {t, s} ^ {\mathrm{DC}} = \kappa P _ {t, s} ^ {\mathrm{DC}} \quad \forall t, s\tag{36}
$$

in which l is the efficiency of the waste heat recovery $\bf { S y s - }$ tem in the DC.

Therefore, based on the section “Formulations of Multiple Criteria” and this section, “System Constraints,” our proposed MCO problem is formulated as follows:

$$
\begin{array}{l} \min \left\{F _ {1}, F _ {2}, F _ {3}, F _ {4}, F _ {5} \right\} \\ \text { s.t. } (7) - (1 4); (2 6) - (3 6). \end{array}\tag{37}
$$

In conclusion, multiple criteria shall be taken into account for DCMG operations, considering operational cost, carbon emissions, WQoS, abandoned waste heat, and RE curtailment. They are minimized at the same time, while various operational constraints of the DCMG are satisfied.

## Solving Algorithm and Decision-Making Method

In this section, we implement an efficient AUGMECON algorithm to solve the proposed MCO problem. The details of this algorithm are presented in the next section. Via this algorithm, Pareto solutions of the MCO can be obtained. We then propose the BDM method to determine the final scheduling solution, as shown in the section “BDM Method.”

## AUGMECON

The AUGMECON algorithm is adopted to solve our mixedinteger linear MCO problem. The basic procedure of this algorithm is described as follows. The first step is to obtain the payoff table, which gives numerical ranges of multiple criteria values in the MCO problem by using lexicographic optimization [43]. Specifically, the lexicographic optimization is conducted in the following steps, as shown in Figure 3. First of all, we optimize the first criterion without considering the impacts of the other criteria and obtain the optimal criterion value as $F _ { 1 } = F _ { 1 } ^ { * }$ . Then, this value is assigned to (1, 1) of the payoff table. Afterward, the second criterion is optimized while considering $F _ { 1 } = F _ { 1 } ^ { * }$ as a constraint. Thus, we can obtain the optimal value as $F _ { 2 } = F _ { 2 } ^ { * }$ , which is sent to (1, 2) of the table. Furthermore, $( 1 , 3 )$ stores the optimal value of the third criterion constrained by $F _ { 1 } = F _ { 1 } ^ { * } , F _ { 2 } = F _ { 2 } ^ { * }$ . As we take five criteria into account, we repeat this procedure until it has looped through all criteria, and the five values can be obtained. These values are saved in the first row of the payoff table. To obtain the second row of this table, the same procedure is conducted as we have just presented. Note that there exists a tiny difference in the second loop, which is to optimize $F _ { 2 }$ without considering others first, as illustrated in Figure  3. With the mentioned optimization principle, other rows can be also obtained for the payoff table.

The second step of this algorithm is to optimize a single criterion by transforming the others into constraints. To this end, the range of criterion values $r _ { i } ( i = 1 , 2 , . . . , I )$ can be determined by the difference between the maximum and minimum values of the ith column in the payoff table, i.e., $r _ { i } = F _ { i } ^ { \operatorname* { m a x } } - F _ { i } ^ { \operatorname* { m i n } }$ . Then, the decision maker should predefine a parameter $G ,$ which is the number of grid points regarding each criterion. If the points are equally distributed over the range $r _  i , $ the value $e _ { i } ^ { g }$ is presented as follows:

$$
e _ {i} ^ {g} = \left\{ \begin{array}{l l} e _ {i} ^ {g - 1} + \frac {F _ {i} ^ {\max} - F _ {i} ^ {\min}}{G - 1} & g = 2, \dots , G \\ F _ {i} ^ {\min} & g = 1 \end{array} \right. (i = 2, \dots , 5).\tag{38}
$$

Therefore, the MCO problem solved via AUGMECON is reformulated as follows:

$$
\begin{array}{l} \min \left\{F _ {1} (\boldsymbol {x}, \xi_ {t, s}) - \epsilon \left(\frac {s _ {2}}{r _ {2}} + \frac {s _ {3}}{r _ {3}} + \frac {s _ {4}}{r _ {4}} + \frac {s _ {5}}{r _ {5}}\right) \right\} \\ \text { s.t. } \left\{ \begin{array}{l l} F _ {2} (\boldsymbol {x}, \xi_ {t, s}) + s _ {2} = e _ {2} ^ {g} \\ F _ {3} (\boldsymbol {x}, \xi_ {t, s}) + s _ {3} = e _ {3} ^ {g} \\ F _ {4} (\boldsymbol {x}, \xi_ {t, s}) + s _ {4} = e _ {4} ^ {g} \\ F _ {5} (\boldsymbol {x}, \xi_ {t, s}) + s _ {5} = e _ {5} ^ {g} \end{array} \right. \quad g = 1, 2,..., G \end{array}\tag{39}
$$

where $\boldsymbol { s } _ { i }$ is the slack variable, and $\epsilon$ is a specific small number. Since $F _ { 2 } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ through $F _ { 5 } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ are considered as constraints in this algorithm, accordingly, the total number of grid points is $G ^ { 4 }$

## BDM Method

Using the AUGMECON algorithm, Pareto solutions of the MCO problem can be obtained. However, it is still difficult to choose the final solution. Usually, the FDM method is used. In this method, each Pareto solution is evaluated by a normalized membership value, which is determined by the fuzzy membership function and the weights of multiple criteria. To be specific, each criterion value of a Pareto solution is normalized to [0, 1] through the function, and then the final solution is selected according to the membership values, which are dependent on the weights. Nevertheless, these weights of the multiple criteria are usually set by decision makers with their own subjective experiences. In this way, preferences among multiple criteria may exist, and larger weights are assigned to some criteria. Then, the final selected solution will perform better for these preferred criteria, but worse for others. This leads to unbalanced decision making.

![](images/8b573b1aa12ceda6c165781e3bcc2b128ecfa6b139280d71ed301703e48a2d4e.jpg)  
FIGURE 3. The procedure of obtaining the payoff table.

Also, it is not easy to choose suitable weights at once via manual selection.

Therefore, to overcome these drawbacks, we propose a BDM method. The weights are determined through an optimization approach using normalized membership values. First, these values are calculated by the fuzzy membership function [44]:

$$
\mu_ {i} \left(\boldsymbol {F} ^ {k}\right) = \left\{ \begin{array}{l l} 0 & \text { if } F _ {i} ^ {k} \geq F _ {i} ^ {\max} \\ \frac {F _ {i} ^ {\max} - F _ {i} ^ {k}}{F _ {i} ^ {\max} - F _ {i} ^ {\min}} & \text { if } F _ {i} ^ {\min} <   F _ {i} ^ {k} <   F _ {i} ^ {\max} \\ 1 & \text { if } F _ {i} ^ {k} \leq F _ {i} ^ {\min} \end{array} \right.\tag{40}
$$

where $\mu _ { i } ( \pmb { F } ^ { k } )$ is the membership value of the ith criterion regarding the kth solution $\mathbf { \Delta } F ^ { k } , \mathbf { \Delta } F _ { i } ^ { \mathrm { m a x } }$ and $F _ { i } ^ { \mathrm { { m i n } } }$ are the maximum and minimum values in terms of the ith criterion, respectively.

Thus, the normalized membership value of the kth Pareto solution is calculated by

$$
\mu (\boldsymbol {F} ^ {k}, \omega) = \frac {\sum_ {i = 1} ^ {I} \omega_ {i} \mu_ {i} (\boldsymbol {F} ^ {k})}{\sum_ {k = 1} ^ {K} \sum_ {i = 1} ^ {I} \omega_ {i} \mu_ {i} (\boldsymbol {F} ^ {k})}\tag{41}
$$

where $\omega = \{ \omega _ { i } \} , 0 < \omega _ { i } < 1$ ,  and $\omega _ { i }$ stands for the weight indicating the preference of the ith criterion, and K and I represent the number of Pareto solutions and multiple criteria, respectively. The normalized membership values of all Pareto solutions can be represented as $M ( \omega ) = ( \mu ( \pmb { F } ^ { 1 } , \omega ) , \mu ( \pmb { F } ^ { 2 } , \omega ) , . . . , \mu ( \pmb { F } ^ { K } , \omega ) )$ . A traditional FDM method selects the solution with the maximum normalized membership value as the final schedule [45].

Table 1. DC system parameters

<table><tr><td>Number of Servers</td><td> $P_{idle}$ (W)</td><td> $P_{peak}$ (W)</td><td>PUE</td><td>Peak Power Consumption(MW)</td><td> $\kappa$ </td></tr><tr><td> $3.5 \times 10^{5}$ </td><td>100</td><td>200</td><td>1.3</td><td>91</td><td>70%</td></tr></table>

However, this method largely relies on the weights of the criteria, which are usually set by decision makers and prone to subjectiveness. To overcome this drawback and reduce the impact of weights on the normalized membership values, we aim to minimize their range by optimizing the weights \~ rather than the manual setting. Therefore, the BDM is formulated as follows:

$$
\begin{array}{l l} \min _ {\omega} & \{\max (M (\omega)) - \min (M (\omega)) \} \\ \text {s.t.} & \sum \omega_ {i} = 1, i = 1,..., I. \end{array}\tag{42}
$$

Afterward, we can use dynamic programming to solve (42) and find the optimal weights \~<sup>)</sup> in (43). Subsequently, the final solution and corresponding criteria values are obtained as follows:

$$
\boldsymbol {F} ^ {*} = \underset {\boldsymbol {F} ^ {k}} {\operatorname{argmax}} M (\boldsymbol {F} ^ {k}, \omega^ {*}), k = 1, \dots , K.\tag{43}
$$

Therefore, we observe that the impact of weights on selecting the final solution can be reduced via this method. This issue is also verified via a results analysis, as shown in the following section.

## Case Study

In this section, we conduct a case study to verify the effectiveness of the proposed framework. The MCO problem in the framework is a mixed-integer linear programming problem, which can be solved by using the CPLEX linear solver in MATLAB R2016a.

## Simulation Case Set

## DC

In our work, the parameters of the DC are set based on the verified simulation cases [20], [32], as shown in Table 1. As Google has disclosed that the PUE of their DCs ranges from 1.09 to 1.31 [46], we set it as a conservative value, i.e., 1.3. In addition, the IT workloads are set and listed in Table 2, referred to [47]. Then, the arrival and minimum departure

## Table 2. IT workload dataset

<table><tr><td> $\underline{t}$ </td><td> $\underline{1}$ </td><td> $\underline{2}$ </td><td> $\underline{3}$ </td><td> $\underline{4}$ </td><td> $\underline{5}$ </td><td> $\underline{6}$ </td><td> $\underline{7}$ </td><td> $\underline{8}$ </td><td> $\underline{9}$ </td><td> $\underline{10}$ </td><td> $\underline{11}$ </td><td> $\underline{12}$ </td></tr><tr><td> $IT_{t}^{RT*}$ </td><td>0.1</td><td>0.125</td><td>0.1</td><td>0</td><td>0.025</td><td>0.05</td><td>0.1</td><td>0.125</td><td>0.15</td><td>0.2</td><td>0.275</td><td>0.288</td></tr><tr><td> $IT_{t}^{DT}$ </td><td>0.45</td><td>0.325</td><td>0.2</td><td>0.7</td><td>0.475</td><td>0.4</td><td>0.625</td><td>0.275</td><td>0.25</td><td>0.3</td><td>0.575</td><td>0.325</td></tr><tr><td>DL**</td><td>5:00</td><td>5:00</td><td>5:00</td><td>10:00</td><td>10:00</td><td>10:00</td><td>13:00</td><td>13:00</td><td>13:00</td><td>13:00</td><td>17:00</td><td>17:00</td></tr><tr><td> $\underline{t}$ </td><td> $\underline{13}$ </td><td> $\underline{14}$ </td><td> $\underline{15}$ </td><td> $\underline{16}$ </td><td> $\underline{17}$ </td><td> $\underline{18}$ </td><td> $\underline{19}$ </td><td> $\underline{20}$ </td><td> $\underline{21}$ </td><td> $\underline{22}$ </td><td> $\underline{23}$ </td><td> $\underline{24}$ </td></tr><tr><td> $IT_{t}^{RT}$ </td><td>0.425</td><td>0.288</td><td>0.25</td><td>0.1875</td><td>0.138</td><td>0.075</td><td>0.05</td><td>0.088</td><td>0.1</td><td>0.125</td><td>0.075</td><td>0.1</td></tr><tr><td> $IT_{t}^{DT}$ </td><td>0.3</td><td>0.3</td><td>0.625</td><td>0.25</td><td>0.3</td><td>0.25</td><td>0.275</td><td>0.575</td><td>0.475</td><td>0.325</td><td>0.375</td><td>0.175</td></tr><tr><td>DL</td><td>17:00</td><td>17:00</td><td>20:00</td><td>20:00</td><td>22:00</td><td>22:00</td><td>22:00</td><td>24:00</td><td>24:00</td><td>24:00</td><td>24:00</td><td>24:00</td></tr></table>

\* IT<sub>t</sub><sup>RT</sup> is the forecasting value of the arrived real-time workload in time interval t; the corresponding normalized standard deviation of the forecasting error is 10% [48]. \*\* DL is the processing deadline of the arrived delay-tolerable workload in time interval t.

![](images/33582b47b7279848a7ef2f84ab3446b15e35aed5d54b3ec69ae3446a0bea4bff.jpg)  
(a)

![](images/c45995e1697019ce485d20f5b8a67e5d5e42e99121c430ac2ea483bfed0e175c.jpg)  
(b)  
FIGURE 4. Case set. (a) Arrival and minimum departure curves of delay-tolerable workload. (b) Day-ahead electricity price and forecasting RE generation.

curves of the delay-tolerable workload are obtained and illustrated in Figure 4(a). In addition, the conversion factor l of waste heat recovery is set to be 70%.

## Energy Supply

For the RE generation, we assume that the DCMG includes 15 wind turbines with rated capacity 1.6 MW and a PV station of 40 MW. The forecast information about PV and wind power generation is set based on a pilot project in North China [49], [50]. The normalized standard deviations of the day-ahead forecast errors for wind and PV power generation are set as 14% and 6%, respectively [51]. Furthermore, the hourly electricity price is referred from the PJM market [52]. Then, we illustrate the forecast information of wind and PV power generation and the electricity price in Figure 4(b).

Case  1: Single-criterion optimization; the objective function is the operation cost $F _ { 1 } ( \pmb { x } , \pmb { \xi } _ { t , s } )$ , while the QoS is a constraint.

Case  2: Single-criterion optimization; the objective function is the WQoS index $F _ { 3 } ( \pmb { x } , \pmb { \xi } _ { t , s } )$

Case  3: Single-criterion optimization; the objective function is the VoLL $F _ { 5 } ( \pmb { x } , \pmb { \xi } _ { t , s } )$

Case 4: The proposed multicriteria optimal operation framework, i.e., the MCO with the BDM method.

## Effectiveness of WQoS Criterion

In the previous studies, the QoS is commonly deemed as a constraint. In other words, the delay-tolerable workloads are required to be completed before the deadline. Nevertheless, the DC should reserve server redundancies for handling the stochastic real-time workload. In other words, the

Specific parameters for the generation units and storage systems are also scaled based on Electric Reliability Council of Texas scheduling data [53], as respectively listed in Tables 3 and 4. The diesel-based carbon emission rate for the generation units and coal-based carbon emission rate for the electricity purchases are set as 0.525 t/MWh and 0.895 t/MWh, according to [37]. In addition, the capacity of the electric boiler is 35 MW. For the AUGME-CON algorithm, e is set as $1 \times { 1 0 } ^ { - 3 }$ and the number of grid points G regarding each criterion is six.

## Simulation Results

To illustrate the effectiveness and superiority of the proposed framework, we set several comparative cases to conduct the simulation analysis. They are described as follows.

Table 3. Operation parameters of generation units

<table><tr><td>Units</td><td> $SU_u$ / $SD_u$  (k$)</td><td> $P_{u,\min}^{\text{unit}}/ P_{u,\max}^{\text{unit}}$  (MW)</td><td> $\alpha_u$  (k$)</td><td> $\beta_u$  (k$/ MWh)</td><td> $\gamma_u$  (k$/ MWh)</td><td>Initial Output (MW)</td><td> $R_u$  (MW/h)</td></tr><tr><td>Power only</td><td>0.3</td><td>5/50</td><td>0.0502</td><td>0.021</td><td>0</td><td>10</td><td>5</td></tr><tr><td>CHP</td><td>0.25</td><td>5/55</td><td>0.0167</td><td>0.0297</td><td>0.0108</td><td>10</td><td>4</td></tr></table>

Table 4. Parameters of ESS and TSS

<table><tr><td>Storage System</td><td>Initial and Final State (MWh)</td><td>Maximum Charging/ Discharging Rate (MW/h)</td><td>Maximum State (MWh)</td><td>Charging/ Discharging Efficiency</td></tr><tr><td>Electricity</td><td>20</td><td>5</td><td>40</td><td>0.95</td></tr><tr><td>Thermal</td><td>50</td><td>20</td><td>100</td><td>0.95</td></tr></table>

unprocessed delay-tolerable workload should be as small as possible. To this end, we propose the WQoS index, with the help of the arrival–departure curve model, as an optimization criterion in the DCMG operation problem.

The comparisons between the arrived and processed delay-tolerable workload regarding Cases 1 and 2 are shown in Figure 5(a) and (b), respectively. In Case 1, we can easily observe that the delay-tolerable workload in the first and second time intervals is processed instantly after it arrives. Moreover, it is notable that a larger amount of workload is scheduled to be processed in the 24th interval. These schedules could reduce the operational cost since the electricity price is low in the aforementioned time intervals, as shown in Figure 4(b). In Case 2, all of the delay-tolerable workloads are completed in time to achieve the optimal WQoS, i.e., the highest server redundancy. The detailed values of WQoS and the operational cost are shown in Table 5.

It is easily observed that the operational cost could reach a minimal value, i.e., 29.058 k\$ in Case  1, while the WQoS has a high value of 0.329. By contrast, for Case 2, the WQoS could be optimized to 0 while causing a high operational cost of 40.858 k\$. Therefore, from a comparison of Cases 1 and 2, it is evident that the WQoS might perform worse if we merely treat the corresponding QoS as a constraint. To pursue a better WQoS, we should consider it as an optimization criterion rather than a constraint.

![](images/077463b29e60439444867397ffdb8574e930b3f19973622e9c66d3ae95cb0095.jpg)  
(a)

![](images/8c82fd574869ff7a5b418dd19cd0ee5551c3445551733ead2709835ba0616d3d.jpg)  
FIGURE 5. Delay-tolerable workload scheduling result in (a) Case 1, (b) Case 2.

Table 5. Objective values for single-criterion optimizing cases

<table><tr><td>Cases</td><td>Operational Cost (k$)</td><td>WQoS</td></tr><tr><td>Case 1</td><td>29.058</td><td>0.329</td></tr><tr><td>Case 2</td><td>40.858</td><td>0</td></tr></table>

## Effectiveness of the Multicriteria Optimal Operation Framework

In our proposed framework, we intend to optimize the multiple criteria of operational cost, carbon emissions, WQoS, VoCE, and VoLL. This is related to the MCO problem, and it can be solved by the AUGMECON algorithm. First of all, we calculate the payoff table to determine the numerical range of the criteria values, which is [29.058, 592.357, 0, 75.641, 27.192] # [34.531, 786.784, 0.535, 268.806, 75.563] with respect to $F _ { 1 } { - } F _ { 5 }$ . Afterward, the AUGMECON algorithm is used, and numerous Pareto solutions are obtained. To better visualize these solutions, we adopt a parallel axis plot, which displays the multiple criteria on separate axes to present criteria values [36]. As shown in Figure $^ { 6 , }$ each line is with respect to a Pareto solution. The criteria values are denoted by positions of the inflection points on the corresponding axes. In general, the points regarding the Pareto solutions are distributed uniformly with each axis. Also, the intersections of these lines could well describe the tradeoff relationship among different criteria [45]. Herein, a notable phenomenon that can be observed is the conflicting relationship between VoCE and VoLL; i.e., optimizing one of these two criteria will worsen the other. Therefore, it is effective to conduct the MCO approach with comprehensive consideration of multiple criteria.

To further verify the effectiveness of the proposed multicriteria optimal operation framework, we conduct the single-criterion optimization as comparative cases, i.e., Cases 1, 2, and 4. The simulation results for the multiple criteria of different cases are shown in Table  6. In Case  1, the operational cost is optimized to 29.058 k\$, while the VoLL is as high as 70.563 MWh. The WQoS is minimized to 0 in Case 2, yet the carbon emissions reach 1,190.114 tons. Moreover, when optimizing the VoLL, the other criteria perform poorly; for instance, the VoCE is as high as 629.934 MWh. In summary, the single-criterion optimization could significantly improve performance for the corresponding aspect in the DCMG operation. However, it would lead to poor performances for other criteria. By contrast, our proposed framework could have a balanced consideration of these multiple criteria and obtain a solution that performs relatively well in Case 4, as shown in Table  6. The values of operational cost, carbon emissions, WQoS, VoCE, and VoLL are 32.777 k\$, 592.357 tons, 0.214, 191.54 MWh, and 35.866 MWh, respectively.

35  
787  
0.5  
269  
71  
![](images/f48d86e945a12bcfdf82b397c6170623f662883086a67fcd71abd44337eb197f.jpg)  
FIGURE 6. Parallel axis plot of Pareto front.

Figure  7 shows the scheduling results of supply and demand for the power sector in the DCMG operation. Figure  7(a)–(d) corresponds to the results of Cases 1–4, respectively. In Figure  7, the histograms above the horizontal axis represent the power generation, ESS discharging, and purchasing from the utility grid. However, the power consumption and ESS charging are described by histograms below the axis. It is observed in Figure  7(a) that most of the electric power is supplied by the utility grid in the first, second, and 24th time intervals, when the electricity price is lower, while, in the other time periods, the power is mainly supplied by the poweris an important power supplier during the ninth through 17th time intervals.

In addition, Figure  8(a)–(d) presents the scheduling results of supply and demand for the thermal sector in Cases 1–4, respectively. Likewise, the histograms above the horizontal axis denote the heat production of the electric boiler, CHP units, TSS releasing, and waste heat recovery system, while the negative ones illustrate the heat load, TSS reserving, and the abandoned heat, which is released to the air. As shown in Figure  8(a), we can see that the TSS is reserving thermal energy in the first and 24th time intervals; the aim is to store the “cheap” electricity in the form of heat for the purpose of optimizing the operational cost in Case  1. In Figure  8(b), the heat load is mainly supplied by waste heat recovery and the electric boiler, such as in the first through fifth time intervals. In Figure  8(c), there is a significant amount of abandoned heat released to the air in most time periods, which is due to the excessive thermal supply. It leads to a high VoCE, as shown in the result for Case  3 in Table  6. Also, it should be

only unit and RE generation. These results are due to the issue that the operational cost is to be minimized in Case 1. Moreover, the utility grid is the main power source for most time intervals in both Case  2 and Case  3, as illustrated in Figure 7(b) and (c). In addition, as shown in Figure 7(d), the electric power is mainly provided by the power-only unit, RE generation, and the CHP unit, and PV generation mentioned that the TSS is releasing in the first through third time intervals while reserving during the ninth through 11th and 16th and 17th intervals, as shown in Figure  8(d). It shows that the TSS also plays a role in improving the flexibility of the DCMG operation in Case  4. In summary, these observations indicate the advantages to conducting the MCO approach with comprehensive consideration of multiple criteria.

Table 6. Values of multiple criteria of different cases

<table><tr><td>Cases</td><td>Operational Cost (k$)</td><td>Carbon Emissions (Tons)</td><td>WQoS</td><td>VoCE (MWh)</td><td>VoLL (MWh)</td></tr><tr><td>Case 1</td><td>29.058</td><td>695.322</td><td>0.329</td><td>75.641</td><td>70.563</td></tr><tr><td>Case 2</td><td>40.858</td><td>1,190.114</td><td>0</td><td>120.769</td><td>49.217</td></tr><tr><td>Case 3</td><td>53.498</td><td>1,619.76</td><td>0.513</td><td>629.934</td><td>27.191</td></tr><tr><td>Case 4</td><td>32.777</td><td>592.357</td><td>0.214</td><td>191.54</td><td>35.866</td></tr></table>

![](images/e01d170175b889cbc063ee9882f5db29e8e1aede0fb7f9f4814435a8cd917c0f.jpg)  
(a)

![](images/c1dd331a48e8081bddae43a6fbb78f43a9e417984724e443cbd3b7e8fe3190f8.jpg)

(c)  
![](images/63f8e61a4be878b698c827fd58f00bd2252011481712a07bab4a6e8f44298b3d.jpg)

(b)  
![](images/8a889c493d73f44996c613342665cb645efedcce900acbf18ef2863b08cdf19a.jpg)  
(d)

<table><tr><td>PV Power</td><td>Wind Power</td><td>Grid Power</td><td>Power Unit</td><td>CHP Unit</td><td>Discharge</td></tr><tr><td>Power Demand</td><td>Charge</td><td>Boiler</td><td>Curtailment</td><td></td><td></td></tr></table>

FIGURE 7. Scheduling result for power sector in (a) Case 1, (b) Case 2, (c) Case 3, and (d) Case 4.

![](images/6ee018440f94e19e156573debb236c9592afd22d20a1121db37f1dc1bd0ee401.jpg)  
(a)

![](images/8efa18c507de1d452bf048cff2fdba95c71377f8e399884252f02d5fad34b028.jpg)  
(c)

![](images/3bc49088d8e0cdfb468b3ddec6b3b5f209f65f9693bf24ab24234e6efd0f2831.jpg)  
(b)

![](images/4c75e38e7c0761d1b0beb91c2e5a4fcdd081b333065072cb944001920c8bcd8b.jpg)  
(d)  
FIGURE 8. Scheduling result for thermal sector in (a) Case 1, (b) Case 2, (c) Case 3, and (d) Case 4.

## Superiority of BDM Method

To evaluate the outperformance of the proposed BDM, we implement comparative cases with FDM and a technique for order preference by similarity to ideal solution (TOP-SIS). They are listed as follows:

![](images/5b9e3ec3cabb284b061d3efdf6912e89d5dea416c2df74c6316c0dcf3df969e1.jpg)  
FIGURE 9. Comparison of criteria values with different decisions on radar chart.

FDM\_1: FDM method with the weight vector [0.2, 0.2, 0.2, 0.2, 0.2]. It indicates the equal consideration for all criteria.

FDM\_2: FDM method with the weight vector [0.9, 0.025, 0.025, 0.025, 0.025]. It indicates an emphasized consideration on the first criterion, i.e., the operational cost. TOPSIS: The TOPSIS method [54] chooses the alternative of the shortest Euclidean distance from the ideal solution and the longest distance from the negative ideal solution.

BDM: Our proposed BDM method with the optimal weight vector is \~ = [ .0 426, .0 107, .0 019, .0 01 0, .438],<sup>)</sup> which is obtained by solving (42) via dynamic programming. Accordingly, criteria values corresponding to the final solution are obtained as F = [ .32 743 5, .92 357, .0 306, .186 021 3, .3 387].<sup>)</sup>

The criteria values of the solutions obtained by the different methods are visualized by the radar chart in Figure  9. It is observed that all criteria values of BDM are relatively small compared with the other methods. By contrast, some criteria may converge to better values, while others perform worse than the other methods. To be specific, the operational cost in FDM\_2 is optimized to the lowest value, while the VoLL is extremely high. It shows that the obtained solutions could reflect the emphasis of corresponding weights through FDM. Moreover, the operational cost, WQoS, and VoCE perform well for both FDM\_1 and TOPSIS, while the carbon emissions and VoLL are similarly significantly high. The results of FDM\_1 and FDM\_2 show that the performance of FDM is highly dependent on the decision maker’s preference, which may be impacted by subjectiveness. Also, compared with TOPSIS, our proposed method could determine the solution with balanced consideration of these multiple criteria. Therefore, we provide the BDM as a potential competitive solving method.

From the results analysis, we can verify the effectiveness of the proposed framework, which is specified as follows:

The arrival–departure curve could successfully characterize the delay-tolerable workload, based on which the WQoS is described. The analyses on the WQoS criterion indicate that it is significant to be considered in the DCMG operation.

The proposed MCO approach is effective for considering multiple criteria; i.e., the tradeoff relationship can be shown, and results of comparative cases verify that it is necessary to consider multiple criteria for DCMG operations, simultaneously.

● The proposed BDM method can effectively choose the final scheduling solution, which could weaken the impacts of manual selection of criteria weights.

## Conclusion

This article has proposed a multicriteria optimal operation framework for DCMGs. In this framework, we first present a delay-tolerable workload characterization method by the arrival–departure curve model. Then, a WQoS index is proposed as a criterion to be optimized. Considering waste heat recovery and RE power integration, we introduce an MCO problem while taking the operational cost, carbon emissions, WQoS, VoCE, and VoLL into account. In addition, the AUGMECON algorithm is used to solve this MCO problem. Finally, we propose a BDM method to select the final solution. Simulation results have demonstrated the effectiveness of the proposed framework. The proposed multicriteria optimal operation framework is evaluated with a simulation system using real datasets. The numerical results demonstrate that the proposed framework is effective, and it has a potential for being employed in real systems.

## Acknowledgment

This work is supported in part by the National Natural Science Foundation of China under Grants 62073148 and 62233006 and in part by the Open Project of the Key Laboratory of Modern Power System Simulation and Control and Renewable Energy Technology, Ministry of Education, Northeast Electric Power University under Grant MPSS2022-04.

## Author Information

Yuanzheng Li is with the School of Artificial Intelligence and Automation, Ministry of Education Key Laboratory of Image Processing and Intelligence Control, Huazhong University of Science and Technology, Wuhan 430074, China. Jingjing Huang (hjj\_2019@hust.edu.cn) is with the China-EU Institute for Clean and Renewable Energy, Huazhong University of Science and Technology, Wuhan 430074, China. Xiaomeng Ai is with the School of Electrical and Electronics Engineering, Huazhong University of Science and Technology, Wuhan 430074, China. Yun Liu is with the School of Electric Power Engineering, South China University of Technology, Guangzhou 510641, China. Hao Wang is with the Department of Data Science and Artificial Intelligence, Monash University, Melbourne, VIC 3800, Australia. Yongzhen Wang is with the Energy Internet Research Institute, Department of Electrical Engineering, Tsinghua University, Beijing, 100084, China. Li is a Senior Member of IEEE. H. Wang and Ai are Members of IEEE. This article first appeared as “A Multi-Criteria Optimal Operation Framework for Renewable Energy Integrated Data Center Microgrid with Waste Heat Recovery” (doi: 10.1109/ICPS51807.2021.9416605) at the 2021 IEEE/IAS 57th Industrial and Commercial Power Systems Technical Conference. This article was reviewed by the IEEE IAS Codes & Standards Committee.

## References

[1] Z. Ding, Y. Cao, L. Xie, Y. Lu, and P. Wang, “Integrated stochastic energy management for data center microgrid considering waste heat recovery,” in Proc. IEEE Ind. Appl. Soc. Annu. Meeting, 2018, pp. 1–9, doi: 10.1109/IAS.2018.8544489.

[2] J. Lyu, S. Zhang, H. Cheng, K. Yuan, Y. Song, and S. Fang, “Optimal sizing of energy station in the multi-energy system integrated with data center,” IEEE Trans. Ind. Appl., early access, 2021, doi: 10.1109/ ICPS48389.2020.9176752.

[3] K. Ebrahimi, G. F. Jones, and A. S. Fleischer, “Thermo-economic analysis of steady state waste heat recovery in data centers using absorption refrigeration,” Appl. Energy, vol. 139, pp. 384–397, Feb. 2015, doi: 10.1016/j.apenergy.2014.10.067.

[4] L. Yu, T. Jiang, and Y. Cao, “Energy cost minimization for distributed internet data centers in smart microgrids considering power outages,” IEEE Trans. Parallel Distrib. Syst., vol. 26, no. 1, pp. 120–130, Jan. 2015, doi: 10.1109/TPDS.2014.2308223.

[5] P. X. Gao, A. R. Curtis, B. Wong, and S. Keshav, “It’s not easy being green,” SIGCOMM Comput. Commun. Rev., vol. 42, no. 4, pp. 211–222, Aug. 2012.

[6] J. Li and W. Qi, “Toward optimal operation of internet data center microgrid,” IEEE Trans. Smart Grid, vol. 9, no. 2, pp. 971–979, Mar. 2018, doi: 10.1109/TSG.2016.2572402.

[7] P. Wang, Y. Cao, and Z. Ding, “Resources planning strategies for data center microgrid considering water footprints,” in Proc. 2nd IEEE Conf. Energy Internet Energy Syst. Integr., 2018, pp. 1–6, doi: 10.1109/EI2.2018.8582288.

[8] L. Yu, T. Jiang, Y. Cao, and Q. Qi, “Carbon-aware energy cost minimization for distributed internet data centers in smart microgrids,” IEEE Internet Things J., vol. 1, no. 3, pp. 255–264, Jun. 2014, doi: 10.1109/ JIOT.2014.2322606.

[9] W. Qi and J. Li, “Towards optimal coordinated operation of distributed internet data center microgrids,” in Proc. IEEE Power Energy Soc. General Meeting, 2016, pp. 1–5, doi: 10.1109/PESGM.2016.7741732.

[10] Y. Yao, L. Huang, A. B. Sharma, L. Golubchik, and M. J. Neely, “Power cost reduction in distributed data centers: A two-time-scale approach for delay tolerant workloads,” IEEE Trans. Parallel Distrib. Syst., vol. 25, no. 1, pp. 200–211, Jan. 2014, doi: 10.1109/TPDS.2012.341.

[11] F. Zhu, Y. Lu, and Z. Ding, “Batch workloads management for data centers considering nodes efficiency,” in Proc. 2nd IEEE Conf. Energy Internet Energy Syst. Integr., 2018, pp. 1–6, doi: 10.1109/ EI2.2018.8582052.

[12] P. Wang, L. Xie, Y. Lu, and Z. Ding, “Day-ahead emission-aware resource planning for data center considering energy storage and batch workloads,” in Proc. IEEE Conf. Energy Internet Energy Syst. Integr., 2017, pp. 1–6, doi: 10.1109/EI2.2017.8245657.

[13] “Apple and the environment.” Apple. Accessed: May 2019. [Online]. Available: https://www.apple.com/environment/

[14] “Data center knowledge.” Facebook. Accessed: May 2019. [Online]. Available: http://www.datacenterknowledge.com

[15] T. Chen, Y. Zhang, X. Wang, and G. B. Giannakis, “Robust workload and energy management for sustainable data centers,” IEEE J. Sel.

Areas Commun., vol. 34, no. 3, pp. 651–664, Mar. 2016, doi: 10.1109/ JSAC.2016.2525618.

[16] M. Ghamkhari, A. Wierman, and H. Mohsenian-Rad, “Energy portfolio optimization of data centers,” IEEE Trans. Smart Grid, vol. 8, no. 4, pp. 1898–1910, Jul. 2017, doi: 10.1109/TSG.2015.2510428.

[17] L. Yu, T. Jiang, Y. Cao, and Q. Zhang, “Risk-constrained operation for internet data centers in deregulated electricity markets,” IEEE Trans. Parallel Distrib. Syst., vol. 25, no. 5, pp. 1306–1316, May 2014, doi: 10.1109/ TPDS.2013.2297095.

[18] M. Ghamkhari and H. Mohsenian-Rad, “Energy and performance management of green data centers: A profit maximization approach,” IEEE Trans. Smart Grid, vol. 4, no. 2, pp. 1017–1025, Jun. 2013, doi: 10.1109/TSG.2013.2237929.

[19] Stockholm Data Parks: Green Computing Redefined, Stockholm, Sweden, 2020. [Online]. Available: https://stockholmdataparks.com/wp -content/uploads/sdp-first-level-presentation\_2020-05-29.pdf

[20] P. Wang, Y. Cao, and Z. Ding, “Flexible multi-energy scheduling scheme for data center to facilitate wind power integration,” IEEE Access, vol. 8, pp. 88,876–88,891, Apr. 2020, doi: 10.1109/ACCESS.2020.2990454.

[21] Z. Ding, Y. Cao, L. Xie, Y. Lu, and P. Wang, “Integrated stochastic energy management for data center microgrid considering waste heat recovery,” IEEE Trans. Ind. Appl., vol. 55, no. 3, pp. 2198–2207, Mar./Jun. 2019, doi: 10.1109/TIA.2018.2890789.

[22] M. Wahlroos, S. Syri, M. Pärssinen, and J. Manner, “Utilizing data center waste heat in district heating – Impacts on energy efficiency and prospects for low-temperature district heating networks,” Energy, vol. 140, no. Part 1, pp. 1228–1238, Dec. 2017, doi: 10.1016/j.energy.2017.08.078.

[23] M. A. Zafer and E. Modiano, “A calculus approach to energyefficient data transmission with quality-of-service constraints,” IEEE/ ACM Trans. Netw., vol. 17, no. 3, pp. 898–911, Jun. 2009, doi: 10.1109/ TNET.2009.2020831.

[24] Z. He, T. Ding, Y. Liu, and Z. Li, “Analysis of a district heating system using waste heat in a distributed cooling data center,” Appl. Thermal Eng., vol. 141, pp. 1131–1140, Jun. 2018, doi: 10.1016/j.applthermaleng.2018.06.036.

[25] M. Deymi-Dashtebayaz and S. Valipour-Namanlo, “Thermoeconomic and environmental feasibility of waste heat recovery of a data center using air source heat pump,” J. Cleaner Prod., vol. 219, pp. 117–126, May 2019, doi: 10.1016/j.jclepro.2019.02.061.

[26] M. Alipour, B. Mohammadi-Ivatloo, and K. Zare, “Stochastic scheduling of renewable and CHP-based microgrids,” IEEE Trans. Ind. Informat., vol. 11, no. 5, pp. 1049–1058, Oct. 2015, doi: 10.1109/ TII.2015.2462296.

[27] C. Belady, A. Rawson, A. Pfleuger, and D. Cader, “Green grid data center power efficiency metric: PUE and DCIE,” Jan. 2008. [Online]. Available: https://www.semanticscholar.org/paper/GREEN-GRID-DATA -CENTER-POWER-EFFICIENCY-METRICS%3A-Belady-Rawson/02cb30fee 7b141c4af6244ab20141d452b66568e or https://www.comtecpower.com/ pdfs/white-paper/pue-and-dcie-eff-metrics.pdf

[28] I. Giagkiozis and P. J. Fleming, “Methods for multi-objective optimization: An analysis,” Inf. Sci., vol. 293, pp. 338–350, 2015, doi: 10.1016/j. ins.2014.08.071.

[29] A. Hamidi, D. Nazarpour, and S. Golshannavaz, “Multiobjective scheduling of microgrids to harvest higher photovoltaic energy,” IEEE Trans. Ind. Informat., vol. 14, no. 1, pp. 47–57, Jan. 2018, doi: 10.1109/ TII.2017.2717906.

[30] Y. Z. Li, Q. H. Wu, L. Jiang, J. B. Yang, and D. L. Xu, “Optimal power system dispatch with wind power integrated using nonlinear interval optimization and evidential reasoning approach,” IEEE Trans. Power Syst., vol. 31, no. 3, pp. 2246–2254, May 2016, doi: 10.1109/TPWRS.2015.2449667.

[31] T. Chen, X. Wang, and G. B. Giannakis, “Cooling-aware energy and workload management in data centers via stochastic optimization,” IEEE J. Sel. Topics Signal Process., vol. 10, no. 2, pp. 402–415, Mar. 2016, doi: 10.1109/JSTSP.2015.2500189.

[32] L. Yu, T. Jiang, and Y. Zou, “Distributed real-time energy management in data center microgrids,” IEEE Trans. Smart Grid, vol. 9, no. 4, pp. 3748–3762, Jul. 2018, doi: 10.1109/TSG.2016.2640453.

[33] W. Fang, Y. Yao, L. Huang, A. B. Sharma, L. Golubchik, and M. J. Neely, “A comment on ‘power cost reduction in distributed data centers: A two time scale approach for delay tolerant workloads’,” IEEE Trans. Parallel Distrib. Syst., vol. 26, no. 5, pp. 1495–1496, May 2015, doi: 10.1109/TPDS.2014.2312920.

[34] Z. Li, Y. Li, Y. Liu, P. Wang, R. Lu, and H. B. Gooi, “Deep learning based densely connected network for load forecasting,” IEEE Trans.

Power Syst., vol. 36, no. 4, pp. 2829–2840, Jul. 2021, doi: 10.1109/ TPWRS.2020.3048359.

[35] Y. Li, G. Hao, Y. Liu, Y. Yu, Z. Ni, and Y. Zhao, “Many-objective distribution network reconfiguration via deep reinforcement learning assisted optimization algorithm,” IEEE Trans. Power Del., vol. 37, no. 3, pp. 2230– 2244, Jun. 2022, doi: 10.1109/TPWRD.2021.3107534.

[36] Y. Z. Li, M. S. Li, and Q. H. Wu, “Optimal reactive power dispatch with wind power integrated using group search optimizer with intraspecific competition and levy walk,” J. Modern Power Syst. Clean Energy, vol. 2, no. 4, pp. 308–318, 2015, doi: 10.1007/s40565-014-0076-9.

[37] “How much carbon dioxide is produced per kilowatthour when generating electricity with fossil fuels?” U.S. Energy Inf. Admin., Washington, DC, USA, 2022. [Online]. Available: https://www.eia.gov/tools/faqs/faq. php?id=74&t=11

[38] Z. Ding and W. Lee, “A stochastic microgrid operation scheme to balance between system reliability and greenhouse gas emission,” IEEE Trans. Ind. Appl., vol. 52, no. 2, pp. 1157–1166, Mar./Apr. 2016, doi: 10.1109/TIA.2015.2490619.

[39] J. Chen, B. Qi, Z. Rong, K. Peng, Y. Zhao, and X. Zhang, “Multi-energy coordinated microgrid scheduling with integrated demand response for flexibility improvement,” Energy, vol. 217, no. 1, Feb. 2021, Art. no. 119387, doi: 10.1016/j.energy.2020.119387.

[40] Y. Liu, Y. Wang, Y. Li, H. B. Gooi, and H. Xin, “Multi-agent based optimal scheduling and trading for multi-microgrids integrated with urban transportation networks,” IEEE Trans. Power Syst., vol. 36, no. 3, pp. 2197–2210, May 2021, doi: 10.1109/TPWRS.2020.3040310.

[41] L. Yu, S. Qin, M. Zhang, C. Shen, T. Jiang, and X. Guan, “A review of deep reinforcement learning for smart building energy management,” IEEE Internet Things J., vol. 8, no. 15, pp. 12,046–12,063, Aug. 2021, doi: 10.1109/JIOT.2021.3078462.

[42] Y. Li et al., “Optimal operation of multimicrogrids via cooperative energy and reserve scheduling,” IEEE Trans. Ind. Informat., vol. 14, no. 8, pp. 3459–3468, Aug. 2018, doi: 10.1109/TII.2018.2792441.

[43] G. Mavrotas, “Effective implementation of the e-constraint method in multi-objective mathematical programming problems,” Appl. Math. Comput., vol. 213, no. 2, pp. 455–465, Jul. 2009, doi: 10.1016/j.amc.2009.03.037.

[44] Y. Z. Li et al., “Risk constrained economic dispatch with integration of wind power by multi-objective optimization approach,” Energy, vol. 126, pp. 810–820, May 2017, doi: 10.1016/j.energy.2017.02.142.

[45] Y. Li, P. Wang, H. B. Gooi, J. Ye, and L. Wu, “Multi-objective optimal dispatch of microgrid under uncertainties via interval optimization,” IEEE Trans. Smart Grid, vol. 10, no. 2, pp. 2046–2058, Mar. 2019, doi: 10.1109/ TSG.2017.2787790.

[46] “Efficiency: How we do it.” Google Data Centers. Accessed: Mar. 31, 2020. [Online]. Available: https://www.google.com/about/datacenters/ efficiency/internal/

[47] J. Luo, L. Rao, and X. Liu, “Temporal load balancing with service delay guarantees for data center energy cost optimization,” IEEE Trans. Parallel Distrib. Syst., vol. 25, no. 3, pp. 775–784, Mar. 2014, doi: 10.1109/ TPDS.2013.69.

[48] J. Li, Z. Li, K. Ren, and X. Liu, “Towards optimal electric demand management for internet data centers,” IEEE Trans. Smart Grid, vol. 3, no. 1, pp. 183–192, Mar. 2012.

[49] Z. Ding, L. Xie, Y. Lu, P. Wang, and S. Xia, “Emission-aware stochastic resource planning scheme for data center microgrid considering batch workload scheduling and risk management,” IEEE Trans. Ind. Appl., vol. 54, no. 6, pp. 5599–5608, Nov./Dec. 2018, doi: 10.1109/TIA.2018.2851516.

[50] “Wind and PV power outputs.” Renewables.ninja. Accessed: Mar. 31, 2020. [Online]. Available: https://www.renewables.ninja

[51] Y. Li, J. Huang, Y. Liu, H. Wang, Y. Wang, and X. Ai, “A multi-criteria optimal operation framework for renewable energy integrated data center microgrid with waste heat recovery,” in Proc. IEEE/IAS 57th Ind. Commercial Power Syst. Tech. Conf., 2021, pp. 1–11, doi: 10.1109/ ICPS51807.2021.9416605.

[52] “Locational marginal pricing.” PJM. Accessed: Mar. 31, 2020. [Online]. Available: https://www.pjm.com/

[53] H. Dou, Y. Qi, W. Wei, and H. Song, “Carbon-aware electricity cost minimization for sustainable data centers,” IEEE Trans. Sustain. Comput., vol. 2, no. 2, pp. 211–223, Apr./Jun. 2017, doi: 10.1109/ TSUSC.2017.2711925.

[54] M. Panda and A. K. Jagadev, “TOPSIS in multi-criteria decision making: A survey,” in Proc. 2nd Int. Conf. Data Sci. Bus. Analytics, 2018, pp. 51–54, doi: 10.1109/ICDSBA.2018.00017.