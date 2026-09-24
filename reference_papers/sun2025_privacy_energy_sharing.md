# Privacy-Preserving Energy Sharing Among Cloud Service Providers via Collaborative Job Scheduling

Yimeng Sun , Member, IEEE, Zhaohao Ding , Senior Member, IEEE, Yuejun Yan , Member, IEEE, Zhaoyang Wang, Payman Dehghanian , Senior Member, IEEE, and Wei-Jen Lee , Life Fellow, IEEE

Abstract—With the growing digitalization of the economy and society, the scale of energy consumption in cloud computing is continuously expanding. Leveraging the flexible scheduling characteristics of computing jobs, data centers operated by different cloud service providers can reduce their energy costs by spatiotemporally shifting jobs to periods and locations with lower energy prices. However, privacy concerns on critical operation information hinder such collaboration among different cloud service providers. In this paper, we propose a privacy-preserving federated reinforcement learning scheme for collaborative job scheduling to enable energy sharing among cloud service providers. First, we establish the collaborative energy management model via job transfer and computing resource allocation as a decentralized partially observable Markov decision process. Then, we develop a personalized federated reinforcement learning approach under a decentralized training with decentralized execution framework, where decisions are made adaptive to the heterogeneous environments of different cloud service providers while protecting their operation privacy. Finally, the real-world traces from Alibaba are used to illustrate and verify the effectiveness of the proposed scheme.

Index Terms—Cloud service provider, data center, federated reinforcement learning, job scheduling, demand side management.

## NOMENCLATURE

## Acronyms

<table><tr><td>CSP</td><td>Cloud service provider</td></tr><tr><td>FRL</td><td>Federated reinforcement learning</td></tr><tr><td>DVFS</td><td>Dynamic voltage and frequency scaling</td></tr></table>

Received 15 January 2024; revised 13 June 2024 and 28 August 2024; accepted 14 October 2024. Date of publication 22 October 2024; date of current version 21 February 2025. This work was supported in part by the National Key Research and Development Program of China under Grant 2023YFE0119800; in part by the National Natural Science Foundation of China under Grant 52277095; and in part by the Young Elite Scientists Sponsorship Program by CAST under Grant 2020QNRC001. Paper no. TSG-00079-2024. (Corresponding author: Zhaohao Ding.)

Yimeng Sun and Zhaohao Ding are with the School of Electrical and Electronic Engineering, North China Electric Power University, Beijing 102206, China (e-mail: yimeng.sun@ncepu.edu.cn; zhaohao.ding@ ncepu.edu.cn).

Yuejun Yan and Zhaoyang Wang are with Alibaba Group, Hangzhou 311121, China (e-mail: yanyuejun.yyj@alibabainc.com; chaoyang.wzy@ alibaba-inc.com).

Payman Dehghanian is with the Department of Electrical and Computer Engineering, George Washington University, Washington, DC 20052 USA (e-mail: payman@gwu.edu).

Wei-Jen Lee is with the Energy Systems Research Center, University of Texas at Arlington, Arlington, TX 76019 USA (e-mail: wlee@uta.edu).

Color versions of one or more figures in this article are available at https://doi.org/10.1109/TSG.2024.3482390.

Digital Object Identifier 10.1109/TSG.2024.3482390

ADMM Alternating direction method of multipliers POMDP Partially observable Markov decision process GNN Graph neural network DTDE Decentralized training with decentralized execution DAG Directed acyclic graphs SLA Service level agreement Non-i.i.d Non-independent and identically distributed.

## Sets and Indices

$\mathcal { N }$ Set of $\mathrm { C S P \ \{ 1 , 2 , \dots , } $ $\mathcal { E }$ Set of edges representing connections between CSPs that are one-hop neighbors $J _ { t , i }$ Set of jobs at CSP i at time t $\mathcal { I } _ { A }$ Set of transferable jobs, $\mathcal { T } _ { A } \subseteq J _ { t , i }$ $\mathcal { I } _ { B }$ Set of non-transferable jobs, $\mathcal { T } _ { B } \subseteq J _ { t , i }$ $\mathcal { A } _ { i , t }$ Set of actions taken by CSP i at time t τ Set of CSP i trajectories $\{ s _ { i , 0 } , a _ { i , 0 } , r _ { i , 0 } , . . . , s _ { i , T } , a _ { i , T }$ $r _ { i , T } \}$ $\mathcal { T } _ { a \nu a }$ Set of feasible tasks in CSPs $t r a n _ { j }$ Indicator of job $j ^ { \circ } \mathrm { s }$ transferability $\hbar n ^ { x , \bar { j } }$ Indicator of task completion, $\hbar n ^ { x , j } = 1$ if task x of job j is completed $t$ Index for time steps $j$ Index for jobs $i$ Index for CSPs.

## Parameters

$r e s _ { i }$ Total computing resources of CSP i $r e \nu _ { x _ { j } }$ Revenue obtained from completing task x of job j $\alpha$ Energy consumption coefficient during operation for CSPs $\beta$ Energy consumption coefficient during idle state for CSPs $c o s t _ { i , t } ^ { j , i ^ { \prime } }$ Transfer cost for job j from CSP i to i<sup></sup> $p e n _ { j }$ Penalty for job j exceeding the deadline $T$ Horizon of the agent’s execution $T _ { c o m }$ Number of communication rounds $E$ Maximum number of training iterations $J ^ { m a x }$ Maximum number of jobs that can be transferred in a single time step.

Variables

$s _ { i , t }$ State of CSP i at time t

$a _ { i , t }$ Action of CSP i at time t $r e s _ { i , t } ^ { a \nu a }$ Available computing resources at CSP i at time t $r _ { i , t }$ Reward of CSP i at time t

$R _ { i , t }$ Cumulative discounted reward for CSP i starting from time t

$p _ { t } ^ { e _ { i } }$ Electricity price encountered by CSP i at time t

$K$ Total number of one-hop neighbors of CSP i.

## Functions and Operators

$\pi _ { \pmb { \theta } _ { i } ^ { t } }$ Policy function of CSP i parameterized by ${ \pmb { \theta } } _ { i } ^ { t }$ $\mathbb { D } ^ { \iota }$ Metric operator induced by arbitrary convex norm, where $\mathbb { D } ( x , y )$ denotes the distance measure between x and y in the space

$\mathbb { E } _ { K } ( \cdot )$ Expectation operator for K CSPs

$\mathcal { L } _ { i } ( \cdot )$ Local loss function of CSP i

$f _ { K } ( \cdot )$ Aggregated loss function of K local loss function

(·) Personalized local loss function of CSP i.

## I. INTRODUCTION

## A. Backgrounds and Motivation

S THE primary infrastructure in the digital era, data centers are experiencing continuous growth in both size and quantity, which results in a significant increase in their power consumption [1]. Due to their unique operational characteristics, data centers have the capacity to flexibly schedule the execution sequence and allocate resources for computing jobs [2]. This inherent flexibility empowers data centers to effectively respond to signals from the power grid, such as spot market prices or carbon emission rates, via strategically scheduling their jobs, which makes them a promising demandside resource.

Among all types of data centers, geo-distributed data centers receive more and more attention as they can optimally schedule their jobs in a spatial-temporal manner, which enables power consumption flexibility across both time and space dimensions [3]. Both academic and industrial sectors have intensively investigated the energy sharing among geodistributed data centers within the management of a single cloud service provider (CSP). Yet, owing to the constraints related to the scale of computing jobs and the diversity of geographical resources, the flexibility attained by a single CSP is limited. In recent years, the emergence of container orchestration technologies, such as Kubernetes [4], Docker Swarm [5], and Amazon Elastic Container Service [6], has facilitated the possibility of collaboration among multiple CSPs. Specifically, some CSPs can transfer their jobs to others according to the characteristics of jobs and operating environments [7]. Hence, collaborative scheduling among CSPs enables a greater degree of flexibility and enhances the utilization efficiency of computing resources. This paper, accordingly, aims to analytically investigate energy sharing among multiple CSPs via collaborative job scheduling.

## B. Literature Review

To date, research on collaborative job scheduling among different CSPs for energy sharing is quite limited. In contrast, the issue of coordination among geo-distributed data centers operated by a single CSP has been investigated by a number of researchers. Initially, many works were present with objectives such as workload balance among data centers [8], [9], [10]. Pahlevan et al. [8] present a two-phase multi-objective optimization algorithm to balance the CPU load and reduce response time. Tripathi et al. [9] establish a min-cost network flow model to address the problem of load balancing in fault-tolerant data centers. Toosi and Buyya [10] propose a fuzzy logic-based algorithm to achieve cost-aware load balancing. Meanwhile, with the increasing concern about energy consumption, more researchers investigated the collaborative strategy to mitigate the total energy cost of geo-distributed data centers [11], [12], [13], [14]. Javadpour et al. [11] propose a priority-based sorting algorithm based on job execution deadlines to reduce total energy costs. Forestiero et al. [12] propose a hierarchical collaborative approach for the integrated management of heterogeneous data centers to achieve energy cost reduction. Mashayekhy et al. [13] construct an integer programming problem for electricity price-aware scheduling of MapReduce jobs. Pradhan and Satapathy [14] construct a fitness function based on a genetic algorithm to globally optimize job execution time and energy consumption costs. Furthermore, some research works started to pay attention to promoting the utilization of renewable energy with coordination among data centers [15], [16], [17], [18]. Qin et al. [15] investigate a queue stability problem to address the timecoupling constraints of carbon emissions and optimize the joint energy cost among data centers. Mahmud and Iyengar [16] and Yuan et al. [17] both consider geo-distributed data centers for energy sharing to accommodate local renewable energy output. Xu et al. [18] tackle the challenge of minimizing costs for big data analytics in geo-distributed data centers connected to unpredictable renewable energy supplies.

To achieve the aforementioned goals, equipment-level energy management methods, such as dynamic voltage and frequency scaling (DVFS) [19], [20], [21], [22], [23], [24] were adopted. Huai et al. [19] present a power-aware threshold unit based on DVFS to dynamically schedule online tasks in the cloud data center. Gu et al. [20] consider the availability status of servers and dynamically adjust voltage and frequency based on service level agreement to ensure the quality of service and reduce energy consumption. Zhou et al. [21] propose a Lyapunov-based online control method to regulate server frequency to achieve carbon emission reduction. Masoudi et al. [22] propose a two-stage energy-aware load balancing scheduling algorithm to deactivate physical machines in managing power consumption. Shirvani et al. [23] explore the effect of energy regulation by DVFS in cluster servers among intrinsically heterogeneous data centers. Liu et al. [24] propose a task scheduling algorithm based on thermal-aware DVFS to achieve maximum energy efficiency. Meanwhile, a number of researchers explored the potential of computing job scheduling and workload management for those targets [25], [26], [27], [28], [29], [30]. Alinezhadi et al. [25] propose a simplified workload scheduling model to provide the demandside response for geo-distributed data centers. Fan et al. [26] introduce a cost-aware workload scheduling by considering multi-dimensional resource utilization and local electricity prices. Lin et al. [27] propose a centralized two-stage workload n June 23,2026 at 12:44:12 UTC from IEEE Xplore. Restrictions apply.

scheduling framework to minimize energy consumption and the makespan. Yan et al. [28] introduce a deep reinforcement learning (DRL) approach to real-time workload scheduling, concentrating on optimizing energy consumption and ensuring high-quality service. Chen et al. [29] formulate the workload management problem as a multi-dimensional bin packing model and utilize the DRL method to address the multiresource multi-server job scheduling problem. Liang et al. [30] propose an efficient online workload management method among geo-distributed data centers based on the Advantage Actor-Critic approach.

Despite this considerable research progress, there are still many unaddressed challenges for energy sharing among multiple CSPs. Specifically, collaborative energy sharing among CSPs poses challenges mainly in two technical aspects: privacy constraints and environmental heterogeneity. The aforementioned literature mostly adopts a centralized scheduler to manage workloads or adjust voltage and frequency, which has visibility and control over all data centers. However, this setup may raise privacy concerns among different CSPs over critical operation information such as in-house job scheduling strategies and computing resource information. To address the data privacy problem in collaborative job scheduling, some studies have developed homomorphic encryption algorithms [31], [32] and smart contracts [33], [34]. However, most of those works focus on protecting customer data privacy while neglecting the operation privacy of different CSPs. Moreover, incorporating encryption algorithms and contract rules in the collaborative job scheduling process may lead to significant computational and communication overhead. Meanwhile, it is worth mentioning that distributed optimization methods, such as the alternating direction method of multipliers (ADMM), have been employed in various scenarios to achieve privacy-preserving energy sharing [35], [36], [37]. Scheduling large-scale stochastic computing jobs with sequential dependencies is inherently a nonconvex real-time decision-making problem, which involves high-dimensional integer variables and intractable non-linear coupling constraints. Thus, applying ADMM-based methods to these types of problems directly poses significant chal lenges. Consequently, data-driven approaches, such as machine learning, have been widely adopted to solve such types of problems in both academic [24], [25], [26], [28], [29], [30] and industrial [38], [39], [40], [41], [42] sectors. However, most of those works neglect the environmental heterogene ity encountered by different CSPs, such as job attributes, job arrival patterns, and locational electricity prices. The heterogeneity leads to datasets with non-independent and identically distributed (non-i.i.d.) characteristics. This undermines the robustness of conventional data-driven decision-making methods and may cause deviations from optimal strategies [43]. Although existing research in federated learning (FL) and transfer learning has investigated the effect of non-i.i.d data on predictive performance and model accuracy [44], [45], [46], there is a notable gap in understanding its implications for decision-making applications. This gap is particularly evident in addressing the challenge of environmental parameter heterogeneity in collaborative scheduling among CSPs, which remains an open area for research and development.

## C. Contributions and Paper Organization

To address the aforementioned challenges, we propose an environmental heterogeneity-aware federated reinforcement learning (FRL) scheme to achieve privacy-preserving energy sharing among CSPs via collaborative job scheduling. The major contributions of this paper are summarized as follows.

1) We establish a refined job scheduling model for data centers operated by different CSPs as a decentralized partially observable Markov decision process (Dec-POMDP), which enables efficient allocation of local computing resources for each CSP while collaboratively transferring jobs among CSPs. Besides, the refined model integrates the graph neural network (GNN) to ensure the execution of complex sequential constraints during the computing processes.

