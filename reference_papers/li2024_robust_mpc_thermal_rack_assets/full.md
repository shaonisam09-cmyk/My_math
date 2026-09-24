# A robust data-driven model predictive thermal control for rack-based data center

![](images/cdc566e04e457182b85bc48200f124c8bed974b413bee3fac8b1e7f3ddfb54fd.jpg)

Yiran Li <sup>a</sup>, Chao Yang <sup>c</sup>, Yuanqing Xia <sup>b,a,∗</sup>

<sup>a</sup> School of Automation, Beijing Institute of Technology, Beijing, 100081, China

<sup>b</sup> Zhongyuan University of Technology, Zhengzhou 450007, China

<sup>c</sup> School of Civil Engineering, Chongqing University, Chongqing, 400045, China

## A R T I C L E I N F O

Keywords: Robust data-driven MPC Rack-based DC Air-cooled system Historical data

## A B S T R A C T

Optimizing and controlling of air-cooled data centers cooling systems are essential for reducing energy consumption. However, traditional cooling system control strategies have been proven inadequate due to the thermodynamic processes complexity and the challenge for accurately modeling the thermal behavior of data center (DC). Therefore, a data-driven control method without explicit modeling process is urgently needed. In this paper, a robust data-driven model predictive control (MPC) method for air-cooled rack-based DCs, based on Willems fundamental lemma, is proposed. The methodology is executed in three phases: Firstly, we show that the system is approximately linear time-invariant (LTI) by zonal model method and experiment. Secondly, we propose a data-driven MPC framework for air-cooled rack-based DC. Thirdly, considering the uncertainties caused by disturbances and the nonlinear dynamics, the datadriven MPC approach is tailored as a robust one. Finally, the proposed approach is verified by computational fluid dynamics simulation experiments. This approach ensures the rack-based DC cold aisle temperature to the reference temperature as well as within acceptable limits. The experiment shows that the steady-state error is guaranteed to be less than 2%, with a control accuracy increased by 2.4% compared with the regular MPC. Thus, this method provides an alternative solution for maintaining the reference temperature in air-cooled rack-based DCs by utilizing only input–output data.

## 1. Introduction

The latest Intergovernment Panel on Climate Change (IPCC) report states that limiting the climate change within 1.5 <sup>◦</sup>C promises to peak carbon dioxide emissions by 2030 and strives to achieve carbon neutralization by 2060 [1]. DC industry is energyintensive [2], occupying more than 1% of global electricity consumption [3]. Recently, the scale and number of DCs are growing rapidly all around the world. Energy conservation and emission reduction of DCs have become the focus of work for the industry.

The cooling system of the DC is a significant consumer of energy, accounting for nearly 30% of total energy expenditure in DC [4]. Essential to DC operations, the cooling system ensures a stable thermal environment for electronics in 24 × 7 constant work. Inadequate control may accelerate component aging and leading to servers outage. Specifically, the cooling system is developed to regulate and maintain a stable temperature for servers inlet, adapting to the thermal dynamics and time-varying workload.

Currently, traditional air-cooled DCs still hold a dominant share of the global market, even as the application of liquid-cooling and free-cooling systems is gaining traction [5]. However, air-cooled system tends to exhibit higher energy consumption levels compared to the other two alternative cooling methods [6]. Consequently, retrofitting DCs to enhance energy efficiency and reduce the Power Usage Effectiveness (PUE) presents a substantial challenge.

<table><tr><td colspan="2">Nomenclature</td></tr><tr><td>DC</td><td>Data center</td></tr><tr><td>MPC</td><td>Model predictive control</td></tr><tr><td>PUE</td><td>Power Usage Effectiveness</td></tr><tr><td>RMCU</td><td>Rack Mountable Cooling Unit</td></tr><tr><td>RL</td><td>Reinforcement learning</td></tr><tr><td>CFD</td><td>Computational fluid dynamics</td></tr><tr><td>COP</td><td>Coefficient of Performance</td></tr><tr><td>N</td><td>Server quantity in rack</td></tr><tr><td>i</td><td>Theithzone in rack from bottom to top</td></tr><tr><td>Si</td><td>Theithserver in rack</td></tr><tr><td>QRMCU</td><td>Air flow rate of RMCU</td></tr><tr><td>Qw</td><td>Coolant flow rate</td></tr><tr><td>QC,i</td><td>Airflow rate of theithserver in cold aisle</td></tr><tr><td>QS,i</td><td>Airflow rate entering into theithserver</td></tr><tr><td>QRMCU,i</td><td>Airflow rate ofithserver from RMCU</td></tr><tr><td>QL,i</td><td>Leakage airflow ofithserver</td></tr><tr><td>QB,i</td><td>Bypass airflow ofithserver</td></tr><tr><td>ρa</td><td>Density of airflow</td></tr><tr><td>cp,a</td><td>Thermal capacity of airflow</td></tr><tr><td>VC</td><td>Volume of inlet cold zone volume</td></tr><tr><td>TC,i</td><td>Temperature ofith inlet cold zone</td></tr><tr><td>TH,i</td><td>Temperature of theith outlet hot zone</td></tr><tr><td>X</td><td>Thermal mass related of theithserver</td></tr><tr><td>TS,i</td><td>Outlet air temperature from theithserver</td></tr><tr><td>PS,i</td><td>Total power dissipated insideithserver</td></tr><tr><td>VH</td><td>Volume of hot aisle zone</td></tr><tr><td>QH,i</td><td>Outlet air flowrate ofithhot zone</td></tr><tr><td>TReturn</td><td>Return air temperatures of RMCU</td></tr><tr><td>TSupply</td><td>Supply air temperature of RMCU</td></tr><tr><td>TCoolantIn</td><td>Inlet temperatures of coolant</td></tr><tr><td>TCoolantOut</td><td>Butlet temperatures of coolant</td></tr><tr><td>Qw</td><td>Coolant flow rate</td></tr><tr><td>cp,w</td><td>Thermal capacity of coolant</td></tr><tr><td>ρw</td><td>Density of coolant</td></tr><tr><td>U</td><td>Overall heat transfer coefficient of RMCU</td></tr><tr><td>A</td><td>Overall surface area of RMCU</td></tr><tr><td>xk</td><td>System state vector</td></tr><tr><td>yk</td><td>System output vector</td></tr><tr><td>uk</td><td>System control value</td></tr><tr><td>wk</td><td>System disturbances</td></tr><tr><td>dk</td><td>Slack variable in state space model</td></tr><tr><td>c</td><td>Maximum interval ofuk</td></tr><tr><td>Ui</td><td>Utilization of theithserver</td></tr><tr><td>Pco</td><td>Rated power consumption of server</td></tr><tr><td>PIT</td><td>Power consumption of IT equipments</td></tr><tr><td>Pcooling</td><td>Power consumption of cooling system</td></tr><tr><td>Pchiller</td><td>Power consumption of chiller</td></tr><tr><td>Pfan</td><td>Power consumption of fan</td></tr><tr><td>a0</td><td>Cubic coefficients of fan</td></tr><tr><td>a1</td><td>Quadratic coefficient of fan</td></tr><tr><td> $a_{2}$ </td><td>Primary coefficient of fan</td></tr><tr><td> $c_{0}$ </td><td>Quadratic coefficients of chiller</td></tr><tr><td> $c_{1}$ </td><td>Primary coefficient of chiller</td></tr><tr><td> $c_{2}$ </td><td>Constant coefficient of chiller</td></tr><tr><td>L</td><td>Depth of Hankel matrix and predict time</td></tr><tr><td>M</td><td>Length of historical data</td></tr><tr><td>m</td><td>Number of input values</td></tr><tr><td>p</td><td>Number of output values</td></tr><tr><td>s</td><td>Number of input steps applied to system</td></tr><tr><td>n</td><td>Order of system or initial data length</td></tr><tr><td> $H_{L}(x)$ </td><td>Hankel matrix of depth L for sequence x</td></tr><tr><td> $\{u_{k}, y_{k}\}_{k=0}^{M-1}$ </td><td>Input–output sequence of length M</td></tr><tr><td> $\{\overline{u}_{k}, \overline{y}_{k}\}$ </td><td>Future trajectory of system</td></tr><tr><td> $\mathbb{U}_{\Omega}, \mathbb{Y}_{\Omega}$ </td><td>Terminal constraint set of input and output</td></tr><tr><td> $y_{ref}$ </td><td>Reference value of y</td></tr><tr><td> $\lambda_{d}$ </td><td>Penalty factors</td></tr><tr><td>Q, P</td><td>Positive definite weighting matrices</td></tr><tr><td> $y_{max}, u_{max}$ </td><td>Maximum of input and output value</td></tr><tr><td> $y_{min}, u_{min}$ </td><td>Minimum of input and output value</td></tr><tr><td> $T_{ini}$ </td><td>Ambient initial temperature</td></tr><tr><td> $T_{s}$ </td><td>Data sampling period</td></tr></table>

DC is a cross-layer system with strong coupling between layers, in which cooling systems comprises of cooling towers, pumps, chillers and computer room air handler (CRAH) that interconnect within the cooling framework, as depicted in Fig. 1. A multitude of studies have been dedicated to enhance the efficiency of DC cooling, particularly focus on minimizing airflow mixing through structural design [7,8]. To effectively curtail power consumption while satisfying hard constraints, a variety of control strategies are implemented across different subsystems. This work delves into the thermal environment of the airflow conditional subsystem within an air-cooled rack-based DC and proposes a data-driven approach to control its operations

