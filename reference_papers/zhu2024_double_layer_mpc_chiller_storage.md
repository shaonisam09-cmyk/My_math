![](images/4317296a2a47adc34bc91e686191578ce9a2c5304f385fc1673cbe9a571bdfeb.jpg)

# The influence of uncertainty parameters on the double-layer model predictive control strategy for the collaborative operation of chiller and cold storage tank in data center

Yiqun Zhu <sup>a</sup>, Quan Zhang <sup>a,\*</sup>, Gongsheng Huang <sup>b</sup>, Jiaqiang Wang <sup>c</sup>, Sikai Zou <sup>d</sup>

<sup>a</sup> College of Civil Engineering, Hunan University, Changsha, 410082, China

<sup>b</sup> Department of Architecture and Civil Engineering, City University of Hong Kong, Hong Kong, 999077, China

<sup>c</sup> School of Energy Science and Engineering, Central South University, Changsha, 410083, China

<sup>d</sup> School of Civil Engineering and Architecture, East China JiaoTong University, Nanchang, 330013, China

## A R T I C L E I N F O

## Handling editor: X Zhao

Keywords: Data center Free cooling Cold storage Double-layer model predictive control (DMPC) Uncertainty parameters

## A B S T R A C T

The inefficient operating states and parameter settings are important factors affecting the high energy con sumption of data center cooling systems. The double-layer model predictive control (DMPC) strategy is devel oped to optimize the operating states and parameter settings of cooling system. The theoretical performance of DMPC strategy is impressive. Meanwhile, the control performance is deeply affected by parameter uncertainties in the model, sensors, and actuator in practical application. Therefore, the influence of uncertain parameters on performance of DMPC strategy is quantified, and the key uncertain parameters (including the wet-bulb tem perature, approaching temperature of cooling tower, cooling capacity of water tank, COP, water flow rates of cooling water pump and chilled water pump) are identified. The results show that the DMPC strategy can prevent the chillers from operating at very low loads and reduce the 24-h PUE by more than 0.01. However, compared to traditional control strategy, the DMPC strategy is more sensitive to uncertain parameters. Parameter uncertainty leads to 70 % mode prediction error rate of DMPC strategy. In mechanical cooling mode, the DMPC strategy is more sensitive to COP than approaching temperature and wet-bulb temperature. However, in the hybrid cooling mode, the sensitivity of the three parameters is opposite to mechanical cooling mode. In free cooling mode, the DMPC strategy is most sensitive to water pump flow rates. Therefore, in order to reduce the computational and economic costs of hybrid cooling systems using the DMPC strategy in data center, more efforts should be placed on reducing the uncertainties of key parameters in initial investment and modeling.

## 1. Introduction

As data centers rapidly expand, handling massive data leads to a significant energy consumption [1,2]. Cooling energy consumption makes up about 40 % of the total energy usage [3]. By 2025, large data centers in China aim for power usage effectiveness (PUE) lower than 1.3 [4], with the current average at 1.55 [5]. Free cooling can be extensively applied in data centers to decrease energy usage [6,7]. Meng et al. [8] showed that optimizing the use of free cooling can increase energy ef ficiency ratio (EER) by more than 50 % at an outdoor temperature of 15 <sup>◦</sup>C.

In addition to using free cooling, adopting more efficient control strategies can also reduce energy consumption [9,10]. In the event of a power outage, data center cooling systems rely on cold water storage tank to provide emergency cooling for 15 min [11]. However, the in formation technology (IT) load rate of large data centers is always less than 30 % [12,13]. In the relatively long period, the chiller always runs under low part load rate (PLR) with low energy efficiency of the cooling system. Over an extended duration, the chiller consistently operates at a low part load rate (PLR), resulting in low coefficient of performance (COP). Thus, cold water storage tank can possess ample capacity to adjust the PLR of chiller effectively for high energy efficiency without sacrificing emergency cooling. Shan et al. [14] demonstrated that employing cold storage technology improved the chiller’s COP by 3.1 % during summer and 22.94 % during winter. Ma [15] achieved a 19.1 % COP increase by optimizing cooling system scheduling with model predictive control (MPC) strategy. These studies focused on commercial buildings and school buildings. Zhu [16] adopted MPC strategy in the experiment and improved the COP of the chiller by 0.013 through cold storage technology in data center. In addition to improving the control strategy, optimizing set parameters can also improve energy efficiency [17,18]. Wang et al. [19] optimized the indoor temperature setting value for switching between mechanical and hybrid cooling mode using MPC strategy. Compared to baseline strategy with the fixed setting value, the energy savings of air conditioner reached 48.75 %. Zou et al. [20] find that when the condenser inlet water temperature ranges from 10 to 22 <sup>◦</sup>C, R32 exhibits a cooling capacity that is 5.4 %–15.6 % higher compared to R22 and R134a. However, in hybrid cooling systems using cold storage technology, there has been little research on optimizing both the setting and operating parameters simultaneously with the MPC strategy.

<table><tr><td colspan="2">Nomenclature</td><td>CR</td><td>Cold release</td></tr><tr><td></td><td></td><td>CN</td><td>Cold normal</td></tr><tr><td>T</td><td>Temperature (°C)</td><td>MILP</td><td>Mixed integer linear programming</td></tr><tr><td>ΔT</td><td>Temperature difference (°C)</td><td>PSO</td><td>Particle swarm optimization</td></tr><tr><td>P</td><td>Power (kW)</td><td>MPC-1</td><td>A benchmark MPC strategy compared to DMPC</td></tr><tr><td>G</td><td>Water flow rate (m3/h)</td><td></td><td></td></tr><tr><td>N</td><td>Number of the cooling unit</td><td colspan="2">Subscripts</td></tr><tr><td>H</td><td>Deadline of one cycle optimization time domain</td><td>wet</td><td>Wet-bulb temperature</td></tr><tr><td>h</td><td>Time</td><td>smh</td><td>Switch wet-bulb temperature of mechanical and hybrid cooling mode</td></tr><tr><td>D</td><td>A value of 0 or 1</td><td></td><td></td></tr><tr><td>U</td><td>A value of 0 or 1</td><td>shf</td><td>Switch wet-bulb temperature of hybrid and free cooling mode</td></tr><tr><td>Q</td><td>Cooling capacity (kW)</td><td></td><td></td></tr><tr><td>Z</td><td>A sufficiently large positive constant</td><td>app</td><td>Approach temperature</td></tr><tr><td>Y</td><td>A value of 0 or 1</td><td>ct</td><td>Cooling tower</td></tr><tr><td>d</td><td>Cooling demand (kW)</td><td>sys</td><td>Cooling system</td></tr><tr><td>α</td><td>Cooling capacity loss of the cold water storage tank</td><td>m</td><td>Cooling mode is mechanical or hybrid cooling mode</td></tr><tr><td>k</td><td>Number of input factors with uncertainty</td><td>f</td><td>Cooling mode is free cooling mode</td></tr><tr><td>Δ</td><td>Quantile variation of uncertain input parameters</td><td>u</td><td>Upper limit</td></tr><tr><td>x</td><td>Normalized values of uncertain input parameters</td><td>l</td><td>Lower limit</td></tr><tr><td>f</td><td>Objective estimation function</td><td>s,l</td><td>Lower limit of the cold storage water flow rate</td></tr><tr><td>EE</td><td>Fundamental effect of uncertain input parameters</td><td>s,u</td><td>Upper limit of the cold storage water flow rate</td></tr><tr><td>μ</td><td>Mean value</td><td>r,l</td><td>Lower limit of the cold release water flow rate</td></tr><tr><td>σ</td><td>Standard deviation</td><td>r,u</td><td>Upper limit of the cold release water flow rate</td></tr><tr><td></td><td></td><td>V</td><td>Volume of cold water storage tank</td></tr><tr><td colspan="2">Acronyms</td><td>cow</td><td>Cooling water flow rate</td></tr><tr><td>MPC</td><td>Model predictive control</td><td>chw</td><td>Chilled water flow rate</td></tr><tr><td>DMPC</td><td>Double-layer model predictive control</td><td>csw</td><td>Cold storage water flow rate</td></tr><tr><td>PUE</td><td>Power usage effectiveness</td><td>crw</td><td>Cold release water flow rate</td></tr><tr><td>COP</td><td>Coefficient of performance</td><td>ct,i</td><td>Cooling tower inlet water temperature</td></tr><tr><td>EER</td><td>Energy efficiency ratio</td><td>ct,o</td><td>Cooling tower outlet water temperature</td></tr><tr><td>IT</td><td>Information technology</td><td>app,l</td><td>Minimum approach temperature of cooling tower</td></tr><tr><td>PLR</td><td>Partial load rate</td><td>ct,l</td><td>Minimum temperature difference of cooling tower</td></tr><tr><td>pPUE</td><td>Partial PUE</td><td>ct,u</td><td>Maximum temperature difference of cooling tower</td></tr><tr><td>MC</td><td>Mechanical cooling</td><td>wt</td><td>Water tank</td></tr><tr><td>FC</td><td>Free cooling</td><td>a,in</td><td>Inlet air temperature</td></tr><tr><td>HC</td><td>Hybrid cooling</td><td>a,re</td><td>Return air temperature</td></tr><tr><td>CS</td><td>Cold storage</td><td></td><td></td></tr></table>

