# Statistical Feasibility Robust Optimization With Polyhedron Uncertainty Set for Hydrogen-Data Center Microgrid Operations

Juntao Duan , Yuanzheng Li , Senior Member, IEEE, Yang Li , Senior Member, IEEE, Pu Yang , and Zhigang Zeng , Fellow, IEEE

Abstract— Robust optimization (RO) has been widely used in the hydrogen-data-center microgrid (H<sub>2</sub>-DCMG) optimal operations. However, the operation results based on RO are too conservative. Statistical feasibility can be introduced into RO to reduce conservatism. Therefore, statistical feasibility-based RO is adopted in the H -DCMG operations optimization. In this study, a data-driven statistical-feasibility-based robust rolling optimization framework is constructed for the optimal operations of the c-DCMG. In this framework, DC temperature is influenced by the uncertain outside temperature, and an uncertainty set is needed to express the uncertain outside temperature. Unfortunately, the existing studies that construct the uncertainty set satisfying statistical feasibility only constructs the ellipsoid uncertainty sets. The ellipsoid uncertainty sets will be converted into the second-order cone constraints, which will increase the complexity when solving. In this study, the Statistical-Guaranteebased Vertex Link (SGVL) algorithm is proposed to construct the polyhedron uncertainty sets, which are used to describe the uncertainty of the outside temperature. Moreover, statistical feasibility-based DC temperature bound is guaranteed by the optimal operations obtained based on these uncertainty sets. Compared with the traditional ellipsoid uncertainty set, the polyhedron uncertainty set can reduce the complexity of the optimization problem and improve the efficiency of the solution process. Case studies based on the real-world temperature dataset are processed. The results show that the introduction of statistical feasibility can reduce the total operation cost by 0.29%∼0.64%. The average solving time of the optimization problems based on the polyhedron uncertainty sets constructed using the SGVL algorithm also reduces by 7%∼13%. The cases also verify that other uncertainty parameters and different kinds of forecasters do not influence the performance and effectiveness of the framework.

Received 10 September 2024; revised 29 November 2024; accepted 3 December 2024. Date of publication 18 December 2024; date of current version 2 April 2025. This article was recommended for publication by Associate Editor R. Carli and Editor Q. Zhao upon evaluation of the reviewers’ comments. This work was supported in part by the Tencent Foundation of China under Grant Tencent TEG RBFR20230705; in part by the National Natural Science Foundation of China under Grant 62422308; and in part by the Foundation for Outstanding Research Groups of Hubei Province of China under Grant 2025AFA012. (Corresponding author: Yuanzheng Li.)

Yang Li is with the School of Electrical Engineering, Northeast Electric Power University, Jilin 132000, China (e-mail: liyang@neepu.edu.cn).

Pu Yang is with Tencent Technology (Shenzhen) Company Ltd., Shenzhen, Guangdong 518057, China (e-mail: dowpuyang@tencent.com).

Digital Object Identifier 10.1109/TASE.2024.3514101

Note to Practitioners—This paper focuses on optimizing the operations of a hydrogen-data center microgrid (H<sub>2</sub>-DCMG) to minimize its operation cost in the way of statistical feasibility-based robust optimization. The statistical feasibility is introduced into the framework for the relaxation of the data center (DC) temperature bound. The Statistical-Guaranteebased Vertex Link (SGVL) algorithm is proposed to construct the uncertainty set of outside temperature that guarantees the dispatch results satisfying statistical feasibility. In practice, the settings of the framework and the data used in the SGVL are significant. Firstly, in the rolling dispatch, the length of the forecasting period needs also to be set after the trade-off between the solving efficiency, the dispatch economy and the DC temperature stability. Secondly, the pre-set DC temperature bound is advisable to be set below the maximum temperature that does not harm its normal operation. When constructing the uncertainty sets, the two parameters of the statistical feasibility need also to be set after the trade-off between the economics of dispatch results and the acceptability of constraint violations. The dataset and the actual forecasting error need to remain equally distributed to guarantee the performance of the dispatch of the proposed framework. The proposed framework can be readily implemented and integrated into the actual dispatch of H -DCMG.

Index Terms— Hydrogen energy storage system, data center microgrid, statistical feasibility, robust optimization.

## ACRONYMS

IT Information technology. DC Data center. A/C Air conditioning. RE Renewable energy. H<sub>2</sub>-DCMG Hydrogen-data-center microgird. HESS Hydrogen energy storage system. RO Robust optimization. SGVL Statistical-guarantee-based vertex link. PSS Power supply system. HSS Hydrogen storage system. SOCP Second-order cone programming problem.

## NOMENCLATURE

$P _ { \mathrm { R E } } ( t )$ Actual RE output in t. $P _ { \mathrm { g r i d } } ( t )$ The power purchased/sold from/to power grid in t .

Juntao Duan, Yuanzheng Li, and Zhigang Zeng are with the Key Laboratory of Image Information Processing and Intelligent Control, School of Artificial Intelligence and Automation, Huazhong University of Science and Technology, Wuhan 430074, China (e-mail: jtduan@hust.edu.cn; Yuanzheng\_Li@hust.edu.cn; zgzeng@hust.edu.cn).

<table><tr><td> $n_{\text{el}}(t)$ </td><td>The amount of  $H_2$  produced in  $t$ .</td></tr><tr><td> $P_{\text{el}}(t)$ </td><td>Electrolyzer power in  $t$ .</td></tr><tr><td> $P_{\text{comp}}(t)$ </td><td>The electrical load of the compression pump in  $t$ .</td></tr><tr><td> $P_{\text{fc}}(t)$ </td><td>The power produced by fuel cell in  $t$ .</td></tr><tr><td> $n_{\text{fc}}(t)$ </td><td>The  $H_2$  consumed by fuel cell in  $t$ .</td></tr><tr><td> $I_{\text{el}}(t), I_{\text{fc}}(t)$ </td><td>The binary variable reflecting the working situations of the electrolyzer and fuel cell.</td></tr><tr><td> $n_{\text{store}}(t)$ </td><td> $H_2$  amount in HSS in  $t$ .</td></tr><tr><td> $W_{\text{RT}}^{s_i}(t)$ </td><td>Processed real-time workload of server cluster  $s_i$  in  $t$ .</td></tr><tr><td> $f_{\text{RT}}^{s_i}(t)$ </td><td>Working frequency plan of server cluster  $s_i$  in  $t$ .</td></tr><tr><td> $D(t)$ </td><td>Departure curve of delay-tolerable workload.</td></tr><tr><td> $W_{\text{DT}}(t)$ </td><td>The serve rate of delay-tolerant workload in  $t$ .</td></tr><tr><td> $f_{\text{DT}}^{s_i}(t)$ </td><td>Working frequency of the server cluster  $s_i$  in  $t$ .</td></tr><tr><td> $W_{\text{DT}}^{s_i}(t)$ </td><td>The delay-tolerant workload serve rate of server cluster  $s_i$  in  $t$ .</td></tr><tr><td> $P_{\text{DC}}(t)$ </td><td>The DC power in  $t$ .</td></tr><tr><td> $H_{\text{DC}}(t)$ </td><td>The emitted heat by the DC in  $t$ .</td></tr><tr><td> $\Delta T_{\text{DC}}(t)$ </td><td>The change of DC temperature in  $t$ .</td></tr><tr><td> $Q_{\text{AC}}(t)$ </td><td>The heat dissipation of the A/C system in  $t$ .</td></tr><tr><td> $P_{\text{AC}}(t)$ </td><td>The power of A/C system in  $t$ .</td></tr><tr><td> $T_{\text{DC}}(t)$ </td><td>DC temperature in  $t$ .</td></tr><tr><td> $\mathbb{C}$ </td><td>The total cost of dispatch.</td></tr></table>

<table><tr><td> $P_{\text{RE}}^{\text{max}}(t)$ </td><td>The maximum RE output in  $t$ .</td></tr><tr><td> $P_{\text{grid}}^{\text{max}}$ </td><td>The upper bound of  $P_{\text{grid}}(t)$ .</td></tr><tr><td> $\eta_{\text{el}}, \eta_{\text{fc}}$ </td><td>The efficiency of the electrolyzer and fuel cells.</td></tr><tr><td> $H_{\text{HV}}, L_{\text{HV}}$ </td><td>High and low calorific values of  $\text{H}_2$ .</td></tr><tr><td> $P_{\text{el}}^{\text{min}}, P_{\text{el}}^{\text{max}}$ </td><td>The lower and upper bounds of  $P_{\text{el}}(t)$ .</td></tr><tr><td> $\gamma_{\text{H}_2}$ </td><td>Heat capacity ratio of  $\text{H}_2$ .</td></tr><tr><td> $R_{\text{gas}}$ </td><td>Ideal gas constant.</td></tr><tr><td> $M_{\text{H}_2}$ </td><td>The molar mass of  $\text{H}_2$ .</td></tr><tr><td> $P_1, P_2$ </td><td>The pressure of the electrolyzer and the HSS.</td></tr><tr><td> $P_{\text{fc}}^{\text{min}}, P_{\text{fc}}^{\text{max}}$ </td><td>The lower and upper bounds of  $P_{\text{fc}}(t)$ .</td></tr><tr><td> $W$ </td><td>The isentropic compression energy.</td></tr><tr><td> $\eta_{\text{comp}}$ </td><td>The efficiency of the compression pump.</td></tr><tr><td> $n_{\text{store}}^{\text{min}}, n_{\text{store}}^{\text{max}}$ </td><td>The lower and upper bounds of  $\text{H}_2$  stored in the HSS.</td></tr><tr><td> $W_{\text{RT,A}}(t)$ </td><td>The arrival rate of the real-time workload in  $t$ .</td></tr><tr><td> $D$ </td><td>The threshold of processing the real-time workloads.</td></tr><tr><td> $W_{\text{DT,A}}(t)$ </td><td>The arrival rate of the delay-tolerant workload in  $t$ .</td></tr><tr><td> $A(t)$ </td><td>Arrival curve of delay-tolerable workload.</td></tr><tr><td> $D_{\text{min}}(t)$ </td><td>Minimum departure curve of delay-tolerable workload.</td></tr></table>

<table><tr><td> $\theta$ </td><td>The parameter between the serving rate and the frequency.</td></tr><tr><td> $f_{\text{max}}^{s_i}$ </td><td>The maximum working frequency of  $s_i$ .</td></tr><tr><td> $N_s$ </td><td>The number of server clusters.</td></tr><tr><td> $P_{\text{idle}}^{s_i}, P_{\text{dyn}}^{s_i}$ </td><td>The idle and dynamic power of  $s_i$ .</td></tr><tr><td> $\varkappa_{\text{DC}}, \varkappa_{\text{AC}}$ </td><td>Linear parameter of the DC and the A/C system between heating or cooling and power.</td></tr><tr><td> $C_{\text{DC}}$ </td><td>DC’s thermal capacitance.</td></tr><tr><td> $M_{\text{DC}}$ </td><td>The mass of the DC.</td></tr><tr><td> $R_{\text{DC}}$ </td><td>The thermal resistance of DC.</td></tr><tr><td> $\kappa$ </td><td>The delay parameter of DC temperature.</td></tr><tr><td> $T_{\text{out}}(t)$ </td><td>The temperature outside the DC in  $t$ .</td></tr><tr><td> $T_{\text{out,fore}}(t)$ </td><td>The forecasting outside temperature in  $t$ .</td></tr><tr><td> $T_{\text{out,err}}(t)$ </td><td>The forecasting error of outside temperature in  $t$ .</td></tr><tr><td> $T_{\text{set}}$ </td><td>The pre-set upper bound of the DC temperature.</td></tr><tr><td> $P_{\text{Load}}(t)$ </td><td>The general loads in  $t$ .</td></tr><tr><td> $\varepsilon, \delta$ </td><td>The parameters in the statistical feasibility.</td></tr><tr><td> $\rho_*$ </td><td>The price of *.</td></tr><tr><td> $\underline{P_{\text{RE}}^{\text{max}}}(t)$ </td><td>Lower bound of  $P_{\text{RE}}^{\text{max}}(t)$  in uncertainty set.</td></tr><tr><td> $\overline{P_{\text{RE}}^{\text{max}}}(t)$ </td><td>Upper bound of  $P_{\text{RE}}^{\text{max}}(t)$  in uncertainty set.</td></tr><tr><td> $\underline{W_{\text{RT,A}}}(t)$ </td><td>Lower bound of  $W_{\text{RT,A}}(t)$  in the uncertainty set.</td></tr><tr><td> $\overline{W_{\text{RT,A}}}(t)$ </td><td>Upper bound of  $W_{\text{RT,A}}(t)$  in the uncertainty set.</td></tr><tr><td> $\underline{P_{\text{Load}}}(t)$ </td><td>Lower bound of  $P_{\text{Load}}(t)$  in the uncertainty set.</td></tr><tr><td> $\overline{P_{\text{Load}}}(t)$ </td><td>Upper bound of  $P_{\text{Load}}(t)$  in the uncertainty set.</td></tr><tr><td> $\mu$ </td><td>The parameter that translates the frequency to the service rate.</td></tr></table>

Uncertainty Sets $\mathcal { P }$ The uncertainty set of $P _ { \mathrm { R E } } ^ { \mathrm { m a x } } ( t )$ $\mathcal { W }$ The uncertainty set of $W _ { \mathrm { R T , A } } ( t )$ $\mathcal { T } _ { \mathfrak { p } }$ The polyhedron uncertainty set of $T _ { \mathrm { o u t , e r r } } ( t )$ $\mathcal { T } _ { \mathrm { e } }$ The ellipsoid uncertainty set of $T _ { \mathrm { o u t , e r r } } ( t )$ $\mathcal { L }$ The uncertainty set of $P _ { \mathrm { L o a d } } ( t )$

## I. INTRODUCTION

W<sup>ITH</sup> <sup>the</sup> <sup>development</sup> <sup>of</sup> <sup>information</sup> <sup>technology</sup> <sup>(IT),</sup> the demands for data storage, computing and processing are increasing explosively [1], [2]. To meet IT service requirements, data centers (DCs) are booming recently [3]. However, the servers in the DCs emit much heat when processing the IT workloads, and thus the DC temperature rises. Excessive temperatures can harm the normal operation of servers in the DC. Consequently, the DC temperature needs to be controlled within the range required for DC operations.

One of the major problems faced in controlling the DC temperature is the high power consumption caused by the air conditioning (A/C) system of DC [4]. Conventionally, the DC, including its A/C system, is usually powered using thermal power, which may increase greenhouse gas emissions. The increment of greenhouse gas emissions will cause climate change and air pollution, and harm the survival of humans. To this end, in recent years, renewable energy (RE) has been denoted to be an effective way to power the DC to enhance carbon reduction based on RE [5], [6]. Compared to the traditional way of using thermal power to power DCs, RE is considered an environmentally friendly way. Using RE to power the DCs can reduce greenhouse gas emissions, thereby reducing environmental pollution. For instance, a novel approach is developed in [7] for the dispatch of RE in a real-world campus microgrid containing a data center. The feasibility of RE in powering the DC is verified in the real-world campus microgrid. However, the adoption of RE may cause some problems with the DC operation, such as the power stability reduction because of its intermittency [8].

Hydrogen-DC microgird (H<sub>2</sub>-DCMG) is recognized as an effective way to leverage RE to power DC. The $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ is equipped with a hydrogen energy storage system (HESS). In recent years, some studies on the HESS have been carried out and the results show the advantages. For instance, in [9], the economic benefits and environmental friendliness of the HESSs are shown. In [5] and [10], the HESSs are used in the microgrids. The advantages of HESS in economic benefits and long lifespan are exhibited by the experimental outcomes from the equipment. In [5], [9], [10], these advantages are verified based on the results obtained from the experimental equipment. These inherent advantages confer upon HESS the ability to deal with the intermittency of RE and to power DCs economically.

