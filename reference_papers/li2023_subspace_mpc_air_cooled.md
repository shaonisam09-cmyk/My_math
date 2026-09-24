# A data-driven subspace predictive control method for air-cooled data center thermal modelling and optimization

Zhe Li, Haoda Wang, Qiu Fang<sup>∗</sup>, Yaonan Wang

National Engineering Research Center for Robot Vision Perception and Control Technology, Department of Contro Science and Engineering, Hunan University, Changsha, Hunan, 410082 PR China

Received 8 September 2022; received in revised form 19 December 2022; accepted 5 February 2023 Available online 12 February 2023

## Abstract

This paper presents a data-driven predictive control method for optimizing the energy consumption of air-cooled data centers with unknown system model parameters. First, based on the measurable data of the studied system, the subspace predictive control (SPC) method is adopted to improve the energy use efficiency of the data center by regulating the power allocation of the server racks and the supply temperature of cold air, while ensuring the safe operating environment of the electronic equipment. Furthermore, a reasonable event-triggered law is designed to solve the problem of the low computational efficiency of the conventional SPC method. The simulation results illustrate that the designed eventtriggered law can improve the computational efficiency of the algorithm while maintaining the control performance of the algorithm, which verifies its application prospect in practice. © 2023 The Franklin Institute. Published by Elsevier Inc. All rights reserved.

## 1. Introduction

The data centers have become the key infrastructure for modern information technology (IT) with the rapidly increasing amount and power consumption in recent years. In 2018, the power consumption of Internet data centers accounted for about 3% of the total global power supply [1], as well as increased yearly at a rate of 15–20%. In particular, the IT facilities and the associated heating, ventilation and air conditioning systems take up the main energy consumption of the data center. Heat is generated in the computer room while the IT systems are working with the electricity consumption. In order to maintain a safe thermal environment for electronic devices, each data center is equipped with a cooling control system, which accounts for more than 40% of the energy consumption of the whole data center [2]. In fact, the power consumption of data centers is supposed to decrease by 45% compared to the current situation by adopting more aggressive energy efficient strategies [3], which implies the available improvement in energy efficiency for data centers.

![](li2023_subspace_mpc_air_cooled_assets/images/4e621defc9a695a76f9c6186b737fb4a1bb86f05fdb5fa2096ed4130253cffc4.jpg)  
Fig. 1. Schematic diagram of data center heat-recirculation

The raised-floor cold/hot aisle configuration is adopted in most of the current air-cooled data centers. Figure 1 demonstrates a typical layout and airflow in a data center server room. The computer room air conditioning (CRAC) unit provides cold air to the ventilation system under the floor, and then distributes the cold air to the cold aisle channel through perforated tiles. The cold air flow through the server racks, taking away the generated heat by the electronic devices. Meanwhile, the hot air output from the rack returns to the air inlet of the CRAC unit. Consequently, the flow paths of cold and hot air are separated by the cold/hot aisle configuration.

Due to the complex nature of mixed airflow and heat transfer inside the data center, some of the hot exhaust air from the outlets of the server racks will recirculate into the inlets of other server racks. Under such circumstances, to provide acceptable inlet temperatures for all servers [4], the supplied air temperature of the cooling system has to be decreased. In the daily operation of existing data centers, the information facilities are usually overcooled to maintain a relatively safer thermal environment and waste a lot of energy. The temperature distribution in the data center changes with the power consumed by racks. Meanwhile, the power of server racks is proportional to the workload. Hence, it is important to coordinate the IT task scheduling and cooling system control and discover the potential energy savings. The coordinated control of the IT and cooling systems is challenging because the real-time prediction of the dynamic temperature of the nonlinear thermal system is hard [5]. The fast temperature evaluation/prediction model acts as a bridge by connecting the IT and cooling systems. By predicting the key temperature distribution in real-time, the control algorithm can provision and efficiently distribute the computing tasks and the supplied cold air to ensure that each server operates within its thermal threshold and minimize the related energy consumption [6]. In the following, related modeling and optimal control methods are introduced. The drawbacks of existing methods make them hard to put into practice.

As for the data center layout design and planning, computational fluid dynamics (CFD) simulation has been widely applied to evaluate the thermal performance of a given configuration of the data center, such as layout, supply air temperature and power density, and so on. However, CFD simulations are usually too time-consuming to be applied in real-time thermal management. Thus, various modeling methods have been introduced to evaluate the temperature more efficiently. In [7], the models based on potential flow theory ware developed as an alternative to CFD, such that only the basic physical mechanism in the flow process is retained to save computing time. But in fact, it is still not effective for the design of real-time control system.

Therefore, the control-oriented temperature evaluation models have been developed to facilitate real-time optimal control by control engineers. In works [8,9], various thermal eval uation indexes were proposed to evaluate the thermal performance of airflow, including the supply heat index (SHI), the return heat index (RHI), and the return temperature index (RTI). Tang et al. [10] proposed an abstract heat flow model to calculate the temperature distribution of racks based on the supply temperature of the air conditioners and the power of the racks, with the assumption of a stable airflow field. In ref. [11], a state space model inspired by the abstract heat transfer model is introduced to dynamically predict the temperature change. Furthermore, relevant studies [6,12] have developed the optimal control method based on the state-space model. Based on the same type of thermal evaluation model, algorithms [13,14] were proposed to improve the energy efficiency of the cooling system by setting its reference temperature as high as possible. Particularly, all the models above were established under the assumption of a stable air flow pattern in the room. In fact, as the fan speed of the rack and the air supply volume of the cooling system can change a lot, the air flow field in the data center will change greatly. However, there are few studies on modeling methods considering such uncertain air flow patterns.

Thanks to the development of data-driven methods, the complicated models of various industrial processes can be constructed based on the collected data [15,16]. As a typical datadriven method, machine learning has been applied in the data center thermal modeling process. Methods [15,17] were proposed to learn the thermal distribution resulting from different workload placements and applied in various purposes. Learning models [18,19] based on neural networks were developed for temperature prediction in data centers. These models pointed directions for the fast temperature evaluation in data centers. However, these learning methods usually need a large amount of historical data or experimental data for model training. Comparatively, the identification method is another classic data-driven modeling technique, in which the subspace identification is a particular example. Subspace identification methods (SIM) have enjoyed tremendous development in the past decades in both theory and practice due to simple and general parametrization for multiple-input-multiple-output (MIMO) systems [20,21]. Inspired by the idea, the subspace predictive control (SPC) approaches were developed to design the predictive controller directly from the measured input and output (I/O) data without the system model [22]. Consequently, this data-driven predictive control method has provoked increasing research in theory and application [23,24].

In this paper, the subspace predictive control (SPC) method is adopted to derive the data center thermal models as well as design its energy management predictive control scheme, which also relies on the system input and output data. It is designed to overcome the shortcomings of previous methods, such as time-consuming modeling computations, steady flow field assumptions, or large amounts of data for modeling training. Particularly, the constructed data matrices by the subspace-aided method can map the relationship with thermal and power consumption models of the data center with explicit physical principles. Therefore, the proposed method has better industrial interpretation than the machine learning methods. Besides, the amount of the collected historical data is much less than that of the machine learning meth ods, which facilitates the dynamic application in data centers. Moreover, an event-triggered law is applied to further reduce the load of data transmission and computation of the SPC method, which improves its feasibility for data-driven energy management in data centers.

The contribution of this work are highlighted as follows:

<sub>•</sub> On the basis of power efficiently and safe operating environment, a data-driven control framework is proposed for for the modeling and managing the thermal environment of air-cooled data centers;

An sub-space model predictive control based technique is proposed for regulating the power allocation of the server racks and the supply temperature of cold air;

A reasonable event-triggered law is designed to solve the problem of the low computational efficiency of the conventional sub-space control method.

