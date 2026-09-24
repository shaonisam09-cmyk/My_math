# The Online Pause and Resume Problem: Optimal Algorithms and An Application to Carbon-Aware Load Shifting

ADAM LECHOWICZ, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup>

NICOLAS CHRISTIANSON, <sup>California</sup> <sup>Institute</sup> <sup>of</sup> <sup>Technology,</sup> <sup>USA</sup>

JINHANG ZUO, <sup>California</sup> <sup>Institute</sup> <sup>of</sup> <sup>Technology</sup> <sup>&</sup> <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup>

NOMAN BASHIR, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup>

MOHAMMAD HAJIESMAILI, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup>

ADAM WIERMAN, <sup>California</sup> <sup>Institute</sup> <sup>of</sup> <sup>Technology,</sup> <sup>USA</sup>

PRASHANT SHENOY, <sup>University</sup> <sup>of</sup> <sup>Massachusetts</sup> <sup>Amherst,</sup> <sup>USA</sup>

We introduce and study the online pause and resume problem. In this problem, a player attempts to find the ?? lowest (alternatively, highest) prices in a sequence of fixed length ?? , which is revealed sequentially. At each time step, the player is presented with a price and decides whether to accept or reject it. The player incurs a <sub>switching</sub> <sub>cost</sub> whenever their decision changes in consecutive time steps, i.e., whenever they pause or resume purchasing. This online problem is motivated by the goal of carbon-aware load shifting, where a workload may be paused during periods of high carbon intensity and resumed during periods of low carbon intensity and incurs a cost when saving or restoring its state. It has strong connections to existing problems studied in the literature on online optimization, though it introduces unique technical challenges that prevent the direct application of existing algorithms. Extending prior work on threshold-based algorithms, we introduce <sub>double-</sub> <sub>threshold</sub> algorithms for both the minimization and maximization variants of this problem. We further show that the competitive ratios achieved by these algorithms are the best achievable by any deterministic online algorithm. Finally, we empirically validate our proposed algorithm through case studies on the application of carbon-aware load shifting using real carbon trace data and existing baseline algorithms.

<sup>CCS</sup> <sup>Concepts:</sup> <sup>•</sup> Theory of computation → Online algorithms<sup>;</sup> Theory and algorithms for application domains<sup>.</sup>

Additional Key Words and Phrases: online pause and resume, carbon-aware load shifting, online algorithms, switching costs, ??-search

## ACM Reference Format:

Adam Lechowicz, Nicolas Christianson, Jinhang Zuo, Noman Bashir, Mohammad Hajiesmaili, Adam Wierman, and Prashant Shenoy. 2023. The Online Pause and Resume Problem: <sub>Optimal</sub> <sub>Algorithms</sub> <sub>and</sub> <sub>An</sub> <sub>Application</sub> <sub>to</sub> Carbon-Aware Load Shifting<sup>.</sup> Proc. ACM Meas. Anal. Comput. Syst. <sup>7,</sup> <sup>3,</sup> <sup>Article</sup> <sup>45</sup> <sup>(December</sup> <sup>2023), 32 pages.</sup> https://doi.org/10.1145/3626776

Authors’ addresses: Adam Lechowicz, University of Massachusetts Amherst, USA, alechowicz@cs.umass.edu; Nicolas Christianson, California Institute of Technology, USA, nchristianson@caltech.edu; Jinhang Zuo, California Institute of Technology & University of Massachusetts Amherst, USA, jhzuo@cs.umass.edu; Noman Bashir, University of Massachusetts Amherst, USA, nbashir@cs.umass.edu; Mohammad Hajiesmaili, University of Massachusetts Amherst, USA, hajiesmaili@cs. umass.edu; Adam Wierman, California Institute of Technology, USA, adamw@caltech.edu; Prashant Shenoy, University of Massachusetts Amherst, USA, shenoy@cs.umass.edu.

Permission to make digital or hard copies of all or part of this work for personal or classroom use is granted without fee provided that copies are not made or distributed for profit or commercial advantage and that copies bear this notice and the full citation on the first page. Copyrights for components of this work owned by others than the author(s) must be honored. Abstracting with credit is permitted. To copy otherwise, or republish, to post on servers or to redistribute to lists, requires prior specific permission and/or a fee. Request permissions from permissions@acm.org. © 2023 Copyright held by the owner/author(s). Publication rights licensed to ACM. 2476-1249/2023/12-ART45 \$15.00 https://doi.org/10.1145/3626776

## 1 INTRODUCTION

This paper introduces and studies the <sub>online</sub> <sub>pause</sub> <sub>and</sub> <sub>resume</sub> <sub>problem</sub> (<sub>OPR</sub>), considering both minimization (<sub>OPR-min</sub>) and maximization (<sub>OPR-max</sub>) variants. In <sub>OPR-min</sub>, a player is presented with time-varying prices in a sequential manner and decides whether or not to purchase one unit of an item at the current price. The player must purchase ?? units of the item over a time horizon of ?? and they incur a <sub>switching</sub> <sub>cost</sub> whenever their decision changes in consecutive time steps, i.e., whenever they pause or resume purchasing. The goal of the player is to minimize their total cost, which consists of the aggregate price of purchasing ?? units and the aggregate switching cost incurred over ?? slots. In <sub>OPR-max</sub>, the setting is exactly the same, but the goal of the player is to maximize their total profit, and any switching cost that they incur is subtracted. In both cases, the price values are revealed to the player one by one in an online manner, and the player has to make a decision without knowing the future values.

Our primary motivation for introducing <sub>OPR</sub> is the emerging importance of carbon-aware computing and, more specifically, carbon-aware temporal workload shifting, which has seen significant attention in recent years [1, 6, 36, 47]. In carbon-aware temporal workload shifting, an interruptible and deferrable workload may be paused during periods of high carbon intensity and resumed during periods of low carbon intensity. The workload must be running for ?? units of time to complete and must be completed before its deadline ?? . However, pausing and resuming the workload typically comes with overheads such as storing the state in memory and checkpointing. For example, an empirical study [20] shows that this overhead can nullify any savings in carbon emissions from temporal shifting if the job is interrupted frequently. Moreover, with the rise of big ML training workloads, such as the training, fine-tuning, and inference of large language models (LLM), data center workloads’ memory footprints are frequently in the hundreds of GBs [37, 41, 48]. These emerging workloads will result in high checkpoint-and-restore overheads, which must be con sidered in carbon-aware scheduling. This motivates adding a <sub>switching</sub> <sub>cost</sub> in <sub>OPR</sub>, since a naïve algorithm that does not account for the interruptions’ overhead may frequently checkpoint and, in some cases, increase carbon emissions beyond a carbon-agnostic execution.

The objective of temporal workload shifting is to minimize the total carbon footprint of running the workload, which includes both the original compute demand and the overhead due to pausing and resuming (a.k.a., the switching cost). We consider a worst-case performance objective based on competitive analysis, defined explicitly in Section 2, wherein we seek to find an efective algorithm that is robust to uncertain and nonstochastic fluctuations in price (or carbon intensity in the context of carbon-aware load shifting). We note that even though statistical modeling of grid carbon intensity has been explored [31], we focus on developing worst-case optimized algorithms as the intended application has nonlinearity and nonstationarity, which complicate the task of designing a single probabilistic model to solve this problem.

<sub>OPR</sub> also captures other interesting applications with highly variable time-varying costs where switching frequently is undesirable. A related example is the carbon-aware electric vehicle (EV) charging problem, which considers when to charge an EV with respect to the time-varying avail ability of carbon-free electricity, a charging deadline (e.g., set by the EV owner), and battery health design goals (i.e., a constant charging rate is better for battery longevity) [52]. When the charger is <sub>non-adaptive</sub> (i.e., the charging rate is either 0 or the maximum rate), the problem reduces exactly to <sub>OPR</sub>. Beyond these “carbon-aware” applications, there are additional examples that deal with pricing, such as managing grid-scale energy storage with respect to real-time prices in the wholesale electricity market, where a “smooth” charge or discharge rate is desired [49]. Another example is renting spot virtual machines from a cloud service provider in the setting where pricing is set according to supply-demand dynamics [2, 40, 53].

On the theory front, the <sub>OPR</sub> problem has strong connections to various existing problems in the literature on online optimization. We extensively review the prior literature in Section 7 and focus on the most relevant theoretical problems below. The <sub>OPR</sub> problem is a generalization of the ??-search problem [23, 29], which belongs to the broader class of online conversion problems [44], a.k.a., time series search and one-way trading [15]. In the minimization variant of the ??-search problem, an online decision-maker aims to buy ?? units of an item for the least cost over a sequence of time-varying cost values. At each step, a cost value is observed, and the decision is whether or not to buy one unit at the current observed cost without knowing the future values (see Section 2.2 for a deeper discusssion of ??-search). In contrast to ??-search, the <sub>OPR</sub> problem introduces the additional component of managing the switching cost, which poses a significant additional challenge in algorithm design.

The existence of the switching cost in <sub>OPR</sub> connects it to the well-studied problem of smoothed online convex optimization (<sub>SOCO</sub>) [25], also known as convex function chasing (<sub>CFC</sub>) [17], and its generalizations including metrical task systems (<sub>MTS</sub>) [7]. In <sub>SOCO</sub>, a learner is faced with a sequence of cost functions $f _ { t }$ that are revealed online, and must choose an action $x _ { t }$ after observing $f _ { t } .$ . Based on that decision, the learner incurs a hitting cost, $f _ { t } ( x _ { t } )$ as well as a switching cost, $\| x _ { t } - x _ { t - 1 } \|$ which captures the cost associated with changing the decision between rounds. In contrast to <sub>SOCO</sub>, <sub>OPR</sub> includes the long-term constraint of satisfying the demand of ?? units over the horizon ?? , which poses a significant challenge not present in <sub>SOCO</sub>-like problems.

The coexistence of these diferentiating factors, namely the <sub>switching</sub> <sub>cost</sub> and the <sub>long-term</sub> <sub>deadline</sub> <sub>constraint</sub>, make <sub>OPR</sub> uniquely challenging, and means that prior algorithms and analyses for related problems such as ??-search and <sub>SOCO</sub> cannot be directly adapted.

<sub>Contributions.</sub> We introduce online algorithms for the minimization and maximization variants of <sub>OPR</sub> and show that our algorithms achieve the best possible competitive ratios. We also evaluate the empirical performance of the proposed algorithms on a case study of carbon-aware load shifting. The details of our contributions are outlined below.

<sub>Algorithmic</sub> <sub>idea:</sub> <sub>Double-threshold.</sub> To tackle <sub>OPR</sub>, we focus our eforts on online threshold-based algorithms (<sub>OTA</sub>), the prominent design paradigm for classic problems such as ??-search [23, 29], one-way trading [15, 44], and online knapsack problems [45, 50, 54]. In the ??-min search problem, for example, a threshold-based algorithm specifies ?? threshold values and chooses to trade the ??-th item only if the current price is less than or equal to the value suggested by the ??-th threshold value.

Direct application of prior <sub>OTA</sub> algorithms to <sub>OPR</sub> results in undesirable behavior (such as frequently changing decisions) since their threshold function design is oblivious to the switching cost present in <sub>OPR</sub>. To address this challenge, we seek an algorithm that can simultaneously achieve the following behaviors: (1) when the player is in “trading mode,” they should not impulsively switch away from trading in response to a price that is only slightly worse, since this will result in a switching penalty; and (2) the player should not switch to “trading mode” unless prices are suficiently good to warrant the switching cost. These two ideas motivate an algorithm design that uses two distinct threshold functions, each of which captures one of the above two cases. We present our algorithms <sub>DTPR-min</sub> and <sub>DTPR-max</sub> for <sub>OPR-min</sub> and <sub>OPR-max</sub>, respectively, in Section 3, which build upon this high-level idea of a double-threshold.

<sub>Main</sub> <sub>results.</sub> While <sub>OTA</sub> algorithms are intuitive and simple to describe, it is highly challenging to design threshold functions that lead the corresponding algorithms to be competitive against the ofline optimum. The addition of switching cost in <sub>OPR</sub> further exacerbates the technical challenge of designing optimal threshold functions. The key result which enables our double-threshold approach is a technical observation (see Observation 3), which shows that the diference between the functions guiding the algorithm’s decisions should be exactly $^ { 2 \beta , }$ where $\beta$ represents the fixed switching cost incurred by changing the decision in <sub>OPR</sub>.

Identifying this relationship between the two threshold functions significantly facilitates the competitive analysis of both <sub>DTPR-min</sub> and <sub>DTPR-max</sub>, enabling our derivation of a closed form of each threshold. Using this idea, we characterize the competitive ratios of <sub>DTPR-min</sub> and <sub>DTPR-max</sub> as a function of problem parameters, including an explicit dependence on the magnitude of the switching cost $\beta$ (see Theorems 4 and 5). Furthermore, we derive lower bounds for the competitive ratio of any deterministic online algorithm, showing that our proposed algorithms are optimal for this problem (formal statements in Theorems 8 and 9). The competitive ratios we derive for both <sub>DTPR-min</sub> and <sub>DTPR-max</sub> exactly recover the best prior competitive results for the ??-search problem [29], which corresponds to the case of $\beta = 0$ in <sub>OPR</sub>, i.e., no switching cost. Formal statements and a more detailed discussion of our main results are presented in Section 4.

<sub>Case</sub> <sub>study.</sub> Finally, in Section 6, we illustrate the performance of our proposed algorithm by conducting an experimental case study simulating the carbon-aware load shifting problem. We utilize real-world <sub>carbon</sub> <sub>traces</sub> from Electricity Maps [32], which contain carbon intensity values for grid-sourced electricity across the world. Our experiments simulate diferent strategies for scheduling a deferrable and interruptible workload in the face of uncertain future carbon intensity values. We show that our algorithm’s performance significantly improves upon existing baseline methods and adapted forms of algorithms for related problems such as ??-min search.

## 2 PROBLEM FORMULATION AND PRELIMINARIES

We begin by formally introducing the <sub>OPR</sub> problem and providing background on the online threshold-based algorithm design paradigm, which is used in the design of our proposed algorithms. Table 1 summarizes the core notations for <sub>OPR</sub>. Recall that this formulation is motivated by the setting of carbon-aware temporal workload shifting, as described in the introduction.

## 2.1 Problem Formulation

We present two variants of the online pause and resume problem (<sub>OPR</sub>). In <sub>OPR-min</sub> (<sub>OPR-max</sub>) a player must buy (sell) $k \geq 1$ units of some asset (one unit at each time step) with the goal of minimizing (maximizing) their total cost (profit) within a time horizon of length ?? . At each time step $1 \leq t \leq T$ , the player is presented with a price $c _ { t } ,$ and must immediately decide whether to accept this price $( x _ { t } = 1 )$ or reject it $( x _ { t } = 0 )$ . The player is required to complete this transaction for all ?? units by some point in time ?? . Both ?? and ?? are known in advance. Thus, the requirement of ?? transactions is a hard constraint, i.e., $\textstyle \sum _ { t = 1 } ^ { T } x _ { t } = k$ , and if at time $T - i$ the player still has ?? units remaining to buy/sell, they must accept the prices in the subsequent ?? slots to accomplish ?? transactions.

Additionally, in both variants of <sub>OPR</sub>, the player incurs a <sub>fixed</sub> <sub>switching</sub> <sub>cost</sub> $\beta > 0$ whenever they decide to change decisions between two adjacent time steps (i.e., when $\| x _ { t - 1 } - x _ { t } \| = 1 )$ . We assume that $x _ { 0 } = 0$ and $x _ { T + 1 } = 0$ , implying that any player must incur a minimum switching cost of $2 \beta ,$ once for switching “on” and once for switching “of”. While the player incurs at least a switching cost of $^ { 2 \beta , }$ note that the total switching cost incurred by the player is bounded by the size of the asset ?? since the switching cost cannot be larger than $k 2 \beta .$

Table 1. A summary of key notations

<table><tr><td>Notation</td><td>Description</td></tr><tr><td> $k \in \mathbb{N}$ </td><td>Number of units which must be bought (or sold)</td></tr><tr><td> $T$ </td><td>Deadline constraint; the player must buy (or sell)  $k$  units before time  $T$ </td></tr><tr><td> $t \in [1,T]$ </td><td>Current time step</td></tr><tr><td> $x_{t} \in \{0,1\}$ </td><td>Decision at time  $t. x_{t} = 1$  if price  $c_{t}$  is accepted,  $x_{t} = 0$  if  $c_{t}$  is not accepted</td></tr><tr><td> $\beta$ </td><td>Switching cost incurred when algorithm’s decision  $x_{t} \neq x_{t-1}$ </td></tr><tr><td> $U$ </td><td>Upper bound on any price that will be encountered</td></tr><tr><td> $L$ </td><td>Lower bound on any price that will be encountered</td></tr><tr><td> $\theta = U/L$ </td><td>Price fluctuation ratio</td></tr><tr><td> $c_{t}$ </td><td>(Online input) Price revealed to the player at time  $t$ </td></tr><tr><td> $c_{\min} \& c_{\max}$ </td><td>(Online input) The actual minimum and maximum prices in a sequence</td></tr></table>

In summary, the ofline version of <sub>OPR-min</sub> can be summarized as follows:

$$
\min \underbrace {\left(\sum_ {t = 1} ^ {T} c _ {t} x _ {t}\right)} _ {\text { Accepted   prices }} + \underbrace {\left(\sum_ {t = 1} ^ {T + 1} \beta | | x _ {t} - x _ {t - 1} | |\right)} _ {\text { Switching   cost }}, \quad \text { s.t., } \underbrace {\sum_ {t = 1} ^ {T} x _ {t} = k ,} _ {\text { Deadline   constraint }} \quad x _ {t} \in \{0, 1 \}, \forall t \in [ 1, T ],\tag{1}
$$

while the ofline version of <sub>OPR-max</sub> is

$$
\max \left(\sum_ {t = 1} ^ {T} c _ {t} x _ {t}\right) - \left(\sum_ {t = 1} ^ {T + 1} \beta | | x _ {t} - x _ {t - 1} | |\right), \quad \text { s.t., } \quad \sum_ {t = 1} ^ {T} x _ {t} = k, \quad x _ {t} \in \{0, 1 \}, \forall t \in [ 1, T ].\tag{2}
$$

Of course, our focus is the online version of <sub>OPR</sub>, where the player must make irrevocable decisions at each time step without the knowledge of future inputs. More specifically, in both variants of <sub>OPR</sub> the sequence of prices $\{ c _ { t } \} _ { t \in [ 1 , T ] }$ is revealed sequentially – future prices are <sub>unknown</sub> to an online algorithm, and each decision $x _ { t }$ is irrevocable.