Recently, various efforts have been made in dispatching DC coupled with HESS. For example, in [11], a hierarchical dispatch strategy assisted by model predictive control is proposed for the dispatch of HESS of the DC. In [1], the operations of DCs equipped with HESS are controlled using predictive control. Xu et al. aim to dispatch a $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ based on a hybrid robust-stochastic methodology to minimize the operation cost and greenhouse gas emissions [12]. The above studies exhibit the superiority of HESS in terms of economy and utilization of RE for powering DCs. However, in these studies, the DC temperature is not considered. High DC temperature is harmful to the normal operations of the servers in the DC. Consequently, DC temperature needs to be controlled to maintain the normal operations of DC.

Generally, the DC temperature is expected to be around the pre-set temperature limitation [13]. However, the DC temperature is influenced not only by the A/C system and the servers in the DC but the uncertain outside temperature. The uncertainty of the outside temperature causes the uncertainty of the DC temperature, thus the DC temperature cannot be controlled precisely. Therefore, the uncertainty of the outside temperature may bring risks to the DC’s operations. For example, when the real outside temperature is significantly higher than the forecast value, the DC temperature may be also much higher than its expected value. The DC temperature may even exceed the pre-set temperature bound. It is evident that the high DC temperature may weaken the DC operation. Therefore, the uncertainty of the outside temperature has to be properly considered and addressed.

To deal with the uncertainty of outside temperature, robust optimization (RO) is widely used. For example, Chen et al. develop an RO strategy to control greenhouse temperature [14]. Moreover, an RO control mechanism is proposed in [15] for the thermostatically controlled loads. In [16], a robust control system is developed for the thermostatically controlled loads. The results collected based on the equipment verify the superiority of the control system. The RO approaches in the above studies seek to optimize the objective for the worst cases and the temperature robustness of the thermostatically controlled loads is guaranteed. However, the results tend to be conservative, and the economic benefits tend to be low. The reason lies in that the RO focuses too much on the extreme and low-probability scenarios of the uncertain outside temperature. These scenarios can lead to increments in the size of the uncertainty sets, and thus the conservatism of the RO results increases.

To reduce the conservatism of RO, neglecting the extreme and low-probability scenarios in the uncertainty sets is an effective way [17]. This negligence does not harm the DCs normal operations [18]. For instance, the maximum forecast error of outside temperature is not included in the uncertainty set. This neglect causes the DC temperature to be slightly higher (e.g., 0.1 <sup>◦</sup>C) than the preset limit. In that case, the working operation of DC will not be significantly affected. However, this negligence can significantly reduce the conservatism of the problem and improve the economics of dispatch.

Statistical feasibility-based RO can reduce the conservatism of the dispatch results by giving the controllable opportunity of violating some constraints. Moreover, the extreme and low-probability scenarios are neglected when constructing the uncertainty sets [17]. In the statistical feasibility-based RO, the constraints related to the uncertainties are re-constructed in the form of restricting the probability that a chance constraint is satisfied. This reconstruction can reduce the impact of the extreme and low-probability scenarios in datasets, thus reducing the conservatism of RO.

To make the dispatch results satisfy the statistical feasibility, the uncertainty sets are constructed based on statistical guarantee. Some extreme and low-probability scenarios are neglected selectively when constructing the uncertainty sets. The optimization results with these uncertainty sets are less conservative and more economical. Some recent studies adopt statistical feasibility-based RO in some actual dispatch problems. For instance, Jiang et al. propose a robust sample-based scheduling scheme for temperature control based on the notion of statistical feasibility [19]. Yang et al. develop a statistical feasibility-based RO framework to minimize the computation and transmission power consumption [20]. However, to the best of the authors’ knowledge, there is no existing study introducing statistical feasibility into the RO of the DC operations, especially into the dispatch of its temperature. Statistical feasibility is considered to help reduce the conservatism of RO and improve the economy of its operation.

Additionally, the studies [19], [20] only employ ellipsoid uncertainty sets into the statistical feasibility-based RO. The ellipsoid uncertainty sets will be converted to the second-order cone constraints when solving [21]. The second-order cone constraints will lead to higher computation complexity and solving time compared to the linear constraints. Longer solving time may weaken the timeliness of the dispatch. The responsiveness of the dispatch to the dynamic RE and the outside temperatures in the $_ { \mathrm { H } _ { 2 } \mathrm { - } \mathrm { D C M G } }$ may be further weakened. Thus, the normal operation of the H -DCMG may be harmed [22]. To this end, the ellipsoid uncertainty sets need to be linearized to decrease the solving time in dispatch. Polyhedron uncertainty sets are a kind of widely used linear uncertainty sets, which are converted to linear constraints when solving. Then, the complexity of the problem is reduced. Through leveraging the polyhedron uncertainty sets, the dynamic responsiveness of the dispatch to the $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ can be improved, and the normal operation of $_ { \mathrm { H } _ { 2 } \mathrm { - } \mathrm { D C M G } }$ is maintained. However, previous studies have not researched the method to construct polyhedron uncertainty sets that can ensure the dispatch results satisfy the statistical feasibility.

In summary, the research gap of this study lies in the following two points.

• There is not an existing study introducing the statistical feasibility-based RO into the $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ dispatch, to the best of the authors’ knowledge. The usability of statistical feasibility in $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ dispatch and the interaction has not been researched in previous studies.

• Previously, the uncertainty sets constructed for the statistical feasibility-based RO are ellipsoid uncertainty sets, which are converted to second-order cone constraints and decrease the timeliness of dispatch. There is not an existing method to construct the polyhedron uncertainty sets ensuring the robust dispatch results satisfying the statistical feasibility.

In this study, a data-driven statistical-feasibility-based $\mathrm { H } _ { 2 ^ { - } }$ DCMG robust rolling dispatch framework is proposed. The statistical feasibility is introduced into the robust dispatch of the $_ { \mathrm { H } _ { 2 } \mathrm { - } \mathrm { D C M G } }$ in this framework. Specifically, the DC temperature bound is reconstructed based on statistical feasibility. By reconstructing the temperature bound, a controllable possibility of violating the chance constraint of DC temperature bound is provided to the dispatch results. Thus, the conservatism of RO is reduced, and the economic benefits are improved. To improve the computation efficiency and ensure the timeliness of dispatch, the Statistical-Guarantee-based Vertex Link (SGVL) algorithm is proposed. In this algorithm, a polyhedron uncertainty set for the DC outside temperature is constructed. In the algorithm, the uncertainty set is constructed based on the statistical guarantee to ensure the statistical feasibility is satisfied. The extreme and low-probability scenarios are neglected, and the conservatism is reduced. The polyhedron uncertainty sets are transferred to linear constraints when solving, thus reducing the computational complexity of the dispatch.

However, in the SGVL algorithm, an eigenvalue decomposition needs to be done to the covariance matrix of the historical data, of which dimension is related to the dispatch period. A long dispatch period will cause the high dimensional covariance matrix, which is computationally challenging in eigenvalue decomposition. Consequently, the rolling dispatch framework is adopted to decrease the dimension of the covariance matrix, thereby reducing the computational challenge in the SGVL. In the rolling dispatch framework, a subproblem is to be solved in each time slot, and the period of this subproblem is the present time slot plus a short future period. The period of the subproblem is much shorter than the whole dispatch period. Therefore, the dimension of the covariance matrix needs eigenvalue decomposition in the SGVL is lower and the computational complexity is reduced. Contributions in this paper mainly lie as follows.

• Data-driven Statistical-Feasibility-Based H<sub>2</sub>-DCMG Robust Rolling Dispatch Framework: A data-driven statistical-feasibility-based robust rolling dispatch framework for the $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ is proposed in this study. A statistical-feasibility-based DC temperature constraint is innovatively constructed to reduce the conservatism of the RO in the framework. The constraint gives the possibility of minor violations of the DC temperature chance constraint in a controllable manner. These violations do not harm the normal operations of the DC but can reduce the power cost during the H -DCMG operations. Additionally, the dispatch framework is a rolling dispatch framework, which meets the requirement of the H -DCMG for operating online. The dimension of the problem is also decreased and the complexity of the subproblem is reduced.

• The SGVL Algorithm: The SGVL algorithm is proposed to construct the polyhedron uncertainty sets for the forecasting error of outside temperature. Through obtaining and linking the vertexes of the ellipsoid uncertainty set, this algorithm focuses on creating a polyhedron uncertainty set based on the statistical guarantee. The statistical guarantee provides a way to selectively neglect extreme and low-probability scenarios while guaranteeing the statistical feasibility is satisfied. The dispatch results with the polyhedron uncertainty set constructed based on the SGVL algorithm are economically advantageous compared to those using the conventional box uncertainty sets. Compared with the ellipsoid uncertainty sets constructed based on statistical guarantee, the SGVL algorithm’s polyhedron uncertainty set is more efficient and less computationally demanding in the solution process.

The rest of this paper is organized as follows. Section II describes the structure of the data-driven statistical-feasibilitybased $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ rolling robust dispatch framework. Then, this section presents the constraints and the objective in the H - DCMG dispatch and organizes the RO problem to be solved in each time slot. In Section III, the constructing methods of the uncertainty set for RE, workload and outside temperature are introduced. The process of the SGVL algorithm and the proof of the dispatch results satisfying the statistical feasibility are also given. The numerical experiments based on the real-world dataset are carried out, and the dispatch results are drawn in Section IV. The results demonstrate advantages in both the economy of results and solution efficiency of the polyhedron uncertainty sets constructed SGVL algorithm. Sensitivity analysis of the parameters are presented and the influence of other uncertainties are also tested. Finally, Section V delivers conclusions, limitations and perspectives of this study.

![](duan2025_robust_h2_dcmg_assets/images/c23596cb6adbc25756a41a737e6d8969ae241794b0f63323fa4e6e3d28e6609c.jpg)  
Fig. 1. The illustration of the data-driven statistical-feasibility-based H -DCMG robust rolling dispatch framework.

## II. STRUCTURE OF THE DATA-DRIVEN STATISTICAL-FEASIBILITY-BASED H<sub>2</sub>-DCMG ROBUST ROLLING DISPATCH FRAMEWORK

The proposed data-driven statistical-feasibility-based $\mathrm { H } _ { 2 ^ { - } }$ DCMG robust rolling dispatch framework is shown in Fig. 1. The dispatch framework is based on the rolling dispatch method, of which the main process is shown in Fig. 1(a). In the rolling dispatch method, the whole dispatch problem with the dispatch period T is divided into some subproblems, each of which is to be solved in each time slot. The dispatch period of the subproblem in t is $t ~ \sim ~ t + N$ , where t is the present time slot and N is the forecasting period. For example, when t = 1, the dispatch period of the subproblem is $1 \sim 1 + N$ , and the subproblem is solved in t = 1. In the next time slot, $t = 2$ , the dispatch period of the subproblem is $2 \sim 2 + N$ , and the subproblem is solved in t = 2. Thus, the rolling dispatch can decompose the long-period, highdimensional optimization problem into multiple short-period, low-dimensional optimization problems.

The structure of the $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ is shown in Fig. 1(b). There are three subsystems in this layer, including the power supply system (PSS), HESS and DC. The PSS contains wind power generators and the power grid and is responsible for providing power to the HESS and DC. When the HESS is full and the RE output is high, the power can be sold to the power grid. The HESS contains electrolyzers, compression pumps, hydrogen storage systems (HSS) and fuel cells. The electrolyzers consume power and produce $\mathrm { H } _ { 2 }$ by electrolyzing water. The produced $\mathrm { H } _ { 2 }$ is compressed by the compression pumps and then stored in the HSS. The fuel cells consume H and supply power to the loads. The DC contains an A/C system and several server clusters. The server clusters process the workloads, including the real-time workloads and the delaytolerant workloads. Heat is emitted by the server clusters when processing the workloads, and thus the DC temperature rises. The A/C system is used to maintain the DC temperature below a pre-set limit to avoid the harm of high temperature to the operations of the DC. The DC temperature is also influenced by the outside temperature, which is uncertain. Additionally, the general loads in the DC, such as the loads of the lighting system and the workload storage device, are also considered as uncertainties in dispatch.

Fig. 1(c) describes the statistical feasibility. The statistical feasibility provides the dispatch results with a chance to violate the chance constraint. Unlike normal chance constraints, the statistical feasibility is more relatable, allowing the results to violate the chance constraints with a pre-set probability. In this framework, statistical feasibility is introduced to re-construct the DC temperature bound as a statistical feasibility-based constraint. The probability of violating the chance constraint leads to the reduction of conservatism of RO, and its controllability keeps the normal operations of the DC from being harmed by this violation.

The rest of this section is organized as follows. The structure of the $\mathrm { H } _ { 2 } .$ -DCMG and its mathematical model are designed. Then, the optimization problem to be solved in each time slot is constructed.

## A. Model of H<sub>2</sub>-DCMG

The model of the PSS is presented in (1) and (2). The PSS of the H -DCMG contains RE generators and the power grid. The output of RE generators cannot be higher than the maximum RE output, as shown in (1). The H -DCMG can also purchase power from the power grid. Also, the power can be sold to the power grid when the HESS is full and the RE output is high. When $P _ { \mathrm { g r i d } } ( t )$ is positive, the H<sub>2</sub>-DCMG purchases power from the grid. When $P _ { \mathrm { g r i d } } ( t )$ is negative, the power is sold to the grid. (2) describes the upper and lower bound of the grid power.

$$
0 \leq P _ {\mathrm{RE}} (t) \leq P _ {\mathrm{RE}} ^ {\max} (t)\tag{1}
$$

$$
- P _ {\text { grid }} ^ {\max} \leq P _ {\text { grid }} (t) \leq P _ {\text { grid }} ^ {\max}\tag{2}
$$

$P _ { \mathrm { R E } } ( t )$ and $P _ { \mathrm { R E } } ^ { \mathrm { m a x } } ( t )$ refer to the actual RE output and the maximum RE output in t, respectively. $P _ { \mathrm { g r i d } } ( t )$ is the grid power in t, and $P _ { \mathrm { g r i d } } ^ { \mathrm { m a x } }$ is its upper bound.

In the H<sub>2</sub>-DCMG, HESS is responsible for power storage. Its model is presented as follows. In HESS, the electrolyzer produces $\mathrm { H } _ { 2 }$ by consuming power. In this study, a linear function (3) on electrolyzer power $P _ { \mathrm { e l } } ( t )$ is adopted to describe the $\mathrm { H } _ { 2 }$ production. Additionally, the upper and lower bound of $P _ { \mathrm { e l } } ( t )$ (4) are also considered.

$$
n _ {\mathrm{el}} (t) = \eta_ {\mathrm{el}} P _ {\mathrm{el}} (t) / H _ {\mathrm{HV}}\tag{3}
$$

$$
I _ {\mathrm{el}} (t) P _ {\mathrm{el}} ^ {\min} \leq P _ {\mathrm{el}} (t) \leq I _ {\mathrm{el}} (t) P _ {\mathrm{el}} ^ {\max}\tag{4}
$$

where $n _ { \mathrm { e l } } ( t )$ is the amount of $\mathrm { H } _ { 2 }$ produced in t. $\eta _ { \mathrm { e l } }$ is the efficiency of the electrolyzer. $H _ { \mathrm { H V } }$ is high calorific value of $\mathrm { H } _ { 2 }$ $P _ { \mathrm { e l } } ^ { \mathrm { m i n } }$ and $P _ { \mathrm { e l } } ^ { \mathrm { m a x } }$ refer to the lower and upper bound of $P _ { \mathrm { e l } } ( t )$ ), respectively. $I _ { \mathrm { e l } } ( t )$ is the binary variable that reflects the working situation of the electrolyzer. When it is 1, the electrolyzer works.

