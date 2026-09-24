![](images/89741d38f8ef0b7b7f8e8e215ed7e4d8cdd3abb5caade58fa56138e7203352be.jpg)

# Joint optimization of cooling parameters and workload distributions based on model predictive control for rack-based data centers

Jiaqiang Wang <sup>a,b,c</sup>, Weiqi Deng <sup>a,b,c</sup>, Chang Yue <sup>a,\*</sup>, Wen Su <sup>a</sup>, Xuelian Bai <sup>d</sup>

<sup>a</sup> School of Energy Science and Engineering, Central South University, Hunan, 410083, Changsha, PR China

<sup>b</sup> Anhui Province Key Laboratory of Intelligent Building & Building Energy Saving, Anhui Jianzhu University, Anhui, 230022, Hefei, PR China

<sup>c</sup> Hunan Provincial Key Laboratory of Low Carbon Healthy Buildings, Hunan, 410083, Changsha, PR China

<sup>d</sup> School of Civil Engineering, Chongqing University, 400045, Chongqing, PR China

## A R T I C L E I N F O

Keywords: Rack-based cooling data center State-space model Cooling parameters Workload distributions Joint optimization

## A B S T R A C T

To satisfy the thermal environment requirements, approximately 40 % of energy consumption is used for cooling in data centers. The efficient cooling system and workload management can improve heat dissipation, thereby reducing energy consumption. This paper introduced a novel joint optimization strategy (JOS) for cooling parameters and workload distributions designed for rack-based cooling data centers. The primary objective is to improve overall energy efficiency while ensuring the safe operation of the data center. The proposed strategy considers the thermal interaction between information technology (IT) equipment and cooling devices, as well as the heterogeneity among different servers, achieving granular optimization of cooling parameters and workload distributions. The impacts of different cooling parameters optimization on the energy management and thermal management performance were analyzed. The energy-saving potential and temperature field control effect considering server heterogeneity for the proposed JOS were investigated. The results show that, compared to univariate optimization, simultaneous optimization of supplied cold air temperature and airflow rate can achieve at least 4.7 % in energy-saving. Furthermore, compared with independent control of the cooling system, the proposed JOS effectively addresses server overcooling, achieving a more uniform temperature distribution and further reducing the cooling energy consumption by 5 %. It is noteworthy that the strategy demonstrates high computational efficiency while ensuring energy efficiency.

## Nomenclature

(continued on next page)

## 1. Introduction

(continued )

<table><tr><td>SQP</td><td>sequential quadratic programming</td></tr><tr><td> $P_{\text{IT}}$ </td><td>IT workload</td></tr><tr><td> $Q_{\text{RCU}}$ </td><td>supplied airflow rate of RCU</td></tr><tr><td> $T_{\text{RCU}}$ </td><td>supplied cold air temperature of RCU</td></tr><tr><td> $\rho_a$ </td><td>air density</td></tr><tr><td> $c_{p,a}$ </td><td>specific heat capacity of air</td></tr><tr><td>X</td><td>thermal mass of the server</td></tr><tr><td> $V_I$ </td><td>volume of the inlet zone</td></tr><tr><td> $V_O$ </td><td>volume of the outlet zone</td></tr><tr><td> $P_{S,i}$ </td><td>power consumption of the ith server</td></tr><tr><td> $T_{I,i}$ </td><td>temperature of ith inlet zone</td></tr><tr><td> $T_{S,i}$ </td><td>temperature of ith exhaust zone</td></tr><tr><td> $T_{O,i}$ </td><td>temperature of ith outlet zone</td></tr><tr><td> $Q_{S,i}$ </td><td>airflow rate of the ith server</td></tr><tr><td> $Q_{L,T}$ </td><td>total leakage airflow rate</td></tr><tr><td> $Q_{OC,i}$ </td><td>airflow rate flowing out of the ith inlet zone</td></tr><tr><td> $Q_{OH,i}$ </td><td>airflow rate flowing out of the ith outlet zone</td></tr><tr><td> $D_i$ </td><td>airflow distribution coefficient from the RCU into the ith server</td></tr><tr><td> $E_i$ </td><td>airflow distribution coefficient of hot air re-circulation</td></tr><tr><td> $F_i$ </td><td>airflow distribution coefficient of cold air bypass</td></tr><tr><td>A, B, C, D</td><td>state-space coefficient matrices</td></tr><tr><td>x</td><td>state vector</td></tr><tr><td>u</td><td>input vector</td></tr><tr><td>y</td><td>output vector</td></tr><tr><td> $T_S$ </td><td>sampling period</td></tr><tr><td> $P_{\text{idle}}$ </td><td>idle server power consumption</td></tr><tr><td> $P_{\text{idle}} + P_{\text{busy}}$ </td><td>fully utilized server power consumption</td></tr><tr><td> $u_i$ </td><td>server utilization</td></tr><tr><td> $P_{\text{cooling}}$ </td><td>cooling system power consumption</td></tr><tr><td> $P_{c,\text{source}}$ </td><td>cold source energy consumption</td></tr><tr><td> $P_{c,\text{fan}}$ </td><td>fan power consumption</td></tr><tr><td> $T_{c,\text{in}}$ </td><td>return air temperature of the RCU</td></tr><tr><td> $T_{c,\text{out}}$ </td><td>supplied cold air temperature of the RCU</td></tr><tr><td> $\alpha_0$ </td><td>primary coefficient of fan</td></tr><tr><td> $\alpha_1$ </td><td>quadratic coefficient of fan</td></tr><tr><td> $\alpha_2$ </td><td>cubic coefficients of fan</td></tr><tr><td> $N_p$ </td><td>prediction horizon</td></tr><tr><td> $N_c$ </td><td>control horizon</td></tr><tr><td> $\rho_i$ </td><td>utilization of the ith server</td></tr><tr><td> $\overline{u}$ </td><td>average server CPU workload rate</td></tr><tr><td>N</td><td>number of servers</td></tr><tr><td> $\underline{T}_{\text{RCU}}$ </td><td>lower limit of supplied cold air temperature</td></tr><tr><td> $\overline{T}_{\text{RCU}}$ </td><td>upper limit of supplied cold air temperature</td></tr><tr><td> $\underline{Q}_{\text{RCU}}$ </td><td>lower limit of supplied airflow rate</td></tr><tr><td> $\overline{Q}_{\text{RCU}}$ </td><td>upper limit of supplied airflow rate</td></tr><tr><td> $\overline{T}_1$ </td><td>maximum temperature of inlet zones</td></tr><tr><td> $\overline{T}_0$ </td><td>maximum temperature of outlet zones</td></tr><tr><td>Y, Φ, Γ</td><td>weight matrix</td></tr><tr><td> $U_{\text{min}}$ </td><td>lower limits of the inputs</td></tr><tr><td> $U_{\text{max}}$ </td><td>upper limits of the inputs</td></tr><tr><td> $\Delta U_{\text{min}}$ </td><td>lower limits of the input variation</td></tr><tr><td> $\Delta U_{\text{max}}$ </td><td>upper limits of the input variation</td></tr><tr><td> $T_{\text{Redline}}$ </td><td>upper limits of server temperatures</td></tr><tr><td> $||\tilde{P}_{\text{cooling}}(h|k)||_1$ </td><td>cooling system energy consumption of prediction horizon</td></tr><tr><td> $||\Delta \tilde{U}(h|k)||_2$ </td><td>input fluctuation of prediction horizon</td></tr><tr><td> $||\tilde{\Psi}(h|k)||_2$ </td><td>penalty of exceeding the threshold temperature of prediction horizon</td></tr></table>

With the rapid development of 5G, industrial internet, cloud computing, artificial intelligence, and other applications [1,2], data centers, as the information backbone of the digital world, are rapidly increasing in size and energy consumption $[ 3 , 4 ]$ . It is estimated that data centers contribute to approximately 1 % of global electricity consumption [5]. To address the high heat dissipation demands of data centers, the cooling system necessitates a continuous cooling supply $^ { 2 4 / 7 }$ throughout the year, to maintain a safe therma environment for electronic devices. Its energy consumption accounts for 30%–40 % of the total energy consumption of data centers [6, 7]. The low efficiency of cooling systems and server overcooling are the main reasons for energy waste in data centers [8,9]. Therefore, the research of thermal control system is important to improve energy efficiency of cooling systems and ensure the safe operation of data centers.