2) We develop a collaborative energy-sharing scheme based on FRL under decentralized training with decentralized execution (DTDE) framework to protect the operation privacy, i.e., the job scheduling strategies and computing resource information of CSPs. Leveraging this scheduling framework, we enable CSPs to cooperatively optimize the job scheduling process for energy sharing without infringing on their privacy.

3) We propose an auto-tuned personalization method to achieve decision-making considering the diverse environmental characteristics of CSPs. This method effectively integrates environmental heterogeneity such as job arrivals and regional electricity prices into the proposed FRL framework, enabling the perception of environmental characteristics and achieving superior scheduling results.

The remainder of this paper is organized as follows. Section II elaborates on the formulation of the collaborative job scheduling problem. Section III introduces our heterogeneity-aware FRL solution for privacy-preserving scheduling among CSPs. Section IV presents the results of our numerical analyses and Section V provides the conclusions.

## II. PROBLEM FORMULATION

In this section, we elaborate on the problem of energy sharing among CSPs via collaborative job scheduling and model the problem as a Dec-POMDP.

## A. Energy Sharing Among CSPs via Collaborative Job Scheduling

We investigate several CSPs that cater to distinct customers by providing computing services, achieving efficient energy sharing through the collaborative scheduling of jobs among their data centers as shown in Fig. 1. Without loss of generality, we assume, in this paper, that each CSP operates only one data center. However, it can be easily extended to the scenario in which one CSP manages multiple data centers as the job scheduling process remains similar to the proposed model.

In real-world production scenarios, jobs received by CSPs exhibit diverse attributes. In this paper, we consider the transferability, dependency, and resource occupancy of those jobs [47]. Transferability pertains to the capability of a job to be moved and processed within the data centers of other CSPs. Jobs executed in standardized computing environments such as network services, batch processing jobs, and serverless computing are transferable. On the contrary, jobs with specific requirements for computing environments remain non-transferable, including database applications, highperformance computing, and multi-GPU training. Dependency refers to the specific execution order among computing stages, known as tasks, of a job. In particular, we use directed acyclic graphs (DAGs) to denote topological relationships between tasks to guarantee the proper execution of the job [48]. Resource occupancy implies unique computing resource demands for each job, requiring detailed scheduling.

![](sun2025_privacy_energy_sharing_assets/images/76cadb1d05efab582cd1b56e930c2ade5024a169199dacb6ebe5d78305124e84.jpg)  
Fig. 1. Privacy-preserving energy sharing among CSPs via collaborative job scheduling.

Considering the aforementioned job attributes, we establish a collaborative job scheduling framework among CSPs, while simultaneously protecting their operation privacy. Under this framework, CSPs can only access private information, such as local job attributes and computing resource status. Then, they make job scheduling decisions, which include determining the destination for job transfers and allocating computing resources for locally processed jobs. Despite a lack of global visibility, they can acquire insights into the behavioral characteristics of other CSPs to support collaborative decisions through the training scheme detailed in Section III. Consequently, CSPs can achieve spatial energy sharing through coordinated job transfers and, simultaneously, attain temporal energy flexibility by strategically allocating local computing resources. This allows the effective utilization of temporal and spatial variations in electricity prices, renewable energy generation, and computing resources, thereby minimizing the overall system cost through the collaborative job scheduling framework.

Based on the aforementioned scheduling process, each CSP is operating as an independent entity and can not directly observe global information but has only partial observability. It is consistent with the definition of the Dec-POMDP. Therefore, we formulate this problem as the following model.

## B. Dec-POMDP Formulation

We model the CSPs network as an undirected graph $\mathcal { G } =$ $( \mathcal { N } , \mathcal { E } )$ , where $\mathcal { N }$ is the set of N CSPs and for $n , m \in \mathcal { N } , \mathcal { E }$ contains all links (n, m) where CSP n and m can communicate within one-hop distance [49]. Each node represents a CSP, and its job scheduling process is modeled as a Dec-POMDP, denoted by the tuple $\Gamma = \langle N , S , P , A , R \rangle$ . The definitions are as follows:

Agent: Each CSP is defined as an agent denoted by $i \in$ $I = \{ 1 , 2 , \dots , N \}$ . At each time step t, each CSP i receives jobs $J _ { t , i } = \{ j _ { t , i } ^ { 1 } , j _ { t , i } ^ { 2 } , \ldots , j _ { t , i } ^ { j } \}$ . These jobs are placed into the job queue according to the arrival time and wait for processing. We use $\mathcal { T } _ { A }$ and ${ \mathcal { I } } _ { B } ,$ , to represent transferable and non-transferable jobs, respectively.

State: For CSP i, the state variable $s _ { i , t } \in \mathcal S$ includes three components, which are job characteristics $s _ { i , t } ^ { j o b }$ , computing resource status $s _ { i , t } ^ { r e s }$ , and price information $s _ { i , t } ^ { p } .$