The produced $\mathrm { H } _ { 2 }$ by the electrolyzer needs to be compressed to meet the pressure requirement of the HSS. The isentropic compression energy W for compression of 1 kg of $\mathrm { H } _ { 2 }$ between pressures $P _ { 1 }$ and $P _ { 2 }$ is given in kJ by (5) [23]. Based on $W ,$ the electrical load of the compression pump in t, noted as $P _ { \mathrm { c o m p } } ( t )$ , is linear to the moss the compressed H<sub>2</sub>, as shown in (6) [24]. After compression, the $\mathrm { H } _ { 2 }$ is stored in the HSS, of which the operation constraints are described as (7) and (8). (7) describes the variation of the amount of $\mathrm { H } _ { 2 }$ stored in the HSS over time. (8) reflects the upper and lower bound of the

$\mathrm { H } _ { 2 }$ amount stored in the HSS.

$$
W = \frac {\gamma_ {\mathrm{H} _ {2}} R _ {\text { gas }} T _ {\mathrm{H} _ {2}}}{\gamma_ {\mathrm{H} _ {2}} - 1} \left[ \left(\frac {P _ {2}}{P _ {1}}\right) ^ {\frac {\gamma_ {\mathrm{H} _ {2}} - 1}{\gamma_ {\mathrm{H} _ {2}}}} - 1 \right] \cdot M _ {\mathrm{H} _ {2}} ^ {- 1}\tag{5}
$$

$$
P _ {\mathrm{comp}} (t) = \eta_ {\mathrm{comp}} \frac {W M _ {\mathrm{H} _ {2}} n _ {\mathrm{el}} (t)}{3 . 6 \times 1 0 ^ {6}}\tag{6}
$$

$$
n _ {\mathrm{store}} (t) = n _ {\mathrm{store}} (t - 1) + n _ {\mathrm{el}} (t) - n _ {\mathrm{fc}} (t)\tag{7}
$$

$$
n _ {\mathrm{store}} ^ {\mathrm{min}} \leq n _ {\mathrm{store}} (t) \leq n _ {\mathrm{store}} ^ {\mathrm{max}}\tag{8}
$$

where $\gamma _ { \mathrm { H } _ { 2 } } = 1 . 4 1$ is the heat capacity ratio of H . $\eta _ { \mathrm { c o m p } }$ is the efficiency of the compression pump. $R _ { \mathrm { g a s } } = 8 . 3 1$ is the ideal gas constant. $T _ { \mathrm { H } _ { 2 } }$ is the temperature of $\mathrm { H } _ { 2 }$ in Kelvin. $M _ { \mathrm { H } _ { 2 } } = 2 . 0 1 4$ g/mol is the molar mass of $\mathrm { H } _ { 2 } . \ n _ { \mathrm { s t o r e } } ( t )$ is $\mathrm { H } _ { 2 }$ amount in HSS in t, and $n _ { \mathrm { s t o r e } } ^ { \mathrm { m i n } }$ and $n _ { \mathrm { s t o r e } } ^ { \mathrm { m a x } }$ are its upper and lower bounds. $n _ { \mathrm { f c } } ( t )$ is the $\mathrm { H } _ { 2 }$ consumed by fuel cell in t.

The fuel cell in HESS produces power by consuming $\mathrm { H } _ { 2 }$ Similar to the electrolyzer, the power production of the fuel cell is also described using a linear function, as shown in (9). Its upper and lower bounds are also considered, as shown in (10).

$$
P _ {\mathrm{fc}} (t) = \eta_ {\mathrm{fc}} L _ {\mathrm{HV}} n _ {\mathrm{fc}} (t)\tag{9}
$$

$$
I _ {\mathrm{fc}} (t) P _ {\mathrm{fc}} ^ {\min} \leq P _ {\mathrm{fc}} (t) \leq I _ {\mathrm{fc}} (t) P _ {\mathrm{fc}} ^ {\max}\tag{10}
$$

where $P _ { \mathrm { f c } } ( t )$ is the power produced by fuel cell in t. η<sub>fc</sub> is efficiency of fuel cell. $L _ { \mathrm { H V } }$ is low calorific value of $\mathrm { H } _ { 2 }$ $P _ { \mathrm { f c } } ^ { \mathrm { m i n } }$ and $P _ { \mathrm { f c } } ^ { \mathrm { m a x } }$ are the lower and higher bound of $P _ { \mathrm { f c } } ( t )$ respectively. $I _ { \mathrm { f c } } ( t )$ is the binary variable of the working situation of the fuel cells.

In the HESS, the fuel cell and the electrolyzer cannot work simultaneously because the HESS cannot be charged and discharged at the same time. Consequently, $I _ { \mathrm { e l } } ( t )$ and $I _ { \mathrm { f c } } ( t )$ cannot be 1 in the same t, as shown in (11).

$$
I _ {\mathrm{el}} (t) + I _ {\mathrm{fc}} (t) \leq 1\tag{11}
$$

In the H -DCMG, the DC is responsible for processing workloads using $N _ { s }$ server clusters. In this study, both real-time workload and delay-tolerant workload are to be processed in the DC. The real-time workload must be processed in time. The queue theory is used to model their arriving, queueing, and processing for real-time workload. The arriving workloads are split into the waiting queues among server clusters. Assuming that the arrival rate of real-time workload in t is $W _ { \mathrm { R T , A } } ( t )$ , and there are $N _ { s }$ server clusters in the DC, the following constraints need to be satisfied.

$$
\sum_ {s _ {i} = 1} ^ {N _ {s}} W _ {\mathrm{RT}} ^ {s _ {i}} (t) = W _ {\mathrm{RT,A}} (t)\tag{12}
$$

$$
\frac {1}{f _ {\mathrm{RT}} ^ {s _ {i}} (t) \mu - W _ {\mathrm{RT}} ^ {s _ {i}} (t)} \leq D\tag{13}
$$

where $W _ { \mathrm { R T } } ^ { s _ { i } } ( t )$ and $f _ { \mathrm { R T } } ^ { s _ { i } } ( t )$ are the processed real-time workload and working frequency plan of server cluster $s _ { i }$ in t. In each time slot t, the sum of the real-time workload processed by all server clusters equals the real-time workload arrived in the same time slot, as shown in (12). $\mu$ is the parameter that translates the frequency to the service rate. According to the queue theory, the waiting time for real-time workload is $1 / [ f _ { \mathrm { R T } } ^ { s _ { i } } ( t ) \mu - W _ { \mathrm { R T } } ^ { s _ { i } } ( t ) ]$ , which should not be larger than the threshold D to ensure the quality of service, as shown in (13). The related proof is presented in Appendix A.

![](duan2025_robust_h2_dcmg_assets/images/ca9143a9995e7091d0a6470f13172dcf01f44f5a21928184c15fbce88dc4ce80.jpg)  
Fig. 2. The illustration of A(t ), D(t ) and $D _ { \mathrm { m i n } } ( t )$

Besides, the delay-tolerant workload has CPU frequency, disk, execution time, and deadline requirements [25]. To describe the constraints related to the delay-tolerant workload, the arrival, departure and minimum departure curves are introduced, noted as A(t), D(t) and $D _ { \mathrm { m i n } } ( t )$ , respectively [26]. As shown in Fig. 2, the green curve is the arrival curve, which is the accumulated arrival rate of the delay-tolerant workload since the beginning of dispatch. The arrival curve reflects the arrival of the delay-tolerant workloads. The red curve is the minimum departure curve, which presents the deadline for processing the delay-tolerant workloads. The blue curve is the departure curve, which reflects the completion rate of the delay-tolerant workloads. The departure curve can be dispatched, early or late processing the delay-tolerant workloads, as described by the dashed blue curve. The mathematical definitions of these curves are presented in Appendix B. For example, a delay-tolerant workload w with deadline $t _ { 4 }$ arrives in $t _ { 1 } .$ , and the values of its arrival curve and minimum departure curve are $A ( t _ { 1 } ) = D _ { \mathrm { m i n } } ( t _ { 4 } ) = w$ . The delay-tolerant workload can be processed when $t _ { 1 } \le t \le t _ { 4 }$ , such as $t _ { 2 }$ and $t _ { 3 } .$ To ensure that the workload is processed after its arrival and before its deadline, the D(t) should be between the $A ( t )$ and $D _ { \mathrm { m i n } } ( t )$ as shown in (14).

$$
D _ {\mathrm{min}} (t) \leq D (t) \leq A (t)\tag{14}
$$

For each server cluster, the serve rate of delay-tolerant workloads can be scheduled by changing the working frequency of the server cluster $f _ { \mathrm { D T } } ^ { s _ { i } } ( t )$ . The relationship between the serve rate and the frequency is a linear function with the parameter $\theta ,$ as shown in (15). The sum of $W _ { \mathrm { D T } } ^ { s _ { i } } ( t )$ for all server clusters equals the delay-tolerant workload serve rate of the DC, noted as $W _ { \mathrm { D T } } ( t )$ , as shown in (16).

$$
f _ {\mathrm{DT}} ^ {s _ {i}} (t) = \theta W _ {\mathrm{DT}} ^ {s _ {i}} (t)\tag{15}
$$

$$
\sum_ {s _ {i} = 1} ^ {N _ {s}} W _ {\mathrm{DT}} ^ {s _ {i}} (t) = W _ {\mathrm{DT}} (t)\tag{16}
$$

where $W _ { \mathrm { D T } } ^ { s _ { i } } ( t )$ is the delay-tolerant workload serve rate of server cluster $s _ { i }$ in $t .$

For each server cluster, the working frequency is the sum of the working frequency for both real-time and delay-tolerant workloads. The working frequency also has upper and lower bounds, as shown in (17). The power of DC is related to the working frequency, as shown in (18).

$$
0 \leq f _ {\mathrm{RT}} ^ {s _ {i}} (t) + f _ {\mathrm{DT}} ^ {s _ {i}} (t) \leq f _ {\max} ^ {s _ {i}}\tag{17}
$$

$$
P _ {\mathrm{DC}} (t) = \sum_ {s _ {i} = 1} ^ {N _ {s}} \left[ P _ {\text { idle }} ^ {s _ {i}} + \left(P _ {\text { idle }} ^ {s _ {i}} - P _ {\text { dyn }} ^ {s _ {i}}\right) \frac {f _ {\mathrm{RT}} ^ {s _ {i}} (t) + f _ {\mathrm{DT}} ^ {s _ {i}} (t)}{f _ {\max} ^ {s _ {i}}} \right]\tag{18}
$$

where $P _ { \mathrm { D C } } ( t )$ is the power of DC in t . $P _ { \mathrm { i d l e } } ^ { s _ { i } }$ and $P _ { \mathrm { d y n } } ^ { s _ { i } }$ refer to the idle and dynamic power of $s _ { i } .$ respectively. The server clusters in the DC emit heat during work.

The emitted heat by the DC $H _ { \mathrm { D C } } ( t )$ is linear to the power with parameter \~<sub>DC</sub>, as shown in (19). The emitted heat increases the temperature of the DC. Note the DC as a whole part, the change of DC temperature $\Delta T _ { \mathrm { D C } } ( t )$ can be expressed as the linear function (20) [27]. The A/C system is used to cool the DC. The heat dissipation of the A/C system is noted as $Q _ { \mathrm { A C } } ( t )$ , of which the expression is illustrated in (21).

$$
H _ {\mathrm{DC}} (t) = \varkappa_ {\mathrm{DC}} P _ {\mathrm{DC}} (t)\tag{19}
$$

$$
\Delta T _ {\mathrm{DC}} (t) = \frac {H _ {\mathrm{DC}} (t) - Q _ {\mathrm{AC}} (t)}{C _ {\mathrm{DC}} M _ {\mathrm{DC}}}\tag{20}
$$

$$
Q _ {\mathrm{AC}} (t) = \varkappa_ {\mathrm{AC}} P _ {\mathrm{AC}} (t)\tag{21}
$$

where $C _ { \mathrm { D C } }$ is the DC thermal capacitance. $M _ { \mathrm { D C } }$ is the mass of the DC. ${ \varkappa _ { \mathrm { A C } } }$ is the linear parameter. $P _ { \mathrm { A C } } ( t )$ is the power of $\mathbf { A } / \mathbf { C }$

In this study, the discrete-time difference equation (22) is used to model the DC temperature $T _ { \mathrm { D C } } ( t )$ [19].

$$
T _ {\mathrm{DC}} (t) = \kappa T _ {\mathrm{DC}} (t - 1) + (1 - \kappa) [ T _ {\mathrm{out}} (t - 1) + \Delta T _ {\mathrm{DC}} (t - 1) ]\tag{22}
$$

where $T _ { \mathrm { o u t } } ( t )$ is outside temperature in t. The parameter κ is set to be $\begin{array} { r } { \exp ( - \frac { \Delta t } { C _ { \mathrm { D C } } R _ { \mathrm { D C } } } ) } \end{array}$ , where $R _ { \mathrm { D C } }$ is its thermal resistance. The outside temperature can be divided into two parts, including the forecasting outside temperature $T _ { \mathrm { o u t , f o r e } } ( t )$ and the forecasting error $T _ { \mathrm { o u t , e r r } } ( t )$ , as shown in (23).

$$
T _ {\mathrm{out}} (t) = T _ {\mathrm{out,fore}} (t) + T _ {\mathrm{out,err}} (t)\tag{23}
$$

To maintain the normal operation of the server clusters, the DC temperature should not be higher than the pre-set temperature $T _ { \mathrm { s e t } }$ , as shown in (24).

$$
T _ {\mathrm{DC}} (t) \leq T _ {\mathrm{set}}\tag{24}
$$

In H<sub>2</sub>-DCMG, the general load $P _ { \mathrm { L o a d } } ( t )$ , such as loads of the lighting system and the workloads storage system, are also considered to be uncertain. The general load is reflected in the power balance constraint (25).

$$
\begin{array}{r l} P _ {\mathrm{fc}} (t) + P _ {\mathrm{grid}} (t) + P _ {\mathrm{RE}} (t) & = P _ {\mathrm{el}} (t) + P _ {\mathrm{DC}} (t) + P _ {\mathrm{AC}} (t) \\ & \quad + P _ {\mathrm{comp}} (t) + P _ {\mathrm{Load}} (t) \end{array}\tag{25}
$$

where $P _ { \mathrm { L o a d } } ( t ) \in \mathcal { L } , \mathcal { L }$ is the uncertainty set of the general load.

## B. Statistical Feasibility-Based Constraint

As mentioned earlier, slight violations of the temperature constraint within a controlled range will not harm the normal operations of DC [18]. To this end, statistical feasibility is introduced into the framework to describe the slight violations of temperature bound. Its mathematical definition is presented below [17].

Definition 1 (Statistical Feasibility): For the chance constraint (26)

$$
\operatorname * {P r} [ g (x; \varpi) \in \mathcal {V} ] \geq 1 - \varepsilon\tag{26}
$$

where Pr[·] is the probability of · $. \ g ( x ; \varpi ) \in \mathcal { V }$ is a constraint with a random vector $\varpi$ and a decision variable vector x. $\varepsilon$ is the requirement for the chance constraints. Assuming that there is a sampled dataset $\Psi = \{ \vartheta _ { 1 } , \vartheta _ { 2 } , . . . , \vartheta _ { n } \}$ , and an uncertainty set $\mathcal { U } ( \Psi )$ is constructed based on 9. An algorithm is statistically feasible if the resulting solution $x ^ { * }$ is feasible for the chance constraint (26) with confidence $1 - \delta$ . Its mathematical formulation is described in (27).

$$
\operatorname * {P r} _ {\Psi} \{\operatorname * {P r} [ g (x; \varpi) \in \mathcal {V} ] \geq 1 - \varepsilon \} \geq 1 - \delta\tag{27}
$$

Statistical feasibility can be adopted in the framework directly. With the requirement of chance constraint $1 - \varepsilon$ and confidence in statistical feasibility $1 - \delta ,$ constraint (24) can be rewritten as a statistical feasibility-based constraint (28).