The rest of this paper is organized as follows. Section 2 introduces the thermal and power model of the data center and formulates its energy management problem. Section 3 presents the power management strategy for the data center based on the SPC method, along with the case studies to illustrate its satisfactory control performance. In Section 4, the event-triggered SPC method is proposed to reduce the data transmission and computation load for practical applications, and the simulation results are analyzed with comparisons. Finally, Section 5 gives the conclusion of this paper.

## 2. Models for data center and problem statement

In the following part of this section, a series of models for data centers are described, including the thermal dynamic model (Section 2.1) and the power consumption model (Section 2.2). Then the study problem of this paper is presented (Section 2.3). All the notations used in this paper are summarized in Table 1.

## 2.1. The thermal dynamic model

From the thermal perspective, both server racks and CRAC units are thermal nodes that cause temperature changes in the the data center environment. The entire data center thermal network contains N rack nodes and M CRAC nodes. Each thermal node has an inlet tem perature and an outlet temperature (see Fig. 2). The outlet temperature dynamics of Rack i is related to the inlet temperature $T _ { \mathrm { r i } , i }$ , outlet temperature $T _ { \mathrm { r o } , i }$ and power consumption $P _ { \mathrm { r } , i ; }$ which can be approximated by the following linear model [11]

$$
\dot {T} _ {\mathrm{ro}, i} (t) = - \alpha_ {\mathrm{r}, i} \cdot T _ {\mathrm{ro}, i} (t) + \alpha_ {\mathrm{r}, i} \cdot T _ {\mathrm{ri}, i} (t) + c _ {\mathrm{r}, i} \cdot P _ {\mathrm{r}, i} (t).\tag{1}
$$

where $\alpha _ { \mathrm { r } , i }$ is the reciprocal of the time constant of the rack Node $i , c _ { \mathrm { r } , i }$ is the mapping factor of Rack i power consumption to outlet temperature variation, and $i \in \{ 1 , 2 , \ldots , N \}$

The CRAC node outlet temperature dynamics are typically modeled as a function of the outlet temperature $T _ { \mathrm { c o } , j }$ and the set temperature $T _ { \mathrm { s e t } , j }$ [25]

$$
\dot {T} _ {\mathrm{co}, j} (t) = - \alpha_ {\mathrm{c}, j} \cdot T _ {\mathrm{co}, j} (t) + \alpha_ {\mathrm{c}, j} \cdot T _ {\mathrm{set}, j} (t).\tag{2}
$$

Table 1 Notations and definitions.

<table><tr><td>Notation</td><td>Definiton</td></tr><tr><td>N</td><td>the number of server racks.</td></tr><tr><td>M</td><td>the number of CRAC units.</td></tr><tr><td>S</td><td>the number of Servers in each rack.</td></tr><tr><td> $T_{\text{ri(ro),i}}$ </td><td>the inlet (outlet) temperature of server Rack i.</td></tr><tr><td> $T_{\text{ci(co),j}}$ </td><td>the inlet (outlet) temperature of CRAC Unit j.</td></tr><tr><td> $T_{\text{set,j}}$ </td><td>the set temperature of CRAC Unit j.</td></tr><tr><td> $T_{\text{ref}}$ </td><td>the reference temperature of server rack.</td></tr><tr><td> $\alpha_{r,i}$ </td><td>the reciprocal of the time constant of the rack Node i.</td></tr><tr><td> $\alpha_{c,j}$ </td><td>the reciprocal of the time constant of the CRAC Node j.</td></tr><tr><td> $c_{r,i}$ </td><td>the mapping factor of Rack i power consumption to outlet temperature variation.</td></tr><tr><td> $\phi_{i,j}$ </td><td>the interference coefficient of Node j to Node i.</td></tr><tr><td> $P_{r,i}$ </td><td>the power consumption of server Rack i.</td></tr><tr><td> $P_{c,j}$ </td><td>the power consumption of CRAC Unit j.</td></tr><tr><td> $P_s$ </td><td>the power consumption of Server s.</td></tr><tr><td> $P_{\text{max}}$ </td><td>the max power consumption of the Server s.</td></tr><tr><td> $P_{\text{tot}}$ </td><td>the total power of server racks needed by all computational tasks.</td></tr><tr><td> $\mu_s(t)$ </td><td>the resource utilization of the Server s.</td></tr><tr><td> $K_j$ </td><td>the positive coefficient related to the air density, the flow rate and the air heat capacity.</td></tr><tr><td> $COP(T_{\text{co},j}(t))$ </td><td>the coefficient of performance of CRAC Unit j.</td></tr><tr><td> $\Omega_{k_1}^{k_2}$ </td><td>the data matrix  $\Omega$  with the initial time instant  $k_1$  and final instant  $k_2$ .</td></tr><tr><td> $\omega^z(k)$ </td><td>the data sequences with step length z starting at time k.</td></tr><tr><td> $N_f$ </td><td>the prediction horizon.</td></tr><tr><td> $K_s$ </td><td>the stable state feedback gain of the system.</td></tr><tr><td> $[\ast]^{\dagger}$ </td><td>the pseudo-inverse of matrix [ $*$ ].</td></tr><tr><td> $e(k)$ </td><td>the input error at the time k.</td></tr><tr><td>u</td><td>the input sequence of the system.</td></tr><tr><td> $u_f$ </td><td>the optimal input sequence of the system.</td></tr><tr><td> $U^{p(f)}$ </td><td>the data matrix composed of system input data.</td></tr><tr><td> $T_{\text{in}}^{p(f)}$ </td><td>the data matrix composed of the system inlet temperature.</td></tr></table>

![](li2023_subspace_mpc_air_cooled_assets/images/d514eb18e3f0a5a84972302e67b421feabebd5ceeaed087b0331e0ecbdeef63e.jpg)  
Fig. 2. Cross interference diagram among the thermal nodes.

where $\alpha _ { \mathrm { c } , j }$ is the reciprocal of the time constant of the CRAC Node $j ,$ , and $j \in \{ 1 , 2 , \dots , M \}$ Here the set temperature $T _ { \mathrm { s e t } , j }$ of the CRAC unit is a control variable and in general is not higher than its inlet temperature.

For inlet temperature dynamics, both of the rack nodes and CRAC nodes are equally affected by heat recirculation from the node itself as well as from other nodes. As shown in Fig. 2, using parameter $\phi _ { i , j }$ to denote the interference of Node j on the inlet temperature dynamics of Node i. Then the inlet temperature dynamics of the thermal node can be modeled as

$$
T _ {\mathrm{in}, i} (t) = \sum_ {j = 1} ^ {N + M} \phi_ {i, j} T _ {\mathrm{out}, j} (t).\tag{3}
$$

Then the input temperature and output temperature of all thermal nodes are combined into the form of vectors,

$$
\begin{array}{c} \mathbf {T} _ {\text {in}} = [ T _ {\text {ri}, 1}, \ldots , T _ {\text {ri}, N}, T _ {\text {ci}, N + 1}, \ldots , T _ {\text {ci}, N + M} ] ^ {\text {T}} \\ \mathbf {T} _ {\text {out}} = [ T _ {\text {ro}, 1}, \ldots , T _ {\text {ro}, N}, T _ {\text {co}, N + 1}, \ldots , T _ {\text {co}, N + M} ] ^ {\text {T}}. \end{array}
$$

Considering ${ \bf T } _ { \mathrm { i n } }$ and $\mathbf { T _ { \mathrm { o u t } } }$ as the output and state variables of the data center thermodynamic model respectively, the following state space model can be obtained:

$$
\begin{array}{r} \dot {\mathbf {T}} _ {\mathrm{out}} (t) = \mathbf {A} \mathbf {T} _ {\mathrm{out}} (t) + \mathbf {B} \mathbf {u} (t) \\ \mathbf {T} _ {\mathrm{in}} (t) = \mathbf {C} \mathbf {T} _ {\mathrm{out}} (t). \end{array}
$$

(4)

