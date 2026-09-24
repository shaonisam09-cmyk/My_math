# CarbonScaler: Leveraging Cloud Workload Elasticity for Optimizing Carbon-Eficiency

WALID A. HANAFY, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup> QIANLIN LIANG, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup> NOMAN BASHIR, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup> DAVID IRWIN, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup> PRASHANT SHENOY, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup>

Cloud platforms are increasing their emphasis on sustainability and reducing their operational carbon footprint. A common approach for reducing carbon emissions is to exploit the temporal flexibility inherent to many cloud workloads by executing them in periods with the greenest energy and suspending them at other times. Since such suspend-resume approaches can incur long delays in job completion times, we present a new approach that exploits the elasticity of batch workloads in the cloud to optimize their carbon emissions. Our approach is based on the notion of “carbon scaling,” similar to cloud autoscaling, where a job dynamically varies its server allocation based on fluctuations in the carbon cost of the grid’s energy. We develop a greedy algorithm for minimizing a job’s carbon emissions via carbon scaling that is based on the well-known problem of marginal resource allocation. We implement a <sub>CarbonScaler</sub> prototype in Kubernetes using its autoscaling capabilities and an analytic tool to guide the carbon-eficient deployment of batch applications in the cloud. We then evaluate CarbonScaler using real-world machine learning training and MPI jobs on a commercial cloud platform and show that it can yield i) 51% carbon savings over carbon-agnostic execution; ii) 37% over a state-of-the-art suspend-resume policy; and iii) 8% over the best static scaling policy.

<sup>CCS</sup> <sup>Concepts: •</sup> Computer systems organization → Cloud computing<sup>; •</sup> Hardware → Renewable energy<sup>; •</sup> Social and professional topics → Sustainability<sup>.</sup>

Additional Key Words and Phrases: Carbon eficiency; Sustainable computing; Auto scaling

## ACM Reference Format:

Walid A. Hanafy, Qianlin Liang, Noman Bashir, David Irwin, and Prashant Shenoy. 2023. CarbonScaler: Leveraging Cloud Workload Elasticity for Optimizing Carbon-Eficiency. <sub>Proc.</sub> <sub>ACM</sub> <sub>Meas.</sub> <sub>Anal.</sub> <sub>Comput.</sub> <sub>Syst.</sub> 7, 3, Article 57 (December 2023), 28 pages. https://doi.org/10.1145/3626788

## 1 INTRODUCTION

Data centers worldwide consume over 200TWh of energy each year—comprising roughly 1% of global electricity usage [46]—and are poised to grow to 3-13% of global electricity demand by 2030 [4, 37]. The growth of hyper-scale cloud providers is fueling this rapid increase in energy use, resulting in a significant environmental impact by increasing carbon and greenhouse gas (GHG) emissions [30, 47]. For the past two decades, cloud providers have relentlessly focused on improving their data centers’ energy-eficiency to reduce their operational costs—by driving down their power usage efectiveness (PUE) close to the optimal value of 1. As a result, optimizations, such as server consolidation, open-air cooling, and power infrastructure improvements, have yielded significant energy-eficiency gains. However, energy-eficiency improvements alone are insuficient to satisfy cloud data centers’ aggressive sustainability goals, since even energy-eficient data centers may generate significant carbon emissions from their energy use. This has led to a new emphasis on carbon-eficient operations that directly target reducing data centers’ overall carbon emissions [14].

Carbon eficiency can be achieved through supply-side or demand-side methods. Supply-side methods include power purchase agreements (PPAs) from renewable generation sources, such as solar, wind, and hydro, which <sub>indirectly</sub> ofset a cloud data center’s carbon emissions. Such optimizations yield net-zero operation [28, 48, 51] over a long period, such as a year, but ofsets by themselves do not eliminate the instantaneous direct emissions at all times [10]. Consequently, supply-side optimizations must be combined with demand-side methods to reduce a cloud data center’s instantaneous direct carbon emissions. Demand-side optimizations exploit the fact that the carbon intensity of grid-supplied electricity varies both temporally and geographically. A common demand-side optimization is <sub>time</sub> <sub>shifting</sub> delay-tolerant workloads to periods with the “greenest” electricity supply. Although not all cloud workloads are delay tolerant, many types of batch workloads exhibit significant temporal, performance, and even geographic flexibility.

One approach for leveraging the temporal flexibility above is to use <sub>suspend-resume</sub> mechanisms [2, 19, 59, 73], where a scheduler suspends a job when electricity’s carbon intensity rises (e.g., above some threshold) and resumes it when it drops (e.g., below the threshold). For example, Google recently adopted carbon-aware time-shifting in its Carbon-Intelligent Computing System [59]. While suspend-resume temporal shifting policies can reduce the carbon emissions of delay-tolerant workloads [73], they sufer from two drawbacks. First, the carbon intensity of grid-supplied elec tricity changes slowly, and there may be long periods (e.g., many hours) of high carbon periods where jobs remain suspended and make no progress. Such suspensions cause substantial delays in completion time, with 7-10<sub>×</sub> increases in completion times in some cases [65]. Second, when batch jobs have limited temporal flexibility and thus cannot be significantly shifted, the efectiveness of these methods is significantly reduced.

To overcome these drawbacks, we present <sub>CarbonScaler</sub>, a new approach that exploits the <sub>resource</sub> <sub>elasticity</sub> of cloud workloads to dynamically vary the amount of resources allocated to applications in response to fluctuations in the carbon cost of their energy supply. Our “carbon scaling” approach is analogous to cloud autoscaling, where the number of servers allocated to a cloud application varies dynamically over time [6]. However, while cloud autoscalers generally respond to variations in applications’ workload demand, often for request-based services, our “carbon scaling” approach responds to the carbon dynamics of electricity. In essence, carbon scaling scales up the servers allocated to an application when the carbon cost is low and gracefully scales them down when the cost increases. In contrast to the static allocation of suspend-resume approaches, carbon scaling enables faster progress during low carbon periods, which can potentially eliminate delays in job completion times while also reducing carbon emissions.

Designing cloud carbon scaler requires addressing two key design challenges: <sub>how</sub> <sub>much</sub> to scale each application up or down and <sub>when</sub>. Since diferent applications exhibit diferent scaling characteristics with respect to the number of allocated servers, a carbon scaler must take this scaling behavior into account when determining how much to scale up each application during low carbon periods. For example, an embarrassingly parallel job can opportunistically scale up significantly without increasing its overhead (thus increasing its carbon eficiency), while applications with scaling bottlenecks should scale up more judiciously. To maximize carbon savings, <sub>CarbonScaler</sub> relies on its knowledge of the energy’s future carbon intensity, application scalability profile, job length, and other execution constraints to compute a schedule to decide when to perform such scaling operations. However, carbon intensity forecasts, profile estimates, and the expected length are error-prone, requiring carbon scaling decisions to be robust to such errors.

![](images/b3f9f90e45f1167dcd34eec159d458a05a86a6b285a609d80809b6466cf8786a.jpg)  
Fig. 1. Grid’s carbon intensity shown over a 10 days period varies spatially and temporally.

![](images/bef5b8363c8bfb44f9f793e81bda62f9d65b59cc44f7e042d6ac7daf9dc7db3a.jpg)  
Fig. 2. Scaling characteristics of common MPI jobs and machine learning training frameworks.

In designing, implementing, and evaluating <sub>CarbonScaler</sub>, we make the following contributions.

<sub>•</sub> We introduce the notion of carbon scaling for cloud applications, which maps to the wellknown problem of marginal resource allocation for which greedy optimal solutions exist [22]. <sub>CarbonScaler</sub> builds on these ideas to develop a greedy autoscaling algorithm that minimizes individual application’s emissions by scaling the resources up and down in response to carbon cost variations. Further, CarbonScaler can substantially reduce or even eliminate the completion time delays seen in suspend-resume approaches.

<sub>•</sub> We implement a full prototype of <sub>CarbonScaler</sub> in Kubernetes and use it to leverage a cloud application’s elasticity and temporal flexibility to reduce its carbon footprint. We also implement our algorithm in <sub>CarbonScaler</sub>’s CarbonAdvisor tool, which enables analysis and simulated execution of cloud applications to evaluate their carbon savings before being deployed in the cloud. CarbonAdvisor enables system designers to understand better how to minimize their carbon cost based on job characteristics, geographic region, and diferent run-time parameters.

<sub>•</sub> We evaluate <sub>CarbonScaler</sub> against multiple baselines using numerous real-world batch applications, including machine learning training and MPI-based scientific jobs. Our results show that <sub>CarbonScaler</sub> can yield up to i) 51% carbon savings over a carbon-agnostic execution, ii) 37% over the state-of-the-art suspend-resume policy, and iii) 8% over the best static scaling policy.

## 2 BACKGROUND

This section provides background on sustainable data centers and carbon-aware scheduling.

## 2.1 Sustainable Data Centers

In addition to their long-standing emphasis on improving energy eficiency by reducing their PUE, cloud data centers have recently begun to focus on reducing their carbon footprint [2, 59]. This can be achieved by reducing operational carbon emissions measured in gCO2eq<sub>/</sub>kWh, a.k.a. Scope 2 emissions [34], resulting from electricity use, as well as by reducing embodied carbon—Scope 3 emissions—that arise during the manufacturing of data center hardware (e.g., servers). Our work focuses on reducing Scope 2 emissions. Cloud platforms have little direct (Scope 1) emissions, and optimizing embodied carbon of computing workloads is beyond the scope of this paper.

## 2.2 Carbon Intensity of Electricity

To reduce Scope 2 emissions, cloud data centers must track the carbon cost of their electricity supply and modulate their electricity consumption over time. The carbon cost of electricity depends on the source of generation. For example, a unit of energy generated by a coal plant will have a high carbon cost (i.e., emissions in terms of $\mathrm { g C O 2 e q / k W h ) }$ , while energy generated from a hydroelectric plant will have no emissions. The electricity the grid supplies is produced by a mix of generation sources, and the resulting carbon cost is a weighted average of the corresponding sources. Importantly, the generation mix varies from one region to another—based on the local power plants in each region—and also varies over time since the generation mix changes based on demand, the relative cost of generation, and intermittent generation from renewable sources.

Figure 1 depicts how the carbon cost difers by country/region and how it exhibits diurnal variations daily. In this case, Ontario tends to have a low but variable carbon cost because its energy mix consists of a large fraction of carbon-free nuclear and hydroelectric energy combined with some coal plants, which results in non-zero carbon intensity, and solar, which causes the diurnal fluctuations. California is similar but has a higher fraction of solar, which results in larger fluctuations, but also a higher fraction of coal plants, which elevates the average carbon intensity. The Netherlands also shows diurnal variation but with a higher average as it relies more on fossil based electricity generation. By contrast, the carbon intensity of electricity in Iceland is nearly zero and flat due to its unique abundance of carbon-free geothermal energy.

## 2.3 Carbon-aware Cloud Scheduling

Many cloud workloads have both temporal flexibility and resource elasticity, which enables exploiting the temporal and spatial variations in energy’s carbon intensity, as demonstrated in recent work [19, 29, 59, 68, 73]. To facilitate such eforts, commercial services, such as electricityMap [45] and WattTime [71], have emerged that aggregate data from grids in diferent parts of the world and expose grid energy’s current and forecasted carbon intensity to cloud providers and users in real-time. Researchers, in turn, are exploiting this data to design carbon-aware schedulers that dynamically shift workloads across time and space to reduce emissions.

As mentioned above, temporal shifting involves moving delay-tolerant batch workloads to periods of low carbon intensity. In Figure 1, for instance, rather than running a batch job continuously in a <sub>carbon-agnostic</sub> manner, <sub>suspend-resume</sub> approaches execute the job in the “valleys”, where the carbon cost is low, and suspend the job during peak periods. This technique has been explored in recent work [19, 59, 68, 73]. Threshold-based suspend-resume scheduling policies suspend jobs whenever the carbon cost rises above a certain threshold, while deadline-based methods choose the ?? lowest carbon cost periods between the arrival time and the deadline to execute the job. Importantly, a key drawback of <sub>suspend-resume</sub> methods, whether threshold-based or deadlinebased, is that the carbon savings depend on the amount of time the user is willing to wait for their job to complete—a higher delay tolerance yields higher savings, but also a longer completion times.

Geographic or spatial shifting, in contrast, migrates jobs or workloads to regions with the greenest electricity grid [19, 52, 76, 77]. However, batch jobs often cannot exploit geographic shifting due to data privacy regulations, such as GDPR, that impose regional restrictions. Even when possible, spatially shifting jobs can incur high migration costs if it requires moving substantial state or data associated with the job. Since <sub>CarbonScaler</sub> focuses on batch jobs, spatial shifting is outside the scope of this paper. We discuss related work in spatial shifting in Section 7.

![](images/126329006982626e3fb5a863dd0fcab670ecc88d77aaa9aed454541b7a9f80e8.jpg)  
(a) Efect of Region

![](images/093cd29b8756d6875b5faf9cb9d029fbaca053a141c270d43c4af7eb615c1413.jpg)  
(b) Efect of Start Time

![](images/19445ce6fc8946c99d0acf41e24af90687dfa4b4fb0807305fc3e7efb3d0b187.jpg)  
(c) Efect of Runtime  
Fig. 3. Best static scale factor varies across (a) geographical regions, (b) job start times, and (c) job execution.

## 2.4 Carbon-Aware Autoscaling

Cloud workloads fall into two broad classes: interactive and batch. Since interactive workloads are latency-sensitive, they are not amenable to temporal shifting optimizations, and scaling is only beneficial in response to demand variations. Hence, our work focuses on distributed batch workloads, such as machine learning jobs, data analytics, and scientific computing simulations, which run on multiple machines. Given its benefits, elastic execution mechanisms are now built into many machine learning frameworks, such as Pytorch [55], data processing frameworks, such as Spark [5], as well as scheduling frameworks [25, 40, 61].

Although autoscaling can be applied from a cluster or cloud service provider perspective, our work focuses on the cloud <sub>application’s</sub> perspective of carbon scaling, similar to cloud autoscaling. A typical autoscaler adjusts the number of servers based on the application demand, where higher demand can be measured in latency or average utilization of provisioned resources [6, 26]. However, <sub>CarbonScaler</sub> adjusts the number of servers based on the carbon intensity of electricity. In both cases, the cloud application operates under an abstract view of the underlying servers and can allocate as many as needed. We discuss carbon scaling from a cloud providers’ perspective in Section 6.