<sub>Competitive</sub> <sub>analysis.</sub> Our goal is to design an online algorithm that maintains a small <sub>competitive</sub> <sub>ratio</sub> [7], i.e., performs nearly as well as the ofline optimal solution. For an online algorithm <sub>ALG</sub> and an ofline optimal solution <sub>OPT</sub>, the competitive ratio for a minimization problem is defined as: $\begin{array} { r } { \mathrm { C R } ( \mathsf { A L G } ) = \operatorname* { m a x } _ { \sigma \in \Omega } \mathsf { A L G } ( \sigma ) / 0 \mathsf { P T } ( \sigma ) } \end{array}$ , where ?? denotes a valid input sequence for the problem and <sub>Ω</sub> is the set of all feasible input instances. Further, ${ \mathsf { O P T } } ( \sigma )$ is the optimal cost given this input, and <sub>ALG(</sub>??<sub>)</sub> is the cost of the solution obtained by running the online algorithm over this input. Conversely, for a problem with a maximization objective, the competitive ratio is defined as $\mathrm { m a x } _ { \sigma \in \Omega } { 0 } \mathsf { P T } ( \sigma ) / \mathsf { A L G } ( \sigma )$ . With these definitions, the competitive ratio for both minimization and maximization problems is always greater than or equal to one, and the lower the better.

Note that competitive algorithm development, in its classic worst-case optimized design, cannot capture data-driven adaptation and stochasticity of data in decision-making. However, beyond the significance of the theoretical analysis in this framework, competitive algorithms could be of interest to practitioners since they are robust against adversarial or non-stationary behavior in the underlying environment. For example, in the context of carbon-aware load shifting, the carbon intensity values significantly change across the temporal and spatial domains following the makeup and behavior of an electric grid (e.g., diferent ISOs and generation mixes; see Figure 9 in the appendix); and online algorithms are robust to those drastic temporal and spatial variations. Competitive algorithms are extremely simple to implement, e.g., in <sub>OPR</sub>, all we need are two threshold functions to decide the pause and resume decisions. Furthermore, worst-case optimized algorithms can potentially be augmented with machine-learned predictions, as explored in e.g. [3, 4, 12, 30, 35, 44], to achieve the best of both worlds of worst-case and average-case performance.

<sub>Assumptions</sub> <sub>and</sub> <sub>additional</sub> <sub>notations.</sub> We make no assumptions on the underlying distribution of the prices other than the assumption that the set of prices arriving online $\{ c _ { t } \} _ { t \in [ 1 , T ] }$ has bounded support, i.e., $c _ { t } \ \in \ [ L , U ] \ \forall t \ \in \ [ 1$ , ?? <sub>]</sub>, where ?? and ?? are known to the player. We also define $\theta = U / L$ as the <sub>price</sub> <sub>fluctuation</sub>. These are standard assumptions in the literature for many online problems, including one-way trading, online search, and online knapsack; and without them the competitive ratio of any algorithm is unbounded. Most papers in this literature additionally assume that $U , L > 0$ (i.e. the lowest price is still positive), but our design can handle the special case where $L = 0 ,$ , and therefore do not adopt this assumption. We use $c _ { \mathrm { m i n } } ( \sigma ) = \mathrm { m i n } _ { t \in [ 1 , T ] } c _ { t }$ and $c _ { \operatorname* { m a x } } ( \sigma ) = \operatorname* { m a x } _ { t \in [ 1 , T ] } c _ { t }$ to denote the minimum and maximum encountered prices for any valid <sub>OPR</sub> sequence ??.

## 2.2 Background: Online Threshold-Based Algorithms (OTA)

Online threshold-based algorithms (<sub>OTA</sub>) are a family of algorithms for online optimization in which a carefully designed <sub>threshold</sub> <sub>function</sub> is used to specify the decisions made at each time step. At a high level, the threshold function defines the “minimum acceptable quality” that an arriving input/price must satisfy in order to be accepted by the algorithm. The threshold is chosen specifically so that an agent greedily accepting prices meeting the threshold at each step will be ensured a competitive guarantee. This algorithmic framework has seen success in the online search and one-way trading problems [15, 23, 29, 44] as well as the related online knapsack problem [45, 50, 54]. In these works, the derived threshold functions are optimal in the sense that the competitive ratios of the resulting threshold-based algorithms match information-theoretic lower bounds of the corresponding online problems. As discussed in the introduction, the framework does not apply directly to the <sub>OPR</sub> setting, but we make use of ideas and techniques from this literature. We briefly detail the most relevant highlights from the prior results before discussing how these related problems generalize to <sub>OPR</sub> in the next section.

<sub>1-min/1-max</sub> <sub>search.</sub> In the online 1-min/1-max search problem, a player attempts to find the single lowest (respectively, highest) price in a sequence, which is revealed sequentially. The player’s objective is to either minimize their cost or maximize their profit. When each price arrives, the player must decide immediately whether to accept the price, and the player is forced to accept exactly one price before the end of the sequence. For this problem, El-Yaniv et al. [15] presents a deterministic threshold-based algorithm. The algorithm assumes a finite price interval, i.e., the price is bounded by the interval <sub>[</sub>??, ?? <sub>]</sub>, where ?? and ?? are known. Then, it sets a constant threshold $\Phi = { \sqrt { L U } }$ , and the algorithm simply selects the first price that is less than or equal to <sub>Φ</sub> (for the maximization version, it accepts the first price greater than or equal to <sub>Φ</sub>). This algorithm achieves a competitive ratio of ${ \sqrt { U / L } } = { \sqrt { \theta } }$ , which matches the lower bound; hence, it is optimal [15].

??<sub>-min/</sub>??<sub>-max</sub> <sub>search.</sub> The online ??-min/??-max search problem extends the 1-min/1-max search problem – a player attempts to find the ?? lowest (conversely, highest) prices in a sequence of prices revealed sequentially. The player’s objective is identical to the 1-min/1-max problem, and the player must accept at least ?? prices by the end of the sequence. Several works have developed a known optimal deterministic threshold-based algorithm for this problem, including [15, 29]. Leveraging the same assumption of a finite price interval <sub>[</sub>??, ?? <sub>]</sub>, the threshold function is a sequence of ?? thresholds $\{ \Phi _ { i } \} _ { i \in [ 1 , k ] }$ , which is also called the <sub>reservation</sub> <sub>price</sub> <sub>policy</sub>. At each step, the algorithm accepts the first price, which is less than or equal to $\Phi _ { i }$ , where ?? <sub>−</sub> 1 is the number of prices that have been accepted thus far (for the maximization version, it accepts the first price which is $\geq \Phi _ { i } )$ \_ In the ??-min setting, this algorithm is ??-competitive, where ?? is the unique solution of

$$
\frac {1 - 1 / \theta}{1 - 1 / \alpha} = \left(1 + \frac {1}{\alpha k}\right) ^ {k}.\tag{3}
$$

For the ??-max variant, this algorithm is ??-competitive, where ?? is the unique solution of

$$
{\frac {\theta - 1}{\omega - 1}} = \left(1 + {\frac {\omega}{k}}\right) ^ {k}.\tag{4}
$$

The sequence of thresholds $\{ \Phi _ { i } \} _ { i \in [ 1 , k ] }$ for both variants of the problem are constructed by analyzing possible input cases, “hedging” against the risk that future (unknown) prices will jump to the worst possible value, i.e., ?? for ??-min search, ?? for ??-max search. These potential cases can be enumerated for diferent values of ??, where $0 \leq i \leq k$ denotes the number of prices accepted so far. By simultaneously <sub>balancing</sub> the competitive ratios for each of these cases (setting each ratio equal to the others), the optimal threshold values and the optimal competitive ratios are derived. We refer to this technique as the <sub>balancing</sub> <sub>rule</sub> and a rigorous proof of this approach, with corresponding lower bounds, can be found in [29]. The lower bounds highlight that the ?? and $\omega$ which solve the expressions for the competitive ratios above are optimal for any deterministic ??-min and ??-max search algorithms, respectively. Further, ?? and ?? provide insight into a fundamental diference between the minimization and maximization settings of ??-search. As discussed in [29], for large ??, the best algorithm for ??-max search is roughly $O ( k { \sqrt [ { k } ] { \theta } } )$ -competitive, while the best algorithm for ??-min search is at best $O ( { \sqrt { \theta } } )$ -competitive. Similarly, for fixed ?? and large ??, the optimal competitive ratio for ??-max search is roughly ?? <sub>(</sub>ln ??<sub>)</sub>, while the optimal competitive ratio for ??-min search converges to $O ( { \sqrt { \theta } } )$ .

## 3 DOUBLE THRESHOLD PAUSE AND RESUME (DTPR) ALGORITHM

A fundamental challenge in algorithm design for <sub>OPR</sub> is how to characterize threshold functions that incorporate the presence of switching costs in their design. Our key algorithmic insight is to incorporate the switching cost into the threshold function by defining <sub>two</sub> <sub>distinct</sub> <sub>threshold</sub> <sub>functions</sub>, where the function to be used for price admittance changes based on the current state (i.e., whether or not the previous price was accepted by the algorithm).

To provide intuition for the state-dependence of the threshold function, consider the setting of <sub>OPR-min</sub>. At a high level, if the player has not accepted the previous price, they should wait to accept anything until prices are suficiently low to justify incurring a cost to switch decisions. On the other hand, if the player has accepted the previous price, they might be willing to accept a slightly higher price – if they do not accept this price, they will incur a cost to switch decisions. While this high-level idea is intuitive, characterizing the form of threshold functions such that the resulting algorithms are competitive is challenging.

<sub>The</sub> <sub>DTPR-min</sub> <sub>algorithm.</sub> Our proposed algorithm, Double Threshold Pause and Resume (<sub>DTPR</sub>) for <sub>OPR-min</sub> is summarized in Algorithm 1. Prior to any prices arriving online, <sub>DTPR-min</sub> computes two families of threshold values, $\{ \ell _ { i } \} _ { i \in [ 1 , k ] }$ and $\{ u _ { i } \} _ { i \in [ 1 , k ] }$ , where $\ell _ { i } \leq u _ { i } \forall i \in [ 1 , k ]$ , whose values are defined as follows.

<sup>Definition</sup> <sup>1</sup> <sup>(</sup>DTPR-min <sup>Threshold</sup> <sup>Values).</sup> For each integer <sup>??</sup> on the interval [<sup>1,</sup> <sup>??</sup>], the following expressions give the corresponding threshold values $o f u _ { i }$ and $\ell _ { i }$ for DTPR-min.

$$
u _ {i} = U \left[ 1 - \left(1 - \frac {1}{\alpha}\right) \left(1 + \frac {1}{k \alpha}\right) ^ {i - 1} \right] + 2 \beta \left[ \left(\frac {1}{k \alpha} - \frac {1}{k} + 1\right) \left(1 + \frac {1}{k \alpha}\right) ^ {i - 1} \right], \quad \ell_ {i} = u _ {i} - 2 \beta ,\tag{5}
$$

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 1 Double Threshold Pause and Resume for OPR-min (DTPR-min)

Input: threshold values  $\{\ell_{i}\}_{i\in[1,k]}$  and  $\{u_{i}\}_{i\in[1,k]}$  defined in Eq. (5), deadline T

Output: online decisions  $\{x_{t}\}_{t\in[1,T]}$ 

1: initialize: i = 1

2: while price  $c_{t}$  arrives and  $i \leq k$  do

3: if  $(k - i) \geq (T - t)$  then ▷ close to the deadline T, we must accept remaining prices

4: price  $c_{t}$  is accepted, set  $x_{t} = 1$ 

5: else if  $x_{t-1} = 0$  then ▷ If previous price was not accepted

6: if  $c_{t} \leq \ell_{i}$  then price  $c_{t}$  is accepted, set  $x_{t} = 1$ 

7: else price  $c_{t}$  is rejected, set  $x_{t} = 0$ 

8: else if  $x_{t-1} = 1$  then ▷ If previous price was accepted

9: if  $c_{t} \leq u_{i}$  then price  $c_{t}$  is accepted, set  $x_{t} = 1$ 

10: else price  $c_{t}$  is rejected, set  $x_{t} = 0$ 

11: update  $i = i + x_{t}$
</div>

where <sup>??</sup> is the competitive ratio of DTPR-min defined in Equation <sup>(9)</sup>.

The role of these thresholds is to incorporate the switching cost into the algorithm’s decisions, and to alter the acceptance criteria of <sub>DTPR-min</sub> based on the current state. For <sub>OPR-min</sub>, the current <sup>state</sup> <sup>is</sup> whether the previous item was accepted<sup>,</sup> <sup>i.e.,</sup> <sup>whether</sup> $x _ { t - 1 }$ is 0 or 1. As prices are sequentially revealed to the algorithm at each time ??, the ??th price accepted by <sub>DTPR-min</sub> will be the first price which is at most $\ell _ { i }$ if $x _ { t - 1 } = 0$ , or at most $u _ { i }$ if $x _ { t - 1 } = 1$ . We note that ?? does not explicitly appear in this definition. As ?? approaches $k ,$ the values of these thresholds decrease, getting closer to ?? (See Figure 1). Note that, as indicated in Line 4, <sub>DTPR-min</sub> may be forced to accept the last prices of the sequence, which can be “worse” than the current threshold values, to satisfy the deadline constraint of <sub>OPR</sub>. Since $T$ (the deadline) does not appear explicitly in the threshold definition, our analysis can handle the case where ?? is not known to the online player, and the forced acceptance is triggered by some external signal.

<sub>The DTPR-max algorithm.</sub> Pseudocode is summarized in the appendix, in Algorithm 2. The logical flow of <sub>DTPR-max</sub> shares a similar structure to that of <sub>DTPR-min</sub>, with a few important diferences highlighted here. For <sub>OPR-max</sub>, the ??th price accepted by <sub>DTPR-max</sub> will be the first price which is at least $u _ { i }$ if $x _ { t - 1 } = 0$ , or at least $\ell _ { i }$ if $x _ { t - 1 } = 1$ . Further, the threshold functions are defined as follows.

<sup>Definition</sup> <sup>2</sup> <sup>(</sup>DTPR-max <sup>Threshold</sup> <sup>Values).</sup> For each integer <sup>??</sup> on the interval [<sup>1,</sup> <sup>??</sup>], the following expressions give the corresponding threshold values of $\mathbf { \dot { \ell } } _ { i }$ and $u _ { i } f o r$ DTPR-max.

$$
\ell_ {i} = L \left[ 1 + (\omega - 1) \left(1 + \frac {\omega}{k}\right) ^ {i - 1} \right] - 2 \beta \left[ \left(\frac {\omega}{k} - \frac {1}{k} + 1\right) \left(1 + \frac {\omega}{k}\right) ^ {i - 1} \right], u _ {i} = \ell_ {i} + 2 \beta ,\tag{6}
$$

where <sup>??</sup> is the competitive ratio of DTPR-max defined in Equation <sup>(10)</sup>.

In Figures 1 and 2, we plot threshold values for <sub>DTPR-min</sub> and <sub>DTPR-max</sub>, respectively, using example parameters of $U = 3 0 , L = 5 , k = 1 0 \mathrm { , }$ and $\beta = 3$ . We annotate the diference of $2 \beta$ between $\ell _ { i }$ and $u _ { i } ;$ recall that each of these thresholds corresponds to a <sub>current</sub> <sub>state</sub> for <sub>DTPR</sub>, i.e. whether the previous item was accepted. Note that the <sub>DTPR-min</sub> threshold values <sub>decrease</sub> as ?? gets larger, while the <sub>DTPR-max</sub> threshold values <sub>increase</sub> as ?? gets larger. At a high-level, each ??th threshold “hedges” against a scenario where none of the future prices meet the current threshold. In this case, even if the algorithm is forced to accept the <sub>worst</sub> <sub>possible</sub> <sub>prices</sub> at the end of the sequence, we want competitive guarantees against an ofline <sub>OPT</sub>. Such guarantees rely on the fact that in the worst-case, $\mathtt { O P T }$ cannot accept prices that are all significantly better than <sub>DTPR</sub>’s ??th “unseen” threshold value because such prices did not exist in the sequence.

![](lechowicz2023_online_pause_resume_assets/images/b87ae66e4b9ee097b56629ef8c158a7cfcceaa7fb3081544ee0184529ff28aa2.jpg)  
Fig. 1. DTPR-min thresholds $\ell _ { i }$ and $u _ { i }$ for $i \in$ <sub>[</sub>1, ??<sub>]</sub> ploted using example parameters (<sup>??</sup> = <sup>10</sup>).

![](lechowicz2023_online_pause_resume_assets/images/4fb8f348a30c04255c5f4ba379b34bc5eb0950183df82d63204226bf9147a852.jpg)  
Fig. 2. DTPR-max thresholds $u _ { i }$ and $\ell _ { i }$ for $i \in$ <sub>[</sub>1, ??<sub>]</sub> ploted using example parameters (<sup>??</sup> = <sup>10</sup>).

## Designing the Double Threshold Values

A key component of the <sub>DTPR</sub> algorithms for both variants are the thresholds in Equations (5) and (6). The key idea is to design the thresholds by incorporating the switching cost into the balancing rules as a hedge against possible worst-case scenarios. To accomplish this, we enumerate three dificult cases that <sub>DTPR</sub> may encounter. (<sub>CASE-1</sub>): Consider an input sequence where <sub>DTPR</sub> does not accept any prices before it is forced to accept the last ?? prices. Here, the enforced prices in the worst-case sequence will be ?? for <sub>OPR-min</sub> and ?? for <sub>OPR-max</sub>. This sequence occurs only if no price in the sequence meets the first threshold for acceptance. On the other hand, in the case that <sub>DTPR</sub> does accept prices before the end of the sequence, we can further divide the possible sequences into two extreme cases for the <sub>switching</sub> <sub>cost</sub> it incurs. (<sub>CASE-2</sub>): In one extreme, the algorithm incurs only the minimum switching cost of $^ { 2 \beta , }$ , meaning that ?? contiguous prices are accepted by <sub>DTPR</sub>. (<sub>CASE-3</sub>): In the other extreme, <sub>DTPR</sub> incurs the maximum switching cost of $k 2 \beta ,$ meaning that ?? non-contiguous prices are accepted. Intuitively, in order for <sub>DTPR</sub> to be competitive in either of these extreme cases, the prices accepted in the latter case should be suficiently “good” to absorb the extra switching cost of $( k - 1 ) 2 \beta .$

Given the insight from these cases, we use can use the balancing rule (see Section 2.2) to derive the two threshold families. Let $\sigma$ be any arbitrary sequence for <sub>OPR</sub>. Given these extreme input sequences, we now concretely show how to write the balancing rule equations. We consider the cases of <sub>DTPR-min</sub> and <sub>DTPR-max</sub> separately below.

<sub>Balancing</sub> <sub>equations</sub> <sub>for</sub> <sub>DTPR-min.</sub> To balance between possible inputs for <sub>OPR-min</sub>, consider the following examples for three diferent values of $c _ { \operatorname* { m i n } } ( \sigma ) > \ell , \ell = \{ \ell _ { 1 } , \ell _ { 2 } , \ell _ { 3 } \} . \operatorname { I f } c _ { \operatorname* { m i n } } ( \sigma ) > \ell _ { i }$ , we know that <sub>OPT</sub> cannot do better than $k \ell _ { i } + 2 \beta .$ . Suppose that ?? is the target competitive ratio. Then each term in equation (7) corresponds to a diferent <sub>case</sub> (e.g. a possible input), and we solve for the threshold values by “balancing” between all of these possible cases:

$$
\begin{array}{l} \frac {\mathrm{DTPR-min} (\sigma)}{\mathrm{OPT} (\sigma)} \leq \underbrace {\frac {k U + 2 \beta}{k \ell_ {1} + 2 \beta}} _ {c _ {\min} (\sigma) > \ell_ {1}} = \underbrace {\frac {\ell_ {1} + (k - 1) U + 4 \beta}{k \ell_ {2} + 2 \beta}} _ {c _ {\min} (\sigma) > \ell_ {2}} = \frac {u _ {1} + (k - 1) U + 2 \beta}{k \ell_ {2} + 2 \beta} \dots \\ \dots = \underbrace {\frac {\ell_ {1} + \ell_ {2} + (k - 2) U + 6 \beta}{k \ell_ {3} + 2 \beta}} = \frac {\ell_ {1} + u _ {2} + (k - 2) U + 4 \beta}{k \ell_ {3} + 2 \beta} = \frac {u _ {1} + u _ {2} + (k - 2) U + 2 \beta}{k \ell_ {3} + 2 \beta} = \dots = \alpha . \end{array}\tag{7}
$$

As an example, consider $c _ { \mathrm { m i n } } ( \sigma ) > \ell _ { 2 }$ and the corresponding cases enumerated above. Suppose <sub>DTPR-min</sub> accepts one price before the end of the sequence $\sigma ,$ , and the other prices accepted are all ?? . In the first case, where the competitive ratio is $\frac { \stackrel { \wedge } { \ell _ { 1 } } + ( k - 1 ) U + 4 \beta } { k \ell _ { 2 } + 2 \beta }$ , we consider the scenario where <sub>DTPR-min</sub> switches twice: once to accept the price $\ell _ { 1 }$ , and once to accept <sub>(</sub>?? <sub>−</sub> 1<sub>)</sub> prices at the end of the sequence, incurring switching cost of $4 \beta .$

In the second case, where the competitive ratio is $\frac { u _ { 1 } + ( k - 1 ) U + 2 \beta } { k \ell _ { 2 } + 2 \beta }$ , we consider the hypothetical scenario where <sub>DTPR-min</sub> only switches once to accept some value $u _ { 1 }$ followed by $( k - 1 )$ prices at the end of the sequence, incurring switching cost of $2 \beta .$ . By enumerating cases in this fashion for the other possible values of $c _ { \operatorname* { m i n } } ( \sigma )$ , we derive a relationship between the lower thresholds $\ell _ { i }$ and the upper thresholds $u _ { i }$ in terms of the switching cost.

<sub>Balancing</sub> <sub>equations</sub> <sub>for DTPR-max.</sub> The same idea extends to balance between possible inputs for <sub>OPR-max</sub>. Consider the following examples for a few values of $c _ { \mathrm { m a x } } ( \sigma ) . \mathrm { I f } c _ { \mathrm { m a x } } ( \sigma ) < u _ { i }$ , we know that <sub>OPT</sub> cannot do better than $k u _ { i } - 2 \beta$ . Suppose that $\omega$ is the target competitive ratio, and we balance between the following possible cases (e.g. possible inputs):

$$
\begin{array}{l} \frac {\mathrm{OPT} (\sigma)}{\mathrm{DTPR-max} (\sigma)} \leq \underbrace {\frac {k u _ {1} - 2 \beta}{k L - 2 \beta}} _ {c _ {\max} (\sigma) <   u _ {1}} = \underbrace {\frac {k u _ {2} - 2 \beta}{u _ {1} + (k - 1) L - 4 \beta}} _ {c _ {\max} (\sigma) <   u _ {2}} = \frac {k u _ {2} - 2 \beta}{\ell_ {1} + (k - 1) L - 2 \beta} \dots \\ \dots = \underbrace {\frac {k u _ {3} - 2 \beta}{u _ {1} + u _ {2} + (k - 2) L - 6 \beta}} _ {c _ {\max} (\sigma) <   u _ {3}} = \frac {k u _ {3} - 2 \beta}{u _ {1} + \ell_ {2} + (k - 2) L - 4 \beta} = \frac {k u _ {3} - 2 \beta}{\ell_ {1} + \ell_ {2} + (k - 2) L - 2 \beta} = \dots = \omega . \end{array}\tag{8}
$$

<sub>Solving</sub> <sub>for</sub> <sub>the</sub> <sub>threshold</sub> <sub>values.</sub> Given the above balancing equations for both the minimization and maximization variants, the next step is to solve for the unknown values of $\ell _ { i }$ and $u _ { i } .$ . The following observation summarizes the key insight that enables this. We show that one can express each $\ell _ { i }$ in terms of $u _ { i }$ and $\beta ,$ which facilitates the analysis required to solve for thresholds in each balancing equation (given by Equations (7) and (8)).

Observation 3. <sub>By</sub> <sub>letting</sub> $u _ { i } = \ell _ { i } + 2 \beta \forall i \in [ 1 , k ]$ , we obtain each possible worst-case permutation $o f \ell _ { i }$ thresholds, $u _ { i }$ thresholds, and switching cost. Let $y \in \left[ 1 , k - 1 \right]$ denote the number of switches incurred by DTPR.

For DTPR-min, suppose that $c _ { \operatorname* { m i n } } ( \sigma ) > \ell _ { j + 1 }$ . By the definition of DTPR-min, we know that accepting any $u _ { i }$ helps avoid a switching cost of +<sup>2??</sup> in the worst case. Thus,

$$
\sum_ {i = 0} ^ {j} u _ {i} + (k - j) U + 2 \beta = \underbrace {\ell_ {i} + \ldots} _ {y} + \underbrace {u _ {i} + \ldots} _ {j - y} + (k - j) U + (y + 1) 2 \beta = \sum_ {i = 0} ^ {j} \ell_ {i} + (k - j) U + (j + 1) 2 \beta .
$$

For DTPR-max, suppose that $c _ { \operatorname* { m a x } } ( \sigma ) < u _ { j + 1 }$ . By the definition of DTPR-max, we know that accepting any $\ell _ { i }$ helps avoid a switching cost $o f - 2 \beta$ in the worst case. Thus,

$$
\sum_ {i = 0} ^ {j} \ell_ {i} + (k - j) L - 2 \beta = \underbrace {u _ {i} + \dots} _ {y} + \underbrace {\ell_ {i} + \dots} _ {j - y} + (k - j) L - (y + 1) 2 \beta = \sum_ {i = 0} ^ {j} u _ {i} + (k - j) L - (j + 1) 2 \beta .
$$

With the above observation, for <sub>DTPR-min</sub>, one can substitute $u _ { i } - 2 \beta$ for each $\ell _ { i } .$ . By comparing adjacent terms in Equation $( 7 )$ , standard algebraic manipulations give a closed form for each $u _ { i }$ in terms of $u _ { 1 }$ . Setting $\begin{array} { r } { \frac { k U + 2 \beta } { k ( u _ { 1 } - 2 \beta ) + 2 \beta } = \alpha _ { \mathrm { : } } } \end{array}$ , we obtain the explicit expression for $u _ { 1 }$ , yielding a closed formula for $\{ u _ { i } \} _ { i \in [ 1 , k ] }$ and $\{ \ell _ { i } \} _ { i \in [ 1 , k ] }$ in Equation (5). Considering the balancing rule in Equation (7) for the case where $c _ { \operatorname* { m i n } } ( \sigma ) \geq \ell _ { k + 1 } ,$ it follows that $\ell _ { k + 1 } = L$ , and thus $u _ { k + 1 } = L + 2 \beta .$ . By substituting this value into Definition 1, we obtain an explicit expression for ?? as shown in Equation (9).

Conversely, for <sub>DTPR-max</sub>, we substitute $\ell _ { i } + 2 \beta$ for each $u _ { i }$ . By comparing adjacent terms in Equation (8), standard methods give a closed form for each $\ell _ { i }$ in terms of $\ell _ { 1 }$ . Setting $\begin{array} { r } { \frac { { \bf \tilde { k } } ( \ell _ { 1 } + 2 \beta ) - 2 \beta } { k L - 2 \beta } = \omega } \end{array}$ we obtain the explicit expression for $\ell _ { 1 } { \mathrm { : } }$ yielding the closed formula for $\{ \ell _ { i } \} _ { i \in [ 1 , k ] }$ and $\{ u _ { i } \} _ { i \in [ 1 , k ] }$ in Equation (6). Considering the balancing rule in Equation (8) for the case where $c _ { \operatorname* { m a x } } ( \sigma ) \leq u _ { k + 1 }$ it follows that $u _ { k + 1 } = U _ { \ast }$ , and thus $\ell _ { k + 1 } = U - 2 \beta .$ . By substituting this value into Definition 2, we obtain an explicit expression for ?? as shown in Equation (10).

## 4 MAIN RESULTS

We now present competitive results of <sub>DTPR</sub> for both variants of <sub>OPR</sub> and discuss the significance of the results in relation to other algorithms for related problems. Our results for the competitive ratios of <sub>DTPR-min</sub> and <sub>DTPR-max</sub> are summarized in Theorems 4 and 5. We also state the lower bound results for any deterministic online algorithms for <sub>OPR-min</sub> and <sub>OPR-max</sub> in Theorems 8 and 9. Proofs of the results for <sub>DTPR-min</sub> and <sub>DTPR-max</sub> are deferred to Section 5 and Appendix B, respectively. Formal proofs of lower bound theorems are given in Appendix D, and a sketch is shown in Section 5.2. Note that in the competitive results, $W ( x )$ denotes the Lambert ?? function, i.e., the inverse of $f ( x ) = x e ^ { x }$ . It is well-known that $W ( x )$ behaves like ln<sub>(</sub>??<sub>)</sub> [21, 43]. We start by presenting our competitive bounds on <sub>DTPR-min</sub> and <sub>DTPR-max</sub>.

<sup>Theorem</sup> <sup>4.</sup> DTPR-min is an <sup>??</sup>-competitive deterministic algorithm for OPR-min, where <sup>??</sup> is the unique positive solution of ?? T 2?? 2 ??

$$
\frac {U - L - 2 \beta}{U (1 - 1 / \alpha) - \left(2 \beta - \frac {2 \beta}{k} + \frac {2 \beta}{k \alpha}\right)} = \left(1 + \frac {1}{k \alpha}\right) ^ {k}.\tag{9}
$$

<sup>Theorem</sup> <sup>5.</sup> DTPR-max is an <sup>??</sup>-competitive deterministic algorithm for OPR-max, where <sup>??</sup> is the unique positive solution of ?? <sub>−</sub> ?? <sub>−</sub> 2?? ?? ??

$$
\frac {U - L - 2 \beta}{L (\omega - 1) - 2 \beta \left(1 - \frac {1}{k} + \frac {\omega}{k}\right)} = \left(1 + \frac {\omega}{k}\right) ^ {k}.\tag{10}
$$

These theorems present upper bounds on the competitive ratios, showing their dependence on the problem parameters. To investigate the behavior of these competitive ratios, in Figures 3 and 4, we show the competitive ratios of both algorithms as problem parameters are varied. More specifically, in Figure 3, we visualize ?? as a function of $\beta$ and $L ,$ , where ?? and ?? are fixed. The color (shown as an annotated color bar on the right-hand side of the plot) represents the order of ??. If $\beta > 0$ and $L \to 0$ , Figure 3 shows that ?? is roughly $O \left( k \right)$ , which we discuss further in Corollary 6(a). In Figure 4, we visualize $\omega$ as a function of $\beta$ and $L ,$ where ?? and ?? are fixed. The color represents the order of ??. In the dark blue region of the plot, Figure 4 shows that $\omega $ <sub>∞</sub> when $\begin{array} { r } { b = \frac { 2 \beta } { L } \to k } \end{array}$ which provides insight into the extreme case for switching cost when $\begin{array} { r } { \beta \gtrsim \frac { k L } { 2 } } \end{array}$

![](lechowicz2023_online_pause_resume_assets/images/2d889c75f0b40fafed8c6f53950b8d4d178680bc34e59fca426cd1ed3cbb14a7.jpg)  
Fig. 3. DTPR-min: Ploting actual values of competi tive ratio <sup>??</sup> for fixed $k \geq 1$ , fixed $U > L$ , and varying values for <sup>??</sup> and $\beta$ (switching cost). Color represents the order of <sup>??</sup> for a given seting of <sup>??</sup> and $\beta .$

![](lechowicz2023_online_pause_resume_assets/images/b48341aa847d9bffb39d4042f2d33956897033c076ea0434f1e886599237e3e2.jpg)  
Fig. 4. DTPR-max: Ploting actual values of competitive ratio <sup>??</sup> for fixed $k \geq 1$ , fixed $U > L ,$ and varying values for <sup>??</sup> and $\beta$ (switching cost). Color represents the order of <sup>??</sup> for a given seting of <sup>??</sup> and $\beta .$

To obtain additional insight into the form of the competitive ratios in Theorems 4 and 5, we present the following corollaries for two asymptotic regimes of interest: <sub>REGIME-1</sub> captures the order of the competitive ratio when ?? is fixed and ?? or ?? are suficiently large, and <sub>REGIME-2</sub> captures the order of the competitive ratio when $k \to \infty$

<sup>Corollary</sup> <sup>6.</sup> (a) For REGIME-1, with fixed $k \geq 1$ and $\beta \in ( 0 , \frac { U - L } { 2 } )$ , the competitive ratio of DTPR-min is

$$
\alpha \sim \frac {k \beta}{k L + 2 \beta} + \sqrt {\frac {k ^ {2} L U + 2 k L \beta + 2 k U \beta + 4 \beta^ {2} + k ^ {2} \beta^ {2}}{k ^ {2} L ^ {2} + 4 k L \beta + 4 \beta^ {2}}}, \quad a n d \alpha \sim O (k) f o r L \rightarrow 0.
$$

(b) Furthermore, for REGIME-2, with $k  \infty$ and $\begin{array} { r } { c = \frac { 2 \beta } { U } , c \in ( 0 , \frac { U - L } { U } ) } \end{array}$ , the competitive ratio of DTPR-min is

$$
\alpha \sim \left[ W \left(\frac {(c + \frac {1}{\theta} - 1) e ^ {c}}{e}\right) - c + 1 \right] ^ {- 1}.
$$

<sup>Corollary</sup> <sup>7.</sup> (a) For REGIME-1, with fixed $k \geq 1$ and $\begin{array} { r } { b = \frac { 2 \beta } { L } , b \in ( 0 , k ) } \end{array}$ , the competitive ratio of DTPR-max is

$$
\omega \sim O \left(\sqrt [ k + 1 ]{k ^ {k} \frac {k \theta}{k - b}}\right),
$$

and (b) for REGIME-2, with <sup>??</sup> → ∞ and $\begin{array} { r } { b = \frac { 2 \beta } { L } , b \in \left( 0 , k \right) } \end{array}$ , the competitive ratio of DTPR-max is

$$
\omega \sim W \left(\frac {\theta - 1 - b}{e ^ {1 + b}}\right) + 1 + b.
$$

Corollary 6(a) contextualizes the behavior of ?? (the competitive ratio of <sub>DTPR-min</sub>) in the most relevant <sub>OPR-min</sub> setting (when $\begin{array} { r } { \beta \in ( 0 , \frac { U - L } { 2 } ) ) } \end{array}$ . Note that in this minimization setting, as $\beta$ grows, the competitive ratio improves. Let us also briefly discuss the other cases for the switching cost $\beta ,$ and why this interval makes sense. When $\beta > \frac { U - L } { 2 }$ , the switching cost is large enough such that <sub>OPT</sub> only incurs a switching cost of $2 \beta .$ . In this regime, $\alpha$ does not fully capture the competitive ratio of <sub>DTPR-min</sub>, since every value in the threshold family $\{ u _ { i } \} _ { i \in [ 1 , k ] }$ is at least $U _ { \mathrm { i } }$ ; in other words, whenever the algorithm begins accepting prices, it will accept ?? prices in a single continuous segment, incurring minimal switching cost of 2??. $\operatorname { A s } \beta \to \infty$ , the competitive ratio of <sub>DTPR-min</sub> approaches 1. This theoretical result corresponds nicely with the empirical observation in [20] that a large switching overhead can nullify carbon emission reductions from temporal shifting if the job is interrupted frequently.

Conversely, Corollary $7 ( \mathrm { a } )$ contextualizes the behavior of ?? in the most relevant <sub>OPR-max</sub> setting (when $\beta \in ( 0 , \frac { k L } { 2 } ) )$ , but we also discuss the other cases for the switching cost $\beta ,$ and why this interval makes sense. When $\begin{array} { r } { \beta \ge \frac { k L } { 2 } } \end{array}$ , the switching cost is too large, and the competitive ratio may become unbounded. Note that this is shown explicitly in Figure 4. Consider an adversarial sequence which forces any <sub>OPR-max</sub> algorithm to accept ?? prices with value ?? at the end of the sequence. On such a sequence, even a player which incurs the minimum switching cost of $2 \beta$ achieves zero or negative profit of $k L - 2 \beta \leq 0 .$ , and this is not well-defined.

Next, to begin to investigate the tightness of Theorems 4 and 5, it is interesting to consider special cases that correspond to models studied in previous work. In particular, when $\beta = 0$ , i.e., there is no switching cost, <sub>OPR</sub> degenerates to the ??-search problem [29]. For fixed $k \geq 1$ and $\theta \to \infty$ , the optimal competitive ratios shown by [29] are $\sqrt { \theta / 2 }$ for ??-min, and $\sqrt [ k ] { k ^ { k } \theta }$ for ??-max (see Section 2.2).

Both versions of <sub>DTPR</sub> exactly recover the optimal ??-search algorithms $[ 2 9 ] . ^ { 2 }$ Figure 3 shows that if $\beta = 0$ and $L \to 0$ , then $\alpha \to \infty$ , which matches the ??-min result of ${ \sqrt { \theta / 2 } } \sim \infty$ . Similarly, Figure 4 shows that if $\beta = 0$ and $L \to 0 ,$ , then $\omega  \infty$ , which matches the ??-max result of $\sqrt [ k ] { k ^ { k } \theta } \sim \infty$

More generally, one can ask if the competitive ratios of <sub>DTPR</sub> can be improved upon by other online algorithms outside of the special case of ??-search. Our next set of results highlights that no improvement is possible, i.e., that <sub>DTPR-min</sub> and <sub>DTPR-max</sub> maintain the optimal competitive ratios possible for any deterministic online algorithm for <sub>OPR</sub>.

Theorem 8. <sub>Let</sub> $k \geq 1 , \theta \geq 1$ , and $\textstyle { \beta \in ( 0 , \frac { U - L } { 2 } ) }$ . Then <sup>??</sup> given by Equation <sup>(9)</sup> is the best competitive ratio that a deterministic online algorithm for OPR-min can achieve.

Theorem 9. <sub>Let</sub> $k \geq 1 , \theta \geq 1$ , and $\beta \in \left( 0 , \frac { k L } { 2 } \right)$ . Then <sup>??</sup> given by Equation (10) is the best competitive ratio that a deterministic online algorithm for OPR-max can achieve.