In rack-based cooling systems, IT equipment and Rack Mountable Cooling Units (RMCUs) are housed within individual racks, offering a more efficient and direct airflow path that swiftly adapts to workload fluctuations [9]. Due to this architectural benefit, it has gained significant research interest in recent years $[ 1 0 , 1 1 ]$ . Beyond the structural advantages, control strategies are pivotal in improving energy efficiency without risking server thermal safety [5]. Traditionally, DC cooling operations have relied on experienced technicians and simple controllers, such as ON/OFF, PID or hand-tuned controller tailored to specific setups. With escalating equipment scales and workload, there is a shift towards intelligent control strategies, including PID-based [12], fuzzy logic [13], and optimization-based control [14–17], etc. However, these methods are unable to synthesize the model accuracy, system constraints and performance optimization, highlighting the potential significance of data-driven MPC approaches. The aim of this study is to design a data-driven MPC algorithm, which not only inherite the advantages of regular ${ \mathrm { M P C } } ,$ but also effectively avoid the complex modeling processes. The proposed approach directly obtain control commands from the historical and real-time data to reach the control objective.

MPC is able to handle system constraints and provides optimal solution, which has already widely used in cooling system of DCs [5]. This method has shown good performance in temperature control and energy saving. Ogawa et al. did a series of studies in employing MPC for modular DC with more than 20% energy-savings, in which the modular DC is modeled as a three-order LTI system by subspace identification [18–20]. Endo et al. applied MPC for control and proposed a just-in-time modeling method in the predictive part, reducing the energy consumption by 28.9% [21]. Fang did a series of works on improving DC’s energy efficiency, such as constrained optimization methods with minimizing the energy consumption [22,23], neural network and joint optimization [24,25], subspace predictive control method [26]. Based on zonal model, an economic MPC strategy was designed to maintain temperature of each zone within an allowable range while minimizing operational costs of the cooling system [27]. Furthermore, in [28,29], sensor fault was considered and a fault tolerant control framework was designed. These methods all require explicit mathematical modeling and parameter identification, and then proceed to the optimization control process.

Mechanism-based and data-driven are two commonly used modeling methods. Hosein et al. provided a real-time mechanismbased temperature prediction method for rack-based DC by zonal model [30], which gave a theoretical and experimental foundation for the following works. To enhance the prediction efficiency of the dynamic temperature distribution in rack-based DC, Wang et al. proposed a temperature prediction method based on prediction-error method with different kinds of workload [31–33]. Rajput et al. set up a thermal model using power consumption and return air temperature as features to predict server inlet and outlet temperature, improving predictive precision by an average of 26.4% [34]. These methods are established based on a specific DC structure, which need pre-analysis of the DC, and the model cannot be updated in real time. This may decrease the control performance due to the changes of working state and the local characteristics of DC.

![](images/5263aa1180258354c0ae5c05a4860ac01cb03ea7d63e052a581c9c061fe51e5e.jpg)  
Fig. 1. The typical cross-layer structure of DC

Data-driven identification algorithms establish gray-box or black-box models, and taking the readily measured environment variables as inputs, avoiding the complex thermodynamic analysis. Grey-box model hybrids simplified physics-based modeling with data-driven approximations [35]. Sahar trained an artificial neural network model to predict the pressure distributions, and a zonal model was developed to analyze the temperature distributions for a row-based DC [36]. In [14], an effective and safe Reinforcement Learning (RL) was proposed to obtain the cooling system model, combining with MPC, the operational efficiency was significantly improved in a large-scale DC. The data-driven model based on deep learning requires a large amount and high-quality data, which may increase the time-consuming cost in training the model.

According to the above research, MPC is a preferred approach for DC cooling system, but faces challenges for obtaining effective model. In order to skip the pre-modeling process and get good control performance, we applied a data-driven MPC strategy directly with small sample historical input–output trajectory data, in the light of Willems’ fundamental lemma [37]. The stability and robustness guarantees of data-driven MPC for LTI system has already been proven [38]. Moreover, the method has been applied in building heating, ventilation, and air conditioning (HVAC) systems [39], power electronics systems [40], aerial robotics [41] and tank systems [38], achieving a comparable control performance of regular MPC.

In summary, to avoid the modeling process, we implement an improved robust data-driven MPC on a typical air-cooled rack-based DC. The contributions of this work are highlighted as follows:

(1) The linearity of the air-cooled rack-based DC thermal environment is analyzed through a data-driven method to illustrate the practicability of the method for such systems;

(2) A data-driven control strategy based on Willems’ fundamental lemma is developed for directly controlling the thermal environment of air-cooled rack-based DC without model identification procedure;

(3) A relaxation variable is included to compensate the external disturbances of DC, improving robustness of the proposed strategy.

The rest of this paper is organized as follows: Section 2 introduces the thermal model and energy consumption of the rackbased DC, formulating the control objective. Section 3 presents the pure data-driven MPC strategy with compensation, along with the control system structure. In Section 4, the effectiveness of proposed data-driven approach is demonstrated by CFD simulation experiments. Finally, Section 5 concludes the paper.

## 2. Model of rack-based DC

In this section, rack-based DC dynamic thermal environment is analyzed by zonal method.

## 2.1. Thermal model of rack-based DC

We consider a rack-based DC containing ?? servers and one RMCU located at the bottom of enclosure, which supplies cooled airflow. The detailed structure of cold and hot aisles in rack enclosure are shown in Fig. 2. Rack enclosure space could be divided into there parts: cold aisle, hot aisle and server. To simplify the description of mass and energy transmission in $\mathrm { D C } ,$ enclosure space is divided into 3 ∗ ?? zones. In each zone, the physical quantities, such as temperature and airflow rates, are assumed uniform. The detailed explanation of the model and formulations can be found in [30].

The RMCU’s cooling unit consists of industrial fans and a heat exchanger, where the airflow rate $Q _ { R M C U }$ of fans and the coolant flow rate $Q _ { w }$ of heat exchanger can be regulated within RMCU. Industrial fans breath in servers’ exhaust hot airflow, removing heat by coolant in heat exchanger and delivering cold air into cold aisle. The fans take the inlet cold air to cool the electronics and servers release hot air to the hot aisle. Besides, there may be airflow leakage through the server gaps due to pressure differences between hot and cold aisle, which result in hot air recirculation or cold air bypass. We covered the empty part in the rack with a dam board to prevent hot airflow from mixing.

Assume that the whole heat exchange process is in rack enclosure, that is, no airflow and thermal transfer between the outside environment and inside enclosure. There are 6 kinds of airflow interact with each other in cold aisle. We take the heat transfer process of airflow at server $S _ { i }$ as an example: (1) air flow rate $Q _ { C , i - 1 }$ , emerging from the below cold aisle zone, which does not exist in the first zone of clod aisle; (2) air flow rate $Q _ { C , i }$ entering into the above cold aisle zone; (3) air flow rate $Q _ { S , i }$ entering into server $S _ { i } \colon ( 4 )$ air flow rate $Q _ { R M C U }$ emerging from the RMCU for server $S _ { i } { \mathrm { ; } }$ (5) leakage airflow rate $Q _ { L , i }$ emerging from the ??th hot aisle zone; (6) bypass airflow rate $Q _ { B , i }$ entering into the ??th hot aisle zone. By the mass balance equation, the airflow relationship for the ??th inlet cold aisle zone is

![](images/ec2bd2139be82589ccf553aff714f8f75b48e68f401cac78bbb732bbda4227e1.jpg)  
Fig. 2. The thermodynamic process of the rack-based DC.

$$
Q _ {C, i} = Q _ {C, i - 1} + Q _ {R M C U} + Q _ {L, i} - Q _ {S, i} - Q _ {B, i}\tag{1}
$$

Based on heat conduction equation, the heat conservation equation for the ??th cold aisle zone is given by

$$
\begin{array}{r l} \rho_ {a} c _ {p, a} V _ {C} \frac {d T _ {C , i}}{d t} & = Q _ {C, i - 1} \rho_ {a} c _ {p, a} T _ {C, i - 1} + Q _ {R M C U} \rho_ {a} c _ {p, a} T _ {S u p p l y} + Q _ {L, i} \rho_ {a} c _ {p, a} T _ {H, i} \\ & \quad - Q _ {B, i} \rho_ {a} c _ {p, a} T _ {C, i} - Q _ {S, i} \rho_ {a} c _ {p, a} T _ {C, i} - Q _ {C, i} \rho_ {a} c _ {p, a} T _ {C, i} \end{array}\tag{2}
$$

where $\rho _ { a }$ and $c _ { p , a }$ are the density and thermal capacity of air, respectively, $V _ { C }$ is the inlet cold zone volume, $T _ { C , i }$ is the temperature of the ??th inlet cold zone, $T _ { H , i }$ is temperature of the ??th outlet hot zone, and $T _ { S u p p l y }$ is the temperature of supply cold airflow of RMCU.

The heat conduction equation for the ??th server zone is

$$
X \frac {d T _ {S , i}}{d t} = - Q _ {S, i} \rho_ {a} c _ {p, a} (T _ {C, i} - T _ {S, i}) + P _ {S, i}\tag{3}
$$

where ?? is thermal mass related to air temperature from the ??th server, $T _ { S , i }$ denotes the outlet air temperature from the ??th server, and $P _ { S , i }$ represents the total power dissipated inside ??th server.