$$
\operatorname * {P r} \{\operatorname * {P r} [ T _ {\mathrm{DC}} (t) \leq T _ {\mathrm{set}} ] \geq 1 - \varepsilon \} \geq 1 - \delta\tag{28}
$$

The difference between the DC temperature bound (24), chance constraint and the statistical feasibility-based constraint (28) is illustrated in Fig. 3. In the traditional constraint, the DC temperature is maintained below the pre-set bound. Traditional RO aims to maintain the dispatch results to satisfy this constraint in all situations, which leads to the conservatism of RO. Differently, in chance constraint, the DC temperature bound is not strict. The probability that the constraint is satisfied in the dispatch results has to be no less than the preset value. The statistical-feasibility-based constraint (28) is a further relaxation of the chance constraint. In (28), the probability of satisfying the chance constraint is no less than the pre-set value, which gives room for dispatch results to violate the chance constant. In other words, the adoption of statisticalfeasibility-based constraint provides more opportunity for the dispatch results of violating the DC temperature bound, and reduces the conservatism of RO.

## C. RO Problem Construction

As described in the rolling dispatch framework, in each time slot $k ,$ the period of the subproblem contains the present and future N time slots $( \mathrm { i . e . , } \ k \ \sim \ k + \ N )$ . The maximum RE output, the arrival rate of real-time workloads and the outside temperature in the forecasting period $k + 1 \sim k + N$ cannot be forecasted perfectly. The future maximum RE output, the arrival rate of real-time workload, outside temperature and the general load in the future N time slots are uncertain. These uncertainties are in uncertainty sets $\mathcal { P } , \mathcal { W } \mathcal { T }$ , and ${ \mathcal { L } } .$ respectively. However, since these present parameters can be monitored by the monitoring system directly, the maximum

![](duan2025_robust_h2_dcmg_assets/images/6aeb23ac9aa311cddc9c625649bf67c720c4ddc440419bfda85fa297acd3cb5a.jpg)  
Fig. 3. The illustration of constraint, chance constraint and statistical feasibility-based constraint.

RE output, arrival rate of real-time workload and outside temperature in k are determined values.

In the RO problem, the total power cost is to be optimized. The total cost, noted as $\mathbb { C } ,$ contains two parts. The first part is the cost of purchasing power from the power grid with the time-of-use price $\rho _ { \mathrm { g r i d } } ( t )$ . The other part contains the aging cost of the electrolyzer, fuel cell, RE generators and A/C. These aging prices are noted as ρ<sub>el</sub>, ρ<sub>fc</sub>, ρ<sub>RE</sub> and $\rho _ { \mathrm { A C } } .$ respectively. The total cost is described in (29).

$$
\begin{array}{l} \mathbb {C} = \sum_ {t = k} ^ {k + N} [ \rho_ {\text { grid }} (t) \cdot P _ {\text { grid }} (t) + \rho_ {\text { el }} \cdot P _ {\text { el }} (t) + \rho_ {\text { fc }} \cdot P _ {\text { fc }} (t) \\ \quad + \rho_ {\text { RE }} \cdot P _ {\text { RE }} (t) + \rho_ {\text { AC }} \cdot P _ {\text { AC }} (t) ] \end{array}\tag{29}
$$

The cost in the forecasting period is uncertain because of its relation to the uncertainties, $P _ { \mathrm { R E } } ^ { \mathrm { m a x } } ( t ) , \ W _ { \mathrm { R T , A } } ( t )$ and $T _ { \mathrm { o u t , e r r } } ( t )$ In RO, the worst cases of these uncertainties are considered. Consequently, the RO problem to be solved in each time slot $k$ is organized as (30).

$$
\begin{array}{l} \min \quad \mathbb {C} \\ \text { s.t. } (1) \sim (2 3), (2 8) \sim (2 5), t = k \sim k + N; \\ P _ {\mathrm{RE}} ^ {\max} (t) \in \mathscr {P}, W _ {\mathrm{RT,A}} (t) \in \mathscr {W}, \\ T _ {\mathrm{out,err}} (t) \in \mathscr {T}, P _ {\mathrm{Load}} (t) \in \mathscr {L}, \\ t = k + 1 \sim k + N. \end{array}\tag{30}
$$

## III. CONSTRUCTION OF UNCERTAINTY SETS

In the RO problem (30), four different uncertainty sets, $\mathcal { P }$ $\mathcal { W } , \mathcal { T }$ and ${ \mathcal { L } } .$ , are used to describe the uncertainty of $P _ { \mathrm { R E } } ^ { \mathrm { m a x } } ( t )$ $W _ { \mathrm { R T , A } } ( t ) , T _ { \mathrm { o u t , e r r } } ( t )$ and $P _ { \mathrm { L o a d } } ( t )$ in the forecast period. In this section, the data-driven methods used to construct these three uncertainty sets are presented.

## A. Construction of Uncertainty Sets ${ \mathcal P } ,$ , W and $\mathcal { L }$

In this study, P, W and L are box uncertainty sets. Their expressions are shown in (31), (32) and (33), respectively.

$$
\mathcal {P}: \{P _ {\mathrm{RE}} ^ {\max} (t) | \underline {{P _ {\mathrm{RE}} ^ {\max}}} (t) \leq P _ {\mathrm{RE}} ^ {\max} (t) \leq \overline {{P _ {\mathrm{RE}} ^ {\max}}} (t) \}\tag{31}
$$

$$
\mathscr {W}: \{W _ {\mathrm{RT,A}} (t) | \underline {{W _ {\mathrm{RT,A}}}} (t) \leq W _ {\mathrm{RT,A}} (t) \leq \overline {{W _ {\mathrm{RT,A}}}} (t) \}\tag{32}
$$

$$
\mathcal {L}: \{P _ {\mathrm{Load}} (t) | \underline {{P _ {\mathrm{Load}}}} (t) \leq P _ {\mathrm{Load}} (t) \leq \overline {{P _ {\mathrm{Load}}}} (t) \}\tag{33}
$$

where $P _ { \mathrm { R E } } ^ { \mathrm { m a x } } ( t ) , ~ \overline { { P _ { \mathrm { R E } } ^ { \mathrm { m a x } } } } ( t )$ are the lower and upper bounds of $P _ { \mathrm { R E } } ^ { \mathrm { m a x } } ( t )$ $W _ { \mathrm { R T , A } } ( t )$ , W<sub>RT,A</sub>(t ), $P _ { \mathrm { L o a d } } ( t )$ and $\overline { { P _ { \mathrm { L o a d } } } } ( t )$ are the lower and upper bounds of $W _ { \mathrm { R T , A } } ( t )$ and $P _ { \mathrm { L o a d } } ( t )$ , respectively.

All the bounds mentioned above are determined based on the historical maximum forecasting error. Take $W _ { \mathrm { R T , A } } ( t )$ as an example. The maximum forecasting error of $W _ { \mathrm { R T , A } } ( t )$ for forecasting a $( 1 \leq a \leq N )$ time slots in advance is $\alpha _ { a }$ . The real value (monitored later) is $W _ { a }$ . The forecasted valued in $t + a$ obtained from the forecasting system in t is $W _ { \mathrm { R T , A , F } } ( t + a )$ The upper and lower bound in $\mathcal { W }$ in $t + a$ can be obtained through increasing or decreasing the ratio of $\alpha _ { a }$ to $W _ { a }$ from the forecasted value $W _ { \mathrm { R T , A , F } } ( t + a )$ , as shown in (34) and (35).

$$
\underline {{W _ {\mathrm{RT,A}}}} (t + a) = W _ {\mathrm{RT,A,F}} (t + a) \cdot (1 - \alpha_ {a} / W _ {a})\tag{34}
$$

$$
\overline {{W _ {\mathrm{RT,A}}}} (t + a) = W _ {\mathrm{RT,A,F}} (t + a) \cdot (1 + \alpha_ {a} / W _ {a})\tag{35}
$$

Similarly, the upper and lower bounds in $\mathcal { P }$ and $\mathcal { L }$ can be obtained in (36) ∼ (39).

$$
\underline {{P _ {\mathrm{RE}} ^ {\max}}} (t + a) = P _ {\mathrm{RE,F}} ^ {\max} (t + a) \cdot (1 - \beta_ {a} / P _ {a})\tag{36}
$$

$$
\overline {{P _ {\mathrm{RE}} ^ {\max}}} (t + a) = P _ {\mathrm{RE,F}} ^ {\max} (t + a) \cdot (1 + \beta_ {a} / P _ {a})\tag{37}
$$

$$
\underline {{P _ {\mathrm{Load}}}} (t + a) = P _ {\mathrm{Load,F}} (t + a) \cdot (1 - \theta_ {a} / L _ {a})\tag{38}
$$

$$
\overline {{P _ {\mathrm{Load}}}} (t + a) = P _ {\mathrm{Load,F}} (t + a) \cdot (1 + \theta_ {a} / L _ {a})\tag{39}
$$

where $P _ { \mathrm { R E , F } } ^ { \mathrm { m a x } } ( t + a )$ is the maximum RE output in $t + a$ forecasted in $t . ~ \beta _ { a }$ and $P _ { a }$ are the maximum forecasting error in a in advance and the monitored real value, respectively. $P _ { \mathrm { L o a d , F } } ( t + a )$ is the general load in $t + a$ forecasted in t. $\theta _ { a }$ and $L _ { a }$ are the maximum forecasting error in a in advance and the monitored real general loads.

## B. Construction of Outside Temperature Uncertainty Set T

In this subsection, the SGVL algorithm is proposed to construct a polyhedron uncertainty set for the outside temperature forecasting errors. Based on the constructed polyhedron uncertainty set, the statistically feasible of the DC temperature is guaranteed. Previously, a framework for constructing such uncertainty sets is outlined in [17]. However, only the ellipsoid uncertainty set construction method is described. The ellipsoid uncertainty set will be converted to a second-order cone constraint, which leads to inconvenience when solving. Consequently, in this study, the SGVL algorithm is proposed to construct a polyhedron uncertainty set while guaranteeing statistically feasible solutions.

The process of the SGVL is shown in Fig. 4. The data in the dataset 9 used in SGVL are N -dimensional forecasting error vectors. One data is a forecasting error vector of one forecasting period, and its $a ^ { \mathrm { t h } } ~ ( 1 \leq a \leq N )$ dimension refers to the forecasting error in $a ^ { \mathrm { t h } }$ in the forecasting period.

1) Data Splitting: The historical outside temperature forecasting error dataset 9 is split into two datasets $\Psi _ { 1 }$ and $\Psi _ { 2 }$ randomly, as shown in Fig. 4(a) and (b). $n _ { 1 }$ and $n _ { 2 }$ are the amount of data in $\Psi _ { 1 }$ and $\Psi _ { 2 }$ , respectively. To guarantee statistical feasibility, the restriction (40) related to $n _ { 2 }$ should be satisfied.

$$
n _ {2} \geq \frac {\log \delta}{\log (1 - \varepsilon)}\tag{40}
$$

For simplicity, $n _ { 2 }$ is set to $M \cdot \left\lceil { \frac { \log \delta } { \log ( 1 - \varepsilon ) } } \right\rceil$ , where ⌈·⌉ means the upward rounding of ·. M is a natural number.

2) Shape Learning: In this step, the dataset $\Psi _ { 1 }$ is used to determine the ellipsoid uncertainty set, as the green solid ellipsoid in Fig. 4(c). Assuming that the mean vector and the covariance matrix of $\Psi _ { 1 }$ are noted as $\bar { \mu }$ and 6, and an ellipsoid can be determined as (41).

$$
(\boldsymbol {\vartheta} - \bar {\boldsymbol {\mu}}) ^ {\top} \Sigma^ {- 1} (\boldsymbol {\vartheta} - \bar {\boldsymbol {\mu}}) \leq 1\tag{41}
$$

where $\pmb { \vartheta } = [ T _ { \mathrm { o u t , e r r } } ( k + 1 ) , \ldots , T _ { \mathrm { o u t , e r r } } ( k + N ) ] .$

Then, the ellipse is converted to the dashed ellipse through rotation in Fig. 4(c). The eigenvalue decomposition of $\Sigma ^ { - 1 }$ is noted as $\mathbf { P } ^ { \top } \wedge \mathbf { P } ,$ where $\begin{array} { r } { \Lambda = \mathrm { d i a g } ( \lambda _ { 1 } , \dots , \lambda _ { N } ) , \ \lambda _ { i } , i \ = } \end{array}$ $1 , \ldots , N$ are the eigenvalues of $\Sigma ^ { - 1 }$ , and P is the matrix consists of its eigenvectors. The dashed ellipsoid is described in (42).

$$
\left(\boldsymbol {\vartheta} ^ {\prime}\right) ^ {\top} \Lambda \boldsymbol {\vartheta} ^ {\prime} \leq 1\tag{42}
$$

where $\pmb { \vartheta } ^ { \prime } = \mathbf { P } ( \pmb { \vartheta } - \pmb { \bar { \mu } } )$

3) Shape Calibration: There are 2N vertexes in ellipsoid (42), as shown in the red points in Fig. 4(c). The vertexes on the positive and negative semi-axis consist of matrix $\mathbf { V } ^ { + }$ and $\mathbf { V } ^ { - }$ , respectively. The formulations of these two matrixes are shown in (43) and (44).

$$
\mathbf {V} ^ {+} = \left[ \boldsymbol {v} _ {1} ^ {+}, \boldsymbol {v} _ {2} ^ {+}, \dots , \boldsymbol {v} _ {N} ^ {+} \right] = \operatorname{diag} \left[ \lambda_ {1} ^ {- \frac {1}{2}}, \lambda_ {2} ^ {- \frac {1}{2}}, \dots , \lambda_ {N} ^ {- \frac {1}{2}} \right]\tag{43}
$$

$$
\mathbf {V} ^ {-} = \left[ \boldsymbol {v} _ {1} ^ {-}, \boldsymbol {v} _ {2} ^ {-}, \dots , \boldsymbol {v} _ {N} ^ {-} \right] = - \operatorname{diag} \left[ \lambda_ {1} ^ {- \frac {1}{2}}, \lambda_ {2} ^ {- \frac {1}{2}}, \dots , \lambda_ {N} ^ {- \frac {1}{2}} \right]\tag{44}
$$

where $v _ { i } ^ { + }$ and $v _ { i } ^ { - } , i = 1 , \ldots , N$ refer to the $i ^ { \mathrm { { t h } } }$ vertexes located on positive and negative semi-axis, respectively. Then, take a vertex on each of the different axes, and a matrix consisting of N vertexes is constructed. Totally, $2 ^ { N }$ different matrixes are constructed, as shown in (45).

$$
\mathbf {W} _ {j} = [ \boldsymbol {v} _ {1} ^ {\pm}, \boldsymbol {v} _ {2} ^ {\pm}, \dots , \boldsymbol {v} _ {N} ^ {\pm} ], j = 1, \dots , 2 ^ {N}\tag{45}
$$

For a matrix $\mathbf { W } _ { j ^ { * } } = [ \pmb { v } _ { 1 } ^ { * } , \pmb { v } _ { 2 } ^ { * } , \ldots , \pmb { v } _ { N } ^ { * } ]$ , the hyperplane over these vertexes can be obtained through solving (46) with a vector of the unknown $\pmb { a } _ { \ast }$

$$
\boldsymbol {a} _ {*} ^ {\top} \mathbf {W} _ {j *} = \mathbb {1} ^ {\top}\tag{46}
$$

where <sup>1</sup> is a N -dimensional vector with elements all 1. Then, a∗ can be obtained as (47)

$$
\boldsymbol {a} _ {*} ^ {\top} = \mathbb {1} ^ {\top} \mathbf {W} _ {j ^ {*}} ^ {- 1}\tag{47}
$$

