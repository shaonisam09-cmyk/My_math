RESEARCH ARTICLE

Xiao GUO, Yanbo CHE, Zhihao ZHENG, Jiulong SUN

# Multi-timescale optimization scheduling of interconnected data centers based on model predictive control

© Higher Education Press 2023

Abstract With the promotion of “dual carbon” strategy, data  center  (DC)  access  to  high-penetration  renewable energy sources (RESs) has become a trend in the industry. However,  the  uncertainty  of  RES  poses  challenges  to  the safe and stable operation of DCs and power grids. In this paper,  a  multi-timescale  optimal  scheduling  model  is established  for  interconnected  data  centers  (IDCs)  based on  model  predictive  control  (MPC),  including  day-ahead optimization,  intraday  rolling  optimization,  and  intraday real-time  correction.  The  day-ahead  optimization  stage aims at the lowest operating cost, the rolling optimization stage  aims  at  the  lowest  intraday  economic  cost,  and  the real-time  correction  aims  at  the  lowest  power  fluctuation, eliminating  the  impact  of  prediction  errors  through coordinated  multi-timescale  optimization.  The  simulation results show that the economic loss is reduced by 19.6%, and the power fluctuation is decreased by 15.23%.

Keywords model  predictive  control,  interconnected data  center,  multi-timescale,  optimized  scheduling, distributed power supply, landscape uncertainty

## 1    Introduction

In recent years, data center (DC), as an important part of the  Internet  and  information  technology,  have  been developing  rapidly  and  expanding  in  scale  [1–3]. However,  the  ensuing  problem  of  high  energy consumption in DCs cannot be ignored [4]. In the context of  the  “dual  carbon”  strategy,  a  high  percentage  of renewable  energy  sources  (RESs)  access  has  become  an important  means  of  reducing  operational  energy consumption,  improving  energy  utilization  efficiency, and  promoting  the  green  and  low-carbon  transformation of DC. In addition, the uncertainty of RES poses a major challenge to optimal scheduling and energy management in DC [5–7]. Therefore, it is of great significance to fully consider  the  uncertainty  problem  of  renewable  energy and make use of the flexible resources of DCs for optimal scheduling  in  order  to  reduce  the  operating  cost  of  DCs and improve the stability of DC operation.

Distinguished  from  flexible  loads  such  as  air conditioning  and  electric  heating,  DC  loads,  as  typical high-energy  loads,  not  only  flexibly  regulate  the  load characteristics  from  time  to  time,  but  also  achieve  the spatial  transfer  of  electric  loads  through  the  transfer  of arithmetic power, which is a high-quality load regulation resource  [8–10].  In  addition  to  servers,  DCs  have  other auxiliary  equipment,  such  as  cooling,  energy  storage (ES),  generators,  and  RES  power  supply,  which  bring flexibility  to  the  operation  [11–14].  To  give  full  play  to the  advantages  of  the  DC,  it  is  necessary  to  formulate  a reasonable and effective scheduling strategy to coordinate the operation of multi-equipment.

Hitherto,  the  research  on  DC  operation  optimization has mainly focused on flexibility in mobilizing resources, energy  forms,  and  ES  devices  [15].  Yuan  et  al.  [16,17] took into account the spatial diversity of Internet service provider  (ISP)  bandwidth  prices,  electricity  prices,  and renewable green energy availability, and made full use of the  spatio-temporal  flexibility  of  DC  workloads  for optimal scheduling, which effectively reduced the energy cost and improved the capacity of RES consumption.

In  Cioara  et  al.  [18],  a  flexible  mechanism  and  model was  defined  for  each  component  of  a  DC  (electrical cooling system, IT workloads, ES, and diesel generators). Optimization  measures  such  as  load  time  shifting, alternative use of non-electrical cooling equipment (e.g., thermal storage), or charging and discharging of ES were used  to  increase  the  capacity  of  RES  consumption.  In Chen  et  al.  [19],  a  system  framework  was  proposed  to integrate RES, distributed storage units, cooling facilities, and  dynamic  pricing  into  the  workloads  and  energy management  tasks  of  a  DC  network,  which  could efficiently  utilize  multiple  resources  to  improve  the energy efficiency and sustainability of a DC. In Wang et al. [20], a mixed-integer linear programming model was developed  to  solve  the  day-ahead  resource  planning problem with the objective of minimizing the energy cost and  carbon  emission  cost  by  considering  conventional generators, renewable resources, and ES systems.

The  above  optimization  controls  are  all  based  on  load and RES forecasting information accurately. However, in actual  operation,  the  predicted  values  of  RES  and  load deviate from the real values, which brought challenges to system  operation  control  [21].  To  deal  with  the uncertainty  in  prediction,  methods  such  as  robust planning  [22]  and  stochastic  planning  [23–25]  had  been applied.

Most  of  the  above  methods  adopt  advance  scheduling strategies  for  multiple  time  periods.  Although  a  rolling optimization  method  can  be  adopted  for  rolling scheduling,  the  above  scheduling  models  are  all approximate models, which cannot fully meet the demand for  online  optimization  and  adjustment  in  the  actual operation  of  the  system.  The  model  predictive  control (MPC) method based on the idea of rolling optimization and  feedback  correction  can  better  solve  the  problem  of inaccurate prediction, with a better robustness. In Paul et al.  [26],  an  MPC-based  energy-aware  scheduling algorithm was proposed to address the resource allocation problem  of  deferrable  jobs  in  the  tiered  architecture  of DCs,  which  effectively  reduced  electricity  bills  and improved  RES  consumption.  Wu  et  al.  [27]  used predictive  data  and  prediction  error  distribution  for  realtime  energy  management  of  DCs  based  on  the  MPC algorithms,  taking  into  account  factors  such  as  RES, dynamic  tariffs,  batteries,  thermal  storage  tanks,  and delayed  execution  of  batch  jobs.  In  Zhu  et  al.  [28],  a stochastic MPC based multi-temporal optimal scheduling method for DC microgrids were proposed for two phases, i.e.,  day-ahead  and  intraday.  Intraday  scheduling  made use  of  the  rolling  optimization  and  feedback  correction functions of MPC to correct deviations in load and RES output,  and  adjusted  the  day-ahead  scheduling  plan  in real  time,  which  ensured  the  effectiveness  of  the  dayahead  plan  and  the  stability  of  the  system  operation. Although  the  above  studies  have  enhanced  the adaptability  to  the  inaccuracy  of  prediction  information by using the MPC method, they do not take into account the  error  between  the  prediction  information  and  the small-timescale  operation  information,  and  thus  cannot meet  the  demand  of  the  small-timescale  dynamic adjustment during the operation of the system.

Accordingly, in this paper multiple flexibility resources are  considered  for  optimal  scheduling  of  interconnected DCs  on  multiple  timescales.  This  paper  is  contributive because  it  introduces  the  interconnected  data  center (IDC)  system  structure  and  energy  consumption characteristics. In addition, considering the uncertainty of intraday  scenery  output  and  the  short-sighted  effect  of real-time  optimization,  it  proposes  a  multi-timescale optimal  scheduling  model  for  IDC,  i.e.,  a  day-ahead optimal  scheduling  with  the  objective  of  minimum operating  cost,  an  intraday  rolling  optimal  scheduling with  the  objective  of  minimum  intraday  economic  cost, and  a  real-time  corrective  scheduling  model  with  the objective  of  minimum  power  fluctuation,  respectively. Moreover, it solves the model using alternating direction method  of  multipliers  (ADMMs)  to  verify  the reasonableness  and  effectiveness  of  the  scheduling strategy proposed in this paper.

## 2    DC model

Figure 1  shows  the  basic  structure  of  the  IDC  system, which  is  mainly  composed  of  an  IDC,  a  DC  microgrid, and  a  DC  operator.  The  IDC  contains  the  geographical dispersed  multiple  IDCs  as  well  as  the  interconnection network,  which  can  provide  customers  with  DC consolidation,  business  information  collaboration,  cloud data  sharing,  data  disaster  recovery  and  backup,  virtual machine migration, and other conveniences, and realizes flexible  expansion  of  DC  scale  and  flexible  business deployment  [29].  The  DC  microgrid  contains  ES equipment, combustion turbine engines (CTEs) and RES, and  other  power  supply  equipment,  which  serves  as  a carrier  for  energy  transmission  to  provide  power resources  for  equipment  operation,  and  meets  the  power quality  requirements  of  high  reliability  and  stability  of various  equipment  in  the  DC.  The  DC  operator  is  a service  provider  or  platform  responsible  for  managing and operating one or more centers. As a representative of all  the  DCs  under  its  management,  it  has  the  right  to allocate the data loads in the region under its jurisdiction, and  can  participate  in  games  and  contracts  between  the DC and other interested parties.