Elastic scaling capabilities have enabled designing policies that scale resources based on electricity’s carbon intensity [65]. For example, a policy might scale up an application’s resources when carbon is low and either halt or scale down when carbon is high. However, unlike traditional autoscalers, a distributed batch application often has communication bottlenecks that vary widely across applications and govern the scaling benefits. Figure 2 depicts the scaling behavior of four deep learning training jobs, which use Horovod and PyTorch for elastic scaling and two MPI tasks that perform scientific computations. As shown, ResNet18 training and the larger ?? -body MPI computation show a linear increase in throughput as the number of servers increases, indicating linear scaling behavior. In contrast, the smaller ?? -body MPI computation exhibits diminishing growth in throughput with increased server allocation. Finally, VGG18 and ResNet50 training tasks exhibit a slower increase in throughput due to scaling bottlenecks. These diferences in scaling behavior, as well as the variability in carbon intensity and execution constraints (e.g., start time and deadline), should be considered by a carbon scaling approach when optimizing for carbon savings.

## 3 CARBONSCALER DESIGN

This section motivates <sub>CarbonScaler</sub> in the context of prior work, formulates the carbon scaling problem, and then presents <sub>CarbonScaler</sub>’s design.

## 3.1 Motivation

The <sub>suspend-resume</sub> and temporal shifting policies proposed in prior work can reduce the carbon emissions of delay-tolerant workloads [73]. However, they sufer from two drawbacks. First, the carbon intensity of grid-supplied electricity changes slowly, and thus, there may be arbitrarily long intervals (e.g., many hours) of high carbon periods where jobs remain suspended and make no progress. Such suspensions delay completion times, with a 7-10<sub>×</sub> increase in completion times in some cases [65]. Second, when batch jobs have limited temporal flexibility and cannot be significantly shifted, the efectiveness of these methods is significantly reduced.

To overcome the drawbacks above, our paper presents <sub>CarbonScaler</sub>, a new approach that exploits the <sub>resource</sub> <sub>elasticity</sub> of cloud workloads to dynamically vary the amount of resources allocated to applications in response to fluctuations in the carbon cost of their energy supply. <sub>CarbonScaler</sub>’s <sup>key</sup> <sup>insight</sup> <sup>is</sup> <sup>that</sup> the scale which yields the minimum carbon consumption, not only depends on the application characteristics but also the variations in carbon intensity across geographical regions, application start times within a given region, and the runtime of an application following a specific <sub>start</sub> <sub>time.</sub> Importantly, current approaches for selecting an application’s scale factor do not apply directly to this context, necessitating a new approach. Specifically, analytic performance models of an application, such as those used in cloud auto-scaling approaches, only account for application performance characteristics and do not consider the impact of time-varying carbon intensity on the scale factor. Similarly, the state-of-the-art approach for leveraging workload elasticity demonstrates that this scale factor varies across applications [65] but does not provide an algorithm for choosing this scale factor or show how carbon intensity variations should be considered when doing so.

To demonstrate the impact of application characteristics and temporal variations in carbon intensity on the scale factor, we consider an <sub>oracle</sub> approach for choosing the best static scale factor for a 24hr job on a per-region, per start time, and per-timeslot for ML (ResNet 18). Figure 3(a) shows that the best static scale factor for a given application varies significantly, from 1<sub>×</sub> to 8<sub>×</sub>, across geographical regions, as diferent regions exhibit diferent variations in carbon intensity. Figure 3(b) presents the distribution of best static scale factors across all the possible start times for various regions for one of them. We observe that there is no single static scale that works for a given region due to the diferences in their carbon intensity profiles. In addition, the static scale must also be adapted depending on when an application executes. Finally, as shown in Figure 3(c), the best static scale factor can even vary during application execution time, where the lowest carbon consumption is achieved by running the application with <sub>five</sub> <sub>diferent</sub> scaling factors. Further, neither application performance models, which are inherently carbon-oblivious, nor state-of-the-art carbon-aware techniques, such as Ecovisor [65] or Wait Awhile[73], can realize this oracle approach.

The dynamicity of choosing the best static scale factor motivates the design of <sub>CarbonScaler</sub>, which adapts the operating scale factor for each application depending on where and when it executes. <sub>CarbonScaler</sub> avoids computing the best static scale factor across application runs in an exhaustive brute-force manner and instead computes a carbon-aware schedule using a greedy approach. We next formulate the problem and present our dynamic scaling algorithm.

## 3.2 Problem Formulation

Similar to cloud autoscalers that scale each application <sub>independently</sub>, a carbon scaler operates independently on each cloud application that wishes to optimize its carbon emissions. When a new batch application arrives at time ??, it specifies (i) the minimum number of servers, ??, that it needs to run, where $m \geq 1$ , and (ii) the maximum number of servers ?? that can be allocated to it, $M \geq m$ . The carbon scaler can then vary the servers allocated to the application between ?? and ??. Suppose that ?? is the estimated job length when executing on the baseline allocation of ?? servers. By default, we assume that the desired job completion time is $T = t + l ,$ , which means that jobs should complete “on time” with no delays. Although ?? must be at least ?? <sub>+</sub> ?? for all jobs, some delay tolerant jobs have significant temporal flexibility and can <sub>optionally</sub> specify a longer completion time ?? such that $T { > } t + l .$ . The value $T - \left( t + l \right)$ represents the slack available when executing the job. This slack captures the willingness of users to wait in order to increase their carbon savings. The default case of $T = t + l$ assumes on-time completion and zero slack.

The completion time ?? specifies the <sub>temporal</sub> <sub>flexibility</sub> (delay tolerance) available to the job, while the maximum server allocation ?? specifies the <sub>resource</sub> <sub>elasticity</sub> of the job. The parameters ?? and ?? can be specified diferently to obtain a range of carbon scaling behaviors. For example, when $T = t + l ,$ , the application has no temporal flexibility and cannot be subjected to suspend-resume mechanisms. In this case, the job can only exploit resource elasticity by scaling up to ?? workers during low carbon periods and must be completed on time with no delays. In contrast, when $M = m$ , the job has no resource flexibility, and the carbon scaler is limited to performing only suspend-resume optimizations with a static number of servers, ??, while also ensuring it completes the job by the specified completion time ?? . Of course, when $T > t + l$ and $M > m$ , a carbon scaler has the most flexibility and can exploit both resource elasticity and temporal shifting via suspend-resume. Our goal is to design a carbon scaler that minimizes a job’s carbon emissions subject to the available resource elasticity and temporal flexibility.

## 3.3 Basic Design

When a new batch job arrives, our system, which we refer to as <sub>CarbonScaler</sub>, computes an <sub>initial</sub> schedule for executing the job through completion. The execution schedule specifies how many servers to allocate to the batch job over time and when to dynamically change the allocation in response to variations in carbon cost. This initial schedule is based on a forecast of future carbon cost, as well as the expected progress of the job over time based on its resource allocation. As the job executes, <sub>CarbonScaler</sub> adjusts its schedule periodically if it encounters forecast errors or deviations in the job’s expected progress — to ensure completion by the specified completion time ?? . Observed deviations can occur due to profiling errors, from network and locality interference [36], or resource procurement denials. We discuss these issues further in §5.7.

<sub>CarbonScaler</sub> assumes that carbon cost forecasts are available; commercial services [43, 71] provide such forecasts for up to four days with high accuracy in most locations. Since the application specifies its temporal flexibility (in terms of completion time ?? ) and its resource elasticity (in terms of the varying server allocation from ?? to ??), <sub>CarbonScaler</sub>’s schedule responds to fluctuations in forecasted carbon cost by scaling down or completely suspending the job when the carbon cost is high and opportunistically scaling up when the carbon cost is low.

Diferent clustered batch applications will have diferent scaling behaviors, as shown in Figure 2, which should be considered when scaling an application’s server capacity between the specified range of ?? to ??. As noted in Figure 2, applications’ throughput either increases sub-linearly or increases somewhat linearly initially and then shows diminishing returns with an additional increase in server capacity. This behavior is a direct consequence of Amdahl’s law [3], which states that the speedup of a parallel application is limited by the amount of sequential code within it — adding server capacity only speeds up the parallel component of the application. Software bottlenecks, such as synchronization overheads, also limit the ability to scale up.

<sub>CarbonScaler</sub> considers this scaling behavior in terms of a <sub>marginal</sub> <sub>capacity</sub> <sub>curve</sub>, shown in Figure 4, which captures the incremental increase in application capacity (i.e., throughput) for each unit increase in server capacity. The ideal case of linear scaling translates to a <sub>flat</sub> marginal capacity curve where each additional server results in a unit increase in (normalized) application capacity (see Figure 4(a)). Most applications will have a diminishing marginal capacity curve, where marginal capacity decreases monotonically with an increase in the server capacity (see Figure 4(b)).

![](images/7db62a3d87786ffbfd5604fac60ebe79b479f3d8e87192ade3fb16d7a785958f.jpg)  
(a) Linear scaling

![](images/00b6e6bc51193c7ca9df330dfc681bd19f406279c7220c34c44791a63e1a63bd.jpg)  
(b) Diminishing scaling  
Fig. 4. Example marginal capacity curves.

The marginal capacity curve and the carbon intensity curve can then be used to scale the application up or down in a carbon-eficient manner. To do $s 0 ,$ the marginal capacity curve is normalized by the forecasted carbon cost in each time step to compute the <sub>marginal</sub> <sub>capacity</sub> <sub>per</sub> <sub>unit</sub> <sub>carbon</sub> — the marginal work done per unit carbon. <sub>CarbonScaler</sub> then adds server capacity to the time slots that maximize the work done per unit of carbon. By doing $s 0 ,$ CarbonScaler <sup>allocates</sup> more server resources when the carbon cost is low since more marginal work can be done at a <sub>lower</sub> <sub>carbon</sub> <sub>cost. CarbonScaler</sub> will incrementally add servers to various time slots until suficient server capacity has been added to complete the job within the desired completion time ?? , thereby yielding a carbon-eficient execution schedule that optimizes the carbon emissions.

In practice, each application can have multiple marginal capacity curves, each representing a diferent phase of its execution. For example, a MapReduce job can have diferent scaling behaviors and marginal capacity curves for its map and reduce phases. For ease of exposition, our discussion below assumes a single marginal capacity curve per application. However, our approach generalizes to multiple marginal capacity curves by considering the appropriate scaling curve in each time slot that corresponds to the current phase of the application’s execution.

## 3.4 Carbon Scaling Algorithm

<sub>CarbonScaler</sub> relies on the knowledge of application scalability profile, carbon intensity forecast, and other job constraints to decide when to i) horizontally scale resources up or down or ii) suspend execution to ensure minimum carbon consumption. As noted earlier, when a new job arrives at time ??, it specifies a <sub>desired</sub> completion time (i.e., a “deadline”) of time ?? . We also assume that the marginal capacity curve of the application is obtained by profiling the application ofline (see Section 4.1) and is known at arrival time. Finally, the algorithm takes the carbon cost forecast ??, which we assume to be correct. We analyze the impact of inaccurate forecasts in Section 5.7.

We assume that the interval <sub>[</sub>??,?? <sub>]</sub> is discretized into smaller fixed-length intervals (e.g., 15 minutes or an hour), and the number of servers allocated to the job can be changed at the start of each interval. Suppose that there are ?? time intervals between $[ t , T ] , n \geq 1$ . Let $c _ { 1 } , c _ { 2 } , . . . , c _ { n }$ denote the forecasted carbon cost in each interval $i , i \in [ t , T ]$ . Suppose that the marginal capacity curve is denoted by $M C _ { m } , M C _ { m + 1 } , . . . , M C _ { M }$ , where $M C _ { j }$ is the marginal capacity increase after allocating the ?? -th servers, $j \in [ m , M ]$ . Since the estimated job length is ?? when executing with minimum server capacity ??, the total work the job needs to perform is $W = l \cdot M C _ { m }$ . Our algorithm must compute a schedule where the aggregate server capacity allocated to the job over <sub>[</sub>??,?? <sub>]</sub> can perform this work before the completion time ?? , minimizing carbon emissions.

The aforementioned carbon scaling problem is a marginal allocation problem of discrete resources, which is known to yield an optimal solution in many cases [22]. Our greedy <sub>Carbon</sub> <sub>Scaling</sub> <sub>Algorithm</sub>, detailed in Algorithm 1, builds on the algorithm and theoretical results in [22]. We provide the requirements and the optimality proof of our greedy <sub>Carbon</sub> <sub>Scaling</sub> <sub>Algorithm</sub> in appendix A. The Algorithm, first computes the <sub>marginal</sub> <sub>capacity</sub> <sub>per</sub> <sub>unit</sub> <sub>carbon</sub> in each time interval ?? by normalizing the ???? curve with carbon cost $c _ { i }$ in that interval (line 5). That is, the marginal capacity per unit carbon in time interval ?? is $M C _ { m } / c _ { i } , M C _ { m + 1 } / c _ { i } , . . . , M C _ { M } / c _ { i }$ . The greedy algorithm then iteratively and incrementally allocates server capacity to various time slots in order of decreasing <sub>marginal</sub> <sub>capacity</sub> <sub>per</sub> <sub>unit</sub> <sub>carbon</sub> (lines 6-11). For each iteration, the algorithm chooses the interval ?? from [1, n] such that allocating incremental server capacity to that time slot maximizes the work done per unit carbon (i.e., chooses the interval with the greatest ${ M C _ { j } / c _ { i } }$ across all intervals). After allocating server capacity to that interval, it iteratively determines the next interval where allocating additional server capacity yields the next highest work done per unit of carbon.

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1: Carbon Scaling Algorithm()

Input: Marginal capacity (MC), time slots [t, T], carbon cost forecast (c), total work (W)
Output: Execution Schedule S

1  $S \leftarrow [0..0]$ ;

2  $L \leftarrow []$ ;

3 for  $i \in [t, T]$  do

4    for  $j \in [m, M]$  do

5    | L.append(i, j, MCj/ci);

6  $L \leftarrow \text{Sort}(L)$ ; // w.r.t. Norm. Marginal Cap.

7  $w \leftarrow 0$ ;

8 while w &lt; W do

9    i, j, * ← L.pop(); // next highest MCj/ci

10    S[i] = j; // increase allocation in slot i

11    w.update(S);

12 return S
</div>

![](images/40e487d777f45dc1257485d5d4466bfde7b30cb68d4c3f09cec3a9dc135dec57.jpg)  
(a) Carbon-agnostic