This MPC strategy is a supervisory control [9]. However, unavoid. able factors such as uncertainty and errors can hinder the control per formance and lead to suboptimal solutions [21,22]. Only a few studies have addressed the uncertainty of MPC strategies for data center cooling systems. Huang et al. [23] used stochastic optimization methods to solve the uncertainty problem in temperature prediction, and found that the operation cost of scheduling varied with the level of temperature pre diction uncertainty. Lin et al. [24] used uncertainty analysis and Bayesian calibration to reveal and compare the effects of primary pa rameters on cooling loads, and found that outdoor temperature, outdoor relative humidity, and air exchange rate have significant effects on the cooling load in different regions. Lin et al. [24] compared and analyzed the impact of key parameters on cooling load, and indicated that factors like outdoor temperature, outdoor relative humidity, and the rate of air exchange exert considerable impact on the cooling load. Wang et al. [25] used the Morris method to conduct sensitivity analysis on uncertain parameters, and found that uncertainty has a greater impact on the control performance of optimization strategy than traditional control strategy.

Most research focuses primarily on less than three uncertain pa rameters affecting the cooling system in conventional buildings [26–28]. Only a few studies have mentioned the impact of multiple parameter uncertainties on the cooling system in data center [25]. However, the impact of model parameters uncertainty, measurement parameters uncertainty, and execution parameters uncertainty on the performance of MPC methods using cold storage technology has not been studied for data centers

The control of hybrid cooling systems using cold storage technology in data centers is complex and influenced by many factors. The uncer tainty of data center cooling systems mainly comes from three sources: uncertainty of model parameters, uncertainty of measurement param eters (measurement error), and uncertainty of execution parameters (execution error) [29]. Minor variations in some uncertain parameters can result in considerable fluctuations in energy usage. However, some negligible uncertain parameters hardly affect performance [30]. Striv ing for pinpoint precision in every parameter invariably amplifies the computational burden on the model and necessitates greater in vestments in sensors and actuators [25]. Therefore, we must identify the key uncertainty parameters, allowing us to focus on them with more accurate measurement methods [31].

To this end, an advanced double-layer model predictive control (DMPC) strategy is developed for a cooling system with a cold water storage tank. The novelty of this work include: (1) In the DMPC strategy, not only device scheduling has been optimized, but also parameter settings have been optimized in the mechanical, hybrid and free cooling mode. DMPC strategy not only increases the security of system opera tion, but also further improves energy efficiency and reduces the number of control variables. (2) The influence of model parameters uncertainty, measurement parameters uncertainty and execution parameters uncer tainty on control performance is also quantified, solving the problem of accurately evaluating control performance. (3) The key uncertain pa rameters are identified, and the problem of complex modeling and high initial investment of monitoring equipment can be solved by ignoring the non-critical parameters.

## 2. Cooling system description

The study case revolves around a representative data center situated in China, comprising 1556 racks each with a power rating of 6 kW and an additional 34 racks rated at 4 kW. Given that the focus of this study is primarily on the cold source system, it is assumed that the partial PUE (pPUE) values for the cooling equipment in the computer room and the power distribution system are fixed, being 0.054 and 0.06 respectively.

The schematic diagram of the studied data center cooling system is shown in Fig. 1. The system can realize three cooling modes: mechanical cooling (MC) mode, free cooling (FC) mode, hybrid cooling (HC) mode. And the system can realize three storage/release modes: cold storage (CS) mode, cold release (CR) mode, cold normal (CN) mode.

The cooling modes are achieved by activating or deactivating the cooling infrastructure encompassed within the solid line box in Fig. 1. The logic of cooling modes is shown in Table 1:

MC mode: When the wet-bulb temperature $( T _ { w e t } )$ exceeds the switch wet-bulb temperature for mechanical and hybrid cooling mode $( T _ { s m h } )$ the chiller remains operational while the heat exchanger is deactivated.

FC mode: When the $T _ { w e t }$ is less than the switch wet-bulb temperature of hybrid and free cooling mode $( T _ { s h f } ) _ { : }$ , the heat exchanger remains operational while the chiller is deactivated.

HC mode: When the $T _ { w e t }$ is between $T _ { s h f }$ and $T _ { s m h } ,$ both the heat exchanger and the chiller operate concurrently.

The storage/release modes are achieved by activating or deactivat ing the cold storage/release infrastructure encompassed within the dashed box in Fig. 1. The logic of cold storage/release modes is shown in Table 2.

CS mode: The cooling units are responsible for supplying chilled water to both the computer room and the water storage tank.

CR mode: The water storage tank and cooling units collaborate to satisfy the cooling demand of the computer room.

CN mode: The cooling units provide chilled water directly to the computer room, while the water storage tank is deactivated. In scenarios where multiple chillers need to be operational, the cooling load is evenly balanced among them.

The performance parameters of the equipment and system are shown in Table 3. The hourly meteorological data for a representative meteo rological year is used for this study, which is provided by the National Meteorological Administration in collaboration with Tsinghua University.

## 3. Control strategy and uncertainty research method

## 3.1. Double layer model predictive control strategy

Fig. 2 shows the performance of the chiller used in this paper. It can be seen that COP increases first and then decreases with the increase of PLR, and there is a high efficiency zone. Therefore, based on the typical data center cooling system architecture mentioned above, cold storage technology is used to improve the energy efficiency of cooling equip ment. In optimization, operating parameters: PLR range of each chiller (20 %–100 %), a total of 5 cooling units; Set parameters: approach temperature of cooling tower $\left( T _ { a p p } \right) \ \left( 1 - 5 \ ^ { \circ } \mathbf { C } \right)$ , temperature difference between supply and return water of cooling tower $\left( \Delta T _ { c t } \right) \left( 4 - 8 \mathrm { ~ } ^ { \circ } \mathrm { C } \right) \left[ 1 0 \right] ;$ The optimization domain is 24 h; In this case, the solution space is approximately $( 8 0 ^ { * } 5 ^ { * } 5 ^ { * } 5 ) ^ { 2 4 } .$ . So if the operating parameters and setting parameters are optimized simultaneously, the optimization space be comes significantly large. To address this issue, the double-layer model predictive control (DMPC) strategy is developed. The first-layer is the optimization of operating parameters. The second-layer optimizes the set parameter based on the optimization of the first layer. Since the DMPC strategy utilizes the control information from the first hour for actual control, and the first-layer algorithm of DMPC strategy optimizes the cold storage/release time and capacity, the second-layer algorithm only needs to optimize set parameters based on the cold storage/release mode and capacity of the first hour in the first-layer algorithm. There fore, the solution space of DMPC is reduced to $( 5 ^ { * } 8 0 ) ^ { 2 4 } + 5 ^ { * } 5 ^ { * } 2 4$

The DMPC strategy is shown in Fig. 3. The mixed integer linear programming (MILP) algorithm is employed for the first-layer optimi zation. The MILP offers a robust and versatile approach for solving optimization problems with a mix of continuous and discrete decision variables, providing optimal solutions to a wide range of real problems. Through the MILP model, the contribution of each decision variable can be understood, thereby better understanding the actual problem [32]. The cold storage/release mode is executed according to the optimization results. By using MILP method to optimize equipment scheduling, the

![](images/468263692d3ecd8d1d3b8addbdfd40cfa82553634cebeb3b7c980723f82afee2.jpg)  
Fig. 1. Cooling system schematic diagram of the data center.

Table 1  
Switching logic of the cooling modes.

<table><tr><td></td><td> $V_1$ </td><td> $V_2$ </td><td> $V_3$ </td><td> $V_4$ </td><td> $V_5$ </td><td> $V_6$ </td><td> $V_7$ </td><td> $V_8$ </td><td> $CV_2$ </td><td> $CV_3$ </td></tr><tr><td>MC</td><td>ON</td><td>OFF</td><td>OFF</td><td>ON</td><td>OFF</td><td>ON</td><td>ON</td><td>OFF</td><td>ON</td><td>OFF</td></tr><tr><td>HC</td><td>ON</td><td>OFF</td><td>ON</td><td>OFF</td><td>ON</td><td>OFF</td><td>ON</td><td>OFF</td><td>ON</td><td>ON</td></tr><tr><td>FC</td><td>OFF</td><td>ON</td><td>ON</td><td>OFF</td><td>ON</td><td>OFF</td><td>OFF</td><td>ON</td><td>OFF</td><td>ON</td></tr></table>

Table 2  
Switching logic of the cold storage/release modes.

<table><tr><td></td><td> $P_{3}$ </td><td> $V_{9}$ </td><td> $V_{10}$ </td><td> $V_{11}$ </td><td> $V_{12}$ </td><td> $CV_{1}$ </td></tr><tr><td>CS</td><td>OFF</td><td>ON</td><td>ON</td><td>ON</td><td>ON</td><td>ON</td></tr><tr><td>CR</td><td>ON</td><td>ON</td><td>OFF</td><td>ON</td><td>ON</td><td>ON</td></tr><tr><td>CN</td><td>OFF</td><td>ON</td><td>OFF</td><td>OFF</td><td>OFF</td><td>OFF</td></tr></table>

Table 3  
Performance parameters of the equipment and system.