$s _ { i , t } ^ { j o b }$ : The job characteristics encompass the aforementioned three types of job attributes. Transferability of job j at time step t is defined as $t r a n _ { j } = \left\{ \begin{array} { l l } { 1 } & { j \in \mathcal { T } _ { A } } \\ { 0 } & { j \in \mathcal { T } _ { B } } \end{array} \right.$ Dependency of job j is denoted as an adjacency matrix $A _ { j } ^ { m a t }$ . It is a binary matrix of size X by X, where X is the number of tasks contained in a job. If there exists an edge $e _ { k , l } \neq \emptyset ,$ , meaning there is a dependency relationship from node k to node l, then the element in the k-th row and l-th column is set to 1, while the rest remain 0. The adjacency matrix is represented as follows:

$$
\boldsymbol {A} _ {j} ^ {\text { mat }} = \left[ \begin{array}{c c c} 0 & \dots & b _ {1 x} \\ \vdots & \ddots & \vdots \\ b _ {x 1} & \dots & 0 \end{array} \right] _ {X \times X}, \{b _ {k l} = 1 | e _ {k, l} \neq \emptyset \}.\tag{1}
$$

Resource occupancy of job j contains the total amount of computing resources required for each task $[ r e q _ { 1 } ^ { j } , \dots , r e q _ { x } ^ { j } ] ,$ and the current completion ratio of each task $[ \hbar n _ { t } ^ { 1 , j } , \dots , \hbar n _ { t } ^ { x , j } ]$

$s _ { i , t } ^ { r e s } \colon$ : The computing resource status consists of the total computing resources $r e s _ { i }$ of CSP i and the amount of currently available resource $r e s _ { i , t } ^ { a \nu a }$

$s _ { i , t } ^ { p } \mathrm { . }$ : The price information includes the time step t and the electricity price of N CSPs at time t, denoted as $[ e _ { t } ^ { 1 } , e _ { t } ^ { 2 } , \ldots , e _ { t } ^ { N } ]$

Action and transition function: The action variable $a _ { i , t }$ ∈ $\mathcal { A } _ { i , t } = \mathcal { A } _ { i , t } ^ { t r } \cup \mathcal { A } _ { i , t } ^ { a l }$ includes job transfer $a _ { i , t } ^ { t r , j } \in \mathcal { A } _ { i , t } ^ { t r }$ and resource allocation $a _ { i , t } ^ { a l , j } \in \mathcal { A } _ { i , t } ^ { a l }$ at time step $t . ~ a _ { i , t } ^ { t r , j }$ decides the transfer destination of jobs in the queue as $( 2 )$ :

$$
a _ {i, t} ^ {t r, j} = \left\{ \begin{array}{l l} i ^ {\prime}, i ^ {\prime} \in I & j \in \mathcal {J} _ {A} \\ i & j \in \mathcal {J} _ {B}. \end{array} \right.\tag{2}
$$

$a _ { i , t } ^ { a l , j }$ allocates computing resources for locally processed jobs based on the DAG relationships of jobs as (3) and (4). It assigns computing resources in the range of 0 to 100% of the available resources for available tasks and ensures that the sum of allocated resources does not exceed the upper limit of the available resources.

$$
a _ {i, t} ^ {a l, j} = \left\{ \begin{array}{l l} \varphi_ {j, x} \in \left[ 0, r e s _ {i, t} ^ {a v a} \right] & x \in \mathcal {T} _ {a v a} \\ \varphi_ {j, x} = 0 & o t h e r w i s e, \end{array} \right.\tag{3}
$$

$$
\sum_ {j \in \mathcal {J} _ {B}} \sum_ {x \in \mathcal {T} _ {a v a}} \varphi_ {j, x} \leq r e s _ {i, t} ^ {a v a},\tag{4}
$$

where $\tau _ { a v a }$ includes available tasks and the idle action.

It shall be noted that once decisions are made by CSPs, the execution of actions begins immediately without alteration or interruption. Subsequently, the computing resource state of CSPs changes to a specific state according to actions. Therefore, the transition function is a deterministic mapping with a transition probability of 1.

Reward: CSP i obtains a reward $r _ { i , t }$ through the following reward function (5):

$$
r _ {i, t} \left(s _ {i, t}, a _ {i, t}\right): \mathcal {S} \times \mathcal {A} \rightarrow \mathcal {R},\tag{5}
$$

which indicates the reward obtained by the CSP for taking action $a _ { i , t }$ at state $s _ { i , t }$

The total reward function for a single-step action is obtained by summing the revenues and negative costs in the job scheduling process. The objective of the CSP is to maximize the reward function, which is defined as:

$$
r _ {i, t} = r _ {i, t} ^ {r e v} + r _ {i, t} ^ {e n e r g y} + r _ {i, t} ^ {t r a n s} + r _ {i, t} ^ {d d l}.\tag{6}
$$

For these four reward components, we define the corresponding reward function as follows.

$r _ { i , t } ^ { r e \nu }$ : Once the computation of task $x _ { j }$ is completed, revenue $r e \nu _ { x _ { j } }$ can be obtained according to the service level agreement (SLA) signed with cloud customers [50]. The cumulative task completion revenue at time step t is

$$
r _ {i, t} ^ {r e v} = \sum_ {j} \sum_ {x} r e v _ {x _ {j}}, \quad f i n ^ {x, j} = 100 \%.\tag{7}
$$

The total computing revenue gained by CSP i at time step t is the sum of the revenues from all completed tasks in that step.

$r _ { i , t } ^ { e n e r g y } ;$ : We define the negative value of the energy consumption cost at time step t as the energy reward to the corresponding CSP. The energy consumption includes operational and idle components [51]. Consequently, the energy reward obtained by CSP i at time step t is

$$
r _ {i, t} ^ {\text {energy}} = - \left(\alpha (r e s _ {i} - r e s _ {i, t} ^ {a v a}) + \beta r e s _ {i, t} ^ {a v a}\right) \cdot p _ {t} ^ {e _ {i}},\tag{8}
$$

where $p _ { t } ^ { e _ { i } }$ denotes the time-varying electricity price encountered by CSP i. α and $\beta$ denote the operational and idle energy coefficients, respectively.

$r _ { i , t } ^ { t r a n s } ;$ : Transfer of jobs requires the occupancy of CSP’s switches and transmission bandwidth, resulting in corresponding transfer costs $c o s t _ { i , t } ^ { j , i ^ { \prime } }$ [52]. The cumulative transfer cost at time step t is

$$
r _ {i, t} ^ {t r a n} = - \sum c o s t _ {i, t} ^ {j, i ^ {\prime}},\tag{9}
$$

where $j \in \mathcal { I } _ { g e n } , i ^ { \prime } \in I \cap i \neq i ^ { \prime } .$ . The transfer cost for CSP i at time step t is the number of transferred jobs multiplied by a small fixed coefficient. Additionally, we set an upper limit $J ^ { m a x }$ for the maximum number of jobs that can be transferred in a single time step, based on the bandwidth capacity and average job transfer occupancy [53].

$r _ { i , t } ^ { d \bar { d } l }$ : According to the deadline specified in the SLA, if a job is not completed by the designated deadline, the corresponding CSP will incur a penalty. The deadline violation reward is defined as the negative value of the total penalties for jobs that violate their deadlines, and it is denoted as:

![](sun2025_privacy_energy_sharing_assets/images/43ce69d30a80bccea93a872555a1e5033eb012b54e16e9f17fc756aea6ef97b9.jpg)  
Fig. 2. CSP policy network architecture.

$$
r _ {i, t} ^ {d d l} = - \sum_ {j} p e n _ {j},\tag{10}
$$

where $p e n _ { j }$ can be obtained through the SLA. For simplicity, we set it to a very large number in this paper.

In this way, the long-term cumulative discounted return from time step t to the end of the scheduling period T can be computed as $R _ { i , t } = r _ { i , t } + \gamma r _ { i , t + 1 } + \cdot \cdot \cdot + \gamma ^ { T - 1 - t } r _ { i , T }$ , where $\gamma \in [ 0 , 1 ]$ is the discount factor that measures the importance of immediate and future rewards.

## III. SOLUTION METHOD

To solve the proposed Dec-POMDP model, we develop a heterogeneity-aware FRL scheme to achieve privacypreserving energy sharing among CSPs. First, we establish a decentralized training framework based on FRL, which implements collaborative job scheduling while protecting CSP operation privacy. Expanding on that, we propose a personalization method that perceives the environmental heterogeneity for the decision-making process of CSPs.

## A. Privacy-Preserving Collaborative Training Framework

To ensure the operation privacy of each CSP, we propose an FRL training approach under the DTDE framework to update the policy networks of CSPs. Under this framework, the decision-making process for each CSP is supported by the policy network $\boldsymbol { \pi } _ { \pmb { \theta } _ { i } ^ { t } }$ , where ${ \pmb { \theta } } _ { i } ^ { t }$ is the parameter vector of CSP i’s policy network. The specific network structure is illustrated in Fig. 2.

The policy network of CSPs consists of two parts which are feature extraction and decision-making network. Firstly, state information is input into the feature extraction network, which utilizes GNN to effectively represent job attributes and environmental characteristics due to its strong graph representation capabilities and scalability [54], [55]. Then, the extracted features are processed by the decision-making network to formulate scheduling strategies, including job transfer and resource allocation. The policy network ensures a detailed representation of the collaborative scheduling process among CSPs while adhering to constraints related to job attributes. The local loss function and update equation for each CSP are given in (11) and (12):

![](sun2025_privacy_energy_sharing_assets/images/7fa13fcde937854eafb028aba7c7350cf3a79179ca27d38ddf31ebaa94dec9c3.jpg)  
Fig. 3. Privacy-preserving training approach via FRL under the DTDE framework.

$$
\mathcal {L} _ {i} \big (\boldsymbol {\theta} _ {i} ^ {t} \big) = - \sum_ {\tau \sim \pi_ {\boldsymbol {\theta} _ {i} ^ {t}}} \log_ {\pi_ {\boldsymbol {\theta} _ {i} ^ {t}}} (a | s) R (\tau),\tag{11}
$$

$$
\pmb {\theta} _ {i} ^ {t + 1} = \pmb {\theta} _ {i} ^ {t} - \lambda \nabla \mathcal {L} _ {i} \big (\pmb {\theta} _ {i} ^ {t} \big),\tag{12}
$$

where $R ( \tau )$ is the cumulative reward of trajectory τ and $\lambda$ is the learning rate. It shall be noted that $R ( \tau )$ is positive due to the physical nature of the proposed model.

Based on the constructed policy network $\pi _ { \pmb { \theta } _ { i } ^ { t } }$ , each CSP executes its scheduling strategies and collects the trajectory τ independently. Then, the local loss function $\mathcal { L } _ { i } ( \pmb { \theta } _ { i } ^ { t } )$ of each CSP is updated in a decentralized manner using the policy gradient descent method. After the decentralized training for $T _ { c o m }$ rounds, the local model parameters ${ \pmb \theta } _ { i } ^ { t }$ of CSP $i \in I$ are aggregated with the parameters of its one-hop neighbors through FRL framework as illustrated in Fig. 3.

The optimization objective is to minimize the aggregated loss function $f ( { \pmb { \theta } } _ { a g g } ^ { t } )$ of K CSPs. The objective function and aggregation equation are defined in (13) and (14) as follows:

$$
\min _ {\boldsymbol {\theta} _ {a g g} ^ {t}} f \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right) = \sum_ {i = 1} ^ {K} q _ {i} \mathcal {L} _ {i} \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right), q _ {i} \geq 0, \sum_ {i} q _ {i} = 1,\tag{13}
$$

$$
\pmb {\theta} _ {a g g} ^ {t} = \frac {1}{K} \sum_ {i \in \mathcal {K}} \pmb {\theta} _ {i} ^ {t} = \mathbb {E} _ {K} \big [ \pmb {\theta} _ {i} ^ {t} \big ],\tag{14}
$$

where $\begin{array} { r } { \mathcal { K } = i \cup \mathcal { T } , \mathcal { T } = \{ i ^ { \prime } | ( i , i ^ { \prime } ) \in \mathcal { E } . \} , K = | \mathcal { K } | . } \end{array}$

Subsequently, the aggregated model parameters $\pmb { \theta } _ { a g g }$ are broadcast to CSP i guiding CSPs to engage in decentralized execution and continuously update their local models. Through multiple rounds of communication and iteration, each CSP continuously adjusts its policy, achieving mutual learning of collaborative operation strategies. Throughout this entire process, CSPs cannot know or infer the specific parameters or state information of other data centers. In this way, the collaborative job scheduling via FRL under the DTDE framework ensures privacy-preserving energy sharing among CSPs.

## B. Heterogeneity-Aware Personalization Method

As mentioned earlier, the environmental heterogeneity faced by CSPs during the decision-making process is another critical concern as the non-i.i.d nature of each CSP dataset could affect the performance of the proposed FRL algorithm. To address this challenge, we propose an environmental heterogeneityaware personalization method by reconstructing the local loss function of each CSP.

Firstly, we make the following definition and assumption.

Definition 1 (Personalized CSP Policy) [56]: For CSPs policy network $\pi _ { \pmb { \theta } _ { i } ^ { t } }$ , if $\pmb \theta _ { i , h } \gg \pmb \theta _ { i , l }$ , where $\pmb \theta _ { i , h }$ are the parameters corresponding to high heterogeneity input features and $\pmb { \theta } _ { i , l }$ to low heterogeneity features, then $\pi _ { \pmb { \theta } _ { i } ^ { t } }$ is defined as a personalized policy network that is capable of perceiving heterogeneity of input features.

Assumption 1: Given the loss function properties of current popular machine learning methods [57] and $R ( \tau )$ is positive, we assume that the local loss function $\mathcal { L } _ { i } ( \pmb { \theta } _ { i } ^ { t } )$ has the following characteristics:

1) $\mathcal { L } _ { i } ( \pmb { \theta } _ { i } ^ { t } )$ is L-smooth, i.e., $\mathbb { D } ( \nabla \mathcal { L } _ { i } ( x ) , \nabla \mathcal { L } _ { i } ( y ) ) \le L \mathbb { D } ( x , y )$ for any $x , y .$