The heat conduction equation for the ??th hot aisle zone reads

$$
\rho_ {a} c _ {p, a} V _ {H} \frac {d T _ {H , i}}{d t} = Q _ {S, i} \rho_ {a} c _ {p, a} T _ {S, i} - Q _ {L, i} \rho_ {a} c _ {p, a} T _ {H, i} + Q _ {B, i} \rho_ {a} c _ {p, a} T _ {C, i} + Q _ {H, i + 1} \rho_ {a} c _ {p, a} T _ {H, i + 1} - Q _ {H, i} \rho_ {a} c _ {p, a} T _ {H, i}\tag{4}
$$

where $V _ { H }$ denotes the volume of hot aisle zone, and $Q _ { H , i }$ denotes the outlet air flow rate in the ??th hot zone.

The heat conduction equations for air and coolant within the RMCU are

$$
\rho_ {a} c _ {p, a} V _ {a} (\frac {d T _ {S u p p l y}}{d t} + \frac {d T _ {R e t u r n}}{d t}) = \rho_ {a} c _ {p, a} Q _ {R M C U} (T _ {R e t u r n} - T _ {S u p p l y}) - \frac {U A}{2} (T _ {R e t u r n} + T _ {S u p p l y} - T _ {C o o l a n t I n} - T _ {C o o l a n t O u t})\tag{5}
$$

$$
\rho_ {w} c _ {p, w} V _ {w} (\frac {d T _ {C o o l a n t I n}}{d t} + \frac {d T _ {C o o l a n t O u t}}{d t}) = \rho_ {w} c _ {p, w} Q _ {w} (T _ {C o o l a n t I n} - T _ {C o o l a n t O u t}) + \frac {U A}{2} (T _ {R e t u r n} + T _ {S u p p l y} - T _ {C o o l a n t I n} - T _ {C o o l a n t O u t})\tag{6}
$$

where $T _ { R e t u r n }$ denote return air temperature of RMCU, $T _ { C o o l a n t I n }$ and $T _ { C o o l a n t O u t }$ are inlet and outlet temperatures of the coolant, respectively, ?? and $c _ { p , w }$ $\rho _ { w }$ are thermal capacity and density of the coolant, respectively, ?? and ?? denote the overall heat transfer coefficient and surface area inside RMCU, respectively. It is supposed that the server fan speed remains constant in this work

## 2.2. State space model

According to [27,28,31], the rack-based DC are formulated as a LTI system with measured disturbance as follows

$$
\begin{array}{c} x _ {k + 1} = A x _ {k} + B u _ {k} + B _ {d} w _ {k} \\ y _ {k} = C x _ {k} \end{array}\tag{7}
$$

where the state vector

$$
x _ {k} = [ T _ {C, 1} T _ {C, 2} \dots T _ {C, i} \dots T _ {C, N} T _ {S u p p l y} T _ {R e t u r n} T _ {C o o l a n t O u t} ] ^ {T}\tag{8}
$$

is the temperatures of each cold aisle zone, supply and return air temperatures of RMCU and coolant return temperature of heat exchanger. The disturbances

$$
w _ {k} = [ T _ {C o o l a n t I n} P _ {S, 1} P _ {S, 2} \dots P _ {S, i} \dots P _ {S, N} ] ^ {T}\tag{9}
$$

caused by coolant temperature and workload fluctuation, which indicates cold source temperature and server exhaust heat fluctuation in DC.

The control input $\boldsymbol { u } _ { k }$ includes the supplied air flow rate (or fan speed) and coolant flow rate (or valve opening percentage) of the heat exchanger in RMCU

$$
u _ {k} = [ Q _ {R M C U} Q _ {w} ] ^ {T}, \left| u _ {k + 1} - u _ {k} \right| \leq c\tag{10}
$$

where ?? is the maximum change of $\boldsymbol { u } _ { k }$ allowed between consecutive time steps.

To control the DC system using regular MPC methods, parameters of LIT system $( A , B , B _ { k } , C )$ are usually challenging to capture precisely by identification technique.

## 2.3. Energy consumption of the rack-based DC

The energy consumption of DC could be divided into two parts: IT system and cooling system. In this work, we assumes that all servers in the rack are active, thus the server energy consumption could be modeled as [42],

$$
P _ {S, i} = P _ {c o} \times U _ {i}\tag{11}
$$

where $U _ { i }$ is the utilization of the ??th server, and $P _ { c o }$ is the rated energy consumption of the server. As a result, the total energy consumption of IT equipment is

$$
P _ {I T} = \sum_ {i = 1} ^ {N} P _ {S, i}\tag{12}
$$

The cooling energy consumption could be directly obtained via measurement, or it could be computed by energy models, which contains chiller and fan, i.e.

$$
P _ {c o o l i n g} = P _ {c h i l l e r} + P _ {f a n}\tag{13}
$$

where, the chiller power consumption depends on the overall cooling load and Coefficient of Performance (COP) of chiller converting the rate of heat removed by chiller to power consumption [43]. A widely-used power consumption model is as follows

$$
P _ {c h i l l e r} = \frac {\rho_ {w} Q _ {w} c _ {p , w} (T _ {C o o l a n t O u t} - T _ {C o o l a n t I n})}{C O P}, T _ {C o o l a n t O u t} > T _ {C o o l a n t I n}\tag{14}
$$

where the term $\rho _ { w } Q _ { w } c _ { p , w } ( T _ { C o o l a n t O u t } - T _ { C o o l a n t I n } )$ indicates the heat removed by chiller.

In this work, it is assumed that the COP subjects to a second order curve [44], which could be improved by increasing inlet coolant temperature, i.e.

$$
C O P (T _ {C o o l a n t I n}) = c _ {0} T _ {C o o l a n t I n} ^ {2} + c _ {1} T _ {C o o l a n t I n} + c _ {2}\tag{15}
$$

where $c _ { 0 } , c _ { 1 }$ and $c _ { 2 }$ are quadratic, primary and constant coefficients of chiller, respectively.

The energy consumption of fan can be approximated by a cubic function of the rotational speed given in revolutions per minute [45]. The air flow rate passing through the RMCU is linear with the fan speed. So the total power of the fan mounted on the rack can be modeled by a 3rd-order polynomial:

$$
P _ {f a n} = a _ {0} Q _ {R M C U} ^ {3} + a _ {1} Q _ {R M C U} ^ {2} + a _ {2} Q _ {R M C U}\tag{16}
$$

where $a _ { 0 } , a _ { 1 }$ and $a _ { 2 }$ are cubic, quadratic and primary coefficients of RMCU air flow rate, respectively. The parameters $a _ { 0 } , a _ { 1 } , a _ { 2 } , c _ { 0 } ,$ $c _ { 1 }$ and $c _ { 2 }$ are constant and could be obtained by regression.

We use Power Usage Effectiveness (PUE) metric to evaluate utilization rate of energy consumption in $\mathrm { D C } ,$

$$
P U E = \frac {P _ {\text { cooling }} + P _ {I T}}{P _ {I T}}\tag{17}
$$

which is the ratio of the total amount of power used by the whole DC to the power delivered to its IT equipment. Higher PUE indicates a higher power expense on the cooling system, implying a waste of power.

The study in [46] indicates that the coolant temperature, coolant flow rate and air flow rate influence system energy consumption in varying degrees. Meanwhile, the inlet temperature of the servers need to be maintained near 25 $^ \circ \mathrm { C }$ for safety and efficiency of server operation [47]. The main objective of this work is to find an optimal or sub-optimal strategy $u _ { k } = \{ Q _ { R M C U } , Q _ { w } \}$ of RMCU, which ensures server inlet temperature in safe range under system constraints as well as reducing energy consumption of the cooling system.

## 3. Robust data-driven MPC for rack-based DC

As detailed in Sections 2.1 and 2.2, an LTI model is established to describe rack-based DC system. However, the model obtained by zonal model is not accurate enough to reflect real-time changes of actual system. Even so, a lot of parameters are still need to be identified. In this part, we design a pure data-driven MPC controller to ensure the efficient operation of thermal environment in rack-based DC system, without explicitly modeling process. Section 3.1 introduces the theoretical basis of data-driven MPC, Section 3.2 develops a robust data-driven MPC strategy for the rack-based DC control system.

## 3.1. Data-driven MPC framework

Our goal is to control an unknown LTI system of order ??, denoted by ??, with ?? inputs and $p$ outputs, using only measured input–output data. We first consider the following standard definition of persistence of excitation.

Definition 1 ([37]). A Hankel matrix of depth ?? generated from sequence $\{ \boldsymbol { x } _ { k } \} _ { k = 0 } ^ { M - 1 }$ with $\boldsymbol { x } _ { k } \in \mathbb { R } ^ { m }$ expressed by Eq. (18) satisfies the condition of ???????? $H _ { L } ( x ) ) = m L ,$ , which is called persistency excitation of order $L$

$$
H _ {L} (x) := \left[ \begin{array}{c c c c} x _ {0} & x _ {1} & \dots & x _ {M - L} \\ x _ {1} & x _ {2} & \dots & x _ {M - L + 1} \\ \vdots & \vdots & \ddots & \vdots \\ x _ {L - 1} & x _ {L} & \dots & x _ {M - 1} \end{array} \right]\tag{18}
$$

Definition 2. We define that an input−output sequence $\left\{ u _ { k } , y _ { k } \right\} _ { k = 0 } ^ { M - 1 }$ is a trajectory of an LTI system $G ,$ if there exists an initial condition $\overline { { x } } \in R ^ { n }$ as well as a state sequence $\left\{ x _ { k } \right\} _ { k = 0 } ^ { M - 1 }$ such that