<table><tr><td>Equipment</td><td>Parameter</td><td>Number</td></tr><tr><td>Variable frequency chiller</td><td>Cooling capacity: 3868.7 kWInput power: 523 kW</td><td>5</td></tr><tr><td>Water-water heat exchanger</td><td>Heat transfer capacity: 3900 kW</td><td>5</td></tr><tr><td>Chilled water pump</td><td>Water flow rate: 600  $m^{3}/h$ ; Pump head: 42m; Input power: 90 kW</td><td>5</td></tr><tr><td>Cooling water pump</td><td>Water flow rate: 700  $m^{3}/h$ ; Pump head: 35m; Input power: 90 kW</td><td>5</td></tr><tr><td>Cold release pump</td><td>Water flow rate: 450  $m^{3}/h$ ; Pump head: 27m; Input power: 45 kW</td><td>5</td></tr><tr><td>Cooling tower</td><td>Water flow rate: 700  $m^{3}/h$ ; Input power: 37 kW; Air flow rate: 651132  $m^{3}/h$ </td><td>5</td></tr><tr><td>Cold water storage tank</td><td>3000 $m^{3}$ </td><td>1</td></tr></table>

![](images/b175248d9d19b6793497155940552a5023094374b7ef19fdb2979432c0934e69.jpg)  
Fig. 2. Performance curve of chiller.

cooling unit can operate in a high efficiency zone. The objective function is the lowest energy consumption during the optimization period. Optimization is carried out under constraints such as cold storage/r elease flow rate, cold storage tank volume, and emergency cold storage capacity. Before entering the optimization process, pre-calculations are performed first. In MC mode, the energy consumption of the cooling system is pre-calculated for all combinations of PLR and chiller quantity. In FC mode, the same pre-calculation is performed for all combinations of chilled water flow rate and heat exchanger quantity. And in HC mode, the cooling system energy consumption of the chiller at its optimum COP is calculated in advance. In the MILP method, the operational state is selected during the optimization process. In MC mode, the number of chillers and PLR are optimized; In FC mode, the number of heat ex changers and chilled water flow rate are optimized; In HC mode, the number of cooling units are optimized. Therefore, by optimizing the hourly operating state of the equipment, the cold storage/release mode is obtained. The optimization variables of first-layer optimization include the time and cold capacity of cold storage/release mode, the number of cooling equipment, the PLR of the chiller, and the water flow rates includes cooling water flow rate $\left( G _ { c o w } \right)$ , chilled water flow rate $( G _ { c h w } ) ,$ , cold storage water flow rate $( G _ { c s w } ) _ { \it \mathrm { \Omega } }$ , and cold release water flow rate $( G _ { c r w } )$

The optimization time domain is 1 h of second-layer algorithm. Therefore, DMPC strategy significantly reduced the dimension of control variables. The particle swarm optimization (PSO) algorithm is adopted as the optimization algorithm for second-layer optimization. As a global search method based on group intelligence, the PSO algorithm is an iterative optimization tool, with the advantages of simple principle, few parameters, easy implementation and fast convergence. So it has been applied in many fields [33]. The optimization variables of second -layer optimization include the $T _ { a p p } ,$ and $\Delta T _ { c t }$

The objective function of the first-layer optimization is to minimize energy consumption of cooling system, as follows:

$$
\min \sum_ {h = h _ {1}} ^ {H} P _ {\text { sys }, h}\tag{1}
$$

$$
P _ {s y s} = P _ {m} + P _ {f}\tag{2}
$$

$$
P _ {m} = \sum_ {i = P L R _ {l}} ^ {P L R _ {u}} \sum_ {k = 0} ^ {N _ {u}} D _ {k, i} P _ {k, i}\tag{3}
$$

$$
P _ {f} = \sum_ {j = G _ {l}} ^ {G _ {u}} \sum_ {k = 0} ^ {N _ {u}} U _ {k, j} P _ {k, j}\tag{4}
$$

$$
\sum_ {i = P L R _ {l}} ^ {P L R _ {u}} \sum_ {k = 0} ^ {N _ {u}} D _ {k, i} + \sum_ {j = G _ {l}} ^ {G _ {u}} \sum_ {k = 0} ^ {N _ {u}} U _ {k, j} = 1\tag{5}
$$

Where P is the power (kW); G represents the water flow rate $( \mathrm { m } ^ { 3 } / \mathrm { h } ) ;$ N represents the number of cooling units; H represents the deadline of one cycle optimization time domain: h is the time: D and U are values of 0 or 1. The meaning of the subscripts is as follows: sys represents the cooling system; m indicates that the cooling mode is MC mode or HC mode; f indicates that the cooling mode is FC mode; u is the upper limit; l is the lower limit.

The constraints of the first-layer optimization are as follows.

(1) The constraint conditions of cold storage/release flow rate are as follows:

$$
\left(Q _ {m} + Q _ {f} + Z ^ {*} Y\right) _ {h} - d _ {h} \geq Q _ {s, l}\tag{6}
$$

$$
\left(Q _ {m} + Q _ {f} - Z ^ {*} (1 - Y)\right) _ {h} - d _ {h} \leq - Q _ {r, l}\tag{7}
$$

$$
- Q _ {r, u} \leq \left(Q _ {m} + Q _ {f}\right) _ {h} - d _ {h} \leq Q _ {s, u}\tag{8}
$$

$$
Q _ {m} = \sum_ {i = P L R _ {l}} ^ {P L R _ {u}} \sum_ {k = 0} ^ {N _ {u}} D _ {k, i} * Q _ {k, i}\tag{9}
$$

$$
Q _ {f} = \sum_ {j = G _ {l}} ^ {G _ {u}} \sum_ {k = 0} ^ {N _ {u}} U _ {k, j} * Q _ {k, j}\tag{10}
$$

![](images/886eda1d7770dc424ec08cada09eda2d60c885c8f4fd4a1e9421b2f4d4c8e137.jpg)  
Fig. 3. DMPC strategy flow chat of cooling system.

where Q represents the cooling capacity (kW); Z is a sufficiently large positive constant; The value of Y indicates the cold storage/release operational status of either the CR mode or the CS mode, where 1 sig nifies the CR mode is operation, and 0 signifies the CS mode is operation; d is the cooling demand (kW). The meaning of the subscripts is as fol lows: s,l and s,u are the lower and upper limits of the cold storage water flow rate, respectively; r,l and $r , u$ are the lower and upper limits of the cold release water flow rate, respectively.

(2) The constraint condition for the cold storage tank volume is as follows:

$$
Q _ {V, l} \leq S ^ {*} \alpha + \left(\sum_ {n = 1} ^ {h} \left(Q _ {m} + Q _ {f}\right) _ {h} - d _ {h}\right) ^ {*} \alpha \leq Q _ {V, u}\tag{11}
$$

where the subscript V is the volume of cold storage tank. α is the cooling capacity loss of the water tank for 1 h. S is the initial cold storage ca pacity of the cold storage tank before entering each optimization.

Table 4 presents the critical values for the MILP algorithm. In Table $4 , P L R _ { u }$ and $P L R _ { l }$ are the maximum and minimum PLR of the chiller operation, respectively, to prevent the chiller from operating under overload and very low load. $G _ { u }$ and $G _ { d }$ are the maximum and minimum flow rates for the pump operation, respectively, to prevent the pump from being overloaded and operating at very low loads. $G _ { V , s , l }$ and $G _ { V , r , l }$ are the minimum flow rates of cold storage and cold release, respectively. During CS mode, the cold storage flow rate cannot be lower than $G _ { V , s , l } .$ And during CR mode, the cold release flow rate cannot be lower than $G _ { V , r , l } .$ In formula (6), $Q _ { s , l }$ represents the lower limit of the cold storage flow rate, which is the cooling capacity calculated by the $G _ { V , s , l } .$ In formula (7), $Q _ { r , l }$ represents the lower limit of the cold release flow rate, which is the cooling capacity calculated by the $G _ { V , r , l } . G _ { V , s , u }$ and $G _ { V , r } ,$ are the maximum flow rates of cold storage and cold release, respec tively. During CS mode, the cold storage flow rate cannot exceed $G _ { V , s , u } .$ And during CR mode, the cold release flow rate cannot exceed $G _ { V , r , u } .$ In formula (8), $Q _ { s , u }$ represents the upper limit of the cold storage flow rate, which is the cooling capacity calculated by the $G _ { V , s , w }$ and $Q _ { r , u }$ represents the upper limit of the cold release flow rate, which is the cooling ca pacity calculated by the $G _ { V , r , u }$ .

The optimization variables of second-layer optimization (PSO algo rithm) include $T _ { a p p }$ and $\Delta T _ { c t } .$ The specific steps are as follows.

Table 4  
Critical value in objective function and constraints.

<table><tr><td> $PLR_u$ </td><td> $PLR_l$ </td><td> $G_u$ </td><td> $G_d$ </td><td> $G_{V,s,l}/G_{V,r,l}$ </td><td> $G_{V,s,u}/G_{V,r,u}$ </td><td>α</td></tr><tr><td>(%)</td><td>(%)</td><td> $m^3/h$ </td><td> $m^3/h$ </td><td> $m^3/h$ </td><td> $m^3/h$ </td><td></td></tr><tr><td>100</td><td>30</td><td>600</td><td>200</td><td>40</td><td>2000</td><td>5 %/24</td></tr></table>

(1) Initialize set parameters and operating parameters, including $T _ { a p p }$ and $\varDelta T _ { c t }$

(2) Calculate the objective function, aiming to minimize system en ergy consumption. The objective function is the same as the firstlayer optimization. The constraint conditions are:

$$
T _ {c t, o} \geq T _ {w e t} + T _ {a p p, l}\tag{12}
$$

where the T represents the temperature (<sup>◦</sup>C). The meaning of the sub scripts is as follows: ct,o represents the cooling tower outlet water; wet represents the wet-bult; app,l represents the minimum approach tem perature of cooling tower. The minimum approach temperature of cooling tower is 1 <sup>◦</sup>C [10].