2) $\mathcal { L } _ { i } ( \pmb { \theta } _ { i } ^ { t } )$ is σ -strongly convex, i.e., $\begin{array} { r l } { \mathcal { L } _ { i } ( y ) ~ - ~ \mathcal { L } _ { i } ( x ) } & { { } \ge } \end{array}$ $\begin{array} { r } { \langle \nabla \mathcal { L } _ { i } ( x ) , y ^ { ' } - x \rangle + \frac { \sigma } { 2 } \mathbb { D } ( y , x ) ^ { 2 } } \end{array}$ , where $\mathbb { D } ( X , Y ) : X \times Y \to$ <sup>R</sup> is an arbitrary metric operator induced by any convex norms.

Based on Definition 1 and Assumption 1, we present Lemma 1 for reconstructing local loss functions for each CSP.

Lemma 1 (Personalization Reconstruction): If the local loss function of CSPs is reconstructed as $\begin{array} { r l } { \mathcal { H } _ { i } ( { \pmb \theta } _ { i } ^ { t + 1 } ) } & { { } = } \end{array}$ $\begin{array} { r } { \mathcal { L } _ { i } ( \pmb { \theta } _ { i } ^ { t + 1 } ) + \frac { \xi _ { i } } { 2 } \mathbb { D } ( \pmb { \theta } _ { i } ^ { t + 1 } , \pmb { \theta } _ { a g g } ^ { t } ) ^ { 2 } } \end{array}$ , then CSPs can achieve personalized decision-making that is aware of environmental heterogeneity.

Proof: We assume that <sup>D</sup>(X, Y) is a metric operator induced by the L2-norm $\Vert \bigstar \bigstar \bigstar | | _ { 2 }$ with Euclidean distance $\| \pmb { x } \| _ { 2 } ^ { 2 } = x _ { 1 } ^ { 2 } +$ $\cdots + x _ { n } ^ { 2 }$ in the following proof.

Performing a quadratic approximation to the local loss function $\mathcal { H } _ { i } ( \pmb \theta _ { i } ^ { t } )$ in the neighborhood of the parameter values $\pmb { \theta } _ { i } ^ { t ^ { * } }$ that obtain the minimum training loss, we have

$$
\begin{array}{l} \tilde {\mathcal {H}} _ {i} (\boldsymbol {\theta} _ {i} ^ {t}) = \mathcal {H} _ {i} (\boldsymbol {\theta} _ {i} ^ {t *}) + \frac {1}{2} (\boldsymbol {\theta} _ {i} ^ {t} - \boldsymbol {\theta} _ {i} ^ {t *}) ^ {\top} \boldsymbol {H} (\boldsymbol {\theta} _ {i} ^ {t} - \boldsymbol {\theta} _ {i} ^ {t *}) \\ = \mathcal {L} _ {i} (\boldsymbol {\theta} _ {i} ^ {t *}) + \frac {\xi_ {i}}{2} \left\| \boldsymbol {\theta} _ {i} ^ {t} - \boldsymbol {\theta} _ {a g g} ^ {t} \right\| _ {2} ^ {2 ^ {2}} + \frac {1}{2} (\boldsymbol {\theta} _ {i} ^ {t} - \boldsymbol {\theta} _ {i} ^ {t *}) ^ {\top} \boldsymbol {H} (\boldsymbol {\theta} _ {i} ^ {t} - \boldsymbol {\theta} _ {i} ^ {t *}), \end{array} \tag {15}
$$

where H is the Hessian matrix of $\tilde { \mathcal { H } } _ { i }$ with respect to ${ \pmb { \theta } } _ { i } ^ { t }$ evaluated at ${ \pmb \theta } _ { i } ^ { t ^ { * } }$ . Then, we calculate the gradient of $\tilde { \mathcal { H } } _ { i }$ and set it equal to zero:

$$
\nabla_ {\boldsymbol {\theta} _ {i} ^ {t}} \tilde {\mathcal {H}} _ {i} = \xi_ {i} \left(\boldsymbol {\theta} _ {i} ^ {t} - \boldsymbol {\theta} _ {a g g} ^ {t}\right) + \boldsymbol {H} \left(\boldsymbol {\theta} _ {i} ^ {t} - \boldsymbol {\theta} _ {i} ^ {t *}\right) = 0.\tag{16}
$$

Since ${ \pmb { \theta } } _ { i } ^ { t ^ { * } }$ is the minimum of $\mathcal { H } _ { i }$ , H is positive semidefinite [58]. Then, the solution for ${ \pmb \theta } _ { i } ^ { t }$ is obtained as follows:

$$
\boldsymbol {\theta} _ {i} ^ {t} = (\boldsymbol {H} + \xi_ {i} \boldsymbol {I}) ^ {- 1} \boldsymbol {H} \left(\boldsymbol {\theta} _ {i} ^ {t *} + \boldsymbol {\theta} _ {a g g} ^ {t}\right).\tag{17}
$$

We perform the eigenvector decomposition of H as ${ \textbf { \em H } } =$ $Q \Lambda Q ^ { \mathsf { T } }$ to (17):

$$
\begin{array}{l} \boldsymbol {\theta} _ {i} ^ {t} = \left(\boldsymbol {Q} \boldsymbol {\Lambda} \boldsymbol {Q} ^ {\intercal} + \xi_ {i} \boldsymbol {I}\right) ^ {- 1} \boldsymbol {Q} \boldsymbol {\Lambda} \boldsymbol {Q} ^ {\intercal} \Big (\boldsymbol {\theta} _ {i} ^ {t *} + \boldsymbol {\theta} _ {a g g} ^ {t} \Big) \\ \qquad = \boldsymbol {Q} (\boldsymbol {\Lambda} + \xi \boldsymbol {I}) ^ {- 1} \boldsymbol {\Lambda} \boldsymbol {Q} ^ {\intercal} \Big (\boldsymbol {\theta} _ {i} ^ {t *} + \boldsymbol {\theta} _ {a g g} ^ {t} \Big) \\ \qquad = \boldsymbol {\lambda} \Big (\boldsymbol {\theta} _ {i} ^ {t *} + \boldsymbol {\theta} _ {a g g} ^ {t} \Big), \boldsymbol {\lambda} = \bigg [ \frac {\lambda_ {i , 1}}{\lambda_ {i , 1} + \xi_ {i}}, \ldots , \frac {\lambda_ {i , d}}{\lambda_ {i , d} + \xi_ {i}} \bigg ], \end{array}\tag{18}
$$

where d is the dimension of input features and λ is the eigenvector of H which measures the heterogeneity of environmental features between local and aggregated models [58]. From (18), we observe that when $\lambda _ { i , m } \ll \xi _ { i } , l i m \theta _ { i , m } ^ { t } = 0 .$ , and when $\lambda _ { i , m } \gg \xi _ { i }$ , lim $\theta _ { i , m } ^ { t } = { \theta _ { i , m } ^ { t } } ^ { * } + \theta _ { a g g , m } ^ { t } \gg 0$ . In this manner, parameters corresponding to features with low heterogeneity are decayed to a sufficiently small value during training to minimize their influence, while those corresponding to high heterogeneity are maintained at a large scope to enhance their impact. As the aggregated parameters continually change, the local parameters are dynamically adjusted accordingly. It enables CSPs to consistently and effectively manage the influence of distinct local characteristics, balancing between local heterogeneous features and aggregated characteristics during training. With that, we prove the reconstructed loss function meets Definition 1 under the L2-norm induction. Moreover, we can extend this proof to arbitrary norms based on the norm equivalence theorem in finite-dimensional spaces. Consequently, we can demonstrate that CSPs can make personalized decisions that align with local heterogeneous characteristics via the proposed personalized reconstruction of the local loss function.

Furthermore, we provide the convergence conditions and the corresponding proof based on the proposed personalized reconstruction method. ■

Lemma 2 (Convergence Conditions): For $\mathcal { H } _ { i } ( { \pmb \theta } _ { i } ^ { t + 1 } )$ of each CSP, if $\begin{array} { r } { \xi _ { i } ~ < ~ \frac { 2 \sigma ( \sigma - \bar { L } D ) } { L D ^ { 2 } } } \end{array}$ , then, the aggregated model $f ( { \pmb { \theta } } _ { a g g } ^ { t } )$ converges.

Proof: Straightforwardly from Assumption 1, the aggregated model $f ( { \pmb { \theta } } _ { a g g } ^ { t } )$ is also L-smooth and σ -strongly convex, as the aggregated model is a finite convex combination of local losses $\mathcal { L } _ { i } ( \pmb { \theta } _ { a g g } ^ { t } )$ . Next, we calculate the gradient of the personalized local loss function and set it equal to zero. We have:

$$
\nabla \mathcal {H} _ {i} \left(\boldsymbol {\theta} _ {i} ^ {t + 1}\right) = \nabla \mathcal {L} _ {i} \left(\boldsymbol {\theta} _ {i} ^ {t + 1}\right) + \xi_ {i} \mathbb {D} \left(\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t}\right) = 0.\tag{19}
$$

Given $\pmb { \theta } _ { a g g } ^ { t + 1 } = \mathbb { E } _ { K } [ \pmb { \theta } _ { i } ^ { t + 1 } ]$ and applying Jensen inequality, we have:

$$
\mathbb {D} \left(\boldsymbol {\theta} _ {a g g} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t}\right) = \mathbb {D} \left(\mathbb {E} _ {K} \left[ \boldsymbol {\theta} _ {i} ^ {t + 1} \right], \boldsymbol {\theta} _ {a g g} ^ {t}\right) \leq \mathbb {E} _ {K} \left[ \mathbb {D} \left(\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t}\right) \right].\tag{20}
$$

Utilizing the σ -strongly convex property of $\mathcal { H } _ { i } ( { \pmb \theta } _ { i } ^ { t + 1 } )$ ), we obtain:

$$
\begin{array}{c} \mathcal {H} _ {i} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1} \Big) \geq \mathcal {H} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) + \Big \langle \nabla \mathcal {H} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big), \boldsymbol {\theta} _ {i} ^ {t + 1} - \boldsymbol {\theta} _ {a g g} ^ {t} \Big \rangle \\ + \frac {\sigma}{2} \mathbb {D} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) ^ {2} \end{array}\tag{21}
$$

$$
\begin{array}{c} \mathcal {H} _ {i} (\boldsymbol {\theta} _ {a g g}) \geq \mathcal {H} _ {i} (\boldsymbol {\theta} _ {i} ^ {t + 1}) + \left\langle \nabla \mathcal {H} _ {i} (\boldsymbol {\theta} _ {i} ^ {t + 1}), \boldsymbol {\theta} _ {a g g} ^ {t} - \boldsymbol {\theta} _ {i} ^ {t + 1} \right\rangle \\ + \frac {\sigma}{2} \mathbb {D} (\boldsymbol {\theta} _ {a g g} ^ {t}, \boldsymbol {\theta} _ {i} ^ {t + 1}) ^ {2} \end{array}\tag{22}
$$

Adding (21) and (22), noting that $\nabla \mathcal { H } _ { i } ( \pmb { \theta } _ { i } ^ { t + 1 } ) = 0$ and applying the Cauchy-Schwarz inequality, we get:

$$
\left\langle \nabla \mathcal {H} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big), \boldsymbol {\theta} _ {i} ^ {t + 1} - \boldsymbol {\theta} _ {a g g} ^ {t} \right\rangle + \sigma \mathbb {D} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) ^ {2} \leq 0\tag{23}
$$