By combining Theorems 4 and 5 with Theorems 8 and 9, these results imply that the competitive <sup>ratios</sup> <sup>of</sup> DTPR-min <sup>and</sup> DTPR-max <sup>are</sup> <sup>optimal</sup> <sup>for</sup> OPR-min <sup>and</sup> OPR-max<sup>.</sup>

Finally, it is interesting to contrast the upper and lower bounds for <sub>OPR</sub> with those for ??-search, since the contrast highlights the impact of switching costs. In <sub>OPR-min</sub>, when $\beta > 0 ;$ , we find that the <sub>DTPR-min</sub> competitive results <sub>improve</sub> on optimal results for ??-min search (where $\beta = 0$ is assumed), particularly in the case where ?? approaches $0 ( { \mathrm { i . e . , } } \theta  \infty )$ . Since Theorem 8 implies that <sub>DTPR-min</sub> is optimal, this shows that the addition of switching cost in <sub>OPR-min</sub> enables an online algorithm to achieve a better competitive ratio compared to ??-min search, which is a surprising result. In contrast, for <sub>OPR-max</sub> with $\beta > 0$ , <sub>DTPR-max</sub>’s competitive bounds are <sub>worse</sub> than existing results for ??-max search, particularly for large $\beta .$ Since Theorem 9 implies that <sub>DTPR-max</sub> is optimal, this suggests that <sub>OPR-max</sub> is fundamentally a <sub>more</sub> <sub>dificult</sub> problem compared to ??-max search.

We note that although the lower bounds shown in Theorems 8 and 9 specifically apply to deterministic algorithms, there are lower bounds in the literature for randomized ??-search [29]. The randomized bound for ??-min search are not an order-improvement over the deterministic lower bound, while the randomized results for ??-max search improve the lower bound to <sub>Ω(</sub>ln ??<sub>)</sub>. However, in the regimes of ?? which are interesting for applications (where ?? is suficiently large), there will be a small diference between the deterministic upper bound and the randomized lower bound in practice. Combined, these results for ??-search suggest that randomization similarly may not yield large improvements in the <sub>OPR</sub> setting. Exploring this dynamic further for <sub>OPR</sub> is an interesting direction for future work

## 5 PROOFS

We now prove the results described in the previous section. In Section 5.1, we prove the <sub>DTPR-min</sub> results presented in Theorem 4 and Corollary 6. In Section 5.2, we provide a proof sketch for the lower bound results in Theorems 8 and 9, and defer the formal proofs to Appendix D. The competitive results for <sub>DTPR-max</sub> in Theorem 5 and Corollary 7 are deferred to Appendix B.

## 5.1 Competitive Results for DTPR-min

We begin by proving Theorem 4 and Corollary 6. The key novelty in the proof of the main competitive results (Theorems 4 and 5) lies in our efort to derive two threshold functions and balance the competitive ratio in several worst-case instances with respect to these thresholds, as outlined in Section 3.

Proof of Theorem 4. For $0 \leq j \leq k _ { \mathrm { { i } } }$ , let $S _ { j } \subseteq S$ be the sets of <sub>OPR-min</sub> price sequences for which <sub>DTPR-min</sub> accepts exactly ?? prices (excluding the $k - j$ prices it is forced to accept at the end of the sequence). Then, all of the possible price sequences for <sub>OPR-min</sub> are represented by $\textstyle S = \bigcup _ { j = 0 } ^ { k } S _ { j }$ . Also, recall that by definition, $\ell _ { k + 1 } = L$ . Let $\epsilon > 0$ be a fixed constant, and define the following two price sequences $\sigma _ { j }$ and $\rho _ { j } \colon$

$$
\begin{array}{c} \forall j \in [ 2, k ]: \sigma_ {j} = \ell_ {1}, u _ {2}, \ldots , u _ {j}, U, \underbrace {\ell_ {j + 1} + \epsilon , \ldots , \ell_ {j + 1} + \epsilon} _ {k}, \underbrace {U , U , \ldots , U} _ {k}. \\ \forall j \in [ 2, k ]: \rho_ {j} = \ell_ {1}, U, \ell_ {2}, U, \ldots , U, \ell_ {j}, U, \underbrace {\ell_ {j + 1} + \epsilon , \ldots , \ell_ {j + 1} + \epsilon} _ {k}, \underbrace {U , U , \ldots , U} _ {k}. \end{array}
$$

There are two special cases for $j = 0$ and $j = 1$ . For $j = 0$ , we have that $\sigma _ { 0 } = \rho _ { 0 }$ , and this sequence simply consists of $\ell _ { 1 } + \epsilon$ repeated ?? times, followed by ?? repeated ?? times. For $j = 1$ , we also have that $\sigma _ { 1 } = \rho _ { 1 }$ , and this sequence consists of one price with value $\ell _ { 1 }$ and one price with value ?? , followed by $\ell _ { 2 } + \epsilon$ repeated ?? times and ?? repeated ?? times.

Observe that as $\epsilon  0 , \sigma _ { j }$ and $\rho _ { j }$ are sequences yielding the worst-case ratios in $S _ { j }$ <sup>,</sup> <sup>as</sup> DTPR-min is forced to accept $( k - j )$ worst-case ?? values at the end of the sequence, and each accepted value is exactly equal to the corresponding threshold.

Note that $\sigma _ { j }$ and $\rho _ { j }$ also represent two extreme possibilities for the additive switching cost. In $\sigma _ { j } ,$ <sub>DTPR-min</sub> only switches twice, but it mostly accepts values $u _ { i }$ . In $\rho _ { j }$ , <sub>DTPR-min</sub> must switch $j + 1$ times because there are many intermediate ?? values, but it only accepts values $\ell _ { i }$ .

In the worst case, we have

$$
\frac {\text { DTPR - min } (\sigma_ {j})}{\text { OPT } (\sigma_ {j})} = \frac {\text { DTPR - min } (\rho_ {j})}{\text { OPT } (\rho_ {j})}.
$$

Also, the optimal solutions for both sequences are lower bounded by the same quantity: $k c _ { \mathrm { m i n } } ( \sigma _ { j } ) + 2 \beta = k c _ { \mathrm { m i n } } ( \rho _ { j } ) + 2 \beta$ . For any sequence ?? in $S _ { j }$ , we have that $c _ { \operatorname* { m i n } } ( s ) ~ > ~ \ell _ { j + 1 }$ , so $\mathsf { O P T } ( \rho _ { j } ) = 0 \mathsf { P T } ( \sigma _ { j } ) \le k \ell _ { j + 1 } + 2 \beta .$

By definition of the threshold families $\{ \ell _ { i } \} _ { i \in [ 1 , k ] }$ and $\{ u _ { i } \} _ { i \in [ 1 , k ] }$ , we know that $\textstyle \sum _ { i = 1 } ^ { j } \ell _ { i } + j 2 \beta = \sum _ { i = 1 } ^ { j } u _ { i }$ for any value $j \geq 2 $

$$
\mathrm{DTPR-min} \left(\rho_ {j}\right) = \left(\sum_ {i = 1} ^ {j} \ell_ {i} + (k - j) U + (j + 1) 2 \beta\right) = \left(\ell_ {1} + \sum_ {i = 2} ^ {j} u _ {i} + (k - j) U + 4 \beta\right) = \mathrm{DTPR-min} \left(\sigma_ {j}\right).
$$

Note that whenever $j < 2 ,$ , we have that $\sigma _ { 0 } = \rho _ { 0 }$ , and $\sigma _ { 1 } = \rho _ { 1 }$ . Thus, ${ \mathsf { D T P R - m i n } } ( \rho _ { j } ) = { \mathsf { D T P R - m i n } } ( \sigma _ { j } )$ holds for any value of ??. By definition of $\ell _ { 1 }$ , we simplify $\begin{array} { r } { \ell _ { 1 } + \sum _ { i = 2 } ^ { j } u _ { i } + ( k - j ) U + 4 \beta } \end{array}$ to $\begin{array} { r } { \sum _ { i = 1 } ^ { j } u _ { i } + ( k - j ) U + 2 \beta . } \end{array}$ . Then, for any sequence $s \in S _ { j }$ , we have the following:

$$
\frac {\mathrm{DTPR-min} (s)}{\mathrm{OPT} (s)} \leq \frac {\mathrm{DTPR-min} (\sigma_ {j})}{\mathrm{OPT} (\sigma_ {j})} = \frac {\mathrm{DTPR-min} (\rho_ {j})}{\mathrm{OPT} (\rho_ {j})} \leq \frac {\sum_ {i = 1} ^ {j} u _ {i} + (k - j) U + 2 \beta}{k \ell_ {j + 1} + 2 \beta}.\tag{11}
$$

Before proceeding to the next step, we use an intermediate result stated in the following lemma with a proof given in Appendix C.

Lemma 10. <sub>For</sub> <sub>any</sub> $0 \leq j \leq k ,$ , by definition o $\mathcal { f } \left\{ \ell _ { i } \right\} _ { i \in [ 1 , k ] } a n d \left\{ u _ { i } \right\} _ { i \in [ 1 , k ] }$

$$
\sum_ {i = 1} ^ {j} u _ {i} + (k - j) U + 2 \beta \leq \alpha \cdot (k \ell_ {j + 1} + 2 \beta).
$$

For $\epsilon  0$ , the competitive ratio <sub>DTPR-mi</sub> $\mathsf { n } / \mathsf { O P T }$ is exactly ??:

$$
\forall 0 \leq j \leq k: \frac {\mathrm{DTPR-min} (\sigma_ {j})}{\mathrm{OPT} (\sigma_ {j})} = \frac {\sum_ {i = 1} ^ {j} u _ {i} + (k - j) U + 2 \beta}{k \ell_ {j + 1} + 2 \beta} = \alpha ,
$$

and thus for any sequence $s \in S$

$$
\forall s \in \mathcal {S}: \quad \frac {\text { DTPR - min } (s)}{k c _ {\min} (s) + 2 \beta} \leq \alpha .
$$

Since $0 \mathsf { P T } ( s ) \geq k c _ { \operatorname* { m i n } } ( s ) + 2 \beta$ for any sequence ??, this implies that <sub>DTPR-min</sub> is ??-competitive. □

Proof of Corollary 6. To show part <sub>(a)</sub> for <sub>REGIME-1</sub>, with fixed $k \geq 1$ , observe that we can expand the right-hand side of Equation (9) using the binomial theorem to obtain the following:

$$
\frac {U - L - 2 \beta}{U \left(1 - \frac {1}{\alpha}\right) - 2 \beta \left(1 - \frac {1}{k} + \frac {1}{k \alpha}\right)} = 1 + \frac {1}{\alpha} + \Theta \left(\alpha^ {- 2}\right).
$$

Next, observe that $\alpha ^ { \star }$ solving the following expression satisfies $\alpha ^ { \star } \geq \alpha \forall k : k \geq 1 , ( \mathrm { i . e . } \alpha ^ { \star }$ is an upper bound of $\alpha )$ : 2??

$$
\frac {U - L - 2 \beta}{U \left(1 - \frac {1}{\alpha^ {\star}}\right) - 2 \beta \left(1 - \frac {1}{k} + \frac {1}{k \alpha^ {\star}}\right)} = 1 + \frac {1}{\alpha^ {\star}}.
$$

By solving the above for $\alpha ^ { \star }$ , we obtain

$$
\alpha \sim \alpha^ {\star} = \frac {k \beta}{k L + 2 \beta} + \sqrt {\frac {k ^ {2} L U + 2 k L \beta + 2 k U \beta + 4 \beta^ {2} + k ^ {2} \beta^ {2}}{k ^ {2} L ^ {2} + 4 k L \beta + 4 \beta^ {2}}}.
$$

Last, note that as $L \to 0$ , we obtain the following result: $\begin{array} { r } { \alpha \sim \frac { k } { 2 } + \sqrt { \frac { k U } { 2 \beta } + 1 + \frac { k ^ { 2 } } { 4 } } \approx O \left( k \right) } \end{array}$

To show part <sub>(b)</sub> for <sub>REGIME-2</sub>, we first observe that the right-hand side of Equation 9 can be approximated as $\left( 1 + \frac { 1 } { k \alpha } \right) ^ { k } \ \approx \ e ^ { 1 / \alpha }$ when $k \to \infty$ . Then by taking limits on both sides, we obtain the following:

$$
\frac {U - L - 2 \beta}{U \left(1 - \frac {1}{\alpha}\right) - 2 \beta (1)} = e ^ {1 / \alpha}.
$$

For simplification purposes, let $\beta = c U / 2 ,$ , where ?? is a small constant on the interval $\textstyle \left( 0 , { \frac { U - L } { U } } \right)$ We then obtain the following:

$$
\frac {U - L - c U}{U \left(1 - \frac {1}{\alpha}\right) - c U} = e ^ {1 / \alpha} \Longrightarrow L / U + c - 1 = \left(\frac {1}{\alpha} + c - 1\right) e ^ {1 / \alpha}.
$$

By definition of Lambert ?? function, solving this equation for ?? obtains the result in Corollary 6(b).

## 5.2 Lower Bound Analysis: Proof of Theorem 8 (OPR-min Lower Bound)

In Theorems 8 and 9, we state that <sub>any</sub> deterministic strategy achieves a competitive ratio of at least $\alpha$ for <sub>OPR-min</sub>, and at least ?? for <sub>OPR-max</sub>. In this section, we formalize the lower bound construction which proves Theorem 8. A similar construction is used to prove Theorem 9 in Appendix D.1. These two results jointly imply that our proposed <sub>DTPR</sub> algorithms are both optimal.

Proof of Theorem 8. Let <sub>ALG</sub> be a deterministic online algorithm for <sub>OPR-min</sub>, and suppose that the adversary uses the price sequence $\ell _ { 1 } , \ldots , \ell _ { k }$ , which is exactly the sequence defined by (5). $\ell _ { 1 }$ is presented to <sub>ALG</sub>, at most ?? times or until <sub>ALG</sub> accepts it. If <sub>ALG</sub> never accepts $\ell _ { 1 }$ , the remainder of the sequence is all $U ,$ , and <sub>ALG</sub> achieves a competitive ratio of $\begin{array} { r } { \frac { k U + 2 \beta } { k \ell _ { 1 } + 2 \beta } = \alpha , } \end{array}$ as defined in (7).

If <sub>ALG</sub> accepts $\ell _ { 1 } ,$ , the next price presented is $U ,$ repeated at most ?? times <sub>or</sub> <sub>until ALG switches</sub> <sub>to</sub> <sub>reject</sub> ?? . After <sub>ALG</sub> has switched, $\ell _ { 2 }$ is presented to ${ \mathsf { A L G } } ,$ , at most ?? times or until <sub>ALG</sub> accepts it. Again, if <sub>ALG</sub> never accepts $\ell _ { 2 } ,$ , the remainder of the sequence is all $U ,$ and <sub>ALG</sub> achieves a competitive ratio of at least $\begin{array} { r } { \frac { \ell _ { 1 } + ( k - 1 ) { \cal U } + 4 \beta } { k \ell _ { 2 } + 2 \beta } = \alpha . } \end{array}$ as defined in (7).

As the sequence continues, whenever <sub>ALG</sub> does not accept some $\ell _ { i }$ after it is presented ?? times, the adversary increases the price to ?? for the remainder of the sequence. Otherwise, if <sub>ALG</sub> accepts ?? prices before the end of the sequence, the adversary concludes by presenting ?? at least ?? times.

Observe that any <sub>ALG</sub> which does not immediately reject the first ?? presented to it after accepting some $\ell _ { i }$ obtains a competitive ratio strictly worse than $\alpha .$ To illustrate this, suppose <sub>ALG</sub> has just accepted $\ell _ { 1 }$ , incurring a cost of $\ell _ { 1 } + \beta$ so far. The adversary begins to present ?? , and <sub>ALG</sub> accepts $y \le ( k - 1 )$ of these ?? prices before switching away. If $y = ( k - 1 )$ , <sub>ALG</sub> will accept ?? prices before the end of the sequence and achieve a competitive ratio of $\frac { \ell _ { 1 } + ( k - 1 ) U + 2 \beta } { k L + 2 \beta } > \alpha$ . Otherwise, if $y < ( k - 1 )$ , the cost incurred by <sub>ALG</sub> so far is at least $\ell _ { 1 } + 2 \beta + y U$ , while the cost incurred by <sub>ALG</sub> if it had immediately switched away $( y = 0 )$ would be $\ell _ { 1 } + 2 \beta -$ since any price which might be accepted by <sub>ALG</sub> in the future should be $\leq U$ , the latter case strictly improves the competitive ratio of ALG.

Assuming that <sub>ALG</sub> does immediately reject any ?? presented to it, and that <sub>ALG</sub> accepts some prices before the end of the sequence, the competitive ratio attained by <sub>ALG</sub> is at least $\begin{array} { r } { \frac { \sum _ { i = 1 } ^ { j } \ell _ { i } + \bar { ( j + 1 ) } 2 \beta + ( k - j ) U } { k \ell _ { i + 1 } + 2 \beta } = \alpha . } \end{array}$ , as defined in (7).

Similarly, if <sub>ALG</sub> accepts ?? prices before the end of the sequence, the competitive ratio attained by <sub>ALG</sub> is at least $\begin{array} { r } { \frac { \sum _ { i = 1 } ^ { k } \ell _ { i } + k 2 \beta } { k L + 2 \beta } = \alpha . } \end{array}$ , as defined in (7).

Since any arbitrary deterministic online algorithm <sub>ALG</sub> cannot achieve a competitive ratio better than ?? playing against this adaptive adversary, our proposed algorithm <sub>DTPR-min</sub> is optimal. □

## 6 CASE STUDY: CARBON-AWARE TEMPORAL WORKLOAD SHIFTING

We now present experimental results for the <sub>DTPR</sub> algorithms in the context of the carbon-aware temporal workload shifting problem. We evaluate <sub>DTPR-min</sub> (and <sub>DTPR-max</sub> in Appendix A) as compared to existing algorithms from the literature that have been adapted for <sub>OPR</sub>.

Table 2. Summary of carbon trace data sets

<table><tr><td>Location</td><td>Pacific NW, U.S.</td><td>New Zealand</td><td>Ontario, Canada</td></tr><tr><td>Number of Data Points</td><td>10,144</td><td>1,324</td><td>17,898</td></tr><tr><td>Max. Carbon Intensity (U)</td><td>648 gCO2eq/kWh</td><td>165 gCO2eq/kWh</td><td>181 gCO2eq/kWh</td></tr><tr><td>Min. Carbon Intensity (L)</td><td>18 gCO2eq/kWh</td><td>54 gCO2eq/kWh</td><td>15 gCO2eq/kWh</td></tr><tr><td>Duration (mm/dd/yy)</td><td>04/20/22 - 12/06/22</td><td>10/19/21 - 11/16/21</td><td>10/19/21 - 12/06/22</td></tr></table>

## 6.1 Experimental Setup