In light of (47), the parameters of all the $2 ^ { N }$ hyperplanes can be obtained as (48).

$$
\boldsymbol {a} _ {j} ^ {\top} = \mathbb {1} ^ {\top} \mathbf {W} _ {j} ^ {- 1}, \forall j = 1, \dots , 2 ^ {N}\tag{48}
$$

![](duan2025_robust_h2_dcmg_assets/images/a5dec0610202424a5b0fe69967d4a08b8abb3670efb511518acac120059b875b.jpg)  
Fig. 4. Diagram of Statistical-Guarantee-based Vertex Link algorithm.

4) Size Calibration: Firstly, construct a map $\begin{array} { r l } { t ( \pmb { \vartheta } ) } & { { } = } \end{array}$ max<sub>j</sub> $\pmb { a } _ { i } ^ { \top } \mathbf { P } ( \pmb { \vartheta } - \pmb { \bar { \mu } } ) . \forall \pmb { \vartheta } \in \Psi _ { 2 } ,$ the value of map t (ϑ) can be sorted in ascending order, as shown in (49).

$$
t (\boldsymbol {\vartheta} _ {(1)}) \leq t (\boldsymbol {\vartheta} _ {(2)}) \leq \dots \leq t (\boldsymbol {\vartheta} _ {(n _ {2})})\tag{49}
$$

Then, calculate $l ^ { * }$ according to (50).

$$
l ^ {*} = \min \left\{l: \sum_ {m = 0} ^ {l - 1} \binom {n _ {2}} {m} (1 - \varepsilon) ^ {m} \varepsilon^ {n _ {2} - m} \geq 1 - \delta , 1 \leq l \leq n _ {2} \right\}\tag{50}
$$

The size parameter of the uncertainty set can be obtained: $\rho ~ = ~ t ( \vartheta _ { ( l ^ { * } ) } )$ , and the polyhedron uncertainty set $\mathcal { T } _ { \mathfrak { p } }$ is constructed as (51).

$$
\mathcal {T} _ {\mathrm{p}}: \left\{\boldsymbol {\vartheta} | \boldsymbol {a} _ {j} ^ {\top} \mathbf {P} (\boldsymbol {\vartheta} - \bar {\boldsymbol {\mu}}) \leq \rho , \forall j = 1, \dots , 2 ^ {N} \right\}\tag{51}
$$

Similarly, for the ellipsoid uncertainty set $\mathcal { T } _ { \mathrm { e } }$ , the map is constructed as $t _ { \mathrm { e } } ( \pmb { \vartheta } ) = ( \pmb { \vartheta } - \pmb { \bar { \mu } } ) ^ { \top } \Sigma ^ { - 1 } ( \pmb { \vartheta } - \pmb { \bar { \mu } } )$ $\forall \vartheta \in \Psi _ { 2 } ,$ compute their map values and sort the map values as (49). Then, its size parameter can be obtained: $\rho _ { \mathrm { e } } = t _ { \mathrm { e } } ( \vartheta _ { ( l ^ { \ast } ) } )$ , and the ellipsoid uncertainty set can also be established as (52).

$$
\mathcal {T} _ {\mathrm{e}}: \left\{\boldsymbol {\vartheta} | (\boldsymbol {\vartheta} - \bar {\boldsymbol {\mu}}) ^ {\top} \Sigma^ {- 1} (\boldsymbol {\vartheta} - \bar {\boldsymbol {\mu}}) \leq \rho_ {\mathrm{e}} \right\}\tag{52}
$$

The process of the SGVL algorithm is described in Algorithm 1. The statistical feasibility guarantee of the SGVL algorithm mainly lies on the Lemma 3 of [17]. For the convenience of the reader, the essentials of the proof, with modifications to adjust our context, are repeated here.

Theorem 1: Let $\varepsilon , \delta \in [ 0 , 1 ]$ be real numbers, and choose a real number n satisfying

$$
n \geq \frac {\log \delta}{\log (1 - \varepsilon)}\tag{53}
$$

Then, (54) is satisfied.

$$
1 - \delta \leq 1 - (1 - \varepsilon) ^ {n}\tag{54}
$$

Proof of Theorem 1: $\forall n \in$ <sup>R</sup> satisfying (53), (55) is satisfied.

$$
n \log (1 - \varepsilon) \leq \log \delta\tag{55}
$$

Obviously, for $\varepsilon , \delta \in [ 0 , 1 ]$ 2

$$
(1 - \varepsilon) ^ {n} \leq \delta\tag{56}
$$

Then, $1 - \delta \leq 1 - ( 1 - \varepsilon ) ^ { n }$

Theorem 2 (Statistical Guarantee): Let $F _ { X }$ be a continuous distribution function and $X _ { 1 } , \dots , X _ { n } \in$ <sup>R</sup> be its independent and identically distributed samples. $X _ { ( 1 ) } < X _ { ( 2 ) } < \cdots < X _ { ( n ) }$ is used to denote the order statistics of $X _ { 1 } , \dots , X _ { n } .$ . For a given $\varepsilon \in [ 0 , 1 ]$ , choose δ satisfying $0 < 1 - \delta \leq 1 - ( 1 - \varepsilon ) ^ { n }$ . Then, for any natural number $l ~ ( 1 \leq l \leq n )$ satisfying

$$
\sum_ {k = l} ^ {n} \binom {n} {k} (1 - \varepsilon) ^ {k} \varepsilon^ {n - k} \leq \delta\tag{57}
$$

and then

$$
\operatorname * {P r} _ {F _ {X}} \{F _ {X} (X _ {(l)}) \geq 1 - \varepsilon \} \geq 1 - \delta\tag{58}
$$

Proof of Theorem 2: Let $s _ { 1 - \varepsilon }$ be the (1 − ε)-quantile of $F _ { X }$ . As a simple computation, (59) can be obtained.

$$
\begin{array}{c} \operatorname * {P r} _ {F _ {X}} \{F _ {X} (X _ {(l)}) > 1 - \varepsilon \} = \operatorname * {P r} _ {F _ {X}} \{X _ {(l)} > s _ {1 - \varepsilon} \} \\ = 1 - \operatorname * {P r} _ {F _ {X}} \{X _ {(l)} \leq s _ {1 - \varepsilon} \} \end{array}\tag{59}
$$

For the order statistics $X _ { ( i ) } , i = 1 , \dotsc , n ,$ (60) is right. For the ease of readers, two examples are presented in Appendix C that support (60).

$$
\begin{array}{r l} \operatorname * {P r} _ {F _ {X}} \left\{X _ {(l)} \leq s _ {1 - \varepsilon} \right\} & = \sum_ {m = l} ^ {n} \binom {n} {m} F _ {X} (s _ {1 - \varepsilon}) ^ {m} [ 1 - F _ {X} (s _ {1 - \varepsilon}) ] ^ {n - m} \\ & = \sum_ {m = l} ^ {n} \binom {n} {m} (1 - \varepsilon) ^ {m} \varepsilon^ {n - m} \end{array} \tag {60}
$$

Following that (59) and (60), (57) implies (58).

Based on Theorem 1 and Theorem 2, for $l ^ { * }$ obtained by (50), Eqn. (57) is satisfied, obviously. Based on the (49) and Theorem 1, (61) can be obtained.

$$
\operatorname * {P r} _ {F _ {t}} \left\{F _ {t} \left[ t \left(\boldsymbol {\vartheta} _ {(l ^ {*})}\right) \right] \geq 1 - \varepsilon \right\} \geq 1 - \delta\tag{61}
$$

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1 SGVL Algorithm

Input: Ψ with n data inside;
Output:  $a_{j}^{\top}$ , P,  $\bar{\mu}$  and  $\rho$ ;
/* Data Splitting */
1:  $n_{2} \leftarrow M \cdot \lceil \log \delta / \log(1 - \varepsilon) \rceil$ ,  $n_{1} \leftarrow n - n_{2}$ ;
2: Split Ψ into Ψ₁ and Ψ₂ randomly;
/* Shape Learning */
3: Compute the  $\bar{\mu}$  and Σ of Ψ₁;
4: Compute eigenvalue decomposition of Σ = PᵀΛP;
/* Shape Calibration */
5: Construct matrixes V⁺ and V⁻ with (42) and (43);
6: Initialize all Wⱼ, j = 1, ..., 2ᴺ to empty matrixes;
7: for i = 1, ..., N do
8: j ← 1;
9: for k = 1, ..., 2ⁱ⁻¹ do
10: for l = 1 : 2ᴺ⁻ⁱ do
11: Wⱼ ← [Wⱼ vᵢ⁺];
12: j ← j + 1;
13: end for
14: for l = 1 : 2ᴺ⁻ⁱ do
15: Wⱼ ← [Wⱼ vᵢ⁻];
16: j ← j + 1;
17: end for
18: end for
19: end for
20: for j = 1, ..., 2ᴺ do
21:  $a_{j}^{\top} \leftarrow \mathbb{1}^{\top} W_{j}^{-1}$ ;
22: end for
/* Size Calibration */
23: ∀θ ∈ Ψ₂, compute t(θ) ← maxⱼ  $a_{j}^{\top} P(\theta - \bar{\mu})$  and sort the values as (49);
24: Calculate l* using (50);
25: ρ ← t(θ(l*))
</div>

where $F _ { t }$ is the distribution function of $t ( \vartheta )$ , and its definition is given in (62).

$$
F _ {t} (\rho) = \operatorname * {P r} \{t (\boldsymbol {\vartheta}) \leq \rho \} = \operatorname * {P r} \{\boldsymbol {\vartheta} \in \mathcal {T} _ {\mathrm{p}} \}\tag{62}
$$

Assuming that the data in the forecasting period has the same distribution as the data in 9. Based on this assumption, for the data in the forecasting period $\vartheta _ { f }$ , (63) is satisfied.

$$
\operatorname * {P r} _ {F _ {t}} \{\operatorname * {P r} \{\boldsymbol {\vartheta} _ {f} \in \mathcal {T} _ {\mathrm{p}} \} \geq 1 - \varepsilon \} \geq 1 - \delta\tag{63}
$$

Consequently, for the feasible solutions to problem (30), the statistical feasibility is satisfied, as shown in (64).

$$
\operatorname * {P r} \{\operatorname * {P r} \{T _ {\mathrm{DC}} (t) \leq T _ {\mathrm{set}} \} \geq 1 - \varepsilon \} \geq 1 - \delta\tag{64}
$$

## C. Reconstruction of Optimization Problem

In this subsection, the problem (30) is transferred to a mixed-integer linear programming problem, which is to be solved by solvers directly. The problem (30) contains four kinds of uncertainty parameters: the maximum RE $P _ { \mathrm { R E } } ^ { \mathrm { m a x } } ( t )$ the arrival rate of real-time workload $W _ { \mathrm { R T , A } } ( t )$ , the outside temperature forecasting error $T _ { \mathrm { o u t , e r r } } ( t )$ and the general load in the DC $P _ { \mathrm { l o a d } } ( t )$

For the RE constraint (1), the worst case is the case in which the RE output is minimal, as shown in (65).

$$
P _ {\mathrm{RE}} (t) \leq \inf _ {P _ {\mathrm{RE}} ^ {\max} (t) \in \mathscr {P}} P _ {\mathrm{RE}} ^ {\max} (t) = \underline {{P _ {\mathrm{RE}} ^ {\max}}} (t)\tag{65}
$$

The worst case of the arrival rate of real-time workload $W _ { \mathrm { R T , A } } ( t )$ is defined as follows. As shown in (12), $W _ { \mathrm { R T , A } } ( t )$ influences the real-time workload of server cluster $s _ { i }$ in t . Thus, the worst case of the arrival rate of real-time workload can be defined as the case that is most likely to violate constraint (13), as shown in (66).

$$
\begin{array}{l} \inf _ {(1 2), W _ {\mathrm{RT,A}} (t) \in \mathscr {W}} [ f _ {\mathrm{RT}} ^ {s _ {i}} (t) \mu - W _ {\mathrm{RT}} (t) ] \\ = f _ {\mathrm{RT}} (t) \mu - \sup _ {(1 2), W _ {\mathrm{RT,A}} (t) \in \mathscr {W}} W _ {\mathrm{RT}} (t) \geq \frac {1}{D} \end{array}\tag{66}
$$

$W _ { \mathrm { R T } } ^ { s _ { i } } ( t )$ is dispatchable, and the summation of all $W _ { \mathrm { R T } } ^ { s _ { i } } ( t )$ of all server clusters in t is $W _ { \mathrm { R T , A } } ( t )$ . Therefore, the worst case of $W _ { \mathrm { R T , A } } ( t )$ is its maximum value, as shown in (67).

$$
W _ {\mathrm{RT,A}} (t) = \overline {{W _ {\mathrm{RT,A}}}} (t)\tag{67}
$$

For the uncertain $P _ { \mathrm { l o a d } } ( t )$ , the worst case cannot be obtained as above. Here, the method from [28] is introduced to obtain its worst case. For ease of expression, the power balance constraint (25) is rewritten as (68).

$$
\operatorname{Generation} (t) = \operatorname{Demand} (t) + P _ {\text { load }} (t)\tag{68}
$$

where Generation $\begin{array} { r } { \mathbf { \Omega } ( t ) = P _ { \mathrm { f c } } ( t ) + P _ { \mathrm { g r i d } } ( t ) + P _ { \mathrm { R E } } ( t ) } \end{array}$ , Demand(t) = $P _ { \mathrm { e l } } ( t ) + P _ { \mathrm { D C } } ( t ) + P _ { \mathrm { A C } } ( t ) + P _ { \mathrm { c o m p } } ( t )$ . Assuming that the number of times that the forecasted general load is deviated is limited, and each deviation means that the general load is on the edge of ${ \mathcal { L } } .$ . Thus, (68) can be presented as (69).

$$
\begin{array}{l} \text { Generation } (t) = \text { Demand } (t) + P _ {\text { Load,F }} (t) \\ \quad - | P _ {\text { Load,F}} (t) - \overline {{P _ {\text { Load }} (t)}} | \cdot I _ {\text { Load }} ^ {\min} (t) \\ \quad + | P _ {\text { Load,F }} (t) - \overline {{\overline {{P _ {\text { Load }}}} (t)}} | \cdot I _ {\text { Load }} ^ {\max} (t) \end{array}\tag{69}
$$

where $I _ { \mathrm { L o a d } } ^ { \mathrm { m i n } } ( t )$ and $I _ { \mathrm { L o a d } } ^ { \mathrm { m a x } } ( t )$ are both binary variables. In each $t , I _ { \mathrm { L o a d } } ^ { \mathrm { m i n } } ( t )$ and $I _ { \mathrm { L o a d } } ^ { \mathrm { m a x } } ( t )$ cannot be 1 at the same time, as shown in (70). Moreover, time intervals cannot exceed the pre-set bound 0, as shown in (71).

$$
I _ {\mathrm{Load}} ^ {\mathrm{min}} (t) + I _ {\mathrm{Load}} ^ {\mathrm{max}} (t) \leq 1\tag{70}
$$

$$
\sum_ {t = 1} ^ {T} [ I _ {\mathrm{Load}} ^ {\min} (t) + I _ {\mathrm{Load}} ^ {\max} (t) ] \leq \Gamma\tag{71}
$$

By adding constraints $( 6 9 ) \sim ( 7 1 )$ into the optimization problem, the general load is addressed.

In RO, the maximum DC temperature caused by the uncertain outside temperature is expected to be lower than the pre-set temperature bound, as shown in (72).

$$
\sup _ {T _ {\text { out,err }} (t) \in \mathcal {T}} T _ {\mathrm{DC}} (t) \leq T _ {\text { set }}, \forall t = k + 1 \sim k + N\tag{72}
$$