$$
\Delta T _ {c t, l} \leq \Delta T _ {c t} \leq \Delta T _ {c t, u}\tag{13}
$$

where ΔT subscript represents the temperature difference (<sup>◦</sup>C); The subscript ct,l represents the minimum $\Delta T _ { c t } \ ( 4 \ ^ { \circ } \mathrm { C } ) ;$ The subscript ct,u represents the maximum $\Delta T _ { c t } ( 8 { \mathrm { ~ } ^ { \circ } } \mathrm { C } )$

(3) The PSO algorithm compares the results of each particle and determines how to move the next generation of particles. Until the termination condition is reached: the last 10 iterations have obtained the same objective function value or reached the maximum set number of iterations (200 iterations).

To validate the superiority of the DMPC strategy, a comparison is made with the traditional control strategy and the MPC-1 strategy. The traditional control strategy is set and operates in the normal mode.

The MPC-1 strategy is to search for the optimal PLR at different times in advance during MC and HC modes, and use the MILP algorithm to optimize the time and capacity of CS and CR modes of cold storage tank, and the number of cooling units. When in FC mode, the heat exchanger is operated at the rated water flow rate of the chilled water pump, and the MILP algorithm is used to optimize the time and capacity of CS and CR modes of cold storage tank and the number of heat exchangers.

The working conditions for the traditional control strategy, the MPC-

Table 5  
The working conditions for three strategies.

<table><tr><td rowspan="2"></td><td colspan="3">Strategy</td></tr><tr><td>Traditional</td><td>MPC-1</td><td>DMPC</td></tr><tr><td>Supply temperature of chilled water</td><td>18</td><td>18</td><td>18</td></tr><tr><td>Return temperature of chilled water</td><td>24</td><td>24</td><td>24</td></tr><tr><td> $T_{ct,o}$  (winter)</td><td>17</td><td>17</td><td>Optimized</td></tr><tr><td> $T_{app}$  (summer)</td><td>3</td><td>3</td><td>Optimized</td></tr><tr><td> $\Delta T_{ct}$ </td><td>6</td><td>6</td><td>Optimized</td></tr><tr><td> $T_{smh}$ </td><td>20</td><td>20</td><td>Optimized</td></tr><tr><td> $T_{shf}$ </td><td>14</td><td>14</td><td>Optimized</td></tr></table>

1 strategy and the DMPC strategy are shown in Table 5: (1) The supply temperature of chilled water for three strategies is ${ 1 8 ~ ^ { \circ } \mathrm { C } }$ and return temperature is $2 4 ~ ^ { \circ } \mathrm { C } .$ (2) For both the traditional control strategy and MPC-1 strategy, the $T _ { c t , o } 1 s 1 7 ^ { \circ } \mathrm { C }$ in winter; the $T _ {  { \boldsymbol { a p p } } }$ is $3 ~ ^ { \circ } \mathrm { C }$ in summer; and the $\Delta T _ { c t }$ is $6 ~ { } ^ { \circ } \mathrm { C } .$ For DMPC strategy, the $T _ { a p p }$ is more than ${ 1 \ ^ { \circ } \mathrm { C } } ;$ the range of $\Delta T _ { c t } \mathrm { i } s 4 - 8 ^ { \circ } \mathrm { C } ;$ and the $T _ { c t , o } , T _ { a p p } ,$ and $1 T _ { c t }$ are optimized. (3) For both the traditional control strategy and MPC-1 strategy, the switch wetbulb temperature of MC mode and HC mode $( T _ { s m h } )$ is $2 0 ~ ^ { \circ } \mathrm { C } ;$ and the switch wet-bulb temperature of HC mode and FC mode $( T _ { s h f } )$ is ${ 1 4 } ~ ^ { \circ } \mathrm { C } .$ For DMPC strategy, the $T _ { s m h }$ and $T _ { s h f }$ are optimized.

## 3.2. Procedure of uncertainty impact analysis on control performance

The accuracy of the DMPC strategy is closely related to the uncer tainty of measurement parameters, model parameters, and execution parameters. Therefore, it is essential to quantify the impact of uncertain parameters on the control performance of DMPC strategy. Additionally, to identify which parameters have a significant impact on performance, a sensitivity analysis of uncertain parameters is conducted. The sche matic of performance estimation of the DMPC strategy under different uncertain parameters is shown in Fig. 4. To study the impact of uncer tainty in various parameters on the control performance, systematic bias and random white noise are added to sensor measurements, model pa rameters and control variables, respectively. Specifically, the difference between the sensor measurement value and the true value is set to simulate the measurement uncertainty, the difference between the optimized calculation model and the simulation model is controlled to simulate the model uncertainty, and the deviation is added to the opti mized control variable to simulate the execution uncertainty. In order to study the influence law of the uncertainty of different parameters on the control performance, the uncertainty analysis and the sensitivity anal ysis of the uncertain parameters are conducted.

The three steps involved in the uncertainty analysis are as follows.

(1) The uncertainty and probability distribution of each input parameter are quantified to adhere a normal distribution [25,26].

(2) Uncertainty transmission is achieved through Monte-Carlo simulation, and the distribution of control performance is ob tained. The Monte-Carlo method is a sample-based approach that uses random input samples to simulate runs repeatedly, gener ating multiple sets of outputs.

(3) The data results are analyzed, and a probability density map is created. Statistical characteristics such as mean value, interme diate value and standard deviation can be obtained. The mean value reflects the effect of the control performance, while the standard deviation indicates the robustness of the control strat egy. A smaller standard deviation implies that the control strat egy is less affected by parameter uncertainty and has better robustness.

The Morris analysis method can comprehensively search the input parameter space, considering interactions between input parameters and nonlinear responses, thereby obtaining the relative sensitivity of different uncertain input parameters on control performance. In addi tion, the Morris analysis method is also a relatively low-cost method, well-suited for addressing problems where solving computational models is time-consuming or the objective function relies on long-term simulation results [34]. Therefore, the Morris sensitivity analysis method is adopted.

These three steps involved in the Morris sensitivity analysis are as follows.

(1) Input matrix generation. Firstly, quantify the uncertain input parameters. Then, randomly sample a discrete hypercube. Finally, reverse each dimension of the hypercube to obtain the matrix of uncertain input parameters. Where, the discretized quantile hypercube is used to represent the normalized features of the input parameters, ensuring that each input parameter is between 0 and 1.

(2) Output matrix generation. The control strategy calculates the control variables based on the input matrix, and inputs the con trol variables into the simulation platform combined with the actual input parameters to calculate the objective function and form the output matrix.

(3) The sensitivity parameter calculation. With the objective function based on step 2, the elementary effect of each parameter on control performance can be obtained by the following [35]:

![](images/b6a38450b876bbffaf0665b67bf53c48e0d3d8675ba84d03fd64db1aec8610b5.jpg)  
Fig. 4. Performance evaluation with DMPC strategy under uncertainty.

$$
E E _ {j} (X) = \frac {\left[ f \left(x _ {1} , x _ {2} , \dots , x _ {j - 1} , x _ {j} + \Delta_ {i} , x _ {j + 1} , \dots , x _ {k}\right) - f (X) \right]}{\Delta_ {i}}\tag{14}
$$

where any x is the normalized value of uncertain input parameters. Δ is the quantile variation of uncertain input parameters. f is the objective estimation function. EE is the elementary effect of uncertain input pa rameters. The Morris analysis method provides two sensitivity measures for each input factor [36], which are the mean value (μ) and standard deviation (σ). The μ gauges the importance of input factors. Larger μ means more significant impact to model output. The σ characterizes nonlinear effects and interactions between input factors.

Both uncertainty analysis and Morris analysis use the same distri bution of input parameters. The quantification of probability distribu tion for each input uncertainty parameter adheres to a normal distribution pattern, as shown in Table 6 [25,26]. The number of Monte-Carlo simulations is 80 (sampling times) \* 3 (3 control strategies) = 240 times. In Morris sensitivity analysis, the minimum number of samples for each control strategy is 200 [37], and the number of simu lations is $2 0 0 \div 3 = 6 0 0$ times.

## 4. Results and discussion

## 4.1. System performance of different control strategies

The January 13th, February 1st and August 25th three specific dates are selected as typical days for analysis due to their different cooling conditions: January 13th represents a full day of FC mode; February 1st includes a combination of HC and FC conditions; August 25th represents a full day of MC mode. Perform typical daily analysis at the 50 % IT load rate.

Fig. 5 shows the operating parameters and PUE on August 25th. In Fig. 5 (a), on August 25th, the $T _ { w e t }$ for 24 h is higher than $2 6 ^ { \circ } \mathrm { C } .$ . All three control strategies operate in MC mode for 24 h. Fig. 5 (b) shows the cold storage/release mode and the number of chillers for different control strategies. As can be seen from Fig. 5 (b), the number of operating chillers of traditional control strategy remains at 2 within $^ { 2 4 \mathrm { h } , }$ while the number of chillers in the MPC-1 and DMPC strategies is more than 4. This is because the traditional control strategy is not optimized. The rule of the traditional control strategy is: Once a chiller reaches full load, an additional chiller is activated, and the cooling load is evenly distributed among them. Since the cooling demand of the computer room is stable in data center, the number of chillers in traditional control strategy re mains unchanged. In addition. two chillers with PLR of about 90 % can satisfy the cooling demand for computer room. And the COP of chiller in the traditional control strategy is 8.66. However, for MPC-1 and DMPC strategies, the number of chillers and PLR are optimized. In MPC-1 and DMPC strategies, in addition to serving as an emergency cooling ca pacity, the cold storage tank also fulfills the function of regulating the operational state of the chiller. When the outdoor $T _ { w e t }$ is low, it is ad vantageous for the chiller to have an appropriate amount of excess cooling capacity, which is stored in the cold storage tank. Conversely, when the outdoor $T _ { w e t }$ is high, it becomes disadvantageous for the chiller, resulting in reduced cooling capacity. The cold storage tank is utilized to supplement the cooling capacity required in the computer room. The COP of MPC-1 strategy is 9.66 by optimizing the number of chillers through the cold storage technology. The COP of DMPC strategy is 10.26 by optimizing the PLR and number of chillers through the cold storage technology. Compared with the traditional control strategy, the COP of the chiller of MPC-1 and DMPC strategies increased by 11.55 % and 18.48 %, respectively. Studies have shown that the cooperative operation of cold storage tank and chillers can effectively reduce energy consumption $[ 1 4 , 3 2 ]$ . In addition, experiments have demonstrated that such strategies can improve the COP of chillers [16].