We consider a carbon-aware load shifting system that operates on a hypothetical data center. An algorithm is given a deferrable and interruptible job that takes ?? time slots to complete, along with a deadline $T \geq k ,$ , such that the job must be completed at most ?? slots after its arrival. The objective is to selectively run units of the job such that the total carbon emissions are minimized while still completing the job before its deadline.

For the minimization variant (<sub>OPR-min</sub>) of the experiments, we consider <sub>carbon</sub> <sub>emissions</sub> <sub>inten-</sub> <sub>sities</sub>, as the price values. At each time step ??, the electricity supply has a carbon intensity $c _ { t } .$ , i.e., if the job is being processed during the time step ?? $( x _ { t } = 1 )$ , the data center’s carbon emissions during that time step are proportional to $c _ { t } .$ If the job is <sub>not</sub> being processed during the time step ?? $( x _ { t } = 0 )$ we assume for simplicity that carbon emissions in the idle state are negligible and essentially 0. To model the combined computational overhead of interrupting, checkpointing, and restarting the job, the algorithm incurs a fixed switching cost of $\beta$ whenever $x _ { t - 1 } \neq x _ { t }$ , whose values are selected relative to the price values.

<sub>Carbon</sub> <sub>data</sub> <sub>traces.</sub> We use real-world carbon traces from Electricity Maps [32], which provide time-series information about the <sub>average</sub> <sub>carbon</sub> <sub>emissions</sub> <sub>intensity</sub> of the electric grid. We use traces from three diferent regions: the Pacific Northwest of the U.S., New Zealand, and Ontario, Canada. The data is provided at an hourly granularity and includes the current average carbon emissions intensity in grams of $\mathrm { C O _ { 2 } }$ equivalent per kilowatt-hour $\mathrm { ( g C O _ { 2 } e q / k W h ) }$ , and the percentage of electricity being supplied from carbon-free sources. In Figure 9 (in Appendix A), we plot three representative actual traces for carbon intensity over time for a 96-hour period in each region.

<sub>Parameter</sub> <sub>settings.</sub> We test for time horizons (?? ) of 48 hours, 72 hours, and 96 hours. The chosen time horizon represents the time at which the job with length ?? must be completed. As is given in the carbon trace data, we consider time slots of one hour.

The online algorithms we use in experiments take ?? and ?? as parameters for their threshold functions. To set these parameters, we examine the entire carbon trace for the current location. For the Pacific NW trace and the Ontario trace, these values represent lower and upper bounds of the carbon intensity values for a full year. For the New Zealand trace, these values are a lower and upper bound for the values during a month of data, which is reflected by a smaller fluctuation ratio. We set ?? and ?? to be the minimum and maximum observed carbon intensity over the entire trace.

To generate each input sequence, a contiguous segment of size ?? is randomly sampled from the given carbon trace. In a few experiments, we simulate greater <sub>volatility</sub> over time by “scaling up” each price’s deviation from the mean. First, we compute the average value over the entire sequence. Next, we compute the diference between each price and this average. Each of these diferences is scaled by a noise factor of $m \geq 1$ . Finally, new carbon values are computed by summing each scaled diference with the average. If $m = 1$ , we recover the same sequence, and if $m > 1$ , any deviation from the mean is proportionately amplified. Any values which become negative after applying this transformation are truncated to 0. This technique allows us to evaluate algorithms under diferent levels of volatility. As we are in the regime where $L = 0$ , none of the other online algorithms considered have competitive guarantees, since their competitive ratios become unbounded when ?? <sub>→</sub> 0. Instead, our <sub>DTPR</sub> algorithm maintains its optimal bound defined in (9) and (10) due to the presence of switching cost $\beta$ in the competitive bounds. Performance in the presence of greater carbon volatility is important, as on-site renewable generation is seeing greater adoption as a supplementary power source for data centers [1, 36].

Table 3. Summary of algorithms tested in our experiments

<table><tr><td>Algorithm</td><td>Carbon-aware</td><td>Switching-aware</td><td>Description</td></tr><tr><td>OPT (offline)</td><td>YES</td><td>YES</td><td>Optimal offline solution</td></tr><tr><td>Carbon-Agnostic</td><td>NO</td><td>YES</td><td>Runs job in the first k time slots</td></tr><tr><td>Const. Threshold</td><td>YES</td><td>NO</td><td>Runs job if carbon meets threshold  $\sqrt{UL}$  [15]</td></tr><tr><td>k-search</td><td>YES</td><td>NO</td><td>Runs ith slot of job if carbon meets threshold  $\Phi_i$  [29]</td></tr><tr><td>DTPR</td><td>YES</td><td>YES</td><td>This work (algorithms proposed in Section 3)</td></tr></table>

<sub>Benchmark</sub> <sub>algorithms.</sub> To evaluate the performance of <sub>DTPR</sub>, we use a dynamic programming approach to calculate the ofline optimal solution for each given sequence and objective, which allows us to report the empirical competitive ratio for each tested algorithm. We compare <sub>DTPR</sub> against two categories of benchmark algorithms, which are summarized in Table 3.

The first category of benchmark algorithms is <sub>carbon-agnostic</sub> algorithms, which run the jobs during the first ?? time slots in order, i.e., accepting prices $c _ { 1 } , \ldots , c _ { k }$ . This approach incurs the minimal switching cost of $^ { 2 \beta , }$ , because it does not interrupt the job while it is being processed. The carbon-agnostic approach simulates the behavior of a scheduler that runs the job to completion as soon as it is submitted, without any focus on reducing carbon emissions. Note that the performance of this approach significantly varies based on the randomly selected sequence, since it will perform well if low-carbon electricity is available in the first few slots, and will perform poorly if the first few slots are high-carbon.

We also compare <sub>DTPR</sub> against <sub>switching-cost-agnostic</sub> algorithms, which only consider carbon cost. We have two algorithms of this type, each drawing from existing online search methods in the literature. Although they do not consider the switching cost in their design, they still incur a switching cost whenever their decision in adjacent time slots difers.

The first such algorithm is a <sub>constant</sub> <sub>threshold</sub> <sub>algorithm</sub>, which uses the $\sqrt { U L }$ threshold value first presented for online search in [15]. In our minimization experiments, this algorithm runs the workload during the first ?? time slots where the carbon intensity is at most $\sqrt { U L }$

The other switching-cost-agnostic algorithm tested is the ??-search algorithm shown by [29] and described in Section 2.2. The ??-min search algorithm chooses to run the ??th hour of the job during the first time slot where the carbon intensity is at most $\Phi _ { i }$

## 6.2 Experimental Results

We now present our experimental results. Our focus is on the empirical competitive ratio (a lower competitive ratio is better). We report the performance of all algorithms for each experimental setting, in each tested region. Throughout the minimization experiments, we observe that <sub>DTPR-min</sub> outperforms the benchmark algorithms. The 95th percentile worst-case empirical competitive ratio achieved by <sub>DTPR-min</sub> is a 48.2% improvement on the carbon-agnostic method, a 15.6% improvement on the ??-min search algorithm, and a 14.4% improvement on the constant threshold algorithm.

In Figure 5, we show results for three diferent values of horizon ?? in each carbon trace, with fixed $\beta \approx U / 2 0$ , fixed $k = \lceil T / 6 \rceil$ , and no added volatility. Although our experiments test three distinct values for ?? , we later observe that the <sub>ratio</sub> <sub>between</sub> ?? <sub>and</sub> ?? is the primary factor that changes the performance of the algorithms we test; in this figure, <sub>DTPR</sub> and the benchmark algorithms compare

Carbon-Agnostic

![](lechowicz2023_online_pause_resume_assets/images/96831081477136f2d76ca06c1d15cf892b34c477bbe4125e5b5486cc7bf24b67.jpg)  
(a) Ontario, Canada

![](lechowicz2023_online_pause_resume_assets/images/0f1d4c72c13ddbe5b549a9f70bed77761b12db60cb2831f3e55d5d04b9121626.jpg)  
(b) U.S. Pacific Northwest

![](lechowicz2023_online_pause_resume_assets/images/e789d8f6b93641c5197fef88260f9fefe4353ce27208e71bce5a15f614efa4eb.jpg)  
(c) New Zealand  
Fig. 5. Experiments for three distinct time horizons, where <sup>??</sup> ∈ {<sup>48,</sup> <sup>72,</sup> <sup>96</sup>}.

(a): Ontario, Canada carbon trace, with $\theta = 1 2 . 0 \bar { 6 }$ (b): U.S. Pacific Northwest carbon trace, with <sup>??</sup> = <sup>36</sup> (c): New Zealand carbon trace, with <sup>??</sup> = <sup>3.05</sup>  
![](lechowicz2023_online_pause_resume_assets/images/1ef88a7578f7dba4d997d5ca0e21b94a4bb5fbd57e588e7c06c922b781664a51.jpg)  
(a) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/e57225442c4ee735c3426f83934a2bee32f2abbb06b8c2ff784a3d2951487899.jpg)  
(b) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/1e3ebf9b6f9dd545e561db017be59557dd7b14abaf1a2c83ac736708b358410e.jpg)  
(c) Changing volatility

![](lechowicz2023_online_pause_resume_assets/images/0e7a595abd5de207968d14a2f32051e5a2b9afcc9f7da819c831f41af9613845.jpg)  
(d) CDF  
Fig. 6. Experiments on Ontario, Canada carbon trace, with <sup>??</sup> = <sup>12.06</sup>, and <sup>??</sup> = <sup>48</sup>.  
(a): Changing job length <sup>??</sup> w.r.t. time horizon <sup>??</sup> (<sup>??</sup>-axis), vs. competitive ratio (b): Changing switching cost <sup>??</sup> w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (c): Diferent volatility levels w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (d): Cumulative distribution function of competitive ratios

very similarly on the same carbon trace for diferent ?? values. As such, we set $T = 4 8$ in the rest of the experiments in this section for brevity. This represents a <sub>time</sub> <sub>horizon</sub> of 48 hours.

In the first experiment, we test all algorithms for diferent job lengths ?? in the range from 4 hours to ?? <sub>/</sub>2 (24 hours). The switching cost ?? is non-zero and fixed to $\approx U / 2 0 ,$ and no volatility is added to the carbon trace. By testing diferent values for ??, this experiment tests diferent ratios between the workload length and the horizon provided to the algorithm. In Figures 6(a), 7(a), and 8(a), we show that the competitive ratio of <sub>DTPR-min</sub> outperforms others, and it compares particularly favorably for <sub>short</sub> job lengths. Averaging over all regions and job lengths, the competitive ratio achieved by <sub>DTPR-min</sub> is a 11.4% improvement on the carbon-agnostic method, a 14.0% improvement on the ??-min search algorithm, and a 5.5% improvement on the constant threshold algorithm.

In the second experiment, we test all algorithms for diferent switching costs ?? in the range from 0 to $U / 5 .$ . The job length ?? is set to 10 hours, and no volatility is added to the carbon trace. By testing diferent values for ??, this experiment tests how an increasing switching cost impacts the performance of <sub>DTPR-min</sub> with respect to other algorithms which do not explicitly consider the switching cost. In Figures 6(b), 7(b), and 8(b), we show that the observed competitive ratio of <sub>DTPR-min</sub> outperforms the benchmark algorithms for most values of ?? in all regions. Unsurprisingly, the carbon-agnostic technique (which incurs minimal switching cost) performs better as $\beta$ grows. While the constant threshold algorithm has relatively consistent performance, the ??-min search algorithm performs noticeably worse as ?? grows. Averaging over all regions and switching cost values, the competitive ratio achieved by <sub>DTPR-min</sub> is a 18.2% improvement on the carbon-agnostic method, a 8.9% improvement on the ??-min search algorithm, and a 4.1% improvement on the constant threshold algorithm.

![](lechowicz2023_online_pause_resume_assets/images/9c3bd20379a9ebf9802bc2daef712e2b3a26ac35de8a2eaaeb7514eb451b5452.jpg)  
(a) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/cd7a5e649749975e682ae2051ae15fb31624fe07acd2c819e32de02b40742094.jpg)  
(b) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/48546c29b13efe9bc8d7d74f8fafbd1eb0fe3c362e726b322ed3c5063a431b79.jpg)  
(c) Changing volatility

![](lechowicz2023_online_pause_resume_assets/images/2e99c5b3f0aab0a440b3bccdb1681c62cdad5cc97891b431ab2056c1d1111bb9.jpg)  
(d) CDF  
Fig. 7. Experiments on U.S. Pacific Northwest carbon trace, with <sup>??</sup> = <sup>36</sup>, and <sup>??</sup> = <sup>48</sup>.

(a): Changing job length <sup>??</sup> w.r.t. time horizon <sup>??</sup> (<sup>??</sup>-axis), vs. competitive ratio (b): Changing switching cost <sup>??</sup> w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (c): Diferent volatility levels w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (d): Cumulative distribution function of competitive ratios  
![](lechowicz2023_online_pause_resume_assets/images/149c6df9361d8aa1bb6604d5e0bb641be8db7c0535feae279e544a78282996da.jpg)  
(a) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/94535ca2ef4b36d6e037468f7f8d962994e3a138619c6390ecf2436165e09b64.jpg)  
(b) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/ad487013851b1e62e05aaa9ee32c7d860251ea73ff1bb4762c47008feaa1eff9.jpg)  
(c) Changing volatility

![](lechowicz2023_online_pause_resume_assets/images/b10ddd90493f02d7b0c2357f35041274ed506ae6cdf5a1b48ce5d17a6f989139.jpg)  
(d) CDF  
Fig. 8. Experiments on New Zealand carbon trace, with <sup>??</sup> = <sup>3.05</sup>, and <sup>??</sup> = <sup>48</sup>.  
Note: the line for Carbon-Agnostic overlaps the line for Constant Threshold in some of the above plots. (a): Changing job length <sup>??</sup> w.r.t. time horizon <sup>??</sup> (<sup>??</sup>-axis), vs. competitive ratio (b): Changing switching cost <sup>??</sup> w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (c): Diferent volatility levels w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (d): Cumulative distribution function of competitive ratios

In the final experiment, we test all algorithms on sequences with diferent volatility. The job length ?? and switching cost ?? are both fixed as previously. We add volatility by setting a <sub>noise</sub> <sub>factor</sub> from the range 1.0 to 3.0. By testing diferent values for this volatility, this experiment tests how each algorithm handles larger fluctuations in the carbon intensity of consecutive time steps. In Figures 6(c), 7(c), and 8(c), we show that the observed competitive ratio of <sub>DTPR-min</sub> outperforms the benchmark algorithms for all noise factors in all regions. Intuitively, higher volatility values cause the online algorithms to perform worse in general. Averaging over all regions and noise factors, the competitive ratio achieved by <sub>DTPR-min</sub> is a 53.6% improvement on the carbon-agnostic method, a 13.5% improvement on the ??-min search algorithm, and a 14.3% improvement on the constant threshold algorithm.

By averaging over all experiments for a given region, we obtain the cumulative distribution function plot for each algorithm’s competitive ratio in Figures 6(d), 7(d), and 8(d). Compared to the carbon-agnostic, constant threshold, and ??-min search algorithms, <sub>DTPR-min</sub> achieves a lower average empirical competitive ratio distribution for all tested regions. Across <sub>all</sub> <sub>regions</sub> at the 95th percentile, <sub>DTPR-min</sub> achieves a worst-case empirical competitive ratio of 1.40. This represents a 48.2% improvement over the <sub>carbon-agnostic</sub> algorithm, and improvements of 15.6% and 14.4% over the ??-min search and constant threshold <sub>switching-cost-agnostic</sub> algorithms, respectively.

## 7 RELATED WORK

This paper contributes directly to three lines of work: (i) work on online search and related problems, e.g., ??-search, one-way trading, and online knapsack; (ii) work on online optimization problems with switching costs, e.g., metrical task systems and convex function chasing; and (iii) work on carbon-aware load shifting. We describe the relationship to each below.

<sub>Online</sub> <sub>Search.</sub> The <sub>OPR</sub> problem is related to the online ??-search problem [23, 29], as discussed in the introduction and Section 2.2. It also has several similar counterparts, including online conversion problems such as one-way trading [14, 15, 34, 44] and online knapsack problems [45, 50, 54], with practical applications to stock trading [29], cloud pricing [53], electric vehicle charging [46], etc. The ??-search problem can be viewed as an integral version of the online conversion problem, while the general online conversion problem allows continuous one-way trading. The basic online knapsack problem studies how to pack arriving items of diferent sizes and values into a knapsack with limited capacity, while its extensions to item departures [45, 53] and multidimensional capacity [50] have also been studied recently. Another line of research leverages ML predictions to design learningaugmented online algorithms for online ??-search [23] and online conversion [45]. However, to the best of our knowledge, none of these works consider the switching cost of changing decisions.

<sub>Metrical</sub> <sub>Task</sub> <sub>Systems.</sub> The metrical task systems (<sub>MTS</sub>) problem was introduced by Borodin et al. in [7]. Several decades of progress on upper and lower bounds on the competitive ratio of <sub>MTS</sub> recently culminated with a tight bound of $\Theta ( \log ^ { 2 } n )$ for the competitive ratio of <sub>MTS</sub> on an arbitrary ??-point metric space, with <sub>Θ(</sub>log ??<sub>)</sub> being possible on certain metric spaces such as trees [8, 9]. Several modified forms of <sub>MTS</sub> have also seen significant attention in the literature, such as smoothed online convex optimization (<sub>SOCO</sub>) and convex function chasing (<sub>CFC</sub>), in which the decision space is an n-dimensional normed vector space and cost functions are restricted to be convex [17, 25]. The best known upper and lower bounds on the competitive ratio of <sub>CFC</sub> are ?? <sub>(</sub>??<sub>)</sub> and <sub>Ω(</sub> ??<sub>)</sub>, respectively, in ??-dimensional Euclidean spaces [10, 39]. However, algorithms with competitive ratios independent of dimension can be obtained for certain special classes of functions, such as ??-polyhedral functions [11]. Several recent works have also investigated the design of learning-augmented algorithms for various cases of <sub>CFC</sub>/<sub>SOCO</sub> and <sub>MTS</sub> which exploit the performance of ML predictions of the optimal decisions [4, 12, 13, 24, 38]. The key characteristic distinguishing <sub>OPR</sub> from <sub>MTS</sub> is the presence of a deadline constraint. None of the algorithms for <sub>MTS</sub>-like problems are designed to handle long-term constraints while being competitive.

<sub>Carbon-Aware</sub> <sub>Temporal</sub> <sub>Workload</sub> <sub>Shifting.</sub> The goal of shifting workloads in time to allow more sustainable operations of data centers has been of interest for more than a decade, e.g., [19, 26– 28]. Traditionally, such papers have used models that build on one of convex function chasing, ??-search, or online knapsack to design algorithms; however such models do not capture both the switching costs and long-term deadlines that are crucial to practical deployment. In recent years, the load shifting literature has focused specifically on reducing the carbon footprint of operations, e.g., [1, 6, 36, 47]. Perhaps most related to this paper is [47], which explores the problem of carbon aware temporal workload shifting and proposes a threshold-based algorithm that suspends the job when the carbon intensity is higher than a threshold value and resumes it when it drops below the threshold. However, it does not consider switching nor does it provide any deadline guarantees. Other recent work on carbon-aware temporal shifting seeks to address the resultant increase in job completion times. In [42], authors leverage the pause and resume approach to reduce the carbon footprint of ML training and high-performance computing applications such as BLAST [16]. However, instead of resuming at normal speed (1<sub>×</sub>) during the low carbon intensity periods, their applications resume operation at a faster speed (??<sub>×</sub>), where the scale factor ?? depends on the application characteristics. It uses a threshold-based approach to determine the low carbon intensity periods but does not consider switching costs or provide any deadline guarantees. A future direction is to extend the <sub>DTPR</sub> algorithms to consider the ability to scale up speed after resuming jobs.