$$
\begin{array}{c} x _ {k + 1} = A x _ {k} + B u _ {k}, x _ {0} = \bar {x} \\ y _ {k} = C x _ {k} \end{array}\tag{19}
$$

for $k = 0 , \ldots , M - 1$ , where $( A , B , C )$ is a minimal realization of $G .$

Theorem 1 (See [48]). Suppose $\{ u _ { k } ^ { d } , y _ { k } ^ { d } \} _ { k = 0 } ^ { M - 1 }$ is a trajectory of an LTI system $G . \ u _ { d }$ is persistently exciting of order $L + n ,$ , where ?? denotes the system order. $\{ \bar { u } _ { k } , \bar { y } _ { k } \} _ { k = 0 } ^ { L - 1 }$ is system’s trajectory if and only $i f \alpha \in \mathbb { R } ^ { M - L + 1 }$ is found to satisfy the following equation

$$
\left[ \begin{array}{c} H _ {L} (u ^ {d}) \\ H _ {L} (y ^ {d}) \end{array} \right] \alpha = \left[ \begin{array}{c} \bar {u} \\ \bar {y} \end{array} \right]\tag{20}
$$

In general, MPC relies on a model of the plant to predict future states and optimize a given performance. Theorem 1 provides an alternative to model the system, which provides an appealing data-driven characterization of trajectories for the unknown LTI system. Thus, to implement a data-driven MPC scheme, one can simply replace the system dynamics characterization by input–output trajectories without requiring any prior identification step. The proposed data-driven MPC scheme, at time $t ,$ is formulated as

$$
\min _ {\alpha (t), \bar {u} (t), \bar {y} (t)} \sum_ {k = 0} ^ {L - 1} l (\bar {u} _ {k} (t), \bar {y} _ {k} (t))\tag{21a}
$$

$$
s. t. \quad \left[ \begin{array}{c} \bar {u} _ {[ - n, L - 1 ]} (t) \\ \bar {y} _ {[ - n, L - 1 ]} (t) \end{array} \right] = \left[ \begin{array}{c} H _ {L + n} (u ^ {d}) \\ H _ {L + n} (y ^ {d}) \end{array} \right] \alpha (t)\tag{21b}
$$

$$
\left[ \begin{array}{c} \bar {u} _ {[ - n, - 1 ]} (t) \\ \bar {y} _ {[ - n, - 1 ]} (t) \end{array} \right] = \left[ \begin{array}{c} u _ {[ t - n, t - 1 ]} \\ y _ {[ t - n, t - 1 ]} \end{array} \right]\tag{21c}
$$

$$
\left[ \begin{array}{c} \bar {u} _ {[ L - n, L - 1 ]} (t) \\ \bar {y} _ {[ L - n, L - 1 ]} (t) \end{array} \right] \in \left[ \begin{array}{c} \mathbb {U} _ {\varOmega} \\ \mathbb {Y} _ {\varOmega} \end{array} \right]\tag{21d}
$$

Compared with regular MPC schemes, constraint Eq. (21b) replaces the system dynamics, ??(??) is taken into consideration as an decision variable. Eq. (21c) ensures that the internal state of the true trajectory aligns with the internal state of the predicted trajectory at time $t ,$ that is the ?? elements $\{ \bar { u } _ { k } ( t ) , \bar { y } _ { k } ( t ) \} _ { k = - r } ^ { L - 1 }$ specify the initial condition of the system. Terminal constraint Eq. (21d) implies that at the end of the prediction, the system enters the terminal set, which can ensure the system stability.

## 3.2. Robust data-driven MPC

To address the impact of external disturbances and the internal nonlinear dynamics of the system, we treat the deviations as a form of matching disturbance, denoted by the slack variable $d _ { k }$ , as defined in Eq. (22a). This implies that the state transition $x _ { k + 1 }$ is also subject to the influence of this matching disturbance $d _ { k }$ . Consequently, the system model represented by Eq. (7) is reformulated to account for this as follows,

$$
x _ {k + 1} = A x _ {k} + B (u _ {k} + d _ {k})\tag{22a}
$$

$$
y _ {k} = C x _ {k}\tag{22b}
$$

where, in view of disturbance, the stacked data-dependent Hankel matrices do not span the systems trajectory space exactly and thus, the output trajectories cannot be predicted accurately. In comparison to the RMCU, which provides cooling, the disturbance has a opposite effect by heating the system, such as the hot airflow exhaust from servers. Hence, it is considered negative with the bounded $d _ { k } \leq \bar { d }$ for $\bar { d } \leq 0 .$

Inspired by [49], with a initial input–output trajectory $( u _ { [ t - n , t - 1 ] } , y _ { [ t - n , t - 1 ] } )$ of length ?? and historical data $\{ u ^ { d } , y ^ { d } \} _ { k = 0 } ^ { M - 1 }$ of the unknown rack-based DC system, a bounded slack variable $d _ { k } ( t )$ is introduced to compensate the model input by relaxing equality constraint (21b).

We propose a robust data-driven MPC framework of rack-based DC, which can be formulated as

$$
\min _ {\alpha (t), d (t), \bar {u} (t), \bar {y} (t)} \sum_ {k = 0} ^ {L - 1} l (\bar {u} _ {k} (t), \bar {y} _ {k} (t)) + \lambda_ {d} \| d _ {k} (t) \| _ {2} ^ {2}\tag{23a}
$$

$$
s. t. \quad \left[ \begin{array}{c} \bar {u} _ {[ - n, L - 1 ]} (t) + d _ {[ - n, L - 1 ]} (t) \\ \bar {y} _ {[ - n, L - 1 ]} (t) \end{array} \right] = \left[ \begin{array}{c} H _ {L + n} (u ^ {d}) \\ H _ {L + n} (y ^ {d}) \end{array} \right] \alpha (t)\tag{23b}
$$

$$
\left[ \begin{array}{c} \bar {u} _ {[ - n, - 1 ]} (t) \\ \bar {y} _ {[ - n, - 1 ]} (t) \\ d _ {[ - n, - 1 ]} (t) \end{array} \right] = \left[ \begin{array}{c} u _ {[ t - n, t - 1 ]} \\ y _ {[ t - n, t - 1 ]} \\ d _ {[ t - n, t - 1 ]} \end{array} \right]\tag{23c}
$$

$$
\bar {y} _ {[ L - n, L - 1 ]} (t) \in \mathbb {Y} _ {\Omega}\tag{23d}
$$

$$
d _ {k} (t) <   \bar {d}\tag{23e}
$$

$$
y _ {m a x} \leq \bar {y} _ {k} (t) \leq y _ {m a x}\tag{23f}
$$

$$
u _ {m i n} \leq \bar {u} _ {k} (t) \leq u _ {m a x}\tag{23g}
$$

$$
\left| \bar {u} _ {k + 1} (t) - \bar {u} _ {k} (t) \right| \leq c\tag{23h}
$$

where $l ( \bar { u } _ { k } ( t ) , \bar { y } _ { k } ( t ) ) = \| \bar { y } _ { k } ( t ) - y _ { r e f } \| _ { O } ^ { 2 } + \| \bar { u } _ { k } ( t ) \| _ { P } ^ { 2 } .$ , ??, ?? are given positive definite weighting matrices, ?? is the predictive horizon and $\mathbb { Y } _ { \varOmega }$ is the terminal set. With large enough penalty factors $\lambda _ { d }$ in cost function, small values of $d _ { k } ( t )$ are preferred. Here, $d _ { k } ( t )$ is augmented with $u _ { k } ( t )$ to create the overall input.

The proposed data-driven MPC method is based on enough historical data collection, which meets the condition of persistency excitation. The overall structure of the control system is shown in Fig. 3, where the historical data is dynamically updated so that the data can describe the system over time.

The proposed robust data-driven MPC scheme is solved online, which is summarized in Algorithm 1. That is, with a length of ?? historical data and length of ?? initial data, after solving the optimization problem with ?? prediction horizon online, the first ?? inputs are applied to the system. Thereafter, the receding horizon is shifted by ?? steps, and the whole scheme is repeated. In [38], the theoretical analysis of exponential stability for the data-driven MPC was provided.

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
1) At time $t$, take the past $n$ measurements $u_{[t - n,t - 1]}$, $y_{[t - n,t - 1]}$ for initial and solve the MPC problem.  
2) Apply the input sequence $u_{[t,t + s - 1]} = \bar{u}_{[0,s - 1]}^{*}(t)$ over the next $s$ step.  
3) Set $t = t + s$ and go back to 1)
</div>

## 4. Experiment results

The control performance of the robust data-driven MPC method are verified using a CFD simulation platform. Section 4.2 describes the experimental environment, data transmission approach and parameter settings. In Sections 4.4 and 4.3, we compare the proposed method with regular MPC and discuss the influence of the selected parameters on control effect. A summary of the experimental variables and parameters is provided in Table 1.

![](images/04cda12d987b7c7f471b02f901d7999d8d332a3928e508aed31b755e7ebfb189.jpg)  
Fig. 3. System framework of robust data-driven MPC for rack-based DC.

Table 1  
Table of parameters for rack-based DC.