Proportion integration differentiation (PID) control is a widely employed method for regulating cooling parameters in data centers.

It utilizes feedback derived from the disparity between the system’s measured value and the setpoint to compute system control [10]. However, PID control can only adjust the setpoint temperature and does not effectively harness the energy-saving potential of the system based on its current state. Model predictive control (MPC) is another method for data center control [11–13]. MPC can predict the state of the system for a future period based on the current state of the system at each sampling cycle. It computes the optimal input sequence to minimize the operational cost of system over the upcoming period and conveys the initial control signal to the contro system, ensuring that the current operating state of the system is indeed optimal [14,15]. It is noteworthy that the ability to predict the system’s future state and employ cost as an objective function is lacking in PID controllers.

However, MPC relies on dynamic predictive modeling of the system. During the optimization process, the thermal model is crucia for real-time prediction of the data center temperature field and assessing the impact of future inputs. The accuracy and timeliness of the model are critical for ensuring the efficient and real-time operation of MPC [16]. Computational fluid dynamics (CFD) simulation is a traditional approach for predicting the data center temperature field [17,18]. Numerical calculations through CFD simulations can yield extremely accurate and fine-grained temperature, flow, and pressure fields $[ 1 9 , 2 0 ]$ . However, due to the complexity of airflow within a data center, CFD models require significant computational resources and processing time, making them unsuitable for therma management or designing real-time control algorithms [21–23]. In the control field, state-space structure is a common method for developing control oriented thermal models [15]. Temperature prediction utilizing the state-space model, constructed by the zona modeling method, enables the rapid and accurate acquisition of the temperature field information within the data center [24.25] Particularly, the zonal modeling approach involves the airflow and heat transfer mechanisms of the data room. The state-space co efficient matrix constructed by this method can map the relationship between the thermal behavior and power consumption model of the data center with clear physical principles. Therefore, the proposed method offers better industrial interpretability compared to machine learning methods [26]. In addition, the dataset required for state-space model parameter identification is much smaller than that of the machine learning approach [27]. Therefore, predictive control based on this model is a promising method for optimizin data center system parameters.

Optimizing cooling parameters to improve cooling efficiency is a common method to reduce energy consumption in data centers. At the same time, to keep IT equipment operate in a safe thermal environment at all times, the supplied cold air temperature and airflow rate need to be adjusted to ensure that the room temperature meets the thermal requirements of ASHARE. However, due to the intricate airflow organization in the data center. hot air often enters the cold aisle from the hot aisle, causing hot air recirculation and resulting in an increase in server inlet temperature. In order to meet the cooling requirements of the data center, it is common to set the supplied cold air temperature excessively low or the supplied airflow rate overly high, resulting in a substantial waste of energy [28]. By synergistically optimizing data center cooling parameters, particularly by enhancing supplied cold air temperature through opti mized airflow pattern management, it is possible to improve the efficiency of the cooling system and consequently reduce overall energy consumption in the data center [29,30]. Fang et al. [31] proposed an optimization method for airflow management in air-cooled data center temperature control. By simultaneously optimizing the supplied cold air temperature and airflow rate, the strategy achieved at least a 10 % energy-saving rate in the cooling system compared to optimizing only the supplied cold air tem perature or airflow rate. Hence, the implementation of multivariate cooperative control in the cooling system proves to be an effective approach for improving system efficiency.

The factors that affect the cooling efficiency and temperature field of data centers include not only cooling parameters, but also server workload distributions. Therefore, optimizing workload distributions is another common method to improve cooling efficiency. This optimization mitigates the impact of hot air re-circulation and cold air bypass within the data center by strategically allocating more workloads to servers with efficient cooling capacity. This approach is often referred to as thermally aware workload assignment [32]. Zhu et al. [33] proposed a model predictive control for optimizing the on/off settings of servers and workload distributions, achieving significant savings in server energy consumption alongside high temperature control efficiency. Song et al. [34] considered a data center rack as an information-physical system and introduced a rack-level thermally-aware workload provisioning problem to reduce ambient temperatures by designing workload distributions. Simulation results demonstrate that the proposed method achieves a temperature reduction ranging from $0 . 5 ^ { \circ } \mathrm { C } \mathrm { t o } 1 ^ { \circ } \mathrm { C } .$ . Therefore, workload distributions optimization is crucial for energy conservation and temperature field control in data centers.

By focusing solely on optimizing cooling parameters and workload distributions. we may not fully exploit on the energy-saving potential of the cooling system. This is because the thermal behavior of servers significantly impacts the cooling system, where phenomena such as hot air re-circulation and cold air bypass in servers affect cooling parameters and result in energy wastage [35,36]. Therefore, considering the thermal correlation between IT equipment and cooling units, some researchers advocate for joint opti mization control of cooling parameters and workload distributions, as shown in Table 1. From the table, it can be seen that optimizing workload distributions can allocate more workloads to racks with higher cooling efficiency compared to independent control of the cooling system, thereby reducing hot air recirculation between racks and improving cooling efficiency. This approach has demon strated an energy-saving rate of over 10 %. However, most current research focuses on the workload distributions of server racks, assuming an even distribution of workload within each server, thereby neglecting server heterogeneity. In the optimization process, if the differences in cooling requirements between servers at different workload levels and the differences in cooling efficiency of servers at different locations in the data center are considered, greater energy savings may be achieved in the cooling system. However, considering that each rack may contain dozens of servers, if MPC is employed to optimize the workload distributions for each server, it would involve optimizing $n \times N _ { c }$ variables in each sampling period (where n is the number of servers and $N _ { c }$ is the control horizon). While theoretically possible to obtain an optimal solution, solving the problem using this approach is time-consuming and may encounter challenges leading to failure. To simplify this issue, some studies assume that servers only operate under no-load and full-load conditions [37,38]. While this assumption achieves certain energy-saving effects, the limited granularity of workload optimization results in an inadequate exploration of the energy-saving potential of the cooling system. If the assumption is relaxed, allowing servers to operate at any state between empty and full load, the computational complexity significantly increases. Therefore, balancing computational efficiency and energy-saving effects in joint optimization is crucial for enhancing the efficiency of cooling systems.

T<sub>a</sub>bl<sub>e</sub> 1  
A review of research on j oint optimization of IT equipment and cooling systems in data centers.

<table><tr><td rowspan="2">Ref.</td><td colspan="2">Joint optimization</td><td rowspan="2">Thermal model</td><td rowspan="2">Cooling parameters</td><td colspan="2">IT workload distributions</td></tr><tr><td>steady-state optimization</td><td>dynamic optimization</td><td>Rack heterogeneity</td><td>Server heterogeneity</td></tr><tr><td>[8]</td><td>✓</td><td></td><td>NARX</td><td>Water temperature and airflow rate</td><td></td><td>✓</td></tr><tr><td>[26]</td><td></td><td>✓</td><td>Subspace identification methods</td><td>Supplied cold air temperature</td><td>✓</td><td></td></tr><tr><td>[31]</td><td>✓</td><td></td><td>Neural network grey box model</td><td>Supplied cold air temperature and airflow rate</td><td>✓</td><td></td></tr><tr><td>[28,35]</td><td></td><td>✓</td><td>State-space model</td><td>Supplied cold air temperature</td><td>✓</td><td></td></tr><tr><td>[37]</td><td></td><td>✓</td><td>NARX</td><td>Supplied airflow rate</td><td></td><td>✓</td></tr><tr><td>[29,30,39]</td><td>✓</td><td></td><td>Heat balance equation</td><td>Water temperature, water flow rate and airflow rate</td><td></td><td>✓</td></tr><tr><td>[40]</td><td>✓</td><td>✓</td><td>Heat balance equation</td><td>Supplied cold air temperature</td><td>✓</td><td></td></tr><tr><td>Our work</td><td>✓</td><td>✓</td><td>State-space model</td><td>Supplied cold air temperature and airflow rate</td><td></td><td>✓</td></tr></table>

To this end, a novel low-complexity joint optimization strategy for cooling parameters and workload distributions based on a linear parameter-varying state-space model was proposed. This strategy takes into account the heterogeneity among servers to achieve granular optimization of cooling parameters and workload distributions. It enhances optimization efficiency while ensuring significant energy savings and maintaining thermal safety in the data center. In order to explore the role of different cooling parameters and workload distributions in energy-saving control and thermal management of data centers, the single-variable control and multivariate control of cooling systems were studied. It could clarify the energy-saving potential and temperature field control effect of optimizing supplied cold air temperature and airflow rate. In addition, the performances of the proposed joint optimization strategy and the independent control of the cooling system were also compared to further explore the energy-saving potential considering server heterogeneity. To demonstrate the superiority of the proposed strategy, we compared its energy-saving performance and computa tional efficiency with the MPC strategy that optimizes cooling parameters and workload distributions simultaneously. To sum up, the main contributions of the current work are as follows:

● A novel low-complexity joint optimization algorithm for cooling parameters and workload distributions based on MPC was pro posed. It considered the heterogeneity among servers, aiming to optimize cooling parameters and workload distributions with granularity.

● A data-driven joint optimization control framework was introduced to minimize cooling system energy consumption while ensuring the safe operation of data centers.

● The impact of univariate and multivariate optimization control of cooling system on energy-saving potential and temperature field control effects were investigated.

● The energy management and thermal management performance, along with the computational efficiency of the proposed joint optimization strategy were comprehensively evaluated.

The remainder of the study is organized as follows. Section 2 outlines the data center dynamic thermal modeling process and the joint optimization control design process. Section 3 evaluates the impact of different optimal control of cooling parameters on the energy management and thermal management performance through a case study. Section 4 compares the control performance of joint optimal control with that of independent optimal control of the cooling system. Finally, Section 5 gives the conclusion of this study

![](images/4d99424100744dd97d686e4c85d907a65ab695b99c0acc4dbc038094217f5a80.jpg)  
Fig. 1. Difference between independent and joint optimization: (a) Cooling system independent optimization. (b) Cooling parameters and workload distributions joint optimization of the proposed method.

## 2. Methodology

## 2.1. Cooling parameters and workload distributions joint optimization strategy

For the joint optimization strategy, the goal is to minimize the energy consumption of the cooling system while ensuring the safe operation of the data center through system parameter optimization. Two optimization control strategies are considered, as shown in Fig. 1. The cooling system independent control strategy does not optimize the workload distributions and considers that the workload is evenly distributed to all servers. In contrast, the proposed joint optimization strategy first optimize the workload distributions based on steady-state conditions. Then the supplied cold air temperature and airflow rate are optimized based on the MPC. Compared with the MPC strategy that optimizes both cooling parameters and workload distributions simultaneously, the proposed method not only greatly improves computational efficiency but also ensures the solvability of the problem.

## 2.2. Data center joint optimization framework

In order to address energy conservation control and thermal management issues in data centers, a control framework based on a state-space model was proposed to optimize both supplied cold air temperature and airflow rate, along with the workload distribu tions. The operation process of the control framework is shown in Fig. 2. Firstly, the data collection platform obtains the current state of the system, which is the temperature distribution information of the data center. The optimization control algorithm includes two parts: prediction and optimization. The state space model can predict the future state of the system based on the current state and evaluate the impact of future inputs on the system state. Then, with the help of optimization algorithms, a control sequence that can minimize the energy consumption of the cooling system while satisfying temperature constraints is found in the prediction horizon. The first control signal is applied to the cooling control system to make the current state of the system the actual optimal. In the next sampling cycle, repeat this optimization process and use feedback values to update the state information in the process model.

## 2.3. Data center dynamic thermal model

Fig. 3(a) depicts a schematic diagram of a rack-based cooling data center, consisting of a rack-based cooling unit (RCU) mounted at the bottom and some servers. The RCU can be a heat pipe or an air handling unit. If the RCU is a heat pipe cooling unit, its principle is shown in Fig. 3(b): cold air enters the cold aisle under the action of the cooling system fan, then enters the server to take away the heat: after that, the heated air returns to the cooling unit through the hot aisle to be cooled again by heat exchange with liquid refrigerant. To calculate the dynamic temperature field of the data center, a low complexity zonal model is employed. It is an intermediate method to calculate temperature fields between the CFD model and the lumped-parameter method, which is not as time-consuming as CFD and avoids the problem of over-simplification that leads to low accuracy of predicted temperature. With the zonal model, the volume in front of and behind each server, as well as the server itself, is divided into different zones (see Fig. 4).

Considering the temperature constraints of the server inlet and outlet, we define three zones for each server: (1) Inlet zones, (2) Server exhaust zones, and (3) Outlet zones. Based on the airflow mass balance relationship within each zone, airflow distribution information can be obtained. The detailed calculation process for airflow distribution can be referenced in our previous work [24,25].

![](images/ef94d2150ebdd1982746f1ec0b8923cf954ac23adce94317d09f9ec3e022528a.jpg)  
Fig. 2. Data center control framework based on linear time-varying state-space model.

![](images/e4dd12aa15f910cc86b8193e9c66f000972b090c1f68c73024852e9fdea15223.jpg)  
Fig. 3. Schematic of a rack-based cooling data center.

![](images/b4196717e1e7d4b316e720c4d629709fc803c4f13b0a3f93442b2216c2de0c9d.jpg)  
Fig. 4. Schematic of airflow distribution in the temperature calculation zones.

After obtaining the airflow distribution information of all zones, the dynamic temperature field of the data center can be predicted in real-time based on the energy balance relationship of each zone. Specifically, the energy balance equations for the server exhaust zones are expressed as:

$$
X \frac {d T _ {S , i}}{d t} = Q _ {S, i} \rho_ {a} c _ {p, a} \left(T _ {I, i} - T _ {S, i}\right) + P _ {S, i}\tag{1}
$$

where $\rho _ { a }$ denotes the air density, $c _ { p , a }$ denotes the specific heat capacity of air, X denotes the thermal mass of the server, $P _ { S , i }$ denotes the power consumption of the server, $T _ { I , i }$ and $T _ { S , i }$ denote the temperatures of the inlet and exhaust zones of the corresponding server, respectively, and $Q _ { S , i }$ denotes the airflow rate of the corresponding server

The energy balance equations for the inlet zones are:

$$
\begin{array}{r l} \rho_ {a} c _ {p, a} V _ {\mathrm{I}} \frac {d T _ {I , i}}{d t} = & D _ {i} Q _ {\mathrm{RCU}} \rho_ {a} c _ {p, a} T _ {\mathrm{RCU}} + E _ {i} \left| Q _ {L, \mathrm{T}} \right| \rho_ {a} c _ {p, a} T _ {O, i} - F _ {i} \left| Q _ {L, \mathrm{T}} \right| \rho_ {a} c _ {p, a} T _ {I, i} \\ & + Q _ {O C, i - 1} \rho_ {a} c _ {p, a} T _ {I, i - 1} - Q _ {O C, i} \rho_ {a} c _ {p, a} T _ {I, i} - Q _ {S, i} \rho_ {a} c _ {p, a} T _ {I, i} \end{array}\tag{2}
$$

The energy balance equations for the outlet zones are:

$$
\begin{array}{c} \rho_ {a} c _ {p, a} V _ {O} \frac {d T _ {O , i}}{d t} = F _ {i} \big | Q _ {L, \mathrm{T}} \big | \rho_ {a} c _ {p, a} T _ {I, i} - E _ {i} \big | Q _ {L, \mathrm{T}} \big | \rho_ {a} c _ {p, a} T _ {O, i} - Q _ {O H, i} \rho_ {a} c _ {p, a} T _ {O, i} \\ + Q _ {O H, i + 1} \rho_ {a} c _ {p, a} T _ {O, i + 1} + Q _ {S, i} \rho_ {a} c _ {p, a} T _ {S, i} \end{array}\tag{3}
$$

where $V _ { \mathrm { I } }$ and $V _ { 0 }$ denote the volumes of the inlet and outlet zones, respectively. T denotes the supplied cold air temperature of the RCU, $T _ { O , i }$ denotes the temperature of the outlet zone, Q denotes the supplied airflow rate of the RCU, $Q _ { L , \mathrm { T } }$ denotes the total leakage airflow rate, $Q _ { O C , i }$ and $Q _ { O H , i }$ denote the airflow rate of the air flowing out of the inlet and outlet zones of the corresponding servers, respectively. $D _ { i }$ denotes the distribution coefficient of the supplied airflow from the RCU into the corresponding server, and $E _ { i }$ and $F _ { i }$ denote the distribution coefficient of the leakage airflow re-circulation and bypass inside each gap, respectively.

The above energy balance equation can be expressed using state space as follows:

$$
\begin{array}{l} \dot {x} = A x + B u \\ y = C x + D u \end{array}\tag{4}
$$