![](images/8bb7456e94c4722fe1837690454949a139f930bdcd43a1ce88e952beb04f7c1e.jpg)  
(b) Linear scaling

![](images/5d97d711787ae535aa77ddf9628e23f7a3248ac5d4486aafacf651d0d579fd7a.jpg)  
(c) Diminishing scaling

![](images/a002e72067fe1c302f015f9659556f2467c81bd57f7d9bad13b2e18aaa219eab.jpg)  
(d) MC per unit carbon  
Fig. 5. An illustrative example of our carbon scaling algorithm at work.

Note that our greedy algorithm may allocate additional capacity to the same interval as the previous iteration of its marginal work done per unit carbon continues to be the highest across all intervals. Otherwise, a new time interval with the next highest marginal work done per unit is chosen for allocating server capacity. Also, when a time interval is initially chosen by the greedy algorithm for capacity allocation, it must be allocated the minimum requirement of ?? servers, after which the allocation can be increased incrementally by one in subsequent steps. Similarly, if a time slot reaches the maximum allocation of ?? servers, it is not considered further by the greedy algorithm. The process continues until suficient capacity has been allocated across the ?? time intervals to complete ?? units of work. This yields an initial schedule where each time interval has either a zero allocation (causing the job to be suspended in that period) or a non-zero allocation between ?? and ??, with the server allocation potentially changing at interval boundaries.

<sub>Example.</sub> To illustrate our carbon scaling algorithm, consider a job of length 2 that arrives at ?? <sub>=</sub> 0 and needs to finish by ?? <sub>=</sub> 3. Suppose that the job needs to execute on at least one server (?? <sub>=</sub> 1) and at most two servers (?? <sub>=</sub> 2). Carbon-agnostic execution will run the job as soon as it arrives, and it will complete at time 2, as shown in Figure 5(a). Suppose that the forecasted carbon cost in time slots 1, 2, and 3 is $c _ { 1 } = 1 0 \mathrm { ( " l o w " ) } , c _ { 2 } = 1 0 0 \mathrm { ( " h i g h " ) }$ , and $c _ { 3 } = 2 0$ ("medium"), respectively. First, assume that the job has ideal scaling behavior and a flat marginal capacity curve of $M C _ { 1 } = 1$ and $M C _ { 2 } = 1$ . The algorithm simply allocates two servers to the job in slot 1, since it has the lowest carbon cost and the highest marginal capacity per unit carbon. As shown in Figure 5(b), such a job runs with two servers and terminates at the end of slot 1.

Next, assume a job with a diminishing marginal capacity curve, given by $M C _ { 1 } = 1$ and $M C _ { 2 } = 0 . 7$ Figure 5(d) shows the marginal capacity per unit cost table $( M C _ { j } / c _ { i } )$ for all three slots. The greedy algorithm allocates the first server to slot 1, since it has the highest marginal capacity per unit cost of 0.1. In the next iteration, the greedy algorithm allocates a second server to slot 1 as it still has the highest marginal capacity per unit cost $( M C _ { 2 } / c _ { 1 } = 0 . 0 7 )$ . Although two servers have been allocated, the total work done by these two servers is only $1 . 7 \left( M C _ { 1 } + M C _ { 2 } \right)$ , which cannot complete the job of length $2 \left( W = 2 \right)$ . The algorithm then allocates another server to slot 3, which has the next highest marginal capacity per unit cost $( M C _ { 1 } / c _ { 3 } = 0 . 0 5 )$ . This yields a schedule where the job is given 2 servers in slot 1, zero in slot 2, and one server in slot 3. The job only runs for one-third of slot 3 before it completes. The example also illustrates a tradeof where <sub>CarbonScaler</sub> reduces the emissions compared to carbon-agnostic execution (from 110 to 40 carbon units) but increases cloud costs by 15% due to the need for a third server. The tradeof between carbon saving and cost overheads is fundamental to carbon-aware computing, as demonstrated by prior work [29].

<sub>Periodic</sub> <sub>Schedule</sub> <sub>Recomputation.</sub> Once the algorithm computes an initial schedule, <sub>CarbonScaler</sub> can begin execution of the job by auto-scaling it up or down, or suspending it, in each time slot as per the schedule. <sub>CarbonScaler</sub> continuously monitors the work done (“job progress”) and the emissions of the job over the course of its execution. Recall that the initial schedule is computed based on a <sub>forecasted</sub> carbon cost and an <sub>estimated</sub> marginal capacity curve derived from profiling, both of which may have errors in their estimates. Similar to weather forecasts, carbon forecasts can have errors, especially over the period of multiple days [43, 44]. Similarly, the marginal capacity curves may not be exact since production environments may difer somewhat from the profiling environment [36, 57, 63]. These errors can cause deviations in the expected work done or the expected carbon emissions as estimated by the initial schedule.

To be robust to carbon prediction or profile estimation errors, <sub>CarbonScaler</sub> compares the expected work and carbon emissions to the estimates in the schedule at the end of each time interval. If the deviations exceed a threshold, it recomputes the schedule for the remainder of the job’s execution from the current time $t ^ { \prime }$ to the completion time ?? . When doing so, <sub>CarbonScaler</sub> can use an updated carbon forecast if available, since such forecasts are often updated every few hours, similar to weather forecasts. Thus, if the progress deviates from the plan (e.g., due to profiling errors), <sub>CarbonScaler</sub> will recompute the schedule to ensure the highest carbon savings. Since some batch jobs can execute for days [70], such schedule adjustments provide robustness to prediction errors and ensure timely job completion while minimizing carbon emissions.

<sub>Run</sub> <sub>Time</sub> <sub>Complexity.</sub> In Algorithm 1, the time complexity of computing the marginal capacity per unit carbon (steps: 3-5) is $O ( n . M )$ , list sorting is $O ( n M \log n M )$ , and computing the schedule is ?? <sub>(</sub>????<sub>)</sub> (steps: 8-11). The total complexity is $O ( n M + n M \log n M ) \approx O ( n M \log n M )$

## 4 CARBONSCALER IMPLEMENTATION

This section describes <sub>CarbonScaler</sub>’s implementation, which optimizes the carbon emissions of distributed batch cloud workloads. Our system comprises three main components: (1) <sub>Carbon</sub> <sub>Profiler</sub>, which uses ofline profiling to estimate marginal capacity (????) curves and energy usage of jobs, (2) <sub>Carbon</sub> <sub>AutoScaler</sub> is our cloud-based carbon scaling system implemented in Kubernetes [41], and (3) <sub>Carbon</sub> <sub>Advisor</sub>, which simulates the execution of the jobs to estimate carbon reduction under diferent deployment configurations. <sub>CarbonScaler</sub> is implemented in Go using <sub>∼</sub>2.5?? SLOC. The code is available at https://github.com/umassos/CarbonScaler.

![](images/77d8d27660dd1028427a67eadd836a499f89e1b3cd5eb3afaab30904136f8363.jpg)  
Fig. 6. An overview of Carbon AutoScaler.

## 4.1 Carbon Profiler

<sub>CarbonScaler</sub> requires the marginal capacity curve of a job for carbon-aware scaling. <sub>Carbon</sub> <sub>Profiler</sub> performs a one-time ofline profiling of a new job to derive its marginal capacity curve. To do so, it runs the job with server allocations ranging from the job-specified minimum number of servers, ??, to the maximum number of servers, ??, and records the work done at each allocation. To minimize the profiling overhead, <sub>Carbon</sub> <sub>Profiler</sub> runs the job for a small, configurable amount of time ?? (up to a few minutes) and varies the resource allocation with a granularity ??, which depends on ??. If ?? > 1, <sub>Carbon</sub> <sub>Profiler</sub> interpolates the recorded measurements to obtain a complete marginal capacity curve. Finally, the marginal capacity curves are valid for a computing environment identical to the profiling environment. The scaling behavior and the expected savings may change if the environment is significantly diferent, necessitating environment-specific profiling or an online update of the capacity curves. <sub>CarbonScaler</sub> also allows substituting <sub>Carbon</sub> <sub>Profiler</sub> with alternative workload profiling approaches from prior work [13, 38, 54, 56–58, 63].

## 4.2 Carbon AutoScaler

Figure 6 shows an overview of <sub>Carbon</sub> <sub>AutoScaler</sub> that uses Kubeflow [40] to implement our <sub>Carbon</sub> <sub>Scaling</sub> <sub>Algorithm</sub> from §3.4. The incoming elastic batch applications use Kubernetes’ Custom Resource Definition (CRD), written in <sub>.yaml</sub> format. <sub>Carbon</sub> <sub>AutoScaler</sub> follows Kuber netes standards in defining its user-facing interface. In this case, the user extends the normal job specification by adding extra <sub>Carbon</sub> <sub>AutoScaler</sub>-specific maps that provide scaling and scheduling information, including minimum ?? and maximum ?? number of servers, completion time ?? , and an estimated job length ??. The user also specifies methods for obtaining the marginal capacity curve, where the current default is profiling. The user then submits the jobs to <sub>Carbon</sub> <sub>AutoScaler</sub> using standard using Kubernetes APIs such as <sub>kubectl</sub>.

We implement <sub>Carbon</sub> <sub>AutoScaler</sub> as a controller that sits on top of the Kubeflow training operator and leverages its core resource management functionality for clustered batch jobs, such <sup>as</sup> <sup>ML</sup> <sup>training</sup> <sup>and</sup> <sup>MPI.</sup> Carbon AutoScaler <sup>first</sup> <sup>runs</sup> <sup>the</sup> Carbon Scaling Algorithm <sup>to</sup> compute the initial schedule for each job. To do so, <sub>Carbon</sub> <sub>AutoScaler</sub> tracks carbon intensity using a dedicated service that provides the instantaneous and forecasted carbon intensity. Then, <sub>Carbon</sub> <sub>AutoScaler</sub> informs the Kubeflow training operator to execute the schedule by modifying the Kubeflow job specification to scale the resources allocated to the job, such as the number of replicas. <sub>Carbon</sub> <sub>AutoScaler</sub> is also in charge of maintaining the job status of the Kubeflow operator. <sub>Carbon</sub> <sub>AutoScaler</sub> implements resource-level and application-level monitoring. <sub>Carbon</sub> <sub>AutoScaler</sub> implements additional Kubernetes services to monitor resource usage, energy usage, and carbon usage over time. We track CPU usage using Kubernetes Metrics Server [64], CPU energy usage using Running Average Power Limiting (RAPL) [17] interfaces and PowerAPI [11], and GPU energy usage using NVIDIA Data Center GPU Manager (DCGM) [53]. The resource and power monitoring can include other resources such as storage and network. <sub>Carbon</sub> <sub>AutoScaler</sub> monitors application-level metrics such as progress and throughput via application-level interfaces.

<table><tr><td>Name</td><td>Implementation</td><td>Epochs</td><td>BatchSize</td><td>Power (W)</td></tr><tr><td>N-Body Simulation (10,000)</td><td>MPI</td><td>138000</td><td>NA</td><td>CPU (60)</td></tr><tr><td>N-Body Simulation (100,000)</td><td>MPI</td><td>1500</td><td>NA</td><td>CPU (60)</td></tr><tr><td>Resnet18 (Tiny ImageNet)</td><td>Pytorch</td><td>173</td><td>256</td><td>CPU+GPU (210)</td></tr><tr><td>EfficientNetB1 (ImageNet)</td><td>Pytorch</td><td>45</td><td>96</td><td>CPU+GPU (210)</td></tr><tr><td>VGG16 (ImageNet)</td><td>Pytorch</td><td>31</td><td>96</td><td>CPU+GPU (210)</td></tr></table>

Table 1. Details of elastic workloads in evaluation. Epochs represent the number of epochs needed for a 24hr job.

Finally, <sub>Carbon</sub> <sub>AutoScaler</sub> registers a reconcile callback function, which is called when the carbon intensity changes and when applications report their progress. This enables <sub>Carbon</sub> <sub>AutoScaler</sub> to detect divergence in progress, throughput, or carbon intensity. <sub>CarbonScaler</sub> then recomputes the schedule as explained in §3.4.

## 4.3 Carbon Advisor

<sub>Carbon</sub> <sub>Advisor</sub> enables pre-deployment analysis of the carbon scaling algorithm in an environment that simulates the operation of <sub>Carbon</sub> <sub>AutoScaler</sub>. <sub>Carbon</sub> <sub>Advisor</sub> takes, as input, a carbon trace, job start time, deadline, job length, and <sub>CarbonScaler</sub>-specific parameters, such as range of server allocations <sub>[</sub>??, ??<sub>]</sub> and marginal capacity curve. The fidelity of <sub>Carbon</sub> <sub>Advisor</sub> depends on the accuracy of the marginal capacity profile for the application. In Section 5, we demonstrate the high fidelity of <sub>Carbon</sub> <sub>Advisor</sub> in estimating the carbon savings from diferent carbon-aware scaling policies. The <sub>Carbon</sub> <sub>Advisor</sub> simulates the running of the job and reports savings for carbon-aware scaling policies. Additionally, the <sub>Carbon</sub> <sub>Advisor</sub> enables simulating various kinds of errors to ensure the robustness of the predictions, as described in Section 5.7. The simple plug-and-play nature of the tool allows application developers to perform what-if scenarios and explore a wide range of parameters before actual deployment. For example, users can explore the benefits of extending their waiting time and its impact on carbon savings. <sub>Carbon</sub> <sub>Advisor</sub> also enables key high-level analysis by default, such as computing the distribution of carbon savings across diferent start times of the year. Finally, to facilitate initial exploration, we plan to provide carbon traces and marginal capacity curves used in the paper alongside the tool.

## 5 EXPERIMENTAL EVALUATION

This section evaluates the performance of <sub>CarbonScaler</sub> using our prototype implementation, described in Section 4. We augment the prototype evaluation results with additional large-scale analysis that leverages <sub>Carbon</sub> <sub>Advisor</sub>.

## 5.1 Experimental Setup

<sub>Workload.</sub> Table 1 describes the elastic workloads we use for evaluating <sub>CarbonScaler</sub> and their specifications. The workloads span both CPU- and GPU-intensive applications such as the ?? -body problem [1] implemented using MPI [24] and machine learning models, including ResNet [31], EficientNet [69], and VGG [69] implemented using Pytorch [55]. The table shows the base configu rations and power measurements for jobs that need 24hrs to finish. The chosen workloads have a wide-range of scaling characteristics (shown in Figure 2), configurations, and energy requirements. <sub>Infrastructure.</sub> We deployed <sub>CarbonScaler</sub> in two diferent settings to demonstrate its adaptability to the underlying infrastructure. For CPU-intensive workloads, we used a local computing cluster consisting of 8 servers, each equipped with a 16-core Xeon CPU E5-2620, connected through a 10G network. For GPU-intensive workloads, we deployed <sub>CarbonScaler</sub> on Amazon Web Services (AWS) using 8 <sub>p2.xlarge</sub> instances, each equipped with NVIDIA K80 GPU.