where u(t ) is the input variable consisting of the rack power distribution $\mathbf { P } _ { \mathrm { r } } ( t ) ^ { \mathrm { T } } = [ P _ { \mathrm { r } , i } ( t ) ]$ and the set temperature of the CRAC unit, i.e., $\mathbf { u } ( t ) = [ \mathbf { P } _ { \mathrm { r } } ( t ) ^ { \mathrm { T } } , \mathbf { T } _ { \mathrm { s e t } } ( t ) ^ { \mathrm { T } } ]$ . Matrices A, B, C are (N+M) order matrices derived from the models Eqs. (1)-(3), and in respect to the air flow pattern (rates), time constant, and other environmental parameters.

## 2.2. The power consumption model

The power consumption of the data center is mainly focused on server operation and temperature regulation of the CRAC units. This paper assumes that all servers in the data center are active and that computing tasks received can be processed directly, thus the server power consumption can be defined as the following linear model [26]

$$
P _ {s} (t) = \mu_ {s} (t) * P _ {\max}.\tag{5}
$$

where $\mu _ { s } ( t )$ is the server resource utilization (mainly refers to CPU resources) at time t, and $P _ { \mathrm { m a x } }$ is the maximum power consumption of the server.

Then the power consumption $P _ { \mathrm { r } , i } ( t )$ of Rack i can be expressed as the sum of the power consumed by each server in Rack i such that,

$$
P _ {\mathrm{r}, i} (t) = \sum_ {s = 1} ^ {S} P _ {i, s} (t).\tag{6}
$$

where S is the number of the servers in the Rack i.

Remark 1. For simplicity and with generality, this paper assumes that all servers are homogeneous, i.e., all servers have the same task processing performance. Meanwhile, the amoun of tasks to be processed by a server is directly expressed as the amount of power to be consumed in this paper.

According to Parolini et al. [27], the power consumption model of the CRAC node j can be described as:

$$
P _ {\mathrm{c}, j} (t) = \frac {K _ {j} (T _ {\mathrm{ci} , j} (t) - T _ {\mathrm{co} , j} (t))}{C O P (T _ {\mathrm{co} , j} (t))}, T _ {\mathrm{ci}, j} (t) \geq T _ {\mathrm{co}, j} (t).\tag{7}
$$

where $K _ { j } = \rho f _ { j } C _ { \mathrm { p } }$ is a positive coefficient related to the air density $\rho _ { \mathrm { { ; } } }$ , the flow rate $f _ { j }$ of the CRAC Node j and the air heat capacity $C _ { \mathrm { p } }$ . The term $C O P ( T _ { \mathrm { c o } , j } ( t ) )$ is a conversion function with respect to the CRAC outlet temperature. This function converts the heat removal rate of a CRAC into its power consumption, indicating that increasing the outlet temperature can improve the energy efficiency of the CRAC unit [25].

## 2.3. Problem statement

The focus of this research is to reduce the cooling power consumption of the data center [26,28]. Changing server task assignments will cause changes in the server power distribution, then affect the distribution of ambient temperature in the data center according to Eqs. (1)– (4). The assignment of tasks is linear with the power assignment of the servers, thus the power assignment is directly adopted as the research object instead of the task assignment in this paper. From Eq. (7), the cooling consumption of the CRAC units is directly related to the outlet temperature which is influenced by the set temperature. Moreover, the study of [29] indicated that the temperature rise per $1 ^ { \circ } \mathrm { C }$ in the outlet temperature of CRAC units will save 4.3%–9.8% of the energy consumption. Consequently, the goal of reducing data center power consumption is to find the optimal power allocation for servers and the optimal set temperature for CRAC units.

On the other hand, the inlet temperature of the servers needs to be maintained near $2 7 ^ { \circ } \mathrm { C }$ for the safety and efficiency of server operation [4]. Hence, the inlet temperature of the servers is also a critical concern. In conclusion, due to the environmental requirements and physical constraints in data centers, the problem to be studied in this paper can be summarized as finding the optimal server power assignment and setting the temperature to minimize the power consumption of servers and CRACs, while ensuring that the server inlet temperature satisfies the operational requirements under various constraints.

Remark 2. The mathematical formulation of the studied optimization problem, which includes the cost function and constraints, is Eqs. (9)–(13) presented in the next section

## 3. Power management strategy for the data center

In practice and from Eqs. (1)–(4), the thermal model is related to the elements such as equipment time constants, ambient air density, and specific heat capacity. However, the values of the required parameters are often obtained by measurement or estimation, assumed to remain constant. The models established in this way are obviously not accurate enough to effectively reflect the real-time changes of the actual system. However, with the help of various sensors, the data such as input and output temperatures, server power consumption, and the set temperature of CRAC can be obtained in the actual data centers in real-time. Therefore, this paper investigates the power consumption problem of data centers based on the perspective of the data-driven method.

## 3.1. Preparation for data-driven predictive contro

With the selected sampling period $T _ { \mathrm { s } } ,$ , the continuous time state space model Eq. (4) can be transformed into the following discrete-time model:

$$
\begin{array}{c} \mathbf {T} _ {\text {out}} (k + 1) = \mathbf {A} _ {\text {d}} \mathbf {T} _ {\text {out}} (k) + \mathbf {B} _ {\text {d}} \mathbf {u} (k) \\ \mathbf {T} _ {\text {in}} (k + 1) = \mathbf {C} _ {\text {d}} \mathbf {T} _ {\text {out}} (k + 1). \end{array}\tag{8}
$$

where $\mathbf { T } _ { \mathrm { o u t } } ( k ) \triangleq \mathbf { T } _ { \mathrm { o u t } } ( k T _ { \mathrm { s } } )$ represents the system outlet temperature at the kth sampling period, $\mathbf { T } _ { \mathrm { i n } } ( k + 1 ) , \mathbf { u } ( k )$ are denoted similarly; Matrices $\mathbf { A } _ { \mathrm { d } } , \mathbf { B } _ { \mathrm { d } } , \mathbf { C } _ { \mathrm { d } }$ are the discrete-time model parameters associated with the sampling period $T _ { \mathrm { s } }$ . Discrete dynamics Eq. (8) will be used as the system model for data-driven predictive control algorithm.

The optimization problem is defined to maintain the server inlet temperature while min imizing the cooling power consumption by finding the optimal power distribution of racks $\hat { \mathbf { P } } _ { \mathrm { r } } ( k )$ and the setting temperature of $\mathrm { C R A } \dot { \mathrm { C s } } \ \hat { \mathbf { T } } _ { \mathrm { s e t } }$ . To describe this optimization problem, $\hat { \mathbf { P } } _ { \mathrm { c } } ,$ $\hat { \mathbf { T } } _ { \mathrm { s e t } } , \ \hat { \mathbf { P } } _ { \mathrm { r } } , \ \hat { \mathbf { T } } _ { \mathrm { r i } }$ are defined as the predicted value to represent the power consumption of the CRAC units, the set temperature of the CRAC units, the assigned power of the racks and the inlet temperature of the servers, respectively. Then the following cost function can be derived:

$$
\min _ {\hat {\mathbf {P}} _ {\mathrm{r}} (k), \hat {\mathbf {T}} _ {\mathrm{set}} (k)} \sum_ {k = 1} ^ {N _ {\mathrm{f}}} (\hat {\mathbf {T}} _ {\mathrm{ri}} (k) - \mathbf {T} _ {\mathrm{ref}}) ^ {\mathrm{T}} \mathbf {Q} (\hat {\mathbf {T}} _ {\mathrm{ri}} (k) - \mathbf {T} _ {\mathrm{ref}}) + \hat {\mathbf {P}} _ {\mathrm{c}} ^ {\mathrm{T}} \mathbf {R} \hat {\mathbf {P}} _ {\mathrm{c}}\tag{9}
$$

s. t. for system dynamics Eqs. (1) − (7),

$$
0 \leq \hat {\mathbf {P}} _ {\mathrm{r}} (k) \leq \overline {{\mathbf {P}}} _ {\mathrm{r}},\tag{10}
$$