where $A ,$ B, and C denote the coefficient matrices. x denotes the state vector for the temperature of each zone, and u denotes the input vector including the supplied cold air temperature, the supplied airflow rate, and the workload of each server. y is the output vector including the temperatures of all zones. The equation is discretized with sampling period $T _ { S }$ as follows:

$$
\begin{array}{l} x _ {t + 1} = A _ {t} x _ {t} + B _ {t} u _ {t} \\ y _ {t} = C x _ {t} + D u _ {t} \end{array}\tag{5}
$$

The details of the input, output, and state variables are shown in Table 2. The matrix $\boldsymbol { C } \in \mathbb { R } ^ { 3 0 \times 3 0 }$ is a unitary matrix so that the output vector $\boldsymbol { y } \in \mathrm { R } ^ { 3 0 \times 1 }$ is the same as the state vector $\boldsymbol { x } \in \mathrm { R } ^ { 3 0 \times 1 }$ and D is a zero matrix. The matrices $\boldsymbol { A } \in \mathrm { R } ^ { 3 0 \times 3 0 }$ (state matrix) and $B \in$ $\boldsymbol { \mathrm { R } } ^ { 3 0 \times 1 2 }$ (input matrix) can be obtained from the heat balance equation for each zone. Both matrices are time-varying, so the equation is a time-varying state-space equation. We have validated the predictive performance of the time-varying state-space model in previous studies, and the results show that the proposed state-space model has good prediction accuracy and timeliness [41]. Therefore, the model proves well-suited for applications in model predictive control within the rack-based cooling data centers.

## 2.4. Power models

Server power consumption consists of CPU power and fan power, with fan power accounting for a small percentage. Therefore, it can be assumed that server power consumption is solely a function of its CPU utilization [39]. The server power consumption mode can be expressed as follows:

$$
P _ {S, i} (t) = P _ {\text { idle }} + P _ {\text { busy }} u _ {i} (t)\tag{6}
$$

where $P _ { \mathrm { i d l e } }$ is the power consumption when the server is idle, $P _ { \mathrm { i d l e } } + P _ { \mathrm { b u s y } }$ is the power consumption when the server is fully utilized, and u denotes the server utilization. In this study, it is assumed that the servers are isomorphic, so $P _ { \mathrm { i d l e } }$ and $P _ { \mathrm { b u s y } }$ are always constant.

Data center cooling system power consumption $P _ { \mathrm { c o o l i n g } }$ is determined by cold source energy consumption $P _ { \mathrm { c , s o u r c e } }$ and fan power consumption $P _ { \mathrm { c , f a n } } \mathrm { : }$

$$
P _ {\text { cooling }} (t) = P _ {\text { c,source }} (t) + P _ {\text { c,fan }} (t)\tag{7}
$$

The cold source power consumption $P _ { \mathrm { c , s o u r c e } }$ is given by the following equation [26]:

$$
P _ {\mathrm{c}, \text { source }} (t) = \frac {\rho_ {a} Q _ {\mathrm{RCU}} (t) c _ {p , a} \left(T _ {\mathrm{c,in}} (t) - T _ {\mathrm{c,out}} (t)\right)}{\operatorname{COP} \left(T _ {\mathrm{c,out}} (t)\right)}\tag{8}
$$

Where $Q _ { \mathrm { { R C U } } } \left( t \right)$ denotes the supply air flow of the RCU at moment t, T (t) denotes the return air temperature of the RCU at moment t, considered equal to the average of the temperatures of the 10 hot zones in this study. $T _ { \mathrm { c , o u t } } ( t )$ denotes the supplied cold air temperature of the RCU at moment t, which is equal to $T _ { \mathrm { { R C U } } } ( t )$ . COP denotes the coefficient of performance of the RCU, which can be expressed as a function of $T _ { \mathrm { c , o u t } } \left( t \right) \left[ 3 1 \right]$ ]:

$$
\mathrm{COP} \left(T _ {\mathrm{c}, \text { out }} (t)\right) = 0. 0 0 6 8 T _ {\mathrm{c}, \text { out }} ^ {2} (t) + 0. 0 0 0 8 T _ {\mathrm{c}, \text { out }} (t) + 0. 4 5 8\tag{9}
$$

The fan power consumption model can be represented as follows:

$$
P _ {\mathrm{c}, \text { fan }} (t) = \alpha_ {0} Q _ {\mathrm{RCU}} (t) + \alpha_ {1} Q _ {\mathrm{RCU}} ^ {2} (t) + \alpha_ {2} Q _ {\mathrm{RCU}} ^ {3} (t)\tag{10}
$$

where $\alpha _ { 0 } \mathrm { ~ , ~ } \alpha _ { 1 } \mathrm { ~ , ~ } \alpha _ { 2 }$ are constants.

## Table 2

States, inputs, and outputs used in the detailed state-space model.

<table><tr><td>State (x)</td><td>Input (u)</td><td>Output (y)</td></tr><tr><td> $x = [T_{I,i}, T_{S,i}, T_{O,i}]^{T}$ </td><td> $u = [T_{\text{RCU}}, Q_{\text{RCU}}, P_{S,i}]^{T}$ </td><td> $y = [T_{I,i}, T_{S,i}, T_{O,i}]^{T}$ </td></tr><tr><td> $T_{I,i}$ : server inlet temperature</td><td> $T_{\text{RCU}}$ : the supplied cold air temperature</td><td> $T_{I,i}$ : server inlet temperature</td></tr><tr><td> $T_{S,i}$ : server exhaust temperature</td><td> $Q_{\text{RCU}}$ : the supplied airflow rate</td><td> $T_{S,i}$ : server exhaust temperature</td></tr><tr><td> $T_{O,i}$ : server outlet temperature</td><td> $P_{S,i}$ : server workload</td><td> $T_{O,i}$ : server outlet temperature</td></tr></table>

## 2.5. Optimization of workload distributions based on steady-state conditions

The first step of the proposed joint optimization strategy involves the optimization of server workload distributions under steadystate conditions. In the optimization problem for server workload distributions, the decision variables include the supplied cold air temperature, the supplied airflow rate, and the utilization of 10 servers. The objective is to find the optimal values of the decision variables at steady state, aiming to minimize the energy consumption of the cooling system. The formulated optimization problem can be expressed as follows:

$$
\begin{array}{c} \text { minimize } \\ \overline {{\rho}}, T _ {\text { RCU }}, Q _ {\text { RCU }} \end{array} P _ {\text { cooling }}\tag{11}
$$

$$
\text { subject   to }: 0 \leq \rho_ {i} \leq 1, i = 1, 2..., N\tag{12}
$$

$$
\sum_ {i = 1} ^ {N} \rho_ {i} = N \cdot \overline {{u}}\tag{13}
$$

$$
\underline {{T}} _ {\mathrm{RCU}} \leq T _ {\mathrm{RCU}} \leq \overline {{T}} _ {\mathrm{RCU}}\tag{14}
$$

$$
\underline {{Q}} _ {\mathrm{RCU}} \leq Q _ {\mathrm{RCU}} \leq \overline {{Q}} _ {\mathrm{RCU}}\tag{15}
$$

$$
\max \left(T _ {I, i}\right) \leq \overline {{T}} _ {\mathrm{I}}, i = 1, 2 \dots , N\tag{16}
$$

$$
\max \left(T _ {O, i}\right) \leq \overline {{T}} _ {0}, i = 1, 2 \dots , N\tag{17}
$$

where $\rho _ { i }$ denotes the utilization of the ith server. and the power consumption of each server can be calculated from $\operatorname { E q } .$ (6), The constraint Eq. (12) requires that the utilization of each server is between 0 and 1. u is the average server CPU workload rate. N is the number of servers. Constraint (13) requires that the sum of all server workloads rate is equal to the total workload. Constraints (14) and (15) are cooling system input constraints and constraints (16) and (17) are server inlet and outlet temperature constraints.

The optimization problem is solved by the Interior-point algorithm, which is implemented by the function fmincon in the opti mization toolbox of the Matlab R2018a software.

## 2.6. Optimization of cooling parameters based on MPC

The second step of the proposed joint optimization strategy involves the dynamic adjustment of cooling parameters based on MPC. The optimization problem defined in this study is to find the optimal supplied cold air temperature T and supplied airflow rate Q of the RCU to minimize the energy consumption of the cooling system while keeping the temperature of the server’s inlet and outlet zones within the safety threshold. The cost function and constraints are as follows:

$$
\min _ {\widehat {U} (k)} \sum_ {h = k} ^ {k + N _ {p} - 1} \left(\Upsilon \big \| \widehat {P} _ {\text { cooling }} (h | k) \big \| _ {1} + \Phi \| \Delta \widehat {U} (h | k) \| _ {2} + \Gamma \| \widehat {\Psi} (h | k) \| _ {2}\right)\tag{18}
$$

subject to. $\boldsymbol { x } _ { t + 1 } = \boldsymbol { A } _ { t } \boldsymbol { x } _ { t } + \boldsymbol { B } _ { t } \boldsymbol { u } _ { t }$

(19)

$$
U _ {\min} \leq \widehat {U} (h | k) \leq U _ {\max}\tag{20}
$$

$$
\Delta U _ {\min} \leq \Delta \widehat {U} (h | k) \leq \Delta U _ {\max}\tag{21}
$$

$$
\widehat {T} _ {\text { Server }} (h | k) \leq T _ {\text { Redline }} + \widehat {\Psi} (h)\tag{22}
$$

where $U = [ T _ { \mathrm { R C U } } , Q _ { \mathrm { R C U } } ] ^ { \mathrm { T } }$ denotes the vector of the supplied cold air temperature and airflow rate, $N _ { P }$ denotes the prediction horizon. ${ \mathrm { \Delta r } } ,$ Φ, Γ denote the weight matrix. $\left| \left| \widehat { P } _ { \mathrm { c o o l i n g } } ( h | k ) \right| \right|$ denotes the energy consumption of the cooling system, calculated using the 1-norm to sum the energy consumption over the prediction horizon. $\| \Delta \widehat { U } ( h | k ) \| _ { 2 }$ denotes the penalty for input fluctuation, where $\Delta \widehat { U } ( h | k ) =$ $\widehat { U } ( h | k ) - \widehat { U } ( h - 1 | k )$ . Here, the 2-norm is used to sum the squares of the changes in cooling system parameters over the prediction horizon, ensuring the cost of system input fluctuations is positive. $\| \widehat \Psi ( h | k ) \| _ { 2 }$ denotes the penalty for server inlet and outlet temper atures exceeding the threshold temperature. In this case, the 2-norm is used to sum the squares of these penalties over the prediction horizon. Additionally, the use of relaxation variables to transform hard constraints into soft constraints ensures the feasibility of solving the optimization problem.

The constraint Eq. (20) imposes limits on the system inputs, $U _ { \mathrm { m i n } }$ and $U _ { \mathrm { m a x } }$ are the lower and upper limits of the inputs, respectively. Eq. (21) restricts the amount of variation in the system inputs, where $\Delta U _ { \mathrm { m i n } }$ and $\Delta U _ { \mathrm { m a x } }$ are the lower and upper limits of the input variation, respectively. Eq. (22) ensures the safe operation of the server, where $\widehat { T } _ { \mathrm { S e r v e r } } ( h | k ) = \left[ \widehat { T } _ { \mathrm { I , m a x } } ( h | k ) , \widehat { T } _ { \mathrm { O , m a x } } ( h | k ) \right] ^ { \mathrm { T } }$ is the vector of the maximum values of the temperatures of the inlet and outlet zones of the server $T _ { \mathrm { R e d l i n e } } = \left[ T _ { \mathrm { R e d l i n e 1 } } , T _ { \mathrm { R e d l i n e 2 } } \right] ^ { \mathrm { T } }$ is the vector of the upper limits of the temperatures of the inlet and outlet zones of the server, and $\widehat \Psi ( h | k ) = \widehat T _ { \mathrm { S e r v e r } } ( h | k ) - T _ { \mathrm { R e d l i n e } }$ is the penalty, which only takes effect if the maximum value of the server’s temperature exceeds the threshold temperature; otherwise, it is zero.

The optimization problem is solved by the sequential quadratic programming (SQP) method, which is implemented by the fmincon function in the optimization toolbox of the Matlab R2018a software.

## 2.7. Simulation experiments based on control strategies

As shown in Fig. 3, the investigated data center in this study consists of the cooling unit located at the bottom of the rack and 10 servers. In order to evaluate the control performance of the proposed control strategy under dynamic workload, the study collects the number of EPA web workload requests from proxy servers at a sampling period of 1 min [42]. Then, this count is converted into the average CPU workload rate. The 24h average CPU workload rate shown in Fig. 5 is used for simulation testing. For the cooling system independent control strategy, the workload is uniformly distributed among the servers, i.e., the utilization of each server is equal to the average CPU workload rate. For the joint optimization strategy, each server workload is the result of distribution optimization based on the total workload. The power consumption of each server can be calculated by Eq. (6).

In order to verify the effectiveness of the proposed control strategies, this study sets up different control strategies for comparison. To ensure that the univariate control model achieves maximum energy savings and adheres to the server temperature constraints, the study kept the supplied airflow rate at $0 . 2 \mathrm { m } ^ { 3 } / s$ and $0 . 1 7 \mathrm { m } ^ { 3 } / \mathrm { s }$ and the supplied cold air temperature at ${ } ^ { 2 3 ^ { \circ } \mathrm { C } }$ and ${ 2 2 ^ { \circ } \mathrm { C } } ,$ respectively. It is crucial to note that deviating below the fixed supplied airflow rate or exceeding the fixed supplied cold air temperature would lead to the server temperature exceeding the threshold temperature. The following seven control strategies are used for comparison.

(1) JOS-hierarchical (The proposed hierarchical joint optimization strategy): This strategy first optimizes workload distributions based on steady-state conditions, and then uses MPC to optimize the $T _ { \mathrm { R C U } }$ and $Q _ { \mathrm { { R C U } } }$

(2) JOS-centralized (Centralized joint optimization strategy): This strategy optimizes $T _ { \mathrm { R C U } } , ~ Q _ { \mathrm { R C U } }$ and workload distributions simultaneously.

(3) Opti-Cooling: This strategy only optimizes $T _ { \mathrm { { R C U } } }$ and $Q _ { \mathrm { { R C U } } } ,$ with the workload evenly distributed among the servers.

(4) $\mathrm { { O p t i - T _ { R C U 1 } } \mathrm { { : } } }$ This strategy only optimizes $T _ { \mathrm { { R C U } } } ,$ , with $Q _ { \mathrm { { R C U } } }$ fixed at $0 . 2 \ \mathrm { m } ^ { 3 } / s ,$ , and the workload evenly distributed among the servers.

(5) Opti-T : This strategy only optimizes $T _ { \mathrm { { R C U } } } ,$ , with $Q _ { \mathrm { { R C U } } }$ fixed at $0 . 1 7 \mathrm { m } ^ { 3 } / s .$ , and the workload evenly distributed among the servers.

(6) $\scriptstyle \mathrm { O p t i - Q _ { R C U 1 } } :$ This strategy only optimizes Q , with $T _ { \mathrm { R C U } }$ fixed at ${ 2 3 } ^ { \circ } \mathrm { C } ,$ and the workload evenly distributed among the servers

(7) Opti- $\scriptstyle Q _ { \mathrm { R C U 2 } } .$ This strategy only optimizes $Q _ { \mathrm { { R C U } } } ,$ with $T _ { \mathrm { R C U } }$ fixed at ${ 2 2 } ^ { \circ } \mathrm { C } ,$ and the workload evenly distributed among the servers.

Since the cooling parameters contain $T _ { \mathrm { R C U } }$ and $Q _ { \mathrm { { R C U } } } ,$ they play different roles in data center control. Therefore, Section 3 explores the energy management and thermal management performance of univariate and multivariate optimization of cooling parameters through the comparative analysis of Opti-Cooling with Opti-T , Opti-T , Opti-Q , and $\mathrm { { O p t i - Q _ { R C U 2 } } }$ . Section 4 explores the further energy saving potential and temperature field control effect of joint optimization on the basis of independent control of the cooling system by comparing JOS-hierarchical with Opti-Cooling. In addition, the high computational efficiency is verified through the comparison of JOS-hierarchical with JOS-centralized.

## 3. Results and discussion

## 3.1. Energy management performance

