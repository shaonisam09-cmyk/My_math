# Data Center Job Scheduling and Energy Management Under Uncertain Environments

Zhaohao Ding , Senior Member, IEEE, Shijie Chen , Student Member, IEEE,

Yimeng Sun , Graduate Student Member, IEEE, Kun Shi, Jiaying Wang , Songsong Chen, Senior Member, IEEE, Tao Xiao, Yehan Wang , Student Member, IEEE, and Xuan Wei , Member, IEEE

Abstract—Data centers have become crucial infrastructure in the digital age, leading to a significant increase in energy consumption. Job scheduling stands out as an effective method to regulate the data center energy consumption by delaying job execution within resource availability and quality of service constraints. However, aleatoric uncertainties associated with incoming job information and real-time electricity market prices, and epistemic uncertainties inherent in the learning environment jointly present unique challenges for efficient job scheduling schemes. To tackle multiple types of uncertainties, we propose an efficient risk-aware job scheduling method for data centers in uncertain environments. Firstly, we formulate the data center job scheduling problem within a Markov framework incorporating job heterogeneity. To capture epistemic and aleatoric uncertainties, the policy function is reconstructed by integrating state-action value distributions with efficient exploration based on enhanced distributional reinforcement learning. Furthermore, to account for the risk preferences in data center decision-making, we include consideration of Conditional Value at Risk in the model. Numerical simulation results demonstrate that the proposed strategy can rapidly adapt to uncertain environments and help data centers make risk-aware job scheduling decisions.

Index Terms—Data center, job scheduling, reinforcement learning, quantile regression, CVaR.

## I. INTRODUCTION

W <sup>ITH</sup> <sup>the</sup> <sup>development</sup> <sup>of</sup> <sup>cloud</sup> <sup>computing,</sup> <sup>Big</sup>Data analytics, and machine learning, the electricity

Received 6 August 2024; revised 30 December 2024; accepted 11 February 2025. Date of publication 6 March 2025; date of current version 9 July 2025. Paper 2024-ESC-1143.R1, presented at the 2024 IEEE/IAS 60th Industrial and Commercial Power Systems Technical Conference, Las Vegas, NV, USA, May 19–23, and approved for publication in the IEEE TRANSACTIONS ON INDUSTRY APPLICATIONS by the Energy Systems Committee of the IEEE Industry Applications Society [DOI: 10.1109/ICPS60943.2024.10563375]. This work was supported by the State Grid Corporation of China Science and Technology Project under Grant 5400-202319244A-1-1-ZN. (Corresponding author: Yimeng Sun.)

Digital Object Identifier 10.1109/TIA.2025.3548576 consumption of data centers has been increasing rapidly [1]. In the United States, data centers consume 130 TWh of energy accounting for 2.5% of total energy consumption in 2022 [2]. Similarly in China, data center power consumption in 2021 reached 216.6 billion kWh, making up for 2.6% of overall societal energy consumption [3]. Additionally, global power consumption from data centers was roughly 460 TWh in 2022 [4]. As data center power consumption continues to grow, the costs of energy procurement from the electricity market are also on the rise. For instance, the annual energy bill for small data centers in the United States has reached around 13 billion dollars [5].

To reduce energy consumption costs of data centers, job scheduling stands out as an effective cost-reducing approach. Specifically, since offline jobs have lower priority and can be delayed, adjusting the execution time of offline jobs can effectively regulate power demand patterns [6], which brings opportunities for the data center to shift energy consumption from high-price periods to low-price periods by strategic job scheduling, thereby reducing overall energy costs. Therefore, well-designed job scheduling strategies are essential for effectively reducing energy costs in data centers.

To fully explore the potential of jobs with different priorities, existing data center job scheduling methods mainly fall into two categories: 1) mathematical optimization and 2) machine learning (ML) based methods. For the mathematical optimization based methods, Loganathan et al. [7] propose an energy-aware virtual machine available time job scheduling algorithm that assigns jobs to virtual machines while taking into account job classification and preemption. Ren et al. [8] introduce a provably efficient online scheduling algorithm based on Lyapunov optimization and mainly consider the resource demand of the jobs, which both energy cost and fairness among different organizations while adhering to queueing delay constraints. For ML-based methods, to name a few, Sun et al. [9] consider privacy protection and propose a job scheduling approach based on federated reinforcement learning. Tang et al. [10] combine the linear regression and wavelet neural network techniques into a prediction method to comprehensively reduce the energy consumption of cloud data center servers, networks, and cooling systems. Ran et al. [11] propose a DRL-based optimization framework, named DeepEE, to improve the energy efficiency for data centers by considering the IT and cooling systems concurrently. Che et al. [12] propose four scheduling methods based

Jiaying Wang and Tao Xiao are with Zhejiang Marketing Service Center, Hangzhou 311121, China (e-mail: wangjiayingee@zju.edu.cn; xjtuxt@ 163.com).

on DRL to automate the scheduling of large-scale workloads to cloud computing resources, concurrently reducing resource consumption and job waiting times. Ding et al. [13] propose a Q-learning-based job scheduling framework for energy-efficient cloud computing, involving a centralized job dispatcher at the cloud level and Q-learning-based schedulers at the virtual machine level for efficient job processing.

In comparison, mathematical optimization methods are limited to making decisions in small-scale scenarios, while MLbased methods can handle online decision-making problems in complex state spaces. Moreover, ML-based methods can continuously learn through interactions with the environment to make scheduling decisions for heterogeneous jobs, helping data centers adapt to dynamically changing job scheduling environments.

However, the aforementioned ML-based strategies primarily concentrate on deterministic job scheduling, neglecting the uncertainties inherent in the job scheduling process. On the one hand, due to the heterogeneity and randomness of jobs, data centers cannot obtain job information before the job arrives. On the other hand, electricity prices are also treated as an uncertain parameter, given the widely adopted ex-post real-time pricing [14]. These uncertainties increase the complexity of scheduling decisions. Because of variations in job execution times and resource requirements, agents need to make decisions based on heterogeneous job information, while fluctuations in electricity prices directly impact job costs. Consequently, considering the uncertainties in the data center operating environment, agents must continuously acquire and learn more information during the learning process to reflect the dynamic changes of the environment in real time, which also enhances the job scheduling model’s adaptability to dynamic environments. It is crucial to fully consider the uncertainty of job information and electricity prices during the data center job scheduling process, which presents fundamental challenges to the current ML-based job scheduling strategy designs.

Recently, distributional reinforcement learning (RL) has emerged as a promising solution by learning the uncertainty within the environment and fitting the distribution of rewards. Bellemare et al. [15] firstly introduce distributional RL and propose the C51 framework, which models the probabilistic state-action value function by minimizing Kullback-Leibler divergence. Dabney et al. [16] develop Quantile Regression Deep Q-Network (QR-DQN), a distributional RL framework that uses quantile regression to fit the probabilistic distribution of stateaction values to capture intrinsic uncertainties. Distributional RL is not only applied in electric vehicles [17] and energy storage devices [18] but also in a few studies on data center job scheduling to output distributions of values, thus considering the uncertainty within the environment. Li et al. [19] utilize distributional RL to model the value distribution of cumulative rewards, capturing the inherent randomness of the scheduling process caused by environmental stochasticity. Chen et al. [20] propose a data center job scheduling scheme based on distributional RL to capture the uncertainties in job arrivals and prices, thereby devising better operational strategies.

Although uncertainties related to job information and electricity prices have been considered in these job scheduling models, they overlook the inherent uncertainties in algorithms when applying distributional RL methods. Specifically, the uncertainty captured by the value distribution of cumulative rewards constructed using distributional RL can be classified into aleatoric uncertainty and epistemic uncertainty based on the source of uncertainty [21]. Aleatoric uncertainty is inherent to the environment, while epistemic uncertainty can be gradually reduced as the model learns and trains. Neglecting epistemic uncertainty may lead to reduced adaptability of the scheduling strategies in uncertain environments. Therefore, it is highly desirable to jointly address aleatoric uncertainty inherent in the job scheduling process and epistemic uncertainty during the training and learning process.