$$
\underline {{\mathbf {T}}} _ {\mathrm{set}} \leq \hat {\mathbf {T}} _ {\mathrm{set}} (k) \leq \overline {{\mathbf {T}}} _ {\mathrm{set}},\tag{11}
$$

$$
\underline {{\mathbf {T}}} _ {\mathrm{ri}} \leq \hat {\mathbf {T}} _ {\mathrm{ri}} (k) \leq \overline {{\mathbf {T}}} _ {\mathrm{ri}},\tag{12}
$$

$$
\| \hat {\mathbf {P}} _ {\mathrm{r}} (k) \| _ {1} = P _ {\text { tot }} (k),\tag{13}
$$

where $N _ { \mathrm { f } }$ is the future optimization horizon, $\mathbf { T } _ { \mathrm { r e f } } = 2 7 ^ { \circ } \mathbf { C }$ is the safety operation temperature of inlet air of the servers, and Q, R are given positive definite weighting matrices.

Constraints Eqs. (10) and (11) are the input constraints of the system, where $\overline { { \mathbf { P } } } _ { \mathrm { r } }$ is the maximum processable power of each rack, and $\underline { { \mathbf { T } } } _ { \mathrm { s e t } } , \overline { { \mathbf { T } } } _ { \mathrm { s e t } }$ are the lower and upper bound of the set temperature of the CRAC unit. Constraint Eq. (12) is the guarantee that the server can run safely and efficiently, and the range of $( \underline { { \mathbf { T } } } _ { \mathrm { i } } - \overline { { \mathbf { T } } } _ { \mathrm { r i } } )$ is the optimal operating range of the server. Constraint Eq. (13) is set based on the server tasks, where $P _ { t o t } ( k )$ is the total power of server racks needed by all computational tasks, i.e., constraint Eq. (13) requires that all computational tasks have to be completed.

## 3.2. Simulation experiment with the subspace predictive control algorithm

In order to solve the above optimal control problem without the model parameters, the data-driven subspace predictive control (SPC) algorithm is adopted. The core of the SPC algorithm is to use the historical data of the system to obtain an output predictor that reflects the input-output mapping relationship of the system, and then solves for the optimal control sequence $\mathbf { u } _ { \mathrm { f } } ,$ where $\mathbf { u } _ { \mathrm { f } } = \hat { \mathbf { P } } _ { \mathrm { r } } ( k )$ based on $\operatorname { E q . } \ ( 9 )$ in this paper.

The SPC algorithm is operated based on the collected system data, which construct the data matrix as follows:

$$
\begin{array}{l} \boldsymbol {\Omega} _ {k _ {1}} ^ {k _ {2}} = \left[ \begin{array}{c c c c} \omega (k _ {1}) & \omega (k _ {1} + 1) & \dots & \omega (k _ {1} + v - 1) \\ \omega (k _ {1} + 1) & \omega (k _ {1} + 2) & \dots & \omega (k _ {1} + v) \\ \vdots & \vdots & \ddots & \vdots \\ \omega (k _ {1} + z - 1) & \omega (k _ {1} + z) & \dots & \omega (k _ {2}) \end{array} \right] \\ \triangleq \left[ \begin{array}{c c c c} \boldsymbol {\omega} ^ {z} (k _ {1}) & \boldsymbol {\omega} ^ {z} (k _ {1} + 1) & \dots & \boldsymbol {\omega} ^ {z} (k _ {1} + v - 1) \end{array} \right] \in \mathbb {R} ^ {\zeta z \times v} \end{array}
$$

where $\Omega _ { k _ { 1 } } ^ { k _ { 2 } }$ denotes the data matrix from the time instant $k _ { 1 }$ to the time instant $k _ { 2 } ; \omega ^ { z } ( k ) \triangleq$ $\left[ \omega ^ { \mathrm { T } } ( k ) , \omega ^ { \mathrm { T } } ( k + 1 ) , \cdots , \omega ^ { \mathrm { T } } ( k + z - 1 ) \right] ^ { \mathrm { T } } \in \mathbb { R } ^ { \xi z }$ is the data sequence consisting of data ele ments $\omega ( k )$ , and $z , \ \nu$ are integers with $z \ll \nu .$

Then the specific implementation of SPC algorithm on the thermal control of data centers is displayed as follows:

<sub>•</sub> Step 1: At the beginning of kth interval, collect enough historical data (at least $z + \nu$ output and input of the state-space model) and construct the data matrix as above such that,

<sub>•</sub> Past interval node inlet temperature

$$
\mathbf {T} _ {\text { in }} ^ {\mathrm{p}} \triangleq \mathbf {T} _ {- j} ^ {J} = \left[ \begin{array}{c c c c} \mathbf {T} _ {\text { in }} ^ {z} (- j) & \mathbf {T} _ {\text { in }} ^ {z} (- j + 1) & \dots & \mathbf {T} _ {\text { in }} ^ {z} (- 2 J + 1) \end{array} \right],
$$

<sub>•</sub> Future interval node inlet temperature

$$
\mathbf {T} _ {\text { in }} ^ {\mathrm{f}} \triangleq \mathbf {T} _ {- j + J} ^ {0} = \left[ \begin{array}{c c c c} \mathbf {T} _ {\text { in }} ^ {z} (- j + J) & \mathbf {T} _ {\text { in }} ^ {z} (- j + J + 1) & \dots & \mathbf {T} _ {\text { in }} ^ {z} (- J + 1) \end{array} \right],
$$

<sub>•</sub> Past interval system input variables

$$
\mathbf {U} ^ {\mathrm{p}} \triangleq \mathbf {U} _ {- j} ^ {- J} = \left[ \begin{array}{c c c c} \mathbf {u} ^ {z} (- j) & \mathbf {u} ^ {z} (- j + 1) & \dots & \mathbf {u} ^ {z} (- 2 J + 1) \end{array} \right],
$$

<sub>•</sub> Future interval system input variables

$$
\mathbf {U} ^ {\mathrm{f}} \triangleq \mathbf {U} _ {- j + J} ^ {0} = \left[ \begin{array}{c c c c} \mathbf {u} ^ {z} (- j + J) & \mathbf {u} ^ {z} (- j + J + 1) & \dots & \mathbf {u} ^ {z} (- J + 1) \end{array} \right],
$$

Step 2: Make the QR decomposition of the following data equation, where $\mathbf { W } ^ { \mathrm { p } } \triangleq [ \mathbf { T } _ { \mathrm { i n } } ^ { \mathrm { p } } \ \mathbf { U } ^ { \mathrm { p } } ] ^ { \mathrm { T } }$

$$
\left[ \begin{array}{c} \mathbf {W} ^ {\mathrm{p}} \\ \mathbf {U} ^ {\mathrm{f}} \\ \mathbf {T} _ {\text {in}} ^ {\mathrm{f}} \end{array} \right] = \left[ \begin{array}{c c c} \mathbf {R} _ {1 1} & 0 & 0 \\ \mathbf {R} _ {2 1} & \mathbf {R} _ {2 2} & 0 \\ \mathbf {R} _ {3 1} & \mathbf {R} _ {3 2} & \mathbf {R} _ {3 3} \end{array} \right] \left[ \begin{array}{c} \mathbf {Q} _ {1} ^ {\mathrm{T}} \\ \mathbf {Q} _ {2} ^ {\mathrm{T}} \\ \mathbf {Q} _ {3} ^ {\mathrm{T}} \end{array} \right]
$$

and then obtain the key parameters $\mathbf { L } _ { \mathrm { w } }$ and $\mathbf { L } _ { \mathrm { u } }$ of the output predictor, where $[ * ] ^ { \dagger }$ denote the pseudo-inverse of matrix [∗].

$$
\mathbf {L} = \left[ \begin{array}{c c} \mathbf {R} _ {3 1} & \mathbf {R} _ {3 2} \end{array} \right] \left[ \begin{array}{c c} \mathbf {R} _ {1 1} & 0 \\ \mathbf {R} _ {2 1} & \mathbf {R} _ {2 2} \end{array} \right] ^ {\dagger}
$$