$$
\sigma \mathbb {D} \Bigl (\pmb {\theta} _ {i} ^ {t + 1}, \pmb {\theta} _ {a g g} ^ {t} \Bigr) ^ {2} \leq - \Bigl \langle \nabla \mathcal {H} _ {i} \Bigl (\pmb {\theta} _ {a g g} ^ {t} \Bigr), \pmb {\theta} _ {i} ^ {t + 1} - \pmb {\theta} _ {a g g} ^ {t} \Bigr \rangle\tag{24}
$$

$$
\begin{array}{l} \leq \left| - \left\langle \nabla \mathcal {H} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big), \boldsymbol {\theta} _ {i} ^ {t + 1} - \boldsymbol {\theta} _ {a g g} ^ {t} \right\rangle \right| \\ \leq \mathbb {D} \Big (\nabla \mathcal {H} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big) \mathbb {D} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) \end{array}
$$

Noting that $\mathbb { D } ( \pmb { \theta } _ { i } ^ { t + 1 } , \pmb { \theta } _ { a g g } ^ { t } ) \geq 0 .$ , we rearrange (24) to obtain:

$$
\mathbb {D} \left(\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t}\right) \leq \frac {1}{\sigma} \mathbb {D} \left(\nabla \mathcal {H} _ {i} \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right)\right)\tag{25}
$$

Given the properties of metric spaces, $\mathbb { D } ( \pmb { \theta } _ { a g g } ^ { t } , \pmb { \theta } _ { a g g } ^ { t } ) \ = \ 0$ Thus,

$$
\nabla \mathcal {H} _ {i} \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right) = \nabla \mathcal {L} _ {i} \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right) + \xi_ {i} \mathbb {D} \left(\boldsymbol {\theta} _ {a g g} ^ {t}, \boldsymbol {\theta} _ {a g g} ^ {t}\right) = \nabla \mathcal {L} _ {i} \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right).\tag{26}
$$

Substituting (25), (26) into (20) and applying the Cauchy-Schwarz inequality, we have:

$$
\begin{array}{l} \mathbb {D} \Big (\boldsymbol {\theta} _ {a g g} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) \leq \mathbb {E} _ {K} \Big [ \mathbb {D} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big ] \\ \quad \leq \frac {1}{\sigma} \mathbb {E} _ {K} \Big [ \mathbb {D} \Big (\nabla \mathcal {L} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big) \Big ] \\ \quad \leq \frac {1}{\sigma} \sqrt {\mathbb {E} _ {K} \Big [ \mathbb {D} \Big (\nabla \mathcal {L} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big) ^ {2} \Big ]} \\ = \frac {D \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big)}{\sigma} \mathbb {D} \Big (\nabla f \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big) \end{array}\tag{27}
$$

where $\begin{array} { r } { D ( \pmb { \theta } _ { a g g } ^ { t } ) = \sqrt { \frac { \mathbb { E } _ { K } [ \mathbb { D } ( \nabla \mathcal { L } _ { i } ( \pmb { \theta } _ { a g g } ^ { t } ) ^ { 2 } ) ] } { \mathbb { D } ( \nabla f ( \pmb { \theta } _ { a g g } ^ { t } ) ) ^ { 2 } } } } \end{array}$ is D-locally dissimilar factor [59]. For simplicity, we use D to represent $D ( \pmb { \theta } _ { a g g } ^ { t } )$ Next, we define $P _ { t + 1 } = \mathbb { E } _ { K } [ \nabla \mathcal { L } _ { i } ( \pmb { \theta } _ { i } ^ { t + 1 } ) - \nabla \mathcal { L } _ { i } ( \pmb { \theta } _ { a g g } ^ { t } ) ]$ and utilize the L-smooth property of $\mathcal { L } _ { i }$ :

$$
\begin{array}{l} \mathbb {D} (P _ {t + 1}) = \mathbb {D} \Big (\mathbb {E} _ {K} \Big [ \nabla \mathcal {L} _ {i} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1} \Big) - \nabla \mathcal {L} _ {i} \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big ] \Big) \\ \leq \mathbb {E} _ {K} \Big [ L \mathbb {D} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big ] = L \mathbb {E} _ {K} \Big [ \mathbb {D} \Big (\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) \Big ]. \end{array} \tag {28}\tag{28}
$$

Substituting (27) into (28), we obtain:

$$
\begin{array}{r l} \mathbb {D} (P _ {t + 1}) & \leq L \mathbb {E} _ {K} \left[ \mathbb {D} \left(\boldsymbol {\theta} _ {i} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t}\right) \right] \leq \frac {L}{\sigma} \mathbb {E} _ {K} \left[ \mathbb {D} \left(\nabla \mathcal {L} _ {i} (\boldsymbol {\theta} _ {a g g} ^ {t})\right) \right] \\ & \leq \frac {L D}{\sigma} \mathbb {D} \left(\nabla f (\boldsymbol {\theta} _ {a g g} ^ {t})\right) \end{array} \tag {29}
$$

Based on the Descent Lemma for the L-smooth property of the aggregation loss function [60], we can find that

$$
\begin{array}{c} f \Big (\boldsymbol {\theta} _ {a g g} ^ {t + 1} \Big) - f \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) \leq \Big \langle \nabla f \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big), \boldsymbol {\theta} _ {a g g} ^ {t + 1} - \boldsymbol {\theta} _ {a g g} ^ {t} \Big \rangle \\ + \frac {L}{2} \mathbb {D} \Big (\boldsymbol {\theta} _ {a g g} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) ^ {2}. \end{array}\tag{30}
$$

According to the concept of gradient noise error [61], $\pmb { \theta } _ { a g g } ^ { t + 1 } -$ $\begin{array} { r } { \pmb { \theta } _ { a g g } ^ { t } = - \frac { 1 } { \xi _ { i } } ( \nabla f ( \pmb { \theta } _ { a g g } ^ { t } ) + P _ { t + 1 } ) } \end{array}$ , substituting this into (30) and using the properties of inner product and the Cauchy-Schwarz inequality, we obtain:

$$
\begin{array}{l} f \Big (\boldsymbol {\theta} _ {a g g} ^ {t + 1} \Big) - f \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big) \\ \leq - \frac {1}{\xi_ {i}} \Big \langle \nabla f \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big), \Big (\nabla f (\boldsymbol {\theta} _ {a g g} ^ {t}) + P _ {t + 1} \Big) \Big \rangle \\ + \frac {L}{2} \mathbb {D} \Big (\boldsymbol {\theta} _ {a g g} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) ^ {2} \end{array}
$$

$$
\begin{array}{l} \leq - \frac {1}{\xi_ {i}} \mathbb {D} \Big (\nabla f (\boldsymbol {\theta} _ {a g g} ^ {t + 1}) \Big) ^ {2} - \frac {1}{\xi_ {i}} \Big \langle \nabla f \Big (\boldsymbol {\theta} _ {a g g} ^ {t} \Big), P _ {t + 1} \Big \rangle \\ \qquad + \frac {L}{2} \mathbb {D} \Big (\boldsymbol {\theta} _ {a g g} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) ^ {2} \\ \leq - \frac {1}{\xi_ {i}} \mathbb {D} \Big (\nabla f (\boldsymbol {\theta} _ {a g g} ^ {t + 1}) \Big) ^ {2} - \frac {1}{\xi_ {i}} \mathbb {D} \Big (\nabla f (\boldsymbol {\theta} _ {a g g} ^ {t}) \Big) \mathbb {D} (P _ {t + 1}) \\ \qquad + \frac {L}{2} \mathbb {D} \Big (\boldsymbol {\theta} _ {a g g} ^ {t + 1}, \boldsymbol {\theta} _ {a g g} ^ {t} \Big) ^ {2}. \end{array}\tag{31}
$$

Substituting (27) and (29) into the right side of (31) and combining like terms, we get:

$$
f \left(\boldsymbol {\theta} _ {a g g} ^ {t + 1}\right) - f \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right) \leq - \left(\frac {1}{\xi_ {i}} - \frac {L D}{\xi_ {i} \sigma} - \frac {L D ^ {2}}{2 \sigma^ {2}}\right) \mathbb {D} \left(\nabla f \left(\boldsymbol {\theta} _ {a g g} ^ {t}\right)\right) ^ {2}.\tag{32}
$$

When $\begin{array} { r } { \xi _ { i } \ \le \ \frac { 2 \sigma ( \sigma - L D ) } { L D ^ { 2 } } } \end{array}$ , it ensures that the updates of the aggregated loss function proceed in the descending direction, and each round of iterations guarantees a sufficient reduction. Also, we know that the proposed energy-sharing problem is bounded due to its inherent nature. Therefore, the convergence of the algorithm is ensured.

In this paper, we employ a constrained auto-tuned method to optimize the value of $\xi _ { i }$ for accelerating the convergence of the model. $\xi _ { i }$ is updated according to (33):

$$
\xi_ {i} ^ {*} = \xi_ {i} ^ {t} - \lambda \nabla \mathcal {H} _ {i} (\pmb {\theta} _ {i} ^ {t}).\tag{33}
$$

To ensure the convergence of the model, the values of $\xi _ { i }$ are determined by $\begin{array} { r } { \xi _ { i } ^ { t + \tilde { 1 } } = \operatorname* { m i n } ( \xi _ { i } ^ { * } , \frac { 2 \sigma ( \sigma - L \tilde { D } ) } { L D ^ { 2 } } ) } \end{array}$ . Consequently, we ensure the convergence of the aggregate model in the collaborative process of CSPs and accelerate the convergence of the model by employing auto-tuned parameters. ■

## C. FRL-Based Collaborative Energy-Sharing Scheme

By integrating the personalized loss function into the proposed FRL training framework, we develop a privacypreserving collaborative energy-sharing scheme for multiple CSPs while accounting for the environmental heterogeneity. Specifically, we reconstruct CSPs’ local loss function as <sub>i</sub> to realize the personalized decision while implementing the proposed FRL training under the DTDE framework to achieve the protection of operation privacy. The pseudo-code for the heterogeneity-aware FRL-based collaborative energy-sharing scheme is presented in Algorithm 1. After CSP i completes $T _ { c o m }$ steps of local training, it performs an anonymous aggregation with its one-hop neighbors. Then, CSP i updates its local policy and adjusts the corresponding parameters based on the aggregated parameters. The iterative process continues until the maximum training iterations $E ,$ where the value of E is determined using the early stopping method [62].

## IV. CASE STUDY

## A. Experimental Setting