The  energy  consumption  of  the  DC  is  modeled according to its energy consumption structure,

$$
P _ {i, t} ^ {\text { load }} = P _ {i, t} ^ {\text { server }} + P _ {i, t} ^ {\text { cool }} + P _ {i, t} ^ {\text { other }},\tag{1}
$$

where $P _ { i , t } ^ { \mathrm { l o a d } }$ is the total energy consumption power of the DC, $P _ { i , t } ^ { \mathrm { c o o l } }$ is the power of DC refrigeration equipment, and $P _ { i , t } ^ { \mathrm { o t h e r } }$ is the power for other equipment in the DC.

The  server  power  consumption  is  modeled  based  on server utilization

$$
\begin{array}{l} P _ {i, t} ^ {\text {server}} = e _ {\text {idle}} ^ {\alpha} m _ {i, t} ^ {\alpha} + \frac {e _ {\text {peak}} ^ {\alpha} - e _ {\text {idle}} ^ {\alpha}}{\mu^ {\alpha}} \alpha_ {i, t} + e _ {\text {idle}} ^ {\beta} m _ {i, t} ^ {\beta} \\ \qquad + \sum_ {q \in Q} \frac {e _ {\text {peak}} ^ {\beta} - e _ {\text {idle}} ^ {\beta}}{\mu^ {\beta}} \beta_ {i, q, t}, \end{array}\tag{2}
$$

where $e _ { \mathrm { i d l e } } ^ { \alpha }$ and $e _ { \mathrm { p e a k } } ^ { \alpha }$ are  the  no-load  power  and  full-load power of a single server processing interactive workload respectively, $e _ { \mathrm { i d l e } } ^ { \beta }$ and $e _ { \mathrm { p e a k } } ^ { \beta }$ are respectively no-load power and  full-load  power  of  a  single  server  processing  batch workload, $\alpha _ { i , t }$ is  the  processing  capacity  of  interactive workloads, $\beta _ { i , q , t }$ q is  the  processing  capacity  of  Class batch  workload, $m _ { i , t } ^ { \alpha }$ is  the  number  of  servers  processing interactive  workloads, $m _ { i , t } ^ { \beta }$ is  the  number  of  servers processing  batch  workloads, $\mu ^ { \alpha }$ is  the  rate  at  which interactive workloads are handled for a single server, and $\mu ^ { \beta }$ is the rate at which batch workloads are handled for a single server.

![](images/457c38d9ce517817b3c3b3cbe6593bd7882c44859db6caf8a53d9b72ce91c633.jpg)  
Fig. 1    Basic structure of DC system.

The  energy  consumption  of  DC  cooling  equipment  is modeled based on linear principles

$$
P _ {i, t} ^ {\mathrm{cool}} = \lambda_ {1} h _ {i, t} + e ^ {\mathrm{cool}},\tag{3}
$$

where $h _ { i , t }$ is the refrigeration capacity of DC refrigeration equipment, $\lambda _ { 1 }$ is  the  performance  coefficient  of  the refrigeration  equipment,  and $e ^ { \mathrm { c o o l } }$ is  the  basic  energy consumption power of refrigeration equipment.

The  indoor  temperature  change  model  of  the  DC  is established as

$$
\begin{array}{l} T _ {i, t} ^ {\text {in}} = T _ {i, t - 1} ^ {\text {in}} e ^ {- \frac {\Delta t}{\lambda_ {2} \lambda_ {3}}} + T _ {i, t} ^ {\text {out}} \left(1 - e ^ {- \frac {\Delta t}{\lambda_ {2} \lambda_ {3}}}\right) \\ \qquad + \lambda_ {2} \left(1 - e ^ {- \frac {\Delta t}{\lambda_ {2} \lambda_ {3}}}\right) \left(- h _ {i, t} + P _ {i, t} ^ {\text {server}} + P _ {i, t} ^ {\text {other}}\right), \end{array}\tag{4}
$$

where $T _ { i , t } ^ { \mathrm { i n } }$ is the indoor temperature of the DC, $T _ { i , t } ^ { \mathrm { o u t } }$ is the outdoor  temperature  of  the  DC,  and $\lambda _ { 2 }$ and $\lambda _ { 3 }$ are  the equivalent thermal parameter of the DC building.

## 3    Multi-timescale optimal scheduling model for IDCs

## 3.1    Multi-timescale scheduling framework

The  multi-timescale  optimal  scheduling  framework  is divided  into  day-ahead  optimization  and  intraday optimization, where intraday optimization is divided into rolling optimization and real-time correction, as shown in Fig. 2.

Day-ahead  optimized  scheduling  takes  the  next  day 24 h as the optimization cycle, with a time interval of 1 h, one  scheduling  plan  being  developed  for  each  day. Intraday  rolling  optimization  scheduling  considers  the short-term forecast results of wind and light output, with control  time  domains  t  to  T  as  the  optimization  cycle, with  a  time  interval  of  1  h,  and  one  scheduling  plan  is executed  every  one  hour,  and  only  scheduling  plan  for time  t  is  executed.  Intraday  real-time  correction scheduling  is  based  on  the  ultra-short-term  prediction results  of  wind-power  with  the  control  time  domain t + n∆t t + 1to    as  the  optimization  period,  the  time interval of 15 min. It is executed once every 15 min, and t + n∆tonly the scheduling plan for the time   is executed.

## 3.2    Day-ahead optimal scheduling model

According  to  the  day-ahead  predicted  values  of  RES output, workload and environmental parameters, the dayahead optimal scheduling model of IDC takes the lowest total  operation  cost  as  the  objective  function  to  develop the  next  day  workload  scheduling  and  energy management  strategy,  provides  reference  for  day-day operation,  and  reports  the  power  purchase  plan  to  the power grid company.

## 3.2.1    Objective function

The  objective  function  of  the  day-ahead  optimal scheduling model of the connected DC is

![](images/020309d23184ffcc1bcb76d0e4cb57906c17e36507533379c966fe6a22656074.jpg)  
Fig. 2    Multi-timescale optimal scheduling framework.

$$
\min \sum_ {i \in N} \sum_ {t \in T} (C _ {i, t} ^ {\text { grid }} + C _ {i, t} ^ {\text { gas }} + C _ {i, t} ^ {\text { ch }}),\tag{5}
$$

i Nwhere   is the number of the DC,   is the total number of DCs, T is the optimized time period, $C _ { i , t } ^ { \mathrm { g r i d } }$ is the electricity tpurchasing cost of the grid at the time   of the DC $i , C _ { i , t } ^ { \mathrm { g a s } }$ is  the  CTE  operation  cost  of  the  DC,  and $C _ { i , t } ^ { \mathrm { c h } }$ is  the operation  cost  of  data  center  energy  storage  (DCES) equipment.

(1) Power purchase cost of power grid

The calculation formula of power purchase cost of DC network is

$$
C _ {i, t} ^ {\text { grid }} = P _ {i, t} ^ {\text { grid }} \gamma_ {i, t} ^ {\text { grid }} \Delta t,   \forall t \in T,   \forall i \in N,\tag{6}
$$

where $P _ { i , t } ^ { \mathrm { g n d } }$ is the power consumption of DC grid, $\gamma _ { i , t } ^ { \mathrm { g n d } }$ is the  power  grid  electricity  price  of  the  region  where  the DC  is  located,  and $\Delta t$ is  the  optimized  scheduling  time interval.

## (2) CTE operation cost

CTEs  generate  electric  energy  by  burning  natural  gas, and  the  relationship  between  fuel  cost  and  the  power  of CTEs in DCs can be expressed as

$$
C _ {i, t} ^ {\text { gas }} = \frac {\gamma_ {i} ^ {\text { gas }} P _ {i , t} ^ {\text { gas }} \Delta t}{Q _ {\mathrm{LHV}} \eta_ {\mathrm{MT}}}, \forall t \in T, \forall i \in N,\tag{7}
$$

where $\gamma _ { i } ^ { \mathrm { g a s } }$ is  the  gas  purchase  cost  per  unit  power generation  of  the  CTE, $P _ { i , t } ^ { \mathrm { g a s } }$ is  CTE  generating  power, $Q _ { \mathrm { L H V } }$ is  the  heat  value  of  the  CTE,  and $\eta _ { \mathrm { M T } }$ is  the generating efficiency of the CTE.

(3) Operation cost of ES equipment

Energy loss and battery loss will occur in the charging and  discharging  process  of  DCES  equipment,  and  the operation cost of ES equipment is

$$
C _ {i, t} ^ {\mathrm{ch}} = \left(P _ {i, t} ^ {\mathrm{bc}} + P _ {i, t} ^ {\mathrm{bd}}\right) \gamma_ {i} ^ {\mathrm{ch}} \Delta t, \forall t \in T, \forall i \in N,\tag{8}
$$