<sub>•</sub> Step 3: Collect the latest inlet temperature of thermal nodes and system inputs into the vector,

$$
\mathbf {w} _ {\mathrm{p}} = \left[ \mathbf {T} _ {i n} ^ {\mathrm{T}} (- J + 1), \dots , \quad \mathbf {T} _ {i n} ^ {\mathrm{T}} (- 1), \mathbf {T} _ {i n} ^ {\mathrm{T}} (0) \quad | \quad \mathbf {u} ^ {\mathrm{T}} (- J + 1), \dots , \mathbf {u} ^ {T} \mathrm{T} (- 1), \mathbf {u} ^ {\mathrm{T}} (0) \right] ^ {\mathrm{T}}
$$

and then based on the parameters $\mathbf { L } _ { \mathrm { w } }$ and $\mathbf { L } _ { \mathrm { u } }$ obtained in Step 2, the desired predictor can be obtained as:

$$
\hat {\mathbf {T}} _ {\mathrm{ri}} = \mathbf {L} _ {\mathrm{w}} \mathbf {w} _ {\mathrm{p}} + \mathbf {L} _ {\mathrm{u}} \mathbf {u} _ {\mathrm{f}}
$$

![](li2023_subspace_mpc_air_cooled_assets/images/58f1e69df73167c58855baadebaedd9c3bd9f91bbff762a64acdcc03e3974391.jpg)  
Fig. 3. Schematic diagram of data center layout.

Table 2  
Parameters of the control algorithm.

<table><tr><td>Notations</td><td>Values</td><td>Units</td></tr><tr><td>N</td><td>4</td><td>-</td></tr><tr><td>M</td><td>1</td><td>-</td></tr><tr><td> $N_{\text{f}}$ </td><td>20</td><td>-</td></tr><tr><td> $T_{\text{s}}$ </td><td>15</td><td>s</td></tr><tr><td> $\overline{P}_{\text{r}}$ </td><td>12</td><td>Kw</td></tr><tr><td> $\overline{T}_{\text{set}}$ </td><td>27</td><td>°C</td></tr><tr><td> $\underline{T}_{\text{set}}$ </td><td>10</td><td>°C</td></tr><tr><td>Q</td><td>100×I</td><td>-</td></tr><tr><td>R</td><td> $10^{-4} \times I$ </td><td>-</td></tr></table>

<sub>•</sub> Step 4: Substitute predictor in Step 3 into the cost function Eq. (9) in the paper to solve for the optimal control sequence u , and feed the first component of the sequence $\mathbf { u } _ { \mathrm { f } }$ into the system. Then, at the beginning of $k + 1$ th time interval, collect the update hirstorical data and repeat Step 1–4.

A small air-cooled data center as shown in Fig. 3 is studied in this paper, which contains N = 4 server racks (2 in Row A and 2 in Row B) and M = 1 CRAC unit. There are 3 racks without arrows are contained with communication devices, which are not considered as main power consumers in the computer room. The parameters of the data center environment and equipment are given in Table 2.

The historical data were collected with 15 s as a sampling period and the required data matrix is constructed by 2020 sets of collected historical operation data (nearly 9 h), including the power of the rack, the inlet/outlet temperature of the thermal nodes and so on. An additional 480 data sets (2 h) of total rack power shown in Fig. 4 were used for conducting simulation tests, which is the power to be distributed to each server racks. The prediction horizon is set as 20 steps for each optimization.

![](li2023_subspace_mpc_air_cooled_assets/images/cfdda4de8175188c1c6d30a88f10e7142917728ff175b98a4d9826387b134e5a.jpg)  
Fig. 4. The server rack power variation in the data center.

Table 3  
The comparison of energy consumption and PUE.

<table><tr><td rowspan="2">Methods</td><td colspan="3">Energy Consumption (kWh)</td><td rowspan="2">PUE</td></tr><tr><td>Server</td><td>CRAC</td><td>Total</td></tr><tr><td>SPC</td><td>18.73</td><td>3.27</td><td>22.00</td><td>1.175</td></tr><tr><td>Opti- $T_{in}$ -Only</td><td>18.73</td><td>3.95</td><td>22.68</td><td>1.211</td></tr><tr><td>No-Task-Assi</td><td>18.73</td><td>4.31</td><td>23.04</td><td>1.230</td></tr><tr><td>Fixed- $T_{set}$ </td><td>18.73</td><td>4.92</td><td>23.65</td><td>1.263</td></tr></table>

To verify the effectiveness of the proposed SPC algorithm, the following three control strategies are used for comparison.

1. Opti- $\cdot T _ { \mathrm { i n } } – O n l y$ Method: This method can adjust the power assignment and the CRAC setting temperature to maintain a safe environment, but only the inlet temperature of the server racks are considered in the cost function, i.e., the function Eq. (9) is changed to

$$
\min \sum_ {k = 1} ^ {N _ {\mathrm{f}}} (\hat {\mathbf {T}} _ {\mathrm{ri}} (k) - \mathbf {T} _ {\mathrm{ref}}) ^ {\mathrm{T}} \mathbf {Q} (\hat {\mathbf {T}} _ {\mathrm{ri}} (k) - \mathbf {T} _ {\mathrm{ref}}).
$$

2. No-Task-Assi Method: This method distributes tasks to each rack evenly. The power of each rack are equal to each other in the simulation.

3. $F i x e d – T _ { \mathrm { s e t } }$ Method: In this method, the setting temperature of the CRAC unit is fixed at a constant value and keeps the safe operation of the servers.

The optimization problem in this paper is solved by the sequential quadratic programming (SQP) method, implemented by the function fmincon in the optimization toolbox of Matlab R2017a software.

## 3.3. Results and discussions

The data center energy consumption under different control strategies is illustrated in Table 3. The PUE is a metric used to evaluate the power usage efficiency of a data center, which represents the ratio of the total energy used by the data center to the energy provided to its information processing equipment, i.e., $\mathit { P U E } = 1 + \parallel \mathbf { P } _ { \mathrm { c } } \parallel _ { 1 } / \parallel \mathbf { P } _ { \mathrm { r } } \parallel _ { 1 }$ . Smaller values of the PUE indicate that the power is being used more efficiently.

![](li2023_subspace_mpc_air_cooled_assets/images/5476086fcb27c37abfea880d2d550741396b16df19498362611fc97c19e0d455.jpg)  
(a) Rack inlet temperature by $\mathrm { O p t i - } T _ { \mathrm { i n } } \mathrm { - } \mathrm { O n l y }$

![](li2023_subspace_mpc_air_cooled_assets/images/f1bed0e568e39320d1a6b2fa5cb490f2a23c8517c80dca6d629aeafe40054c64.jpg)  
(b) Rack inlet temperature by SPC

Fig. 5. Comparison of rack inlet temperature by $\mathrm { O p t i - } T _ { \mathrm { i n } } \mathrm { - O n l y }$ and SPC.  
![](li2023_subspace_mpc_air_cooled_assets/images/12a3fb2b97e013b45abb940ba22662c330694a72ccc5565625ad3430bbb913a2.jpg)  
Fig. 6. Comparison of total power consumption by $\mathrm { O p t i - } T _ { \mathrm { i n } } \mathrm { - } \mathrm { O n l y }$ and SPC.

Table 3 shows that the energy consumption of the SPC is the least, and the PUE of the SPC strategy is the best. It is worth noting that server consumption for all strategies is the same in Table 3, which is because we just take the distribution of rack power into consideration and the total amount of power of racks is equal to the test data.