In addition to our direct contributions in the above fields, our work is adjacent to several existing studies which have considered switching costs and <sub>hysteretic</sub> <sub>control</sub> in queueing models for single servers, server farms, and clouds. In [22], an M/M/1 queueing system is presented where the decision maker chooses arrival and service rates at each epoch and incurs a switching cost to change the rates. In this regime, they show that the optimal policy is a hysteretic policy, which exhibits resistance to change due to the switching cost. Gandhi et al. [18] present an M/M/k queueing system for server farms with setup costs, where turning a server on incurs a time delay. Similarly, [33] presents and analyzes a nearly-optimal mechanism to control the performance and power consumption of a server farm, where the setup cost incurs time and energy. A few works have also considered similar problems with diferent assumptions, such as job arrivals distributed according to a stochastic fluid model [5], and modeling the control policy as a Markov decision process [51]. It is notable that nearly all of these works derive hysteretic control policies based on the queue length, which essentially use a double threshold technique to resist changing decisions as a function of switching cost, a similar flavor of the result as we present in our setting. However, <sub>OPR</sub> is foundationally diferent as compared to the above works since we consider a single workload, a single deadline, and costs are exogenous to the online decision; this results in an algorithm design and analysis technique that difer substantially from these queueing models.

## 8 CONCLUDING REMARKS

Motivated by carbon-aware load shifting, we introduce and study the online pause and resume problem (<sub>OPR</sub>), which bridges gaps between several online optimization problems. To our knowledge, it is the first online optimization problem that includes both long-term constraints and switching costs. Our main results provide optimal online algorithms for the minimization and maximization variants of this problem, as well as lower bounds for the competitive ratio of any deterministic online algorithm. Notably, our proposed algorithms match existing optimal results for the related ??-search problem when the switching cost is 0, and improve on the ??-min search competitive bounds for non-zero switching cost. The key to our results is a novel double threshold algorithm that we expect to be applicable in other online problems with switching costs.

There are a number of interesting directions in which to continue the study of <sub>OPR</sub>. We have highlighted the application of <sub>OPR</sub> to carbon-aware load shifting, but <sub>OPR</sub> also applies to many other problems where pricing changes over time and frequent switching is undesirable. Pursuing these applications is important. Theoretically, there are several interesting open questions. First, considering the target application of carbon-aware load shifting, some workloads are <sub>highly</sub> <sub>paral-</sub> <sub>lelizable</sub> [42], which adds another dimension of scaling to the problem (i.e., instead of choosing to run 1 unit of the job in each time slot, the online player must decide how many units to allocate at each time slot). Furthermore, considering <sub>heterogeneous</sub> <sub>switching</sub> <sub>costs</sub> would be a logical extension of the setting we have considered here, modeling, for example, switching models which act as a function of the time spent in the current state. Both of these make the theoretical problem more challenging, and are important considerations for future work. Additionally, very recent work has incorporated machine-learned advice to achieve better performance on related online problems, including ??-search [23, 44], <sub>CFC/SOCO</sub> [12, 24], and <sub>MTS</sub> [4, 13, 38]. Designing learning-augmented algorithms for <sub>OPR</sub> is a very promising line of future work, particularly considering applications such as carbon-aware load shifting, where accurate predictions can significantly improve the algorithm’s understanding of the future in the best case, without sacrificing worst-case guarantees.

## ACKNOWLEDGMENTS

We thank our shepherd Issac Grosof and the anonymous SIGMETRICS reviewers for their valuable insight and feedback.

This research is supported by National Science Foundation grants CAREER-2045641, CNS-2102963, CNS-2106299, CNS-2146814, CNS-1518941, CPS-2136197, CPS-2136199, NGSDI-2105494, NGSDI-2105648, 1908298, 2020888, 2021693, 2045641, 2213636, and 2211888.

This material is based upon work supported by the U.S. Department of Energy, Ofice of Science, Ofice of Advanced Scientific Computing Research, Department of Energy Computational Science Graduate Fellowship, and an NSF Graduate Research Fellowship (DGE-1745301).

## DISCLAIMERS

This report was prepared as an account of work sponsored by an agency of the United States Government. Neither the United States Government nor any agency thereof, nor any of their employees, makes any warranty, express or implied, or assumes any legal liability or responsibility for the accuracy, completeness, or usefulness of any information, apparatus, product, or process disclosed, or represents that its use would not infringe privately owned rights. Reference herein to any specific commercial product, process, or service by trade name, trademark, manufacturer, or otherwise does not necessarily constitute or imply its endorsement, recommendation, or favoring by the United States Government or any agency thereof. The views and opinions of authors expressed herein do not necessarily state or reflect those of the United States Government or any agency thereof.

## REFERENCES

[1] Bilge Acun, Benjamin Lee, Fiodar Kazhamiaka, Kiwan Maeng, Udit Gupta, Manoj Chakkaravarthy, David Brooks and Carole-Jean Wu. 2023. Carbon Explorer: A Holistic Framework for Designing Carbon Aware Datacenters. In Proceedings of the 28th ACM International Conference on Architectural Support for Programming Languages and Operating <sub>Systems,</sub> <sub>Volume</sub> <sub>2</sub> (Vancouver, BC, Canada) <sub>(ASPLOS</sub> <sub>2023)</sub>. Association for Computing Machinery, New York, NY, USA, 118–132. https://doi.org/10.1145/3575693.3575754

[2] Pradeep Ambati, Noman Bashir, David Irwin, Mohammad Hajiesmaili, and Prashant Shenoy. 2020. Hedge Your Bets: Optimizing Long-term Cloud Costs by Mixing VM Purchasing Options. In <sub>2020</sub> <sub>IEEE</sub> <sub>International</sub> <sub>Conference</sub> <sub>on</sub> <sub>Cloud</sub> <sub>Engineering</sub> <sub>(IC2E)</sub>. 105–115. https://doi.org/10.1109/IC2E48712.2020.00018

[3] Spyros Angelopoulos, Christoph Dürr, Shendan Jin, Shahin Kamali, and Marc Renault. 2022. Online Computation with Untrusted Advice. arXiv:1905.05655 [cs.DS]

[4] Antonios Antoniadis, Christian Coester, Marek Elias, Adam Polak, and Bertrand Simon. 2020. Online Metric Algorithms <sup>with</sup> <sup>Untrusted</sup> <sup>Predictions.</sup> <sup>In</sup> Proceedings of the 37th International Conference on Machine Learning<sup>.</sup> <sup>PMLR,</sup> <sup>345–355.</sup>

[5] Naser M. Asghari, M. Mandjes, and Anwar Walid. 2014. Energy-eficient scheduling in multi-core servers. <sub>Computer</sub> <sub>Networks</sub> 59 (2014), 33–43. https://doi.org/10.1016/j.bjp.2013.12.009

[6] Noman Bashir, Tian Guo, Mohammad Hajiesmaili, David Irwin, Prashant Shenoy, Ramesh Sitaraman, Abel Souza, and Adam Wierman. 2021. Enabling Sustainable Clouds: The Case for Virtualizing the Energy System. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>ACM</sub> <sub>Symposium</sub> <sub>on</sub> <sub>Cloud</sub> <sub>Computing</sub> (Seattle, WA, USA) <sub>(SoCC</sub> <sub>’21)</sub>. Association for Computing Machinery, New York, NY, USA, 350–358. https://doi.org/10.1145/3472883.3487009

[7] Allan Borodin, Nathan Linial, and Michael E. Saks. 1992. An Optimal On-Line Algorithm for Metrical Task System. <sub>J.</sub> <sub>ACM</sub> 39, 4 (Oct 1992), 745–763. https://doi.org/10.1145/146585.146588

[8] Sébastien Bubeck, Christian Coester, and Yuval Rabani. 2023. The Randomized \$k\$-Server Conjecture Is False!. In Proceedings of the 55th Annual ACM Symposium on Theory of Computing (STOC 2023) <sup>(Orlando,</sup> <sup>FL,</sup> <sup>USA)</sup> (STOC 2023)<sup>.</sup> Association for Computing Machinery, New York, NY, USA, 581–594. https://doi.org/10.1145/3564246.3585132

[9] Sébastien Bubeck, Michael B. Cohen, James R. Lee, and Yin Tat Lee. 2021. Metrical Task Systems on Trees via Mirror Descent and Unfair Gluing. <sub>SIAM</sub> <sub>J.</sub> <sub>Comput.</sub> 50, 3 (Jan. 2021), 909–923. https://doi.org/10.1137/19M1237879

[10] Sébastien Bubeck, Bo’az Klartag, Yin Tat Lee, Yuanzhi Li, and Mark Sellke. 2019. Chasing Nested Convex Bodies Nearly <sup>Optimally.</sup> <sup>In</sup> Proceedings of the 2020 ACM-SIAM Symposium on Discrete Algorithms (SODA)<sup>.</sup> <sup>Society</sup> <sup>for</sup> <sup>Industrial</sup> <sup>and</sup> Applied Mathematics, 1496–1508. https://doi.org/10.1137/1.9781611975994.91

[11] NiangJun Chen, Gautam Goel, and Adam Wierman. 2018. Smoothed Online Convex Optimization in High Dimensions <sup>via</sup> <sup>Online</sup> <sup>Balanced</sup> <sup>Descent.</sup> <sup>In</sup> Proceedings of the 31st Conference On Learning Theory<sup>.</sup> <sup>PMLR,</sup> <sup>1574–1594.</sup>

[12] Nicolas Christianson, Tinashe Handina, and Adam Wierman. 2022. Chasing Convex Bodies and Functions with <sup>Black-Box</sup> <sup>Advice.</sup> <sup>In</sup> Proceedings of the 35th Conference on Learning Theory<sup>,</sup> <sup>Vol.</sup> <sup>178.</sup> <sup>PMLR,</sup> <sup>867–908.</sup>

[13] Nicolas Christianson, Junxuan Shen, and Adam Wierman. 2023. Optimal robustness-consistency tradeofs for learning-<sup>augmented</sup> <sup>metrical</sup> <sup>task</sup> <sup>systems.</sup> <sup>In</sup> International Conference on Artificial Intelligence and Statistics<sup>.</sup>

[14] Peter Damaschke, Phuong Hoai Ha, and Philippas Tsigas. 2007. Online Search with Time-Varying Price Bounds. <sub>Algorithmica</sub> 55, 4 (Dec. 2007), 619–642. https://doi.org/10.1007/s00453-007-9156-9

[15] R. El-Yaniv, A. Fiat, R. M. Karp, and G. Turpin. 2001. Optimal Search and One-Way Trading Online Algorithms. <sub>Algorithmica</sub> 30, 1 (May 2001), 101–139. https://doi.org/10.1007/s00453-001-0003-0

[16] National Center for Biotechnology Information. 2022. Basic Local Alignment Search Tool (BLAST). https://blast.ncbi. nlm.nih.gov.

[17] Joel Friedman and Nathan Linial. 1993. On convex body chasing. <sub>Discrete</sub> <sub>&</sub> <sub>Computational</sub> <sub>Geometry</sub> 9, 3 (March 1993), 293–321. https://doi.org/10.1007/bf02189324

[18] Anshul Gandhi, Mor Harchol-Balter, and Ivo Adan. 2010. Server farms with setup costs. <sub>Performance</sub> <sub>Evaluation</sub> 67, 11 (2010), 1123–1138. https://doi.org/10.1016/j.peva.2010.07.004 Performance 2010.

[19] Vani Gupta, Prashant Shenoy, and Ramesh K Sitaraman. 2019. Combining renewable solar and open air cooling for <sup>greening</sup> <sup>internet-scale</sup> <sup>distributed</sup> <sup>networks.</sup> <sup>In</sup> Proceedings of the Tenth ACM International Conference on Future Energy <sub>Systems</sub>. 303–314.

[20] Walid A. Hanafy, Roozbeh Bostandoost, Noman Bashir, David Irwin, Mohammad Hajiesmaili, and Prashant Shenoy. 2023. The War of the Eficiencies: Understanding the Tension between Carbon and Energy Optimization. In <sub>Proceedings</sub> of the 2nd Workshop on Sustainable Computer Systems. ACM. https://doi.org/10.1145/3604930.3605709

[21] Abdolhossein Hoorfar and Mehdi Hassani. 2008. Inequalities on the Lambert W function and hyperpower function. Journal of Inequalities in Pure and Applied Mathematics <sup>9,</sup> <sup>51</sup> <sup>(Jan.</sup> <sup>2008).</sup> <sup>Issue</sup> <sup>2.</sup>

[22] M. Yu. Kitaev and Richard F. Serfozo. 1999. M/M/1 Queues with Switching Costs and Hysteretic Optimal Control. <sub>Operations</sub> <sub>Research</sub> 47, 2 (1999), 310–312. https://doi.org/10.1287/opre.47.2.310

[23] Russell Lee, Bo Sun, John C. S. Lui, and Mohammad Hajiesmaili. 2022. Pareto-Optimal Learning-Augmented Algorithms for Online k-Search Problems. arXiv:2211.06567 https://arxiv.org/abs/2211.06567

[24] Pengfei Li, Jianyi Yang, and Shaolei Ren. 2022. Expert-Calibrated Learning for Online Optimization with Switching <sup>Costs.</sup> Proceedings of the ACM on Measurement and Analysis of Computing Systems <sup>6,</sup> <sup>2</sup> <sup>(May</sup> <sup>2022),</sup> <sup>1–35. https:</sup> //doi.org/10.1145/3530894

[25] Minghong Lin, Zhenhua Liu, Adam Wierman, and Lachlan L. H. Andrew. 2012. Online algorithms for geographical load balancing. In <sub>2012</sub> <sub>International</sub> <sub>Green</sub> <sub>Computing</sub> <sub>Conference</sub> <sub>(IGCC)</sub>. IEEE. https://doi.org/10.1109/igcc.2012.6322266

[26] Minghong Lin, Adam Wierman, Lachlan LH Andrew, and Eno Thereska. 2012. Dynamic right-sizing for power proportional data centers. <sub>IEEE/ACM</sub> <sub>Transactions</sub> <sub>on</sub> <sub>Networking</sub> 21, 5 (2012), 1378–1391.

[27] Zhenhua Liu, Yuan Chen, Cullen Bash, Adam Wierman, Daniel Gmach, Zhikui Wang, Manish Marwah, and Chris Hyser. 2012. Renewable and cooling aware workload management for sustainable data centers. In <sub>Proceedings</sub> <sub>of</sub> the 12th ACM SIGMETRICS/PERFORMANCE joint international conference on Measurement and Modeling of Computer <sub>Systems</sub>. 175–186.

[28] Zhenhua Liu, Minghong Lin, Adam Wierman, Steven H Low, and Lachlan LH Andrew. 2011. Greening geographical <sup>load</sup> <sup>balancing.</sup> ACM SIGMETRICS Performance Evaluation Review <sup>39,</sup> <sup>1</sup> <sup>(2011),</sup> <sup>193–204.</sup>

[29] Julian Lorenz, Konstantinos Panagiotou, and Angelika Steger. 2008. Optimal Algorithms for k-Search with Application in Option Pricing. <sub>Algorithmica</sub> 55, 2 (Aug. 2008), 311–328. https://doi.org/10.1007/s00453-008-9217-8

[30] Thodoris Lykouris and Sergei Vassilvtiskii. 2018. Competitive Caching with Machine Learned Advice. In <sub>Proceedings</sub> of the 35th International Conference on Machine Learning (Proceedings of Machine Learning Research, Vol. 80)<sup>,</sup> <sup>Jennifer</sup> Dy and Andreas Krause (Eds.). PMLR, 3296–3305. https://proceedings.mlr.press/v80/lykouris18a.html

[31] Diptyaroop Maji, Ramesh K. Sitaraman, and Prashant Shenoy. 2022. DACF: Day-Ahead Carbon Intensity Forecasting <sup>of</sup> <sup>Power</sup> <sup>Grids</sup> <sup>Using</sup> <sup>Machine</sup> <sup>Learning.</sup> <sup>In</sup> Proceedings of the Thirteenth ACM International Conference on Future <sub>Energy</sub> <sub>Systems</sub> (Virtual Event) <sub>(e-Energy</sub> <sub>’22)</sub>. Association for Computing Machinery, New York, NY, USA, 188–192. https://doi.org/10.1145/3538637.3538849

[32] Electricity Maps. 2020. Electricity Map. https://www.electricitymap.org/map.

[33] Isi Mitrani. 2011. Managing performance and power consumption in a server farm. <sub>Annals</sub> <sub>of</sub> <sub>Operations</sub> <sub>Research</sub> 202, 1 (July 2011), 121–134. https://doi.org/10.1007/s10479-011-0932-1

[34] Esther Mohr, Iftikhar Ahmad, and Günter Schmidt. 2014. Online algorithms for conversion problems: a survey. <sub>Surveys</sub> in Operations Research and Management Science <sup>19,</sup> <sup>2</sup> <sup>(2014),</sup> <sup>87–104</sup>

[35] Manish Purohit, Zoya Svitkina, and Ravi Kumar. 2018. Improving Online Algorithms via ML Predictions. In <sub>Advances</sub> <sub>in</sub> <sub>Neural</sub> <sub>Information</sub> <sub>Processing</sub> <sub>Systems</sub>, S. Bengio, H. Wallach, H. Larochelle, K. Grauman, N. Cesa-Bianchi, and R. Garnett (Eds.), Vol. 31. Curran Associates, Inc.

[36] Ana Radovanovic, Ross Koningstein, Ian Schneider, Bokan Chen, Alexandre Duarte, Binz Roy, Diyue Xiao, Maya Haridasan, Patrick Hung, Nick Care, et al. 2022. Carbon-Aware Computing for Datacenters. <sub>IEEE</sub> <sub>Transactions</sub> <sub>on</sub> Power Systems <sup>(2022).</sup>

[37] Samyam Rajbhandari, Olatunji Ruwase, Jef Rasley, Shaden Smith, and Yuxiong He. 2021. ZeRO-Infinity: Breaking the GPU Memory Wall for Extreme Scale Deep Learning. In Proceedings of the International Conference for High Performance Computing, Networking, Storage and Analysis<sup>.</sup> <sup>1–14.</sup>

[38] Daan Rutten, Nicolas Christianson, Debankur Mukherjee, and Adam Wierman. 2022. Smoothed Online Optimization with Unreliable Predictions. https://doi.org/10.48550/arXiv.2202.0351