The energy consumption and power usage effectiveness (PUE) of the cooling system with different control strategies are shown in Fig. 6. PUE is a key indicator for evaluating the efficiency of power usage in a data center. It represents the ratio of the total energy consumption of the data center to the energy consumption of IT equipment. Theoretically, a lower PUE signifies higher power usage efficiency. The results indicate that th $\mathrm { O p t i - Q _ { R C U 2 } }$ strategy causes serious server supercooling problems, resulting in wasted cooling energy consumption. Therefore, it exhibits the highest cooling system energy consumption, with a 24-h power consumption of 10.195 kW h and a PUE value of 1.291. In contrast, Opti-Cooling shows the optimal results in terms of both energy consumption and PUE value, with its 24-h power consumption of 8.270 kW h and the PUE value of 1.236.

![](images/35e634c41030ecfaeb2f6b1a06878aac44a9b0885d03d05095e356d32776bdd6.jpg)  
Fig. 5. The workload rate variation in the data center.

![](images/257181aeafd86459f6ddac33570afdb28b7e5b845ba50f37d917d4982e06d7cd.jpg)  
Fig. 6. The comparison of cooling system energy consumption and PUE under different control strategies.

It is worth noting that the Opti-Cooling and $\mathrm { { O p t i - T _ { R C U 1 } } }$ have almost equal cold source energy consumption, while Opti-Cooling achieves a 15.59 % reduction in fan power consumption. This reduction is attributed to Opti-Cooling setting the supplied airflow rate much lower, resulting in a lower PUE and an overall energy saving of 4.73 %. However, Opti-Cooling does not show significant fan energy savings compared to the other three control strategies and may even have higher fan energy consumption. The primary reduction in overall energy consumption achieved by Opti-Cooling is through a significant decrease in cold source energy con sumption, mainly by setting the supplied cold air temperature as high as possible. This is because fan energy consumption represents a small percentage of the overall energy consumption, and its contribution to overall energy saving is relatively small. As a result, the overall energy saving can be achieved by reducing cold source energy consumption at the expense of fan energy consumption. Compared to the other three control strategies, Opti-Cooling reduces the cooling system energy consumption by 5.18 %, 13.35 %, and 18.88 %, respectively. The results show that the Opti-Cooling effectively reduces energy consumption by not only decreasing the supplied airflow rate but also suppressing server overcooling by adjusting the supplied cold air temperature through increased airflow rate.

Fig. 7 shows the boxplots of the supplied cold air temperature and airflow rate of the systems with different control strategies. The average supplied cold air temperature of the Opti-Cooling is higher than that of the other four control strategies, reaching $2 6 . 1 2 ^ { \circ } \mathrm { C } .$ At the same time. Opti-Cooling shows the highest dispersion in supplied airflow rate and the lowest dispersion in supplied cold air temperature. It suggests that Opti-Cooling can improve the supplied cold air temperature and reduce fluctuations by adjusting the supplied airflow rate, thereby achieving stability and energy efficiency in the data center.

![](images/23a5dab0b3b729055aad82c19d2d982bdf8b469411aa9e0e0bdb5a9496ec62e9.jpg)  
Fig. 7. Comparison of cooling parameters under different control strategies.

## 3.2. Thermal management performance

To ensure that even if the server workload fluctuates significantly in a short period of time, the temperature in the inlet and outlet zones remains within the specified range, the penalty weight coefficients (Γ) for violating temperature constraints in the cost function are set to sufficiently large values. The maximum inlet zone and outlet zone temperatures of racks with different control strategies are shown in Fig. 8. The results demonstrate that all control strategies effectively maintain inlet zone temperatures below $2 7 ^ { \circ } \mathrm { C }$ and outlet zone temperatures below ${ } ^ { 3 5 } \ { } ^ { \circ } \mathrm { C } ,$ ensuring the secure operation of the data center. It is noteworthy that Opti-Cooling exhibits higher temperature extremes compared to the other control strategies, with its maximum inlet and outlet temperatures consistently stable around ${ } ^ { 2 7 } \ { } ^ { \circ } \mathrm { C }$ and $3 5 ~ ^ { \circ } \mathrm { C } .$ Therefore, the Opti-Cooling performs best when the maximum inlet/outlet temperature are close to the threshold temperature.

The averages inlet and outlet temperature of the 10 servers are shown in Fig. 9. The Opti-Cooling effectively reduces energy waste by optimizing the supplied airflow rate to minimize cold air bypass, resulting in higher average temperatures in the outlet zones compared to the $\mathrm { { O p t i - T _ { R C U 1 } } }$ and $\mathrm { { O p t i - T _ { R C U 2 } } . }$ Compared with Opti-Cooling, the $\mathrm { O p t i - Q _ { R C U 1 } }$ and $\mathrm { O p t i - Q _ { R C U 2 } }$ consistently maintain lower inlet temperatures for all servers, indicating a noticeable overcooling phenomenon. The Opti-Cooling strategy achieves a more uniform temperature distribution, reducing temperature fluctuations, and ensuring the stability and reliability of server operation.

A comprehensive comparison of the different control strategies reveals that the control strategy of only optimizing supplied cold air temperature has a certain advantage in system energy saving over only optimizing supplied airflow rate. However, this strategy can cause large temperature fluctuations. The control strategy of only optimizing the supplied airflow rate has relatively high energy consumption, but it can ensure the stability of the temperature. The simultaneous control and optimization of supplied cold air temperature and airflow rate can effectively avoid overcooling, thereby reducing the energy consumption of the cooling system Additionally, it can minimize temperature fluctuations, ensuring the stable operation of the server.

## 3.3. Effect of workload level

To explore the impact of server workload levels on cooling system energy consumption, the workload data used for testing is divided into three phases based on the CPU workload rate of the data center. Specifically. the period from 0 to 8 h is regarded as lowworkload operation, 16–24 h as medium-workload operation, and 8–16 h as high-workload operation. The characteristics of different workloads are shown in Table 3.

The energy consumption of the cooling system with different control strategies for different workloads is shown in Fig. 10. As the system goes from low workload to high workload, the energy savings of Opti-Cooling with respect to $\mathrm { { O p t i - T _ { R C U 1 } } }$ decreases from 8.718 % to 1.133 %. In addition, it can be seen that the cold source energy consumption of Opti-Cooling and $\mathrm { { O p t i - T _ { R C U 1 } } }$ is almost equal, and the energy saving of the cooling system is mainly realized by reducing the fan power consumption. When the workload is high, the airflow has reached its maximum value, resulting in insignificant energy savings. Therefore, when the data center is in a long-time low workload operation, Opti-Cooling will show more obvious advantages. Compared to $\mathrm { { O p t i - Q _ { R C U 1 } } }$ , Opti-Cooling has comparable energy savings under low, medium, and high loads. Opti-Cooling achieves energy savings mainly by reducing cold source energy con sumption, which is because Opti-Cooling can maintain the supplied cold air temperature at a high level by adjusting the supplied airflow rate, thus realizing significant energy savings under various workload levels.

## 4. Cooling parameters and workload distributions joint optimization strategy

An optimization horizon is selected to verify the convergence of the optimization algorithm, as shown in Fig. 11. Fig. 11(a) shows

![](images/73c0277d572de23cf730525920ea61f2d84fce578a1689f2e9fdcd7e99a72687.jpg)

![](images/8890b81513f441a454284f9b6e10adabede20f44a1081aa8acba2d947b874ed4.jpg)  
Fig. 8. The comparison of maximum server inlet and outlet temperature under different control strategies: (a) Maximum inlet temperature. (b) Maximum outlet temperature.

![](images/cf5b8b8baa4b96271f653c03ea351e7c4b6557f09fa7833515db3938cf8f0a8c.jpg)  
Fig. 9. The comparison of average temperature values of server inlet and outlet zones under different control strategies.

Table 3  
Characteristics of different workloads.

<table><tr><td>Workload</td><td>Low workload</td><td>Medium workload</td><td>High workload</td></tr><tr><td>Time (h)</td><td>0–8</td><td>16–24</td><td>8–16</td></tr><tr><td>Average workload (%)</td><td>16.16</td><td>25.87</td><td>50.33</td></tr><tr><td>Peak workload (%)</td><td>48.13</td><td>70.63</td><td>88.75</td></tr></table>

![](images/1e75d08d41d2c1d586139be879c723deb1840c31749f4e8ea1f3257f187c09ca.jpg)  
Fig. 10. The comparison of cooling system energy consumption of different control strategies under different workloads.