The results in Figs. 5 and 6 compare the SPC method with different cost functions. When only the inlet temperature of the server racks is considered, the SPC algorithm is able to ensure that the inlet temperature of all racks is close to the reference temperature $( 2 7 ^ { \circ } \mathrm { C } )$ as shown in Fig. 5(a). When both the inlet temperature of server racks and the cooling power consumption are considered, the SPC algorithm can effectively reduce the data center power consumption while ensuring the safe operation of the servers (the deviation of the inlet temperature is less than $2 ^ { \circ } \mathrm { C }$ as shown in Fig. 5(b)). The data in Table 3 and Fig. 6 indicate that the SPC method can achieve about 17% of the cooling power reduction when taking the cooling power and inlet temperature simultaneously into the optimization.

![](li2023_subspace_mpc_air_cooled_assets/images/fbb5bf2b40341b97d6718e9fa4112ebe0add3c86eeb8ae0ee214f4d765a782f4.jpg)

(a) Average resource utilization (linear to power usage) of server racks  
![](li2023_subspace_mpc_air_cooled_assets/images/a82a0b501cd3eab18f49320704c3c539e4a5af1e058c4404a85d55257d80e987.jpg)  
(b) CRAC unit setting temperature

Fig. 7. Comparison of 4 control strategies.  
![](li2023_subspace_mpc_air_cooled_assets/images/35a0595a073b7229308f52f3af4cc9160527731ec5d8d78f68b85f2325924044.jpg)  
Fig. 8. Maximum rack inlet temperature of 4 control strategies.

The comparisons of all the four control strategies are shown in Fig. 7. The CRAC set temperature is the main control variable in the $N o { - } T a s k { - } A s s i$ Method method, and the Fixed-$T _ { \mathrm { s e t } }$ Method only takes power allocation into consideration. Figure 7(a)) shows that both the $S P C$ method and $O p t i  – T _ { \mathrm { i n } } – O n l y$ method prioritize the power allocation to rack 3 and rack 4, due to the fact that racks 3 and 4 are closer to the CRAC unit. The No-Task-Assi method distributes the power equally. Comparing the results of SPC and No-Task-Assi Method, it indicates that uneven power load allocation can reduce the heat recirculation between racks and improve the cooling efficiency. By setting the temperature CRAC higher, the cooling power of the SPC is 24.1% less than the $N o - T a s k { - } A s s i$

The $F i x e d – T _ { \mathrm { s e t } }$ Method sets CRAC temperature at a relatively low value, which means the data center is overcooled. In this situation, the optimizer of the $F i x e d – T _ { \mathrm { s e t } }$ Method intends to make the rack inlet temperature much closer to the reference temperature, hence allocating more power to rack 2 and rack 1 which are far away from the CRAC unit.

Figure 8 illustrates the maximum inlet temperature of the server racks under four control strategies. All the methods except the $N o { - } T a s k { - } A s s i$ keep the inlet temperature of the server racks close to the reference temperature $( 2 7 ^ { \circ } \mathrm { C } )$ with no more than $2 ^ { \circ } \mathrm { C }$ difference. Hence, the uneven power allocation can help keeping the inlet temperature target.

![](li2023_subspace_mpc_air_cooled_assets/images/83ba596ac3c3394e6be832597cbfe2c4abef2f3cacfbadd2e5da629178334ca7.jpg)  
Fig. 9. Calculation time of the SPC algorithm at each step.

Based on the above results, the SPC algorithm proposed in this paper can effectively reduce the cooling power consumption of the data center while ensuring the safe operation of the servers. However, there is still a key problem to be studied. As a typical predictive control algorithm, the SPC algorithm still requires a receding horizon optimization approach to ensure the stability of the system, i.e., only the first component of the optimized control sequence is fed into the system at each moment. As a data-driven method, the SPC method needs to construct a predictor and then solve the optimization problem at each step, which takes considerable time as shown in Fig. 9. The result shows that each calculation of the SPC algorithm takes about 7 s on average, and even more than 10 s at maximum. However, the length of the discrete-time is only 15 s, which is unacceptable for the receding horizon control.

## 4. Power management strategy by the event-triggered SPC method

## 4.1. The event-triggered mechanism for SPC

Based on the simulation and discussion in the above section, the low computation efficiency of SPC hinders its practical application despite of its verified performance. Therefore, to alleviate the heavy data computation and transmission load, an event-triggered mechanism is adopted to improve the feasibility of the SPC method. The event-triggered mechanism was initiated from reducing the network transmission load, however, it has been applied to many fields with development due to its effectiveness [30,31]. Since the system is fully controllable, there exists a state-feedback gain to stabilize the system. Then the event-triggered mechanism can be established by checking the error between each predictive input component and the theoretically existing stable control input. If the error is within some defined certain bound, the corresponding input component can be proved stable based on the robust control theory and directly fed into the system; otherwise, the SPC input sequence $\mathbf { u } _ { \mathrm { f } }$ should be updated with the newly collected data. The corresponding system diagram is illustrated in Fig. 10.

Based on the above ideology and the mechanism in Fig. 10, the stable feedback gain $\mathbf { K } _ { \mathrm { s } }$ is the key parameter for the event-triggered law. Without the definite system internal model, however, a data-driven method is adopted to derive the robust stable state-feedback gain $\mathbf { K } _ { \mathrm { s } }$ via Lemma 1 [32].

Lemma 1. If there exist $\mathbf { X } \succ 0 , \ \mathbf { G } \in \mathbb { R } ^ { N \times n }$ such that

$$
\mathbf {X G} = \mathbf {I}\tag{14}
$$

![](li2023_subspace_mpc_air_cooled_assets/images/86aebfaa6bfb66e3688e233ced8225ce85d0f22cd7b9b26119a828c21d521118.jpg)  
Fig. 10. Diagram of the event-triggered SPC system

as well as

$$
\left[ \begin{array}{c c} * & * \\ * & * \\ * & * \\ * & * \end{array} \right] ^ {T} \left[ \begin{array}{c c c c} - \mathbf {X} & 0 & 0 & 0 \\ 0 & \mathbf {X} & 0 & 0 \\ 0 & 0 & \mathbf {Q} _ {\mathrm{w}} & \mathbf {S} _ {\mathrm{w}} \\ 0 & 0 & \mathbf {S} _ {\mathrm{w}} ^ {\mathrm{T}} & \mathbf {R} _ {\mathrm{w}} \end{array} \right] \left[ \begin{array}{c c} \mathbf {I} & 0 \\ \mathbf {X} _ {+} \mathbf {G} & \mathbf {B} _ {\mathrm{d}} \\ 0 & \mathbf {I} \\ - \mathbf {G} & 0 \end{array} \right] \prec 0\tag{15}
$$

then the state-feedback gain ${ \bf K } _ { \mathrm { s } } = { \bf U } { \bf G }$ is stable for the system with bounded disturbance. The parameters ${ \bf Q } _ { \mathrm { w } } = - { \bf I } , ~ { \bf S } _ { \mathrm { w } } = 0 , ~ { \bf R } _ { \mathrm { w } } = \bar { \omega } ^ { 2 } { \bf I }$ are some known matrices, and the $\mathbf { X } , \mathbf { X } _ { + }$ are the data matrices made by the system historical data.

Therefore, Lemma 1 provides a feasible condition for designing a stable state-feedback gain based on the system data. The system robust stability with bounded disturbance is ensured if ${ \bf K } _ { s }$ can be derived. By performing the congruence transformation with $\operatorname { d i a g } ( \mathbf { X } ^ { - 1 }$ I) and the Schur complement theorem [33,34], the nonlinear matrix inequality Eq. (15) can be transformed to the following linear matrix inequality (LMI):

$$
\left[ \begin{array}{c c c c} - \mathbf {X} ^ {- 1} & - \mathbf {H} ^ {\mathrm{T}} \mathbf {S} _ {\mathrm{w}} ^ {\mathrm{T}} & \mathbf {H} ^ {\mathrm{T}} \mathbf {X} _ {+} ^ {\mathrm{T}} & \mathbf {H} ^ {\mathrm{T}} \\ - \mathbf {S} _ {\mathrm{w}} \mathbf {H} & \mathbf {Q} _ {\mathrm{w}} & \mathbf {B} _ {\mathrm{d}} ^ {\mathrm{T}} & 0 \\ \mathbf {X} _ {+} \mathbf {H} & \mathbf {B} _ {\mathrm{d}} & - \mathbf {X} ^ {- 1} & 0 \\ \mathbf {H} & 0 & 0 & - \mathbf {R} _ {\mathrm{w}} ^ {- 1} \end{array} \right] \prec 0\tag{16}
$$