[39] Mark Sellke. 2020. Chasing Convex Bodies Optimally. In Proceedings of the Thirty-First Annual ACM-SIAM Symposium <sub>on</sub> <sub>Discrete</sub> <sub>Algorithms</sub> <sub>(SODA</sub> <sub>’20)</sub>. Society for Industrial and Applied Mathematics, USA, 1509–1518.

[40] Supreeth Shastri, Amr Rizk, and David Irwin. 2016. Transient guarantees: Maximizing the value of idle cloud capacity. <sup>In</sup> SC’16: Proceedings of the International Conference for High Performance Computing, Networking, Storage and Analysis<sup>.</sup> IEEE, 992–1002.

[41] Shaden Smith, Mostofa Patwary, Brandon Norick, Patrick LeGresley, Samyam Rajbhandari, Jared Casper, Zhun Liu, Shrimai Prabhumoye, George Zerveas, Vijay Korthikanti, et al. 2022. Using DeepSpeed and Megatron to Train Megatron-Turing NLG 530B, a Large-Scale Generative Language Model. <sub>arXiv</sub> <sub>preprint</sub> <sub>arXiv:2201.11990</sub> (2022).

[42] Abel Souza, Noman Bashir, Jorge Murillo, Walid Hanafy, Qianlin Liang, David Irwin, and Prashant Shenoy. 2023. Ecovisor: A Virtual Energy System for Carbon-Eficient Applications. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>28th</sub> <sub>ACM</sub> <sub>International</sub> Conference on Architectural Support for Programming Languages and Operating Systems, Volume 2 <sup>(Vancouver,</sup> <sup>BC,</sup> Canada) <sub>(ASPLOS</sub> <sub>2023)</sub>. Association for Computing Machinery, New York, NY, USA, 252–265. https://doi.org/10.1145 3575693.3575709

[43] Seán M. Stewart. 2009. On Certain Inequalities Involving the Lambert W function. <sub>Journal</sub> <sub>of</sub> <sub>Inequalities</sub> <sub>in</sub> <sub>Pure</sub> <sub>and</sub> <sub>Applied</sub> <sub>Mathematics</sub> 10, 96 (Nov. 2009). Issue 4.

[44] Bo Sun, Russell Lee, Mohammad Hajiesmaili, Adam Wierman, and Danny Tsang. 2021. Pareto-Optimal Learning Augmented Algorithms for Online Conversion Problems. In <sub>Advances</sub> <sub>in</sub> <sub>Neural</sub> <sub>Information</sub> <sub>Processing</sub> <sub>Systems</sub>, M. Ranzato, A. Beygelzimer, Y. Dauphin, P.S. Liang, and J. Wortman Vaughan (Eds.), Vol. 34. Curran Associates, Inc., 10339–10350. https://proceedings.neurips.cc/paper\_files/paper/2021/file/55a988dfb00a914717b3000a3374694c-Paper.pdf

[45] Bo Sun, Lin Yang, Mohammad Hajiesmaili, Adam Wierman, John CS Lui, Don Towsley, and Danny HK Tsang. 2022. <sup>The</sup> <sup>Online</sup> <sup>Knapsack</sup> <sup>Problem</sup> <sup>with</sup> <sup>Departures.</sup> Proceedings of the ACM on Measurement and Analysis of Computing <sub>Systems</sub> 6, 3 (2022), 1–32.

[46] Bo Sun, Ali Zeynali, Tongxin Li, Mohammad Hajiesmaili, Adam Wierman, and Danny HK Tsang. 2020. Competitive Algorithms for the Online Multiple Knapsack Problem With Application to Electric Vehicle Charging. <sub>Proceedings</sub> <sub>of</sub> the ACM on Measurement and Analysis of Computing Systems <sup>4,</sup> <sup>3</sup> <sup>(2020),</sup> <sup>1–32.</sup>

[47] Philipp Wiesner, Ilja Behnke, Dominik Scheinert, Kordian Gontarska, and Lauritz Thamsen. 2021. Let’s Wait AWhile: How Temporal Workload Shifting Can Reduce Carbon Emissions in the Cloud. In <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>22nd</sub> <sub>International</sub> <sub>Middleware</sub> <sub>Conference</sub>. Association for Computing Machinery, New York, NY, USA, 260–272.

[48] Guangxuan Xiao, Ji Lin, Mickael Seznec, Hao Wu, Julien Demouth, and Song Han. 2023. SmoothQuant: Accurate and Eficient Post-Training Quantization for Large Language Models. In <sub>International</sub> <sub>Conference</sub> <sub>on</sub> <sub>Machine</sub> <sub>Learning</sub> <sub>(Proceedings</sub> <sub>of</sub> <sub>Machine</sub> <sub>Learning</sub> <sub>Research)</sub>. PMLR, PMLR, Honolulu, HI, USA, 38087–38099.

[49] Lin Yang, Mohammad H. Hajiesmaili, Ramesh Sitaraman, Adam Wierman, Enrique Mallada, and Wing S. Wong. 2020. Online Linear Optimization with Inventory Management Constraints. <sub>Proc.</sub> <sub>ACM</sub> <sub>Meas.</sub> <sub>Anal.</sub> <sub>Comput.</sub> <sub>Syst.</sub> 4, 1, Article 16 (may 2020), 29 pages. https://doi.org/10.1145/3379482

[50] Lin Yang, Ali Zeynali, Mohammad H. Hajiesmaili, Ramesh K. Sitaraman, and Don Towsley. 2021. Competitive Algorithms for Online Multidimensional Knapsack Problems. <sub>Proceedings</sub> <sub>of</sub> <sub>the</sub> <sub>ACM</sub> <sub>on</sub> <sub>Measurement</sub> <sub>and</sub> <sub>Analysis</sub> <sub>of</sub> <sub>Computing</sub> <sub>Systems</sub> 5, 3, Article 30 (Dec 2021), 30 pages.

[51] Zexi Yang, Meng-Hsi Chen, Zhisheng Niu, and Dawei Huang. 2011. An Optimal Hysteretic Control Policy for <sup>Energy</sup> <sup>Saving</sup> <sup>in</sup> <sup>Cloud</sup> <sup>Computing.</sup> <sup>In</sup> 2011 IEEE Global Telecommunications Conference - GLOBECOM 2011<sup>.</sup> <sup>1–5.</sup> https://doi.org/10.1109/GLOCOM.2011.6133628

[52] Sheng Shui Zhang. 2006. The efect of the charging protocol on the cycle life of a Li-ion battery. <sub>Journal</sub> <sub>of</sub> <sub>Power</sub> <sub>Sources</sub> 161, 2 (2006), 1385–1391. https://doi.org/10.1016/j.jpowsour.2006.06.040

[53] ZiJun Zhang, Zongpeng Li, and Chuan Wu. 2017. Optimal posted prices for online cloud resource allocation. <sub>Proceedings</sub> of the ACM on Measurement and Analysis of Computing Systems <sup>1,</sup> <sup>1</sup> <sup>(2017),</sup> <sup>1–26.</sup>

[54] Yunhong Zhou, Deeparnab Chakrabarty, and Rajan Lukose. 2008. Budget Constrained Bidding in Keyword Auctions and Online Knapsack Problems. In <sub>Lecture</sub> <sub>Notes</sub> <sub>in</sub> <sub>Computer</sub> <sub>Science</sub>. Springer Berlin Heidelberg, Heidelberg, DE 566–576. https://doi.org/10.1007/978-3-540-92185-1\_63

![](lechowicz2023_online_pause_resume_assets/images/760cbd2c04a3674b597d3d0ac03cfcf1cc153eda1af1b5973c7e64e747c6a033.jpg)

```txt
Carbon intensity values over time, New Zealand carbon trace
carbon intensity (in gCO2eq/kWh)
date and time
T = 48
T = 72
T = 96
```

Carbon intensity values over time, U.S. Pacific NW carbon trace  
![](lechowicz2023_online_pause_resume_assets/images/dd71886626e2de76b9605a4a92feef5a4f3ec0f423de0ad8001482047b4b877c.jpg)  
Fig. 9. Carbon intensity (in $\mathsf { g C O _ { 2 } e q / k W h } )$ values ploted for each region tested in our numerical experiments, with one-hour granularity. We plot a representative random interval of <sup>96</sup> hours, with vertical lines demarcating the diferent values for $T$ (time horizon) tested in our experiments. In all regions, carbon values roughly follow a diurnal (daily cycle) patern. Actual values and observed intensities significantly vary in diferent regions.

## A CASE STUDY RESULTS FOR DTPR-MAX ALGORITHM

<div class="mineru-algorithm" style="white-space: pre-wrap; font-family:monospace;">
Algorithm 2 Double Threshold Pause and Resume for OPR-max (DTPR-max)

Input: threshold values  $\{u_{i}\}_{i\in[1,k]}$  and  $\{\ell_{i}\}_{i\in[1,k]}$  defined in Equation (6), deadline T

Output: online decisions  $\{x_{t}\}_{t\in[1,T]}$ 

1: initialize: i = 1;

2: while price  $c_{t}$  arrives and  $i \leq k$  do

3: if  $(k - i) \geq (T - t)$  then ▷ close to the deadline T, we must accept remaining prices

4: price  $c_{t}$  is accepted, set  $x_{t} = 1$ 

5: else if  $x_{t-1} = 0$  then ▷ If previous price was not accepted

6: if  $c_{t} \geq u_{i}$  then price  $c_{t}$  is accepted, set  $x_{t} = 1$ 

7: else price  $c_{t}$  is rejected, set  $x_{t} = 0$ 

8: else if  $x_{t-1} = 1$  then ▷ If previous price was accepted

9: if  $c_{t} \geq \ell_{i}$  then price  $c_{t}$  is accepted, set  $x_{t} = 1$ 

10: else price  $c_{t}$  is rejected, set  $x_{t} = 0$ 

11: update  $i = i + x_{t}$
</div>

This section presents and discusses the deferred experimental results for the <sub>DTPR-max</sub> algorithm (pseudocode summarized in Algorithm 2) in the carbon-aware temporal workload shifting case study. We evaluate <sub>DTPR-max</sub> against the same benchmark algorithms described in Section 6.1.

<sup>For</sup> <sup>the</sup> maximization metric<sup>,</sup> <sup>we</sup> <sup>consider</sup> <sup>the</sup> percentage $o f$ carbon-free electricity <sup>powering</sup> <sup>the</sup> grid. At each time step ??, the electricity supply has a carbon-free percentage $c _ { t } , \mathrm { i . e . }$ , if the job is being processed during time slot $t \left( x _ { t } = 1 \right)$ , the electricity powering the data center’s is $c _ { t } \%$ <sub>carbon-free</sub>, and the objective is to maximize this percentage over all ?? slots of the active running of the workload.

![](lechowicz2023_online_pause_resume_assets/images/711f6cf6f7b37dbf1c3166c3c171ae71bacac667427c01056be20b4c0f394bf9.jpg)  
(a) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/231192597d88d6a8b9a8c1e11ea3d2758005d0d3e34147cc9e6b645cf33ead8e.jpg)  
(b) Changing $\beta$

![](lechowicz2023_online_pause_resume_assets/images/e02f492400d6deccb9e8482cc35d5acc35022eabae07b0688bbbad346cf71114.jpg)  
(c) Changing volatility

![](lechowicz2023_online_pause_resume_assets/images/48b8da50428f3de0068a598605367d4f3c9584afb746f654f3d9607cc0623d2d.jpg)  
(d) CDF  
Fig. 10. Maximization experiments on Ontario, Canada carbon trace, with $\theta \approx 1 . 5 1$ and $T = 4 8 .$ (a): Changing job length <sup>??</sup> w.r.t. time horizon <sup>??</sup> (<sup>??</sup>-axis), vs. competitive ratio (b): Changing switching cost $\beta$ w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (c): Diferent volatility levels w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (d): Cumulative distribution function of competitive ratios

In these maximization experiments, the switching-cost-agnostic ??-max-search algorithm chooses to run the ??th hour of the job during the first time slot where the carbon-free supply is at least $\Phi _ { i } .$ Similarly, the constant threshold algorithm chooses to run the job whenever the carbon-free supply is at least ?? ??. We set ?? and ?? to be the minimum and maximum carbon-free supply percentages over the entire trace being studied.

As in Section 6.2, our focus is on the competitive ratio (lower competitive ratio is better). We report the performance of all algorithms for each experiment setting, in each tested region.

In the first experiment, we test all algorithms for diferent job lengths ?? in the range from 4 hours to $T / 2 ( 2 4 )$ . The switching cost $\beta$ is non-zero and fixed, and no volatility is added to the carbon trace. By testing diferent values for $k ,$ this experiment tests diferent ratios between the workload length and the slack provided to the algorithm. In Figures 10(a), 11(a), and $1 2 ( \mathrm { a } )$ , we show that the observed average competitive ratio of <sub>DTPR-max</sub> narrowly outperforms the benchmark algorithms for all values of ?? in all regions, and it compares particularly favorably for <sub>short</sub> job lengths. Averaging over all regions and job lengths, the competitive ratio achieved by <sub>DTPR-max</sub> is a 4.9% improvement on the carbon-agnostic method, a 8.4% improvement on the ??-max search algorithm, and a 2.1% improvement on the constant threshold algorithm.

In the second experiment, we test all algorithms for diferent switching costs $\beta$ in the range from 0 to $U / 5 .$ . The job length ?? is set to 10 hours, and no volatility is added to the carbon trace. By testing diferent values for $\beta ,$ this experiment tests how an increasing switching cost impacts the performance of <sub>DTPR-max</sub> with respect to other algorithms which do not explicitly consider the switching cost. In Figures 10(b), 11(b), and 12(b), we show that the average competitive ratio of <sub>DTPR-max</sub> notably outperforms the other algorithms for a wide range of $\dot { \boldsymbol { { \beta } } }$ values in all regions. Unsurprisingly, the carbon-agnostic technique (which only incurs a switching cost of $2 \beta )$ is more competitive as $\beta$ grows. The ??-max search algorithm performs noticeably worse as $\beta$ grows. While the constant threshold algorithm has relatively consistent performance, the ??-max search algorithm performs noticeably worse as $\beta$ grows. Averaging over all regions and switching cost values, the competitive ratio achieved by <sub>DTPR-max</sub> is a 2.5% improvement on the carbon-agnostic method, a 6.4% improvement on the ??-max search algorithm, and a 0.1% improvement on the constant threshold algorithm.

In the final experiment, we test all algorithms on sequences with diferent volatility. The job length ?? and switching cost $\beta$ are both fixed. We add volatility by setting a <sub>noise</sub> <sub>factor</sub> from the range 1.0 to 3.0. By testing diferent values for this volatility, this experiment tests how each algorithm handles larger fluctuations in the carbon intensity of consecutive time steps. In Figures 10(c), 11(c), and 12(c), we show that the observed average competitive ratio of <sub>DTPR-max</sub> outperforms the other algorithms for most noise factors in all regions, with a slight degradation in the Pacific Northwest region. Intuitively, higher volatility values cause the online algorithms to perform worse in general. Averaging over all regions and noise factors, the competitive ratio achieved by <sub>DTPR-max</sub> is a 13.0% improvement on the carbon-agnostic method, a 11.2% improvement on the ??-max search algorithm, and a 2.1% improvement on the constant threshold algorithm.

![](lechowicz2023_online_pause_resume_assets/images/8cc187b9487fa32b82eeddf80418aa60737eb02b1f76baa385c9f9e7434ca557.jpg)  
(a) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/ae3d9fd9e79351bf719fe6fe6a70fecb2697a5a58bd410cbaa2aa361cbd77502.jpg)  
(b) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/64a4a9d336b8f58c7b63db49edb1b5b79e16158f6f5f85464244ec10a99b1f1b.jpg)  
(c) Changing volatility

![](lechowicz2023_online_pause_resume_assets/images/13a4a2fb1087f0f83be5f1b135108a672f1bce23f63bd3fc6d3e8c14e08e0768.jpg)  
(d) CDF

Fig. 11. Maximization experiments on U.S. Pacific Northwest carbon trace, with $\theta \approx 5 . 2 4$ and $T = 4 8 .$ (a): Changing job length <sup>??</sup> w.r.t. time horizon <sup>??</sup> (<sup>??</sup>-axis), vs. competitive ratio (b): Changing switching cost <sup>??</sup> w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (c): Diferent volatility levels w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (d): Cumulative distribution function of competitive ratios  
![](lechowicz2023_online_pause_resume_assets/images/59c8eeea603873112809f85c57b077a4598b0597339c40fd726f33d49da0f82b.jpg)  
(a) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/c732fcad03cb49059ae8d95adc66100afde4a09fa779674cc03c192bbac75a65.jpg)  
(b) Changing <sup>??</sup>

![](lechowicz2023_online_pause_resume_assets/images/a196ce80b1637cafa2d127e1300c37a4704e7c7aa91cb53d3e166fd3428076ee.jpg)  
(c) Changing volatility

![](lechowicz2023_online_pause_resume_assets/images/e3d9fb7aa7b4d901f22bf35dd073c63b40d4d6f182d0ff36b1e0c2be7f00e5f0.jpg)  
(d) CDF  
Fig. 12. Maximization experiments on New Zealand carbon trace, with <sup>??</sup> ≈ <sup>1.35</sup> and <sup>??</sup> = <sup>48</sup>.  
(a): Changing job length <sup>??</sup> w.r.t. time horizon <sup>??</sup> (<sup>??</sup>-axis), vs. competitive ratio (b): Changing switching cost <sup>??</sup> w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (c): Diferent volatility levels w.r.t. <sup>??</sup> (<sup>??</sup> -axis), vs. competitive ratio (d): Cumulative distribution function of competitive ratios

By averaging over all experiments for a given region, we obtain the cumulative distribution function plot for each algorithm’s competitive ratio in Figures 10(d), 11(d), and 12(d). Compared to the carbon-agnostic, constant threshold, and ??-max search algorithms, <sub>DTPR-max</sub> generally exhibits a lower average empirical competitive ratio over the tested regions. Notably, all of the algorithms are nearly 1-competitive in our experiments. Compared to our minimization experiments, <sub>DTPR-max</sub> outperforms the baseline algorithms by a smaller margin. Across <sub>all</sub> <sub>regions</sub> at the 95th percentile, <sub>DTPR-max</sub> achieves a worst-case empirical competitive ratio of 1.08. This represents a 16.1% improvement over the <sub>carbon-agnostic</sub> algorithm, and improvements of 11.4% and 2.19% over the ??-max search and constant threshold <sub>switching-cost-agnostic</sub> algorithms, respectively.

We conjecture that one dynamic contributing to this is the relatively low values of ?? observed for the carbon-free supply percentage in these real-world carbon traces.

## B COMPETITIVE ANALYSIS OF DTPR-MAX: PROOF OF THEOREM 5

Here we prove the <sub>DTPR-max</sub> results presented in Theorem 5 and Corollary 7.