Table 6  
Probability distribution of uncertainty parameters.

<table><tr><td></td><td>Parameter</td><td>Distribution</td></tr><tr><td>Model uncertainties</td><td>COP of chiller (COP)</td><td>N (0, 12)</td></tr><tr><td rowspan="6">Measurement uncertainties</td><td>Cooling water flow rate ( $G_{cow}$ , m3/h)</td><td>N (0, 202)</td></tr><tr><td>Chilled water flow rate ( $G_{chw}$ , m3/h)</td><td>N (0,202)</td></tr><tr><td>Water flow rate of cold release pump ( $G_{crw}$ , m3/h)</td><td>N (0, 202)</td></tr><tr><td>IT load ( $Q_{IT}$ , kW)</td><td>N (0, 702)</td></tr><tr><td>Wet-bulb temperature ( $T_{wet}$ , °C)</td><td>N (0, 12)</td></tr><tr><td>Cooling capacity of water tank ( $Q_{wt}$ , kW)</td><td>N (0, 702)</td></tr><tr><td rowspan="2">Execution uncertainties</td><td>Approaching temperature of cooling tower ( $T_{app}$ , °C)</td><td>N (0, 0.82)</td></tr><tr><td>Executed PLR of chiller (PLR, %)</td><td>N (0, 22)</td></tr></table>

In Fig. $5 ( \mathbf { c } ) _ { i }$ , the $T _ {  { \boldsymbol { a p p } } }$ of traditional and MPC-1 strategy is $3 \ ^ { \circ } \mathrm { C } .$ The average $T _ { { a p p } }$ of DMPC strategy is around $1 . 8 ^ { \circ } \mathrm { C } ,$ and the $\Delta T _ { c t }$ is adjusted to $8 \ ^ { \circ } \mathrm { C } .$ In Fig. 5 (d), both MPC-1 and DMPC strategies have a PLR of around 55 %. The chiller COP of the MPC-1 strategy is higher than traditional control strategy, and DMPC strategy further improved the chiller COP. Although reducing the $T _ { a p p }$ increases the energy con sumption of the cooling tower fan, it has a greater impact on the improvement of the COP of the chiller. MPC-1 strategy reduces PUE by 0.017 compared to traditional control strategy, while DMPC strategy reduces PUE by 0.022 compared to traditional control strategy in Fig. 5 (e).

In Fig. 5 (e), the PUE of the traditional control strategy is signifi cantly higher at 15–16 and 23 h compared to MPC-1 and DMPC stra tegies. This is because 15–16 h is the period with the highest $T _ { w e t }$ prompting MPC-1 and DMPC strategies to operate in CR mode, which results in the PUE of the MPC-1 and DMPC strategies being lower during this period compared to the traditional control strategy. For MPC-1 and DMPC strategies, at the end of the day, the cold storage tank returns to the initial state. Therefore, both MPC-1 and DMPC strategies operate in CR mode at the 23rd hour, and the PUE of both strategies is lower than that of the traditional control strategy. The PUE of MPC-1 strategy at the 14th hour is higher than that of DMPC and traditional control strategies. This is because the chillers of the MPC-1 strategy always operate at the optimum point, so the degree of cold storage and release is higher than that of the DMPC strategy, and the switching times of cold storage and release are frequent. Therefore, the MPC-1 strategy is in the CS mode at the 14th hour, and the PUE of the MPC-1 strategy at this hour is higher than that of the traditional control strategy. However, the temperature at the 14th hour is relatively high, and the DMPC strategy optimizes both PLR and the number of chillers. Therefore, the DMPC strategy operates in the CR mode during this hour, which is more conducive to efficient cooling system operation. Consequently, the PUE of the DMPC strategy is lower than that of the traditional control strategy. At the 22 nd h, the PUE of the DMPC strategy is higher than that of the traditional strategy. This is because, although the chillers of the DMPC strategy are operating in the high efficiency zone, the DMPC strategy is in the CS mode at the 22 nd h.

Fig. 6 shows the operating parameters and PUE on February 1st. In Fig. 6 (a), on February 1st, the $T _ { w e t }$ for the first 7 h is below ${ 1 4 } \ { ^ { \circ } \mathrm { C } } ,$ and the following 17 h is between 14 and $2 0 ~ ^ { \circ } \mathrm { C } .$ So, the cooling mode of MPC-1 and traditional control strategy is FC mode for the first $^ { 7 \mathrm { h } , }$ and HC mode for the following 17 h in Fig. 6 (b). But the cooling mode for the 8th, 9th, 10th, 23rd, and 24th hours of the DMPC strategy is FC mode. The running time of the chiller in DMPC strategy is reduced. As shown in $\mathrm { F i g . } 6 \left( \mathbf { a } \right)$ the outdoor $T _ { w e t }$ at the 8th, 9th and 10th hours just exceeded ${ 1 4 ^ { \circ } } \mathrm { C } ,$ so it is shown in Fig. 6 (b) that from the 8th hour, the FC mode entered the HC mode of traditional control and MPC-1 strategies. Therefore, as shown in Fig. 6 (e), the chiller is turned on at this time, and the PLR of the chiller is lower than 0.3. But, through DMPC optimiza tion, the $T _ {  { \boldsymbol { a p p } } }$ of the 8th, 9th and 10th hours of the cooling tower is $^ { 2 . 6 , }$ 2.1 and ${ } ^ { 1 . 2 ^ { \circ } \mathrm { C } }$ respectively in Fig. 6 (d). The DMPC meets the FC mode, and the chiller is in the off state, avoiding the low-load operation of the chiller. The operation principle of the 23rd and 24th hours is the same as that of the 8th, 9th and 10th hours. As can be seen from Fig. 6 (f), the PUE of MPC-1 and DMPC strategies at the 15th, 16th, and 17th hours is the same and lower than that of traditional control strategy. This is because, as shown in $\mathrm { F i g . } 6 ( \mathbf { a } ) ,$ , the 15th, 16th and 17th hours are the periods with the highest outdoor $T _ { w e t } ,$ so the MPC-1 and DMPC strategies are in CR mode. Meanwhile, the cooling capacity is completely provided by the cold storage tank, and the chiller is turned off. MPC-1 reduces PUE by 0.004 compared to traditional control strategy, while DMPC reduces PUE by 0.013 compared to traditional control strategy in Fig. 6 (f).

![](images/33c804e102c5e326fe1864dcd6926f41bc19d2162fae748aa89780fc698b2a88.jpg)  
(a) Twet on August 25th

![](images/4017080887b56ae8a61cace0178597696d37fcd59befe2fd19cd8f20222cd8c1.jpg)

![](images/36463b8f29a961eace12b8e0ed1b48587a3221d08ba523bf761dfaefa21d0eee.jpg)  
(b) Cold storage/release mode and number of chillers

(c) Cooling water supply temperature and ∆Tct  
![](images/d660890425faf7e45be7e9ee0d3cc9acb34ae8de196fc96dae9d4c5ffa200eda.jpg)  
(d) Chiller operation status

![](images/b498c0ce88b6274dcd62f45d8416ff87113d37dbc3d5557ce3e467e5cb6a414d.jpg)  
(e) PUE.  
Fig. 5. Operating parameters and PUE on August 25th.

Fig. 7 shows the operating parameters and PUE on January 13th. From Fig. 7 (a), The outdoor $T _ { w e t }$ throughout the day is below $1 4 ^ { \circ } \mathrm { C } . \mathrm { A l l }$ three control strategies operate in FC mode. ${ \mathrm { F i g . ~ 7 ~ } } ( \mathbf { b } )$ shows the cold storage/release mode and the number of heat exchangers for different control strategies. As shown in ${ \mathrm { F i g . ~ } } 7 \ ( { \mathrm { c } } ) { \mathrm { , } }$ , the $T _ { c t , o }$ for all three control strategies is around ${ 1 7 } ^ { \circ } \mathrm { C } ,$ , indicating that setting the $T _ { c t , o }$ for FC mode to 17 <sup>◦</sup>C is optimal. The $\Delta T _ { c t }$ of the DMPC strategy is $8 ^ { \circ } \mathrm { C } ,$ which is the main reason why the DMPC strategy reduces PUE in Fig. 7 (d).