where $\mathbf H = \mathbf G \mathbf X ^ { - 1 }$ is the notation defined to simplify the LMI. Then, the stabilizing statefeedback gain can be derived as

$$
\mathbf {K} _ {\mathrm{s}} = \mathbf {U H X}\tag{17}
$$

Remark 3. With the pre-defined error bound $\bar { \omega }$ and the system data, as long as there exists a feasible solution for the LMI Eq. (16), $\mathbf { K } _ { \mathrm { s } }$ can stabilize the closed-loop system as the robust control state-feedback gain with bounded input disturbance.

Table 4  
The computation efficiency comparison of the SPC methods.

<table><tr><td>Item</td><td>SPC</td><td>ET-SPC</td></tr><tr><td>Event-triggered times</td><td>240/240</td><td>31/240</td></tr><tr><td>Average computation time (sec)</td><td>6.65</td><td>7.18</td></tr><tr><td>Maximum computation time (sec)</td><td>10.30</td><td>10.07</td></tr><tr><td>Simulation time (sec)</td><td>1602.6</td><td>222.49</td></tr><tr><td>Saved time (sec)</td><td colspan="2">1380</td></tr></table>

## 4.2. The design of the event-triggered law

With the derived state-feedback gain $\mathbf { K } _ { \mathrm { s } }$ for a certain bound $\bar { \omega } ,$ , the following event-triggered law is proposed with the defined input deviation e(k).

$$
\mathbf {e} (k) = \mathbf {u} _ {\mathrm{f}} (k) - \mathbf {K} _ {\mathrm{s}} \mathbf {x} (k) \leq \bar {\omega}\tag{18}
$$

where $\mathbf { u } _ { \mathrm { f } } ( k )$ indicates the kth component of the SPC input sequence $\mathbf { u } _ { \mathrm { f } } , \mathbf { x } ( k )$ represents the state variable in the state space model, which is $\mathbf { T } _ { \mathrm { o u t } } ( k )$ in this paper.

Then with each predictive input component $\mathbf { u } _ { \mathrm { f } } ( k )$ , the following equation can be derived for the discrete-time state-space realization of Eq. (8) such that,

$$
\begin{array}{r l} \mathbf {x} (k + 1) & = \mathbf {A} _ {\mathrm{d}} \mathbf {x} (k) + \mathbf {B} _ {\mathrm{d}} \mathbf {u} _ {\mathrm{f}} (k) \\ & = (\mathbf {A} _ {\mathrm{d}} + \mathbf {B} _ {\mathrm{d}} \mathbf {K} _ {\mathrm{s}}) \mathbf {x} (k) + \mathbf {B} _ {\mathrm{d}} \mathbf {e} (k) \end{array}\tag{19}
$$

By taking the input error $\mathbf { e } ( k )$ as the input disturbance, the design of the event-triggered law of Eq. (18) can be transformed into solving for the robust state-feedback gain of $\mathbf { K } _ { \mathrm { s } }$ by Lemma 1 in Section 4.1. Meanwhile, the guaranteed stability of the integrated event-triggered SPC method with $\mathbf { K } _ { \mathrm { s } }$ is verified by the robust stability theory, which can be referred to [32] for details.

Remark 4. Particularly, the event-triggered law is developed for the discrete-time system with constant sampling time, which prevents it from the Zeno phenomenon.

## 4.3. Simulation and analysis of the event-triggered SPC in power management

In this part, the proposed event-triggered SPC method is applied to the power management of the data center. The experimental objective and condition are set as the same as in Section III-C. The event-trigger condition is set to be $\pm 1 . 5 ^ { \circ } \mathrm { C }$ of the inlet temperature allowing for the rack operational safety. In Fig. 11, it reveals that the inlet temperature of each rack is maintained within the range of $\pm 1 . 5 ^ { \circ } \mathrm { C } .$ , and the power distribution also gives priority to Racks 3 and 4, indicating that the SPC algorithm with the event-triggered law can still effectively reduce the cooling power while ensuring the safe operation.

Figure 12 illustrates the event-triggered time instant of the proposed method during simulation. Moreover, the computational efficiency and control effect are compared with and without the event-triggered law via Table 4.

As shown in Fig. 12, the event-triggered law is only triggered 31 times within 240 iterations, which is greatly reduced by 87% compared with the horizon receding optimization of the conventional SPC method. The triggering time instants also include the mandatory triggering due to the SPC algorithm with the predictive input horizon f = 20 in Eq. (9), which lead to the periodic triggering in Fig. 12. The average and maximum calculation time of each optimization iteration almost remain the same after introducing the event-triggered law, indicating that the event-triggered law will not bring an additional burden to the optimization. However, within the 240-step iterations, 1380 s of the calculation time have been saved after adding the event-triggered law, which is reduced by about 86%. Therefore, it can be concluded that the proposed event-triggered SPC method has greater computational efficiency and saves considerable computational resources.

![](li2023_subspace_mpc_air_cooled_assets/images/a946fd2cd94d4d13bdd70eaafaca296a4c10a5f4b9efbc66931f8726e78749d6.jpg)  
(a) Inlet temperature of each rack

![](li2023_subspace_mpc_air_cooled_assets/images/901676ee122d6a3a5d6d4a63e70951fdfdd7fa3c04806dc09cf1891123627665.jpg)  
(b) Power allocation of each rack

Fig. 11. Performance of the event-triggered SPC method in power management.  
![](li2023_subspace_mpc_air_cooled_assets/images/b8406ef9b9d38c7c47be53d4b646d50410e823f5badb4d50f2119b91dd03a7f0.jpg)  
Fig. 12. The event-triggered time instant of the proposed method.

On the other hand, the control performance can be analyzed in terms of the inlet temperature and energy consumption, which is illustrated in Table 5. The slight difference of the average inlet temperature on each rack (about 0.2<sup>◦</sup>C) indicates that the algorithm after adding the event-triggered law can still ensure the safe operation of the server. Similarly for the energy consumption, the event-triggered SPC has almost the same performance as the conventional method, with only 0.04 kWh difference in the 240-step (1 h) calculation.

Above all, the proposed event-triggered SPC can improve the computational efficiency by over 80% while maintaining the control performance. Thus, the event-triggered method can considerably reduce the data transmission and computation load and satisfy the control performance for the data center, which is more applicable in practice.

Table 5  
The control performance comparison of the SPC methods.

<table><tr><td>Item</td><td></td><td>SPC</td><td>ET-SPC</td></tr><tr><td colspan="2">Maximum inlet temperature (°C)</td><td>29.79</td><td>29.56</td></tr><tr><td colspan="2">Energy Consumption (kWh)</td><td>22.00</td><td>21.96</td></tr><tr><td colspan="2">PUE</td><td>1.175</td><td>1.172</td></tr><tr><td rowspan="4">The average inlet temperature difference (°C)</td><td>Rack 1</td><td>0.15</td><td></td></tr><tr><td>Rack 2</td><td>0.17</td><td></td></tr><tr><td>Rack 3</td><td>0.22</td><td></td></tr><tr><td>Rack 4</td><td>0.17</td><td></td></tr></table>

## 5. Conclusion

In this paper, a data-driven subspace predictive control method is developed to solve the energy consumption management problem in the case of unknown data center model parameters. The method provides the optimal server power allocation scheme and the setting temperature of CRAC units based on the measurable historical data, which minimizes the cooling power consumption of the data center. In addition, to improve the computational efficiency of the algorithm, an event-triggered law is designed to replace the typical receding horizon optimization of the subspace predictive control method. The simulation results with comparisons illustrate the satisfactory control performance and improved computational efficiency, which provides prospects for its practical applications.