To identify the maximum DC temperature caused by the uncertain outside temperature, the DC temperature in $t = k +$ $1 \sim k + N$ needs to be calculated based on the DC temperature in $t \ = \ k$ . Moreover, only the influence of the uncertainty outside temperature is considered, and the influence of $\Delta T _ { \mathrm { D C } }$ is ignored. Thus, (22) can be simplified as (73).

$$
T _ {\mathrm{DC}} (t) = \kappa T _ {\mathrm{DC}} (t - 1) + (1 - \kappa) [ T _ {\mathrm{out,fore}} (t - 1) + T _ {\mathrm{out,err}} (t - 1) ]\tag{73}
$$

All $T _ { \mathrm { D C } } ( t )$ follows (73). $\forall t = k + 1 \sim k + N .$ , the DC temperature can be forecasted in k as shown in (74).

$$
T _ {\mathrm{DC}} (t) = \kappa^ {t - k} T _ {\mathrm{DC}} (k) + \kappa^ {\top} [ t _ {\mathrm{out,fore}} (t) + t _ {\mathrm{out,err}} (t) ]\tag{74}
$$

where the vectors κ, ${ \pmb t } _ { \mathrm { o u t , f o r e } } ( t )$ and $\pmb { t } _ { \mathrm { o u t , e r r } } ( t )$ are defined as follows.

$$
\begin{array}{c} \boldsymbol {\kappa} = (1 - \kappa) [ \kappa^ {t - k - 1}, \kappa^ {t - k - 2}, \ldots , \kappa^ {0} ] ^ {\top} \\ \boldsymbol {t} _ {\text { out,fore }} (t) = [ T _ {\text { out,fore }} (t - 1), T _ {\text { out,fore }} (t - 2), \ldots , T _ {\text { out,fore }} (k) ] ^ {\top} \\ \boldsymbol {t} _ {\text { out,err }} (t) = [ T _ {\text { out,err }} (t - 1), T _ {\text { out,err }} (t - 2), \ldots , T _ {\text { out,err }} (k) ] ^ {\top} \end{array}
$$

$T _ { \mathrm { D C } } ( k )$ and ${ \pmb t } _ { \mathrm { o u t , f o r e } } ( t )$ are deterministic value/vectors in $t =$ $k ,$ so the highest $T _ { \mathrm { D C } } ( t )$ case influenced by the forecasting error can be obtained as in (75).

$$
\begin{array}{c} \sup _ {T _ {\text { out,err }} (t) \in \mathcal {T}} T _ {\mathrm{DC}} (t) = \kappa^ {t - k} T _ {\mathrm{DC}} (k) + \boldsymbol {\kappa} ^ {\top} \boldsymbol {t} _ {\text { out,fore }} (t) \\ + \sup _ {T _ {\text { out,err }} (t) \in \mathcal {T}} \boldsymbol {\kappa} ^ {\top} \boldsymbol {t} _ {\text { out,err }} (t) \end{array}\tag{75}
$$

Thus, the maximum forecasting error can be obtained through solving the subproblem (76).

$$
T^{*}_{\text{out,err}}(t) = \operatorname *{arg  max}_{\substack{T_{\text{out,err}}(m)\in \mathcal{T},\\ \forall m = k + 1\sim t}}\kappa^{\top}\boldsymbol{t}_{\text{out,err}}(t), t = k + 1\sim k + N\tag{76}
$$

The nature of problem (76) depends on the type of uncertainty set T . If the polyhedron uncertainty set $\mathcal { T } _ { \mathfrak { p } }$ is used, (76) will be a linear programming problem. Instead, when the ellipsoid uncertainty set $\mathcal { T } _ { \mathrm { e } }$ is used, (76) is a second-order cone programming problem (SOCP). To solve a SOCP, the inner point method is usually adopted. Compared to the simplex method, the time complexity of the inner point method is $O ( n ^ { 3 } )$ , while the time complexity when solving linear programming is usually $O ( n ^ { 2 . 5 } )$ [29]. Consequently, when the number of decision variables n is determined, solving the SOCP takes longer time using the inner point method, than solving the linear programming using the simplex method. As a result, constructing the polyhedron uncertainty set $\mathcal { T } _ { \mathfrak { p } }$ contributes to the improvement of the solving effectiveness.

In summary, the problem (30) is transferred to the mixed integer linear programming problem (77), which can be solved by the solvers.

$$
\begin{array}{l} \min \quad \mathbb {C} \\ \text { s.t. } (1) \sim (2 5), t = k \sim k + N; \\ (6 5), (6 7), (6 9) \sim (7 1), (7 6), \\ t = k + 1 \sim k + N. \end{array}\tag{77}
$$

In practice, the solving process can be organized as the following two steps.

1) Find the worst case of the forecasting errors of the outside temperature, i.e., solve $\begin{array} { r l } { T _ { \mathrm { o u t , e r r } } ^ { * } ( t ) } & { { } = } \end{array}$ arg max $\pmb { \kappa } ^ { \top } \pmb { t } _ { \mathrm { o u t , e r r } } ( t )$ by solver directly. Tout,err (m)<sup>∈</sup>T , ∀m=k+1∼t

2) Solve the problem by solver:

$$
\begin{array}{l} \min \mathbb {C} \\ \text { s.t. } (1) \sim (2 5), t = k \sim k + N; \\ (6 5), (6 7), (6 9) \sim (7 1), \\ T _ {\text { out,err }} (t) = T _ {\text { out,err }} ^ {*} (t), t = k + 1 \sim k + N. \end{array}
$$

The solutions of the RO problem (30) based on the uncertainty set of the outside temperature constructed using the SGVL algorithm are less conservative. The reason lies in that the SGVL algorithm constructs uncertainty sets neglecting the extreme and low-probability scenarios of the outside temperature forecasting error. Thus, the uncertainty sets tend to be smaller and the worst scenarios found by solving the subproblems (76) tend to be better and less conservative. Therefore, the statistical feasibility-based RO contributes to reducing conservatism in the solutions.

## IV. NUMERICAL EXPERIMENT

In this section, numerical case studies are presented to test the effectiveness of the framework, the SGVL algorithm proposed in the foregoing and the impact of some critical parameters: $N , \varepsilon , \delta$ and the forecasting errors. Moreover, the influences of other uncertainties, the size of the $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ and different forecasting error datasets are also examined. Firstly, the parameters, data and platform used in the experiments are presented. Secondly, the indicators, including the total cost and the probability of violating constraint (24), are also defined. Finally, the numerical results are presented and analyzed. Based on these results, the effectiveness and robustness of the proposed framework are verified.

## A. Data Preparation and Indicators Design

In the framework, the outside temperature data is sourced from the Jena Climate Dataset and wind power output is from Elia Group. The whole dispatch period is 24 hours and the length of each time slot 1t is 15 minutes $( \mathrm { i } . \mathrm { e } . , T = 9 6 )$ . Other parameters of the $\mathrm { H } _ { 2 } .$ -DCMG are presented in Table I. The time-of-use price of the grid power is presented in Table II. The arrival and minimum departure curves of delay-tolerant workload are from [30], as shown in Fig. 5. The general loads in the study are estimated based on the Power Usage Effectiveness model [3]. The forecasted general loads are predicted to be 10% of the expected loads of server clusters in the DC, which is estimated based on the forecasted real-time workloads and the collected delay-tolerant workloads. All the simulations are conducted on a PC equipped with i7-12700H 2.30 GHz and 16 GB RAM, using MATLAB R2022a and GUROBI 10.0.1.

Since there are multiple random elements in the framework, the reduced Monte Carlo method is adopted with 50 independent experiments [31], [32]. In this section, the total cost, probability of violating constraint (24) and the probabilities of violating the chance constraint Pr $\{ T _ { \mathrm { D C } } ( t ) \le T _ { \mathrm { s e t } } \} \ge 1 - \varepsilon$ are used as indicators. The total cost <sup>C</sup> is used to test the economic performance of the framework, which is calculated

TABLE I  
THE PARAMETERS IN THE H -DCMG

<table><tr><td>Parameter</td><td>Value</td><td>Parameter</td><td>Value</td></tr><tr><td> $T_{\text{set}}$ </td><td>40 °C</td><td> $N_s$ </td><td>3</td></tr><tr><td> $\eta_{\text{el}}$ </td><td>0.74</td><td> $C_{\text{DC}}$ </td><td>1 kWh/°C</td></tr><tr><td> $\eta_{\text{fc}}$ </td><td>0.5</td><td> $R_{\text{DC}}$ </td><td>2.5 °C/kW</td></tr><tr><td> $\eta_{\text{comp}}$ </td><td>0.745</td><td> $f_{\text{max}}^{s_i}$ </td><td>9000 GHz</td></tr><tr><td> $H_{\text{HV}}$ </td><td>0.0795 kWh/mol</td><td> $P_{\text{idle}}^{s_i}$ </td><td>960 kW</td></tr><tr><td> $L_{\text{HV}}$ </td><td>0.0671 kWh/mol</td><td> $P_{\text{dyn}}^{s_i}$ </td><td>1740 kW</td></tr><tr><td> $P_{\text{el}}^{\text{max}}$ </td><td>20000 kW</td><td> $\mu$ </td><td>1</td></tr><tr><td> $n_{\text{store}}^{\text{min}}$ </td><td>35714.3 mol</td><td> $\theta$ </td><td>0.1</td></tr><tr><td> $P_1$ </td><td>1.5 MPa</td><td> $\rho_{\text{el}}$ </td><td>0.00305 $/kWh</td></tr><tr><td> $P_2$ </td><td>70 MPa</td><td> $\rho_{\text{fc}}$ </td><td>0.00305 $/kWh</td></tr><tr><td> $n_{\text{store}}^{\text{max}}$ </td><td>178571.4 mol</td><td> $\rho_{\text{RE}}$ </td><td>0.02 $/kWh</td></tr><tr><td> $\eta_{\text{heat}}$ </td><td>0.15</td><td> $\rho_{\text{AC}}$ </td><td>0.209 $/kWh</td></tr><tr><td> $\kappa$ </td><td>0.9394</td><td> $M_{\text{DC}}$ </td><td>500 t</td></tr><tr><td> $\eta_{\text{AC}}$ </td><td>0.1</td><td> $D$ </td><td>5</td></tr><tr><td> $P_{\text{grid}}^{\text{max}}$ </td><td>40000 kW</td><td></td><td></td></tr></table>

TABLE II

TIME-OF-USE POWER PRICE  
![](duan2025_robust_h2_dcmg_assets/images/e8df1b26a7cc51505f6de978489367f57aba67689ee1a50789e38a0ad324bfb8.jpg)  
Fig. 5. The arrival and minimum departure curves of delay-tolerant workload.

using (78).

$$
\begin{array}{l} \mathbb {C} = \sum_ {t = 1} ^ {T} [ \rho_ {\text { grid }} (t) \cdot P _ {\text { grid }} (t) + \rho_ {\text { el }} \cdot P _ {\text { el }} (t) + \rho_ {\text { fc }} \cdot P _ {\text { fc }} (t) \\ \quad + \rho_ {\text { RE }} \cdot P _ {\text { RE }} (t) + \rho_ {\text { AC }} \cdot P _ {\text { AC }} (t) ] \end{array}\tag{78}
$$

The violating probability is calculated using the frequency of violating constraint (24). The definition of violating probability is presented in (79).

$$
\text { Probability } = \sum_ {i = 1} ^ {5 0} \frac {\text { Times   of   violating(24)inithdispatch }}{5 0 T}\tag{79}
$$

The “Times of violating (24) in dispatch” in (79) refers to the violating times in actual operation. The DC temperature of the actual operation is the present DC temperature. In the problem to be solved in each time slot (30), the present DC temperature refers to $T _ { \mathrm { D C } } ( k )$ .

Except the probability of violating constraint (24), the probability of violating the chance constraint $\operatorname* { P r } \{ T _ { \mathrm { D C } } ( t ) \leq T _ { \mathrm { s e t } } \} \geq$ $1 - \varepsilon$ is also considered. This indicator is designed to test the satisfaction of the statistical feasibility-based constraint. In the 50 Monte Carlo experiments, the probability of violating the chance constraint is defined as in (80).

![](duan2025_robust_h2_dcmg_assets/images/90348bcb63345560df2ed2fe5eb24e6f238eb361b9915f99e92fc86287936477.jpg)  
Fig. 6. Total costs in EXP1.

![](duan2025_robust_h2_dcmg_assets/images/9bc3ca069a508a7d1a21e0d223e86510180b572f99d526dfc359e12845aa7c7b.jpg)  
Fig. 7. The average solving time.

$$
\text { Probability } = \frac {\text { Times   of   violating   chance   constraint }}{5 0}\tag{80}
$$

where “Times of violating chance constraint” means the times of violating $\operatorname* { P r } \{ T _ { \mathrm { D C } } ( t ) ~ \leq ~ T _ { \mathrm { s e t } } \} ~ \geq ~ 1 - \varepsilon$ in the 50 Monte Carlo experiments.

## B. EXP 1: Comparison Among Polyhedron, Ellipsoid and Uncertainty Sets

EXP1 is designed to show the advantages of the polyhedron uncertainty set constructed using the SGVL algorithm. In EXP1 the polyhedron and ellipsoid uncertainty sets of outside temperature are constructed through (51) and (41), respectively. The box uncertainty set is constructed based on the historical data, in the same way as the box uncertainty sets in Section III. A. In this experiment, $\varepsilon = \delta = 0 . 0 5 .$ $N = [ 1 , 2 , 3 , 4 , 5 , 6 , 7 , 8 ] ^ { \top }$ . Gaussian noise is added to the real-time and forecasted outside temperature.

The dispatch costs in EXP1 are given in Fig. 6. The total cost <sup>C</sup> with polyhedron and ellipsoid uncertainty sets is lower than the ones with box uncertainty sets. This result exhibits the fact that the introduction of statistical feasibility into the framework can reduce the conservatism of the dispatch results. Additionally, the results with polyhedron uncertainty sets (i.e., the red bars) are not much higher than the results with ellipsoid uncertainty sets (i.e., the yellow bars). This fact means that the polyhedron uncertainty set constructed using the proposed SGVL does not worsen the optimization result compared with the ellipsoid uncertainty set. Moreover, as N varies from 1 to 8, the total cost increases. This trend suggests that as the extension of the forecasting period, conservatism increases. The reason for this increment is that the forecasting errors in further periods are larger than the ones in closer periods. These larger errors lead to bigger uncertainty sets, and thus the conservatism increases.

The primary advantage of the polyhedron uncertainty set over the ellipsoid set is its reduced solving complexity. The average solving time with polyhedron and ellipsoid uncertainty set, and the percentage difference is given in Fig. 7. As shown in the folded lines, the solving time with the ellipsoid uncertainty set (the green folded line) is always higher than the yellow folded line, which represents the solving time with the polyhedron uncertainty sets. This fact suggests that the polyhedron uncertainty set constructed using the SGVL algorithm can reduce the complexity of the problem which is to be solved in each time slot. Additionally, with the increment of $N ,$ the solving time also increases. This is because the increment of N leads to a higher dimension of the optimization problem. The blue bars in Fig. 7 show the difference between solving time with different uncertainty sets in percentage. The adoption of polyhedron uncertainty sets can reduce 7%∼14% of the solving time.

In summary, the advantage of the proposed polyhedron uncertainty set $\mathcal { T } _ { \mathfrak { p } }$ is verified in EXP1. Compared with the generally used box uncertainty set, the polyhedron uncertainty set constructed based on statistical guarantee can lead to lower total costs. The traditional ellipsoid uncertainty sets constructed based on the statistical guarantee lead to the second-order cone constraint in the subproblem (76), which increases the complexity of the subproblem and decreases the solving efficiency. The proposed polyhedron uncertainty set can significantly reduce the solving complexity and increase the solving efficiency. This reduction is because the subproblem (76) with $\mathcal { T } _ { \mathfrak { p } }$ is a linear program problem, which is easy to solve.

## C. EXP2: The Sensitivity Analysis of the Parameters