![](images/7fd1b7e7b983ff352f0e135f7e60d7bbcc7bb86541189891dc436d27feeb4fd9.jpg)  
Fig. 7. Most cloud regions globally have a high carbon cost, but also show significant daily variations, providing an opportunity for CarbonScaler to optimize carbon emissions.

<sub>Carbon</sub> <sub>Traces.</sub> We collected carbon traces for diferent geographical locations using electricityMap [45], an online service that provides real-time and archival carbon intensity information. We use average carbon intensity values, measured in grams of carbon dioxide equivalent per kilowatt-hour (gCO2eq<sub>/</sub>kWh), provided at hourly granularity. The collected carbon traces span from January 2020 to December 2022, we specify the duration for each trace where it is used.

To choose representative regions for our evaluation, we analyzed the average carbon intensity and the coeficient of variation (computed as standard deviation over mean) for diferent AWS regions. Figure 7 shows the results for 37 regions. Most regions have high carbon intensity but also show high daily variations, while some have low carbon intensity with similarly high daily variations. Since <sub>suspend-resume</sub> and <sub>CarbonScaler</sub> rely on these high variations to reduce emissions, the figure indicates that both techniques will be efective in the majority of low-carbon as well as high-carbon cloud regions. A few cloud regions have stable carbon costs (i.e., low variations), including low carbon regions such as Iceland and Sweden, and high carbon regions such as India and Singapore. The efectiveness of <sub>suspend-resume</sub> and <sub>CarbonScaler</sub> is diminished in such cloud regions as changing the execution time and scale does not alter the carbon intensity. Still, such regions are a small minority of the total cloud regions in a global cloud platform such as AWS. Based on this analysis, we choose Netherlands ( ) as a representative high carbon region and Ontario, Canada ( ) as an example of a low carbon region for our subsequent experiments. Nonetheless, we evaluate the potential savings across regions in Section 5.6.

<sub>Baselines</sub> <sub>Policies.</sub> We evaluate the performance of <sub>CarbonScaler</sub> against three baseline poli-<sup>cies:</sup> carbon-agnostic<sup>,</sup> suspend-resume<sup>,</sup> <sup>and</sup> static-scale<sup>.</sup> <sup>The</sup> carbon-agnostic <sup>is</sup> <sup>a</sup> <sup>simple</sup> policy that runs a job without considering carbon emissions and represents the status quo. The <sub>suspend-resume</sub> policy is inspired by prior work [19, 73]. As mentioned in §2.3, <sub>suspend-resume</sub> can be implemented in two ways: threshold-based, which uses a carbon threshold to suspend resume a job in a deadline-unaware manner, and deadline-based, which chooses the ?? lowest carbon periods before the specified deadline for execution. In this case, <sub>suspend-resume</sub> defaults to <sub>carbon-agnostic</sub> policy when the completion time equals the job length (?? <sub>=</sub> ??), i.e., no slack, since execution cannot be deferred. This policy acts as a baseline for temporal shifting scenarios where we assume a job has a completion time higher than the job length (?? > ??). Finally, <sub>static-scale</sub> is another policy inspired by prior work [65], where an application picks the lowest carbon intensity points and runs with a certain <sub>static</sub> scale factor to utilize the carbon intensity variations better. This is our default baseline for scenarios where we evaluate <sub>CarbonScaler</sub> for its ability to leverage workload elasticity and scaling. Unless stated otherwise, we report the mean across 15 runs for our cloud experiments and 100 runs for <sub>Carbon</sub> <sub>Advisor</sub>’s simulated executions.

![](images/3bd7dc89464cef0dc3bce546ef80c8b0e5d0d0e3c4794a10b818ecd619e78ebd.jpg)  
Fig. 8. CarbonScaler in action for a 48hrs long <sup>??</sup> -body MPI job $( N = 1 0 0 k ) ,$ where $T = 2 \times l .$

<sub>Carbon</sub> <sub>Advisor</sub> <sub>Fidelity.</sub> To demonstrate the fidelity of the simulator, we compare the carbon savings estimates from <sub>Carbon</sub> <sub>Advisor</sub> with the results from various real experiments in the evaluation. <sub>Carbon</sub> <sub>Advisor</sub> estimates have an average error of less than 5%, demonstrating the high accuracy of our simulation results based on <sub>Carbon</sub> <sub>Advisor</sub>.

## 5.2 CarbonScaler in Action

To show <sub>CarbonScaler</sub> in action, we ran a 48hr ?? -body MPI job on our CPU cluster and compared its execution to the threshold-based <sub>suspend-resume</sub> (deadline-unaware) and <sub>carbon-agnostic</sub> policies. As shown in Figure 8, the <sub>carbon-agnostic</sub> policy starts the job as soon as it arrives and finishes in 48hrs at the cost of 184g of $C O _ { 2 }$ emissions. The <sub>suspend-resume</sub> policy <sub>suspends</sub> the job during high carbon intensity periods and waits for the carbon intensity to fall below a threshold $( 2 5 ^ { t h }$ percentile in this case) to <sub>resume</sub> the job. By leveraging temporal flexibility, <sub>suspend-resume</sub> saved 45% carbon compared to the <sub>carbon-agnostic</sub> policy but increased the job completion time by 4<sub>×</sub>. Finally, we set the desired completion time ?? to be 96hrs, i.e., $T = 2 \times l ,$ , and execute our proposed <sub>CarbonScaler</sub> policy. <sub>CarbonScaler</sub> scales the number of servers depending on the application’s scaling properties and the carbon cost at a given time. As a result, <sub>CarbonScaler</sub> achieves 42% carbon saving over <sub>carbon-agnostic</sub> policy. <sub>CarbonScaler</sub> achieves comparable savings with <sub>suspend-resume</sub> while also reducing the job completion time to 2<sub>×</sub> of <sub>carbon-agnostic</sub> policy.

## 5.3 Impact of Workload Elasticity

The two key aspects that impact carbon savings from <sub>CarbonScaler</sub> are temporal flexibility and workload elasticity. While prior work necessitates temporal flexibility for carbon savings, <sub>CarbonScaler</sub> can achieve significant savings by leveraging workload elasticity even when no temporal flexibility is available. The extent of savings depends on the scalability properties of the workload: a highly scalable job (with flat or close to flat marginal capacity) can achieve higher savings, as illustrated for the simple workload in Figure 5. To demonstrate the elasticity efect, we limit the job completion time to the job length, i.e., $T = l ,$ , which means no temporal flexibility is available. We run 24hrs long jobs for various applications in Table 1 using <sub>carbon-agnostic</sub> <sup>policy,</sup> static-scale <sup>(2</sup>×<sup>),</sup> <sup>and</sup> CarbonScaler<sup>.</sup>

Figure 9 shows the performance of the three policies for diferent workloads. Figure 9(a) compares the absolute carbon footprint of the three policies and shows that the <sub>CarbonScaler</sub> highest savings are for highly scalable workloads. For example, for ?? -body (?? =100k) and ML (ResNet18), CarbonScaler <sup>saves</sup> <sup>up</sup> <sup>to</sup> <sup>140</sup> <sup>and</sup> <sup>63</sup> <sup>(gCO2eq)</sup> <sup>compared</sup> <sup>to</sup> carbon-agnostic <sup>and</sup> static-scale (2<sub>×</sub>), respectively. To demonstrate the superiority of <sub>CarbonScaler</sub>, independent of the task and start-time dependent carbon consumption, we compare the normalized carbon savings of diferent policies to <sub>CarbonScaler</sub>. Figure 9(b) compares the performance of all policies to <sub>CarbonScaler</sub>, where the whiskers represent the $9 5 ^ { t h }$ percentile confidence interval and the horizontal line represents the performance of <sub>CarbonScaler</sub>. The figure shows that, aside from the saving, workloads, and start times, <sub>CarbonScaler</sub> demonstrates the ability to outperform all other policies. In particu-<sup>lar,</sup> CarbonScaler <sup>uses</sup> <sup>33%</sup> <sup>and</sup> <sup>20%</sup> <sup>less</sup> <sup>carbon</sup> <sup>than</sup> carbon-agnostic <sup>and</sup> static-scale <sup>(2</sup>×<sup>),</sup> respectively. The figure also shows that, since the <sub>static-scale</sub> does not consider the job’s scala bility properties, it can instead <sub>increase</sub> the carbon consumption for some workloads by as much as 20% by scaling the job beyond a single <sub>optimal</sub> scale factor. On the other hand, <sub>CarbonScaler</sub> is cognizant of scaling behavior and picks a diferent scale at each time slot that has the highest work done per unit carbon cost, yielding minimum carbon consumption.

![](images/c23fac9cda24d24cfdeb2e16dd3cfac2cd303479de1091d4599d2f17c5a59a22.jpg)  
(a) Carbon footprint

![](images/67bdb72f5bdfc4b1a25092833bb0f96c52cad5ec649a90e09dfc031be157bffa.jpg)  
(b) Performance w.r.t <sub>CarbonScaler</sub>

Fig. 9. Carbon footprint and performance of diferent workloads scheduled under carbon-agnostic, static-scale (2×), and CarbonScaler, in Ontario, Canada, where <sup>??</sup> = <sup>??</sup> (i.e., no slack and on-time completion).  
![](images/1ac526cbf2b88ea67740c642a66f2d9012a7ceb63225f9c26eabf475fa44e93a.jpg)  
(a)

![](images/5a881174ff370bc9c75a67d4c829201980d4e75cb2f887e086e6737c1b58e4b5.jpg)  
(b)

![](images/737dcb2df8d08b6b2094eaa0deacf3eec718b47f76d4e78e2d2c554a9b402603.jpg)  
(c)

Fig. 10. CarbonScaler vs. the best static scale (SS) factor in Ontario, Canada. Carbon emissions of various static scale factors compared to CarbonScaler (a), percentage of start times when a policy consumes more carbon than carbon-agnostic (b), and static scale oracle against CarbonScaler for multiple applications (c).  
![](images/e11c60c6d04f3b4cfdb563d2b15d8d6953bd8a7d17854b4f1123e957537374b4.jpg)  
(a) Netherlands

![](images/620633e1052c278e721d8d001a7803df9d8df89a0bbce7967a39257301d6ac21.jpg)  
(b) California, US

![](images/8e797b5ee337ae6061fdb247a7e41bdc47e459087b0639934f7324306f011b51.jpg)  
(c) Texas, US  
Fig. 11. Comparing CarbonScaler with static scale oracle in multiple regions.

To further demonstrate <sub>CarbonScaler</sub> benefits over the best static scale factor, we use <sub>Carbon</sub> <sub>Advisor</sub> to compare <sub>CarbonScaler</sub> against oracle-based static scale factors. Figure 10(a) shows the performance of all scale factors and <sub>CarbonScaler</sub> for ?? -body (?? =10k). The static scaling consumes 17-65% more carbon than <sub>CarbonScaler</sub>. While the <sub>static-scale</sub> policy can reduce carbon emissions compared to <sub>carbon-agnostic</sub> for scale factors 2 and 3, it can consume more carbon at higher scale factors due to the non-linear scalability of the workloads. The potential increase in carbon consumption is not only true for an arbitrary non-optimal scale factor; even the best scale factor for each start time can consume more carbon than <sub>carbon-agnostic</sub>. Figure 10(b) shows the probability that the best scale factor (on top of each bar) yields a higher consumption than the <sub>carbon-agnostic</sub> operation. As shown, certain instances always exist where this best scale factor performs worse than <sub>carbon-agnostic</sub>. Perhaps the only exception is ML (VGG16), a non-scalable application, where the best scale factor is the <sub>carbon-agnostic</sub> (1<sub>×</sub>).

![](images/e06000481d8ee038c9ec965059cd5296780e5971991f6c3fd93591f131215a20.jpg)

![](images/7303057b797561ebd670fa4645a09966827283c6361759bc6a41fd41c25e4b6d.jpg)  
(a) Ontario, Canada

![](images/88ebcf97176828375c912179542ba132288a7b9cc2c832000ba6400307d6bc84.jpg)

![](images/ab56b37a8480ada9c62c41d14d617e0eab5bae2a6989b1117495eb34cb31254f.jpg)  
(b) Netherlands  
Fig. 12. Carbon footprint and normalized performance of diferent workloads and policies, where $T = 1 . 5 \times l .$

As opposed to <sub>CarbonScaler</sub>, the best static scale factor may not be optimal for all the time slots during the execution of a job, resulting in higher carbon emissions. In Figure 10(c), we show the additional savings from adapting the scale factor during the execution of a job for multiple applications. As demonstrated, <sub>CarbonScaler</sub> outperforms the static scale oracle by 1.2% to 8%, depending on the job’s scalability characteristics. Figure 11 extends the evaluation of 10(c) and shows how <sub>CarbonScaler</sub> outperforms the oracle <sub>static-scale</sub> in diferent regions, even when carbon savings are limited. However, it is worth noting that static state oracle is the artifact of our implementation. Neither application performance models, which are inherently carbon-oblivious, nor state-of-the-art carbon-aware techniques, such as Ecovisor [65] or Wait Awhile[73], can realize this optimal oracle approach.

Key Takeaway. CarbonScaler better leverages the workload elasticity by choosing dynamic scale factors depending on the job scalability characteristics and carbon intensity for each start time for the job and each time slot during a job’s execution.

## 5.4 Impact of Temporal Flexibility