the optimal workload distributions based on steady-state optimization, and Fig. 11(b) shows the dynamic optimization of cooling parameters based on MPC. It can be seen that with the increase of the number of iterations, the cost function gradually tends to a stable value, indicating that the optimization results converge. In addition, the optimization results of the algorithm are tested under different initial values, and the objective function values are almost identical across all optimization horizons, which further verifies the convergence of the algorithm.

The energy consumption of the data center under the two control strategies is shown in Fig. 12. The data shows that the cooling system with the JOS-hierarchical has reduced cold source energy consumption and fan energy consumption compared to the Opti-Cooling strategy. Therefore, when server workload distributions optimization is considered, 5 % of cooling energy consumption can be saved for the data center.

Figs. 13 and 14 show the results of workload distributions and cooling parameter optimization for the two control strategies, respectively. In contrast to previous studies where server loads are usually distributed close to the cooling units, Fig. 13 reveals that the distribution of server workloads is directly proportional to the amount of server cold air bypass, i.e.. the larger the amount of cold air bypass, the more workloads are allocated. In this way, more workloads can be distributed to servers with higher cooling losses, effectively alleviating the problem of server overcooling and thus reducing the energy consumption of the cooling system. In addition, as shown in Fig. 14, the fundamental reason for the JOS-hierarchical to achieve energy conservation is to increase the supplied cold air temperature while reducing the supplied airflow rate of the cooling system through optimization of server workload distributions.

![](images/c3d31e9cd421ad88f7db24e26f73d10ef4735c3526e269c63e7124f07b5cd0e1.jpg)

![](images/6311dfcb38e677244309d37cc34417a3220fd65b95495a3f1839e27ede97b55d.jpg)  
Fig. 11. Optimization process of JOS-hierarchical

![](images/2f36d54f417937b40a7e9b6410ca8408c98e101da5158b040a741f7462f4b4f2.jpg)  
Fig. 12. The comparison of cooling system energy consumption under Opti-Cooling and JOS-hierarchical.

Fig. 15 shows the 24-h average values of server inlet and outlet temperatures under two control strategies. In terms of server inlet temperatures, those under the JOS-hierarchical consistently surpass the temperatures under the Opti-Cooling strategy. For server outlet temperatures, notable overcooling phenomena are observed in the 8th and 9th servers under the Opti-Cooling strategy, leading to substantial temperature variations among different servers. Conversely, the JOS-hierarchical effectively utilizes wasted cooling capacity by optimizing workload distributions and adjusting cooling parameters, significantly mitigating server overcooling and achieving a more uniform temperature distribution. As a result, the JOS-hierarchical demonstrates substantial advantages in reducing the energy consumption of data center cooling systems and fostering a uniform temperature field distribution.

In order to demonstrate the computational efficiency of the JOS-hierarchical, it is compared with the JOS-centralized. If the JOScentralized is used to optimize the cooling parameters and workload distributions, $1 2 { \times } N _ { c }$ variables need to be optimized at the same time in each sampling cycle. The results show that the whole optimization process of JOS-centralized takes 2.54 times longer than that of the JOS-hierarchical, but the reduction of cooling system energy consumption is only 0.43 %. It is noteworthy that our optimization problem entails optimizing the workload distributions for only 10 servers. In practical scenarios, a data center rack may consist of dozens of servers, leading to an exponential rise in computational complexity. Conversely, our approach significantly simplifies the computation while achieving substantial energy savings.

a)  
![](images/30bcb3bd7a28a3e407f808a1974aded1ae837eeeba68d576193ad285555dc7d4.jpg)  
Fig. 13. JOS-hierarchical workload distributions optimization results

![](images/af44fe5badee04a357af1627c3443e05651f627c95726860ad518dd9c7f14f3e.jpg)

b)  
![](images/8711881e39241f6e7edb0575e8fab77f8350531780e99bc3abcf90a3239a5f66.jpg)  
Fig. 14. Comparison of control parameters under Opti-Cooling and JOS-hierarchical: (a) Supplied cold air temperature. (b) Supplied airflow rate

The above research indicates that compared to independent control of the cooling system, the proposed joint optimization strategy considers the thermal correlation between the cooling system and IT equipment. This strategy optimizes fine-grained workload dis tributions to adjust server fan speeds, while coordinating the airflow of the cooling system to achieve a supply-demand match for heterogeneous servers. This reduces server overcooling, leading to lower energy consumption and more uniform temperature dis tribution. At the same time, the proposed hierarchical joint optimization strategy demonstrates higher optimization efficiency compared to traditional centralized joint optimization strategy, ensuring energy savings while achieving faster convergence and higher feasibility. Therefore, the proposed control strategy is of great importance for energy saving control and thermal management in data

![](images/0fa602e9fb436fcb5fe739779f43315baef67bf8a3683b07422b89d3d3756678.jpg)  
Average temperature of server inlet zones (°C)

![](images/29eca816c0abf56cc09387b7349860a9beaf971e47ab1e06e1b4720201716fee.jpg)  
Average temperature of server outlet zones (°C)  
Fig. 15. Comparison of average temperature of server inlet and outlet under Opti-Cooling and JOS-hierarchical.

centers.

## 5. Conclusions and future works

In this study, a novel joint optimization strategy based on MPC for cooling systems and workload distributions was proposed for data centers with rack-based cooling architecture, aimed at energy-saving control and thermal management. For a given workload, it can optimize granular workload distributions based on steady-state conditions, and then dynamically adjust cooling parameters based on MPC to improve the efficiency of the cooling system. The proposed strategy not only achieves significant energy savings but also greatly enhances optimization efficiency. To explore the role of different cooling parameters and workload distributions in joint optimization, the effects of univariate optimization and multivariate optimization of cooling parameters on control performance were first analyzed. Then, the proposed hierarchical joint optimization strategy was compared and analyzed with independent control of the cooling system, verifying the energy saving potential and temperature field control effect that considers server heterogeneity. Finally, the computational efficiency and energy efficiency of the proposed hierarchical joint optimization strategy and the centralized joint optimization strategy were compared to verify the superiority. The main conclusions of this work are as follows:

(1) Compared with single variable optimization of cooling parameters, the multivariate optimization strategy can improve the supply air temperature by optimizing the airflow distribution, achieving at least 4.7 % energy savings.

(2) Compared with independent control of the cooling system, the proposed joint optimization strategy can suppress server overcooling, further saving 5 % of cooling energy for data centers, and achieving a more uniform temperature distribution.

(3) Compared with centralized joint optimization strategy, the proposed hierarchical joint optimization strategy greatly reduces the complexity of optimization problems and can have higher computational efficiency while ensuring energy-saving effects.

Although the simulation experiment results have verified that the proposed strategy can be well applied to the optimization contro of cooling parameters and workload distributions in data centers. However, some disturbances are inevitable in actual systems, leading to deviations between the ideal conditions of numerical simulations and the real-world operation of the system. Therefore, in future research, we plan to build an experimental platform to further validate the application of the control optimization algorithm in actua data centers. In addition, the uncertainty of models, sensor errors, and execution errors may potentially affect this control strategy’s practical application. Therefore, it is necessary to explore the impact of uncertainty on control performance based on on-site exper imental testing in future work.

## CRediT authorship contribution statement

Jiaqiang Wang: Writing – review & editing, Supervision, Methodology, Conceptualization. Weiqi Deng: Writing – original draft Software, Methodology, Formal analysis. Chang Yue: Supervision, Software, Formal analysis, Conceptualization. Wen Su: Visuali zation, Software, Data curation. Xuelian Bai: Writing – review & editing.

## Declaration of competing interest

The authors declare that they have no known competing financial interests or personal relationships that could have appeared to influence the work reported in this paper.

## Acknowledgment

The research work of this study was supported by the National Natural Science Foundation of China (No.52208136), the Natural Science Foundation of Hunan Province of China (2024JJ5436), the International Science and Technology Cooperation Programme of China (2023YFE0120400), and the Opening Foundation of Anhui Province Key Laboratory of Intelligent Building & Building Energy

Saving (IBES2022KF05).

## Data availability

The authors do not have permission to share data.

## References

[1] Y.J. Choi, B.R. Park, J.Y. Hvun, J.W. Moon. Development of an adaptive artificial neural network model and optimal control algorithm for a data center cyber–physical system, Build. Environ. 210 (2022) 108704.

[2] J. Wang, Y. Guo, C. Yue, W. Deng, L. Zeng, Comprehensive assessment of waste heat recovery mismatch and renewable energy integration in data centers: a multifaceted energy, economic, and environmental perspectives, J. Clean. Prod. 472 (2024) 143466.