In this section, we conduct numerical experiments with three CSPs to illustrate the effectiveness of the proposed collaborative energy-sharing scheme. The three CSPs used in this numerical case each contain 500 servers, corresponding to 50,000 computing resources. The server operational energy coefficient α is $1 \times 1 0 ^ { - 7 } / \mathrm { M W }$ , and the idle energy coefficient $\beta$ is $0 . 4 \times 1 0 ^ { - 7 } / \mathrm { M W } .$

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1: Heterogeneity-Aware FRL-Based Collaborative Energy Sharing
1 Initialize CSPs' local model with random weights $\theta_i^0, i = 1, 2...N$;
2 Initialize the communication round $T_{com}$, training horizon $T$, maximum training iterations $E$, epoch = 0;
3 while epoch &lt; E do
4    if epoch = $T_{com}$, $2T_{com}$, ... then
5    Calculate $\theta_{agg}^{epoch} = \mathbb{E}_K[\theta_i^{epoch}]$;
6    Broadcast $\theta_{agg}^{epoch}$ back to CSP $i$;
7    else
8    for each CSP $i \in N$ in parallel do
9    for $t = 1:T$ do
10    Select action $a_{i,t}$ according to policy $\pi(\theta_i)$;
11    Get reward $r_{i,a}$ and next state $s_{t+1}$;
12    Store trajectory $\tau$ and compute the local loss $\mathcal{H}_i(\boldsymbol{\theta}_i^t)$;
13    Update $\boldsymbol{\theta}_i^{t+1}$ using $\nabla_{\boldsymbol{\theta}_i^t}\mathcal{H}_i(\boldsymbol{\theta}_i^t)$;
14    Calculate $\xi_i^*$ using $\nabla_{\xi_i^t}\mathcal{H}_i(\boldsymbol{\theta}_i^t)$;
15    if $\xi_i* &lt; \frac{2\sigma(\sigma-LD)}{LD^2}$ then
16    $\xi_i^{t+1} = \xi_i^*$
17    else
18    $\xi_i^{t+1} = \frac{2\sigma(\sigma-LD)}{LD^2}$
19    end
20    end
21    end
22    end
23    epoch += 1;
24 end
</div>

TABLE I  
HYPERPARAMETER SETTINGS OF CSP POLICY NETWORK

<table><tr><td>Network parameter setting</td><td>input layer × output layer</td></tr><tr><td>Conv_1</td><td>11 × 16</td></tr><tr><td>Conv_2</td><td>16 × 8</td></tr><tr><td>Linear_1</td><td>19 × 8</td></tr><tr><td>Linear_2</td><td>8 × 8</td></tr><tr><td>Policy net_1</td><td>16 × 32 × 16 × 3</td></tr><tr><td>Policy net_2</td><td>24 × 32 × 16 × 1</td></tr></table>

The activation function between layers in the policy network of CSPs is ReLU [63]. The hyperparameters of the policy network are configured as shown in Table I.

In the experimental system, we consider the electricity price and job arrival amounts as stochastic parameters. Detailed information on the jobs is obtained from Alibaba and the job arrival is modeled as a Poisson distribution based on the acquired historical data. Fig. 4 illustrates the spatial-temporal variations in job arrivals and electricity prices at each CSP over a 24-hour period.

![](sun2025_privacy_energy_sharing_assets/images/6c9d3654dc46c79e55228dfd8901c308b93c3553d0ae3d13423ab79db514fdc4.jpg)

Fig. 4. Job arrivals and electricity prices at (a) CSP 1, (b) CSP 2, and (c) CSP 3.  
![](sun2025_privacy_energy_sharing_assets/images/a737561e07e55fab2f40cd6a0f6caa580964c1bb878f4bdbecca3d40581a4575.jpg)  
Fig. 5. Variations in the computing energy consumption of (a) CSP 1, (b) CSP 2, and (c) CSP 3.

## B. Energy Sharing Among CSPs via Collaborative Job Scheduling

To demonstrate the effectiveness of the proposed collaborative energy-sharing framework, we illustrate the variations in computing energy consumption of three CSPs in Fig. 5 and provide a detailed illustration of the job scheduling process in Fig. 6 and Fig. 7.

Fig. 5 demonstrates the collaborative job scheduling among three CSPs based on computing job arrivals and electricity prices at respective locations. As shown in Fig. 4, both the electricity price and the job arrival amounts at CSP 1 remain at a low level from 0:00-7:00 and 21:00-24:00. Simultaneously, the electricity prices at CSP 2 and CSP 3 are significantly higher than that at CSP 1. As a result, CSP 2 and CSP 3 offload most of the transferable jobs to CSP 1 for execution to achieve spatial flexibility. Furthermore, CSPs process a higher volume of jobs during periods characterized by low electricity prices, aligning with the temporal variation of prices and achieving temporal flexibility.

![](sun2025_privacy_energy_sharing_assets/images/ef9522372d80575727632d979f053b2713aa938db561a6c63275cf439a0f76f5.jpg)  
Fig. 6. A snapshot of CSP 3’s job transfer decisions at 2:00.

On the other hand, the job transfer decision is also influenced by the computing resource utilization status at the destination CSP. For instance, CSP 1 takes into account a substantial amount of jobs that other CSPs need to handle from 17:00-20:00, thus offloading only a small portion of the transferable load to other CSPs. Therefore, the proposed scheduling method enables efficient energy sharing among CSPs by leveraging both the electricity price and computing resources in different locations and time periods. This allows job processing with low electricity prices and reduces the total energy costs.

We also provide a detailed demonstration of the job scheduling process for CSP 3 at 2:00 to show the collaborative job transfer and computing resource allocation decisions of CSPs. At this time, the electricity prices for three CSPs are 18.39\$/MWh, 52.52 \$/MWh, and 39.68 \$/MWh, respectively. There are 191 jobs in CSP 3’s job queue, including 22 remaining unfinished jobs and 169 jobs that arrived at 2:00. It can be observed from Fig. 6 that due to the low electricity price at CSP 1, CSP 3 schedules the majority of jobs for execution by CSP 1, with a small portion being processed locally. Additionally, the deadlines for jobs transferred to CSP 1 are mainly distributed within 2 hours. The reason behind this strategy lies in the limited temporal flexibility of this subset of jobs. In other words, they necessitate the full exploitation of the energy cost-saving benefits associated with low electricity prices within the specified deadline. Concurrently, jobs with more lenient deadline constraints can be retained locally, enabling flexible resource allocation decisions in response to future fluctuations in electricity prices.

Fig. 7 illustrates the computing resource allocation for the 67 locally processed jobs by CSP 3 at 2:00. The varying colors in the grids indicate that CSP 3 prioritizes allocating computing resources to tasks of the remaining 22 unfinished jobs in the job queue. This preference is driven by the fact that these jobs are closer to their respective deadlines compared to the newly arrived jobs, leading to their prioritized execution to mitigate potential significant penalties that will be incurred if the deadlines are exceeded. Additionally, observing along the task axis reveals that computing resources are allocated only to currently available tasks, such as Task 5 of Job 1 and Tasks 5, 6, and 7 of Job 11. This allocation aligns with the constraints imposed by the job dependency as shown in Fig. 7(a), which further demonstrates the effectiveness of the feature extraction network.

![](sun2025_privacy_energy_sharing_assets/images/c6d86682e6d4df13603f545bd1fa71d49922a3d1bf4e834d7225475e4b11ecc6.jpg)  
Fig. 7. Microscopic resource allocation decisions of CSP 3 at 2:00.

TABLE II  
THE SCHEDULING RESULTS IN DIFFERENT CASES

<table><tr><td></td><td>Case I</td><td>Case II</td><td>Case III</td></tr><tr><td>Energy cost ($)</td><td>13092.41</td><td>12634.77</td><td>18685.52</td></tr><tr><td>Transfer cost ($)</td><td>2113</td><td>2072</td><td>0</td></tr><tr><td>Deadline violation rate</td><td>0.395%</td><td>0.352%</td><td>0.571%</td></tr></table>

To further compare and evaluate the effectiveness of the proposed collaborative scheduling framework, we present the optimization results under three cases. The settings and optimization results are shown below.

• Case I: The proposed method. This case is the proposed DTDE framework, in which all CSPs perform collaborative energy sharing based on the environmental heterogeneity-aware FRL scheme.

• Case II: Globally centralized method. This case is a centralized scheduling framework, in which all CSPs jobs are sent to a central scheduler (i.e., the system is globally optimized). However, it shall be noted that this case is infeasible for multiple CSPs collaboration due to privacy concerns.

• Case III: Fully decentralized method. This case is a fully decentralized framework, in which the three CSPs individually handle their jobs without any collaboration. All jobs cannot be transferred between CSPs, and only the local computing resource state can be observed.

Table II reveals that the performance of our proposed model closely approximates that of the globally centralized scheduling method. Furthermore, the proposed method reduces the total cost by 18.62% and the deadline violation rate by 30.77% compared to the fully decentralized method.

Fig. 8 illustrates a comparison of the training process for three different cases. It can be observed that, although the globally centralized method has the lowest total cost, it requires a significant amount of computation time for training. On the contrary, the fully decentralized method converges faster but yields inferior optimization results. Consequently, the proposed method not only achieves near-optimal results but also enhances the training performance.

![](sun2025_privacy_energy_sharing_assets/images/ec564e49a671cdceb9f747caf1171ddb981480b983f16434a56ce29fe82a51e2.jpg)  
Fig. 8. Comparison of the training results in three cases.

![](sun2025_privacy_energy_sharing_assets/images/b9eeb29dea5f1dae48a1eb581e01ded588c6750b36b18468113613c17307d562.jpg)

![](sun2025_privacy_energy_sharing_assets/images/7cbed31ba437d44e63e7a87761c23e2b31c96b4c309d4b32401e2a41fc2f7606.jpg)  
Fig. 9. Comparison of optimization results with and without heterogeneityaware Scheduling.

## C. Environmental Heterogeneity-Aware Energy Sharing

In this subsection, we conduct several numerical experiments to illustrate the impact of environmental heterogeneity awareness on CSP energy sharing.

Fig. 9 displays the results of collaborative scheduling among CSPs with and without environmental heterogeneity awareness. The energy consumption cost and transfer cost are normalized, with the benchmark set at 15,000 and 2,000 dollars, respectively. It can be observed that the heterogeneityaware scheduling results exhibit lower costs and deadline violation rates than the heterogeneity-agnostic approach, along with smaller variance. This improvement stems from the consideration of environmental heterogeneity, enabling more personalized decisions aligned with local characteristics. In contrast, the heterogeneity-agnostic approach enforces the decisions generated by the aggregation model among three CSPs, which makes CSPs take actions based on similar policies without considering environmental heterogeneity. For instance, CSPs allocate comparable amounts of computing resources for locally processed jobs, regardless of the electricity price differences faced by CSPs. Consequently, this leads to a deviation of CSP decisions from optimal choices and to be less robust to environmental heterogeneity, resulting in suboptimal results.

![](sun2025_privacy_energy_sharing_assets/images/7210c45a70034b1677ac279adf213b8a4b4147d45b4698494884e71205abf389.jpg)

Fig. 10. The daily computing energy consumption of (a) heterogeneity-aware and (b) heterogeneity-agnostic CSPs.  
![](sun2025_privacy_energy_sharing_assets/images/3ae1dd99676cd3fab0b6e877ed2c3d4dcb167dec5cc349e20252a1d93411a16e.jpg)  
Fig. 11. Impact of the discount factor on the proposed method.

We further compare the computing energy consumption variations among three CSPs with and without heterogeneity awareness. The computing energy consumption in Fig. 10(a) fluctuates in response to the varying electricity prices at different times and locations to minimize energy costs, and exhibits distinct temporal and spatial disparities. This observation also aligns with the results presented in Fig. 5. In contrast, Fig. 10 (b) shows that the temporal and spatial differences in computing energy consumption among CSPs are not significant. This indicates that heterogeneity-agnostic scheduling is prone to lead to similar actions, resulting in the over-utilization of computing resources and inadequate adaptation to variations in electricity prices.

## D. Sensitivity and Scalability Analyses