Furthermore, considering that the uncertainty of job information and electricity prices poses risks to the profit and operation of the data center, risk measurement plays a crucial role in optimizing data center job scheduling strategies in an uncertain environment, providing decision-makers with effective strategies based on risk preferences. Conditional Value at Risk (CVaR), as a common measurement for distribution [22], has been widely applied in the balance between profit maximization and risk reduction, which motivates us to combine CVaR and distributional RL to construct a risk-aware data center job scheduling model.

In this paper, we develop an efficient risk-aware job scheduling method for data centers based on enhanced distributional reinforcement learning. Based on enhanced distributional reinforcement learning, the policy function is reconstructed by integrating state-action value distributions with efficient exploration to capture epistemic and aleatoric uncertainties. To construct a risk data center job scheduling model, we adopt CVaR to reflect the data center’s risk preferences. Compared with previous studies on data center job scheduling, this paper proposes an efficient risk-aware job scheduling model for the data center in uncertain environments. The contributions of this paper are threefold:

1) A modeling framework for data center job scheduling is developed, accounting for uncertain operational environments and the processing of heterogeneous jobs. The proposed model incorporates multidimensional uncertainties in data center operations, enhancing its ability to adapt to diverse job characteristics, fluctuations in electricity market prices, and variations in job arrival patterns.

2) An uncertainty-aware optimization algorithm based on enhanced distributional reinforcement learning is proposed to comprehensively address the uncertainties in the job scheduling process. By reconstructing the policy function and incorporating state value distributions and efficient exploration strategies, the algorithm characterizes aleatoric uncertainty as a return distribution and expedites the reduction of epistemic uncertainty through efficient exploration.

3) To account for the risk preference of the data center, a distributional RL model incorporating CVaR into the reward is established to provide various risk combination strategies. By minimizing the weighted value of the reward distribution, the obtained data center job scheduling strategy can make the trade-off between reward and risk.

The rest of the article consists of the following sections. Section II describes the Markov decision process (MDP) formulation of the data center job scheduling problem. Section III presents a solution algorithm based on enhanced distributional reinforcement learning. Section IV demonstrates the result of case studies while Section V concludes this work.

## II. PROBLEM FORMULATION

In this section, we first provide a comprehensive description of the data center job scheduling process. Subsequently, we formalize it into an MDP framework.

## A. Data Center Job Scheduling Process

Generally, the data center aims to maximize its operational profit by employing an optimal deterministic continuous job scheduling strategy within a specified period. The profit is typically defined as the computation revenue from completing jobs minus the electricity expenses. We assume the data center only participates in the real-time market for real-time electricity purchases, thus facing the high dynamics and uncertainty of real-time market prices. Additionally, in the job scheduling of the data center, we consider that the data center can only obtain job information after the job arrives, hence the incoming job information is stochastic and uncertain. Furthermore, once a computing job begins, it cannot be interrupted. In other words, if there are jobs whose resource constraints are not met within the deadline-constrained period, other jobs cannot be stopped to satisfy this job. In this way, the data center will incur penalties in the form of fines. In the decision-making process of the data center, we consider the uncertainty of job arrival information and electricity prices. Considering these factors, the data center evaluates the associated returns and risks before making decisions and then executes the optimal job scheduling strategy.

As depicted in Fig. 1, the data center job scheduler operates as an agent designed to maximize operational profit through the optimization of job scheduling strategies. Upon receiving computing requests, the data center extracts relevant job attributes, including resource requirements, arrival time, execution duration, deadline, and computational revenue. This information, along with electricity prices and server resource utilization, is formulated as the state input for the decision network. The agent then determines the optimal scheduling action by selecting the one with the highest action value derived from the network’s output. After the job is executed, the data center updates its resource utilization status and job queue accordingly. Energy consumption costs and job execution outcomes are subsequently computed based on these updates and the prevailing electricity prices. Finally, these results serve as feedback, delivered to the agent as a reward to inform and refine the scheduling policy.

![](images/4680d37d71b4941fc47458053826836016ddb18ab74256d61df5295f3c90fd5f.jpg)  
Fig. 1. Data center job scheduling framework under uncertain environment.

## B. MDP Formulation

To model the abovementioned process, we formulate the data center job scheduling system as an MDP framework, as shown in Fig. 1. The MDP is defined by a tuple $\Gamma { = } \langle S , P , A , R , \gamma \rangle$ where S, P, A, R, γ are the sets of states, transition probability functions, sets of actions, reward functions, and a discount factor, respectively. The specific definitions are given as follows.

Agent: We define the job scheduling system of the data center as the agent. The agent is responsible for receiving packaged information from the data center as the state and making job scheduling decisions as an action. After the agent receives immediate rewards for executing the action, it optimizes strategy based on the updated information about states, actions, and rewards.

State: Suppose that the data center processes N jobs within $T$ time steps. Since the state transitions to the next state with the completion of the current job scheduling, we define the state $s _ { n }$ as a collection of the current scheduled job, the resource utilization of the data center server’s resource utilization, and the predicted electricity price, denoted as $s _ { n } = \langle j _ { n } , z _ { s e r , n } , \hat { p } \rangle$ . Here, $j _ { n } = \{ u _ { n } , t a _ { n } , t e _ { n } , t d _ { n } , e _ { n } \}$ for $n \in \{ 1 , 2 , \ldots , N \}$ <sup>ˆ =</sup>represents the information of the arrival job $n , z _ { s e r , n } = \{ z _ { n , 1 } , z _ { n , 2 } , . . . , z _ { n , T } \}$ denotes the resource utiliza-<sup>=</sup>tion of the data center server at each time step when job n is scheduled, and $\hat { p } = \{ \hat { p } _ { 1 } , \hat { p } _ { 2 } , \dots , \hat { p } _ { T } \}$ signifies the predicted electricity price at each time step. Additionally, $u _ { n } , t a _ { n } , t e _ { n } , t d _ { n } , e _ { n }$ denote the job resource utilization, arrival time, execution time, deadline, and computational revenue, respectively. Due to the differences in resource demand, arrival time, execution time, deadline, and computing reward, the state in which the data center schedules heterogeneous jobs varies. Based on different state information, the agent achieves optimized scheduling of heterogeneous jobs.

![](images/bdb0555acbf719c57315a4ae039d5b988aac7ae93b781c6a9cb5efab0deac5a6.jpg)  
Fig. 2. Feasible range of action selection for data center job scheduling.

Action: The action $a _ { n } = \langle t s _ { n } \rangle$ represents the time step at which the currently scheduled job begins execution, where $t s _ { n } \in$ $\{ 1 , 2 , \ldots , T \}$ represents a discrete variable. As shown in Fig. 2, we define the feasible range of $t s _ { n }$ that satisfies the resource utilization and QoS constraints as $\zeta _ { f , r }$ and $\zeta _ { f , q }$ respectively. Under both QoS and resource upper limit constraints, we can obtain the agent feasible action range $\zeta _ { f } = \zeta _ { f , q } \cap \zeta _ { f , r } .$ . Specifically, the execution time $t s _ { n }$ of a job cannot precede its arrival time $t a _ { n }$ in practical situations, i.e., $t s _ { n } \geq t a _ { n }$ . Additionally, considering QoS constraints, the execution time of a job must ensure its completion before the deadline, i.e., $t s _ { n } + t e _ { n } \leq t d _ { n }$ Therefore, the feasible range of actions under QoS constraints is $t a _ { n } \leq t s _ { n } \leq t d _ { n } - t e _ { n } .$ we define as $\zeta _ { f , q }$ . Considering resource constraints, the computing time of the job should not exceed the upper limit of resource utilization of the data center server, i.e., $z _ { n , t } + u _ { n } \leq z _ { s e r } ^ { u p } \forall t \in \zeta _ { e }$ , where $z _ { s e r } ^ { u p }$ represents the upper limit of server resource utilization and $\zeta _ { e } = [ t s _ { n } , t s _ { n } + t e _ { n } ]$ is the period of job execution.