In addition to workload elasticity, temporal flexibility can be an important source of carbon savings for delay-tolerant jobs. We evaluate the impact of temporal flexibility by running workloads <sup>from</sup> <sup>Table</sup> <sup>1</sup> <sup>using</sup> carbon-agnostic <sup>policy,</sup> suspend-resume <sup>policy,</sup> <sup>and</sup> CarbonScaler <sup>with</sup> extended completion times where $T > l .$ . To ensure that the <sub>suspend-resume</sub> respects the job specified completion time, we use the deadline-aware version of the <sub>suspend-resume</sub> policy [73]. Figure 12 shows the carbon consumption (left) and performance of diferent policies (right) when <sub>running</sub> the workloads with 24 hrs length ??, and 36 hrs as completion time ?? , $T = 1 . 5 \times l ,$ across two locations. <sub>CarbonScaler</sub> is better at exploiting the temporal flexibility and outperforms the <sub>suspend-resume</sub> policy for all workloads. As shown, <sub>CarbonScaler</sub> is able to save 36% and 22% compared to <sub>carbon-agnostic</sub> and <sub>suspend-resume</sub> in Ontario, Canada, and 51% and 37% in the Netherlands for the highly scalable ML (ResNet18). On the other hand, for less scalable workloads such as ML(VGG16), most of the carbon savings of <sub>CarbonScaler</sub> stem from time-shifting, yielding comparable savings to <sub>suspend-resume</sub>. The right column of the figure also demonstrates the superiority of <sub>CarbonScaler</sub> aside from the carbon savings, which is start-time and task dependent. <sub>Efect</sub> <sub>of</sub> <sub>Completion</sub> <sub>Time.</sub> Prior results have demonstrated that temporal flexibility can yield significant savings. Figure 13 evaluates the gain in carbon savings with increasing temporal flexi bility (higher desired completion time ?? ). We run a 12hrs ML training job (ResNet18) and configure it to complete in 12hrs $( T = l )$ up to 36hrs $( T = 3 \times l )$ . For higher completion times, more low carbon slots become available, which allows <sub>CarbonScaler</sub> and <sub>suspend-resume</sub> to reduce the carbon consumption by 30-45% and 0-32%, respectively. <sub>CarbonScaler</sub> achieves higher savings by using a higher scale factor during the lowest carbon slots and only picks a higher carbon slot if it gives a better marginal work done per unit carbon. For very high completion times, the savings of <sub>CarbonScaler</sub> over <sub>suspend-resume</sub> diminish, since it begins to prefer job suspensions over high scale factors to avoid the impact of non-linear scaling behavior.

![](images/ba28dd844ee17dbeb575e308141300dba4796b94af4f68109386268385b57632.jpg)  
Fig. 13. Efect of completion time on the carbon footprint of a 12hrs long ResNet18 job in Ontario, Canada.

![](images/92690b13a7203b5793c90c61ccda5cc10651486a18c95be8daae9d7956eb5503.jpg)  
Fig. 14. Efect of job length on $C O _ { 2 }$ savings for an <sup>??</sup> - Body $( N { = } 1 0 0 k )$ job in Ontario, Canada, $T = 1 . 5 \times l .$

![](images/2479c9d79bc024d48c4129da4541ae9a905fdba33703002d438dbad31a0ea697.jpg)  
(a) Carbon Consumption (g)

![](images/077b98950fdbb0f74278796b27e35f6dbb5e858bbc5a0c448b5026bd23a08227.jpg)  
(b) Performance w.r.t. <sub>CarbonScaler</sub> (%)  
Fig. 15. Carbon consumption and normalized performance of 24-hour <sup>??</sup> -body (<sup>??</sup> =100k) MPI job with diferent cluster sizes in Ontario, Canada, where $T = 1 . 5 \times l .$

<sub>Efect</sub> <sub>of</sub> <sub>Job</sub> <sub>Length.</sub> The length of a job is another key factor in determining carbon savings. As the job length increases, more low-carbon slots become available as the grid’s carbon intensity generally has a diurnal pattern. To evaluate the impact of job length, we varied the job length from 6 hours to 96 hours and used our <sub>Carbon</sub> <sub>Advisor</sub> to analyze the estimated carbon savings. Figure 14 shows the carbon savings of diferent policies, against a <sub>carbon-agnostic</sub> baseline, for the ?? -body(?? =100k) MPI workload when?? <sub>=</sub> 1.5<sub>×</sub>??. <sub>CarbonScaler</sub> outperforms <sub>suspend-resume</sub> and <sub>carbon-agnostic</sub> over various job lengths. The carbon savings increase with job length since there are more low-carbon time slots to choose from, providing opportunities for greater savings. Overall, <sub>CarbonScaler</sub> achieves 30% more savings than <sub>suspend-resume</sub> for long batch jobs.

<sub>Efect</sub> <sub>of</sub> <sub>Cluster</sub> <sub>Size.</sub> Our experiments thus far have used a lower bound of 1 server $( m = 1 )$ and an upper bound of 8 servers $( M = 8 )$ for workloads due to cluster size and cloud cost constraints.

![](images/781fe49f73f0977741a2985d939e32eed8115e8611d079c2c5039418a41ffffa.jpg)  
(a) Workloads (Fig. 9)

![](images/b1f60c224d5d94b8352e989ff361263815f4fd0fb4e4500e8779eb2c56147bfd.jpg)  
(b) Deadlines (Fig. 13)

![](images/eba7dc5bf06575bd834ab7161f91b7ef47d3966bcd9ec008a66afe1699ad3939.jpg)  
(c) Carbon-Cost tradeof  
Fig. 16. Monetary cost overhead of CarbonScaler over carbon-agnostic execution for diferent scenarios.

However, larger batch jobs execute on larger clusters, with larger ?? and ??. For example, certain HPC and ML training applications run on tens or even hundreds of servers in the cloud [18, 35] and can only be executed on a large number of servers $m \gg 1$ . To evaluate the eficacy of <sub>CarbonScaler</sub> for large clusters, we extrapolated the marginal capacity curve for the current ?? -body(?? <sub>=</sub> 100??<sub>)</sub> job. Then, we use <sub>Carbon</sub> <sub>Advisor</sub> to estimate how carbon savings change when running progressively bigger jobs on increasing cluster sizes while keeping the job length unchanged at 24hrs.

Figure 15(a) compares the carbon consumption across cluster sizes. The figure shows that, although the savings percentages diminish with larger cluster sizes as they are less dynamic, the absolute carbon savings increase. Figure 15(b) depicts the relation between policies aside from the size-dependent carbon consumption. As shown, <sub>CarbonScaler</sub> can obtain 30–42% additional savings than <sub>carbon-agnostic</sub>, and <sub>suspend-resume</sub> achieves the same savings of 17% over <sub>carbon-agnostic</sub> policy across all cluster sizes. The <sub>suspend-resume</sub> achieves this static saving since it suspends the job in the same high carbon periods regardless of the cluster size. Lastly, the figure shows that the savings diference between <sub>CarbonScaler</sub> and <sub>suspend-resume</sub> reduces as the cluster size increases since the marginal capacity curve shows diminishing gains for larger cluster sizes.

Key Takeaway. CarbonScaler exploits temporal flexibility to outperform suspend-resume policy across regions with diferent carbon costs and over diferent job lengths, completion times, and cluster sizes.

## 5.5 Monetary Cost Overhead

As discussed in Section 3.4, for the workloads with diminishing marginal capacity curves, <sub>CarbonScaler</sub> can potentially incur extra cloud costs quantified as the additional cloud computehours needed compared to the <sub>carbon-agnostic</sub> policy. In Figure 16, we present the efect of workload scalability, extended completion time, and degree of flexibility on the added cost of <sub>CarbonScaler</sub>. Figure 16(a) shows that the highly scalable workloads such as ?? -body (?? <sub>=</sub> 100) and ML (ResNet18) that yield the highest savings under <sub>CarbonScaler</sub> cost only 5-10% higher than a <sub>carbon-agnostic</sub> policy. The less scalable workloads incur higher costs for the same carbon savings. It is worth noting that the <sub>static-scale</sub> would also incur similar overheads as the cost depends on the scaling properties of the workload [29]. For ML (ResNet18) workload, Figure 13 demonstrates that as the job completion time increases, the added cost increases up to 7% and then plateaus with a further increase in completion time. This is because, at higher job completion times, there are more low-carbon slots available where <sub>CarbonScaler</sub> can scale higher. Importantly, across both scenarios, the added cost never increases beyond 18%. Finally, in figure 16 (c), we leverage <sub>Carbon</sub> <sub>Advisor</sub> to highlight the tradeof between carbon savings and cost overheads across diferent degrees of flexibility for ML (ResNet18). The figure shows that there exists a flexibility degree that yields the highest carbon savings of almost 9% per each % of added cost.

Key Takeaway. The cloud cost overhead of CarbonScaler is small but depends on the scalability properties of the workloads (the higher the scalability, the lower the cost overhead). Furthermore, there may be a sweet spot across various dimensions that yields the highest savings per unit of added cost.

![](images/25bc700490872b4ceff5a8a175a9d921f32b6fea0833fcdcd337707cf7484b6c.jpg)  
Fig. 17. Carbon consumption (kg) and savings (%), for an ML (ResNet18) job, where <sup>??</sup> = <sup>??</sup>, across geographical regions (carbon intensity increases from left to right).

![](images/9b859314da8a628918d25911418c7650443147cbf81ae2ffe1b374078fe089c2.jpg)  
(a)

![](images/1b60fb4d07aba5409595090f05361ac20e2ece2fc8d9e0919c0be8c0be1a8cf0.jpg)  
(b)  
Fig. 18. Efect of variation (a) and location (b) on realized savings for an ML (ResNet18) job $( T = l = 2 4 h r s )$

## 5.6 Impact of Carbon Cost Dynamics

Since achievable carbon savings depend on the temporal characteristics of the carbon costs within a cloud region, which significantly vary across regions, we next evaluate the impact of regions and carbon intensity variability on carbon savings.

<sub>Carbon</sub> <sub>Savings</sub> <sub>Across</sub> <sub>Cloud</sub> <sub>Regions.</sub> To assess the efect of regions on carbon savings, we use <sub>Carbon</sub> <sub>Advisor</sub> to compute carbon savings achieved by a 24hrs long ML (ResNet18) job, with ?? <sub>=</sub> ??, across 16 diferent AWS cloud regions. Figure 17 provides several insights about the average relative and absolute carbon savings compared to the <sub>carbon-agnostic</sub> policy. First, the figure shows that the carbon emissions of the same job can vary by an <sub>order</sub> <sub>of</sub> <sub>magnitude</sub> depending on which cloud region is used to execute it. Second, <sub>CarbonScaler</sub> is able to achieve significant carbon savings (in %) for most regions, with a median and average savings of 16% and 19%, respectively. So long as the carbon costs exhibit diurnal variations, <sub>CarbonScaler</sub> can reduce the job’s emissions over the <sub>carbon-agnostic</sub> policy regardless of whether it runs in a low or high carbon region. Finally, Figure 17 shows that India’s ( ) region is an exception: while it has high absolute carbon costs, its low hourly variations prevent <sub>CarbonScaler</sub> from generating much savings.

<sub>Efect</sub> <sub>of</sub> <sub>Variability.</sub> As noted earlier, regions with variable carbon cost tend to generate higher carbon savings. This is because the high variations in such regions provide more low carbon periods to exploit for carbon reductions. We use the coeficient of variation, standard deviation divided by mean, as a metric to quantify the variability of the region. Figure 18(a) shows the carbon savings, for each starting point of the year, for a 24hrs ML (ResNet18) job with no excess time for Ontario, Canada using <sub>Carbon</sub> <sub>Advisor</sub>. The carbon savings are highly correlated with the coeficient of variation, with a Pearson coeficient of 0.82. However, even a highly variable location like Ontario has a small fraction of hours when savings are less than 20%, a fraction that will vary depending on the region. Figure 18(b) presents the distribution of carbon savings and compare regions with diferent average coeficient of variation. Note that the curves on the right are better as they lead to high carbon savings most of the time. The regions represented by the curves are also strictly ordered by their coeficient of variation, which means that a coeficient of variation can be used to rank regions, when mean carbon cost is comparable, for their carbon saving potential.

Key Takeaway. CarbonScaler achieves carbon savings for most cloud regions regardless of their absolute carbon cost. In addition, higher diurnal variations in carbon cost translate to greater savings.

## 5.7 Robustness to Errors

In prior experiments, we assumed that the carbon forecasts are perfect and applications are profiled on an environment similar to what they eventually run on, yielding highly accurate marginal capacity curves. However, in practice, these assumptions may not always be true, and we evaluate the impact of deviation from the ground-truth for these two factors.

![](images/c0307ef6b2717ed8688b52cebac4da70aaf20d6851619783bef70c7f5d97e338.jpg)  
Fig. 19. Illustrative example of error in carbon forecasts for an <sup>??</sup> -Body $( N = 1 0 0 k )$ workload.

<sub>Efect</sub> <sub>of</sub> <sub>Carbon</sub> <sub>Forecast</sub> <sub>Error.</sub> Carbon forecasts are easily available through online tools and services such as [43, 44, 71], with a reported mean accuracy of 6.4%. More importantly, the fidelity of <sub>CarbonScaler</sub> does not depend on the actual magnitude of the carbon forecast and instead relies on correctly identifying the hills (high carbon slots) and valleys (low carbon slots) in the carbon trace, which can be predicted with high accuracy. To illustrate this efect, we generate carbon traces with forecast errors of up to 30% by adding a uniformly random error in the range of -X% to X% for an error of X%. Figure 19(top) shows an example ground-truth and forecasted (X = 30% error) carbon cost time-series. While an erroneous forecast deviates from ground-truth at certain points, it still retains the hills and valleys, leading to harmonious schedules in both cases.

To further quantify the efect of forecast errors, we compare the performance of <sub>CarbonScaler</sub> with perfect carbon forecast to an error-agnostic variant of <sub>CarbonScaler</sub> that is oblivious to forecast errors, and <sub>CarbonScaler</sub> that recompute the schedule when the realized forecast error exceeds 5%. Figure 20 shows the carbon overhead over the perfect forecast scenario. The results highlight the resiliency of <sub>CarbonScaler</sub> to forecast errors, as a 30% forecast error resulted in merely 4% added carbon at $9 5 ^ { t h }$ percentile.

<sub>Efect</sub> <sub>of</sub> <sub>Profiling</sub> <sub>Errors.</sub> The marginal capacity curves generated by the <sub>Carbon</sub> <sub>Profiler</sub> can become erroneous if the environment characteristics, such as network bottlenecks [36], change during the execution. This can impact the carbon savings of a given job if scaling behavior changes due to deviation from actual marginal capacity curves. To evaluate the efect of erroneous profiles, we added uniformly random errors to the marginal capacity curves and measured the carbon consumption using <sub>Carbon</sub> <sub>Advisor</sub>. Figure 21 shows the carbon overhead over <sub>CarbonScaler</sub> with accurate marginal capacity profiles. The results show that the magnitude of error depends on the application power consumption and scalability behavior, e.g., the ?? -body job is less afected by errors as it has low power consumption and scales somewhat linearly. Additionally, we only show the results for the initial phase of execution, where errors persist. <sub>CarbonScaler</sub>’s error-handling mechanism of updating marginal capacity curves, when they deviate, corrects the errors, and net overhead over the entire execution of the workload would be considerably small.