Proof of Theorem 5. For $0 \leq j \leq k _ { \mathrm { { i } } }$ , let $S _ { j } \subseteq S$ be the sets of <sub>OPR-max</sub> price sequences for which <sub>DTPR-max</sub> accepts exactly ?? prices (excluding the $k - j$ prices it is forced to accept at the end of the sequence). Then all of the possible price sequences for <sub>OPR-max</sub> are represented by $\textstyle S = \bigcup _ { j = 0 } ^ { k } S _ { j }$ By definition, $u _ { k + 1 } = U . \operatorname { L e t } \epsilon > 0$ be fixed, and define the following two price sequences $\sigma _ { j }$ and $\rho _ { j } . \mathbf { \dot { \mathbf { \cdot } } }$

$$
\begin{array}{c} \forall 0 \leq j \leq k: \sigma_ {j} = u _ {1}, \ell_ {2}, \ldots , \ell_ {j}, L, \underbrace {u _ {j + 1} - \epsilon , \ldots , u _ {j + 1} - \epsilon} _ {k}, \underbrace {L , L , \ldots , L} _ {k}. \\ \forall 0 \leq j \leq k: \rho_ {j} = u _ {1}, L, u _ {2}, L, \ldots , L, u _ {j}, L, \underbrace {u _ {j + 1} - \epsilon , \ldots , u _ {j + 1} - \epsilon} _ {k}, \underbrace {L , L , \ldots , L} _ {k}. \end{array}
$$

We have two special cases for $j = 0$ and $j = 1$ . For $j = 0 _ { \mathrm { { i } } }$ , we have that $\sigma _ { 0 } = \rho _ { 0 }$ , and this sequence simply consists of $u _ { 1 } - \epsilon$ repeated ?? times, followed by ?? repeated ?? times. For $j = 1$ , we also have that $\sigma _ { 1 } = \rho _ { 1 }$ , and this sequence consists of one price with value $u _ { 1 }$ and one price with value $L ,$ followed by $u _ { 2 } - \epsilon$ repeated ?? times and ?? repeated ?? times.

Observe that as $\epsilon  0 , \sigma _ { j }$ and $\rho _ { j }$ are sequences yielding the worst-case ratios in $S _ { j }$ <sup>,</sup> <sup>as</sup> DTPR-max <sup>is</sup> forced to accept $( k - j )$ worst-case ?? values at the end of the sequence, and each accepted value is exactly equal to the corresponding threshold.

$\sigma _ { j }$ and $\rho _ { j }$ also represent two extreme possibilities for the switching cost. In $\sigma _ { j }$ <sup>,</sup> DTPR-max <sup>only</sup> switches twice, but it mostly accepts values $\ell _ { i } .$ . In $\rho _ { j }$ , <sub>DTPR-max</sub> must switch $j + 1$ times because there are many intermediate ?? values, but it only accepts values which are at least $u _ { i }$ .

Observe that ${ 0 \mathsf { P T } } ( \sigma _ { j } ) / { \mathsf { D T P R } } { - } { \mathsf { m a x } } ( \sigma _ { j } ) = { 0 \mathsf { P T } } ( \rho _ { j } ) / { \mathsf { D } } { \mathsf { T P R } } { - } { \mathsf { m a x } } ( \rho _ { j } )$ . First, the optimal solution for both sequences is exactly the same: $k c _ { \mathrm { m a x } } ( \sigma _ { j } ) - 2 \beta = k c _ { \mathrm { m a x } } ( \rho _ { j } ) - 2 \beta$ For any sequence ?? in $S _ { j }$ , we also know that $c _ { \operatorname* { m a x } } ( s ) < u _ { j + 1 } , s _ { 0 } 0 \mathsf { P T } ( \rho _ { j } ) = 0 \mathsf { P T } ( \sigma _ { j } ) \le k u _ { j + 1 } - 2 \beta .$

By definition of the threshold families $\{ u _ { i } \} _ { i \in [ 1 , k ] }$ and $\{ \ell _ { i } \} _ { i \in [ 1 , k ] }$ , we know that $\textstyle \sum _ { i = 1 } ^ { j } u _ { i } - j 2 \beta = \sum _ { i = 1 } ^ { j } \ell _ { i }$ for any value $j \geq 2 \colon$

$$
\mathrm{DTPR} - \max (\rho_ {j}) = \left(u _ {1} + \sum_ {i = 2} ^ {j} \ell_ {i} + (k - j) L - 4 \beta\right) = \left(\sum_ {i = 1} ^ {j} u _ {i} + (k - j) L - (j + 1) 2 \beta\right) = \mathrm{DTPR} - \max (\sigma_ {j}).
$$

Note that whenever $j < 2 ,$ , we have that $\sigma _ { 0 } = \rho _ { 0 }$ , and $\sigma _ { 1 } = \rho _ { 1 }$ . Thus, ${ \mathsf { D T P R - m i n } } ( \rho _ { j } ) = { \mathsf { D T P R - m i n } } ( \sigma _ { j } )$ holds for any value of ??.

By definition of $u _ { 1 } { \mathrm { : } }$ , we simplify $\begin{array} { r } { u _ { 1 } + \sum _ { i = 2 } ^ { j } \ell _ { i } + ( k - j ) L - 4 \beta \mathrm { t o } \sum _ { i = 1 } ^ { j } \ell _ { i } + ( k - j ) L - 2 \beta . } \end{array}$ For any sequence $s \in S _ { j }$ , we have the following:

$$
\frac {\operatorname{OPT} (s)}{\operatorname{DTPR} - \max (s)} \leq \frac {\operatorname{OPT} (\sigma_ {j})}{\operatorname{DTPR} - \max (\sigma_ {j})} = \frac {\operatorname{OPT} (\rho_ {j})}{\operatorname{DTPR} - \max (\rho_ {j})} \leq \frac {k u _ {j + 1} - 2 \beta}{\sum_ {i = 1} ^ {j} \ell_ {i} + (k - j) L - 2 \beta}.\tag{12}
$$

Lemma 11. <sub>For</sub> <sub>any</sub> $j \in [ 0 , k ]$ , by definition of $\{ u _ { i } \} _ { i \in [ 1 , k ] }$ and $\{ \ell _ { i } \} _ { i \in [ 1 , k ] }$

$$
\omega \cdot \left(\sum_ {i = 1} ^ {j} \ell_ {i} + (k - j) L - 2 \beta\right) \leq k u _ {j + 1} - 2 \beta . \quad \text {   The   proof   is   deferred   to   Appendix   } C.
$$

For $\epsilon  0$ , the competitive ratio <sub>OPT/DTPR-max</sub> is exactly ??:

$$
\forall 0 \leq j \leq k: \quad \frac {\mathrm{OPT} (\sigma_ {j})}{\mathrm{DTPR-max} (\sigma_ {j})} = \frac {k u _ {j + 1} - 2 \beta}{\sum_ {i = 1} ^ {j} \ell_ {i} + (k - j) L - 2 \beta} = \omega .
$$

and thus for any sequence $s \in S$

$$
\forall s \in \mathcal {S}: \quad \frac {k c _ {\max} (s) - 2 \beta}{\text { DTPR - max } (s)} \leq \omega .
$$

Since $0 \mathsf { P T } ( s ) \le k c _ { \mathrm { m a x } } ( s ) - 2 \beta$ for any sequence $s ,$ this implies that <sub>DTPR-max</sub> is ??-competitive. □

Proof of Corollary 7. For simplification purposes, let $\beta = b L / 2$ , where ?? is a real constant on the interval $( 0 , k )$ . To show part <sub>(a)</sub> for <sub>REGIME-1</sub>, with fixed $k \geq 1$ , observe that for suficiently large ??, we have the following:

$$
\theta - b - 1 = (\omega - 1) \left(1 + \frac {\omega}{k}\right) ^ {k} - \left(b - \frac {b}{k} + \frac {b \omega}{k}\right) \left(1 + \frac {\omega}{k}\right) ^ {k} \approx (1 + o (1)) \left[ \omega \left(\frac {\omega}{k}\right) ^ {k} - b \left(\frac {\omega}{k}\right) ^ {k + 1} - b \right].
$$

Let $\begin{array} { r } { \omega _ { + } = \sqrt [ k + 1 ] { k ^ { k } \cdot \frac { k \theta } { k - b } } } \end{array}$ . Then, for suficiently large ??, we have the following:

$$
(1 + o (1)) \left[ \omega_ {+} \left(\frac {\omega_ {+}}{k}\right) ^ {k} - b \left(\frac {\omega_ {+}}{k}\right) ^ {k + 1} - b \right] = (1 + o (1)) \frac {(k - b) (\theta)}{k - b} = (1 + o (1)) [ \theta - b ].
$$

Furthermore, let $\varepsilon > 0$ and set $\omega _ { - } = \left( 1 - \varepsilon \right) \sqrt [ k + 1 ] { k ^ { k } \cdot \frac { k \theta } { k - b } } .$

A similar calculation as above shows that for suficiently large ?? we have:

$$
\left(\omega_ {-} - 1\right) \left(1 + \frac {\omega_ {-}}{k}\right) ^ {k} - \left(b - \frac {b}{k} + \frac {a \omega_ {-}}{k}\right) \left(1 + \frac {\omega_ {-}}{k}\right) ^ {k} \geq (1 - 3 k \varepsilon) [ \theta - b ].
$$

Thus, $\begin{array} { r } { \omega = O \left( \sqrt [ k + 1 ] { k ^ { k } \frac { k \theta } { k - b } } \right) } \end{array}$ satisfies (10) for suficiently large ??, fixed $k \geq 1$ , and $\begin{array} { r } { \beta = \frac { b L } { 2 } } \end{array}$ s.t. $b \in ( 1 , k )$

To show part <sub>(b)</sub> for <sub>REGIME-2</sub>, observe that the right-hand side of (10) can be approximated as $\left( 1 + \frac { \omega } { k } \right) ^ { k } \approx e ^ { \omega }$ when $k \to \infty$ . Then by taking limits on both sides, we obtain the following:

$$
\frac {U - L - 2 \beta}{L (\omega - 1) - 2 \beta (1)} = e ^ {\omega}.
$$

Let $\beta = b L / 2$ as outlined above. We then obtain the following:

$$
\frac {U - L - b L}{L (\omega - 1) - b L} = \frac {\theta - 1 - b}{\omega - 1 - b} = e ^ {\omega} \implies \theta - 1 - b = (\omega - 1 - b) e ^ {\omega}.
$$

By definition of the Lambert ?? function, solving this equation for ?? obtains part (2).

## C PROOFS OF LEMMAS 10 AND 11

In this section, we give the deferred proofs of Lemmas 10 and 11, which are used in the proofs of Theorem 4 and Theorem 5, respectively.

Proof of Lemma 10. We show that the following holds for any $j \in [ 0 , k ]$ , by Definition 1:

$$
\sum_ {i = 1} ^ {j} u _ {i} + (k - j) U + 2 \beta \leq \alpha \cdot (k \ell_ {j + 1} + 2 \beta).
$$

First, note that $k \ell _ { j + 1 } = k ( u _ { j + 1 } - 2 \beta )$ for all $j \in [ 0 , k ]$ , by Observation 3. This gives us the following:

$$
\sum_ {i = 1} ^ {j} u _ {i} + (k - j) U + 2 \beta \leq \alpha k u _ {j + 1} + \alpha 2 \beta - \alpha k 2 \beta ,
$$

$$
\sum_ {i = 1} ^ {j} u _ {i} + (k - j) U + \left[ 2 \beta - \alpha 2 \beta + \alpha k 2 \beta \right] \leq \alpha k u _ {j + 1},
$$

$$
\frac {(k - j) U}{\alpha k} + \frac {\sum_ {i = 1} ^ {j} u _ {i}}{\alpha k} + \left[ \frac {2 \beta}{\alpha k} - \frac {2 \beta}{k} + 2 \beta \right] \leq u _ {j + 1}.
$$

By substituting Def. 1 into $\textstyle \sum _ { i = 1 } ^ { j } u _ { i }$ , the above can be simplified exactly to the closed form for $u _ { j + 1 } { \mathrm { : } }$

$$
\frac {U}{\alpha} - \frac {j U}{\alpha k} + \left(\frac {\sum_ {i = 1} ^ {j} u _ {i}}{\alpha k}\right) + \left[ \frac {2 \beta}{\alpha k} - \frac {2 \beta}{k} + 2 \beta \right] = u _ {j + 1},
$$

$$
\left[ U - \left(U - \frac {1}{\alpha}\right) \left(1 + \frac {1}{\alpha k}\right) ^ {j} \right] + \left[ \left(\frac {2 \beta}{\alpha k} - \frac {2 \beta}{k} + 2 \beta\right) \left(1 + \frac {1}{\alpha k}\right) ^ {j} \right] = u _ {j + 1}.
$$

and the claim follows by the definition of $u _ { j + 1 }$

Proof of Lemma 11. We show that the following holds for any $j \in [ 0 , k ]$ , by Definition 2:

$$
\omega \cdot \left(\sum_ {i = 1} ^ {j} \ell_ {i} + (k - j) L - 2 \beta\right) \leq k u _ {j + 1} - 2 \beta .
$$

First, note that $k u _ { j + 1 } = k ( \ell _ { j + 1 } + 2 \beta )$ for all $j \in [ 0 , k ]$ , by Observation 3. This gives us the following:

$$
\sum_ {i = 1} ^ {j} \ell_ {i} + (k - j) L - 2 \beta \leq \frac {k \ell_ {j + 1}}{\omega} - \frac {2 \beta}{\omega} + \frac {k 2 \beta}{\omega},
$$

$$
\sum_ {i = 1} ^ {j} \ell_ {i} + (k - j) L - \left[ 2 \beta - \frac {2 \beta}{\omega} + \frac {k 2 \beta}{\omega} \right] \leq \frac {k \ell_ {j + 1}}{\omega},
$$

$$
\frac {\omega \left(\sum_ {i = 1} ^ {j} \ell_ {i}\right)}{k} + \frac {\omega (k - j) L}{k} - \left[ \frac {\omega 2 \beta}{k} - \frac {2 \beta}{k} + 2 \beta \right] \leq \ell_ {j + 1}.
$$

By substituting Def. 2 into $\textstyle \sum _ { i = 1 } ^ { j } \ell _ { i }$ , the above can be simplified exactly to the closed form for $\ell _ { j + 1 }$ :

$$
\omega L - \frac {\omega j L}{k} + \frac {\omega (\sum_ {i = 1} ^ {j} \ell_ {i})}{k} - \left[ \frac {\omega 2 \beta}{k} - \frac {2 \beta}{k} + 2 \beta \right] = \ell_ {j + 1},
$$

$$
\left[ L + (\omega L - L) \left(1 + \frac {\omega}{k}\right) ^ {j} \right] - \left[ \left(\frac {\omega 2 \beta}{k} - \frac {2 \beta}{k} + 2 \beta\right) \left(1 + \frac {\omega}{k}\right) ^ {j} \right] = \ell_ {j + 1}.
$$

and the claim follows by the definition of $\ell _ { j + 1 }$

## D PROOFS OF LOWER BOUND RESULTS

This section formally proves the lower bound results for <sub>OPR-max</sub>, building on the proof for <sub>OPR-min</sub> provided in Section 5.2.

## D.1 Proof of Theorem 9 (OPR-max Lower Bound)

Proof of Theorem 9. Let <sub>ALG</sub> be a deterministic online algorithm for <sub>OPR-max</sub>, and suppose that the adversary uses the price sequence $u _ { 1 } , \ldots , u _ { k }$ , which is exactly the sequence defined by (6). $u _ { 1 }$ is presented to <sub>ALG</sub>, at most ?? times or until <sub>ALG</sub> accepts it. If <sub>ALG</sub> never accepts $u _ { 1 }$ , the remainder of the sequence is all $L ,$ and <sub>ALG</sub> achieves a competitive ratio of $\frac { k u _ { 1 } - 2 \beta } { k L - 2 \beta } = \omega _ { \mathrm { ; } }$ , as defined in (8).

If <sub>ALG</sub> accepts $u _ { 1 }$ , the next price presented is $L ,$ , repeated at most ?? times <sub>or</sub> <sub>until ALG switches</sub> <sub>to</sub> <sub>reject</sub> ??. After <sub>ALG</sub> has switched, $u _ { 2 }$ is presented to ${ \mathsf { A L G } } ,$ at most ?? times or until <sub>ALG</sub> accepts it. Again, if <sub>ALG</sub> never accepts $u _ { 2 } .$ , the remainder of the sequence is all ??, and <sub>ALG</sub> achieves a competitive ratio of at least $\begin{array} { r } { \frac { k u _ { 2 } - 2 \beta } { u _ { 1 } + ( k - 1 ) L - 4 \beta } = \omega } \end{array}$ , as defined in (8).

As the sequence continues, whenever <sub>ALG</sub> does not accept some $u _ { i }$ after it is presented ?? times, the adversary drops the price to ?? for the remainder of the sequence. Otherwise, if <sub>ALG</sub> accepts ?? prices before the end of the sequence, the adversary concludes by presenting ?? at least ?? times.

Observe that any <sub>ALG</sub> which does not immediately reject the first ?? presented to it after accepting some $u _ { i }$ obtains a competitive ratio strictly worse than $\omega .$ . To illustrate this, suppose <sub>ALG</sub> has just accepted $u _ { 1 }$ , achieving a profit of $u _ { 1 } - \beta$ so far. The adversary begins to present ?? prices, and <sub>ALG</sub> accepts $y \le ( k - 1 )$ <sub>)</sub> of these ?? prices before switching away. If $y = \left( k - 1 \right)$ , <sub>ALG</sub> will accept ?? prices before the end of the sequence and achieve a competitive ratio of $\frac { k U - 2 \beta } { u _ { 1 } + ( k - 1 ) L - 2 \beta } > \omega$ . Otherwise, if $y < ( k - 1 )$ , the profit achieved by <sub>ALG</sub> so far is at most $u _ { 1 } - 2 \beta + y L$ , while the profit achieved by <sub>ALG</sub> if it had immediately switched away $( y = 0 )$ would be $u _ { 1 } - 2 \beta \cdot$ – since any price which might be accepted by <sub>ALG</sub> in the future should be <sub>≥</sub> ??, the latter case strictly improves the competitive ratio of <sub>ALG</sub>.

Assuming that <sub>ALG</sub> does immediately reject any ?? presented to it, and that <sub>ALG</sub> accepts some prices before the end of the sequence, the competitive ratio attained by <sub>ALG</sub> is at least $\begin{array} { r } { \frac { k u _ { j + 1 } - 2 \beta } { \sum _ { i = 1 } ^ { j } u _ { i } - ( j + 1 ) 2 \beta + ( k - j ) L } = \omega } \end{array}$ , as defined in (8).

Similarly, if <sub>ALG</sub> accepts ?? prices before the end of the sequence, the competitive ratio attained by <sub>ALG</sub> is at least $\begin{array} { r } { \frac { k U - 2 \beta } { \sum _ { i = 1 } ^ { k } u _ { i } - k 2 \beta } = \omega } \end{array}$ , as defined in (8).

Since any arbitrary deterministic online algorithm <sub>ALG</sub> cannot achieve a competitive ratio better than ?? playing against this adaptive adversary, our proposed algorithm <sub>DTPR-max</sub> is optimal. □

Received February 2023; revised October 2023; accepted October 2023