When the action proposed by the agent falls within the feasible range $\zeta _ { f }$ , the current job will be scheduled to the specified time step $t s _ { n }$ to execute. At this point, the resource utilization $z _ { s e r , n }$ of the data center servers will change for the corresponding job execution period, i.e., $z _ { n + 1 , t } = z _ { n , t } + u _ { n } ~ \forall t \in \zeta _ { e }$ . Moreover, the current job $j _ { n }$ transitions to the next action $j _ { n + 1 }$ and repeats the aforementioned scheduling process. When the feasible action range $\zeta _ { f }$ is an empty set, it indicates that no actions can simultaneously satisfy QoS and resource upper limit constraints, resulting in a penalty for the data center. In this case, the resource utilization $z _ { s e r , n }$ unchanges, and the job $j _ { n }$ transitions to the next job $j _ { n + 1 }$ for continued scheduling.

Reward: The immediate reward $r _ { n }$ is the profit obtained from executing the current job, defined as the difference between revenue and cost, calculated by (1)-(3), as

$$
r _ {n} = e _ {n} - c _ {n} - b _ {n},\tag{1}
$$

$$
c _ {n} = \sum_ {t \in \zeta_ {e}} (p _ {t} * g _ {n}),\tag{2}
$$

$$
g _ {n} = P U E * \left(p _ {i d l e} + \left(p _ {p e a k} - p _ {i d l e}\right) * u _ {n}\right),\tag{3}
$$

where $\mathrm { c } _ { n }$ is the energy consumption cost of job $n , p _ { t }$ is the real electricity price at time step $t , g _ { n }$ is the energy consumption of job n per time step, $p _ { i d l e } , P U E$ is the power usage effectiveness of the data center and $p _ { p e a k }$ are the server power in idle state and peak state respectively. $b _ { n }$ is the penalty for job n failing to complete execution, reducing the immediate reward $r _ { n }$ far below that of successful execution.

Policy and $\gamma \dot { } .$ The ultimate goal is to maximize the total profit R from executing all jobs. R is the long-term cumulative discounted reward and can be obtained via (4).

$$
R = r _ {1} + \gamma r _ {2} + \dots + \gamma^ {N - 1} r _ {N},\tag{4}
$$

where $\gamma \in [ 0 , 1 ]$ is the discount factor.

Policy $\pi ( a _ { n } | s _ { n } )$ means the probability of taking action $a _ { n }$ in state $s _ { n } .$ . Corresponding to each policy, the state-action value function Q is expressed in (5). Using the values corresponding to each action output by the decision network $Q ( s _ { n } , a _ { n } ) a _ { n } \in$ $\{ 1 , 2 , \ldots , T \}$ , we can obtain the optimal policy for the current state, which is the purpose of the agent, as shown in (6).

$$
Q \left(s _ {n}, a _ {n}\right) = \mathbb {E} \left(r _ {n} + \gamma Q \left(s _ {n + 1}, a _ {n + 1}\right)\right),\tag{5}
$$

$$
\pi^ {*} \left(a _ {n}, s _ {n}\right) = \underset {a _ {n}} {\arg \max} Q \left(s _ {n}, a _ {n}\right) \quad a _ {n} \in \zeta_ {f}.\tag{6}
$$

## III. DISTRIBUTIONAL RL-BASED JOB SCHEDULIN APPROACH

To achieve uncertainty characterization and risk-aware job scheduling in data centers, we propose a policy optimization model that integrates distributional RL, efficient exploration (eff-explor) and CVaR. Firstly, we introduce an uncertainty characterization model for data centers based on distributional RL. Then we integrate eff-explor within the QR-DQN approach to comprehensively consider uncertainty. Lastly, we propose a risk-aware data center job scheduling policy model based on the characterized uncertainties and CVaR.

## A. Job Scheduling Based on Distributional RL

To characterize the impact of uncertainties in the job scheduling decision-making process, we define the concept of uncertainty as the dispersion of the cumulative distributional reward of the data center. In other words, the greater the dispersion of the cumulative distribution of rewards, the higher the variability of the random variable under the distribution, resulting in increased uncertainty in the data center’s returns [23].

Firstly, to characterize the uncertainties in the data center job scheduling process, we employ distributional RL to effectively capture uncertainties. Considering traditional RL focuses on the expected value of rewards $Q ( s _ { n } , a _ { n } )$ , distributional RL aims to learn the distribution of returns for state-action pairs, and we represent the distributional rewards as $\mathcal { R } ( s _ { n } , a _ { n } )$ . The relationship between $\textstyle { \mathcal { R } } ( s _ { n } , a _ { n } )$ and $Q ( s _ { n } , a _ { n } )$ is shown in (7). Additionally, the Bellman update formula is expressed in (8). Through (8), the stochastic return $\mathcal { R } ( s _ { n } , a _ { n } )$ is described by a recursive distributional equation, allowing for continuous updates to approximate the actual probability distribution of job scheduling return [15], [16].

$$
Q \left(s _ {n}, a _ {n}\right) = \mathbb {E} \left(\mathcal {R} \left(s _ {n}, a _ {n}\right)\right),\tag{7}
$$

$$
\mathcal {R} \left(s _ {n}, a _ {n}\right) \stackrel {{D}} {{=}} r _ {n} + \gamma \mathcal {R} \left(s _ {n + 1}, a _ {n + 1}\right).\tag{8}
$$

As one of the effective methods of distributional RL, we employ the QR-DQN approach to construct the data center job scheduling optimization model. According to the operation principle of RL, the job scheduling optimization model needs to use data obtained from interacting with the environment to update and learn. As shown in Fig. 3, the job scheduling model primarily consists of two neural networks. After the agent and the data center complete the interaction process described in Section II, the replay buffer stores information such as state $s _ { n } .$ , action $a _ { n }$ , reward $r _ { n }$ , and next state $s _ { n + 1 }$ . Subsequently, a mini-batch is randomly sampled from the replay buffer, and the relevant information is passed to both the online network and the target network. The output of the networks is the value corresponding to each of the $N _ { q }$ quantiles for each action. Based on the outputs, the value distribution of each action can be obtained.

![](images/f813ad635f8250495a07e9b0a313bbf815fc2e4aad64520a9ff579a138454a13.jpg)  
Fig. 3. Update and learning process illustration of the proposed model.

Subsequently, the online network can use the state $s _ { i }$ and action $a _ { i }$ arise from the mini-batch to obtain the predicted value distribution $\mathcal { R } ( s _ { i } , a _ { i } )$ . And the $\mathcal { R } ( s _ { i } , a _ { i } )$ is shown in the green spike plot. In the spike plot, the x-axis of each spike is the network output. The target network outputs the value distribution for all actions based on the next state $s _ { i + 1 }$ , shown as a probability density function (PDF). Then we select the optimal action’s value distribution $\mathscr { R } ( s _ { i + 1 } , a _ { i + 1 } )$ . Using $N _ { q }$ quantile parameters, the distributional reward can be transferred to the cumulative distribution function $F _ { \mathcal { R } } { } ^ { - 1 }$ (CDF), as expressed in (9). Subsequently, the update of $\textstyle { \mathcal { R } } ( s _ { n } , a _ { n } )$ in (8) can be written as (10) and we can obtain the target value distribution $\hat { \mathcal { R } } ( s _ { i } , a _ { i } )$ based on $\mathcal { R } ( s _ { i } , a _ { i } )$