Impact of Server Procurement Denial. <sup>Since</sup> CarbonScaler <sup>dynamically</sup> <sup>scales</sup> <sup>each</sup> <sup>job</sup> <sup>inde</sup> pendently, similar to cloud autoscalers, many jobs may request cloud servers during low carbon periods, creating a high demand for servers during such periods. Thus, jobs may end up competing with one another for additional servers, which can cause the cloud platform to deny some requests for new instances, to avoid failures. For example, it is not uncommon to see denials for popular GPU instances during work hours, even in the absence of carbon scaling. To evaluate the efect of such denials, we run a 24hr job with 48hr completion time, $( T = 2 \times l )$ , with diferent probabilities of random procurement denials. In such cases <sub>CarbonScaler</sub> keeps retrying its request and then recomputes the schedule to mitigate the impact of denials on job completion. Figure 22 illustrates that the carbon overhead, compared to a no-denial scenario, increases as the denial percentage increases. The overhead’s magnitude depends on a job’s scalability behavior. For example, a highly scalable ?? -body job incurs 5% overhead, while a non-scalable ML job (VGG16) incurs up to 15% overhead compared to the best schedule.

![](images/801f95d7c5420977d0f99d896360f18a943a0510bbbe3b0bbad39489ef94aba0.jpg)  
CO<sub>2</sub> forecast error (%)

![](images/5c3bd610742a74374decc7a2d180e445623c749c9b24a0b32cf8dbcfec3bd2e9.jpg)  
Profile estimation error (%)

![](images/f9a361d42a595c0b64bebd56c251086a3bc61895abdba2dc5548494a5ec6974a.jpg)  
Fig. 20. Efect of carbon forecast er- Fig. 21. Efect of errors in profiled Fig. 22. Carbon overhead of the rors for an <sup>??</sup> -body (<sup>??</sup> =100k) job. marginal capacity curves. server procurement denial.

Key Takeaway. CarbonScaler only depends on carbon cost trends, and simple recomputations achieve savings comparable to the perfect estimation. The potential overheads of profiling error can be overcome by updating marginal capacity curves as they start to deviate. Finally, resource availability can impact the achievable savings, but the magnitude depends on the scalability properties of the workloads.

## 5.8 System Overheads

<sub>CarbonScaler</sub> incurs two types of systemic overheads in its execution. First, <sub>CarbonScaler</sub> incurs switching overhead, which is the overhead of scaling or suspending, as the number of resources changes over time. The scaling overhead is a function of the application state size (e.g., the number of parameters in ML models). Although <sub>CarbonScaler</sub> did not account for this overhead in its scheduling decisions, in our experiments, the scaling overhead was between 20-40 seconds. We note that <sub>suspend-resume</sub> incurs similar overheads as the state is scale-independent. The second source of overhead is the time needed by <sub>Carbon</sub> <sub>Profiler</sub> to obtain marginal capacity curves. As mentioned in §4.1, profiling time can be configured using profile duration ?? at each allocation level, and granularity ?? of allocations profiled. We use ?? <sub>=</sub> 1 minute, and ?? <sub>=</sub> 1, i.e., we profile across all possible allocation levels. Thus, the one-time profiling took 40 minutes, where each workload in Figure 9 took only 8 minutes.

Key Takeaway. CarbonScaler’s systemic overheads are small, configurable, and generally occur once.

## 6 DISCUSSION

<sub>CarbonScaler</sub> takes an application-centric approach to reduce the carbon footprint of cloud workloads. While addressing potential second-order efects is outside the scope of <sub>CarbonScaler</sub>, we discuss the implications for cloud operators when customers operate in a carbon-aware manner.

<sub>Capacity</sub> <sub>Constraints.</sub> Cloud operators have diferent optimization goals and constraints than their tenants. The conflicts are handled through the <sub>pay-as-you-go</sub> pricing model, which hides the underlying constraints, objectives, and potential second-order efects from customers. Additionally, datacenters are designed for peak demand to handle workloads that exhibit diurnal patterns, where they increase at certain times of the day and are correlated between customers. As a result, they typically have low utilization, usually between 40-60% [7, 8, 62], providing enough headroom to handle peaks from carbon-aware demand shifting. Carbon savings are achieved by aligning the demand with the carbon intensity. However, as more and more customers try to increase their carbon eficiency, the compute and power demand will increase at certain periods beyond the datacenter capacity. This will require cloud operators to handle such spikes by adopting a dynamic pricing model, enforce fair sharing limits, or by denying resource acquisition requests if needed. The modeling of such dynamic pricing and carbon-aware fair shares and how <sub>CarbonScaler</sub> will respond is outside the scope of this paper. For acquisition denials, we demonstrate that <sub>CarbonScaler</sub> is robust to such denials (see Figure 22).

Datacenter Energy Optimizations. CarbonScaler <sup>is</sup> <sup>an</sup> <sup>application-centric</sup> <sup>approach</sup> <sup>to</sup> <sup>reducing</sup> the carbon footprint of executing workloads in the cloud, which can be used by organizations that are setting ambitious goals for reducing the carbon footprint of their operations [12, 33, 67]. While cloud customers’ behavior impacts the cloud datacenter operation, the pay-as-you-go model hides that from the customer. Internally, cloud operators can deploy several optimizations, such as forecasting demand and putting servers into a deep sleep or turning them of completely, ofering resources at a discounted price, and procuring location-specific renewable energy. Many cloud operators are already experimenting with such optimizations. Examples include variable capacity computing at Google [59, 75], spot VMs ofered by AWS [20], and 24/7 renewable energy procurements by all the major cloud providers [21, 28]. Considering the impact of such operator-side optimizations is outside the scope of a customer-oriented approach like <sub>CarbonScaler</sub>.

<sub>Holistic</sub> <sub>Emissions</sub> <sub>Reduction.</sub> A datacenter’s carbon emissions arise from manufacturing hardware like servers (embodied emissions) and operating these resources (operational emissions). While both emission types are important, they require distinct optimizations [9]. For instance, cutting embodied emissions involves extending device lifespan and choosing low operational carbon suppliers [2, 42]. How cloud operators and customers leverage such techniques for optimizing embodied carbon is outside the scope of this paper. Instead, in this paper, we focus on reducing operational carbon emissions by modulating how and when we execute our workloads.

## 7 RELATED WORK

<sub>Batch</sub> <sub>Scheduler.</sub> HPC schedulers have focused on achieving high utilization and performance eficiency. Traditional batch schedulers such as Slurm [74] and Torque [66] focus on fixed-sized clusters and employed multiple policies to optimize turnaround [16], utilization [60], and energy[27]. Recent schedulers such as Borg, Kubernetes, and Mesos [32, 41] have utilized the elasticity of cloud resources while considering the monetary cost. In both cases, sustainability concerns have influenced operational and scheduling decisions, leading to optimization objectives such as reducing carbon consumption. In the rest of this section, we discuss recent research on carbon-aware scheduling.

<sub>Energy</sub> <sub>Accounting.</sub> Reporting carbon consumption depends on a cluster’s ability to account for an individual tenant’s energy consumption. <sub>CarbonScaler</sub> currently focuses on CPU and GPU resources as they are 1) highly correlated with total energy consumption [39], 2) software tools such as (RAPL) [17] and <sub>nvidia-smi</sub> [53], are available on modern processors and GPUs. However, our accounting methods can be generalized to other server resources as shown in [11, 15, 23, 39]. Such accounting techniques are vital for holistic carbon optimization since cloud service providers such as Microsoft [50], and AWS [49] are starting to ofer basic carbon management capabilities.

<sub>Temporal</sub> <sub>Shifting.</sub> Temporal shifting by delaying execution of batch jobs from high carbon slots to lower carbon slots has been explored in [19, 59, 73]. The Let’s wait-a-while [73] approach uses temporal shifting to reduce the carbon footprint of batch workloads using threshold and deadline-based methods and by exploiting overnight or weekend hours to extract savings. In [19], the authors highlight the implications of scheduling AI workloads in diferent settings and suggest temporal shifting to minimize the carbon footprint. Finally, in [59], the authors employ a virtual limit on resources when carbon cost is high to force the scheduler to shift workloads to lower carbon periods. As noted in §1, a limitation of temporal shifting approaches is that they delay job completion times and may also require users to specify deadlines for jobs. In contrast, <sub>CarbonScaler</sub> employs resource elasticity to scale and complete jobs in a timely manner and can additionally exploit temporal flexibility whenever available.

<sub>Spatial</sub> <sub>Shifting.</sub> Prior work has studied spatial shifting to select the region with the lowest carbon footprint to execute newly arriving jobs. The authors of [2, 19, 68, 76] explore the spatial selection to achieve lower carbon cost. The authors of [2] explore data center and power upgrade plans to allow more carbon-eficient execution, while [19, 68] explore cloud data center regions and potential carbon savings. Lastly, [76] exploits migration to avoid energy curtailment. While we study the benefits of using diferent geographic regions to run carbon scaling jobs in §5.6, a full analysis of combining spatial shifting with carbon scaling is outside the scope of this paper.

## 8 CONCLUSION

Many compute-intensive cloud workloads, such as ML training and scientific computations, have inherent resource elasticity and temporal flexibility that can be leveraged to optimize carbon emission reductions. To exploit this opportunity, we propose <sub>CarbonScaler</sub> that judiciously scales up or down an application, based on its scalability behavior and carbon cost, to minimize its carbon emissions. We implement <sub>CarbonScaler</sub> as a cloud-based autoscaler implemented using Kubernetes and a simulation-based advisory tool to facilitate pre-deployment analysis. We demonstrate the eficacy of <sub>CarbonScaler</sub> in reducing carbon emissions for various workloads, job configurations, and cloud regions. We demonstrated that using real-world machine learning training and MPI jobs on a commercial cloud platform, <sub>CarbonScaler</sub> can yield i) 51% carbon savings over carbonagnostic execution, ii) 37% over a suspend-resume policy, and iii) 8% over the best static scaling policy. In the future, we plan to extend <sub>CarbonScaler</sub> into a cluster-wide scheduler to address the challenges of resource heterogeneity, resource pressure, priorities, and power management.

## ACKNOWLEDGMENTS

We thank the anonymous reviewers and our shepherd Anshul Gandhi, for their valuable comments, which improved the quality of this paper. This research is supported by NSF grants 2211302, 2211888, 2213636, 2105494, US Army contract W911NF-17-2-0196, VMware, and Amazon Web Services.

## REFERENCES

[1] J. Sverre Aarseth. 1985. 12 - Direct Methods for N-Body Simulations. In <sub>Multiple</sub> <sub>Time</sub> <sub>Scales</sub>. Academic Press, 377–418. https://doi.org/10.1016/B978-0-12-123420-1.50017-3

[2] Bilge Acun, Benjamin Lee, Fiodar Kazhamiaka, Kiwan Maeng, Udit Gupta, Manoj Chakkaravarthy, David Brooks and Carole-Jean Wu. 2023. Carbon Explorer: A Holistic Framework for Designing Carbon Aware Datacenters. In Proceedings of the 28th ACM International Conference on Architectural Support for Programming Languages and Operating <sub>Systems,</sub> <sub>Volume</sub> <sub>2</sub> (Vancouver, BC, Canada) <sub>(ASPLOS</sub> <sub>2023)</sub>. Association for Computing Machinery, New York, NY, USA, 118–132. https://doi.org/10.1145/3575693.3575754

[3] Gene M Amdahl. 1967. Validity of the Single Processor Approach to Achieving Large Scale Computing Capabilities. In Proceedings of the Spring Joint Computer Conference<sup>.</sup>

[4] Anders S. G. Andrae and Tomas Edler. 2015. On Global Electricity Usage of Communication Technology: Trends to 2030. <sub>Challenges</sub> 6, 1 (2015), 117–157. https://doi.org/10.3390/challe6010117

[5] Michael Armbrust, Tathagata Das, Aaron Davidson, Ali Ghodsi, Andrew Or, Josh Rosen, Ion Stoica, Patrick Wendell, Reynold Xin, and Matei Zaharia. 2015. Scaling Spark in the Real World: Performance and Usability. <sub>Proc.</sub> <sub>VLDB</sub> <sub>Endow.</sub> 8, 12 (aug 2015), 1840–1843. https://doi.org/10.14778/2824032.2824080

[6] AWS. 2022. AWS Auto Scaling. https://aws.amazon.com/autoscaling/.

<sup>[7]</sup> <sup>Luiz</sup> <sup>André</sup> <sup>Barroso</sup> <sup>and</sup> <sup>Urs</sup> <sup>Hölzle.</sup> <sup>2009.</sup> The Datacenter as a Computer: An Introduction to the Design of Warehouse-Scale <sub>Machines</sub>. Springer Nature, Europe. 189 pages.

[8] Noman Bashir, Nan Deng, Krzysztof Rzadca, David Irwin, Sree Kodak, and Rohit Jnagal. 2021. Take it to the Limit: Peak Prediction-driven Resource Overcommitment in Datacenters. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>Sixteenth</sub> <sub>European</sub> <sub>Conference</sub> <sub>on</sub> <sub>Computer</sub> <sub>Systems</sub> (Online Event, United Kingdom) <sub>(EuroSys</sub> <sub>’21)</sub>. Association for Computing Machinery, New York NY, USA, 556–573. https://doi.org/10.1145/3447786.3456259

[9] Noman Bashir, David Irwin, and Prashant Shenoy. 2023. On the Promise and Pitfalls of Optimizing Embodied Carbon. <sup>In</sup> Proceedings of the 2nd Workshop on Sustainable Computer Systems (HotCarbon)<sup>.</sup> <sup>ACM,</sup> <sup>New</sup> <sup>York,</sup> <sup>NY,</sup> <sup>USA,</sup> <sup>6</sup> <sup>pages.</sup>

[10] Noman Bashir, David Irwin, Prashant Shenoy, and Abel Souza. 2022. Sustainable Computing – Without the Hot Air. In HotCarbon: Workshop on Sustainable Computer Systems Design and Implementation<sup>.</sup> <sup>ACM,</sup> <sup>New</sup> <sup>York,</sup> <sup>NY,</sup> <sup>USA,</sup> <sup>7</sup> <sup>pages.</sup>