In Fig. 7 (d), for the first 19 h, the PUE of the MPC-1 strategy is higher than that of the traditional control strategy and the DMPC strategy. This is because, as seen in Fig. 7 (b), the MPC-1 strategy operates in the CS mode during the first 19 h, resulting in a higher PUE compared to the traditional control mode. As for the DMPC strategy, although it also incorporates the CS mode during the first 19 h, as seen in ${ \mathrm { F i g . 7 } } ( \mathrm { c } ) ,$ , the cooling water supply and return temperature difference in DMPC strategy is large, and the cooling water flow is small, leading to lower energy consumption compared to MPC-1 strategy. From the 20th to the 24th hour, the PUE of the MPC-1 strategy is significantly reduced due to the CR mode

## 4.2. Quantification of the impact of uncertainty on control performance

The histograms of energy consumption and mode prediction error distributions for three control methods of three days in Figs. 8–10. For a clearer and more convenient comparative analysis, the quantified results of parameter uncertainty are summarized in Table 7. Due to the $T _ { w e t }$ being significantly higher than $T _ { s m h }$ on August 25th and considerably lower than $T _ { s h f }$ on January 13th, there are no cooling mode prediction error distributions during these two days. However, on February 1st, the $T _ { w e t }$ is around $T _ { s h f } ,$ so there is a cooling mode prediction error distribu tion, as shown in Fig. 9 (e). The coefficient of variation of system energy consumption (referred to as the relative standard deviation) is a measure of the dispersion of the frequency distribution of system energy con sumption. The higher the value, the wider the range of system distri bution and the greater the uncertainty of performance.

![](images/189412a681a977949daf2d94cc5cd61bcb0a3329cd7b812f66360480459d72db.jpg)

(a) Twer on February 1st.  
![](images/5c8d22fd98f39641eeebbe4f2a60d9012a5ad5862fcf52bbd737817d8248447a.jpg)  
(b) Cooling mode on February 1st.

![](images/2a1cc5b65023ddc4606761e3413815e37d6aeb76418d2829fc5fc1c3fa1fa52d.jpg)  
(d) Cooling water supply temperature and ∆Tct.

![](images/ed982fa238929ff9a1fa1d82bdb009657384fab28b238673c1bf6c81c48af989.jpg)

![](images/150f09e95e025bbd67b754f937d8d466673aedd5ef80c37a1eb1cb758fb43fb5.jpg)  
(e) Chiller operation status

![](images/d25e5e65e0cefefc175676c30b9a9bda349644c9221f2f0d665d3a09a8fb83f6.jpg)  
(c) Cold storage/release mode and number of cooling units on February 1st

![](images/273d31f5d582543ff5be9a0ccae80f476cf245df81a7952440acac68a29ea176.jpg)  
(f) PUE.  
Fig. 6. Operating parameters and PUE on February 1st.

The number of Monte-Carlo simulations for each strategy is 80. The meaning of Fig. 8 (d) is that, assuming the horizontal axis represents x and the vertical axis represents y, there are x hours in which the number of cold storage/release mode prediction errors is y within a 24-h period. For example: the bar with an x-axis value of 2 in Fig. 8 (d) indicates that, for the MPC-1 strategy, there are 2 h within a 24-h period in which the number of mode prediction errors is 1. Both the MPC-1 and the DMPC strategies have the highest number of mode prediction errors in the 4–12 h over a 24-h period. This is because the uncertainty of $T _ { w e t }$ changes the operating conditions during the day, and the uncertainty of the COP of the chiller changes the operating state of the chiller, so the cold storage/release mode is changed when seeking optimization. Dur ing Monte-Carlo simulations, smaller parameter deviations result in fewer hours with incorrect cold storage/release modes in a day. How ever, the cold storage/release mode for each hour is influenced by the entire day, so basically, once there is an incorrect mode judgment, it will inevitably affect the mode judgment for at least two time periods. The uncertainty parameters are normally distributed, the storage/release cooling capacity is balanced throughout the day, and the possibility of the cold storage/release mode being wrong in each hour is small. Although there is uncertainty in the $T _ { w e t } ,$ the beginning of the day is in cold storage mode, and the period of the day with the highest $T _ { w e t }$ remains consistent, that ${ \mathrm { i } } s ,$ cold release mode is still carried out when the working conditions are unfavorable. Therefore, the period when the mode is wrong is basically the period of temperature fluctuation. During this period, the cold storage and release modes alternate, but mode prediction errors do not occur all the time. Consequently, most of the hours with wrong mode prediction are within 12 h. Among them, for the cases where the mode prediction error exceeds 7 h in a day, the fre quency of occurrences of such errors with the DMPC strategy is higher than with the MPC-1 strategy. This is because the DMPC strategy can accurately optimize the PLR and setting parameters. The MPC-1 strategy only optimizes the number of chillers. The PLR is the best operating state calculated in advance, and the cooling water supply temperature and the cooling water supply and return temperature difference are both fixed values. Therefore, compared with the MPC-1 strategy, more parameters can affect the DMPC strategy, and the DMPC strategy is more dependent

![](images/0651b5a7efeb0f09a951a7377460f95926723d93b59306d36114dd627954053f.jpg)  
(a) Twet on January 13th

![](images/ab958412ac481aaa7b7621f9d627f7f98debdbadb2087408e648ff76d5cb7a03.jpg)  
(c) Cooling water supply temperature and ∆Tc.

![](images/1817e58c8d5c78b8563c39fd5408531b3c2d8c31e6e778f95e369fec6bc184af.jpg)  
(b) Cold storage/release mode and number of heat exchangers on January

![](images/b46d624409408680848bcbe12e4ffd49595a6b098ba4a3ee69095bbb01e64c39.jpg)  
(d) PUE.

Fig. 7. Operating parameters and PUE on January 13th.  
![](images/071a18cd7fa78f06a9174f3d90707f498ea9f4b295d78222e1a04cc31858dc0a.jpg)  
(a) Energy consumption distribution of traditional control strategy.

![](images/3e99dbaf77cc62dafb2a32e92b68aaa0617fa41e51c8cbf56199942e8d5dfcd7.jpg)  
(b) Energy consumption distribution of  
MPC-1 strategy.

![](images/86b89eba38af8478e1e0e5e252f1108273e957af962e7641409f655978eac87e.jpg)  
(c) Energy consumption distribution of DMPC strategy

![](images/e875004d4ae60d620fd591fc88e22649315a64e11abbcf793250cf81a9ed2c84.jpg)  
Integrated hour number of cold storage/release mode error  
(d) Cold storage/release mode prediction error distribution of MPC-1 and DMPC  
strategies under various uncertainties.

Fig. 8. Energy consumption and mode prediction error distributions on August 25th.

![](images/78b99ce3bc68085d9de0487febe9a8ad16f6f0bab384c11e36fe9fe55381ad58.jpg)  
(a) Energy consumption distribution of traditional control strategy.

![](images/67f11835239eb7d29755cef44e7a42e397d40a2ad02085e52aa72ef41318187c.jpg)  
(b) Energy consumption distribution of

![](images/02a96ffc26971c9d6443663f3ebce0b077bd83999a30d2d3079fd1e14c26f848.jpg)  
(c) Energy consumption distribution of DMPC strategy.

![](images/45cb4d4c1d2906bfed3e3ebe13e7d8fb1bbeeb1b907baab822210e291b4aadff.jpg)

(d) Cold storage/release mode prediction error distribution of MPC-1 and DMPC strategies under various uncertainties.  
![](images/d9da92615f0dd0f6112b464bc3de7f14c8b71d182f07dc05af25cd524e56a5b9.jpg)  
(e) Cooling mode prediction error distribution of MPC-1 and DMPC strategies under various uncertainties.  
Fig. 9. Energy consumption and mode prediction error distributions on February 1st.

on the accuracy of the parameters. Consequently, the DMPC strategy can have cold storage/release mode prediction errors at more hours in a day.

Fig. 9 (e) shows the number of hours in a 24-h period during which cooling mode prediction errors occur. As can be seen from Fig. 9 (e), there are 2, 3, and 4 h within a 24-h day where the number of errors in cooling mode prediction errors peaks. This is because, on February 1, there are 2–4 h during which the $T _ { w e t }$ is very close to the switching temperature between free and hybrid cooling modes. Due to un certainties in the outdoor $T _ { w e t }$ and the $T _ { a p p } ,$ there is cooling mode pre diction error when the cooling mode switching occurs. Therefore, the errors in cooling mode prediction are concentrated within these 2–4 h.

The energy consumption distribution range of DMPC strategy is wider than MPC-1 strategy in Figs. 8–10, with a larger average coeffi cient of energy consumption variation of DMPC strategy (average 3.03) than MPC-1 strategy (average 2.95) in Table 7. The number of prediction errors in the cooling mode of DMPC strategy for more than 6 h (9 times) is higher than MPC-1 strategy (4 times) on February 1st in Fig. 9 (e), and the coefficient of variation of DMPC strategy is higher than MPC-1 strategy in Table 7. So, the robustness of DMPC is worse than that of MPC-1 strategy. The average number of prediction errors in the cold storage/release mode of MPC-1 strategy and DMPC strategy is above 13 %, indicating poorer robustness compared to traditional control strategy.

The distribution range of system energy consumption for traditional control strategies on August 25th, February 1st, and January 13th is $3 3 . 1 8 \times 1 0 ^ { 3 } { \sim } 3 9 . 1 0 \times 1 0 ^ { { \bar { 3 } } } \mathrm { k W h } , 1 6 . 5 6 \times 1 0 ^ { { \bar { 3 } } } { \sim } 1 9 . 8 6 \times 1 0 ^ { 3 } \mathrm { k W h } , 9 . 0 8 \times 1 0 ^ { 1 0 } \mathrm { k W h }$ $1 0 ^ { 3 } { \sim } 1 0 . 5 1 \times 1 0 ^ { 3 }$ kWh, the coefficient of variation reached 3.53, 4.25, and 3.22. This means that the control performance of traditional, MPC-1 and DMPC strategies is significantly affected by the uncertainty of the studied parameters, and their robustness is poor.