where $P _ { i , t } ^ { \mathrm { b c } }$ and $P _ { i , t } ^ { \mathrm { b d } }$ respectively  are  the  charging  and discharging power of DCES equipment, and $\gamma _ { i } ^ { \mathrm { { c h } } }$ is the unit power usage cost of the ES equipment.

## 3.2.2    Constraint conditions

(1) Power balance constraints

The  power  supply  in  the  micro-grid  of  the  DC  is optimized to meet the energy demand of the DC, and the RES  is  optimized  to  provide  electric  energy.  The  power balance constraint conditions are

$$
P _ {i, t} ^ {\text { load }} = P _ {i, t} ^ {\text { grid }} + P _ {i, t} ^ {\text { bd }} - P _ {i, t} ^ {\text { bc }} + P _ {i, t} ^ {\text { gas }} + P _ {i, t} ^ {\text { wind }} + P _ {i, t} ^ {\text { solar }}, \forall t \in T, \forall i \in N,\tag{9}
$$

where $P _ { i , t } ^ { \mathrm { w i n d } }$ is  the  power  generated  by  the  fan  in  the microgrid of the DC, and $P _ { i , t } ^ { \mathrm { s o l a r } }$ is the photovoltaic power generation power in the microgrid of DC.

(2) Constraints of ES equipment

ES equipment should meet the upper and lower limits of charge and discharge power requirements and capacity requirements, whose constraint conditions are

$$
0 \leqslant P _ {i, t} ^ {\mathrm{bd}} \leqslant P ^ {\mathrm{bd-max}}, \forall t \in T, \forall i \in N,\tag{10}
$$

$$
0 \leqslant P _ {i, t} ^ {\mathrm{bc}} \leqslant P ^ {\mathrm{bc-max}}, \forall t \in T, \forall i \in N,\tag{11}
$$

$$
L ^ {\min} \leqslant L _ {i, t} \leqslant L ^ {\max}, \forall t \in T, \forall i \in N,\tag{12}
$$

P<sup>bc-max</sup> where    and $P ^ { \mathrm { b d . } }$ <sup>-max</sup>    are  respectively  the  maximum charging  and  discharging  power  of  the  ES  device  in  the $\mathrm { D C } , \eta ^ { \mathrm { b c } }$ and $\eta ^ { \mathrm { b d } }$ are  respectively  the  charging  and discharging  efficiency  of  the  ES  equipment, $L _ { i , t }$ is  the capacity  of  the  ES  equipment,  and $L ^ { \mathrm { m i n } }$ and $L ^ { \mathrm { m a x } }$ are respectively  the  minimum  and  maximum  capacity requirements of the ES equipment.

The capacity of the ES device can be obtained by

$$
L _ {i, t} = L _ {i, t - 1} + P _ {i, t} ^ {\mathrm{bc}} - P _ {i, t} ^ {\mathrm{bd}}, \forall t \in T, \forall i \in N.\tag{13}
$$

In addition, the capacity value of the ES device in the initial period and the end period of the scheduling cycle should be consistent, expressed as

$$
\begin{array}{l} L _ {i, t} = L _ {i, 0}, \forall t \in T, \forall i \in N, \\ P ^ {\text { gas - min }} \leqslant P _ {i, t} ^ {\text { gas }} \leqslant P ^ {\text { gas - max }}, \forall t \in T, \forall i \in N. \end{array}\tag{14}
$$

(3) Constraint conditions of CTE

The  CTE  of  DC  should  meet  output  upper  and  lower limit constraints and climbing constraints, expressed as

$$
P ^ {\text { gas - min }} \leqslant P _ {i, t} ^ {\text { gas }} \leqslant P ^ {\text { gas - max }}, \forall t \in T, \forall i \in N,\tag{15}
$$

$$
R ^ {\text { gas - min }} \leqslant P _ {i, t - 1} ^ {\text { gas }} - P _ {i, t} ^ {\text { gas }} \leqslant R ^ {\text { gas - max }}, \forall t \in T, \forall i \in N,\tag{16}
$$

P<sup>gas-min</sup> P<sup>gas-max</sup> where    and    respectively  are  the  upper  and <sup>gas-max</sup> <sup>gas-min</sup>lower  limits  of  CTE  output,  and    and    are respectively the upper and lower limits of CTE climbing power.

(4) Working load constraints

Interactive  workload  should  meet  load  conservation when scheduling among multiple DCs, whose constraint

conditions are

$$
\sum_ {i \in N} \alpha_ {i, t} = \sum_ {i \in N} \varphi_ {i, t} ^ {\alpha}, \forall t \in T,\tag{17}
$$

where $\varphi _ { i , t } ^ { \alpha }$ i  is the interactive work load arriving at DC   at ttime  .

Batch workloads can be scheduled on a spatiotemporal scale  and  need  to  be  processed  within  the  deadline.  The constraints are

$$
\begin{array}{c} \sum_ {t = 1} ^ {\tau + T _ {q} ^ {\beta}} \beta_ {i, q, t} \geqslant \sum_ {t = 1} ^ {\tau} \varphi_ {i, q, t} ^ {\beta}, \\ \forall i \in N,   \forall q \in Q,   \forall \tau \in \left[ 1, T - T _ {q} ^ {\beta} \right], \end{array}\tag{18}
$$

$$
\begin{array}{c} \sum_ {t = 1} ^ {T} \beta_ {i, q, t} \geqslant \sum_ {t = 1} ^ {\tau} \varphi_ {i, q, t} ^ {\beta}, \\ \forall i \in N,   \forall q \in Q,   \forall \tau \in \left[ T - T _ {q} ^ {\beta}, T \right], \end{array}\tag{19}
$$

$$
\sum_ {t = 1} ^ {\tau} \beta_ {i, q, t} \leqslant \sum_ {t = 1} ^ {\tau} \varphi_ {i, q, t} ^ {\beta}, \quad \forall i \in N,   \forall q \in Q,   \forall \tau \in T,\tag{20}
$$

where $\varphi _ { i , q , t } ^ { \beta }$ is  the  batch  work  load  of  Class $q$ arriving  at iDC   at time $t , T _ { q } ^ { \beta }$ is the maximum delay processing time for Class $q$ τ  batch workloads, and   is any time slot within the scheduling cycle.

(5) Load service level agreement constraints

To  ensure  the  quality  of  service  when  DCs  process interactive workloads and avoid the long delay caused by queuing,  load  service  level  agreement  constraints  are expressed  in  Eq.  (21)  according  to  the  MM1  queuing model

$$
\frac {1}{m _ {i , t} ^ {\alpha} \mu^ {\alpha} - \alpha_ {i , t}} \leqslant t ^ {\alpha}, \forall t \in T, \forall i \in N,\tag{21}
$$

where $t ^ { \alpha }$ is  the  maximum  queuing  delay  allowed  when processing interactive workload.

The workload must also meet the server capacity limit when it is processed in the DC, which is expressed as

$$
m _ {i, t} ^ {\beta} \geqslant \frac {\sum_ {q \in Q} \beta_ {i , q , t}}{\mu^ {\beta}}, \forall t \in T, \forall i \in N,\tag{22}
$$

$$
m _ {i, t} ^ {\alpha} + m _ {i, t} ^ {\beta} \leqslant m _ {i} ^ {\max}, \forall t \in T, \forall i \in N,\tag{23}
$$

where $m _ { i } ^ { \operatorname* { m a x } }$ is  the  maximum  number  of  servers  in  the iDC  .

(6) Constraint conditions of refrigeration system

DC  refrigeration  power  should  meet  the  upper  and lower limit constraints

$$
P _ {i, t} ^ {\text { cool - min }} \leqslant P _ {i, t} ^ {\text { cool }} \leqslant P _ {i, t} ^ {\text { cool - max }}, \forall t \in T, \forall i \in N,\tag{24}
$$

where $P _ { i , t } ^ { \mathrm { c o o l - m a x } }$ and $P _ { i , t } ^ { \mathrm { c o o l - m i n } }$ respectively are the upper and lower limits of the refrigeration system power.

In the day-ahead scheduling plan, the indoor temperature of the DC refrigeration system should be equal to the set temperature, whose constraint conditions are

$$
T _ {i, t} ^ {\text { in }} = T ^ {\text { set }}, \forall t \in T, \forall i \in N,\tag{25}
$$

where $T ^ { \mathrm { s e t } }$ is  the  day-ahead  set  temperature  of  the  DC room.

(7) Constraints on power transmission of the grid

The transmission power supplied by the power grid to the  DC  should  be  less  than  the  maximum  allowed transmission  power.  In  addition,  this  paper  does  not consider the reverse sale of power from the microgrid in the DC to the power grid. The constraint conditions of the power transmission of the power grid are