[11] Aurélien Bourdon, Adel Noureddine, Romain Rouvoy, and Lionel Seinturier. 2013. Powerapi: A Software Library to Monitor the Energy Consumed at the Process-level. <sub>ERCIM</sub> <sub>News</sub> (2013).

[12] Seán Boyle and Casey Junod. 2023. Accelerating our climate commitments on Earth Day. https://blog.twitter.com/en\_ us/topics/company/2022/accelerating-our-climate-commitments-on-earth-day.

[13] Ermao Cai, Da-Cheng Juan, Dimitrios Stamoulis, and Diana Marculescu. 2017. Neuralpower: Predict and Deploy Energy-eficient Convolutional Neural Networks. In <sub>Asian</sub> <sub>Conference</sub> <sub>on</sub> <sub>Machine</sub> <sub>Learning</sub>.

[14] A. Chien. 2021. Driving the Cloud to True Zero Carbon. <sub>Communication</sub> <sub>of</sub> <sub>the</sub> <sub>ACM</sub> 64, 2 (February 2021).

[15] Maxime Colmant, Mascha Kurpicz, Pascal Felber, Loïc Huertas, Romain Rouvoy, and Anita Sobe. 2015. Process-<sup>Level</sup> <sup>Power</sup> <sup>Estimation</sup> <sup>in</sup> <sup>VM-Based</sup> <sup>Systems.</sup> <sup>In</sup> Proceedings of the Tenth European Conference on Computer Systems (Bordeaux, France) <sub>(EuroSys</sub> <sub>’15)</sub>. Association for Computing Machinery, New York, NY, USA, Article 14, 14 pages. https://doi.org/10.1145/2741948.2741971

[16] Renato L.F. Cunha, Eduardo R. Rodrigues, Leonardo P. Tizzei, and Marco A.S. Netto. 2017. Job placement advisor based on turnaround predictions for HPC hybrid clouds. <sub>Future</sub> <sub>Generation</sub> <sub>Computer</sub> <sub>Systems</sub> 67 (2017), 35–46. https://doi.org/10.1016/j.future.2016.08.010

[17] Howard David, Eugene Gorbatov, Ulf R Hanebutte, Rahul Khanna, and Christian Le. 2010. RAPL: Memory Power <sup>Estimation</sup> <sup>and</sup> <sup>Capping.</sup> <sup>In</sup> ACM/IEEE International Symposium on Low-Power Electronics and Design (ISLPED)

[18] Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. 2019. BERT: Pre-training of Deep Bidirectional <sup>Transformers</sup> <sup>for</sup> <sup>Language</sup> <sup>Understanding.</sup> <sup>In</sup> Proceedings of the 2019 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies, Volume 1 (Long and Short Papers)<sup>.</sup> <sup>Association</sup> for Computational Linguistics, Minneapolis, Minnesota, 4171–4186. https://doi.org/10.18653/v1/N19-1423

[19] Jesse Dodge, Taylor Prewitt, Remi Tachet des Combes, Erika Odmark, Roy Schwartz, Emma Strubell, Alexandra Sasha Luccioni, Noah A. Smith, Nicole DeCario, and Will Buchanan. 2022. Measuring the Carbon Intensity of AI in Cloud <sup>Instances.</sup> <sup>In</sup> 2022 ACM Conference on Fairness, Accountability, and Transparency (FAccT ’22)<sup>.</sup>

[20] EC2 2022. Amazon EC2 Spot Instances. https://aws.amazon.com/ec2/spot/.

[21] EPA. 2023. Green Power Partnership Long-term Contracts. https://www.epa.gov/greenpower/green-powerpartnership-long-term-contracts

[22] Awi Federgruen and Henri Groenevelt. 1986. The Greedy Procedure for Resource Allocation Problems: Necessary and Suficient Conditions for Optimality. <sub>Oper.</sub> <sub>Res.</sub> 34, 6 (dec 1986), 909–918

[23] Guillaume Fieni, Romain Rouvoy, and Lionel Seinturier. 2020. SmartWatts: Self-Calibrating Software-Defined Power <sup>Meter</sup> <sup>for</sup> <sup>Containers.</sup> <sup>In</sup> 2020 20th IEEE/ACM International Symposium on Cluster, Cloud and Internet Computing <sub>(CCGRID)</sub>. 479–488. https://doi.org/10.1109/CCGrid49817.2020.00-45

[24] Message P Forum. 1994. <sub>MPI:</sub> <sub>A</sub> <sub>Message-Passing</sub> <sub>Interface</sub> <sub>Standard</sub>. Technical Report. USA.

[25] William Fox, Devarshi Ghoshal, Abel Souza, Gonzalo P. Rodrigo, and Lavanya Ramakrishnan. 2017. E-HPC: A Library for Elastic Resource Management in HPC Environments. In Proceedings of the 12th Workshop on Workflows in Support <sub>of</sub> <sub>Large-Scale</sub> <sub>Science</sub> (Denver, Colorado) <sub>(WORKS</sub> <sub>’17)</sub>. Association for Computing Machinery, New York, NY, USA, Article 1, 11 pages. https://doi.org/10.1145/3150994.3150996

[26] Anshul Gandhi, Mor Harchol-Balter, Ram Raghunathan, and Michael A. Kozuch. 2012. AutoScale: Dynamic, Robust Capacity Management for Multi-Tier Data Centers. <sub>ACM</sub> <sub>Trans.</sub> <sub>Comput.</sub> <sub>Syst.</sub> 30, 4, Article 14 (nov 2012), 26 pages. https://doi.org/10.1145/2382553.2382556

[27] Saurabh Kumar Garg, Chee Shin Yeo, Arun Anandasivam, and Rajkumar Buyya. 2011. Environment-conscious scheduling of HPC applications on distributed cloud-oriented data centers. <sub>J.</sub> <sub>Parallel</sub> <sub>and</sub> <sub>Distrib.</sub> <sub>Comput.</sub> 71, 6 (2011), 732–749.

[28] Google. 2022. Google’s Green PPAs: What, How, and Why. https://static.googleusercontent.com/media/www.google. com/en//green/pdfs/renewable-energy.pdf.

[29] Walid A Hanafy, Roozbeh Bostandoost, Noman Bashir, David Irwin, Mohammad Hajiesmaili, and Prashant Shenoy. 2023. The War of the Eficiencies: Understanding the Tension between Carbon and Energy Optimization. In <sub>Proc.</sub> <sub>2nd</sub> ACM Workshop on Hot Topics in Sustainable Computing Systems (HotCarbon’23)<sup>.</sup>

[30] Fiona Harvey. 2021. The Guardian, Major Climate Changes Inevitable and Irreversible – IPCC’s Starkest Warning Yet. https://www.theguardian.com/science/2021/aug/09/humans-have-caused-unprecedented-and-/irreversiblechange-to-climate-scientists-warn.

[31] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. 2016. Deep Residual Learning for Image Recognition. In Proceedings of the IEEE conference on computer vision and pattern recognition (CVPR)<sup>.</sup>

[32] Benjamin Hindman, Andy Konwinski, Matei Zaharia, Ali Ghodsi, Anthony D. Joseph, Randy Katz, Scott Shenker, and Ion Stoica. 2011. Mesos: A Platform for Fine-Grained Resource Sharing in the Data Center. In <sub>USENIX</sub> <sub>Symposium</sub> <sub>on</sub> <sub>Networked</sub> <sub>Systems</sub> <sub>Design</sub> <sub>and</sub> <sub>Implementation</sub> <sub>(NSDI)</sub>. USENIX Association, Boston, MA, 14. https://www.usenix.org conference/nsdi11/mesos-platform-fine-grained-resource-sharing-data-center

[33] VMware Inc. 2023. Journey to Net Zero. https://www.vmware.com/company/net-zero.html.

[34] World Resource Institute. 2022. <sub>GreenHouseGas</sub> <sub>Protocol</sub>. https://ghgprotocol.org/

[35] Sam Adé Jacobs, Nikoli Dryden, Roger Pearce, and Brian Van Essen. 2017. Towards Scalable Parallel Training of Deep <sup>Neural</sup> <sup>Networks.</sup> <sup>In</sup> Proceedings of the Machine Learning on HPC Environments (MLHPC’17)<sup>.</sup>

[36] Virajith Jalaparti, Peter Bodik, Ishai Menache, Sriram Rao, Konstantin Makarychev, and Matthew Caesar. 2015. Network-Aware Scheduling for Data-Parallel Jobs: Plan When You Can. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>2015</sub> <sub>ACM</sub> <sub>Conference</sub> <sub>on</sub> Special Interest Group on Data Communication <sup>(London,</sup> <sup>United</sup> <sup>Kingdom)</sup> (SIGCOMM ’15)<sup>.</sup> <sup>Association</sup> <sup>for</sup> <sup>Computing</sup> Machinery, New York, NY, USA, 407–420. https://doi.org/10.1145/2785956.2787488

[37] Nicola Jones. 2018. How to Stop Data Centres from Gobbling Up the World’s Electricity. <sub>Nature</sub> (2018).

[38] Daniel Justus, John Brennan, Stephen Bonner, and Andrew Stephen McGough. 2018. Predicting the Computational <sup>Cost</sup> <sup>of</sup> <sup>Deep</sup> <sup>Learning</sup> <sup>Models.</sup> <sup>In</sup> 2018 IEEE International Conference on Big Data (Big Data)<sup>.</sup>

[39] Aman Kansal, Feng Zhao, Jie Liu, Nupur Kothari, and Arka A. Bhattacharya. 2010. Virtual Machine Power Metering <sup>and</sup> <sup>Provisioning.</sup> <sup>In</sup> Proceedings of the 1st ACM Symposium on Cloud Computing <sup>(Indianapolis,</sup> <sup>Indiana,</sup> <sup>USA)</sup> (SoCC <sub>’10)</sub>. Association for Computing Machinery, New York, NY, USA, 39–50. https://doi.org/10.1145/1807128.1807136

[40] Kubeflow. 2022. Kubeflow: The Machine Learning Toolkit for Kubernetes. https://www.kubeflow.org/. Accessed: 2022-10-03.

[41] Kubernetes. 2022. Kubernetes: Production-grade Container Orchestration. https://kubernetes.io/. Accessed: 2022-10-03.

[42] Baolin Li, Siddharth Samsi, Vijay Gadepally, and Devesh Tiwari. 2023. Sustainable HPC: Modeling, Characterization, and Implications of Carbon Footprint in Modern HPC Systems. arXiv:2306.13177 [cs.DC]

[43] Diptyaroop Maji, Prashant Shenoy, and Ramesh K. Sitaraman. 2022. CarbonCast: Multi-Day Forecasting of Grid <sup>Carbon</sup> <sup>Intensity.</sup> <sup>In</sup> Proceedings of the 9th ACM International Conference on Systems for Energy-Eficient Buildings, <sub>Cities,</sub> <sub>and</sub> <sub>Transportation</sub> (Boston, Massachusetts) <sub>(BuildSys</sub> <sub>’22)</sub>. Association for Computing Machinery, New York, NY, USA, 198–207. https://doi.org/10.1145/3563357.3564079

[44] Diptyaroop Maji, Ramesh K. Sitaraman, and Prashant Shenoy. 2022. DACF: Day-Ahead Carbon Intensity Forecasting of <sup>Power</sup> <sup>Grids</sup> <sup>Using</sup> <sup>Machine</sup> <sup>Learning.</sup> <sup>In</sup> Proceedings of the Thirteenth ACM International Conference on Future Energy Systems (e-Energy’22)<sup>.</sup>

[45] Electricity Maps. 2022. Electricity Map. https://www.electricitymap.org/map.

[46] Eric R. Masanet, Arman Shehabi, Nuoa Lei, Sarah J. Smith, and Jonathan G. Koomey. 2020. Recalibrating Global Data Center Energy-use Estimates. <sub>Science</sub> (2020)

[47] Valérie Masson-Delmotte, Panmao Zhai, Anna Pirani, Sarah L Connors, Clotilde Péan, Sophie Berger, Nada Caud, Yang <sup>Chen,</sup> <sup>Leah</sup> <sup>Goldfarb,</sup> <sup>Melissa</sup> <sup>I</sup> <sup>Gomis,</sup> <sup>et</sup> <sup>al. 2021.</sup> Summary for Policymakers. In: Climate Change 2021: The Physical Science Basis. Contribution of Working Group I to the Sixth Assessment Report of the Intergovernmental Panel on Climate <sub>Change</sub>. Technical Report. United Nation Intergovernmental Panel on Climate Change (IPCC).

[48] META. 2022. How We’re Helping Fight Climate Change. https://about.fb.com/news/2021/06/2020-sustainabilityreport-how-were-helping-fight-climate-change/.

[49] Microsoft. 2022. AWS Customer Carbon Footprint Tool. https://aws.amazon.com/blogs/aws/new-customer-carbonfootprint-tool/.

[50] Microsoft. 2022. Microsoft Carbon accouting tool. https://www.microsoft.com/en-us/sustainability/emissions-impactdashboard.

[51] Microsoft. 2022. Microsoft is Changing the Way It Buys Renewable Energy. https://www.theverge.com/2021/7/14/ 22574431/microsoft-renewable-energy-purchases.

[52] Fereydoun Farrahi Moghaddam, Reza Farrahi Moghaddam, and Mohamed Cheriet. 2014. Carbon-aware Distributed Cloud: Multi-level Grouping Genetic Algorithm. <sub>Cluster</sub> <sub>Computing</sub> (2014)

[53] NVIDIA. 2022. Manage and Monitor GPUs in Cluster Environments. https://developer.nvidia.com/dcgm. Accessed: 2022-10-08.

[54] Yosuke Oyama, Akihiro Nomura, Ikuro Sato, Hiroki Nishimura, Yukimasa Tamatsu, and Satoshi Matsuoka. 2016. Predicting Statistics of Asynchronous SGD Parameters for a Large-scale Distributed Deep Learning System on GPU <sup>Supercomputers.</sup> <sup>In</sup> 2016 IEEE International Conference on Big Data (Big Data)<sup>.</sup>

[55] Adam Paszke, Sam Gross, Francisco Massa, Adam Lerer, James Bradbury, Gregory Chanan, Trevor Killeen, Zeming Lin, Natalia Gimelshein, Luca Antiga, Alban Desmaison, Andreas Kopf, Edward Yang, Zachary DeVito, Martin Raison, Alykhan Tejani, Sasank Chilamkurthy, Benoit Steiner, Lu Fang, Junjie Bai, and Soumith Chintala. 2019. PyTorch: An Imperative Style, High-Performance Deep Learning Library. In <sub>Advances</sub> <sub>in</sub> <sub>Neural</sub> <sub>Information</sub> <sub>Processing</sub> <sub>Systems</sub> (NIPS’19)<sup>.</sup>