$$
\mathcal {R} (s _ {n}, a _ {n}) _ {\tau_ {j}} := F _ {\mathcal {R}} ^ {- 1} (\hat {\tau} _ {j}), j = 1, 2, \ldots , N _ {q},\tag{9}
$$

$$
\mathcal {R} (s _ {n}, a _ {n}) _ {\tau_ {j}} \leftarrow \mathcal {R} (s _ {n}, a _ {n}) _ {\tau_ {j}}
$$

$$
+ \gamma \left(\hat {\tau} _ {j} - \mathbb {I} _ {\{r _ {n} + \gamma \mathcal {R} (s _ {n + 1}, a _ {n + 1}) _ {\tau_ {j}} <   \mathcal {R} (s _ {n}, a _ {n}) _ {\tau_ {j}} \}}\right),\tag{10}
$$

where $\begin{array} { r } { \hat { \tau } _ { j } : = \frac { \tau _ { j - 1 } + \tau _ { j } } { 2 } } \end{array}$ represents midpoints of the quantiles $\tau _ { j } =$ $\frac { j } { N _ { a } }$ and $\tau _ { 0 } = 0 ,$ <sup>I</sup> is the indicator function.

In Fig. 3, the blue plot shows the CDF of the predicted distribution $\mathcal { R } ( s _ { i } , a _ { i } )$ and the target distribution $\hat { \mathcal { R } } ( s _ { i } , a _ { i } )$ , with the <sup>( ) ( )</sup>blue shaded area indicating the difference. Within the algorithm, the Huber loss function $\mathcal { L } _ { k } ( u )$ is employed for updating the online network upon completion of all jobs in the data center, as depicted in the (11)-(12).

$$
\rho_ {\tau} ^ {k} (u) = \left\{ \begin{array}{l l} \frac {1}{2} u ^ {2} & i f | u | <   k \\ k \left(| u | - \frac {1}{2} u\right) & \text {otherwise,} \end{array} \right.\tag{11}
$$

![](images/d18a285f37db31c19bdaa79559851215e975eea38b2c55ac712ed15552034a7a.jpg)

Fig. 4. The sources of aleatoric uncertainty and epistemic uncertainty in the data center job scheduling process.  
![](images/af7f41eb82de7b15a25484eb4b92a9aca86df397bc7a5d8c11b77d967b2c0ee1.jpg)  
Fig. 5. Evolution of the aleatoric uncertainty and epistemic uncertainty.

$$
\mathcal {L} _ {k} (u) = \left| \tau - \delta_ {\{u <   0 \}} \right| \frac {\rho_ {\tau} ^ {k} (u)}{k},\tag{12}
$$

where k is constant and is the threshold value of the quantile Huber loss and $u = r _ { n } + \gamma \mathcal { R } ( s _ { n + 1 } , a _ { n + 1 } ) - \mathcal { R } ( s _ { n } , a _ { n } )$ is the TD error.

Therefore, based on the distributional RL approach, we can obtain the reward distribution of state-action pairs in the data center, thereby characterizing the uncertainty of the pairs.

## B. Incorporation of Efficient Exploration Method

Based on the reward distribution $\mathcal { R } ( s _ { n } , a _ { n } )$ obtained from subsection A, we propose a reconstructed policy function to comprehensively capture uncertainty.

As illustrated in Fig. 4, the data center is subject to two distinct types of uncertainty: epistemic uncertainty and aleatoric uncertainty [21], each stemming from different sources. Epistemic uncertainty arises from the discrepancy between the model’s estimated reward distribution and the true reward distribution, attributable to the model’s incomplete understanding of the uncertain environment. Notably, this type of uncertainty diminishes as the model gains more knowledge through exploration. In contrast, aleatoric uncertainty is intrinsic to the environment, exemplified by fluctuations in electricity prices and variability in job arrivals. This inherent uncertainty results in the data center’s profit being characterized by a distribution rather than a fixed value and remains unaffected by further model exploration. The evolution trends of these two uncertainties as exploration increases are depicted in Fig. 5. To effectively address both forms of uncertainty, we integrate efficient exploration with distributional RL, enabling a more comprehensive characterization of these uncertainties.

According to the characteristics of epistemic uncertainty and aleatoric uncertainty, we propose a reconstructed policy function that comprehensively considers the uncertainty of the data center’s operational environment, as shown in (13) and (14). Specifically, when the data center obtains state $s _ { n }$ , we use the distributional RL model to obtain the reward distributions for all actions. Generally, the actions with high uncertainty typically have dispersed value distributions. In other words, we can use the variance of the distribution to quantify the uncertainty associated with these actions [24]. Furthermore, we consider that the data center should explore actions with high variances to accelerate the decay of epistemic uncertainty. This is because exploring actions with high variance helps increase the agent’s information gain from the environment [25]. The feedback from high-variance actions fluctuates more, meaning they provide more diverse information, helping the agent gain a more comprehensive understanding of the dynamic changes in the environment, which accelerates the reduction of epistemic uncertainty. Therefore, it is natural to incorporate variance into the action selection process. As the number of model training iterations increases, the epistemic uncertainty in the reward distribution gradually diminishes to a negligible level. At this point, the data center focuses on achieving the ultimate goal of maximizing returns.

$$
\pi^ {*} (a _ {n}, s _ {n}) = \underset {a _ {n}} {\arg \max} \left[ Q (a _ {n}, s _ {n}) + d _ {e} \sigma_ {\mathcal {R} (s _ {n}, a _ {n})} \right],\tag{13}
$$

where $\sigma _ { \mathcal { R } ( s _ { n } , a _ { n } ) }$ is the variance of the reward distributional $\mathcal { R } ( s _ { n } , a _ { n } ) , Q ( s _ { n } , a _ { n } )$ represents the expectation of reward distribution $\mathcal { R } ( s _ { n } , a _ { n } )$ <sup>)</sup>, and $d _ { e }$ is a coefficient that decays with exploration. From the classical QR theory, it is known that the parametric uncertainty decays at the following rate [26]:

$$
d _ {e} = d \sqrt {\frac {\log e}{e}},\tag{14}
$$

where d is a constant factor, and e represents the training step, which increases as the model trains. Once the model converges, we define the total number of training steps as MaxEpisodes. It also corresponds to the maximum value of e.

Based on the proposed policy function, the job scheduling model is capable of enabling the model to quickly converge to a distribution that closely approximates the true distribution and finding the optimal balance between exploration and exploitation. Specifically, during the initial exploration phase, the data center’s exploration of the environment is still insufficient. At this point, the data center will prioritize high-variance, highuncertainty actions to effectively explore the environment and discover potentially high-reward strategies. As the number of training steps e increases, the coefficient $d _ { e }$ decreases. The data center’s action selection strategy gradually shifts towards more stable actions with higher expected rewards to maximize returns.

## C. Risk-Aware Data Center Job Scheduling Approach

Due to the aforementioned uncertainties in the data center operating environment, the scheduling decisions are exposed to risks. On the one hand, uncertainty in job information can lead to risks such as compromised service quality and resource wastage. On the other hand, uncertainty in electricity prices exposes the data center to the risk of higher electricity costs. Given that CVaR is a widely used [27], [28], [29] and consistent risk metric [30], we integrate the uncertainty characterization model with the CVaR approach to enable risk-aware decision-making in data center job scheduling under the uncertain environment.

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1: Risk-aware data center job scheduling approach based on enhanced distributional RL.

Initialize the online network, target network, environment, number of quantile samples  $N_{q}$ , the experiences number  $N_{e}$ , minibatch size  $N_{b}$ , and the target network update frequency F;