$$
0 \leqslant P _ {i, t} ^ {\text { grid }} \leqslant P ^ {\text { grid - max }}, \forall t \in T, \forall i \in N,\tag{26}
$$

Pwhere   <sup>grid-max</sup> is the maximum transmission power of the grid supply to the DC.

## 3.3    Intraday rolling optimized scheduling model

The  intraday  rolling  optimization  stage  is  based  on  the day-ahead optimization plan and is optimized every hour ahead.  Its  main  function  is  to  adjust  the  day-ahead scheduling  plan  to  ensure  the  operation  economy  of  the IDC under the uncertainty of wind-power output, and to provide  reference  for  the  intraday  real-time  correction stage.

## 3.3.1    Objective function

The  rolling  optimization  model  takes  the  minimum intraday power purchase penalty and operation cost in the time  domain  of  rolling  optimization  as  the  objective function.  At  the  same  time,  in  the  rolling  optimization stage, the constraints on indoor temperature of the DC are relaxed.  By  using  the  energy  flexibility  of  the  DC refrigeration  system,  the  power  consumption  is  adjusted to  reduce  the  economic  loss  caused  by  the  prediction error. The objective function of this stage is

$$
\min \sum_ {i \in N} \sum_ {t = t _ {\mathrm{s}}} ^ {T} (C _ {i, t} ^ {\text { grid - DI }} + C _ {i, t} ^ {\text { gas - DI }} + C _ {i, t} ^ {\text { ch - DI }} + C _ {i, t} ^ {\text { temp - DI }}),\tag{27}
$$

where $t _ { \mathrm { s } }$ is  the  rolling  optimization  start  time  period, $C _ { i , t } ^ { \mathrm { g r i d - D I } }$ is  the  intraday  power  purchase  cost, $C _ { i , t } ^ { \mathrm { g a s - D I } }$ is  the intraday  CTE  operating  cost, $C _ { i , t } ^ { \mathrm { c h - D I } }$ is  the  intraday  ES equipment  operating  cost,  and $\ ' { C } _ { i , t } ^ { \mathrm { t e m p - D I } }$ is  the  indoor temperature offset cost.

The calculation formula of $C _ { i , t } ^ { \mathrm { g a s - D I } }$ is the same as Eq. (7), while the calculation formula of $C _ { i , t } ^ { \mathrm { c h - D I } }$ is the same as Eq. (8). The intraday stage power purchase cost is

$$
\begin{array}{c} C _ {i, t} ^ {\text {grid - DI}} = \Big (P _ {i, t} ^ {\text {grid - DI}} \gamma_ {i, t} ^ {\text {grid}} + \Delta P _ {i, t} ^ {\text {grid - DI}} \gamma^ {\text {grid - DI}} \Big) \Delta t, \\ \forall t \in T,   \forall i \in N, \end{array}\tag{28}
$$

$$
P _ {i, t} ^ {\text { grid - DI }} = P _ {i, t} ^ {\text { grid }} + \Delta P _ {i, t} ^ {\text { grid - DI }},
$$

$$
\forall t \in T, \forall i \in N,\tag{29}
$$

where $\Delta P _ { i , t } ^ { \mathrm { g r i d - D I } }$ is  the  intraday  purchased  power adjustment,  and $\gamma ^ { \mathrm { g r i d - D I } }$ is  the  unit  penalty  cost  of  the intraday purchased power adjustment.

Indoor  set  temperature  offset  cost $C _ { i , t } ^ { \mathrm { t e m p - D I } }$ is  calculated as

$$
C _ {i, t} ^ {\text { temp - DI }} = (T _ {i, t} ^ {\text { in - DI }} - T _ {i, t} ^ {\text { in }}) ^ {2} \gamma^ {\text { temp }} \Delta t, \forall t \in T, \forall i \in N,\tag{30}
$$

where $T _ { i , t } ^ { \mathrm { i n - D I } }$ is the intraday DC indoor temperature, $T _ { i , t } ^ { \mathrm { i n } }$ is the  day-ahead  indoor  temperature,  and $\gamma ^ { \mathrm { { t e m p } } }$ is  the  unit penalty cost of the temperature offset.

## 3.3.2    Constraint conditions

The  DC  intraday  power  balance  constraint  differs  from the day-ahead power balance constraint (according to Eq. (9)),  which  also  includes  the  intraday  power  purchase power  adjustment  amount.  The  intraday  power  balance constraint is

$$
\begin{array}{c} P _ {i, t} ^ {\text {load - DI}} = P _ {i, t} ^ {\text {grid}} + \Delta P _ {i, t} ^ {\text {grid - DI}} + P _ {i, t} ^ {\text {bd - DI}} - P _ {i, t} ^ {\text {bc - DI}} + P _ {i, t} ^ {\text {gas - DI}} \\ + P _ {i, t} ^ {\text {wind - DI}} + P _ {i, t} ^ {\text {solar - DI}}, \quad \forall t \in T, \forall i \in N. \end{array}\tag{31}
$$

In  the  process  of  intraday  optimization,  DCs  relax  the restrictions  on  indoor  temperature,  indoor  temperature meets  the  upper  and  lower  limits  constraints,  and  the intraday cooling system temperature constraints are

$$
T ^ {\min} \leqslant T _ {i, t} ^ {\text { in - DI }} \leqslant T ^ {\max}, \forall t \in T, \forall i \in N,\tag{32}
$$

where $T ^ { \mathrm { m a x } }$ is  the  maximum  DC  room  temperature,  and $T$ <sup>min</sup> is the minimum DC room temperature.

The  ES  device  constraints,  CTE  constraints,  workload constraints, load service level agreement constraints, and grid transmission power constraints in the day-day rolling optimization model are the same as those in the day-day model, which will not be described here.

## 3.4    Intraday real-time corrective scheduling model

In the intraday rolling optimization stage, the economy of intraday  operation  of  the  IDC  is  mainly  ensured.  In  the real-time  operation  process,  there  is  still  a  small prediction error in the ultra-short-term forecast results of wind-wind  output.  Therefore,  it  is  necessary  to  perform real-time correction of large-timescale scheduling plan in intraday rolling optimization on a small-timescale, so that the  DC  real-time  correction  phase  operation  plan  tracks the  operation  plan  in  the  rolling  optimization  phase, reducing  power  fluctuations,  and  thus  guaranteeing  the economy of the overall scheduling results. The real-time correction  is  executed  every  15  min  in  advance. According  to  the  results  of  the  RES  ultra-short-term prediction,  the  dispatch  plan  obtained  in  the  intraday rolling optimization stage is corrected and adjusted. Since the  system  operation  deviation  feedback  is  required  in this  stage,  the  optimization  model  for  the  real-time correction phase is constructed using an MPC approach.

## 3.4.1    Objective function

Based  on  the  scheduling  plan  of  rolling  optimization stage,  each  rolling  optimization  scheduling  interval  is divided  into  several  intervals  with  smaller  timescales.

According  to  the  change  of  RES  output  within  the smaller  timescale,  the  minimum  power  adjustment penalty  cost  is  taken  as  the  objective  function  which  is expressed as

$$
\min \sum_ {i \in N} \sum_ {t = t _ {\mathrm{s}} + n \Delta t} ^ {t _ {\mathrm{s}} + 1} \| \psi_ {i, t} ^ {\mathrm{RT}} - \psi_ {i, t _ {\mathrm{s}}} ^ {\mathrm{DI}} \| _ {Q} ^ {2},\tag{33}
$$

nwhere    is  the  number  of  time  periods  within  the  realtime  correction  phase, $\psi _ { i , t } ^ { \mathrm { R T } }$ is  the  output  variable  of  the real-time  correction  phase, $\psi _ { i , t _ { \mathrm { s } } } ^ { \mathrm { D I } }$ is  the  reference  value  of the output variable, i.e., the $t _ { \mathrm { s } }$ time period intraday rolling optimization  scheduling  plan,  and $Q$ is  the  penalty coefficient matrix of the output variable.

In  the  real-time  correction  stage,  due  to  the  poor regulation  flexibility  of  the  CTE,  real-time  correction adjustment  is  not  conducted,  and  it  is  set  that  the  startstop  and  output  state  of  the  CTE  at  this  stage  should follow the optimal scheduling results of intraday rolling. In  addition,  due  to  the  coupling  characteristics  between IDCs  in  the  spatial  scheduling  of  interactive  workload, adjusting  the  workload  scheduling  plan  of  a  single  DC will  affect  the  scheduling  plans  of  other  DCs,  thus affecting  the  optimization  results  of  the  whole  real-time correction  stage.  Therefore,  the  interactive  workload scheduling  plan  also  follows  the  results  of  intraday rolling  optimization.  Based  on  this,  the  output  variables of  the  real-time  correction  stage  mainly  include  power purchase,  the  charging  and  discharging  power  of  ES equipment, and the power consumption of DC load. The formula of output variables is