[3] J. Wang, Q. Zhang, S. Yoon, Y. Yu, Impact of uncertainties on the supervisory control performance of a hybrid cooling system in data center, Build. Environ. 148 (2019) 361–371.

[4] Y. Tian, J. Wang, Z. Qi, C. Yue, P. Wang, S. Yoon, Calibration method for sensor drifting bias in data center cooling system using Bayesian Inference coupling with Autoencoder, J. Build. Eng. 67 (2023) 105961.

[5] E. Masanet, A. Shehabi, N. Lei, S. Smith, J. Koomey, Recalibrating global data center energy-use estimates, Science 367 (6481) (2020) 984–986.

[6] Y. Zhang, K. Shan, X. Li, H. Li, S. Wang, Research and Technologies for next-generation high-temperature data centers – state-of-the-arts and future perspectives, Renew. Sust. Energ. Rev. 171 (2023) 112991.

[7] J. Wang, Y. Tian, Z. Qi, L. Zeng, P. Wang, S. Yoon, Sensor fault diagnosis and correction for data center cooling system using hybrid multi-label random Forest and Bavesian Inference. Build. Environ, 249 (2024) 111124.

[8] S. MirhoseiniNejad, G. Badawy, D.G. Down, Holistic thermal-aware workload management and infrastructure control for heterogeneous data centers using machine learning, Future Generat. Comput. Syst. 118 (2021) 208–218.

[9] R. Zhao. Y. Du. X. Yang, Z. Zhou, W. Wang, X. Yang, A critical review on the thermal management of data center for local hotspot elimination, Energy Build, 297 (2023) 113486.

[10] B. Durand-Estebe, C. Le Bot, J.N. Mancos, E. Arquis, Data center optimization using PID regulation in CFD simulations, Energy Build. 66 (2013) 154–164.

[11] M. Kheradmandi, D.G. Down, H. Moazamigoodarzi, Energy-efficient data-based zonal control of temperature for data centers. 2019 Tenth International Green and Sustainable Computing Conference (IGSC), IEEE, 2019, pp. 1–7.

[12]. K. Jiang, M. Kheradmandi. C. Hu. S. Pal. F. Yan. Data-driven fault tolerant predictive control for temperature regulation in data center with rack-based cooling architecture, Mechatronics 79 (2021) 102633.

[13] Y. Zhu, Q. Zhang, L. Zeng, J. Wang, S. Zou, H. Zheng, An advanced control strategy for optimizing the operation state of chillers with cold storage technology in data center, Energy Build. 301 (2023) 113684.

[14] J. Wang, Q. Zhang, Y. Yu, X. Chen, S. Yoon, Application of model-based control strategy to hybrid free cooling system with latent heat thermal energy storage for TBSs, Energy Build. 167 (2018) 89–105.

[15] J. Hu. P. Karava. A state-space modeling approach and multi-level optimization algorithm for predictive control of multi-zone buildings with mixed-mode cooling, Build. Environ. 80 (2014) 259–273.

[16] W. Liu. X. Tong, J. Wang, C. Yue, O. Zhang, Real-time temperature predictions via state-space model and parameters identification within rack-based cooling data centers, J. Build. Eng. 58 (2022) 105013.

[17] S. Tashiro, Y. Tarutani, G. Hasegawa, Y. Nakamura, K. Matsuda, M. Matsuoka, A network model for prediction of temperature distribution in data centers. 2015 IEEE 4th International Conference on Cloud Networking (CloudNet), IEEE, 2015, pp. 261–266.

[18] K. Sun, N. Luo, X. Luo, T. Hong, Prototype energy models for data centers, Energy Build. 231 (2021) 110603.

[19] Y. Fulpagare, Y. Joshi, A. Bhargav, Rack level transient CFD modeling of data center, Int. J. Numer. Methods Heat Fluid Flow 28 (4) (2018), 00-00.

[20] Y. Fulpagare, A. Bhargav, Advances in data center thermal management, Renew. Sust. Energ. Rev. 43 (2015) 981–996

[21] X. Han, W. Tian, J. VanGilder, W. Zuo, C. Faulkner, An open source fast fluid dynamics model for data center thermal management, Energy Build. 230 (2021) 110599.

center, J. Build. Eng. 78 (2023) 107722.

[23] H. Liu, Y. Sun, X. Sun, Y. Wang, H. Chao, F. Yan, Data center server workload and infrastructure control based on a joint RSM and CFD approach, J. Build. Eng. 77 (2023) 107431.

[24] X. Tong, J. Wang, W. Liu, H.-A. Samah, Q. Zhang, L. Zhang, A time-varying state-space model for real-time temperature predictions in rack-based cooling data centers, Appl. Therm. Eng. 230 (2023) 120737.

[25] J. Wang, X. Tong, C. Yue, W. Liu, Q. Zhang, L. Zeng, G. Huang, Real-time temperature distribution reconstruction via linear parameter-varying state-space model and Kalman filter in rack-based cooling data centers, Build. Environ. 242 (2023) 110601.

Inst, 360 (5) (2023) 3657–3676.

[27] A. Talib. S. Park, P. Im. J. Joe. Grey-box and ANN-based building models for multistep-ahead prediction of indoor temperature to implement model predictive control, Eng. Appl. Artif. Intell. 126 (2023) 107115.

202–212.

[29] S. MirhoseiniNejad, H. Moazamigoodarzi, G. Badawy, D.G. Down, Joint data center cooling and workload management: a thermal-aware approach, Future Generat. Comput. Syst. 104 (2020) 174–186.

[30] R. Gupta, S. Asgari, H. Moazamigoodarzi, D.G. Down, I.K. Puri, Energy, exergy and computing efficiency based data center workload and cooling management, Appl. Energy 299 (2021) 117050.

[31] O. Fang, J. Zhou. S. Wang, Y. Wang, Control-oriented modeling and optimization for the temperature and airflow management in an air-cooled data-center. Neural Comput, Appl. 34 (7) (2022) 5225–5240.

[32] S.M.M. Nejad, G. Badawy, D.G. Down, EAWA: Energy-Aware Workload Assignment in Data Centers, 2018 International Conference on High Performance Computing & Simulation (HPCS), IEEE, 2018, pp. 260–267.

[33] H. Zhu. J. Wang, M. Song, O. Fang, Thermal-aware load provisioning for server clusters by using model predictive control. 2015 IEEE Conference on Control Applications (CCA), IEEE, 2015, pp. 336–340.

[34] S. Mengxuan, H. Zhu, O. Fang, J. Wang, Thermal-aware load balancing in a server rack, 2016 IEEE Conference on Control Applications (CCA), IEEE, 2016 pp. 462–467.

[35] Q. Fang, J. Wang, Q. Gong, QoS-driven power management of data centers via model predictive control, IEEE Trans. Autom. Sci. Eng. 13 (4) (2016) 1557–1566.

[36] T. Mukherjee, A. Banerjee, G. Varsamopoulos, S.K.S. Gupta, S. Rungta, Spatio-temporal thermal-aware job scheduling to minimize energy consumption in virtualized heterogeneous data centers, Comput. Network. 53 (17) (2009) 2888–2904.

[37] S. Mirhoseininejad, G. Badawy, D.G. Down, A data-driven, multi-setpoint model predictive thermal control system for data centers, J. Netw. Syst. Manag. 29 (1) (2020) 7.

[38] Q. Tang, S.K.S. Gupta, G. Varsamopoulos, Energy-efficient thermal-aware task scheduling for homogeneous high-performance computing data centers: a cyberphysical approach, IEEE Trans. Parallel Distr. Syst. 19 (11) (2008) 1458–1472.

[39] R. Gupta, H. Moazamigoodarzi, S. MirhoseiniNejad, D.G. Down, I.K. Puri, Workload management for air-cooled data centers: an energy and exergy based approach, Energy 209 (2020) 118485.

[40] Q. Fang, J. Wang, Q. Gong, M. Song, Thermal-aware energy management of an HPC data center via two-time-scale control, IEEE Trans. Ind. Inf. 13 (5) (2017) 2260-2269.

[41] W. Deng, J. Wang, C. Yue, Y. Guo, Q. Zhang, Model-based control strategy with linear parameter-varying state-space model for rack-based cooling data centers, Energy Build. 319 (2024) 114528.

[42] The IRCache Project. http://www.ircache.net/.