## Declaration of Competing Interest

The authors declare that they have no known competing financial interests or personal relationships that could have appeared to influence the work reported in this paper.

## CRediT authorship contribution statement

Zhe Li: Conceptualization, Methodology. Haoda Wang: Software, Data curation, Valida tion, Writing – original draft. Qiu Fang: Methodology, Investigation, Writing – review & editing. Yaonan Wang: Supervision.

## Acknowledgments

This work was supported in part by the Major Project of Science and Technology Innovation 2030 under grant No. 2021ZD0113100, the National Natural Science Foundation of China under Grant No. (61903132, 61903134), and by the Special Funding Support for the Construction of Innovative Province of Hunan under Grant No. (2021GK1010, 2020JJ5086).

## References

[1] M. Chen, C. Gao, M. Song, S. Chen, D. Li, Q. Liu, Internet data centers participating in demand response: a comprehensive review, Renew. Sust. Energy Rev. 117 (2020) 109466.

[2] H. Rong, H. Zhang, S. Xiao, C. Li, C. Hu, Optimizing energy consumption for data centers, Renew. Sust. Energy Rev. 58 (2016) 674–691.

[3] A. Shehabi, S. Smith, D. Sartor, R. Brown, M. Herrlin, J. Koomey, E. Masanet, N. Horner, I. Azevedo, W. Lintner, United States Data Center Energy Usage Report(2016).

[4] ASHRAE TC 9.9, Thermal guidelines for data processing environments-expanded data center classes and usage guidance, Whitepaper by ASHRAE TC, 2011.

[5] J. Athavale, M. Yoda, Y. Joshi, Thermal modeling of data centers for control and energy usage optimization, in: Adv. Heat Trans., Vol. 50, Elsevier, 2018, pp. 123–186.

[6] Q. Fang, J. Wang, Q. Gong, Qos-driven power management of data centers via model predictive control, IEEE Trans. Autom. Sci. Eng. 13 (4) (2016) 1557–1566.

[7] M.M. Toulouse, G. Doljac, V.P. Carey, C. Bash, Exploration of a potential-flow-based compact model of air-flow transport in data centers, in: ASME 2009 International Mech. Engi. Congress and Expo., American Society of Mechanical Engineers, 2009, pp. 41–50.

[8] R. Sharma, C. Bash, C. Patel, Dimensionless parameters for evaluation of thermal design and performance of large-scale data centers, in: 8th AIAA/ASME Joint Thermophysics and Heat Transfer Conference, 2002, p. 3091.

[9] M.K. Herrlin, Improved data center energy efficiency and thermal performance by advanced airflow analysis, in: Digital Power Forum, 2007, pp. 10–12.

[10] Q. Tang, T. Mukherjee, S.K. Gupta, P. Cayton, Sensor-based fast thermal evaluation model for energy efficient high-performance datacenters, in: 4th International Conf. ISIP, IEEE, 2006, pp. 203–208.

[11] L. Parolini, B. Sinopoli, B.H. Krogh, Reducing data center energy consumption via coordinated cooling and load management, in: Proc. of the 2008 Conf. on PACSH, Vol. 8, 2008. 14–14

[12] Q. Fang, J. Wang, Q. Gong, M. Song, Thermal-aware energy management of HPC data center via two-time-scale control, IEEE Trans. Ind. Inform. 13 (5) (2017) 2260– 2269.

[13] T. Mukherjee, A. Banerjee, G. Varsamopoulos, S.K. Gupta, Model-driven coordinated management of data centers, Comput. Netw. 54 (16) (2010) 2869–2886.

[14] M. Song, K. Chen, J. Wang, Numerical study on the optimized control of CRACs in a data center based on a fast temperature-predicting model, J. Energy Eng. 143 (5) (2017) 04017041.1–04017041.8.

[15] M. Demirci, A survey of machine learning applications for energy-efficient resource management in cloud computing environments, in: 14th Inter. Conf. ICMLA, IEEE, 2015, pp. 1185–1190.

[16] X.Z. Jin, G.H. Yang, W.W. Che, Adaptive pinning control of deteriorated nonlinear coupling networks with circuit realization, IEEE Trans. Neur. Net. Lear. 23 (9) (2012) 1345–1355.

[17] D. Yi, X. Zhou, Y. Wen, R. Tan, Efficient compute-intensive job allocation in data centers via deep reinforcement learning, IEEE Trans. Parall. Distr. 31 (6) (2020) 1474–1485.

[18] S. MirhoseiniNejad, F.M. García, G. Badawy, D.G. Down, ALTM: adaptive learning-based thermal model for temperature predictions in data centers, in: 2019 IEEE-StICT, IEEE, 2019, pp. 1–6.

[19] Q. Fang, Z. Li, Y. Wang, M. Song, J. Wang, A neural-network enhanced modeling method for real-time evaluation of the temperature distribution in a data center, Neural Comput. Appl. 31 (12) (2019) 8379–8391.

[20] S.J. Qin, An overview of subspace identification, Comput. Chem. Eng. 30 (10–12) (2006) 1502–1513.

[21] G. Van der Veen, J.-W. van Wingerden, M. Bergamasco, M. Lovera, M. Verhaegen, Closed-loop subspace identification methods: an overview, IET Control Theory A 7 (10) (2013) 1339–1358

[22] W. Favorell, B. De Moor, M. Gevers, SPC: subspace predictive control, IFAC Proc. Vol. 32 (2) (1999) 4004–4009.

[23] Z. Li, X. Yuan, Y. Wang, C.-H. Xie, Subspace predictive control with the data-driven event-triggered law for linear time-invariant systems, J. Franklin I. 356 (15) (2019) 8167–8181.

[24] X.-S. Luo, Y.-D. Song, Data-driven predictive control of Hammerstein–Wiener systems based on subspace identification, Inform. Sci. 422 (2018) 447–461.

[25] J.D. Moore, J.S. Chase, P. Ranganathan, R.K. Sharma, Making scheduling “cool”: Temperature-aware workload placement in data centers, in: USENIX Annual Tech. Conf., General Track, 2005, pp. 61–75

[26] A. Hameed, A. Khoshkbarforoushha, R. Ranjan, P.P. Jayaraman, J. Kolodziej, P. Balaji, S. Zeadally, Q.M. Malluhi, N. Tziritas, A. Vishnu, et al., A survey and taxonomy on energy efficient resource allocation techniques for cloud computing systems, Computing 98 (7) (2016) 751–774.

[27] L. Parolini, B. Sinopoli, B.H. Krogh, Z. Wang, A cyber–physical systems approach to data center modeling and control for energy efficiency, Proc. IEEE 100 (1) (2012) 254–268.

[28] H. Sun, P. Stolf, J.-M. Pierson, Spatio-temporal thermal-aware scheduling for homogeneous high-performance computing datacenters, Future Gener, Comp. Syst. 71 (2017) 157–170

[29] N. Wang, J. Zhang, X. Xia, Energy consumption of air conditioners at different temperature set points, Energy Build. 65 (2013) 412–418

[30] Y. Tang, X. Jin, Y. Shi, W. Du, Event-triggered attitude synchronization of multiple rigid body systems with velocity-free measurements, Automatica 143 (2022) 110460

[31] X. Li, Y. Tang, Y. Zou, S. Li, W.X. Zheng, Event-triggered multiagent consensus under relative output sensing, IEEE Trans. Cybern. (2022).

[32] J. Berberich, A. Koch, C.W. Scherer, F. Allgöwer, Robust data-driven state-feedback design, in: 2020 ACC, IEEE, 2020, pp. 1532–1538.

[33] C.W. Scherer, Robust mixed control and linear parameter-varying control with full block scalings, in: Adv. LMI Meth. Contr., SIAM, 2000, pp. 187–207.

[34] C. Scherer, S. Weiland, Linear matrix inequalities in control 3 (2) (2000). Delft, The Netherlands