$$
\psi_ {i, t} ^ {\mathrm{RT}} = \left[ P _ {i, t} ^ {\text { grid - RT }}, P _ {i, t} ^ {\text { bd - RT }}, P _ {i, t} ^ {\text { bc - RT }}, P _ {i, t} ^ {\text { load - RT }} \right].\tag{34}
$$

The  control  variable  is  defined  as $u _ { i , t } ^ { \mathrm { R T } }$ ,  which  is  the adjustment amount relative to the rolling optimal dispatch plan,  mainly  including  the  purchased  power  adjustment amount $\Delta P _ { i , t } ^ { \mathrm { g r i d - R T } }$ the  ES  equipment  charging  and discharging  power  adjustment  amount $\Delta P _ { i , t } ^ { \mathrm { b c - R T } }$ and $\Delta P _ { i . t } ^ { \mathrm { b d - R T } } ;$ and  the  DC  load  power  adjustment  amount $\Delta P _ { i , t } ^ { \mathrm { l o a d - R T } }$ , which is given by

$$
u _ {i, t} ^ {\mathrm{RT}} = \left[ \Delta P _ {i, t} ^ {\text { grid - RT }}, \Delta P _ {i, t} ^ {\text { bd - RT }}, \Delta P _ {i, t} ^ {\text { bc - RT }}, \Delta P _ {i, t} ^ {\text { load - RT }} \right],\tag{35}
$$

where  the  first  three  items $\Delta P _ { i , t } ^ { \mathrm { g r i d - R T } } , \ \Delta P _ { i , t } ^ { \mathrm { b d - R T } }$ ,  and $\Delta P _ { i , t } ^ { \mathrm { b c - R T } }$ can be directly controlled and adjusted, but the DC power regulation $\Delta P _ { i , t } ^ { \mathrm { l o a d - R T } }$ cannot be directly regulated because it consists  of  the  regulation  of  the  refrigeration  system cooling capacity $\Delta \bar { h } _ { i , t } ^ { \mathrm { R T } }$ and the batch workload adjustment $\Delta \beta _ { i , q , i } ^ { \mathrm { R T } }$ . The formula for calculating $\Delta P _ { i , t } ^ { \mathrm { l o a d - R T } }$ is

$$
\Delta P _ {i, t} ^ {\mathrm{load-RT}} = \lambda_ {1} \Delta h _ {i, t} ^ {\mathrm{RT}} + \sum_ {q \in Q} \frac {e _ {\mathrm{peak}} ^ {\beta}}{\mu^ {\beta}} \Delta \beta_ {i, q, t} ^ {\mathrm{RT}}.\tag{36}
$$

The  perturbation  variable  is  the  predicted  variation  of the  wind-power  output  in  the  real-time  correction  stage within days, which is expressed as:

$$
r _ {i, t} ^ {\mathrm{RT}} = \left[ \Delta P _ {i, t} ^ {\text { wind - RT }}, \Delta P _ {i, t} ^ {\text { solar - RT }} \right].\tag{37}
$$

The update value strategy of the output variable in the real-time correction stage is:

$$
\psi_ {i, t _ {\mathrm{s}} + n \Delta t} ^ {\mathrm{RT}} = \psi_ {i, t _ {\mathrm{s}}} ^ {\mathrm{DI}} + u _ {i, t _ {\mathrm{s}} + n \Delta t} ^ {\mathrm{RT}} + r _ {i, t _ {\mathrm{s}} + n \Delta t} ^ {\mathrm{RT}}.\tag{38}
$$

## 3.4.2    Constraint conditions

The  CTE  power  in  the  real-time  correction  stage  is  the same as the optimization result in the rolling optimization stage, whose constraint conditions are

$$
P _ {i, t} ^ {\mathrm{gas-RT}} = P _ {i, t} ^ {\mathrm{gas-DI}}.\tag{39}
$$

The  optimization  results  of  the  interactive  workload scheduling plan in the same rolling optimization stage are

$$
\alpha_ {i, t} ^ {\mathrm{RT}} = \alpha_ {i, t} ^ {\mathrm{DI}}.\tag{40}
$$

The  remaining  constraints  include  intraday  power balance  constraints,  ES  device  constraints,  workload constraints, load service level agreement constraints, and grid  transmission  power  constraints  of  the  same  rolling optimization model, which are not listed here.

## 3.5    Distributed solution method based on ADMM

The  centralized  optimal  scheduling  method  for  IDCs increases the communication and computation burden of the  scheduling  system,  and  the  optimization  results  are difficult  to  describe  the  interaction  process  among  DCs. Therefore,  the  alternating  direction  multiplier  method  is used to decouple the coupling constraints between IDCs, and  the  optimal  workload  interactions  are  obtained through iteration.

To  realize  the  distributed  solution  of  the  original optimization  problem,  it  is  necessary  to  reconstruct  the original  problem  in  a  distributed  manner  and  divide  the original problem into multiple independent subproblems. Since  the  IDCs  have  natural  partitioning  properties,  the IDCs  are  split  into  multiple  individuals  to  solve separately,  that  is,  the  objective  function  of  the  optimal total  cost  of  the  IDCs  is  split  into  the  objective  function of  the  optimal  cost  of  multiple  independent  DCs.  The sub-objective function calculation formula $f _ { i }$ i  for the DC is defined as

$$
\min f _ {i} = \sum_ {t \in T} (C _ {i, t} ^ {\text { grid }} + C _ {i, t} ^ {\text { gas }} + C _ {i, t} ^ {\text { ch }}), \text {   s.t.   Eqs.   (9) - (26) },\tag{41}
$$