EXP2 is designed to test the sensitivity of the datadriven statistical-feasibility-based $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ robust rolling dispatch framework to the parameters ε and δ. In EXP2, $\delta = [ 0 . 0 5 , 0 . 1 , 0 . 1 5 , 0 . 2 ] ^ { \top } , \varepsilon = [ 0 . 0 5 , 0 . 1 , 0 . 1 5 , 0 . 2 ] ^ { \top } , N =$ $[ 5 , 6 , 7 , 8 ] ^ { \top }$ . Only the polyhedron uncertainty set is adopted. Different forecasting error factors $f e$ of outside temperature are introduced. In this experiment, the influence of forecasting error factors on the real temperature is shown in (81).

$$
\text { real } T _ {\text { out }} (t) = \text { forecasted } T _ {\text { out }} (t) + f e \cdot T _ {\text { out,err }} (t)\tag{81}
$$

where $T _ { \mathrm { o u t , e r r } } ( t ) \sim N ( 0 , \delta _ { T } )$ , δ<sub>T</sub> is the standard deviation of the forecasted temperature based on the historical dataset.

The probability of violating constraint (24) is shown in Fig. 8. For each set of (ε, δ), the probability of violating (24) increases with the increment of ε. The probability also increases with the increment of δ, but this increment is much more implicit compared to the increment caused by the increment of ε. Additionally, the forecasting error factor also influences this probability. When $f e \mathrm { ~ = ~ } 0 . 5$ or $f e \ = \ 1$ , the violating probability is maintained around the pre-set probability ε. However, when $f e = 1 . 5 ,$ the violating probability is apparently higher than the pre-set probability. This is because the requirement for the dataset to be the same as the actual forecasting error distribution is not being met. When $f e \leq 1$ , the actual forecasting errors are lower than the ones in the dataset, which leads to larger uncertainty sets and decreases the violating probability. However, when $f e > 1$ the actual forecasting errors for the outside temperature are larger than the errors in the dataset. Therefore, the uncertainty set cannot cover enough area of the error’s distribution, and the violating probability increases by around 7%. Additionally, for different lengths of the forecasting period N , the probability differs little. This fact shows that the length of the forecasting period has little influence on the violating probability.

![](duan2025_robust_h2_dcmg_assets/images/46d77de9dc59b1ac0e989800bcdd3878da199540937a943ae0584f382c8f1a46.jpg)  
Fig. 8. The probabilities of violating the temperature bound in EXP2.

![](duan2025_robust_h2_dcmg_assets/images/4443940711d624e54c31dfbfd2bba975f471f935c940b4afdb1430050770fd9a.jpg)  
Fig. 9. The total costs in EXP2.

![](duan2025_robust_h2_dcmg_assets/images/2aaf58b5a1a4efdd6896e6d417dc40631a061cd67292a8b2b4355ef6b79aefb5.jpg)

Fig. 10. The probability of violating the chance constraint in EXP2.  
![](duan2025_robust_h2_dcmg_assets/images/2e7afc066911b585c367c0324a1b9c6a2b1c3a7e0bc0d58bd9b7ac9799f4c849.jpg)  
Fig. 11. The total cost in EXP3.

![](duan2025_robust_h2_dcmg_assets/images/d896bbc4ef39eaba28278d1f77bd464d1127787cc182a7b58b1d1c3d719b8c55.jpg)  
Fig. 12. The violating probability in EXP3.

![](duan2025_robust_h2_dcmg_assets/images/5186bde3386f9569e843aac54713f356f467036cac79f1c54dca3c3696604a89.jpg)  
Fig. 13. The violating chance constraint probability in EXP3.

The total costs with different parameters are shown in Fig. 9. Different from the violating probability, the influence of parameters on the total costs is much lower. The same as the results in EXP1, a longer forecasting period N leads to higher total costs. Additionally, the larger ε and δ lead to relatively lower costs. This fact comes from the difference in the size of uncertainty sets. Larger ε and δ lead to smaller uncertainty sets because more historical scenarios are expected to be ignored. Smaller uncertainty sets reduce the conservatism of RO, and thus the total costs are also reduced. However, this reduction is not as distinct as the influence of different N and the different types of uncertainty sets. Additionally, $f e$ also influences the costs. When $f e > 1$ , the total costs are higher than the costs with $f e \ \leq \ 1$ . This fact is caused by the DC temperature influenced by the uncertain outside temperature. When the forecasting errors are large, the DC temperature is expected to be higher. Thus, the power of the A/C system increases, and the total cost also increases.

The probabilities of violating the chance constraint are presented in Fig. 10. This probability of violating the chance constraint mainly varies with the variations of δ and $f e .$ With the increment of δ, the probabilities are increases. When $f e = 0 . 5 , 1$ , the probabilities of violating the chance constraint are maintained around the pre-set δ. This fact shows that the outer chance constraint in the statistical feasibility-based constraint (28) is satisfied. However, when $f e \ = \ 1 . 5$ , the probabilities break the bound of the δ. This fact shows that when the real forecasting errors are larger than the errors in the historical datasets, the statistical feasibility-based constraint cannot be satisfied.

In summary, the parameters ε and δ influence the dispatch results. Larger parameters lead to higher probabilities of violating constraint (24), probabilities of violating the chance constraint and relatively lower total costs. Choosing the right ε and δ can result in more stable and economical dispatch results. Additionally, the forecasting errors also influence the dispatch results. Higher errors will lead to higher violating probability of both the temperature bound and the chance constraint, and higher total costs. As a result, the stability of the forecaster is important. The forecasting errors should not be larger than the errors in the historical dataset.

## D. EXP3: The Influence of the Forecasting Errors of Wind Power, Real-Time Workloads and General Loads

EXP3 is designed to examine the influence of the forecasting error of wind power, real-time workloads and general loads. In this experiment, both ε and δ are set to 0.05, $N \ = \ [ 5 , 6 , 7 , 8 ] ^ { \top }$ . The forecasting error factor $f e$ is also introduced into the experiment. Its influences can be expressed as (82).

$$
\text { real   value } = \text { forecasted   value } + f e \cdot \text { error } \cdot \text { forecasted   value }\tag{82}
$$

where error refers to the forecasting error rates. Its definition is error = random × ratio. random is a random number, which follows $N ( 0 , 0 . 3 3 ^ { 2 } )$ . r atio refers to the maximum $\alpha _ { a } / W _ { a }$ $\beta _ { a } / P _ { a }$ and $\theta _ { a } / L _ { a }$ for all a in Section III-A.

TABLE III  
THE TOTAL COSTS AND VIOLATING PROBABILITY IN EXP4

<table><tr><td rowspan="2"></td><td rowspan="2">Size</td><td colspan="4">N</td></tr><tr><td>5</td><td>6</td><td>7</td><td>8</td></tr><tr><td rowspan="5">Total Cost ($)</td><td>1</td><td>5293.065</td><td>5301.293</td><td>5302.054</td><td>5308.012</td></tr><tr><td>2</td><td>11364.47</td><td>11387.55</td><td>11381.19</td><td>11412.23</td></tr><tr><td>3</td><td>17458.21</td><td>17464.65</td><td>17474.38</td><td>17491.51</td></tr><tr><td>4</td><td>23535.42</td><td>23542.45</td><td>23559.64</td><td>23548.94</td></tr><tr><td>5</td><td>29621.79</td><td>29614.56</td><td>29625.93</td><td>29682.32</td></tr><tr><td rowspan="5">Violating Probability</td><td>1</td><td>0.063542</td><td>0.051875</td><td>0.05375</td><td>0.052292</td></tr><tr><td>2</td><td>0.07452</td><td>0.065</td><td>0.050833</td><td>0.051673</td></tr><tr><td>3</td><td>0.07486</td><td>0.064792</td><td>0.051</td><td>0.051671</td></tr><tr><td>4</td><td>0.0775</td><td>0.065417</td><td>0.049833</td><td>0.051762</td></tr><tr><td>5</td><td>0.0724</td><td>0.065</td><td>0.050763</td><td>0.047292</td></tr><tr><td rowspan="5">Violating Chance Constraint Probability</td><td>1</td><td>0.04</td><td>0.06</td><td>0.04</td><td>0.06</td></tr><tr><td>2</td><td>0.04</td><td>0.08</td><td>0.06</td><td>0.06</td></tr><tr><td>3</td><td>0.08</td><td>0.08</td><td>0.1</td><td>0.06</td></tr><tr><td>4</td><td>0.06</td><td>0.08</td><td>0.06</td><td>0.08</td></tr><tr><td>5</td><td>0</td><td>0.02</td><td>0.06</td><td>0.06</td></tr></table>

The total costs in EXP3 with different forecasting error factors are presented in Fig. 11. The total costs with different forecasting error factors do not distinctly differ. This is because the forecasting error of the wind power, real-time workloads and general loads do not have the latency as the temperature. These uncertain can be addressed in t, and not reduce the influence in future time slots. Therefore, the total costs are not influenced distinctly.

The probability of violating constraint (24) is shown in Fig. 12. It can be seen that with different $f e ,$ the violating probability is not efficiently impacted by the forecasting error factor. All probabilities are maintained around 0.05, which shows that the forecasting errors of wind power, real-time workloads and general loads do not harm the stability of dispatching DC temperature.

The probabilities of violating the chance constraint are presented in Fig. 13. With different $f e ,$ the probabilities of violating the chance constraint do not differ distinctly. Similar to the probabilities in Fig. 12, the probabilities of violating the chance constraint are also maintained around 0.05. This fact also expresses that the forecasting errors of wind power, real-time workloads and general loads do not influence the stability of dispatching DC temperature distinctly.

In summary, the forecasting errors of other uncertainties, including wind power, real-time workloads and general loads, do not distinctly influence the total cost and the violating probability.

## E. EXP4: The Influence of Scalability of the $H _ { 2 } { - } D C M G$

EXP4 is designed to examine the scalability of the proposed framework to the $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ with different sizes. In this experiment, ε and δ are set to 0.05, and N is set to $[ 5 , 6 , 7 , 8 ] ^ { \top }$ . Only the polyhedron uncertainty set is adopted. In this experiment, the size of the HESS, PSS and DC in the $_ { \mathrm { H } _ { 2 } \mathrm { - D C M G } }$ are set as $1 \sim 5$ times of the original ones.

The total cost and the violating probability in EXP4 are presented in Table III. With the increment of size from 1 to 5, the total cost also increases from around \$5300 to over \$29600.

![](duan2025_robust_h2_dcmg_assets/images/6b126d67cfac8115ecfbaa555d3d8e03e07dcf5356292d132b5d90c2d9c63ed6.jpg)  
Fig. 14. The total cost in EXP5.

![](duan2025_robust_h2_dcmg_assets/images/fd8b3eee862c05da5f053b66121f7fcb2c1f00a8b6e0fb0f80818d03940e06a1.jpg)  
Fig. 15. The violating probability in EXP5.

![](duan2025_robust_h2_dcmg_assets/images/122401e664884e29d909d636b6438cc8939aaee2647c239df9415792d259a030.jpg)  
Fig. 16. The violating chance constraint probability in EXP5.

This is because the increment in the size of the $\mathrm { H } _ { 2 } .$ -DCMG leads to more power generation and consumption. As shown in (29), these generations and consumptions are costly, thus the total cost increases.

Differently, the violating probability does not vary distinctly with the increment in size. This is because the uncertainty of the outside temperature is not influenced by the size of the $_ { \mathrm { H } _ { 2 } \mathrm { - } \mathrm { D C M G } }$ . Then the dispatched DC temperature is mostly maintained below the pre-set temperature, and the normal operations of the DC are kept.

Similar to the probabilities of violating constraint (24), the probabilities of violating the chance constraint also do not vary distinctly with the variation of the sizes. The stability of the DC temperature is maintained and the statistical feasibility-based constraint is satisfied.

In conclusion, the scalability of the proposed framework is verified. The total cost increases with the increment of the size of the ${ \mathrm { H } } _ { 2 } { \mathrm { - D C M G } } ,$ while the probabilities of violating constraint (24) do not vary distinctly.

## F. EXP5: The Influence of Different Datasets

EXP5 is designed to test the performance of different datasets of the outside temperature forecasting errors. In this experiment, the Gaussian noise is added to the historical dataset 9. The standard deviation of the forecasting error in the real dispatch period is the same as the standard deviation of the corresponding dataset. The forecasting error factor is the multiple of the standard deviation of the dataset to the original dataset in the experiment.

The total cost in EXP5 is presented in Fig. 14. The influence of $f e$ on the total cost is slight. This is because most of the forecasting errors in the datasets are minimal. Although the standard deviation of the dataset is multiplied by $f e$ the minimal errors are not influenced effectively. Additionally, the larger forecasting errors, in other words, the extreme and low-probability scenarios, are ignored when constructing the uncertainty sets based on the SGVL algorithm. Therefore, the sizes of the uncertainty set only rise a little with the increment of $f e ,$ and the total costs also rise a little.

The violating probabilities in EXP5 are shown in Fig. 15. The violating probabilities are mostly maintained around $0 . 0 4 \sim 0 . 0 8$ , which shows the stability of the dispatched DC temperature. Similarly, the probabilities of violating the chance constraint, presented in Fig. 16, are mainly maintained around 0.05. This fact shows that the statistical feasibility-based constraint is satisfied. The stability of the dispatched DC temperature is not impacted distinctly by different forecasting error datasets.

In summary, the performance of the proposed framework shows the applicability of the framework with different datasets. The robustness of the framework to different forecasters is also verified indirectly.

## G. The Conclusion of the Experiments

In this section, V different experiments are processed, and the results are analyzed. In EXP1, different kinds of uncertainty sets are adopted to verify the advantages of statistical feasibility in the field of total cost. The polyhedron uncertainty set constructed using the proposed SGVL algorithm has also been verified to effectively reduce the complexity of the problem and reduce the solving time. EXP2 is designed to test the sensitivity of the parameters ε and δ. In this experiment, the stability of the dispatched DC temperature obtained based on the polyhedron uncertainty set is verified. In EXP3, the forecasting errors of wind power, real-time workloads and general loads are verified to have no significant impact on the dispatch results. The scalability of the proposed framework to the H -DCMG of different sizes is examined in EXP4. Finally, in EXP5, the different datasets of the forecasting errors are used to examine their influence. In this experiment, the framework is tested on different datasets, and the applicability to different forecasters is indirectly verified. In summary, the framework is verified to apply to the dispatch of different $\mathrm { H } _ { 2 ^ { - } }$ DCMGs with different forecasters. The total cost is lower, the stability of the dispatched DC temperature is maintained and the statistical feasibility-based constraint is mainly satisfied.

## V. CONCLUSION

This paper has proposed a data-driven robust rolling dispatch framework for the H<sub>2</sub>-DCMG. The mathematical models of the HESS, PSS and DC in the H -DCMG are presented. There are multiple uncertainties, including outside temperature, wind power, real-time workloads and general loads. In the framework, RO is introduced to deal with the multiple uncertainties and the statistical feasibility is also introduced to reduce the conservatism of RO.

A key innovation in this study is the development of the SGVL algorithm. This algorithm constructs a polyhedron uncertainty set for the outside temperature in RO based on statistical guarantee. This polyhedron uncertainty set effectively reduces the computational complexity of the solving process, because the polyhedron uncertainty set is transferred into linear constraints when solving. Moreover, the RO results are to be more economical because the statistical feasibility is satisfied.

Multiple cases are designed based on real-world data to test the advantages of the proposed framework with statistical feasibility and the polyhedron uncertainty set constructed using the SGVL algorithm. The statistical feasibility has been verified to reduce the conservatism in RO. The cases verify the effectiveness of the polyhedron uncertainty set in reducing the computational complexity when solving. The parameters ε and δ are shown to affect the violating probability of the temperature bound and the chance constraint, but the impact on the total cost is little. Additionally, the framework with the polyhedron uncertainty set is verified to be applicable for different forecasters and $\mathrm { H } _ { 2 } { \mathrm { - D C M G s } }$ with different sizes.