## 4.3. Sensitivity identification of the uncertainty parameters

The results from the Morris sensitivity analysis on August 25th are

![](images/244a9fda4a12a8f09de54a1b031c8e4270e3d64b240101ed6c1e5dbb131c5edc.jpg)  
(a) Energy consumption distribution of

![](images/45148cbdd9c8276866ec4c6777ad7586c6ac6bd4977b62f6ee86c4350f3f4bf9.jpg)  
(b) Energy consumption distribution of

traditional control strategy  
![](images/9376256a71b101efb8ca062679e4a489fc2a4c9603693cc7390ce23f79cecdfc.jpg)  
(c) Energy consumption distribution of DMPC strategy

MPC-1 strategy.  
![](images/facb01968955a150dfb373a6ee613781051e83d0f6a5c1b84a5877e04b6b0699.jpg)  
Integrated hour number of cold storage/release mode error  
(d) Cold storage/release mode prediction error distribution of MPC-1 and DMPC strategies under various uncertainties

Fig. 10. Energy consumption and mode prediction error distributions on January 13th.  
Table 7  
Summary of the results of uncertainty performance.

<table><tr><td rowspan="3">Index</td><td colspan="3">System energy consumption × 103(kWh)</td><td colspan="3">Cold storage/release mode prediction error (%)</td><td colspan="3">Cooling mode prediction error (%)</td></tr><tr><td>Traditional</td><td>MPC-1</td><td>DMPC</td><td>Traditional</td><td>MPC-1</td><td>DMPC</td><td>Traditional</td><td>MPC-1</td><td>DMPC</td></tr><tr><td>Traditional</td><td>MPC-1</td><td>DMPC</td><td>Traditional</td><td>MPC-1</td><td>DMPC</td><td>Traditional</td><td>MPC-1</td><td>DMPC</td></tr><tr><td>8.25</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td></tr><tr><td>No-error</td><td>35.09</td><td>28.00</td><td>27.34</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Min</td><td>33.18</td><td>26.22</td><td>26.19</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Max</td><td>39.10</td><td>30.84</td><td>30.34</td><td>0</td><td>69</td><td>70</td><td>0</td><td>0</td><td>0</td></tr><tr><td>|Min-Max|</td><td>5.91</td><td>4.62</td><td>4.15</td><td>0</td><td>69</td><td>70</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Mean</td><td>35.56</td><td>28.12</td><td>27.94</td><td>0</td><td>26.71</td><td>26.75</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Standard deviation</td><td>1.26</td><td>0.94</td><td>0.75</td><td>0</td><td>18.08</td><td>20.73</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Coefficient of variation</td><td>3.53</td><td>2.80</td><td>2.70</td><td>0</td><td>67.71</td><td>77.50</td><td>0</td><td>0</td><td>0</td></tr><tr><td>2.1</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td></tr><tr><td>No-error</td><td>17.54</td><td>16.61</td><td>15.83</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Min</td><td>16.56</td><td>15.31</td><td>14.97</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Max</td><td>19.86</td><td>18.10</td><td>18.49</td><td>0</td><td>47</td><td>44</td><td>0</td><td>34</td><td>42</td></tr><tr><td>|Min-Max|</td><td>3.31</td><td>2.79</td><td>3.52</td><td>0</td><td>47</td><td>44</td><td>0</td><td>34</td><td>42</td></tr><tr><td>Mean</td><td>18.00</td><td>16.66</td><td>16.32</td><td>0</td><td>19.71</td><td>13.63</td><td>0</td><td>13.30</td><td>14.44</td></tr><tr><td>Standard deviation</td><td>0.69</td><td>0.59</td><td>0.68</td><td>0</td><td>16.11</td><td>12.77</td><td>0</td><td>10.79</td><td>12.38</td></tr><tr><td>Coefficient of variation</td><td>4.25</td><td>3.52</td><td>3.83</td><td>0</td><td>81.73</td><td>93.70</td><td>0</td><td>81.15</td><td>85.73</td></tr><tr><td>1.13</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td></tr><tr><td>No-error</td><td>9.65</td><td>8.51</td><td>6.80</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Min</td><td>9.08</td><td>7.96</td><td>6.48</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Max</td><td>10.51</td><td>9.00</td><td>7.88</td><td>0</td><td>50</td><td>69</td><td>0</td><td>0</td><td>0</td></tr><tr><td>|Min-Max|</td><td>1.43</td><td>1.04</td><td>1.40</td><td>0</td><td>50</td><td>69</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Mean</td><td>9.72</td><td>8.51</td><td>6.85</td><td>0</td><td>23.83</td><td>32.00</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Standard deviation</td><td>0.31</td><td>0.22</td><td>0.17</td><td>0</td><td>16.56</td><td>22.74</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Coefficient of variation</td><td>3.22</td><td>2.53</td><td>2.55</td><td>0</td><td>69.49</td><td>71.06</td><td>0</td><td>0</td><td>0</td></tr></table>

Coefficient of variation = (Standard deviation/Mean) × 100 %.

shown in Fig. 11. For the traditional control strategy, the cooling ca pacity of water tank (5) and the cold release pump water flow rate (9) have no impact on the system. This is because the traditional control strategy does not perform cold storage/release regulation. For MPC-1 and DMPC strategies, in addition to COP (6) and cooling tower approaching temperature (3), the cooling capacity of water tank (5) also has a significant impact. Due to the impact of approaching temperature (3) on the COP of the chiller, it has a significant impact on the system. The cooling capacity of water tank (5) can affect the cooling capacity of the chiller and affect the regulation of cold storage/release in the later period. Therefore, the impact of the cooling capacity of water tank (5) is also relatively large. The influence of equipment parameters is COP (6) > cooling water flow rate (7) > chilled water flow rate (8).

![](images/accedc7c573dd45c5423bd5a8163eadd7fe17fd4126437d25169e9fa189d65f4.jpg)  
(a) Traditional control strategy.

![](images/9f3a0a5d1322d10a3932a97755ce40cf365e6fd55a02329d4ee05e54026042ed.jpg)

(b) MPC-1 strategy.  
![](images/d69211d79896e0e7e720f4d03eff75bd38d8636fd852222b322cfc1a117298cb.jpg)  
(c) DMPC strategy.  
Fig. 11. Results from the Morris sensitivity analysis on August 25th

The results from the Morris sensitivity analysis on February 1st are shown in Fig. 12. February 1st includes a combination of hybrid and free cooling conditions throughout the day. For Traditional and MPC-1 strategies, due to being in hybrid cooling mode for most of the time, COP (6) still has the greatest impact. However, due to free cooling participating in refrigeration throughout the day, the effects of cooling water pump flow rate (7) and chilled water pump flow rate (8) are also significant, with little difference from COP (6). For DMPC strategy, the free cooling time is increased by adjusting the approaching temperature (3), so the impact of COP (6) is not the greatest. The wet-bulb temper ature (2) and approaching temperature (3) directly affect the opening time of the cooling units, so these two parameters have a greater impact.

The results from the Morris sensitivity analysis on January 13th are shown in Fig. 13. January 13th represents a full day of free cooling mode. COP (6) has no impact on system performance. The chilled water pump flow rate (8) and cooling water pump flow rate (7) have the greatest impact. During free cooling mode, MPC-1 strategy operates at the rated flow rate of the chilled water pump, with a larger storage/ release capacity than DMPC strategy. Therefore, the impact of the cooling capacity of water tank (5) of MPC-1 is greater than DMPC strategy.

## 5. Conclusions

An advanced double-layer model predictive control (DMPC) strategy is proposed for hybrid cooling system with cold storage technology in data center. In the DMPC strategy, the first-layer is the optimization of operating parameters using mixed integer linear programming (MILP) algorithm, and the second-layer optimizes the set parameter based on the optimization of the first layer using particle swarm optimization (PSO) algorithm. The theoretical operational performance of DMPC strategy is impressive, but uncertainties in models, sensors, and actua tors may hinder the practical application. Therefore, the influence of uncertainty on the control performance of hybrid cooling system is studied in this paper, including the control performance quantification of uncertainty parameters and sensitivity identification of uncertain parameters. The conclusions are summarized as follows.

![](images/c52862f00f855b132e6fccc45d45b1a2a5f70399156b974149f422b3432219ff.jpg)  
(a) Traditional control strategy.

![](images/94f4fdd43fb7071d17ef2d87edfbd2a6a711545849259fbbf643488b84c46df8.jpg)  
(b) MPC-1 strategy.

![](images/0a39edc9c62eabafc0bbc39a737902584b5c1936f68a072083d12347e827fe65.jpg)  
(c) DMPC strategy.  
Fig. 12. Results from the Morris sensitivity analysis on February 1st.

(1) The DMPC strategy reduces the running time of the chiller and increases the time of free cooling. And the DMPC strategy can prevent the chillers from operating at very low loads.

(2) Among the three control strategies, uncertainty has the greatest impact on the control performance of the DMPC strategy. The cooling and cold storage/release modes prediction error rate of DMPC strategy is highest, and the coefficient of variation of energy consumption (average 3.03) is also the largest. Therefore, the DMPC strategy is more sensitive to uncertain parameters.