The  variables  of  each  DC  in  constraints  (Eqs.  (6)–(9) and  Eqs.  (18)–(26))  are  independent  of  each  other. Therefore,  they  can  be  directly  applied  to  the  suboptimization  problem.  However,  due  to  the  coupling characteristics  among  multiple  DCs  in  workload  space scheduling,  decoupling  is  not  possible  if  the  workload space scheduling constraint (Eq. (17) is relaxed directly. Therefore,  the  inter-DC  interaction  variable $\alpha _ { i - j , t } ^ { \mathrm { t r } }$ is introduced,  which  is  defined  as  the  interactive  workload i jvolume scheduled between DC  and DC  . When $\alpha _ { i - j , t } ^ { \mathrm { t r } }$ is ia  positive  sign,  it  indicates  that  DC   receives  the jworkload  volume  scheduled  by  DC ,  and  when  it  is  a inegative  sign,  it  indicates  that  DC   schedules  the  workjload volume to DC  . Taking three IDCs as an example, the constraint decoupling process is shown in Fig. 3.

![](images/62b49e35ecc2b1d66e8c498a774d20d90d1ef1e0a399e94f11a776cca6b86e0d.jpg)  
Fig. 3    Schematic diagram of distributed decoupling of IDCs.

After  decoupling  the  constraint,  Eqs.  (3)–(13)  are changed to

$$
\alpha_ {i, t} = \varphi_ {i, t} ^ {\alpha} + \sum_ {j \in N, j \neq i} \alpha_ {i - j, t} ^ {\mathrm{tr}}, \forall t \in T, \forall i \in N.\tag{42}
$$

In addition, boundary consistency conditions need to be introduced to ensure convergence of the solution:

$$
\alpha_ {i - j, t} ^ {\mathrm{tr}} + \alpha_ {j - i, t} ^ {\mathrm{tr}} = 0, \forall t \in T, \forall i, j \in N, i \neq j.\tag{43}
$$

With the distributed optimization model decoupled, the workload scheduling constraints are relaxed to obtain the iaugmented Lagrangian function of the DC  , expressed as

$$
\begin{array}{l} L _ {i} = \min \Big (f _ {i} + \sum_ {j \in N, j \neq i} \sum_ {t \in T} [ \lambda_ {i - j, t} (\alpha_ {i - j, t} ^ {\mathrm{tr}} + a _ {j - i, t} ^ {\mathrm{tr}}) ] \\ \quad + \sum_ {j \in N, j \neq i} \sum_ {t \in T} \left(\frac {\rho}{2} \| \alpha_ {i - j, t} ^ {\mathrm{tr}} + a _ {j - i, t} ^ {\mathrm{tr}} \| _ {2} ^ {2}\right) \Big), \end{array}\tag{44}
$$

where $\lambda _ { i - j , t }$ k  is the Lagrange multiplier for the  th iteration i jbetween DCs   and  .

Each  DC  optimizes  its  own  scheduling  policy  by solving  Eqs.  (3)–(30),  and  only  interactive  workload scheduling  information  is  exchanged  between  DCs. According  to  the  principle  of  the  alternating  direction multiplier  method,  the  form  of  the  (k  +  1)th  iteration update is derived as:

$$
\begin{array}{c} \alpha_ {i - j, t} ^ {\mathrm{tr}} (k + 1) = \operatorname * {a r g m i n} L _ {i} \Big (\lambda_ {i - j, t} (k), \alpha_ {i - j, t} ^ {\mathrm{tr}} (k), \alpha_ {j - i, t} ^ {\mathrm{tr}} (k) \Big), \\ \forall i, j \in N, i \neq j, \end{array}\tag{45}
$$

$$
\begin{array}{c} \alpha_ {j - i, t} ^ {\mathrm{tr}} (k + 1) = \operatorname * {a r g m i n} L _ {j} \Big (\lambda_ {j - i, t} (k), \alpha_ {j - i, t} ^ {\mathrm{tr}} (k), \alpha_ {i - j, t} ^ {\mathrm{tr}} (k + 1) \Big), \\ \forall i, j \in N, i \neq j, \end{array}\tag{46}
$$

$$
\begin{array}{c} \lambda_ {i - j, t} (k + 1) = \lambda_ {j - i, t} (k + 1) = \lambda_ {i - j} (k) + \rho \left(\alpha_ {i - j, t} (k + 1) \right. \\ \qquad \qquad + \alpha_ {j - i, t} (k + 1)), \qquad \forall i, j \in N, i \neq j. \end{array}\tag{47}
$$

The convergence criteria are Eqs. (48) and (49), and the iteration is skipped when the conditions are satisfied:

$$
\begin{array}{c} \| r _ {i} (k + 1) \| _ {2} = \sum_ {j \in N, j \neq i} \sum_ {t \in T} \| \alpha_ {i - j, t} ^ {\mathrm{tr}} + a _ {j - i, t} ^ {\mathrm{tr}} \| _ {2} \leqslant \varepsilon_ {\mathrm{pri}} ^ {1}, \\ \| s _ {i} (k + 1) \| _ {2} = \sum_ {j \in N, j \neq i} \sum_ {t \in T} \| \alpha_ {i - j, t} ^ {\mathrm{tr}} (k + 1) - \alpha_ {i - j, t} ^ {\mathrm{tr}} (k) \| _ {2} \leqslant \varepsilon_ {\text {dual}} ^ {1}, \end{array} \tag {48}\tag{49}
$$

where $\varepsilon _ { \mathrm { { p r i } } } ^ { 1 }$ and $\varepsilon _ { \mathrm { d u a l } } ^ { 1 }$ are the original residual and pairwise residual  convergence  metrics,  respectively,  for  the distributed optimal scheduling problem of IDCs.

Since  the  Lagrange  multiplier  term  in  the  generalized Lagrange  function  Eq.  (44)  is  a  function  related  to  the workload,  the  order  of  magnitude  of  this  term  is  much larger  than  that  of  the  original  objective  function $f _ { i } ,$ which  will  result  in  the  original  residuals  and  pairwise residuals  not  being  able  to  converge  to  a  smaller  value. For  this  reason,  the  setting  criteria  for  the  original  and pairwise  residuals  should  be  too  small.  By  referring  to Ref.  [30],  the  formula  for  the  convergence  index  of  the original  residual  and  pairwise  residual  is  obtained  and expressed as:

$$
\begin{array}{r l} \varepsilon_ {\mathrm{pri}} ^ {1} = & \sqrt {n} \varepsilon_ {\mathrm{abs}} + \varepsilon_ {\mathrm{rel}} \max \Big \{\sum_ {j \in N, j \neq i} \sum_ {t \in T} \| \alpha_ {i - j, t} ^ {\mathrm{tr}} \| _ {2}, \\ & \sum_ {j \in N, j \neq i} \sum_ {t \in T} \| \alpha_ {j - i, t} ^ {\mathrm{tr}} \| _ {2} \Big \}, \end{array}\tag{50}
$$

$$
\varepsilon_ {\text { dual }} ^ {1} = \sqrt {n} \varepsilon_ {\text { abs }} + \varepsilon_ {\text { rel }} \left(\sum_ {j \in N, j \neq i} \sum_ {t \in T} \| \lambda_ {i - j, t} \| _ {2}\right),\tag{51}
$$

where $\varepsilon _ { \mathrm { a b s } }$ and $\varepsilon _ { \mathrm { r e l } }$ are  the  absolute  convergence  and relative convergence, respectively, whose general value is 0.001.

The two-stage distributed solution process based on the combination  of  rolling  optimization  and  real-time correction with MPC is shown in Fig. 4.

## 4    Example analysis

## 4.1    Multi-timescale scheduling framework

This paper considers an arithmetic analysis of three IDCs managed  by  one  DC  operator  that  are  distributed  in different  regions.  The  grid  tariff  uses  the  grid  proxy purchase  tariff  issued  by  the  local  power  supply company,  see  Fig.  S1  in  Electronic  Supplementary Material  (ESM),  and  the  scenery  output  of  each  DC  is shown in Fig. S2. A total of two types of batch workloads in  the  DC  are  set,  and  the  ratio  of  the  number  of interactive workloads to the two types of batch workloads is  set  to  6:2:1.  Figure  S3  shows  the  arrival  rate  of  each type of workload in the DC. It is assumed that the errors of  the  intraday  rolling  optimization  phase  and  the  realtime  correction  phase  scenery  output  follow  a  normal distribution,  and  the  predicted  values  of  the  rolling optimization  phase  are  simulated  by  superimposing  the errors  on  the  predicted  values  before  the  day,  and  the predicted  values  of  the  real-time  correction  phase  are simulated  by  superimposing  the  errors  on  the  predicted values of the rolling optimization phase. Figure S4 shows the  scenery  output  of  the  rolling  optimization  phase  and the  real-time  correction  phase  obtained  from  the simulation.  The  MATLAB  2019b  platform  is  used  to implement  the  ADMM  based  multi-timescale optimization programming and call the CPLEX solver to solve the sub-problem. To prove the effectiveness of the method, the following simulation scenarios are set up for comparative analysis:

![](images/6d6beae940dae53881994f33befd5f21d29cac8c3593d157c728f66acecc176d.jpg)  
Fig. 4    Multi-timescale distributed optimization solution process.

Scenario  1:  Performing  rolling  optimization  phase optimization  scheduling  with  real-time  correction  phase optimization  scheduling  using  the  method  proposed  in this paper;

Scenario  2:  Optimized  scheduling  in  the  rolling optimization  phase,  without  optimal  scheduling  in  the real-time  correction  phase,  with  the  power  fluctuations caused by the scenery power forecast errors compensated by the grid;

Scenario 3: No rolling optimization phase optimization scheduling,  no  real-time  correction  phase  optimization scheduling,  and  the  power  fluctuations  caused  by  the scenery power forecast errors are compensated for by the grid.

## 4.2    Analysis of optimization results in each scenario

Table 1  shows  the  operating  cost  and  grid  power fluctuation  rate  of  IDCs  under  different  scenarios,  and

Fig. 5  shows  the  grid  power  fluctuation  of  IDCs  under different scenarios.

From  Table 1,  it  can  be  seen  that  the  multi-timescale optimal  scheduling  model  (Scenario  1)  achieves  the lowest  grid  power  fluctuation  while  ensuring  the  lowest total operating cost of the IDC, and has the best economy and  the  lowest  power  fluctuation  rate  of  all  scenarios. Comparing  the  results  of  Scenario  1  and  the  day-ahead optimization,  it  can  be  found  that  Scenario  can  save US\$6464.5  in  economic  cost  relative  to  the  day-ahead schedule  because  the  intraday  optimal  scheduling liberalizes  the  indoor  temperature  restriction,  which allows  the  DC  to  change  the  indoor  temperature  during the  intraday  operation  and  thus  reduce  the  load  power cost.  The  indoor  temperature  of  the  cooling  system during the intraday real-time correction phase is shown in Fig. S5.

Comparing  Scenarios  1  and  2,  it  can  be  seen  that Scenario  2  bears  US\$5430.2  more  economic  cost  than Scenario  1  due  to  the  small-timescale  forecast  deviation in  the  real-time  correction  stage,  and  the  power fluctuation  rate  of  the  grid  is  5.95%  higher  than  that  of Scenario  1.  This  indicates  that  the  real-time  correction scheduling  can  effectively  reduce  the  impact  of  smalltimescale  uncertainty  of  RES  output  by  making  system operation  state  adjustment  based  on  rolling  optimization scheduling.  This  shows  that  real-time  corrective scheduling  can  effectively  reduce  the  impact  of uncertainty in the small-timescale of RES output, reduce the economic cost, and at the same time achieve effective suppression of power fluctuation of each equipment.

Comparing  Scenarios  1  and  3,  it  can  be  seen  that Scenario  1  has  a  reduction  of  US\$31252.2  in  the economic  cost  and  a  reduction  in  the  power  fluctuation rate of the grid by 15.23% compared with Scenario 3 due to the intraday multi-timescale optimal scheduling, which indicates  that  the  intraday  multi-timescale  optimal scheduling  eliminates  the  impact  of  prediction  errors  on the  basis  of  the  day-ahead  scheduling  plan  and  greatly ensures the economical operation of the interconnection. The DC operation economy is greatly ensured.

Table 1    Comparison of optimization results of multi-timescale operation under different scenarios

<table><tr><td>Scenarios</td><td>Electricity purchase cost/US$</td><td>CTE/US$</td><td>ES/US$</td><td>Grid power fluctuation rate/US$</td></tr><tr><td>Previous plans</td><td>98678.1</td><td>33725.2</td><td>2236.4</td><td>-</td></tr><tr><td>Scenario 1</td><td>98794.1</td><td>26650.9</td><td>2730.2</td><td>1.29%</td></tr><tr><td>Scenario 2</td><td>104322.6</td><td>26650.9</td><td>2631.9</td><td>7.24%</td></tr><tr><td>Scenario 3</td><td>123465.8</td><td>33725.2</td><td>2236.4</td><td>16.52%</td></tr></table>

![](images/8f1e5126a16f2696402a4ff6389ce8b0b46a20a0172c2c9a38d25cc8ac48e8f0.jpg)  
(a)

![](images/25bfd701f21064e0c67cd4c82ba158e78ae24dda0745b8d09e34c813131011af.jpg)  
(b)

![](images/7c6e0763ff1fdb1f1126e48276e7e8bbd85f62a4d91314a7144fdf16975ace0b.jpg)  
Fig. 5    Power fluctuation of IDC power grid in different scenarios.  
(a) DC1 grid power fluctuation; (b) DC2 grid power fluctuation; (c) DC3 grid power fluctuation.

## 4.3    Rolling optimization scheduling results

Figure 6  shows  the  energy  management  optimization results  of  each  DC  in  the  rolling  scheduling  stage.  The priority  is  to  change  the  power  consumption  load  by adjusting the workload scheduling strategy and regulating the cooling power to eliminate the scenery output forecast deviation  in  the  rolling  optimization  phase.  When  the deviation  of  RES  cannot  be  eliminated  by  adjusting  the power  consumption  load,  the  energy  management strategy  is  then  adjusted  according  to  the  purchase penalty cost and the operating cost of each power supply equipment.  Intraday  rolling  optimization  is  an  economic optimization based on the day-ahead dispatching strategy. To  reduce  the  intraday  redispatching  cost,  the interconnection  DC  reduces  power  purchase  (e.g.,  from 1:00 to 2:00 in DC1), reduces CTE generation (e.g., from

14:00 to 17:00 in DC1), increases ES charging (e.g., from 3:00 to 5:00 in DC3) when the intraday forecast of RES output is large and increasing the electricity consumption load (e.g., from 13:00 to 14:00 in DC1), with the opposite scheduling  strategy  when  the  intraday  forecast  of  RES output  is  small  as  compared  to  when  it  is  large.  In summary,  the  intraday  rolling  optimal  dispatch  achieves economy  while  filling  the  large  timescale  RES  forecast deviations with source-load side flexibility resources.

## 4.4    Real-time correction scheduling results

Figure 7  shows  the  optimization  results  of  energy management  in  the  IDC  during  the  intraday  real-time correction  phase.  As  can  be  seen  from  Fig. 7  that,  even though  the  CTEs  are  not  power  adjusted  and  the interactive  workloads  are  not  spatially  scheduled  during the real-time correction phase, the IDCs can still maintain a  balanced  energy  supply  for  each  DC  during  the  realtime  correction  phase  when  the  RES  output  fluctuates rapidly by only adjusting the grid power, ES power, and adjusting the batch workload scheduling plan and cooling power.

Figure S6 shows the power fluctuations of each device during the intraday real-time correction phase. As can be

![](images/8dc396d70bafa37d541598a724458bce10f50bff2bba944ab0fdb203e74d2625.jpg)  
(a)

![](images/58467a50e6b542af6c9db4228ca13758d326ea41185de5e5302b2fac06123dea.jpg)  
(b)

![](images/fbf06ce72b887a6ced9c6f9302927755d84994010a80b27ed13d4c45887fa9c8.jpg)  
Fig. 6    Energy management optimization results in rolling scheduling phase. (a) DC1; (b) DC2; (c) DC3.

![](images/ad4df85239534755658e96c3cf8944849c6ee64edc7ed5cf85de33bc3a910834.jpg)  
(a)

![](images/e66ff714d4c9a1f90d4dabdf339dafdc1e8903bc07305380e7b198a6edf3d2f5.jpg)  
(b)

![](images/139b0120fc791ae9058eef749608518411a9ad64e1a02959b224a9023a316529.jpg)  
(c)  
Fig. 7    Energy management optimization results in real-time corrective phase. (a) DC1; (b) DC2; (c) DC3.

seen from Fig. S6 that the fluctuation of grid power and ES power of each DC is small, while the load power of the DC is relatively more volatile. This is due to the high cost  of  grid  power  regulation  in  intraday  operation,  and the  fact  that  setting  the  grid  power  fluctuation  penalty coefficient  higher  in  the  real-time  correction  phase  can safeguard  the  system  operating  economy.  Meanwhile, frequent  charge/discharge  state  changes  have  a  greater impact on the life of the ES battery. Therefore, setting a higher ES power fluctuation penalty factor can extend the life  of  the  ES  equipment.  Setting  a  minimum  DC  load power fluctuation penalty factor can make full use of the DC  load-side  workload  and  cooling  system  scheduling flexibility  to  perform  real-time  correction  phase  power fluctuation  leveling.  For  example,  in  the  6:00  to  12:00 scenario  of  DC1,  the  power  prediction  error  fluctuates greatly, and the corresponding DC load power fluctuation fluctuates greatly in this period, while the grid power and ES power fluctuation are effectively smoothed out.

## 4.5    Convergence analysis of ADMM

In this paper, the ADMM is used to solve the distributed optimization scheduling problem of interconnected DCs, and  the  convergence  of  the  objective  function  values  of each  DC  is  shown  in  Fig.  S7.  The  objective  function values  of  each  DC  converge  to  a  stable  value  when  the number of iterations reaches about 20 times. Figure S7(d) compares  the  optimization  results  of  the  centralized algorithm  with  the  optimization  results  of  the  ADMM, from  which  it  can  be  clearly  seen  that  the  difference between the ADMM and the centralized algorithm results is  smaller.  Therefore,  the  ADMM  can  converge  to  the optimal solution within a finite number of iterations when solving  the  optimal  scheduling  problem  for interconnected DCs.

Calculated  according  to  Eqs.  (50)  and  (51),  the convergence  criterion  for  the  original  residuals  and  the pairwise residuals is set to 100. The iterative convergence of the residuals of each DC is shown in Fig. 8. When the number of iterations is about 20, the original residuals of each DC have already reached the convergence criterion, and  when  the  number  of  iterations  is  about  18,  the pairwise  residuals  of  each  DC  have  already  reached  the convergence  criterion.  However,  as  the  number  of iterations increases, the residuals of each DC still cannot converge to a smaller value below 1. The reason for this is that the coupling variable is the workload, which is of an  order  of  magnitude  larger  than  the  operating  cost  in the  objective  function.  Although  the  residuals  are  still unable  to  converge  to  a  smaller  value  after  reaching  the convergence  criterion,  the  impact  on  the  value  of  the objective function for each DC is minimal, as evidenced by the results of the comparison between the centralized algorithm and the ADMM in Fig. S7(d).

In  this  paper,  the  convergence  process  of  the  original residuals of ADMM is compared with that of the Levenberg-Marquardt  (LM)  in  the  centralized  framework,  as shown  in  Fig. 9.  From  Fig. 9,  it  can  be  seen  that  compared to the LM algorithm in the centralized framework, which reaches convergence at the 23rd time, the ADMM makes  the  system  computation  smaller  and  faster,  and greatly reduces the need for communication and storage. Moreover,  each  DC  only  exchanges  the  workloads  after the  last  iteration,  which  exchanges  less  information  and protects the information privacy of each DC.

## 5    Conclusions

In this paper, a multi-timescale optimal scheduling model is proposed for the uncertainty problem of RES output in IDCs.  The  model  consists  of  day-ahead  optimization, rolling  optimization,  and  real-time  correction,  i.e.,  the day-ahead  optimization  phase  with  the  objective  of lowest operating cost, rolling optimization phase with the objective of lowest intraday economic cost, and real-time correction with the objective of lowest power fluctuation. The  effects  arising  from  prediction  errors  are  eliminated step  by  step  through  coordinated  optimization  at  multi timescales. The ADMM is used to solve the model. The parameters  of  the  algorithm  are  set  through  the  relevant reference  and  the  algorithm  analysis  is  analyzed.  The main conclusions are as follows:

1)  Based  on  the  results  of  the  day-ahead  optimal dispatch,  the  intraday  rolling  optimization  makes  use  of the  source-load  side  flexibility  resources  in  a  rolling manner  for  intraday  economic  dispatch,  generating  an intraday large time-scale dispatch plan, so that the system is able to efficiently consume the RES forecast deviation, and thus safeguard the operating economy of the system.