for episode = 1:MaxEpisodes do

    for n = 1: N do
    Get state  $s_{n}$  from the environment;
    Choose an action  $a_{n}$  by (17) according to the  $\varepsilon$ -greedy policy;
    Execute the action  $a_{n}$ ;
    State  $s_{n}$  transit to the next state  $s_{n+1}$ ;
    Calculate the reward  $r_{n}$  by (1)-(3);
    Store state  $s_{n}$ , action  $a_{n}$ , reward  $r_{n}$ , next state  $s_{n+1}$  as a trajectory in replay buffer;
    if  $N_{e} &gt; N_{b}$  then
    Sample  $N_{b}$  trajectories from replay buffer;
    Uniformly sample out  $N_{q}$  quartiles  $[\tau_{1}, \tau_{2}, ..., \tau_{N_{q}}]$  in the interval [0,1];
    Calculate the quantile Huber loss by (11-12);
    Update the online network using  $\nabla L_{QR}^{k}$ ;
    if episode &gt; F then
    | Update the target network;
    end
    end
    end
end
</div>

Using the uncertainty characterization model, we can obtain a usable and precise reward distribution for job scheduling action. Based on the obtained distribution, the standard distributional RL framework typically learns the strategy by utilizing the mean of the return distribution which is the risk-neutral strategy. To develop a more comprehensive data center job scheduling model, we integrate the data center’s risk preferences into the established uncertainty-characterization model, extending it to learn risk-sensitive strategies. Risk refers to any uncertainty regarding future possible outcomes, and in this paper, we use the return distribution to measure risk. A popular method for risk measurement is the CVaR, which is easy to implement and empirically effective [31].

For the random variable R, the VaR at confidence level $\alpha \in$ 0, 1 is defined as:

$$
V a R _ {\alpha} (\mathcal {R}) := \inf \{\mathcal {R} | F (\mathcal {R}) \geq \alpha \} = F ^ {- 1} (\alpha),\tag{15}
$$

which is the definition of the quantile function shown in (9).

As an extension of VaR, CVaR is defined as the conditional mean of the tail distribution, as shown in (16).

$$
C V a R _ {\alpha} (\mathcal {R}) := E \left[ \mathcal {R} | \mathcal {R} \leq V a R _ {\alpha} (x) \right].\tag{16}
$$

According to (13), when the data center job scheduling model is in the later stages of exploration, the policy is trained to maximize the mean return. To consider the preferences of the data center, we integrate the CVaR method into the job scheduling model. Therefore, the job scheduling action selection is updated to:

TABLE I  
THE PARAMETER SETTINGS

<table><tr><td>Paramete</td><td>Value</td><td>Paramete</td><td>Value</td></tr><tr><td> $\alpha$ </td><td>95%</td><td> $p_{idle}$ </td><td>100</td></tr><tr><td> $\gamma$ </td><td>0.99</td><td> $p_{peak}$ </td><td>200</td></tr><tr><td> $N_b$ </td><td>32</td><td>Learning rate</td><td>0.001</td></tr><tr><td> $N_q$ </td><td>32</td><td>Fully connected layers</td><td>64, 128, 32</td></tr></table>

TABLE II  
THE PARAMETER SETTING OF JOBS

<table><tr><td>Parameters</td><td>Value</td></tr><tr><td>Resource utilization  $u_n$ </td><td>0.05-0.12</td></tr><tr><td>Execution time  $te_n$ </td><td>1-3 time steps</td></tr><tr><td>Deadline  $td_n$ </td><td> $tan_n+12$ </td></tr><tr><td>Computational revenue  $e_n$ </td><td> $(u_n*(p_{peak}-p_{idle})+p_{idle})*$  $PUE*40*te_n$ </td></tr></table>

![](images/bc2c8f2726a471d6091f251008f8ba0e2f5112a55589c5d9527cd89d3ea1e464.jpg)  
Fig. 6. Arrival patterns of computing jobs.

$$
\begin{array}{r l} \pi^ {*} (s _ {n}, a _ {n}) = & \underset {a _ {n}} {\arg \max} (1 - \beta) Q (s _ {n}, a _ {n}) \\ & + d _ {e} \sigma_ {\mathcal {R} (s _ {n}, a _ {n})} + \beta C V a R _ {\alpha} (\mathcal {R}), \end{array}\tag{17}
$$

where $\beta$ is a constant used to measure the weight of CVaR in action selection. The larger the confidence level α, the more risk-averse the data center [32].

The training process for the risk-aware data center job scheduling model based on Ehanced Distributional RL is illustrated in Algorithm 1.

## IV. CASE STUDY

## A. Experiment Settings

In the experimental system, the parameter settings are shown in Tables I and II, and the computing job arrival patterns are shown in Fig. 6. Additionally, the electricity prices are obtained from the PJM data [33], and we simplify the predicted electricity prices as $\hat { p } _ { t } = \eta p _ { t }$ , where $\eta \in [ 0 . 9 5 - 1 . 0 5 ]$ [34]. The activation function between layers is ReLU. To better compare the proposed strategy, we contrast it with the first in first out (FIFO) strategy and double deep Q-network (DDQN) strategy. In the FIFO strategy, the jobs in the data center are queued in the order of their arrival, with the earliest arriving jobs being executed first [35]. In the DDQN strategy, the agent uses the DDQN algorithm for data center job scheduling.

![](images/ec3e8d2da34d99a0889a423116c4d1508258e18aeb86565eec5c63376fccb8f4.jpg)  
Fig. 7. Data center job scheduling results under the proposed strategy $( \beta = 0 )$

![](images/8931aa13061a8f9701264a56c3d406ceeb59719acc62acdf6a4692b13927ff0d.jpg)  
Fig. 8. Data center resource job scheduling results under the proposed strategy at different electricity price (β = 0).

![](images/13631df40ce6264de73e52d1f59277b6d758b17a0305000b15af77d991bb36e7.jpg)  
Fig. 9. Data center job scheduling results under FIFO strategy.

## B. Analysis of Data Center Job Scheduling Results in Uncertain Environments

In this subsection, we compare and analyze the data center job scheduling results under different strategies, and demonstrate the evolution of epistemic and aleatoric uncertainties under the proposed strategy.

As shown in Figs. 7 and 8, we present the data center job scheduling results under the proposed strategy. Fig. 7 shows that under the proposed strategy, the data center is able to adjust resource usage during high electricity price periods (t 14 and <sup>=</sup>t  15) to reduce energy consumption while scheduling jobs to the low electricity price periods to complete the job execution. The proposed method effectively manages energy consumption under varying electricity prices, as shown in Fig. 8. By reducing energy use during high-price periods and increasing it during low-price periods, the proposed method optimizes the overall energy consumption curve. This highlights its ability to handle electricity price uncertainties and flexibly schedule heterogeneous jobs during low-price periods, significantly lowering the data center’s total energy cost.

In contrast, as shown in Fig. 9, the FIFO strategy schedules jobs to be executed immediately upon arrival within the constraints. When $t < 9$ , as time steps increase, the resource usage of the data center initially rises. When $t > 1 4$ , the number of jobs

![](images/a48267065dc57a3fcb5d84eec36785b16f1a8e63b0a6d3279a484c31209b58c0.jpg)

(a)  
![](images/a97b355a5967e26ee4bb2c850f1045b0f11ab1e9b4c48e947447bb78289a6640.jpg)  
(b)  
Fig. 10. Data center job scheduling results under (a) DDQN model and (b) QR-DQN model.