In this subsection, we conduct sensitivity analyses on the discount factor and learning rate and evaluate the scalability of the proposed model.

Fig.11 illustrates the impact of the discount factor γ on the performance of the proposed method. A small discount factor may lead CSPs short-sighted, whereas a large one can cause instability in the return function estimates [64]. As shown in Fig. 11, with γ set to 0.9, the proposed method achieves a balance between these two extremes, effectively reducing the costs of CSPs.

Table III demonstrates the impact of different learning rates on the convergence of the proposed method. When the learning rate is set at $1 \times 1 0 ^ { - 3 }$ , the excessively high value can induce training instability and hinder convergence. Conversely, reducing the learning rate to $1 \times 1 0 ^ { - 4 }$ , while stabilizing the training process, results in slower parameter updates and consequently a prolonged training duration. Despite this, the improvement in cost is marginal, with only a 0.37% reduction. Referring to the classic policy-based algorithm [65], we set the learning rate to $3 \times 1 0 ^ { - 4 }$ , which achieves a good balance between training time and performance.

TABLE III  
IMPACT OF LEARNING RATES ON THE TRAINING PROCESS

<table><tr><td>Learning rate</td><td>Convergence time (hour)</td><td>Training epoch</td><td>Total cost($)</td></tr><tr><td> $1 \times 10^{-4}$ </td><td>11.83</td><td>6500</td><td>15148.57</td></tr><tr><td> $3 \times 10^{-4}$ </td><td>3.371</td><td>2000</td><td>15205.41</td></tr><tr><td> $1 \times 10^{-3}$ </td><td>Divergence</td><td>/</td><td>/</td></tr></table>

TABLE IV

TRAINING AND INFERENCE TIME UNDER DIFFERENT SETTINGS OF THE JOB ARRIVAL SCALE

<table><tr><td>Job arrival scale (per day)</td><td>training time (hour)</td><td>inference time (ms)</td></tr><tr><td>9,000</td><td>0.729</td><td>8</td></tr><tr><td>21,000</td><td>3.371</td><td>19</td></tr><tr><td>54,000</td><td>17.664</td><td>51</td></tr></table>

From Table IV, it can be observed that although the training time increases with the scale of the problem, the inference time is almost instantaneous and grows approximately linearly with the problem scale. Therefore, the proposed method can meet the requirement of real-time decision-making for large-scale job arrival scenarios.

## V. CONCLUSION

In this paper, we develop a privacy-preserving job scheduling scheme among CSPs via environmental heterogeneity-aware FRL to achieve energy sharing. First, we establish a Dec-POMDP framework to model the CSPs’ job transfer and computing resource allocation decisions. Based on the proposed Dec-POMDP, we develop an FRL approach under the DTDE framework that facilitates collaborative scheduling among CSPs while safeguarding operation privacy. Subsequently, considering the heterogeneity in job attributes, job arrival distributions, and electricity prices among CSPs, we introduce a personalization scheme and integrate it into the FRL algorithm. Finally, we conduct multiple numerical experiments based on real-world data from Alibaba to demonstrate the effectiveness of collaborative energy sharing among CSPs.

## REFERENCES

[1] U. Arshad, M. Aleem, G. Srivastava, and J. C.-W. Lin, “Utilizing power consumption and SLA violations using dynamic VM consolidation in cloud data centers,” Renew. Sustain. Energy Rev., vol. 167, Oct. 2022, Art. no. 112782.

[2] M. Chen, C. Gao, M. Song, S. Chen, D. Li, and Q. Liu, “Internet data centers participating in demand response: A comprehensive review,” Renew. Sustain. Energy Rev., vol. 117, Jan. 2020, Art. no. 109466.

[3] A. Khosravi and R. Buyya, “Energy and carbon footprint-aware management of geo-distributed cloud data centers: A taxonomy, state of the art, and future directions,” in Sustainable Development: Concepts, Methodologies, Tools, and Applications. Hershey, PA, USA: IGI Global, 2018, pp. 1456–1475.

[4] C. Carrión, “Kubernetes scheduling: Taxonomy, ongoing issues and challenges,” ACM Comput. Surv., vol. 55, no. 7, pp. 1–37, 2022.

[5] N. Marathe, A. Gandhi, and J. M. Shah, “Docker swarm and kubernetes in cloud computing environment,” in Proc. 3rd Int. Conf. Trends Electron. Inform. (ICOEI), 2019, pp. 179–184.

[6] P. Acuña and P. Acuña, “Amazon EC2 container service,” Deploying Rails Docker, Kubernetes ECS. New York, NY, USA: Apress, 2016, pp. 69–98.

[7] D. Xu et al., “A review of research on multi-cloud management platforms,” in Proc. 7th Int. Conf. Inf. Sci., Comput. Technol. Transp., 2022, pp. 1–16.

[8] A. Pahlevan, P. G. Del Valle, and D. Atienza, “Exploiting CPU-load and data correlations in multi-objective VM placement for geo-distributed data centers,” in Proc. Design, Autom. Test Eur. Conf. Exhibit. (DATE), 2016, pp. 1333–1338.

[9] R. Tripathi, V. Sivaraman, and V. Tamarapalli, “Distributed cost-aware fault-tolerant load balancing in geo-distributed data centers,” IEEE Trans. Green Commun. Netw., vol. 6, no. 1, pp. 472–483, Mar. 2022.

[10] A. N. Toosi and R. Buyya, “A fuzzy logic-based controller for cost and energy efficient load balancing in geo-distributed data centers,” in Proc. IEEE/ACM 8th Int. Conf. Util. Cloud Comput. (UCC), 2015, pp. 186–194.

[11] A. Javadpour et al., “An energy-optimized embedded load balancing using DVFS computing in cloud data centers,” Comput. Commun., vol. 197, pp. 255–266, Jan. 2023.

[12] A. Forestiero, C. Mastroianni, M. Meo, G. Papuzzo, and M. Sheikhalishahi, “Hierarchical approach for efficient workload management in geo-distributed data centers,” IEEE Trans. Green Commun. Netw., vol. 1, no. 1, pp. 97–111, Mar. 2017.

[13] L. Mashayekhy, M. M. Nejad, D. Grosu, Q. Zhang, and W. Shi, “Energyaware scheduling of mapreduce jobs for big data applications,” IEEE Trans. Parallel Distrib. Syst., vol. 26, no. 10, pp. 2720–2733, Oct. 2014.

[14] R. Pradhan and S. C. Satapathy, “Energy aware genetic algorithm for independent task scheduling in heterogeneous multi-cloud environment,” J. Sci. Ind. Res., vol. 81, no. 7, pp. 1–9, 2022.

[15] Y. Qin, W. Han, Y. Yang, and W. Yang, “Joint energy optimization on the server and network sides for geo-distributed data centers,” J. Supercomput., vol. 77, pp. 7757–7790, Jan. 2021.

[16] A. H. Mahmud and S. Iyengar, “A distributed framework for carbon and cost aware geographical job scheduling in a hybrid data center infrastructure,” in Proc. IEEE Int. Conf. Auton. Comput. (ICAC), 2016, pp. 75–84.

[17] H. Yuan, J. Bi, and M. Zhou, “Geography-aware task scheduling for profit maximization in distributed green data centers,” IEEE Trans. Cloud Comput., vol. 10, no. 3, pp. 1864–1874, Jul.–Sep. 2020.

[18] C. Xu, K. Wang, P. Li, R. Xia, S. Guo, and M. Guo, “Renewable energy-aware big data analytics in geo-distributed data centers with reinforcement learning,” IEEE Trans. Netw. Sci. Eng., vol. 7, no. 1, pp. 205–215, Jan.–Mar. 2018.

[19] W. Huai, W. Huang, S. Jin, and Z. Qian, “Towards energy efficient scheduling for online tasks in cloud data centers based on DVFS,” in Proc. 9th Int. Conf. Innov. Mob. Internet Serv. Ubiquitous Comput., 2015, pp. 225–232.

[20] L. Gu, D. Zeng, A. Barnawi, S. Guo, and I. Stojmenovic, “Optimal task placement with QoS constraints in geo-distributed data centers using DVFS,” IEEE Trans. Comput., vol. 64, no. 7, pp. 2049–2059, Jul. 2015.

[21] Z. Zhou et al., “Carbon-aware load balancing for geo-distributed cloud services,” in Proc. IEEE 21st Int. Symp. Model., Anal. Simul. Comput. Telecommun. Syst., 2013, pp. 232–241.

[22] J. Masoudi, B. Barzegar, and H. Motameni, “Energy-aware virtual machine allocation in DVFS-enabled cloud data centers,” IEEE Access, vol. 10, pp. 3617–3630, 2021.

[23] M. H. Shirvani, A. M. Rahmani, and A. Sahafi, “A survey study on virtual machine migration and server consolidation techniques in DVFSenabled cloud datacenter: Taxonomy and challenges,” J. King Saud Univ.-Comput. Inf. Sci., vol. 32, no. 3, pp. 267–286, 2020.

[24] H. Liu et al., “Thermal-aware and DVFS-enabled big data task scheduling for data centers,” IEEE Trans. Big Data, vol. 4, no. 2, pp. 177–190, Jun. 2017.

[25] A. Alinezhadi, S. M. Sheikholeslami, S. K. Atapour, J. Abouei, and K. N. Plataniotis, “Intelligent privacy-preserving demand response for green data centers,” Electric Power Syst. Res., vol. 221, Aug. 2023, Art. no. 109394.

[26] G. Fan, L. Chen, H. Yu, and D. Liu, “Formally modeling and analyzing cost-aware job scheduling for cloud data center,” Softw., Pract. Exp., vol. 48, no. 9, pp. 1536–1559, 2018.

[27] J. Lin, D. Cui, Z. Peng, Q. Li, and J. He, “A two-stage framework for the multi-user multi-data center job scheduling and resource allocation,” IEEE Access, vol. 8, pp. 197863–197874, 2020.

[28] J. Yan et al., “Energy-aware systems for real-time job scheduling in cloud data centers: A deep reinforcement learning approach,” Comput. Elect. Eng., vol. 99, Apr. 2022, Art. no. 107688.

[29] W. Chen, Y. Xu, and X. Wu, “Deep reinforcement learning for multiresource multi-machine job scheduling,” 2017, arXiv:1711.07440.

[30] S. Liang, Z. Yang, F. Jin, and Y. Chen, “Data centers job scheduling with deep reinforcement learning,” in Proc. Pacific-Asia Conf. Knowl. Discov. Data Min., 2020, pp. 906–917.

[31] S. M. Errapotu et al., “Clock auction inspired privacy preserving emergency demand response in colocation data centers,” IEEE Trans. Dependable Secure Comput., vol. 17, no. 4, pp. 691–702, Jul./Aug. 2018.

[32] Y. Wen, J. Liu, W. Dou, X. Xu, B. Cao, and J. Chen, “Scheduling workflows with privacy protection constraints for big data applications on cloud,” Future Gener. Comput. Syst., vol. 108, pp. 1084–1091, Jul. 2020.

[33] Z. Wang et al., “Reinforcement learning based task scheduling for environmentally sustainable federated cloud computing,” J. Cloud Comput., vol. 12, no. 1, p. 174, 2023.