2)  Intraday  real-time  correction,  taking  the  dispatch results  of  intraday  rolling  optimization  as  a  reference, quickly  adjusts  the  workload  and  the  dispatch  plan  of each equipment, adapts to the small time-scale fluctuation of  RES  output  during  online  operation,  and  weakens  its uncertain impact. It can suppress power fluctuation while guaranteeing  the  operation  economy,  thus  ensuring  the stable operation of each equipment as well as the power grid.

![](images/e6f1538af122d8f8dfd903c2b23e13389d208d5c21327a9f6c5327dc0a593f26.jpg)  
(a)

![](images/464e883584f64caa7201c66c2665cf6514c64dd5aeff804da9b55d544bc5a7cb.jpg)  
(b)

Fig. 8    Convergence of residual iterations across DCs. (a) Original residuals; (b) dyadic residuals.  
![](images/6e4a3ec988eed74553af6244d5a918e6f106b24ac775a4461de60e56b220eb4e.jpg)  
Fig. 9    Original residual convergence process of ADMM and LM.

Competing interests    The  authors  declare  that  they  have  no  competing interests.

Electronic Supplementary Material    Supplementary  material  is available  in  the  online  version  of  this  article  at  https://doi.org/10.1007/ s11708-023-0912-6 and is accessible for authorized users