There are some limitations in this study. For example, there are too many constraints in the polyhedron uncertainty sets. In the N -dimensional space, there are $2 ^ { N }$ hyperplanes in the uncertainty sets. When the dimension is high, the extra linear constraints may reduce the solving efficiency. Consequently, the method to reduce the amount of constraints in the uncertainty sets needs to be studied. Secondly, distributional RO has attracted much attention in recent years. The combination of statistical feasibility-based RO and distributional RO is valuable for searching. Finally, the precise control of the devices in the $_ { \mathrm { H } _ { 2 } \mathrm { - } \mathrm { D C M G } }$ is not studied. The model predictive control can be combined with the statistical feasibility-based RO to propose better dispatch and control strategies for the $\mathrm { H } _ { 2 ^ { - } }$ DCMG.

## APPENDIX A APPENDIX A

## THE PROOF OF THE WAITING TIME OF THE REAL-TIME WORKLOAD

Assuming that the arrival and serving of the real-time workload of the server cluster $s _ { i }$ in t are consistent with Poisson distribution [33]. To simplify the presentation, two new symbols are used in this proof as follows.

$$
\mu_ {\mathrm{RT}} := f _ {\mathrm{RT}} ^ {s _ {i}} (t) \mu \quad \lambda_ {\mathrm{RT}} := W _ {\mathrm{RT}} ^ {s _ {i}} (t)\tag{83}
$$

The server cluster can be considered as a M/M/1 system. The transition probability matrix of the Birth-Death Process is as shown in (84).

$$
\mathbf {Q} = \left[ \begin{array}{c c c c} - \lambda_ {\mathrm{RT}} & \lambda_ {\mathrm{RT}} & 0 & \dots \\ \mu_ {\mathrm{RT}} & - (\mu_ {\mathrm{RT}} + \lambda_ {\mathrm{RT}}) & \lambda_ {\mathrm{RT}} & \dots \\ 0 & \mu_ {\mathrm{RT}} & - (\mu_ {\mathrm{RT}} + \lambda_ {\mathrm{RT}}) & \ddots \\ \vdots & \vdots & \ddots & \ddots \end{array} \right]\tag{84}
$$

Assuming that the total number of the real-time workloads in the system is $n = 0 , 1 , 2 , \ldots \mathrm { P } _ { n }$ is the probability that n workloads are in the system. When the system is stable, the following equation is satisfied.

$$
[ \mathrm{P} _ {0} \mathrm{P} _ {1} \mathrm{P} _ {2} \dots ] \mathbf {Q} = \mathbf {0}\tag{85}
$$

The solutions of (85):

$$
\mathrm{P} _ {n} = \left(\frac {\lambda_ {\mathrm{RT}}}{\mu_ {\mathrm{RT}}}\right) ^ {n} \mathrm{P} _ {0}\tag{86}
$$

The normalization condition needs to be satisfied:

$$
\sum_ {n = 0} ^ {\infty} \mathrm{P} _ {n} = 1 \Rightarrow \mathrm{P} _ {0} = 1 - \frac {\lambda_ {\mathrm{RT}}}{\mu_ {\mathrm{RT}}}\tag{87}
$$

The expected number of the workloads $L _ { s }$ is

$$
L _ {q} = \sum_ {n = 0} ^ {\infty} n \mathrm{P} _ {n} = 1 - \frac {\lambda_ {\mathrm{RT}}}{\mu_ {\mathrm{RT}}} \sum_ {n = 0} ^ {\infty} n \left(\frac {\lambda_ {\mathrm{RT}}}{\mu_ {\mathrm{RT}}}\right) ^ {n} = \frac {\lambda_ {\mathrm{RT}}}{\mu_ {\mathrm{RT}} - \lambda_ {\mathrm{RT}}}\tag{88}
$$

Finally, the expected waiting time for the real-time workloads is

$$
w _ {s} = \frac {L _ {q}}{\lambda_ {\mathrm{RT}}} = \frac {1}{\mu_ {\mathrm{RT}} - \lambda_ {\mathrm{RT}}}\tag{89}
$$

## APPENDIX B

DEFINITIONS OF ARRIVAL, DEPARTURE AND MINIMUM DEPARTURE CURVES OF DELAY-TOLERANT WORKLOADS

According to [26], the definitions of arrival, departure and minimum departure curves of delay-tolerant workloads are presented below.

Definition 2: - Arrival Curve: The arrival curve A(t) is the cumulative quantity of delay-tolerable workload arrived before t, as shown in (90).

$$
A (t) = \sum_ {k = 1} ^ {t} W _ {\mathrm{DT,A}} (k)\tag{90}
$$

where $W _ { \mathrm { D T , A } } ( k )$ is the arrival rate of delay-tolerant workload in k.

Definition 3: - Departure Curve: The departure curve D(t) is the cumulative quantity of delay-tolerable workload that is completed before t, as shown in (91).

$$
D (t) = \sum_ {k = 1} ^ {t} W _ {\mathrm{DT}} (k)\tag{91}
$$

where $W _ { \mathrm { D T } } ( k )$ is the serve rate of delay-tolerant workload in k.

Definition 4: - Minimum Departure Curve: Usually, the delay-tolerant workload should be completed before its deadline, such information is given when the delay-tolerant workload arrives. The minimum departure curve of delaytolerant $D _ { \mathrm { m i n } } ( t )$ is the cumulative workload with a deadline before t. The minimum departure curve can be described as (92).

$$
D _ {\mathrm{min}} (t) = \sum_ {k = 1} ^ {t} W _ {\mathrm{DT,D}} (k)\tag{92}
$$

where $W _ { \mathrm { D T , D } } ( k )$ is the arrival rate of delay-tolerant workload with deadline in k.

APPENDIX C TWO EXAMPLES TO SUPPORT (60)

To see (60), for $l = n$ , we can obtain

$$
\begin{array}{c} \operatorname * {P r} _ {F _ {X}} \{X _ {(n)} \leq s _ {1 - \varepsilon} \} = \prod_ {i = 1} ^ {n} \operatorname * {P r} _ {F _ {X}} \{X _ {(i)} \leq s _ {1 - \varepsilon} \} \\ = F _ {X} (s _ {1 - \varepsilon}) ^ {n} \end{array}\tag{93}
$$

Next, for $l = n - 1$ , the following equation can be derived.

$$
\begin{array}{l} \operatorname * {P r} _ {F _ {X}} \{X _ {(n - 1)} \leq s _ {1 - \varepsilon} \} \\ = \prod_ {i = 1} ^ {n} \operatorname * {P r} _ {F _ {X}} \{X _ {(i)} \leq s _ {1 - \varepsilon} \} \\ + \sum_ {i = 1} ^ {n} \operatorname * {P r} _ {F _ {X}} \{\forall j \neq i X _ {(j)} \leq s _ {1 - \varepsilon}, X _ {(i)} > s _ {1 - \varepsilon} \} \\ = \binom {n} {n} F _ {X} (s _ {1 - \varepsilon}) ^ {n} \\ + \binom {n} {n - 1} F _ {X} (s _ {1 - \varepsilon}) ^ {n - 1} [ 1 - F _ {X} (s _ {1 - \varepsilon}) ] \end{array}\tag{94}
$$

Similarly, we can obtain the first equality of (60) for general l.

## REFERENCES

[1] J. Liu, Z. Xu, J. Wu, K. Liu, X. Sun, and X. Guan, “Optimal planning of Internet data centers decarbonized by hydrogen-water-based energy systems,” IEEE Trans. Autom. Sci. Eng., vol. 20, no. 3, pp. 1577–1590, Jul. 2023.

[2] A. D. Carnerero, D. R. Ramirez, T. Alamo, and D. Limon, “Probabilistically certified management of data centers using predictive control,” IEEE Trans. Autom. Sci. Eng., vol. 19, no. 4, pp. 2849–2861, Oct. 2022.

[3] X. Long et al., “Collaborative response of data center coupled with hydrogen storage system for renewable energy absorption,” IEEE Trans. Sustain. Energy, vol. 15, no. 2, pp. 986–1000, Apr. 2024.

[4] Y. Zhang, K. Shan, X. Li, H. Li, and S. Wang, “Research and technologies for next-generation high-temperature data centers—Stateof-the-arts and future perspectives,” Renew. Sustain. Energy Rev., vol. 171, Jan. 2023, Art. no. 112991.

[5] M. B. Abdelghany, A. Al-Durra, H. H. Zeineldin, and F. Gao, “A coordinated multitimescale model predictive control for output power smoothing in hybrid microgrid incorporating hydrogen energy storage,” IEEE Trans. Ind. Informat., vol. 20, no. 9, pp. 10987–11001, Sep. 2024.

[6] Y. Li, C. Yu, M. Shahidehpour, T. Yang, Z. Zeng, and T. Chai, “Deep reinforcement learning for smart grid operations: Algorithms, applications, and prospects,” Proc. IEEE, vol. 111, no. 9, pp. 1055–1096, Sep. 2023.

[7] C. Zhang, Y. Rezgui, Z. Luo, B. Jiang, and T. Zhao, “Simultaneous community energy supply-demand optimization by microgrid operation scheduling optimization and occupant-oriented flexible energy-use regulation,” Appl. Energy, vol. 373, Nov. 2024, Art. no. 123922.

[8] Y. Li et al., “Artificial intelligence-based methods for renewable power system operation,” Nature Rev. Electr. Eng., vol. 1, no. 3, pp. 163–179, Feb. 2024.

[9] A. M. Moustafa, M. B. Abdelghany, A.-S.-A. Younis, M. Moness, A. Al-Durra, and J. M. Guerrero, “Software-defined control of an emulated hydrogen energy storage for energy Internet ecosystems,” Int. J. Hydrogen Energy, vol. 50, pp. 893–909, Jan. 2024.

[10] M. B. Abdelghany, A. Al-Durra, H. Zeineldin, and J. Hu, “Integration of cascaded coordinated rolling horizon control for output power smoothing in islanded wind–solar microgrid with multiple hydrogen storage tanks,” Energy, vol. 291, Mar. 2024, Art. no. 130442.

[11] K. Wang et al., “A hierarchical dispatch strategy of hybrid energy storage system in Internet data center with model predictive control,” Appl. Energy, vol. 331, Feb. 2023, Art. no. 120414.

[12] Y.-P. Xu, R.-H. Liu, L.-Y. Tang, H. Wu, and C. She, “Risk-averse multiobjective optimization of multi-energy microgrids integrated with powerto-hydrogen technology, electric vehicles and data center under a hybrid robust-stochastic technique,” Sustain. Cities Soc., vol. 79, Apr. 2022, Art. no. 103699.

[13] H. Moazamigoodarzi, R. Gupta, S. Pal, P. J. Tsai, S. Ghosh, and I. K. Puri, “Modeling temperature distribution and power consumption in IT server enclosures with row-based cooling architectures,” Appl. Energy, vol. 261, Mar. 2020, Art. no. 114355.

[14] W.-H. Chen and F. You, “Semiclosed greenhouse climate control under uncertainty via machine learning and data-driven robust model predictive control,” IEEE Trans. Control Syst. Technol., vol. 30, no. 3, pp. 1186–1197, May 2022.

[15] X. Gong, E. Castillo-Guerra, J. L. Cardenas-Barrera, B. Cao, S. A. Saleh, and L. Chang, “Robust hierarchical control mechanism for aggregated thermostatically controlled loads,” IEEE Trans. Smart Grid, vol. 12, no. 1, pp. 453–467, Jan. 2021.

[16] M. Abdurohman, A. G. Putrada, and M. M. Deris, “A robust Internet of Things-based aquarium control system using decision tree regression algorithm,” IEEE Access, vol. 10, pp. 56937–56951, 2022.

[17] L. J. Hong, Z. Huang, and H. Lam, “Learning-based robust optimization: Procedures and statistical guarantees,” Manage. Sci., vol. 67, no. 6, pp. 3447–3467, Jun. 2021.

[18] T. Wendy, B. Kevin, and A. Victor, “The unexpected impact of raising data center temperatures,” Schneider Electric, Rueil-Malmaison, France, White Paper 221 Revision 0, 2016. [Online]. Available: https://www.se. com/ng/en/download/document/SPD\_VAVR-9SZM5D\_EN/

[19] W. Jiang, C. Lu, and C. Wu, “Robust scheduling of thermostatically controlled loads with statistically feasible guarantees,” IEEE Trans. Smart Grid, vol. 14, no. 5, pp. 3561–3572, Aug. 2023.

[20] K. Yang, Y. Shi, W. Yu, and Z. Ding, “Energy-efficient processing and robust wireless cooperative transmission for edge inference,” IEEE Internet Things J., vol. 7, no. 10, pp. 9456–9470, Oct. 2020.

[21] J. Duchi. (2018). Optimization With Uncertain Data. [Online]. Available: http://web.stanford.edu/class/ee364b/lectures/robust\_notes.pdf

[22] L. Ye, Y. Jin, K. Wang, W. Chen, F. Wang, and B. Dai, “A multiarea intra-day dispatch strategy for power systems under high share of renewable energy with power support capacity assessment,” Appl. Energy, vol. 351, Dec. 2023, Art. no. 121866.

[23] J. O. Jensen, A. P. Vestbø, Q. Li, and N. J. Bjerrum, “The energy efficiency of onboard hydrogen storage,” J. Alloys Compounds, vols. 446–447, pp. 723–728, Oct. 2007.

[24] T. D. Hutty, S. Dong, and S. Brown, “Suitability of energy storage with reversible solid oxide cells for microgrid applications,” Energy Convers. Manage., vol. 226, Dec. 2020, Art. no. 113499.

[25] J. Li, Z. Bao, and Z. Li, “Modeling demand response capability by Internet data centers processing batch computing jobs,” IEEE Trans. Smart Grid, vol. 6, no. 2, pp. 737–747, Mar. 2015.

[26] Y. Li, J. Huang, Y. Liu, H. Wang, Y. Wang, and X. Ai, “A multicriteria optimal operation framework for renewable energy integrated data center microgrid with waste heat recovery,” in Proc. IEEE/IAS 57th Ind. Commercial Power Syst. Tech. Conf. (I&CPS), Apr. 2021, pp. 1–11.

[27] E. K. Lee, I. Kulkarni, D. Pompili, and M. Parashar, “Proactive thermal management in green datacenters,” J. Supercomput., vol. 60, no. 2, pp. 165–195, May 2012.

[28] D. Bertsimas, D. B. Brown, and C. Caramanis, “Theory and applications of robust optimization,” SIAM Rev., vol. 53, no. 3, pp. 464–501, 2011.

[29] F. Alizadeh and D. Goldfarb, “Second-order cone programming,” Math. Program., vol. 95, no. 1, pp. 3–51, Jan. 2003.

[30] Y. Lian, Y. Li, Y. Zhao, C. Yu, T. Zhao, and L. Wu, “Robust multiobjective optimization for islanded data center microgrid operations,” Appl. Energy, vol. 330, Jan. 2023, Art. no. 120344.

[31] J. Dupacová, N. Gröwe-Kuska, and W. Römisch, “Scenario reduction inˇ stochastic programming,” Math. Program., vol. 95, no. 3, pp. 493–511, Mar. 2003.

[32] F. Cordiano and B. De Schutter, “Scenario reduction with guarantees for stochastic optimal control of linear systems,” in Proc. Eur. Control Conf. (ECC), Jun. 2024, pp. 3502–3508.

[33] D. Gross, J. F. Shortle, J. M. Thompson, and C. M. Harris, Simple Markovian Queueing Models. Hoboken, NJ, USA: Wiley, 2008, ch. 2, pp. 49–115, doi: 10.1002/9781118625651.ch2.