<table><tr><td>Notations</td><td>Value</td><td>Notations</td><td>Value</td></tr><tr><td> $N$ </td><td>9</td><td> $P_{co}$ </td><td>1 kW</td></tr><tr><td> $U_i$ </td><td>75%</td><td> $a_0$ </td><td>2.76</td></tr><tr><td> $a_1$ </td><td>-1.786</td><td> $a_2$ </td><td>0.77</td></tr><tr><td> $c_0$ </td><td>0.0068</td><td> $c_1$ </td><td>0.0008</td></tr><tr><td> $c_2$ </td><td>0.458</td><td> $T_{ini}$ </td><td>30 °C</td></tr><tr><td> $\rho_a$ </td><td>1.29 kg/m3</td><td> $c_{p,a}$ </td><td>1.04 J/(kg × °C)</td></tr><tr><td> $\rho_w$ </td><td>1000 kg/m3</td><td> $c_{w,a}$ </td><td>4.2 × 103 J/(kg × °C)</td></tr><tr><td> $T_s$ </td><td>15 s</td><td> $T_{ref}$ </td><td>25 °C</td></tr><tr><td> $L$ </td><td>30</td><td> $M$ </td><td>240</td></tr><tr><td> $T_{max}$ </td><td>32 °C</td><td> $T_{min}$ </td><td>18 °C</td></tr><tr><td> $u_{max}^{Q_{RMCU}}$ </td><td>0.222 m3/s</td><td> $u_{min}^{Q_{RMCU}}$ </td><td>0.444 m3/s</td></tr><tr><td> $u_{max}^{Q_w}$ </td><td>0.0005 m3/s</td><td> $u_{min}^{Q_w}$ </td><td>0.0003 m3/s</td></tr><tr><td> $Q$ </td><td>100 × I</td><td> $R$ </td><td>10 × I</td></tr><tr><td> $\lambda_d$ </td><td>10000</td><td> $T_{CoolantIn}$ </td><td>18 °C</td></tr></table>

## 4.1. Experiment scenarios

We construct an air-cooled rack-based DC with 9 servers and 1 RMCU mounted at the bottom as a case study in CFD transient simulator to evaluate the performance of proposed robust data-driven MPC method. The architecture of the simulation model are shown in Fig. 4. The RMCU has two fans with the same speed and one heat exchanger with coolant flow. The fan speed is normalized into [0, 1] with maximal 0.444 $\mathbf { m } ^ { 3 } / s$ volumetric airflow rate. The supply coolant temperature is ${ 1 8 ~ ^ { \circ } \mathrm { C } }$ with the maximal coolant flow rate 0.0005 $\mathbf { m } ^ { 3 } / s .$ . The server utilization is set to be 75% with an initial rack enclosure temperature of $3 0 ~ ^ { \circ } \mathrm { C }$

The reference temperature of the server inlet air flow is set to be $y _ { r e f } \ = \ 2 5 \ ^ { \circ } \mathrm { C } .$ . Since the server inlet temperature in rack increases from bottom to top, the topmost server is attached with a temperature sensor as the mainly controlled value. The rack inlet temperature threshold $T _ { m a x }$ is set to be $3 2 ~ ^ { \circ } \mathrm { C }$ according to American Society of Heating, Refrigerating and Air-Conditioning Engineers(ASHRAE) guideline [50]. The iteration step for transient simulation is set to be 50 to ensure the accuracy of model solution. All simulations run on a computer equipped with an Intel Core i9-14900 14-core CPU and 64 GB RAM.

An experiment is conducted to demonstrate the linear characteristics of a rack-based DC cooling system, specifically its adherence to the principles of additivity and homogeneity, as expressed by the equations $L ( u _ { 1 } + u _ { 2 } ) = L ( u _ { 1 } ) + L ( u _ { 2 } )$ and $\alpha L ( u ) = L ( \alpha u )$ . We introduce sinusoidal and random inputs into the open-loop DC system. The system’s response, depicted in Fig. 5, which generally aligns with linear behavior. However, certain deviations are observed, indicating that nonlinear effect is pronounced. The selection of output variables can affect the control performance.

## 4.2. Data collection and simulation setup

Historical data are gathered using a $T _ { s } = 1 5 ~ s$ sampling period, reflecting the relatively rapid thermal dynamics of the target system. During online simulation, we continuously update the input control signal and collect sensor outputs to historical dataset of the open-loop system. To maintain safety of the system, each control variable must be confined within a safe operational range, especially during the exploration phase. Initially, in the absence of specific data, this range can be set to be conservative and then expand gradually [14], which employs a range-limited uniform random walk for each control variable, as detailed in Eq. (24).

![](images/e72840e84fa8bb4fd8211ad8e162a9c425e5819f53cf518e3e0bac8e2a48d57f.jpg)  
Fig. 4. Architecture of DC system.

![](images/1534627ad975f465a5979133a40b5eeec1bbd03c4a2e3a92105704f2d5193def.jpg)  
(a) Temperature response for sinusoidal input  
(b) Temperature response for random input  
Fig. 5. System linearity test of air-cooled rack-based DC.

$$
u _ {d} (t + 1) = m a x (u _ {\mathrm{min}}, \mathrm{min} (u _ {m a x}, u _ {d} (t) + v _ {d})), v _ {d} \sim U n i f o r m (- c, c)\tag{24}
$$

where the variable $u _ { d } ( t )$ represents the control input for the RMCU at time step ??, while ?? denotes the maximum allowed absolute change between successive time steps. From this, we construct a historical trajectory consisting of ?? = 240 data points, denoted as $\{ u _ { d } , y _ { d } \} _ { d = 0 } ^ { M - 1 }$ . To enable the proposed method in practical engineering, the selected output variables should be the exact sensor measurements. Besides, the selection of output variables should make trade-off between the observability guarantee of the system (select enough output variables) and blocking out nonlinear links (the number of variables should not be too large) of an actual nonlinear system. Thus, we chose two different sets of measured output conditions for comparative experiments: $\{ T _ { C , 1 } , T _ { C , 9 } , T _ { S u p p l y } , T _ { R e t u r n } \}$ and $\{ T _ { C , 1 } , T _ { C , 9 } , T _ { S u p p l y } , T _ { R e t u r n } , T _ { W a t e r O u t } \}$ . The performance of the proposed data-driven MPC method is verified by two scenarios: during system start-up and when the system temperature is returning to normal from an abnormal state.

The prediction horizon ?? is set to be 30 steps, with the initial input–output trajectory length ?? = 9. Design parameters are as follows,

$$
Q = 1 0 0 \times I, R = 1 0 \times I, \lambda_ {d} = 1 0 0 0 0
$$

![](images/e9608dbbdb4006747ce8a6e7b3e3b263deabce4a9a64aea3133fb38dbeaf16e8.jpg)  
Fig. 6. Data transmission of the control system.

![](images/5d3457f987e4325a57adb18d929ae8874119b4903cc1f59c2410ac285650c5d7.jpg)

![](images/c163d0e1f04be6dfa302725dd225e535a6abc9cd1a6aff5dbfc316d91835912e.jpg)

![](images/325742654b3b3fb9d8154b355b09206605b64cfa097282ae1127f21e293120f6.jpg)

![](images/8598781c8f4d8872749942b887ded4efb0194860fd9b9d8fd5e240796c26e5fd.jpg)

![](images/b0974046c9b31aca69c1f28806fc9897c80c5ece99f164024481d89b8c301e39.jpg)

![](images/cc1b46f25851b7603865e606d089473e9ed58ecadbb2ef20fc21974efe9e80ba.jpg)

![](images/02f9cdc127fb48e7f612bd15f59bd2a1d8ab2a14a9d7266d3cf99c1c4b87f1b3.jpg)

![](images/bfca92ae6f5beb6d88d78add7662a6d7b4eb0be5d0a0483be1dd8158ca86f9d9.jpg)

![](images/eeba38c576973f9b976a1341ab22dc4ec9bec0f8009e24891ac66f6a2d2cb427.jpg)  
(a) Measured outputs:{TC,1, TC,9, Ts upply, TReturn)

![](images/a1538786c8a29038d5f358e9e48fae74d2f241e2c8d9c3769faed781f4bf7dfd.jpg)  
(b) Measured outputs:{TC,1, TC,9, Ts upply, TReturn, TWaterOut)  
Fig. 7. Dynamic performance comparison when DC start-up.

The optimization problem is solved by IPOPT with YALMIP toolbox. The schematic of the control system is illustrated in Fig. 6, where data interchange is conducted via CSV files, represented by the arrows indicating the direction of data flow.

## 4.3. Comparison of parameters for data-driven MPC

In this section, we examine the impact of system output number ?? and control steps ?? on the control effectiveness for the rack-based DC system. Two operational conditions are considered.

## 4.3.1. Condition1: DC system start-up

Fig. 7 illustrates a comparison of control performance for the rack-based DC during start-up, with different number of outputs and control step sizes. It is observed that the data-driven MPC approach with $\{ T _ { C , 1 } , T _ { C , 9 } , T _ { S u p p l y } , T _ { R e t u r n } \}$ achieves a smoother, albeit slower, control process compared to the one with an additional output. Furthermore, increasing the control step size ?? introduces more system fluctuations. For instance, when ?? = 3 under the four outputs condition, the system fails to fully stabilize at a fixed value. Conversely, when $s = 1$ and ?? = 2, the steady-state errors are relatively lower at 1.2% and 1.7%, respectively. The control performance, in terms of both steady-state error and temperature dynamics, is markedly superior with four measured outputs compared to the five-outputs case. This underscores the importance of selecting appropriate input–output trajectories for designing an effective data-driven controller.