## References

Wang H, Huang J W, Lin X J, et al. Proactive demand response1. for data centers: A win-win solution. IEEE Transactions on Smart Grid, 2016, 7(3): 1584–1596

Wang  W,  Abdolrashidi  A,  Yu  N  P,  et  al.  Frequency  regulation2. service  provision  in  data  center  with  computational  flexibility. Applied Energy, 2019, 251: 113304

Chen T Y, Zhang Y, Wang X, et al. Robust workload and energy3. management  for  sustainable  data  centers.  IEEE  Journal  on Selected Areas in Communications, 2016, 34(3): 651–664

Ebrahimi K, Jones G F, Fleischer A S. Thermo-economic analysis4. of  steady  state  waste  heat  recovery  in  data  centers  using absorption refrigeration. Applied Energy, 2015, 139: 384–397

Han  O  Z,  Ding  T,  Zhang  X  S,  et  al.  A  shared  energy  storage5. business  model  for  data  center  clusters  considering  renewable energy uncertainties. Renewable Energy, 2023, 202: 1273–90

Landré D, Nicod J M, Varnier C. Optimal standalone data center6. renewable power supply using an offline optimization approach. Sustainable Computing-Informatics & Systems, 2022, 34: 100627

Cao  X  Y,  Zhang  J  S,  Poor  H  V.  Data  center  demand  response7. with  on-site  renewable  generation:  A  bargaining  approach. IEEE/ACM  Transactions  on  Networking,  2018,  26(6): 2707–2720

Chen Z, Wu L, Li Z. Electric demand response management for8. distributed large-scale internet data centers. IEEE Transactions on Smart Grid, 2014, 5(2): 651–661

Chen  M,  Gao  C  W,  Shahidehpour  M,  et  al.  Internet  data  center9. load  modeling  for  demand  response  considering  the  coupling  of multiple  regulation  methods.  IEEE  Transactions  on  Smart  Grid, 2021, 12(3): 2060–2076

Lasemi  M  A,  Alizadeh  S,  Assili  M,  et  al.  Energy  cost10. optimization  of  globally  distributed  Internet  Data  Centers  by copula-based  multidimensional  correlation  modeling.  Energy Reports, 2023, 9: 631–644

Oró  E,  Depoorter  V,  Garcia  A,  et  al.  Energy  efficiency  and11. renewable  energy  integration  in  data  centres.  Strategies  and modelling  review.  Renewable  &  Sustainable  Energy  Reviews, 2015.42: 429-445

Cheung  H,  Wang  S  W,  Zhuang  C  Q,  et  al.  A  simplified  power12. consumption model of information technology (IT) equipment in data  centers  for  energy  system  real-time  dynamic  simulation. Applied Energy, 2018, 222: 329–342

Nadjahi  C,  Louahlia  H,  Lemasson  S.  A  review  of  thermal13. management  and  innovative  cooling  strategies  for  data  center. Sustainable Computing-Informatics & Systems, 2018, 19: 14–28

Oró  E,  Codina  M,  Salom  J.  Energy  model  optimization  for14. thermal energy storage system integration in data centres. Journal of Energy Storage, 2016, 8: 129–41

Zhao  Q,  Xiong  C  C,  Yu  C,  et  al.  A  new  energy-aware  task15. scheduling  method  for  data-intensive  applications  in  the  cloud. Journal of Network and Computer Applications, 2016, 59: 14–27

Yuan  H,  Bi  J,  Zhou  M  C.  Spatial  task  scheduling  for  cost16. minimization  in  distributed  green  cloud  data  centers.  IEEE Transactions  on  Automation  Science  and  Engineering,  2019, 16(2): 729–740

Yuan  H,  Bi  J,  Zhou  M  C.  Spatiotemporal  task  scheduling  for17. heterogeneous  delay-tolerant  applications  in  distributed  green data  centers.  IEEE  Transactions  on  Automation  Science  and Engineering, 2019, 16(4): 1686–1697

Cioara  T,  Anghel  I,  Antal  M,  et  al.  Data  center  optimization18. methodology  to  maximize  the  usage  of  locally  produced renewable  energy.  In:  Proceedings  of  the  2015  Sustainable Internet and ICT for Sustainability, Madrid, Spain, 2015

Chen T Y, Zhang Y, Wang X, et al. Robust workload and energy19. management  for  sustainable  data  centers.  IEEE  Journal  on Selected Areas in Communications, 2016, 34(3): 651–664

Wang  P,  Xie  L  Y.  LU  Y,  et  al.  Day-ahead  emission-aware20. resource planning for data center considering energy storage and batch  workloads.  In:  Proceedings  of  the  IEEE  Conference  on Energy  Internet  and  Energy  System  Integration,  Beijing,  China, 2017

Liu Z, Huang B, Hu X, et al. Blockchain-based renewable energy21. trading  using  information  entropy  theory.  IEEE  Transactions  on Network Science and Engineering, 2023

Jawad M, Qureshi M B, Khan M U S, et al. A robust optimization22. technique  for  energy  cost  minimization  of  cloud  data  centers. IEEE Transactions on Cloud Computing, 2021, 9(2): 447–460

Zhang H F, Xu T, Wu H, et al. Risk-based stochastic day-ahead23. operation  for  data  centre  virtual  power  plants.  IET  Renewable Power Generation, 2019, 13(10): 1660–1669

Ding Z H, Cao Y J, Xie L Y, et al. Integrated stochastic energy24. management  for  data  center  microgrid  considering  waste  heat recovery.  IEEE  Transactions  on  Industry  Applications,  2019, 55(3): 2198–2207

Ding  Z  H,  Xie  L  Y,  Lu  Y,  et  al.  Emission-aware  stochastic25. resource  planning  scheme  for  data  center  microgrid  considering batch  workload  scheduling  and  risk  management.  IEEE Transactions on Industry Applications, 2018, 54(6): 5599–5608

Paul  D,  Zhong  W  D,  Bose  S  K.  Energy  efficient  scheduling  in26. data  centers.  In:  Proceedings  of  the  2015  IEEE  International Conference on Communications, London, UK, 2015

Wu  Y,  Xue  X,  Le  L,  et  al.  Real-time  energy  management  of27. large-scale data centers: A model predictive control approach. In: Proceedings  of  the  2020  IEEE  Sustainable  Power  and  Energy Conference, Chengdu, China, 2020

Zhu Y X, Wang J Y, Bi K T, et al. Energy optimal dispatch of the28. data  center  microgrid  based  on  stochastic  model  predictive control. Frontiers in Energy Research, 2022, 10: 863292

Wang H, Shen H Y, Wieder P, et al. A data center interconnects29. calculus.  In:  26th  IEEE/ACM  International  Symposium  on Quality of Service, Banff, Canada, 2018

Wang  H,  Ai  Q,  Wu  J,  et  al.  Bi-level  distributed  optimization  for30. microgrid  clusters  based  on  alternating  direction  method  of multipliers. Power System Technology, 2018, 42(6): 1718–1727