decreases, but resource usage is nearly constant. Subsequently, resource usage begins to decline at $t = 1 9 .$ This can be explained by the accumulation of jobs during periods of high job arrival rates, where some jobs cannot be executed immediately due to resource constraints. Thus, even when job arrivals are lower after $\rangle > 1 4 .$ , there is still considerable resource usage in the data center. During the high electricity price period $t = 1 4 \tan t = 1 5$ resource usage under the FIFO strategy does not significantly decrease, resulting in substantial energy costs. Therefore, as shown in Table II, the profit obtained by the data center under the FIFO strategy is much lower than that under the proposed strategy. Additionally, we present the job scheduling results of the QR-DQN model and the DDQN model for the data center, as shown in Fig. 10. In Fig. 10(a), under the DDQN model, the resource usage curve of the data center maintains a relatively high value during the high electricity price periods at $t = 1 5$ . This is because the DDQN model cannot perceive the uncertainties in job information and electricity prices during job scheduling. Therefore, when facing heterogeneous jobs and fluctuating electricity prices, the DDQN model’s performance is far inferior to distributional RL. In Fig. 10(b), we can see that the QR-DQN model is similar to the proposed strategy, and adjusts the job scheduling strategy to reduce resource usage during the high electricity price period at $t = 1 5$ . Different from Fig. 8, the resource usage of the data center is lower at $t = 1 2 , t = 1 4$ , and $t = 2 0 !$ only at $t = 2 4$ is the resource usage higher than that of the proposed strategy. This can be explained by the QR-DQN model reducing resource usage at these time steps to lower total energy costs. However, this leads to job accumulation and the resource usage at $t = 2 4$ is higher than that of the proposed strategy to execute as many jobs as possible. Finally, some jobs remain unexecuted, resulting in penalties for the data center, which causes the total profit of the QR-DQN model to be lower than that of the proposed strategy, as shown in Table III. Therefore, QR-DQN can perceive uncertainties in job information and electricity prices to strategically schedule jobs, but it still has certain biases that lead to lower profit.

TABLE III  
THE TOTAL PROFIT UNDER THE DIFFERENT STRATEGIES

<table><tr><td>Strategy</td><td>Total profit/$</td></tr><tr><td>Proposed strategy( $\beta = 0$ )</td><td>158739.85</td></tr><tr><td>QR-DQN strategy</td><td>158698.86</td></tr><tr><td>DDQN strategy</td><td>138719.06</td></tr><tr><td>FIFO strategy</td><td>132435.00</td></tr></table>

![](images/8cf4c84474ab851a2ad1b3faff7a01de71b90c98577271bcd19a683f1af2b3cf.jpg)  
Fig. 11. The evolution of epistemic uncertainty.

![](images/9f1968534f98ccb736040dff142b78c286c76b1f414c42c4330bde4037a32168.jpg)

![](images/c52002c5826fe5db7ca1ae447c6fdfed07769943aa2469498a9896c07dac185a.jpg)

![](images/e3aabf7dbfe925e5b48a13a2f6dee001c302fb22c507e7d863a9a5efb90f3dc8.jpg)

![](images/6630d174ea5dd2646517916a2acffd48b85ef382de1b16acf5038c5c5225aa8b.jpg)  
Fig. 12. The evolution of aleatoric uncertainty during training under the proposed model.

Table III shows that the proposed method achieves the highest operational profit compared to QR-DQN, DDQN, and FIFO strategies. The QR-DQN strategy fails to incorporate epistemic uncertainty, leading to biased reward distributions that hinder effective strategy optimization. Similarly, the DDQN strategy exhibits limited adaptability and decision-making precision due to its insufficient consideration of uncertainty. FIFO lacks energy-awareness and scheduling optimization, resulting in lower profit. In contrast, the proposed method incorporates both epistemic and aleatoric uncertainties, enabling energy-aware scheduling in uncertain environments and improving data center profit.

![](images/8a68c8e8df09c64a0876a6b5900737cdb9930d6cf0ccbfbc159e185920fc83c5.jpg)

Fig. 13. Data center job scheduling results under the proposed strategy $( \beta =$ 0.5).  
![](images/a50898db5126c454f671548b85d1d365ce6b69540ce438b1d3565fdc330247be.jpg)  
Fig. 14. The cumulated profit of data center $( \beta = 0 . 7 )$

As shown in Figs. 11 and 12, we explicitly quantify and analyze the epistemic and aleatoric uncertainties of the proposed model during the learning process. Epistemic uncertainty is characterized by the difference between the model’s output distribution and the true distribution. The trend of the Wasserstein-1 distance [36] between these distributions serves as a metric for changes in epistemic uncertainty. Fig. 11 demonstrates that, compared to the version without the efficient exploration module, the proposed strategy reduces epistemic uncertainty more rapidly, enabling the model to learn environmental information more effectively to support scheduling decisions.

Fig. 12 illustrates the evolution of the job scheduling reward distribution, reflecting the learning process for aleatoric uncertainty. The proposed method captures environmental uncertainty and represents it as a distribution. Combining these two aspects, the proposed model characterizes and addresses both types of environmental uncertainty, enabling efficient job scheduling in uncertain environments.

## C. Risk-Aware Job Scheduling Strategy

In this subsection, we conduct numerical experiments to illustrate the impact of risk-aware operation for data center job scheduling.

In terms of the proposed strategy’s risk perception, Fig. 13 shows the data center job scheduling results under a risk-averse preference with $\beta = 0 . 5$ . Under the risk-averse strategy, the data center does not schedule jobs during the low electricity price periods at $t = 7$ and $t = 8$ . This is because, although the electricity price is low in this price curve, it corresponds to a high electricity price period in other price curves. Therefore, under this strategy, the data center adopts a different approach compared to the risk-neutral strategy to avoid the high-cost risk.

As shown in Figs. 14 and 15, we examined the probability distribution of the total profit of the data center under the same heterogeneous job and electricity price curve. The probability distribution indicates that risk-averse data centers have lower estimated reward values while risk-seeking data centers have higher estimated values. These differences explain the varying scheduling strategies seen in Figs. 7 and 13.

![](images/938c56f1533e3296a879f07cf1e59813c377e7dfa4fe75902a7b68e7cf54552c.jpg)  
Fig. 15. The cumulated profit of data center $( \beta = 0 . 3 )$

![](images/f15639b80efe001f44f52294ed99842224100a51f6fdb8c16ec37d8c4c30a3ab.jpg)  
Fig. 16. Data center profits of the proposed risk-aware job scheduling approach under different β.

![](images/3c85a149f5e854b03c3837b5b5b12134fabe4939886e1fee4b41fc64272199a0.jpg)  
Fig. 17. The cumulative reward during the training process with different methods.

Additionally, we compared the total profit of the data center under different risk-averse preferences, as illustrated in Fig. 16. It is evident that as the data center’s risk-averse degree increases, the total profit shows a decreasing trend. This is because when the data center adopts risk-averse strategies, the selection of actions becomes more conservative, which does not mean the decision is optimal or yields the highest economic returns. The probability of high profit decreases, resulting in an overall decline in average total profit.

## D. Performance Comparison and Analysis

In this subsection, we compare and analyze the proposed algorithm with other algorithms from multiple dimensions, highlighting the advantages of the proposed algorithm in data center job scheduling.

As shown in Fig. 17, we compared the cumulative rewards of the proposed model with other models within the same environment. It can be observed that the cumulative rewards of all three algorithms initially rise and then level off with the increase in training episodes, indicating that the algorithms converge after episode 2000. The proposed strategy achieves the highest cumulative reward, followed by QR-DQN, with DDQN having the lowest. This demonstrates that the proposed data center job scheduling strategy is superior to the strategies of the other two algorithms. Additionally, we can observe that the volatility of cumulative rewards after convergence is higher for DDQN compared to the other two algorithms. This is because DDQN algorithms’ learning expectations cannot effectively account for uncertainties in the environment. Therefore, the proposed strategy is not only a superior job scheduling strategy but also exhibits more stability than traditional RL algorithms. Moreover, in the uncertain environment of the data center, as the strategy converges, the reward of the proposed method can remain within a high range and fluctuate slightly.