![](images/350041d57a0586f2656fb3b79b5e4ebdf7f58bcac143ead8658eb0c40eaae670.jpg)  
Fig. 8. Snapshots of servers temperature distribution when start-up.

![](images/ee2bb85fd62de9801d35b8a669a5e7f0e19aee55bd88f406c3960249edd231d9.jpg)

![](images/ad06e6e484c82a6967004f2e9f34fd3f18a42eb0582bcb1b8c43b7281f8ec71b.jpg)

![](images/17af1b3d40b36d2cbc3a64f0c3463e717eb4fbf132b3c20c3bddda2e54cf49f7.jpg)

![](images/b0bcf74973abd1c001efb0896277ec5c998f00bf6235c1e140ae2bce9eacd2bd.jpg)

![](images/a7d1f31c4a659c82a5a16f25a7f4504d54ffabfacefc41bbe6fdb4618dcf2439.jpg)

![](images/55a2123d1b61ed24abf2373049722641c5991cfc736bf40641358f847b47e67c.jpg)

![](images/d614379f4ca06018093031cd8839ba35fbc2a2d4fd464c8e4d185573417e32bf.jpg)

![](images/726156ed8c4f758c13a0f967fc2479e1fe04738ac04107e464b0b6dd79910d49.jpg)

![](images/d842a1c9c69d37722e40a01d146c286af703e434b367767c0bb70eafab47e7f5.jpg)  
(a) Measured outputs:{TC,1, TC,9, Ts upply, T Return}

![](images/1313f5f889e58662abedc52e285c0b014ffaf897a0b5b062b985bfa92d4b8def.jpg)  
(b) Measured outputs:{TC,1, TC,9, Ts upply, T Return, TWaterOut}  
Fig. 9. Dynamic performance comparison when DC recovers from abnormal condition. (For interpretation of the references to color in this figure legend, the reader is referred to the web version of this article.)

Fig. 8 depicts snapshots of the server inlet temperature distribution at 2-min intervals during the DC start-up. It is evident that the top server inlet temperature within the rack is the highest, leading to the formation of hotspots as the servers heat up. With the RMCU timely and appropriate cooling intervention, the temperature distribution within the rack progressively equalizes. The entire regulation process takes approximately 16 to 20 min to reach a uniform state. The PUE of DC stable state is shown as Table 2.

## 4.3.2. Condition2: DC system recover from abnormal

Fig. 9 illustrates the dynamic performance of rack-based DC systems recovering from an abnormal scenario, under different measured outputs and control step sizes $p .$ In the given scenario, the rack-based DC regains control at 27 min, as indicated by the green line. Controllers utilizing both four measured outputs $\{ T _ { C , 1 } , T _ { C , 9 } , T _ { S u p p l y } , T _ { R e t u r n } \}$ and five measured outputs $\{ T _ { C , 1 } , T _ { C , 9 } , T _ { S u p p l y } , T _ { R e t u r n } , T _ { W a t e r O u t } \}$ are capable of restoring the DC to its reference temperature. However, the controller with four measured outputs maintains a smaller steady-state error but more volatile with larger ??. The recovery process is more rapid with the controller that includes five measured outputs. Increasing the control step size ?? results in greater system fluctuations. For instance, when $s = 3$ under the condition with four measured outputs, the system’s steady state fails to fully stabilize. In contrast, when $s = 1$ or $s = 2$ , the steady-state error is 1% and 1.5%, respectively. For the scenario with five measured outputs, the steady-state error is greater, approximately 6.4%.

Fig. 10 depicts temperature distribution snapshots of the cold asile and hot asile changing over time during the recover process. For cold asile, at the initial phase, the temperature is significantly much higher than the limit, but the rate of cooling is relatively fast. However, hot asile accumulates a significant amount of heat during system anomaly, the RMCU also tries to cool the hot aisle. Combined with Fig. 9, the RMCU could quickly cool down the rack-based DC system to a lower temperature (about 1 min) and gradually adjust it to a reference value. Comparing the changing trend of return air temperature and control inputs of RMCU, when the temperature of hot aisle returns to the same level as the temperature before the abnormal condition, then the control inputs of RMCU begin to decrease, temperature of the cold aisle increases slightly and the system steady-state error decreases. This indicates that the system returns to equilibrium after RMCU works hard to consume the accumulated heat in abnormal condition. The entire regulation process takes approximately 20 to 25 min to reach the equilibrium state.

![](images/dc4917385d3f4b5818bd3a09f04d7e9863f168cc9d24c6cd83adfbec56b6efa7.jpg)  
Fig. 10. Snapshots of servers temperature distribution when recover from abnormal

Overall, for the above two experimental scenarios, the steady-state values of four measured output condition are lower than that of five measured output condition. The data-driven MPC is theoretically suitable for LTI systems, while the rack-based DC is not a standard LTI system with a certain degree of nonlinearity. Introducing more output variables may increase the nonlinearity leading to the deterioration of the control performance. In addition, we also tested the case with three outputs. The results show that no matter how to choose the output variables, the system cannot be stabilized. Therefore, for the actual controlled object, how to select the appropriate input and output data is still an open problem, and we leave this problem for our future study.

## 4.4. Comparison with regular MPC

As a comparison, we also built a regular MPC algorithm for the rack-based DC system. Referring to [27], the system model parameters are obtained by subspace identification method. The regular MPC is formulated as

$$
\min _ {u (t)} \sum_ {k = 0} ^ {L - 1} l (u _ {k} (t), y _ {k} (t))\tag{25a}
$$

$$
s. t. x _ {k + 1} = A x _ {k} + B u _ {k} + B _ {d} w _ {k}\tag{25b}
$$

$$
y _ {k} (t) = C x _ {k} (t)\tag{25c}
$$

$$
w _ {k + 1} (t) = w _ {k} (t)\tag{25d}
$$

$$
y _ {[ L - 1, L - n ]} (t) \in \mathbb {Y} _ {\Omega}\tag{25e}
$$

$$
y _ {m i n} \leq y _ {k} (t) \leq y _ {m a x}\tag{25f}
$$

$$
u _ {m i n} \leq u _ {k} (t) \leq u _ {m a x}\tag{25g}
$$

$$
\left| u _ {k + 1} (t) - u _ {k} (t) \right| \leq c\tag{25h}
$$

where constraint Eq. (25f) is inlet temperature limits, Eq. (25g) is the upper and lower bounds of the control input, and Eq. (25h) is the maximum absolute change allowed between consecutive time steps. The state space model of regular MPC algorithm is identified by a subspace identification algorithm N4SID.

Fig. 11 presents a comparative analysis of the dynamic performance between regular MPC and data-driven MPC (utilizing four measured outputs: $\{ T _ { C , 1 } , T _ { C , 9 } , T _ { S u p p l y } , T _ { R e t u r n } \} )$ during the start-up and recovery from abnormal conditions of the DC. After each iteration, the state space model is re-identified. Both regular and data-driven MPC controllers are capable of maintaining the DC at a steady state. The data-driven MPC exhibite a steady-state error of approximately 1%, superior to the 4.5% error observed with regular MPC. The data-driven approach, while providing a smoother system response, has a slower dynamics process. Specifically: (1) Upon DC start-up, the time to reach a steady state for data-driven MPC is approximately 22 min, while 7.8 min for regular MPC. (2) During recovery from abnormal condition, the time to regain control for data-driven MPC is about 24.75 min, while 20 min for regular MPC. (3) Due to the large oscillation of the actuator during adjustment process, regular MPC demonstrates a larger overshoot compared to the data-driven method. Besides, the fan speed and coolant flow rates are relatively large for the five-output condition, comparatively, the PUE of the system should be relatively high. The PUE of DC stable state is shown as Table 2. Compared with the regular MPC, the robust data-driven MPC method proposed in this work has certain advantages in energy consumption saving on average.

![](images/3787f915dd9f4033056f99d67d67c49b3bfbe9e5a4b41b319db939a80531497c.jpg)

![](images/b9e69fe586b8c0a9755a54f9d0a9471b6282fe083f93766bb11e787c28d73b42.jpg)

![](images/8ec9c251a2bb09f44f5dbc3f5b11175a14390b4fd4299e8aa7651b62cfb94dda.jpg)

![](images/ec555b4049d377ff64acb677f7527ee4238f4d79a2e7b0232bbc5e15a96753f5.jpg)

![](images/0d5f67ab20e9c2a8346b52d88de6a338b0454db1d5426724b7ca2ab2a8ca0217.jpg)

![](images/e43f2fe945d6e958b58e6f9b604ffeec890befa101e66c50ff6bd12c5a5137be.jpg)

![](images/a80df6f99256b101eb625a6651bb9867297647b0c7b6c6affd3e74d76df48a28.jpg)

![](images/16dfb61808a646c3591b4b5bd59fcc2b315c4c50c21df1133a3db1154ee1ae9e.jpg)

![](images/875fb801a91be6cd7d5530bbe6eded1b9650fcb15a8dd5224efd64ae2a877ae5.jpg)  
(a) Start-up

![](images/848887b7277e7d85c93f373d4d09ca40499fa9b467ac3cfa83a6b0f272b1067d.jpg)  
(b) Recover from abnormal

Fig. 11. Dynamic performance comparison of data-driven MPC and regular MPC.  
Table 2  
PUE of the DC in different condition.