[34] A. Ray, K. Lafata, Z. Zhang, Y. Xiong, and K. Chakrabarty, “Privacypreserving job scheduler for GPU sharing,” in Proc. IEEE/ACM 23rd Int. Symp. Cluster, Cloud Internet Comput. Workshops (CCGridW), 2023, pp. 337–339.

[35] S. Mohseni, M. S. Pishvaee, and R. Dashti, “Privacy-preserving energy trading management in networked microgrids via data-driven robust optimization assisted by machine learning,” Sustain. Energy, Grids Netw., vol. 34, Jun. 2023, Art. no. 101011.

[36] Y. Xu, S. Ye, Z. Qin, X. Lin, J. Huangfu, and W. Zhou, “A coordinated optimal scheduling model with Nash bargaining for shared energy storage and multi-microgrids based on two-layer ADMM,” Sustain. Energy Technol. Assess., vol. 56, Mar. 2023, Art. no. 102996.

[37] Z.-P. Yuan, P. Li, Z.-L. Li, and J. Xia, “A fully distributed privacypreserving energy management system for networked microgrid cluster based on homomorphic encryption,” IEEE Trans. Smart Grid, vol. 15, no. 2, pp. 1735–1748, Mar. 2024.

[38] K. Rzadca et al., “Autopilot: Workload autoscaling at Google,” in Proc. 15th Eur. Conf. Comput. Syst., 2020, pp. 1–16.

[39] Y. Peng, Y. Bao, Y. Chen, C. Wu, C. Meng, and W. Lin, “Dl2: A deep learning-driven scheduler for deep learning clusters,” IEEE Trans. Parallel Distrib. Syst., vol. 32, no. 8, pp. 1947–1960, Aug. 2021.

[40] A. Verma, L. Pedrosa, M. Korupolu, D. Oppenheimer, E. Tune, and J. Wilkes, “Large-scale cluster management at Google with borg,” in Proc. 10th Eur. Conf. Comput. Syst., 2015, pp. 1–17.

[41] H. Mao, M. Alizadeh, I. Menache, and S. Kandula, “Resource management with deep reinforcement learning,” in Proc. 15th ACM Workshop Hot Topics Netw., 2016, pp. 50–56.

[42] S. Zhang, M. Xu, W. Y. B. Lim, and D. Niyato, “Sustainable AIGC workload scheduling of geo-distributed data Centers: A multi-agent reinforcement learning approach,” in Proc. IEEE Global Commun. Conf., 2023, pp. 3500–3505.

[43] F. Sattler, S. Wiedemann, K.-R. Müller, and W. Samek, “Robust and communication-efficient federated learning from non-IID data,” IEEE Trans. Neural Netw. Learn. Syst., vol. 31, no. 9, pp. 3400–3413, Sep. 2020.

[44] Y. Chen, Y. Ning, M. Slawski, and H. Rangwala, “Asynchronous online federated learning for edge devices with non-IID data,” in Proc. IEEE Int. Conf. Big Data (Big Data), 2020, pp. 15–24.

[45] M. Savi and F. Olivadese, “Short-term energy consumption forecasting at the edge: A federated learning approach,” IEEE Access, vol. 9, pp. 95949–95969, 2021.

[46] J. S. Nightingale, Y. Wang, F. Zobiri, and M. A. Mustafa, “Effect of clustering in federated learning on non-IID electricity consumption prediction,” in Proc. IEEE PES Innov. Smart Grid Technol. Conf. Eur., 2022, pp. 1–5.

[47] W. Liu et al., “Online job scheduling scheme for low-carbon data center operation: An information and energy nexus perspective,” Appl. Energy, vol. 338, May 2023, Art. no. 120918.

[48] H. Cao, H. Jin, X. Wu, S. Wu, and X. Shi, “DAGMap: Efficient and dependable scheduling of DAG workflow job in grid,” J. Supercomput., vol. 51, pp. 201–223, Feb. 2010.

[49] C. Bettstetter and J. Eberspacher, “Hop distances in homogeneous ad hoc networks,” in Proc. 57th IEEE Semiannu. Veh. Technol. Conf., vol. 4, 2003, pp. 2286–2290.

[50] “Alibaba cluster data.” 2021. Accessed: Jan. 6, 2024. [Online]. Available: https://github.com/alibaba/clusterdata

[51] H. Chen, Y. Zhang, M. C. Caramanis, and A. K. Coskun, “EnergyQARE: QoS-aware data center participation in smart grid regulation service reserve provision,” ACM Trans. Model. Perform. Eval. Comput. Syst., vol. 4, no. 1, pp. 1–31, 2019.

[52] J. Zhang, J. Chen, J. Luo, and A. Song, “Efficient location-aware data placement for data-intensive applications in geo-distributed scientific data centers,” Tsinghua Sci. Technol., vol. 21, no. 5, pp. 471–481, 2016.

[53] Z. Hu, B. Li, and J. Luo, “Time-and cost-efficient task scheduling across geo-distributed data centers,” IEEE Trans. Parallel Distrib. Syst., vol. 29, no. 3, pp. 705–718, Mar. 2018.

[54] J. Zhou et al., “Graph neural networks: A review of methods and applications,” AI Open, vol. 1, pp. 57–81, Jan. 2021.

[55] M. Fey, J. E. Lenssen, F. Weichert, and J. Leskovec, “GNNAutoScale: Scalable and expressive graph neural networks via historical embeddings,” in Proc. Int. Conf. Mach. Learn., 2021, pp. 3294–3304.

[56] H. Chen et al., “Self-aware personalized federated learning,” in Proc. Adv. Neural Inf. Process. Syst., vol. 35, 2022, pp. 20675–20688.

[57] S. Wang et al., “Adaptive federated learning in resource constrained edge computing systems,” IEEE J. Sel. Areas Commun., vol. 37, no. 6, pp. 1205–1221, Jun. 2019.

[58] I. Goodfellow, Y. Bengio, and A. Courville, Deep Learning. Cambridge, MA, USA: MIT press, 2016. [Online]. Available: http://www. deeplearningbook.org

[59] T. Li, A. K. Sahu, M. Zaheer, M. Sanjabi, A. Talwalkar, and V. Smith, “Federated optimization in heterogeneous networks,” in Proc. Mach. Learn. Syst., vol. 2, 2020, pp. 429–450.

[60] H. Fawzi, Topics in Convex Optimization (L16): Mathematical Tripos Part III Guide to Courses 2016–2017, Cambridge Univ., Cambridge, U.K., 2016.

[61] S. Smith, E. Elsen, and S. De, “On the generalization benefit of noise in stochastic gradient descent,” in Proc. Int. Conf. Mach. Learn., 2020, pp. 9058–9067.

[62] D. Buchaca, E. Romero, F. Mazzanti, and J. Delgado, “Stopping criteria in contrastive divergence: Alternatives to the reconstruction error,” 2013, arXiv:1312.6062.

[63] X. Glorot, A. Bordes, and Y. Bengio, “Deep sparse rectifier neural networks,” in Proc. 14th Int. Conf. Artif. Intell. Statist., 2011, pp. 315–323.

[64] V. François-Lavet, R. Fonteneau, and D. Ernst, “How to discount deep reinforcement learning: Towards new dynamic strategies,” 2015, arXiv:1512.02011.

[65] J. Schulman, F. Wolski, P. Dhariwal, A. Radford, and O. Klimov, “Proximal policy optimization algorithms,” 2017, arXiv:1707.06347.

![](sun2025_privacy_energy_sharing_assets/images/398fa1b349b36d0cacba8669aaa863cb60acaf91269372ecdc85dd2cbf755bc1.jpg)

Zhaohao Ding (Senior Member, IEEE) received the B.S. degree in electrical engineering and the B.A. degree in finance from Shandong University, Jinan, China, in 2010, and the Ph.D. degree in electrical engineering from the University of Texas at Arlington, Arlington, TX, USA, in 2015. He is currently a Professor with North China Electric Power University, Beijing, China. His research interests include power system planning and operation, power market, distributed resource management, and electric transportation system.

![](sun2025_privacy_energy_sharing_assets/images/98967979bd86b5f59ce18c5e69e1d76d993431f468a496373967835f5541f3de.jpg)

Yuejun Yan (Member, IEEE) received the Ph.D. degree from the University of Pennsylvania. She is currently the Technical Lead of Alibaba Cloud Global Data Center Sustainability Innovation. She is also the Lead Principal Investigator for Green Cloud Computing Project in ANGEL (Alibaba-NTU Global e-Sustainability CorpLab). Her research interest is on green AI and green cloud computing.

![](sun2025_privacy_energy_sharing_assets/images/e81307361f204e5397738c129f3e09974bf17173cbb55dcbfcd2eea0929d7b1e.jpg)

Zhaoyang Wang is currently the General Manager of Global Data Center, Alibaba Cloud. He leads global data center planning, delivery, research and development, and operations.

![](sun2025_privacy_energy_sharing_assets/images/8ccf615f45e4c8986407270ff732a7d5acab5d8ca873c17bb665b1a42f85ea2d.jpg)

Payman Dehghanian (Senior Member, IEEE) received the B.Sc. degree in electrical engineering from the University of Tehran, Tehran, Iran, in 2009, the M.Sc. degree in electrical engineering from the Sharif University of Technology, Tehran, in 2011, and the Ph.D. degree in electrical engineering from Texas A&M University, TX, USA, in 2017. He is currently an Assistant Professor with the Department of Electrical and Computer Engineering, George Washington University, Washington, DC, USA. His research interests include power system reliabilit and resilience assessment, data-informed decision-making for maintenance and asset management in electrical systems, and smart electricity grid applications. He was the recipient of the 2014 and 2015 IEEE Region 5 Outstanding Professional Achievement Award, the 2015 IEEE-HKN Outstanding Young Professional Award, the 2021 Early Career Award from the Washington Academy of Sciences, and the 2022 Early Career Researcher Award from George Washington University.

![](sun2025_privacy_energy_sharing_assets/images/495c6f5edb80ea60c7d1db7174bbea0d57b259548f4bbf92fa7c5f4a7005cc4d.jpg)  
Yimeng Sun (Member, IEEE) received the B.S. degree in electrical engineering and automation from North China Electric Power University, China, in 2021, where she is currently pursuing the Ph.D. degree in electrical engineering with the School of Electrical and Electronic Engineering. Her current research interests include power system optimization and demand side management.

![](sun2025_privacy_energy_sharing_assets/images/ac1943beb1e1566842f4134e2a9feae623d9a8fcb08be5d4e71ab7d22f141b4a.jpg)

Wei-Jen Lee (Life Fellow, IEEE) received the B.S. and M.S. degrees in electrical engineering from National Taiwan University, Taipei, Taiwan, in 1978 and 1980, respectively, and the Ph.D. degree in electrical engineering from the University of Texas at Arlington, Arlington, TX, USA, in 1985. In 1986, he joined the University of Texas at Arlington, where he is currently a Professor and the Chair of the Department of Electrical Engineering, and the Director of the Energy Systems Research Center. His research interests include power flow, transient and dynamic stability, voltage stability, short circuit, relay coordination, power quality analysis, arc flash hazards, electrical safety, renewable energy, and deregulation for utility companies. He is the Past President of IEEE Industry Applications Society and a Registered Professional Engineer in the State of Texas.