TABLE IV  
THE COMPARATIVE RESULTS OF THE PROPOSED MODEL AND THE MATHEMATICAL MODEL

<table><tr><td></td><td>Proposed model</td><td>Mathematical programming model</td></tr><tr><td>Total profit</td><td>103347$</td><td>104525$</td></tr><tr><td>CVaR (α = 95%)</td><td>217.35$</td><td>256.54$</td></tr><tr><td>Optimization time (N = 60)</td><td>0.14s</td><td>460.40s</td></tr><tr><td>Optimization time (N = 120)</td><td>0.28s</td><td>4063.25s</td></tr></table>

TABLE V

THE TRAINING TIME AND INFERENCE TIME OF THE PROPOSED MODEL

<table><tr><td></td><td>N=60</td><td>N=120</td><td>N=200</td><td>N=300</td></tr><tr><td>Training time</td><td>659.80s</td><td>936.97s</td><td>2097.96s</td><td>4416.45s</td></tr><tr><td>Inference time</td><td>0.14s</td><td>0.21s</td><td>1.27s</td><td>1.77s</td></tr></table>

We compare the scheduling performance of the proposed strategy with a mathematical programming-based model [37] under the same conditions. As shown in Table IV, the proposed model achieves results that are nearly identical to those of the mathematical programming model, with only a slight difference of 1.13%. However, the proposed model significantly outperforms in terms of optimization time. Notably, as the problem scale increases, the mathematical programming model experiences exponential growth in computation time due to its computing complexity, while the proposed method maintains near-linear growth. This adaptability makes the proposed model well-suited for online decision-making in dynamic data center environments that demand rapid and efficient responses.

To further demonstrate the scalability of the proposed method under different scales, we compared the training and inference times of the model in scenarios with varying job numbers N. As shown in Table V, although the training time increases with the scale of the problem, the inference time remains nearly instantaneous and grows approximately linearly with the problem scale. Therefore, the proposed method exhibits excellent scalability and low computational complexity, making it highly suitable for large-scale data center job scheduling environments.

## V. CONCLUSION

This paper presents an efficient job scheduling method for data centers, addressing uncertainties and heterogeneous jobs to optimize energy consumption. First, the problem is formulated as a MDP incorporating job heterogeneity and operation uncertainty. To capture epistemic and aleatoric uncertainties, the policy function is reconstructed by integrating state-action value distributions with efficient exploration (eff-explor). Building upon this approach, we combine CVaR with the uncertainty characterization to enable risk-aware decision-making for job scheduling. Simulation results demonstrate the proposed method’s effectiveness in managing uncertainties in job arrival and electricity price fluctuations, improving operation profit. In the future, we could explore incorporating multiobjective optimization to balance energy efficiency, cost reduction, and system reliability while further enhancing the model’s robustness in handling more complex and dynamic uncertainties.

## REFERENCES

[1] Z. Ding, Y. Cao, L. Xie, Y. Lu, and P. Wang, “Integrated stochastic energy management for data center microgrid considering waste heat recovery,” IEEE Trans. Ind. Appl., vol. 55, no. 3, pp. 2198–2207, May/Jun. 2019.

[2] International Energy Agency, Accessed: Sep. 13, 2023. [Online]. Available: https://www.iea.org/reports/data-centres-and-datatransmissionnetworks

[3] China Environmental News, Accessed: Feb. 10, 2022. [Online]. Available: http://epaper.cenews.com.cn/html/2023-12/29/node\_2.htm

[4] “Utilities face challenges, opportunities from AI-driven data center power demand growth: Report,” Accessed: Apr. 01, 2024. [Online]. Available: https://www.spglobal.com/en

[5] M. Patel, A. Upadhyay, F. Battaglia, F. Singer, and M. Ohadi, “Energy audit of data centers and server rooms on an academic campus—A case study,” in Proc. 18th IEEE Intersociety Conf. Thermal Thermomechanical Phenomena Electron. Syst., 2019, pp. 1289–1295.

[6] J. Xu, Z. Hao, R. Zhang, and X. Sun, “A method based on the combination of laxity and ant colony system for cloud-fog task scheduling,” IEEE Access, vol. 7, pp. 116218–116226, 2019.

[7] S. Loganathan, R. D. Saravanan, and S. Mukherjee, “Energy aware resource management and job scheduling in cloud datacenter,” Int. J. Intell. Eng. Syst., vol. 10, no. 4, pp. 175–184, 2017.

[8] S. Ren, Y. He, and F. Xu, “Provably-efficient job scheduling for energy and fairness in geographically distributed data centers,” in Proc. IEEE 32nd Int. Conf. Distrib. Comput. Syst., 2012, pp. 22–31.

[9] Y. Sun, Z. Ding, Y. Yan, Z. Wang, P. Dehghanian, and W.-J. Lee, “Privacypreserving energy sharing among cloud service providers via collaborative job scheduling,” IEEE Trans. Smart Grid, vol. 16, no. 2, pp. 1168–1180, Mar. 2025.

[10] X. Tang, X. Liao, J. Zheng, and X. Yang, “Energy efficient job scheduling with workload prediction on cloud data center,” Cluster Comput., vol. 21, no. 3, pp. 1581–1593, 2018.

[11] Y. Ran, H. Hu, X. Zhou, and Y. Wen, “DeepEE: Joint optimization of job scheduling and cooling control for data center energy efficiency using deep reinforcement learning,” in Proc. IEEE 39th Int. Conf. Distrib. Comput. Syst., 2019, pp. 645–655.

[12] H. Che, Z. Bai, R. Zuo, and H. Li, “A deep reinforcement learning approach to the optimization of data center task scheduling,” Complexity, vol. 2020, pp. 1–12, 2020.

[13] D. Ding, X. Fan, Y. Zhao, K. Kang, Q. Yin, and J. Zeng, “Q-learning based dynamic task scheduling for energy-efficient cloud computing,” Future Gener. Comput. Syst., vol. 108, pp. 361–371, 2020.

[14] T. Zheng and E. Litvinov, “On ex post pricing in the real-time electricity market,” IEEE Trans. Power Syst., vol. 26, no. 1, pp. 153–164, Feb. 2011.

[15] M. G. Bellemare, W. Dabney, and R. Munos, “A distributional perspective on reinforcement learning,” in Proc. Int. Conf. Mach. Learn., 2017, pp. 449–458.

[16] W. Dabney, M. Rowland, M. Bellemare, and R. Munos, “Distributional reinforcement learning with quantile regression,” in Proc. 32nd AAAI Conf. Artif. Intell. 30th Innov. Appl. Artif. Intell. Conf. 8th AAAI Symp. Educ. Adv. Artif. Intell., 2018, pp. 2892–2901.

[17] Y. Sun, Z. Ding, Z. Hu, and W.-J. Lee, “Risk-aware operation modeling for ride-hailing fleet in order grabbing mode: A distributional reinforcement learning approach,” IEEE Trans. Smart Grid, vol. 14, no. 5, pp. 3913–3926, Sep. 2023.

[18] D. K. Panda, O. Turner, S. Das, and M. Abusara, “Prioritized experience replay based deep distributional reinforcement learning for battery operation in microgrids,” J. Cleaner Prod., vol. 434, 2024, Art. no. 139947.

[19] T. Li, S. Ying, Y. Zhao, and J. Shang, “Batch jobs load balancing scheduling in cloud computing using distributional reinforcement learning,” IEEE Trans. Parallel Distrib. Syst., vol. 35, no. 1, pp. 169–185, Jan. 2024.