<table><tr><td rowspan="2">PUE</td><td colspan="3">4 Measured outputs</td><td colspan="3">5 Measured outputs</td><td rowspan="2">Regular MPC</td></tr><tr><td>m = 1</td><td>m = 2</td><td>m = 3</td><td>m = 1</td><td>m = 2</td><td>m = 3</td></tr><tr><td>Start-up</td><td>1.378</td><td>1.366</td><td>1.352</td><td>1.375</td><td>1.372</td><td>1.369</td><td>1.394</td></tr><tr><td>Recover</td><td>1.379</td><td>1.374</td><td>1.366</td><td>1.401</td><td>1.398</td><td>1.397</td><td>1.389</td></tr></table>

## 5. Conclusion

This paper introduces a data-driven control strategy for the cooling system in rack-based DCs. This approach circumvents the need for explicit system parameter identification, which is particularly advantageous given the intricate thermal dynamics of DC environments. Simulation comparisons demonstrate the efficacy of this approach. The conclusions of this work are as follows:

(1) A data-driven control strategy based on Willems’ fundamental lemma is developed to directly control air-cooled rack-based DC’s thermal environment without a model identification procedure. In scenarios of rack-based DC startup and abnormal recovery, the experiments show that the steady error can be guaranteed to be less than 2%.

(2) A relaxation variable is included to compensate for the external disturbances of rack-based DC, improving the robustness of the proposed strategy. Compared with regular MPC, the proposed robust data-driven MPC can improve the control accuracy, providing an alternative solution for rack-based DCs' thermal control.

(3) The selection of output variables affects the control performance, and the experimental results show that the selection of four output data $\{ T _ { C , 1 } , T _ { C , 9 } , T _ { S u p p l y } , T _ { R e t u r n } \}$ can achieve better control effectiveness for the rack-based DC.

(4) The proposed robust data-driven MPC method has a slightly better energy-saving effect and smoother adjustment of the actuator than regular MPC.

However. this study does not delve into robust control techniques to counteract disturbances stemming from load fluctuations and other external factors. Developing an adaptive, robust, data-driven MPC that can dynamically respond to server load imbalances is a significant area for future research

## CRediT authorship contribution statement

Yiran Li: Writing – review & editing, Writing – original draft, Validation, Methodology. Chao Yang: Writing – review & editing, Software, Data curation. Yuanqing Xia: Supervision, Methodology, Conceptualization.

## Declaration of competing interest

The authors declare that they have no known competing financial interests or personal relationships that could have appeared to influence the work reported in this paper.

## Acknowledgment

This work was supported by the National Natural Science Foundation of China under Grant 61836001.

## Data availability

Data will be made available on request.

## References

[1] Y. Wang, C.-h. Guo, C. Du, X.-j. Chen, L.-q. Jia, X.-n. Guo, R.-s. Chen, M.-s. Zhang, Z.-y. Chen, H.-d. Wang, Carbon peak and carbon neutrality in China: Goals, implementation path, and prospects, China Geol. 4 (2021) 1–27, http://dx.doi.org/10.31035/cg2021083.

[2] B. Whitehead, D. Andrews, A. Shah, G. Maidment, Assessing the environmental impact of data centres part 1: Background, energy use and metrics, Build Environ. 82 (2014) 151–159, http://dx.doi.org/10.1016/j.buildenv.2014.08.021.

[3] E. Masanet, A. Shehabi, N. Lei, S. Smith, J. Koomey, Recalibrating global data center energy-use estimates, Science 367 (6481) (2020) 984–986, http://dx.doi.org/10.1126/science.aba3758, URL: https://www.science.org/doi/abs/10.1126/science.aba3758.

[4] Y. Zhang, K. Shan, X. Li, H. Li, S. Wang, Research and technologies for next-generation high-temperature data centers – state-of-the-arts and future perspectives, Renew. Sustain. Energy Rev. 171 (2023) 112991, http://dx.doi.org/10.1016/j.rser.2022.112991, URL: https://www.sciencedirect.com/science/ article/pii/S1364032122008723.

[5] Q. Zhang, Z. Meng, X. Hong, Y. Zhan, J. Liu, J. Dong, T. Bai, J. Niu, M.J. Deen, A survey on data center cooling systems: Technology, power consumption modeling and control strategy optimization, J. Syst. Archit. 119 (2021) 102253, http://dx.doi.org/10.1016/j.sysarc.2021.102253, URL: https://www.sciencedirect.com/science/article/pii/S1383762121001739.

[6] S. Xu, H. Zhang, Z. Wang, Thermal management and energy consumption in air, liquid, and free cooling systems for data centers: A review, Energies 16 (3) (2023) http://dx.doi.org/10.3390/en16031279. URL: https://www.mdpi.com/1996-1073/16/3/1279.

[7] X. Xiong, Y. Fulpagare, P.S. Lee, A numerical investigation of fan wall cooling system for modular air-cooled data center, Build. Environ. 205 (2021) 108287, http://dx.doi.org/10.1016/j.buildenv.2021.108287.

[8] S.-Y. Lim, H.-J. Chang, Airflow management analysis to suppress data center hot spots, Build. Environ. 197 (2021) 107843, http://dx.doi.org/10.1016/j buildenv.2021.107843

[9] H. Moazamigoodarzi, S. Pal, D. Down, M. Esmalifalak, I.K. Puri, Performance of a rack mountable cooling unit in an IT server enclosure, Therm. Sci. Eng. Prog. 17 (2020) 100395.

[10] H. Moazamigoodarzi, P.J. Tsai, S. Pal, S. Ghosh, I.K. Puri, Influence of cooling architecture on data center power consumption, Energy 183 (2019) 525–535 http://dx.doi.org/10.1016/j.energy.2019.06.140, URL: https://www.sciencedirect.com/science/article/pii/S0360544219312721.

[11] R. Gupta, S. Asgari, H. Moazamigoodarzi, S. Pal, I.K. Puri, Cooling architecture selection for air-cooled data centers by minimizing exergy destruction, Energy 201 (2020) 117625, http://dx.doi.org/10.1016/j.energy.2020.117625, URL: https://www.sciencedirect.com/science/article/pii/S0360544220307325.

[12] H.-C. Chen, Y.-W. Bai, Improvement of a high-current-density power backplane design with a PID fan control cooling system on an enterprise server, IEEE Can. J. Electr. Comput. Eng. 44 (1) (2021) 1–9, http://dx.doi.org/10.1109/ICJECE.2020.3011357.

[13] G. Mohsenian, S. Khalili, M. Tradat. Y. Manaserh. S. Rangarajan, A. Desu, D. Thakur, K. Nemati, K. Ghose, B. Sammakia, A novel integrated fuzzy contro system toward automated local airflow management in data centers, Control Eng. Pract. 112 (2021) 104833, http://dx.doi.org/10.1016/j.conengprac.2021. 104833. URL: https://www.sciencedirect.com/science/article/pji/S0967066121001106

[14] N. Lazic, T. Lu, C. Boutilier, M. Ryu, E.J. Wong, B. Roy, G. Imwalle, Data center cooling using model-predictive control, in: Proceedings of the Thirty-Second Conference on Neural Information Processing Systems, NeurIPS-18, Montreal, OC. 2018, pp. 3818–3827, URL: https://papers. pips cc/paper/7638. data-center-cooling-using-model-predictive-control.

[15] Y.J. Choi, B.R. Park, J.Y. Hyun, J.W. Moon, Development of an adaptive artificial neural network model and optimal control algorithm for a data center cyber-physical system. Build. Environ. 210 (2022) 108704. http://dx.doi,org/10.1016/i,buildeny,2021.108704.

[16] Z. Yang, B. Niu, Y. Pan, Y. Chen, Multi-objective optimization of supply air jet enhancing airflow uniformity in data center using Taguchi/CRITIC/TOPSIS triple method, Build. Environ. 244 (2023) 110784, http://dx.doi.org/10.1016/j.buildeny.2023.110784.

[17] J. Wang, Y. Tian, Z. Qi, L. Zeng, P. Wang, S. Yoon, Sensor fault diagnosis and correction for data center cooling system using hybrid multi-label random forest and Bavesian inference, Build. Environ. 249 (2024) 111124, http://dx.doi.org/10.1016/i.buildeny.2023.111124.

[18] M. Ogawa, H. Endo, H. Fukuda, H. Kodama, T. Sugimoto, T. Horie, T. Maruyama, M. Kondo, Cooling control based on model predictive control using temperature information of IT equipment for modular data center utilizing fresh-air, in: 2013 13th International Conference on Control, Automation and Systems, ICCAS 2013, 2013, pp. 1815–1820, http://dx.doi.org/10.1109/ICCAS.2013.6704235.

[19] M. Ogawa, H. Endo, H. Fukuda, H. Kodama, T. Sugimoto, H. Soneda, M. Kondo, Cooling control restraining effects due to ICT equipment utilization of disturbance based on model predictive control for modular data center, in: 2014 IEEE Conference on Control Applications, CCA, 2014, pp. 183–190, http://dx.doi.org/10.1109/CCA.2014.6981349.

[20] M. Ogawa, H. Fukuda, H. Kodama, H. Endo, T. Sugimoto, T. Kasajima, M. Kondo, Development of a cooling control system for data centers utilizing indirect fresh air based on model predictive control, in: 2015 7th International Congress on Ultra Modern Telecommunications and Control Systems and Workshops. ICUMT. 2015, pp. 132–13Z. http://dx.doi org/10.1109/ICUMT.2015.7382417