(3) The following are the key parameters for identification: the un certainty of execution parameters in approaching temperature; the uncertainty of measurement parameters in wet-bulb tem perature, cooling water pump flow rate, chilled water pump flow rate and cooling capacity of water tank; and model uncertainties in COP. The wet-bulb temperature and cooling tower approach ing temperature are the most significant parameters affecting the control performance in hybrid cooling mode.

## CRediT authorship contribution statement

Yiqun Zhu: Writing – original draft, Software, Methodology, Formal analysis, Data curation, Conceptualization. Quan Zhang: Writing – re view & editing, Supervision, Project administration, Funding acquisition, Conceptualization. Gongsheng Huang: Writing – review & editing, Supervision, Software. Jiaqiang Wang: Writing – review & editing, Software, Funding acquisition. Sikai Zou: Writing – review & editing, Funding acquisition.

![](images/c01edf596b4c09986a8376d98c8d30dc213ae62cb14b3beeda7ef90e8a30f4fa.jpg)  
(a) Traditional control strategy.

![](images/cf8c3af21c4912553cb615b0638b15f4632eead2f78551d52f72cea01da0893b.jpg)

(b) MPC-1 strategy.  
![](images/8a20f46047a1a0a33a26cb093d3a86e4e045bab479af53368a8f1e13ff8ae24f.jpg)  
(c) DMPC strategy.  
Fig. 13. Results from the Morris sensitivity analysis on January 13th.

## Declaration of competing interest

The authors declare that they have no known competing financial interests or personal relationships that could have appeared to influence the work reported in this paper.

## Acknowledgments

This work was supported by the National Natural Science Found of China [grant number 52178073, 52108101]; International Science and Technology Innovation Cooperation Projects between Governments [grant number 2023YFE0120400]; The National Sustainable

Development Agenda Innovation Demonstration Zone Construction Program in Chenzhou [grant number 2022SFQ28]; Jiangxi Provincial Natural Science Foundation [grant number 20232BAB214070]; Key Core Technology Research Project of the Xiangjiang New Area Science and Technology Innovation and Industry Promotion Bureau in Changsha City: Key Technology Research and Industrialization of 5G Base Station Modular Hybrid Cooling System.

## Data availability

Data will be made available on request.

## References

[1] Zhang K. Zhang Y. Liu J. Niu X. Recent advancements on thermal management and evaluation for data centers. Appl Therm Eng 2018:142:215–31.

[2] Mi R. Bai X. Xu X. Ren F. Energy performance evaluation in a data center with water-side free cooling. Energy Build 2023:113278.

[3] Wang Y, Bai X, Fu Y, Tang Y, Jin C, Li Z. Field experiment and numerical simulation for airflow evaluation in a data center with row-based cooling. Energy Build 2023;294:113231.

[4] Zhou F, Shen C, Ma G, Yan X. Power usage effectiveness analysis of a liquid-pumpdriven hybrid cooling system for data centers in subclimate zones. Sustain Energy Technol Assessments 2022:52:102277.

[5] Zou S, Zhang Q, Yue C. Comparative study on different energy-saving plans using water-side economizer to retrofit the computer room air conditioning system. J Build Eng 2023:69:106278.

[6] Amado EA, Schneider PS, Bresolin CS. Free cooling potential for Brazilian data centers based on approach point methodology. Int J Refrig 2021:122:171-80.

[7] Deymi-Dashtebayaz M, Namanlo SV. Potentiometric and economic analysis of using air and water-side economizers for data center cooling based on various weather conditions. Int J Refrig 2019:99:213–25.

[8] Meng F, Zhang Q, Zou S, Zhu X, Liu L, Chen S. Operating parameters optimization of a thermosyphon and compressor system used in 5G TBS. Appl Therm Eng 2024; 241:122331.

[9] Wang S, Ma ZJH. Supervisory and Optimal Control of Building HVAC Systems: A Review. HVAC&R Research 2008;14:3–32.

[10] Wang J, Zhang Q, Yu Y, Chen X, Yoon S. Application of model-based control strategy to hybrid free cooling system with latent heat thermal energy storage for TBSs. Energy Build 2018;167:89–105.

[11] Ma X, Zhang Q, Zou S. An experimental and numerical study on the thermal performance of a loop thermosyphon integrated with latent thermal energy storage for emergency cooling in a data center. Energy 2022;253:123946.

[12] Zou S, Zhang Q, Yu Y, Yue C, Wang J, Ma X. Field study on the self-adaptive capacity of multi-split heat pipe system (MSHPS) under non-uniform conditions in data center. Appl Therm Eng 2019;160:113999.

[13] Liu Y, Wei X, Xiao J, Liu Z, Xu Y, Tian Y. Energy consumption and emission mitigation prediction based on data center traffic and PUE for global data centers. Global Energy Interconnection 2020;3:272–82.

[14] Shan K, Fan C, Wang JJE. Model predictive control for thermal energy storage assisted large central cooling systems 2019;179:916–27.

[15] Ma Y, Borrelli F, Hencey B, Coffey B, Bengea S, Haves P. Model predictive control for the operation of building cooling systems. IEEE American Control Conference 2012:20:796–803.

[16] Zhu Y, Zhang Q, Zeng L, Wang J, Zou S, Zheng H. An advanced control strategy for optimizing the operation state of chillers with cold storage technology in data center. Energy Build 2023;301:113684.

[17] Chen Y, Zhang Y, Meng Q. Study of ventilation cooling technology for telecommunication base stations: control strategy and application strategy. Energy Build 2012:50:212–8

[18] Guo Y, Li G, Chen H, Hu Y, Shen L, Li H, Hu M, Li J. Development of a virtual variable-speed compressor power sensor for variable refrigerant flow air conditioning system. Int J Refrig 2017:74:73–85.

[19] Wang J, Zhang Q, Yu Y. An advanced control of hybrid cooling technology for telecommunication base stations. Energy Build 2016:133:172–84.

[20] Zou S, Zhang Q, Yue C, Wang J, Du S. Study on the performance and free cooling potential of a R32 loop thermosyphon system used in data center. Energy Build 2022:256:111682.

[21] Wang Z, Zhang W, Fan H, Zhang C, Zhao Y, Huang Z. An uncertainty-tolerant robust distributed control strategy for building cooling water systems considering measurement uncertainties. J Build Eng 2023;76:107162.

[22] Bae Y, Bhattacharya S, Cui B, Lee S, Li Y, Zhang L, Im P, Adetola V, Vrabie D, Leach M, Kuruganti T. Sensor impacts on building and HVAC controls: a critical review for building energy performance. Advances in Applied Energy 2021;4: 100068.

[23] Huang B, Huang S, Ma X, Katipamula S, Wu D, Lutes R. Stochastic scheduling for commercial building cooling systems: considering uncertainty in zone temperature prediction. Appl Energy 2023;346:121367.

[24] Lin L, Chen G, Liu X, Liu X, Zhang T. Characterizing cooling load in multi-area airport terminal buildings: clustering and uncertainty analysis for energy flexibility. J Build Eng 2023;79:107797.

[25] Wang J, Zhang Q, Yoon S, Yu Y. Impact of uncertainties on the supervisory control performance of a hybrid cooling system in data center. Build Environ 2019;148: 361–71.

[26] Dong F, Wang J, Xie K, Tian L, Ma Z. An interval prediction method for quantifying the uncertainties of cooling load based on time classification. J Build Eng 2022;56: 104739.

[27] Jin S, Li Y. Analyzing the performance of electricity, heating, and cooling supply nexus in a hybrid energy system of airport under uncertainty. Energy 2023;272: 127138.

[28] Liu Z, Zhou X, Tian W, Liu X, Yan D. Impacts of uncertainty in building envelope thermal transmittance on heating/cooling demand in the urban context. Energy Build 2022;273:112363.

[29] Kim SH. An evaluation of robust controls for passive building thermal mass and mechanical thermal energy storage under uncertainty. Applied energy 2013;111: 602–23.

[30] Yoon S, Yu Y. Hidden factors and handling strategies on virtual in-situ sensor calibration in building energy systems: prior information and cancellation effect. Appl Energy 2018;212:1069–82.

[31] Yoon S, Yu Y. Extended virtual in-situ calibration method in building systems using Bayesian inference. Autom ConStruct 2017;73:20–30.

[32] Deng K, Sun Y, Li S, Lu Y, Brouwer J, Mehta PG, Zhou MC, Chakraborty AJAS. Model predictive control of central chiller plant with thermal energy storage via dynamic programming and mixed-integer linear programming, automation science and engineering. IEEE Transactions on 2015;12:565–79.

[33] Eberhart Y Shi. Particle swarm optimization: developments, applications and resources. Congr Evol Comput 2002;1:81–6.

[34] Saltelli A, Annoni P, Azzini I, Campolongo F, Ratto M, Tarantola S. Variance based sensitivity analysis of model output. Design and estimator for the total sensitivity index. Comput Phys Commun 2010:181:259–70

[35] Xia L, Ma Z, Kokogiannakis G, Wang Z, Wang S. A model-based design optimization strategy for ground source heat pump systems with integrated photovoltaic thermal collectors. Appl Energy 2018:214:178–90.

[36] Campolongo F. Cariboni J. Saltelli AJEM. An effective screening design for sensitivity analysis of large models. Environ Model Software 2007:22:1509–18

[37] Sohier H, Farges J-L, Piet-Lahanier H. Improvement of the representativity of the Morris method for air-launch-to-orbit separation. JFAC Proc Vol 2014:47:7954–9