[20] K. Shi, S. Chen, S. Chen, Y. Sun, and Z. Ding, “Energy-aware data center job scheduling scheme under uncertain environment,” in Proc. IEEE/IAS 60th Ind. Commercial Power Syst. Tech. Conf., 2024, pp. 1–6.

[21] A. Kendall and Y. Gal, “What uncertainties do we need in Bayesian deep learning for computer vision?,” in Proc. 31st Int. Conf. Neural Inf. Process. Syst., 2017, pp. 5580–5590.

[22] R. T. Rockafellar and S. Uryasev, “Conditional value-at-risk for general loss distributions,” J. Bank. Finance, vol. 26, no. 7, pp. 1443–1471, 2002.

[23] “Terminology in analytical measurement-introduction to VIM 3,” Jun. 23, 2019. [Online]. Available: https://www.eurachem.org/images/stories/ Guides/pdf/TAM\_2011\_Final\_web.pdf

[24] D. Koraˇcin, A. Panorska, V. Isakov, J. S. Touma, and J. Swall, “A statistical approach for estimating uncertainty in dispersion modeling: An example of application in southwestern USA,” Atmosph. Environ., vol. 41, no. 3, pp. 617–628, 2007.

[25] B. Azhagusundari et al., “Feature selection based on information gain,” Int. J. Innov. Technol. Exploring Eng., vol. 2, no. 2, pp. 18–21, 2013.

[26] R. Koenker, Quantile Regression[M]. Cambridge, U.K.: Cambridge Univ. Press, 2005.

[27] A. Hakobyan, G. C. Kim, and I. Yang, “Risk-aware motion planning and control using CVaR-constrained optimization,” IEEE Robot. Automat. Lett., vol. 4, no. 4, pp. 3924–3931, Oct. 2019.

[28] J. Yin, Z. Zhang, and P. Tsiotras, “Risk-aware model predictive path integral control using conditional value-at-risk,” in Proc. 2023 IEEE Int. Conf. Robot. Automat., 2023, pp. 7937–7943.

[29] Y. Chow, A. Tamar, S. Mannor, and M. Pavone, “Risk-sensitive and robust decision-making: A CVaR optimization approach,” in Proc. 29th Int. Conf. Neural Inf. Process. Syst., 2015, pp. 1522–1530. [Online]. Available: https://proceedings.neurips.cc/paper\_files/paper/2015/ file/64223ccf70bbb65a3a4aceac37e21016-Paper.pdf

[30] A. Dixit et al., “STEP: Stochastic traversability evaluation and planning for risk-aware off-road navigation,” Robot.: Sci. Syst. XVII, 2021.

[31] Y. Chow and M. Ghavamzadeh, “Algorithms for CVaR optimization in MDPS,” in Proc. 28th Int. Conf. Neural Inf. Process. Syst., 2014, pp. 3509– 3517.

[32] J. Su, S. Mehrani, P. Dehghanian, and M. A. Lejeune, “Quasi second-order stochastic dominance model for balancing wildfire risks and power outages due to proactive public safety de-energizations,” IEEE Trans. Power Syst., vol. 39, no. 2, pp. 2528–2542, Mar. 2024.

[33] “Market PJM data,” Accessed: Jun. 10, 2023. [Online]. Available: https: //www.pjm.com/

[34] H. Ebrahimian, S. Barmayoon, M. Mohammadi, and N. Ghadimi, “The price prediction for the energy market based on a new method,” Econ. Res.-Ekonomska istraživanja, vol. 31, no. 1, pp. 313–337, 2018.

[35] U. Schwiegeishohn and R. Yahyapour, “Improving first-come-first-serve job scheduling by gang scheduling,” in Proc. Workshop Job Scheduling Strategies Parallel Process., Springer, 1998, pp. 180–198.

[36] S. Vallender, “Calculation of the Wasserstein distance between probability distributions on the line,” Theory Probability Appl., vol. 18, no. 4, pp. 784–786, 1974.

[37] F. Cao, Y. Wang, F. Zhu, Y. Cao, and Z. Ding, “Ups node-based workload management for data centers considering flexible service requirements,” IEEE Trans. Ind. Appl., vol. 55, no. 6, pp. 5533–5542, 2019.

![](images/0be851e3028fd5a6997ee0472c2d19d40303d2ac0a56d825a43484a43bc8fb8e.jpg)

Shijie Chen (Student Member, IEEE) received the B.S. degree in the electrical engineering and automation in 2023 from North China Electric Power University, Beijing, China, where she is currently working toward the M.S. degree. Her research interests include data center job scheduling and participation in the market.

![](images/9bf7dc25b74eced9c6bd2676d8fff183079debca352109a7e2cc927eabe83285.jpg)

Yimeng Sun (Graduate Student Member, IEEE) received the B.S. degree in the electrical engineering and automation in 2021 from North China Electric Power University, China, where she is currently working toward the Ph.D. degree in electrical engineering with School of Electrical and Electronic Engineering. Her research interests include power system optimization and demand side management.

![](images/7c973f8d04e58fcb9c4b8f25f82386bfef5061bf144633390698e018770d9b94.jpg)

Kun Shi received the M.S. degree in electronics and communications engineering from North China Electric Power University, Beijing, China. He is currently with China Electric Power Research Institute Company, Ltd. His research interests include intelligent electricity consumption and supply-demand interaction technology.

![](images/dc67a9a364ff7b8829088631a541480d95708b71c5f54fd9f8a2f4ea923148bf.jpg)

Jiaying Wang received the M.S. degree in electrical engineering from Zhejiang University, Hangzhou, China. She is currently with State Grid Zhejiang Marketing Service Center. Her research interests include demand side management.

![](images/49dc2e07e9db22aef2e7d7471f5eb61a518ed0266155bdf5cd087fdad04ea6fb.jpg)  
Zhaohao Ding (Senior Member, IEEE) received the B.S. degree in electrical engineering and the B.A. degree in finance from Shandong University, Jinan, China, in 2010, and the Ph.D. degree in electrical engineering from the University of Texas at Arlington, Arlington, TX, USA, in 2015. He is currently a Professor with North China Electric Power University, Beijing, China. His research interests include power system planning and operation, power market, distributed resource management, and electric transportation system.

![](images/54fcb1dfe5853b9bfd3b57b9f227c61b0f91acae29b47b62b8fa36a21b54cada.jpg)

Songsong Chen (Senior Member, IEEE) received the M.S. degree in communication and information systems from North China Electric Power University, Beijing, China. He is currently with China Electric Power Research Institute Company, Ltd. His research interests include intelligent electricity interaction, customer-side energy efficiency optimization, and power analysis and prediction.

![](images/a1a8b7cc3f7f23df6c4d691f3a52401e3de2c77eceea773b2158f67287e1d201.jpg)

![](images/2c57920f6fd096132c7f2817236076faba7c85cee993c8909770ac81ee209d26.jpg)

Tao Xiao received the B.S. degree in electrical engineering and automation from Xi’an Jiaotong University, Xi’an, China. His research focuses on electricity marketing.

Yehan Wang (Student Member, IEEE) received the B.S. degree in the electrical engineering and automation in 2023 from North China Electric Power University, China, where she is currently working toward the M.S. degree. Her research interests include demand side management and load forecasting.

![](images/77de39e3b4cdf0bec6b97267c2a3d50fdbf1f2d41c7ff7bf317f778ff8e383d6.jpg)

Xuan Wei (Member, IEEE) received the B.S. and M.S. degrees in electrical engineering from North China Electric Power University, Beijing, China, in 2017 and 2020, respectively, and the Ph.D. degree in electrical engineering from Tsinghua University, Beijing, China, in 2024. She is currently a Lecturer with North China ElectricPower University, Beijing, China. Her research interests include distributed optimization and operation of virtual power plants and the integration of electric vehicles.