[21] H. Endo, S. Suzuki, H. Kodama, T. Hatanaka, H. Fukuda, M. Fujita, Development of predictive control system using just-in-time modeling and enthalpyaware control in air conditioners for large-scale data center, in: 2018 18th International Conference on Control. Automation and Systems, ICCAS. 2018. pp. 1278–1283.

[22] Q. Wang, M. Song, Q. Fang, J. Wang, Thermal-aware flow field optimization for energy saving of data centers, in: 2018 Annual American Control Conference, ACC, 2018, pp. 3744–3749, http://dx.doi.org/10.23919/ACC.2018.8430975.

[23] Q. Fang, Q. Wang, M. Song, J. Wang, Control-oriented modelling and evaluation for the temperature distribution in data-centers, in: International Heat Transfer Conference Digital Library, Begel House Inc., 2018.

[24] Q. Fang, Q. Gong, J. Wang, Y. Wang, Optimization based resource and cooling management for a high performance computing data center, ISA Trans. 90 (2019) 202–212, http://dx.doi.org/10.1016/j.isatra.2018.12.038, URL: https://www.sciencedirect.com/science/article/pii/S0019057818305330.

[25] Q. Fang, J. Zhou, S. Wang, Y. Wang, Control-oriented modeling and optimization for the temperature and airflow management in an air-cooled data-center, Neural Comput. Appl. (2022) 1–16.

[26] Z. Li, H. Wang, Q. Fang, Y. Wang, A data-driven subspace predictive control method for air-cooled data center thermal modelling and optimization, J. Franklin Inst. 360 (5) (2023) 3657–3676, http://dx.doi.org/10.1016/j.jfranklin.2023.02.007, URL: https://www.sciencedirect.com/science/article/pii S001600322300087X

[27] M. Kheradmandi, D.G. Down, H. Moazamigoodarzi, Energy-efficient data-based zonal control of temperature for data centers, in: 2019 Tenth International Green and Sustainable Computing Conference, IGSC, 2019, pp. 1–7, http://dx.doi.org/10.1109/IGSC48788.2019.8957198.

[28] M. Kheradmandi, D.G. Down, Data driven fault tolerant thermal management of data centers, in: 2020 International Conference on Computing, Networking and Communications, ICNC, IEEE, 2020, pp. 736–740.

[29] K. Jiang, M. Kheradmandi, C. Hu. S. Pal. F. Yan, Data-driven fault tolerant predictive control for temperature regulation in data center with rack based cooling architecture, Mechatronics 79 (2021) 102633, http://dx.doi.org/10.1016/j.mechatronics.2021.102633, URL: https://www.sciencedirect.com science/article/pii/S0957415821001100.

[30] H. Moazamigoodarzi, S. Pal, S. Ghosh, I.K. Puri, Real-time temperature predictions in IT server enclosures, Int. J. Heat Mass Transfer 127 (2018) 890–900, http://dx.doi.org/10.1016/j.ijheatmasstransfer.2018.08.091, URL: https://www.sciencedirect.com/science/article/pii/S0017931018328977.

[31] W. Liu, X. Tong, J. Wang, C. Yue, Q. Zhang, Real-time temperature predictions via state-space model and parameters identification within rack-based cooling data centers, J. Build. Eng. 58 (2022) 105013, http://dx.doi.org/10.1016/j.jobe.2022.105013, URL: https://www.sciencedirect.com/science/article pii/S2352710222010233.

[32] J. Wang, X. Tong, C. Yue, W. Liu, Q. Zhang, L. Zeng, G. Huang, Real-time temperature distribution reconstruction via linear parameter-varying state-space model and Kalman filter in rack-based cooling data centers, Build. Environ. 242 (2023) 110601, http://dx.doi.org/10.1016/j.buildenv.2023.110601, URL: https://www.sciencedirect.com/science/article/pii/S0360132323006285.

[33] X. Tong, J. Wang, W. Liu, H.-A. SAMAH, Q. Zhang, L. Zhang, A time-varying state-space model for real-time temperature predictions in rack-based cooling data centers, Appl. Therm. Eng. 230 (2023) 120737, http://dx.doi.org/10.1016/j.applthermaleng.2023.120737, URL: https://www.sciencedirect. com/science/article/pii/S1359431123007664

[34] N.S. Rajput, Y. Miyamoto, T. Natsumeda, M. Yoshikawa, Rack thermal model prediction accuracy improvement by utilizing rack power consumption as modelling feature, in: 2019 18th IEEE Intersociety Conference on Thermal and Thermomechanical Phenomena in Electronic Systems, ITherm, 2019, pp. 693–697, http://dx.doi.org/10.1109/ITHERM.2019.8757274.

[35] S. Asgari, H. Moazamigoodarzi, P.J. Tsai, S. Pal, R. Zheng, G. Badawy, I.K. Puri, Hybrid surrogate model for online temperature and pressure predictions in data centers, Future Gener. Comput. Syst. 114 (2021) 531–547, http://dx.doi.org/10.1016/j.future.2020.08.029, URL: https://www.sciencedirect.com/ science/article/pii/S0167739X19329036.

[36] S. Asgari. S. MirhoseiniNeiad, H. Moazamigoodarzi. R. Gupta. R. Zheng, LK. Puri, A gray-box model for real-time transient temperature predictions in data centers, Appl. Therm. Eng. 185 (2021) 116319, http://dx.doi.org/10.1016/j.applthermaleng.2020.116319, URL: https://www.sciencedirect.com/science article/pii/S1359431120337984.

[37] J.C. Willems, P. Rapisarda, I. Markovsky, B.L. De Moor, A note on persistency of excitation, Systems Control Lett. 54 (4) (2005) 325–329, http: //dx.doi,org/10.1016/i.sysconle,2004.09.003. URI: https://linkinghub.elsevier.com/retrieve/pii/S0167691104001434.

[38] J. Berberich, J. Köhler, M.A. Müller, F. Allgöwer, Data-driven model predictive control with stability and robustness guarantees, JEEE Trans, Autom Control 66 (4) (2021) 1702–1717, http://dx.doi.org/10.1109/TAC.2020.3000182.

[39] Y. Lian, J. Shi, M. Koch, C.N. Jones, Adaptive robust data-driven building control via bilevel reformulation: An experimental result, IEEE Trans. Control Syst. Technol. 31 (6) (2023) 2420–2436, http://dx.doi.org/10.1109/TCST.2023.3259641.

[40] L. Huang, J. Coulson, J. Lygeros, F. Dörfler, Decentralized data-enabled predictive control for power system oscillation damping, IEEE Trans. Control Syst. Technol, 30 (3) (2022) 1065–1077, http://dx.doi,org/10.1109/TCST,2021.3088638.

[41] J. Coulson, J. Lygeros, F. Dörfler, Data-enabled predictive control: In the shallows of the deepc, in: 2019 18th European Control Conference, ECC, 2019, pp. 307–312, http://dx.doi.org/10.23919/ECC.2019.8795639.

[42] L. Ismail, H. Materwala, Computing server power modeling in a data center: Survey, taxonomy, and performance evaluation, ACM Comput. Surv. (3) (2021) 53.

[43] L. Parolini, B. Sinopoli, B.H. Krogh, Reducing data center energy consumption via coordinated cooling and load management, in: Proceedings of the 2008 Conference on Power Aware Computing and Systems, HotPower, Vol. 8, 2008, pp. 14–14.

[44] L. Parolini, B. Sinopoli, B.H. Krogh, Z. Wang, A cyber–physical systems approach to data center modeling and control for energy efficiency, Proc. IEEE 100 (1) (2012).254–268. http://dx.doi.org/10.1109/JPROC.2011.2161244.

[45] Z. Wang, C. Bash, N. Tolia, M. Marwah, X. Zhu, P. Ranganathan, Optimal Fan Speed Control for Thermal Management of Servers, in: International Electronic Packaging Technical Conference and Exhibition, ASME 2009 InterPACK Conference, Volume 2, 2009, pp. 709–719, http://dx.doi.org/10.1115 InterPACK2009-89074.

[46] H. Moazamigoodarzi, R. Gupta, S. Pal. P.J. Tsai. S. Ghosh, I.K. Puri, Modeling temperature distribution and power consumption in IT server enclosures with row-based cooling architectures, Appl. Energy 261 (2020) 114355, http://dx.doi.org/10.1016/j.apenergy.2019.114355, URL: https://www.sciencedirect com/science/article/pii/S0306261919320422

[47] T. Ashrae, 9.9 (2011) thermal guidelines for data processing environments–expanded data center classes and usage guidance, in: Whitepaper Prepared by ASHRAE Technical Committee. TC. Vol. 9. 2011.

[48] J. Berberich, F. Allgöwer, A trajectory-based framework for data-driven system analysis and control, in: 2020 European Control Conference, ECC, 2020, pp. 1365–1370, http://dx.doi.org/10.23919/ECC51009.2020.9143608.

[49] L. Xu, M.S. Turan, B. Guo, G. Ferrari-Trecate, A data-driven convex programming approach to worst-case robust tracking controller design, 2021, URL: https://api.semanticscholar.org/CorpusID:232035772.

[50] ASHRAE TC 9.9, Thermal Guidelines for Data Processing Environments: Expanded Data Center Classes and Usage Guidance, Technical Report, American Society of Heating, Refrigerating and Air-Conditioning Engineers, Inc., Atlanta, 2011, Technical Committee Report.