[56] Ziqian Pei, Chensheng Li, Xiaowei Qin, Xiaohui Chen, and Guo Wei. 2019. Iteration Time Prediction for CNN in Multi-GPU Platform: Modeling and Analysis. <sub>IEEE</sub> <sub>Access</sub> (2019).

[57] Yanghua Peng, Yixin Bao, Yangrui Chen, Chuan Wu, and Chuanxiong Guo. 2018. Optimus: An Eficient Dynamic Resource Scheduler for Deep Learning Clusters. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>Thirteenth</sub> <sub>EuroSys</sub> <sub>Conference</sub> (Porto, Portugal) <sub>(EuroSys</sub> <sub>’18)</sub>. Association for Computing Machinery, New York, NY, USA, Article 3, 14 pages. https://doi.org/10.1145 3190508.3190517

[58] Qi, Evan R. Sparks, and Ameet S. Talwalkar. 2017. Paleo: A Performance Model for Deep Neural Networks. In <sub>The</sub> International Conference on Learning Representations (ICLR’17)<sup>.</sup>

[59] Ana Radovanovic, Ross Koningstein, Ian Schneider, Bokan Chen, Alexandre Duarte, Binz Roy, Diyue Xiao, Maya Haridasan, Patrick Hung, Nick Care, Saurav Talukdar, Eric Mullen, Kendal Smith, Mariellen Cottman, and Walfredo Cirne. 2022. Carbon-Aware Computing for Datacenters. <sub>IEEE</sub> <sub>Transactions</sub> <sub>on</sub> <sub>Power</sub> <sub>Systems</sub> (2022), 1–1. https: //doi.org/10.1109/TPWRS.2022.3173250

[60] Albert Reuther, Chansup Byun, William Arcand, David Bestor, Bill Bergeron, Matthew Hubbell, Michael Jones, Peter Michaleas, Andrew Prout, Antonio Rosa, and Jeremy Kepner. 2018. Scalable system scheduling for HPC and big data. <sub>J.</sub> <sub>Parallel</sub> <sub>and</sub> <sub>Distrib.</sub> <sub>Comput.</sub> 111 (2018), 76–92. https://doi.org/10.1016/j.jpdc.2017.06.009

[61] Alexander Sergeev and Mike Del Balso. 2018. Horovod: Fast and Easy Distributed Deep Learning in TensorFlow. <sub>arXiv</sub> preprint arXiv:1802.05799 <sup>(2018).</sup>

[62] Arman Shehabi, Sarah Smith, Dale Sartor, Richard Brown, Magnus Herrlin, Jonathan Koomey, Eric Masanet, Nathaniel Horner, Inês Azevedo, and William Lintner. 2016. United States Data Center Energy Usage Report. (6 2016). https: //doi.org/10.2172/1372902

[63] Shaohuai Shi, Qiang Wang, and Xiaowen Chu. 2018. Performance Modeling and Evaluation of Distributed Deep <sup>Learning</sup> <sup>Frameworks</sup> <sup>on</sup> <sup>GPUs.</sup> <sup>In</sup> 2018 IEEE 16th Intl Conf on Dependable, Autonomic and Secure Computing, 16th Intl Conf on Pervasive Intelligence and Computing, 4th Intl Conf on Big Data Intelligence and Computing and Cyber Science and Technology Congress(DASC/PiCom/DataCom/CyberSciTech)<sup>.</sup> <sup>949–957. https://doi.org/10.1109/DASC/PiCom</sup> DataCom/CyberSciTec.2018.000-4

[64] Kubernetes SIGs. 2022. <sub>Kubernetes</sub> <sub>Metrics</sub> <sub>Server</sub>. Kubernetes SIGs. https://github.com/kubernetes-sigs/metrics-server

[65] Abel Souza, Noman Bashir, Jorge Murillo, Walid Hanafy, Qianlin Liang, David Irwin, and Prashant Shenoy. 2023. Ecovisor: A Virtual Energy System for Carbon-Eficient Applications. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>28th</sub> <sub>ACM</sub> <sub>International</sub> Conference on Architectural Support for Programming Languages and Operating Systems, Volume 2 <sup>(Vancouver,</sup> <sup>BC,</sup> Canada) <sub>(ASPLOS</sub> <sub>2023)</sub>. Association for Computing Machinery, New York, NY, USA, 252–265. https://doi.org/10.1145 3575693.3575709

<sup>[66]</sup> <sup>Garrick</sup> <sup>Staples.</sup> <sup>2006.</sup> <sup>TORQUE</sup> <sup>resource</sup> <sup>manager.</sup> <sup>In</sup> Proceedings of the 2006 ACM/IEEE conference on Supercomputing<sup>.</sup> ACM, New York, NY, USA, 8.

[67] Emma Stewart. 2023. Net Zero + Nature: Our Commitment to the Environment. https://about.netflix.com/en/news/netzero-nature-our-climate-commitment

[68] Thanathorn Sukprasert, Abel Souza, Noman Bashir, David Irwin, and Prashant Shenoy. 2023. Quantifying the Benefits of Carbon-Aware Temporal and Spatial Workload Shifting in the Cloud. arXiv:2306.06502 [cs.DC]

[69] Mingxing Tan and Quoc Le. 2019. EficientNet: Rethinking Model Scaling for Convolutional Neural Networks. In Proceedings of the 36th International Conference on Machine Learning (Proceedings of Machine Learning Research, Vol. 97)<sup>.</sup> PMLR, 6105–6114. https://proceedings.mlr.press/v97/tan19a.htm

[70] Muhammad Tirmazi, Adam Barker, Nan Deng, Md E. Haque, Zhijing Gene Qin, Steven Hand, Mor Harchol-Balter, <sup>and</sup> <sup>John</sup> <sup>Wilkes.</sup> <sup>2020.</sup> <sup>Borg:</sup> <sup>The</sup> <sup>next</sup> <sup>Generation.</sup> <sup>In</sup> Proceedings of the Fifteenth European Conference on Computer <sub>Systems</sub> (Heraklion, Greece) <sub>(EuroSys</sub> <sub>’20)</sub>. Association for Computing Machinery, New York, NY, USA, Article 30, 14 pages. https://doi.org/10.1145/3342195.3387517

[71] WattTime. 2022. WattTime. https://www.watttime.org/.

[72] Qizhen Weng, Wencong Xiao, Yinghao Yu, Wei Wang, Cheng Wang, Jian He, Yong Li, Liping Zhang, Wei Lin, and Yu Ding. 2022. MLaaS in the wild: Workload analysis and scheduling in Large-Scale heterogeneous GPU clusters. In <sub>19th</sub> USENIX Symposium on Networked Systems Design and Implementation (NSDI 22)<sup>.</sup> <sup>USENIX</sup> <sup>Association,</sup> <sup>945–960.</sup>

[73] Philipp Wiesner, Ilja Behnke, Dominik Scheinert, Kordian Gontarska, and Lauritz Thamsen. 2021. Let’s Wait Awhile: How Temporal Workload Shifting Can Reduce Carbon Emissions in the Cloud. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>22nd</sub> <sub>International</sub> <sub>Middleware</sub> <sub>Conference</sub> (Québec city, Canada) <sub>(Middleware</sub> <sub>’21)</sub>. Association for Computing Machinery, New York, NY, USA, 260–272. https://doi.org/10.1145/3464298.3493399

[74] Andy B Yoo, Morris A Jette, and Mark Grondona. 2003. Slurm: Simple Linux Utility for Resource Management. In Workshop on Job Scheduling Strategies for Parallel Processing<sup>.</sup> <sup>Springer,</sup> <sup>New</sup> <sup>York,</sup> <sup>NY,</sup> <sup>USA,</sup> <sup>44–60.</sup>

[75] Chaojie Zhang and Andrew A. Chien. 2021. Scheduling Challenges for Variable Capacity Resources. In <sub>Job</sub> <sub>Scheduling</sub> <sub>Strategies</sub> <sub>for</sub> <sub>Parallel</sub> <sub>Processing</sub>, Dalibor Klusáček, Walfredo Cirne, and Gonzalo P. Rodrigo (Eds.). Springer Internationa Publishing, Cham, 190–209.

[76] Jiajia Zheng, Andrew A. Chien, and Sangwon Suh. 2020. Mitigating Curtailment and Carbon Emissions through Load Migration between Data Centers. <sub>Joule</sub> 4, 10 (2020), 2208–2222. https://doi.org/10.1016/j.joule.2020.08.001

[77] Zhi Zhou, Fangming Liu, Yong Xu, Ruolan Zou, Hong Xu, John C.S. Lui, and Hai Jin. 2013. Carbon-Aware Load Balancing <sup>for</sup> <sup>Geo-distributed</sup> <sup>Cloud</sup> <sup>Services.</sup> <sup>In</sup> International Symposium on Modelling, Analysis and Simulation of Computer and <sub>Telecommunication</sub> <sub>Systems</sub>. IEEE, New York, NY, USA, 232–241. https://doi.org/10.1109/MASCOTS.2013.31

## A CARBONSCALER OPTIMALITY

The carbon scaling problem addressed by <sub>CarbonScaler</sub> is a marginal resource allocation problem, where greedily selecting the local optimum (maximum marginal capacity per unit carbon), as in Algorithm 1, yields the global optimum solution [22]. Consequently, the optimality of the <sub>Carbon</sub> <sub>Scaling</sub> <sub>Algorithm</sub> follows from the theoretical results of [22] and is shown below.

<sup>Theorem</sup> <sup>1.</sup> Consider a distributed batch job with a known monotonically decreasing marginal capacity curve, s.t. $M C _ { m } > M C _ { m + 1 } > . . > M C _ { M }$ . The job needs to finish work <sup>??</sup> , within <sup>??</sup> time slots with known carbon costs $c _ { 1 } , c _ { 2 } , . . . , c _ { n } ,$ respectively. Greedily selecting the slot <sup>??</sup> and scaling the job to <sup>??</sup> servers with the highest marginal capacity per unit carbon $M C _ { j } / { c _ { i } } ^ { 5 }$ , in each step, results in the lowest (optimal) amount of carbon consumption.

Proof. We prove Theorem 1 by contradiction. Let ?? be an optimal solution schedule that finishes work ?? and has a carbon cost $C _ { S }$ . The schedule ?? is constructed by allocating time slots and number of servers, until ?? is completed. The tuple $( i , j )$ denotes the ??-th time slot and the ??-th server allocated to the job. $M C _ { j }$ is the marginal work done when allocating the ??-th server, and $c _ { i }$ is the carbon cost used per server at time slot ??, where we assume perfect knowledge of both. The total carbon cost is $\begin{array} { r } { C _ { S } = \sum _ { i \in n } c _ { i } \times S [ i ] } \end{array}$ , where $S [ i ]$ is the used number of servers at time slot ??.

The tuple $( k , l )$ denotes the ??-th time slot and the ??-th server, with marginal capacity per unit carbon of $M C _ { l } / c _ { k }$ . Assume that there exists a time slot ?? and a number of servers $j ,$ where $M C _ { l } / c _ { k } >$ $M C _ { j } / c _ { i } , \mathrm { s . t . , } ( i , j ) \in S$ and $( k , l ) \notin S$ . We denote $S ^ { ' }$ as a new schedule, where we only switch the ??-th time slot and ?? -th server with ??-th time slot and the ??-th server, which has the higher marginal capacity per unit carbon. To ensure that the schedule $S ^ { ' }$ finishes work $W _ { i }$ , the amount of work $M C _ { j }$ must be incorporated into the new schedule. We denote $c _ { i }$ and $\gamma ,$ as the old carbon and new carbon costs to perform work $M C _ { j }$ , respectively. ?? is computed based on the relationship between ?? and ??, where:

$$
\gamma = \left\{ \begin{array}{l l} c _ {k} \cdot \frac {M C _ {j}}{M C _ {l}}, & \text { if   } l \leq j. \\ c _ {k} + (\frac {M C _ {j} - M C _ {l}}{M C _ {j}}) \cdot c _ {i}, & \text { otherwise   } (l > j). \end{array} \right.\tag{1}
$$

In the first case $( l \leq j ) , M C _ { l } \geq M C _ { j }$ and job will use part or all of the time slot ??. In the second case $( l > j ) , M C _ { l } < M C _ { j }$ . We perform $M C _ { l }$ work in time slot $k ,$ and run the overflow work, $M C _ { j } - M C _ { l }$ in time slot ??, utilizing $\frac { M \bar { C } _ { j } - M C _ { l } } { M C _ { j } }$ of time slot ?? and all of time slot ??.

Next, to show that $c _ { i } ~ > ~ \gamma$ and $( C _ { S } > C _ { S ^ { ' } } )$ , we consider both cases. In the first case, since $M C _ { l } / c _ { k } > M C _ { j } / c _ { i }$ then:

$$
c _ {i} > c _ {k} \cdot \frac {M C _ {j}}{M C _ {l}}\tag{2}
$$

$$
c _ {i} > \gamma\tag{3}
$$

In the second case, since $\begin{array} { r } { \frac { M C _ { l } } { c _ { k } } > \frac { M C _ { j } } { c _ { i } } } \end{array}$ , then:

$$
c _ {k} <   \frac {M C _ {l}}{M C _ {j}} \cdot c _ {i}\tag{4}
$$

By substituting $c _ { k }$ in case $2 { : }$

$$
\frac {M C _ {l}}{M C _ {j}} \cdot c _ {i} + (\frac {M C _ {j} - M C _ {l}}{M C _ {j}}) \cdot c _ {i} > \gamma\tag{5}
$$

$$
\frac {M C _ {l} \cdot c _ {i} + M C _ {j} \cdot c _ {i} - M C _ {l} \cdot c _ {i}}{M C _ {j}} > \gamma\tag{6}
$$

$$
c _ {i} > \gamma\tag{7}
$$

Therefore, carbon consumption of $S ^ { ' }$ , denoted as $C _ { S ^ { ' } } = C _ { S } - c _ { i }$ <sub>+</sub> ?? is less than carbon cost of $S \left( C _ { S } \right)$ since $c _ { i } > \gamma$ . Hence, ?? is not optimal, a contradiction. □

Received February 2023; revised October 2023; accepted October 2023